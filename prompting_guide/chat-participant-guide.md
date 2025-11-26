# 🤖 Chat Participant Implementation Guide

## What are Chat Participants?

Chat participants are specialized AI assistants in VS Code that users invoke with `@` mentions (like `@workspace`, `@vscode`, `@terminal`). They extend GitHub Copilot Chat with domain-specific expertise and have full control over:
- The conversation flow and prompt construction
- How they interpret user requests
- What context they gather from the workspace
- How they respond (markdown, code blocks, buttons, file trees, etc.)

## Why Build a Chat Participant?

A custom chat participant can **automate the entire Precision-First workflow** by:
1. **Intercepting generic requests** and identifying missing context
2. **Asking clarifying questions** in a structured way
3. **Gathering project context automatically** (versions, configs, existing patterns)
4. **Constructing optimal prompts** based on gathered information
5. **Validating responses** against project constraints before showing to user

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    User Types in Chat                       │
│              "@precision Create IaC for Fargate"            │
└────────────────────────┬────────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────────┐
│              Custom Chat Participant Handler                │
│  1. Parse request                                           │
│  2. Check for missing context                               │
│  3. Query workspace (MCP, file system, git)                │
│  4. Ask clarifying questions if needed                      │
│  5. Build structured prompt                                 │
│  6. Send to language model                                  │
│  7. Validate & stream response                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Implementation Steps

### 1. Package.json Registration

Define your chat participant in the extension manifest:

```json
{
  "name": "precision-workflow",
  "displayName": "Precision Workflow Assistant",
  "description": "Structured prompting assistant for production code",
  "version": "0.0.1",
  "engines": {
    "vscode": "^1.90.0"
  },
  "categories": ["AI", "Chat"],
  "activationEvents": [],
  "main": "./out/extension.js",
  "contributes": {
    "chatParticipants": [
      {
        "id": "precision-workflow.assistant",
        "name": "precision",
        "fullName": "Precision Workflow Assistant",
        "description": "Production-ready code with structured prompts",
        "isSticky": true,
        "commands": [
          {
            "name": "iac",
            "description": "Generate infrastructure as code with full context"
          },
          {
            "name": "api",
            "description": "Create API implementation with best practices"
          },
          {
            "name": "component",
            "description": "Generate UI component with proper structure"
          }
        ],
        "disambiguation": [
          {
            "category": "infrastructure",
            "description": "User wants to create infrastructure as code (IaC) for cloud resources",
            "examples": [
              "Create IaC for Fargate service",
              "Generate Terraform for Lambda",
              "Build CloudFormation for RDS"
            ]
          }
        ]
      }
    ]
  },
  "scripts": {
    "compile": "tsc -p ./",
    "watch": "tsc -watch -p ./",
    "package": "vsce package"
  },
  "devDependencies": {
    "@types/vscode": "^1.90.0",
    "@types/node": "20.x",
    "typescript": "^5.3.0",
    "@vscode/vsce": "^2.24.0"
  }
}
```

**Key Properties:**
- `id` - Unique identifier for your participant
- `name` - Short name used for @mentions (e.g., `@precision`)
- `fullName` - Display name shown in responses
- `description` - Shown as placeholder text in chat input
- `commands` - Slash commands (e.g., `/iac`, `/api`)
- `disambiguation` - Helps VS Code automatically route questions to your participant

### 2. Extension Activation

Create your participant in the extension's `activate` function:

```typescript
import * as vscode from 'vscode';

export function activate(context: vscode.ExtensionContext) {
  // Create the chat participant
  const precision = vscode.chat.createChatParticipant(
    'precision-workflow.assistant',
    requestHandler
  );

  // Set icon
  precision.iconPath = vscode.Uri.joinPath(
    context.extensionUri, 
    'assets/precision-icon.png'
  );

  // Optional: Follow-up provider
  precision.followupProvider = {
    provideFollowups(result, context, token) {
      return [
        {
          prompt: 'Add tests for this code',
          label: 'Generate tests'
        },
        {
          prompt: 'Explain the architecture',
          label: 'Explain design'
        }
      ];
    }
  };

  context.subscriptions.push(precision);
}
```

### 3. Request Handler (Core Logic)

Implement the main request handler that processes user prompts:

```typescript
interface ProjectContext {
  techStack: {
    language: string;
    framework: string;
    version: string;
  };
  constraints: string[];
  existingPatterns: string[];
  documentation: string[];
}

const requestHandler: vscode.ChatRequestHandler = async (
  request: vscode.ChatRequest,
  context: vscode.ChatContext,
  stream: vscode.ChatResponseStream,
  token: vscode.CancellationToken
) => {
  // Phase 1: Gather context
  stream.progress('🔍 Analyzing project context...');
  
  const projectContext = await gatherProjectContext();
  
  // Phase 2: Check for missing information
  const missingInfo = identifyMissingInfo(request, projectContext);
  
  if (missingInfo.length > 0) {
    // Ask clarifying questions
    stream.markdown('## 📋 I need some additional information:\n\n');
    missingInfo.forEach((info, i) => {
      stream.markdown(`${i + 1}. **${info.question}**\n`);
      stream.markdown(`   ${info.description}\n\n`);
    });
    
    return { metadata: { needsMoreInfo: true } };
  }
  
  // Phase 3: Build structured prompt
  const structuredPrompt = buildStructuredPrompt(
    request.prompt,
    request.command,
    projectContext
  );
  
  // Phase 4: Send to language model
  stream.progress('⚙️ Generating response with project context...');
  
  const messages = [
    vscode.LanguageModelChatMessage.User(structuredPrompt)
  ];
  
  const chatResponse = await request.model.sendRequest(messages, {}, token);
  
  // Phase 5: Stream response with validation
  let generatedCode = '';
  for await (const fragment of chatResponse.text) {
    stream.markdown(fragment);
    generatedCode += fragment;
  }
  
  // Phase 6: Add helpful buttons
  stream.button({
    command: 'precision.validateOutput',
    title: '✓ Validate Against Constraints',
    arguments: [generatedCode, projectContext]
  });
  
  stream.button({
    command: 'precision.addTests',
    title: '🧪 Generate Tests',
    arguments: [generatedCode]
  });
  
  return { 
    metadata: { 
      command: request.command,
      context: projectContext 
    } 
  };
};
```

### 4. Helper Functions

**Gather Project Context:**
```typescript
async function gatherProjectContext(): Promise<ProjectContext> {
  const workspaceFolder = vscode.workspace.workspaceFolders?.[0];
  if (!workspaceFolder) {
    return getDefaultContext();
  }
  
  // Read package.json or equivalent
  const packageJsonPath = vscode.Uri.joinPath(
    workspaceFolder.uri, 
    'package.json'
  );
  
  try {
    const packageJson = await vscode.workspace.fs.readFile(packageJsonPath);
    const parsed = JSON.parse(packageJson.toString());
    
    // Extract tech stack
    const techStack = {
      language: 'TypeScript',
      framework: detectFramework(parsed.dependencies),
      version: parsed.dependencies?.[detectFramework(parsed.dependencies)]
    };
    
    // Read .copilot-instructions.md for constraints
    const constraintsPath = vscode.Uri.joinPath(
      workspaceFolder.uri,
      '.vscode/copilot-instructions.md'
    );
    
    let constraints: string[] = [];
    try {
      const constraintsContent = await vscode.workspace.fs.readFile(constraintsPath);
      constraints = parseConstraints(constraintsContent.toString());
    } catch {
      // No constraints file
    }
    
    return { techStack, constraints, existingPatterns: [], documentation: [] };
  } catch {
    return getDefaultContext();
  }
}
```

**Build Structured Prompt:**
```typescript
function buildStructuredPrompt(
  userPrompt: string,
  command: string | undefined,
  context: ProjectContext
): string {
  return `
## Context
You are an expert in ${context.techStack.framework} ${context.techStack.version}.
Your goal is to provide technically correct, production-ready code.

## Project Specifications
- **Framework**: ${context.techStack.framework}
- **Version**: ${context.techStack.version}
- **Language**: ${context.techStack.language}

## Hard Constraints
${context.constraints.map(c => `- ${c}`).join('\n')}

## Task
${userPrompt}

## Output Format
1. **Brief explanation** (2-3 sentences)
2. **Code implementation** (properly formatted with comments)
3. **Key decisions** (bullet list of important choices made)

## Requirements
- Follow project constraints strictly
- Include inline comments for complex logic
- Use TypeScript strict mode
- Handle errors appropriately
`;
}
```

**Identify Missing Information:**
```typescript
function identifyMissingInfo(
  request: vscode.ChatRequest,
  context: ProjectContext
): Array<{ question: string; description: string }> {
  const missing: Array<{ question: string; description: string }> = [];
  
  // Check based on command
  if (request.command === 'iac') {
    if (!request.prompt.includes('AWS') && !request.prompt.includes('Azure')) {
      missing.push({
        question: 'Cloud Provider',
        description: 'Which cloud provider? (AWS, Azure, GCP)'
      });
    }
  }
  
  return missing;
}
```

---

## Installation & Testing

### Local Development

```bash
# 1. Create extension folder
mkdir precision-workflow-extension
cd precision-workflow-extension

# 2. Initialize
npm init -y
npm install --save-dev @types/vscode @vscode/test-cli typescript

# 3. Create files
mkdir src
touch src/extension.ts
touch tsconfig.json

# 4. Compile
npx tsc -p ./

# 5. Test in Extension Development Host
code --extensionDevelopmentHost=.
```

### Package & Install

```bash
# Package extension
npx vsce package

# Install locally
code --install-extension precision-workflow-0.0.1.vsix
```

### Usage

Once installed, users can invoke your participant:
```
@precision Create IaC for AWS Fargate service handling REST API
```

Or use slash commands:
```
@precision /iac Fargate service with load balancer
```

---

## Documentation Resources

### Official VS Code Documentation
- **Chat Participant API Guide**: https://code.visualstudio.com/api/extension-guides/chat
- **Chat API Reference**: https://code.visualstudio.com/api/references/vscode-api#chat
- **Extension Samples**: https://github.com/microsoft/vscode-extension-samples/tree/main/chat-sample
- **Language Model API**: https://code.visualstudio.com/api/extension-guides/language-model
- **Publishing Extensions**: https://code.visualstudio.com/api/working-with-extensions/publishing-extension

### Related APIs
- **Language Model Tools**: https://code.visualstudio.com/api/extension-guides/tools
- **MCP Integration**: https://code.visualstudio.com/api/extension-guides/mcp

### Best Practices
- **Microsoft AI Guidelines**: https://www.microsoft.com/en-us/ai/tools-practices
- **GitHub Copilot Extensibility**: https://docs.github.com/copilot/building-copilot-extensions

### NPM Packages
- **@vscode/chat-extension-utils** - Simplifies tool calling
- **@vscode/prompt-tsx** - Advanced prompt construction

---

## Advantages Over Manual Prompting

| Aspect | Manual Prompting | Chat Participant |
|--------|------------------|------------------|
| **Context Gathering** | User must manually provide | Automatic from workspace |
| **Consistency** | Varies per request | Enforced by extension |
| **Validation** | User must verify | Built-in constraint checking |
| **Reusability** | Copy-paste templates | One-time setup, always available |
| **Team Adoption** | Each dev learns separately | Installed once, used by all |
| **Version Control** | No tracking | Extension versioned |

---

## Related Resources

- [Workflow 1: Precision-First](./workflow-1-precision-first.md) - The workflow this automates
- [Workflow 2: Rapid Prototyping](./workflow-2-rapid-prototyping.md) - Alternative workflow
- [Tools & Techniques](./tools-and-techniques.md) - Other implementation options
