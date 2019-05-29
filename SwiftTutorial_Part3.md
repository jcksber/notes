
Swift Tutorial: Building an iOS application - Part 3
====================================================
> Notes by Jack Kasbeer
>
> May 20, 2019

*Reference documentation:*
[Swift Tutorial: Building an iOS application - Part 3](https://www.airpair.com/swift/building-swift-app-tutorial-3)


Table of Contents
-----------------
1. Introduction
2. What you need
3. Finishing off Save
4. Listing Tasks
5. Prettying things up a bit



1 Introduction
--------------
Here's the code needed to finish implementing the save feature:
```swift
if segue.identifier == "dismissAndSave" {
  let task = Task(title: titleField.text, notes: notesField.text)
  TaskStore.sharedInstance.add(task)
}
```

You also need to make sure you've got the correct identifier set for all of the segues in your Storyboard.


2 What you need
---------------
1. A Mac
2. Xcode
3. Basic understanding of Swift
4. To have read part 1 and 2 of this series


3 Finishing off Save
--------------------
Time to finish off where you left your app.  Open up your `Main.storyboard` file and make sure the two segues between the buttons from our `AddTaskViewController` and `MasterViewController` have good names, such as "dismissAndCancel" and "dismissAndSave".  This will make it easy for us to identify them in this next bit.

In `AddTaskViewController.swift`, we're going to set up our `prepareForSegue:sender:` method.
```swift
override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject!) {
  if segue.identifier == "dismissAndSave" {
    let task = Task(title: titleField.text, notes: notesField.text)
    TaskStore.sharedInstance.add(task)
  }
}
```

First of all, you’re going to check that the segue you’re preparing for is the save segue, because your cancel segue will do everything you want it to do anyway, which is nothing.

It’s good practice, even when it’s the only segue you have set up, to be explicit about which segue you want the code to run for. That way when things change later, the developer — which could be you — will know exactly what is going on and how pieces are connected.

If this is the segue you’re looking for, you’re going to assign a new task to a constant, filling out that task from the two text fields.

Then you can use the `TaskStore` that you created in part 2 to keep your `Task` safe and sound by adding it to the `sharedInstance`.

Running the application now, everything “works” up until you get to the list. At least there aren’t (or shouldn’t be) any crashes, and things are running smoothly.

There is no point in saving a task if you can’t actually see it, though, so time to start getting into your second feature.


4 Listing Tasks
---------------
Before you start trying to list tasks, it’s important to mention that as of right now, no data is being stored between launches.

I suggest that you learn a little about CoreData after you’ve completed this tutorial series, or maybe even the new and shiny Realm, which is aiming to be the easy replacement for CoreData and sure has turned some heads.

### 4.1 Using what we've got
When you generated the project, you were given a `MasterViewController` with the basic delegate and data source methods set up for us, including deletion.

You’ve also got an extremely basic `DetailViewController`, complete with that absolutely gorgeous label in the middle of the screen (sarcasm of course).

Worry about the design in a bit “when the designers have it ready” (there are no designers, the designers are a lie). For now, you just want to focus on being a great programmer who lives on the edge and gets something working in Swift.

So let's do that.  Open up `MasterViewController.swift` and make a few changes, the first of which will be removing the unwanted objects property on our `MasterViewController`.  Delete the line at the top of the controller that looks like this:
```swift
var objects = NSMutableArray()
```

You have no need for that anymore. Removing it is going to make Xcode mad though, because that property is used in quite a few places. Start fixing the errors from top to bottom, replacing the old code with the new code that uses your `TaskStore`.

### 4.2 Opening the detail screen
The first place that an error shows up is in the `prepareForSegue:sender:` method of your `MasterViewController`.  You need to make two changes to the last two lines inside the if statement:
```swift
override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
  if segue.identifier == "showDetail" {
    if let indexPath = self.tableView.indexPathForSelectedRow() {
      let task = TaskStore.sharedInstance.get(indexPath.row)
      (segue.destinationViewController as DetailViewController).detailItem = task
    }
  }
}
```
On the second line inside the if statement, change the constant name to `task` instead of `object`, and use the `get` method on the `sharedInstance` of your `TaskStore` to get the task that you're after.  No need for casting now either because you know exactly what you're getting back from the `get` method: a `Task`.

The change for the third line inside of the if statement is comparatively boring; all you're doing is setting the `detailItem` of your `DetailViewController` to be the `task` instead of the `object` because you changed the constant name.

You now need to fix a problem elsewhere, specifically in your `DetailViewController`, so open up the `DetailViewController.swift` file and make a small change.

### 4.3 No more AnyObject, we like structs
The `DetailViewController` is expecting an `AnyObject?`, which means a struct just isn't going to fit into place here.  You can fix that though by changing the type of the `detailItem` property to be `Task?` instead.
```swift
var detailItem: Task? {
  didSet {
    // Update the view.
    self.configureView()
  }
}
```

While here, let's fix up the rest of the controller.  It won't take much, it's just the `configureView` method that you need to change.

Where you unwrap the `detailItem` optional, you’re saying it should be of the type `AnyObject`, which you know isn’t true because you just changed away from that, time to fix it. Then, because `Task` doesn’t have a description property, you’ll change it to be `title`:
```swift
func configureView() {
  // Update the user interface for the detail item.
  if let detail: Task = self.detailItem {
    if let label = self.detailDescriptionLabel {
      label.text = detail.title
    }
  }
}
```

That's all you're going to change in this controller until some design changes come in, so you can head back to your `MasterViewController.swift` file and finish that off.

### 4.4 Changing the data source and delegate
The next error in line for fixing in your `MasterViewController` is the `UITableViewDataSource` protocol method `tableView:numberOfRowsInSection:`.

At the moment you’re just calling count on what was the array of objects. Now you want to call count on your `sharedInstance` of your `TaskStore` instead.
```swift
override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  return TaskStore.sharedInstance.count
}
```

Of course you don't actually have a count property at the moment, so time to create that now.  Open up the `TaskStore.swift` file and add this under the `tasks` property declaration:
```swift
var count: Int {
  get {
    return tasks.count
  }
}
```

You could have also created this as a method, like so:
```swift
func count() -> Int {
  return tasks.count
}
```

It would have been shorter, but it lacks some style. By making `count` a computed property, you keep a consistent API with `Array` and don’t try to force what is essentially a property into a method.

What you might not know is that you can actually make this even more concise than the method definition. If you’re creating a computed property, it has no setter, and so you can actually get rid of the second and fourth lines and have this instead:
```swift
var count: Int {
  return tasks.count
}
```

### 4.5 Back to the data source
Moving on from the distraction of the small niceties in Swift, time to finish off this data source. Your `tableView:numberOfRowsInSection:` method is fine now, so it’s time to move onto the `tableView:cellForRowAtIndexPath:` method.

This change is very similar to the change in your `prepareForSegue:sender:` method in your `MasterViewController` and the `configureView` method of our `DetailViewController`, you’re just changing how you get your `Task` and which property you’re using from it.
```swift
override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
  let cell = tableView.dequeueReusableCellWithIdentifier("Cell", forIndexPath: indexPath) as UITableViewCell

  let task = TaskStore.sharedInstance.get(indexPath.row)
  cell.textLabel?.text = task.title
  return cell
}
```

The two lines that changed were the two just before the return statement.  Instead of using the `objects` array and its subscript operator, you're going to use the `get` method from your `TaskStore` once again, assigning the result to a `task` constant instead of a constant called `object`.

On the last line before the return statement, you changed what you’re setting the `text` property of your `textLabel` to the title of the task you got.

### 4.6 Finishing up the basics with the delegate
You can probably see that you have one error left, and that's in the `UITableViewDelegate` method `tableView:commitEditingStyle:forRowAtIndexPath:`.  The change here is quite simple, but it's going to require that you add something to your `TaskStore` again:
```swift
override func tableView(tableView: UITableView, commitEditingStyle editingStyle: UITableViewCellEditingStyle, forRowAtIndexPath indexPath: NSIndexPath) {
  if editingStyle == .Delete {
    TaskStore.sharedInstance.removeTaskAtIndex(indexPath.row)
    tableView.deleteRowsAtIndexPaths([indexPath], withRowAnimation: .Fade)
  } else if editingStyle == .Insert {
    // Create a new instance of the appropriate class, insert it into the array, and add a new row to the table view.
  }
}
```

You only had to alter one line in this method, changing `objects.removeObjectAtIndex(indexPath.row)` to `TaskStore.sharedInstance.removeTaskAtIndex(indexPath.row)` inside the `.Delete` part of the if statement.

Because this method doesn't exist just yet, you need to create it.  Open up your `TaskStore.swift` file and add that in.

### 4.7 One more method for TaskStore
At the end of your `TaskStore` class, you're going to add in the simple `removeTaskAtIndex:` method:
```swift
func removeTaskAtIndex(index: Int) {
  tasks.removeAtIndex(index)
}
```

The reason I got you to do this instead of just accessing the tasks array directly and calling the `removeAtIndex:` method on the array is that it allows you to define an API for consumers of your `TaskStore` class. Later on, you can change the internals of your `TaskStore` class without breaking a bunch of other code.


5 Prettying things up a bit
---------------------------
There are a few small changes you can make to the design that will enhance the application. You still haven’t received your “design from your designers,” but there is an obvious flaw in your design. How do we view the notes?

### 5.1 Altering the Storyboard
In your `Main.storyboard` file, click on the prototype table view cell.  Now you need to change from the default `UITableViewStyle` -- which has just one text label -- to the subtitle style so you can show a preview of the notes.

With the table view selected, you can change the “Style” in the Attributes Inspector. In the drop down, select “Subtitle” and you should see the prototype cell change to contain a second label.

### 5.2 Updating our MasterViewController
Open up the `MasterViewController.swift` file and make some changes to the `tableView:cellForRowAtIndexPath:` method.
```swift
override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
  let cell = tableView.dequeueReusableCellWithIdentifier("Cell", forIndexPath: indexPath) as UITableViewCell

  let task = TaskStore.sharedInstance.get(indexPath.row)
  cell.textLabel?.text = task.title
  cell.detailTextLabel?.text = task.notes
  return cell
}
```

The only change here is setting the text property of your `detailTextLabel` between where you're setting the `text` property of the `textLabel` and the line where you return the cell from the method.

Now if you run the application and add a task (making sure that you fill out the notes field), you can see a preview of your notes when the list of tasks comes back up.  Of course if you fill out the notes field with something too long to fit in the `detailTextLabel` it will just cut it off.  That's what you'll be fixing next.

### 5.3 Redesigning the detail view
The small centered label isn’t really going to cut it, so open the `Main.storyboard` file up again; you’re going make some changes to the `DetailViewController`.

The small centred label isn’t really going to cut it, so open the Main.storyboard file up again; you’re going make some changes to the `DetailViewController`.

Next you need to drag out a Text View from your Object Library into your `DetailViewController`, then make a few changes in the Attributes Inspector after sorting out the layout.

Pump the font size up to 18 points using the font picker which you can access by clicking the “T” icon in the font field. Then, uncheck the “Editable” checkbox just underneath the font field. It would also be nice if you could open up links and phone numbers straight from the notes, so check “Links” and “Phone Numbers” in the “Detection” section of the Attributes Inspector.

Finally, you need to link up the Text View and the `DetailViewController`. Open up the Assistant Editor (click the tuxedo looking icon in the toolbar), then hold control and drag from the Text View to just underneath where you define the `@IBOutlet` for our `detailTextLabel` and call the new outlet “notesView”.

### 5.4 Displaying our notes
With an outlet created, all you have left is the simple task of putting the content into it. Open up the `DetailViewController.swift` file, and make some changes to your `configureView` method.
```swift
func configureView() {
  // Update the user interface for the detail item.
  if let detail: Task = self.detailItem {
    if let label = self.detailDescriptionLabel {
      label.text = detail.title
    }
  }
}
```

You can see the changes here inside of the new if statement inside of the wrapping if statement. I’ve also gone through and removed the pointless use of self in front of all the properties.

First you’re going to unwrap your optional `UITextView`, even though it’s implicitly unwrapped. Then, if it exists, it’s going to be assigned to the `notes` constant, so you can set the `text` of your `notesView` to be the `notes` property of your detail item (a Task).

### 5.5 A basic app
When you hit Run now, you’ll have a basic task management application to play with! Yay! Congratulations on building your first iOS application in Swift!
