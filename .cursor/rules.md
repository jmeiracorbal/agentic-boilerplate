# Cursor IDE Agent Rules & Configuration Guidelines

## Overview

This document defines the rules and best practices for using Cursor IDE agents in the agentic-boilerplate project. These guidelines ensure consistent, high-quality code generation and assistance throughout the development workflow.

---

## Core Principles

### 1. Code Quality First
- All generated code must follow the project's established code style and conventions
- Prioritize readability and maintainability over brevity
- Include proper error handling and validation

### 2. Context Awareness
- Agents must understand the project structure and existing patterns
- Reference existing implementations before suggesting new patterns
- Maintain consistency with the codebase conventions

### 3. Documentation
- Generated code should be self-documenting with clear comments
- Include docstrings for functions, classes, and modules
- Update relevant documentation when making significant changes

### 4. Testing
- Suggest or generate corresponding test cases for new functionality
- Ensure code changes don't break existing tests
- Include edge case coverage in test suggestions

---

## Agent Configuration Rules

### Code Generation Rules

#### Rule 1: Follow Project Conventions
- **Description**: All generated code must adhere to project-specific patterns and conventions
- **Action**: Before generating code, analyze existing similar implementations
- **Scope**: Functions, classes, modules, and configurations

#### Rule 2: Type Safety
- **Description**: Enforce strong typing in all code generation
- **Action**: Use proper type hints in Python, TypeScript, or other languages
- **Example**: Use type annotations for function parameters and return values
```python
def process_data(input_data: dict[str, Any], config: Config) -> ProcessResult:
    """Process data with type-safe parameters."""
    pass
```

#### Rule 3: Error Handling
- **Description**: All generated code must include proper error handling
- **Action**: Include try-catch blocks, validation, and meaningful error messages
- **Scope**: API calls, file operations, data processing

#### Rule 4: DRY Principle
- **Description**: Don't Repeat Yourself - avoid code duplication
- **Action**: Refactor duplicated code into reusable utilities
- **Reference**: Check existing utility modules before generating new code

### Documentation Rules

#### Rule 5: Docstring Standards
- **Description**: All functions and classes require docstrings
- **Format**: Follow the project's docstring convention (Google/NumPy style)
- **Minimum Content**: Brief description, parameters, return value, exceptions

```python
def get_agent_config(agent_id: str) -> AgentConfig:
    """
    Retrieve agent configuration by ID.
    
    Args:
        agent_id: Unique identifier for the agent
        
    Returns:
        AgentConfig: The configuration object for the agent
        
    Raises:
        ConfigNotFoundError: If agent configuration doesn't exist
    """
    pass
```

#### Rule 6: Comment Clarity
- **Description**: Comments should explain the "why", not the "what"
- **Action**: Add comments for complex logic, business rules, and non-obvious decisions
- **Avoid**: Obvious comments that just restate the code

#### Rule 7: README & Documentation Updates
- **Description**: Keep documentation synchronized with code changes
- **Action**: Update relevant markdown files when adding new features
- **Scope**: Feature additions, API changes, configuration changes

### Testing Rules

#### Rule 8: Test Coverage
- **Description**: Generate tests for all new functionality
- **Minimum Coverage**: 80% for new code
- **Test Types**: Unit tests, integration tests (as appropriate)

#### Rule 9: Test Naming
- **Description**: Tests should clearly describe what they test
- **Format**: `test_<function_name>_<condition>_<expected_result>`
- **Example**: `test_process_agent_with_invalid_config_raises_error`

#### Rule 10: Test Independence
- **Description**: Tests must be independent and not rely on execution order
- **Action**: Use proper setup/teardown, avoid shared state
- **Scope**: Unit tests must not depend on other unit tests

### Architecture & Design Rules

#### Rule 11: Modularity
- **Description**: Code should be modular and follow separation of concerns
- **Action**: Create separate modules for different responsibilities
- **Structure**: Group related functionality into cohesive modules

#### Rule 12: Configuration Management
- **Description**: All configurable values should be externalized
- **Action**: Use configuration files, environment variables, or config classes
- **Avoid**: Hardcoded values in source code

#### Rule 13: API Contract Consistency
- **Description**: Maintain consistent API contracts for agent interactions
- **Action**: Follow existing patterns for request/response structures
- **Validation**: Validate inputs at API boundaries

### Performance Rules

#### Rule 14: Efficiency
- **Description**: Generated code should be performant
- **Action**: Consider time complexity and memory usage
- **Optimization**: Suggest optimizations for known bottlenecks

#### Rule 15: Logging & Monitoring
- **Description**: Include appropriate logging for debugging and monitoring
- **Levels**: Use DEBUG, INFO, WARNING, ERROR appropriately
- **Performance**: Avoid verbose logging in hot paths

---

## Configuration Best Practices

### Directory Structure
```
.cursor/
├── rules.md           # This file
├── config.json        # IDE configuration
└── custom-rules/      # Custom rule definitions
```

### Environment Variables
Define required environment variables for:
- `AGENT_CONFIG_PATH`: Path to agent configurations
- `LOG_LEVEL`: Logging verbosity
- `API_KEY`: API authentication (if applicable)

### IDE Settings
- **Auto-format**: Enable on save
- **Linting**: Enforce project linting rules
- **Type Checking**: Enable strict type checking

---

## Interaction Guidelines

### When Requesting Code Generation

1. **Provide Context**
   - Share relevant existing code examples
   - Describe the intended use case
   - Specify any constraints or requirements

2. **Specify Requirements**
   - Language and framework
   - Performance expectations
   - Security considerations

3. **Request Specific Aspects**
   - Implementation details
   - Test cases
   - Documentation needs

### When Reviewing Agent Suggestions

1. **Check Compliance**
   - Verify adherence to these rules
   - Ensure consistency with existing code
   - Validate error handling

2. **Assess Quality**
   - Code readability and maintainability
   - Performance implications
   - Security considerations

3. **Provide Feedback**
   - Request specific changes
   - Reference rule violations
   - Suggest improvements

---

## Project-Specific Conventions

### Naming Conventions
- **Functions**: `snake_case` (Python), `camelCase` (JavaScript)
- **Classes**: `PascalCase`
- **Constants**: `UPPER_SNAKE_CASE`
- **Private Members**: Prefix with `_`

### File Organization
- Python: `src/` for source, `tests/` for tests
- Configuration: Store in `config/` directory
- Documentation: Maintain in `docs/` directory

### Import Organization
1. Standard library imports
2. Third-party imports
3. Local application imports

---

## Security Rules

#### Rule 16: Input Validation
- **Description**: All external inputs must be validated
- **Action**: Implement input sanitization and validation
- **Scope**: API inputs, file uploads, user data

#### Rule 17: Secrets Management
- **Description**: Never hardcode secrets or sensitive data
- **Action**: Use environment variables or secure vaults
- **Audit**: Regularly scan for exposed secrets

#### Rule 18: Dependency Security
- **Description**: Keep dependencies up to date and secure
- **Action**: Use dependency scanning tools
- **Review**: Regularly audit for vulnerabilities

---

## Common Patterns

### Agent Configuration Pattern
```python
@dataclass
class AgentConfig:
    """Standard agent configuration structure."""
    name: str
    model: str
    temperature: float = 0.7
    max_tokens: int = 2048
    system_prompt: str = ""
    
    def validate(self) -> bool:
        """Validate configuration parameters."""
        return all([self.name, self.model, 0 <= self.temperature <= 1])
```

### Error Handling Pattern
```python
class AgentError(Exception):
    """Base exception for agent operations."""
    pass

class ConfigurationError(AgentError):
    """Raised when configuration is invalid."""
    pass
```

### Logging Pattern
```python
import logging

logger = logging.getLogger(__name__)

logger.debug("Detailed diagnostic information")
logger.info("Confirmation that things are working as expected")
logger.warning("Something unexpected, or indicative of some problem in the near future")
logger.error("Due to a more serious problem, the software has not been able to perform some function")
```

---

## Maintenance & Updates

- Review these rules quarterly
- Update based on project learnings and evolution
- Communicate changes to the team
- Version control this document with the codebase

---

## References

- [PEP 8 – Style Guide for Python Code](https://pep8.org/)
- [Google Python Style Guide](https://google.github.io/styleguide/pyguide.html)
- [Cursor IDE Documentation](https://docs.cursor.sh/)

---

**Last Updated**: 2025-12-27  
**Maintained By**: Project Team  
**Status**: Active
