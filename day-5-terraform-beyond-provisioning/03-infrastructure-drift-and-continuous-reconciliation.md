# Infrastructure Drift and Continuous Reconciliation

## Introduction

Infrastructure as Code is built on a simple principle:

> The infrastructure running in the cloud should match the infrastructure defined in code.

Terraform allows engineers to declare the desired state of infrastructure using configuration files.

Examples include:

* Networks
* Databases
* Kubernetes Clusters
* Storage
* Security Resources

Terraform then provisions infrastructure to match that desired state.

However, production environments are constantly changing.

Manual modifications, emergency fixes, operational workarounds, and external automation can cause the actual infrastructure to diverge from the infrastructure defined in code.

This divergence is known as infrastructure drift.

---

## What Is Infrastructure Drift?

Infrastructure drift occurs when the actual infrastructure no longer matches the desired infrastructure defined in Terraform.

Terraform represents:

```text
Desired State
```

Cloud resources represent:

```text
Actual State
```

When they diverge:

```text
Desired State
        ≠
Actual State
```

drift has occurred.

Infrastructure as Code relies on both states remaining aligned.

When alignment is lost, operational risks begin to increase.

---

## Why Drift Happens

Infrastructure drift is usually introduced through changes that occur outside Terraform.

Common examples include:

* Manual console modifications
* Emergency production fixes
* Direct API updates
* External automation tools
* Configuration changes performed by administrators

Example:

Terraform defines:

```text
Instance Type = t3.medium
```

A manual console update changes the instance to:

```text
Instance Type = t3.large
```

The actual infrastructure no longer matches the declared configuration.

Drift has been introduced.

---

## Configuration Drift

Configuration drift is the most common form of infrastructure drift.

Configuration defines how a resource behaves.

Examples include:

* Instance types
* Network rules
* Backup settings
* Security policies
* Storage configurations

When a resource configuration is modified outside Terraform:

```text
Terraform Configuration
        ≠
Resource Configuration
```

configuration drift occurs.

This creates uncertainty because Terraform's understanding of the resource no longer reflects reality.

Future deployments may:

* Revert changes unexpectedly
* Introduce additional modifications
* Trigger resource replacement
* Create operational instability

Configuration drift reduces trust in Infrastructure as Code.

---

## State Drift

Terraform state acts as the bridge between:

```text
Terraform Configuration
        ↓
Terraform State
        ↓
Actual Infrastructure
```

The state file stores:

* Resource identities
* Resource relationships
* Dependency information
* Metadata required for lifecycle management

State drift occurs when Terraform state no longer accurately reflects the actual infrastructure.

Example:

Terraform state contains:

```text
Database Exists
```

Actual infrastructure:

```text
Database Deleted
```

Terraform's understanding of reality becomes incorrect.

This is state drift.

---

### State Drift vs State Corruption

These are different problems.

State drift:

```text
Terraform State
        ≠
Actual Infrastructure
```

State corruption:

```text
State File Damaged
```

or

```text
State File Lost
```

State corruption was discussed earlier in the series.

State drift is primarily an operational challenge caused by infrastructure changes occurring outside Terraform.

---

## Why State Matters

State enables Terraform to manage infrastructure safely throughout its lifecycle.

State tracks:

* Resource ownership
* Dependencies
* Relationships
* Infrastructure identity

This becomes particularly important during refactoring.

Example:

A resource moves between modules.

Without state:

```text
Delete Resource
        ↓
Create Resource
```

Terraform assumes a new resource is required.

With state migration:

```text
Resource Identity Preserved
        ↓
Ownership Updated
```

Terraform understands that the resource already exists.

State allows infrastructure evolution without unnecessary destruction.

---

## The Risks of Drift

Drift introduces several operational challenges.

### Unpredictable Deployments

Terraform plans become difficult to predict because the infrastructure no longer matches expectations.

---

### Security Risks

Manual changes may bypass governance and security controls.

Examples:

* Open firewall rules
* Disabled encryption
* Excessive permissions

---

### Operational Inconsistency

Different environments begin behaving differently.

Example:

```text
Development
        ≠
Staging
        ≠
Production
```

even though they originate from the same Terraform configuration.

---

### Loss of Trust

The most dangerous outcome of drift is loss of confidence in Infrastructure as Code.

Engineers begin questioning whether Terraform accurately represents reality.

---

## Detecting Drift

Drift should be identified as early as possible.

The most common detection mechanism is:

```text
terraform plan
```

Terraform compares:

```text
Configuration
        ↓
State
        ↓
Cloud Infrastructure
```

and identifies differences.

Unexpected changes often indicate drift.

---

Additional drift detection mechanisms include:

* AWS Config
* Azure Policy
* Google Cloud Config Validator
* Platform audits
* Security scans
* Infrastructure reviews

Regular review processes help identify drift before it becomes a production problem.

---

## Correcting Drift Safely

A common mistake is correcting drift directly through the cloud console.

Example:

```text
Drift Detected
        ↓
Manual Fix
```

This often introduces additional drift.

The preferred workflow is:

```text
Drift Detected
        ↓
Investigate Root Cause
        ↓
Update Terraform Code
        ↓
Review Changes
        ↓
Apply Through Terraform
```

Infrastructure should always converge back to the declared state.

Terraform should remain the authoritative source of infrastructure configuration.

---

## Continuous Reconciliation

Modern platform engineering focuses on continuous reconciliation.

The objective is simple:

```text
Desired State
        =
Actual State
```

at all times.

Whenever differences appear, the platform should identify and correct them.

Continuous reconciliation ensures infrastructure remains aligned with organizational standards and operational requirements.

---

## GitOps and Drift Prevention

GitOps provides one of the most effective approaches to managing drift.

Git becomes the single source of truth.

```text
Git Repository
        ↓
Desired State
```

All infrastructure changes originate from Git.

The workflow becomes:

```text
Git Commit
        ↓
Pull Request
        ↓
Review
        ↓
Approval
        ↓
Terraform Apply
```

Direct infrastructure modifications are discouraged.

---

### GitOps Principles

#### Infrastructure Changes Start in Git

Changes are made through code rather than through cloud consoles.

---

#### Review Before Deployment

Every infrastructure modification is reviewed before reaching production.

---

#### Continuous Convergence

Infrastructure continuously moves toward the state defined in Git.

```text
Git
        ↓
Desired State
        ↓
Actual State
```

The goal is to eliminate divergence.

---

#### Auditability

Every infrastructure change becomes traceable.

Organizations can identify:

* Who changed it
* Why it changed
* When it changed

This improves accountability and governance.

---

## Platform Engineering Perspective

Infrastructure drift is one of the biggest threats to reliable Infrastructure as Code.

The goal is not to eliminate all drift completely.

The goal is to:

* Detect drift quickly
* Understand drift
* Correct drift safely
* Prevent unnecessary drift

Successful platforms maintain alignment between:

```text
Terraform Configuration

Terraform State

Actual Infrastructure
```

When these remain synchronized, Infrastructure as Code remains trustworthy.

---

## Key Takeaway

Infrastructure drift occurs when the actual infrastructure no longer matches the desired infrastructure defined in Terraform.

Drift can arise from manual changes, emergency fixes, external automation, or operational workarounds.

Organizations detect drift through planning, audits, and governance processes, and correct drift by reconciling infrastructure back to the declared state.

Continuous reconciliation and GitOps practices ensure infrastructure remains aligned with code, allowing Infrastructure as Code to remain a reliable source of truth.

