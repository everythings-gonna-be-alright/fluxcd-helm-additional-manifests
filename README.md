# Additional Manifests Helm Chart

## Problem

FluxCD `HelmRelease` cannot create dependencies between Helm charts and custom Kubernetes objects (CRDs, custom resources, etc.). You can't ensure custom objects are deployed **after** a specific HelmRelease completes.

## Solution

This Helm chart acts as a wrapper that deploys additional manifests (CRDs, custom resources, etc.) **with** dependency management. By using this chart in a `HelmRelease` with `dependsOn`, you can create custom objects only after prerequisite Helm charts are installed.

## Usage

Deploy your main application via FluxCD `HelmRelease`, then deploy this chart with `dependsOn` referencing the main release:

```yaml
apiVersion: helm.toolkit.fluxcd.io/v2beta1
kind: HelmRelease
metadata:
  name: my-app-crds
spec:
  dependsOn:
    - name: my-app  # Wait for main app installation
  chart:
    spec:
      chart: ./additional-manifests
  values:
    manifests:
      - apiVersion: example.com/v1
        kind: MyCustomResource
        metadata:
          name: my-resource
```

Now your custom objects deploy **after** the dependency is ready.
