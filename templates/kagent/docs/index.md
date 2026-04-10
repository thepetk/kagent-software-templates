# kagent Agent Template

This template creates a kagent-based AI agent on Kubernetes, managed via GitOps (ArgoCD).

## Variations

### Agent (Simple / Declarative)

Creates a **GitOps repository only** containing:
- A `kagent.dev/v1alpha2` `Agent` resource (declarative type)
- A `ModelConfig` resource referencing your LLM API key Secret
- Kustomize manifests managed by ArgoCD

**Prerequisites:**
- kagent installed on your cluster (`kubectl get crd agents.kagent.dev`)
- A Kubernetes Secret containing your LLM provider API key pre-created in the target namespace
- ArgoCD instance configured in RHDH

### BYO Agent

Connects an **existing agent repository** to a new **GitOps repository** containing:
- A `kagent.dev/v1alpha2` `Agent` resource (external type) pointing to your deployed agent
- A `Deployment`, `Service`, and `Route` for the agent container
- Tekton Repository resources pointing to your existing source repo
- Kustomize manifests managed by ArgoCD

**Prerequisites:**
- All prerequisites from the Agent variation
- An existing agent source repository (A2A-compatible Python agent or similar)
- A container image built from your existing repo and pushed to a registry

## After Deployment

Invoke your agent using the kagent CLI:

```bash
kagent invoke --agent <name> --task "your task here"
```

Or check the A2A endpoint:

```bash
kubectl port-forward svc/kagent-controller 8083:8083 -n kagent
curl http://localhost:8083/api/a2a/<namespace>/<name>/.well-known/agent.json
```
