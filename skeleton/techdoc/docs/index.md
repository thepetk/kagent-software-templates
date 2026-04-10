# **Introduction**

Congratulations! You deployed a kagent agent from the kagent Software Template. The template has created a GitOps deployment repository to manage your agent's Kubernetes resources.

{%- if values.byoAgent %}

A source code repository was also created for your BYO agent implementation.

!!! info

    **Source Code Repository:** [${{ values.srcRepoURL }}](${{ values.srcRepoURL }})

    **GitOps Repository:** [${{ values.repoURL }}](${{ values.repoURL }})

{%- else %}

!!! info

    **GitOps Repository:** [${{ values.repoURL }}](${{ values.repoURL }})

{%- endif %}

# **Invoking the Agent**

Once deployed, you can invoke your agent using the kagent CLI:

```bash
kagent invoke --agent ${{ values.name }} --task "your task here"
```

Or via the A2A protocol endpoint:

```bash
# Check agent card
curl http://localhost:8083/api/a2a/${{ values.namespace }}/${{ values.name }}/.well-known/agent.json

# Enable port-forwarding first
kubectl port-forward svc/kagent-controller 8083:8083 -n kagent
```

You can also use the kagent dashboard:

```bash
kagent dashboard
```

# **Deployment Information**

All Kubernetes resources were deployed into the `${{ values.namespace }}` namespace. You can view them via:

```bash
kubectl get agents -n ${{ values.namespace }}
kubectl get modelconfigs -n ${{ values.namespace }}
```

!!! tip

    You can also login to your ArgoCD server to see the Argo view for deployed resources!
