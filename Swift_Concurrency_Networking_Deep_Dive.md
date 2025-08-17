# Swift + iOS Concurrency & Networking Deep Dive
## Complete Guide to Modern iOS Concurrency, Networking, and Race Condition Prevention

---

## Table of Contents
1. [Swift Concurrency Fundamentals](#swift-concurrency-fundamentals)
2. [Advanced Concurrency Patterns](#advanced-concurrency-patterns)
3. [Queues and Dispatch Systems](#queues-and-dispatch-systems)
4. [Async/Await and Structured Concurrency](#asyncawait-and-structured-concurrency)
5. [Networking in Depth](#networking-in-depth)
6. [Advanced Networking Features](#advanced-networking-features)
7. [Race Conditions and Prevention](#race-conditions-and-prevention)
8. [Advanced Race Condition Scenarios](#advanced-race-condition-scenarios)
9. [Real-World Examples and Use Cases](#real-world-examples-and-use-cases)
10. [Production-Ready Patterns](#production-ready-patterns)
11. [Best Practices and Performance](#best-practices-and-performance)
12. [Testing & Quality Assurance](#testing--quality-assurance)

---

## Swift Concurrency Fundamentals

### What is Concurrency?

**Concurrency** is the ability to execute multiple tasks simultaneously or in an interleaved manner, improving performance and responsiveness in iOS apps. It's not the same as parallelism - concurrency is about handling multiple tasks that can be interleaved, while parallelism is about executing multiple tasks simultaneously on different CPU cores.

**Key Differences:**
- **Concurrency**: Multiple tasks making progress over time (can be interleaved on single core)
- **Parallelism**: Multiple tasks executing simultaneously on different cores
- **Asynchronous**: Tasks that don't block the current thread while waiting

**Why Concurrency Matters in iOS:**
- **UI Responsiveness**: Keep UI thread free for smooth animations and user interactions
- **Performance**: Utilize multiple CPU cores efficiently for better throughput
- **User Experience**: Handle multiple operations without blocking the user interface
- **Battery Life**: Efficient resource management and background processing
- **Network Operations**: Handle multiple network requests simultaneously
- **File I/O**: Process files without freezing the user interface

### Swift Concurrency Approaches

Swift provides multiple approaches to handle concurrency, each with specific use cases and benefits:

#### 1. Grand Central Dispatch (GCD)
**What it is:** Low-level C-based API that provides a simple interface for executing code concurrently on multicore hardware.

**Core Concepts:**
- **Queues**: Abstract representations of threads
- **Tasks**: Units of work (closures) that can be executed
- **Quality of Service**: Priority levels for different types of work
- **Synchronous vs Asynchronous**: Blocking vs non-blocking execution

**When to use:**
- Low-level concurrency control
- Simple background operations
- Performance-critical code
- Legacy code compatibility

**Example Use Cases:**
```swift
// Simple background processing
DispatchQueue.global(qos: .userInitiated).async {
    // Process images in background
    let processedImages = self.processImages(images)
    
    // Update UI on main thread
    DispatchQueue.main.async {
        self.updateImageView(with: processedImages)
    }
}

// Serial queue for critical operations
let criticalQueue = DispatchQueue(label: "critical.operations")
criticalQueue.async {
    // Database write operations
    self.saveToDatabase(data)
}
```

#### 2. Operation and OperationQueue
**What it is:** High-level, object-oriented approach built on top of GCD that provides additional features like dependencies, priorities, and cancellation.

**Core Concepts:**
- **Operations**: Encapsulated units of work with state management
- **Operation Queues**: Managed queues that execute operations
- **Dependencies**: Operations can depend on other operations
- **Priorities**: Different priority levels for operations
- **Cancellation**: Ability to cancel operations before completion

**When to use:**
- Complex workflows with dependencies
- Operations that need to be cancelled
- Operations with different priorities
- Operations that need to be paused/resumed

**Example Use Cases:**
```swift
// Create operations with dependencies
let downloadOperation = DownloadOperation(url: imageURL)
let processOperation = ProcessImageOperation()
let saveOperation = SaveToDatabaseOperation()

// Set up dependencies
processOperation.addDependency(downloadOperation)
saveOperation.addDependency(processOperation)

// Add to queue
let queue = OperationQueue()
queue.addOperations([downloadOperation, processOperation, saveOperation], waitUntilFinished: false)

// Cancel operations if needed
downloadOperation.cancel()
```

#### 3. Async/Await (Modern Swift Concurrency)
**What it is:** Modern Swift concurrency system (iOS 13+) that provides structured, readable asynchronous code without callback hell.

**Core Concepts:**
- **async functions**: Functions that can be suspended and resumed
- **await keyword**: Points where async functions can be suspended
- **Structured concurrency**: Hierarchical organization of concurrent tasks
- **Task isolation**: Automatic memory management and thread safety
- **Error handling**: Natural error propagation without completion handlers

**When to use:**
- New code targeting iOS 13+
- Complex asynchronous workflows
- Code that needs to be readable and maintainable
- Error handling scenarios

**Example Use Cases:**
```swift
// Simple async function
func fetchUserData() async throws -> User {
    let url = URL(string: "https://api.example.com/user")!
    let (data, response) = try await URLSession.shared.data(from: url)
    
    guard let httpResponse = response as? HTTPURLResponse,
          httpResponse.statusCode == 200 else {
        throw NetworkError.invalidResponse
    }
    
    return try JSONDecoder().decode(User.self, from: data)
}

// Usage with error handling
Task {
    do {
        let user = try await fetchUserData()
        await MainActor.run {
            self.user = user
            self.updateUI()
        }
    } catch {
        await MainActor.run {
            self.showError(error)
        }
    }
}
```

#### 4. Combine Framework
**What it is:** Reactive programming framework that provides a declarative way to handle asynchronous events and data streams.

**Core Concepts:**
- **Publishers**: Sources of values over time
- **Subscribers**: Consumers of published values
- **Operators**: Transformations and combinations of data streams
- **Schedulers**: Control over which thread operations run on
- **Cancellation**: Automatic cleanup of subscriptions

**When to use:**
- Reactive UI updates
- Data binding scenarios
- Complex event handling
- Integration with SwiftUI

**Example Use Cases:**
```swift
// Data binding with Combine
class UserViewModel: ObservableObject {
    @Published var user: User?
    @Published var isLoading = false
    
    private var cancellables = Set<AnyCancellable>()
    
    func fetchUser() {
        isLoading = true
        
        URLSession.shared.dataTaskPublisher(for: userURL)
            .map(\.data)
            .decode(type: User.self, decoder: JSONDecoder())
            .receive(on: DispatchQueue.main)
            .sink(
                receiveCompletion: { completion in
                    self.isLoading = false
                    if case .failure(let error) = completion {
                        print("Error: \(error)")
                    }
                },
                receiveValue: { user in
                    self.user = user
                }
            )
            .store(in: &cancellables)
    }
}
```

### Concurrency Models in iOS

#### 1. Thread-Based Concurrency
**What it is:** Traditional approach where you manually create and manage threads.

**Characteristics:**
- **Explicit thread management**: You control thread creation and lifecycle
- **Shared memory**: Threads share the same memory space
- **Complexity**: Requires careful synchronization to prevent race conditions
- **Performance**: Can be efficient but error-prone

**Example:**
```swift
// Manual thread creation (not recommended in modern iOS)
class ThreadBasedManager {
    private var threads: [Thread] = []
    
    func startWork() {
        let thread = Thread {
            // Work on background thread
            self.performWork()
        }
        thread.start()
        threads.append(thread)
    }
    
    private func performWork() {
        // Perform work on background thread
        // Must be careful about accessing shared resources
    }
}
```

#### 2. Queue-Based Concurrency
**What it is:** GCD approach where you submit work to queues rather than managing threads directly.

**Characteristics:**
- **Implicit thread management**: System manages threads for you
- **Work units**: Submit closures or blocks of work
- **Simpler**: Less error-prone than manual thread management
- **Efficient**: System optimizes thread usage

**Example:**
```swift
// Queue-based approach (recommended)
class QueueBasedManager {
    private let workQueue = DispatchQueue(label: "work.queue", attributes: .concurrent)
    
    func startWork() {
        workQueue.async {
            // Work automatically runs on appropriate thread
            self.performWork()
        }
    }
    
    private func performWork() {
        // Work is automatically scheduled on appropriate thread
    }
}
```

#### 3. Actor-Based Concurrency
**What it is:** Modern Swift concurrency approach where actors provide automatic isolation of mutable state.

**Characteristics:**
- **Automatic isolation**: Mutable state is automatically protected
- **No manual synchronization**: System handles thread safety
- **Performance**: Efficient concurrent access without locks
- **Memory safety**: Automatic memory management

**Example:**
```swift
// Actor-based approach (iOS 15+)
actor UserManager {
    private var users: [User] = []
    private var isLoading = false
    
    func addUser(_ user: User) {
        users.append(user)
    }
    
    func getUsers() -> [User] {
        return users
    }
    
    func setLoading(_ loading: Bool) {
        isLoading = loading
    }
}

// Usage
let userManager = UserManager()
Task {
    await userManager.addUser(User(name: "John"))
    let users = await userManager.getUsers()
}
```

### Concurrency Challenges and Solutions

#### 1. Race Conditions
**What they are:** Situations where the outcome depends on the timing of thread execution.

**Example:**
```swift
// ❌ DANGEROUS - Race condition
class UnsafeCounter {
    private var count = 0
    
    func increment() {
        count += 1 // Not atomic!
    }
    
    func getCount() -> Int {
        return count
    }
}

// Multiple threads calling increment() simultaneously can cause:
// Thread 1: reads count (0)
// Thread 2: reads count (0)
// Thread 1: adds 1, writes 1
// Thread 2: adds 1, writes 1
// Result: count = 1 (should be 2!)
```

**Solutions:**
```swift
// ✅ SAFE - Serial queue
class SafeCounter {
    private var count = 0
    private let queue = DispatchQueue(label: "counter.queue", attributes: .serial)
    
    func increment() {
        queue.sync {
            count += 1
        }
    }
    
    func getCount() -> Int {
        return queue.sync {
            return count
        }
    }
}

// ✅ SAFE - Actor (iOS 15+)
actor ActorCounter {
    private var count = 0
    
    func increment() {
        count += 1
    }
    
    func getCount() -> Int {
        return count
    }
}
```

#### 2. Deadlocks
**What they are:** Situations where two or more threads are waiting for each other to release resources.

**Example:**
```swift
// ❌ DANGEROUS - Deadlock
class DeadlockExample {
    private let lock1 = NSLock()
    private let lock2 = NSLock()
    
    func method1() {
        lock1.lock()
        // Simulate some work
        Thread.sleep(forTimeInterval: 0.1)
        
        lock2.lock() // Waiting for lock2
        // Critical section
        lock2.unlock()
        lock1.unlock()
    }
    
    func method2() {
        lock2.lock()
        // Simulate some work
        Thread.sleep(forTimeInterval: 0.1)
        
        lock1.lock() // Waiting for lock1
        // Critical section
        lock1.unlock()
        lock2.unlock()
    }
}

// If method1 and method2 are called simultaneously:
// method1 acquires lock1, waits for lock2
// method2 acquires lock2, waits for lock1
// Both are waiting forever - DEADLOCK!
```

**Solutions:**
```swift
// ✅ SAFE - Consistent lock ordering
class SafeLockExample {
    private let lock1 = NSLock()
    private let lock2 = NSLock()
    
    func method1() {
        // Always acquire locks in the same order
        lock1.lock()
        defer { lock1.unlock() }
        
        lock2.lock()
        defer { lock2.unlock() }
        
        // Critical section
    }
    
    func method2() {
        // Always acquire locks in the same order
        lock1.lock()
        defer { lock1.unlock() }
        
        lock2.lock()
        defer { lock2.unlock() }
        
        // Critical section
    }
}
```

#### 3. Memory Management
**What it is:** Ensuring proper cleanup of resources in concurrent environments.

**Challenges:**
- **Retain cycles**: Objects holding strong references to each other
- **Memory leaks**: Objects not being deallocated properly
- **Resource cleanup**: Ensuring resources are released on the correct thread

**Solutions:**
```swift
// ❌ DANGEROUS - Retain cycle
class RetainCycleExample {
    var completionHandler: (() -> Void)?
    
    func setupHandler() {
        completionHandler = {
            self.doSomething() // Strong reference to self
        }
    }
}

// ✅ SAFE - Weak reference
class SafeReferenceExample {
    var completionHandler: (() -> Void)?
    
    func setupHandler() {
        completionHandler = { [weak self] in
            guard let self = self else { return }
            self.doSomething() // Weak reference prevents retain cycle
        }
    }
}

// ✅ SAFE - Unowned reference (when you're certain object won't be deallocated)
class UnownedReferenceExample {
    var completionHandler: (() -> Void)?
    
    func setupHandler() {
        completionHandler = { [unowned self] in
            self.doSomething() // Unowned reference
        }
    }
}
```

### Performance Considerations

#### 1. Thread Creation Overhead
**What it is:** Creating threads has overhead, so creating too many threads can hurt performance.

**Best Practices:**
- Use GCD queues instead of manually creating threads
- Let the system manage thread pools
- Use appropriate QoS levels for different types of work

**Example:**
```swift
// ❌ BAD - Creating too many threads
class BadThreadManager {
    func processItems(_ items: [String]) {
        for item in items {
            Thread {
                self.processItem(item)
            }.start() // Creates a new thread for each item
        }
    }
}

// ✅ GOOD - Using GCD queues
class GoodQueueManager {
    private let queue = DispatchQueue(label: "processing.queue", attributes: .concurrent)
    
    func processItems(_ items: [String]) {
        for item in items {
            queue.async {
                self.processItem(item)
            }
        }
    }
}
```

#### 2. Context Switching
**What it is:** The overhead of switching between threads.

**Minimizing Impact:**
- Group related work on the same thread when possible
- Use serial queues for related operations
- Avoid excessive thread hopping

**Example:**
```swift
// ❌ BAD - Excessive context switching
class BadContextSwitching {
    func processData(_ data: [String]) {
        for item in data {
            DispatchQueue.global().async {
                let processed = self.processItem(item)
                DispatchQueue.main.async {
                    self.updateUI(with: processed)
                }
            }
        }
    }
}

// ✅ GOOD - Minimizing context switching
class GoodContextSwitching {
    func processData(_ data: [String]) {
        DispatchQueue.global().async {
            let processedItems = data.map { self.processItem($0) }
            
            DispatchQueue.main.async {
                self.updateUI(with: processedItems)
            }
        }
    }
}
```

#### 3. Memory Pressure
**What it is:** When the system is running low on memory.

**Handling:**
- Respond to memory warnings
- Clean up caches when memory pressure is high
- Use autorelease pools for temporary objects

**Example:**
```swift
class MemoryPressureHandler {
    private let cache = NSCache<NSString, UIImage>()
    
    init() {
        // Configure cache to respond to memory pressure
        cache.countLimit = 100
        cache.totalCostLimit = 50 * 1024 * 1024 // 50MB
        
        // Listen for memory warnings
        NotificationCenter.default.addObserver(
            self,
            selector: #selector(handleMemoryWarning),
            name: UIApplication.didReceiveMemoryWarningNotification,
            object: nil
        )
    }
    
    @objc private func handleMemoryWarning() {
        // Clear cache when memory pressure is high
        cache.removeAllObjects()
    }
}
```

### Real-World Concurrency Scenarios

#### 1. Image Processing Pipeline
**What it is:** Processing multiple images concurrently without blocking the UI.

**Implementation:**
```swift
class ImageProcessingPipeline {
    private let processingQueue = DispatchQueue(label: "image.processing", attributes: .concurrent)
    private let resultQueue = DispatchQueue(label: "results", attributes: .serial)
    
    func processImages(_ images: [UIImage]) async -> [ProcessedImage] {
        return await withThrowingTaskGroup(of: ProcessedImage.self) { group in
            for image in images {
                group.addTask {
                    return try await self.processImage(image)
                }
            }
            
            var results: [ProcessedImage] = []
            for try await result in group {
                results.append(result)
            }
            return results
        }
    }
    
    private func processImage(_ image: UIImage) async throws -> ProcessedImage {
        // Simulate image processing
        try await Task.sleep(nanoseconds: 100_000_000) // 0.1 seconds
        
        // Apply filters, resize, etc.
        let processedImage = image // Simplified for example
        
        return ProcessedImage(original: image, processed: processedImage)
    }
}

struct ProcessedImage {
    let original: UIImage
    let processed: UIImage
}
```

#### 2. Network Request Manager
**What it is:** Managing multiple network requests concurrently with proper error handling.

**Implementation:**
```swift
class NetworkRequestManager: ObservableObject {
    @Published var isLoading = false
    @Published var error: Error?
    
    private let session = URLSession.shared
    private var activeTasks: [URLSessionTask] = []
    
    func performMultipleRequests<T: Decodable>(_ requests: [URLRequest]) async throws -> [T] {
        await MainActor.run {
            self.isLoading = true
            self.error = nil
        }
        
        defer {
            Task { @MainActor in
                self.isLoading = false
            }
        }
        
        return try await withThrowingTaskGroup(of: T.self) { group in
            for request in requests {
                group.addTask {
                    return try await self.performRequest(request)
                }
            }
            
            var results: [T] = []
            for try await result in group {
                results.append(result)
            }
            return results
        }
    }
    
    private func performRequest<T: Decodable>(_ request: URLRequest) async throws -> T {
        let (data, response) = try await session.data(for: request)
        
        guard let httpResponse = response as? HTTPURLResponse,
              (200...299).contains(httpResponse.statusCode) else {
            throw NetworkError.httpError(statusCode: (response as? HTTPURLResponse)?.statusCode ?? 0)
        }
        
        return try JSONDecoder().decode(T.self, from: data)
    }
}

enum NetworkError: Error {
    case httpError(statusCode: Int)
    case invalidResponse
    case decodingError
}
```

#### 3. Database Operations
**What it is:** Performing database operations concurrently while maintaining data integrity.

**Implementation:**
```swift
class DatabaseManager {
    private let queue = DispatchQueue(label: "database.queue", attributes: .serial)
    private var database: Database?
    
    func performBatchOperations(_ operations: [DatabaseOperation]) async throws {
        try await withThrowingTaskGroup(of: Void.self) { group in
            for operation in operations {
                group.addTask {
                    try await self.performOperation(operation)
                }
            }
            
            try await group.waitForAll()
        }
    }
    
    private func performOperation(_ operation: DatabaseOperation) async throws {
        try await withCheckedThrowingContinuation { continuation in
            queue.async {
                do {
                    switch operation {
                    case .insert(let data):
                        try self.database?.insert(data)
                    case .update(let id, let data):
                        try self.database?.update(id: id, data: data)
                    case .delete(let id):
                        try self.database?.delete(id: id)
                    }
                    continuation.resume()
                } catch {
                    continuation.resume(throwing: error)
                }
            }
        }
    }
}

enum DatabaseOperation {
    case insert(Data)
    case update(id: String, data: Data)
    case delete(id: String)
}
```

### Summary of Concurrency Fundamentals

**Key Takeaways:**

1. **Concurrency vs Parallelism**: Understand the difference and when each is appropriate
2. **Multiple Approaches**: Swift provides different tools for different scenarios
3. **Thread Safety**: Always consider how concurrent access affects shared resources
4. **Performance**: Choose the right approach for your specific use case
5. **Memory Management**: Be careful about retain cycles and resource cleanup
6. **Error Handling**: Proper error handling is crucial in concurrent code
7. **Testing**: Concurrent code requires special testing approaches

**When to Use Each Approach:**

- **GCD**: Simple background operations, performance-critical code
- **Operation Queues**: Complex workflows with dependencies
- **Async/Await**: New code, readable asynchronous workflows
- **Combine**: Reactive programming, SwiftUI integration
- **Actors**: Complex state management, iOS 15+

**Common Pitfalls to Avoid:**

- Using `DispatchQueue.main.sync` (can cause deadlocks)
- Not handling retain cycles in closures
- Accessing shared resources without synchronization
- Creating too many threads manually
- Not responding to memory pressure

**Best Practices:**

- Always use MainActor for UI updates
- Choose appropriate QoS levels for background work
- Use serial queues for critical sections
- Implement proper error handling and recovery
- Test concurrent code thoroughly
- Monitor performance and memory usage

This foundation provides the essential knowledge needed to understand and implement concurrency in iOS apps effectively, safely, and performantly.

---

## Advanced Concurrency Patterns

### Memory Management & ARC in Concurrency

**What it is:** Advanced memory management techniques specifically designed for concurrent environments where multiple threads may access objects simultaneously.

**Core Concepts:**
- **ARC (Automatic Reference Counting)**: Swift's memory management system that automatically tracks object references
- **Reference Cycles**: Situations where objects hold strong references to each other, preventing deallocation
- **Weak References**: References that don't prevent objects from being deallocated
- **Unowned References**: References that assume the object won't be deallocated

**Why it matters in concurrency:**
- Multiple threads can create complex reference patterns
- Background operations may outlive UI objects
- Improper memory management can cause memory leaks
- Memory leaks in concurrent code can be harder to detect

#### Strong/Weak Reference Cycles in Async Contexts
```swift
// ❌ DANGEROUS - Retain cycle with strong self
class DataManager {
    private var data: [String] = []
    
    func fetchData() {
        DispatchQueue.global().async {
            // Strong reference to self creates retain cycle
            self.data = ["item1", "item2", "item3"]
            DispatchQueue.main.async {
                // Another strong reference
                self.updateUI()
            }
        }
    }
    
    func updateUI() {
        // UI update logic
    }
}

// ✅ SAFE - Weak reference to prevent retain cycles
class SafeDataManager {
    private var data: [String] = []
    
    func fetchData() {
        DispatchQueue.global().async { [weak self] in
            guard let self = self else { return }
            
            self.data = ["item1", "item2", "item3"]
            
            DispatchQueue.main.async { [weak self] in
                guard let self = self else { return }
                self.updateUI()
            }
        }
    }
    
    func updateUI() {
        // UI update logic
    }
}

// ✅ SAFE - Unowned reference when you're certain object won't be deallocated
class UnownedDataManager {
    private var data: [String] = []
    
    func fetchData() {
        DispatchQueue.global().async { [unowned self] in
            self.data = ["item1", "item2", "item3"]
            
            DispatchQueue.main.async { [unowned self] in
                self.updateUI()
            }
        }
    }
    
    func updateUI() {
        // UI update logic
    }
}
```

#### Memory Leaks in Dispatch Queues
```swift
// ❌ DANGEROUS - Queue retains objects indefinitely
class LeakyManager {
    private let queue = DispatchQueue(label: "leaky.queue")
    private var handlers: [() -> Void] = []
    
    func addHandler(_ handler: @escaping () -> Void) {
        queue.async {
            // Queue retains the handler closure
            self.handlers.append(handler)
        }
    }
}

// ✅ SAFE - Proper cleanup and weak references
class SafeManager {
    private let queue = DispatchQueue(label: "safe.queue")
    private var handlers: [WeakHandler] = []
    
    func addHandler(_ handler: @escaping () -> Void) {
        queue.async {
            // Use weak wrapper to prevent retain cycles
            let weakHandler = WeakHandler(handler: handler)
            self.handlers.append(weakHandler)
            
            // Clean up deallocated handlers
            self.handlers = self.handlers.filter { $0.handler != nil }
        }
    }
}

// Weak wrapper for handlers
class WeakHandler {
    weak var handler: (() -> Void)?
    
    init(handler: @escaping () -> Void) {
        self.handler = handler
    }
}
```

#### Automatic Memory Management with Actors
```swift
actor MemorySafeManager {
    private var data: [String: Any] = [:]
    private var observers: [String: () -> Void] = [:]
    
    func setData(_ value: Any, for key: String) {
        data[key] = value
        notifyObservers(for: key)
    }
    
    func addObserver(_ observer: @escaping () -> Void, for key: String) {
        observers[key] = observer
    }
    
    private func notifyObservers(for key: String) {
        observers[key]?()
    }
    
    // Actors automatically handle memory management
    // No need for weak references or manual cleanup
}
```

### Advanced GCD Features

**What it is:** Advanced Grand Central Dispatch features that provide sophisticated control over concurrent operations, synchronization, and system-level event handling.

**Core Concepts:**
- **Dispatch Barriers**: Special flags that make concurrent queues behave like serial queues for specific operations
- **Dispatch Sources**: System-level event monitoring for file changes, timers, and memory pressure
- **Complex Dependencies**: Managing workflows where some tasks depend on others
- **Queue Hierarchies**: Creating queues with specific priorities and relationships

**Why it matters:**
- Provides fine-grained control over concurrent operations
- Enables efficient read-write synchronization
- Allows monitoring of system events
- Supports complex workflow management

#### Dispatch Barriers for Read-Write Synchronization
```swift
class ThreadSafeCache {
    private let queue = DispatchQueue(label: "cache.queue", attributes: .concurrent)
    private var cache: [String: Any] = [:]
    
    // Multiple readers can access simultaneously
    func getValue(for key: String) -> Any? {
        return queue.sync {
            return cache[key]
        }
    }
    
    // Writer blocks all other operations (readers and writers)
    func setValue(_ value: Any, for key: String) {
        queue.async(flags: .barrier) {
            self.cache[key] = value
        }
    }
    
    // Multiple readers can run concurrently
    func getAllKeys() -> [String] {
        return queue.sync {
            return Array(cache.keys)
        }
    }
}

// Real-world usage: Image cache with concurrent reads, exclusive writes
class ImageCache {
    private let queue = DispatchQueue(label: "image.cache", attributes: .concurrent)
    private var images: [String: UIImage] = [:]
    private let maxCacheSize = 100
    
    func getImage(for key: String) -> UIImage? {
        return queue.sync {
            return images[key]
        }
    }
    
    func setImage(_ image: UIImage, for key: String) {
        queue.async(flags: .barrier) {
            if self.images.count >= self.maxCacheSize {
                // Remove oldest entries
                let oldestKeys = Array(self.images.keys.prefix(self.images.count - self.maxCacheSize + 1))
                for key in oldestKeys {
                    self.images.removeValue(forKey: key)
                }
            }
            self.images[key] = image
        }
    }
}
```

#### Dispatch Sources for System Events
```swift
class SystemEventMonitor {
    private var sources: [DispatchSource] = []
    
    // File system monitoring
    func monitorFile(at path: String, handler: @escaping () -> Void) {
        let fileDescriptor = open(path, O_EVTONLY)
        guard fileDescriptor >= 0 else { return }
        
        let source = DispatchSource.makeFileSystemObjectSource(
            fileDescriptor: fileDescriptor,
            eventMask: .write,
            queue: DispatchQueue.global()
        )
        
        source.setEventHandler {
            handler()
        }
        
        source.setCancelHandler {
            close(fileDescriptor)
        }
        
        source.resume()
        sources.append(source)
    }
    
    // Timer source
    func createTimer(interval: TimeInterval, handler: @escaping () -> Void) {
        let source = DispatchSource.makeTimerSource(queue: DispatchQueue.global())
        
        source.schedule(deadline: .now(), repeating: interval)
        source.setEventHandler {
            handler()
        }
        
        source.resume()
        sources.append(source)
    }
    
    // Memory pressure monitoring
    func monitorMemoryPressure(handler: @escaping () -> Void) {
        let source = DispatchSource.makeMemoryPressureSource(
            eventMask: [.warning, .critical],
            queue: DispatchQueue.global()
        )
        
        source.setEventHandler {
            handler()
        }
        
        source.resume()
        sources.append(source)
    }
    
    func cleanup() {
        sources.forEach { $0.cancel() }
        sources.removeAll()
    }
}
```

#### Dispatch Groups with Complex Dependencies
```swift
class ComplexTaskManager {
    private let group = DispatchGroup()
    private let queue = DispatchQueue(label: "complex.tasks", attributes: .concurrent)
    
    func performComplexWorkflow() {
        // Phase 1: Data preparation (can run in parallel)
        let dataTasks = ["task1", "task2", "task3"]
        
        for task in dataTasks {
            group.enter()
            queue.async {
                self.performDataTask(task)
                self.group.leave()
            }
        }
        
        // Phase 2: Wait for all data tasks, then start processing
        group.notify(queue: .main) {
            self.startProcessingPhase()
        }
    }
    
    func performDataTask(_ task: String) {
        // Simulate work
        Thread.sleep(forTimeInterval: 1.0)
        print("Completed data task: \(task)")
    }
    
    func startProcessingPhase() {
        print("All data tasks completed, starting processing phase")
        
        // Phase 3: Sequential processing tasks
        let processingTasks = ["process1", "process2", "process3"]
        
        for task in processingTasks {
            group.enter()
            queue.async {
                self.performProcessingTask(task)
                self.group.leave()
            }
        }
        
        // Final completion
        group.notify(queue: .main) {
            self.workflowCompleted()
        }
    }
    
    func performProcessingTask(_ task: String) {
        Thread.sleep(forTimeInterval: 0.5)
        print("Completed processing task: \(task)")
    }
    
    func workflowCompleted() {
        print("All phases completed successfully!")
    }
}
```

#### Custom Dispatch Queue Hierarchies
```swift
class QueueHierarchyManager {
    // High priority queue for critical operations
    private let criticalQueue = DispatchQueue(
        label: "critical.queue",
        qos: .userInitiated,
        attributes: .serial
    )
    
    // Medium priority queue for normal operations
    private let normalQueue = DispatchQueue(
        label: "normal.queue",
        qos: .default,
        attributes: .concurrent
    )
    
    // Low priority queue for background tasks
    private let backgroundQueue = DispatchQueue(
        label: "background.queue",
        qos: .utility,
        attributes: .concurrent
    )
    
    // Queue target hierarchy for dependency management
    private let dependencyQueue: DispatchQueue
    
    init() {
        // Create a dependency queue that targets the critical queue
        dependencyQueue = DispatchQueue(
            label: "dependency.queue",
            qos: .userInitiated,
            attributes: .serial,
            target: criticalQueue
        )
    }
    
    func performCriticalOperation(_ operation: @escaping () -> Void) {
        criticalQueue.async {
            operation()
        }
    }
    
    func performNormalOperation(_ operation: @escaping () -> Void) {
        normalQueue.async {
            operation()
        }
    }
    
    func performBackgroundOperation(_ operation: @escaping () -> Void) {
        backgroundQueue.async {
            operation()
        }
    }
    
    func performDependentOperation(_ operation: @escaping () -> Void) {
        // This will respect the critical queue's priority
        dependencyQueue.async {
            operation()
        }
    }
}
```

### Performance Profiling & Debugging

**What it is:** Comprehensive tools and techniques for analyzing, monitoring, and optimizing concurrent code performance in iOS applications.

**Core Concepts:**
- **Performance Profiling**: Measuring execution time, memory usage, and CPU utilization
- **Thread State Inspection**: Understanding what's happening in each thread
- **Bottleneck Detection**: Identifying performance issues in concurrent code
- **Memory Pressure Handling**: Responding to system memory warnings

**Why it matters:**
- Concurrent code can have subtle performance issues
- Memory leaks are harder to detect in concurrent environments
- Performance bottlenecks can affect user experience
- System resources must be managed efficiently

#### Instruments for Concurrency Analysis
```swift
class PerformanceProfiler {
    private let metrics = PerformanceMetrics()
    
    func profileOperation<T>(_ operation: @escaping () -> T, name: String) -> T {
        let startTime = CFAbsoluteTimeGetCurrent()
        let startMemory = getMemoryUsage()
        
        let result = operation()
        
        let endTime = CFAbsoluteTimeGetCurrent()
        let endMemory = getMemoryUsage()
        
        let duration = endTime - startTime
        let memoryDelta = endMemory - startMemory
        
        metrics.recordOperation(name: name, duration: duration, memoryDelta: memoryDelta)
        
        return result
    }
    
    func profileAsyncOperation<T>(_ operation: @escaping () async -> T, name: String) async -> T {
        let startTime = CFAbsoluteTimeGetCurrent()
        let startMemory = getMemoryUsage()
        
        let result = await operation()
        
        let endTime = CFAbsoluteTimeGetCurrent()
        let endMemory = getMemoryUsage()
        
        let duration = endTime - startTime
        let memoryDelta = endMemory - startMemory
        
        metrics.recordOperation(name: name, duration: duration, memoryDelta: memoryDelta)
        
        return result
    }
    
    private func getMemoryUsage() -> UInt64 {
        var info = mach_task_basic_info()
        var count = mach_msg_type_number_t(MemoryLayout<mach_task_basic_info>.size)/4
        
        let kerr: kern_return_t = withUnsafeMutablePointer(to: &info) {
            $0.withMemoryRebound(to: integer_t.self, capacity: 1) {
                task_info(mach_task_self_,
                         task_flavor_t(MACH_TASK_BASIC_INFO),
                         $0,
                         &count)
            }
        }
        
        return kerr == KERN_SUCCESS ? info.resident_size : 0
    }
}

class PerformanceMetrics {
    private var operations: [String: [Double]] = [:]
    private let queue = DispatchQueue(label: "metrics.queue", attributes: .concurrent)
    
    func recordOperation(name: String, duration: Double, memoryDelta: Double) {
        queue.async(flags: .barrier) {
            if self.operations[name] == nil {
                self.operations[name] = []
            }
            self.operations[name]?.append(duration)
        }
    }
    
    func getAverageDuration(for operation: String) -> Double? {
        return queue.sync {
            guard let durations = operations[operation] else { return nil }
            return durations.reduce(0, +) / Double(durations.count)
        }
    }
    
    func getPerformanceReport() -> String {
        return queue.sync {
            var report = "Performance Report:\n"
            for (name, durations) in operations {
                let average = durations.reduce(0, +) / Double(durations.count)
                let min = durations.min() ?? 0
                let max = durations.max() ?? 0
                report += "\(name): avg=\(String(format: "%.4f", average))s, min=\(String(format: "%.4f", min))s, max=\(String(format: "%.4f", max))s\n"
            }
            return report
        }
    }
}
```

#### Thread State Inspection
```swift
class ThreadInspector {
    static func getCurrentThreadInfo() -> String {
        let thread = Thread.current
        let queue = DispatchQueue.current?.label ?? "Unknown"
        let isMainThread = Thread.isMainThread
        let threadName = thread.name ?? "Unnamed"
        let threadQuality = thread.qualityOfService.rawValue
        
        return """
        Thread: \(threadName)
        Queue: \(queue)
        Is Main: \(isMainThread)
        QoS: \(threadQuality)
        """
    }
    
    static func getAllThreadsInfo() -> [String] {
        var threadList: thread_act_array_t?
        var threadCount: mach_msg_type_number_t = 0
        
        let result = task_threads(mach_task_self_, &threadList, &threadCount)
        
        guard result == KERN_SUCCESS, let threads = threadList else {
            return ["Failed to get thread info"]
        }
        
        var threadInfos: [String] = []
        
        for i in 0..<Int(threadCount) {
            let thread = threads[Int(i)]
            let info = getThreadBasicInfo(thread)
            threadInfos.append(info)
        }
        
        vm_deallocate(mach_task_self_, 
                     vm_address_t(UInt(bitPattern: threads)), 
                     vm_size_t(threadCount * MemoryLayout<thread_t>.size))
        
        return threadInfos
    }
    
    private static func getThreadBasicInfo(_ thread: thread_t) -> String {
        var basicInfo = thread_basic_info()
        var count = mach_msg_type_number_t(MemoryLayout<thread_basic_info>.size)/4
        
        let result = withUnsafeMutablePointer(to: &basicInfo) {
            $0.withMemoryRebound(to: integer_t.self, capacity: 1) {
                thread_info(thread,
                           thread_flavor_t(THREAD_BASIC_INFO),
                           $0,
                           &count)
            }
        }
        
        if result == KERN_SUCCESS {
            return "Thread \(thread): CPU=\(basicInfo.cpu_usage), State=\(basicInfo.run_state)"
        } else {
            return "Thread \(thread): Unable to get info"
        }
    }
}
```

#### Performance Bottlenecks Identification
```swift
class BottleneckDetector {
    private let profiler = PerformanceProfiler()
    private let threshold: Double = 0.1 // 100ms threshold
    
    func detectBottlenecks<T>(_ operation: @escaping () -> T, name: String) -> T {
        let result = profiler.profileOperation(operation, name: name)
        
        if let duration = profiler.getAverageDuration(for: name),
           duration > threshold {
            print("⚠️ Performance bottleneck detected in \(name): \(String(format: "%.4f", duration))s")
            suggestOptimizations(for: name, duration: duration)
        }
        
        return result
    }
    
    private func suggestOptimizations(for operation: String, duration: Double) {
        if duration > 1.0 {
            print("🔴 Critical: Consider moving to background queue")
        } else if duration > 0.5 {
            print("🟡 Warning: Consider async execution")
        } else if duration > 0.1 {
            print("🟠 Notice: Monitor for degradation")
        }
    }
}
```

#### Memory Pressure Handling
```swift
class MemoryPressureHandler {
    private let memoryWarningSource: DispatchSourceMemoryPressure
    private let queue = DispatchQueue(label: "memory.pressure", qos: .utility)
    
    init() {
        memoryWarningSource = DispatchSource.makeMemoryPressureSource(
            eventMask: [.warning, .critical],
            queue: queue
        )
        
        setupMemoryPressureHandling()
    }
    
    private func setupMemoryPressureHandling() {
        memoryWarningSource.setEventHandler { [weak self] in
            self?.handleMemoryPressure()
        }
        
        memoryWarningSource.setCancelHandler {
            print("Memory pressure monitoring cancelled")
        }
        
        memoryWarningSource.resume()
    }
    
    private func handleMemoryPressure() {
        let currentPressure = getCurrentMemoryPressure()
        
        switch currentPressure {
        case .warning:
            print("⚠️ Memory pressure warning - cleaning up caches")
            cleanupCaches()
            
        case .critical:
            print("🚨 Critical memory pressure - aggressive cleanup")
            aggressiveCleanup()
            
        default:
            break
        }
    }
    
    private func cleanupCaches() {
        // Clear image caches
        ImageCache.shared.clearOldEntries()
        
        // Clear data caches
        DataCache.shared.clearExpiredEntries()
        
        // Suggest garbage collection
        if #available(iOS 13.0, *) {
            Task.detached(priority: .background) {
                // Force memory cleanup
            }
        }
    }
    
    private func aggressiveCleanup() {
        // Clear all caches
        ImageCache.shared.clearAll()
        DataCache.shared.clearAll()
        
        // Cancel non-essential operations
        NetworkManager.shared.cancelNonEssentialRequests()
        
        // Force memory cleanup
        autoreleasepool {
            // Perform intensive cleanup operations
        }
    }
    
    private func getCurrentMemoryPressure() -> DispatchSource.MemoryPressureEvent {
        // This would typically come from the system
        // For demonstration, we'll return a default value
        return .warning
    }
    
    deinit {
        memoryWarningSource.cancel()
    }
}
```

---

## Queues and Dispatch Systems

**What it is:** The fundamental building blocks of concurrent programming in iOS, providing different types of queues for organizing and prioritizing work.

**Core Concepts:**
- **Queues**: Abstract representations of threads that manage work execution
- **Dispatch Systems**: Mechanisms for submitting work to queues
- **Quality of Service**: Priority levels that determine execution order
- **Synchronous vs Asynchronous**: Blocking vs non-blocking execution patterns

**Why it matters:**
- Proper queue selection affects app performance and responsiveness
- Different queue types serve different purposes
- Understanding queues is essential for effective concurrency
- Queue misuse can cause deadlocks and performance issues

### 1. Grand Central Dispatch (GCD) Queues

#### Main Queue

**What it is:** A special serial queue that runs on the main thread and is responsible for all UI updates and user interactions.

**Core Concepts:**
- **Serial Execution**: Only one task can run at a time
- **Main Thread**: Runs on the primary thread of the application
- **UI Updates**: All UI modifications must happen on this queue
- **User Interactions**: Handles touch events and user input

**When to use:**
- UI updates and animations
- User interaction handling
- Updating data models that affect the UI
- Refreshing UI after network operations

**Real-world examples:**
- Updating table view cells after data fetch
- Refreshing UI after network calls complete
- Handling button tap events
- Updating progress indicators

**Dangerous patterns to avoid:**
- Using `DispatchQueue.main.sync` can cause deadlocks
- Performing heavy work on the main queue blocks the UI
- Not using the main queue for UI updates can cause crashes
```swift
// Main queue - UI updates only
DispatchQueue.main.async {
    self.updateUI()
    self.animateButton()
}

// Main queue sync (DANGEROUS - can cause deadlock!)
// DispatchQueue.main.sync { } // ❌ Never do this!
```

**When to use**: UI updates, animations, user interactions
**Real-world example**: Updating table view cells, refreshing UI after network calls

#### Global Queues

**What it is:** System-provided concurrent queues with different priority levels (Quality of Service) for different types of work.

**Core Concepts:**
- **Quality of Service (QoS)**: Priority system that determines execution order
- **Concurrent Execution**: Multiple tasks can run simultaneously
- **System Managed**: iOS automatically manages thread allocation
- **Priority Levels**: Different queues for different types of work

**QoS Priority System:**
1. **userInteractive** (0.1 seconds): Highest priority for animations and user input
2. **userInitiated** (0.2 seconds): High priority for user-triggered operations
3. **default** (0.5 seconds): Default priority for general purpose tasks
4. **utility** (2.0 seconds): Lower priority for background processing
5. **background** (10+ seconds): Lowest priority for maintenance tasks
6. **unspecified**: No QoS specified, system decides priority

**When to use each level:**
- **userInteractive**: Animations, user input processing, immediate UI feedback
- **userInitiated**: User actions like button taps, search operations
- **default**: General purpose work, default choice for most operations
- **utility**: Background processing, I/O operations, network requests
- **background**: Maintenance tasks, cleanup operations, prefetching

**Real-world examples:**
- **userInteractive**: Smooth animations, responsive UI
- **userInitiated**: Processing user search queries, handling button taps
- **default**: General data processing, API calls
- **utility**: Image processing, file operations, background sync
- **background**: Database cleanup, cache maintenance, analytics
```swift
// Quality of Service (QoS) levels
DispatchQueue.global(qos: .userInteractive).async {
    // Highest priority - animations, user input
}

DispatchQueue.global(qos: .userInitiated).async {
    // High priority - user-initiated operations
}

DispatchQueue.global(qos: .default).async {
    // Default priority
}

DispatchQueue.global(qos: .utility).async {
    // Lower priority - background tasks
}

DispatchQueue.global(qos: .background).async {
    // Lowest priority - maintenance tasks
}

DispatchQueue.global(qos: .unspecified).async {
    // No QoS specified
}
```

**QoS Priority Order** (highest to lowest):
1. **userInteractive**: 0.1 seconds
2. **userInitiated**: 0.2 seconds
3. **default**: 0.5 seconds
4. **utility**: 2.0 seconds
5. **background**: 10+ seconds

#### Custom Serial Queues

**What it is:** User-defined queues that execute tasks one at a time in the order they were submitted.

**Core Concepts:**
- **Serial Execution**: Tasks execute sequentially, one after another
- **FIFO Order**: First In, First Out execution order
- **Custom Labels**: Meaningful names for debugging and profiling
- **Thread Safety**: Natural synchronization for shared resources

**When to use:**
- Database operations that must be sequential
- File I/O operations that can't be concurrent
- Critical sections that need exclusive access
- Operations that depend on previous results
- Resource management and cleanup

**Real-world examples:**
- Core Data operations (insert, update, delete)
- File downloads that must be sequential
- Database migrations and schema updates
- Configuration management and settings
- Logging and analytics data processing

**Benefits:**
- Prevents race conditions automatically
- Easier to reason about execution order
- Natural synchronization without locks
- Predictable behavior and debugging

**Considerations:**
- Can become a bottleneck if tasks are long-running
- Not suitable for independent operations
- May cause blocking if not used carefully
```swift
// Serial queue - tasks execute one at a time
let serialQueue = DispatchQueue(label: "com.app.serialQueue")

serialQueue.async {
    // Task 1
    print("Task 1 started")
    Thread.sleep(forTimeInterval: 1)
    print("Task 1 completed")
}

serialQueue.async {
    // Task 2 - waits for Task 1 to complete
    print("Task 2 started")
    print("Task 2 completed")
}

// Output:
// Task 1 started
// Task 1 completed
// Task 2 started
// Task 2 completed
```

**When to use**: Database operations, file I/O, critical sections
**Real-world example**: Core Data operations, file downloads

#### Custom Concurrent Queues

**What it is:** User-defined queues that allow multiple tasks to execute simultaneously, improving throughput for independent operations.

**Core Concepts:**
- **Concurrent Execution**: Multiple tasks can run at the same time
- **Independent Operations**: Tasks don't depend on each other
- **Custom Labels**: Meaningful names for debugging and profiling
- **Performance Optimization**: Better utilization of multiple CPU cores

**When to use:**
- Independent operations that can run in parallel
- Image processing and filtering operations
- Multiple network requests that don't depend on each other
- Data transformation and computation tasks
- Background processing and analysis

**Real-world examples:**
- Processing multiple images simultaneously
- Making parallel API calls to different endpoints
- Computing multiple mathematical operations
- Analyzing different data sets concurrently
- Prefetching multiple resources

**Benefits:**
- Better CPU utilization and performance
- Faster completion of independent tasks
- Scalable with available CPU cores
- Suitable for CPU-intensive operations

**Considerations:**
- Tasks must be truly independent
- Can consume more system resources
- May cause contention for shared resources
- Requires careful design to avoid race conditions
```swift
// Concurrent queue - multiple tasks can run simultaneously
let concurrentQueue = DispatchQueue(label: "com.app.concurrentQueue", 
                                  attributes: .concurrent)

concurrentQueue.async {
    // Task 1
    print("Task 1 started")
    Thread.sleep(forTimeInterval: 1)
    print("Task 1 completed")
}

concurrentQueue.async {
    // Task 2 - runs simultaneously with Task 1
    print("Task 2 started")
    print("Task 2 completed")
}

// Output (order may vary):
// Task 1 started
// Task 2 started
// Task 2 completed
// Task 1 completed
```

**When to use**: Independent operations, image processing, network requests
**Real-world example**: Processing multiple images, parallel API calls

### 2. Queue Types and Characteristics

**What it is:** Understanding the fundamental differences between queue types and their execution characteristics.

**Core Concepts:**
- **Serial Queues**: Execute tasks one at a time in order
- **Concurrent Queues**: Execute multiple tasks simultaneously
- **Execution Order**: How tasks are scheduled and executed
- **Performance Characteristics**: Impact on throughput and latency

**Why it matters:**
- Choosing the right queue type affects performance
- Different queue types serve different purposes
- Understanding characteristics helps prevent bottlenecks
- Proper queue selection improves user experience

#### Serial vs Concurrent

**Serial Queue Characteristics:**
- **Sequential Execution**: Tasks execute one after another
- **Predictable Order**: FIFO (First In, First Out) execution
- **Natural Synchronization**: Prevents race conditions automatically
- **Single Thread**: Typically uses one thread for execution
- **Use Cases**: Critical sections, ordered operations, resource management

**Concurrent Queue Characteristics:**
- **Parallel Execution**: Multiple tasks can run simultaneously
- **Unpredictable Order**: Tasks may complete in different order than submitted
- **Requires Synchronization**: Manual synchronization needed for shared resources
- **Multiple Threads**: Can utilize multiple CPU cores
- **Use Cases**: Independent operations, CPU-intensive tasks, parallel processing
```swift
// Serial Queue Characteristics
let serialQueue = DispatchQueue(label: "serial")
serialQueue.async { /* Task A */ }
serialQueue.async { /* Task B */ }
// Execution order: A → B (sequential)

// Concurrent Queue Characteristics
let concurrentQueue = DispatchQueue(label: "concurrent", attributes: .concurrent)
concurrentQueue.async { /* Task A */ }
concurrentQueue.async { /* Task B */ }
// Execution order: A and B may run simultaneously
```

#### Synchronous vs Asynchronous Execution

**What it is:** Understanding the difference between blocking and non-blocking execution patterns in concurrent programming.

**Core Concepts:**
- **Synchronous Execution**: Blocks the current thread until completion
- **Asynchronous Execution**: Returns immediately, doesn't block the current thread
- **Thread Blocking**: Impact on thread availability and responsiveness
- **Completion Handling**: How to handle task completion

**Why it matters:**
- Synchronous execution can block UI updates
- Asynchronous execution improves responsiveness
- Understanding the difference prevents deadlocks
- Proper choice affects app performance and user experience

**Synchronous Execution (Sync):**
- **Blocking Behavior**: Current thread waits for completion
- **Return Value**: Direct return of result
- **Error Handling**: Throws errors directly
- **Use Cases**: Simple operations, when you need immediate results
- **Danger**: Can cause deadlocks if used incorrectly

**Asynchronous Execution (Async):**
- **Non-blocking Behavior**: Current thread continues immediately
- **Completion Handling**: Uses completion handlers or async/await
- **Error Handling**: Errors passed through completion handlers
- **Use Cases**: Long-running operations, UI responsiveness
- **Benefits**: Better user experience, prevents blocking

**Key Differences:**
- **Sync**: Blocks current thread, waits for completion, direct return
- **Async**: Non-blocking, continues execution, completion-based results
```swift
// Synchronous (blocks current thread)
DispatchQueue.global().sync {
    // This blocks until completion
    performHeavyOperation()
}
// Code here waits for the above to complete

// Asynchronous (non-blocking)
DispatchQueue.global().async {
    // This runs in background, doesn't block
    performHeavyOperation()
}
// Code here continues immediately
```

**Key Differences**:
- **Sync**: Blocks current thread, waits for completion
- **Async**: Non-blocking, continues execution immediately

---

## Async/Await and Structured Concurrency

**What it is:** Modern Swift concurrency system introduced in iOS 13+ that provides structured, readable asynchronous code without callback hell.

**Core Concepts:**
- **Async Functions**: Functions that can be suspended and resumed
- **Await Keyword**: Points where async functions can be suspended
- **Structured Concurrency**: Hierarchical organization of concurrent tasks
- **Task Isolation**: Automatic memory management and thread safety
- **Error Handling**: Natural error propagation without completion handlers

**Why it matters:**
- Eliminates callback hell and pyramid of doom
- Makes asynchronous code as readable as synchronous code
- Provides better error handling and cancellation support
- Enables more maintainable and testable code
- Reduces bugs related to asynchronous programming

**Benefits over traditional approaches:**
- **Readability**: Code reads like synchronous code
- **Error Handling**: Natural try-catch syntax
- **Cancellation**: Built-in support for task cancellation
- **Memory Safety**: Automatic memory management
- **Performance**: Efficient task scheduling and execution

### Modern Swift Concurrency (iOS 13+)

#### Basic Async/Await

**What it is:** The fundamental syntax and patterns for writing asynchronous code using Swift's modern concurrency system.

**Core Concepts:**
- **async keyword**: Marks functions that can be suspended
- **await keyword**: Points where functions can be suspended
- **Suspension**: Functions can pause execution and resume later
- **Continuation**: Automatic resumption when awaited operations complete
- **Error propagation**: Natural error handling with try-catch

**Why it matters:**
- Eliminates the complexity of completion handlers
- Makes asynchronous code easier to read and understand
- Provides better error handling than callback-based approaches
- Enables composition of asynchronous operations
- Reduces callback hell and pyramid of doom

**Key Differences from Traditional Approaches:**

**Traditional Completion Handler Approach:**
- Uses closures for completion handling
- Error handling through completion callbacks
- Can lead to nested callbacks (callback hell)
- Harder to compose multiple operations
- More complex error handling

**Modern Async/Await Approach:**
- Uses natural function syntax
- Error handling with try-catch
- Linear code flow without nesting
- Easy composition of operations
- Cleaner, more maintainable code

**Real-world benefits:**
- **Network Requests**: Cleaner API calls without nested callbacks
- **File Operations**: Sequential file operations without callback chains
- **Database Operations**: Complex database workflows become linear
- **UI Updates**: Safe UI updates from background operations
```swift
// Traditional completion handler approach
func fetchUserData(completion: @escaping (User?, Error?) -> Void) {
    URLSession.shared.dataTask(with: url) { data, response, error in
        // Handle response
        completion(user, error)
    }.resume()
}

// Modern async/await approach
func fetchUserData() async throws -> User {
    let (data, response) = try await URLSession.shared.data(from: url)
    let user = try JSONDecoder().decode(User.self, from: data)
    return user
}

// Usage
Task {
    do {
        let user = try await fetchUserData()
        await MainActor.run {
            self.user = user
            self.updateUI()
        }
    } catch {
        print("Error: \(error)")
    }
}
```

#### Structured Concurrency with Task Groups

**What it is:** A way to manage multiple concurrent operations as a group, ensuring proper lifecycle management and error handling.

**Core Concepts:**
- **Task Groups**: Collections of related concurrent operations
- **Group Lifecycle**: Automatic management of task creation and cleanup
- **Error Handling**: Proper error propagation across group members
- **Cancellation**: Automatic cancellation of all group members
- **Completion**: Waiting for all tasks to complete

**Why it matters:**
- Prevents task leaks and orphaned operations
- Ensures proper cleanup when operations complete or fail
- Provides structured error handling across multiple operations
- Enables efficient concurrent execution with proper coordination
- Makes complex concurrent workflows manageable

**Key Benefits:**
- **Automatic Cleanup**: Tasks are automatically cleaned up when group exits
- **Error Propagation**: Errors from any task can be handled appropriately
- **Cancellation Support**: Cancelling the group cancels all child tasks
- **Resource Management**: Efficient use of system resources
- **Composability**: Groups can be composed and nested

**Use Cases:**
- **Parallel Data Processing**: Processing multiple items simultaneously
- **Multiple Network Requests**: Fetching data from multiple endpoints
- **File Operations**: Reading/writing multiple files concurrently
- **Image Processing**: Applying filters to multiple images
- **Database Operations**: Performing multiple database queries

**Real-world scenarios:**
- **Social Media App**: Loading user profile, posts, and friends simultaneously
- **E-commerce App**: Fetching product details, reviews, and recommendations
- **Photo App**: Processing multiple images with different filters
- **News App**: Loading articles, images, and related content
```swift
func fetchMultipleUsers() async throws -> [User] {
    let userIds = [1, 2, 3, 4, 5]
    
    return try await withThrowingTaskGroup(of: User.self) { group in
        for id in userIds {
            group.addTask {
                return try await fetchUser(id: id)
            }
        }
        
        var users: [User] = []
        for try await user in group {
            users.append(user)
        }
        return users
    }
}
```

#### Actor for Thread Safety

**What it is:** A reference type that provides automatic isolation of mutable state, ensuring thread safety without manual synchronization.

**Core Concepts:**
- **Actor Isolation**: Automatic protection of mutable state
- **Message Passing**: Communication through method calls
- **No Manual Locks**: Automatic synchronization without explicit locks
- **Memory Safety**: Automatic memory management and cleanup
- **Performance**: Efficient concurrent access without blocking

**Why it matters:**
- Eliminates the need for manual synchronization
- Prevents race conditions automatically
- Provides better performance than traditional locking approaches
- Makes concurrent code safer and easier to write
- Reduces bugs related to thread safety

**Key Benefits:**
- **Automatic Thread Safety**: No need to manually manage locks or queues
- **Performance**: Efficient concurrent access without blocking
- **Memory Safety**: Automatic memory management and cleanup
- **Composability**: Actors can be composed and nested safely
- **Debugging**: Easier to debug than manually synchronized code

**Use Cases:**
- **Shared State Management**: Managing app state across multiple threads
- **Resource Management**: Managing shared resources like caches
- **Data Processing**: Coordinating data processing across threads
- **Configuration Management**: Managing app configuration safely
- **Service Coordination**: Coordinating multiple services

**Real-world examples:**
- **User Session Manager**: Managing user authentication state
- **Cache Manager**: Managing image or data caches
- **Configuration Manager**: Managing app settings and preferences
- **Data Synchronizer**: Coordinating data synchronization
- **Network Manager**: Managing network state and requests

**Comparison with Traditional Approaches:**

**Traditional Approach (Manual Synchronization):**
- Requires manual lock management
- Prone to deadlocks and race conditions
- Complex error handling
- Harder to debug and maintain

**Actor Approach:**
- Automatic isolation and synchronization
- No manual lock management needed
- Natural error handling
- Easier to debug and maintain
```swift
actor UserManager {
    private var users: [User] = []
    private var isLoading = false
    
    func addUser(_ user: User) {
        users.append(user)
    }
    
    func getUsers() -> [User] {
        return users
    }
    
    func setLoading(_ loading: Bool) {
        isLoading = loading
    }
}

// Usage
let userManager = UserManager()
Task {
    await userManager.addUser(User(name: "John"))
    let users = await userManager.getUsers()
}
```

---

## Networking in Depth

**What it is:** Comprehensive coverage of iOS networking capabilities, from basic HTTP requests to advanced networking patterns and best practices.

**Core Concepts:**
- **URLSession**: Foundation framework for network operations
- **HTTP Methods**: GET, POST, PUT, DELETE operations
- **Request/Response Handling**: Managing network data flow
- **Error Handling**: Comprehensive error classification and recovery
- **Configuration**: Customizing network behavior and policies

**Why it matters:**
- Most iOS apps require network communication
- Proper networking affects app reliability and user experience
- Network errors are common and must be handled gracefully
- Network performance impacts app responsiveness
- Security considerations are critical for user data

**Key Areas Covered:**
- Basic network requests and responses
- Advanced URLSession configuration
- Network request patterns and best practices
- Error handling and recovery strategies
- Performance optimization and caching

### 1. URLSession Fundamentals

#### Basic Network Request

**What it is:** The fundamental building blocks for making HTTP requests and handling responses in iOS applications.

**Core Concepts:**
- **HTTP Request**: Structured data sent to a server
- **HTTP Response**: Structured data received from a server
- **Status Codes**: HTTP response codes indicating success/failure
- **Data Handling**: Processing response data (JSON, XML, binary)
- **Error Handling**: Managing network failures and errors

**Why it matters:**
- Foundation for all network communication in iOS apps
- Understanding basics enables building complex networking features
- Proper error handling prevents app crashes
- Response validation ensures data integrity
- Status code handling provides user feedback

**Key Components:**
- **Request Creation**: Building HTTP requests with proper headers
- **Response Processing**: Handling different response types
- **Status Code Validation**: Checking for successful responses
- **Data Decoding**: Converting response data to Swift objects
- **Error Propagation**: Handling and reporting network errors

**Real-world scenarios:**
- **API Integration**: Connecting to REST APIs
- **File Downloads**: Downloading images, documents, media
- **Data Synchronization**: Syncing app data with servers
- **User Authentication**: Login and registration flows
- **Content Updates**: Fetching latest app content

**Common HTTP Methods:**
- **GET**: Retrieve data from server
- **POST**: Send data to server
- **PUT**: Update existing data
- **DELETE**: Remove data from server
- **PATCH**: Partial data updates
```swift
class NetworkManager {
    static let shared = NetworkManager()
    private let session = URLSession.shared
    
    func fetchData<T: Decodable>(from url: URL) async throws -> T {
        let (data, response) = try await session.data(from: url)
        
        guard let httpResponse = response as? HTTPURLResponse else {
            throw NetworkError.invalidResponse
        }
        
        guard httpResponse.statusCode == 200 else {
            throw NetworkError.httpError(statusCode: httpResponse.statusCode)
        }
        
        return try JSONDecoder().decode(T.self, from: data)
    }
}

enum NetworkError: Error {
    case invalidResponse
    case httpError(statusCode: Int)
    case decodingError
}
```

#### Advanced URLSession Configuration

**What it is:** Customizing URLSession behavior for specific use cases, performance requirements, and network conditions.

**Core Concepts:**
- **Configuration Objects**: URLSessionConfiguration for customizing behavior
- **Timeout Settings**: Controlling request and resource timeouts
- **Connection Policies**: Managing cellular and expensive network access
- **Custom Headers**: Adding authentication and metadata to requests
- **Caching Policies**: Controlling response caching behavior

**Why it matters:**
- Different apps have different networking requirements
- Customization improves performance and user experience
- Proper configuration prevents network failures
- Security headers protect user data
- Caching reduces network usage and improves performance

**Key Configuration Options:**
- **Timeout Intervals**: Request and resource timeout settings
- **Network Access**: Cellular and expensive network policies
- **Custom Headers**: Authentication, user agent, content type
- **Caching**: Memory and disk cache configuration
- **Connection Limits**: Maximum concurrent connections

**Use Cases:**
- **High-Security Apps**: Banking, healthcare, enterprise
- **Media Apps**: Video streaming, large file downloads
- **Social Apps**: Real-time updates, image sharing
- **E-commerce Apps**: Product catalogs, payment processing
- **Utility Apps**: Background sync, offline support

**Performance Considerations:**
- **Connection Pooling**: Reusing connections for efficiency
- **Request Prioritization**: Managing multiple concurrent requests
- **Bandwidth Management**: Controlling data usage
- **Background Processing**: Continuing network operations when app is backgrounded
```swift
class AdvancedNetworkManager {
    private let session: URLSession
    
    init() {
        let config = URLSessionConfiguration.default
        config.timeoutIntervalForRequest = 30
        config.timeoutIntervalForResource = 300
        config.waitsForConnectivity = true
        config.allowsCellularAccess = true
        config.allowsExpensiveNetworkAccess = true
        
        // Custom headers
        config.httpAdditionalHeaders = [
            "User-Agent": "MyApp/1.0",
            "Accept": "application/json"
        ]
        
        self.session = URLSession(configuration: config)
    }
    
    func uploadData(_ data: Data, to url: URL) async throws -> UploadResponse {
        var request = URLRequest(url: url)
        request.httpMethod = "POST"
        request.setValue("application/json", forHTTPHeaderField: "Content-Type")
        
        let (responseData, response) = try await session.upload(for: request, from: data)
        
        guard let httpResponse = response as? HTTPURLResponse,
              httpResponse.statusCode == 200 else {
            throw NetworkError.httpError(statusCode: (response as? HTTPURLResponse)?.statusCode ?? 0)
        }
        
        return try JSONDecoder().decode(UploadResponse.self, from: responseData)
    }
}
```

### 2. Network Request Patterns

**What it is:** Common patterns and strategies for handling network requests reliably, including retry logic, cancellation, and error recovery.

**Core Concepts:**
- **Retry Strategies**: Automatically retrying failed requests
- **Request Cancellation**: Stopping ongoing network operations
- **Error Classification**: Categorizing different types of network errors
- **Fallback Mechanisms**: Alternative approaches when primary methods fail
- **Request Queuing**: Managing multiple concurrent requests

**Why it matters:**
- Network failures are common and must be handled gracefully
- Retry logic improves app reliability and user experience
- Proper cancellation prevents resource waste
- Error classification enables appropriate user feedback
- Fallback mechanisms ensure app functionality during network issues

**Key Patterns:**
- **Exponential Backoff**: Increasing delays between retry attempts
- **Circuit Breaker**: Preventing cascading failures
- **Request Deduplication**: Avoiding duplicate requests
- **Request Prioritization**: Managing request importance
- **Batch Operations**: Grouping multiple requests together

#### Retry Logic

**What it is:** Automatically retrying failed network requests with intelligent backoff strategies to improve reliability.

**Core Concepts:**
- **Automatic Retry**: Detecting failures and retrying automatically
- **Backoff Strategies**: Delaying retry attempts to avoid overwhelming servers
- **Maximum Retries**: Limiting retry attempts to prevent infinite loops
- **Error Classification**: Different retry strategies for different error types
- **User Feedback**: Informing users about retry attempts and progress

**Why it matters:**
- Network failures are often temporary and resolve with retries
- Improves app reliability and user experience
- Reduces user frustration with network issues
- Prevents unnecessary app crashes or errors
- Enables graceful handling of poor network conditions

**Retry Strategies:**
- **Immediate Retry**: Retry immediately for transient errors
- **Fixed Delay**: Wait a fixed time before retrying
- **Exponential Backoff**: Increase delay exponentially with each attempt
- **Jitter**: Add randomness to prevent thundering herd problems
- **Adaptive Retry**: Adjust strategy based on error patterns

**When to Retry:**
- **Transient Errors**: 5xx server errors, network timeouts
- **Rate Limiting**: 429 Too Many Requests
- **Temporary Issues**: Network connectivity problems
- **Server Overload**: 503 Service Unavailable

**When NOT to Retry:**
- **Client Errors**: 4xx errors (bad request, unauthorized)
- **Authentication Failures**: 401 Unauthorized, 403 Forbidden
- **Resource Not Found**: 404 Not Found
- **Validation Errors**: 422 Unprocessable Entity

**Real-world examples:**
- **Social Media Apps**: Retrying failed post uploads
- **E-commerce Apps**: Retrying failed payment processing
- **Media Apps**: Retrying failed video streaming
- **Messaging Apps**: Retrying failed message delivery
- **Sync Apps**: Retrying failed data synchronization
```swift
func fetchWithRetry<T: Decodable>(from url: URL, 
                                 maxRetries: Int = 3,
                                 delay: TimeInterval = 1.0) async throws -> T {
    var lastError: Error?
    
    for attempt in 1...maxRetries {
        do {
            return try await fetchData(from: url)
        } catch {
            lastError = error
            if attempt < maxRetries {
                try await Task.sleep(nanoseconds: UInt64(delay * 1_000_000_000))
                delay *= 2 // Exponential backoff
            }
        }
    }
    
    throw lastError ?? NetworkError.unknown
}
```

#### Request Cancellation

**What it is:** The ability to stop ongoing network operations before they complete, preventing resource waste and improving app responsiveness.

**Core Concepts:**
- **Cancellation Tokens**: Mechanisms to signal cancellation
- **Resource Cleanup**: Properly releasing network resources
- **User Experience**: Allowing users to cancel long-running operations
- **Memory Management**: Preventing memory leaks from cancelled requests
- **State Management**: Handling cancelled operations gracefully

**Why it matters:**
- Prevents unnecessary network usage and battery drain
- Improves app responsiveness and user experience
- Allows users to change their minds about operations
- Prevents resource waste on abandoned operations
- Enables proper cleanup of network resources

**Cancellation Scenarios:**
- **User Cancellation**: User taps cancel button or navigates away
- **Timeout Cancellation**: Request exceeds time limit
- **App State Changes**: App goes to background or terminates
- **Network Changes**: Network connectivity changes
- **Priority Changes**: Higher priority requests need resources

**Implementation Approaches:**
- **URLSessionTask Cancellation**: Using built-in cancellation methods
- **Custom Cancellation**: Implementing custom cancellation logic
- **Cancellation Propagation**: Cancelling dependent operations
- **Cleanup Handlers**: Proper resource cleanup on cancellation
- **User Feedback**: Informing users about cancellation status

**Real-world examples:**
- **File Downloads**: Cancelling large file downloads
- **Image Loading**: Cancelling image requests when scrolling
- **Search Operations**: Cancelling search requests for new queries
- **Data Sync**: Cancelling background synchronization
- **Media Streaming**: Cancelling video/audio streaming

**Best Practices:**
- **Immediate Response**: Cancel operations as quickly as possible
- **Resource Cleanup**: Ensure all resources are properly released
- **User Feedback**: Provide clear feedback about cancellation
- **State Consistency**: Maintain app state consistency after cancellation
- **Error Handling**: Distinguish between cancellation and failure
```swift
class CancellableNetworkManager {
    private var tasks: [URLSessionTask] = []
    
    func fetchData<T: Decodable>(from url: URL) async throws -> T {
        let task = Task {
            try await fetchDataInternal(from: url)
        }
        
        // Store task for potential cancellation
        if let urlTask = task.value as? URLSessionTask {
            tasks.append(urlTask)
        }
        
        return try await task.value
    }
    
    func cancelAllRequests() {
        tasks.forEach { $0.cancel() }
        tasks.removeAll()
    }
}
```

---

## Advanced Networking Features

**What it is:** Enterprise-level networking capabilities that go beyond basic HTTP requests, including security, real-time communication, and advanced network management.

**Core Concepts:**
- **Network Security**: Protecting data in transit and at rest
- **Authentication**: Verifying user identity and permissions
- **Real-time Communication**: Live data exchange and updates
- **Background Processing**: Continuing network operations when app is backgrounded
- **Advanced Protocols**: WebSocket, Server-Sent Events, and custom protocols

**Why it matters:**
- Security is critical for user data protection
- Authentication enables personalized experiences
- Real-time communication provides engaging user experiences
- Background processing improves app functionality
- Advanced protocols enable sophisticated networking features

**Key Areas Covered:**
- Certificate pinning and SSL/TLS security
- OAuth 2.0 and biometric authentication
- WebSocket and real-time communication
- Push notifications and background processing
- Custom URL protocols and interceptors

### Network Security & Authentication

#### Certificate Pinning Implementation

**What it is:** A security technique that validates server certificates against a predefined set of trusted certificates, preventing man-in-the-middle attacks.

**Core Concepts:**
- **Certificate Validation**: Verifying server identity beyond standard SSL/TLS
- **Trust Anchors**: Predefined set of trusted certificates
- **Attack Prevention**: Protecting against certificate authority compromises
- **Hash Verification**: Using certificate fingerprints for validation
- **Fallback Handling**: Managing validation failures gracefully

**Why it matters:**
- Protects against man-in-the-middle attacks
- Prevents certificate authority compromises from affecting your app
- Ensures communication with legitimate servers
- Required for high-security applications (banking, healthcare)
- Provides additional security beyond standard SSL/TLS

**Security Threats Addressed:**
- **Man-in-the-Middle Attacks**: Intercepting and modifying traffic
- **Certificate Authority Compromise**: Fake certificates from compromised CAs
- **DNS Spoofing**: Redirecting traffic to malicious servers
- **Network Interception**: Corporate or public network monitoring
- **Certificate Substitution**: Replacing valid certificates with fake ones

**Implementation Approaches:**
- **Hash Pinning**: Storing certificate hash values
- **Public Key Pinning**: Storing public key hashes
- **Certificate Chain Pinning**: Validating entire certificate chains
- **Dynamic Pinning**: Updating pin values over time
- **Fallback Pinning**: Multiple pin values for redundancy

**Use Cases:**
- **Financial Apps**: Banking, payment processing, trading
- **Healthcare Apps**: Medical records, telemedicine, health monitoring
- **Enterprise Apps**: Corporate data, employee information
- **Government Apps**: Official services, citizen data
- **High-Security Apps**: Authentication, sensitive data transmission

**Best Practices:**
- **Multiple Pins**: Store multiple certificate hashes
- **Regular Updates**: Update pin values when certificates change
- **Graceful Degradation**: Handle pin validation failures
- **User Communication**: Inform users about security measures
- **Testing**: Thorough testing of pin validation logic
```swift
class CertificatePinningManager: NSObject, URLSessionDelegate {
    private let pinnedCertificates: Set<String> = [
        "sha256/AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA=", // Replace with actual cert hashes
        "sha256/BBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB="
    ]
    
    func urlSession(_ session: URLSession,
                   didReceive challenge: URLAuthenticationChallenge,
                   completionHandler: @escaping (URLSession.AuthChallengeDisposition, URLCredential?) -> Void) {
        
        guard let serverTrust = challenge.protectionSpace.serverTrust else {
            completionHandler(.cancelAuthenticationChallenge, nil)
            return
        }
        
        // Get certificate chain
        let certificateCount = SecTrustGetCertificateCount(serverTrust)
        var certificates: [SecCertificate] = []
        
        for i in 0..<certificateCount {
            if let certificate = SecTrustGetCertificateAtIndex(serverTrust, i) {
                certificates.append(certificate)
            }
        }
        
        // Check if any certificate in the chain matches our pinned certificates
        for certificate in certificates {
            let data = SecCertificateCopyData(certificate)
            let sha256 = sha256(data: data as Data)
            
            if pinnedCertificates.contains(sha256) {
                completionHandler(.useCredential, URLCredential(trust: serverTrust))
                return
            }
        }
        
        // Certificate not found in pinned set
        completionHandler(.cancelAuthenticationChallenge, nil)
    }
    
    private func sha256(data: Data) -> String {
        var hash = [UInt8](repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
        data.withUnsafeBytes { buffer in
            _ = CC_SHA256(buffer.baseAddress, CC_LONG(buffer.count), &hash)
        }
        return "sha256/" + hash.map { String(format: "%02x", $0) }.joined()
    }
}

// Usage with custom URLSession
class SecureNetworkManager {
    private let session: URLSession
    
    init() {
        let config = URLSessionConfiguration.default
        let delegate = CertificatePinningManager()
        self.session = URLSession(configuration: config, delegate: delegate, delegateQueue: nil)
    }
    
    func secureRequest<T: Decodable>(from url: URL) async throws -> T {
        let (data, response) = try await session.data(from: url)
        
        guard let httpResponse = response as? HTTPURLResponse,
              (200...299).contains(httpResponse.statusCode) else {
            throw NetworkError.httpError(statusCode: (response as? HTTPURLResponse)?.statusCode ?? 0)
        }
        
        return try JSONDecoder().decode(T.self, from: data)
    }
}
```

#### OAuth 2.0 Flow Handling

**What it is:** Implementation of the OAuth 2.0 authentication protocol for secure user authentication and authorization in iOS applications.

**Core Concepts:**
- **Authorization Flow**: Multi-step process for user authentication
- **Access Tokens**: Short-lived tokens for API access
- **Refresh Tokens**: Long-lived tokens for obtaining new access tokens
- **Scope Management**: Controlling what resources the app can access
- **Token Storage**: Securely storing authentication credentials

**Why it matters:**
- Industry standard for secure authentication
- Enables third-party login (Google, Facebook, Apple)
- Provides secure access to user resources
- Supports fine-grained permission control
- Enables personalized user experiences

**OAuth 2.0 Flow Steps:**
1. **Authorization Request**: App requests user permission
2. **User Consent**: User grants or denies permissions
3. **Authorization Code**: Server returns temporary code
4. **Token Exchange**: App exchanges code for access token
5. **API Access**: App uses token for authenticated requests
6. **Token Refresh**: Renewing expired access tokens

**Grant Types:**
- **Authorization Code**: Most secure, for server-side apps
- **Implicit**: Less secure, for client-side apps
- **Client Credentials**: For app-to-app authentication
- **Resource Owner Password**: Direct username/password (not recommended)
- **Device Authorization**: For devices without browsers

**Security Considerations:**
- **HTTPS Only**: All OAuth communication must use HTTPS
- **State Parameter**: Preventing CSRF attacks
- **PKCE**: Proof Key for Code Exchange for public clients
- **Token Storage**: Secure storage of tokens in keychain
- **Token Validation**: Verifying token authenticity and expiration

**Real-world examples:**
- **Social Media Apps**: Login with Google, Facebook, Twitter
- **E-commerce Apps**: User accounts and order history
- **Banking Apps**: Secure access to financial data
- **Healthcare Apps**: Patient portal access
- **Enterprise Apps**: Single sign-on integration

**Best Practices:**
- **Secure Storage**: Use iOS Keychain for token storage
- **Token Refresh**: Implement automatic token renewal
- **Error Handling**: Graceful handling of authentication failures
- **User Experience**: Clear feedback during authentication process
- **Logout Handling**: Proper cleanup of stored tokens
```swift
class OAuth2Manager: ObservableObject {
    @Published var isAuthenticated = false
    @Published var accessToken: String?
    
    private let clientID = "your_client_id"
    private let clientSecret = "your_client_secret"
    private let redirectURI = "yourapp://oauth/callback"
    private let authURL = "https://api.example.com/oauth/authorize"
    private let tokenURL = "https://api.example.com/oauth/token"
    
    func startOAuthFlow() {
        var components = URLComponents(string: authURL)
        components?.queryItems = [
            URLQueryItem(name: "client_id", value: clientID),
            URLQueryItem(name: "redirect_uri", value: redirectURI),
            URLQueryItem(name: "response_type", value: "code"),
            URLQueryItem(name: "scope", value: "read write"),
            URLQueryItem(name: "state", value: generateRandomState())
        ]
        
        if let url = components?.url {
            UIApplication.shared.open(url)
        }
    }
    
    func handleCallback(url: URL) async throws {
        guard let components = URLComponents(url: url, resolvingAgainstBaseURL: false),
              let code = components.queryItems?.first(where: { $0.name == "code" })?.value,
              let state = components.queryItems?.first(where: { $0.name == "state" })?.value else {
            throw OAuthError.invalidCallback
        }
        
        // Exchange authorization code for access token
        try await exchangeCodeForToken(code: code)
    }
    
    private func exchangeCodeForToken(code: String) async throws {
        var request = URLRequest(url: URL(string: tokenURL)!)
        request.httpMethod = "POST"
        request.setValue("application/x-www-form-urlencoded", forHTTPHeaderField: "Content-Type")
        
        let body = [
            "grant_type": "authorization_code",
            "client_id": clientID,
            "client_secret": clientSecret,
            "code": code,
            "redirect_uri": redirectURI
        ]
        
        request.httpBody = body.percentEncoded()
        
        let (data, response) = try await URLSession.shared.data(for: request)
        
        guard let httpResponse = response as? HTTPURLResponse,
              httpResponse.statusCode == 200 else {
            throw OAuthError.tokenExchangeFailed
        }
        
        let tokenResponse = try JSONDecoder().decode(TokenResponse.self, from: data)
        
        await MainActor.run {
            self.accessToken = tokenResponse.accessToken
            self.isAuthenticated = true
        }
        
        // Store token securely
        try await storeTokenSecurely(tokenResponse.accessToken)
    }
    
    private func storeTokenSecurely(_ token: String) async throws {
        let query: [String: Any] = [
            kSecClass as String: kSecClassGenericPassword,
            kSecAttrAccount as String: "oauth_access_token",
            kSecValueData as String: token.data(using: .utf8)!,
            kSecAttrAccessible as String: kSecAttrAccessibleWhenUnlockedThisDeviceOnly
        ]
        
        SecItemDelete(query as CFDictionary)
        
        let status = SecItemAdd(query as CFDictionary, nil)
        guard status == errSecSuccess else {
            throw OAuthError.tokenStorageFailed
        }
    }
    
    private func generateRandomState() -> String {
        let letters = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"
        return String((0..<32).map { _ in letters.randomElement()! })
    }
}

struct TokenResponse: Codable {
    let accessToken: String
    let tokenType: String
    let expiresIn: Int
    let refreshToken: String?
    
    enum CodingKeys: String, CodingKey {
        case accessToken = "access_token"
        case tokenType = "token_type"
        case expiresIn = "expires_in"
        case refreshToken = "refresh_token"
    }
}

enum OAuthError: Error {
    case invalidCallback
    case tokenExchangeFailed
    case tokenStorageFailed
}

extension Dictionary {
    func percentEncoded() -> Data? {
        return map { key, value in
            let escapedKey = "\(key)".addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed) ?? ""
            let escapedValue = "\(value)".addingPercentEncoding(withAllowedCharacters: .urlQueryAllowed) ?? ""
            return escapedKey + "=" + escapedValue
        }
        .joined(separator: "&")
        .data(using: .utf8)
    }
}
```

#### Biometric Authentication Integration

**What it is:** Using Touch ID, Face ID, or other biometric methods to authenticate users for network operations and secure data access.

**Core Concepts:**
- **Biometric Types**: Touch ID, Face ID, and other biometric methods
- **Local Authentication**: iOS framework for biometric verification
- **Secure Enclave**: Hardware security for biometric data storage
- **Authentication Context**: Managing authentication state and policies
- **Fallback Handling**: Alternative authentication when biometrics fail

**Why it matters:**
- Provides seamless and secure user authentication
- Eliminates need for passwords in many scenarios
- Improves user experience and app security
- Required for sensitive operations (payments, data access)
- Leverages device security features

**Biometric Capabilities:**
- **Touch ID**: Fingerprint recognition on supported devices
- **Face ID**: Facial recognition on newer devices
- **Biometric Availability**: Checking what biometrics are available
- **Authentication Policies**: Configuring authentication requirements
- **Fallback Options**: Password or PIN when biometrics unavailable

**Security Features:**
- **Secure Enclave**: Hardware-isolated biometric storage
- **Liveness Detection**: Preventing spoofing attacks
- **Authentication Context**: Managing authentication state
- **Policy Enforcement**: Requiring authentication for specific operations
- **Audit Logging**: Tracking authentication attempts

**Use Cases:**
- **Financial Apps**: Banking, payments, trading
- **Healthcare Apps**: Medical records, prescriptions
- **Enterprise Apps**: Corporate data, employee access
- **Shopping Apps**: Payment processing, order management
- **Social Apps**: Private content, direct messages

**Implementation Considerations:**
- **Availability Checking**: Verifying biometric support
- **User Enrollment**: Guiding users through biometric setup
- **Error Handling**: Managing authentication failures
- **Fallback Authentication**: Alternative authentication methods
- **User Experience**: Clear feedback and instructions

**Best Practices:**
- **Graceful Degradation**: Handle devices without biometrics
- **Clear Communication**: Explain why authentication is needed
- **Error Recovery**: Provide clear paths when authentication fails
- **User Choice**: Allow users to opt out of biometrics
- **Security Education**: Help users understand security benefits
```swift
import LocalAuthentication

class BiometricAuthManager: ObservableObject {
    @Published var biometricType: LABiometryType = .none
    @Published var isBiometricAvailable = false
    
    private let context = LAContext()
    
    init() {
        checkBiometricAvailability()
    }
    
    func checkBiometricAvailability() {
        var error: NSError?
        
        if context.canEvaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, error: &error) {
            isBiometricAvailable = true
            biometricType = context.biometryType
        } else {
            isBiometricAvailable = false
            print("Biometric authentication not available: \(error?.localizedDescription ?? "Unknown error")")
        }
    }
    
    func authenticateWithBiometrics(reason: String = "Authenticate to access secure data") async throws -> Bool {
        return try await withCheckedThrowingContinuation { continuation in
            context.evaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, localizedReason: reason) { success, error in
                if let error = error {
                    continuation.resume(throwing: BiometricAuthError.evaluationFailed(error))
                } else {
                    continuation.resume(returning: success)
                }
            }
        }
    }
    
    func authenticateForNetworkRequest() async throws -> String {
        let authenticated = try await authenticateWithBiometrics(reason: "Authenticate to make secure network request")
        
        guard authenticated else {
            throw BiometricAuthError.authenticationFailed
        }
        
        // Return secure token or proceed with network request
        return "secure_token_after_biometric_auth"
    }
}

enum BiometricAuthError: Error, LocalizedError {
    case evaluationFailed(Error)
    case authenticationFailed
    
    var errorDescription: String? {
        switch self {
        case .evaluationFailed(let error):
            return "Biometric evaluation failed: \(error.localizedDescription)"
        case .authenticationFailed:
            return "Biometric authentication failed"
        }
    }
}

// Secure network manager with biometric authentication
class SecureBiometricNetworkManager: ObservableObject {
    @Published var isLoading = false
    @Published var error: Error?
    
    private let biometricAuth = BiometricAuthManager()
    private let session = URLSession.shared
    
    func secureRequest<T: Decodable>(from url: URL) async throws -> T {
        // First authenticate with biometrics
        let _ = try await biometricAuth.authenticateForNetworkRequest()
        
        // Then proceed with secure network request
        let (data, response) = try await session.data(from: url)
        
        guard let httpResponse = response as? HTTPURLResponse,
              (200...299).contains(httpResponse.statusCode) else {
            throw NetworkError.httpError(statusCode: (response as? HTTPURLResponse)?.statusCode ?? 0)
        }
        
        return try JSONDecoder().decode(T.self, from: data)
    }
}
```

### Advanced URLSession Features

#### Custom URLProtocol Implementation
```swift
class CustomURLProtocol: URLProtocol {
    private var session: URLSession?
    private var sessionTask: URLSessionTask?
    
    override class func canInit(with request: URLRequest) -> Bool {
        // Only handle requests to our custom scheme
        return request.url?.scheme == "custom"
    }
    
    override class func canonicalRequest(for request: URLRequest) -> URLRequest {
        // Modify request if needed
        var mutableRequest = request
        mutableRequest.setValue("CustomProtocol", forHTTPHeaderField: "X-Protocol")
        return mutableRequest
    }
    
    override func startLoading() {
        guard let url = request.url else {
            client?.urlProtocolDidFinishLoading(self)
            return
        }
        
        // Create a new request with a different scheme
        var newRequest = request
        newRequest.url = URL(string: url.absoluteString.replacingOccurrences(of: "custom://", with: "https://"))
        
        let config = URLSessionConfiguration.default
        session = URLSession(configuration: config, delegate: self, delegateQueue: nil)
        
        sessionTask = session?.dataTask(with: newRequest)
        sessionTask?.resume()
    }
    
    override func stopLoading() {
        sessionTask?.cancel()
        sessionTask = nil
        session = nil
    }
}

extension CustomURLProtocol: URLSessionDataDelegate {
    func urlSession(_ session: URLSession, dataTask: URLSessionDataTask, didReceive data: Data) {
        client?.urlProtocol(self, didLoad: data)
    }
    
    func urlSession(_ session: URLSession, dataTask: URLSessionDataTask, didReceive response: URLResponse, completionHandler: @escaping (URLSession.ResponseDisposition) -> Void) {
        client?.urlProtocol(self, didReceive: response, cacheStoragePolicy: .allowed)
        completionHandler(.allow)
    }
    
    func urlSession(_ session: URLSession, task: URLSessionTask, didCompleteWithError error: Error?) {
        if let error = error {
            client?.urlProtocol(self, didFailWithError: error)
        } else {
            client?.urlProtocolDidFinishLoading(self)
        }
    }
}

// Register custom protocol
class CustomProtocolManager {
    static func registerCustomProtocol() {
        URLProtocol.registerClass(CustomURLProtocol.self)
    }
    
    static func unregisterCustomProtocol() {
        URLProtocol.unregisterClass(CustomURLProtocol.self)
    }
}
```

#### Request/Response Interceptors
```swift
protocol NetworkInterceptor {
    func interceptRequest(_ request: URLRequest) -> URLRequest
    func interceptResponse(_ response: URLResponse, data: Data) -> (URLResponse, Data)
}

class LoggingInterceptor: NetworkInterceptor {
    func interceptRequest(_ request: URLRequest) -> URLRequest {
        print("🌐 Outgoing Request: \(request.httpMethod ?? "Unknown") \(request.url?.absoluteString ?? "Unknown")")
        print("📋 Headers: \(request.allHTTPHeaderFields ?? [:])")
        
        if let body = request.httpBody {
            print("📦 Body: \(String(data: body, encoding: .utf8) ?? "Unable to decode")")
        }
        
        return request
    }
    
    func interceptResponse(_ response: URLResponse, data: Data) -> (URLResponse, Data) {
        if let httpResponse = response as? HTTPURLResponse {
            print("📥 Response: \(httpResponse.statusCode) \(httpResponse.url?.absoluteString ?? "Unknown")")
            print("📋 Response Headers: \(httpResponse.allHeaderFields)")
        }
        
        print("📦 Response Data: \(String(data: data, encoding: .utf8) ?? "Unable to decode")")
        
        return (response, data)
    }
}

class AuthenticationInterceptor: NetworkInterceptor {
    private let authManager: OAuth2Manager
    
    init(authManager: OAuth2Manager) {
        self.authManager = authManager
    }
    
    func interceptRequest(_ request: URLRequest) -> URLRequest {
        var mutableRequest = request
        
        if let token = authManager.accessToken {
            mutableRequest.setValue("Bearer \(token)", forHTTPHeaderField: "Authorization")
        }
        
        return mutableRequest
    }
    
    func interceptResponse(_ response: URLResponse, data: Data) -> (URLResponse, Data) {
        // Handle 401 responses by refreshing token
        if let httpResponse = response as? HTTPURLResponse,
           httpResponse.statusCode == 401 {
            Task {
                // Trigger token refresh
                await authManager.refreshTokenIfNeeded()
            }
        }
        
        return (response, data)
    }
}

class InterceptorNetworkManager {
    private let session = URLSession.shared
    private let interceptors: [NetworkInterceptor]
    
    init(interceptors: [NetworkInterceptor]) {
        self.interceptors = interceptors
    }
    
    func request<T: Decodable>(_ request: URLRequest) async throws -> T {
        // Apply request interceptors
        var interceptedRequest = request
        for interceptor in interceptors {
            interceptedRequest = interceptor.interceptRequest(interceptedRequest)
        }
        
        let (data, response) = try await session.data(for: interceptedRequest)
        
        // Apply response interceptors
        var interceptedResponse = response
        var interceptedData = data
        for interceptor in interceptors {
            (interceptedResponse, interceptedData) = interceptor.interceptResponse(interceptedResponse, interceptedData)
        }
        
        guard let httpResponse = interceptedResponse as? HTTPURLResponse,
              (200...299).contains(httpResponse.statusCode) else {
            throw NetworkError.httpError(statusCode: (interceptedResponse as? HTTPURLResponse)?.statusCode ?? 0)
        }
        
        return try JSONDecoder().decode(T.self, from: interceptedData)
    }
}
```

### Real-time Communication

**What it is:** Technologies and protocols that enable live, bidirectional communication between iOS apps and servers, providing real-time updates and interactive experiences.

**Core Concepts:**
- **Real-time Updates**: Live data synchronization without polling
- **Bidirectional Communication**: Two-way data exchange
- **Persistent Connections**: Long-lived connections for continuous communication
- **Event-driven Architecture**: Responding to events as they happen
- **Connection Management**: Handling connection lifecycle and failures

**Why it matters:**
- Enables engaging user experiences with live updates
- Reduces server load compared to polling approaches
- Provides immediate feedback for user actions
- Enables collaborative and interactive features
- Improves app responsiveness and user engagement

**Key Technologies:**
- **WebSockets**: Full-duplex communication over HTTP
- **Server-Sent Events**: One-way real-time updates from server
- **Push Notifications**: Background communication with users
- **Custom Protocols**: Specialized communication protocols

**Use Cases:**
- **Chat Applications**: Real-time messaging and conversations
- **Gaming**: Live game state and multiplayer interactions
- **Collaboration Tools**: Shared documents and real-time editing
- **Live Streaming**: Video and audio streaming
- **IoT Applications**: Device monitoring and control

#### WebSocket Implementation

**What it is:** A protocol that provides full-duplex communication channels over a single TCP connection, enabling real-time bidirectional communication between iOS apps and servers.

**Core Concepts:**
- **Full-Duplex Communication**: Simultaneous two-way data exchange
- **Persistent Connection**: Long-lived connection that stays open
- **Message-based Protocol**: Data sent as discrete messages
- **Connection States**: Open, connecting, closing, closed states
- **Heartbeat Mechanism**: Keeping connections alive with ping/pong

**Why it matters:**
- Enables real-time features like chat and live updates
- More efficient than HTTP polling for frequent updates
- Provides immediate feedback for user actions
- Reduces server load and network overhead
- Enables interactive and collaborative features

**WebSocket Lifecycle:**
1. **Handshake**: Initial HTTP upgrade request to WebSocket
2. **Connection**: Establishing persistent TCP connection
3. **Communication**: Bidirectional message exchange
4. **Heartbeat**: Keeping connection alive with ping/pong
5. **Closure**: Graceful connection termination

**Message Types:**
- **Text Messages**: UTF-8 encoded string data
- **Binary Messages**: Raw binary data (images, files)
- **Control Frames**: Connection management (ping, pong, close)
- **Data Frames**: Application data payload

**Connection Management:**
- **Automatic Reconnection**: Handling connection failures
- **Connection State Monitoring**: Tracking connection status
- **Error Handling**: Managing connection errors gracefully
- **Resource Cleanup**: Proper cleanup on connection closure

**Real-world examples:**
- **Chat Apps**: Real-time messaging and group conversations
- **Gaming Apps**: Live game state and multiplayer interactions
- **Collaboration Tools**: Real-time document editing
- **Live Streaming**: Video and audio streaming
- **IoT Dashboards**: Real-time device monitoring

**Best Practices:**
- **Connection Monitoring**: Track connection state and health
- **Automatic Reconnection**: Implement retry logic for failures
- **Message Queuing**: Queue messages when disconnected
- **Error Handling**: Graceful handling of connection issues
- **Resource Management**: Proper cleanup of resources
```swift
import Foundation

class WebSocketManager: NSObject, ObservableObject {
    @Published var isConnected = false
    @Published var lastMessage: String = ""
    @Published var connectionStatus: ConnectionStatus = .disconnected
    
    private var webSocket: URLSessionWebSocketTask?
    private let session: URLSession
    
    enum ConnectionStatus {
        case disconnected
        case connecting
        case connected
        case reconnecting
        case failed
    }
    
    override init() {
        let config = URLSessionConfiguration.default
        config.timeoutIntervalForRequest = 30
        config.timeoutIntervalForResource = 300
        self.session = URLSession(configuration: config, delegate: self, delegateQueue: nil)
        super.init()
    }
    
    func connect(to url: URL) {
        connectionStatus = .connecting
        
        let request = URLRequest(url: url)
        webSocket = session.webSocketTask(with: request)
        webSocket?.resume()
        
        startReceiving()
    }
    
    func disconnect() {
        webSocket?.cancel(with: .normalClosure, reason: nil)
        webSocket = nil
        connectionStatus = .disconnected
        isConnected = false
    }
    
    func send(_ message: String) {
        let webSocketMessage = URLSessionWebSocketTask.Message.string(message)
        webSocket?.send(webSocketMessage) { error in
            if let error = error {
                print("Failed to send message: \(error)")
            }
        }
    }
    
    func send(_ data: Data) {
        let webSocketMessage = URLSessionWebSocketTask.Message.data(data)
        webSocket?.send(webSocketMessage) { error in
            if let error = error {
                print("Failed to send data: \(error)")
            }
        }
    }
    
    private func startReceiving() {
        webSocket?.receive { [weak self] result in
            guard let self = self else { return }
            
            switch result {
            case .success(let message):
                DispatchQueue.main.async {
                    self.handleMessage(message)
                }
                // Continue receiving
                self.startReceiving()
                
            case .failure(let error):
                DispatchQueue.main.async {
                    self.connectionStatus = .failed
                    self.isConnected = false
                }
                print("WebSocket receive error: \(error)")
            }
        }
    }
    
    private func handleMessage(_ message: URLSessionWebSocketTask.Message) {
        switch message {
        case .string(let text):
            lastMessage = text
            print("Received text message: \(text)")
            
        case .data(let data):
            if let text = String(data: data, encoding: .utf8) {
                lastMessage = text
                print("Received data message: \(text)")
            }
            
        @unknown default:
            print("Unknown message type")
        }
    }
    
    func ping() {
        webSocket?.sendPing { error in
            if let error = error {
                print("Ping failed: \(error)")
            } else {
                print("Ping successful")
            }
        }
    }
}

extension WebSocketManager: URLSessionWebSocketDelegate {
    func urlSession(_ session: URLSession, webSocketTask: URLSessionWebSocketTask, didOpenWithProtocol protocol: String?) {
        DispatchQueue.main.async {
            self.connectionStatus = .connected
            self.isConnected = true
        }
        print("WebSocket connected with protocol: \(`protocol` ?? "none")")
    }
    
    func urlSession(_ session: URLSession, webSocketTask: URLSessionWebSocketTask, didCloseWith closeCode: URLSessionWebSocketTask.CloseCode, reason: Data?) {
        DispatchQueue.main.async {
            self.connectionStatus = .disconnected
            self.isConnected = false
        }
        
        let reasonString = reason.flatMap { String(data: $0, encoding: .utf8) } ?? "Unknown"
        print("WebSocket closed with code: \(closeCode), reason: \(reasonString)")
    }
}

// Real-time chat implementation
class ChatWebSocketManager: WebSocketManager {
    @Published var messages: [ChatMessage] = []
    
    func sendChatMessage(_ text: String, from user: String) {
        let message = ChatMessage(text: text, from: user, timestamp: Date())
        messages.append(message)
        
        let jsonData = try? JSONEncoder().encode(message)
        if let data = jsonData {
            send(data)
        }
    }
    
    override func handleMessage(_ message: URLSessionWebSocketTask.Message) {
        super.handleMessage(message)
        
        switch message {
        case .data(let data):
            if let chatMessage = try? JSONDecoder().decode(ChatMessage.self, from: data) {
                DispatchQueue.main.async {
                    self.messages.append(chatMessage)
                }
            }
        default:
            break
        }
    }
}

struct ChatMessage: Codable, Identifiable {
    let id = UUID()
    let text: String
    let from: String
    let timestamp: Date
}
```

#### Server-Sent Events (SSE) Implementation

**What it is:** A web standard that allows servers to push updates to iOS apps over HTTP connections, providing one-way real-time communication from server to client.

**Core Concepts:**
- **One-way Communication**: Server pushes data to client only
- **HTTP-based**: Uses standard HTTP protocol and infrastructure
- **Event Stream**: Continuous stream of server events
- **Automatic Reconnection**: Built-in reconnection handling
- **Event Types**: Different categories of server events

**Why it matters:**
- Simpler than WebSockets for one-way communication
- Works through firewalls and proxies
- Built-in reconnection and error handling
- Standard HTTP infrastructure support
- Efficient for server-to-client notifications

**SSE Characteristics:**
- **HTTP Long Polling**: Keeps connection open for updates
- **Event Format**: Structured event data with metadata
- **Reconnection**: Automatic reconnection on connection loss
- **Event IDs**: Tracking event order and delivery
- **Retry Logic**: Built-in retry mechanism for failed connections

**Event Structure:**
- **Event Type**: Categorizing different types of events
- **Data Payload**: Actual event data content
- **Event ID**: Unique identifier for event tracking
- **Retry Interval**: Suggested reconnection delay
- **Comments**: Server-side comments and metadata

**Use Cases:**
- **Live Notifications**: Real-time alerts and updates
- **Data Feeds**: Live data streams and analytics
- **Status Updates**: Progress tracking and status monitoring
- **News and Content**: Live content updates
- **Monitoring Dashboards**: Real-time system monitoring

**Advantages over WebSockets:**
- **Simpler Implementation**: Less complex than WebSockets
- **HTTP Compatibility**: Works with existing HTTP infrastructure
- **Automatic Reconnection**: Built-in reconnection handling
- **Event Formatting**: Structured event data format
- **Browser Support**: Native browser support

**Implementation Considerations:**
- **Connection Management**: Handling long-lived HTTP connections
- **Event Parsing**: Parsing server-sent event format
- **Reconnection Logic**: Implementing reconnection strategies
- **Error Handling**: Managing connection and parsing errors
- **Resource Cleanup**: Proper cleanup of connections
```swift
class SSEManager: NSObject, ObservableObject {
    @Published var events: [SSEEvent] = []
    @Published var isConnected = false
    
    private var session: URLSession!
    private var dataTask: URLSessionDataTask?
    
    struct SSEEvent: Identifiable {
        let id = UUID()
        let event: String
        let data: String
        let timestamp: Date
    }
    
    override init() {
        super.init()
        let config = URLSessionConfiguration.default
        config.timeoutIntervalForRequest = 0 // No timeout for SSE
        config.timeoutIntervalForResource = 0
        session = URLSession(configuration: config, delegate: self, delegateQueue: nil)
    }
    
    func connect(to url: URL) {
        var request = URLRequest(url: url)
        request.setValue("text/event-stream", forHTTPHeaderField: "Accept")
        request.setValue("no-cache", forHTTPHeaderField: "Cache-Control")
        
        dataTask = session.dataTask(with: request)
        dataTask?.resume()
        isConnected = true
    }
    
    func disconnect() {
        dataTask?.cancel()
        dataTask = nil
        isConnected = false
    }
}

extension SSEManager: URLSessionDataDelegate {
    func urlSession(_ session: URLSession, dataTask: URLSessionDataTask, didReceive data: Data) {
        guard let text = String(data: data, encoding: .utf8) else { return }
        
        let lines = text.components(separatedBy: .newlines)
        var currentEvent: String = ""
        var currentData: String = ""
        
        for line in lines {
            if line.hasPrefix("event:") {
                currentEvent = String(line.dropFirst(6)).trimmingCharacters(in: .whitespaces)
            } else if line.hasPrefix("data:") {
                currentData = String(line.dropFirst(5)).trimmingCharacters(in: .whitespaces)
            } else if line.isEmpty && !currentData.isEmpty {
                // End of event
                let event = SSEEvent(event: currentEvent, data: currentData, timestamp: Date())
                DispatchQueue.main.async {
                    self.events.append(event)
                }
                
                currentEvent = ""
                currentData = ""
            }
        }
    }
    
    func urlSession(_ session: URLSession, task: URLSessionTask, didCompleteWithError error: Error?) {
        DispatchQueue.main.async {
            self.isConnected = false
        }
        
        if let error = error {
            print("SSE connection error: \(error)")
        }
    }
}
```

#### Push Notification Handling

**What it is:** System-level notifications that allow servers to send messages to iOS apps even when they're not actively running, enabling background communication and user engagement.

**Core Concepts:**
- **Remote Notifications**: Messages sent from remote servers
- **Device Tokens**: Unique identifiers for targeting specific devices
- **Notification Payload**: Structured data sent with notifications
- **Background Processing**: Handling notifications when app is backgrounded
- **User Interaction**: Responding to user taps on notifications

**Why it matters:**
- Enables communication with users when app is not active
- Drives user engagement and app usage
- Provides real-time updates and alerts
- Enables background data synchronization
- Improves user experience and app retention

**Notification Types:**
- **Alert Notifications**: User-visible alert messages
- **Badge Notifications**: App icon badge updates
- **Sound Notifications**: Audio alerts and custom sounds
- **Silent Notifications**: Background data updates
- **Rich Notifications**: Media attachments and custom UI

**Notification Lifecycle:**
1. **Registration**: App requests notification permissions
2. **Token Generation**: System generates device token
3. **Token Exchange**: App sends token to server
4. **Notification Delivery**: Server sends notification to device
5. **User Interaction**: User responds to notification
6. **App Launch**: App launches and handles notification

**Background Processing:**
- **Background App Refresh**: Processing notifications in background
- **Silent Notifications**: Updating app data without user interaction
- **Content-available**: Triggering background processing
- **Mutable-content**: Allowing notification modification
- **Category Actions**: Custom notification actions

**Implementation Requirements:**
- **App Delegate**: Handling notification lifecycle events
- **Notification Service Extension**: Modifying notification content
- **Notification Content Extension**: Custom notification UI
- **Background Modes**: Enabling background processing
- **Push Certificates**: Server authentication and security

**Use Cases:**
- **Social Media**: New messages, likes, and comments
- **E-commerce**: Order updates, promotions, and reminders
- **News Apps**: Breaking news and content updates
- **Messaging Apps**: New message notifications
- **Utility Apps**: Reminders, alerts, and status updates

**Best Practices:**
- **Permission Management**: Clear explanation of notification benefits
- **Token Management**: Secure storage and transmission of device tokens
- **User Preferences**: Allowing users to control notification types
- **Content Relevance**: Sending valuable and timely notifications
- **Performance**: Efficient background processing and data handling
```swift
import UserNotifications
import PushKit

class PushNotificationManager: NSObject, ObservableObject {
    @Published var isRegistered = false
    @Published var deviceToken: String?
    
    override init() {
        super.init()
        requestPermissions()
    }
    
    func requestPermissions() {
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .badge, .sound]) { granted, error in
            DispatchQueue.main.async {
                self.isRegistered = granted
            }
            
            if granted {
                DispatchQueue.main.async {
                    UIApplication.shared.registerForRemoteNotifications()
                }
            }
            
            if let error = error {
                print("Notification permission error: \(error)")
            }
        }
    }
    
    func registerForPushNotifications() {
        UNUserNotificationCenter.current().getNotificationSettings { settings in
            DispatchQueue.main.async {
                self.isRegistered = settings.authorizationStatus == .authorized
            }
        }
    }
    
    func handleDeviceToken(_ deviceToken: Data) {
        let tokenParts = deviceToken.map { data in String(format: "%02.2hhx", data) }
        let token = tokenParts.joined()
        
        DispatchQueue.main.async {
            self.deviceToken = token
        }
        
        // Send token to your server
        sendTokenToServer(token)
    }
    
    private func sendTokenToServer(_ token: String) {
        guard let url = URL(string: "https://your-api.com/register-device") else { return }
        
        var request = URLRequest(url: url)
        request.httpMethod = "POST"
        request.setValue("application/json", forHTTPHeaderField: "Content-Type")
        
        let body = [
            "device_token": token,
            "platform": "ios",
            "app_version": Bundle.main.infoDictionary?["CFBundleShortVersionString"] as? String ?? "unknown"
        ]
        
        request.httpBody = try? JSONSerialization.data(withJSONObject: body)
        
        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                print("Failed to register device token: \(error)")
            } else {
                print("Device token registered successfully")
            }
        }.resume()
    }
}

// App Delegate integration
extension PushNotificationManager {
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        handleDeviceToken(deviceToken)
    }
    
    func application(_ application: UIApplication, didFailToRegisterForRemoteNotificationsWithError error: Error) {
        print("Failed to register for push notifications: \(error)")
    }
    
    func application(_ application: UIApplication, didReceiveRemoteNotification userInfo: [AnyHashable: Any], fetchCompletionHandler completionHandler: @escaping (UIBackgroundFetchResult) -> Void) {
        // Handle push notification
        handlePushNotification(userInfo)
        completionHandler(.newData)
    }
    
    private func handlePushNotification(_ userInfo: [AnyHashable: Any]) {
        // Process push notification data
        if let aps = userInfo["aps"] as? [String: Any] {
            if let alert = aps["alert"] as? String {
                print("Push notification: \(alert)")
            }
            
            if let badge = aps["badge"] as? Int {
                UIApplication.shared.applicationIconBadgeNumber = badge
            }
        }
    }
}
```

#### Background App Refresh Strategies

**What it is:** Techniques for keeping iOS apps updated with fresh data even when they're not actively being used, improving user experience and app functionality.

**Core Concepts:**
- **Background Processing**: Executing code when app is not in foreground
- **Background App Refresh**: System-managed background execution
- **Background Tasks**: Custom background execution windows
- **Data Synchronization**: Keeping app data up to date
- **Resource Management**: Efficient use of background execution time

**Why it matters:**
- Keeps app content fresh and relevant
- Improves user experience when returning to app
- Enables background data synchronization
- Reduces loading times and network requests
- Maintains app functionality in background

**Background Execution Modes:**
- **Background App Refresh**: System-managed background updates
- **Background Processing**: Custom background task execution
- **Background URLSession**: Continuing network operations
- **Background Fetch**: Periodic background data updates
- **Silent Push Notifications**: Triggering background processing

**Background Task Types:**
- **Data Synchronization**: Syncing with remote servers
- **Content Prefetching**: Loading content before user needs it
- **Cache Management**: Updating and cleaning app caches
- **Analytics and Logging**: Sending usage data and logs
- **Maintenance Tasks**: Database cleanup and optimization

**Implementation Strategies:**
- **Task Registration**: Requesting background execution time
- **Task Expiration**: Handling limited background execution time
- **Resource Management**: Efficient use of background resources
- **Error Handling**: Managing background task failures
- **User Preferences**: Respecting user background refresh settings

**System Limitations:**
- **Execution Time**: Limited background execution windows
- **Resource Constraints**: CPU, memory, and network limitations
- **User Control**: Users can disable background refresh
- **System Optimization**: iOS may limit background execution
- **Battery Impact**: Background processing affects battery life

**Best Practices:**
- **Efficient Processing**: Minimize background execution time
- **User Control**: Respect user background refresh preferences
- **Resource Management**: Efficient use of system resources
- **Error Handling**: Graceful handling of background failures
- **User Communication**: Inform users about background activities

**Real-world examples:**
- **Social Media Apps**: Refreshing feeds and notifications
- **News Apps**: Updating headlines and content
- **Email Apps**: Syncing new messages
- **Weather Apps**: Updating forecasts and alerts
- **E-commerce Apps**: Syncing orders and inventory
```swift
class BackgroundRefreshManager: ObservableObject {
    @Published var lastRefreshDate: Date?
    @Published var refreshStatus: RefreshStatus = .idle
    
    enum RefreshStatus {
        case idle
        case refreshing
        case completed
        case failed
    }
    
    func scheduleBackgroundRefresh() {
        let request = BGAppRefreshTaskRequest(identifier: "com.app.background.refresh")
        request.earliestBeginDate = Date(timeIntervalSinceNow: 15 * 60) // 15 minutes from now
        
        do {
            try BGTaskScheduler.shared.submit(request)
            print("Background refresh scheduled successfully")
        } catch {
            print("Failed to schedule background refresh: \(error)")
        }
    }
    
    func handleBackgroundRefresh(_ task: BGAppRefreshTask) {
        // Set expiration handler
        task.expirationHandler = {
            task.setTaskCompleted(success: false)
        }
        
        // Perform background work
        performBackgroundWork { [weak self] success in
            DispatchQueue.main.async {
                self?.refreshStatus = success ? .completed : .failed
                self?.lastRefreshDate = Date()
            }
            
            task.setTaskCompleted(success: success)
        }
    }
    
    private func performBackgroundWork(completion: @escaping (Bool) -> Void) {
        // Simulate background work
        DispatchQueue.global(qos: .background).async {
            // Perform actual background tasks
            let success = self.syncData()
            completion(success)
        }
    }
    
    private func syncData() -> Bool {
        // Implement your data synchronization logic
        // This could include:
        // - Fetching new data from server
        // - Updating local database
        // - Processing cached data
        
        // Simulate work
        Thread.sleep(forTimeInterval: 2.0)
        
        // Return success/failure based on actual operation
        return true
    }
}

// Background task registration in App Delegate
extension BackgroundRefreshManager {
    func registerBackgroundTasks() {
        BGTaskScheduler.shared.register(forTaskWithIdentifier: "com.app.background.refresh", using: nil) { task in
            self.handleBackgroundRefresh(task as! BGAppRefreshTask)
        }
    }
}
```

---

## Race Conditions and Prevention

**What it is:** Understanding, identifying, and preventing race conditions - situations where the outcome of concurrent operations depends on the timing of thread execution.

**Core Concepts:**
- **Race Conditions**: Unpredictable behavior due to concurrent access
- **Data Races**: Multiple threads accessing shared data simultaneously
- **Synchronization**: Coordinating access to shared resources
- **Thread Safety**: Ensuring safe concurrent access
- **Atomic Operations**: Operations that cannot be interrupted

**Why it matters:**
- Race conditions cause unpredictable app behavior
- Can lead to data corruption and app crashes
- Difficult to reproduce and debug
- Affects app reliability and user experience
- Understanding prevention is crucial for concurrent code

**Common Causes:**
- **Shared Mutable State**: Multiple threads modifying the same data
- **Unprotected Critical Sections**: Code that should be atomic
- **Order Dependencies**: Operations that must happen in specific order
- **Resource Contention**: Multiple threads competing for resources
- **Timing Issues**: Operations that depend on execution timing

**Impact on Apps:**
- **Data Corruption**: Incorrect or inconsistent data
- **App Crashes**: Unexpected exceptions and failures
- **Performance Issues**: Inefficient resource usage
- **User Experience**: Unpredictable app behavior
- **Debugging Difficulty**: Hard to reproduce and fix

### What are Race Conditions?

**Definition:** Race conditions occur when multiple threads access shared resources simultaneously, leading to unpredictable behavior and data corruption.

**Technical Explanation:**
- **Concurrent Access**: Two or more threads accessing the same resource
- **Timing Dependency**: Outcome depends on the order of execution
- **Non-deterministic**: Results vary between different runs
- **Shared State**: Multiple threads modifying the same data
- **Synchronization Failure**: Lack of proper coordination between threads

**Why They Happen:**
- **CPU Scheduling**: Operating system may switch between threads unpredictably
- **Memory Access**: Multiple threads reading/writing to same memory location
- **Resource Contention**: Threads competing for limited resources
- **Order Dependencies**: Operations that must happen in specific sequence
- **Timing Windows**: Critical sections that are too small or unprotected

**Real-world Analogy:**
Think of a race condition like two people trying to use the same bathroom at the same time. If there's no lock (synchronization), both might enter simultaneously, leading to chaos. The outcome depends on who gets there first, which is unpredictable.

**In Software Context:**
- **Bank Account**: Two transactions updating balance simultaneously
- **Counter Increment**: Multiple threads incrementing the same counter
- **Array Modification**: Adding/removing items from shared array
- **Cache Updates**: Multiple threads updating cache entries
- **Configuration Changes**: Modifying app settings from different threads

### Common Race Condition Scenarios

**What it is:** Real-world examples of race conditions that commonly occur in iOS applications, demonstrating the types of problems that can arise from concurrent access.

**Core Concepts:**
- **Shared Mutable State**: Data that can be modified by multiple threads
- **Critical Sections**: Code sections that must be executed atomically
- **Resource Contention**: Multiple threads competing for the same resources
- **Order Dependencies**: Operations that must happen in specific sequence
- **Timing Windows**: Periods when race conditions can occur

**Why it matters:**
- Understanding common scenarios helps identify potential problems
- Demonstrates the importance of proper synchronization
- Shows real-world impact of race conditions
- Helps developers recognize dangerous patterns
- Provides context for prevention strategies

**Common Patterns:**
- **Counter Operations**: Incrementing/decrementing shared counters
- **Collection Modifications**: Adding/removing items from shared collections
- **Configuration Updates**: Modifying app settings from multiple threads
- **Cache Management**: Updating shared cache data
- **Resource Allocation**: Managing limited resources across threads

#### 1. Shared Mutable State

**What it is:** Situations where multiple threads can read and modify the same data simultaneously, leading to data corruption and inconsistent results.

**Core Concepts:**
- **Mutable Data**: Data that can be changed after creation
- **Shared Access**: Multiple threads accessing the same data
- **Read-Modify-Write**: Operations that read, modify, and write data
- **Atomic Operations**: Operations that cannot be interrupted
- **Data Consistency**: Ensuring data remains valid and consistent

**Why it matters:**
- Most common cause of race conditions in iOS apps
- Can lead to data corruption and app crashes
- Difficult to detect during development
- Affects app reliability and user experience
- Understanding this pattern is crucial for prevention

**Common Examples:**
- **Counter Variables**: Incrementing/decrementing shared counters
- **Configuration Settings**: App settings modified from multiple threads
- **Cache Data**: Shared cache entries updated concurrently
- **User State**: User data modified from different operations
- **Resource Counters**: Tracking available resources across threads

**Race Condition Sequence:**
1. **Thread A** reads the current value
2. **Thread B** reads the same value (before Thread A writes)
3. **Thread A** modifies the value and writes it back
4. **Thread B** modifies the value and writes it back
5. **Result**: Thread A's changes are lost

**Real-world Impact:**
- **Banking Apps**: Incorrect account balances
- **Gaming Apps**: Wrong score calculations
- **Social Apps**: Incorrect like/comment counts
- **E-commerce Apps**: Inventory count errors
- **Utility Apps**: Configuration setting corruption

**Detection Methods:**
- **Stress Testing**: Running concurrent operations repeatedly
- **Thread Analysis**: Using Instruments to monitor thread behavior
- **Logging**: Adding detailed logging to track execution order
- **Assertions**: Adding checks for data consistency
- **Code Review**: Identifying potential race condition patterns
```swift
// ❌ DANGEROUS - Race condition prone
class UnsafeCounter {
    private var count = 0
    
    func increment() {
        count += 1 // Not atomic!
    }
    
    func getCount() -> Int {
        return count
    }
}

// Multiple threads calling increment() simultaneously can cause:
// Thread 1: reads count (0)
// Thread 2: reads count (0)
// Thread 1: adds 1, writes 1
// Thread 2: adds 1, writes 1
// Result: count = 1 (should be 2!)
```

#### 2. Array Modification

**What it is:** Race conditions that occur when multiple threads simultaneously modify collections like arrays, dictionaries, or sets, leading to data corruption and crashes.

**Core Concepts:**
- **Collection Mutability**: Collections that can be modified after creation
- **Structural Changes**: Adding, removing, or modifying collection elements
- **Index Management**: Managing collection indices during modifications
- **Capacity Changes**: Dynamic resizing of collections
- **Element References**: Managing references to collection elements

**Why it matters:**
- Collections are commonly shared across threads in iOS apps
- Array modifications can cause crashes and data corruption
- Index out of bounds errors are common race condition symptoms
- Affects data integrity and app stability
- Understanding this pattern helps prevent collection-related crashes

**Common Race Condition Scenarios:**
- **Concurrent Additions**: Multiple threads adding elements simultaneously
- **Concurrent Removals**: Multiple threads removing elements at the same time
- **Mixed Operations**: Some threads adding while others remove
- **Index Manipulation**: Modifying indices while iterating
- **Capacity Changes**: Resizing collections during modifications

**Race Condition Examples:**
1. **Index Out of Bounds**: Thread A removes element, Thread B accesses invalid index
2. **Lost Updates**: Thread A adds element, Thread B overwrites it
3. **Corrupted State**: Inconsistent collection state due to partial modifications
4. **Crash Scenarios**: Accessing elements that no longer exist
5. **Data Loss**: Elements being removed before they can be processed

**Real-world Impact:**
- **Chat Apps**: Messages being lost or duplicated
- **Social Media**: Posts or comments disappearing
- **E-commerce**: Shopping cart items being corrupted
- **Gaming Apps**: Game state becoming inconsistent
- **Utility Apps**: Settings or preferences being lost

**Detection Methods:**
- **Concurrent Access Testing**: Running multiple threads that modify collections
- **Stress Testing**: Repeatedly adding/removing elements
- **Boundary Testing**: Testing edge cases with empty/full collections
- **Logging**: Tracking collection modifications and access patterns
- **Assertions**: Checking collection state consistency

**Prevention Strategies:**
- **Synchronization**: Using locks or queues to protect collections
- **Immutable Collections**: Using immutable data structures when possible
- **Copy-on-Write**: Creating copies before modifications
- **Atomic Operations**: Using atomic collection operations
- **Thread Confinement**: Limiting collections to specific threads
```swift
// ❌ DANGEROUS - Array modification from multiple threads
class UnsafeArrayManager {
    private var items: [String] = []
    
    func addItem(_ item: String) {
        items.append(item) // Not thread-safe!
    }
    
    func removeItem(at index: Int) {
        if index < items.count {
            items.remove(at: index) // Not thread-safe!
        }
    }
}
```

### Race Condition Prevention Strategies

**What it is:** Systematic approaches to prevent race conditions by ensuring proper synchronization and coordination between concurrent operations.

**Core Concepts:**
- **Synchronization**: Coordinating access to shared resources
- **Critical Sections**: Code sections that must be executed atomically
- **Thread Safety**: Ensuring safe concurrent access to data
- **Resource Protection**: Preventing unauthorized access to shared resources
- **Atomic Operations**: Operations that cannot be interrupted

**Why it matters:**
- Prevention is better than detection and fixing
- Proper strategies eliminate race conditions entirely
- Improves app reliability and user experience
- Reduces debugging time and maintenance costs
- Enables safe concurrent programming

**Strategy Categories:**
- **Queue-based**: Using serial queues for synchronization
- **Lock-based**: Using locks and semaphores
- **Actor-based**: Using Swift actors for automatic isolation
- **Atomic**: Using hardware-level atomic operations
- **Immutable**: Using immutable data structures

**Implementation Considerations:**
- **Performance Impact**: Synchronization has overhead
- **Complexity**: Some strategies add complexity to code
- **Maintainability**: Code should remain readable and maintainable
- **Testing**: Strategies must be thoroughly tested
- **Documentation**: Thread safety guarantees must be documented

#### 1. Serial Queues for Critical Sections

**What it is:** Using serial dispatch queues to ensure that critical sections of code are executed by only one thread at a time, preventing race conditions.

**Core Concepts:**
- **Serial Execution**: Only one task can run at a time
- **Critical Sections**: Code that must be executed atomically
- **Queue Management**: Managing access to shared resources
- **Thread Safety**: Ensuring safe concurrent access
- **Performance Optimization**: Minimizing synchronization overhead

**Why it matters:**
- Simple and effective way to prevent race conditions
- Built into iOS with Grand Central Dispatch
- Minimal performance overhead compared to locks
- Easy to understand and implement
- Provides predictable execution order

**How it Works:**
1. **Queue Creation**: Create a serial queue for critical operations
2. **Task Submission**: Submit critical operations to the queue
3. **Sequential Execution**: Queue executes operations one at a time
4. **Resource Protection**: Shared resources are accessed safely
5. **Completion Handling**: Operations complete in predictable order

**Use Cases:**
- **Database Operations**: Core Data operations, SQLite access
- **File Operations**: Reading, writing, and modifying files
- **Configuration Management**: App settings and preferences
- **Cache Management**: Shared cache updates and maintenance
- **Resource Allocation**: Managing limited system resources

**Advantages:**
- **Simplicity**: Easy to implement and understand
- **Performance**: Lower overhead than traditional locks
- **Reliability**: Built-in iOS framework, well-tested
- **Debugging**: Easier to debug than complex locking schemes
- **Maintainability**: Code remains readable and maintainable

**Considerations:**
- **Performance Impact**: Serial execution can create bottlenecks
- **Queue Selection**: Choose appropriate QoS levels
- **Error Handling**: Proper error handling in critical sections
- **Testing**: Thorough testing of queue behavior
- **Documentation**: Document thread safety guarantees

**Real-world Examples:**
- **User Settings**: Modifying app configuration safely
- **Data Persistence**: Saving and loading user data
- **Cache Updates**: Managing image and data caches
- **Network State**: Tracking connection status
- **App State**: Managing app lifecycle and state
```swift
class SafeCounter {
    private var count = 0
    private let queue = DispatchQueue(label: "com.app.counter", attributes: .serial)
    
    func increment() {
        queue.sync {
            count += 1
        }
    }
    
    func getCount() -> Int {
        return queue.sync {
            return count
        }
    }
}
```

#### 2. Actors (iOS 15+)

**What it is:** Swift actors are reference types that provide automatic isolation of mutable state, ensuring thread safety without manual synchronization by automatically serializing access to their properties and methods.

**Core Concepts:**
- **Actor Isolation**: Automatic serialization of access to actor state
- **Message Passing**: Communication through method calls and property access
- **Automatic Synchronization**: No manual locks or queues needed
- **Reference Semantics**: Actors are reference types with value-like isolation
- **Async Access**: External access to actor properties is always asynchronous

**Why it matters:**
- Eliminates the need for manual synchronization
- Prevents data races automatically at compile time
- Provides compile-time safety guarantees
- Simplifies concurrent code and reduces bugs
- Modern approach that's easier to reason about

**How Actors Work:**
1. **State Isolation**: Each actor instance has isolated mutable state
2. **Message Passing**: External code communicates through method calls
3. **Serialized Execution**: Only one method can execute at a time per actor
4. **Automatic Synchronization**: Compiler ensures thread safety
5. **Async Access**: External property access is automatically async

**Use Cases:**
- **Shared Mutable State**: Managing app state across multiple threads
- **Resource Management**: Coordinating access to limited resources
- **Data Processing**: Managing data that needs to be updated concurrently
- **Configuration Management**: App settings that can be modified
- **Cache Management**: Shared caches with concurrent access

**Advantages:**
- **Compile-time Safety**: Race conditions detected at compile time
- **Automatic Synchronization**: No manual lock management needed
- **Performance**: Efficient message passing and minimal overhead
- **Maintainability**: Code is easier to read and maintain
- **Debugging**: Easier to debug than complex locking schemes

**Considerations:**
- **iOS 15+**: Requires iOS 15 or later
- **Async Access**: External property access is always async
- **Performance Overhead**: Small overhead for message passing
- **Learning Curve**: New paradigm for Swift developers
- **Migration**: May require refactoring existing code

**Real-world Examples:**
- **User Session Manager**: Managing user authentication state
- **Data Cache**: Shared cache with concurrent read/write access
- **Configuration Store**: App settings that can be modified
- **Resource Pool**: Managing limited system resources
- **Event Bus**: Coordinating events across multiple components
```swift
actor SafeArrayManager {
    private var items: [String] = []
    
    func addItem(_ item: String) {
        items.append(item)
    }
    
    func removeItem(at index: Int) {
        if index < items.count {
            items.remove(at: index)
        }
    }
    
    func getItems() -> [String] {
        return items
    }
}

// Usage
let manager = SafeArrayManager()
Task {
    await manager.addItem("Item 1")
    await manager.addItem("Item 2")
    let items = await manager.getItems()
}
```

#### 3. Locks and Semaphores

**What it is:** Traditional synchronization primitives that provide explicit control over access to shared resources, allowing developers to manually coordinate thread access and prevent race conditions.

**Core Concepts:**
- **Mutual Exclusion**: Only one thread can access a resource at a time
- **Lock Acquisition**: Threads must acquire a lock before accessing resources
- **Lock Release**: Threads must release locks to allow other threads access
- **Semaphore Counting**: Controlling access to a limited number of resources
- **Deadlock Prevention**: Avoiding situations where threads wait indefinitely

**Why it matters:**
- Provides fine-grained control over synchronization
- Useful when automatic solutions aren't appropriate
- Can be more efficient for specific use cases
- Gives developers explicit control over concurrency
- Essential for complex synchronization scenarios

**Types of Locks:**
- **NSLock**: Basic mutual exclusion lock
- **NSRecursiveLock**: Lock that can be acquired multiple times by same thread
- **NSConditionLock**: Lock with condition-based waiting
- **@synchronized**: Objective-C style synchronization directive
- **Dispatch Semaphore**: GCD-based counting semaphore

**Semaphore Types:**
- **Binary Semaphore**: Value of 0 or 1 (mutex-like behavior)
- **Counting Semaphore**: Value can be any non-negative integer
- **Resource Pool**: Managing access to limited resources
- **Producer-Consumer**: Coordinating between producers and consumers

**Use Cases:**
- **Resource Pool Management**: Limiting concurrent access to resources
- **Producer-Consumer Patterns**: Coordinating data production and consumption
- **Complex Synchronization**: When simple queues aren't sufficient
- **Performance-Critical Code**: Where manual control is needed
- **Legacy Code Integration**: Working with existing synchronization code

**Advantages:**
- **Explicit Control**: Developers have full control over synchronization
- **Performance**: Can be more efficient than queue-based approaches
- **Flexibility**: Supports complex synchronization patterns
- **Familiarity**: Traditional approach that many developers know
- **Fine-grained**: Can optimize specific synchronization scenarios

**Considerations:**
- **Complexity**: Manual lock management can be error-prone
- **Deadlock Risk**: Improper use can lead to deadlocks
- **Maintenance**: Code can become difficult to maintain
- **Debugging**: Lock-related bugs can be hard to debug
- **Testing**: Requires thorough testing of lock behavior

**Best Practices:**
- **Always Release**: Ensure locks are released in all code paths
- **Minimize Lock Scope**: Hold locks for as short a time as possible
- **Avoid Nested Locks**: Prevent deadlock scenarios
- **Use Try-Catch**: Handle exceptions properly in lock-protected code
- **Document Lock Order**: Document the order in which locks should be acquired
```swift
class SafeCounterWithLock {
    private var count = 0
    private let lock = NSLock()
    
    func increment() {
        lock.lock()
        defer { lock.unlock() }
        count += 1
    }
    
    func getCount() -> Int {
        lock.lock()
        defer { lock.unlock() }
        return count
    }
}

// Using DispatchSemaphore
class SafeCounterWithSemaphore {
    private var count = 0
    private let semaphore = DispatchSemaphore(value: 1)
    
    func increment() {
        semaphore.wait()
        defer { semaphore.signal() }
        count += 1
    }
    
    func getCount() -> Int {
        semaphore.wait()
        defer { semaphore.signal() }
        return count
    }
}
```

#### 4. Atomic Operations

**What it is:** Hardware-level operations that are guaranteed to complete without interruption, providing the most efficient way to perform simple operations on shared data without explicit synchronization.

**Core Concepts:**
- **Hardware Atomicity**: Operations that cannot be interrupted by other threads
- **Memory Barriers**: Ensuring memory operations complete in correct order
- **Compare-and-Swap**: Atomic read-modify-write operations
- **Load-Linked/Store-Conditional**: Alternative atomic operation pattern
- **Memory Ordering**: Controlling the visibility of memory operations

**Why it matters:**
- Most efficient synchronization for simple operations
- No lock overhead or context switching
- Hardware-level guarantees for correctness
- Essential for high-performance concurrent code
- Foundation for building more complex synchronization

**Types of Atomic Operations:**
- **Atomic Reads**: Guaranteed to read a complete value
- **Atomic Writes**: Guaranteed to write a complete value
- **Atomic Increment/Decrement**: Adding/subtracting values atomically
- **Compare-and-Swap**: Conditional atomic updates
- **Atomic Bit Operations**: Setting/clearing individual bits

**Hardware Support:**
- **CPU Instructions**: Native atomic instructions on modern processors
- **Memory Barriers**: Ensuring proper memory ordering
- **Cache Coherency**: Maintaining consistency across CPU cores
- **Atomic Width**: Support for different data sizes (8, 16, 32, 64 bits)
- **Platform Differences**: Varying support across different architectures

**Use Cases:**
- **Counter Operations**: Incrementing/decrementing shared counters
- **Flag Management**: Setting/clearing boolean flags
- **Reference Counting**: Managing object reference counts
- **Lock-Free Data Structures**: Building non-blocking data structures
- **Performance-Critical Code**: Where every cycle matters

**Advantages:**
- **Performance**: Fastest possible synchronization
- **Scalability**: No contention between threads
- **Efficiency**: No context switching or blocking
- **Simplicity**: Simple to use for basic operations
- **Hardware Support**: Built into modern processors

**Considerations:**
- **Limited Scope**: Only works for simple operations
- **Complexity**: Building complex operations requires careful design
- **Platform Support**: May not be available on all platforms
- **Debugging**: Atomic operations can be hard to debug
- **Memory Ordering**: Understanding memory barriers is complex

**Best Practices:**
- **Use for Simple Operations**: Atomic operations for basic read/write
- **Understand Memory Ordering**: Know when memory barriers are needed
- **Test Thoroughly**: Atomic operations require extensive testing
- **Document Assumptions**: Clearly document memory ordering requirements
- **Fallback to Locks**: Use locks for complex operations
```swift
class AtomicCounter {
    private let queue = DispatchQueue(label: "com.app.atomic", attributes: .concurrent)
    private var _count = 0
    
    var count: Int {
        get {
            return queue.sync { _count }
        }
        set {
            queue.async(flags: .barrier) {
                self._count = newValue
            }
        }
    }
    
    func increment() {
        queue.async(flags: .barrier) {
            self._count += 1
        }
    }
}
```

---

## Advanced Race Condition Scenarios

**What it is:** Complex and sophisticated race condition scenarios that go beyond basic shared state issues, involving intricate patterns of concurrent access, resource management, and system-level interactions.

**Core Concepts:**
- **Complex Patterns**: Multi-threaded interactions that are difficult to analyze
- **Resource Contention**: Multiple threads competing for limited resources
- **System-Level Issues**: Race conditions involving OS resources and APIs
- **Timing Dependencies**: Operations that depend on precise timing
- **Inter-Process Communication**: Race conditions between different processes

**Why it matters:**
- Complex race conditions are harder to detect and fix
- Can cause subtle bugs that appear intermittently
- Understanding these patterns helps prevent advanced issues
- Essential for building robust concurrent systems
- Required knowledge for production-level applications

**Common Complex Patterns:**
- **Producer-Consumer**: Coordinating data production and consumption
- **Reader-Writer Locks**: Managing concurrent read/write access
- **Deadlock Scenarios**: Multiple threads waiting for each other
- **Priority Inversion**: Lower priority tasks blocking higher priority ones
- **Resource Leaks**: Resources not properly cleaned up in concurrent code

**Detection Challenges:**
- **Intermittent Behavior**: Bugs that don't reproduce consistently
- **Timing Dependencies**: Issues that depend on execution order
- **System Load**: Problems that only appear under load
- **Debugging Complexity**: Hard to trace through concurrent execution
- **Performance Impact**: Subtle performance degradation

**Prevention Strategies:**
- **Design Patterns**: Using proven concurrent design patterns
- **Static Analysis**: Tools that detect potential race conditions
- **Testing Strategies**: Comprehensive concurrent testing approaches
- **Code Review**: Peer review focused on concurrency issues
- **Documentation**: Clear documentation of thread safety guarantees

### Complex Data Race Patterns

#### Producer-Consumer Pattern Implementation

**What it is:** A concurrent design pattern where one or more threads (producers) generate data and place it in a shared buffer, while other threads (consumers) retrieve and process that data from the buffer.

**Core Concepts:**
- **Producer Threads**: Threads that create and add data to a shared buffer
- **Consumer Threads**: Threads that retrieve and process data from the buffer
- **Shared Buffer**: Thread-safe data structure that holds items between production and consumption
- **Synchronization**: Coordinating access to the shared buffer
- **Flow Control**: Managing the rate of production vs consumption

**Why it matters:**
- Common pattern in concurrent applications
- Enables efficient parallel processing of data
- Balances work between different types of threads
- Prevents producers from overwhelming consumers
- Essential for building scalable concurrent systems

**Pattern Components:**
1. **Buffer**: Thread-safe queue or collection for storing items
2. **Producer Logic**: Code that generates and adds items to buffer
3. **Consumer Logic**: Code that retrieves and processes items
4. **Synchronization**: Locks, semaphores, or queues for thread safety
5. **Flow Control**: Mechanisms to prevent buffer overflow/underflow

**Common Use Cases:**
- **Image Processing**: Multiple threads processing images from a queue
- **Data Pipeline**: Processing data through multiple stages
- **Event Handling**: Processing events from multiple sources
- **Task Distribution**: Distributing work across worker threads
- **Stream Processing**: Processing continuous data streams

**Implementation Challenges:**
- **Buffer Management**: Preventing buffer overflow or underflow
- **Thread Coordination**: Ensuring producers and consumers work together
- **Performance**: Minimizing synchronization overhead
- **Error Handling**: Managing failures in production or consumption
- **Resource Management**: Proper cleanup of resources

**Synchronization Approaches:**
- **Semaphores**: Controlling access to buffer slots
- **Condition Variables**: Signaling when buffer state changes
- **Locks**: Protecting buffer access
- **Queues**: Using dispatch queues for coordination
- **Channels**: Using Swift concurrency channels

**Best Practices:**
- **Size Limits**: Set appropriate buffer size limits
- **Error Handling**: Handle production/consumption failures gracefully
- **Performance Monitoring**: Monitor buffer utilization and throughput
- **Resource Cleanup**: Ensure proper cleanup when shutting down
- **Testing**: Test with various production/consumption rates
```swift
class ProducerConsumerQueue<T> {
    private let queue = DispatchQueue(label: "producer.consumer", attributes: .concurrent)
    private var items: [T] = []
    private let maxCapacity: Int
    private let condition = NSCondition()
    
    init(maxCapacity: Int = 100) {
        self.maxCapacity = maxCapacity
    }
    
    func produce(_ item: T) {
        queue.async(flags: .barrier) {
            while self.items.count >= self.maxCapacity {
                // Wait for space
                self.condition.wait()
            }
            
            self.items.append(item)
            self.condition.signal() // Notify consumers
        }
    }
    
    func consume() -> T? {
        return queue.sync {
            while self.items.isEmpty {
                // Wait for items
                condition.wait()
            }
            
            let item = items.removeFirst()
            condition.signal() // Notify producers
            return item
        }
    }
    
    func consumeAll() -> [T] {
        return queue.sync {
            let allItems = items
            items.removeAll()
            condition.broadcast() // Notify all waiting threads
            return allItems
        }
    }
    
    var count: Int {
        return queue.sync { items.count }
    }
}

// Real-world usage: Image processing pipeline
class ImageProcessingPipeline {
    private let inputQueue = ProducerConsumerQueue<UIImage>(maxCapacity: 10)
    private let outputQueue = ProducerConsumerQueue<ProcessedImage>(maxCapacity: 20)
    private let processingQueue = DispatchQueue(label: "image.processing", attributes: .concurrent)
    
    func startProcessing() {
        // Start producer
        startProducer()
        
        // Start multiple consumers
        for i in 0..<3 {
            startConsumer(id: i)
        }
    }
    
    private func startProducer() {
        DispatchQueue.global(qos: .userInitiated).async {
            // Simulate producing images
            for i in 0..<100 {
                if let image = self.generateImage(number: i) {
                    self.inputQueue.produce(image)
                    Thread.sleep(forTimeInterval: 0.1)
                }
            }
        }
    }
    
    private func startConsumer(id: Int) {
        processingQueue.async {
            while true {
                if let image = self.inputQueue.consume() {
                    let processedImage = self.processImage(image)
                    self.outputQueue.produce(processedImage)
                }
            }
        }
    }
    
    private func generateImage(number: Int) -> UIImage? {
        // Simulate image generation
        return UIImage()
    }
    
    private func processImage(_ image: UIImage) -> ProcessedImage {
        // Simulate image processing
        Thread.sleep(forTimeInterval: 0.5)
        return ProcessedImage(original: image, processed: image)
    }
}

struct ProcessedImage {
    let original: UIImage
    let processed: UIImage
}
```

#### Reader-Writer Lock Implementation

**What it is:** A synchronization primitive that allows multiple threads to read shared data simultaneously while ensuring exclusive access for write operations, optimizing performance for read-heavy workloads.

**Core Concepts:**
- **Multiple Readers**: Multiple threads can read data concurrently
- **Exclusive Writers**: Only one thread can write data at a time
- **Read-Write Exclusion**: Writers block all readers and other writers
- **Read-Read Concurrency**: Readers don't block each other
- **Priority Management**: Balancing read and write access

**Why it matters:**
- Optimizes performance for read-heavy workloads
- Allows concurrent access for read operations
- Maintains data consistency during writes
- Improves scalability for shared data structures
- Common pattern in database and cache systems

**Lock States:**
- **Unlocked**: No threads are accessing the data
- **Read-Locked**: One or more threads are reading
- **Write-Locked**: One thread is writing (exclusive access)
- **Write-Pending**: Writer is waiting for readers to finish
- **Read-Pending**: Readers are waiting for writer to finish

**Implementation Approaches:**
- **Semaphore-based**: Using counting semaphores for reader count
- **Condition Variable**: Using condition variables for state changes
- **Atomic Operations**: Using atomic operations for state management
- **Dispatch Barriers**: Using GCD barriers for write operations
- **Custom Implementation**: Building specialized reader-writer locks

**Use Cases:**
- **Database Systems**: Managing concurrent database access
- **Cache Systems**: Shared caches with frequent reads
- **Configuration Data**: App settings accessed by multiple threads
- **Shared Resources**: Resources that are read more than written
- **Data Structures**: Collections accessed concurrently

**Performance Characteristics:**
- **Read Performance**: Excellent for concurrent reads
- **Write Performance**: May be slower due to reader blocking
- **Scalability**: Scales well with number of readers
- **Fairness**: May lead to writer starvation in read-heavy workloads
- **Memory Overhead**: Minimal overhead for lock management

**Best Practices:**
- **Reader Count**: Track number of active readers accurately
- **Write Priority**: Consider giving writers priority to prevent starvation
- **Timeout Handling**: Implement timeouts to prevent deadlocks
- **Performance Monitoring**: Monitor read/write ratios and performance
- **Testing**: Test with various read/write patterns

**Common Pitfalls:**
- **Writer Starvation**: Writers may wait indefinitely if reads are continuous
- **Deadlock Risk**: Improper implementation can lead to deadlocks
- **Performance Degradation**: Poor performance under write-heavy workloads
- **Complexity**: More complex than simple mutex locks
- **Debugging**: Harder to debug than simpler synchronization primitives
```swift
class ReaderWriterLock<T> {
    private let queue = DispatchQueue(label: "reader.writer", attributes: .concurrent)
    private var data: T
    private let lock = NSLock()
    
    init(initialValue: T) {
        self.data = initialValue
    }
    
    // Multiple readers can access simultaneously
    func read<U>(_ operation: (T) -> U) -> U {
        return queue.sync {
            return operation(data)
        }
    }
    
    // Writers block all other operations
    func write<U>(_ operation: (inout T) -> U) -> U {
        return queue.sync(flags: .barrier) {
            return operation(&data)
        }
    }
    
    // Atomic read-modify-write operation
    func atomicUpdate<U>(_ operation: (inout T) -> U) -> U {
        return queue.sync(flags: .barrier) {
            return operation(&data)
        }
    }
}

// Real-world usage: Configuration manager
class ConfigurationManager {
    private let configLock = ReaderWriterLock(initialValue: [String: Any]())
    
    func getValue(for key: String) -> Any? {
        return configLock.read { config in
            return config[key]
        }
    }
    
    func setValue(_ value: Any, for key: String) {
        configLock.write { config in
            config[key] = value
        }
    }
    
    func updateMultipleValues(_ updates: [String: Any]) {
        configLock.write { config in
            for (key, value) in updates {
                config[key] = value
            }
        }
    }
    
    func getAllValues() -> [String: Any] {
        return configLock.read { config in
            return config
        }
    }
}
```

#### Deadlock Detection and Prevention

**What it is:** Techniques and strategies for identifying, avoiding, and resolving deadlock situations where multiple threads are waiting indefinitely for resources held by other threads, creating a circular dependency.

**Core Concepts:**
- **Deadlock Conditions**: Four necessary conditions for deadlock to occur
- **Resource Allocation**: How resources are requested and released
- **Circular Wait**: Threads waiting in a circular chain
- **Prevention Strategies**: Techniques to avoid deadlock formation
- **Detection Methods**: Tools and techniques to identify deadlocks

**Why it matters:**
- Deadlocks cause apps to freeze and become unresponsive
- Can be difficult to reproduce and debug
- Understanding prevention is crucial for reliable concurrent code
- Affects user experience and app stability
- Essential knowledge for production systems

**Four Deadlock Conditions:**
1. **Mutual Exclusion**: Resources cannot be shared between threads
2. **Hold and Wait**: Threads hold resources while waiting for others
3. **No Preemption**: Resources cannot be forcibly taken from threads
4. **Circular Wait**: Circular chain of threads waiting for resources

**Prevention Strategies:**
- **Resource Ordering**: Always acquire resources in a consistent order
- **Timeout Mechanisms**: Implement timeouts for resource acquisition
- **Resource Preemption**: Allow resources to be taken from waiting threads
- **Single Resource**: Design to use only one resource at a time
- **Resource Pooling**: Use resource pools instead of individual resources

**Detection Methods:**
- **Resource Allocation Graph**: Visual representation of resource allocation
- **Cycle Detection**: Algorithms to detect circular dependencies
- **Timeout Monitoring**: Detecting threads that wait too long
- **Resource Tracking**: Monitoring resource allocation patterns
- **Static Analysis**: Tools that analyze code for potential deadlocks

**Common Deadlock Scenarios:**
- **Nested Locks**: Acquiring multiple locks in different orders
- **Database Transactions**: Multiple transactions waiting for locks
- **File Operations**: Multiple threads waiting for file access
- **Network Resources**: Multiple network operations waiting for connections
- **Memory Allocation**: Multiple threads waiting for memory blocks

**Best Practices:**
- **Lock Ordering**: Always acquire locks in a consistent, documented order
- **Timeout Implementation**: Set reasonable timeouts for all resource acquisitions
- **Resource Minimization**: Minimize the number of resources held simultaneously
- **Error Handling**: Implement proper error handling for resource acquisition failures
- **Testing**: Test with multiple concurrent operations to detect deadlocks

**Debugging Deadlocks:**
- **Thread Dumps**: Analyze thread states and resource holdings
- **Resource Monitoring**: Track resource allocation and release
- **Logging**: Add detailed logging for resource operations
- **Instruments**: Use Xcode Instruments to analyze thread behavior
- **Code Review**: Review code for potential deadlock patterns
```swift
class DeadlockDetector {
    private var lockGraph: [String: Set<String>] = [:]
    private let graphLock = NSLock()
    
    func acquireLock(_ lockName: String, from thread: String) -> Bool {
        graphLock.lock()
        defer { graphLock.unlock() }
        
        // Check for potential deadlock
        if wouldCauseDeadlock(lockName: lockName, from: thread) {
            print("⚠️ Potential deadlock detected! Refusing to acquire lock \(lockName)")
            return false
        }
        
        // Add edge to graph
        if lockGraph[thread] == nil {
            lockGraph[thread] = []
        }
        lockGraph[thread]?.insert(lockName)
        
        return true
    }
    
    func releaseLock(_ lockName: String, from thread: String) {
        graphLock.lock()
        defer { graphLock.unlock() }
        
        lockGraph[thread]?.remove(lockName)
        if lockGraph[thread]?.isEmpty == true {
            lockGraph.removeValue(forKey: thread)
        }
    }
    
    private func wouldCauseDeadlock(lockName: String, from thread: String) -> Bool {
        // Simple cycle detection using DFS
        var visited: Set<String> = []
        var recursionStack: Set<String> = []
        
        return hasCycle(from: thread, visited: &visited, recursionStack: &recursionStack)
    }
    
    private func hasCycle(from node: String, visited: inout Set<String>, recursionStack: inout Set<String>) -> Bool {
        if recursionStack.contains(node) {
            return true // Cycle detected
        }
        
        if visited.contains(node) {
            return false
        }
        
        visited.insert(node)
        recursionStack.insert(node)
        
        if let neighbors = lockGraph[node] {
            for neighbor in neighbors {
                if hasCycle(from: neighbor, visited: &visited, recursionStack: &recursionStack) {
                    return true
                }
            }
        }
        
        recursionStack.remove(node)
        return false
    }
}

// Safe lock manager with deadlock detection
class SafeLockManager {
    private let detector = DeadlockDetector()
    private let locks: [String: NSLock] = [:]
    private let lockAccessQueue = DispatchQueue(label: "lock.access", attributes: .serial)
    
    init(lockNames: [String]) {
        for name in lockNames {
            locks[name] = NSLock()
        }
    }
    
    func withLock<T>(_ lockName: String, operation: () -> T) -> T? {
        let threadId = String(describing: Thread.current)
        
        guard detector.acquireLock(lockName, from: threadId) else {
            return nil
        }
        
        defer {
            detector.releaseLock(lockName, from: threadId)
        }
        
        guard let lock = locks[lockName] else { return nil }
        
        lock.lock()
        defer { lock.unlock() }
        
        return operation()
    }
    
    func withMultipleLocks<T>(_ lockNames: [String], operation: () -> T) -> T? {
        // Sort locks to prevent deadlock
        let sortedLockNames = lockNames.sorted()
        
        for lockName in sortedLockNames {
            guard detector.acquireLock(lockName, from: String(describing: Thread.current)) else {
                return nil
            }
        }
        
        defer {
            for lockName in sortedLockNames {
                detector.releaseLock(lockName, from: String(describing: Thread.current))
            }
        }
        
        // Acquire all locks in order
        for lockName in sortedLockNames {
            locks[lockName]?.lock()
        }
        
        defer {
            // Release locks in reverse order
            for lockName in sortedLockNames.reversed() {
                locks[lockName]?.unlock()
            }
        }
        
        return operation()
    }
}
```

#### Priority Inversion Problems

**What it is:** A situation where a high-priority task is blocked by a lower-priority task that holds a shared resource, causing the high-priority task to wait longer than expected or indefinitely.

**Core Concepts:**
- **Task Priority**: Different levels of importance for tasks
- **Resource Contention**: Multiple tasks competing for shared resources
- **Priority Inheritance**: Temporarily raising priority of lower-priority tasks
- **Blocking Chains**: Chain of tasks blocking each other
- **Real-time Constraints**: Time-sensitive operations that must complete on schedule

**Why it matters:**
- Can cause critical tasks to miss deadlines
- Affects real-time system performance
- Difficult to detect and reproduce
- Can lead to system failures in critical applications
- Understanding helps design better priority systems

**Priority Inversion Types:**
1. **Classic Priority Inversion**: High-priority task blocked by low-priority task
2. **Bounded Priority Inversion**: Limited duration of priority inversion
3. **Unbounded Priority Inversion**: Indefinite blocking of high-priority task
4. **Chain Priority Inversion**: Multiple tasks in blocking chain
5. **Resource Priority Inversion**: Resource allocation causing priority issues

**Common Scenarios:**
- **Database Locks**: High-priority query waiting for low-priority transaction
- **File System Access**: Critical operation blocked by background file operation
- **Network Resources**: Important network request blocked by background sync
- **Memory Allocation**: Critical task waiting for memory from low-priority task
- **UI Updates**: Important UI update blocked by background processing

**Prevention Strategies:**
- **Priority Inheritance**: Temporarily raise priority of resource-holding tasks
- **Priority Ceiling**: Set minimum priority for resource access
- **Resource Preemption**: Allow high-priority tasks to preempt resources
- **Timeout Mechanisms**: Implement timeouts for resource acquisition
- **Priority Scheduling**: Use priority-aware scheduling algorithms

**Detection Methods:**
- **Priority Monitoring**: Track task priorities and execution times
- **Resource Tracking**: Monitor resource allocation and blocking
- **Performance Analysis**: Analyze task completion times
- **Real-time Monitoring**: Monitor real-time constraints and deadlines
- **Static Analysis**: Tools that analyze priority-related issues

**Real-world Examples:**
- **Audio Processing**: High-priority audio interrupted by background tasks
- **Sensor Data**: Critical sensor readings delayed by background processing
- **User Input**: Important user interactions blocked by background operations
- **System Services**: Critical system services delayed by user applications
- **Background Sync**: Important operations blocked by background synchronization

**Best Practices:**
- **Priority Design**: Carefully design priority levels for all tasks
- **Resource Management**: Implement priority-aware resource management
- **Timeout Handling**: Set appropriate timeouts for all operations
- **Monitoring**: Continuously monitor priority-related performance issues
- **Testing**: Test with various priority scenarios and system loads

**iOS-Specific Considerations:**
- **QoS Levels**: Use appropriate Quality of Service levels
- **Background Modes**: Be careful with background task priorities
- **UI Thread**: Ensure UI thread maintains appropriate priority
- **System Resources**: Respect system resource priorities
- **Battery Life**: Balance priority with battery consumption
```swift
class PriorityInversionExample {
    private let highPriorityQueue = DispatchQueue(label: "high.priority", qos: .userInitiated)
    private let lowPriorityQueue = DispatchQueue(label: "low.priority", qos: .utility)
    private let sharedResource = NSLock()
    
    func demonstratePriorityInversion() {
        // Low priority task acquires the lock
        lowPriorityQueue.async {
            print("🔵 Low priority task acquiring lock")
            self.sharedResource.lock()
            
            // Simulate long operation
            Thread.sleep(forTimeInterval: 5.0)
            
            print("🔵 Low priority task releasing lock")
            self.sharedResource.unlock()
        }
        
        // High priority task tries to acquire the lock
        highPriorityQueue.async {
            print("🔴 High priority task waiting for lock")
            self.sharedResource.lock()
            
            print("🔴 High priority task acquired lock")
            self.sharedResource.unlock()
        }
        
        // Medium priority task that doesn't need the lock
        DispatchQueue.global(qos: .default).async {
            print("🟡 Medium priority task running")
            // This task can run while high priority task is blocked
            Thread.sleep(forTimeInterval: 2.0)
            print("🟡 Medium priority task completed")
        }
    }
}

// Solution: Priority inheritance
class PriorityInheritanceLock {
    private let lock = NSLock()
    private var currentOwner: Thread?
    private var waitingThreads: [Thread] = []
    
    func lock() {
        let currentThread = Thread.current
        
        if lock.try() {
            currentOwner = currentThread
        } else {
            // Add to waiting list
            waitingThreads.append(currentThread)
            
            // Wait for lock
            lock.lock()
            
            // Remove from waiting list
            waitingThreads.removeAll { $0 == currentThread }
            currentOwner = currentThread
        }
    }
    
    func unlock() {
        guard currentOwner == Thread.current else {
            fatalError("Attempting to unlock from wrong thread")
        }
        
        currentOwner = nil
        lock.unlock()
        
        // Boost priority of waiting threads if needed
        boostWaitingThreadPriorities()
    }
    
    private func boostWaitingThreadPriorities() {
        for thread in waitingThreads {
            if thread.qualityOfService.rawValue > Thread.current.qualityOfService.rawValue {
                // Boost the waiting thread's priority
                thread.qualityOfService = Thread.current.qualityOfService
            }
        }
    }
}
```

### System-Level Concurrency

#### Core Data Concurrency

**What it is:** Managing concurrent access to Core Data managed object contexts, ensuring thread safety while maintaining data integrity and performance in iOS applications.

**Core Concepts:**
- **Managed Object Contexts**: Thread-specific containers for managed objects
- **Concurrency Types**: Different concurrency models for contexts
- **Context Coordination**: Coordinating between multiple contexts
- **Thread Confinement**: Ensuring contexts are used on correct threads
- **Data Synchronization**: Keeping data consistent across contexts

**Why it matters:**
- Core Data is fundamental to many iOS apps
- Improper concurrency can cause crashes and data corruption
- Understanding prevents common Core Data issues
- Essential for building robust data-driven applications
- Affects app performance and reliability

**Concurrency Types:**
- **Main Queue**: Context that runs on the main thread
- **Private Queue**: Context that runs on a background queue
- **Confinement**: Legacy approach with strict thread confinement
- **Nested Contexts**: Child contexts for temporary operations
- **Coordinator Sharing**: Multiple contexts sharing the same coordinator

**Context Relationships:**
1. **Parent-Child**: Child contexts inherit from parent contexts
2. **Sibling Contexts**: Independent contexts sharing the same coordinator
3. **Coordinator**: Manages the persistent store and model
4. **Store**: The actual data storage (SQLite, XML, etc.)
5. **Model**: The data model definition

**Common Patterns:**
- **Background Processing**: Using private queue contexts for heavy operations
- **UI Updates**: Using main queue context for UI-related operations
- **Batch Operations**: Using temporary contexts for bulk operations
- **Data Import**: Using dedicated contexts for data import operations
- **Search Operations**: Using background contexts for search operations

**Thread Safety Rules:**
- **Context Confinement**: Each context is tied to a specific queue
- **Object Sharing**: Objects cannot be shared between contexts
- **Queue Usage**: Contexts must be used on their designated queues
- **Object Passing**: Pass object IDs, not objects, between contexts
- **Merge Policies**: Define how changes are merged between contexts

**Best Practices:**
- **Use Main Queue for UI**: Always use main queue context for UI updates
- **Background for Heavy Work**: Use private queue contexts for heavy operations
- **Object ID Passing**: Pass object IDs between contexts, not objects
- **Proper Error Handling**: Handle Core Data errors appropriately
- **Performance Monitoring**: Monitor Core Data performance and memory usage

**Common Pitfalls:**
- **Cross-Context Object Usage**: Using objects from one context in another
- **Queue Violations**: Using contexts on wrong queues
- **Memory Issues**: Not saving or resetting contexts properly
- **Deadlocks**: Improper context coordination causing deadlocks
- **Data Inconsistency**: Not properly merging changes between contexts

**Performance Considerations:**
- **Context Lifecycle**: Create and destroy contexts appropriately
- **Batch Operations**: Use batch operations for large data sets
- **Fetch Request Optimization**: Optimize fetch requests for performance
- **Memory Management**: Monitor memory usage and context count
- **Store Coordination**: Use appropriate store coordination strategies Contexts
```swift
import CoreData

class CoreDataConcurrencyManager {
    private let persistentContainer: NSPersistentContainer
    private let mainContext: NSManagedObjectContext
    private let backgroundContext: NSManagedObjectContext
    
    init() {
        persistentContainer = NSPersistentContainer(name: "DataModel")
        persistentContainer.loadPersistentStores { _, error in
            if let error = error {
                fatalError("Core Data error: \(error)")
            }
        }
        
        mainContext = persistentContainer.viewContext
        mainContext.automaticallyMergesChangesFromParent = true
        
        backgroundContext = persistentContainer.newBackgroundContext()
        backgroundContext.mergePolicy = NSMergeByPropertyObjectTrumpMergePolicy
    }
    
    // Main context for UI updates
    func performOnMainContext<T>(_ operation: @escaping (NSManagedObjectContext) -> T) async -> T {
        return await MainActor.run {
            return operation(mainContext)
        }
    }
    
    // Background context for heavy operations
    func performOnBackgroundContext<T>(_ operation: @escaping (NSManagedObjectContext) -> T) async throws -> T {
        return try await withCheckedThrowingContinuation { continuation in
            backgroundContext.perform {
                do {
                    let result = operation(self.backgroundContext)
                    
                    if self.backgroundContext.hasChanges {
                        try self.backgroundContext.save()
                    }
                    
                    continuation.resume(returning: result)
                } catch {
                    continuation.resume(throwing: error)
                }
            }
        }
    }
    
    // Batch operations
    func performBatchOperation(_ operation: @escaping (NSManagedObjectContext) -> Void) async throws {
        try await withCheckedThrowingContinuation { continuation in
            backgroundContext.perform {
                do {
                    operation(self.backgroundContext)
                    
                    if self.backgroundContext.hasChanges {
                        try self.backgroundContext.save()
                    }
                    
                    continuation.resume()
                } catch {
                    continuation.resume(throwing: error)
                }
            }
        }
    }
    
    // Fetch with proper concurrency
    func fetch<T: NSManagedObject>(_ request: NSFetchRequest<T>) async throws -> [T] {
        return try await performOnBackgroundContext { context in
            return try context.fetch(request)
        }
    }
    
    // Save with proper concurrency
    func save() async throws {
        try await performOnMainContext { context in
            if context.hasChanges {
                try context.save()
            }
        }
    }
}

// Usage example
class UserManager {
    private let coreDataManager = CoreDataConcurrencyManager()
    
    func createUser(name: String, email: String) async throws {
        try await coreDataManager.performOnBackgroundContext { context in
            let user = User(context: context)
            user.name = name
            user.email = email
            user.createdAt = Date()
        }
    }
    
    func fetchUsers() async throws -> [User] {
        let request: NSFetchRequest<User> = User.fetchRequest()
        request.sortDescriptors = [NSSortDescriptor(key: "createdAt", ascending: false)]
        
        return try await coreDataManager.fetch(request)
    }
    
    func updateUser(_ user: User, with updates: [String: Any]) async throws {
        try await coreDataManager.performOnBackgroundContext { context in
            let userInContext = context.object(with: user.objectID) as! User
            
            for (key, value) in updates {
                userInContext.setValue(value, forKey: key)
            }
            
            userInContext.updatedAt = Date()
        }
    }
}
```

#### File System Operations
```swift
class FileSystemManager {
    private let fileQueue = DispatchQueue(label: "file.system", attributes: .concurrent)
    private let fileCoordinator = NSFileCoordinator()
    
    // Thread-safe file reading
    func readFile(at url: URL) async throws -> Data {
        return try await withCheckedThrowingContinuation { continuation in
            fileQueue.async {
                var error: NSError?
                var data: Data?
                
                self.fileCoordinator.coordinate(readingItemAt: url, options: .withoutChanges, error: &error) { url in
                    data = try? Data(contentsOf: url)
                }
                
                if let error = error {
                    continuation.resume(throwing: error)
                } else if let data = data {
                    continuation.resume(returning: data)
                } else {
                    continuation.resume(throwing: FileSystemError.readFailed)
                }
            }
        }
    }
    
    // Thread-safe file writing
    func writeFile(_ data: Data, to url: URL) async throws {
        try await withCheckedThrowingContinuation { continuation in
            fileQueue.async {
                var error: NSError?
                
                self.fileCoordinator.coordinate(writingItemAt: url, options: .forDeleting, error: &error) { url in
                    try data.write(to: url)
                }
                
                if let error = error {
                    continuation.resume(throwing: error)
                } else {
                    continuation.resume()
                }
            }
        }
    }
    
    // Thread-safe directory operations
    func createDirectory(at url: URL) async throws {
        try await withCheckedThrowingContinuation { continuation in
            fileQueue.async {
                var error: NSError?
                
                self.fileCoordinator.coordinate(writingItemAt: url, options: .forMoving, error: &error) { url in
                    try FileManager.default.createDirectory(at: url, withIntermediateDirectories: true)
                }
                
                if let error = error {
                    continuation.resume(throwing: error)
                } else {
                    continuation.resume()
                }
            }
        }
    }
    
    // Batch file operations
    func performBatchFileOperations(_ operations: [FileOperation]) async throws {
        try await withThrowingTaskGroup(of: Void.self) { group in
            for operation in operations {
                group.addTask {
                    try await self.performFileOperation(operation)
                }
            }
            
            try await group.waitForAll()
        }
    }
    
    private func performFileOperation(_ operation: FileOperation) async throws {
        switch operation {
        case .read(let url):
            _ = try await readFile(at: url)
            
        case .write(let data, let url):
            try await writeFile(data, to: url)
            
        case .delete(let url):
            try await deleteFile(at: url)
            
        case .move(let from, let to):
            try await moveFile(from: from, to: to)
        }
    }
    
    private func deleteFile(at url: URL) async throws {
        try await withCheckedThrowingContinuation { continuation in
            fileQueue.async {
                var error: NSError?
                
                self.fileCoordinator.coordinate(writingItemAt: url, options: .forDeleting, error: &error) { url in
                    try FileManager.default.removeItem(at: url)
                }
                
                if let error = error {
                    continuation.resume(throwing: error)
                } else {
                    continuation.resume()
                }
            }
        }
    }
    
    private func moveFile(from source: URL, to destination: URL) async throws {
        try await withCheckedThrowingContinuation { continuation in
            fileQueue.async {
                var error: NSError?
                
                self.fileCoordinator.coordinate(writingItemAt: source, options: .forMoving, error: &error) { source in
                    try FileManager.default.moveItem(at: source, to: destination)
                }
                
                if let error = error {
                    continuation.resume(throwing: error)
                } else {
                    continuation.resume()
                }
            }
        }
    }
}

enum FileOperation {
    case read(URL)
    case write(Data, URL)
    case delete(URL)
    case move(URL, URL)
}

enum FileSystemError: Error {
    case readFailed
    case writeFailed
    case deleteFailed
    case moveFailed
}
```

#### Hardware Resource Sharing
```swift
class HardwareResourceManager {
    private let resourceQueue = DispatchQueue(label: "hardware.resource", attributes: .serial)
    private var availableResources: Set<HardwareResource> = []
    private var resourceAllocations: [String: HardwareResource] = [:]
    
    enum HardwareResource: String, CaseIterable {
        case camera = "Camera"
        case microphone = "Microphone"
        case location = "Location"
        case bluetooth = "Bluetooth"
        case wifi = "WiFi"
    }
    
    func requestResource(_ resource: HardwareResource, for taskId: String) async -> Bool {
        return await withCheckedContinuation { continuation in
            resourceQueue.async {
                if self.availableResources.contains(resource) {
                    self.availableResources.remove(resource)
                    self.resourceAllocations[taskId] = resource
                    continuation.resume(returning: true)
                } else {
                    continuation.resume(returning: false)
                }
            }
        }
    }
    
    func releaseResource(for taskId: String) {
        resourceQueue.async {
            if let resource = self.resourceAllocations.removeValue(forKey: taskId) {
                self.availableResources.insert(resource)
            }
        }
    }
    
    func getAvailableResources() -> Set<HardwareResource> {
        return resourceQueue.sync {
            return availableResources
        }
    }
    
    func initializeResources() {
        resourceQueue.async {
            self.availableResources = Set(HardwareResource.allCases)
        }
    }
}

// Camera resource management example
class CameraManager {
    private let resourceManager = HardwareResourceManager()
    private let cameraQueue = DispatchQueue(label: "camera.operations", attributes: .serial)
    
    func startCameraSession() async throws -> Bool {
        let taskId = UUID().uuidString
        
        guard await resourceManager.requestResource(.camera, for: taskId) else {
            throw CameraError.resourceUnavailable
        }
        
        defer {
            resourceManager.releaseResource(for: taskId)
        }
        
        return try await withCheckedThrowingContinuation { continuation in
            cameraQueue.async {
                // Initialize camera
                let success = self.initializeCamera()
                continuation.resume(returning: success)
            }
        }
    }
    
    private func initializeCamera() -> Bool {
        // Camera initialization logic
        Thread.sleep(forTimeInterval: 1.0)
        return true
    }
}

enum CameraError: Error {
    case resourceUnavailable
    case initializationFailed
}
```

#### Inter-Process Communication
```swift
class InterProcessCommunicationManager {
    private let messageQueue = DispatchQueue(label: "ipc.message", attributes: .serial)
    private let sharedDefaults = UserDefaults(suiteName: "group.com.app.shared")
    
    // Shared UserDefaults for simple data sharing
    func shareData(_ data: [String: Any], withKey key: String) {
        messageQueue.async {
            self.sharedDefaults?.set(data, forKey: key)
            self.sharedDefaults?.synchronize()
        }
    }
    
    func getSharedData(forKey key: String) -> [String: Any]? {
        return messageQueue.sync {
            return sharedDefaults?.object(forKey: key) as? [String: Any]
        }
    }
    
    // File-based IPC
    func writeToSharedFile(_ data: Data, filename: String) async throws {
        guard let containerURL = FileManager.default.containerURL(forSecurityApplicationGroupIdentifier: "group.com.app.shared") else {
            throw IPCError.containerUnavailable
        }
        
        let fileURL = containerURL.appendingPathComponent(filename)
        
        try await withCheckedThrowingContinuation { continuation in
            messageQueue.async {
                do {
                    try data.write(to: fileURL)
                    continuation.resume()
                } catch {
                    continuation.resume(throwing: error)
                }
            }
        }
    }
    
    func readFromSharedFile(filename: String) async throws -> Data {
        guard let containerURL = FileManager.default.containerURL(forSecurityApplicationGroupIdentifier: "group.com.app.shared") else {
            throw IPCError.containerUnavailable
        }
        
        let fileURL = containerURL.appendingPathComponent(filename)
        
        return try await withCheckedThrowingContinuation { continuation in
            messageQueue.async {
                do {
                    let data = try Data(contentsOf: fileURL)
                    continuation.resume(returning: data)
                } catch {
                    continuation.resume(throwing: error)
                }
            }
        }
    }
    
    // Notification-based IPC
    func postNotification(name: String, userInfo: [String: Any]? = nil) {
        messageQueue.async {
            NotificationCenter.default.post(name: NSNotification.Name(name), object: nil, userInfo: userInfo)
        }
    }
    
    func observeNotification(name: String, handler: @escaping ([String: Any]?) -> Void) -> NSObjectProtocol {
        return NotificationCenter.default.addObserver(forName: NSNotification.Name(name), object: nil, queue: nil) { notification in
            handler(notification.userInfo)
        }
    }
}

enum IPCError: Error {
    case containerUnavailable
    case readFailed
    case writeFailed
}

// Usage example for app extension communication
class AppExtensionCommunicator {
    private let ipcManager = InterProcessCommunicationManager()
    
    func sendDataToExtension(_ data: [String: Any]) {
        ipcManager.shareData(data, withKey: "extension_data")
        ipcManager.postNotification(name: "DataUpdated", userInfo: data)
    }
    
    func receiveDataFromExtension() -> [String: Any]? {
        return ipcManager.getSharedData(forKey: "extension_data")
    }
    
    func setupExtensionObserver(handler: @escaping ([String: Any]?) -> Void) -> NSObjectProtocol {
        return ipcManager.observeNotification(name: "ExtensionDataUpdated") { userInfo in
            handler(userInfo)
        }
    }
}
```

---

## Real-World Examples and Use Cases

### 1. Image Loading and Caching
```swift
actor ImageCache {
    private var cache: [String: UIImage] = [:]
    private let maxCacheSize = 100
    
    func getImage(for key: String) -> UIImage? {
        return cache[key]
    }
    
    func setImage(_ image: UIImage, for key: String) {
        if cache.count >= maxCacheSize {
            // Remove oldest entries
            let oldestKeys = Array(cache.keys.prefix(cache.count - maxCacheSize + 1))
            for key in oldestKeys {
                cache.removeValue(forKey: key)
            }
        }
        cache[key] = image
    }
}

class ImageLoader: ObservableObject {
    @Published var image: UIImage?
    private let cache = ImageCache()
    private let session = URLSession.shared
    
    func loadImage(from url: URL) async {
        // Check cache first
        if let cachedImage = await cache.getImage(for: url.absoluteString) {
            await MainActor.run {
                self.image = cachedImage
            }
            return
        }
        
        // Download image
        do {
            let (data, _) = try await session.data(from: url)
            if let downloadedImage = UIImage(data: data) {
                await cache.setImage(downloadedImage, for: url.absoluteString)
                await MainActor.run {
                    self.image = downloadedImage
                }
            }
        } catch {
            print("Error loading image: \(error)")
        }
    }
}
```

### 2. Database Operations
```swift
actor DatabaseManager {
    private let queue = DispatchQueue(label: "com.app.database", attributes: .serial)
    private var database: Database?
    
    func initialize() async {
        await withCheckedContinuation { continuation in
            queue.async {
                // Initialize database
                self.database = Database()
                continuation.resume()
            }
        }
    }
    
    func saveUser(_ user: User) async throws {
        try await withCheckedThrowingContinuation { continuation in
            queue.async {
                do {
                    try self.database?.save(user)
                    continuation.resume()
                } catch {
                    continuation.resume(throwing: error)
                }
            }
        }
    }
    
    func fetchUsers() async throws -> [User] {
        try await withCheckedThrowingContinuation { continuation in
            queue.async {
                do {
                    let users = try self.database?.fetchUsers() ?? []
                    continuation.resume(returning: users)
                } catch {
                    continuation.resume(throwing: error)
                }
            }
        }
    }
}
```

### 3. Network Request Manager
```swift
class NetworkRequestManager: ObservableObject {
    @Published var isLoading = false
    @Published var error: Error?
    
    private let session = URLSession.shared
    private var activeTasks: [URLSessionTask] = []
    private let queue = DispatchQueue(label: "com.app.network", attributes: .concurrent)
    
    func performRequest<T: Decodable>(_ request: URLRequest) async throws -> T {
        await MainActor.run {
            self.isLoading = true
            self.error = nil
        }
        
        defer {
            Task { @MainActor in
                self.isLoading = false
            }
        }
        
        do {
            let (data, response) = try await session.data(for: request)
            
            guard let httpResponse = response as? HTTPURLResponse else {
                throw NetworkError.invalidResponse
            }
            
            guard (200...299).contains(httpResponse.statusCode) else {
                throw NetworkError.httpError(statusCode: httpResponse.statusCode)
            }
            
            return try JSONDecoder().decode(T.self, from: data)
        } catch {
            await MainActor.run {
                self.error = error
            }
            throw error
        }
    }
    
    func cancelAllRequests() {
        queue.async {
            self.activeTasks.forEach { $0.cancel() }
            self.activeTasks.removeAll()
        }
    }
}
```

### 4. Real-Time Data Synchronization
```swift
actor DataSynchronizer {
    private var lastSyncTime: Date = Date.distantPast
    private let syncInterval: TimeInterval = 300 // 5 minutes
    private var isSyncing = false
    
    func shouldSync() -> Bool {
        return Date().timeIntervalSince(lastSyncTime) >= syncInterval
    }
    
    func syncIfNeeded() async throws {
        guard shouldSync() && !isSyncing else { return }
        
        isSyncing = true
        defer { isSyncing = false }
        
        // Perform sync operations
        try await performSync()
        lastSyncTime = Date()
    }
    
    private func performSync() async throws {
        // Simulate sync operations
        try await Task.sleep(nanoseconds: 2_000_000_000) // 2 seconds
    }
}
```

---

## Best Practices and Performance

### 1. Queue Selection Guidelines

#### When to Use Each Queue Type:
- **Main Queue**: UI updates, user interactions
- **Global User Interactive**: Animations, user input processing
- **Global User Initiated**: User-triggered operations
- **Global Default**: General purpose tasks
- **Global Utility**: Background processing, I/O operations
- **Global Background**: Maintenance, cleanup tasks
- **Custom Serial**: Critical sections, ordered operations
- **Custom Concurrent**: Independent parallel operations

### 2. Performance Optimization

#### Task Prioritization
```swift
// High priority for user actions
DispatchQueue.global(qos: .userInitiated).async {
    processUserAction()
}

// Lower priority for background tasks
DispatchQueue.global(qos: .utility).async {
    performBackgroundCleanup()
}
```

#### Memory Management
```swift
class MemoryEfficientManager {
    private let queue = DispatchQueue(label: "com.app.memory", attributes: .serial)
    private var cache: [String: Any] = [:]
    
    func cleanupCache() {
        queue.async {
            // Remove old entries to prevent memory leaks
            let cutoffDate = Date().addingTimeInterval(-3600) // 1 hour ago
            self.cache = self.cache.filter { key, value in
                // Keep only recent entries
                return true // Simplified for example
            }
        }
    }
}
```

### 3. Error Handling and Recovery

#### Comprehensive Error Handling
```swift
enum ConcurrencyError: Error, LocalizedError {
    case timeout
    case cancelled
    case resourceUnavailable
    case deadlock
    
    var errorDescription: String? {
        switch self {
        case .timeout:
            return "Operation timed out"
        case .cancelled:
            return "Operation was cancelled"
        case .resourceUnavailable:
            return "Required resource is unavailable"
        case .deadlock:
            return "Deadlock detected"
        }
    }
}

func performOperationWithTimeout<T>(_ operation: @escaping () async throws -> T,
                                  timeout: TimeInterval) async throws -> T {
    try await withThrowingTaskGroup(of: T.self) { group in
        group.addTask {
            try await operation()
        }
        
        group.addTask {
            try await Task.sleep(nanoseconds: UInt64(timeout * 1_000_000_000))
            throw ConcurrencyError.timeout
        }
        
        let result = try await group.next()!
        group.cancelAll()
        return result
    }
}
```

### 4. Testing Concurrency

#### Unit Testing Async Code
```swift
import XCTest

class ConcurrencyTests: XCTestCase {
    func testAsyncOperation() async throws {
        let expectation = XCTestExpectation(description: "Async operation completed")
        
        Task {
            let result = try await performAsyncOperation()
            XCTAssertNotNil(result)
            expectation.fulfill()
        }
        
        await fulfillment(of: [expectation], timeout: 5.0)
    }
    
    func testRaceCondition() async throws {
        let counter = SafeCounter()
        let iterations = 1000
        
        await withThrowingTaskGroup(of: Void.self) { group in
            for _ in 0..<iterations {
                group.addTask {
                    await counter.increment()
                }
            }
        }
        
        let finalCount = await counter.getCount()
        XCTAssertEqual(finalCount, iterations)
    }
}
```

---

## Summary and Key Takeaways

### Concurrency Best Practices:
1. **Always use MainActor for UI updates**
2. **Choose appropriate QoS levels for tasks**
3. **Use actors for shared mutable state**
4. **Implement proper error handling and recovery**
5. **Test race conditions thoroughly**
6. **Monitor performance and memory usage**

### Networking Best Practices:
1. **Implement retry logic with exponential backoff**
2. **Use background URLSession for large downloads**
3. **Implement proper request cancellation**
4. **Cache responses when appropriate**
5. **Handle network connectivity changes**

### Race Condition Prevention:
1. **Use serial queues for critical sections**
2. **Implement actors for complex state management**
3. **Use atomic operations when possible**
4. **Test with multiple concurrent operations**
5. **Document thread safety guarantees**

This comprehensive guide covers the essential aspects of Swift concurrency, networking, and race condition prevention in modern iOS development. The examples provided demonstrate real-world scenarios and best practices that you can apply to your projects.

---

## Production-Ready Patterns

### Error Recovery & Resilience

#### Circuit Breaker Pattern Implementation
```swift
enum CircuitBreakerState {
    case closed      // Normal operation
    case open        // Failing, reject requests
    case halfOpen    // Testing if service is recovered
}

class CircuitBreaker {
    private let failureThreshold: Int
    private let recoveryTimeout: TimeInterval
    private let expectedFailureRate: Double
    
    private var state: CircuitBreakerState = .closed
    private var failureCount = 0
    private var lastFailureTime: Date?
    private let stateQueue = DispatchQueue(label: "circuit.breaker", attributes: .serial)
    
    init(failureThreshold: Int = 5, recoveryTimeout: TimeInterval = 60, expectedFailureRate: Double = 0.5) {
        self.failureThreshold = failureThreshold
        self.recoveryTimeout = recoveryTimeout
        self.expectedFailureRate = expectedFailureRate
    }
    
    func execute<T>(_ operation: @escaping () async throws -> T) async throws -> T {
        return try await withCheckedThrowingContinuation { continuation in
            stateQueue.async {
                switch self.state {
                case .closed:
                    self.executeOperation(operation, continuation: continuation)
                    
                case .open:
                    if self.shouldAttemptReset() {
                        self.state = .halfOpen
                        self.executeOperation(operation, continuation: continuation)
                    } else {
                        continuation.resume(throwing: CircuitBreakerError.serviceUnavailable)
                    }
                    
                case .halfOpen:
                    self.executeOperation(operation, continuation: continuation)
                }
            }
        }
    }
    
    private func executeOperation<T>(_ operation: @escaping () async throws -> T, continuation: CheckedContinuation<T, Error>) {
        Task {
            do {
                let result = try await operation()
                self.recordSuccess()
                continuation.resume(returning: result)
            } catch {
                self.recordFailure()
                continuation.resume(throwing: error)
            }
        }
    }
    
    private func recordSuccess() {
        stateQueue.async {
            self.failureCount = 0
            self.lastFailureTime = nil
            
            if self.state == .halfOpen {
                self.state = .closed
            }
        }
    }
    
    private func recordFailure() {
        stateQueue.async {
            self.failureCount += 1
            self.lastFailureTime = Date()
            
            if self.failureCount >= self.failureThreshold {
                self.state = .open
            }
        }
    }
    
    private func shouldAttemptReset() -> Bool {
        guard let lastFailure = lastFailureTime else { return false }
        return Date().timeIntervalSince(lastFailure) >= recoveryTimeout
    }
    
    func getStatus() -> CircuitBreakerState {
        return stateQueue.sync { state }
    }
    
    func forceOpen() {
        stateQueue.async {
            self.state = .open
        }
    }
    
    func forceClose() {
        stateQueue.async {
            self.state = .closed
            self.failureCount = 0
            self.lastFailureTime = nil
        }
    }
}

enum CircuitBreakerError: Error {
    case serviceUnavailable
}

// Usage with network requests
class ResilientNetworkManager {
    private let circuitBreaker = CircuitBreaker()
    private let session = URLSession.shared
    
    func resilientRequest<T: Decodable>(from url: URL) async throws -> T {
        return try await circuitBreaker.execute {
            let (data, response) = try await self.session.data(from: url)
            
            guard let httpResponse = response as? HTTPURLResponse,
                  (200...299).contains(httpResponse.statusCode) else {
                throw NetworkError.httpError(statusCode: (response as? HTTPURLResponse)?.statusCode ?? 0)
            }
            
            return try JSONDecoder().decode(T.self, from: data)
        }
    }
}
```

#### Graceful Degradation Strategies
```swift
class GracefulDegradationManager: ObservableObject {
    @Published var currentMode: DegradationMode = .full
    @Published var availableFeatures: Set<Feature> = []
    
    enum DegradationMode {
        case full           // All features available
        case limited        // Limited functionality
        case offline        // Offline mode only
        case emergency      // Critical features only
    }
    
    enum Feature: String, CaseIterable {
        case realTimeSync = "Real-time Sync"
        case imageProcessing = "Image Processing"
        case advancedSearch = "Advanced Search"
        case pushNotifications = "Push Notifications"
        case cloudStorage = "Cloud Storage"
        case socialFeatures = "Social Features"
    }
    
    private let networkMonitor = NetworkMonitor()
    private let performanceMonitor = PerformanceMonitor()
    
    init() {
        setupMonitoring()
        updateAvailableFeatures()
    }
    
    private func setupMonitoring() {
        networkMonitor.connectionStatusChanged = { [weak self] status in
            self?.handleNetworkStatusChange(status)
        }
        
        performanceMonitor.performanceDegraded = { [weak self] in
            self?.handlePerformanceDegradation()
        }
    }
    
    private func handleNetworkStatusChange(_ status: NetworkStatus) {
        DispatchQueue.main.async {
            switch status {
            case .connected:
                self.currentMode = .full
            case .limited:
                self.currentMode = .limited
            case .disconnected:
                self.currentMode = .offline
            }
            self.updateAvailableFeatures()
        }
    }
    
    private func handlePerformanceDegradation() {
        DispatchQueue.main.async {
            if self.currentMode == .full {
                self.currentMode = .limited
            } else if self.currentMode == .limited {
                self.currentMode = .emergency
            }
            self.updateAvailableFeatures()
        }
    }
    
    private func updateAvailableFeatures() {
        var features: Set<Feature> = []
        
        switch currentMode {
        case .full:
            features = Set(Feature.allCases)
            
        case .limited:
            features = [.realTimeSync, .imageProcessing, .advancedSearch, .pushNotifications]
            
        case .offline:
            features = [.imageProcessing, .advancedSearch]
            
        case .emergency:
            features = [.advancedSearch]
        }
        
        availableFeatures = features
    }
    
    func isFeatureAvailable(_ feature: Feature) -> Bool {
        return availableFeatures.contains(feature)
    }
    
    func executeWithDegradation<T>(_ operation: @escaping () async throws -> T, fallback: @escaping () async throws -> T) async throws -> T {
        do {
            return try await operation()
        } catch {
            // Try fallback operation
            return try await fallback()
        }
    }
}

enum NetworkStatus {
    case connected
    case limited
    case disconnected
}

class NetworkMonitor {
    var connectionStatusChanged: ((NetworkStatus) -> Void)?
    
    init() {
        // Monitor network status changes
        // Implementation would use Network framework
    }
}

class PerformanceMonitor {
    var performanceDegraded: (() -> Void)?
    
    init() {
        // Monitor app performance
        // Implementation would track CPU, memory, battery usage
    }
}
```

#### Offline-First Architecture
```swift
class OfflineFirstManager: ObservableObject {
    @Published var isOnline = false
    @Published var pendingOperations: [PendingOperation] = []
    @Published var syncStatus: SyncStatus = .idle
    
    enum SyncStatus {
        case idle
        case syncing
        case completed
        case failed
    }
    
    private let localDatabase: LocalDatabase
    private let networkManager: NetworkManager
    private let operationQueue = DispatchQueue(label: "offline.operations", attributes: .serial)
    
    init(localDatabase: LocalDatabase, networkManager: NetworkManager) {
        self.localDatabase = localDatabase
        self.networkManager = networkManager
        setupNetworkMonitoring()
    }
    
    private func setupNetworkMonitoring() {
        // Monitor network connectivity
        // When online, sync pending operations
    }
    
    // Create operation (always succeeds locally)
    func createItem<T: Codable>(_ item: T, type: String) async throws -> String {
        let operationId = UUID().uuidString
        
        // Save locally first
        try await localDatabase.save(item, withId: operationId)
        
        // Queue for sync
        let operation = PendingOperation(
            id: operationId,
            type: .create,
            entityType: type,
            data: try JSONEncoder().encode(item),
            timestamp: Date()
        )
        
        await queueOperation(operation)
        
        return operationId
    }
    
    // Update operation
    func updateItem<T: Codable>(_ item: T, withId id: String, type: String) async throws {
        // Update locally first
        try await localDatabase.update(item, withId: id)
        
        // Queue for sync
        let operation = PendingOperation(
            id: id,
            type: .update,
            entityType: type,
            data: try JSONEncoder().encode(item),
            timestamp: Date()
        )
        
        await queueOperation(operation)
    }
    
    // Delete operation
    func deleteItem(withId id: String, type: String) async throws {
        // Delete locally first
        try await localDatabase.delete(withId: id)
        
        // Queue for sync
        let operation = PendingOperation(
            id: id,
            type: .delete,
            entityType: type,
            data: nil,
            timestamp: Date()
        )
        
        await queueOperation(operation)
    }
    
    private func queueOperation(_ operation: PendingOperation) async {
        await MainActor.run {
            pendingOperations.append(operation)
        }
        
        // Try to sync immediately if online
        if isOnline {
            await syncPendingOperations()
        }
    }
    
    func syncPendingOperations() async {
        guard isOnline else { return }
        
        await MainActor.run {
            syncStatus = .syncing
        }
        
        do {
            let operations = await MainActor.run { pendingOperations }
            
            for operation in operations {
                try await syncOperation(operation)
                
                // Remove from pending after successful sync
                await MainActor.run {
                    pendingOperations.removeAll { $0.id == operation.id }
                }
            }
            
            await MainActor.run {
                syncStatus = .completed
            }
        } catch {
            await MainActor.run {
                syncStatus = .failed
            }
            print("Sync failed: \(error)")
        }
    }
    
    private func syncOperation(_ operation: PendingOperation) async throws {
        switch operation.type {
        case .create:
            try await networkManager.createItem(operation.entityType, data: operation.data)
            
        case .update:
            try await networkManager.updateItem(operation.entityType, id: operation.id, data: operation.data)
            
        case .delete:
            try await networkManager.deleteItem(operation.entityType, id: operation.id)
        }
    }
    
    // Fetch data (always from local first, then sync if online)
    func fetchItems<T: Codable>(type: String) async throws -> [T] {
        // Get from local database
        let localItems: [T] = try await localDatabase.fetchAll(type: type)
        
        // If online, sync in background
        if isOnline {
            Task.detached(priority: .background) {
                await self.syncPendingOperations()
            }
        }
        
        return localItems
    }
}

struct PendingOperation: Identifiable, Codable {
    let id: String
    let type: OperationType
    let entityType: String
    let data: Data?
    let timestamp: Date
    
    enum OperationType: String, Codable {
        case create
        case update
        case delete
    }
}

protocol LocalDatabase {
    func save<T: Codable>(_ item: T, withId id: String) async throws
    func update<T: Codable>(_ item: T, withId id: String) async throws
    func delete(withId id: String) async throws
    func fetchAll<T: Codable>(type: String) async throws -> [T]
}

protocol NetworkManager {
    func createItem(_ type: String, data: Data) async throws
    func updateItem(_ type: String, id: String, data: Data) async throws
    func deleteItem(_ type: String, id: String) async throws
}
```

#### Data Synchronization Conflict Resolution
```swift
class ConflictResolutionManager {
    enum ConflictType {
        case updateConflict      // Both local and remote modified
        case deleteConflict      // Local deleted, remote modified
        case versionConflict     // Version mismatch
        case mergeConflict       // Cannot auto-merge
    }
    
    struct Conflict {
        let type: ConflictType
        let localData: Data?
        let remoteData: Data?
        let localVersion: Int
        let remoteVersion: Int
        let entityId: String
        let entityType: String
    }
    
    private let resolutionStrategies: [ConflictType: ConflictResolutionStrategy]
    
    init() {
        self.resolutionStrategies = [
            .updateConflict: LastWriteWinsStrategy(),
            .deleteConflict: DeletionWinsStrategy(),
            .versionConflict: VersionBasedStrategy(),
            .mergeConflict: ManualResolutionStrategy()
        ]
    }
    
    func resolveConflict(_ conflict: Conflict) async throws -> ConflictResolution {
        guard let strategy = resolutionStrategies[conflict.type] else {
            throw ConflictResolutionError.noStrategyAvailable
        }
        
        return try await strategy.resolve(conflict)
    }
    
    func detectConflicts(localData: Data?, remoteData: Data?, localVersion: Int, remoteVersion: Int) -> ConflictType? {
        if localVersion == remoteVersion {
            return nil // No conflict
        }
        
        if localData == nil && remoteData != nil {
            return .deleteConflict
        }
        
        if localData != nil && remoteData == nil {
            return .deleteConflict
        }
        
        if localData != nil && remoteData != nil {
            if localVersion > remoteVersion {
                return .versionConflict
            } else {
                return .updateConflict
            }
        }
        
        return .mergeConflict
    }
}

protocol ConflictResolutionStrategy {
    func resolve(_ conflict: ConflictResolutionManager.Conflict) async throws -> ConflictResolution
}

struct ConflictResolution {
    let resolvedData: Data
    let newVersion: Int
    let resolutionType: String
}

class LastWriteWinsStrategy: ConflictResolutionStrategy {
    func resolve(_ conflict: ConflictResolutionManager.Conflict) async throws -> ConflictResolution {
        // Choose the most recent version based on timestamp
        let localTimestamp = getTimestamp(from: conflict.localData)
        let remoteTimestamp = getTimestamp(from: conflict.remoteData)
        
        let resolvedData: Data
        let newVersion: Int
        
        if localTimestamp > remoteTimestamp {
            resolvedData = conflict.localData ?? Data()
            newVersion = conflict.localVersion + 1
        } else {
            resolvedData = conflict.remoteData ?? Data()
            newVersion = conflict.remoteVersion + 1
        }
        
        return ConflictResolution(
            resolvedData: resolvedData,
            newVersion: newVersion,
            resolutionType: "LastWriteWins"
        )
    }
    
    private func getTimestamp(from data: Data?) -> Date {
        // Extract timestamp from data
        // Implementation depends on data structure
        return Date()
    }
}

class DeletionWinsStrategy: ConflictResolutionStrategy {
    func resolve(_ conflict: ConflictResolutionManager.Conflict) async throws -> ConflictResolution {
        // If one side deleted, deletion wins
        if conflict.localData == nil {
            return ConflictResolution(
                resolvedData: Data(),
                newVersion: conflict.remoteVersion + 1,
                resolutionType: "DeletionWins"
            )
        } else {
            return ConflictResolution(
                resolvedData: Data(),
                newVersion: conflict.localVersion + 1,
                resolutionType: "DeletionWins"
            )
        }
    }
}

class VersionBasedStrategy: ConflictResolutionStrategy {
    func resolve(_ conflict: ConflictResolutionManager.Conflict) async throws -> ConflictResolution {
        // Use the higher version number
        let resolvedData = conflict.localVersion > conflict.remoteVersion ? conflict.localData : conflict.remoteData
        let newVersion = max(conflict.localVersion, conflict.remoteVersion) + 1
        
        return ConflictResolution(
            resolvedData: resolvedData ?? Data(),
            newVersion: newVersion,
            resolutionType: "VersionBased"
        )
    }
}

class ManualResolutionStrategy: ConflictResolutionStrategy {
    func resolve(_ conflict: ConflictResolutionManager.Conflict) async throws -> ConflictResolution {
        // This would typically involve user interaction
        // For now, throw an error indicating manual resolution needed
        throw ConflictResolutionError.manualResolutionRequired
    }
}

enum ConflictResolutionError: Error {
    case noStrategyAvailable
    case manualResolutionRequired
}
```

### Advanced Error Handling and Recovery

#### Comprehensive Error Handling with Recovery
```swift
class ErrorRecoveryManager {
    private let recoveryStrategies: [ErrorType: RecoveryStrategy]
    private let errorQueue = DispatchQueue(label: "error.recovery", attributes: .serial)
    
    enum ErrorType {
        case networkError(NetworkError)
        case databaseError(DatabaseError)
        case validationError(ValidationError)
        case systemError(SystemError)
    }
    
    init() {
        self.recoveryStrategies = [
            .networkError(.timeout): RetryStrategy(maxAttempts: 3, backoff: .exponential),
            .networkError(.serverError): CircuitBreakerStrategy(),
            .databaseError(.corruption): DataRecoveryStrategy(),
            .validationError(.invalidData): DataCorrectionStrategy(),
            .systemError(.memoryPressure): ResourceCleanupStrategy()
        ]
    }
    
    func handleError(_ error: Error) async throws {
        let errorType = classifyError(error)
        
        if let strategy = recoveryStrategies[errorType] {
            try await strategy.recover(from: errorType)
        } else {
            // Default error handling
            try await handleUnknownError(error)
        }
    }
    
    private func classifyError(_ error: Error) -> ErrorType {
        // Classify errors based on their type
        if let networkError = error as? NetworkError {
            return .networkError(networkError)
        } else if let databaseError = error as? DatabaseError {
            return .databaseError(databaseError)
        } else if let validationError = error as? ValidationError {
            return .validationError(validationError)
        } else {
            return .systemError(.unknown)
        }
    }
    
    private func handleUnknownError(_ error: Error) async throws {
        // Log error and attempt basic recovery
        print("Unknown error: \(error)")
        
        // Try to continue with degraded functionality
        try await degradeFunctionality()
    }
    
    private func degradeFunctionality() async throws {
        // Implement graceful degradation
        // Disable non-essential features
        // Switch to offline mode if possible
    }
}

protocol RecoveryStrategy {
    func recover(from error: ErrorRecoveryManager.ErrorType) async throws
}

class RetryStrategy: RecoveryStrategy {
    private let maxAttempts: Int
    private let backoff: BackoffStrategy
    
    init(maxAttempts: Int, backoff: BackoffStrategy) {
        self.maxAttempts = maxAttempts
        self.backoff = backoff
    }
    
    func recover(from error: ErrorRecoveryManager.ErrorType) async throws {
        var attempt = 0
        var lastError: Error?
        
        while attempt < maxAttempts {
            do {
                // Attempt recovery
                try await performRecovery(for: error)
                return
            } catch {
                lastError = error
                attempt += 1
                
                if attempt < maxAttempts {
                    let delay = backoff.calculateDelay(for: attempt)
                    try await Task.sleep(nanoseconds: UInt64(delay * 1_000_000_000))
                }
            }
        }
        
        throw RecoveryError.maxAttemptsExceeded(lastError)
    }
    
    private func performRecovery(for error: ErrorRecoveryManager.ErrorType) async throws {
        // Implement specific recovery logic
        switch error {
        case .networkError(.timeout):
            try await retryNetworkOperation()
        default:
            throw RecoveryError.unsupportedErrorType
        }
    }
    
    private func retryNetworkOperation() async throws {
        // Retry the failed network operation
        // Implementation depends on specific use case
    }
}

enum BackoffStrategy {
    case fixed(TimeInterval)
    case exponential
    case fibonacci
    
    func calculateDelay(for attempt: Int) -> TimeInterval {
        switch self {
        case .fixed(let delay):
            return delay
            
        case .exponential:
            return pow(2.0, Double(attempt - 1))
            
        case .fibonacci:
            return Double(fibonacci(attempt))
        }
    }
    
    private func fibonacci(_ n: Int) -> Int {
        if n <= 1 { return n }
        return fibonacci(n - 1) + fibonacci(n - 2)
    }
}

enum RecoveryError: Error {
    case maxAttemptsExceeded(Error?)
    case unsupportedErrorType
}

// Additional error types for demonstration
enum NetworkError: Error {
    case timeout
    case serverError
    case connectionFailed
}

enum DatabaseError: Error {
    case corruption
    case connectionFailed
    case queryFailed
}

enum ValidationError: Error {
    case invalidData
    case missingRequiredField
    case formatError
}

enum SystemError: Error {
    case memoryPressure
    case diskFull
    case unknown
}
```

---

## Testing & Quality Assurance

### Stress Testing Concurrent Systems

#### Concurrent Operation Stress Testing
```swift
class ConcurrentStressTester {
    private let testQueue = DispatchQueue(label: "stress.test", attributes: .concurrent)
    private let resultsQueue = DispatchQueue(label: "results", attributes: .serial)
    private var testResults: [TestResult] = []
    
    struct TestResult {
        let testName: String
        let concurrentOperations: Int
        let duration: TimeInterval
        let successCount: Int
        let failureCount: Int
        let averageResponseTime: TimeInterval
        let memoryUsage: UInt64
        let cpuUsage: Double
    }
    
    func stressTest<T>(_ operation: @escaping () async throws -> T,
                       testName: String,
                       concurrentOperations: Int,
                       duration: TimeInterval) async -> TestResult {
        
        let startTime = CFAbsoluteTimeGetCurrent()
        let startMemory = getMemoryUsage()
        let startCPU = getCPUUsage()
        
        var successCount = 0
        var failureCount = 0
        var responseTimes: [TimeInterval] = []
        
        let group = DispatchGroup()
        let semaphore = DispatchSemaphore(value: concurrentOperations)
        
        // Start concurrent operations
        for i in 0..<concurrentOperations {
            group.enter()
            
            testQueue.async {
                semaphore.wait()
                
                let operationStart = CFAbsoluteTimeGetCurrent()
                
                Task {
                    do {
                        _ = try await operation()
                        
                        await MainActor.run {
                            successCount += 1
                            responseTimes.append(CFAbsoluteTimeGetCurrent() - operationStart)
                        }
                    } catch {
                        await MainActor.run {
                            failureCount += 1
                        }
                    }
                    
                    group.leave()
                    semaphore.signal()
                }
            }
        }
        
        // Wait for completion or timeout
        let timeoutResult = group.wait(timeout: .now() + duration)
        
        let endTime = CFAbsoluteTimeGetCurrent()
        let endMemory = getMemoryUsage()
        let endCPU = getCPUUsage()
        
        let testDuration = endTime - startTime
        let memoryDelta = endMemory - startMemory
        let cpuDelta = endCPU - startCPU
        
        let averageResponseTime = responseTimes.isEmpty ? 0 : responseTimes.reduce(0, +) / Double(responseTimes.count)
        
        let result = TestResult(
            testName: testName,
            concurrentOperations: concurrentOperations,
            duration: testDuration,
            successCount: successCount,
            failureCount: failureCount,
            averageResponseTime: averageResponseTime,
            memoryUsage: memoryDelta,
            cpuUsage: cpuDelta
        )
        
        resultsQueue.async {
            self.testResults.append(result)
        }
        
        return result
    }
    
    func runComprehensiveStressTest() async {
        let tests = [
            ("Database Write", 100, 30.0),
            ("Database Read", 200, 30.0),
            ("Network Request", 50, 30.0),
            ("Image Processing", 25, 30.0),
            ("File Operations", 75, 30.0)
        ]
        
        for (testName, concurrentOps, duration) in tests {
            let result = await stressTest(
                { try await performTestOperation(testName) },
                testName: testName,
                concurrentOperations: concurrentOps,
                duration: duration
            )
            
            print("Stress Test Result: \(result.testName)")
            print("  Concurrent Operations: \(result.concurrentOperations)")
            print("  Duration: \(String(format: "%.2f", result.duration))s")
            print("  Success: \(result.successCount), Failures: \(result.failureCount)")
            print("  Avg Response Time: \(String(format: "%.4f", result.averageResponseTime))s")
            print("  Memory Usage: \(result.memoryUsage) bytes")
            print("  CPU Usage: \(String(format: "%.2f", result.cpuUsage))%")
            print("---")
        }
    }
    
    private func performTestOperation(_ testName: String) async throws -> String {
        // Simulate different types of operations
        switch testName {
        case "Database Write":
            try await Task.sleep(nanoseconds: UInt64.random(in: 10_000_000...100_000_000)) // 10-100ms
            return "Write completed"
            
        case "Database Read":
            try await Task.sleep(nanoseconds: UInt64.random(in: 5_000_000...50_000_000)) // 5-50ms
            return "Read completed"
            
        case "Network Request":
            try await Task.sleep(nanoseconds: UInt64.random(in: 50_000_000...500_000_000)) // 50-500ms
            return "Network request completed"
            
        case "Image Processing":
            try await Task.sleep(nanoseconds: UInt64.random(in: 100_000_000...1_000_000_000)) // 100-1000ms
            return "Image processing completed"
            
        case "File Operations":
            try await Task.sleep(nanoseconds: UInt64.random(in: 20_000_000...200_000_000)) // 20-200ms
            return "File operation completed"
            
        default:
            try await Task.sleep(nanoseconds: 50_000_000) // 50ms
            return "Operation completed"
        }
    }
    
    private func getMemoryUsage() -> UInt64 {
        var info = mach_task_basic_info()
        var count = mach_msg_type_number_t(MemoryLayout<mach_task_basic_info>.size)/4
        
        let kerr: kern_return_t = withUnsafeMutablePointer(to: &info) {
            $0.withMemoryRebound(to: integer_t.self, capacity: 1) {
                task_info(mach_task_self_,
                         task_flavor_t(MACH_TASK_BASIC_INFO),
                         $0,
                         &count)
            }
        }
        
        return kerr == KERN_SUCCESS ? info.resident_size : 0
    }
    
    private func getCPUUsage() -> Double {
        // Simplified CPU usage calculation
        // In a real implementation, you'd use more sophisticated methods
        return Double.random(in: 0...100)
    }
    
    func getTestResults() -> [TestResult] {
        return resultsQueue.sync { testResults }
    }
    
    func generateTestReport() -> String {
        let results = getTestResults()
        var report = "Concurrent Stress Test Report\n"
        report += "==============================\n\n"
        
        for result in results {
            report += "Test: \(result.testName)\n"
            report += "  Concurrent Operations: \(result.concurrentOperations)\n"
            report += "  Duration: \(String(format: "%.2f", result.duration))s\n"
            report += "  Success Rate: \(String(format: "%.1f", Double(result.successCount) / Double(result.successCount + result.failureCount) * 100))%\n"
            report += "  Average Response Time: \(String(format: "%.4f", result.averageResponseTime))s\n"
            report += "  Memory Usage: \(result.memoryUsage) bytes\n"
            report += "  CPU Usage: \(String(format: "%.2f", result.cpuUsage))%\n\n"
        }
        
        return report
    }
}
```

#### Race Condition Stress Testing
```swift
class RaceConditionStressTester {
    private let testQueue = DispatchQueue(label: "race.condition.test", attributes: .concurrent)
    private var detectedRaces: [RaceCondition] = []
    
    struct RaceCondition {
        let testName: String
        let description: String
        let timestamp: Date
        let threadInfo: String
        let expectedValue: Any
        let actualValue: Any
    }
    
    func testCounterRaceCondition(iterations: Int = 10000) async -> Bool {
        let counter = UnsafeCounter()
        var raceDetected = false
        
        await withThrowingTaskGroup(of: Void.self) { group in
            // Launch multiple concurrent increment operations
            for _ in 0..<10 {
                group.addTask {
                    for _ in 0..<iterations {
                        counter.increment()
                    }
                }
            }
        }
        
        let finalValue = counter.getCount()
        let expectedValue = 10 * iterations
        
        if finalValue != expectedValue {
            let race = RaceCondition(
                testName: "Counter Race Condition",
                description: "Multiple threads incrementing counter simultaneously",
                timestamp: Date(),
                threadInfo: "Main thread",
                expectedValue: expectedValue,
                actualValue: finalValue
            )
            
            detectedRaces.append(race)
            raceDetected = true
            
            print("🚨 Race condition detected!")
            print("  Expected: \(expectedValue)")
            print("  Actual: \(finalValue)")
            print("  Difference: \(expectedValue - finalValue)")
        }
        
        return raceDetected
    }
    
    func testArrayRaceCondition(iterations: Int = 1000) async -> Bool {
        let arrayManager = UnsafeArrayManager()
        var raceDetected = false
        
        await withThrowingTaskGroup(of: Void.self) { group in
            // Concurrent add operations
            for i in 0..<5 {
                group.addTask {
                    for j in 0..<iterations {
                        arrayManager.addItem("Item \(i)-\(j)")
                    }
                }
            }
            
            // Concurrent remove operations
            for i in 0..<5 {
                group.addTask {
                    for _ in 0..<iterations {
                        if arrayManager.getItemCount() > 0 {
                            arrayManager.removeItem(at: 0)
                        }
                    }
                }
            }
        }
        
        let finalCount = arrayManager.getItemCount()
        let expectedCount = 5 * iterations - 5 * iterations // Should be 0
        
        if finalCount != expectedCount {
            let race = RaceCondition(
                testName: "Array Race Condition",
                description: "Multiple threads modifying array simultaneously",
                timestamp: Date(),
                threadInfo: "Main thread",
                expectedValue: expectedCount,
                actualValue: finalCount
            )
            
            detectedRaces.append(race)
            raceDetected = true
            
            print("🚨 Array race condition detected!")
            print("  Expected count: \(expectedCount)")
            print("  Actual count: \(finalCount)")
        }
        
        return raceDetected
    }
    
    func testDataRaceCondition(iterations: Int = 10000) async -> Bool {
        let dataManager = UnsafeDataManager()
        var raceDetected = false
        
        await withThrowingTaskGroup(of: Void.self) { group in
            // Concurrent read operations
            for _ in 0..<5 {
                group.addTask {
                    for _ in 0..<iterations {
                        _ = dataManager.getValue()
                    }
                }
            }
            
            // Concurrent write operations
            for i in 0..<5 {
                group.addTask {
                    for j in 0..<iterations {
                        dataManager.setValue("Value \(i)-\(j)")
                    }
                }
            }
        }
        
        // Check for data corruption
        let finalValue = dataManager.getValue()
        let expectedValue = "Value 4-\(iterations - 1)" // Last written value
        
        if finalValue != expectedValue {
            let race = RaceCondition(
                testName: "Data Race Condition",
                description: "Multiple threads reading/writing shared data",
                timestamp: Date(),
                threadInfo: "Main thread",
                expectedValue: expectedValue,
                actualValue: finalValue
            )
            
            detectedRaces.append(race)
            raceDetected = true
            
            print("🚨 Data race condition detected!")
            print("  Expected: \(expectedValue)")
            print("  Actual: \(finalValue)")
        }
        
        return raceDetected
    }
    
    func runAllRaceConditionTests() async -> [RaceCondition] {
        print("Starting Race Condition Stress Tests...")
        
        let tests = [
            ("Counter Race Condition", { await self.testCounterRaceCondition() }),
            ("Array Race Condition", { await self.testArrayRaceCondition() }),
            ("Data Race Condition", { await self.testDataRaceCondition() })
        ]
        
        for (testName, test) in tests {
            print("\nRunning \(testName)...")
            let raceDetected = await test()
            
            if raceDetected {
                print("❌ \(testName) FAILED - Race condition detected")
            } else {
                print("✅ \(testName) PASSED - No race condition detected")
            }
        }
        
        return detectedRaces
    }
    
    func generateRaceConditionReport() -> String {
        var report = "Race Condition Test Report\n"
        report += "==========================\n\n"
        
        if detectedRaces.isEmpty {
            report += "✅ All tests passed - No race conditions detected\n"
        } else {
            report += "❌ \(detectedRaces.count) race condition(s) detected:\n\n"
            
            for (index, race) in detectedRaces.enumerated() {
                report += "\(index + 1). \(race.testName)\n"
                report += "   Description: \(race.description)\n"
                report += "   Timestamp: \(race.timestamp)\n"
                report += "   Expected: \(race.expectedValue)\n"
                report += "   Actual: \(race.actualValue)\n\n"
            }
        }
        
        return report
    }
}

// Unsafe classes for testing race conditions
class UnsafeCounter {
    private var count = 0
    
    func increment() {
        count += 1
    }
    
    func getCount() -> Int {
        return count
    }
}

class UnsafeArrayManager {
    private var items: [String] = []
    
    func addItem(_ item: String) {
        items.append(item)
    }
    
    func removeItem(at index: Int) {
        if index < items.count {
            items.remove(at: index)
        }
    }
    
    func getItemCount() -> Int {
        return items.count
    }
}

class UnsafeDataManager {
    private var value: String = "Initial"
    
    func setValue(_ newValue: String) {
        value = newValue
    }
    
    func getValue() -> String {
        return value
    }
}
```

### Performance Benchmarking

#### Comprehensive Performance Benchmarking
```swift
class PerformanceBenchmarker {
    private let benchmarkQueue = DispatchQueue(label: "benchmark", attributes: .serial)
    private var benchmarks: [String: BenchmarkResult] = [:]
    
    struct BenchmarkResult {
        let name: String
        let iterations: Int
        let totalTime: TimeInterval
        let averageTime: TimeInterval
        let minTime: TimeInterval
        let maxTime: TimeInterval
        let memoryUsage: UInt64
        let cpuUsage: Double
        let timestamp: Date
    }
    
    func benchmark<T>(_ operation: @escaping () -> T,
                      name: String,
                      iterations: Int = 1000,
                      warmupIterations: Int = 100) -> BenchmarkResult {
        
        // Warmup phase
        for _ in 0..<warmupIterations {
            _ = operation()
        }
        
        let startTime = CFAbsoluteTimeGetCurrent()
        let startMemory = getMemoryUsage()
        let startCPU = getCPUUsage()
        
        var times: [TimeInterval] = []
        
        // Actual benchmark
        for _ in 0..<iterations {
            let iterationStart = CFAbsoluteTimeGetCurrent()
            _ = operation()
            let iterationEnd = CFAbsoluteTimeGetCurrent()
            times.append(iterationEnd - iterationStart)
        }
        
        let endTime = CFAbsoluteTimeGetCurrent()
        let endMemory = getMemoryUsage()
        let endCPU = getCPUUsage()
        
        let totalTime = endTime - startTime
        let averageTime = times.reduce(0, +) / Double(times.count)
        let minTime = times.min() ?? 0
        let maxTime = times.max() ?? 0
        let memoryUsage = endMemory - startMemory
        let cpuUsage = endCPU - startCPU
        
        let result = BenchmarkResult(
            name: name,
            iterations: iterations,
            totalTime: totalTime,
            averageTime: averageTime,
            minTime: minTime,
            maxTime: maxTime,
            memoryUsage: memoryUsage,
            cpuUsage: cpuUsage,
            timestamp: Date()
        )
        
        benchmarkQueue.async {
            self.benchmarks[name] = result
        }
        
        return result
    }
    
    func benchmarkAsync<T>(_ operation: @escaping () async -> T,
                           name: String,
                           iterations: Int = 1000,
                           warmupIterations: Int = 100) async -> BenchmarkResult {
        
        // Warmup phase
        for _ in 0..<warmupIterations {
            _ = await operation()
        }
        
        let startTime = CFAbsoluteTimeGetCurrent()
        let startMemory = getMemoryUsage()
        let startCPU = getCPUUsage()
        
        var times: [TimeInterval] = []
        
        // Actual benchmark
        for _ in 0..<iterations {
            let iterationStart = CFAbsoluteTimeGetCurrent()
            _ = await operation()
            let iterationEnd = CFAbsoluteTimeGetCurrent()
            times.append(iterationEnd - iterationStart)
        }
        
        let endTime = CFAbsoluteTimeGetCurrent()
        let endMemory = getMemoryUsage()
        let endCPU = getCPUUsage()
        
        let totalTime = endTime - startTime
        let averageTime = times.reduce(0, +) / Double(times.count)
        let minTime = times.min() ?? 0
        let maxTime = times.max() ?? 0
        let memoryUsage = endMemory - startMemory
        let cpuUsage = endCPU - startCPU
        
        let result = BenchmarkResult(
            name: name,
            iterations: iterations,
            totalTime: totalTime,
            averageTime: averageTime,
            minTime: minTime,
            maxTime: maxTime,
            memoryUsage: memoryUsage,
            cpuUsage: cpuUsage,
            timestamp: Date()
        )
        
        benchmarkQueue.async {
            self.benchmarks[name] = result
        }
        
        return result
    }
    
    func compareBenchmarks(_ name1: String, _ name2: String) -> String? {
        guard let benchmark1 = benchmarks[name1],
              let benchmark2 = benchmarks[name2] else {
            return nil
        }
        
        let timeDifference = benchmark2.averageTime - benchmark1.averageTime
        let timePercentage = (timeDifference / benchmark1.averageTime) * 100
        
        let memoryDifference = Int64(benchmark2.memoryUsage) - Int64(benchmark1.memoryUsage)
        let memoryPercentage = Double(memoryDifference) / Double(benchmark1.memoryUsage) * 100
        
        var comparison = "Benchmark Comparison: \(name1) vs \(name2)\n"
        comparison += "==========================================\n\n"
        
        comparison += "Execution Time:\n"
        comparison += "  \(name1): \(String(format: "%.6f", benchmark1.averageTime))s\n"
        comparison += "  \(name2): \(String(format: "%.6f", benchmark2.averageTime))s\n"
        comparison += "  Difference: \(String(format: "%.6f", timeDifference))s (\(String(format: "%.1f", timePercentage))%)\n\n"
        
        comparison += "Memory Usage:\n"
        comparison += "  \(name1): \(benchmark1.memoryUsage) bytes\n"
        comparison += "  \(name2): \(benchmark2.memoryUsage) bytes\n"
        comparison += "  Difference: \(memoryDifference) bytes (\(String(format: "%.1f", memoryPercentage))%)\n\n"
        
        if timeDifference < 0 {
            comparison += "✅ \(name2) is \(String(format: "%.1f", abs(timePercentage)))% faster\n"
        } else {
            comparison += "❌ \(name2) is \(String(format: "%.1f", timePercentage))% slower\n"
        }
        
        if memoryDifference < 0 {
            comparison += "✅ \(name2) uses \(String(format: "%.1f", abs(memoryPercentage)))% less memory\n"
        } else {
            comparison += "❌ \(name2) uses \(String(format: "%.1f", memoryPercentage))% more memory\n"
        }
        
        return comparison
    }
    
    func generateBenchmarkReport() -> String {
        var report = "Performance Benchmark Report\n"
        report += "============================\n\n"
        
        let sortedBenchmarks = benchmarks.values.sorted { $0.averageTime < $1.averageTime }
        
        for (index, benchmark) in sortedBenchmarks.enumerated() {
            report += "\(index + 1). \(benchmark.name)\n"
            report += "   Average Time: \(String(format: "%.6f", benchmark.averageTime))s\n"
            report += "   Min Time: \(String(format: "%.6f", benchmark.minTime))s\n"
            report += "   Max Time: \(String(format: "%.6f", benchmark.maxTime))s\n"
            report += "   Total Time: \(String(format: "%.3f", benchmark.totalTime))s\n"
            report += "   Iterations: \(benchmark.iterations)\n"
            report += "   Memory Usage: \(benchmark.memoryUsage) bytes\n"
            report += "   CPU Usage: \(String(format: "%.2f", benchmark.cpuUsage))%\n"
            report += "   Timestamp: \(benchmark.timestamp)\n\n"
        }
        
        return report
    }
    
    private func getMemoryUsage() -> UInt64 {
        var info = mach_task_basic_info()
        var count = mach_msg_type_number_t(MemoryLayout<mach_task_basic_info>.size)/4
        
        let kerr: kern_return_t = withUnsafeMutablePointer(to: &info) {
            $0.withMemoryRebound(to: integer_t.self, capacity: 1) {
                task_info(mach_task_self_,
                         task_flavor_t(MACH_TASK_BASIC_INFO),
                         $0,
                         &count)
            }
        }
        
        return kerr == KERN_SUCCESS ? info.resident_size : 0
    }
    
    private func getCPUUsage() -> Double {
        // Simplified CPU usage calculation
        // In a real implementation, you'd use more sophisticated methods
        return Double.random(in: 0...100)
    }
}

// Usage examples
class BenchmarkExamples {
    let benchmarker = PerformanceBenchmarker()
    
    func runQueueBenchmarks() async {
        // Benchmark different queue types
        let serialQueue = DispatchQueue(label: "serial")
        let concurrentQueue = DispatchQueue(label: "concurrent", attributes: .concurrent)
        
        // Serial queue benchmark
        let serialResult = benchmarker.benchmark({
            serialQueue.sync {
                // Simulate work
                Thread.sleep(forTimeInterval: 0.001)
            }
        }, name: "Serial Queue Sync", iterations: 1000)
        
        // Concurrent queue benchmark
        let concurrentResult = benchmarker.benchmark({
            concurrentQueue.sync {
                // Simulate work
                Thread.sleep(forTimeInterval: 0.001)
            }
        }, name: "Concurrent Queue Sync", iterations: 1000)
        
        // Async operations benchmark
        let asyncResult = await benchmarker.benchmarkAsync({
            await withCheckedContinuation { continuation in
                concurrentQueue.async {
                    Thread.sleep(forTimeInterval: 0.001)
                    continuation.resume()
                }
            }
        }, name: "Async Operation", iterations: 1000)
        
        print("Queue Benchmarks Completed")
        print(benchmarker.generateBenchmarkReport())
        
        if let comparison = benchmarker.compareBenchmarks("Serial Queue Sync", "Concurrent Queue Sync") {
            print("\nComparison:")
            print(comparison)
        }
    }
    
    func runMemoryBenchmarks() {
        // Benchmark memory allocation patterns
        let arrayResult = benchmarker.benchmark({
            var array: [Int] = []
            for i in 0..<1000 {
                array.append(i)
            }
            return array.count
        }, name: "Array Allocation", iterations: 1000)
        
        let dictionaryResult = benchmarker.benchmark({
            var dict: [String: Int] = [:]
            for i in 0..<1000 {
                dict["key\(i)"] = i
            }
            return dict.count
        }, name: "Dictionary Allocation", iterations: 1000)
        
        print("Memory Benchmarks Completed")
        print(benchmarker.generateBenchmarkReport())
    }
}
```

### Memory Leak Detection

#### Memory Leak Detection System
```swift
class MemoryLeakDetector {
    private let detectionQueue = DispatchQueue(label: "memory.leak.detection", attributes: .serial)
    private var trackedObjects: [String: WeakReference] = [:]
    private var leakReports: [MemoryLeakReport] = []
    
    class WeakReference {
        weak var object: AnyObject?
        let objectType: String
        let creationTime: Date
        let stackTrace: String
        
        init(object: AnyObject, objectType: String, stackTrace: String) {
            self.object = object
            self.objectType = objectType
            self.creationTime = creationTime
            self.stackTrace = stackTrace
        }
        
        var isLeaked: Bool {
            return object == nil
        }
    }
    
    struct MemoryLeakReport {
        let objectType: String
        let creationTime: Date
        let detectionTime: Date
        let stackTrace: String
        let memoryAddress: String
        let leakDuration: TimeInterval
    }
    
    func trackObject(_ object: AnyObject, name: String? = nil) {
        let objectType = String(describing: type(of: object))
        let objectName = name ?? "\(objectType)_\(UUID().uuidString.prefix(8))"
        let stackTrace = getStackTrace()
        
        let weakRef = WeakReference(
            object: object,
            objectType: objectType,
            stackTrace: stackTrace
        )
        
        detectionQueue.async {
            self.trackedObjects[objectName] = weakRef
        }
    }
    
    func detectLeaks() -> [MemoryLeakReport] {
        return detectionQueue.sync {
            var leaks: [MemoryLeakReport] = []
            let now = Date()
            
            for (name, weakRef) in trackedObjects {
                if weakRef.isLeaked {
                    let leakDuration = now.timeIntervalSince(weakRef.creationTime)
                    
                    let report = MemoryLeakReport(
                        objectType: weakRef.objectType,
                        creationTime: weakRef.creationTime,
                        detectionTime: now,
                        stackTrace: weakRef.stackTrace,
                        memoryAddress: name,
                        leakDuration: leakDuration
                    )
                    
                    leaks.append(report)
                    leakReports.append(report)
                    
                    // Remove leaked object from tracking
                    trackedObjects.removeValue(forKey: name)
                }
            }
            
            return leaks
        }
    }
    
    func startPeriodicLeakDetection(interval: TimeInterval = 30.0) {
        Timer.scheduledTimer(withTimeInterval: interval, repeats: true) { _ in
            Task {
                let leaks = self.detectLeaks()
                if !leaks.isEmpty {
                    await self.reportLeaks(leaks)
                }
            }
        }
    }
    
    private func reportLeaks(_ leaks: [MemoryLeakReport]) async {
        await MainActor.run {
            print("🚨 Memory Leaks Detected: \(leaks.count)")
            
            for leak in leaks {
                print("  Object Type: \(leak.objectType)")
                print("  Creation Time: \(leak.creationTime)")
                print("  Leak Duration: \(String(format: "%.1f", leak.leakDuration))s")
                print("  Stack Trace: \(leak.stackTrace)")
                print("---")
            }
        }
    }
    
    private func getStackTrace() -> String {
        let symbols = Thread.callStackSymbols
        let relevantSymbols = symbols.prefix(5) // First 5 stack frames
        return relevantSymbols.joined(separator: "\n")
    }
    
    func generateLeakReport() -> String {
        var report = "Memory Leak Detection Report\n"
        report += "=============================\n\n"
        
        let currentLeaks = detectLeaks()
        let totalLeaks = leakReports.count + currentLeaks.count
        
        report += "Current Leaks: \(currentLeaks.count)\n"
        report += "Total Leaks Detected: \(totalLeaks)\n"
        report += "Tracked Objects: \(trackedObjects.count)\n\n"
        
        if !currentLeaks.isEmpty {
            report += "Active Memory Leaks:\n"
            report += "===================\n\n"
            
            for leak in currentLeaks {
                report += "Object Type: \(leak.objectType)\n"
                report += "Creation Time: \(leak.creationTime)\n"
                report += "Leak Duration: \(String(format: "%.1f", leak.leakDuration))s\n"
                report += "Stack Trace:\n\(leak.stackTrace)\n\n"
            }
        }
        
        if !leakReports.isEmpty {
            report += "Historical Leaks:\n"
            report += "=================\n\n"
            
            for leak in leakReports.suffix(10) { // Last 10 leaks
                report += "Object Type: \(leak.objectType)\n"
                report += "Creation Time: \(leak.creationTime)\n"
                report += "Detection Time: \(leak.detectionTime)\n"
                report += "Leak Duration: \(String(format: "%.1f", leak.leakDuration))s\n\n"
            }
        }
        
        return report
    }
    
    func clearHistory() {
        detectionQueue.async {
            self.leakReports.removeAll()
        }
    }
}

// Usage example
class MemoryLeakExample {
    let leakDetector = MemoryLeakDetector()
    
    func demonstrateMemoryLeak() {
        // Start leak detection
        leakDetector.startPeriodicLeakDetection(interval: 10.0)
        
        // Create objects that will be tracked
        let object1 = NSObject()
        let object2 = NSObject()
        
        leakDetector.trackObject(object1, name: "TestObject1")
        leakDetector.trackObject(object2, name: "TestObject2")
        
        // Simulate memory leak by creating strong references in closures
        DispatchQueue.global().async {
            // This closure captures object1 strongly
            _ = object1
            print("Object1 captured in closure")
        }
        
        // object2 will be deallocated normally
        print("Object2 will be deallocated")
        
        // Check for leaks after a delay
        DispatchQueue.main.asyncAfter(deadline: .now() + 15.0) {
            let leaks = self.leakDetector.detectLeaks()
            print("Leaks detected: \(leaks.count)")
            
            let report = self.leakDetector.generateLeakReport()
            print(report)
        }
    }
}
```

### Integration Testing with Real Networks

#### Network Integration Testing
```swift
class NetworkIntegrationTester {
    private let testQueue = DispatchQueue(label: "network.integration.test", attributes: .concurrent)
    private var testResults: [NetworkTestResult] = []
    
    struct NetworkTestResult {
        let testName: String
        let endpoint: String
        let method: String
        let statusCode: Int?
        let responseTime: TimeInterval
        let success: Bool
        let error: Error?
        let timestamp: Date
        let responseSize: Int?
    }
    
    func testEndpoint(_ endpoint: String, method: String = "GET", expectedStatus: Int = 200) async -> NetworkTestResult {
        let startTime = CFAbsoluteTimeGetCurrent()
        
        do {
            guard let url = URL(string: endpoint) else {
                throw NetworkTestError.invalidURL
            }
            
            var request = URLRequest(url: url)
            request.httpMethod = method
            
            let (data, response) = try await URLSession.shared.data(for: request)
            
            let endTime = CFAbsoluteTimeGetCurrent()
            let responseTime = endTime - startTime
            
            let httpResponse = response as? HTTPURLResponse
            let statusCode = httpResponse?.statusCode
            let success = statusCode == expectedStatus
            
            let result = NetworkTestResult(
                testName: "\(method) \(endpoint)",
                endpoint: endpoint,
                method: method,
                statusCode: statusCode,
                responseTime: responseTime,
                success: success,
                error: nil,
                timestamp: Date(),
                responseSize: data.count
            )
            
            testQueue.async {
                self.testResults.append(result)
            }
            
            return result
            
        } catch {
            let endTime = CFAbsoluteTimeGetCurrent()
            let responseTime = endTime - startTime
            
            let result = NetworkTestResult(
                testName: "\(method) \(endpoint)",
                endpoint: endpoint,
                method: method,
                statusCode: nil,
                responseTime: responseTime,
                success: false,
                error: error,
                timestamp: Date(),
                responseSize: nil
            )
            
            testQueue.async {
                self.testResults.append(result)
            }
            
            return result
        }
    }
    
    func runComprehensiveNetworkTests() async {
        let testEndpoints = [
            ("https://httpbin.org/get", "GET", 200),
            ("https://httpbin.org/post", "POST", 200),
            ("https://httpbin.org/status/404", "GET", 404),
            ("https://httpbin.org/status/500", "GET", 500),
            ("https://httpbin.org/delay/1", "GET", 200),
            ("https://httpbin.org/delay/3", "GET", 200)
        ]
        
        print("Starting Network Integration Tests...")
        
        await withThrowingTaskGroup(of: NetworkTestResult.self) { group in
            for (endpoint, method, expectedStatus) in testEndpoints {
                group.addTask {
                    return await self.testEndpoint(endpoint, method: method, expectedStatus: expectedStatus)
                }
            }
            
            for try await result in group {
                print("Test: \(result.testName)")
                print("  Status: \(result.statusCode ?? 0)")
                print("  Response Time: \(String(format: "%.3f", result.responseTime))s")
                print("  Success: \(result.success ? "✅" : "❌")")
                if let error = result.error {
                    print("  Error: \(error)")
                }
                print("---")
            }
        }
        
        print("Network Integration Tests Completed")
        print(generateNetworkTestReport())
    }
    
    func testNetworkPerformance(endpoint: String, iterations: Int = 10) async -> PerformanceMetrics {
        var responseTimes: [TimeInterval] = []
        var successCount = 0
        var failureCount = 0
        
        for _ in 0..<iterations {
            let result = await testEndpoint(endpoint)
            responseTimes.append(result.responseTime)
            
            if result.success {
                successCount += 1
            } else {
                failureCount += 1
            }
        }
        
        let averageResponseTime = responseTimes.reduce(0, +) / Double(responseTimes.count)
        let minResponseTime = responseTimes.min() ?? 0
        let maxResponseTime = responseTimes.max() ?? 0
        
        return PerformanceMetrics(
            endpoint: endpoint,
            iterations: iterations,
            averageResponseTime: averageResponseTime,
            minResponseTime: minResponseTime,
            maxResponseTime: maxResponseTime,
            successRate: Double(successCount) / Double(iterations),
            responseTimes: responseTimes
        )
    }
    
    func generateNetworkTestReport() -> String {
        let results = testQueue.sync { testResults }
        var report = "Network Integration Test Report\n"
        report += "==================================\n\n"
        
        let successfulTests = results.filter { $0.success }
        let failedTests = results.filter { !$0.success }
        
        report += "Summary:\n"
        report += "  Total Tests: \(results.count)\n"
        report += "  Successful: \(successfulTests.count)\n"
        report += "  Failed: \(failedTests.count)\n"
        report += "  Success Rate: \(String(format: "%.1f", Double(successfulTests.count) / Double(results.count) * 100))%\n\n"
        
        if !failedTests.isEmpty {
            report += "Failed Tests:\n"
            report += "=============\n\n"
            
            for test in failedTests {
                report += "Test: \(test.testName)\n"
                report += "  Endpoint: \(test.endpoint)\n"
                report += "  Method: \(test.method)\n"
                report += "  Status Code: \(test.statusCode ?? 0)\n"
                report += "  Error: \(test.error?.localizedDescription ?? "Unknown")\n\n"
            }
        }
        
        let averageResponseTime = results.map { $0.responseTime }.reduce(0, +) / Double(results.count)
        report += "Performance:\n"
        report += "  Average Response Time: \(String(format: "%.3f", averageResponseTime))s\n"
        report += "  Fastest Response: \(String(format: "%.3f", results.map { $0.responseTime }.min() ?? 0))s\n"
        report += "  Slowest Response: \(String(format: "%.3f", results.map { $0.responseTime }.max() ?? 0))s\n"
        
        return report
    }
}

struct PerformanceMetrics {
    let endpoint: String
    let iterations: Int
    let averageResponseTime: TimeInterval
    let minResponseTime: TimeInterval
    let maxResponseTime: TimeInterval
    let successRate: Double
    let responseTimes: [TimeInterval]
}

enum NetworkTestError: Error {
    case invalidURL
    case timeout
    case serverError
}

// Usage example
class NetworkTestingExample {
    let networkTester = NetworkIntegrationTester()
    
    func runNetworkTests() async {
        // Run comprehensive network tests
        await networkTester.runComprehensiveNetworkTests()
        
        // Test specific endpoint performance
        let metrics = await networkTester.testNetworkPerformance(endpoint: "https://httpbin.org/get", iterations: 20)
        
        print("Performance Metrics for \(metrics.endpoint):")
        print("  Average Response Time: \(String(format: "%.3f", metrics.averageResponseTime))s")
        print("  Success Rate: \(String(format: "%.1f", metrics.successRate * 100))%")
        print("  Min Response Time: \(String(format: "%.3f", metrics.minResponseTime))s")
        print("  Max Response Time: \(String(format: "%.3f", metrics.maxResponseTime))s")
    }
}
```

---

## Summary and Key Takeaways

### Concurrency Best Practices:
1. **Always use MainActor for UI updates**
2. **Choose appropriate QoS levels for tasks**
3. **Use actors for shared mutable state**
4. **Implement proper error handling and recovery**
5. **Test race conditions thoroughly**
6. **Monitor performance and memory usage**

### Networking Best Practices:
1. **Implement retry logic with exponential backoff**
2. **Use background URLSession for large downloads**
3. **Implement proper request cancellation**
4. **Cache responses when appropriate**
5. **Handle network connectivity changes**

### Race Condition Prevention:
1. **Use serial queues for critical sections**
2. **Implement actors for complex state management**
3. **Use atomic operations when possible**
4. **Test with multiple concurrent operations**
5. **Document thread safety guarantees**

### Advanced Patterns:
1. **Circuit breaker for fault tolerance**
2. **Graceful degradation for resilience**
3. **Offline-first architecture for reliability**
4. **Conflict resolution for data synchronization**
5. **Comprehensive error recovery strategies**

### Testing & Quality Assurance:
1. **Stress test concurrent systems thoroughly**
2. **Benchmark performance regularly**
3. **Detect and fix memory leaks early**
4. **Test with real network conditions**
5. **Monitor production performance metrics**

This comprehensive guide covers the essential aspects of Swift concurrency, networking, and race condition prevention in modern iOS development. The examples provided demonstrate real-world scenarios and best practices that you can apply to your projects.
