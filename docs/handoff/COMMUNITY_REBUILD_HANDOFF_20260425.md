# Community Rebuild Handoff 20260425

This handoff document extracts the highest-value lessons from the previous
implementation and live-validation rounds.

It should be treated as rebuild guidance, not as a request to preserve the old
codebase wholesale.

## What The Rebuild Should Keep

1. the group is the smallest collaboration unit
2. one group corresponds to one collaboration objective
3. the group charter must be immutable after creation
4. workflows must be mounted in the group protocol
5. workflow behavior must be composed from reusable action modules
6. visible message body content is part of the protocol surface, not a cosmetic layer
7. live validation must verify content truth, not just stage advancement

## What Went Wrong Repeatedly

### 1. Stage Passability Was Mistaken For Workflow Correctness

Multiple runs reached later stages, but the actual body content was generic,
meta, or deterministic scaffolding.

The repeated failure pattern was:

- a stage advanced
- formal close signals existed
- the visible body did not contain a real deliverable
- downstream agents therefore consumed placeholders or nonsense

Rebuild rule:

- every workflow validation must inspect visible body content and artifact
  previews, not only gate satisfaction

### 2. Deterministic Artifact Fallback Polluted Business Stages

Several business-stage outputs were fabricated by deterministic fallback code.

Observed examples included:

- fake `candidate_material_pool`
- fake `product_draft`
- fake `publish_decision`
- fake `product_evaluation_report`
- fake `retrospective_plan`

Rebuild rule:

- deterministic fallbacks may exist only for explicit test modes
- business-stage artifact production must fail honestly when no real artifact is
  available

### 3. Bystander Misfire Was A Protocol-Level Bug

Repeated bystander replies were not caused by any single workflow stage.

The actual bug was that optional visible collaboration traffic reached model
execution without a protocol-level ownership gate. Prompt wording alone was not
enough to suppress non-owned turns.

The fix direction was correct:

- put ownership suppression between runtime judgment and model execution
- do not try to solve bystander misfire inside individual action modules

Rebuild rule:

- keep ownership enforcement as a protocol execution guard

### 4. Group Creation Had A Bootstrap Contamination Race

The old live harness created the group first, let peers join, and patched the
protocol afterwards.

That allowed the server's default bootstrap session to contaminate the new
group before the target protocol was bound.

Rebuild rule:

- create group without active peers
- bind the target charter and workflow immediately
- only then let roles join and react

### 5. Model Fallback Order Drifted Away From Operator Intent

Explicit test APIs were meant to be tried first, with broader truth-source
models only as later fallback.

Earlier candidate sorting drifted away from that rule and later success-cache
behavior could reorder candidates in unintended ways.

Rebuild rule:

- preserve a stable priority chain
- explicit operator-provided test APIs must stay ahead of truth-source expansion
- suppression and cooldown logic must not accidentally reactivate the whole pool

## What The Rebuild Should Not Preserve

Do not preserve these patterns:

- workflow-specific prompt patches standing in for protocol semantics
- payload-only closes with no visible body deliverable
- deterministic business artifacts in live workflow modes
- hidden peer choreography instead of action contracts
- stage success metrics that ignore message content quality
- hardcoded silence rules when authority and stage ownership can express the same boundary

## Recommended Rebuild Order

1. define immutable charter structure
2. define action-module contracts
3. define runtime ownership and suppression rules
4. define artifact visibility requirements
5. define stage contracts
6. compose workflows on top
7. add layered tests
8. run live validation that checks real content

## Testing Requirements For The Rebuild

Testing should be layered:

1. charter tests
2. action unit tests
3. producer-consumer pair tests
4. stage loop tests
5. workflow composition tests
6. live tests that validate visible deliverables

Live tests must answer all of these:

- did the correct role respond
- did non-owned roles stay suppressed
- was the required context mounted
- was the action interpreted correctly
- did the body contain the real deliverable
- could the downstream consumer read and use that body content
- did the stage advance for the right reason rather than because of fake scaffolding

## Minimal Operational Lessons

- check actual group messages, not only session snapshots
- inspect both `text` and artifact payload shape
- treat generic managerial summaries as suspicious by default
- treat repeated duplicate closes as a state-machine or suppression bug
- treat bystander chatter as a protocol ownership bug first
- treat workflow runs that only pass gates as incomplete evidence

## Source Of These Lessons

These lessons were extracted from:

- the prior community skill implementation and live validation work
- workflow protocol and action-module design iterations
- live probes for bystander misfire suppression
- repeated failures where business stages advanced with generic or deterministic content

The old implementation remains a useful negative reference, but not a safe
foundation to continue extending directly.
