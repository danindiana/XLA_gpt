# Git Tutorial with Visual Diagrams

This comprehensive guide explains Git concepts using Mermaid diagrams for clear visualization.

## Table of Contents

1. [Git Workflow Basics](#git-workflow-basics)
2. [Git File States](#git-file-states)
3. [Branching Strategy](#branching-strategy)
4. [Merging Concepts](#merging-concepts)
5. [Rebasing Workflow](#rebasing-workflow)
6. [Remote Operations](#remote-operations)
7. [Git History and Time Travel](#git-history-and-time-travel)
8. [Conflict Resolution](#conflict-resolution)

---

## Git Workflow Basics

The fundamental Git workflow involves three main areas: Working Directory, Staging Area (Index), and Repository.

```mermaid
graph LR
    A[Working Directory] -->|git add| B[Staging Area]
    B -->|git commit| C[Local Repository]
    C -->|git push| D[Remote Repository]
    D -->|git fetch| C
    D -->|git pull| A
    C -->|git checkout| A

    style A fill:#f9f,stroke:#333,stroke-width:2px
    style B fill:#bbf,stroke:#333,stroke-width:2px
    style C fill:#bfb,stroke:#333,stroke-width:2px
    style D fill:#fbb,stroke:#333,stroke-width:2px
```

### Key Commands:
- `git add <file>` - Stage changes
- `git commit -m "message"` - Commit staged changes
- `git push` - Push commits to remote
- `git pull` - Fetch and merge remote changes
- `git checkout <file>` - Discard working directory changes

---

## Git File States

Files in Git can exist in different states throughout their lifecycle.

```mermaid
stateDiagram-v2
    [*] --> Untracked: New file created
    Untracked --> Staged: git add
    Staged --> Untracked: git rm --cached

    Staged --> Committed: git commit
    Committed --> Modified: Edit file
    Modified --> Staged: git add

    Modified --> Committed: git commit -a
    Committed --> [*]: git rm

    note right of Untracked
        File exists but Git
        doesn't track it
    end note

    note right of Staged
        Changes ready to
        be committed
    end note

    note right of Modified
        Tracked file has
        been changed
    end note
```

---

## Branching Strategy

Branching allows parallel development and is essential for collaborative work.

```mermaid
gitGraph
    commit id: "Initial commit"
    commit id: "Add feature foundation"

    branch develop
    checkout develop
    commit id: "Setup dev environment"

    branch feature/user-auth
    checkout feature/user-auth
    commit id: "Add login form"
    commit id: "Add authentication logic"

    checkout develop
    branch feature/dashboard
    commit id: "Create dashboard layout"
    commit id: "Add widgets"

    checkout develop
    merge feature/user-auth tag: "v0.1"

    checkout feature/dashboard
    commit id: "Polish UI"

    checkout develop
    merge feature/dashboard tag: "v0.2"

    checkout main
    merge develop tag: "v1.0"
```

### Common Branch Types:
- **main/master** - Production-ready code
- **develop** - Integration branch for features
- **feature/** - New features or enhancements
- **hotfix/** - Urgent production fixes
- **release/** - Release preparation

---

## Merging Concepts

Merging combines changes from different branches.

```mermaid
graph TD
    subgraph "Fast-Forward Merge"
        A1[main: A] --> B1[main: B]
        B1 --> C1[main: C]
        C1 -.->|git merge feature| D1[main: D]

        A1 --> E1[feature: A]
        E1 --> D1[feature: D]
    end

    subgraph "Three-Way Merge"
        A2[main: A] --> B2[main: B]
        B2 --> C2[main: C]

        A2 --> D2[feature: A]
        D2 --> E2[feature: D]

        C2 --> F2[Merge Commit M]
        E2 --> F2
        F2 -.-> G2[main: M]
    end

    style D1 fill:#9f9
    style F2 fill:#f99
    style G2 fill:#9f9
```

### Merge Types:
- **Fast-Forward**: Linear history, no merge commit needed
- **Three-Way Merge**: Creates merge commit with two parents
- **Squash Merge**: Combines all commits into one

---

## Rebasing Workflow

Rebasing rewrites commit history by moving commits to a new base.

```mermaid
graph TB
    subgraph "Before Rebase"
        A1[A] --> B1[B]
        B1 --> C1[C - main]
        A1 --> D1[D]
        D1 --> E1[E - feature]
    end

    subgraph "After Rebase"
        A2[A] --> B2[B]
        B2 --> C2[C - main]
        C2 --> D2[D']
        D2 --> E2[E' - feature]
    end

    C1 -.->|git rebase main| D2

    style C1 fill:#bbf
    style C2 fill:#bbf
    style E1 fill:#f9f
    style E2 fill:#9f9
```

### Rebase vs Merge:
- **Rebase**: Clean, linear history; rewrites commits
- **Merge**: Preserves history; creates merge commits
- **Interactive Rebase**: Edit, squash, or reorder commits

⚠️ **Warning**: Never rebase public/shared branches!

---

## Remote Operations

Understanding how local and remote repositories interact.

```mermaid
sequenceDiagram
    participant WD as Working Directory
    participant LR as Local Repo
    participant RR as Remote Repo (origin)
    participant UR as Upstream Repo

    WD->>LR: git commit
    LR->>RR: git push origin main

    Note over RR: Other developers push changes

    RR->>LR: git fetch origin
    Note over LR: Updates remote-tracking branches

    LR->>WD: git merge origin/main

    Note over UR: Original project updates
    UR->>LR: git fetch upstream
    LR->>WD: git merge upstream/main

    WD->>LR: git commit
    LR->>RR: git push origin main
    RR->>UR: Pull Request
```

### Key Remote Commands:
- `git clone <url>` - Clone repository
- `git remote add <name> <url>` - Add remote
- `git fetch <remote>` - Download objects and refs
- `git pull <remote> <branch>` - Fetch + merge
- `git push <remote> <branch>` - Upload commits

---

## Git History and Time Travel

Navigate and manipulate Git history safely.

```mermaid
graph LR
    A[A] --> B[B]
    B --> C[C]
    C --> D[D - HEAD, main]

    D -.->|git reset --soft HEAD~2| B
    D -.->|git reset --mixed HEAD~2| B
    D -.->|git reset --hard HEAD~2| B

    D -->|git revert C| E[E: Revert C]

    B -.->|git checkout B| F[Detached HEAD at B]

    style D fill:#9f9
    style B fill:#bbf
    style E fill:#f99
    style F fill:#ff9
```

### Time Travel Commands:
- `git reset --soft <commit>` - Move HEAD, keep staging & working
- `git reset --mixed <commit>` - Move HEAD, keep working (default)
- `git reset --hard <commit>` - Move HEAD, discard all changes
- `git revert <commit>` - Create new commit that undoes changes
- `git checkout <commit>` - Detached HEAD state for exploration

---

## Conflict Resolution

When merging branches with conflicting changes.

```mermaid
flowchart TD
    A[Start Merge/Rebase] --> B{Conflicts?}
    B -->|No| C[Merge Complete ✓]
    B -->|Yes| D[Git Marks Conflicts]

    D --> E[Open Conflicted Files]
    E --> F{Resolution Strategy}

    F -->|Manual| G[Edit Files:<br/>Keep Current/Incoming/Both]
    F -->|Use Ours| H[git checkout --ours file]
    F -->|Use Theirs| I[git checkout --theirs file]
    F -->|Merge Tool| J[git mergetool]

    G --> K[git add resolved files]
    H --> K
    I --> K
    J --> K

    K --> L{More Conflicts?}
    L -->|Yes| E
    L -->|No| M[git commit / git rebase --continue]
    M --> C

    B -.->|Abort| N[git merge --abort<br/>git rebase --abort]
    L -.->|Abort| N

    style C fill:#9f9
    style D fill:#f99
    style K fill:#bbf
    style N fill:#faa
```

### Conflict Markers:
```
<<<<<<< HEAD (Current Change)
Your changes
=======
Their changes
>>>>>>> branch-name (Incoming Change)
```

### Resolution Tools:
- **Manual editing** - Direct file modification
- **git checkout --ours/--theirs** - Choose one version
- **git mergetool** - Visual merge tool
- **VS Code** - Built-in merge conflict resolver

---

## Best Practices

1. **Commit Often**: Small, logical commits are easier to review and revert
2. **Write Clear Messages**: Use conventional commit format
3. **Pull Before Push**: Avoid conflicts by staying updated
4. **Branch for Features**: Keep main/develop stable
5. **Review Before Merging**: Use pull requests for code review
6. **Don't Rewrite Public History**: Avoid force push to shared branches
7. **Use .gitignore**: Don't commit generated files or secrets
8. **Tag Releases**: Mark important points in history

---

## Quick Reference

### Essential Commands Cheat Sheet

| Command | Description |
|---------|-------------|
| `git init` | Initialize new repository |
| `git clone <url>` | Clone remote repository |
| `git status` | Check working directory status |
| `git add <file>` | Stage file changes |
| `git commit -m "msg"` | Commit staged changes |
| `git push` | Push commits to remote |
| `git pull` | Fetch and merge remote changes |
| `git branch <name>` | Create new branch |
| `git checkout <branch>` | Switch to branch |
| `git merge <branch>` | Merge branch into current |
| `git log --oneline --graph` | View commit history |
| `git diff` | Show unstaged changes |
| `git stash` | Temporarily store changes |
| `git stash pop` | Restore stashed changes |

---

## Additional Resources

- [Official Git Documentation](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book/en/v2)
- [GitHub Git Guides](https://github.com/git-guides)
- [Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials)

---

*This tutorial uses Mermaid diagrams for visualization. For best viewing experience, use a Markdown viewer that supports Mermaid rendering (GitHub, GitLab, VS Code with extensions, etc.).*
