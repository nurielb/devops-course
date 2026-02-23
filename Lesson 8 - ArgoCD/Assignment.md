# Assignment: Build a CI Pipeline with GitHub Actions

## GitHub repository link
GitOps Repository (Helm): https://github.com/nurielb/final-project-helm/branches
Application Repository: https://github.com/nurielb/final-project

## Screenshots
ArgoCD Apps:
![Photo](<Screenshots/argocd-apps.png>)

Kubectl Pods:
![Photo](<Screenshots/kubectl-get-pods.png>)

## Explanations

### How ArgoCD connects to GitHub

1. You create an Application in ArgoCD with predefined:
    - git repository url
    - git credentials (stored as k8s secret in cluster)
    - target branch
    - directory path (containing the helm chart to be monitored)ß
    - target helm values file
2. ArgoCD clones the git repository and checks out the desired branch.
Then it periodically pulls and detects diffs.

### How Helm rendering works in ArgoCD
If application source type = Helm
ArgoCD periodically pulls Git to compute the desired state, and continuously compares that desired state to the live cluster. It can sync when Git changes or when the cluster drifts.”
When a drift is detected, it applies the new desired state directly with k8s api.
It does not apply the template using helm. This happens so git stay the single source of truth regarding the cluster's state.
If Helm applied it, Helm would be the one to track and manage the releases, and not ArgoCD (not git).
So ArgoCD uses Helm's templating to apply the desired state by itself, not by using Helm.

### How Reconciliation Works
ArgoCD constantly compares the desired state vs the current cluster's state:
1. It creates manifests from git (when source is helm, then it generates templates using helm)
2. It queries the k8s api
3. Compares desired objects vs live objects in the cluster
4. If anything is different, the application is marked as `Out Of Sync`
5. Then a sync is needed, either automatically if enabled or manually when it is wanted