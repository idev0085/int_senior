# Docker Interview Questions

## Basic Questions

### 1. What is Docker?
Docker is an open-source platform that automates the deployment, scaling, and management of applications using containerization technology. It packages applications and their dependencies into containers that can run consistently across different environments.

### 2. What are the benefits of using Docker?
- Consistent environments across development, testing, and production
- Faster deployment and scaling
- Resource efficiency compared to VMs
- Isolation of applications
- Simplified dependency management
- Easy version control and rollback
- Microservices architecture support
- Reduced infrastructure costs

### 3. What is the difference between Docker and Virtual Machines?
**Docker Containers**:
- Share host OS kernel
- Lightweight (MBs)
- Start in seconds
- Lower resource overhead
- Better density on single host

**Virtual Machines**:
- Each has its own OS
- Heavy (GBs)
- Start in minutes
- Higher resource overhead
- Complete isolation

### 4. What is a Docker Image?
A Docker image is a read-only template containing:
- Application code
- Runtime environment
- System libraries
- Dependencies
- Configuration files

Images are built from Dockerfile instructions and stored in registries.

### 5. What is a Docker Container?
A container is a running instance of a Docker image. It's a lightweight, standalone, executable package that includes everything needed to run the application.

### 6. What is a Dockerfile?
A Dockerfile is a text file containing instructions to build a Docker image.

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

### 7. What is Docker Hub?
Docker Hub is a cloud-based registry service for storing and sharing Docker images. It provides:
- Public and private repositories
- Official images
- Automated builds
- Webhooks
- Team collaboration

### 8. What are the main Docker components?
- **Docker Engine**: Core runtime
- **Docker Daemon**: Background service managing containers
- **Docker Client**: CLI for interacting with daemon
- **Docker Registry**: Storage for images
- **Docker Compose**: Multi-container orchestration
- **Docker Swarm**: Native clustering

## Intermediate Questions

### 9. Explain Dockerfile instructions
- `FROM` - Base image
- `RUN` - Execute commands during build
- `COPY` - Copy files from host to image
- `ADD` - Copy files (supports URLs and archives)
- `WORKDIR` - Set working directory
- `ENV` - Set environment variables
- `EXPOSE` - Document port usage
- `CMD` - Default command when container starts
- `ENTRYPOINT` - Configure container as executable
- `VOLUME` - Create mount point
- `ARG` - Build-time variables
- `LABEL` - Add metadata

### 10. What is the difference between CMD and ENTRYPOINT?
**CMD**:
- Default command and/or parameters
- Can be overridden at runtime
- Multiple CMDs, only last one takes effect

**ENTRYPOINT**:
- Configures container to run as executable
- Not easily overridden
- CMD becomes arguments to ENTRYPOINT

```dockerfile
ENTRYPOINT ["python"]
CMD ["app.py"]
# Run: docker run myimage main.py (overrides CMD)
```

### 11. What is the difference between COPY and ADD?
**COPY**:
- Simple file copying
- Preferred for basic file operations
- More transparent

**ADD**:
- Auto-extracts tar archives
- Supports URLs
- More features but less predictable
- Use COPY unless you need ADD's special features

### 12. What are Docker volumes?
Volumes are persistent data storage mechanisms:
- Persist data beyond container lifecycle
- Share data between containers
- Stored outside container filesystem
- Managed by Docker

```bash
# Create volume
docker volume create myvolume

# Use volume
docker run -v myvolume:/app/data myimage
```

### 13. What are the types of Docker storage?
- **Volumes**: Managed by Docker, best for persistence
- **Bind Mounts**: Map host directory to container
- **tmpfs Mounts**: Stored in host memory only

### 14. What is Docker Compose?
Docker Compose is a tool for defining and running multi-container applications using YAML files.

```yaml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db
  db:
    image: postgres:14
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - db-data:/var/lib/postgresql/data
volumes:
  db-data:
```

### 15. What are Docker networks?
Docker networks enable container communication:
- **Bridge** (default): Isolated network for containers on same host
- **Host**: Remove network isolation, use host network
- **Overlay**: Multi-host networking for Swarm
- **Macvlan**: Assign MAC address to container
- **None**: Disable networking

```bash
docker network create mynetwork
docker run --network=mynetwork myimage
```

### 16. What is Docker layer caching?
Docker builds images in layers:
- Each instruction creates a layer
- Layers are cached
- Unchanged layers reused in rebuilds
- Optimize by ordering instructions (frequently changing last)

```dockerfile
# Good: Dependencies cached separately from code
COPY package*.json ./
RUN npm install
COPY . .
```

### 17. What is multi-stage build?
Multi-stage builds create smaller production images by separating build and runtime stages.

```dockerfile
# Build stage
FROM node:18 AS builder
WORKDIR /app
COPY . .
RUN npm install && npm run build

# Production stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm install --production
CMD ["node", "dist/index.js"]
```

### 18. What are common Docker commands?

```bash
# Images
docker images                    # List images
docker build -t myapp:1.0 .     # Build image
docker pull nginx               # Pull image
docker rmi image_id             # Remove image
docker tag source target        # Tag image

# Containers
docker ps                       # List running containers
docker ps -a                    # List all containers
docker run -d -p 8080:80 nginx # Run container
docker stop container_id        # Stop container
docker start container_id       # Start container
docker restart container_id     # Restart container
docker rm container_id          # Remove container
docker exec -it container_id bash # Execute command

# System
docker system prune             # Remove unused data
docker logs container_id        # View logs
docker inspect container_id     # Detailed info
docker stats                    # Resource usage
```

## Advanced Questions

### 19. What is Docker Swarm?
Docker Swarm is Docker's native clustering and orchestration tool:
- Manages cluster of Docker engines
- Service scaling
- Load balancing
- Rolling updates
- Service discovery
- Health checks

```bash
# Initialize swarm
docker swarm init

# Deploy service
docker service create --replicas 3 -p 80:80 nginx

# Scale service
docker service scale myservice=5
```

### 20. What is the difference between Docker Swarm and Kubernetes?
**Docker Swarm**:
- Simpler setup and learning curve
- Tightly integrated with Docker
- Good for smaller deployments
- Limited ecosystem

**Kubernetes**:
- More complex but powerful
- Larger ecosystem and community
- Better for large-scale deployments
- More features and flexibility
- Industry standard

### 21. How do you optimize Docker images?
- Use smaller base images (alpine)
- Multi-stage builds
- Minimize layers (combine RUN commands)
- Use .dockerignore
- Remove unnecessary files
- Don't install debug tools in production
- Leverage build cache
- Use specific versions, not `latest`

```dockerfile
# Bad
RUN apt-get update
RUN apt-get install -y package1
RUN apt-get install -y package2

# Good
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    && rm -rf /var/lib/apt/lists/*
```

### 22. What is .dockerignore?
Similar to .gitignore, excludes files from build context:

```
node_modules
.git
.env
*.log
.DS_Store
coverage/
.vscode/
```

### 23. How do you handle secrets in Docker?
- Docker secrets (Swarm mode)
- Environment variables
- External secret management (Vault, AWS Secrets Manager)
- Build arguments (for build-time secrets)
- Never hardcode secrets in images

```bash
# Docker secret
echo "mypassword" | docker secret create db_password -
docker service create --secret db_password myapp
```

### 24. What is Docker health check?
Health checks determine container health:

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s --retries=3 \
  CMD curl -f http://localhost/ || exit 1
```

```bash
# In compose
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 30s
  timeout: 10s
  retries: 3
```

### 25. How do you debug Docker containers?
```bash
# View logs
docker logs -f container_id

# Execute shell
docker exec -it container_id sh

# Inspect container
docker inspect container_id

# View processes
docker top container_id

# Monitor resources
docker stats container_id

# Copy files from container
docker cp container_id:/path/to/file ./local/path

# Run with interactive shell
docker run -it --entrypoint sh image_name
```

### 26. What are Docker BuildKit features?
BuildKit is Docker's improved build engine:
- Parallel build stages
- Better caching
- Secret mounting
- SSH agent forwarding
- Build cache export/import

```dockerfile
# syntax=docker/dockerfile:1
FROM alpine
RUN --mount=type=secret,id=mysecret cat /run/secrets/mysecret
```

### 27. How do you implement CI/CD with Docker?
- Build images in CI pipeline
- Tag images with version/commit SHA
- Push to registry
- Deploy to environments
- Use Docker Compose for testing
- Automated testing in containers

```yaml
# GitLab CI example
build:
  script:
    - docker build -t myapp:$CI_COMMIT_SHA .
    - docker push myapp:$CI_COMMIT_SHA

deploy:
  script:
    - docker pull myapp:$CI_COMMIT_SHA
    - docker-compose up -d
```

### 28. What is Docker context?
Docker context manages multiple Docker environments:

```bash
# Create context
docker context create remote --docker "host=ssh://user@remote"

# Use context
docker context use remote

# List contexts
docker context ls
```

### 29. How do you limit container resources?
```bash
# Memory limit
docker run -m 512m myimage

# CPU limit
docker run --cpus="1.5" myimage

# In compose
services:
  web:
    image: myapp
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
```

### 30. What is Docker registry?
A Docker registry stores and distributes Docker images:
- **Docker Hub**: Public registry
- **Private Registry**: Self-hosted
- **Cloud Registries**: AWS ECR, Google GCR, Azure ACR

```bash
# Run private registry
docker run -d -p 5000:5000 --name registry registry:2

# Push to private registry
docker tag myapp localhost:5000/myapp
docker push localhost:5000/myapp
```

## Best Practices

### 31. Docker Security Best Practices
- Use official base images
- Scan images for vulnerabilities
- Don't run as root user
- Use secrets management
- Keep images updated
- Minimize attack surface
- Use read-only filesystems when possible
- Enable Docker Content Trust
- Limit container capabilities
- Use network segmentation

```dockerfile
# Run as non-root
FROM node:18-alpine
RUN addgroup -g 1001 -S nodejs && adduser -S nodejs -u 1001
USER nodejs
```

### 32. Docker Production Best Practices
- Use specific image versions
- Implement health checks
- Configure resource limits
- Use multi-stage builds
- Implement logging and monitoring
- Use orchestration (Kubernetes, Swarm)
- Automate deployments
- Regular backups
- Disaster recovery plan
- Documentation
