# 🛠️ Tools & Techniques for Workflow Automation

This document outlines various tools and techniques for implementing and enhancing the Precision-First and Rapid Prototyping workflows.

---

## Hard Constraint Mechanisms

### `.copilot-instructions.md`
Project-level persistent instructions that GitHub Copilot automatically reads.

**Location:** `.vscode/copilot-instructions.md`

**Example:**
```markdown
# Project Context for GitHub Copilot

## Tech Stack
- **Framework**: Next.js 14.2 (App Router)
- **Language**: TypeScript 5.3 (strict mode)
- **Database**: PostgreSQL 16 with Prisma 5.8

## Hard Constraints
1. Never use `any` type - use `unknown` with type guards instead
2. Always use async/await, never raw Promises
3. All API routes must use try-catch with typed error responses
4. All user inputs must be validated with Zod schemas
```

### Prompt Templates Directory
Reusable structured prompts for common tasks.

**Structure:**
```
.prompts/
├── iac-aws-fargate.md
├── api-rest-crud.md
├── component-react.md
└── database-migration.md
```

### Linters & Type Checkers
Automatically enforce code rules.

**Tools:**
- **ESLint** - JavaScript/TypeScript linting
- **TypeScript** - Type checking
- **Pylint** - Python linting
- **Prettier** - Code formatting

**Integration:** Configure pre-commit hooks to validate LLM output.

### Test Suites
Automated tests verify LLM output meets functional requirements.

**Frameworks:**
- **Jest** - JavaScript/TypeScript
- **Pytest** - Python
- **Vitest** - Vite projects

---

## Soft Constraint Mechanisms

### Conversational Refinement
Natural language feedback for preferences and style.

**Example:**
```
"I like this error handling pattern - use it for all API routes going forward"
```

### Few-Shot Examples
Provide 1-3 code examples showing desired pattern.

**Example:**
```markdown
## Example Pattern

Here's how we handle database transactions:

```typescript
async function updateUser(id: string, data: UserUpdate) {
  return await prisma.$transaction(async (tx) => {
    const user = await tx.user.update({ where: { id }, data });
    await tx.auditLog.create({ /* ... */ });
    return user;
  });
}
```

Use this pattern for all multi-step database operations.
```

### Session Memory
Reference earlier conversation decisions.

**Example:**
```
"Use the same error handling approach from step 3"
"Follow the validation pattern we established for the auth endpoint"
```

---

## Advanced Collaboration Tools

### Model Context Protocol (MCP)

Structured way to provide external context (databases, APIs, file systems) to LLMs.

**What it does:**
- LLM can query your database schema directly for accurate SQL generation
- LLM accesses your Git history to understand code evolution
- LLM reads project files automatically

**Example MCP Server:**
```json
{
  "mcpServers": {
    "precision-workflow": {
      "command": "npx",
      "args": ["-y", "@your-org/precision-workflow-mcp"]
    }
  }
}
```

**Implementation:**
- Expose read-only APIs for project context
- Provide schema information
- Enable safe file system access
- Share git history and blame information

**Resources:**
- [Model Context Protocol Docs](https://modelcontextprotocol.io/)
- [VS Code MCP Integration](https://code.visualstudio.com/api/extension-guides/mcp)

### Retrieval-Augmented Generation (RAG)

LLM dynamically retrieves relevant documentation.

**Use Cases:**
- Point LLM to your organization's internal docs for domain-specific knowledge
- LLM searches through previous similar implementations in codebase
- Access to up-to-date external documentation

**Implementation:**
- Index your documentation (using vector databases like Pinecone, Weaviate)
- Implement semantic search
- Inject relevant context into prompts automatically

**Example Flow:**
```
User Request
  ↓
Semantic Search (finds relevant docs)
  ↓
Augmented Prompt (request + relevant docs)
  ↓
LLM Response
```

### Agent Orchestration

Multiple specialized LLM agents handle different aspects.

**Architecture:**
```
┌─────────────────┐
│ Orchestrator    │ ← User's generic request
│ Agent           │
└────────┬────────┘
         │
    ┌────┴────┬─────────┬──────────┐
    ↓         ↓         ↓          ↓
┌────────┐ ┌──────┐ ┌────────┐ ┌──────────┐
│Context │ │Impl  │ │Testing │ │Review    │
│Agent   │ │Agent │ │Agent   │ │Agent     │
└────────┘ └──────┘ └────────┘ └──────────┘
```

**Agent Responsibilities:**
- **Orchestrator** - Routes requests, manages workflow
- **Context Agent** - Gathers project information, asks questions
- **Implementation Agent** - Generates code
- **Testing Agent** - Creates tests
- **Review Agent** - Validates against constraints

**Frameworks:**
- **LangChain** - Agent orchestration framework
- **AutoGPT** - Autonomous agent system
- **CrewAI** - Multi-agent collaboration

### IDE Integration

Tools that provide automatic context to LLMs.

**GitHub Copilot Chat:**
- Automatic access to current file
- Terminal output context
- Git diff awareness
- Workspace file structure

**Cursor IDE:**
- Deep codebase understanding
- Multi-file editing
- Codebase-wide search

**Continue.dev:**
- Open-source Copilot alternative
- Customizable context providers
- Works with multiple LLM providers

---

## Implementation Options

### Option 1: VS Code Extension (Chat Participant)

**Features:**
- Custom chat participant (`@precision`)
- Workspace-aware context gathering
- Template library for common tasks
- Validation against project constraints
- Interactive clarifying questions
- Automated prompt construction

**Installation:**
```bash
# Clone template or create from scratch
git clone https://github.com/microsoft/vscode-extension-samples
cd chat-sample

# Install dependencies
npm install

# Compile and run
npm run compile
code --extensionDevelopmentHost .

# Or package and install
vsce package
code --install-extension precision-workflow-0.0.1.vsix
```

**See:** [Chat Participant Guide](./chat-participant-guide.md) for complete implementation.

### Option 2: MCP Server

**Features:**
- Exposes project context to any LLM
- Reads codebase, git history, dependencies
- Provides structured templates
- Validates LLM output against rules

**Setup:**
```json
{
  "mcpServers": {
    "precision-workflow": {
      "command": "npx",
      "args": ["-y", "@your-org/precision-workflow-mcp"]
    }
  }
}
```

**Implementation:**
```typescript
// server.ts
import { Server } from '@modelcontextprotocol/sdk/server/index.js';

const server = new Server({
  name: 'precision-workflow',
  version: '1.0.0',
});

// Expose project context
server.setRequestHandler('context/get', async () => {
  return {
    techStack: await readPackageJson(),
    constraints: await readConstraints(),
    patterns: await analyzeCodePatterns(),
  };
});

// Start server
server.listen();
```

### Option 3: CLI + Copilot Chat Integration

**Features:**
- Standalone CLI for prompt generation
- Integrates with Copilot Chat via slash commands
- Project template management
- Context caching

**Installation:**
```bash
npm install -g precision-prompt-cli
```

**Usage:**
```bash
# Generate prompt
precision-prompt iac --provider aws --service fargate

# Interactive mode
precision-prompt interactive

# Apply to current chat
precision-prompt apply --chat-id abc123

# Save as template
precision-prompt save-template my-iac-template
```

**Example CLI Implementation:**
```typescript
#!/usr/bin/env node
import { Command } from 'commander';
import inquirer from 'inquirer';

const program = new Command();

program
  .command('iac')
  .option('--provider <provider>', 'Cloud provider')
  .option('--service <service>', 'Service type')
  .action(async (options) => {
    // Gather additional context
    const answers = await inquirer.prompt([
      { name: 'region', message: 'AWS Region?', default: 'us-east-1' },
      { name: 'vpc', message: 'Existing VPC ID?' },
    ]);
    
    // Build structured prompt
    const prompt = buildPrompt({ ...options, ...answers });
    
    // Copy to clipboard
    await clipboard.write(prompt);
    console.log('✅ Prompt copied to clipboard!');
  });

program.parse();
```

---

## Practical Examples

### Example 1: VS Code Workspace Settings

**File: `.vscode/copilot-instructions.md`**
```markdown
# Project Context for GitHub Copilot

## Tech Stack
- **Framework**: Next.js 14.2 (App Router)
- **Language**: TypeScript 5.3 (strict mode)
- **Database**: PostgreSQL 16 with Prisma 5.8
- **API**: tRPC 11.x
- **Styling**: Tailwind CSS 3.4

## Hard Constraints
1. **TypeScript**: Never use `any` type - use `unknown` with type guards
2. **Async**: Always use async/await, never raw Promises
3. **Error Handling**: All API routes must use try-catch with typed errors
4. **Security**: All user inputs must be validated with Zod schemas
5. **Database**: All queries must use Prisma transactions for multi-step ops

## Code Patterns to Follow
- Use Server Components by default, Client Components only when needed
- Prefix client components with "use client" directive
- Keep components under 200 lines - extract if larger
- Use `cn()` utility for className merging (from shadcn/ui)

## Forbidden Patterns
- ❌ Client-side environment variables (use server actions instead)
- ❌ Direct database queries in components (use tRPC procedures)
- ❌ Inline styles (use Tailwind classes)
- ❌ console.log in production code (use proper logging library)
```

### Example 2: Interactive CLI Tool

**Tool: `prompt-wizard`**
```bash
$ prompt-wizard iac

🎯 Workflow 1: Precision-First Development
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📋 Phase 1: Context Establishment
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

? What cloud provider? › AWS
? What service type? › Fargate
? IaC tool? › Terraform
? Terraform version? › 1.6
? Describe the service: › REST API for user management

📋 Phase 2: Constraints
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

? Security requirements:
  ✓ HTTPS only
  ✓ No public IPs
  ✓ Enable CloudWatch logs

? Networking:
  ✓ Use existing VPC
  VPC ID: › vpc-12345

✅ Generated structured prompt (copied to clipboard)
📄 Saved to: .prompts/iac-fargate-2024-11-26.md

Ready to paste into GitHub Copilot Chat!
```

### Example 3: MCP Integration

**Automatic Context Injection:**
```
User: "@precision Create API endpoint"
  ↓
MCP Server reads:
  - package.json → detects Express 4.18
  - .eslintrc → finds no-console rule
  - existing routes → identifies pattern
  ↓
Constructs prompt with full context
  ↓
LLM generates code matching project style
```

---

## Comparison Matrix

| Feature | Chat Participant | MCP Server | CLI Tool |
|---------|------------------|------------|----------|
| **Automatic Context** | ✅ Full | ✅ Full | ⚠️ Manual |
| **IDE Integration** | ✅ Native | ✅ Via Protocol | ❌ Copy-paste |
| **Multi-LLM Support** | ❌ VS Code only | ✅ Any LLM | ✅ Any LLM |
| **Setup Complexity** | Medium | High | Low |
| **Maintenance** | Extension updates | Server updates | Script updates |
| **Team Distribution** | Marketplace | npm package | npm global |
| **Best For** | VS Code users | Multi-tool setup | Quick adoption |

---

## Related Resources

- [Workflow 1: Precision-First](./workflow-1-precision-first.md)
- [Workflow 2: Rapid Prototyping](./workflow-2-rapid-prototyping.md)
- [Chat Participant Guide](./chat-participant-guide.md)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [VS Code Extension API](https://code.visualstudio.com/api)
