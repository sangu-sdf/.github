# Chapter 6: Logging

## Introduction

Every software application, from a small command-line utility to a massive distributed system, needs a way to communicate what is happening during its execution. During development, you might sprinkle `System.out.println()` statements throughout your code to track variable values and execution flow. But what happens when your application goes to production? You cannot sit in front of a terminal watching console output 24 hours a day. You need a systematic, configurable, and reliable way to record what your application is doing -- and that is exactly what **logging** provides.

Logging is one of the most important cross-cutting concerns in software development. It is the practice of recording messages about your application's behavior during runtime, sending those messages to one or more destinations (such as the console, a file, a database, or even an email inbox), and formatting them in a way that makes them useful for debugging, monitoring, and auditing.

In this chapter, you will learn what logging is, why it matters, how logging levels help you control the verbosity of your output, how pattern layouts let you customize the format of log messages, and how appenders determine where those messages go. All examples use Java with the popular **Log4j2** and **SLF4J** logging frameworks.

---

## 6.1 What Is Logging?

### 6.1.1 Definition

In the physical world, logging refers to the process of cutting down trees and transporting them. In the software world, the metaphor is apt: we are "cutting" through the flow of our program and "transporting" information about that flow to a destination where it can be examined.

**Software logging** is the process of recording messages about an application's runtime behavior and sending those messages to a designated destination. The messages can include information about:

- What methods were called and with what arguments
- What decisions the code made at branch points
- What errors or exceptions occurred
- How long certain operations took
- What data was received from external systems

### 6.1.2 Why Not Just Use `System.out.println()`?

If you have written any Java program, you have almost certainly used `System.out.println()` to debug your code. While this works for simple cases, it has severe limitations:

| Feature | `System.out.println()` | Logging Framework |
|---|---|---|
| Can be turned off without code changes | No | Yes |
| Supports severity levels | No | Yes |
| Can write to files, databases, email | No (console only) | Yes |
| Includes timestamps automatically | No | Yes |
| Includes class/method information | No | Yes |
| Thread-safe by design | Partially | Yes |
| Configurable output format | No | Yes |
| Performance optimized | No | Yes |

A logging framework gives you all these capabilities without modifying your source code -- you configure logging behavior externally through configuration files.

### 6.1.3 Popular Java Logging Frameworks

Java has a rich ecosystem of logging frameworks. Here are the most commonly used ones:

- **java.util.logging (JUL):** Built into the JDK. Simple but limited.
- **Log4j2:** The successor to the original Log4j. Feature-rich, high-performance, and widely used.
- **SLF4J (Simple Logging Facade for Java):** Not a logging implementation itself, but a facade (abstraction layer) that lets you plug in any logging framework underneath.
- **Logback:** The default implementation behind SLF4J, created by the same author as Log4j.

In practice, most modern Java projects use **SLF4J as the facade** with either **Logback** or **Log4j2** as the underlying implementation. This chapter focuses on **Log4j2** for configuration examples and **SLF4J** for the API.

---

## 6.2 Setting Up Logging in a Java Project

### 6.2.1 Adding Dependencies with Maven

To use Log4j2 with SLF4J in a Maven project, add the following dependencies to your `pom.xml`:

```xml
<dependencies>
    <!-- SLF4J API -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>2.0.9</version>
    </dependency>

    <!-- Log4j2 Implementation -->
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-core</artifactId>
        <version>2.21.1</version>
    </dependency>

    <!-- Bridge between SLF4J and Log4j2 -->
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-slf4j2-impl</artifactId>
        <version>2.21.1</version>
    </dependency>
</dependencies>
```

### 6.2.2 Adding Dependencies with Gradle

For Gradle-based projects, add the following to your `build.gradle`:

```groovy
dependencies {
    implementation 'org.slf4j:slf4j-api:2.0.9'
    implementation 'org.apache.logging.log4j:log4j-core:2.21.1'
    implementation 'org.apache.logging.log4j:log4j-slf4j2-impl:2.21.1'
}
```

### 6.2.3 Your First Logging Example

Here is a simple Java class that uses SLF4J for logging:

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloLogging {

    // Create a logger for this class
    private static final Logger logger = LoggerFactory.getLogger(HelloLogging.class);

    public static void main(String[] args) {
        logger.info("Application has started.");
        logger.debug("This is a debug message.");
        logger.warn("This is a warning.");
        logger.error("Something went wrong!");
        logger.info("Application is shutting down.");
    }
}
```

When you run this program with a properly configured logging framework, you will see output similar to:

```
2025-01-15 10:23:45.123 INFO  [main] HelloLogging - Application has started.
2025-01-15 10:23:45.125 DEBUG [main] HelloLogging - This is a debug message.
2025-01-15 10:23:45.126 WARN  [main] HelloLogging - This is a warning.
2025-01-15 10:23:45.127 ERROR [main] HelloLogging - Something went wrong!
2025-01-15 10:23:45.128 INFO  [main] HelloLogging - Application is shutting down.
```

Notice how each line automatically includes the timestamp, the logging level, the thread name, the class name, and the message. You did not have to manually construct any of this -- the logging framework handles it all.

---

## 6.3 Logging Levels

### 6.3.1 What Are Logging Levels?

Every log message has an associated **severity level** that indicates how important or critical the message is. Logging levels allow you to filter messages based on their importance. During development, you might want to see every single message (including detailed trace information). In production, you typically only want to see warnings and errors.

The standard logging levels, from least severe to most severe, are:

```
TRACE < DEBUG < INFO < WARN < ERROR < FATAL
```

When you set a logging level in your configuration, you will see all messages at that level **and above**. For example, if you set the level to `INFO`, you will see `INFO`, `WARN`, `ERROR`, and `FATAL` messages, but `TRACE` and `DEBUG` messages will be suppressed.

### 6.3.2 Detailed Explanation of Each Level

#### TRACE

The most granular level of logging. Use `TRACE` to record extremely detailed information about the internal flow of your application. This level is typically only enabled when diagnosing a specific, hard-to-reproduce problem.

```java
public double calculateDiscount(double price, double discountPercent) {
    logger.trace("Entering calculateDiscount(price={}, discountPercent={})", price, discountPercent);
    double discount = price * (discountPercent / 100.0);
    double finalPrice = price - discount;
    logger.trace("Calculated discount={}, finalPrice={}", discount, finalPrice);
    return finalPrice;
}
```

**When to use:** Method entry/exit tracing, loop iteration details, variable state at each step.

#### DEBUG

Used for information that is useful during development and debugging but would be too verbose for production. `DEBUG` messages help developers understand the flow and state of the application.

```java
public User findUserById(int userId) {
    logger.debug("Looking up user with ID: {}", userId);
    User user = userRepository.findById(userId);
    if (user != null) {
        logger.debug("Found user: {}", user.getUsername());
    } else {
        logger.debug("No user found with ID: {}", userId);
    }
    return user;
}
```

**When to use:** Method arguments and return values, decision points in business logic, query parameters, configuration values loaded at startup.

#### INFO

The default level for production logging. `INFO` messages record significant events in the application's lifecycle -- things that operators and administrators would want to know about during normal operation.

```java
public void startServer(int port) {
    logger.info("Starting HTTP server on port {}", port);
    // ... server startup code ...
    logger.info("HTTP server started successfully. Ready to accept connections.");
}

public void processOrder(Order order) {
    logger.info("Processing order #{} for customer {}", order.getId(), order.getCustomerName());
    // ... processing logic ...
    logger.info("Order #{} processed successfully. Total: ${}", order.getId(), order.getTotal());
}
```

**When to use:** Application startup/shutdown, configuration summaries, significant business events (order placed, user registered, payment processed), scheduled task execution.

#### WARN (WARNING)

Indicates a potential problem or an unexpected situation that the application can recover from, but that someone should investigate. The application continues to work, but something is not quite right.

```java
public void connectToDatabase(String url, int maxRetries) {
    int attempt = 0;
    while (attempt < maxRetries) {
        try {
            connection = DriverManager.getConnection(url);
            logger.info("Database connection established.");
            return;
        } catch (SQLException e) {
            attempt++;
            logger.warn("Database connection attempt {} of {} failed. Retrying...",
                        attempt, maxRetries);
        }
    }
    logger.error("All {} database connection attempts failed.", maxRetries);
}
```

**When to use:** Deprecated API usage, retrying an operation, approaching resource limits (disk space low, connection pool nearly full), fallback to default configuration.

#### ERROR

Indicates a serious problem that has caused an operation to fail. The application may continue running, but the specific operation or request could not be completed. Error messages should always include enough context to diagnose the problem.

```java
public void saveUserProfile(UserProfile profile) {
    try {
        database.save(profile);
        logger.info("User profile saved for user: {}", profile.getUserId());
    } catch (DatabaseException e) {
        logger.error("Failed to save user profile for user: {}. Database error: {}",
                     profile.getUserId(), e.getMessage(), e);
    }
}
```

Notice the third argument `e` in the `logger.error()` call. When you pass an exception as the last argument, the logging framework will automatically print the full stack trace, which is invaluable for debugging.

**When to use:** Unhandled exceptions in a specific operation, failed external service calls, data integrity violations, file I/O failures.

#### FATAL

The most severe level. Indicates a catastrophic failure that will likely cause the application to terminate. `FATAL` is used sparingly -- only when something so severe has happened that the application cannot continue.

```java
public void initialize() {
    try {
        loadConfiguration();
        connectToDatabase();
        startMessageQueue();
    } catch (Exception e) {
        logger.fatal("Application initialization failed. Cannot continue.", e);
        System.exit(1);
    }
}
```

**When to use:** Application cannot start due to missing critical resources, unrecoverable system errors, out-of-memory situations.

> **Note:** SLF4J does not have a `FATAL` level. If you use SLF4J, you would use `logger.error()` for fatal-level situations. Log4j2's native API does support `FATAL`.

### 6.3.3 Logging Level Hierarchy in Practice

Here is a visual summary of what you see at each configured level:

| Configured Level | TRACE | DEBUG | INFO | WARN | ERROR | FATAL |
|---|---|---|---|---|---|---|
| TRACE | Visible | Visible | Visible | Visible | Visible | Visible |
| DEBUG | Hidden | Visible | Visible | Visible | Visible | Visible |
| INFO | Hidden | Hidden | Visible | Visible | Visible | Visible |
| WARN | Hidden | Hidden | Hidden | Visible | Visible | Visible |
| ERROR | Hidden | Hidden | Hidden | Hidden | Visible | Visible |
| FATAL | Hidden | Hidden | Hidden | Hidden | Hidden | Visible |

### 6.3.4 Best Practices for Choosing Levels

1. **Be consistent.** Establish team conventions for what level to use in different situations.
2. **Do not log sensitive data** (passwords, API keys, personal information) at any level.
3. **Use parameterized messages** instead of string concatenation for performance:

```java
// BAD - string concatenation happens even if DEBUG is disabled
logger.debug("User " + username + " logged in from " + ipAddress);

// GOOD - parameters are only resolved if DEBUG is enabled
logger.debug("User {} logged in from {}", username, ipAddress);
```

---

## 6.4 Pattern Layouts

### 6.4.1 What Are Pattern Layouts?

A **pattern layout** defines the format of each log message. Instead of getting a plain text message, you can configure the logging framework to include timestamps, thread names, class names, line numbers, and more -- all in a format that suits your needs.

In Log4j2, the pattern layout is specified using a special syntax with conversion characters, each preceded by a `%` sign.

### 6.4.2 Common Pattern Conversion Characters

| Pattern | Description | Example Output |
|---|---|---|
| `%d` or `%date` | Date and time of the log event | `2025-01-15 10:23:45,123` |
| `%p` or `%level` | Logging level | `INFO` |
| `%c` or `%logger` | Logger name (usually the class name) | `com.example.MyClass` |
| `%m` or `%message` | The log message | `User logged in` |
| `%n` | Platform-specific line separator | (newline) |
| `%t` or `%thread` | Thread name | `main` |
| `%L` or `%line` | Line number in the source file | `42` |
| `%M` or `%method` | Method name | `processOrder` |
| `%C` or `%class` | Fully qualified class name | `com.example.OrderService` |
| `%X{key}` | MDC (Mapped Diagnostic Context) value | (custom value) |
| `%%` | A literal percent sign | `%` |

### 6.4.3 Pattern Layout Examples

**Simple pattern:**
```xml
<PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n"/>
```
Output:
```
2025-01-15 10:23:45 INFO  com.example.MyClass - Application started
```

**Detailed pattern with thread and line number:**
```xml
<PatternLayout pattern="%d{ISO8601} [%t] %-5level %logger{1}:%L - %msg%n"/>
```
Output:
```
2025-01-15T10:23:45,123 [main] INFO  MyClass:42 - Application started
```

**Colorized pattern (for console output):**
```xml
<PatternLayout pattern="%style{%d{DEFAULT}}{yellow} %highlight{%-5level}{FATAL=bg_red, ERROR=red, WARN=yellow, INFO=green} %message%n"/>
```

This configuration uses Log4j2's built-in color support to make the timestamp yellow, fatal messages have a red background, error messages are red, warnings are yellow, and info messages are green. Colorized output makes it much easier to spot problems when reading logs in a terminal.

### 6.4.4 Date Format Patterns

The `%d` conversion character supports various date formats:

```xml
%d{DEFAULT}          <!-- 2025-01-15 10:23:45,123 -->
%d{ISO8601}          <!-- 2025-01-15T10:23:45,123 -->
%d{yyyy-MM-dd}       <!-- 2025-01-15 -->
%d{HH:mm:ss.SSS}    <!-- 10:23:45.123 -->
%d{dd/MMM/yyyy}      <!-- 15/Jan/2025 -->
```

### 6.4.5 Alignment and Padding

The `-5` in `%-5level` means "left-align and pad to 5 characters." This ensures that all level names are the same width, making log output easier to read:

```
INFO  com.example.MyClass - Message one
ERROR com.example.MyClass - Message two
WARN  com.example.MyClass - Message three
```

Without padding (`%level`), the output would be misaligned:

```
INFO com.example.MyClass - Message one
ERROR com.example.MyClass - Message two
WARN com.example.MyClass - Message three
```

---

## 6.5 Appenders

### 6.5.1 What Are Appenders?

An **appender** is the component responsible for delivering log messages to their destination. The name comes from the idea that messages are "appended" to a log. Different appenders send messages to different destinations, and you can use multiple appenders simultaneously -- for example, sending `INFO` and above to a file while also sending `ERROR` and above via email.

### 6.5.2 Console Appender

The console appender writes log messages to the standard output (`System.out`) or standard error (`System.err`). This is the most common appender during development.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Appenders>
        <Console name="ConsoleAppender" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Root level="debug">
            <AppenderRef ref="ConsoleAppender"/>
        </Root>
    </Loggers>
</Configuration>
```

### 6.5.3 File Appender

The file appender writes log messages to a file on disk. This is essential for production systems where you need to review logs after the fact.

```xml
<Appenders>
    <File name="FileAppender" fileName="logs/application.log">
        <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </File>
</Appenders>
```

#### Rolling File Appender

In production, a single log file can grow to enormous sizes. A **rolling file appender** automatically creates new log files based on size or time criteria, and can delete old files to conserve disk space.

```xml
<Appenders>
    <RollingFile name="RollingFileAppender"
                 fileName="logs/app.log"
                 filePattern="logs/app-%d{yyyy-MM-dd}-%i.log.gz">
        <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        <Policies>
            <!-- Roll over when the file reaches 10 MB -->
            <SizeBasedTriggeringPolicy size="10 MB"/>
            <!-- Roll over daily -->
            <TimeBasedTriggeringPolicy interval="1" modulate="true"/>
        </Policies>
        <!-- Keep at most 30 files -->
        <DefaultRolloverStrategy max="30"/>
    </RollingFile>
</Appenders>
```

This configuration will:
- Create a new log file when the current one reaches 10 MB or at the start of each day
- Compress old log files using gzip (`.gz` extension)
- Keep at most 30 archived log files

### 6.5.4 Database Appender (JDBC)

For applications that need to store logs in a database for querying and analysis, Log4j2 provides a JDBC appender:

```xml
<Appenders>
    <JDBC name="DatabaseAppender" tableName="application_logs">
        <ConnectionFactory class="com.example.LogConnectionFactory" method="getConnection"/>
        <Column name="event_date" isEventTimestamp="true"/>
        <Column name="level" pattern="%level"/>
        <Column name="logger" pattern="%logger"/>
        <Column name="message" pattern="%message"/>
        <Column name="exception" pattern="%ex{full}"/>
    </JDBC>
</Appenders>
```

You would also need to create the corresponding database table:

```sql
CREATE TABLE application_logs (
    id         BIGINT AUTO_INCREMENT PRIMARY KEY,
    event_date TIMESTAMP NOT NULL,
    level      VARCHAR(10) NOT NULL,
    logger     VARCHAR(255) NOT NULL,
    message    TEXT,
    exception  TEXT
);
```

### 6.5.5 SMTP (Email) Appender

For critical errors that require immediate attention, you can configure an email appender that sends log messages via email:

```xml
<Appenders>
    <SMTP name="EmailAppender"
          subject="Application Error Alert"
          to="dev-team@example.com"
          from="app-alerts@example.com"
          smtpHost="smtp.example.com"
          smtpPort="587"
          smtpUsername="alerts@example.com"
          smtpPassword="secret"
          bufferSize="50">
        <ThresholdFilter level="ERROR" onMatch="ACCEPT" onMismatch="DENY"/>
        <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} %-5level %logger - %msg%n"/>
    </SMTP>
</Appenders>
```

This appender buffers messages and sends an email when an `ERROR` or `FATAL` message is logged, including the previous 50 messages as context.

### 6.5.6 Using Multiple Appenders Together

One of the most powerful features of logging frameworks is the ability to use multiple appenders simultaneously. Here is a complete `log4j2.xml` configuration that writes to both the console and a rolling file:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Appenders>
        <!-- Console: show everything at DEBUG and above -->
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout
                pattern="%style{%d{HH:mm:ss.SSS}}{dim} %highlight{%-5level}{FATAL=bg_red, ERROR=red, WARN=yellow, INFO=green, DEBUG=cyan} [%t] %logger{1} - %msg%n"/>
        </Console>

        <!-- File: store everything at INFO and above -->
        <RollingFile name="File"
                     fileName="logs/app.log"
                     filePattern="logs/app-%d{yyyy-MM-dd}.log.gz">
            <PatternLayout
                pattern="%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%t] %logger - %msg%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="1"/>
            </Policies>
            <DefaultRolloverStrategy max="90"/>
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- Application-specific logger -->
        <Logger name="com.example" level="debug" additivity="false">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="File" level="info"/>
        </Logger>

        <!-- Root logger as fallback -->
        <Root level="warn">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="File"/>
        </Root>
    </Loggers>
</Configuration>
```

### 6.5.7 Custom Appenders

If none of the built-in appenders meet your needs, you can create a custom appender. For example, you might want to send log messages to a Slack channel or a custom monitoring service. Log4j2 provides a plugin mechanism for creating custom appenders by extending `AbstractAppender`.

---

## 6.6 When and What to Log

Knowing how to log is only half the battle. Knowing **when** and **what** to log is equally important. Poor logging practices can result in either too little information (making debugging impossible) or too much noise (making it hard to find relevant messages).

### 6.6.1 What You Should Log

- **Application lifecycle events:** startup, shutdown, configuration loaded
- **Authentication events:** login attempts (successful and failed), logouts, password changes
- **Business operations:** order placed, payment processed, report generated
- **External system interactions:** API calls made, responses received, connection established/lost
- **Errors and exceptions:** always log the full stack trace with `logger.error("message", exception)`
- **Performance data:** operation durations, queue sizes, cache hit/miss ratios

### 6.6.2 What You Should NOT Log

- **Passwords and credentials:** never log plaintext or hashed passwords
- **Credit card numbers or financial data:** subject to PCI-DSS compliance
- **Personal identification information (PII):** social security numbers, medical records
- **API keys and tokens:** these can be used to impersonate your application
- **Sensitive business data:** unless required and properly secured

### 6.6.3 A Practical Logging Example

Here is a realistic example of a service class with appropriate logging:

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class OrderService {

    private static final Logger logger = LoggerFactory.getLogger(OrderService.class);

    private final PaymentGateway paymentGateway;
    private final OrderRepository orderRepository;
    private final EmailService emailService;

    public OrderService(PaymentGateway paymentGateway,
                        OrderRepository orderRepository,
                        EmailService emailService) {
        this.paymentGateway = paymentGateway;
        this.orderRepository = orderRepository;
        this.emailService = emailService;
    }

    public OrderResult placeOrder(Order order) {
        logger.info("Placing order for customer: {}, items: {}",
                     order.getCustomerId(), order.getItemCount());
        logger.debug("Order details: {}", order);

        // Validate the order
        if (!order.isValid()) {
            logger.warn("Invalid order received from customer: {}. Reason: {}",
                        order.getCustomerId(), order.getValidationErrors());
            return OrderResult.invalid(order.getValidationErrors());
        }

        // Process payment
        try {
            logger.debug("Initiating payment of ${} for order #{}",
                         order.getTotal(), order.getId());
            PaymentResult payment = paymentGateway.charge(order.getTotal(),
                                                          order.getPaymentMethod());

            if (!payment.isSuccessful()) {
                logger.warn("Payment declined for order #{}. Reason: {}",
                            order.getId(), payment.getDeclineReason());
                return OrderResult.paymentFailed(payment.getDeclineReason());
            }

            logger.info("Payment successful for order #{}. Transaction ID: {}",
                        order.getId(), payment.getTransactionId());
        } catch (PaymentException e) {
            logger.error("Payment processing error for order #{}", order.getId(), e);
            return OrderResult.error("Payment processing failed");
        }

        // Save the order
        try {
            orderRepository.save(order);
            logger.info("Order #{} saved successfully.", order.getId());
        } catch (DatabaseException e) {
            logger.error("Failed to save order #{} to database", order.getId(), e);
            return OrderResult.error("Order could not be saved");
        }

        // Send confirmation email
        try {
            emailService.sendOrderConfirmation(order);
            logger.debug("Confirmation email sent for order #{}", order.getId());
        } catch (EmailException e) {
            // Email failure should not fail the order
            logger.warn("Failed to send confirmation email for order #{}. "
                       + "Email will be retried later.", order.getId(), e);
        }

        logger.info("Order #{} placed successfully for customer {}",
                    order.getId(), order.getCustomerId());
        return OrderResult.success(order.getId());
    }
}
```

Notice the deliberate choices in this example:

- **INFO** is used for significant business events (order placed, payment successful)
- **DEBUG** is used for detailed information useful during development (order details, email sent)
- **WARN** is used for recoverable problems (payment declined, email failed but order continues)
- **ERROR** is used for actual failures with full exception stack traces
- Sensitive data (credit card numbers) is never logged
- Messages include contextual information (order ID, customer ID) for traceability

---

## 6.7 Mapped Diagnostic Context (MDC)

When your application handles multiple requests concurrently (as web applications do), it can be difficult to trace which log messages belong to which request. **MDC (Mapped Diagnostic Context)** lets you attach contextual key-value pairs to the current thread, and those values automatically appear in every log message from that thread.

```java
import org.slf4j.MDC;

public class RequestFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
        try {
            // Set context values for this request
            MDC.put("requestId", UUID.randomUUID().toString());
            MDC.put("userId", getCurrentUserId(request));
            MDC.put("clientIp", request.getRemoteAddr());

            chain.doFilter(request, response);
        } finally {
            // Always clear MDC to prevent memory leaks
            MDC.clear();
        }
    }
}
```

Then in your pattern layout, reference MDC values with `%X{key}`:

```xml
<PatternLayout pattern="%d %-5level [%X{requestId}] [user:%X{userId}] %logger - %msg%n"/>
```

Output:
```
2025-01-15 10:23:45 INFO  [a1b2c3d4] [user:john.doe] OrderService - Order #1234 placed successfully
```

---

## 6.8 Logging Best Practices

1. **Use a logging facade (SLF4J):** This decouples your code from a specific logging implementation and makes it easy to switch frameworks.

2. **Use parameterized messages:** Avoid string concatenation in log statements. Use `{}` placeholders instead.

3. **Log at the right level:** Be intentional about your choice of level. Do not log everything at `INFO`.

4. **Include context:** Every log message should include enough information to understand what happened without reading the source code.

5. **Log exceptions properly:** Always pass the exception object as the last argument to the logging method so the stack trace is captured.

6. **Do not log and throw:** If you catch an exception, either log it and handle it, or re-throw it. Do not do both, as it leads to duplicate log entries.

```java
// BAD: log and throw
try {
    doSomething();
} catch (Exception e) {
    logger.error("Failed", e);
    throw e; // This will get logged again higher up
}

// GOOD: either log and handle, or just re-throw
try {
    doSomething();
} catch (Exception e) {
    logger.error("Failed to do something, using default", e);
    return defaultValue; // Handled here
}
```

7. **Configure logging externally:** Never hard-code logging levels in your source code. Use configuration files (`log4j2.xml`, `logback.xml`) so you can change logging behavior without recompiling.

8. **Use rolling file appenders in production:** Prevent unbounded disk usage by configuring size and time-based log rotation.

---

## 6.9 Summary and Key Takeaways

- **Logging** is the systematic recording of an application's runtime behavior to designated destinations.
- **Logging levels** (TRACE, DEBUG, INFO, WARN, ERROR, FATAL) control the severity and verbosity of log output. Setting a level shows all messages at that level and above.
- **Pattern layouts** define the format of log messages, allowing you to include timestamps, class names, thread names, line numbers, and more.
- **Appenders** determine where log messages are sent: console, file, database, email, or custom destinations.
- Use **SLF4J** as a facade for portability, and **Log4j2** or **Logback** as the implementation.
- Always use **parameterized messages** for performance and readability.
- **Never log sensitive data** such as passwords, credit card numbers, or API keys.
- Use **MDC** to add request-specific context to log messages in multi-threaded applications.
- Configure logging **externally** via configuration files, not in source code.

---

## 6.10 Homework / Assignments

### Assignment 1: Basic Logging Setup

Create a Java project (using Maven or Gradle) with Log4j2 and SLF4J. Write a class called `StudentManager` that manages a list of students. Implement the following methods, each with appropriate logging:

- `addStudent(String name, int age)` -- Log an INFO message when a student is added.
- `removeStudent(String name)` -- Log a WARN if the student is not found, INFO if removed.
- `findStudent(String name)` -- Log a DEBUG message with the search query and result.

Configure your logging to output to both the console and a file called `student-manager.log`.

### Assignment 2: Logging Levels in Practice

Using the project from Assignment 1, add a method `importStudentsFromFile(String filePath)` that reads student data from a CSV file. Use the following logging levels appropriately:

- **DEBUG:** Log each line read from the file.
- **INFO:** Log the total number of students imported.
- **WARN:** Log any lines that have invalid format but continue processing.
- **ERROR:** Log if the file cannot be opened (include the exception).

### Assignment 3: Custom Pattern and Rolling File

Configure a `log4j2.xml` file that:

1. Uses a console appender with colorized output showing time (hours:minutes:seconds), level, short class name, and message.
2. Uses a rolling file appender that creates a new file daily and keeps at most 7 days of logs.
3. Sets the root logger to INFO, but sets your application's package to DEBUG.

Test your configuration by writing a simple program that generates messages at all logging levels.

### Assignment 4: Exception Logging

Write a `Calculator` class with a `divide(double a, double b)` method. When `b` is zero, throw an `ArithmeticException`. In the calling code, catch the exception and log it at the ERROR level, making sure the full stack trace appears in the log output. Verify that the stack trace is visible in both your console and file appenders.
