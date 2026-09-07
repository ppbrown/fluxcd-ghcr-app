# fluxcd-ghcr-app
Flux wrapper for autodeploy of my ghcr-test app

## How it works

This repo is self-contained: it holds both the workload (`namespace.yaml`,
`deployment.yaml`, `service.yaml`) and the Flux image-automation CRs that keep
it updated:

- `imagerepository.yaml` scans `ghcr.io/ppbrown/ghcr-test` for tags every 5m.
- `imagepolicy.yaml` picks the highest numeric tag (filters out non-numeric
  tags like `prod`), since ghcr-test's tags are plain incrementing integers,
  not semver.
- `gitrepository-write.yaml` + `imageupdateautomation.yaml` write the new tag
  back into `deployment.yaml`'s image line (the `$imagepolicy` marker comment
  on that line is what gets rewritten) and push a commit to this repo's
  `main` branch. It reuses the existing `flux-system` deploy key/secret,
  which has also been granted write access to this repo on GitHub.
- `kustomization.yaml` ties it all together for whatever toolkit
  `Kustomization` points at this repo.

The piece that actually links this repo into the live cluster 
lives separately in
`fluxcd-test/apps`, following the existing `prod/helmbased/` pattern
there
