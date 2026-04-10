# kagent Software Templates

> **Warning:** This is a proof-of-concept (PoC) repository. It is not production-ready and is intended for experimentation and development purposes only.

Backstage software templates for creating [kagent](https://kagent.dev)-based AI agents on Kubernetes, designed for use with [Red Hat Developer Hub (RHDH)](https://developers.redhat.com/rhdh).

## Overview

This repository provides a single Backstage template with two variations that scaffold kagent agent resources managed via GitOps (ArgoCD).

[kagent](https://kagent.dev) is a CNCF sandbox project that brings agentic AI to Kubernetes using custom resources. Agents are defined as Kubernetes CRDs and expose the [A2A protocol](https://a2aproject.github.io/A2A/) for interoperability.

## Template Variations

### Agent

Creates a **GitOps repository only** with the kagent Kubernetes resources needed to run a declarative agent:

- `Agent` CRD (`kagent.dev/v1alpha2`) — defines the agent's system prompt, tools, and A2A skills
- `ModelConfig` CRD — configures the LLM provider (OpenAI, Anthropic, Azure, Ollama)
- Kustomize manifests managed by ArgoCD

No source code repository or build pipeline is involved. The agent runs entirely within the kagent controller using declarative YAML.

### BYO Agent

Connects an **existing agent repository** to a new **GitOps repository**. Use this when you already have a Python A2A-compatible agent and want to deploy it on a cluster managed by kagent:

- `Agent` CRD (`type: External`) — points to the deployed agent service
- `Deployment`, `Service`, `Route` — runs the agent container
- Tekton `Repository` resources linking to your existing source repo
- Kustomize manifests managed by ArgoCD

## Prerequisites

### Both variations

- kagent installed on your cluster: `kubectl get crd agents.kagent.dev`
- ArgoCD instance configured in RHDH
- A Kubernetes namespace for deployment (default: `kagent`)

### Agent variation only

- A Kubernetes `Secret` pre-created in the target namespace containing your LLM provider API key

### BYO Agent variation only

- An existing A2A-compatible agent repository
- A container image built from your repo and pushed to an accessible registry

## Usage

### Adding the template to RHDH

Import `all.yaml` as a catalog location in your RHDH instance:

```yaml
# In your app-config.yaml catalog section
catalog:
  locations:
    - type: url
      target: https://<your-host>/path/to/all.yaml
```

Or register it directly via the RHDH UI: **Catalog → Register Existing Component** → paste the URL to `all.yaml`.

### Running the template

1. Navigate to **Create** in RHDH
2. Find the **kagent Agent** template
3. Select a variation: **Agent** or **BYO Agent**
4. Fill in the wizard fields
5. The template creates a GitOps repository and registers it in the RHDH catalog

### Invoking a deployed agent

```bash
# Via kagent CLI
kagent invoke --agent <name> --task "your task here"

# Via A2A endpoint (requires port-forward)
kubectl port-forward svc/kagent-controller 8083:8083 -n kagent
curl http://localhost:8083/api/a2a/<namespace>/<name>/.well-known/agent.json
```

## Related Projects

- [kagent](https://kagent.dev) — Kubernetes-native AI agents
- [A2A Protocol](https://a2aproject.github.io/A2A/) — Agent-to-Agent interoperability
- [ai-lab-template](https://github.com/redhat-ai-dev/ai-lab-template) — RHDH templates for AI applications (referenced as a submodule)
