# DevOps Golden Rules for AI-Assisted Development

## Applying DevOps Rules in AI IDE Contexts

DevOps practices are essential for maintaining quality, efficiency, and reliability throughout the software development lifecycle. When working with AI-assisted development, these rules become even more important as they provide structure and consistency to potentially varied AI outputs. Implementing DevOps golden rules in AI contexts offers several benefits:

1. **Consistency Across Generated Code**: Ensures AI-generated code follows the same operational standards regardless of when or how it was created.

2. **Quality Assurance**: Maintains high-quality standards through automated testing and validation processes for AI-assisted development.

3. **Operational Reliability**: Guarantees that AI-generated code can be reliably deployed, scaled, and maintained in production environments.

4. **Cross-team Collaboration**: Facilitates better collaboration between development and operations teams when working with AI tools.

### Implementation Methods in AI Workflows

DevOps rules can be enforced in AI-assisted development through:

- **CI/CD Integration**: Automated verification of AI-generated code against DevOps standards
- **Template Repositories**: Providing AI with access to standardized project templates
- **Custom AI Prompts**: Including DevOps requirements in AI instructions
- **Pre-commit Hooks**: Validating DevOps compliance before code is committed
- **Documentation Generation**: Using AI to maintain DevOps documentation

## TESTED DevOps Golden Rules

### Code Quality and Design

#### **FOCUS ON KISS AND DRY PRINCIPLES**
- Keep It Simple, Stupid: Favor simplicity over complexity in design
- Don't Repeat Yourself: Avoid code duplication through proper abstraction
- Use clear, descriptive naming conventions
- Break complex functionality into smaller, focused components
- Regularly refactor code to improve clarity and maintainability

```typescript
// BAD: Violating KISS and DRY principles
function getUserData(userId: string) {
  // Complex, nested logic
  const user = db.findUser(userId);
  if (user) {
    const permissions = db.getUserPermissions(userId);
    const profile = db.getUserProfile(userId);
    return {
      id: user.id,
      name: user.name,
      email: user.email,
      permissions: permissions,
      profile: profile
    };
  } else {
    return null;
  }
}

function getAdminData(adminId: string) {
  // Duplicated logic with minor variations
  const admin = db.findUser(adminId);
  if (admin) {
    const permissions = db.getUserPermissions(adminId);
    const profile = db.getUserProfile(adminId);
    return {
      id: admin.id,
      name: admin.name,
      email: admin.email,
      permissions: permissions,
      profile: profile,
      isAdmin: true
    };
  } else {
    return null;
  }
}

// GOOD: Following KISS and DRY principles
async function getUserById(userId: string, includeDetails = true): Promise<User | null> {
  const user = await userRepository.findById(userId);
  
  if (!user) {
    return null;
  }
  
  if (!includeDetails) {
    return user;
  }
  
  return enrichUserWithDetails(user);
}

async function enrichUserWithDetails(user: User): Promise<EnrichedUser> {
  const [permissions, profile] = await Promise.all([
    permissionRepository.getForUser(user.id),
    profileRepository.getForUser(user.id)
  ]);
  
  return {
    ...user,
    permissions,
    profile
  };
}

// Usage
const regularUser = await getUserById(userId);
const adminUser = await getUserById(adminId);
```

### Testing and Quality Assurance

#### **USE TDD, WRITE TESTS BEFORE CODE, AIM TO 100% CODE COVERAGE**
- Start by writing failing tests that define expected behavior
- Implement only the code necessary to pass the tests
- Refactor code while maintaining test coverage
- Include unit, integration, and end-to-end tests as appropriate
- Automate test execution in the CI pipeline

```typescript
// Example of Test-Driven Development workflow

// 1. Write the test first
import { describe, it, expect } from 'vitest';
import { calculateDiscountedPrice } from './pricing';

describe('calculateDiscountedPrice', () => {
  it('applies percentage discount correctly', () => {
    // Define expected behavior before implementation
    expect(calculateDiscountedPrice(100, { type: 'percentage', value: 20 })).toBe(80);
  });
  
  it('applies fixed amount discount correctly', () => {
    expect(calculateDiscountedPrice(100, { type: 'fixed', value: 15 })).toBe(85);
  });
  
  it('prevents negative prices after discount', () => {
    expect(calculateDiscountedPrice(10, { type: 'fixed', value: 15 })).toBe(0);
  });
  
  it('handles zero price correctly', () => {
    expect(calculateDiscountedPrice(0, { type: 'percentage', value: 20 })).toBe(0);
  });
});

// 2. Implement the code to pass the test
type DiscountType = 'percentage' | 'fixed';

interface Discount {
  type: DiscountType;
  value: number;
}

export function calculateDiscountedPrice(originalPrice: number, discount: Discount): number {
  if (originalPrice <= 0) return 0;
  
  let finalPrice: number;
  
  if (discount.type === 'percentage') {
    finalPrice = originalPrice * (1 - discount.value / 100);
  } else {
    finalPrice = originalPrice - discount.value;
  }
  
  // Prevent negative prices
  return Math.max(finalPrice, 0);
}

// 3. Run tests to verify - all tests should pass
```

#### **ALWAYS KEEP TESTS UPDATED!**
- Update tests whenever requirements or functionality changes
- Delete tests for removed functionality
- Add tests for new features before implementation
- Maintain accurate test documentation
- Use tests as living documentation of system behavior

```typescript
// Example of updating tests with changing requirements

// Original feature and test
export function validatePassword(password: string): boolean {
  // Original requirement: at least 8 characters
  return password.length >= 8;
}

describe('validatePassword', () => {
  it('accepts passwords with at least 8 characters', () => {
    expect(validatePassword('password123')).toBe(true);
    expect(validatePassword('short')).toBe(false);
  });
});

// When requirements change, update tests first
describe('validatePassword', () => {
  it('accepts passwords with at least 8 characters', () => {
    expect(validatePassword('password123')).toBe(true);
    expect(validatePassword('short')).toBe(false);
  });
  
  // New test for updated requirements
  it('requires at least one number', () => {
    expect(validatePassword('password123')).toBe(true);
    expect(validatePassword('passwordABC')).toBe(false);
  });
  
  it('requires at least one special character', () => {
    expect(validatePassword('password1#')).toBe(true);
    expect(validatePassword('password123')).toBe(false);
  });
});

// Then update implementation to match
export function validatePassword(password: string): boolean {
  // Updated requirements: at least 8 chars, 1 number, and 1 special char
  const hasMinLength = password.length >= 8;
  const hasNumber = /\d/.test(password);
  const hasSpecial = /[!@#$%^&*(),.?":{}|<>]/.test(password);
  
  return hasMinLength && hasNumber && hasSpecial;
}
```

### Dependency Management

#### **ALWAYS USE LTS VERSIONS OF LIBS AND LANGUAGES**
- Specify Long-Term Support (LTS) versions in package managers
- Document version requirements in project documentation
- Implement version checking in CI/CD pipelines
- Plan for regular updates to maintain LTS compatibility
- Balance stability with necessary features

```json
// package.json example with LTS versions
{
  "name": "reliable-service",
  "version": "1.0.0",
  "engines": {
    "node": ">=18.12.0 <19.0.0"
  },
  "dependencies": {
    "express": "^4.18.2",
    "mongoose": "^7.6.3",
    "winston": "^3.10.0"
  },
  "devDependencies": {
    "typescript": "^5.2.2",
    "jest": "^29.7.0",
    "eslint": "^8.52.0"
  },
  "scripts": {
    "check-versions": "npx check-node-version --node '>= 18.12.0 < 19.0.0'",
    "prestart": "npm run check-versions",
    "start": "node dist/server.js"
  }
}
```

```dockerfile
# Dockerfile using LTS versions
FROM node:18.12-alpine

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm ci --only=production

COPY . .

EXPOSE 8080
CMD ["node", "dist/server.js"]
```

### API Development

#### **WHEN CREATING RESTFUL APIS, FOLLOW THE OPENAPI 3.1 PATTERN**
- Define API contracts using OpenAPI Specification 3.1
- Design RESTful endpoints following REST principles
- Use standard HTTP methods appropriately
- Implement consistent error handling
- Include comprehensive parameter validation

```yaml
# Example OpenAPI 3.1 specification (openapi.yaml)
openapi: 3.1.0
info:
  title: Product API
  description: API for managing product information
  version: 1.0.0
servers:
  - url: https://api.example.com/v1
    description: Production server
  - url: https://dev-api.example.com/v1
    description: Development server
paths:
  /products:
    get:
      summary: Get a list of products
      operationId: getProducts
      parameters:
        - name: category
          in: query
          description: Filter by product category
          schema:
            type: string
        - name: limit
          in: query
          description: Maximum number of products to return
          schema:
            type: integer
            default: 20
      responses:
        '200':
          description: Successfully retrieved products
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Product'
        '400':
          $ref: '#/components/responses/BadRequest'
        '500':
          $ref: '#/components/responses/ServerError'
    post:
      summary: Create a new product
      operationId: createProduct
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/ProductInput'
      responses:
        '201':
          description: Product created successfully
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Product'
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '500':
          $ref: '#/components/responses/ServerError'
  /products/{id}:
    get:
      summary: Get a product by ID
      operationId: getProductById
      parameters:
        - name: id
          in: path
          required: true
          description: Product ID
          schema:
            type: string
      responses:
        '200':
          description: Successfully retrieved product
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Product'
        '404':
          $ref: '#/components/responses/NotFound'
        '500':
          $ref: '#/components/responses/ServerError'
components:
  schemas:
    Product:
      type: object
      properties:
        id:
          type: string
          format: uuid
        name:
          type: string
        price:
          type: number
          format: float
        category:
          type: string
        createdAt:
          type: string
          format: date-time
      required:
        - id
        - name
        - price
    ProductInput:
      type: object
      properties:
        name:
          type: string
          minLength: 3
          maxLength: 100
        price:
          type: number
          format: float
          minimum: 0
        category:
          type: string
      required:
        - name
        - price
  responses:
    BadRequest:
      description: Invalid request
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
    NotFound:
      description: Resource not found
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
    Unauthorized:
      description: Authentication required
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
    ServerError:
      description: Internal server error
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/Error'
```
### Containerization and Environment Management

#### **USE DOCKER AND DOCKER-COMPOSE, KEEP DOCKERFILE UPDATED!**
- Containerize applications for consistent environments
- Use multi-stage builds for optimal container size
- Include health checks in container configurations
- Keep base images updated with security patches
- Document container configurations and requirements

```dockerfile
# Example of a well-maintained Dockerfile with multi-stage build
# Build stage
FROM node:18.12-alpine AS builder

WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm ci

# Copy source code and build
COPY tsconfig.json ./
COPY src/ ./src/
RUN npm run build

# Production stage
FROM node:18.12-alpine AS production

# Set environment variables
ENV NODE_ENV=production
ENV PORT=8080

# Set non-root user for security
RUN addgroup -g 1001 appuser && \
    adduser -u 1001 -G appuser -s /bin/sh -D appuser

WORKDIR /app

# Copy only production dependencies
COPY package*.json ./
RUN npm ci --only=production && npm cache clean --force

# Copy built application from builder stage
COPY --from=builder /app/dist ./dist

# Set proper ownership
RUN chown -R appuser:appuser /app

# Switch to non-root user
USER appuser

# Expose port and add healthcheck
EXPOSE ${PORT}
HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD wget -q --spider http://localhost:${PORT}/health || exit 1

# Set startup command
CMD ["node", "dist/server.js"]
```

```yaml
# Example docker-compose.yml
version: '3.8'

services:
  api:
    build:
      context: .
      dockerfile: Dockerfile
      target: production
    ports:
      - "${API_PORT:-8080}:8080"
    environment:
      - NODE_ENV=production
      - DB_HOST=db
      - DB_PORT=5432
      - DB_USER=${DB_USER}
      - DB_PASSWORD=${DB_PASSWORD}
      - DB_NAME=${DB_NAME}
      - REDIS_HOST=redis
      - REDIS_PORT=6379
    depends_on:
      - db
      - redis
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "wget", "-q", "--spider", "http://localhost:8080/health"]
      interval: 30s
      timeout: 5s
      retries: 3
      start_period: 15s

  db:
    image: postgres:15-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=${DB_USER}
      - POSTGRES_PASSWORD=${DB_PASSWORD}
      - POSTGRES_DB=${DB_NAME}
    ports:
      - "${DB_PORT:-5432}:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${DB_USER} -d ${DB_NAME}"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 10s

  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data
    ports:
      - "${REDIS_PORT:-6379}:6379"
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 3

volumes:
  postgres_data:
  redis_data:
```

#### **ALWAYS SET ENVIRONMENT VARIABLES ON EVERYTHING THAT IS CONFIGURABLE!**
- Use environment variables for all configuration that varies between environments
- Provide sensible defaults where appropriate
- Document all environment variables and their purpose
- Use a centralized configuration service in complex systems
- Include validation for required environment variables

#### **ALWAYS USE ENVIRONMENT VARIABLES FOR KEYS**
- Never hardcode API keys, passwords, or secrets
- Store sensitive information in environment variables
- Use secret management services for production environments
- Implement secure handling of secrets in CI/CD pipelines
- Rotate keys regularly and update environment variables

```typescript
// Secure API client using environment variables for keys
import axios, { AxiosInstance } from 'axios';

class PaymentGatewayClient {
  private client: AxiosInstance;
  private apiKey: string;
  
  constructor() {
    // Get API key from environment
    this.apiKey = process.env.PAYMENT_API_KEY || '';
    
    if (!this.apiKey) {
      throw new Error('Missing required PAYMENT_API_KEY environment variable');
    }
    
    // Create HTTP client with auth header
    this.client = axios.create({
      baseURL: process.env.PAYMENT_API_URL || 'https://api.payment-gateway.com',
      headers: {
        'Authorization': `Bearer ${this.apiKey}`,
        'Content-Type': 'application/json',
      },
      timeout: parseInt(process.env.PAYMENT_API_TIMEOUT || '5000', 10),
    });
  }
  
  async createPayment(amount: number, currency: string, description: string) {
    try {
      const response = await this.client.post('/payments', {
        amount,
        currency,
        description,
      });
      return response.data;
    } catch (error) {
      console.error('Payment gateway error:', error);
      throw new Error('Failed to process payment');
    }
  }
  
  // Additional methods...
}

export default new PaymentGatewayClient();
```

#### **ALWAYS PREPARE TO MULTI-ENVIRONMENT**
- Design systems to work across development, staging, and production environments
- Use configuration-based feature flags for environment-specific behaviors
- Implement automated environment provisioning
- Ensure parity between environments where possible
- Document environment differences and requirements

```typescript
// Environment-aware configuration and feature flags
import dotenv from 'dotenv';

// Load environment-specific configuration
function loadEnvironmentConfig() {
  const environment = process.env.NODE_ENV || 'development';
  
  // Load base config first
  dotenv.config({ path: '.env' });
  
  // Then override with environment-specific config
  dotenv.config({ path: `.env.${environment}`, override: true });
  
  return {
    environment,
    isProduction: environment === 'production',
    isStaging: environment === 'staging',
    isDevelopment: environment === 'development',
    isTest: environment === 'test',
  };
}

const env = loadEnvironmentConfig();

// Feature flag management based on environment
class FeatureFlags {
  private flags: Record<string, boolean> = {};
  
  constructor() {
    // Parse feature flags from environment variables
    const flagPrefix = 'FEATURE_';
    
    // Load all environment variables that start with FEATURE_
    Object.keys(process.env)
      .filter(key => key.startsWith(flagPrefix))
      .forEach(key => {
        const flagName = key.replace(flagPrefix, '').toLowerCase();
        this.flags[flagName] = process.env[key] === 'true';
      });
      
    // Add environment-based default flags
    this.flags = {
      ...this.flags,
      // Features always enabled in development
      'detailed_logging': env.isDevelopment || env.isTest,
      'mock_external_services': env.isDevelopment || env.isTest,
      'swagger_ui': env.isDevelopment || env.isStaging,
      
      // Features enabled in staging for testing
      'new_payment_flow': env.isStaging || this.flags['new_payment_flow'],
      
      // Production safety features
      'rate_limiting': env.isProduction || this.flags['rate_limiting'],
      'enhanced_security': env.isProduction || this.flags['enhanced_security'],
    };
  }
  
  isEnabled(flagName: string): boolean {
    return !!this.flags[flagName.toLowerCase()];
  }
  
  getAllFlags(): Record<string, boolean> {
    return { ...this.flags };
  }
}

export const featureFlags = new FeatureFlags();

// Example usage
if (featureFlags.isEnabled('new_payment_flow')) {
  // Use new payment processing logic
} else {
  // Use legacy payment processing logic
}

// Environment-specific database connection
const dbConfig = {
  host: process.env.DB_HOST || 'localhost',
  port: parseInt(process.env.DB_PORT || '5432', 10),
  database: process.env.DB_NAME || 'app',
  username: process.env.DB_USER || 'postgres',
  password: process.env.DB_PASSWORD || '',
  // Apply different connection pooling based on environment
  pool: {
    min: env.isProduction ? 5 : 2,
    max: env.isProduction ? 30 : 10,
    idle: 10000,
  },
  // Enable SSL only in production and staging
  ssl: env.isProduction || env.isStaging,
};
```

### Code Maintenance

#### **KEEP DEVELOPMENT STAGE AS CLEAN AS POSSIBLE**
- Implement automated code formatting and linting
- Use pre-commit hooks to prevent committing problematic code
- Regularly review and refactor code
- Remove redundant or deprecated code
- Maintain a clean git history with meaningful commits

```json
// Example .prettierrc for consistent code formatting
{
  "semi": true,
  "trailingComma": "all",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2
}
```

```json
// Example .eslintrc.json for code quality enforcement
{
  "root": true,
  "parser": "@typescript-eslint/parser",
  "plugins": [
    "@typescript-eslint",
    "prettier",
    "import"
  ],
  "extends": [
    "eslint:recommended",
    "plugin:@typescript-eslint/recommended",
    "plugin:prettier/recommended",
    "plugin:import/errors",
    "plugin:import/warnings",
    "plugin:import/typescript"
  ],
  "rules": {
    "no-console": "warn",
    "no-unused-vars": "off",
    "@typescript-eslint/no-unused-vars": ["error", {
      "argsIgnorePattern": "^_",
      "varsIgnorePattern": "^_"
    }],
    "import/order": ["error", {
      "groups": [
        "builtin",
        "external",
        "internal",
        ["parent", "sibling"],
        "index"
      ],
      "newlines-between": "always",
      "alphabetize": { "order": "asc", "caseInsensitive": true }
    }]
  }
}
```

```yaml
# Example pre-commit configuration (.pre-commit-config.yaml)
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: no-commit-to-branch
        args: [--branch, main, --branch, production]
  
  - repo: https://github.com/pre-commit/mirrors-prettier
    rev: v3.0.3
    hooks:
      - id: prettier
        types_or: [javascript, typescript, json]
  
  - repo: https://github.com/pre-commit/mirrors-eslint
    rev: v8.50.0
    hooks:
      - id: eslint
        files: \.[jt]sx?$
        types: [file]
        additional_dependencies:
          - eslint@8.50.0
          - "@typescript-eslint/eslint-plugin@6.7.3"
          - "@typescript-eslint/parser@6.7.3"
          - "eslint-config-prettier@9.0.0"
          - "eslint-plugin-prettier@5.0.0"
          - "eslint-plugin-import@2.28.1"
          - "typescript@5.2.2"
```

#### **WHENEVER YOU FIND A DEADCODE, ASK THE USER PERMISSION TO REMOVE IT**
- Identify unused functions, variables, and imports
- Flag dead code for review before removal
- Document the identified code with clear justification
- Obtain explicit permission before removing code
- Track code removal through version control

```typescript
// Example of identifying and flagging dead code

// FLAGGED: This function appears to be unused in the project
// Last modified: 6 months ago by developer@example.com
// To remove this code, please get permission from the team lead
/**
 * @deprecated This function is no longer used and should be removed
 */
function legacyPaymentProcessor(amount: number, accountId: string) {
  console.log(`Processing payment of ${amount} for account ${accountId}`);
  // implementation...
}

// Current implementation used throughout the application
function processPayment(paymentDetails: PaymentDetails) {
  console.log(`Processing payment for ${paymentDetails.accountId}`);
  // implementation...
}
```

```typescript
// Example prompting process for dead code removal
/*
  DEAD CODE IDENTIFIED
  
  The following function in src/services/payment.ts appears to be unused:

  function legacyPaymentProcessor(amount: number, accountId: string) {
    console.log(`Processing payment of ${amount} for account ${accountId}`);
    // implementation...
  }
  
  Analysis:
  - No references found in codebase
  - Last modified 6 months ago
  - Similar functionality exists in processPayment function
  
  Recommendation:
  Remove this function to improve code maintainability.
  
  Do you want to proceed with removing this function? (yes/no)
*/
```

#### **UPON UNIT-TESTING, EXTERNAL DEPENDENCIES SUCH AS DATABASES OR APIS SHOULD BE MOCKED!**
- Use mocking frameworks to isolate tests from external dependencies
- Create consistent mocking patterns for common dependencies
- Document mock behavior and assumptions
- Avoid network or database calls in unit tests
- Supplement with integration tests for actual dependency behavior

```typescript
// Example of mocking external dependencies in tests
import { describe, it, expect, vi } from 'vitest';
import UserService from './UserService';
import { UserRepository } from './UserRepository';
import { EmailService } from '../notifications/EmailService';

// Mock dependencies
vi.mock('./UserRepository', () => ({
  UserRepository: {
    findById: vi.fn(),
    update: vi.fn(),
  }
}));

vi.mock('../notifications/EmailService', () => ({
  EmailService: {
    sendWelcomeEmail: vi.fn(),
    sendPasswordResetEmail: vi.fn(),
  }
}));

describe('UserService', () => {
  beforeEach(() => {
    vi.resetAllMocks();
  });
  
  describe('registerUser', () => {
    it('should create a new user and send welcome email', async () => {
      // Arrange
      const userData = {
        email: 'test@example.com',
        password: 'securePassword123',
        name: 'Test User',
      };
      
      const createdUser = {
        id: '123',
        email: userData.email,
        name: userData.name,
        createdAt: new Date(),
      };
      
      // Mock repository to return the created user
      UserRepository.create = vi.fn().mockResolvedValue(createdUser);
      
      // Act
      const result = await UserService.registerUser(userData);
      
      // Assert
      expect(UserRepository.create).toHaveBeenCalledWith({
        email: userData.email,
        password: expect.any(String), // Hashed password
        name: userData.name,
      });
      
      expect(EmailService.sendWelcomeEmail).toHaveBeenCalledWith(
        userData.email,
        userData.name
      );
      
      expect(result).toEqual({
        id: createdUser.id,
        email: createdUser.email,
        name: createdUser.name,
      });
    });
    
    it('should handle email service failure gracefully', async () => {
      // Arrange
      const userData = {
        email: 'test@example.com',
        password: 'securePassword123',
        name: 'Test User',
      };
      
      const createdUser = {
        id: '123',
        email: userData.email,
        name: userData.name,
        createdAt: new Date(),
      };
      
      // Mock repository to return the created user
      UserRepository.create = vi.fn().mockResolvedValue(createdUser);
      
      // Mock email service to fail
      EmailService.sendWelcomeEmail = vi.fn().mockRejectedValue(
        new Error('Email service unavailable')
      );
      
      // Act
      const result = await UserService.registerUser(userData);
      
      // Assert
      expect(UserRepository.create).toHaveBeenCalled();
      expect(EmailService.sendWelcomeEmail).toHaveBeenCalled();
      
      // User should still be created even if email fails
      expect(result).toEqual({
        id: createdUser.id,
        email: createdUser.email,
        name: createdUser.name,
      });
    });
  });
});
```

## UNTESTED DevOps Golden Rules

Here are additional DevOps-focused golden rules that can be tested:

### 1. **AUTOMATE REPETITIVE TASKS WITH CI/CD PIPELINES**
- Implement continuous integration for automated testing
- Set up continuous delivery for streamlined deployments
- Automate build, test, and release processes
- Include security scanning in the CI/CD pipeline
- Maintain comprehensive pipeline documentation

### 2. **IMPLEMENT INFRASTRUCTURE AS CODE (IAC)**
- Define all infrastructure using code (Terraform, CloudFormation, etc.)
- Version control infrastructure code alongside application code
- Test infrastructure changes before applying to production
- Implement review processes for infrastructure changes
- Document infrastructure components and relationships

### 3. **ADOPT A ZERO-DOWNTIME DEPLOYMENT STRATEGY**
- Implement blue/green or canary deployment patterns
- Use feature flags for controlled feature rollouts
- Design systems for backward compatibility
- Test deployment processes in staging environments
- Have defined rollback procedures for failed deployments

### 4. **ESTABLISH COMPREHENSIVE MONITORING AND ALERTING**
- Monitor both technical and business metrics
- Implement proactive alerting for potential issues
- Create detailed dashboards for system visibility
- Set appropriate thresholds for different alert levels
- Document the meaning and required action for each alert

### 5. **USE SEMANTIC VERSIONING FOR RELEASES**
- Follow the MAJOR.MINOR.PATCH versioning pattern
- Document breaking changes in MAJOR version updates
- Maintain detailed changelogs for all releases
- Tag releases in version control
- Consider automated release note generation

### 6. **DESIGN FOR SCALABILITY AND RESILIENCE**
- Implement horizontal scaling where possible
- Design with redundancy for critical components
- Implement circuit breakers for dependent services
- Use queue-based architectures for load handling
- Document scaling limits and bottlenecks
