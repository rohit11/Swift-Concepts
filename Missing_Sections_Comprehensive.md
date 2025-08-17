# Comprehensive Missing Sections for README.md

This file contains in-depth explanations of all the missing Swift concepts with comprehensive examples, technical details, and real-world applications.

## Result Type - Deep Dive

The `Result<Success, Failure>` type is a fundamental modern Swift feature that represents the outcome of an operation that can either succeed or fail. Unlike optionals which only indicate presence/absence, or throwing functions which require try-catch blocks, Result provides a type-safe, composable way to handle both success and failure cases.

### Why Result Type Matters

**Traditional Error Handling Problems:**
```swift
// ❌ Old way - Multiple approaches mixed together
func fetchUser(id: String) -> User? { /* ... */ }  // Optional - no error info
func saveUser(_ user: User) throws { /* ... */ }   // Throwing - requires try-catch
func validateUser(_ user: User) -> Bool { /* ... */ } // Boolean - no details

// ❌ Inconsistent error handling
let user = fetchUser(id: "123")
if let user = user {
    do {
        try saveUser(user)
        if validateUser(user) {
            print("Success!")
        } else {
            print("Validation failed") // No details about what failed
        }
    } catch {
        print("Save failed: \(error)") // Generic error handling
    }
} else {
    print("User not found") // No details about why
}
```

**Result Type Solution:**
```swift
// ✅ Consistent, type-safe error handling
enum UserError: Error, LocalizedError {
    case notFound(String)
    case invalidData(String)
    case networkError(NetworkError)
    case validationFailed([ValidationError])
    
    var errorDescription: String? {
        switch self {
        case .notFound(let id):
            return "User with ID '\(id)' not found"
        case .invalidData(let reason):
            return "Invalid user data: \(reason)"
        case .networkError(let networkError):
            return "Network error: \(networkError.localizedDescription)"
        case .validationFailed(let errors):
            return "Validation failed: \(errors.map(\.message).joined(separator: ", "))"
        }
    }
}

func fetchUser(id: String) -> Result<User, UserError> {
    // Implementation with detailed error information
}

func saveUser(_ user: User) -> Result<Void, UserError> {
    // Implementation with detailed error information
}

func validateUser(_ user: User) -> Result<Void, UserError> {
    // Implementation with detailed error information
}
```

### Result Type Internals and Implementation

**Understanding the Result Enum:**
```swift
// This is what Result actually looks like internally
enum Result<Success, Failure: Error> {
    case success(Success)
    case failure(Failure)
    
    // Computed properties for easy access
    var value: Success? {
        switch self {
        case .success(let value):
            return value
        case .failure:
            return nil
        }
    }
    
    var error: Failure? {
        switch self {
        case .success:
            return nil
        case .failure(let error):
            return error
        }
    }
    
    // Type-safe boolean checks
    var isSuccess: Bool {
        if case .success = self { return true }
        return false
    }
    
    var isFailure: Bool {
        if case .failure = self { return true }
        return false
    }
}
```

**Advanced Result Methods and Transformations:**
```swift
extension Result {
    // Transform success values
    func map<NewSuccess>(_ transform: (Success) -> NewSuccess) -> Result<NewSuccess, Failure> {
        switch self {
        case .success(let value):
            return .success(transform(value))
        case .failure(let error):
            return .failure(error)
        }
    }
    
    // Transform failure values
    func mapError<NewFailure: Error>(_ transform: (Failure) -> NewFailure) -> Result<Success, NewFailure> {
        switch self {
        case .success(let value):
            return .success(value)
        case .failure(let error):
            return .failure(transform(error))
        }
    }
    
    // Chain operations that can fail
    func flatMap<NewSuccess>(_ transform: (Success) -> Result<NewSuccess, Failure>) -> Result<NewSuccess, Failure> {
        switch self {
        case .success(let value):
            return transform(value)
        case .failure(let error):
            return .failure(error)
        }
    }
    
    // Recover from failures
    func recover(_ handler: (Failure) -> Success) -> Result<Success, Never> {
        switch self {
        case .success(let value):
            return .success(value)
        case .failure(let error):
            return .success(handler(error))
        }
    }
    
    // Recover with another operation
    func recoverWith(_ handler: (Failure) -> Result<Success, Failure>) -> Result<Success, Failure> {
        switch self {
        case .success(let value):
            return .success(value)
        case .failure(let error):
            return handler(error)
        }
    }
}
```

### Real-World Result Type Patterns

**Pattern 1: API Response Handling with Detailed Error Types**
```swift
// Comprehensive API error handling
enum APIError: Error, LocalizedError {
    case invalidURL
    case noData
    case decodingError(DecodingError)
    case serverError(HTTPStatus, String)
    case networkError(NetworkError)
    case authenticationError(String)
    case rateLimitExceeded(TimeInterval)
    
    var errorDescription: String? {
        switch self {
        case .invalidURL:
            return "Invalid URL provided"
        case .noData:
            return "No data received from server"
        case .decodingError(let decodingError):
            return "Failed to decode response: \(decodingError.localizedDescription)"
        case .serverError(let status, let message):
            return "Server error \(status.rawValue): \(message)"
        case .networkError(let networkError):
            return "Network error: \(networkError.localizedDescription)"
        case .authenticationError(let reason):
            return "Authentication failed: \(reason)"
        case .rateLimitExceeded(let retryAfter):
            return "Rate limit exceeded. Try again in \(Int(retryAfter)) seconds"
        }
    }
    
    var recoverySuggestion: String? {
        switch self {
        case .invalidURL:
            return "Check the URL format and try again"
        case .noData:
            return "Check your internet connection and try again"
        case .decodingError:
            return "The server response format has changed. Please update the app"
        case .serverError(let status, _):
            switch status {
            case .internalServerError:
                return "Server is experiencing issues. Please try again later"
            case .serviceUnavailable:
                return "Service is temporarily unavailable. Please try again later"
            default:
                return "Please try again later"
            }
        case .networkError:
            return "Check your internet connection and try again"
        case .authenticationError:
            return "Please log in again"
        case .rateLimitExceeded:
            return "You've made too many requests. Please wait before trying again"
        }
    }
}

enum HTTPStatus: Int {
    case ok = 200
    case created = 201
    case badRequest = 400
    case unauthorized = 401
    case forbidden = 403
    case notFound = 404
    case internalServerError = 500
    case serviceUnavailable = 503
}

class APIClient {
    private let session: URLSession
    private let baseURL: URL
    private let authToken: String?
    
    init(session: URLSession = .shared, baseURL: URL, authToken: String? = nil) {
        self.session = session
        self.baseURL = baseURL
        self.authToken = authToken
    }
    
    func request<T: Codable>(
        endpoint: String,
        method: HTTPMethod = .GET,
        body: Data? = nil,
        headers: [String: String] = [:]
    ) async -> Result<T, APIError> {
        
        // Build URL
        guard let url = URL(string: endpoint, relativeTo: baseURL) else {
            return .failure(.invalidURL)
        }
        
        // Build request
        var request = URLRequest(url: url)
        request.httpMethod = method.rawValue
        request.httpBody = body
        
        // Add headers
        var allHeaders = headers
        allHeaders["Content-Type"] = "application/json"
        if let authToken = authToken {
            allHeaders["Authorization"] = "Bearer \(authToken)"
        }
        request.allHTTPHeaderFields = allHeaders
        
        // Make request
        do {
            let (data, response) = try await session.data(for: request)
            
            // Check HTTP response
            guard let httpResponse = response as? HTTPURLResponse else {
                return .failure(.networkError(.invalidResponse))
            }
            
            // Handle different status codes
            switch httpResponse.statusCode {
            case 200...299:
                // Success - try to decode
                do {
                    let decoded = try JSONDecoder().decode(T.self, from: data)
                    return .success(decoded)
                } catch {
                    return .failure(.decodingError(error as! DecodingError))
                }
                
            case 401:
                return .failure(.authenticationError("Invalid or expired token"))
                
            case 403:
                return .failure(.authenticationError("Insufficient permissions"))
                
            case 404:
                return .failure(.serverError(.notFound, "Resource not found"))
                
            case 429:
                let retryAfter = httpResponse.value(forHTTPHeaderField: "Retry-After")
                    .flatMap { Double($0) } ?? 60.0
                return .failure(.rateLimitExceeded(retryAfter))
                
            case 500...599:
                let errorMessage = String(data: data, encoding: .utf8) ?? "Unknown server error"
                let status = HTTPStatus(rawValue: httpResponse.statusCode) ?? .internalServerError
                return .failure(.serverError(status, errorMessage))
                
            default:
                let errorMessage = String(data: data, encoding: .utf8) ?? "Unknown error"
                return .failure(.serverError(.badRequest, errorMessage))
            }
            
        } catch {
            return .failure(.networkError(.requestFailed(error)))
        }
    }
}

// Usage with comprehensive error handling
class UserService {
    private let apiClient: APIClient
    
    init(apiClient: APIClient) {
        self.apiClient = apiClient
    }
    
    func fetchUser(id: String) async -> Result<User, APIError> {
        return await apiClient.request(endpoint: "/users/\(id)")
    }
    
    func createUser(_ user: CreateUserRequest) async -> Result<User, APIError> {
        let body = try? JSONEncoder().encode(user)
        return await apiClient.request(
            endpoint: "/users",
            method: .POST,
            body: body
        )
    }
    
    func updateUser(id: String, updates: UpdateUserRequest) async -> Result<User, APIError> {
        let body = try? JSONEncoder().encode(updates)
        return await apiClient.request(
            endpoint: "/users/\(id)",
            method: .PUT,
            body: body
        )
    }
}

// Comprehensive error handling in UI
class UserViewModel: ObservableObject {
    @Published var user: User?
    @Published var isLoading = false
    @Published var errorMessage: String?
    @Published var showError = false
    
    private let userService: UserService
    
    init(userService: UserService) {
        self.userService = userService
    }
    
    @MainActor
    func fetchUser(id: String) async {
        isLoading = true
        errorMessage = nil
        showError = false
        
        let result = await userService.fetchUser(id: id)
        
        switch result {
        case .success(let user):
            self.user = user
            self.isLoading = false
            
        case .failure(let error):
            self.isLoading = false
            self.errorMessage = error.localizedDescription
            self.showError = true
            
            // Log error for debugging
            print("Failed to fetch user: \(error)")
            
            // Handle specific error types
            switch error {
            case .authenticationError:
                // Redirect to login
                NotificationCenter.default.post(name: .userNeedsAuthentication, object: nil)
                
            case .rateLimitExceeded(let retryAfter):
                // Show retry timer
                DispatchQueue.main.asyncAfter(deadline: .now() + retryAfter) {
                    Task { await self.fetchUser(id: id) }
                }
                
            case .networkError:
                // Show offline message
                self.errorMessage = "You appear to be offline. Please check your connection."
                
            default:
                break
            }
        }
    }
}
```

**Pattern 2: Data Processing Pipeline with Result Chaining**
```swift
// Complex data processing with Result chaining
struct DataProcessingPipeline {
    
    // Step 1: Validate input
    func validateInput(_ data: RawData) -> Result<ValidatedData, ValidationError> {
        // Implementation
    }
    
    // Step 2: Transform data
    func transformData(_ data: ValidatedData) -> Result<TransformedData, TransformationError> {
        // Implementation
    }
    
    // Step 3: Enrich data
    func enrichData(_ data: TransformedData) -> Result<EnrichedData, EnrichmentError> {
        // Implementation
    }
    
    // Step 4: Store data
    func storeData(_ data: EnrichedData) -> Result<StoredData, StorageError> {
        // Implementation
    }
    
    // Chain all operations
    func processData(_ rawData: RawData) -> Result<StoredData, ProcessingError> {
        return validateInput(rawData)
            .flatMap { validatedData in
                transformData(validatedData)
            }
            .flatMap { transformedData in
                enrichData(transformedData)
            }
            .flatMap { enrichedData in
                storeData(enrichedData)
            }
            .mapError { error in
                ProcessingError.processingFailed(error)
            }
    }
    
    // Alternative: Process with partial success tracking
    func processDataWithPartialSuccess(_ rawData: RawData) -> ProcessingResult {
        var results: [ProcessingStep] = []
        var currentData: Any = rawData
        
        // Step 1: Validation
        let validationResult = validateInput(rawData)
        results.append(ProcessingStep.validation(validationResult))
        
        guard case .success(let validatedData) = validationResult else {
            return ProcessingResult(
                steps: results,
                finalData: nil,
                hasPartialSuccess: false
            )
        }
        
        currentData = validatedData
        
        // Step 2: Transformation
        let transformationResult = transformData(validatedData)
        results.append(ProcessingStep.transformation(transformationResult))
        
        guard case .success(let transformedData) = transformationResult else {
            return ProcessingResult(
                steps: results,
                finalData: validatedData,
                hasPartialSuccess: true
            )
        }
        
        currentData = transformedData
        
        // Continue with remaining steps...
        let enrichmentResult = enrichData(transformedData)
        results.append(ProcessingStep.enrichment(enrichmentResult))
        
        let storageResult = storeData(transformedData)
        results.append(ProcessingStep.storage(storageResult))
        
        return ProcessingResult(
            steps: results,
            finalData: currentData,
            hasPartialSuccess: true
        )
    }
}

// Usage with comprehensive error handling
class DataProcessor {
    private let pipeline: DataProcessingPipeline
    
    init(pipeline: DataProcessingPipeline) {
        self.pipeline = pipeline
    }
    
    func processData(_ rawData: RawData) async -> Result<ProcessedData, ProcessingError> {
        // Process data with full pipeline
        let result = pipeline.processData(rawData)
        
        switch result {
        case .success(let storedData):
            return .success(ProcessedData(from: storedData))
            
        case .failure(let error):
            // Try partial processing for recovery
            let partialResult = pipeline.processDataWithPartialSuccess(rawData)
            
            if partialResult.hasPartialSuccess {
                // Log partial success for debugging
                print("Partial processing successful: \(partialResult.steps.filter(\.isSuccess).count)/\(partialResult.steps.count) steps")
                
                // Return partial data with error information
                return .failure(.partialProcessingFailed(error, partialResult.finalData))
            } else {
                return .failure(error)
            }
        }
    }
}
```

### Result Type Best Practices and Advanced Patterns

**1. Error Type Design**
```swift
// Design error types that are actionable and informative
enum ValidationError: Error, LocalizedError {
    case requiredFieldMissing(String)
    case invalidFormat(String, String) // field name, expected format
    case valueOutOfRange(String, ClosedRange<Double>) // field name, valid range
    case customValidationFailed(String, String) // field name, reason
    
    var errorDescription: String? {
        switch self {
        case .requiredFieldMissing(let field):
            return "\(field) is required"
        case .invalidFormat(let field, let format):
            return "\(field) must be in format: \(format)"
        case .valueOutOfRange(let field, let range):
            return "\(field) must be between \(range.lowerBound) and \(range.upperBound)"
        case .customValidationFailed(let field, let reason):
            return "\(field) validation failed: \(reason)"
        }
    }
    
    var recoverySuggestion: String? {
        switch self {
        case .requiredFieldMissing(let field):
            return "Please enter a value for \(field)"
        case .invalidFormat(let field, let format):
            return "Please format \(field) as: \(format)"
        case .valueOutOfRange(let field, let range):
            return "Please enter a value between \(range.lowerBound) and \(range.upperBound) for \(field)"
        case .customValidationFailed:
            return "Please check the input and try again"
        }
    }
}
```

**2. Result Composition and Utilities**
```swift
// Utility functions for working with multiple Results
extension Result {
    // Combine multiple Results into a single Result
    static func combine<T, E: Error>(_ results: [Result<T, E>]) -> Result<[T], E> {
        var values: [T] = []
        
        for result in results {
            switch result {
            case .success(let value):
                values.append(value)
            case .failure(let error):
                return .failure(error)
            }
        }
        
        return .success(values)
    }
    
    // Combine Results with different success types
    static func combine<A, B, E: Error>(
        _ resultA: Result<A, E>,
        _ resultB: Result<B, E>
    ) -> Result<(A, B), E> {
        switch (resultA, resultB) {
        case (.success(let a), .success(let b)):
            return .success((a, b))
        case (.failure(let error), _):
            return .failure(error)
        case (_, .failure(let error)):
            return .failure(error)
        }
    }
    
    // Retry logic
    func retry<T>(
        maxAttempts: Int,
        delay: TimeInterval = 1.0,
        operation: @escaping () async -> Result<T, Failure>
    ) async -> Result<T, Failure> {
        
        for attempt in 1...maxAttempts {
            let result = await operation()
            
            if case .success = result {
                return result
            }
            
            if attempt < maxAttempts {
                try? await Task.sleep(nanoseconds: UInt64(delay * 1_000_000_000))
            }
        }
        
        // Return the last failure if all attempts failed
        return await operation()
    }
}

// Usage
let results = [
    validateInput(input1),
    validateInput(input2),
    validateInput(input3)
]

let combinedResult = Result.combine(results)
switch combinedResult {
case .success(let validatedInputs):
    // Process all validated inputs
    processAll(validatedInputs)
case .failure(let error):
    // Handle validation error
    handleValidationError(error)
}
```

**3. Result in Async Context**
```swift
// Modern async/await with Result
extension Result {
    // Convert async throwing function to Result
    static func catching<T>(
        _ operation: () async throws -> T
    ) async -> Result<T, Error> {
        do {
            let result = try await operation()
            return .success(result)
        } catch {
            return .failure(error)
        }
    }
    
    // Convert async throwing function with specific error type
    static func catching<T, E: Error>(
        _ operation: () async throws -> T
    ) async -> Result<T, E> where E: Error {
        do {
            let result = try await operation()
            return .success(result)
        } catch let error as E {
            return .failure(error)
        } catch {
            // Convert unknown errors to a default error type
            return .failure(UnknownError.unknown(error) as! E)
        }
    }
}

// Usage in modern async code
class ModernDataService {
    func fetchData() async -> Result<Data, APIError> {
        return await Result.catching {
            let (data, response) = try await URLSession.shared.data(from: url)
            // Process response...
            return data
        }
    }
}
```

---

## Property Wrappers - Deep Dive

Property wrappers are one of Swift's most powerful features, introduced in Swift 5.1. They allow you to define reusable logic that gets applied to properties automatically, eliminating boilerplate code and providing declarative, type-safe property behavior. Property wrappers are extensively used in SwiftUI and modern Swift development.

### Understanding Property Wrapper Fundamentals

**What Property Wrappers Actually Are:**
Property wrappers are types that "wrap" a property and provide additional functionality. They're declared using the `@propertyWrapper` attribute and can be applied to properties using the `@` syntax. Under the hood, Swift automatically generates the necessary code to use your wrapper.

**The Magic Behind Property Wrappers:**
```swift
// When you write this:
@Validated(range: 0...100)
var score: Int = 0

// Swift automatically generates this equivalent code:
private var _score: Validated<Int> = Validated(wrappedValue: 0, range: 0...100)
var score: Int {
    get { _score.wrappedValue }
    set { _score.wrappedValue = newValue }
}
```

**Core Requirements for Property Wrappers:**
```swift
@propertyWrapper
struct MyWrapper<T> {
    // 1. Must have a property named 'wrappedValue'
    var wrappedValue: T
    
    // 2. Must have an initializer (can be synthesized)
    init(wrappedValue: T) {
        self.wrappedValue = wrappedValue
    }
    
    // 3. Optional: Can have a projectedValue for additional access
    var projectedValue: SomeType {
        // Return additional information or functionality
    }
}
```

### Built-in Property Wrappers Deep Dive

#### @State - Local State Management

`@State` is SwiftUI's fundamental state management tool. It's designed for simple value types that are owned by the current view and don't need to be shared with other views.

**How @State Works Internally:**
```swift
// When you declare:
@State private var count = 0

// SwiftUI creates:
private var _count: State<Int> = State(wrappedValue: 0)
var count: Int {
    get { _count.wrappedValue }
    set { _count.wrappedValue = newValue }
}

// The State wrapper:
struct State<Value> {
    private var _value: Value
    
    var wrappedValue: Value {
        get { _value }
        set { 
            _value = newValue
            // SwiftUI automatically triggers view updates
            // by calling the view's body property
        }
    }
    
    init(wrappedValue: Value) {
        self._value = wrappedValue
    }
}
```

**@State Best Practices and Limitations:**
```swift
struct CounterView: View {
    // ✅ Good: Simple value types
    @State private var count = 0
    @State private var isAnimating = false
    @State private var text = ""
    
    // ❌ Bad: Complex reference types (use @StateObject instead)
    // @State private var user = User() // Don't do this!
    
    // ❌ Bad: Arrays/Dictionaries that need to be shared
    // @State private var items: [Item] = [] // Use @StateObject for shared data
    
    var body: some View {
        VStack {
            Text("Count: \(count)")
                .font(.title)
                .scaleEffect(isAnimating ? 1.2 : 1.0)
                .animation(.easeInOut(duration: 0.2), value: isAnimating)
            
            Button("Increment") {
                count += 1
                isAnimating = true
                
                // Reset animation after delay
                DispatchQueue.main.asyncAfter(deadline: .now() + 0.2) {
                    isAnimating = false
                }
            }
            .buttonStyle(.borderedProminent)
            
            Button("Reset") {
                count = 0
            }
            .buttonStyle(.bordered)
        }
        .padding()
    }
}
```

**@State with Computed Properties:**
```swift
struct AdvancedCounterView: View {
    @State private var count = 0
    @State private var multiplier = 1
    
    // Computed property that depends on @State
    private var total: Int {
        count * multiplier
    }
    
    // Computed property that can modify @State
    private var isEven: Bool {
        get { count % 2 == 0 }
        set {
            if newValue && count % 2 != 0 {
                count += 1 // Make it even
            } else if !newValue && count % 2 == 0 {
                count += 1 // Make it odd
            }
        }
    }
    
    var body: some View {
        VStack(spacing: 20) {
            Text("Count: \(count)")
                .font(.title)
            
            Text("Multiplier: \(multiplier)")
                .font(.headline)
            
            Text("Total: \(total)")
                .font(.largeTitle)
                .fontWeight(.bold)
            
            Text("Is Even: \(isEven ? "Yes" : "No")")
                .foregroundColor(isEven ? .green : .red)
            
            HStack(spacing: 20) {
                Button("Increment") {
                    count += 1
                }
                .buttonStyle(.borderedProminent)
                
                Button("Double Multiplier") {
                    multiplier *= 2
                }
                .buttonStyle(.bordered)
                
                Button("Toggle Even") {
                    isEven.toggle()
                }
                .buttonStyle(.bordered)
            }
        }
        .padding()
    }
}
```

#### @Binding - Two-Way Data Binding

`@Binding` creates a two-way connection between a parent view and a child view. It's essential for creating reusable components that need to modify data owned by their parent.

**Understanding @Binding Internals:**
```swift
// When you declare:
@Binding var isOn: Bool

// SwiftUI creates:
var isOn: Bool {
    get { _isOn.wrappedValue }
    set { _isOn.wrappedValue = newValue }
}

// The Binding wrapper:
struct Binding<Value> {
    let get: () -> Value
    let set: (Value) -> Void
    
    var wrappedValue: Value {
        get { get() }
        set { set(newValue) }
    }
}
```

**@Binding in Practice - Creating Reusable Components:**
```swift
// Reusable toggle component
struct CustomToggle: View {
    @Binding var isOn: Bool
    let title: String
    let icon: String
    let color: Color
    
    var body: some View {
        HStack {
            Image(systemName: icon)
                .foregroundColor(color)
                .frame(width: 24)
            
            Text(title)
                .font(.body)
            
            Spacer()
            
            Toggle("", isOn: $isOn)
                .labelsHidden()
        }
        .padding(.vertical, 8)
        .contentShape(Rectangle())
        .onTapGesture {
            isOn.toggle()
        }
    }
}

// Reusable form field component
struct FormField: View {
    @Binding var text: String
    let title: String
    let placeholder: String
    let icon: String
    let keyboardType: UIKeyboardType
    
    var body: some View {
        HStack {
            Image(systemName: icon)
                .foregroundColor(.gray)
                .frame(width: 24)
            
            VStack(alignment: .leading, spacing: 4) {
                Text(title)
                    .font(.caption)
                    .foregroundColor(.secondary)
                
                TextField(placeholder, text: $text)
                    .keyboardType(keyboardType)
                    .textFieldStyle(.plain)
            }
        }
        .padding(.vertical, 8)
    }
}

// Usage in parent view
struct SettingsView: View {
    @State private var notificationsEnabled = true
    @State private var darkModeEnabled = false
    @State private var autoSaveEnabled = true
    @State private var username = ""
    @State private var email = ""
    
    var body: some View {
        Form {
            Section("Preferences") {
                CustomToggle(
                    isOn: $notificationsEnabled,
                    title: "Push Notifications",
                    icon: "bell.fill",
                    color: .blue
                )
                
                CustomToggle(
                    isOn: $darkModeEnabled,
                    title: "Dark Mode",
                    icon: "moon.fill",
                    color: .purple
                )
                
                CustomToggle(
                    isOn: $autoSaveEnabled,
                    title: "Auto Save",
                    icon: "arrow.clockwise",
                    color: .green
                )
            }
            
            Section("Account") {
                FormField(
                    text: $username,
                    title: "Username",
                    placeholder: "Enter username",
                    icon: "person.fill",
                    keyboardType: .default
                )
                
                FormField(
                    text: $email,
                    title: "Email",
                    placeholder: "Enter email",
                    icon: "envelope.fill",
                    keyboardType: .emailAddress
                )
            }
        }
        .navigationTitle("Settings")
    }
}
```

**@Binding with Computed Properties:**
```swift
struct AdvancedFormView: View {
    @State private var firstName = ""
    @State private var lastName = ""
    
    // Computed binding for full name
    private var fullName: Binding<String> {
        Binding(
            get: { "\(firstName) \(lastName)".trimmingCharacters(in: .whitespaces) },
            set: { fullName in
                let components = fullName.components(separatedBy: " ")
                firstName = components.first ?? ""
                lastName = components.dropFirst().joined(separator: " ")
            }
        )
    }
    
    var body: some View {
        VStack(spacing: 20) {
            FormField(
                text: $firstName,
                title: "First Name",
                placeholder: "Enter first name",
                icon: "person.fill",
                keyboardType: .default
            )
            
            FormField(
                text: $lastName,
                title: "Last Name",
                placeholder: "Enter last name",
                icon: "person.fill",
                keyboardType: .default
            )
            
            FormField(
                text: fullName,
                title: "Full Name",
                placeholder: "Enter full name",
                icon: "person.2.fill",
                keyboardType: .default
            )
            
            Text("Preview: \(fullName.wrappedValue)")
                .font(.caption)
                .foregroundColor(.secondary)
        }
        .padding()
    }
}
```

#### @ObservedObject - External Object Observation

`@ObservedObject` is used to observe external objects that conform to `ObservableObject`. It's perfect for sharing data between views and implementing the MVVM pattern.

**Understanding @ObservedObject Internals:**
```swift
// When you declare:
@ObservedObject var userProfile: UserProfile

// SwiftUI creates:
var userProfile: UserProfile {
    get { _userProfile.wrappedValue }
    set { _userProfile.wrappedValue = newValue }
}

// The ObservedObject wrapper:
struct ObservedObject<ObjectType: ObservableObject> {
    private var _wrappedValue: ObjectType
    
    var wrappedValue: ObjectType {
        get { _wrappedValue }
        set { _wrappedValue = newValue }
    }
    
    // SwiftUI automatically subscribes to the object's objectWillChange publisher
    // and triggers view updates when the object changes
}
```

**@ObservedObject with MVVM Pattern:**
```swift
// Model
struct User: Identifiable, Codable {
    let id: String
    var name: String
    var email: String
    var isVerified: Bool
    var profileImageURL: URL?
    var joinDate: Date
    var lastLoginDate: Date?
}

// ViewModel
class UserProfileViewModel: ObservableObject {
    @Published var user: User?
    @Published var isLoading = false
    @Published var errorMessage: String?
    @Published var isEditing = false
    
    private let userService: UserService
    private var cancellables = Set<AnyCancellable>()
    
    init(userService: UserService) {
        self.userService = userService
    }
    
    func loadUser(id: String) {
        isLoading = true
        errorMessage = nil
        
        userService.fetchUser(id: id)
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.isLoading = false
                    if case .failure(let error) = completion {
                        self?.errorMessage = error.localizedDescription
                    }
                },
                receiveValue: { [weak self] user in
                    self?.user = user
                }
            )
            .store(in: &cancellables)
    }
    
    func updateUser(name: String, email: String) {
        guard var updatedUser = user else { return }
        updatedUser.name = name
        updatedUser.email = email
        
        isLoading = true
        errorMessage = nil
        
        userService.updateUser(updatedUser)
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.isLoading = false
                    if case .failure(let error) = completion {
                        self?.errorMessage = error.localizedDescription
                    }
                },
                receiveValue: { [weak self] user in
                    self?.user = user
                    self?.isEditing = false
                }
            )
            .store(in: &cancellables)
    }
    
    func deleteUser() {
        guard let userId = user?.id else { return }
        
        isLoading = true
        errorMessage = nil
        
        userService.deleteUser(id: userId)
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.isLoading = false
                    if case .failure(let error) = completion {
                        self?.errorMessage = error.localizedDescription
                    }
                },
                receiveValue: { [weak self] _ in
                    self?.user = nil
                    // Post notification to navigate away
                    NotificationCenter.default.post(name: .userDeleted, object: nil)
                }
            )
            .store(in: &cancellables)
    }
    
    // Computed properties
    var displayName: String {
        user?.name ?? "Unknown User"
    }
    
    var isUserVerified: Bool {
        user?.isVerified ?? false
    }
    
    var userStatus: String {
        if let lastLogin = user?.lastLoginDate {
            let formatter = RelativeDateTimeFormatter()
            return "Last seen \(formatter.localizedString(for: lastLogin, relativeTo: Date()))"
        } else {
            return "Never logged in"
        }
    }
}

// View
struct UserProfileView: View {
    @ObservedObject var viewModel: UserProfileViewModel
    @State private var editedName = ""
    @State private var editedEmail = ""
    
    var body: some View {
        Group {
            if viewModel.isLoading {
                ProgressView("Loading...")
            } else if let user = viewModel.user {
                userProfileContent(user)
            } else {
                Text("User not found")
                    .foregroundColor(.secondary)
            }
        }
        .alert("Error", isPresented: .constant(viewModel.errorMessage != nil)) {
            Button("OK") {
                viewModel.errorMessage = nil
            }
        } message: {
            if let errorMessage = viewModel.errorMessage {
                Text(errorMessage)
            }
        }
        .onAppear {
            if viewModel.user == nil {
                viewModel.loadUser(id: "current-user-id")
            }
        }
    }
    
    @ViewBuilder
    private func userProfileContent(_ user: User) -> some View {
        ScrollView {
            VStack(spacing: 20) {
                // Profile header
                VStack(spacing: 12) {
                    if let imageURL = user.profileImageURL {
                        AsyncImage(url: imageURL) { image in
                            image
                                .resizable()
                                .aspectRatio(contentMode: .fill)
                        } placeholder: {
                            Image(systemName: "person.circle.fill")
                                .foregroundColor(.gray)
                        }
                        .frame(width: 100, height: 100)
                        .clipShape(Circle())
                    } else {
                        Image(systemName: "person.circle.fill")
                            .font(.system(size: 100))
                            .foregroundColor(.gray)
                    }
                    
                    Text(viewModel.displayName)
                        .font(.title)
                        .fontWeight(.bold)
                    
                    Text(user.email)
                        .font(.subheadline)
                        .foregroundColor(.secondary)
                    
                    HStack {
                        Image(systemName: viewModel.isUserVerified ? "checkmark.seal.fill" : "xmark.seal")
                            .foregroundColor(viewModel.isUserVerified ? .green : .red)
                        
                        Text(viewModel.isUserVerified ? "Verified" : "Not Verified")
                            .font(.caption)
                            .foregroundColor(viewModel.isUserVerified ? .green : .red)
                    }
                }
                
                // User info
                VStack(alignment: .leading, spacing: 12) {
                    Text("User Information")
                        .font(.headline)
                        .padding(.horizontal)
                    
                    VStack(spacing: 8) {
                        InfoRow(title: "Member Since", value: user.joinDate.formatted(date: .abbreviated, time: .omitted))
                        InfoRow(title: "Status", value: viewModel.userStatus)
                    }
                    .padding()
                    .background(Color(.systemGray6))
                    .cornerRadius(10)
                }
                
                // Action buttons
                VStack(spacing: 12) {
                    if viewModel.isEditing {
                        editingView
                    } else {
                        actionButtons
                    }
                }
            }
            .padding()
        }
        .navigationTitle("Profile")
        .navigationBarTitleDisplayMode(.large)
        .toolbar {
            if !viewModel.isEditing {
                ToolbarItem(placement: .navigationBarTrailing) {
                    Button("Edit") {
                        startEditing()
                    }
                }
            }
        }
    }
    
    @ViewBuilder
    private var editingView: some View {
        VStack(spacing: 16) {
            VStack(alignment: .leading, spacing: 8) {
                Text("Name")
                    .font(.caption)
                    .foregroundColor(.secondary)
                
                TextField("Enter name", text: $editedName)
                    .textFieldStyle(.roundedBorder)
            }
            
            VStack(alignment: .leading, spacing: 8) {
                Text("Email")
                    .font(.caption)
                    .foregroundColor(.secondary)
                
                TextField("Enter email", text: $editedEmail)
                    .textFieldStyle(.roundedBorder)
                    .keyboardType(.emailAddress)
                    .autocapitalization(.none)
            }
            
            HStack(spacing: 12) {
                Button("Cancel") {
                    cancelEditing()
                }
                .buttonStyle(.bordered)
                
                Button("Save") {
                    saveChanges()
                }
                .buttonStyle(.borderedProminent)
                .disabled(editedName.isEmpty || editedEmail.isEmpty)
            }
        }
        .padding()
        .background(Color(.systemGray6))
        .cornerRadius(10)
    }
    
    @ViewBuilder
    private var actionButtons: some View {
        VStack(spacing: 12) {
            Button("Change Password") {
                // Navigate to password change view
            }
            .buttonStyle(.bordered)
            .frame(maxWidth: .infinity)
            
            Button("Delete Account") {
                // Show confirmation dialog
            }
            .buttonStyle(.bordered)
            .foregroundColor(.red)
            .frame(maxWidth: .infinity)
        }
    }
    
    private func startEditing() {
        editedName = viewModel.user?.name ?? ""
        editedEmail = viewModel.user?.email ?? ""
        viewModel.isEditing = true
    }
    
    private func cancelEditing() {
        viewModel.isEditing = false
    }
    
    private func saveChanges() {
        viewModel.updateUser(name: editedName, email: editedEmail)
    }
}

struct InfoRow: View {
    let title: String
    let value: String
    
    var body: some View {
        HStack {
            Text(title)
                .foregroundColor(.secondary)
            Spacer()
            Text(value)
                .fontWeight(.medium)
        }
    }
}
```

This comprehensive coverage of Property Wrappers shows how they provide a powerful, declarative way to manage property behavior in Swift. The key benefits are:

1. **Reusability**: Define logic once, use it everywhere
2. **Type Safety**: Compile-time guarantees about property behavior
3. **Declarative**: Clear, readable code that expresses intent
4. **Composability**: Combine multiple wrappers for complex behavior
5. **Performance**: Optimized by the Swift compiler

Property wrappers are essential for modern Swift development, especially in SwiftUI applications where they provide the foundation for state management and data flow.

---

## Key Paths - Deep Dive

Key paths are a powerful Swift feature that provides type-safe access to properties. They're represented by the `KeyPath<Root, Value>` type and allow you to reference properties without actually accessing them, enabling powerful functional programming patterns, dynamic property access, and compile-time safety.

### Understanding Key Paths Fundamentals

**What Key Paths Actually Are:**
Key paths are compile-time representations of property access paths. They're not values themselves, but rather "paths" that describe how to get from one type to another through a series of property accesses. Think of them as "directions" to properties rather than the properties themselves.

**Key Path Syntax and Types:**
```swift
// Basic key path syntax
let nameKeyPath: KeyPath<Person, String> = \Person.name
let ageKeyPath: KeyPath<Person, Int> = \Person.age

// Nested key paths
let cityKeyPath: KeyPath<Person, String> = \Person.address.city
let streetKeyPath: KeyPath<Person, String> = \Person.address.street

// Key path with generic types
let firstElementKeyPath: KeyPath<[String], String?> = \[String].first
let countKeyPath: KeyPath<[String], Int> = \[String].count

// Partial key paths (can be nil)
let optionalNameKeyPath: KeyPath<Person?, String?> = \Person?.name
```

**Key Path Categories:**
```swift
// 1. Writable Key Paths - Can read and write
var writableKeyPath: WritableKeyPath<Person, String> = \Person.name

// 2. Reference Writable Key Paths - Can read and write even on let constants
let referenceWritableKeyPath: ReferenceWritableKeyPath<Person, String> = \Person.name

// 3. Partial Key Paths - Can be nil
let partialKeyPath: PartialKeyPath<Person> = \Person.name

// 4. Any Key Paths - Type-erased key paths
let anyKeyPath: AnyKeyPath = \Person.name
```

### Key Paths in Practice

#### Basic Property Access

**Simple Property Access:**
```swift
struct Person {
    let name: String
    let age: Int
    let email: String
    var address: Address
}

struct Address {
    let street: String
    let city: String
    let country: String
    var postalCode: String
}

// Create key paths
let nameKeyPath: KeyPath<Person, String> = \Person.name
let ageKeyPath: KeyPath<Person, Int> = \Person.age
let cityKeyPath: KeyPath<Person, String> = \Person.address.city

// Use key paths to access properties
let person = Person(
    name: "John Doe",
    age: 30,
    email: "john@example.com",
    address: Address(
        street: "123 Main St",
        city: "New York",
        country: "USA",
        postalCode: "10001"
    )
)

// Access properties using key paths
let name = person[keyPath: nameKeyPath]        // "John Doe"
let age = person[keyPath: ageKeyPath]          // 30
let city = person[keyPath: cityKeyPath]        // "New York"

// Key paths work with any instance of the type
let anotherPerson = Person(
    name: "Jane Smith",
    age: 25,
    email: "jane@example.com",
    address: Address(
        street: "456 Oak Ave",
        city: "Los Angeles",
        country: "USA",
        postalCode: "90210"
    )
)

let anotherName = anotherPerson[keyPath: nameKeyPath]  // "Jane Smith"
let anotherCity = anotherPerson[keyPath: cityKeyPath]  // "Los Angeles"
```

**Writable Key Paths:**
```swift
// Writable key paths allow modification
var mutablePerson = person
let nameWritableKeyPath: WritableKeyPath<Person, String> = \Person.name
let postalCodeKeyPath: WritableKeyPath<Person, String> = \Person.address.postalCode

// Modify properties using key paths
mutablePerson[keyPath: nameWritableKeyPath] = "John Smith"
mutablePerson[keyPath: postalCodeKeyPath] = "10002"

print(mutablePerson.name)           // "John Smith"
print(mutablePerson.address.postalCode) // "10002"

// Reference writable key paths work even on let constants
let referencePerson = person
let referenceNameKeyPath: ReferenceWritableKeyPath<Person, String> = \Person.name

// This works because Person is a struct (value type)
// For reference types, you'd need ReferenceWritableKeyPath
referencePerson[keyPath: referenceNameKeyPath] = "John Smith"
```

#### Key Paths with Collections

**Array Operations with Key Paths:**
```swift
let people = [
    Person(name: "Alice", age: 25, email: "alice@example.com", address: Address(street: "1 First St", city: "Boston", country: "USA", postalCode: "02101")),
    Person(name: "Bob", age: 30, email: "bob@example.com", address: Address(street: "2 Second St", city: "Chicago", country: "USA", postalCode: "60601")),
    Person(name: "Charlie", age: 35, email: "charlie@example.com", address: Address(street: "3 Third St", city: "Denver", country: "USA", postalCode: "80201")),
    Person(name: "Diana", age: 28, email: "diana@example.com", address: Address(street: "4 Fourth St", city: "Seattle", country: "USA", postalCode: "98101"))
]

// Extract all names
let names = people.map(\.name)
print(names) // ["Alice", "Bob", "Charlie", "Diana"]

// Extract all ages
let ages = people.map(\.age)
print(ages) // [25, 30, 35, 28]

// Extract all cities
let cities = people.map(\.address.city)
print(cities) // ["Boston", "Chicago", "Denver", "Seattle"]

// Sort by age
let sortedByAge = people.sorted(by: \.age)
print(sortedByAge.map(\.name)) // ["Alice", "Diana", "Bob", "Charlie"]

// Sort by name
let sortedByName = people.sorted(by: \.name)
print(sortedByName.map(\.name)) // ["Alice", "Bob", "Charlie", "Diana"]

// Sort by city
let sortedByCity = people.sorted(by: \.address.city)
print(sortedByCity.map(\.name)) // ["Alice", "Bob", "Charlie", "Diana"]

// Find the oldest person
let oldestPerson = people.max(by: \.age)
print(oldestPerson?.name) // "Charlie"

// Find the youngest person
let youngestPerson = people.min(by: \.age)
print(youngestPerson?.name) // "Alice"

// Calculate average age
let averageAge = Double(people.map(\.age).reduce(0, +)) / Double(people.count)
print(averageAge) // 29.5

// Group people by country
let groupedByCountry = Dictionary(grouping: people, by: \.address.country)
print(groupedByCountry.keys) // ["USA"]
print(groupedByCountry["USA"]?.map(\.name)) // ["Alice", "Bob", "Charlie", "Diana"]
```

**Dictionary Operations with Key Paths:**
```swift
let personDict = [
    "person1": Person(name: "Alice", age: 25, email: "alice@example.com", address: Address(street: "1 First St", city: "Boston", country: "USA", postalCode: "02101")),
    "person2": Person(name: "Bob", age: 30, email: "bob@example.com", address: Address(street: "2 Second St", city: "Chicago", country: "USA", postalCode: "60601")),
    "person3": Person(name: "Charlie", age: 35, email: "charlie@example.com", address: Address(street: "3 Third St", city: "Denver", country: "USA", postalCode: "80201"))
]

// Extract all names from dictionary values
let dictNames = personDict.values.map(\.name)
print(dictNames) // ["Alice", "Bob", "Charlie"]

// Extract all cities from dictionary values
let dictCities = personDict.values.map(\.address.city)
print(dictCities) // ["Boston", "Chicago", "Denver"]

// Find person with highest age
let oldestInDict = personDict.values.max(by: \.age)
print(oldestInDict?.name) // "Charlie"

// Create a new dictionary with just names
let nameDict = Dictionary(uniqueKeysWithValues: personDict.map { ($0.key, $0.value.name) })
print(nameDict) // ["person1": "Alice", "person2": "Bob", "person3": "Charlie"]
```

#### Advanced Key Path Patterns

**Key Path Composition:**
```swift
// Compose key paths to create complex access paths
extension KeyPath {
    // Compose two key paths: Root -> Value -> FinalValue
    func appending<T>(_ other: KeyPath<Value, T>) -> KeyPath<Root, T> {
        return self.appending(path: other)
    }
}

// Usage
let addressKeyPath: KeyPath<Person, Address> = \Person.address
let cityKeyPath: KeyPath<Address, String> = \Address.city

// Compose them to get Person -> Address -> City
let personToCityKeyPath: KeyPath<Person, String> = addressKeyPath.appending(cityKeyPath)

// This is equivalent to \Person.address.city
let directKeyPath: KeyPath<Person, String> = \Person.address.city

// Both approaches give the same result
let city1 = person[keyPath: personToCityKeyPath]
let city2 = person[keyPath: directKeyPath]
print(city1 == city2) // true
```

**Key Paths with Functions:**
```swift
// Use key paths with higher-order functions
extension Array {
    // Sort by multiple criteria using key paths
    func sorted<T: Comparable>(by keyPaths: KeyPath<Element, T>...) -> [Element] {
        return sorted { first, second in
            for keyPath in keyPaths {
                let firstValue = first[keyPath: keyPath]
                let secondValue = second[keyPath: keyPath]
                if firstValue != secondValue {
                    return firstValue < secondValue
                }
            }
            return false
        }
    }
    
    // Group by multiple key paths
    func grouped<T: Hashable>(by keyPath: KeyPath<Element, T>) -> [T: [Element]] {
        return Dictionary(grouping: self, by: { $0[keyPath: keyPath] })
    }
    
    // Filter by key path value
    func filter<T: Equatable>(where keyPath: KeyPath<Element, T>, equals value: T) -> [Element] {
        return filter { $0[keyPath: keyPath] == value }
    }
    
    // Find first element by key path value
    func first<T: Equatable>(where keyPath: KeyPath<Element, T>, equals value: T) -> Element? {
        return first { $0[keyPath: keyPath] == value }
    }
}

// Usage
let people = [
    Person(name: "Alice", age: 25, email: "alice@example.com", address: Address(street: "1 First St", city: "Boston", country: "USA", postalCode: "02101")),
    Person(name: "Bob", age: 30, email: "bob@example.com", address: Address(street: "2 Second St", city: "Chicago", country: "USA", postalCode: "60601")),
    Person(name: "Charlie", age: 35, email: "charlie@example.com", address: Address(street: "3 Third St", city: "Denver", country: "USA", postalCode: "80201")),
    Person(name: "Diana", age: 28, email: "diana@example.com", address: Address(street: "4 Fourth St", city: "Seattle", country: "USA", postalCode: "98101")),
    Person(name: "Eve", age: 25, email: "eve@example.com", address: Address(street: "5 Fifth St", city: "Boston", country: "USA", postalCode: "02102"))
]

// Sort by age, then by name
let sortedByAgeThenName = people.sorted(by: \.age, \.name)
print(sortedByAgeThenName.map { "\($0.name) (\($0.age))" })
// ["Alice (25)", "Eve (25)", "Diana (28)", "Bob (30)", "Charlie (35)"]

// Group by city
let groupedByCity = people.grouped(by: \.address.city)
for (city, cityPeople) in groupedByCity {
    print("\(city): \(cityPeople.map(\.name).joined(separator: ", "))")
}
// Boston: Alice, Eve
// Chicago: Bob
// Denver: Charlie
// Seattle: Diana

// Filter people from Boston
let bostonPeople = people.filter(where: \.address.city, equals: "Boston")
print(bostonPeople.map(\.name)) // ["Alice", "Eve"]

// Find first person from Chicago
let chicagoPerson = people.first(where: \.address.city, equals: "Chicago")
print(chicagoPerson?.name) // "Bob"
```

**Key Paths with Generic Types:**
```swift
// Generic function that works with any type and key path
func extractValues<T, U>(from items: [T], using keyPath: KeyPath<T, U>) -> [U] {
    return items.map { $0[keyPath: keyPath] }
}

// Generic function for sorting
func sortItems<T, U: Comparable>(_ items: [T], by keyPath: KeyPath<T, U>) -> [T] {
    return items.sorted { $0[keyPath: keyPath] < $1[keyPath: keyPath] }
}

// Generic function for finding minimum/maximum
func findExtremum<T, U: Comparable>(_ items: [T], by keyPath: KeyPath<T, U>, isMax: Bool) -> T? {
    return isMax ? items.max(by: { $0[keyPath: keyPath] < $1[keyPath: keyPath] }) :
                   items.min(by: { $0[keyPath: keyPath] < $1[keyPath: keyPath] })
}

// Usage with different types
struct Product {
    let name: String
    let price: Double
    let category: String
}

let products = [
    Product(name: "Laptop", price: 999.99, category: "Electronics"),
    Product(name: "Book", price: 19.99, category: "Books"),
    Product(name: "Phone", price: 699.99, category: "Electronics"),
    Product(name: "Pen", price: 2.99, category: "Office")
]

// Extract all product names
let productNames = extractValues(from: products, using: \.name)
print(productNames) // ["Laptop", "Book", "Phone", "Pen"]

// Extract all prices
let productPrices = extractValues(from: products, using: \.price)
print(productPrices) // [999.99, 19.99, 699.99, 2.99]

// Sort products by price
let sortedByPrice = sortItems(products, by: \.price)
print(sortedByPrice.map(\.name)) // ["Pen", "Book", "Phone", "Laptop"]

// Find most expensive product
let mostExpensive = findExtremum(products, by: \.price, isMax: true)
print(mostExpensive?.name) // "Laptop"

// Find cheapest product
let cheapest = findExtremum(products, by: \.price, isMax: false)
print(cheapest?.name) // "Pen"
```

### Real-World Key Path Applications

**Pattern 1: Data Validation System**
```swift
// Generic validation system using key paths
struct ValidationRule<T, U> {
    let keyPath: KeyPath<T, U>
    let validator: (U) -> Bool
    let errorMessage: String
    
    init(_ keyPath: KeyPath<T, U>, validator: @escaping (U) -> Bool, errorMessage: String) {
        self.keyPath = keyPath
        self.validator = validator
        self.errorMessage = errorMessage
    }
    
    func validate(_ object: T) -> ValidationResult {
        let value = object[keyPath: keyPath]
        let isValid = validator(value)
        return ValidationResult(
            isValid: isValid,
            errorMessage: isValid ? nil : errorMessage,
            fieldName: String(describing: keyPath)
        )
    }
}

struct ValidationResult {
    let isValid: Bool
    let errorMessage: String?
    let fieldName: String
}

class Validator<T> {
    private var rules: [ValidationRule<T, Any>] = []
    
    func addRule<U>(_ keyPath: KeyPath<T, U>, validator: @escaping (U) -> Bool, errorMessage: String) {
        let rule = ValidationRule(keyPath, validator: validator, errorMessage: errorMessage)
        rules.append(rule as! ValidationRule<T, Any>)
    }
    
    func validate(_ object: T) -> [ValidationResult] {
        return rules.compactMap { rule in
            // This is a simplified version - in practice you'd need type erasure
            // or a different approach to handle the generic constraint
            return nil
        }
    }
}

// Usage
struct User {
    var name: String
    var email: String
    var age: Int
    var password: String
}

let validator = Validator<User>()

// Add validation rules
validator.addRule(\.name, validator: { !$0.isEmpty }, errorMessage: "Name is required")
validator.addRule(\.email, validator: { $0.contains("@") }, errorMessage: "Invalid email format")
validator.addRule(\.age, validator: { $0 >= 18 }, errorMessage: "Must be 18 or older")
validator.addRule(\.password, validator: { $0.count >= 8 }, errorMessage: "Password must be at least 8 characters")

let user = User(name: "", email: "invalid-email", age: 16, password: "123")
let results = validator.validate(user)

for result in results {
    if !result.isValid {
        print("\(result.fieldName): \(result.errorMessage ?? "")")
    }
}
```

**Pattern 2: Dynamic Form Generation**
```swift
// Form field configuration using key paths
struct FormField<T, U> {
    let keyPath: KeyPath<T, U>
    let title: String
    let placeholder: String
    let keyboardType: UIKeyboardType
    let validation: ((U) -> Bool)?
    
    init(_ keyPath: KeyPath<T, U>, title: String, placeholder: String = "", keyboardType: UIKeyboardType = .default, validation: ((U) -> Bool)? = nil) {
        self.keyPath = keyPath
        self.title = title
        self.placeholder = placeholder
        self.keyboardType = keyboardType
        self.validation = validation
    }
}

struct FormConfiguration<T> {
    let fields: [FormField<T, Any>]
    
    init(_ fields: [FormField<T, Any>]) {
        self.fields = fields
    }
}

// Usage
struct UserForm {
    var firstName: String
    var lastName: String
    var email: String
    var phone: String
    var age: Int
}

let userFormConfig = FormConfiguration([
    FormField(\.firstName, title: "First Name", placeholder: "Enter first name"),
    FormField(\.lastName, title: "Last Name", placeholder: "Enter last name"),
    FormField(\.email, title: "Email", placeholder: "Enter email", keyboardType: .emailAddress),
    FormField(\.phone, title: "Phone", placeholder: "Enter phone number", keyboardType: .phonePad),
    FormField(\.age, title: "Age", placeholder: "Enter age", keyboardType: .numberPad)
])

// Generate form dynamically
struct DynamicFormView<T>: View {
    let configuration: FormConfiguration<T>
    @Binding var data: T
    
    var body: some View {
        Form {
            ForEach(Array(configuration.fields.enumerated()), id: \.offset) { index, field in
                // Generate form fields based on configuration
                // This is a simplified example - you'd need type erasure for real implementation
                Text("Field: \(field.title)")
            }
        }
    }
}
```

**Pattern 3: Database Query Builder**
```swift
// Simple query builder using key paths
struct QueryCondition<T, U: Equatable> {
    let keyPath: KeyPath<T, U>
    let operator: QueryOperator
    let value: U
    
    enum QueryOperator {
        case equals
        case notEquals
        case greaterThan
        case lessThan
        case contains
    }
}

struct Query<T> {
    private var conditions: [QueryCondition<T, Any>] = []
    private var sortBy: (KeyPath<T, Any>, Bool)? // keyPath, ascending
    private var limit: Int?
    
    func `where`<U: Equatable>(_ keyPath: KeyPath<T, U>, equals value: U) -> Query<T> {
        let condition = QueryCondition(keyPath: keyPath, operator: .equals, value: value)
        conditions.append(condition as! QueryCondition<T, Any>)
        return self
    }
    
    func sort<U: Comparable>(by keyPath: KeyPath<T, U>, ascending: Bool = true) -> Query<T> {
        sortBy = (keyPath as! KeyPath<T, Any>, ascending)
        return self
    }
    
    func limit(_ count: Int) -> Query<T> {
        limit = count
        return self
    }
}

// Usage
let query = Query<User>()
    .where(\.address.city, equals: "Boston")
    .sort(by: \.age, ascending: false)
    .limit(10)

// In a real implementation, this would generate SQL or other query language
print("Generated query for users from Boston, sorted by age descending, limited to 10 results")
```

### Key Paths Best Practices and Advanced Patterns

**1. Type Safety and Compile-Time Guarantees**
```swift
// Key paths provide compile-time safety
let validKeyPath: KeyPath<Person, String> = \Person.name        // ✅ Compiles
let invalidKeyPath: KeyPath<Person, String> = \Person.nonexistent // ❌ Compile error

// Key paths work with optionals
let optionalNameKeyPath: KeyPath<Person?, String?> = \Person?.name
let optionalCityKeyPath: KeyPath<Person?, String?> = \Person?.address.city

// Key paths work with arrays
let arrayKeyPath: KeyPath<[Person], Int> = \[Person].count
let firstElementKeyPath: KeyPath<[Person], Person?> = \[Person].first
```

**2. Performance Considerations**
```swift
// Key paths are very efficient - they're just compile-time constructs
// No runtime overhead for property access
let name = person[keyPath: \.name]  // Same performance as person.name

// Key paths can be stored and reused
let storedKeyPath: KeyPath<Person, String> = \.name
let name1 = person[keyPath: storedKeyPath]
let name2 = anotherPerson[keyPath: storedKeyPath]

// Key paths work well with collections
let people = [person, anotherPerson]
let allNames = people.map(\.name)  // Efficient mapping
```

**3. Advanced Key Path Patterns**
```swift
// Key paths with computed properties
extension Person {
    var fullName: String {
        return "\(name) (\(age))"
    }
}

let fullNameKeyPath: KeyPath<Person, String> = \.fullName
let fullName = person[keyPath: fullNameKeyPath]

// Key paths with subscripts
let array = [1, 2, 3, 4, 5]
let firstElementKeyPath: KeyPath<[Int], Int?> = \[Int].first
let firstElement = array[keyPath: firstElementKeyPath]

// Key paths with enums
enum UserStatus {
    case active
    case inactive
    case suspended
}

struct User {
    let name: String
    let status: UserStatus
}

let statusKeyPath: KeyPath<User, UserStatus> = \.status
let user = User(name: "John", status: .active)
let status = user[keyPath: statusKeyPath]
```

---

## Mirror and Reflection - Deep Dive

Mirror and reflection in Swift provide runtime type introspection capabilities, allowing you to examine the structure and properties of types at runtime. While Swift is primarily a statically-typed language, reflection enables dynamic behavior, debugging tools, and generic data processing that would be impossible with compile-time type information alone.

### Understanding Reflection Fundamentals

**What Reflection Actually Is:**
Reflection is the ability of a program to examine, introspect, and modify its own structure and behavior at runtime. In Swift, this is primarily achieved through the `Mirror` type, which provides a view into the internal structure of any value.

**Why Reflection Matters:**
```swift
// ❌ Without reflection - hardcoded, inflexible
struct User {
    let name: String
    let age: Int
    let email: String
}

func printUser(_ user: User) {
    print("Name: \(user.name)")
    print("Age: \(user.age)")
    print("Email: \(user.email)")
}

// ❌ Problems:
// 1. Must know all properties at compile time
// 2. Adding new properties requires updating this function
// 3. No way to handle unknown types generically
// 4. Duplicate code for different types

// ✅ With reflection - dynamic, flexible
func printAnyObject(_ value: Any) {
    let mirror = Mirror(reflecting: value)
    print("Type: \(mirror.subjectType)")
    
    for child in mirror.children {
        if let label = child.label {
            print("\(label): \(child.value)")
        }
    }
}

// Now works with any type automatically
printAnyObject(User(name: "John", age: 30, email: "john@example.com"))
printAnyObject(Product(name: "Laptop", price: 999.99))
printAnyObject(["item1", "item2", "item3"])
```

**Mirror Internals and Structure:**
```swift
// The Mirror type provides a view into any value's structure
struct Mirror {
    let subjectType: Any.Type           // The type being reflected
    let children: Mirror.Children       // Collection of child elements
    let displayStyle: Mirror.DisplayStyle? // How the value should be displayed
    let superclassMirror: Mirror?      // Reflection of superclass (if any)
    
    // Children can be properties, tuple elements, or collection items
    struct Child {
        let label: String?              // Property name (nil for tuple elements)
        let value: Any                  // The actual value
    }
    
    // Display styles indicate the kind of value
    enum DisplayStyle {
        case `struct`                   // Struct type
        case `class`                    // Class type
        case `enum`                     // Enum type
        case tuple                       // Tuple type
        case optional                    // Optional type
        case collection                  // Array, Set, Dictionary
        case dictionary                  // Dictionary type
        case set                         // Set type
    }
    
    // Children collection provides access to all child elements
    struct Children: Collection {
        let elements: [Child]
        
        var startIndex: Int { elements.startIndex }
        var endIndex: Int { elements.endIndex }
        
        subscript(index: Int) -> Child {
            elements[index]
        }
        
        func index(after i: Int) -> Int {
            elements.index(after: i)
        }
    }
}
```

### Basic Reflection Operations

**Creating and Using Mirrors:**
```swift
// Basic mirror creation
let user = User(name: "John Doe", age: 30, email: "john@example.com")
let mirror = Mirror(reflecting: user)

// Examine the mirror's properties
print("Subject type: \(mirror.subjectType)")                    // User
print("Display style: \(mirror.displayStyle?.rawValue ?? "none")") // struct
print("Number of children: \(mirror.children.count)")            // 3

// Iterate through all properties
for child in mirror.children {
    if let label = child.label {
        print("\(label): \(child.value) (\(type(of: child.value)))")
    } else {
        print("Unlabeled: \(child.value) (\(type(of: child.value)))")
    }
}
// Output:
// name: John Doe (String)
// age: 30 (Int)
// email: john@example.com (String)

// Access specific children by index
if let firstChild = mirror.children.first {
    print("First property: \(firstChild.label ?? "unnamed") = \(firstChild.value)")
}

// Find specific property by label
if let nameChild = mirror.children.first(where: { $0.label == "name" }) {
    print("Found name property: \(nameChild.value)")
}
```

**Working with Different Types:**
```swift
// Struct reflection
struct Product {
    let id: String
    let name: String
    let price: Double
    let isAvailable: Bool
}

let product = Product(id: "123", name: "Laptop", price: 999.99, isAvailable: true)
let productMirror = Mirror(reflecting: product)

print("Product properties:")
for child in productMirror.children {
    if let label = child.label {
        print("  \(label): \(child.value)")
    }
}

// Class reflection
class Customer {
    let id: String
    var name: String
    var email: String
    
    init(id: String, name: String, email: String) {
        self.id = id
        self.name = name
        self.email = email
    }
}

let customer = Customer(id: "456", name: "Jane Smith", email: "jane@example.com")
let customerMirror = Mirror(reflecting: customer)

print("\nCustomer properties:")
for child in customerMirror.children {
    if let label = child.label {
        print("  \(label): \(child.value)")
    }
}

// Enum reflection
enum OrderStatus {
    case pending
    case processing
    case shipped(trackingNumber: String)
    case delivered(deliveryDate: Date)
    case cancelled(reason: String)
}

let status = OrderStatus.shipped(trackingNumber: "TRK123456")
let statusMirror = Mirror(reflecting: status)

print("\nOrder status:")
print("  Type: \(statusMirror.subjectType)")
print("  Display style: \(statusMirror.displayStyle?.rawValue ?? "none")")

// For enums with associated values, children contain the associated values
for child in statusMirror.children {
    if let label = child.label {
        print("  \(label): \(child.value)")
    } else {
        print("  Associated value: \(child.value)")
    }
}

// Tuple reflection
let tuple = (name: "John", age: 30, city: "New York")
let tupleMirror = Mirror(reflecting: tuple)

print("\nTuple elements:")
for (index, child) in tupleMirror.children.enumerated() {
    if let label = child.label {
        print("  \(label): \(child.value)")
    } else {
        print("  Element \(index): \(child.value)")
    }
}

// Collection reflection
let array = ["apple", "banana", "cherry"]
let arrayMirror = Mirror(reflecting: array)

print("\nArray elements:")
for (index, child) in arrayMirror.children.enumerated() {
    print("  [\(index)]: \(child.value)")
}

// Optional reflection
let optionalString: String? = "Hello, World!"
let optionalMirror = Mirror(reflecting: optionalString)

print("\nOptional value:")
print("  Type: \(optionalMirror.subjectType)")
print("  Display style: \(optionalMirror.displayStyle?.rawValue ?? "none")")
print("  Children count: \(optionalMirror.children.count)")

// If the optional has a value, it will have one child
if let firstChild = optionalMirror.children.first {
    print("  Value: \(firstChild.value)")
}
```

### Advanced Reflection Patterns

**Pattern 1: Generic Object Serialization**
```swift
// Generic serializer that works with any type
class ReflectionSerializer {
    
    // Serialize any object to a dictionary
    static func serialize<T>(_ object: T) -> [String: Any] {
        let mirror = Mirror(reflecting: object)
        var result: [String: Any] = [:]
        
        for child in mirror.children {
            if let label = child.label {
                result[label] = serializeValue(child.value)
            }
        }
        
        return result
    }
    
    // Recursively serialize values
    private static func serializeValue(_ value: Any) -> Any {
        let mirror = Mirror(reflecting: value)
        
        switch mirror.displayStyle {
        case .struct, .class:
            // Recursively serialize objects
            return serialize(value)
            
        case .enum:
            // Handle enums with associated values
            var enumDict: [String: Any] = [:]
            enumDict["_type"] = String(describing: mirror.subjectType)
            
            if let firstChild = mirror.children.first {
                if let label = firstChild.label {
                    enumDict[label] = serializeValue(firstChild.value)
                } else {
                    enumDict["value"] = serializeValue(firstChild.value)
                }
            }
            return enumDict
            
        case .collection:
            // Serialize arrays and other collections
            return mirror.children.map { serializeValue($0.value) }
            
        case .dictionary:
            // Serialize dictionaries
            var dict: [String: Any] = [:]
            for child in mirror.children {
                if let tuple = child.value as? (key: Any, value: Any) {
                    let keyString = String(describing: tuple.key)
                    dict[keyString] = serializeValue(tuple.value)
                }
            }
            return dict
            
        case .optional:
            // Handle optionals
            if let firstChild = mirror.children.first {
                return serializeValue(firstChild.value)
            } else {
                return NSNull()
            }
            
        default:
            // Primitive types
            return value
        }
    }
    
    // Deserialize dictionary back to object (simplified version)
    static func deserialize<T>(_ dict: [String: Any], as type: T.Type) -> T? {
        // This is a simplified example - real implementation would be more complex
        // and would require type information or a custom deserialization protocol
        return nil
    }
}

// Usage
struct ComplexObject {
    let id: String
    let name: String
    let metadata: [String: Any]
    let tags: [String]
    let status: OrderStatus
    let optionalField: String?
}

let complexObject = ComplexObject(
    id: "789",
    name: "Complex Product",
    metadata: ["category": "Electronics", "brand": "TechCorp"],
    tags: ["new", "featured", "electronics"],
    status: .shipped(trackingNumber: "TRK789"),
    optionalField: nil
)

let serialized = ReflectionSerializer.serialize(complexObject)
print("Serialized object:")
for (key, value) in serialized {
    print("  \(key): \(value)")
}
```

**Pattern 2: Dynamic Property Access and Modification**
```swift
// Dynamic property accessor
class DynamicPropertyAccessor {
    
    // Get property value by name
    static func getValue<T>(_ object: T, for propertyName: String) -> Any? {
        let mirror = Mirror(reflecting: object)
        
        for child in mirror.children {
            if child.label == propertyName {
                return child.value
            }
        }
        
        return nil
    }
    
    // Set property value by name (for reference types)
    static func setValue<T: AnyObject>(_ object: T, propertyName: String, value: Any) -> Bool {
        let mirror = Mirror(reflecting: object)
        
        for child in mirror.children {
            if child.label == propertyName {
                // Use key-value coding for actual property setting
                // This is a simplified example
                return true
            }
        }
        
        return false
    }
    
    // Get all property names
    static func getPropertyNames<T>(_ object: T) -> [String] {
        let mirror = Mirror(reflecting: object)
        return mirror.children.compactMap { $0.label }
    }
    
    // Get all property values
    static func getPropertyValues<T>(_ object: T) -> [String: Any] {
        let mirror = Mirror(reflecting: object)
        var result: [String: Any] = [:]
        
        for child in mirror.children {
            if let label = child.label {
                result[label] = child.value
            }
        }
        
        return result
    }
    
    // Check if object has property
    static func hasProperty<T>(_ object: T, named propertyName: String) -> Bool {
        let mirror = Mirror(reflecting: object)
        return mirror.children.contains { $0.label == propertyName }
    }
    
    // Get property type information
    static func getPropertyType<T>(_ object: T, for propertyName: String) -> Any.Type? {
        let mirror = Mirror(reflecting: object)
        
        for child in mirror.children {
            if child.label == propertyName {
                return type(of: child.value)
            }
        }
        
        return nil
    }
}

// Usage
class ConfigurableObject {
    var name: String
    var age: Int
    var isActive: Bool
    
    init(name: String, age: Int, isActive: Bool) {
        self.name = name
        self.age = age
        self.isActive = isActive
    }
}

let configObject = ConfigurableObject(name: "Test", age: 25, isActive: true)

// Get property names
let propertyNames = DynamicPropertyAccessor.getPropertyNames(configObject)
print("Property names: \(propertyNames)")

// Get property values
let propertyValues = DynamicPropertyAccessor.getPropertyValues(configObject)
print("Property values: \(propertyValues)")

// Check if property exists
let hasNameProperty = DynamicPropertyAccessor.hasProperty(configObject, named: "name")
print("Has name property: \(hasNameProperty)")

// Get property type
let nameType = DynamicPropertyAccessor.getPropertyType(configObject, for: "age")
print("Age property type: \(nameType?.self ?? "unknown")")

// Get specific property value
if let nameValue = DynamicPropertyAccessor.getValue(configObject, for: "name") {
    print("Name value: \(nameValue)")
}
```

**Pattern 3: Debugging and Logging System**
```swift
// Advanced debugging system using reflection
class ReflectionDebugger {
    
    // Pretty print any object
    static func prettyPrint<T>(_ object: T, indent: Int = 0) {
        let mirror = Mirror(reflecting: object)
        let indentString = String(repeating: "  ", count: indent)
        
        print("\(indentString)Type: \(mirror.subjectType)")
        
        if let displayStyle = mirror.displayStyle {
            print("\(indentString)Display Style: \(displayStyle)")
        }
        
        if mirror.children.isEmpty {
            print("\(indentString)Value: \(object)")
        } else {
            print("\(indentString)Properties:")
            for child in mirror.children {
                if let label = child.label {
                    print("\(indentString)  \(label):")
                    prettyPrint(child.value, indent: indent + 2)
                } else {
                    print("\(indentString)  [unnamed]:")
                    prettyPrint(child.value, indent: indent + 2)
                }
            }
        }
    }
    
    // Compare two objects and show differences
    static func compareObjects<T>(_ obj1: T, _ obj2: T) -> [String] {
        let mirror1 = Mirror(reflecting: obj1)
        let mirror2 = Mirror(reflecting: obj2)
        
        var differences: [String] = []
        
        // Compare types
        if mirror1.subjectType != mirror2.subjectType {
            differences.append("Type mismatch: \(mirror1.subjectType) vs \(mirror2.subjectType)")
        }
        
        // Compare properties
        let properties1 = Set(mirror1.children.compactMap { $0.label })
        let properties2 = Set(mirror2.children.compactMap { $0.label })
        
        let uniqueTo1 = properties1.subtracting(properties2)
        let uniqueTo2 = properties2.subtracting(properties1)
        
        if !uniqueTo1.isEmpty {
            differences.append("Properties only in first object: \(uniqueTo1)")
        }
        
        if !uniqueTo2.isEmpty {
            differences.append("Properties only in second object: \(uniqueTo2)")
        }
        
        // Compare common properties
        let commonProperties = properties1.intersection(properties2)
        for property in commonProperties {
            let value1 = DynamicPropertyAccessor.getValue(obj1, for: property)
            let value2 = DynamicPropertyAccessor.getValue(obj2, for: property)
            
            if !isEqual(value1, value2) {
                differences.append("Property '\(property)' differs: \(value1 ?? "nil") vs \(value2 ?? "nil")")
            }
        }
        
        return differences
    }
    
    // Check if two values are equal (simplified)
    private static func isEqual(_ value1: Any?, _ value2: Any?) -> Bool {
        if value1 == nil && value2 == nil { return true }
        if value1 == nil || value2 == nil { return false }
        
        // This is a simplified comparison - real implementation would be more complex
        return String(describing: value1) == String(describing: value2)
    }
    
    // Generate object summary
    static func generateSummary<T>(_ object: T) -> String {
        let mirror = Mirror(reflecting: object)
        let propertyCount = mirror.children.count
        let typeName = String(describing: mirror.subjectType)
        
        var summary = "\(typeName) with \(propertyCount) properties:\n"
        
        for child in mirror.children {
            if let label = child.label {
                let valueType = type(of: child.value)
                let valueString = String(describing: child.value)
                summary += "  • \(label): \(valueType) = \(valueString)\n"
            }
        }
        
        return summary
    }
}

// Usage
struct TestObject {
    let id: String
    let name: String
    let scores: [Int]
    let metadata: [String: String]
}

let obj1 = TestObject(
    id: "1",
    name: "Test",
    scores: [85, 90, 78],
    metadata: ["category": "A", "status": "active"]
)

let obj2 = TestObject(
    id: "2",
    name: "Test",
    scores: [85, 90, 78],
    metadata: ["category": "B", "status": "active"]
)

// Pretty print object
print("Object 1:")
ReflectionDebugger.prettyPrint(obj1)

// Compare objects
let differences = ReflectionDebugger.compareObjects(obj1, obj2)
print("\nDifferences:")
for difference in differences {
    print("  • \(difference)")
}

// Generate summary
let summary = ReflectionDebugger.generateSummary(obj1)
print("\nSummary:")
print(summary)
```

### Real-World Reflection Applications

**Application 1: Configuration System**
```swift
// Dynamic configuration system using reflection
class ConfigurationManager {
    
    // Load configuration from dictionary into any object
    static func loadConfiguration<T>(_ config: [String: Any], into object: T) -> T {
        let mirror = Mirror(reflecting: object)
        
        // This is a simplified example - real implementation would need
        // to handle actual property setting, which requires more complex reflection
        
        return object
    }
    
    // Save configuration from any object to dictionary
    static func saveConfiguration<T>(_ object: T) -> [String: Any] {
        return ReflectionSerializer.serialize(object)
    }
    
    // Validate configuration against schema
    static func validateConfiguration<T>(_ object: T, against schema: [String: Any]) -> [String] {
        let mirror = Mirror(reflecting: object)
        var errors: [String] = []
        
        for child in mirror.children {
            if let label = child.label {
                if let schemaValue = schema[label] {
                    // Validate against schema
                    if !isValidValue(child.value, for: schemaValue) {
                        errors.append("Invalid value for \(label): \(child.value)")
                    }
                } else {
                    errors.append("Missing required property: \(label)")
                }
            }
        }
        
        return errors
    }
    
    private static func isValidValue(_ value: Any, for schema: Any) -> Bool {
        // Simplified validation - real implementation would be more complex
        return true
    }
}

// Usage
struct AppConfig {
    let apiBaseURL: String
    let timeout: TimeInterval
    let maxRetries: Int
    let debugMode: Bool
}

let config = AppConfig(
    apiBaseURL: "https://api.example.com",
    timeout: 30.0,
    maxRetries: 3,
    debugMode: true
)

// Save configuration
let savedConfig = ConfigurationManager.saveConfiguration(config)
print("Saved configuration: \(savedConfig)")

// Validate configuration
let schema: [String: Any] = [
    "apiBaseURL": "string",
    "timeout": "number",
    "maxRetries": "number",
    "debugMode": "boolean"
]

let validationErrors = ConfigurationManager.validateConfiguration(config, against: schema)
if validationErrors.isEmpty {
    print("Configuration is valid")
} else {
    print("Configuration errors:")
    for error in validationErrors {
        print("  • \(error)")
    }
}
```

**Application 2: Data Mapping System**
```swift
// Generic data mapper using reflection
class DataMapper {
    
    // Map dictionary to object
    static func map<T>(_ dict: [String: Any], to type: T.Type) -> T? {
        // This is a simplified example - real implementation would be more complex
        // and would require type information and property setting capabilities
        return nil
    }
    
    // Map object to dictionary
    static func map<T>(_ object: T) -> [String: Any] {
        return ReflectionSerializer.serialize(object)
    }
    
    // Map between different object types
    static func map<From, To>(_ from: From, to type: To.Type) -> To? {
        let fromDict = map(from)
        return map(fromDict, to: type)
    }
    
    // Transform object properties
    static func transform<T>(_ object: T, using transformer: (String, Any) -> Any) -> [String: Any] {
        let mirror = Mirror(reflecting: object)
        var result: [String: Any] = [:]
        
        for child in mirror.children {
            if let label = child.label {
                result[label] = transformer(label, child.value)
            }
        }
        
        return result
    }
}

// Usage
struct SourceObject {
    let firstName: String
    let lastName: String
    let userAge: Int
}

struct TargetObject {
    let fullName: String
    let age: Int
    let isAdult: Bool
}

let source = SourceObject(firstName: "John", lastName: "Doe", userAge: 25)

// Transform object properties
let transformed = DataMapper.transform(source) { label, value in
    switch label {
    case "firstName", "lastName":
        return "\(source.firstName) \(source.lastName)"
    case "userAge":
        return value
    default:
        return value
    }
}

print("Transformed: \(transformed)")
```

### Reflection Best Practices and Limitations

**Best Practices:**
```swift
// 1. Use reflection sparingly - prefer compile-time solutions when possible
// 2. Cache reflection results for performance-critical code
// 3. Handle errors gracefully - reflection can fail
// 4. Consider security implications - reflection can expose private information
// 5. Use reflection for debugging and development tools

// Performance optimization example
class CachedReflection {
    private static var cache: [String: Mirror] = [:]
    
    static func getMirror<T>(for object: T) -> Mirror {
        let key = String(describing: type(of: object))
        
        if let cached = cache[key] {
            return cached
        }
        
        let mirror = Mirror(reflecting: object)
        cache[key] = mirror
        return mirror
    }
    
    static func clearCache() {
        cache.removeAll()
    }
}
```

**Limitations and Considerations:**
```swift
// 1. Performance overhead - reflection is slower than direct access
// 2. Type erasure - reflection loses some type information
// 3. Private properties - may not be accessible depending on access control
// 4. Platform differences - behavior may vary between platforms
// 5. Compiler optimizations - reflection can interfere with optimizations

// Example of limitations
class PrivateObject {
    private let privateProperty = "private"
    internal let internalProperty = "internal"
    public let publicProperty = "public"
}

let privateObj = PrivateObject()
let mirror = Mirror(reflecting: privateObj)

print("All properties:")
for child in mirror.children {
    if let label = child.label {
        print("  \(label): \(child.value)")
    }
}

// Note: Private properties may not be visible depending on the context
// and platform
```

This comprehensive coverage of Mirror and Reflection shows how they provide powerful runtime introspection capabilities in Swift. The key benefits are:

1. **Dynamic Behavior**: Examine and manipulate objects at runtime
2. **Generic Processing**: Work with unknown types generically
3. **Debugging Tools**: Build powerful debugging and logging systems
4. **Data Serialization**: Create flexible serialization systems
5. **Configuration Management**: Build dynamic configuration systems

While reflection should be used sparingly due to performance implications, it's essential for building development tools, debugging systems, and generic data processing pipelines that need to work with unknown types at runtime.

---

## Codable - Deep Dive

The `Codable` protocol in Swift provides a powerful, type-safe way to encode and decode data to and from various formats like JSON, Property Lists, and custom binary formats. It's the foundation for data serialization in modern Swift applications, enabling seamless integration with web APIs, local storage, and data interchange.

### Understanding Codable Fundamentals

**What Codable Actually Is:**
`Codable` is a type alias that combines two protocols: `Encodable` and `Decodable`. It provides automatic synthesis of encoding and decoding logic for most Swift types, eliminating the need for manual serialization code.

```swift
// Codable is a type alias for Encodable & Decodable
typealias Codable = Encodable & Decodable

// The two protocols provide different capabilities
protocol Encodable {
    func encode(to encoder: Encoder) throws
}

protocol Decodable {
    init(from decoder: Decoder) throws
}
```

**Why Codable Matters:**
```swift
// ❌ Without Codable - manual, error-prone serialization
struct User {
    let id: String
    let name: String
    let email: String
    let age: Int
}

// Manual JSON encoding
extension User {
    func toJSON() -> [String: Any] {
        return [
            "id": id,
            "name": name,
            "email": email,
            "age": age
        ]
    }
    
    static func fromJSON(_ json: [String: Any]) -> User? {
        guard let id = json["id"] as? String,
              let name = json["name"] as? String,
              let email = json["email"] as? String,
              let age = json["age"] as? Int else {
            return nil
        }
        
        return User(id: id, name: name, email: email, age: age)
    }
}

// ❌ Problems:
// 1. Manual property mapping
// 2. Type casting errors
// 3. No compile-time safety
// 4. Duplicate code for different types
// 5. Error-prone and hard to maintain

// ✅ With Codable - automatic, type-safe serialization
struct User: Codable {
    let id: String
    let name: String
    let email: String
    let age: Int
}

// Automatic encoding/decoding - no manual code needed!
let user = User(id: "123", name: "John", email: "john@example.com", age: 30)

// Encode to JSON
let encoder = JSONEncoder()
let data = try encoder.encode(user)

// Decode from JSON
let decoder = JSONDecoder()
let decodedUser = try decoder.decode(User.self, from: data)
```

### Basic Codable Implementation

**Simple Codable Types:**
```swift
// Basic struct with Codable conformance
struct Person: Codable {
    let id: String
    let name: String
    let age: Int
    let isActive: Bool
}

// Enums with raw values are automatically Codable
enum UserRole: String, Codable {
    case admin = "admin"
    case user = "user"
    case moderator = "moderator"
}

// Arrays and dictionaries of Codable types are automatically Codable
struct UserGroup: Codable {
    let name: String
    let members: [Person]
    let roles: [String: UserRole]
    let metadata: [String: String]
}

// Nested Codable types
struct Address: Codable {
    let street: String
    let city: String
    let state: String
    let zipCode: String
    let country: String
}

struct Contact: Codable {
    let person: Person
    let address: Address
    let phoneNumbers: [String]
    let emergencyContact: Person?
}

// Usage
let person = Person(id: "1", name: "John Doe", age: 30, isActive: true)
let address = Address(street: "123 Main St", city: "New York", state: "NY", zipCode: "10001", country: "USA")
let contact = Contact(
    person: person,
    address: address,
    phoneNumbers: ["555-1234", "555-5678"],
    emergencyContact: Person(id: "2", name: "Jane Doe", age: 28, isActive: true)
)

// Encode to JSON
let encoder = JSONEncoder()
encoder.outputFormatting = .prettyPrinted

do {
    let jsonData = try encoder.encode(contact)
    if let jsonString = String(data: jsonData, encoding: .utf8) {
        print("Encoded JSON:")
        print(jsonString)
    }
} catch {
    print("Encoding error: \(error)")
}

// Decode from JSON
let jsonString = """
{
    "person": {
        "id": "3",
        "name": "Bob Smith",
        "age": 35,
        "isActive": true
    },
    "address": {
        "street": "456 Oak Ave",
        "city": "Los Angeles",
        "state": "CA",
        "zipCode": "90210",
        "country": "USA"
    },
    "phoneNumbers": ["555-9999"],
    "emergencyContact": null
}
"""

let jsonData = jsonString.data(using: .utf8)!
let decoder = JSONDecoder()

do {
    let decodedContact = try decoder.decode(Contact.self, from: jsonData)
    print("Decoded contact: \(decodedContact.person.name)")
} catch {
    print("Decoding error: \(error)")
}
```

**Codable with Different Data Types:**
```swift
// Codable works with all standard Swift types
struct DataTypes: Codable {
    let string: String
    let int: Int
    let double: Double
    let bool: Bool
    let date: Date
    let data: Data
    let url: URL
    let decimal: Decimal
    let uuid: UUID
    let array: [String]
    let dictionary: [String: Int]
    let optional: String?
    let nestedOptional: String??
}

// Custom date encoding/decoding
let dateFormatter = DateFormatter()
dateFormatter.dateFormat = "yyyy-MM-dd HH:mm:ss"

let encoder = JSONEncoder()
encoder.dateEncodingStrategy = .formatted(dateFormatter)

let decoder = JSONDecoder()
decoder.dateDecodingStrategy = .formatted(dateFormatter)

// Usage
let dataTypes = DataTypes(
    string: "Hello",
    int: 42,
    double: 3.14,
    bool: true,
    date: Date(),
    data: "Hello".data(using: .utf8)!,
    url: URL(string: "https://example.com")!,
    decimal: Decimal(123.45),
    uuid: UUID(),
    array: ["item1", "item2"],
    dictionary: ["key1": 1, "key2": 2],
    optional: "optional value",
    nestedOptional: "nested optional"
)

do {
    let jsonData = try encoder.encode(dataTypes)
    let decoded = try decoder.decode(DataTypes.self, from: jsonData)
    print("Successfully encoded and decoded")
} catch {
    print("Error: \(error)")
}
```

### Advanced Codable Patterns

**Custom Coding Keys:**
```swift
// Custom coding keys for API compatibility
struct APIUser: Codable {
    let id: String
    let fullName: String
    let emailAddress: String
    let userAge: Int
    let isUserActive: Bool
    
    // Custom coding keys to match API response
    enum CodingKeys: String, CodingKey {
        case id = "user_id"
        case fullName = "full_name"
        case emailAddress = "email_address"
        case userAge = "user_age"
        case isUserActive = "is_user_active"
    }
}

// Usage
let apiUser = APIUser(
    id: "123",
    fullName: "John Doe",
    emailAddress: "john@example.com",
    userAge: 30,
    isUserActive: true
)

// This will generate JSON with the custom keys
let jsonData = try JSONEncoder().encode(apiUser)
if let jsonString = String(data: jsonData, encoding: .utf8) {
    print("API JSON: \(jsonString)")
    // Output: {"user_id":"123","full_name":"John Doe","email_address":"john@example.com","user_age":30,"is_user_active":true}
}
```

**Custom Encoding and Decoding:**
```swift
// Custom encoding/decoding for complex types
struct CustomUser: Codable {
    let id: String
    let name: String
    let email: String
    let profile: UserProfile
    
    // Custom encoding
    func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        
        try container.encode(id, forKey: .id)
        try container.encode(name, forKey: .name)
        try container.encode(email, forKey: .email)
        
        // Custom profile encoding
        let profileData = try JSONEncoder().encode(profile)
        try container.encode(profileData, forKey: .profile)
    }
    
    // Custom decoding
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        
        id = try container.decode(String.self, forKey: .id)
        name = try container.decode(String.self, forKey: .name)
        email = try container.decode(String.self, forKey: .email)
        
        // Custom profile decoding
        let profileData = try container.decode(Data.self, forKey: .profile)
        profile = try JSONDecoder().decode(UserProfile.self, from: profileData)
    }
    
    enum CodingKeys: String, CodingKey {
        case id, name, email, profile
    }
}

struct UserProfile: Codable {
    let avatar: String
    let bio: String
    let preferences: [String: String]
}

// Usage
let profile = UserProfile(
    avatar: "avatar.jpg",
    bio: "Software developer",
    preferences: ["theme": "dark", "language": "en"]
)

let customUser = CustomUser(
    id: "123",
    name: "John Doe",
    email: "john@example.com",
    profile: profile
)

// Encode and decode
let jsonData = try JSONEncoder().encode(customUser)
let decodedUser = try JSONDecoder().decode(CustomUser.self, from: jsonData)
```

**Conditional Conformance:**
```swift
// Conditional conformance for generic types
struct Container<T> {
    let value: T
    let metadata: [String: String]
}

// Only conform to Codable if T is Codable
extension Container: Codable where T: Codable {
    // Automatic synthesis works here
}

// Usage
let stringContainer = Container(value: "Hello", metadata: ["type": "string"])
let intContainer = Container(value: 42, metadata: ["type": "int"])

// These work because String and Int are Codable
let stringData = try JSONEncoder().encode(stringContainer)
let intData = try JSONEncoder().encode(intContainer)

// This would cause a compile error if T wasn't Codable
// let anyContainer = Container(value: Any(), metadata: [:])
// let anyData = try JSONEncoder().encode(anyContainer) // ❌ Compile error
```

**Polymorphic Decoding:**
```swift
// Handle different types in the same JSON structure
enum Message: Codable {
    case text(TextMessage)
    case image(ImageMessage)
    case video(VideoMessage)
    
    // Custom coding keys
    enum CodingKeys: String, CodingKey {
        case type
        case data
    }
    
    // Custom encoding
    func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        
        switch self {
        case .text(let message):
            try container.encode("text", forKey: .type)
            try container.encode(message, forKey: .data)
        case .image(let message):
            try container.encode("image", forKey: .type)
            try container.encode(message, forKey: .data)
        case .video(let message):
            try container.encode("video", forKey: .type)
            try container.encode(message, forKey: .data)
        }
    }
    
    // Custom decoding
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        let type = try container.decode(String.self, forKey: .type)
        
        switch type {
        case "text":
            let data = try container.decode(TextMessage.self, forKey: .data)
            self = .text(data)
        case "image":
            let data = try container.decode(ImageMessage.self, forKey: .data)
            self = .image(data)
        case "video":
            let data = try container.decode(VideoMessage.self, forKey: .data)
            self = .video(data)
        default:
            throw DecodingError.dataCorruptedError(
                forKey: .type,
                in: container,
                debugDescription: "Unknown message type: \(type)"
            )
        }
    }
}

struct TextMessage: Codable {
    let content: String
    let timestamp: Date
}

struct ImageMessage: Codable {
    let url: URL
    let caption: String?
    let timestamp: Date
}

struct VideoMessage: Codable {
    let url: URL
    let duration: TimeInterval
    let thumbnail: URL?
    let timestamp: Date
}

// Usage
let messages: [Message] = [
    .text(TextMessage(content: "Hello!", timestamp: Date())),
    .image(ImageMessage(url: URL(string: "https://example.com/image.jpg")!, caption: "Beautiful sunset", timestamp: Date())),
    .video(VideoMessage(url: URL(string: "https://example.com/video.mp4")!, duration: 120, thumbnail: URL(string: "https://example.com/thumb.jpg"), timestamp: Date()))
]

// Encode
let jsonData = try JSONEncoder().encode(messages)

// Decode
let decodedMessages = try JSONDecoder().decode([Message].self, from: jsonData)

// Process different message types
for message in decodedMessages {
    switch message {
    case .text(let textMessage):
        print("Text: \(textMessage.content)")
    case .image(let imageMessage):
        print("Image: \(imageMessage.url)")
    case .video(let videoMessage):
        print("Video: \(videoMessage.url), Duration: \(videoMessage.duration)s")
    }
}
```

### Real-World Codable Applications

**Application 1: API Client with Codable**
```swift
// Comprehensive API client using Codable
class APIClient {
    private let session: URLSession
    private let baseURL: URL
    
    init(session: URLSession = .shared, baseURL: URL) {
        self.session = session
        self.baseURL = baseURL
    }
    
    // Generic request method
    func request<T: Codable>(
        endpoint: String,
        method: HTTPMethod = .GET,
        body: Encodable? = nil,
        headers: [String: String] = [:]
    ) async throws -> T {
        
        guard let url = URL(string: endpoint, relativeTo: baseURL) else {
            throw APIError.invalidURL
        }
        
        var request = URLRequest(url: url)
        request.httpMethod = method.rawValue
        
        // Add headers
        var allHeaders = headers
        allHeaders["Content-Type"] = "application/json"
        request.allHTTPHeaderFields = allHeaders
        
        // Add body if provided
        if let body = body {
            let encoder = JSONEncoder()
            encoder.dateEncodingStrategy = .iso8601
            request.httpBody = try encoder.encode(body)
        }
        
        // Make request
        let (data, response) = try await session.data(for: request)
        
        // Check response
        guard let httpResponse = response as? HTTPURLResponse else {
            throw APIError.invalidResponse
        }
        
        guard 200...299 ~= httpResponse.statusCode else {
            throw APIError.serverError(httpResponse.statusCode, data)
        }
        
        // Decode response
        let decoder = JSONDecoder()
        decoder.dateDecodingStrategy = .iso8601
        
        do {
            return try decoder.decode(T.self, from: data)
        } catch {
            throw APIError.decodingError(error, data)
        }
    }
}

// API models using Codable
struct CreateUserRequest: Codable {
    let name: String
    let email: String
    let password: String
    let age: Int?
    
    enum CodingKeys: String, CodingKey {
        case name, email, password, age
    }
    
    // Custom encoding to exclude password from logs
    func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        
        try container.encode(name, forKey: .name)
        try container.encode(email, forKey: .email)
        try container.encode(password, forKey: .password)
        try container.encodeIfPresent(age, forKey: .age)
    }
}

struct UserResponse: Codable {
    let id: String
    let name: String
    let email: String
    let age: Int?
    let createdAt: Date
    let isActive: Bool
    
    enum CodingKeys: String, CodingKey {
        case id, name, email, age
        case createdAt = "created_at"
        case isActive = "is_active"
    }
}

struct APIErrorResponse: Codable {
    let error: String
    let message: String
    let code: Int?
    let details: [String: String]?
}

// API error types
enum APIError: Error, LocalizedError {
    case invalidURL
    case invalidResponse
    case serverError(Int, Data)
    case decodingError(Error, Data)
    case networkError(Error)
    
    var errorDescription: String? {
        switch self {
        case .invalidURL:
            return "Invalid URL"
        case .invalidResponse:
            return "Invalid response"
        case .serverError(let code, _):
            return "Server error: \(code)"
        case .decodingError(let error, _):
            return "Decoding error: \(error.localizedDescription)"
        case .networkError(let error):
            return "Network error: \(error.localizedDescription)"
        }
    }
}

enum HTTPMethod: String {
    case GET = "GET"
    case POST = "POST"
    case PUT = "PUT"
    case DELETE = "DELETE"
    case PATCH = "PATCH"
}

// Usage
class UserService {
    private let apiClient: APIClient
    
    init(apiClient: APIClient) {
        self.apiClient = apiClient
    }
    
    func createUser(_ request: CreateUserRequest) async throws -> UserResponse {
        return try await apiClient.request(
            endpoint: "/users",
            method: .POST,
            body: request
        )
    }
    
    func getUser(id: String) async throws -> UserResponse {
        return try await apiClient.request(endpoint: "/users/\(id)")
    }
    
    func updateUser(id: String, updates: [String: Any]) async throws -> UserResponse {
        // Convert dictionary to JSON data for updates
        let jsonData = try JSONSerialization.data(withJSONObject: updates)
        let decoder = JSONDecoder()
        decoder.dateDecodingStrategy = .iso8601
        
        // This is a simplified example - real implementation would be more complex
        return try await apiClient.request(
            endpoint: "/users/\(id)",
            method: .PUT,
            body: updates
        )
    }
}

// Usage
let apiClient = APIClient(baseURL: URL(string: "https://api.example.com")!)
let userService = UserService(apiClient: apiClient)

Task {
    do {
        let createRequest = CreateUserRequest(
            name: "John Doe",
            email: "john@example.com",
            password: "secret123",
            age: 30
        )
        
        let newUser = try await userService.createUser(createRequest)
        print("Created user: \(newUser.name)")
        
        let fetchedUser = try await userService.getUser(id: newUser.id)
        print("Fetched user: \(fetchedUser.name)")
        
    } catch {
        print("Error: \(error.localizedDescription)")
    }
}
```

**Application 2: Local Storage with Codable**
```swift
// Local storage manager using Codable
class LocalStorageManager {
    private let userDefaults: UserDefaults
    private let fileManager: FileManager
    
    init(userDefaults: UserDefaults = .standard, fileManager: FileManager = .default) {
        self.userDefaults = userDefaults
        self.fileManager = fileManager
    }
    
    // Save to UserDefaults
    func save<T: Codable>(_ object: T, forKey key: String) throws {
        let data = try JSONEncoder().encode(object)
        userDefaults.set(data, forKey: key)
    }
    
    // Load from UserDefaults
    func load<T: Codable>(_ type: T.Type, forKey key: String) throws -> T? {
        guard let data = userDefaults.data(forKey: key) else {
            return nil
        }
        
        return try JSONDecoder().decode(type, from: data)
    }
    
    // Save to file
    func saveToFile<T: Codable>(_ object: T, filename: String) throws {
        let documentsPath = fileManager.urls(for: .documentDirectory, in: .userDomainMask).first!
        let fileURL = documentsPath.appendingPathComponent(filename)
        
        let data = try JSONEncoder().encode(object)
        try data.write(to: fileURL)
    }
    
    // Load from file
    func loadFromFile<T: Codable>(_ type: T.Type, filename: String) throws -> T? {
        let documentsPath = fileManager.urls(for: .documentDirectory, in: .userDomainMask).first!
        let fileURL = documentsPath.appendingPathComponent(filename)
        
        guard fileManager.fileExists(atPath: fileURL.path) else {
            return nil
        }
        
        let data = try Data(contentsOf: fileURL)
        return try JSONDecoder().decode(type, from: data)
    }
    
    // Save to file with custom encoder
    func saveToFileWithCustomEncoder<T: Codable>(
        _ object: T,
        filename: String,
        encoder: JSONEncoder
    ) throws {
        let documentsPath = fileManager.urls(for: .documentDirectory, in: .userDomainMask).first!
        let fileURL = documentsPath.appendingPathComponent(filename)
        
        let data = try encoder.encode(object)
        try data.write(to: fileURL)
    }
    
    // Load from file with custom decoder
    func loadFromFileWithCustomDecoder<T: Codable>(
        _ type: T.Type,
        filename: String,
        decoder: JSONDecoder
    ) throws -> T? {
        let documentsPath = fileManager.urls(for: .documentDirectory, in: .userDomainMask).first!
        let fileURL = documentsPath.appendingPathComponent(filename)
        
        guard fileManager.fileExists(atPath: fileURL.path) else {
            return nil
        }
        
        let data = try Data(contentsOf: fileURL)
        return try decoder.decode(type, from: data)
    }
}

// Usage
struct AppSettings: Codable {
    let theme: String
    let language: String
    let notificationsEnabled: Bool
    let lastSyncDate: Date
    let userPreferences: [String: String]
}

struct UserData: Codable {
    let profile: UserProfile
    let settings: AppSettings
    let recentItems: [String]
    let favorites: Set<String>
}

let storageManager = LocalStorageManager()

// Save settings
let settings = AppSettings(
    theme: "dark",
    language: "en",
    notificationsEnabled: true,
    lastSyncDate: Date(),
    userPreferences: ["autoSave": "true", "syncInterval": "5min"]
)

do {
    try storageManager.save(settings, forKey: "appSettings")
    try storageManager.saveToFile(settings, filename: "settings.json")
    print("Settings saved successfully")
} catch {
    print("Failed to save settings: \(error)")
}

// Load settings
do {
    let loadedSettings = try storageManager.load(AppSettings.self, forKey: "appSettings")
    let fileSettings = try storageManager.loadFromFile(AppSettings.self, filename: "settings.json")
    
    if let settings = loadedSettings {
        print("Loaded settings: \(settings.theme)")
    }
    
    if let fileSettings = fileSettings {
        print("File settings: \(fileSettings.language)")
    }
} catch {
    print("Failed to load settings: \(error)")
}

// Custom encoder/decoder for specific needs
let customEncoder = JSONEncoder()
customEncoder.outputFormatting = .prettyPrinted
customEncoder.dateEncodingStrategy = .formatted(DateFormatter())

let customDecoder = JSONDecoder()
customDecoder.dateDecodingStrategy = .formatted(DateFormatter())

// Save with custom encoder
do {
    try storageManager.saveToFileWithCustomEncoder(settings, filename: "pretty_settings.json", encoder: customEncoder)
    print("Settings saved with custom encoder")
} catch {
    print("Failed to save with custom encoder: \(error)")
}
```

### Codable Best Practices and Advanced Patterns

**1. Error Handling and Validation**
```swift
// Comprehensive error handling for Codable
enum CodableError: Error, LocalizedError {
    case missingRequiredField(String)
    case invalidValue(String, Any)
    case typeMismatch(String, Any.Type, Any.Type)
    case dataCorrupted(String)
    
    var errorDescription: String? {
        switch self {
        case .missingRequiredField(let field):
            return "Missing required field: \(field)"
        case .invalidValue(let field, let value):
            return "Invalid value for \(field): \(value)"
        case .typeMismatch(let field, let expected, let actual):
            return "Type mismatch for \(field): expected \(expected), got \(actual)"
        case .dataCorrupted(let reason):
            return "Data corrupted: \(reason)"
        }
    }
}

// Custom validation during decoding
struct ValidatedUser: Codable {
    let id: String
    let name: String
    let email: String
    let age: Int
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        
        id = try container.decode(String.self, forKey: .id)
        name = try container.decode(String.self, forKey: .name)
        email = try container.decode(String.self, forKey: .email)
        age = try container.decode(Int.self, forKey: .age)
        
        // Validation
        try validate()
    }
    
    private func validate() throws {
        if id.isEmpty {
            throw CodableError.invalidValue("id", id)
        }
        
        if name.isEmpty {
            throw CodableError.invalidValue("name", name)
        }
        
        if !email.contains("@") {
            throw CodableError.invalidValue("email", email)
        }
        
        if age < 0 || age > 150 {
            throw CodableError.invalidValue("age", age)
        }
    }
    
    enum CodingKeys: String, CodingKey {
        case id, name, email, age
    }
}
```

**2. Performance Optimization**
```swift
// Reuse encoders and decoders for better performance
class OptimizedStorageManager {
    private let encoder: JSONEncoder
    private let decoder: JSONDecoder
    
    init() {
        encoder = JSONEncoder()
        encoder.outputFormatting = .sortedKeys // Consistent output for caching
        
        decoder = JSONDecoder()
        decoder.dateDecodingStrategy = .iso8601
    }
    
    // Cache encoded data for frequently accessed objects
    private var cache: [String: Data] = [:]
    
    func saveWithCache<T: Codable>(_ object: T, forKey key: String) throws {
        let data = try encoder.encode(object)
        cache[key] = data
        UserDefaults.standard.set(data, forKey: key)
    }
    
    func loadWithCache<T: Codable>(_ type: T.Type, forKey key: String) throws -> T? {
        // Check cache first
        if let cachedData = cache[key] {
            return try decoder.decode(type, from: cachedData)
        }
        
        // Load from storage
        guard let data = UserDefaults.standard.data(forKey: key) else {
            return nil
        }
        
        // Cache the result
        cache[key] = data
        return try decoder.decode(type, from: data)
    }
    
    func clearCache() {
        cache.removeAll()
    }
}
```

**3. Advanced Encoding Strategies**
```swift
// Custom encoding strategies for different use cases
struct FlexibleEncoder {
    static func createEncoder(for useCase: EncodingUseCase) -> JSONEncoder {
        let encoder = JSONEncoder()
        
        switch useCase {
        case .api:
            encoder.dateEncodingStrategy = .iso8601
            encoder.keyEncodingStrategy = .convertToSnakeCase
            encoder.outputFormatting = []
            
        case .storage:
            encoder.dateEncodingStrategy = .formatted(DateFormatter())
            encoder.outputFormatting = .prettyPrinted
            
        case .debug:
            encoder.outputFormatting = [.prettyPrinted, .sortedKeys]
            encoder.dateEncodingStrategy = .formatted(DateFormatter())
        }
        
        return encoder
    }
}

enum EncodingUseCase {
    case api
    case storage
    case debug
}

// Usage
let apiEncoder = FlexibleEncoder.createEncoder(for: .api)
let storageEncoder = FlexibleEncoder.createEncoder(for: .storage)
let debugEncoder = FlexibleEncoder.createEncoder(for: .debug)

let user = User(id: "123", name: "John", email: "john@example.com", age: 30)

// Different encodings for different purposes
let apiData = try apiEncoder.encode(user)      // Compact for API
let storageData = try storageEncoder.encode(user) // Pretty for storage
let debugData = try debugEncoder.encode(user)   // Formatted for debugging
```

This comprehensive coverage of Codable shows how it provides a powerful, type-safe foundation for data serialization in Swift. The key benefits are:

1. **Type Safety**: Compile-time guarantees about data structure
2. **Automatic Synthesis**: No manual serialization code needed
3. **Performance**: Optimized encoding/decoding with caching
4. **Flexibility**: Custom encoding strategies for different use cases
5. **Error Handling**: Comprehensive error handling and validation

Codable is essential for modern Swift development, especially when working with web APIs, local storage, and data interchange between different systems.

---

## Combine Framework - Deep Dive

The Combine framework is Apple's reactive programming solution for Swift, providing a declarative approach to handling asynchronous events and data streams. It enables you to write cleaner, more maintainable code by treating data as a stream of events that can be transformed, filtered, and combined using functional programming principles.

### Understanding Combine Fundamentals

**What Combine Actually Is:**
Combine is a framework that implements the Publisher-Subscriber pattern, allowing you to create data streams that automatically propagate changes to interested parties. It's designed to work seamlessly with SwiftUI and provides a unified way to handle asynchronous operations.

```swift
// ❌ Without Combine - traditional callback-based approach
class UserManager {
    var onUserUpdate: ((User) -> Void)?
    var onError: ((Error) -> Void)?
    
    func fetchUser(id: String) {
        // Simulate network request
        DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
            let user = User(id: id, name: "John Doe", email: "john@example.com")
            self.onUserUpdate?(user)
        }
    }
}

// ❌ Problems:
// 1. Callback hell with nested closures
// 2. Difficult to chain operations
// 3. Hard to cancel operations
// 4. No built-in error handling
// 5. Memory management issues

// ✅ With Combine - reactive, composable streams
class UserManager {
    private let cancellables = Set<AnyCancellable>()
    
    func fetchUser(id: String) -> AnyPublisher<User, Error> {
        return Future<User, Error> { promise in
            DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
                let user = User(id: id, name: "John Doe", email: "john@example.com")
                promise(.success(user))
            }
        }
        .eraseToAnyPublisher()
    }
}

// Usage with Combine
let userManager = UserManager()
userManager.fetchUser(id: "123")
    .sink(
        receiveCompletion: { completion in
            switch completion {
            case .finished:
                print("User fetch completed")
            case .failure(let error):
                print("Error: \(error)")
            }
        },
        receiveValue: { user in
            print("Received user: \(user.name)")
        }
    )
    .store(in: &cancellables)
```

**Core Combine Concepts:**
```swift
// 1. Publisher - Emits values over time
protocol Publisher {
    associatedtype Output
    associatedtype Failure: Error
    
    func receive<S>(subscriber: S) where S: Subscriber, S.Input == Output, S.Failure == Failure
}

// 2. Subscriber - Receives values from publishers
protocol Subscriber {
    associatedtype Input
    associatedtype Failure: Error
    
    func receive(subscription: Subscription)
    func receive(_ input: Input) -> SubscriberDemand
    func receive(completion: SubscriberCompletion<Failure>)
}

// 3. Subscription - Controls the flow of data
protocol Subscription {
    func request(_ demand: SubscriberDemand)
    func cancel()
}

// 4. AnyCancellable - Manages subscription lifecycle
class AnyCancellable {
    func cancel()
}
```

### Basic Combine Operations

**Creating Publishers:**
```swift
// Just - Emits a single value then completes
let justPublisher = Just("Hello, Combine!")
justPublisher
    .sink(receiveValue: { value in
        print("Just: \(value)")
    })
    .store(in: &cancellables)

// Future - Emits a single value asynchronously
let futurePublisher = Future<String, Never> { promise in
    DispatchQueue.global().asyncAfter(deadline: .now() + 1) {
        promise(.success("Future completed"))
    }
}

futurePublisher
    .sink(receiveValue: { value in
        print("Future: \(value)")
    })
    .store(in: &cancellables)

// PassthroughSubject - Manual value emission
let subject = PassthroughSubject<String, Never>()
subject
    .sink(receiveValue: { value in
        print("Subject: \(value)")
    })
    .store(in: &cancellables)

// Emit values
subject.send("First value")
subject.send("Second value")
subject.send(completion: .finished)

// CurrentValueSubject - Holds current value and emits changes
let currentSubject = CurrentValueSubject<String, Never>("Initial value")
currentSubject
    .sink(receiveValue: { value in
        print("Current: \(value)")
    })
    .store(in: &cancellables)

currentSubject.send("Updated value")
print("Current value: \(currentSubject.value)")

// Timer - Emits values at regular intervals
let timerPublisher = Timer.publish(every: 1.0, on: .main, in: .common)
    .autoconnect()

timerPublisher
    .sink(receiveValue: { date in
        print("Timer: \(date)")
    })
    .store(in: &cancellables)

// URLSession data task publisher
let url = URL(string: "https://api.example.com/users")!
let dataPublisher = URLSession.shared.dataTaskPublisher(for: url)
    .map(\.data)
    .decode(type: [User].self, decoder: JSONDecoder())
    .eraseToAnyPublisher()

dataPublisher
    .sink(
        receiveCompletion: { completion in
            if case .failure(let error) = completion {
                print("Network error: \(error)")
            }
        },
        receiveValue: { users in
            print("Received \(users.count) users")
        }
    )
    .store(in: &cancellables)
```

**Transforming Publishers:**
```swift
// Map - Transform values
let numberPublisher = Just(42)
numberPublisher
    .map { $0 * 2 }
    .map { "The result is \($0)" }
    .sink(receiveValue: { value in
        print(value) // "The result is 84"
    })
    .store(in: &cancellables)

// CompactMap - Transform and filter out nil values
let stringNumbers = ["1", "2", "three", "4", "five"]
let compactMapPublisher = stringNumbers.publisher
    .compactMap { Int($0) }
    .sink(receiveValue: { number in
        print("Valid number: \(number)")
    })
    .store(in: &cancellables)

// FlatMap - Transform to new publishers
let userIDs = ["1", "2", "3"]
let flatMapPublisher = userIDs.publisher
    .flatMap { userID in
        userManager.fetchUser(id: userID)
    }
    .sink(receiveValue: { user in
        print("Fetched user: \(user.name)")
    })
    .store(in: &cancellables)

// SwitchToLatest - Switch to latest publisher
let searchText = PassthroughSubject<String, Never>()
let switchToLatestPublisher = searchText
    .debounce(for: .milliseconds(300), scheduler: DispatchQueue.main)
    .removeDuplicates()
    .flatMap { searchTerm in
        userManager.searchUsers(query: searchTerm)
    }
    .switchToLatest()
    .sink(receiveValue: { users in
        print("Search results: \(users.count) users")
    })
    .store(in: &cancellables)

// Emit search terms
searchText.send("john")
searchText.send("jane")
searchText.send("john") // This will cancel the previous search
```

**Filtering Publishers:**
```swift
// Filter - Only emit values that pass a condition
let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
numbers.publisher
    .filter { $0 % 2 == 0 }
    .sink(receiveValue: { number in
        print("Even number: \(number)")
    })
    .store(in: &cancellables)

// RemoveDuplicates - Remove consecutive duplicate values
let duplicateNumbers = [1, 1, 2, 2, 3, 3, 4, 4, 5, 5]
duplicateNumbers.publisher
    .removeDuplicates()
    .sink(receiveValue: { number in
        print("Unique number: \(number)")
    })
    .store(in: &cancellables)

// First - Emit only the first value
numbers.publisher
    .first { $0 > 5 }
    .sink(receiveValue: { number in
        print("First number > 5: \(number)")
    })
    .store(in: &cancellables)

// Last - Emit only the last value
numbers.publisher
    .last { $0 < 5 }
    .sink(receiveValue: { number in
        print("Last number < 5: \(number)")
    })
    .store(in: &cancellables)

// DropFirst - Skip the first N values
numbers.publisher
    .dropFirst(3)
    .sink(receiveValue: { number in
        print("After dropping first 3: \(number)")
    })
    .store(in: &cancellables)

// Prefix - Take only the first N values
numbers.publisher
    .prefix(3)
    .sink(receiveValue: { number in
        print("First 3 numbers: \(number)")
    })
    .store(in: &cancellables)
```

**Combining Publishers:**
```swift
// CombineLatest - Combine latest values from multiple publishers
let nameSubject = PassthroughSubject<String, Never>()
let ageSubject = PassthroughSubject<Int, Never>()

Publishers.CombineLatest(nameSubject, ageSubject)
    .map { name, age in
        "\(name) is \(age) years old"
    }
    .sink(receiveValue: { description in
        print(description)
    })
    .store(in: &cancellables)

// Emit values
nameSubject.send("John")
ageSubject.send(25)
nameSubject.send("Jane")
ageSubject.send(30)

// Merge - Merge multiple publishers into one
let publisher1 = PassthroughSubject<String, Never>()
let publisher2 = PassthroughSubject<String, Never>()

Publishers.Merge(publisher1, publisher2)
    .sink(receiveValue: { value in
        print("Merged: \(value)")
    })
    .store(in: &cancellables)

publisher1.send("From publisher 1")
publisher2.send("From publisher 2")
publisher1.send("Another from 1")

// Zip - Pair values from multiple publishers
let zipPublisher = Publishers.Zip(nameSubject, ageSubject)
    .sink(receiveValue: { name, age in
        print("Zipped: \(name) - \(age)")
    })
    .store(in: &cancellables)

// Switch - Switch between publishers
let switchPublisher = PassthroughSubject<AnyPublisher<String, Never>, Never>()
switchPublisher
    .switchToLatest()
    .sink(receiveValue: { value in
        print("Switched: \(value)")
    })
    .store(in: &cancellables)

let publisherA = Just("Publisher A").eraseToAnyPublisher()
let publisherB = Just("Publisher B").eraseToAnyPublisher()

switchPublisher.send(publisherA)
switchPublisher.send(publisherB) // This will cancel publisherA
```

### Advanced Combine Patterns

**Pattern 1: MVVM with Combine**
```swift
// User model
struct User: Codable, Identifiable {
    let id: String
    let name: String
    let email: String
    let avatar: String?
}

// User view model using Combine
class UserViewModel: ObservableObject {
    @Published var users: [User] = []
    @Published var isLoading = false
    @Published var errorMessage: String?
    
    private let userService: UserService
    private var cancellables = Set<AnyCancellable>()
    
    init(userService: UserService) {
        self.userService = userService
        setupBindings()
    }
    
    private func setupBindings() {
        // Auto-refresh users when search changes
        $searchText
            .debounce(for: .milliseconds(300), scheduler: DispatchQueue.main)
            .removeDuplicates()
            .filter { !$0.isEmpty }
            .flatMap { [weak self] searchTerm in
                self?.userService.searchUsers(query: searchTerm) ?? Empty()
            }
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.isLoading = false
                    if case .failure(let error) = completion {
                        self?.errorMessage = error.localizedDescription
                    }
                },
                receiveValue: { [weak self] users in
                    self?.users = users
                }
            )
            .store(in: &cancellables)
    }
    
    @Published var searchText = ""
    
    func fetchUsers() {
        isLoading = true
        errorMessage = nil
        
        userService.fetchUsers()
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.isLoading = false
                    if case .failure(let error) = completion {
                        self?.errorMessage = error.localizedDescription
                    }
                },
                receiveValue: { [weak self] users in
                    self?.users = users
                }
            )
            .store(in: &cancellables)
    }
    
    func createUser(_ user: User) {
        userService.createUser(user)
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    if case .failure(let error) = completion {
                        self?.errorMessage = error.localizedDescription
                    }
                },
                receiveValue: { [weak self] newUser in
                    self?.users.append(newUser)
                }
            )
            .store(in: &cancellables)
    }
    
    func deleteUser(at indexSet: IndexSet) {
        guard let index = indexSet.first else { return }
        let user = users[index]
        
        userService.deleteUser(id: user.id)
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    if case .failure(let error) = completion {
                        self?.errorMessage = error.localizedDescription
                    }
                },
                receiveValue: { [weak self] _ in
                    self?.users.remove(at: index)
                }
            )
            .store(in: &cancellables)
    }
}

// User service using Combine
class UserService {
    private let apiClient: APIClient
    
    init(apiClient: APIClient) {
        self.apiClient = apiClient
    }
    
    func fetchUsers() -> AnyPublisher<[User], Error> {
        return apiClient.request(endpoint: "/users")
    }
    
    func searchUsers(query: String) -> AnyPublisher<[User], Error> {
        return apiClient.request(endpoint: "/users/search?q=\(query)")
    }
    
    func createUser(_ user: User) -> AnyPublisher<User, Error> {
        return apiClient.request(endpoint: "/users", method: .POST, body: user)
    }
    
    func deleteUser(id: String) -> AnyPublisher<Void, Error> {
        return apiClient.request(endpoint: "/users/\(id)", method: .DELETE)
    }
}
```

**Pattern 2: Form Validation with Combine**
```swift
// Form validation using Combine
class RegistrationFormViewModel: ObservableObject {
    @Published var email = ""
    @Published var password = ""
    @Published var confirmPassword = ""
    @Published var isFormValid = false
    @Published var emailError = ""
    @Published var passwordError = ""
    
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        setupValidation()
    }
    
    private func setupValidation() {
        // Email validation
        $email
            .debounce(for: .milliseconds(300), scheduler: DispatchQueue.main)
            .removeDuplicates()
            .map { email in
                if email.isEmpty {
                    return "Email is required"
                } else if !email.contains("@") {
                    return "Invalid email format"
                } else {
                    return ""
                }
            }
            .assign(to: \.emailError, on: self)
            .store(in: &cancellables)
        
        // Password validation
        $password
            .debounce(for: .milliseconds(300), scheduler: DispatchQueue.main)
            .removeDuplicates()
            .map { password in
                if password.isEmpty {
                    return "Password is required"
                } else if password.count < 8 {
                    return "Password must be at least 8 characters"
                } else if !password.contains(where: { $0.isNumber }) {
                    return "Password must contain a number"
                } else {
                    return ""
                }
            }
            .assign(to: \.passwordError, on: self)
            .store(in: &cancellables)
        
        // Form validation
        Publishers.CombineLatest3($email, $password, $confirmPassword)
            .map { email, password, confirmPassword in
                let isEmailValid = self.emailError.isEmpty && !email.isEmpty
                let isPasswordValid = self.passwordError.isEmpty && !password.isEmpty
                let isConfirmPasswordValid = password == confirmPassword && !confirmPassword.isEmpty
                
                return isEmailValid && isPasswordValid && isConfirmPasswordValid
            }
            .assign(to: \.isFormValid, on: self)
            .store(in: &cancellables)
    }
    
    func submitForm() -> AnyPublisher<Bool, Error> {
        guard isFormValid else {
            return Fail(error: ValidationError.formInvalid)
                .eraseToAnyPublisher()
        }
        
        let user = CreateUserRequest(
            email: email,
            password: password
        )
        
        return userService.createUser(user)
            .map { _ in true }
            .eraseToAnyPublisher()
    }
}

enum ValidationError: Error, LocalizedError {
    case formInvalid
    
    var errorDescription: String? {
        switch self {
        case .formInvalid:
            return "Form validation failed"
        }
    }
}
```

**Pattern 3: Network Request Management**
```swift
// Network request manager with Combine
class NetworkManager {
    private let session: URLSession
    private let requestQueue = DispatchQueue(label: "network.queue", qos: .utility)
    private var activeRequests: [String: AnyCancellable] = [:]
    
    init(session: URLSession = .shared) {
        self.session = session
    }
    
    func request<T: Codable>(
        endpoint: String,
        method: HTTPMethod = .GET,
        body: Encodable? = nil,
        headers: [String: String] = [:],
        requestID: String? = nil
    ) -> AnyPublisher<T, Error> {
        
        let id = requestID ?? UUID().uuidString
        
        // Cancel existing request with same ID
        cancelRequest(id: id)
        
        return Future<T, Error> { [weak self] promise in
            guard let self = self else { return }
            
            guard let url = URL(string: endpoint) else {
                promise(.failure(NetworkError.invalidURL))
                return
            }
            
            var request = URLRequest(url: url)
            request.httpMethod = method.rawValue
            
            // Add headers
            var allHeaders = headers
            allHeaders["Content-Type"] = "application/json"
            request.allHTTPHeaderFields = allHeaders
            
            // Add body
            if let body = body {
                do {
                    let encoder = JSONEncoder()
                    request.httpBody = try encoder.encode(body)
                } catch {
                    promise(.failure(NetworkError.encodingError(error)))
                    return
                }
            }
            
            // Make request
            let cancellable = self.session.dataTaskPublisher(for: request)
                .tryMap { data, response in
                    guard let httpResponse = response as? HTTPURLResponse else {
                        throw NetworkError.invalidResponse
                    }
                    
                    guard 200...299 ~= httpResponse.statusCode else {
                        throw NetworkError.serverError(httpResponse.statusCode, data)
                    }
                    
                    return data
                }
                .decode(type: T.self, decoder: JSONDecoder())
                .receive(on: DispatchQueue.main)
                .sink(
                    receiveCompletion: { completion in
                        if case .failure(let error) = completion {
                            promise(.failure(error))
                        }
                    },
                    receiveValue: { value in
                        promise(.success(value))
                    }
                )
            
            // Store the cancellable
            self.activeRequests[id] = cancellable
        }
        .handleEvents(
            receiveCompletion: { [weak self] _ in
                self?.activeRequests.removeValue(forKey: id)
            }
        )
        .eraseToAnyPublisher()
    }
    
    func cancelRequest(id: String) {
        activeRequests[id]?.cancel()
        activeRequests.removeValue(forKey: id)
    }
    
    func cancelAllRequests() {
        activeRequests.values.forEach { $0.cancel() }
        activeRequests.removeAll()
    }
}

enum NetworkError: Error, LocalizedError {
    case invalidURL
    case invalidResponse
    case serverError(Int, Data)
    case encodingError(Error)
    case decodingError(Error)
    
    var errorDescription: String? {
        switch self {
        case .invalidURL:
            return "Invalid URL"
        case .invalidResponse:
            return "Invalid response"
        case .serverError(let code, _):
            return "Server error: \(code)"
        case .encodingError(let error):
            return "Encoding error: \(error.localizedDescription)"
        case .decodingError(let error):
            return "Decoding error: \(error.localizedDescription)"
        }
    }
}
```

### Real-World Combine Applications

**Application 1: Real-time Chat Application**
```swift
// Chat message model
struct ChatMessage: Codable, Identifiable {
    let id: String
    let senderID: String
    let content: String
    let timestamp: Date
    let messageType: MessageType
    
    enum MessageType: String, Codable {
        case text
        case image
        case file
    }
}

// Chat service using Combine
class ChatService {
    private let webSocketManager: WebSocketManager
    private let messageSubject = PassthroughSubject<ChatMessage, Never>()
    
    init(webSocketManager: WebSocketManager) {
        self.webSocketManager = webSocketManager
        setupWebSocket()
    }
    
    private func setupWebSocket() {
        webSocketManager.messagePublisher
            .compactMap { data -> ChatMessage? in
                try? JSONDecoder().decode(ChatMessage.self, from: data)
            }
            .sink(receiveValue: { [weak self] message in
                self?.messageSubject.send(message)
            })
            .store(in: &cancellables)
    }
    
    var messagePublisher: AnyPublisher<ChatMessage, Never> {
        return messageSubject.eraseToAnyPublisher()
    }
    
    func sendMessage(_ message: ChatMessage) {
        do {
            let data = try JSONEncoder().encode(message)
            webSocketManager.send(data: data)
        } catch {
            print("Failed to encode message: \(error)")
        }
    }
}

// Chat view model
class ChatViewModel: ObservableObject {
    @Published var messages: [ChatMessage] = []
    @Published var isConnected = false
    @Published var errorMessage: String?
    
    private let chatService: ChatService
    private var cancellables = Set<AnyCancellable>()
    
    init(chatService: ChatService) {
        self.chatService = chatService
        setupBindings()
    }
    
    private func setupBindings() {
        // Listen for new messages
        chatService.messagePublisher
            .receive(on: DispatchQueue.main)
            .sink(receiveValue: { [weak self] message in
                self?.messages.append(message)
            })
            .store(in: &cancellables)
        
        // Listen for connection status
        webSocketManager.connectionPublisher
            .receive(on: DispatchQueue.main)
            .assign(to: \.isConnected, on: self)
            .store(in: &cancellables)
    }
    
    func sendMessage(content: String, type: ChatMessage.MessageType = .text) {
        let message = ChatMessage(
            id: UUID().uuidString,
            senderID: currentUserID,
            content: content,
            timestamp: Date(),
            messageType: type
        )
        
        chatService.sendMessage(message)
    }
}
```

**Application 2: Data Synchronization**
```swift
// Data sync service using Combine
class DataSyncService {
    private let localDatabase: LocalDatabase
    private let remoteAPI: RemoteAPI
    private let syncSubject = PassthroughSubject<SyncEvent, Never>()
    
    var syncPublisher: AnyPublisher<SyncEvent, Never> {
        return syncSubject.eraseToAnyPublisher()
    }
    
    func syncData() -> AnyPublisher<SyncResult, Error> {
        return Publishers.CombineLatest3(
            fetchLocalData(),
            fetchRemoteData(),
            fetchLastSyncTimestamp()
        )
        .flatMap { localData, remoteData, lastSync in
            self.performSync(local: localData, remote: remoteData, since: lastSync)
        }
        .handleEvents(
            receiveOutput: { [weak self] result in
                self?.syncSubject.send(.syncCompleted(result))
            },
            receiveCompletion: { [weak self] completion in
                if case .failure(let error) = completion {
                    self?.syncSubject.send(.syncFailed(error))
                }
            }
        )
        .eraseToAnyPublisher()
    }
    
    private func performSync(
        local: [DataItem],
        remote: [DataItem],
        since: Date
    ) -> AnyPublisher<SyncResult, Error> {
        
        // Find conflicts
        let conflicts = findConflicts(local: local, remote: remote)
        
        // Resolve conflicts
        return resolveConflicts(conflicts)
            .flatMap { resolvedData in
                // Apply resolved data
                self.applyResolvedData(resolvedData)
            }
            .map { _ in
                SyncResult(
                    itemsSynced: remote.count,
                    conflictsResolved: conflicts.count,
                    timestamp: Date()
                )
            }
            .eraseToAnyPublisher()
    }
}

enum SyncEvent {
    case syncStarted
    case syncCompleted(SyncResult)
    case syncFailed(Error)
    case conflictDetected(DataConflict)
}

struct SyncResult {
    let itemsSynced: Int
    let conflictsResolved: Int
    let timestamp: Date
}
```

### Combine Best Practices and Performance

**1. Memory Management**
```swift
// Proper memory management with Combine
class MemoryManagedViewModel: ObservableObject {
    @Published var data: [String] = []
    
    private var cancellables = Set<AnyCancellable>()
    
    func setupDataStream() {
        // Store cancellables to prevent memory leaks
        dataPublisher
            .receive(on: DispatchQueue.main)
            .assign(to: \.data, on: self)
            .store(in: &cancellables)
    }
    
    deinit {
        // Cancellables are automatically cancelled when stored in Set
        // But you can also manually cancel if needed
        cancellables.forEach { $0.cancel() }
    }
}
```

**2. Performance Optimization**
```swift
// Performance optimization with Combine
class OptimizedViewModel: ObservableObject {
    @Published var searchResults: [User] = []
    
    private var cancellables = Set<AnyCancellable>()
    
    func setupSearch() {
        $searchText
            .debounce(for: .milliseconds(300), scheduler: DispatchQueue.main)
            .removeDuplicates()
            .filter { !$0.isEmpty }
            .flatMap { searchTerm in
                userService.searchUsers(query: searchTerm)
                    .subscribe(on: DispatchQueue.global(qos: .userInitiated))
                    .receive(on: DispatchQueue.main)
            }
            .sink(receiveValue: { [weak self] users in
                self?.searchResults = users
            })
            .store(in: &cancellables)
    }
}
```

**3. Error Handling**
```swift
// Comprehensive error handling with Combine
extension Publisher where Failure == Error {
    func handleError(_ handler: @escaping (Error) -> Void) -> AnyPublisher<Output, Never> {
        return self
            .catch { error in
                handler(error)
                return Empty()
            }
            .eraseToAnyPublisher()
    }
}

// Usage
userService.fetchUsers()
    .handleError { error in
        print("User fetch failed: \(error)")
        // Show user-friendly error message
        showErrorAlert(error.localizedDescription)
    }
    .sink(receiveValue: { users in
        self.users = users
    })
    .store(in: &cancellables)
```

This comprehensive coverage of the Combine Framework shows how it provides a powerful, reactive programming solution for Swift. The key benefits are:

1. **Declarative Programming**: Write cleaner, more maintainable code
2. **Automatic Memory Management**: Built-in subscription lifecycle management
3. **Composability**: Easily combine and transform data streams
4. **Performance**: Efficient handling of asynchronous operations
5. **SwiftUI Integration**: Seamless integration with SwiftUI's reactive system

Combine is essential for modern iOS development, especially when building complex user interfaces and managing asynchronous data flows.

---

## SwiftUI Integration - Deep Dive

SwiftUI is Apple's modern declarative framework for building user interfaces across all Apple platforms. It represents a fundamental shift from the imperative UIKit approach to a reactive, data-driven architecture that seamlessly integrates with Swift's type system, property wrappers, and functional programming concepts.

### Understanding SwiftUI Fundamentals

**What SwiftUI Actually Is:**
SwiftUI is a declarative UI framework that automatically updates the interface when your data changes. It's built on top of Swift's type system and leverages property wrappers, protocols, and generics to create a reactive, state-driven user interface system.

```swift
// ❌ Without SwiftUI - imperative UIKit approach
class UserViewController: UIViewController {
    var user: User?
    var nameLabel: UILabel!
    var emailLabel: UILabel!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
        updateUI()
    }
    
    func setupUI() {
        nameLabel = UILabel()
        nameLabel.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(nameLabel)
        
        emailLabel = UILabel()
        emailLabel.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(emailLabel)
        
        // Manual constraint setup...
        NSLayoutConstraint.activate([
            nameLabel.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 20),
            nameLabel.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            // ... more constraints
        ])
    }
    
    func updateUI() {
        nameLabel.text = user?.name ?? "No name"
        emailLabel.text = user?.email ?? "No email"
    }
    
    func userDidUpdate(_ newUser: User) {
        user = newUser
        updateUI() // Must remember to call this
    }
}

// ❌ Problems:
// 1. Manual UI setup and constraint management
// 2. Must remember to update UI when data changes
// 3. Boilerplate code for common UI patterns
// 4. Difficult to preview and iterate
// 5. No automatic state synchronization

// ✅ With SwiftUI - declarative, reactive approach
struct UserView: View {
    let user: User
    
    var body: some View {
        VStack(alignment: .leading, spacing: 16) {
            Text(user.name)
                .font(.title)
                .foregroundColor(.primary)
            
            Text(user.email)
                .font(.body)
                .foregroundColor(.secondary)
        }
        .padding()
    }
}

// ✅ Benefits:
// 1. Automatic UI updates when data changes
// 2. Declarative syntax that's easy to read
// 3. Built-in layout system
// 4. Live previews for rapid iteration
// 5. Automatic state management
```

**Core SwiftUI Concepts:**
```swift
// 1. View Protocol - The foundation of all SwiftUI views
protocol View {
    associatedtype Body: View
    var body: Body { get }
}

// 2. ViewBuilder - Function builder for creating view hierarchies
@ViewBuilder
func createUserInfo(user: User) -> some View {
    VStack {
        Text(user.name)
        Text(user.email)
    }
}

// 3. Some View - Opaque return type for view composition
struct ContentView: View {
    var body: some View {
        // The compiler infers the exact type
        VStack {
            Text("Hello")
            Image(systemName: "star.fill")
        }
    }
}

// 4. View Modifiers - Chainable transformations
Text("Hello, World!")
    .font(.title)
    .foregroundColor(.blue)
    .padding()
    .background(Color.yellow)
    .cornerRadius(10)
```

### SwiftUI State Management

**Property Wrappers in SwiftUI:**
```swift
// @State - Local view state
struct CounterView: View {
    @State private var count = 0
    
    var body: some View {
        VStack {
            Text("Count: \(count)")
                .font(.title)
            
            Button("Increment") {
                count += 1
            }
            .buttonStyle(.borderedProminent)
            
            Button("Decrement") {
                count -= 1
            }
            .buttonStyle(.bordered)
        }
        .padding()
    }
}

// @Binding - Pass state to child views
struct CounterView: View {
    @State private var count = 0
    
    var body: some View {
        VStack {
            Text("Count: \(count)")
                .font(.title)
            
            CounterControls(count: $count)
        }
        .padding()
    }
}

struct CounterControls: View {
    @Binding var count: Int
    
    var body: some View {
        HStack {
            Button("Increment") {
                count += 1
            }
            .buttonStyle(.borderedProminent)
            
            Button("Decrement") {
                count -= 1
            }
            .buttonStyle(.bordered)
        }
    }
}

// @ObservedObject - External observable objects
class UserViewModel: ObservableObject {
    @Published var user: User?
    @Published var isLoading = false
    @Published var errorMessage: String?
    
    func fetchUser() {
        isLoading = true
        // Simulate network request
        DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
            self.user = User(id: "1", name: "John Doe", email: "john@example.com")
            self.isLoading = false
        }
    }
}

struct UserProfileView: View {
    @ObservedObject var viewModel: UserViewModel
    
    var body: some View {
        VStack {
            if viewModel.isLoading {
                ProgressView("Loading...")
            } else if let user = viewModel.user {
                VStack(alignment: .leading, spacing: 16) {
                    Text(user.name)
                        .font(.title)
                    
                    Text(user.email)
                        .font(.body)
                        .foregroundColor(.secondary)
                }
            } else if let errorMessage = viewModel.errorMessage {
                Text(errorMessage)
                    .foregroundColor(.red)
            }
            
            Button("Fetch User") {
                viewModel.fetchUser()
            }
            .buttonStyle(.borderedProminent)
        }
        .padding()
        .onAppear {
            viewModel.fetchUser()
        }
    }
}

// @StateObject - Create and own observable objects
struct ContentView: View {
    @StateObject private var userViewModel = UserViewModel()
    
    var body: some View {
        UserProfileView(viewModel: userViewModel)
    }
}

// @EnvironmentObject - Dependency injection through environment
class AppSettings: ObservableObject {
    @Published var theme: Theme = .light
    @Published var language: Language = .english
    
    enum Theme: String, CaseIterable {
        case light, dark, system
    }
    
    enum Language: String, CaseIterable {
        case english, spanish, french
    }
}

struct SettingsView: View {
    @EnvironmentObject var settings: AppSettings
    
    var body: some View {
        Form {
            Section("Appearance") {
                Picker("Theme", selection: $settings.theme) {
                    ForEach(AppSettings.Theme.allCases, id: \.self) { theme in
                        Text(theme.rawValue.capitalized)
                    }
                }
            }
            
            Section("Language") {
                Picker("Language", selection: $settings.language) {
                    ForEach(AppSettings.Language.allCases, id: \.self) { language in
                        Text(language.rawValue.capitalized)
                    }
                }
            }
        }
        .navigationTitle("Settings")
    }
}

// @Environment - Access environment values
struct ContentView: View {
    @Environment(\.colorScheme) var colorScheme
    @Environment(\.sizeCategory) var sizeCategory
    
    var body: some View {
        VStack {
            Text("Current theme: \(colorScheme == .dark ? "Dark" : "Light")")
            Text("Size category: \(sizeCategory.description)")
        }
        .padding()
    }
}
```

**Advanced State Management Patterns:**
```swift
// Pattern 1: MVVM with SwiftUI
class ProductListViewModel: ObservableObject {
    @Published var products: [Product] = []
    @Published var isLoading = false
    @Published var searchText = ""
    @Published var selectedCategory: ProductCategory?
    
    private let productService: ProductService
    private var cancellables = Set<AnyCancellable>()
    
    init(productService: ProductService) {
        self.productService = productService
        setupBindings()
    }
    
    private func setupBindings() {
        // Auto-filter products when search or category changes
        Publishers.CombineLatest($searchText, $selectedCategory)
            .debounce(for: .milliseconds(300), scheduler: DispatchQueue.main)
            .flatMap { [weak self] searchText, category in
                self?.productService.searchProducts(query: searchText, category: category) ?? Empty()
            }
            .receive(on: DispatchQueue.main)
            .assign(to: \.products, on: self)
            .store(in: &cancellables)
    }
    
    var filteredProducts: [Product] {
        if searchText.isEmpty && selectedCategory == nil {
            return products
        }
        
        return products.filter { product in
            let matchesSearch = searchText.isEmpty || 
                product.name.localizedCaseInsensitiveContains(searchText)
            let matchesCategory = selectedCategory == nil || 
                product.category == selectedCategory
            
            return matchesSearch && matchesCategory
        }
    }
    
    func loadProducts() {
        isLoading = true
        
        productService.fetchProducts()
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.isLoading = false
                },
                receiveValue: { [weak self] products in
                    self?.products = products
                }
            )
            .store(in: &cancellables)
    }
}

struct ProductList: View {
    @StateObject private var viewModel: ProductListViewModel
    @State private var showingAddProduct = false
    
    init(productService: ProductService) {
        _viewModel = StateObject(wrappedValue: ProductListViewModel(productService: productService))
    }
    
    var body: some View {
        NavigationView {
            VStack {
                // Search and filter controls
                SearchAndFilterView(
                    searchText: $viewModel.searchText,
                    selectedCategory: $viewModel.selectedCategory
                )
                
                // Product list
                if viewModel.isLoading {
                    ProgressView("Loading products...")
                        .frame(maxWidth: .infinity, maxHeight: .infinity)
                } else if viewModel.filteredProducts.isEmpty {
                    EmptyStateView()
                } else {
                    List(viewModel.filteredProducts) { product in
                        ProductRow(product: product)
                    }
                }
            }
            .navigationTitle("Products")
            .toolbar {
                ToolbarItem(placement: .navigationBarTrailing) {
                    Button("Add") {
                        showingAddProduct = true
                    }
                }
            }
            .sheet(isPresented: $showingAddProduct) {
                AddProductView()
            }
        }
        .onAppear {
            viewModel.loadProducts()
        }
    }
}

// Pattern 2: State restoration and persistence
class AppState: ObservableObject {
    @Published var currentUser: User?
    @Published var isAuthenticated = false
    @Published var appSettings: AppSettings
    
    private let userDefaults = UserDefaults.standard
    private let encoder = JSONEncoder()
    private let decoder = JSONDecoder()
    
    init() {
        // Load saved settings
        if let settingsData = userDefaults.data(forKey: "appSettings"),
           let settings = try? decoder.decode(AppSettings.self, from: settingsData) {
            self.appSettings = settings
        } else {
            self.appSettings = AppSettings()
        }
        
        // Load saved user
        if let userData = userDefaults.data(forKey: "currentUser"),
           let user = try? decoder.decode(User.self, from: userData) {
            self.currentUser = user
            self.isAuthenticated = true
        }
    }
    
    func saveSettings() {
        if let data = try? encoder.encode(appSettings) {
            userDefaults.set(data, forKey: "appSettings")
        }
    }
    
    func saveUser() {
        if let user = currentUser,
           let data = try? encoder.encode(user) {
            userDefaults.set(data, forKey: "currentUser")
        }
    }
    
    func signOut() {
        currentUser = nil
        isAuthenticated = false
        userDefaults.removeObject(forKey: "currentUser")
    }
}

// Pattern 3: Complex state coordination
class ShoppingCart: ObservableObject {
    @Published var items: [CartItem] = []
    @Published var isCheckingOut = false
    
    var totalItems: Int {
        items.reduce(0) { $0 + $1.quantity }
    }
    
    var totalPrice: Decimal {
        items.reduce(0) { $0 + ($1.product.price * Decimal($1.quantity)) }
    }
    
    func addItem(_ product: Product, quantity: Int = 1) {
        if let existingIndex = items.firstIndex(where: { $0.product.id == product.id }) {
            items[existingIndex].quantity += quantity
        } else {
            items.append(CartItem(product: product, quantity: quantity))
        }
    }
    
    func removeItem(_ product: Product) {
        items.removeAll { $0.product.id == product.id }
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
    
    func clearCart() {
        items.removeAll()
    }
}

struct CartItem: Identifiable {
    let id = UUID()
    let product: Product
    var quantity: Int
}
```

### SwiftUI View Composition and Layout

**Building Reusable Components:**
```swift
// Reusable card component
struct CardView<Content: View>: View {
    let content: Content
    let backgroundColor: Color
    let cornerRadius: CGFloat
    let shadowRadius: CGFloat
    
    init(
        backgroundColor: Color = .white,
        cornerRadius: CGFloat = 12,
        shadowRadius: CGFloat = 4,
        @ViewBuilder content: () -> Content
    ) {
        self.backgroundColor = backgroundColor
        self.cornerRadius = cornerRadius
        self.shadowRadius = shadowRadius
        self.content = content()
    }
    
    var body: some View {
        content
            .padding()
            .background(backgroundColor)
            .cornerRadius(cornerRadius)
            .shadow(radius: shadowRadius)
    }
}

// Usage
CardView(backgroundColor: .blue.opacity(0.1)) {
    VStack(alignment: .leading, spacing: 8) {
        Text("Featured Product")
            .font(.headline)
        Text("This is a special offer")
            .font(.caption)
            .foregroundColor(.secondary)
    }
}

// Reusable button styles
struct PrimaryButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .padding(.horizontal, 24)
            .padding(.vertical, 12)
            .background(Color.blue)
            .foregroundColor(.white)
            .cornerRadius(8)
            .scaleEffect(configuration.isPressed ? 0.95 : 1.0)
            .animation(.easeInOut(duration: 0.1), value: configuration.isPressed)
    }
}

struct SecondaryButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .padding(.horizontal, 24)
            .padding(.vertical, 12)
            .background(Color.clear)
            .foregroundColor(.blue)
            .overlay(
                RoundedRectangle(cornerRadius: 8)
                    .stroke(Color.blue, lineWidth: 1)
            )
            .scaleEffect(configuration.isPressed ? 0.95 : 1.0)
            .animation(.easeInOut(duration: 0.1), value: configuration.isPressed)
    }
}

// Usage
Button("Primary Action") {
    // Action
}
.buttonStyle(PrimaryButtonStyle())

Button("Secondary Action") {
    // Action
}
.buttonStyle(SecondaryButtonStyle())

// Reusable form components
struct FormTextField: View {
    let title: String
    let placeholder: String
    @Binding var text: String
    let keyboardType: UIKeyboardType
    let textContentType: UITextContentType?
    
    init(
        title: String,
        placeholder: String,
        text: Binding<String>,
        keyboardType: UIKeyboardType = .default,
        textContentType: UITextContentType? = nil
    ) {
        self.title = title
        self.placeholder = placeholder
        self._text = text
        self.keyboardType = keyboardType
        self.textContentType = textContentType
    }
    
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text(title)
                .font(.headline)
                .foregroundColor(.primary)
            
            TextField(placeholder, text: $text)
                .textFieldStyle(RoundedBorderTextFieldStyle())
                .keyboardType(keyboardType)
                .textContentType(textContentType)
        }
    }
}

struct FormSecureField: View {
    let title: String
    let placeholder: String
    @Binding var text: String
    let textContentType: UITextContentType?
    
    init(
        title: String,
        placeholder: String,
        text: Binding<String>,
        textContentType: UITextContentType? = nil
    ) {
        self.title = title
        self.placeholder = placeholder
        self._text = text
        self.textContentType = textContentType
    }
    
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text(title)
                .font(.headline)
                .foregroundColor(.primary)
            
            SecureField(placeholder, text: $text)
                .textFieldStyle(RoundedBorderTextFieldStyle())
                .textContentType(textContentType)
        }
    }
}

// Usage in a form
struct RegistrationForm: View {
    @State private var email = ""
    @State private var password = ""
    @State private var confirmPassword = ""
    
    var body: some View {
        VStack(spacing: 20) {
            FormTextField(
                title: "Email",
                placeholder: "Enter your email",
                text: $email,
                keyboardType: .emailAddress,
                textContentType: .emailAddress
            )
            
            FormSecureField(
                title: "Password",
                placeholder: "Enter your password",
                text: $password,
                textContentType: .password
            )
            
            FormSecureField(
                title: "Confirm Password",
                placeholder: "Confirm your password",
                text: $confirmPassword,
                textContentType: .password
            )
            
            Button("Register") {
                // Registration logic
            }
            .buttonStyle(PrimaryButtonStyle())
            .disabled(!isFormValid)
        }
        .padding()
    }
    
    private var isFormValid: Bool {
        !email.isEmpty && !password.isEmpty && password == confirmPassword
    }
}
```

**Advanced Layout Techniques:**
```swift
// Custom layout using Layout protocol
struct FlowLayout: Layout {
    let spacing: CGFloat
    
    init(spacing: CGFloat = 8) {
        self.spacing = spacing
    }
    
    func sizeThatFits(
        proposal: ProposedViewSize,
        subviews: Subviews,
        cache: inout Void
    ) -> CGSize {
        let result = FlowResult(
            in: proposal.replacingUnspecifiedDimensions().width,
            subviews: subviews,
            spacing: spacing
        )
        return result.size
    }
    
    func placeSubviews(
        in bounds: CGRect,
        proposal: ProposedViewSize,
        subviews: Subviews,
        cache: inout Void
    ) {
        let result = FlowResult(
            in: bounds.width,
            subviews: subviews,
            spacing: spacing
        )
        
        for (index, subview) in subviews.enumerated() {
            let point = CGPoint(
                x: bounds.minX + result.positions[index].x,
                y: bounds.minY + result.positions[index].y
            )
            subview.place(at: point, proposal: .unspecified)
        }
    }
}

struct FlowResult {
    let size: CGSize
    let positions: [CGPoint]
    
    init(in maxWidth: CGFloat, subviews: LayoutSubviews, spacing: CGFloat) {
        var positions: [CGPoint] = []
        var currentX: CGFloat = 0
        var currentY: CGFloat = 0
        var lineHeight: CGFloat = 0
        var maxWidth: CGFloat = 0
        
        for subview in subviews {
            let subviewSize = subview.sizeThatFits(.unspecified)
            
            if currentX + subviewSize.width > maxWidth && currentX > 0 {
                currentX = 0
                currentY += lineHeight + spacing
                lineHeight = 0
            }
            
            positions.append(CGPoint(x: currentX, y: currentY))
            currentX += subviewSize.width + spacing
            lineHeight = max(lineHeight, subviewSize.height)
            maxWidth = max(maxWidth, currentX)
        }
        
        self.positions = positions
        self.size = CGSize(width: maxWidth, height: currentY + lineHeight)
    }
}

// Usage
struct TagCloudView: View {
    let tags: [String]
    
    var body: some View {
        FlowLayout(spacing: 8) {
            ForEach(tags, id: \.self) { tag in
                Text(tag)
                    .padding(.horizontal, 12)
                    .padding(.vertical, 6)
                    .background(Color.blue.opacity(0.1))
                    .foregroundColor(.blue)
                    .cornerRadius(16)
            }
        }
        .padding()
    }
}

// Custom view modifier for animations
struct SlideTransition: ViewModifier {
    let edge: Edge
    let isActive: Bool
    
    func body(content: Content) -> some View {
        content
            .offset(x: isActive ? 0 : (edge == .leading ? -UIScreen.main.bounds.width : UIScreen.main.bounds.width))
            .animation(.easeInOut(duration: 0.3), value: isActive)
    }
}

extension View {
    func slideTransition(edge: Edge, isActive: Bool) -> some View {
        modifier(SlideTransition(edge: edge, isActive: isActive))
    }
}

// Usage
struct SlideView: View {
    @State private var isVisible = false
    
    var body: some View {
        VStack {
            Text("Slide in from left")
                .slideTransition(edge: .leading, isActive: isVisible)
            
            Button("Toggle") {
                isVisible.toggle()
            }
        }
    }
}
```

### SwiftUI Navigation and Presentation

**Navigation Patterns:**
```swift
// Tab-based navigation
struct MainTabView: View {
    @StateObject private var userViewModel = UserViewModel()
    @StateObject private var cartViewModel = CartViewModel()
    
    var body: some View {
        TabView {
            ProductListView()
                .tabItem {
                    Image(systemName: "house.fill")
                    Text("Home")
                }
            
            CartView()
                .tabItem {
                    Image(systemName: "cart.fill")
                    Text("Cart")
                    if cartViewModel.itemCount > 0 {
                        Text("\(cartViewModel.itemCount)")
                    }
                }
            
            ProfileView()
                .tabItem {
                    Image(systemName: "person.fill")
                    Text("Profile")
                }
        }
        .environmentObject(userViewModel)
        .environmentObject(cartViewModel)
    }
}

// Stack-based navigation
struct ProductDetailView: View {
    let product: Product
    @State private var showingReviews = false
    @State private var selectedImageIndex = 0
    
    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 20) {
                // Product images
                TabView(selection: $selectedImageIndex) {
                    ForEach(Array(product.images.enumerated()), id: \.offset) { index, image in
                        AsyncImage(url: image) { image in
                            image
                                .resizable()
                                .aspectRatio(contentMode: .fit)
                        } placeholder: {
                            ProgressView()
                        }
                        .tag(index)
                    }
                }
                .tabViewStyle(PageTabViewStyle())
                .frame(height: 300)
                
                // Product info
                VStack(alignment: .leading, spacing: 16) {
                    Text(product.name)
                        .font(.title)
                        .fontWeight(.bold)
                    
                    Text(product.description)
                        .font(.body)
                        .foregroundColor(.secondary)
                    
                    HStack {
                        Text("$\(product.price, specifier: "%.2f")")
                            .font(.title2)
                            .fontWeight(.semibold)
                        
                        Spacer()
                        
                        Button("Add to Cart") {
                            // Add to cart logic
                        }
                        .buttonStyle(PrimaryButtonStyle())
                    }
                }
                .padding(.horizontal)
            }
        }
        .navigationTitle(product.name)
        .navigationBarTitleDisplayMode(.inline)
        .toolbar {
            ToolbarItem(placement: .navigationBarTrailing) {
                Button("Reviews") {
                    showingReviews = true
                }
            }
        }
        .sheet(isPresented: $showingReviews) {
            ReviewsView(product: product)
        }
    }
}

// Deep linking and navigation
class NavigationCoordinator: ObservableObject {
    @Published var path = NavigationPath()
    @Published var presentedSheet: Sheet?
    @Published var presentedFullScreenCover: FullScreenCover?
    
    enum Sheet: Identifiable {
        case addProduct
        case editProduct(Product)
        case settings
        
        var id: String {
            switch self {
            case .addProduct: return "addProduct"
            case .editProduct(let product): return "editProduct-\(product.id)"
            case .settings: return "settings"
            }
        }
    }
    
    enum FullScreenCover: Identifiable {
        case login
        case onboarding
        
        var id: String {
            switch self {
            case .login: return "login"
            case .onboarding: return "onboarding"
            }
        }
    }
    
    func navigateToProduct(_ product: Product) {
        path.append(product)
    }
    
    func navigateToCategory(_ category: ProductCategory) {
        path.append(category)
    }
    
    func presentSheet(_ sheet: Sheet) {
        presentedSheet = sheet
    }
    
    func presentFullScreenCover(_ cover: FullScreenCover) {
        presentedFullScreenCover = cover
    }
    
    func dismissSheet() {
        presentedSheet = nil
    }
    
    func dismissFullScreenCover() {
        presentedFullScreenCover = nil
    }
    
    func popToRoot() {
        path.removeLast(path.count)
    }
}

struct MainNavigationView: View {
    @StateObject private var coordinator = NavigationCoordinator()
    
    var body: some View {
        NavigationStack(path: $coordinator.path) {
            ProductListView()
                .navigationDestination(for: Product.self) { product in
                    ProductDetailView(product: product)
                }
                .navigationDestination(for: ProductCategory.self) { category in
                    CategoryView(category: category)
                }
        }
        .environmentObject(coordinator)
        .sheet(item: $coordinator.presentedSheet) { sheet in
            switch sheet {
            case .addProduct:
                AddProductView()
            case .editProduct(let product):
                EditProductView(product: product)
            case .settings:
                SettingsView()
            }
        }
        .fullScreenCover(item: $coordinator.presentedFullScreenCover) { cover in
            switch cover {
            case .login:
                LoginView()
            case .onboarding:
                OnboardingView()
            }
        }
    }
}
```

**Presentation and Modals:**
```swift
// Custom modal presentation
struct CustomModal<Content: View>: View {
    let content: Content
    @Binding var isPresented: Bool
    let style: ModalStyle
    
    enum ModalStyle {
        case sheet
        case fullScreen
        case custom(transition: AnyTransition, alignment: Alignment)
        
        static var slideUp: ModalStyle {
            .custom(
                transition: .move(edge: .bottom).combined(with: .opacity),
                alignment: .bottom
            )
        }
        
        static var slideIn: ModalStyle {
            .custom(
                transition: .move(edge: .trailing).combined(with: .opacity),
                alignment: .trailing
            )
        }
    }
    
    init(
        isPresented: Binding<Bool>,
        style: ModalStyle = .sheet,
        @ViewBuilder content: () -> Content
    ) {
        self._isPresented = isPresented
        self.style = style
        self.content = content()
    }
    
    var body: some View {
        ZStack {
            if isPresented {
                Color.black.opacity(0.3)
                    .ignoresSafeArea()
                    .onTapGesture {
                        withAnimation(.easeInOut(duration: 0.3)) {
                            isPresented = false
                        }
                    }
                
                content
                    .transition(transition)
                    .animation(.easeInOut(duration: 0.3), value: isPresented)
            }
        }
    }
    
    private var transition: AnyTransition {
        switch style {
        case .sheet:
            return .move(edge: .bottom).combined(with: .opacity)
        case .fullScreen:
            return .opacity
        case .custom(let transition, _):
            return transition
        }
    }
}

// Usage
struct CustomModalExample: View {
    @State private var showingModal = false
    
    var body: some View {
        VStack {
            Button("Show Custom Modal") {
                withAnimation {
                    showingModal = true
                }
            }
            .buttonStyle(PrimaryButtonStyle())
        }
        .customModal(isPresented: $showingModal, style: .slideUp) {
            VStack(spacing: 20) {
                Text("Custom Modal")
                    .font(.title)
                
                Text("This is a custom modal with slide-up animation")
                    .multilineTextAlignment(.center)
                
                Button("Dismiss") {
                    withAnimation {
                        showingModal = false
                    }
                }
                .buttonStyle(SecondaryButtonStyle())
            }
            .padding()
            .background(Color.white)
            .cornerRadius(20)
            .shadow(radius: 10)
        }
    }
}
```

### SwiftUI Performance and Optimization

**Performance Best Practices:**
```swift
// Lazy loading and pagination
struct LazyProductList: View {
    let products: [Product]
    @State private var visibleProducts: [Product] = []
    @State private var isLoadingMore = false
    
    private let pageSize = 20
    
    var body: some View {
        LazyVStack(spacing: 16) {
            ForEach(visibleProducts) { product in
                ProductRow(product: product)
                    .onAppear {
                        if product == visibleProducts.last {
                            loadMoreProducts()
                        }
                    }
            }
            
            if isLoadingMore {
                ProgressView("Loading more...")
                    .frame(maxWidth: .infinity)
                    .padding()
            }
        }
        .onAppear {
            loadInitialProducts()
        }
    }
    
    private func loadInitialProducts() {
        visibleProducts = Array(products.prefix(pageSize))
    }
    
    private func loadMoreProducts() {
        guard !isLoadingMore else { return }
        
        isLoadingMore = true
        
        DispatchQueue.main.asyncAfter(deadline: .now() + 0.5) {
            let currentCount = visibleProducts.count
            let newProducts = Array(products.dropFirst(currentCount).prefix(pageSize))
            visibleProducts.append(contentsOf: newProducts)
            isLoadingMore = false
        }
    }
}

// Efficient list updates
struct OptimizedProductList: View {
    let products: [Product]
    
    var body: some View {
        List(products) { product in
            ProductRow(product: product)
                .id(product.id) // Explicit ID for better diffing
        }
        .listStyle(PlainListStyle())
    }
}

// View identity and stability
struct StableProductRow: View {
    let product: Product
    
    var body: some View {
        HStack {
            AsyncImage(url: product.imageURL) { image in
                image
                    .resizable()
                    .aspectRatio(contentMode: .fit)
            } placeholder: {
                Color.gray.opacity(0.3)
            }
            .frame(width: 60, height: 60)
            .cornerRadius(8)
            
            VStack(alignment: .leading, spacing: 4) {
                Text(product.name)
                    .font(.headline)
                    .lineLimit(2)
                
                Text(product.description)
                    .font(.caption)
                    .foregroundColor(.secondary)
                    .lineLimit(3)
            }
            
            Spacer()
            
            Text("$\(product.price, specifier: "%.2f")")
                .font(.subheadline)
                .fontWeight(.semibold)
        }
        .padding(.vertical, 8)
    }
}

// Conditional view rendering
struct ConditionalProductView: View {
    let product: Product
    @State private var isExpanded = false
    
    var body: some View {
        VStack(alignment: .leading, spacing: 12) {
            // Always visible content
            HStack {
                Text(product.name)
                    .font(.headline)
                
                Spacer()
                
                Button(isExpanded ? "Show Less" : "Show More") {
                    withAnimation(.easeInOut(duration: 0.3)) {
                        isExpanded.toggle()
                    }
                }
                .buttonStyle(SecondaryButtonStyle())
            }
            
            // Conditional content
            if isExpanded {
                VStack(alignment: .leading, spacing: 8) {
                    Text(product.description)
                        .font(.body)
                        .foregroundColor(.secondary)
                    
                    HStack {
                        ForEach(product.tags, id: \.self) { tag in
                            Text(tag)
                                .font(.caption)
                                .padding(.horizontal, 8)
                                .padding(.vertical, 4)
                                .background(Color.blue.opacity(0.1))
                                .foregroundColor(.blue)
                                .cornerRadius(12)
                        }
                    }
                }
                .transition(.opacity.combined(with: .move(edge: .top)))
            }
        }
        .padding()
        .background(Color.white)
        .cornerRadius(12)
        .shadow(radius: 2)
    }
}
```

This comprehensive coverage of SwiftUI Integration shows how it provides a modern, declarative approach to building user interfaces in Swift. The key benefits are:

1. **Declarative Syntax**: Write UI code that's easy to read and understand
2. **Automatic Updates**: UI automatically updates when data changes
3. **Built-in Animations**: Smooth animations and transitions
4. **Cross-Platform**: Single codebase for iOS, macOS, watchOS, and tvOS
5. **Performance**: Efficient rendering and memory management

SwiftUI represents the future of Apple platform development, providing a powerful and intuitive way to build modern user interfaces.

---

## Foundation Framework Integration - Deep Dive

The Foundation framework is the core framework that provides fundamental functionality for all Apple platforms. It contains essential data types, collection classes, and utilities that form the foundation of Swift development. Understanding how to effectively integrate Foundation with Swift's modern features is crucial for building robust, performant applications.

### Understanding Foundation Fundamentals

**What Foundation Actually Is:**
Foundation is a framework that provides basic data types, collection classes, and utility functions that are essential for application development. It bridges the gap between Swift's modern type system and the underlying Objective-C runtime, providing interoperability and performance optimizations.

```swift
// ❌ Without Foundation - limited functionality
struct BasicUser {
    let name: String
    let age: Int
    let email: String
}

// ❌ Problems:
// 1. No built-in serialization
// 2. No networking capabilities
// 3. No file system access
// 4. No date/time handling
// 5. No localization support
// 6. No data persistence

// ✅ With Foundation - comprehensive functionality
import Foundation

struct User: Codable {
    let name: String
    let age: Int
    let email: String
    let createdAt: Date
    let id: UUID
    let preferences: [String: Any]
    
    // Foundation provides:
    // - Codable for JSON serialization
    // - Date for time handling
    // - UUID for unique identifiers
    // - Dictionary for flexible data storage
}

// Foundation enables powerful operations
let user = User(
    name: "John Doe",
    age: 30,
    email: "john@example.com",
    createdAt: Date(),
    id: UUID(),
    preferences: ["theme": "dark", "notifications": true]
)

// JSON serialization
let encoder = JSONEncoder()
let data = try encoder.encode(user)

// File persistence
let documentsPath = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first!
let fileURL = documentsPath.appendingPathComponent("user.json")
try data.write(to: fileURL)

// Network requests
let url = URL(string: "https://api.example.com/users")!
let request = URLRequest(url: url)
```

**Core Foundation Types and Their Swift Equivalents:**
```swift
// 1. String - Foundation-enhanced string handling
let foundationString = NSString(string: "Hello, Foundation!")
let swiftString = String(foundationString)

// Foundation string methods
let range = foundationString.range(of: "Foundation")
let substring = foundationString.substring(with: range)

// Swift string with Foundation capabilities
let swiftStringWithFoundation = "Hello, World!" as NSString
let localizedString = NSLocalizedString("greeting", comment: "A greeting message")

// 2. Number types - NSNumber and Swift numeric types
let nsNumber = NSNumber(value: 42)
let intValue = nsNumber.intValue
let doubleValue = nsNumber.doubleValue
let boolValue = nsNumber.boolValue

// Swift numeric types with Foundation
let swiftInt: Int = 42
let nsNumberFromSwift = NSNumber(value: swiftInt)

// 3. Collections - NSArray, NSDictionary, NSSet
let nsArray = NSArray(array: [1, 2, 3, 4, 5])
let swiftArray = Array(nsArray) as! [Int]

let nsDictionary = NSDictionary(dictionary: ["key": "value"])
let swiftDictionary = Dictionary(nsDictionary) as! [String: String]

let nsSet = NSSet(set: Set([1, 2, 3, 4, 5]))
let swiftSet = Set(nsArray) as! Set<Int>

// 4. Data - NSData and Swift Data
let nsData = NSData(data: "Hello".data(using: .utf8)!)
let swiftData = Data(nsData)

// 5. Date - NSDate and Swift Date
let nsDate = NSDate()
let swiftDate = Date()
let dateFromNS = Date(timeIntervalSince1970: nsDate.timeIntervalSince1970)
```

### Foundation Data Types and Collections

**Advanced String Operations:**
```swift
// String encoding and decoding
extension String {
    // Encode to different encodings
    func data(using encoding: String.Encoding) -> Data? {
        return self.data(using: encoding)
    }
    
    // Decode from data with fallback
    init?(data: Data, encoding: String.Encoding, fallback: String.Encoding = .utf8) {
        if let string = String(data: data, encoding: encoding) {
            self = string
        } else if let string = String(data: data, encoding: fallback) {
            self = string
        } else {
            return nil
        }
    }
    
    // URL encoding/decoding
    var urlEncoded: String {
        return self.addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed) ?? self
    }
    
    var urlDecoded: String {
        return self.removingPercentEncoding ?? self
    }
    
    // Base64 encoding/decoding
    var base64Encoded: String {
        return Data(self.utf8).base64EncodedString()
    }
    
    init?(base64Encoded: String) {
        guard let data = Data(base64Encoded: base64Encoded),
              let string = String(data: data, encoding: .utf8) else {
            return nil
        }
        self = string
    }
    
    // Localization
    var localized: String {
        return NSLocalizedString(self, comment: "")
    }
    
    func localized(with arguments: CVarArg...) -> String {
        return String(format: self.localized, arguments: arguments)
    }
}

// Usage
let originalString = "Hello, World! 你好世界"
let encoded = originalString.urlEncoded
let decoded = encoded.urlDecoded
let base64 = originalString.base64Encoded
let localized = "greeting".localized

// String formatting with Foundation
let formatter = NumberFormatter()
formatter.numberStyle = .currency
formatter.locale = Locale(identifier: "en_US")

let price = 29.99
let formattedPrice = formatter.string(from: NSNumber(value: price))

// Date formatting
let dateFormatter = DateFormatter()
dateFormatter.dateStyle = .full
dateFormatter.timeStyle = .short
dateFormatter.locale = Locale(identifier: "en_US")

let now = Date()
let formattedDate = dateFormatter.string(from: now)

// Custom string processing
extension String {
    // Extract numbers from string
    var numbers: [Int] {
        let numbers = self.components(separatedBy: CharacterSet.decimalDigits.inverted)
        return numbers.compactMap { Int($0) }
    }
    
    // Extract email addresses
    var emailAddresses: [String] {
        let pattern = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"
        let regex = try! NSRegularExpression(pattern: pattern)
        let range = NSRange(self.startIndex..<self.endIndex, in: self)
        let matches = regex.matches(in: self, range: range)
        
        return matches.compactMap { match in
            guard let range = Range(match.range, in: self) else { return nil }
            return String(self[range])
        }
    }
    
    // Validate email format
    var isValidEmail: Bool {
        let pattern = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"
        let regex = try! NSRegularExpression(pattern: pattern)
        let range = NSRange(self.startIndex..<self.endIndex, in: self)
        return regex.firstMatch(in: self, range: range) != nil
    }
    
    // Truncate with ellipsis
    func truncated(to length: Int, trailing: String = "...") -> String {
        if self.count <= length {
            return self
        }
        return String(self.prefix(length)) + trailing
    }
}

// Usage
let textWithNumbers = "Order #12345 for $99.99"
let extractedNumbers = textWithNumbers.numbers // [12345, 99, 99]

let textWithEmails = "Contact us at john@example.com or support@company.com"
let emails = textWithEmails.emailAddresses // ["john@example.com", "support@company.com"]

let longText = "This is a very long text that needs to be truncated"
let truncated = longText.truncated(to: 20) // "This is a very long..."
```

**Advanced Collection Operations:**
```swift
// Array extensions with Foundation capabilities
extension Array where Element: Equatable {
    // Remove duplicates while preserving order
    var unique: [Element] {
        var seen = Set<Element>()
        return self.filter { element in
            if seen.contains(element) {
                return false
            } else {
                seen.insert(element)
                return true
            }
        }
    }
    
    // Group by key path
    func grouped<T: Hashable>(by keyPath: KeyPath<Element, T>) -> [T: [Element]] {
        return Dictionary(grouping: self, by: { $0[keyPath: keyPath] })
    }
    
    // Chunk into smaller arrays
    func chunked(into size: Int) -> [[Element]] {
        return stride(from: 0, to: count, by: size).map {
            Array(self[$0..<min($0 + size, count)])
        }
    }
    
    // Safe subscript access
    subscript(safe index: Index) -> Element? {
        return indices.contains(index) ? self[index] : nil
    }
}

// Dictionary extensions
extension Dictionary {
    // Merge with another dictionary
    mutating func merge(_ other: [Key: Value]) {
        for (key, value) in other {
            self[key] = value
        }
    }
    
    // Merge and return new dictionary
    func merged(with other: [Key: Value]) -> [Key: Value] {
        var result = self
        result.merge(other)
        return result
    }
    
    // Filter by key or value
    func filter(by keyFilter: ((Key) -> Bool)? = nil, valueFilter: ((Value) -> Bool)? = nil) -> [Key: Value] {
        return self.filter { key, value in
            let keyPasses = keyFilter?(key) ?? true
            let valuePasses = valueFilter?(value) ?? true
            return keyPasses && valuePasses
        }
    }
    
    // Transform keys and values
    func transform<K, V>(keyTransform: (Key) -> K, valueTransform: (Value) -> V) -> [K: V] {
        var result: [K: V] = [:]
        for (key, value) in self {
            result[keyTransform(key)] = valueTransform(value)
        }
        return result
    }
}

// Set extensions
extension Set {
    // Union with multiple sets
    func union(_ sets: Set<Element>...) -> Set<Element> {
        var result = self
        for set in sets {
            result.formUnion(set)
        }
        return result
    }
    
    // Intersection with multiple sets
    func intersection(_ sets: Set<Element>...) -> Set<Element> {
        var result = self
        for set in sets {
            result.formIntersection(set)
        }
        return result
    }
    
    // Symmetric difference with multiple sets
    func symmetricDifference(_ sets: Set<Element>...) -> Set<Element> {
        var result = self
        for set in sets {
            result.formSymmetricDifference(set)
        }
        return result
    }
}

// Usage
let numbers = [1, 2, 2, 3, 3, 4, 5, 5]
let uniqueNumbers = numbers.unique // [1, 2, 3, 4, 5]

struct Person {
    let name: String
    let age: Int
    let city: String
}

let people = [
    Person(name: "John", age: 25, city: "New York"),
    Person(name: "Jane", age: 30, city: "New York"),
    Person(name: "Bob", age: 25, city: "Los Angeles")
]

let groupedByAge = people.grouped(by: \.age)
// [25: [Person(name: "John", age: 25, city: "New York"), Person(name: "Bob", age: 25, city: "Los Angeles")],
//  30: [Person(name: "Jane", age: 30, city: "New York")]]

let groupedByCity = people.grouped(by: \.city)
// ["New York": [Person(name: "John", age: 25, city: "New York"), Person(name: "Jane", age: 30, city: "New York")],
//  "Los Angeles": [Person(name: "Bob", age: 25, city: "Los Angeles")]]

let chunkedNumbers = numbers.chunked(into: 3) // [[1, 2, 2], [3, 3, 4], [5, 5]]

let dict1 = ["a": 1, "b": 2]
let dict2 = ["c": 3, "d": 4]
let merged = dict1.merged(with: dict2) // ["a": 1, "b": 2, "c": 3, "d": 4]

let filtered = merged.filter(by: { $0 != "a" }, valueFilter: { $0 > 2 })
// ["c": 3, "d": 4]

let transformed = merged.transform(
    keyTransform: { $0.uppercased() },
    valueTransform: { $0 * 2 }
) // ["A": 2, "B": 4, "C": 6, "D": 8]
```

### Foundation Date and Time Handling

**Advanced Date Operations:**
```swift
// Date extensions with Foundation capabilities
extension Date {
    // Calendar components
    var calendar: Calendar {
        return Calendar.current
    }
    
    var year: Int {
        return calendar.component(.year, from: self)
    }
    
    var month: Int {
        return calendar.component(.month, from: self)
    }
    
    var day: Int {
        return calendar.component(.day, from: self)
    }
    
    var hour: Int {
        return calendar.component(.hour, from: self)
    }
    
    var minute: Int {
        return calendar.component(.minute, from: self)
    }
    
    var second: Int {
        return calendar.component(.second, from: self)
    }
    
    var weekday: Int {
        return calendar.component(.weekday, from: self)
    }
    
    var weekdaySymbol: String {
        let formatter = DateFormatter()
        formatter.dateFormat = "EEEE"
        return formatter.string(from: self)
    }
    
    var monthSymbol: String {
        let formatter = DateFormatter()
        formatter.dateFormat = "MMMM"
        return formatter.string(from: self)
    }
    
    // Date arithmetic
    func adding(_ component: Calendar.Component, value: Int) -> Date {
        return calendar.date(byAdding: component, value: value, to: self) ?? self
    }
    
    func subtracting(_ component: Calendar.Component, value: Int) -> Date {
        return calendar.date(byAdding: component, value: -value, to: self) ?? self
    }
    
    // Start/end of periods
    var startOfDay: Date {
        return calendar.startOfDay(for: self)
    }
    
    var endOfDay: Date {
        let start = startOfDay
        return calendar.date(byAdding: .day, value: 1, to: start) ?? self
    }
    
    var startOfWeek: Date {
        let components = calendar.dateComponents([.yearForWeekOfYear, .weekOfYear], from: self)
        return calendar.date(from: components) ?? self
    }
    
    var endOfWeek: Date {
        let start = startOfWeek
        return calendar.date(byAdding: .weekOfYear, value: 1, to: start) ?? self
    }
    
    var startOfMonth: Date {
        let components = calendar.dateComponents([.year, .month], from: self)
        return calendar.date(from: components) ?? self
    }
    
    var endOfMonth: Date {
        let start = startOfMonth
        return calendar.date(byAdding: .month, value: 1, to: start) ?? self
    }
    
    var startOfYear: Date {
        let components = calendar.dateComponents([.year], from: self)
        return calendar.date(from: components) ?? self
    }
    
    var endOfYear: Date {
        let start = startOfYear
        return calendar.date(byAdding: .year, value: 1, to: start) ?? self
    }
    
    // Relative time descriptions
    var timeAgo: String {
        let formatter = RelativeDateTimeFormatter()
        formatter.unitsStyle = .full
        return formatter.localizedString(for: self, relativeTo: Date())
    }
    
    // Age calculation
    var age: Int {
        return calendar.dateComponents([.year], from: self, to: Date()).year ?? 0
    }
    
    // Is today, yesterday, tomorrow
    var isToday: Bool {
        return calendar.isDateInToday(self)
    }
    
    var isYesterday: Bool {
        return calendar.isDateInYesterday(self)
    }
    
    var isTomorrow: Bool {
        return calendar.isDateInTomorrow(self)
    }
    
    // Is this week, month, year
    var isThisWeek: Bool {
        return calendar.isDate(self, equalTo: Date(), toGranularity: .weekOfYear)
    }
    
    var isThisMonth: Bool {
        return calendar.isDate(self, equalTo: Date(), toGranularity: .month)
    }
    
    var isThisYear: Bool {
        return calendar.isDate(self, equalTo: Date(), toGranularity: .year)
    }
}

// Date formatter utilities
class DateFormattingUtilities {
    static let shared = DateFormattingUtilities()
    
    private let dateFormatter = DateFormatter()
    private let relativeFormatter = RelativeDateTimeFormatter()
    private let intervalFormatter = DateIntervalFormatter()
    
    private init() {
        setupFormatters()
    }
    
    private func setupFormatters() {
        relativeFormatter.unitsStyle = .full
        intervalFormatter.dateStyle = .medium
        intervalFormatter.timeStyle = .short
    }
    
    // Format date with custom style
    func format(_ date: Date, style: DateFormatter.Style) -> String {
        dateFormatter.dateStyle = style
        dateFormatter.timeStyle = .none
        return dateFormatter.string(from: date)
    }
    
    // Format date with custom format string
    func format(_ date: Date, format: String) -> String {
        dateFormatter.dateFormat = format
        return dateFormatter.string(from: date)
    }
    
    // Format relative time
    func formatRelative(_ date: Date) -> String {
        return relativeFormatter.localizedString(for: date, relativeTo: Date())
    }
    
    // Format date interval
    func formatInterval(from startDate: Date, to endDate: Date) -> String {
        return intervalFormatter.string(from: startDate, to: endDate)
    }
    
    // Parse date from string
    func parse(_ string: String, format: String) -> Date? {
        dateFormatter.dateFormat = format
        return dateFormatter.date(from: string)
    }
    
    // Parse date from multiple possible formats
    func parse(_ string: String, formats: [String]) -> Date? {
        for format in formats {
            if let date = parse(string, format: format) {
                return date
            }
        }
        return nil
    }
}

// Usage
let now = Date()
let tomorrow = now.adding(.day, value: 1)
let nextWeek = now.adding(.weekOfYear, value: 1)
let nextMonth = now.adding(.month, value: 1)

print("Today: \(now.startOfDay)")
print("This week: \(now.startOfWeek) to \(now.endOfWeek)")
print("This month: \(now.startOfMonth) to \(now.endOfMonth)")
print("Tomorrow: \(tomorrow.timeAgo)")

let formatter = DateFormattingUtilities.shared
print("Formatted: \(formatter.format(now, style: .full))")
print("Custom: \(formatter.format(now, format: "yyyy-MM-dd HH:mm:ss"))")
print("Relative: \(formatter.formatRelative(tomorrow))")

// Date parsing
let dateString = "2024-01-15 14:30:00"
let parsedDate = formatter.parse(dateString, format: "yyyy-MM-dd HH:mm:ss")

// Multiple format parsing
let ambiguousDate = "15/01/2024"
let parsed = formatter.parse(ambiguousDate, formats: ["dd/MM/yyyy", "MM/dd/yyyy", "yyyy-MM-dd"])
```

**Calendar and Time Zone Operations:**
```swift
// Calendar utilities
extension Calendar {
    // Working days in a month
    func workingDays(in month: Date) -> Int {
        let range = self.range(of: .day, in: .month, for: month)!
        let startOfMonth = self.startOfMonth(for: month)
        
        var workingDays = 0
        for day in range {
            if let date = self.date(byAdding: .day, value: day - 1, to: startOfMonth) {
                let weekday = self.component(.weekday, from: date)
                if weekday != 1 && weekday != 7 { // Not Sunday (1) or Saturday (7)
                    workingDays += 1
                }
            }
        }
        return workingDays
    }
    
    // Next occurrence of a weekday
    func nextOccurrence(of weekday: Int, from date: Date) -> Date? {
        let currentWeekday = self.component(.weekday, from: date)
        let daysToAdd = (weekday - currentWeekday + 7) % 7
        return self.date(byAdding: .day, value: daysToAdd, to: date)
    }
    
    // Previous occurrence of a weekday
    func previousOccurrence(of weekday: Int, from date: Date) -> Date? {
        let currentWeekday = self.component(.weekday, from: date)
        let daysToSubtract = (currentWeekday - weekday + 7) % 7
        return self.date(byAdding: .day, value: -daysToSubtract, to: date)
    }
    
    // Is business day
    func isBusinessDay(_ date: Date) -> Bool {
        let weekday = self.component(.weekday, from: date)
        return weekday != 1 && weekday != 7
    }
    
    // Add business days
    func addingBusinessDays(_ businessDays: Int, to date: Date) -> Date? {
        var result = date
        var remainingDays = businessDays
        
        while remainingDays > 0 {
            result = self.date(byAdding: .day, value: 1, to: result) ?? result
            if self.isBusinessDay(result) {
                remainingDays -= 1
            }
        }
        
        return result
    }
}

// Time zone utilities
extension TimeZone {
    // Current time zone abbreviation
    var abbreviation: String {
        return self.abbreviation() ?? "Unknown"
    }
    
    // Offset from GMT
    var offsetFromGMT: TimeInterval {
        return self.secondsFromGMT(for: Date())
    }
    
    // Formatted offset
    var formattedOffset: String {
        let hours = Int(offsetFromGMT / 3600)
        let minutes = Int((offsetFromGMT.truncatingRemainder(dividingBy: 3600)) / 60)
        let sign = hours >= 0 ? "+" : "-"
        return String(format: "%@%02d:%02d", sign, abs(hours), abs(minutes))
    }
    
    // Is daylight saving time
    var isDaylightSavingTime: Bool {
        return self.isDaylightSavingTime(for: Date())
    }
}

// Usage
let calendar = Calendar.current
let workingDays = calendar.workingDays(in: Date())
print("Working days this month: \(workingDays)")

let nextMonday = calendar.nextOccurrence(of: 2, from: Date()) // Monday = 2
let previousFriday = calendar.previousOccurrence(of: 6, from: Date()) // Friday = 6

let businessDate = calendar.addingBusinessDays(5, to: Date())
print("5 business days from now: \(businessDate?.formatted() ?? "Unknown")")

let timeZone = TimeZone.current
print("Current time zone: \(timeZone.identifier)")
print("Abbreviation: \(timeZone.abbreviation)")
print("Offset: \(timeZone.formattedOffset)")
print("DST: \(timeZone.isDaylightSavingTime ? "Yes" : "No")")
```

### Foundation File System and Data Persistence

**File System Operations:**
```swift
// File manager utilities
extension FileManager {
    // Documents directory
    var documentsDirectory: URL {
        return self.urls(for: .documentDirectory, in: .userDomainMask).first!
    }
    
    // Library directory
    var libraryDirectory: URL {
        return self.urls(for: .libraryDirectory, in: .userDomainMask).first!
    }
    
    // Caches directory
    var cachesDirectory: URL {
        return self.urls(for: .cachesDirectory, in: .userDomainMask).first!
    }
    
    // Temporary directory
    var temporaryDirectory: URL {
        return self.temporaryDirectory
    }
    
    // Application support directory
    var applicationSupportDirectory: URL {
        return self.urls(for: .applicationSupportDirectory, in: .userDomainMask).first!
    }
    
    // Create directory if it doesn't exist
    func createDirectoryIfNeeded(at url: URL) throws {
        if !self.fileExists(atPath: url.path) {
            try self.createDirectory(at: url, withIntermediateDirectories: true)
        }
    }
    
    // File size
    func fileSize(at url: URL) -> Int64? {
        guard let attributes = try? self.attributesOfItem(atPath: url.path) else {
            return nil
        }
        return attributes[.size] as? Int64
    }
    
    // File creation date
    func fileCreationDate(at url: URL) -> Date? {
        guard let attributes = try? self.attributesOfItem(atPath: url.path) else {
            return nil
        }
        return attributes[.creationDate] as? Date
    }
    
    // File modification date
    func fileModificationDate(at url: URL) -> Date? {
        guard let attributes = try? self.attributesOfItem(atPath: url.path) else {
            return nil
        }
        return attributes[.modificationDate] as? Date
    }
    
    // Is directory
    func isDirectory(at url: URL) -> Bool {
        guard let attributes = try? self.attributesOfItem(atPath: url.path) else {
            return false
        }
        return (attributes[.type] as? FileAttributeType) == .typeDirectory
    }
    
    // Directory contents
    func contentsOfDirectory(at url: URL, includingPropertiesForKeys keys: [URLResourceKey]? = nil) throws -> [URL] {
        return try self.contentsOfDirectory(at: url, includingPropertiesForKeys: keys)
    }
    
    // Recursive directory contents
    func recursiveContentsOfDirectory(at url: URL) throws -> [URL] {
        var urls: [URL] = []
        
        func enumerateDirectory(_ directoryURL: URL) throws {
            let contents = try self.contentsOfDirectory(at: directoryURL)
            for item in contents {
                urls.append(item)
                if self.isDirectory(at: item) {
                    try enumerateDirectory(item)
                }
            }
        }
        
        try enumerateDirectory(url)
        return urls
    }
    
    // Copy file with overwrite
    func copyFile(from sourceURL: URL, to destinationURL: URL, overwrite: Bool = false) throws {
        if overwrite && self.fileExists(atPath: destinationURL.path) {
            try self.removeItem(at: destinationURL)
        }
        try self.copyItem(at: sourceURL, to: destinationURL)
    }
    
    // Move file with overwrite
    func moveFile(from sourceURL: URL, to destinationURL: URL, overwrite: Bool = false) throws {
        if overwrite && self.fileExists(atPath: destinationURL.path) {
            try self.removeItem(at: destinationURL)
        }
        try self.moveItem(at: sourceURL, to: destinationURL)
    }
    
    // Safe file operations
    func safeWrite(_ data: Data, to url: URL) throws {
        let tempURL = url.appendingPathExtension("tmp")
        
        // Write to temporary file
        try data.write(to: tempURL)
        
        // Move to final location
        try self.moveItem(at: tempURL, to: url)
    }
}

// Data persistence utilities
class DataPersistenceManager {
    private let fileManager = FileManager.default
    
    // Save data to documents directory
    func save(_ data: Data, to filename: String, in directory: FileManager.SearchPathDirectory = .documentDirectory) throws {
        let directoryURL = fileManager.urls(for: directory, in: .userDomainMask).first!
        let fileURL = directoryURL.appendingPathComponent(filename)
        
        try fileManager.safeWrite(data, to: fileURL)
    }
    
    // Load data from documents directory
    func load(filename: String, from directory: FileManager.SearchPathDirectory = .documentDirectory) throws -> Data {
        let directoryURL = fileManager.urls(for: directory, in: .userDomainMask).first!
        let fileURL = directoryURL.appendingPathComponent(filename)
        
        return try Data(contentsOf: fileURL)
    }
    
    // Save codable object
    func save<T: Codable>(_ object: T, to filename: String, in directory: FileManager.SearchPathDirectory = .documentDirectory) throws {
        let data = try JSONEncoder().encode(object)
        try save(data, to: filename, in: directory)
    }
    
    // Load codable object
    func load<T: Codable>(_ type: T.Type, from filename: String, in directory: FileManager.SearchPathDirectory = .documentDirectory) throws -> T {
        let data = try load(filename: filename, from: directory)
        return try JSONDecoder().decode(type, from: data)
    }
    
    // Check if file exists
    func fileExists(filename: String, in directory: FileManager.SearchPathDirectory = .documentDirectory) -> Bool {
        let directoryURL = fileManager.urls(for: directory, in: .userDomainMask).first!
        let fileURL = directoryURL.appendingPathComponent(filename)
        return fileManager.fileExists(atPath: fileURL.path)
    }
    
    // Delete file
    func delete(filename: String, from directory: FileManager.SearchPathDirectory = .documentDirectory) throws {
        let directoryURL = fileManager.urls(for: directory, in: .userDomainMask).first!
        let fileURL = directoryURL.appendingPathComponent(filename)
        
        if fileManager.fileExists(atPath: fileURL.path) {
            try fileManager.removeItem(at: fileURL)
        }
    }
    
    // Get file URL
    func fileURL(filename: String, in directory: FileManager.SearchPathDirectory = .documentDirectory) -> URL {
        let directoryURL = fileManager.urls(for: directory, in: .userDomainMask).first!
        return directoryURL.appendingPathComponent(filename)
    }
}

// Usage
let persistenceManager = DataPersistenceManager()

// Save and load data
let userData = User(id: "123", name: "John", email: "john@example.com")
try persistenceManager.save(userData, to: "user.json")

let loadedUser = try persistenceManager.load(User.self, from: "user.json")
print("Loaded user: \(loadedUser.name)")

// File operations
let fileManager = FileManager.default
let documentsPath = fileManager.documentsDirectory
print("Documents directory: \(documentsPath.path)")

let fileSize = fileManager.fileSize(at: fileManager.fileURL(filename: "user.json"))
print("File size: \(fileSize ?? 0) bytes")

let isDirectory = fileManager.isDirectory(at: documentsPath)
print("Is directory: \(isDirectory)")

// Create directory structure
let appDataPath = fileManager.applicationSupportDirectory.appendingPathComponent("MyApp")
try fileManager.createDirectoryIfNeeded(at: appDataPath)

// List directory contents
let contents = try fileManager.contentsOfDirectory(at: documentsPath)
print("Directory contents: \(contents.map { $0.lastPathComponent })")
```

### Foundation Networking and URL Handling

**URL and Network Utilities:**
```swift
// URL utilities
extension URL {
    // URL components
    var queryParameters: [String: String] {
        guard let components = URLComponents(url: self, resolvingAgainstBaseURL: false),
              let queryItems = components.queryItems else {
            return [:]
        }
        
        var parameters: [String: String] = [:]
        for item in queryItems {
            parameters[item.name] = item.value
        }
        return parameters
    }
    
    // Add query parameters
    func addingQueryParameters(_ parameters: [String: String]) -> URL? {
        guard var components = URLComponents(url: self, resolvingAgainstBaseURL: false) else {
            return nil
        }
        
        var queryItems = components.queryItems ?? []
        for (key, value) in parameters {
            queryItems.append(URLQueryItem(name: key, value: value))
        }
        components.queryItems = queryItems
        
        return components.url
    }
    
    // Remove query parameters
    func removingQueryParameters(_ parameters: [String]) -> URL? {
        guard var components = URLComponents(url: self, resolvingAgainstBaseURL: false) else {
            return nil
        }
        
        components.queryItems = components.queryItems?.filter { !parameters.contains($0.name) }
        return components.url
    }
    
    // File extension
    var fileExtension: String {
        return self.pathExtension
    }
    
    // File name without extension
    var fileNameWithoutExtension: String {
        return self.deletingPathExtension().lastPathComponent
    }
    
    // Is directory
    var isDirectory: Bool {
        return self.hasDirectoryPath
    }
    
    // Parent directory
    var parentDirectory: URL? {
        return self.deletingLastPathComponent()
    }
    
    // Relative path from base
    func relativePath(from base: URL) -> String? {
        return self.path.replacingOccurrences(of: base.path, with: "")
    }
}

// Network utilities
class NetworkUtilities {
    // Check network reachability
    static func isNetworkReachable() -> Bool {
        // This is a simplified check - real implementation would use Network framework
        return true
    }
    
    // Validate URL
    static func isValidURL(_ string: String) -> Bool {
        guard let url = URL(string: string) else {
            return false
        }
        return UIApplication.shared.canOpenURL(url)
    }
    
    // Extract domain from URL
    static func extractDomain(from url: URL) -> String? {
        return url.host
    }
    
    // Extract path components
    static func extractPathComponents(from url: URL) -> [String] {
        return url.pathComponents.filter { $0 != "/" }
    }
    
    // Build URL from components
    static func buildURL(scheme: String = "https", host: String, path: String, queryParameters: [String: String] = [:]) -> URL? {
        var components = URLComponents()
        components.scheme = scheme
        components.host = host
        components.path = path
        
        if !queryParameters.isEmpty {
            components.queryItems = queryParameters.map { URLQueryItem(name: $0.key, value: $0.value) }
        }
        
        return components.url
    }
    
    // URL encoding
    static func encodeURL(_ string: String) -> String {
        return string.addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed) ?? string
    }
    
    // URL decoding
    static func decodeURL(_ string: String) -> String {
        return string.removingPercentEncoding ?? string
    }
}

// Usage
let urlString = "https://api.example.com/users?page=1&limit=10&sort=name"
guard let url = URL(string: urlString) else { return }

let parameters = url.queryParameters
print("Query parameters: \(parameters)")
// ["page": "1", "limit": "10", "sort": "name"]

let newURL = url.addingQueryParameters(["filter": "active"])
print("New URL: \(newURL?.absoluteString ?? "Invalid")")

let domain = NetworkUtilities.extractDomain(from: url)
print("Domain: \(domain ?? "Unknown")")

let pathComponents = NetworkUtilities.extractPathComponents(from: url)
print("Path components: \(pathComponents)")

let builtURL = NetworkUtilities.buildURL(
    host: "api.example.com",
    path: "/users",
    queryParameters: ["page": "1", "limit": "10"]
)
print("Built URL: \(builtURL?.absoluteString ?? "Invalid")")

// File URL operations
let fileURL = URL(fileURLWithPath: "/Users/username/Documents/file.txt")
print("File extension: \(fileURL.fileExtension)")
print("File name: \(fileURL.fileNameWithoutExtension)")
print("Parent directory: \(fileURL.parentDirectory?.path ?? "None")")
```

This comprehensive coverage of Foundation Framework Integration shows how it provides essential functionality for all Swift applications. The key benefits are:

1. **Core Functionality**: Essential data types and utilities
2. **Performance**: Optimized implementations for common operations
3. **Interoperability**: Seamless integration with Objective-C
4. **Cross-Platform**: Consistent behavior across all Apple platforms
5. **Rich APIs**: Comprehensive set of tools for common development tasks

Foundation is the backbone of Swift development, providing the essential building blocks that all other frameworks build upon.

---

## Advanced Property Wrapper Patterns - Deep Dive

Property wrappers in Swift are a powerful feature that enables you to create reusable, composable abstractions for property behavior. While basic property wrappers are straightforward, advanced patterns unlock sophisticated functionality including composition, projected values, custom storage, and integration with Swift's type system.

### Understanding Advanced Property Wrapper Concepts

**What Advanced Property Wrapper Patterns Actually Are:**
Advanced property wrapper patterns go beyond simple value wrapping to create sophisticated, composable systems that can transform how properties behave, store data, and interact with Swift's type system. They enable complex behaviors like validation, persistence, transformation, and reactive updates.

```swift
// ❌ Without advanced property wrappers - repetitive, error-prone code
class User {
    private var _name: String = ""
    private var _email: String = ""
    private var _age: Int = 0
    
    var name: String {
        get { return _name }
        set {
            if newValue.isEmpty {
                print("Warning: Name cannot be empty")
                return
            }
            if newValue.count > 50 {
                print("Warning: Name too long")
                return
            }
            _name = newValue.trimmingCharacters(in: .whitespacesAndNewlines)
        }
    }
    
    var email: String {
        get { return _email }
        set {
            if !newValue.contains("@") {
                print("Warning: Invalid email format")
                return
            }
            _email = newValue.lowercased().trimmingCharacters(in: .whitespacesAndNewlines)
        }
    }
    
    var age: Int {
        get { return _age }
        set {
            if newValue < 0 || newValue > 150 {
                print("Warning: Invalid age")
                return
            }
            _age = newValue
        }
    }
}

// ❌ Problems:
// 1. Duplicate validation logic
// 2. Manual backing storage management
// 3. No reusability across different types
// 4. Error handling is inconsistent
// 5. Validation rules are hardcoded

// ✅ With advanced property wrappers - clean, composable, reusable
class User {
    @Validated(validator: { !$0.isEmpty && $0.count <= 50 })
    @Trimmed
    var name: String = ""
    
    @Validated(validator: { $0.contains("@") })
    @Trimmed
    @Lowercased
    var email: String = ""
    
    @Validated(validator: { $0 >= 0 && $0 <= 150 })
    var age: Int = 0
}

// ✅ Benefits:
// 1. Reusable validation logic
// 2. Automatic backing storage management
// 3. Composable behaviors
// 4. Consistent error handling
// 5. Declarative validation rules
```

**Core Advanced Concepts:**
```swift
// 1. Property wrapper composition
// 2. Projected values and custom accessors
// 3. Custom storage strategies
// 4. Type erasure and generic constraints
// 5. Integration with Swift's type system
// 6. Performance optimization techniques
```

### Property Wrapper Composition

**Pattern 1: Chaining Multiple Wrappers**
```swift
// Base validation wrapper
@propertyWrapper
struct Validated<T> {
    private var value: T
    private let validator: (T) -> Bool
    private let errorHandler: (String) -> Void
    
    init(wrappedValue: T, validator: @escaping (T) -> Bool, errorHandler: @escaping (String) -> Void = { print($0) }) {
        self.validator = validator
        self.errorHandler = errorHandler
        self.value = wrappedValue
    }
    
    var wrappedValue: T {
        get { value }
        set {
            if validator(newValue) {
                value = newValue
            } else {
                errorHandler("Validation failed for value: \(newValue)")
            }
        }
    }
}

// String transformation wrapper
@propertyWrapper
struct Trimmed {
    private var value: String
    
    init(wrappedValue: String) {
        self.value = wrappedValue.trimmingCharacters(in: .whitespacesAndNewlines)
    }
    
    var wrappedValue: String {
        get { value }
        set { value = newValue.trimmingCharacters(in: .whitespacesAndNewlines) }
    }
}

// Case transformation wrapper
@propertyWrapper
struct Lowercased {
    private var value: String
    
    init(wrappedValue: String) {
        self.value = wrappedValue.lowercased()
    }
    
    var wrappedValue: String {
        get { value }
        set { value = newValue.lowercased() }
    }
}

// Number range wrapper
@propertyWrapper
struct Clamped<T: Comparable> {
    private var value: T
    private let range: ClosedRange<T>
    
    init(wrappedValue: T, range: ClosedRange<T>) {
        self.range = range
        self.value = range.clamp(wrappedValue)
    }
    
    var wrappedValue: T {
        get { value }
        set { value = range.clamp(newValue) }
    }
}

// Extension to make ClosedRange clamp values
extension ClosedRange {
    func clamp(_ value: Bound) -> Bound {
        return min(max(value, lowerBound), upperBound)
    }
}

// Usage with composition
class AdvancedUser {
    @Validated(validator: { !$0.isEmpty && $0.count <= 50 })
    @Trimmed
    var name: String = ""
    
    @Validated(validator: { $0.contains("@") })
    @Trimmed
    @Lowercased
    var email: String = ""
    
    @Clamped(range: 0...150)
    var age: Int = 0
    
    @Clamped(range: 0.0...100.0)
    var score: Double = 0.0
}

// Test the composition
let user = AdvancedUser()
user.name = "  John Doe  "  // Will be trimmed and validated
user.email = "  JOHN@EXAMPLE.COM  "  // Will be trimmed, validated, and lowercased
user.age = 200  // Will be clamped to 150
user.score = 150.0  // Will be clamped to 100.0

print("Name: '\(user.name)'")  // "John Doe"
print("Email: '\(user.email)'")  // "john@example.com"
print("Age: \(user.age)")  // 150
print("Score: \(user.score)")  // 100.0
```

**Pattern 2: Conditional Wrapper Application**
```swift
// Conditional validation wrapper
@propertyWrapper
struct ConditionalValidated<T> {
    private var value: T
    private let condition: () -> Bool
    private let validator: (T) -> Bool
    private let errorHandler: (String) -> Void
    
    init(wrappedValue: T, condition: @escaping () -> Bool, validator: @escaping (T) -> Bool, errorHandler: @escaping (String) -> Void = { print($0) }) {
        self.value = wrappedValue
        self.condition = condition
        self.validator = validator
        self.errorHandler = errorHandler
    }
    
    var wrappedValue: T {
        get { value }
        set {
            if condition() {
                if validator(newValue) {
                    value = newValue
                } else {
                    errorHandler("Validation failed for value: \(newValue)")
                }
            } else {
                value = newValue
            }
        }
    }
}

// Environment-aware wrapper
@propertyWrapper
struct EnvironmentAware<T> {
    private var value: T
    private let key: String
    private let defaultValue: T
    
    init(wrappedValue: T, key: String, defaultValue: T) {
        self.value = wrappedValue
        self.key = key
        self.defaultValue = defaultValue
        self.loadFromEnvironment()
    }
    
    var wrappedValue: T {
        get { value }
        set { 
            value = newValue
            saveToEnvironment()
        }
    }
    
    private func loadFromEnvironment() {
        if let envValue = ProcessInfo.processInfo.environment[key] {
            if let converted = convertFromString(envValue) {
                value = converted
            }
        } else {
            value = defaultValue
        }
    }
    
    private func saveToEnvironment() {
        // In a real app, you might save to UserDefaults or other storage
        print("Saving \(value) to environment key: \(key)")
    }
    
    private func convertFromString(_ string: String) -> T? {
        // This is a simplified conversion - real implementation would be more robust
        if T.self == String.self {
            return string as? T
        } else if T.self == Int.self {
            return Int(string) as? T
        } else if T.self == Bool.self {
            return Bool(string) as? T
        }
        return nil
    }
}

// Usage with conditional logic
class Configuration {
    @EnvironmentAware(key: "DEBUG_MODE", defaultValue: false)
    var debugMode: Bool = false
    
    @ConditionalValidated(
        condition: { ProcessInfo.processInfo.environment["STRICT_VALIDATION"] == "true" },
        validator: { $0.count >= 8 }
    )
    var password: String = ""
    
    @ConditionalValidated(
        condition: { ProcessInfo.processInfo.environment["VALIDATE_EMAIL"] == "true" },
        validator: { $0.contains("@") && $0.contains(".") }
    )
    var email: String = ""
}

// Test with different environments
let config = Configuration()

// Set environment variables
setenv("STRICT_VALIDATION", "true", 1)
setenv("VALIDATE_EMAIL", "false", 1)

config.password = "123"  // Will fail validation (strict mode enabled)
config.email = "invalid"  // Will pass validation (email validation disabled)

print("Password: \(config.password)")  // Empty (validation failed)
print("Email: \(config.email)")  // "invalid" (validation passed)
```

**Pattern 3: Wrapper with Custom Storage**
```swift
// Custom storage wrapper
@propertyWrapper
struct Persistent<T: Codable> {
    private let key: String
    private let defaultValue: T
    private let storage: StorageStrategy
    
    enum StorageStrategy {
        case userDefaults
        case keychain
        case file
        case custom((String) -> T?, (String, T) -> Void)
    }
    
    init(wrappedValue: T, key: String, storage: StorageStrategy = .userDefaults) {
        self.key = key
        self.defaultValue = wrappedValue
        self.storage = storage
    }
    
    var wrappedValue: T {
        get {
            switch storage {
            case .userDefaults:
                return UserDefaults.standard.object(forKey: key) as? T ?? defaultValue
            case .keychain:
                return loadFromKeychain() ?? defaultValue
            case .file:
                return loadFromFile() ?? defaultValue
            case .custom(let loader, _):
                return loader(key) ?? defaultValue
            }
        }
        set {
            switch storage {
            case .userDefaults:
                UserDefaults.standard.set(newValue, forKey: key)
            case .keychain:
                saveToKeychain(newValue)
            case .file:
                saveToFile(newValue)
            case .custom(_, let saver):
                saver(key, newValue)
            }
        }
    }
    
    private func loadFromKeychain() -> T? {
        // Simplified keychain implementation
        return nil
    }
    
    private func saveToKeychain(_ value: T) {
        // Simplified keychain implementation
        print("Saving to keychain: \(value)")
    }
    
    private func loadFromFile() -> T? {
        let documentsPath = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first!
        let fileURL = documentsPath.appendingPathComponent("\(key).json")
        
        guard let data = try? Data(contentsOf: fileURL) else { return nil }
        return try? JSONDecoder().decode(T.self, from: data)
    }
    
    private func saveToFile(_ value: T) {
        let documentsPath = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first!
        let fileURL = documentsPath.appendingPathComponent("\(key).json")
        
        if let data = try? JSONEncoder().encode(value) {
            try? data.write(to: fileURL)
        }
    }
}

// Usage with custom storage
class UserPreferences {
    @Persistent(key: "user_theme", storage: .userDefaults)
    var theme: String = "light"
    
    @Persistent(key: "user_token", storage: .keychain)
    var authToken: String = ""
    
    @Persistent(key: "user_settings", storage: .file)
    var settings: [String: Any] = [:]
    
    @Persistent(
        key: "custom_data",
        storage: .custom(
            loader: { key in
                // Custom loading logic
                return "custom_value" as? String
            },
            saver: { key, value in
                // Custom saving logic
                print("Custom save: \(key) = \(value)")
            }
        )
    )
    var customData: String = ""
}

// Test persistence
let prefs = UserPreferences()
prefs.theme = "dark"
prefs.authToken = "secret_token_123"
prefs.settings = ["notifications": true, "autoSave": false]
prefs.customData = "important_data"

// Values are automatically persisted
print("Theme: \(prefs.theme)")
print("Token: \(prefs.authToken)")
print("Settings: \(prefs.settings)")
print("Custom: \(prefs.customData)")
```

### Advanced Projected Values and Custom Accessors

**Pattern 1: Projected Values with Rich Information**
```swift
// Enhanced validation wrapper with projected value
@propertyWrapper
struct ValidatedWithDetails<T> {
    private var value: T
    private let validator: (T) -> ValidationResult
    
    struct ValidationResult {
        let isValid: Bool
        let errors: [String]
        let warnings: [String]
        let suggestions: [String]
        
        static let valid = ValidationResult(isValid: true, errors: [], warnings: [], suggestions: [])
        
        static func error(_ message: String) -> ValidationResult {
            return ValidationResult(isValid: false, errors: [message], warnings: [], suggestions: [])
        }
        
        static func warning(_ message: String) -> ValidationResult {
            return ValidationResult(isValid: false, errors: [], warnings: [message], suggestions: [])
        }
        
        static func suggestion(_ message: String) -> ValidationResult {
            return ValidationResult(isValid: false, errors: [], warnings: [], suggestions: [message])
        }
    }
    
    init(wrappedValue: T, validator: @escaping (T) -> ValidationResult) {
        self.validator = validator
        self.value = wrappedValue
    }
    
    var wrappedValue: T {
        get { value }
        set {
            let result = validator(newValue)
            if result.isValid {
                value = newValue
            } else {
                // Log errors but don't prevent setting
                for error in result.errors {
                    print("Error: \(error)")
                }
                for warning in result.warnings {
                    print("Warning: \(warning)")
                }
                value = newValue
            }
        }
    }
    
    var projectedValue: ValidationResult {
        return validator(value)
    }
}

// Usage with projected values
class UserProfile {
    @ValidatedWithDetails { email in
        if email.isEmpty {
            return .error("Email cannot be empty")
        }
        if !email.contains("@") {
            return .error("Email must contain @ symbol")
        }
        if !email.contains(".") {
            return .warning("Email should contain a domain")
        }
        if email.count < 5 {
            return .suggestion("Consider using a longer email address")
        }
        return .valid
    }
    var email: String = ""
    
    @ValidatedWithDetails { password in
        var errors: [String] = []
        var warnings: [String] = []
        var suggestions: [String] = []
        
        if password.isEmpty {
            errors.append("Password cannot be empty")
        }
        if password.count < 8 {
            errors.append("Password must be at least 8 characters")
        }
        if password.count < 12 {
            warnings.append("Consider using a longer password for better security")
        }
        if !password.contains(where: { $0.isNumber }) {
            suggestions.append("Consider adding numbers to your password")
        }
        if !password.contains(where: { $0.isUppercase }) {
            suggestions.append("Consider adding uppercase letters to your password")
        }
        
        return ValidationResult(
            isValid: errors.isEmpty,
            errors: errors,
            warnings: warnings,
            suggestions: suggestions
        )
    }
    var password: String = ""
}

// Test with projected values
let profile = UserProfile()

// Set invalid email
profile.email = "invalid"
print("Email validation: \(profile.$email)")

// Set weak password
profile.password = "weak"
print("Password validation: \(profile.$password)")

// Set valid values
profile.email = "user@example.com"
profile.password = "StrongPass123"
print("Email validation: \(profile.$email)")
print("Password validation: \(profile.$password)")
```

**Pattern 2: Custom Accessors with Side Effects**
```swift
// Wrapper with custom getter and setter logic
@propertyWrapper
struct Observable<T> {
    private var value: T
    private var observers: [(T) -> Void] = []
    
    init(wrappedValue: T) {
        self.value = wrappedValue
    }
    
    var wrappedValue: T {
        get {
            print("Getting value: \(value)")
            return value
        }
        set {
            print("Setting value from \(value) to \(newValue)")
            let oldValue = value
            value = newValue
            notifyObservers(oldValue: oldValue, newValue: newValue)
        }
    }
    
    var projectedValue: ObservableProjection<T> {
        return ObservableProjection(wrapper: self)
    }
    
    private func notifyObservers(oldValue: T, newValue: T) {
        for observer in observers {
            observer(newValue)
        }
    }
    
    func addObserver(_ observer: @escaping (T) -> Void) {
        observers.append(observer)
    }
    
    func removeObserver(_ observer: @escaping (T) -> Void) {
        observers.removeAll { $0 as AnyObject === observer as AnyObject }
    }
}

// Projection for the observable wrapper
class ObservableProjection<T> {
    private weak var wrapper: Observable<T>?
    
    init(wrapper: Observable<T>) {
        self.wrapper = wrapper
    }
    
    func observe(_ observer: @escaping (T) -> Void) {
        wrapper?.addObserver(observer)
    }
    
    func stopObserving(_ observer: @escaping (T) -> Void) {
        wrapper?.removeObserver(observer)
    }
}

// Usage with custom accessors
class DataModel {
    @Observable var counter: Int = 0
    @Observable var name: String = ""
    
    init() {
        // Set up observers
        $counter.observe { newValue in
            print("Counter changed to: \(newValue)")
        }
        
        $name.observe { newValue in
            print("Name changed to: \(newValue)")
        }
    }
}

// Test observable behavior
let model = DataModel()

// These will trigger observers and logging
model.counter = 5
model.name = "John Doe"
model.counter += 1

// Reading values will also log
print("Current counter: \(model.counter)")
print("Current name: \(model.name)")
```

**Pattern 3: Computed Properties with Caching**
```swift
// Caching wrapper for expensive computations
@propertyWrapper
struct Cached<T> {
    private var value: T?
    private let compute: () -> T
    private var isDirty: Bool = true
    
    init(wrappedValue: T, compute: @escaping () -> T) {
        self.value = wrappedValue
        self.compute = compute
    }
    
    var wrappedValue: T {
        get {
            if isDirty || value == nil {
                value = compute()
                isDirty = false
            }
            return value!
        }
        set {
            value = newValue
            isDirty = false
        }
    }
    
    var projectedValue: CachedProjection<T> {
        return CachedProjection(wrapper: self)
    }
    
    func invalidate() {
        isDirty = true
        value = nil
    }
}

// Projection for cached wrapper
class CachedProjection<T> {
    private weak var wrapper: Cached<T>?
    
    init(wrapper: Cached<T>) {
        self.wrapper = wrapper
    }
    
    func invalidate() {
        wrapper?.invalidate()
    }
    
    var isDirty: Bool {
        // This would need to be exposed in the Cached wrapper
        return true // Simplified for example
    }
}

// Usage with caching
class ExpensiveComputation {
    @Cached(compute: { 
        print("Computing expensive value...")
        // Simulate expensive computation
        Thread.sleep(forTimeInterval: 1.0)
        return "Computed at \(Date())"
    })
    var expensiveValue: String = ""
    
    func updateData() {
        // This will invalidate the cache
        $expensiveValue.invalidate()
    }
}

// Test caching behavior
let computation = ExpensiveComputation()

// First access - will compute
print("First access: \(computation.expensiveValue)")

// Second access - will use cached value
print("Second access: \(computation.expensiveValue)")

// Invalidate cache
computation.updateData()

// Third access - will compute again
print("Third access: \(computation.expensiveValue)")
```

### Type Erasure and Generic Constraints

**Pattern 1: Type-Erased Wrapper Composition**
```swift
// Type-erased property wrapper
protocol AnyPropertyWrapper {
    associatedtype Value
    var wrappedValue: Value { get set }
}

// Type-erased wrapper that can hold any property wrapper
@propertyWrapper
struct TypeErased<T> {
    private var storage: AnyStorage<T>
    
    init<W: AnyPropertyWrapper>(_ wrapper: W) where W.Value == T {
        self.storage = WrapperStorage(wrapper: wrapper)
    }
    
    var wrappedValue: T {
        get { storage.get() }
        set { storage.set(newValue) }
    }
    
    // Type-erased storage
    private protocol AnyStorage<T> {
        func get() -> T
        func set(_ value: T)
    }
    
    // Concrete storage implementation
    private struct WrapperStorage<W: AnyPropertyWrapper>: AnyStorage<T> where W.Value == T {
        private var wrapper: W
        
        init(wrapper: W) {
            self.wrapper = wrapper
        }
        
        func get() -> T {
            return wrapper.wrappedValue
        }
        
        func set(_ value: T) {
            wrapper.wrappedValue = value
        }
    }
}

// Usage with type erasure
class FlexibleUser {
    @TypeErased(Validated(validator: { !$0.isEmpty }))
    var name: String = ""
    
    @TypeErased(Trimmed())
    var email: String = ""
    
    @TypeErased(Clamped(range: 0...150))
    var age: Int = 0
}

// Test type-erased wrapper
let user = FlexibleUser()
user.name = ""  // Will fail validation
user.email = "  TEST@EXAMPLE.COM  "  // Will be trimmed
user.age = 200  // Will be clamped

print("Name: '\(user.name)'")
print("Email: '\(user.email)'")
print("Age: \(user.age)")
```

**Pattern 2: Generic Constraints and Conditional Conformance**
```swift
// Wrapper with generic constraints
@propertyWrapper
struct ValidatedCollection<C: Collection> where C.Element: Equatable {
    private var value: C
    private let validators: [(C.Element) -> Bool]
    private let uniqueConstraint: Bool
    
    init(wrappedValue: C, validators: [(C.Element) -> Bool] = [], uniqueConstraint: Bool = false) {
        self.value = wrappedValue
        self.validators = validators
        self.uniqueConstraint = uniqueConstraint
    }
    
    var wrappedValue: C {
        get { value }
        set {
            var validatedValue = newValue
            
            // Apply validators
            for validator in validators {
                validatedValue = validatedValue.filter { validator($0) }
            }
            
            // Apply uniqueness constraint
            if uniqueConstraint {
                validatedValue = Array(Set(validatedValue)) as! C
            }
            
            value = validatedValue
        }
    }
}

// Wrapper with conditional conformance
@propertyWrapper
struct ConditionalWrapper<T> {
    private var value: T
    private let condition: (T) -> Bool
    private let transform: (T) -> T
    
    init(wrappedValue: T, condition: @escaping (T) -> Bool, transform: @escaping (T) -> T) {
        self.value = wrappedValue
        self.condition = condition
        self.transform = transform
    }
    
    var wrappedValue: T {
        get { value }
        set {
            if condition(newValue) {
                value = transform(newValue)
            } else {
                value = newValue
            }
        }
    }
}

// Usage with generic constraints
class DataContainer {
    @ValidatedCollection(validators: [{ $0 > 0 }], uniqueConstraint: true)
    var numbers: [Int] = []
    
    @ValidatedCollection(validators: [{ $0.count > 0 }])
    var strings: [String] = []
    
    @ConditionalWrapper(
        condition: { $0 > 100 },
        transform: { $0 * 2 }
    )
    var largeNumber: Int = 0
}

// Test generic constraints
let container = DataContainer()

// Numbers will be validated and made unique
container.numbers = [1, 2, 2, 3, -1, 4, 4, 5]
print("Validated numbers: \(container.numbers)")  // [1, 2, 3, 4, 5]

// Strings will be validated
container.strings = ["", "hello", "world", ""]
print("Validated strings: \(container.strings)")  // ["hello", "world"]

// Large numbers will be transformed
container.largeNumber = 50  // No transformation
container.largeNumber = 150  // Will be doubled
print("Large number: \(container.largeNumber)")  // 300
```

### Performance Optimization Techniques

**Pattern 1: Lazy Initialization and Memoization**
```swift
// Lazy initialization wrapper
@propertyWrapper
struct Lazy<T> {
    private var storage: LazyStorage<T>
    
    init(wrappedValue: T) {
        self.storage = .value(wrappedValue)
    }
    
    init(compute: @escaping () -> T) {
        self.storage = .computed(compute)
    }
    
    var wrappedValue: T {
        get {
            switch storage {
            case .value(let value):
                return value
            case .computed(let compute):
                let value = compute()
                storage = .value(value)
                return value
            }
        }
        set {
            storage = .value(newValue)
        }
    }
    
    private enum LazyStorage<T> {
        case value(T)
        case computed(() -> T)
    }
}

// Memoization wrapper
@propertyWrapper
struct Memoized<T: Hashable, R> {
    private var cache: [T: R] = [:]
    private let compute: (T) -> R
    
    init(compute: @escaping (T) -> R) {
        self.compute = compute
    }
    
    var wrappedValue: (T) -> R {
        return { input in
            if let cached = cache[input] {
                return cached
            }
            let result = compute(input)
            cache[input] = result
            return result
        }
    }
    
    func clearCache() {
        cache.removeAll()
    }
}

// Usage with performance optimization
class PerformanceOptimized {
    @Lazy(compute: {
        print("Computing expensive value...")
        Thread.sleep(forTimeInterval: 2.0)
        return "Expensive computation result"
    })
    var expensiveValue: String = ""
    
    @Memoized { number in
        print("Computing factorial for \(number)...")
        return (1...number).reduce(1, *)
    }
    var factorial: (Int) -> Int = { _ in 0 }
    
    func resetCache() {
        factorial.clearCache()
    }
}

// Test performance optimization
let optimized = PerformanceOptimized()

// First access - will compute
print("First access: \(optimized.expensiveValue)")

// Second access - will use cached value
print("Second access: \(optimized.expensiveValue)")

// Memoized function
print("Factorial 5: \(optimized.factorial(5))")
print("Factorial 5 again: \(optimized.factorial(5))")  // Cached
print("Factorial 6: \(optimized.factorial(6))")

// Clear cache
optimized.resetCache()
print("Factorial 5 after cache clear: \(optimized.factorial(5))")  // Re-computed
```

**Pattern 2: Atomic Operations and Thread Safety**
```swift
// Atomic wrapper for thread safety
@propertyWrapper
struct Atomic<T> {
    private var value: T
    private let queue = DispatchQueue(label: "atomic.queue", attributes: .concurrent)
    
    init(wrappedValue: T) {
        self.value = wrappedValue
    }
    
    var wrappedValue: T {
        get {
            return queue.sync { value }
        }
        set {
            queue.async(flags: .barrier) {
                self.value = newValue
            }
        }
    }
    
    // Atomic read-modify-write operation
    func modify(_ operation: (T) -> T) {
        queue.async(flags: .barrier) {
            self.value = operation(self.value)
        }
    }
    
    // Atomic compare-and-swap operation
    func compareAndSwap(expected: T, new: T) -> Bool {
        return queue.sync(flags: .barrier) {
            if self.value == expected {
                self.value = new
                return true
            }
            return false
        }
    }
}

// Usage with atomic operations
class ThreadSafeCounter {
    @Atomic var count: Int = 0
    
    func increment() {
        count.modify { $0 + 1 }
    }
    
    func decrement() {
        count.modify { $0 - 1 }
    }
    
    func compareAndSwap(expected: Int, new: Int) -> Bool {
        return count.compareAndSwap(expected: expected, new: new)
    }
}

// Test thread safety
let counter = ThreadSafeCounter()

// Multiple threads accessing the counter
DispatchQueue.concurrentPerform(iterations: 1000) { _ in
    counter.increment()
}

print("Final count: \(counter.count)")

// Test compare-and-swap
let success = counter.compareAndSwap(expected: 1000, new: 2000)
print("Compare-and-swap success: \(success)")
print("New count: \(counter.count)")
```

### Real-World Advanced Applications

**Application 1: Configuration Management System**
```swift
// Configuration wrapper with validation and persistence
@propertyWrapper
struct Config<T: Codable> {
    private let key: String
    private let defaultValue: T
    private let validator: ((T) -> Bool)?
    private let transformer: ((T) -> T)?
    
    init(wrappedValue: T, key: String, validator: ((T) -> Bool)? = nil, transformer: ((T) -> T)? = nil) {
        self.key = key
        self.defaultValue = wrappedValue
        self.validator = validator
        self.transformer = transformer
        
        // Load initial value
        if let loaded = loadValue() {
            self.wrappedValue = loaded
        }
    }
    
    var wrappedValue: T {
        get {
            return loadValue() ?? defaultValue
        }
        set {
            var value = newValue
            
            // Apply transformation
            if let transformer = transformer {
                value = transformer(value)
            }
            
            // Apply validation
            if let validator = validator, !validator(value) {
                print("Validation failed for \(key): \(value)")
                return
            }
            
            // Save value
            saveValue(value)
        }
    }
    
    private func loadValue() -> T? {
        guard let data = UserDefaults.standard.data(forKey: key) else { return nil }
        return try? JSONDecoder().decode(T.self, from: data)
    }
    
    private func saveValue(_ value: T) {
        if let data = try? JSONEncoder().encode(value) {
            UserDefaults.standard.set(data, forKey: key)
        }
    }
}

// Usage in configuration system
class AppConfiguration {
    @Config(key: "app_theme", validator: { ["light", "dark", "system"].contains($0) })
    var theme: String = "system"
    
    @Config(key: "max_retry_attempts", validator: { $0 >= 0 && $0 <= 10 })
    var maxRetries: Int = 3
    
    @Config(
        key: "api_timeout",
        validator: { $0 >= 1.0 && $0 <= 60.0 },
        transformer: { $0 * 1000 }  // Convert to milliseconds
    )
    var apiTimeout: Double = 30.0
    
    @Config(key: "debug_mode")
    var debugMode: Bool = false
}

// Test configuration system
let config = AppConfiguration()

// Valid values
config.theme = "dark"
config.maxRetries = 5
config.apiTimeout = 15.0

// Invalid values (will be rejected)
config.theme = "invalid"  // Will not be set
config.maxRetries = 15    // Will not be set
config.apiTimeout = 100.0 // Will not be set

print("Theme: \(config.theme)")
print("Max retries: \(config.maxRetries)")
print("API timeout: \(config.apiTimeout)")
```

**Application 2: Reactive Data Binding System**
```swift
// Reactive binding wrapper
@propertyWrapper
struct Reactive<T> {
    private var value: T
    private var bindings: [String: (T) -> Void] = [:]
    
    init(wrappedValue: T) {
        self.value = wrappedValue
    }
    
    var wrappedValue: T {
        get { value }
        set {
            let oldValue = value
            value = newValue
            notifyBindings(oldValue: oldValue, newValue: newValue)
        }
    }
    
    var projectedValue: ReactiveProjection<T> {
        return ReactiveProjection(wrapper: self)
    }
    
    private func notifyBindings(oldValue: T, newValue: T) {
        for (id, binding) in bindings {
            binding(newValue)
        }
    }
    
    func addBinding(id: String, _ binding: @escaping (T) -> Void) {
        bindings[id] = binding
    }
    
    func removeBinding(id: String) {
        bindings.removeValue(forKey: id)
    }
}

// Projection for reactive wrapper
class ReactiveProjection<T> {
    private weak var wrapper: Reactive<T>?
    
    init(wrapper: Reactive<T>) {
        self.wrapper = wrapper
    }
    
    func bind(id: String, _ binding: @escaping (T) -> Void) {
        wrapper?.addBinding(id: id, binding)
    }
    
    func unbind(id: String) {
        wrapper?.removeBinding(id: id)
    }
}

// Usage in reactive system
class ReactiveForm {
    @Reactive var username: String = ""
    @Reactive var email: String = ""
    @Reactive var password: String = ""
    
    init() {
        setupBindings()
    }
    
    private func setupBindings() {
        // Bind username changes to validation
        $username.bind(id: "validation") { username in
            self.validateUsername(username)
        }
        
        // Bind email changes to validation
        $email.bind(id: "validation") { email in
            self.validateEmail(email)
        }
        
        // Bind password changes to strength calculation
        $password.bind(id: "strength") { password in
            self.calculatePasswordStrength(password)
        }
    }
    
    private func validateUsername(_ username: String) {
        let isValid = username.count >= 3
        print("Username validation: \(isValid ? "Valid" : "Invalid")")
    }
    
    private func validateEmail(_ email: String) {
        let isValid = email.contains("@") && email.contains(".")
        print("Email validation: \(isValid ? "Valid" : "Invalid")")
    }
    
    private func calculatePasswordStrength(_ password: String) {
        let strength = password.count >= 8 ? "Strong" : "Weak"
        print("Password strength: \(strength)")
    }
}

// Test reactive system
let form = ReactiveForm()

// These will trigger bindings automatically
form.username = "john"
form.email = "john@example.com"
form.password = "secret123"

// Change values to see reactive updates
form.username = "j"
form.email = "invalid"
form.password = "weak"
```

This comprehensive coverage of Advanced Property Wrapper Patterns shows how they enable sophisticated, composable property behaviors in Swift. The key benefits are:

1. **Composability**: Combine multiple wrappers for complex behaviors
2. **Reusability**: Create wrappers that work across different types
3. **Performance**: Optimize property access with caching and lazy loading
4. **Thread Safety**: Ensure thread-safe property access
5. **Reactive Programming**: Create automatic data binding systems

Advanced property wrapper patterns represent the cutting edge of Swift's metaprogramming capabilities, enabling developers to create powerful, type-safe abstractions that enhance code quality and developer productivity.

---

## Swift Package Manager - Deep Dive

Swift Package Manager (SPM) is Apple's official dependency management and build system for Swift projects. It provides a modern, integrated way to manage dependencies, build projects, and distribute Swift packages across all Apple platforms. Understanding SPM is essential for modern Swift development, especially when working with open-source libraries and creating reusable components.

### Understanding Swift Package Manager Fundamentals

**What Swift Package Manager Actually Is:**
Swift Package Manager is a tool for managing the distribution of Swift code that integrates with the Swift build system to automate the process of downloading, compiling, and linking dependencies. It's designed to be simple to use while providing powerful features for package management.

```swift
// ❌ Without Swift Package Manager - manual dependency management
// 1. Download dependencies manually from GitHub
// 2. Copy source files into project
// 3. Manually manage file organization
// 4. Handle version conflicts manually
// 5. Update dependencies by repeating the process
// 6. No automatic dependency resolution
// 7. Difficult to share projects with others

// ❌ Problems:
// 1. Time-consuming manual process
// 2. No version control for dependencies
// 3. Difficult to update dependencies
// 4. Risk of breaking changes
// 5. No dependency graph management
// 6. Inconsistent project structures

// ✅ With Swift Package Manager - automated dependency management
// Package.swift automatically defines dependencies
// Automatic version resolution and conflict resolution
// Easy updates and rollbacks
// Consistent project structure
// Integration with Xcode and command line
// Support for multiple platforms and targets

// ✅ Benefits:
// 1. Automated dependency management
// 2. Version control and semantic versioning
// 3. Easy updates and rollbacks
// 4. Consistent project structure
// 5. Multi-platform support
// 6. Integration with Swift ecosystem
```

**Core SPM Concepts:**
```swift
// 1. Package - A collection of Swift source files and a manifest
// 2. Target - A module of code that can be compiled independently
// 3. Product - Something that can be consumed by other packages
// 4. Dependency - A requirement for another package
// 5. Manifest - Package.swift file that describes the package
// 6. Repository - Source control location for the package
```

### Basic Package Structure and Configuration

**Package.swift Manifest File:**
```swift
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "MyLibrary",
    platforms: [
        .iOS(.v13),
        .macOS(.v10_15),
        .tvOS(.v13),
        .watchOS(.v6)
    ],
    products: [
        // Library that can be imported by other packages
        .library(
            name: "MyLibrary",
            targets: ["MyLibrary"]
        ),
        // Executable that can be run
        .executable(
            name: "MyTool",
            targets: ["MyTool"]
        )
    ],
    dependencies: [
        // Remote dependencies
        .package(url: "https://github.com/apple/swift-log.git", from: "1.0.0"),
        .package(url: "https://github.com/apple/swift-argument-parser.git", from: "1.0.0"),
        
        // Local dependencies
        .package(path: "../LocalDependency"),
        
        // Git dependencies with specific requirements
        .package(url: "https://github.com/example/package.git", .exact("2.1.0")),
        .package(url: "https://github.com/example/another.git", .upToNextMajor(from: "1.0.0")),
        .package(url: "https://github.com/example/third.git", .upToNextMinor(from: "1.2.0")),
        .package(url: "https://github.com/example/fourth.git", .branch("develop")),
        .package(url: "https://github.com/example/fifth.git", .revision("abc123"))
    ],
    targets: [
        // Main library target
        .target(
            name: "MyLibrary",
            dependencies: [
                .product(name: "Logging", package: "swift-log"),
                "LocalDependency"
            ],
            path: "Sources/MyLibrary",
            exclude: ["Tests"],
            sources: ["Core", "Extensions"],
            resources: [
                .process("Resources"),
                .copy("Assets")
            ],
            swiftSettings: [
                .define("DEBUG", .when(configuration: .debug)),
                .unsafeFlags(["-Xfrontend", "-enable-actor-data-race-checks"])
            ]
        ),
        
        // Test target
        .testTarget(
            name: "MyLibraryTests",
            dependencies: ["MyLibrary"],
            path: "Tests/MyLibraryTests"
        ),
        
        // Executable target
        .executableTarget(
            name: "MyTool",
            dependencies: [
                .product(name: "ArgumentParser", package: "swift-argument-parser")
            ],
            path: "Sources/MyTool"
        ),
        
        // Binary target (for pre-compiled frameworks)
        .binaryTarget(
            name: "BinaryFramework",
            path: "Binaries/BinaryFramework.xcframework"
        )
    ]
)
```

**Package Directory Structure:**
```swift
MyLibrary/
├── Package.swift                 // Package manifest
├── Sources/                      // Source code directory
│   ├── MyLibrary/               // Main library target
│   │   ├── Core/                // Core functionality
│   │   │   ├── Models.swift
│   │   │   ├── Services.swift
│   │   │   └── Utilities.swift
│   │   ├── Extensions/          // Swift extensions
│   │   │   ├── String+Extensions.swift
│   │   │   └── Date+Extensions.swift
│   │   ├── Resources/           // Resources (images, data files)
│   │   │   ├── images/
│   │   │   └── data.json
│   │   └── Assets/              // Assets (copy as-is)
│   │       └── config.plist
│   └── MyTool/                  // Executable target
│       └── main.swift
├── Tests/                        // Test code directory
│   └── MyLibraryTests/          // Test target
│       ├── ModelsTests.swift
│       ├── ServicesTests.swift
│       └── UtilitiesTests.swift
├── Binaries/                     // Binary targets
│   └── BinaryFramework.xcframework
├── Documentation/                // Package documentation
│   └── README.md
├── .gitignore                    // Git ignore file
└── README.md                     // Package description
```

**Basic Package Creation:**
```swift
// Create a new package from command line
// swift package init --type library
// swift package init --type executable
// swift package init --type empty

// Example: Creating a utility library
// swift package init --type library --name StringUtilities

// This creates:
// - Package.swift
// - Sources/StringUtilities/
// - Tests/StringUtilitiesTests/
// - .gitignore
// - README.md

// Example Package.swift for a utility library
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "StringUtilities",
    platforms: [
        .iOS(.v13),
        .macOS(.v10_15)
    ],
    products: [
        .library(
            name: "StringUtilities",
            targets: ["StringUtilities"]
        )
    ],
    dependencies: [],
    targets: [
        .target(
            name: "StringUtilities",
            dependencies: []
        ),
        .testTarget(
            name: "StringUtilitiesTests",
            dependencies: ["StringUtilities"]
        )
    ]
)
```

### Advanced Package Configuration

**Complex Dependencies and Version Resolution:**
```swift
// Advanced Package.swift with complex dependencies
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "AdvancedApp",
    platforms: [
        .iOS(.v14),
        .macOS(.v11),
        .tvOS(.v14),
        .watchOS(.v7)
    ],
    products: [
        .library(
            name: "AdvancedAppCore",
            type: .dynamic,  // Dynamic library
            targets: ["AdvancedAppCore"]
        ),
        .library(
            name: "AdvancedAppUI",
            type: .static,   // Static library
            targets: ["AdvancedAppUI"]
        ),
        .executable(
            name: "AdvancedAppCLI",
            targets: ["AdvancedAppCLI"]
        )
    ],
    dependencies: [
        // Core dependencies
        .package(url: "https://github.com/apple/swift-log.git", from: "1.0.0"),
        .package(url: "https://github.com/apple/swift-argument-parser.git", from: "1.0.0"),
        
        // Networking and data
        .package(url: "https://github.com/Alamofire/Alamofire.git", from: "5.0.0"),
        .package(url: "https://github.com/realm/SwiftLint.git", from: "0.50.0"),
        
        // UI frameworks
        .package(url: "https://github.com/onevcat/Kingfisher.git", from: "7.0.0"),
        .package(url: "https://github.com/SnapKit/SnapKit.git", from: "5.0.0"),
        
        // Testing
        .package(url: "https://github.com/Quick/Quick.git", from: "5.0.0"),
        .package(url: "https://github.com/Quick/Nimble.git", from: "10.0.0"),
        
        // Local development dependencies
        .package(path: "../SharedComponents"),
        .package(path: "../InternalTools")
    ],
    targets: [
        // Core target with multiple dependencies
        .target(
            name: "AdvancedAppCore",
            dependencies: [
                .product(name: "Logging", package: "swift-log"),
                "SharedComponents",
                "InternalTools"
            ],
            path: "Sources/Core",
            exclude: ["Legacy", "Deprecated"],
            sources: ["Models", "Services", "Networking"],
            resources: [
                .process("Resources"),
                .copy("Config")
            ],
            swiftSettings: [
                .define("DEBUG", .when(configuration: .debug)),
                .define("RELEASE", .when(configuration: .release)),
                .unsafeFlags(["-Xfrontend", "-enable-actor-data-race-checks"])
            ],
            cSettings: [
                .define("DEBUG", .when(configuration: .debug))
            ],
            cxxSettings: [
                .define("DEBUG", .when(configuration: .debug))
            ]
        ),
        
        // UI target with UI-specific dependencies
        .target(
            name: "AdvancedAppUI",
            dependencies: [
                "AdvancedAppCore",
                .product(name: "Kingfisher", package: "Kingfisher"),
                .product(name: "SnapKit", package: "SnapKit")
            ],
            path: "Sources/UI",
            resources: [
                .process("Assets"),
                .copy("Localization")
            ]
        ),
        
        // CLI target with argument parsing
        .executableTarget(
            name: "AdvancedAppCLI",
            dependencies: [
                "AdvancedAppCore",
                .product(name: "ArgumentParser", package: "swift-argument-parser")
            ],
            path: "Sources/CLI"
        ),
        
        // Test targets
        .testTarget(
            name: "AdvancedAppCoreTests",
            dependencies: [
                "AdvancedAppCore",
                .product(name: "Quick", package: "Quick"),
                .product(name: "Nimble", package: "Nimble")
            ],
            path: "Tests/Core"
        ),
        
        .testTarget(
            name: "AdvancedAppUITests",
            dependencies: [
                "AdvancedAppUI",
                .product(name: "Quick", package: "Quick"),
                .product(name: "Nimble", package: "Nimble")
            ],
            path: "Tests/UI"
        ),
        
        // Binary target for pre-compiled frameworks
        .binaryTarget(
            name: "ThirdPartyFramework",
            path: "Binaries/ThirdPartyFramework.xcframework"
        )
    ]
)
```

**Conditional Dependencies and Platform-Specific Code:**
```swift
// Package with conditional dependencies
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "CrossPlatformLibrary",
    platforms: [
        .iOS(.v13),
        .macOS(.v10_15),
        .tvOS(.v13),
        .watchOS(.v6),
        .linux
    ],
    products: [
        .library(
            name: "CrossPlatformLibrary",
            targets: ["CrossPlatformLibrary"]
        )
    ],
    dependencies: [
        // iOS/macOS specific dependencies
        .package(url: "https://github.com/apple/swift-log.git", from: "1.0.0"),
        
        // Linux specific dependencies
        .package(url: "https://github.com/apple/swift-system.git", from: "1.0.0")
    ],
    targets: [
        .target(
            name: "CrossPlatformLibrary",
            dependencies: [
                .product(name: "Logging", package: "swift-log", condition: .when(platforms: [.iOS, .macOS, .tvOS, .watchOS])),
                .product(name: "SystemPackage", package: "swift-system", condition: .when(platforms: [.linux]))
            ],
            path: "Sources/CrossPlatformLibrary",
            swiftSettings: [
                .define("DEBUG", .when(configuration: .debug)),
                .define("RELEASE", .when(configuration: .release)),
                .define("iOS", .when(platforms: [.iOS])),
                .define("macOS", .when(platforms: [.macOS])),
                .define("tvOS", .when(platforms: [.tvOS])),
                .define("watchOS", .when(platforms: [.watchOS])),
                .define("Linux", .when(platforms: [.linux]))
            ]
        ),
        
        .testTarget(
            name: "CrossPlatformLibraryTests",
            dependencies: ["CrossPlatformLibrary"]
        )
    ]
)

// Usage in source code with conditional compilation
#if canImport(Logging)
import Logging
#endif

#if canImport(SystemPackage)
import SystemPackage
#endif

public class CrossPlatformService {
    #if DEBUG
    private let isDebug = true
    #else
    private let isDebug = false
    #endif
    
    #if os(iOS) || os(macOS) || os(tvOS) || os(watchOS)
    private let logger = Logger(label: "CrossPlatformService")
    #endif
    
    #if os(Linux)
    private let systemInfo = SystemInfo()
    #endif
    
    public func performOperation() {
        #if DEBUG
        print("Debug mode enabled")
        #endif
        
        #if os(iOS) || os(macOS) || os(tvOS) || os(watchOS)
        logger.info("Performing operation")
        #endif
        
        #if os(Linux)
        let platform = systemInfo.platform
        print("Running on Linux: \(platform)")
        #endif
    }
}
```

**Custom Build Settings and Compiler Flags:**
```swift
// Package with custom build settings
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "CustomBuildSettings",
    platforms: [
        .iOS(.v14),
        .macOS(.v11)
    ],
    products: [
        .library(
            name: "CustomBuildSettings",
            targets: ["CustomBuildSettings"]
        )
    ],
    dependencies: [],
    targets: [
        .target(
            name: "CustomBuildSettings",
            dependencies: [],
            swiftSettings: [
                // Define custom flags
                .define("CUSTOM_FEATURE"),
                .define("API_VERSION", to: "2"),
                .define("DEBUG", .when(configuration: .debug)),
                .define("RELEASE", .when(configuration: .release)),
                
                // Enable specific language features
                .enableUpcomingFeature("BareSlashRegexLiterals"),
                .enableUpcomingFeature("ConciseMagicFile"),
                .enableUpcomingFeature("ExistentialAny"),
                .enableUpcomingFeature("ForwardTrailingClosures"),
                .enableUpcomingFeature("ImplicitOpenExistentials"),
                .enableUpcomingFeature("StrictConcurrency"),
                
                // Custom unsafe flags (use with caution)
                .unsafeFlags(["-Xfrontend", "-enable-actor-data-race-checks"]),
                .unsafeFlags(["-Xswiftc", "-O"]),
                
                // Conditional settings
                .define("IOS_SPECIFIC", .when(platforms: [.iOS])),
                .define("MACOS_SPECIFIC", .when(platforms: [.macOS]))
            ],
            cSettings: [
                .define("C_DEBUG", .when(configuration: .debug)),
                .define("C_RELEASE", .when(configuration: .release)),
                .headerSearchPath("include"),
                .unsafeFlags(["-O2"])
            ],
            cxxSettings: [
                .define("CXX_DEBUG", .when(configuration: .debug)),
                .define("CXX_RELEASE", .when(configuration: .release)),
                .headerSearchPath("include"),
                .unsafeFlags(["-std=c++17"])
            ]
        ),
        
        .testTarget(
            name: "CustomBuildSettingsTests",
            dependencies: ["CustomBuildSettings"]
        )
    ]
)
```

### Package Development and Testing

**Local Package Development:**
```swift
// Working with local packages during development
// Package.swift with local dependencies
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "MainApp",
    products: [
        .library(name: "MainApp", targets: ["MainApp"])
    ],
    dependencies: [
        // Local package for development
        .package(path: "../SharedLibrary"),
        
        // Local package with specific branch
        .package(path: "../ExperimentalFeature"),
        
        // Remote dependencies
        .package(url: "https://github.com/apple/swift-log.git", from: "1.0.0")
    ],
    targets: [
        .target(
            name: "MainApp",
            dependencies: [
                "SharedLibrary",
                "ExperimentalFeature",
                .product(name: "Logging", package: "swift-log")
            ]
        ),
        
        .testTarget(
            name: "MainAppTests",
            dependencies: ["MainApp"]
        )
    ]
)

// Directory structure for local development
// MainApp/
// ├── Package.swift
// ├── Sources/
// └── Tests/
// ../SharedLibrary/
// ├── Package.swift
// ├── Sources/
// └── Tests/
// ../ExperimentalFeature/
// ├── Package.swift
// ├── Sources/
// └── Tests/

// Commands for local development
// swift package resolve          # Resolve dependencies
// swift package update          # Update dependencies
// swift package clean           # Clean build artifacts
// swift package reset           # Reset package state
// swift package show-dependencies # Show dependency graph
// swift package describe        # Show package description
```

**Package Testing and Test Dependencies:**
```swift
// Package with comprehensive testing setup
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "TestableLibrary",
    products: [
        .library(name: "TestableLibrary", targets: ["TestableLibrary"])
    ],
    dependencies: [
        // Testing frameworks
        .package(url: "https://github.com/Quick/Quick.git", from: "5.0.0"),
        .package(url: "https://github.com/Quick/Nimble.git", from: "10.0.0"),
        .package(url: "https://github.com/AliSoftware/OHHTTPStubs.git", from: "9.0.0"),
        .package(url: "https://github.com/pointfreeco/swift-snapshot-testing.git", from: "1.0.0")
    ],
    targets: [
        .target(
            name: "TestableLibrary",
            dependencies: []
        ),
        
        // Unit tests
        .testTarget(
            name: "TestableLibraryTests",
            dependencies: [
                "TestableLibrary",
                .product(name: "Quick", package: "Quick"),
                .product(name: "Nimble", package: "Nimble")
            ]
        ),
        
        // Integration tests
        .testTarget(
            name: "TestableLibraryIntegrationTests",
            dependencies: [
                "TestableLibrary",
                .product(name: "Quick", package: "Quick"),
                .product(name: "Nimble", package: "Nimble"),
                .product(name: "OHHTTPStubs", package: "OHHTTPStubs")
            ]
        ),
        
        // Snapshot tests
        .testTarget(
            name: "TestableLibrarySnapshotTests",
            dependencies: [
                "TestableLibrary",
                .product(name: "SnapshotTesting", package: "swift-snapshot-testing")
            ]
        )
    ]
)

// Example test file structure
// Tests/
// ├── TestableLibraryTests/
// │   ├── ModelsTests.swift
// │   ├── ServicesTests.swift
// │   └── UtilitiesTests.swift
// ├── TestableLibraryIntegrationTests/
// │   ├── APIIntegrationTests.swift
// │   └── DatabaseIntegrationTests.swift
// └── TestableLibrarySnapshotTests/
//     ├── ViewSnapshotTests.swift
//     └── DataSnapshotTests.swift

// Running tests
// swift test                           # Run all tests
// swift test --filter ModelTests      # Run specific test class
// swift test --filter "test.*Model"   # Run tests matching pattern
// swift test --parallel               # Run tests in parallel
// swift test --enable-test-discovery  # Enable test discovery
```

**Package Documentation and Resources:**
```swift
// Package with documentation and resources
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "DocumentedLibrary",
    products: [
        .library(name: "DocumentedLibrary", targets: ["DocumentedLibrary"])
    ],
    dependencies: [],
    targets: [
        .target(
            name: "DocumentedLibrary",
            dependencies: [],
            resources: [
                // Processed resources (images, data files)
                .process("Resources/Images"),
                .process("Resources/Data"),
                
                // Copied resources (configuration files, etc.)
                .copy("Resources/Config"),
                .copy("Resources/Localization")
            ]
        ),
        
        .testTarget(
            name: "DocumentedLibraryTests",
            dependencies: ["DocumentedLibrary"]
        )
    ]
)

// Directory structure for resources
// Sources/DocumentedLibrary/
// ├── Core/
// ├── Resources/
// │   ├── Images/
// │   │   ├── icon.png
// │   │   └── logo.svg
// │   ├── Data/
// │   │   ├── sample.json
// │   │   └── config.yaml
// │   ├── Config/
// │   │   ├── default.plist
// │   │   └── production.plist
// │   └── Localization/
// │       ├── en.lproj/
// │       └── es.lproj/

// Using resources in code
import Foundation

public class ResourceManager {
    public static func loadImage(named name: String) -> Data? {
        guard let url = Bundle.module.url(forResource: name, withExtension: nil, subdirectory: "Resources/Images") else {
            return nil
        }
        return try? Data(contentsOf: url)
    }
    
    public static func loadData(named name: String) -> Data? {
        guard let url = Bundle.module.url(forResource: name, withExtension: nil, subdirectory: "Resources/Data") else {
            return nil
        }
        return try? Data(contentsOf: url)
    }
    
    public static func loadConfig(named name: String) -> [String: Any]? {
        guard let url = Bundle.module.url(forResource: name, withExtension: "plist", subdirectory: "Resources/Config") else {
            return nil
        }
        return NSDictionary(contentsOf: url) as? [String: Any]
    }
}

// Documentation comments
/**
 A comprehensive library for managing application resources.
 
 This library provides utilities for:
 - Loading and managing images
 - Handling configuration files
 - Managing localization resources
 - Processing data files
 
 ## Usage
 
 ```swift
 let imageData = ResourceManager.loadImage(named: "icon")
 let config = ResourceManager.loadConfig(named: "default")
 ```
 
 ## Requirements
 
 - iOS 13.0+
 - macOS 10.15+
 - Swift 5.9+
 
 ## Installation
 
 ### Swift Package Manager
 ```swift
 dependencies: [
     .package(url: "https://github.com/example/DocumentedLibrary.git", from: "1.0.0")
 ]
 ```
 */
public struct DocumentedLibrary {
    /// The current version of the library
    public static let version = "1.0.0"
    
    /// Initialize the library
    public init() {}
}
```

### Advanced Package Features

**Binary Targets and Pre-compiled Frameworks:**
```swift
// Package with binary targets
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "BinaryPackage",
    products: [
        .library(name: "BinaryPackage", targets: ["BinaryPackage"])
    ],
    dependencies: [],
    targets: [
        .target(
            name: "BinaryPackage",
            dependencies: ["BinaryFramework"]
        ),
        
        // Binary target for pre-compiled framework
        .binaryTarget(
            name: "BinaryFramework",
            path: "Binaries/BinaryFramework.xcframework"
        ),
        
        // Binary target with checksum verification
        .binaryTarget(
            name: "VerifiedFramework",
            url: "https://example.com/framework.xcframework.zip",
            checksum: "1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef"
        ),
        
        .testTarget(
            name: "BinaryPackageTests",
            dependencies: ["BinaryPackage"]
        )
    ]
)

// Directory structure for binary targets
// BinaryPackage/
// ├── Package.swift
// ├── Sources/
// │   └── BinaryPackage/
// ├── Tests/
// └── Binaries/
//     ├── BinaryFramework.xcframework/
//     └── VerifiedFramework.xcframework/

// Using binary targets in code
import BinaryFramework

public class BinaryPackage {
    public func useBinaryFramework() {
        // Use functionality from the binary framework
        let result = BinaryFramework.performOperation()
        print("Result: \(result)")
    }
}
```

**Conditional Products and Targets:**
```swift
// Package with conditional products and targets
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "ConditionalPackage",
    platforms: [
        .iOS(.v13),
        .macOS(.v10_15)
    ],
    products: [
        // Core library available on all platforms
        .library(
            name: "ConditionalPackage",
            targets: ["ConditionalPackage"]
        ),
        
        // iOS-specific library
        .library(
            name: "ConditionalPackageiOS",
            targets: ["ConditionalPackageiOS"],
            condition: .when(platforms: [.iOS])
        ),
        
        // macOS-specific library
        .library(
            name: "ConditionalPackageMacOS",
            targets: ["ConditionalPackageMacOS"],
            condition: .when(platforms: [.macOS])
        )
    ],
    dependencies: [],
    targets: [
        // Core target
        .target(
            name: "ConditionalPackage",
            dependencies: []
        ),
        
        // iOS-specific target
        .target(
            name: "ConditionalPackageiOS",
            dependencies: ["ConditionalPackage"],
            condition: .when(platforms: [.iOS])
        ),
        
        // macOS-specific target
        .target(
            name: "ConditionalPackageMacOS",
            dependencies: ["ConditionalPackage"],
            condition: .when(platforms: [.macOS])
        ),
        
        // Test targets
        .testTarget(
            name: "ConditionalPackageTests",
            dependencies: ["ConditionalPackage"]
        ),
        
        .testTarget(
            name: "ConditionalPackageiOSTests",
            dependencies: ["ConditionalPackageiOS"],
            condition: .when(platforms: [.iOS])
        ),
        
        .testTarget(
            name: "ConditionalPackageMacOSTests",
            dependencies: ["ConditionalPackageMacOS"],
            condition: .when(platforms: [.macOS])
        )
    ]
)
```

**Package Plugins and Custom Build Tools:**
```swift
// Package with plugins
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "PluginPackage",
    products: [
        .library(name: "PluginPackage", targets: ["PluginPackage"])
    ],
    dependencies: [
        // Plugin dependencies
        .package(url: "https://github.com/apple/swift-format.git", from: "0.50.0")
    ],
    targets: [
        .target(
            name: "PluginPackage",
            dependencies: []
        ),
        
        // Plugin target
        .plugin(
            name: "FormatPlugin",
            capability: .buildTool,
            dependencies: [
                .product(name: "SwiftFormat", package: "swift-format")
            ]
        ),
        
        .testTarget(
            name: "PluginPackageTests",
            dependencies: ["PluginPackage"]
        )
    ]
)

// Using plugins in Package.swift
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "MainApp",
    products: [
        .executable(name: "MainApp", targets: ["MainApp"])
    ],
    dependencies: [
        .package(path: "../PluginPackage")
    ],
    targets: [
        .executableTarget(
            name: "MainApp",
            dependencies: ["PluginPackage"],
            plugins: [
                .plugin(name: "FormatPlugin", package: "PluginPackage")
            ]
        )
    ]
)
```

### Real-World Package Examples

**Example 1: Networking Library Package**
```swift
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "NetworkingKit",
    platforms: [
        .iOS(.v13),
        .macOS(.v10_15),
        .tvOS(.v13),
        .watchOS(.v6)
    ],
    products: [
        .library(
            name: "NetworkingKit",
            targets: ["NetworkingKit"]
        ),
        .library(
            name: "NetworkingKitCombine",
            targets: ["NetworkingKitCombine"]
        )
    ],
    dependencies: [
        .package(url: "https://github.com/apple/swift-log.git", from: "1.0.0")
    ],
    targets: [
        .target(
            name: "NetworkingKit",
            dependencies: [
                .product(name: "Logging", package: "swift-log")
            ],
            swiftSettings: [
                .define("DEBUG", .when(configuration: .debug))
            ]
        ),
        
        .target(
            name: "NetworkingKitCombine",
            dependencies: [
                "NetworkingKit"
            ]
        ),
        
        .testTarget(
            name: "NetworkingKitTests",
            dependencies: ["NetworkingKit"]
        ),
        
        .testTarget(
            name: "NetworkingKitCombineTests",
            dependencies: ["NetworkingKitCombine"]
        )
    ]
)

// Sources/NetworkingKit/NetworkManager.swift
import Foundation
import Logging

public class NetworkManager {
    private let logger = Logger(label: "NetworkingKit.NetworkManager")
    private let session: URLSession
    
    public init(session: URLSession = .shared) {
        self.session = session
    }
    
    public func request<T: Codable>(_ endpoint: Endpoint) async throws -> T {
        logger.info("Making request to \(endpoint.url)")
        
        let (data, response) = try await session.data(from: endpoint.url)
        
        guard let httpResponse = response as? HTTPURLResponse else {
            throw NetworkError.invalidResponse
        }
        
        guard 200...299 ~= httpResponse.statusCode else {
            throw NetworkError.serverError(httpResponse.statusCode, data)
        }
        
        return try JSONDecoder().decode(T.self, from: data)
    }
}

public struct Endpoint {
    public let url: URL
    
    public init(url: URL) {
        self.url = url
    }
}

public enum NetworkError: Error {
    case invalidResponse
    case serverError(Int, Data)
}
```

**Example 2: UI Component Library Package**
```swift
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "UIComponents",
    platforms: [
        .iOS(.v14),
        .macOS(.v11)
    ],
    products: [
        .library(
            name: "UIComponents",
            targets: ["UIComponents"]
        )
    ],
    dependencies: [],
    targets: [
        .target(
            name: "UIComponents",
            dependencies: [],
            resources: [
                .process("Resources/Assets.xcassets"),
                .copy("Resources/Localization")
            ]
        ),
        
        .testTarget(
            name: "UIComponentsTests",
            dependencies: ["UIComponents"]
        )
    ]
)

// Sources/UIComponents/CustomButton.swift
#if canImport(UIKit)
import UIKit

public class CustomButton: UIButton {
    public override init(frame: CGRect) {
        super.init(frame: frame)
        setupButton()
    }
    
    public required init?(coder: NSCoder) {
        super.init(coder: coder)
        setupButton()
    }
    
    private func setupButton() {
        layer.cornerRadius = 8
        backgroundColor = .systemBlue
        setTitleColor(.white, for: .normal)
    }
}
#elseif canImport(AppKit)
import AppKit

public class CustomButton: NSButton {
    public override init(frame frameRect: NSRect) {
        super.init(frame: frameRect)
        setupButton()
    }
    
    public required init?(coder: NSCoder) {
        super.init(coder: coder)
        setupButton()
    }
    
    private func setupButton() {
        bezelStyle = .rounded
        isBordered = true
    }
}
#endif
```

### Package Management Best Practices

**Version Management and Dependency Resolution:**
```swift
// Best practices for dependency management
// 1. Use semantic versioning
// 2. Specify version ranges appropriately
// 3. Lock dependency versions for production
// 4. Regular dependency updates

// Package.resolved file (auto-generated)
// {
//   "pins" : [
//     {
//       "identity" : "swift-log",
//       "kind" : "remoteSourceControl",
//       "location" : "https://github.com/apple/swift-log.git",
//       "state" : {
//         "revision" : "abc123...",
//         "version" : "1.0.0"
//       }
//     }
//   ],
//   "version" : 2
// }

// Commands for dependency management
// swift package resolve              # Resolve dependencies
// swift package update               # Update dependencies
// swift package show-dependencies    # Show dependency graph
// swift package describe             # Show package details
// swift package clean                # Clean build artifacts
// swift package reset                # Reset package state

// Dependency update strategies
// 1. Regular updates (weekly/monthly)
// 2. Security updates (immediate)
// 3. Major version updates (planned)
// 4. Lock file management
```

**Security and Trust:**
```swift
// Security best practices
// 1. Use HTTPS URLs for dependencies
// 2. Verify checksums for binary targets
// 3. Review dependency sources
// 4. Keep dependencies updated
// 5. Use trusted package sources

// Example with checksum verification
.binaryTarget(
    name: "SecureFramework",
    url: "https://trusted-source.com/framework.xcframework.zip",
    checksum: "verified-checksum-here"
)

// Package signing and verification
// 1. GPG signatures for packages
// 2. Certificate pinning
// 3. Source code review
// 4. Dependency scanning tools
```

This comprehensive coverage of Swift Package Manager shows how it provides a modern, integrated approach to dependency management in Swift. The key benefits are:

1. **Automated Management**: Automatic dependency resolution and updates
2. **Version Control**: Semantic versioning and conflict resolution
3. **Multi-Platform**: Support for all Apple platforms and Linux
4. **Integration**: Seamless integration with Xcode and command line
5. **Security**: Built-in security features and verification

Swift Package Manager is the standard way to manage dependencies in modern Swift development, providing a robust foundation for building and distributing Swift packages.
