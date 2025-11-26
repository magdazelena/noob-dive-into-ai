# 🚀 Workflow 2: Rapid Prototyping with Guardrails

*Best for: Exploration, proof-of-concepts, learning new technologies, refactoring*

## Overview

The Rapid Prototyping workflow emphasizes speed and exploration while maintaining essential quality guardrails. It's designed for scenarios where quick iteration, learning, and discovering the right approach are more valuable than perfection. Constraints emerge organically rather than being defined upfront.

---

## Phase 1: Quick Context Setup

### 1. Provide minimal viable context
Engineer gives LLM just enough information to start.

**Substeps:**
- State primary goal in one sentence (e.g., "Build REST API for user authentication")
- Mention tech stack (e.g., "Node.js + Express + PostgreSQL")
- Set 2-3 critical constraints only (e.g., "Use JWT, hash passwords")

**Example:**
```markdown
## Quick Context
**Goal**: Build a REST API for user authentication
**Stack**: Node.js 20 + Express + PostgreSQL
**Must-haves**: JWT tokens, bcrypt password hashing, input validation
```

### 2. Enable agent-driven discovery
LLM proactively asks clarifying questions about ambiguities.

**Substeps:**
- LLM identifies missing information (versions, preferences, edge cases)
- Engineer answers questions or grants LLM autonomy to choose sensible defaults
- LLM documents assumptions made

**Example LLM Response:**
```markdown
I'll help you build this! A few quick questions:

1. **Database ORM**: Prefer raw SQL, Sequelize, or Prisma? (I'll use Prisma if no preference)
2. **Token expiry**: How long should JWTs be valid? (Default: 24 hours)
3. **Refresh tokens**: Need refresh token functionality? (I'll skip if not mentioned)

Reply with your preferences, or say "use defaults" and I'll proceed with sensible choices.
```

---

## Phase 2: Collaborative Exploration

### 3. Generate initial scaffold
LLM creates foundational structure with best practices.

**Substeps:**
- LLM proposes file structure and architecture
- LLM includes boilerplate with TODO comments for engineer input
- LLM explains rationale for architectural choices

**Example LLM Output:**
```markdown
## Proposed Structure

```
project/
├── src/
│   ├── routes/
│   │   └── auth.routes.js    // Authentication endpoints
│   ├── controllers/
│   │   └── auth.controller.js // Business logic
│   ├── middleware/
│   │   └── auth.middleware.js // JWT verification
│   ├── models/
│   │   └── user.model.js      // User schema
│   └── utils/
│       ├── jwt.js             // Token generation/validation
│       └── password.js        // Bcrypt helpers
├── config/
│   └── db.js                  // Database connection
└── server.js                  // Entry point
```

**Architecture Decisions:**
- **Route-Controller pattern**: Separates HTTP logic from business logic
- **Middleware-based auth**: Reusable across protected routes
- **Utility modules**: Keeps crypto operations isolated and testable

Should I generate the code for these files?
```

### 4. Parallel prototyping
Engineer and LLM work on different aspects simultaneously.

**Substeps:**
- Engineer focuses on business logic or UI
- LLM generates utilities, tests, configuration files
- Both use shared context from step 1

**Example Division of Work:**
```markdown
## Parallel Work Plan

**You (Engineer) work on:**
- User registration business rules (e.g., email uniqueness, password requirements)
- Specific API response formats your frontend needs

**I (LLM) will generate:**
- Database connection setup
- JWT utility functions
- Password hashing utilities
- Basic route structure
- Express server boilerplate

Let me know when you're ready, and I'll start with the boilerplate!
```

### 5. Real-time constraint discovery
Engineer adds soft constraints as patterns emerge.

**Substeps:**
- Engineer notices LLM pattern preference (e.g., "Keep using this error handling approach")
- Engineer codifies successful patterns as new constraints
- LLM adapts future suggestions to match emerging style

**Example:**
```markdown
**Engineer**: "I like how you're using this error format:
```json
{ "error": { "code": "VALIDATION_ERROR", "message": "...", "details": [...] } }
```
Use this structure for all error responses going forward."

**LLM**: "Got it! I'll use that error structure consistently. Should I update the previous auth routes to match this format?"
```

---

## Phase 3: Rapid Iteration

### 6. Request variations
Engineer asks LLM to generate multiple approaches to same problem.

**Substeps:**
- LLM provides 2-3 implementations with different trade-offs
- LLM explains pros/cons of each (performance vs readability, complexity vs flexibility)
- Engineer selects preferred approach or hybrid

**Example:**
```markdown
**Engineer**: "Show me different ways to handle token refresh"

**LLM**:
**Option 1: Sliding Window (Simple)**
- Pro: Easy to implement, one token type
- Con: Less secure if token is compromised

**Option 2: Refresh Token (Secure)**
- Pro: Short-lived access tokens, better security
- Con: More complex, requires token storage

**Option 3: Rotating Refresh Tokens (Most Secure)**
- Pro: Maximum security, tokens single-use
- Con: Most complex, requires careful state management

Which approach fits your security requirements best?
```

### 7. Incremental refinement
Engineer makes small requests to improve generated code.

**Substeps:**
- Request specific changes (e.g., "Extract this into a helper function")
- Ask for optimizations (e.g., "Reduce database queries")
- LLM implements changes and explains impact

**Example:**
```markdown
**Engineer**: "This auth middleware is doing 2 database queries per request. Can we optimize?"

**LLM**: "Good catch! We can cache the user lookup. Here's an optimized version using Redis:

```javascript
// Before: 2 DB queries per request
// After: 1 DB query + 1 Redis lookup (much faster)
```

This reduces latency from ~50ms to ~5ms per request. Trade-off: adds Redis dependency."
```

### 8. Integration testing
Engineer tests generated code and reports issues conversationally.

**Substeps:**
- Engineer describes unexpected behavior (e.g., "Returns undefined when X is null")
- LLM analyzes issue and proposes fix
- Engineer confirms fix or continues debugging loop

**Example:**
```markdown
**Engineer**: "When I test the login endpoint with an invalid password, I get a 500 error instead of 401."

**LLM**: "That's because the bcrypt comparison is throwing an error. Here's the fix:

```javascript
// Before: Throws error on invalid password
const isValid = await bcrypt.compare(password, user.passwordHash);

// After: Returns false on invalid password
const isValid = user && await bcrypt.compare(password, user.passwordHash).catch(() => false);
```

This handles the edge case gracefully. Can you test again?"
```

---

## Phase 4: Hardening & Transition

### 9. Elevate to production quality
Engineer requests LLM to add production concerns.

**Substeps:**
- Add error handling, logging, and monitoring
- Include input validation and security checks
- Generate unit and integration tests

**Example Request:**
```markdown
"This prototype works! Now let's harden it for production:
1. Add comprehensive error handling for all edge cases
2. Add request logging (timestamp, user, endpoint, duration)
3. Add rate limiting to prevent abuse
4. Generate unit tests for auth utilities
5. Add integration tests for auth endpoints"
```

### 10. Create documentation package
LLM generates all necessary documentation.

**Substeps:**
- Write README with setup instructions
- Generate API documentation from code
- Create architecture diagram or explanation of key flows

**Example Output:**
```markdown
# Authentication API

## Setup
```bash
npm install
cp .env.example .env  # Configure DATABASE_URL and JWT_SECRET
npm run migrate
npm start
```

## API Endpoints

### POST /api/auth/register
Register a new user.

**Request:**
```json
{ "email": "user@example.com", "password": "SecurePass123!" }
```

**Response (201):**
```json
{ "user": { "id": "...", "email": "..." }, "token": "..." }
```

## Architecture
[Mermaid diagram of auth flow]
```

### 11. Extract reusable patterns
Engineer and LLM identify patterns for future use.

**Substeps:**
- LLM suggests which code could be generalized into library functions
- Engineer decides what to extract to shared utilities
- Document patterns in team knowledge base

**Example:**
```markdown
**LLM**: "I noticed we're using this error handling pattern in multiple places:

```javascript
try {
  // operation
} catch (error) {
  logger.error(error);
  res.status(500).json({ error: formatError(error) });
}
```

Should we extract this into an `asyncHandler` wrapper that all routes can use? This would reduce boilerplate and ensure consistent error handling."
```

---

## Complete Example: Rapid Prototyping Prompt

### Generic Request
```
"Build a todo list API"
```

### Rapid Prototyping Prompt
```markdown
## Goal
Build a simple REST API for a todo list app

## Stack
Node.js + Express + SQLite (for simplicity)

## Must-Haves
- CRUD operations for todos (create, read, update, delete)
- Each todo has: id, title, description, completed (boolean)
- Basic validation (non-empty titles)

## Nice-to-Haves (if easy)
- Due dates
- Priority levels
- Search/filter

Start with the basics and we'll iterate from there. Use sensible defaults for anything not specified.
```

**LLM Response:**
```markdown
Perfect! I'll build a lightweight todo API with SQLite. Quick plan:

1. **File structure**: Simple 3-file setup (server, routes, db)
2. **Database**: SQLite with better-sqlite3 (no setup needed)
3. **Validation**: Basic checks, can add more later
4. **Defaults**: REST conventions, JSON responses, HTTP status codes

I'll start with CRUD operations, then we can add filtering if needed. Sound good?

[Generates initial code...]
```

---

## When to Use This Workflow

✅ **Use Workflow 2 when:**
- Exploring multiple solution approaches
- Learning new technology or framework
- Creating internal tools with flexible requirements
- Rapid iteration is more valuable than perfection
- Requirements are unclear or evolving
- Building proof-of-concept or MVP
- Experimenting with new architectural patterns

❌ **Don't use Workflow 2 when:**
- Working on security-critical systems
- Code needs to pass compliance audits
- Public API with backward compatibility needs
- Multiple teams depend on the code
- Cost of errors is very high

---

## Transitioning to Workflow 1

**Signs it's time to switch:**
- Prototype proves valuable and needs hardening
- Security requirements become clear
- Multiple developers need to maintain code
- Performance optimization becomes critical
- Need to integrate with production systems

**How to transition:**
1. **Extract constraints**: Document patterns that emerged during prototyping
2. **Add tests**: Ensure behavior is locked in before refactoring
3. **Apply Workflow 1**: Use precision-first for new features
4. **Refactor incrementally**: Don't rewrite everything at once

---

## Benefits

- **Speed**: Start coding in minutes, not hours
- **Learning**: Discover what works through experimentation
- **Flexibility**: Adapt quickly to changing requirements
- **Lower pressure**: Less paralysis from upfront planning
- **Innovation**: Freedom to try unconventional approaches
- **Engagement**: More conversational, less formal

---

## Anti-Patterns to Avoid

❌ **No guardrails**: Even rapid prototyping needs basic security (don't skip password hashing!)
❌ **No documentation**: Future-you won't remember why decisions were made
❌ **No testing**: At least test happy paths before declaring "done"
❌ **Ignoring tech debt**: Track shortcuts taken for later cleanup
❌ **Premature optimization**: Don't gold-plate code that might change

---

## Related Resources

- [Workflow 1: Precision-First](./workflow-1-precision-first.md) - For production-quality code
- [Chat Participant Implementation](./chat-participant-guide.md) - Automate this workflow
- [Tools & Techniques](./tools-and-techniques.md) - Supporting tools and integrations
