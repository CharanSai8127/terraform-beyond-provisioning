# Provider Versions and Partial Applies

## Introduction

Terraform itself does not know how to create infrastructure.

Terraform understands infrastructure definitions written in HashiCorp Configuration Language (HCL), but it does not know how to communicate directly with cloud providers.

When a Terraform configuration defines:

```text
VPC
EKS Cluster
Database
Load Balancer
```

Terraform requires a mechanism to translate those declarations into actual cloud provider API calls.

This responsibility belongs to providers.

Providers act as the bridge between Terraform and the infrastructure platform.

As organizations scale Terraform usage, provider management becomes increasingly important because provider behavior directly influences how Terraform creates, modifies, and destroys infrastructure.

Many production incidents are not caused by Terraform itself.

They are caused by provider changes, failed applies, and inconsistencies introduced during infrastructure operations.

---

## Understanding Terraform Providers

Terraform follows a simple architecture.

```text
Terraform Configuration
          ↓
Provider
          ↓
Cloud Provider API
          ↓
Infrastructure
```

Consider a simple resource declaration:

```text
resource "aws_vpc" "main"
```

Terraform understands:

```text
Create A VPC
```

The AWS provider understands:

```text
Which AWS API
Must Be Called
```

The provider converts Terraform resources into API requests that cloud providers can understand.

Without providers:

```text
Terraform
↓
Cannot Create Infrastructure
```

Terraform defines intent.

Providers perform implementation.

---

## Providers as Infrastructure Translators

Every cloud provider exposes infrastructure through APIs.

Examples include:

```text
AWS APIs

Azure APIs

Google Cloud APIs
```

Terraform does not interact with these APIs directly.

Instead:

```text
Terraform
↓
Provider
↓
Cloud API
```

The provider maps Terraform resources and attributes into the appropriate API calls.

This abstraction allows engineers to work with infrastructure declaratively while providers handle platform-specific implementation details.

---

## Why Provider Versions Matter

Providers continuously evolve.

Cloud providers regularly release:

```text
New Features

New Resource Types

New Attributes

New API Versions
```

Terraform providers evolve alongside them.

As a result:

```text
Provider Version A
```

may behave differently from:

```text
Provider Version B
```

even when the Terraform code remains unchanged.

This introduces operational risk.

A provider upgrade may:

```text
Add New Attributes

Remove Existing Attributes

Modify Defaults

Deprecate Features

Change Resource Behavior
```

The same Terraform code can therefore produce different outcomes depending on the provider version being used.

---

## Real-World Example

Imagine an environment originally built using:

```text
AWS Provider 5.x
```

Months later, an engineer executes:

```bash
terraform init -upgrade
```

and upgrades to:

```text
AWS Provider 6.x
```

The Terraform configuration remains unchanged.

However:

```text
Resource Defaults Change

Provider Behavior Changes

Deprecated Attributes Appear
```

The next:

```bash
terraform plan
```

may suddenly report unexpected modifications.

The infrastructure has not changed.

The provider interpretation has changed.

This is why mature organizations treat provider versions as part of the platform contract.

---

## Version Pinning

To ensure predictable behavior, platform teams commonly pin provider versions.

Example:

```text
Terraform Version
↓
1.12.x

AWS Provider
↓
5.95.x
```

Version pinning provides:

```text
Consistency

Predictability

Repeatability
```

across:

```text
Development

Staging

Production
```

Without version pinning:

```text
Different Engineers
↓
Different Provider Versions
↓
Different Results
```

This creates unnecessary operational risk.

---

## Safe Provider Upgrade Strategy

Provider upgrades should be treated similarly to application upgrades.

A mature upgrade process typically follows:

```text
Development
        ↓
Validation
        ↓
Staging
        ↓
Validation
        ↓
Production
```

Each environment performs:

```bash
terraform plan
```

to identify unexpected changes before deployment.

The objective is not simply obtaining the latest provider.

The objective is ensuring infrastructure behavior remains predictable.

---

## Understanding Failed Applies

Terraform operations do not always complete successfully.

Infrastructure creation may fail because of:

```text
Network Interruptions

API Failures

Permission Issues

Rate Limiting

Provider Bugs

Infrastructure Constraints
```

When Terraform fails during an apply operation, infrastructure may be left in a partially updated state.

This situation is known as a failed apply.

---

## What Are Partial Applies?

A partial apply occurs when Terraform successfully completes some operations but fails before completing all planned changes.

Example:

Terraform plans:

```text
20 Resources
```

Terraform successfully creates:

```text
Resource 1
Resource 2
Resource 3
...
Resource 10
```

Then:

```text
Network Failure
```

interrupts execution.

The result becomes:

```text
Infrastructure
↓
Partially Updated

State
↓
May Not Fully Reflect Reality
```

This is one of the most common operational incidents encountered by Terraform users.

---

## Why Partial Applies Are Dangerous

The primary risk is not the failed deployment itself.

The primary risk is the possibility that:

```text
Reality
≠
State
```

For example:

Terraform successfully creates:

```text
EKS Cluster
```

AWS confirms success.

Before state can be updated:

```text
Terraform Process Crashes
```

Now:

```text
AWS
↓
Cluster Exists

State
↓
Cluster Missing
```

Terraform's understanding no longer matches reality.

Future operations may behave unexpectedly until consistency is restored.

---

## Recovering From Failed Applies

The first step is understanding the current situation.

Platform teams typically begin with:

```bash
terraform plan
```

Terraform performs a refresh operation and compares:

```text
Configuration

State

Infrastructure
```

to determine the current status.

In many situations, recovery is straightforward.

Simply executing:

```bash
terraform apply
```

again allows Terraform to reconcile incomplete operations.

Terraform identifies:

```text
What Exists

What Is Missing

What Still Needs To Be Created
```

and continues from the current state.

---

## When Recovery Becomes Difficult

Recovery becomes more complex when:

```text
Infrastructure Updated

State Not Updated
```

Terraform may believe resources are missing even though they already exist.

In these situations, recovery may require:

```bash
terraform import
```

to rebuild resource mappings inside state.

Alternatively, organizations may restore a previous state version if corruption is detected early.

---

## Platform Engineering Perspective

Failed applies are often viewed as deployment failures.

Platform teams view them differently.

A failed apply is usually a state consistency problem.

The infrastructure may already exist.

The challenge becomes restoring Terraform's understanding of that infrastructure.

Similarly, provider versions are not merely software dependencies.

They define how Terraform interprets infrastructure.

As platforms scale, provider management becomes part of platform governance.

Mature organizations therefore manage:

```text
Terraform Versions

Provider Versions

State Versions
```

with the same level of discipline applied to production software.

---

## Key Takeaways

* Providers translate Terraform configurations into cloud provider API calls.
* Terraform cannot provision infrastructure without providers.
* Provider behavior changes over time through version upgrades.
* Provider versions should be pinned and upgraded deliberately.
* Partial applies occur when Terraform completes only part of a deployment.
* Failed applies frequently become state consistency problems.
* Terraform can often recover through reconciliation during subsequent applies.
* Recovery becomes more difficult when infrastructure and state diverge.
* Mature platform teams treat provider management as a critical operational responsibility.

In the next file, we will explore state governance, ownership, auditing, access control, backup strategies, and why Terraform state should be treated as production data rather than a simple configuration artifact.

