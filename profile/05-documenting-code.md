# Chapter 5: Documenting Code -- JavaDoc, Doxygen, and GitHub Pages

## Introduction

Writing code that works is only half the job. The other half is making sure that other developers -- and your future self -- can understand that code. Documentation is the bridge between what the code *does* and what the reader *needs to know* about it.

This chapter covers **code documentation** in Java: from inline comments to structured **JavaDoc** documentation comments, including the standard tags that describe parameters, return values, exceptions, and more. You will then learn how to use **Doxygen**, a universal documentation generator, to transform those comments into professional HTML documentation pages. Finally, you will learn how to publish that documentation online for free using **GitHub Pages**.

By the end of this chapter, you will be able to write comprehensive JavaDoc comments, generate browsable documentation from your code, and host it publicly on the web.

---

## 5.1 Why Document Code?

### The Case for Documentation

Consider this method:

```java
public double calc(double a, double b, int t) {
    if (t == 1) return a + b;
    if (t == 2) return a - b;
    if (t == 3) return a * b;
    if (t == 4) {
        if (b == 0) throw new ArithmeticException();
        return a / b;
    }
    return 0;
}
```

Without any context, a reader must carefully analyze the code to understand what `t` means, what values it can take, and what happens with invalid input. Now compare it with a documented version:

```java
/**
 * Performs a basic arithmetic operation on two numbers.
 *
 * @param a the first operand
 * @param b the second operand
 * @param t the operation type: 1 = add, 2 = subtract, 3 = multiply, 4 = divide
 * @return the result of the arithmetic operation
 * @throws ArithmeticException if {@code t} is 4 and {@code b} is zero
 * @throws IllegalArgumentException if {@code t} is not 1, 2, 3, or 4
 */
public double calculate(double a, double b, int t) {
    // ...
}
```

The documentation immediately tells you everything you need to know to use this method correctly, without reading the implementation.

### Types of Documentation

Software documentation exists at multiple levels:

| Level | Purpose | Examples |
|---|---|---|
| **Inline comments** | Explain tricky or non-obvious logic within code | `// Handle edge case for leap years` |
| **Documentation comments** | Describe the purpose, parameters, and behavior of classes, methods, and fields | JavaDoc, Doxygen comments |
| **README files** | Provide project-level overview, setup instructions, usage examples | `README.md` in repository root |
| **Architecture docs** | Describe high-level system design and decisions | Design documents, wikis |
| **API documentation** | Generated HTML pages describing public interfaces | JavaDoc output, Doxygen output |
| **User documentation** | End-user guides, tutorials, FAQs | User manuals, help pages |

This chapter focuses on **documentation comments** and **generated API documentation**.

---

## 5.2 Comments in Java -- A Quick Review

Java supports three styles of comments:

### Single-Line Comments

```java
// This is a single-line comment
int count = 0; // Initialize counter to zero
```

### Multi-Line (Block) Comments

```java
/*
 * This is a multi-line comment.
 * It can span several lines.
 */
int total = calculateTotal(items);
```

### Documentation Comments (JavaDoc)

```java
/**
 * This is a documentation comment.
 * It starts with /** and ends with */
 * It is processed by documentation generators.
 */
public void processData() {
    // ...
}
```

The critical difference is the **double asterisk** at the start: `/**` instead of `/*`. This signals to documentation tools that the comment contains structured documentation meant to be extracted and published.

---

## 5.3 JavaDoc Comments

### What Is JavaDoc?

**JavaDoc** is both a documentation format and a tool. As a format, it defines a set of tags (like `@param`, `@return`, `@throws`) that you embed in special comments. As a tool (bundled with the JDK), it parses those comments and generates HTML documentation.

JavaDoc comments can be placed before:

- **Classes** and **interfaces**
- **Constructors**
- **Methods**
- **Fields** (instance variables and constants)
- **Packages** (via `package-info.java`)
- **Enums**

### Basic Structure

A JavaDoc comment has two parts:

1. **Description**: Free-form text explaining the element. The first sentence becomes the summary in index pages.
2. **Tags**: Structured metadata starting with `@` symbols.

```java
/**
 * A utility class for performing basic mathematical operations.
 * This class provides static methods for common calculations
 * such as addition, subtraction, multiplication, and division.
 *
 * <p>All methods in this class are thread-safe and handle
 * edge cases such as division by zero.</p>
 *
 * @author Jane Smith
 * @version 1.2
 * @since 1.0
 */
public class MathUtils {
    // ...
}
```

> **Note**: JavaDoc descriptions support HTML tags like `<p>`, `<code>`, `<ul>`, `<li>`, `<b>`, etc. This lets you format the documentation with paragraphs, lists, bold text, and code snippets.

---

## 5.4 JavaDoc Tags Reference

### Common Tags

Here is a comprehensive reference of the most important JavaDoc tags:

#### `@param` -- Method Parameters

Describes a method or constructor parameter. You should provide one `@param` tag for every parameter.

```java
/**
 * Calculates the area of a rectangle.
 *
 * @param width  the width of the rectangle, must be positive
 * @param height the height of the rectangle, must be positive
 * @return the area of the rectangle
 */
public double rectangleArea(double width, double height) {
    return width * height;
}
```

#### `@return` -- Return Value

Describes what the method returns. Omit this tag for `void` methods.

```java
/**
 * Generates a random integer within the specified range.
 *
 * @param min the lower bound (inclusive)
 * @param max the upper bound (inclusive)
 * @return a random integer between {@code min} and {@code max}, inclusive
 */
public int randomInRange(int min, int max) {
    return new Random().nextInt(max - min + 1) + min;
}
```

#### `@throws` (or `@exception`) -- Exceptions

Documents the exceptions a method can throw and under what circumstances. Use one tag per exception type.

```java
/**
 * Reads the contents of a file and returns them as a string.
 *
 * @param filePath the path to the file to read
 * @return the contents of the file as a single string
 * @throws FileNotFoundException if the file does not exist at the given path
 * @throws IOException           if an I/O error occurs while reading
 * @throws IllegalArgumentException if {@code filePath} is null or empty
 */
public String readFile(String filePath) throws FileNotFoundException, IOException {
    // ...
}
```

#### `@author` -- Author Information

Specifies the author of a class or interface. Multiple `@author` tags can be used for multiple authors.

```java
/**
 * Represents a bank account with basic operations.
 *
 * @author Jane Smith
 * @author John Doe
 */
public class BankAccount {
    // ...
}
```

#### `@version` -- Version Number

Indicates the version of the class or interface.

```java
/**
 * Provides logging functionality for the application.
 *
 * @author Development Team
 * @version 2.1.0
 */
public class Logger {
    // ...
}
```

#### `@since` -- Introduction Version

Indicates the version when this element was first introduced.

```java
/**
 * Formats log messages with timestamps and log levels.
 *
 * @since 1.5
 */
public String formatMessage(String level, String message) {
    // ...
}
```

#### `@see` -- Cross-References

Creates a "See Also" section with links to other classes, methods, or URLs.

```java
/**
 * Sorts an array of integers using the quicksort algorithm.
 *
 * @param array the array to sort
 * @see java.util.Arrays#sort(int[])
 * @see <a href="https://en.wikipedia.org/wiki/Quicksort">Quicksort on Wikipedia</a>
 */
public void quickSort(int[] array) {
    // ...
}
```

#### `@deprecated` -- Deprecation Notice

Marks an element as deprecated and suggests an alternative.

```java
/**
 * Logs a message to the console.
 *
 * @deprecated Use {@link #log(LogLevel, String)} instead for level-based logging.
 */
@Deprecated
public void log(String message) {
    System.out.println(message);
}
```

#### `{@code}` -- Inline Code

Renders text in monospace font and prevents HTML interpretation. Use it for code references within descriptions.

```java
/**
 * Returns {@code true} if the list is empty, {@code false} otherwise.
 * This is equivalent to checking {@code list.size() == 0}.
 */
public boolean isEmpty() {
    return size == 0;
}
```

#### `{@link}` -- Inline Links

Creates a hyperlink to another class, method, or field within the documentation.

```java
/**
 * Processes the data using the algorithm defined in
 * {@link Algorithm#process(Data)}.
 *
 * @see Algorithm
 */
public void processData(Data data) {
    // ...
}
```

### Tag Summary Table

| Tag | Applies To | Purpose |
|---|---|---|
| `@param name description` | Methods, constructors | Describe a parameter |
| `@return description` | Methods | Describe the return value |
| `@throws ExceptionType description` | Methods, constructors | Document an exception |
| `@author name` | Classes, interfaces | Specify the author |
| `@version number` | Classes, interfaces | Specify the version |
| `@since version` | Any element | Version when introduced |
| `@see reference` | Any element | Cross-reference |
| `@deprecated description` | Any element | Mark as deprecated |
| `{@code text}` | Inline in descriptions | Format as code |
| `{@link reference}` | Inline in descriptions | Create a hyperlink |
| `{@inheritDoc}` | Methods | Inherit parent's doc |

---

## 5.5 Comprehensive JavaDoc Example

Here is a complete Java class with thorough JavaDoc documentation:

```java
/**
 * A simple logging utility that writes messages to the console.
 *
 * <p>The Logger supports three log levels: INFO, WARNING, and ERROR.
 * Messages below the configured minimum log level are suppressed.</p>
 *
 * <p>Usage example:</p>
 * <pre>{@code
 * Logger logger = new Logger("MyApp", LogLevel.INFO);
 * logger.info("Application started");
 * logger.warning("Low memory detected");
 * logger.error("Failed to connect to database");
 * }</pre>
 *
 * @author SDF Course Team
 * @version 2.0
 * @since 1.0
 * @see LogLevel
 */
public class Logger {

    /**
     * Enumeration of supported log levels, ordered by severity.
     */
    public enum LogLevel {
        /** Informational messages for normal operations. */
        INFO,
        /** Warning messages for potentially harmful situations. */
        WARNING,
        /** Error messages for serious failures. */
        ERROR
    }

    /** The name of the component using this logger. */
    private final String componentName;

    /** The minimum log level; messages below this level are suppressed. */
    private LogLevel minimumLevel;

    /**
     * Constructs a new Logger with the specified component name and minimum log level.
     *
     * @param componentName the name of the component or module using this logger;
     *                      must not be {@code null} or empty
     * @param minimumLevel  the minimum log level to display; messages below this
     *                      level will be suppressed
     * @throws IllegalArgumentException if {@code componentName} is null or empty
     */
    public Logger(String componentName, LogLevel minimumLevel) {
        if (componentName == null || componentName.isEmpty()) {
            throw new IllegalArgumentException("Component name must not be null or empty");
        }
        this.componentName = componentName;
        this.minimumLevel = minimumLevel;
    }

    /**
     * Logs a message at the specified log level.
     *
     * <p>The message is printed to the standard output in the format:</p>
     * <pre>[LEVEL] componentName: message</pre>
     *
     * <p>If the specified level is below the configured minimum level,
     * the message is silently discarded.</p>
     *
     * @param level   the severity level of the message
     * @param message the message to log; must not be {@code null}
     * @throws IllegalArgumentException if {@code message} is null
     */
    public void log(LogLevel level, String message) {
        if (message == null) {
            throw new IllegalArgumentException("Message must not be null");
        }
        if (level.ordinal() >= minimumLevel.ordinal()) {
            System.out.println("[" + level + "] " + componentName + ": " + message);
        }
    }

    /**
     * Logs an informational message.
     *
     * <p>This is a convenience method equivalent to calling
     * {@link #log(LogLevel, String)} with {@link LogLevel#INFO}.</p>
     *
     * @param message the informational message to log
     */
    public void info(String message) {
        log(LogLevel.INFO, message);
    }

    /**
     * Logs a warning message.
     *
     * <p>This is a convenience method equivalent to calling
     * {@link #log(LogLevel, String)} with {@link LogLevel#WARNING}.</p>
     *
     * @param message the warning message to log
     */
    public void warning(String message) {
        log(LogLevel.WARNING, message);
    }

    /**
     * Logs an error message.
     *
     * <p>This is a convenience method equivalent to calling
     * {@link #log(LogLevel, String)} with {@link LogLevel#ERROR}.</p>
     *
     * @param message the error message to log
     */
    public void error(String message) {
        log(LogLevel.ERROR, message);
    }

    /**
     * Returns the current minimum log level.
     *
     * @return the minimum log level
     */
    public LogLevel getMinimumLevel() {
        return minimumLevel;
    }

    /**
     * Sets the minimum log level.
     *
     * <p>After calling this method, only messages at or above the new
     * level will be displayed.</p>
     *
     * @param minimumLevel the new minimum log level
     */
    public void setMinimumLevel(LogLevel minimumLevel) {
        this.minimumLevel = minimumLevel;
    }

    /**
     * Returns the name of the component associated with this logger.
     *
     * @return the component name, never {@code null}
     */
    public String getComponentName() {
        return componentName;
    }
}
```

---

## 5.6 JavaDoc Best Practices

### Write for Your Audience

Documentation comments should be written for **users** of your code, not implementers. Focus on:

- **What** the element does (purpose and behavior).
- **How** to use it (parameters, return values, examples).
- **When** it might fail (exceptions).

Avoid describing **how** the implementation works internally unless it affects the user.

### The First Sentence Matters

The first sentence of a JavaDoc comment is used as the **summary** in index pages and tooltips. Make it clear and self-contained:

```java
// Good: clear, complete first sentence
/**
 * Returns the number of elements in this list.
 * ...
 */

// Bad: vague or incomplete first sentence
/**
 * This method is used for getting the size.
 * ...
 */
```

### Document All Public Elements

At a minimum, document:

- Every **public class** and **interface**.
- Every **public method** and **constructor**.
- Every **public field** and **constant**.
- All **parameters**, **return values**, and **thrown exceptions** for public methods.

For private and package-private elements, documentation is optional but encouraged for complex logic.

### Be Precise About Nullability

Always specify whether parameters or return values can be `null`:

```java
/**
 * Finds a user by their unique identifier.
 *
 * @param userId the unique identifier of the user, must not be {@code null}
 * @return the user with the given ID, or {@code null} if no such user exists
 */
public User findById(String userId) {
    // ...
}
```

### Use `{@code}` for Technical Terms

Wrap class names, method names, `null`, `true`, `false`, and other code references in `{@code}`:

```java
/**
 * Returns {@code true} if this collection contains no elements.
 * Equivalent to checking whether {@code size() == 0}.
 */
```

### Common Mistakes to Avoid

1. **Restating the method name**: `/** Gets the name. */ public String getName()` -- this adds no value. Instead, explain what "name" means in context.
2. **Missing `@param` tags**: Every parameter should be documented.
3. **Forgetting `@throws`**: If a method can throw an exception, document it.
4. **Stale documentation**: When you change code, update the documentation to match.
5. **No description, only tags**: Always write at least one sentence of description before the tags.

---

## 5.7 Generating Documentation with Doxygen

### What Is Doxygen?

**Doxygen** is a widely used documentation generator that supports multiple programming languages including Java, C, C++, Python, C#, and more. While Java has its own built-in `javadoc` tool, Doxygen offers several advantages:

- **Multi-language support**: A single tool for projects that mix languages.
- **Additional output formats**: HTML, LaTeX (PDF), RTF, man pages, XML.
- **Diagram generation**: Class diagrams, collaboration diagrams, and call graphs (with Graphviz).
- **Cross-referencing**: Automatic linking between classes, methods, and files.
- **Flexible configuration**: Hundreds of options via a configuration file.

### Installing Doxygen

1. Visit the Doxygen download page: [https://www.doxygen.nl/download.html](https://www.doxygen.nl/download.html)
2. Download the installer for your operating system.
3. Run the installer and follow the prompts.
4. Verify the installation:

```bash
doxygen --version
```

Optionally, install **Graphviz** for diagram generation:
- Download from [https://graphviz.org/download/](https://graphviz.org/download/)
- Doxygen uses Graphviz's `dot` tool to generate class hierarchy and dependency diagrams.

### Generating a Configuration File

Doxygen is driven by a configuration file (usually named `Doxyfile`). Generate a default one:

```bash
cd /path/to/your/project
doxygen -g
```

This creates a `Doxyfile` with default settings and extensive comments explaining each option.

### Key Configuration Options

Open the `Doxyfile` in a text editor and adjust these important settings:

```
# Project information
PROJECT_NAME           = "Logger"
PROJECT_NUMBER         = "2.0"
PROJECT_BRIEF          = "A simple logging utility for Java applications"

# Input settings
INPUT                  = src/
RECURSIVE              = YES
FILE_PATTERNS          = *.java

# Output settings
OUTPUT_DIRECTORY       = docs
GENERATE_HTML          = YES
GENERATE_LATEX         = NO

# JavaDoc compatibility
JAVADOC_AUTOBRIEF      = YES
OPTIMIZE_OUTPUT_JAVA   = YES

# Diagram generation (requires Graphviz)
HAVE_DOT               = YES
CLASS_DIAGRAMS         = YES
CLASS_GRAPH            = YES
COLLABORATION_GRAPH    = YES
```

### Running Doxygen

Once your `Doxyfile` is configured, generate the documentation:

```bash
doxygen Doxyfile
```

Doxygen reads your source files, extracts the documentation comments, and generates HTML pages in the `docs/html/` directory. Open `docs/html/index.html` in a web browser to view the result.

### Understanding the Output

The generated documentation typically includes:

- **Main page**: An overview with the project name and description.
- **Class list**: All documented classes with summaries.
- **Class detail pages**: Full documentation for each class, including:
  - Class description
  - Inheritance hierarchy
  - Member variable documentation
  - Method documentation with parameters, return values, and exceptions
  - Source code cross-references
- **File list**: Documentation organized by source file.
- **Search functionality**: A search box for finding classes, methods, and terms.

### Alternative: Using the Built-In `javadoc` Tool

If you prefer to use Java's built-in documentation generator instead of Doxygen:

```bash
javadoc -d docs/api -sourcepath src -subpackages com.myproject
```

This generates standard Java API documentation pages (the same format as the official Java API docs).

Key `javadoc` options:

| Option | Description |
|---|---|
| `-d <directory>` | Output directory for generated pages |
| `-sourcepath <path>` | Root directory of source files |
| `-subpackages <pkg>` | Process all packages recursively |
| `-author` | Include `@author` tags in output |
| `-version` | Include `@version` tags in output |
| `-private` | Include private members in documentation |
| `-windowtitle "title"` | Set the browser window title |

---

## 5.8 Hosting Documentation on GitHub Pages

### What Is GitHub Pages?

**GitHub Pages** is a free static website hosting service provided by GitHub. It serves HTML, CSS, and JavaScript files directly from a repository. While it can be used for any static website, its most common use case is hosting **project documentation**.

Each GitHub repository can have its own GitHub Pages site, accessible at a URL like:

```
https://yourusername.github.io/your-repository-name/
```

### Setting Up GitHub Pages

There are several ways to configure GitHub Pages. The simplest approach for documentation is to use the `docs/` folder on the `main` branch.

#### Step 1: Generate Documentation into `docs/`

Configure Doxygen (or `javadoc`) to output into a `docs/` directory at the root of your repository:

In your `Doxyfile`:
```
OUTPUT_DIRECTORY       = docs
```

Or with `javadoc`:
```bash
javadoc -d docs -sourcepath src -subpackages com.myproject
```

#### Step 2: Commit the Documentation

```bash
git add docs/
git commit -m "Add generated API documentation"
git push origin main
```

#### Step 3: Enable GitHub Pages

1. Go to your repository on GitHub.
2. Click **Settings** (gear icon at the top).
3. In the left sidebar, click **Pages**.
4. Under **Source**, select:
   - Branch: `main`
   - Folder: `/docs`
5. Click **Save**.

#### Step 4: Access Your Documentation

After a few minutes, your documentation will be available at:

```
https://yourusername.github.io/your-repository-name/
```

If you used Doxygen and the HTML is inside `docs/html/`, the URL might be:

```
https://yourusername.github.io/your-repository-name/html/
```

To fix this, you can either configure Doxygen to output directly into `docs/` (without the `html/` subdirectory) by setting:

```
HTML_OUTPUT            = .
```

Or move the contents after generation:

```bash
# After running doxygen
cp -r docs/html/* docs/
```

### Updating Documentation

Whenever you update your code and documentation comments:

1. Re-run Doxygen to regenerate the HTML files.
2. Commit the updated `docs/` directory.
3. Push to GitHub.
4. GitHub Pages automatically serves the updated files.

```bash
# Regenerate documentation
doxygen Doxyfile

# Commit updated docs
git add docs/
git commit -m "Update API documentation"
git push origin main
```

### GitHub Pages Best Practices

- **Automate generation**: Consider using GitHub Actions to automatically regenerate documentation whenever code is pushed.
- **Keep docs in sync**: Always regenerate documentation when you change public APIs.
- **Add a link in README**: Include a link to your hosted documentation in your `README.md`:

```markdown
## Documentation

Full API documentation is available at:
https://yourusername.github.io/your-repository-name/
```

- **Use a custom domain** (optional): GitHub Pages supports custom domains if you want a more professional URL.

---

## 5.9 Documentation Workflow Summary

Here is the complete workflow from writing code to publishing documentation:

```
1. Write Java code with JavaDoc comments
            |
            v
2. Run Doxygen (or javadoc) to generate HTML
            |
            v
3. Commit generated docs/ to your Git repository
            |
            v
4. Push to GitHub
            |
            v
5. Enable GitHub Pages (Settings > Pages > /docs)
            |
            v
6. Documentation is live at yourusername.github.io/repo/
```

### Project Structure Example

```
my-project/
├── .git/
├── .gitignore
├── Doxyfile                    # Doxygen configuration
├── README.md
├── src/
│   ├── Logger.java             # Source code with JavaDoc comments
│   ├── LogLevel.java
│   └── Main.java
└── docs/                       # Generated documentation (committed to Git)
    ├── index.html
    ├── classLogger.html
    ├── classLogLevel.html
    ├── search/
    └── ...
```

---

## Summary and Key Takeaways

1. **Documentation comments** are structured comments (starting with `/**`) that describe classes, methods, parameters, return values, and exceptions. They are distinct from regular comments, which explain implementation details.

2. **JavaDoc** is the standard documentation format for Java. Its core tags are:
   - `@param` -- describe method parameters
   - `@return` -- describe the return value
   - `@throws` -- describe exceptions
   - `@author`, `@version`, `@since` -- metadata about the element
   - `@see` -- cross-references
   - `@deprecated` -- mark obsolete elements
   - `{@code}` and `{@link}` -- inline formatting

3. **Write documentation for users**, not implementers. Focus on what a method does, its parameters, return value, and failure conditions -- not how it works internally.

4. **The first sentence** of a JavaDoc comment is the summary. Make it clear and self-contained.

5. **Doxygen** is a universal documentation generator that supports Java (and many other languages). Configure it via a `Doxyfile` and run it to produce HTML documentation.

6. **GitHub Pages** provides free static hosting for documentation. Configure it to serve from the `docs/` folder on your `main` branch, and your API documentation becomes publicly accessible.

7. **Keep documentation in sync with code**. Outdated documentation is worse than no documentation because it actively misleads readers.

---

## Homework / Assignments

### Assignment 1: Write JavaDoc Comments

Take your Logger project (or another Java project) and add comprehensive JavaDoc comments to:

1. The class declaration (include `@author`, `@version`, `@since`, and a description).
2. All public constructors (document all parameters with `@param` and any exceptions with `@throws`).
3. All public methods (document parameters, return values, and exceptions).
4. All public fields and constants.
5. At least one method should include a usage example in the description using `<pre>{@code ... }</pre>`.

### Assignment 2: Generate Documentation with Doxygen

1. Install Doxygen on your machine.
2. Generate a `Doxyfile` in your project root.
3. Configure the `Doxyfile` with your project name, input directory, and output settings.
4. Run Doxygen and verify the generated HTML documentation opens correctly in a browser.
5. Review the output and fix any missing or incomplete documentation in your source code.

### Assignment 3: Publish Documentation on GitHub Pages

1. Move or configure the generated documentation to be in the `docs/` directory of your repository.
2. Commit the `docs/` directory and push to GitHub.
3. Enable GitHub Pages in your repository settings (Settings > Pages > Source: `main` branch, `/docs` folder).
4. Wait a few minutes, then verify your documentation is accessible at `https://yourusername.github.io/your-repository-name/`.
5. Share the URL as your submission.

### Bonus: Improve Documentation Quality

Review the official Java API documentation (for example, the `java.util.ArrayList` class) and note the level of detail in the descriptions, the consistency of the formatting, and the use of tags. Then review your own documentation and improve it to match that level of quality.

---

## Additional Resources

- [JavaDoc Tool Reference (Oracle)](https://docs.oracle.com/en/java/javase/21/javadoc/javadoc.html)
- [How to Write Doc Comments for JavaDoc (Oracle)](https://www.oracle.com/technical-resources/articles/java/javadoc-tool.html)
- [Doxygen Manual](https://www.doxygen.nl/manual/index.html)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Java 21 API Documentation](https://docs.oracle.com/en/java/javase/21/docs/api/) -- an excellent example of generated JavaDoc documentation
