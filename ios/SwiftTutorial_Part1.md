
Swift Tutorial: Building an iOS application - Part 1
====================================================
> Notes by Jack Kasbeer
>
> May 16, 2019

*Reference documentation:*
[Swift Tutorial: Building an iOS application-Part 1](https://www.airpair.com/swift/building-swift-app-tutorial)


Table of Contents
-----------------
1. Introduction
2. What You Need
3. Getting Started
4. Learning By Example
5. Coming Up Next




1 Introduction
--------------
Swift has taken the Apple developer world by storm since its announcement at WWDC 2014. With Swift being such a new language, there are no real experts yet, and design patterns are still being discovered and written about as people learn what it excels at.


2 What You Need
---------------
1. A Mac w/ OS >= Mavericks
2. Xcode
3. A basic understanding of Swift


3 Getting Started
-----------------
Create a new Project, then select "Master-Detail Application" under iOS > Application.

This template will give us a great starting point with a Storyboard already set up with most of what you need, and a controller for both the Master (list) controller and the Detail controller.

The reason you’re going to use this template is that you’re going to create the “Hello World” of more complete iOS applications, the task list app.

The “Organization Identifier” will help create your “Bundle Identifier”, which is used to give your application a unique name for Apple and it’s devices.

For the “Language” drop down, ensure you have “Swift” and not “Objective-C” selected. This will make most of the code Xcode generates for us be created as Swift files and not Objective-C files.

Lastly, for the “Devices” drop down, select “iPhone”, and make sure “Use Core Data” is not checked. Core Data is great to learn about, but we’re just taking a look at the core parts of creating an iOS application in this series.


4 Learning By Example
---------------------
Before you jump into writing our custom code, lets take a look at what you can learn from the Swift code that was already generated for you by Xcode when you selected the “Master-Detail Application” template.

When you play with it, you’ll see that you start off in on a list screen with a “+” and “Edit” button in the navigation bar. Tapping on “+” will add a row to the list containing the date/time the row was added. Tapping on that row will show a details screen with the date/time in the middle of the screen, and on the list screen, you can delete rows by either swiping on them and pressing delete, or entering edit mode and deleting them in there.

Moving on though, lets start looking through the code that Xcode has generated for us.


### 4.1 AppDelegate.swift
The entry point to our application is going to be our `AppDelegate.swift` file, so lets start exploring the code in there first.

Ignoring the comments at the top of the file, the first line of code you come across is `import UIKit`. This line is going to give you access to all the bits and pieces available in UIKit within this file. If you’ve done Objective-C development in the past, this will be very familiar to you, but instead of importing header files for a class or framework, we’re importing the module. This will be done in almost every file in an iOS application, either for `UIKit`, or for simpler files, `Foundation`.

The next line of code is the odd looking `@UIApplicationMain`. In Swift there is no main file like there is in many other languages, instead, for iOS applications, you tell Xcode which file you want to be the main file by adding this attribute to a Swift class. You will likely never move this from the location Xcode puts it, but it’s good to know exactly what that odd looking line of code is doing.

Now you have our class definition for our `AppDelegate`. You can see it inherits from `UIResponder`, and the `UIApplicationDelegate` protocol. Coming from Objective-C, this syntax is a large improvement on the way it was being done.

The only function that actually has executable code within it is the `application:didFinishLaunchingWithOptions:` function, but that is simply returning true.

What is happening, and why this application still works, is there is some magic going on behind the scenes. In the settings for your project, the Main.storyboard file has been set to be the launching point for you application, which will set up the window and controllers when the application is launching.

The only other code worth mentioning in this file is the variable definition for window.
```swift
var window: UIWindow?
```

This is going to create a variable property on the `AppDelegate` with an optional `UIWindow` as its type, which is expressed as `UIWindow?`, note the question mark on the end.

An important thing to note though about optionals, is their use in parameter types. You’ll notice a exclamation mark on the end of some types in Swift. This is called an “implicitly unwrapped optional”.
```swift
@IBOutlet weak var detailDescriptionLabel: UILabel!
```

The difference between an implicitly unwrapped optional and a normal optional, is that if you try to use an implicitly unwrapped optional when it has no value, it will cause a runtime error, just as if you were to try and unwrap and use a normal optional when it has no value. This is why this kind of optional is only used in places where it is extremely unlikely there will be no value, and if there were no value, it would be a serious error anyway, so it’s better to fail fast and crash.

You might also notice that the second parameter of `application:didFinishLaunchingWithOptions:` has two names. The first being `didFinishLaunchingWithOptions`, which acts as the public name for when the function is called. The second name, `launchOptions`, acts as the name for the constant while it’s used within the scope of the function.


### 4.2 MasterViewController.swift
Opening up the `MasterViewController.swift` file, you can see that there is a lot more going on in here.  You’ve seen the `import` in the `AppDelegate.swift` file, and you have probably worked out that the `MasterViewController` class inherits from `UITableViewController`, I’m not going to go over every bit of code, but instead look at the interesting parts that Swift makes improvements on when compared to how it would usually be done in Objective-C.

Taking a quick look over the code, there is likely one thing that stands out quickly, whic is the use of the keyword `override` before almost every function.
```swift
override func viewDidLoad() {
override func prepare() {
override func viewWillAppear() {
```

Because the majority of these functions are already defined in `MasterViewController`'s superclass, or somewhere in the ancestry chain, you need to explicitly tell the compiler that you are sure that you want to override and/or extend the existing functionality of the ancestor functions.  This is to reduce errors where you might accidentally use a function name that has already been used, and then subsequently break everything.

The first part of your `MasterViewController` class is the objects variable property definition, which is being initialized with an empty `NSMutableArray()`.
```swift
var objects = NSMutableArray()
```

When the view has finished being loaded for your controller, the `viewDidLoad` method is going to be called. This function starts out by calling the superclass’s method, which is done with almost all functions like this one as you’ll see soon.

It follows up the superclass call by setting up the left and right bar button items. The interesting parts to note here is the use of a constant for `addButton`, which is suggested for all things like this that won’t change after being created, and the other part is the initialization of the `UIBarButtonItem`.
```swift
UIBarButtonItem(barButtonSystemItem: .Add, target: self, action: "insertNewObject:")
```

Also note the nicer way you tell it which type of `UIBarButtonSystemItem` you want to use. Instead of saying `UIBarButtonSystemItemAdd`, you can instead use `.Add` because the rest of `UIBarButtonSystemItem.Add` will be inferred using Swift’s brilliant type inference.

Looking at the `insertNewObject:` function, we can see an interesting type of `Any` for it’s sender parameter.
```swift
func insertNewObject(_ sender: Any) {
```

On the first line of the `tableView:cellForRowAtIndexPath:` function, we call `dequeueReusableCellWithIdentifier:forIndexPath:` on the table view. The return type of this method is `AnyObject`.

Because any kind of class could be returned from this function call, you need to type cast it using the `as UITableViewCell` that you see on the end of the line. You’ll later change this to be our custom cell class of `TaskCell` that you’ll create later.

This is common place when working with Cocoa Touch frameworks, because of the need for compatibility with the old Objective-C style where the return type was `id`, which meant a pointer to any object. Keep in mind that pointers are avoided in Swift whenever possible, which is why solutions like this were created to make your code much safer.


### 4.3 DetailViewController.swift
You have one more file to look at, which is your `DetailViewController`. This file is much smaller than the one for MasterViewController, but it still has several great examples of what Swift does to help us write better code.

Starting off we have our `@IBOutlet` attribute for the `detailDescriptionLabel` variable. Just as it was in Objective-C header files, this `@IBOutlet` variable attribute lets Interface Builder know about this property on our `DetailViewController`.
```swift
@IBOutlet weak var detailDescriptionLabel: UILabel!
```

You’ll also notice that this variable is defined to be weak because you don’t actually want `DetailViewController` to be the owner of this view.

Next you have an interesting looking variable property definition. This property has some extra code attached to it to actually define behaviour of a property right next to it’s definition.
```swift
var detailItem: AnyObject? {
  didSet {
    // Update the view.
    self.configureView()
  }
}
```

You have an optional `AnyObject?` type, but the interesting part is the `didSet` block of code.

Properties can have blocks of code that execute as callbacks to certain events. The amazing part about this though is that instead of defining a special setter that does this, you can have a much more clear definition of why that code is there.

This code now clearly states that after the `detailItem` has been set, at any point from now into the future, `configureView` should be called because something pertaining to the view has changed. Having these callbacks is a huge leap in the expressive nature of code for iOS applications.

Now I told you earlier that you should read up on optionals, because they’re used very regularly when developing iOS applications, and here is another one of those occasions where they’re used.
```swift
func configureView() {
  // Update the user interface for the detail item.
  if let detail: AnyObject = self.detailItem {
    if let label = self.detailDescriptionLabel {
      label.text = detail.description
    }
  }
}
```


5 Coming Up Next
----------------
In this part of the tutorial series on building iOS applications with Swift, you didn’t get to actually write any code. You did do something more important though, which was look at how Swift effects the way you work with the Cocoa Touch classes and APIs, because without this, you would begin to fight against Swift as you attempt to write applications like you always have.

With this foundation out of the way now though, you can start building out your application over the next few parts of this tutorial series about building iOS applications with Apple’s amazing new programming language, Swift. Be sure to check out part 2!
