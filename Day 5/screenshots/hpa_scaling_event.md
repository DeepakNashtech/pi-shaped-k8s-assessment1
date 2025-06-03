# Simulated HPA Scaling Event

This document simulates the output of Kubernetes commands showing the Horizontal Pod Autoscaler (HPA) scaling the backend deployment based on CPU utilization.

## Initial State (Low Load)

```bash
$ kubectl get hpa
NAME          REFERENCE                     TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
backend-hpa   Deployment/backend-deployment   10%/50%   1         5         1          5m

$ kubectl top pods
NAME                                CPU(cores)   MEMORY(bytes)
backend-deployment-xxxx-yyyy        25m          150Mi
frontend-deployment-zzzz-aaaa       15m          80Mi
```

**Observation:** The backend deployment is running with 1 replica, and the CPU utilization is low (10%), well below the target of 50%.

## Increased Load

*(Simulating a traffic surge causing increased CPU load on the backend pods)*

## Scaling Event

```bash
$ kubectl get hpa
NAME          REFERENCE                     TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
backend-hpa   Deployment/backend-deployment   75%/50%   1         5         3          10m

$ kubectl top pods
NAME                                CPU(cores)   MEMORY(bytes)
backend-deployment-xxxx-yyyy        110m         160Mi
backend-deployment-xxxx-zzzz        105m         155Mi
backend-deployment-xxxx-aaaa        108m         158Mi
frontend-deployment-zzzz-aaaa       18m          82Mi
```

**Observation:** The CPU utilization has increased to 75%, exceeding the target of 50%. The HPA has automatically scaled the backend deployment up to 3 replicas to handle the increased load.

## Load Decreases

*(Simulating the traffic surge subsiding)*

## Scaling Down

```bash
$ kubectl get hpa
NAME          REFERENCE                     TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
backend-hpa   Deployment/backend-deployment   20%/50%   1         5         1          15m

$ kubectl top pods
NAME                                CPU(cores)   MEMORY(bytes)
backend-deployment-xxxx-bbbb        30m          152Mi
frontend-deployment-zzzz-aaaa       16m          81Mi
```

**Observation:** The CPU utilization has dropped back to 20%. After a stabilization period, the HPA scales the backend deployment back down to the minimum replica count (1) to conserve resources.
