
Top REST API Best Practices
---------------------------
#### Author: Jack Kasbeer
#### Created: 03.03.19 : 17:08
(Reference documentation)[https://code-maze.com/top-rest-api-best-practices/]


### Intro

*What is the difference between REST and HTTP?*

* REST is pretty flexible and compatible with HTTP that is the main protocol the internet is based upon.
* It is an architectural style and not the standard; hence, it provides a lot of freedom to implement various design best practices.
* It's language agnostic!

A familiarity with HTTP is assumed moving forward.

### Contents

#### About REST:
* What is REST?
* Is REST bound to HTTP?
* REST and HATEOAS Support
* What does RESTful API mean?
* Too much REST aka RESTafarian syndrome
#### REST API best practices:
* Abstract vs Concrete API
* URI formatting (nouns, not verbs). Good URL vs Bad URL examples
* Error handling
* Status codes
* Security
* Versioning REST API
* Importance of documentation


About REST
==========
### So what is REST essentially?
REST (Representational State Transfer) is an architectural style founded by Roy Fielding (who also developed HTTP 1.1 in parallel..)

It's primary use is to **communicate between computer systems on the World Wide Web**.

### Is REST bound to HTTP?
By definition, no.  Other protocol may be used with REST, but HTTP has remained undisputed champion.

### REST and HATEOAS Support
HATEOAS (Hypermedia As The Engine Of Application State) is the important feature of every scalable REST API.

HATEOAS constraint proposes that the client and server communicate entirely utilizing the *hypermedia*

There are several advantages to using hypermedia:
* Enables API designers rather than to include everything they can in each response, to instead allow part of the response to link to other endpoints - thus decouple the design!
* Helps API evolve and mature more gracefully
* Provides means for user to explore API more deeply

Example (GitHub):
Request 
```javascript
GET https://api.github.com/users/codemazeblog
```
Response
```javascript
{
  "login": "CodeMazeBlog",
  "id": 29179238,
  "avatar_url": "https://avatars0.githubusercontent.com/u/29179238?v=4",
  "gravatar_id": "",
  "url": "https://api.github.com/users/CodeMazeBlog",
  "html_url": "https://github.com/CodeMazeBlog",
  "followers_url": "https://api.github.com/users/CodeMazeBlog/followers",
  "following_url": "https://api.github.com/users/CodeMazeBlog/following{/other_user}",
  "gists_url": "https://api.github.com/users/CodeMazeBlog/gists{/gist_id}",
  "starred_url": "https://api.github.com/users/CodeMazeBlog/starred{/owner}{/repo}",
  "subscriptions_url": "https://api.github.com/users/CodeMazeBlog/subscriptions",
  "organizations_url": "https://api.github.com/users/CodeMazeBlog/orgs",
  "repos_url": "https://api.github.com/users/CodeMazeBlog/repos",
  "events_url": "https://api.github.com/users/CodeMazeBlog/events{/privacy}",
  "received_events_url": "https://api.github.com/users/CodeMazeBlog/received_events",
  "type": "User",
  "site_admin": false,
  "name": "Code Maze",
  "company": "Code Maze",
  "blog": "https://code-maze.com",
  "bio": "A practical programmers' resource.",
  ...
}
```

### What does RESTful API mean?
"RESTful", implies a few features:
* Client-server architecture - complete service is comprised of a client (frontend) and server (backend)
* Stateless - **All REST interactions are stateless.  That is, each request contains all of the information necessary for a connector to understand the request, independent of any requests that may have preceded it.**
* Cacheable - client should be able to cache responses for greater performance

So, a RESTful API is a service that hopefully follows these rules and uses HTTP methods to manipulate the set of resources.

But.. why?
We need them to make distributed applications that communicate over the internet for ease, flexibility, and scalability.

### Can we have too much REST?
Yes. The phrase for the people that follow the REST fanatically as defined by Mike Schinkel:

> A RESTifarian is a zealous proponent of the REST software architectural style as defined by Roy T. Fielding in Chapter 5 of his PhD. dissertation at UCIrvine. You can find RESTifarians in the wild on the REST-discuss mailing list. But be careful, RESTifarians can be extremely meticulous when discussing the finer points of REST, as I learned recently while participating on the list. :-)

We need a bit **pragmatism** to make good applications and services.

So let's discuss next some important points that make the API "shine", and the lives of the users a whole lot easier.


REST API Best Practices
=======================
### Abstract vs Concrete APIs
When developing software we often use abstraction and polymorphism to get most of our applications. We want to reuse as much of the code as possible.

In the case of APIs, **this is not exactly the case**.

For the REST APIs, the **concerete is better than abstract**.

To understand why, consider which API you'd rather use: X, that has one `/entities`, or Y, which has `/owners`, `/blogs`, and `/blogposts` separately?  Clearly API Y is the better choice (and is much more concrete).

### URI formatting (nouns, not verbs). Good URL vs Bad URL examples
**Best Practice Topic:** formatting your endpoints

Using a software development approach, you'll end up with something like this:
```javascript
/getAllBlogPosts
/updateBlogPost/12
/deleteBlogPost/12
/getAuthorById/3
/deleteAuthor/3
/updateAuthor/3
```
You get the point... there will be way too many endpoints, each serving a specific purpose. *There is a much better system to do this*.

**Treat the resource like a noun, and HTTP method as a verb**. Following this approach, we get something like:
```javascript
GET /blogposts            //get all the blog posts
GET /blogposts/12         //gets the blog post with the id 12
POST /blogposts           //adds a new blog post and returns the details
DELETE /blogposts/12      //removes the blog post with the id 12
GET /authors/3/blogposts  //gets all the blog posts of the author with id 3
```
This is a cleaner and more precise way to create an API and it's immediately clear to the end user that there is a method to the madness.

You can make it even cleaner by using singular instead of plural for the resource names.

### Error handling
**Best Practice Topic:** handling errors

Here's how some top dogs do it:

**Twitter:**
* Request: `GET https://api.twitter.com/1.1/account/settings.json`
* Response: Status Code 400
  ```javascript
  {"errors":[{"code":215,"message":"Bad Authentication data."}]}
  ```
Twitter gives you the Status Code and Error Code with a short description of the nature of the error that occurred.  They leave it up to you to look the coddes up on their Response Codes page.

**Facebook:**
* Request: `GET https://graph.facebook.com/me/photos`
* Response: Status Code 400
  ```javascript
  {
    "error": {
        "message": "An active access token must be used to query information about the current user.",
        "type": "OAuthException",
        "code": 2500,
        "fbtrace_id": "DzkTMkgIA7V"
    }
  }
  ```
Facebook gives you a more descriptive error message.

**Twilio:**
* Request: `GET https://api.twilio.com/2010-04-01/Accounts
* Response: Status Code 404
  ```javascript
  <?xml version='1.0' encoding='UTF-8'?>
  <TwilioResponse>
      <RestException>
          <Code>20404</Code>
          <Message>The requested resource /2010-04-01/Accounts/1234/IncomingPhoneNumbers/1234 was not found</Message>
          <MoreInfo>https://www.twilio.com/docs/errors/20404</MoreInfo>
          <Status>404</Status>
      </RestException>
  </TwilioResponse>
  ```
Twilio gives you XML response by default and the link to the documentation where you can find the error details.

The important thing is **not to leave the user of the REST API "hanging"** (not knowing what happened or aimlessly wandering through the wastes of StackOverflow searching for the explanation).

### Status codes
**Best Practice Concept:** pragmatic use of web error codes

When designing a REST API, we communicate with the API user by utilizing HTTP Status Codes. There are a lot of status codes, describing multiple possible responses.

But just how many should we use? **Should we have a strict status code for every situation?**

There are over over 70 status codes out there... and no one has them memorized (nor wants to).

Most developers are familiar with the most common status codes:
* `200 OK`
* `400 Bad Request`
* `500 Internal Server Error`
By starting with these three, you can cover most of the functionalities of your REST API.

Other commonly seen codes include:
* `201 Created`
* `204 No Content`
* `401 Unauthorized`
* `403 Forbidden`
* `404 Not Found`

Stay pragmatic - help the user by using a **limited number of codes** and descriptive messages.

### Security
**Best Practice Concept:** time-stamping & logging

**REST doesn't deal with security.** It relies upon standard HTTP mechanisms like basic or digest authentication.

Every request should be made over **HTTPS**.

**The client is where the state should be stored and verified.**

**Timestamping and logging** requests can help a bit too.

Much more to be said on this topic, but not covered here.

### REST API Versioning
**Best Practice Concept:** versioning

*Imagine this scenario...* 
> You've already written your REST API and it has been very successful and many people have used it and are happy with it.  But you have that juicy new functionality that breaks other parts of the system.  The breaking change.

The **solution** to this is **when you start making your API**, we can *version it* by **prefixing the endpoints with the API version:**
`https://api.example.com/v1/authors/2/blogposts/13`

Doing it this way allows you to simply increment the API version number whenever there are breaking changes in our API.  This also signals users.

### Importance of documentation
**Best Practice Concept:** you need documentation

**Without documentation, your API is as good as dead.**

**Proper documentation is essential** for every software product and web service alike.

Great examples:
* [Twilio](https://www.twilio.com/docs/api/rest/)
* [Facebook](https://developers.facebook.com/docs/)
* [Google](https://developers.google.com/maps/documentation/)

There are many tools to help, but don't forget the human touch :-)

### Conclusion
These are only best practices. Try it out.


