# Exercise 11 - CrashLoopBackOff Investigation

## Objective

Investigate why the payment-service pod enters the CrashLoopBackOff state.

## Incident

Pod Status

```
CrashLoopBackOff
```

Logs

```
panic:
dial tcp 10.20.0.15:5432: connection refused
```

## Investigation

- Checked pod status using `kubectl get pods`
- Viewed logs using `kubectl logs`
- Examined events using `kubectl describe pod`
- Verified Kubernetes services
- Checked Kubernetes secrets

## Findings

- DNS Issue: No
- Secret Issue: No
- Database Issue: Yes

## Root Cause

The application failed to connect to the PostgreSQL database because the database was unavailable or not accepting connections on port 5432. Kubernetes repeatedly restarted the container, resulting in the CrashLoopBackOff state.

## Commands Used

```bash
kubectl get pods
kubectl logs <pod-name>
kubectl describe pod <pod-name>
kubectl get svc
kubectl get secrets
```
