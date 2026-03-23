# Chapter 3: Git & GitHub Part 1 -- Introduction to Git

## Introduction

Software development rarely happens in isolation. Even when you work alone, you constantly make changes to your code -- adding features, fixing bugs, refactoring structures. Without a systematic way to track these changes, you risk losing work, introducing errors you cannot undo, or simply forgetting what you changed and why.

This chapter introduces **Git**, the most widely used version control system in the world, and **GitHub**, the most popular platform for hosting Git repositories online. By the end of this chapter, you will be able to create a local Git repository, track changes to your files, write meaningful commit messages, ignore files that should not be versioned, inspect your project's history, and push your work to a remote repository on GitHub.

---

## 3.1 Version Control Systems

### What Is a Version Control System?

A **Version Control System** (VCS) -- also known as *revision control*, *source control*, or *source code management* -- is software that records changes to files over time. It lets you:

- **Track every modification** made to every file in a project.
- **Revert** to any previous state if something goes wrong.
- **Compare** changes between versions to understand what was modified.
- **Collaborate** with other developers without overwriting each other's work.
- **Maintain parallel lines of development** (branches) for features or experiments.

Although VCS tools are most commonly associated with source code, they can manage any type of file: configuration files, documentation, web assets, design files, and more.

### A Brief History

Version control has evolved through several generations:

| Generation | Model | Examples |
|---|---|---|
| First | Local only, file locking | RCS, SCCS |
| Second | Centralized server | CVS, Subversion (SVN), Perforce |
| Third | Distributed | **Git**, Mercurial, Bazaar |

In **centralized** systems, a single server holds the authoritative copy of the repository. Developers check out files from that server, make changes, and commit back. If the server goes down, no one can commit.

In **distributed** systems like Git, every developer has a complete copy of the entire repository, including its full history. You can commit, branch, and merge entirely offline. The central server (if one exists) is just another copy -- a convenient meeting point, not a single point of failure.

---

## 3.2 What Is Git?

**Git** is a free, open-source, distributed version control system designed for speed, data integrity, and support for distributed, non-linear workflows.

### Origin Story

Git was created in **2005** by **Linus Torvalds**, the creator of the Linux kernel. The Linux kernel project had been using a proprietary distributed VCS called BitKeeper, but when free access to that tool was revoked, Torvalds built Git as a replacement. His goals were:

- **Speed** -- operations should be fast, even on large projects.
- **Simplicity of design** -- the data model should be clean.
- **Strong support for non-linear development** -- thousands of parallel branches.
- **Fully distributed** -- no need for a central server.
- **Capable of handling large projects** efficiently (like the Linux kernel).

Today, Git is the dominant version control system used by individual developers, open-source communities, startups, and large enterprises alike.

### Key Concepts

Before diving into commands, it helps to understand a few core ideas:

- **Repository (repo)**: A directory whose contents are tracked by Git. It contains a hidden `.git` folder with all version control data.
- **Commit**: A snapshot of your project at a point in time. Each commit has a unique identifier (a SHA-1 hash), an author, a timestamp, and a message describing the change.
- **Staging area (index)**: An intermediate area where you prepare changes before committing them. This lets you control exactly which changes go into the next commit.
- **Branch**: A lightweight, movable pointer to a commit. Branches allow parallel lines of development.
- **Working directory**: The files and folders you see and edit on your file system.

### Git Is Not GitHub

It is important to distinguish between **Git** and **GitHub**:

- **Git** is the version control *tool* -- a command-line program that runs on your local machine.
- **GitHub** is a *hosting platform* that provides a web interface, collaboration features, and cloud storage for Git repositories.

You can use Git without GitHub (for example, using GitLab, Bitbucket, or a self-hosted server), and you can browse GitHub without using Git (through the web interface). In practice, most developers use both together.

---

## 3.3 What Is GitHub?

**GitHub** is a cloud-based platform (owned by Microsoft since 2018) that hosts Git repositories and provides a rich set of collaborative tools on top of Git. The name follows the pattern of "[something] + Hub" -- a central place for working with Git projects.

### Key Features of GitHub

- **Repository hosting**: Store your Git repositories in the cloud, making them accessible from anywhere.
- **Pull requests**: Propose changes and review code collaboratively before merging.
- **Issues**: Track bugs, feature requests, and tasks.
- **Actions**: Automate workflows such as testing, building, and deploying code.
- **GitHub Pages**: Host static websites directly from a repository.
- **Wikis**: Create project documentation.
- **Security scanning**: Detect vulnerabilities in your dependencies.
- **Social features**: Follow developers, star repositories, and explore trending projects.

### Free vs. Paid

GitHub offers a generous free tier that includes unlimited public and private repositories, making it an excellent choice for students and open-source projects.

---

## 3.4 Installing Git

### Download and Install

Git is available for all major operating systems. To install it:

1. Visit the official download page: [https://git-scm.com/downloads](https://git-scm.com/downloads)
2. Download the installer for your operating system (Windows, macOS, or Linux).
3. Run the installer. On Windows, you can accept the default options during installation.

### Verify the Installation

After installation, open a terminal (Command Prompt, PowerShell, or Git Bash on Windows; Terminal on macOS/Linux) and run:

```bash
git --version
```

You should see output similar to:

```
git version 2.43.0
```

If you see a version number, Git is installed correctly.

### Initial Configuration

Before you start using Git, you should configure your name and email address. These are embedded in every commit you make:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

The `--global` flag means these settings apply to all repositories on your machine. You can override them for a specific repository by running the same commands without `--global` inside that repository.

To verify your configuration:

```bash
git config --list
```

### Choosing a Default Editor

Git sometimes opens a text editor (for example, when writing a commit message without the `-m` flag). You can set your preferred editor:

```bash
git config --global core.editor "code --wait"    # Visual Studio Code
git config --global core.editor "notepad"         # Notepad (Windows)
git config --global core.editor "nano"            # Nano (Linux/macOS)
```

---

## 3.5 Creating a Local Repository

### Initializing a Repository with `git init`

To start tracking a project with Git, navigate to the project's root directory in your terminal and run:

```bash
cd /path/to/your/project
git init
```

This command creates a hidden `.git` subdirectory inside your project folder. This `.git` directory is the **heart of the repository** -- it contains all of Git's internal data: configuration, object database, references, hooks, and more.

```
my-project/
├── .git/          <-- Git's internal data (DO NOT modify manually!)
├── src/
│   └── Main.java
├── README.md
└── .gitignore
```

> **Warning**: Never manually edit or delete files inside the `.git` directory unless you know exactly what you are doing. Corrupting this directory can make your repository unusable.

### Example: Initializing a Java Project

```bash
mkdir calculator
cd calculator
git init
```

Output:

```
Initialized empty Git repository in /home/user/calculator/.git/
```

At this point, the repository is empty -- Git is aware of the directory but is not yet tracking any files.

---

## 3.6 Checking Repository Status with `git status`

The `git status` command is the single most frequently used Git command. It tells you:

- Which **branch** you are on.
- Which files have been **modified** but not yet staged.
- Which files are **staged** (in the index) and ready to be committed.
- Which files are **untracked** (new files Git does not know about yet).

```bash
git status
```

### Example Output

After creating a new file `Main.java` in the repository:

```
On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        Main.java

nothing added to commit but untracked files present (use "git add" to track)
```

Git is telling you that `Main.java` exists in the working directory but is not being tracked. You need to explicitly tell Git to start tracking it.

### Short Status

For a more concise view, use:

```bash
git status -s
```

Output:

```
?? Main.java
```

The `??` means the file is untracked. Other symbols you will see:

| Symbol | Meaning |
|---|---|
| `??` | Untracked |
| `A` | Staged (added to index) |
| `M` | Modified |
| `D` | Deleted |
| `R` | Renamed |

---

## 3.7 Staging Changes with `git add`

### The Staging Area (Index)

Git uses a two-step process for saving changes:

1. **Stage** the changes (add them to the index).
2. **Commit** the staged changes (save them permanently in the repository).

The staging area acts as a **preparation zone**. It lets you selectively choose which changes to include in the next commit. This is powerful because you might have modified five files but want to commit changes to only two of them.

### Basic Usage

To stage a single file:

```bash
git add Main.java
```

To stage multiple files:

```bash
git add Main.java Calculator.java README.md
```

To stage an entire directory:

```bash
git add src/
```

To stage all modified and new files (use with caution):

```bash
git add .
```

To stage only files that are already tracked (modified or deleted, but not new):

```bash
git add -u
```

### Example Workflow

```bash
# Create a Java file
echo 'public class Main { public static void main(String[] args) { System.out.println("Hello, Git!"); } }' > Main.java

# Check status -- file is untracked
git status

# Stage the file
git add Main.java

# Check status again -- file is now staged
git status
```

After staging, `git status` shows:

```
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   Main.java
```

### Unstaging a File

If you accidentally stage a file, you can remove it from the staging area without deleting the file itself:

```bash
git rm --cached Main.java
```

Or, if the file was previously committed and you want to unstage recent changes:

```bash
git restore --staged Main.java
```

---

## 3.8 Committing Changes with `git commit`

### What Is a Commit?

A **commit** is a permanent snapshot of all staged changes. Each commit records:

- The **changes** themselves (what was added, modified, or deleted).
- The **author** (name and email).
- A **timestamp** (when the commit was created).
- A **commit message** (a human-readable description of the changes).
- A **SHA-1 hash** (a unique 40-character identifier, e.g., `a1b2c3d4e5...`).
- A **parent pointer** (linking to the previous commit, forming a history chain).

### Creating a Commit

The most common way to create a commit is with the `-m` flag, which lets you provide the commit message inline:

```bash
git commit -m "Add Main class with hello world output"
```

Output:

```
[main (root-commit) a1b2c3d] Add Main class with hello world output
 1 file changed, 5 insertions(+)
 create mode 100644 Main.java
```

> **Important**: The commit message is required. Git will not allow you to create a commit without one.

### Writing Good Commit Messages

Commit messages are critical for maintaining a readable project history. Follow these best practices:

1. **Use the imperative mood**: Write "Add feature" not "Added feature" or "Adds feature."
2. **Keep the subject line short**: Aim for 50 characters or fewer.
3. **Capitalize the subject line**: Start with an uppercase letter.
4. **Do not end the subject line with a period**.
5. **Separate the subject from the body with a blank line** (for longer messages).
6. **Explain the "why", not just the "what"**: The code diff already shows what changed; the message should explain the reasoning.

**Good examples:**

```
Add input validation for calculator operations
Fix division by zero error in Calculator.divide()
Refactor logging to use SLF4J instead of System.out
```

**Bad examples:**

```
fixed stuff
update
asdfgh
changes
```

### The Complete Workflow

Here is a typical sequence of commands for tracking changes in a Java project:

```bash
# 1. Make changes to your code (edit files, create new files, etc.)

# 2. Check what has changed
git status

# 3. Stage the changes you want to commit
git add src/Calculator.java
git add src/Main.java

# 4. Commit the staged changes with a descriptive message
git commit -m "Implement basic arithmetic operations in Calculator"

# 5. Verify the commit was created
git log --oneline
```

### Shortcut: Stage and Commit in One Step

If you want to stage all **modified** (but not new) files and commit them in one command:

```bash
git commit -am "Fix typo in Calculator output message"
```

The `-a` flag automatically stages all tracked files that have been modified or deleted. Note that this does **not** include untracked (new) files -- you must `git add` those explicitly first.

---

## 3.9 The `.gitignore` File

### Why Ignore Files?

Not every file in your project directory should be tracked by Git. Common examples of files to exclude:

- **Compiled output**: `.class` files, `target/` or `build/` directories.
- **IDE configuration**: `.idea/` (IntelliJ), `.vscode/`, `.project` (Eclipse).
- **Operating system files**: `.DS_Store` (macOS), `Thumbs.db` (Windows).
- **Dependency caches**: `node_modules/`, `.gradle/`.
- **Sensitive data**: API keys, passwords, `.env` files.
- **Temporary files**: Log files, editor swap files.

Tracking these files clutters your repository, can cause conflicts between team members using different IDEs, and might expose sensitive information.

### Creating a `.gitignore` File

Create a file named exactly `.gitignore` (note the leading dot) in the root of your repository. Each line specifies a pattern for files or directories to ignore:

```gitignore
# Compiled Java class files
*.class

# Build output directories
/target/
/build/
/out/

# IntelliJ IDEA
.idea/
*.iml

# Eclipse
.project
.classpath
.settings/

# VS Code
.vscode/

# Operating system files
.DS_Store
Thumbs.db

# Log files
*.log

# Environment variables (sensitive data)
.env
```

### Pattern Syntax

| Pattern | Meaning |
|---|---|
| `*.class` | Ignore all files ending in `.class` |
| `/build/` | Ignore the `build` directory at the repo root only |
| `build/` | Ignore any directory named `build` anywhere |
| `doc/*.txt` | Ignore `.txt` files in the `doc/` directory (not subdirectories) |
| `doc/**/*.pdf` | Ignore `.pdf` files in `doc/` and all its subdirectories |
| `!important.log` | Do NOT ignore `important.log` (exception to a previous rule) |
| `#` | Comment line |

### Pre-Made `.gitignore` Templates

You do not need to write `.gitignore` files from scratch. GitHub maintains a collection of templates for various languages and IDEs at [github.com/github/gitignore](https://github.com/github/gitignore).

For a Java project, the template includes patterns for `.class` files, build tool output (`Maven`, `Gradle`), IDE files, and more.

Most IDEs (IntelliJ IDEA, Eclipse, VS Code) also auto-generate a `.gitignore` when you create a new Git project.

> **Tip**: The `.gitignore` file itself should be committed to your repository so that all collaborators share the same ignore rules.

### Important Notes

- The file must be named **exactly** `.gitignore` -- any misspelling and Git will not recognize it.
- `.gitignore` only prevents **untracked** files from being added. If a file was already committed before being added to `.gitignore`, Git will continue to track it. To stop tracking it, you must explicitly remove it from the index:

```bash
git rm --cached filename.class
git commit -m "Stop tracking compiled class files"
```

---

## 3.10 Viewing Commit History with `git log`

The `git log` command displays the commit history of the current branch. It shows each commit's hash, author, date, and message.

### Basic Usage

```bash
git log
```

Output:

```
commit a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0 (HEAD -> main)
Author: Your Name <your.email@example.com>
Date:   Mon Sep 15 14:30:00 2025 +0400

    Add input validation for calculator operations

commit b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1
Author: Your Name <your.email@example.com>
Date:   Mon Sep 15 12:00:00 2025 +0400

    Implement basic arithmetic operations in Calculator

commit c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0u1v2
Author: Your Name <your.email@example.com>
Date:   Mon Sep 15 10:00:00 2025 +0400

    Add Main class with hello world output
```

### Useful Formatting Options

```bash
# Compact one-line format
git log --oneline

# Output:
# a1b2c3d Add input validation for calculator operations
# b2c3d4e Implement basic arithmetic operations in Calculator
# c3d4e5f Add Main class with hello world output

# Show a graph of branches
git log --oneline --graph

# Limit the number of commits shown
git log -5

# Show commits by a specific author
git log --author="Your Name"

# Show commits in a date range
git log --since="2025-09-01" --until="2025-09-30"

# Show what files were changed in each commit
git log --stat

# Show the actual changes (diff) in each commit
git log -p

# Show all branches
git log --all --oneline --graph
```

The `--all` flag is particularly useful because by default `git log` only shows commits reachable from the current branch. Using `--all` shows commits from every branch.

---

## 3.11 Remote Repositories

### Local vs. Remote

So far, everything we have done has been **local** -- on your own machine. This is perfectly fine for personal projects, but real-world software development requires collaboration. You need a way to:

- **Share** your code with teammates.
- **Back up** your repository in the cloud.
- **Synchronize** changes made by different developers.

A **remote repository** is a copy of your repository hosted on a server (such as GitHub, GitLab, or Bitbucket). You can push your local commits to the remote and pull other people's commits from it.

### Creating a Repository on GitHub

1. Sign in to [github.com](https://github.com) (create an account if you do not have one).
2. Click the **+** button in the top-right corner and select **New repository**.
3. Fill in the repository details:
   - **Repository name**: e.g., `calculator`
   - **Description**: optional but recommended
   - **Visibility**: Public or Private
   - **Initialize**: You can optionally add a README, `.gitignore`, or license. If you already have a local repo, leave these unchecked.
4. Click **Create repository**.

GitHub will show you instructions for connecting your local repository to the new remote.

### Connecting a Local Repository to GitHub

If you already have a local repository with commits, you can link it to a GitHub remote:

```bash
# Add a remote named "origin" pointing to your GitHub repo
git remote add origin https://github.com/yourusername/calculator.git

# Push your local main branch to the remote
git push -u origin main
```

The `-u` (or `--set-upstream`) flag sets up a tracking relationship so that future `git push` and `git pull` commands know which remote branch to use by default.

### Cloning a Repository with `git clone`

If a repository already exists on GitHub (or any Git server), you can create a local copy using `git clone`:

```bash
git clone https://github.com/yourusername/calculator.git
```

This command:

1. Creates a new directory named `calculator`.
2. Initializes a `.git` directory inside it.
3. Downloads all commits, branches, and tags from the remote.
4. Checks out the default branch (usually `main`).
5. Automatically configures the remote named `origin` to point to the URL you cloned from.

You can also clone into a specific directory:

```bash
git clone https://github.com/yourusername/calculator.git my-calculator
```

### Pushing Changes with `git push`

After making and committing changes locally, you send them to the remote with `git push`:

```bash
git push origin main
```

If the upstream is already set (via `-u`), you can simply run:

```bash
git push
```

Git will upload all new local commits to the remote repository, making them available to other collaborators.

### Pulling Changes with `git pull`

To download and integrate changes that others have pushed to the remote:

```bash
git pull origin main
```

Or, if the upstream is set:

```bash
git pull
```

`git pull` actually performs two operations:

1. `git fetch` -- downloads new commits from the remote.
2. `git merge` -- integrates those commits into your current branch.

> **Best Practice**: Always pull before you push. This ensures your local branch is up to date with the remote and reduces the chance of conflicts.

### Complete Workflow Example

Here is a full example of creating a Java project, initializing Git, and pushing to GitHub:

```bash
# Create project directory and files
mkdir calculator
cd calculator

# Create a Java source file
mkdir -p src
cat > src/Calculator.java << 'EOF'
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

    public double divide(int a, int b) {
        if (b == 0) {
            throw new ArithmeticException("Cannot divide by zero");
        }
        return (double) a / b;
    }
}
EOF

# Initialize Git repository
git init

# Create .gitignore
cat > .gitignore << 'EOF'
*.class
/out/
/build/
.idea/
*.iml
.DS_Store
EOF

# Stage all files
git add src/Calculator.java .gitignore

# Create the first commit
git commit -m "Add Calculator class with basic arithmetic operations"

# Connect to GitHub and push
git remote add origin https://github.com/yourusername/calculator.git
git push -u origin main
```

---

## 3.12 Command Reference

Here is a summary of all Git commands covered in this chapter:

| Command | Description |
|---|---|
| `git init` | Create an empty Git repository or reinitialize an existing one |
| `git clone <url>` | Clone a repository into a new directory |
| `git status` | Show the working tree status |
| `git add <file>` | Add file contents to the staging area (index) |
| `git add -u` | Stage all modified and deleted tracked files |
| `git rm --cached <file>` | Remove a file from the staging area without deleting it |
| `git commit -m "message"` | Record staged changes to the repository |
| `git commit -am "message"` | Stage all tracked changes and commit |
| `git log` | Show commit logs |
| `git log --oneline` | Show compact commit logs |
| `git remote add origin <url>` | Add a remote repository |
| `git push -u origin main` | Push to remote and set upstream |
| `git push` | Push commits to the configured remote |
| `git pull` | Fetch and integrate changes from the remote |
| `git config --global user.name "Name"` | Set your Git username |
| `git config --global user.email "email"` | Set your Git email |

---

## Summary and Key Takeaways

1. **Version control systems** track changes to files over time, enabling collaboration, history tracking, and safe experimentation.
2. **Git** is a distributed VCS created by Linus Torvalds. Every developer has a full copy of the repository, enabling offline work and fast operations.
3. **GitHub** is a cloud platform that hosts Git repositories and adds collaboration tools like pull requests, issues, and actions.
4. The basic Git workflow is: **edit** files, **stage** them with `git add`, and **commit** them with `git commit`.
5. The **staging area** (index) lets you selectively choose which changes go into a commit.
6. **`.gitignore`** prevents non-essential files (compiled output, IDE config, secrets) from being tracked.
7. **`git log`** lets you inspect the history of commits on your branch.
8. **Remote repositories** (on GitHub or similar platforms) enable collaboration. Use `git push` to upload and `git pull` to download changes.
9. **Commit messages matter** -- write clear, imperative-mood messages that explain why a change was made.

---

## Homework / Assignments

### Assignment 1: Initialize and Push a Java Project

1. Create a new Java project on your local machine (you may use the Logger project from previous chapters or create a new one).
2. Initialize it as a Git repository using `git init`.
3. Create a `.gitignore` file appropriate for a Java project (ignore `.class` files, IDE configuration, build output, etc.).
4. Stage and commit both your Java source files and the `.gitignore` file with meaningful commit messages.
5. Create a new repository on your GitHub account.
6. Push your local repository to GitHub.

### Assignment 2: Create a README and Practice Pull

1. In the same repository from Assignment 1, create a `README.md` file locally. Include:
   - The project name
   - A brief description
   - Instructions for how to compile and run the project
2. Stage and commit the `README.md` file. Push it to GitHub.
3. Now, edit the `README.md` file **directly on GitHub** (using the web editor) -- add an extra line like "Last updated: [today's date]".
4. Back in your local terminal, use `git pull` to download the changes made on GitHub.
5. Verify that your local `README.md` now contains the changes you made on GitHub.

### Assignment 3: Practice the Full Workflow

Perform the following sequence and take note of the output at each step:

1. Create a new file `MathUtils.java` with at least two static methods.
2. Run `git status` and observe the output.
3. Stage the file with `git add`.
4. Run `git status` again and compare with step 2.
5. Commit the file.
6. Run `git log --oneline` to see your commit history.
7. Modify `MathUtils.java` (add another method).
8. Run `git diff` to see the unstaged changes.
9. Stage and commit the modification.
10. Push all changes to GitHub.

---

## Additional Resources

- [Official Git Documentation](https://git-scm.com/doc)
- [Learn Git Branching (interactive)](https://learngitbranching.js.org/) -- an excellent visual tool for learning Git concepts
- [GitHub Docs](https://docs.github.com/)
- [Pro Git Book (free)](https://git-scm.com/book/en/v2) -- comprehensive reference for Git
- [GitHub Student Developer Pack](https://education.github.com/pack) -- free tools and resources for students
