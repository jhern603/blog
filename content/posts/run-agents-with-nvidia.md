---
title: "NVIDIA OpenShell: Run Your AI Agents in a Sandbox"
date: 2026-04-10
description: "AI agents are powerful and dangerous. OpenShell is NVIDIA's answer to running them safely."
tags:
  - Agentic AI
  - security
  - devops
  - NVIDIA
  - Docker
categories:
  - AI Security
gradientWord: "NVIDIA OpenShell:"
draft: false
---

> *OpenShell is currently alpha software. APIs and behavior may change without notice.*
> Learn more about OpenShell by visiting [the Github Repo](https://github.com/NVIDIA/OpenShell)

AI agents read your files, execute shell commands, install packages, and make network requests autonomously, at machine speed. Most people are running agents directly on their workstations or servers, with the same access as a privileged user and almost no audit trail.

**NVIDIA's OpenShell** is a runtime layer that sits between your agent and your infrastructure, and I think it is a step in the right direction since it addresses **how agents should be run as a matter of course.**


## What Is OpenShell?

OpenShell is an open source runtime for executing autonomous AI agents inside isolated sandboxes. It is part of the NVIDIA Agent Toolkit and ships under the Apache 2.0 license.

The key architectural decision is **out-of-process policy enforcement.** Instead of relying on the agent's internal guardrails, or its system prompt, OpenShell enforces constraints on the *environment* the agent runs in. The agent cannot read, modify, or reason around the policy because the policy lives outside of the agent context entirely.

Openshell applies [the browser tab model](https://www.geeksforgeeks.org/techtips/how-do-web-browser-handle-tab-and-multiple-windows/) to agents. Each session is isolated. Permissions are verified by the runtime before any action executes.

A sandbox governs four things:
  1. What files the agent can access? 
  2. What network calls the agent make? 
  3. What processes can the agent run?
  4. Where is inference traffic routed?

The last point is what makes or breaks a sandboxed agent since it determines how the inference router can keep context on-device versus sending context to an outside provider like Claude or OpenAI.

Credentials are never written to the sandbox filesystem. They are injected as environment variables at creation time which the CLI auto-discovers from your shell environment.

Launching an agent only takes one command:

```bash
openshell sandbox create --from openclaw -- claude
```

OpenShell is agent-agnostic and the same policy applies regardless of which provider you use.


## Why Not Just Run Agents Directly?

When you run Claude Code or Codex directly on your machine, the agent inherits your full user context. It can read files such as `~/.aws/credentials`, ssh keys, and anything else accessible to your user. It can make outbound requests to any host. Its actions are only logged as part of your shell history.

Tools like Claude Code ship with internal guardrails. Those guardrails are not sufficient because it is the equivalent of telling a 3 year-old to not touch a hot stove. Curiosity *will* get the best of them.

This means that agents are subject to the same attack surface that these guardrails are trying to defend. A malicious string embedded in a document or a dependency comment can influence agent behavior through prompt injection.

OpenShell moves the control point outside the agent's reach entirely. A prompt injection can change what the agent *tries* to do. It cannot change what the runtime *allows* it to do.

My take: running agents without a sandboxed environment is the same mistake as running untrusted code without a container. The capability is real, the productivity gains are real, and the blast radius is also real.


## Benefits

The security model is the obvious headline, but a few operational benefits are worth calling out.

### Declarative, version-controlled policy.
Policies are YAML files that live alongside your code. You can review, diff, and audit them like any other configuration. OpenShell even has an agent skill that generates a policy from a plain-language description, though you should still review the output before using it.

### Agent-agnostic enforcement.
Replace Claude Code with Codex or switch from a frontier model to a local Ollama instance, the policies do not change. You maintain one security configuration for all agents.

### Full audit trail
Every allow and deny decision is logged. This matters for compliance and also for debugging when an agent is blocked from something it legitimately needs.

### Remote deployment
Sandboxes can be deployed to a remote gateway with no code changes:

```bash
openshell sandbox create --remote spark --from openclaw -- claude
```
This is useful for CI/CD pipelines where you want agent execution isolated from the runner itself.

## Drawbacks

OpenShell is alpha software. APIs may break. The project is currently single-player (one developer, one environment, one gateway.) Multi-tenant enterprise deployments are on the roadmap.

It requires Docker, does not support Windows as a host, and GPU passthrough for local inference is explicitly experimental.

Policy authoring requires you to understand what your agent actually needs, which directories it reads, which endpoints it calls, which binaries it runs.

None of these are reasons to avoid it. They are reasons to step back and do your homework before deploying a production system on top of it without understanding the reprecusions.

## In CI/CD Pipelines

An agent that runs in a reproducible, sandboxed environment with a version-controlled policy is an agent you can put in a pipeline with confidence.

The pattern is straightforward
  - Provision a sandbox with a scoped policy 
  - Run the agent
  - Route the output through a human approval gate before anything is merged or deployed.

For example, you can create a policy for a docs agent with the following constraints:
- Read access to the source and docs directories
- Outbound HTTPS to the LLM API only
- No write access outside the docs folder.

The agent cannot go further than that regardless of what it reasons about or what a prompt injection tells it to do.

For deployment pipelines, agents are useful for pre-deployment validation. The policy grants read access to the plan output and nothing else. No cloud provider API access, no infrastructure state writes.

Different pipeline stages warrant different policies. Because policies are files, you maintain one per stage and review changes to them in pull requests like any other configuration.

## For Non-Technical Users

OpenShell's interface is CLI-first, so there is not a GUI for this population to use, which incurs a cost to learn the technology, and triggers a sellback effect where these users will just go back to using agents without guardrails.

Using a managed OpenShell deployment, a marketing writer can get an agent with read access to the content repository and write access to a drafts folder. An ops analyst can get an agent wired to dashboards and reporting APIs, with all inference routed locally so source data stays on the network. The agent is the same. The policy defines the scope.

NVIDIA's **[NemoClaw](https://github.com/NVIDIA/NemoClaw)** takes this further for personal use. NemoClaw wraps OpenShell with pre-configured privacy policies and open models into a single-command deployment. You get a self-evolving personal agent with sensible defaults, without managing gateway configuration yourself.

The gap is real: anyone writing their own policies today needs to understand what their agent does at the process level. That will improve. The architecture for delegating configuration to a platform team or a reference stack is already there.