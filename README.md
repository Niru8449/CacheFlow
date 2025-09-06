# ⚡ CacheFlow
## 📌 Overview  
This project is a **distributed, in-memory cache system** implemented in **Java**.  
It provides **fast, scalable, and fault-tolerant caching** with support for **eviction policies, write strategies, request collapsing, and consistent hashing**.  

It simulates how modern distributed caching systems (like Redis, Memcached, or Hazelcast) work, with support for **multiple storage backends** and **asynchronous client operations**.  

---

## ✅ Features  
- **In-memory distributed caching** with consistent hashing  
- **Eviction policies**: LRU (Least Recently Used) & LFU (Least Frequently Used)  
- **Write strategies**: Write-Through & Write-Back  
- **Pluggable databases** (MySQL, PostgreSQL, or custom backends)  
- **Request collapsing** to avoid duplicate DB fetches  
- **Striped executor service** for per-key ordering with high concurrency  
- **Asynchronous client API** with `Future` support  
- **Scalable design** for multiple cache nodes  

---

## ⚙️ Architecture Components  

### 1. **Distributed Cache Coordinator**  
- Class: `DistributedCache<K,V>`  
- Routes client requests to cache nodes using **consistent hashing**.  
- Manages node additions/removals dynamically.  

### 2. **Cache Node**  
- Class: `CacheNode<K,V>`  
- Stores key-value entries in memory.  
- Handles eviction policy & write policy.  
- Connects to persistent storage (MySQL/Postgres).  

### 3. **Eviction Policy Module**  
- Classes:  
  - `LRUEvictionStrategy`  
  - `LFUEvictionStrategy`  
- Ensures cache doesn’t exceed capacity.  

### 4. **Write Policy Module**  
- Classes:  
  - `WriteThroughStrategy` → Writes immediately to DB.  
  - `WriteBackStrategy` → Defers writes until eviction/update.  

### 5. **Storage Layer**  
- Classes:  
  - `MySqlDatabase`  
  - `PostgresDatabase`  
- Factory: `StorageFactory` → Configures backend dynamically.  

### 6. **Request Collapser**  
- Class: `RequestCollapser<K,V>`  
- Collapses concurrent requests for the same key into one DB/cache fetch.  

### 7. **Striped Executor Service**  
- Class: `StripedExecutorService`  
- Guarantees **per-key task ordering** while allowing concurrency across keys.  

### 8. **Client API**  
- Class: `CacheClient<K,V>`  
- Provides **async API**:  
  - `putAsync(key, value)`  
  - `getAsync(key)`  
  - `deleteAsync(key)`  

---

## 🔐 Reliability & Consistency  
- Ensures **fault tolerance** with consistent hashing.  
- Maintains **write consistency** via configurable strategies.  
- Collapses duplicate requests to reduce load on storage.  

---

## 🌐 API Examples  

| Operation       | Method Example                                  | Description                       |
|-----------------|------------------------------------------------|-----------------------------------|
| Put             | `cacheClient.putAsync("user:1", "Alice")`      | Adds a key-value entry            |
| Get             | `cacheClient.getAsync("user:1")`               | Retrieves value by key            |
| Delete          | `cacheClient.deleteAsync("user:1")`            | Removes key from cache            |
| Eviction        | Automatic (LRU/LFU)                           | Removes least-used entries        |
| Write Policy    | Configurable (Write-Through / Write-Back)     | Defines how DB writes occur       |

---

## 🛠️ Tech Stack  
- **Language:** Java 17+  
- **Concurrency:** `ExecutorService`, `CompletableFuture`  
- **Storage:** MySQL, PostgreSQL (pluggable)  
- **Design Patterns:** Factory, Strategy, Collapser  
- **Build Tool:** Maven  

---

## 📂 Project Structure  
```
distributed-cache/
│── src/main/java/com/cache/
│   ├── cache/               # Core cache classes (CacheNode, DistributedCache, Client)
│   ├── evictionpolicies/    # Eviction strategies (LRU, LFU)
│   ├── writepolicies/       # Write strategies (Write-Through, Write-Back)
│   ├── storage/             # Storage backends (MySQL, Postgres, Factory)
│   ├── utils/               # Utility classes (RequestCollapser, StripedExecutorService)
│── README.md                # Project documentation
```

---

## 🚀 Getting Started  

### Prerequisites  
- Java 17+  
- Maven 3.8+  
- MySQL/Postgres (optional, for persistence)  

### Running the Project  
1. Clone the repository:  
   ```bash
   git clone <repo-url>
   cd distributed-cache
   ```

2. Build the project:  
   ```bash
   mvn clean install
   ```

3. Run Example (inside `Main.java` or test client):  
   ```java
   CacheClient<String, String> client = new CacheClient<>(...);
   client.putAsync("key1", "value1");
   client.getAsync("key1").get(); // returns value1
   ```
