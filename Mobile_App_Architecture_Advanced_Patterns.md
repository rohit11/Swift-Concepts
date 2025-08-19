# Mobile App Architecture Advanced Patterns (Continuation)

## Table of Contents
1. [Coordinator Pattern](#coordinator-pattern)
2. [MVI (Model-View-Intent)](#mvi-model-view-intent)
3. [TCA (The Composable Architecture)](#tca-the-composable-architecture)
4. [BLoC (Business Logic Component)](#bloc-business-logic-component)
5. [Redux/Flux Pattern](#reduxflux-pattern)
6. [Repository Pattern](#repository-pattern)
7. [Team Scaling & Multi-Team Considerations](#team-scaling--multi-team-considerations)
8. [Migration Strategies](#migration-strategies)
9. [Performance Considerations](#performance-considerations)
10. [Testing Strategies](#testing-strategies)

---

## Coordinator Pattern

### What is the Coordinator Pattern?
The Coordinator pattern is a navigation architecture that centralizes navigation logic and decouples view controllers from navigation concerns. It's particularly useful for complex navigation flows and deep linking.

### Coordinator Pattern Diagram
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   AppDelegate   │    │   Coordinator   │    │  ViewController │
│                 │    │                 │    │                 │
│ - App Lifecycle│◄──►│ - Navigation    │◄──►│ - UI Logic      │
│ - Deep Linking │    │ - Flow Control  │    │ - User Input    │
│ - Root Setup   │    │ - Child Coord   │    │ - Data Display  │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                                │
                                ▼
                       ┌─────────────────┐
                       │  Child Coord    │
                       │                 │
                       │ - Sub-flows     │
                       │ - Navigation    │
                       │ - Dependencies  │
                       └─────────────────┘
```

### Swift (iOS) Implementation
```swift
// MARK: - Coordinator Protocol
protocol Coordinator: AnyObject {
    var childCoordinators: [Coordinator] { get set }
    var navigationController: UINavigationController { get set }
    
    func start()
}

// MARK: - Base Coordinator
class BaseCoordinator: Coordinator {
    var childCoordinators: [Coordinator] = []
    var navigationController: UINavigationController
    
    init(navigationController: UINavigationController) {
        self.navigationController = navigationController
    }
    
    func start() {
        // Override in subclasses
    }
    
    func addChildCoordinator(_ coordinator: Coordinator) {
        childCoordinators.append(coordinator)
    }
    
    func removeChildCoordinator(_ coordinator: Coordinator) {
        childCoordinators = childCoordinators.filter { $0 !== coordinator }
    }
}

// MARK: - Main Coordinator
class MainCoordinator: BaseCoordinator {
    override func start() {
        let userProfileVC = UserProfileViewController()
        userProfileVC.coordinator = self
        navigationController.pushViewController(userProfileVC, animated: false)
    }
    
    func showUserSettings() {
        let settingsCoordinator = UserSettingsCoordinator(navigationController: navigationController)
        addChildCoordinator(settingsCoordinator)
        settingsCoordinator.start()
    }
    
    func showUserDetails(for user: User) {
        let detailsCoordinator = UserDetailsCoordinator(navigationController: navigationController, user: user)
        addChildCoordinator(detailsCoordinator)
        detailsCoordinator.start()
    }
}

// MARK: - User Settings Coordinator
class UserSettingsCoordinator: BaseCoordinator {
    override func start() {
        let settingsVC = UserSettingsViewController()
        settingsVC.coordinator = self
        navigationController.pushViewController(settingsVC, animated: true)
    }
    
    func showPrivacySettings() {
        let privacyVC = PrivacySettingsViewController()
        privacyVC.coordinator = self
        navigationController.pushViewController(privacyVC, animated: true)
    }
}

// MARK: - App Delegate Integration
@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?
    var coordinator: MainCoordinator?
    
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        window = UIWindow(frame: UIScreen.main.bounds)
        
        let navigationController = UINavigationController()
        coordinator = MainCoordinator(navigationController: navigationController)
        coordinator?.start()
        
        window?.rootViewController = navigationController
        window?.makeKeyAndVisible()
        
        return true
    }
}
```

### Kotlin (Android) Implementation
```kotlin
// MARK: - Coordinator Interface
interface Coordinator {
    fun start()
    fun stop()
}

// MARK: - Base Coordinator
abstract class BaseCoordinator : Coordinator {
    private val childCoordinators = mutableListOf<Coordinator>()
    
    protected fun addChildCoordinator(coordinator: Coordinator) {
        childCoordinators.add(coordinator)
    }
    
    protected fun removeChildCoordinator(coordinator: Coordinator) {
        childCoordinators.remove(coordinator)
    }
    
    override fun stop() {
        childCoordinators.forEach { it.stop() }
        childCoordinators.clear()
    }
}

// MARK: - Main Coordinator
class MainCoordinator(
    private val activity: Activity,
    private val container: ViewGroup
) : BaseCoordinator() {
    
    override fun start() {
        val userProfileFragment = UserProfileFragment()
        userProfileFragment.coordinator = this
        
        activity.supportFragmentManager.beginTransaction()
            .replace(container.id, userProfileFragment)
            .commit()
    }
    
    fun showUserSettings() {
        val settingsCoordinator = UserSettingsCoordinator(activity, container)
        addChildCoordinator(settingsCoordinator)
        settingsCoordinator.start()
    }
    
    fun showUserDetails(user: User) {
        val detailsCoordinator = UserDetailsCoordinator(activity, container, user)
        addChildCoordinator(detailsCoordinator)
        detailsCoordinator.start()
    }
}

// MARK: - User Settings Coordinator
class UserSettingsCoordinator(
    private val activity: Activity,
    private val container: ViewGroup
) : BaseCoordinator() {
    
    override fun start() {
        val settingsFragment = UserSettingsFragment()
        settingsFragment.coordinator = this
        
        activity.supportFragmentManager.beginTransaction()
            .replace(container.id, settingsFragment)
            .addToBackStack(null)
            .commit()
    }
    
    fun showPrivacySettings() {
        val privacyFragment = PrivacySettingsFragment()
        privacyFragment.coordinator = this
        
        activity.supportFragmentManager.beginTransaction()
            .replace(container.id, privacyFragment)
            .addToBackStack(null)
            .commit()
    }
}
```

### TypeScript (React Native) Implementation
```typescript
// MARK: - Coordinator Interface
interface Coordinator {
    start(): void;
    stop(): void;
}

// MARK: - Base Coordinator
abstract class BaseCoordinator implements Coordinator {
    protected childCoordinators: Coordinator[] = [];
    
    protected addChildCoordinator(coordinator: Coordinator): void {
        this.childCoordinators.push(coordinator);
    }
    
    protected removeChildCoordinator(coordinator: Coordinator): void {
        this.childCoordinators = this.childCoordinators.filter(c => c !== coordinator);
    }
    
    stop(): void {
        this.childCoordinators.forEach(coordinator => coordinator.stop());
        this.childCoordinators = [];
    }
}

// MARK: - Main Coordinator
class MainCoordinator extends BaseCoordinator {
    constructor(private navigation: NavigationContainerRef) {
        super();
    }
    
    start(): void {
        this.navigation.navigate('UserProfile');
    }
    
    showUserSettings(): void {
        const settingsCoordinator = new UserSettingsCoordinator(this.navigation);
        this.addChildCoordinator(settingsCoordinator);
        settingsCoordinator.start();
    }
    
    showUserDetails(user: User): void {
        const detailsCoordinator = new UserDetailsCoordinator(this.navigation, user);
        this.addChildCoordinator(detailsCoordinator);
        detailsCoordinator.start();
    }
}

// MARK: - User Settings Coordinator
class UserSettingsCoordinator extends BaseCoordinator {
    constructor(private navigation: NavigationContainerRef) {
        super();
    }
    
    start(): void {
        this.navigation.navigate('UserSettings');
    }
    
    showPrivacySettings(): void {
        this.navigation.navigate('PrivacySettings');
    }
}

// MARK: - Navigation Setup
const App: React.FC = () => {
    const navigationRef = useRef<NavigationContainerRef>(null);
    const coordinator = useMemo(() => new MainCoordinator(navigationRef.current!), []);
    
    useEffect(() => {
        coordinator.start();
    }, [coordinator]);
    
    return (
        <NavigationContainer ref={navigationRef}>
            <Stack.Navigator>
                <Stack.Screen name="UserProfile" component={UserProfileScreen} />
                <Stack.Screen name="UserSettings" component={UserSettingsScreen} />
                <Stack.Screen name="PrivacySettings" component={PrivacySettingsScreen} />
            </Stack.Navigator>
        </NavigationContainer>
    );
};
```

### When to Use Coordinator Pattern
```yaml
✅ Use Coordinator Pattern When:
- Complex navigation flows
- Deep linking requirements
- Multiple navigation stacks
- Team size 6+ developers
- Complex app architecture
- Need for navigation testing
- Modular navigation

❌ Don't Use Coordinator Pattern When:
- Simple navigation
- Single developer projects
- Quick prototypes
- Simple tab-based apps
- Limited navigation complexity
```

---

## MVI (Model-View-Intent)

### What is MVI?
MVI is a unidirectional data flow architecture that emphasizes predictable state management through a single source of truth. It's particularly useful for complex state management and reactive applications.

### MVI Architecture Diagram
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│      View       │    │      Model      │    │     Intent      │
│                 │    │                 │    │                 │
│ - UI Elements  │◄──►│ - State        │◄──►│ - User Actions  │
│ - State Display│    │ - Business Logic│    │ - System Events │
│ - Intent Emit  │    │ - Data Models   │    │ - Navigation    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                                │
                                ▼
                       ┌─────────────────┐
                       │   Reducer       │
                       │                 │
                       │ - State Updates │
                       │ - Side Effects  │
                       │ - Business Rules│
                       └─────────────────┘
```

### Swift (iOS) Implementation
```swift
// MARK: - Model (State)
struct UserProfileState {
    let user: User?
    let isLoading: Bool
    let errorMessage: String?
    
    static let initial = UserProfileState(user: nil, isLoading: false, errorMessage: nil)
}

// MARK: - Intent
enum UserProfileIntent {
    case viewDidLoad
    case retryTapped
    case userTapped
}

// MARK: - Reducer
class UserProfileReducer {
    func reduce(state: UserProfileState, intent: UserProfileIntent) -> UserProfileState {
        switch intent {
        case .viewDidLoad:
            return UserProfileState(user: nil, isLoading: true, errorMessage: nil)
        case .retryTapped:
            return UserProfileState(user: nil, isLoading: true, errorMessage: nil)
        case .userTapped:
            return state
        }
    }
    
    func reduce(state: UserProfileState, event: UserProfileEvent) -> UserProfileState {
        switch event {
        case .userLoaded(let user):
            return UserProfileState(user: user, isLoading: false, errorMessage: nil)
        case .userLoadFailed(let error):
            return UserProfileState(user: nil, isLoading: false, errorMessage: error.localizedDescription)
        }
    }
}

// MARK: - Event
enum UserProfileEvent {
    case userLoaded(User)
    case userLoadFailed(Error)
}

// MARK: - ViewModel
class UserProfileViewModel: ObservableObject {
    @Published private(set) var state: UserProfileState = .initial
    
    private let reducer = UserProfileReducer()
    private let userService: UserServiceProtocol
    
    init(userService: UserServiceProtocol) {
        self.userService = userService
    }
    
    func dispatch(_ intent: UserProfileIntent) {
        state = reducer.reduce(state: state, intent: intent)
        
        switch intent {
        case .viewDidLoad, .retryTapped:
            loadUser()
        case .userTapped:
            // Handle user tap
            break
        }
    }
    
    private func loadUser() {
        userService.fetchUser { [weak self] result in
            DispatchQueue.main.async {
                let event: UserProfileEvent
                switch result {
                case .success(let user):
                    event = .userLoaded(user)
                case .failure(let error):
                    event = .userLoadFailed(error)
                }
                self?.state = self?.reducer.reduce(state: self?.state ?? .initial, event: event) ?? .initial
            }
        }
    }
}

// MARK: - View
struct UserProfileView: View {
    @StateObject private var viewModel = UserProfileViewModel(userService: UserService())
    
    var body: some View {
        VStack {
            if viewModel.state.isLoading {
                ProgressView()
            } else if let error = viewModel.state.errorMessage {
                VStack {
                    Text("Error: \(error)")
                    Button("Retry") {
                        viewModel.dispatch(.retryTapped)
                    }
                }
            } else if let user = viewModel.state.user {
                VStack {
                    Text(user.name)
                        .font(.title)
                    Text(user.email)
                        .font(.subheadline)
                }
                .onTapGesture {
                    viewModel.dispatch(.userTapped)
                }
            }
        }
        .onAppear {
            viewModel.dispatch(.viewDidLoad)
        }
    }
}
```

### Kotlin (Android) Implementation
```kotlin
// MARK: - Model (State)
data class UserProfileState(
    val user: User? = null,
    val isLoading: Boolean = false,
    val errorMessage: String? = null
)

// MARK: - Intent
sealed class UserProfileIntent {
    object ViewDidLoad : UserProfileIntent()
    object RetryTapped : UserProfileIntent()
    object UserTapped : UserProfileIntent()
}

// MARK: - Event
sealed class UserProfileEvent {
    data class UserLoaded(val user: User) : UserProfileEvent()
    data class UserLoadFailed(val error: Throwable) : UserProfileEvent()
}

// MARK: - Reducer
class UserProfileReducer {
    fun reduce(state: UserProfileState, intent: UserProfileIntent): UserProfileState {
        return when (intent) {
            is UserProfileIntent.ViewDidLoad -> state.copy(isLoading = true, errorMessage = null)
            is UserProfileIntent.RetryTapped -> state.copy(isLoading = true, errorMessage = null)
            is UserProfileIntent.UserTapped -> state
        }
    }
    
    fun reduce(state: UserProfileState, event: UserProfileEvent): UserProfileState {
        return when (event) {
            is UserProfileEvent.UserLoaded -> state.copy(user = event.user, isLoading = false, errorMessage = null)
            is UserProfileEvent.UserLoadFailed -> state.copy(isLoading = false, errorMessage = event.error.message)
        }
    }
}

// MARK: - ViewModel
class UserProfileViewModel : ViewModel() {
    private val _state = MutableLiveData(UserProfileState())
    val state: LiveData<UserProfileState> = _state
    
    private val reducer = UserProfileReducer()
    private val userService = UserService()
    
    fun dispatch(intent: UserProfileIntent) {
        _state.value = reducer.reduce(_state.value ?: UserProfileState(), intent)
        
        when (intent) {
            is UserProfileIntent.ViewDidLoad, is UserProfileIntent.RetryTapped -> loadUser()
            is UserProfileIntent.UserTapped -> handleUserTap()
        }
    }
    
    private fun loadUser() {
        viewModelScope.launch {
            try {
                val user = userService.fetchUser()
                val event = UserProfileEvent.UserLoaded(user)
                _state.value = reducer.reduce(_state.value ?: UserProfileState(), event)
            } catch (error: Exception) {
                val event = UserProfileEvent.UserLoadFailed(error)
                _state.value = reducer.reduce(_state.value ?: UserProfileState(), event)
            }
        }
    }
    
    private fun handleUserTap() {
        // Handle user tap
    }
}
```

### TypeScript (React Native) Implementation
```typescript
// MARK: - Model (State)
interface UserProfileState {
    user: User | null;
    isLoading: boolean;
    errorMessage: string | null;
}

const initialState: UserProfileState = {
    user: null,
    isLoading: false,
    errorMessage: null
};

// MARK: - Intent
type UserProfileIntent = 
    | { type: 'VIEW_DID_LOAD' }
    | { type: 'RETRY_TAPPED' }
    | { type: 'USER_TAPPED' };

// MARK: - Event
type UserProfileEvent = 
    | { type: 'USER_LOADED'; user: User }
    | { type: 'USER_LOAD_FAILED'; error: Error };

// MARK: - Reducer
class UserProfileReducer {
    reduce(state: UserProfileState, intent: UserProfileIntent): UserProfileState {
        switch (intent.type) {
            case 'VIEW_DID_LOAD':
            case 'RETRY_TAPPED':
                return { ...state, isLoading: true, errorMessage: null };
            case 'USER_TAPPED':
                return state;
            default:
                return state;
        }
    }
    
    reduce(state: UserProfileState, event: UserProfileEvent): UserProfileState {
        switch (event.type) {
            case 'USER_LOADED':
                return { ...state, user: event.user, isLoading: false, errorMessage: null };
            case 'USER_LOAD_FAILED':
                return { ...state, isLoading: false, errorMessage: event.error.message };
            default:
                return state;
        }
    }
}

// MARK: - ViewModel Hook
const useUserProfileViewModel = () => {
    const [state, setState] = useState<UserProfileState>(initialState);
    const reducer = useMemo(() => new UserProfileReducer(), []);
    const userService = useMemo(() => new UserService(), []);
    
    const dispatch = useCallback((intent: UserProfileIntent) => {
        setState(currentState => reducer.reduce(currentState, intent));
        
        switch (intent.type) {
            case 'VIEW_DID_LOAD':
            case 'RETRY_TAPPED':
                loadUser();
                break;
            case 'USER_TAPPED':
                handleUserTap();
                break;
        }
    }, []);
    
    const loadUser = useCallback(async () => {
        try {
            const user = await userService.fetchUser();
            const event: UserProfileEvent = { type: 'USER_LOADED', user };
            setState(currentState => reducer.reduce(currentState, event));
        } catch (error) {
            const event: UserProfileEvent = { type: 'USER_LOAD_FAILED', error };
            setState(currentState => reducer.reduce(currentState, event));
        }
    }, [userService, reducer]);
    
    const handleUserTap = useCallback(() => {
        // Handle user tap
    }, []);
    
    return {
        state,
        dispatch
    };
};

// MARK: - View
const UserProfileView: React.FC = () => {
    const { state, dispatch } = useUserProfileViewModel();
    
    useEffect(() => {
        dispatch({ type: 'VIEW_DID_LOAD' });
    }, [dispatch]);
    
    if (state.isLoading) {
        return <LoadingView />;
    }
    
    if (state.errorMessage) {
        return (
            <ErrorView 
                message={state.errorMessage} 
                onRetry={() => dispatch({ type: 'RETRY_TAPPED' })} 
            />
        );
    }
    
    if (!state.user) {
        return null;
    }
    
    return (
        <TouchableOpacity onPress={() => dispatch({ type: 'USER_TAPPED' })}>
            <View style={styles.container}>
                <Text style={styles.name}>{state.user.name}</Text>
                <Text style={styles.email}>{state.user.email}</Text>
            </View>
        </TouchableOpacity>
    );
};
```

### When to Use MVI
```yaml
✅ Use MVI When:
- Complex state management
- Unidirectional data flow
- Team size 6+ developers
- Reactive applications
- Complex UI interactions
- Need for predictable state
- Multiple state sources

❌ Don't Use MVI When:
- Simple applications
- Single developer projects
- Quick prototypes
- Simple state management
- Limited UI complexity
```

---

## Conclusion

This continuation document covers advanced mobile app architecture patterns that complement the main architecture guide:

### **Advanced Patterns Covered:**
1. **Coordinator Pattern**: Navigation management and flow control
2. **MVI (Model-View-Intent)**: Unidirectional data flow architecture

### **Key Benefits:**
- **Coordinator Pattern**: Centralized navigation, testable flows, modular navigation
- **MVI**: Predictable state, unidirectional flow, complex state management

### **Implementation Examples:**
- **Swift (iOS)**: Full coordinator and MVI implementations
- **Kotlin (Android)**: Android-specific coordinator and MVI patterns
- **TypeScript (React Native)**: React Native coordinator and MVI hooks

### **Use Cases:**
- **Coordinator**: Complex navigation, deep linking, modular apps
- **MVI**: Complex state, reactive UI, predictable data flow

These patterns provide additional architectural options for complex mobile applications and can be combined with the core architectures (MVC, MVP, MVVM, VIPER, Clean) to create robust, maintainable, and scalable mobile applications.
