<!-- <p align="center">
<img src="/src/frontend/static/icons/Hipster_HeroLogoMaroon.svg" width="300" alt="Online Boutique" />
</p> -->
![Continuous Integration](https://github.com/GoogleCloudPlatform/microservices-demo/workflows/Continuous%20Integration%20-%20Main/Release/badge.svg)

**Online Boutique** is a cloud-first microservices demo application.  The application is a
web-based e-commerce app where users can browse items, add them to the cart, and purchase them.

Google uses this application to demonstrate how developers can modernize enterprise applications using Google Cloud products, including: [Google Kubernetes Engine (GKE)](https://cloud.google.com/kubernetes-engine), [Cloud Service Mesh (CSM)](https://cloud.google.com/service-mesh), [gRPC](https://grpc.io/), [Cloud Operations](https://cloud.google.com/products/operations), [Spanner](https://cloud.google.com/spanner), [Memorystore](https://cloud.google.com/memorystore), [AlloyDB](https://cloud.google.com/alloydb), and [Gemini](https://ai.google.dev/). This application works on any Kubernetes cluster.

If you're using this demo, please **★Star** this repository to show your interest!


## Architecture

**Online Boutique** is composed of 11 microservices written in different
languages that talk to each other over gRPC.

[![Architecture of
microservices](/docs/img/architecture-diagram.png)](/docs/img/architecture-diagram.png)

Find **Protocol Buffers Descriptions** at the [`./protos` directory](/protos).

| Service                                              | Language      | Description                                                                                                                       |
| ---------------------------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [frontend](/src/frontend)                           | Go            | Exposes an HTTP server to serve the website. Does not require signup/login and generates session IDs for all users automatically. |
| [cartservice](/src/cartservice)                     | C#            | Stores the items in the user's shopping cart in Redis and retrieves it.                                                           |
| [productcatalogservice](/src/productcatalogservice) | Go            | Provides the list of products from a JSON file and ability to search products and get individual products.                        |
| [currencyservice](/src/currencyservice)             | Node.js       | Converts one money amount to another currency. Uses real values fetched from European Central Bank. It's the highest QPS service. |
| [paymentservice](/src/paymentservice)               | Node.js       | Charges the given credit card info (mock) with the given amount and returns a transaction ID.                                     |
| [shippingservice](/src/shippingservice)             | Go            | Gives shipping cost estimates based on the shopping cart. Ships items to the given address (mock)                                 |
| [emailservice](/src/emailservice)                   | Python        | Sends users an order confirmation email (mock).                                                                                   |
| [checkoutservice](/src/checkoutservice)             | Go            | Retrieves user cart, prepares order and orchestrates the payment, shipping and the email notification.                            |
| [recommendationservice](/src/recommendationservice) | Python        | Recommends other products based on what's given in the cart.                                                                      |
| [adservice](/src/adservice)                         | Java          | Provides text ads based on given context words.                                                                                   |
| [loadgenerator](/src/loadgenerator)                 | Python/Locust | Continuously sends requests imitating realistic user shopping flows to the frontend.                                              |

## Screenshots

| Home Page                                                                                                         | Checkout Screen                                                                                                    |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| [![Screenshot of store homepage](/docs/img/online-boutique-frontend-1.png)](/docs/img/online-boutique-frontend-1.png) | [![Screenshot of checkout screen](/docs/img/online-boutique-frontend-2.png)](/docs/img/online-boutique-frontend-2.png) |


# 🏪 Online Boutique - Microservices Deployment Project

## 📋 Project Overview

Welcome to your **Microservices Deployment Project**! You will deploy Google's Online Boutique - a cloud-native microservices demo application across **3 EC2 instances** with load balancing and scaling capabilities.

Your mission is to demonstrate mastery of distributed systems, containerization, and load balancing by deploying this 11-service application across multiple instances.

## Learning Objectives

By completing this project, you will master:

- **Distributed Deployment**: Deploying microservices across multiple instances
- **Container Orchestration**: Using Docker to manage complex applications
- **Load Balancing**: Implementing Nginx for traffic distribution and high availability
- **Scaling**: Horizontal scaling of microservices
- **Service Discovery**: Managing communication between distributed services
- **Infrastructure Management**: AWS EC2, networking, and security configuration

## 🏗️ Application Architecture

The Online Boutique consists of **11 microservices** that you'll deploy across **3 EC2 instances**:

```
                    ┌─────────────────┐
                    │   Instance 3    │
                    │ Load Balancer   │
                    │    (Nginx)      │
                    └─────────┬───────┘
                              │
                    ┌─────────▼───────┐
                    │   Load Balance  │
                    │    Traffic      │
                    └─────┬─────┬─────┘
                          │     │
              ┌───────────▼─┐ ┌─▼───────────┐
              │ Instance 1  │ │ Instance 2  │
              │Application  │ │Application  │
              │ Services    │ │ Services    │
              └─────────────┘ └─────────────┘
```

### Service Details

| Service | Language | Purpose | Port |
|---------|----------|---------|------|
| **frontend** | Go | Web UI and user interface | 8080 |
| **cartservice** | C# | Shopping cart management | 7070 |
| **productcatalogservice** | Go | Product catalog and search | 3550 |
| **currencyservice** | Node.js | Currency conversion | 7000 |
| **paymentservice** | Node.js | Payment processing | 50051 |
| **shippingservice** | Go | Shipping cost calculation | 50051 |
| **emailservice** | Python | Order confirmation emails | 8080 |
| **checkoutservice** | Go | Order orchestration | 5050 |
| **recommendationservice** | Python | Product recommendations | 8080 |
| **adservice** | Java | Contextual advertisements | 9555 |
| **loadgenerator** | Python/Locust | Simulates user traffic | N/A |

**Important**: Each service has its own **Dockerfile** in the `src/[service-name]/` directory that you can use to build custom images, or you can use the pre-built images.

## Your Mission

### Phase 1: Infrastructure Setup

**Task 1.1: Launch 3 EC2 Instances**
- **Instance 1**: Application Server 1
- **Instance 2**: Application Server 2  
- **Instance 3**: Nginx Load Balancer

**Requirements:**
- Instance type: t3.medium
- OS: Ubuntu 22.04 LTS
- Storage: 20 GB minimum
- Security groups configured

**Task 1.2: Security Group Configuration**
Configure security groups to allow:
- **Instance 1 & 2**: Accept traffic from Instance 3 and SSH access
- **Instance 3**: Accept HTTP/HTTPS from internet, communicate with Instance 1 & 2, SSH access

### Phase 2: Application Deployment (Instances 1 & 2)

**Task 2.1: Choose Your Deployment Strategy**
You have flexibility in how you deploy the services:

**Option A: Docker Compose**
- Create `docker-compose.yml` files
- Use pre-built images or build from Dockerfiles
- Manage services as a stack

**Option B: Individual Docker Containers**
- Run each service as separate containers
- Build images from provided Dockerfiles
- Manage containers individually

**Option C: Mixed Approach**
- Combine both strategies as needed
- Group related services with Docker Compose
- Run others individually

**Task 2.2: Service Distribution**
Distribute the 11 services across Instance 1 and Instance 2. Consider:
- **Resource requirements** of each service
- **Dependencies** between services
- **Load balancing** requirements
- **Scaling** potential

**Suggested Distribution** :
- **Instance 1**: Frontend, Checkout, Recommendation, Ad Service, Load Generator
- **Instance 2**: Product Catalog, Cart, Currency, Payment, Shipping, Email + Redis

**Task 2.3: Container Management**
Whether using Docker Compose or individual containers:
- Configure **environment variables** for service communication
- Set up **networking** between services
- Implement **health checks**
- Configure **resource limits**
- Set up **persistent storage** where needed (Redis) (Volumes)

**Task 2.4: Build or Use Images**
For each service, decide:
- **Use pre-built images**: `us-central1-docker.pkg.dev/google-samples/microservices-demo/[service]:v0.10.2`
- **Build from Dockerfile**: `docker build -t my-[service] src/[service]/`

### Phase 3: Load Balancer Setup (Instance 3)

**Task 3.1: Install and Configure Nginx**
- Install Nginx on Instance 3
- Configure as a **reverse proxy**
- Set up **upstream servers** (Instance 1 & 2)

**Task 3.2: Load Balancing Configuration**
Implement:
- **Round-robin** or **weighted** load balancing
- **Health checks** for backend instances
- **Failover** handling
- **Session persistence** if needed

**Task 3.3: Advanced Features**
Configure:
- **Rate limiting**
- **SSL termination** (optional)
- **Caching** for static content
- **Security headers**
- **Access logging**

### Phase 4: Scaling and High Availability

**Task 4.1: Horizontal Scaling**
Demonstrate scaling by:
- **Adding more instances** of specific services
- **Load balancing** between multiple instances of the same service
- **Testing** performance improvements

**Task 4.2: Service Scaling Strategies**
Implement scaling for:
- **Frontend services** (multiple frontend containers)
- **Backend services** (multiple API service instances)
- **Database scaling** (Redis clustering or replication)

**Task 4.3: Auto-scaling Preparation**
Design your architecture to support:
- **Easy addition** of new instances
- **Dynamic configuration** updates
- **Monitoring** for scaling decisions

### Phase 5: Testing and Validation

**Task 5.1: Functional Testing**
- Access application through load balancer
- Test complete user workflows
- Verify all services communicate properly
- Test cross-instance communication

**Task 5.2: Load Testing**
- Use the built-in **load generator**
- Perform **Apache Bench** or similar testing
- Monitor **performance metrics**
- Test **load distribution**

**Task 5.3: Scaling Validation**
- **Scale up** services during load testing
- **Scale down** and verify graceful handling
- Test **failover** scenarios
- Measure **performance improvements**

**Task 5.4: Failure Testing**
- Stop services on one instance
- Verify load balancer **failover**
- Test **service recovery**
- Document **recovery procedures**


# You Deliver

### 1. Architecture Documentation
- **Instance layout** and service distribution
- **Network diagram** showing load balancing
- **Scaling strategy** documentation

### 2. Deployment Configuration
- **Docker configurations** (Compose files or run commands)
- **Nginx configuration** with load balancing
- **Environment variables** and service discovery setup

### 3. Scaling Implementation
- **Horizontal scaling** examples
- **Load balancing** configuration
- **Performance testing** results

### 4. Testing Documentation
- **Load testing** results and analysis
- **Scaling validation** tests
- **Failure recovery** procedures


## Decisions You'll Make

### Service Distribution
- Which services go on which instance? Explain why ! 
- How to handle service dependencies? 
- Resource allocation strategy?

### Deployment Method
- Docker Compose vs individual containers?
- Build custom images or use pre-built?
- How to manage configuration?

### Load Balancing Strategy
- Round-robin vs weighted distribution?
- Health check configuration?
- Failover handling approach?

### Scaling Approach
- Which services to scale first?
- How to handle stateful vs stateless services?
- Monitoring and metrics strategy?

## Challenges

### Service Discovery
- Services finding each other across instances
- Dynamic IP address handling
- Port management and conflicts

### Load Balancing
- Proper upstream configuration
- Health check implementation
- Session handling for stateful services

### Scaling Complexity
- Managing multiple instances of same service
- Database scaling (Redis)
- Configuration management at scale

### Resource Management
- Memory and CPU allocation
- Network bandwidth considerations
- Storage requirements

## How to Start ? 

1. **Start simple** - Get basic deployment working first
2. **Test incrementally** - Validate each phase before moving on
3. **Document decisions** - Explain your architecture choices
4. **Monitor everything** - Use logs and metrics extensively
5. **Plan for scale** - Design with scaling in mind from the start
6. **Test failure scenarios** - Don't just test the happy path


## Success Criteria

Your project succeeds when:
- ✅ All 11 services deployed across 2 application instances
- ✅ Nginx load balancer distributing traffic effectively
- ✅ Application accessible and fully functional
- ✅ Scaling demonstrated with performance improvements
- ✅ Failure scenarios handled gracefully
- ✅ Architecture well-documented and justified

## Resources

### Docker Images (Pre-built)
```
us-central1-docker.pkg.dev/google-samples/microservices-demo/[service-name]:v0.10.2
```

### Dockerfiles
Each service has a Dockerfile in `src/[service-name]/Dockerfile`

### Service Communication
- **gRPC** for inter-service communication
- **HTTP/REST** for frontend
- **Redis** for cart data storage

