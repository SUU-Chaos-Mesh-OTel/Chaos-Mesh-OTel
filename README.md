# Chaos-Mesh-OTel

## 2025, Thursday (8:30 AM)

**Authors:**  
- Antoni Wójcik  
- Grzegorz Piśkorski  
- Kacper Jurek  
- Wojciech Suski  

---

## Contents

1. [Introduction](#1-introduction)  
2. [Theoretical background / technology stack](#2-theoretical-background--technology-stack)  
   1. [Fault injections](#fault-injections)  
   2. [Key Technologies Used](#key-technologies-used)  
3. [Case study concept description](#3-case-study-concept-description)  
   1. [Test Scenarios](#test-scenarios)  
   2. [Observability and Monitoring](#observability-and-monitoring)  
4. [Solution architecture](#4-solution-architecture)  
   1. [General Architecture](#41-general-architecture)  
   2. [Architecture Overview](#42-architecture-overview)  
   3. [Component Roles and Interactions](#43-component-roles-and-interactions)  
      - [Minikube (Kubernetes Environment)](#minikube-kubernetes-environment)  
      - [Chaos Mesh](#chaos-mesh)  
      - [Prometheus](#prometheus)  
      - [Grafana](#grafana)  
      - [Load Generator](#load-generator)  
      - [Online Boutique Microservices](#online-boutique-microservices)  
5. [Environment configuration description](#5-environment-configuration-description)  
6. [Tools installation](#6-tools-installation)  
7. [How to reproduce - step by step](#7-how-to-reproduce---step-by-step)  
   1. [Infrastructure as Code approach](#71-infrastructure-as-code-approach)  
8. [Demo deployment steps](#8-demo-deployment-steps)  
   1. [Configuration set-up](#81-configuration-set-up)  
   2. [Data preparation](#82-data-preparation)  
   3. [Execution procedure](#83-execution-procedure)  
   4. [Results presentation](#84-results-presentation)  
9. [Using AI in the project](#9-using-ai-in-the-project)  
10. [Summary – conclusions](#10-summary--conclusions)  
11. [References](#11-references)  


## 1. Introduction
Chaos Mesh is an open source cloud-native Chaos Engineering platform. It offers various types of fault simulation and has an enormous capability to orchestrate fault scenarios. Using Chaos Mesh, we can conveniently simulate various abnormalities that might occur in reality during the development, testing, and production environments and find potential problems in the system. To lower the threshold for a Chaos Engineering project, Chaos Mesh provides us with a visualization operation. We can easily design our Chaos scenarios on the Web UI and monitor the status of Chaos experiments.

Core strengths of Chaos Mesh:
* An easy-to-use system: Chaos Mesh makes full use of automation with graphical operations and Kubernetes-based usage.
* Cloud Native: Chaos Mesh supports Kubernetes environment with its powerful automation ability.
* Various fault simulation scenarios: Chaos Mesh covers most of the scenarios of basic fault simulation in the distributed testing system.
* Flexible experiment orchestration capabilities: We can design our own Chaos experiment scenarios on the platform, including multiple mixing experiments and application status checks.
* High security: Chaos Mesh is designed with multiple layers of security control and provides high security.
* Easily scalable: It's easy to add new fault test types and functions to Chaos Mesh.


## 2. Theoretical background / technology stack
### Fault injections
Fault injection is the key of Chaos experiments. Chaos Mesh covers a full range of faults that might occur in a distributed system, and provides three comprehensive and fine-grained fault types: basic resource faults, platform faults, and application-layer faults.
* Basic resource faults:
  - PodChaos: simulates Pod failures, such as Pod node restart, Pod's persistent unavailability, and certain container failures in a specific Pod.
  - NetworkChaos: simulates network failures, such as network latency, packet loss, packet disorder, and network partitions.
  - DNSChaos: simulates DNS failures, such as the parsing failure of DNS domain name and the wrong IP address returned.
  - HTTPCHaos: simulates HTTP communication failures, such as HTTP communication latency.
  - StressChaos: simulates CPU race or memory race.
  - IOChaos: simulates the I/O failure of an application file, such as I/O delays, read and write failures.
  - TimeChaos: simulates the time jump exception.
  - KernelChaos: simulates kernel failures, such as an exception of the application memory allocation.
* Platform faults:
  - AWSChaos: simulates AWS platform failures, such as the AWS node restart.
  - GCPChaos: simulates GCP platform failures, such as the GCP node restart.
* Application faults:
  - JVMChaos: simulates JVM application failures, such as the function call delay.

### Key Technologies Used

- **Kubernetes**  
  A powerful container orchestration system used to deploy, manage, and scale containerized applications. Chaos Mesh runs natively within Kubernetes clusters.

- **Chaos Mesh**  
  The core Chaos Engineering platform used to inject faults into Kubernetes-managed applications for testing system resilience.

- **Chaos Dashboard**  
  A web-based UI for creating, managing, and visualizing chaos experiments and workflows.

- **Prometheus** (optional)  
  An open-source monitoring and alerting toolkit used to collect metrics from the application during chaos experiments.

- **Grafana** (optional)  
  A visualization tool often paired with Prometheus to monitor the effects of chaos injections in real time.


## 3. Case study concept description

The goal of this case study is to demonstrate the practical usage of Chaos Mesh in a Kubernetes environment to simulate various fault scenarios, evaluate system resilience, and observe the behavior of microservices under stress. We aim to showcase how chaos engineering principles can be applied to a real-world application — in this case, the *Online Boutique* microservices demo — while collecting and visualizing telemetry data using observability tools such as Prometheus and Grafana.

This proof of concept focuses on running controlled chaos experiments across four distinct fault categories provided by Chaos Mesh:

### Test Scenarios

1. **Pod Fault Injection**
   - **Objective:** Simulate the crash or eviction of critical pods to test how the system recovers and how quickly Kubernetes reschedules the affected pods.
   - **Example:** Terminate the `checkoutservice` or `frontend` pod and monitor user experience, recovery time, and service availability.
   - **Expected Outcome:** Kubernetes should reschedule the pod. Dashboards should show brief unavailability or latency spikes.

2. **Network Fault Injection**
   - **Objective:** Introduce artificial network issues such as latency, packet loss, or partitioning to observe the impact on inter-service communication.
   - **Example:** Add 1000ms latency between `frontend` and `productcatalogservice`.
   - **Expected Outcome:** Increase in response times, potential timeouts, visible in Grafana panels showing request durations and error rates.

3. **Stress Testing (CPU & Memory)**
   - **Objective:** Apply high CPU or memory load to simulate resource exhaustion and observe system degradation or auto-scaling responses.
   - **Example:** Apply CPU stress on the `recommendationservice` to consume all available cores for 60 seconds.
   - **Expected Outcome:** Service latency increases, possible throttling, potential pod restarts. Metrics should indicate high CPU usage and degraded performance.

4. **HTTP Fault Injection**
   - **Objective:** Introduce artificial HTTP response delays or errors to verify error-handling logic and system robustness.
   - **Example:** Inject 500ms delay and 10% HTTP 500 responses to `adservice`.
   - **Expected Outcome:** Increased error rate and degraded user experience; dashboards reflect anomalies in HTTP metrics (e.g., 5xx rate spike).

### Observability and Monitoring

Each test will be run with telemetry data collection enabled. Prometheus will scrape metrics from the Online Boutique services and Chaos Mesh itself. These metrics will be visualized in Grafana dashboards, focusing on:
- Request/response latency
- CPU/memory usage
- Pod availability status
- Error rates
- Service-level indicators (SLIs)

The main objective is to analyze how each fault scenario affects the performance and stability of the system and how quickly the system returns to normal operation. This approach highlights the importance of chaos engineering in identifying potential weaknesses before they occur in production.


## 4. Solution architecture

### 4.1 General Architecture
[![General architecture](/docs/img/otel.png)](/docs/img/otel.png)

The solution is designed to run in a **local Kubernetes cluster**, provisioned using **Minikube**, and integrates various components including microservices, observability tooling, and fault injection infrastructure. This architecture emulates a production-grade environment on a local machine for the purpose of experimentation, validation, and educational demonstration.

### 4.2 Architecture Overview

The core components of the architecture include:

* **Minikube** — Local Kubernetes environment simulating a production-like cluster.
* **Online Boutique** — A cloud-native microservices demo application consisting of 11 interconnected services.
* **Chaos Mesh** — A chaos engineering toolset used to inject controlled faults into the system.
* **Prometheus** — A metrics collection and time-series storage system.
* **Grafana** — A visualization and dashboarding tool for Prometheus data.
* **Load Generator** — A component of Online Boutique that simulates realistic traffic patterns by generating HTTP requests to the `frontend` service.

### 4.3 Component Roles and Interactions

#### **Minikube (Kubernetes Environment)**

* Provides a local container-orchestrated environment.
* Hosts all application workloads including Online Boutique, Chaos Mesh, and observability tools.
* Enables testing of service orchestration, fault tolerance, and pod lifecycle management.

#### **Chaos Mesh**

* Deployed into the Minikube cluster using Helm.
* Includes a dashboard for visual scenario creation and a set of CRDs and controllers for fault injection.
* Targets specific pods or services within Online Boutique for chaos experiments (e.g., network delays, pod terminations, CPU stress).

#### **Prometheus**

* Automatically scrapes metrics from Online Boutique services and Chaos Mesh components.
* Stores time-series metrics for resource usage, HTTP/gRPC performance, error rates, etc.
* Also collects Kubernetes-specific metrics (e.g., pod restarts, CPU usage).

#### **Grafana**

* Connects to Prometheus as a data source.
* Visualizes key metrics across services and the cluster.
* Dashboards include:

  * Service-level latency and error rates
  * CPU and memory consumption
  * Pod availability and restart frequency
  * Specific Chaos Mesh experiment timelines and impact

#### **Load Generator**

* Continuously sends requests to the `frontend` service, simulating real user interactions.
* Ensures that there is live traffic during chaos scenarios to observe system degradation and recovery.

#### **Online Boutique Microservices**

* Consists of 11 microservices that represent a simplified e-commerce platform.
* Each service communicates primarily over **gRPC**, exposing realistic network traffic and dependencies.
* The `frontend` service is the entry point for users and the target for load generation.

[![Architecture of
microservices](/docs/img/architecture-diagram.png)](/docs/img/architecture-diagram.png)


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
| [loadgenerator](/src/loadgenerator)                 | Python/Locust | Continuously sends requests imitating realistic user shopping flows to the frontend.   

## 5. Environment configuration description

### Purpose of Using Minikube

Minikube is a local Kubernetes distribution designed for development and testing purposes. It allows users to create lightweight Kubernetes clusters directly on a local machine. In this project, Minikube was chosen to host a simulated microservices-based application and provide an isolated, reproducible testing ground for failure injection using Chaos Mesh.

Its main role is to:

- Simplify deployment and orchestration of multiple containerized services.
- Provide a local platform to simulate realistic production-like conditions.
- Enable testing of fault tolerance strategies without external infrastructure dependencies.

### Role of Microservices-Based Application

To study system robustness under adverse conditions, a realistic application architecture is required. The **Google Cloud Online Boutique** demo — a cloud-native microservices application — was used as the system under test. It simulates an online store composed of multiple interdependent services, including frontend, product catalog, cart, checkout, and payment services.

The purpose of this application in the environment is to:

- Represent a complex, stateful, and realistic production workload.
- Demonstrate how cascading failures and bottlenecks occur between services.
- Serve as a testbed for policy learning under system stress (e.g., simulating partial lockdowns through resource constraints or failures).

### Use of Chaos Mesh

**Chaos Mesh** is a powerful chaos engineering platform designed to introduce failures into Kubernetes-based systems. It enables the simulation of various fault types (e.g., pod failure, network delay, disk I/O error) to evaluate the resilience and recovery behavior of applications.

In this setup, Chaos Mesh is used to:

- Inject controlled failures into specific microservices.
- Test the environment's fault tolerance and self-healing properties.
- Provide a basis for reinforcement learning agents to observe state transitions resulting from system perturbations.

Pod failure scenarios were used to emulate real-world crashes, service restarts, or node outages. These experiments helped evaluate how well the system recovers and whether DQN-based policies can anticipate and mitigate such disruptions.

### Role of Istio

**Istio** is a service mesh platform that provides traffic control, observability, and security between microservices. By intercepting and managing all service-to-service communication, Istio allows fine-grained control over network behavior, making it ideal for chaos engineering and monitoring.

In this project, Istio was employed to:

- Monitor and collect metrics (e.g., request latency, error rate) across services.
- Automatically inject sidecars (Envoy proxies) for advanced observability.
- Facilitate tracing and visualization of traffic flow during and after failure events.

Its integration ensures that policy decisions made by the reinforcement learning agent can be informed by precise system-level feedback.

### Monitoring Stack: Prometheus and Grafana

To assess the impact of chaos experiments and evaluate learned policies, detailed metrics are required. **Prometheus** was used as the metrics collection system, and **Grafana** provided real-time dashboards for visualization.

These tools were used to:

- Collect quantitative data about system health, resource usage, and failure recovery.
- Visualize key metrics that serve as reward signals for the DQN agent.
- Enable manual inspection of system behavior during experiments.

Together, they form the observability layer of the environment, providing insight into how each failure impacts the system and how policies affect stability.

### Summary

The environment configuration combines Minikube (for local orchestration), Chaos Mesh (for failure injection), Istio (for service control), and Prometheus/Grafana (for monitoring). The Google microservices demo provides a realistic workload. This setup enables safe, repeatable experiments to test the impact of failures and explore how Deep Q-Learning can optimize policy responses in dynamic and uncertain environments.


This section describes the setup and configuration steps taken to prepare the experimental environment for Chaos Mesh experiments.

## 6. Tools installation

### Minikube Setup

To simulate a local Kubernetes environment, **Minikube** was installed and configured using the following steps:

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube start
eval $(minikube docker-env)
```

These steps enabled the use of Minikube's internal Docker daemon, allowing built images to be used directly by Kubernetes without pushing them to an external registry.

### Helm setup

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

### Kubectl setup

```bash
curl -LO https://dl.k8s.io/release/v1.33.0/bin/linux/amd64/kubectl
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

### Chaos Mesh Installation

To simulate failures, Chaos Mesh was installed using Helm:
```bash
helm repo add chaos-mesh https://charts.chaos-mesh.org
```

## 7. How to reproduce - step by step

### Start minikube cluster
```bash
minikube start --cpus=6 --memory=8192
```

### Deployment of example webservice with loadgenerator -- opentelemetry demo

```bash
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
helm install my-otel-demo open-telemetry/opentelemetry-demo
kubectl -n default port-forward svc/frontend-proxy 8080:8080
```

The following services are available at these paths after the frontend-proxy service is exposed with port forwarding:
| Name               | URL                             |
|--------------------|---------------------------------|
| Webstore           | http://localhost:8080/          |
| Jaeger UI          | http://localhost:8080/jaeger/ui/|
| Grafana            | http://localhost:8080/grafana/  |
| Load Generator UI  | http://localhost:8080/loadgen/  |
| Feature Flags UI   | http://localhost:8080/feature/  |

### Create a dedicated namespace and install Chaos Mesh:
```bash
kubectl create ns chaos-mesh
helm install chaos-mesh chaos-mesh/chaos-mesh -n=chaos-mesh --set dashboard.securityMode=false --version 2.7.2
kubectl port-forward -n chaos-mesh chaos-dashboard-6465b58bcc-vj4zt 8888:2333
```
`securityMode=false` was used to disable the safe mode.

Chaos-dashboard is accessible at: http://localhost:8888/

### 7.1 Infrastructure as Code approach

## 8. Demo deployment steps

### 8.1 Configuration set-up

### 8.2 Data preparation

### 8.3 Execution procedure

### 8.4 Results presentation

## 9. Using AI in the project

## 10. Summary – conclusions

## 11. References

- [Chaos Mesh Overview - official documentation](https://chaos-mesh.org/docs/)
- [Hello Minikube - minikube tutorial](https://kubernetes.io/docs/tutorials/hello-minikube/)
- [Helm documentation](https://helm.sh/docs/)
- [Opentelemetry-Demo](https://github.com/open-telemetry/opentelemetry-demo)
- [OpenTelemetry - Getting Started](https://opentelemetry.io/docs/platforms/kubernetes/getting-started)
- [Simple OpenTelemetry setup in a Kubernetes environment](https://medium.com/incerto-technologies/simple-opentelemetry-setup-in-a-kubernetes-environment-aa7dc3e3fcf9)
- [Set up OpenTelemetry Collector for Application Observability](https://grafana.com/docs/grafana-cloud/monitor-applications/application-observability/collector/opentelemetry-collector/)
