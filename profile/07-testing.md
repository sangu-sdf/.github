# Chapter 7: Testing

## Introduction

Imagine you have spent weeks building a software application. You have written thousands of lines of code, fixed numerous bugs, and added feature after feature. Now comes the critical question: **does it actually work?** Not just in the way you think it should, but in the way the specification demands, under all the conditions real users will subject it to.

This is where **software testing** comes in. Testing is the process of evaluating your software to verify that it meets its specified requirements and to identify defects before they reach your users. It is not an afterthought or a luxury -- it is an essential part of the software development process that directly determines the quality, reliability, and reputation of your product.

In this chapter, you will learn what testing is, why it matters, the different types and methods of testing, and how to write automated unit tests in Java using **JUnit 5**. You will also learn about stubs, mocks, and the principles of quality testing that separate amateur code from professional software.

---

## 7.1 What Is Software Testing?

### 7.1.1 Definition

**Software testing** is the process of executing a program or system with the intent of finding defects, verifying that the software meets its specified requirements, and ensuring that it behaves correctly under expected and unexpected conditions.

Put simply: testing checks whether each component of your software works "correctly" -- meaning it matches the pre-defined specifications. If your specification says that adding 2 and 3 should return 5, testing verifies that your code actually returns 5 (and not 4, or 6, or an error).

### 7.1.2 Why Testing Matters

Without testing, you are essentially shipping code based on hope. Here are the concrete reasons why testing is non-negotiable in professional software development:

1. **Detect defects early:** Bugs found during development cost a fraction of what they cost in production. A bug caught by a unit test costs minutes to fix. The same bug found by a customer in production might cost hours of debugging, a hotfix release, customer support calls, and lost trust.

2. **Ensure correctness:** Testing verifies that your software does what it is supposed to do. It provides evidence -- not just confidence, but proof -- that your code meets its requirements.

3. **Meet market standards:** Users and businesses expect software to work reliably. Poor quality leads to negative reviews, lost customers, and damaged reputation.

4. **Prevent regressions:** When you fix a bug or add a feature, existing functionality must continue to work. Tests serve as a safety net that catches unintended side effects.

5. **Enable refactoring:** Without tests, developers are afraid to change code because they might break something. With comprehensive tests, you can confidently restructure and improve code, knowing that the tests will catch any problems.

6. **Document behavior:** Well-written tests serve as executable documentation. They show exactly how the code is expected to behave, which is often clearer than written documentation.

### 7.1.3 Goals of Testing

When you test your software, you are trying to achieve several goals:

- **Verify correctness:** Confirm that the software produces the expected output for given inputs.
- **Find defects:** Discover bugs, errors, and unexpected behaviors before they reach users.
- **Validate against standards:** Ensure the product meets industry standards and business requirements.
- **Resolve issues during development:** Fix problems while they are cheap and easy to address.
- **Prevent future failures:** Build confidence that changes will not introduce new problems.

### 7.1.4 Characteristics of Good Tests

Not all tests are created equal. Your tests should be:

- **Practical:** They should test realistic scenarios that actually matter.
- **Reliable:** They should produce the same result every time they run (no flaky tests).
- **Authentic:** They should reflect real-world usage patterns.
- **Capable of finding bugs:** If a test never fails, it is probably not testing anything meaningful.
- **Able to validate correctness:** They should verify that the software meets its specification.
- **Fast:** Tests that take too long to run will be skipped by developers.
- **Independent:** Each test should be able to run in isolation without depending on other tests.
- **Readable:** Other developers should be able to understand what a test is checking and why.

---

## 7.2 When Should You Test?

### 7.2.1 Testing During Development

Many types of testing can and should happen while you are writing code:

- **Unit tests** are written alongside (or even before) the code they test. This is the foundation of Test-Driven Development (TDD).
- **API tests** verify that your interfaces work correctly as you build them.
- **Integration tests** verify that components work together correctly.

### 7.2.2 Testing After Development

Some testing naturally happens after the code is written:

- **UI testing** verifies that the user interface works as designed.
- **User Acceptance Testing (UAT)** involves end users verifying the software meets their needs.
- **Performance testing** measures how the system behaves under load.

### 7.2.3 When Should Testing Stop?

Testing could theoretically go on forever -- there are always more scenarios to check. In practice, testing can stop (or significantly reduce) when:

- All required test cases have been executed effectively.
- Code defects have been minimized or eliminated below an acceptable threshold.
- The testing team has completed their test plan.
- The product has been adequately secured against known threats.
- The product is ready for release based on exit criteria.

However, in modern continuous integration/continuous deployment (CI/CD) pipelines, automated tests run on every code change, so testing never truly "stops" -- it becomes an ongoing, automated process.

---

## 7.3 Software Testing Methods

Software testing is broadly divided into two categories: **functional testing** and **non-functional testing**. The key difference is that functional testing verifies *what* the software does (its behavior), while non-functional testing verifies *how well* it does it (its performance, security, scalability).

### 7.3.1 Functional Testing

Functional testing examines whether the software conforms to its defined specifications. It tests the software's behavior by providing inputs and verifying that the outputs match expectations.

> Remember: "functional testing" does not mean testing individual functions one by one. It means testing the *functionality* of the software -- which might involve testing entire workflows that span multiple functions, classes, and modules.

### 7.3.2 Non-Functional Testing

Non-functional testing evaluates the system's attributes beyond basic functionality: performance, scalability, reliability, security, and usability. These tests are often more forward-looking, assessing the product's viability and value for the long term.

---

## 7.4 Types of Functional Testing

### 7.4.1 Unit Testing

**Unit testing** is the most fundamental level of testing. It verifies that individual units of code -- typically methods or functions -- work correctly in isolation.

Key characteristics of unit tests:
- They test a single "unit" of code (usually one method).
- They run fast (milliseconds per test).
- They are isolated from external dependencies (databases, networks, file systems).
- They are automated and run frequently (on every build).

A unit test follows the **AAA pattern:**
1. **Arrange:** Set up the test data and conditions.
2. **Act:** Execute the code being tested.
3. **Assert:** Verify that the result matches expectations.

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalculatorTest {

    @Test
    void additionShouldReturnSumOfTwoNumbers() {
        // Arrange
        Calculator calculator = new Calculator();

        // Act
        int result = calculator.add(3, 5);

        // Assert
        assertEquals(8, result);
    }
}
```

### 7.4.2 API Testing

An **Application Programming Interface (API)** is the connection between your program and external applications or services. API testing verifies that these interfaces work correctly -- that they accept the right inputs, return the right outputs, handle errors gracefully, and perform within acceptable time limits.

API testing is particularly important because:
- APIs are contracts between systems. If one side breaks the contract, integrations fail.
- APIs are often exposed to external consumers, making reliability critical.
- Bugs in APIs can have cascading effects across multiple systems.

In Java, API testing can be done using libraries like **REST Assured** for HTTP APIs:

```java
import io.restassured.RestAssured;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

class UserApiTest {

    @Test
    void shouldReturnUserById() {
        given()
            .baseUri("http://localhost:8080")
            .pathParam("id", 1)
        .when()
            .get("/api/users/{id}")
        .then()
            .statusCode(200)
            .body("name", equalTo("John Doe"))
            .body("email", equalTo("john@example.com"));
    }
}
```

### 7.4.3 UI (User Interface) Testing

The **user interface** is the communication channel between the user and the application. Every software product is developed with specific UI specifications that define how users interact with the application.

UI testing verifies that these specifications are met:
- Does the registration page accept and validate user input correctly?
- Does the "Submit" button actually submit the form?
- Do navigation links take users to the correct pages?
- Does the UI render correctly across different screen sizes and browsers?

In Java, UI testing can be automated using **Selenium WebDriver**:

```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.By;
import org.openqa.selenium.chrome.ChromeDriver;
import org.junit.jupiter.api.*;
import static org.junit.jupiter.api.Assertions.*;

class LoginPageTest {

    private WebDriver driver;

    @BeforeEach
    void setUp() {
        driver = new ChromeDriver();
    }

    @AfterEach
    void tearDown() {
        driver.quit();
    }

    @Test
    void shouldLoginWithValidCredentials() {
        driver.get("http://localhost:8080/login");

        WebElement usernameField = driver.findElement(By.id("username"));
        WebElement passwordField = driver.findElement(By.id("password"));
        WebElement loginButton = driver.findElement(By.id("loginBtn"));

        usernameField.sendKeys("testuser");
        passwordField.sendKeys("password123");
        loginButton.click();

        String currentUrl = driver.getCurrentUrl();
        assertTrue(currentUrl.contains("/dashboard"),
                   "User should be redirected to the dashboard after login.");
    }
}
```

### 7.4.4 Integration Testing

While unit tests verify individual components in isolation, **integration testing** verifies that those components work correctly together. Integration testing checks the interfaces and interactions between modules.

For example, in an e-commerce application, an integration test might verify:
- When a user adds an item to their cart, the cart page correctly displays the item.
- When a user proceeds to checkout, the payment module correctly receives the order total from the cart module.
- When a payment is processed, the order module correctly updates the order status.

The goal of integration testing is to confirm that components work *synchronously* -- that Component A works correctly with Component B, and that the data flows correctly between them.

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class OrderIntegrationTest {

    @Test
    void shouldCreateOrderAndUpdateInventory() {
        // Set up real (or in-memory) database
        Database db = new InMemoryDatabase();
        InventoryService inventory = new InventoryService(db);
        OrderService orders = new OrderService(db, inventory);

        // Add product to inventory
        inventory.addProduct("LAPTOP-001", 10);

        // Place an order
        Order order = orders.placeOrder("LAPTOP-001", 2);

        // Verify the order was created
        assertNotNull(order);
        assertEquals("CONFIRMED", order.getStatus());

        // Verify inventory was updated (integration point)
        assertEquals(8, inventory.getStock("LAPTOP-001"));
    }
}
```

### 7.4.5 Regression Testing

Software development is iterative. You fix bugs, add features, and refactor code. Each change carries the risk of breaking something that was previously working. **Regression testing** is the process of re-running existing tests after code changes to ensure that nothing has been broken.

Types of regression testing include:

- **Corrective regression testing:** Re-running the exact same test cases when the code has not changed (but the environment has).
- **Selective regression testing:** Running only the tests that are related to the changed code.
- **Progressive regression testing:** Writing new tests for new functionality and running them alongside existing tests.

In practice, regression testing is one of the strongest arguments for automated testing. If all your tests are automated and run in a CI/CD pipeline, regression testing happens automatically on every code change.

---

## 7.5 Types of Non-Functional Testing

### 7.5.1 Volume Testing

Every application has limits on how much data it can handle. **Volume testing** (also called **load testing**) evaluates how the system performs with varying amounts of data, particularly large volumes that approach or exceed the system's design capacity.

Questions that volume testing answers:
- How does the application perform with 100 users? 1,000? 100,000?
- Does the database query still return in under 1 second with 10 million records?
- Does the file upload feature work with a 2 GB file?
- What happens when the message queue has 1 million pending messages?

Tools commonly used for volume/load testing in Java:
- **Apache JMeter** for HTTP load testing
- **Gatling** for high-performance load testing with Scala/Java
- **k6** for modern load testing

### 7.5.2 Security Testing

In today's world, security is a fundamental requirement for any software application. **Security testing** evaluates the application's ability to protect data and maintain functionality against attacks and threats.

Security testing covers:

- **Authentication:** Can users prove their identity? Are passwords stored securely (hashed, not plaintext)?
- **Authorization:** Can users access only what they are allowed to? Is role-based access enforced?
- **Confidentiality:** Is sensitive data encrypted in transit and at rest?
- **Input validation:** Is the application protected against SQL injection, XSS, and other injection attacks?
- **Session management:** Are sessions properly managed, timed out, and invalidated?

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class SecurityTest {

    @Test
    void shouldRejectSqlInjectionInLoginForm() {
        AuthService authService = new AuthService();

        // Attempt SQL injection
        boolean result = authService.login("admin' OR '1'='1", "anything");

        assertFalse(result, "SQL injection should not bypass authentication.");
    }

    @Test
    void shouldNotExposePasswordInErrorMessage() {
        AuthService authService = new AuthService();

        try {
            authService.login("invaliduser", "secretpassword");
        } catch (AuthenticationException e) {
            assertFalse(e.getMessage().contains("secretpassword"),
                        "Error message should not contain the user's password.");
        }
    }
}
```

---

## 7.6 Unit Testing with JUnit 5

### 7.6.1 What Is JUnit 5?

**JUnit 5** (also known as **JUnit Jupiter**) is the most widely used testing framework for Java. It provides annotations, assertions, and utilities for writing and running automated tests. JUnit 5 is the latest major version, bringing modern features like parameterized tests, nested tests, and lambda-friendly assertions.

### 7.6.2 Setting Up JUnit 5

**Maven:**
```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.1</version>
    <scope>test</scope>
</dependency>
```

**Gradle:**
```groovy
testImplementation 'org.junit.jupiter:junit-jupiter:5.10.1'
```

### 7.6.3 The Calculator Example

Let us build a complete example step by step. First, the class we want to test:

```java
public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }

    public double divide(double a, double b) {
        if (b == 0) {
            throw new ArithmeticException("Cannot divide by zero");
        }
        return a / b;
    }

    public boolean isEven(int number) {
        return number % 2 == 0;
    }

    public int factorial(int n) {
        if (n < 0) {
            throw new IllegalArgumentException("Factorial is not defined for negative numbers");
        }
        if (n == 0 || n == 1) {
            return 1;
        }
        int result = 1;
        for (int i = 2; i <= n; i++) {
            result *= i;
        }
        return result;
    }
}
```

Now, the comprehensive test class:

```java
import org.junit.jupiter.api.*;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;
import org.junit.jupiter.params.provider.ValueSource;

import static org.junit.jupiter.api.Assertions.*;

@DisplayName("Calculator Tests")
class CalculatorTest {

    private Calculator calculator;

    @BeforeEach
    void setUp() {
        calculator = new Calculator();
    }

    // ==================== Addition Tests ====================

    @Nested
    @DisplayName("Addition")
    class AdditionTests {

        @Test
        @DisplayName("should return the sum of two positive numbers")
        void addPositiveNumbers() {
            assertEquals(8, calculator.add(3, 5));
        }

        @Test
        @DisplayName("should return correct result when adding negative numbers")
        void addNegativeNumbers() {
            assertEquals(-8, calculator.add(-3, -5));
        }

        @Test
        @DisplayName("should return correct result when adding zero")
        void addZero() {
            assertEquals(5, calculator.add(5, 0));
            assertEquals(5, calculator.add(0, 5));
        }

        @Test
        @DisplayName("should return zero when adding a number and its negation")
        void addNumberAndItsNegation() {
            assertEquals(0, calculator.add(5, -5));
        }

        @ParameterizedTest(name = "{0} + {1} = {2}")
        @CsvSource({
            "1, 1, 2",
            "10, 20, 30",
            "-5, 5, 0",
            "100, -50, 50",
            "0, 0, 0"
        })
        @DisplayName("should add two numbers correctly (parameterized)")
        void addParameterized(int a, int b, int expected) {
            assertEquals(expected, calculator.add(a, b));
        }
    }

    // ==================== Subtraction Tests ====================

    @Nested
    @DisplayName("Subtraction")
    class SubtractionTests {

        @Test
        @DisplayName("should subtract two numbers correctly")
        void subtractNumbers() {
            assertEquals(2, calculator.subtract(5, 3));
        }

        @Test
        @DisplayName("should return negative result when subtracting larger from smaller")
        void subtractLargerFromSmaller() {
            assertEquals(-2, calculator.subtract(3, 5));
        }
    }

    // ==================== Multiplication Tests ====================

    @Nested
    @DisplayName("Multiplication")
    class MultiplicationTests {

        @Test
        @DisplayName("should multiply two positive numbers")
        void multiplyPositiveNumbers() {
            assertEquals(15, calculator.multiply(3, 5));
        }

        @Test
        @DisplayName("should return zero when multiplying by zero")
        void multiplyByZero() {
            assertEquals(0, calculator.multiply(5, 0));
            assertEquals(0, calculator.multiply(0, 5));
        }

        @Test
        @DisplayName("should return negative when multiplying positive by negative")
        void multiplyMixedSigns() {
            assertEquals(-15, calculator.multiply(3, -5));
        }
    }

    // ==================== Division Tests ====================

    @Nested
    @DisplayName("Division")
    class DivisionTests {

        @Test
        @DisplayName("should divide two numbers correctly")
        void divideNumbers() {
            assertEquals(2.5, calculator.divide(5, 2), 0.0001);
        }

        @Test
        @DisplayName("should throw ArithmeticException when dividing by zero")
        void divideByZero() {
            ArithmeticException exception = assertThrows(
                ArithmeticException.class,
                () -> calculator.divide(10, 0)
            );
            assertEquals("Cannot divide by zero", exception.getMessage());
        }

        @Test
        @DisplayName("should return correct result for integer division")
        void divideExactly() {
            assertEquals(5.0, calculator.divide(10, 2), 0.0001);
        }
    }

    // ==================== isEven Tests ====================

    @Nested
    @DisplayName("isEven")
    class IsEvenTests {

        @ParameterizedTest(name = "{0} is even")
        @ValueSource(ints = {0, 2, 4, 100, -6})
        @DisplayName("should return true for even numbers")
        void evenNumbers(int number) {
            assertTrue(calculator.isEven(number));
        }

        @ParameterizedTest(name = "{0} is odd")
        @ValueSource(ints = {1, 3, 5, 99, -7})
        @DisplayName("should return false for odd numbers")
        void oddNumbers(int number) {
            assertFalse(calculator.isEven(number));
        }
    }

    // ==================== Factorial Tests ====================

    @Nested
    @DisplayName("Factorial")
    class FactorialTests {

        @Test
        @DisplayName("factorial of 0 should be 1")
        void factorialOfZero() {
            assertEquals(1, calculator.factorial(0));
        }

        @Test
        @DisplayName("factorial of 1 should be 1")
        void factorialOfOne() {
            assertEquals(1, calculator.factorial(1));
        }

        @Test
        @DisplayName("factorial of 5 should be 120")
        void factorialOfFive() {
            assertEquals(120, calculator.factorial(5));
        }

        @Test
        @DisplayName("factorial of negative number should throw exception")
        void factorialOfNegative() {
            assertThrows(IllegalArgumentException.class,
                         () -> calculator.factorial(-1));
        }
    }
}
```

### 7.6.4 Key JUnit 5 Annotations

| Annotation | Purpose |
|---|---|
| `@Test` | Marks a method as a test method |
| `@BeforeEach` | Runs before each test method (setup) |
| `@AfterEach` | Runs after each test method (cleanup) |
| `@BeforeAll` | Runs once before all tests in the class (must be static) |
| `@AfterAll` | Runs once after all tests in the class (must be static) |
| `@DisplayName` | Sets a human-readable name for the test |
| `@Nested` | Allows grouping tests in inner classes |
| `@Disabled` | Temporarily disables a test |
| `@ParameterizedTest` | Runs the same test with different inputs |
| `@ValueSource` | Provides a simple array of values for parameterized tests |
| `@CsvSource` | Provides comma-separated values for parameterized tests |
| `@RepeatedTest` | Runs the same test multiple times |
| `@Timeout` | Fails the test if it takes longer than specified |

### 7.6.5 Key JUnit 5 Assertions

```java
// Basic assertions
assertEquals(expected, actual);          // Checks equality
assertNotEquals(unexpected, actual);     // Checks inequality
assertTrue(condition);                   // Checks that condition is true
assertFalse(condition);                  // Checks that condition is false
assertNull(object);                      // Checks that object is null
assertNotNull(object);                   // Checks that object is not null

// Exception assertions
assertThrows(ExpectedException.class, () -> {
    // Code that should throw
});

// Floating-point comparison with delta
assertEquals(3.14, result, 0.001);       // True if difference < 0.001

// Array assertions
assertArrayEquals(expectedArray, actualArray);

// Grouped assertions (all execute even if one fails)
assertAll("person",
    () -> assertEquals("John", person.getFirstName()),
    () -> assertEquals("Doe", person.getLastName()),
    () -> assertEquals(30, person.getAge())
);

// Timeout assertion
assertTimeout(Duration.ofSeconds(2), () -> {
    // Code that should complete within 2 seconds
    slowMethod();
});
```

---

## 7.7 Stub and Mock Objects

### 7.7.1 The Problem of External Dependencies

Unit tests should test a single unit of code in isolation. But real-world code rarely exists in isolation -- it depends on databases, web services, file systems, email servers, and other components. You cannot (and should not) connect to a real database or call a real API in a unit test. So how do you test code that depends on these external systems?

The answer is **test doubles** -- objects that stand in for real dependencies during testing. The two most common types are **stubs** and **mocks**.

### 7.7.2 Stubs

A **stub** is a simplified implementation of a dependency that returns pre-configured responses. Stubs do not verify behavior -- they simply provide the data your code needs to run.

```java
// The interface our code depends on
public interface UserRepository {
    User findById(int id);
    void save(User user);
}

// A stub implementation for testing
public class StubUserRepository implements UserRepository {

    @Override
    public User findById(int id) {
        // Always return a predetermined user
        return new User(1, "John Doe", "john@example.com");
    }

    @Override
    public void save(User user) {
        // Do nothing -- we don't need actual persistence in tests
    }
}
```

Using the stub in a test:

```java
@Test
void shouldGreetUserByName() {
    UserRepository stubRepo = new StubUserRepository();
    GreetingService service = new GreetingService(stubRepo);

    String greeting = service.greetUser(1);

    assertEquals("Hello, John Doe!", greeting);
}
```

### 7.7.3 Mocks

A **mock** is like a stub, but it also records how it was called and allows you to verify interactions. Mocks answer the question: "Was this method called with the right arguments?"

While you can create mocks manually, it is much more practical to use a mocking framework. **Mockito** is the most popular mocking library for Java.

**Setting up Mockito (Maven):**
```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.8.0</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>5.8.0</version>
    <scope>test</scope>
</dependency>
```

**Using Mockito:**

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    @Mock
    private PaymentGateway paymentGateway;

    @Mock
    private EmailService emailService;

    @InjectMocks
    private OrderService orderService;

    @Test
    void shouldProcessOrderSuccessfully() {
        // Arrange: configure mock behavior
        Order order = new Order(1, "LAPTOP", 999.99);
        when(paymentGateway.charge(999.99)).thenReturn(new PaymentResult(true, "TXN-001"));

        // Act
        OrderResult result = orderService.processOrder(order);

        // Assert: verify the result
        assertTrue(result.isSuccessful());

        // Verify: check that the mocks were called correctly
        verify(paymentGateway).charge(999.99);
        verify(orderRepository).save(order);
        verify(emailService).sendConfirmation(order);
    }

    @Test
    void shouldNotSaveOrderWhenPaymentFails() {
        // Arrange
        Order order = new Order(2, "PHONE", 599.99);
        when(paymentGateway.charge(599.99)).thenReturn(new PaymentResult(false, null));

        // Act
        OrderResult result = orderService.processOrder(order);

        // Assert
        assertFalse(result.isSuccessful());

        // Verify: order should NOT be saved when payment fails
        verify(paymentGateway).charge(599.99);
        verify(orderRepository, never()).save(any());
        verify(emailService, never()).sendConfirmation(any());
    }
}
```

### 7.7.4 Stub vs. Mock: When to Use Which

| Aspect | Stub | Mock |
|---|---|---|
| Purpose | Provides canned answers | Verifies interactions |
| Complexity | Simple | More sophisticated |
| Verification | State-based (check return value) | Behavior-based (check method calls) |
| Use when | You need a dependency to return data | You need to verify that a method was called |
| Example | "Return user John when asked for ID 1" | "Verify that sendEmail() was called exactly once" |

**General guideline:** Use stubs for queries (methods that return data) and mocks for commands (methods that perform actions with side effects).

---

## 7.8 Quality Testing

### 7.8.1 What Is Quality Testing?

Quality testing goes beyond verifying that individual features work. It evaluates the overall quality of the software product from multiple perspectives:

- **Code quality:** Is the code well-structured, readable, and maintainable?
- **Test coverage:** What percentage of the code is covered by tests?
- **Defect density:** How many defects exist per thousand lines of code?
- **User satisfaction:** Does the software meet users' expectations?

### 7.8.2 Code Coverage

**Code coverage** measures what percentage of your source code is executed during testing. It is an important metric, but it should not be the only goal.

Common coverage metrics:
- **Line coverage:** Percentage of lines executed by tests
- **Branch coverage:** Percentage of decision branches (if/else) taken by tests
- **Method coverage:** Percentage of methods called by tests
- **Condition coverage:** Percentage of boolean conditions evaluated to both true and false

In Java, **JaCoCo** is the standard tool for measuring code coverage:

```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.11</version>
    <executions>
        <execution>
            <goals>
                <goal>prepare-agent</goal>
            </goals>
        </execution>
        <execution>
            <id>report</id>
            <phase>test</phase>
            <goals>
                <goal>report</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

> **Important:** 100% code coverage does not mean your software is bug-free. It only means every line was executed during testing. The tests themselves might be weak or miss edge cases. Aim for meaningful tests, not just high coverage numbers.

### 7.8.3 The Testing Pyramid

The **testing pyramid** is a widely accepted model for how to balance different types of tests:

```
        /  UI   \          Few, slow, expensive
       /  Tests  \
      /___________\
     / Integration \       Moderate number
    /    Tests      \
   /_________________\
  /    Unit Tests     \    Many, fast, cheap
 /_____________________\
```

- **Unit tests** form the base: you should have many of them. They are fast, cheap, and test individual components.
- **Integration tests** are in the middle: fewer than unit tests, they verify that components work together.
- **UI/E2E tests** are at the top: the fewest in number, they are slow and expensive but verify the entire system from the user's perspective.

### 7.8.4 Test Naming Conventions

Good test names describe what is being tested, under what conditions, and what the expected outcome is. A common pattern is:

```
should[ExpectedBehavior]When[Condition]
```

Examples:
```java
void shouldReturnZeroWhenListIsEmpty()
void shouldThrowExceptionWhenInputIsNull()
void shouldSendEmailWhenOrderIsPlaced()
void shouldReturnFalseWhenPasswordIsTooShort()
```

---

## 7.9 Test-Driven Development (TDD)

### 7.9.1 What Is TDD?

**Test-Driven Development** is a development practice where you write the test *before* you write the code. It follows a simple cycle known as **Red-Green-Refactor**:

1. **Red:** Write a test for the next piece of functionality. Run it -- it should fail because the functionality does not exist yet.
2. **Green:** Write the minimum amount of code needed to make the test pass.
3. **Refactor:** Clean up the code while keeping the tests green.

### 7.9.2 TDD Example: Building a StringCalculator

**Step 1: Red** -- Write the first test:

```java
@Test
void shouldReturnZeroForEmptyString() {
    StringCalculator calc = new StringCalculator();
    assertEquals(0, calc.add(""));
}
```

**Step 2: Green** -- Write just enough code to pass:

```java
public class StringCalculator {
    public int add(String numbers) {
        if (numbers.isEmpty()) {
            return 0;
        }
        return 0; // placeholder
    }
}
```

**Step 3: Red** -- Write the next test:

```java
@Test
void shouldReturnNumberForSingleNumber() {
    StringCalculator calc = new StringCalculator();
    assertEquals(5, calc.add("5"));
}
```

**Step 4: Green** -- Extend the implementation:

```java
public int add(String numbers) {
    if (numbers.isEmpty()) {
        return 0;
    }
    return Integer.parseInt(numbers);
}
```

**Step 5: Red** -- Write a test for multiple numbers:

```java
@Test
void shouldReturnSumForTwoNumbers() {
    StringCalculator calc = new StringCalculator();
    assertEquals(8, calc.add("3,5"));
}
```

**Step 6: Green** -- Handle multiple numbers:

```java
public int add(String numbers) {
    if (numbers.isEmpty()) {
        return 0;
    }
    String[] parts = numbers.split(",");
    int sum = 0;
    for (String part : parts) {
        sum += Integer.parseInt(part.trim());
    }
    return sum;
}
```

This cycle continues, with each iteration adding new functionality driven by a new test.

---

## 7.10 Practical Tips for Writing Good Tests

1. **Each test should test one thing.** If a test has multiple assertions checking different behaviors, split it into separate tests.

2. **Tests should be independent.** No test should depend on the result of another test. Use `@BeforeEach` to set up fresh state.

3. **Use descriptive test names.** A failing test name should tell you what went wrong without reading the test code.

4. **Do not test trivial code.** Getters, setters, and constructors rarely need their own tests unless they contain logic.

5. **Test edge cases.** Empty inputs, null values, boundary values, maximum/minimum values, and error conditions are where bugs hide.

6. **Keep tests fast.** If your tests take more than a few seconds to run, developers will skip them.

7. **Do not test implementation details.** Test the behavior (what the code does), not the implementation (how it does it). This makes tests resilient to refactoring.

8. **Fix broken tests immediately.** A test suite with known failures quickly becomes one that is ignored entirely.

---

## 7.11 Summary and Key Takeaways

- **Software testing** is the process of verifying that software meets its requirements and identifying defects.
- Testing goals include finding defects, verifying correctness, meeting standards, and preventing future failures.
- **Functional testing** verifies what the software does: unit testing, API testing, UI testing, integration testing, and regression testing.
- **Non-functional testing** verifies how well the software performs: volume testing, security testing, performance testing.
- **Unit testing** is the foundation of the testing pyramid -- tests should be fast, isolated, and numerous.
- **JUnit 5** is the standard testing framework for Java, providing annotations, assertions, and parameterized test support.
- **Stubs** provide pre-configured responses for dependencies; **mocks** verify that methods were called with expected arguments.
- **Mockito** is the most popular Java mocking library.
- **Code coverage** measures what percentage of code is tested, but high coverage does not guarantee quality tests.
- The **testing pyramid** suggests having many unit tests, fewer integration tests, and even fewer UI tests.
- **TDD** (Test-Driven Development) writes tests before code, following the Red-Green-Refactor cycle.

---

## 7.12 Homework / Assignments

### Assignment 1: Calculator Tests

Create a `Calculator` class with the following methods:
- `add(int a, int b)`
- `subtract(int a, int b)`
- `multiply(int a, int b)`
- `divide(double a, double b)` -- should throw `ArithmeticException` when `b` is zero
- `power(int base, int exponent)` -- calculates base raised to the exponent
- `modulo(int a, int b)` -- returns the remainder of a divided by b

Write comprehensive JUnit 5 tests for each method. Include tests for:
- Normal cases with positive and negative numbers
- Edge cases (zero, maximum integer values)
- Error cases (division by zero, negative exponents if unsupported)
- Use `@ParameterizedTest` for at least two methods

### Assignment 2: String Utility Tests

Create a `StringUtils` class with the following methods:
- `reverse(String input)` -- reverses the string
- `isPalindrome(String input)` -- checks if the string reads the same forward and backward
- `countVowels(String input)` -- counts the number of vowels
- `capitalize(String input)` -- capitalizes the first letter of each word

Write JUnit 5 tests for each method. Make sure to test null inputs, empty strings, single characters, and strings with special characters.

### Assignment 3: Testing with Mocks

Create a simple `UserService` class that depends on a `UserRepository` interface. The `UserService` should have:
- `registerUser(String username, String email)` -- saves a new user and sends a welcome email
- `getUserByEmail(String email)` -- retrieves a user by email

Write JUnit 5 tests using **Mockito** to:
- Mock the `UserRepository` and `EmailService`
- Verify that `save()` is called when registering a user
- Verify that a welcome email is sent after registration
- Test what happens when the repository throws an exception

### Assignment 4: Integration Test

Write an integration test that verifies the interaction between a `ShoppingCart` class and an `InventoryService` class. The test should verify that:
- Adding an item to the cart reduces the inventory count
- Removing an item from the cart restores the inventory count
- Adding more items than available in inventory throws an exception
