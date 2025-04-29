# Developer Experience (DevXP) Golden Rules for AI-Assisted Development

## Applying Rules in AI IDE Contexts

AI-assisted development environments (like VS Code with GitHub Copilot, JetBrains with AI Assistant, etc.) introduce unique challenges and opportunities for maintaining consistent development practices. Golden rules are essential in these contexts for several reasons:

1. **Consistency Across AI Interactions**: AI assistants may generate code differently based on prompt phrasing, leading to inconsistencies if not governed by shared rules.

2. **Guardrails for AI-Generated Code**: Rules function as guardrails to ensure AI-generated code follows organizational standards and best practices.

3. **Knowledge Transfer**: Rules explicitly document organizational wisdom that AI models might not have learned during their training.

4. **Developer Alignment**: Clear rules help align developers' interactions with AI assistants, ensuring everyone follows similar practices regardless of their AI prompting skills.

### Implementation Methods

Golden rules can be enforced in AI-assisted development through:

- **Meta-Prompts**: Include rule snippets in prompts to AI assistants
- **IDE Extensions**: Use custom extensions that validate AI-generated code against rules
- **Documentation**: Keep rules in accessible documentation that developers can reference
- **Code Reviews**: Include rule compliance in peer review criteria
- **Automation**: Implement hooks in CI/CD pipelines to verify rule adherence

## Tested DevXP Golden Rules

### Decision Making & Collaboration

#### **USE DEFENSIVE PROGRAMMING!**
- Validate all inputs to ensure they meet expected formats and constraints.
- Use default values for optional parameters to avoid undefined behavior.
- Handle edge cases explicitly to prevent unexpected errors.
- Use try-catch blocks to gracefully handle exceptions and log errors.
- Limit the scope of variables to reduce unintended side effects.

#### **EVERYTIME YOU FACE A LOGIC DECISION, ASK THE USER!**
- Do not make assumptions about business logic or requirements
- Present options clearly with pros and cons when facing a decision point
- Explicitly ask for user input before proceeding with implementation
- Document the decision once made for future reference

```typescript
// Example prompt to AI:
// "I need to implement user authentication. Here are my requirements: [...]"

// AI should respond with:
/*
For user authentication, I see several options:
1. JWT-based authentication
   - Pros: Stateless, scalable
   - Cons: Token revocation challenges
2. Session-based authentication
   - Pros: Easy revocation, mature ecosystem
   - Cons: Requires session storage
3. OAuth integration
   - Pros: Delegates authentication, industry standard
   - Cons: More complex setup, dependency on providers

Which approach would you prefer for this implementation?
*/
```

#### **ALWAYS FOLLOW THE RULES!**
- Adhere to established patterns and guidelines without exception
- If a rule conflicts with a requirement, discuss the conflict explicitly
- Never silently bypass or ignore established rules
- Maintain a list of approved exceptions when necessary

### Process & Documentation

#### **ON EVERY DEMAND, ALWAYS CREATE A STEP-BY-STEP ON HOW TO ACT AND DISPLAY IT!**
- Break down complex tasks into clear, sequential steps
- Provide a visual roadmap before beginning implementation
- Reference specific documentation for each major step
- Update steps if the process changes during implementation

```markdown
# Implementation Plan: User Authentication System

1. **Setup Authentication Libraries**
   - Install required packages
   - Configure authentication middleware
   
2. **Create User Model**
   - Define schema with required security fields
   - Implement password hashing
   
3. **Build Authentication Endpoints**
   - Create login/signup routes
   - Implement token generation and validation
   
4. **Add Authentication Guards**
   - Protect restricted routes
   - Implement role-based access control
   
5. **Testing**
   - Write unit tests for authentication logic
   - Create integration tests for auth flow
   
6. **Documentation**
   - Document API endpoints
   - Create usage examples
```

#### **SAVE IN CONTEXT WHICH STEP YOU ARE INTO AND PROCEED TO THE NEXT STEP UPON FINISH**
- Clearly mark the current step in the process
- Track progress through complex operations
- Provide clear transition indicators between steps
- Maintain state awareness in long-running tasks

```bash
# Example of tracking in a deployment process:
echo "Step 1/5: Building application..."
npm run build
echo "✓ Build complete"

echo "Step 2/5: Running tests..."
npm run test
echo "✓ Tests passed"

# Current step
echo "Step 3/5: Deploying to staging..."
npm run deploy:staging
echo "✓ Staging deployment complete"

# Next steps
echo "Step 4/5: Running smoke tests..."
echo "Step 5/5: Promoting to production..."
```

### Patterns & Consistency

#### **EVERYTIME YOU CREATE A NEW TYPE OF RESOURCE, REGISTER A DOCUMENT ON ITS PATTERN**
- Document the structure and purpose of new resource types
- Provide examples of correct implementation
- Establish naming conventions and file organization
- Create templates for future instances of the resource

```markdown
# User Model Pattern

## Structure
- Models should be in `src/models` directory
- Filename should be singular, PascalCase (e.g., `User.ts`)
- Must implement the appropriate interface

## Properties
- Required properties first, optional later
- Include JSDoc comments for all properties
- Sensitive fields must be marked with `@sensitive` tag

## Methods
- CRUD methods follow repository pattern
- Validation methods prefixed with `validate`
- Transformation methods prefixed with `transform`

## Example
```typescript
// src/models/User.ts
import { BaseModel } from '../core/BaseModel';

/**
 * Represents a user in the system
 */
export class User extends BaseModel {
  /**
   * Unique identifier
   */
  id: string;
  
  /**
   * User's email address
   * @sensitive
   */
  email: string;
  
  // Additional properties...
}
```

#### **COMMIT INTO CODING PATTERNS**
- Establish and follow consistent patterns across the codebase
- Document patterns in accessible developer resources
- Use automated linting and formatting to enforce patterns
- Review pattern adherence in code reviews
- Evolve patterns intentionally and document changes

### Logging & Debugging

#### **USE LOGGING TO TRACK AND DEBUG APPLICATION**
- Include logging statements at key points in application flow
- Log entry and exit points of significant functions
- Include relevant context in all log messages
- Structure logs to be machine-parseable but human-readable

#### **USE THE SAME LOGGING PATTERN EVERYWHERE**
- Apply consistent formatting to all log statements
- Include standardized metadata (timestamp, service name, correlation ID)
- Use the same log structure across all services
- Ensure consistency in terminology and abbreviations

```typescript
// Consistent logging pattern example
logger.info('User registration initiated', { userId, source, referrer });
logger.debug('Validating user data', { userId, validationRules });
logger.warn('Invalid login attempt', { userId, attemptCount, ipAddress });
logger.error('Payment processing failed', { userId, orderId, errorCode, errorDetails });
```

#### **USE A CENTRALIZED LOGGING MODULE**
- Create a single logging module that all parts of the application use
- Configure logging behavior in one place
- Export convenient logging functions for different contexts
- Ensure all logging goes through this central module

```typescript
// src/utils/logger.ts
import winston from 'winston';

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  defaultMeta: { service: 'user-service' },
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ],
});

// Export consistent logging interface
export default {
  debug: (message: string, meta: object = {}) => logger.debug(message, meta),
  info: (message: string, meta: object = {}) => logger.info(message, meta),
  warn: (message: string, meta: object = {}) => logger.warn(message, meta),
  error: (message: string, meta: object = {}) => logger.error(message, meta),
  critical: (message: string, meta: object = {}) => logger.log('critical', message, meta),
};
```

#### **USE MULTIPLE LOG LEVELS**
- **DEBUG**: Detailed information, useful for development and troubleshooting
  ```typescript
  logger.debug('Processing array items', { count: items.length, processingMode });
  ```

- **INFO**: General information and security audit events
  ```typescript
  logger.info('User logged in successfully', { userId, loginMethod });
  ```

- **WARN**: Warning conditions, potential issues that aren't errors
  ```typescript
  logger.warn('API rate limit approaching', { endpoint, usagePercent: 85, clientId });
  ```

- **ERROR**: Error conditions and exceptions
  ```typescript
  logger.error('Database query failed', { query, params, errorMessage: err.message });
  ```

- **CRITICAL**: Critical conditions requiring immediate attention
  ```typescript
  logger.critical('System shutdown initiated due to resource exhaustion', { availableMemory, activeConnections });
  ```

### Documentation

#### **ALWAYS CREATE RICH DOCUMENTATION OVER EVERYTHING THAT IS CONFIGURABLE**
- Document all configuration options with examples
- Explain default values and their implications
- Include validation rules and acceptable value ranges
- Provide context for when different configurations should be used

```typescript
/**
 * Connection pool configuration
 * 
 * @property {number} min - Minimum number of connections to maintain in the pool
 *                          Default: 5
 *                          Recommended: 5-10 for most applications, 10-20 for high-traffic
 * 
 * @property {number} max - Maximum number of connections the pool can create
 *                          Default: 20
 *                          Note: Should be aligned with database server limitations
 * 
 * @property {number} idleTimeoutMillis - How long a connection can be idle before being removed
 *                                        Default: 30000 (30 seconds)
 *                                        Set lower for highly contested environments
 * 
 * @property {boolean} ssl - Whether to use SSL for connections
 *                           Default: true in production, false in development
 *                           Required for production environments
 */
export interface DbPoolConfig {
  min: number;
  max: number;
  idleTimeoutMillis: number;
  ssl: boolean;
}
```

#### **FOLLOW THE SAME DOCUMENTATION STYLE EVERYWHERE**
- Standardize documentation format across the codebase
- Use consistent terminology and conventions
- Structure all documentation sections in the same order
- Use the same tone and level of detail throughout