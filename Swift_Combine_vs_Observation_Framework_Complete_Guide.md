# Swift Combine vs Observation Framework Complete Guide 🚀

## Table of Contents
1. [Introduction](#introduction)
2. [Swift Observation Framework](#swift-observation-framework)
3. [Combine Framework](#combine-framework)
4. [Framework Comparison](#framework-comparison)
5. [When to Use Each Framework](#when-to-use-each-framework)
6. [Migration Strategies](#migration-strategies)
7. [Best Practices](#best-practices)
8. [Real-World Examples](#real-world-examples)

---

## Introduction

Swift provides two powerful frameworks for reactive programming and state management:

- **Swift Observation Framework** (iOS 17+): Apple's latest state observation system
- **Combine Framework** (iOS 13+): Apple's reactive programming framework

This guide provides comprehensive coverage of both frameworks, their use cases, and when to choose one over the other.

---

## Swift Observation Framework

The Swift Observation framework is Apple's modern approach to state observation, introduced in iOS 17. It's designed to be simpler, more performant, and easier to use than previous solutions.

### Core Concepts

#### 1. @Observable Macro
```swift
@Observable
class User {
    var name: String = ""
    var age: Int = 0
    var email: String = ""
    
    init(name: String, age: Int, email: String) {
        self.name = name
        self.age = age
        self.email = email
    }
}

// Usage in SwiftUI
struct UserView: View {
    let user: User
    
    var body: some View {
        VStack {
            Text(user.name)
            Text("\(user.age)")
            Text(user.email)
        }
        .onChange(of: user.name) { oldValue, newValue in
            print("Name changed from \(oldValue) to \(newValue)")
        }
    }
}
```

#### 2. Observation Framework Protocols
```swift
import Observation

@Observable
class DataStore {
    var items: [String] = []
    var isLoading = false
    var errorMessage: String?
    
    func addItem(_ item: String) {
        items.append(item)
    }
    
    func removeItem(at index: Int) {
        items.remove(at: index)
    }
    
    func clearItems() {
        items.removeAll()
    }
}

// Manual observation
let store = DataStore()
let observation = withObservationTracking {
    // This closure will re-run when observed properties change
    print("Items count: \(store.items.count)")
    print("Loading: \(store.isLoading)")
}

// Trigger changes
store.addItem("New Item")
store.isLoading = true
```

#### 3. Advanced Observation Patterns
```swift
@Observable
class AdvancedStore {
    private var _items: [String] = []
    var items: [String] {
        get { _items }
        set { _items = newValue }
    }
    
    var itemCount: Int { items.count }
    var isEmpty: Bool { items.isEmpty }
    
    func addItem(_ item: String) {
        _items.append(item)
    }
}

// Computed properties are automatically observed
let store = AdvancedStore()
withObservationTracking {
    print("Store is empty: \(store.isEmpty)")
    print("Item count: \(store.itemCount)")
}

store.addItem("First Item")
// Both isEmpty and itemCount will trigger observation
```

### SwiftUI Integration

#### 1. Automatic Updates
```swift
@Observable
class CounterViewModel {
    var count = 0
    var isRunning = false
    
    func increment() {
        count += 1
    }
    
    func decrement() {
        count -= 1
    }
    
    func startTimer() {
        isRunning = true
        Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { _ in
            if self.isRunning {
                self.increment()
            }
        }
    }
    
    func stopTimer() {
        isRunning = false
    }
}

struct CounterView: View {
    @State private var viewModel = CounterViewModel()
    
    var body: some View {
        VStack(spacing: 20) {
            Text("Count: \(viewModel.count)")
                .font(.largeTitle)
                .fontWeight(.bold)
            
            HStack(spacing: 20) {
                Button("Increment") {
                    viewModel.increment()
                }
                .buttonStyle(.borderedProminent)
                
                Button("Decrement") {
                    viewModel.decrement()
                }
                .buttonStyle(.borderedProminent)
            }
            
            Button(viewModel.isRunning ? "Stop Timer" : "Start Timer") {
                if viewModel.isRunning {
                    viewModel.stopTimer()
                } else {
                    viewModel.startTimer()
                }
            }
            .buttonStyle(.borderedProminent)
            .tint(viewModel.isRunning ? .red : .green)
        }
        .padding()
    }
}
```

#### 2. Form Validation
```swift
@Observable
class LoginForm {
    var email = ""
    var password = ""
    var isFormValid = false
    var errorMessage = ""
    
    private func validateForm() {
        let emailValid = isValidEmail(email)
        let passwordValid = password.count >= 8
        
        isFormValid = emailValid && passwordValid
        
        if !emailValid {
            errorMessage = "Please enter a valid email"
        } else if !passwordValid {
            errorMessage = "Password must be at least 8 characters"
        } else {
            errorMessage = ""
        }
    }
    
    private func isValidEmail(_ email: String) -> Bool {
        let emailRegex = "[A-Z0-9a-z._%+-]+@[A-Za-z0-9.-]+\\.[A-Za-z]{2,64}"
        let emailPredicate = NSPredicate(format: "SELF MATCHES %@", emailRegex)
        return emailPredicate.evaluate(with: email)
    }
    
    // Trigger validation when properties change
    var _email: String {
        get { email }
        set { 
            email = newValue
            validateForm()
        }
    }
    
    var _password: String {
        get { password }
        set { 
            password = newValue
            validateForm()
        }
    }
}

struct LoginView: View {
    @State private var form = LoginForm()
    
    var body: some View {
        VStack(spacing: 20) {
            Text("Login")
                .font(.largeTitle)
                .fontWeight(.bold)
            
            VStack(alignment: .leading, spacing: 8) {
                Text("Email")
                    .font(.headline)
                TextField("Enter email", text: $form._email)
                    .textFieldStyle(.roundedBorder)
                    .textInputAutocapitalization(.never)
                    .keyboardType(.emailAddress)
            }
            
            VStack(alignment: .leading, spacing: 8) {
                Text("Password")
                    .font(.headline)
                SecureField("Enter password", text: $form._password)
                    .textFieldStyle(.roundedBorder)
            }
            
            if !form.errorMessage.isEmpty {
                Text(form.errorMessage)
                    .foregroundColor(.red)
                    .font(.caption)
            }
            
            Button("Login") {
                // Handle login
            }
            .buttonStyle(.borderedProminent)
            .disabled(!form.isFormValid)
        }
        .padding()
    }
}
```

#### 3. Complex State Management
```swift
@Observable
class ShoppingCart {
    private var _items: [CartItem] = []
    var items: [CartItem] { _items }
    
    var totalItems: Int { _items.reduce(0) { $0 + $1.quantity } }
    var totalPrice: Double { _items.reduce(0) { $0 + ($1.price * Double($1.quantity)) } }
    var isEmpty: Bool { _items.isEmpty }
    
    func addItem(_ item: Product) {
        if let existingIndex = _items.firstIndex(where: { $0.productId == item.id }) {
            _items[existingIndex].quantity += 1
        } else {
            let cartItem = CartItem(productId: item.id, name: item.name, price: item.price, quantity: 1)
            _items.append(cartItem)
        }
    }
    
    func removeItem(_ item: CartItem) {
        _items.removeAll { $0.productId == item.productId }
    }
    
    func updateQuantity(for productId: UUID, quantity: Int) {
        if let index = _items.firstIndex(where: { $0.productId == productId }) {
            if quantity <= 0 {
                _items.remove(at: index)
            } else {
                _items[index].quantity = quantity
            }
        }
    }
    
    func clearCart() {
        _items.removeAll()
    }
}

struct CartItem {
    let productId: UUID
    let name: String
    let price: Double
    var quantity: Int
}

struct Product {
    let id: UUID
    let name: String
    let price: Double
    let imageURL: String
}

struct ShoppingCartView: View {
    @State private var cart = ShoppingCart()
    
    var body: some View {
        NavigationView {
            VStack {
                if cart.isEmpty {
                    VStack(spacing: 20) {
                        Image(systemName: "cart")
                            .font(.system(size: 60))
                            .foregroundColor(.gray)
                        Text("Your cart is empty")
                            .font(.title2)
                            .foregroundColor(.gray)
                    }
                    .frame(maxWidth: .infinity, maxHeight: .infinity)
                } else {
                    List(cart.items) { item in
                        HStack {
                            VStack(alignment: .leading) {
                                Text(item.name)
                                    .font(.headline)
                                Text("$\(item.price, specifier: "%.2f")")
                                    .foregroundColor(.secondary)
                            }
                            
                            Spacer()
                            
                            Stepper("\(item.quantity)", value: Binding(
                                get: { item.quantity },
                                set: { cart.updateQuantity(for: item.productId, quantity: $0) }
                            ), in: 1...99)
                        }
                    }
                    
                    VStack(spacing: 16) {
                        HStack {
                            Text("Total Items:")
                                .font(.headline)
                            Spacer()
                            Text("\(cart.totalItems)")
                                .font(.headline)
                        }
                        
                        HStack {
                            Text("Total Price:")
                                .font(.title2)
                                .fontWeight(.bold)
                            Spacer()
                            Text("$\(cart.totalPrice, specifier: "%.2f")")
                                .font(.title2)
                                .fontWeight(.bold)
                        }
                        
                        Button("Checkout") {
                            // Handle checkout
                        }
                        .buttonStyle(.borderedProminent)
                        .frame(maxWidth: .infinity)
                    }
                    .padding()
                }
            }
            .navigationTitle("Shopping Cart")
            .toolbar {
                if !cart.isEmpty {
                    ToolbarItem(placement: .navigationBarTrailing) {
                        Button("Clear") {
                            cart.clearCart()
                        }
                    }
                }
            }
        }
    }
}
```

### Manual Observation

#### 1. withObservationTracking
```swift
@Observable
class DataManager {
    var data: [String] = []
    var isLoading = false
    var lastUpdated: Date = Date()
    
    func fetchData() {
        isLoading = true
        
        // Simulate network request
        DispatchQueue.main.asyncAfter(deadline: .now() + 1.0) {
            self.data = ["Item 1", "Item 2", "Item 3"]
            self.isLoading = false
            self.lastUpdated = Date()
        }
    }
}

class DataObserver {
    private var dataManager: DataManager
    private var observation: Any?
    
    init(dataManager: DataManager) {
        self.dataManager = dataManager
        setupObservation()
    }
    
    private func setupObservation() {
        observation = withObservationTracking {
            print("Data updated:")
            print("  Items: \(dataManager.data)")
            print("  Loading: \(dataManager.isLoading)")
            print("  Last updated: \(dataManager.lastUpdated)")
        }
    }
    
    func startObserving() {
        // The observation will automatically trigger when properties change
        dataManager.fetchData()
    }
}

// Usage
let dataManager = DataManager()
let observer = DataObserver(dataManager: dataManager)
observer.startObserving()
```

#### 2. Custom Observation
```swift
@Observable
class WeatherService {
    var temperature: Double = 0.0
    var humidity: Double = 0.0
    var pressure: Double = 0.0
    
    func updateWeather() {
        // Simulate weather updates
        temperature = Double.random(in: 15...35)
        humidity = Double.random(in: 30...90)
        pressure = Double.random(in: 1000...1020)
    }
}

class WeatherDisplay {
    private let weatherService: WeatherService
    private var observation: Any?
    
    init(weatherService: WeatherService) {
        self.weatherService = weatherService
        setupObservation()
    }
    
    private func setupObservation() {
        observation = withObservationTracking {
            self.displayWeather()
        }
    }
    
    private func displayWeather() {
        print("=== Weather Update ===")
        print("Temperature: \(weatherService.temperature)°C")
        print("Humidity: \(weatherService.humidity)%")
        print("Pressure: \(weatherService.pressure) hPa")
        print("====================")
    }
    
    func startMonitoring() {
        // Update weather every 5 seconds
        Timer.scheduledTimer(withTimeInterval: 5.0, repeats: true) { _ in
            self.weatherService.updateWeather()
        }
    }
}

// Usage
let weatherService = WeatherService()
let display = WeatherDisplay(weatherService: weatherService)
display.startMonitoring()
```

---

## Combine Framework

The Combine framework is Apple's reactive programming framework introduced in iOS 13. It provides a declarative approach to handling asynchronous events and data streams.

### Core Concepts

#### 1. Publishers
```swift
import Combine

// Basic publishers
let justPublisher = Just("Hello, Combine!")
let sequencePublisher = [1, 2, 3, 4, 5].publisher
let timerPublisher = Timer.publish(every: 1.0, on: .main, in: .common).autoconnect()

// Custom publisher
struct CustomPublisher: Publisher {
    typealias Output = Int
    typealias Failure = Never
    
    func receive<S>(subscriber: S) where S: Subscriber, S.Input == Int, S.Failure == Never {
        let subscription = CustomSubscription(subscriber: subscriber)
        subscriber.receive(subscription: subscription)
    }
}

class CustomSubscription: Subscription {
    private var subscriber: AnySubscriber<Int, Never>?
    private var demand: Subscribers.Demand = .none
    
    init<S: Subscriber>(subscriber: S) where S: Subscriber, S.Input == Int, S.Failure == Never {
        self.subscriber = AnySubscriber(subscriber)
    }
    
    func request(_ demand: Subscribers.Demand) {
        self.demand = demand
        if demand > .none {
            subscriber?.receive(42)
            subscriber?.receive(completion: .finished)
        }
    }
    
    func cancel() {
        subscriber = nil
    }
}
```

#### 2. Subscribers
```swift
// Sink subscriber
let cancellable = sequencePublisher
    .sink(
        receiveCompletion: { completion in
            switch completion {
            case .finished:
                print("Completed successfully")
            case .failure(let error):
                print("Failed with error: \(error)")
            }
        },
        receiveValue: { value in
            print("Received value: \(value)")
        }
    )

// Assign subscriber
class ViewModel: ObservableObject {
    @Published var count: Int = 0
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        Just(42)
            .assign(to: \.count, on: self)
            .store(in: &cancellables)
    }
}

// Custom subscriber
class CustomSubscriber: Subscriber {
    typealias Input = String
    typealias Failure = Never
    
    func receive(subscription: Subscription) {
        subscription.request(.unlimited)
    }
    
    func receive(_ input: String) -> Subscribers.Demand {
        print("Custom subscriber received: \(input)")
        return .unlimited
    }
    
    func receive(completion: Subscribers.Completion<Never>) {
        print("Custom subscriber completed")
    }
}
```

#### 3. Operators
```swift
// Transformation operators
let transformedPublisher = sequencePublisher
    .map { $0 * 2 }
    .filter { $0 % 2 == 0 }
    .removeDuplicates()

// Combining operators
let namePublisher = CurrentValueSubject<String, Never>("")
let agePublisher = CurrentValueSubject<Int, Never>(0)

let combinedPublisher = namePublisher
    .combineLatest(agePublisher)
    .map { "\($0) is \($1) years old" }

// Timing operators
let debouncedPublisher = namePublisher
    .debounce(for: .milliseconds(500), scheduler: RunLoop.main)

let throttledPublisher = agePublisher
    .throttle(for: .seconds(1), scheduler: RunLoop.main, latest: true)
```

### MVVM with Combine

#### 1. User List ViewModel
```swift
class UserListViewModel: ObservableObject {
    @Published var users: [User] = []
    @Published var isLoading = false
    @Published var errorMessage: String?
    @Published var searchText = ""
    
    private let userRepository: UserRepository
    private var cancellables = Set<AnyCancellable>()
    
    init(userRepository: UserRepository) {
        self.userRepository = userRepository
        setupBindings()
    }
    
    private func setupBindings() {
        // Debounce search text to avoid excessive API calls
        $searchText
            .debounce(for: .milliseconds(300), scheduler: RunLoop.main)
            .removeDuplicates()
            .sink { [weak self] searchTerm in
                self?.searchUsers(query: searchTerm)
            }
            .store(in: &cancellables)
    }
    
    func fetchUsers() {
        isLoading = true
        errorMessage = nil
        
        userRepository.fetchUsers()
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
    
    private func searchUsers(query: String) {
        guard !query.isEmpty else {
            fetchUsers()
            return
        }
        
        isLoading = true
        
        userRepository.searchUsers(query: query)
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
}
```

#### 2. Network Layer
```swift
class NetworkManager {
    static let shared = NetworkManager()
    private let session = URLSession.shared
    
    func request<T: Codable>(_ endpoint: APIEndpoint) -> AnyPublisher<T, NetworkError> {
        guard let url = endpoint.url else {
            return Fail(error: NetworkError.invalidURL)
                .eraseToAnyPublisher()
        }
        
        var request = URLRequest(url: url)
        request.httpMethod = endpoint.method.rawValue
        request.allHTTPHeaderFields = endpoint.headers
        
        if let body = endpoint.body {
            request.httpBody = try? JSONSerialization.data(withJSONObject: body)
        }
        
        return session.dataTaskPublisher(for: request)
            .tryMap { data, response in
                guard let httpResponse = response as? HTTPURLResponse else {
                    throw NetworkError.invalidResponse
                }
                
                guard 200...299 ~= httpResponse.statusCode else {
                    throw NetworkError.httpError(statusCode: httpResponse.statusCode)
                }
                
                return data
            }
            .decode(type: T.self, decoder: JSONDecoder())
            .mapError { error in
                if let networkError = error as? NetworkError {
                    return networkError
                }
                return NetworkError.decodingError(error)
            }
            .eraseToAnyPublisher()
    }
}
```

---

## Framework Comparison

### Performance Characteristics

| Aspect | Swift Observation | Combine |
|--------|------------------|---------|
| **Memory Usage** | Lower overhead, direct property access | Higher overhead, publisher/subscriber chain |
| **CPU Usage** | Minimal, only when properties change | Higher, continuous stream processing |
| **Startup Time** | Faster, no setup required | Slower, requires publisher setup |
| **Bundle Size** | Smaller, built into Swift | Larger, separate framework |

### Use Case Comparison

#### Swift Observation Best For:
- **Simple state management** in SwiftUI
- **Property observation** without complex transformations
- **Direct data binding** to UI components
- **Performance-critical** applications
- **iOS 17+** projects

#### Combine Best For:
- **Complex data streams** and transformations
- **Asynchronous operations** (network, timers, notifications)
- **Event-driven architectures**
- **Cross-platform** compatibility (iOS 13+)
- **Reactive programming** patterns

### Code Complexity Comparison

#### Swift Observation (Simple)
```swift
@Observable
class SimpleStore {
    var count = 0
    
    func increment() {
        count += 1
    }
}

// Usage
let store = SimpleStore()
withObservationTracking {
    print("Count: \(store.count)")
}
store.increment() // Automatically triggers observation
```

#### Combine (Complex)
```swift
class ComplexStore: ObservableObject {
    @Published var count = 0
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        setupBindings()
    }
    
    private func setupBindings() {
        $count
            .sink { [weak self] newCount in
                print("Count: \(newCount)")
            }
            .store(in: &cancellables)
    }
    
    func increment() {
        count += 1
    }
}

// Usage
let store = ComplexStore()
store.increment() // Triggers publisher chain
```

---

## When to Use Each Framework

### Use Swift Observation When:

#### 1. **Simple State Management**
```swift
@Observable
class UserPreferences {
    var isDarkMode = false
    var fontSize: Double = 16.0
    var language = "en"
    
    func toggleTheme() {
        isDarkMode.toggle()
    }
    
    func updateFontSize(_ size: Double) {
        fontSize = max(12.0, min(24.0, size))
    }
}
```

#### 2. **Direct UI Binding**
```swift
struct PreferencesView: View {
    @State private var preferences = UserPreferences()
    
    var body: some View {
        Form {
            Toggle("Dark Mode", isOn: $preferences.isDarkMode)
            
            HStack {
                Text("Font Size")
                Slider(value: $preferences.fontSize, in: 12...24, step: 1)
                Text("\(Int(preferences.fontSize))")
            }
            
            Picker("Language", selection: $preferences.language) {
                Text("English").tag("en")
                Text("Spanish").tag("es")
                Text("French").tag("fr")
            }
        }
        .navigationTitle("Preferences")
    }
}
```

#### 3. **Performance-Critical Applications**
```swift
@Observable
class GameState {
    var playerPosition = CGPoint.zero
    var playerHealth = 100
    var score = 0
    var isGameOver = false
    
    func updatePlayerPosition(_ position: CGPoint) {
        playerPosition = position
        checkCollisions()
    }
    
    func takeDamage(_ amount: Int) {
        playerHealth -= amount
        if playerHealth <= 0 {
            isGameOver = true
        }
    }
    
    private func checkCollisions() {
        // Collision detection logic
    }
}
```

### Use Combine When:

#### 1. **Complex Data Streams**
```swift
class DataProcessingViewModel: ObservableObject {
    @Published var processedData: [ProcessedItem] = []
    @Published var isLoading = false
    @Published var errorMessage: String?
    
    private var cancellables = Set<AnyCancellable>()
    
    func processDataStream() {
        let dataSource = createDataSource()
        let filterStream = createFilterStream()
        let transformStream = createTransformStream()
        
        dataSource
            .combineLatest(filterStream, transformStream)
            .map { data, filter, transform in
                return self.applyFilterAndTransform(data, filter: filter, transform: transform)
            }
            .filter { $0.isValid }
            .debounce(for: .milliseconds(100), scheduler: RunLoop.main)
            .receive(on: DispatchQueue.global(qos: .userInitiated))
            .map { self.heavyProcessing($0) }
            .receive(on: RunLoop.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.isLoading = false
                    if case .failure(let error) = completion {
                        self?.errorMessage = error.localizedDescription
                    }
                },
                receiveValue: { [weak self] items in
                    self?.processedData = items
                }
            )
            .store(in: &cancellables)
    }
}
```

#### 2. **Network Operations**
```swift
class NetworkViewModel: ObservableObject {
    @Published var data: [User] = []
    @Published var isLoading = false
    @Published var errorMessage: String?
    
    private var cancellables = Set<AnyCancellable>()
    
    func fetchUsers() {
        isLoading = true
        errorMessage = nil
        
        let usersPublisher = fetchUsersFromAPI()
        let postsPublisher = fetchPostsFromAPI()
        
        Publishers.CombineLatest(usersPublisher, postsPublisher)
            .map { users, posts in
                return self.enrichUsersWithPosts(users, posts: posts)
            }
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.isLoading = false
                    if case .failure(let error) = completion {
                        self?.errorMessage = error.localizedDescription
                    }
                },
                receiveValue: { [weak self] enrichedUsers in
                    self?.data = enrichedUsers
                }
            )
            .store(in: &cancellables)
    }
}
```

#### 3. **Real-time Features**
```swift
class RealTimeViewModel: ObservableObject {
    @Published var messages: [Message] = []
    @Published var isConnected = false
    @Published var connectionStatus = "Disconnected"
    
    private let webSocketManager: WebSocketManager
    private var cancellables = Set<AnyCancellable>()
    
    init(webSocketManager: WebSocketManager) {
        self.webSocketManager = webSocketManager
        setupBindings()
    }
    
    private func setupBindings() {
        // Multiple real-time streams
        webSocketManager.connectionStatusPublisher
            .receive(on: DispatchQueue.main)
            .assign(to: \.isConnected, on: self)
            .store(in: &cancellables)
        
        webSocketManager.messagePublisher
            .receive(on: DispatchQueue.main)
            .sink { [weak self] message in
                self?.messages.append(message)
            }
            .store(in: &cancellables)
        
        webSocketManager.connectionStatusPublisher
            .receive(on: DispatchQueue.main)
            .map { $0 ? "Connected" : "Disconnected" }
            .assign(to: \.connectionStatus, on: self)
            .store(in: &cancellables)
    }
}
```

---

## Migration Strategies

### From Combine to Swift Observation

#### 1. **Simple State Management**
```swift
// Before (Combine)
class OldViewModel: ObservableObject {
    @Published var count = 0
    @Published var name = ""
    
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        $count
            .sink { [weak self] newCount in
                print("Count changed to: \(newCount)")
            }
            .store(in: &cancellables)
    }
}

// After (Swift Observation)
@Observable
class NewViewModel {
    var count = 0
    var name = ""
    
    init() {
        withObservationTracking {
            print("Count changed to: \(count)")
        }
    }
}
```

#### 2. **Complex Transformations**
```swift
// Before (Combine)
class OldComplexViewModel: ObservableObject {
    @Published var firstName = ""
    @Published var lastName = ""
    @Published var fullName = ""
    
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        Publishers.CombineLatest($firstName, $lastName)
            .map { "\($0) \($1)" }
            .assign(to: \.fullName, on: self)
            .store(in: &cancellables)
    }
}

// After (Swift Observation)
@Observable
class NewComplexViewModel {
    var firstName = ""
    var lastName = ""
    
    var fullName: String {
        "\(firstName) \(lastName)"
    }
}
```

### From Swift Observation to Combine

#### 1. **When You Need Complex Streams**
```swift
// Before (Swift Observation)
@Observable
class SimpleViewModel {
    var searchText = ""
    var results: [String] = []
    
    func performSearch() {
        // Simple search logic
        results = searchText.isEmpty ? [] : ["Result 1", "Result 2"]
    }
}

// After (Combine)
class ComplexViewModel: ObservableObject {
    @Published var searchText = ""
    @Published var results: [String] = []
    @Published var isLoading = false
    
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        $searchText
            .debounce(for: .milliseconds(300), scheduler: RunLoop.main)
            .removeDuplicates()
            .filter { !$0.isEmpty }
            .flatMap { searchTerm in
                self.searchAPI(searchTerm)
            }
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.isLoading = false
                },
                receiveValue: { [weak self] results in
                    self?.results = results
                }
            )
            .store(in: &cancellables)
    }
    
    private func searchAPI(_ term: String) -> AnyPublisher<[String], Never> {
        // API call implementation
        return Just(["API Result 1", "API Result 2"])
            .eraseToAnyPublisher()
    }
}
```

---

## Best Practices

### Swift Observation Best Practices

#### 1. **Keep It Simple**
```swift
@Observable
class GoodExample {
    var name = ""
    var age = 0
    
    // Simple computed property
    var isAdult: Bool { age >= 18 }
    
    // Simple method
    func incrementAge() {
        age += 1
    }
}

// Avoid complex logic in @Observable classes
@Observable
class BadExample {
    var name = ""
    var age = 0
    
    // Don't do complex operations here
    func complexBusinessLogic() {
        // This should be in a separate service
    }
}
```

#### 2. **Use Computed Properties**
```swift
@Observable
class ShoppingCart {
    private var _items: [Item] = []
    var items: [Item] { _items }
    
    // Computed properties are automatically observed
    var totalPrice: Double { _items.reduce(0) { $0 + $1.price } }
    var itemCount: Int { _items.count }
    var isEmpty: Bool { _items.isEmpty }
    
    func addItem(_ item: Item) {
        _items.append(item)
    }
}
```

#### 3. **Minimize Property Changes**
```swift
@Observable
class EfficientExample {
    private var _data: [String] = []
    var data: [String] { _data }
    
    // Batch updates to minimize observation triggers
    func updateData(_ newData: [String]) {
        _data = newData // Single property change
    }
    
    // Instead of multiple individual updates
    func inefficientUpdate(_ newData: [String]) {
        _data.removeAll()
        for item in newData {
            _data.append(item) // Multiple property changes
        }
    }
}
```

### Combine Best Practices

#### 1. **Memory Management**
```swift
class GoodCombineExample: ObservableObject {
    @Published var data: [String] = []
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        setupBindings()
    }
    
    private func setupBindings() {
        // Always store cancellables
        $data
            .sink { [weak self] newData in
                self?.processData(newData)
            }
            .store(in: &cancellables)
    }
    
    deinit {
        // Cancellables are automatically cancelled
        print("ViewModel deallocated")
    }
}
```

#### 2. **Error Handling**
```swift
class RobustCombineExample: ObservableObject {
    @Published var data: [String] = []
    @Published var errorMessage: String?
    
    private var cancellables = Set<AnyCancellable>()
    
    func fetchData() {
        dataPublisher
            .catch { error in
                // Handle specific errors
                switch error {
                case NetworkError.noConnection:
                    return Just(["Offline data"])
                case NetworkError.serverError:
                    return Just(["Cached data"])
                default:
                    return Just([])
                }
            }
            .replaceError(with: [])
            .receive(on: DispatchQueue.main)
            .assign(to: \.data, on: self)
            .store(in: &cancellables)
    }
}
```

#### 3. **Scheduler Usage**
```swift
class EfficientCombineExample: ObservableObject {
    @Published var processedData: [String] = []
    
    private var cancellables = Set<AnyCancellable>()
    
    func processData() {
        dataPublisher
            .receive(on: DispatchQueue.global(qos: .userInitiated))
            .map { data in
                // Heavy processing on background queue
                return self.heavyProcessing(data)
            }
            .receive(on: RunLoop.main)
            .assign(to: \.processedData, on: self)
            .store(in: &cancellables)
    }
}
```

---

## Real-World Examples

### E-commerce App Architecture

#### Swift Observation Approach
```swift
@Observable
class ProductStore {
    private var _products: [Product] = []
    var products: [Product] { _products }
    
    var featuredProducts: [Product] { _products.filter { $0.isFeatured } }
    var onSaleProducts: [Product] { _products.filter { $0.isOnSale } }
    
    func loadProducts() async {
        // Async loading
        let loadedProducts = await ProductAPI.fetchProducts()
        _products = loadedProducts
    }
}

@Observable
class ShoppingCart {
    private var _items: [CartItem] = []
    var items: [CartItem] { _items }
    
    var totalPrice: Double { _items.reduce(0) { $0 + $1.totalPrice } }
    var itemCount: Int { _items.reduce(0) { $0 + $1.quantity } }
    
    func addItem(_ product: Product, quantity: Int = 1) {
        if let existingIndex = _items.firstIndex(where: { $0.productId == product.id }) {
            _items[existingIndex].quantity += quantity
        } else {
            let cartItem = CartItem(product: product, quantity: quantity)
            _items.append(cartItem)
        }
    }
}
```

#### Combine Approach
```swift
class ProductViewModel: ObservableObject {
    @Published var products: [Product] = []
    @Published var featuredProducts: [Product] = []
    @Published var onSaleProducts: [Product] = []
    @Published var isLoading = false
    @Published var errorMessage: String?
    
    private var cancellables = Set<AnyCancellable>()
    
    init() {
        setupBindings()
    }
    
    private func setupBindings() {
        $products
            .map { products in
                products.filter { $0.isFeatured }
            }
            .assign(to: \.featuredProducts, on: self)
            .store(in: &cancellables)
        
        $products
            .map { products in
                products.filter { $0.isOnSale }
            }
            .assign(to: \.onSaleProducts, on: self)
            .store(in: &cancellables)
    }
    
    func loadProducts() {
        isLoading = true
        
        ProductAPI.fetchProducts()
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { [weak self] completion in
                    self?.isLoading = false
                    if case .failure(let error) = completion {
                        self?.errorMessage = error.localizedDescription
                    }
                },
                receiveValue: { [weak self] products in
                    self?.products = products
                }
            )
            .store(in: &cancellables)
    }
}
```

### Chat Application

#### Swift Observation Approach
```swift
@Observable
class ChatViewModel {
    var messages: [Message] = []
    var isConnected = false
    var connectionStatus = "Disconnected"
    
    private let webSocketManager: WebSocketManager
    
    init(webSocketManager: WebSocketManager) {
        self.webSocketManager = webSocketManager
        setupObservation()
    }
    
    private func setupObservation() {
        withObservationTracking {
            self.connectionStatus = self.isConnected ? "Connected" : "Disconnected"
        }
    }
    
    func sendMessage(_ text: String) {
        let message = Message(text: text, isFromMe: true, timestamp: Date())
        messages.append(message)
        webSocketManager.send(message: message)
    }
}
```

#### Combine Approach
```swift
class ChatViewModel: ObservableObject {
    @Published var messages: [Message] = []
    @Published var isConnected = false
    @Published var connectionStatus = "Disconnected"
    
    private let webSocketManager: WebSocketManager
    private var cancellables = Set<AnyCancellable>()
    
    init(webSocketManager: WebSocketManager) {
        self.webSocketManager = webSocketManager
        setupBindings()
    }
    
    private func setupBindings() {
        webSocketManager.connectionStatusPublisher
            .receive(on: DispatchQueue.main)
            .assign(to: \.isConnected, on: self)
            .store(in: &cancellables)
        
        webSocketManager.connectionStatusPublisher
            .receive(on: DispatchQueue.main)
            .map { $0 ? "Connected" : "Disconnected" }
            .assign(to: \.connectionStatus, on: self)
            .store(in: &cancellables)
        
        webSocketManager.messagePublisher
            .receive(on: DispatchQueue.main)
            .sink { [weak self] message in
                self?.messages.append(message)
            }
            .store(in: &cancellables)
    }
}
```

---

## Conclusion

### Key Takeaways

1. **Swift Observation** is ideal for:
   - Simple state management
   - Direct UI binding
   - Performance-critical applications
   - iOS 17+ projects

2. **Combine** is ideal for:
   - Complex data streams
   - Asynchronous operations
   - Event-driven architectures
   - Cross-platform compatibility

3. **Choose based on complexity**:
   - Use Swift Observation for simple state
   - Use Combine for complex streams
   - Consider hybrid approaches for complex apps

4. **Performance considerations**:
   - Swift Observation has lower overhead
   - Combine provides more flexibility
   - Both can be used together effectively

### Future Considerations

- **Swift Observation** will likely become the standard for simple state management
- **Combine** will remain essential for complex reactive programming
- **Hybrid approaches** will become common in large applications
- **Performance optimization** will drive framework choice

Both frameworks are powerful tools in the Swift ecosystem, and understanding when to use each will help you build better, more performant applications.
