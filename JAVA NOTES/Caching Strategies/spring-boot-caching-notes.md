# 🚀 Caching in Spring Boot REST APIs
### A Java Developer's Guide to `CacheManager`, `@Cacheable`, `@CacheEvict` & More

---

## 📌 Why Caching?

As a Java developer, you'll often hit performance bottlenecks when:
- The same DB query runs **repeatedly** for the same input
- External API calls are **slow and expensive**
- Business logic is **heavy** but the result rarely changes

**Caching** solves this by storing the result of a method call and returning the cached value on repeated calls — skipping the actual execution.

```
First Call  → Method Executes → Result stored in Cache → Returns result
Second Call → Cache Hit       → Returns result directly (No DB/Logic hit)
```

---

## 🔧 Setting Up Caching in Spring Boot

### 1. Add Dependency

For simple in-memory caching (no extra setup needed):
```xml
<!-- pom.xml -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

For Redis-based caching:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

---

### 2. Enable Caching — `@EnableCaching`

You **must** enable caching explicitly in your Spring Boot app.

```java
@SpringBootApplication
@EnableCaching  // ← THIS IS REQUIRED
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

> ⚠️ Without `@EnableCaching`, all cache annotations are **silently ignored**.

---

## 🗂️ CacheManager — The Brain of Caching

`CacheManager` is the **central interface** that manages all caches.
Spring Boot auto-configures a `CacheManager` based on what's on your classpath.

| Dependency Present         | Auto-configured CacheManager       |
|----------------------------|------------------------------------|
| Nothing extra              | `ConcurrentMapCacheManager` (JVM)  |
| `spring-boot-starter-redis`| `RedisCacheManager`                |
| Ehcache on classpath       | `EhCacheCacheManager`              |
| Caffeine on classpath      | `CaffeineCacheManager`             |

### Defining CacheManager Manually (Simple In-Memory)

```java
@Configuration
@EnableCaching
public class CacheConfig {

    @Bean
    public CacheManager cacheManager() {
        // Simple in-memory cache using ConcurrentHashMap under the hood
        ConcurrentMapCacheManager cacheManager = new ConcurrentMapCacheManager();
        cacheManager.setCacheNames(List.of("products", "users", "orders"));
        return cacheManager;
    }
}
```

### Defining CacheManager with Caffeine (Recommended for Production In-Memory)

```java
@Configuration
@EnableCaching
public class CacheConfig {

    @Bean
    public CacheManager cacheManager() {
        CaffeineCacheManager cacheManager = new CaffeineCacheManager("products", "users");
        cacheManager.setCaffeine(
            Caffeine.newBuilder()
                .expireAfterWrite(10, TimeUnit.MINUTES)  // TTL = 10 mins
                .maximumSize(500)                        // Max 500 entries
                .recordStats()                           // Enable hit/miss stats
        );
        return cacheManager;
    }
}
```

> Add Caffeine dependency:
> ```xml
> <dependency>
>     <groupId>com.github.ben-manes.caffeine</groupId>
>     <artifactId>caffeine</artifactId>
> </dependency>
> ```

---

## 📦 Core Cache Annotations

---

### ✅ `@Cacheable` — Store the Result

Marks a method whose **result should be cached**.
On the next call with the same key, the cached value is returned **without executing the method**.

```java
@Service
public class ProductService {

    @Cacheable(value = "products", key = "#id")
    public Product getProductById(Long id) {
        System.out.println("Fetching from DB for id: " + id);  // Only prints on cache miss
        return productRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("Product not found"));
    }
}
```

**REST Controller:**
```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

    @Autowired
    private ProductService productService;

    @GetMapping("/{id}")
    public ResponseEntity<Product> getProduct(@PathVariable Long id) {
        return ResponseEntity.ok(productService.getProductById(id));
    }
}
```

**Flow:**
```
GET /api/products/1  (1st call) → DB hit  → stored in cache["products"]["1"]
GET /api/products/1  (2nd call) → Cache hit → returned directly ✅
GET /api/products/2  (1st call) → DB hit  → stored in cache["products"]["2"]
```

---

#### 🔑 Cache Key Strategies

```java
// Key based on single param
@Cacheable(value = "products", key = "#id")

// Key based on multiple params
@Cacheable(value = "products", key = "#category + '-' + #brand")

// Key based on object field
@Cacheable(value = "products", key = "#request.categoryId")

// Using SpEL expressions
@Cacheable(value = "products", key = "T(java.util.Objects).hash(#id, #version)")

// Default key (uses all params automatically if key not specified)
@Cacheable(value = "products")
```

---

#### 🚦 Conditional Caching

```java
// Only cache if id > 10
@Cacheable(value = "products", key = "#id", condition = "#id > 10")

// Only cache if result is not null
@Cacheable(value = "products", key = "#id", unless = "#result == null")

// Only cache if result list is not empty
@Cacheable(value = "products", key = "#category", unless = "#result.isEmpty()")
```

---

### ♻️ `@CacheEvict` — Remove from Cache

Used to **invalidate/remove** entries from the cache.
Call this when data is **updated or deleted** so stale data is not served.

```java
@Service
public class ProductService {

    // Evict a specific entry
    @CacheEvict(value = "products", key = "#id")
    public void deleteProduct(Long id) {
        productRepository.deleteById(id);
    }

    // Evict all entries in the cache
    @CacheEvict(value = "products", allEntries = true)
    public void clearAllProducts() {
        System.out.println("All product cache cleared!");
    }

    // Evict AFTER method executes (default is BEFORE)
    @CacheEvict(value = "products", key = "#product.id", beforeInvocation = false)
    public Product updateProduct(Product product) {
        return productRepository.save(product);
    }
}
```

**REST Controller:**
```java
@DeleteMapping("/{id}")
public ResponseEntity<Void> deleteProduct(@PathVariable Long id) {
    productService.deleteProduct(id);  // Cache entry for this id is evicted
    return ResponseEntity.noContent().build();
}
```

---

### 🔄 `@CachePut` — Update the Cache

Unlike `@Cacheable`, `@CachePut` **always executes** the method AND updates the cache.
Use this on **update operations** to keep cache in sync.

```java
@CachePut(value = "products", key = "#product.id")
public Product updateProduct(Product product) {
    // Method always runs, result is stored/updated in cache
    return productRepository.save(product);
}
```

**Key Difference:**

| Annotation     | Executes Method? | Updates Cache? | Use Case          |
|----------------|-----------------|----------------|-------------------|
| `@Cacheable`   | Only on miss    | Only on miss   | Read operations   |
| `@CachePut`    | Always          | Always         | Update operations |
| `@CacheEvict`  | Yes             | Removes entry  | Delete operations |

---

### 🎯 `@Caching` — Multiple Cache Operations on One Method

```java
@Caching(
    put  = { @CachePut(value = "products", key = "#product.id") },
    evict = { @CacheEvict(value = "productList", allEntries = true) }
)
public Product saveProduct(Product product) {
    return productRepository.save(product);
}
```

---

### 🏷️ `@CacheConfig` — Class-Level Cache Configuration

Avoids repeating `value = "products"` on every method:

```java
@Service
@CacheConfig(cacheNames = "products")  // Applied to all methods in this class
public class ProductService {

    @Cacheable(key = "#id")           // No need to write value = "products"
    public Product getProductById(Long id) { ... }

    @CacheEvict(key = "#id")
    public void deleteProduct(Long id) { ... }

    @CachePut(key = "#product.id")
    public Product updateProduct(Product product) { ... }
}
```

---

## 🔴 Redis Cache Configuration (Production-Grade)

```yaml
# application.yml
spring:
  cache:
    type: redis
  data:
    redis:
      host: localhost
      port: 6379
      password: yourpassword   # if secured
      timeout: 2000ms
```

```java
@Configuration
@EnableCaching
public class RedisCacheConfig {

    @Bean
    public RedisCacheConfiguration redisCacheConfiguration() {
        return RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofMinutes(30))           // TTL = 30 mins
                .disableCachingNullValues()                  // Don't cache nulls
                .serializeValuesWith(
                    RedisSerializationContext.SerializationPair.fromSerializer(
                        new GenericJackson2JsonRedisSerializer()  // JSON serialization
                    )
                );
    }

    @Bean
    public RedisCacheManager cacheManager(RedisConnectionFactory connectionFactory) {
        Map<String, RedisCacheConfiguration> cacheConfigs = new HashMap<>();

        // Custom TTL per cache
        cacheConfigs.put("products", redisCacheConfiguration().entryTtl(Duration.ofHours(1)));
        cacheConfigs.put("users",    redisCacheConfiguration().entryTtl(Duration.ofMinutes(15)));

        return RedisCacheManager.builder(connectionFactory)
                .cacheDefaults(redisCacheConfiguration())
                .withInitialCacheConfigurations(cacheConfigs)
                .build();
    }
}
```

> ⚠️ For Redis caching, your entity classes **must implement `Serializable`**:
> ```java
> public class Product implements Serializable {
>     private static final long serialVersionUID = 1L;
>     // fields...
> }
> ```

---

## ⚠️ Common Mistakes & Pitfalls

### 1. Self-Invocation Problem (Most Common!)

```java
@Service
public class ProductService {

    public Product process(Long id) {
        return getProductById(id);  // ❌ Cache WON'T work — self-invocation bypasses proxy
    }

    @Cacheable(value = "products", key = "#id")
    public Product getProductById(Long id) {
        return productRepository.findById(id).orElseThrow();
    }
}
```

**Fix:** Inject the service into itself or use a separate service class:
```java
@Service
public class ProductService {

    @Autowired
    private ProductService self;  // Spring injects the proxy

    public Product process(Long id) {
        return self.getProductById(id);  // ✅ Goes through proxy — cache works
    }

    @Cacheable(value = "products", key = "#id")
    public Product getProductById(Long id) {
        return productRepository.findById(id).orElseThrow();
    }
}
```

---

### 2. Caching `null` Values

```java
// ❌ Problem: Caches null — every "not found" occupies cache space
@Cacheable(value = "products", key = "#id")
public Product getProductById(Long id) {
    return productRepository.findById(id).orElse(null);
}

// ✅ Fix: Use unless to skip caching nulls
@Cacheable(value = "products", key = "#id", unless = "#result == null")
public Product getProductById(Long id) {
    return productRepository.findById(id).orElse(null);
}
```

---

### 3. Forgetting to Evict After Updates

```java
// ❌ Stale cache — update runs but old value still served from cache
public Product updateProduct(Product product) {
    return productRepository.save(product);
}

// ✅ Use @CachePut or @CacheEvict
@CachePut(value = "products", key = "#product.id")
public Product updateProduct(Product product) {
    return productRepository.save(product);
}
```

---

### 4. `@Cacheable` on `private` Methods

Spring AOP proxy **cannot intercept private methods**.

```java
@Cacheable(value = "products", key = "#id")
private Product fetchFromDB(Long id) { ... }  // ❌ Cache NEVER applied
```
**Fix:** Always annotate `public` methods.

---

## 🧪 Testing Cached Methods

```java
@SpringBootTest
class ProductServiceTest {

    @Autowired
    private ProductService productService;

    @Autowired
    private CacheManager cacheManager;

    @Test
    void shouldCacheProductOnFirstCall() {
        productService.getProductById(1L);  // 1st call — DB hit
        productService.getProductById(1L);  // 2nd call — should hit cache

        Cache cache = cacheManager.getCache("products");
        assertNotNull(cache.get(1L));        // Entry should be in cache
    }

    @Test
    void shouldEvictCacheOnDelete() {
        productService.getProductById(1L);  // populate cache
        productService.deleteProduct(1L);   // should evict

        Cache cache = cacheManager.getCache("products");
        assertNull(cache.get(1L));           // Entry should be gone
    }
}
```

---

## 📋 Complete CRUD Example with Caching

```java
@Service
@CacheConfig(cacheNames = "products")
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    // CREATE — save to DB, populate cache
    @CachePut(key = "#result.id")
    public Product createProduct(ProductDTO dto) {
        Product p = new Product(dto.getName(), dto.getPrice());
        return productRepository.save(p);
    }

    // READ — cache on first call, return cached on subsequent calls
    @Cacheable(key = "#id", unless = "#result == null")
    public Product getProductById(Long id) {
        return productRepository.findById(id).orElse(null);
    }

    // READ ALL — cache the full list
    @Cacheable(key = "'all-products'")
    public List<Product> getAllProducts() {
        return productRepository.findAll();
    }

    // UPDATE — always execute, update cache entry
    @CachePut(key = "#product.id")
    @CacheEvict(value = "products", key = "'all-products'")  // also clear list cache
    public Product updateProduct(Product product) {
        return productRepository.save(product);
    }

    // DELETE — remove from cache
    @Caching(evict = {
        @CacheEvict(key = "#id"),
        @CacheEvict(key = "'all-products'")
    })
    public void deleteProduct(Long id) {
        productRepository.deleteById(id);
    }
}
```

---

## 📊 Cache Strategy Summary

```
┌─────────────────────────────────────────────────────────────────┐
│                    CACHING DECISION FLOW                        │
│                                                                 │
│  Data Changes Frequently?                                       │
│  ├── YES → Short TTL (seconds/minutes) or No Cache             │
│  └── NO  → Cache it!                                           │
│            ├── Read-heavy?    → @Cacheable                     │
│            ├── Write/Update?  → @CachePut + @CacheEvict        │
│            └── Delete?        → @CacheEvict                    │
│                                                                 │
│  Environment?                                                   │
│  ├── Single Instance  → Caffeine (in-memory, fast)             │
│  └── Multiple Instances (Cluster) → Redis (distributed)       │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🚀 Quick Reference Cheat Sheet

| Annotation        | When to Use                          | Key Behavior                   |
|-------------------|--------------------------------------|--------------------------------|
| `@EnableCaching`  | Main class / Config class            | Activates cache infrastructure |
| `@Cacheable`      | GET / Read operations                | Skip method if cache hit       |
| `@CachePut`       | PUT / UPDATE operations              | Always run, always update cache|
| `@CacheEvict`     | DELETE / Invalidate operations       | Remove entry from cache        |
| `@Caching`        | Multiple cache ops on same method    | Combine multiple annotations   |
| `@CacheConfig`    | Class level cache name config        | Avoid repetition               |

---

> 💡 **Developer Tip:** Always prefer **Redis** in production microservices. Use **Caffeine** for single-node applications where you need speed. Avoid the default `ConcurrentMapCacheManager` in production as it has **no TTL support** out of the box.
