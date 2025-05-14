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
3. [Case study concept description](#3-case-study-concept-description)  
4. [Solution architecture](#4-solution-architecture)  
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

## 3. Case study concept description

## 4. Solution architecture

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
