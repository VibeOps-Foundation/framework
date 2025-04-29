# Using Rules in AI IDEs: Trustworthy MCP Implementation

This directory contains rules specific to implementing **Trustworthy Model Context Providers (MCPs)** in your AI-assisted development workflow. These rules help ensure that AI assistants consistently follow secure patterns when accessing systems or sensitive data. It is important to note that while rules can define **how the model interacts with the MCP**, they **cannot delimit or restrict the interactions of the MCP itself**. MCPs operate independently of the rules governing AI assistants and must be designed to handle interactions securely and robustly.

## Understanding Rules in AI-Assisted Development

Rules in AI IDEs are instructions or guardrails that guide the behavior of AI coding assistants. They help maintain consistency, enforce best practices, and improve security across projects. For Trustworthy MCPs, rules define how AI should interact with systems and handle sensitive information, but they do not impose constraints on the MCP's internal operations or its interactions with external systems.

## Global vs. Local Rules

### Global Rules
- **Definition**: Apply across your entire workspace or organization
- **Location**: Typically stored in central repositories or global configuration files
- **Use Cases**:
    - Enterprise-wide security policies
    - Organizational coding standards
    - Cross-project architectural principles
    - Standard MCP interaction patterns for AI assistants

### Local Rules
- **Definition**: Apply to specific projects, files, or directories
- **Location**: Stored within project directories or project-specific configuration files
- **Use Cases**:
    - Project-specific MCP interaction guidelines for AI assistants
    - Domain-specific data handling requirements
    - Project-unique security requirements
    - Override or extend global rules for specific use cases

## How Rules Are Evaluated

AI assistants typically evaluate rules in the following order:

1. **Global rules** are loaded first as a baseline
2. **Project-specific rules** are applied next, potentially overriding global rules
3. **Directory-level rules** may further refine behavior for specific components
4. **File-level rules** provide the most granular control
5. **Prompt-specific rules** can override all others for a single interaction

It is critical to understand that these rules govern the AI assistant's behavior and interactions with the MCP but do not dictate or limit the MCP's own functionality or decision-making processes.

## Enforcing Strict Rule Adherence

To ensure AI assistants strictly follow your rules:

### 1. Explicit Rule Declaration

Use clear, unambiguous language in your rule definitions:

```
RULE: ALWAYS ENCRYPT SENSITIVE DATA BEFORE STORING OR TRANSMITTING
```

### 2. Priority Indicators

Assign priority levels to emphasize critical rules:

```
[CRITICAL] NEVER STORE AUTHENTICATION CREDENTIALS IN SOURCE CODE
```

### 3. Rule Preambles in Prompts

Include rule references at the beginning of your prompts:

```
Following the Trustworthy MCP rules (especially MCP-SEC-1, MCP-SEC-2), 
help me create a database access provider.
```

### 4. Consequence Statements

Clearly state the negative outcomes of rule violations:

```
WARNING: Failing to follow secure MCP patterns may result in data leakage
```

### 5. Validation Requests

Ask the AI to validate its response against applicable rules:

```
Before finalizing, please validate that your response follows all Trustworthy MCP rules.
```

## Implementation in Popular AI IDEs

### Cursor Rules
Cursor allows you to create rule files in the `.cursor/rules/` directory:

```
# .cursor/rules/mcp-rules.md

# MCP Security Rules
1. All database connections must use parameterized queries
2. API keys must be loaded from environment variables only
3. Input from external systems must be validated before processing
```

### Windsurf Rules
In Windsurf, create a `.windsurfrules` file in your project root:

```
# .windsurfrules

[mcp.security]
sanitize_inputs = true
use_encryption = true
validate_output = true
log_access = true

[mcp.implementation]
pattern = "provider_factory"
connection_pooling = true
```

### GitHub Copilot
While GitHub Copilot doesn't support rule files directly, you can create reference files that it will consider in context:

```
# MCPGUIDELINES.md placed in your project root

# Model Context Provider Guidelines
1. MCPs must implement the IMCPProvider interface
2. All MCPs must support connection timeouts
3. MCPs must implement proper error handling
```

## Best Practices for Rule Creation

1. **Be Specific**: Avoid ambiguous or vague rules
2. **Provide Examples**: Include examples of both compliance and violations
3. **Categorize Rules**: Organize rules by category (security, performance, style)
4. **Include Rationale**: Explain why each rule exists
5. **Version Rules**: Track changes to rules over time
6. **Test Rules**: Verify that AI assistants correctly interpret and follow your rules
7. **Document Exceptions**: Clearly document when exceptions to rules are permitted

## Contributing New Rules

To add a new Trustworthy MCP rule to this directory:

1. Create a markdown file with a descriptive name (e.g., `data-access-rules.md`)
2. Follow the template structure provided in `rule-template.md`
3. Ensure your rule includes examples of both correct and incorrect implementations
4. Submit a pull request following our [contribution guidelines](../../../CONTRIBUTING.md) Rules in AI IDEs: Trustworthy MCP Implementation