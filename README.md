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
![Screenshot from 2025-05-21 16-41-27](https://github.com/user-attachments/assets/1d34a1d0-a9fc-4b90-86b0-7f2fccc64a82)

![Screenshot from 2025-05-21 16-47-15](https://github.com/user-attachments/assets/1ae42287-f9e9-4a48-83c5-a30e755bdf7b)

![Screenshot from 2025-05-21 17-08-37](https://github.com/user-attachments/assets/665994ef-4a93-492e-8cc8-782836c26500)

![Screenshot from 2025-05-21 17-11-37](https://github.com/user-attachments/assets/4bc572f8-214e-4f9e-9494-2f2a35180624)


## Docker Hub Image Link
[Docker Image](https://hub.docker.com/r/deepaknash/hello-docker)
