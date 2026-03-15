# Chapter 2: Error Handling and Debugging

## Handling Errors Gracefully and Finding Bugs Systematically

---

## Introduction

No matter how carefully you write your code, errors are inevitable. Users will enter unexpected input. Files will be missing. Network connections will drop. Memory will run out. The question is not *whether* errors will occur, but *how your program will respond* when they do.

This chapter covers the three categories of errors you will encounter as a programmer -- **syntax errors**, **logical errors (bugs)**, and **runtime errors (exceptions)** -- and teaches you how to handle each one. You will learn Java's exception handling mechanism (`try`, `catch`, `finally`, `throw`), how to create custom exceptions, and best practices for writing robust error-handling code. The second half of the chapter covers **debugging** -- the systematic process of finding and fixing bugs using the tools built into modern IDEs.

---

## 1. What Is an Error?

In everyday language, an error is a mistake -- doing something incorrectly or producing an unintended result. In programming, errors fall into distinct categories based on *when* and *how* they manifest.

The three categories of errors in code are:

| Category | When It Appears | How It Is Detected | Example |
|----------|----------------|-------------------|---------|
| **Syntax Error** | Compile time | Compiler / IDE | Missing semicolon, mismatched braces |
| **Logical Error (Bug)** | Runtime (produces wrong result) | Testing / observation | Calculator showing 2 + 2 = 5 |
| **Runtime Error (Exception)** | Runtime (causes crash) | Exception mechanism | Division by zero, null pointer access |

Let us examine each category in detail.

---

## 2. Syntax Errors

### 2.1 What Are Syntax Errors?

Syntax errors are violations of the grammar rules of the programming language. They are the easiest errors to detect because the compiler (or the IDE's real-time analyzer) catches them *before* the code runs. The program simply will not compile until all syntax errors are fixed.

Think of syntax errors as grammatical mistakes in natural language. If you write "The cat on sat mat the," a human reader knows something is wrong even if they can guess your intent. Similarly, the Java compiler knows that `if (x = 5)` is not valid syntax for a condition.

### 2.2 Common Syntax Errors in Java

#### Missing Semicolons

```java
// Syntax error: missing semicolon
int x = 10
System.out.println(x);
```

```java
// Fixed
int x = 10;
System.out.println(x);
```

#### Mismatched Braces or Parentheses

```java
// Syntax error: missing closing brace
public void greet() {
    System.out.println("Hello");

```

```java
// Fixed
public void greet() {
    System.out.println("Hello");
}
```

#### Using Assignment Instead of Comparison

```java
// Syntax error in Java: = is assignment, not comparison
if (x = 5) {
    System.out.println("x is five");
}
```

```java
// Fixed: use == for comparison
if (x == 5) {
    System.out.println("x is five");
}
```

Note: In some languages like C/C++, `if (x = 5)` is valid syntax (it assigns 5 to x and evaluates the result as a boolean). In Java, this is a compile-time error when `x` is an `int`, because an `int` cannot be used as a boolean condition. Java's stricter type system protects you here.

#### Wrong Method Signature

```java
// Syntax error: return type mismatch
public int getName() {
    return "John"; // Cannot return String from int method
}
```

```java
// Fixed
public String getName() {
    return "John";
}
```

### 2.3 How to Deal with Syntax Errors

Syntax errors are the friendliest errors because:

1. **The compiler tells you exactly where they are** -- it provides the file name, line number, and a description of the problem.
2. **Your IDE highlights them in real time** -- modern IDEs like IntelliJ IDEA underline syntax errors with red squiggly lines as you type, often before you even try to compile.
3. **They are usually easy to fix** -- add a semicolon, close a brace, fix a typo.

The goal is to write code whose errors never get past the syntax category. The more serious errors -- logical and runtime errors -- are much harder to find and fix.

---

## 3. Logical Errors (Bugs)

### 3.1 What Are Logical Errors?

A logical error, commonly called a **bug**, is an error in the logic of your program. The code compiles successfully and runs without crashing, but it produces **incorrect results**. This makes bugs the most insidious type of error because there is no automatic notification that something is wrong.

The term "bug" in computing has a long history. The process of finding and fixing bugs is called **debugging**.

### 3.2 Examples of Logical Errors

#### Off-by-One Error

One of the most common bugs in programming:

```java
// Bug: prints one too few elements (misses the last one)
public void printArray(int[] numbers) {
    for (int i = 0; i < numbers.length - 1; i++) {  // Bug: should be < numbers.length
        System.out.println(numbers[i]);
    }
}
```

```java
// Fixed
public void printArray(int[] numbers) {
    for (int i = 0; i < numbers.length; i++) {
        System.out.println(numbers[i]);
    }
}
```

#### Wrong Operator

```java
// Bug: uses + instead of *
public double calculateRectangleArea(double length, double width) {
    return length + width;  // Bug! Should be length * width
}
```

The code compiles, runs, and returns a number -- but the wrong number. If you do not test carefully, this bug can go unnoticed for a long time.

#### Incorrect Boolean Logic

```java
// Bug: AND should be OR
public boolean isWeekend(String day) {
    return day.equals("Saturday") && day.equals("Sunday");
    // A day cannot be both Saturday AND Sunday at the same time!
    // This always returns false.
}
```

```java
// Fixed
public boolean isWeekend(String day) {
    return day.equals("Saturday") || day.equals("Sunday");
}
```

#### Wrong Order of Operations

```java
// Bug: calculates Fahrenheit to Celsius incorrectly
public double fahrenheitToCelsius(double fahrenheit) {
    return fahrenheit - 32 * 5 / 9;
    // Due to operator precedence, this computes: fahrenheit - ((32 * 5) / 9)
    // instead of: (fahrenheit - 32) * 5 / 9
}
```

```java
// Fixed
public double fahrenheitToCelsius(double fahrenheit) {
    return (fahrenheit - 32) * 5.0 / 9.0;
}
```

### 3.3 How to Prevent Bugs

- **Write unit tests** that verify your code produces the correct output for known inputs.
- **Test edge cases**: empty inputs, zero values, negative numbers, maximum values.
- **Review your logic carefully** before considering the code "done."
- **Use a debugger** (covered later in this chapter) to step through code and watch variables.
- **Rubber duck debugging**: explain your code line by line to someone (or something -- even a rubber duck). The act of explaining often reveals the flaw.

---

## 4. Runtime Errors and Exceptions

### 4.1 What Are Runtime Errors?

Runtime errors occur while the program is running. Unlike syntax errors (caught at compile time) and logical errors (which produce wrong results silently), runtime errors cause the program to **crash** -- unless they are handled properly.

In Java (and most modern languages), runtime errors are represented as **exceptions**. An exception is an object that describes an abnormal condition that has occurred during program execution.

### 4.2 Common Runtime Errors in Java

#### NullPointerException

The most notorious exception in Java. It occurs when you try to use a reference that points to `null`.

```java
String name = null;
int length = name.length();  // NullPointerException!
```

#### ArrayIndexOutOfBoundsException

Accessing an array element with an invalid index:

```java
int[] numbers = {1, 2, 3};
System.out.println(numbers[5]);  // ArrayIndexOutOfBoundsException!
```

#### ArithmeticException

Division by zero with integers:

```java
int result = 10 / 0;  // ArithmeticException: / by zero
```

#### NumberFormatException

Trying to convert a non-numeric string to a number:

```java
int value = Integer.parseInt("hello");  // NumberFormatException!
```

#### FileNotFoundException / IOException

Trying to read a file that does not exist:

```java
FileReader reader = new FileReader("nonexistent.txt");  // FileNotFoundException!
```

### 4.3 The Java Exception Hierarchy

All exceptions in Java are objects that inherit from the `Throwable` class:

```
Throwable
├── Error (serious, unrecoverable -- do NOT catch these)
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── ...
└── Exception (recoverable -- these are what you handle)
    ├── IOException
    ├── SQLException
    ├── RuntimeException (unchecked)
    │   ├── NullPointerException
    │   ├── ArrayIndexOutOfBoundsException
    │   ├── ArithmeticException
    │   ├── IllegalArgumentException
    │   └── ...
    └── ...
```

Key distinctions:

- **Errors** (`OutOfMemoryError`, `StackOverflowError`) are serious JVM-level problems. You generally should not catch or handle these.
- **Checked exceptions** (subclasses of `Exception` but not `RuntimeException`) must be either caught or declared in the method signature. The compiler enforces this.
- **Unchecked exceptions** (subclasses of `RuntimeException`) do not need to be declared or caught. They typically indicate programming errors (bugs).

---

## 5. Exception Handling: try, catch, finally

### 5.1 The try-catch Block

The `try-catch` block is the fundamental mechanism for handling exceptions in Java. You put the code that might throw an exception inside the `try` block, and the code that handles the exception inside the `catch` block.

```java
public class DivisionExample {
    public static void main(String[] args) {
        try {
            int numerator = 10;
            int denominator = 0;
            int result = numerator / denominator;
            System.out.println("Result: " + result);
        } catch (ArithmeticException e) {
            System.out.println("Error: Cannot divide by zero!");
        }

        System.out.println("Program continues running...");
    }
}
```

**Output:**
```
Error: Cannot divide by zero!
Program continues running...
```

Without the `try-catch`, the program would crash on the division line, and "Program continues running..." would never be printed.

### 5.2 Catching Multiple Exceptions

You can catch different types of exceptions with separate `catch` blocks:

```java
import java.util.Scanner;

public class InputProcessor {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        try {
            System.out.print("Enter a number: ");
            int num1 = Integer.parseInt(scanner.nextLine());

            System.out.print("Enter another number: ");
            int num2 = Integer.parseInt(scanner.nextLine());

            int result = num1 / num2;
            System.out.println("Result: " + result);

        } catch (NumberFormatException e) {
            System.out.println("Error: Please enter valid integers!");
        } catch (ArithmeticException e) {
            System.out.println("Error: Cannot divide by zero!");
        }
    }
}
```

In this example:
- If the user enters "abc" instead of a number, a `NumberFormatException` is caught.
- If the user enters "0" as the second number, an `ArithmeticException` is caught.

You can also catch multiple exception types in a single `catch` block using the multi-catch syntax (Java 7+):

```java
try {
    // risky code
} catch (NumberFormatException | ArithmeticException e) {
    System.out.println("Error: " + e.getMessage());
}
```

### 5.3 The Exception Object

When an exception is caught, the catch block receives the exception object (`e` in our examples). This object provides useful information:

```java
try {
    int[] arr = {1, 2, 3};
    System.out.println(arr[10]);
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Message: " + e.getMessage());
    System.out.println("Type: " + e.getClass().getName());
    e.printStackTrace(); // Prints the full stack trace (useful for debugging)
}
```

**Output:**
```
Message: Index 10 out of bounds for length 3
Type: java.lang.ArrayIndexOutOfBoundsException
java.lang.ArrayIndexOutOfBoundsException: Index 10 out of bounds for length 3
    at Example.main(Example.java:4)
```

### 5.4 The finally Block

The `finally` block executes **regardless of whether an exception was thrown or not**. It is used to ensure that cleanup code always runs -- such as closing files, database connections, or network sockets.

```java
import java.io.FileReader;
import java.io.BufferedReader;
import java.io.IOException;

public class FileReaderExample {
    public static void main(String[] args) {
        BufferedReader reader = null;

        try {
            reader = new BufferedReader(new FileReader("data.txt"));
            String line = reader.readLine();
            System.out.println("First line: " + line);

        } catch (IOException e) {
            System.out.println("Error reading file: " + e.getMessage());

        } finally {
            // This block ALWAYS executes, whether an exception occurred or not
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    System.out.println("Error closing file: " + e.getMessage());
                }
            }
            System.out.println("Cleanup complete.");
        }
    }
}
```

The `finally` block is essential for resource management. Without it, resources might remain open if an exception occurs, leading to resource leaks.

### 5.5 try-with-resources (Java 7+)

Java provides a cleaner way to manage resources that implement the `AutoCloseable` interface. The **try-with-resources** statement automatically closes resources when the block exits, whether normally or due to an exception:

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class FileReaderModern {
    public static void main(String[] args) {
        try (BufferedReader reader = new BufferedReader(new FileReader("data.txt"))) {
            String line = reader.readLine();
            System.out.println("First line: " + line);
        } catch (IOException e) {
            System.out.println("Error reading file: " + e.getMessage());
        }
        // reader is automatically closed here -- no finally block needed!
    }
}
```

This is the preferred approach in modern Java. It is shorter, cleaner, and less error-prone.

---

## 6. Throwing Exceptions

### 6.1 The throw Keyword

You can create and throw your own exceptions using the `throw` keyword. This is how you signal that something has gone wrong in your code.

```java
public class Validator {

    public void validateName(String name) {
        if (name == null) {
            throw new IllegalArgumentException("Name cannot be null");
        }
        if (name.trim().isEmpty()) {
            throw new IllegalArgumentException("Name cannot be empty");
        }
        if (name.length() > 100) {
            throw new IllegalArgumentException("Name cannot exceed 100 characters");
        }
    }
}
```

When a `throw` statement executes:
1. An exception object is created.
2. The normal flow of execution stops immediately.
3. The JVM looks for a matching `catch` block up the call stack.
4. If no matching `catch` is found, the program terminates with an error message and stack trace.

### 6.2 The throws Keyword (Declaring Exceptions)

If a method might throw a checked exception that it does not handle itself, it must declare this in its method signature using the `throws` keyword:

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;

public class FileService {

    // This method might throw IOException, so it declares it
    public String readFileContents(String filePath) throws IOException {
        return Files.readString(Path.of(filePath));
    }
}
```

The caller of this method must then either catch the exception or declare it in their own `throws` clause:

```java
public class Application {

    public static void main(String[] args) {
        FileService fileService = new FileService();

        // Option 1: Handle it here
        try {
            String contents = fileService.readFileContents("config.txt");
            System.out.println(contents);
        } catch (IOException e) {
            System.out.println("Could not read configuration file: " + e.getMessage());
        }
    }
}
```

### 6.3 Creating Custom Exceptions

For domain-specific error conditions, create your own exception classes. This makes your error handling more expressive and allows callers to catch specific types of errors.

```java
// Custom exception for validation errors
public class ValidationException extends RuntimeException {

    private final String fieldName;

    public ValidationException(String fieldName, String message) {
        super(message);
        this.fieldName = fieldName;
    }

    public String getFieldName() {
        return fieldName;
    }
}

// Custom exception for insufficient funds
public class InsufficientFundsException extends Exception {

    private final double amountRequested;
    private final double availableBalance;

    public InsufficientFundsException(double amountRequested, double availableBalance) {
        super(String.format(
            "Insufficient funds: requested %.2f but only %.2f available",
            amountRequested, availableBalance
        ));
        this.amountRequested = amountRequested;
        this.availableBalance = availableBalance;
    }

    public double getAmountRequested() {
        return amountRequested;
    }

    public double getAvailableBalance() {
        return availableBalance;
    }
}
```

**Using the custom exception:**

```java
public class BankAccount {
    private double balance;

    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }

    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount <= 0) {
            throw new ValidationException("amount", "Withdrawal amount must be positive");
        }
        if (amount > balance) {
            throw new InsufficientFundsException(amount, balance);
        }
        balance -= amount;
    }

    public double getBalance() {
        return balance;
    }
}
```

### 6.4 Checked vs. Unchecked Exceptions: When to Use Which

| Use Checked Exceptions When... | Use Unchecked Exceptions When... |
|-------------------------------|----------------------------------|
| The caller can reasonably recover | The error is a programming mistake |
| The error is expected to happen sometimes | The error should never happen in correct code |
| Example: file not found, network timeout | Example: null pointer, invalid argument |
| Extend `Exception` | Extend `RuntimeException` |

---

## 7. Best Practices for Error Handling

### 7.1 Use Exceptions, Not Error Codes

In older programming styles, functions returned special values (like `-1` or `null`) to indicate errors. This is fragile because the caller can easily forget to check the return value.

**Bad -- using error codes:**

```java
public int divide(int a, int b) {
    if (b == 0) {
        return -1;  // Error code. But what if -1 is a valid result?
    }
    return a / b;
}

// Caller might forget to check!
int result = divide(10, 0);
System.out.println("Result: " + result);  // Prints -1, but no error is apparent
```

**Good -- using exceptions:**

```java
public int divide(int a, int b) {
    if (b == 0) {
        throw new ArithmeticException("Cannot divide by zero");
    }
    return a / b;
}

// Caller is forced to deal with the possibility of failure
try {
    int result = divide(10, 0);
    System.out.println("Result: " + result);
} catch (ArithmeticException e) {
    System.out.println("Error: " + e.getMessage());
}
```

### 7.2 Catch Specific Exceptions

Catch the most specific exception type possible. Catching `Exception` (or worse, `Throwable`) hides bugs and makes debugging harder.

```java
// Bad: catches everything, including bugs you want to know about
try {
    processData();
} catch (Exception e) {
    System.out.println("Something went wrong");
}

// Good: catches only the expected exception
try {
    processData();
} catch (IOException e) {
    System.out.println("Error reading data: " + e.getMessage());
}
```

### 7.3 Do Not Swallow Exceptions

An empty `catch` block silently ignores errors. This is one of the worst practices in programming because problems are hidden instead of handled.

```java
// Terrible: the error is silently ignored
try {
    importantOperation();
} catch (Exception e) {
    // Do nothing -- pretend it never happened
}

// Better: at minimum, log the error
try {
    importantOperation();
} catch (Exception e) {
    logger.error("Failed to complete important operation", e);
}
```

### 7.4 Provide Context in Exception Messages

Exception messages should contain enough information to understand what went wrong and where.

```java
// Bad: unhelpful message
throw new IllegalArgumentException("Invalid value");

// Good: specific, actionable message
throw new IllegalArgumentException(
    "Student age must be between 0 and 150, but was: " + age
);
```

### 7.5 Separate Error Handling from Business Logic

Error handling code should not obscure the main logic of your program.

**Bad -- error handling mixed into business logic:**

```java
public void processStudentRegistration(Student student) {
    try {
        if (student == null) {
            throw new ValidationException("student", "Student cannot be null");
        }
        if (student.getName() == null || student.getName().isEmpty()) {
            throw new ValidationException("name", "Name is required");
        }
        if (student.getAge() < 16 || student.getAge() > 100) {
            throw new ValidationException("age", "Age must be between 16 and 100");
        }
        database.save(student);
        emailService.sendWelcome(student.getEmail());
        logger.info("Student registered: " + student.getName());
    } catch (ValidationException e) {
        logger.warn("Validation failed: " + e.getMessage());
    } catch (DatabaseException e) {
        logger.error("Database error during registration", e);
    } catch (EmailException e) {
        logger.warn("Could not send welcome email", e);
    }
}
```

**Good -- validation separated, cleaner flow:**

```java
public void processStudentRegistration(Student student) {
    validateStudent(student);
    saveStudent(student);
    sendWelcomeEmail(student);
}

private void validateStudent(Student student) {
    if (student == null) {
        throw new ValidationException("student", "Student cannot be null");
    }
    if (student.getName() == null || student.getName().isEmpty()) {
        throw new ValidationException("name", "Name is required");
    }
    if (student.getAge() < 16 || student.getAge() > 100) {
        throw new ValidationException("age", "Age must be between 16 and 100");
    }
}

private void saveStudent(Student student) {
    try {
        database.save(student);
        logger.info("Student registered: " + student.getName());
    } catch (DatabaseException e) {
        logger.error("Database error during registration", e);
        throw new RegistrationException("Could not save student", e);
    }
}

private void sendWelcomeEmail(Student student) {
    try {
        emailService.sendWelcome(student.getEmail());
    } catch (EmailException e) {
        logger.warn("Could not send welcome email to: " + student.getEmail(), e);
        // Non-critical: registration still succeeds
    }
}
```

---

## 8. A Complete Example: Exception Handling in a Logger

Let us put everything together with a practical example. We will build a `ConsoleLogger` class with proper validation and exception handling.

```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

/**
 * A simple console logger that prints timestamped messages.
 * Validates all inputs and throws appropriate exceptions for invalid data.
 */
public class ConsoleLogger {

    private static final DateTimeFormatter TIMESTAMP_FORMAT =
        DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");

    /**
     * Logs a message to the console with a timestamp.
     *
     * @param message the message to log; must not be null or empty
     * @throws IllegalArgumentException if the message is null or empty
     */
    public void log(String message) {
        validateMessage(message);
        String timestamp = LocalDateTime.now().format(TIMESTAMP_FORMAT);
        System.out.println("[" + timestamp + "] " + message);
    }

    /**
     * Logs an error message along with exception details.
     *
     * @param message   the error message; must not be null or empty
     * @param exception the exception that caused the error; must not be null
     * @throws IllegalArgumentException if message or exception is null/empty
     */
    public void logError(String message, Exception exception) {
        validateMessage(message);
        if (exception == null) {
            throw new IllegalArgumentException("Exception must not be null");
        }

        String timestamp = LocalDateTime.now().format(TIMESTAMP_FORMAT);
        System.out.println("[" + timestamp + "] ERROR: " + message);
        System.out.println("  Exception: " + exception.getClass().getName()
            + " - " + exception.getMessage());
    }

    private void validateMessage(String message) {
        if (message == null) {
            throw new IllegalArgumentException("Log message must not be null");
        }
        if (message.trim().isEmpty()) {
            throw new IllegalArgumentException("Log message must not be empty");
        }
    }
}
```

**Using the logger with proper error handling:**

```java
public class Application {

    public static void main(String[] args) {
        ConsoleLogger logger = new ConsoleLogger();

        // Normal usage
        logger.log("Application started");

        // Handling potential errors
        try {
            logger.log(null);  // This will throw an exception
        } catch (IllegalArgumentException e) {
            System.out.println("Caught error: " + e.getMessage());
        }

        try {
            logger.log("   ");  // This will also throw an exception
        } catch (IllegalArgumentException e) {
            System.out.println("Caught error: " + e.getMessage());
        }

        // Logging an error with exception details
        try {
            int result = 10 / 0;
        } catch (ArithmeticException e) {
            logger.logError("Calculation failed", e);
        }

        logger.log("Application finished");
    }
}
```

**Output:**
```
[2024-09-15 14:30:00] Application started
Caught error: Log message must not be null
Caught error: Log message must not be empty
[2024-09-15 14:30:00] ERROR: Calculation failed
  Exception: java.lang.ArithmeticException - / by zero
[2024-09-15 14:30:00] Application finished
```

---

## 9. Debugging

### 9.1 What Is Debugging?

Debugging is the process of finding and fixing bugs (logical errors) in your code. The term comes from the idea of removing "bugs" from the system.

Modern IDEs provide powerful debugging tools that let you:

- **Pause execution** at specific lines of code
- **Step through code** one line at a time
- **Inspect variable values** at each step
- **Modify variable values** during execution
- **Evaluate expressions** on the fly

Debugging is an essential skill that separates productive developers from those who spend hours adding `System.out.println()` statements everywhere.

### 9.2 Debug Mode

Every modern IDE (IntelliJ IDEA, Eclipse, VS Code with Java extensions) has a **debug mode**. When you run your application in debug mode, you gain the ability to control its execution and inspect its state.

To start debugging in IntelliJ IDEA, click the **Debug** button (the bug icon) instead of the **Run** button, or press `Shift + F9`.

### 9.3 Breakpoints

A **breakpoint** is a marker you set on a specific line of code. When the program reaches that line during debug mode, execution **pauses**, and you can inspect the state of the program.

To set a breakpoint:
- **IntelliJ IDEA**: Click in the gutter (the gray area to the left of the line numbers). A red dot appears.
- **VS Code**: Click in the gutter to the left of the line number. A red dot appears.
- **Eclipse**: Double-click in the gutter area.

```java
public class DebugExample {
    public static void main(String[] args) {
        int sum = 0;

        for (int i = 1; i <= 10; i++) {
            sum += i;               // <-- Set a breakpoint on this line
        }

        System.out.println("Sum: " + sum);  // <-- Or this line
    }
}
```

When the debugger hits the breakpoint, you can see:
- The current value of `i`
- The current value of `sum`
- The call stack (which methods called which)
- Local and global variables

### 9.4 Stepping Through Code

Once the debugger has paused at a breakpoint, you control execution using these commands:

| Command | Shortcut (IntelliJ) | What It Does |
|---------|---------------------|-------------|
| **Step Over** | `F8` | Execute the current line and move to the next line in the same method. If the line contains a method call, the method executes fully without stepping into it. |
| **Step Into** | `F7` | If the current line contains a method call, jump into that method and pause at its first line. |
| **Step Out** | `Shift + F8` | Execute the rest of the current method and return to the calling method. |
| **Continue (Resume)** | `F9` | Continue execution until the next breakpoint is hit. If no more breakpoints exist, the program runs to completion. |

#### When to Use Each Command

- **Step Over**: Use when you trust the method being called and only want to see its result. For example, you trust that `Math.sqrt()` works correctly and just want to see what value it returns.
- **Step Into**: Use when you suspect the bug is inside the method being called. For example, your `calculateTotal()` method returns the wrong value, so you step into it to watch it execute line by line.
- **Step Out**: Use when you have stepped into a method, determined it is not the source of the bug, and want to return to the caller quickly.
- **Continue**: Use when you want to skip ahead to the next breakpoint, perhaps in a different part of the code.

### 9.5 A Debugging Walkthrough

Consider this buggy code:

```java
public class GradeCalculator {

    public static String getLetterGrade(double percentage) {
        if (percentage >= 90) {
            return "A";
        }
        if (percentage >= 80) {
            return "B";
        }
        if (percentage >= 70) {
            return "C";
        }
        if (percentage >= 60) {
            return "D";
        }
        return "F";
    }

    public static double calculateAverage(int[] scores) {
        int sum = 0;
        for (int i = 0; i <= scores.length; i++) {  // Bug: should be < not <=
            sum += scores[i];
        }
        return sum / scores.length;  // Bug: integer division
    }

    public static void main(String[] args) {
        int[] scores = {85, 92, 78, 95, 88};
        double average = calculateAverage(scores);
        String grade = getLetterGrade(average);
        System.out.println("Average: " + average + ", Grade: " + grade);
    }
}
```

**Debugging this step by step:**

1. **Set a breakpoint** on the line `double average = calculateAverage(scores);` in `main`.
2. **Run in debug mode.** The program pauses at the breakpoint.
3. **Step Into** the `calculateAverage` method.
4. **Step Over** through the loop, watching `sum` and `i` at each iteration.
5. When `i` reaches `5` (which equals `scores.length`), you will notice that `scores[5]` will throw an `ArrayIndexOutOfBoundsException`. **Bug found!** The condition should be `i < scores.length`, not `i <= scores.length`.
6. After fixing that bug, debug again. Watch the return value: `sum / scores.length` performs **integer division** because both operands are `int`. For scores `{85, 92, 78, 95, 88}`, the sum is 438 and 438 / 5 = 87 (truncated from 87.6). **Bug found!** The fix is `(double) sum / scores.length`.

**Fixed code:**

```java
public static double calculateAverage(int[] scores) {
    int sum = 0;
    for (int i = 0; i < scores.length; i++) {  // Fixed: < instead of <=
        sum += scores[i];
    }
    return (double) sum / scores.length;  // Fixed: cast to double for decimal result
}
```

### 9.6 Conditional Breakpoints

Sometimes you do not want the debugger to pause at every iteration of a loop -- you want it to pause only when a certain condition is true. **Conditional breakpoints** let you specify a boolean expression; the breakpoint only triggers when the expression evaluates to `true`.

**How to set a conditional breakpoint:**
1. Set a regular breakpoint.
2. Right-click the breakpoint (in IntelliJ IDEA) or click the gear icon.
3. Enter a condition, for example: `i == 50` or `name.equals("John")`.

```java
public void processOrders(List<Order> orders) {
    for (Order order : orders) {
        double total = calculateTotal(order);  // Breakpoint with condition: total < 0
        applyDiscount(order);
        saveOrder(order);
    }
}
```

With a conditional breakpoint `total < 0` on the `calculateTotal` line, the debugger pauses only when the total is negative -- which is exactly the case you are trying to investigate.

**Use cases for conditional breakpoints:**
- Pausing at a specific loop iteration (e.g., `i == 99` to debug the 100th iteration)
- Pausing when a variable has a specific value (e.g., `userId == 42`)
- Pausing only for error conditions (e.g., `result == null`)

### 9.7 Watches and Evaluate Expression

Most debuggers let you:

- **Add watches**: Specify variables or expressions that you want to monitor continuously. The debugger shows their values at every pause.
- **Evaluate expressions**: Type any Java expression and see its result in the current context. For example, you could evaluate `scores.length`, `sum > 100`, or even `Arrays.toString(scores)` while paused.

These features are invaluable for understanding complex state during debugging.

### 9.8 Debugging Tips

1. **Start with a hypothesis.** Before launching the debugger, think about what you expect the code to do and where it might go wrong. Debugging is most effective when you have a theory to test.

2. **Use binary search for bugs.** If a long process produces the wrong result, set a breakpoint halfway through. If the state is correct at that point, the bug is in the second half. This lets you zero in on the problem quickly.

3. **Check your assumptions.** When debugging, verify things you "know" to be true. Often, the bug is in something you assumed was correct.

4. **Avoid excessive print statements.** While `System.out.println()` can be useful for quick checks, a debugger is far more powerful for complex problems. Learn to use it fluently.

5. **Debug the smallest possible case.** If a bug occurs with 1000 records, try to reproduce it with 5. Smaller inputs make it easier to step through the code.

---

## 10. Putting It All Together

Here is a complete, real-world-style example that demonstrates error handling and the kind of code you would debug in practice:

```java
import java.util.ArrayList;
import java.util.List;

public class StudentGradeManager {

    private final List<Student> students = new ArrayList<>();

    /**
     * Adds a student to the manager.
     *
     * @param student the student to add; must not be null
     * @throws IllegalArgumentException if the student is null
     */
    public void addStudent(Student student) {
        if (student == null) {
            throw new IllegalArgumentException("Student cannot be null");
        }
        students.add(student);
    }

    /**
     * Calculates the class average grade.
     *
     * @return the average grade as a percentage
     * @throws IllegalStateException if no students have been added
     */
    public double calculateClassAverage() {
        if (students.isEmpty()) {
            throw new IllegalStateException("Cannot calculate average: no students");
        }

        double totalGrade = 0;
        for (Student student : students) {
            totalGrade += student.getGrade();
        }
        return totalGrade / students.size();
    }

    /**
     * Finds a student by name.
     *
     * @param name the name to search for; must not be null or empty
     * @return the student with the given name
     * @throws IllegalArgumentException if name is null or empty
     * @throws StudentNotFoundException if no student with the given name exists
     */
    public Student findStudent(String name) {
        if (name == null || name.trim().isEmpty()) {
            throw new IllegalArgumentException("Student name must not be null or empty");
        }

        for (Student student : students) {
            if (student.getName().equalsIgnoreCase(name.trim())) {
                return student;
            }
        }

        throw new StudentNotFoundException("No student found with name: " + name);
    }

    public static void main(String[] args) {
        StudentGradeManager manager = new StudentGradeManager();

        // Add students
        manager.addStudent(new Student("Alice", 92.5));
        manager.addStudent(new Student("Bob", 85.0));
        manager.addStudent(new Student("Charlie", 78.3));

        // Calculate and display class average
        try {
            double average = manager.calculateClassAverage();
            System.out.printf("Class average: %.1f%%%n", average);
        } catch (IllegalStateException e) {
            System.out.println("Error: " + e.getMessage());
        }

        // Find a student
        try {
            Student student = manager.findStudent("Bob");
            System.out.println("Found: " + student.getName()
                + " with grade " + student.getGrade());
        } catch (StudentNotFoundException e) {
            System.out.println("Error: " + e.getMessage());
        }

        // Try to find a non-existent student
        try {
            Student student = manager.findStudent("David");
            System.out.println("Found: " + student.getName());
        } catch (StudentNotFoundException e) {
            System.out.println("Error: " + e.getMessage());
        }

        // Try invalid input
        try {
            manager.addStudent(null);
        } catch (IllegalArgumentException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}

// Supporting classes:

class Student {
    private final String name;
    private final double grade;

    public Student(String name, double grade) {
        this.name = name;
        this.grade = grade;
    }

    public String getName() { return name; }
    public double getGrade() { return grade; }
}

class StudentNotFoundException extends RuntimeException {
    public StudentNotFoundException(String message) {
        super(message);
    }
}
```

**Output:**
```
Class average: 85.3%
Found: Bob with grade 85.0
Error: No student found with name: David
Error: Student cannot be null
```

---

## Summary and Key Takeaways

1. **Syntax errors** are the easiest to find -- the compiler catches them. Aim to keep all your errors in this category.

2. **Logical errors (bugs)** are the hardest to find because the code runs without crashing but produces wrong results. Prevention through testing and careful logic review is essential.

3. **Runtime errors (exceptions)** occur during program execution and can crash the application. Java's exception mechanism (`try`/`catch`/`finally`) lets you handle them gracefully.

4. **Use specific exception types.** Catch the most specific exception you can. Never silently swallow exceptions.

5. **Provide meaningful error messages.** Include enough context to understand what went wrong.

6. **Separate error handling from business logic.** Keep your main code path clean and readable.

7. **Create custom exceptions** when standard exception types are not expressive enough for your domain.

8. **Use try-with-resources** for automatic resource management.

9. **Learn your debugger.** Master breakpoints, step over/into/out, conditional breakpoints, and watches. These tools will save you countless hours.

10. **Debug with a hypothesis.** Think first, then verify with the debugger. Do not just blindly step through code hoping to stumble on the problem.

---

## Homework / Assignments

### Assignment 1: Add Exception Handling to the Logger (Required)

Take the `ConsoleLogger` class from the Chapter 1 homework and add exception handling:

1. Add validation logic to the logger so that:
   - The log message must not be `null`
   - The log message must not be empty or contain only whitespace
   - Violating these rules should throw an `IllegalArgumentException` with a descriptive message

2. Write a test class (`LoggerTest`) with a `main` method that:
   - Successfully logs a few messages
   - Attempts to log a `null` message and catches the exception
   - Attempts to log an empty string and catches the exception
   - Prints a confirmation after each catch block that the exception was handled correctly

### Assignment 2: Debug the Logger (Required)

Using your IDE's debugger:

1. Set breakpoints in your `ConsoleLogger`'s `log` method.
2. Run the application in debug mode.
3. Step through the execution and verify that:
   - All variables have the expected values
   - The validation logic correctly identifies invalid inputs
   - The timestamp is generated correctly
4. Take note of how the debugger shows the call stack and variable values.

### Assignment 3: Custom Exception Practice (Optional)

Create a `TemperatureConverter` class that converts between Celsius and Fahrenheit:

1. Create a custom exception class called `TemperatureOutOfRangeException` that stores the invalid temperature and the valid range.
2. The converter should throw `TemperatureOutOfRangeException` if the input temperature is below absolute zero (-273.15 C or -459.67 F).
3. The converter should throw `IllegalArgumentException` if the scale parameter is not "C" or "F".
4. Write a `main` method that tests all three scenarios: successful conversion, out-of-range temperature, and invalid scale.

```java
// Starter structure:
public class TemperatureConverter {

    public double convert(double temperature, String fromScale, String toScale) {
        // Your implementation here
    }
}
```

### Assignment 4: Debugging Exercise (Optional)

The following code has multiple bugs. Use the debugger to find and fix all of them. Do not just read the code -- actually use breakpoints and step through execution.

```java
public class BuggyStatistics {

    public static double calculateMedian(int[] numbers) {
        // Bug(s) exist in this method
        int[] sorted = numbers.clone();

        for (int i = 0; i < sorted.length; i++) {
            for (int j = i; j < sorted.length; j++) {
                if (sorted[i] > sorted[j]) {
                    int temp = sorted[i];
                    sorted[i] = sorted[j];
                    sorted[j] = temp;
                }
            }
        }

        int mid = sorted.length / 2;
        if (sorted.length % 2 == 0) {
            return sorted[mid] + sorted[mid - 1] / 2;
        } else {
            return sorted[mid];
        }
    }

    public static void main(String[] args) {
        int[] evenSet = {7, 3, 1, 4};      // Expected median: 3.5
        int[] oddSet = {5, 2, 9, 1, 7};    // Expected median: 5

        System.out.println("Even set median: " + calculateMedian(evenSet));
        System.out.println("Odd set median: " + calculateMedian(oddSet));
    }
}
```

Identify the bugs, explain what each one causes, and provide the corrected code.
