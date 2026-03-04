# Lens Selector Example Helm Chart

This is a production-ready Helm chart for deploying the Gravitate-Health Lens Selector Example service to Kubernetes.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.8+
- (Optional) Istio 1.10+ if using Istio networking

## Installation

### From OCI Registry (Recommended)

```bash
# Login to GitHub Container Registry (if private)
helm registry login ghcr.io -u <your-username>

# Install the chart
helm install lens-selector oci://ghcr.io/gravitate-health/charts/lens-selector-example --version 0.1.0

# Install with custom namespace
helm install lens-selector oci://ghcr.io/gravitate-health/charts/lens-selector-example \
  --namespace lens-system \
  --create-namespace \
  --version 0.1.0
```

### From Local Chart

```bash
# From the repository root
helm install lens-selector ./charts/lens-selector-example

# With custom values
helm install lens-selector ./charts/lens-selector-example -f custom-values.yaml
```

## Configuration

The following table lists the key configurable parameters and their default values.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `1` |
| `image.repository` | Container image repository | `ghcr.io/gravitate-health/lens-selector-example` |
| `image.tag` | Container image tag | `latest` |
| `image.pullPolicy` | Image pull policy | `Always` |
| `service.type` | Kubernetes service type | `ClusterIP` |
| `service.port` | Service port | `3000` |
| `config.environment` | Environment name | `prod` |
| `networking.type` | Networking type (`none`, `ingress`, `istio`) | `none` |
| `resources.limits.cpu` | CPU limit | `nil` |
| `resources.limits.memory` | Memory limit | `nil` |
| `resources.requests.cpu` | CPU request | `nil` |
| `resources.requests.memory` | Memory request | `nil` |
| `autoscaling.enabled` | Enable HPA | `false` |
| `customLabels.gravitateHealth` | Enable Gravitate-Health focusing label | `true` |

### Example: Production Configuration

```yaml
# production-values.yaml
replicaCount: 3

image:
  tag: "v1.0.0"
  pullPolicy: IfNotPresent

resources:
  limits:
    cpu: 200m
    memory: 256Mi
  requests:
    cpu: 100m
    memory: 128Mi

autoscaling:
  enabled: true
  minReplicas: 3
  maxReplicas: 10
  targetCPUUtilizationPercentage: 70

config:
  environment: "prod"

livenessProbe:
  enabled: true
  path: /health

readinessProbe:
  enabled: true
  path: /health
```

### Example: Ingress Configuration

```yaml
# ingress-values.yaml
networking:
  type: "ingress"
  ingress:
    className: "nginx"
    annotations:
      cert-manager.io/cluster-issuer: "letsencrypt-prod"
    hosts:
      - host: lens-selector.example.com
        paths:
          - path: /
            pathType: Prefix
    tls:
      - secretName: lens-selector-tls
        hosts:
          - lens-selector.example.com
```

### Example: Istio Configuration

```yaml
# istio-values.yaml
networking:
  type: "istio"
  istio:
    hosts:
      - lens-selector.example.com
    gateways:
      - istio-system/public-gateway
    routes:
      - match:
          - uri:
              prefix: "/lens-selector"
        timeout: 30s
        retries:
          attempts: 3
          perTryTimeout: 10s
```

## Networking Options

The chart supports three networking modes:

### 1. None (Default)
Service is only accessible within the cluster via ClusterIP.

```yaml
networking:
  type: "none"
```

### 2. Ingress
Standard Kubernetes Ingress for external access.

```yaml
networking:
  type: "ingress"
```

### 3. Istio
Istio VirtualService and Gateway for service mesh routing.

```yaml
networking:
  type: "istio"
```

## Upgrading

```bash
# Upgrade from OCI registry
helm upgrade lens-selector oci://ghcr.io/gravitate-health/charts/lens-selector-example --version 0.2.0

# Upgrade from local chart with new values
helm upgrade lens-selector ./charts/lens-selector-example -f updated-values.yaml
```

## Uninstallation

```bash
helm uninstall lens-selector
```

## Development

### Linting

```bash
helm lint charts/lens-selector-example
```

### Testing Template Rendering

```bash
# Render templates with default values
helm template lens-selector charts/lens-selector-example

# Render with custom values
helm template lens-selector charts/lens-selector-example -f test-values.yaml

# Debug mode
helm template lens-selector charts/lens-selector-example --debug
```

### Packaging

```bash
# Package the chart
helm package charts/lens-selector-example

# Generate chart documentation (requires helm-docs)
helm-docs charts/lens-selector-example
```

### Publishing to OCI Registry

```bash
# Login to GitHub Container Registry
echo $GITHUB_TOKEN | helm registry login ghcr.io -u <username> --password-stdin

# Package the chart
helm package charts/lens-selector-example

# Push to OCI registry
helm push lens-selector-example-0.1.0.tgz oci://ghcr.io/gravitate-health/charts
```

## Troubleshooting

### Check deployment status

```bash
helm status lens-selector
kubectl get pods -l app.kubernetes.io/name=lens-selector-example
kubectl logs -l app.kubernetes.io/name=lens-selector-example
```

### View rendered manifests

```bash
helm get manifest lens-selector
```

### Validate chart

```bash
helm lint charts/lens-selector-example
helm template lens-selector charts/lens-selector-example --validate
```

## Service Discovery Label

This chart automatically applies the Gravitate-Health focusing label required for service discovery:

```yaml
eu.gravitate-health.fosps.focusing: "true"
```

This can be controlled via:

```yaml
customLabels:
  gravitateHealth: true  # Set to false to disable
```

## Support

For issues and questions:
- GitHub Issues: https://github.com/Gravitate-Health/lens-selector-example/issues
- Documentation: https://github.com/Gravitate-Health/lens-selector-example

## License

Apache License 2.0 - see LICENSE file for details.
