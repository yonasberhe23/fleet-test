# Kustomize Fleet Example

This directory demonstrates deploying a simple NGINX app to multiple environments (dev, staging, prod) using Kustomize overlays and Rancher Fleet's user-driven bundle approach.

## Structure

```
kustomize/
  base/
    deployment.yaml      # Base Deployment manifest for NGINX
    service.yaml         # Base Service manifest for NGINX
    kustomization.yaml   # Kustomize file for base resources
  overlays/
    dev/
      kustomization.yaml # Overlay for dev environment
      patch.yaml         # Patch to set replicas to 2
    staging/
      kustomization.yaml # Overlay for staging environment
      patch.yaml         # Patch to set replicas to 3
    prod/
      kustomization.yaml # Overlay for prod environment
      patch.yaml         # Patch to set replicas to 5
```

## What is Deployed?
- A simple NGINX Deployment and Service.
- Each overlay customizes the number of replicas for the deployment:
  - dev: 2 replicas
  - staging: 3 replicas
  - prod: 5 replicas

## How to Use with Fleet (User-Driven Bundles)

To deploy only the dev and staging overlays (and skip prod), use the following `GitRepo` manifest in the Rancher UI (Apps > Git Repos) or apply it manually:

```yaml
apiVersion: fleet.cattle.io/v1alpha1
kind: GitRepo
metadata:
  name: custom-bundles-test
  namespace: fleet-local
spec:
  repo: https://github.com/yonasberhe23/fleet-test
  branch: main
  bundles:
    - base: kustomize/overlays/dev
    - base: kustomize/overlays/staging
```

- Replace `<your-org>/<your-repo>` and `branch` as needed.
- Only the overlays listed in `bundles` will be deployed as Fleet bundles.
- **Prod will be skipped, even though the folder exists.**

## Purpose
- To demonstrate how to selectively deploy Kustomize overlays with Fleet's new user-driven bundle approach.
- To show how overlays can be included or excluded by editing the `bundles` field in the `GitRepo` resource. 