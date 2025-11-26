# 🎯 Workflow 1: Precision-First Development Loop

*Best for: Critical features, API development, production code, debugging complex issues*

## Overview

The Precision-First workflow prioritizes accuracy, completeness, and adherence to standards. It's designed for scenarios where code quality, security, and maintainability are paramount. This workflow frontloads planning and constraint definition to minimize errors and rework.

---

## Phase 1: Context Establishment

### 1. Define project scope
Engineer provides project type, tech stack, and version constraints upfront.

**Substeps:**
- Specify exact framework/library versions (e.g., "React 18.2, TypeScript 5.3")
- Identify relevant documentation sources (official docs, internal wiki, API specs)
- Declare coding standards (ESLint config, style guide, architectural patterns)

**Example:**
```markdown
## Project Context
- **Framework**: Next.js 14.2 (App Router)
- **Language**: TypeScript 5.3 (strict mode)
- **Database**: PostgreSQL 16 with Prisma 5.8
- **Documentation**: Next.js official docs, internal API standards v2.1
```

### 2. Set up hard constraints
Engineer establishes non-negotiable rules in a `.copilot-instructions.md` file.

**Substeps:**
- Define forbidden patterns (e.g., "Never use `any` type in TypeScript")
- Specify required patterns (e.g., "Always use async/await over Promises")
- List security requirements (e.g., "Sanitize all user inputs")

**Example `.vscode/copilot-instructions.md`:**
```markdown
## Hard Constraints
1. **TypeScript**: Never use `any` type - use `unknown` with type guards instead
2. **Async**: Always use async/await, never raw Promises
3. **Error Handling**: All API routes must use try-catch with typed error responses
4. **Security**: All user inputs must be validated with Zod schemas
5. **Database**: All queries must use Prisma transactions for multi-step operations
```

### 3. Provide reference materials
Engineer supplies authoritative documentation or code samples.

**Substeps:**
- Attach relevant doc snippets or paste into prompt
- Link to internal design documents or ADRs (Architecture Decision Records)
- Include existing code examples that should be mimicked

**Example:**
```markdown
## Reference Materials
- AWS ECS Fargate documentation (https://docs.aws.amazon.com/ecs/)
- Internal ADR-005: Infrastructure naming conventions
- Example: /infrastructure/existing-service.tf (similar service)
```

---

## Phase 2: Task Specification

### 4. Decompose the task
Engineer breaks down complex requests into discrete, verifiable steps.

**Substeps:**
- Create numbered list of deliverables (e.g., "1. Create interface, 2. Implement logic, 3. Add tests")
- Define acceptance criteria for each step
- Specify dependencies between steps

**Example:**
```markdown
## Task Breakdown
1. **Create TypeScript interface** for User entity
   - Acceptance: Matches database schema exactly
   - Dependency: None

2. **Implement CRUD operations** in user.service.ts
   - Acceptance: All operations use transactions, handle errors properly
   - Dependency: Requires step 1

3. **Add unit tests** with 90%+ coverage
   - Acceptance: Tests pass, cover happy path and error cases
   - Dependency: Requires step 2
```

### 5. Specify output format
Engineer declares exact structure expected from LLM.

**Substeps:**
- Request code in specific language blocks with comments
- Ask for step-by-step reasoning before code
- Require confidence levels (high/medium/low) for each suggestion

**Example:**
```markdown
## Output Format Required
1. **Brief explanation** (2-3 sentences about approach)
2. **Step-by-step reasoning** (numbered list of key decisions)
3. **Code implementation** (TypeScript block with inline comments)
4. **Confidence level** (high/medium/low with justification)
5. **Testing considerations** (what should be tested)
```

---

## Phase 3: Iterative Refinement

### 6. Request implementation
LLM generates code following provided constraints and structure.

**Substeps:**
- LLM cites sources used (docs, examples provided)
- LLM highlights assumptions made
- LLM provides implementation with inline comments explaining decisions

### 7. Verify against constraints
Engineer reviews output for adherence to hard constraints.

**Substeps:**
- Check linting/type errors automatically
- Validate against acceptance criteria from step 4
- Run tests if generated

**Verification Checklist:**
- [ ] No `any` types used
- [ ] All async operations use async/await
- [ ] Error handling present and typed
- [ ] Input validation with Zod
- [ ] Database operations use transactions

### 8. Iterative feedback loop
Engineer provides specific corrections referencing original constraints.

**Substeps:**
- Quote specific constraint violated (e.g., "Violates constraint #3: no `any` types")
- Provide example of expected correction
- LLM adjusts and re-explains reasoning

**Example Feedback:**
```markdown
This implementation violates Constraint #1 (no `any` types). 

On line 15: `const data: any = await fetch(...)`

Should be:
```typescript
interface ApiResponse {
  id: string;
  name: string;
}
const data: unknown = await fetch(...);
const validated = ApiResponseSchema.parse(data);
```

Please correct and explain why type safety is important here.
```

---

## Phase 4: Validation & Documentation

### 9. Cross-verify accuracy
Engineer validates implementation against authoritative sources.

**Substeps:**
- Compare with official documentation
- Test edge cases and error scenarios
- Verify performance characteristics if relevant

**Validation Steps:**
```markdown
## Validation Checklist
- [ ] Compare API calls with AWS SDK v3 documentation
- [ ] Test with invalid input (empty strings, null, undefined)
- [ ] Test with edge cases (very long strings, special characters)
- [ ] Verify error messages match expected format
- [ ] Check performance with realistic data sizes
```

### 10. Document decisions
LLM generates inline comments and summary documentation.

**Substeps:**
- Explain non-obvious implementation choices
- Document any deviation from standard patterns with justification
- Create JSDoc/docstring for public APIs

**Example Documentation:**
```typescript
/**
 * Creates a new user with validated input data.
 * 
 * @param data - Raw user data from request body
 * @returns Created user object with generated ID
 * @throws {ValidationError} If input data fails Zod validation
 * @throws {DatabaseError} If transaction fails or conflicts
 * 
 * Note: Uses Prisma transaction to ensure atomicity.
 * This is required per constraint #5 for all multi-step DB operations.
 */
async function createUser(data: unknown): Promise<User> {
  // Validate input (constraint #4: security)
  const validated = CreateUserSchema.parse(data);
  
  // Use transaction (constraint #5: database operations)
  return await prisma.$transaction(async (tx) => {
    // Implementation...
  });
}
```

---

## Complete Example: Precision-First Prompt

### Generic Request
```
"Create an API endpoint for user registration"
```

### Structured Precision-First Prompt
```markdown
## Context
You are a senior backend engineer specializing in Next.js 14 with TypeScript.
Your goal is to provide technically correct, production-ready code.

## Project Specifications
- **Framework**: Next.js 14.2 (App Router)
- **Language**: TypeScript 5.3 (strict mode)
- **Database**: PostgreSQL 16 with Prisma 5.8
- **Validation**: Zod 3.22
- **Documentation**: Next.js Route Handlers docs, Prisma Client API v5.8

## Hard Constraints
1. Never use `any` type - use `unknown` with type guards
2. All async operations use async/await
3. All API routes use try-catch with typed error responses
4. All user inputs validated with Zod schemas
5. All database operations use Prisma transactions

## Task
Create a POST endpoint at `/api/users` that:
1. Accepts JSON body with { email, name, password }
2. Validates input with Zod
3. Hashes password with bcrypt
4. Creates user in database (Prisma)
5. Returns created user (without password) or error

## Acceptance Criteria
- TypeScript compiles with no errors (strict mode)
- Input validation covers all required fields and formats
- Password is hashed before storage
- Transaction rollback on any error
- Returns 201 on success, 400 on validation error, 500 on server error

## Output Format
1. **Brief explanation** (2-3 sentences)
2. **Step-by-step reasoning** (key decisions made)
3. **Code implementation** (TypeScript with inline comments)
4. **Confidence level** (high/medium/low with reason)
5. **Testing recommendations** (what should be tested)

## Confidence Requirements
For each major decision, indicate:
- **High**: Based on official Next.js/Prisma docs
- **Medium**: Based on common patterns
- **Low**: Assumption that should be verified
```

---

## When to Use This Workflow

✅ **Use Workflow 1 when:**
- Working on financial, healthcare, or security-critical systems
- Modifying existing production code
- Creating public APIs with backward compatibility requirements
- Compliance or audit requirements demand traceability
- Code will be maintained long-term by a team
- Performance or security is critical

❌ **Don't use Workflow 1 when:**
- Rapidly prototyping to test an idea
- Learning a new technology or framework
- Creating throwaway code or experiments
- Time-to-market is more important than perfection
- Requirements are unclear or likely to change significantly

---

## Benefits

- **Accuracy**: Upfront constraints prevent common mistakes
- **Consistency**: All code follows same standards automatically
- **Maintainability**: Well-documented decisions help future developers
- **Security**: Explicit security constraints enforced from the start
- **Audit Trail**: Clear record of decisions and reasoning
- **Team Alignment**: Everyone uses same workflow and standards

---

## Related Resources

- [Workflow 2: Rapid Prototyping](./workflow-2-rapid-prototyping.md) - Alternative for faster iteration
- [Chat Participant Implementation](./chat-participant-guide.md) - Automate this workflow
- [Tools & Techniques](./tools-and-techniques.md) - Supporting tools and integrations
- [Ultimate Prompt Template](../prompting/ultimate_prompt.md) - Generic template structure
