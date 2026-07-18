# Contributing

Thank you for your interest in contributing to this project!

The public repository currently contains documentation and community resources. Source code is maintained privately. The guidelines below describe the framework's development practices and future contribution workflow.

This project is currently developed primarily as a solo effort, but the goal is to build a framework that can eventually support community contributions, extensions, and collaboration.

Whether you are reporting issues, suggesting improvements, improving documentation, or contributing code, all feedback is appreciated.

---

# Ways to Contribute

There are several ways you can help improve the project.

## 🐛 Bug Reports

Found something that is not working correctly?

Please create an issue with:

- A clear description of the problem
- Steps to reproduce the issue
- Expected behavior
- Actual behavior
- Screenshots or logs if applicable
- Version where the issue occurred

Example:

```

Version:
v0.xx.xx

System:
Inventory System

Issue:
Items disappear after saving and loading.

Steps:

1. Add item to inventory
2. Save game
3. Reload save
4. Item is missing

```

---

## 💡 Feature Requests

Have an idea for improving the framework?

Feature requests are welcome.

Please include:

- What problem the feature solves
- Why it would improve the framework
- Possible use cases
- Any examples or references

Large features may require discussion before implementation to ensure they fit the overall architecture.

---

## 📚 Documentation Improvements

Documentation is a major part of this project.

Contributions can include:

- Fixing unclear explanations
- Improving examples
- Adding tutorials
- Correcting outdated information
- Improving system documentation

Clear documentation helps everyone build with the framework.

---

# Code Contributions

Code contributions may be expanded as the project grows.

Before submitting major code changes:

1. Open an issue or discussion first
2. Explain the purpose of the change
3. Discuss architectural impact
4. Wait for approval before large implementations

This helps prevent conflicting approaches and keeps the framework consistent.

---

# Development Guidelines

## Architecture

This project follows:

- Modular system design
- Data-driven development
- Generator-based workflows
- Separation of runtime and authoring tools
- Reusable framework components

New systems should follow existing architectural patterns whenever possible.

---

## Code Style

Please follow existing project conventions:

- Use clear naming
- Keep systems modular
- Avoid unnecessary dependencies
- Comment complex logic
- Document public systems
- Prefer maintainable solutions over shortcuts

---

## Documentation Requirements

New major systems should include:

- System overview
- Purpose and responsibilities
- Usage examples
- Architecture notes
- Related files

Documentation is considered part of the feature.

---

# Branching Strategy

As the project grows, development will move toward a structured workflow.

Planned branches:

```

main
└── stable releases

develop
└── active development

feature/*
└── new systems or improvements

fix/*
└── bug fixes

```

---

# Pull Requests

Pull requests should include:

## Description

Explain:

- What changed
- Why it changed
- What systems are affected

## Testing

Include:

- What was tested
- Any limitations
- Known issues

## Documentation

Update documentation when:

- Adding new systems
- Changing workflows
- Modifying public APIs

---

# Commit Guidelines

Commits follow the project versioning format to make development history easy to track.

## First Commit of a Development Day

```text
<developer>| v<major>.<milestone>.<day> |<System>|<Short Description>
```

Example:

```bash
"red| v0.52.192 |Quests|Objective KILL Definition"
```

---

## Additional Commits for the Same Development Day

Append a sequential commit number to the version.

```text
<developer>| v<major>.<milestone>.<day>.<commit> |<System>|<Short Description>
```

Where:

| Part | Description |
|------|-------------|
| major | Major project version |
| milestone | Development milestone |
| day | Development day of the project |
| commit | Sequential commit number for that day |

Example:

```text
"red| v0.52.192.001 |Quests|Objective KILL Definition"
```

If multiple commits are made during the same development day, increment only the commit number.

Examples:

```text
v0.52.192.001
v0.52.192.002
v0.52.192.003
```

The next development day resets the commit counter.

```text
v0.52.193.001
```

---

## Developer Identifier

Every commit begins with a short developer identifier.

Guidelines:

- 1–3 lowercase characters
- Unique per contributor
- Remains consistent across the project

Examples:

```text
red
sam
jd
aj
```

Example commits:

```bash
git commit -m "red| v0.52.192 |Quests|Objective KILL Definition"
git commit -m "red| v0.52.192.001 |Quest Menu|Tracking Authority"

git commit -m "sam| v0.52.192 |Inventory|Drag & Drop"
git commit -m "sam| v0.52.192.001 |Inventory|Stack Merge"
```

---

## Guidelines

- **Major**, **milestone**, and **development day** values are managed by Red (unauthorizedlogin).
- Contributors should only increment the commit suffix (`.001`, `.002`, etc.) when making additional commits on the current development day.
- Milestone changes will always be clearly announced
- Keep descriptions short and descriptive.
- Use the primary system affected.
- One feature or fix per commit whenever practical.
- Increment only the commit number when making multiple commits on the same development day.
- Reset the commit number to `.001` when the development day changes.

Common categories:

- Feature
- System Cleanup
- Refactor
- Fix
- Documentation
- Database
- Generator
- UI
- Performance
- Polish
```
```

---

# Project Structure

The project is organized around modular systems.

Major areas include:

```

system/
├── entity/
├── ui/
├── world/
├── database/
├── generators/
└── documentation/

```

Contributors should avoid tightly coupling systems unless the architecture requires it.

---

# Current Maintainer

This project is currently maintained by:

**Project Creator**

As the project grows, additional maintainers and contributors may be added.

---

# Code of Conduct

Contributors are expected to:

- Be respectful
- Provide constructive feedback
- Help maintain a welcoming environment
- Focus discussions on improving the project

Harassment, hostility, or disruptive behavior will not be tolerated.

---

# Thank You

Every contribution helps improve the project.

Whether you submit code, documentation, bug reports, ideas, or feedback, your involvement helps shape the future of the framework.
```
