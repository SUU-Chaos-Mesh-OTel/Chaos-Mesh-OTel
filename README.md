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
6. [Installation method](#6-installation-method)  
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

## 6. Installation method

## 7. How to reproduce - step by step

### 7.1 Infrastructure as Code approach

## 8. Demo deployment steps

### 8.1 Configuration set-up

### 8.2 Data preparation

### 8.3 Execution procedure

### 8.4 Results presentation

## 9. Using AI in the project

## 10. Summary – conclusions

## 11. References
