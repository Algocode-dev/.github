# AlgoCode — Distributed Online Judge Platform

> A production-grade, microservices-based online judge built from scratch — think LeetCode architecture, built by one engineer as a deep dive into distributed systems.

---

## What Is AlgoCode?

AlgoCode is a coding problem and submission evaluation platform built as a distributed system. Users browse problems, submit code solutions, and receive verdicts — all handled asynchronously across four independent microservices.

No monolith. No shortcuts. Every service owns its domain, communicates through queues, and fails independently.

---

## System Architecture

```
                         ┌─────────────────────┐
                         │       Client         │
                         └──────────┬──────────┘
                                    │
                                    ▼
                    ┌───────────────────────────────┐
                    │          API Gateway           │
                    │   JWT Auth · RBAC · Routing    │
                    └────┬──────────────────┬────────┘
                         │                  │
              ┌──────────┘                  └──────────┐
              ▼                                         ▼
  ┌─────────────────────┐               ┌─────────────────────────┐
  │   Problem Service   │               │    Redis / BullMQ Queue  │
  │  MongoDB · REST API │               │    (submission pipeline) │
  └─────────────────────┘               └────────────┬────────────┘
                                                      │
                                                      ▼
                                        ┌─────────────────────────┐
                                        │    Evaluator Service     │
                                        │  Docker Sandbox · BullMQ │
                                        │  TypeScript · Verdicts   │
                                        └────────────┬────────────┘
                                                      │
                                                      ▼
                                        ┌─────────────────────────┐
                                        │  Notification Service    │
                                        │  BullMQ · Nodemailer    │
                                        │  Async result delivery  │
                                        └─────────────────────────┘
```

---

## The Four Services

### 🔐 [API Gateway](https://github.com/Algocode-dev/API_Gateway)
Single entry point for all client traffic. Handles JWT authentication, role-based access control (RBAC), and request routing. No downstream service is ever exposed directly.

**Stack:** Node.js · Express · MySQL · Sequelize · JWT · bcrypt · Winston

---

### 📋 [Problem Service](https://github.com/Algocode-dev/AlgoCode-problem-service)
REST API for managing coding problems, test cases, and constraints. Uses MongoDB's document model to store problems and their embedded test cases as a single unit — making evaluator reads fast with no joins.

**Stack:** Node.js · Express · MongoDB · Mongoose · Winston · Jest

---

### ⚙️ [Evaluator Service](https://github.com/Algocode-dev/Algocode-Evaluator_Service)
The execution core. Consumes submission jobs from a BullMQ queue, runs user code in isolated Docker containers with enforced CPU and memory limits, compares output against test cases, and produces a structured verdict. No user code ever runs on the host.

**Stack:** TypeScript · Node.js · BullMQ · Redis · Docker

---

### 🔔 [Notification Service](https://github.com/Algocode-dev/Notification-service)
Consumes verdict results from the BullMQ results queue and delivers submission outcome notifications to users. Fully decoupled from the execution pipeline — failures here never affect verdict recording.

**Stack:** Node.js · BullMQ · Redis · Nodemailer · Winston

---

## Key Engineering Concepts Demonstrated

| Concept | Where |
|---|---|
| Microservices with domain separation | All 4 services |
| Async job processing with BullMQ + Redis | Evaluator · Notification |
| OS-level sandboxing with Docker | Evaluator Service |
| Stateless JWT auth + RBAC | API Gateway |
| Repository pattern (Controller → Service → Repository) | Gateway · Problem Service |
| Document modelling with embedded subdocuments | Problem Service (MongoDB) |
| Dead-letter queues and retry logic | Notification Service |
| Structured logging with Winston | All 4 services |
| Integration testing with seeded fixtures | Problem Service |

---

## Tech Stack Overview

![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=nodedotjs&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat&logo=typescript&logoColor=white)
![Express](https://img.shields.io/badge/Express-000000?style=flat&logo=express&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![Redis](https://img.shields.io/badge/Redis-DC382D?style=flat&logo=redis&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat&logo=mongodb&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat&logo=mysql&logoColor=white)
![BullMQ](https://img.shields.io/badge/BullMQ-FF0000?style=flat&logo=bull&logoColor=white)

---

## Built By

**Dhruv Gupta** — Backend Engineer  
[GitHub](https://github.com/Dhruv7850) · [LinkedIn](https://linkedin.com/in/your-profile) · [LeetCode](https://leetcode.com/your-handle)

> Built as a self-directed deep dive into distributed systems, async job processing, and secure code execution infrastructure.
