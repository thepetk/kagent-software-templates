# ${{ values.name }} GitOps

This repository contains the GitOps manifests for the **${{ values.name }}** kagent agent.

## Resources

| Resource | Description |
|---|---|
| `Agent` | kagent Agent CRD defining the AI agent |
{%- if not values.byoAgent %}
| `ModelConfig` | LLM provider configuration for the agent |
{%- endif %}
{%- if values.byoAgent %}
| `Deployment` | The BYO Python agent container deployment |
| `Service` | Kubernetes Service exposing the agent |
| `Route` | OpenShift Route for external access |
{%- endif %}

## Deployment

This repository is managed by ArgoCD. Changes pushed to the `main` branch are automatically synced to the `${{ values.namespace }}` namespace.

## Invoking the Agent

Once deployed, you can invoke the agent via the kagent CLI:

```bash
kagent invoke --agent ${{ values.name }} --task "your task here"
```

Or via the A2A endpoint:

```bash
curl http://localhost:8083/api/a2a/${{ values.namespace }}/${{ values.name }}/.well-known/agent.json
```
