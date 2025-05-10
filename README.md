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

Both techniques help make your error handling more robust and maintainable!

# Advance Java || Generics



