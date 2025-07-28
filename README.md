# Context Engineering Template

A comprehensive template for getting started with Context Engineering - the discipline of engineering context for AI coding assistants so they have the information necessary to get the job done end to end.

> **Context Engineering is 10x better than prompt engineering and 100x better than vibe coding.**

## 🚀 Quick Start

```bash
# 1. Clone this template
git clone https://github.com/coleam00/Context-Engineering-Intro.git
cd Context-Engineering-Intro

# 2. Set up your project rules (optional - template provided)
# Edit CLAUDE.md to add your project-specific guidelines

# 3. Add examples (highly recommended)
# Place relevant code examples in the examples/ folder

# 4. Create your initial feature request
# Edit INITIAL.md with your feature requirements

# 5. Generate a comprehensive PRP (Product Requirements Prompt)
# In Claude Code, run:
/generate-prp INITIAL.md

# 6. Execute the PRP to implement your feature
# In Claude Code, run:
/execute-prp PRPs/your-feature-name.md
```

## 📚 Table of Contents

- [What is Context Engineering?](#what-is-context-engineering)
- [Template Structure](#template-structure)
- [Step-by-Step Guide](#step-by-step-guide)
- [Writing Effective INITIAL.md Files](#writing-effective-initialmd-files)
- [The PRP Workflow](#the-prp-workflow)
- [Using Examples Effectively](#using-examples-effectively)
- [Best Practices](#best-practices)

## What is Context Engineering?

Context Engineering represents a paradigm shift from traditional prompt engineering:

### Prompt Engineering vs Context Engineering

**Prompt Engineering:**
- Focuses on clever wording and specific phrasing
- Limited to how you phrase a task
- Like giving someone a sticky note

**Context Engineering:**
- A complete system for providing comprehensive context
- Includes documentation, examples, rules, patterns, and validation
- Like writing a full screenplay with all the details

### Why Context Engineering Matters

1. **Reduces AI Failures**: Most agent failures aren't model failures - they're context failures
2. **Ensures Consistency**: AI follows your project patterns and conventions
3. **Enables Complex Features**: AI can handle multi-step implementations with proper context
4. **Self-Correcting**: Validation loops allow AI to fix its own mistakes

## Template Structure

```
context-engineering-intro/
├── .claude/
│   ├── commands/
│   │   ├── generate-prp.md         # Enhanced: Generates comprehensive PRPs with risk assessment
│   │   ├── execute-prp.md          # Enhanced: Executes PRPs with robust error recovery
│   │   ├── review-prp.md           # NEW: PRP quality review and improvement
│   │   ├── validate-code.md        # NEW: Comprehensive code validation
│   │   ├── debug-implementation.md # NEW: Systematic debugging framework
│   │   ├── security-audit.md       # NEW: Complete security assessment
│   │   ├── performance-optimize.md # NEW: Performance analysis and optimization
│   │   ├── refactor-legacy.md      # NEW: Legacy code modernization
│   │   └── README.md              # Command documentation and usage guide
│   └── settings.local.json        # Enhanced: Claude Code permissions for new tools
├── PRPs/
│   ├── templates/
│   │   └── prp_base.md            # Base template for PRPs
│   └── EXAMPLE_multi_agent_prp.md # Example of a complete PRP
├── examples/                       # Your code examples (critical!)
├── CLAUDE.md                      # Enhanced: Global rules with advanced error prevention
├── INITIAL.md                     # Template for feature requests
├── INITIAL_EXAMPLE.md             # Example feature request
└── README.md                      # This file
```

This template doesn't focus on RAG and tools with context engineering because I have a LOT more in store for that soon. ;)

## Step-by-Step Guide

### 1. Set Up Global Rules (CLAUDE.md)

The `CLAUDE.md` file contains project-wide rules that the AI assistant will follow in every conversation. The template includes:

- **Project awareness**: Reading planning docs, checking tasks
- **Code structure**: File size limits, module organization
- **Testing requirements**: Unit test patterns, coverage expectations
- **Style conventions**: Language preferences, formatting rules
- **Documentation standards**: Docstring formats, commenting practices

**You can use the provided template as-is or customize it for your project.**

### 2. Create Your Initial Feature Request

Edit `INITIAL.md` to describe what you want to build:

```markdown
## FEATURE:
[Describe what you want to build - be specific about functionality and requirements]

## EXAMPLES:
[List any example files in the examples/ folder and explain how they should be used]

## DOCUMENTATION:
[Include links to relevant documentation, APIs, or MCP server resources]

## OTHER CONSIDERATIONS:
[Mention any gotchas, specific requirements, or things AI assistants commonly miss]
```

**See `INITIAL_EXAMPLE.md` for a complete example.**

### 3. Generate the PRP

PRPs (Product Requirements Prompts) are comprehensive implementation blueprints that include:

- Complete context and documentation
- Implementation steps with validation
- Error handling patterns
- Test requirements
- **NEW: Risk assessment and mitigation strategies**
- **NEW: Security and performance requirements**
- **NEW: Quality gates and validation commands**

Run in Claude Code:
```bash
/generate-prp INITIAL.md
```

**Enhanced Features:**
- **Requirement Validation**: Ensures all requirements are complete and measurable
- **Risk Assessment**: Identifies and mitigates implementation risks
- **Context Sufficiency**: Validates that all necessary context is included
- **Technical Debt Assessment**: Considers impact on existing codebase

## Advanced Command Suite (NEW)

This template now includes a comprehensive suite of specialized commands for bulletproof development:

### Quality Assurance Commands
```bash
# Review and improve PRP quality
/review-prp PRPs/your-feature.md

# Comprehensive code validation
/validate-code src/

# Systematic debugging
/debug-implementation "issue description"
```

### Security & Performance Commands
```bash
# Complete security audit
/security-audit src/

# Performance analysis and optimization
/performance-optimize src/module.py

# Legacy code modernization
/refactor-legacy legacy_code.py
```

### Key Benefits
- **Error Prevention**: Advanced validation and rollback strategies
- **Security First**: Comprehensive security scanning and compliance checking
- **Performance Optimization**: Multi-dimensional performance analysis
- **Quality Assurance**: Automated testing and code quality validation
- **Legacy Modernization**: Systematic approach to technical debt reduction

See `.claude/commands/README.md` for detailed command documentation.

This command will:
1. Read your feature request
2. Research the codebase for patterns
3. Search for relevant documentation
4. Create a comprehensive PRP in `PRPs/your-feature-name.md`

### 4. Execute the PRP

Once generated, execute the PRP to implement your feature:

```bash
/execute-prp PRPs/your-feature-name.md
```

**Enhanced AI coding assistant will:**
1. **Pre-execution Validation**: Environment health checks and dependency validation
2. **Load Context**: Read all context from the PRP with enhanced analysis
3. **Create Implementation Plan**: Detailed task breakdown with risk mitigation
4. **Execute with Safety**: Step-by-step implementation with continuous validation
5. **Enhanced Error Recovery**: Categorized error handling with automated fixes
6. **Quality Assurance**: Multi-level validation including security and performance
7. **Complete Documentation**: Comprehensive reporting and knowledge transfer

**NEW Quality Features:**
- **Environment Health Checks**: Validates system readiness before starting
- **Enhanced Rollback Strategy**: Timestamped checkpoints for safe recovery
- **Real-time Validation**: Continuous quality checks during implementation
- **Security Integration**: Built-in security scanning and validation
- **Performance Monitoring**: Tracks performance impact during development

## Writing Effective INITIAL.md Files

### Key Sections Explained

**FEATURE**: Be specific and comprehensive
- ❌ "Build a web scraper"
- ✅ "Build an async web scraper using BeautifulSoup that extracts product data from e-commerce sites, handles rate limiting, and stores results in PostgreSQL"

**EXAMPLES**: Leverage the examples/ folder
- Place relevant code patterns in `examples/`
- Reference specific files and patterns to follow
- Explain what aspects should be mimicked

**DOCUMENTATION**: Include all relevant resources
- API documentation URLs
- Library guides
- MCP server documentation
- Database schemas

**OTHER CONSIDERATIONS**: Capture important details
- Authentication requirements
- Rate limits or quotas
- Common pitfalls
- Performance requirements

## The PRP Workflow

### How /generate-prp Works

The command follows this process:

1. **Research Phase**
   - Analyzes your codebase for patterns
   - Searches for similar implementations
   - Identifies conventions to follow

2. **Documentation Gathering**
   - Fetches relevant API docs
   - Includes library documentation
   - Adds gotchas and quirks

3. **Blueprint Creation**
   - Creates step-by-step implementation plan
   - Includes validation gates
   - Adds test requirements

4. **Quality Check**
   - Scores confidence level (1-10)
   - Ensures all context is included

### How /execute-prp Works

1. **Load Context**: Reads the entire PRP
2. **Plan**: Creates detailed task list using TodoWrite
3. **Execute**: Implements each component
4. **Validate**: Runs tests and linting
5. **Iterate**: Fixes any issues found
6. **Complete**: Ensures all requirements met

See `PRPs/EXAMPLE_multi_agent_prp.md` for a complete example of what gets generated.

## Using Examples Effectively

The `examples/` folder is **critical** for success. AI coding assistants perform much better when they can see patterns to follow.

### What to Include in Examples

1. **Code Structure Patterns**
   - How you organize modules
   - Import conventions
   - Class/function patterns

2. **Testing Patterns**
   - Test file structure
   - Mocking approaches
   - Assertion styles

3. **Integration Patterns**
   - API client implementations
   - Database connections
   - Authentication flows

4. **CLI Patterns**
   - Argument parsing
   - Output formatting
   - Error handling

### Example Structure

```
examples/
├── README.md           # Explains what each example demonstrates
├── cli.py             # CLI implementation pattern
├── agent/             # Agent architecture patterns
│   ├── agent.py      # Agent creation pattern
│   ├── tools.py      # Tool implementation pattern
│   └── providers.py  # Multi-provider pattern
└── tests/            # Testing patterns
    ├── test_agent.py # Unit test patterns
    └── conftest.py   # Pytest configuration
```

## Best Practices

### 1. Be Explicit in INITIAL.md
- Don't assume the AI knows your preferences
- Include specific requirements and constraints
- Reference examples liberally

### 2. Provide Comprehensive Examples
- More examples = better implementations
- Show both what to do AND what not to do
- Include error handling patterns

### 3. Use Validation Gates
- PRPs include test commands that must pass
- AI will iterate until all validations succeed
- This ensures working code on first try

### 4. Leverage Documentation
- Include official API docs
- Add MCP server resources
- Reference specific documentation sections

### 5. Customize CLAUDE.md
- Add your conventions
- Include project-specific rules
- Define coding standards

## Resources

- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Context Engineering Best Practices](https://www.philschmid.de/context-engineering)