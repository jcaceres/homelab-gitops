# homelab-gitops
Homelab GitOps: ArgoCD / Argo Workflows on the `k3s-argo` cluster (3-node HA k3s, API VIP 192.168.30.70).

## Layout
- `bootstrap/argocd/values.yaml`: Helm values for the ArgoCD install (`~/Scripts/argocd-install.sh`)
- `bootstrap/root-app.yaml`: the app-of-apps root, applied once by hand
- `apps/`: one Application manifest per app; the root app syncs this directory
- `workloads/<app>/`: the manifests each Application deploys

## Bootstrap
```
kubectl --context argo apply -f bootstrap/root-app.yaml
```

## Practice exercises (nginx-test)
1. Drift: `kubectl --context argo -n nginx-test scale deploy nginx --replicas=5`, watch selfHeal revert it to 2.
2. Git change: edit `replicas` or the image tag in `workloads/nginx-test/nginx.yaml`, push, watch the sync.
3. Prune: delete the Service from the manifest, push, watch it get pruned.
4. Add a second app: drop a new manifest in `apps/`, push, watch the root app create it.
