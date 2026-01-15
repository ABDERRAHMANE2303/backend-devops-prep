## 1️⃣ Java Fundamentals (Must be crystal clear)

### Object-Oriented Programming (each concept = 1 checkbox)

- **Encapsulation**
    - encapsulation allows protecting domain/businees model/data , instead of exposing fields they can be modified with controlled methods , example . wont expose a setemail but something like deactivate it …
- **Abstraction** — interfaces vs abstract classes (Spring use case)
    - the goal is abstracting behavior and only defining what can be done , the difference is
        - interfaces are used when the classes implementing them are **not related by nature**, only by behavior. (example of notification service , define the notification metho in an interface , implementation would be email sms ….)
        - abstract classes are used when subclasses are supposed to share some parts of the process but override others , file processing as an example , to avoid redoing validation…we can treat that and subclasses only treat the part specific to the file format .

| Interface | Abstract Class |
| --- | --- |
| Describes **capability** | Describes **is-a relationship** |
| Multiple implementations | Single inheritance |
| No state (mostly) | Can hold state |

```java
public abstract class AbstractFileProcessor {

    public final void process(File file) {
        validate(file);
        open(file);
        processInternal(file); // variable part
        close(file);
    }

    protected void validate(File file) {
        // shared validation logic
    }

    protected void open(File file) {
        // shared open logic
    }
		//to be overriden in subclasses , @Override
    protected abstract void processInternal(File file);
		
    protected void close(File file) {
        // shared close logic
    }
}

```

- **Inheritance**
    - inheritence : what u are , is a
    - composition : what u have , injecting behavior , has a
    - a car is a vehicle (inheritence) , a car has an engine ()

```java
public interface Engine {
    void start();
}

public class ElectricEngine implements Engine {
    public void start() {
        System.out.println("Electric engine");
    }
}

public class Vehicle {
    private final Engine engine; // composition , sort of injecting engine 

    public Vehicle(Engine engine) {
        this.engine = engine;
    }

    public void start() {
        engine.start();
    }
}
```

- **Polymorphism** — **The ability to treat different implementations as the same type and have behavior resolved at runtime.**
    - same interface diffrent business logic , in spring boot we inject interfaces usually and bean behavior differs based on which implementation is used (**Runtime polymorphism via dependency injection**)
    - Interfaces enable polymorphism, but polymorphism is the runtime selection of the implementation.
    
    ```java
    @Service
    public class OrderService {
    
        private final PaymentService paymentService; //could be stripe or paypal
    
        public OrderService(PaymentService paymentService) {
            this.paymentService = paymentService;
        }
    
        public void processOrder(double amount) {
            paymentService.pay(amount); // polymorphic call
        }
    }
    ```
    

### JAVA access modifiers & keywords

- **`public`**
    - Accessible **from anywhere**
    - Used for APIs, controllers, service methods
- **`private`**
    - Accessible **only inside the class**
    - Used for fields to enforce encapsulation
- **`protected`**
    - Accessible in:
        - same package
        - subclasses (even in other packages)
    - Often used in **abstract classes** for extension points
- **(default / package-private)** *(often forgotten)*
    - No keyword
    - Accessible **only within the same package**
    - Useful for internal implementation details
- **`final`**
    - Used for immutability & safety
    - freezes the reference, not the object’s internal state.
    - **`final class`** → cannot be extended
    - **`final method`** → cannot be overridden
    - **`final variable`** → value cannot change , cuz the value here is the reference
- **`static`**
    - Belongs to the class, not the instance
    - Used for utilities, constants
- **`abstract`**
    - Cannot be instantiated , ever .
    - Forces subclasses to implement missing behavior

| Keyword | Used for |
| --- | --- |
| `private` | Protect state |
| `public` | Expose behavior |
| `protected` | Extension points |
| package-private | Internal APIs |
| `final` | Safety & immutability |
| `static` | Shared, stateless |
| `abstract` | Controlled inheritance |
- a static method cannot be overriden cuz it belongs to the class not to the instance while a final method cannot be overriden cuz thats its job by default , one is by design  the other is by nature .
- A static field is shared across all instances because it belongs to the class, while a final field cannot change after initialization because immutability is its purpose — one is about ownership and scope , the other about mutability.

### Core Java Collections

- **most used collections**

| Type | Use Case / Description | Common Operations / Complexity | Notes |
| --- | --- | --- | --- |
| `ArrayList` | Dynamic ordered list, random access, iterate over elements | `get(i)` O(1), `add()` amortized O(1), `remove()` O(n) | Most used List in backend |
| Array (`T[]`) | Fixed-size collection, primitives or objects | `get(i)` O(1), `set(i)` O(1) | Low-level, memory efficient, cannot grow |
| `HashSet` | Store unique elements, fast lookup | `add()` O(1), `contains()` O(1), `remove()` O(1) | Most used Set, unordered |
| `HashMap<K,V>` | Key → value mapping, fast lookup | `put()` O(1), `get()` O(1), `remove()` O(1) | Most used Map, unordered, use LinkedHashMap if insertion order matters |
| `enum` | Fixed set of constants, type-safe | `values()`, `valueOf()` O(1) | Often used for roles, statuses, states |
- **Concrete Declarations**

```java
import java.util.*;

public class CollectionsDemo {

    public static void main(String[] args) {

        // ===== ArrayList =====
        List<String> users = new ArrayList<>();
        users.add("Alice");              // O(1) amortized
        users.add("Bob");
        users.get(0);                    // O(1)
        users.remove("Bob");             // O(n)

        // ===== Array =====
        int[] numbers = {1, 2, 3};
        int first = numbers[0];          // O(1)
        numbers[1] = 20;                 // O(1)
        // numbers[3] = 4;               // ❌ out of bounds

        // ===== HashSet =====
        Set<String> roles = new HashSet<>();
        roles.add("ADMIN");              // O(1)
        roles.add("ADMIN");              // ignored (unique)
        roles.contains("ADMIN");         // O(1)
        roles.remove("ADMIN");           // O(1)

        // ===== HashMap =====
        Map<Integer, String> userMap = new HashMap<>();
        userMap.put(1, "Alice");         // O(1)
        userMap.get(1);                  // O(1)
        userMap.containsKey(1);          // O(1)
        userMap.remove(1);               // O(1)

        // ===== Enum =====
        Role role = Role.ADMIN;
        Role[] allRoles = Role.values(); // O(1)
        Role fromString = Role.valueOf("USER"); // O(1)
    }

    enum Role {
        ADMIN, USER, GUEST
    }
}
```

- **other less frequently used collections :**

| Type | Use Case / Description | Notes |
| --- | --- | --- |
| `LinkedList` | Doubly linked list, fast insert/remove in middle | Rarely used, mostly queue-like operations |
| `TreeMap` | Sorted key → value mapping | O(log n) access, iteration in key order |
| `LinkedHashMap` | Key → value mapping, preserves insertion order | Useful for predictable iteration order |
| `TreeSet` | Sorted unique elements | O(log n) operations, rarely needed |
| `CopyOnWriteArrayList` | Thread-safe list for concurrent read-heavy scenarios | Expensive writes, good for listeners/event lists |
| `ConcurrentHashMap` | Thread-safe map | Great for backend multi-threaded caching or counters |

### Object Equality & Null Safety

- If two objects are equal, they must have the same hash code.
- `equals()` checks logical equality, `hashCode()` ensures consistent behavior in hash-based collections, and `Optional` wraps potentially null values to enforce null-safety in method returns.
- If two objects are equal, they must have the same hash code , by defqult java checks equality by checking the in memory reference of an object , but in business logic sometimes equality is defined based on other factor , 2 users with the same id …. so we override equals and hashcode to better suit our business logic rather than using the default one .
- hashets also can face issues since they check the hashcode before checking equals so 2 instances even if same fields are applied would result in diffrent hashcodes .

```java
Set<User> users = new HashSet<>();

users.add(new User(1, "Alice"));
users.contains(new User(1, "Alice")); // ❌ false if not overridden
```

### Concurrency (high-level)

- A process is an independent program with its own memory; a thread is a lightweight unit inside a process sharing memory. Thread safety matters to avoid race conditions when multiple threads access shared resources. In Java, we can achieve this via `synchronized` blocks/methods or thread-safe collections, and `synchronized` essentially acts as a mutex.
- A Java application = **1 process + many threads**
- Hardware threads limit parallelism, while OS threads define concurrency; many OS threads can be multiplexed onto a small number of hardware threads.

### Clean Code & Design

- SOLID ensures maintainable, readable, and extendable code. SRP splits responsibilities, OCP allows extension without modification, LSP guarantees subclass substitutability, ISP avoids bloated interfaces, and DIP decouples high-level logic from low-level implementations.
    - **S — Single Responsibility Principle (SRP):** Each class should have **one reason to change**, focusing on a single responsibility.
    - **O — Open/Closed Principle (OCP):** Software should be **open for extension, closed for modification**.
    - **L — Liskov Substitution Principle (LSP):** Subtypes must be **substitutable for their base types** without breaking behavior , ie child classes must honor parent’s contract , having a class penguin extending a class bird with a method fly while penguins cant do so would cause runtime problems when another class uses a penguin in a context where it should be able to fly in .
    - **I — Interface Segregation Principle (ISP):** Prefer **many small, specific interfaces** over one large, bloated interface.
    - **D — Dependency Inversion Principle (DIP):** High-level modules should **depend on abstractions**, not concrete implementations.
    
    ```java
    // SRP: This interface is ONLY about payment processing
    // ISP: Small, focused interface (not bloated)
    public interface PaymentProcessor {
        void pay(double amount);
    }
    // OCP: New payment methods can be added without modifying existing code
    // LSP: Any implementation can replace PaymentProcessor safely
    public class CreditCardPayment implements PaymentProcessor {
        @Override
        public void pay(double amount) {
            System.out.println("Paid " + amount + " using Credit Card");
        }
    }
    
    public class PaypalPayment implements PaymentProcessor {
        @Override
        public void pay(double amount) {
            System.out.println("Paid " + amount + " using PayPal");
        }
    }
    //class depends on abstraction(PaymentProcessor),not concrete classes
    public class CheckoutService {
    
        private final PaymentProcessor paymentProcessor;
    
        // Dependency Injection (constructor-based)
        public CheckoutService(PaymentProcessor paymentProcessor) {
            this.paymentProcessor = paymentProcessor;
        }
    
        public void checkout(double amount) {
            paymentProcessor.pay(amount);
        }
    }
    // ===== Application entry point =====
    public class Main {
        public static void main(String[] args) {
    
            PaymentProcessor payment = new CreditCardPayment();
            CheckoutService checkout = new CheckoutService(payment);
            checkout.checkout(100.0);
    
            // Switch implementation WITHOUT changing CheckoutService
            PaymentProcessor paypal = new PaypalPayment();
            checkout = new CheckoutService(paypal);
            checkout.checkout(200.0);
        }
    }
    ```
    
    ---
    

## 2️⃣ Databases & Persistence (Backend mindset)

### Relational Databases

- **ACID**

| Principle | Meaning | Concrete backend example |
| --- | --- | --- |
| **A — Atomicity** | A transaction is **all or nothing** | Register user: insert into `users` table and `user_roles` table. If one fails, rollback both. |
| **C — Consistency** | Database moves from **one valid state to another** | `email` column has unique constraint; inserting duplicate fails → DB remains consistent. |
| **I — Isolation** | Transactions don’t interfere; **concurrent safety** | Two users trying to book the same seat: isolation ensures only one succeeds. |
| **D — Durability** | Once committed, data is **permanent**, even on crash | After committing a payment, the record stays in the DB despite server failure. |
- indexes reduce search time while increasing insert complexity

### NoSQL (conceptual)

- NoSQL exists to handle flexible, large-scale, distributed data that relational databases struggle with. Unlike SQL, it offers schema-less storage and horizontal scaling. Choose NoSQL when you need high performance, flexible schemas, or **distributed multi-region setups.**

---

## 3️⃣ Spring & Spring Boot (CORE of the discussion)

### Spring Core Concepts

- Spring is a Java framework that simplifies building enterprise and backend applications by managing object creation, dependencies, and application configuration for you.
- **Spring is a Java framework that provides Inversion of Control and Dependency Injection to reduce coupling, improve testability, and make applications easier to maintain and scale.**
- **Spring is a container that manages your objects and their dependencies.**
- Inversion of Control is the principle where the framework controls object creation, and Dependency Injection is the mechanism Spring uses to provide dependencies to classes.

```java
public interface NotificationService {
    void send(String message);
}
@Component
public class EmailNotificationService implements NotificationService {
    public void send(String message) {
        System.out.println("Email sent: " + message);
    }
}
//injecting the dependency
@Component
public class UserService {
    private final NotificationService notificationService;
    // Constructor Injection (BEST PRACTICE)
    public UserService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }
    public void registerUser() {
        notificationService.send("Welcome!");
    }
}
```

- dependencyb injection is basically a mechanism to give classes the other classes they need , in the code example the userservice requires a notificationservice so passing it in the constructor is dependency injection , inversion of control is having something other than the class itself managing it , meaning the class does not have a new notificationservice() rather it gets initialized in another place , when spring is the one in control it initializes classes in order and creates and injects when needed , thats ioc with di in spring case , spring boot simplifies the configuration …
- when multiple options are available to inject we can either have a `@Primary` ie default or use `@Qualifier("paypalPayment")` to specify which one to use for a specific class .
- **Spring commun annotations**
    - **Component & Stereotypes**
        
        A Bean is any object managed by the Spring container. `@Component` is just ONE way to declare a bean.
        
    
    | Annotation | Meaning | Practical use |
    | --- | --- | --- |
    | `@Component` | Generic Spring bean | Utility or generic service |
    | `@Service` | Business logic layer | Services |
    | `@Repository` | Data access layer | Repositories / DAOs |
    | `@Controller` | MVC Controller | Web controllers |
    | `@RestController` | @Controller + `@ResponseBody` | REST APIs |
    - **Dependency Injection**
    
    | Annotation | Use |
    | --- | --- |
    | `@Autowired` | Inject dependency (constructor preferred) |
    | `@Qualifier` | Choose between multiple beans |
    | `@Primary` | Default bean if multiple exist |
    - **Lifecycle & Scope**
    
    | Annotation | Purpose |
    | --- | --- |
    | `@PostConstruct` | After dependency injection |
    | `@PreDestroy` | Before bean destruction |
    | `@Scope` | Define bean scope |
- **Bean lifecycle (very high level)**
    - A bean is a Java object whose lifecycle is managed by Spring.
    - **lifecycle**
        1. Bean Instantiation
        2. Dependency Injection
        3. Initialization
        4. Ready to Use
        5. Destruction (on app shutdown)

### Spring Boot

- Spring Boot makes Spring usable fast
- `@SpringBootApplication` is a convenience annotation that combines `@Configuration` (defines beans), `@ComponentScan` (detects components), and `@EnableAutoConfiguration` (auto-configures beans based on classpath dependencies), simplifying Spring Boot setup.

### Project Structure & Roles

- Controller — role & responsibilities
- Service — business logic
- Repository — data access
- DTO — why not expose entities
- Entity — JPA mapping basics

### Common Annotations

| Annotation | Layer / Purpose | What It Does | Notes / Practical Use |
| --- | --- | --- | --- |
| `@RestController` | Controller / Web Layer | Marks a class as a REST controller; combines `@Controller` + `@ResponseBody` | Used for **REST APIs** returning JSON directly. |
| `@Controller` | Controller / Web Layer | Marks a class as a Spring MVC controller; returns **views** (HTML, JSP, Thymeleaf) | Use `@ResponseBody` on methods to return JSON. |
| `@Service` | Service Layer | Marks a class as a **business service bean**; Spring registers it for DI | Typically contains **business logic**. |
| `@Repository` | Persistence Layer / DAO | Marks a class as a **data repository**; Spring treats it as a bean + adds **exception translation** | Used with JDBC / JPA repositories. |
| `@Entity` | JPA / Database Layer | Marks a class as a **database entity**; maps it to a table | Requires `@Id` for primary key; managed by JPA/Hibernate. |
| `@Autowired` | DI / Injection | Injects a Spring-managed bean automatically | Can be **field, setter, or constructor** injection. Preferred: constructor injection. |
| `@Configuration` | Config / Setup | Marks a class as a **source of bean definitions** | Used instead of XML config; beans defined with `@Bean` inside. |
| `@Component` | Generic | Marks any class as a Spring-managed bean | Base annotation; specialized versions: `@Service`, `@Repository`, `@Controller`. |
| `@PostMapping` | Controller / Request Mapping | Maps HTTP **POST requests** to a method | Example: `@PostMapping("/login")` |
| `@Valid` | Validation | Triggers **Bean Validation** on request body or method parameter | Often used with `@RequestBody` to enforce constraints like `@NotNull`. |
| `@Value` | Config / Properties | Injects a **property value** from `application.properties` or `application.yml` | Example: `@Value("${app.name}") private String appName;` |

### Spring Data JPA (and refreshers) / Spring DB layer notions

- JPA is a standard specification for mapping Java objects to relational databases. Hibernate is a popular implementation of JPA, providing extra ORM features. Spring Data JPA simplifies data access by generating repository implementations automatically, letting us focus on the business logic instead of boilerplate CRUD code.

| Layer | Responsibility |
| --- | --- |
| JPA | Specification (what should ORM do) |
| Hibernate | Implementation of JPA (actual ORM engine) |
| Spring Data JPA | Spring abstraction to reduce boilerplate and provide repository patterns |
- **REPOSITORIES**
    
    Repositories are interfaces that abstract data access; Spring Data JPA automatically provides implementations for CRUD operations, query derivation from method names, and custom queries via `@Query`.
    
    - Common predefined methods (from `JpaRepository` / `CrudRepository`)
    
    | Method | Purpose |
    | --- | --- |
    | `save(entity)` | Insert or update an entity |
    | `findById(id)` | Find entity by primary key |
    | `findAll()` | Retrieve all entities |
    | `delete(entity)` | Delete an entity |
    | `count()` | Count total entities |
    | `existsById(id)` | Check if entity exists by ID |
    - Query derivation by method names
    
    | Method Name | Query Generated |
    | --- | --- |
    | `findByName(String name)` | `SELECT * FROM table WHERE name = ?` |
    | `findByNameAndEmail(String name, String email)` | `SELECT * FROM table WHERE name=? AND email=?` |
    | `findByCreatedAtAfter(LocalDate date)` | `SELECT * FROM table WHERE createdAt > ?` |
    - Custom queries with `@Query`
    
    ```java
    @Query("SELECT u FROM User u WHERE u.age > :age")
    List<User> findUsersOlderThan(@Param("age") int age);
    ```
    
    - `CrudRepository` vs `JpaRepository`
    
    | Interface | Extends | Purpose / Notes |
    | --- | --- | --- |
    | `CrudRepository<T, ID>` | `Repository<T, ID>` | Provides **basic CRUD operations**: `save`, `findById`, `findAll`, `delete`, `count`. Lightweight, simple. |
    | `JpaRepository<T, ID>` | `PagingAndSortingRepository<T, ID>` → `CrudRepository` | Extends `CrudRepository` with **additional JPA-specific methods**: batch operations, flushing, deleting in batch, pagination & sorting. Usually preferred in Spring Boot apps. |
    | `PagingAndSortingRepository<T, ID>` | `CrudRepository` | Adds **pagination and sorting** capabilities to CrudRepository. |
- `@Transactional` ensures a method or class runs inside a db transaction

### Spring Security (Basics only)

- **Why:** Protect data, prevent unauthorized access.
- **AuthN vs AuthZ:** AuthN → who you are; AuthZ → what you can do.
- **JWT:** Stateless token for identity; sent in headers; verified on each request.
- **CORS:** Allows safe cross-origin requests; solves browser blocking issues.
- **Config idea:** Define public vs secured endpoints, auth mechanism, filters for JWT, CORS policies.

---

## 4️⃣ REST APIs & Backend Design (VERY IMPORTANT)

### REST Fundamentals

- What is a REST API (**RE**presentational **S**tate **T**ransfer)
    - **REST** = architectural style for client–server communication
    - Uses **HTTP**, **resources**, **stateless requests**
    - Resources identified by **URLs** (`/users/1`)
    - Data usually exchanged as **JSON**
- HTTP methods (`GET`, `POST`, `PUT`, `DELETE`)

| Method | Purpose | Idempotent | Spring |
| --- | --- | --- | --- |
| GET | Read resource | ✅ | `@GetMapping` |
| POST | Create resource | ❌ | `@PostMapping` |
| PUT | Update (replace) | ✅ | `@PutMapping` |
| PATCH | Partial update | ❌ | `@PatchMapping` |
| DELETE | Delete | ✅ | `@DeleteMapping` |
- Status codes (200, 201, 400, 401, 403, 404, 500)

| Code | Meaning | When to use |
| --- | --- | --- |
| 200 OK | Success | GET, PUT success |
| 201 Created | Resource created | POST success |
| 400 Bad Request | Invalid input | Validation error |
| 401 Unauthorized | Not authenticated | Missing/invalid token |
| 403 Forbidden | Not allowed | Authenticated but no rights |
| 404 Not Found | Resource missing | ID doesn’t exist |
| 500 Internal Server Error | Server bug | Unhandled exception |
- controller input annotations

| Annotation | Used for | Example |
| --- | --- | --- |
| `@RequestBody` | JSON body → object | POST / PUT |
| `@PathVariable` | Value from URL | `/users/{id}` |
| `@RequestParam` | Query parameter | `/users?page=1` |
| `@RequestHeader` | HTTP headers | `Authorization` |

### API Design

- Resource-based URLs (`/users`, `/users/{id}`)
- Use nouns, plural, lowercase
- No verbs in URLs
- API versioning (`/api/v1`)

---

### Requests & Responses

- Headers → auth, content-type
- Body → JSON payload
- Params → query (`?page=0`) / path (`/{id}`)
- Use DTOs (don’t expose entities)

---

### Validation & Errors

- `@Valid` on request objects
- Common annotations: `@NotNull`, `@NotBlank`, `@Size`, `@Email`
- Global handling with `@ControllerAdvice`
- Return clear error messages

---

### Pagination & Filtering

- Avoid large responses
- Use `page` & `size`
- Filter via query params (`?role=ADMIN`)

---

## 5️⃣ Containers, CI/CD & Cloud-Native Awareness

### Standard CI/CD Pipeline (Order)

- **Code** → developer pushes to repo (Git)
- **Build** → compile app, resolve dependencies
- **Test** → run unit / integration tests
- **Code Quality** → static analysis, linting, coverage
- **Package** → build artifact (JAR, Docker image)
- **Security Scan (optional)** → dependency & image scanning
- **Deploy** → deploy to env (dev / staging / prod)
- **Verify** → health checks, smoke tests
- **Monitor** → logs, metrics, alerts

### Code Quality (SonarQube)

- Run **after build, before deploy**
- Analyzes bugs, vulnerabilities, code smells
- Enforces **quality gates** (fail pipeline if not met)
- Integrated via CI tool (GitHub Actions, GitLab CI, Jenkins)
