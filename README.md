# Advance Java || Exception 

# Explanation of Custom Exceptions and Chaining Exceptions for Entry-Level Software Engineers

## Custom Exceptions

### What are Custom Exceptions?
Custom exceptions are exception classes that you create yourself to represent specific error conditions in your application. While programming languages provide many built-in exceptions (like `NullPointerException` or `IOException`), sometimes you need more specific exceptions that better describe problems in your particular application.

### Why use Custom Exceptions?
1. **Specific error handling**: You can catch and handle very specific error conditions
2. **Better code organization**: Related errors can be grouped under a common parent exception
3. **More informative**: You can include additional information about what went wrong
4. **Domain-specific**: They make your code more expressive about your business domain

### How to create Custom Exceptions (Java Example)
```java
// Custom exception for when a user is not found
public class UserNotFoundException extends Exception {
    private String username;
    
    public UserNotFoundException(String username) {
        super("User " + username + " not found");
        this.username = username;
    }
    
    public String getUsername() {
        return username;
    }
}

// Usage
public User findUser(String username) throws UserNotFoundException {
    // If user not found in database
    throw new UserNotFoundException(username);
}
```

### Best Practices
1. Typically extend `Exception` (for checked exceptions) or `RuntimeException` (for unchecked)
2. Provide constructors that take meaningful error messages
3. Include any additional fields that might help with error handling
4. Name them clearly (usually ending with "Exception")

## Chaining Exceptions

### What is Exception Chaining?
Exception chaining (or exception wrapping) is when you catch one exception and throw another, while preserving the original exception. This allows you to:
- Convert low-level exceptions to higher-level ones
- Add more context to an exception
- Maintain the complete error history

### Why use Exception Chaining?
1. **Abstraction**: Hide implementation details from calling code
2. **Context**: Add more application-specific information
3. **Preservation**: Don't lose the original cause of the error
4. **Debugging**: Stack traces show the complete chain of failures

### How to chain exceptions (Java Example)
```java
try {
    // Code that might throw IOException
    FileInputStream file = new FileInputStream("config.txt");
} catch (IOException e) {
    // Wrap the low-level IO exception in our application exception
    throw new ApplicationConfigException("Failed to load configuration", e);
}

// ApplicationConfigException would look like:
public class ApplicationConfigException extends Exception {
    public ApplicationConfigException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

### Key Points about Chaining
1. The original exception becomes the "cause" of the new exception
2. When the exception is logged, you'll see both stack traces
3. You can access the original exception with `getCause()`
4. This pattern is common when bridging between layers (e.g., database → service → UI)

### Example of accessing chained exceptions:
```java
try {
    loadApplication();
} catch (ApplicationConfigException e) {
    System.out.println("Error: " + e.getMessage());
    System.out.println("Root cause: " + e.getCause().getMessage());
    e.printStackTrace(); // Will show both exceptions
}
```

## Summary Table

| Concept | Purpose | When to Use | Key Benefit |
|---------|---------|-------------|-------------|
| **Custom Exceptions** | Create domain-specific error types | When built-in exceptions aren't specific enough | Better expresses your application's error conditions |
| **Exception Chaining** | Wrap and preserve exceptions | When translating between abstraction layers | Maintains complete error history while adding context |


# The `Comparable` Interface in Java - Explained for Entry-Level Engineers

## What is the `Comparable` Interface?

The `Comparable` interface is a fundamental Java interface used to define the **natural ordering** of objects. It allows objects of a class to be compared to each other, making it possible to sort collections of those objects.

## Key Points

1. **Part of Java's core**: Found in `java.lang` package (no import needed)
2. **Single-method interface**: Only requires implementing `compareTo()`
3. **Used for sorting**: Enables automatic sorting in collections like `Arrays.sort()` or `Collections.sort()`

## The `compareTo()` Method

```java
public interface Comparable<T> {
    int compareTo(T other);
}
```

The `compareTo()` method returns:
- **Negative number** if "this" object is less than the "other" object
- **Zero** if they are equal
- **Positive number** if "this" object is greater than the "other" object

## Example: Implementing Comparable

Let's create a `Person` class that can be sorted by age:

```java
public class Person implements Comparable<Person> {
    private String name;
    private int age;
    
    // Constructor
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    @Override
    public int compareTo(Person other) {
        // Compare based on age
        return this.age - other.age;
    }
    
    // Getters and toString() omitted for brevity
}
```

## Using the Comparable Implementation

```java
public class Main {
    public static void main(String[] args) {
        List<Person> people = new ArrayList<>();
        people.add(new Person("Alice", 30));
        people.add(new Person("Bob", 25));
        people.add(new Person("Charlie", 35));
        
        // Sorting uses our compareTo() implementation
        Collections.sort(people);
        
        // People are now sorted by age
        System.out.println(people); 
        // [Bob (25), Alice (30), Charlie (35)]
    }
}
```

## Important Rules for `compareTo()`

1. **Consistency with equals**: Should generally return 0 when `equals()` returns true
2. **Sign symmetry**: `a.compareTo(b)` should return the opposite sign of `b.compareTo(a)`
3. **Transitivity**: If `a > b` and `b > c`, then `a > c`

## Common Implementation Patterns

1. **Numerical comparison**:
   ```java
   return this.value - other.value;  // Simple for numbers
   ```

2. **String comparison**:
   ```java
   return this.name.compareTo(other.name);  // Use String's compareTo
   ```

3. **Multiple fields**:
   ```java
   int result = this.lastName.compareTo(other.lastName);
   if (result == 0) {
       result = this.firstName.compareTo(other.firstName);
   }
   return result;
   ```

## When to Use Comparable

1. When there's a single, natural ordering for your objects
2. When you want objects to be sortable by default
3. When working with sorted collections like `TreeSet` or `TreeMap`

## Comparable vs. Comparator

| Feature        | Comparable | Comparator |
|---------------|------------|------------|
| Package       | java.lang  | java.util  |
| Method        | compareTo() | compare() |
| Sorting logic | Inside the class | Separate class |
| Number of orderings | One (natural) | Many (custom) |

## Best Practices

1. Handle null values appropriately (usually throw NullPointerException)
2. Document the ordering behavior in your class
3. Consider using `Integer.compare()`, `Double.compare()` etc. to avoid overflow issues
4. Make your class immutable if possible when implementing Comparable

Understanding `Comparable` is essential for working with collections and writing clean, maintainable Java code!
Both techniques help make your error handling more robust and maintainable!

# **Generics, Generic Classes/Interfaces, and Wildcards in Java**  
*(Explained for Entry-Level Software Engineers)*  

## **1. Generics Overview**  
Generics in Java allow you to write **type-safe** and **reusable** code by enabling **classes, interfaces, and methods** to work with different data types while maintaining compile-time type checking.  

### **Why Use Generics?**  
✔ **Type Safety** → Prevents `ClassCastException` at runtime.  
✔ **Code Reusability** → Write a single class/method that works with multiple types.  
✔ **No Need for Typecasting** → Compiler ensures correct types are used.  

---

## **2. Generic Classes**  
A **generic class** is a class that can work with any data type. It is defined with a **type parameter** (`<T>`, `<K,V>`, etc.).  

### **Example: A Simple Generic Box Class**  
```java
public class Box<T> {  // T is a type parameter
    private T content;

    public void setContent(T content) {
        this.content = content;
    }

    public T getContent() {
        return content;
    }
}
```
### **Usage:**
```java
Box<String> stringBox = new Box<>();
stringBox.setContent("Hello");
String value = stringBox.getContent();  // No typecasting needed!

Box<Integer> intBox = new Box<>();
intBox.setContent(100);
int num = intBox.getContent();  // Works with Integer too!
```

### **Key Points:**
- `T` (or any placeholder like `E`, `K`, `V`) represents a generic type.
- At compile time, Java replaces `T` with the actual type (`String`, `Integer`, etc.).
- Helps avoid runtime errors by enforcing type safety.

---

## **3. Generic Interfaces**  
Similar to generic classes, **interfaces** can also be generic.  

### **Example: A Generic List Interface**  
```java
public interface List<T> {
    void add(T element);
    T get(int index);
}
```
### **Implementation:**
```java
public class CustomList<T> implements List<T> {
    private T[] elements;
    private int size;

    public void add(T element) {
        elements[size++] = element;
    }

    public T get(int index) {
        return elements[index];
    }
}
```
### **Usage:**
```java
List<String> names = new CustomList<>();
names.add("Alice");
String name = names.get(0);  // Type-safe!
```

---

## **4. Wildcards (`?`) in Generics**  
Wildcards (`?`) allow **flexibility** when working with unknown types. They are used in **method parameters** to accept different generic types.  

### **Types of Wildcards:**  

### **(1) Unbounded Wildcard (`<?>`)**
- Accepts **any type**.
- Used when the method works with any generic type but does not modify it.

#### **Example:**
```java
public void printList(List<?> list) {
    for (Object item : list) {
        System.out.println(item);
    }
}
```
#### **Usage:**
```java
List<String> names = List.of("Alice", "Bob");
List<Integer> numbers = List.of(1, 2, 3);

printList(names);    // Works!
printList(numbers);  // Also works!
```

---

### **(2) Upper-Bounded Wildcard (`<? extends T>`)**
- Accepts **any subtype of `T`** (e.g., `Number` → `Integer`, `Double`).
- Used for **read-only** operations.

#### **Example: Sum of Numbers**
```java
public double sum(List<? extends Number> numbers) {
    double sum = 0.0;
    for (Number num : numbers) {
        sum += num.doubleValue();
    }
    return sum;
}
```
#### **Usage:**
```java
List<Integer> integers = List.of(1, 2, 3);
List<Double> doubles = List.of(1.5, 2.5);

System.out.println(sum(integers));  // 6.0
System.out.println(sum(doubles));   // 4.0
```

---

### **(3) Lower-Bounded Wildcard (`<? super T>`)**
- Accepts **any supertype of `T`** (e.g., `Object` → `String`).
- Used for **write operations** (e.g., adding elements).

#### **Example: Adding Numbers to a List**
```java
public void addNumbers(List<? super Integer> list) {
    list.add(10);
    list.add(20);
}
```
#### **Usage:**
```java
List<Number> numbers = new ArrayList<>();
List<Object> objects = new ArrayList<>();

addNumbers(numbers);  // Works (Number is super of Integer)
addNumbers(objects);  // Works (Object is super of Integer)
```

---

## **5. Key Differences & When to Use What**  

| Feature | Generic Class/Interface (`<T>`) | Unbounded Wildcard (`<?>`) | Upper-Bounded (`<? extends T>`) | Lower-Bounded (`<? super T>`) |
|---------|-------------------------------|---------------------------|--------------------------------|-----------------------------|
| **Purpose** | Define reusable type-safe classes/interfaces | Accept any unknown type | Accept subtypes of `T` (read-only) | Accept supertypes of `T` (write operations) |
| **Example** | `Box<T>`, `List<T>` | `List<?>` | `List<? extends Number>` | `List<? super Integer>` |
| **Flexibility** | Fixed type at instantiation | Works with any type | Works with subtypes | Works with supertypes |
| **Use Case** | Creating generic data structures | Processing unknown lists | Reading numbers (e.g., `sum()`) | Adding elements (e.g., `addNumbers()`) |

---

## **6. Best Practices**  
✔ **Prefer generics** over raw types (`List` → `List<String>`).  
✔ **Use wildcards** when methods need flexibility (`List<?>`).  
✔ **Upper bounds (`? extends`)** for **read-only** operations.  
✔ **Lower bounds (`? super`)** for **write** operations.  

---

## **Summary**  
- **Generic Classes/Interfaces (`<T>`)** → Reusable type-safe structures.  
- **Wildcards (`?`)** → Flexibility in method parameters.  
  - `<?>` → Any type (read-only).  
  - `<? extends T>` → Subtypes (read-only).  
  - `<? super T>` → Supertypes (write operations).  

Generics make Java code **safer**, **more reusable**, and **less prone to runtime errors**! 🚀

# **The `Iterator` Interface in Java - Explained for Beginners**

## **What is the `Iterator` Interface?**
The `Iterator` is a Java interface that provides a **standard way to traverse through elements** in a collection one by one. It's like a "cursor" that moves through your data.

- **Package:** `java.util.Iterator`
- **Key Methods:** `hasNext()`, `next()`, `remove()`

## **Why Do We Need Iterators?**
1. **Safe traversal:** Avoids `ConcurrentModificationException` during iteration
2. **Uniform access:** Works the same way across all collections
3. **Flexible removal:** Allows removing elements while iterating

## **Core Iterator Methods**

| Method | Returns | Purpose |
|--------|---------|---------|
| `boolean hasNext()` | `true`/`false` | Checks if more elements exist |
| `E next()` | Next element | Returns the next element |
| `void remove()` | - | Removes last returned element (optional) |

## **Basic Iterator Example**

```java
List<String> fruits = new ArrayList<>();
fruits.add("Apple");
fruits.add("Banana");
fruits.add("Orange");

// Get an iterator
Iterator<String> iterator = fruits.iterator();

// Traverse the collection
while (iterator.hasNext()) {
    String fruit = iterator.next();
    System.out.println(fruit);
    
    // Safely remove "Banana" if found
    if (fruit.equals("Banana")) {
        iterator.remove();
    }
}
```

**Output:**
```
Apple
Banana
Orange
```
(After iteration, "Banana" is removed from the list)

## **Key Features of Iterators**

1. **Fail-Fast Behavior:** 
   - Throws `ConcurrentModificationException` if collection is modified while iterating (except through iterator's own `remove()`)

2. **Single Direction:**
   - Can only move forward (no `previous()` like in `ListIterator`)

3. **One-Time Use:**
   - After reaching the end, iterator is exhausted (must get new one to restart)

## **Iterator vs For-Each Loop**

```java
// For-each loop (uses Iterator internally)
for (String fruit : fruits) {
    System.out.println(fruit);
    // fruits.remove(fruit); ← Would throw exception!
}

// Explicit Iterator (allows removal)
Iterator<String> it = fruits.iterator();
while (it.hasNext()) {
    String fruit = it.next();
    if (condition) {
        it.remove(); // Safe removal
    }
}
```

## **When to Use Iterator Directly**

1. When you need to **remove elements** during iteration
2. When working with **non-List collections** (like `Set`, `Queue`)
3. When implementing **custom collection** classes

## **Implementing Your Own Iterator**

```java
class Countdown implements Iterable<Integer> {
    private int start;
    
    public Countdown(int start) {
        this.start = start;
    }
    
    @Override
    public Iterator<Integer> iterator() {
        return new Iterator<>() {
            private int current = start;
            
            @Override
            public boolean hasNext() {
                return current > 0;
            }
            
            @Override
            public Integer next() {
                if (!hasNext()) throw new NoSuchElementException();
                return current--;
            }
        };
    }
}

// Usage:
for (int num : new Countdown(5)) {
    System.out.println(num); // Prints 5,4,3,2,1
}
```

## **Common Pitfalls**

1. **Calling `next()` without `hasNext()`**
   - Throws `NoSuchElementException` if no more elements

2. **Modifying collection while iterating**
   - Always use `iterator.remove()` instead of collection's `remove()`

3. **Assuming multiple active iterators**
   - Each iterator maintains its own position

## **Summary**

- `Iterator` provides safe, standardized collection traversal
- Required for implementing `Iterable` (which enables for-each loops)
- Essential for proper collection modification during iteration
- Used extensively in Java Collections Framework

Now you can traverse collections like a pro! 🚀

# **Understanding Comparable and Comparator in Java**

## **1. Comparable Interface (Natural Ordering)**

### **What is Comparable?**
- Used to define the **natural/default ordering** of objects
- Implemented by the class itself
- Modifies the original class

### **Key Points:**
✔ Single method: `compareTo(T o)`  
✔ Returns: negative (less than), zero (equal), positive (greater than)  
✔ Used by `Collections.sort()` and sorted collections like `TreeSet`

### **Example: Sorting Persons by Age**
```java
class Person implements Comparable<Person> {
    String name;
    int age;
    
    @Override
    public int compareTo(Person other) {
        return this.age - other.age; // Sort by age
    }
}

// Usage:
List<Person> people = new ArrayList<>();
Collections.sort(people); // Uses compareTo
```

### **When to Use Comparable?**
- When there's one obvious natural ordering
- When you control the class source code
- For default sorting behavior

---

## **2. Comparator Interface (Custom Ordering)**

### **What is Comparator?**
- Used to define **multiple custom sorting** strategies
- Implemented as separate classes
- Doesn't modify original class

### **Key Points:**
✔ Single method: `compare(T o1, T o2)`  
✔ Can create multiple comparators for different sortings  
✔ More flexible than Comparable

### **Example: Different Sorting Strategies**
```java
// Name comparator
class NameComparator implements Comparator<Person> {
    @Override
    public int compare(Person p1, Person p2) {
        return p1.name.compareTo(p2.name);
    }
}

// Age comparator
class AgeComparator implements Comparator<Person> {
    @Override
    public int compare(Person p1, Person p2) {
        return p1.age - p2.age;
    }
}

// Usage:
Collections.sort(people, new NameComparator()); // Sort by name
Collections.sort(people, new AgeComparator()); // Sort by age
```

### **Java 8+ Lambda Syntax**
```java
// Sort by name (shorter syntax)
Collections.sort(people, (p1, p2) -> p1.name.compareTo(p2.name));

// Sort by age descending
Collections.sort(people, (p1, p2) -> p2.age - p1.age);
```

---

## **Key Differences**

| Feature        | Comparable | Comparator |
|---------------|------------|------------|
| **Package**   | java.lang  | java.util  |
| **Method**    | compareTo() | compare() |
| **Sorting Logic** | Inside the class | Separate class |
| **Number of Orderings** | One (natural) | Many (custom) |
| **Class Modification** | Required | Not required |
| **Lambda Friendly** | No | Yes |

---

## **When to Use Which?**

### **Use Comparable when:**
- There's a single obvious natural ordering
- You have control over the class source code
- Default sorting behavior is needed

### **Use Comparator when:**
- You need multiple sorting strategies
- You can't modify the original class
- You need temporary or custom sorting
- You want to sort using Java 8 lambdas

---

## **Best Practices**

1. **For Comparable:**
   - Keep consistent with `equals()`
   - Handle null values carefully
   - Use `Integer.compare(a,b)` instead of `a-b` to avoid overflow

2. **For Comparator:**
   - Make comparators `Serializable` if needed
   - Consider using `Comparator.comparing()` for cleaner code
   - Chain comparators with `thenComparing()`

```java
// Advanced Comparator example
Comparator<Person> comparator = Comparator
    .comparing(Person::getLastName)
    .thenComparing(Person::getFirstName)
    .thenComparingInt(Person::getAge);
```

---

## **Summary**

- **Comparable** → For natural/default ordering (modifies class)
- **Comparator** → For custom/multiple orderings (external to class)
- Both essential for sorting objects in Java
- Modern Java prefers Comparator for its flexibility

Now you can sort objects any way you need! 🚀

# **The Map Interface in Java - Explained for Beginners**

## **What is a Map?**

A **Map** is a Java collection that stores data as **key-value pairs** (like a dictionary). Each key must be unique, and each key maps to exactly one value.

### **Key Characteristics:**
- **Unique keys** (no duplicates)
- **Each key maps to one value**
- **Not a subtype of Collection interface** (different hierarchy)
- **Common implementations**: `HashMap`, `TreeMap`, `LinkedHashMap`

## **Why Use Maps?**
- Fast lookup by key (O(1) for HashMap)
- Store associative data (e.g., userID → User object)
- Eliminate duplicate keys automatically
- Flexible value storage (can store objects, collections, etc.)

## **Core Map Methods**

| Method | Description | Example |
|--------|-------------|---------|
| `V put(K key, V value)` | Adds key-value pair | `map.put("Alice", 25)` |
| `V get(Object key)` | Returns value for key | `map.get("Alice")` → 25 |
| `boolean containsKey(Object key)` | Checks if key exists | `map.containsKey("Bob")` |
| `V remove(Object key)` | Removes key-value pair | `map.remove("Alice")` |
| `Set<K> keySet()` | Returns all keys | `map.keySet()` |
| `Collection<V> values()` | Returns all values | `map.values()` |
| `Set<Map.Entry<K,V>> entrySet()` | Returns key-value pairs | `map.entrySet()` |
| `int size()` | Returns number of pairs | `map.size()` |

## **Common Map Implementations**

1. **HashMap**
   - Most common implementation
   - No ordering guarantees
   - O(1) time for basic operations
   ```java
   Map<String, Integer> ages = new HashMap<>();
   ```

2. **TreeMap**
   - Sorted by keys (natural ordering or Comparator)
   - O(log n) time for operations
   ```java
   Map<String, Integer> sortedAges = new TreeMap<>();
   ```

3. **LinkedHashMap**
   - Maintains insertion order
   - Slower than HashMap but predictable iteration
   ```java
   Map<String, Integer> orderedAges = new LinkedHashMap<>();
   ```

## **Basic Map Example**

```java
Map<String, Integer> studentGrades = new HashMap<>();

// Adding entries
studentGrades.put("Alice", 90);
studentGrades.put("Bob", 85);
studentGrades.put("Charlie", 95);

// Accessing values
int aliceGrade = studentGrades.get("Alice"); // 90

// Checking existence
boolean hasBob = studentGrades.containsKey("Bob"); // true

// Iterating (3 ways)
// 1. Key iteration
for (String name : studentGrades.keySet()) {
    System.out.println(name);
}

// 2. Value iteration
for (Integer grade : studentGrades.values()) {
    System.out.println(grade);
}

// 3. Entry iteration (most common)
for (Map.Entry<String, Integer> entry : studentGrades.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
```

## **Important Map Behaviors**

1. **Duplicate Keys**
   ```java
   map.put("Alice", 25);
   map.put("Alice", 30); // Overwrites previous value
   ```

2. **Null Handling**
   - HashMap/LinkedHashMap: Allows one null key and multiple null values
   - TreeMap: Doesn't allow null keys (throws NullPointerException)

3. **Immutable Maps (Java 9+)**
   ```java
   Map<String, Integer> immutable = Map.of(
       "Alice", 25,
       "Bob", 30
   );
   ```

## **When to Use Which Map?**

| Situation | Recommended Map |
|-----------|-----------------|
| Need fastest access, don't care about order | HashMap |
| Need sorted keys | TreeMap |
| Need insertion-order iteration | LinkedHashMap |
| Need thread-safety | ConcurrentHashMap |
| Read-only map | Map.of() (Java 9+) |

## **Common Use Cases**

1. **Database-like lookups**
   ```java
   Map<Integer, User> userDatabase = new HashMap<>();
   ```

2. **Counting occurrences**
   ```java
   Map<String, Integer> wordCounts = new HashMap<>();
   for (String word : words) {
       wordCounts.merge(word, 1, Integer::sum);
   }
   ```

3. **Caching**
   ```java
   Map<String, ExpensiveObject> cache = new HashMap<>();
   ```

## **Best Practices**

1. Always specify generic types (`Map<K,V>`)
2. Use `entrySet()` for iteration (most efficient)
3. Consider `compute()`/`merge()` for complex updates
4. For thread safety, use `ConcurrentHashMap` or `Collections.synchronizedMap()`
5. Override `equals()` and `hashCode()` properly for custom key objects

## **Java 8+ Enhancements**

```java
// Default value if key absent
int grade = studentGrades.getOrDefault("Dave", -1);

// Compute if absent
studentGrades.computeIfAbsent("Alice", k -> calculateGrade(k));

// Merge values
studentGrades.merge("Alice", 10, Integer::sum);
```

Now you're ready to use Maps effectively in your Java programs! They're one of the most useful data structures for real-world programming tasks.

# **Functional Interfaces in Java - Explained Simply**

## **What is a Functional Interface?**

A functional interface is **an interface with exactly one abstract method** (but can have multiple default/static methods). They enable **lambda expressions** and **method references** in Java.

### **Key Characteristics:**
- Must have **only one abstract method** (SAM - Single Abstract Method)
- Can have **any number of default/static methods**
- Often annotated with `@FunctionalInterface` (optional but recommended)
- Used extensively with Java 8's **lambda expressions**

## **Why Use Functional Interfaces?**
- Enable **functional programming** in Java
- Make code more **concise** with lambdas
- Support **behavior parameterization** (passing functions as arguments)
- Foundation for Java's **Stream API**

## **Built-in Functional Interfaces (java.util.function)**

| Interface          | Method         | Description | Common Use |
|--------------------|----------------|-------------|------------|
| `Supplier<T>`      | `T get()`      | Provides values | Lazy initialization |
| `Consumer<T>`      | `void accept(T)` | Consumes values | ForEach operations |
| `Predicate<T>`     | `boolean test(T)` | Condition check | Filtering |
| `Function<T,R>`    | `R apply(T)`   | Transforms input | Mapping |
| `UnaryOperator<T>` | `T apply(T)`   | Same-type transform | Math operations |
| `BiFunction<T,U,R>`| `R apply(T,U)` | Two-arg function | Combining values |

## **Example: Creating a Functional Interface**

```java
@FunctionalInterface
interface StringProcessor {
    String process(String input);  // Single abstract method
    
    default void log(String msg) {  // Default method allowed
        System.out.println("Log: " + msg);
    }
}
```

## **Using Functional Interfaces with Lambdas**

```java
// Traditional way (anonymous class)
StringProcessor oldWay = new StringProcessor() {
    @Override
    public String process(String input) {
        return input.toUpperCase();
    }
};

// Lambda way (much cleaner!)
StringProcessor lambdaWay = input -> input.toUpperCase();

System.out.println(lambdaWay.process("hello"));  // Prints "HELLO"
```

## **Common Built-in Functional Interfaces in Action**

### **1. Predicate (Tests a condition)**
```java
Predicate<String> isLong = s -> s.length() > 5;
System.out.println(isLong.test("Hello"));  // false
System.out.println(isLong.test("Hello World"));  // true
```

### **2. Function (Transforms input)**
```java
Function<String, Integer> lengthMapper = s -> s.length();
System.out.println(lengthMapper.apply("Java"));  // 4
```

### **3. Consumer (Performs action)**
```java
Consumer<String> printer = s -> System.out.println(">> " + s);
printer.accept("Functional!");  // Prints ">> Functional!"
```

### **4. Supplier (Provides values)**
```java
Supplier<Double> randomSupplier = () -> Math.random();
System.out.println(randomSupplier.get());  // Random number
```

## **Method References (Shortcut for Lambdas)**
```java
// Equivalent to: s -> System.out.println(s)
Consumer<String> printer = System.out::println;

// Equivalent to: s -> s.length()
Function<String, Integer> lengthGetter = String::length;
```

## **When to Create Custom Functional Interfaces?**
1. When none of the built-in interfaces fit your needs
2. When you need more descriptive method names
3. When working with specific domain operations

## **Best Practices**
✔ Always use `@FunctionalInterface` annotation  
✔ Prefer built-in interfaces when possible  
✔ Keep parameter/return types generic for reusability  
✔ Use method references where applicable for readability  

## **Real-World Example with Stream API**
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// Using Predicate and Consumer
names.stream()
     .filter(name -> name.startsWith("A"))  // Predicate
     .forEach(System.out::println);         // Consumer

// Using Function
List<Integer> lengths = names.stream()
                           .map(String::length)  // Function
                           .collect(Collectors.toList());
```

Functional interfaces are the backbone of modern Java programming, enabling clean, functional-style code! 🚀


# **Wildcards in Java Generics - Explained Simply**

Wildcards (`?`) are a powerful feature in Java generics that provide flexibility when working with unknown types. They're primarily used in method parameters to accept different generic type variations.

## **Types of Wildcards**

### **1. Unbounded Wildcard (`<?>`)**
Accepts **any type**, but in a read-only fashion.

```java
// Can accept List<String>, List<Integer>, etc.
public void printList(List<?> list) {
    for (Object item : list) {
        System.out.println(item);
    }
}
```

**When to use:** When you only need to read from the collection.

---

### **2. Upper-Bounded Wildcard (`<? extends T>`)**
Accepts **the specified type (T) or any of its subtypes**.

```java
// Accepts List<Number> + List<Integer>, List<Double>, etc.
public double sum(List<? extends Number> numbers) {
    double sum = 0.0;
    for (Number num : numbers) {
        sum += num.doubleValue();
    }
    return sum;
}
```

**When to use:** When you need **read-only** access to elements of a type hierarchy.

---

### **3. Lower-Bounded Wildcard (`<? super T>`)**
Accepts **the specified type (T) or any of its supertypes**.

```java
// Accepts List<Number>, List<Object> for Integer elements
public void addNumbers(List<? super Integer> list) {
    list.add(10);
    list.add(20);
}
```

**When to use:** When you need to **write** elements into a collection.

---

## **Key Differences**

| Wildcard Type | Syntax | Accepts | Typical Use |
|--------------|--------|---------|-------------|
| Unbounded | `<?>` | Any type | Read-only operations |
| Upper-Bounded | `<? extends T>` | T and its subtypes | Safe reading from collections |
| Lower-Bounded | `<? super T>` | T and its supertypes | Safe writing to collections |

## **Why Use Wildcards?**

1. **Flexibility**: Methods can accept wider range of generic types
2. **Type Safety**: Maintain compile-time checks while being permissive
3. **API Design**: Create more reusable methods in collections framework

## **Practical Example**

```java
// Works with any List (unbounded)
public static void printAll(List<?> list) {
    for (Object elem : list) {
        System.out.println(elem);
    }
}

// Accepts Number or subclasses (upper bound)
public static double sumNumbers(List<? extends Number> list) {
    return list.stream().mapToDouble(Number::doubleValue).sum();
}

// Accepts Integer or superclasses (lower bound)
public static void addIntegers(List<? super Integer> list) {
    list.add(1);
    list.add(2);
}
```

## **Common Pitfalls**

1. **Cannot instantiate wildcards**:
   ```java
   List<?> list = new ArrayList<?>(); // COMPILE ERROR
   ```

2. **Upper bounds are read-only**:
   ```java
   void add(List<? extends Number> list) {
       list.add(10); // COMPILE ERROR - unsafe
   }
   ```

3. **Lower bounds have restricted reading**:
   ```java
   void process(List<? super Integer> list) {
       Integer i = list.get(0); // COMPILE ERROR - could be Number/Object
   }
   ```

Wildcards help balance type safety with flexibility in Java generics, especially when designing APIs that need to work with multiple generic types!
