# fluxcd-ghcr-app

FluxCD middle layer test wrapper for deploying my ghcr-test app. This is the middle link in a proper GitOps chain.
It sits between AppDev delivered container image, and the real cluster IaC.
It comprises the general-purpose application definitions, that would then get customized for specific environments such as prod or dev.

## How it works

This repo is read-only from Flux's github-access perspective.
It is a complete, self-contained app definition: 

`namespace.yaml`, `deployment.yaml`, `service.yaml`, tied
together by `kustomization.yaml`. 

It's fully functional on its own: you *can* point a 
`flux create kustomization ... --source=GitRepository/...` directly at
this repo and it will deploy and run something.

HOWEVER. It uses a placeholder hardcoded tag.

In a production situation, you would probably want to use the latest build. This can be done 
by pulling this in with an override vaguely like the following:

```yaml
spec:
  .....
  sourceRef:
    kind: GitRepository
    name: fluxcd-ghcr-app
  imagesjj
    - name: ghcr.io/ppbrown/ghcr-test
      newTag: "14" # {"$imagepolicy": "flux-system:ghcr-app:tag"}
```

The $imagepolicy magic can automatically write updates to change "14" as needed, although you would probably want a better
placeholder.

For the full Enterprise-grade flux auto-updating loader code that does this, see

https://github.com/ppbrown/fluxcd-starterkit/blob/master/prod/ghcr-app/imagepolicy.yaml

## Why a middle layer?

Why not just put the application IaC code all in at the flux repo layer? I would say two primary reasons:

1. Cleaner modularity. As your app setup gets more complex, it can be nice to have it seperate from the top level Flux control layers.

2. Cleaner git logs. If you have auto-pull of newer versions of apps, that goes into the repo. Which gets a commit log by the flux bot. Which then makes kind of a mess if you want to do a simple review of changes for a particular app's config.  If this is your primary goal, you might pull in all of your "middle layer" definitions into a single repo if you wanted. But single-repo-per-app makes it easier to do open source modular sharing as well.
