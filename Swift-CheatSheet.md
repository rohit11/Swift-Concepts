# Swift Quick Reference Cheat Sheet 🚀

*Fast lookup for Swift syntax and common patterns*

---

## Variables & Constants

```swift
let constant = "immutable"           // Constant
var variable = "mutable"             // Variable
var explicitType: String = "type"    // Explicit type
let inferred = "Swift guesses type"  // Type inference
```

**When to use:**
- `let` for values that never change
- `var` for values that can change

---

## Basic Types

```swift
// Numbers
let integer: Int = 42
let double: Double = 3.14
let float: Float = 3.14

// Text
let string: String = "Hello"
let character: Character = "A"

// Boolean
let boolean: Bool = true

// Collections
let array: [String] = ["a", "b"]
let dictionary: [String: Int] = ["a": 1, "b": 2]
let set: Set<String> = ["a", "b"]
```

---

## Operators

```swift
// Arithmetic
let sum = a + b
let difference = a - b
let product = a * b
let quotient = a / b
let remainder = a % b

// Compound
count += 1      // count = count + 1
count -= 1      // count = count - 1
count *= 2      // count = count * 2
count /= 2      // count = count / 2

// Comparison
let equal = a == b
let notEqual = a != b
let less = a < b
let greater = a > b
let lessEqual = a <= b
let greaterEqual = a >= b

// Logical
let and = a && b
let or = a || b
let not = !a
```

---

## Strings

```swift
let firstName = "John"
let lastName = "Doe"

// Concatenation
let fullName = firstName + " " + lastName

// Interpolation
let greeting = "Hello, \(firstName)!"

// Methods
let upper = text.uppercased()
let lower = text.lowercased()
let hasText = text.contains("search")
let words = text.components(separatedBy: " ")
let replaced = text.replacingOccurrences(of: "old", with: "new")
```

---

## Collections

### Arrays
```swift
var array = ["a", "b", "c"]
array.append("d")                    // Add to end
array.insert("x", at: 1)            // Insert at index
array.remove(at: 0)                 // Remove at index
array.removeLast()                   // Remove last
let first = array[0]                // Access by index
let count = array.count             // Get count
let isEmpty = array.isEmpty         // Check if empty
let hasItem = array.contains("a")   // Check if contains
```

### Dictionaries
```swift
var dict = ["key1": "value1", "key2": "value2"]
dict["key3"] = "value3"            // Add/update
dict.removeValue(forKey: "key1")    // Remove
let value = dict["key1"]            // Access (returns optional)
let keys = Array(dict.keys)         // Get all keys
let values = Array(dict.values)     // Get all values
```

### Sets
```swift
var set: Set<String> = ["a", "b", "c"]
set.insert("d")                     // Add element
set.remove("a")                     // Remove element
let union = set1.union(set2)        // Combine sets
let intersection = set1.intersection(set2)  // Common elements
let difference = set1.subtracting(set2)     // Elements in set1 not in set2
```

---

## Control Flow

### If Statements
```swift
if condition {
    // code
} else if anotherCondition {
    // code
} else {
    // code
}

// Ternary
let result = condition ? value1 : value2
```

### Switch Statements
```swift
switch value {
case value1:
    // code
case value2, value3:
    // code
case range1...range2:
    // code
default:
    // code
}
```

### Loops
```swift
// For-in
for item in array {
    // code
}

// For-in with range
for i in 1...5 { }           // 1 to 5 inclusive
for i in 1..<5 { }           // 1 to 4

// While
while condition {
    // code
}

// Repeat-while
repeat {
    // code
} while condition
```

---

## Functions

```swift
// Basic function
func functionName() {
    // code
}

// With parameters
func greet(name: String) {
    print("Hello, \(name)!")
}

// With return value
func add(a: Int, b: Int) -> Int {
    return a + b
}

// With external names
func greet(firstName: String, lastName: String) {
    // code
}

// Without external names
func multiply(_ a: Int, _ b: Int) -> Int {
    return a * b
}

// Default parameters
func createUser(name: String, age: Int = 18) {
    // code
}

// Variadic parameters
func sum(numbers: Int...) -> Int {
    return numbers.reduce(0, +)
}

// Function calls
greet(name: "John")
greet(firstName: "John", lastName: "Doe")
multiply(5, 3)
createUser(name: "Alice")
sum(numbers: 1, 2, 3)
```

---

## Closures

```swift
// Basic closure
let closure = { (parameter: Type) -> ReturnType in
    // code
    return value
}

// Shorthand
let simple = { $0 * 2 }

// With collections
let doubled = array.map { $0 * 2 }
let filtered = array.filter { $0 > 5 }
let sum = array.reduce(0) { $0 + $1 }
let sorted = array.sorted { $0 < $1 }

// Trailing closure
array.map { item in
    return item.uppercased()
}
```

---

## Enumerations

```swift
// Basic enum
enum Direction {
    case north, south, east, west
}

// With associated values
enum Result {
    case success(String)
    case failure(Error)
}

// With raw values
enum Planet: Int {
    case mercury = 1, venus = 2, earth = 3
}

// Using enums
let direction = Direction.north
let result = Result.success("Data loaded")
let earth = Planet.earth
let earthNumber = earth.rawValue
```

---

## Structures & Classes

### Structures
```swift
struct Point {
    var x: Double
    var y: Double
    
    func distance(to other: Point) -> Double {
        // implementation
    }
    
    mutating func moveBy(x: Double, y: Double) {
        self.x += x
        self.y += y
    }
}

let point = Point(x: 0, y: 0)
var mutablePoint = Point(x: 1, y: 1)
mutablePoint.moveBy(x: 5, y: 3)
```

### Classes
```swift
class Person {
    var name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    func birthday() {
        age += 1
    }
    
    deinit {
        // cleanup code
    }
}

let person = Person(name: "John", age: 25)
person.birthday()
```

---

## Properties

```swift
struct Rectangle {
    var width: Double
    var height: Double
    
    // Computed property
    var area: Double {
        return width * height
    }
    
    // Computed property with setter
    var perimeter: Double {
        get {
            return 2 * (width + height)
        }
        set {
            let side = newValue / 4
            width = side
            height = side
        }
    }
    
    // Property observers
    var temperature: Double = 0.0 {
        willSet {
            print("Will change to \(newValue)")
        }
        didSet {
            print("Changed from \(oldValue) to \(temperature)")
        }
    }
    
    // Type property
    static let maxSize = 1000.0
}
```

---

## Methods

```swift
class Counter {
    var count = 0
    
    // Instance method
    func increment() {
        count += 1
    }
    
    // Mutating method (for structs)
    mutating func increment(by amount: Int) {
        count += amount
    }
    
    // Type method
    static func reset() {
        // static method code
    }
}

let counter = Counter()
counter.increment()
Counter.reset()
```

---

## Subscripts

```swift
struct Matrix {
    let rows: Int, columns: Int
    var grid: [Double]
    
    subscript(row: Int, column: Int) -> Double {
        get {
            return grid[(row * columns) + column]
        }
        set {
            grid[(row * columns) + column] = newValue
        }
    }
}

var matrix = Matrix(rows: 2, columns: 2)
matrix[0, 0] = 1.0
let value = matrix[0, 0]
```

---

## Inheritance

```swift
class Vehicle {
    var numberOfWheels: Int
    
    init(wheels: Int) {
        self.numberOfWheels = wheels
    }
    
    func startEngine() {
        print("Starting engine")
    }
}

class Car: Vehicle {
    var numberOfDoors: Int
    
    init(doors: Int) {
        self.numberOfDoors = doors
        super.init(wheels: 4)
    }
    
    override func startEngine() {
        print("Starting car engine")
    }
}

// Final class (no inheritance)
final class FinalClass {
    // code
}
```

---

## Initialization

```swift
class Person {
    var name: String
    var age: Int
    
    // Designated initializer
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    // Convenience initializer
    convenience init(name: String) {
        self.init(name: name, age: 18)
    }
    
    // Required initializer
    required init() {
        self.name = "Unknown"
        self.age = 0
    }
}

let person1 = Person(name: "John", age: 25)
let person2 = Person(name: "Alice")  // age defaults to 18
```

---

## Optional Chaining

```swift
class Person {
    var name: String
    var address: Address?
}

class Address {
    var street: String
    var city: String
}

let person = Person(name: "John")
person.address = Address(street: "123 Main", city: "NYC")

// Safe property access
let city = person.address?.city  // "NYC" or nil
let street = person.address?.street  // "123 Main" or nil

// Safe method calls
let result = person.address?.getFullAddress()
```

---

## Error Handling

```swift
enum NetworkError: Error {
    case noConnection
    case invalidResponse
    case timeout
}

func fetchData() throws -> String {
    // Simulate network call
    throw NetworkError.timeout
}

// Using throwing functions
do {
    let data = try fetchData()
    print("Success: \(data)")
} catch NetworkError.noConnection {
    print("No internet")
} catch NetworkError.timeout {
    print("Request timed out")
} catch {
    print("Unknown error: \(error)")
}

// Try with optional
let data = try? fetchData()  // Returns nil if error

// Try with forced unwrapping (dangerous!)
let forcedData = try! fetchData()  // Crashes if error
```

---

## Concurrency

```swift
// Async function
func fetchUserData() async throws -> String {
    try await Task.sleep(nanoseconds: 1_000_000_000)
    return "User data"
}

// Using async functions
func processData() async {
    do {
        let data = try await fetchUserData()
        print("Received: \(data)")
    } catch {
        print("Error: \(error)")
    }
}

// Task
Task {
    await processData()
}
```

---

## Protocols

```swift
protocol Drawable {
    func draw()
    var area: Double { get }
}

struct Circle: Drawable {
    let radius: Double
    
    func draw() {
        print("Drawing circle")
    }
    
    var area: Double {
        return Double.pi * radius * radius
    }
}

// Protocol with associated types
protocol Container {
    associatedtype Item
    var items: [Item] { get set }
    mutating func add(_ item: Item)
}
```

---

## Generics

```swift
// Generic function
func swapValues<T>(_ a: inout T, _ b: inout T) {
    let temp = a
    a = b
    b = temp
}

// Generic type
struct Stack<Element> {
    var items: [Element] = []
    
    mutating func push(_ item: Element) {
        items.append(item)
    }
    
    mutating func pop() -> Element? {
        return items.popLast()
    }
}

// Using generics
var intStack = Stack<Int>()
intStack.push(1)
intStack.push(2)

var stringStack = Stack<String>()
stringStack.push("Hello")
```

---

## Extensions

```swift
extension String {
    func reversed() -> String {
        return String(self.reversed())
    }
    
    var isPalindrome: Bool {
        let cleaned = self.lowercased().filter { $0.isLetter }
        return cleaned == cleaned.reversed()
    }
}

extension Int {
    var isEven: Bool {
        return self % 2 == 0
    }
    
    func times(_ action: () -> Void) {
        for _ in 0..<self {
            action()
        }
    }
}

// Using extensions
let text = "racecar"
print(text.isPalindrome)  // true

let number = 3
number.times { print("Hello") }  // Prints "Hello" 3 times
```

---

## Access Control

```swift
public class PublicClass { }           // Accessible everywhere
internal class InternalClass { }       // Accessible within module (default)
fileprivate class FilePrivateClass { } // Accessible within file
private class PrivateClass { }         // Accessible within type

// Access levels for members
class Example {
    public var publicProperty = "Public"
    internal var internalProperty = "Internal"
    fileprivate var fileprivateProperty = "File Private"
    private var privateProperty = "Private"
}
```

---

## Common Patterns

### Singleton
```swift
class NetworkManager {
    static let shared = NetworkManager()
    private init() {}
    
    func fetchData() {
        // Implementation
    }
}

// Usage
NetworkManager.shared.fetchData()
```

### Builder Pattern
```swift
struct URLBuilder {
    private var components = URLComponents()
    
    func scheme(_ scheme: String) -> URLBuilder {
        var copy = self
        copy.components.scheme = scheme
        return copy
    }
    
    func host(_ host: String) -> URLBuilder {
        var copy = self
        copy.components.host = host
        return copy
    }
    
    func build() -> URL? {
        return components.url
    }
}

let url = URLBuilder()
    .scheme("https")
    .host("api.example.com")
    .build()
```

### Observer Pattern
```swift
class Observable<T> {
    private var observers: [(T) -> Void] = []
    
    var value: T {
        didSet {
            observers.forEach { $0(value) }
        }
    }
    
    init(_ value: T) {
        self.value = value
    }
    
    func observe(_ observer: @escaping (T) -> Void) {
        observers.append(observer)
    }
}

let observable = Observable("Initial")
observable.observe { value in
    print("Value changed to: \(value)")
}
observable.value = "New Value"
```

---

## Quick Tips

### Performance
- Use `let` instead of `var` when possible
- Prefer structs over classes for simple data types
- Use `lazy` for expensive properties
- Use `weak` references to prevent retain cycles

### Safety
- Use optional binding (`if let`, `guard let`)
- Prefer `as?` over `as!` for type casting
- Handle errors with `do-catch`
- Use `guard` for early returns

### Readability
- Use meaningful names
- Break long functions into smaller ones
- Use extensions to organize code
- Follow Swift naming conventions

---

## Common SwiftUI Patterns

```swift
// State management
@State private var text = ""
@StateObject private var viewModel = ViewModel()
@ObservedObject var model: Model
@EnvironmentObject var data: DataModel

// Computed properties
var computedValue: String {
    return "Computed: \(text)"
}

// View modifiers
Text("Hello")
    .font(.title)
    .foregroundColor(.blue)
    .padding()
    .background(Color.gray)
    .cornerRadius(10)
```

---

*Keep this cheat sheet handy for quick reference while coding!*
