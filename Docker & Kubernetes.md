We’ll learn:

- What Docker and Kubernetes are and why they matter
- Key concepts, terms, and mental models
- Practical usage and commands
- Architectural best practices, scaling, and patterns
- How to use them together and where they fit in backend/distributed systems interviews

## **1. What is Docker?**

Docker is a platform for **packaging, distributing, and running applications (code & dependencies) in [[Containers]]**. It makes it easy to build portable, reproducible application environments.

---
## **2. Key Concepts and Terms**

- **Image:** The _blueprint_ or template—contains code, runtime, libraries, environment (like a snapshot).
    
- **Container:** A running instance of an image (stateful or ephemeral).
    
- **Dockerfile:** Declarative script with instructions to build an image (FROM base, COPY code, RUN install, CMD start, etc).
    
- **Registry:** Place to store/share images (Docker Hub, AWS ECR, GCR).
    
- **Volume:** Mounted host storage for data persistence or sharing between containers.
    
- **Network:** Virtual networks for containers to communicate, isolated from host/other containers by default.