# Helm Chart Repository

This is the Helm chart repository for additional-manifests.

## Usage

Add this repository to Helm:

```bash
helm repo add additional-manifests https://everythings-gonna-be-alright.github.io/fluxcd-helm-additional-manifests
helm repo update
```

Install the chart:

```bash
helm install my-release additional-manifests/additional-manifests
```

## Available Charts

- **additional-manifests**: A Helm chart for rendering additional manifests with FluxCD
