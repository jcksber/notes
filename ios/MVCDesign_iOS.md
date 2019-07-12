
Model-View-Controller (MVC) in iOS - A Modern Approach
======================================================
> Notes by Jack Kasbeer
> 
> May 29, 2019

*Reference documentation:*
[Model-View-Controller in iOS - A Modern Approach](https://www.raywenderlich.com/1000705-model-view-controller-mvc-in-ios-a-modern-approach)


Getting Started
---------------
The MVC pattern is commonplace in iOS development.  While it's not a difficult pattern to understand and get familiar with, there are things to consider to avoid common pitfalls.

This tutorial will help you avoid common mistakes that lead to apps that are too difficult to extend.

In this tutorial you won't be doing any coding.  Instead, you will walk through a project that uses MVC with best practices in mind.

Make sure to have the "MVCTutorial" project downloaded and open in order to follow along


What is MVC
-----------
MVC: Model, View, Controller

MVC is a software development pattern made up of three main objects:
1. The **Model** is where your data resides.  Things like persistence, model objects, parsers, managers, and networking code live there.
2. The **View** layer is the face of your app.  Its classes are often reusable as they don't contain any domain-specific logic.  For example, a `UILabel` is a view that presents text on the screen, and it's reusable and extensible.
3. The **Controller** mediates between the view and the model via the delegation pattern.  In an ideal scenario, the controller entity won't know the concrete view it's deal with.  Instead, it will communicate with an abstraction via a protocol.  A classic example is the way a `UITableView` communicates with its data source via the `UITableViewDataSource` protocol.

When you put everything together, the flow is roughly the following:
User actions in the View tell the Controller to update the Model (if necessary), which then notifies the Controller upon completion, and then updates the View.

The View, Controller, and Model are meant to be separate from the other, fulfilling a specific role.  The goal when building an app is to adhere to the pattern when building all layers of the app.

[Apple's MVC documentation](https://developer.apple.com/library/archive/documentation/General/Conceptual/CocoaEncyclopedia/Model-View-Controller/Model-View-Controller.html) explains these layers in detail and can give you a good theoretical understanding.  From a practical perspective, though, it falls a little short.

In this tutorial, you'll learn to treat MVC as a pattern and not as a strict rule that you must never break.  As is the case with many things in software development, nothing is perfect and neither is MVC.  There are gray areas that you'll run into, but no decision you make is the wrong decision.  As long as you don't have files that are too big, or code that is difficult to expand upon, then you're likely doing a good job.  Use MVC -- and other patterns for that matter -- as architectural guidelines and foundations for your app.


The Model (M)
-------------
The model layer encompasses your app's data.  No surprise there, but there are usually other classes and objects in your projects that you can include in this layer:

- **Network code:** You preferably only use a single class for network communication across your entire app.  It makes it easy to abstract concepts common to all networking requests like HTTP headers, response and error-handing and more.
- **Persistence code:** You use this when persisting data to a database, Core Data or storing data on a device.
- **Parsing code:** You should include objects that parse network responses in the model layer.  For example, in Swift model objects, you can use JSON encoding/decoding to handle parsing.  This way, everything is self-contained and your network layer doesn't have to know the details of all your model objects in order to parse them.  Business and parsing logic is all self-contained within the models.
- **Managers and abstraction layers/classes:** Everyone uses them, everyone needs them, nobody knows what to call them or where they belong.  It's normal to have the typical "manager" objects that often act as the glue between other classes.  These can also be wrappers around lower-level, more robust API: a keychain wrapper on iOS, a class to help with notifications or a model to help you work with HealthKit.
- **Data sources and delegates:** Something that may be less common is developers relying on model objects to be the data source or delegate of other components such as table or collection views.  It's common to see these implemented in the controller layer even when there's a lot of custom logic that's best kept compartmentalized.
- **Constants:** It's good practice to have a file with constants.  Consider putting these within a structure of structures or variables.  That way, you can reuse storyboard names, date formatters, colors, etc.
- **Helpers and extensions:** In your projects, you will often add methods to extend the capabilities of strings, collections, etc.  These too can be considered part of the model.

From a testing perspective, the model is an excellent candidate.  You can confirm business logic, mock some of your networking or persistence methods or add tests around sistive pieces of your model layer.


The View (V)
------------
When users interact with your app, they are interacting with the view layer.  It should not contain any business logic.  In code terms, you'll normally see:
- `UIView` subclasses: These range from a basic `UIView` to complex custom UI controls.
- Classes that are part of UIKit/AppKit.
- Core Animation.
- Core Graphics.

Typical code smells found in this layer manifest in different ways but boil down to include anything unrelated to UI in your view layer.  This can include network calls, business logic, manipulating models and so on.

Use the following as a checklist when inspecting your view layer:
- Does it interact with the model layer?
- Does it contain any business logic?
- Does it try to do anything not related to UI?

If you answer "yes" to any of these questions, you have an opportunity to clean up and refactor.

Well-written view components are often reusable, but don’t focus on that from the start. It’s easy to get caught up in the thought of taking a cool button you built and making it compatible for a dozen different scenarios that your app may never need. 

Consider making components reusable only when you actually need to. When you have multiple use cases, that’s the time to make a component more generic.

From a testing perspective, UI testing in iOS allows you to test things like transitions, UI elements having specific attributes or UI interactions working as intended.


The Controller (C)
------------------
The controller layer is the least reusable part of your app as it often involves your domain-specific rules.  It should be no surprise that what makes sense in your app won't always make sense in someone else's.  The controller will then use all the elements in your model layer to define the flow of information in your app.

Usually, you'll see classes from this layer deciding things like:
- What should I access first: the persistence or the network?
- How often should I refresh the app?
- What should the next screen be and in which circumstances?
- If the app goes to the background, what should I tidy up?
- The user tapped on a cell; what should I do next?

Think of the controller layer as the brain, or engine, of the app; it decides what happens next.

You likely won't do much testing in the controller layer as, for the most part, it's meant to kick things off, trigger data loads, handle UI interactions, mediate between UI and model, etc.


Putting it all Together
-----------------------
Now that you have a better understanding of the layers involved in the MVC pattern, it's time to see it in action with this tutorial's sample app.

Run the app.  This is a WWDC-like app that shows sessions and attendees for the event.  The app loads data from JSON files in the app bundle on launch.  Loading the data triggers the controllers to update the view and begin waiting for user interaction.

To start, expand the **Model** folder.  The three "pure" model classes are `Event`, `Attendee`, and `Session`.  Note how these conform to `Decodable` so parsing from a JSON response is simple.

From there, you have other model files that are part of your network calls.  This tutorial's project loads a file from the app's bundle, but you can add a remote URL to load a file or call a web service.  The main classes are:
- **Network:** A wrapper around `URLSession`
- **NetworkError:** To handle different errors during the request/response process
- **WebService:** To add a helper method for loading both attendees and sessions

Of note is **Network.swift**, which has a method that uses generics to request JSON from a URL and parse it into an object type you specify in the method call.  It handles HTTP responses and codes, errors, parsing, concurrency and making the `URLSession` call for you.

These files are small, straightforward, contain their own logic and you can test them with, at most, some mock methods.

Moving on to the view, it's mostly contained in your storyboard files.  This is a good thing as it's not only more visual, but reducing the lines of code you write means reducing the amount of possible bugs you can introduce.

You may also have view logic in files, as is the case with **SessionCell.swift**, particularly when you do custom animations or UI elements.  Your views should not know about business logic, interact with or know about model objects or do anything that doesn't pertain to the view itself.

Finally, you get to the controller layer, this is where things get interesting as they act as the glue between view and model.  `AttendeeViewController` and `SessionViewController` have the same concept.  These display a list of items that you can select to view details.  When you present the details for a selected item, you use the `DetailViewController`.

Some things to note are:
- The `AppDelegate` is in charge of loading the event data and then passing it to each of the controllers.
- When the controller receives data, it triggers a reload of the table with the data.
- The controllers act as the data source and delegate of the table view.
- The controllers handle passing the details information when the user selects a session or event.

It's all very lean, clean and easy to understand.  You could even, for more robust projects, create separate classes or objects to act as data sources or delegates, further simplifying your controllers.

There is no heavy reliance on completion blocks, delegation (outside of what the table view needs) or mixing up business logic in views or controllers.  You also don't have the dreaded "MVC: massive view controllers."  This occurs when too much of your business logic lives inside of the controller.

Some tips to help you notice when an item in a layer may be overstepping its boundaries are:
- The controller is doing network requests, parsing or persistence.
- The controller (or any file) has hundreds or thousands of lines of code.
- Views expect model objects to "configure" themselves or set up UI elements for display.
- Controllers are doing a lot of business logic, calculations, data wrangling or manipulation.
