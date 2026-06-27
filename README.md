# Exercise 11 - CrashLoopBackOff Investigation

## Overview

This exercise demonstrates how to troubleshoot a Kubernetes pod that is continuously restarting due to a **CrashLoopBackOff** error. The objective is to identify the root cause by analyzing pod status, logs, events, services, and secrets.

---

## Scenario

The `payment-service` application failed to start successfully and Kubernetes reported the following status:

```text
CrashLoopBackOff
```

Application logs showed:

```text
panic:
dial tcp 10.20.0.15:5432: connection refused
```

---

## Objective

* Investigate the reason for the pod failure.
* Determine whether the issue is related to:

  * DNS
  * Database
  * Kubernetes Secrets
* Identify the root cause and explain the findings.

---

## Tools Used

* Kubernetes
* Minikube
* kubectl
* Git
* GitHub

---

## Investigation Steps

### 1. Check Pod Status

```bash
kubectl get pods
```

**Observation**

The `payment-service` pod was repeatedly restarting and entered the **CrashLoopBackOff** state.

---

### 2. Check Application Logs

```bash
kubectl logs <pod-name>
```

**Output**

```text
panic:
dial tcp 10.20.0.15:5432: connection refused
```

**Observation**

The application failed while attempting to connect to the PostgreSQL database.

---

### 3. Describe the Pod

```bash
kubectl describe pod <pod-name>
```

**Observation**

The Events section displayed:

```text
Back-off restarting failed container
```

This confirmed that Kubernetes was continuously restarting the failed container.

---

### 4. Verify Kubernetes Services

```bash
kubectl get svc
```

**Observation**

Only the default Kubernetes service was available. No PostgreSQL service was running.

---

### 5. Verify Kubernetes Secrets

```bash
kubectl get secrets
```

**Observation**

No database-related secret was found, and there were no authentication errors in the application logs.

---

## Findings

| Investigation  | Result                      |
| -------------- | --------------------------- |
| Pod Status     | CrashLoopBackOff            |
| Logs           | Database connection refused |
| DNS Issue      | No                          |
| Secret Issue   | No                          |
| Database Issue | Yes                         |

---

## Root Cause

The application attempted to connect to a PostgreSQL database on **port 5432**, but the connection was refused because the database service was unavailable or not accepting connections.

Since the application exited with an error, Kubernetes continuously restarted the container, resulting in the **CrashLoopBackOff** state.

---

## Commands Used

```bash
kubectl get pods

kubectl logs <pod-name>

kubectl describe pod <pod-name>

kubectl get svc

kubectl get secrets
```

---

## Project Structure

```
Exercise-11-CrashLoopBackOff-Investigation/
│
├── payment-deployment.yaml
├── README.md
└── screenshots/
    ├── 01-pod-status.png
    ├── 02-pod-logs.png
    └── 03-pod-events.png
```

---

## Screenshots

* **01-pod-status.png** – Pod status showing CrashLoopBackOff
* **02-pod-logs.png** – Application logs displaying the database connection error
* **03-pod-events.png** – Pod events showing `Back-off restarting failed container`

---

## Key Learnings

* Learned how to investigate a **CrashLoopBackOff** issue in Kubernetes.
* Used `kubectl logs` to identify application-level failures.
* Used `kubectl describe pod` to inspect Kubernetes events.
* Verified services and secrets during troubleshooting.
* Distinguished between DNS issues, database issues, and secret-related issues.
* Followed a structured troubleshooting approach to identify the root cause.

---

## Conclusion

The investigation confirmed that the issue was **not related to DNS or Kubernetes Secrets**. The root cause was a **database connectivity failure**, where the application could not establish a connection to the PostgreSQL database on port **5432**. This caused the container to exit repeatedly, leading Kubernetes to place the pod in the **CrashLoopBackOff** state.
