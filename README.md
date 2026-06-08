# Claude Code — Complete Reference Guide
> *Based on Net Ninja Tutorial Series*

---

## 1. What is Claude Code?

Claude Code is an **agentic coding tool** built by Anthropic that brings Claude directly into your development workflow. It can read, write, and edit code across your entire codebase, run terminal commands, manage files, and execute multi-step tasks autonomously — all through natural language.

Unlike a basic chat interface, Claude Code understands the full context of your project: its architecture, frameworks, file structure, and conventions. It acts like a senior engineer sitting beside you.

**Key capabilities:**
- Reads and navigates entire codebases
- Writes, edits, and refactors code across multiple files
- Runs shell commands and interprets output
- Creates and manages files and directories
- Commits to Git with meaningful messages
- Executes long, multi-step plans autonomously

---

## 2. Three Ways to Use Claude Code

### 1. Website
Access Claude Code at [claude.ai](https://claude.ai) directly in your browser. Useful for quick questions, planning, and working with pasted code snippets.

### 2. Terminal (Claude Code CLI)
The primary and most powerful way. Install globally and run from any project directory.

```bash
# Install
npm install -g @anthropic-ai/claude-code

# Launch in your project
cd my-project
claude
```

Claude Code reads your entire project in context and operates like a native terminal tool.

### 3. Application (Claude Cowork / IDE Integration)
Claude Code integrates with desktop environments and IDEs. Available as Claude Cowork — a desktop tool for automating file and task management — and through IDE extensions for VS Code and others.

---

## 3. Spec-Driven Workflow

A **spec-driven workflow** means you define *what* you want built before Claude writes a single line of code. This produces higher quality output, fewer rewrites, and a clear paper trail.

### The Flow

```
Idea → Spec (SPEC.md) → Plan (PLAN.md) → Implementation → Review
```

### Step 1: Write the Spec

Create a `SPEC.md` at your project root describing the feature or system:

```markdown
# Feature Spec: User Authentication

## Goal
Add JWT-based authentication to the Express API.

## Requirements
- POST /auth/register — create user, hash password with bcrypt
- POST /auth/login — validate credentials, return signed JWT
- Middleware: protect routes with token verification
- Tokens expire after 7 days

## Out of Scope
- OAuth / social login
- Password reset flow
```

### Step 2: Generate the Plan

Tell Claude Code to read the spec and produce a technical plan:

```
Read SPEC.md and create a detailed implementation plan saved to PLAN.md.
Include file paths, function signatures, and order of operations.
```

### Step 3: Implement

Once the plan exists, instruct Claude to execute it:

```
Follow PLAN.md and implement the authentication feature.
Start with the user model, then routes, then middleware.
```

---

## 4. Custom / Spec Commands

Claude Code supports **custom slash commands** — reusable instructions stored in your project that you can trigger by name.

### Creating a Custom Command

Create a markdown file inside `.claude/commands/`:

```
.claude/
  commands/
    analyze.md
    review.md
    deploy-check.md
```

**Example — `.claude/commands/analyze.md`:**

```markdown
Analyze the codebase and provide a summary of this project outlining:
- Frameworks and libraries in use
- Architecture pattern (MVC, microservices, monolith, etc.)
- Main features and entry points
- Any notable conventions or patterns
- Potential areas for improvement
```

### Using a Custom Command

```bash
/analyze
```

Claude reads the command file and executes those instructions against your current codebase.

---

## 5. Technical Plan

Before implementing any non-trivial feature, ask Claude to produce a plan first. This is the "plan mode" concept — Claude reasons before acting.

### First Prompt Example — Analyze the Codebase

```
Analyze this codebase and provide a summary of the project outlining 
any frameworks, architecture, or main features.
```

**Claude will output something like:**

```
Project: E-commerce API
Framework: Express.js + TypeScript
Database: PostgreSQL via Prisma ORM
Architecture: RESTful MVC
Auth: JWT middleware
Main Features: Products, Orders, Users, Payments (Stripe)
Test Runner: Jest
Notable: All routes versioned under /api/v1
```

This gives Claude (and you) a shared understanding before making changes.

---

## 6. Implementing a Feature — First Prompt Pattern

Use this pattern for clean feature implementation:

```
1. First prompt: Understand
   "Analyze [area of codebase] and explain how it currently works."

2. Second prompt: Plan
   "Based on that, create a plan to add [feature]. Save it to PLAN.md."

3. Third prompt: Implement
   "Implement the plan in PLAN.md. Start with [specific file or layer]."
```

### Example

```bash
# Step 1
Analyze the authentication system in this project and explain the current flow.

# Step 2
Create a plan to add role-based access control (admin, user, guest). Save to PLAN.md.

# Step 3
Implement PLAN.md. Begin with the User model, then the middleware.
```

---

## 7. Resuming Sessions

Claude Code sessions can be paused and resumed without losing context.

### Resume Last Session

```bash
# Full flag
claude --resume

# Short flag
claude -r
```

### Resume a Specific Session

```bash
claude --resume <session-id>
```

### After Resuming — Continue with a Command

```bash
claude -r
# Claude loads previous context, then you can run:
/resume
```

The `/resume` command tells Claude to re-read where it left off and continue the task.

---

## 8. Models

Claude Code supports multiple models. Switch based on the task complexity and speed requirements.

| Model | Best For | Speed | Cost |
|-------|----------|-------|------|
| **Claude Opus** | Complex reasoning, architecture, large refactors | Slower | Higher |
| **Claude Sonnet** | Everyday coding, features, debugging | Balanced | Balanced |
| **Claude Haiku** | Fast edits, simple tasks, autocomplete-style use | Fastest | Lowest |

### Switching Models

```bash
# Set model at launch
claude --model claude-opus-4-6
claude --model claude-sonnet-4-6
claude --model claude-haiku-4-5

# Or use /model command inside a session
/model claude-opus-4-6
```

> **Tip:** Use Opus for planning and architecture; Sonnet for implementation; Haiku for quick edits.

---

## 9. Built-in Commands

### `/doctor`
Checks your Claude Code installation for common issues — missing dependencies, misconfigured paths, version mismatches.

```bash
/doctor
```

**What it checks:**
- Node.js version compatibility
- CLI installation integrity
- API key configuration
- Network connectivity to Anthropic API
- Common dependency issues

---

### `/config`
View and edit Claude Code configuration settings.

```bash
# Open config
/config

# Common settings
/config set model claude-sonnet-4-6
/config set maxTokens 8096
/config set theme dark
```

---

### `/theme`
Switch the terminal UI theme.

```bash
/theme dark
/theme light
/theme system   # Follows OS setting
```

---

### `/init`
Initializes Claude Code in a project. Most importantly, creates a `CLAUDE.md` file — the project memory file.

```bash
/init
```

**What `/init` does:**
- Scans your project structure
- Detects frameworks, scripts, and conventions
- Creates `CLAUDE.md` with a project summary
- Sets up `.claude/` directory for custom commands

---

## 10. CLAUDE.md — Project Memory

`CLAUDE.md` is a special file at your project root that Claude Code reads at the start of every session. It's your **persistent project context**.

### Example `CLAUDE.md`

```markdown
# Project: MyApp API

## Stack
- Node.js + Express + TypeScript
- PostgreSQL + Prisma
- Jest for testing

## Conventions
- All routes live in src/routes/
- Controllers in src/controllers/
- Use async/await, never callbacks
- Run `npm run lint` before committing

## Common Commands
- `npm run dev` — start dev server
- `npm test` — run test suite
- `npm run migrate` — run pending migrations

## Important Notes
- Never commit .env
- API versioned under /api/v1
- Auth token expected as Bearer in Authorization header
```

Claude reads this file automatically — no need to re-explain your project every session.

---

## 11. @filename — Adding Files to Context

Reference specific files inline by prefixing with `@`:

```bash
# Add a single file to context
@src/controllers/userController.ts

# Ask about it
@src/models/User.ts explain the schema and relationships in this model.

# Reference + instruct
@PLAN.md implement step 3 from this plan.
```

**What happens:** Claude selects that file and adds it to the active context window. You'll see a confirmation like:

```
1 file selected: src/models/User.ts
```

Then give your instruction on the same line or the next prompt.

---

## 12. Context Management

### `/context`
View what files and content are currently loaded in Claude's context window.

```bash
/context
```

Shows: active files, token count, session summary.

---

### `/compact`
Summarizes and compresses the current conversation to free up context window space — without losing the important details.

```bash
/compact
```

Use this in long sessions before Claude starts forgetting earlier context.

---

### `/rewind`
Undo the last action or step Claude took. Useful when Claude goes in the wrong direction.

```bash
/rewind
```

Steps back one action. Run multiple times to rewind further.

---

### `/model`
Switch models mid-session.

```bash
/model claude-opus-4-6
/model claude-haiku-4-5
```

---

## 13. Permissions

Claude Code asks for permission before taking destructive or sensitive actions:

- Deleting files
- Running scripts
- Making network requests
- Writing to sensitive directories

### Permission Levels

```bash
# Trust all actions in this session (use carefully)
/permission trust-all

# Trust specific action types
/permission trust-writes
/permission trust-commands

# Review what permissions are granted
/permission list

# Reset to default (ask for everything)
/permission reset
```

> **Best practice:** Use default permissions during exploration; grant broader trust only when you're confident in the plan.

---

## 14. Git — Commit Messages & Settings

Claude Code integrates with Git and can write commit messages, stage files, and manage branches.

### Auto-Generate Commit Messages

```bash
# Ask Claude to commit with a meaningful message
Commit the current changes with an appropriate conventional commit message.
```

**Claude will:**
1. Run `git diff --staged`
2. Analyze what changed
3. Write a commit message following [Conventional Commits](https://www.conventionalcommits.org/) format
4. Execute `git commit -m "..."`

**Example output:**
```
feat(auth): add JWT middleware for route protection

- Adds verifyToken middleware to src/middleware/auth.ts
- Protects /api/v1/users and /api/v1/orders routes
- Token expiry set to 7 days
```

### Git Settings in CLAUDE.md

Add your preferred Git conventions to `CLAUDE.md`:

```markdown
## Git Conventions
- Use conventional commits: feat, fix, chore, docs, refactor, test
- Always reference ticket number if available: feat(auth): ... [PROJ-123]
- Keep subject line under 72 characters
- Run tests before committing
```

---

## 15. Components & Hooks (React / Frontend Spec)

When building UI components, use the spec-driven workflow:

### Component Spec Pattern

```bash
Create a reusable <DataTable> component with these requirements:
- Props: columns[], data[], onRowClick, loading, emptyMessage
- Sortable columns (click header to sort asc/desc)
- Loading skeleton state
- Accessible: proper aria roles
- Styled with Tailwind CSS
- TypeScript with full prop types
Save the component to src/components/DataTable.tsx
```

### Hooks Spec Pattern

```bash
Create a custom hook useAuth with:
- Returns: { user, login, logout, isLoading, error }
- Persists token to localStorage
- Auto-refreshes token before expiry
- TypeScript typed
Save to src/hooks/useAuth.ts
```

---

## 16. Plan Mode

**Plan mode** is the practice of making Claude reason and plan *before* writing code. This is one of the highest-leverage habits in Claude Code.

### Enable Explicit Plan Mode

```bash
# Tell Claude to plan only — no implementation yet
Plan how you would implement [feature]. Do not write any code yet.
Output the plan to PLAN.md with file paths, function names, and order of operations.
```

### Review the Plan

Read `PLAN.md`. Edit it if anything is wrong. This is your chance to redirect before any code is written.

### Execute the Plan

```bash
Implement PLAN.md exactly as written. Work through it step by step.
After each step, tell me what you completed and what comes next.
```

### Why Plan Mode Matters

| Without Plan Mode | With Plan Mode |
|---|---|
| Claude guesses at approach | You approve the approach first |
| Hard to course-correct mid-way | Easy to edit the plan before starting |
| Messy multi-file changes | Ordered, traceable implementation |
| Hard to resume after interruption | PLAN.md is a checkpoint |

---

## Quick Reference Card

```
/doctor          → Check installation health
/init            → Set up project + create CLAUDE.md
/config          → View/edit settings
/theme           → Switch UI theme
/model           → Switch AI model mid-session
/context         → View active context window
/compact         → Compress context to save tokens
/rewind          → Undo last Claude action
/permission      → Manage action permissions
@filename        → Add a file to context
claude -r        → Resume last session
claude --resume  → Resume last session (full flag)
/resume          → Continue from where Claude left off
```

---

## Workflow Cheat Sheet

```bash
# Start a new project session
cd my-project
claude
/init                          # Create CLAUDE.md
/analyze                       # Run custom analyze command

# Plan and build a feature
Plan how to add [feature]. Save to PLAN.md.
Implement PLAN.md step by step.

# Manage a long session
/compact                       # Free up context
/rewind                        # Undo last action
claude -r                      # Resume tomorrow

# Git workflow
Commit the staged changes with a conventional commit message.
```

---

*Claude Code — Anthropic | claude.ai*
