---
title: "Blog 3"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# AWS Lambda MicroVMs — When Serverless Gets VM-Level Power

## 1. What is AWS Lambda MicroVMs?

AWS has just launched **AWS Lambda MicroVMs** — an entirely new serverless compute primitive, distinct from traditional Lambda Functions, that provides VM-level isolated sandboxes: no shared kernel, no shared resources between sessions. It's built on Firecracker — the very same virtualization technology that has powered over 15 trillion Lambda invocations every month.

In short: if Lambda Functions let you "run a piece of code and then it's gone," Lambda MicroVMs give you your own dedicated virtual machine — one that starts up almost instantly, can hold state for hours, yet remains fully serverless, with no server to manage.

## 2. The problem with traditional Lambda

Classic Lambda runs functions in a shared environment — fast, cheap, but not isolated enough for sensitive workloads or ones that need long-lived state. This created an awkward situation for a lot of teams: they were forced to pick one of two directions, even though the actual job was often simple — "run a piece of user- or AI-generated code, then stop":

- **Lambda Functions**: fast, cheap, no infrastructure to manage — but no persistent state, a hard 15-minute execution cap, and not deep enough isolation for sensitive use cases.
- **Containers or EC2**: better isolation, state can persist — but you have to manage the entire infrastructure lifecycle yourself: clusters, capacity, patching, networking... and you still pay even when there's no traffic (idle).

This tradeoff became even more pronounced as a wave of multi-tenant applications running user- or AI-generated code took off — coding assistants, AI agents, code execution sandboxes, data analytics platforms, and more. Each user or session needs its own isolated execution environment, so that one person's bug or malicious code can't affect another user running in parallel.

## 3. How Lambda MicroVMs solves this

Lambda MicroVMs provides a fully isolated sandbox at the VM level, with no shared kernel or resources between sessions. The mechanism is quite interesting, following an "image first, then launch" model:

1. **Create a MicroVM Image**: you package your application code together with a Dockerfile into a zip archive, upload it to Amazon S3, then call the Lambda API to create an image. Lambda runs that Dockerfile, initializes your application, and captures a snapshot of the full memory and disk state once the app is ready.
2. **Launch a MicroVM from that image**: whenever you need a new isolated environment — for a user session, a job, or a sandbox — you call `run-microvm`. Lambda launches the MicroVM from the pre-initialized snapshot instead of cold-booting from scratch, so startup is near-instant.
3. **Connect directly**: each MicroVM gets its own dedicated HTTPS endpoint, supporting popular protocols like HTTP/2, gRPC, and WebSockets — no load balancer needed, no networking to set up yourself.
4. **Suspend/Resume on demand**: once the user stops interacting, the MicroVM automatically suspends after a configurable idle window, preserving the full memory and disk state. When traffic returns, it resumes right where it left off — state is preserved for up to 8 hours.

The entire process doesn't require you to manually manage clusters, capacity, or a VM lifecycle the way you would running EC2 or containers yourself.

## 4. Why this is a big deal

Previously, when building an application that needed to hand each user or job its own environment, you almost always had to trade off between three things: **startup speed**, **level of isolation**, and **ability to retain state**. There was no option that gave you all three at once:

| | Lambda Functions | EC2 / ECS | Lambda MicroVMs |
|---|---|---|---|
| Startup speed | Very fast | Slower, self-managed | Near-instant (resumes from snapshot) |
| Isolation level | Shared resources | Strong isolation | VM-level, no shared kernel |
| State retention | None (15-min max per run) | Yes, but self-managed | Yes, up to 8 hours, auto suspend/resume |
| Infra management | None needed | Required (clusters, patching, capacity...) | None needed |
| Cost while idle | None (nothing runs) | Still incurred | Reduced via auto-suspend |

Lambda MicroVMs fills exactly that gap in the middle: you get VM-level isolation and state retention comparable to running your own EC2, while keeping the "don't worry about servers" experience that defines serverless.

Notably, this isn't an update to Lambda Functions — it's an **entirely new resource** within the Lambda ecosystem, with its own distinct API (`lambda-microvms`). Lambda Functions remain the right choice for short-lived, event-driven, request-response workloads, while Lambda MicroVMs is purpose-built for multi-tenant applications that need to hand each user or session its own long-lived, isolated execution environment. The two are complementary, not a replacement for one another.

## 5. Real-world use cases

- **AI agents with long-running sessions**: modern coding assistants and AI agents often need to run LLM-generated code iteratively while preserving context between iterations, and can rapidly spin up or tear down environments to test alternative code paths — for example, in reinforcement learning. Lambda MicroVMs fits this need directly.
- **User code execution sandboxes**: platforms like online judges, code playgrounds, or vulnerability scanners need strong isolation between sessions, the ability to scale quickly under bursts of concurrent requests, and sometimes elevated OS-level privileges.
- **CI/CD**: pipelines need ephemeral, isolated build and test environments that start quickly and can be discarded after each run — MicroVMs provides a dedicated isolated environment per pipeline stage, with no shared state between jobs.
- **Multi-tenant sensitive data processing**: data-processing tasks that must guarantee absolutely no information leakage between different tenants sharing the same platform.

## 6. Things worth noting

At launch, Lambda MicroVMs is available in five regions: US East (N. Virginia), US East (Ohio), US West (Oregon), Asia Pacific (Tokyo), and one Europe region. The current supported configuration is ARM64 architecture, with up to 16 vCPUs, 32 GB of memory, and 32 GB of disk per instance. The default base image is a minimal Amazon Linux 2023, which you can further customize through your own Dockerfile.

As for pricing, Lambda MicroVMs bills per second across several different dimensions (running compute resources, snapshot storage, and so on), so before moving to production it's worth reviewing the official AWS Lambda pricing page closely to estimate costs for your specific usage pattern — especially if you'll be running many concurrent sessions with long state-retention windows.

## 7. Closing thoughts

Lambda MicroVMs isn't a minor Lambda update — it's an answer to a problem a lot of teams have run into over the past few years: how do you give each user or job its own execution environment that's isolated enough, fast enough, without having to operate the infrastructure yourself? With the explosion of AI agents and applications running user- or AI-generated code, this is one of the more important developments to watch in AWS's serverless ecosystem going forward.


**Original Post (AWS Study Group):** https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206918726739754/?rdid=6SeKNuZnOr9PjQrn#

