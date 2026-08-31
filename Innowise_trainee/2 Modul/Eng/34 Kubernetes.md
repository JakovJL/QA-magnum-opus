# Kubernetes

## Table of Contents

- [[#What is Kubernetes]]
- [[#Main Objects]]
- [[#Networking and Configuration]]
- [[#Scaling and Recovery]]
- [[#What to Check as a Tester]]
- [[#Common Mistakes]]
- [[#Key Takeaways]]
- [[#Interview Questions]]
- [[#Practical Questions]]

**Related notes:** [[AQA Java eng]]

---

## What is Kubernetes

**Kubernetes** is a container orchestration system. It deploys containers, maintains the desired number of replicas, manages network access, and restarts failed instances.

**Goal:** reliably run and scale applications in a cluster of servers.

Kubernetes is often used alongside Docker, but solves a different problem: Docker creates and runs a container, while Kubernetes manages many containers across an environment.

---

## Main Objects

- **Pod** — the smallest deployable unit; typically contains one application container;
- **Deployment** — describes the desired number of Pods and the update strategy;
- **Service** — provides a stable network address for a group of Pods;
- **Namespace** — logical separation of resources, e.g. `dev`, `test`, `prod`;
- **ConfigMap** — stores non-sensitive configuration;
- **Secret** — stores sensitive data.

```text
Deployment → Pod → application container
Service → directs requests to Pod
```

---

## Networking and Configuration

An application typically addresses another service by `Service` name, not by an individual Pod's IP. A Pod may be recreated and get a different IP.

Configuration and secrets are passed through environment variables or mounted files. Passwords and tokens must not be written into a Docker image, source code, or a plain `ConfigMap`.

---

## Scaling and Recovery

Kubernetes tracks the desired state. If a `Deployment` requires three Pods and one fails, the system tries to create a new one.

Health probes help determine application readiness:

- **readiness probe** — whether the Pod is ready to accept user traffic;
- **liveness probe** — whether the application is stuck and needs a restart.

Incorrect probes can cause a working Pod to be removed from load balancing or restarted repeatedly.

---

## What to Check as a Tester

- the application is accessible through the `Service`;
- a new version deploys without unacceptable downtime;
- incorrect configuration is visible in logs and Pod status;
- the application recovers after a Pod failure;
- readiness does not direct traffic to an unprepared application;
- secrets do not leak into responses, logs, or the repository;
- when scaling, data and scenarios work correctly with multiple instances.

---

## Common Mistakes

| Mistake | Better Approach |
|---|---|
| Treating a Pod as a permanent server | Consider Pods temporary; do not store state only inside a Pod |
| Connecting to a Pod by IP | Use the `Service` name |
| Storing passwords in `ConfigMap` | Use `Secret` and restrict access |
| Not checking readiness and liveness | Test startup, recovery, and application updates |

---

## Key Takeaways

> [!summary] Key points in 30 seconds
> - Kubernetes is a container orchestration system: it deploys containers, keeps the desired number of replicas, manages network access, and restarts failed instances
> - Docker creates and runs one container, while Kubernetes manages many containers across a cluster of servers
> - Main objects: Pod (the smallest deployable unit), Deployment (desired number of Pods and update strategy), Service (stable network address), Namespace (e.g. dev, test, prod), `ConfigMap`, `Secret`
> - Address another service by its `Service` name, not by a Pod IP — a Pod can be recreated and get a different IP
> - Pods are temporary: do not treat a Pod as a permanent server and do not store state only inside it
> - Passwords and tokens must be stored in `Secret`, not in a Docker image, source code, or a plain `ConfigMap`
> - A readiness probe decides if a Pod is ready to accept traffic, a liveness probe decides if the application is stuck and needs a restart — incorrect probes remove working Pods from load balancing or restart them repeatedly
> - Most often asked: Pod vs Deployment vs Service, readiness vs liveness probes, ConfigMap vs Secret, why not connect to a Pod by IP

---

## Interview Questions

### Beginner Questions

**1. What is Kubernetes?**

Kubernetes is a container orchestration system. It deploys containers, maintains the desired number of replicas, manages network access, and restarts failed instances.

**2. What is a Pod in Kubernetes?**

The smallest deployable unit, typically containing one application container.

**3. What is the difference between a Deployment and a Service?**

A Deployment describes the desired number of Pods and the update strategy. A Service provides a stable network address for a group of Pods.

**4. What are ConfigMap and Secret used for?**

ConfigMap stores non-sensitive configuration. Secret stores sensitive data (passwords, tokens).

### Intermediate Questions

**1. What is the difference between a readiness probe and a liveness probe?**

A readiness probe checks whether the Pod is ready to accept traffic. A liveness probe checks whether the application is still running and needs to be restarted.

**2. Why should you avoid connecting to a Pod by IP?**

A Pod can be recreated and get a different IP. The Service name should be used instead.

**3. What should a tester check when working with Kubernetes?**

Whether the application is accessible through the Service; whether a new version deploys without unacceptable downtime; whether incorrect configuration is visible in logs and Pod status; whether the application recovers after a Pod failure; whether secrets do not leak into responses or logs.

### Advanced Questions

**1. Why should a Pod not be treated as a permanent server?**

A Pod is temporary: it can be recreated on failure, update, or scaling. State should not be stored only inside a Pod.

**2. What problems can incorrect readiness and liveness probes cause?**

A working Pod may be removed from load balancing (readiness) or restarted repeatedly (liveness), causing failures and application instability.

**3. Why is it important to test a new version deployment?**

To verify that the update happens without unacceptable downtime (rolling update) and that incorrect configuration is detected during deployment, not after.

### Code Questions

**1. What does this diagram mean?**

```text
Deployment → Pod → application container
Service → directs requests to Pod
```

**Answer:** The Deployment manages Pod creation, each Pod contains an application container. The Service provides a stable address to access the Pods.

**2. What mistake is made in this configuration?**

Storing a password in ConfigMap instead of Secret.

**Answer:** Passwords and tokens are sensitive data — they should be stored in Secret, not ConfigMap. Secret is encrypted and supports stricter access control.

### Practical Questions

**1. How do you check if the application is accessible through a Service?**

Use `kubectl port-forward` to access the service locally, then send an HTTP request:

```bash
kubectl port-forward service/my-app 8080:80
curl http://localhost:8080/health
```

**2. How do you verify a Pod is running and healthy?**

```bash
kubectl get pods
kubectl describe pod my-app-xxx
```

Look at the `STATUS` column and the `Conditions` section in the describe output.

**3. How do you test that readiness probe works?**

Deploy a version that is slow to start. Check that the Pod stays in `NotReady` state until the probe succeeds and that no traffic reaches it before that.

**4. How do you test application recovery after a Pod crash?**

Delete a Pod manually:

```bash
kubectl delete pod my-app-xxx
```

Then verify that a new Pod starts automatically and the application works within the expected time.

**5. How do you verify that secrets are not exposed in logs?**

Store a known value in a Secret, deploy the app, and check that the value does not appear in `kubectl logs <pod>`.

**6. How do you test rolling update with zero downtime?**

Update the Deployment image: `kubectl set image deployment/my-app my-app=my-app:v2`. While it rolls out, continuously send requests and assert zero `502` or connection errors.

**7. How do you check that configuration from ConfigMap is applied correctly?**

Create a ConfigMap, mount it in the Pod, exec into the Pod and read the file:

```bash
kubectl exec my-app-pod -- cat /etc/config/app.properties
```

Assert the value matches what you set.

**8. How do you test that multiple instances work correctly?**

Scale the Deployment: `kubectl scale deployment my-app --replicas=3`. Then verify that all instances respond and that data consistency is maintained (e.g., all instances see the same state).

**9. How do you check Pod logs and status?**

```bash
kubectl logs -f my-app-pod          # stream logs
kubectl logs --previous my-app-pod  # logs from previous (crashed) instance
kubectl describe pod my-app-pod     # detailed status and events
```

**10. How do you verify namespace isolation?**

Deploy the same application name in two namespaces, create a Service in each, and verify that the Service in one namespace cannot reach the Pod in the other.
