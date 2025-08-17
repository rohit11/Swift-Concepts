# Swift Language Complete Learning Guide 🚀

*Master Swift with comprehensive explanations, real-world examples, and deep understanding*

## 📖 About This Guide

This guide is designed to be your comprehensive Swift learning resource. Unlike the official Swift book which covers everything in detail, this guide focuses on **practical understanding** with:

- **In-depth explanations** of why and how each concept works
- **Real-world examples** that you'll actually use in apps
- **Common pitfalls** and how to avoid them
- **Best practices** from professional Swift development
- **Links to official documentation** for deeper exploration

## 🔗 Official Swift Documentation

For complete reference and advanced topics, always refer to the [official Swift Language Guide](https://docs.swift.org/swift-book/documentation/the-swift-programming-language).

## 🎯 How to Use This Guide

1. **Read each section completely** - Don't skip the explanations
2. **Type out examples** - Don't just copy-paste, understand by doing
3. **Experiment** - Modify examples to see what happens
4. **Build projects** - Apply concepts to real app ideas
5. **Reference the cheat sheet** - Use Swift-CheatSheet.md for quick syntax lookup

## 📚 Guide Status

**This guide is being continuously updated** to provide comprehensive, in-depth explanations of Swift concepts. Currently updated sections include:

✅ **The Basics** - Complete with detailed explanations, examples, and best practices  
✅ **Basic Types** - Comprehensive coverage of integers, floats, booleans, and strings  
✅ **Collection Types** - Complete with Arrays, Dictionaries, and Sets (comprehensive coverage)  
✅ **Control Flow** - Complete with If statements, Switch statements, and Loops  
✅ **Functions** - Complete with parameter types, overloading, and advanced features  
✅ **Closures** - Complete with syntax, collection operations, and real-world examples  
✅ **Enumerations** - Complete with associated values, raw values, and real-world examples  
✅ **Structures and Classes** - Complete with value vs reference types and real-world examples  
✅ **Properties** - Complete with stored, computed, observers, and property wrappers  
✅ **Methods** - Complete with instance, type, and mutating methods  
✅ **Subscripts** - Complete with basic syntax, parameter types, and real-world examples  
✅ **Inheritance** - Complete with basic syntax, overriding, and real-world examples  
✅ **Initialization** - Complete with basic initializers, parameters, and real-world examples  
✅ **Deinitialization** - Complete with resource cleanup and memory management  
✅ **Optional Chaining** - Complete with safe access, methods, and real-world examples  
🔄 **Remaining sections** - Will be updated with the same level of detail  

**For complete reference and advanced topics, always refer to the [official Swift Language Guide](https://docs.swift.org/swift-book/documentation/the-swift-programming-language).**

## Table of Contents
- [The Basics](#the-basics)
- [Basic Operators](#basic-operators)
- [Strings and Characters](#strings-and-characters)
- [Collection Types](#collection-types)
- [Control Flow](#control-flow)
- [Functions](#functions)
- [Closures](#closures)
- [Enumerations](#enumerations)
- [Structures and Classes](#structures-and-classes)
- [Properties](#properties)
- [Methods](#methods)
- [Subscripts](#subscripts)
- [Inheritance](#inheritance)
- [Initialization](#initialization)
- [Deinitialization](#deinitialization)
- [Optional Chaining](#optional-chaining)
- [Error Handling](#error-handling)
- [Concurrency](#concurrency)
- [Macros](#macros)
- [Type Casting](#type-casting)
- [Nested Types](#nested-types)
- [Extensions](#extensions)
- [Protocols](#protocols)
- [Generics](#generics)
- [Opaque and Boxed Protocol Types](#opaque-and-boxed-protocol-types)
- [Automatic Reference Counting](#automatic-reference-counting)
- [Memory Safety](#memory-safety)
- [Access Control](#access-control)
- [Advanced Operators](#advanced-operators)

---

## The Basics

Swift's foundation is built on a few core principles: **safety**, **performance**, and **clarity**. Understanding these basics thoroughly will make everything else much easier.

### Variables and Constants

**Why This Matters:** Swift's type system is designed to catch errors at compile time, not runtime. Understanding the difference between constants and variables is crucial for writing safe, efficient code.

#### Constants (`let`) - Immutable Values

Constants are values that never change after they're set. They're the default choice in Swift because they're safer and more performant.

```swift
// Constants - use when value won't change
let maxUsers = 100
let appName = "MyApp"
let apiBaseURL = "https://api.myapp.com"
let maxRetryAttempts = 3

// Constants with explicit types
let explicitDouble: Double = 95
let explicitString: String = "Hello"
let explicitArray: [Int] = [1, 2, 3, 4, 5]
```

**When to use constants:**
- **Configuration values** that won't change during app execution
- **Magic numbers** that should be named constants (like `maxRetryAttempts`)
- **API endpoints** and configuration strings
- **Mathematical constants** like `π` or `e`
- **Any value** that logically shouldn't change

**Benefits of constants:**
- **Performance**: Swift can optimize code better knowing values won't change
- **Safety**: Prevents accidental modifications that could cause bugs
- **Clarity**: Makes code intent clear to other developers
- **Thread safety**: Constants are inherently thread-safe

#### Variables (`var`) - Mutable Values

Variables are values that can change during execution. Use them only when you actually need mutability.

```swift
// Variables - use when value can change
var currentUserCount = 0
var isLoggedIn = false
var currentScore = 0
var userName = ""
var itemsInCart: [String] = []

// Variables with explicit types
var explicitCounter: Int = 0
var explicitStatus: Bool = false
```

**When to use variables:**
- **State that changes** (user input, network responses, game scores)
- **Counters and accumulators** (loops, user actions)
- **User interface state** (text field contents, selection states)
- **Temporary calculations** that get updated

**Best practices:**
- Start with `let` by default
- Only change to `var` when you actually need mutability
- Use descriptive names that indicate the value can change

#### Type Inference - Swift's Smart Type Detection

Swift can automatically determine the type of a value based on what you assign to it. This makes code cleaner while maintaining type safety.

```swift
// Type inference - Swift guesses the type automatically
let score = 95        // Swift infers: Int
let average = 95.5    // Swift infers: Double
let name = "John"     // Swift infers: String
let isActive = true   // Swift infers: Bool
let coordinates = (x: 10, y: 20)  // Swift infers: (x: Int, y: Int)

// Swift is very smart about type inference
let numbers = [1, 2, 3, 4, 5]  // Swift infers: [Int]
let mixedArray = [1, "hello", true]  // Swift infers: [Any]
```

**How type inference works:**
1. Swift looks at the literal value you assign
2. It determines the most specific type that can hold that value
3. It applies that type to your constant/variable
4. The type is locked in and can't be changed

**Benefits:**
- **Cleaner code** - no need to write types everywhere
- **Type safety** - still catches type errors at compile time
- **Performance** - no runtime type checking needed

**When to use explicit types:**
- **Clarity** - when the inferred type might not be obvious
- **API requirements** - when you need a specific type for a function
- **Documentation** - to make code more readable for other developers

```swift
// Sometimes explicit types are clearer
let userID: String = "12345"  // Clear this is a string ID, not a number
let maxFileSize: Int64 = 1024 * 1024 * 100  // Clear this is a large number
let apiResponse: [String: Any] = [:]  // Clear this is a JSON-like dictionary
```

#### Type Annotations - Being Explicit About Types

While type inference is powerful, sometimes you want to be explicit about types for clarity or to force a specific type.

```swift
// Explicit type annotations
let explicitDouble: Double = 95  // Forces Double instead of Int
let explicitFloat: Float = 95.0  // Forces Float instead of Double
let explicitString: String = "95"  // Forces String instead of Int

// Useful for API compatibility
let userAge: UInt8 = 25  // Small, positive integer
let fileSize: UInt64 = 1024 * 1024 * 100  // Large file size
let temperature: Float = 98.6  // Single precision for temperature
```

**When to use explicit types:**
- **API compatibility** - when you need a specific type for a function
- **Performance** - when you need a smaller type (UInt8 vs Int)
- **Clarity** - when the inferred type might be confusing
- **Documentation** - to make your code's intent clear

#### Real-World Example: User Registration

Let's see how these concepts work together in a real app scenario:

```swift
// User registration form
struct UserRegistration {
    // Constants - won't change during registration
    let minPasswordLength = 8
    let maxUsernameLength = 20
    let allowedCharacters = CharacterSet.alphanumerics
    
    // Variables - will change as user types
    var username = ""
    var email = ""
    var password = ""
    var confirmPassword = ""
    
    // Computed property using constants
    var isPasswordValid: Bool {
        return password.count >= minPasswordLength &&
               password == confirmPassword
    }
    
    var isFormComplete: Bool {
        return !username.isEmpty && 
               !email.isEmpty && 
               !password.isEmpty && 
               !confirmPassword.isEmpty
    }
}

// Usage
var registration = UserRegistration()
registration.username = "john_doe"
registration.email = "john@example.com"
registration.password = "secure123"
registration.confirmPassword = "secure123"

if registration.isFormComplete && registration.isPasswordValid {
    print("Ready to register user!")
} else {
    print("Please complete all fields and ensure passwords match")
}
```

**Key takeaways from this example:**
- **Constants** define the rules and constraints
- **Variables** hold the changing user input
- **Computed properties** use constants to validate variables
- **Type inference** makes the code clean and readable

#### Common Pitfalls and How to Avoid Them

**Pitfall 1: Using `var` when you should use `let`**

```swift
// ❌ Wrong - this value never changes
var maxRetries = 3

// ✅ Correct - this is a constant
let maxRetries = 3
```

**Pitfall 2: Not understanding type inference**

```swift
// ❌ Wrong - Swift infers this as Int, not Double
let price = 19.99  // This is actually 19 (Int)

// ✅ Correct - be explicit about decimal types
let price: Double = 19.99
let price = 19.99  // Or use decimal notation
```

**Pitfall 3: Changing constants**

```swift
// ❌ This will cause a compile error
let maxUsers = 100
maxUsers = 200  // Error: Cannot assign to 'let' constant

// ✅ Use var if you need to change the value
var maxUsers = 100
maxUsers = 200  // This works
```

#### Performance Implications

Understanding the performance benefits of constants:

```swift
// Constants allow Swift to optimize better
let maxItems = 1000
let itemsPerPage = 20

// Swift can optimize this loop better knowing maxItems is constant
for i in 0..<maxItems {
    if i % itemsPerPage == 0 {
        print("Page \(i / itemsPerPage + 1)")
    }
}

// Variables require more runtime checks
var currentMax = 1000
for i in 0..<currentMax {  // Swift must check currentMax each iteration
    // ... loop body
}
```

#### Best Practices Summary

1. **Start with `let`** - Only use `var` when you actually need mutability
2. **Use descriptive names** - Make it clear what the value represents
3. **Group related constants** - Use structs or enums to organize constants
4. **Be explicit when needed** - Use type annotations for clarity
5. **Think about scope** - Constants can be local, instance-level, or type-level

```swift
// Good organization of constants
struct AppConstants {
    static let maxUsers = 1000
    static let apiTimeout: TimeInterval = 30.0
    static let maxRetries = 3
    
    struct UI {
        static let cornerRadius: CGFloat = 8.0
        static let animationDuration: TimeInterval = 0.3
    }
    
    struct API {
        static let baseURL = "https://api.myapp.com"
        static let version = "v1"
    }
}

// Usage
let timeout = AppConstants.apiTimeout
let cornerRadius = AppConstants.UI.cornerRadius
let apiURL = "\(AppConstants.API.baseURL)/\(AppConstants.API.version)"
```

This foundation will serve you well as you learn more advanced Swift concepts. The key is understanding **why** Swift makes these choices and how they help you write better, safer code.

### Basic Types

Swift provides a rich set of built-in types that are designed to be both safe and performant. Understanding these types deeply will help you make better decisions about which type to use in different situations.

#### Integer Types - Whole Numbers

Swift provides several integer types, each optimized for different use cases. The choice between them affects both memory usage and performance.

```swift
// Signed integers (can be positive or negative)
let age: Int = 25                    // Platform-dependent (32-bit or 64-bit)
let smallNumber: Int8 = 127          // -128 to 127
let mediumNumber: Int16 = 32767      // -32,768 to 32,767
let largeNumber: Int32 = 2147483647  // -2,147,483,648 to 2,147,483,647
let hugeNumber: Int64 = 9223372036854775807  // -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807

// Unsigned integers (positive only)
let positiveAge: UInt = 25           // Platform-dependent
let smallPositive: UInt8 = 255       // 0 to 255
let mediumPositive: UInt16 = 65535   // 0 to 65,535
let largePositive: UInt32 = 4294967295  // 0 to 4,294,967,295
let hugePositive: UInt64 = 18446744073709551615  // 0 to 18,446,744,073,709,551,615
```

**When to use each integer type:**

- **`Int`** (default choice):
  - General-purpose counting and arithmetic
  - Array indices and loop counters
  - Most everyday integer needs
  - Automatically adapts to platform (32-bit on older devices, 64-bit on newer)

- **`Int8/Int16/Int32/Int64`**:
  - When you need specific size guarantees
  - Interfacing with C APIs that require specific types
  - Memory-constrained environments
  - Network protocols with fixed-size fields

- **`UInt` and variants**:
  - When you know values will never be negative
  - Array counts and sizes
  - Memory addresses and offsets
  - **Warning**: Be careful with UInt - mixing with Int can cause issues

**Real-world example - Memory-efficient data structure:**

```swift
struct CompactUser {
    let id: UInt32           // 4 bytes, 0 to ~4 billion users
    let age: UInt8           // 1 byte, 0 to 255 years
    let score: UInt16        // 2 bytes, 0 to 65,535 points
    let isActive: Bool       // 1 byte
    // Total: 8 bytes instead of 16+ bytes with Int types
}

// Usage
let user = CompactUser(id: 12345, age: 25, score: 1000, isActive: true)
```

**Common pitfalls with integers:**

```swift
// ❌ Overflow error - Int8 can only hold -128 to 127
let small: Int8 = 127
small + 1  // Runtime crash: arithmetic overflow

// ✅ Safe arithmetic with overflow operators
let safeResult = small &+ 1  // Wraps around to -128

// ❌ Mixing signed and unsigned can cause issues
let signed: Int = -5
let unsigned: UInt = 10
// let result = signed + unsigned  // Compile error

// ✅ Convert to same type first
let result = signed + Int(unsigned)  // 5
```

#### Floating-Point Types - Decimal Numbers

Swift provides two floating-point types with different precision and performance characteristics.

```swift
// Double - 64-bit precision (default for floating-point)
let precisePrice: Double = 19.99
let scientificNotation: Double = 1.23e-4  // 0.000123
let largeDecimal: Double = 123456789.123456789

// Float - 32-bit precision
let temperature: Float = 98.6
let smallDecimal: Float = 3.14159
let approximatePi: Float = 3.14159
```

**When to use each floating-point type:**

- **`Double`** (default choice):
  - Financial calculations (prices, money)
  - Scientific calculations requiring precision
  - Most decimal arithmetic
  - Better precision for large numbers

- **`Float`**:
  - When memory is critical
  - Graphics and game development (performance)
  - Approximate calculations where precision isn't crucial
  - Interfacing with APIs that require Float

**Precision comparison:**

```swift
let doubleValue: Double = 3.141592653589793
let floatValue: Float = 3.141592653589793

print("Double: \(doubleValue)")  // 3.141592653589793
print("Float: \(floatValue)")    // 3.1415927 (less precise)

// Financial calculation example
let price: Double = 19.99
let quantity: Int = 3
let total: Double = price * Double(quantity)
print("Total: $\(total)")  // Total: $59.97 (precise)

// Temperature example (Float is fine for this)
let tempCelsius: Float = 25.0
let tempFahrenheit: Float = (tempCelsius * 9/5) + 32
print("Temperature: \(tempFahrenheit)°F")  // Temperature: 77.0°F
```

**Floating-point precision issues:**

```swift
// ❌ Floating-point arithmetic can be imprecise
let a: Double = 0.1
let b: Double = 0.2
let c: Double = 0.3
print(a + b == c)  // false! (0.30000000000000004 != 0.3)

// ✅ Use approximate comparison for floating-point
let tolerance: Double = 0.0001
let isEqual = abs((a + b) - c) < tolerance  // true

// ✅ For financial calculations, use Decimal or work in cents
let priceInCents: Int = 1999  // $19.99 in cents
let quantity: Int = 3
let totalInCents: Int = priceInCents * quantity
let totalInDollars: Double = Double(totalInCents) / 100.0
print("Total: $\(totalInDollars)")  // Total: $59.97
```

#### Boolean Type - True or False

The Boolean type is simple but crucial for control flow and logic.

```swift
// Boolean values
let isActive = true
let hasPermission = false
let isLoggedIn: Bool = true
let canEdit: Bool = false

// Boolean expressions
let isAdult = age >= 18
let canVote = isAdult && isCitizen
let needsPermission = !hasPermission
```

**Boolean best practices:**

```swift
// ✅ Good - descriptive names
let isUserLoggedIn = true
let hasValidSubscription = false
let canAccessAdminPanel = false

// ❌ Bad - unclear names
let flag = true
let status = false
let check = true

// ✅ Good - positive names when possible
let isEnabled = true
let hasPermission = true

// ❌ Bad - double negatives
let isNotDisabled = true  // Confusing
let hasNoPermission = false  // Confusing
```

**Boolean logic examples:**

```swift
// User authentication logic
let isUserLoggedIn = true
let hasValidSession = true
let isSessionExpired = false
let hasAdminRole = false

// Complex boolean logic
let canAccessAdmin = isUserLoggedIn && 
                    hasValidSession && 
                    !isSessionExpired && 
                    hasAdminRole

// Using boolean for state management
var isEditing = false
var isSaving = false
var hasUnsavedChanges = false

// State validation
let canSave = !isEditing && !isSaving && hasUnsavedChanges
let canEdit = !isSaving && !hasUnsavedChanges
```

#### String Type - Text Data

Strings in Swift are powerful, Unicode-compliant, and optimized for performance.

```swift
// String literals
let simpleString = "Hello, World!"
let multiLineString = """
    This is a multi-line string.
    It can span multiple lines.
    Perfect for long text or formatting.
    """

// String with special characters
let quote = "He said \"Hello!\""
let path = "C:\\Users\\Name\\Documents"
let unicode = "Hello 🌍 World! 🚀"

// Empty strings
let emptyString = ""
let anotherEmpty = String()
```

**String operations and methods:**

```swift
let text = "Hello, Swift Programming!"

// Basic operations
let length = text.count                    // 25
let isEmpty = text.isEmpty                 // false
let hasPrefix = text.hasPrefix("Hello")    // true
let hasSuffix = text.hasSuffix("!")       // true

// Case conversion
let upper = text.uppercased()             // "HELLO, SWIFT PROGRAMMING!"
let lower = text.lowercased()             // "hello, swift programming!"

// Searching and replacing
let containsSwift = text.contains("Swift") // true
let replaced = text.replacingOccurrences(of: "Swift", with: "Python")
// "Hello, Python Programming!"

// Splitting and joining
let words = text.components(separatedBy: " ")
// ["Hello,", "Swift", "Programming!"]
let joined = words.joined(separator: "-")
// "Hello,-Swift-Programming!"

// Substring operations
let startIndex = text.startIndex
let endIndex = text.endIndex
let firstWord = text[..<text.firstIndex(of: " ")!]
// "Hello,"
```

**String interpolation and formatting:**

```swift
let name = "John"
let age = 25
let score = 95.5

// Basic interpolation
let greeting = "Hello, \(name)!"

// Complex interpolation
let summary = """
    Name: \(name)
    Age: \(age)
    Score: \(String(format: "%.1f", score))
    Grade: \(score >= 90 ? "A" : score >= 80 ? "B" : "C")
    """

// Number formatting
let price = 19.99
let formattedPrice = String(format: "$%.2f", price)  // "$19.99"

let percentage = 0.85
let formattedPercentage = String(format: "%.1f%%", percentage * 100)  // "85.0%"
```

**Real-world string examples:**

```swift
// User input validation
struct UserInputValidator {
    static func validateEmail(_ email: String) -> Bool {
        let emailRegex = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"
        let emailPredicate = NSPredicate(format: "SELF MATCHES %@", emailRegex)
        return emailPredicate.evaluate(with: email)
    }
    
    static func validatePassword(_ password: String) -> (isValid: Bool, errors: [String]) {
        var errors: [String] = []
        
        if password.count < 8 {
            errors.append("Password must be at least 8 characters")
        }
        
        if !password.contains(where: { $0.isNumber }) {
            errors.append("Password must contain at least one number")
        }
        
        if !password.contains(where: { $0.isUppercase }) {
            errors.append("Password must contain at least one uppercase letter")
        }
        
        return (errors.isEmpty, errors)
    }
}

// Usage
let email = "user@example.com"
let password = "MyPassword123"

if UserInputValidator.validateEmail(email) {
    print("Valid email")
} else {
    print("Invalid email")
}

let passwordValidation = UserInputValidator.validatePassword(password)
if passwordValidation.isValid {
    print("Valid password")
} else {
    print("Password errors: \(passwordValidation.errors)")
}
```

**String performance considerations:**

```swift
// ❌ Inefficient - creates new string each iteration
var result = ""
for i in 1...1000 {
    result += "\(i) "  // Creates new string each time
}

// ✅ Efficient - use array then join
var numbers: [String] = []
for i in 1...1000 {
    numbers.append("\(i)")
}
let efficientResult = numbers.joined(separator: " ")

// ✅ Most efficient - use String interpolation for simple cases
let simpleResult = (1...1000).map { "\($0)" }.joined(separator: " ")
```

#### Type Conversion and Coercion

Understanding how Swift handles type conversion is crucial for avoiding runtime errors.

```swift
// Integer to floating-point
let intValue: Int = 42
let doubleValue: Double = Double(intValue)  // 42.0
let floatValue: Float = Float(intValue)     // 42.0

// Floating-point to integer (truncates, doesn't round)
let price: Double = 19.99
let wholePrice: Int = Int(price)  // 19 (truncated)

// String to number
let numberString = "42"
let number = Int(numberString)  // Optional(42)
let invalidString = "not a number"
let invalidNumber = Int(invalidString)  // nil

// Number to string
let age = 25
let ageString = String(age)  // "25"
let formattedAge = "\(age)"  // "25"
```

**Safe type conversion:**

```swift
// ❌ Unsafe - can crash
let userInput = "42"
let age = Int(userInput)!  // Force unwrapping - dangerous

// ✅ Safe - handle nil case
if let age = Int(userInput) {
    print("User age: \(age)")
} else {
    print("Invalid age input")
}

// ✅ Safe with default value
let age = Int(userInput) ?? 0  // Use 0 if conversion fails

// ✅ Safe with error handling
guard let age = Int(userInput) else {
    print("Invalid age input")
    return
}
print("User age: \(age)")
```

#### Type Aliases - Making Types More Readable

Type aliases can make your code more readable and maintainable.

```swift
// Basic type aliases
typealias UserID = String
typealias Score = Int
typealias Price = Double
typealias IsActive = Bool

// Usage
let currentUserID: UserID = "user123"
let userScore: Score = 95
let itemPrice: Price = 19.99
let userStatus: IsActive = true

// Complex type aliases
typealias UserCredentials = (username: String, password: String)
typealias APIResponse<T> = (data: T, statusCode: Int, message: String)
typealias CompletionHandler<T> = (Result<T, Error>) -> Void

// Usage
let credentials: UserCredentials = ("john_doe", "secret123")
let response: APIResponse<[String]> = (["item1", "item2"], 200, "Success")
let completion: CompletionHandler<String> = { result in
    switch result {
    case .success(let data):
        print("Success: \(data)")
    case .failure(let error):
        print("Error: \(error)")
    }
}
```

**Real-world type alias example:**

```swift
// API layer with type aliases
typealias HTTPMethod = String
typealias HTTPHeaders = [String: String]
typealias HTTPStatusCode = Int
typealias JSONData = [String: Any]

struct APIRequest {
    let method: HTTPMethod
    let url: URL
    let headers: HTTPHeaders
    let body: Data?
}

struct APIResponse<T> {
    let data: T
    let statusCode: HTTPStatusCode
    let headers: HTTPHeaders
}

// Usage
let request = APIRequest(
    method: "GET",
    url: URL(string: "https://api.example.com/users")!,
    headers: ["Authorization": "Bearer token123"],
    body: nil
)

let response = APIResponse(
    data: ["user1", "user2"],
    statusCode: 200,
    headers: ["Content-Type": "application/json"]
)
```

#### Best Practices for Type Selection

1. **Use the most specific type** that meets your needs
2. **Prefer `Int` over specific integer types** unless you need size guarantees
3. **Use `Double` for most decimal calculations** unless memory is critical
4. **Use `Bool` for true/false conditions** - avoid using 0/1
5. **Use descriptive names** that indicate the type's purpose
6. **Use type aliases** to make complex types more readable
7. **Handle type conversion safely** - avoid force unwrapping

**Type selection decision tree:**

```
Do you need a whole number?
├─ Yes → Do you need negative numbers?
│  ├─ Yes → Use Int
│  └─ No → Use UInt (be careful with mixing types)
└─ No → Do you need decimal precision?
   ├─ Yes → Is memory critical?
   │  ├─ Yes → Use Float
   │  └─ No → Use Double
   └─ No → Is it true/false?
      ├─ Yes → Use Bool
      └─ No → Use String
```

This comprehensive understanding of Swift's basic types will give you a solid foundation for building robust, efficient applications. The key is choosing the right type for the job and understanding the trade-offs between precision, memory usage, and performance.

---

## Basic Operators

### Arithmetic Operators

```swift
let a = 10
let b = 3

let sum = a + b        // 13
let difference = a - b // 7
let product = a * b    // 30
let quotient = a / b   // 3 (integer division)
let remainder = a % b  // 1

// Compound assignment
var count = 5
count += 1  // count = count + 1
count *= 2  // count = count * 2
```

**When to use:**
- `+`, `-`, `*`, `/` for basic math
- `%` for finding remainders (great for cycles, patterns)
- Compound operators for cleaner code

### Comparison Operators

```swift
let x = 5
let y = 10

let isEqual = x == y        // false
let isNotEqual = x != y     // true
let isLess = x < y          // true
let isGreater = x > y       // false
let isLessOrEqual = x <= y  // true
let isGreaterOrEqual = x >= y // false
```

**When to use:**
- `==` and `!=` for equality checks
- `<`, `>`, `<=`, `>=` for ordering and ranges

### Logical Operators

```swift
let isLoggedIn = true
let hasPermission = false
let isAdmin = true

let canAccess = isLoggedIn && hasPermission  // false
let isSpecialUser = isLoggedIn || isAdmin    // true
let isRegularUser = !isAdmin                 // false
```

**When to use:**
- `&&` when ALL conditions must be true
- `||` when ANY condition can be true
- `!` to negate a boolean value

---

## Strings and Characters

### String Basics

```swift
let firstName = "John"
let lastName = "Doe"

// String concatenation
let fullName = firstName + " " + lastName

// String interpolation (preferred)
let greeting = "Hello, \(firstName) \(lastName)!"

// Multi-line strings
let address = """
    123 Main Street
    New York, NY 10001
    USA
    """
```

**When to use:**
- `+` for simple concatenation
- String interpolation for dynamic content
- Multi-line strings for long text

### String Methods

```swift
let text = "Hello, Swift!"

// Check if contains
let hasHello = text.contains("Hello")  // true

// Split into array
let words = text.components(separatedBy: " ")  // ["Hello,", "Swift!"]

// Replace
let newText = text.replacingOccurrences(of: "Hello", with: "Hi")

// Case conversion
let upperText = text.uppercased()
let lowerText = text.lowercased()
```

**When to use:**
- `contains()` for searching text
- `components()` for parsing CSV or space-separated data
- `replacingOccurrences()` for text replacement
- Case methods for user input normalization

---

## Collection Types

### Arrays

Arrays are Swift's primary ordered collection type. They're incredibly versatile and optimized for performance, making them the go-to choice for most collection needs.

#### Creating and Initializing Arrays

Arrays can be created in several ways, each with different use cases and performance characteristics.

```swift
// Array literals - most common way
var fruits = ["Apple", "Banana", "Orange"]
var numbers: [Int] = [1, 2, 3, 4, 5]
var mixedArray: [Any] = [1, "hello", true, 3.14]

// Empty arrays with type annotation
var emptyStrings: [String] = []
var emptyNumbers = [Int]()
var emptyDoubles: Array<Double> = []

// Arrays with repeated values
let repeatedZeros = Array(repeating: 0, count: 10)  // [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
let repeatedStrings = Array(repeating: "Placeholder", count: 5)

// Arrays from ranges
let rangeArray = Array(1...10)  // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let evenNumbers = Array(stride(from: 0, to: 20, by: 2))  // [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

// Arrays from other collections
let set: Set<String> = ["Red", "Blue", "Green"]
let arrayFromSet = Array(set)  // Order may vary since sets are unordered
```

**When to use each initialization method:**
- **Array literals** for known, fixed collections
- **Empty arrays** when building collections dynamically
- **Repeated values** for initialization with default values
- **Ranges** for sequential numbers
- **From other collections** when converting between collection types

#### Adding and Removing Elements

Understanding how to modify arrays efficiently is crucial for good performance.

```swift
var shoppingList = ["Milk", "Bread", "Eggs"]

// Adding elements
shoppingList.append("Cheese")           // Add to end: ["Milk", "Bread", "Eggs", "Cheese"]
shoppingList.insert("Butter", at: 1)   // Insert at specific index: ["Milk", "Butter", "Bread", "Eggs", "Cheese"]

// Adding multiple elements
shoppingList.append(contentsOf: ["Yogurt", "Juice"])  // Add multiple at end
shoppingList.insert(contentsOf: ["Salt", "Pepper"], at: 0)  // Insert multiple at beginning

// Removing elements
shoppingList.remove(at: 2)             // Remove at specific index: ["Salt", "Pepper", "Milk", "Butter", "Eggs", "Cheese", "Yogurt", "Juice"]
shoppingList.removeFirst()             // Remove first element: ["Pepper", "Milk", "Butter", "Eggs", "Cheese", "Yogurt", "Juice"]
shoppingList.removeLast()              // Remove last element: ["Pepper", "Milk", "Butter", "Eggs", "Cheese", "Yogurt"]

// Removing with conditions
shoppingList.removeAll { $0.count < 4 }  // Remove all items with less than 4 characters
shoppingList.removeAll()                 // Remove all elements (empty array)

// Safe removal (won't crash if index is out of bounds)
if shoppingList.count > 5 {
    shoppingList.remove(at: 5)
}
```

**Performance considerations:**
- **`append`** is O(1) amortized - very fast
- **`insert`** is O(n) - slower for large arrays
- **`remove`** is O(n) - slower for large arrays
- **`removeLast`** is O(1) - very fast
- **`removeFirst`** is O(n) - slower for large arrays

#### Accessing and Modifying Elements

Arrays provide multiple ways to access and modify elements, each with different safety characteristics.

```swift
var scores = [85, 92, 78, 96, 88]

// Basic indexing
let firstScore = scores[0]      // 85
let thirdScore = scores[2]      // 78

// Safe indexing with bounds checking
if scores.count > 3 {
    let fourthScore = scores[3]  // Safe access
}

// Using indices property for safe iteration
for index in scores.indices {
    print("Score \(index + 1): \(scores[index])")
}

// Subscript access with ranges
let firstThree = scores[0..<3]      // [85, 92, 78] (ArraySlice)
let lastThree = scores[2...]        // [78, 96, 88] (ArraySlice)
let middleThree = scores[1...3]     // [92, 78, 96] (ArraySlice)

// Converting ArraySlice back to Array
let firstThreeArray = Array(scores[0..<3])

// Modifying elements
scores[0] = 90                    // Change first score to 90
scores[1...3] = [95, 80, 98]     // Replace middle three scores

// Safe modification
if scores.count > 4 {
    scores[4] = 89                // Safe modification
}
```

**Array safety best practices:**

```swift
// ❌ Dangerous - can crash if index is out of bounds
let score = scores[10]  // Crashes if array has less than 11 elements

// ✅ Safe - check bounds first
if scores.count > 10 {
    let score = scores[10]
}

// ✅ Safe - use optional binding with indices
if let index = scores.indices.last {
    let lastScore = scores[index]
}

// ✅ Safe - use first and last properties
let firstScore = scores.first  // Returns optional
let lastScore = scores.last    // Returns optional

// ✅ Safe - use enumerated for index-value pairs
for (index, score) in scores.enumerated() {
    print("Score \(index + 1): \(score)")
}
```

#### Array Properties and Methods

Arrays come with many useful properties and methods for working with collections.

```swift
let temperatures = [72.5, 68.2, 75.1, 69.8, 71.3]

// Basic properties
let count = temperatures.count           // 5
let isEmpty = temperatures.isEmpty       // false
let capacity = temperatures.capacity     // Current capacity (may be larger than count)

// Accessing elements
let first = temperatures.first           // Optional(72.5)
let last = temperatures.last            // Optional(71.3)
let random = temperatures.randomElement() // Random element (optional)

// Finding elements
let hasHighTemp = temperatures.contains { $0 > 80 }  // false
let firstHighTemp = temperatures.first { $0 > 70 }   // Optional(72.5)
let lastHighTemp = temperatures.last { $0 > 70 }     // Optional(71.3)

// Finding indices
if let index = temperatures.firstIndex(of: 75.1) {
    print("75.1 is at index \(index)")  // 75.1 is at index 2
}

if let index = temperatures.firstIndex(where: { $0 > 70 }) {
    print("First temperature above 70: \(temperatures[index])")  // First temperature above 70: 72.5
}

// Array transformations
let roundedTemps = temperatures.map { round($0) }           // [73.0, 68.0, 75.0, 70.0, 71.0]
let highTemps = temperatures.filter { $0 > 70 }             // [72.5, 75.1, 71.3]
let totalTemp = temperatures.reduce(0, +)                   // 356.9
let avgTemp = temperatures.reduce(0, +) / Double(temperatures.count)  // 71.38

// Sorting
let sortedTemps = temperatures.sorted()                     // [68.2, 69.8, 71.3, 72.5, 75.1]
let reverseSorted = temperatures.sorted(by: >)              // [75.1, 72.5, 71.3, 69.8, 68.2]
```

#### Real-World Array Examples

Let's see how arrays are used in practical applications.

**Example 1: Shopping Cart Management**

```swift
struct ShoppingItem {
    let name: String
    let price: Double
    var quantity: Int
}

class ShoppingCart {
    private var items: [ShoppingItem] = []
    
    func addItem(_ item: ShoppingItem) {
        if let existingIndex = items.firstIndex(where: { $0.name == item.name }) {
            items[existingIndex].quantity += item.quantity
        } else {
            items.append(item)
        }
    }
    
    func removeItem(named name: String) {
        items.removeAll { $0.name == name }
    }
    
    func updateQuantity(for name: String, to quantity: Int) {
        if let index = items.firstIndex(where: { $0.name == name }) {
            if quantity <= 0 {
                items.remove(at: index)
            } else {
                items[index].quantity = quantity
            }
        }
    }
    
    var totalPrice: Double {
        return items.reduce(0) { $0 + ($1.price * Double($1.quantity)) }
    }
    
    var itemCount: Int {
        return items.reduce(0) { $0 + $1.quantity }
    }
    
    func clear() {
        items.removeAll()
    }
    
    func getItems() -> [ShoppingItem] {
        return items
    }
}

// Usage
let cart = ShoppingCart()
cart.addItem(ShoppingItem(name: "Apple", price: 0.50, quantity: 5))
cart.addItem(ShoppingItem(name: "Banana", price: 0.30, quantity: 3))
cart.addItem(ShoppingItem(name: "Apple", price: 0.50, quantity: 2))  // Updates existing

print("Total items: \(cart.itemCount)")      // Total items: 10
print("Total price: $\(cart.totalPrice)")    // Total price: $4.9

cart.updateQuantity(for: "Apple", to: 10)
print("Apple quantity: \(cart.getItems().first { $0.name == "Apple" }?.quantity ?? 0)")
```

**Example 2: High Score Management**

```swift
struct HighScore {
    let playerName: String
    let score: Int
    let date: Date
    
    init(playerName: String, score: Int) {
        self.playerName = playerName
        self.score = score
        self.date = Date()
    }
}

class HighScoreManager {
    private var scores: [HighScore] = []
    private let maxScores = 10
    
    func addScore(_ score: HighScore) {
        scores.append(score)
        scores.sort { $0.score > $1.score }  // Sort by score descending
        
        if scores.count > maxScores {
            scores = Array(scores.prefix(maxScores))  // Keep only top scores
        }
    }
    
    func getTopScores() -> [HighScore] {
        return scores
    }
    
    func getPlayerBestScore(_ playerName: String) -> HighScore? {
        return scores.first { $0.playerName == playerName }
    }
    
    func getPlayerRank(_ playerName: String) -> Int? {
        return scores.firstIndex { $0.playerName == playerName }.map { $0 + 1 }
    }
    
    func isHighScore(_ score: Int) -> Bool {
        return scores.count < maxScores || score > scores.last?.score ?? 0
    }
}

// Usage
let scoreManager = HighScoreManager()

scoreManager.addScore(HighScore(playerName: "Alice", score: 1500))
scoreManager.addScore(HighScore(playerName: "Bob", score: 2200))
scoreManager.addScore(HighScore(playerName: "Charlie", score: 1800))
scoreManager.addScore(HighScore(playerName: "Alice", score: 1600))  // Better score

let topScores = scoreManager.getTopScores()
for (index, score) in topScores.enumerated() {
    print("\(index + 1). \(score.playerName): \(score.score)")
}

if let aliceRank = scoreManager.getPlayerRank("Alice") {
    print("Alice's rank: #\(aliceRank)")
}
```

#### Array Performance and Memory Management

Understanding how arrays work internally helps you write more efficient code.

```swift
// Array capacity management
var dynamicArray: [Int] = []
print("Initial capacity: \(dynamicArray.capacity)")  // Usually 0

for i in 1...10 {
    dynamicArray.append(i)
    print("Count: \(dynamicArray.count), Capacity: \(dynamicArray.capacity)")
}

// Reserve capacity for better performance
var optimizedArray: [Int] = []
optimizedArray.reserveCapacity(1000)  // Pre-allocate space for 1000 elements

// This prevents multiple reallocations when adding many elements
for i in 1...1000 {
    optimizedArray.append(i)
}
```

**Performance tips:**

1. **Use `reserveCapacity`** when you know the final size
2. **Prefer `append` over `insert`** when possible
3. **Use `removeLast` instead of `remove(at: count-1)`**
4. **Avoid frequent array modifications** in performance-critical code
5. **Use `ArraySlice`** for temporary views without copying

#### Common Array Patterns

```swift
// Pattern 1: Building arrays conditionally
var filteredItems: [String] = []
for item in allItems {
    if item.isValid && item.isAvailable {
        filteredItems.append(item.name)
    }
}

// Pattern 2: Transforming arrays
let numbers = [1, 2, 3, 4, 5]
let doubled = numbers.map { $0 * 2 }
let evenNumbers = numbers.filter { $0 % 2 == 0 }
let sum = numbers.reduce(0, +)

// Pattern 3: Combining arrays
let array1 = [1, 2, 3]
let array2 = [4, 5, 6]
let combined = array1 + array2  // [1, 2, 3, 4, 5, 6]

// Pattern 4: Removing duplicates while preserving order
extension Array where Element: Hashable {
    func removingDuplicates() -> [Element] {
        var seen = Set<Element>()
        return filter { seen.insert($0).inserted }
    }
}

let duplicates = [1, 2, 2, 3, 3, 4]
let unique = duplicates.removingDuplicates()  // [1, 2, 3, 4]
```

#### Best Practices for Arrays

1. **Choose the right initialization method** for your use case
2. **Use bounds checking** to avoid crashes
3. **Prefer `first` and `last` properties** over indexing when possible
4. **Use `reserveCapacity`** when you know the final size
5. **Choose the right modification method** based on performance needs
6. **Use functional methods** (`map`, `filter`, `reduce`) for cleaner code
7. **Consider using `ArraySlice`** for temporary views
8. **Handle empty arrays gracefully** in your code

Arrays are one of Swift's most powerful and commonly used types. Mastering them will give you a solid foundation for working with collections and building efficient applications.

### Dictionaries

Dictionaries are Swift's key-value collection type, providing fast lookups by unique keys. They're essential for mapping relationships between data and are widely used in APIs, configuration, and data modeling.

#### Understanding Dictionary Fundamentals

Dictionaries store pairs of keys and values, where each key must be unique within the dictionary. The key type must conform to the `Hashable` protocol, which allows Swift to efficiently locate values.

```swift
// Basic dictionary creation
var userInfo: [String: Any] = [
    "name": "John",
    "age": 25,
    "email": "john@example.com"
]

// Type-safe dictionaries (preferred when possible)
var userScores: [String: Int] = [
    "Alice": 95,
    "Bob": 87,
    "Charlie": 92
]

var userSettings: [String: Bool] = [
    "notifications": true,
    "darkMode": false,
    "autoSave": true
]

// Empty dictionaries
var emptyDict: [String: Int] = [:]
var anotherEmpty = Dictionary<String, Double>()
```

**Why use type-safe dictionaries:**
- **Compile-time safety** - Swift catches type mismatches
- **Better performance** - No runtime type checking needed
- **Cleaner code** - No need for type casting
- **IDE support** - Better autocomplete and error detection

#### Dictionary Operations - CRUD Operations

Understanding how to create, read, update, and delete dictionary entries is fundamental to working with dictionaries effectively.

```swift
var inventory: [String: Int] = [
    "apples": 50,
    "bananas": 30,
    "oranges": 25
]

// CREATE - Adding new key-value pairs
inventory["grapes"] = 40
inventory["pears"] = 15

// READ - Accessing values
let appleCount = inventory["apples"]  // Optional(50)
let grapeCount = inventory["grapes"]  // Optional(40)
let missingItem = inventory["mango"]  // nil

// Safe access with default values
let appleCountSafe = inventory["apples"] ?? 0  // 50 (or 0 if key doesn't exist)
let mangoCountSafe = inventory["mango"] ?? 0   // 0 (key doesn't exist)

// UPDATE - Modifying existing values
inventory["apples"] = 45        // Direct assignment
inventory["bananas"]? += 5      // Optional chaining (only if key exists)

// Safe update with nil check
if let currentCount = inventory["oranges"] {
    inventory["oranges"] = currentCount + 10
}

// DELETE - Removing key-value pairs
inventory.removeValue(forKey: "pears")  // Returns removed value (Optional(15))
inventory["grapes"] = nil               // Sets value to nil (effectively removes)

// Check if key exists
let hasApples = inventory["apples"] != nil  // true
let hasMango = inventory["mango"] != nil    // false
```

**Safe dictionary access patterns:**

```swift
// Pattern 1: Optional binding for safe access
if let count = inventory["apples"] {
    print("We have \(count) apples")
} else {
    print("No apples in inventory")
}

// Pattern 2: Guard statement for early return
guard let count = inventory["bananas"] else {
    print("Bananas not found in inventory")
    return
}
print("Banana count: \(count)")

// Pattern 3: Nil coalescing for default values
let count = inventory["oranges"] ?? 0
print("Orange count: \(count)")

// Pattern 4: Dictionary subscript with default
let count = inventory["mango", default: 0]  // Returns 0 if key doesn't exist
```

#### Dictionary Properties and Methods

Dictionaries provide many useful properties and methods for working with key-value data.

```swift
var userPreferences: [String: Any] = [
    "theme": "dark",
    "language": "en",
    "notifications": true,
    "autoSave": false,
    "fontSize": 14
]

// Basic properties
let count = userPreferences.count           // 5
let isEmpty = userPreferences.isEmpty       // false
let keys = Array(userPreferences.keys)     // ["theme", "language", "notifications", "autoSave", "fontSize"]
let values = Array(userPreferences.values) // ["dark", "en", true, false, 14]

// Checking for keys
let hasTheme = userPreferences.keys.contains("theme")           // true
let hasDarkMode = userPreferences.keys.contains("darkMode")     // false

// Checking for values
let hasDarkTheme = userPreferences.values.contains("dark")      // true
let hasLargeFont = userPreferences.values.contains(where: { 
    if let fontSize = $0 as? Int { return fontSize > 12 }
    return false
})  // true

// Dictionary capacity and performance
let capacity = userPreferences.capacity     // Current capacity (may be larger than count)
userPreferences.reserveCapacity(100)       // Pre-allocate space for better performance
```

#### Advanced Dictionary Operations

Swift provides powerful methods for transforming and combining dictionaries.

```swift
var scores1: [String: Int] = ["Alice": 95, "Bob": 87, "Charlie": 92]
var scores2: [String: Int] = ["David": 88, "Eve": 91, "Alice": 98]

// Merging dictionaries
scores1.merge(scores2) { current, new in
    // In case of duplicate keys, keep the higher score
    return max(current, new)
}
// Result: ["Alice": 98, "Bob": 87, "Charlie": 92, "David": 88, "Eve": 91]

// Creating new dictionary by merging
let combinedScores = scores1.merging(scores2) { current, new in
    return max(current, new)
}

// Filtering dictionaries
let highScores = scores1.filter { $0.value >= 90 }
// Result: ["Alice": 98, "Charlie": 92]

// Transforming dictionaries
let scoreDescriptions = scores1.mapValues { score in
    switch score {
    case 90...: return "Excellent"
    case 80..<90: return "Good"
    case 70..<80: return "Average"
    default: return "Needs Improvement"
    }
}
// Result: ["Alice": "Excellent", "Bob": "Good", "Charlie": "Excellent"]

// Reducing dictionaries
let totalScore = scores1.values.reduce(0, +)  // 277
let averageScore = Double(totalScore) / Double(scores1.count)  // 92.33
```

#### Real-World Dictionary Examples

Let's see how dictionaries are used in practical applications.

**Example 1: User Configuration Management**

```swift
struct UserConfig {
    private var settings: [String: Any] = [:]
    
    // Default configuration
    init() {
        settings = [
            "theme": "light",
            "language": "en",
            "notifications": true,
            "autoSave": true,
            "fontSize": 14,
            "soundEnabled": true,
            "privacyLevel": "standard"
        ]
    }
    
    // Type-safe getters with defaults
    var theme: String {
        get { return settings["theme"] as? String ?? "light" }
        set { settings["theme"] = newValue }
    }
    
    var fontSize: Int {
        get { return settings["fontSize"] as? Int ?? 14 }
        set { 
            let clampedValue = max(8, min(24, newValue))  // Clamp between 8-24
            settings["fontSize"] = clampedValue 
        }
    }
    
    var notifications: Bool {
        get { return settings["notifications"] as? Bool ?? true }
        set { settings["notifications"] = newValue }
    }
    
    // Bulk operations
    func updateSettings(_ newSettings: [String: Any]) {
        for (key, value) in newSettings {
            if isValidValue(value, forKey: key) {
                settings[key] = value
            }
        }
    }
    
    func resetToDefaults() {
        settings = [
            "theme": "light",
            "language": "en",
            "notifications": true,
            "autoSave": true,
            "fontSize": 14,
            "soundEnabled": true,
            "privacyLevel": "standard"
        ]
    }
    
    func exportSettings() -> [String: Any] {
        return settings
    }
    
    private func isValidValue(_ value: Any, forKey key: String) -> Bool {
        switch key {
        case "fontSize":
            if let intValue = value as? Int {
                return intValue >= 8 && intValue <= 24
            }
            return false
        case "theme":
            if let stringValue = value as? String {
                return ["light", "dark", "auto"].contains(stringValue)
            }
            return false
        case "language":
            if let stringValue = value as? String {
                return ["en", "es", "fr", "de"].contains(stringValue)
            }
            return false
        default:
            return true
        }
    }
}

// Usage
var userConfig = UserConfig()
print("Current theme: \(userConfig.theme)")  // Current theme: light

userConfig.theme = "dark"
userConfig.fontSize = 18
print("Updated theme: \(userConfig.theme), Font size: \(userConfig.fontSize)")

// Bulk update
userConfig.updateSettings([
    "theme": "auto",
    "notifications": false,
    "soundEnabled": false
])

// Export for saving
let exportedSettings = userConfig.exportSettings()
print("Exported settings: \(exportedSettings)")
```

**Example 2: Shopping Cart with Product Catalog**

```swift
struct Product {
    let id: String
    let name: String
    let price: Double
    let category: String
    let inStock: Bool
}

class ProductCatalog {
    private var products: [String: Product] = [:]
    
    func addProduct(_ product: Product) {
        products[product.id] = product
    }
    
    func getProduct(by id: String) -> Product? {
        return products[id]
    }
    
    func searchProducts(by category: String) -> [Product] {
        return products.values.filter { $0.category == category }
    }
    
    func getProductsInStock() -> [Product] {
        return products.values.filter { $0.inStock }
    }
    
    func updateStock(for productId: String, inStock: Bool) {
        if var product = products[productId] {
            // Note: This would need Product to be a class or have mutating methods
            // For this example, we'll just update the dictionary
            products[productId] = Product(
                id: product.id,
                name: product.name,
                price: product.price,
                category: product.category,
                inStock: inStock
            )
        }
    }
}

class ShoppingCart {
    private var items: [String: Int] = [:]  // Product ID -> Quantity
    private let catalog: ProductCatalog
    
    init(catalog: ProductCatalog) {
        self.catalog = catalog
    }
    
    func addItem(productId: String, quantity: Int = 1) {
        guard let product = catalog.getProduct(by: productId), product.inStock else {
            print("Product not available or out of stock")
            return
        }
        
        let currentQuantity = items[productId] ?? 0
        items[productId] = currentQuantity + quantity
        print("Added \(quantity) \(product.name) to cart")
    }
    
    func removeItem(productId: String, quantity: Int = 1) {
        guard let currentQuantity = items[productId] else { return }
        
        let newQuantity = currentQuantity - quantity
        if newQuantity <= 0 {
            items.removeValue(forKey: productId)
            print("Removed \(productId) from cart")
        } else {
            items[productId] = newQuantity
            print("Updated quantity for \(productId) to \(newQuantity)")
        }
    }
    
    func getCartContents() -> [(Product, Int)] {
        var contents: [(Product, Int)] = []
        
        for (productId, quantity) in items {
            if let product = catalog.getProduct(by: productId) {
                contents.append((product, quantity))
            }
        }
        
        return contents
    }
    
    func getTotalPrice() -> Double {
        return items.reduce(0) { total, item in
            let (productId, quantity) = item
            let product = catalog.getProduct(by: productId)
            return total + (product?.price ?? 0) * Double(quantity)
        }
    }
    
    func clearCart() {
        items.removeAll()
    }
    
    func getItemCount() -> Int {
        return items.values.reduce(0, +)
    }
}

// Usage
let catalog = ProductCatalog()

// Add products to catalog
catalog.addProduct(Product(id: "1", name: "iPhone", price: 999.99, category: "Electronics", inStock: true))
catalog.addProduct(Product(id: "2", name: "MacBook", price: 1299.99, category: "Electronics", inStock: true))
catalog.addProduct(Product(id: "3", name: "Coffee Mug", price: 12.99, category: "Kitchen", inStock: true))
catalog.addProduct(Product(id: "4", name: "Desk Lamp", price: 45.99, category: "Home", inStock: false))

let cart = ShoppingCart(catalog: catalog)

// Add items to cart
cart.addItem(productId: "1", quantity: 2)  // 2 iPhones
cart.addItem(productId: "3", quantity: 1)  // 1 Coffee Mug

// View cart contents
let contents = cart.getCartContents()
for (product, quantity) in contents {
    print("\(product.name) x\(quantity) - $\(product.price * Double(quantity))")
}

print("Total items: \(cart.getItemCount())")
print("Total price: $\(cart.getTotalPrice())")

// Remove items
cart.removeItem(productId: "1", quantity: 1)  // Remove 1 iPhone
print("Updated total: $\(cart.getTotalPrice())")
```

#### Dictionary Performance and Memory Considerations

Understanding how dictionaries work internally helps you write more efficient code.

```swift
// Dictionary capacity management
var performanceDict: [String: Int] = [:]
print("Initial capacity: \(performanceDict.capacity)")  // Usually 0

for i in 1...100 {
    performanceDict["key\(i)"] = i
    if i % 20 == 0 {
        print("Count: \(performanceDict.count), Capacity: \(performanceDict.capacity)")
    }
}

// Reserve capacity for better performance
var optimizedDict: [String: Int] = [:]
optimizedDict.reserveCapacity(1000)  // Pre-allocate space

// This prevents multiple reallocations when adding many elements
for i in 1...1000 {
    optimizedDict["key\(i)"] = i
}
```

**Performance characteristics:**

- **Lookup**: O(1) average case - very fast
- **Insertion**: O(1) average case - very fast
- **Deletion**: O(1) average case - very fast
- **Memory**: Uses hash tables internally, so some overhead for small dictionaries
- **Key requirements**: Keys must be `Hashable` for O(1) performance

#### Common Dictionary Patterns

```swift
// Pattern 1: Counting occurrences
let words = ["apple", "banana", "apple", "cherry", "banana", "apple"]
var wordCounts: [String: Int] = [:]

for word in words {
    wordCounts[word, default: 0] += 1
}
// Result: ["apple": 3, "banana": 2, "cherry": 1]

// Pattern 2: Grouping by property
struct Person {
    let name: String
    let age: Int
    let city: String
}

let people = [
    Person(name: "Alice", age: 25, city: "New York"),
    Person(name: "Bob", age: 30, city: "Los Angeles"),
    Person(name: "Charlie", age: 25, city: "New York"),
    Person(name: "David", age: 35, city: "Los Angeles")
]

var peopleByCity: [String: [Person]] = [:]
for person in people {
    peopleByCity[person.city, default: []].append(person)
}

// Pattern 3: Default values for missing keys
var userPreferences: [String: String] = [:]
let theme = userPreferences["theme", default: "light"]
let language = userPreferences["language", default: "en"]

// Pattern 4: Dictionary as function cache
var fibonacciCache: [Int: Int] = [0: 0, 1: 1]

func fibonacci(_ n: Int) -> Int {
    if let cached = fibonacciCache[n] {
        return cached
    }
    
    let result = fibonacci(n - 1) + fibonacci(n - 2)
    fibonacciCache[n] = result
    return result
}

// Pattern 5: Dictionary as configuration
struct AppConfig {
    static let defaults: [String: Any] = [
        "apiBaseURL": "https://api.myapp.com",
        "timeout": 30.0,
        "maxRetries": 3,
        "debugMode": false,
        "logLevel": "info"
    ]
    
    static func getValue<T>(for key: String, defaultValue: T) -> T {
        return defaults[key] as? T ?? defaultValue
    }
}

let apiURL: String = AppConfig.getValue(for: "apiBaseURL", defaultValue: "https://default.com")
let timeout: Double = AppConfig.getValue(for: "timeout", defaultValue: 60.0)
```

#### Best Practices for Dictionaries

1. **Use type-safe dictionaries** when possible to avoid runtime errors
2. **Provide default values** for missing keys using nil coalescing
3. **Use `reserveCapacity`** when you know the final size
4. **Choose appropriate key types** that are `Hashable` and meaningful
5. **Handle missing keys gracefully** with optional binding or default values
6. **Use dictionary methods** like `mapValues`, `filter`, and `reduce` for transformations
7. **Consider memory usage** for very large dictionaries
8. **Use dictionaries for fast lookups** rather than arrays when searching by key

**When to use dictionaries vs other collections:**

- **Use dictionaries when:**
  - You need fast lookups by unique keys
  - You're mapping relationships between data
  - You need to associate metadata with items
  - You're building caches or lookup tables

- **Use arrays when:**
  - You need ordered collections
  - You're iterating through all items
  - You need index-based access
  - You're building lists or queues

- **Use sets when:**
  - You need unique, unordered collections
  - You're checking for membership
  - You need set operations (union, intersection, etc.)

Dictionaries are incredibly powerful tools in Swift, providing fast, flexible data storage that's essential for most applications. Understanding their performance characteristics and best practices will help you build efficient, maintainable code.

### Sets

Sets are Swift's unordered collection type that guarantees uniqueness of elements. They're built on hash tables, providing extremely fast membership testing and set operations, making them ideal for scenarios where you need to ensure no duplicates and perform set mathematics.

#### Understanding Set Fundamentals

Sets store unique elements where each element must conform to the `Hashable` protocol. This allows Swift to efficiently determine membership and perform set operations.

```swift
// Basic set creation
var uniqueNumbers: Set<Int> = [1, 2, 3, 4, 5]
var colors: Set<String> = ["Red", "Blue", "Green"]
var mixedTypes: Set<AnyHashable> = ["Hello", 42, true]

// Empty sets with type annotation
var emptyIntSet: Set<Int> = []
var emptyStringSet = Set<String>()
var emptyDoubleSet: Set<Double> = []

// Sets from arrays (automatically removes duplicates)
let duplicateNumbers = [1, 2, 2, 3, 3, 4, 4, 5]
let uniqueSet = Set(duplicateNumbers)  // [1, 2, 3, 4, 5]

// Sets from ranges
let rangeSet = Set(1...10)  // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
let evenSet = Set(stride(from: 0, to: 20, by: 2))  // [0, 2, 4, 6, 8, 10, 12, 14, 16, 18]
```

**Why use sets:**
- **Uniqueness guarantee** - No duplicate elements possible
- **Fast membership testing** - O(1) average case for `contains`
- **Set operations** - Union, intersection, difference, etc.
- **Memory efficient** - No duplicate storage
- **Mathematical correctness** - Follows set theory principles

#### Set Operations - Adding, Removing, and Testing

Understanding how to modify sets and test membership is fundamental to working with sets effectively.

```swift
var programmingLanguages: Set<String> = ["Swift", "Python", "JavaScript"]

// Adding elements
programmingLanguages.insert("Java")           // Add single element
programmingLanguages.insert("Swift")          // No effect (already exists)
programmingLanguages.insert("C++")            // Add another element

// Adding multiple elements
programmingLanguages.insert("Go")             // Add one more
programmingLanguages.insert("Rust")           // Add one more

// Removing elements
let removedLanguage = programmingLanguages.remove("Python")  // Returns "Python" (optional)
programmingLanguages.remove("JavaScript")                    // Returns "JavaScript" (optional)
programmingLanguages.remove("NonExistent")                  // Returns nil

// Safe removal with nil check
if let removed = programmingLanguages.remove("C++") {
    print("Removed: \(removed)")
} else {
    print("Language not found")
}

// Testing membership
let knowsSwift = programmingLanguages.contains("Swift")      // true
let knowsPython = programmingLanguages.contains("Python")    // false (was removed)

// Checking if set is empty
let isEmpty = programmingLanguages.isEmpty                   // false
let count = programmingLanguages.count                       // Current count
```

**Set membership testing patterns:**

```swift
// Pattern 1: Direct membership test
if programmingLanguages.contains("Swift") {
    print("Swift is in the set")
}

// Pattern 2: Membership test with action
if programmingLanguages.contains("Rust") {
    print("Rust is a modern language")
} else {
    print("Rust not found in set")
}

// Pattern 3: Using contains with closures
let hasModernLanguage = programmingLanguages.contains { language in
    ["Rust", "Go", "Swift"].contains(language)
}

// Pattern 4: Checking multiple conditions
let hasMultipleLanguages = programmingLanguages.count >= 3
let hasSpecificLanguage = programmingLanguages.contains("Swift") && programmingLanguages.contains("Rust")
```

#### Set Properties and Methods

Sets provide many useful properties and methods for working with unique collections.

```swift
var frontendTechnologies: Set<String> = ["HTML", "CSS", "JavaScript", "React", "Vue", "Angular"]
var backendTechnologies: Set<String> = ["Node.js", "Python", "Java", "Go", "Rust"]

// Basic properties
let frontendCount = frontendTechnologies.count           // 6
let backendCount = backendTechnologies.count             // 5
let frontendEmpty = frontendTechnologies.isEmpty         // false

// Accessing elements
let firstElement = frontendTechnologies.first             // Random element (sets are unordered)
let randomElement = frontendTechnologies.randomElement()  // Random element (optional)

// Converting to arrays
let frontendArray = Array(frontendTechnologies)          // Convert to array (order may vary)
let sortedFrontend = frontendTechnologies.sorted()       // Convert to sorted array

// Checking relationships
let isSubset = ["HTML", "CSS"].allSatisfy { frontendTechnologies.contains($0) }  // true
let isSuperset = frontendTechnologies.contains("HTML") && frontendTechnologies.contains("CSS")  // true

// Set capacity and performance
let capacity = frontendTechnologies.capacity              // Current capacity
frontendTechnologies.reserveCapacity(100)                // Pre-allocate space
```

#### Set Operations - Mathematical Set Theory

Sets excel at mathematical operations, making them perfect for data analysis and algorithm implementation.

```swift
let setA: Set<Int> = [1, 2, 3, 4, 5]
let setB: Set<Int> = [4, 5, 6, 7, 8]

// Union - all elements from both sets
let union = setA.union(setB)                    // [1, 2, 3, 4, 5, 6, 7, 8]
let unionInPlace = setA.union(setB)             // Creates new set

// Intersection - elements common to both sets
let intersection = setA.intersection(setB)       // [4, 5]
let intersectionInPlace = setA.intersection(setB) // Creates new set

// Difference - elements in setA but not in setB
let difference = setA.subtracting(setB)         // [1, 2, 3]
let differenceInPlace = setA.subtracting(setB)   // Creates new set

// Symmetric difference - elements in either set but not both
let symmetricDifference = setA.symmetricDifference(setB)  // [1, 2, 3, 6, 7, 8]

// Subset and superset testing
let isSubset = setA.isSubset(of: union)         // true
let isSuperset = union.isSuperset(of: setA)     // true
let isStrictSubset = setA.isStrictSubset(of: union)  // true (not equal)
let isStrictSuperset = union.isStrictSuperset(of: setA)  // true (not equal)

// Disjoint testing
let isDisjoint = setA.isDisjoint(with: setB)    // false (they share elements)
let disjointSet: Set<Int> = [9, 10, 11]
let isDisjointWith = setA.isDisjoint(with: disjointSet)  // true
```

**Advanced set operations:**

```swift
// Combining multiple sets
let set1: Set<Int> = [1, 2, 3]
let set2: Set<Int> = [3, 4, 5]
let set3: Set<Int> = [5, 6, 7]

// Union of multiple sets
let allSets = set1.union(set2).union(set3)     // [1, 2, 3, 4, 5, 6, 7]

// Intersection of multiple sets
let commonElements = set1.intersection(set2).intersection(set3)  // [5]

// Using reduce for complex operations
let allNumbers = [set1, set2, set3].reduce(Set<Int>()) { result, set in
    result.union(set)
}

// Filtering sets
let evenNumbers = setA.filter { $0 % 2 == 0 }  // [2, 4]
let largeNumbers = setA.filter { $0 > 3 }      // [4, 5]

// Transforming sets
let doubledNumbers = Set(setA.map { $0 * 2 })  // [2, 4, 6, 8, 10]
```

#### Real-World Set Examples

Let's see how sets are used in practical applications.

**Example 1: User Permission System**

```swift
enum Permission: String, CaseIterable {
    case read = "read"
    case write = "write"
    case delete = "delete"
    case admin = "admin"
    case moderate = "moderate"
    case invite = "invite"
}

struct User {
    let id: String
    let name: String
    var permissions: Set<Permission>
    
    init(id: String, name: String, permissions: Set<Permission> = []) {
        self.id = id
        self.name = name
        self.permissions = permissions
    }
    
    func hasPermission(_ permission: Permission) -> Bool {
        return permissions.contains(permission)
    }
    
    func hasAnyPermission(_ permissions: Set<Permission>) -> Bool {
        return !self.permissions.isDisjoint(with: permissions)
    }
    
    func hasAllPermissions(_ permissions: Set<Permission>) -> Bool {
        return permissions.isSubset(of: self.permissions)
    }
    
    mutating func grantPermission(_ permission: Permission) {
        permissions.insert(permission)
    }
    
    mutating func revokePermission(_ permission: Permission) {
        permissions.remove(permission)
    }
    
    mutating func grantPermissions(_ permissions: Set<Permission>) {
        self.permissions.formUnion(permissions)
    }
    
    mutating func revokePermissions(_ permissions: Set<Permission>) {
        self.permissions.subtract(permissions)
    }
}

class PermissionManager {
    private var users: [String: User] = [:]
    
    func addUser(_ user: User) {
        users[user.id] = user
    }
    
    func getUser(id: String) -> User? {
        return users[id]
    }
    
    func usersWithPermission(_ permission: Permission) -> [User] {
        return users.values.filter { $0.hasPermission(permission) }
    }
    
    func usersWithAnyPermission(_ permissions: Set<Permission>) -> [User] {
        return users.values.filter { $0.hasAnyPermission(permissions) }
    }
    
    func usersWithAllPermissions(_ permissions: Set<Permission>) -> [User] {
        return users.values.filter { $0.hasAllPermissions(permissions) }
    }
    
    func grantPermissionToUser(_ userId: String, _ permission: Permission) {
        users[userId]?.grantPermission(permission)
    }
    
    func revokePermissionFromUser(_ userId: String, _ permission: Permission) {
        users[userId]?.revokePermission(permission)
    }
}

// Usage
let permissionManager = PermissionManager()

// Create users with different permission levels
let admin = User(id: "admin1", name: "Super Admin", permissions: Set(Permission.allCases))
let moderator = User(id: "mod1", name: "Moderator", permissions: [.read, .moderate, .invite])
let regularUser = User(id: "user1", name: "Regular User", permissions: [.read])
let editor = User(id: "editor1", name: "Content Editor", permissions: [.read, .write])

permissionManager.addUser(admin)
permissionManager.addUser(moderator)
permissionManager.addUser(regularUser)
permissionManager.addUser(editor)

// Check permissions
let canWrite = editor.hasPermission(.write)  // true
let canDelete = editor.hasPermission(.delete)  // false

// Find users with specific permissions
let usersWhoCanModerate = permissionManager.usersWithPermission(.moderate)
let usersWhoCanWrite = permissionManager.usersWithPermission(.write)

// Complex permission checks
let contentPermissions: Set<Permission> = [.read, .write]
let usersWhoCanHandleContent = permissionManager.usersWithAllPermissions(contentPermissions)

// Grant new permissions
permissionManager.grantPermissionToUser("user1", .write)
let updatedUser = permissionManager.getUser(id: "user1")
print("User can now write: \(updatedUser?.hasPermission(.write) ?? false)")
```

**Example 2: Tag Management System**

```swift
struct Article {
    let id: String
    let title: String
    let content: String
    var tags: Set<String>
    
    init(id: String, title: String, content: String, tags: Set<String> = []) {
        self.id = id
        self.title = title
        self.content = content
        self.tags = tags
    }
}

class TagManager {
    private var articles: [String: Article] = [:]
    private var tagIndex: [String: Set<String>] = [:]  // Tag -> Article IDs
    
    func addArticle(_ article: Article) {
        articles[article.id] = article
        
        // Update tag index
        for tag in article.tags {
            tagIndex[tag, default: Set()].insert(article.id)
        }
    }
    
    func removeArticle(_ articleId: String) {
        guard let article = articles[articleId] else { return }
        
        // Remove from tag index
        for tag in article.tags {
            tagIndex[tag]?.remove(articleId)
            if tagIndex[tag]?.isEmpty == true {
                tagIndex.removeValue(forKey: tag)
            }
        }
        
        articles.removeValue(forKey: articleId)
    }
    
    func updateArticleTags(_ articleId: String, newTags: Set<String>) {
        guard let article = articles[articleId] else { return }
        
        let oldTags = article.tags
        let addedTags = newTags.subtracting(oldTags)
        let removedTags = oldTags.subtracting(newTags)
        
        // Remove old tags from index
        for tag in removedTags {
            tagIndex[tag]?.remove(articleId)
            if tagIndex[tag]?.isEmpty == true {
                tagIndex.removeValue(forKey: tag)
            }
        }
        
        // Add new tags to index
        for tag in addedTags {
            tagIndex[tag, default: Set()].insert(articleId)
        }
        
        // Update article
        var updatedArticle = article
        updatedArticle.tags = newTags
        articles[articleId] = updatedArticle
    }
    
    func articlesWithTag(_ tag: String) -> [Article] {
        guard let articleIds = tagIndex[tag] else { return [] }
        return articleIds.compactMap { articles[$0] }
    }
    
    func articlesWithTags(_ tags: Set<String>) -> [Article] {
        guard !tags.isEmpty else { return [] }
        
        // Find articles that have ALL the specified tags
        let articleIds = tags.reduce(Set(articles.keys)) { result, tag in
            result.intersection(tagIndex[tag] ?? Set())
        }
        
        return articleIds.compactMap { articles[$0] }
    }
    
    func articlesWithAnyTag(_ tags: Set<String>) -> [Article] {
        guard !tags.isEmpty else { return [] }
        
        // Find articles that have ANY of the specified tags
        let articleIds = tags.reduce(Set<String>()) { result, tag in
            result.union(tagIndex[tag] ?? Set())
        }
        
        return articleIds.compactMap { articles[$0] }
    }
    
    func relatedTags(for tag: String) -> Set<String> {
        guard let articleIds = tagIndex[tag] else { return Set() }
        
        var relatedTags = Set<String>()
        for articleId in articleIds {
            if let article = articles[articleId] {
                relatedTags.formUnion(article.tags)
            }
        }
        
        relatedTags.remove(tag)  // Remove the original tag
        return relatedTags
    }
    
    func getAllTags() -> Set<String> {
        return Set(tagIndex.keys)
    }
    
    func getPopularTags(limit: Int = 10) -> [(tag: String, count: Int)] {
        return tagIndex.map { (tag: $0.key, count: $0.value.count) }
            .sorted { $0.count > $1.count }
            .prefix(limit)
            .map { (tag: $0.tag, count: $0.count) }
    }
}

// Usage
let tagManager = TagManager()

// Add articles with tags
let article1 = Article(id: "1", title: "Swift Programming", content: "...", tags: ["swift", "programming", "ios"])
let article2 = Article(id: "2", title: "iOS Development", content: "...", tags: ["ios", "development", "swift"])
let article3 = Article(id: "3", title: "Web Development", content: "...", tags: ["web", "development", "javascript"])

tagManager.addArticle(article1)
tagManager.addArticle(article2)
tagManager.addArticle(article3)

// Find articles by tags
let swiftArticles = tagManager.articlesWithTag("swift")
let developmentArticles = tagManager.articlesWithTag("development")

// Find articles with multiple tags
let iosSwiftArticles = tagManager.articlesWithTags(["ios", "swift"])

// Find articles with any of several tags
let anyDevelopmentArticles = tagManager.articlesWithAnyTag(["ios", "web"])

// Get related tags
let relatedToSwift = tagManager.relatedTags(for: "swift")

// Get popular tags
let popularTags = tagManager.getPopularTags(limit: 5)

// Update article tags
tagManager.updateArticleTags("1", newTags: ["swift", "programming", "ios", "beginner"])

// Remove article
tagManager.removeArticle("3")
```

#### Set Performance and Memory Considerations

Understanding how sets work internally helps you write more efficient code.

```swift
// Set capacity management
var performanceSet: Set<Int> = []
print("Initial capacity: \(performanceSet.capacity)")  // Usually 0

for i in 1...100 {
    performanceSet.insert(i)
    if i % 20 == 0 {
        print("Count: \(performanceSet.count), Capacity: \(performanceSet.capacity)")
    }
}

// Reserve capacity for better performance
var optimizedSet: Set<Int> = []
optimizedSet.reserveCapacity(1000)  // Pre-allocate space

// This prevents multiple reallocations when adding many elements
for i in 1...1000 {
    optimizedSet.insert(i)
}
```

**Performance characteristics:**

- **Membership testing**: O(1) average case - extremely fast
- **Insertion**: O(1) average case - very fast
- **Deletion**: O(1) average case - very fast
- **Set operations**: O(n + m) where n and m are set sizes
- **Memory**: Uses hash tables internally, some overhead for small sets
- **Element requirements**: Elements must be `Hashable` for O(1) performance

#### Common Set Patterns

```swift
// Pattern 1: Removing duplicates from arrays
let duplicateItems = ["apple", "banana", "apple", "cherry", "banana"]
let uniqueItems = Array(Set(duplicateItems))  // Order may vary

// Pattern 2: Finding common elements
let user1Interests = Set(["swift", "ios", "programming"])
let user2Interests = Set(["swift", "android", "programming"])
let commonInterests = user1Interests.intersection(user2Interests)  // ["swift", "programming"]

// Pattern 3: Finding unique elements
let allInterests = user1Interests.union(user2Interests)  // All unique interests
let uniqueToUser1 = user1Interests.subtracting(user2Interests)  // Only in user1
let uniqueToUser2 = user2Interests.subtracting(user1Interests)  // Only in user2

// Pattern 4: Set as a cache for expensive operations
var expensiveOperationCache: Set<String> = []

func expensiveOperation(_ input: String) -> String {
    if expensiveOperationCache.contains(input) {
        return "Cached result for \(input)"
    }
    
    // Perform expensive operation
    let result = "Expensive result for \(input)"
    expensiveOperationCache.insert(input)
    return result
}

// Pattern 5: Set for tracking state
var completedTasks: Set<String> = []
var inProgressTasks: Set<String> = []

func startTask(_ taskId: String) {
    inProgressTasks.insert(taskId)
}

func completeTask(_ taskId: String) {
    inProgressTasks.remove(taskId)
    completedTasks.insert(taskId)
}

func isTaskInProgress(_ taskId: String) -> Bool {
    return inProgressTasks.contains(taskId)
}

func isTaskCompleted(_ taskId: String) -> Bool {
    return completedTasks.contains(taskId)
}
```

#### Best Practices for Sets

1. **Use sets for uniqueness** - When you need to ensure no duplicates
2. **Use sets for membership testing** - When you need fast `contains` operations
3. **Use sets for set operations** - Union, intersection, difference, etc.
4. **Choose appropriate element types** - Must conform to `Hashable`
5. **Use `reserveCapacity`** when you know the final size
6. **Consider memory overhead** - Sets use more memory than arrays for small collections
7. **Use sets for mathematical operations** - Perfect for set theory applications
8. **Convert to arrays when order matters** - Sets are unordered

**When to use sets vs other collections:**

- **Use sets when:**
  - You need to ensure uniqueness
  - You're doing frequent membership testing
  - You need set operations (union, intersection, etc.)
  - You're implementing mathematical algorithms
  - You're building caches or lookup tables

- **Use arrays when:**
  - You need ordered collections
  - You're iterating through all items
  - You need index-based access
  - You're building lists or queues
  - Order matters

- **Use dictionaries when:**
  - You need key-value pairs
  - You need fast lookups by unique keys
  - You're mapping relationships between data

Sets are powerful tools for working with unique collections and performing set mathematics. They're particularly useful in algorithms, data analysis, and systems where uniqueness and membership testing are important. Understanding their performance characteristics and use cases will help you choose the right collection type for your needs.

---

## Control Flow

Control flow is the foundation of programming logic - it determines how your program makes decisions and repeats actions. Swift provides powerful, safe control flow constructs that help you write clear, maintainable code.

### If Statements

If statements are the most fundamental control flow construct, allowing your program to make decisions based on conditions.

#### Basic If-Else Structure

```swift
let score = 85

if score >= 90 {
    print("Excellent!")
} else if score >= 80 {
    print("Good job!")
} else if score >= 70 {
    print("Not bad!")
} else {
    print("Need improvement!")
}

// Ternary operator - concise conditional assignment
let grade = score >= 90 ? "A" : score >= 80 ? "B" : score >= 70 ? "C" : "F"
```

**When to use if-else vs ternary:**
- **Use if-else when:**
  - You have complex logic with multiple statements
  - You need to perform multiple actions
  - The condition is complex or involves multiple variables
  - You want maximum readability

- **Use ternary when:**
  - You have a simple condition
  - You're assigning a value based on a condition
  - The logic fits on one line
  - You want concise code

#### Advanced If Statement Patterns

```swift
// Pattern 1: Multiple conditions with logical operators
let age = 25
let hasLicense = true
let hasInsurance = false

if age >= 16 && hasLicense && hasInsurance {
    print("You can drive!")
} else if age >= 16 && hasLicense {
    print("You can drive, but you need insurance!")
} else if age >= 16 {
    print("You need both a license and insurance to drive!")
} else {
    print("You're too young to drive!")
}

// Pattern 2: Optional binding in if statements
let userInput: String? = "42"

if let input = userInput, let number = Int(input), number > 0 {
    print("Valid positive number: \(number)")
} else {
    print("Invalid input or non-positive number")
}

// Pattern 3: Guard statements for early returns
func processUser(_ user: User?) -> Bool {
    guard let user = user else {
        print("No user provided")
        return false
    }
    
    guard user.age >= 18 else {
        print("User must be 18 or older")
        return false
    }
    
    guard !user.isBanned else {
        print("User is banned")
        return false
    }
    
    // Process the valid user
    print("Processing user: \(user.name)")
    return true
}

// Pattern 4: Complex nested conditions
let temperature = 75
let humidity = 60
let isRaining = false

if temperature > 80 {
    if humidity > 70 {
        print("It's hot and humid - stay hydrated!")
    } else {
        print("It's hot but not too humid")
    }
} else if temperature > 60 {
    if isRaining {
        print("It's mild but raining - bring an umbrella!")
    } else {
        print("It's pleasant weather")
    }
} else {
    if isRaining {
        print("It's cold and raining - bundle up!")
    } else {
        print("It's cold but dry")
    }
}
```

#### Real-World If Statement Examples

**Example 1: User Authentication System**

```swift
struct User {
    let username: String
    let password: String
    let isActive: Bool
    let loginAttempts: Int
    let lastLoginDate: Date?
}

class AuthenticationManager {
    private let maxLoginAttempts = 5
    private let lockoutDuration: TimeInterval = 900 // 15 minutes
    
    func authenticateUser(_ username: String, password: String, storedUser: User?) -> AuthResult {
        // Check if user exists
        guard let user = storedUser else {
            return .failure(.userNotFound)
        }
        
        // Check if user is active
        guard user.isActive else {
            return .failure(.accountDeactivated)
        }
        
        // Check if account is locked
        if user.loginAttempts >= maxLoginAttempts {
            if let lastLogin = user.lastLoginDate {
                let timeSinceLastLogin = Date().timeIntervalSince(lastLogin)
                if timeSinceLastLogin < lockoutDuration {
                    let remainingTime = lockoutDuration - timeSinceLastLogin
                    return .failure(.accountLocked(remainingTime: remainingTime))
                }
            }
        }
        
        // Verify password
        if user.password == password {
            return .success(user)
        } else {
            return .failure(.invalidPassword)
        }
    }
}

enum AuthResult {
    case success(User)
    case failure(AuthError)
}

enum AuthError {
    case userNotFound
    case accountDeactivated
    case accountLocked(remainingTime: TimeInterval)
    case invalidPassword
}

// Usage
let authManager = AuthenticationManager()
let user = User(
    username: "john_doe",
    password: "secret123",
    isActive: true,
    loginAttempts: 3,
    lastLoginDate: Date().addingTimeInterval(-600) // 10 minutes ago
)

let result = authManager.authenticateUser("john_doe", password: "wrong_password", storedUser: user)

switch result {
case .success(let user):
    print("Welcome, \(user.username)!")
case .failure(.userNotFound):
    print("User not found")
case .failure(.accountDeactivated):
    print("Account is deactivated")
case .failure(.accountLocked(let remainingTime)):
    print("Account is locked. Try again in \(Int(remainingTime / 60)) minutes")
case .failure(.invalidPassword):
    print("Invalid password")
}
```

**Example 2: E-commerce Order Validation**

```swift
struct Order {
    let items: [OrderItem]
    let customer: Customer
    let shippingAddress: Address
    let paymentMethod: PaymentMethod
}

struct OrderItem {
    let productId: String
    let quantity: Int
    let price: Double
    let inStock: Bool
}

struct Customer {
    let id: String
    let email: String
    let isVerified: Bool
    let creditLimit: Double
    let outstandingBalance: Double
}

struct Address {
    let street: String
    let city: String
    let state: String
    let zipCode: String
    let country: String
}

enum PaymentMethod {
    case creditCard(String)
    case paypal(String)
    case bankTransfer(String)
}

class OrderValidator {
    func validateOrder(_ order: Order) -> ValidationResult {
        var errors: [ValidationError] = []
        
        // Validate customer
        if !order.customer.isVerified {
            errors.append(.customerNotVerified)
        }
        
        // Validate items
        for item in order.items {
            if !item.inStock {
                errors.append(.itemOutOfStock(productId: item.productId))
            }
            
            if item.quantity <= 0 {
                errors.append(.invalidQuantity(productId: item.productId, quantity: item.quantity))
            }
        }
        
        // Validate total amount
        let totalAmount = order.items.reduce(0) { $0 + ($1.price * Double($1.quantity)) }
        let availableCredit = order.customer.creditLimit - order.customer.outstandingBalance
        
        if totalAmount > availableCredit {
            errors.append(.insufficientCredit(required: totalAmount, available: availableCredit))
        }
        
        // Validate shipping address
        if order.shippingAddress.country.isEmpty || order.shippingAddress.state.isEmpty {
            errors.append(.invalidShippingAddress)
        }
        
        // Validate payment method
        if !isValidPaymentMethod(order.paymentMethod) {
            errors.append(.invalidPaymentMethod)
        }
        
        if errors.isEmpty {
            return .valid
        } else {
            return .invalid(errors)
        }
    }
    
    private func isValidPaymentMethod(_ method: PaymentMethod) -> Bool {
        switch method {
        case .creditCard(let number):
            return number.count >= 13 && number.count <= 19
        case .paypal(let email):
            return email.contains("@")
        case .bankTransfer(let accountNumber):
            return accountNumber.count >= 8
        }
    }
}

enum ValidationResult {
    case valid
    case invalid([ValidationError])
}

enum ValidationError {
    case customerNotVerified
    case itemOutOfStock(productId: String)
    case invalidQuantity(productId: String, quantity: Int)
    case insufficientCredit(required: Double, available: Double)
    case invalidShippingAddress
    case invalidPaymentMethod
}

// Usage
let validator = OrderValidator()
let order = Order(
    items: [
        OrderItem(productId: "1", quantity: 2, price: 29.99, inStock: true),
        OrderItem(productId: "2", quantity: 1, price: 99.99, inStock: false)
    ],
    customer: Customer(
        id: "123",
        email: "john@example.com",
        isVerified: true,
        creditLimit: 1000.0,
        outstandingBalance: 150.0
    ),
    shippingAddress: Address(
        street: "123 Main St",
        city: "New York",
        state: "NY",
        zipCode: "10001",
        country: "USA"
    ),
    paymentMethod: .creditCard("1234567890123456")
)

let validationResult = validator.validateOrder(order)

switch validationResult {
case .valid:
    print("Order is valid and can be processed")
case .invalid(let errors):
    print("Order validation failed:")
    for error in errors {
        switch error {
        case .customerNotVerified:
            print("- Customer account is not verified")
        case .itemOutOfStock(let productId):
            print("- Product \(productId) is out of stock")
        case .invalidQuantity(let productId, let quantity):
            print("- Invalid quantity \(quantity) for product \(productId)")
        case .insufficientCredit(let required, let available):
            print("- Insufficient credit: required $\(required), available $\(available)")
        case .invalidShippingAddress:
            print("- Invalid shipping address")
        case .invalidPaymentMethod:
            print("- Invalid payment method")
        }
    }
}
```

### Switch Statements

Switch statements provide a clean, readable way to handle multiple possible values. They're more powerful than if-else chains and can handle complex pattern matching.

#### Basic Switch Structure

```swift
let direction = "north"

switch direction {
case "north":
    print("Go up")
case "south":
    print("Go down")
case "east":
    print("Go right")
case "west":
    print("Go left")
default:
    print("Unknown direction")
}

// Switch with ranges
let temperature = 75

switch temperature {
case 0..<32:
    print("Freezing")
case 32..<50:
    print("Cold")
case 50..<70:
    print("Cool")
case 70..<90:
    print("Warm")
default:
    print("Hot")
}
```

#### Advanced Switch Patterns

```swift
// Pattern 1: Multiple values in a single case
let character = "a"

switch character {
case "a", "e", "i", "o", "u":
    print("Vowel")
case "b", "c", "d", "f", "g", "h", "j", "k", "l", "m", "n", "p", "q", "r", "s", "t", "v", "w", "x", "y", "z":
    print("Consonant")
default:
    print("Not a letter")
}

// Pattern 2: Value binding
let coordinate = (x: 5, y: 10)

switch coordinate {
case (0, 0):
    print("At origin")
case (_, 0):
    print("On x-axis")
case (0, _):
    print("On y-axis")
case (let x, let y) where x == y:
    print("On diagonal line: x = y = \(x)")
case (let x, let y) where x == -y:
    print("On diagonal line: x = -y = \(x)")
case (let x, let y):
    print("At point (\(x), \(y))")
}

// Pattern 3: Where clauses
let point = (x: 5, y: 5)

switch point {
case let (x, y) where x == y && x > 0:
    print("First quadrant diagonal")
case let (x, y) where x == y && x < 0:
    print("Third quadrant diagonal")
case let (x, y) where x == -y && x > 0:
    print("Fourth quadrant diagonal")
case let (x, y) where x == -y && x < 0:
    print("Second quadrant diagonal")
case (0, 0):
    print("At origin")
default:
    print("Somewhere else")
}

// Pattern 4: Enum matching with associated values
enum NetworkResponse {
    case success(String)
    case failure(Error)
    case loading
}

let response: NetworkResponse = .success("Data received")

switch response {
case .success(let data):
    print("Success: \(data)")
case .failure(let error):
    print("Error: \(error)")
case .loading:
    print("Still loading...")
}

// Pattern 5: Type casting
let mixedArray: [Any] = ["Hello", 42, 3.14, true, "World"]

for item in mixedArray {
    switch item {
    case let string as String:
        print("String: \(string)")
    case let number as Int:
        print("Integer: \(number)")
    case let double as Double:
        print("Double: \(double)")
    case let boolean as Bool:
        print("Boolean: \(boolean)")
    default:
        print("Unknown type")
    }
}
```

#### Real-World Switch Examples

**Example 1: HTTP Status Code Handler**

```swift
enum HTTPStatus {
    case informational(Int)
    case success(Int)
    case redirection(Int)
    case clientError(Int)
    case serverError(Int)
    case unknown(Int)
    
    init(statusCode: Int) {
        switch statusCode {
        case 100...199:
            self = .informational(statusCode)
        case 200...299:
            self = .success(statusCode)
        case 300...399:
            self = .redirection(statusCode)
        case 400...499:
            self = .clientError(statusCode)
        case 500...599:
            self = .serverError(statusCode)
        default:
            self = .unknown(statusCode)
        }
    }
    
    var description: String {
        switch self {
        case .informational(let code):
            switch code {
            case 100: return "Continue"
            case 101: return "Switching Protocols"
            case 102: return "Processing"
            default: return "Informational Response"
            }
        case .success(let code):
            switch code {
            case 200: return "OK"
            case 201: return "Created"
            case 202: return "Accepted"
            case 204: return "No Content"
            case 206: return "Partial Content"
            default: return "Success Response"
            }
        case .redirection(let code):
            switch code {
            case 300: return "Multiple Choices"
            case 301: return "Moved Permanently"
            case 302: return "Found"
            case 304: return "Not Modified"
            case 307: return "Temporary Redirect"
            default: return "Redirection Response"
            }
        case .clientError(let code):
            switch code {
            case 400: return "Bad Request"
            case 401: return "Unauthorized"
            case 403: return "Forbidden"
            case 404: return "Not Found"
            case 429: return "Too Many Requests"
            default: return "Client Error"
            }
        case .serverError(let code):
            switch code {
            case 500: return "Internal Server Error"
            case 501: return "Not Implemented"
            case 502: return "Bad Gateway"
            case 503: return "Service Unavailable"
            case 504: return "Gateway Timeout"
            default: return "Server Error"
            }
        case .unknown(let code):
            return "Unknown Status Code: \(code)"
        }
    }
    
    var shouldRetry: Bool {
        switch self {
        case .clientError(let code):
            return code == 429 // Rate limited
        case .serverError:
            return true // Always retry server errors
        default:
            return false
        }
    }
    
    var isSuccess: Bool {
        if case .success = self {
            return true
        }
        return false
    }
}

// Usage
let statusCodes = [200, 404, 500, 429, 301, 100]

for code in statusCodes {
    let status = HTTPStatus(statusCode: code)
    print("\(code): \(status.description)")
    
    if status.shouldRetry {
        print("  → Should retry this request")
    }
    
    if status.isSuccess {
        print("  → Request was successful")
    }
}
```

**Example 2: Game State Machine**

```swift
enum GameState {
    case mainMenu
    case playing(level: Int, score: Int, lives: Int)
    case paused(level: Int, score: Int, lives: Int)
    case gameOver(finalScore: Int, levelReached: Int)
    case settings
    case loading
}

class GameStateManager {
    private var currentState: GameState = .mainMenu
    
    func updateState(_ newState: GameState) {
        let oldState = currentState
        currentState = newState
        
        // Handle state transitions
        switch (oldState, newState) {
        case (.mainMenu, .playing):
            print("Starting new game...")
            startGame()
            
        case (.playing, .paused):
            print("Game paused")
            pauseGame()
            
        case (.paused, .playing):
            print("Game resumed")
            resumeGame()
            
        case (.playing, .gameOver):
            print("Game over!")
            endGame()
            
        case (.gameOver, .mainMenu):
            print("Returning to main menu")
            returnToMainMenu()
            
        case (.mainMenu, .settings):
            print("Opening settings")
            openSettings()
            
        case (.settings, .mainMenu):
            print("Closing settings")
            closeSettings()
            
        default:
            print("Invalid state transition from \(oldState) to \(newState)")
        }
    }
    
    func handleUserInput(_ input: String) {
        switch currentState {
        case .mainMenu:
            handleMainMenuInput(input)
            
        case .playing(let level, let score, let lives):
            handlePlayingInput(input, level: level, score: score, lives: lives)
            
        case .paused(let level, let score, let lives):
            handlePausedInput(input, level: level, score: score, lives: lives)
            
        case .gameOver:
            handleGameOverInput(input)
            
        case .settings:
            handleSettingsInput(input)
            
        case .loading:
            print("Game is loading, input ignored")
        }
    }
    
    private func handleMainMenuInput(_ input: String) {
        switch input {
        case "start":
            updateState(.playing(level: 1, score: 0, lives: 3))
        case "settings":
            updateState(.settings)
        case "quit":
            print("Quitting game...")
        default:
            print("Unknown command: \(input)")
        }
    }
    
    private func handlePlayingInput(_ input: String, level: Int, score: Int, lives: Int) {
        switch input {
        case "pause":
            updateState(.paused(level: level, score: score, lives: lives))
        case "move_left":
            movePlayer(direction: .left)
        case "move_right":
            movePlayer(direction: .right)
        case "jump":
            jumpPlayer()
        case "shoot":
            shootProjectile()
        default:
            print("Unknown command: \(input)")
        }
    }
    
    private func handlePausedInput(_ input: String, level: Int, score: Int, lives: Int) {
        switch input {
        case "resume":
            updateState(.playing(level: level, score: score, lives: lives))
        case "restart":
            updateState(.playing(level: 1, score: 0, lives: 3))
        case "main_menu":
            updateState(.mainMenu)
        default:
            print("Unknown command: \(input)")
        }
    }
    
    private func handleGameOverInput(_ input: String) {
        switch input {
        case "restart":
            updateState(.playing(level: 1, score: 0, lives: 3))
        case "main_menu":
            updateState(.mainMenu)
        default:
            print("Unknown command: \(input)")
        }
    }
    
    private func handleSettingsInput(_ input: String) {
        switch input {
        case "back":
            updateState(.mainMenu)
        case "volume_up":
            increaseVolume()
        case "volume_down":
            decreaseVolume()
        case "difficulty":
            changeDifficulty()
        default:
            print("Unknown command: \(input)")
        }
    }
    
    // Placeholder methods
    private func startGame() { /* Implementation */ }
    private func pauseGame() { /* Implementation */ }
    private func resumeGame() { /* Implementation */ }
    private func endGame() { /* Implementation */ }
    private func returnToMainMenu() { /* Implementation */ }
    private func openSettings() { /* Implementation */ }
    private func closeSettings() { /* Implementation */ }
    private func movePlayer(direction: Direction) { /* Implementation */ }
    private func jumpPlayer() { /* Implementation */ }
    private func shootProjectile() { /* Implementation */ }
    private func increaseVolume() { /* Implementation */ }
    private func decreaseVolume() { /* Implementation */ }
    private func changeDifficulty() { /* Implementation */ }
}

enum Direction {
    case left, right
}

// Usage
let gameManager = GameStateManager()

// Simulate user interactions
gameManager.handleUserInput("start")      // Start game
gameManager.handleUserInput("pause")      // Pause game
gameManager.handleUserInput("resume")     // Resume game
gameManager.handleUserInput("main_menu")  // Return to menu
```

### Loops

Loops allow you to repeat code multiple times. Swift provides several loop constructs, each optimized for different use cases.

#### For-In Loops

For-in loops are the most common loop type in Swift, perfect for iterating over collections and ranges.

```swift
// Basic for-in with arrays
let names = ["Alice", "Bob", "Charlie"]
for name in names {
    print("Hello, \(name)!")
}

// For-in with ranges
for i in 1...5 {
    print("Number \(i)")
}

// For-in with half-open ranges
for i in 1..<5 {
    print("Number \(i)")  // Prints 1, 2, 3, 4
}

// For-in with stride
for i in stride(from: 0, to: 20, by: 2) {
    print("Even number: \(i)")  // Prints 0, 2, 4, 6, 8, 10, 12, 14, 16, 18
}

// For-in with dictionaries
let scores = ["Alice": 95, "Bob": 87, "Charlie": 92]
for (name, score) in scores {
    print("\(name) scored \(score)")
}

// For-in with enumerated arrays
let fruits = ["Apple", "Banana", "Orange"]
for (index, fruit) in fruits.enumerated() {
    print("\(index + 1). \(fruit)")
}

// For-in with where clause
for i in 1...10 where i % 2 == 0 {
    print("Even number: \(i)")  // Prints 2, 4, 6, 8, 10
}
```

#### While and Repeat-While Loops

While loops are useful when you don't know how many iterations you'll need, and repeat-while ensures at least one execution.

```swift
// While loop
var countdown = 5
while countdown > 0 {
    print("\(countdown)...")
    countdown -= 1
}
print("Blast off!")

// Repeat-while (do-while equivalent)
var number = 0
repeat {
    print("Number: \(number)")
    number += 1
} while number < 3

// While with complex condition
var attempts = 0
var success = false
let maxAttempts = 5

while !success && attempts < maxAttempts {
    attempts += 1
    print("Attempt \(attempts) of \(maxAttempts)")
    
    // Simulate some operation
    success = Bool.random()
    
    if success {
        print("Success on attempt \(attempts)!")
    } else if attempts < maxAttempts {
        print("Failed, trying again...")
    } else {
        print("Failed after \(maxAttempts) attempts")
    }
}
```

#### Real-World Loop Examples

**Example 1: Data Processing Pipeline**

```swift
struct DataRecord {
    let id: String
    let value: Double
    let timestamp: Date
    let isValid: Bool
}

class DataProcessor {
    func processRecords(_ records: [DataRecord]) -> ProcessingResult {
        var validRecords: [DataRecord] = []
        var invalidRecords: [DataRecord] = []
        var totalValue: Double = 0
        var minValue: Double = Double.infinity
        var maxValue: Double = -Double.infinity
        
        // Process each record
        for record in records {
            if record.isValid {
                validRecords.append(record)
                totalValue += record.value
                
                if record.value < minValue {
                    minValue = record.value
                }
                
                if record.value > maxValue {
                    maxValue = record.value
                }
            } else {
                invalidRecords.append(record)
            }
        }
        
        let averageValue = validRecords.isEmpty ? 0 : totalValue / Double(validRecords.count)
        
        return ProcessingResult(
            totalRecords: records.count,
            validRecords: validRecords.count,
            invalidRecords: invalidRecords.count,
            averageValue: averageValue,
            minValue: minValue == Double.infinity ? 0 : minValue,
            maxValue: maxValue == -Double.infinity ? 0 : maxValue,
            processingDate: Date()
        )
    }
    
    func findAnomalies(in records: [DataRecord], threshold: Double) -> [DataRecord] {
        var anomalies: [DataRecord] = []
        
        // Calculate mean and standard deviation
        let values = records.map { $0.value }
        let mean = values.reduce(0, +) / Double(values.count)
        
        let variance = values.reduce(0) { sum, value in
            sum + pow(value - mean, 2)
        } / Double(values.count)
        let standardDeviation = sqrt(variance)
        
        // Find records that deviate significantly from the mean
        for record in records {
            let zScore = abs(record.value - mean) / standardDeviation
            if zScore > threshold {
                anomalies.append(record)
            }
        }
        
        return anomalies
    }
    
    func batchProcess(_ records: [DataRecord], batchSize: Int) -> [ProcessingResult] {
        var results: [ProcessingResult] = []
        var currentBatch: [DataRecord] = []
        
        for record in records {
            currentBatch.append(record)
            
            if currentBatch.count >= batchSize {
                let result = processRecords(currentBatch)
                results.append(result)
                currentBatch.removeAll()
            }
        }
        
        // Process remaining records
        if !currentBatch.isEmpty {
            let result = processRecords(currentBatch)
            results.append(result)
        }
        
        return results
    }
}

struct ProcessingResult {
    let totalRecords: Int
    let validRecords: Int
    let invalidRecords: Int
    let averageValue: Double
    let minValue: Double
    let maxValue: Double
    let processingDate: Date
}

// Usage
let processor = DataProcessor()

// Create sample data
let sampleRecords = [
    DataRecord(id: "1", value: 10.5, timestamp: Date(), isValid: true),
    DataRecord(id: "2", value: 15.2, timestamp: Date(), isValid: true),
    DataRecord(id: "3", value: 8.7, timestamp: Date(), isValid: true),
    DataRecord(id: "4", value: -5.0, timestamp: Date(), isValid: false),
    DataRecord(id: "5", value: 22.1, timestamp: Date(), isValid: true),
    DataRecord(id: "6", value: 1000.0, timestamp: Date(), isValid: true), // Anomaly
]

// Process all records
let result = processor.processRecords(sampleRecords)
print("Processed \(result.totalRecords) records:")
print("- Valid: \(result.validRecords)")
print("- Invalid: \(result.invalidRecords)")
print("- Average: \(result.averageValue)")
print("- Range: \(result.minValue) to \(result.maxValue)")

// Find anomalies
let anomalies = processor.findAnomalies(in: sampleRecords, threshold: 2.0)
print("\nFound \(anomalies.count) anomalies:")
for anomaly in anomalies {
    print("- Record \(anomaly.id): \(anomaly.value)")
}

// Batch processing
let batchResults = processor.batchProcess(sampleRecords, batchSize: 2)
print("\nBatch processing results:")
for (index, batchResult) in batchResults.enumerated() {
    print("Batch \(index + 1): \(batchResult.validRecords) valid records")
}
```

**Example 2: Game Loop Implementation**

```swift
class GameLoop {
    private var isRunning = false
    private var lastUpdateTime: TimeInterval = 0
    private var gameState: GameState = .playing(level: 1, score: 0, lives: 3)
    
    func start() {
        isRunning = true
        lastUpdateTime = CACurrentMediaTime()
        print("Game loop started")
        
        while isRunning {
            let currentTime = CACurrentMediaTime()
            let deltaTime = currentTime - lastUpdateTime
            
            update(deltaTime: deltaTime)
            render()
            
            lastUpdateTime = currentTime
            
            // Cap frame rate to 60 FPS
            let targetFrameTime: TimeInterval = 1.0 / 60.0
            if deltaTime < targetFrameTime {
                Thread.sleep(forTimeInterval: targetFrameTime - deltaTime)
            }
        }
    }
    
    func stop() {
        isRunning = false
        print("Game loop stopped")
    }
    
    private func update(deltaTime: TimeInterval) {
        // Update game logic based on current state
        switch gameState {
        case .playing(let level, var score, var lives):
            // Update player position
            updatePlayer(deltaTime: deltaTime)
            
            // Update enemies
            updateEnemies(deltaTime: deltaTime)
            
            // Check collisions
            checkCollisions()
            
            // Update score
            score += Int(deltaTime * 10) // Score increases over time
            
            // Update game state
            gameState = .playing(level: level, score: score, lives: lives)
            
        case .paused:
            // No updates while paused
            break
            
        case .gameOver:
            // Handle game over state
            break
            
        default:
            break
        }
    }
    
    private func render() {
        // Render current game state
        switch gameState {
        case .playing(let level, let score, let lives):
            print("Level: \(level) | Score: \(score) | Lives: \(lives)")
            
        case .paused(let level, let score, let lives):
            print("PAUSED - Level: \(level) | Score: \(score) | Lives: \(lives)")
            
        case .gameOver(let finalScore, let levelReached):
            print("GAME OVER - Final Score: \(finalScore) | Level Reached: \(levelReached)")
            
        default:
            break
        }
    }
    
    private func updatePlayer(deltaTime: TimeInterval) {
        // Simulate player movement
        // In a real game, this would update player position, velocity, etc.
    }
    
    private func updateEnemies(deltaTime: TimeInterval) {
        // Simulate enemy AI and movement
        // In a real game, this would update enemy positions, behaviors, etc.
    }
    
    private func checkCollisions() {
        // Check for collisions between game objects
        // In a real game, this would detect and handle collisions
    }
}

// Simulate game loop
let gameLoop = GameLoop()

// Start the game loop in a separate thread to avoid blocking
DispatchQueue.global(qos: .userInteractive).async {
    gameLoop.start()
}

// Stop the game loop after 5 seconds
DispatchQueue.main.asyncAfter(deadline: .now() + 5) {
    gameLoop.stop()
}
```

#### Loop Control Statements

Swift provides several statements to control loop execution:

```swift
// Break statement - exit loop early
for i in 1...10 {
    if i == 5 {
        break  // Exit loop when i equals 5
    }
    print(i)  // Prints 1, 2, 3, 4
}

// Continue statement - skip current iteration
for i in 1...10 {
    if i % 2 == 0 {
        continue  // Skip even numbers
    }
    print(i)  // Prints 1, 3, 5, 7, 9
}

// Nested loops with labels
outerLoop: for i in 1...3 {
    innerLoop: for j in 1...3 {
        if i == 2 && j == 2 {
            break outerLoop  // Exit outer loop
        }
        print("(\(i), \(j))")
    }
}

// Early exit with guard
func processItems(_ items: [String]) {
    for item in items {
        guard !item.isEmpty else {
            continue  // Skip empty items
        }
        
        guard item.count > 3 else {
            break  // Exit if item is too short
        }
        
        print("Processing: \(item)")
    }
}
```

#### Best Practices for Loops

1. **Choose the right loop type** for your use case
2. **Use for-in** for iterating over collections and ranges
3. **Use while** when you don't know the number of iterations
4. **Use repeat-while** when you need at least one execution
5. **Avoid infinite loops** by ensuring termination conditions
6. **Use break and continue** for better control flow
7. **Consider performance** for large collections
8. **Use labels** for complex nested loops

**Performance considerations:**

- **For-in loops** are optimized for collections
- **While loops** can be more efficient for complex conditions
- **Avoid modifying collections** while iterating over them
- **Use enumerated()** when you need both index and value
- **Consider using stride** for custom step sizes

Control flow is fundamental to programming, and Swift provides powerful, safe constructs that help you write clear, maintainable code. Understanding when and how to use each control flow mechanism will make you a more effective Swift developer.

---

## Functions

Functions are the building blocks of Swift programs - they encapsulate reusable code, provide structure, and enable you to break complex problems into manageable pieces. Swift's function system is powerful and flexible, supporting multiple parameter styles, return types, and advanced features.

### Basic Functions

Functions in Swift are first-class citizens, meaning they can be assigned to variables, passed as parameters, and returned from other functions.

#### Function Declaration and Syntax

```swift
// Simple function with no parameters or return value
func greet() {
    print("Hello, World!")
}

// Function with parameters
func greet(name: String) {
    print("Hello, \(name)!")
}

// Function with return value
func add(a: Int, b: Int) -> Int {
    return a + b
}

// Function with multiple return values using tuple
func getUserInfo() -> (name: String, age: Int, email: String) {
    return ("John Doe", 25, "john@example.com")
}

// Function with optional return value
func findUser(by id: String) -> User? {
    // Implementation would search for user
    return nil
}

// Function that never returns (fatal error, infinite loop, etc.)
func crashApp() -> Never {
    fatalError("This app has crashed!")
}
```

**Function naming conventions:**
- **Use camelCase** for function names
- **Start with a verb** that describes the action
- **Be descriptive** but concise
- **Follow Swift API Design Guidelines**

#### Parameter Types and Styles

Swift provides several ways to handle parameters, each with different use cases and benefits.

```swift
// External parameter names (default behavior)
func greet(firstName: String, lastName: String) {
    print("Hello, \(firstName) \(lastName)!")
}

// Omitting external names with underscore
func multiply(_ a: Int, _ b: Int) -> Int {
    return a * b
}

// Custom external names
func move(from start: Int, to end: Int) {
    print("Moving from \(start) to \(end)")
}

// Default parameter values
func createUser(name: String, age: Int = 18, isActive: Bool = true) {
    print("Creating user: \(name), Age: \(age), Active: \(isActive)")
}

// Variadic parameters (variable number of arguments)
func sum(numbers: Int...) -> Int {
    return numbers.reduce(0, +)
}

// In-out parameters (can modify the original value)
func swapValues(_ a: inout Int, _ b: inout Int) {
    let temp = a
    a = b
    b = temp
}

// Function calls
greet(firstName: "John", lastName: "Doe")
let result = multiply(5, 3)
move(from: 0, to: 10)
createUser(name: "Alice")
createUser(name: "Bob", age: 25)
let total = sum(numbers: 1, 2, 3, 4, 5)

var x = 5, y = 10
swapValues(&x, &y)
print("x: \(x), y: \(y)")  // x: 10, y: 5
```

**Parameter best practices:**

```swift
// ✅ Good - clear parameter names
func calculateArea(width: Double, height: Double) -> Double {
    return width * height
}

// ❌ Bad - unclear parameter names
func calc(w: Double, h: Double) -> Double {
    return w * h
}

// ✅ Good - descriptive external names
func move(from startPosition: CGPoint, to endPosition: CGPoint) {
    // Implementation
}

// ✅ Good - omit external names for simple functions
func add(_ a: Int, _ b: Int) -> Int {
    return a + b
}

// ✅ Good - use default values for optional parameters
func configureView(title: String, backgroundColor: UIColor = .white, cornerRadius: CGFloat = 8.0) {
    // Implementation
}
```

#### Function Overloading

Swift supports function overloading, allowing you to define multiple functions with the same name but different parameter types or counts.

```swift
// Overloaded functions for different types
func process(_ value: Int) -> String {
    return "Integer: \(value)"
}

func process(_ value: String) -> String {
    return "String: \(value)"
}

func process(_ value: Double) -> String {
    return "Double: \(value)"
}

func process(_ value: Bool) -> String {
    return "Boolean: \(value)"
}

// Overloaded functions with different parameter counts
func calculate(_ a: Int, _ b: Int) -> Int {
    return a + b
}

func calculate(_ a: Int, _ b: Int, _ c: Int) -> Int {
    return a + b + c
}

func calculate(_ values: [Int]) -> Int {
    return values.reduce(0, +)
}

// Usage
print(process(42))           // "Integer: 42"
print(process("Hello"))      // "String: Hello"
print(process(3.14))         // "Double: 3.14"
print(process(true))         // "Boolean: true"

print(calculate(5, 3))       // 8
print(calculate(1, 2, 3))    // 6
print(calculate([1, 2, 3, 4])) // 10
```

#### Real-World Function Examples

**Example 1: Data Validation System**

```swift
struct ValidationRule<T> {
    let validate: (T) -> Bool
    let errorMessage: String
}

class DataValidator {
    // Generic validation function
    static func validate<T>(_ value: T, using rules: [ValidationRule<T>]) -> ValidationResult {
        for rule in rules {
            if !rule.validate(value) {
                return .failure(rule.errorMessage)
            }
        }
        return .success
    }
    
    // String validation rules
    static let nonEmptyString = ValidationRule<String>(
        validate: { !$0.trimmingCharacters(in: .whitespacesAndNewlines).isEmpty },
        errorMessage: "String cannot be empty"
    )
    
    static let minLength = { (length: Int) in
        ValidationRule<String>(
            validate: { $0.count >= length },
            errorMessage: "String must be at least \(length) characters long"
        )
    }
    
    static let maxLength = { (length: Int) in
        ValidationRule<String>(
            validate: { $0.count <= length },
            errorMessage: "String cannot exceed \(length) characters"
        )
    }
    
    static let emailFormat = ValidationRule<String>(
        validate: { $0.contains("@") && $0.contains(".") },
        errorMessage: "Invalid email format"
    )
    
    // Integer validation rules
    static let positiveInteger = ValidationRule<Int>(
        validate: { $0 > 0 },
        errorMessage: "Value must be positive"
    )
    
    static let range = { (min: Int, max: Int) in
        ValidationRule<Int>(
            validate: { $0 >= min && $0 <= max },
            errorMessage: "Value must be between \(min) and \(max)"
        )
    }
    
    // Array validation rules
    static let nonEmptyArray = ValidationRule<[Any]>(
        validate: { !$0.isEmpty },
        errorMessage: "Array cannot be empty"
    )
    
    static let arraySize = { (size: Int) in
        ValidationRule<[Any]>(
            validate: { $0.count == size },
            errorMessage: "Array must contain exactly \(size) elements"
        )
    }
}

enum ValidationResult {
    case success
    case failure(String)
}

// Usage
let validator = DataValidator()

// Validate email
let email = "user@example.com"
let emailRules = [
    DataValidator.nonEmptyString,
    DataValidator.minLength(5),
    DataValidator.maxLength(100),
    DataValidator.emailFormat
]

let emailValidation = DataValidator.validate(email, using: emailRules)
switch emailValidation {
case .success:
    print("Email is valid")
case .failure(let error):
    print("Email validation failed: \(error)")
}

// Validate age
let age = 25
let ageRules = [
    DataValidator.positiveInteger,
    DataValidator.range(min: 13, max: 120)
]

let ageValidation = DataValidator.validate(age, using: ageRules)
switch ageValidation {
case .success:
    print("Age is valid")
case .failure(let error):
    print("Age validation failed: \(error)")
}

// Validate array
let numbers = [1, 2, 3, 4, 5]
let arrayRules = [
    DataValidator.nonEmptyArray,
    DataValidator.arraySize(5)
]

let arrayValidation = DataValidator.validate(numbers, using: arrayRules)
switch arrayValidation {
case .success:
    print("Array is valid")
case .failure(let error):
    print("Array validation failed: \(error)")
}
```

**Example 2: Mathematical Function Library**

```swift
struct MathFunctions {
    // Basic arithmetic
    static func add(_ a: Double, _ b: Double) -> Double {
        return a + b
    }
    
    static func subtract(_ a: Double, _ b: Double) -> Double {
        return a - b
    }
    
    static func multiply(_ a: Double, _ b: Double) -> Double {
        return a * b
    }
    
    static func divide(_ a: Double, _ b: Double) -> Double? {
        guard b != 0 else { return nil }
        return a / b
    }
    
    // Advanced mathematical functions
    static func power(_ base: Double, _ exponent: Double) -> Double {
        return pow(base, exponent)
    }
    
    static func squareRoot(_ value: Double) -> Double? {
        guard value >= 0 else { return nil }
        return sqrt(value)
    }
    
    static func factorial(_ n: Int) -> Int? {
        guard n >= 0 else { return nil }
        guard n <= 20 else { return nil } // Prevent overflow
        
        if n == 0 || n == 1 {
            return 1
        }
        
        var result = 1
        for i in 2...n {
            result *= i
        }
        return result
    }
    
    // Statistical functions
    static func average(_ values: [Double]) -> Double? {
        guard !values.isEmpty else { return nil }
        return values.reduce(0, +) / Double(values.count)
    }
    
    static func median(_ values: [Double]) -> Double? {
        guard !values.isEmpty else { return nil }
        
        let sorted = values.sorted()
        let count = sorted.count
        
        if count % 2 == 0 {
            let mid1 = sorted[count / 2 - 1]
            let mid2 = sorted[count / 2]
            return (mid1 + mid2) / 2
        } else {
            return sorted[count / 2]
        }
    }
    
    static func standardDeviation(_ values: [Double]) -> Double? {
        guard values.count > 1 else { return nil }
        
        let mean = values.reduce(0, +) / Double(values.count)
        let variance = values.reduce(0) { sum, value in
            sum + pow(value - mean, 2)
        } / Double(values.count)
        
        return sqrt(variance)
    }
    
    // Geometric functions
    static func areaOfCircle(radius: Double) -> Double {
        return Double.pi * radius * radius
    }
    
    static func circumferenceOfCircle(radius: Double) -> Double {
        return 2 * Double.pi * radius
    }
    
    static func areaOfRectangle(width: Double, height: Double) -> Double {
        return width * height
    }
    
    static func areaOfTriangle(base: Double, height: Double) -> Double {
        return 0.5 * base * height
    }
    
    // Utility functions
    static func isPrime(_ n: Int) -> Bool {
        guard n > 1 else { return false }
        guard n != 2 else { return true }
        guard n % 2 != 0 else { return false }
        
        let sqrtN = Int(sqrt(Double(n)))
        for i in stride(from: 3, through: sqrtN, by: 2) {
            if n % i == 0 {
                return false
            }
        }
        return true
    }
    
    static func greatestCommonDivisor(_ a: Int, _ b: Int) -> Int {
        var x = abs(a)
        var y = abs(b)
        
        while y != 0 {
            let temp = y
            y = x % y
            x = temp
        }
        
        return x
    }
    
    static func leastCommonMultiple(_ a: Int, _ b: Int) -> Int {
        return abs(a * b) / greatestCommonDivisor(a, b)
    }
}

// Usage
let math = MathFunctions()

// Basic arithmetic
print("5 + 3 = \(math.add(5, 3))")
print("10 - 4 = \(math.subtract(10, 4))")
print("6 × 7 = \(math.multiply(6, 7))")

if let quotient = math.divide(15, 3) {
    print("15 ÷ 3 = \(quotient)")
}

// Advanced functions
print("2^8 = \(math.power(2, 8))")

if let sqrt = math.squareRoot(16) {
    print("√16 = \(sqrt)")
}

if let fact = math.factorial(5) {
    print("5! = \(fact)")
}

// Statistical functions
let testScores = [85, 92, 78, 96, 88, 91, 87]
let doubleScores = testScores.map { Double($0) }

if let avg = math.average(doubleScores) {
    print("Average score: \(avg)")
}

if let med = math.median(doubleScores) {
    print("Median score: \(med)")
}

if let stdDev = math.standardDeviation(doubleScores) {
    print("Standard deviation: \(stdDev)")
}

// Geometric functions
let radius = 5.0
print("Circle area: \(math.areaOfCircle(radius: radius))")
print("Circle circumference: \(math.circumferenceOfCircle(radius: radius))")

// Utility functions
print("Is 17 prime? \(math.isPrime(17))")
print("GCD of 48 and 18: \(math.greatestCommonDivisor(48, 18))")
print("LCM of 12 and 18: \(math.leastCommonMultiple(12, 18))")
```

### Advanced Function Features

#### Function Types and Closures

Functions in Swift are types, which means they can be assigned to variables, passed as parameters, and returned from other functions.

```swift
// Function type declaration
typealias MathOperation = (Double, Double) -> Double

// Functions as variables
let addFunction: MathOperation = MathFunctions.add
let multiplyFunction: MathOperation = MathFunctions.multiply

// Using function variables
let result1 = addFunction(5, 3)      // 8
let result2 = multiplyFunction(4, 6)  // 24

// Functions as parameters
func performOperation(_ operation: MathOperation, on a: Double, and b: Double) -> Double {
    return operation(a, b)
}

let sum = performOperation(addFunction, on: 10, and: 5)      // 15
let product = performOperation(multiplyFunction, on: 7, and: 8) // 56

// Functions returning functions
func createOperation(_ operation: String) -> MathOperation {
    switch operation {
    case "add":
        return MathFunctions.add
    case "multiply":
        return MathFunctions.multiply
    case "subtract":
        return MathFunctions.subtract
    default:
        return MathFunctions.add
    }
}

let operation = createOperation("multiply")
let result = operation(6, 7)  // 42
```

#### Higher-Order Functions

Higher-order functions take functions as parameters or return functions. They're powerful tools for creating flexible, reusable code.

```swift
// Function that applies a transformation
func applyTransformation<T, U>(_ items: [T], _ transform: (T) -> U) -> [U] {
    var result: [U] = []
    for item in items {
        result.append(transform(item))
    }
    return result
}

// Function that filters items
func filterItems<T>(_ items: [T], _ predicate: (T) -> Bool) -> [T] {
    var result: [T] = []
    for item in items {
        if predicate(item) {
            result.append(item)
        }
    }
    return result
}

// Function that reduces items to a single value
func reduceItems<T, U>(_ items: [T], _ initial: U, _ combine: (U, T) -> U) -> U {
    var result = initial
    for item in items {
        result = combine(result, item)
    }
    return result
}

// Usage
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// Transform: double each number
let doubled = applyTransformation(numbers) { $0 * 2 }
print("Doubled: \(doubled)")

// Filter: get even numbers
let evens = filterItems(numbers) { $0 % 2 == 0 }
print("Evens: \(evens)")

// Reduce: sum all numbers
let sum = reduceItems(numbers, 0) { $0 + $1 }
print("Sum: \(sum)")

// Chain operations
let result = numbers
    .filter { $0 % 2 == 0 }      // Get evens
    .map { $0 * $0 }             // Square them
    .reduce(0, +)                // Sum them
print("Sum of squared evens: \(result)")
```

#### Function Composition

Function composition allows you to combine multiple functions into a single function.

```swift
// Function composition operator
infix operator >>>: CompositionPrecedence
precedencegroup CompositionPrecedence {
    associativity: left
    higherThan: AssignmentPrecedence
}

func >>> <A, B, C>(f: @escaping (A) -> B, g: @escaping (B) -> C) -> (A) -> C {
    return { a in g(f(a)) }
}

// Example functions
func addOne(_ x: Int) -> Int { return x + 1 }
func multiplyByTwo(_ x: Int) -> Int { return x * 2 }
func square(_ x: Int) -> Int { return x * x }
func toString(_ x: Int) -> String { return "Result: \(x)" }

// Compose functions
let addOneAndMultiply = addOne >>> multiplyByTwo
let addOneMultiplyAndSquare = addOne >>> multiplyByTwo >>> square
let fullPipeline = addOne >>> multiplyByTwo >>> square >>> toString

// Usage
let result1 = addOneAndMultiply(5)        // (5 + 1) * 2 = 12
let result2 = addOneMultiplyAndSquare(3)  // ((3 + 1) * 2)² = 64
let result3 = fullPipeline(4)             // "Result: 100"

print(result1)  // 12
print(result2)  // 64
print(result3)  // "Result: 100"
```

#### Best Practices for Functions

1. **Keep functions small and focused** - Single responsibility principle
2. **Use descriptive names** - Function names should clearly describe what they do
3. **Limit parameters** - Functions with many parameters are hard to use and maintain
4. **Use default parameters** - Reduce the need for multiple overloaded functions
5. **Return early** - Use guard statements and early returns for better readability
6. **Handle errors gracefully** - Use optionals, throwing functions, or Result types
7. **Document complex functions** - Use comments to explain complex logic
8. **Test your functions** - Write unit tests for critical functions

**Function design principles:**

- **Single responsibility** - Each function should do one thing well
- **Pure functions** - When possible, make functions pure (no side effects)
- **Immutable parameters** - Don't modify input parameters unless necessary
- **Clear return values** - Make return values obvious and consistent
- **Error handling** - Design functions to handle errors gracefully

Functions are the foundation of Swift programming. Mastering function design, parameter handling, and advanced features will make you a more effective and productive Swift developer.

---

## Closures

Closures are self-contained blocks of functionality that can be passed around and used in your code. They're one of Swift's most powerful features, enabling functional programming patterns, callbacks, and elegant data transformations. Understanding closures is crucial for modern Swift development.

### Understanding Closures

Closures are functions that can capture and store references to variables and constants from the surrounding context. They're similar to blocks in Objective-C and lambdas in other programming languages.

#### Basic Closure Syntax

```swift
// Basic closure as a variable
let greet = { (name: String) in
    print("Hello, \(name)!")
}

// Closure with parameters and return value
let multiply = { (a: Int, b: Int) -> Int in
    return a * b
}

// Closure with no parameters
let getRandomNumber = { () -> Int in
    return Int.random(in: 1...100)
}

// Closure with no parameters and no return value
let doSomething = { () in
    print("Doing something...")
}

// Using closures
greet("World")                    // Prints: Hello, World!
let result = multiply(5, 3)      // result = 15
let random = getRandomNumber()    // random = some random number
doSomething()                     // Prints: Doing something...
```

**Closure syntax breakdown:**
- **Parameters**: `(name: String)` - input values the closure accepts
- **Return type**: `-> Int` - what the closure returns (optional)
- **Keyword**: `in` - separates the closure's signature from its body
- **Body**: The actual code that executes when the closure is called

#### Closure Types and Type Inference

Swift can infer closure types in many contexts, making your code cleaner and more readable.

```swift
// Explicit type annotation
let explicitClosure: (String) -> Void = { name in
    print("Hello, \(name)!")
}

// Type inference from context
let numbers = [1, 2, 3, 4, 5]

// Swift infers the closure type from the array's map method
let doubled = numbers.map { number in
    return number * 2
}

// Even more concise with shorthand argument names
let tripled = numbers.map { $0 * 3 }

// Multiple operations with explicit return
let processed = numbers.map { number -> Int in
    let doubled = number * 2
    let plusOne = doubled + 1
    return plusOne
}
```

**Type inference benefits:**
- **Cleaner code** - No need to specify types when Swift can infer them
- **Better readability** - Focus on logic, not type declarations
- **Maintainability** - Types automatically update when you change the closure
- **Compile-time safety** - Swift still catches type errors

#### Closure Expressions and Shorthand Syntax

Swift provides several ways to write closures, from verbose to extremely concise.

```swift
let names = ["Alice", "Bob", "Charlie", "David"]

// 1. Full closure syntax
let fullSyntax = names.map({ (name: String) -> String in
    return name.uppercased()
})

// 2. Inferring parameter and return types
let inferredTypes = names.map({ name in
    return name.uppercased()
})

// 3. Implicit return from single-expression closures
let implicitReturn = names.map({ name in
    name.uppercased()
})

// 4. Shorthand argument names
let shorthandArgs = names.map({ $0.uppercased() })

// 5. Trailing closure syntax
let trailingClosure = names.map { $0.uppercased() }

// 6. Multiple operations with trailing closure
let complexProcessing = names.map { name in
    let upper = name.uppercased()
    let reversed = String(upper.reversed())
    return "\(upper) -> \(reversed)"
}

print(fullSyntax)        // ["ALICE", "BOB", "CHARLIE", "DAVID"]
print(shorthandArgs)     // ["ALICE", "BOB", "CHARLIE", "DAVID"]
print(complexProcessing) // ["ALICE -> ECILA", "BOB -> BOB", "CHARLIE -> EILRAHC", "DAVID -> DIVAD"]
```

**When to use each syntax:**
- **Full syntax**: When you need maximum clarity or complex logic
- **Inferred types**: When the types are obvious from context
- **Implicit return**: When the closure body is a single expression
- **Shorthand arguments**: When you have simple, single-parameter operations
- **Trailing closures**: When the closure is the last parameter (most common)

### Closures with Collections

Closures shine when working with collections, providing powerful, readable ways to transform and filter data.

#### Map - Transforming Elements

The `map` function transforms each element in a collection using a closure.

```swift
let scores = [85, 92, 78, 96, 88]

// Basic transformation
let letterGrades = scores.map { score -> String in
    switch score {
    case 90...: return "A"
    case 80..<90: return "B"
    case 70..<80: return "C"
    case 60..<70: return "D"
    default: return "F"
    }
}

// Complex transformation
let gradeReports = scores.map { score in
    let letter = score >= 90 ? "A" : score >= 80 ? "B" : score >= 70 ? "C" : score >= 60 ? "D" : "F"
    let message = score >= 70 ? "Passing" : "Needs improvement"
    return "Score: \(score), Grade: \(letter), Status: \(message)"
}

// Transforming to different types
let scoreDescriptions = scores.map { score in
    if score >= 90 {
        return "Excellent performance"
    } else if score >= 80 {
        return "Good work"
    } else if score >= 70 {
        return "Satisfactory"
    } else {
        return "Needs improvement"
    }
}

print(letterGrades)      // ["B", "A", "C", "A", "B"]
print(gradeReports)      // ["Score: 85, Grade: B, Status: Passing", ...]
print(scoreDescriptions) // ["Good work", "Excellent performance", ...]
```

#### Filter - Selecting Elements

The `filter` function creates a new collection containing only elements that satisfy a condition.

```swift
let students = [
    (name: "Alice", grade: 95, attendance: 0.95),
    (name: "Bob", grade: 78, attendance: 0.88),
    (name: "Charlie", grade: 92, attendance: 0.92),
    (name: "David", grade: 65, attendance: 0.75),
    (name: "Eve", grade: 88, attendance: 0.90)
]

// Filter by grade
let highPerformers = students.filter { $0.grade >= 90 }
let needsHelp = students.filter { $0.grade < 70 }

// Filter by attendance
let goodAttendance = students.filter { $0.attendance >= 0.90 }

// Complex filtering
let wellRoundedStudents = students.filter { student in
    student.grade >= 80 && student.attendance >= 0.85
}

let atRiskStudents = students.filter { student in
    student.grade < 75 || student.attendance < 0.80
}

print("High performers: \(highPerformers.map { $0.name })")
print("Needs help: \(needsHelp.map { $0.name })")
print("Good attendance: \(goodAttendance.map { $0.name })")
print("Well-rounded: \(wellRoundedStudents.map { $0.name })")
print("At risk: \(atRiskStudents.map { $0.name })")
```

#### Reduce - Combining Elements

The `reduce` function combines all elements in a collection into a single value.

```swift
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// Basic reduction - sum
let sum = numbers.reduce(0) { result, number in
    result + number
}

// Product
let product = numbers.reduce(1) { result, number in
    result * number
}

// Find maximum
let max = numbers.reduce(numbers.first!) { result, number in
    result > number ? result : number
}

// Find minimum
let min = numbers.reduce(numbers.first!) { result, number in
    result < number ? result : number
}

// Complex reduction - create a summary
let summary = numbers.reduce("") { result, number in
    if result.isEmpty {
        return "Numbers: \(number)"
    } else {
        return result + ", \(number)"
    }
}

// Reduce to create a dictionary
let numberStats = numbers.reduce(into: [:]) { result, number in
    result[number] = number * number
}

print("Sum: \(sum)")           // Sum: 55
print("Product: \(product)")   // Product: 3628800
print("Max: \(max)")           // Max: 10
print("Min: \(min)")           // Min: 1
print(summary)                 // Numbers: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
print(numberStats)             // [1: 1, 2: 4, 3: 9, 4: 16, 5: 25, ...]
```

#### Sorted - Ordering Elements

The `sorted` function creates a new collection with elements in a specified order.

```swift
let people = [
    (name: "Alice", age: 25, salary: 75000),
    (name: "Bob", age: 30, salary: 85000),
    (name: "Charlie", age: 22, salary: 65000),
    (name: "David", age: 35, salary: 95000),
    (name: "Eve", age: 28, salary: 80000)
]

// Sort by age (ascending)
let byAge = people.sorted { $0.age < $1.age }

// Sort by salary (descending)
let bySalary = people.sorted { $0.salary > $1.salary }

// Sort by multiple criteria
let byAgeThenSalary = people.sorted { first, second in
    if first.age == second.age {
        return first.salary > second.salary
    }
    return first.age < second.age
}

// Sort by name length, then alphabetically
let byNameLength = people.sorted { first, second in
    if first.name.count == second.name.count {
        return first.name < second.name
    }
    return first.name.count < second.name.count
}

print("By age: \(byAge.map { "\($0.name)(\($0.age))" })")
print("By salary: \(bySalary.map { "\($0.name)($\($0.salary))" })")
print("By age then salary: \(byAgeThenSalary.map { "\($0.name)(\($0.age), $\($0.salary))" })")
print("By name length: \(byNameLength.map { "\($0.name)(\($0.name.count))" })")
```

### Real-World Closure Examples

Let's see how closures are used in practical applications.

**Example 1: Event Handling System**

```swift
typealias EventHandler<T> = (T) -> Void
typealias EventFilter<T> = (T) -> Bool

class EventManager<T> {
    private var handlers: [String: [EventHandler<T>]] = [:]
    private var filters: [String: [EventFilter<T>]] = [:]
    
    func addHandler(for event: String, handler: @escaping EventHandler<T>) {
        if handlers[event] == nil {
            handlers[event] = []
        }
        handlers[event]?.append(handler)
    }
    
    func addFilter(for event: String, filter: @escaping EventFilter<T>) {
        if filters[event] == nil {
            filters[event] = []
        }
        filters[event]?.append(filter)
    }
    
    func trigger(event: String, with data: T) {
        // Check if event should be filtered
        if let eventFilters = filters[event] {
            let shouldProceed = eventFilters.allSatisfy { filter in
                filter(data)
            }
            
            if !shouldProceed {
                print("Event '\(event)' was filtered out")
                return
            }
        }
        
        // Execute handlers
        if let eventHandlers = handlers[event] {
            for handler in eventHandlers {
                handler(data)
            }
        }
    }
    
    func removeHandlers(for event: String) {
        handlers.removeValue(forKey: event)
    }
    
    func removeFilters(for event: String) {
        filters.removeValue(forKey: event)
    }
}

// Usage
let eventManager = EventManager<String>()

// Add handlers
eventManager.addHandler(for: "user_login") { username in
    print("User \(username) logged in")
}

eventManager.addHandler(for: "user_login") { username in
    print("Sending welcome email to \(username)")
}

eventManager.addHandler(for: "user_logout") { username in
    print("User \(username) logged out")
}

// Add filters
eventManager.addFilter(for: "user_login") { username in
    return username.count >= 3  // Only allow usernames with 3+ characters
}

eventManager.addFilter(for: "user_login") { username in
    return !username.contains("admin")  // Block admin logins
}

// Trigger events
eventManager.trigger(event: "user_login", with: "john_doe")
eventManager.trigger(event: "user_login", with: "admin")
eventManager.trigger(event: "user_logout", with: "john_doe")
```

**Example 2: Configuration Builder Pattern**

```swift
struct NetworkConfig {
    let baseURL: String
    let timeout: TimeInterval
    let retryCount: Int
    let headers: [String: String]
    let cachePolicy: CachePolicy
    
    enum CachePolicy {
        case noCache
        case memoryOnly
        case diskOnly
        case memoryAndDisk
    }
}

class NetworkConfigBuilder {
    private var baseURL: String = "https://api.example.com"
    private var timeout: TimeInterval = 30.0
    private var retryCount: Int = 3
    private var headers: [String: String] = [:]
    private var cachePolicy: NetworkConfig.CachePolicy = .memoryAndDisk
    
    func baseURL(_ url: String) -> NetworkConfigBuilder {
        baseURL = url
        return self
    }
    
    func timeout(_ seconds: TimeInterval) -> NetworkConfigBuilder {
        timeout = seconds
        return self
    }
    
    func retryCount(_ count: Int) -> NetworkConfigBuilder {
        retryCount = count
        return self
    }
    
    func header(_ key: String, _ value: String) -> NetworkConfigBuilder {
        headers[key] = value
        return self
    }
    
    func headers(_ newHeaders: [String: String]) -> NetworkConfigBuilder {
        headers.merge(newHeaders) { _, new in new }
        return self
    }
    
    func cachePolicy(_ policy: NetworkConfig.CachePolicy) -> NetworkConfigBuilder {
        cachePolicy = policy
        return self
    }
    
    func build() -> NetworkConfig {
        return NetworkConfig(
            baseURL: baseURL,
            timeout: timeout,
            retryCount: retryCount,
            headers: headers,
            cachePolicy: cachePolicy
        )
    }
}

// Usage with closures for customization
func createDefaultConfig() -> NetworkConfig {
    return NetworkConfigBuilder()
        .timeout(60.0)
        .retryCount(5)
        .header("User-Agent", "MyApp/1.0")
        .build()
}

func createConfig(for environment: String, customizer: (NetworkConfigBuilder) -> NetworkConfigBuilder) -> NetworkConfig {
    let builder = NetworkConfigBuilder()
        .baseURL("https://\(environment).api.example.com")
        .timeout(45.0)
    
    return customizer(builder).build()
}

// Create configurations
let defaultConfig = createDefaultConfig()
let productionConfig = createConfig(for: "prod") { builder in
    builder
        .timeout(120.0)
        .retryCount(10)
        .header("Authorization", "Bearer prod-token")
        .cachePolicy(.noCache)
}

let stagingConfig = createConfig(for: "staging") { builder in
    builder
        .timeout(90.0)
        .retryCount(7)
        .header("X-Environment", "staging")
        .cachePolicy(.memoryOnly)
}

print("Default config: \(defaultConfig.baseURL), timeout: \(defaultConfig.timeout)")
print("Production config: \(productionConfig.baseURL), timeout: \(productionConfig.timeout)")
print("Staging config: \(stagingConfig.baseURL), timeout: \(stagingConfig.timeout)")
```

**Example 3: Data Processing Pipeline**

```swift
struct DataProcessor<T, U> {
    let transform: (T) -> U
    let filter: ((U) -> Bool)?
    let validate: ((U) -> Bool)?
    
    init(transform: @escaping (T) -> U, filter: ((U) -> Bool)? = nil, validate: ((U) -> Bool)? = nil) {
        self.transform = transform
        self.filter = filter
        self.validate = validate
    }
    
    func process(_ data: [T]) -> [U] {
        var result: [U] = []
        
        for item in data {
            let transformed = transform(item)
            
            // Apply filter if present
            if let filter = filter, !filter(transformed) {
                continue
            }
            
            // Apply validation if present
            if let validate = validate, !validate(transformed) {
                print("Validation failed for item: \(transformed)")
                continue
            }
            
            result.append(transformed)
        }
        
        return result
    }
}

// Usage
let rawData = ["1", "2", "invalid", "4", "5", "6", "7", "8", "9", "10"]

// Create processing pipeline
let processor = DataProcessor<String, Int>(
    transform: { Int($0) ?? 0 },  // Convert strings to integers
    filter: { $0 > 0 },           // Only positive numbers
    validate: { $0 <= 100 }       // Numbers must be <= 100
)

let processedData = processor.process(rawData)
print("Processed data: \(processedData)")

// Create a more complex pipeline
let complexProcessor = DataProcessor<String, String>(
    transform: { str in
        guard let number = Int(str) else { return "Invalid: \(str)" }
        return "Number: \(number)"
    },
    filter: { str in
        !str.hasPrefix("Invalid")  // Filter out invalid entries
    },
    validate: { str in
        str.count > 0  // Ensure non-empty strings
    }
)

let complexResult = complexProcessor.process(rawData)
print("Complex result: \(complexResult)")

// Chain multiple processors
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

let step1 = DataProcessor<Int, Int>(
    transform: { $0 * 2 },        // Double each number
    filter: { $0 % 2 == 0 }       // Only even numbers
)

let step2 = DataProcessor<Int, String>(
    transform: { "Result: \($0)" },  // Convert to string
    validate: { $0.count > 8 }       // Ensure string is long enough
)

let step1Result = step1.process(numbers)
let step2Result = step2.process(step1Result)

print("Step 1: \(step1Result)")
print("Step 2: \(step2Result)")
```

### Advanced Closure Concepts

#### Capturing Values

Closures can capture and store references to variables and constants from the surrounding context.

```swift
func makeIncrementer(forIncrement amount: Int) -> () -> Int {
    var runningTotal = 0
    
    func incrementer() -> Int {
        runningTotal += amount
        return runningTotal
    }
    
    return incrementer
}

// Create incrementers
let incrementByTen = makeIncrementer(forIncrement: 10)
let incrementByFive = makeIncrementer(forIncrement: 5)

// Each closure captures its own copy of runningTotal
print(incrementByTen())  // 10
print(incrementByTen())  // 20
print(incrementByTen())  // 30

print(incrementByFive()) // 5
print(incrementByFive()) // 10
print(incrementByFive()) // 15

// The original incrementByTen still works
print(incrementByTen())  // 40
```

#### Escaping Closures

Escaping closures are closures that are called after the function returns. They're commonly used for asynchronous operations.

```swift
class DataManager {
    private var completionHandlers: [() -> Void] = []
    
    func fetchData(completion: @escaping (String) -> Void) {
        // Simulate async operation
        DispatchQueue.global().asyncAfter(deadline: .now() + 1) {
            let data = "Fetched data"
            completion(data)
        }
    }
    
    func storeCompletionHandler(_ handler: @escaping () -> Void) {
        completionHandlers.append(handler)
    }
    
    func executeAllHandlers() {
        for handler in completionHandlers {
            handler()
        }
        completionHandlers.removeAll()
    }
}

// Usage
let dataManager = DataManager()

dataManager.fetchData { data in
    print("Received: \(data)")
}

dataManager.storeCompletionHandler {
    print("Stored handler executed")
}

// Execute stored handlers
dataManager.executeAllHandlers()
```

#### Autoclosures

Autoclosures automatically wrap expressions in closures, allowing you to delay evaluation.

```swift
func logIfNeeded(_ message: @autoclosure () -> String, when condition: Bool) {
    if condition {
        print(message())
    }
}

// Usage
let expensiveOperation = "This is an expensive operation"
logIfNeeded(expensiveOperation, when: false)  // Message not printed
logIfNeeded(expensiveOperation, when: true)   // Message printed

// Without @autoclosure, you'd need to write:
func logIfNeeded(_ message: () -> String, when condition: Bool) {
    if condition {
        print(message())
    }
}

// Usage would be more verbose:
logIfNeeded({ expensiveOperation }, when: true)
```

### Best Practices for Closures

1. **Use trailing closures** when the closure is the last parameter
2. **Keep closures focused** - Each closure should do one thing well
3. **Use meaningful parameter names** - Avoid single-letter names unless very simple
4. **Consider closure capture** - Be aware of what values are captured
5. **Use escaping closures carefully** - They can create retain cycles
6. **Prefer functional programming** - Use map, filter, reduce for data transformations
7. **Use type inference** when the types are obvious from context
8. **Document complex closures** - Add comments for complex logic

**Closure design principles:**

- **Single responsibility** - Each closure should have one clear purpose
- **Readability** - Choose the right level of verbosity for your team
- **Performance** - Be aware of closure capture costs
- **Reusability** - Design closures that can be reused in different contexts
- **Testing** - Closures should be testable and predictable

Closures are one of Swift's most powerful features, enabling functional programming patterns, elegant data transformations, and flexible callback systems. Mastering closures will make you a more effective Swift developer and open up new ways to solve problems.

---

## Enumerations

Enumerations (enums) are a powerful way to define a group of related values in Swift. They're much more feature-rich than enums in many other languages, supporting associated values, raw values, and methods. Enums are essential for modeling state, options, and data that has a finite set of possible values.

### Understanding Enumerations

Enumerations define a common type for a group of related values, enabling you to work with those values in a type-safe way within your code.

#### Basic Enum Syntax

```swift
// Simple enum with cases
enum Direction {
    case north
    case south
    case east
    case west
}

// Enum with multiple cases on one line
enum CompassDirection {
    case north, south, east, west
}

// Using enums
let currentDirection = Direction.north
let targetDirection = Direction.south

// Switch statements work perfectly with enums
switch currentDirection {
case .north:
    print("Heading north")
case .south:
    print("Heading south")
case .east:
    print("Heading east")
case .west:
    print("Heading west")
}

// Enum with String raw values
enum Planet: String {
    case mercury = "Mercury"
    case venus = "Venus"
    case earth = "Earth"
    case mars = "Mars"
    case jupiter = "Jupiter"
    case saturn = "Saturn"
    case uranus = "Uranus"
    case neptune = "Neptune"
}

// Accessing raw values
let earth = Planet.earth
let earthName = earth.rawValue  // "Earth"

// Creating from raw value
if let planet = Planet(rawValue: "Mars") {
    print("Found planet: \(planet)")
}
```

**Enum benefits:**
- **Type safety** - Compiler ensures you only use valid enum cases
- **Clear intent** - Code is more readable and self-documenting
- **Exhaustive handling** - Switch statements must handle all cases
- **Namespace isolation** - Enum cases don't conflict with other code

#### Enums with Associated Values

Associated values allow you to store additional data with each enum case, making them incredibly powerful for modeling complex data.

```swift
// Enum with associated values for API responses
enum APIResponse {
    case success(data: String, statusCode: Int)
    case failure(error: String, statusCode: Int)
    case loading
    case noConnection
}

// Enum for different types of shapes
enum Shape {
    case circle(radius: Double)
    case rectangle(width: Double, height: Double)
    case triangle(base: Double, height: Double)
    case square(side: Double)
}

// Enum for user authentication states
enum AuthState {
    case notAuthenticated
    case authenticating
    case authenticated(user: User, token: String)
    case failed(error: AuthError)
}

// Using associated values
let response = APIResponse.success(data: "User data", statusCode: 200)
let circle = Shape.circle(radius: 5.0)
let rectangle = Shape.rectangle(width: 10.0, height: 5.0)

// Extracting associated values
switch response {
case .success(let data, let statusCode):
    print("Success: \(data) with status \(statusCode)")
case .failure(let error, let statusCode):
    print("Error: \(error) with status \(statusCode)")
case .loading:
    print("Request is loading...")
case .noConnection:
    print("No internet connection")
}

// Pattern matching with associated values
switch circle {
case .circle(let radius):
    let area = Double.pi * radius * radius
    print("Circle area: \(area)")
case .rectangle(let width, let height):
    let area = width * height
    print("Rectangle area: \(area)")
case .triangle(let base, let height):
    let area = 0.5 * base * height
    print("Triangle area: \(area)")
case .square(let side):
    let area = side * side
    print("Square area: \(area)")
}
```

#### Enums with Raw Values

Raw values provide a way to assign predefined values to enum cases, useful for interfacing with external systems or databases.

```swift
// Enum with integer raw values
enum HTTPStatusCode: Int {
    case ok = 200
    case created = 201
    case accepted = 202
    case noContent = 204
    case badRequest = 400
    case unauthorized = 401
    case forbidden = 403
    case notFound = 404
    case internalServerError = 500
    case notImplemented = 501
    case badGateway = 502
    case serviceUnavailable = 503
}

// Enum with string raw values
enum NetworkError: String {
    case noConnection = "No internet connection"
    case timeout = "Request timed out"
    case invalidResponse = "Invalid response from server"
    case serverError = "Server error occurred"
}

// Enum with character raw values
enum ASCIICharacter: Character {
    case tab = "\t"
    case newline = "\n"
    case carriageReturn = "\r"
    case space = " "
}

// Using raw values
let status = HTTPStatusCode.ok
print("Status code: \(status.rawValue)")  // 200

let error = NetworkError.timeout
print("Error message: \(error.rawValue)")  // "Request timed out"

// Creating from raw values
if let statusCode = HTTPStatusCode(rawValue: 404) {
    print("Found status: \(statusCode)")  // .notFound
}

if let networkError = NetworkError(rawValue: "No internet connection") {
    print("Found error: \(networkError)")  // .noConnection
}
```

#### Enums with Methods and Computed Properties

Enums can have methods and computed properties, making them behave like classes or structs.

```swift
enum Shape {
    case circle(radius: Double)
    case rectangle(width: Double, height: Double)
    case triangle(base: Double, height: Double)
    case square(side: Double)
    
    // Computed property for area
    var area: Double {
        switch self {
        case .circle(let radius):
            return Double.pi * radius * radius
        case .rectangle(let width, let height):
            return width * height
        case .triangle(let base, let height):
            return 0.5 * base * height
        case .square(let side):
            return side * side
        }
    }
    
    // Computed property for perimeter
    var perimeter: Double {
        switch self {
        case .circle(let radius):
            return 2 * Double.pi * radius
        case .rectangle(let width, let height):
            return 2 * (width + height)
        case .triangle(let base, let height):
            // For simplicity, assuming equilateral triangle
            let side = sqrt(base * base + height * height)
            return base + 2 * side
        case .square(let side):
            return 4 * side
        }
    }
    
    // Method to describe the shape
    func describe() -> String {
        switch self {
        case .circle(let radius):
            return "Circle with radius \(radius)"
        case .rectangle(let width, let height):
            return "Rectangle \(width) × \(height)"
        case .triangle(let base, let height):
            return "Triangle with base \(base) and height \(height)"
        case .square(let side):
            return "Square with side \(side)"
        }
    }
    
    // Method to check if shape is regular
    var isRegular: Bool {
        switch self {
        case .circle, .square:
            return true
        case .rectangle(let width, let height):
            return width == height
        case .triangle:
            return false  // Assuming not equilateral
        }
    }
}

// Usage
let circle = Shape.circle(radius: 5.0)
let rectangle = Shape.rectangle(width: 10.0, height: 5.0)

print(circle.describe())      // "Circle with radius 5.0"
print("Area: \(circle.area)") // "Area: 78.54..."
print("Perimeter: \(circle.perimeter)") // "Perimeter: 31.42..."

print(rectangle.describe())   // "Rectangle 10.0 × 5.0"
print("Area: \(rectangle.area)") // "Area: 50.0"
print("Is regular: \(rectangle.isRegular)") // "Is regular: false"
```

### Real-World Enum Examples

Let's see how enums are used in practical applications.

**Example 1: Game State Management**

```swift
enum GameState {
    case mainMenu
    case loading(progress: Double)
    case playing(level: Int, score: Int, lives: Int)
    case paused(level: Int, score: Int, lives: Int)
    case gameOver(finalScore: Int, levelReached: Int, reason: GameOverReason)
    case settings
}

enum GameOverReason {
    case noLivesLeft
    case timeExpired
    case levelFailed
    case playerQuit
}

class GameManager {
    private var currentState: GameState = .mainMenu
    
    func updateState(_ newState: GameState) {
        let oldState = currentState
        currentState = newState
        
        // Handle state transitions
        handleStateTransition(from: oldState, to: newState)
    }
    
    private func handleStateTransition(from oldState: GameState, to newState: GameState) {
        switch (oldState, newState) {
        case (.mainMenu, .loading):
            print("Starting to load game...")
            startLoading()
            
        case (.loading, .playing):
            print("Game loaded, starting play...")
            startGame()
            
        case (.playing, .paused):
            print("Game paused")
            pauseGame()
            
        case (.paused, .playing):
            print("Game resumed")
            resumeGame()
            
        case (.playing, .gameOver):
            print("Game over!")
            endGame()
            
        case (.gameOver, .mainMenu):
            print("Returning to main menu")
            returnToMainMenu()
            
        case (.mainMenu, .settings):
            print("Opening settings")
            openSettings()
            
        case (.settings, .mainMenu):
            print("Closing settings")
            closeSettings()
            
        default:
            print("Invalid state transition from \(oldState) to \(newState)")
        }
    }
    
    func getCurrentLevel() -> Int? {
        switch currentState {
        case .playing(let level, _, _), .paused(let level, _, _):
            return level
        default:
            return nil
        }
    }
    
    func getCurrentScore() -> Int? {
        switch currentState {
        case .playing(_, let score, _), .paused(_, let score, _):
            return score
        case .gameOver(let finalScore, _, _):
            return finalScore
        default:
            return nil
        }
    }
    
    func canPause() -> Bool {
        if case .playing = currentState {
            return true
        }
        return false
    }
    
    func canResume() -> Bool {
        if case .paused = currentState {
            return true
        }
        return false
    }
    
    // Placeholder methods
    private func startLoading() { /* Implementation */ }
    private func startGame() { /* Implementation */ }
    private func pauseGame() { /* Implementation */ }
    private func resumeGame() { /* Implementation */ }
    private func endGame() { /* Implementation */ }
    private func returnToMainMenu() { /* Implementation */ }
    private func openSettings() { /* Implementation */ }
    private func closeSettings() { /* Implementation */ }
}

// Usage
let gameManager = GameManager()

// Simulate game flow
gameManager.updateState(.loading(progress: 0.0))
gameManager.updateState(.loading(progress: 0.5))
gameManager.updateState(.loading(progress: 1.0))
gameManager.updateState(.playing(level: 1, score: 0, lives: 3))

// Check current state
if let level = gameManager.getCurrentLevel() {
    print("Current level: \(level)")
}

if let score = gameManager.getCurrentScore() {
    print("Current score: \(score)")
}

print("Can pause: \(gameManager.canPause())")    // true
print("Can resume: \(gameManager.canResume())")  // false

// Pause and resume
gameManager.updateState(.paused(level: 1, score: 100, lives: 3))
print("Can pause: \(gameManager.canPause())")    // false
print("Can resume: \(gameManager.canResume())")  // true

gameManager.updateState(.playing(level: 1, score: 100, lives: 3))
gameManager.updateState(.gameOver(finalScore: 500, levelReached: 2, reason: .noLivesLeft))
gameManager.updateState(.mainMenu)
```

**Example 2: Network Request System**

```swift
enum HTTPMethod: String {
    case get = "GET"
    case post = "POST"
    case put = "PUT"
    case delete = "DELETE"
    case patch = "PATCH"
    case head = "HEAD"
    case options = "OPTIONS"
}

enum RequestPriority {
    case low
    case normal
    case high
    case critical
}

enum RequestState {
    case pending
    case inProgress(startTime: Date, retryCount: Int)
    case completed(response: HTTPResponse)
    case failed(error: NetworkError, retryCount: Int)
    case cancelled
}

enum NetworkError: Error, LocalizedError {
    case noConnection
    case timeout(duration: TimeInterval)
    case invalidResponse(statusCode: Int, message: String)
    case serverError(statusCode: Int, message: String)
    case authenticationFailed
    case rateLimited(retryAfter: TimeInterval)
    
    var errorDescription: String? {
        switch self {
        case .noConnection:
            return "No internet connection available"
        case .timeout(let duration):
            return "Request timed out after \(duration) seconds"
        case .invalidResponse(let statusCode, let message):
            return "Invalid response (\(statusCode)): \(message)"
        case .serverError(let statusCode, let message):
            return "Server error (\(statusCode)): \(message)"
        case .authenticationFailed:
            return "Authentication failed"
        case .rateLimited(let retryAfter):
            return "Rate limited. Try again in \(retryAfter) seconds"
        }
    }
}

struct HTTPResponse {
    let statusCode: Int
    let data: Data
    let headers: [String: String]
}

struct NetworkRequest {
    let id: UUID
    let url: URL
    let method: HTTPMethod
    let headers: [String: String]
    let body: Data?
    let priority: RequestPriority
    var state: RequestState = .pending
    
    init(url: URL, method: HTTPMethod = .get, headers: [String: String] = [:], body: Data? = nil, priority: RequestPriority = .normal) {
        self.id = UUID()
        self.url = url
        self.method = method
        self.headers = headers
        self.body = body
        self.priority = priority
    }
}

class NetworkManager {
    private var requests: [UUID: NetworkRequest] = [:]
    
    func submitRequest(_ request: NetworkRequest) {
        requests[request.id] = request
        processRequest(request.id)
    }
    
    private func processRequest(_ requestId: UUID) {
        guard var request = requests[requestId] else { return }
        
        // Update state to in progress
        request.state = .inProgress(startTime: Date(), retryCount: 0)
        requests[requestId] = request
        
        // Simulate network request
        DispatchQueue.global().asyncAfter(deadline: .now() + 2) {
            self.completeRequest(requestId, success: Bool.random())
        }
    }
    
    private func completeRequest(_ requestId: UUID, success: Bool) {
        guard var request = requests[requestId] else { return }
        
        if success {
            let response = HTTPResponse(
                statusCode: 200,
                data: "Success".data(using: .utf8)!,
                headers: [:]
            )
            request.state = .completed(response: response)
        } else {
            let error = NetworkError.serverError(statusCode: 500, message: "Internal server error")
            request.state = .failed(error: error, retryCount: 0)
        }
        
        requests[requestId] = request
        print("Request \(requestId) completed with state: \(request.state)")
    }
    
    func getRequestState(_ requestId: UUID) -> RequestState? {
        return requests[requestId]?.state
    }
    
    func cancelRequest(_ requestId: UUID) {
        guard var request = requests[requestId] else { return }
        request.state = .cancelled
        requests[requestId] = request
    }
    
    func retryRequest(_ requestId: UUID) {
        guard let request = requests[requestId] else { return }
        
        if case .failed(_, let retryCount) = request.state, retryCount < 3 {
            var updatedRequest = request
            updatedRequest.state = .inProgress(startTime: Date(), retryCount: retryCount + 1)
            requests[requestId] = updatedRequest
            
            // Process the retry
            processRequest(requestId)
        }
    }
}

// Usage
let networkManager = NetworkManager()

// Create requests
let getRequest = NetworkRequest(
    url: URL(string: "https://api.example.com/users")!,
    method: .get,
    priority: .normal
)

let postRequest = NetworkRequest(
    url: URL(string: "https://api.example.com/users")!,
    method: .post,
    headers: ["Content-Type": "application/json"],
    body: "{\"name\": \"John\"}".data(using: .utf8),
    priority: .high
)

// Submit requests
networkManager.submitRequest(getRequest)
networkManager.submitRequest(postRequest)

// Check request states
DispatchQueue.main.asyncAfter(deadline: .now() + 3) {
    if let state = networkManager.getRequestState(getRequest.id) {
        print("GET request state: \(state)")
    }
    
    if let state = networkManager.getRequestState(postRequest.id) {
        print("POST request state: \(state)")
    }
}
```

**Example 3: Result Type Implementation**

```swift
enum Result<Success, Failure: Error> {
    case success(Success)
    case failure(Failure)
    
    // Computed properties
    var isSuccess: Bool {
        if case .success = self {
            return true
        }
        return false
    }
    
    var isFailure: Bool {
        if case .failure = self {
            return true
        }
        return false
    }
    
    // Methods for extracting values
    func get() throws -> Success {
        switch self {
        case .success(let value):
            return value
        case .failure(let error):
            throw error
        }
    }
    
    func getOrElse(_ defaultValue: Success) -> Success {
        switch self {
        case .success(let value):
            return value
        case .failure:
            return defaultValue
        }
    }
    
    // Transformation methods
    func map<NewSuccess>(_ transform: (Success) -> NewSuccess) -> Result<NewSuccess, Failure> {
        switch self {
        case .success(let value):
            return .success(transform(value))
        case .failure(let error):
            return .failure(error)
        }
    }
    
    func mapError<NewFailure: Error>(_ transform: (Failure) -> NewFailure) -> Result<Success, NewFailure> {
        switch self {
        case .success(let value):
            return .success(value)
        case .failure(let error):
            return .failure(transform(error))
        }
    }
    
    func flatMap<NewSuccess>(_ transform: (Success) -> Result<NewSuccess, Failure>) -> Result<NewSuccess, Failure> {
        switch self {
        case .success(let value):
            return transform(value)
        case .failure(let error):
            return .failure(error)
        }
    }
    
    // Error handling
    func `catch`(_ handler: (Failure) -> Success) -> Success {
        switch self {
        case .success(let value):
            return value
        case .failure(let error):
            return handler(error)
        }
    }
}

// Usage
enum ValidationError: Error {
    case emptyString
    case tooShort(minLength: Int)
    case invalidCharacters
}

func validateUsername(_ username: String) -> Result<String, ValidationError> {
    if username.isEmpty {
        return .failure(.emptyString)
    }
    
    if username.count < 3 {
        return .failure(.tooShort(minLength: 3))
    }
    
    if username.contains(" ") {
        return .failure(.invalidCharacters)
    }
    
    return .success(username)
}

func createUser(username: String) -> Result<User, ValidationError> {
    let validationResult = validateUsername(username)
    
    return validationResult.map { validUsername in
        return User(username: validUsername)
    }
}

// Simulate user creation
let usernames = ["", "ab", "john doe", "john_doe", "valid_user"]

for username in usernames {
    let result = createUser(username: username)
    
    switch result {
    case .success(let user):
        print("Successfully created user: \(user.username)")
    case .failure(let error):
        print("Failed to create user '\(username)': \(error)")
    }
}

// Using Result methods
let validationResult = validateUsername("john_doe")

if validationResult.isSuccess {
    print("Username is valid")
}

let username = validationResult.getOrElse("default_user")
print("Username: \(username)")

let userResult = validationResult.map { username in
    return User(username: username)
}

let errorResult = validationResult.mapError { error in
    return "Validation failed: \(error)"
}
```

### Advanced Enum Features

#### Recursive Enums

Recursive enums are useful for representing hierarchical data structures like trees or expressions.

```swift
// Recursive enum for arithmetic expressions
enum ArithmeticExpression {
    case number(Int)
    case addition(ArithmeticExpression, ArithmeticExpression)
    case multiplication(ArithmeticExpression, ArithmeticExpression)
    case subtraction(ArithmeticExpression, ArithmeticExpression)
    case division(ArithmeticExpression, ArithmeticExpression)
    
    // Method to evaluate the expression
    func evaluate() -> Int {
        switch self {
        case .number(let value):
            return value
        case .addition(let left, let right):
            return left.evaluate() + right.evaluate()
        case .multiplication(let left, let right):
            return left.evaluate() * right.evaluate()
        case .subtraction(let left, let right):
            return left.evaluate() - right.evaluate()
        case .division(let left, let right):
            let rightValue = right.evaluate()
            guard rightValue != 0 else {
                fatalError("Division by zero")
            }
            return left.evaluate() / rightValue
        }
    }
    
    // Method to get string representation
    var description: String {
        switch self {
        case .number(let value):
            return "\(value)"
        case .addition(let left, let right):
            return "(\(left.description) + \(right.description))"
        case .multiplication(let left, let right):
            return "(\(left.description) × \(right.description))"
        case .subtraction(let left, let right):
            return "(\(left.description) - \(right.description))"
        case .division(let left, let right):
            return "(\(left.description) ÷ \(right.description))"
        }
    }
}

// Usage
let expression = ArithmeticExpression.addition(
    .number(5),
    .multiplication(.number(3), .number(4))
)

print("Expression: \(expression.description)")  // "(5 + (3 × 4))"
print("Result: \(expression.evaluate())")      // 17
```

#### Enums with Protocols

Enums can conform to protocols, making them even more powerful.

```swift
protocol Describable {
    var description: String { get }
}

protocol Comparable {
    static func < (lhs: Self, rhs: Self) -> Bool
}

enum Card: Describable, Comparable {
    case ace
    case two, three, four, five, six, seven, eight, nine, ten
    case jack, queen, king
    
    var description: String {
        switch self {
        case .ace: return "Ace"
        case .two: return "2"
        case .three: return "3"
        case .four: return "4"
        case .five: return "5"
        case .six: return "6"
        case .seven: return "7"
        case .eight: return "8"
        case .nine: return "9"
        case .ten: return "10"
        case .jack: return "Jack"
        case .queen: return "Queen"
        case .king: return "King"
        }
    }
    
    var value: Int {
        switch self {
        case .ace: return 14
        case .two: return 2
        case .three: return 3
        case .four: return 4
        case .five: return 5
        case .six: return 6
        case .seven: return 7
        case .eight: return 8
        case .nine: return 9
        case .ten: return 10
        case .jack: return 11
        case .queen: return 12
        case .king: return 13
        }
    }
    
    static func < (lhs: Card, rhs: Card) -> Bool {
        return lhs.value < rhs.value
    }
}

// Usage
let cards: [Card] = [.ace, .king, .queen, .jack, .ten]
let sortedCards = cards.sorted()

for card in sortedCards {
    print("\(card.description) (value: \(card.value))")
}
```

### Best Practices for Enumerations

1. **Use enums for finite sets** - When you have a known, limited set of values
2. **Leverage associated values** - Store additional data with enum cases
3. **Use raw values for external systems** - When interfacing with APIs or databases
4. **Implement methods and properties** - Make enums more than just value containers
5. **Use exhaustive switch statements** - Let the compiler ensure all cases are handled
6. **Consider protocol conformance** - Make enums work with Swift's type system
7. **Use descriptive case names** - Make your code self-documenting
8. **Group related functionality** - Keep enum methods focused and cohesive

**Enum design principles:**

- **Single responsibility** - Each enum should represent one concept
- **Completeness** - Include all possible values for the concept
- **Extensibility** - Design enums that can grow with your application
- **Performance** - Enums are value types and very efficient
- **Type safety** - Use enums to prevent invalid states

Enumerations are one of Swift's most powerful features, providing type safety, clarity, and extensibility. They're essential for modeling state, options, and data that has a finite set of possible values. Mastering enums will help you write more robust, maintainable code.

---

## Structures and Classes

Structures and classes are the fundamental building blocks of Swift programs. They define properties and methods to add functionality, but they have important differences in how they're stored and passed around. Understanding these differences is crucial for writing efficient, correct Swift code.

### Understanding Value Types vs Reference Types

The most fundamental difference between structures and classes is how they're stored and passed around in memory.

#### Value Types (Structures)

Structures are value types, meaning they're copied when assigned to a new variable or passed to a function.

```swift
struct Point {
    var x: Double
    var y: Double
    
    func distance(to other: Point) -> Double {
        let deltaX = x - other.x
        let deltaY = y - other.y
        return sqrt(deltaX * deltaX + deltaY * deltaY)
    }
    
    mutating func move(by deltaX: Double, deltaY: Double) {
        x += deltaX
        y += deltaY
    }
}

// Creating and using structures
var point1 = Point(x: 0, y: 0)
var point2 = point1  // Creates a copy

point2.x = 5
point2.y = 5

print("Point 1: (\(point1.x), \(point1.y))")  // (0, 0)
print("Point 2: (\(point2.x), \(point2.y))")  // (5, 5)
print("Distance: \(point1.distance(to: point2))")  // 7.07...

// Modifying a structure
point1.move(by: 3, deltaY: 4)
print("Point 1 after move: (\(point1.x), \(point1.y))")  // (3, 4)
```

**Key characteristics of value types:**
- **Copied on assignment** - Each variable gets its own copy
- **Independent state** - Changes to one copy don't affect others
- **Thread-safe** - No shared mutable state
- **Predictable behavior** - Easy to reason about
- **Memory efficient** - Can be stored on the stack

#### Reference Types (Classes)

Classes are reference types, meaning they're shared when assigned to new variables or passed to functions.

```swift
class Person {
    var name: String
    var age: Int
    var address: String
    
    init(name: String, age: Int, address: String) {
        self.name = name
        self.age = age
        self.address = address
    }
    
    func haveBirthday() {
        age += 1
        print("\(name) is now \(age) years old!")
    }
    
    func move(to newAddress: String) {
        address = newAddress
        print("\(name) moved to \(newAddress)")
    }
}

// Creating and using classes
let person1 = Person(name: "Alice", age: 25, address: "123 Main St")
let person2 = person1  // Creates a reference, not a copy

person2.haveBirthday()  // This affects the same object
person2.move(to: "456 Oak Ave")

print("Person 1: \(person1.name), Age: \(person1.age), Address: \(person1.address)")
print("Person 2: \(person2.name), Age: \(person2.age), Address: \(person2.address)")

// Both variables point to the same object
print("Same object: \(person1 === person2)")  // true
```

**Key characteristics of reference types:**
- **Shared on assignment** - Multiple variables can reference the same object
- **Shared state** - Changes affect all references to the object
- **Identity** - Can check if two variables reference the same object
- **Inheritance** - Can create subclasses
- **Reference counting** - Swift manages memory automatically

### When to Use Structures vs Classes

Choosing between structures and classes depends on your specific use case and requirements.

#### Use Structures When:

```swift
// ✅ Good use cases for structures

// 1. Simple data models
struct User {
    let id: UUID
    var name: String
    var email: String
    var isActive: Bool
}

// 2. Mathematical concepts
struct Vector2D {
    var x: Double
    var y: Double
    
    var magnitude: Double {
        return sqrt(x * x + y * y)
    }
    
    var normalized: Vector2D {
        let mag = magnitude
        return Vector2D(x: x / mag, y: y / mag)
    }
    
    static func + (lhs: Vector2D, rhs: Vector2D) -> Vector2D {
        return Vector2D(x: lhs.x + rhs.x, y: lhs.y + rhs.y)
    }
    
    static func - (lhs: Vector2D, rhs: Vector2D) -> Vector2D {
        return Vector2D(x: lhs.x - rhs.x, y: lhs.y - rhs.y)
    }
}

// 3. Configuration objects
struct AppConfig {
    let apiBaseURL: String
    let timeout: TimeInterval
    let maxRetries: Int
    let enableLogging: Bool
}

// 4. Immutable data
struct Constants {
    static let maxUsers = 1000
    static let defaultTimeout: TimeInterval = 30.0
    static let supportedLanguages = ["en", "es", "fr", "de"]
}
```

**Structure advantages:**
- **Predictable copying** - No unexpected shared state
- **Thread safety** - Can be safely used across threads
- **Performance** - Often faster for small, simple data
- **Memory efficiency** - Can be stored on the stack
- **Immutability** - Easy to make immutable with `let`

#### Use Classes When:

```swift
// ✅ Good use cases for classes

// 1. Objects with identity
class UserAccount {
    let id: UUID
    var username: String
    var email: String
    var isActive: Bool
    var lastLoginDate: Date?
    
    init(id: UUID, username: String, email: String) {
        self.id = id
        self.username = username
        self.email = email
        self.isActive = true
    }
    
    func login() {
        lastLoginDate = Date()
        print("User \(username) logged in")
    }
    
    func deactivate() {
        isActive = false
        print("User \(username) deactivated")
    }
}

// 2. Objects that need inheritance
class Animal {
    let name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    func makeSound() {
        print("Some animal sound")
    }
}

class Dog: Animal {
    var breed: String
    
    init(name: String, age: Int, breed: String) {
        self.breed = breed
        super.init(name: name, age: age)
    }
    
    override func makeSound() {
        print("Woof!")
    }
    
    func fetch() {
        print("\(name) is fetching the ball")
    }
}

// 3. Objects that manage resources
class FileManager {
    private var openFiles: [String: FileHandle] = [:]
    
    func openFile(at path: String) -> Bool {
        // Implementation to open file
        print("Opening file at: \(path)")
        return true
    }
    
    func closeFile(at path: String) {
        // Implementation to close file
        print("Closing file at: \(path)")
    }
    
    deinit {
        // Clean up resources
        for (path, _) in openFiles {
            closeFile(at: path)
        }
    }
}

// 4. Objects that need to be shared
class NetworkManager {
    static let shared = NetworkManager()
    
    private var activeConnections: [String: URLSessionDataTask] = [:]
    
    private init() {}
    
    func makeRequest(to url: String, completion: @escaping (Data?) -> Void) {
        // Implementation
        print("Making request to: \(url)")
    }
    
    func cancelRequest(to url: String) {
        if let task = activeConnections[url] {
            task.cancel()
            activeConnections.removeValue(forKey: url)
        }
    }
}
```

**Class advantages:**
- **Identity** - Can check if two variables reference the same object
- **Inheritance** - Can create subclasses and override methods
- **Shared state** - Multiple parts of code can modify the same object
- **Reference semantics** - Changes propagate to all references
- **Customization** - Can customize initialization and deinitialization

### Real-World Examples

Let's see how structures and classes are used in practical applications.

**Example 1: E-commerce System**

```swift
// Structures for data models
struct Product {
    let id: UUID
    let name: String
    let description: String
    let price: Decimal
    let category: ProductCategory
    let inStock: Bool
    let tags: Set<String>
    
    var formattedPrice: String {
        return "$\(price)"
    }
    
    var isOnSale: Bool {
        return price < 50.0
    }
}

enum ProductCategory: String, CaseIterable {
    case electronics = "Electronics"
    case clothing = "Clothing"
    case books = "Books"
    case home = "Home & Garden"
    case sports = "Sports & Outdoors"
}

struct OrderItem {
    let product: Product
    var quantity: Int
    
    var totalPrice: Decimal {
        return product.price * Decimal(quantity)
    }
}

struct Address {
    let street: String
    let city: String
    let state: String
    let zipCode: String
    let country: String
    
    var fullAddress: String {
        return "\(street), \(city), \(state) \(zipCode), \(country)"
    }
}

// Classes for business logic
class ShoppingCart {
    private var items: [OrderItem] = []
    private let user: UserAccount
    
    init(user: UserAccount) {
        self.user = user
    }
    
    func addItem(_ product: Product, quantity: Int = 1) {
        if let existingIndex = items.firstIndex(where: { $0.product.id == product.id }) {
            items[existingIndex].quantity += quantity
        } else {
            let newItem = OrderItem(product: product, quantity: quantity)
            items.append(newItem)
        }
        
        print("Added \(quantity) \(product.name) to cart")
    }
    
    func removeItem(_ product: Product) {
        items.removeAll { $0.product.id == product.id }
        print("Removed \(product.name) from cart")
    }
    
    func updateQuantity(for product: Product, to quantity: Int) {
        if let index = items.firstIndex(where: { $0.product.id == product.id }) {
            if quantity <= 0 {
                items.remove(at: index)
            } else {
                items[index].quantity = quantity
            }
        }
    }
    
    var totalItems: Int {
        return items.reduce(0) { $0 + $1.quantity }
    }
    
    var totalPrice: Decimal {
        return items.reduce(0) { $0 + $1.totalPrice }
    }
    
    func clear() {
        items.removeAll()
        print("Cart cleared")
    }
    
    func checkout(shippingAddress: Address) -> Order? {
        guard !items.isEmpty else {
            print("Cannot checkout empty cart")
            return nil
        }
        
        let order = Order(
            id: UUID(),
            user: user,
            items: items,
            shippingAddress: shippingAddress,
            status: .pending
        )
        
        // Clear cart after successful checkout
        items.removeAll()
        
        return order
    }
}

class Order {
    let id: UUID
    let user: UserAccount
    let items: [OrderItem]
    let shippingAddress: Address
    let orderDate: Date
    var status: OrderStatus
    var trackingNumber: String?
    
    init(id: UUID, user: UserAccount, items: [OrderItem], shippingAddress: Address, status: OrderStatus) {
        self.id = id
        self.user = user
        self.items = items
        self.shippingAddress = shippingAddress
        self.orderDate = Date()
        self.status = status
    }
    
    var totalItems: Int {
        return items.reduce(0) { $0 + $1.quantity }
    }
    
    var totalPrice: Decimal {
        return items.reduce(0) { $0 + $1.totalPrice }
    }
    
    func updateStatus(_ newStatus: OrderStatus) {
        status = newStatus
        print("Order \(id) status updated to: \(newStatus)")
    }
    
    func addTrackingNumber(_ number: String) {
        trackingNumber = number
        print("Tracking number added: \(number)")
    }
}

enum OrderStatus: String, CaseIterable {
    case pending = "Pending"
    case confirmed = "Confirmed"
    case processing = "Processing"
    case shipped = "Shipped"
    case delivered = "Delivered"
    case cancelled = "Cancelled"
    
    var canBeModified: Bool {
        switch self {
        case .pending, .confirmed:
            return true
        default:
            return false
        }
    }
}

// Usage
let user = UserAccount(id: UUID(), username: "john_doe", email: "john@example.com")
let cart = ShoppingCart(user: user)

let product1 = Product(
    id: UUID(),
    name: "iPhone 15",
    description: "Latest iPhone model",
    price: 999.99,
    category: .electronics,
    inStock: true,
    tags: ["smartphone", "apple", "5g"]
)

let product2 = Product(
    id: UUID(),
    name: "Wireless Headphones",
    description: "Noise-cancelling headphones",
    price: 299.99,
    category: .electronics,
    inStock: true,
    tags: ["audio", "wireless", "noise-cancelling"]
)

// Add items to cart
cart.addItem(product1, quantity: 1)
cart.addItem(product2, quantity: 2)

print("Cart total: \(cart.totalItems) items, $\(cart.totalPrice)")

// Checkout
let address = Address(
    street: "123 Main St",
    city: "New York",
    state: "NY",
    zipCode: "10001",
    country: "USA"
)

if let order = cart.checkout(shippingAddress: address) {
    print("Order created: \(order.id)")
    print("Order total: $\(order.totalPrice)")
    
    // Update order status
    order.updateStatus(.confirmed)
    order.updateStatus(.processing)
    order.addTrackingNumber("1Z999AA1234567890")
}
```

**Example 2: Game Engine Architecture**

```swift
// Structures for mathematical concepts
struct Vector3D {
    var x: Double
    var y: Double
    var z: Double
    
    static let zero = Vector3D(x: 0, y: 0, z: 0)
    
    var magnitude: Double {
        return sqrt(x * x + y * y + z * z)
    }
    
    var normalized: Vector3D {
        let mag = magnitude
        guard mag > 0 else { return .zero }
        return Vector3D(x: x / mag, y: y / mag, z: z / mag)
    }
    
    static func + (lhs: Vector3D, rhs: Vector3D) -> Vector3D {
        return Vector3D(x: lhs.x + rhs.x, y: lhs.y + rhs.y, z: lhs.z + rhs.z)
    }
    
    static func - (lhs: Vector3D, rhs: Vector3D) -> Vector3D {
        return Vector3D(x: lhs.x - rhs.x, y: lhs.y - rhs.y, z: lhs.z - rhs.z)
    }
    
    static func * (lhs: Vector3D, rhs: Double) -> Vector3D {
        return Vector3D(x: lhs.x * rhs, y: lhs.y * rhs, z: lhs.z * rhs)
    }
}

struct Transform {
    var position: Vector3D
    var rotation: Vector3D
    var scale: Vector3D
    
    static let identity = Transform(
        position: .zero,
        rotation: .zero,
        scale: Vector3D(x: 1, y: 1, z: 1)
    )
    
    var matrix: [[Double]] {
        // Simplified matrix representation
        return [
            [scale.x, 0, 0, position.x],
            [0, scale.y, 0, position.y],
            [0, 0, scale.z, position.z],
            [0, 0, 0, 1]
        ]
    }
}

// Classes for game objects
class GameObject {
    let id: UUID
    var name: String
    var transform: Transform
    var isActive: Bool
    var components: [Component]
    
    init(id: UUID, name: String, transform: Transform = .identity) {
        self.id = id
        self.name = name
        self.transform = transform
        self.isActive = true
        self.components = []
    }
    
    func addComponent(_ component: Component) {
        component.gameObject = self
        components.append(component)
    }
    
    func getComponent<T: Component>(ofType type: T.Type) -> T? {
        return components.first { $0 is T } as? T
    }
    
    func update(deltaTime: TimeInterval) {
        guard isActive else { return }
        
        for component in components {
            component.update(deltaTime: deltaTime)
        }
    }
    
    func destroy() {
        isActive = false
        print("GameObject '\(name)' destroyed")
    }
}

protocol Component: AnyObject {
    var gameObject: GameObject? { get set }
    func update(_ deltaTime: TimeInterval)
}

class TransformComponent: Component {
    weak var gameObject: GameObject?
    
    func update(_ deltaTime: TimeInterval) {
        // Update transform logic
    }
    
    func move(by offset: Vector3D) {
        gameObject?.transform.position = gameObject?.transform.position ?? .zero + offset
    }
    
    func rotate(by rotation: Vector3D) {
        gameObject?.transform.rotation = gameObject?.transform.rotation ?? .zero + rotation
    }
}

class RenderComponent: Component {
    weak var gameObject: GameObject?
    var mesh: String
    var material: String
    var isVisible: Bool = true
    
    init(mesh: String, material: String) {
        self.mesh = mesh
        self.material = material
    }
    
    func update(_ deltaTime: TimeInterval) {
        // Update rendering logic
    }
    
    func setVisibility(_ visible: Bool) {
        isVisible = visible
    }
}

class PhysicsComponent: Component {
    weak var gameObject: GameObject?
    var velocity: Vector3D = .zero
    var acceleration: Vector3D = .zero
    var mass: Double = 1.0
    var isKinematic: Bool = false
    
    func update(_ deltaTime: TimeInterval) {
        guard !isKinematic else { return }
        
        // Update physics
        velocity = velocity + acceleration * deltaTime
        gameObject?.transform.position = (gameObject?.transform.position ?? .zero) + velocity * deltaTime
    }
    
    func applyForce(_ force: Vector3D) {
        acceleration = acceleration + force * (1.0 / mass)
    }
    
    func setVelocity(_ newVelocity: Vector3D) {
        velocity = newVelocity
    }
}

// Game engine manager
class GameEngine {
    private var gameObjects: [UUID: GameObject] = [:]
    private var renderQueue: [RenderComponent] = []
    private var physicsQueue: [PhysicsComponent] = []
    
    func createGameObject(name: String, transform: Transform = .identity) -> GameObject {
        let gameObject = GameObject(id: UUID(), name: name, transform: transform)
        gameObjects[gameObject.id] = gameObject
        return gameObject
    }
    
    func destroyGameObject(_ id: UUID) {
        gameObjects.removeValue(forKey: id)
    }
    
    func update(deltaTime: TimeInterval) {
        // Update all game objects
        for gameObject in gameObjects.values {
            gameObject.update(deltaTime: deltaTime)
        }
        
        // Update physics
        updatePhysics(deltaTime: deltaTime)
        
        // Render
        render()
    }
    
    private func updatePhysics(_ deltaTime: TimeInterval) {
        for gameObject in gameObjects.values {
            if let physics = gameObject.getComponent(ofType: PhysicsComponent.self) {
                physics.update(deltaTime)
            }
        }
    }
    
    private func render() {
        for gameObject in gameObjects.values {
            if let render = gameObject.getComponent(ofType: RenderComponent.self) {
                renderQueue.append(render)
            }
        }
        
        // Process render queue
        for render in renderQueue {
            if render.isVisible {
                print("Rendering \(render.mesh) with \(render.material)")
            }
        }
        
        renderQueue.removeAll()
    }
}

// Usage
let engine = GameEngine()

// Create a player character
let player = engine.createGameObject(name: "Player", transform: Transform(
    position: Vector3D(x: 0, y: 0, z: 0),
    rotation: .zero,
    scale: Vector3D(x: 1, y: 1, z: 1)
))

// Add components
player.addComponent(TransformComponent())
player.addComponent(RenderComponent(mesh: "player_mesh", material: "player_material"))
player.addComponent(PhysicsComponent())

// Get components and use them
if let physics = player.getComponent(ofType: PhysicsComponent.self) {
    physics.setVelocity(Vector3D(x: 5, y: 0, z: 0))
    physics.applyForce(Vector3D(x: 0, y: -9.8, z: 0)) // Gravity
}

if let render = player.getComponent(ofType: RenderComponent.self) {
    render.setVisibility(true)
}

// Game loop simulation
for i in 1...5 {
    print("Frame \(i):")
    engine.update(deltaTime: 1.0/60.0)
    
    if let physics = player.getComponent(ofType: PhysicsComponent.self) {
        print("Player position: \(player.transform.position)")
    }
}
```

### Advanced Features

#### Structures with Methods

Structures can have methods, computed properties, and even mutating methods.

```swift
struct Rectangle {
    var width: Double
    var height: Double
    
    // Computed properties
    var area: Double {
        return width * height
    }
    
    var perimeter: Double {
        return 2 * (width + height)
    }
    
    var isSquare: Bool {
        return width == height
    }
    
    // Methods
    func contains(point: Point) -> Bool {
        return point.x >= 0 && point.x <= width &&
               point.y >= 0 && point.y <= height
    }
    
    func intersects(with other: Rectangle) -> Bool {
        let left = max(0, other.width)
        let right = min(width, other.width)
        let top = max(0, other.height)
        let bottom = min(height, other.height)
        
        return left < right && top < bottom
    }
    
    // Mutating methods
    mutating func scale(by factor: Double) {
        width *= factor
        height *= factor
    }
    
    mutating func rotate() {
        let temp = width
        width = height
        height = temp
    }
}

// Usage
var rect = Rectangle(width: 10, height: 5)
print("Area: \(rect.area)")           // 50
print("Perimeter: \(rect.perimeter)") // 30
print("Is square: \(rect.isSquare)")  // false

rect.scale(by: 2.0)
print("Scaled area: \(rect.area)")    // 200

rect.rotate()
print("Rotated dimensions: \(rect.width) × \(rect.height)") // 10 × 20
```

#### Classes with Inheritance

Classes support inheritance, allowing you to create hierarchies of related types.

```swift
class Vehicle {
    let make: String
    let model: String
    var year: Int
    var mileage: Double
    
    init(make: String, model: String, year: Int) {
        self.make = make
        self.model = model
        self.year = year
        self.mileage = 0
    }
    
    func start() {
        print("\(make) \(model) is starting")
    }
    
    func stop() {
        print("\(make) \(model) is stopping")
    }
    
    func drive(distance: Double) {
        mileage += distance
        print("Drove \(distance) miles. Total mileage: \(mileage)")
    }
    
    var description: String {
        return "\(year) \(make) \(model) with \(mileage) miles"
    }
}

class Car: Vehicle {
    let numberOfDoors: Int
    var fuelLevel: Double
    
    init(make: String, model: String, year: Int, numberOfDoors: Int) {
        self.numberOfDoors = numberOfDoors
        self.fuelLevel = 100.0
        super.init(make: make, model: model, year: year)
    }
    
    override func start() {
        guard fuelLevel > 0 else {
            print("Cannot start: no fuel")
            return
        }
        super.start()
    }
    
    override func drive(distance: Double) {
        let fuelConsumption = distance * 0.05 // 0.05 gallons per mile
        fuelLevel -= fuelConsumption
        
        if fuelLevel < 0 {
            fuelLevel = 0
            print("Out of fuel!")
        }
        
        super.drive(distance: distance)
        print("Fuel level: \(fuelLevel)%")
    }
    
    func refuel(amount: Double) {
        fuelLevel = min(100.0, fuelLevel + amount)
        print("Refueled. Fuel level: \(fuelLevel)%")
    }
}

class Motorcycle: Vehicle {
    let engineSize: Double
    var hasSidecar: Bool
    
    init(make: String, model: String, year: Int, engineSize: Double) {
        self.engineSize = engineSize
        self.hasSidecar = false
        super.init(make: make, model: model, year: year)
    }
    
    override func start() {
        print("Starting \(engineSize)L engine")
        super.start()
    }
    
    func attachSidecar() {
        hasSidecar = true
        print("Sidecar attached")
    }
    
    func detachSidecar() {
        hasSidecar = false
        print("Sidecar detached")
    }
}

// Usage
let car = Car(make: "Toyota", model: "Camry", year: 2020, numberOfDoors: 4)
let motorcycle = Motorcycle(make: "Harley-Davidson", model: "Sportster", year: 2019, engineSize: 1.2)

car.start()
car.drive(distance: 50)
car.refuel(amount: 20)

motorcycle.start()
motorcycle.attachSidecar()
motorcycle.drive(distance: 25)
```

### Best Practices for Structures and Classes

1. **Choose structures by default** - Use structures unless you need reference semantics
2. **Use classes for identity** - When you need to check if two variables reference the same object
3. **Use classes for inheritance** - When you need to create subclasses
4. **Use structures for data** - When modeling simple data types
5. **Consider performance** - Structures can be more efficient for small data
6. **Use mutating methods** - For structures that need to modify their properties
7. **Implement proper initialization** - Provide clear, safe ways to create instances
8. **Use access control** - Control what can access your properties and methods

**Design principles:**

- **Value semantics** - Prefer structures for predictable, independent behavior
- **Reference semantics** - Use classes when you need shared state or identity
- **Composition over inheritance** - Favor composition when possible
- **Single responsibility** - Each type should have one clear purpose
- **Encapsulation** - Hide implementation details behind a clean interface

Structures and classes are the foundation of Swift programming. Understanding when and how to use each will help you write more efficient, maintainable, and correct code. The choice between them affects not just performance, but also the behavior and safety of your programs.

---

## Properties

Properties are values associated with a class, structure, or enumeration. They provide a way to store and retrieve data, and Swift offers several types of properties with different behaviors and use cases. Understanding properties is essential for creating well-designed, efficient Swift code.

### Understanding Properties

Properties in Swift can be stored values, computed values, or type-level values. Each type serves different purposes and has different characteristics.

#### Stored Properties

Stored properties are the most basic type - they store constant or variable values as part of an instance.

```swift
struct Person {
    // Stored properties
    let id: UUID
    var name: String
    var age: Int
    var email: String
    var isActive: Bool
    
    // Default values
    var lastLoginDate: Date? = nil
    var loginCount: Int = 0
    
    // Lazy stored properties
    lazy var fullProfile: String = {
        // Expensive computation that's only done when first accessed
        return "ID: \(id)\nName: \(name)\nAge: \(age)\nEmail: \(email)\nActive: \(isActive)"
    }()
}

class BankAccount {
    // Stored properties with access control
    private let accountNumber: String
    private var balance: Decimal
    var accountHolder: String
    var isActive: Bool
    
    // Computed property that depends on stored properties
    var formattedBalance: String {
        return "$\(balance)"
    }
    
    init(accountNumber: String, initialBalance: Decimal, accountHolder: String) {
        self.accountNumber = accountNumber
        self.balance = initialBalance
        self.accountHolder = accountHolder
        self.isActive = true
    }
    
    func deposit(amount: Decimal) {
        guard amount > 0 else { return }
        balance += amount
    }
    
    func withdraw(amount: Decimal) -> Bool {
        guard amount > 0 && amount <= balance else { return false }
        balance -= amount
        return true
    }
}

// Usage
let person = Person(
    id: UUID(),
    name: "John Doe",
    age: 30,
    email: "john@example.com",
    isActive: true
)

print(person.fullProfile)  // Lazy property computed on first access

let account = BankAccount(
    accountNumber: "1234567890",
    initialBalance: 1000.00,
    accountHolder: "John Doe"
)

account.deposit(amount: 500.00)
print("Balance: \(account.formattedBalance)")  // $1500.00
```

**Stored property characteristics:**
- **Memory allocation** - Space is allocated for each instance
- **Direct access** - Values are stored directly in memory
- **Default values** - Can provide initial values
- **Lazy evaluation** - Can defer computation until first access
- **Access control** - Can control who can read/write properties

#### Computed Properties

Computed properties don't store values - they provide a getter and optionally a setter to retrieve and set other properties and values.

```swift
struct Rectangle {
    var width: Double
    var height: Double
    
    // Computed property - getter only
    var area: Double {
        return width * height
    }
    
    // Computed property with custom getter
    var perimeter: Double {
        get {
            return 2 * (width + height)
        }
    }
    
    // Computed property with getter and setter
    var diagonal: Double {
        get {
            return sqrt(width * width + height * height)
        }
        set {
            // Calculate new dimensions maintaining aspect ratio
            let ratio = width / height
            let newDiagonal = newValue
            width = newDiagonal / sqrt(1 + ratio * ratio)
            height = width / ratio
        }
    }
    
    // Computed property with custom setter
    var center: (x: Double, y: Double) {
        get {
            return (width / 2, height / 2)
        }
        set {
            let deltaX = newValue.x - width / 2
            let deltaY = newValue.y - height / 2
            width += deltaX * 2
            height += deltaY * 2
        }
    }
}

struct Circle {
    var radius: Double
    
    // Computed properties
    var diameter: Double {
        get { return radius * 2 }
        set { radius = newValue / 2 }
    }
    
    var area: Double {
        get { return Double.pi * radius * radius }
        set { radius = sqrt(newValue / Double.pi) }
    }
    
    var circumference: Double {
        get { return Double.pi * diameter }
        set { radius = newValue / (2 * Double.pi) }
    }
}

// Usage
var rect = Rectangle(width: 10, height: 5)
print("Area: \(rect.area)")           // 50.0
print("Perimeter: \(rect.perimeter)") // 30.0
print("Diagonal: \(rect.diagonal)")   // 11.18...

// Using setter
rect.diagonal = 15.0
print("New dimensions: \(rect.width) × \(rect.height)")

var circle = Circle(radius: 5.0)
print("Area: \(circle.area)")         // 78.54...
print("Diameter: \(circle.diameter)") // 10.0

// Using setter
circle.area = 100.0
print("New radius: \(circle.radius)") // 5.64...
```

**Computed property benefits:**
- **Derived values** - Calculate values from other properties
- **Validation** - Ensure values are within valid ranges
- **Encapsulation** - Hide internal implementation details
- **Consistency** - Ensure related properties stay in sync
- **Performance** - Can cache expensive calculations

#### Property Observers

Property observers monitor and respond to changes in a property's value. They're called every time a property's value is set.

```swift
class TemperatureMonitor {
    var temperature: Double = 20.0 {
        willSet {
            print("Temperature will change from \(temperature)°C to \(newValue)°C")
        }
        didSet {
            print("Temperature changed from \(oldValue)°C to \(temperature)°C")
            
            // Respond to temperature changes
            if temperature > 30.0 {
                print("⚠️ Warning: High temperature detected!")
            } else if temperature < 0.0 {
                print("❄️ Warning: Low temperature detected!")
            }
        }
    }
    
    var humidity: Double = 50.0 {
        willSet {
            print("Humidity will change from \(humidity)% to \(newValue)%")
        }
        didSet {
            print("Humidity changed from \(oldValue)% to \(humidity)%")
            
            // Respond to humidity changes
            if humidity > 80.0 {
                print("💧 Warning: High humidity detected!")
            } else if humidity < 20.0 {
                print("🏜️ Warning: Low humidity detected!")
            }
        }
    }
}

class UserProfile {
    var username: String = "" {
        willSet {
            print("Username will change from '\(username)' to '\(newValue)'")
        }
        didSet {
            print("Username changed from '\(oldValue)' to '\(username)'")
            
            // Validate new username
            if username.count < 3 {
                print("⚠️ Username is too short")
            }
            
            if username.contains(" ") {
                print("⚠️ Username contains spaces")
            }
        }
    }
    
    var age: Int = 0 {
        willSet {
            print("Age will change from \(age) to \(newValue)")
        }
        didSet {
            print("Age changed from \(oldValue) to \(age)")
            
            // Validate age
            if age < 13 {
                print("⚠️ User is too young")
            } else if age > 120 {
                print("⚠️ Invalid age value")
            }
        }
    }
}

// Usage
let monitor = TemperatureMonitor()
monitor.temperature = 25.0
monitor.temperature = 35.0
monitor.humidity = 85.0

let profile = UserProfile()
profile.username = "john_doe"
profile.username = "jo"  // Too short
profile.age = 25
profile.age = 150  // Invalid
```

**Property observer use cases:**
- **Validation** - Check values before and after changes
- **Logging** - Track property changes for debugging
- **Side effects** - Perform actions when properties change
- **Synchronization** - Keep related properties in sync
- **Notifications** - Alert other parts of the system

#### Type Properties

Type properties belong to the type itself, not to instances of the type. They're useful for storing values that are shared across all instances.

```swift
struct MathConstants {
    // Type properties (static)
    static let pi = Double.pi
    static let e = 2.718281828459045
    static let goldenRatio = 1.618033988749895
    
    // Computed type property
    static var randomAngle: Double {
        return Double.random(in: 0...(2 * pi))
    }
    
    // Type property with custom getter
    static var maxInt: Int {
        return Int.max
    }
}

class NetworkManager {
    // Singleton pattern with type property
    static let shared = NetworkManager()
    
    // Configuration type properties
    static let defaultTimeout: TimeInterval = 30.0
    static let maxRetries = 3
    static let baseURL = "https://api.example.com"
    
    // Computed type property
    static var isNetworkAvailable: Bool {
        // Check network status
        return true // Simplified
    }
    
    // Private initializer for singleton
    private init() {}
    
    func makeRequest() {
        print("Making request to \(NetworkManager.baseURL)")
        print("Timeout: \(NetworkManager.defaultTimeout)s")
    }
}

class UserDefaults {
    // Type properties for common keys
    static let userIDKey = "user_id"
    static let usernameKey = "username"
    static let emailKey = "email"
    static let lastLoginKey = "last_login"
    
    // Computed type property
    static var defaultSettings: [String: Any] {
        return [
            userIDKey: "",
            usernameKey: "",
            emailKey: "",
            lastLoginKey: Date()
        ]
    }
}

// Usage
print("Pi: \(MathConstants.pi)")
print("Random angle: \(MathConstants.randomAngle)")
print("Max int: \(MathConstants.maxInt)")

let networkManager = NetworkManager.shared
networkManager.makeRequest()

print("Default settings: \(UserDefaults.defaultSettings)")
```

**Type property benefits:**
- **Shared state** - Values shared across all instances
- **Configuration** - Centralized configuration values
- **Constants** - Immutable values associated with types
- **Singleton pattern** - Single instance shared across app
- **Performance** - Computed once, shared everywhere

### Real-World Property Examples

Let's see how properties are used in practical applications.

**Example 1: E-commerce Product System**

```swift
struct Product {
    let id: UUID
    let name: String
    let description: String
    let basePrice: Decimal
    let category: ProductCategory
    let tags: Set<String>
    
    // Stored properties
    var stockQuantity: Int
    var isActive: Bool
    var discountPercentage: Decimal
    
    // Computed properties
    var finalPrice: Decimal {
        let discount = basePrice * (discountPercentage / 100)
        return basePrice - discount
    }
    
    var isOnSale: Bool {
        return discountPercentage > 0
    }
    
    var isInStock: Bool {
        return stockQuantity > 0
    }
    
    var stockStatus: StockStatus {
        if stockQuantity == 0 {
            return .outOfStock
        } else if stockQuantity <= 5 {
            return .lowStock
        } else {
            return .inStock
        }
    }
    
    var formattedPrice: String {
        if isOnSale {
            return "~~$\(basePrice)~~ $\(finalPrice) (\(discountPercentage)% off)"
        } else {
            return "$\(finalPrice)"
        }
    }
    
    // Property observers
    var lastModified: Date = Date() {
        didSet {
            print("Product '\(name)' was modified at \(lastModified)")
        }
    }
    
    var viewCount: Int = 0 {
        didSet {
            if viewCount % 100 == 0 {
                print("Product '\(name)' reached \(viewCount) views!")
            }
        }
    }
}

enum ProductCategory: String, CaseIterable {
    case electronics = "Electronics"
    case clothing = "Clothing"
    case books = "Books"
    case home = "Home & Garden"
    case sports = "Sports & Outdoors"
    
    // Type properties
    static let featuredCategories: [ProductCategory] = [.electronics, .clothing]
    
    static var totalCategories: Int {
        return allCases.count
    }
}

enum StockStatus: String {
    case inStock = "In Stock"
    case lowStock = "Low Stock"
    case outOfStock = "Out of Stock"
    
    var color: String {
        switch self {
        case .inStock: return "green"
        case .lowStock: return "orange"
        case .outOfStock: return "red"
        }
    }
}

class ProductManager {
    // Type properties
    static let maxProductsPerPage = 20
    static let defaultSortOrder = SortOrder.nameAscending
    
    // Singleton instance
    static let shared = ProductManager()
    
    private var products: [UUID: Product] = [:]
    
    private init() {}
    
    func addProduct(_ product: Product) {
        products[product.id] = product
        print("Added product: \(product.name)")
    }
    
    func getProduct(by id: UUID) -> Product? {
        return products[id]
    }
    
    func updateStock(for productId: UUID, newQuantity: Int) {
        guard var product = products[productId] else { return }
        product.stockQuantity = newQuantity
        product.lastModified = Date()
        products[productId] = product
        
        print("Updated stock for \(product.name) to \(newQuantity)")
    }
    
    func applyDiscount(to productId: UUID, percentage: Decimal) {
        guard var product = products[productId] else { return }
        product.discountPercentage = percentage
        product.lastModified = Date()
        products[productId] = product
        
        print("Applied \(percentage)% discount to \(product.name)")
    }
    
    func getProductsByCategory(_ category: ProductCategory) -> [Product] {
        return products.values.filter { $0.category == category }
    }
    
    func getOnSaleProducts() -> [Product] {
        return products.values.filter { $0.isOnSale }
    }
}

enum SortOrder {
    case nameAscending, nameDescending
    case priceAscending, priceDescending
    case dateAdded, popularity
}

// Usage
let productManager = ProductManager.shared

let product1 = Product(
    id: UUID(),
    name: "iPhone 15 Pro",
    description: "Latest iPhone with advanced features",
    basePrice: 999.99,
    category: .electronics,
    tags: ["smartphone", "apple", "5g"],
    stockQuantity: 50,
    isActive: true,
    discountPercentage: 0
)

let product2 = Product(
    id: UUID(),
    name: "Wireless Headphones",
    description: "Noise-cancelling wireless headphones",
    basePrice: 299.99,
    category: .electronics,
    tags: ["audio", "wireless", "noise-cancelling"],
    stockQuantity: 3,
    isActive: true,
    discountPercentage: 15
)

// Add products
productManager.addProduct(product1)
productManager.addProduct(product2)

// Display product information
print("Product 1: \(product1.formattedPrice)")
print("Stock status: \(product1.stockStatus.rawValue)")

print("Product 2: \(product2.formattedPrice)")
print("Stock status: \(product2.stockStatus.rawValue)")

// Update properties
productManager.updateStock(for: product1.id, newQuantity: 45)
productManager.applyDiscount(to: product1.id, percentage: 10)

// Get updated product
if let updatedProduct = productManager.getProduct(by: product1.id) {
    print("Updated product: \(updatedProduct.formattedPrice)")
    print("Stock status: \(updatedProduct.stockStatus.rawValue)")
}

// Category information
print("Total categories: \(ProductCategory.totalCategories)")
print("Featured categories: \(ProductCategory.featuredCategories.map { $0.rawValue })")
```

**Example 2: Game Character System**

```swift
struct Stats {
    var health: Int
    var mana: Int
    var strength: Int
    var agility: Int
    var intelligence: Int
    
    // Computed properties
    var maxHealth: Int {
        return 100 + (strength * 10)
    }
    
    var maxMana: Int {
        return 50 + (intelligence * 15)
    }
    
    var attackPower: Int {
        return strength + (agility / 2)
    }
    
    var defense: Int {
        return agility + (strength / 3)
    }
    
    var criticalChance: Double {
        return Double(agility) / 100.0
    }
    
    // Property observers
    var health: Int {
        didSet {
            if health <= 0 {
                print("⚠️ Character is defeated!")
            } else if health <= maxHealth * 0.2 {
                print("🚨 Critical health warning!")
            }
        }
    }
    
    var mana: Int {
        didSet {
            if mana <= 0 {
                print("⚠️ Out of mana!")
            }
        }
    }
}

class Character {
    let id: UUID
    let name: String
    let characterClass: CharacterClass
    
    // Stored properties
    var level: Int = 1
    var experience: Int = 0
    var stats: Stats
    var inventory: [Item] = []
    var isAlive: Bool = true
    
    // Computed properties
    var experienceToNextLevel: Int {
        return level * 100
    }
    
    var isMaxLevel: Bool {
        return level >= 100
    }
    
    var healthPercentage: Double {
        return Double(stats.health) / Double(stats.maxHealth)
    }
    
    var manaPercentage: Double {
        return Double(stats.mana) / Double(stats.maxMana)
    }
    
    var canUseAbility: Bool {
        return stats.mana >= 10 && isAlive
    }
    
    // Property observers
    var level: Int {
        didSet {
            if level > oldValue {
                print("🎉 \(name) reached level \(level)!")
                levelUp()
            }
        }
    }
    
    var experience: Int {
        didSet {
            if experience >= experienceToNextLevel && !isMaxLevel {
                level += 1
            }
        }
    }
    
    // Initializer
    init(id: UUID, name: String, characterClass: CharacterClass) {
        self.id = id
        self.name = name
        self.characterClass = characterClass
        
        // Initialize stats based on character class
        switch characterClass {
        case .warrior:
            self.stats = Stats(health: 120, mana: 30, strength: 15, agility: 8, intelligence: 5)
        case .mage:
            self.stats = Stats(health: 80, mana: 100, strength: 5, agility: 6, intelligence: 18)
        case .archer:
            self.stats = Stats(health: 90, mana: 50, strength: 8, agility: 15, intelligence: 7)
        }
    }
    
    // Methods
    func takeDamage(_ amount: Int) {
        guard isAlive else { return }
        
        let actualDamage = max(1, amount - stats.defense)
        stats.health = max(0, stats.health - actualDamage)
        
        if stats.health == 0 {
            isAlive = false
        }
        
        print("\(name) took \(actualDamage) damage. Health: \(stats.health)/\(stats.maxHealth)")
    }
    
    func heal(_ amount: Int) {
        guard isAlive else { return }
        
        let oldHealth = stats.health
        stats.health = min(stats.maxHealth, stats.health + amount)
        let healed = stats.health - oldHealth
        
        print("\(name) healed \(healed) health. Health: \(stats.health)/\(stats.maxHealth)")
    }
    
    func useAbility() {
        guard canUseAbility else {
            print("\(name) cannot use ability")
            return
        }
        
        stats.mana -= 10
        print("\(name) used ability. Mana: \(stats.mana)/\(stats.maxMana)")
    }
    
    private func levelUp() {
        // Increase stats
        stats.strength += 2
        stats.agility += 2
        stats.intelligence += 2
        
        // Restore health and mana
        stats.health = stats.maxHealth
        stats.mana = stats.maxMana
        
        print("\(name) stats increased!")
    }
}

enum CharacterClass: String, CaseIterable {
    case warrior = "Warrior"
    case mage = "Mage"
    case archer = "Archer"
    
    // Type properties
    static let startingClasses: [CharacterClass] = [.warrior, .mage, .archer]
    
    static var totalClasses: Int {
        return allCases.count
    }
    
    var description: String {
        switch self {
        case .warrior: return "Strong melee fighter with high health and defense"
        case .mage: return "Powerful spellcaster with high mana and intelligence"
        case .archer: return "Agile ranged fighter with high agility and critical chance"
        }
    }
}

struct Item {
    let id: UUID
    let name: String
    let description: String
    let itemType: ItemType
    let stats: Stats?
    let value: Int
    
    enum ItemType: String {
        case weapon = "Weapon"
        case armor = "Armor"
        case consumable = "Consumable"
        case accessory = "Accessory"
    }
}

// Usage
let characterManager = CharacterManager()

let warrior = Character(
    id: UUID(),
    name: "Aragorn",
    characterClass: .warrior
)

let mage = Character(
    id: UUID(),
    name: "Gandalf",
    characterClass: .mage
)

// Display character information
print("\(warrior.name) - Level \(warrior.level)")
print("Health: \(warrior.healthPercentage * 100)%")
print("Mana: \(warrior.manaPercentage * 100)%")
print("Attack Power: \(warrior.stats.attackPower)")
print("Defense: \(warrior.stats.defense)")

// Combat simulation
warrior.takeDamage(50)
warrior.heal(20)
warrior.useAbility()

// Level up
warrior.experience = 150

// Character class information
print("Available classes: \(CharacterClass.allCases.map { $0.rawValue })")
print("Total classes: \(CharacterClass.totalClasses)")

for characterClass in CharacterClass.allCases {
    print("\(characterClass.rawValue): \(characterClass.description)")
}
```

### Advanced Property Features

#### Lazy Properties

Lazy properties defer their initialization until they're first accessed, which can improve performance and avoid unnecessary work.

```swift
class DataProcessor {
    private var data: [Int] = []
    
    // Lazy property - computed only when first accessed
    lazy var expensiveCalculation: [Int] = {
        print("Performing expensive calculation...")
        return data.map { $0 * $0 }.filter { $0 % 2 == 0 }
    }()
    
    // Lazy property with complex initialization
    lazy var statistics: Statistics = {
        print("Calculating statistics...")
        let sum = data.reduce(0, +)
        let average = data.isEmpty ? 0 : Double(sum) / Double(data.count)
        let sorted = data.sorted()
        let median = sorted.isEmpty ? 0 : sorted[sorted.count / 2]
        
        return Statistics(sum: sum, average: average, median: median)
    }()
    
    func addData(_ value: Int) {
        data.append(value)
    }
    
    func getProcessedData() -> [Int] {
        return expensiveCalculation  // Calculated on first access
    }
    
    func getStats() -> Statistics {
        return statistics  // Calculated on first access
    }
}

struct Statistics {
    let sum: Int
    let average: Double
    let median: Int
}

// Usage
let processor = DataProcessor()

// Add data
processor.addData(1)
processor.addData(2)
processor.addData(3)
processor.addData(4)
processor.addData(5)

// Lazy properties are computed only when accessed
print("Getting processed data...")
let processed = processor.getProcessedData()  // First access - calculation happens

print("Getting statistics...")
let stats = processor.getStats()  // First access - calculation happens

// Subsequent accesses use cached values
let processed2 = processor.getProcessedData()  // No calculation
let stats2 = processor.getStats()  // No calculation
```

#### Property Wrappers

Property wrappers are a powerful feature that allows you to define custom behavior for properties.

```swift
// Custom property wrapper for validation
@propertyWrapper
struct Validated<T> {
    private var value: T
    private let validator: (T) -> Bool
    private let errorMessage: String
    
    init(wrappedValue: T, validator: @escaping (T) -> Bool, errorMessage: String) {
        self.value = wrappedValue
        self.validator = validator
        self.errorMessage = errorMessage
        
        // Validate initial value
        if !validator(wrappedValue) {
            print("Warning: \(errorMessage)")
        }
    }
    
    var wrappedValue: T {
        get { return value }
        set {
            if validator(newValue) {
                value = newValue
            } else {
                print("Error: \(errorMessage)")
            }
        }
    }
}

// Property wrapper for logging
@propertyWrapper
struct Logged<T> {
    private var value: T
    private let name: String
    
    init(wrappedValue: T, name: String) {
        self.value = wrappedValue
        self.name = name
        print("\(name) initialized with: \(wrappedValue)")
    }
    
    var wrappedValue: T {
        get {
            print("\(name) accessed: \(value)")
            return value
        }
        set {
            print("\(name) changed from \(value) to \(newValue)")
            value = newValue
        }
    }
}

// Property wrapper for caching
@propertyWrapper
struct Cached<T> {
    private var value: T?
    private let compute: () -> T
    
    init(compute: @escaping () -> T) {
        self.compute = compute
    }
    
    var wrappedValue: T {
        if let value = value {
            return value
        } else {
            let computed = compute()
            value = computed
            return computed
        }
    }
    
    mutating func clearCache() {
        value = nil
    }
}

// Usage
struct User {
    @Validated(wrappedValue: "", validator: { !$0.isEmpty }, errorMessage: "Username cannot be empty")
    var username: String
    
    @Validated(wrappedValue: 0, validator: { $0 >= 0 && $0 <= 120 }, errorMessage: "Age must be between 0 and 120")
    var age: Int
    
    @Logged(wrappedValue: "user@example.com", name: "Email")
    var email: String
    
    @Cached(compute: { 
        print("Computing expensive value...")
        return "Expensive computed value"
    })
    var expensiveValue: String
}

var user = User()
user.username = "john_doe"  // Valid
user.username = ""           // Error: Username cannot be empty
user.age = 25               // Valid
user.age = 150              // Error: Age must be between 0 and 120

// Logged property shows access
let email = user.email      // Shows access log
user.email = "new@email.com" // Shows change log

// Cached property
let value1 = user.expensiveValue  // Computes and caches
let value2 = user.expensiveValue  // Uses cached value
```

### Best Practices for Properties

1. **Choose the right property type** - Stored vs computed vs lazy
2. **Use property observers wisely** - Don't create infinite loops
3. **Consider performance** - Lazy properties for expensive operations
4. **Validate data** - Use property observers for validation
5. **Control access** - Use appropriate access control levels
6. **Document computed properties** - Explain complex calculations
7. **Use property wrappers** - For reusable property behavior
8. **Keep properties focused** - Each property should have one purpose

**Property design principles:**

- **Encapsulation** - Hide implementation details behind properties
- **Validation** - Ensure data integrity through property observers
- **Performance** - Use lazy properties for expensive operations
- **Consistency** - Keep related properties in sync
- **Clarity** - Make property names and behavior clear

Properties are fundamental to Swift programming, providing a clean way to store and access data. Understanding the different types of properties and when to use each will help you write more efficient, maintainable, and correct code.

---

## Methods

Methods are functions that are associated with a particular type. They provide behavior and functionality to your classes, structures, and enumerations. Swift offers several types of methods, each with different characteristics and use cases.

### Understanding Methods

Methods in Swift can be instance methods, type methods, or mutating methods. Each type serves different purposes and has different behaviors.

#### Instance Methods

Instance methods are functions that belong to instances of a class, structure, or enumeration.

```swift
class Calculator {
    private var history: [String] = []
    
    // Instance method
    func add(_ a: Double, _ b: Double) -> Double {
        let result = a + b
        let operation = "\(a) + \(b) = \(result)"
        history.append(operation)
        return result
    }
    
    func subtract(_ a: Double, _ b: Double) -> Double {
        let result = a - b
        let operation = "\(a) - \(b) = \(result)"
        history.append(operation)
        return result
    }
    
    func multiply(_ a: Double, _ b: Double) -> Double {
        let result = a * b
        let operation = "\(a) × \(b) = \(result)"
        history.append(operation)
        return result
    }
    
    func divide(_ a: Double, _ b: Double) -> Double? {
        guard b != 0 else {
            print("Error: Division by zero")
            return nil
        }
        
        let result = a / b
        let operation = "\(a) ÷ \(b) = \(result)"
        history.append(operation)
        return result
    }
    
    func clearHistory() {
        history.removeAll()
        print("History cleared")
    }
    
    func getHistory() -> [String] {
        return history
    }
    
    func getLastOperation() -> String? {
        return history.last
    }
}

// Usage
let calculator = Calculator()
let sum = calculator.add(10, 5)        // 15.0
let difference = calculator.subtract(10, 3)  // 7.0
let product = calculator.multiply(4, 6)      // 24.0

if let quotient = calculator.divide(15, 3) {
    print("Quotient: \(quotient)")     // 5.0
}

print("History: \(calculator.getHistory())")
print("Last operation: \(calculator.getLastOperation() ?? "None")")
```

**Instance method characteristics:**
- **Belong to instances** - Called on specific instances of a type
- **Access instance properties** - Can read and modify instance state
- **Instance-specific behavior** - Each instance can have different behavior
- **Memory allocation** - Each instance has its own copy of methods

#### Type Methods

Type methods (also called class methods) belong to the type itself, not to instances of the type.

```swift
struct MathUtils {
    // Type method (static)
    static func factorial(_ n: Int) -> Int {
        guard n >= 0 else { return 0 }
        if n == 0 || n == 1 { return 1 }
        
        var result = 1
        for i in 2...n {
            result *= i
        }
        return result
    }
    
    static func isPrime(_ n: Int) -> Bool {
        guard n > 1 else { return false }
        guard n != 2 else { return true }
        guard n % 2 != 0 else { return false }
        
        let sqrtN = Int(sqrt(Double(n)))
        for i in stride(from: 3, through: sqrtN, by: 2) {
            if n % i == 0 {
                return false
            }
        }
        return true
    }
    
    static func greatestCommonDivisor(_ a: Int, _ b: Int) -> Int {
        var x = abs(a)
        var y = abs(b)
        
        while y != 0 {
            let temp = y
            y = x % y
            x = temp
        }
        
        return x
    }
    
    static func leastCommonMultiple(_ a: Int, _ b: Int) -> Int {
        return abs(a * b) / greatestCommonDivisor(a, b)
    }
}

class NetworkManager {
    // Type properties
    static let shared = NetworkManager()
    static let baseURL = "https://api.example.com"
    static let timeout: TimeInterval = 30.0
    
    // Type method
    static func validateURL(_ urlString: String) -> Bool {
        guard let url = URL(string: urlString) else { return false }
        return url.scheme == "http" || url.scheme == "https"
    }
    
    static func createRequest(for endpoint: String) -> URLRequest? {
        let fullURL = baseURL + endpoint
        guard let url = URL(string: fullURL) else { return nil }
        
        var request = URLRequest(url: url)
        request.timeoutInterval = timeout
        request.setValue("application/json", forHTTPHeaderField: "Content-Type")
        
        return request
    }
    
    static func logRequest(_ request: URLRequest) {
        print("Request: \(request.httpMethod ?? "GET") \(request.url?.absoluteString ?? "")")
        print("Headers: \(request.allHTTPHeaderFields ?? [:])")
    }
    
    private init() {}
}

// Usage
let factorial = MathUtils.factorial(5)  // 120
let isPrime = MathUtils.isPrime(17)     // true
let gcd = MathUtils.greatestCommonDivisor(48, 18)  // 6
let lcm = MathUtils.leastCommonMultiple(12, 18)    // 36

let isValidURL = NetworkManager.validateURL("https://example.com")  // true
if let request = NetworkManager.createRequest(for: "/users") {
    NetworkManager.logRequest(request)
}
```

**Type method benefits:**
- **Shared functionality** - Methods available to all instances
- **No instance required** - Can be called without creating instances
- **Configuration** - Centralized configuration and utility methods
- **Memory efficiency** - Single copy shared across all instances

#### Mutating Methods

Mutating methods are used in structures and enumerations to modify their properties.

```swift
struct Point {
    var x: Double
    var y: Double
    
    // Mutating method
    mutating func move(by deltaX: Double, deltaY: Double) {
        x += deltaX
        y += deltaY
    }
    
    mutating func move(to newX: Double, newY: Double) {
        x = newX
        y = newY
    }
    
    mutating func scale(by factor: Double) {
        x *= factor
        y *= factor
    }
    
    mutating func rotate(by angle: Double) {
        let cosAngle = cos(angle)
        let sinAngle = sin(angle)
        
        let newX = x * cosAngle - y * sinAngle
        let newY = x * sinAngle + y * cosAngle
        
        x = newX
        y = newY
    }
    
    // Non-mutating method
    func distance(to other: Point) -> Double {
        let deltaX = x - other.x
        let deltaY = y - other.y
        return sqrt(deltaX * deltaX + deltaY * deltaY)
    }
}

struct Rectangle {
    var origin: Point
    var size: (width: Double, height: Double)
    
    // Computed properties
    var center: Point {
        get {
            return Point(
                x: origin.x + size.width / 2,
                y: origin.y + size.height / 2
            )
        }
        set {
            origin.x = newValue.x - size.width / 2
            origin.y = newValue.y - size.height / 2
        }
    }
    
    var area: Double {
        return size.width * size.height
    }
    
    // Mutating methods
    mutating func move(by deltaX: Double, deltaY: Double) {
        origin.move(by: deltaX, deltaY: deltaY)
    }
    
    mutating func resize(width: Double, height: Double) {
        size.width = width
        size.height = height
    }
    
    mutating func scale(by factor: Double) {
        size.width *= factor
        size.height *= factor
    }
    
    // Non-mutating methods
    func contains(_ point: Point) -> Bool {
        return point.x >= origin.x &&
               point.x <= origin.x + size.width &&
               point.y >= origin.y &&
               point.y <= origin.y + size.height
    }
    
    func intersects(with other: Rectangle) -> Bool {
        let left = max(origin.x, other.origin.x)
        let right = min(origin.x + size.width, other.origin.x + other.size.width)
        let top = max(origin.y, other.origin.y)
        let bottom = min(origin.y + size.height, other.origin.y + other.size.height)
        
        return left < right && top < bottom
    }
}

// Usage
var point = Point(x: 0, y: 0)
point.move(by: 5, deltaY: 3)
print("Point: (\(point.x), \(point.y))")  // (5.0, 3.0)

point.scale(by: 2.0)
print("Scaled point: (\(point.x), \(point.y))")  // (10.0, 6.0)

point.rotate(by: Double.pi / 2)  // 90 degrees
print("Rotated point: (\(point.x), \(point.y))")

var rect = Rectangle(origin: Point(x: 0, y: 0), size: (width: 10, height: 5))
print("Rectangle area: \(rect.area)")  // 50.0

rect.move(by: 5, deltaY: 5)
print("Moved rectangle center: (\(rect.center.x), \(rect.center.y))")

rect.scale(by: 2.0)
print("Scaled rectangle area: \(rect.area)")  // 200.0
```

**Mutating method characteristics:**
- **Modify properties** - Can change instance state
- **Required for structures** - Must be marked as `mutating`
- **Value type behavior** - Work with Swift's value semantics
- **Instance modification** - Change the instance they're called on

### Real-World Method Examples

Let's see how methods are used in practical applications.

**Example 1: Bank Account Management System**

```swift
class BankAccount {
    let accountNumber: String
    let accountHolder: String
    private var balance: Decimal
    private var transactions: [Transaction] = []
    private var isActive: Bool
    
    // Computed properties
    var currentBalance: Decimal {
        return balance
    }
    
    var formattedBalance: String {
        return "$\(balance)"
    }
    
    var transactionHistory: [Transaction] {
        return transactions
    }
    
    var lastTransaction: Transaction? {
        return transactions.last
    }
    
    // Initializer
    init(accountNumber: String, accountHolder: String, initialBalance: Decimal = 0) {
        self.accountNumber = accountNumber
        self.accountHolder = accountHolder
        self.balance = initialBalance
        self.isActive = true
        
        if initialBalance > 0 {
            let initialDeposit = Transaction(
                type: .deposit,
                amount: initialBalance,
                description: "Initial deposit"
            )
            transactions.append(initialDeposit)
        }
    }
    
    // Instance methods
    func deposit(amount: Decimal, description: String = "Deposit") -> Bool {
        guard isActive else {
            print("Account is inactive")
            return false
        }
        
        guard amount > 0 else {
            print("Deposit amount must be positive")
            return false
        }
        
        balance += amount
        
        let transaction = Transaction(
            type: .deposit,
            amount: amount,
            description: description
        )
        transactions.append(transaction)
        
        print("Deposited \(amount) to account \(accountNumber)")
        return true
    }
    
    func withdraw(amount: Decimal, description: String = "Withdrawal") -> Bool {
        guard isActive else {
            print("Account is inactive")
            return false
        }
        
        guard amount > 0 else {
            print("Withdrawal amount must be positive")
            return false
        }
        
        guard balance >= amount else {
            print("Insufficient funds")
            return false
        }
        
        balance -= amount
        
        let transaction = Transaction(
            type: .withdrawal,
            amount: amount,
            description: description
        )
        transactions.append(transaction)
        
        print("Withdrew \(amount) from account \(accountNumber)")
        return true
    }
    
    func transfer(amount: Decimal, to recipient: BankAccount, description: String = "Transfer") -> Bool {
        guard isActive && recipient.isActive else {
            print("One or both accounts are inactive")
            return false
        }
        
        guard withdraw(amount: amount, description: "Transfer to \(recipient.accountHolder)") else {
            return false
        }
        
        guard recipient.deposit(amount: amount, description: "Transfer from \(accountHolder)") else {
            // Rollback if recipient deposit fails
            deposit(amount: amount, description: "Transfer rollback")
            return false
        }
        
        print("Successfully transferred \(amount) to \(recipient.accountHolder)")
        return true
    }
    
    func getTransactionSummary() -> TransactionSummary {
        let deposits = transactions.filter { $0.type == .deposit }
        let withdrawals = transactions.filter { $0.type == .withdrawal }
        
        let totalDeposits = deposits.reduce(0) { $0 + $1.amount }
        let totalWithdrawals = withdrawals.reduce(0) { $0 + $1.amount }
        
        return TransactionSummary(
            totalDeposits: totalDeposits,
            totalWithdrawals: totalWithdrawals,
            netChange: totalDeposits - totalWithdrawals,
            transactionCount: transactions.count
        )
    }
    
    func deactivate() {
        isActive = false
        print("Account \(accountNumber) deactivated")
    }
    
    func reactivate() {
        isActive = true
        print("Account \(accountNumber) reactivated")
    }
    
    // Type methods
    static func validateAccountNumber(_ accountNumber: String) -> Bool {
        // Simple validation: must be 10 digits
        return accountNumber.count == 10 && accountNumber.allSatisfy { $0.isNumber }
    }
    
    static func generateAccountNumber() -> String {
        // Generate a random 10-digit account number
        var number = ""
        for _ in 0..<10 {
            number += String(Int.random(in: 0...9))
        }
        return number
    }
    
    static func calculateInterest(principal: Decimal, rate: Decimal, time: Decimal) -> Decimal {
        return principal * rate * time
    }
}

struct Transaction {
    let id: UUID
    let type: TransactionType
    let amount: Decimal
    let description: String
    let timestamp: Date
    
    init(type: TransactionType, amount: Decimal, description: String) {
        self.id = UUID()
        self.type = type
        self.amount = amount
        self.description = description
        self.timestamp = Date()
    }
}

enum TransactionType {
    case deposit
    case withdrawal
    case transfer
}

struct TransactionSummary {
    let totalDeposits: Decimal
    let totalWithdrawals: Decimal
    let netChange: Decimal
    let transactionCount: Int
}

// Usage
let account1 = BankAccount(
    accountNumber: BankAccount.generateAccountNumber(),
    accountHolder: "John Doe",
    initialBalance: 1000.00
)

let account2 = BankAccount(
    accountNumber: BankAccount.generateAccountNumber(),
    accountHolder: "Jane Smith"
)

// Perform transactions
account1.deposit(amount: 500.00, description: "Salary deposit")
account1.withdraw(amount: 200.00, description: "ATM withdrawal")
account1.transfer(amount: 300.00, to: account2, description: "Rent payment")

// Get account information
print("Account 1 balance: \(account1.formattedBalance)")
print("Account 2 balance: \(account2.formattedBalance)")

let summary = account1.getTransactionSummary()
print("Total deposits: \(summary.totalDeposits)")
print("Total withdrawals: \(summary.totalWithdrawals)")
print("Net change: \(summary.netChange)")
print("Transaction count: \(summary.transactionCount)")

// Validate account number
let isValid = BankAccount.validateAccountNumber("1234567890")  // true
let generatedNumber = BankAccount.generateAccountNumber()
print("Generated account number: \(generatedNumber)")

// Calculate interest
let interest = BankAccount.calculateInterest(principal: 1000.00, rate: 0.05, time: 1.0)
print("Interest earned: $\(interest)")
```

**Example 2: Game Character System with Methods**

```swift
class GameCharacter {
    let id: UUID
    let name: String
    let characterClass: CharacterClass
    
    // Stored properties
    var level: Int = 1
    var experience: Int = 0
    var health: Int
    var mana: Int
    var strength: Int
    var agility: Int
    var intelligence: Int
    var inventory: [Item] = []
    var isAlive: Bool = true
    
    // Computed properties
    var maxHealth: Int {
        return 100 + (strength * 10)
    }
    
    var maxMana: Int {
        return 50 + (intelligence * 15)
    }
    
    var attackPower: Int {
        return strength + (agility / 2)
    }
    
    var defense: Int {
        return agility + (strength / 3)
    }
    
    var criticalChance: Double {
        return Double(agility) / 100.0
    }
    
    var healthPercentage: Double {
        return Double(health) / Double(maxHealth)
    }
    
    var manaPercentage: Double {
        return Double(mana) / Double(maxMana)
    }
    
    // Initializer
    init(id: UUID, name: String, characterClass: CharacterClass) {
        self.id = id
        self.name = name
        self.characterClass = characterClass
        
        // Initialize stats based on character class
        switch characterClass {
        case .warrior:
            self.health = 120
            self.mana = 30
            self.strength = 15
            self.agility = 8
            self.intelligence = 5
        case .mage:
            self.health = 80
            self.mana = 100
            self.strength = 5
            self.agility = 6
            self.intelligence = 18
        case .archer:
            self.health = 90
            self.mana = 50
            self.strength = 8
            self.agility = 15
            self.intelligence = 7
        }
    }
    
    // Instance methods
    func takeDamage(_ amount: Int) -> Int {
        guard isAlive else { return 0 }
        
        let actualDamage = max(1, amount - defense)
        health = max(0, health - actualDamage)
        
        if health == 0 {
            isAlive = false
            print("💀 \(name) has been defeated!")
        } else if health <= maxHealth * 0.2 {
            print("🚨 \(name) is critically wounded!")
        }
        
        return actualDamage
    }
    
    func heal(_ amount: Int) -> Int {
        guard isAlive else { return 0 }
        
        let oldHealth = health
        health = min(maxHealth, health + amount)
        let healed = health - oldHealth
        
        if healed > 0 {
            print("💚 \(name) healed \(healed) health")
        }
        
        return healed
    }
    
    func useAbility(_ ability: Ability) -> Bool {
        guard isAlive else {
            print("\(name) is defeated and cannot use abilities")
            return false
        }
        
        guard mana >= ability.manaCost else {
            print("\(name) doesn't have enough mana to use \(ability.name)")
            return false
        }
        
        mana -= ability.manaCost
        
        // Apply ability effects
        switch ability.type {
        case .attack:
            print("⚔️ \(name) used \(ability.name) for \(ability.power) damage!")
        case .heal:
            let healed = heal(ability.power)
            print("💚 \(name) used \(ability.name) and healed \(healed) health!")
        case .buff:
            strength += ability.power
            print("💪 \(name) used \(ability.name) and gained \(ability.power) strength!")
        }
        
        return true
    }
    
    func addItem(_ item: Item) {
        inventory.append(item)
        print("📦 \(name) obtained \(item.name)")
        
        // Apply item effects
        if let statBonus = item.statBonus {
            strength += statBonus.strength
            agility += statBonus.agility
            intelligence += statBonus.intelligence
            print("📈 \(name) stats increased from \(item.name)")
        }
    }
    
    func removeItem(_ item: Item) {
        guard let index = inventory.firstIndex(where: { $0.id == item.id }) else { return }
        
        // Remove item effects
        if let statBonus = item.statBonus {
            strength -= statBonus.strength
            agility -= statBonus.agility
            intelligence -= statBonus.intelligence
            print("📉 \(name) stats decreased from removing \(item.name)")
        }
        
        inventory.remove(at: index)
        print("🗑️ \(name) removed \(item.name)")
    }
    
    func gainExperience(_ amount: Int) {
        experience += amount
        print("✨ \(name) gained \(amount) experience points")
        
        // Check for level up
        let experienceNeeded = level * 100
        if experience >= experienceNeeded {
            levelUp()
        }
    }
    
    private func levelUp() {
        level += 1
        experience = 0
        
        // Increase stats
        strength += 2
        agility += 2
        intelligence += 2
        
        // Restore health and mana
        health = maxHealth
        mana = maxMana
        
        print("🎉 \(name) reached level \(level)!")
        print("📊 Stats increased! Health and mana restored!")
    }
    
    func getStatus() -> CharacterStatus {
        return CharacterStatus(
            name: name,
            level: level,
            health: health,
            maxHealth: maxHealth,
            mana: mana,
            maxMana: maxMana,
            strength: strength,
            agility: agility,
            intelligence: intelligence,
            isAlive: isAlive
        )
    }
    
    // Type methods
    static func createRandomCharacter(name: String) -> GameCharacter {
        let characterClass = CharacterClass.allCases.randomElement() ?? .warrior
        return GameCharacter(id: UUID(), name: name, characterClass: characterClass)
    }
    
    static func compareCharacters(_ char1: GameCharacter, _ char2: GameCharacter) -> CharacterComparison {
        let char1Power = char1.attackPower + char1.defense
        let char2Power = char2.attackPower + char2.defense
        
        if char1Power > char2Power {
            return .firstStronger
        } else if char2Power > char1Power {
            return .secondStronger
        } else {
            return .equal
        }
    }
}

enum CharacterClass: String, CaseIterable {
    case warrior = "Warrior"
    case mage = "Mage"
    case archer = "Archer"
}

struct Ability {
    let id: UUID
    let name: String
    let type: AbilityType
    let power: Int
    let manaCost: Int
    
    enum AbilityType {
        case attack
        case heal
        case buff
    }
}

struct Item {
    let id: UUID
    let name: String
    let description: String
    let statBonus: StatBonus?
    
    struct StatBonus {
        let strength: Int
        let agility: Int
        let intelligence: Int
    }
}

struct CharacterStatus {
    let name: String
    let level: Int
    let health: Int
    let maxHealth: Int
    let mana: Int
    let maxMana: Int
    let strength: Int
    let agility: Int
    let intelligence: Int
    let isAlive: Bool
}

enum CharacterComparison {
    case firstStronger
    case secondStronger
    case equal
}

// Usage
let warrior = GameCharacter(
    id: UUID(),
    name: "Aragorn",
    characterClass: .warrior
)

let mage = GameCharacter(
    id: UUID(),
    name: "Gandalf",
    characterClass: .mage
)

// Combat simulation
print("=== Combat Simulation ===")
let damage = warrior.takeDamage(30)
print("\(warrior.name) took \(damage) damage")

warrior.heal(20)
print("\(warrior.name) health: \(warrior.healthPercentage * 100)%")

// Use abilities
let fireball = Ability(
    id: UUID(),
    name: "Fireball",
    type: .attack,
    power: 25,
    manaCost: 15
)

mage.useAbility(fireball)

// Add items
let sword = Item(
    id: UUID(),
    name: "Steel Sword",
    description: "A sharp steel sword",
    statBonus: Item.StatBonus(strength: 5, agility: 2, intelligence: 0)
)

warrior.addItem(sword)

// Gain experience
warrior.gainExperience(150)

// Get character status
let warriorStatus = warrior.getStatus()
print("\(warriorStatus.name) - Level \(warriorStatus.level)")
print("Health: \(warriorStatus.health)/\(warriorStatus.maxHealth)")
print("Mana: \(warriorStatus.mana)/\(warriorStatus.maxMana)")

// Compare characters
let comparison = GameCharacter.compareCharacters(warrior, mage)
switch comparison {
case .firstStronger:
    print("\(warrior.name) is stronger than \(mage.name)")
case .secondStronger:
    print("\(mage.name) is stronger than \(warrior.name)")
case .equal:
    print("Both characters are equally strong")
}

// Create random character
let randomCharacter = GameCharacter.createRandomCharacter(name: "Random Hero")
print("Created random character: \(randomCharacter.name) - \(randomCharacter.characterClass.rawValue)")
```

### Advanced Method Features

#### Method Overloading

Swift supports method overloading, allowing you to define multiple methods with the same name but different parameters.

```swift
class MathOperations {
    // Overloaded add methods
    func add(_ a: Int, _ b: Int) -> Int {
        return a + b
    }
    
    func add(_ a: Double, _ b: Double) -> Double {
        return a + b
    }
    
    func add(_ a: String, _ b: String) -> String {
        return a + b
    }
    
    func add(_ numbers: [Int]) -> Int {
        return numbers.reduce(0, +)
    }
    
    // Overloaded multiply methods
    func multiply(_ a: Int, _ b: Int) -> Int {
        return a * b
    }
    
    func multiply(_ a: Double, _ b: Double) -> Double {
        return a * b
    }
    
    func multiply(_ a: Int, _ b: Int, _ c: Int) -> Int {
        return a * b * c
    }
    
    // Overloaded utility methods
    func format(_ number: Int) -> String {
        return "\(number)"
    }
    
    func format(_ number: Double) -> String {
        return String(format: "%.2f", number)
    }
    
    func format(_ number: Int, withPrefix prefix: String) -> String {
        return "\(prefix)\(number)"
    }
}

// Usage
let math = MathOperations()

let intSum = math.add(5, 3)           // 8
let doubleSum = math.add(5.5, 3.2)    // 8.7
let stringSum = math.add("Hello", " World")  // "Hello World"
let arraySum = math.add([1, 2, 3, 4, 5])    // 15

let intProduct = math.multiply(4, 6)        // 24
let doubleProduct = math.multiply(4.5, 6.2) // 27.9
let tripleProduct = math.multiply(2, 3, 4)  // 24

let intFormat = math.format(42)                    // "42"
let doubleFormat = math.format(3.14159)            // "3.14"
let prefixedFormat = math.format(42, withPrefix: "$")  // "$42"
```

#### Method Chaining

Method chaining allows you to call multiple methods on an object in a single statement.

```swift
class StringBuilder {
    private var content: String = ""
    
    func append(_ text: String) -> StringBuilder {
        content += text
        return self
    }
    
    func appendLine(_ text: String) -> StringBuilder {
        content += text + "\n"
        return self
    }
    
    func appendFormat(_ format: String, _ arguments: CVarArg...) -> StringBuilder {
        content += String(format: format, arguments: arguments)
        return self
    }
    
    func clear() -> StringBuilder {
        content = ""
        return self
    }
    
    func build() -> String {
        return content
    }
    
    func print() -> StringBuilder {
        Swift.print(content)
        return self
    }
}

// Usage
let builder = StringBuilder()
    .append("Hello")
    .append(" ")
    .append("World")
    .appendLine("!")
    .appendFormat("The answer is %d", 42)
    .appendLine("")
    .print()

let result = builder.build()
print("Built string: \(result)")
```

### Best Practices for Methods

1. **Choose descriptive names** - Method names should clearly describe what they do
2. **Single responsibility** - Each method should do one thing well
3. **Use appropriate access control** - Control who can call your methods
4. **Consider mutating vs non-mutating** - Use `mutating` when modifying state
5. **Overload methods wisely** - Provide clear, useful variations
6. **Return meaningful values** - Return useful information when appropriate
7. **Handle errors gracefully** - Use optionals, throwing, or Result types
8. **Document complex methods** - Add comments for complex logic

**Method design principles:**

- **Encapsulation** - Hide implementation details behind method interfaces
- **Cohesion** - Methods should work together cohesively
- **Coupling** - Minimize dependencies between methods
- **Readability** - Make method calls read like natural language
- **Testability** - Design methods that are easy to test

Methods are essential to Swift programming, providing behavior and functionality to your types. Understanding the different types of methods and when to use each will help you write more organized, maintainable, and powerful code.

---

## Subscripts

Subscripts provide a way to access elements in a collection, sequence, or list using a shorthand syntax. They're a powerful feature that makes your custom types feel like built-in Swift collections. Understanding subscripts will help you create more intuitive and user-friendly APIs.

### Understanding Subscripts

Subscripts allow you to access elements using square bracket notation, similar to how you access arrays and dictionaries.

#### Basic Subscript Syntax

```swift
struct Matrix {
    let rows: Int
    let columns: Int
    private var grid: [Double]
    
    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        self.grid = Array(repeating: 0.0, count: rows * columns)
    }
    
    // Basic subscript for accessing elements
    subscript(row: Int, column: Int) -> Double {
        get {
            assert(row >= 0 && row < rows, "Row index out of range")
            assert(column >= 0 && column < columns, "Column index out of range")
            return grid[row * columns + column]
        }
        set {
            assert(row >= 0 && row < rows, "Row index out of range")
            assert(column >= 0 && column < columns, "Column index out of range")
            grid[row * columns + column] = newValue
        }
    }
    
    // Subscript for accessing entire rows
    subscript(row: Int) -> [Double] {
        get {
            assert(row >= 0 && row < rows, "Row index out of range")
            let startIndex = row * columns
            let endIndex = startIndex + columns
            return Array(grid[startIndex..<endIndex])
        }
        set {
            assert(row >= 0 && row < rows, "Row index out of range")
            assert(newValue.count == columns, "Row must have \(columns) elements")
            let startIndex = row * columns
            for (index, value) in newValue.enumerated() {
                grid[startIndex + index] = value
            }
        }
    }
}

// Usage
var matrix = Matrix(rows: 3, columns: 3)

// Set values using subscript
matrix[0, 0] = 1.0
matrix[0, 1] = 2.0
matrix[0, 2] = 3.0
matrix[1, 0] = 4.0
matrix[1, 1] = 5.0
matrix[1, 2] = 6.0
matrix[2, 0] = 7.0
matrix[2, 1] = 8.0
matrix[2, 2] = 9.0

// Access values using subscript
print("Element at [1, 1]: \(matrix[1, 1])")  // 5.0

// Access entire row using subscript
let firstRow = matrix[0]  // [1.0, 2.0, 3.0]
print("First row: \(firstRow)")

// Modify entire row using subscript
matrix[1] = [10.0, 11.0, 12.0]
print("Modified second row: \(matrix[1])")
```

**Subscript characteristics:**
- **Square bracket syntax** - Use `[]` to access elements
- **Multiple parameters** - Can take any number of parameters
- **Get and set** - Can provide both read and write access
- **Type safety** - Return type is specified in the subscript declaration
- **Validation** - Can include assertions and error checking

#### Subscripts with Different Parameter Types

Subscripts can accept various parameter types, making them very flexible.

```swift
struct MultiIndexCollection {
    private var data: [String: [Int: String]] = [:]
    
    // Subscript with string key
    subscript(key: String) -> [Int: String]? {
        get {
            return data[key]
        }
        set {
            data[key] = newValue
        }
    }
    
    // Subscript with integer key
    subscript(index: Int) -> [String: String]? {
        get {
            var result: [String: String] = [:]
            for (key, value) in data {
                if let stringValue = value[index] {
                    result[key] = stringValue
                }
            }
            return result.isEmpty ? nil : result
        }
    }
    
    // Subscript with tuple key
    subscript(key: String, index: Int) -> String? {
        get {
            return data[key]?[index]
        }
        set {
            if data[key] == nil {
                data[key] = [:]
            }
            data[key]?[index] = newValue
        }
    }
    
    // Subscript with range
    subscript(range: Range<Int>) -> [String: [String]] {
        var result: [String: [String]] = [:]
        for (key, value) in data {
            let filteredValues = value.filter { range.contains($0.key) }.values
            if !filteredValues.isEmpty {
                result[key] = Array(filteredValues)
            }
        }
        return result
    }
}

// Usage
var collection = MultiIndexCollection()

// Set values using different subscript types
collection["users", 1] = "John"
collection["users", 2] = "Jane"
collection["products", 1] = "iPhone"
collection["products", 2] = "iPad"

// Access using string key
if let users = collection["users"] {
    print("Users: \(users)")
}

// Access using integer key
if let index1 = collection[1] {
    print("Index 1: \(index1)")
}

// Access using tuple key
if let user = collection["users", 1] {
    print("User at index 1: \(user)")
}

// Access using range
let range1to2 = collection[1..<3]
print("Range 1-2: \(range1to2)")
```

#### Read-Only Subscripts

Subscripts can be read-only, providing access without the ability to modify values.

```swift
struct ReadOnlyMatrix {
    let rows: Int
    let columns: Int
    private let grid: [Double]
    
    init(rows: Int, columns: Int, initialValue: Double = 0.0) {
        self.rows = rows
        self.columns = columns
        self.grid = Array(repeating: initialValue, count: rows * columns)
    }
    
    // Read-only subscript
    subscript(row: Int, column: Int) -> Double {
        get {
            assert(row >= 0 && row < rows, "Row index out of range")
            assert(column >= 0 && column < columns, "Column index out of range")
            return grid[row * columns + column]
        }
    }
    
    // Read-only subscript for rows
    subscript(row: Int) -> [Double] {
        let startIndex = row * columns
        let endIndex = startIndex + columns
        return Array(grid[startIndex..<endIndex])
    }
    
    // Read-only subscript for columns
    subscript(column column: Int) -> [Double] {
        var result: [Double] = []
        for row in 0..<rows {
            result.append(self[row, column])
        }
        return result
    }
}

// Usage
let matrix = ReadOnlyMatrix(rows: 3, columns: 3, initialValue: 1.0)

// Access values (read-only)
let element = matrix[1, 1]  // 1.0
let row = matrix[0]         // [1.0, 1.0, 1.0]
let column = matrix[column: 1]  // [1.0, 1.0, 1.0]

print("Element: \(element)")
print("Row: \(row)")
print("Column: \(column)")

// This would cause a compilation error:
// matrix[0, 0] = 5.0  // Error: Cannot assign to subscript
```

### Real-World Subscript Examples

Let's see how subscripts are used in practical applications.

**Example 1: Custom Dictionary with Multiple Value Types**

```swift
struct FlexibleDictionary {
    private var storage: [String: Any] = [:]
    
    // Subscript for any value type
    subscript<T>(key: String) -> T? {
        get {
            return storage[key] as? T
        }
        set {
            storage[key] = newValue
        }
    }
    
    // Subscript with default value
    subscript<T>(key: String, default defaultValue: T) -> T {
        get {
            return storage[key] as? T ?? defaultValue
        }
        set {
            storage[key] = newValue
        }
    }
    
    // Subscript for array values
    subscript(key: String) -> [Any] {
        get {
            return storage[key] as? [Any] ?? []
        }
        set {
            storage[key] = newValue
        }
    }
    
    // Subscript for dictionary values
    subscript(key: String) -> [String: Any] {
        get {
            return storage[key] as? [String: Any] ?? [:]
        }
        set {
            storage[key] = newValue
        }
    }
    
    // Subscript for nested access
    subscript(path: String...) -> Any? {
        get {
            var current: Any? = storage
            for key in path {
                if let dict = current as? [String: Any] {
                    current = dict[key]
                } else {
                    return nil
                }
            }
            return current
        }
        set {
            guard path.count > 1 else {
                storage[path[0]] = newValue
                return
            }
            
            var current = storage
            for i in 0..<path.count - 1 {
                let key = path[i]
                if current[key] == nil {
                    current[key] = [String: Any]()
                }
                current = current[key] as! [String: Any]
            }
            current[path.last!] = newValue
        }
    }
    
    // Utility methods
    func removeValue(forKey key: String) {
        storage.removeValue(forKey: key)
    }
    
    func contains(key: String) -> Bool {
        return storage[key] != nil
    }
    
    var keys: [String] {
        return Array(storage.keys)
    }
    
    var values: [Any] {
        return Array(storage.values)
    }
    
    var count: Int {
        return storage.count
    }
}

// Usage
var dict = FlexibleDictionary()

// Store different types
dict["name"] = "John Doe"
dict["age"] = 30
dict["isActive"] = true
dict["scores"] = [85, 92, 78, 96]
dict["address"] = ["street": "123 Main St", "city": "New York"]

// Access with type safety
let name: String? = dict["name"]
let age: Int? = dict["age"]
let scores: [Int] = dict["scores", default: []]
let address: [String: String] = dict["address"]

print("Name: \(name ?? "Unknown")")
print("Age: \(age ?? 0)")
print("Scores: \(scores)")
print("Address: \(address)")

// Nested access
dict["user", "profile", "avatar"] = "profile.jpg"
let avatar = dict["user", "profile", "avatar"] as? String
print("Avatar: \(avatar ?? "None")")

// Array operations
dict["tags"] = ["swift", "ios", "programming"]
let tags: [String] = dict["tags", default: []]
print("Tags: \(tags)")
```

**Example 2: Game Inventory System with Subscripts**

```swift
struct InventorySlot {
    let id: Int
    var item: Item?
    var quantity: Int
    
    init(id: Int) {
        self.id = id
        self.item = nil
        self.quantity = 0
    }
    
    var isEmpty: Bool {
        return item == nil || quantity == 0
    }
    
    var isFull: Bool {
        guard let item = item else { return false }
        return quantity >= item.maxStackSize
    }
}

class Inventory {
    private var slots: [InventorySlot]
    let capacity: Int
    
    init(capacity: Int) {
        self.capacity = capacity
        self.slots = (0..<capacity).map { InventorySlot(id: $0) }
    }
    
    // Subscript for accessing slots by index
    subscript(index: Int) -> InventorySlot? {
        get {
            guard index >= 0 && index < capacity else { return nil }
            return slots[index]
        }
        set {
            guard index >= 0 && index < capacity, let newValue = newValue else { return }
            slots[index] = newValue
        }
    }
    
    // Subscript for accessing slots by item type
    subscript(itemType: ItemType) -> [InventorySlot] {
        return slots.filter { $0.item?.type == itemType }
    }
    
    // Subscript for accessing slots by item name
    subscript(itemName: String) -> [InventorySlot] {
        return slots.filter { $0.item?.name == itemName }
    }
    
    // Subscript for accessing slots by range
    subscript(range: Range<Int>) -> [InventorySlot] {
        guard range.lowerBound >= 0 && range.upperBound <= capacity else { return [] }
        return Array(slots[range])
    }
    
    // Subscript for accessing slots by multiple indices
    subscript(indices: Int...) -> [InventorySlot] {
        return indices.compactMap { self[$0] }
    }
    
    // Methods
    func addItem(_ item: Item, quantity: Int = 1) -> Bool {
        // Find empty slot or slot with same item
        if let existingSlot = slots.first(where: { $0.item?.id == item.id && !$0.isFull }) {
            let spaceAvailable = item.maxStackSize - existingSlot.quantity
            let amountToAdd = min(quantity, spaceAvailable)
            existingSlot.quantity += amountToAdd
            return amountToAdd == quantity
        }
        
        // Find empty slot
        if let emptySlot = slots.first(where: { $0.isEmpty }) {
            emptySlot.item = item
            emptySlot.quantity = min(quantity, item.maxStackSize)
            return true
        }
        
        return false
    }
    
    func removeItem(from slotIndex: Int, quantity: Int = 1) -> Bool {
        guard let slot = self[slotIndex], !slot.isEmpty else { return false }
        
        let amountToRemove = min(quantity, slot.quantity)
        slot.quantity -= amountToRemove
        
        if slot.quantity == 0 {
            slot.item = nil
        }
        
        return true
    }
    
    func getItemCount(_ itemName: String) -> Int {
        return slots
            .filter { $0.item?.name == itemName }
            .reduce(0) { $0 + $1.quantity }
    }
    
    func hasItem(_ itemName: String, quantity: Int = 1) -> Bool {
        return getItemCount(itemName) >= quantity
    }
    
    func getEmptySlots() -> [InventorySlot] {
        return slots.filter { $0.isEmpty }
    }
    
    func getFullSlots() -> [InventorySlot] {
        return slots.filter { $0.isFull }
    }
}

struct Item {
    let id: UUID
    let name: String
    let description: String
    let type: ItemType
    let maxStackSize: Int
    let value: Int
    
    enum ItemType: String, CaseIterable {
        case weapon = "Weapon"
        case armor = "Armor"
        case consumable = "Consumable"
        case material = "Material"
        case accessory = "Accessory"
    }
}

// Usage
let inventory = Inventory(capacity: 20)

// Create some items
let sword = Item(
    id: UUID(),
    name: "Steel Sword",
    description: "A sharp steel sword",
    type: .weapon,
    maxStackSize: 1,
    value: 100
)

let potion = Item(
    id: UUID(),
    name: "Health Potion",
    description: "Restores health",
    type: .consumable,
    maxStackSize: 10,
    value: 25
)

let ore = Item(
    id: UUID(),
    name: "Iron Ore",
    description: "Raw iron ore",
    type: .material,
    maxStackSize: 99,
    value: 5
)

// Add items to inventory
inventory.addItem(sword)
inventory.addItem(potion, quantity: 5)
inventory.addItem(ore, quantity: 15)

// Access slots using different subscript types
let firstSlot = inventory[0]
print("First slot: \(firstSlot?.item?.name ?? "Empty")")

let weaponSlots = inventory[.weapon]
print("Weapon slots: \(weaponSlots.count)")

let potionSlots = inventory["Health Potion"]
print("Health potion slots: \(potionSlots.count)")

let firstFiveSlots = inventory[0..<5]
print("First 5 slots: \(firstFiveSlots.count)")

let specificSlots = inventory[0, 2, 4]
print("Specific slots: \(specificSlots.count)")

// Check inventory status
print("Empty slots: \(inventory.getEmptySlots().count)")
print("Full slots: \(inventory.getFullSlots().count)")
print("Iron ore count: \(inventory.getItemCount("Iron Ore"))")
print("Has 10 health potions: \(inventory.hasItem("Health Potion", quantity: 10))")
```

**Example 3: Data Grid with Advanced Subscripts**

```swift
struct DataGrid<T> {
    private var data: [[T]]
    let rows: Int
    let columns: Int
    
    init(rows: Int, columns: Int, defaultValue: T) {
        self.rows = rows
        self.columns = columns
        self.data = Array(repeating: Array(repeating: defaultValue, count: columns), count: rows)
    }
    
    // Basic subscript for individual cells
    subscript(row: Int, column: Int) -> T {
        get {
            assert(row >= 0 && row < rows, "Row index out of range")
            assert(column >= 0 && column < columns, "Column index out of range")
            return data[row][column]
        }
        set {
            assert(row >= 0 && row < rows, "Row index out of range")
            assert(column >= 0 && column < columns, "Column index out of range")
            data[row][column] = newValue
        }
    }
    
    // Subscript for entire rows
    subscript(row: Int) -> [T] {
        get {
            assert(row >= 0 && row < rows, "Row index out of range")
            return data[row]
        }
        set {
            assert(row >= 0 && row < rows, "Row index out of range")
            assert(newValue.count == columns, "Row must have \(columns) elements")
            data[row] = newValue
        }
    }
    
    // Subscript for entire columns
    subscript(column column: Int) -> [T] {
        get {
            assert(column >= 0 && column < columns, "Column index out of range")
            return data.map { $0[column] }
        }
        set {
            assert(column >= 0 && column < columns, "Column index out of range")
            assert(newValue.count == rows, "Column must have \(rows) elements")
            for (row, value) in newValue.enumerated() {
                data[row][column] = value
            }
        }
    }
    
    // Subscript for rectangular regions
    subscript(rowRange: Range<Int>, columnRange: Range<Int>) -> [[T]] {
        get {
            assert(rowRange.lowerBound >= 0 && rowRange.upperBound <= rows, "Row range out of bounds")
            assert(columnRange.lowerBound >= 0 && columnRange.upperBound <= columns, "Column range out of bounds")
            
            var result: [[T]] = []
            for row in rowRange {
                var rowData: [T] = []
                for column in columnRange {
                    rowData.append(data[row][column])
                }
                result.append(rowData)
            }
            return result
        }
        set {
            assert(rowRange.lowerBound >= 0 && rowRange.upperBound <= rows, "Row range out of bounds")
            assert(columnRange.lowerBound >= 0 && columnRange.upperBound <= columns, "Column range out of bounds")
            assert(newValue.count == rowRange.count, "New data must have \(rowRange.count) rows")
            
            for (rowIndex, row) in newValue.enumerated() {
                assert(row.count == columnRange.count, "Each row must have \(columnRange.count) columns")
                let actualRow = rowRange.lowerBound + rowIndex
                for (columnIndex, value) in row.enumerated() {
                    let actualColumn = columnRange.lowerBound + columnIndex
                    data[actualRow][actualColumn] = value
                }
            }
        }
    }
    
    // Subscript for specific coordinates
    subscript(coordinates: [(row: Int, column: Int)]) -> [T] {
        get {
            return coordinates.map { data[$0.row][$0.column] }
        }
        set {
            assert(coordinates.count == newValue.count, "Number of coordinates must match number of values")
            for (index, coordinate) in coordinates.enumerated() {
                data[coordinate.row][coordinate.column] = newValue[index]
            }
        }
    }
    
    // Utility methods
    func fill(with value: T) {
        for row in 0..<rows {
            for column in 0..<columns {
                data[row][column] = value
            }
        }
    }
    
    func transpose() -> DataGrid<T> {
        let transposed = DataGrid<T>(rows: columns, columns: rows, defaultValue: data[0][0])
        for row in 0..<rows {
            for column in 0..<columns {
                transposed[column, row] = data[row][column]
            }
        }
        return transposed
    }
    
    func printGrid() {
        for row in 0..<rows {
            var rowString = "["
            for column in 0..<columns {
                rowString += "\(data[row][column])"
                if column < columns - 1 {
                    rowString += ", "
                }
            }
            rowString += "]"
            print(rowString)
        }
    }
}

// Usage
var grid = DataGrid(rows: 4, columns: 4, defaultValue: 0)

// Fill with some data
for row in 0..<4 {
    for column in 0..<4 {
        grid[row, column] = row * 4 + column
    }
}

print("Original grid:")
grid.printGrid()

// Access individual cells
print("\nElement at [2, 3]: \(grid[2, 3])")

// Access entire row
let secondRow = grid[1]
print("\nSecond row: \(secondRow)")

// Access entire column
let thirdColumn = grid[column: 2]
print("\nThird column: \(thirdColumn)")

// Access rectangular region
let region = grid[1..<3, 1..<3]
print("\nRegion [1..<3, 1..<3]:")
for row in region {
    print(row)
}

// Modify rectangular region
grid[1..<3, 1..<3] = [[100, 101], [110, 111]]
print("\nAfter modifying region:")
grid.printGrid()

// Access specific coordinates
let coordinates = [(0, 0), (1, 1), (2, 2), (3, 3)]
let diagonalValues = grid[coordinates]
print("\nDiagonal values: \(diagonalValues)")

// Modify specific coordinates
grid[coordinates] = [999, 888, 777, 666]
print("\nAfter modifying diagonal:")
grid.printGrid()

// Transpose the grid
let transposed = grid.transpose()
print("\nTransposed grid:")
transposed.printGrid()
```

### Advanced Subscript Features

#### Subscripts with Generic Types

Subscripts can work with generic types, making them very flexible.

```swift
struct GenericContainer<T> {
    private var items: [T] = []
    
    // Subscript for accessing items by index
    subscript(index: Int) -> T? {
        get {
            guard index >= 0 && index < items.count else { return nil }
            return items[index]
        }
        set {
            guard index >= 0 && index < items.count, let newValue = newValue else { return }
            items[index] = newValue
        }
    }
    
    // Subscript for accessing items by range
    subscript(range: Range<Int>) -> [T] {
        get {
            guard range.lowerBound >= 0 && range.upperBound <= items.count else { return [] }
            return Array(items[range])
        }
        set {
            guard range.lowerBound >= 0 && range.upperBound <= items.count else { return }
            items.replaceSubrange(range, with: newValue)
        }
    }
    
    // Subscript for accessing items by predicate
    subscript(where predicate: (T) -> Bool) -> [T] {
        return items.filter(predicate)
    }
    
    // Methods
    mutating func add(_ item: T) {
        items.append(item)
    }
    
    mutating func remove(at index: Int) {
        guard index >= 0 && index < items.count else { return }
        items.remove(at: index)
    }
    
    var count: Int {
        return items.count
    }
}

// Usage
var stringContainer = GenericContainer<String>()
stringContainer.add("Apple")
stringContainer.add("Banana")
stringContainer.add("Cherry")
stringContainer.add("Date")

// Access by index
if let item = stringContainer[1] {
    print("Item at index 1: \(item)")
}

// Access by range
let range = stringContainer[1..<3]
print("Range 1-2: \(range)")

// Access by predicate
let fruitsWithA = stringContainer { $0.contains("a") }
print("Fruits with 'a': \(fruitsWithA)")

// Modify by index
stringContainer[2] = "Coconut"
print("After modification: \(stringContainer[2] ?? "nil")")

// Modify by range
stringContainer[0..<2] = ["Apricot", "Blueberry"]
print("After range modification:")
for i in 0..<stringContainer.count {
    print("\(i): \(stringContainer[i] ?? "nil")")
}
```

#### Subscripts with Multiple Return Types

Subscripts can return different types based on the parameters.

```swift
struct FlexibleArray {
    private var data: [Any] = []
    
    // Subscript that returns different types based on parameter
    subscript(index: Int) -> Any? {
        get {
            guard index >= 0 && index < data.count else { return nil }
            return data[index]
        }
        set {
            guard index >= 0 && index < data.count else { return }
            data[index] = newValue ?? NSNull()
        }
    }
    
    // Subscript for type-safe access
    subscript<T>(index: Int, as type: T.Type) -> T? {
        guard index >= 0 && index < data.count else { return nil }
        return data[index] as? T
    }
    
    // Subscript for conditional access
    subscript(index: Int, where condition: (Any) -> Bool) -> Any? {
        guard index >= 0 && index < data.count else { return nil }
        let item = data[index]
        return condition(item) ? item : nil
    }
    
    // Methods
    mutating func add(_ item: Any) {
        data.append(item)
    }
    
    mutating func insert(_ item: Any, at index: Int) {
        guard index >= 0 && index <= data.count else { return }
        data.insert(item, at: index)
    }
    
    var count: Int {
        return data.count
    }
}

// Usage
var flexibleArray = FlexibleArray()
flexibleArray.add("Hello")
flexibleArray.add(42)
flexibleArray.add(3.14)
flexibleArray.add(true)

// Access with type safety
let string: String? = flexibleArray[0, as: String.self]
let number: Int? = flexibleArray[1, as: Int.self]
let double: Double? = flexibleArray[2, as: Double.self]
let boolean: Bool? = flexibleArray[3, as: Bool.self]

print("String: \(string ?? "nil")")
print("Number: \(number ?? 0)")
print("Double: \(double ?? 0.0)")
print("Boolean: \(boolean ?? false)")

// Conditional access
let stringItem = flexibleArray[0, where: { $0 is String }]
let numericItem = flexibleArray[1, where: { $0 is Numeric }]

print("String item: \(stringItem ?? "nil")")
print("Numeric item: \(numericItem ?? "nil")")
```

### Best Practices for Subscripts

1. **Choose intuitive syntax** - Make subscript usage feel natural
2. **Provide bounds checking** - Validate indices to prevent crashes
3. **Use appropriate parameter types** - Choose types that make sense for your use case
4. **Consider performance** - Avoid expensive operations in frequently used subscripts
5. **Provide meaningful defaults** - Use default values when appropriate
6. **Document complex subscripts** - Explain how to use complex subscript patterns
7. **Test edge cases** - Ensure subscripts handle boundary conditions correctly
8. **Use consistent naming** - Follow Swift naming conventions

**Subscript design principles:**

- **Intuitive access** - Subscript usage should feel natural and expected
- **Type safety** - Provide type-safe access when possible
- **Performance** - Keep subscript operations efficient
- **Flexibility** - Support multiple access patterns when useful
- **Consistency** - Follow established patterns from Swift standard library

Subscripts are a powerful feature that can make your custom types feel like built-in Swift collections. Understanding how to implement and use subscripts effectively will help you create more intuitive and user-friendly APIs.

---

## Inheritance

Inheritance is a fundamental concept in object-oriented programming that allows one class to inherit properties and methods from another class. Swift provides a powerful inheritance system that enables code reuse, polymorphism, and hierarchical organization of types. Understanding inheritance is crucial for building complex, maintainable applications.

### Understanding Inheritance

Inheritance creates a relationship between classes where one class (the subclass) inherits from another class (the superclass).

#### Basic Inheritance Syntax

```swift
// Base class (superclass)
class Vehicle {
    let make: String
    let model: String
    var year: Int
    var mileage: Double
    
    init(make: String, model: String, year: Int) {
        self.make = make
        self.model = model
        self.year = year
        self.mileage = 0
    }
    
    func start() {
        print("\(make) \(model) is starting")
    }
    
    func stop() {
        print("\(make) \(model) is stopping")
    }
    
    func drive(distance: Double) {
        mileage += distance
        print("Drove \(distance) miles. Total mileage: \(mileage)")
    }
    
    var description: String {
        return "\(year) \(make) \(model) with \(mileage) miles"
    }
}

// Subclass inheriting from Vehicle
class Car: Vehicle {
    let numberOfDoors: Int
    var fuelLevel: Double
    
    // Custom initializer
    init(make: String, model: String, year: Int, numberOfDoors: Int) {
        self.numberOfDoors = numberOfDoors
        self.fuelLevel = 100.0
        super.init(make: make, model: model, year: year)
    }
    
    // Override inherited method
    override func start() {
        guard fuelLevel > 0 else {
            print("Cannot start: no fuel")
            return
        }
        super.start()
    }
    
    // Override inherited method with custom behavior
    override func drive(distance: Double) {
        let fuelConsumption = distance * 0.05 // 0.05 gallons per mile
        fuelLevel -= fuelConsumption
        
        if fuelLevel < 0 {
            fuelLevel = 0
            print("Out of fuel!")
        }
        
        super.drive(distance: distance)
        print("Fuel level: \(fuelLevel)%")
    }
    
    // New method specific to Car
    func refuel(amount: Double) {
        fuelLevel = min(100.0, fuelLevel + amount)
        print("Refueled. Fuel level: \(fuelLevel)%")
    }
    
    // Override computed property
    override var description: String {
        return super.description + " (\(numberOfDoors) doors, \(fuelLevel)% fuel)"
    }
}

// Another subclass
class Motorcycle: Vehicle {
    let engineSize: Double
    var hasSidecar: Bool
    
    init(make: String, model: String, year: Int, engineSize: Double) {
        self.engineSize = engineSize
        self.hasSidecar = false
        super.init(make: make, model: model, year: year)
    }
    
    override func start() {
        print("Starting \(engineSize)L engine")
        super.start()
    }
    
    func attachSidecar() {
        hasSidecar = true
        print("Sidecar attached")
    }
    
    func detachSidecar() {
        hasSidecar = false
        print("Sidecar detached")
    }
    
    override var description: String {
        let sidecarInfo = hasSidecar ? " with sidecar" : ""
        return super.description + " (\(engineSize)L engine\(sidecarInfo))"
    }
}

// Usage
let car = Car(make: "Toyota", model: "Camry", year: 2020, numberOfDoors: 4)
let motorcycle = Motorcycle(make: "Harley-Davidson", model: "Sportster", year: 2019, engineSize: 1.2)

car.start()
car.drive(distance: 50)
car.refuel(amount: 20)

motorcycle.start()
motorcycle.attachSidecar()
motorcycle.drive(distance: 25)

print("Car: \(car.description)")
print("Motorcycle: \(motorcycle.description)")
```

**Inheritance characteristics:**
- **Single inheritance** - Swift classes can inherit from only one superclass
- **Code reuse** - Subclasses inherit all properties and methods from superclass
- **Method overriding** - Subclasses can override inherited methods
- **Polymorphism** - Subclass instances can be used where superclass is expected
- **Initialization chain** - Subclasses must call superclass initializers

#### Method Overriding

Method overriding allows subclasses to provide custom implementations of inherited methods.

```swift
class Animal {
    let name: String
    var age: Int
    
    init(name: String, age: Int) {
        self.name = name
        self.age = age
    }
    
    func makeSound() {
        print("Some animal sound")
    }
    
    func move() {
        print("\(name) is moving")
    }
    
    func eat() {
        print("\(name) is eating")
    }
    
    var description: String {
        return "\(name) (\(age) years old)"
    }
}

class Dog: Animal {
    let breed: String
    var isTrained: Bool
    
    init(name: String, age: Int, breed: String) {
        self.breed = breed
        self.isTrained = false
        super.init(name: name, age: age)
    }
    
    // Override method with custom implementation
    override func makeSound() {
        print("\(name) says: Woof!")
    }
    
    // Override method with additional behavior
    override func move() {
        super.move()
        if isTrained {
            print("\(name) is walking obediently")
        } else {
            print("\(name) is running around excitedly")
        }
    }
    
    // Override computed property
    override var description: String {
        let trainedStatus = isTrained ? "trained" : "untrained"
        return super.description + " - \(breed) (\(trainedStatus))"
    }
    
    // New methods specific to Dog
    func fetch() {
        print("\(name) is fetching the ball")
    }
    
    func train() {
        isTrained = true
        print("\(name) has been trained!")
    }
}

class Cat: Animal {
    let color: String
    var isIndoor: Bool
    
    init(name: String, age: Int, color: String) {
        self.color = color
        self.isIndoor = true
        super.init(name: name, age: age)
    }
    
    override func makeSound() {
        print("\(name) says: Meow!")
    }
    
    override func move() {
        if isIndoor {
            print("\(name) is walking around the house")
        } else {
            print("\(name) is exploring the garden")
        }
    }
    
    override var description: String {
        let location = isIndoor ? "indoor" : "outdoor"
        return super.description + " - \(color) cat (\(location))"
    }
    
    func purr() {
        print("\(name) is purring contentedly")
    }
    
    func goOutside() {
        isIndoor = false
        print("\(name) went outside")
    }
}

// Usage
let dog = Dog(name: "Buddy", age: 3, breed: "Golden Retriever")
let cat = Cat(name: "Whiskers", age: 2, color: "Orange")

// Call overridden methods
dog.makeSound()    // "Buddy says: Woof!"
cat.makeSound()    // "Whiskers says: Meow!"

dog.move()         // "Buddy is moving" + "Buddy is running around excitedly"
cat.move()         // "Whiskers is walking around the house"

// Call specific methods
dog.fetch()
cat.purr()

// Train the dog
dog.train()
dog.move()         // Now shows "walking obediently"

// Let cat go outside
cat.goOutside()
cat.move()         // Now shows "exploring the garden"

// Display descriptions
print("Dog: \(dog.description)")
print("Cat: \(cat.description)")
```

#### Property Overriding

Subclasses can override inherited properties to provide custom getters, setters, and observers.

```swift
class Shape {
    var name: String
    var color: String
    
    init(name: String, color: String) {
        self.name = name
        self.color = color
    }
    
    // Computed property that can be overridden
    var area: Double {
        return 0.0
    }
    
    // Computed property with getter and setter
    var description: String {
        get {
            return "\(color) \(name)"
        }
        set {
            // Parse new description to update properties
            let components = newValue.components(separatedBy: " ")
            if components.count >= 2 {
                color = components[0]
                name = components[1...].joined(separator: " ")
            }
        }
    }
    
    // Property with observers
    var isVisible: Bool = true {
        didSet {
            print("\(name) visibility changed to: \(isVisible)")
        }
    }
}

class Circle: Shape {
    var radius: Double
    
    init(radius: Double, color: String) {
        self.radius = radius
        super.init(name: "Circle", color: color)
    }
    
    // Override computed property
    override var area: Double {
        return Double.pi * radius * radius
    }
    
    // Override property with custom getter and setter
    override var description: String {
        get {
            return super.description + " with radius \(radius)"
        }
        set {
            super.description = newValue
            // Extract radius from description if possible
            if let radiusRange = newValue.range(of: "radius ") {
                let radiusString = String(newValue[radiusRange.upperBound...])
                if let newRadius = Double(radiusString) {
                    radius = newRadius
                }
            }
        }
    }
    
    // Override property observers
    override var isVisible: Bool {
        didSet {
            super.isVisible = isVisible
            if isVisible {
                print("Circle with radius \(radius) is now visible")
            } else {
                print("Circle with radius \(radius) is now hidden")
            }
        }
    }
}

class Rectangle: Shape {
    var width: Double
    var height: Double
    
    init(width: Double, height: Double, color: String) {
        self.width = width
        self.height = height
        super.init(name: "Rectangle", color: color)
    }
    
    override var area: Double {
        return width * height
    }
    
    override var description: String {
        return super.description + " (\(width) × \(height))"
    }
    
    // Override property observers
    override var isVisible: Bool {
        didSet {
            super.isVisible = isVisible
            let status = isVisible ? "visible" : "hidden"
            print("\(width) × \(height) rectangle is now \(status)")
        }
    }
}

// Usage
let circle = Circle(radius: 5.0, color: "Red")
let rectangle = Rectangle(width: 10.0, height: 5.0, color: "Blue")

print("Circle area: \(circle.area)")
print("Rectangle area: \(rectangle.area)")

print("Circle: \(circle.description)")
print("Rectangle: \(rectangle.description)")

// Test property observers
circle.isVisible = false
rectangle.isVisible = false

// Test property setters
circle.description = "Green Circle with radius 7.5"
print("Updated circle: \(circle.description)")
print("New radius: \(circle.radius)")
```

### Real-World Inheritance Examples

Let's see how inheritance is used in practical applications.

**Example 1: Employee Management System**

```swift
class Employee {
    let id: UUID
    let name: String
    let email: String
    var hireDate: Date
    var salary: Decimal
    var isActive: Bool
    
    init(id: UUID, name: String, email: String, hireDate: Date, salary: Decimal) {
        self.id = id
        self.name = name
        self.email = email
        self.hireDate = hireDate
        self.salary = salary
        self.isActive = true
    }
    
    func work() {
        print("\(name) is working")
    }
    
    func takeBreak() {
        print("\(name) is taking a break")
    }
    
    func requestTimeOff(days: Int) -> Bool {
        print("\(name) requested \(days) days off")
        return true
    }
    
    func calculatePay() -> Decimal {
        return salary
    }
    
    var yearsOfService: Int {
        let calendar = Calendar.current
        let components = calendar.dateComponents([.year], from: hireDate, to: Date())
        return components.year ?? 0
    }
    
    var description: String {
        return "\(name) (\(email)) - \(yearsOfService) years of service"
    }
}

class Manager: Employee {
    var teamSize: Int
    var department: String
    private var directReports: [Employee] = []
    
    init(id: UUID, name: String, email: String, hireDate: Date, salary: Decimal, department: String) {
        self.department = department
        self.teamSize = 0
        super.init(id: id, name: name, email: email, hireDate: hireDate, salary: salary)
    }
    
    override func work() {
        print("\(name) is managing the \(department) department")
    }
    
    override func calculatePay() -> Decimal {
        // Managers get a bonus based on team size
        let teamBonus = Decimal(teamSize) * 100
        return salary + teamBonus
    }
    
    func addTeamMember(_ employee: Employee) {
        directReports.append(employee)
        teamSize = directReports.count
        print("\(employee.name) added to \(name)'s team")
    }
    
    func removeTeamMember(_ employee: Employee) {
        if let index = directReports.firstIndex(where: { $0.id == employee.id }) {
            directReports.remove(at: index)
            teamSize = directReports.count
            print("\(employee.name) removed from \(name)'s team")
        }
    }
    
    func conductPerformanceReview(for employee: Employee) {
        print("\(name) is conducting performance review for \(employee.name)")
    }
    
    override var description: String {
        return super.description + " - \(department) Manager (Team: \(teamSize))"
    }
}

class Developer: Employee {
    var programmingLanguages: [String]
    var experienceLevel: ExperienceLevel
    var currentProject: String?
    
    enum ExperienceLevel: String, CaseIterable {
        case junior = "Junior"
        case mid = "Mid-Level"
        case senior = "Senior"
        case lead = "Lead"
        
        var salaryMultiplier: Decimal {
            switch self {
            case .junior: return 1.0
            case .mid: return 1.3
            case .senior: return 1.6
            case .lead: return 2.0
            }
        }
    }
    
    init(id: UUID, name: String, email: String, hireDate: Date, baseSalary: Decimal, experienceLevel: ExperienceLevel) {
        self.programmingLanguages = []
        self.experienceLevel = experienceLevel
        super.init(id: id, name: name, email: email, hireDate: hireDate, salary: baseSalary * experienceLevel.salaryMultiplier)
    }
    
    override func work() {
        if let project = currentProject {
            print("\(name) is coding on \(project)")
        } else {
            print("\(name) is coding")
        }
    }
    
    func addProgrammingLanguage(_ language: String) {
        if !programmingLanguages.contains(language) {
            programmingLanguages.append(language)
            print("\(name) learned \(language)")
        }
    }
    
    func assignToProject(_ project: String) {
        currentProject = project
        print("\(name) assigned to \(project)")
    }
    
    override func calculatePay() -> Decimal {
        // Developers get bonuses for each programming language
        let languageBonus = Decimal(programmingLanguages.count) * 50
        return salary + languageBonus
    }
    
    override var description: String {
        let projectInfo = currentProject != nil ? " on \(currentProject!)" : ""
        return super.description + " - \(experienceLevel.rawValue) Developer\(projectInfo)"
    }
}

class SalesPerson: Employee {
    var salesTarget: Decimal
    var currentSales: Decimal
    var commissionRate: Decimal
    
    init(id: UUID, name: String, email: String, hireDate: Date, baseSalary: Decimal, salesTarget: Decimal, commissionRate: Decimal) {
        self.salesTarget = salesTarget
        self.currentSales = 0
        self.commissionRate = commissionRate
        super.init(id: id, name: name, email: email, hireDate: hireDate, salary: baseSalary)
    }
    
    override func work() {
        print("\(name) is making sales calls")
    }
    
    func makeSale(amount: Decimal) {
        currentSales += amount
        print("\(name) made a sale of $\(amount)")
    }
    
    override func calculatePay() -> Decimal {
        let commission = currentSales * commissionRate
        return salary + commission
    }
    
    var performancePercentage: Double {
        return Double((currentSales / salesTarget) * 100)
    }
    
    override var description: String {
        return super.description + " - Sales (Target: $\(salesTarget), Current: $\(currentSales))"
    }
}

// Usage
let manager = Manager(
    id: UUID(),
    name: "Sarah Johnson",
    email: "sarah@company.com",
    hireDate: Date().addingTimeInterval(-365*24*3600*2), // 2 years ago
    salary: 80000,
    department: "Engineering"
)

let developer = Developer(
    id: UUID(),
    name: "Mike Chen",
    email: "mike@company.com",
    hireDate: Date().addingTimeInterval(-365*24*3600), // 1 year ago
    baseSalary: 70000,
    experienceLevel: .senior
)

let salesPerson = SalesPerson(
    id: UUID(),
    name: "Alex Rodriguez",
    email: "alex@company.com",
    hireDate: Date().addingTimeInterval(-365*24*3600*6), // 6 months ago
    baseSalary: 50000,
    salesTarget: 100000,
    commissionRate: 0.1
)

// Build team
manager.addTeamMember(developer)

// Assign developer to project
developer.assignToProject("Mobile App")
developer.addProgrammingLanguage("Swift")
developer.addProgrammingLanguage("Kotlin")

// Make sales
salesPerson.makeSale(amount: 25000)
salesPerson.makeSale(amount: 30000)

// Display employee information
print("Manager: \(manager.description)")
print("Developer: \(developer.description)")
print("Sales Person: \(salesPerson.description)")

// Calculate pay
print("Manager pay: $\(manager.calculatePay())")
print("Developer pay: $\(developer.calculatePay())")
print("Sales Person pay: $\(salesPerson.calculatePay())")

// Performance review
manager.conductPerformanceReview(for: developer)

// Check sales performance
print("Sales performance: \(salesPerson.performancePercentage)%")
```

**Example 2: Banking System with Inheritance**

```swift
class BankAccount {
    let accountNumber: String
    let accountHolder: String
    var balance: Decimal
    var isActive: Bool
    private var transactions: [Transaction] = []
    
    init(accountNumber: String, accountHolder: String, initialBalance: Decimal = 0) {
        self.accountNumber = accountNumber
        self.accountHolder = accountHolder
        self.balance = initialBalance
        self.isActive = true
        
        if initialBalance > 0 {
            let initialDeposit = Transaction(
                type: .deposit,
                amount: initialBalance,
                description: "Initial deposit"
            )
            transactions.append(initialDeposit)
        }
    }
    
    func deposit(amount: Decimal) -> Bool {
        guard isActive else { return false }
        guard amount > 0 else { return false }
        
        balance += amount
        let transaction = Transaction(
            type: .deposit,
            amount: amount,
            description: "Deposit"
        )
        transactions.append(transaction)
        
        print("Deposited $\(amount) to account \(accountNumber)")
        return true
    }
    
    func withdraw(amount: Decimal) -> Bool {
        guard isActive else { return false }
        guard amount > 0 else { return false }
        guard balance >= amount else { return false }
        
        balance -= amount
        let transaction = Transaction(
            type: .withdrawal,
            amount: amount,
            description: "Withdrawal"
        )
        transactions.append(transaction)
        
        print("Withdrew $\(amount) from account \(accountNumber)")
        return true
    }
    
    func getTransactionHistory() -> [Transaction] {
        return transactions
    }
    
    var description: String {
        return "\(accountHolder) - \(accountNumber) - $\(balance)"
    }
}

class SavingsAccount: BankAccount {
    let interestRate: Decimal
    var minimumBalance: Decimal
    
    init(accountNumber: String, accountHolder: String, initialBalance: Decimal, interestRate: Decimal, minimumBalance: Decimal) {
        self.interestRate = interestRate
        self.minimumBalance = minimumBalance
        super.init(accountNumber: accountNumber, accountHolder: accountHolder, initialBalance: initialBalance)
    }
    
    override func withdraw(amount: Decimal) -> Bool {
        guard balance - amount >= minimumBalance else {
            print("Withdrawal would violate minimum balance requirement")
            return false
        }
        
        return super.withdraw(amount: amount)
    }
    
    func addInterest() {
        let interest = balance * interestRate
        balance += interest
        
        let transaction = Transaction(
            type: .interest,
            amount: interest,
            description: "Interest payment"
        )
        
        print("Added $\(interest) interest to account \(accountNumber)")
    }
    
    override var description: String {
        return super.description + " (Savings - \(interestRate * 100)% interest)"
    }
}

class CheckingAccount: BankAccount {
    var monthlyFee: Decimal
    var overdraftLimit: Decimal
    var checkNumber: Int
    
    init(accountNumber: String, accountHolder: String, initialBalance: Decimal, monthlyFee: Decimal, overdraftLimit: Decimal) {
        self.monthlyFee = monthlyFee
        self.overdraftLimit = overdraftLimit
        self.checkNumber = 1001
        super.init(accountNumber: accountNumber, accountHolder: accountHolder, initialBalance: initialBalance)
    }
    
    override func withdraw(amount: Decimal) -> Bool {
        guard isActive else { return false }
        guard amount > 0 else { return false }
        
        let availableBalance = balance + overdraftLimit
        guard availableBalance >= amount else { return false }
        
        balance -= amount
        let transaction = Transaction(
            type: .withdrawal,
            amount: amount,
            description: "Withdrawal"
        )
        transactions.append(transaction)
        
        if balance < 0 {
            print("Account is now overdrawn by $\(abs(balance))")
        }
        
        print("Withdrew $\(amount) from account \(accountNumber)")
        return true
    }
    
    func writeCheck(amount: Decimal, to payee: String) -> Int {
        guard withdraw(amount: amount) else { return 0 }
        
        let checkNum = checkNumber
        checkNumber += 1
        
        let transaction = Transaction(
            type: .check,
            amount: amount,
            description: "Check #\(checkNum) to \(payee)"
        )
        transactions.append(transaction)
        
        print("Wrote check #\(checkNum) for $\(amount) to \(payee)")
        return checkNum
    }
    
    func chargeMonthlyFee() {
        balance -= monthlyFee
        
        let transaction = Transaction(
            type: .fee,
            amount: monthlyFee,
            description: "Monthly maintenance fee"
        )
        transactions.append(transaction)
        
        print("Charged monthly fee of $\(monthlyFee)")
    }
    
    override var description: String {
        return super.description + " (Checking - $\(monthlyFee) monthly fee)"
    }
}

class BusinessAccount: BankAccount {
    let businessName: String
    let businessType: String
    var authorizedUsers: [String]
    var businessCreditLimit: Decimal
    
    init(accountNumber: String, accountHolder: String, businessName: String, businessType: String, initialBalance: Decimal, creditLimit: Decimal) {
        self.businessName = businessName
        self.businessType = businessType
        self.authorizedUsers = [accountHolder]
        self.businessCreditLimit = creditLimit
        super.init(accountNumber: accountNumber, accountHolder: accountHolder, initialBalance: initialBalance)
    }
    
    func addAuthorizedUser(_ username: String) {
        if !authorizedUsers.contains(username) {
            authorizedUsers.append(username)
            print("Added \(username) as authorized user")
        }
    }
    
    func removeAuthorizedUser(_ username: String) {
        if let index = authorizedUsers.firstIndex(of: username) {
            authorizedUsers.remove(at: index)
            print("Removed \(username) as authorized user")
        }
    }
    
    override func withdraw(amount: Decimal) -> Bool {
        let availableBalance = balance + businessCreditLimit
        guard availableBalance >= amount else { return false }
        
        balance -= amount
        let transaction = Transaction(
            type: .withdrawal,
            amount: amount,
            description: "Business withdrawal"
        )
        transactions.append(transaction)
        
        print("Business withdrawal of $\(amount) from account \(accountNumber)")
        return true
    }
    
    func getBusinessReport() -> BusinessReport {
        let totalDeposits = transactions.filter { $0.type == .deposit }.reduce(0) { $0 + $1.amount }
        let totalWithdrawals = transactions.filter { $0.type == .withdrawal }.reduce(0) { $0 + $1.amount }
        
        return BusinessReport(
            businessName: businessName,
            accountNumber: accountNumber,
            balance: balance,
            totalDeposits: totalDeposits,
            totalWithdrawals: totalWithdrawals,
            authorizedUsers: authorizedUsers
        )
    }
    
    override var description: String {
        return super.description + " (Business - \(businessType))"
    }
}

struct Transaction {
    let id: UUID
    let type: TransactionType
    let amount: Decimal
    let description: String
    let timestamp: Date
    
    enum TransactionType {
        case deposit, withdrawal, interest, fee, check
    }
    
    init(type: TransactionType, amount: Decimal, description: String) {
        self.id = UUID()
        self.type = type
        self.amount = amount
        self.description = description
        self.timestamp = Date()
    }
}

struct BusinessReport {
    let businessName: String
    let accountNumber: String
    let balance: Decimal
    let totalDeposits: Decimal
    let totalWithdrawals: Decimal
    let authorizedUsers: [String]
}

// Usage
let savings = SavingsAccount(
    accountNumber: "SAV001",
    accountHolder: "John Doe",
    initialBalance: 5000,
    interestRate: 0.025,
    minimumBalance: 1000
)

let checking = CheckingAccount(
    accountNumber: "CHK001",
    accountHolder: "John Doe",
    initialBalance: 2000,
    monthlyFee: 15,
    overdraftLimit: 500
)

let business = BusinessAccount(
    accountNumber: "BUS001",
    accountHolder: "Jane Smith",
    businessName: "Tech Solutions Inc",
    businessType: "Technology Services",
    initialBalance: 10000,
    creditLimit: 50000
)

// Test different account types
savings.deposit(amount: 1000)
savings.withdraw(amount: 500)
savings.addInterest()

checking.deposit(amount: 500)
checking.withdraw(amount: 100)
checking.writeCheck(amount: 200, to: "Utility Company")
checking.chargeMonthlyFee()

business.deposit(amount: 5000)
business.addAuthorizedUser("Manager1")
business.addAuthorizedUser("Manager2")
business.withdraw(amount: 3000)

// Display account information
print("Savings: \(savings.description)")
print("Checking: \(checking.description)")
print("Business: \(business.description)")

// Get business report
let report = business.getBusinessReport()
print("Business Report for \(report.businessName)")
print("Balance: $\(report.balance)")
print("Total Deposits: $\(report.totalDeposits)")
print("Total Withdrawals: $\(report.totalWithdrawals)")
print("Authorized Users: \(report.authorizedUsers.joined(separator: ", "))")
```

### Advanced Inheritance Features

#### Final Classes

Final classes cannot be inherited from, providing performance benefits and preventing further customization.

```swift
final class StringProcessor {
    private var cache: [String: String] = [:]
    
    func process(_ input: String) -> String {
        if let cached = cache[input] {
            return cached
        }
        
        let processed = input.uppercased().trimmingCharacters(in: .whitespaces)
        cache[input] = processed
        return processed
    }
    
    func clearCache() {
        cache.removeAll()
    }
}

// This would cause a compilation error:
// class ExtendedStringProcessor: StringProcessor { } // Error: Inheritance from a final class
```

#### Required Initializers

Required initializers must be implemented by all subclasses.

```swift
class DatabaseConnection {
    let host: String
    let port: Int
    let database: String
    
    required init(host: String, port: Int, database: String) {
        self.host = host
        self.port = port
        self.database = database
    }
    
    func connect() {
        print("Connecting to \(host):\(port)/\(database)")
    }
}

class MySQLConnection: DatabaseConnection {
    let version: String
    
    required init(host: String, port: Int, database: String) {
        self.version = "8.0"
        super.init(host: host, port: port, database: database)
    }
    
    override func connect() {
        print("Connecting to MySQL \(version) at \(host):\(port)/\(database)")
    }
}

class PostgreSQLConnection: DatabaseConnection {
    let sslMode: String
    
    required init(host: String, port: Int, database: String) {
        self.sslMode = "require"
        super.init(host: host, port: port, database: database)
    }
    
    override func connect() {
        print("Connecting to PostgreSQL at \(host):\(port)/\(database) with SSL mode: \(sslMode)")
    }
}

// Usage
let mysql = MySQLConnection(host: "localhost", port: 3306, database: "testdb")
let postgres = PostgreSQLConnection(host: "localhost", port: 5432, database: "testdb")

mysql.connect()
postgres.connect()
```

### Best Practices for Inheritance

1. **Use inheritance for "is-a" relationships** - Subclass should be a specialized version of superclass
2. **Keep inheritance hierarchies shallow** - Avoid deep inheritance chains
3. **Use composition over inheritance** - Favor composition when possible
4. **Override methods carefully** - Ensure overridden methods maintain expected behavior
5. **Use final classes** - Mark classes as final when inheritance is not needed
6. **Design for inheritance** - Consider how subclasses will use your class
7. **Document inheritance contracts** - Clearly specify what subclasses must implement
8. **Test inheritance hierarchies** - Ensure all subclasses work correctly

**Inheritance design principles:**

- **Liskov Substitution Principle** - Subclasses should be usable wherever superclass is expected
- **Single Responsibility** - Each class should have one reason to change
- **Open/Closed Principle** - Open for extension, closed for modification
- **Interface Segregation** - Keep interfaces focused and cohesive
- **Dependency Inversion** - Depend on abstractions, not concretions

Inheritance is a powerful tool for creating hierarchical relationships between classes. Understanding when and how to use inheritance effectively will help you build more organized, maintainable, and extensible code.

---

## Initialization

Initialization is the process of preparing an instance of a class, structure, or enumeration for use. Swift provides a comprehensive initialization system that ensures all properties have valid values before an instance is used. Understanding initialization is crucial for creating robust, safe code.

### Understanding Initialization

Initialization involves setting initial values for stored properties and performing any other setup required for the instance to work correctly.

#### Basic Initializers

Initializers are special methods that create and configure instances of a type.

```swift
struct Person {
    let name: String
    var age: Int
    var email: String?
    var isActive: Bool
    
    // Custom initializer
    init(name: String, age: Int, email: String? = nil) {
        self.name = name
        self.age = age
        self.email = email
        self.isActive = true
    }
    
    // Convenience initializer
    init(name: String, age: Int) {
        self.init(name: name, age: age, email: nil)
    }
    
    // Convenience initializer for anonymous users
    init(age: Int) {
        self.init(name: "Anonymous", age: age, email: nil)
    }
}

class BankAccount {
    let accountNumber: String
    let accountHolder: String
    var balance: Decimal
    var isActive: Bool
    private var transactions: [String] = []
    
    // Designated initializer
    init(accountNumber: String, accountHolder: String, initialBalance: Decimal = 0) {
        self.accountNumber = accountNumber
        self.accountHolder = accountHolder
        self.balance = initialBalance
        self.isActive = true
        
        if initialBalance > 0 {
            transactions.append("Initial deposit: $\(initialBalance)")
        }
    }
    
    // Convenience initializer
    convenience init(accountHolder: String) {
        let accountNumber = UUID().uuidString.prefix(8).uppercased()
        self.init(accountNumber: String(accountNumber), accountHolder: accountHolder, initialBalance: 0)
    }
    
    // Convenience initializer with default account holder
    convenience init() {
        self.init(accountHolder: "Unknown")
    }
}

// Usage
let person1 = Person(name: "John Doe", age: 30, email: "john@example.com")
let person2 = Person(name: "Jane Smith", age: 25)
let person3 = Person(age: 18)

let account1 = BankAccount(accountNumber: "12345678", accountHolder: "John Doe", initialBalance: 1000)
let account2 = BankAccount(accountHolder: "Jane Smith")
let account3 = BankAccount()

print("Person 1: \(person1.name), \(person1.age), \(person1.email ?? "No email")")
print("Person 2: \(person2.name), \(person2.age), \(person2.email ?? "No email")")
print("Person 3: \(person3.name), \(person3.age), \(person3.email ?? "No email")")

print("Account 1: \(account1.accountNumber), \(account1.accountHolder), $\(account1.balance)")
print("Account 2: \(account2.accountNumber), \(account2.accountHolder), $\(account2.balance)")
print("Account 3: \(account3.accountNumber), \(account3.accountHolder), $\(account3.balance)")
```

**Initializer characteristics:**
- **Property initialization** - All stored properties must have values
- **Two-phase initialization** - Properties initialized before methods called
- **Initializer delegation** - Initializers can call other initializers
- **Default values** - Properties can have default values
- **Optional properties** - Optional properties default to `nil`

#### Initializer Parameters

Initializers can have various parameter types and configurations.

```swift
struct Rectangle {
    var width: Double
    var height: Double
    var color: String
    var isVisible: Bool
    
    // Initializer with external parameter names
    init(width: Double, height: Double, color: String = "Black", isVisible: Bool = true) {
        self.width = width
        self.height = height
        self.color = color
        self.isVisible = isVisible
    }
    
    // Initializer with custom external parameter names
    init(width w: Double, height h: Double) {
        self.width = w
        self.height = h
        self.color = "Black"
        self.isVisible = true
    }
    
    // Initializer with omitted external parameter names
    init(_ width: Double, _ height: Double) {
        self.width = width
        self.height = height
        self.color = "Black"
        self.isVisible = true
    }
    
    // Initializer with default values
    init(square size: Double) {
        self.width = size
        self.height = size
        self.color = "Black"
        self.isVisible = true
    }
    
    // Computed properties
    var area: Double {
        return width * height
    }
    
    var perimeter: Double {
        return 2 * (width + height)
    }
}

// Usage
let rect1 = Rectangle(width: 10, height: 5, color: "Red")
let rect2 = Rectangle(width: 8, height: 6)
let rect3 = Rectangle(width: 12, height: 8, color: "Blue", isVisible: false)
let rect4 = Rectangle(width: 15, height: 10)
let rect5 = Rectangle(20, 15)
let square = Rectangle(square: 7)

print("Rectangle 1: \(rect1.width) × \(rect1.height), \(rect1.color), visible: \(rect1.isVisible)")
print("Rectangle 2: \(rect2.width) × \(rect2.height), \(rect2.color), visible: \(rect2.isVisible)")
print("Rectangle 3: \(rect3.width) × \(rect3.height), \(rect3.color), visible: \(rect3.isVisible)")
print("Rectangle 4: \(rect4.width) × \(rect4.height), \(rect4.color), visible: \(rect4.isVisible)")
print("Rectangle 5: \(rect5.width) × \(rect5.height), \(rect5.color), visible: \(rect5.isVisible)")
print("Square: \(square.width) × \(square.height), \(square.color), visible: \(square.isVisible)")

print("Rectangle 1 area: \(rect1.area)")
print("Rectangle 1 perimeter: \(rect1.perimeter)")
```

#### Failable Initializers

Failable initializers can return `nil` if initialization fails.

```swift
struct Temperature {
    let celsius: Double
    
    // Failable initializer for Celsius
    init?(celsius: Double) {
        guard celsius >= -273.15 else { return nil } // Absolute zero
        self.celsius = celsius
    }
    
    // Failable initializer for Fahrenheit
    init?(fahrenheit: Double) {
        let celsius = (fahrenheit - 32) * 5/9
        self.init(celsius: celsius)
    }
    
    // Failable initializer for Kelvin
    init?(kelvin: Double) {
        let celsius = kelvin - 273.15
        self.init(celsius: celsius)
    }
    
    // Computed properties
    var fahrenheit: Double {
        return celsius * 9/5 + 32
    }
    
    var kelvin: Double {
        return celsius + 273.15
    }
    
    var description: String {
        return "\(celsius)°C (\(fahrenheit)°F, \(kelvin)K)"
    }
}

struct EmailAddress {
    let address: String
    
    // Failable initializer with validation
    init?(_ address: String) {
        let emailRegex = #"^[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$"#
        
        guard address.range(of: emailRegex, options: .regularExpression) != nil else {
            return nil
        }
        
        self.address = address
    }
    
    var domain: String {
        let components = address.components(separatedBy: "@")
        return components.count > 1 ? components[1] : ""
    }
    
    var username: String {
        let components = address.components(separatedBy: "@")
        return components.first ?? ""
    }
}

// Usage
let temp1 = Temperature(celsius: 25)
let temp2 = Temperature(fahrenheit: 98.6)
let temp3 = Temperature(kelvin: 300)
let temp4 = Temperature(celsius: -300) // Will be nil

print("Temperature 1: \(temp1?.description ?? "Invalid")")
print("Temperature 2: \(temp2?.description ?? "Invalid")")
print("Temperature 3: \(temp3?.description ?? "Invalid")")
print("Temperature 4: \(temp4?.description ?? "Invalid")")

let email1 = EmailAddress("user@example.com")
let email2 = EmailAddress("invalid-email")
let email3 = EmailAddress("another@domain.org")

print("Email 1: \(email1?.address ?? "Invalid") - Domain: \(email1?.domain ?? "N/A")")
print("Email 2: \(email2?.address ?? "Invalid")")
print("Email 3: \(email3?.address ?? "Invalid") - Username: \(email3?.username ?? "N/A")")
```

### Real-World Initialization Examples

Let's see how initialization is used in practical applications.

**Example 1: Configuration Management System**

```swift
struct AppConfig {
    let appName: String
    let version: String
    let buildNumber: Int
    let environment: Environment
    let apiBaseURL: String
    let timeout: TimeInterval
    let maxRetries: Int
    let debugMode: Bool
    
    enum Environment: String, CaseIterable {
        case development = "Development"
        case staging = "Staging"
        case production = "Production"
        
        var isDebugEnabled: Bool {
            return self == .development || self == .staging
        }
        
        var apiTimeout: TimeInterval {
            switch self {
            case .development: return 30
            case .staging: return 60
            case .production: return 120
            }
        }
    }
    
    // Designated initializer
    init(appName: String, version: String, buildNumber: Int, environment: Environment) {
        self.appName = appName
        self.version = version
        self.buildNumber = buildNumber
        self.environment = environment
        
        // Set environment-specific values
        switch environment {
        case .development:
            self.apiBaseURL = "http://localhost:8080/api"
            self.timeout = environment.apiTimeout
            self.maxRetries = 3
            self.debugMode = true
        case .staging:
            self.apiBaseURL = "https://staging-api.example.com/api"
            self.timeout = environment.apiTimeout
            self.maxRetries = 2
            self.debugMode = true
        case .production:
            self.apiBaseURL = "https://api.example.com/api"
            self.timeout = environment.apiTimeout
            self.maxRetries = 1
            self.debugMode = false
        }
    }
    
    // Convenience initializer for development
    convenience init(development appName: String, version: String, buildNumber: Int) {
        self.init(appName: appName, version: version, buildNumber: buildNumber, environment: .development)
    }
    
    // Convenience initializer for production
    convenience init(production appName: String, version: String, buildNumber: Int) {
        self.init(appName: appName, version: version, buildNumber: buildNumber, environment: .production)
    }
    
    // Failable initializer from dictionary
    init?(from dictionary: [String: Any]) {
        guard let appName = dictionary["appName"] as? String,
              let version = dictionary["version"] as? String,
              let buildNumber = dictionary["buildNumber"] as? Int,
              let environmentString = dictionary["environment"] as? String,
              let environment = Environment(rawValue: environmentString) else {
            return nil
        }
        
        self.init(appName: appName, version: version, buildNumber: buildNumber, environment: environment)
    }
    
    // Failable initializer from JSON data
    init?(from jsonData: Data) {
        guard let json = try? JSONSerialization.jsonObject(with: jsonData) as? [String: Any] else {
            return nil
        }
        
        self.init(from: json)
    }
    
    var description: String {
        return "\(appName) v\(version) (\(buildNumber)) - \(environment.rawValue)"
    }
    
    var debugInfo: String {
        return """
        App: \(appName)
        Version: \(version)
        Build: \(buildNumber)
        Environment: \(environment.rawValue)
        API URL: \(apiBaseURL)
        Timeout: \(timeout)s
        Max Retries: \(maxRetries)
        Debug Mode: \(debugMode)
        """
    }
}

// Usage
let devConfig = AppConfig(development: "MyApp", version: "1.0.0", buildNumber: 100)
let prodConfig = AppConfig(production: "MyApp", version: "1.0.0", buildNumber: 100)

print("Development Config: \(devConfig.description)")
print("Production Config: \(prodConfig.description)")

print("\nDevelopment Debug Info:")
print(devConfig.debugInfo)

print("\nProduction Debug Info:")
print(prodConfig.debugInfo)

// Test failable initializer
let configDict: [String: Any] = [
    "appName": "TestApp",
    "version": "2.0.0",
    "buildNumber": 200,
    "environment": "Staging"
]

if let configFromDict = AppConfig(from: configDict) {
    print("\nConfig from dictionary: \(configFromDict.description)")
} else {
    print("Failed to create config from dictionary")
}

// Test JSON initialization
let jsonString = """
{
    "appName": "JSONApp",
    "version": "3.0.0",
    "buildNumber": 300,
    "environment": "Production"
}
"""

if let jsonData = jsonString.data(using: .utf8),
   let configFromJSON = AppConfig(from: jsonData) {
    print("\nConfig from JSON: \(configFromJSON.description)")
} else {
    print("Failed to create config from JSON")
}
```

**Example 2: Database Connection Pool**

```swift
class DatabaseConnection {
    let id: UUID
    let host: String
    let port: Int
    let database: String
    let username: String
    let password: String
    var isConnected: Bool
    var lastUsed: Date
    var connectionCount: Int
    
    // Designated initializer
    init(host: String, port: Int, database: String, username: String, password: String) {
        self.id = UUID()
        self.host = host
        self.port = port
        self.database = database
        self.username = username
        self.password = password
        self.isConnected = false
        self.lastUsed = Date()
        self.connectionCount = 0
    }
    
    // Convenience initializer with default port
    convenience init(host: String, database: String, username: String, password: String) {
        self.init(host: host, port: 5432, database: database, username: username, password: password)
    }
    
    // Convenience initializer for local development
    convenience init(local database: String) {
        self.init(host: "localhost", port: 5432, database: database, username: "postgres", password: "password")
    }
    
    // Failable initializer from connection string
    init?(connectionString: String) {
        // Parse connection string format: "host:port/database?username=user&password=pass"
        let components = connectionString.components(separatedBy: "/")
        guard components.count == 2 else { return nil }
        
        let hostPort = components[0].components(separatedBy: ":")
        let host = hostPort[0]
        let port = hostPort.count > 1 ? Int(hostPort[1]) ?? 5432 : 5432
        
        let dbAndParams = components[1].components(separatedBy: "?")
        let database = dbAndParams[0]
        
        var username = "postgres"
        var password = ""
        
        if dbAndParams.count > 1 {
            let params = dbAndParams[1].components(separatedBy: "&")
            for param in params {
                let keyValue = param.components(separatedBy: "=")
                if keyValue.count == 2 {
                    switch keyValue[0] {
                    case "username": username = keyValue[1]
                    case "password": password = keyValue[1]
                    default: break
                    }
                }
            }
        }
        
        self.init(host: host, port: port, database: database, username: username, password: password)
    }
    
    func connect() -> Bool {
        guard !isConnected else { return true }
        
        // Simulate connection logic
        print("Connecting to \(host):\(port)/\(database) as \(username)")
        
        // Simulate connection delay
        Thread.sleep(forTimeInterval: 0.1)
        
        isConnected = true
        lastUsed = Date()
        connectionCount += 1
        
        print("Successfully connected to database")
        return true
    }
    
    func disconnect() {
        guard isConnected else { return }
        
        print("Disconnecting from \(host):\(port)/\(database)")
        isConnected = false
    }
    
    func execute(_ query: String) -> Bool {
        guard isConnected else {
            print("Cannot execute query: not connected")
            return false
        }
        
        print("Executing query: \(query)")
        lastUsed = Date()
        return true
    }
    
    var connectionInfo: String {
        return "\(username)@\(host):\(port)/\(database) - Connected: \(isConnected), Uses: \(connectionCount)"
    }
}

class ConnectionPool {
    private var connections: [DatabaseConnection] = []
    private let maxConnections: Int
    private let connectionTimeout: TimeInterval
    
    init(maxConnections: Int = 10, connectionTimeout: TimeInterval = 300) {
        self.maxConnections = maxConnections
        self.connectionTimeout = connectionTimeout
    }
    
    // Convenience initializer for development
    convenience init(development maxConnections: Int = 5) {
        self.init(maxConnections: maxConnections, connectionTimeout: 60)
    }
    
    // Convenience initializer for production
    convenience init(production maxConnections: Int = 20) {
        self.init(maxConnections: maxConnections, connectionTimeout: 600)
    }
    
    func addConnection(_ connection: DatabaseConnection) -> Bool {
        guard connections.count < maxConnections else {
            print("Connection pool is full")
            return false
        }
        
        connections.append(connection)
        print("Added connection to pool. Total: \(connections.count)/\(maxConnections)")
        return true
    }
    
    func getConnection() -> DatabaseConnection? {
        // Find an available connection
        if let availableConnection = connections.first(where: { $0.isConnected }) {
            availableConnection.lastUsed = Date()
            return availableConnection
        }
        
        // Try to connect an existing connection
        if let connection = connections.first(where: { !$0.isConnected }) {
            if connection.connect() {
                return connection
            }
        }
        
        return nil
    }
    
    func removeExpiredConnections() {
        let cutoffDate = Date().addingTimeInterval(-connectionTimeout)
        connections.removeAll { $0.lastUsed < cutoffDate }
        print("Removed expired connections. Remaining: \(connections.count)")
    }
    
    var poolStatus: String {
        let connected = connections.filter { $0.isConnected }.count
        let total = connections.count
        return "Pool Status: \(connected)/\(total) connected, \(maxConnections - total) available slots"
    }
}

// Usage
let devPool = ConnectionPool(development: 3)
let prodPool = ConnectionPool(production: 15)

// Create connections using different initializers
let localConnection = DatabaseConnection(local: "devdb")
let remoteConnection = DatabaseConnection(host: "db.example.com", database: "proddb", username: "admin", password: "secret")
let stringConnection = DatabaseConnection(connectionString: "localhost:5432/testdb?username=test&password=test123")

// Add connections to pools
devPool.addConnection(localConnection!)
devPool.addConnection(stringConnection!)

prodPool.addConnection(remoteConnection!)

// Test connections
if let conn1 = devPool.getConnection() {
    print("Got connection: \(conn1.connectionInfo)")
    conn1.execute("SELECT * FROM users")
}

if let conn2 = prodPool.getConnection() {
    print("Got connection: \(conn2.connectionInfo)")
    conn2.execute("SELECT COUNT(*) FROM orders")
}

// Display pool status
print("\nDevelopment Pool: \(devPool.poolStatus)")
print("Production Pool: \(prodPool.poolStatus)")

// Test connection string parsing
let testStrings = [
    "localhost:5432/mydb",
    "db.example.com:5432/proddb?username=user&password=pass",
    "invalid-string"
]

for connectionString in testStrings {
    if let connection = DatabaseConnection(connectionString: connectionString) {
        print("Successfully parsed: \(connection.connectionInfo)")
    } else {
        print("Failed to parse: \(connectionString)")
    }
}
```

### Advanced Initialization Features

#### Required Initializers

Required initializers must be implemented by all subclasses.

```swift
class Animal {
    let name: String
    let species: String
    
    required init(name: String, species: String) {
        self.name = name
        self.species = species
    }
    
    required init(name: String) {
        self.name = name
        self.species = "Unknown"
    }
    
    var description: String {
        return "\(name) (\(species))"
    }
}

class Dog: Animal {
    let breed: String
    
    required init(name: String, species: String) {
        self.breed = "Mixed"
        super.init(name: name, species: species)
    }
    
    required init(name: String) {
        self.breed = "Mixed"
        super.init(name: name)
    }
    
    init(name: String, breed: String) {
        self.breed = breed
        super.init(name: name, species: "Canis familiaris")
    }
    
    override var description: String {
        return super.description + " - \(breed)"
    }
}

class Cat: Animal {
    let color: String
    
    required init(name: String, species: String) {
        self.color = "Unknown"
        super.init(name: name, species: species)
    }
    
    required init(name: String) {
        self.color = "Unknown"
        super.init(name: name)
    }
    
    init(name: String, color: String) {
        self.color = color
        super.init(name: name, species: "Felis catus")
    }
    
    override var description: String {
        return super.description + " - \(color)"
    }
}

// Usage
let dog1 = Dog(name: "Buddy", species: "Canis familiaris")
let dog2 = Dog(name: "Max")
let dog3 = Dog(name: "Rex", breed: "German Shepherd")

let cat1 = Cat(name: "Whiskers", species: "Felis catus")
let cat2 = Cat(name: "Fluffy")
let cat3 = Cat(name: "Shadow", color: "Black")

print("Dog 1: \(dog1.description)")
print("Dog 2: \(dog2.description)")
print("Dog 3: \(dog3.description)")

print("Cat 1: \(cat1.description)")
print("Cat 2: \(cat2.description)")
print("Cat 3: \(cat3.description)")
```

#### Initializer Delegation

Initializers can call other initializers to avoid code duplication.

```swift
class Vehicle {
    let make: String
    let model: String
    var year: Int
    var mileage: Double
    
    // Designated initializer
    init(make: String, model: String, year: Int, mileage: Double = 0) {
        self.make = make
        self.model = model
        self.year = year
        self.mileage = mileage
    }
    
    // Convenience initializer
    convenience init(make: String, model: String, year: Int) {
        self.init(make: make, model: model, year: year, mileage: 0)
    }
    
    // Convenience initializer with current year
    convenience init(make: String, model: String) {
        let currentYear = Calendar.current.component(.year, from: Date())
        self.init(make: make, model: model, year: currentYear)
    }
    
    var description: String {
        return "\(year) \(make) \(model) with \(mileage) miles"
    }
}

class Car: Vehicle {
    let numberOfDoors: Int
    var fuelLevel: Double
    
    // Designated initializer
    init(make: String, model: String, year: Int, mileage: Double, numberOfDoors: Int, fuelLevel: Double = 100.0) {
        self.numberOfDoors = numberOfDoors
        self.fuelLevel = fuelLevel
        super.init(make: make, model: model, year: year, mileage: mileage)
    }
    
    // Convenience initializer
    convenience init(make: String, model: String, year: Int, numberOfDoors: Int) {
        self.init(make: make, model: model, year: year, mileage: 0, numberOfDoors: numberOfDoors)
    }
    
    // Convenience initializer with current year
    convenience init(make: String, model: String, numberOfDoors: Int) {
        let currentYear = Calendar.current.component(.year, from: Date())
        self.init(make: make, model: model, year: currentYear, numberOfDoors: numberOfDoors)
    }
    
    // Convenience initializer with default doors
    convenience init(make: String, model: String, year: Int) {
        self.init(make: make, model: model, year: year, numberOfDoors: 4)
    }
    
    override var description: String {
        return super.description + " (\(numberOfDoors) doors, \(fuelLevel)% fuel)"
    }
}

// Usage
let vehicle1 = Vehicle(make: "Toyota", model: "Camry", year: 2020)
let vehicle2 = Vehicle(make: "Honda", model: "Civic")
let vehicle3 = Vehicle(make: "Ford", model: "Focus", year: 2019, mileage: 15000)

let car1 = Car(make: "BMW", model: "3 Series", year: 2021, numberOfDoors: 4)
let car2 = Car(make: "Mercedes", model: "C-Class", numberOfDoors: 2)
let car3 = Car(make: "Audi", model: "A4", year: 2020, mileage: 5000, numberOfDoors: 4, fuelLevel: 75.0)

print("Vehicle 1: \(vehicle1.description)")
print("Vehicle 2: \(vehicle2.description)")
print("Vehicle 3: \(vehicle3.description)")

print("Car 1: \(car1.description)")
print("Car 2: \(car2.description)")
print("Car 3: \(car3.description)")
```

### Best Practices for Initialization

1. **Initialize all properties** - Ensure all stored properties have values
2. **Use convenience initializers** - Reduce code duplication with convenience initializers
3. **Provide meaningful defaults** - Use default parameter values when appropriate
4. **Validate input** - Use failable initializers for validation
5. **Keep initializers simple** - Avoid complex logic in initializers
6. **Use required initializers** - When subclasses must implement specific initializers
7. **Document initialization** - Clearly document initialization requirements
8. **Test edge cases** - Ensure initializers handle all scenarios correctly

**Initialization design principles:**

- **Complete initialization** - All properties must have valid values
- **Two-phase initialization** - Properties initialized before methods called
- **Initializer delegation** - Use convenience initializers to avoid duplication
- **Validation** - Use failable initializers for input validation
- **Default values** - Provide sensible defaults when possible

Initialization is a critical part of Swift programming that ensures type safety and proper object setup. Understanding how to implement effective initializers will help you create robust, maintainable code.

---

## Deinitialization

Deinitialization is the process of cleaning up resources when an instance of a class is no longer needed. Swift uses Automatic Reference Counting (ARC) to manage memory, and deinitializers provide a way to perform cleanup operations before an object is deallocated. Understanding deinitialization is crucial for managing resources properly and avoiding memory leaks.

### Understanding Deinitialization

Deinitializers are called automatically when an instance of a class is about to be deallocated, allowing you to perform cleanup operations.

#### Basic Deinitializer Syntax

```swift
class FileManager {
    let filePath: String
    var fileHandle: FileHandle?
    var isOpen: Bool = false
    
    init(filePath: String) {
        self.filePath = filePath
        print("FileManager initialized for: \(filePath)")
    }
    
    func openFile() {
        // Simulate opening a file
        isOpen = true
        print("File opened: \(filePath)")
    }
    
    func closeFile() {
        if isOpen {
            isOpen = false
            print("File closed: \(filePath)")
        }
    }
    
    // Deinitializer - called automatically when instance is deallocated
    deinit {
        print("FileManager deinitializing for: \(filePath)")
        
        // Clean up resources
        closeFile()
        
        // Release file handle if it exists
        fileHandle = nil
        
        print("FileManager cleanup completed for: \(filePath)")
    }
}

class DatabaseConnection {
    let connectionString: String
    var isConnected: Bool = false
    var connectionId: String
    
    init(connectionString: String) {
        self.connectionString = connectionString
        self.connectionId = UUID().uuidString.prefix(8).uppercased()
        print("Database connection initialized: \(connectionId)")
    }
    
    func connect() {
        isConnected = true
        print("Database connected: \(connectionId)")
    }
    
    func disconnect() {
        if isConnected {
            isConnected = false
            print("Database disconnected: \(connectionId)")
        }
    }
    
    deinit {
        print("Database connection deinitializing: \(connectionId)")
        
        // Ensure connection is closed
        disconnect()
        
        // Log cleanup
        print("Database connection cleanup completed: \(connectionId)")
    }
}

// Usage
func testFileManager() {
    let fileManager = FileManager(filePath: "/path/to/file.txt")
    fileManager.openFile()
    
    // fileManager will be deallocated when this function ends
    print("Function ending, fileManager will be deallocated")
}

func testDatabaseConnection() {
    let dbConnection = DatabaseConnection(connectionString: "localhost:5432/mydb")
    dbConnection.connect()
    
    // dbConnection will be deallocated when this function ends
    print("Function ending, dbConnection will be deallocated")
}

print("=== Testing File Manager ===")
testFileManager()

print("\n=== Testing Database Connection ===")
testDatabaseConnection()

print("\nBoth functions completed")
```

**Deinitializer characteristics:**
- **Automatic calling** - Called automatically by ARC when instance is deallocated
- **No parameters** - Cannot take parameters or return values
- **No manual calling** - Cannot be called directly
- **Cleanup operations** - Used for releasing resources, closing connections, etc.
- **Class-only** - Only available in classes, not structures or enumerations

#### Deinitialization with Resource Management

Deinitializers are particularly useful for managing external resources that need to be properly released.

```swift
class NetworkManager {
    let url: String
    var session: URLSession?
    var activeTasks: [URLSessionDataTask] = []
    
    init(url: String) {
        self.url = url
        self.session = URLSession.shared
        print("NetworkManager initialized for: \(url)")
    }
    
    func startRequest() -> URLSessionDataTask? {
        guard let session = session else { return nil }
        
        let task = session.dataTask(with: URL(string: url)!) { data, response, error in
            if let error = error {
                print("Request failed: \(error)")
            } else {
                print("Request completed successfully")
            }
        }
        
        activeTasks.append(task)
        task.resume()
        print("Started network request")
        return task
    }
    
    func cancelAllRequests() {
        for task in activeTasks {
            task.cancel()
        }
        activeTasks.removeAll()
        print("Cancelled all network requests")
    }
    
    deinit {
        print("NetworkManager deinitializing for: \(url)")
        
        // Cancel any active network requests
        cancelAllRequests()
        
        // Clean up session
        session = nil
        
        print("NetworkManager cleanup completed for: \(url)")
    }
}

class AudioPlayer {
    let audioFile: String
    var isPlaying: Bool = false
    var volume: Float = 1.0
    
    init(audioFile: String) {
        self.audioFile = audioFile
        print("AudioPlayer initialized for: \(audioFile)")
    }
    
    func play() {
        isPlaying = true
        print("Playing audio: \(audioFile) at volume \(volume)")
    }
    
    func stop() {
        if isPlaying {
            isPlaying = false
            print("Stopped audio: \(audioFile)")
        }
    }
    
    func setVolume(_ newVolume: Float) {
        volume = max(0.0, min(1.0, newVolume))
        print("Volume set to: \(volume)")
    }
    
    deinit {
        print("AudioPlayer deinitializing for: \(audioFile)")
        
        // Stop playback
        stop()
        
        // Reset volume
        volume = 1.0
        
        print("AudioPlayer cleanup completed for: \(audioFile)")
    }
}

// Usage
func testNetworkManager() {
    let networkManager = NetworkManager(url: "https://api.example.com")
    networkManager.startRequest()
    
    // Simulate some work
    print("Network manager is working...")
    
    // networkManager will be deallocated when this function ends
}

func testAudioPlayer() {
    let audioPlayer = AudioPlayer(audioFile: "song.mp3")
    audioPlayer.play()
    audioPlayer.setVolume(0.8)
    
    // Simulate some work
    print("Audio player is working...")
    
    // audioPlayer will be deallocated when this function ends
}

print("=== Testing Network Manager ===")
testNetworkManager()

print("\n=== Testing Audio Player ===")
testAudioPlayer()

print("\nBoth functions completed")
```

### Real-World Deinitialization Examples

Let's see how deinitialization is used in practical applications.

**Example 1: File System Manager with Resource Cleanup**

```swift
class FileSystemManager {
    let basePath: String
    var openFiles: [String: FileHandle] = [:]
    var tempFiles: [String] = []
    var isMonitoring: Bool = false
    
    init(basePath: String) {
        self.basePath = basePath
        print("FileSystemManager initialized for: \(basePath)")
    }
    
    func openFile(_ filename: String) -> Bool {
        let fullPath = "\(basePath)/\(filename)"
        
        // Simulate opening a file
        let fileHandle = FileHandle()
        openFiles[filename] = fileHandle
        print("Opened file: \(filename)")
        return true
    }
    
    func closeFile(_ filename: String) {
        if let fileHandle = openFiles.removeValue(forKey: filename) {
            // Simulate closing file
            print("Closed file: \(filename)")
        }
    }
    
    func createTempFile() -> String {
        let tempFilename = "temp_\(UUID().uuidString).tmp"
        tempFiles.append(tempFilename)
        print("Created temp file: \(tempFilename)")
        return tempFilename
    }
    
    func deleteTempFile(_ filename: String) {
        if let index = tempFiles.firstIndex(of: filename) {
            tempFiles.remove(at: index)
            print("Deleted temp file: \(filename)")
        }
    }
    
    func startMonitoring() {
        isMonitoring = true
        print("Started file system monitoring")
    }
    
    func stopMonitoring() {
        isMonitoring = false
        print("Stopped file system monitoring")
    }
    
    deinit {
        print("FileSystemManager deinitializing for: \(basePath)")
        
        // Close all open files
        for filename in openFiles.keys {
            closeFile(filename)
        }
        
        // Delete all temp files
        for tempFile in tempFiles {
            deleteTempFile(tempFile)
        }
        
        // Stop monitoring
        if isMonitoring {
            stopMonitoring()
        }
        
        print("FileSystemManager cleanup completed for: \(basePath)")
    }
}

class FileHandle {
    let id: String
    
    init() {
        self.id = UUID().uuidString.prefix(8).uppercased()
        print("FileHandle created: \(id)")
    }
    
    deinit {
        print("FileHandle deallocated: \(id)")
    }
}

// Usage
func testFileSystemManager() {
    let fileManager = FileSystemManager(basePath: "/var/tmp")
    
    // Open some files
    fileManager.openFile("document1.txt")
    fileManager.openFile("document2.txt")
    
    // Create temp files
    let temp1 = fileManager.createTempFile()
    let temp2 = fileManager.createTempFile()
    
    // Start monitoring
    fileManager.startMonitoring()
    
    // Simulate some work
    print("File system manager is working...")
    
    // fileManager will be deallocated when this function ends
}

print("=== Testing File System Manager ===")
testFileSystemManager()
print("Function completed")
```

**Example 2: Database Connection Pool with Cleanup**

```swift
class DatabaseConnectionPool {
    let poolName: String
    let maxConnections: Int
    var connections: [DatabaseConnection] = []
    var isActive: Bool = true
    
    init(poolName: String, maxConnections: Int = 10) {
        self.poolName = poolName
        self.maxConnections = maxConnections
        print("DatabaseConnectionPool initialized: \(poolName)")
    }
    
    func addConnection(_ connection: DatabaseConnection) -> Bool {
        guard connections.count < maxConnections else {
            print("Pool is full, cannot add connection")
            return false
        }
        
        connections.append(connection)
        print("Added connection to pool. Total: \(connections.count)/\(maxConnections)")
        return true
    }
    
    func getConnection() -> DatabaseConnection? {
        guard isActive else { return nil }
        
        if let connection = connections.first(where: { !$0.isInUse }) {
            connection.isInUse = true
            connection.lastUsed = Date()
            print("Got connection from pool")
            return connection
        }
        
        print("No available connections in pool")
        return nil
    }
    
    func returnConnection(_ connection: DatabaseConnection) {
        connection.isInUse = false
        print("Returned connection to pool")
    }
    
    func closeAllConnections() {
        for connection in connections {
            connection.close()
        }
        connections.removeAll()
        print("Closed all connections in pool")
    }
    
    deinit {
        print("DatabaseConnectionPool deinitializing: \(poolName)")
        
        // Stop accepting new requests
        isActive = false
        
        // Close all connections
        closeAllConnections()
        
        print("DatabaseConnectionPool cleanup completed: \(poolName)")
    }
}

class DatabaseConnection {
    let id: String
    let host: String
    let database: String
    var isInUse: Bool = false
    var isConnected: Bool = false
    var lastUsed: Date
    
    init(host: String, database: String) {
        self.id = UUID().uuidString.prefix(8).uppercased()
        self.host = host
        self.database = database
        self.lastUsed = Date()
        print("DatabaseConnection created: \(id)")
    }
    
    func connect() {
        isConnected = true
        print("DatabaseConnection \(id) connected to \(host)/\(database)")
    }
    
    func close() {
        if isConnected {
            isConnected = false
            print("DatabaseConnection \(id) closed")
        }
    }
    
    deinit {
        print("DatabaseConnection deallocating: \(id)")
        
        // Ensure connection is closed
        close()
        
        print("DatabaseConnection cleanup completed: \(id)")
    }
}

// Usage
func testDatabasePool() {
    let pool = DatabaseConnectionPool(poolName: "MainPool", maxConnections: 5)
    
    // Create and add connections
    for i in 1...3 {
        let connection = DatabaseConnection(host: "db\(i).example.com", database: "mydb")
        connection.connect()
        pool.addConnection(connection)
    }
    
    // Use some connections
    if let conn1 = pool.getConnection() {
        print("Using connection: \(conn1.id)")
        pool.returnConnection(conn1)
    }
    
    if let conn2 = pool.getConnection() {
        print("Using connection: \(conn2.id)")
        pool.returnConnection(conn2)
    }
    
    // Simulate some work
    print("Database pool is working...")
    
    // pool will be deallocated when this function ends
}

print("=== Testing Database Pool ===")
testDatabasePool()
print("Function completed")
```

**Example 3: Cache Manager with Memory Management**

```swift
class CacheManager {
    let cacheName: String
    let maxSize: Int
    var cache: [String: CacheEntry] = [:]
    var accessOrder: [String] = []
    var isMonitoring: Bool = false
    
    init(cacheName: String, maxSize: Int = 100) {
        self.cacheName = cacheName
        self.maxSize = maxSize
        print("CacheManager initialized: \(cacheName)")
    }
    
    func setValue(_ value: Any, forKey key: String) {
        // Remove oldest entry if cache is full
        if cache.count >= maxSize {
            evictOldestEntry()
        }
        
        let entry = CacheEntry(key: key, value: value)
        cache[key] = entry
        
        // Update access order
        if let index = accessOrder.firstIndex(of: key) {
            accessOrder.remove(at: index)
        }
        accessOrder.append(key)
        
        print("Cached value for key: \(key)")
    }
    
    func getValue(forKey key: String) -> Any? {
        guard let entry = cache[key] else { return nil }
        
        // Update access order
        if let index = accessOrder.firstIndex(of: key) {
            accessOrder.remove(at: index)
        }
        accessOrder.append(key)
        
        entry.accessCount += 1
        entry.lastAccessed = Date()
        
        print("Retrieved value for key: \(key)")
        return entry.value
    }
    
    func removeValue(forKey key: String) {
        cache.removeValue(forKey: key)
        accessOrder.removeAll { $0 == key }
        print("Removed value for key: \(key)")
    }
    
    private func evictOldestEntry() {
        guard let oldestKey = accessOrder.first else { return }
        removeValue(forKey: oldestKey)
        print("Evicted oldest entry: \(oldestKey)")
    }
    
    func startMonitoring() {
        isMonitoring = true
        print("Started cache monitoring")
    }
    
    func stopMonitoring() {
        isMonitoring = false
        print("Stopped cache monitoring")
    }
    
    func clearCache() {
        cache.removeAll()
        accessOrder.removeAll()
        print("Cleared all cache entries")
    }
    
    var cacheStats: String {
        return "Cache: \(cacheName), Entries: \(cache.count)/\(maxSize), Monitoring: \(isMonitoring)"
    }
    
    deinit {
        print("CacheManager deinitializing: \(cacheName)")
        
        // Stop monitoring
        if isMonitoring {
            stopMonitoring()
        }
        
        // Clear cache
        clearCache()
        
        print("CacheManager cleanup completed: \(cacheName)")
    }
}

class CacheEntry {
    let key: String
    let value: Any
    var accessCount: Int
    var lastAccessed: Date
    let created: Date
    
    init(key: String, value: Any) {
        self.key = key
        self.value = value
        self.accessCount = 0
        self.lastAccessed = Date()
        self.created = Date()
        print("CacheEntry created for key: \(key)")
    }
    
    deinit {
        print("CacheEntry deallocated for key: \(key)")
    }
}

// Usage
func testCacheManager() {
    let cache = CacheManager(cacheName: "UserCache", maxSize: 5)
    
    // Start monitoring
    cache.startMonitoring()
    
    // Add some values
    cache.setValue("John Doe", forKey: "user1")
    cache.setValue("Jane Smith", forKey: "user2")
    cache.setValue(25, forKey: "age1")
    cache.setValue(30, forKey: "age2")
    cache.setValue("john@example.com", forKey: "email1")
    
    // Access some values
    _ = cache.getValue(forKey: "user1")
    _ = cache.getValue(forKey: "user2")
    
    // Add one more (should trigger eviction)
    cache.setValue("Bob Johnson", forKey: "user3")
    
    // Display cache stats
    print("Cache stats: \(cache.cacheStats)")
    
    // Simulate some work
    print("Cache manager is working...")
    
    // cache will be deallocated when this function ends
}

print("=== Testing Cache Manager ===")
testCacheManager()
print("Function completed")
```

### Advanced Deinitialization Features

#### Deinitialization with Weak References

Understanding how weak references affect deinitialization timing.

```swift
class Parent {
    let name: String
    weak var child: Child?
    
    init(name: String) {
        self.name = name
        print("Parent initialized: \(name)")
    }
    
    deinit {
        print("Parent deallocated: \(name)")
    }
}

class Child {
    let name: String
    var parent: Parent?
    
    init(name: String) {
        self.name = name
        print("Child initialized: \(name)")
    }
    
    deinit {
        print("Child deallocated: \(name)")
    }
}

// Usage
func testWeakReferences() {
    let parent = Parent(name: "John")
    let child = Child(name: "Junior")
    
    // Create circular reference with weak reference
    parent.child = child
    child.parent = parent
    
    print("Both objects created with weak reference")
    
    // parent and child will be deallocated when this function ends
    // The weak reference prevents a retain cycle
}

print("=== Testing Weak References ===")
testWeakReferences()
print("Function completed")
```

#### Deinitialization with Closures

Managing closure capture lists to avoid retain cycles.

```swift
class EventManager {
    let name: String
    var eventHandlers: [String: () -> Void] = [:]
    
    init(name: String) {
        self.name = name
        print("EventManager initialized: \(name)")
    }
    
    func addEventHandler(for event: String, handler: @escaping () -> Void) {
        eventHandlers[event] = handler
        print("Added event handler for: \(event)")
    }
    
    func removeEventHandler(for event: String) {
        eventHandlers.removeValue(forKey: event)
        print("Removed event handler for: \(event)")
    }
    
    func triggerEvent(_ event: String) {
        if let handler = eventHandlers[event] {
            handler()
            print("Triggered event: \(event)")
        }
    }
    
    deinit {
        print("EventManager deinitializing: \(name)")
        
        // Clear all event handlers
        eventHandlers.removeAll()
        
        print("EventManager cleanup completed: \(name)")
    }
}

class EventSubscriber {
    let name: String
    weak var eventManager: EventManager?
    
    init(name: String, eventManager: EventManager) {
        self.name = name
        self.eventManager = eventManager
        
        // Use weak self in closure to avoid retain cycle
        eventManager.addEventHandler(for: "userAction") { [weak self] in
            guard let self = self else { return }
            print("\(self.name) received userAction event")
        }
        
        print("EventSubscriber initialized: \(name)")
    }
    
    deinit {
        print("EventSubscriber deallocated: \(name)")
    }
}

// Usage
func testEventManager() {
    let eventManager = EventManager(name: "MainEvents")
    let subscriber = EventSubscriber(name: "UserInterface", eventManager: eventManager)
    
    // Trigger an event
    eventManager.triggerEvent("userAction")
    
    // Simulate some work
    print("Event manager is working...")
    
    // Both objects will be deallocated when this function ends
    // The weak reference in the closure prevents retain cycles
}

print("=== Testing Event Manager ===")
testEventManager()
print("Function completed")
```

### Best Practices for Deinitialization

1. **Use for cleanup only** - Deinitializers should only perform cleanup operations
2. **Avoid complex logic** - Keep deinitializers simple and fast
3. **Release resources** - Close files, connections, and release external resources
4. **Handle weak references** - Use weak references to avoid retain cycles
5. **Test deinitialization** - Ensure cleanup happens correctly in all scenarios
6. **Avoid side effects** - Don't perform operations that could fail
7. **Document cleanup** - Clearly document what the deinitializer does
8. **Consider timing** - Deinitialization timing depends on ARC

**Deinitialization design principles:**

- **Resource cleanup** - Release all acquired resources
- **State cleanup** - Reset object state to safe values
- **Connection cleanup** - Close all open connections
- **Memory management** - Avoid retain cycles with weak references
- **Predictable behavior** - Ensure cleanup happens reliably

Deinitialization is an important part of Swift's memory management system. Understanding how to implement effective deinitializers will help you manage resources properly and avoid memory leaks in your applications.

---

## Optional Chaining

Optional chaining is a powerful Swift feature that allows you to call properties, methods, and subscripts on optional values without unwrapping them first. It provides a safe and concise way to work with optional values and can help prevent runtime crashes. Understanding optional chaining is essential for writing robust, safe Swift code.

### Understanding Optional Chaining

Optional chaining allows you to access properties and methods on optional values, automatically handling the case where the value is `nil`.

#### Basic Optional Chaining Syntax

```swift
class Person {
    let name: String
    var residence: Residence?
    
    init(name: String) {
        self.name = name
    }
    
    var description: String {
        return "Person: \(name)"
    }
}

class Residence {
    let address: String
    var rooms: [Room] = []
    
    init(address: String) {
        self.address = address
    }
    
    var numberOfRooms: Int {
        return rooms.count
    }
    
    func addRoom(_ room: Room) {
        rooms.append(room)
    }
    
    subscript(index: Int) -> Room? {
        guard index >= 0 && index < rooms.count else { return nil }
        return rooms[index]
    }
}

class Room {
    let name: String
    let area: Double
    
    init(name: String, area: Double) {
        self.name = name
        self.area = area
    }
    
    var description: String {
        return "\(name) (\(area) sq ft)"
    }
}

// Usage
let john = Person(name: "John")
let jane = Person(name: "Jane")

// Create a residence for Jane
let janeResidence = Residence(address: "123 Main St")
let livingRoom = Room(name: "Living Room", area: 200.0)
let bedroom = Room(name: "Bedroom", area: 150.0)

janeResidence.addRoom(livingRoom)
janeResidence.addRoom(bedroom)
jane.residence = janeResidence

// Optional chaining examples
print("John's residence address: \(john.residence?.address ?? "No residence")")
print("Jane's residence address: \(jane.residence?.address ?? "No residence")")

print("John's number of rooms: \(john.residence?.numberOfRooms ?? 0)")
print("Jane's number of rooms: \(jane.residence?.numberOfRooms ?? 0)")

// Accessing rooms through optional chaining
if let firstRoom = john.residence?[0] {
    print("John's first room: \(firstRoom.description)")
} else {
    print("John has no rooms")
}

if let firstRoom = jane.residence?[0] {
    print("Jane's first room: \(firstRoom.description)")
} else {
    print("Jane has no rooms")
}

// Calling methods through optional chaining
john.residence?.addRoom(Room(name: "Kitchen", area: 100.0)) // Does nothing since residence is nil
jane.residence?.addRoom(Room(name: "Kitchen", area: 100.0)) // Adds room to Jane's residence

print("Jane's updated number of rooms: \(jane.residence?.numberOfRooms ?? 0)")
```

**Optional chaining characteristics:**
- **Safe access** - Prevents crashes when accessing properties on `nil` values
- **Automatic unwrapping** - Handles optional values automatically
- **Chained calls** - Can chain multiple optional calls together
- **Nil coalescing** - Often used with `??` to provide default values
- **Method calls** - Can call methods on optional values safely

#### Optional Chaining with Properties

Optional chaining works with both stored and computed properties.

```swift
struct Address {
    let street: String
    let city: String
    let state: String
    let zipCode: String
    
    var fullAddress: String {
        return "\(street), \(city), \(state) \(zipCode)"
    }
}

struct ContactInfo {
    let email: String?
    let phone: String?
    let address: Address?
    
    var hasContactInfo: Bool {
        return email != nil || phone != nil || address != nil
    }
}

class Employee {
    let id: String
    let name: String
    var contactInfo: ContactInfo?
    var manager: Employee?
    var subordinates: [Employee] = []
    
    init(id: String, name: String) {
        self.id = id
        self.name = name
    }
    
    var hasSubordinates: Bool {
        return !subordinates.isEmpty
    }
    
    func addSubordinate(_ employee: Employee) {
        subordinates.append(employee)
        employee.manager = self
    }
}

// Usage
let ceo = Employee(id: "001", name: "John CEO")
let manager = Employee(id: "002", name: "Jane Manager")
let worker = Employee(id: "003", name: "Bob Worker")

// Set up hierarchy
ceo.addSubordinate(manager)
manager.addSubordinate(worker)

// Add contact info
let ceoAddress = Address(street: "123 Executive Blvd", city: "Business City", state: "CA", zipCode: "90210")
let ceoContact = ContactInfo(email: "ceo@company.com", phone: "555-0100", address: ceoAddress)
ceo.contactInfo = ceoContact

let managerContact = ContactInfo(email: "manager@company.com", phone: nil, address: nil)
manager.contactInfo = managerContact

// Optional chaining with properties
print("CEO email: \(ceo.contactInfo?.email ?? "No email")")
print("CEO phone: \(ceo.contactInfo?.phone ?? "No phone")")
print("CEO address: \(ceo.contactInfo?.address?.fullAddress ?? "No address")")

print("Manager email: \(manager.contactInfo?.email ?? "No email")")
print("Manager phone: \(manager.contactInfo?.phone ?? "No phone")")
print("Manager address: \(manager.contactInfo?.address?.fullAddress ?? "No address")")

print("Worker email: \(worker.contactInfo?.email ?? "No email")")
print("Worker phone: \(worker.contactInfo?.phone ?? "No phone")")
print("Worker address: \(worker.contactInfo?.address?.fullAddress ?? "No address")")

// Check if employees have contact info
print("CEO has contact info: \(ceo.contactInfo?.hasContactInfo ?? false)")
print("Manager has contact info: \(manager.contactInfo?.hasContactInfo ?? false)")
print("Worker has contact info: \(worker.contactInfo?.hasContactInfo ?? false)")

// Access through manager chain
print("Worker's manager: \(worker.manager?.name ?? "No manager")")
print("Worker's manager's manager: \(worker.manager?.manager?.name ?? "No manager's manager")")
print("Worker's manager's manager's email: \(worker.manager?.manager?.contactInfo?.email ?? "No email")")
```

#### Optional Chaining with Methods

Optional chaining works with method calls, allowing you to call methods on optional values safely.

```swift
class BankAccount {
    let accountNumber: String
    let accountHolder: String
    var balance: Decimal
    var isActive: Bool
    
    init(accountNumber: String, accountHolder: String, initialBalance: Decimal = 0) {
        self.accountNumber = accountNumber
        self.accountHolder = accountHolder
        self.balance = initialBalance
        self.isActive = true
    }
    
    func deposit(amount: Decimal) -> Bool {
        guard amount > 0 else { return false }
        balance += amount
        print("Deposited $\(amount). New balance: $\(balance)")
        return true
    }
    
    func withdraw(amount: Decimal) -> Bool {
        guard amount > 0 && amount <= balance else { return false }
        balance -= amount
        print("Withdrew $\(amount). New balance: $\(balance)")
        return true
    }
    
    func transfer(amount: Decimal, to recipient: BankAccount) -> Bool {
        guard withdraw(amount: amount) else { return false }
        recipient.deposit(amount: amount)
        print("Transferred $\(amount) to \(recipient.accountHolder)")
        return true
    }
    
    var description: String {
        return "\(accountHolder) - \(accountNumber) - $\(balance)"
    }
}

class Bank {
    let name: String
    var accounts: [BankAccount] = []
    
    init(name: String) {
        self.name = name
    }
    
    func addAccount(_ account: BankAccount) {
        accounts.append(account)
        print("Added account to \(name)")
    }
    
    func findAccount(by accountNumber: String) -> BankAccount? {
        return accounts.first { $0.accountNumber == accountNumber }
    }
    
    func processTransfer(from fromAccountNumber: String, to toAccountNumber: String, amount: Decimal) -> Bool {
        guard let fromAccount = findAccount(by: fromAccountNumber),
              let toAccount = findAccount(by: toAccountNumber) else {
            print("One or both accounts not found")
            return false
        }
        
        return fromAccount.transfer(amount: amount, to: toAccount)
    }
}

// Usage
let bank = Bank(name: "Swift Bank")

let account1 = BankAccount(accountNumber: "001", accountHolder: "John Doe", initialBalance: 1000)
let account2 = BankAccount(accountNumber: "002", accountHolder: "Jane Smith", initialBalance: 500)

bank.addAccount(account1)
bank.addAccount(account2)

// Optional chaining with method calls
let foundAccount = bank.findAccount(by: "001")
foundAccount?.deposit(amount: 200)
foundAccount?.withdraw(amount: 100)

// Chained optional method calls
bank.findAccount(by: "001")?.transfer(amount: 150, to: account2)

// Check if transfer was successful
if bank.findAccount(by: "001")?.transfer(amount: 50, to: account2) == true {
    print("Transfer successful")
} else {
    print("Transfer failed")
}

// Display final balances
print("Account 1: \(account1.description)")
print("Account 2: \(account2.description)")
```

### Real-World Optional Chaining Examples

Let's see how optional chaining is used in practical applications.

**Example 1: User Profile Management System**

```swift
struct UserProfile {
    let id: UUID
    let username: String
    let email: String
    var firstName: String?
    var lastName: String?
    var dateOfBirth: Date?
    var profilePicture: String?
    
    var fullName: String? {
        guard let firstName = firstName, let lastName = lastName else { return nil }
        return "\(firstName) \(lastName)"
    }
    
    var age: Int? {
        guard let dateOfBirth = dateOfBirth else { return nil }
        let calendar = Calendar.current
        let ageComponents = calendar.dateComponents([.year], from: dateOfBirth, to: Date())
        return ageComponents.year
    }
    
    var displayName: String {
        return fullName ?? username
    }
}

struct UserPreferences {
    let userId: UUID
    var theme: String
    var language: String
    var notificationsEnabled: Bool
    var privacyLevel: PrivacyLevel
    
    enum PrivacyLevel: String, CaseIterable {
        case public = "Public"
        case friends = "Friends Only"
        case private = "Private"
    }
}

struct UserSettings {
    let userId: UUID
    var preferences: UserPreferences?
    var lastLoginDate: Date?
    var isActive: Bool
    
    init(userId: UUID) {
        self.userId = userId
        self.isActive = true
    }
}

class UserManager {
    private var users: [UUID: UserProfile] = [:]
    private var userSettings: [UUID: UserSettings] = [:]
    
    func createUser(username: String, email: String) -> UserProfile {
        let userId = UUID()
        let profile = UserProfile(
            id: userId,
            username: username,
            email: email
        )
        
        users[userId] = profile
        userSettings[userId] = UserSettings(userId: userId)
        
        print("Created user: \(username)")
        return profile
    }
    
    func getUser(by id: UUID) -> UserProfile? {
        return users[id]
    }
    
    func getUserSettings(by id: UUID) -> UserSettings? {
        return userSettings[id]
    }
    
    func updateUserProfile(_ id: UUID, firstName: String?, lastName: String?, dateOfBirth: Date?) {
        guard var profile = users[id] else { return }
        
        profile.firstName = firstName
        profile.lastName = lastName
        profile.dateOfBirth = dateOfBirth
        
        users[id] = profile
        print("Updated profile for user: \(id)")
    }
    
    func updateUserPreferences(_ id: UUID, theme: String, language: String, privacyLevel: UserPreferences.PrivacyLevel) {
        guard var settings = userSettings[id] else { return }
        
        let preferences = UserPreferences(
            userId: id,
            theme: theme,
            language: language,
            notificationsEnabled: true,
            privacyLevel: privacyLevel
        )
        
        settings.preferences = preferences
        userSettings[id] = settings
        print("Updated preferences for user: \(id)")
    }
    
    func getUserDisplayInfo(_ id: UUID) -> String {
        guard let profile = users[id] else { return "User not found" }
        
        var info = "User: \(profile.displayName)"
        
        if let age = profile.age {
            info += " (Age: \(age))"
        }
        
        if let theme = userSettings[id]?.preferences?.theme {
            info += " - Theme: \(theme)"
        }
        
        if let privacy = userSettings[id]?.preferences?.privacyLevel.rawValue {
            info += " - Privacy: \(privacy)"
        }
        
        return info
    }
    
    func isUserActive(_ id: UUID) -> Bool {
        return userSettings[id]?.isActive ?? false
    }
    
    func getUsersByPrivacyLevel(_ privacyLevel: UserPreferences.PrivacyLevel) -> [UserProfile] {
        return users.values.filter { user in
            userSettings[user.id]?.preferences?.privacyLevel == privacyLevel
        }
    }
}

// Usage
let userManager = UserManager()

// Create users
let user1 = userManager.createUser(username: "john_doe", email: "john@example.com")
let user2 = userManager.createUser(username: "jane_smith", email: "jane@example.com")

// Update profiles
userManager.updateUserProfile(user1.id, firstName: "John", lastName: "Doe", dateOfBirth: Calendar.current.date(from: DateComponents(year: 1990, month: 5, day: 15)))
userManager.updateUserProfile(user2.id, firstName: "Jane", lastName: "Smith", dateOfBirth: Calendar.current.date(from: DateComponents(year: 1985, month: 8, day: 22)))

// Update preferences
userManager.updateUserPreferences(user1.id, theme: "Dark", language: "English", privacyLevel: .public)
userManager.updateUserPreferences(user2.id, theme: "Light", language: "Spanish", privacyLevel: .friends)

// Display user information using optional chaining
print("User 1: \(userManager.getUserDisplayInfo(user1.id))")
print("User 2: \(userManager.getUserDisplayInfo(user2.id))")

// Check user status
print("User 1 active: \(userManager.isUserActive(user1.id))")
print("User 2 active: \(userManager.isUserActive(user2.id))")

// Get users by privacy level
let publicUsers = userManager.getUsersByPrivacyLevel(.public)
let friendsOnlyUsers = userManager.getUsersByPrivacyLevel(.friends)

print("Public users: \(publicUsers.count)")
print("Friends only users: \(friendsOnlyUsers.count)")

// Test with non-existent user
let nonExistentUser = UUID()
print("Non-existent user active: \(userManager.isUserActive(nonExistentUser))")
print("Non-existent user info: \(userManager.getUserDisplayInfo(nonExistentUser))")
```

**Example 2: E-commerce Product System with Optional Chaining**

```swift
struct Product {
    let id: UUID
    let name: String
    let description: String
    let basePrice: Decimal
    let category: ProductCategory
    var stockQuantity: Int
    var isActive: Bool
    var discountPercentage: Decimal
    
    var finalPrice: Decimal {
        let discount = basePrice * (discountPercentage / 100)
        return basePrice - discount
    }
    
    var isOnSale: Bool {
        return discountPercentage > 0
    }
    
    var isInStock: Bool {
        return stockQuantity > 0
    }
}

enum ProductCategory: String, CaseIterable {
    case electronics = "Electronics"
    case clothing = "Clothing"
    case books = "Books"
    case home = "Home & Garden"
    
    var taxRate: Decimal {
        switch self {
        case .electronics: return 0.08
        case .clothing: return 0.06
        case .books: return 0.04
        case .home: return 0.07
        }
    }
}

struct ProductReview {
    let id: UUID
    let productId: UUID
    let userId: UUID
    let rating: Int
    let comment: String?
    let date: Date
    
    var isPositive: Bool {
        return rating >= 4
    }
}

struct ProductInventory {
    let productId: UUID
    var currentStock: Int
    var reorderPoint: Int
    var supplier: String?
    var lastRestocked: Date?
    
    var needsRestocking: Bool {
        return currentStock <= reorderPoint
    }
    
    var daysSinceLastRestock: Int? {
        guard let lastRestocked = lastRestocked else { return nil }
        let calendar = Calendar.current
        let components = calendar.dateComponents([.day], from: lastRestocked, to: Date())
        return components.day
    }
}

class ProductCatalog {
    private var products: [UUID: Product] = [:]
    private var reviews: [UUID: [ProductReview]] = [:]
    private var inventory: [UUID: ProductInventory] = [:]
    
    func addProduct(_ product: Product) {
        products[product.id] = product
        
        // Initialize inventory
        let productInventory = ProductInventory(
            productId: product.id,
            currentStock: product.stockQuantity,
            reorderPoint: max(1, product.stockQuantity / 4)
        )
        inventory[product.id] = productInventory
        
        print("Added product: \(product.name)")
    }
    
    func getProduct(by id: UUID) -> Product? {
        return products[id]
    }
    
    func addReview(_ review: ProductReview) {
        if reviews[review.productId] == nil {
            reviews[review.productId] = []
        }
        reviews[review.productId]?.append(review)
        print("Added review for product: \(review.productId)")
    }
    
    func getProductReviews(for productId: UUID) -> [ProductReview]? {
        return reviews[productId]
    }
    
    func updateInventory(_ productId: UUID, newStock: Int, supplier: String? = nil) {
        guard var product = products[productId] else { return }
        guard var productInventory = inventory[productId] else { return }
        
        product.stockQuantity = newStock
        productInventory.currentStock = newStock
        
        if let supplier = supplier {
            productInventory.supplier = supplier
        }
        
        productInventory.lastRestocked = Date()
        
        products[productId] = product
        inventory[productId] = productInventory
        
        print("Updated inventory for \(product.name): \(newStock) units")
    }
    
    func getProductInfo(_ productId: UUID) -> String {
        guard let product = products[productId] else { return "Product not found" }
        
        var info = "\(product.name) - $\(product.finalPrice)"
        
        if product.isOnSale {
            info += " (\(product.discountPercentage)% off)"
        }
        
        if let productInventory = inventory[productId] {
            info += " - Stock: \(productInventory.currentStock)"
            
            if productInventory.needsRestocking {
                info += " (Needs restocking)"
            }
            
            if let daysSinceRestock = productInventory.daysSinceLastRestock {
                info += " - Last restocked: \(daysSinceRestock) days ago"
            }
        }
        
        if let productReviews = reviews[productId] {
            let averageRating = Double(productReviews.reduce(0) { $0 + $1.rating }) / Double(productReviews.count)
            info += " - Rating: \(String(format: "%.1f", averageRating))/5"
            
            let positiveReviews = productReviews.filter { $0.isPositive }.count
            info += " (\(positiveReviews) positive reviews)"
        }
        
        return info
    }
    
    func getProductsNeedingRestock() -> [Product] {
        return products.values.filter { product in
            inventory[product.id]?.needsRestocking == true
        }
    }
    
    func getTopRatedProducts(minRating: Double = 4.0) -> [Product] {
        return products.values.filter { product in
            guard let productReviews = reviews[product.id] else { return false }
            let averageRating = Double(productReviews.reduce(0) { $0 + $1.rating }) / Double(productReviews.count)
            return averageRating >= minRating
        }
    }
}

// Usage
let catalog = ProductCatalog()

// Create products
let product1 = Product(
    id: UUID(),
    name: "iPhone 15 Pro",
    description: "Latest iPhone with advanced features",
    basePrice: 999.99,
    category: .electronics,
    stockQuantity: 50,
    isActive: true,
    discountPercentage: 0
)

let product2 = Product(
    id: UUID(),
    name: "Wireless Headphones",
    description: "Noise-cancelling wireless headphones",
    basePrice: 299.99,
    category: .electronics,
    stockQuantity: 3,
    isActive: true,
    discountPercentage: 15
)

let product3 = Product(
    id: UUID(),
    name: "Programming Book",
    description: "Learn Swift programming",
    basePrice: 49.99,
    category: .books,
    stockQuantity: 25,
    isActive: true,
    discountPercentage: 0
)

// Add products to catalog
catalog.addProduct(product1)
catalog.addProduct(product2)
catalog.addProduct(product3)

// Add reviews
let review1 = ProductReview(
    id: UUID(),
    productId: product1.id,
    userId: UUID(),
    rating: 5,
    comment: "Excellent phone!",
    date: Date()
)

let review2 = ProductReview(
    id: UUID(),
    productId: product1.id,
    userId: UUID(),
    rating: 4,
    comment: "Great features, but expensive",
    date: Date()
)

catalog.addReview(review1)
catalog.addReview(review2)

// Update inventory
catalog.updateInventory(product2.id, newStock: 1, supplier: "AudioCorp")

// Display product information using optional chaining
print("Product 1: \(catalog.getProductInfo(product1.id))")
print("Product 2: \(catalog.getProductInfo(product2.id))")
print("Product 3: \(catalog.getProductInfo(product3.id))")

// Get products needing restock
let productsNeedingRestock = catalog.getProductsNeedingRestock()
print("Products needing restock: \(productsNeedingRestock.count)")

// Get top rated products
let topRatedProducts = catalog.getTopRatedProducts(minRating: 4.5)
print("Top rated products: \(topRatedProducts.count)")
```

### Advanced Optional Chaining Features

#### Optional Chaining with Subscripts

Optional chaining works with subscripts, allowing safe access to collection elements.

```swift
class DataManager {
    private var data: [String: [String: Any]] = [:]
    
    func setValue(_ value: Any, forKey key: String, inSection section: String) {
        if data[section] == nil {
            data[section] = [:]
        }
        data[section]?[key] = value
    }
    
    func getValue(forKey key: String, inSection section: String) -> Any? {
        return data[section]?[key]
    }
    
    func getSection(_ section: String) -> [String: Any]? {
        return data[section]
    }
    
    subscript(section: String) -> [String: Any]? {
        return data[section]
    }
    
    subscript(section: String, key: String) -> Any? {
        return data[section]?[key]
    }
}

// Usage
let dataManager = DataManager()

// Set some data
dataManager.setValue("John Doe", forKey: "name", inSection: "user")
dataManager.setValue(30, forKey: "age", inSection: "user")
dataManager.setValue("john@example.com", forKey: "email", inSection: "user")

dataManager.setValue("Dark", forKey: "theme", inSection: "preferences")
dataManager.setValue("English", forKey: "language", inSection: "preferences")

// Access data using optional chaining with subscripts
let userName = dataManager["user"]?["name"] as? String
let userAge = dataManager["user"]?["age"] as? Int
let userEmail = dataManager["user"]?["email"] as? String

let theme = dataManager["preferences"]?["theme"] as? String
let language = dataManager["preferences"]?["language"] as? String

// Non-existent data
let nonExistentSection = dataManager["nonexistent"]?["key"]
let nonExistentKey = dataManager["user"]?["nonexistent"]

print("User name: \(userName ?? "Unknown")")
print("User age: \(userAge ?? 0)")
print("User email: \(userEmail ?? "No email")")
print("Theme: \(theme ?? "Default")")
print("Language: \(language ?? "English")")
print("Non-existent section: \(nonExistentSection ?? "nil")")
print("Non-existent key: \(nonExistentKey ?? "nil")")

// Using subscript syntax
let userName2 = dataManager["user", "name"] as? String
let userAge2 = dataManager["user", "age"] as? Int

print("User name (subscript): \(userName2 ?? "Unknown")")
print("User age (subscript): \(userAge2 ?? 0)")
```

#### Optional Chaining with Type Casting

Optional chaining can be combined with type casting for safe type conversion.

```swift
class MediaItem {
    let id: UUID
    let title: String
    let type: MediaType
    
    init(id: UUID, title: String, type: MediaType) {
        self.id = id
        self.title = title
        self.type = type
    }
    
    enum MediaType: String {
        case movie = "Movie"
        case tvShow = "TV Show"
        case book = "Book"
        case music = "Music"
    }
}

class Movie: MediaItem {
    let director: String
    let runtime: Int
    let genre: String
    
    init(id: UUID, title: String, director: String, runtime: Int, genre: String) {
        self.director = director
        self.runtime = runtime
        self.genre = genre
        super.init(id: id, title: title, type: .movie)
    }
}

class TVShow: MediaItem {
    let seasons: Int
    let episodes: Int
    let network: String
    
    init(id: UUID, title: String, seasons: Int, episodes: Int, network: String) {
        self.seasons = seasons
        self.episodes = episodes
        self.network = network
        super.init(id: id, title: title, type: .tvShow)
    }
}

class MediaLibrary {
    private var items: [UUID: MediaItem] = [:]
    
    func addItem(_ item: MediaItem) {
        items[item.id] = item
        print("Added \(item.type.rawValue): \(item.title)")
    }
    
    func getItem(by id: UUID) -> MediaItem? {
        return items[id]
    }
    
    func getMovie(by id: UUID) -> Movie? {
        return items[id] as? Movie
    }
    
    func getTVShow(by id: UUID) -> TVShow? {
        return items[id] as? TVShow
    }
    
    func getMovieInfo(_ id: UUID) -> String? {
        guard let movie = items[id] as? Movie else { return nil }
        return "\(movie.title) by \(movie.director) (\(movie.runtime) min, \(movie.genre))"
    }
    
    func getTVShowInfo(_ id: UUID) -> String? {
        guard let tvShow = items[id] as? TVShow else { return nil }
        return "\(tvShow.title) (\(tvShow.seasons) seasons, \(tvShow.episodes) episodes on \(tvShow.network))"
    }
}

// Usage
let library = MediaLibrary()

// Create media items
let movie1 = Movie(id: UUID(), title: "The Matrix", director: "Wachowskis", runtime: 136, genre: "Sci-Fi")
let tvShow1 = TVShow(id: UUID(), title: "Breaking Bad", seasons: 5, episodes: 62, network: "AMC")
let movie2 = Movie(id: UUID(), title: "Inception", director: "Christopher Nolan", runtime: 148, genre: "Thriller")

// Add to library
library.addItem(movie1)
library.addItem(tvShow1)
library.addItem(movie2)

// Access using optional chaining with type casting
let movie1Info = library.getMovieInfo(movie1.id)
let tvShow1Info = library.getTVShowInfo(tvShow1.id)
let movie2Info = library.getMovieInfo(movie2.id)

// Try to get movie info for TV show (should return nil)
let tvShowAsMovie = library.getMovieInfo(tvShow1.id)

print("Movie 1: \(movie1Info ?? "Not found")")
print("TV Show 1: \(tvShow1Info ?? "Not found")")
print("Movie 2: \(movie2Info ?? "Not found")")
print("TV Show as Movie: \(tvShowAsMovie ?? "Cannot get movie info for TV show")")

// Safe type casting with optional chaining
if let movie = library.getItem(by: movie1.id) as? Movie {
    print("Found movie: \(movie.title) by \(movie.director)")
}

if let tvShow = library.getItem(by: tvShow1.id) as? TVShow {
    print("Found TV show: \(tvShow.title) on \(tvShow.network)")
}

// Try to cast movie as TV show (should fail)
if let tvShow = library.getItem(by: movie1.id) as? TVShow {
    print("Movie cast as TV show: \(tvShow.title)")
} else {
    print("Cannot cast movie as TV show")
}
```

### Best Practices for Optional Chaining

1. **Use for safe access** - Use optional chaining to safely access optional properties
2. **Combine with nil coalescing** - Use `??` to provide default values
3. **Chain multiple calls** - Chain multiple optional calls together safely
4. **Avoid force unwrapping** - Use optional chaining instead of force unwrapping
5. **Consider performance** - Optional chaining has minimal performance overhead
6. **Use with type casting** - Combine with `as?` for safe type conversion
7. **Handle nil cases** - Always consider what happens when the value is `nil`
8. **Test edge cases** - Ensure your code handles all possible nil scenarios

**Optional chaining design principles:**

- **Safety first** - Prevent crashes from accessing nil values
- **Graceful degradation** - Handle missing data gracefully
- **Clear intent** - Make it clear when you're dealing with optional values
- **Consistent patterns** - Use consistent optional chaining patterns throughout your code
- **Error handling** - Use optional chaining as part of your error handling strategy

Optional chaining is a powerful feature that makes Swift code safer and more readable. Understanding how to use optional chaining effectively will help you write robust, crash-resistant code that gracefully handles missing or nil values.

---

## Error Handling

Error handling is a crucial aspect of Swift programming that allows you to respond to and recover from errors that occur during program execution. Swift provides a comprehensive error handling system using throwing functions, do-catch statements, and the Result type. Understanding error handling is essential for building robust, production-ready applications.

### Understanding Error Handling

Error handling in Swift is designed to be safe, expressive, and easy to use. It allows you to define, throw, catch, and handle errors in a structured way.

#### Basic Error Types

Errors in Swift conform to the `Error` protocol, which can be implemented by any type.

```swift
// Simple error enum
enum NetworkError: Error {
    case noConnection
    case timeout
    case invalidResponse
    case serverError(Int)
}

// Error with associated values
enum ValidationError: Error {
    case emptyField(String)
    case invalidFormat(String, String) // field name, expected format
    case outOfRange(String, min: Int, max: Int)
    case custom(String)
}

// Error with custom localized description
enum FileError: Error, LocalizedError {
    case fileNotFound(String)
    case permissionDenied(String)
    case diskFull
    case corruptedFile(String)
    
    var errorDescription: String? {
        switch self {
        case .fileNotFound(let filename):
            return "File '\(filename)' not found"
        case .permissionDenied(let filename):
            return "Permission denied for file '\(filename)'"
        case .diskFull:
            return "Disk is full"
        case .corruptedFile(let filename):
            return "File '\(filename)' is corrupted"
        }
    }
    
    var failureReason: String? {
        switch self {
        case .fileNotFound:
            return "The specified file does not exist"
        case .permissionDenied:
            return "Insufficient permissions to access the file"
        case .diskFull:
            return "No available disk space"
        case .corruptedFile:
            return "File data is corrupted or invalid"
        }
    }
    
    var recoverySuggestion: String? {
        switch self {
        case .fileNotFound:
            return "Check the file path and try again"
        case .permissionDenied:
            return "Request appropriate permissions or use a different location"
        case .diskFull:
            return "Free up disk space and try again"
        case .corruptedFile:
            return "Try restoring from backup or re-downloading the file"
        }
    }
}

// Error with custom debug description
enum DatabaseError: Error, CustomDebugStringConvertible {
    case connectionFailed(String)
    case queryFailed(String)
    case transactionFailed(String)
    
    var debugDescription: String {
        switch self {
        case .connectionFailed(let details):
            return "Database connection failed: \(details)"
        case .queryFailed(let details):
            return "Database query failed: \(details)"
        case .transactionFailed(let details):
            return "Database transaction failed: \(details)"
        }
    }
}

// Usage
let networkError = NetworkError.serverError(500)
let validationError = ValidationError.outOfRange("age", min: 0, max: 120)
let fileError = FileError.fileNotFound("document.txt")
let databaseError = DatabaseError.connectionFailed("Invalid credentials")

print("Network error: \(networkError)")
print("Validation error: \(validationError)")
print("File error: \(fileError)")
print("Database error: \(databaseError)")

// Access error descriptions
if let description = fileError.errorDescription {
    print("Error description: \(description)")
}

if let reason = fileError.failureReason {
    print("Failure reason: \(reason)")
}

if let suggestion = fileError.recoverySuggestion {
    print("Recovery suggestion: \(suggestion)")
}

print("Debug description: \(databaseError.debugDescription)")
```

**Error characteristics:**
- **Protocol conformance** - Errors conform to the `Error` protocol
- **Associated values** - Can carry additional information about the error
- **Localized descriptions** - Can provide user-friendly error messages
- **Debug information** - Can include detailed debugging information
- **Recovery suggestions** - Can suggest how to fix the error

#### Throwing Functions

Functions that can throw errors are marked with the `throws` keyword.

```swift
class DataValidator {
    static func validateEmail(_ email: String) throws -> String {
        guard !email.isEmpty else {
            throw ValidationError.emptyField("email")
        }
        
        let emailRegex = #"^[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$"#
        guard email.range(of: emailRegex, options: .regularExpression) != nil else {
            throw ValidationError.invalidFormat("email", "valid email address")
        }
        
        return email.lowercased()
    }
    
    static func validateAge(_ age: Int) throws -> Int {
        guard age >= 0 else {
            throw ValidationError.outOfRange("age", min: 0, max: 120)
        }
        
        guard age <= 120 else {
            throw ValidationError.outOfRange("age", min: 0, max: 120)
        }
        
        return age
    }
    
    static func validatePassword(_ password: String) throws -> String {
        guard !password.isEmpty else {
            throw ValidationError.emptyField("password")
        }
        
        guard password.count >= 8 else {
            throw ValidationError.invalidFormat("password", "at least 8 characters")
        }
        
        guard password.range(of: #"[A-Z]"#, options: .regularExpression) != nil else {
            throw ValidationError.invalidFormat("password", "at least one uppercase letter")
        }
        
        guard password.range(of: #"[0-9]"#, options: .regularExpression) != nil else {
            throw ValidationError.invalidFormat("password", "at least one number")
        }
        
        return password
    }
    
    static func validateUserData(email: String, age: Int, password: String) throws -> (email: String, age: Int, password: String) {
        let validatedEmail = try validateEmail(email)
        let validatedAge = try validateAge(age)
        let validatedPassword = try validatePassword(password)
        
        return (validatedEmail, validatedAge, validatedPassword)
    }
}

class FileManager {
    static func readFile(at path: String) throws -> String {
        // Simulate file reading
        guard !path.isEmpty else {
            throw FileError.fileNotFound("Empty path")
        }
        
        guard path.hasSuffix(".txt") else {
            throw FileError.invalidFormat("File must have .txt extension")
        }
        
        // Simulate file content
        return "File content from \(path)"
    }
    
    static func writeFile(_ content: String, to path: String) throws {
        // Simulate file writing
        guard !path.isEmpty else {
            throw FileError.fileNotFound("Empty path")
        }
        
        guard content.count > 0 else {
            throw FileError.custom("Cannot write empty content")
        }
        
        print("Writing content to \(path): \(content)")
    }
    
    static func deleteFile(at path: String) throws {
        // Simulate file deletion
        guard !path.isEmpty else {
            throw FileError.fileNotFound("Empty path")
        }
        
        print("Deleting file at \(path)")
    }
}

// Usage
do {
    let email = try DataValidator.validateEmail("user@example.com")
    print("Valid email: \(email)")
} catch {
    print("Email validation error: \(error)")
}

do {
    let age = try DataValidator.validateAge(25)
    print("Valid age: \(age)")
} catch {
    print("Age validation error: \(error)")
}

do {
    let password = try DataValidator.validatePassword("SecurePass123")
    print("Valid password: \(password)")
} catch {
    print("Password validation error: \(error)")
}

// Test validation errors
do {
    let invalidEmail = try DataValidator.validateEmail("invalid-email")
    print("Valid email: \(invalidEmail)")
} catch ValidationError.emptyField(let field) {
    print("Empty field: \(field)")
} catch ValidationError.invalidFormat(let field, let expected) {
    print("Invalid format for \(field): expected \(expected)")
} catch {
    print("Other validation error: \(error)")
}

do {
    let invalidAge = try DataValidator.validateAge(150)
    print("Valid age: \(invalidAge)")
} catch ValidationError.outOfRange(let field, let min, let max) {
    print("\(field) out of range: must be between \(min) and \(max)")
} catch {
    print("Other validation error: \(error)")
}
```

#### Do-Catch Statements

The `do-catch` statement allows you to handle errors thrown by throwing functions.

```swift
class UserRegistration {
    static func registerUser(email: String, age: Int, password: String) {
        do {
            let validatedData = try DataValidator.validateUserData(email: email, age: age, password: password)
            print("User registration successful!")
            print("Email: \(validatedData.email)")
            print("Age: \(validatedData.age)")
            print("Password: \(validatedData.password)")
        } catch ValidationError.emptyField(let field) {
            print("Registration failed: \(field) cannot be empty")
        } catch ValidationError.invalidFormat(let field, let expected) {
            print("Registration failed: \(field) must be \(expected)")
        } catch ValidationError.outOfRange(let field, let min, let max) {
            print("Registration failed: \(field) must be between \(min) and \(max)")
        } catch {
            print("Registration failed with unexpected error: \(error)")
        }
    }
    
    static func registerUserWithRecovery(email: String, age: Int, password: String) {
        do {
            let validatedData = try DataValidator.validateUserData(email: email, age: age, password: password)
            print("User registration successful!")
            print("Email: \(validatedData.email)")
            print("Age: \(validatedData.age)")
            print("Password: \(validatedData.password)")
        } catch ValidationError.emptyField(let field) {
            print("Registration failed: \(field) cannot be empty")
            print("Please provide a value for \(field)")
        } catch ValidationError.invalidFormat(let field, let expected) {
            print("Registration failed: \(field) must be \(expected)")
            print("Please correct the format of \(field)")
        } catch ValidationError.outOfRange(let field, let min, let max) {
            print("Registration failed: \(field) must be between \(min) and \(max)")
            print("Please provide a valid \(field) value")
        } catch {
            print("Registration failed with unexpected error: \(error)")
            print("Please contact support for assistance")
        }
    }
}

// Usage
print("=== Testing User Registration ===")

// Valid user
UserRegistration.registerUser(email: "john@example.com", age: 25, password: "SecurePass123")

print("\n---\n")

// Invalid email
UserRegistration.registerUser(email: "invalid-email", age: 25, password: "SecurePass123")

print("\n---\n")

// Invalid age
UserRegistration.registerUser(email: "jane@example.com", age: 150, password: "SecurePass123")

print("\n---\n")

// Invalid password
UserRegistration.registerUser(email: "bob@example.com", age: 30, password: "weak")

print("\n=== Testing User Registration with Recovery ===")

// Test with recovery suggestions
UserRegistration.registerUserWithRecovery(email: "", age: 25, password: "SecurePass123")
```

### Real-World Error Handling Examples

Let's see how error handling is used in practical applications.

**Example 1: Network Request System with Error Handling**

```swift
enum NetworkError: Error, LocalizedError {
    case noInternetConnection
    case invalidURL
    case timeout(TimeInterval)
    case serverError(Int, String)
    case invalidResponse
    case decodingError(String)
    
    var errorDescription: String? {
        switch self {
        case .noInternetConnection:
            return "No internet connection available"
        case .invalidURL:
            return "Invalid URL provided"
        case .timeout(let seconds):
            return "Request timed out after \(seconds) seconds"
        case .serverError(let code, let message):
            return "Server error \(code): \(message)"
        case .invalidResponse:
            return "Invalid response from server"
        case .decodingError(let details):
            return "Failed to decode response: \(details)"
        }
    }
    
    var recoverySuggestion: String? {
        switch self {
        case .noInternetConnection:
            return "Check your internet connection and try again"
        case .invalidURL:
            return "Verify the URL is correct and try again"
        case .timeout:
            return "Try again later or check your connection speed"
        case .serverError:
            return "Try again later or contact support if the problem persists"
        case .invalidResponse:
            return "Try again or contact support if the problem persists"
        case .decodingError:
            return "The response format may have changed. Please update the app."
        }
    }
}

struct User: Codable {
    let id: Int
    let name: String
    let email: String
    let username: String
}

struct Post: Codable {
    let id: Int
    let userId: Int
    let title: String
    let body: String
}

class NetworkManager {
    private let baseURL: String
    private let timeout: TimeInterval
    
    init(baseURL: String, timeout: TimeInterval = 30.0) {
        self.baseURL = baseURL
        self.timeout = timeout
    }
    
    func fetchUsers() async throws -> [User] {
        let urlString = "\(baseURL)/users"
        
        guard let url = URL(string: urlString) else {
            throw NetworkError.invalidURL
        }
        
        // Simulate network request
        try await Task.sleep(nanoseconds: UInt64(1_000_000_000)) // 1 second
        
        // Simulate random errors
        let random = Int.random(in: 1...10)
        if random == 1 {
            throw NetworkError.noInternetConnection
        } else if random == 2 {
            throw NetworkError.timeout(timeout)
        } else if random == 3 {
            throw NetworkError.serverError(500, "Internal Server Error")
        }
        
        // Simulate successful response
        let users = [
            User(id: 1, name: "John Doe", email: "john@example.com", username: "johndoe"),
            User(id: 2, name: "Jane Smith", email: "jane@example.com", username: "janesmith"),
            User(id: 3, name: "Bob Johnson", email: "bob@example.com", username: "bobjohnson")
        ]
        
        return users
    }
    
    func fetchPosts(for userId: Int) async throws -> [Post] {
        let urlString = "\(baseURL)/users/\(userId)/posts"
        
        guard let url = URL(string: urlString) else {
            throw NetworkError.invalidURL
        }
        
        // Simulate network request
        try await Task.sleep(nanoseconds: UInt64(500_000_000)) // 0.5 seconds
        
        // Simulate random errors
        let random = Int.random(in: 1...10)
        if random == 1 {
            throw NetworkError.serverError(404, "User not found")
        }
        
        // Simulate successful response
        let posts = [
            Post(id: 1, userId: userId, title: "First Post", body: "This is my first post"),
            Post(id: 2, userId: userId, title: "Second Post", body: "This is my second post")
        ]
        
        return posts
    }
    
    func createPost(userId: Int, title: String, body: String) async throws -> Post {
        let urlString = "\(baseURL)/posts"
        
        guard let url = URL(string: urlString) else {
            throw NetworkError.invalidURL
        }
        
        // Simulate network request
        try await Task.sleep(nanoseconds: UInt64(800_000_000)) // 0.8 seconds
        
        // Simulate validation error
        guard !title.isEmpty else {
            throw NetworkError.invalidResponse
        }
        
        guard !body.isEmpty else {
            throw NetworkError.invalidResponse
        }
        
        // Simulate successful response
        let post = Post(id: Int.random(in: 100...999), userId: userId, title: title, body: body)
        return post
    }
}

class UserService {
    private let networkManager: NetworkManager
    
    init(networkManager: NetworkManager) {
        self.networkManager = networkManager
    }
    
    func loadUserProfile(userId: Int) async {
        do {
            print("Loading user profile for user \(userId)...")
            
            // Fetch user posts
            let posts = try await networkManager.fetchPosts(for: userId)
            print("Loaded \(posts.count) posts for user \(userId)")
            
            for post in posts {
                print("- \(post.title)")
            }
            
        } catch NetworkError.serverError(let code, let message) {
            print("❌ Server error \(code): \(message)")
            print("💡 \(NetworkError.serverError(code, message).recoverySuggestion ?? "")")
        } catch NetworkError.timeout(let seconds) {
            print("⏰ Request timed out after \(seconds) seconds")
            print("💡 \(NetworkError.timeout(seconds).recoverySuggestion ?? "")")
        } catch NetworkError.noInternetConnection {
            print("🌐 No internet connection")
            print("💡 \(NetworkError.noInternetConnection.recoverySuggestion ?? "")")
        } catch {
            print("❌ Unexpected error: \(error)")
        }
    }
    
    func createNewPost(userId: Int, title: String, body: String) async {
        do {
            print("Creating new post for user \(userId)...")
            
            let post = try await networkManager.createPost(userId: userId, title: title, body: body)
            print("✅ Post created successfully!")
            print("   ID: \(post.id)")
            print("   Title: \(post.title)")
            print("   Body: \(post.body)")
            
        } catch NetworkError.invalidResponse {
            print("❌ Invalid post data")
            print("💡 Please ensure title and body are not empty")
        } catch NetworkError.serverError(let code, let message) {
            print("❌ Server error \(code): \(message)")
            print("💡 \(NetworkError.serverError(code, message).recoverySuggestion ?? "")")
        } catch {
            print("❌ Unexpected error: \(error)")
        }
    }
}

// Usage
let networkManager = NetworkManager(baseURL: "https://api.example.com")
let userService = UserService(networkManager: networkManager)

// Test user profile loading
Task {
    await userService.loadUserProfile(userId: 1)
}

// Test post creation
Task {
    await userService.createNewPost(userId: 1, title: "My New Post", body: "This is the content of my new post")
}

// Test post creation with invalid data
Task {
    await userService.createNewPost(userId: 1, title: "", body: "This post has no title")
}
```

**Example 2: File Processing System with Error Handling**

```swift
enum FileProcessingError: Error, LocalizedError {
    case fileNotFound(String)
    case permissionDenied(String)
    case invalidFormat(String)
    case processingFailed(String)
    case outputDirectoryNotFound(String)
    case diskFull
    case corruptedData(String)
    
    var errorDescription: String? {
        switch self {
        case .fileNotFound(let filename):
            return "File '\(filename)' not found"
        case .permissionDenied(let filename):
            return "Permission denied for file '\(filename)'"
        case .invalidFormat(let details):
            return "Invalid file format: \(details)"
        case .processingFailed(let reason):
            return "File processing failed: \(reason)"
        case .outputDirectoryNotFound(let path):
            return "Output directory not found: \(path)"
        case .diskFull:
            return "Disk is full"
        case .corruptedData(let details):
            return "File data is corrupted: \(details)"
        }
    }
    
    var recoverySuggestion: String? {
        switch self {
        case .fileNotFound:
            return "Check the file path and ensure the file exists"
        case .permissionDenied:
            return "Check file permissions or run with appropriate privileges"
        case .invalidFormat:
            return "Verify the file format and try again"
        case .processingFailed:
            return "Check the file content and try again"
        case .outputDirectoryNotFound:
            return "Create the output directory or specify a valid path"
        case .diskFull:
            return "Free up disk space and try again"
        case .corruptedData:
            return "Try restoring from backup or re-downloading the file"
        }
    }
}

struct FileInfo {
    let name: String
    let size: Int
    let type: String
    let lastModified: Date
}

class FileProcessor {
    private let inputDirectory: String
    private let outputDirectory: String
    
    init(inputDirectory: String, outputDirectory: String) throws {
        self.inputDirectory = inputDirectory
        self.outputDirectory = outputDirectory
        
        // Validate directories
        try validateDirectories()
    }
    
    private func validateDirectories() throws {
        // Simulate directory validation
        guard !inputDirectory.isEmpty else {
            throw FileProcessingError.fileNotFound("Input directory path is empty")
        }
        
        guard !outputDirectory.isEmpty else {
            throw FileProcessingError.outputDirectoryNotFound("Output directory path is empty")
        }
        
        print("✅ Directories validated successfully")
    }
    
    func processFile(_ filename: String) throws -> FileInfo {
        print("Processing file: \(filename)")
        
        // Simulate file processing
        try validateFile(filename)
        let fileInfo = try readFileInfo(filename)
        try processFileContent(filename)
        try writeOutputFile(filename)
        
        print("✅ File processed successfully: \(filename)")
        return fileInfo
    }
    
    private func validateFile(_ filename: String) throws {
        guard !filename.isEmpty else {
            throw FileProcessingError.fileNotFound("Filename is empty")
        }
        
        guard filename.hasSuffix(".txt") || filename.hasSuffix(".csv") else {
            throw FileProcessingError.invalidFormat("File must be .txt or .csv")
        }
        
        // Simulate file existence check
        let random = Int.random(in: 1...10)
        if random == 1 {
            throw FileProcessingError.fileNotFound(filename)
        } else if random == 2 {
            throw FileProcessingError.permissionDenied(filename)
        }
    }
    
    private func readFileInfo(_ filename: String) throws -> FileInfo {
        // Simulate reading file info
        let random = Int.random(in: 1...10)
        if random == 1 {
            throw FileProcessingError.corruptedData("Unable to read file header")
        }
        
        return FileInfo(
            name: filename,
            size: Int.random(in: 100...10000),
            type: filename.hasSuffix(".txt") ? "Text" : "CSV",
            lastModified: Date()
        )
    }
    
    private func processFileContent(_ filename: String) throws {
        // Simulate content processing
        let random = Int.random(in: 1...10)
        if random == 1 {
            throw FileProcessingError.processingFailed("Invalid content format")
        } else if random == 2 {
            throw FileProcessingError.corruptedData("Content contains invalid characters")
        }
        
        // Simulate processing time
        try Task.sleep(nanoseconds: UInt64(100_000_000)) // 0.1 seconds
    }
    
    private func writeOutputFile(_ filename: String) throws {
        // Simulate writing output file
        let random = Int.random(in: 1...10)
        if random == 1 {
            throw FileProcessingError.diskFull
        } else if random == 2 {
            throw FileProcessingError.outputDirectoryNotFound(outputDirectory)
        }
        
        print("📝 Writing output file: \(filename)")
    }
    
    func processMultipleFiles(_ filenames: [String]) -> [FileInfo] {
        var processedFiles: [FileInfo] = []
        var errors: [String: Error] = [:]
        
        for filename in filenames {
            do {
                let fileInfo = try processFile(filename)
                processedFiles.append(fileInfo)
            } catch {
                errors[filename] = error
                print("❌ Failed to process \(filename): \(error)")
                
                // Provide recovery suggestions
                if let fileError = error as? FileProcessingError,
                   let suggestion = fileError.recoverySuggestion {
                    print("💡 \(suggestion)")
                }
            }
        }
        
        // Summary
        print("\n📊 Processing Summary:")
        print("✅ Successfully processed: \(processedFiles.count) files")
        print("❌ Failed to process: \(errors.count) files")
        
        if !errors.isEmpty {
            print("\n❌ Errors encountered:")
            for (filename, error) in errors {
                print("   \(filename): \(error)")
            }
        }
        
        return processedFiles
    }
}

// Usage
do {
    let processor = try FileProcessor(inputDirectory: "/input", outputDirectory: "/output")
    
    let filenames = [
        "document1.txt",
        "data.csv",
        "report.txt",
        "invalid.pdf", // This will fail
        "empty.txt"    // This might fail
    ]
    
    let processedFiles = processor.processMultipleFiles(filenames)
    
    print("\n📁 Successfully processed files:")
    for fileInfo in processedFiles {
        print("   \(fileInfo.name) (\(fileInfo.type), \(fileInfo.size) bytes)")
    }
    
} catch {
    print("❌ Failed to initialize file processor: \(error)")
    
    if let fileError = error as? FileProcessingError,
       let suggestion = fileError.recoverySuggestion {
        print("💡 \(suggestion)")
    }
}
```

### Advanced Error Handling Features

#### Result Type

The `Result` type provides a way to handle success and failure cases without throwing errors.

```swift
enum ValidationResult {
    case success(String)
    case failure(ValidationError)
}

class AdvancedValidator {
    static func validateEmail(_ email: String) -> Result<String, ValidationError> {
        guard !email.isEmpty else {
            return .failure(.emptyField("email"))
        }
        
        let emailRegex = #"^[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$"#
        guard email.range(of: emailRegex, options: .regularExpression) != nil else {
            return .failure(.invalidFormat("email", "valid email address"))
        }
        
        return .success(email.lowercased())
    }
    
    static func validateAge(_ age: Int) -> Result<Int, ValidationError> {
        guard age >= 0 && age <= 120 else {
            return .failure(.outOfRange("age", min: 0, max: 120))
        }
        
        return .success(age)
    }
    
    static func validatePassword(_ password: String) -> Result<String, ValidationError> {
        guard !password.isEmpty else {
            return .failure(.emptyField("password"))
        }
        
        guard password.count >= 8 else {
            return .failure(.invalidFormat("password", "at least 8 characters"))
        }
        
        guard password.range(of: #"[A-Z]"#, options: .regularExpression) != nil else {
            return .failure(.invalidFormat("password", "at least one uppercase letter"))
        }
        
        guard password.range(of: #"[0-9]"#, options: .regularExpression) != nil else {
            return .failure(.invalidFormat("password", "at least one number"))
        }
        
        return .success(password)
    }
}

// Usage
let emailResult = AdvancedValidator.validateEmail("user@example.com")
let ageResult = AdvancedValidator.validateAge(25)
let passwordResult = AdvancedValidator.validatePassword("SecurePass123")

// Handle results
switch emailResult {
case .success(let email):
    print("Valid email: \(email)")
case .failure(let error):
    print("Email validation error: \(error)")
}

switch ageResult {
case .success(let age):
    print("Valid age: \(age)")
case .failure(let error):
    print("Age validation error: \(error)")
}

switch passwordResult {
case .success(let password):
    print("Valid password: \(password)")
case .failure(let error):
    print("Password validation error: \(error)")
}

// Using map and flatMap with Result
let combinedResult = AdvancedValidator.validateEmail("user@example.com")
    .flatMap { email in
        AdvancedValidator.validateAge(25).map { age in
            (email, age)
        }
    }

switch combinedResult {
case .success(let (email, age)):
    print("Both email and age are valid: \(email), \(age)")
case .failure(let error):
    print("Validation failed: \(error)")
}
```

#### Error Propagation

Understanding how errors propagate through function calls.

```swift
class DataService {
    func fetchUserData(userId: String) throws -> User {
        // Simulate network request
        try Task.sleep(nanoseconds: UInt64(500_000_000)) // 0.5 seconds
        
        // Simulate error
        let random = Int.random(in: 1...5)
        if random == 1 {
            throw NetworkError.serverError(500, "Internal Server Error")
        }
        
        return User(id: 1, name: "John Doe", email: "john@example.com", username: "johndoe")
    }
    
    func processUserData(_ user: User) throws -> String {
        // Simulate processing
        try Task.sleep(nanoseconds: UInt64(200_000_000)) // 0.2 seconds
        
        // Simulate error
        let random = Int.random(in: 1...5)
        if random == 1 {
            throw ValidationError.invalidFormat("user data", "valid user information")
        }
        
        return "Processed: \(user.name)"
    }
    
    func saveUserData(_ data: String) throws -> Bool {
        // Simulate saving
        try Task.sleep(nanoseconds: UInt64(300_000_000)) // 0.3 seconds
        
        // Simulate error
        let random = Int.random(in: 1...5)
        if random == 1 {
            throw FileError.diskFull
        }
        
        return true
    }
    
    func completeUserOperation(userId: String) throws -> String {
        // This function calls multiple throwing functions
        // Errors will propagate up the call stack
        
        let user = try fetchUserData(userId: userId)
        let processedData = try processUserData(user)
        let saved = try saveUserData(processedData)
        
        if saved {
            return "Operation completed successfully"
        } else {
            throw ValidationError.custom("Failed to save data")
        }
    }
}

// Usage
let dataService = DataService()

do {
    let result = try dataService.completeUserOperation(userId: "123")
    print("✅ \(result)")
} catch NetworkError.serverError(let code, let message) {
    print("❌ Network error: \(code) - \(message)")
} catch ValidationError.invalidFormat(let field, let expected) {
    print("❌ Validation error: \(field) must be \(expected)")
} catch FileError.diskFull {
    print("❌ Disk is full")
} catch {
    print("❌ Unexpected error: \(error)")
}
```

### Best Practices for Error Handling

1. **Define specific error types** - Create meaningful error enums with associated values
2. **Provide recovery suggestions** - Help users understand how to fix errors
3. **Use appropriate error handling** - Choose between throwing functions and Result types
4. **Handle errors at appropriate levels** - Don't let errors bubble up unnecessarily
5. **Provide user-friendly messages** - Use LocalizedError for better user experience
6. **Test error scenarios** - Ensure your error handling works correctly
7. **Document error conditions** - Clearly document when and why errors occur
8. **Consider error recovery** - Provide ways to recover from errors when possible

**Error handling design principles:**

- **Fail fast** - Detect and report errors as early as possible
- **Graceful degradation** - Handle errors without crashing the application
- **User experience** - Provide clear, actionable error messages
- **Debugging support** - Include sufficient information for debugging
- **Recovery mechanisms** - Suggest ways to fix or work around errors

Error handling is a critical aspect of building robust Swift applications. Understanding how to implement effective error handling will help you create applications that gracefully handle failures and provide a better user experience.

---

## Concurrency

Concurrency is a fundamental concept in modern programming that allows multiple tasks to execute simultaneously, improving performance and responsiveness. Swift provides a powerful concurrency system with async/await, actors, and structured concurrency. Understanding concurrency is essential for building responsive, scalable applications.

### Understanding Concurrency

Concurrency allows your program to perform multiple operations simultaneously, making better use of system resources and improving user experience.

#### Basic Async/Await Syntax

Async/await is Swift's modern approach to handling asynchronous operations.

```swift
// Basic async function
func fetchUserData(userId: Int) async throws -> User {
    // Simulate network delay
    try await Task.sleep(nanoseconds: UInt64(1_000_000_000)) // 1 second
    
    // Simulate fetching user data
    return User(
        id: userId,
        name: "User \(userId)",
        email: "user\(userId)@example.com",
        isActive: true
    )
}

// Async function that calls other async functions
func fetchUserProfile(userId: Int) async throws -> UserProfile {
    print("Fetching user profile for user \(userId)...")
    
    // Fetch user data
    let user = try await fetchUserData(userId: userId)
    
    // Fetch user posts
    let posts = try await fetchUserPosts(userId: userId)
    
    // Fetch user settings
    let settings = try await fetchUserSettings(userId: userId)
    
    return UserProfile(user: user, posts: posts, settings: settings)
}

// Helper functions
func fetchUserPosts(userId: Int) async throws -> [Post] {
    try await Task.sleep(nanoseconds: UInt64(500_000_000)) // 0.5 seconds
    
    return [
        Post(id: 1, title: "First Post", content: "Hello World"),
        Post(id: 2, title: "Second Post", content: "Swift is awesome")
    ]
}

func fetchUserSettings(userId: Int) async throws -> UserSettings {
    try await Task.sleep(nanoseconds: UInt64(300_000_000)) // 0.3 seconds
    
    return UserSettings(theme: "Dark", notifications: true)
}

// Data structures
struct User {
    let id: Int
    let name: String
    let email: String
    let isActive: Bool
}

struct Post {
    let id: Int
    let title: String
    let content: String
}

struct UserSettings {
    let theme: String
    let notifications: Bool
}

struct UserProfile {
    let user: User
    let posts: [Post]
    let settings: UserSettings
}

// Usage
Task {
    do {
        let profile = try await fetchUserProfile(userId: 123)
        print("✅ User profile loaded successfully!")
        print("User: \(profile.user.name)")
        print("Posts: \(profile.posts.count)")
        print("Theme: \(profile.settings.theme)")
    } catch {
        print("❌ Failed to load user profile: \(error)")
    }
}
```

**Async/await characteristics:**
- **Non-blocking** - Async functions don't block the calling thread
- **Structured** - Async calls are clearly marked and easy to follow
- **Error handling** - Can use try-catch with async functions
- **Composable** - Async functions can call other async functions
- **Performance** - Better than callback-based approaches

#### Concurrent Tasks

Swift allows you to run multiple async operations concurrently.

```swift
class DataFetcher {
    func fetchAllData(userId: Int) async throws -> AllUserData {
        print("Starting to fetch all data for user \(userId)...")
        
        // Fetch data concurrently
        async let user = fetchUser(userId: userId)
        async let posts = fetchPosts(userId: userId)
        async let settings = fetchSettings(userId: userId)
        async let friends = fetchFriends(userId: userId)
        
        // Wait for all data to complete
        let (userData, postsData, settingsData, friendsData) = try await (user, posts, settings, friends)
        
        print("All data fetched successfully!")
        
        return AllUserData(
            user: userData,
            posts: postsData,
            settings: settingsData,
            friends: friendsData
        )
    }
    
    func fetchUser(_ userId: Int) async throws -> User {
        print("Fetching user data...")
        try await Task.sleep(nanoseconds: UInt64(1_000_000_000)) // 1 second
        return User(id: userId, name: "User \(userId)", email: "user\(userId)@example.com", isActive: true)
    }
    
    func fetchPosts(_ userId: Int) async throws -> [Post] {
        print("Fetching user posts...")
        try await Task.sleep(nanoseconds: UInt64(800_000_000)) // 0.8 seconds
        return [
            Post(id: 1, title: "Post 1", content: "Content 1"),
            Post(id: 2, title: "Post 2", content: "Content 2")
        ]
    }
    
    func fetchSettings(_ userId: Int) async throws -> UserSettings {
        print("Fetching user settings...")
        try await Task.sleep(nanoseconds: UInt64(600_000_000)) // 0.6 seconds
        return UserSettings(theme: "Light", notifications: false)
    }
    
    func fetchFriends(_ userId: Int) async throws -> [User] {
        print("Fetching user friends...")
        try await Task.sleep(nanoseconds: UInt64(1_200_000_000)) // 1.2 seconds
        return [
            User(id: 101, name: "Friend 1", email: "friend1@example.com", isActive: true),
            User(id: 102, name: "Friend 2", email: "friend2@example.com", isActive: false)
        ]
    }
}

struct AllUserData {
    let user: User
    let posts: [Post]
    let settings: UserSettings
    let friends: [User]
}

// Usage
let fetcher = DataFetcher()

Task {
    do {
        let startTime = Date()
        let allData = try await fetcher.fetchAllData(userId: 123)
        let endTime = Date()
        
        let duration = endTime.timeIntervalSince(startTime)
        print("✅ Data fetched in \(String(format: "%.2f", duration)) seconds")
        print("User: \(allData.user.name)")
        print("Posts: \(allData.posts.count)")
        print("Friends: \(allData.friends.count)")
        
    } catch {
        print("❌ Failed to fetch data: \(error)")
    }
}
```

#### Task Groups

Task groups allow you to manage multiple concurrent tasks with more control.

```swift
class ImageProcessor {
    func processImages(_ imageUrls: [String]) async throws -> [ProcessedImage] {
        print("Processing \(imageUrls.count) images...")
        
        return try await withTaskGroup(of: ProcessedImage.self) { group in
            var processedImages: [ProcessedImage] = []
            
            // Add tasks to the group
            for (index, url) in imageUrls.enumerated() {
                group.addTask {
                    return try await self.processImage(url, priority: index)
                }
            }
            
            // Collect results as they complete
            for try await processedImage in group {
                processedImages.append(processedImage)
                print("✅ Processed image: \(processedImage.url)")
            }
            
            return processedImages.sorted { $0.priority < $1.priority }
        }
    }
    
    func processImage(_ url: String, priority: Int) async throws -> ProcessedImage {
        print("🔄 Processing image: \(url)")
        
        // Simulate image processing
        let processingTime = Double.random(in: 0.5...2.0)
        try await Task.sleep(nanoseconds: UInt64(processingTime * 1_000_000_000))
        
        // Simulate random failures
        if Int.random(in: 1...10) == 1 {
            throw ImageProcessingError.processingFailed("Failed to process \(url)")
        }
        
        return ProcessedImage(
            url: url,
            priority: priority,
            size: Int.random(in: 100...1000),
            format: "JPEG"
        )
    }
}

struct ProcessedImage {
    let url: String
    let priority: Int
    let size: Int
    let format: String
}

enum ImageProcessingError: Error {
    case processingFailed(String)
}

// Usage
let processor = ImageProcessor()
let imageUrls = [
    "https://example.com/image1.jpg",
    "https://example.com/image2.jpg",
    "https://example.com/image3.jpg",
    "https://example.com/image4.jpg",
    "https://example.com/image5.jpg"
]

Task {
    do {
        let startTime = Date()
        let processedImages = try await processor.processImages(imageUrls)
        let endTime = Date()
        
        let duration = endTime.timeIntervalSince(startTime)
        print("✅ All images processed in \(String(format: "%.2f", duration)) seconds")
        
        for image in processedImages {
            print("   \(image.url) - \(image.size)KB - \(image.format)")
        }
        
    } catch {
        print("❌ Image processing failed: \(error)")
    }
}
```

### Real-World Concurrency Examples

Let's see how concurrency is used in practical applications.

**Example 1: E-commerce Inventory Management System**

```swift
enum InventoryError: Error {
    case insufficientStock(String)
    case productNotFound(String)
    case databaseError(String)
}

struct Product {
    let id: String
    let name: String
    var stockQuantity: Int
    let price: Decimal
    let category: String
}

struct Order {
    let id: String
    let userId: String
    let items: [OrderItem]
    let totalAmount: Decimal
    let status: OrderStatus
    
    enum OrderStatus: String {
        case pending = "Pending"
        case processing = "Processing"
        case completed = "Completed"
        case cancelled = "Cancelled"
    }
}

struct OrderItem {
    let productId: String
    let quantity: Int
    let unitPrice: Decimal
}

class InventoryManager {
    private var products: [String: Product] = [:]
    private var orders: [String: Order] = [:]
    
    init() {
        // Initialize with sample products
        products = [
            "P001": Product(id: "P001", name: "iPhone 15", stockQuantity: 50, price: 999.99, category: "Electronics"),
            "P002": Product(id: "P002", name: "MacBook Pro", stockQuantity: 25, price: 1999.99, category: "Electronics"),
            "P003": Product(id: "P003", name: "AirPods Pro", stockQuantity: 100, price: 249.99, category: "Electronics"),
            "P004": Product(id: "P004", name: "iPad Air", stockQuantity: 75, price: 599.99, category: "Electronics"),
            "P005": Product(id: "P005", name: "Apple Watch", stockQuantity: 30, price: 399.99, category: "Electronics")
        ]
    }
    
    func processOrder(_ order: Order) async throws -> Order {
        print("🔄 Processing order: \(order.id)")
        
        // Validate stock availability
        try await validateStock(for: order)
        
        // Reserve stock
        try await reserveStock(for: order)
        
        // Process payment (simulated)
        try await processPayment(for: order)
        
        // Update inventory
        try await updateInventory(for: order)
        
        // Update order status
        var updatedOrder = order
        updatedOrder.status = .completed
        
        orders[order.id] = updatedOrder
        
        print("✅ Order \(order.id) processed successfully")
        return updatedOrder
    }
    
    private func validateStock(for order: Order) async throws {
        print("📋 Validating stock for order \(order.id)...")
        
        try await Task.sleep(nanoseconds: UInt64(200_000_000)) // 0.2 seconds
        
        for item in order.items {
            guard let product = products[item.productId] else {
                throw InventoryError.productNotFound("Product \(item.productId) not found")
            }
            
            guard product.stockQuantity >= item.quantity else {
                throw InventoryError.insufficientStock("Insufficient stock for \(product.name). Available: \(product.stockQuantity), Requested: \(item.quantity)")
            }
        }
        
        print("✅ Stock validation passed")
    }
    
    private func reserveStock(for order: Order) async throws {
        print("🔒 Reserving stock for order \(order.id)...")
        
        try await Task.sleep(nanoseconds: UInt64(300_000_000)) // 0.3 seconds
        
        for item in order.items {
            products[item.productId]?.stockQuantity -= item.quantity
        }
        
        print("✅ Stock reserved successfully")
    }
    
    private func processPayment(for order: Order) async throws {
        print("💳 Processing payment for order \(order.id)...")
        
        try await Task.sleep(nanoseconds: UInt64(500_000_000)) // 0.5 seconds
        
        // Simulate payment processing
        if Int.random(in: 1...20) == 1 {
            throw InventoryError.databaseError("Payment processing failed")
        }
        
        print("✅ Payment processed successfully")
    }
    
    private func updateInventory(for order: Order) async throws {
        print("📊 Updating inventory for order \(order.id)...")
        
        try await Task.sleep(nanoseconds: UInt64(200_000_000)) // 0.2 seconds
        
        // Simulate database update
        if Int.random(in: 1...20) == 1 {
            throw InventoryError.databaseError("Failed to update inventory")
        }
        
        print("✅ Inventory updated successfully")
    }
    
    func processMultipleOrders(_ orders: [Order]) async -> [Order] {
        print("🚀 Processing \(orders.count) orders concurrently...")
        
        return await withTaskGroup(of: Order.self) { group in
            var processedOrders: [Order] = []
            
            // Add all orders to the task group
            for order in orders {
                group.addTask {
                    do {
                        return try await self.processOrder(order)
                    } catch {
                        print("❌ Failed to process order \(order.id): \(error)")
                        // Return cancelled order
                        var cancelledOrder = order
                        cancelledOrder.status = .cancelled
                        return cancelledOrder
                    }
                }
            }
            
            // Collect results
            for await processedOrder in group {
                processedOrders.append(processedOrder)
            }
            
            return processedOrders.sorted { $0.id < $1.id }
        }
    }
    
    func getInventoryStatus() -> [String: Int] {
        var status: [String: Int] = [:]
        for (id, product) in products {
            status[id] = product.stockQuantity
        }
        return status
    }
}

// Usage
let inventoryManager = InventoryManager()

// Create sample orders
let orders = [
    Order(
        id: "O001",
        userId: "U001",
        items: [
            OrderItem(productId: "P001", quantity: 1, unitPrice: 999.99),
            OrderItem(productId: "P003", quantity: 2, unitPrice: 249.99)
        ],
        totalAmount: 1499.97,
        status: .pending
    ),
    Order(
        id: "O002",
        userId: "U002",
        items: [
            OrderItem(productId: "P002", quantity: 1, unitPrice: 1999.99)
        ],
        totalAmount: 1999.99,
        status: .pending
    ),
    Order(
        id: "O003",
        userId: "U003",
        items: [
            OrderItem(productId: "P004", quantity: 1, unitPrice: 599.99),
            OrderItem(productId: "P005", quantity: 1, unitPrice: 399.99)
        ],
        totalAmount: 999.98,
        status: .pending
    )
]

// Process orders concurrently
Task {
    let startTime = Date()
    
    print("Initial inventory status:")
    let initialStatus = inventoryManager.getInventoryStatus()
    for (id, quantity) in initialStatus {
        print("   \(id): \(quantity) units")
    }
    
    print("\n" + String(repeating: "=", count: 50))
    
    let processedOrders = await inventoryManager.processMultipleOrders(orders)
    
    let endTime = Date()
    let duration = endTime.timeIntervalSince(startTime)
    
    print("\n" + String(repeating: "=", count: 50))
    print("🎯 Processing completed in \(String(format: "%.2f", duration)) seconds")
    
    print("\nOrder processing results:")
    for order in processedOrders {
        print("   \(order.id): \(order.status.rawValue)")
    }
    
    print("\nFinal inventory status:")
    let finalStatus = inventoryManager.getInventoryStatus()
    for (id, quantity) in finalStatus {
        print("   \(id): \(quantity) units")
    }
}
```

**Example 2: Weather Data Aggregation System**

```swift
struct WeatherData {
    let city: String
    let temperature: Double
    let humidity: Int
    let description: String
    let timestamp: Date
}

struct CityWeather {
    let city: String
    let current: WeatherData
    let forecast: [WeatherData]
    let alerts: [String]
}

enum WeatherError: Error {
    case cityNotFound(String)
    case apiError(String)
    case networkError(String)
}

class WeatherService {
    private let cities = ["New York", "London", "Tokyo", "Sydney", "Paris", "Berlin", "Moscow", "Beijing"]
    
    func getWeatherForAllCities() async throws -> [CityWeather] {
        print("🌤️ Fetching weather data for \(cities.count) cities...")
        
        return try await withTaskGroup(of: CityWeather.self) { group in
            var cityWeathers: [CityWeather] = []
            
            // Fetch weather for each city concurrently
            for city in cities {
                group.addTask {
                    return try await self.getWeatherForCity(city)
                }
            }
            
            // Collect results
            for try await cityWeather in group {
                cityWeathers.append(cityWeather)
                print("✅ Weather data received for \(cityWeather.city)")
            }
            
            return cityWeathers.sorted { $0.city < $1.city }
        }
    }
    
    func getWeatherForCity(_ city: String) async throws -> CityWeather {
        print("🔄 Fetching weather for \(city)...")
        
        // Simulate API calls with different response times
        let baseDelay = Double.random(in: 0.5...2.0)
        try await Task.sleep(nanoseconds: UInt64(baseDelay * 1_000_000_000))
        
        // Simulate random API errors
        if Int.random(in: 1...15) == 1 {
            throw WeatherError.apiError("API rate limit exceeded for \(city)")
        }
        
        // Generate weather data
        let currentWeather = WeatherData(
            city: city,
            temperature: Double.random(in: -10...35),
            humidity: Int.random(in: 30...90),
            description: getRandomWeatherDescription(),
            timestamp: Date()
        )
        
        let forecast = generateForecast(for: city)
        let alerts = generateAlerts(for: city)
        
        return CityWeather(
            city: city,
            current: currentWeather,
            forecast: forecast,
            alerts: alerts
        )
    }
    
    private func generateForecast(for city: String) -> [WeatherData] {
        var forecast: [WeatherData] = []
        
        for day in 1...5 {
            let date = Calendar.current.date(byAdding: .day, value: day, to: Date()) ?? Date()
            let weather = WeatherData(
                city: city,
                temperature: Double.random(in: -15...40),
                humidity: Int.random(in: 25...95),
                description: getRandomWeatherDescription(),
                timestamp: date
            )
            forecast.append(weather)
        }
        
        return forecast
    }
    
    private func generateAlerts(for city: String) -> [String] {
        var alerts: [String] = []
        
        // Simulate weather alerts
        if Int.random(in: 1...5) == 1 {
            alerts.append("Severe weather warning")
        }
        
        if Int.random(in: 1...8) == 1 {
            alerts.append("Air quality alert")
        }
        
        return alerts
    }
    
    private func getRandomWeatherDescription() -> String {
        let descriptions = [
            "Sunny", "Cloudy", "Rainy", "Snowy", "Partly Cloudy",
            "Thunderstorm", "Foggy", "Windy", "Clear", "Overcast"
        ]
        return descriptions.randomElement() ?? "Unknown"
    }
    
    func analyzeWeatherPatterns(_ cityWeathers: [CityWeather]) async -> WeatherAnalysis {
        print("📊 Analyzing weather patterns...")
        
        let totalCities = cityWeathers.count
        let citiesWithAlerts = cityWeathers.filter { !$0.alerts.isEmpty }.count
        
        let averageTemperature = cityWeathers.reduce(0.0) { $0 + $1.current.temperature } / Double(totalCities)
        let averageHumidity = cityWeathers.reduce(0) { $0 + $1.current.humidity } / totalCities
        
        let hottestCity = cityWeathers.max { $0.current.temperature < $1.current.temperature }?.city ?? "Unknown"
        let coldestCity = cityWeathers.min { $0.current.temperature < $1.current.temperature }?.city ?? "Unknown"
        
        let weatherDistribution = Dictionary(grouping: cityWeathers) { $0.current.description }
            .mapValues { $0.count }
        
        return WeatherAnalysis(
            totalCities: totalCities,
            citiesWithAlerts: citiesWithAlerts,
            averageTemperature: averageTemperature,
            averageHumidity: averageHumidity,
            hottestCity: hottestCity,
            coldestCity: coldestCity,
            weatherDistribution: weatherDistribution
        )
    }
}

struct WeatherAnalysis {
    let totalCities: Int
    let citiesWithAlerts: Int
    let averageTemperature: Double
    let averageHumidity: Int
    let hottestCity: String
    let coldestCity: String
    let weatherDistribution: [String: Int]
}

// Usage
let weatherService = WeatherService()

Task {
    do {
        let startTime = Date()
        
        // Fetch weather data for all cities concurrently
        let cityWeathers = try await weatherService.getWeatherForAllCities()
        
        // Analyze weather patterns
        let analysis = await weatherService.analyzeWeatherPatterns(cityWeathers)
        
        let endTime = Date()
        let duration = endTime.timeIntervalSince(startTime)
        
        print("\n" + String(repeating: "=", count: 60))
        print("🌍 WEATHER ANALYSIS COMPLETE")
        print(String(repeating: "=", count: 60))
        print("⏱️  Total time: \(String(format: "%.2f", duration)) seconds")
        print("🏙️  Cities analyzed: \(analysis.totalCities)")
        print("⚠️   Cities with alerts: \(analysis.citiesWithAlerts)")
        print("🌡️  Average temperature: \(String(format: "%.1f", analysis.averageTemperature))°C")
        print("💧 Average humidity: \(analysis.averageHumidity)%")
        print("🔥 Hottest city: \(analysis.hottestCity)")
        print("❄️  Coldest city: \(analysis.coldestCity)")
        
        print("\n📊 Weather distribution:")
        for (description, count) in analysis.weatherDistribution.sorted(by: { $0.value > $1.value }) {
            print("   \(description): \(count) cities")
        }
        
        print("\n🏙️  City details:")
        for cityWeather in cityWeathers {
            print("   \(cityWeather.city): \(String(format: "%.1f", cityWeather.current.temperature))°C, \(cityWeather.current.description)")
            if !cityWeather.alerts.isEmpty {
                print("      ⚠️  Alerts: \(cityWeather.alerts.joined(separator: ", "))")
            }
        }
        
    } catch {
        print("❌ Weather service error: \(error)")
    }
}
```

### Advanced Concurrency Features

#### Actors

Actors provide thread-safe access to mutable state.

```swift
actor BankAccount {
    private var balance: Decimal
    private let accountNumber: String
    private var transactions: [Transaction] = []
    
    init(accountNumber: String, initialBalance: Decimal = 0) {
        self.accountNumber = accountNumber
        self.balance = initialBalance
    }
    
    func deposit(amount: Decimal) async throws {
        guard amount > 0 else {
            throw BankError.invalidAmount("Deposit amount must be positive")
        }
        
        balance += amount
        
        let transaction = Transaction(
            type: .deposit,
            amount: amount,
            timestamp: Date()
        )
        transactions.append(transaction)
        
        print("💰 Deposited $\(amount) to account \(accountNumber)")
    }
    
    func withdraw(amount: Decimal) async throws {
        guard amount > 0 else {
            throw BankError.invalidAmount("Withdrawal amount must be positive")
        }
        
        guard balance >= amount else {
            throw BankError.insufficientFunds("Insufficient funds. Balance: $\(balance), Requested: $\(amount)")
        }
        
        balance -= amount
        
        let transaction = Transaction(
            type: .withdrawal,
            amount: amount,
            timestamp: Date()
        )
        transactions.append(transaction)
        
        print("💸 Withdrew $\(amount) from account \(accountNumber)")
    }
    
    func transfer(amount: Decimal, to recipient: BankAccount) async throws {
        try await withdraw(amount: amount)
        try await recipient.deposit(amount: amount)
        
        let transaction = Transaction(
            type: .transfer,
            amount: amount,
            timestamp: Date()
        )
        transactions.append(transaction)
        
        print("💳 Transferred $\(amount) to account \(recipient.accountNumber)")
    }
    
    func getBalance() async -> Decimal {
        return balance
    }
    
    func getTransactionHistory() async -> [Transaction] {
        return transactions
    }
    
    var accountNumber: String {
        return accountNumber
    }
}

struct Transaction {
    let type: TransactionType
    let amount: Decimal
    let timestamp: Date
    
    enum TransactionType {
        case deposit, withdrawal, transfer
    }
}

enum BankError: Error {
    case invalidAmount(String)
    case insufficientFunds(String)
}

// Usage
Task {
    let account1 = BankAccount(accountNumber: "ACC001", initialBalance: 1000)
    let account2 = BankAccount(accountNumber: "ACC002", initialBalance: 500)
    
    // Perform concurrent operations
    async let deposit1 = account1.deposit(amount: 200)
    async let deposit2 = account2.deposit(amount: 100)
    async let transfer = account1.transfer(amount: 150, to: account2)
    
    do {
        try await (deposit1, deposit2, transfer)
        
        let balance1 = await account1.getBalance()
        let balance2 = await account2.getBalance()
        
        print("✅ All operations completed successfully")
        print("Account 1 balance: $\(balance1)")
        print("Account 2 balance: $\(balance2)")
        
    } catch {
        print("❌ Bank operation failed: \(error)")
    }
}
```

### Best Practices for Concurrency

1. **Use async/await** - Prefer async/await over completion handlers
2. **Run concurrent tasks** - Use task groups for multiple concurrent operations
3. **Handle errors properly** - Use try-catch with async functions
4. **Use actors for shared state** - Actors provide thread-safe access to mutable state
5. **Avoid blocking operations** - Don't block the main thread with long-running tasks
6. **Consider task priorities** - Use appropriate task priorities for different operations
7. **Test concurrency** - Ensure your concurrent code works correctly
8. **Monitor performance** - Measure the impact of concurrency on your application

**Concurrency design principles:**

- **Non-blocking** - Keep the main thread responsive
- **Structured** - Use clear, readable async/await patterns
- **Safe** - Ensure thread safety with actors and proper synchronization
- **Efficient** - Use concurrency to improve performance, not just for its own sake
- **Maintainable** - Write concurrent code that's easy to understand and debug

Concurrency is a powerful tool for building responsive, scalable Swift applications. Understanding how to use async/await, task groups, and actors effectively will help you create applications that make better use of system resources and provide a better user experience.

---

#### @MainActor and Main Thread Safety

The `@MainActor` attribute ensures that code runs on the main thread, which is essential for UI updates and other main-thread-only operations.

```swift
// Class marked with @MainActor
@MainActor
class UIViewController {
    var title: String = ""
    var view: UIView?
    
    func updateUI() {
        // This code automatically runs on the main thread
        title = "Updated Title"
        view?.backgroundColor = .red
    }
    
    // Nonisolated method can run on any thread
    nonisolated func processData() -> String {
        return "Processed data"
    }
}

// Function marked with @MainActor
@MainActor
func updateUserInterface() {
    // This function can only be called from the main thread
    print("Updating UI on main thread")
}

// Property marked with @MainActor
@MainActor
var globalUIState: String = "Initial"

// Usage
Task {
    // This will run on a background thread
    let data = await someAsyncOperation()
    
    // Switch to main thread for UI updates
    await MainActor.run {
        updateUserInterface()
        globalUIState = "Updated"
    }
    
    // Or use @MainActor function directly
    await updateUserInterface()
}

// MainActor.run for temporary main thread execution
Task {
    let result = await someAsyncOperation()
    
    await MainActor.run {
        // This block runs on the main thread
        print("Result: \(result)")
        // Update UI elements here
    }
}
```

#### Sendable and Data Race Safety

The `Sendable` protocol ensures that types can be safely passed between concurrent contexts without data races.

```swift
// Sendable struct - safe to pass between threads
struct UserProfile: Sendable {
    let id: String
    let name: String
    let email: String
    let preferences: UserPreferences
    
    // Nested type must also be Sendable
    struct UserPreferences: Sendable {
        let theme: String
        let notifications: Bool
    }
}

// Sendable enum - safe by default
enum UserStatus: Sendable {
    case active
    case inactive
    case suspended(String) // Associated value must be Sendable
}

// Sendable class - must be final and have only immutable properties
final class Configuration: Sendable {
    let apiKey: String
    let baseURL: String
    let timeout: TimeInterval
    
    init(apiKey: String, baseURL: String, timeout: TimeInterval) {
        self.apiKey = apiKey
        self.baseURL = baseURL
        self.timeout = timeout
    }
}

// Function that requires Sendable parameters
func processUserData<T: Sendable>(_ user: T) async {
    // This function can safely work with concurrent data
    await Task.detached {
        // user is safe to use here because it's Sendable
        print("Processing user: \(user)")
    }
}

// Actor with Sendable requirements
actor DataProcessor {
    func process<T: Sendable>(_ data: T) async -> String {
        // data is safe to use because it's Sendable
        return "Processed: \(data)"
    }
}

// Usage
let userProfile = UserProfile(
    id: "user123",
    name: "John Doe",
    email: "john@example.com",
    preferences: UserProfile.UserPreferences(theme: "Dark", notifications: true)
)

let config = Configuration(
    apiKey: "abc123",
    baseURL: "https://api.example.com",
    timeout: 30.0
)

Task {
    await processUserData(userProfile)
    await processUserData(config)
}
```

#### Task Priorities and Cancellation

Swift provides sophisticated task management with priorities and cancellation support.

```swift
// Task with specific priority
Task(priority: .high) {
    print("High priority task started")
    try await Task.sleep(nanoseconds: 1_000_000_000)
    print("High priority task completed")
}

Task(priority: .low) {
    print("Low priority task started")
    try await Task.sleep(nanoseconds: 2_000_000_000)
    print("Low priority task completed")
}

// Task with cancellation checking
func longRunningTask() async throws -> String {
    try await Task.sleep(nanoseconds: 5_000_000_000) // 5 seconds
    
    // Check for cancellation
    try Task.checkCancellation()
    
    return "Task completed"
}

// Cancellable task
let task = Task {
    do {
        let result = try await longRunningTask()
        print("Result: \(result)")
    } catch is CancellationError {
        print("Task was cancelled")
    } catch {
        print("Task failed with error: \(error)")
    }
}

// Cancel the task after 2 seconds
Task {
    try await Task.sleep(nanoseconds: 2_000_000_000)
    task.cancel()
}

// Task with cancellation handler
func cancellableTask() async throws -> String {
    return try await withTaskCancellationHandler {
        try await longRunningTask()
    } onCancel: {
        // Cleanup code when cancelled
        print("Cleaning up cancelled task")
    }
}

// Usage
let cancellableTask = Task {
    do {
        let result = try await cancellableTask()
        print("Result: \(result)")
    } catch is CancellationError {
        print("Task was cancelled")
    } catch {
        print("Task failed: \(error)")
    }
}

// Cancel after 3 seconds
Task {
    try await Task.sleep(nanoseconds: 3_000_000_000)
    cancellableTask.cancel()
}
```

#### Structured Concurrency with Async Sequences

Async sequences provide a way to work with streams of asynchronous data.

```swift
// Custom async sequence
struct NumberGenerator: AsyncSequence {
    typealias Element = Int
    
    let count: Int
    
    struct AsyncIterator: AsyncIteratorProtocol {
        let count: Int
        var current = 0
        
        mutating func next() async throws -> Int? {
            guard current < count else { return nil }
            
            // Simulate async work
            try await Task.sleep(nanoseconds: 100_000_000) // 0.1 seconds
            
            current += 1
            return current
        }
    }
    
    func makeAsyncIterator() -> AsyncIterator {
        return AsyncIterator(count: count)
    }
}

// Async sequence extension
extension NumberGenerator {
    func filter(_ predicate: @escaping (Int) -> Bool) -> AsyncFilterSequence<NumberGenerator> {
        return self.filter(predicate)
    }
    
    func map<T>(_ transform: @escaping (Int) -> T) -> AsyncMapSequence<NumberGenerator, T> {
        return self.map(transform)
    }
}

// Usage
Task {
    print("Generating numbers...")
    
    for try await number in NumberGenerator(count: 10) {
        print("Generated: \(number)")
    }
    
    print("Generation complete")
}

// Using async sequence operations
Task {
    let evenNumbers = NumberGenerator(count: 20)
        .filter { $0 % 2 == 0 }
        .map { "Even: \($0)" }
    
    for try await evenNumber in evenNumbers {
        print(evenNumber)
    }
}

// Async stream for real-time data
class DataStream {
    private var continuation: AsyncStream<Int>.Continuation?
    private var stream: AsyncStream<Int>?
    
    init() {
        (stream, continuation) = AsyncStream.makeStream(of: Int.self)
    }
    
    func startGenerating() {
        Task {
            for i in 1...10 {
                try await Task.sleep(nanoseconds: 500_000_000) // 0.5 seconds
                continuation?.yield(i)
            }
            continuation?.finish()
        }
    }
    
    func getStream() -> AsyncStream<Int> {
        return stream ?? AsyncStream { _ in }
    }
}

// Usage
let dataStream = DataStream()
dataStream.startGenerating()

Task {
    for try await value in dataStream.getStream() {
        print("Received: \(value)")
    }
    print("Stream finished")
}
```

#### Nonisolated and Isolated Keywords

These keywords provide fine-grained control over actor isolation.

```swift
actor BankAccount {
    private var balance: Decimal
    private let accountNumber: String
    
    init(accountNumber: String, initialBalance: Decimal = 0) {
        self.accountNumber = accountNumber
        self.balance = initialBalance
    }
    
    // Isolated method - can access actor state
    func deposit(amount: Decimal) async throws {
        guard amount > 0 else {
            throw BankError.invalidAmount("Amount must be positive")
        }
        
        balance += amount
        print("Deposited $\(amount)")
    }
    
    // Nonisolated method - cannot access actor state
    nonisolated func getAccountNumber() -> String {
        return accountNumber
    }
    
    // Nonisolated computed property
    nonisolated var displayName: String {
        return "Account \(accountNumber)"
    }
    
    // Isolated method that calls nonisolated method
    func processTransaction() async {
        // Can call nonisolated methods
        let name = getAccountNumber()
        let display = displayName
        
        print("Processing transaction for \(display)")
    }
}

// Nonisolated function that works with actors
nonisolated func validateAmount(_ amount: Decimal) -> Bool {
    return amount > 0 && amount <= 10000
}

// Usage
let account = BankAccount(accountNumber: "ACC001", initialBalance: 1000)

Task {
    // Can call nonisolated methods without await
    let accountNumber = account.getAccountNumber()
    let displayName = account.displayName
    
    print("Account: \(displayName)")
    
    // Must use await for isolated methods
    try await account.deposit(amount: 500)
    await account.processTransaction()
}
```

#### Global Actors

Global actors provide application-wide isolation guarantees.

```swift
// Global actor for UI operations
@globalActor
actor UIActor {
    static let shared = UIActor()
}

// Global actor for database operations
@globalActor
actor DatabaseActor {
    static let shared = DatabaseActor()
}

// Global actor for network operations
@globalActor
actor NetworkActor {
    static let shared = NetworkActor()
}

// Using global actors
@UIActor
class UIViewController {
    var title: String = ""
    
    func updateTitle(_ newTitle: String) {
        title = newTitle
        print("Title updated to: \(newTitle)")
    }
}

@DatabaseActor
class DatabaseManager {
    private var connections: [String: String] = [:]
    
    func connect(to database: String) {
        connections[database] = "Connected"
        print("Connected to \(database)")
    }
    
    func disconnect(from database: String) {
        connections.removeValue(forKey: database)
        print("Disconnected from \(database)")
    }
}

@NetworkActor
class NetworkManager {
    private var activeRequests: Set<String> = []
    
    func startRequest(_ id: String) {
        activeRequests.insert(id)
        print("Started request: \(id)")
    }
    
    func finishRequest(_ id: String) {
        activeRequests.remove(id)
        print("Finished request: \(id)")
    }
}

// Usage
Task {
    let uiController = UIViewController()
    let dbManager = DatabaseManager()
    let networkManager = NetworkManager()
    
    // These operations run on their respective actors
    await uiController.updateTitle("New Title")
    await dbManager.connect(to: "users")
    await networkManager.startRequest("req1")
    
    // Concurrent operations on different actors
    async let uiUpdate = uiController.updateTitle("Updated Title")
    async let dbConnect = dbManager.connect(to: "products")
    async let networkStart = networkManager.startRequest("req2")
    
    try await (uiUpdate, dbConnect, networkStart)
    
    print("All operations completed")
}
```

#### Advanced Concurrency Patterns

```swift
// Async timeout pattern
func withTimeout<T>(_ operation: @escaping () async throws -> T, 
                   timeout: TimeInterval) async throws -> T {
    return try await withThrowingTaskGroup(of: T.self) { group in
        group.addTask {
            try await operation()
        }
        
        group.addTask {
            try await Task.sleep(nanoseconds: UInt64(timeout * 1_000_000_000))
            throw TimeoutError.timeout
        }
        
        let result = try await group.next()!
        group.cancelAll()
        return result
    }
}

enum TimeoutError: Error {
    case timeout
}

// Retry pattern
func withRetry<T>(_ operation: @escaping () async throws -> T,
                  maxAttempts: Int = 3,
                  delay: TimeInterval = 1.0) async throws -> T {
    var lastError: Error?
    
    for attempt in 1...maxAttempts {
        do {
            return try await operation()
        } catch {
            lastError = error
            print("Attempt \(attempt) failed: \(error)")
            
            if attempt < maxAttempts {
                try await Task.sleep(nanoseconds: UInt64(delay * 1_000_000_000))
            }
        }
    }
    
    throw lastError ?? TimeoutError.timeout
}

// Usage
Task {
    do {
        // Operation with timeout
        let result = try await withTimeout({
            try await Task.sleep(nanoseconds: 3_000_000_000)
            return "Operation completed"
        }, timeout: 2.0)
        
        print("Result: \(result)")
    } catch TimeoutError.timeout {
        print("Operation timed out")
    } catch {
        print("Operation failed: \(error)")
    }
    
    // Operation with retry
    let retryResult = try await withRetry({
        // Simulate sometimes failing operation
        if Int.random(in: 1...3) == 1 {
            throw NSError(domain: "Test", code: 1, userInfo: nil)
        }
        return "Retry successful"
    }, maxAttempts: 5, delay: 0.5)
    
    print("Retry result: \(retryResult)")
}
```

### Best Practices for Advanced Concurrency

1. **Use @MainActor for UI code** - Ensure UI updates happen on the main thread
2. **Make types Sendable** - Ensure data can be safely passed between concurrent contexts
3. **Handle task cancellation** - Always check for cancellation in long-running tasks
4. **Use appropriate task priorities** - Match priorities to task importance
5. **Leverage async sequences** - For streaming and processing data
6. **Use global actors** - For application-wide isolation guarantees
7. **Implement proper error handling** - Handle both cancellation and other errors
8. **Test concurrent code thoroughly** - Ensure thread safety and correct behavior

---

## Macros

Macros are a powerful Swift feature that allows you to generate code at compile time. They can reduce boilerplate, enforce patterns, and create more expressive APIs.

### Understanding Macros

**Why This Matters:** Macros eliminate repetitive code and enable compile-time code generation, making your code more maintainable and less error-prone.

#### Basic Macro Syntax

```swift
// Macro declaration
@macro
macro MyMacro() = #externalMacro(
    module: "MyMacroModule",
    type: "MyMacroImplementation"
)

// Using a macro
@MyMacro
struct MyStruct {
    // Generated code will be inserted here
}
```

#### Built-in Macros

Swift provides several built-in macros for common tasks:

```swift
// @available - API availability checking
@available(iOS 15.0, macOS 12.0, *)
func newFeature() {
    // This function is only available on iOS 15+ and macOS 12+
}

// @discardableResult - Allow ignoring return values
@discardableResult
func processData() -> String {
    return "Processed"
}

// Can be called without storing result
processData() // No warning about unused result

// @frozen - Optimize enum performance
@frozen enum Direction {
    case north, south, east, west
}

// @inlinable - Suggest inline expansion
@inlinable
func fastCalculation(_ x: Int) -> Int {
    return x * 2 + 1
}
```

#### Custom Macro Example

Here's a simple example of how macros can be used to generate boilerplate code:

```swift
// Define a macro for generating Codable conformance
@macro
macro Codable() = #externalMacro(
    module: "CodableMacroModule",
    type: "CodableMacroImplementation"
)

// Use the macro
@Codable
struct User {
    let id: Int
    let name: String
    let email: String
    // Macro automatically generates Codable conformance
}

// The macro generates this code:
// extension User: Codable {}
```

### Best Practices for Macros

1. **Use built-in macros** - Leverage Swift's built-in macros when possible
2. **Keep macros simple** - Complex macros can be hard to debug
3. **Document macro behavior** - Make it clear what your macro does
4. **Test macro output** - Ensure generated code works correctly
5. **Consider alternatives** - Sometimes protocols or extensions are simpler

---

## Type Casting

Type casting allows you to check the type of an instance or treat it as a different superclass or subclass type within its class hierarchy.

### Understanding Type Casting

**Why This Matters:** Type casting is essential when working with class hierarchies, protocols, and generic types. It allows you to safely convert between related types.

#### Type Checking with `is`

The `is` operator checks whether an instance is of a certain type:

```swift
class Animal {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

class Dog: Animal {
    func bark() {
        print("Woof!")
    }
}

class Cat: Animal {
    func meow() {
        print("Meow!")
    }
}

let animals: [Animal] = [Dog(name: "Buddy"), Cat(name: "Whiskers")]

for animal in animals {
    if animal is Dog {
        print("\(animal.name) is a dog")
    } else if animal is Cat {
        print("\(animal.name) is a cat")
    }
}
```

#### Downcasting with `as?` and `as!`

Use `as?` for safe downcasting and `as!` for forced downcasting:

```swift
// Safe downcasting with as?
for animal in animals {
    if let dog = animal as? Dog {
        dog.bark()
    } else if let cat = animal as? Cat {
        cat.meow()
    }
}

// Forced downcasting with as! (use with caution)
let firstAnimal = animals[0]
let dog = firstAnimal as! Dog  // Crashes if not a Dog
dog.bark()
```

#### Type Casting for Any and AnyObject

```swift
// Any can represent any type
let things: [Any] = [0, 0.0, 42, 3.14159, "hello", (3.0, 5.0)]

for thing in things {
    switch thing {
    case 0 as Int:
        print("zero as an Int")
    case 0 as Double:
        print("zero as a Double")
    case let someInt as Int:
        print("an integer value of \(someInt)")
    case let someDouble as Double where someDouble > 0:
        print("a positive double value of \(someDouble)")
    case is String:
        print("some string")
    case let (x, y) as (Double, Double):
        print("an (x, y) point at \(x), \(y)")
    default:
        print("something else")
    }
}

// AnyObject can represent any class type
let objects: [AnyObject] = [Dog(name: "Buddy"), Cat(name: "Whiskers")]

for object in objects {
    if let dog = object as? Dog {
        print("Found a dog: \(dog.name)")
    }
}
```

#### Type Casting in Practice

```swift
// Real-world example: Handling different view types
protocol View {
    var id: String { get }
}

class Button: View {
    let id: String
    let title: String
    
    init(id: String, title: String) {
        self.id = id
        self.title = title
    }
    
    func tap() {
        print("Button \(title) tapped")
    }
}

class Label: View {
    let id: String
    let text: String
    
    init(id: String, text: String) {
        self.id = id
        self.text = text
    }
}

class ViewController {
    var views: [View] = []
    
    func addView(_ view: View) {
        views.append(view)
    }
    
    func handleTap(at index: Int) {
        guard index < views.count else { return }
        
        let view = views[index]
        
        // Type cast to handle different view types
        if let button = view as? Button {
            button.tap()
        } else if let label = view as? Label {
            print("Label tapped: \(label.text)")
        }
    }
}

// Usage
let vc = ViewController()
vc.addView(Button(id: "btn1", title: "Submit"))
vc.addView(Label(id: "lbl1", text: "Hello World"))

vc.handleTap(at: 0)  // Button tapped
vc.handleTap(at: 1)  // Label tapped: Hello World
```

### Best Practices for Type Casting

1. **Prefer `as?` over `as!`** - Safe downcasting prevents crashes
2. **Use `is` for type checking** - More readable than type casting
3. **Handle all cases** - Don't forget default cases in switch statements
4. **Consider protocols** - Sometimes protocols are better than type casting
5. **Test edge cases** - Ensure your type casting handles unexpected types

---

## Nested Types

Nested types allow you to define types within the scope of another type, providing better organization and namespace management.

### Understanding Nested Types

**Why This Matters:** Nested types help organize related code, provide clear namespacing, and make your code more readable and maintainable.

#### Basic Nested Type Syntax

```swift
struct Bank {
    // Nested enum
    enum AccountType {
        case checking
        case savings
        case business
    }
    
    // Nested struct
    struct Account {
        let number: String
        let type: AccountType
        var balance: Double
        
        // Nested enum within nested struct
        enum Status {
            case active
            case inactive
            case frozen
        }
        
        var status: Status = .active
    }
    
    // Nested class
    class Transaction {
        let id: String
        let amount: Double
        let timestamp: Date
        
        init(id: String, amount: Double) {
            self.id = id
            self.amount = amount
            self.timestamp = Date()
        }
    }
}

// Using nested types
let accountType = Bank.AccountType.checking
let account = Bank.Account(number: "12345", type: .checking, balance: 1000.0)
let transaction = Bank.Transaction(id: "TXN001", amount: 100.0)
```

#### Nested Types in Practice

```swift
// Real-world example: E-commerce system
struct ECommerce {
    // Product-related types
    struct Product {
        let id: String
        let name: String
        let price: Decimal
        let category: Category
        
        enum Category: String, CaseIterable {
            case electronics = "Electronics"
            case clothing = "Clothing"
            case books = "Books"
            case home = "Home & Garden"
        }
        
        struct Inventory {
            var quantity: Int
            let warehouse: String
            
            enum Status {
                case inStock
                case lowStock
                case outOfStock
            }
            
            var status: Status {
                switch quantity {
                case 0:
                    return .outOfStock
                case 1...5:
                    return .lowStock
                default:
                    return .inStock
                }
            }
        }
        
        var inventory: Inventory
    }
    
    // Order-related types
    struct Order {
        let id: String
        let customer: Customer
        let items: [OrderItem]
        let status: Status
        
        enum Status: String, CaseIterable {
            case pending = "Pending"
            case confirmed = "Confirmed"
            case shipped = "Shipped"
            case delivered = "Delivered"
            case cancelled = "Cancelled"
        }
        
        struct OrderItem {
            let product: Product
            let quantity: Int
            let unitPrice: Decimal
            
            var totalPrice: Decimal {
                return unitPrice * Decimal(quantity)
            }
        }
        
        var totalAmount: Decimal {
            return items.reduce(0) { $0 + $1.totalPrice }
        }
    }
    
    // Customer-related types
    struct Customer {
        let id: String
        let name: String
        let email: String
        let address: Address
        
        struct Address {
            let street: String
            let city: String
            let state: String
            let zipCode: String
            let country: String
            
            var fullAddress: String {
                return "\(street), \(city), \(state) \(zipCode), \(country)"
            }
        }
    }
}

// Usage
let customer = ECommerce.Customer(
    id: "CUST001",
    name: "John Doe",
    email: "john@example.com",
    address: ECommerce.Customer.Address(
        street: "123 Main St",
        city: "New York",
        state: "NY",
        zipCode: "10001",
        country: "USA"
    )
)

let product = ECommerce.Product(
    id: "PROD001",
    name: "iPhone 15",
    price: 999.99,
    category: .electronics,
    inventory: ECommerce.Product.Inventory(
        quantity: 10,
        warehouse: "NYC-WH-01"
    )
)

let orderItem = ECommerce.Order.OrderItem(
    product: product,
    quantity: 1,
    unitPrice: product.price
)

let order = ECommerce.Order(
    id: "ORD001",
    customer: customer,
    items: [orderItem],
    status: .pending
)

print("Order total: $\(order.totalAmount)")
print("Customer address: \(customer.address.fullAddress)")
print("Product status: \(product.inventory.status)")
```

#### Nested Types with Protocols

```swift
protocol DataProcessor {
    associatedtype Input
    associatedtype Output
    
    func process(_ input: Input) -> Output
}

struct ImageProcessor: DataProcessor {
    // Nested types for image processing
    struct Input {
        let imageData: Data
        let format: ImageFormat
        let quality: Quality
        
        enum ImageFormat: String, CaseIterable {
            case jpeg = "JPEG"
            case png = "PNG"
            case heic = "HEIC"
        }
        
        enum Quality: Int, CaseIterable {
            case low = 1
            case medium = 2
            case high = 3
            
            var compressionQuality: Float {
                switch self {
                case .low: return 0.3
                case .medium: return 0.6
                case .high: return 0.9
                }
            }
        }
    }
    
    struct Output {
        let processedImageData: Data
        let metadata: Metadata
        
        struct Metadata {
            let originalSize: Int
            let processedSize: Int
            let processingTime: TimeInterval
            let compressionRatio: Double
        }
    }
    
    func process(_ input: Input) -> Output {
        // Simulate image processing
        let startTime = Date()
        
        // Process image based on format and quality
        let processedData = input.imageData // Simplified
        
        let endTime = Date()
        let processingTime = endTime.timeIntervalSince(startTime)
        
        let metadata = Output.Metadata(
            originalSize: input.imageData.count,
            processedSize: processedData.count,
            processingTime: processingTime,
            compressionRatio: Double(processedData.count) / Double(input.imageData.count)
        )
        
        return Output(
            processedImageData: processedData,
            metadata: metadata
        )
    }
}

// Usage
let processor = ImageProcessor()
let input = ImageProcessor.Input(
    imageData: Data(),
    format: .jpeg,
    quality: .high
)

let output = processor.process(input)
print("Processing time: \(output.metadata.processingTime)s")
print("Compression ratio: \(output.metadata.compressionRatio)")
```

### Best Practices for Nested Types

1. **Use for logical grouping** - Nest types that are closely related
2. **Keep nesting shallow** - Avoid deeply nested types (more than 2-3 levels)
3. **Use descriptive names** - Make nested type names clear and meaningful
4. **Consider access control** - Use appropriate access levels for nested types
5. **Group related functionality** - Keep related types together in the same scope

---

## Extensions

Extensions allow you to add new functionality to existing types without modifying their original source code.

### Understanding Extensions

**Why This Matters:** Extensions provide a clean way to add functionality to types you don't own, organize your code better, and implement protocol conformance separately from type definitions.

#### Basic Extension Syntax

```swift
// Extending existing types
extension String {
    // Add computed properties
    var isPalindrome: Bool {
        let cleaned = self.lowercased().filter { $0.isLetter }
        return cleaned == cleaned.reversed()
    }
    
    // Add methods
    func reversed() -> String {
        return String(self.reversed())
    }
    
    // Add initializers
    init(repeating character: Character, count: Int) {
        self = String(repeating: character, count: count)
    }
}

// Using extensions
let text = "racecar"
print(text.isPalindrome)  // true
print(text.reversed())    // "racecar"
let dashes = String(repeating: "-", count: 10)  // "----------"
```

#### Extending with Computed Properties

```swift
extension Int {
    // Computed properties
    var isEven: Bool {
        return self % 2 == 0
    }
    
    var isOdd: Bool {
        return !isEven
    }
    
    var absoluteValue: Int {
        return abs(self)
    }
    
    var factorial: Int {
        guard self >= 0 else { return 0 }
        guard self > 1 else { return 1 }
        return self * (self - 1).factorial
    }
}

// Usage
let number = 7
print("\(number) is \(number.isEven ? "even" : "odd")")  // 7 is odd
print("\(number) factorial is \(number.factorial)")      // 7 factorial is 5040
print("Absolute value of -42 is \((-42).absoluteValue)") // Absolute value of -42 is 42
```

#### Extending with Methods

```swift
extension Array where Element: Numeric {
    // Add methods for numeric arrays
    func sum() -> Element {
        return self.reduce(0, +)
    }
    
    func average() -> Double {
        guard !isEmpty else { return 0.0 }
        return Double(sum()) / Double(count)
    }
    
    func product() -> Element {
        return self.reduce(1, *)
    }
}

extension Array where Element == String {
    // Add methods for string arrays
    func joined(separator: String = " ") -> String {
        return self.joined(separator: separator)
    }
    
    func capitalized() -> [String] {
        return self.map { $0.capitalized }
    }
    
    func filterEmpty() -> [String] {
        return self.filter { !$0.isEmpty }
    }
}

// Usage
let numbers = [1, 2, 3, 4, 5]
print("Sum: \(numbers.sum())")           // Sum: 15
print("Average: \(numbers.average())")   // Average: 3.0
print("Product: \(numbers.product())")   // Product: 120

let words = ["hello", "", "world", "swift", ""]
print("Joined: \(words.filterEmpty().joined())")        // Joined: hello world swift
print("Capitalized: \(words.capitalized())")            // Capitalized: ["Hello", "", "World", "Swift", ""]
```

#### Extending with Initializers

```swift
extension Date {
    // Convenience initializers
    init(year: Int, month: Int, day: Int) {
        var components = DateComponents()
        components.year = year
        components.month = month
        components.day = day
        components.hour = 0
        components.minute = 0
        components.second = 0
        
        self = Calendar.current.date(from: components) ?? Date()
    }
    
    init(today: Bool) {
        if today {
            self = Date()
        } else {
            self = Date().addingTimeInterval(-86400) // Yesterday
        }
    }
}

extension CGPoint {
    // Convenience initializers for common point patterns
    init(center: CGPoint, radius: CGFloat, angle: CGFloat) {
        let x = center.x + radius * cos(angle)
        let y = center.y + radius * sin(angle)
        self.init(x: x, y: y)
    }
    
    init(midpoint between: CGPoint, and: CGPoint) {
        let x = (between.x + and.x) / 2
        let y = (between.y + and.y) / 2
        self.init(x: x, y: y)
    }
}

// Usage
let birthday = Date(year: 1990, month: 5, day: 15)
let yesterday = Date(today: false)

let center = CGPoint(x: 100, y: 100)
let pointOnCircle = CGPoint(center: center, radius: 50, angle: .pi / 4)
let midpoint = CGPoint(midpoint: CGPoint(x: 0, y: 0), and: CGPoint(x: 100, y: 100))
```

#### Protocol Extensions

```swift
protocol Identifiable {
    var id: String { get }
}

extension Identifiable {
    // Default implementation
    var displayID: String {
        return "ID: \(id)"
    }
    
    // Default method implementation
    func isSame(as other: Identifiable) -> Bool {
        return self.id == other.id
    }
}

// Types automatically get the default implementation
struct User: Identifiable {
    let id: String
    let name: String
}

struct Product: Identifiable {
    let id: String
    let title: String
}

// Usage
let user = User(id: "user123", name: "John")
let product = Product(id: "prod456", title: "iPhone")

print(user.displayID)                    // ID: user123
print(product.displayID)                 // ID: prod456
print(user.isSame(as: product))         // false
```

#### Extensions in Practice

```swift
// Real-world example: Extending Foundation types for app-specific functionality
extension String {
    // Validation extensions
    var isValidEmail: Bool {
        let emailRegex = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"
        let emailPredicate = NSPredicate(format: "SELF MATCHES %@", emailRegex)
        return emailPredicate.evaluate(with: self)
    }
    
    var isValidPhoneNumber: Bool {
        let phoneRegex = "^[0-9]{10}$"
        let phonePredicate = NSPredicate(format: "SELF MATCHES %@", phoneRegex)
        return phonePredicate.evaluate(with: self)
    }
    
    var isStrongPassword: Bool {
        let hasUppercase = self.rangeOfCharacter(from: .uppercaseLetters) != nil
        let hasLowercase = self.rangeOfCharacter(from: .lowercaseLetters) != nil
        let hasDigit = self.rangeOfCharacter(from: .decimalDigits) != nil
        let hasSpecialChar = self.rangeOfCharacter(from: .punctuationCharacters) != nil
        
        return hasUppercase && hasLowercase && hasDigit && hasSpecialChar && self.count >= 8
    }
}

extension Date {
    // Date formatting extensions
    var shortDate: String {
        let formatter = DateFormatter()
        formatter.dateStyle = .short
        return formatter.string(from: self)
    }
    
    var longDate: String {
        let formatter = DateFormatter()
        formatter.dateStyle = .long
        return formatter.string(from: self)
    }
    
    var timeAgo: String {
        let interval = Date().timeIntervalSince(self)
        
        switch interval {
        case 0..<60:
            return "Just now"
        case 60..<3600:
            let minutes = Int(interval / 60)
            return "\(minutes) minute\(minutes == 1 ? "" : "s") ago"
        case 3600..<86400:
            let hours = Int(interval / 3600)
            return "\(hours) hour\(hours == 1 ? "" : "s") ago"
        case 86400..<2592000:
            let days = Int(interval / 86400)
            return "\(days) day\(days == 1 ? "" : "s") ago"
        default:
            return shortDate
        }
    }
}

// Usage
let email = "user@example.com"
let phone = "1234567890"
let password = "SecurePass123!"

print("Email valid: \(email.isValidEmail)")           // Email valid: true
print("Phone valid: \(phone.isValidPhoneNumber)")     // Phone valid: true
print("Password strong: \(password.isStrongPassword)") // Password strong: true

let pastDate = Date().addingTimeInterval(-3600) // 1 hour ago
print("Past date: \(pastDate.timeAgo)")        // Past date: 1 hour ago
```

### Best Practices for Extensions

1. **Use for organization** - Group related functionality in extensions
2. **Extend protocols** - Provide default implementations for protocols
3. **Keep extensions focused** - Each extension should have a single responsibility
4. **Use conditional extensions** - Extend types only when they meet certain criteria
5. **Don't override existing functionality** - Extensions should add, not replace
6. **Consider access control** - Use appropriate access levels for extension members
7. **Document complex extensions** - Explain what the extension adds and why

---

## Protocols

Protocols define a blueprint of methods, properties, and other requirements that suit a particular task or piece of functionality.

### Understanding Protocols

**Why This Matters:** Protocols are the foundation of Swift's type system, enabling polymorphism, code reuse, and flexible architecture. They're essential for building maintainable, testable code.

#### Basic Protocol Syntax

```swift
// Basic protocol definition
protocol Drawable {
    func draw()
    var area: Double { get }
}

// Protocol adoption
struct Circle: Drawable {
    let radius: Double
    
    func draw() {
        print("Drawing circle with radius \(radius)")
    }
    
    var area: Double {
        return Double.pi * radius * radius
    }
}

struct Rectangle: Drawable {
    let width: Double
    let height: Double
    
    func draw() {
        print("Drawing rectangle \(width) x \(height)")
    }
    
    var area: Double {
        return width * height
    }
}

// Using protocols as types
let shapes: [Drawable] = [Circle(radius: 5), Rectangle(width: 10, height: 5)]

for shape in shapes {
    shape.draw()
    print("Area: \(shape.area)")
}
```

#### Protocol Requirements

```swift
protocol Vehicle {
    // Property requirements
    var numberOfWheels: Int { get }
    var maxSpeed: Double { get }
    
    // Method requirements
    func startEngine()
    func stopEngine()
    func accelerate(by amount: Double)
    
    // Mutating method requirement (for structs)
    mutating func refuel(amount: Double)
    
    // Initializer requirement
    init(numberOfWheels: Int, maxSpeed: Double)
}

// Implementing the protocol
class Car: Vehicle {
    let numberOfWheels: Int
    let maxSpeed: Double
    private var currentSpeed: Double = 0
    private var fuelLevel: Double = 100
    
    required init(numberOfWheels: Int, maxSpeed: Double) {
        self.numberOfWheels = numberOfWheels
        self.maxSpeed = maxSpeed
    }
    
    func startEngine() {
        print("Car engine started")
    }
    
    func stopEngine() {
        print("Car engine stopped")
        currentSpeed = 0
    }
    
    func accelerate(by amount: Double) {
        currentSpeed = min(currentSpeed + amount, maxSpeed)
        print("Current speed: \(currentSpeed)")
    }
    
    func refuel(amount: Double) {
        fuelLevel = min(fuelLevel + amount, 100)
        print("Fuel level: \(fuelLevel)%")
    }
}

struct Motorcycle: Vehicle {
    let numberOfWheels: Int
    let maxSpeed: Double
    private var currentSpeed: Double = 0
    private var fuelLevel: Double = 100
    
    init(numberOfWheels: Int, maxSpeed: Double) {
        self.numberOfWheels = numberOfWheels
        self.maxSpeed = maxSpeed
    }
    
    func startEngine() {
        print("Motorcycle engine started")
    }
    
    func stopEngine() {
        print("Motorcycle engine stopped")
        currentSpeed = 0
    }
    
    func accelerate(by amount: Double) {
        currentSpeed = min(currentSpeed + amount, maxSpeed)
        print("Current speed: \(currentSpeed)")
    }
    
    mutating func refuel(amount: Double) {
        fuelLevel = min(fuelLevel + amount, 100)
        print("Fuel level: \(fuelLevel)%")
    }
}
```

#### Protocol Inheritance

```swift
protocol Animal {
    var name: String { get }
    func makeSound()
}

protocol Pet: Animal {
    var owner: String { get }
    func play()
}

protocol Trainable: Pet {
    func train(command: String)
}

// Implementing multiple protocols
struct Dog: Trainable {
    let name: String
    let owner: String
    private var trainedCommands: Set<String> = []
    
    func makeSound() {
        print("\(name): Woof!")
    }
    
    func play() {
        print("\(name) is playing with \(owner)")
    }
    
    func train(command: String) {
        trainedCommands.insert(command)
        print("\(name) learned: \(command)")
    }
    
    func perform(command: String) -> Bool {
        if trainedCommands.contains(command) {
            print("\(name) performs: \(command)")
            return true
        } else {
            print("\(name) doesn't know: \(command)")
            return false
        }
    }
}

// Usage
let dog = Dog(name: "Buddy", owner: "John")
dog.makeSound()
dog.play()
dog.train(command: "sit")
dog.train(command: "stay")
dog.perform(command: "sit")
dog.perform(command: "roll over")
```

#### Protocol Extensions

```swift
protocol Identifiable {
    var id: String { get }
}

// Default implementation
extension Identifiable {
    var displayID: String {
        return "ID: \(id)"
    }
    
    func isSame(as other: Identifiable) -> Bool {
        return self.id == other.id
    }
}

// Constrained extensions
extension Collection where Element: Identifiable {
    func find(by id: String) -> Element? {
        return first { $0.id == id }
    }
    
    func contains(_ element: Identifiable) -> Bool {
        return contains { $0.id == element.id }
    }
}

// Types automatically get the default implementation
struct User: Identifiable {
    let id: String
    let name: String
    let email: String
}

struct Product: Identifiable {
    let id: String
    let title: String
    let price: Double
}

// Usage
let users = [User(id: "user1", name: "Alice", email: "alice@example.com"),
             User(id: "user2", name: "Bob", email: "bob@example.com")]

let products = [Product(id: "prod1", title: "iPhone", price: 999.99),
                Product(id: "prod2", title: "MacBook", price: 1999.99)]

// Using protocol extension methods
if let user = users.find(by: "user1") {
    print(user.displayID)  // ID: user1
}

if users.contains(Product(id: "user1", title: "", price: 0)) {
    print("User found by ID")
}
```

#### Associated Types

```swift
protocol Container {
    associatedtype Item
    var items: [Item] { get set }
    var count: Int { get }
    
    mutating func add(_ item: Item)
    func item(at index: Int) -> Item?
}

// Implementing with associated types
struct Stack<Element>: Container {
    typealias Item = Element
    var items: [Element] = []
    
    var count: Int {
        return items.count
    }
    
    mutating func add(_ item: Element) {
        items.append(item)
    }
    
    func item(at index: Int) -> Element? {
        guard index >= 0 && index < items.count else { return nil }
        return items[index]
    }
    
    mutating func pop() -> Element? {
        return items.popLast()
    }
    
    func peek() -> Element? {
        return items.last
    }
}

struct Queue<Element>: Container {
    typealias Item = Element
    var items: [Element] = []
    
    var count: Int {
        return items.count
    }
    
    mutating func add(_ item: Element) {
        items.append(item)
    }
    
    func item(at index: Int) -> Element? {
        guard index >= 0 && index < items.count else { return nil }
        return items[index]
    }
    
    mutating func dequeue() -> Element? {
        guard !items.isEmpty else { return nil }
        return items.removeFirst()
    }
    
    func peek() -> Element? {
        return items.first
    }
}

// Usage
var intStack = Stack<Int>()
intStack.add(1)
intStack.add(2)
intStack.add(3)

var stringQueue = Queue<String>()
stringQueue.add("First")
stringQueue.add("Second")
stringQueue.add("Third")

print("Stack count: \(intStack.count)")
print("Queue count: \(stringQueue.count)")

if let item = intStack.pop() {
    print("Popped: \(item)")
}

if let item = stringQueue.dequeue() {
    print("Dequeued: \(item)")
}
```

#### Protocols in Practice

```swift
// Real-world example: Network layer with protocols
protocol NetworkRequest {
    var url: URL { get }
    var method: HTTPMethod { get }
    var headers: [String: String] { get }
    var body: Data? { get }
}

enum HTTPMethod: String {
    case get = "GET"
    case post = "POST"
    case put = "PUT"
    case delete = "DELETE"
}

protocol NetworkResponse {
    var data: Data { get }
    var statusCode: Int { get }
    var headers: [String: String] { get }
}

protocol NetworkClient {
    func send<T: NetworkRequest>(_ request: T) async throws -> NetworkResponse
}

// Concrete implementations
struct APIRequest: NetworkRequest {
    let url: URL
    let method: HTTPMethod
    let headers: [String: String]
    let body: Data?
    
    init(endpoint: String, method: HTTPMethod = .get, headers: [String: String] = [:], body: Data? = nil) {
        self.url = URL(string: endpoint)!
        self.method = method
        self.headers = headers
        self.body = body
    }
}

struct HTTPResponse: NetworkResponse {
    let data: Data
    let statusCode: Int
    let headers: [String: String]
}

class URLSessionClient: NetworkClient {
    func send<T: NetworkRequest>(_ request: T) async throws -> NetworkResponse {
        var urlRequest = URLRequest(url: request.url)
        urlRequest.httpMethod = request.method.rawValue
        urlRequest.httpBody = request.body
        
        for (key, value) in request.headers {
            urlRequest.setValue(value, forHTTPHeaderField: key)
        }
        
        let (data, response) = try await URLSession.shared.data(for: urlRequest)
        
        guard let httpResponse = response as? HTTPURLResponse else {
            throw NetworkError.invalidResponse
        }
        
        let headers = httpResponse.allHeaderFields as? [String: String] ?? [:]
        
        return HTTPResponse(
            data: data,
            statusCode: httpResponse.statusCode,
            headers: headers
        )
    }
}

enum NetworkError: Error {
    case invalidResponse
    case invalidStatusCode(Int)
}

// Usage
let client: NetworkClient = URLSessionClient()

let request = APIRequest(
    endpoint: "https://api.example.com/users",
    method: .get,
    headers: ["Authorization": "Bearer token123"]
)

do {
    let response = try await client.send(request)
    print("Response status: \(response.statusCode)")
    print("Response data size: \(response.data.count) bytes")
} catch {
    print("Network error: \(error)")
}
```

### Best Practices for Protocols

1. **Start with protocols** - Define interfaces before implementations
2. **Use protocol extensions** - Provide default implementations when possible
3. **Keep protocols focused** - Each protocol should have a single responsibility
4. **Use associated types** - For generic protocol requirements
5. **Prefer composition over inheritance** - Use protocols to compose behavior
6. **Document protocol requirements** - Make it clear what implementers must provide
7. **Test with protocols** - Use protocols to enable better testing

---

## Generics

Generics enable you to write flexible, reusable functions and types that can work with any type, subject to requirements you define.

### Understanding Generics

**Why This Matters:** Generics eliminate code duplication by allowing you to write functions and types that work with multiple types while maintaining type safety.

#### Generic Functions

```swift
// Generic function that works with any type
func swapValues<T>(_ a: inout T, _ b: inout T) {
    let temp = a
    a = b
    b = temp
}

// Generic function with constraints
func findIndex<T: Equatable>(of valueToFind: T, in array: [T]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}

// Generic function with multiple constraints
func processItems<T>(_ items: [T]) where T: Identifiable, T: Equatable {
    for item in items {
        print("Processing: \(item.displayID)")
    }
}

// Usage
var x = 5, y = 10
swapValues(&x, &y)
print("x: \(x), y: \(y)")  // x: 10, y: 5

let strings = ["apple", "banana", "cherry"]
if let index = findIndex(of: "banana", in: strings) {
    print("Found at index: \(index)")
}

let users = [User(id: "user1", name: "Alice", email: "alice@example.com")]
processItems(users)
```

#### Generic Types

```swift
// Generic stack implementation
struct Stack<Element> {
    var items: [Element] = []
    
    mutating func push(_ item: Element) {
        items.append(item)
    }
    
    mutating func pop() -> Element? {
        return items.popLast()
    }
    
    func peek() -> Element? {
        return items.last
    }
    
    var isEmpty: Bool {
        return items.isEmpty
    }
    
    var count: Int {
        return items.count
    }
}

// Generic queue implementation
struct Queue<Element> {
    var items: [Element] = []
    
    mutating func enqueue(_ item: Element) {
        items.append(item)
    }
    
    mutating func dequeue() -> Element? {
        guard !items.isEmpty else { return nil }
        return items.removeFirst()
    }
    
    func peek() -> Element? {
        return items.first
    }
    
    var isEmpty: Bool {
        return items.isEmpty
    }
    
    var count: Int {
        return items.count
    }
}

// Usage
var intStack = Stack<Int>()
intStack.push(1)
intStack.push(2)
intStack.push(3)

var stringQueue = Queue<String>()
stringQueue.enqueue("First")
stringQueue.enqueue("Second")
stringQueue.enqueue("Third")

print("Stack: \(intStack.items)")
print("Queue: \(stringQueue.items)")

if let popped = intStack.pop() {
    print("Popped: \(popped)")
}

if let dequeued = stringQueue.dequeue() {
    print("Dequeued: \(dequeued)")
}
```

#### Generic Type Constraints

```swift
// Generic type with constraints
struct SortedArray<Element: Comparable> {
    private var items: [Element] = []
    
    mutating func insert(_ item: Element) {
        items.append(item)
        items.sort()
    }
    
    func contains(_ item: Element) -> Bool {
        return items.contains(item)
    }
    
    var sortedItems: [Element] {
        return items
    }
}

// Generic type with protocol constraints
struct Cache<Key: Hashable, Value> {
    private var storage: [Key: Value] = [:]
    
    mutating func set(_ value: Value, for key: Key) {
        storage[key] = value
    }
    
    func get(_ key: Key) -> Value? {
        return storage[key]
    }
    
    mutating func remove(_ key: Key) {
        storage.removeValue(forKey: key)
    }
    
    var allKeys: [Key] {
        return Array(storage.keys)
    }
    
    var allValues: [Value] {
        return Array(storage.values)
    }
}

// Usage
var sortedNumbers = SortedArray<Int>()
sortedNumbers.insert(5)
sortedNumbers.insert(2)
sortedNumbers.insert(8)
sortedNumbers.insert(1)
print("Sorted: \(sortedNumbers.sortedItems)")

var userCache = Cache<String, User>()
userCache.set(User(id: "user1", name: "Alice", email: "alice@example.com"), for: "user1")
userCache.set(User(id: "user2", name: "Bob", email: "bob@example.com"), for: "user2")

if let user = userCache.get("user1") {
    print("Cached user: \(user.name)")
}
```

#### Generic Where Clauses

```swift
// Generic function with where clause
func allItemsMatch<T1, T2>(_ array1: [T1], _ array2: [T2], 
                           where predicate: (T1, T2) -> Bool) -> Bool {
    guard array1.count == array2.count else { return false }
    
    for i in 0..<array1.count {
        if !predicate(array1[i], array2[i]) {
            return false
        }
    }
    return true
}

// Generic type extension with where clause
extension Array where Element: Comparable {
    func isSorted() -> Bool {
        for i in 1..<count {
            if self[i] < self[i-1] {
                return false
            }
        }
        return true
    }
}

extension Array where Element == String {
    func longestString() -> String? {
        return self.max(by: { $0.count < $1.count })
    }
}

// Usage
let numbers1 = [1, 2, 3, 4, 5]
let numbers2 = [2, 4, 6, 8, 10]

let allEven = allItemsMatch(numbers1, numbers2) { $0 * 2 == $1 }
print("All numbers doubled: \(allEven)")

let sortedArray = [1, 2, 3, 4, 5]
let unsortedArray = [3, 1, 4, 1, 5]

print("Sorted array is sorted: \(sortedArray.isSorted())")
print("Unsorted array is sorted: \(unsortedArray.isSorted())")

let words = ["apple", "banana", "cherry", "dragonfruit"]
if let longest = words.longestString() {
    print("Longest word: \(longest)")
}
```

#### Generics in Practice

```swift
// Real-world example: Generic data processing pipeline
protocol DataProcessor {
    associatedtype Input
    associatedtype Output
    
    func process(_ input: Input) -> Output
}

protocol DataValidator {
    associatedtype Data
    
    func validate(_ data: Data) -> Bool
    var validationErrors: [String] { get }
}

// Generic pipeline that can work with any data type
struct ProcessingPipeline<Input, Output> {
    private let processors: [AnyProcessor<Input, Output>]
    
    init(processors: [AnyProcessor<Input, Output>]) {
        self.processors = processors
    }
    
    func process(_ input: Input) -> Output {
        var current: Any = input
        
        for processor in processors {
            current = processor.process(current)
        }
        
        return current as! Output
    }
}

// Type-erased processor wrapper
struct AnyProcessor<Input, Output> {
    private let _process: (Input) -> Output
    
    init<T: DataProcessor>(_ processor: T) where T.Input == Input, T.Output == Output {
        self._process = processor.process
    }
    
    func process(_ input: Input) -> Output {
        return _process(input)
    }
}

// Concrete implementations
struct StringProcessor: DataProcessor {
    typealias Input = String
    typealias Output = String
    
    func process(_ input: String) -> String {
        return input.trimmingCharacters(in: .whitespacesAndNewlines)
            .lowercased()
            .capitalized
    }
}

struct NumberProcessor: DataProcessor {
    typealias Input = String
    typealias Output = Double
    
    func process(_ input: String) -> Double {
        return Double(input) ?? 0.0
    }
}

struct StringValidator: DataValidator {
    typealias Data = String
    
    private var _validationErrors: [String] = []
    
    var validationErrors: [String] {
        return _validationErrors
    }
    
    func validate(_ data: String) -> Bool {
        _validationErrors.removeAll()
        
        if data.isEmpty {
            _validationErrors.append("String cannot be empty")
        }
        
        if data.count < 3 {
            _validationErrors.append("String must be at least 3 characters")
        }
        
        return _validationErrors.isEmpty
    }
}

// Usage
let stringProcessor = StringProcessor()
let numberProcessor = NumberProcessor()

let processors: [AnyProcessor<String, Any>] = [
    AnyProcessor(stringProcessor),
    AnyProcessor(numberProcessor)
]

let pipeline = ProcessingPipeline(processors: processors)
let result = pipeline.process("  42.5  ")

print("Processed result: \(result)")

let validator = StringValidator()
if validator.validate("ab") {
    print("Validation passed")
} else {
    print("Validation failed: \(validator.validationErrors)")
}
```

### Best Practices for Generics

1. **Use descriptive type parameter names** - `Element` is better than `T` for collections
2. **Apply constraints judiciously** - Only constrain types when necessary
3. **Use where clauses for complex constraints** - Keep constraints readable
4. **Consider type erasure** - Use `AnyProcessor` pattern for complex generic protocols
5. **Test with multiple types** - Ensure generics work with various type combinations
6. **Document generic requirements** - Make it clear what types can be used
7. **Use generics for code reuse** - Don't duplicate code for different types

---

## Opaque and Boxed Protocol Types

Swift provides two ways to work with protocols as return types: opaque types and boxed protocol types.

### Understanding Opaque Types

**Why This Matters:** Opaque types provide type safety while hiding implementation details, enabling you to return different concrete types that conform to the same protocol.

#### Opaque Types with `some`

```swift
protocol Shape {
    var area: Double { get }
    func draw()
}

struct Circle: Shape {
    let radius: Double
    
    var area: Double {
        return Double.pi * radius * radius
    }
    
    func draw() {
        print("Drawing circle with radius \(radius)")
    }
}

struct Rectangle: Shape {
    let width: Double
    let height: Double
    
    var area: Double {
        return width * height
    }
    
    func draw() {
        print("Drawing rectangle \(width) x \(height)")
    }
}

// Function returning opaque type
func createShape(isCircle: Bool) -> some Shape {
    if isCircle {
        return Circle(radius: 5)
    } else {
        return Rectangle(width: 10, height: 5)
    }
}

// Function returning specific opaque type
func createCircle() -> some Shape {
    return Circle(radius: 10)
}

// Usage
let shape1 = createShape(isCircle: true)
let shape2 = createShape(isCircle: false)

shape1.draw()
shape2.draw()

print("Shape 1 area: \(shape1.area)")
print("Shape 2 area: \(shape2.area)")
```

#### Opaque Types with Associated Types

```swift
protocol Container {
    associatedtype Item
    var items: [Item] { get }
    func add(_ item: Item)
}

struct IntContainer: Container {
    typealias Item = Int
    var items: [Int] = []
    
    func add(_ item: Int) {
        items.append(item)
    }
}

struct StringContainer: Container {
    typealias Item = String
    var items: [String] = []
    
    func add(_ item: String) {
        items.append(item)
    }
}

// Function returning opaque type with associated type
func createContainer<T>(for type: T.Type) -> some Container where T: ExpressibleByIntegerLiteral {
    if T.self == Int.self {
        return IntContainer()
    } else {
        return IntContainer() // Simplified for example
    }
}

// Usage
let intContainer = createContainer(for: Int.self)
intContainer.add(42)
intContainer.add(100)

print("Int container items: \(intContainer.items)")
```

#### Boxed Protocol Types with `any`

```swift
// Function returning boxed protocol type
func createAnyShape(isCircle: Bool) -> any Shape {
    if isCircle {
        return Circle(radius: 5)
    } else {
        return Rectangle(width: 10, height: 5)
    }
}

// Function returning array of boxed protocol types
func createShapeArray() -> [any Shape] {
    return [
        Circle(radius: 3),
        Rectangle(width: 4, height: 6),
        Circle(radius: 7)
    ]
}

// Usage
let anyShape1 = createAnyShape(isCircle: true)
let anyShape2 = createAnyShape(isCircle: false)

anyShape1.draw()
anyShape2.draw()

let shapeArray = createShapeArray()
for shape in shapeArray {
    shape.draw()
    print("Area: \(shape.area)")
}
```

#### Performance Considerations

```swift
// Performance comparison
protocol Processor {
    associatedtype Input
    associatedtype Output
    
    func process(_ input: Input) -> Output
}

struct FastProcessor: Processor {
    typealias Input = Int
    typealias Output = String
    
    func process(_ input: Int) -> String {
        return "Processed: \(input)"
    }
}

struct SlowProcessor: Processor {
    typealias Input = Int
    typealias Output = String
    
    func process(_ input: Int) -> String {
        // Simulate slow processing
        Thread.sleep(forTimeInterval: 0.1)
        return "Slowly processed: \(input)"
    }
}

// Opaque type - compiler knows the exact type at compile time
func createFastProcessor() -> some Processor {
    return FastProcessor()
}

// Boxed protocol type - runtime type checking
func createAnyProcessor(isFast: Bool) -> any Processor {
    if isFast {
        return FastProcessor()
    } else {
        return SlowProcessor()
    }
}

// Usage
let fastProcessor = createFastProcessor()
let anyProcessor = createAnyProcessor(isFast: false)

// Opaque type is more performant
let result1 = fastProcessor.process(42)

// Boxed protocol type has runtime overhead
let result2 = anyProcessor.process(42)
```

#### Real-World Example

```swift
// Real-world example: Plugin system
protocol Plugin {
    associatedtype Configuration
    associatedtype Result
    
    func configure(_ config: Configuration)
    func execute() -> Result
}

struct ImagePlugin: Plugin {
    typealias Configuration = ImageConfig
    typealias Result = ProcessedImage
    
    private var config: ImageConfig?
    
    func configure(_ config: ImageConfig) {
        self.config = config
    }
    
    func execute() -> ProcessedImage {
        guard let config = config else {
            return ProcessedImage(data: Data(), format: .unknown)
        }
        
        // Process image based on configuration
        let processedData = Data() // Simplified
        let format: ImageFormat = config.quality == .high ? .png : .jpeg
        
        return ProcessedImage(data: processedData, format: format)
    }
}

struct TextPlugin: Plugin {
    typealias Configuration = TextConfig
    typealias Result = ProcessedText
    
    private var config: TextConfig?
    
    func configure(_ config: TextConfig) {
        self.config = config
    }
    
    func execute() -> ProcessedText {
        guard let config = config else {
            return ProcessedText(text: "", language: .unknown)
        }
        
        // Process text based on configuration
        let processedText = config.text.uppercased() // Simplified
        let language: Language = config.language
        
        return ProcessedText(text: processedText, language: language)
    }
}

// Configuration types
struct ImageConfig {
    let quality: Quality
    let format: ImageFormat
    
    enum Quality {
        case low, medium, high
    }
    
    enum ImageFormat {
        case jpeg, png, heic
    }
}

struct TextConfig {
    let text: String
    let language: Language
    
    enum Language {
        case english, spanish, french, unknown
    }
}

// Result types
struct ProcessedImage {
    let data: Data
    let format: ImageFormat
    
    enum ImageFormat {
        case jpeg, png, heic, unknown
    }
}

struct ProcessedText {
    let text: String
    let language: Language
    
    enum Language {
        case english, spanish, french, unknown
    }
}

// Plugin manager using opaque types
class PluginManager {
    // Opaque type - more performant, type-safe
    func getImagePlugin() -> some Plugin {
        return ImagePlugin()
    }
    
    // Boxed protocol type - flexible, runtime type checking
    func getPlugin(for type: String) -> any Plugin {
        switch type {
        case "image":
            return ImagePlugin()
        case "text":
            return TextPlugin()
        default:
            return ImagePlugin() // Default fallback
        }
    }
}

// Usage
let manager = PluginManager()

// Using opaque type
let imagePlugin = manager.getImagePlugin()
imagePlugin.configure(ImageConfig(quality: .high, format: .png))
let processedImage = imagePlugin.execute()

// Using boxed protocol type
let anyPlugin = manager.getPlugin(for: "text")
anyPlugin.configure(TextConfig(text: "Hello World", language: .english))
let processedText = anyPlugin.execute()
```

### Best Practices for Opaque and Boxed Types

1. **Use opaque types (`some`) for performance** - When you know the exact type at compile time
2. **Use boxed types (`any`) for flexibility** - When you need runtime type flexibility
3. **Consider performance implications** - Opaque types are more performant
4. **Use opaque types in public APIs** - Hide implementation details
5. **Use boxed types for collections** - When you need to store different types
6. **Understand type erasure** - Know when types are erased at runtime
7. **Test both approaches** - Ensure your code works with both opaque and boxed types

---

## Automatic Reference Counting

Automatic Reference Counting (ARC) is Swift's memory management system that automatically tracks and manages your app's memory usage.

### Understanding ARC

**Why This Matters:** ARC prevents memory leaks and crashes by automatically freeing memory when objects are no longer needed. Understanding how it works helps you write more efficient and stable code.

#### How ARC Works

```swift
class Person {
    let name: String
    var apartment: Apartment?
    
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

class Apartment {
    let unit: String
    var tenant: Person?
    
    init(unit: String) {
        self.unit = unit
        print("Apartment \(unit) is being initialized")
    }
    
    deinit {
        print("Apartment \(unit) is being deinitialized")
    }
}

// ARC automatically manages memory
var person1: Person?
var person2: Person?
var person3: Person?

person1 = Person(name: "John")      // Reference count: 1
person2 = person1                   // Reference count: 2
person3 = person1                   // Reference count: 3

person1 = nil                       // Reference count: 2
person2 = nil                       // Reference count: 1
person3 = nil                       // Reference count: 0, deinit called
```

#### Strong Reference Cycles

```swift
// Strong reference cycle - causes memory leak
class Person {
    let name: String
    var apartment: Apartment?
    
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

class Apartment {
    let unit: String
    var tenant: Person?
    
    init(unit: String) {
        self.unit = unit
        print("Apartment \(unit) is being initialized")
    }
    
    deinit {
        print("Apartment \(unit) is being deinitialized")
    }
}

// Creating a strong reference cycle
var john: Person?
var unit4A: Apartment?

john = Person(name: "John")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A!.tenant = john

// Even when we set these to nil, deinit is never called
john = nil
unit4A = nil

// Memory leak! Objects are never deallocated
```

#### Resolving Strong Reference Cycles

```swift
// Solution 1: Weak references
class Person {
    let name: String
    weak var apartment: Apartment?  // Weak reference
    
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

class Apartment {
    let unit: String
    weak var tenant: Person?        // Weak reference
    
    init(unit: String) {
        self.unit = unit
        print("Apartment \(unit) is being initialized")
    }
    
    deinit {
        print("Apartment \(unit) is being deinitialized")
    }
}

// Solution 2: Unowned references
class CreditCard {
    let number: String
    unowned let customer: Person    // Unowned reference
    
    init(number: String, customer: Person) {
        self.number = number
        self.customer = customer
        print("CreditCard \(number) is being initialized")
    }
    
    deinit {
        print("CreditCard \(number) is being deinitialized")
    }
}

class Person {
    let name: String
    var creditCard: CreditCard?
    
    init(name: String) {
        self.name = name
        print("\(name) is being initialized")
    }
    
    deinit {
        print("\(name) is being deinitialized")
    }
}

// Usage
var person: Person?
person = Person(name: "John")
person!.creditCard = CreditCard(number: "1234-5678-9012-3456", customer: person!)

person = nil  // Both objects are deallocated
```

#### Closures and Strong Reference Cycles

```swift
class HTMLElement {
    let name: String
    let text: String
    
    lazy var asHTML: () -> String = {
        // Strong reference cycle - self captures self
        return "<\(self.name)>\(self.text)</\(self.name)>"
    }
    
    init(name: String, text: String) {
        self.name = name
        self.text = text
        print("\(name) element is being initialized")
    }
    
    deinit {
        print("\(name) element is being deinitialized")
    }
}

// Solution: Capture lists
class HTMLElement {
    let name: String
    let text: String
    
    lazy var asHTML: () -> String = { [weak self] in
        // Weak capture prevents strong reference cycle
        guard let self = self else { return "" }
        return "<\(self.name)>\(self.text)</\(self.name)>"
    }
    
    // Alternative with unowned
    lazy var asHTMLUnowned: () -> String = { [unowned self] in
        return "<\(self.name)>\(self.text)</\(self.name)>"
    }
    
    init(name: String, text: String) {
        self.name = name
        self.text = text
        print("\(name) element is being initialized")
    }
    
    deinit {
        print("\(name) element is being deinitialized")
    }
}

// Usage
var paragraph: HTMLElement? = HTMLElement(name: "p", text: "Hello, world!")
print(paragraph!.asHTML())

paragraph = nil  // Now deinit is called
```

#### ARC in Practice

```swift
// Real-world example: Network manager with ARC
class NetworkManager {
    static let shared = NetworkManager()
    private var activeRequests: [String: URLSessionDataTask] = [:]
    
    private init() {}
    
    func fetchData(from url: URL, completion: @escaping (Data?, Error?) -> Void) -> String {
        let requestId = UUID().uuidString
        
        let task = URLSession.shared.dataTask(with: url) { [weak self] data, response, error in
            // Weak capture prevents retain cycle
            defer {
                DispatchQueue.main.async {
                    self?.activeRequests.removeValue(forKey: requestId)
                }
            }
            
            completion(data, error)
        }
        
        activeRequests[requestId] = task
        task.resume()
        
        return requestId
    }
    
    func cancelRequest(with id: String) {
        activeRequests[id]?.cancel()
        activeRequests.removeValue(forKey: id)
    }
}

// Usage
let manager = NetworkManager.shared
let requestId = manager.fetchData(from: URL(string: "https://api.example.com")!) { data, error in
    if let data = data {
        print("Received \(data.count) bytes")
    } else if let error = error {
        print("Error: \(error)")
    }
}

// Cancel request if needed
// manager.cancelRequest(with: requestId)
```

### Best Practices for ARC

1. **Use weak references** - When the referenced object can be nil
2. **Use unowned references** - When you're certain the referenced object won't be nil
3. **Use capture lists** - In closures to prevent retain cycles
4. **Avoid strong reference cycles** - Be careful with bidirectional relationships
5. **Monitor memory usage** - Use Instruments to detect memory leaks
6. **Test deallocation** - Ensure objects are properly deallocated
7. **Use value types** - Structs and enums don't have reference counting issues

---

## Memory Safety

Swift's memory safety features prevent common programming errors that can lead to crashes and security vulnerabilities.

### Understanding Memory Safety

**Why This Matters:** Memory safety prevents crashes, data corruption, and security vulnerabilities by ensuring proper access to memory locations.

#### Conflicting Access to Memory

```swift
// Example of conflicting access
var stepSize = 1

func increment(_ number: inout Int) {
    number += stepSize
}

// This causes a runtime error - conflicting access
// increment(&stepSize)  // Error: conflicting access

// Solution: Use a local copy
func increment(_ number: inout Int) {
    let copyOfStepSize = stepSize
    number += copyOfStepSize
}

increment(&stepSize)  // Now works correctly
```

#### Conflicting Access to Properties

```swift
struct Player {
    var name: String
    var health: Int
    var energy: Int
    
    mutating func restoreHealth(by amount: Int) {
        health += amount
    }
    
    mutating func restoreEnergy(by amount: Int) {
        energy += amount
    }
}

var player = Player(name: "Hero", health: 100, energy: 100)

// This would cause a runtime error
// player.restoreHealth(by: 20)
// player.restoreEnergy(by: 30)

// Solution: Use a single mutating method
extension Player {
    mutating func restore(health: Int, energy: Int) {
        self.health += health
        self.energy += energy
    }
}

player.restore(health: 20, energy: 30)
print("Health: \(player.health), Energy: \(player.energy)")
```

#### Conflicting Access to Arrays

```swift
var numbers = [1, 2, 3, 4, 5]

// This would cause a runtime error
// numbers[0] = numbers[1] + numbers[2]

// Solution: Use intermediate variables
let sum = numbers[1] + numbers[2]
numbers[0] = sum

// Or use a single operation
numbers[0] = numbers[1] + numbers[2]  // This works because it's a single operation
```

#### Memory Safety in Practice

```swift
// Real-world example: Safe data processing
class DataProcessor {
    private var data: [Int] = []
    private let queue = DispatchQueue(label: "data.processing", attributes: .concurrent)
    
    func addData(_ value: Int) {
        queue.async(flags: .barrier) { [weak self] in
            self?.data.append(value)
        }
    }
    
    func processData() -> [Int] {
        return queue.sync {
            return data.map { $0 * 2 }
        }
    }
    
    func clearData() {
        queue.async(flags: .barrier) { [weak self] in
            self?.data.removeAll()
        }
    }
}

// Usage
let processor = DataProcessor()

// Add data from multiple threads safely
DispatchQueue.concurrentPerform(iterations: 10) { index in
    processor.addData(index)
}

// Process data safely
let processed = processor.processData()
print("Processed data: \(processed)")

// Clear data safely
processor.clearData()
```

### Best Practices for Memory Safety

1. **Avoid conflicting access** - Don't access the same memory location from multiple places simultaneously
2. **Use proper synchronization** - Use queues, locks, or actors for concurrent access
3. **Be careful with inout parameters** - Ensure no conflicting access to the same variable
4. **Use value types when possible** - Structs and enums provide better memory safety
5. **Test concurrent code** - Ensure your code works correctly under concurrent access
6. **Use Swift's built-in safety features** - Let the compiler catch potential issues
7. **Document thread safety** - Make it clear which methods are thread-safe

---

## Access Control

Access control restricts access to parts of your code from code in other source files and modules.

### Understanding Access Control

**Why This Matters:** Access control helps you hide implementation details, create clean APIs, and prevent misuse of your code.

#### Access Levels

```swift
// Open - highest access level
open class OpenClass {
    open var openProperty: String = "Open"
    open func openMethod() {}
}

// Public - accessible from any module
public class PublicClass {
    public var publicProperty: String = "Public"
    public func publicMethod() {}
}

// Internal - default access level
internal class InternalClass {
    internal var internalProperty: String = "Internal"
    internal func internalMethod() {}
}

// File-private - accessible only within the same file
fileprivate class FilePrivateClass {
    fileprivate var filePrivateProperty: String = "File Private"
    fileprivate func filePrivateMethod() {}
}

// Private - lowest access level
private class PrivateClass {
    private var privateProperty: String = "Private"
    private func privateMethod() {}
}
```

#### Access Control for Properties

```swift
public struct BankAccount {
    // Public property - accessible from anywhere
    public let accountNumber: String
    
    // Internal property - accessible within the module
    internal var balance: Decimal
    
    // Private property - accessible only within the struct
    private var transactions: [Transaction] = []
    
    // File-private property - accessible within the file
    fileprivate var lastTransactionDate: Date?
    
    public init(accountNumber: String, initialBalance: Decimal) {
        self.accountNumber = accountNumber
        self.balance = initialBalance
    }
    
    // Public method
    public func getBalance() -> Decimal {
        return balance
    }
    
    // Internal method
    internal func deposit(amount: Decimal) {
        balance += amount
        addTransaction(type: .deposit, amount: amount)
    }
    
    // Private method
    private func addTransaction(type: TransactionType, amount: Decimal) {
        let transaction = Transaction(type: type, amount: amount, timestamp: Date())
        transactions.append(transaction)
        lastTransactionDate = transaction.timestamp
    }
}

// Private supporting types
private enum TransactionType {
    case deposit, withdrawal
}

private struct Transaction {
    let type: TransactionType
    let amount: Decimal
    let timestamp: Date
}
```

#### Access Control for Extensions

```swift
// Extending with access control
extension BankAccount {
    // Internal method in extension
    internal func withdraw(amount: Decimal) -> Bool {
        guard amount <= balance else { return false }
        
        balance -= amount
        addTransaction(type: .withdrawal, amount: amount)
        return true
    }
    
    // Private computed property in extension
    private var isOverdrawn: Bool {
        return balance < 0
    }
}

// Private extension
private extension BankAccount {
    func validateTransaction(_ amount: Decimal) -> Bool {
        return amount > 0 && amount <= 10000
    }
}
```

#### Access Control for Protocols

```swift
// Public protocol
public protocol Bankable {
    var accountNumber: String { get }
    var balance: Decimal { get }
    
    func deposit(amount: Decimal)
    func withdraw(amount: Decimal) -> Bool
}

// Internal protocol
internal protocol TransactionLoggable {
    var transactionCount: Int { get }
    func getTransactionHistory() -> [String]
}

// Private protocol
private protocol InternalBanking {
    func calculateInterest() -> Decimal
    func applyFees()
}

// Implementing protocols with different access levels
public class SavingsAccount: Bankable, TransactionLoggable {
    public let accountNumber: String
    internal var balance: Decimal
    private var transactions: [String] = []
    
    public init(accountNumber: String, initialBalance: Decimal) {
        self.accountNumber = accountNumber
        self.balance = initialBalance
    }
    
    public func deposit(amount: Decimal) {
        balance += amount
        transactions.append("Deposit: \(amount)")
    }
    
    public func withdraw(amount: Decimal) -> Bool {
        guard amount <= balance else { return false }
        
        balance -= amount
        transactions.append("Withdrawal: \(amount)")
        return true
    }
    
    internal var transactionCount: Int {
        return transactions.count
    }
    
    internal func getTransactionHistory() -> [String] {
        return transactions
    }
}
```

#### Access Control in Practice

```swift
// Real-world example: Framework design
public struct UserManager {
    // Public interface
    public static let shared = UserManager()
    
    // Private implementation
    private var users: [String: User] = [:]
    private let queue = DispatchQueue(label: "user.manager", attributes: .concurrent)
    
    private init() {}
    
    // Public methods
    public func createUser(name: String, email: String) -> User? {
        guard isValidEmail(email) else { return nil }
        
        let user = User(name: name, email: email)
        
        queue.async(flags: .barrier) { [weak self] in
            self?.users[email] = user
        }
        
        return user
    }
    
    public func getUser(email: String) -> User? {
        return queue.sync {
            return users[email]
        }
    }
    
    public func updateUser(_ user: User) -> Bool {
        guard users[user.email] != nil else { return false }
        
        queue.async(flags: .barrier) { [weak self] in
            self?.users[user.email] = user
        }
        
        return true
    }
    
    // Internal validation method
    internal func isValidEmail(_ email: String) -> Bool {
        let emailRegex = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"
        let emailPredicate = NSPredicate(format: "SELF MATCHES %@", emailRegex)
        return emailPredicate.evaluate(with: email)
    }
}

// Public model
public struct User {
    public let name: String
    public let email: String
    internal var lastLoginDate: Date?
    
    public init(name: String, email: String) {
        self.name = name
        self.email = email
    }
}

// Usage from another module
let manager = UserManager.shared

if let user = manager.createUser(name: "John Doe", email: "john@example.com") {
    print("Created user: \(user.name)")
    
    if let retrievedUser = manager.getUser(email: "john@example.com") {
        print("Retrieved user: \(retrievedUser.name)")
    }
}
```

### Best Practices for Access Control

1. **Start with private** - Make everything private by default
2. **Expose only what's necessary** - Keep your public API minimal
3. **Use internal for module access** - Allow access within your module
4. **Use fileprivate for file access** - When you need access across types in the same file
5. **Document public APIs** - Make it clear how to use your public interface
6. **Consider framework design** - Plan access levels for reusable code
7. **Test access levels** - Ensure your access control works as expected

---

## Advanced Operators

Swift provides advanced operators beyond the basic arithmetic and logical operators.

### Understanding Advanced Operators

**Why This Matters:** Advanced operators provide efficient ways to perform bitwise operations, overflow handling, and custom operator definitions.

#### Bitwise Operators

```swift
// Bitwise NOT (~)
let initialBits: UInt8 = 0b00001111
let invertedBits = ~initialBits
print("Original: \(String(initialBits, radix: 2))")
print("Inverted: \(String(invertedBits, radix: 2))")

// Bitwise AND (&)
let firstSixBits: UInt8 = 0b11111100
let lastSixBits: UInt8 = 0b00111111
let middleFourBits = firstSixBits & lastSixBits
print("Middle four: \(String(middleFourBits, radix: 2))")

// Bitwise OR (|)
let someBits: UInt8 = 0b10110010
let moreBits: UInt8 = 0b01011110
let combinedBits = someBits | moreBits
print("Combined: \(String(combinedBits, radix: 2))")

// Bitwise XOR (^)
let inputBits: UInt8 = 0b00010100
let toggleBits: UInt8 = 0b00000100
let outputBits = inputBits ^ toggleBits
print("Toggled: \(String(outputBits, radix: 2))")

// Bitwise left shift (<<)
let shiftBits: UInt8 = 4
let shift1 = shiftBits << 1
let shift2 = shiftBits << 2
let shift6 = shiftBits << 6
print("Original: \(shiftBits)")
print("Shift 1: \(shift1)")
print("Shift 2: \(shift2)")
print("Shift 6: \(shift6)")

// Bitwise right shift (>>)
let originalBits: UInt8 = 0b11111111
let rightShift1 = originalBits >> 1
let rightShift2 = originalBits >> 2
print("Original: \(String(originalBits, radix: 2))")
print("Right shift 1: \(String(rightShift1, radix: 2))")
print("Right shift 2: \(String(rightShift2, radix: 2))")
```

#### Overflow Operators

```swift
// Overflow addition
var unsignedOverflow = UInt8.max
print("Before overflow: \(unsignedOverflow)")
unsignedOverflow = unsignedOverflow &+ 1
print("After overflow: \(unsignedOverflow)")

// Overflow subtraction
var unsignedUnderflow = UInt8.min
print("Before underflow: \(unsignedUnderflow)")
unsignedUnderflow = unsignedUnderflow &- 1
print("After underflow: \(unsignedUnderflow)")

// Overflow multiplication
var multiplicationOverflow = UInt8.max
print("Before multiplication overflow: \(multiplicationOverflow)")
multiplicationOverflow = multiplicationOverflow &* 2
print("After multiplication overflow: \(multiplicationOverflow)")

// Signed integer overflow
var signedOverflow = Int8.max
print("Before signed overflow: \(signedOverflow)")
signedOverflow = signedOverflow &+ 1
print("After signed overflow: \(signedOverflow)")
```

#### Custom Operators

```swift
// Custom prefix operator
prefix operator +++

prefix func +++(value: inout Int) -> Int {
    value += 2
    return value
}

var number = 5
let result = +++number
print("Number: \(number), Result: \(result)")

// Custom postfix operator
postfix operator ---

postfix func ---(value: inout Int) -> Int {
    let currentValue = value
    value -= 2
    return currentValue
}

var anotherNumber = 10
let postfixResult = anotherNumber---
print("Another number: \(anotherNumber), Postfix result: \(postfixResult)")

// Custom infix operator
infix operator +-: AdditionPrecedence

func +-(left: Int, right: Int) -> Int {
    return left + right - 1
}

let infixResult = 5 +- 3
print("5 +- 3 = \(infixResult)")

// Custom precedence group
precedencegroup CustomPrecedence {
    higherThan: AdditionPrecedence
    lowerThan: MultiplicationPrecedence
    associativity: left
}

infix operator **: CustomPrecedence

func **(left: Int, right: Int) -> Int {
    return Int(pow(Double(left), Double(right)))
}

let powerResult = 2 ** 3 ** 2
print("2 ** 3 ** 2 = \(powerResult)")
```

#### Advanced Operators in Practice

```swift
// Real-world example: Bit manipulation for flags
struct UserPermissions {
    private var rawValue: UInt32
    
    static let read = UserPermissions(rawValue: 1 << 0)      // 0001
    static let write = UserPermissions(rawValue: 1 << 1)     // 0010
    static let execute = UserPermissions(rawValue: 1 << 2)   // 0100
    static let delete = UserPermissions(rawValue: 1 << 3)    // 1000
    
    init(rawValue: UInt32) {
        self.rawValue = rawValue
    }
    
    // Custom operators for permission manipulation
    static func |(left: UserPermissions, right: UserPermissions) -> UserPermissions {
        return UserPermissions(rawValue: left.rawValue | right.rawValue)
    }
    
    static func &(left: UserPermissions, right: UserPermissions) -> UserPermissions {
        return UserPermissions(rawValue: left.rawValue & right.rawValue)
    }
    
    static func ^(left: UserPermissions, right: UserPermissions) -> UserPermissions {
        return UserPermissions(rawValue: left.rawValue ^ right.rawValue)
    }
    
    static prefix func ~(permissions: UserPermissions) -> UserPermissions {
        return UserPermissions(rawValue: ~permissions.rawValue)
    }
    
    // Check if permissions contain specific permission
    func contains(_ permission: UserPermissions) -> Bool {
        return (self & permission).rawValue != 0
    }
    
    // Add permissions
    mutating func add(_ permission: UserPermissions) {
        rawValue |= permission.rawValue
    }
    
    // Remove permissions
    mutating func remove(_ permission: UserPermissions) {
        rawValue &= ~permission.rawValue
    }
    
    // Toggle permissions
    mutating func toggle(_ permission: UserPermissions) {
        rawValue ^= permission.rawValue
    }
}

// Usage
var userPermissions = UserPermissions.read | UserPermissions.write
print("User has read and write permissions")

if userPermissions.contains(.read) {
    print("User can read")
}

if userPermissions.contains(.execute) {
    print("User can execute")
} else {
    print("User cannot execute")
}

userPermissions.add(.execute)
print("Added execute permission")

userPermissions.toggle(.write)
print("Toggled write permission")

// Custom operator for combining permissions
infix operator +: AdditionPrecedence

func +(left: UserPermissions, right: UserPermissions) -> UserPermissions {
    return left | right
}

let adminPermissions = UserPermissions.read + UserPermissions.write + UserPermissions.execute + UserPermissions.delete
print("Admin has all permissions")
```

### Best Practices for Advanced Operators

1. **Use bitwise operators for flags** - Efficient way to handle multiple boolean states
2. **Use overflow operators when needed** - Handle cases where overflow is expected
3. **Create custom operators sparingly** - Only when they improve readability
4. **Follow Swift naming conventions** - Use descriptive names for custom operators
5. **Document custom operators** - Make it clear what your operators do
6. **Test edge cases** - Ensure operators work correctly with boundary values
7. **Consider readability** - Sometimes explicit functions are clearer than custom operators