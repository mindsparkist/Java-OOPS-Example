# **Java Collection Framework - Complete Guide**

## **1. Collection Framework Hierarchy**

```
Iterable (Interface)
│
└── Collection (Interface)
    │
    ├── List (Interface)
    │   ├── ArrayList (Class)
    │   └── LinkedList (Class)
    │
    ├── Queue (Interface)
    │   └── PriorityQueue (Class)
    │
    └── Set (Interface)
        └── HashSet (Class)
```

## **2. Core Interfaces**

### **A. Iterable Interface**
- Root interface for all collections
- Provides iterator for sequential access
- Enables for-each loops
- Key method: `iterator()`

### **B. Collection Interface**
- Root interface for all collection types
- Common operations:
  ```java
  boolean add(E e)
  boolean remove(Object o)
  int size()
  boolean isEmpty()
  boolean contains(Object o)
  ```

## **3. List Interface**

**Characteristics:**
- Ordered collection (maintains insertion order)
- Allows duplicates
- Index-based access

### **Implementations:**

#### **1. ArrayList**
- **Resizable array** implementation
- Fast random access (O(1))
- Slow insertions/deletions in middle (O(n))
- Ideal for read-heavy operations
```java
List<String> list = new ArrayList<>();
list.add("Apple");  // O(1) amortized
list.get(0);        // O(1)
```

#### **2. LinkedList**
- **Doubly-linked list** implementation
- Fast insertions/deletions (O(1))
- Slow random access (O(n))
- Implements both List and Deque
```java
List<String> list = new LinkedList<>();
list.add("Apple");  // O(1)
list.get(0);        // O(n)
```

## **4. Queue Interface**

**Characteristics:**
- FIFO (First-In-First-Out) typically
- Used for holding elements prior to processing

### **Implementation:**

#### **PriorityQueue**
- Elements ordered by natural ordering or Comparator
- No null elements allowed
- Head is least element (for min-heap)
```java
Queue<Integer> pq = new PriorityQueue<>();
pq.offer(5);  // O(log n)
pq.poll();    // O(log n) - removes head
```

## **5. Set Interface**

**Characteristics:**
- No duplicate elements
- At most one null element
- No positional access

### **Implementation:**

#### **HashSet**
- Backed by hash table (HashMap)
- Constant-time basic operations (O(1))
- Unordered collection
```java
Set<String> set = new HashSet<>();
set.add("Apple");  // O(1)
set.contains("Apple");  // O(1)
```

## **Comparison Table**

| Collection | Ordering | Duplicates | Null Allowed | Access | Best For |
|------------|----------|------------|--------------|--------|----------|
| ArrayList | Insertion | Yes | Yes | Index (O(1)) | Frequent access |
| LinkedList | Insertion | Yes | Yes | Sequential (O(n)) | Frequent inserts/deletes |
| PriorityQueue | Sorted | No | No | Head only (O(log n)) | Priority processing |
| HashSet | None | No | One | Object (O(1)) | Unique elements |

## **When to Use Which?**

- **ArrayList**: When you need fast access by index
- **LinkedList**: When you need frequent add/remove at ends
- **PriorityQueue**: When you need priority-based processing
- **HashSet**: When you need uniqueness and fast lookups

The Collection Framework provides powerful, ready-to-use data structures that cover most programming needs!


