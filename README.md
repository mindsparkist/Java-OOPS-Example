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

# Advance Java || Generics



