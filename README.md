# iOS Swift and Swift UI -Questions-Answers-2025
This guide helps you prepare for **iOS developer interviews** with practical Q&A and code samples.

---

## 🗂️ Table of Contents
1. [MVC vs MVVM in Swift and SwiftUI](#mvc-vs-mvvm-in-swift-and-swiftui)
2. [Struct vs Class in Swift](#struct-vs-class-in-swift)   
---

# 🧩 MVC vs MVVM in Swift and SwiftUI

This guide explains the **difference between MVC and MVVM** architecture patterns in **UIKit (Storyboard)** and **SwiftUI**, with clean examples and visuals.

---

## 1️⃣ MVC with Storyboard

### 🧠 Model
```swift
struct User {
    let name: String
}
```

### **Controller (ViewController)**
```swift
import UIKit

class UserViewController: UIViewController {
    @IBOutlet weak var nameLabel: UILabel!  // connected from storyboard
    
    var user: User?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        user = User(name: "John")
        nameLabel.text = user?.name   // Controller updates View
    }
}
```

👉 In **MVC**, the `ViewController` holds both **UI code** and **logic**, which makes it become *fat* when the app grows.

---

## 2️⃣ MVVM with Storyboard

### 🧠 Model
```swift
struct User {
    let name: String
}
```

### 💡 ViewModel
```swift
class UserViewModel {
    private let user: User
    
    init(user: User) {
        self.user = user
    }
    
    var displayName: String {
        return "Hello, \(user.name)"
    }
}
```

### 🎨 View (ViewController)
```swift
import UIKit

class UserViewController: UIViewController {
    @IBOutlet weak var nameLabel: UILabel!   // connected from storyboard
    
    var viewModel: UserViewModel!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        viewModel = UserViewModel(user: User(name: "John"))
        nameLabel.text = viewModel.displayName   // View just binds to ViewModel
    }
}
```

👉 In **MVVM**, the **ViewController** simply binds to a **ViewModel**, keeping UI logic separate from business logic.

---

## 3️⃣ SwiftUI Examples

### 🔹 MVC-like in SwiftUI
*(SwiftUI doesn’t truly use MVC, but if all logic is inside the `View`, it behaves like MVC.)*

```swift
// Model
struct User {
    let name: String
}

// View (acts as View + Controller)
struct UserView: View {
    let user = User(name: "John")  // Model directly inside View

    var body: some View {
        VStack {
            Text("Welcome")
            Text(user.name)   // Directly using model
        }
    }
}
```

👉 Here, `UserView` is both **View + Controller**.  
Good for small UIs but messy as logic grows.

---

### 🔹 MVVM in SwiftUI
*(Uses `ObservableObject` and property wrappers like `@StateObject` or `@ObservedObject`.)*

```swift
// Model
struct User {
    let name: String
}

// ViewModel
class UserViewModel: ObservableObject {
    @Published var user: User
    
    init(user: User) {
        self.user = user
    }
    
    var displayName: String {
        "Hello, \(user.name)"
    }
}

// View
struct UserView: View {
    @StateObject var viewModel = UserViewModel(user: User(name: "John"))
    
    var body: some View {
        VStack {
            Text("Welcome")
            Text(viewModel.displayName)  // View observes ViewModel
        }
    }
}
```

👉 In **MVVM**:
- **View** only shows UI  
- **ViewModel** handles logic — API calls, validation, state management  
- Easier to test the **ViewModel** separately without UI

---

## ⚖️ Difference Between MVC and MVVM

| Feature | MVC | MVVM |
|----------|-----|------|
| **Model Creation** | In Controller | In ViewModel |
| **Logic Location** | Controller (UI + Logic mixed) | ViewModel (clean separation) |
| **Testing** | Hard to test (UI-dependent) | Easy (ViewModel testable separately) |
| **Best For** | Simple Apps / UIKit | Large Apps / SwiftUI |
| **SwiftUI Usage** | ❌ Not Recommended | ✅ Recommended |

---

## 🧠 Explanation

- In **MVC**, the model object is created in the **controller**.  
  Swift (UIKit) uses this approach — but it’s **not recommended in SwiftUI**.  
  The controller becomes heavy and difficult to maintain.

- In **MVVM**, the model object is created inside the **ViewModel**.  
  The **View** uses a **ViewModel instance**.  
  The ViewModel contains **all business logic**, keeping UI code clean and testable.

- In **SwiftUI**:
  - The **ViewModel** extends `ObservableObject`
  - The **View** uses `@StateObject` to access the ViewModel
  - The View only shows UI, while logic (like API calls or validation) stays in ViewModel

---

## 🧭 Architecture Overview

### MVC
```
Model  <---->  Controller  <---->  View
```
🧩 The Controller acts as the “glue” → often becomes too big and hard to maintain.

---

### MVVM
```
Model  <---->  ViewModel  <---->  View
              (binding/observe)
```
🧩 The View simply observes the ViewModel → cleaner and scalable.

---

## 💡 Quick Memory Hook

> 🟢 **MVC = Simple but Messy (fat controllers)**  
> 🟣 **MVVM = Clean but Complex (needs bindings)**

---

### ✅ Summary

> In **Storyboard (UIKit)** — MVC makes the `ViewController` heavy because it updates UI directly from the model.  
> In **MVVM**, the ViewController binds to the ViewModel, keeping it light and **testable**.

---

## 🧪 Unit Testing Comparison

| Architecture | Unit Testing | Reason |
|---------------|--------------|--------|
| **MVC** | ❌ Difficult | Logic mixed with UI (IBOutlet, View) |
| **MVVM** | ✅ Easy | Logic in ViewModel without UI dependencies |

🧠 In MVVM, create a **test case file** for your ViewModel and test its logic independently.


---
# 🧩 Struct vs Class in Swift
---
## ⚙️ Type Difference
| Concept | Struct | Class |
|----------|---------|--------|
| **Type** | Value Type | Reference Type |
| **Memory** | Copied when assigned | Shared (reference) |
| **Inheritance** | ❌ Not supported | ✅ Supported |
| **Mutability** | Needs `var` to modify | Can mutate even if reference is `let` |
| **Deinitializer** | ❌ No `deinit` | ✅ Has `deinit` |

👉 **Quick Hook:**  
“**Struct = Value, no Inheritance. Class = Reference, Inheritance + deinit.**”  
Structs create *copies* (safe, independent). Classes share *references* (changes reflect everywhere).

---

## 🧠 Example: Value Type vs Reference Type

### Struct (Value Type)
```swift
struct StructUser {
    var name: String
}

var a = StructUser(name: "John")
var b = a  // COPY

a.name = "Mike"

print(a) // [ name: "Mike" ]
print(b) // [ name: "John" ]  // independent copy
```

### Class (Reference Type)
```swift
class ClassUser {
    var name: String
    init(name: String) {
        self.name = name
    }
}

var a = ClassUser(name: "John")
var b = a  // REFERENCE (both point to same object)

a.name = "Mike"

print(a.name) // Mike
print(b.name) // Mike  // both reflect change
```

---

## 🧱 Difference Between Class and Structure

### **Class**
- It is **pass by reference** → assigning it to another variable makes a **reference**, not a copy.  
  So, changes in one reflect in another.  
- Acts as a **blueprint** of an entity.  
- Has **properties** and **methods**.  
- **Supports:** Constructors, Inheritance, Method Override.  
- Must add an `init()` method.  
- Use `super` to access parent methods.  
- Can adopt **protocols**.  

Example
NSObject -> UIResponder -> UIView -> UIController -> UIButton

#### Example:
```swift
class Animal {
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    func move() {
        print("Animal can move")
    }
}

class Dragon: Animal {
    func fly() {
        print("Dragon can fly")
    }
    
    override func move() {
        print("Dragon can move fast")
    }
}

let animal = Animal(name: "Dog")
animal.move() // Animal can move

let dragon = Dragon(name: "Draco")
dragon.move() // Dragon can move fast
dragon.fly()  // Dragon can fly
```

---

### **Struct**
- It is **pass by value** → assigning it to another variable makes a **separate copy**.  
  Changes in one do not affect the other.  
- Can adopt **protocols**.  
- Use `mutating` keyword to change property values inside methods.  
  ```swift
  mutating func takeDamage(_ amount: Int) {
      health -= amount
  }
  ```
- Structs are **immutable** by default.

#### Example:
```swift
struct Animal {
    var name: String
    
    func move() {
        print("Animal can move")
    }
}

let animal = Animal(name: "Tiger")
animal.move() // Animal can move
```

---

## 🧭 Choosing Between Structure and Class
- ✅ Use **Struct** by default.
- 🧩 Use **Class** when you need **Objective‑C interoperability**.

---

## 🧾 Key Differences Summary
| Aspect | Struct | Class |
|---------|---------|--------|
| **Memory Handling** | Copied | Referenced |
| **Inheritance** | ❌ No | ✅ Yes |
| **Deinitializer** | ❌ No | ✅ Yes |
| **Initializer** | Automatic memberwise | Custom required |
| **Retain Cycle** | ❌ Not possible | ✅ Possible |
| **Weak Reference** | ❌ Not allowed | ✅ Allowed |

---

## 🧮 Struct vs Enum (Bonus)

| Feature | Struct | Enum |
|----------|---------|-------|
| **Purpose** | Stores multiple properties/methods | Defines fixed set of related values |
| **Cases** | None | Has named cases |
| **Associated Values** | Normal properties | Can have associated values per case |
| **Inheritance** | ❌ | ❌ |
| **Usage** | Data models | States, options, or choices |

👉 **Quick Hook:**  
“**Struct = data container, Enum = fixed options with cases.**”

---
# 🧩 Swift Memory Management & Modifiers Guide

This README provides a detailed explanation of **Memory Management (ARC)** and different **Modifiers in Swift**, essential for interview preparation and professional iOS development.

---

## 🗂️ Table of Contents
1. [Memory Management (ARC)](#memory-management-arc)
2. [View Modifiers (SwiftUI)](#view-modifier-swiftui)
3. [Access Modifiers](#access-modifier)
4. [Type Modifiers](#type-modifier)
5. [Declaration Modifiers](#declaration-modifier)

---

## 🧩 Memory Management (ARC)

**ARC (Automatic Reference Counting)** automatically manages memory by counting object references.

### 🧠 Reference Types

| Type | Description | Example Usage |
|------|--------------|----------------|
| **Strong** | Default reference; keeps object in memory as long as reference exists. | Regular variable reference |
| **Weak** | Optional reference; does not keep object in memory; becomes `nil` automatically when object deallocates. | Use in delegates or parent-child references |
| **Unowned** | Non-optional reference; does not keep object in memory; assumes object will never be nil during its lifetime. | Use when lifetime of reference is guaranteed |

### 💡 Memory Safety
- **Strong** can cause retain cycles.  
- **Weak** and **Unowned** prevent retain cycles.

### 🔧 Example
```swift
class Parent {
    var children: [Child] = []
}

class Child {
    weak var parent: Parent?        // Weak to avoid retain cycle
    unowned let school: School      // Unowned when guaranteed to exist
}
```

### ✅ Best Practices
- Use **weak** for delegates and parent references.  
- Use **unowned** only when you're certain the reference will never be `nil`.  
- Be careful of **retain cycles** in closures.

👉 **Quick hook:** “Strong owns, Weak doesn’t but optional, Unowned doesn’t and assumes non-nil.”

---

## 🎨 View Modifier (SwiftUI)

These modify the **appearance or behavior of a view**.

Examples:
- `.font()`  
- `.padding()`  
- `.background(Color.blue)`  
- `.onTapGesture { }`

```swift
Text("Hello, SwiftUI!")
    .font(.title)
    .padding()
    .background(Color.yellow)
    .onTapGesture {
        print("Tapped!")
    }
```

---

## 🔐 Access Modifier

Define **where** your class, struct, or property can be accessed.

| Modifier | Access Scope | Notes |
|-----------|---------------|-------|
| **private** | Within its class or struct | Most restrictive |
| **fileprivate** | Within same Swift file | Useful for helper extensions |
| **internal** | Default (within same module) | Default level |
| **public** | Accessible from other modules | Can’t be subclassed outside |
| **open** | Accessible & subclassable from anywhere | Frameworks/API use this |

```swift
class BankAccount {
    private var balance: Double = 0.0

    public func deposit(amount: Double) {
        if amount > 0 {
            balance += amount
        }
    }
}

class TransactionManager {
    var description: String = "Transaction"
}
```

---

## ⚙️ Type Modifier

Affect **how a type behaves** or how its members are accessed.

| Modifier | Description |
|-----------|--------------|
| **static** | Belongs to type, not instance. Cannot be overridden. |
| **class** | Type-level method/property that can be overridden. |

```swift
class Animal {
    static func category() -> String {
        return "Mammal"
    }
    
    class func sound() -> String {
        return "Generic Sound"
    }
}
```

---

## 🧾 Declaration Modifier

Change how declarations behave, their visibility, or lifecycle.

| Modifier | Description |
|-----------|--------------|
| **open** | Can be subclassed/overridden anywhere |
| **public** | Usable anywhere but not overridable outside module |
| **internal** | Default access; available in same module |
| **fileprivate** | Access within same file |
| **private** | Access within same class/struct |
| **final** | Prevent subclassing/overriding |
| **static** | Belongs to type, not instance |
| **class** | Allows overriding by subclass |
| **mutating** | Allows struct/enum methods to modify self |
| **lazy** | Property created only on first use |
| **override** | Replace superclass implementation |
| **required** | Subclasses must implement initializer |
| **convenience** | Secondary initializer calling designated one |
| **optional** | Protocol method/property may be unimplemented |
| **inout** | Allows function to modify passed variable directly |
| **dynamic** | Enables Objective-C runtime method dispatch |
| **@MainActor** | Runs code on main thread |
| **@objc** | Exposes Swift to Objective-C runtime |
| **@discardableResult** | Ignores unused return value warnings |
| **@available** | Limits availability per OS version |
| **@nonobjc** | Hides method from Objective-C runtime |

### Example
```swift
final class Example {
    lazy var data = [1, 2, 3]

    @discardableResult
    func add(_ num: Int) -> [Int] {
        data.append(num)
        return data
    }
}
```

---

## 🧠 Summary

| Concept | Description |
|----------|--------------|
| **Strong** | Owns memory |
| **Weak** | Non-owning, optional |
| **Unowned** | Non-owning, non-optional |
| **Access Modifiers** | Control visibility |
| **Declaration Modifiers** | Control behavior |
| **Type Modifiers** | Change static/class behavior |

👉 **Quick Memory Tip:**  
“**Strong owns**, **Weak doesn’t (optional)**, **Unowned doesn’t (non-optional)**.”


---
# 🧩 Optionals in Swift
---

### 🧩 Constants and Variables
- **let** → Declares a constant; value cannot be changed after assignment.  
- **var** → Declares a variable; value can be modified anytime.

### ❓ Optionals
- **Optional (?)** → Variable that may contain a value or `nil`.

### ✅ Safe Unwrapping
- **if let** → Safely unwraps an optional within a block; executes code only if value exists.  
- **guard let** → Safely unwraps an optional; exits current scope if value is nil, ensures safe usage after.

👉 **Quick hook:** “`let` = constant, `var` = changeable, `Optional` = maybe nil, `if let` = unwrap in block, `guard let` = unwrap early and exit if nil.”

---

### 💡 Why Optionals?
Optionals handle `nil` values safely, preventing null pointer exceptions.

### 🧠 Example in Swift
```swift
var name: String? = "John"

// Safe unwrapping using if-let
if let unwrapped = name {
    print(unwrapped)
}

// Safe unwrapping using guard-let
guard let unwrapped = name else {
    return
}

// Nil coalescing — use a default value if nil
let result = name ?? "Default"

// Force unwrap — only use if you're sure it's not nil
let forced = name!
```

---

### 🧭 When to Use Each
| Technique | Use Case |
|------------|-----------|
| **Optional binding (if let)** | When you need to use the value temporarily in a small scope |
| **Guard let** | Early exit pattern, cleaner flow |
| **Nil coalescing (??)** | Provide default values for nil cases |
| **Force unwrap (!)** | Only when absolutely certain the value exists |

---

📘 **Summary:**  
Optionals make Swift safer and more predictable by handling `nil` values gracefully — preventing runtime crashes.


# 🧠 Swift Properties Explained — Stored, Computed & Lazy

---
What is Stored Properties, Computed Properties, Lazy stored properties?
---

## 📦 Stored Properties
Stored properties **store actual values** for a constant or variable.

### 🔹 Example

```swift
let pizzaInInches = 12   // Constant stored property
var numberOfSlices = 5   // Variable stored property
```

- `let` → constant (cannot be changed)
- `var` → mutable (can change value)

---

## ⚙️ Computed Properties
Computed properties **don’t store values** — instead, they **calculate** them each time you access them.

### 🔹 Example 1 (Getter Only)

```swift
let pizzaInInches = 12

var numberOfSlices: Int {
    return pizzaInInches - 4
}

print(numberOfSlices) // Output: 8
```

### 🔹 Example 2 (Getter & Setter)

```swift
let pizzaInInches = 12

var numberOfSlices: Int {
    get {
        return pizzaInInches - 4
    }
    set {
        print("New value is \(newValue)")
    }
}

print(numberOfSlices)  // Output: 8
numberOfSlices = 10    // Output: "New value is 10"
```

### 🧠 Notes
- Must be declared with `var` (cannot use `let`)
- Must have a **return type**
- Acts as **getter/setter**
- **`newValue`** gives access to the updated value inside setter
- Can replace small functions
- Reduces lines of code

---

## 💤 Lazy Stored Properties
A **lazy stored property** delays its initialization until the first time it’s used.  
This is useful when:
- Initialization is **expensive**
- The value **may not always be needed**
- You want to save **memory and performance**

### 🔹 Example

```swift
lazy var persistentContainer: NSPersistentContainer = {
    let container = NSPersistentContainer(name: "DataModel")
    container.loadPersistentStores { (storeDescription, error) in
        if let error = error as NSError? {
            fatalError("Unresolved error \(error), \(error.userInfo)")
        }
    }
    return container
}()
```

### 🧠 Notes
- Initialized **only when accessed**
- Must be declared with **var**
- Cannot be used with **let**
- Provides **memory benefit**

---

## ⚖️ Difference Between Lazy & Computed Properties

| Feature | Lazy Property | Computed Property |
|----------|----------------|------------------|
| Stores Value | ✅ Yes (stored value) | ❌ No (calculated on access) |
| When Loaded | When accessed first time | Every time it’s accessed |
| Memory Benefit | ✅ Yes | ❌ No |
| Declaration | Must be `var` | Must be `var` |
| Use Case | Expensive setup (e.g., Core Data, networking) | Lightweight calculations |

---

✅ **Summary**
- **Stored** → holds value  
- **Computed** → calculates value  
- **Lazy** → stores value but initializes later  

Each serves a specific purpose for optimizing your app’s performance and readability.


---
### What are property wrappers?

---
Built In Property Wrapper
---

@State, @Binding @Published, @AppStorage are all built-in property wrappers provided by Swift (mainly for SwiftUI and Combine).

| Wrapper         | Framework | Purpose                                                                    | Example                                                            |
| --------------- | --------- | -------------------------------------------------------------------------- | ------------------------------------------                         |
| **@State**      | SwiftUI   | Used for local, mutable state inside a SwiftUI view.                       | `@State private var count = 0`                                     |
| **@binding  **  | SwiftUI   | It provides a two-way data flow — when the child updates the value, the parent sees the change too.” | `@Binding var name: String"`             |
| **@Published**  | Combine   | Used inside `ObservableObject` to automatically publish changes.           | `@Published var name = "John"`                                     |
| **@AppStorage** | SwiftUI   | Stores data directly in **UserDefaults** and keeps it in sync with the UI. | `@AppStorage("theme") var theme = "Light"`                         |


- All of them are property wrappers, meaning they:
- Wrap around a stored property (wrappedValue)
- Add extra behavior such as:
- Auto-updating UI (@State, @AppStorage)
- Notifying subscribers (@Published)
- Are written with the @ syntax.


---
🧠 What Are Property Observers?
---

Property observers are special blocks of code that run automatically when a property’s value changes.
They allow you to observe and respond to changes in stored properties.

| Observer      | When It Runs                      | Purpose                                                 |
| ------------- | --------------------------------- | ------------------------------------------------------- |
| **`willSet`** | Just **before** the value changes | Lets you know the *new value* that’s about to be set    |
| **`didSet`**  | Right **after** the value changes | Lets you access the *old value* and react to the change |


```swift
var score: Int = 0 {
    willSet {
        print("Score will change to \(newValue)")
    }
    didSet {
        print("Score changed from \(oldValue) to \(score)")
    }
}

score = 10

Output:
Score will change to 10
Score changed from 0 to 10

```
---
🧠 What Are Property Accessors?
---

Those are property accessors, not observers.
get and set are called Property Accessors
They are used in computed properties, not stored ones.

```swift
var doubleScore: Int {
    get { score * 2 }
    set { score = newValue / 2 }
}

```

| Keyword             | Type          | Used For                                               |
| ------------------- | ------------- | ------------------------------------------------------ |
| `get`, `set`        | **Accessors** | Define how a **computed property** reads/writes values |
| `willSet`, `didSet` | **Observers** | React to **changes** in stored properties              |


---
# What is Custom Property Wrappers, Projected value? 
---

Property wrappers add **extra behavior** to Swift properties.  
They help you add logic around how a property is **stored, read, or modified** — without repeating code.

---

## ✳️ Basic Example

```swift
@propertyWrapper
struct Capitalized {
    private var text: String = ""

    var wrappedValue: String {
        get { text }
        set { text = newValue.capitalized }
    }
}

struct User {
    @Capitalized var name: String
}

var user = User()
user.name = "john doe"
print(user.name) // Output: "John Doe"
```

✅ *Explanation:*  
Every time you assign a value to `name`, it automatically becomes capitalized.

---
## 🪄 What Is a Projected Value and Wrapped Value?
----

When you create a **property wrapper**, Swift gives you two special abilities:  

1. A **wrapped value** – the real data you use every day (e.g., `player.score`).  
2. A **projected value** – extra info or helper you access using `$` (e.g., `player.$score`).

---

### Example – Custom Wrapper

```swift
@propertyWrapper
struct Limited {
    private var value: Int = 0
    private var maxLimit: Int

    // ✅ wrappedValue – the real property value
    var wrappedValue: Int {
        get { value }
        set { value = min(newValue, maxLimit) }
    }

    // ✅ projectedValue – the extra helper or message
    var projectedValue: String {
        return "Value is within \(maxLimit) limit"
    }

    init(limit: Int) {
        self.maxLimit = limit
    }
}

struct Player {
    @Limited(limit: 100) var score: Int
}

var player = Player()
player.score = 120
print(player.score)   // Output: 100
print(player.$score)  // Output: "Value is within 100 limit"
```

---

### 🧩 How It Works (Under the Hood)

When you write:
```swift
@Limited(limit: 100) var score: Int
```

Swift automatically expands it into something like this:

```swift
private var _score = Limited(limit: 100)

var score: Int {
    get { _score.wrappedValue }
    set { _score.wrappedValue = newValue }
}

var $score: String {
    _score.projectedValue
}
```

So:
- `score` → uses `wrappedValue`
- `$score` → uses `projectedValue`

---

### 💡 Quick Summary

| Concept | Used As | Purpose | Example |
|----------|----------|----------|----------|
| **wrappedValue** | `score` | The actual value you read/write | `player.score = 120` |
| **projectedValue** | `$score` | Provides extra info or helper behavior | `print(player.$score)` |

---

## 🔍 What Is a Property Observer?

Property observers let you **react to value changes** in a stored property.  
They are called **every time** the property’s value is set.

### Example:

```swift
class Counter {
    var value: Int = 0 {
        willSet {
            print("About to set value to \(newValue)")
        }
        didSet {
            print("Value changed from \(oldValue) to \(value)")
        }
    }
}

let counter = Counter()
counter.value = 5
// Output:
// About to set value to 5
// Value changed from 0 to 5
```

✅ *Explanation:*  
- `willSet` runs **before** the value changes.  
- `didSet` runs **after** the value changes.

---

🧩 **Summary:**
| Concept | Purpose | Example |
|----------|----------|----------|
| **Property Wrapper** | Add reusable logic around property storage | `@State`, `@Published` |
| **Wrapped Value** | The property’s main value | `player.score` |
| **Projected Value** | Helper accessed using `$` | `player.$score` |
| **Property Observer** | React to changes (`willSet`, `didSet`) | `counter.value = 5` |

---
🚀 *Now you understand how property wrappers, projected values, and property observers work together in Swift!*
---
## 🧩 SwiftUI & UIKit Integration
---

### How do you manage state in SwiftUI?
- `@State` for local view state.  
- `@ObservedObject` or `@StateObject` for shared model state.  
- `@EnvironmentObject` for global data.

```swift
struct CounterView: View {
    @State private var count = 0
    var body: some View {
        VStack {
            Text("Count: \(count)")
            Button("Increment") { count += 1 }
        }
    }
}
```

---
# 🧩 SwiftUI Data Flow

SwiftUI provides several **property wrappers** to manage data flow between views.

---

## 🧠 Property Wrappers Overview

| Wrapper | Purpose |
|----------|----------|
| `@State` | Local state within a view |
| `@Binding` | Two-way connection to parent’s state |
| `@StateObject` | Owns lifecycle of an ObservableObject |
| `@ObservedObject` | External object that publishes changes |
| `@EnvironmentObject` | Shared object across the view hierarchy |

---

## 🔹 @State

When a property's value changes, SwiftUI **automatically refreshes the UI**.

- Stores values that can change over time.
- Refreshes dependent views automatically.

```swift
@State var count = 0

var body: some View {
    Button("Tap Count: \(count)") {
        count += 1
    }
}
```

---

## 🔹 @Binding

Used to **create a two-way connection** between parent and child views.

If a child view updates the value, it reflects in the parent as well.

**Parent View:**

```swift
struct ContentView: View {
    @State private var name: String = ""

    var body: some View {
        VStack {
            LoginField(text: $name)
            Text("Name is \(name)")
        }.padding()
    }
}
```

**Child View:**

```swift
struct LoginField: View {
    @Binding var text: String

    var body: some View {
        TextField("Enter Text", text: $text)
    }
}
```
<img width="492" height="462" alt="Screenshot 2025-10-28 at 7 59 24 PM" src="https://github.com/user-attachments/assets/c325f214-d837-47ae-b9f7-b8eeeb8b8a31" />


---

## 🔹 @StateObject vs @ObservedObject

- `@StateObject` → Used when the **view owns** the data (persists value even when recreated).
- `@ObservedObject` → Used when the **data is external** (recreates instance each time).

```swift
struct RandomNumberView: View {
    @State var number: Int = 0

    var body: some View {
        VStack {
            Text("Random number: \(number)")

            Button("Generate Random Number") {
                number = (0...1000).randomElement() ?? 0
            }

            Divider().padding()

            ContentView()
        }
    }
}

struct ContentView: View {
    @State var count: Int = 0
    @ObservedObject var vm = CounterViewModel() // Not persistent

//    @StateObject var vm = CounterViewModel() // Persistent

    var body: some View {
        VStack {
            Text("State Count: \(count)")
            Text("Observed Obj Count: \(vm.count)")

            Button("Increment") {
                count += 1
                vm.increment()
            }
        }
    }
}

final class CounterViewModel: ObservableObject {
    @Published var count: Int = 0

    func increment() {
        count += 1
    }
}
```

<img width="631" height="530" alt="Screenshot 2025-10-28 at 7 54 11 PM" src="https://github.com/user-attachments/assets/16abee03-8ad7-4230-a6f0-80c96b5619f3" />

---

## 🔹 @EnvironmentObject

Allows **shared data across all views** without manual passing.

### Example

**Main App:**

```swift
@main
struct EnvironmentObjExampleApp: App {
    @StateObject private var productVM = ProductViewModel()

    var body: some Scene {
        WindowGroup {
            FirstView().environmentObject(productVM)
        }
    }
}
```

**ViewModel:**

```swift
class ProductViewModel: ObservableObject {
    @Published var productName = "Mac Mini"
    @Published var productPrice = 80000.00
}
```

**First View:**

```swift
struct FirstView: View {
    @EnvironmentObject var productVM: ProductViewModel

    var body: some View {
        NavigationView {
            VStack {
                Text("Product: \(productVM.productName)")
                Text("Price: \(productVM.productPrice)")

                NavigationLink("Go to Second Screen") {
                    SecondView()
                }
                NavigationLink("Go to Third Screen") {
                    ThirdView()
                }
            }
        }
    }
}
```

**Second View:**

```swift
struct SecondView: View {
    @EnvironmentObject var productVM: ProductViewModel

    var body: some View {
        VStack {
            TextField("Product Name", text: $productVM.productName)
            TextField("Product Price", value: $productVM.productPrice, format: .number)
            Text("Now: \(productVM.productName) - \(productVM.productPrice)")
        }
    }
}
```

**Third View:**

```swift
struct ThirdView: View {
    @EnvironmentObject var productVM: ProductViewModel

    var body: some View {
        VStack {
            Text("Product: \(productVM.productName)")
            Text("Price: $\(productVM.productPrice, specifier: "%.2f")")
        }
    }
}
```
<img width="599" height="379" alt="Screenshot 2025-10-28 at 8 00 59 PM" src="https://github.com/user-attachments/assets/6bd52650-6a9d-4272-a893-431adf8f7db9" />

---

## ✅ When to Use Which

| Wrapper | Use Case |
|----------|-----------|
| `@State` | Simple local state in a single view |
| `@Binding` | Two-way data sharing between parent-child |
| `@StateObject` | Persistent state owned by a view |
| `@ObservedObject` | Reusable external data model |
| `@EnvironmentObject` | Global shared data (e.g., theme, user session) |

---

👉 **Quick Hook:**  
“State stores, Binding shares, StateObject owns, ObservedObject watches, EnvironmentObject spreads.”


<img width="521" height="346" alt="Screenshot 2025-10-28 at 8 03 11 PM" src="https://github.com/user-attachments/assets/05042398-2c82-41f1-9f64-72b49393b2e6" />

<img width="410" height="422" alt="Screenshot 2025-10-28 at 8 03 34 PM" src="https://github.com/user-attachments/assets/1a90863a-812b-468a-ab29-2b22d8b2d290" />

<img width="597" height="460" alt="Screenshot 2025-10-28 at 8 04 08 PM" src="https://github.com/user-attachments/assets/fad9c77e-f93d-4f78-9fca-7d1e2103d1f9" />

<img width="482" height="576" alt="Screenshot 2025-10-28 at 8 04 25 PM" src="https://github.com/user-attachments/assets/8f6b969a-f509-45ee-83de-c52cd3fbed09" />

<img width="611" height="204" alt="Screenshot 2025-10-28 at 8 04 52 PM" src="https://github.com/user-attachments/assets/4d982a87-1c0c-4389-8a86-eefb4f5c9362" />


---
## 🧩  What is Codable?
---

**Codable** is a type alias that combines two protocols — `Encodable` and `Decodable`.

```swift
typealias Codable = Encodable & Decodable
```

It allows Swift objects to be easily converted to and from **JSON**, **Property Lists**, or other data formats.

---

## 🧱 Codable Includes Two Protocols

### 1. 📝 Encodable
Converts Swift objects **into JSON** (or other formats).

Commonly used with:
- `JSONEncoder()`  
- `PropertyListEncoder()`

### 🔹 Example — Encode (Swift → JSON)

```swift
func saveItems() {
    let userdefaults = UserDefaults.standard
    if let savedData = try? JSONEncoder().encode(itemArray) {
        userdefaults.set(savedData, forKey: "todoItems")
    }
    self.tableView.reloadData()
}
```

---

### 2. 📦 Decodable
Converts **JSON data back into Swift objects**.

Commonly used with:
- `JSONDecoder()`  
- `PropertyListDecoder()`

### 🔹 Example — Decode (JSON → Swift)

```swift
func getData() {
    let userdefaults = UserDefaults.standard
    if let savedData = userdefaults.data(forKey: "todoItems") {
        itemArray = try! JSONDecoder().decode([Item].self, from: savedData)
    }
    self.tableView.reloadData()
}
```

---

## 🎵 Real-Time Analogy

| Action | Example | Meaning |
|---------|----------|----------|
| Encode | Music → CD | Convert data into storable form |
| Decode | CD → Music | Convert stored data back into usable form |

---

## 🌍 Real-Time Uses of Codable

- API communication (JSON request & response)
- Saving app data to **UserDefaults**
- Persisting objects in **files**
- Encoding/decoding settings or user info

---

## 💻 Practical Example

```swift
struct User: Codable {
    let name: String
    let age: Int
}

// Encode (Swift → JSON)
let user = User(name: "John", age: 25)
if let jsonData = try? JSONEncoder().encode(user) {
    print(String(data: jsonData, encoding: .utf8)!)
    // Output: {"name":"John","age":25}
}

// Decode (JSON → Swift)
let json = """ {"name":"John","age":25} """.data(using: .utf8)! 
if let decodedUser = try? JSONDecoder().decode(User.self, from: json) { print(decodedUser.name) // Output: John 
}
```

---
# 🧩 Clean Architecture, MVP, VIPER, and Singleton in Swift
---
## 🧱 What is Clean Architecture in Swift?
Clean Architecture in iOS is about **separating the app into layers** so that business logic is independent of the UI or frameworks.

### 📚 Main Layers:
- **Entities:** Core models or business objects.
- **Use Cases / Interactors:** Contain business rules or logic.
- **Presentation:** Handles UI and user interaction.
- **Infrastructure:** Manages external dependencies like networking or databases.

### ✅ Benefits:
- Modular and scalable code.
- Easier unit testing.
- Independent layers (changes in one layer don’t break others).
- Better maintainability and reusability.

---

## 🧩 MVP (Model-View-Presenter)

### Explanation:
- **Model:** Holds data or business logic.
- **View:** Displays the UI to the user.
- **Presenter:** Acts as a mediator between View and Model, handling presentation logic.

### Usage:
MVP separates **UI from logic**, making the app easier to test and maintain.

### Example:
```swift
protocol LoginView: AnyObject {
    func showLoginSuccess()
    func showLoginError()
}

class LoginPresenter {
    weak var view: LoginView?
    
    init(view: LoginView) {
        self.view = view
    }
    
    func login(username: String, password: String) {
        if username == "admin" && password == "1234" {
            view?.showLoginSuccess()
        } else {
            view?.showLoginError()
        }
    }
}
```
---

## 🧩 OOP & Architecture

### 4️⃣ Explain MVC, MVVM, VIPER patterns.
- **MVC:** Model-View-Controller (default in UIKit).
- **MVVM:** Separates UI from logic with `ViewModel`.
- **VIPER:** Modular and testable with clear separation of layers.

**MVVM Example:**
```swift
class UserViewModel: ObservableObject {
    @Published var username = ""
    func fetchUser() {
        username = "John Doe"
    }
}
```
---

## 🧠 VIPER (View-Interactor-Presenter-Entity-Router)

### Explanation:
VIPER architecture divides an app into five layers:
- **View:** Handles UI and user interactions.
- **Interactor:** Contains business logic and data handling.
- **Presenter:** Formats data for the view.
- **Entity:** Defines model objects.
- **Router (Wireframe):** Handles navigation between screens.

### Usage:
Used for **large-scale apps** to keep code **modular, testable, and organized**.

### Example Flow:
```
User Action → View → Presenter → Interactor → Entity → Presenter → View
```

---

## 🧩 Singleton in Swift
---

A **Singleton** provides a single, shared instance of a class throughout the app.

### When & Where to Use:
Use Singleton for shared resources like:
- Network Manager
- User Session
- App Settings
- Database Manager

### Example:
```swift
final class MyManager {
    static let shared = MyManager()  // single shared instance
    private init() {}               // prevents external instantiation
    
    func printMessage() {
        print("Singleton instance accessed!")
    }
}

// Usage
MyManager.shared.printMessage()
```

### 🔍 Explanation:
- `final` → prevents subclassing.
- `static` → creates a single shared instance.
- `private init()` → ensures no external instances are created.

---

## ✅ Advantages of Singleton:
- Single global access point.
- Saves memory by sharing one instance.
- Useful for shared resources (e.g., configuration, logging, caching).

## ⚠️ Disadvantages of Singleton:
- Can lead to **tight coupling**.
- Difficult to **mock in unit tests**.
- May introduce **hidden dependencies**.
- Harder to manage in **multi-threaded environments**.

---

### 🪄 Quick Summary
| Concept | Purpose | Key Benefit |
|----------|----------|--------------|
| **Clean Architecture** | Layered separation | Independent, testable code |
| **MVP** | UI logic separation | Easier testing |
| **VIPER** | Modular design | Best for large projects |
| **Singleton** | Shared instance | Centralized management |


---
## Networking & API Handling
---

### Example of REST API with async/await + Codable
```swift
class NetworkService {
    func fetchUsers() async throws -> [User] {
        let url = URL(string: "https://api.example.com/users")!
        let (data, _) = try await URLSession.shared.data(from: url)
        return try JSONDecoder().decode([User].self, from: data)
    }
}

struct User: Codable, Identifiable {
    let id: Int
    let name: String
}
```

✅ **Explanation:**
- `Codable` = `Decodable` + `Encodable`.
- `async/await` simplifies asynchronous code.

---
# 🧩 What is Delegates / Protocol, Closures, and Data Structures
---
## 1. Delegate / Protocol

**Description:**  
Delegate is used for one-to-one communication between objects, usually when you want to send data back from a child to a parent.

**Example:** Passing data from a `DetailViewController` back to `HomeViewController`

```swift
// Protocol
protocol DataDelegate: AnyObject {
    func didSendData(_ data: String)
}

// Detail View
class DetailViewController: UIViewController {
    weak var delegate: DataDelegate?    
    func sendDataBack() {
        delegate?.didSendData("Hello from Detail")
    }
}

// Home View
class HomeViewController: UIViewController, DataDelegate {
    func openDetail() {
        let detailVC = DetailViewController()
        detailVC.delegate = self
        present(detailVC, animated: true)
    }
    
    func didSendData(_ data: String) {
        print(data) // "Hello from Detail"
    }
}
```

**When to use:** One-to-one communication, especially between view controllers.

---

## 2. Closure (Callback)

**Description:**  
Closure is used for inline, lightweight callbacks, can be one-to-many or asynchronous, and doesn’t require a protocol.

**Example:**
```swift
// Detail View
class DetailViewController: UIViewController {
    var completion: ((String) -> Void)?
    
    func sendDataBack() {
        completion?("Hello via Closure")
    }
}

// Home View
class HomeViewController: UIViewController {
    func openDetail() {
        let detailVC = DetailViewController()
        detailVC.completion = { data in
            print(data) // "Hello via Closure"
        }
        present(detailVC, animated: true)
    }
}
```

**When to use:** One-time, inline, or asynchronous callbacks like API responses or button actions.  
**Delegate:** formal, reusable, one-to-one.  
**Closure:** lightweight, inline, great for async or temporary callbacks.

---

## 🧩 Difference between Swift, Objective-C, and SwiftUI

---

| Feature | Swift | Objective-C | SwiftUI |
|----------|--------|--------------|----------|
| Type | Modern programming language | C-based language | Declarative UI framework |
| Syntax | Concise, type-safe | Verbose, uses @ symbols | Declarative view syntax |

**Examples:**

**Swift**
```swift
let name = "John"
print("Hello \(name)")
```

**Objective-C**
```objc
NSString *name = @"John";
NSLog(@"Hello %@", name);
```

**SwiftUI**
```swift
Text("Hello John")
    .font(.title)
    .foregroundColor(.blue)
```

---

## 🧩  Difference between Weak, Atomic, Non-Atomic

---

### Weak - Avoid Retain Cycle
```objc
@property (nonatomic, weak) id<SomeDelegate> delegate;
```

### Atomic - Thread Safe
```objc
@property (atomic, strong) NSString *name;
```

### Nonatomic - Not Thread Safe (Faster)
```objc
@property (nonatomic, strong) NSString *name;
```

---

## 🧩 Explain Higher Order Functions

---

### Map
```swift
let numbers = [1, 2, 3, 4]
let squares = numbers.map { $0 * $0 }
print(squares)  // [1, 4, 9, 16]
```

### FlatMap
```swift
let nested = [[1, 2], [3, 4], [5, 6]]
let flattened = nested.flatMap { $0 }
print(flattened)  // [1, 2, 3, 4, 5, 6]
```

### Filter
```swift
let numbers = [1, 2, 3, 4, 5, 6]
let even = numbers.filter { $0 % 2 == 0 }
print(even)  // [2, 4, 6]
```

### Reduce
```swift
let total = [1, 2, 3, 4, 5].reduce(0) { $0 + $1 }
print(total)  // 15
```

### Split
```swift
let text = "apple,banana,grape"
let fruits = text.split(separator: ",")
print(fruits)  // ["apple", "banana", "grape"]
```

### Join
```swift
let words = ["Hello", "Swift", "World"]
let sentence = words.joined(separator: " ")
print(sentence)  // "Hello Swift World"
```

---

## 🧩 Collections in Swift

---

### Set
A Set is a collection of unique, unordered values.
Unlike arrays, a set does not allow duplicates.

It’s fast for checking if an element exists.

```swift
var fruits: Set<String> = ["Apple", "Banana", "Mango"]
fruits.insert("Orange")
print(fruits)

insert(_:) → add element
remove(_:) → remove element
contains(_:) → check if element exists
union(_:) → combine sets
intersection(_:) → common elements
```

### Array
```swift
var fruits = ["Apple", "Banana", "Mango"]
fruits.append("Orange")
let upperFruits = fruits.map { $0.uppercased() }
print(upperFruits)
```
Common array functions (higher-order):
append(_:) → add element
remove(at:) → remove element
map → transform elements
filter → filter elements
reduce → combine elements
forEach → iterate elements

### Dictionary
```swift
var studentScores = ["John": 90, "Emma": 85, "Liam": 95]
studentScores["Olivia"] = 88
studentScores.forEach { key, value in
    print("\(key): \(value)")
}
```
Common dictionary functions:
updateValue(_:forKey:) → add/update element
removeValue(forKey:) → remove element
keys → get all keys
values → get all values
forEach → iterate key-value pairs

studentScores["Olivia"] = 88            // add new
studentScores.removeValue(forKey: "John") // remove
studentScores.forEach { key, value in
    print("\(key): \(value)")
}

---

## SwiftUI Examples

**Array Example in SwiftUI**
```swift
struct ContentView: View {
    let fruits = ["Apple", "Banana", "Mango"]
    var body: some View {
        List(fruits, id: \.self) { fruit in
            Text(fruit)
        }
    }
}
```

**Dictionary Example in SwiftUI**
```swift
struct ContentView: View {
    let studentScores = ["John": 90, "Emma": 85, "Liam": 95]
    var body: some View {
        List(studentScores.keys.sorted(), id: \.self) { key in
            HStack {
                Text(key)
                Spacer()
                Text("\(studentScores[key]!)")
            }
        }
    }
}
```

---

✅ **Summary**
- **Delegates** = one-to-one reusable communication.
- **Closures** = inline callback for async or temporary use.
- **Higher Order Functions** = clean and functional data manipulation.
- **SwiftUI** = declarative way to design UI.
  


---
## Is Array a Value Type or Reference Type in Swift?
---

### Explanation:
- **Array is a Value Type** because it is implemented as a **struct**, and structs are value types in Swift.
- When you assign an array to another variable or pass it to a function, a **copy** is made.
- However, Swift uses **Copy-on-Write (CoW)** optimization, meaning an actual copy of the array’s elements occurs **only when one of the copies is modified.**

### ✅ Example:
```swift
var arr1 = [1, 2, 3]
var arr2 = arr1   // no copy yet, both share storage

arr2.append(4)    // now copy happens (copy-on-write)

print(arr1) // [1, 2, 3]
print(arr2) // [1, 2, 3, 4]
```

**Conclusion:**
- **Conceptually → Value Type**
- **Internally Optimized → Uses reference under the hood until mutation (Copy-on-Write)**


---

## Xcode Overview and Key Components

---

### 🧭 Navigator (Bundle)
- Manages project files: `.swift`, `.storyboard`, `.xcassets`, `AppDelegate`, `SceneDelegate`, etc.
- Access to file hierarchy and resources.

### ✏️ Editor
- Central area for writing code (`Storyboard` for UI, `Canvas` for SwiftUI views).
- Supports code completion, syntax highlighting, and interface editing.

### 🔍 Inspector
- Right-hand panel to view or edit properties of UI elements.
- Used for setting **constraints**, **connections (IBOutlets/IBActions)**, and **attributes**.

### 🧰 Toolbar
- Located on top of Xcode.
- Contains controls for **running the app**, **choosing a simulator or device**, and **checking build status**.

### 🐞 Debug Area
- Shows runtime logs, console outputs, and variable inspection.
- Helps identify **breakpoints** and **crash reports**.

### ⚙️ Instruments
- Used for performance profiling (CPU, memory, and energy usage).

### 🌐 Source Control Integration
- Direct integration with **GitHub** and other version control systems.
- Allows commits, branches, and merges directly in Xcode.

### 🧱 Reality Composer
- Used for designing **AR (Augmented Reality)** and **3D content** experiences.


---
## Key Features of Swift Language
---

| Feature | Description |
|----------|--------------|
| **Single-line comment** | `// This is a comment` |
| **Multi-line comment** | `/* Comment */` |
| **Auto-completion** | Xcode suggests code completions as you type |
| **Image literal** | Quickly insert images from the asset catalog |
| **Print statement** | Displays output in console: `print("Hello")` |
| **String interpolation** | Embed variables: `print("Value: \(x)")` |
| **Playground feature** | Allows real-time code execution for testing logic |
| **Auto-fix errors** | Xcode suggests quick fixes for syntax or type errors |

---
## Exception Handling and Debugging in Swift
---

### Common Errors:
- **Array index out of bounds**
- **Signal SIGABRT**
- **Not Key Value Coding Compliant (KVC)**

### Steps to Handle Exceptions:
1. Read the **debug message** carefully.
2. Identify the **reason for crash** from the console.
3. Search online for specific **error resolution**.
4. **Right-click** the view in the storyboard to check **missing connections**.
5. **Recreate outlets or actions** if a broken connection is found.

### Example:
```swift
let array = [1, 2, 3]
print(array[5]) // ❌ Index out of range error
```

To fix, always check the index validity before access.

---
## Additional Swift Concepts
---

### 🧩 Array
- A **collection of elements of the same type.**
```swift
var numbers = [1, 2, 3]
numbers.append(4)
print(numbers) // [1, 2, 3, 4]
```

### ♻️ Code Refactoring
- The process of **improving code structure without changing its behavior.**
- **Goal:** Make code reusable, readable, and maintainable.

---

📘 **Summary**
- Arrays in Swift are **value types** with **copy-on-write** optimization.
- Xcode provides an integrated environment for **coding, debugging, and UI design**.
- Swift is a **modern, safe, and concise language** ideal for iOS development.
- Debugging and refactoring improve **app stability** and **code quality**.


---
## Key Features of Swift vs Objective-C
---
**Swift** offers several advantages over Objective-C:

- ✅ Type safety – prevents type-related runtime errors.  
- ✅ Optionals eliminate null pointer exceptions (common crash cause).  
- ✅ Concise and readable syntax – faster development.  
- ✅ Automatic memory management via ARC.  
- ✅ Protocol-oriented programming – promotes modular, reusable code.  
- ✅ Value types (structs, enums) – enhance performance.  
- ✅ Playground support for live code testing.  
- ✅ Bridging with Objective-C for mixed projects.  

---
## Optionals in Swift
---

**Definition:** Optionals represent a variable that may or may not hold a value.  
They help avoid null pointer exceptions by requiring safe unwrapping.

```swift
var name: String? = "John"
if let unwrapped = name {
    print(unwrapped) // Safely prints the value
} else {
    print("Value is nil")
}
```

---
## iOS App Lifecycle & ViewController Lifecycle
---

### App Lifecycle States:
- **Not Running** → App is closed.
- **Inactive** → App is in foreground but not receiving input.
- **Active** → App is running and receiving input.
- **Background** → App is executing code in the background.
- **Suspended** → App is in memory but not executing code.

### Common Delegate Methods:
- `application:didFinishLaunchingWithOptions` → App launch setup  
- `applicationDidBecomeActive` → App becomes active  
- `applicationWillEnterForeground` → Coming from background  

### ViewController Lifecycle:
`loadView` → `viewDidLoad` → `viewWillAppear` → `viewDidAppear` → `viewWillDisappear` → `viewDidDisappear` → `viewDidUnload`

---
## Passing Data Between ViewControllers
---

- **Segues (prepareForSegue)** → Forward data during navigation  
- **Delegation pattern** → Backward communication using protocols  
- **Notifications** → One-to-many communication  
- **Singleton / Shared instances** → Global data access  
- **Closures / Completion handlers** → Inline callback approach  
- **UserDefaults** → For persistent, simple key-value data  
- **Core Data / SQLite** → For complex persistent data  

---
## Loose vs Tight Coupling in iOS
---

### Tight Coupling Example
When classes are directly dependent on each other:

```swift
class Engine {
    func start() {
        print("Engine started")
    }
}

class Car {
    let engine = Engine()
    func start() {
        engine.start()
        print("Car started")
    }
}
```

**Advantages:**
- Simple structure  
- Good for small projects  

**Disadvantages:**
- Hard to scale or test  
- Limited reusability  
- Changes propagate through multiple classes  

---

### Loose Coupling Example
Using abstraction or protocols to reduce dependencies:

```swift
protocol Engine {
    func start()
}

class DieselEngine: Engine {
    func start() {
        print("Diesel engine started")
    }
}

class Car {
    var engine: Engine
    init(engine: Engine) {
        self.engine = engine
    }
    func start() {
        engine.start()
        print("Car started")
    }
}

let dieselEngine = DieselEngine()
let car = Car(engine: dieselEngine)
car.start()
```

**Advantages:**
- High flexibility and scalability  
- Easier to test and maintain  

**Disadvantages:**
- Slightly more complex design  
- Minor performance overhead  

---
## Frame vs Bounds
---

| Property | Description |
|-----------|--------------|
| **Frame** | Defines the view’s position and size relative to its **superview**. |
| **Bounds** | Defines the view’s own coordinate system (content area). |

**Example:**
```swift
print(view.frame)   // position in superview
print(view.bounds)  // internal coordinate system
```

---
## @objc vs @nonobjc
---

- **@objc** → Makes Swift methods/classes visible to Objective-C.  
  Useful for interoperability and Core Data integration.  

- **@nonobjc** → Prevents exposure to Objective-C runtime.  
  Avoids naming conflicts and keeps Swift-only functions isolated.

```swift
@objc class MyObjCClass: NSObject {
    @objc func accessibleInObjC() {}
    @nonobjc func swiftOnlyMethod() {}
}
```

---

### ✅ Summary

| Concept | Key Point |
|----------|------------|
| **Swift** | Type-safe, modern, performant language |
| **Optionals** | Prevent null pointer crashes |
| **Lifecycle** | App + ViewController stages |
| **Data Transfer** | Delegation, closures, segues, etc. |
| **Coupling** | Loose = flexible, Tight = simple |
| **Frame vs Bounds** | External vs Internal coordinates |
| **@objc/@nonobjc** | Swift-Objective-C interoperability control |


---
## 🧩 Content Hugging and Compression Resistance Priority
---

| Priority Level | Value  |
|----------------|---------|
| Low            | 251     |
| Medium         | 750     |
| High           | 1000    |

**Content Hugging Priority**: Content does **not want to grow**.  
If you have 2 labels and you do not want the 1st label to grow, give its horizontal content hugging priority to `252`.  
Here label 1 will not grow but label 2 will grow.

<img width="370" height="106" alt="Screenshot 2025-10-28 at 10 59 59 PM" src="https://github.com/user-attachments/assets/7bd5a8ee-337b-4529-91be-d73921fc4d73" />


**Content Compression Resistance Priority**: Content does **not want to shrink**.  
If the 1st label in a stack view should not shrink, give its horizontal compression resistance priority to `1000`.

<img width="393" height="90" alt="Screenshot 2025-10-28 at 11 00 11 PM" src="https://github.com/user-attachments/assets/aa32c836-317b-4056-9855-851f3ee0a2de" />

---
## 🧩 Escaping vs Non-Escaping Closures in Swift
---

### Non-Escaping Closure
A **non-escaping** closure is executed **within the function** before it returns.

```swift
func fetchData(completion: () -> Void) {
    print("Fetching data...")
    completion()
}

fetchData {
    print("Data fetched!")
}
```

**Output:**
```
Fetching data...
Data fetched!
```

✅ **Advantages:**
- Simpler and faster (no need to store the closure).
- Safe — the closure cannot outlive the function’s scope.

❌ **Disadvantages:**
- Can’t be used for asynchronous operations (e.g., network calls).

---

### Escaping Closure
An **escaping** closure is called **after the function has returned**, usually for async tasks (like API calls).

```swift
func downloadFile(completion: @escaping () -> Void) {
    print("Start downloading...")
    DispatchQueue.global().asyncAfter(deadline: .now() + 2) {
        completion()
    }
}

downloadFile {
    print("Download finished!")
}
```

**Output:**
```
Start downloading...
Download finished!
```

✅ **Advantages:**
- Perfect for async operations (API calls, timers).
- Allows callbacks after long-running tasks complete.

❌ **Disadvantages:**
- Requires careful memory management (retain cycles).
- Slightly slower due to escaping behavior.

**Example with Retain Cycle:**

```swift
class Downloader {
    var completion: (() -> Void)?

    func startDownload() {
        downloadFile {
            print("Download complete")
        }
    }

    func downloadFile(completion: @escaping () -> Void) {
        self.completion = completion
    }

    deinit {
        print("Downloader deinitialized")
    }
}

var downloader: Downloader? = Downloader()
downloader?.startDownload()
downloader = nil  // ❌ Retain cycle, deinit not called
```

Use `[weak self]` or `[unowned self]` in the capture list to prevent leaks.

---
## 🧩 GCD (Grand Central Dispatch) – Quality of Service (QoS)
---

GCD defines several **QoS classes** to prioritize tasks:

| QoS Class         | Description | Priority |
|-------------------|--------------|-----------|
| User-interactive  | UI updates, animations | Highest |
| User-initiated    | Tasks initiated by user | High |
| Default           | Standard priority | Medium |
| Utility           | Long-running, less urgent | Low |
| Background        | Sync, cache, prefetch | Lowest |

### Example Usage

**User-Initiated:**
```swift
DispatchQueue.global(qos: .userInitiated).async {
    let data = fetchData()
    DispatchQueue.main.async {
        self.updateUI(with: data)
    }
}
```

**Utility:**
```swift
DispatchQueue.global(qos: .utility).async {
    let fileData = downloadLargeFile()
    DispatchQueue.main.async {
        self.showDownloadComplete()
    }
}
```

**User-Interactive:**
```swift
DispatchQueue.global(qos: .userInteractive).async {
    performQuickTask()
}
```

---

# Swift Concurrency & Task Management

## 🧩 Difference Between Dispatch Queue and Operation Queue

### **Dispatch Queue (GCD)**
- Part of Grand Central Dispatch (GCD) → lightweight C-level API.
- Executes tasks FIFO either serially or concurrently.
- Very fast, minimal overhead, good for simple async tasks.

**Example:**
```swift
DispatchQueue.global().async {
    // background work
}
```

### **Operation Queue**
- Built on top of GCD but more high-level, object-oriented (Operation class).
- Supports dependencies, priorities, cancellation, and KVO.
- More flexible for complex task management.

**Example:**
```swift
let queue = OperationQueue()
queue.addOperation {
    // background work
}
```

### **Summary**
> "Dispatch Queue (GCD) is low-level, lightweight, and good for simple async tasks. Operation Queue is higher-level, supports dependencies, priorities, and cancellation—better for complex task management."

### **When to Use Each**
- **GCD:** Simple async or background tasks.
- **OperationQueue:** Complex operations with dependencies, cancellations, or priorities (e.g., image downloading + filtering).

---

## Example: Photo Gallery App

**Scenario:** Each image must be downloaded, then filtered, then displayed.

### **Why OperationQueue is Better**
✅ Supports chaining with dependencies (filter runs after download)  
✅ Operations are cancellable if user scrolls away  
✅ Easier to observe completion

**Code Example:**
```swift
import Foundation
import UIKit

// Operation to download image
class DownloadImageOperation: Operation {
    let url: URL
    var downloadedImage: UIImage?
    
    init(url: URL) {
        self.url = url
    }
    
    override func main() {
        if isCancelled { return }
        if let data = try? Data(contentsOf: url) {
            if isCancelled { return }
            downloadedImage = UIImage(data: data)
        }
    }
}

// Operation to apply filter
class FilterImageOperation: Operation {
    var inputImage: UIImage?
    var filteredImage: UIImage?
    
    override func main() {
        if isCancelled { return }
        guard let image = inputImage else { return }
        // Fake grayscale filter
        filteredImage = image.withRenderingMode(.alwaysTemplate)
    }
}

// Usage
let queue = OperationQueue()
let downloadOp = DownloadImageOperation(url: URL(string: "https://picsum.photos/200")!)
let filterOp = FilterImageOperation()

// Dependency: filter runs AFTER download
filterOp.addDependency(downloadOp)
queue.addOperations([downloadOp, filterOp], waitUntilFinished: false)

filterOp.completionBlock = {
    if let result = filterOp.filteredImage {
        print("Filtered image ready: \(result)")
    }
}
```

---

## 🧩 Explain GCD - Quality of Service (QoS) Classes

**QoS Classes:**
- `User-interactive` → Highest priority
- `User-initiated`
- `Default`
- `Utility`
- `Background` → Lowest priority

**User-interactive:** Immediate UI updates  
**User-initiated:** Tasks initiated by user that should finish quickly  
**Utility:** Long-running background tasks (e.g., downloading files)  
**Background:** Tasks not time-sensitive (e.g., syncing data)

**Example:**
```swift
DispatchQueue.global(qos: .userInitiated).async {
    let data = fetchData()
    DispatchQueue.main.async {
        self.updateUI(with: data)
    }
}
```

### **QoS Task Priorities**
| QoS | Priority | Example Use Case |
|------|-----------|-----------------|
| User-interactive | Highest | Animation, UI updates |
| User-initiated | High | Fetching user data |
| Default | Normal | Standard tasks |
| Utility | Low | Downloading large files |
| Background | Lowest | Cache sync, maintenance |

---

## 🧩 Have you a Google API Experience?

**Yes**, I have worked with Google APIs in both **iOS** and **Flutter** projects.

### **In iOS (Swift):**
- Used **Google Maps**, **Firebase**, and **Google Sign-In**.
- Integrated SDKs and handled REST APIs using **URLSession** or **Alamofire**.

### **In Flutter:**
- Used packages from **pub.dev** like:
  - `google_maps_flutter`
  - `firebase_auth`
  - `google_sign_in`

### **Key Points:**
- Manage authentication and authorization using **OAuth 2.0** or API keys.
- Parse JSON responses safely using `Codable`.
- Maintain modular service layers for cleaner code.

---

✅ **Summary**
| Feature | Dispatch Queue (GCD) | Operation Queue |
|----------|----------------------|-----------------|
| Level | Low-level | High-level |
| Dependencies | ❌ No | ✅ Yes |
| Cancellation | ❌ No | ✅ Yes |
| KVO Support | ❌ No | ✅ Yes |
| Performance | ✅ Fast | ⚡ Slightly higher overhead |
| Best for | Simple async tasks | Complex task dependencies |

---
# 🧩 Swift Memory Management: Weak vs Strong References

## 🔍 Why Weak is Slower than Strong
When you declare a property as **weak**, Swift (and the underlying Objective-C runtime) must do extra bookkeeping to manage memory safely, which makes it slightly slower than a **strong** reference.

### 📘 How it Works
- Swift keeps a **list of all weak references** to an object.
- When the object is deallocated, Swift **walks through that list** and **sets all weak references to `nil`** to prevent dangling pointers.

### ⚙️ Strong Reference
- Just increments/decrements the retain count of an object.
- Very fast — simple retain/release operations.

---

## ⚡ Performance Comparison

| Reference Type | Retain Count | Speed | Notes |
|-----------------|--------------|--------|--------|
| **Strong** | Increases | ⚡ Fast | Simple retain/release |
| **Weak** | Doesn’t increase | 🐢 Slower | Tracks and nils references |
| **Unowned** | Doesn’t increase | ⚡ Fast | No tracking, but unsafe if accessed after deallocation |

---

## 🧠 Mutability in Swift

| Type | Keyword | Can Modify? | Example |
|------|----------|-------------|----------|
| Immutable | `let` | ❌ No | `let x = 10` |
| Mutable | `var` | ✅ Yes | `var x = 10; x = 20` |

---

### ✅ Summary
- Use **strong** for normal ownership.
- Use **weak** to avoid retain cycles (e.g., delegates).
- Use **unowned** when you know the reference will never become `nil`.


# 🧩 Testing Questions in Swift (XCTest)

## 1. What is unit testing, and why is it important?
Unit testing is a software testing method where individual components or functions are tested in isolation.  
It ensures code correctness, simplifies debugging, and enhances code quality.

---

## 2. How do you set up a unit test target in Xcode?
1. Open your Xcode project.  
2. Select **File > New > Target**.  
3. Choose **Unit Testing Bundle**.  
4. Name the target and click **Finish**.

---

## 3. What is XCTest?
**XCTest** is Apple's testing framework used for writing unit tests and UI tests for iOS, macOS, watchOS, and tvOS applications.

---

## 4. How do you create a test case class using XCTest?
```swift
import XCTest

class MyTests: XCTestCase {
    func testExample() {
        let result = 2 + 2
        XCTAssertEqual(result, 4, "The result should be 4")
    }
}
```

---

## 5. Explain the lifecycle of a test method in XCTest
- `setUp()` : Called before each test method.  
- `tearDown()` : Called after each test method.  
- `setUpWithError()` & `tearDownWithError()` : Handle throwing errors if needed.

---

## 6. What are assertions in unit testing? Provide examples.
```swift
XCTAssert(true) // General assertion
XCTAssertEqual(2 + 2, 4) // Equality check
XCTAssertNotEqual(2 + 2, 5) // Inequality check
XCTAssertNil(nil) // Nil check
XCTAssertNotNil("Hello") // Non-nil check
```

---

## 7. How do you write asynchronous test cases using XCTest?
```swift
func testAsyncCall() {
    let expectation = self.expectation(description: "Async Call")
    
    fetchData { data in
        XCTAssertNotNil(data)
        expectation.fulfill()
    }
    
    waitForExpectations(timeout: 5, handler: nil)
}
```
**Explanation:** XCTest expectations let your test pause until asynchronous code finishes, ensuring you can assert results after async completion.

---

## 8. What are test doubles? Explain mocks, stubs, and fakes.

# 🧩 Test Doubles in Unit Testing (Simplified Guide)

**Test doubles** are objects that stand in for real objects in tests.  
They simulate or control behavior in a predictable way.

When testing, sometimes you don’t want to use real services (like APIs, databases, or payment systems).  
So you make **“fake” versions** of them — these are called **Test Doubles**.

There are 3 main types: **Stub, Mock, and Fake**

---

## 🧠 1. Stub – Returns a fixed (predefined) value

A **Stub** just gives fake data when your code asks for it.

### Example – Weather App

```swift
// Real service
class WeatherService {
    func getWeather(for city: String) -> String {
        // Normally it would call a real API
        return "Fetching from server..."
    }
}

// Stub version
class WeatherServiceStub: WeatherService {
    override func getWeather(for city: String) -> String {
        return "Sunny 25°C" // Always returns same fake data
    }
}

// Test
func testWeatherDisplay() {
    let stub = WeatherServiceStub()
    let weather = stub.getWeather(for: "Mumbai")
    XCTAssertEqual(weather, "Sunny 25°C")
}
```

✅ *Stub gives predictable fake output.*

---

## 🧠 2. Mock – Checks if something happened (verifies behavior)

A **Mock** doesn’t give fake data — it records whether a function was called or not.

### Example – Payment Service

```swift
// Real service
class PaymentService {
    func sendReceipt() { /* Real email sending logic */ }
}

// Mock version
class MockPaymentService: PaymentService {
    var didSendReceipt = false

    override func sendReceipt() {
        didSendReceipt = true // Mark that function was called
    }
}

// Test
func testSendReceiptCalled() {
    let mock = MockPaymentService()
    mock.sendReceipt()

    XCTAssertTrue(mock.didSendReceipt, "Receipt should be sent")
}
```

✅ *Mock helps you confirm that a method was called.*

---

## 🧠 3. Fake – Works like real logic but simpler (usually in-memory)

A **Fake** is a simple working version of a real component.  
It actually stores or retrieves data — but only in memory (not a real database).

### Example – Fake Database

```swift
// Real Database
class Database {
    func save(key: String, value: String) { /* writes to disk */ }
    func get(key: String) -> String? { nil }
}

// Fake version
class FakeDatabase: Database {
    private var storage = [String: String]()
    
    override func save(key: String, value: String) {
        storage[key] = value
    }

    override func get(key: String) -> String? {
        return storage[key]
    }
}

// Test
func testFakeDatabaseStoresData() {
    let db = FakeDatabase()
    db.save(key: "user", value: "Nand")
    XCTAssertEqual(db.get(key: "user"), "Nand")
}
```

✅ *Fake behaves like a real database — but runs fast and doesn’t need setup.*

<img width="1536" height="1024" alt="Image" src="https://github.com/user-attachments/assets/f5a91353-71ad-4bf4-8d49-b3b45270df78" />


---

## 🧩 Summary Table

| Type | Purpose | Example |
|------|----------|----------|
| **Stub** | Returns fixed values | Weather always “Sunny” |
| **Mock** | Checks if method called | `sendReceipt()` called |
| **Fake** | Simple working logic | In-memory database |

---

🧠 **Tip:**  
In real projects, test doubles help you isolate your logic and test it **without depending on APIs or databases**.

---
## 9. What is TDD, and what are its benefits?
---

**TDD (Test-Driven Development)** involves writing tests before writing the actual implementation code.  

**Cycle:** Red → Green → Refactor

### Example:
```swift
class CalculatorTests: XCTestCase {
    func testAddition() {
        let result = Calculator.add(2, 3)
        XCTAssertEqual(result, 5)
    }
}

struct Calculator {
    static func add(_ a: Int, _ b: Int) -> Int {
        return a + b
    }
}
```

**Benefits:**
- Reduces bugs  
- Improves design  
- Provides better test coverage  

---

## 10. What is code coverage, and how do you enable it in Xcode?
Code coverage measures how much of the source code is tested by unit tests.

1. Open Xcode.  
2. Go to **Product > Scheme > Edit Scheme...**  
3. Select **Test** and check **Gather Coverage for Targets**.

---

## 11. How do you interpret code coverage reports?
- **Green** → Fully tested code  
- **Red** → Untested code  

---

## 12. What are best practices for writing unit tests?
✅ Write independent tests  
✅ Use meaningful method names  
✅ Test one thing per method  
✅ Use test doubles when needed  
✅ Keep tests readable and maintainable  

---

## 13. How can you prevent unreliable tests?
- Avoid external dependencies  
- Use mock services  
- Ensure consistent test data  

---

## 14. How do you test Core Data models?
Test Core Data models by using an **in-memory persistent container** and validating CRUD operations.
![IMG_8633](https://github.com/user-attachments/assets/d5cf7b3a-c554-4966-b175-16a49f8e1d9d)

---

## 15. How do you test ViewModels in MVVM?
```swift
class ViewModelTests: XCTestCase {
    func testViewModelFetch() {
        let viewModel = MyViewModel(service: MockService())
        viewModel.fetchData()
        XCTAssertEqual(viewModel.data.count, 3)
    }
}
```

## 16. When should you use **strong**, **weak**, and **unowned**?

### 🟢 Strong
- **Default type of reference.**
- Used when one object *owns* another (like a parent owning a child).
- Keeps the object alive as long as the reference exists.

**Example:**
```swift
class ViewController: UIViewController {
    var label = UILabel() // Strong reference
}
```
A `ViewController` strongly owns its `UILabel`.

---

### 🟡 Weak
- Used when two objects can exist independently.
- Doesn’t keep the object alive → it’s automatically set to `nil` when the object is deallocated.
- Always declared as `weak var`.

**Example:**
```swift
protocol MyDelegate: AnyObject {
    func didTapButton()
}

class MyViewController {
    weak var delegate: MyDelegate? // Weak reference to avoid retain cycle
}
```

A delegate reference is usually **weak** to avoid retain cycles.

---

### 🔴 Unowned
- Used when one object depends on another and they have the same lifetime.
- Doesn’t keep the object alive, **but won’t become nil automatically** → leads to a crash if accessed after deallocation.

**Example:**
```swift
class Customer {
    var creditCard: CreditCard?
}

class CreditCard {
    unowned let customer: Customer // Unowned reference
    init(customer: Customer) {
        self.customer = customer
    }
}
```

A `CreditCard` has an **unowned** reference to its `Customer`.

---

### 📋 Summary Table

| Reference | Keeps object alive? | Becomes nil automatically? | Use when… |
|------------|---------------------|-----------------------------|------------|
| **strong** | ✅ Yes | ❌ No | Object owns another |
| **weak**   | ❌ No | ✅ Yes | Objects can exist separately |
| **unowned**| ❌ No | ❌ No | Objects have same lifetime |

---

✅ **Tips:**
- Use **strong** by default.
- Use **weak** for delegates or references that can become `nil`.
- Use **unowned** when two objects depend on each other and live the same duration.


![Strong, Weak, and Unowned References](https://github.com/user-attachments/assets/26d66b71-01dc-420c-9026-de3aaa3c0db1)

---
### What is ARC and how does it work?
---

ARC (Automatic Reference Counting) manages memory automatically.  
It increases retain count when an object is referenced and decreases when released.

- **Strong** → Keeps object alive.  
- **Weak** → Doesn’t retain, becomes nil when deallocated.  
- **Unowned** → Doesn’t retain, but unsafe (crashes if accessed after deallocation).

---
SOLID Principle - Single Responsibility Principle (SRP)
---
**S**ingle Responsibility – Class does one job.  
**O**pen/Closed – Open for extension, closed for modification.  
**L**iskov Substitution – Subtypes must behave like base types.  
**I**nterface Segregation – Don’t force classes to implement unused interfaces.  
**D**ependency Inversion – Depend on abstractions, not concretions.

---
** Single Responsibility Principle (SRP)** 
---
The Single Responsibility Principle states that every class should have only one reason to change, meaning it should have only one job or responsibility. This helps in maintaining clean, modular, and easily testable code.

Key Points:
- Segregation of duties ensures that changes in one module do not affect another module.

- Every class should have only one responsibility.

```
Bad Example:
In this example, the `InvoiceManager` class is handling multiple responsibilities — calculating invoices, managing database operations, and sending communications.

class InvoiceManager {
   func calculateInvoice() { }
   func manageDB() { }
   func sendEmail() { }
}
```
```
Good Example:
Here, each feature is separated into its own class — one class for calculation, one for database management, and another for communication. This aligns with the Single Responsibility Principle.

class CalculateInvoice {
   func calculate() { }
}

class DBManager {
   func insert() { }
   func update() { }
}

class Communication {
   func sendEmail() { }
}
```
---
** Open Closed Principle (SRP) **
---
■ Open Closed Principle

Definition:
A class should be open for extension but closed for modification.
You should be able to add new functionality without changing existing code.

■ Explanation:
This principle promotes flexibility and reduces the risk of introducing bugs
when adding new features. Instead of modifying existing code,
you extend it using inheritance or protocols.
---
```
■ Bad Example (Violates Open Closed Principle):
class PaymentProcessor {
    func pay(by type: String) {
        if type == "creditCard" {
            // process credit card payment
        } else if type == "paypal" {
            // process PayPal payment
        }
    }
}
Problem:
- Every time a new payment method is added, you must modify this class.
- This increases the risk of breaking existing functionality.
---
■ Good Example (Follows Open Closed Principle):
protocol Payment {
    func pay()
}

class CreditCardPayment: Payment {
    func pay() {
        print("Paid using Credit Card")
        }
}

class PayPalPayment: Payment {
    func pay() {
    print("Paid using PayPal")
    }
}
Now, the PaymentProcessor can accept any Payment type without modification.

class PaymentProcessor {
    func process(payment: Payment) {
    payment.pay()
    }
}
```
---
** Liskov Substitution Principle **
---

Definition:
The Liskov Substitution Principle states that objects of a superclass should be
replaceable with objects of a subclass without breaking the program.

Explanation:
If class B is a subclass of class A, then we should be able to replace A with B
without affecting the behavior of the program.

■ Bad Example
```
class Bird {
    func fly() { }
    }

class Penguin: Bird {
    override func fly() {
    // Penguins can’t fly - leads to runtime error!
    fatalError("Penguins can't fly!")
    }
}

func makeBirdFly(_ bird: Bird) {
    bird.fly()
}

let sparrow = Bird()
makeBirdFly(sparrow) // ■ Works
let penguin = Penguin()
makeBirdFly(penguin) // ■ Breaks LSP
```
---
■ Good Example

```
class Bird {
    func makeSound() { }
}

protocol Flyable {
    func fly()
}

class Sparrow: Bird, Flyable {
    func fly() { print("Sparrow flying") }
}

class Penguin: Bird {
    func swim(){}
}

func letBirdFly(bird : flyable){
    bird:fly()
}

correct  : letBirdFly(bird:Sparrow)
Wrong    : letBirdFly(bird:Penguin)
```
---
** Interface Segregation Principle (ISP) **
---

The Interface Segregation Principle (ISP) states that:
"A class should not be forced to implement interfaces it does not use."
Meaning: An interface should have only the methods that are relevant to a specific class.
If a class doesn’t need a certain behavior, that method should not be in its interface.
In Swift, this can be implemented using multiple small protocols instead of one large protocol.

■ Bad Example
```
protocol Worker {
    func work()
    func eat()
}

class Human: Worker {
    func work() { print("Human working...") }
    func eat() { print("Human eating...") }
}

class Robot: Worker {
    func work() { print("Robot working...") }
    func eat() { /* ■ Robot does not need to eat */ }
}
```
■ Good Example

```
protocol Workable { func work() }
protocol Eatable { func eat() }

class Human: Workable, Eatable {
    func work() { print("Human working...") }
    func eat() { print("Human eating...") }
}

class Robot: Workable {
    func work() { print("Robot working...") }
}
```

■ Summary:

■ Split large protocols into smaller, more specific ones.
■ Classes should implement only what they actually need.

---
** Dependency Inversion Principle **
---

The Dependency Inversion Principle (DIP) states that high-level modules should not depend on low-level modules. 
Both should depend on abstractions. This helps reduce tight coupling between components, improving flexibility and testability.

Key points:

• • High-level modules should not depend on low-level modules.
• • Both should depend on abstractions (e.g., protocols or interfaces).
• • Abstractions should not depend on details. Details should depend on abstractions.

❌ Bad Example
In the following example, the OrderService class depends directly on the EmailNotifier class. 
If we want to switch to an SMSNotifier, we must modify the OrderService class — which violates DIP.

```
class EmailNotifier {
   func sendEmail() { }
}

class OrderService {
   let notifier = EmailNotifier()
   func placeOrder() {
       notifier.sendEmail()
   }
}
```

✅ Good Example
In this improved version, we introduce a Notifier protocol. Both EmailNotifier and SMSNotifier conform to this protocol. OrderService now depends on the abstraction (Notifier) rather than a concrete class.

```
protocol Notifier {
   func sendNotification()
}

class EmailNotifier: Notifier {
   func sendNotification() { }
}

class SMSNotifier: Notifier {
   func sendNotification() { }
}

class OrderService {
   let notifier: Notifier
   init(notifier: Notifier) {
       self.notifier = notifier
   }
   func placeOrder() {
       notifier.sendNotification()
   }
}

let email = OrderService(notifier: EmailNotifier())
email.placeOrder()

// Same for SMS service
let sms = OrderService(notifier: SMSNotifier())
sms.placeOrder()
```

Now, OrderService does not depend on any specific notification implementation. 
We can add new notifiers (like PushNotifier) without modifying existing code, adhering to the Open/Closed and Dependency Inversion principles.

---
## 🧩 Advanced Topics
---
### Explain Combine framework
Combine is Apple’s reactive programming framework.
---

```swift
import Combine

class ViewModel: ObservableObject {
    @Published var text = ""
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        $text.sink { print("Text changed: \($0)") }.store(in: &cancellables)
    }
}
```

---

### Explain Dependency Injection
Passing dependencies instead of creating them inside a class.

```swift
class APIService {}
class ViewModel {
    private let service: APIService
    init(service: APIService) {
        self.service = service
    }
}
```

---

## iOS App Lifecycle

### Describe App Lifecycle methods
- `application(_:didFinishLaunchingWithOptions:)`
- `sceneWillEnterForeground`
- `sceneDidEnterBackground`

---

## Performance Optimization

- Use Instruments for memory leaks.  
- Profile with Time Profiler.  
- Avoid force unwrapping.  
- Use lazy loading for heavy objects.


# 📘 Combine Framework --- Interview Ready Guide

## What is Combine?

Combine is Apple's reactive programming framework for handling
asynchronous events and data streams. It provides **Publishers**,
**Subscribers**, and **Operators**. Think of it as a modern alternative
to Delegates, Closures, and NotificationCenter.


## Basic Terms

  **Publisher**         Emits a sequence of values over time (e.g., API
                        responses, text field updates).

  **Subscriber**        Receives and reacts to those emitted values.

  **Operator**          Modifies values between publisher and subscriber
                        (e.g., `map`, `filter`, `debounce`).

  **Cancellable**       A token to stop a subscription when you no longer
                        need updates.

## Basic Example --- Just Publisher

``` swift
import Combine

let publisher = Just("Hello Combine")
let subscriber = publisher.sink { value in
    print("Received:", value)
}
```

**Explanation:** - `Just` is a simple publisher that emits one value and
finishes. - `sink` is a subscriber that receives that value. - Output:
`Received: Hello Combine`

------------------------------------------------------------------------

## Combine with Multiple Operators

``` swift
import Combine

let numbers = [1, 2, 3, 4, 5]
let publisher = numbers.publisher

let subscriber = publisher
    .filter { $0 % 2 == 0 }   // Take only even numbers
    .map { $0 * 10 }          // Multiply each by 10
    .sink { value in
        print("Received:", value)
    }
```

**Explanation:** - The array becomes a publisher. - `filter` passes only
even numbers. - `map` modifies them. - Output:
`Received: 20   Received: 40`

------------------------------------------------------------------------

## Combine with URLSession (Most Common Interview Example)

``` swift
import Combine
import Foundation

struct User: Codable {
    let id: Int
    let name: String
}

class NetworkManager {
    var cancellables = Set<AnyCancellable>()
    
    func fetchUsers() {
        guard let url = URL(string: "https://jsonplaceholder.typicode.com/users") else { return }
        
        URLSession.shared.dataTaskPublisher(for: url)
            .map { $0.data }
            .decode(type: [User].self, decoder: JSONDecoder())
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { completion in
                    switch completion {
                    case .finished:
                        print("✅ Finished fetching users")
                    case .failure(let error):
                        print("❌ Error:", error)
                    }
                },
                receiveValue: { users in
                    print("👤 Users:", users)
                }
            )
            .store(in: &cancellables)
    }
}
```

**Explanation:** - `dataTaskPublisher` publishes (data, response) from a
network call. - `.map` extracts the data. - `.decode` converts JSON to
`[User]`. - `.receive(on:)` ensures UI updates happen on the main
thread. - `.sink` handles completion and values. - `.store(in:)` keeps
the subscription alive until you cancel or the object deallocates.

✅ **Interview Tip:** You can explain that Combine simplifies async data
handling and replaces old patterns like Delegates and Closures.

------------------------------------------------------------------------

## Combine with @Published and ViewModel (MVVM Integration)

``` swift
import Combine
import Foundation

class CounterViewModel: ObservableObject {
    @Published var count = 0
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        $count
            .filter { $0 % 2 == 0 }
            .sink { value in
                print("Even count:", value)
            }
            .store(in: &cancellables)
    }
    
    func increment() {
        count += 1
    }
}
```

**Explanation:** - `@Published` automatically creates a publisher for
the property. - `$count` allows Combine to observe changes. - `.sink`
reacts whenever `count` changes. - This pattern is heavily used in
**SwiftUI MVVM**.

------------------------------------------------------------------------

## Important Operators for Interview

  -------------------------------------------------------------------------------------------------------------
  Operator               Description                   Example
  ---------------------- ----------------------------- --------------------------------------------------------
  `map`                  Transforms data               `.map { $0 * 2 }`

  `filter`               Filters emitted values        `.filter { $0 > 10 }`

  `debounce`             Waits before emitting         `.debounce(for: .seconds(1), scheduler: RunLoop.main)`

  `merge`                Combines multiple publishers  `publisher1.merge(with: publisher2)`

  `combineLatest`        Emits when any of multiple    `combineLatest(textPublisher, sliderPublisher)`
                         publishers emit               
  -------------------------------------------------------------------------------------------------------------

## Common Interview Questions

1.  What is Combine used for?
2.  Difference between Combine, async/await, and Delegates?
3.  What is the role of Cancellable?
4.  Explain `map`, `filter`, and `decode`.
5.  How to handle networking with Combine?
6.  How to cancel a subscription?
7.  How is Combine used with SwiftUI's `@Published` properties?

---
# Role of `Cancellable` in Combine (Swift)
---

## 🧩 What is `Cancellable`?

`Cancellable` is a **protocol** in Combine that represents **a
connection (subscription)** between a **Publisher** and a
**Subscriber**.\
When you subscribe to a publisher, Combine returns a `Cancellable`
object --- typically stored in a variable like `AnyCancellable`.\
If you cancel it, the publisher stops sending values, freeing memory and
resources.

------------------------------------------------------------------------

## 💡 Why It's Needed

Combine creates **long-living data streams** (e.g., network requests, UI
bindings).\
If these aren't stopped manually, they keep emitting data --- leading to
**memory leaks** or **unnecessary background work**.

`Cancellable` lets you: - Stop the subscription anytime\
- Prevent memory leaks\
- Control when data flow should end

------------------------------------------------------------------------

## 🧠 Example

``` swift
import Combine

class ExampleViewModel {
    var cancellable: AnyCancellable?

    func fetchData() {
        cancellable = URLSession.shared.dataTaskPublisher(for: URL(string: "https://api.example.com")!)
            .map(\.data)
            .sink(receiveCompletion: { completion in
                print("Completed:", completion)
            }, receiveValue: { data in
                print("Received \(data.count) bytes")
            })
    }

    func cancelRequest() {
        cancellable?.cancel()
        print("Request cancelled")
    }
}

let viewModel = ExampleViewModel()
viewModel.fetchData()

// Later when not needed
viewModel.cancelRequest()
```

### 🧾 Output

    Request cancelled

Here: - `sink` returns an `AnyCancellable` - When you call `cancel()`,
the subscription stops --- Combine will not send more values or
completion events.

------------------------------------------------------------------------

## 🧱 Common Patterns

### 1. Store multiple cancellables

``` swift
var cancellables = Set<AnyCancellable>()

publisher
    .sink { value in print(value) }
    .store(in: &cancellables)
```

### 2. Automatic cancellation

When the object (e.g., `ViewModel`) deinitializes, all cancellables in
its set are automatically cancelled.

------------------------------------------------------------------------

## ⚙️ Summary

  Aspect           Description
  ---------------- -----------------------------------------------------
  **Type**         Protocol
  **Purpose**      Manage and cancel Combine subscriptions
  **Prevents**     Memory leaks, unnecessary background work
  **Common Use**   Stored in `AnyCancellable` or `Set<AnyCancellable>`
  **Analogy**      Like `Task.cancel()` in `async/await`

------------------------------------------------------------------------

## 🔁 Comparison Tip

-   **Combine's `Cancellable`** → Cancels data streams (reactive
    pipelines).\
-   **`Task` in async/await** → Cancels concurrent async work.\
    Both are mechanisms for **cleaning up async operations** safely.

---
# Difference between Combine, async/await, and Delegates in Swift
---

## 🧩 Overview Table

  --------------------------------------------------------------------------------
  Feature          Combine                 async/await          Delegates
  ---------------- ----------------------- -------------------- ------------------
  **Introduced     iOS 13                  iOS 15               iOS 2 (very old)
  In**                                                          

  **Type**         Reactive Framework      Structured           Design Pattern
                                           Concurrency          

  **Paradigm**     Publisher--Subscriber   Sequential,          One-to-one
                   (Reactive)              Synchronous-like     communication
                                           async code           

  **Best For**     Handling multiple async Simple async tasks   Passing
                   data streams or event   (network calls, file data/events
                   chains                  IO, etc.)            between two
                                                                objects

  **Data Flow**    One-to-many (multiple   One-time async       One-to-one
                   subscribers)            response             

  **Complexity**   High                    Medium               Low

  **Memory         Uses `AnyCancellable`   Automatic (`Task` &  Uses `weak`
  Handling**                               structured           delegate reference
                                           concurrency)         
  

------------------------------------------------------------------------

## 🧠 1. Delegates

**Oldest and simplest** pattern for one-to-one communication between
objects.

### ✅ Use When

-   You need to send data/events **from one object to another**
    (e.g. from a child view controller to parent).
-   Example: TableView delegates, custom callbacks.

### 🧩 Example

``` swift
protocol DataDelegate: AnyObject {
    func didReceiveData(_ data: String)
}

class Sender {
    weak var delegate: DataDelegate?
    
    func fetchData() {
        delegate?.didReceiveData("Hello from Delegate!")
    }
}

class Receiver: DataDelegate {
    func didReceiveData(_ data: String) {
        print(data)
    }
}

let sender = Sender()
let receiver = Receiver()
sender.delegate = receiver
sender.fetchData()
// Output: Hello from Delegate!
```

------------------------------------------------------------------------

## ⚡️ 2. Combine

**Reactive programming** framework by Apple.\
Used for chaining and transforming multiple asynchronous streams (like
network responses, UI updates, notifications).

### ✅ Use When

-   You need **reactive streams** (multiple async updates over time).
-   Example: Combine network publisher → decode → update UI.

### 🧩 Example

``` swift
import Combine
import Foundation

class APIService {
    var cancellable: AnyCancellable?

    func fetchData() {
        cancellable = URLSession.shared.dataTaskPublisher(for: URL(string: "https://api.example.com")!)
            .map(\.data)
            .decode(type: [String].self, decoder: JSONDecoder())
            .sink(receiveCompletion: { print($0) },
                  receiveValue: { print("Data: \($0)") })
    }
}
```

------------------------------------------------------------------------

## 🕒 3. async/await

Introduced in Swift 5.5 (iOS 15).\
Makes asynchronous code look **synchronous and linear**, improving
readability.

### ✅ Use When

-   You have **one-time async calls** like fetching data, writing files,
    etc.
-   You want **clean, modern async syntax** without callbacks or
    publishers.

### 🧩 Example

``` swift
func fetchData() async throws -> [String] {
    let url = URL(string: "https://api.example.com")!
    let (data, _) = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode([String].self, from: data)
}

Task {
    do {
        let result = try await fetchData()
        print("Data:", result)
    } catch {
        print("Error:", error)
    }
}
```

------------------------------------------------------------------------

## 🧭 When to Use Which

  -----------------------------------------------------------------------
  Scenario                        Best Option
  ------------------------------- ---------------------------------------
  One-to-one communication        **Delegate**
  between objects (simple)        

  Handling continuous event/data  **Combine**
  streams                         

  Simple, single async task (like **async/await**
  network calls)                  

  Complex pipelines (e.g. data →  **Combine**
  transform → UI update →         
  logging)                        

  Migrate legacy code             Start moving **Delegates →
                                  async/await** or **Combine**
  
------------------------------------------------------------------------

### 🚀 Tip

-   **Delegates** are great for UIKit patterns and simple callbacks.\
-   **async/await** is best for modern async logic (clean and
    readable).\
-   **Combine** is powerful for complex reactive scenarios (but overkill
    for simple async calls).

---
 # 🧩 Combine Framework – Advantages, Keywords, and Usage
---

## 🚀 Advantages of Combine

Combine provides a **declarative, consistent, and type-safe** way to handle asynchronous data  
(events, network calls, user input) with less boilerplate and better readability.

Use Combine for **reactive programming in Swift** — such as:

- Binding UI to data  
- Handling network responses  
- Form validation  
- Search debounce  
- Chaining async operations in a clean pipeline  

---

## 🧠 Keywords and Usage

### 🔹 `cancellables`
A collection that stores subscriptions (`AnyCancellable`) to keep Combine pipelines alive until explicitly cancelled or deallocated.

### 🔹 `debounce`
Delays publishing values until no new values are received for a specified time interval, useful for reducing rapid updates (e.g., typing).

### 🔹 `removeDuplicates`
Prevents publishing of consecutive duplicate values, ensuring downstream only receives changes.

### 🔹 `assign(to:)`
Automatically assigns published values to a property, keeping it updated with the latest emitted value.

### 🔹 `sink`
Subscribes to a publisher and allows you to handle received values (and optionally completion events) manually.

### 🔹 `CombineLatest`
Watches multiple publishers and emits combined values whenever **any** of them changes.

### 🔹 `Merge`
Combines multiple publishers so that any of them can emit a value independently.

---

## 🧩 Example 1 – Using CombineLatest

```swift
Publishers.CombineLatest($email, $password)
    .map { email, password in
        return email.contains("@") && password.count >= 6
    }
    .assign(to: &$isValid)
```

### 🧾 Explanation
- `$email` and `$password` are `@Published` properties — publishers that emit values whenever they change.  
- `Publishers.CombineLatest` takes these two publishers and **combines** them.  
- Every time either `email` or `password` changes, it gives the latest values of both.  
- `.map { email, password in ... }` checks validation logic.  
- `.assign(to: &$isValid)` automatically updates the `isValid` property with the result.

✅ So, `isValid` becomes `true` only if the email and password are valid.

---

## 🧩 Example 2 – Using Merge

```swift
Publishers.Merge($email, $password)
    .sink { [weak self] _ in
        guard let self = self else { return }
        self.showMessage = false
        self.isUserFound = false
    }
    .store(in: &cancellables)
```

### 🧾 Explanation
- `Publishers.Merge($email, $password)` emits values **whenever any one** of them changes.  
- `.sink { ... }` subscribes to the publisher and reacts to changes.  

When the user types in either field:
- `showMessage = false` → hides the “User Found / Not Found” message.  
- `isUserFound = false` → resets the login result.  
- `.store(in: &cancellables)` keeps the subscription alive; otherwise, it cancels immediately.

---

## 🧠 Summary

| Concept | Description |
|----------|--------------|
| Combine | Declarative framework for managing async data |
| cancellables | Holds Combine subscriptions |
| debounce | Delays emissions to prevent rapid updates |
| CombineLatest | Combines latest values of multiple publishers |
| Merge | Emits values when any publisher changes |
| sink | Subscriber to handle values manually |

---
