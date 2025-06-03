# Day 4: 2-Tier Application Deployment and Optimization

This repository contains Kubernetes manifests for deploying a 2-tier application (frontend and backend) with a focus on performance tuning and cost optimization strategies.

## Application Architecture

The application consists of two tiers:

1. **Frontend**: A simple Nginx deployment serving static content (placeholder).
2. **Backend**: An Apache HTTP Server deployment representing a backend API or service (placeholder).

## Manifest Files

- `frontend.yaml`: Contains the Deployment and Service for the frontend tier.
- `backend.yaml`: Contains the Deployment and Service for the backend tier.
- `hpa.yaml`: Contains the Horizontal Pod Autoscaler configuration for the backend tier.

## Optimization Strategies Implemented

Several strategies have been implemented in the manifests to optimize for performance, reliability, and cost:

1.  **Resource Requests and Limits (`frontend.yaml`, `backend.yaml`)**: Both frontend and backend deployments have specific CPU and memory requests and limits defined. 
    -   **Requests** guarantee a minimum amount of resources for each pod, ensuring predictable performance and proper scheduling.
    -   **Limits** prevent pods from consuming excessive resources, protecting other workloads and the node itself from resource starvation. This contributes to overall cluster stability and can help optimize costs by preventing unnecessary resource usage.
    -   *Comment Example*: `Performance/Cost Consideration: Setting resource requests and limits ensures predictable performance and prevents resource hogging.`

2.  **Liveness and Readiness Probes (`frontend.yaml`, `backend.yaml`)**: Both deployments utilize HTTP GET probes:
    -   **Readiness Probes** ensure that traffic is only routed to pods that are ready to serve requests. This prevents users from hitting unresponsive pods during startup or temporary issues, improving user experience.
    -   **Liveness Probes** check if the application within the pod is still running correctly. If a liveness probe fails repeatedly, Kubernetes automatically restarts the container, helping to recover from deadlocks or unresponsive states.
    -   *Comment Example*: `Performance/Reliability Consideration: Probes ensure traffic is only sent to healthy pods and unhealthy pods are restarted.`

3.  **Horizontal Pod Autoscaler (HPA) (`hpa.yaml`)**: An HPA is configured for the backend deployment to automatically scale the number of replicas based on CPU utilization.
    -   The HPA targets an average CPU utilization of 50% across all backend pods.
    -   It can scale the number of replicas between 1 (minReplicas) and 5 (maxReplicas).
    -   This allows the application to handle varying loads efficiently. During low traffic, it scales down to minimize resource consumption (cost optimization). During high traffic (e.g., flash sales), it scales up automatically to maintain performance and responsiveness.
    -   *Comment Example*: `Performance/Cost Consideration: Scaling based on CPU utilization helps handle load spikes efficiently, ensuring performance while potentially saving costs by not over-provisioning replicas.`

4.  **Service Types (`frontend.yaml`, `backend.yaml`)**: 
    -   The frontend uses a `NodePort` service for easier external access during testing/development.
    -   The backend uses a `ClusterIP` service, making it only accessible from within the cluster. This is a security best practice for internal services, reducing the attack surface.

## Simulated Scaling Event

A simulated scaling event demonstrating the HPA in action can be found in [screenshots/hpa_scaling_event.md](screenshots/hpa_scaling_event.md).

## Core Concept Questions

### Why are liveness and readiness probes critical in keeping a product’s user experience stable and reliable?

Liveness and readiness probes are fundamental Kubernetes features that play a critical role in maintaining application stability and ensuring a reliable user experience. They act as automated health checks, allowing Kubernetes to understand the internal state of application containers and make intelligent decisions about traffic routing and container lifecycle management.

**Readiness Probes** determine if a container is ready to start accepting traffic. Their importance stems from several factors:

1.  **Preventing Traffic to Uninitialized Pods**: Applications often require time to initialize, load configurations, establish database connections, or warm up caches before they can serve requests correctly. Without readiness probes, Kubernetes might route traffic to a pod immediately after its container starts, even if the application inside isn't ready. This leads to errors, timeouts, and a poor user experience. Readiness probes ensure that the Service only includes pods that have explicitly signaled their readiness, guaranteeing that users only interact with fully functional instances.
2.  **Graceful Deployments and Updates**: During rolling updates or deployments, readiness probes ensure that new pods are fully ready before they are added to the service endpoint list and start receiving traffic. This prevents service disruptions during updates, as traffic is seamlessly shifted only to healthy, ready pods.
3.  **Handling Temporary Unavailability**: If an application temporarily cannot serve requests (e.g., waiting for a downstream dependency), the readiness probe can fail. Kubernetes will temporarily remove the pod from the service endpoints, preventing users from experiencing errors. Once the application recovers and the readiness probe passes again, Kubernetes automatically adds the pod back into rotation.

**Liveness Probes** determine if a container is still running correctly. If a liveness probe fails, Kubernetes assumes the container is unhealthy and restarts it. Their criticality lies in:

1.  **Automatic Recovery from Deadlocks**: Applications can sometimes enter states where they are running but unresponsive (e.g., due to deadlocks, infinite loops, or corrupted internal state). Liveness probes detect these situations. When a probe fails repeatedly, Kubernetes intervenes by restarting the container, often resolving the issue automatically without manual intervention.
2.  **Ensuring Long-Term Application Health**: Over time, applications might encounter issues that degrade their ability to function correctly but don't cause the process to crash. Liveness probes provide a mechanism to detect such persistent unhealthy states and force a restart, ensuring the long-term health and reliability of the application.
3.  **Reducing Mean Time To Recovery (MTTR)**: By automatically detecting and restarting unhealthy containers, liveness probes significantly reduce the time it takes to recover from certain types of failures, improving overall service availability.

In essence, readiness probes protect users from interacting with pods that aren't ready, while liveness probes ensure that unresponsive or deadlocked pods are automatically recovered. Together, they form a crucial foundation for building self-healing, resilient applications in Kubernetes, directly contributing to a stable and reliable user experience even in the face of application initialization delays, temporary issues, or unexpected failures.

### How does HPA help in handling flash sales, seasonal load spikes, or traffic surges in real-world applications like an e-commerce platform?

Horizontal Pod Autoscaler (HPA) is a vital Kubernetes component for managing the dynamic resource demands of real-world applications, particularly those experiencing variable traffic patterns like e-commerce platforms during flash sales, holidays, or marketing campaigns.

HPA automatically adjusts the number of running pods in a deployment or other scalable resource based on observed metrics, most commonly CPU utilization or memory consumption. Its benefits in handling traffic surges are manifold:

1.  **Automatic Scalability**: The primary benefit is HPA's ability to automatically scale the application horizontally (by adding more pods) when load increases. During a flash sale or traffic surge, user requests increase dramatically. HPA detects the corresponding rise in CPU or memory usage across the existing pods. When the average utilization exceeds the predefined target threshold (e.g., 50% CPU), HPA automatically provisions new pods, up to the configured maximum limit.
2.  **Maintaining Performance and Responsiveness**: By adding more pods, HPA distributes the increased load across a larger number of instances. This prevents individual pods from becoming overwhelmed, ensuring that the application remains responsive and maintains acceptable performance levels (e.g., low latency) even under peak load. Without HPA, a sudden surge could saturate the existing pods, leading to slow response times, errors, and a poor user experience, potentially resulting in lost sales.
3.  **Cost Optimization**: HPA also scales the application down when the load decreases. After a flash sale ends or a seasonal spike subsides, traffic returns to normal levels. HPA observes the drop in resource utilization and automatically terminates surplus pods, scaling the deployment back towards the minimum configured replica count. This ensures that resources are only provisioned when needed, preventing over-provisioning and optimizing infrastructure costs during periods of lower demand.
4.  **Rapid Response to Fluctuations**: HPA monitors metrics at regular intervals (configurable, typically every 15-30 seconds) and can react relatively quickly to changes in load. This rapid response is crucial for handling sudden, unexpected traffic surges that can occur during events like flash sales or viral marketing campaigns.
5.  **Improved Resource Utilization**: By dynamically adjusting the number of pods based on actual demand, HPA leads to more efficient use of cluster resources compared to static provisioning based on peak load estimates. Resources are allocated elastically, matching capacity closely to current needs.
6.  **Reduced Manual Intervention**: HPA automates the scaling process, reducing the need for operations teams to manually monitor traffic and adjust replica counts during high-stakes events like sales. This frees up personnel and reduces the risk of human error in scaling decisions.

For an e-commerce platform, HPA configured on critical components like the product catalog service, shopping cart API, or order processing backend ensures that the platform can seamlessly handle the influx of users during a Black Friday sale, a holiday shopping season, or a limited-time promotion. It provides the elasticity needed to maintain a positive customer experience while managing operational costs effectively.

![Alt Text](/assets/Screenshot%20from%202025-06-03%2017-19-57.png)
![Alt Text](/assets/Screenshot%20from%202025-06-03%2017-32-45.png)
![Alt Text](/assets/Screenshot%20from%202025-06-03%2017-32-56.png)
