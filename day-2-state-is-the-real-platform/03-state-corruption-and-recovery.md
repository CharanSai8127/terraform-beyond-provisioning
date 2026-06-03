# State Corruption and Recovery

## Introduction

Terraform's ability to manage infrastructure safely depends entirely upon the integrity of its state.

Every lifecycle operation performed by Terraform relies on a simple assumption:

```text
State
=
Reality
```

As long as Terraform's state accurately reflects the infrastructure deployed within the cloud provider, Terraform can safely determine:

* What exists
* What has changed
* What should be created
* What should be modified
* What should be destroyed

Problems begin when this assumption becomes false.

```text
State
≠
Reality
```

Terraform starts making decisions based on incorrect information.

This situation is commonly referred to as state corruption.

Contrary to popular belief, state corruption does not necessarily mean a broken JSON file.

Most real-world state corruption incidents occur when Terraform's understanding of infrastructure no longer matches the actual infrastructure deployed in the cloud.

---

## Understanding State Corruption

Many engineers imagine state corruption as:

```text
terraform.tfstate
↓
Invalid JSON
```

While possible, this is relatively uncommon.

A far more common scenario is:

```text
Infrastructure Exists

Terraform Does Not Know
```

or:

```text
Terraform Thinks Resource Exists

Resource Has Been Deleted
```

The state remains technically valid.

Terraform's understanding becomes inaccurate.

This is the form of corruption that causes the majority of Terraform incidents.

---

## Causes of State Corruption

State corruption usually originates from one of several common scenarios.

### Manual State Modifications

Terraform provides commands capable of directly manipulating state.

Examples include:

```bash
terraform state rm
terraform state mv
```

While useful, these commands bypass normal infrastructure operations.

Consider:

```bash
terraform state rm aws_eks_cluster.production
```

The EKS cluster continues running.

However Terraform forgets the cluster exists.

The infrastructure remains:

```text
Running
```

The state records:

```text
Missing
```

Terraform's understanding is now incorrect.

---

### Interrupted Applies

Another common cause is a failed apply operation.

Imagine Terraform plans:

```text
20 Resources
```

Terraform successfully creates:

```text
Resource 1
Resource 2
...
Resource 10
```

Then:

```text
Network Failure

Terminal Closure

CI Pipeline Failure

Power Loss
```

interrupts execution.

Infrastructure may be partially updated.

State may not reflect all completed operations.

The environment enters a partially applied condition.

---

### Backend Failures

Historically, many organizations stored Terraform state locally.

Examples include:

```text
Laptop Failure

Disk Corruption

Accidental Deletion

File Overwrite
```

State becomes unavailable.

Modern remote backends significantly reduce this risk.

However backend failures can still occur through:

```text
Incorrect Permissions

Accidental State Deletion

Misconfigured Backends

Storage Issues
```

The infrastructure may remain healthy while Terraform loses access to its operational memory.

---

### Manual Cloud Modifications

One of the most frequent causes of state inconsistency occurs outside Terraform.

Example:

An engineer logs into AWS and manually deletes:

```text
Security Group

Subnet

Load Balancer
```

Terraform state still records:

```text
Resource Exists
```

Reality says:

```text
Resource Deleted
```

The next Terraform operation discovers a mismatch.

Terraform and reality are no longer aligned.

---

## Orphaned Resources

An orphaned resource is infrastructure that exists but is no longer tracked by Terraform.

Example:

```text
EC2 Instance Exists
```

State contains:

```text
No Record
```

Terraform cannot:

* Update the resource
* Replace the resource
* Destroy the resource

because Terraform no longer knows it owns it.

Orphaned resources frequently appear after:

```text
State Loss

Incorrect State Commands

Failed Imports

Manual State Modification
```

Over time orphaned resources increase operational complexity and cloud costs.

---

## What Happens When State Becomes Inconsistent?

Terraform assumes:

```text
State
=
Reality
```

When that assumption becomes false, Terraform begins making decisions using inaccurate information.

Possible outcomes include:

```text
Duplicate Resources

Unexpected Replacements

Failed Deployments

Failed Destruction Operations

Infrastructure Drift

Service Interruptions
```

The larger the platform, the greater the potential impact.

Terraform's decisions are only as reliable as the state it trusts.

---

## A Realistic Production Incident

Consider a production Kubernetes platform.

Terraform manages:

```text
VPC

EKS Cluster

Node Groups

IAM Roles

Monitoring
```

An engineer accidentally executes:

```bash
terraform state rm aws_eks_cluster.production
```

The cluster remains operational.

Applications continue serving traffic.

However Terraform loses awareness of the cluster.

The next plan reports:

```text
Resource Missing
```

Terraform attempts:

```text
Create New EKS Cluster
```

Terraform is not malfunctioning.

Terraform is operating based on the information available within state.

The problem is that state no longer reflects reality.

This is one of the most common examples of state corruption in real-world environments.

---

## State Restoration

State restoration is the preferred recovery approach.

The objective is simple:

```text
Restore A Known Good State
```

This is why mature Terraform platforms enable:

```text
S3 Versioning

State Backups

Version History
```

Consider:

```text
State Version 100

State Version 101

State Version 102
```

Corruption occurs in:

```text
Version 102
```

Recovery becomes:

```text
Restore Version 101
```

Terraform regains a consistent view of infrastructure.

This process is significantly easier than rebuilding state from scratch.

---

## State Reconstruction

State reconstruction becomes necessary when no valid state exists.

Examples include:

```text
State Deleted

State Permanently Corrupted

No Backup Available
```

The infrastructure still exists.

Terraform has lost its memory.

Terraform must rebuild its understanding.

This process typically relies on:

```bash
terraform import
```

Example:

```bash
terraform import aws_vpc.main vpc-123456
```

Terraform rebuilds the mapping between:

```text
Terraform Resource
↓
Cloud Resource
```

This process must be repeated for every resource Terraform should manage.

For large environments, reconstruction can become extremely time consuming.

---

## Recovery Process

When corruption occurs, mature platform teams typically follow a structured process.

### Step 1

Stop all Terraform operations.

```text
No Applies

No Pipeline Runs

No State Changes
```

---

### Step 2

Determine the scope of the problem.

Questions include:

```text
State Missing?

State Corrupted?

Single Resource Affected?

Entire Environment Affected?
```

---

### Step 3

Attempt restoration.

Preferred recovery sources include:

```text
S3 Version History

State Backups

Previous Snapshots
```

---

### Step 4

If restoration is impossible:

```text
Perform State Reconstruction
```

using imports.

---

### Step 5

Validate consistency.

Execute:

```bash
terraform plan
```

Terraform should report:

```text
No Changes
```

indicating:

```text
State
=
Reality
```

once again.

---

## Platform Engineering Perspective

Infrastructure failures are visible.

State failures are often invisible until Terraform performs an operation.

This makes state corruption particularly dangerous.

A failed server can usually be rebuilt.

A failed state file may require reconstructing Terraform's understanding of an entire platform.

This is why mature organizations invest heavily in:

* Remote Backends
* State Versioning
* Backup Strategies
* Recovery Procedures
* Access Controls
* State Governance

State is not simply a file.

State is Terraform's operational memory.

Protecting that memory is one of the most important responsibilities of a platform team.

---

## Key Takeaways

* State corruption usually means Terraform's understanding differs from reality.
* Manual state changes and manual cloud changes are common causes of inconsistency.
* Interrupted applies can leave infrastructure partially updated.
* Orphaned resources exist when infrastructure is no longer tracked by Terraform.
* State restoration is preferred over state reconstruction.
* State reconstruction often requires importing resources back into Terraform.
* Recovery should focus on restoring the relationship between state and reality.
* Mature platform teams treat state protection and recovery as critical operational responsibilities.

In the next file, we will explore provider version mismatches, partial applies, upgrade strategies, and how seemingly harmless provider changes can create unexpected production incidents.

