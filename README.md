# fluxcd-ghcr-app
Flux wrapper for autodeploy of my ghcr-test app

## How it works

This repo is read-only from Flux's perspective and is a complete, self-contained
app definition: `namespace.yaml`, `deployment.yaml`, `service.yaml`, tied
together by `kustomization.yaml`. It's fully functional on its own: you can
point a `flux create kustomization ... --source=GitRepository/...` directly at
this repo and it will deploy and run

In a production situation, you would pull it in with an override vaguely like the following:



```yaml
spec:
  .....
  sourceRef:
    kind: GitRepository
    name: fluxcd-ghcr-app
  images:
    - name: ghcr.io/ppbrown/ghcr-test
      newTag: "14" # {"$imagepolicy": "flux-system:ghcr-app:tag"}
```

The $imagepolicy magic can automatically write updates to change "14" as needed, although you would probably want a better
placeholder.

For the full flux auto-loader code, see

https://github.com/ppbrown/fluxcd-starterkit/blob/master/prod/ghcr-app/imagepolicy.yaml

