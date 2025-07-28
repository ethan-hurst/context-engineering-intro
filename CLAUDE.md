### 🔄 Project Awareness & Context
- **Always read `PLANNING.md`** at the start of a new conversation to understand the project's architecture, goals, style, and constraints.
- **Check `TASK.md`** before starting a new task. If the task isn’t listed, add it with a brief description and today's date.
- **Use consistent naming conventions, file structure, and architecture patterns** as described in `PLANNING.md`.
- **Use venv_linux** (the virtual environment) whenever executing Python commands, including for unit tests.

### 🧱 Code Structure & Modularity
- **Never create a file longer than 500 lines of code.** If a file approaches this limit, refactor by splitting it into modules or helper files.
- **Organize code into clearly separated modules**, grouped by feature or responsibility.
  For agents this looks like:
    - `agent.py` - Main agent definition and execution logic 
    - `tools.py` - Tool functions used by the agent 
    - `prompts.py` - System prompts
- **Use clear, consistent imports** (prefer relative imports within packages).
- **Use clear, consistent imports** (prefer relative imports within packages).
- **Use python_dotenv and load_env()** for environment variables.

### 🧪 Testing & Reliability
- **Always create Pytest unit tests for new features** (functions, classes, routes, etc).
- **After updating any logic**, check whether existing unit tests need to be updated. If so, do it.
- **Tests should live in a `/tests` folder** mirroring the main app structure.
  - Include at least:
    - 1 test for expected use
    - 1 edge case
    - 1 failure case

### ✅ Task Completion
- **Mark completed tasks in `TASK.md`** immediately after finishing them.
- Add new sub-tasks or TODOs discovered during development to `TASK.md` under a “Discovered During Work” section.

### 📎 Style & Conventions
- **Use Python** as the primary language.
- **Follow PEP8**, use type hints, and format with `black`.
- **Use `pydantic` for data validation**.
- Use `FastAPI` for APIs and `SQLAlchemy` or `SQLModel` for ORM if applicable.
- Write **docstrings for every function** using the Google style:
  ```python
  def example():
      """
      Brief summary.

      Args:
          param1 (type): Description.

      Returns:
          type: Description.
      """
  ```

### 📚 Documentation & Explainability
- **Update `README.md`** when new features are added, dependencies change, or setup steps are modified.
- **Comment non-obvious code** and ensure everything is understandable to a mid-level developer.
- When writing complex logic, **add an inline `# Reason:` comment** explaining the why, not just the what.

### 🧠 AI Behavior Rules
- **Never assume missing context. Ask questions if uncertain.**
- **Never hallucinate libraries or functions** – only use known, verified Python packages.
- **Always confirm file paths and module names** exist before referencing them in code or tests.
- **Never delete or overwrite existing code** unless explicitly instructed to or if part of a task from `TASK.md`.

### 🛡️ Error Prevention & Recovery
- **Pre-flight Checks**: Always validate environment, dependencies, and file structure before starting
- **Incremental Validation**: Run tests and lints after each significant change, not just at the end
- **Rollback Strategy**: Create git checkpoints before major changes; know how to revert cleanly
- **Defensive Coding**: Anticipate edge cases, validate inputs, handle exceptions gracefully
- **Resource Management**: Check disk space, memory usage, and API rate limits before intensive operations

### 🔍 Debugging Protocol
- **Systematic Approach**: When errors occur, follow this order:
  1. Read the full error message and stack trace
  2. Identify the exact line and operation that failed
  3. Check for typos, missing imports, or syntax errors
  4. Validate data types and variable states at the failure point
  5. Test with minimal reproducible examples
- **Logging Strategy**: Use appropriate log levels (DEBUG, INFO, WARN, ERROR) with context
- **Tool Usage**: Leverage debuggers, profilers, and static analysis tools
- **Documentation**: When debugging complex issues, document the root cause and solution

### 🔒 Security First
- **Input Validation**: Always sanitize and validate user inputs and external data
- **Secret Management**: Never hardcode secrets; use environment variables or secure vaults
- **Dependency Security**: Regularly check for vulnerabilities in dependencies
- **Access Control**: Implement proper authentication and authorization patterns
- **Error Information**: Never expose sensitive information in error messages or logs

### ⚡ Performance Optimization
- **Measure First**: Profile before optimizing; don't guess at bottlenecks
- **Efficient Algorithms**: Choose appropriate data structures and algorithms for the scale
- **Resource Usage**: Monitor memory leaks, connection pools, and file handles
- **Caching Strategy**: Implement caching at appropriate levels (memory, disk, network)
- **Async Patterns**: Use async/await properly for I/O-bound operations

### 📦 Dependency Management
- **Version Pinning**: Use specific versions for production dependencies
- **Compatibility Checks**: Verify new dependencies don't conflict with existing ones
- **License Compliance**: Ensure all dependencies have compatible licenses
- **Minimal Dependencies**: Only add dependencies that provide significant value
- **Update Strategy**: Have a plan for keeping dependencies current and secure

### 🔄 Code Review Checklist
- **Functionality**: Does the code solve the problem as specified?
- **Readability**: Is the code clear and well-documented?
- **Performance**: Are there obvious performance issues?
- **Security**: Are there potential security vulnerabilities?
- **Testing**: Are there adequate tests covering the changes?
- **Standards**: Does the code follow project conventions and style guides?