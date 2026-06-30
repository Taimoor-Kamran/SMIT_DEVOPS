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
