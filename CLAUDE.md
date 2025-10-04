# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is an educational materials repository containing course documentation and tutorials in French, primarily focused on web development, Linux system administration, databases, and version control. The content is structured as markdown files organized by topic.

## Repository Structure

```
documents/
├── database/        # Database tutorials (SQL, MySQL)
├── linux/          # Linux administration and tutorials
│   ├── tp-intro-linux/       # Introductory Linux exercises
│   ├── usecase/              # Use cases (VM setup, Docker, services)
│   ├── serveur_dev_web/      # Web development server setup
│   └── win-docker-LAMP/      # Docker LAMP stack on Windows
├── projet/         # Version control tutorials (Git, GitHub)
├── quickstart/     # Quick installation guides (Kitty terminal, etc.)
└── web/           # Web development (HTML, CSS, Node.js, npm, Gulp)
```

## Content Organization

- **Language**: All documentation is in French
- **Format**: Markdown (.md) files with code examples and exercises
- **Git Configuration**: Repository uses `main` as the default branch
- **Ignored Files**: Draft files (`*-draft*`), specific use cases in `linux/usecase/draft/`, and certain service documentation files (see `.gitignore`)

## Common Tasks

### Adding New Tutorial Content

When creating new tutorial files:
- Place files in the appropriate topic directory (database/, linux/, web/, projet/, quickstart/)
- Use markdown format with clear section headers
- Include practical code examples in fenced code blocks
- For Linux tutorials, prefer command examples over long explanations
- For Git/GitHub tutorials, include command-line examples with expected output

### Working with Existing Documentation

- All content is educational, designed for teaching/learning
- Most files contain:
  - Step-by-step instructions
  - Code examples (bash, SQL, JavaScript, etc.)
  - Practical exercises
  - External reference links

### Git Workflow

This repository follows standard Git practices as documented in `projet/intro-git-github.md`:
- Main branch: `main`
- Use descriptive commit messages in French
- Standard workflow: `git add` → `git commit` → `git push origin main`

## Key Topics Covered

- **Linux**: Command-line basics, file permissions, shell scripting, VM setup, Docker, web servers
- **Web Development**: HTML, CSS, Node.js, npm package management, Gulp build tools
- **Databases**: SQL queries, joins, database design exercises (using MySQL)
- **Version Control**: Git fundamentals, GitHub collaboration, branches, merge strategies
- **DevOps**: Docker containers, LAMP stack, reverse proxy with nginx

## File Conventions

- Use `.md` extension for all documentation
- Tutorial files often include exercises (`tp1-`, `tp2-`, etc.)
- Installation guides are in `quickstart/` or `linux/usecase/`
- Reference materials use descriptive names (e.g., `linux_commandes_base.md`, `git-aide-memoire.md`)
