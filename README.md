# kustomize-helm-demo

Kustomize layered on top of a Helm chart, deployed by Argo CD.

- `base/` renders the **podinfo** Helm chart (`helmCharts:` + `base/values.yaml`).
- `overlays/staging` and `overlays/prod` pull in the base and **patch the rendered
  output** — namespace, replicas, UI colour/message, memory, and (prod only) an
  extra `HorizontalPodAutoscaler`. Overlays do not re-run Helm.
- `kustomize build overlays/<env> --enable-helm` gives the final manifests;
  Argo CD runs the same build (`--enable-helm` is on in `argocd-cm`).

```
base/values.yaml ──► helm template podinfo ──► raw manifests
                                                     │
overlays/<env>/kustomization.yaml (ns + patches) ────┼──► kustomize transforms
overlays/prod/hpa.yaml ──────────────────────────────┘
                                                     ▼
                                       final YAML ──► kubectl / Argo CD
```
