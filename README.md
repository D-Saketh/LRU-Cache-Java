# ⚡ LRU Cache — Java Low-Level Design

A **modular in-memory caching system implemented in Java**, designed using low-level design principles to demonstrate how a cache can support different **eviction algorithms, storage mechanisms, and write policies**.

The current implementation uses **LRU (Least Recently Used) eviction** with a **HashMap + Doubly Linked List**, providing **O(1)** average-time `get` and `put` operations.

---

## 🎯 Project Intent

The goal of this project is not just to implement an LRU cache.

It is designed to model how a real caching system can be structured using **separation of concerns and interchangeable components**.

```text
                    Cache
                      │
          ┌───────────┼───────────┐
          ↓           ↓           ↓
      Eviction     Storage      Write
      Strategy     Mechanism     Policy
          │           │           │
         LRU      In-Memory    Write-Through
```

This design makes it possible to change one part of the system without tightly coupling it to the others.

---

# 🧠 Core Design

## LRU Eviction

The cache maintains recently used entries using:

* **HashMap** for O(1) key lookup
* **Doubly Linked List** for O(1) insertion, deletion, and reordering

### Cache ordering

```text
Most Recently Used                    Least Recently Used

      Head
       ↓
   [K1] ⇄ [K2] ⇄ [K3] ⇄ [K4]
                           ↑
                          Tail
```

When an entry is accessed, it is moved to the front.

When the cache reaches its capacity, the entry at the tail is removed.

---

# ⚙️ Operations

### `get(key)`

```text
1. Search key in HashMap
2. If key exists:
      - Move node to the front
      - Return value
3. Otherwise:
      - Return cache miss
```

**Average Time Complexity: O(1)**

### `put(key, value)`

```text
1. Check whether key already exists
2. Update existing entry or create a new node
3. Move node to the front
4. If capacity is exceeded:
      - Remove least recently used node
      - Remove it from HashMap
```

**Average Time Complexity: O(1)**

---

# 🏗️ Architecture

```text
                    ┌──────────────────┐
                    │      Cache       │
                    └────────┬─────────┘
                             │
             ┌───────────────┼────────────────┐
             ↓               ↓                ↓
      ┌─────────────┐ ┌─────────────┐ ┌──────────────┐
      │  Eviction   │ │   Storage   │ │    Write     │
      │  Algorithm  │ │  Mechanism  │ │    Policy    │
      └──────┬──────┘ └──────┬──────┘ └──────┬───────┘
             │               │                │
             ↓               ↓                ↓
           LRU          In-Memory         Write-Through
```

The system is organized around interfaces so that implementations can be replaced without changing the core cache logic.

---

# 📂 Project Structure

```text
src/
│
├── EvictionAlgorithms/
│   ├── EvictionAlgorithm.java
│   └── ConcreteEvictionAlgorithms/
│       └── LRUEvictionAlgorithm.java
│
├── Executors/
│   └── KeyBasedExecutor.java
│
├── MainCache/
│   └── Cache.java
│
├── StorageMechanisms/
│   ├── Interfaces/
│   │   ├── CacheStorage.java
│   │   └── DBStorage.java
│   │
│   └── ConcreteStorages/
│       ├── ConcreteCacheStorages/
│       │   └── InMemoryCacheStorage.java
│       │
│       └── ConcreteDBStorages/
│           └── SimpleDBStorage.java
│
├── UtilityClasses/
│   ├── DoublyLinkedList.java
│   └── DoublyLinkedListNode.java
│
├── WritePolicies/
│   ├── WritePolicy.java
│   └── ConcreteWritePolicies/
│       └── WriteThroughPolicy.java
│
└── Main.java
```

---

# 🔑 Key Design Components

### Cache

Acts as the main entry point for cache operations and coordinates the different components.

### Eviction Algorithm

Defines how entries should be removed when the cache reaches its capacity.

Current implementation:

**LRU — Least Recently Used**

The design allows additional eviction strategies to be introduced independently.

### Storage Mechanism

Separates cache storage from the cache management logic.

Current implementation includes:

* In-memory cache storage
* Database storage abstraction

### Write Policy

Defines how changes made to the cache are propagated to persistent storage.

Current implementation:

**Write-Through Policy**

### Doubly Linked List

Maintains the usage order required by the LRU algorithm.

### Key-Based Executor

Provides the execution layer for cache operations based on keys.

---

# 🧩 Design Patterns & Principles

The project demonstrates several important low-level design concepts:

* **Strategy Pattern** — interchangeable eviction algorithms and write policies
* **Interface-based design** — components depend on abstractions
* **Separation of concerns** — eviction, storage, and write behavior are isolated
* **Composition over inheritance** — cache behavior is assembled from independent components
* **Encapsulation** — internal cache structures are hidden behind well-defined operations

---

# ⏱️ Complexity

| Operation          | Average Time |
| ------------------ | -----------: |
| `get(key)`         |     **O(1)** |
| `put(key, value)`  |     **O(1)** |
| Remove LRU entry   |     **O(1)** |
| HashMap lookup     |     **O(1)** |
| Linked-list update |     **O(1)** |

### Space Complexity

**O(capacity)**

The HashMap and Doubly Linked List store at most the configured cache capacity of entries.

---

# 🔄 Example

Assume the cache capacity is **3**.

```text
put(1, A)
put(2, B)
put(3, C)
```

Cache order:

```text
Most Recent                    Least Recent

[3] ⇄ [2] ⇄ [1]
```

Access key `1`:

```text
get(1)
```

Now:

```text
[1] ⇄ [3] ⇄ [2]
```

Add another entry:

```text
put(4, D)
```

The least recently used entry `2` is removed:

```text
[4] ⇄ [1] ⇄ [3]
```

---

# 🛠️ Technologies

* **Java**
* Object-Oriented Programming
* HashMap
* Doubly Linked List
* Interfaces & Abstraction
* Low-Level Design
* Design Patterns
* Data Structures & Algorithms

---

# 🚀 Future Enhancements

* Add LFU eviction strategy
* Add FIFO eviction strategy
* Support configurable cache capacity
* Add thread-safe cache operations
* Add concurrent access handling
* Add cache expiration / TTL
* Add cache statistics such as hit rate and miss rate
* Add unit tests
* Add benchmarking for different eviction strategies

---

# 📌 Key Takeaways

This project demonstrates how to move from a basic **LRU Cache implementation** toward a more extensible **cache system design**.

The central design idea is:

```text
                    Cache System
                         │
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
   Eviction           Storage           Write
   Strategy          Mechanism          Policy
        │                │                │
       LRU           In-Memory       Write-Through
```

The components are designed to remain independent, making the system easier to extend and maintain.

---

## 👨‍💻 Author

**Darimireddy Saketh Ram**

GitHub: [D-Saketh](https://github.com/D-Saketh)
