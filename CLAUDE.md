# Claude Agent Guidelines and Best Practices

## Overview

This document outlines best practices for working with Claude as an AI code agent within the agentic-boilerplate framework. These guidelines help ensure effective collaboration, reliable code generation, and maintainable AI-assisted workflows.

## Core Principles

### 1. Clear Intent and Context
- **Provide explicit objectives**: Clearly state what you want the AI agent to accomplish
- **Include relevant context**: Share repository structure, existing patterns, and constraints
- **Define success criteria**: Specify how you'll measure if the task is complete
- **Share constraints**: Mention performance requirements, dependencies, or architectural decisions

### 2. Iterative Refinement
- **Start with prototypes**: Begin with simple implementations and iterate
- **Provide feedback loops**: Guide the agent toward better solutions with specific feedback
- **Validate outputs**: Test generated code before integration
- **Refine incrementally**: Make small, focused adjustments rather than complete rewrites

### 3. Responsibility and Review
- **Always review AI-generated code**: Don't merge or deploy without understanding it
- **Maintain human oversight**: Use AI to augment, not replace, human judgment
- **Document assumptions**: Clearly mark where AI made decisions
- **Preserve accountability**: Keep records of what was generated and why

## Best Practices for Code Generation

### Planning Phase
1. **Break down large tasks**: Divide complex features into smaller, manageable components
2. **Define interfaces first**: Specify function signatures and type definitions before implementation
3. **List requirements explicitly**: Include error handling, edge cases, and performance needs
4. **Reference existing code**: Point to similar implementations in the codebase

### Implementation Phase
1. **Request specific formats**: Ask for code in a particular style matching your codebase
2. **Include test cases**: Ask the agent to generate unit tests alongside implementation
3. **Follow your conventions**: Ensure naming conventions, formatting, and patterns match
4. **Validate dependencies**: Confirm all imports and external dependencies are available

### Testing and Validation
1. **Test locally first**: Run generated code in a local environment before committing
2. **Check edge cases**: Verify behavior with boundary conditions and error states
3. **Performance testing**: Validate generated solutions meet performance requirements
4. **Integration testing**: Ensure new code works with existing components

## Working with Claude in Code Workflows

### Effective Prompting
- **Be specific**: "Create a React component that displays user data from an API" is better than "Make a React component"
- **Provide examples**: Share code snippets of expected style and patterns
- **Ask for explanations**: Request comments and documentation alongside code
- **Request alternatives**: Ask for multiple approaches to evaluate trade-offs

### Handling Limitations
- **Verify complex logic**: AI may make mistakes in intricate algorithms; verify carefully
- **Check integration points**: Ensure generated code correctly interfaces with existing systems
- **Test security-critical code**: Always review authentication, authorization, and data handling
- **Validate assumptions**: Confirm the AI understood your requirements correctly

### Continuous Improvement
1. **Document patterns that work well**: Save effective prompts and approaches
2. **Learn from mistakes**: When AI makes errors, understand why and adjust future prompts
3. **Build a knowledge base**: Create examples and templates for common tasks
4. **Share successful patterns**: Document reusable approaches for your team

## Code Quality Standards

### Documentation
- Generated code should include:
  - Function/class docstrings explaining purpose and parameters
  - Inline comments for complex logic
  - Type annotations where applicable
  - Examples of usage for public APIs

### Testing
- Every generated function should have:
  - Unit tests covering main functionality
  - Edge case tests for boundary conditions
  - Integration tests for external dependencies
  - Error handling tests for failure scenarios

### Style and Consistency
- Follow your project's linting rules
- Match existing code formatting and naming conventions
- Use consistent error handling patterns
- Maintain uniform code organization

## Security Considerations

1. **Code Review is Critical**: Always review AI-generated code for security vulnerabilities
2. **Dependency Verification**: Confirm external dependencies are legitimate and well-maintained
3. **Data Handling**: Ensure proper handling of sensitive data, authentication tokens, and user information
4. **Input Validation**: Verify that input validation is present and correct
5. **Error Messages**: Check that error messages don't expose sensitive information

## Common Pitfalls to Avoid

- ❌ Accepting code without understanding it
- ❌ Using generated code in security-critical areas without thorough review
- ❌ Ignoring performance implications of generated solutions
- ❌ Merging code without tests
- ❌ Not providing sufficient context to the AI
- ❌ Treating AI output as infallible
- ❌ Skipping code review and validation steps

## Tips for Success

✅ **Start conversations with clear context**: Share your codebase structure and conventions
✅ **Ask for step-by-step explanations**: Request the AI explain its reasoning
✅ **Test incrementally**: Validate each component as it's generated
✅ **Provide constructive feedback**: Guide the AI toward better solutions
✅ **Maintain code ownership**: Understand every line before committing
✅ **Document decisions**: Keep notes on why certain approaches were chosen
✅ **Build institutional knowledge**: Share learnings across your team

## Working with Claude's Strengths

Claude excels at:
- 📝 Writing clear, well-documented code
- 🧪 Generating comprehensive test suites
- 🔍 Explaining existing code and patterns
- 📋 Creating boilerplate and scaffolding
- 🎯 Refactoring code for clarity
- 📚 Writing documentation and guides
- 🐛 Identifying potential issues and bugs

## Prompt Templates

### For New Features
```
I need to implement [feature description].
Context:
- Technology stack: [list frameworks/languages]
- Existing pattern to follow: [reference existing code]
- Requirements: [list specific requirements]
- Constraints: [any limitations or requirements]

Please provide:
1. Implementation with inline comments
2. Unit tests
3. Usage examples
```

### For Code Review
```
Please review this code for:
- Security vulnerabilities
- Performance issues
- Edge cases not handled
- Code style consistency
- Potential bugs

[Code to review]
```

### For Debugging
```
This code is not working as expected.
Expected behavior: [what should happen]
Actual behavior: [what's happening]
Context: [relevant code and error messages]

Please help identify the issue and suggest a fix.
```

## Integration with CI/CD

- Ensure all AI-generated code passes linting checks
- Require all generated code to have tests with minimum coverage thresholds
- Use automated security scanning tools on generated code
- Run generated code through type checking (TypeScript, mypy, etc.)
- Include code review gates before merging

## References and Resources

- [Claude API Documentation](https://docs.anthropic.com)
- [Prompt Engineering Best Practices](https://docs.anthropic.com/prompt_eng)
- [Code Safety Guidelines](https://docs.anthropic.com)

## Version History

- **2025-12-27**: Initial version created with comprehensive guidelines and best practices

---

**Remember**: The goal is to use Claude as a powerful tool to enhance your development process while maintaining high standards of code quality, security, and maintainability.
