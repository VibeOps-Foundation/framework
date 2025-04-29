# Using Rules in AI IDEs: Success Criteria & KPIs

This directory contains rules for implementing **Success Criteria & KPIs** - metrics and evaluation frameworks to assess the effectiveness of AI-assisted development and measure the actual value added.

## Understanding Rules in AI-Assisted Development

Rules in AI IDEs serve as guidance systems that direct AI assistants' behavior. For Success Criteria & KPIs, these rules help establish measurable standards for evaluating AI assistance quality and defining what constitutes successful AI collaboration.

## Global vs. Local Rules

### Global Rules
- **Definition**: Apply across your entire workspace or organization
- **Location**: Typically stored in central repositories or global configuration files
- **Use Cases**:
  - Organization-wide quality metrics
  - Standard performance indicators
  - Cross-project success benchmarks
  - Global efficiency measurements

### Local Rules
- **Definition**: Apply to specific projects, files, or directories
- **Location**: Stored within project directories or project-specific configuration files
- **Use Cases**:
  - Project-specific quality metrics
  - Team-level performance indicators
  - Domain-specific success criteria
  - Component-level evaluation frameworks

## How Rules Are Evaluated

AI assistants typically evaluate success criteria rules in the following order:

1. **Global success metrics** provide baseline expectations
2. **Project-specific criteria** adjust expectations for project context
3. **Task-type criteria** apply specialized metrics for different operations
4. **Component-level metrics** may impose stricter requirements for critical areas
5. **Individual interaction metrics** measure specific AI assistant responses

The evaluation can be used both to guide the AI's output quality and to measure the effectiveness of AI-assisted development over time.

## Enforcing Strict Rule Adherence

To ensure AI assistants strictly follow your success criteria rules:

### 1. Define Clear Quality Thresholds

Establish specific, measurable quality bars:

```
RULE: GENERATED CODE MUST MAINTAIN AT LEAST 90% TEST COVERAGE
```

### 2. Require Self-Assessment

Ask the AI to evaluate its own output against criteria:

```
RULE: RATE YOUR RESPONSE AGAINST OUR SUCCESS CRITERIA AND IDENTIFY AREAS FOR IMPROVEMENT
```

### 3. Reference Specific Metrics in Prompts

Direct the AI to prioritize certain success metrics:

```
Following our performance optimization KPI framework (especially KPI-PERF-003), 
help me improve the efficiency of this database query.
```

### 4. Request Compliance Statements

Have the AI confirm compliance with success criteria:

```
Before completing your response, verify and state how your solution meets our 
success criteria for maintainability and performance.
```

### 5. Include Measurement Guidelines

Provide clear guidance on how success will be measured:

```
RULE: SOLUTIONS WILL BE EVALUATED ON: 
1. TIME COMPLEXITY (40%)
2. SPACE EFFICIENCY (30%) 
3. READABILITY (20%)
4. EDGE CASE HANDLING (10%)
```

## Implementation in Popular AI IDEs

### Cursor Rules
Cursor allows you to create rule files in the `.cursor/rules/` directory:

```
# .cursor/rules/success-criteria.md

# Code Quality Success Criteria
[KPI-001] Code must maintain cyclomatic complexity under 15 per function
[KPI-002] Documentation must cover all public APIs with examples
[KPI-003] Performance must not degrade by more than 5% from baseline
[KPI-004] All solutions must handle identified edge cases
```

### Windsurf Rules
In Windsurf, create a `.windsurfrules` file in your project root:

```
# .windsurfrules

[success_criteria]
test_coverage_minimum = 80
max_cyclomatic_complexity = 15
documentation_completeness = 90
performance_benchmark = "baseline * 1.1"
```

### GitHub Copilot
While GitHub Copilot doesn't support rule files directly, you can create reference files that it will consider in context:

```
# SUCCESSCRITERIA.md placed in your project root

# AI Assistance Success Metrics
1. Generated code must pass all existing tests
2. New features must include appropriate tests
3. Code must follow existing architectural patterns
4. Documentation must be complete and accurate
5. Performance must meet specified benchmarks
```

## Best Practices for Rule Creation

1. **Make Criteria Measurable**: Define success in quantifiable terms
2. **Balance Multiple Dimensions**: Include metrics for quality, performance, and user experience
3. **Align with Business Goals**: Connect technical metrics to business outcomes
4. **Define Minimum Thresholds**: Establish clear pass/fail criteria
5. **Include Leading Indicators**: Identify early signals of success or failure
6. **Review and Update**: Regularly revisit criteria as project needs evolve
7. **Prioritize Metrics**: Indicate which metrics matter most in different contexts

## Contributing New Rules

To add a new Success Criteria or KPI rule to this directory:

1. Create a markdown file with a descriptive name (e.g., `performance-kpis.md`)
2. Follow the structure used in existing rule files
3. Include clear, measurable definitions of success
4. Provide methods for measuring and evaluating each metric
5. Submit a pull request following our [contribution guidelines](../../../CONTRIBUTING.md)
