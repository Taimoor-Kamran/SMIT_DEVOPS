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

A **Docker image** is a read-only template that contains everything needed to run your application:
the code, the runtime, the libraries, the config files — packaged into one file.

**Image vs Container — what is the difference?**

| | Image | Container |
|---|---|---|
| What it is | A blueprint / recipe | A running instance of the image |
| State | Read-only, never changes | Has a writable layer on top |
| Analogy | A **recipe** for a cake | The **actual cake** you baked |
| Created by | `docker build` | `docker run` |

You can run **many containers** from the **same image** — just like you can bake many cakes from one recipe.

```
nginx image  →  container 1 (port 8080)
             →  container 2 (port 8081)
             →  container 3 (port 8082)
```

### Where do images come from?

Images are stored in a **registry** — a central place to upload and download images.

| Registry | Description |
|----------|-------------|
| Docker Hub | The default public registry — millions of free images |
| GitHub Container Registry (ghcr.io) | GitHub's registry for your own images |
| Amazon ECR | AWS's private registry for production |
| Google Artifact Registry | GCP's registry |
| Your own private registry | Can self-host with Docker Registry |

**How to get an image from Docker Hub:**

```bash
docker pull nginx           # downloads the latest nginx image
docker pull ubuntu:22.04    # downloads Ubuntu 22.04 image
docker pull node:20-alpine  # downloads Node.js 20 on Alpine Linux (tiny)
```

**How to see images you have downloaded:**

```bash
docker images
```

Output:
```
REPOSITORY   TAG        IMAGE ID       SIZE
nginx        latest     abc123def456   187MB
ubuntu       22.04      def456abc789   77MB
node         20-alpine  fed123cba456   130MB
```

---

## Image Layers

A Docker image is not a single file — it is built from **multiple layers** stacked on top of each other.
Each layer represents one instruction in the Dockerfile.

**Think of layers like a stack of transparent slides:**
Each slide adds something new on top. The final image is all the slides combined.

```
Layer 4:  COPY app.py /app/          ← your application code
Layer 3:  RUN pip install flask       ← install Python packages
Layer 2:  RUN apt-get install python3 ← install Python
Layer 1:  FROM ubuntu:22.04           ← start with Ubuntu base
```

### Why are layers important?

Layers are **cached** by Docker. If you rebuild the image and only changed your app code (Layer 4),
Docker reuses Layers 1, 2, and 3 from the cache — it only rebuilds Layer 4.
This makes builds **much faster**.

```
First build:     Layer 1 ✅ built  →  Layer 2 ✅ built  →  Layer 3 ✅ built  →  Layer 4 ✅ built
Second build:    Layer 1 ⚡ cached →  Layer 2 ⚡ cached →  Layer 3 ⚡ cached →  Layer 4 ✅ rebuilt
```

### Layers are also SHARED between images

If two images both use `ubuntu:22.04` as their base, Docker stores that layer **once** on disk.
Both images share it — this saves a huge amount of disk space.

```
Image A:  ubuntu:22.04 (shared) + python + flask app
Image B:  ubuntu:22.04 (shared) + node + express app
            ↑
       same layer, stored once on disk
```

### The Writable Container Layer

When you **run** a container from an image, Docker adds one more layer on top — a **writable layer**.

```
┌──────────────────────────────────────┐
│  Writable Layer (container runtime)  │  ← files you create/change while the container is running
├──────────────────────────────────────┤
│  Layer 4: COPY app.py                │  (read-only)
├──────────────────────────────────────┤
│  Layer 3: RUN pip install flask      │  (read-only)
├──────────────────────────────────────┤
│  Layer 2: RUN apt-get install python │  (read-only)
├──────────────────────────────────────┤
│  Layer 1: FROM ubuntu:22.04          │  (read-only)
└──────────────────────────────────────┘
```

> **Important:** When the container is deleted, the writable layer is lost.
> To save data permanently, use **Docker Volumes** (covered in a later class).

### How to inspect layers of an image

```bash
docker history nginx
```

Output (simplified):
```
IMAGE          CREATED        CREATED BY                              SIZE
abc123def456   2 weeks ago    CMD ["nginx" "-g" "daemon off;"]        0B
<missing>      2 weeks ago    COPY /etc/nginx /etc/nginx              4.61kB
<missing>      2 weeks ago    RUN apt-get install nginx               55.3MB
<missing>      2 weeks ago    FROM debian:bullseye-slim               80.5MB
```

Each row is one layer. You can see exactly what each layer added and how big it is.

### Best practices for image layers

| Practice | Why |
|----------|-----|
| Put rarely-changing layers first (e.g. `FROM`, `RUN apt-get`) | Maximise cache hits on rebuild |
| Put frequently-changing layers last (e.g. `COPY app.py`) | Only the last layer rebuilds each time |
| Combine `RUN` commands with `&&` | Fewer layers = smaller image |
| Use a small base image (`alpine` instead of `ubuntu`) | Reduces final image size dramatically |
| Clean up in the same `RUN` layer (`apt-get clean`) | Removing files in a later layer doesn't help — use same layer |

**Example — bad (many layers, bigger image):**
```dockerfile
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get clean
```

**Example — good (one layer, same cache benefit, smaller image):**
```dockerfile
RUN apt-get update && apt-get install -y curl && apt-get clean
```

---

## Quick Reference

### Containers vs VMs Cheat Sheet

| | VM | Container |
|--|--|--|
| OS | Full OS (GB) | Shared kernel (MB) |
| Start | Minutes | Milliseconds |
| Size | GB | MB |
| Isolation | Strong | Process-level |
| Tool | VirtualBox / VMware / KVM | Docker / Podman |

### Docker Image Commands Cheat Sheet

| Command | What it does |
|---------|-------------|
| `docker pull nginx` | Download nginx image from Docker Hub |
| `docker images` | List all images on your machine |
| `docker rmi nginx` | Delete an image |
| `docker build -t myapp .` | Build an image from a Dockerfile in current folder |
| `docker history nginx` | Show all layers of the nginx image |
| `docker inspect nginx` | Show full metadata of an image |

### Docker Container Commands Cheat Sheet

| Command | What it does |
|---------|-------------|
| `docker run nginx` | Run a container from the nginx image |
| `docker run -d nginx` | Run in background (detached) |
| `docker run -p 8080:80 nginx` | Map port 8080 on host to port 80 in container |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers (including stopped) |
| `docker stop <id>` | Stop a running container |
| `docker rm <id>` | Delete a stopped container |
| `docker logs <id>` | View container output logs |
| `docker exec -it <id> bash` | Open a terminal inside a running container |

### Key Terms Glossary

| Term | Meaning |
|------|---------|
| Hypervisor | Software that creates and manages VMs |
| Guest OS | The OS running inside a VM |
| Host OS | The OS on your physical machine |
| Image | Read-only template to create containers |
| Container | A running instance of an image |
| Layer | One step in building a Docker image |
| Registry | A server that stores and serves Docker images |
| Docker Hub | The default public registry for Docker images |
| Kernel | The core of the OS that manages hardware |
| `docker pull` | Download an image from a registry |
| `docker build` | Create an image from a Dockerfile |
| `docker run` | Start a container from an image |
