
Ultimate Guide to Learning Swift in One Day
===========================================
> Notes by Jack Kasbeer 
>
> May 16, 2019

*Reference documentation:*
[Ultimate Guide to Learning Swift in One Day](https://www.airpair.com/swift/complete-guide-to-swift)


Table of Contents
-----------------
1. What is Swift?
2. Getting Started
3. Syntax
4. App flow & structure
5. Using Swift & Objective-C together
6. Remember, it's still in beta
7. Build your first game using Swift



1 What is Swift?
----------------
In Apple's words:
> Swift adopts safe programming patterns and adds modern features to make programming easier, more flexible, and more fun It is allegedly very easy to learn Swift and get used to it, which is great news if you're new to iOS development. If you're an experienced iOS developer, don't worry, the knowledge and expertise you've gained over the years won't go to waste.


2 Getting Started
-----------------
Make sure you have Xcode installed. Now, you have two options:

Open a new Swift project:
1. Launch Xcode
2. Select File -> New -> Project
3. Select "Single View Application" (under iOS -> Application)
4. Fill in the details and make sure that you choose "Swift" under the
"Language" field
5. Run your newly created project

Use Playground:
1. Launch Xcode
2. Select File -> New -> File
3. Select "Playground" (under iOS -> Source)

Playgrounds allow you to experiment with Swift code and see results immediately, without the overhead of running a full app.


3 Syntax
--------
In Swift, you don't need to write semicolons at the end of every statement.

### 3.1 Variables & Constants
```swift
var sampleVariable = 1     // This is how you define a new variable
let sampleConstant = "Constant" // This is how you define a new constant

var sampleInteger: Int = 3  // Defining a variable with an explicit type
let sampleString: String = "Another Constant"

// Including values/expressions inside strings ("The sum is: 4")
let sumString = "The sum is: \(sampleVariable + sampleInteger)"

var sampleList = ["item1", "item2", "item3"]      // Defining an array
var sampleDict = ["key1" : "value1", "key2" : "value2"] // Defining a dictionary

sampleList[1] = "Updated Item"  // Setting the value of an element
println(sampleDict["key2"]( // Reading the value of an element
```

A few important things you should know about variables and constants in Swift:
- A constant's value doesn't have to be known at compile time, but it can only be assigned a value once.
- You don't have to specify the variable/constant's type. It can be inferred from the provided value.
- Once a variable's type has been set (whether implicitly or explicitly), values assigned to it must conform to that type.
- Similar to other languages you may be accustomed to, the array's first index is 0.
- You can create empty arrays by using the initializer syntax. If the type information can be inferred, you can simply write an empty Array as `[]` and an empty Dictionary as `[:]`. If not, you will need to specify the types (for example, `String[]()` or `Dictionary<String, Int>()`).
- An optional value either contains a 'real' value (`Int`, `String`, etc) or `nil` to indicate that a value is missing. They are defined by adding a question mark (`?`) after the type of the object you want to set up as an optional.

### 3.2 Conditions & Loops
```swift
// This is how you define an optional
var optionalString: String? = "Temp"  

// This is a simple condition
if (sampleInteger > 4)      
{       
   println("true")
}
else
{
   // Using 'if' and 'let' together allows you to check for values that might be missing
   if let nonOptionalString = optionalString
   {
      println("The string's value is: \(nonOptionalString)")
   }
   else
   {
      // If we made it here, it means that the optionalString's value was nil
   }
}

// This is how you use a switch statement
switch sampleString   
{
   // Switch statements aren't limited to just integers
   case "Constant":
      sampleInteger = 10    

   // No need to add 'break' statements. Only matching ones will be executed
   case "Another Constant", "Some Constant":
      sampleInteger = 11 

   // Switch statements support a variety of comparison operations  
   case let x where x.hasPrefix("Constant"):
      sampleInteger = 12 

   // The switch must cover all cases, so a default case should be used 
   default:
      sampleInteger = 13  
}

// A simple for loop
for (var i = 0; i < 6; i++)
{
   println("This is round #\(i)")
}

// A simple loop on a dictionary
for (key, value) in sampleDict  
{
   println("The value for \(key) is \(value)")
}

// A simple loop on an array
for item in sampleList    
{
   println("The current item is \(item)")
}
```

A few important things you should know about conditions and loops in Swift:
- You don't have to use parentheses around the condition but you do have to include braces around the body.
- The conditional must a boolean. This means that implicit comparisons to zero aren't supported (unlike what you may be used to from other languages).
- In addition to the control flow structures that were presented in the code above, you can also use while and do while similar to what you may know from other languages.
- In order to represent a range of values, you can use `...` (includes both the lower and the upper values) or `..<` (omits the upper value). Example: `for i in 0...6`


### 3.3 Functions
```swift
// This is how you define a function (the return value type is defined after the ->)
func sampleFunction(param1: Int, param2: String) -> String
{
    if (param1 == 0)
    {
        return param2
    }
    else
    {
        return "Not Zero"
    }
}

// A function can return multiple values by using tuples
func getChipmunksNames() -> (String, String, String)
{
   return ("Simon", "Alvin", "Theodore")
}

// You can access a specific member of the tuple using . followed by the index
println("The eldest chipmunk is: \(getChipmunksNames().0)")

// A function can take a variable number of arguments, as an array
func sum(numbers: Int...) -> Int 
{
   var sum = 0
   for curNum in numbers
   {
      sum += curNum
   }
   return sum
}

// You can call the function with any amount of elements for the numbers argument
println("The sum of no numbers is: \(sum())")
println("The sum of three numbers is: \(sum(1,2,3))")
println("The sum of six numbers is: \(sum(1,4,7,2,9,124))")

// Functions can be nested (you can define a function inside another function)
func writeStory(firstWord: String) -> String
{
   var curStory = "Once there was a \(firstWord)"

   // Inner functions can access the variables from the outer function
   func continueStory()
   {
      curStory += " and they lived happily ever after"
   }

   continueStory()
   return curStory
}
```

A few important things you should know about Functions in Swift:
- A function can return another function as its return value.
- A function can take another function as one of its arguments.


### 3.4 Objects & Classes
```swift
// This is how you define a class
class Animal
{
   // Properties are declared like variables, but only exist within the class' context
   var name = "Unknown Species"
   var sound = "Silence"

   // This is a basic initializer 
   init(name: String, sound: String)
   {
      self.name = name
      self.sound = sound
   }

   func makeSound() -> String
   {
      return sound
   }
}

// And this is how you instantiate a class and access its properties
var animal = Animal(name: "Dog", sound: "Bark")
animal.name = "Stray Dog"
println("A \(animal.name) make a \(animal.makeSound()) sound")

// This is how you define a subclass
class Dog : Animal
{
   var breed = "Unknown"  
   let crySound: String = "Cry"

   // Properties can also have getters and setters
   var isCrying: Bool
   {
      get
      {
         // Notice that you can compare strings' content using ==
         return sound == crySound
      }
      set
      {
         // The value received for the property is accessed using 'newValue'
         if newValue == true
         {
            sound = crySound
         }
      }
   }

   init(breed: String)
   {
      self.breed = breed
      super.init("Dog", "Bark")
   }

   func goFetch()
   {
      println("Fetch!")
   }

   // Use 'override' for methods that are also defined in the superclass
   override func makeSound() -> String
   {
      return "Bark"
   }
}
```

A few important things you should know about Objects and Classes in Swift:
- You can use `self.` in order to distinguish between property and argument names, similar to what you may be accustomed to from Objective-C.
- Every property has to have a value assigned to it (either in the declaration or in the initializer method).
- You can also use `deinit` in order to create a method that will be called when the class' instance is deallocated. Use this when there are cleanups that need to be done when the object is destroyed.
- In addition to using getters and setters, you can also use `didSet` and `willSet` if you want to ensure that certain properties will never conflict with other properties' new values.
- Unlike functions, when you call a method on an object, you need to also specify the argument names. You can omit the name for the first argument if you want. The rest are mandatory.
- The default 'inside' names for the arguments are identical to the ones used when referenced from outside the class (when calling the method). You can optionally specify a second (different) name for each argument to be used inside the method.


### 3.5 Enumerations
```swift
// This is how you define an enum
enum CardShape: Int
{
   // We defined the raw value for the enum as Int, so specifying the first is enough
   case Hearts = 1
   case Clubs, Spades, Diamonds 

   // Enums can have methods associated with them
   func description() -> String
   {
      switch self
      {
         case .Hearts:
            return "Hearts"

         case .Clubs:
            return "Clubs"

         case .Spades:
            return "Spades"

         case .Diamonds:
            return "Diamonds"

         default:
            return "Undefined"
      }
   } 

   func isRed() -> Bool
   {
      case .Hearts, .Diamonds:
         return true

      default:
         return false
   }
}

// Getting the raw value for an enum value (in this case, 3)
// Raw value types can also be strings or floating point values
var heartShapeRawValue = CardShape.Spades.toRaw()

var heartShape = CardShape.fromRaw(heartShapeRawValue)
println("One of the card shapes is \(heartShape.description())")
```

One important thing you should know about Enumerations in Swift:
- If there isn't a meaningful raw value for the enum you're creating, you don't have to specify one. Example: Just use `enum CardShape {...}` instead of `enum CardShape: Int {...}`


4 App flow & structure
----------------------
The basic app structure in Swift is very similar to the one you may know from Objective-C.  When you create a new project using the "Single View Application" template we mentioned earlier, you'll see two main files:
- AppDelegate.swift
- ViewController.swift

Those of you familar with iOS development can see the resemblance to the equivalent files on Objective-C projects.  Let's quickly go over the important methods in these files.

In *AppDelegate.swift*, you can find:
```swift
func application(application: UIApplication, 
    didFinishLaunchingWithOptions launchOptions: NSDictionary?) -> Bool
```

Which is the first point of customisation after the application is launched.


You can also find the methods that are called when the app is sent to/from the background, resigns/becomes active (when a phone call is received, for example) and when the application is eventually terminated (removed from the background by the user or by the OS).  This is what these methods look like in Swift:
```swift
func applicationWillResignActive(application: UIApplication)

func applicationDidEnterBackground(application: UIApplication)

func applicationWillEnterForeground(application: UIApplication)

func applicationDidBecomeActive(application: UIApplication)

func applicationWillTerminate(application: UIApplication)
```

In *ViewController.swift*, you can find:
```swift
override func viewDidLoad()

override func didReceiveMemoryWarning()
```

The `viewDidLoad` method is called (as its name suggests) when the view finishes loading.  This is where you'll put necessary logic for the view's proper display.

The `didReceiveMemoryWarning` method is a chance for you as the developer to clean things up if the OS decides to warn you that your app is taking up too much space and may be termined soon unless you take some action.


5 Using Swift & Objective-C together
------------------------------------
If you have an existing Objective-C iOS project and don't want to completely migrate it to Swift, but still want to use some of Swift's advantages (and learn to master the language as you code), you can mix and match.  You can have both Swift and Objective-C files coest in the same project, no matter what was the project's original language.  All you need to do is create a new `.swift` file and add it to your existing Objective-C project.


### 5.1 Objective-C code in a Swift file
In order to access your Objective-C classes from your Swift code, you'll need to rely on an Objective-C bridging header.

When you add a file from a different programming language, Xcode will offer to create such a bridging header file for you.  The name of the file should be "[your-product-module-name]-Bridging-Header.h".  In that file, you will need to import every Objective-C header you want to expose to Swift.  So, an example bridging file can look like this:
```swift
#import "SampleClass.h"
#import "SampleView.h"
#import "SomeOtherSampleClass.h"
```

All of the classes included in this file will be available in all Swift files without the need to import/include them in these files. Using these classes is done with the standard Swift syntax.


### Swift code in an Objective-C file
Any Swift files that are accessible in your target will be visible in your Objective-C files. They will be included in an automatically generated Objective-C header file named "[your-product-module-name]-Swift.h". In order to access Swift classes inside your Objective-C code, all you need to do is import this bridge header file in any relevant Objective-C .m file:
```swift
#import "[your-product-module-name]-Swift.h"
```

*Note:* [your-product-module-name] = your-product-name (unless it contains non alphanumeric characters, in which case these characters will be replaced with an underscore).


6 Remember, it's still in beta
------------------------------
Now that you have all of the basics down and have almost reached the end of this guide, it's important to stress one thing - Swift is currently still in beta. This means that it can (and probably will) change. 

Also, as an up and coming Swift guru, you owe it to yourself to make sure you don't miss any of these announcements. The best way to do this is to follow Apple's Swift Blog at https://developer.apple.com/swift/blog


7 Build your first game in Swift
--------------------------------
Continue with the 3 part tutorial:
- Part 1: The basics of working with Swift in XCode
- Part 2: Getting started with the Interface Builder, storyboards, and structs
- Part 3: Gluing the Swift app together and customizing the user interface
