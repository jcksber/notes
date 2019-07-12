
Django project optization guide (pt.3) [2017]
=============================================
> Created: July 12, 2019
>
> Notes by: Jack Kasbeer

[Reference Documentation](https://dizballanze.com/en/django-project-optimization-part-3/)


#### All parts of this guide:
- Part 1: Profile and Django settings
- Part 2: Working with databases 
- Part 3: Caching (this doc)

*This document will specifically cover Part 3: Caching*

In this part of the guide, I will cover the most valuable approach to achieve high performance - caching. The essence of caching is that you place the most commonly used data to fast storage in order to speed up the access to them. It's important to understand that the fast storage (i.e. memory) often has limited capacity. So we should use it only for that data which will be often used.


Django cache framework
----------------------
Django has different built-in cache features. Cache storage can be set up with `CACHES` dict in `settings.py`:
```python
CACHES = {
    "default": {
        "BACKEND": "django.core.cache.backends.db.DatabaseCache",
        "LOCATION": "my_cache_table",
    }
}
```

Django has several built-in backends.  Let's check some of them:
- `DummyCache`: caches nothing, is used in development or testing environments to temporarily disable caching,
- `DatabaseCache`: stores cache in the database.  Not very fast storage, but can be useful to store results of long calculations or difficult database queries.
- `MemcachedCache`: uses [Memcached](http://memcached.org/) as a cache storage.  You need to have Memcached server(s) to use this backend.

**`MemcachedCache` is the most suitable backend for production usage.**  `DatabaseCache` also can be useful in specific cases.  Also, Django supports 3rd-party backends, for example, Redis can be a good option as a cache backend.  Redis provides more features than Memcached and it's quite possible that you're already using it in your project.  You can install `django-redis` package and [configure](http://niwinz.github.io/django-redis/latest/#_configure_as_cache_backend) it as a cache backend.

### The per-site cache
If you don't have any dynamic content on your project, you can simply solve the cache problem - enabling the per-site caching.  You need to add several changes to your `settings.py` for this:
```python
MIDDLEWARE = [
    'django.middleware.cache.UpdateCacheMiddleware',

    # place all other middlewares here

    'django.middleware.cache.FetchFromCacheMiddleware',
]

# Key in `CACHES` dict
CACHE_MIDDLEWARE_ALIAS = 'default'

# Additional prefix for cache keys
CACHE_MIDDLEWARE_KEY_PREFIX = ''

# Cache key TTL in seconds
CACHE_MIDDLEWARE_SECONDS = 600
```

As you can see, you should add middlewares at the beginning and at the end of `MIDDLEWARE` list.  After that, all GET and HEAD requests will be cached for `CACHE_MIDDLEWARE_SECONDS` seconds.

You can also clear cache programmatically:
```python
from django.core.cache import caches

cache = caches['default']  # `default` is a key from CACHES dict in settings.py
cache.clear()
```

Or you can clean cache directly in the cache storage if necessary.  An example for Redis:
```bash
$ redis-cli -n 1 FLUSHDB # 1 is a DB number specified in settings.py
```

### The per-view caching
In case it's not appropriate to cache the whole site, you can enable caching only for specific views (i.e. most highly used). Django provides `cache_page` decorator for this:
```python
from django.views.decorators.cache import cache_page


@cache_page(600, cache='default', key_prefix='')
def author_page_view(request, username):
    author = get_object_or_404(Author, username=username)
    show_articles_link = author.articles.exists()
    return render(
        request, 'blog/author.html',
        context=dict(author=author, show_articles_link=show_articles_link))
```

`cache_page` accepts following arguments:
- first, required argument is a time-to-live of cache in seconds,
- `cache`: key from `CACHES` dict,
- `key_prefix`: cache key prefix

Also, you can apply this decorator in `urls.py`, that is convenient for Class-Based Views:
```python
urlpatterns = [
    url(r'^$', cache_page(600)(ArticlesListView.as_view()), name='articles_list'),
    ...
]
```

If for example page content changes are based on authenticated user, then this approach won't work.  To solve this problem you should use one of the approaches described below.

### Template fragment caching
In the previous part of this guide, I mentioned that `QuerySet` objects are lazy and SQL requests are delayed as long as possible. We can take advantage of this and cache template fragments, that will let us to avoid SQL requests for cache TTL. cache template tag is provided for this:
```django
{% load cache %}

<h1>Articles list</h1>

<p>Authors count: {{ authors_count }}</p>

<h2>Top authors</h2>

{% cache 500 top_author %}
<ul>
    {% for author in top_authors %}
    <li>{{ author.username }} ({{ author.articles_count }})</li>
    {% endfor %}
</ul>
{% endcache %}

{% cache 500 articles_list %}
{% for article in articles %}
<article>
    <h2>{{ article.title }}</h2>
    <time>{{ article.created_at }}</time>
    <p>Author: <a href="{% url 'author_page' username=article.author.username %}">{{ article.author.username }}</a></p>
    <p>Tags:
    {% for tag in article.tags.all %}
        {{ tag }}{% if not forloop.last %}, {% endif %}
    {% endfor %}
</article>
{% endfor %}
{% endcache %}
```

The result of adding `cache` template tags to our template:
- Before: 7 queries @ 79.16 ms
- After: 4 queries @ 69.87 ms

`cache` accepts following arguments:
- first required argument is a TTL of cache,
- the second required argument is a frament name,
- optional additional variables which identify fragment by dynamic data,
- keywork `using='default'` argument, should correspond to a key in `CACHES` dict.

For example, we need to cache each template fragment separately for different users.  Let's provide an additional variable that identifies a user to `cache` template tag:
```django
{% cache 500 personal_articles_list request.user.username %}
    <!-- ... -->
{% %}
```

You can even provide several additional variables like this to create caching key based on a combination of their values.

### The low-level caching
Django provides access to the low-level caching API. You can use it to save/extract/delete data by specified cache key. Let's check out a small example:
```python
from django.core.cache import cache

class ArticlesListView(ListView):

    #...

    def get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        authors_count = cache.get('authors_count')
        if authors_count is None:
            authors_count = Author.objects.count()
            cache.set('authors_count', authors_count)
        context['authors_count'] = authors_count
        #...
        return context
```

In this code fragment, we check whether authors count is in the cache by `authors_count` key. `cache.get` method returns not `None` value if the key exists in the cache storage. In that case, we can use this value without any requests to the database. Otherwise, code requests authors count from the database and saves it in the cache. In this way, we avoid database requests for a cache TTL.

Besides database queries results, it makes sense to cache results of complex calculations or requests to external services.  It's important to understand that data can change and cache will contain stale information.  There are several approaches to minimize a chance to use stale data from cache:
- set up cache TTL to correspond frequency of data change,
- add cache invalidation.

**Cache invalidation should happen on data change.**. Let's check out how to add cache invalidation for authors count example:
```python
from django.db.models.signals import post_delete, post_save
from django.dispatch import receiver
from django.core.cache import cache


def clear_authors_count_cache():
    cache.delete('authors_count')


@receiver(post_delete, sender=Author)
def author_post_delete_handler(sender, **kwargs):
    clear_authors_count_cache()


@receiver(post_save, sender=Author)
def author_post_save_handler(sender, **kwargs):
    if kwargs['created']:
        clear_authors_count_cache()
```

In this example two signal handlers on adding/deleting of authors were added.  That makes possible to remove cache by `authors_count` key on authors quantity change and the new number of authors will be fetched from the database.


cached_property
---------------
Besides of cache framework, Django provides an ability to cache methods' invocations right in the process memory. This type of caching is possible only for methods without arguments (besides `self`). This type of cache will live while the corresponding instance exists.

`cached_property` is included Django decorator.  Methods with this decorator also become properties.  Let's check out an example:
```python
class Author(models.Model):
    username = models.CharField(max_length=64, db_index=True)
    email = models.EmailField()
    bio = models.TextField()

    @cached_property
    def articles_count(self):
        return self.articles.count()
```


































