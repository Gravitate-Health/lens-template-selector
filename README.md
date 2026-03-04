# Gravitate Health Lens Selector Example

[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

---
## Table of contents

- [Gravitate Health Lens Selector Example](#gravitate-health-lens-selector-example)
  - [Table of contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Kubernetes Deployment](#kubernetes-deployment)
  - [Usage](#usage)
  - [Known issues and limitations](#known-issues-and-limitations)
  - [Getting help](#getting-help)
  - [Contributing](#contributing)
  - [License](#license)
  - [Authors and history](#authors-and-history)
  - [Acknowledgments](#acknowledgments)

---
## Introduction

This repository contains an example of a lens selector. A lens selector provides a list of lens names, and also provides the lenses to the focusing manager.

---
## Kubernetes Deployment

This service can be deployed to Kubernetes using either raw manifests or the production-ready Helm chart (recommended).

### Deploy via Helm (OCI Registry) - Recommended

The Helm chart is distributed as an OCI artifact via GitHub Container Registry. You can deploy directly without cloning the repository:

```bash
# Login to the registry (if private)
helm registry login ghcr.io -u <your-username>

# Deploy directly from the OCI registry
helm install lens-selector oci://ghcr.io/gravitate-health/charts/lens-selector-example --version 0.1.0

# Or with custom values
helm install lens-selector oci://ghcr.io/gravitate-health/charts/lens-selector-example \
  --version 0.1.0 \
  --set image.tag=v1.0.0 \
  --set replicaCount=3

# Upgrade an existing release
helm upgrade lens-selector oci://ghcr.io/gravitate-health/charts/lens-selector-example --version 0.1.0
```

### Deploy via Helm (Local Development)

For local development or customization:

```bash
# Clone the repository
git clone https://github.com/Gravitate-Health/lens-selector-example.git
cd lens-selector-example

# Lint the chart
helm lint charts/lens-selector-example

# Preview the rendered templates
helm template lens-selector charts/lens-selector-example

# Install from local chart
helm install lens-selector charts/lens-selector-example

# Install with custom values file
helm install lens-selector charts/lens-selector-example -f my-values.yaml
```

### Helm Configuration Options

Key configuration options in `values.yaml`:

- **Image Configuration**: Customize repository, tag, and pull policy
- **Replicas**: Scale the deployment (or enable autoscaling)
- **Resources**: Set CPU/memory requests and limits
- **Networking**: Choose between `none` (default), `ingress`, or `istio` for external access
- **Environment**: Configure environment variables via ConfigMap

Example custom values:

```yaml
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

networking:
  type: "ingress"
  ingress:
    className: "nginx"
    hosts:
      - host: lens-selector.example.com
        paths:
          - path: /
            pathType: Prefix
```

### Deploy via Raw Kubernetes Manifests

Alternatively, you can deploy using the raw manifests:

```bash
kubectl apply -f kubernetes/001_lens-selector-example-service.yaml
kubectl apply -f kubernetes/002_lens-selector-example-deployment.yaml
```

**Important**: In order to be discovered by the focusing manager, the service includes the following label:

```yaml
metadata:
  labels:
    eu.gravitate-health.fosps.focusing: "true"
```

This label is automatically applied when using the Helm chart (controlled by `customLabels.gravitateHealth` in values.yaml).

---
## Usage

Service will be accessible internally from the kubernetes cluster with the url `http://lens-selector-example.default.svc.cluster.local:3000/focus`

---
## Known issues and limitations

---
## Getting help

---
## Contributing

---
## License

This project is distributed under the terms of the [Apache License, Version 2.0 (AL2)](http://www.apache.org/licenses/LICENSE-2.0).  The license applies to this file and other files in the [GitHub repository](https://github.com/Gravitate-Health/Focusing-module) hosting this file.

```
Copyright 2022 Universidad Politécnica de Madrid

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
---
## Authors and history

- Guillermo Mejías ([@gmej](https://github.com/gmej))


---
## Acknowledgments
