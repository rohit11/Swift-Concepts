# iOS and Android Memory Profiling & Performance Measurement Complete Guide

## Table of Contents
1. [Introduction](#introduction)
2. [iOS Memory Profiling Tools](#ios-memory-profiling-tools)
3. [Android Memory Profiling Tools](#android-memory-profiling-tools)
4. [Memory Leak Detection](#memory-leak-detection)
5. [CPU Usage Monitoring](#cpu-usage-monitoring)
6. [Performance Measurement](#performance-measurement)
7. [Best Practices for Memory Management](#best-practices-for-memory-management)
8. [Common Memory Issues and Solutions](#common-memory-issues-and-solutions)
9. [Self-Reference Cycles and Retain Cycles](#self-reference-cycles-and-retain-cycles)
10. [Real-World Examples](#real-world-examples)

## Introduction

Memory management and performance optimization are critical aspects of mobile app development. Poor memory management can lead to crashes, slow performance, and poor user experience. This guide covers comprehensive tools and techniques for both iOS and Android platforms.

## iOS Memory Profiling Tools

### 1. Instruments (Xcode)

Instruments is the primary profiling tool in Xcode that provides comprehensive analysis of your app's performance.

#### Key Instruments for Memory Analysis:

**Allocations Instrument:**
- Tracks memory allocations and deallocations
- Shows memory usage over time
- Identifies memory growth patterns
- Detects memory leaks

**Leaks Instrument:**
- Automatically detects memory leaks
- Shows call stack for leaked objects
- Provides detailed leak analysis

**VM Tracker:**
- Monitors virtual memory usage
- Shows memory regions and their states
- Tracks memory pressure

#### Usage Example:

```swift
// Example of proper memory management in Swift
class UserProfileViewController: UIViewController {
    private var userProfile: UserProfile?
    private var imageLoader: ImageLoader?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupUserProfile()
    }
    
    private func setupUserProfile() {
        userProfile = UserProfile()
        imageLoader = ImageLoader()
        
        // Weak reference to avoid retain cycles
        imageLoader?.onImageLoaded = { [weak self] image in
            self?.updateProfileImage(image)
        }
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        // Clean up resources
        imageLoader?.cancelLoading()
    }
    
    deinit {
        print("UserProfileViewController deallocated")
    }
}
```

### 2. Memory Graph Debugger

The Memory Graph Debugger in Xcode helps visualize object relationships and detect retain cycles.

#### Features:
- Visual representation of object graphs
- Automatic detection of retain cycles
- Memory usage statistics
- Object lifecycle tracking

#### Usage:
1. Run your app in Xcode
2. Go to Debug → Debug Memory Graph
3. Look for purple arrows indicating retain cycles
4. Examine object relationships

### 3. Xcode Memory Report

Built-in memory reporting in Xcode provides real-time memory usage information.

#### Access:
- View → Debug Area → Activate Console
- Look for memory warnings and statistics

## Android Memory Profiling Tools

### 1. Android Profiler (Android Studio)

Android Studio's built-in profiler provides comprehensive memory analysis.

#### Memory Profiler Features:
- Real-time memory usage monitoring
- Memory allocation tracking
- Garbage collection analysis
- Memory leak detection

#### Usage Example:

```kotlin
// Example of proper memory management in Android
class UserProfileActivity : AppCompatActivity() {
    private var userProfile: UserProfile? = null
    private var imageLoader: ImageLoader? = null
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_user_profile)
        setupUserProfile()
    }
    
    private fun setupUserProfile() {
        userProfile = UserProfile()
        imageLoader = ImageLoader()
        
        // Use weak reference to avoid memory leaks
        imageLoader?.setOnImageLoadedListener(object : ImageLoader.OnImageLoadedListener {
            override fun onImageLoaded(image: Bitmap) {
                updateProfileImage(image)
            }
        })
    }
    
    override fun onDestroy() {
        super.onDestroy()
        // Clean up resources
        imageLoader?.cancelLoading()
        imageLoader = null
        userProfile = null
    }
}
```

### 2. LeakCanary

LeakCanary is a memory leak detection library for Android.

#### Setup:

```gradle
dependencies {
    debugImplementation 'com.squareup.leakcanary:leakcanary-android:2.12'
}
```

#### Usage:

```kotlin
// LeakCanary automatically detects memory leaks
// No additional code needed - it works automatically in debug builds
```

### 3. Android Memory Analyzer (MAT)

MAT is a powerful tool for analyzing heap dumps and identifying memory issues.

#### Features:
- Heap dump analysis
- Memory leak detection
- Object reference analysis
- Memory usage optimization suggestions

## Memory Leak Detection

### Common Causes of Memory Leaks:

1. **Retain Cycles (iOS) / Memory Leaks (Android)**
2. **Strong References in Closures/Anonymous Classes**
3. **Unclosed Resources (Files, Network Connections)**
4. **Static References to Context/View Controllers**
5. **Event Listeners Not Properly Removed**

### iOS Memory Leak Detection:

```swift
// ❌ WRONG - Creates retain cycle
class NetworkManager {
    var completionHandler: ((Data) -> Void)?
    
    func fetchData() {
        // This creates a retain cycle
        completionHandler = { data in
            self.processData(data) // self is captured strongly
        }
    }
}

// ✅ CORRECT - Uses weak reference
class NetworkManager {
    var completionHandler: ((Data) -> Void)?
    
    func fetchData() {
        // Use weak self to avoid retain cycle
        completionHandler = { [weak self] data in
            guard let self = self else { return }
            self.processData(data)
        }
    }
}
```

### Android Memory Leak Detection:

```kotlin
// ❌ WRONG - Creates memory leak
class NetworkManager {
    private var callback: ((String) -> Unit)? = null
    
    fun fetchData() {
        // This captures the activity context strongly
        callback = { data ->
            processData(data) // Implicit reference to outer class
        }
    }
}

// ✅ CORRECT - Uses weak reference
class NetworkManager {
    private var callback: ((String) -> Unit)? = null
    
    fun fetchData() {
        // Use weak reference to avoid memory leak
        callback = { data ->
            processData(data)
        }
    }
}
```

## CPU Usage Monitoring

### iOS CPU Monitoring:

#### 1. Instruments - CPU Profiler:
- Shows CPU usage over time
- Identifies CPU-intensive operations
- Tracks thread activity
- Shows call stack for high CPU usage

#### 2. Code-level CPU Monitoring:

```swift
import Foundation

class CPUMonitor {
    static func getCurrentCPUUsage() -> Double {
        var info = mach_task_basic_info()
        var count = mach_msg_type_number_t(MemoryLayout<mach_task_basic_info>.size)/4
        
        let kerr: kern_return_t = withUnsafeMutablePointer(to: &info) {
            $0.withMemoryRebound(to: integer_t.self, capacity: 1) {
                task_info(mach_task_self(),
                         task_flavor_t(MACH_TASK_BASIC_INFO),
                         $0,
                         &count)
            }
        }
        
        if kerr == KERN_SUCCESS {
            return Double(info.cpu_usage) / Double(TH_USAGE_SCALE) * 100.0
        }
        return 0.0
    }
}

// Usage in your app
class PerformanceMonitor {
    private var timer: Timer?
    
    func startMonitoring() {
        timer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { _ in
            let cpuUsage = CPUMonitor.getCurrentCPUUsage()
            print("Current CPU Usage: \(cpuUsage)%")
            
            if cpuUsage > 80.0 {
                print("⚠️ High CPU usage detected!")
            }
        }
    }
}
```

### Android CPU Monitoring:

#### 1. Android Profiler - CPU Profiler:
- Real-time CPU usage
- Method tracing
- CPU-intensive operation identification

#### 2. Code-level CPU Monitoring:

```kotlin
import android.app.ActivityManager
import android.content.Context

class CPUMonitor(private val context: Context) {
    
    fun getCurrentCPUUsage(): Float {
        val activityManager = context.getSystemService(Context.ACTIVITY_SERVICE) as ActivityManager
        val processStats = activityManager.getProcessMemoryInfo(intArrayOf(android.os.Process.myPid()))
        
        if (processStats.isNotEmpty()) {
            return processStats[0].totalPss.toFloat() / 1024f // Convert to MB
        }
        return 0f
    }
    
    fun startMonitoring() {
        // Use Handler or Timer for periodic monitoring
        val handler = android.os.Handler(android.os.Looper.getMainLooper())
        val runnable = object : Runnable {
            override fun run() {
                val cpuUsage = getCurrentCPUUsage()
                println("Current CPU Usage: ${cpuUsage}MB")
                
                if (cpuUsage > 100) {
                    println("⚠️ High CPU usage detected!")
                }
                
                handler.postDelayed(this, 1000) // Check every second
            }
        }
        handler.post(runnable)
    }
}
```

## Performance Measurement

### iOS Performance Measurement:

#### 1. Time Profiling:

```swift
import Foundation

class PerformanceMeasurer {
    static func measureExecutionTime<T>(_ operation: () -> T) -> (T, TimeInterval) {
        let startTime = CFAbsoluteTimeGetCurrent()
        let result = operation()
        let endTime = CFAbsoluteTimeGetCurrent()
        let executionTime = endTime - startTime
        
        return (result, executionTime)
    }
    
    static func measureAsyncExecution<T>(_ operation: @escaping () async -> T) async -> (T, TimeInterval) {
        let startTime = CFAbsoluteTimeGetCurrent()
        let result = await operation()
        let endTime = CFAbsoluteTimeGetCurrent()
        let executionTime = endTime - startTime
        
        return (result, executionTime)
    }
}

// Usage examples
class ImageProcessor {
    func processImage(_ image: UIImage) -> UIImage? {
        let (result, time) = PerformanceMeasurer.measureExecutionTime {
            // Your image processing code here
            return image
        }
        
        print("Image processing took: \(time * 1000)ms")
        return result
    }
    
    func processImageAsync(_ image: UIImage) async -> UIImage? {
        let (result, time) = await PerformanceMeasurer.measureAsyncExecution {
            // Your async image processing code here
            return image
        }
        
        print("Async image processing took: \(time * 1000)ms")
        return result
    }
}
```

#### 2. Memory Usage Measurement:

```swift
import Foundation

class MemoryMeasurer {
    static func getCurrentMemoryUsage() -> UInt64 {
        var info = mach_task_basic_info()
        var count = mach_msg_type_number_t(MemoryLayout<mach_task_basic_info>.size)/4
        
        let kerr: kern_return_t = withUnsafeMutablePointer(to: &info) {
            $0.withMemoryRebound(to: integer_t.self, capacity: 1) {
                task_info(mach_task_self(),
                         task_flavor_t(MACH_TASK_BASIC_INFO),
                         $0,
                         &count)
            }
        }
        
        if kerr == KERN_SUCCESS {
            return info.resident_size
        }
        return 0
    }
    
    static func formatMemoryUsage(_ bytes: UInt64) -> String {
        let formatter = ByteCountFormatter()
        formatter.allowedUnits = [.useMB, .useGB]
        formatter.countStyle = .memory
        return formatter.string(fromByteCount: Int64(bytes))
    }
}
```

### Android Performance Measurement:

#### 1. Time Profiling:

```kotlin
import kotlin.system.measureTimeMillis

class PerformanceMeasurer {
    
    companion object {
        inline fun <T> measureExecutionTime(operation: () -> T): Pair<T, Long> {
            val startTime = System.currentTimeMillis()
            val result = operation()
            val endTime = System.currentTimeMillis()
            val executionTime = endTime - startTime
            
            return Pair(result, executionTime)
        }
        
        suspend inline fun <T> measureAsyncExecutionTime(operation: suspend () -> T): Pair<T, Long> {
            val startTime = System.currentTimeMillis()
            val result = operation()
            val endTime = System.currentTimeMillis()
            val executionTime = endTime - startTime
            
            return Pair(result, executionTime)
        }
    }
}

// Usage examples
class ImageProcessor {
    
    fun processImage(bitmap: Bitmap): Bitmap? {
        val (result, time) = PerformanceMeasurer.measureExecutionTime {
            // Your image processing code here
            return bitmap
        }
        
        println("Image processing took: ${time}ms")
        return result
    }
    
    suspend fun processImageAsync(bitmap: Bitmap): Bitmap? {
        val (result, time) = PerformanceMeasurer.measureAsyncExecutionTime {
            // Your async image processing code here
            return bitmap
        }
        
        println("Async image processing took: ${time}ms")
        return result
    }
}
```

#### 2. Memory Usage Measurement:

```kotlin
import android.app.ActivityManager
import android.content.Context

class MemoryMeasurer(private val context: Context) {
    
    fun getCurrentMemoryUsage(): Long {
        val activityManager = context.getSystemService(Context.ACTIVITY_SERVICE) as ActivityManager
        val memoryInfo = ActivityManager.MemoryInfo()
        activityManager.getMemoryInfo(memoryInfo)
        
        return memoryInfo.availMem
    }
    
    fun getTotalMemory(): Long {
        val activityManager = context.getSystemService(Context.ACTIVITY_SERVICE) as ActivityManager
        val memoryInfo = ActivityManager.MemoryInfo()
        activityManager.getMemoryInfo(memoryInfo)
        
        return memoryInfo.totalMem
    }
    
    fun formatMemoryUsage(bytes: Long): String {
        val mb = bytes / (1024 * 1024)
        return "${mb}MB"
    }
}
```

## Best Practices for Memory Management

### iOS Best Practices:

1. **Use Weak References in Closures:**
```swift
// ✅ Good
class ViewController: UIViewController {
    private var dataManager: DataManager?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        dataManager = DataManager()
        
        dataManager?.onDataReceived = { [weak self] data in
            self?.updateUI(with: data)
        }
    }
}

// ❌ Bad - Creates retain cycle
dataManager?.onDataReceived = { data in
    self.updateUI(with: data) // Strong reference to self
}
```

2. **Proper Delegate Pattern:**
```swift
// ✅ Good
protocol DataManagerDelegate: AnyObject {
    func didReceiveData(_ data: Data)
}

class DataManager {
    weak var delegate: DataManagerDelegate?
    
    func fetchData() {
        // Fetch data and notify delegate
        delegate?.didReceiveData(Data())
    }
}
```

3. **Use Unowned References Carefully:**
```swift
// ✅ Good - When you're certain the reference won't become nil
class NetworkManager {
    private let completionHandler: (Data) -> Void
    
    init(completion: @escaping (Data) -> Void) {
        self.completionHandler = completion
    }
    
    func fetchData() {
        // Use unowned when you're certain the reference is valid
        DispatchQueue.global().async { [unowned self] in
            let data = self.performNetworkRequest()
            self.completionHandler(data)
        }
    }
}
```

### Android Best Practices:

1. **Use Weak References:**
```kotlin
// ✅ Good
class DataManager {
    private var callback: WeakReference<DataCallback>? = null
    
    fun setCallback(callback: DataCallback) {
        this.callback = WeakReference(callback)
    }
    
    fun notifyDataReceived(data: String) {
        callback?.get()?.onDataReceived(data)
    }
}
```

2. **Proper Context Management:**
```kotlin
// ✅ Good
class MyActivity : AppCompatActivity() {
    private val dataManager by lazy { DataManager() }
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // Use application context for long-lived objects
        val longLivedManager = LongLivedManager(applicationContext)
    }
}
```

3. **Remove Listeners:**
```kotlin
// ✅ Good
class MyActivity : AppCompatActivity() {
    private var dataListener: DataListener? = null
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        dataListener = DataListener()
        dataManager.addListener(dataListener)
    }
    
    override fun onDestroy() {
        super.onDestroy()
        dataManager.removeListener(dataListener)
        dataListener = null
    }
}
```

## Common Memory Issues and Solutions

### 1. Image Memory Issues:

#### iOS:
```swift
// ✅ Good - Proper image memory management
class ImageCache {
    private let cache = NSCache<NSString, UIImage>()
    
    init() {
        cache.countLimit = 100 // Limit number of cached images
        cache.totalCostLimit = 50 * 1024 * 1024 // 50MB limit
    }
    
    func setImage(_ image: UIImage, forKey key: String) {
        let cost = Int(image.size.width * image.size.height * 4) // 4 bytes per pixel
        cache.setObject(image, forKey: key as NSString, cost: cost)
    }
    
    func getImage(forKey key: String) -> UIImage? {
        return cache.object(forKey: key as NSString)
    }
}
```

#### Android:
```kotlin
// ✅ Good - Proper image memory management
class ImageCache(context: Context) {
    private val cache = LruCache<String, Bitmap>(getCacheSize(context))
    
    private fun getCacheSize(context: Context): Int {
        val maxMemory = (Runtime.getRuntime().maxMemory() / 1024).toInt()
        return maxMemory / 8 // Use 1/8th of available memory
    }
    
    fun put(key: String, bitmap: Bitmap) {
        cache.put(key, bitmap)
    }
    
    fun get(key: String): Bitmap? {
        return cache.get(key)
    }
}
```

### 2. Network Request Memory Management:

#### iOS:
```swift
// ✅ Good - Proper network request management
class NetworkManager {
    private var activeRequests: [URLSessionDataTask] = []
    
    func fetchData(from url: URL, completion: @escaping (Result<Data, Error>) -> Void) {
        let task = URLSession.shared.dataTask(with: url) { [weak self] data, response, error in
            // Remove task from active requests
            DispatchQueue.main.async {
                self?.activeRequests.removeAll { $0 == task }
            }
            
            if let error = error {
                completion(.failure(error))
                return
            }
            
            if let data = data {
                completion(.success(data))
            }
        }
        
        activeRequests.append(task)
        task.resume()
    }
    
    func cancelAllRequests() {
        activeRequests.forEach { $0.cancel() }
        activeRequests.removeAll()
    }
}
```

#### Android:
```kotlin
// ✅ Good - Proper network request management
class NetworkManager {
    private val activeRequests = mutableListOf<Call>()
    
    fun fetchData(url: String, callback: NetworkCallback) {
        val call = apiService.getData(url)
        activeRequests.add(call)
        
        call.enqueue(object : Callback<ResponseBody> {
            override fun onResponse(call: Call, response: Response<ResponseBody>) {
                activeRequests.remove(call)
                // Handle response
            }
            
            override fun onFailure(call: Call, t: Throwable) {
                activeRequests.remove(call)
                // Handle failure
            }
        })
    }
    
    fun cancelAllRequests() {
        activeRequests.forEach { it.cancel() }
        activeRequests.clear()
    }
}
```

## Self-Reference Cycles and Retain Cycles

### iOS Retain Cycles:

#### 1. Closure Retain Cycles:
```swift
// ❌ WRONG - Creates retain cycle
class DataManager {
    var onDataReceived: ((String) -> Void)?
    
    func fetchData() {
        // This creates a retain cycle
        onDataReceived = { data in
            self.processData(data) // self is captured strongly
        }
    }
}

// ✅ CORRECT - Uses weak self
class DataManager {
    var onDataReceived: ((String) -> Void)?
    
    func fetchData() {
        onDataReceived = { [weak self] data in
            guard let self = self else { return }
            self.processData(data)
        }
    }
}
```

#### 2. Delegate Retain Cycles:
```swift
// ❌ WRONG - Strong delegate reference
class DataManager {
    var delegate: DataManagerDelegate? // Strong reference
}

// ✅ CORRECT - Weak delegate reference
class DataManager {
    weak var delegate: DataManagerDelegate? // Weak reference
}
```

#### 3. Timer Retain Cycles:
```swift
// ❌ WRONG - Timer retains self
class ViewController: UIViewController {
    var timer: Timer?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        timer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { _ in
            self.updateUI() // self is captured strongly
        }
    }
}

// ✅ CORRECT - Uses weak self
class ViewController: UIViewController {
    var timer: Timer?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        timer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { [weak self] _ in
            self?.updateUI()
        }
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        timer?.invalidate()
        timer = nil
    }
}
```

### Android Memory Leaks:

#### 1. Anonymous Class Memory Leaks:
```kotlin
// ❌ WRONG - Creates memory leak
class MyActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        button.setOnClickListener {
            // This captures the activity context strongly
            updateUI()
        }
    }
}

// ✅ CORRECT - Uses weak reference
class MyActivity : AppCompatActivity() {
    private val clickListener = View.OnClickListener {
        updateUI()
    }
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        button.setOnClickListener(clickListener)
    }
    
    override fun onDestroy() {
        super.onDestroy()
        button.setOnClickListener(null)
    }
}
```

#### 2. Handler Memory Leaks:
```kotlin
// ❌ WRONG - Handler can cause memory leaks
class MyActivity : AppCompatActivity() {
    private val handler = Handler()
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        handler.postDelayed({
            updateUI() // Captures activity context
        }, 5000)
    }
}

// ✅ CORRECT - Proper handler management
class MyActivity : AppCompatActivity() {
    private val handler = Handler(Looper.getMainLooper())
    private val updateRunnable = Runnable { updateUI() }
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        handler.postDelayed(updateRunnable, 5000)
    }
    
    override fun onDestroy() {
        super.onDestroy()
        handler.removeCallbacks(updateRunnable)
    }
}
```

## Real-World Examples

### Example 1: Photo Gallery App

#### iOS Implementation:
```swift
class PhotoGalleryViewController: UIViewController {
    private var photos: [Photo] = []
    private var imageCache = ImageCache()
    private var activeDownloads: [URLSessionDataTask] = []
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupCollectionView()
        loadPhotos()
    }
    
    private func loadPhotos() {
        PhotoService.shared.fetchPhotos { [weak self] result in
            DispatchQueue.main.async {
                switch result {
                case .success(let photos):
                    self?.photos = photos
                    self?.collectionView.reloadData()
                case .failure(let error):
                    self?.showError(error)
                }
            }
        }
    }
    
    private func downloadImage(for photo: Photo, at indexPath: IndexPath) {
        guard let url = photo.imageURL else { return }
        
        // Check cache first
        if let cachedImage = imageCache.getImage(forKey: photo.id) {
            updateCell(at: indexPath, with: cachedImage)
            return
        }
        
        let task = URLSession.shared.dataTask(with: url) { [weak self] data, response, error in
            DispatchQueue.main.async {
                // Remove from active downloads
                self?.activeDownloads.removeAll { $0 == task }
                
                if let data = data, let image = UIImage(data: data) {
                    // Cache the image
                    self?.imageCache.setImage(image, forKey: photo.id)
                    self?.updateCell(at: indexPath, with: image)
                }
            }
        }
        
        activeDownloads.append(task)
        task.resume()
    }
    
    private func updateCell(at indexPath: IndexPath, with image: UIImage) {
        guard let cell = collectionView.cellForItem(at: indexPath) as? PhotoCell else { return }
        cell.configure(with: image)
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        // Cancel active downloads when leaving the view
        activeDownloads.forEach { $0.cancel() }
        activeDownloads.removeAll()
    }
    
    deinit {
        print("PhotoGalleryViewController deallocated")
    }
}
```

#### Android Implementation:
```kotlin
class PhotoGalleryActivity : AppCompatActivity() {
    private lateinit var binding: ActivityPhotoGalleryBinding
    private val photos = mutableListOf<Photo>()
    private val imageCache = ImageCache(this)
    private val activeDownloads = mutableListOf<Call>()
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityPhotoGalleryBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        setupRecyclerView()
        loadPhotos()
    }
    
    private fun loadPhotos() {
        PhotoService.fetchPhotos(object : Callback<List<Photo>> {
            override fun onResponse(call: Call<List<Photo>>, response: Response<List<Photo>>) {
                response.body()?.let { photoList ->
                    photos.clear()
                    photos.addAll(photoList)
                    binding.recyclerView.adapter?.notifyDataSetChanged()
                }
            }
            
            override fun onFailure(call: Call<List<Photo>>, t: Throwable) {
                showError(t.message ?: "Unknown error")
            }
        })
    }
    
    private fun downloadImage(photo: Photo, position: Int) {
        // Check cache first
        imageCache.get(photo.id)?.let { bitmap ->
            updateViewHolder(position, bitmap)
            return
        }
        
        val call = PhotoService.downloadImage(photo.imageUrl)
        activeDownloads.add(call)
        
        call.enqueue(object : Callback<ResponseBody> {
            override fun onResponse(call: Call<ResponseBody>, response: Response<ResponseBody>) {
                activeDownloads.remove(call)
                
                response.body()?.let { responseBody ->
                    val bitmap = BitmapFactory.decodeStream(responseBody.byteStream())
                    bitmap?.let {
                        imageCache.put(photo.id, it)
                        updateViewHolder(position, it)
                    }
                }
            }
            
            override fun onFailure(call: Call<ResponseBody>, t: Throwable) {
                activeDownloads.remove(call)
            }
        })
    }
    
    private fun updateViewHolder(position: Int, bitmap: Bitmap) {
        val holder = binding.recyclerView.findViewHolderForAdapterPosition(position)
        holder?.let {
            if (it is PhotoViewHolder) {
                it.bind(bitmap)
            }
        }
    }
    
    override fun onDestroy() {
        super.onDestroy()
        // Cancel active downloads
        activeDownloads.forEach { it.cancel() }
        activeDownloads.clear()
    }
}
```

### Example 2: Real-time Data Streaming App

#### iOS Implementation:
```swift
class DataStreamViewController: UIViewController {
    private var dataStream: DataStream?
    private var updateTimer: Timer?
    private var dataPoints: [DataPoint] = []
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
        startDataStream()
    }
    
    private func startDataStream() {
        dataStream = DataStream()
        
        // Use weak self to avoid retain cycle
        dataStream?.onDataReceived = { [weak self] dataPoint in
            DispatchQueue.main.async {
                self?.handleNewData(dataPoint)
            }
        }
        
        // Start periodic updates
        updateTimer = Timer.scheduledTimer(withTimeInterval: 1.0, repeats: true) { [weak self] _ in
            self?.updateUI()
        }
    }
    
    private func handleNewData(_ dataPoint: DataPoint) {
        dataPoints.append(dataPoint)
        
        // Keep only last 1000 data points to prevent memory growth
        if dataPoints.count > 1000 {
            dataPoints.removeFirst(dataPoints.count - 1000)
        }
        
        updateChart()
    }
    
    private func updateChart() {
        // Update chart with new data
        chartView.update(with: dataPoints)
    }
    
    private func updateUI() {
        // Update UI elements
        statusLabel.text = "Data points: \(dataPoints.count)"
    }
    
    override func viewWillDisappear(_ animated: Bool) {
        super.viewWillDisappear(animated)
        stopDataStream()
    }
    
    private func stopDataStream() {
        dataStream?.stop()
        dataStream = nil
        
        updateTimer?.invalidate()
        updateTimer = nil
    }
    
    deinit {
        print("DataStreamViewController deallocated")
    }
}
```

#### Android Implementation:
```kotlin
class DataStreamActivity : AppCompatActivity() {
    private lateinit var binding: ActivityDataStreamBinding
    private var dataStream: DataStream? = null
    private val dataPoints = mutableListOf<DataPoint>()
    private val updateHandler = Handler(Looper.getMainLooper())
    private val updateRunnable = Runnable { updateUI() }
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityDataStreamBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        setupUI()
        startDataStream()
    }
    
    private fun startDataStream() {
        dataStream = DataStream()
        
        dataStream?.setOnDataReceivedListener(object : DataStream.OnDataReceivedListener {
            override fun onDataReceived(dataPoint: DataPoint) {
                runOnUiThread {
                    handleNewData(dataPoint)
                }
            }
        })
        
        // Start periodic updates
        updateHandler.postDelayed(updateRunnable, 1000)
    }
    
    private fun handleNewData(dataPoint: DataPoint) {
        dataPoints.add(dataPoint)
        
        // Keep only last 1000 data points to prevent memory growth
        if (dataPoints.size > 1000) {
            dataPoints.removeAt(0)
        }
        
        updateChart()
    }
    
    private fun updateChart() {
        // Update chart with new data
        binding.chartView.update(dataPoints)
    }
    
    private fun updateUI() {
        // Update UI elements
        binding.statusText.text = "Data points: ${dataPoints.size}"
        
        // Schedule next update
        updateHandler.postDelayed(updateRunnable, 1000)
    }
    
    override fun onDestroy() {
        super.onDestroy()
        stopDataStream()
    }
    
    private fun stopDataStream() {
        dataStream?.stop()
        dataStream = null
        
        updateHandler.removeCallbacks(updateRunnable)
    }
}
```

## Conclusion

Memory management and performance optimization are ongoing processes in mobile app development. By using the right tools and following best practices, you can create apps that are both performant and memory-efficient.

### Key Takeaways:

1. **Always use profiling tools** to identify memory issues early
2. **Implement proper memory management patterns** to prevent leaks
3. **Monitor performance metrics** continuously
4. **Use weak references** to avoid retain cycles
5. **Clean up resources** properly in lifecycle methods
6. **Test on real devices** with different memory constraints
7. **Implement memory limits** for caches and data structures
8. **Use appropriate data structures** for your use case

### Tools Summary:

**iOS:**
- Instruments (Xcode)
- Memory Graph Debugger
- Xcode Memory Report
- Time Profiler

**Android:**
- Android Profiler
- LeakCanary
- Android Memory Analyzer (MAT)
- CPU Profiler

By following this comprehensive guide and using the appropriate tools for your platform, you'll be well-equipped to create high-performance, memory-efficient mobile applications.
