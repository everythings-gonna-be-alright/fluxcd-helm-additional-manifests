# Additional Manifests Helm Chart

## Problem

FluxCD `HelmRelease` cannot create dependencies between Helm charts and custom Kubernetes objects (CRDs, custom resources, etc.). You can't ensure custom objects are deployed **after** a specific HelmRelease completes.

## Solution

This Helm chart acts as a wrapper that deploys additional manifests (CRDs, custom resources, etc.) **with** dependency management. By using this chart in a `HelmRelease` with `dependsOn`, you can create custom objects only after prerequisite Helm charts are installed.

## Installation

```bash
# Add the Helm repository
helm repo add additional-manifests https://everything-gonna-be-alright.github.io/fluxcd-helm-additional-manifests/
helm repo update

# Install the chart
helm install my-release additional-manifests/additional-manifests

# Install with custom values
helm install my-release additional-manifests/additional-manifests -f values.yaml
```

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

## Using with FluxCD

For FluxCD users, reference the chart via HelmRepository:

```yaml
apiVersion: source.toolkit.fluxcd.io/v1beta2
kind: HelmRepository
metadata:
  name: additional-manifests
  namespace: flux-system
spec:
  interval: 5m
  url: https://everything-gonna-be-alright.github.io/fluxcd-helm-additional-manifests/
---
apiVersion: helm.toolkit.fluxcd.io/v2beta1
kind: HelmRelease
metadata:
  name: my-app-additional
  namespace: default
spec:
  dependsOn:
    - name: my-app  # Wait for main app
  interval: 5m
  chart:
    spec:
      chart: additional-manifests
      version: '>=0.1.0'
      sourceRef:
        kind: HelmRepository
        name: additional-manifests
        namespace: flux-system
  values:
    manifests:
      - apiVersion: v1
        kind: ConfigMap
        metadata:
          name: app-config
        data:
          key: value
```
