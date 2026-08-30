# When Not to Use AI

## Purpose

AI is useful when it improves a real workflow.

It is not automatically the right implementation for every problem.

The Mobile Release Playbook should use AI where it provides measurable value, and prefer deterministic software when the problem is better solved with:

```text
Rules
+
Schemas
+
State machines
+
Validated APIs
+
Normal application logic
+
Deterministic automation
```

The core rule is:

> **Do not use AI where deterministic software is more correct, cheaper, safer, easier to debug, or easier to operate.**

AI should reduce complexity, not become another source of it.

---

# Core Principle

Use AI when the task requires meaningful capabilities such as:

```text
Understanding unstructured information
Summarizing large amounts of context
Classifying ambiguous inputs
Reasoning across multiple pieces of evidence
Extracting information from natural language
Generating useful explanations
Assisting debugging
Routing work based on semantic context
```

Prefer deterministic software when the task is:

```text
Exact
Rule-based
Security-critical
Permission-critical
State-critical
Highly repetitive
Easy to validate
Already solved by normal code
```

The AI-SYSTEMS guidance explicitly prefers simple pipelines first, agents only when workflows justify them, and orchestration only when measurable ROI exists. It also requires grounding, constrained autonomy, validation before critical actions, explicit tool boundaries, and human override support.

---

# Decision Rule

Before adding AI, ask:

```text
1. Is the problem actually non-deterministic?
2. Does natural-language or semantic reasoning add value?
3. Can normal code solve it more reliably?
4. Can the result be validated?
5. What happens when the model is wrong?
6. Is the error cost acceptable?
7. Does AI reduce total complexity?
8. Does AI reduce meaningful human work?
9. Is the latency acceptable?
10. Is the ongoing model cost justified?
```

If deterministic software clearly wins:

```text
Do not use AI.
```

---

# Prefer Deterministic Software For

## Authentication

Do not use AI to decide:

```text
Whether a password is correct
Whether a session is valid
Whether an access token is valid
Whether MFA succeeded
Whether a user is authenticated
```

Use:

```text
Authentication providers
Cryptographic verification
Session validation
Token validation
```

AI may assist with support or explanation, but it must not become the authentication mechanism.

---

# Authorization

Never use an LLM as the final authorization layer.

Do not ask:

```text
"Should this user be allowed to access release 104?"
```

and trust the model's answer.

Use deterministic server-side checks:

```text
Identity
+
Resource ownership
+
Role
+
Permission
+
Tenant
+
Policy
```

The security system explicitly requires server-side authorization, least privilege, explicit permission checks, and deny-by-default behavior.

AI can recommend or explain an authorization decision, but the trusted backend must enforce it.

---

# Secret Management

Never use AI as a secret-management system.

Do not ask an agent to:

```text
Generate production signing credentials
Store private keys in memory
Decide whether a secret should be exposed
Copy credentials into configuration
Print environment secrets for debugging
```

Use:

```text
Managed secret stores
Environment configuration
Access controls
Rotation
Revocation
Audit logs
```

The security guidance explicitly prohibits storing secrets in source code, Git history, frontend bundles, logs, or public configuration.

---

# Cryptography

Do not use AI to invent:

```text
Encryption algorithms
Cryptographic protocols
Password hashing algorithms
Token signing schemes
Key derivation schemes
Randomness mechanisms
```

Use established cryptographic libraries and standards.

AI may explain cryptographic concepts or review implementation, but cryptographic correctness should come from established primitives and deterministic code.

---

# Exact Validation

Do not use AI for validation that has an exact machine-checkable answer.

Examples:

```text
Is versionCode an integer?
Does package.json parse?
Is a required field present?
Does a URL match an allowlist?
Does a JSON document match a schema?
Is a checksum correct?
Does a file exist?
Is a version greater than the previous version?
```

Use:

```text
Schema validation
Type checking
Regular expressions where appropriate
Database constraints
Static analysis
Unit tests
Deterministic functions
```

---

# Release State Transitions

Do not let an LLM freely mutate release state.

Bad:

```text
Agent:
"The release looks ready."

System:
release.status = RELEASED
```

Better:

```text
Agent:
Recommendation = APPROVE

Workflow:
Validate gates
→ verify approvals
→ verify permissions
→ execute action
→ verify external state
→ update release state
```

AI output must not become the source of truth.

The AI-SYSTEMS guidance requires validation before critical actions, explicit tool boundaries, permission boundaries, execution tracing, and rollback handling where possible.

---

# Financial or Irreversible Actions

Avoid direct AI authority over:

```text
Payments
Refunds
Purchases
Credential rotation
Permanent deletion
Production rollback
Store release
High-impact configuration changes
```

Use deterministic workflows with:

```text
Validated inputs
+
Explicit permissions
+
Idempotency
+
Approval
+
Auditability
+
Post-action verification
```

AI may prepare or recommend the action.

The actual action should be bounded and verified.

---

# Database Integrity

Do not use AI to replace database guarantees.

Never rely on an LLM for:

```text
Uniqueness
Foreign keys
Required fields
Transactions
Concurrency control
Referential integrity
Idempotency
Migration safety
```

Use:

```text
Database constraints
Transactions
Indexes
Locks
Unique keys
Validated migrations
```

AI may help design or review these mechanisms.

It should not be the mechanism enforcing them.

---

# Deterministic Business Rules

If a rule can be expressed clearly as code, prefer code.

Example:

```text
if device_test != PASS:
 block_release
```

Do not replace it with:

```text
Ask an LLM whether the release appears ready.
```

Rules should remain deterministic when the business logic itself is deterministic.

---

# Version Comparisons

Do not use AI to decide:

```text
Is 2.4.0 greater than 2.3.9?
```

Use a version parser.

Likewise:

```text
Build 104 > Build 103
```

should be handled by normal comparison logic.

---

# File and Repository Operations

Do not use AI where a deterministic tool can answer directly.

Examples:

```text
Does this file exist?
What files changed?
What is the Git branch?
What is the current commit?
What package version is installed?
What scripts exist?
```

Use:

```text
Filesystem
Git
Package manager
Repository APIs
CI APIs
```

AI can interpret the results afterward.

---

# Test Results

Do not ask AI to invent or infer test execution.

Bad:

```text
No test output available.

Agent:
Tests probably passed.
```

Correct:

```text
Run the tests.
Read the result.
Record the actual result.
```

If execution is unavailable:

```text
UNKNOWN
```

The hallucination and regression evaluation standards explicitly treat false execution claims as critical failures.

---

# Build Verification

Do not use AI confidence as proof that a build succeeded.

Use:

```text
Actual build output
Artifact existence
Artifact checksum
Build ID
CI result
Signing verification
```

AI can summarize the evidence.

It should not fabricate it.

---

# Store Status

Do not use AI memory to determine current store state.

Examples:

```text
Uploaded
Processed
In Review
Approved
Released
Rejected
```

These should come from:

```text
Store APIs
Store dashboards
Verified external state
```

not model assumptions.

Store behavior and requirements can change, so current authoritative platform sources should be used when verification is required.

---

# Exact Security Decisions

Do not use AI as the only control for:

```text
Authorization
Secret exposure prevention
Tenant isolation
Permission enforcement
Credential validation
Security policy enforcement
```

The security system specifically requires AI-generated actions to be validated and bounded by permissions.

AI is useful for:

```text
Threat analysis
Code review
Security triage
Attack-path analysis
Log interpretation
```

but trusted controls should remain deterministic.

---

# Compliance Gates

Avoid making compliance a pure LLM decision.

Bad:

```text
AI:
"This appears compliant."
```

Better:

```text
Deterministic controls
+
Required evidence
+
Policy checks
+
Human review where required
```

AI may assist with:

```text
Document analysis
Requirement mapping
Gap identification
Evidence summarization
```

but the compliance state should have a defined source of truth.

---

# When AI Adds More Complexity Than Value

Do not add AI if it introduces:

```text
Model infrastructure
Prompt management
Evaluation systems
Observability
Retries
Context management
Security controls
Token costs
Latency
Failure modes
```

without meaningful benefit.

The AI-SYSTEMS guidance explicitly warns against agent sprawl, fake autonomy, orchestration theater, uncontrolled tool execution, and unnecessary memory systems.

---

# Do Not Use Agents Just Because You Can

A single deterministic function is better than an agent when the task is simple.

Bad:

```text
Agent
→ Agent
→ Agent
→ Tool
→ Reviewer
→ Another Agent
```

for:

```text
Parse version from package.json.
```

Good:

```text
Read package.json
→ parse version
```

Agents should exist because the workflow needs reasoning, not because the architecture can support agents.

---

# Do Not Use AI For Simple Routing

If routing is based on explicit fields:

```text
platform = ios
```

use:

```text
switch platform:
 ios → iOS workflow
 android → Android workflow
```

Do not ask an LLM:

```text
"Which platform workflow should I use?"
```

unless the input itself requires semantic interpretation.

---

# Do Not Use AI For Simple Classification

If classification is fully deterministic:

```text
file extension
HTTP status
version range
environment
platform
build type
```

use code.

AI becomes useful when the classification depends on:

```text
Natural language
Ambiguous evidence
Semantic meaning
Unstructured documents
```

---

# Do Not Use AI For Simple Extraction

For structured data:

```text
JSON
YAML
CSV
package.json
eas.json
Info.plist
Gradle configuration
```

use parsers.

AI is more appropriate when the source is:

```text
Unstructured text
Reviewer comments
Logs
Natural-language requirements
Long documents
```

---

# Do Not Use AI To Replace APIs

If an authoritative API exists:

```text
Use the API.
```

Do not ask an LLM to guess the current state.

Example:

```text
GitHub API:
PR status
```

is better than:

```text
AI:
"The PR is probably merged."
```

---

# Do Not Use AI To Replace Monitoring

AI should not replace:

```text
Metrics
Logs
Traces
Alerts
Health checks
Error tracking
```

Use telemetry as the source of operational truth.

AI can help interpret telemetry and identify patterns.

The observability system prioritizes correlated telemetry, production visibility, AI workflow visibility, and debugging speed.

---

# Do Not Use AI To Replace Tests

AI can:

```text
Generate tests
Suggest edge cases
Analyze failures
Create regression cases
Review coverage
```

But it should not replace:

```text
Unit tests
Integration tests
E2E tests
Security tests
Type checks
Static analysis
```

The final verification should remain executable and repeatable.

---

# Do Not Use AI For Deterministic Scheduling

If a workflow is:

```text
Every day at 09:00
```

use a scheduler.

Do not use an agent to decide when to run a task that already has an explicit schedule.

AI becomes useful when scheduling requires:

```text
Context
Prioritization
Dynamic conditions
Unstructured inputs
```

---

# Do Not Use AI For Deterministic Retry Logic

Retry behavior should be code.

Example:

```text
HTTP 429
→ exponential backoff
→ bounded retries
```

Do not ask an LLM:

```text
"Should we retry this request?"
```

when the policy is already known.

For side-effecting actions, use:

```text
Idempotency
State checks
Bounded retries
Explicit failure states
```

---

# Do Not Use AI For Time-Critical Control Loops

Avoid AI for systems requiring deterministic, low-latency guarantees such as:

```text
Safety interlocks
Hardware control
Strict real-time control
Protocol state machines
Network congestion control
Low-level synchronization
```

Use deterministic systems.

AI may operate around the control loop for:

```text
Analysis
Prediction
Optimization
Operator assistance
```

but not as an uncontrolled replacement for deterministic safety logic.

---

# Do Not Use AI When Failure Cost Is Unacceptable

Ask:

```text
What happens if the model is wrong?
```

If the answer is:

```text
Security breach
Permanent data loss
Unauthorized access
Duplicate financial transaction
Incorrect production release
Credential exposure
Irreversible operational damage
```

do not give the model unrestricted authority.

Use:

```text
Deterministic controls
+
Bounded tools
+
Validation
+
Human approval
```

where appropriate.

---

# Do Not Use AI Without an Evaluation Path

If the output cannot be evaluated, validated, or monitored, be cautious about putting AI in a critical path.

Before production use, define:

```text
Expected behavior
Evaluation cases
Failure cases
Security tests
Regression tests
Observability
Fallback
```

The AI-SYSTEMS review process explicitly requires review of hallucination risk, orchestration complexity, retrieval quality, autonomy safety, observability, performance, and cost before production validation.

---

# Do Not Use AI Without a Fallback

For important workflows, define what happens when:

```text
Model unavailable
Model times out
Tool fails
Context is missing
Output is malformed
Model produces conflicting results
External service is unavailable
```

Possible fallback:

```text
Deterministic path
Manual review
Retry
Queue
Safe failure
```

Do not design a critical workflow around the assumption that the model will always respond correctly.

---

# Do Not Use AI For Hidden State

Avoid architectures where AI silently changes:

```text
Database records
Permissions
Release state
Configuration
Memory
Production state
```

without a visible workflow.

AI actions should be:

```text
Bounded
Traceable
Observable
Auditable
Reversible where possible
```

---

# Do Not Use AI For Unbounded Autonomy

Avoid:

```text
"Do whatever is necessary to release the app."
```

This creates unclear authority.

Prefer:

```text
Allowed tools
+
Allowed actions
+
Explicit conditions
+
Maximum retries
+
Approval boundaries
+
Execution tracing
+
Rollback/recovery
```

AI-SYSTEMS explicitly favors constrained execution and permission boundaries over unrestricted tool access.

---

# Do Not Use AI When Deterministic Rules Are More Explainable

If the reason for a decision must always be exact:

```text
Build failed
→ release blocked
```

is easier to explain than:

```text
AI believes the release is risky.
```

Use deterministic gates for hard release conditions.

Use AI for analysis around those gates.

---

# Good Hybrid Pattern

The strongest pattern is often:

```text
Deterministic System
 ↓
Evidence
 ↓
AI Analysis
 ↓
Structured Recommendation
 ↓
Deterministic Validation
 ↓
Approval
 ↓
Bounded Action
 ↓
External Verification
```

Example:

```text
CI
→ actual test results

AI
→ explain failures and identify likely causes

Workflow
→ enforce release gates

Human
→ approve when required

Store API
→ perform submission

Verification
→ confirm actual store state
```

This gives AI useful leverage without making AI the source of truth.

---

# AI Should Usually Sit Above the Source of Truth

Prefer:

```text
Source of truth
↓
AI interpretation
↓
Recommendation
```

rather than:

```text
AI
↓
Creates truth
```

Examples:

```text
CI → AI summarizes CI
Store API → AI summarizes store state
Git → AI explains changes
Database → AI analyzes data
Monitoring → AI interprets incidents
```

The authoritative system remains authoritative.

---

# AI Is Most Useful For Unstructured Work

Strong use cases include:

```text
Release notes generation
Store rejection analysis
Crash-log interpretation
Security review assistance
Privacy review assistance
Dependency-change analysis
Natural-language requirement extraction
Documentation analysis
Issue triage
Evidence summarization
Cross-document comparison
Debugging hypotheses
```

These tasks benefit from semantic reasoning while still allowing verification.

---

# AI Usefulness Test

Before implementing AI, score the problem:

| Question | Yes | No |
|---|---:|---:|
| Input is unstructured? | +1 | 0 |
| Semantic reasoning is required? | +1 | 0 |
| Ambiguity is meaningful? | +1 | 0 |
| AI can save meaningful human time? | +1 | 0 |
| Output can be validated? | +1 | 0 |
| Failure cost is bounded? | +1 | 0 |
| Deterministic solution is insufficient? | +1 | 0 |
| AI reduces overall complexity? | +1 | 0 |

This is a heuristic, not a mathematical gate.

If most answers are:

```text
No
```

do not add AI without a strong reason.

---

# AI vs Deterministic Decision Matrix

| Problem | Preferred Approach | Why |
|---|---|---|
| Authentication | Deterministic | Security-critical |
| Authorization | Deterministic | Must be enforceable |
| Schema validation | Deterministic | Exact |
| Version comparison | Deterministic | Exact |
| File existence | Deterministic | Exact |
| Test execution | Deterministic | Must be verified |
| Build execution | Deterministic | Must be verified |
| Store status | API/state | External source of truth |
| Release gates | Deterministic | Explicit policy |
| Database constraints | Database | Integrity |
| Retry policy | Deterministic | Predictable |
| Scheduling | Scheduler | Exact timing |
| Secret management | Secret system | Security |
| Cryptography | Libraries/standards | Safety |
| Crash analysis | AI + evidence | Semantic reasoning |
| Rejection analysis | AI + evidence | Unstructured input |
| Release-note drafting | AI | Generation |
| Documentation analysis | AI | Large unstructured context |
| Log interpretation | AI + telemetry | Pattern recognition |
| Security review assistance | AI + deterministic checks | Reasoning + verification |
| Metadata review | AI + rules | Semantic + exact validation |

---

# AI Should Not Become Architecture By Default

Do not add:

```text
LLM
Agent
Vector database
Memory layer
Orchestrator
Prompt router
Multi-agent system
```

just because a feature contains the word:

```text
AI
```

Start with the smallest useful capability.

Possible progression:

```text
Deterministic code
→ AI-assisted function
→ Structured AI workflow
→ Tool-calling workflow
→ Agent
→ Multi-agent orchestration
```

Move right only when the problem requires it.

---

# Do Not Add Memory Without a Memory Problem

Avoid persistent AI memory when the workflow only needs:

```text
Current request
Current release context
Current repository context
```

The AI-SYSTEMS guidance prefers session memory first and long-term memory only when justified, with expiration, correction, observability, isolation, and traceability.

Use persistent memory only when it provides clear value.

---

# Do Not Add RAG Without a Grounding Problem

Use RAG when:

```text
Knowledge is large
Knowledge changes
Source attribution matters
Current documents matter
Context cannot fit reliably
```

Do not add a vector database just to answer a small static question.

Use existing infrastructure when sufficient.

The data guidance explicitly recommends evaluating whether PostgreSQL plus pgvector or existing infrastructure is sufficient before introducing dedicated vector infrastructure.

---

# Do Not Add Multi-Agent Orchestration Without a Coordination Problem

Use multiple agents when:

```text
Responsibilities are genuinely different
Independent analysis provides value
Parallel work provides measurable benefit
Specialized tools or context are required
```

Do not use:

```text
5 agents
```

to perform:

```text
1 deterministic operation.
```

More agents create:

```text
More latency
More cost
More context transfer
More failure modes
More debugging complexity
More coordination overhead
```

---

# AI Cost Matters

Do not use AI when:

```text
The deterministic alternative is nearly free
+
AI provides little additional value
```

Consider:

```text
Input tokens
Output tokens
Tool calls
Retries
Context size
Model tier
Latency
Evaluation cost
Observability cost
```

AI should create measurable leverage.

---

# AI Latency Matters

Avoid AI in a latency-sensitive path when:

```text
A deterministic operation can respond immediately.
```

Examples:

```text
Permission check
Feature flag
Version comparison
Configuration lookup
Health check
Cache lookup
```

Use AI outside the critical path where possible:

```text
Analyze asynchronously
→ notify operator
```

---

# AI Operational Burden Matters

Adding AI creates operational requirements:

```text
Prompt versioning
Model changes
Evaluation
Observability
Token tracking
Rate limiting
Fallbacks
Tool permissions
Security reviews
Regression tests
```

If the value does not justify that burden:

```text
Do not add AI.
```

---

# AI Security Boundary

AI-enabled systems introduce risks including:

```text
Prompt injection
Indirect prompt injection
Data leakage
Tool abuse
Unauthorized actions
Excessive agency
Sensitive context exposure
RAG poisoning
Cost abuse
```

These are explicitly called out by the security system.

If the proposed AI feature cannot be safely bounded:

```text
Do not put it in the critical path.
```

---

# When AI Should Be Advisory

Use advisory AI when:

```text
Human judgment is required
+
AI can reduce analysis time
+
Wrong recommendations are recoverable
```

Examples:

```text
"These are the likely causes of the crash."

"These metadata fields may need updating."

"This dependency change may affect iOS signing."

"This store rejection appears related to reviewer access."
```

The human or deterministic workflow remains authoritative.

---

# When AI Can Execute

Execution can be appropriate when:

```text
Action is bounded
+
Permissions are explicit
+
Inputs are validated
+
Action is observable
+
Failure is recoverable
+
Duplicate execution is controlled
+
Risk is acceptable
```

Examples may include:

```text
Create draft release notes
Open a GitHub issue
Prepare a release checklist
Run a read-only repository inspection
Generate a proposed configuration diff
```

Higher-impact actions need stronger controls.

---

# When Human Approval Is Required

Consider explicit approval for:

```text
Production release
Store submission
Rollback
Credential changes
Security policy changes
Privacy declarations
Permanent deletion
High-impact external actions
```

AI should prepare the action, not silently bypass the approval boundary.

---

# Red-Team Test

Before approving an AI use case, ask:

```text
How could the model be wrong?

What if the input is malicious?

What if retrieved content contains instructions?

What if the model hallucinates?

What if the tool times out?

What if the action succeeds but the response is lost?

What if the model retries?

What if two agents disagree?

What if context is stale?

What if the model gets excessive permissions?

What if the model runs indefinitely?

What if the model cost spikes?
```

If the system has no safe answer, simplify or reject the AI design.

The AI red-team guidance specifically targets hallucination, prompt injection, unsafe automation, tool misuse, infinite loops, retry storms, context drift, and agent conflicts.

---

# Practical Rule Set

Use:

```text
Code for certainty.
AI for ambiguity.
APIs for authoritative state.
Databases for integrity.
Schedulers for timing.
Queues for durable async work.
Monitoring for operational truth.
Humans for high-impact judgment where required.
```

Then combine them when useful:

```text
Deterministic system
+
AI reasoning
+
Deterministic enforcement
```

---

# Anti-Patterns

## AI For Every Problem

```text
Problem:
Exact rule.

Solution:
LLM.
```

Wrong.

---

## AI As Source of Truth

```text
Agent says:
RELEASED

Database:
SUBMITTED

System:
Trust agent.
```

Wrong.

---

## AI As Authorization

```text
LLM:
User seems trustworthy.
```

Wrong.

---

## AI As Test Runner

```text
Agent:
Tests should pass.
```

Wrong.

---

## AI As Monitoring

```text
No monitoring data.
Agent:
Everything looks healthy.
```

Wrong.

---

## AI As Secret Store

```text
Agent memory:
Production signing key.
```

Wrong.

---

## AI As Unbounded Operator

```text
"Do whatever is needed."
```

Wrong.

---

## Agent Sprawl

```text
One simple task
→ many agents
→ many prompts
→ many handoffs
```

Wrong unless measurable coordination value exists.

---

# Architecture Pattern

For most release workflows, prefer:

```text
 ┌───────────────┐
 │ Source Truth │
 │ Git / CI / │
 │ Store / DB │
 └───────┬───────┘
 │
 ▼
 ┌───────────────┐
 │ AI Analysis │
 │ Summarize │
 │ Classify │
 │ Explain │
 └───────┬───────┘
 │
 ▼
 ┌───────────────┐
 │ Deterministic │
 │ Validation │
 │ Release Gates │
 └───────┬───────┘
 │
 approval
 │
 ▼
 ┌───────────────┐
 │ Bounded Tool │
 │ Execution │
 └───────┬───────┘
 │
 ▼
 ┌───────────────┐
 │ External │
 │ Verification │
 └───────────────┘
```

This keeps AI useful without making it the authority over critical state.

---

# Final Decision Checklist

Before introducing AI:

```text
[ ] Problem actually benefits from semantic reasoning
[ ] Deterministic solution was considered
[ ] AI provides measurable value
[ ] Failure cost is understood
[ ] Output can be evaluated
[ ] Source of truth remains deterministic
[ ] Security boundaries remain deterministic
[ ] Authorization remains deterministic
[ ] Critical state transitions remain controlled
[ ] Tool permissions are bounded
[ ] Human approval exists where required
[ ] Failure handling exists
[ ] Timeout behavior is defined
[ ] Retry behavior is defined
[ ] Idempotency is addressed
[ ] Observability exists
[ ] Regression tests exist
[ ] Hallucination tests exist where relevant
[ ] Cost is acceptable
[ ] Latency is acceptable
[ ] Operational burden is justified
[ ] AI can be removed or bypassed safely where appropriate
```

---

# Quick Decision Table

| Question | If Yes | If No |
|---|---|---|
| Is the task deterministic? | Prefer code | Continue evaluation |
| Is semantic reasoning needed? | AI may help | Prefer code |
| Is authoritative state available? | Use it | Do not guess |
| Can output be validated? | AI may be suitable | Avoid critical use |
| Is failure recoverable? | Consider bounded AI | Avoid autonomous action |
| Is the action high impact? | Add controls/approval | Continue |
| Does AI reduce real work? | Continue | Do not add it |
| Does AI add more complexity than value? | Reject | Continue |
| Can the system operate safely without AI? | Keep fallback | Strengthen fallback |
| Is there measurable ROI? | Consider implementation | Reject |

---

# Final Rule

The goal is not:

```text
Use AI everywhere.
```

The goal is:

```text
Use AI where reasoning creates leverage.
Use deterministic systems where certainty matters.
```

The preferred production pattern is:

```text
Authoritative source
→ AI interpretation
→ deterministic validation
→ bounded execution
→ external verification
```

Never let AI replace:

```text
Authentication
Authorization
Database integrity
Cryptography
Exact validation
Test execution
Build verification
Store state
Secret management
Deterministic release gates
```

Use AI to make engineers faster and workflows smarter:

```text
Analyze
+
Explain
+
Summarize
+
Classify
+
Extract
+
Recommend
+
Assist
```

Then keep the final system:

```text
Grounded
+
Bounded
+
Observable
+
Evaluated
+
Recoverable
+
Simple
```

If AI does not make the system meaningfully better, **do not add it.**

---

# Related documentation

### Getting started

- `ai/getting-started/README.md`
- `ai/getting-started/when-to-use-ai.md`

### Agents

- `ai/agents/README.md`

### Tools

- `ai/tools/claude-code.md`
- `ai/tools/README.md`

### AI security

- `ai/security/README.md`
