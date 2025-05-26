# Day 1 - Docker & Kubernetes Workshop

## Core Concept Questions

### Why is Docker useful in building and deploying microservices for a real-world product (like an e-commerce or banking app)?
Docker allows you to package microservices into isolated containers with their dependencies. This ensures consistency across environments (dev, test, prod), simplifies CI/CD pipelines, and allows each service to scale and deploy independently, which is vital for complex, real-world apps like e-commerce or banking systems.

### What is the difference between a Docker image and a container in the context of scaling a web application?
A Docker **image** is a static template with the application code and environment. A **container** is a runtime instance of that image. In scaling, multiple containers can be spawned from the same image to distribute load efficiently across the infrastructure.

### How does Kubernetes complement Docker when running a product at scale (e.g., hundreds of containers)?
Kubernetes automates deployment, scaling, and management of containerized applications. It helps orchestrate containers across a cluster, manages service discovery, self-healing, rolling updates, and auto-scaling—critical for operating large-scale systems efficiently.

---

## Screenshot or Push Log
![Docker Build Screenshot](../../../../../Pictures/Screenshots/Screenshot%20from%202025-05-21%2016-41-27.png)
![Docker Run Screenshot](../../../../../Pictures/Screenshots/Screenshot%20from%202025-05-21%2016-47-15.png)
![Dpcker Push Screenshot](../../../../../Pictures/Screenshots/Screenshot%20from%202025-05-21%2017-08-37.png)
![DockerHub Repository](../../../../../Pictures/Screenshots/Screenshot%20from%202025-05-21%2017-11-37.png)

## Docker Hub Image Link
[Docker Image](https://hub.docker.com/r/deepaknash/hello-docker)

# Kubernetes Architecture and Pod Scheduling Exercise

## Implementation Details

### Resource Management
- CPU and Memory requests: 100m CPU, 128Mi memory
- CPU and Memory limits: 200m CPU, 256Mi memory

### Node Affinity Rules
1. Required Rules (Hard Requirements):
   - Pods must be scheduled on Linux nodes
   - This is enforced using `requiredDuringSchedulingIgnoredDuringExecution`
   - Uses the `kubernetes.io/os` label to ensure Linux compatibility

2. Preferred Rules (Soft Requirements):
   - Pods prefer to be scheduled on worker nodes (weight: 1)
   - This is implemented using `preferredDuringSchedulingIgnoredDuringExecution`
   - Uses the `node-role.kubernetes.io/worker` label
   - Weight of 1 indicates the strength of this preference

### Tolerations
- Pods can tolerate the master node's NoSchedule taint
- This allows pods to be scheduled on master nodes if necessary
- Uses the `node-role.kubernetes.io/master` taint key

### Implementation Files
1. `deployment.yaml`: Contains the main deployment configuration
2. `node-affinity.yaml`: Contains the node affinity rules
3. `combined-manifest.yaml`: Combined configuration for easy deployment

### Verification Steps
To verify the deployment and pod placement:
```bash
# Apply the combined manifest
kubectl apply -f combined-manifest.yaml

# Check pod status and placement
kubectl get pods -o wide

# Verify node affinity rules
kubectl describe pod <pod-name>
```

The output should show:
- Pods running on Linux nodes
- Preferred placement on worker nodes
- Resource limits and requests being respected
- Tolerations allowing master node scheduling if needed

## Core Concept Questions

### Why do we set requests and limits for CPU/memory in a production-grade product?
Setting resource requests and limits in Kubernetes is crucial for production environments for several reasons:

1. **Resource Guarantees**: Requests ensure that pods get the minimum resources they need to function properly, preventing resource starvation.

2. **Resource Protection**: Limits prevent pods from consuming excessive resources that could impact other applications on the same node.

3. **Scheduling Efficiency**: The scheduler uses requests to make informed decisions about pod placement, ensuring nodes aren't overcommitted.

4. **Cost Management**: Helps in capacity planning and cost optimization by providing visibility into resource usage.

5. **Stability**: Prevents "noisy neighbor" problems where one pod's resource consumption affects others.

### When would a product team apply node affinity in Kubernetes?
Node affinity is applied in various scenarios:

1. **Hardware Requirements**: When pods need specific hardware (e.g., GPUs, SSDs, or high-memory nodes).

2. **Geographic Distribution**: To ensure pods run in specific regions or availability zones.

3. **Workload Isolation**: To separate different types of workloads (e.g., production vs. development).

4. **Performance Optimization**: To place pods closer to required resources or services.

5. **Compliance Requirements**: To meet regulatory requirements about data locality.

6. **Cost Optimization**: To utilize specific instance types or spot instances.

![alt text](<assets/Screenshot from 2025-05-22 14-33-58.png>) 
![alt text](<assets/Screenshot from 2025-05-22 14-34-57.png>) 
![alt text](<assets/Screenshot from 2025-05-22 14-42-59.png>) 
![alt text](<assets/Screenshot from 2025-05-22 14-43-52.png>)

# Kubernetes Networking and Service Discovery

## Service Types Implementation

### NodePort Service
- Service name: `hello-express-service`
- Type: NodePort
- Port: 80
- Target Port: 8080
- Access: Available on all node IPs at the assigned NodePort

### LoadBalancer Service
- Service name: `hello-express-lb`
- Type: LoadBalancer
- Port: 80
- Target Port: 8080
- Access: Available through the cloud provider's load balancer IP

### Ingress Configuration
- Name: `hello-express-ingress`
- Path: `/api`
- Backend Service: `hello-express-service`
- Port: 80
- Access: Available through the Ingress controller's IP/hostname

## Core Concept Questions

### How would you expose an internal microservice (e.g., user-auth) differently than a public-facing frontend in a Kubernetes-based product?

For internal microservices like user-auth:
1. Use ClusterIP service type to keep it accessible only within the cluster
2. Implement network policies to restrict access to specific namespaces
3. Use service mesh (like Istio) for internal service-to-service communication
4. Implement mTLS for secure internal communication

For public-facing frontend:
1. Use LoadBalancer or Ingress for external access
2. Implement proper TLS termination
3. Use WAF (Web Application Firewall) for security
4. Configure proper rate limiting and DDoS protection

### Why might a product use Ingress instead of directly exposing each microservice via LoadBalancer?

1. **Cost Efficiency**: Each LoadBalancer service requires a cloud load balancer, which can be expensive. Ingress uses a single load balancer for multiple services.

2. **Simplified Management**: Ingress provides a single entry point for all services, making it easier to manage SSL/TLS certificates and routing rules.

3. **Advanced Routing**: Ingress supports path-based routing, host-based routing, and can handle complex URL rewrites and redirects.

4. **Security**: Centralized security policies and SSL termination at the Ingress level.

5. **Traffic Management**: Better control over traffic distribution, canary deployments, and A/B testing.

## Testing Instructions

1. Apply the configurations:
```bash
kubectl apply -f deployment.yaml
kubectl apply -f loadbalancer-service.yaml
kubectl apply -f ingress.yaml
```

2. Test the services:
```bash
# Get NodePort
kubectl get svc hello-express-service

# Get LoadBalancer IP
kubectl get svc hello-express-lb

# Get Ingress IP/hostname
kubectl get ingress hello-express-ingress
```

3. Access the application:
- NodePort: `http://<node-ip>:<nodeport>`
- LoadBalancer: `http://<loadbalancer-ip>`
- Ingress: `http://<ingress-ip>/api`

![alt text](<assets/Screenshot from 2025-05-26 12-48-06.png>)
![alt text](<assets/Screenshot from 2025-05-26 12-50-13.png>)
