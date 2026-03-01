---
name: Mirror Council
category: multi-agent
purpose: Multi-agent deliberation and consensus building for complex decisions
tags:
  - multi-agent
  - consensus
  - deliberation
  - coordination
  - decision-making
version: 1.0.0
requires:
  - openclaw CLI
  - minimum 2 agents
  - shared context directory
---

# Mirror Council (ES)

## Overview

Mirror Council enables coordinated deliberation among multiple OpenClaw agents. Agents represent different perspectives (e.g., DEV, OPS, SECURITY, LEGAL), present arguments, challenge assumptions, and work toward consensus or documented disagreement. Ideal for architectural decisions, risk assessments, and cross-domain reviews.

## Core Concepts

| Concept | Description |
|---------|-------------|
| **Council** | A temporary or persistent group of 2-6 agents convened for deliberation |
| **Perspective** | Each agent represents a distinct viewpoint (technical, operational, security, etc.) |
| **Deliberation Round** | One cycle where all agents present their position |
| **Consensus Threshold** | Configurable agreement level (unanimous, majority, supermajority) |
| **Dissent Record** | Documented disagreement when consensus cannot be reached |
| **Mirror** | The shared context where council proceedings are recorded |

## Commands

### `/council:init <name> [agents...]`
Initialize a new council session.

```bash
/council:init "API Gateway Decision" dev ops security
```

### `/council:propose <proposition>`
Submit a proposal for deliberation.

```bash
/council:propose "Migrate authentication to OAuth2 with JWT refresh tokens"
```

### `/council:deliberate [rounds]`
Run deliberation rounds (default: 2).

```bash
/council:deliberate 3
```

### `/council:vote [threshold]`
Call for vote with threshold (unanimous|majority|supermajority).

```bash
council:vote supermajority
```

### `/council:dissent`
Record formal dissent if consensus fails.

```bash
/council:dissent
```

### `/council:summary`
Generate final council summary.

```bash
/council:summary
```

## Configuration

Create `.openclaw/council.yaml` in your workspace:

```yaml
council:
  default_threshold: supermajority
  max_rounds: 5
  dissent_grace_period: 2
  perspectives:
    - dev
    - ops
    - security
    - legal
  context_dir: .openclaw/council/
```

## Use Cases

### 1. Architectural Decision: Database Selection
**Scenario**: Team must choose between PostgreSQL, MongoDB, or DynamoDB for a new service.

```bash
/council:init "DB Selection" dev ops security
/council:propose "Use PostgreSQL with read replicas for the user service"
```

Each agent contributes:
- **DEV**: Query patterns, ORM support, migrations
- **OPS**: Backup strategy, replication latency, operational overhead
- **SECURITY**: Encryption at rest, access controls, audit logging

### 2. Security Risk Assessment
**Scenario**: Evaluating third-party API integration.

```bash
/council:init "Payment API Integration" dev ops security legal
/council:propose "Integrate Stripe via official SDK with webhook validation"
```

### 3. Infrastructure Change Approval
**Scenario**: Migrating from Docker Compose to Kubernetes.

```bash
/council:init "K8s Migration" dev ops security
/council:propose "Migrate production workload to EKS with managed node groups"
```

## Workflow

1. **Convene**: `/council:init` - Define name and participating agents
2. **Propose**: `/council:propose` - Present the decision or question
3. **Deliberate**: `/council:deliberate` - Agents present perspectives in rounds
4. **Vote**: `/council:vote` - Check consensus
5. **Resolve**: If consensus → proceed; if not → `/council:dissent` and document
6. **Summarize**: `/council:summary` - Generate record

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Agents not responding | Verify all agents are online with `/status` |
| Deadlock in deliberation | Increase `max_rounds` in config or call vote early |
| Consensus impossible | Use `/council:dissent` to document minority view |
| Context lost | Check `.openclaw/council/` for preserved state |

## Example Output

```
=== MIRROR COUNCIL: API Gateway Decision ===

PROPOSAL: Deploy API Gateway with rate limiting and auth

DELIBERATION ROUND 1:
  [DEV]     → Favors Kong for plugin ecosystem
  [OPS]     → Prefers AWS API Gateway for managed ops
  [SECURITY]→ Requires JWT validation at gateway level

DELIBERATION ROUND 2:
  [DEV]     → Can adapt to AWS API Gateway with custom authorizers
  [OPS]     → Accepts Kong if operational burden is documented
  [SECURITY]→ Confirms JWT requirement is met by both options

VOTE: MAJORITY
  [DEV]     → YES (AWS API Gateway)
  [OPS]     → YES (AWS API Gateway)
  [SECURITY]→ YES (AWS API Gateway)

RESULT: CONSENSUS REACHED
ACTION: Proceed with AWS API Gateway

DISSENT: None recorded
SUMMARY: Council recommends AWS API Gateway with JWT authorizers
```