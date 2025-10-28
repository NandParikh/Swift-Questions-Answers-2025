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
```swift
var fruits: Set<String> = ["Apple", "Banana", "Mango"]
fruits.insert("Orange")
print(fruits)
```

### Array
```swift
var fruits = ["Apple", "Banana", "Mango"]
fruits.append("Orange")
let upperFruits = fruits.map { $0.uppercased() }
print(upperFruits)
```

### Dictionary
```swift
var studentScores = ["John": 90, "Emma": 85, "Liam": 95]
studentScores["Olivia"] = 88
studentScores.forEach { key, value in
    print("\(key): \(value)")
}
```

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
