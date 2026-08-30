# When to Use AI

## Purpose

AI should be used when it solves a real release-engineering problem better than ordinary software, deterministic automation, or a human working with clear tools.

The goal of AI in the Mobile Release Playbook is not to make the repository "AI-powered."

The goal is to help developers:

```text
Understand
→ Analyze
→ Decide
→ Prepare
→ Execute safely
→ Verify
```

AI is most useful where the input is unstructured, the reasoning is semantic, or the amount of information is too large or repetitive for efficient manual handling.

The core rule is:

> **Use AI where reasoning creates measurable leverage, and keep deterministic systems responsible for facts, permissions, state, and high-risk execution.**

---

# What AI Is Good At

AI is particularly useful for work involving:

```text
Natural language
Unstructured documents
Logs
Reviewer messages
Release notes
Repository changes
Large bodies of documentation
Ambiguous failures
Cross-document comparison
Pattern recognition
Classification
Summarization
Drafting
Reasoning over evidence
```

Typical release-engineering examples include:

```text
Analyze a crash
→ identify likely causes
→ suggest verification steps

Analyze a store rejection
→ classify the issue
→ identify likely missing information
→ prepare a remediation checklist

Review a release
→ inspect available evidence
→ identify gaps
→ explain blockers

Generate release notes
→ summarize verified product changes
→ produce a developer-readable draft

Review metadata
→ compare current product behavior with store listing
→ identify inconsistencies
```

AI should assist these workflows without becoming the source of truth.

---

# The AI Boundary

A strong architecture separates:

```text
Authoritative systems
        ↓
Evidence
        ↓
AI reasoning
        ↓
Structured recommendation
        ↓
Deterministic validation
        ↓
Human approval where required
        ↓
Bounded execution
        ↓
External verification
```

Examples of authoritative systems:

```text
Git
CI
Test runners
Package managers
Build systems
App Store Connect
Google Play Console
Databases
Monitoring systems
Repository files
```

AI should interpret these systems, not replace them.

---

# Use AI When the Input Is Unstructured

AI is a strong fit when the input cannot be handled cleanly by a simple parser or rule.

Examples:

```text
App review rejection message
Crash stack trace with surrounding logs
Long CI failure output
Developer issue description
Release notes from multiple commits
Privacy documentation
Store reviewer instructions
Large repository documentation
```

Example:

```text
Input:
"Reviewer says users cannot access the required feature after login."

AI:
Classify the likely review-access problem.
Identify relevant checks.
Suggest what evidence to inspect.

Deterministic system:
Verify the actual login flow and build.
```

This combination is stronger than asking AI to make the final decision alone.

---

# Use AI When Semantic Reasoning Is Required

Use AI when the task depends on meaning rather than exact values.

Examples:

```text
Does this change introduce a privacy concern?

What is the likely cause of this crash?

Does this rejection relate to reviewer access?

Which parts of the release changed meaningfully?

Are these two requirements describing the same behavior?

Does the current store description accurately represent the feature?
```

These questions can benefit from semantic analysis.

---

# Use AI When Large Context Must Be Compressed

AI is useful for reducing large amounts of information into something a developer can act on.

Examples:

```text
100 changed files
→ release-impact summary

Large CI log
→ failure summary

Multiple store messages
→ rejection timeline

Many commits
→ release-note draft

Long technical documentation
→ relevant implementation guidance
```

The summary must preserve important:

```text
Blockers
Unknowns
Warnings
Evidence
Version/build identity
Security findings
Approval state
```

Never optimize summarization by silently removing critical information.

---

# Use AI For Release Analysis

AI can help answer:

```text
What changed?

What could affect the release?

What needs verification?

What looks risky?

What is missing?

What should the developer inspect next?
```

Example workflow:

```text
Git diff
    ↓
AI analyzes changed areas
    ↓
Classifies potential release impact
    ↓
Maps risks to checks
    ↓
Produces review checklist
    ↓
Developer verifies findings
```

AI should not automatically convert a risk assessment into:

```text
APPROVED
```

---

# Use AI For Debugging Assistance

Debugging is one of the strongest AI use cases.

AI can help with:

```text
Crash-log analysis
Stack-trace interpretation
Error classification
Likely root causes
Reproduction hypotheses
Regression analysis
Dependency-change analysis
Configuration comparison
Suggested diagnostic commands
```

A good debugging workflow is:

```text
Observed failure
→ collect evidence
→ AI identifies hypotheses
→ developer verifies
→ reproduce
→ isolate cause
→ fix
→ regression test
```

Do not allow:

```text
Possible cause
```

to become:

```text
Confirmed root cause
```

without evidence.

---

# Use AI For Store Rejection Analysis

Store rejection messages are often:

```text
Natural language
Context-heavy
Ambiguous
Platform-specific
Reviewer-written
```

AI can help turn them into an actionable workflow.

Example:

```text
Reviewer message
      ↓
AI classification
      ↓
Likely category
      ↓
Relevant repository areas
      ↓
Required evidence
      ↓
Suggested checks
      ↓
Developer remediation
```

Useful categories may include:

```text
Reviewer access
Metadata
Privacy
Permissions
Functionality
Account requirements
Content
Payments
Technical submission issue
```

The category should remain a working classification until verified.

---

# Use AI For Release Notes

AI is a good fit for drafting:

```text
Release notes
Changelog entries
Internal release summaries
QA summaries
Migration notes
Developer handoff notes
```

Recommended flow:

```text
Verified commits
+
Verified issue descriptions
+
Verified release changes
        ↓
AI draft
        ↓
Human review
        ↓
Final release notes
```

Do not let AI invent features or user-facing claims.

---

# Use AI For Documentation Analysis

AI can help developers navigate large documentation sets.

Good uses:

```text
Find relevant guidance
Compare two documented approaches
Summarize a framework release
Extract configuration requirements
Explain a technical concept
Map a requirement to project files
```

For current or volatile platform requirements:

```text
Official source
→ retrieve current evidence
→ AI explains it
```

Do not rely on model memory for changing platform policies.

---

# Use AI For Repository Analysis

AI can help inspect a repository for release impact.

Examples:

```text
Which files affect Android release?

What changed in the iOS signing flow?

Did this dependency change include native code?

Which configuration files are involved?

Which tests are relevant to this change?

What release documentation is now outdated?
```

The repository itself remains authoritative.

AI should inspect actual files and tools rather than inventing repository structure.

---

# Use AI For Metadata Review

AI is useful for semantic consistency checks.

Example:

```text
Current feature:
Document scanning

Store description:
No mention of document scanning

Screenshots:
Old onboarding
```

AI can identify:

```text
Potential metadata inconsistency
```

Then deterministic or human review can verify:

```text
What changed
What must be updated
What is actually supported
```

AI should never invent marketing claims.

---

# Use AI For Privacy Review Assistance

AI can help identify areas that deserve privacy review.

Examples:

```text
New analytics SDK
New camera usage
New location usage
New tracking behavior
New account information
New third-party service
New permissions
```

Workflow:

```text
Code/config change
→ AI identifies potential privacy impact
→ inspect actual data flow
→ verify declarations
→ human/security/privacy review where required
```

AI is an analysis layer, not the privacy source of truth.

---

# Use AI For Dependency Impact Analysis

AI can help explain the practical impact of dependency changes.

Examples:

```text
What changed between versions?

Is this dependency native?

Could this affect iOS builds?

Could this affect Android builds?

Which parts of the repository use it?

What tests should be rerun?
```

Combine:

```text
Package manifest
+
Lockfile
+
Git diff
+
Framework documentation
+
Build/test results
```

Do not let AI invent package versions or compatibility claims.

---

# Use AI For QA Assistance

AI can improve test planning.

Examples:

```text
Generate edge cases
Identify affected flows
Review existing test coverage
Analyze failed tests
Suggest regression scenarios
Group related failures
```

The actual gate remains:

```text
Executable tests
+
real test results
```

AI should not replace test execution.

---

# Use AI For Failure Triage

AI is useful when many failures need to be grouped.

Example:

```text
100 CI failures
```

AI can identify:

```text
Likely same root failure
Dependency-related failures
Environment failures
Independent failures
Possible flaky tests
```

Then deterministic logs and test results confirm the classification.

---

# Use AI For Repetitive Engineering Analysis

AI can provide leverage when developers repeatedly perform the same reasoning task.

Examples:

```text
Review release changes
Summarize CI failures
Analyze store rejection
Prepare release notes
Check metadata consistency
Create regression candidates
Explain build failures
```

If the task happens frequently and has clear inputs/outputs, an AI workflow may provide meaningful leverage.

---

# Use AI When Human Review Is Expensive

AI can reduce the amount of information a developer must inspect manually.

Example:

```text
Raw:
2,000 lines of CI output

AI:
Relevant failure
+
likely affected module
+
important evidence
+
recommended next checks

Developer:
Verifies the evidence.
```

This is a good use of AI because:

```text
AI reduces reading and reasoning effort.
```

It does not need to become the final authority.

---

# Use AI For Classification With Ambiguous Inputs

AI is useful when categories depend on meaning.

Example:

```text
Input:
"Reviewer cannot access the subscription screen after signing in."

Potential classification:
REVIEW_ACCESS
```

A deterministic classifier may struggle if the same concept is expressed in many ways.

AI can produce:

```text
Category
+
confidence
+
evidence
+
reason
```

The workflow can then apply deterministic policy.

---

# Use AI For Recommendations

AI can recommend:

```text
What to inspect
What test to run
What documentation to read
What files may be affected
What risks deserve attention
What remediation options exist
```

Recommendations should remain distinguishable from execution.

Example:

```text
Recommendation:
Run iOS device validation.

Actual state:
Device validation has not run.
```

Never confuse the two.

---

# Use AI For Drafting, Not Final Truth

Strong drafting use cases:

```text
Release notes
Changelog entries
Issue descriptions
PR summaries
Store metadata drafts
Incident summaries
Documentation drafts
Test-case drafts
```

Recommended:

```text
Evidence
→ AI draft
→ validation
→ human review
→ publish
```

This is safer than autonomous publication.

---

# Use AI With Retrieval When Knowledge Matters

Use retrieval when the answer depends on:

```text
Large documentation
Current project configuration
Repository-specific knowledge
Current platform requirements
Internal release procedures
Historical project decisions
```

Preferred flow:

```text
Retrieve relevant evidence
→ rank/select
→ provide context to AI
→ generate grounded response
→ preserve evidence references
```

The AI-SYSTEMS guidance prefers:

```text
Simple pipelines first
RAG when grounding matters
Agents only when workflows justify them
Orchestration only when measurable ROI exists
```

Avoid adding a vector database or complex retrieval system when ordinary search or existing infrastructure is enough.

---

# Use AI With Tools When External Actions Are Needed

Tool calling is useful when AI needs verified information from systems such as:

```text
Git
CI
Issue tracker
Store APIs
Repository APIs
Monitoring systems
Build systems
```

Preferred pattern:

```text
AI decides what information is needed
→ tool retrieves authoritative data
→ AI interprets result
→ workflow validates
```

For actions:

```text
AI proposes
→ permission check
→ validation
→ bounded tool execution
→ result verification
```

Do not provide unrestricted tool access.

---

# Use AI For Bounded Automation

AI can safely automate lower-risk work when:

```text
The task is well-defined
+
Inputs are constrained
+
Tools are limited
+
Output is validated
+
Failure is recoverable
+
Actions are observable
```

Examples:

```text
Create a draft release checklist
Generate release notes
Open a draft issue
Prepare a metadata update
Summarize a failed build
Create a regression-test proposal
```

Higher-impact actions require stronger controls.

---

# Use AI When the Output Can Be Evaluated

Good AI use cases have measurable evaluation criteria.

Example:

```text
Store rejection classification
```

can be evaluated against:

```text
Known category
Required evidence
Expected next checks
Forbidden claims
```

Likewise:

```text
Crash analysis
```

can be evaluated for:

```text
Evidence use
Correct hypotheses
Missing evidence detection
Useful reproduction steps
No false certainty
```

If you cannot tell whether the AI is getting better or worse, the workflow is difficult to operate safely.

---

# Use AI When Failure Is Recoverable

Good AI use cases generally tolerate some uncertainty.

Examples:

```text
Drafting
Summarization
Analysis
Triage
Recommendations
Documentation assistance
Test-case suggestions
```

If AI is wrong:

```text
Human catches it
```

or:

```text
Deterministic validation catches it
```

This is much safer than putting an unverified model decision directly in a high-impact path.

---

# Use AI With Constrained Autonomy

When AI must act, define:

```text
Allowed tools
Allowed resources
Allowed actions
Maximum retries
Maximum execution time
Required approvals
Validation rules
Rollback/recovery
Audit trail
```

The AI-SYSTEMS architecture favors constrained autonomy, explicit tool boundaries, validation before critical actions, observability, permission boundaries, and rollback handling where possible.

Avoid:

```text
"Do whatever is necessary."
```

Prefer:

```text
"Inspect the release.
You may read repository and CI data.
You may prepare a release checklist.
You may not submit or release anything."
```

---

# Use AI When It Reduces Total Complexity

AI should make the system simpler for the developer.

Good:

```text
CI logs
→ AI summarizes failure
→ developer understands issue faster
```

Bad:

```text
CI logs
→ agent router
→ retrieval agent
→ debugging agent
→ reviewer agent
→ memory agent
→ orchestration layer
→ final agent
```

for a problem that could be solved with:

```text
CI logs
→ one focused analysis step
```

The AI-SYSTEMS guidance explicitly warns against agent sprawl and orchestration theater.

---

# Start With the Smallest AI Capability

Use this progression:

```text
1. Deterministic code
2. AI-assisted function
3. Structured AI workflow
4. Tool-calling workflow
5. Agent
6. Multi-agent orchestration
```

Move to the next level only when the current level cannot solve the real problem.

Do not start with multi-agent architecture.

---

# AI Function

Start with a focused capability:

```text
analyzeCrash(logs)
classifyRejection(message)
summarizeRelease(changes)
draftReleaseNotes(changes)
reviewMetadata(product, listing)
```

This is often easier to:

```text
Test
Observe
Version
Replace
Debug
Control
```

---

# Structured AI Workflow

Use a structured workflow when multiple steps are required.

Example:

```text
Retrieve release data
→ analyze changes
→ identify risks
→ produce findings
→ validate schema
```

This is useful when the sequence is predictable.

---

# Agent

Use an agent when the task requires:

```text
Dynamic planning
Tool selection
Iterative investigation
Multiple possible paths
Adaptive reasoning
```

Example:

```text
Debugging Agent
```

may:

```text
Inspect error
→ inspect changed files
→ inspect dependency changes
→ inspect relevant logs
→ propose reproduction
```

Only use this complexity when it provides real value.

---

# Multi-Agent System

Use multiple agents only when responsibilities are genuinely different.

Example:

```text
QA Agent
Security Auditor
Privacy Auditor
Metadata Agent
Release Auditor
```

Each should have:

```text
Clear responsibility
Clear inputs
Clear outputs
Clear permissions
Clear escalation path
```

Do not create multiple agents that perform the same reasoning.

---

# When RAG Is Appropriate

RAG is useful when:

```text
The source corpus is large
+
The information matters
+
The source changes
+
Grounding matters
+
The relevant context cannot reliably be supplied directly
```

Examples:

```text
Framework documentation
Repository documentation
Internal release procedures
Platform requirements
Historical release decisions
```

Use the simplest retrieval mechanism that meets the requirement.

---

# When Memory Is Appropriate

Memory is useful when future workflows genuinely depend on information from previous interactions.

Examples:

```text
Previous release decisions
Known project-specific constraints
Recurring repository conventions
Historical debugging findings
```

Start with:

```text
Current session/context
```

before adding long-term memory.

Long-term memory should have:

```text
Ownership
Expiration
Correction
Isolation
Traceability
Observability
```

Do not store everything forever.

---

# When Orchestration Is Appropriate

Orchestration is justified when:

```text
Multiple steps depend on each other
+
Different tools or agents are needed
+
The workflow has measurable value
```

Example:

```text
Release request
→ gather context
→ run checks
→ analyze findings
→ aggregate decisions
→ request approval
→ execute
→ verify
```

Do not add orchestration merely to make a workflow look autonomous.

---

# When Async AI Workflows Are Appropriate

Use asynchronous execution when:

```text
The task is long-running
The task uses external services
The task does not need an immediate response
The task may require multiple tool calls
```

Examples:

```text
Full repository release audit
Large crash analysis
Release documentation generation
Multi-step store investigation
```

Use bounded:

```text
Retries
Timeouts
Queue depth
Execution time
Cost
```

Do not create infinite agent retries.

---

# AI Decision Framework

Before implementing AI, answer:

| Question | Desired Answer |
|---|---|
| Is the input unstructured? | Yes |
| Is semantic reasoning required? | Yes |
| Is there meaningful ambiguity? | Yes |
| Does AI reduce human effort? | Yes |
| Can the output be evaluated? | Yes |
| Can important claims be grounded? | Yes |
| Is failure recoverable? | Yes |
| Can permissions be constrained? | Yes |
| Can authoritative state remain deterministic? | Yes |
| Is the operational cost justified? | Yes |

The more "No" answers, the weaker the AI case.

---

# AI Fit Matrix

| Task | AI Fit | Recommended Pattern |
|---|---|---|
| Crash analysis | High | AI + logs + verification |
| Store rejection analysis | High | AI + source evidence |
| Release-note drafting | High | AI + verified changes + human review |
| CI failure summarization | High | AI + actual logs |
| Metadata review | High | AI + product/store evidence |
| Privacy impact triage | High | AI + data-flow verification |
| Dependency impact analysis | High | AI + manifests + docs |
| Test-case generation | High | AI + executable validation |
| Documentation analysis | High | Retrieval + AI |
| Release readiness explanation | High | Evidence + AI |
| Version comparison | Low | Deterministic code |
| File existence | Low | Filesystem |
| Test execution | Low | Test runner |
| Build execution | Low | Build system |
| Store status | Low | Store API/dashboard |
| Authorization | None | Server-side policy |
| Secret management | None | Secret management system |
| Database constraints | None | Database |
| Cryptography | None | Established libraries |
| Exact state transition | Low | State machine |
| Deterministic retry | Low | Application logic |
| Scheduling | Low | Scheduler |

---

# AI Workflow Example: Release Audit

A practical release audit can use:

```text
1. Load release context
2. Retrieve actual build/test evidence
3. Inspect relevant repository changes
4. AI analyzes potential release risks
5. AI identifies missing evidence
6. Deterministic rules validate hard gates
7. Findings are structured
8. Critical findings are preserved
9. Human approval is required where policy requires it
10. Execution remains separately controlled
```

The important distinction is:

```text
AI:
"What should we investigate?"

Deterministic system:
"What is actually true?"
```

---

# AI Workflow Example: Crash Investigation

```text
Crash report
    ↓
Collect stack trace
    ↓
Collect release/build identity
    ↓
Inspect recent changes
    ↓
AI proposes likely causes
    ↓
Developer reproduces
    ↓
Fix
    ↓
Regression test
    ↓
Verify
```

AI accelerates investigation.

It does not declare a root cause without evidence.

---

# AI Workflow Example: Store Rejection

```text
Store rejection
    ↓
Retrieve current release context
    ↓
AI classifies rejection
    ↓
Identify affected feature
    ↓
Inspect repository/build
    ↓
Verify against official platform guidance
    ↓
Prepare remediation
    ↓
Human review
    ↓
Resubmit when appropriate
    ↓
Verify store state
```

This keeps AI useful while preserving platform authority.

---

# AI Workflow Example: Release Notes

```text
Git changes
    ↓
Issue/PR context
    ↓
AI summarizes meaningful changes
    ↓
Remove technical noise
    ↓
Draft user-facing notes
    ↓
Verify every claim
    ↓
Human review
    ↓
Publish
```

Never let the model invent features.

---

# AI Output Contract

For important AI workflows, prefer structured output.

Example:

```yaml
finding:
  category:
  severity:
  claim:
  evidence_ids:
  confidence:
  status:
  recommended_action:
```

For release decisions:

```yaml
verdict:
  value:
  blockers:
  unknowns:
  evidence:
  next_actions:
```

This makes AI output easier to:

```text
Validate
Store
Test
Audit
Compare
Display
```

---

# Evidence Requirements

For high-risk claims:

```text
Evidence required
```

Examples:

```text
Build passed
→ CI/build evidence

Tests passed
→ actual test result

Store approved
→ store evidence

Release live
→ external verification

Security passed
→ security evaluation evidence
```

If evidence is missing:

```text
UNKNOWN
```

Do not ask AI to fill the gap.

---

# Human Approval Boundary

AI may help with:

```text
Audits
Analysis
Debugging
Metadata drafting
Release notes
Repository inspection
Repetitive checks
Workflow preparation
```

Humans should remain responsible for important decisions involving:

```text
Production releases
Signing credentials
Secrets
Store submission
Destructive actions
Account changes
Permissions
Irreversible operations
```

Use:

```text
AI assists
+
Human verifies
+
Human approves
```

where appropriate.

---

# Security Requirements

Before deploying an AI workflow, review:

```text
Prompt injection
Indirect prompt injection
Data leakage
Tool abuse
Unauthorized actions
Excessive agency
Sensitive context exposure
RAG poisoning
Credential exposure
Cost abuse
```

Never:

```text
Trust model output blindly
Give unrestricted tool access
Expose production secrets
Allow arbitrary shell access
Treat retrieved documents as trusted instructions
```

AI security must be designed into the workflow, not added after implementation.

---

# Evaluation Requirements

Every important AI workflow should have tests for:

```text
Correctness
Grounding
Hallucination
Missing evidence
Stale evidence
Conflicting evidence
Prompt injection
Tool failures
Permission boundaries
Regression cases
```

For high-risk workflows:

```text
Critical failures = 0
```

should be the target production gate.

---

# Observability Requirements

For production AI workflows record enough information to understand:

```text
Which workflow ran
Which agent ran
Which model was used
Which prompt/version was used
Which context was supplied
Which tools were called
Which evidence was retrieved
Which result was produced
Which decision followed
How long it took
What it cost
Whether it failed
```

Do not log sensitive prompts or secrets unnecessarily.

---

# Cost and Latency

Before using AI in a repeated workflow, estimate:

```text
Input tokens
Output tokens
Tool calls
Retries
Model cost
Execution time
Context size
```

Measure real usage after implementation.

Avoid:

```text
Large context
+
many agents
+
repeated retries
```

when a smaller workflow can produce the same result.

---

# Common Good Uses

The Mobile Release Playbook can use AI effectively for:

```text
Release impact analysis
Crash investigation
Store rejection analysis
Release-note drafting
Metadata review
Dependency impact analysis
Privacy review assistance
Security review assistance
CI failure analysis
Documentation analysis
Test-case generation
Regression-case generation
Issue triage
Release summary generation
```

These are generally strongest when the AI works from verified project evidence.

---

# Common Bad Uses

Avoid using AI for:

```text
Authentication
Authorization
Secret storage
Cryptographic implementation
Exact version comparison
Exact schema validation
Test execution
Build execution
Store state truth
Database integrity
Permission enforcement
Deterministic release gates
Unbounded production control
```

Use normal engineering systems for these.

---

# AI Implementation Ladder

Start here:

```text
Level 0
No AI
```

Use when deterministic software is sufficient.

```text
Level 1
AI-assisted analysis
```

Examples:

```text
Summarization
Drafting
Classification
Explanation
```

```text
Level 2
Structured AI workflow
```

Examples:

```text
Retrieve
→ analyze
→ structure
→ validate
```

```text
Level 3
Tool-calling AI
```

Examples:

```text
Inspect repository
Inspect CI
Inspect store state
```

```text
Level 4
Bounded agent
```

Examples:

```text
Iterative debugging
Release investigation
Complex repository analysis
```

```text
Level 5
Multi-agent workflow
```

Use only when:

```text
Specialization
+
coordination
+
parallelism
```

provide measurable value.

Higher levels require stronger:

```text
Evaluation
Security
Observability
Permissions
Recovery
Cost controls
```

---

# Red-Team Questions

Before approving an AI workflow, ask:

```text
What if the model is wrong?

What if evidence is missing?

What if evidence is stale?

What if two sources conflict?

What if a repository file contains prompt injection?

What if a store message contains malicious instructions?

What if the tool times out?

What if the action succeeded but the response was lost?

What if the model retries?

What if two agents disagree?

What if the workflow loops?

What if context becomes too large?

What if the model gets expensive?

What if a tool has more permission than intended?
```

A production design should have a concrete answer to each relevant question.

---

# Production Readiness

An AI workflow is ready for production when:

```text
[ ] The use case has a clear reason to use AI
[ ] Deterministic alternatives were considered
[ ] Source of truth is defined
[ ] AI output is structured where useful
[ ] Important claims can be grounded
[ ] Unknown states are preserved
[ ] Hallucination tests exist
[ ] Regression tests exist
[ ] Prompt injection is tested
[ ] Tool permissions are bounded
[ ] Human approval boundaries are explicit
[ ] Tool failures are handled
[ ] Retry behavior is bounded
[ ] Idempotency is addressed
[ ] Observability exists
[ ] Cost is measured
[ ] Latency is measured
[ ] Failure recovery exists
[ ] AI can be bypassed safely where required
```

---

# Decision Rule

Use AI when:

```text
The problem requires reasoning
+
the input contains meaningful ambiguity or unstructured information
+
AI provides measurable leverage
+
the result can be evaluated
+
important claims can be grounded
+
failure is controlled
```

Do not use AI when:

```text
A deterministic system already solves the problem better
+
AI adds little value
+
failure cost is high
+
the output cannot be verified
+
the operational burden outweighs the benefit
```

---

# Final Rule

The question is not:

> **"Can we use AI here?"**

The better question is:

> **"Does AI make this release workflow materially better without making it less reliable?"**

Use AI for:

```text
Understanding
+
Analysis
+
Summarization
+
Classification
+
Reasoning
+
Drafting
+
Investigation
+
Recommendations
```

Use deterministic systems for:

```text
Truth
+
Permissions
+
State
+
Integrity
+
Execution
+
Verification
```

The strongest architecture is usually:

```text
Authoritative systems
→ evidence
→ AI reasoning
→ deterministic validation
→ human approval where needed
→ bounded execution
→ external verification
```

Start simple.

Use the smallest AI capability that solves the real problem.

Add retrieval only when grounding needs it.

Add tools only when external information or action is required.

Add agents only when dynamic reasoning justifies them.

Add orchestration only when coordination creates measurable value.

The goal is not maximum AI.

The goal is:

```text
More useful engineering
+
Less repetitive work
+
Better release decisions
+
Faster debugging
+
Stronger evidence
+
Safe execution
```

---

# Related documentation

### Getting started

- `ai/getting-started/README.md`
- `ai/getting-started/when-not-to-use-ai.md`

### Agents

- `ai/agents/README.md`

### Tools

- `ai/tools/claude-code.md`
- `ai/tools/README.md`

### AI security

- `ai/security/README.md`
