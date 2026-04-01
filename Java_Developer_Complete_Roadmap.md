# ☕ Complete Java Developer Roadmap — Fresher se Expert Tak
> **200+ Topics & Subtopics** | Beginner se Expert Journey  
> Har topic ke neeche sub-topics bhi hain — ek ek karke seekho!

---

## 🗺️ Roadmap Overview

| Phase | Name | Duration | Focus |
|-------|------|----------|-------|
| Phase 1 | Java Foundations | 0–3 months | Core Java basics |
| Phase 2 | Advanced Core Java | 3–6 months | Collections, Streams, Threads |
| Phase 3 | Spring Ecosystem | 6–9 months | Spring Boot, REST, JPA, Security |
| Phase 4 | Microservices & DevOps | 9–12 months | Docker, Kubernetes, Kafka, CI/CD |
| Phase 5 | Expert & Architect Level | 12+ months | JVM, Cloud, DSA, Architecture |

---

## 🟢 PHASE 1 — Java Foundations (0–3 Months)

### 1.1 Setup & Basic Syntax
- JDK installation & IDE setup (IntelliJ IDEA / Eclipse)
- Hello World program — main() method structure
- Variables & Data Types: `int`, `double`, `float`, `char`, `boolean`, `String`
- Type Casting — implicit (widening) & explicit (narrowing)
- Operators — arithmetic, relational, logical, bitwise, ternary (`?:`)
- Scanner class for user input (`System.in`)
- `System.out.print()`, `println()`, `printf()`
- Comments — single-line `//`, multi-line `/* */`, JavaDoc `/** */`

### 1.2 Control Flow
- `if / else if / else` statements
- `switch` statement & switch expression (Java 14+)
- `for` loop, `while` loop, `do-while` loop
- Enhanced for-each loop (`for (int x : arr)`)
- `break`, `continue`, `return` keywords
- Nested loops & patterns (star, number pyramids)
- Labeled break & continue

### 1.3 Object-Oriented Programming (OOP) ⭐
- Classes & Objects (blueprint vs instance concept)
- Constructors — default, parameterized, copy constructor
- `this` keyword & constructor chaining
- **Inheritance** — `extends` keyword, `super` keyword
- Method Overriding & `@Override` annotation
- Method Overloading (compile-time polymorphism)
- Runtime Polymorphism — upcasting & dynamic dispatch
- **Abstract class** & abstract methods
- **Interfaces** — default & static methods (Java 8+), multiple implementation
- **Encapsulation** — private fields, getters & setters
- Access Modifiers — `public`, `private`, `protected`, package-private (default)
- `static` keyword — fields, methods, blocks, nested class
- `final` keyword — variables (constant), methods (can't override), classes (can't extend)
- `instanceof` operator & pattern matching (Java 16+)

### 1.4 Arrays & Strings
- 1D arrays — declaration, initialization, traversal
- 2D arrays — matrix operations
- `Arrays` class — `sort()`, `binarySearch()`, `fill()`, `copyOf()`
- String class & common methods (`length`, `charAt`, `substring`, `indexOf`, `replace`, `split`)
- String immutability & String Pool concept
- `StringBuilder` & `StringBuffer` (mutable strings — difference in thread safety)
- `String.format()` & `formatted()` method
- String comparison — `equals()` vs `==`
- Regular expressions basics (`Pattern`, `Matcher`)

### 1.5 Exception Handling
- `try-catch-finally` block structure
- `throw` & `throws` keywords
- Checked vs Unchecked Exceptions
- Exception class hierarchy — `Throwable` → `Error` / `Exception`
- Custom Exceptions (extend `Exception` or `RuntimeException`)
- Multi-catch block (Java 7+) — `catch(A | B e)`
- Try-with-resources — `AutoCloseable` interface
- Exception chaining (`getCause`, `initCause`)

---

## 🟣 PHASE 2 — Advanced Core Java (3–6 Months)

### 2.1 Collections Framework ⭐
- **List** — `ArrayList`, `LinkedList`, `Vector`, `Stack`
- **Set** — `HashSet`, `LinkedHashSet`, `TreeSet`
- **Map** — `HashMap`, `LinkedHashMap`, `TreeMap`, `Hashtable`
- **Queue** — `PriorityQueue`, `ArrayDeque` (also acts as `Deque`)
- `Iterator` & `ListIterator` — `hasNext()`, `next()`, `remove()`
- `Collections` utility class — `sort()`, `shuffle()`, `min()`, `max()`, `unmodifiableList()`
- `Comparable` vs `Comparator` (natural vs custom ordering)
- Fail-fast vs Fail-safe iterators
- Thread-safe collections — `ConcurrentHashMap`, `CopyOnWriteArrayList`

### 2.2 Generics
- Generic Classes — `class Box<T>`
- Generic Methods — `<T> T getValue()`
- Bounded Type Parameters — `<T extends Number>`
- Wildcards — `<?>`, `<? extends T>`, `<? super T>`
- Type Erasure — how generics work at runtime
- Generic Interfaces — `Comparable<T>`, `Iterable<T>`

### 2.3 Java 8+ Modern Features ⭐⭐
- **Lambda Expressions** — `(a, b) -> a + b` syntax
- **Functional Interfaces** — `@FunctionalInterface`
- `Predicate<T>`, `Function<T,R>`, `Consumer<T>`, `Supplier<T>`, `BiFunction<T,U,R>`
- **Stream API** — creating streams (`of`, `list.stream()`, `Arrays.stream()`)
- Stream intermediate ops — `map()`, `filter()`, `flatMap()`, `sorted()`, `distinct()`, `peek()`
- Stream terminal ops — `collect()`, `reduce()`, `count()`, `findFirst()`, `anyMatch()`, `forEach()`
- Collectors — `toList()`, `toSet()`, `groupingBy()`, `joining()`, `counting()`
- **Optional\<T\>** — `of()`, `ofNullable()`, `orElse()`, `orElseGet()`, `map()`, `flatMap()`
- **Method References** — `Class::staticMethod`, `obj::instanceMethod`, `Class::new`
- Default & Static methods in Interface
- **Date/Time API** — `LocalDate`, `LocalTime`, `LocalDateTime`, `ZonedDateTime`
- `Duration` & `Period`, `DateTimeFormatter`
- `CompletableFuture` — `supplyAsync`, `thenApply`, `thenCompose`, `exceptionally`
- Java 9: `List.of()`, `Map.of()`, `Set.of()`, `Optional.ifPresentOrElse()`
- Java 10: `var` keyword (local variable type inference)
- Java 14+: Records (preview) → Java 16: Records GA
- Java 16: Pattern Matching for instanceof
- Java 17: Sealed Classes
- Java 21: **Virtual Threads** (Project Loom) — lightweight threads

### 2.4 Multithreading & Concurrency ⭐
- `Thread` class — creating via `extends Thread`
- `Runnable` interface — creating via `implements Runnable`
- Thread lifecycle — `NEW`, `RUNNABLE`, `BLOCKED`, `WAITING`, `TIMED_WAITING`, `TERMINATED`
- Thread methods — `start()`, `sleep()`, `join()`, `interrupt()`, `yield()`
- `synchronized` keyword — method-level & block-level
- `wait()`, `notify()`, `notifyAll()` — inter-thread communication
- `ExecutorService` — `newFixedThreadPool`, `newSingleThreadExecutor`, `newCachedThreadPool`
- `ThreadPoolExecutor` — `corePoolSize`, `maxPoolSize`, `queueCapacity`
- `Callable<V>` & `Future<V>` — `get()`, `isDone()`, `cancel()`
- `ScheduledExecutorService` — `schedule()`, `scheduleAtFixedRate()`
- `volatile` keyword — visibility guarantee
- Atomic classes — `AtomicInteger`, `AtomicLong`, `AtomicReference`
- `ReentrantLock` & `ReadWriteLock`
- `CountDownLatch`, `CyclicBarrier`, `Semaphore`, `Phaser`
- Deadlock, Livelock, Starvation — understanding & prevention
- `Fork/Join Framework` — `ForkJoinPool`, `RecursiveTask`

### 2.5 Java I/O & NIO
- `FileReader` & `FileWriter` (character streams)
- `BufferedReader` & `BufferedWriter` (buffered character I/O)
- `InputStream` & `OutputStream` (byte streams)
- `ObjectInputStream` & `ObjectOutputStream`
- Serialization — `Serializable` interface, `serialVersionUID`, `transient` keyword
- NIO.2 — `Path`, `Files`, `Paths` utility class
- File operations — `createFile`, `delete`, `copy`, `move`, `readAllLines`
- Directory listing & file tree walking (`Files.walk()`)
- `WatchService` for file system changes

### 2.6 Design Patterns ⭐
#### Creational Patterns
- Singleton (thread-safe: DCL, Enum Singleton)
- Factory Method & Abstract Factory
- Builder Pattern (fluent API)
- Prototype (clone)

#### Structural Patterns
- Adapter (class & object)
- Decorator, Facade, Proxy, Composite

#### Behavioral Patterns
- Strategy Pattern
- Observer / Pub-Sub Pattern
- Command, Template Method, Iterator
- State, Chain of Responsibility

#### Principles
- **SOLID** — SRP, OCP, LSP, ISP, DIP
- DRY (Don't Repeat Yourself)
- KISS (Keep It Simple, Stupid)
- YAGNI (You Aren't Gonna Need It)

---

## 🟠 PHASE 3 — Spring Ecosystem (6–9 Months)

### 3.1 Build Tools
- **Maven** — `pom.xml` structure, `groupId`, `artifactId`, `version`
- Maven lifecycle — `validate`, `compile`, `test`, `package`, `install`, `deploy`
- Maven repositories — local (`~/.m2`), central, remote
- Maven plugins — Surefire (unit tests), Failsafe (integration), Shade (fat jar)
- Maven multi-module projects
- **Gradle** — `build.gradle` (Groovy/Kotlin DSL)
- Gradle tasks, configurations, dependencies
- Gradle wrapper — `gradlew`, `gradlew.bat`

### 3.2 Database & JDBC
- SQL basics — `CREATE`, `SELECT`, `INSERT`, `UPDATE`, `DELETE`
- SQL Joins — `INNER`, `LEFT`, `RIGHT`, `FULL OUTER`, `CROSS`
- Aggregation — `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`, `GROUP BY`, `HAVING`
- Subqueries, Views, Indexes, Stored Procedures
- **JDBC** — `DriverManager.getConnection()`
- `Statement` vs `PreparedStatement` (SQL injection prevention)
- `ResultSet` — traversal & data extraction
- JDBC transaction — `setAutoCommit(false)`, `commit()`, `rollback()`
- Connection Pooling — **HikariCP**, Apache DBCP2
- Database: MySQL, PostgreSQL setup & config

### 3.3 Spring Core ⭐⭐
- **IoC Container** — `BeanFactory` vs `ApplicationContext`
- **Dependency Injection** — Constructor, Setter, Field injection
- Stereotype annotations — `@Component`, `@Service`, `@Repository`, `@Controller`
- `@Autowired`, `@Qualifier`, `@Primary`
- `@Value` & `@ConfigurationProperties`
- **Bean Scopes** — `@Scope`: `singleton`, `prototype`, `request`, `session`
- Bean Lifecycle — `@PostConstruct`, `@PreDestroy`, `InitializingBean`
- `@Configuration` & `@Bean` (Java-based config)
- **Spring AOP** — Aspect, JoinPoint, Pointcut, Advice types
- AOP annotations — `@Aspect`, `@Before`, `@After`, `@Around`, `@AfterReturning`
- `@Transactional` — propagation, isolation, rollbackFor
- Spring Events — `ApplicationEvent`, `@EventListener`

### 3.4 Spring Boot ⭐⭐
- `@SpringBootApplication` — combines `@EnableAutoConfiguration` + `@ComponentScan` + `@Configuration`
- Auto-configuration mechanism — `spring.factories` / `AutoConfiguration.imports`
- `application.properties` & `application.yml` configuration
- Spring Boot Starters — `spring-boot-starter-web`, `-data-jpa`, `-security`, `-test`
- Embedded server — Tomcat (default), Jetty, Undertow
- **Spring Boot Actuator** — `/actuator/health`, `/metrics`, `/info`, `/env`
- Custom endpoints, securing actuator
- **Spring Profiles** — `@Profile`, `spring.profiles.active`
- `CommandLineRunner` & `ApplicationRunner`
- External configuration hierarchy (env vars > properties > yaml)
- Spring Boot DevTools (hot reload)
- Custom auto-configuration

### 3.5 Spring MVC & REST API ⭐⭐
- `@RestController` vs `@Controller`
- `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@PatchMapping`
- `@RequestParam`, `@PathVariable`, `@RequestBody`, `@RequestHeader`
- `ResponseEntity<T>` — status codes, headers, body
- **DTO pattern** — Data Transfer Objects (request/response separation)
- **Bean Validation** — `@Valid`, `@NotNull`, `@NotBlank`, `@Size`, `@Email`, `@Pattern`
- Custom validators — `ConstraintValidator`
- **Global Exception Handling** — `@ControllerAdvice`, `@ExceptionHandler`
- `ProblemDetail` & RFC 7807 error responses (Spring 6+)
- Content negotiation — JSON, XML (Jackson, JAXB)
- CORS configuration — `@CrossOrigin`, global CORS config
- **Swagger/OpenAPI docs** — `springdoc-openapi`, `@Operation`, `@Schema`
- HTTP status codes — 2xx, 3xx, 4xx, 5xx understanding

### 3.6 Spring Data JPA ⭐⭐
- JPA concepts — ORM, `EntityManager`, `PersistenceContext`
- Hibernate as JPA provider — `hibernate.ddl-auto`
- `@Entity`, `@Table`, `@Column`, `@Id`, `@GeneratedValue`
- `@ManyToOne`, `@OneToMany`, `@OneToOne`, `@ManyToMany`
- `@JoinColumn`, `@JoinTable`
- Cascade types — `ALL`, `PERSIST`, `MERGE`, `REMOVE`, `REFRESH`, `DETACH`
- Fetch types — `EAGER` vs `LAZY` loading
- `JpaRepository` — `findById`, `findAll`, `save`, `delete`, `existsById`
- `CrudRepository`, `PagingAndSortingRepository`
- Custom queries — `@Query` (JPQL & Native SQL)
- Derived query methods — `findByName`, `findByAgeGreaterThan`, `findByEmailContaining`
- **Pagination & Sorting** — `Pageable`, `PageRequest`, `Sort`
- **N+1 problem** — detection & fix (`JOIN FETCH`, `@EntityGraph`)
- `@Modifying` for update/delete queries
- Spring Data Auditing — `@CreatedDate`, `@LastModifiedDate`, `@CreatedBy`

### 3.7 Spring Security ⭐
- Security Filter Chain — how requests pass through filters
- Authentication vs Authorization
- `UserDetails` & `UserDetailsService`
- `PasswordEncoder` — `BCryptPasswordEncoder`
- `SecurityConfig` — `@EnableWebSecurity`, `HttpSecurity`
- Session Management vs Stateless (JWT)
- **JWT** — JSON Web Token structure (`header.payload.signature`)
- JWT generation, validation, refresh token flow
- **OAuth2** — Authorization Code, Client Credentials flows
- Spring Security OAuth2 Resource Server
- Method-level security — `@PreAuthorize`, `@PostAuthorize`, `@Secured`
- Role-based Access Control (RBAC) — `ROLE_` prefix
- CSRF protection — when to disable (REST APIs)

---

## 🟡 PHASE 4 — Microservices & DevOps (9–12 Months)

### 4.1 Microservices Architecture ⭐⭐
- Monolith vs Microservices — pros, cons, when to use
- Microservices principles — single responsibility, loose coupling, high cohesion
- **Service Discovery** — Spring Cloud Eureka (server & client)
- **API Gateway** — Spring Cloud Gateway (routing, filters, rate limiting)
- Client-side Load Balancing — Spring Cloud LoadBalancer
- **Circuit Breaker** — Resilience4j (`CircuitBreaker`, `Retry`, `RateLimiter`, `Bulkhead`)
- Declarative REST Client — **OpenFeign** (`@FeignClient`)
- Centralized Config — Spring Cloud Config Server & Client
- Distributed Tracing — Micrometer Tracing + Zipkin/Jaeger
- 12-Factor App methodology
- **Saga Pattern** — Choreography vs Orchestration

### 4.2 Message Queues & Event-Driven
- **Apache Kafka** — brokers, topics, partitions, consumer groups, offsets
- Kafka Producer — `KafkaTemplate`, key-value serialization
- Kafka Consumer — `@KafkaListener`, concurrency, error handling, DLQ
- Kafka Streams — stateful stream processing basics
- **RabbitMQ** — exchanges (direct, topic, fanout), queues, bindings, routing keys
- Spring AMQP — `RabbitTemplate`, `@RabbitListener`
- Event-Driven Architecture — event schema design
- Outbox Pattern — reliable event publishing
- CQRS — command & query model separation
- Event Sourcing — event log as source of truth

### 4.3 Testing (Unit & Integration) ⭐
- **JUnit 5** — `@Test`, `@BeforeEach`, `@AfterEach`, `@BeforeAll`, `@AfterAll`
- Assertions — `assertEquals`, `assertThrows`, `assertAll`, `assertThat`
- Parameterized Tests — `@ParameterizedTest`, `@ValueSource`, `@CsvSource`, `@MethodSource`
- **Mockito** — `mock()`, `spy()`, `when().thenReturn()`, `doThrow()`
- `@Mock`, `@InjectMocks`, `@Spy`, `@Captor` — annotations
- Mockito verify — `verify()`, `times()`, `never()`, `ArgumentCaptor`
- **Spring Boot Test** — `@SpringBootTest`, `@WebMvcTest`, `@DataJpaTest`, `@MockBean`
- MockMvc — `perform()`, `andExpect()`, `jsonPath()`
- **TestContainers** — real DB/Kafka in integration tests
- H2 in-memory database for testing
- Awaitility for async test assertions
- WireMock — stubbing external HTTP services
- Code coverage — **JaCoCo**, coverage thresholds in Maven/Gradle
- Test pyramid — unit vs integration vs E2E

### 4.4 Docker & Containers ⭐
- Docker concepts — images, containers, volumes, networks
- Dockerfile — `FROM`, `RUN`, `COPY`, `EXPOSE`, `CMD`, `ENTRYPOINT`
- Multi-stage Docker builds for Java (build + runtime layers)
- `docker-compose` — multi-service local setup
- Docker networking — bridge, host, overlay
- Docker volumes — named, bind mounts
- Docker Hub & private registry
- Spring Boot Docker — Cloud Native Buildpacks (`./mvnw spring-boot:build-image`)
- Jib — containerize without Dockerfile
- JVM in containers — `-XX:+UseContainerSupport`, `-XX:MaxRAMPercentage`

### 4.5 Kubernetes ⭐
- Kubernetes architecture — control plane, worker nodes
- **Pods** — smallest deployable unit, multi-container pods
- **ReplicaSets & Deployments** — desired state management
- **Services** — ClusterIP, NodePort, LoadBalancer, ExternalName
- ConfigMaps & Secrets — externalizing config from images
- Ingress & Ingress Controller (nginx, traefik)
- Persistent Volumes & PersistentVolumeClaims
- **Horizontal Pod Autoscaler (HPA)**
- Resource limits & requests (CPU, memory)
- Liveness & Readiness probes
- `kubectl` — essential commands (`apply`, `get`, `describe`, `logs`, `exec`, `port-forward`)
- **Helm** — package manager for Kubernetes, Chart structure

### 4.6 CI/CD & Git ⭐
- Git core — `init`, `clone`, `add`, `commit`, `push`, `pull`, `fetch`
- Git branching — `branch`, `checkout`, `merge`, `rebase`
- Branching strategies — Git Flow, Trunk-Based Development
- Merge vs Rebase vs Squash
- Pull Requests & Code Review best practices
- Conflict resolution
- **GitHub Actions** — workflow YAML, jobs, steps, marketplace actions
- **Jenkins** — declarative pipeline, Jenkinsfile, agents, stages, post actions
- Building, testing & packaging in CI pipeline
- Docker image build & push in pipeline
- CD to Kubernetes via kubectl/Helm
- **SonarQube** — static analysis, quality gates, code smells
- Artifact registry — Nexus, JFrog Artifactory
- Semantic versioning (SemVer — MAJOR.MINOR.PATCH)

---

## 🔵 PHASE 5 — Expert & Architect Level (12+ Months)

### 5.1 JVM Internals & Performance ⭐⭐
- JVM architecture — ClassLoader, JIT Compiler, Execution Engine, GC
- Runtime Data Areas — Heap, Stack, Method Area, PC Register, Native Method Stack
- Class loading — loading, linking, initialization, delegation model
- JIT Compilation — C1, C2 compilers, tiered compilation, code cache
- **GC algorithms** — G1GC (default), ZGC, Shenandoah, CMS, Serial, Parallel
- GC tuning — `-Xmx`, `-Xms`, `-XX:+UseG1GC`, `-XX:MaxGCPauseMillis`
- Memory leaks — detection patterns (static fields, listeners, caches)
- Heap dumps — `jmap`, analyzing with Eclipse MAT, JVisualVM
- Thread dumps — `jstack`, detecting deadlocks & CPU hot threads
- Performance profiling — VisualVM, JProfiler, **Async Profiler**, Java Flight Recorder
- **GraalVM** — polyglot, Native Image (compile to native binary, instant startup)
- Project Panama & Project Valhalla — future of Java
- Bytecode manipulation — ASM, ByteBuddy, Javassist
- **JMH** — Java Microbenchmark Harness

### 5.2 Reactive Programming
- Reactive Manifesto — Responsive, Resilient, Elastic, Message-Driven
- Reactive Streams spec — Publisher, Subscriber, Subscription, Processor
- **Project Reactor** — `Mono<T>` (0-1 element), `Flux<T>` (0-N elements)
- Reactor operators — `map`, `flatMap`, `filter`, `zip`, `merge`, `switchMap`
- Backpressure — how to handle slow consumers
- Error handling — `onErrorReturn`, `onErrorResume`, `retry`, `retryWhen`
- Schedulers — `Schedulers.parallel()`, `.boundedElastic()`, `.single()`
- **Spring WebFlux** — `@RestController` with `Mono/Flux` return types
- `WebClient` — non-blocking HTTP client (replacement for `RestTemplate`)
- Reactive DB access — R2DBC drivers (`r2dbc-postgresql`, `r2dbc-h2`)
- Testing reactive code — `StepVerifier`, `TestPublisher`

### 5.3 Advanced Architecture Patterns
- **Domain-Driven Design (DDD)** — Ubiquitous Language, Bounded Contexts
- DDD building blocks — Entity, Value Object, Aggregate, Repository, Domain Service
- **Hexagonal Architecture** — Ports & Adapters (primary/secondary ports)
- **Clean Architecture** — layers & dependency rules
- **CQRS** — separating command model from query model
- **Event Sourcing** — event as source of truth, event replay
- **Saga Pattern** — long-running distributed transactions
- Strangler Fig — gradually replace monolith with microservices
- Sidecar, Ambassador, Anti-Corruption Layer patterns
- API Versioning strategies (URL, header, query param)
- **CAP Theorem** — Consistency, Availability, Partition Tolerance
- Eventual Consistency patterns

### 5.4 Cloud Platforms (AWS / GCP / Azure)
- **AWS** — EC2, Auto Scaling Groups, Load Balancer (ALB/NLB)
- AWS — S3 (storage), RDS (managed SQL), DynamoDB (NoSQL)
- AWS — Lambda (serverless), ECS (containers), EKS (Kubernetes)
- AWS — API Gateway, SQS, SNS, EventBridge
- AWS — CloudWatch (logs, metrics, alarms), X-Ray (tracing)
- Spring Cloud AWS — S3 integration, SQS listener, Parameter Store
- **GCP** — Compute Engine, Cloud Run, GKE, Cloud SQL, Pub/Sub
- **Azure** — App Service, AKS, Cosmos DB, Azure Functions, Service Bus
- Serverless Java — AWS Lambda with Spring Cloud Function
- Infrastructure as Code — **Terraform**, AWS CloudFormation
- Cloud-native patterns — health checks, graceful shutdown, externalized config

### 5.5 Data Structures & Algorithms ⭐⭐ (For Interviews!)
#### Linear Data Structures
- Arrays — two-pointer, sliding window, prefix sum
- Strings — palindrome, anagram, substring problems
- LinkedList — singly, doubly, circular; reverse, detect cycle (Floyd's)
- Stack — balanced parentheses, monotonic stack problems
- Queue & Deque — BFS implementation, sliding window max
- Hashing — HashMap internals, collision resolution

#### Non-Linear Data Structures
- Trees — Binary Tree traversal (in/pre/post order, level order BFS)
- Binary Search Tree (BST) — insert, delete, search, validate
- Balanced BST — AVL trees, Red-Black Trees (concept)
- Heap — min/max heap, `PriorityQueue` in Java, Heap Sort
- Trie — prefix tree for string problems
- Segment Tree & Fenwick Tree (BIT)

#### Graphs
- Representation — adjacency list & adjacency matrix
- BFS (Breadth-First Search) & DFS (Depth-First Search)
- Dijkstra's Algorithm (shortest path)
- Bellman-Ford Algorithm (negative weights)
- Floyd-Warshall (all-pairs shortest path)
- Topological Sort (Kahn's + DFS approach)
- Union-Find (Disjoint Set Union) — path compression, union by rank

#### Algorithms
- **Dynamic Programming** — memoization (top-down), tabulation (bottom-up)
- DP patterns — 0/1 Knapsack, LCS, LIS, coin change, grid DP
- **Greedy Algorithms** — activity selection, Huffman coding
- **Backtracking** — N-Queens, Sudoku solver, subsets, permutations
- Sorting — QuickSort, MergeSort, HeapSort, TimSort (Java `Arrays.sort`)
- Binary Search variations — first/last position, rotated array
- Bit Manipulation — AND, OR, XOR tricks

#### Practice
- LeetCode — Easy (150) → Medium (100) → Hard (50)
- Company-specific prep — Amazon, Google, Microsoft, Flipkart patterns
- Mock interviews on Pramp, InterviewBit

### 5.6 Security & Observability
- OWASP Top 10 — SQL Injection, XSS, CSRF, SSRF, broken auth
- Input validation & sanitization best practices
- Secret management — HashiCorp Vault, AWS Secrets Manager
- HTTPS, TLS/SSL — certificate management, mTLS for service-to-service
- Structured logging — JSON logs, correlation IDs, MDC (Mapped Diagnostic Context)
- Log levels — TRACE, DEBUG, INFO, WARN, ERROR — best practices
- Metrics — Micrometer, custom metrics (`@Timed`, `@Counted`)
- **Prometheus** — metrics scraping, PromQL basics
- **Grafana** — dashboards, alerting rules
- Distributed Tracing — OpenTelemetry, Jaeger, Zipkin
- APM tools — Elastic APM, Datadog, New Relic
- Load testing — Gatling, Apache JMeter
- SonarQube — code smells, bugs, vulnerabilities, technical debt

---

## 💡 Resources to Learn

### 📚 Books
| Book | For |
|------|-----|
| Head First Java | Absolute Beginners |
| Effective Java (Joshua Bloch) | Intermediate/Advanced |
| Java Concurrency in Practice | Multithreading |
| Clean Code (Robert C. Martin) | Code quality |
| Designing Data-Intensive Applications | Architecture |
| Spring in Action | Spring Framework |

### 🎥 YouTube Channels
- **Telusko** — Java basics (Hindi/English)
- **Java Brains** — Spring Framework
- **Amigoscode** — Spring Boot, Microservices
- **TechWorld with Nana** — Docker, Kubernetes
- **CodeWithDurgesh** — Java in Hindi

### 🌐 Practice Platforms
- **LeetCode** — DSA practice
- **HackerRank** — Java challenges
- **Coding Ninjas** — structured courses
- **GeeksforGeeks** — theory + practice
- **Spring.io** — official guides

---

## 🎯 Month-wise Study Plan (Fresher → Job Ready)

| Month | Focus |
|-------|-------|
| Month 1 | Java Basics + OOP |
| Month 2 | Arrays, Strings, Exception Handling |
| Month 3 | Collections, Generics, I/O |
| Month 4 | Java 8 Features (Streams, Lambda, Optional) |
| Month 5 | Multithreading, Design Patterns |
| Month 6 | Maven/Gradle, SQL, JDBC |
| Month 7 | Spring Core + Spring Boot |
| Month 8 | Spring MVC + REST API + Spring Data JPA |
| Month 9 | Spring Security + Testing + Git |
| Month 10 | Docker + Microservices basics |
| Month 11 | DSA Practice (LeetCode) + Kafka basics |
| Month 12 | Mock Interviews + Projects + Job Applications |

---

## 🚀 Projects to Build (Portfolio ke liye)

1. **Student Management System** (Core Java + JDBC)
2. **Library Management REST API** (Spring Boot + JPA + MySQL)
3. **E-commerce Backend** (Spring Boot + Security + JWT + JPA)
4. **Chat Application** (Spring Boot + WebSocket)
5. **Microservices Demo** (2-3 services + Eureka + API Gateway + Docker)
6. **Job Portal API** (Full Stack — Spring Boot + React/Angular)

---

> 💬 **Yaad Rakho:** Ek topic ko ek bar padho, hands-on code likho, phir agle topic par jao.  
> 🔥 **Consistency > Intensity** — Roz 2-3 ghante dedicate karo aur 12 mahino mein aap ek strong Java Developer ban sakte ho!
