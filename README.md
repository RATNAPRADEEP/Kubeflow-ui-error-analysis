# Kubeflow UI Error Analysis (Frontend)

## 🚀 Demo

<p align="center">
  <img src="./demo.png" width="700"/>
</p>

This demo shows automatic classification of Kubernetes pod failures (e.g., OOMKilled) with root cause and suggested fixes.

---

## ⚙️ How it works

1. Paste Kubernetes pod logs  
2. Click *Analyze Error*  
3. System detects failure type (e.g., OOMKilled, CrashLoopBackOff)  
4. Extracts root cause  
5. Suggests actionable fix  

---

## 🚀 Run Locally

```bash
npm install
npm start
```⁠


## 🚀 Overview
This project improves the *Kubeflow Pipelines UI* by introducing a structured and visual classification of Kubernetes pod lifecycle failures.

It helps users quickly understand *why pipeline runs fail* without manually inspecting logs.

A working prototype is implemented to classify Kubernetes pod failures.

### Example:

Input(example logs):
- Container terminated with exit code 137 
- Reason: OOMKilled

Output:
- Type: ResourceLimitError  
- Root Cause: Container killed due to low memory  
- Suggested Fix: Increase memory limits in Kubernetes pod spec
---

## 🎯 Problem Statement

Debugging failed Kubeflow pipeline runs currently requires:

* Manual log inspection
* Understanding Kubernetes failure states
* Tracing distributed system errors

This process is time-consuming and reduces developer productivity.

---

## 💡 Proposed Solution

A **frontend-only classification system** that:

* Uses existing Kubeflow APIs (no backend changes)
* Analyzes pod status and logs (last 200–500 lines)
* Classifies failures into meaningful categories
* Displays results clearly in the UI

---

## 🧠 Initial Scope

Focus on the most common Kubernetes failures:

* **OOMKilled** – Memory limit exceeded
* **ImagePullBackOff** – Image pull failure
* **CrashLoopBackOff** – Repeated container crashes

These represent a large portion of real pipeline failures.

---

## 🔧 Approach

### Data Collection

* Fetch pod status from Kubeflow APIs
* Fetch logs asynchronously (non-blocking UI)

### Classification Engine (Frontend)

Based on:

* Kubernetes reason
* Exit codes
* Log pattern matching (regex)

Each classifier includes:

* Trigger rules
* Confidence scoring
* Suggested fix

### UI Integration

* Display inside **Run Details page**
* Show:

  * Failure type
  * Confidence
  * Suggested remediation

---

## 📊 Example Classification

| Failure Type     | Cause                 | Suggested Fix               |
| ---------------- | --------------------- | --------------------------- |
| OOMKilled        | Memory limit exceeded | Increase memory limits      |
| ImagePullBackOff | Image pull failure    | Check image/registry access |
| CrashLoopBackOff | Runtime crash         | Inspect logs                |

---

## 🧩 Architecture

Kubeflow API → Pod Data → Log Fetcher → Rule Engine → UI Display

---

## ⚡ Key Features

* Frontend-only implementation
* No backend changes required
* Extensible rule-based system
* Non-blocking log analysis
* UI visualization of classified failures

---

## 🔮 Future Work

* Configurable timeout detection for stalled pods
* Visualization of provisioning failures (e.g., Unschedulable)
* Additional classifiers via rule registry

---

## 📚 Background Study (Kubeflow Internals)

### 1. System Overview

Kubeflow Pipelines (KFP) enables defining and orchestrating ML workflows on Kubernetes using:

* Python SDK (pipeline authoring)
* Go API Server (control plane)
* Driver & Launcher (execution orchestration)
* Argo Workflows (workflow engine)
* MySQL (state persistence)
* ML Metadata (artifact tracking)
* React Frontend (monitoring UI)

### Detailed Analysis Documents

- [Driver Workflow Submission](deep-dives/driver_workflow_submission.md)
- [Run Server Request Flow](deep-dives/run_server_request_flow.md)
---

### 2. End-to-End Execution Flow

1. User defines pipeline in Python
2. Compiler generates pipeline spec (YAML)
3. API server validates and stores metadata
4. Argo executes workflow
5. Driver manages pod execution
6. Persistence agent updates state

---

### 3. Backend Components Study

#### API Server

* Handles CreateRun, GetRun
* Performs RBAC validation
* Stores metadata

#### Driver

* Patches pod specs
* Applies configuration
* Submits workflows

#### Launcher

* Downloads input artifacts
* Executes components
* Uploads outputs

#### Persistence Agent

* Polls workflow state
* Updates MySQL
* Handles consistency

---

### 4. Observed Risk Areas

* Protobuf schema drift
* State synchronization issues
* Failure visibility gaps

---

### 5. Debugging Challenges Identified

* Deep log inspection required
* Poor error summarization in UI
* Distributed failure tracing complexity
* Cross-layer error ambiguity

---

### 6. Areas of Potential Contribution

* Structured error classification
* Intelligent error summary UI
* Improved failure visibility
* Observability enhancements

---

### 7. Next Study Areas

* Driver pod patch logic
* RunServer request lifecycle
* Frontend artifact resolution
* MLMD event traversal

---

## 🛠 Tech Stack

* React
* TypeScript
* Kubeflow Pipelines UI
* Kubernetes

---

## 📌 Status

🚧 Initial implementation phase

---

## 🤝 Contribution Goal

Improve:

* Debugging experience
* Observability
* Developer productivity in Kubeflow Pipelines

---

## 👤 Author

GitHub: https://github.com/RATNAPRADEEP
