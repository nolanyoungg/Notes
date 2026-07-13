You are a Principal Software Engineer, AI Agent Systems Architect, TypeScript Monorepo Architect, and Application Security Engineer.

Your assignment is to design and fully implement the initial production-grade foundation of the following blank GitHub repository:

Repository:
https://github.com/nolanyoungg/Nolan-Young-local-agent-laboratory

Default branch:
main

The repository is currently blank. You must build the complete initial implementation from the ground up.

# 1. Primary Objective

Create a TypeScript-based npm-workspaces monorepo named:

Nolan Young Local Agent Laboratory

This repository will be a local AI agent development platform. It must contain exactly three independently runnable applications:

1. `code-editor`
2. `build-assistant`
3. `release-engineer`

These must not be implemented as one large agent with mixed responsibilities.

The correct hierarchy is:

```text
Monorepo
├── Shared infrastructure packages
└── Independent applications
    └── Focused agents
        └── Explicitly permitted tools
```

Each application must have a narrow responsibility, its own workflow, prompts, configuration, reports, and CLI entry point while reusing common infrastructure from internal workspace packages.

The runtime AI provider must be local-first.

The initial supported local model runtime must be:

```text
Ollama
```

The expected model is:

```text
qwen2.5-coder:14b
```

No real OpenAI API key, OpenAI account, hosted OpenAI model, or paid OpenAI API service may be required.

Codex is being used only to construct this repository. Codex must not become part of the resulting application architecture.

# 2. Core Architectural Principle

The model is a reasoning component.

The TypeScript application is the authority.

Qwen may request an action, but the application must:

1. Parse the request.
2. Validate its schema.
3. Verify that the active agent may use the requested tool.
4. Verify that the requested path is inside the approved workspace.
5. Reject symlink escapes and traversal attempts.
6. Execute the operation through deterministic TypeScript code.
7. Record the action and result in a local trace.
8. Return the result to the model.

Never rely on a system prompt as the security boundary.

The runtime must enforce permissions in code.

The model must never receive unrestricted filesystem access or unrestricted shell access.

# 3. Required Repository Structure

Create the following high-level structure:

```text
Nolan-Young-local-agent-laboratory/
├── apps/
│   ├── code-editor/
│   ├── build-assistant/
│   └── release-engineer/
│
├── packages/
│   ├── agent-runtime/
│   ├── local-model-client/
│   ├── filesystem-tools/
│   ├── process-tools/
│   ├── workspace-security/
│   ├── tracing/
│   └── shared-types/
│
├── examples/
│   ├── broken-typescript-project/
│   ├── sample-node-project/
│   └── sample-release-project/
│
├── workspaces/
│   └── .gitkeep
│
├── reports/
│   └── runs/
│       └── .gitkeep
│
├── docs/
│   ├── architecture.md
│   ├── getting-started.md
│   ├── security-model.md
│   ├── adding-an-application.md
│   ├── adding-an-agent.md
│   ├── adding-a-tool.md
│   ├── configuration.md
│   └── workflows/
│       ├── code-editor.md
│       ├── build-assistant.md
│       └── release-engineer.md
│
├── scripts/
│   ├── check-ollama.ts
│   ├── verify-workspaces.ts
│   └── clean-generated-data.ts
│
├── .editorconfig
├── .env.example
├── .gitattributes
├── .gitignore
├── eslint.config.js
├── package.json
├── package-lock.json
├── tsconfig.base.json
├── LICENSE
└── README.md
```

You may add narrowly justified files where required for tests, schemas, configuration, fixtures, CI, documentation, or package exports.

Do not remove or rename any required top-level directory.

# 4. Technology Requirements

Use:

```text
Node.js
TypeScript
npm workspaces
ES modules
Zod
Ollama
Vitest
ESLint
Prettier
```

Use a currently supported Node.js LTS version and document the required version in:

```text
package.json
README.md
.nvmrc
```

Use strict TypeScript settings.

At minimum, enable:

```json
{
  "strict": true,
  "noUncheckedIndexedAccess": true,
  "exactOptionalPropertyTypes": true,
  "noImplicitOverride": true,
  "noFallthroughCasesInSwitch": true,
  "noImplicitReturns": true,
  "forceConsistentCasingInFileNames": true
}
```

Do not use `any` unless a documented integration boundary makes it unavoidable.

Prefer `unknown`, Zod validation, discriminated unions, exhaustive switches, and typed error classes.

# 5. Root Workspace Configuration

Configure npm workspaces for:

```json
[
  "apps/*",
  "packages/*"
]
```

The root package must be private.

Create root commands that can run all validation and each application independently.

Required root commands:

```bash
npm run build
npm run typecheck
npm run lint
npm run format
npm run format:check
npm run test
npm run validate
npm run clean

npm run code-editor -- --help
npm run build-assistant -- --help
npm run release-engineer -- --help

npm run check:ollama
```

`npm run validate` must run the complete deterministic repository quality gate:

```text
format check
lint
typecheck
tests
build
```

All commands must work from the repository root.

Generate and commit `package-lock.json`.

# 6. Shared Package: `shared-types`

Create:

```text
packages/shared-types/
├── src/
│   ├── agents.ts
│   ├── models.ts
│   ├── tools.ts
│   ├── tracing.ts
│   ├── workflows.ts
│   ├── errors.ts
│   └── index.ts
├── package.json
├── tsconfig.json
└── README.md
```

Define typed contracts for:

* Agent identity
* Agent definition
* Agent instructions
* Agent permissions
* Agent execution status
* Agent messages
* Model requests
* Model responses
* Tool definitions
* Tool calls
* Tool results
* Trace events
* Workflow identifiers
* Workflow results
* Validation findings
* Severity levels
* Structured errors
* Run IDs
* Workspace metadata

Use Zod schemas at runtime and inferred TypeScript types where appropriate.

Tool requests must use a discriminated union.

Example conceptual shape:

```typescript
type AgentAction =
  | ListFilesAction
  | ReadFileAction
  | SearchTextAction
  | WriteFileAction
  | ApplyPatchAction
  | ReadProcessLogAction
  | GetProcessStatusAction
  | FinishAction;
```

Do not accept arbitrary JSON tool objects without schema validation.

# 7. Shared Package: `local-model-client`

Create:

```text
packages/local-model-client/
├── src/
│   ├── LocalModelClient.ts
│   ├── OllamaModelClient.ts
│   ├── MockModelClient.ts
│   ├── ModelClientFactory.ts
│   ├── OllamaHealthCheck.ts
│   ├── errors.ts
│   └── index.ts
├── test/
├── package.json
├── tsconfig.json
└── README.md
```

Requirements:

* Define a provider-neutral `LocalModelClient` interface.
* Implement an Ollama client.
* Implement a deterministic mock client for tests.
* Do not require an API key for Ollama.
* Default Ollama URL:

```text
http://127.0.0.1:11434
```

* Default model:

```text
qwen2.5-coder:14b
```

* Support configurable:

  * base URL
  * model name
  * request timeout
  * temperature
  * context length
  * retry count
  * retry delay
* Use low temperature by default.
* Add health checking.
* Add actionable errors for:

  * Ollama unavailable
  * model not installed
  * timeout
  * malformed model output
  * empty model response
  * connection failure
* Do not silently fall back to a hosted provider.
* Never transmit repository content to an external service.

The mock provider must make tests reliable without requiring Ollama.

# 8. Shared Package: `workspace-security`

Create:

```text
packages/workspace-security/
├── src/
│   ├── WorkspaceGuard.ts
│   ├── PathPolicy.ts
│   ├── ReadPolicy.ts
│   ├── WritePolicy.ts
│   ├── SymlinkGuard.ts
│   ├── IgnoreMatcher.ts
│   ├── WorkspaceLock.ts
│   ├── errors.ts
│   └── index.ts
├── test/
├── package.json
├── tsconfig.json
└── README.md
```

This package is a critical security boundary.

Requirements:

* Resolve one canonical absolute workspace root per run.
* Resolve every requested path relative to that root.
* Reject:

  * `..` path traversal
  * absolute paths outside the workspace
  * symlink escapes
  * null-byte paths
  * malformed paths
  * paths exceeding configured limits
* Support glob-based read and write policies.
* Support explicit forbidden paths.
* Support ignore patterns.
* Protect by default:

  * `.git/**`
  * `.env`
  * `.env.*`
  * `node_modules/**`
  * private keys
  * certificate files
  * credential files
  * SSH material
  * package-manager caches
  * generated reports belonging to the agent platform
* Allow applications to define narrower policies.
* Ensure write permission does not imply delete permission.
* Do not implement recursive deletion tools.
* Add a workspace lock to prevent two mutating workflows from modifying the same target simultaneously.
* Include unit tests for traversal and symlink escape attempts.

The workspace may be:

1. A committed sample beneath `examples/`
2. A temporary project beneath `workspaces/`
3. An explicitly supplied external absolute path

The agent platform repository and target workspace must remain conceptually separate.

# 9. Shared Package: `filesystem-tools`

Create:

```text
packages/filesystem-tools/
├── src/
│   ├── ListFilesTool.ts
│   ├── ReadFileTool.ts
│   ├── SearchTextTool.ts
│   ├── WriteFileTool.ts
│   ├── CreateFileTool.ts
│   ├── ApplyPatchTool.ts
│   ├── ReadFileMetadataTool.ts
│   ├── ToolFactory.ts
│   ├── errors.ts
│   └── index.ts
├── test/
├── package.json
├── tsconfig.json
└── README.md
```

Requirements:

* Every tool must use `WorkspaceGuard`.
* Every input must be validated with Zod.
* Limit file sizes and total output sizes.
* Skip binary files unless explicitly supported.
* Return truncated-output metadata when applicable.
* Use deterministic sorting.
* Normalize line endings carefully.
* Preserve existing file encoding where feasible.
* Write files atomically through a temporary file and rename.
* `apply_patch` must:

  * require exact target paths
  * reject changes outside the workspace
  * report failed hunks clearly
  * avoid partially applied corrupt patches
* Add a dry-run capability for write operations.
* Record pre-change hashes and post-change hashes.
* Do not expose unrestricted directory deletion.
* Do not allow direct edits to forbidden files.

Read-only applications must not register write tools at all.

# 10. Shared Package: `process-tools`

Create:

```text
packages/process-tools/
├── src/
│   ├── ProcessManager.ts
│   ├── CommandAllowlist.ts
│   ├── OneShotCommandRunner.ts
│   ├── WatcherManager.ts
│   ├── ProcessLogStore.ts
│   ├── ProcessStatusStore.ts
│   ├── ProcessTermination.ts
│   ├── errors.ts
│   └── index.ts
├── test/
├── package.json
├── tsconfig.json
└── README.md
```

The model must never construct and execute arbitrary shell strings.

Requirements:

* Commands must be defined by application-owned configuration.
* Commands must be represented as:

  * executable
  * argument array
  * working directory
  * timeout
  * environment allowlist
* Do not use `shell: true` by default.
* Reject shell metacharacter-based command injection.
* Capture stdout and stderr separately.
* Limit log size while preserving useful tails.
* Record:

  * process ID
  * command identifier
  * start time
  * stop time
  * exit code
  * signal
  * timeout status
* Support one-shot processes.
* Support long-running watchers.
* Detect early watcher failure.
* Stop child processes in `finally` blocks.
* Handle Ctrl+C and termination signals.
* Prevent orphaned watcher processes.
* Add tests using harmless fixture commands.

Only the TypeScript application may select registered command identifiers.

The model may request a permitted command identifier where appropriate, but it must never supply a raw command.

# 11. Shared Package: `tracing`

Create:

```text
packages/tracing/
├── src/
│   ├── TraceRecorder.ts
│   ├── JsonlTraceWriter.ts
│   ├── RunDirectoryManager.ts
│   ├── ReportWriter.ts
│   ├── Redaction.ts
│   ├── errors.ts
│   └── index.ts
├── test/
├── package.json
├── tsconfig.json
└── README.md
```

Every workflow run must create:

```text
reports/runs/<timestamp>-<application>-<run-id>/
├── trace.jsonl
├── run-metadata.json
├── final-report.md
└── final-result.json
```

Applications may add additional reports.

Trace events must include:

* workflow started
* workflow completed
* workflow failed
* agent started
* agent completed
* model request started
* model response received
* tool requested
* tool rejected
* tool started
* tool completed
* tool failed
* process started
* process completed
* validation started
* validation completed
* repair pass started
* repair pass completed

Redact potential secrets from traces.

Do not record complete environment variables.

Do not log private keys, tokens, passwords, authorization headers, or secret file contents.

Trace recording must be local.

# 12. Shared Package: `agent-runtime`

Create:

```text
packages/agent-runtime/
├── src/
│   ├── AgentRunner.ts
│   ├── AgentLoop.ts
│   ├── ToolRegistry.ts
│   ├── ToolPermissionGuard.ts
│   ├── StructuredResponseParser.ts
│   ├── ConversationState.ts
│   ├── StepLimiter.ts
│   ├── RetryPolicy.ts
│   ├── ContextBudget.ts
│   ├── errors.ts
│   └── index.ts
├── test/
├── package.json
├── tsconfig.json
└── README.md
```

Responsibilities:

* Run one focused agent configuration at a time.
* Construct prompts.
* Send messages to the selected local model.
* Parse structured responses.
* Reject malformed responses.
* Validate tool calls.
* Check agent tool permissions.
* Execute approved tools.
* append tool results to conversation state.
* Stop when the agent returns a valid final response.
* Stop after a configured maximum step count.
* Prevent infinite loops.
* Detect repeated identical tool calls.
* Apply bounded retries to malformed model output.
* Track context size.
* Truncate or summarize tool output deterministically when needed.
* Never silently broaden permissions.

The runtime must support multiple agents using the same local model.

An agent is a configuration boundary consisting of:

* name
* description
* system instructions
* permitted tools
* maximum steps
* output schema
* model configuration

An agent is not assumed to be a separate model process.

# 13. Application One: `code-editor`

Create:

```text
apps/code-editor/
├── src/
│   ├── index.ts
│   ├── cli.ts
│   ├── config.ts
│   ├── CodeEditorWorkflow.ts
│   ├── agents/
│   │   ├── PlannerAgent.ts
│   │   ├── EditorAgent.ts
│   │   └── ReviewerAgent.ts
│   ├── reports/
│   │   └── CodeEditReportWriter.ts
│   └── index.ts
├── prompts/
│   ├── planner.system.md
│   ├── editor.system.md
│   └── reviewer.system.md
├── config/
│   ├── permissions.json
│   └── edit-policy.json
├── test/
├── package.json
├── tsconfig.json
└── README.md
```

## Purpose

Safely plan, apply, and review bounded source-code changes in one explicitly supplied workspace.

## Agents

### Planner Agent

Read-only.

Responsibilities:

* Understand the user task.
* Inspect relevant project structure.
* Identify affected files.
* Identify risks.
* Create a structured implementation plan.
* Avoid editing files.

Allowed tools:

```text
list_files
read_file
read_file_metadata
search_text
```

### Editor Agent

Read/write, but only under explicit policy.

Responsibilities:

* Execute the approved plan.
* Apply focused changes.
* Avoid unrelated refactors.
* Preserve project conventions.
* Use patches instead of rewriting entire files where practical.
* Return a structured change summary.

Allowed tools:

```text
list_files
read_file
read_file_metadata
search_text
create_file
write_file
apply_patch
```

### Reviewer Agent

Read-only.

Responsibilities:

* Review actual post-edit files.
* Compare changes against the plan.
* Identify omissions, regressions, or unnecessary modifications.
* Produce a structured review.
* Never modify files.

## Required workflow

```text
Validate workspace
Acquire workspace lock
Create run directory
Run planner
Write change-plan.json
Present or persist proposed scope
Run editor unless dry-run
Record changed files and hashes
Run reviewer
Write final review
Release workspace lock
```

Support modes:

```text
plan-only
dry-run
apply
```

Required CLI examples:

```bash
npm run code-editor -- \
  --workspace ./examples/broken-typescript-project \
  --task "Add robust numeric input validation to the calculator" \
  --mode plan-only
```

```bash
npm run code-editor -- \
  --workspace ./examples/broken-typescript-project \
  --task "Add robust numeric input validation to the calculator" \
  --mode dry-run
```

```bash
npm run code-editor -- \
  --workspace ./examples/broken-typescript-project \
  --task "Add robust numeric input validation to the calculator" \
  --mode apply
```

The dry-run mode must not modify target files.

The application must create:

```text
change-plan.json
proposed-changes.diff
changed-files.json
review-report.json
final-report.md
```

Where a file is modified, record its original and final SHA-256 hashes.

Do not automatically commit, push, or open a pull request.

# 14. Application Two: `build-assistant`

Create:

```text
apps/build-assistant/
├── src/
│   ├── index.ts
│   ├── cli.ts
│   ├── config.ts
│   ├── BuildAssistantWorkflow.ts
│   ├── RepairLoop.ts
│   ├── agents/
│   │   ├── BuildDiagnosisAgent.ts
│   │   ├── BuildRepairAgent.ts
│   │   └── BuildReviewerAgent.ts
│   ├── reports/
│   │   └── BuildReportWriter.ts
│   └── index.ts
├── prompts/
│   ├── diagnosis.system.md
│   ├── repair.system.md
│   └── reviewer.system.md
├── config/
│   ├── permissions.json
│   └── commands.example.json
├── test/
├── package.json
├── tsconfig.json
└── README.md
```

## Purpose

Run an approved project build or development watcher, inspect deterministic build output, allow Qwen to repair relevant source files, and stop after success or a bounded number of attempts.

## Important process rule

The application owns the process.

The model does not receive arbitrary shell access.

## Agents

### Build Diagnosis Agent

Read-only.

Responsibilities:

* Read the current build status.
* Read bounded build-log output.
* Inspect relevant source files.
* Identify likely root causes.
* Produce a repair plan.

### Build Repair Agent

Read/write.

Responsibilities:

* Repair only files relevant to current build failures.
* Avoid unrelated architectural changes.
* Re-read current build output after changes.
* Stop when the build succeeds or no safe repair is possible.

### Build Reviewer Agent

Read-only.

Responsibilities:

* Review the final changed files.
* Verify that changes correspond to reported failures.
* Flag broad, unrelated, or risky modifications.

## Required workflow

```text
Validate workspace
Load application-owned command configuration
Acquire workspace lock
Start approved one-shot build or watcher
Wait for initial status
Capture logs
Run diagnosis agent if failed
Run repair agent
Wait for rebuild or rerun approved build
Repeat up to configured maximum repair passes
Run final reviewer
Stop watcher in finally block
Release workspace lock
Write reports
```

Support:

```text
one-shot build mode
watcher mode
```

Required CLI examples:

```bash
npm run build-assistant -- \
  --workspace ./examples/broken-typescript-project \
  --command build \
  --task "Resolve all TypeScript compilation failures"
```

```bash
npm run build-assistant -- \
  --workspace ./examples/broken-typescript-project \
  --command dev \
  --watch \
  --task "Repair compiler failures while the development watcher runs"
```

Command configuration must resemble:

```json
{
  "commands": {
    "build": {
      "executable": "npm",
      "arguments": ["run", "build"],
      "timeoutMilliseconds": 120000,
      "mode": "one-shot"
    },
    "dev": {
      "executable": "npm",
      "arguments": ["run", "dev"],
      "startupTimeoutMilliseconds": 30000,
      "mode": "watcher"
    },
    "test": {
      "executable": "npm",
      "arguments": ["test"],
      "timeoutMilliseconds": 120000,
      "mode": "one-shot"
    }
  }
}
```

Do not permit command values to be supplied directly by model output.

Required reports:

```text
initial-build-status.json
diagnosis.json
repair-attempts.json
changed-files.json
final-build-status.json
review-report.json
final-report.md
```

Default maximum repair passes:

```text
3
```

Default maximum agent steps per role:

```text
20
```

On failure after all passes:

* Return a nonzero process exit code.
* Preserve the workspace.
* Preserve logs.
* Clearly identify unresolved failures.
* Do not claim success.

# 15. Application Three: `release-engineer`

Create:

```text
apps/release-engineer/
├── src/
│   ├── index.ts
│   ├── cli.ts
│   ├── config.ts
│   ├── ReleaseEngineerWorkflow.ts
│   ├── ValidationWorkflow.ts
│   ├── RepairLoop.ts
│   ├── agents/
│   │   ├── ReleaseReviewerAgent.ts
│   │   └── ReleaseRepairAgent.ts
│   ├── checks/
│   │   ├── CheckDefinition.ts
│   │   ├── CommandCheck.ts
│   │   ├── FilePresenceCheck.ts
│   │   ├── ForbiddenFileCheck.ts
│   │   └── PackageInspectionCheck.ts
│   ├── packaging/
│   │   ├── PackageBuilder.ts
│   │   ├── PackageValidator.ts
│   │   └── ChecksumGenerator.ts
│   ├── reports/
│   │   └── ReleaseReportWriter.ts
│   └── index.ts
├── prompts/
│   ├── reviewer.system.md
│   └── repair.system.md
├── config/
│   ├── permissions.json
│   ├── checks.example.json
│   └── package-rules.example.json
├── test/
├── package.json
├── tsconfig.json
└── README.md
```

## Purpose

Prepare a local project release candidate through deterministic validation, optional bounded repair, packaging, archive inspection, checksum generation, and release reporting.

The application must not publish packages, create Git tags, push commits, or create GitHub releases.

## Deterministic validation

The application must support configured checks such as:

```text
format check
lint
typecheck
tests
build
required files
forbidden files
package-content validation
```

Checks are executed by TypeScript-owned command definitions.

AI may interpret failures and repair source files, but AI must not determine whether a command passed.

Process exit codes and deterministic validators determine pass or fail.

## Agents

### Release Reviewer Agent

Read-only.

Responsibilities:

* Inspect release configuration.
* Inspect validation reports.
* Identify risks, omissions, and likely repair strategy.
* Never modify files.

### Release Repair Agent

Read/write.

Responsibilities:

* Receive structured failed-check reports.
* Apply focused source-code repairs.
* Avoid changing release policy merely to make failures disappear.
* Never remove or weaken mandatory checks without explicit user instruction.

## Required workflow

```text
Validate workspace
Acquire workspace lock
Load validation configuration
Run deterministic checks
If failures are repairable and repair enabled:
    Run release reviewer
    Run release repair agent
    Rerun all checks
    Repeat up to maximum repair passes
Fail if mandatory checks remain unsuccessful
Build release package
Inspect package contents
Extract package into a temporary directory
Validate extracted package
Generate SHA-256 checksum
Write release notes and reports
Release workspace lock
```

Required CLI commands:

```bash
npm run release-engineer -- \
  check \
  --workspace ./examples/sample-release-project
```

```bash
npm run release-engineer -- \
  prepare \
  --workspace ./examples/sample-release-project \
  --repair
```

```bash
npm run release-engineer -- \
  package \
  --workspace ./examples/sample-release-project
```

```bash
npm run release-engineer -- \
  release \
  --workspace ./examples/sample-release-project \
  --repair
```

Required outputs:

```text
validation-report.json
repair-attempts.json
package-manifest.json
package-validation.json
release-notes.md
final-report.md
artifacts/<project-name>-<version>.zip
artifacts/<project-name>-<version>.zip.sha256
```

Package rules must support:

* required files
* forbidden paths
* included paths
* excluded paths
* expected top-level directory behavior
* maximum archive size
* archive extraction verification
* path traversal protection during extraction

Forbidden package content should include by default:

```text
.git/**
.github/**
node_modules/**
.env
.env.*
*.key
*.pem
reports/**
workspaces/**
temporary files
platform runtime logs
```

Do not package the local agent laboratory itself when a separate external workspace is the target.

# 16. Application Configuration

Create a root `.env.example` containing non-secret local defaults:

```dotenv
MODEL_PROVIDER=ollama
OLLAMA_BASE_URL=http://127.0.0.1:11434
OLLAMA_MODEL=qwen2.5-coder:14b
MODEL_TEMPERATURE=0.1
MODEL_CONTEXT_TOKENS=32768
MODEL_REQUEST_TIMEOUT_MS=180000
MODEL_MAX_RETRIES=2

DEFAULT_MAX_AGENT_STEPS=20
DEFAULT_MAX_REPAIR_PASSES=3

REPORTS_DIRECTORY=reports/runs
```

Do not require `.env` when defaults are sufficient.

Do not commit an actual `.env`.

Validate all configuration through Zod.

Configuration precedence must be documented and consistently implemented:

```text
CLI arguments
environment variables
application config file
safe defaults
```

# 17. CLI Requirements

Use a robust typed CLI library or implement equivalent validation.

Every application must support:

```bash
--help
--version
--workspace
--task
--model
--ollama-url
--max-steps
--report-directory
--verbose
```

Mutating workflows must also support appropriate controls such as:

```bash
--dry-run
--max-repair-passes
```

Requirements:

* Resolve relative paths from the user’s current working directory.
* Print the resolved workspace before beginning.
* Print the selected model provider and model.
* Print the run-report directory.
* Print a clear final success or failure summary.
* Return nonzero exit codes on invalid arguments or failed workflows.
* Never print secrets.

# 18. Prompt Design

Create detailed prompts under each application’s `prompts/` directory.

Prompts must clearly define:

* Agent role
* Scope
* Allowed operations
* Forbidden operations
* Required evidence
* Structured response shape
* Completion criteria
* Requirement not to invent file contents
* Requirement to use tools
* Requirement not to claim success without evidence
* Requirement to avoid unrelated rewrites
* Requirement to preserve existing architecture unless change is justified

The prompt is not the enforcement mechanism.

Tool registration and runtime validation must independently enforce the same restrictions.

# 19. Example Projects

Create three intentionally small, committed sample projects.

## `examples/broken-typescript-project`

Purpose:

* Test Code Editor
* Test Build Assistant

Include:

* TypeScript source
* Package scripts
* A few deliberate but documented compile or validation errors
* Tests
* README explaining expected exercises

Do not leave the root monorepo’s own validation broken because of fixture errors.

The fixture must be isolated from root workspace builds where necessary.

## `examples/sample-node-project`

Purpose:

* Test safe code editing
* Test planning and review

Include:

* Small functional Node.js application
* Unit tests
* Input-validation improvement opportunity
* Clean build before modification

## `examples/sample-release-project`

Purpose:

* Test Release Engineer

Include:

* Build command
* Test command
* Packageable output
* Example package rules
* At least one optional exercise that can intentionally trigger validation failure

Document exact commands for trying each application against the examples.

# 20. Testing Requirements

Add meaningful automated tests.

At minimum test:

## Workspace security

* Normal relative path resolution
* Traversal rejection
* Absolute outside-path rejection
* Symlink escape rejection
* Forbidden path rejection
* Read-only policy enforcement
* Write policy enforcement
* Workspace lock behavior

## Filesystem tools

* List files
* Read text file
* Binary rejection
* Search text
* Atomic write
* Patch application
* Failed patch rollback
* Dry-run behavior
* Output truncation

## Agent runtime

* Successful tool loop
* Malformed model output
* Unknown tool
* Disallowed tool
* Maximum step termination
* Repeated-call detection
* Model timeout
* Final structured result

Use `MockModelClient` so tests do not depend on Ollama.

## Process tools

* Approved command execution
* Rejected command identifier
* Timeout behavior
* Log capture
* Watcher early exit
* Process termination

## Applications

Add smoke tests for:

* Code Editor plan-only mode
* Code Editor dry-run mode
* Build Assistant failure report
* Release Engineer validation report
* Package inspection and checksum generation

Tests must not mutate permanent source fixtures without copying them to temporary directories.

# 21. Documentation Requirements

The root README must explain:

1. What the repository is
2. Why it is a monorepo
3. Difference between applications, agents, tools, and models
4. Why the model is not the authority
5. Why shell and filesystem access are restricted
6. How to install Ollama
7. How to pull Qwen:

```bash
ollama pull qwen2.5-coder:14b
```

8. How to install repository dependencies:

```bash
npm ci
```

9. How to validate the repository:

```bash
npm run validate
```

10. How to run each application
11. How reports and traces work
12. How to use an external workspace safely
13. Known limitations of a 14B local model
14. Troubleshooting
15. Security warnings

Document that smaller local models may:

* Produce malformed structured output
* Select the wrong tool
* Miss cross-file dependencies
* Repeat actions
* Overwrite more code than intended
* Fail to resolve complex builds

Explain how the platform mitigates those issues through:

* Zod validation
* Bounded retries
* Step limits
* Explicit tool permissions
* Workspace isolation
* Dry-run modes
* Deterministic checks
* Review agents
* Repair-pass limits
* Local traces

Create architecture diagrams using Mermaid where useful.

Do not rely on external hosted diagrams.

# 22. Continuous Integration

Add a GitHub Actions workflow for the laboratory repository itself.

Create:

```text
.github/workflows/ci.yml
```

CI must:

* Run on pull requests and pushes to `main`
* Use the documented Node.js version
* Run `npm ci`
* Run `npm run validate`
* Not require Ollama
* Use the mock model for tests
* Cache npm dependencies appropriately
* Use least-privilege permissions
* Avoid write permissions unless necessary

Do not run live local-model integration tests in GitHub Actions.

# 23. Code Quality Standards

All source code must:

* Be complete and functional
* Use descriptive names
* Use strict TypeScript
* Include useful comments for non-obvious security or lifecycle behavior
* Avoid redundant comments
* Validate all external input
* Use explicit error handling
* Avoid swallowed exceptions
* Use `finally` for locks and long-running processes
* Avoid singleton global mutable state
* Avoid hidden fallback behavior
* Avoid arbitrary command execution
* Avoid raw path concatenation
* Avoid unsafe archive extraction
* Avoid giant multipurpose files
* Follow separation of concerns
* Include tests for critical behavior

Do not create placeholder modules whose functions only throw “not implemented.”

Do not create a superficial directory scaffold without working behavior.

# 24. Explicit Non-Goals

Do not implement:

* OpenAI hosted model support
* Paid API integrations
* Cloud deployment
* Browser-based UI
* Electron application
* VS Code extension
* GitHub issue automation
* Git commit or push automation
* Pull-request creation
* Autonomous deletion
* Autonomous package publishing
* WordPress Theme Factory
* File Assistant
* Repository Reviewer
* Docker-based code execution
* General-purpose shell access
* Remote telemetry
* External trace export

The repository should be architected so future providers or applications could be added, but do not implement speculative features beyond the stated scope.

# 25. Implementation Sequence

Proceed in this order:

1. Inspect the repository and confirm its actual starting state.
2. Create a detailed implementation plan.
3. Establish root npm-workspaces configuration.
4. Implement shared types.
5. Implement workspace security.
6. Implement local model client.
7. Implement tracing.
8. Implement filesystem tools.
9. Implement process tools.
10. Implement agent runtime.
11. Implement Code Editor.
12. Implement Build Assistant.
13. Implement Release Engineer.
14. Create sample projects.
15. Add tests.
16. Add documentation.
17. Add CI.
18. Install dependencies.
19. Generate `package-lock.json`.
20. Run complete validation.
21. Repair every failure.
22. Re-run validation from a clean state.
23. Inspect final repository structure.
24. Produce a final implementation report.

Do not stop after planning.

Do not stop after creating directories.

Do not leave validation failures for the user to resolve.

# 26. Required Final Validation

Before declaring completion, run:

```bash
npm ci
npm run format:check
npm run lint
npm run typecheck
npm run test
npm run build
npm run validate
```

Also run:

```bash
npm run code-editor -- --help
npm run build-assistant -- --help
npm run release-engineer -- --help
```

Exercise deterministic smoke workflows against temporary copies of the example projects using the mock model where required.

Confirm:

* No real API key is required.
* CI does not require Ollama.
* Tests do not require Ollama.
* The real runtime defaults to Ollama.
* The default model is `qwen2.5-coder:14b`.
* All agent actions are locally traced.
* Mutating workflows enforce workspace locks.
* Path traversal is rejected.
* Symlink escapes are rejected.
* Raw shell commands cannot be supplied by the model.
* Dry-run modes do not modify files.
* Watcher processes terminate cleanly.
* Failed workflows return nonzero exit codes.
* Release archives are inspected after creation.
* Generated reports are written under `reports/runs/`.
* The repository is clean of temporary runtime artifacts.

Use `git status` and inspect the final diff before completion.

# 27. Final Response Requirements

At completion, provide:

1. Concise implementation summary
2. Final repository tree
3. Shared packages implemented
4. Applications implemented
5. Security controls implemented
6. Tests implemented
7. Documentation created
8. Exact validation commands executed
9. Validation results
10. Example commands for all three applications
11. Known limitations
12. Any deliberate design decisions
13. Confirmation that no OpenAI API key is required
14. Confirmation that runtime model calls go to local Ollama
15. Confirmation that the repository contains exactly these application directories:

```text
apps/code-editor
apps/build-assistant
apps/release-engineer
```

Do not claim that a command passed unless you actually ran it and observed a successful result.

Do not conceal incomplete functionality.

# 28. Governing Design Rule

Maintain this responsibility boundary throughout the implementation:

```text
Repository application:
- Validates input
- Controls the workspace
- Starts approved processes
- Executes tools
- Determines pass or fail
- Records traces
- Enforces limits
- Packages artifacts

Qwen:
- Examines provided evidence
- Creates plans
- Requests approved tools
- Proposes or applies permitted source changes
- Interprets deterministic failures
- Produces structured findings
```

The pipeline controls the environment.

The model operates only through narrow, validated capabilities.

Build the repository accordingly.
