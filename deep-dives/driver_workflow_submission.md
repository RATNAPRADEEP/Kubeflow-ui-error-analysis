# Driver Component – Workflow Submission Deep Dive

## Role of Driver

The Driver component is responsible for converting a validated
PipelineSpec into a Kubernetes Workflow Custom Resource (CR)
and submitting it to the cluster.

It acts as the bridge between:

API Server → Kubernetes (Argo Workflows)

---

## High-Level Responsibility

1. Patch PodSpecs with:
   - Image pull secrets
   - Service accounts
   - Resource limits
2. Apply default configurations
3. Inject metadata:
   - Run ID
   - Experiment ID
   - Labels for tracking
4. Submit Argo Workflow CR to Kubernetes API

---

## Execution Path (Simplified)

CreateRun()
  → ResourceManager.CreateRun()
    → Driver.CreateWorkflow()
      → WorkflowClient.Create()
        → Kubernetes API Server
          → etcd
          → Argo controller picks up CR

---

## Critical Observations

- Driver performs pod patching before submission
- Any failure here prevents workflow creation
- Metadata consistency between DB and CR is essential
- Labels enable UI and backend reconciliation

---

## Failure Windows

1. DB write succeeds
2. Driver fails before CR submission
   → Orphaned DB run record

3. CR submitted but:
   - Kubernetes API timeout
   - Admission webhook failure
   - Argo controller delay

These create distributed state drift scenarios.

---

## Contribution Opportunity

Potential improvements:

- Transactional guard between DB write and CR submission
- Retry mechanism with idempotent workflow creation
- Improved error surfacing back to API layer
- Structured failure classification (Driver-level errors)
