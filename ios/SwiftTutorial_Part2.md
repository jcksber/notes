
Swift Tutorial: Building an iOS application - Part 2
====================================================
> Notes by Jack Kasbeer
>
> May 16, 2019

*Reference documentation:*
[Swift Tutorial: Building an iOS application - Part 2](https://www.airpair.com/swift/building-swift-app-tutorial-2)


Table of Contents
-----------------
1. Introduction
2. What you need
3. Our first feature
4. Our core business objects
5. Finishing our first feature



1 Introduction
------------
In part 1 of this tutorial series on building out an iOS application using Swift, you didn’t write any of your own code just yet. Instead you achieved something extremely important: a fundamental understanding of how Swift affects our use of the Cocoa Touch frameworks.

Using the “TaskMe” application that you generated in Xcode 6, it’s time to start adding some real functionality.


2 What You Need
-------------
1. A Mac
2. Xcode
3. Basic understanding of Swift
4. To have read part 1 of this series


3 Our First Feature
-------------------
The first feature you’re going to add is just simply having the ability to add a new task to your task list. Once you’ve created this feature, you’ll be able to tap the “+” button, type in your task’s title and some notes if you like, and when you tap “Save”, you’ll be taken back to the list of tasks.


### 3.1 Setting up our Storyboard
For your 'new task' user interface, it would be nice if the user could tap a "+" button and have a new controller pop up modally.  Open up `Main.storyboard` and get started on this.

You're actually going to use two controllers to achieve this, a `UINavigationController` and a normal `UIViewController` which will be embedded in the new `UINavigationController` (you won't be reusing the one already on screen, as that's for the main workflow). So drag out a `UINavigationController` and `UIViewController` from the Object Library into the editor area.

Delete the table view controller that was created with the `UINavigationController`, then hold down control, click on the `UINavigationController`, and drag across to the newly created `UIViewController`.  In the popup that appears, choose "root view controller" from underneath the "Relationship Segue" section of this.  This will embed the `UIViewController` in the `UINavigationController` so you can later add "Cancel" and "Save" buttons to it.

Next you need to set up the segue for navigating to the `UINavigationController` and `UIViewController`.  Before you do that, go into the `MasterViewController.swift` file and delete the following two lines from the `viewDidLoad` method:
```swift
let addButton = UIBarButtonItem(barButtonSystemItem: .Add, target: self, action: "insertNewObject:")
self.navigationItem.rightBarButtonItem = addButton
```

With those deleted, you can also delete the `insertNewObject:` method.

Back in your `Main.storyboard` file, drag a Bar Button Item from the Object Library onto the right hand side of the `MasterViewController`'s navigation bar.  Then, in the Attributes Inspector, change its Identifier to "Add".

Now you need to set up our segue from the `MasterViewController` to the new `UINavigationController`.  Hold control and drag the Bar Button Item you just created to the new `UINavigationController`, and choose "present modally" from within the "Action Segue" section of the popup.

Select the new segue -- it will look like a rectangle in a circle as part of an arrow between the `MasterViewController` and the new `UINavigationController` -- and set the Identifier to "showAddTask" using the Attributes Inspector.

The last thing left to do is to set up our screen.  From the Object Library, drag two Text Fields into the `UIViewController`, positioning them as you see fit.  You should try setting up the Auto Layout constraints by holding control and dragging around both the Text Fields and the containing view.

With the first Text Field selected, you're going to edit some settings in the Attributes Inspector.  First, change the empty placeholder to say "Task Title," and then change the font size to 24 points.  You can do this through the front editor, which can be opened by clicking on the "T" icon next to the font.

So that you can play around with the height as well as the width, change the Border Style to be the second one from the right.  Now you can play with the height; I made my text field about 60 points tall, and I also change the Alignment to be centered.

Now, do the same for the second Text Field, except maybe give it a smaller font size -- I made mine 16 points -- and height, and make the placeholder text something like "Notes."  I also set the alignment for the notes field to be left instead of center.

Click on the navigation bar in the new `UIViewController`, and set the title to be "Add Task" in the Attributes Inspector.

With all of this done, you now have your two fields and the controllers.  Next you need to create your `UIViewController` subclass for our new Add Task screen.

Before you continue though, try running the application to see that you can now open the new screen, though you can't get back.


### 3.2 Creating the AddTaskViewController
Create a new file using "Cocoa Touch Class" from the options.  In the "Subclass:" field, type or select "UIViewController", then add "AddTask" to the beginning of the name in the "Class:" field.  Leave "Also create XIB file" unchecked, and make sure "Swift" is selected for "Language". Click "Create".

Before you start editing the file, **remember to set this to be the class for the** `UIViewController` **you created in our Storyboard.**  Open your `Main.storyboard` up again, select the new `UIViewController` you created in the previous section, and set the "Class" to be `AddTaskViewController` in the Identity Inspector (the panel on the top left that looks like a small square and some lines).

Now, with the focus on the `AddTaskViewController` in Interface Builder, open up the Assistant Editor (the tuxedo-looking icon in the toolbar) so that you can set up the outlets between the Storyboard and our code.

Hold control and drag from each of the Text Fields to the top of the class definition for `AddTaskViewController`, naming each `titleField` and `notesField` respectively.  Should look something like this:
```swift
class AddTaskViewController: UIViewController {
    @IBOutlet weak var titleField: UITextField!
    @IBOutlet weak var notesField: UITextField!

    override func viewDidLoad() {
```

The `@IBOutlet` is a declaration of the connection between the Storyboard file and your code.  The reason it uses an implicitly unwrapped optional is so that the view controller can be initialized without this set, and then these can be set once the views are ready.  It won't effect us in most cases, like setting things up in `viewDidLoad` or `viewWillAppear:animated:`, as it will be set by then.

The `weak` declaration of the variable is also to ensure there are no unnecessary references.  Without this, the `AddTaskViewController` would own the two `UITextFields`, which means that when the view attempts to be released for whatever reason, it wouldn't be allowed to because the controller is still hanging onto its subviews.


### 3.3 Cancelling task creation
The next step is to set up the "Cancel" button so that when you tap it, your task insn't saved.  Instead, it simply hides the task creation screen.

In the `Main.storyboard` file, you're going to drag out a new Bar Button Item from the Object Library, and place it into the left hand side of the navigation bar of our `AddTaskViewController`'s screen.

Select the Bar Button Item, then change the Identifier to "Cancel" in the Attributes Inspector.

It's important to use these standard version of UI elements for two reasons: future-proofing and accessibility.

Now you need to get this "Cancel" button all hooked up.  To do that, you're going to create a new "dismiss" segue.

Create a new file with a Cocoa Touch Class, "Subclass of:" set to "UIStoryboardSegue", and "Class:" set to be "DismissSegue" (in Swift of course).

You need to make one tweak before you use this class in your Storyboard.  It will currently let a "dismiss" segue appear in Interface Builder, but it will fail at runtime due to some weird behind-the-scenes naming stuff Swift does.  To fix this, you need to explicitly define the Objective-C class name so the Storyboard can find the correct class.

You will need to change the class definition to look like this:
```swift
@objc(DismissSegue) class DismissSegue: UIStoryboardSegue {
```

Now to create a perform method for our custom segue that will simply dismiss the controller.
```swift
override func perform() {
  if let controller = sourceViewController.presentingViewController? {
    controller.dismissViewControllerAnimated(true, completion: nil)
  }
}
```

Now to connect the dots.  In the `Main.storyboard` file, hold control and drag from the selected "Cancel" button, to the `MasterViewController`.  Select the "dismiss" option from the popup.

With all that done, you're ready to go! Build and Run.


### 3.4 Saving our task
Time to actually create some real business value -- saving a task -- and to do that, you'll need to save a "Save" button.

In your `Main.storyboard` file, drag out another Bar Button Item from the Object Library, this time placing it on the right hand side of your `AddTaskViewController`'s navigation bar.

Make sure it's selected, then in our Attributes Inspector, change it's Identifier to "Save".

While you're here in this file, let's also clean up something else that has been bugging me.  The users of your application don't care that the list screen is the "Master" screen; to them it's the "Tasks" screen.  Double click where it says "Master" and change it to "Tasks."

Now add a "dismiss" segue between the "Save" button and the `MasterViewController` just like you did with the "Cancel" button.

If you run the application now, you should be able to dismiss the modal using either of the buttons, which is exactly what you’re looking for. However, it would also be nice to have a task added to a list of tasks, and for our Table View to refresh so that it shows the new task.

You need some central place to put these tasks, and you need a good definition of what exactly a task is, so time to put a hold on working on the “Save” feature for now, and create these parts.


4 Our core business objects
---------------------------
Modelling your core business objects separately from your user interface is extremely important.  It makes them flexible to use, and portable if you were ever to make your application work across platforms -- such as adding an iPad or OS X application, or whatever the next ingenious Apple creation ends up being.

You don't need much.  A simple `Task` struct and a `TaskStore` class should do you nicely.

### 4.1 Creating the Task struct
Create a new file that's simply a plain-old "Swift File".  Name the file "Task.swift" and then you want to create a new `Task` struct within the file:
```swift
struct Task {
    let title: String
    let notes: String

    init(title: String, notes: String) {
        self.title = title
        self.notes = notes
    }
}
```

You may be thinking, “there is no way to edit the Task!” That’s right; instead of making the struct mutable, you’re going to embrace the functional style Swift is aiming to push you towards, and create a nice immutable value object.

When you need to make changes to a task, you’ll simply delete the old one and replace it with the new one. It will seem from the users perspective that it was just edited, but you’re creating less mutable state for you to manage this way, making your life as a programmer easier.


### 4.2 Managing our TaskStore
Next you need another Swift file for our `TaskStore` which you'll set up to be a singleton so that you can access the one instance at all times, adding and "editing" tasks as much as you like.

Create a "Swift File" and name it "TaskStore.swift":
```swift
class TaskStore {
    class var sharedInstance: TaskStore {
        struct Static {
            static let instance = TaskStore()
        }
        return Static.instance
    }

    var tasks: [Task] = []

    func add(task: Task) {
        tasks.append(task)
    }

    func replace(task: Task, atIndex index: Int) {
        tasks[index] = task
    }

    func get(index: Int) -> Task {
        return tasks[index]
    }
}
```

The first few lines of the `TaskStore` class creates a way for you to use a singleton of your `TaskStore` by accessing it with `TaskStore.sharedInstance`.

You then have a few basic functions to `add`, `replace`, and `get` tasks from your list.

At some point in the future you could replace this with a more suitable version of this `TaskStore` class, likely which stores the tasks across launches, but this will do for now.


5 Finishing our first feature
-----------------------------
I'm going to wrap up part 2 here with a challenge for you to finish off the "Save" feature.  Don't worry just yet about displaying the list of tasks, as I'll be covering that in the next part.  Simply attempt to get the tasks saved into the `TaskStore`.

Hint: the trick will be to give the segue from the "Save" button a name, and use the `prepareForSegue:sender:` method.
