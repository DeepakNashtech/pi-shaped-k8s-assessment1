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
