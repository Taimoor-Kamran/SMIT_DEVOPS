# Class 1: Containers vs VMs, Images, and Layers

---

## Table of Contents
1. [What is Virtualization?](#what-is-virtualization)
2. [Virtual Machines (VMs)](#virtual-machines-vms)
3. [Containers](#containers)
4. [Containers vs VMs — Side by Side](#containers-vs-vms--side-by-side)
5. [What is a Docker Image?](#what-is-a-docker-image)
6. [Image Layers](#image-layers)
7. [Quick Reference](#quick-reference)

---

## What is Virtualization?

**Virtualization** means running one or more "fake" computers inside your real computer.
Your physical computer has CPU, RAM, and a hard disk.
Virtualization lets you **share** those resources across many isolated environments at the same time.

There are **two main technologies** that do this: **Virtual Machines** and **Containers**.

**Simple analogy:**
- A **VM** is like renting a **separate apartment** — your own kitchen, bathroom, walls, everything.
- A **Container** is like renting a **room in a shared flat** — you share the kitchen and plumbing, but your room is private.

---

## Virtual Machines (VMs)

A **Virtual Machine** is a complete computer that runs inside software called a **hypervisor**.
It has its own operating system, its own kernel, its own virtual hard disk, and its own virtual CPU and RAM.

**How a VM stack looks:**

```
┌─────────────────────────────────────┐
│         Your Application            │
├─────────────────────────────────────┤
│       Guest OS (e.g. Ubuntu)        │  ← full OS inside the VM
├─────────────────────────────────────┤
│            Hypervisor               │  ← manages VMs (VMware, VirtualBox, KVM)
├─────────────────────────────────────┤
│        Host OS (your laptop OS)     │
├─────────────────────────────────────┤
│       Physical Hardware             │  ← CPU, RAM, Disk
└─────────────────────────────────────┘
```

### What is a Hypervisor?

A **hypervisor** is the software that creates and manages VMs.
It sits between the physical hardware and the VMs, sharing resources.

| Hypervisor | Type | Example tools |
|------------|------|--------------|
| Type 1 (bare-metal) | Runs directly on hardware — no host OS needed | VMware ESXi, Microsoft Hyper-V, KVM |
| Type 2 (hosted) | Runs inside a host OS (like an app) | VirtualBox, VMware Workstation, Parallels |

### Pros and Cons of VMs

| Pros | Cons |
|------|------|
| Complete isolation — one VM crashing does not affect others | Slow to start (1-2 minutes to boot) |
| Can run a different OS than the host (e.g. Windows on Linux) | Uses lots of RAM and disk (each VM needs its own OS) |
| Very secure — strong separation between VMs | Takes minutes to create and configure |
| Great for running untrusted code | Overkill for small apps — wasteful |

---

## Containers

A **container** is a lightweight, isolated environment that runs your application and its dependencies — but **shares the host OS kernel** instead of having its own.

Think of a container as a **box** that contains:
- Your application code
- All libraries and packages it needs
- Configuration files
- Environment variables

But it does **NOT** contain: a full operating system or a kernel.

**How a Container stack looks:**

```
┌──────────────┐  ┌──────────────┐  ┌──────────────┐
│  Container 1 │  │  Container 2 │  │  Container 3 │
│  (Node app)  │  │  (Python app)│  │  (MySQL)     │
├──────────────┴──┴──────────────┴──┴──────────────┤
│              Container Runtime (Docker)           │  ← manages containers
├───────────────────────────────────────────────────┤
│              Host OS Kernel                       │  ← SHARED by all containers
├───────────────────────────────────────────────────┤
│              Physical Hardware                    │
└───────────────────────────────────────────────────┘
```

> **Key insight:** Because containers share the OS kernel, they are much lighter and faster than VMs.
> You can start a container in **milliseconds** vs minutes for a VM.

### Pros and Cons of Containers

| Pros | Cons |
|------|------|
| Extremely fast to start (milliseconds) | Less isolation than VMs — containers share the kernel |
| Very lightweight — uses very little RAM and disk | All containers must use the same OS type as the host |
| Easy to scale — spin up 100 containers in seconds | Security vulnerabilities in the kernel affect all containers |
| Consistent: "works on my machine" problem is solved | Newer technology — more complex to learn at first |
| Perfect for microservices and CI/CD pipelines | |

---

## Containers vs VMs — Side by Side

| Feature | Virtual Machine | Container |
|---------|----------------|-----------|
| OS | Full OS per VM (GB each) | Shares host OS kernel |
| Size | Gigabytes | Megabytes |
| Startup time | 1–5 minutes | Milliseconds |
| Performance | Slower (overhead from full OS) | Near-native speed |
| Isolation | Very strong (separate kernel) | Process-level isolation |
| Portability | Can be slow to move (large files) | Very portable (small image) |
| Resource usage | High (RAM, CPU, disk) | Very low |
| Best for | Full OS isolation, legacy apps | Microservices, CI/CD, cloud apps |
| Examples | VirtualBox, VMware, KVM | Docker, Podman, containerd |

### When to use a VM vs a Container?

**Use a VM when:**
- You need to run a **completely different OS** (e.g. Windows app on a Linux server)
- You need **maximum isolation** (e.g. security testing, untrusted code)
- You are running **legacy applications** that need a full OS environment

**Use a Container when:**
- You are deploying a **web app, API, or microservice**
- You want **fast deployments** and **easy scaling**
- You are using **CI/CD pipelines** (build, test, deploy)
- You want the app to behave the **same way** on every machine

---

## What is a Docker Image?
