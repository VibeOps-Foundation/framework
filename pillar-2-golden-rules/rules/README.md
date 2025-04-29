# Using Rules in AI IDEs: Golden Rules Implementation

This directory contains **Golden Rules** for AI-assisted development - the non-negotiable principles that all generated code must follow. These rules provide consistency, quality, and security guardrails for your development workflow.

## Understanding Rules in AI-Assisted Development

Golden Rules in AI IDEs are clear instructions that guide AI assistants to generate code meeting your organization's standards. They establish consistent patterns, reduce technical debt, and ensure alignment with quality and security requirements across all AI-assisted development.

## Global vs. Local Rules

### Global Rules
- **Definition**: Apply across your entire workspace or organization
- **Location**: Typically stored in central repositories or global configuration files
- **Use Cases**:
  - Organization-wide coding standards
  - Security requirements that apply to all projects
  - Architecture patterns and principles
  - Documentation standards

### Local Rules
- **Definition**: Apply to specific projects, files, or directories
- **Location**: Stored within project directories or project-specific configuration files
- **Use Cases**:
  - Project-specific coding conventions
  - Domain-specific implementation requirements
  - Override or extend global rules for specific components
  - Rules for handling specialized frameworks or libraries

## How Rules Are Evaluated

AI assistants typically evaluate rules in the following order:

1. **Global rules** are loaded first as a baseline
2. **Project-specific rules** are applied next, potentially overriding global rules
3. **Directory-level rules** may further refine behavior for specific components
4. **File-level rules** provide the most granular control
5. **Prompt-specific rules** can override all others for a single interaction

The evaluation typically follows a hierarchical model where more specific rules (local) can override more general rules (global) when there's a conflict, but critical global rules may be marked as non-overridable.

## Enforcing Strict Rule Adherence

To ensure AI assistants strictly follow your golden rules:

### 1. Use Clear, Imperative Language

Frame rules as direct commands rather than suggestions:

```
RULE: ALWAYS USE PARAMETERIZED QUERIES FOR DATABASE OPERATIONS
```

### 2. Apply Consistent Formatting

Use distinctive formatting to make rules stand out:

```
## [MANDATORY] ERROR HANDLING
ALL public methods MUST implement appropriate error handling and logging.
```

### 3. Include Rules in Prompts

Reference specific rules directly in your prompts:

```
Following our Golden Rules (especially DevXP-001 and SecOps-003), 
help me implement this authentication service.
```

### 4. Request Rule Validation

Ask the AI to validate its response against applicable rules:

```
Before completing your response, verify compliance with our Golden Rules 
and highlight any areas where rules are being applied.
```

### 5. Use Rule IDs

Assign unique identifiers to each rule for easy reference:

```
[GR-SEC-001] NEVER store passwords in plaintext or source code.
```

## Implementation in Popular AI IDEs

### Cursor Rules
Cursor allows you to create rule files in the `.cursor/rules/` directory:

```
# .cursor/rules/golden-rules.md

# Security Golden Rules
[GR-SEC-001] All user input must be validated and sanitized
[GR-SEC-002] Authentication must use secure, industry-standard protocols
[GR-SEC-003] Authorization checks must be applied at every entry point

# Code Quality Golden Rules
[GR-QUAL-001] All public methods must have documentation
[GR-QUAL-002] Test coverage must exceed 80% for all new code
[GR-QUAL-003] No method should exceed 30 lines of code
```

### Windsurf Rules
In Windsurf, create a `.windsurfrules` file in your project root:

```
# .windsurfrules

[security]
sanitize_inputs = true
encrypt_sensitive_data = true
audit_authorization_checks = true

[code_quality]
max_method_length = 30
required_test_coverage = 80
enforce_documentation = true
```

### GitHub Copilot
While GitHub Copilot doesn't support rule files directly, you can create reference files that it will consider in context:

```
# GOLDENRULES.md placed in your project root

# Organization Golden Rules
1. All code must follow SOLID principles
2. Security vulnerabilities from OWASP Top 10 must be actively prevented
3. All functions must include input validation
4. Comprehensive error handling is required
```

## Best Practices for Rule Creation

1. **Be Clear and Specific**: Avoid ambiguous rules that could be interpreted in multiple ways
2. **Prioritize Rules**: Indicate which rules are most critical to follow
3. **Provide Examples**: Include examples of both compliance and violations
4. **Explain Rationale**: Help developers understand why each rule exists
5. **Group Related Rules**: Organize rules into logical categories
6. **Limit Quantity**: Focus on the most important rules (quality over quantity)
7. **Review and Update**: Regularly review rules to ensure they remain relevant

## Existing Golden Rules in This Directory

This directory contains several sets of golden rules:

- [DevOps Rules](DevOps-rules.md): Rules for DevOps practices in AI-assisted development
- [Developer Experience Rules](DevXP-rules.md): Rules to improve the developer experience
- [Security Operations Rules](SecOps-rules.md): Security best practices for AI-assisted development

## Contributing New Golden Rules

To contribute a new set of golden rules:

1. Create a markdown file with a descriptive name (e.g., `accessibility-rules.md`)
2. Follow the structure used in existing rule files
3. Include examples demonstrating proper implementation
4. Submit a pull request following our [contribution guidelines](../../../CONTRIBUTING.md)
