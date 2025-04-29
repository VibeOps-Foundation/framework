# Security Operations (SecOps) Golden Rules for AI-Assisted Development

## Applying Security Rules in AI IDE Contexts

Security is a critical concern in software development, and AI-assisted development introduces unique security challenges and opportunities. AI assistants might generate code with security vulnerabilities if not properly guided, making security-focused golden rules essential in these contexts for several reasons:

1. **Security by Design**: Security rules embed security considerations directly into the development process rather than as an afterthought.

2. **Consistent Security Practices**: Rules ensure all developers—regardless of security expertise—apply the same security standards when working with AI assistants.

3. **Preventing AI Blind Spots**: AI models might not recognize security implications in certain contexts or might reproduce vulnerable patterns from their training data.

4. **Compliance Adherence**: Security rules help maintain compliance with regulatory requirements and industry standards.

### Implementation Methods in AI Workflows

Security rules can be enforced in AI-assisted development through:

- **Security-Focused Meta-Prompts**: Include security requirements in prompts to AI assistants
- **Automated Security Scanning**: Integrate SAST/DAST tools into the development workflow
- **Security Checklists**: Implement pre-commit and pre-merge security verification
- **AI Guardian Patterns**: Train AI to recognize and avoid generating insecure code
- **Security Annotations**: Use comments and annotations to flag security-sensitive regions

## TESTED SecOps Golden Rules

### Documentation and Process

#### **CREATE A RICH DOCUMENTATION OVER SECURITY PROCESS AND KEEP IT UPDATED**
- Document all security-related processes and controls
- Include threat models for key components
- Maintain a security decision log
- Update documentation after security incidents or changes
- Ensure documentation covers both prevention and incident response

```markdown
# Authentication Security Documentation

## Threat Model
- Unauthorized access attempts
- Credential stuffing attacks
- Session hijacking
- Man-in-the-middle attacks

## Security Controls
1. **Password Requirements**
   - Minimum length: 12 characters
   - Complexity: Must include uppercase, lowercase, numbers, and symbols
   - Implementation: `PasswordValidator` class with `zxcvbn` strength estimation

2. **Multi-factor Authentication**
   - Required for: Admin accounts, financial operations
   - Optional for: Standard user accounts
   - Implementation: TOTP via `otplib`

3. **Session Management**
   - Session timeout: 30 minutes of inactivity
   - Session rotation: On privilege change
   - Implementation: `SessionManager` with secure cookie configuration

## Incident Response
1. Forced password reset for affected accounts
2. Session invalidation
3. IP blocking for repeated failures
4. Audit log review

## Last Updated: 2025-03-15
- Updated MFA requirements based on security audit findings
- Added session rotation on privilege change
```

### Web Application Security

#### **ALWAYS USE CSRF PROTECTION ON STATE-CHANGING REQUESTS**
- Implement CSRF tokens for all forms and state-changing requests
- Validate CSRF tokens on the server side before processing requests
- Use SameSite cookie attributes to provide additional protection
- Implement token rotation for extended user sessions

```javascript
// Express.js example with CSRF protection
const csrf = require('csurf');
const express = require('express');
const app = express();

// Configure CSRF protection middleware
const csrfProtection = csrf({ 
  cookie: { 
    httpOnly: true, 
    sameSite: 'strict',
    secure: process.env.NODE_ENV === 'production'
  } 
});

// Apply CSRF protection to state-changing routes
app.post('/user/update-profile', csrfProtection, (req, res) => {
  // CSRF token is automatically validated by middleware
  // If invalid, an error is thrown before reaching this code
  userService.updateProfile(req.user.id, req.body);
  res.redirect('/user/profile');
});

// Include CSRF token in forms
app.get('/user/profile/edit', csrfProtection, (req, res) => {
  res.render('edit-profile', { 
    csrfToken: req.csrfToken() 
  });
});
```

```html
<!-- Form with CSRF token -->
<form action="/user/update-profile" method="post">
  <input type="hidden" name="_csrf" value="{{csrfToken}}">
  <!-- Form fields -->
  <button type="submit">Update Profile</button>
</form>
```

#### **ALWAYS USE XSS PROTECTION ON USER INPUT**
- Sanitize all user input before rendering or processing
- Implement Content Security Policy (CSP) headers
- Use context-specific output encoding
- Apply framework-provided XSS protection mechanisms
- Validate input against whitelisted patterns

```javascript
// React example with XSS protection
import DOMPurify from 'dompurify';

function UserCommentComponent({ comment }) {
  // Sanitize user-generated HTML before rendering
  const sanitizedComment = DOMPurify.sanitize(comment.html, {
    FORBID_TAGS: ['script', 'style', 'iframe', 'form'],
    FORBID_ATTR: ['onerror', 'onload', 'onclick']
  });
  
  return (
    <div className="comment">
      <h3>{comment.author}</h3>
      {/* Use dangerouslySetInnerHTML only with sanitized content */}
      <div dangerouslySetInnerHTML={{ __html: sanitizedComment }} />
    </div>
  );
}
```

```typescript
// Express.js example with helmet for CSP headers
import express from 'express';
import helmet from 'helmet';

const app = express();

// Set Content-Security-Policy headers
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", "'https://trusted-cdn.example.com'"],
    styleSrc: ["'self'", "'https://trusted-cdn.example.com'"],
    imgSrc: ["'self'", "data:", "https://*.example.com"],
    connectSrc: ["'self'", "https://api.example.com"],
    fontSrc: ["'self'", "https://trusted-cdn.example.com"],
    objectSrc: ["'none'"],
    mediaSrc: ["'self'"],
    frameSrc: ["'none'"],
  }
}));
```

#### **ALWAYS USE SQL INJECTION PROTECTION ON USER INPUT**
- Use parameterized queries or prepared statements for all database operations
- Never concatenate user input directly into SQL queries
- Apply input validation and whitelisting for query parameters
- Use ORM frameworks that handle SQL escaping automatically
- Implement least privilege database users

```typescript
// TypeORM example with parameterized queries
import { getRepository } from 'typeorm';
import { User } from '../entities/User';

export async function findUserByEmail(email: string): Promise<User | undefined> {
  // Correct: Using parameterized queries
  const userRepository = getRepository(User);
  return userRepository.findOne({ where: { email } });
}

export async function findUsersByRole(role: string): Promise<User[]> {
  // Correct: Using query builder with parameters
  const userRepository = getRepository(User);
  return userRepository
    .createQueryBuilder('user')
    .where('user.role = :role', { role })
    .getMany();
}

// NEVER do this:
export async function searchUsers(searchTerm: string): Promise<User[]> {
  // WRONG: Direct string concatenation - SQL Injection vulnerability
  const userRepository = getRepository(User);
  // This is vulnerable to SQL injection:
  // return userRepository.query(`SELECT * FROM users WHERE name LIKE '%${searchTerm}%'`);
  
  // CORRECT: Use parameters
  return userRepository.query(
    `SELECT * FROM users WHERE name LIKE :search`,
    { search: `%${searchTerm}%` }
  );
}
```

#### **ALWAYS USE XSRF PROTECTION ON USER INPUT**
- Implement proper origin and referrer validation
- Use secure, SameSite cookies to prevent cross-origin requests
- Apply additional authorization checks for sensitive operations
- Implement custom headers for AJAX requests

```typescript
// Express.js middleware for XSRF protection
function xsrfProtection(req, res, next) {
  // Check if request is coming from our origin
  const origin = req.headers.origin;
  const allowedOrigins = [
    'https://app.example.com',
    'https://admin.example.com'
  ];
  
  // For non-GET requests, validate origin
  if (req.method !== 'GET' && req.method !== 'HEAD' && req.method !== 'OPTIONS') {
    if (!origin || !allowedOrigins.includes(origin)) {
      return res.status(403).json({ error: 'Invalid request origin' });
    }
    
    // Check for custom X-Requested-With header in AJAX requests
    if (req.xhr && req.headers['x-requested-with'] !== 'XMLHttpRequest') {
      return res.status(403).json({ error: 'Invalid AJAX request' });
    }
  }
  
  next();
}

// Cookie configuration
app.use(cookieParser());
app.use(session({
  secret: process.env.SESSION_SECRET,
  cookie: {
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    sameSite: 'strict'
  }
}));

app.use(xsrfProtection);
```

#### **ALWAYS USE XXE PROTECTION ON USER INPUT**
- Disable XML external entity processing in XML parsers
- Use secure parser configurations for XML, JSON, and YAML
- Validate and sanitize user-supplied XML content
- Consider alternatives to XML when possible

```javascript
// Node.js XML parser with XXE protection
const { DOMParser } = require('xmldom');
const fs = require('fs');

function parseXMLSafely(xmlString) {
  // Configure parser with XXE protection
  const parser = new DOMParser({
    errorHandler: {
      error: (msg) => { console.error('XML parse error:', msg); },
      fatalError: (msg) => { throw new Error('Fatal XML parse error: ' + msg); }
    },
    // Disable external entity processing
    resolveExternalEntities: () => null,
    // Reject DOCTYPE declarations
    locator: {},
    validateXML: true
  });

  try {
    // Parse XML with security controls
    return parser.parseFromString(xmlString, 'application/xml');
  } catch (error) {
    console.error('XML parsing failed due to security controls:', error);
    throw new Error('Invalid XML content');
  }
}

// Example usage
app.post('/api/process-xml', (req, res) => {
  try {
    const xmlDoc = parseXMLSafely(req.body.xml);
    // Process the XML safely
    res.json({ success: true });
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});
```

### Secure Development Practices

#### **NEVER COMMIT NON-PUBLIC KEYS INTO REPOS**
- Use environment variables for sensitive configuration
- Implement secrets management systems for keys and credentials
- Add sensitive file patterns to `.gitignore`
- Use pre-commit hooks to prevent accidental credential commits
- Perform regular secret scanning on repositories

```bash
# Example .gitignore for preventing credential commits
# Private keys
*.pem
*.key
id_rsa
*.p12
*.pfx

# Configuration with secrets
.env
.env.*
config/secrets.yml
config/credentials.yml
*credentials*.json

# AWS specific
.aws/credentials
.aws/config

# Database
*.sqlite
*.db

# Logs (might contain sensitive data)
logs/
*.log
```

```javascript
// Example using environment variables for configuration
const config = {
  database: {
    host: process.env.DB_HOST || 'localhost',
    port: process.env.DB_PORT || 5432,
    username: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME,
  },
  api: {
    key: process.env.API_KEY,
    endpoint: process.env.API_ENDPOINT || 'https://api.example.com',
  },
  jwt: {
    secret: process.env.JWT_SECRET,
    expiresIn: process.env.JWT_EXPIRES_IN || '1h',
  }
};

// Validate that required secrets are provided
['DB_USER', 'DB_PASSWORD', 'DB_NAME', 'API_KEY', 'JWT_SECRET'].forEach(key => {
  if (!process.env[key]) {
    throw new Error(`Missing required environment variable: ${key}`);
  }
});

module.exports = config;
```

#### **WHENEVER INSTALLING PACKAGES, VERIFY CRITICAL ISSUES**
- Run security audits on dependencies before installation
- Implement automated vulnerability scanning in CI/CD pipeline
- Set policies for acceptable vulnerability severity
- Keep dependencies updated regularly
- Monitor security advisories for used packages

```bash
# npm security check before installation
npm audit before npm install

# Check for vulnerabilities in existing packages
npm audit

# Fix vulnerabilities automatically where possible
npm audit fix

# Generate a detailed report
npm audit --json > security-audit.json

# Yarn equivalent
yarn audit
```

```javascript
// Example of a package.json script for security checks
{
  "name": "secure-app",
  "version": "1.0.0",
  "scripts": {
    "preinstall": "npm audit --audit-level=moderate",
    "security-check": "npm audit --audit-level=moderate && snyk test",
    "prepush": "npm run security-check"
  },
  "husky": {
    "hooks": {
      "pre-commit": "npm run security-check"
    }
  },
  "dependencies": {
    // ...
  }
}
```

#### **AVOID BAD SMELLING CODE**
- Implement security-focused code reviews that identify code smells
- Address security-related technical debt promptly
- Refactor complex security-critical code for clarity
- Use static analysis tools to detect security smells
- Document security-critical sections clearly

```typescript
// Example of refactoring a security code smell

// BEFORE: Security code smell - Authentication logic mixed with business logic
function processUserOrder(userId, orderId, items) {
  // Authentication mixed with business logic
  if (!sessions[userId] || sessions[userId].expired) {
    return { error: 'Unauthorized' };
  }
  
  // Authorization mixed in
  if (orders[orderId].userId !== userId) {
    return { error: 'Forbidden' };
  }
  
  // Business logic
  const total = calculateTotal(items);
  
  // Direct database query with potential injection
  db.query(`UPDATE orders SET total = ${total} WHERE id = ${orderId}`);
  
  return { success: true };
}

// AFTER: Refactored with separation of concerns
// Authentication middleware
function authenticationMiddleware(req, res, next) {
  if (!req.session || !req.session.userId) {
    return res.status(401).json({ error: 'Unauthorized' });
  }
  next();
}

// Authorization service
class OrderAuthorizationService {
  static async canAccessOrder(userId, orderId) {
    const order = await OrderRepository.findById(orderId);
    return order && order.userId === userId;
  }
}

// Order controller with business logic only
async function processUserOrder(req, res) {
  const { userId } = req.session;
  const { orderId } = req.params;
  const { items } = req.body;
  
  // Clean authorization check
  const canAccess = await OrderAuthorizationService.canAccessOrder(userId, orderId);
  if (!canAccess) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  
  // Business logic
  const total = calculateTotal(items);
  
  // Secure database operation
  await OrderRepository.updateTotal(orderId, total);
  
  return res.json({ success: true });
}

// Usage
app.put('/api/orders/:orderId', 
  authenticationMiddleware, 
  processUserOrder
);
```

## UNTESTED SecOps Golden Rules

Here are additional security-focused golden rules that can be tested:

### 1. **IMPLEMENT PROPER AUTHENTICATION FOR EVERY ENDPOINT**
- Use industry-standard authentication protocols (OAuth, OpenID Connect)
- Implement multi-factor authentication for sensitive operations
- Apply proper session management with secure cookies
- Create clear authentication boundaries between different parts of the application
- Log and monitor authentication attempts and failures

### 2. **PRACTICE DEFENSE IN DEPTH**
- Implement multiple layers of security controls
- Don't rely on a single security measure for critical functions
- Assume that any single security control might fail
- Apply complementary security approaches (e.g., validation + parameterization + least privilege)
- Regularly test security controls through penetration testing

### 3. **APPLY THE PRINCIPLE OF LEAST PRIVILEGE**
- Grant minimal permissions necessary for each component
- Use fine-grained access controls
- Regularly audit and trim permissions
- Run services and processes with minimal privileges
- Implement proper role-based access control (RBAC)

### 4. **ENCRYPT SENSITIVE DATA AT REST AND IN TRANSIT**
- Use HTTPS/TLS for all data transmission
- Implement proper certificate validation
- Encrypt sensitive data before storage
- Use modern, strong encryption algorithms
- Manage encryption keys securely

### 5. **IMPLEMENT SECURITY LOGGING AND MONITORING**
- Log security-relevant events with appropriate detail
- Ensure logs can't be easily tampered with
- Create alerts for suspicious activity patterns
- Preserve logs for an appropriate time period
- Include enough context in logs for investigation

### 6. **VALIDATE ALL INPUTS AND OUTPUTS**
- Implement server-side validation regardless of client-side checks
- Apply strong typing and schema validation
- Use allowlisting over denylisting for input validation
- Validate content types and encodings
- Validate redirects and forwards

### 7. **IMPLEMENT RATE LIMITING AND BRUTE FORCE PROTECTION**
- Apply rate limiting to authentication attempts
- Implement progressive delays for repeated failures
- Set sensible rate limits for API endpoints
- Create alerts for unusual request patterns
- Use CAPTCHA for suspected automated attacks

### 108. **PRACTICE SECURE ERROR HANDLING**
- Don't expose sensitive information in error messages
- Log detailed errors internally but return sanitized messages to users
- Handle exceptions securely
- Fail securely (deny by default)
- Ensure error handling doesn't disable security controls
