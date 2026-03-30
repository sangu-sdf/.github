# Chapter 4: Git & GitHub Part 2 -- Branching, Merging, and Collaboration

## Introduction

In Chapter 3, you learned the fundamentals of Git: creating repositories, staging and committing changes, ignoring files, and synchronizing with a remote on GitHub. Those skills are sufficient for a single developer working on a single line of development, but real-world projects demand more.

What happens when you need to work on a new feature without disrupting the stable version of your code? What if two developers change the same file at the same time? How do teams review each other's code before integrating it?

This chapter introduces **branching** -- Git's mechanism for maintaining parallel lines of development -- along with **merging** and **rebasing** for combining work, **conflict resolution** for handling overlapping changes, **tagging** for marking important milestones, and **pull requests** for collaborative code review on GitHub.

---

## 4.1 Understanding Branches

### What Is a Branch?

A **branch** in Git is a lightweight, movable pointer to a specific commit. When you create a new branch, Git simply creates a new pointer -- it does not copy any files. This makes branching in Git extremely fast and inexpensive.

By default, every Git repository starts with a single branch called **`main`** (historically called `master`). As you make commits, the `main` branch pointer automatically advances to the latest commit.

### Why Use Branches?

Branches let you:

- **Develop features in isolation**: Work on a new feature without affecting the stable `main` branch.
- **Fix bugs safely**: Create a branch for a bug fix, test it, and merge it back only when it is ready.
- **Experiment freely**: Try out ideas on a branch. If the experiment fails, simply delete the branch.
- **Collaborate without conflict**: Each developer works on their own branch, merging into `main` only when their work is complete and reviewed.

### Visualizing Branches

Consider a repository with three commits on `main`. When you create a new branch called `fixBug` and make commits on it, the history looks like this:

```
          C4 --- C5 --- C6   (fixBug)
         /
C1 --- C2 --- C3             (main)
```

- `main` points to commit `C3`.
- `fixBug` was created from `C2` and has its own commits `C4`, `C5`, and `C6`.
- The two branches have **diverged** -- each has commits the other does not.

### The HEAD Pointer

Git uses a special pointer called **`HEAD`** to know which branch you are currently on. When you switch branches, `HEAD` moves to point to the new branch, and Git updates your working directory to reflect the files at that branch's latest commit.

```
          C4 --- C5 --- C6   (fixBug) <-- HEAD
         /
C1 --- C2 --- C3             (main)
```

In this diagram, `HEAD` points to `fixBug`, meaning you are working on the `fixBug` branch.

---

## 4.2 Branch Commands

### Listing Branches

To see all local branches:

```bash
git branch
```

Output:

```
  fixBug
* main
```

The `*` indicates the currently checked-out branch. Additional listing options:

```bash
# List all branches (local and remote)
git branch -a

# List only remote branches
git branch -r
```

### Creating a Branch

To create a new branch (without switching to it):

```bash
git branch feature/add-logging
```

This creates a new branch pointer at the current commit. The naming convention `feature/...` is a common practice for organizing branches by purpose.

### Switching Branches with `git checkout`

To switch to an existing branch:

```bash
git checkout feature/add-logging
```

Git will update your working directory to match the state of that branch. `HEAD` now points to `feature/add-logging`.

> **Note**: `git switch <branchname>` is a newer command that does the same thing as `git checkout <branchname>`. It was introduced to reduce the overloaded nature of `checkout`. Both commands work identically for switching branches.

### Create and Switch in One Step

To create a new branch and immediately switch to it:

```bash
git checkout -b bugfix/null-pointer
```

This is equivalent to:

```bash
git branch bugfix/null-pointer
git checkout bugfix/null-pointer
```

### Deleting a Branch

After a branch has been merged and is no longer needed:

```bash
git branch -d feature/add-logging
```

The `-d` flag performs a **safe delete** -- Git will refuse to delete the branch if it contains unmerged changes. To force-delete a branch regardless:

```bash
git branch -D feature/add-logging
```

> **Warning**: Force-deleting a branch with unmerged commits means those commits may become unreachable and eventually garbage-collected. Use with caution.

### Viewing All Branch History

To see the commit history across all branches:

```bash
git log --all --oneline --graph
```

This produces a visual ASCII graph showing how branches diverge and merge:

```
* a1b2c3d (HEAD -> main) Merge branch 'feature/add-logging'
|\
| * d4e5f6g (feature/add-logging) Add file logging capability
| * e5f6g7h Add console logging
|/
* b2c3d4e Add Calculator class
* c3d4e5f Initial commit
```

---

## 4.3 Branch Naming Conventions

While Git allows almost any branch name, teams typically adopt naming conventions to keep things organized:

| Prefix | Purpose | Example |
|---|---|---|
| `feature/` | New features | `feature/user-authentication` |
| `bugfix/` or `fix/` | Bug fixes | `bugfix/null-pointer-exception` |
| `hotfix/` | Urgent production fixes | `hotfix/security-patch` |
| `release/` | Release preparation | `release/v2.0` |
| `refactor/` | Code refactoring | `refactor/extract-service-layer` |
| `test/` | Adding or modifying tests | `test/calculator-unit-tests` |

**Best practices for branch names:**

- Use lowercase letters with hyphens as separators.
- Be descriptive but concise.
- Include a ticket or issue number if your team uses an issue tracker (e.g., `feature/PROJ-123-add-login`).

---

## 4.4 Practical Branching Example

Let us walk through a realistic branching workflow for a Java project:

```bash
# Start on the main branch
git checkout main

# Create and switch to a feature branch
git checkout -b feature/add-logging-levels

# Edit your Java file to add logging levels
# (Imagine you add INFO, WARNING, ERROR levels to your Logger class)

# Stage and commit your changes
git add src/Logger.java
git commit -m "Add INFO, WARNING, and ERROR logging levels"

# Make another change
git add src/Logger.java
git commit -m "Add log level filtering to Logger"

# Switch back to main
git checkout main

# Your working directory now reflects the main branch --
# the logging level changes are NOT visible here
```

At this point, your history looks like this:

```
          D --- E   (feature/add-logging-levels)
         /
A --- B --- C       (main) <-- HEAD
```

---

## 4.5 Resolving Code Conflicts

### What Is a Conflict?

A **conflict** occurs when two branches modify the **same lines** of the **same file** in different ways. Git cannot automatically determine which change is correct, so it asks you to resolve the conflict manually.

Conflicts typically happen when:

- Two developers edit the same function on different branches.
- One branch deletes a file while another modifies it.
- Two branches add different content at the same location in a file.

### How Git Marks Conflicts

When a merge encounters a conflict, Git modifies the affected file to show both versions:

```java
public class Calculator {
<<<<<<< HEAD
    // Version from the current branch (main)
    public double divide(int a, int b) {
        return (double) a / b;
    }
=======
    // Version from the branch being merged (bugfix/division)
    public double divide(int a, int b) {
        if (b == 0) {
            throw new ArithmeticException("Cannot divide by zero");
        }
        return (double) a / b;
    }
>>>>>>> bugfix/division
}
```

The conflict markers mean:

- `<<<<<<< HEAD` -- start of the current branch's version.
- `=======` -- separator between the two versions.
- `>>>>>>> bugfix/division` -- end of the incoming branch's version.

### Resolving the Conflict

To resolve the conflict:

1. **Open the file** in your editor.
2. **Decide** which version to keep (or combine both versions).
3. **Remove the conflict markers** (`<<<<<<<`, `=======`, `>>>>>>>`).
4. **Save** the file.
5. **Stage** the resolved file.
6. **Commit** to complete the merge.

After resolution, the file might look like:

```java
public class Calculator {
    public double divide(int a, int b) {
        if (b == 0) {
            throw new ArithmeticException("Cannot divide by zero");
        }
        return (double) a / b;
    }
}
```

Then:

```bash
git add src/Calculator.java
git commit -m "Resolve merge conflict: keep division-by-zero check"
```

### Tips for Avoiding Conflicts

- **Pull frequently**: Keep your branch up to date with `main` to minimize divergence.
- **Keep branches short-lived**: The longer a branch lives, the more it diverges and the more conflicts you will face.
- **Communicate with your team**: If two people need to work on the same file, coordinate to avoid overlapping changes.
- **Make small, focused commits**: Smaller changes are easier to merge and produce fewer conflicts.

---

## 4.6 Merging Branches

### What Is Merging?

**Merging** is the most straightforward way to combine the work from two branches. When you merge branch B into branch A, Git creates a new **merge commit** on branch A that incorporates all changes from branch B.

### The Merge Process

The key rule to remember: **You must be on the branch that you want to merge INTO.**

If you want to merge `bugfix/division` into `main`, you must first check out `main`:

```bash
# Step 1: Switch to the target branch
git checkout main

# Step 2: Merge the source branch into the current branch
git merge bugfix/division
```

### Fast-Forward Merge

If `main` has not received any new commits since the branch was created, Git can simply move the `main` pointer forward. This is called a **fast-forward merge**:

Before:
```
A --- B --- C          (main) <-- HEAD
              \
               D --- E (bugfix/division)
```

After `git merge bugfix/division`:
```
A --- B --- C --- D --- E   (main) <-- HEAD
                            (bugfix/division)
```

No merge commit is created because there is no divergence to reconcile.

### Three-Way Merge

If both branches have new commits since they diverged, Git performs a **three-way merge** and creates a merge commit:

Before:
```
          D --- E         (bugfix/division)
         /
A --- B --- C --- F       (main) <-- HEAD
```

After `git merge bugfix/division`:
```
          D --- E
         /       \
A --- B --- C --- F --- G   (main) <-- HEAD
```

Commit `G` is the merge commit. It has **two parents**: `F` (from `main`) and `E` (from `bugfix/division`).

### Step-by-Step Merge Example

```bash
# 1. Create and switch to a feature branch
git checkout -b feature/add-logging

# 2. Make changes and commit
git add src/Logger.java
git commit -m "Add basic logging functionality"

git add src/Logger.java
git commit -m "Add timestamp to log messages"

# 3. Switch back to main
git checkout main

# 4. Optionally, make another commit on main
git add src/Main.java
git commit -m "Update Main to use new configuration"

# 5. Merge the feature branch into main
git merge feature/add-logging

# 6. If there are no conflicts, Git creates a merge commit automatically
# 7. If there ARE conflicts, resolve them, stage, and commit

# 8. Optionally, delete the feature branch
git branch -d feature/add-logging
```

---

## 4.7 Rebasing

### What Is Rebasing?

**Rebasing** is an alternative to merging for integrating changes from one branch into another. Instead of creating a merge commit, rebasing **replays** the commits from your branch on top of the target branch, producing a clean, linear history.

### How Rebase Works

Before rebase:
```
          D --- E         (bugFix)  <-- HEAD
         /
A --- B --- C --- F       (main)
```

After `git rebase main` (while on `bugFix`):
```
                          D' --- E'   (bugFix)  <-- HEAD
                         /
A --- B --- C --- F               (main)
```

Git takes commits `D` and `E` from `bugFix`, creates **copies** (`D'` and `E'`) with new hashes, and places them after commit `F` (the tip of `main`). The original commits `D` and `E` still exist temporarily but are no longer referenced by any branch.

### The Rebase Process

The key rule for rebasing: **You must be on the branch you want to rebase.**

If you want to rebase `bugFix` onto `main`:

```bash
# Step 1: Switch to the branch you want to rebase
git checkout bugFix

# Step 2: Rebase onto the target branch
git rebase main
```

After rebasing, you typically want to update `main` to point to the new tip:

```bash
# Step 3: Switch to main
git checkout main

# Step 4: Fast-forward main to include the rebased commits
git merge bugFix
```

Since `bugFix` is now ahead of `main` in a straight line, this merge is a fast-forward.

### Step-by-Step Rebase Example

```bash
# 1. Create a feature branch and make commits
git checkout -b bugFix
git add src/Calculator.java
git commit -m "Fix rounding error in divide method"

# 2. Switch to main and make a commit there too
git checkout main
git add src/Main.java
git commit -m "Add user input handling"

# 3. Switch back to bugFix and rebase onto main
git checkout bugFix
git rebase main

# 4. Now bugFix's commits sit on top of main's latest commit
# 5. Switch to main and fast-forward merge
git checkout main
git merge bugFix

# 6. Clean up
git branch -d bugFix
```

---

## 4.8 Merge vs. Rebase -- When to Use Which

Both merge and rebase integrate changes from one branch into another, but they produce different histories:

### Merge

```
          D --- E
         /       \
A --- B --- C --- F --- G   (merge commit)
```

**Pros:**
- Preserves the complete history, including when branches were created and merged.
- Non-destructive -- existing commits are never changed.
- Simple and safe.

**Cons:**
- Creates merge commits that can clutter the history, especially in active projects.
- The history graph can become complex with many branches.

### Rebase

```
A --- B --- C --- F --- D' --- E'   (linear history)
```

**Pros:**
- Produces a clean, linear history that is easy to read.
- No extra merge commits.
- Makes `git log` and `git bisect` easier to use.

**Cons:**
- Rewrites commit history (creates new commit hashes).
- Can be dangerous if used on shared branches (other developers may have based work on the original commits).
- Conflicts must be resolved commit-by-commit during rebase.

### The Golden Rule of Rebasing

> **Never rebase commits that have been pushed to a shared remote branch.**

If other developers have based their work on commits that you then rebase (and thus replace with new commits), their history will diverge from yours, leading to confusion and potential data loss.

**Safe to rebase**: Your local feature branch that only you work on, before pushing.

**Unsafe to rebase**: The `main` branch, or any branch that others have pulled.

### Quick Decision Guide

| Situation | Recommended |
|---|---|
| Integrating a feature branch into `main` | **Merge** (via pull request) |
| Updating your feature branch with latest `main` | **Rebase** (keeps your branch clean) |
| Shared branch with multiple contributors | **Merge** (never rebase shared branches) |
| Personal branch before opening a pull request | **Rebase** (for clean history) |

---

## 4.9 Tagging Commits

### What Are Tags?

A **tag** is a named reference to a specific commit. Unlike branches, tags do not move -- they permanently mark a specific point in history. Tags are commonly used to mark **release versions** (e.g., `v1.0`, `v2.3.1`).

### Why Use Tags?

- **Mark releases**: When you ship version 1.0 of your software, tag the commit so you can always find it.
- **Reference important milestones**: Mark commits where significant changes occurred.
- **Simplify deployment**: Many CI/CD systems trigger deployments when a tag is pushed.

### Creating Tags

**Lightweight tags** are simple pointers to a commit (similar to a branch that does not move):

```bash
# Tag the current commit
git tag v1.0

# Tag a specific commit by its hash
git tag v0.9 a1b2c3d
```

**Annotated tags** store additional information (tagger name, email, date, and a message). These are recommended for releases:

```bash
git tag -a v1.0 -m "Release version 1.0 with basic calculator features"
```

### Listing Tags

```bash
# List all tags
git tag

# List tags matching a pattern
git tag -l "v1.*"
```

### Viewing Tag Details

```bash
# Show details of an annotated tag
git show v1.0
```

### Pushing Tags to Remote

By default, `git push` does not transfer tags. You must push them explicitly:

```bash
# Push a specific tag
git push origin v1.0

# Push all tags
git push origin --tags
```

### Deleting Tags

```bash
# Delete a local tag
git tag -d v1.0

# Delete a remote tag
git push origin --delete v1.0
```

### Semantic Versioning

When tagging releases, the most widely adopted convention is **Semantic Versioning (SemVer)**: `vMAJOR.MINOR.PATCH`

- **MAJOR**: Incremented for incompatible API changes (e.g., `v1.0.0` to `v2.0.0`).
- **MINOR**: Incremented for new features that are backward-compatible (e.g., `v1.0.0` to `v1.1.0`).
- **PATCH**: Incremented for backward-compatible bug fixes (e.g., `v1.0.0` to `v1.0.1`).

Example progression: `v1.0.0` -> `v1.0.1` -> `v1.1.0` -> `v1.2.0` -> `v2.0.0`

---

## 4.10 Creating Pull Requests on GitHub

### What Is a Pull Request?

A **Pull Request** (PR) is a GitHub feature (not a Git feature) that lets you propose changes from one branch to another. It provides:

- A **visual diff** of all changes.
- A **discussion thread** for code review.
- **Automated checks** (tests, linting, etc.) if configured.
- A **merge button** to integrate the changes when approved.

Pull requests are the primary mechanism for code review and collaboration on GitHub.

### Pull Request for Your Own Repository

When working on a feature in your own repository:

1. **Create a feature branch** and make your commits:

   ```bash
   git checkout -b feature/add-logging-levels
   git add src/Logger.java
   git commit -m "Add INFO, WARNING, and ERROR logging levels"
   git push -u origin feature/add-logging-levels
   ```

2. **Open a pull request on GitHub**:
   - Go to your repository on GitHub.
   - You will often see a banner saying "feature/add-logging-levels had recent pushes" with a **Compare & pull request** button. Click it.
   - Alternatively, click the **Pull requests** tab and then **New pull request**.
   - Select the **base branch** (usually `main`) and the **compare branch** (your feature branch).
   - Write a descriptive title and description explaining what the changes do and why.
   - Click **Create pull request**.

3. **Review and merge**:
   - Review the diff to verify the changes.
   - If satisfied, click **Merge pull request** and then **Confirm merge**.
   - Optionally, delete the feature branch after merging.

### Pull Request for Another Person's Repository (Contributing to Open Source)

When you want to contribute to a repository you do not own:

1. **Fork** the repository -- this creates a copy under your GitHub account.
2. **Clone** your fork locally:

   ```bash
   git clone https://github.com/yourusername/forked-repo.git
   ```

3. **Create a branch**, make changes, and push to your fork:

   ```bash
   git checkout -b fix/typo-in-readme
   # Make changes...
   git add README.md
   git commit -m "Fix typo in README installation instructions"
   git push -u origin fix/typo-in-readme
   ```

4. **Open a pull request** from your fork's branch to the original repository's `main` branch.
5. The repository maintainers will review your PR, possibly request changes, and eventually merge it (or close it with feedback).

### Writing Good Pull Request Descriptions

A well-written PR description helps reviewers understand your changes:

```markdown
## Summary
Add INFO, WARNING, and ERROR logging levels to the Logger class.

## Changes
- Added LogLevel enum with INFO, WARNING, and ERROR values
- Modified Logger.log() to accept a LogLevel parameter
- Added filtering: log messages below the configured level are suppressed
- Updated Main.java to demonstrate the new logging levels

## Testing
- Tested all three logging levels with console output
- Verified that filtering works correctly (setting level to WARNING suppresses INFO messages)

## Related Issues
Closes #12
```

### Pull Request Best Practices

- **Keep PRs small and focused**: A PR that changes 50 lines is easy to review. A PR that changes 5,000 lines is not.
- **One PR per feature or fix**: Do not bundle unrelated changes.
- **Write a clear description**: Explain what you changed, why, and how to test it.
- **Respond to review feedback**: Address comments, make changes, and push updated commits.
- **Do not merge your own PR** without review (in a team setting).

---

## 4.11 Command Reference

| Command | Description |
|---|---|
| `git branch` | List all local branches |
| `git branch <name>` | Create a new branch |
| `git branch -d <name>` | Delete a branch (safe) |
| `git branch -D <name>` | Force-delete a branch |
| `git branch -a` | List all local and remote branches |
| `git branch -r` | List remote branches |
| `git checkout <branch>` | Switch to a branch |
| `git checkout -b <branch>` | Create and switch to a new branch |
| `git switch <branch>` | Switch to a branch (modern alternative) |
| `git merge <branch>` | Merge a branch into the current branch |
| `git rebase <branch>` | Rebase the current branch onto another |
| `git tag` | List all tags |
| `git tag <name>` | Create a lightweight tag at the current commit |
| `git tag -a <name> -m "msg"` | Create an annotated tag |
| `git tag <name> <commit>` | Tag a specific commit |
| `git push origin --tags` | Push all tags to remote |
| `git log --all --oneline --graph` | Show commit graph of all branches |

---

## Summary and Key Takeaways

1. **Branches** are lightweight pointers to commits. They let you work on features, fixes, and experiments in isolation without affecting the stable `main` branch.
2. **Create branches** with `git branch` or `git checkout -b`. **Switch** with `git checkout` or `git switch`. **Delete** with `git branch -d`.
3. **Conflicts** occur when two branches modify the same lines in the same file. Git marks conflicts with `<<<<<<<`, `=======`, and `>>>>>>>` markers that you must resolve manually.
4. **Merging** creates a merge commit that combines two branches. You must be on the **target branch** (the one you want to merge into).
5. **Rebasing** replays your commits on top of another branch, producing a linear history. You must be on the **branch being rebased**. Never rebase shared branches.
6. **Tags** permanently mark specific commits, typically used for release versions. Use semantic versioning (`vMAJOR.MINOR.PATCH`).
7. **Pull requests** on GitHub enable code review and controlled merging. They are the standard workflow for team collaboration and open-source contributions.
8. Use **descriptive branch names** with prefixes like `feature/`, `bugfix/`, and `hotfix/` to keep your repository organized.

---

## Homework / Assignments

### Assignment 1: Branching and Merging Practice

Using your Logger project repository (or another Java project):

1. Make sure your `main` branch is up to date with GitHub (`git pull`).
2. Create a new branch called `feature/add-logging-levels`.
3. On this branch, add at least two commits that implement logging levels (e.g., INFO, WARNING, ERROR) in your Logger class.
4. Push the `feature/add-logging-levels` branch to GitHub.
5. Create a **pull request** on GitHub from `feature/add-logging-levels` to `main`.
6. Review the pull request diff on GitHub.
7. Merge the pull request into `main`.
8. **Do not delete** the `feature/add-logging-levels` branch after merging -- leave it for verification.

### Assignment 2: Simulate and Resolve a Conflict

1. Create a new branch called `experiment/conflict-test` from `main`.
2. On this branch, modify a specific method in one of your Java files (change the implementation).
3. Switch back to `main` and modify the **same method** differently.
4. Commit both changes on their respective branches.
5. Try to merge `experiment/conflict-test` into `main`.
6. Resolve the conflict manually, choosing the best version of the code.
7. Commit the resolution and push to GitHub.

### Assignment 3: Tagging a Release

1. Once your Logger project is in a working state, create an annotated tag called `v1.0` with a message describing the release.
2. Push the tag to GitHub.
3. Verify that the tag appears on your GitHub repository's releases page.

### Bonus: Learn Git Branching

Visit [https://learngitbranching.js.org/](https://learngitbranching.js.org/) and complete at least the following exercises:
- Introduction Sequence (all levels)
- Ramping Up (all levels)

Take a screenshot of your completed levels.

---

## Additional Resources

- [Learn Git Branching (interactive)](https://learngitbranching.js.org/) -- the best visual tool for understanding branching, merging, and rebasing
- [Git Branching - Basic Branching and Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging) -- from the Pro Git book
- [GitHub Pull Request Documentation](https://docs.github.com/en/pull-requests)
- [Semantic Versioning Specification](https://semver.org/)
- [Atlassian Git Tutorials - Merge vs. Rebase](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)
