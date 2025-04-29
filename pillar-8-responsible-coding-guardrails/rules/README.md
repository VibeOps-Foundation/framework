# Using Rules in AI IDEs: Responsible Coding Guardrails

This directory contains rules for implementing **Responsible Coding Guardrails** - security, privacy, and quality safeguards to ensure AI-generated code meets organizational standards.

## Understanding Rules in AI-Assisted Development

Rules in AI IDEs provide structured guidance for AI assistants' behavior. For Responsible Coding Guardrails, these rules establish boundaries and requirements to prevent AI from generating code with security vulnerabilities, privacy issues, or quality problems.

## Global vs. Local Rules

### Global Rules
- **Definition**: Apply across your entire workspace or organization
- **Location**: Typically stored in central repositories or global configuration files
- **Use Cases**:
  - Organization-wide security policies
  - Standard privacy requirements
  - Universal code quality standards
  - Cross-project compliance requirements

### Local Rules
- **Definition**: Apply to specific projects, files, or directories
- **Location**: Stored within project directories or project-specific configuration files
- **Use Cases**:
  - Project-specific security requirements
  - Domain-specific compliance needs
  - Component-level quality standards
  - Team-specific coding practices

## How Rules Are Evaluated

AI assistants typically evaluate responsible coding guardrail rules in the following order:

1. **Security guardrails** apply first to prevent critical vulnerabilities
2. **Privacy protection rules** ensure sensitive data is handled properly
3. **Quality standard rules** enforce maintainable, efficient code
4. **Compliance-specific rules** address regulatory requirements
5. **Style and convention rules** ensure consistency with existing code

The evaluation process creates multiple layers of protection, ensuring that generated code is safe, privacy-conscious, high-quality, and compliant.

## Enforcing Strict Rule Adherence

To ensure AI assistants strictly follow your responsible coding guardrails:

### 1. Use Clear Security Requirements

Explicitly define security boundaries:

```
RULE: NEVER GENERATE CODE THAT STORES CREDENTIALS IN SOURCE CODE
```

### 2. Provide Specific Constraint Patterns

Define patterns that must be avoided or required:

```
RULE: ALL USER INPUT MUST BE VALIDATED AND SANITIZED BEFORE USE
RULE: DATABASE QUERIES MUST ALWAYS USE PARAMETERIZED STATEMENTS
```

### 3. Reference Guardrails in Prompts

Direct the AI to follow specific guardrails:

```
Following our Responsible Coding Guardrails (specifically rules RCG-SEC-001 and RCG-SEC-002), 
help me implement this user authentication feature.
```

### 4. Request Explicit Verification

Ask the AI to validate its output against guardrails:

```
Before completing your response, verify that your code meets our security guardrails
and explicitly confirm compliance with each relevant rule.
```

### 5. Require Security Analysis

Have the AI assess its own code for vulnerabilities:

```
RULE: FOR SECURITY-SENSITIVE FUNCTIONS, INCLUDE A THREAT ANALYSIS COMMENT
IDENTIFYING POTENTIAL VULNERABILITIES AND MITIGATIONS
```

## Implementation in Popular AI IDEs

### Cursor Rules
Cursor allows you to create rule files in the `.cursor/rules/` directory:

```
# .cursor/rules/security-guardrails.md

# Security Guardrails
[RCG-SEC-001] All user input must be validated and sanitized
[RCG-SEC-002] Authentication must use secure, industry-standard methods
[RCG-SEC-003] Authorization checks must be applied at all access points
[RCG-SEC-004] Data must be encrypted in transit and at rest
```

### Windsurf Rules
In Windsurf, create a `.windsurfrules` file in your project root:

```
# .windsurfrules

[security_guardrails]
validate_user_input = true
use_parameterized_queries = true
require_encryption = true
enforce_authentication = true
prevent_hardcoded_secrets = true
```

### GitHub Copilot
While GitHub Copilot doesn't support rule files directly, you can create reference files that it will consider in context:

```
# SECURITYGUARDRAILS.md placed in your project root

# Security Requirements for Generated Code
1. Never store secrets or credentials in code
2. Always validate and sanitize user input
3. Use parameterized queries for database access
4. Implement proper error handling without leaking information
5. Apply principle of least privilege
```

## Best Practices for Rule Creation

1. **Be Specific and Actionable**: Define clear, implementable requirements
2. **Provide Both Positive and Negative Examples**: Show what to do and what to avoid
3. **Align with Industry Standards**: Reference OWASP, NIST, or other standards
4. **Include Reasoning**: Explain why each rule is important
5. **Define Severity Levels**: Indicate which rules are non-negotiable vs. recommended
6. **Provide Implementation Guidance**: Include examples of compliant code
7. **Update for New Threats**: Regularly review and update security guardrails

## Contributing New Rules

To add a new Responsible Coding Guardrail rule to this directory:

1. Create a markdown file with a descriptive name (e.g., `api-security-rules.md`)
2. Follow the structure used in existing rule files
3. Include examples of both secure and vulnerable code
4. Provide clear implementation guidance
5. Submit a pull request following our [contribution guidelines](../../../CONTRIBUTING.md)
