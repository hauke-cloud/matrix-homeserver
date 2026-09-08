<!-- llm-readme-management spec=1 commit=46e1a0971ed568d40bf0f9b8051a18a1fbba1d0d template=helm model=qwen3.6-35b-a3b digest=5b6cfbe96d9f generated=2026-09-08T21:35:15Z -->
<a href="https://hauke.cloud" target="_blank"><img src="https://img.shields.io/badge/home-hauke.cloud-brightgreen" alt="hauke.cloud" style="display: block;" /></a>
<a href="https://github.com/hauke-cloud" target="_blank"><img src="https://img.shields.io/badge/github-hauke.cloud-blue" alt="hauke.cloud Github Organisation" style="display: block;" /></a>
<a href="https://github.com/hauke-cloud/llm-readme-management" target="_blank"><img src="https://img.shields.io/badge/template-helm-orange" alt="Repository type - helm" style="display: block;" /></a>


# Matrix Homeserver


<img src="https://raw.githubusercontent.com/hauke-cloud/.github/main/resources/img/organisation-logo-small.png" alt="hauke.cloud logo" width="109" height="123" align="right">


<llm header hint="Name the chart and what it deploys.">

This Helm chart, `matrix-homeserver`, deploys a self-hosted Matrix ecosystem on Kubernetes that includes the Element Stack, a Crunchydata PGO-backed Postgres database with pgBackRest backups, and an optional matrix-hookshot bridge. You can use it to install and manage production-ready Matrix infrastructure directly in your cluster.

</llm>


## :book: Description

<llm description>

This repository provides a Helm chart for deploying a self-hosted Matrix homeserver stack on Kubernetes. You use it to install the Element Stack alongside an integrated Postgres database managed by Crunchydata PGO and an optional matrix-hookshot bridge service. The chart bundles three OCI sub-charts, applies production-ready defaults for high availability and monitoring, and handles pgBackRest configuration through a single values file.

Published to `ghcr.io/hauke-cloud/charts`, the chart serves as a versioned deployment package within the organisation’s Helm registry. You configure TLS, ingress routing, and database parameters directly in `values.yaml` before running a standard Helm install command.

- Pulls `matrix-stack`, `postgres-database`, and `matrix-hookshot` as OCI sub-chart dependencies
- Deploys an optional Postgres cluster with pgBouncer pooling, metrics exporter, pgBackRest backups, and async WAL handling
- Creates a ConfigMap to configure asynchronous archive-get and archive-push process-max values for pgBackRest
- Publishes the chart as an OCI artifact to `ghcr.io/hauke-cloud/charts/matrix-homeserver` on push to main

</llm>


## :clipboard: Requirements

<llm requirements hint="Give the Kubernetes version constraint from Chart.yaml, the Helm version, and any dependency charts or CRDs that must already be present.">

Before using this repository, ensure you have the following installed, configured, or granted:
- Helm 3.x with OCI registry support for local chart management and CI workflows
- A running Kubernetes cluster with an ingress controller
- Crunchydata PGO deployed in the target cluster to provide required Custom Resource Definitions
- cert-manager configured for TLS provisioning (auto-TLS is enabled by default)
- A GitHub `GITHUB_TOKEN` with repository push permissions for CI authentication and release creation
- Python and pre-commit installed for local development hooks

</llm>


## 🚀 Getting started

<llm getting_started hint="helm repo add, helm install and helm upgrade with the real repository URL and chart name. Show a values override only if the chart needs one to start.">

1. Clone the repository and enter its directory.
```bash
git clone https://github.com/hauke-cloud/matrix-homeserver.git
cd matrix-homeserver
```
2. Fetch the OCI sub-chart dependencies into your local environment.
```bash
helm dependency build charts/matrix-homeserver/
```
3. Deploy the chart to your Kubernetes cluster by specifying a published version tag.
```bash
helm install matrix-homeserver oci://ghcr.io/hauke-cloud/charts/matrix-homeserver --version <VER>
```

</llm>


## :airplane: Usage

<llm usage hint="Show installing with a values file, and how to reach or verify the deployed workload.">

To deploy the stack, you provide a custom values file that overrides the defaults in `values.yaml`. You must ensure Crunchydata PGO and an ingress controller are already running in your cluster. The following example enables Element Web, configures cert-manager for automatic TLS, and sets the Postgres major version to match your operator:

```yaml
matrix-stack:
  elementWeb:
    enabled: true
  certManager:
    clusterIssuer: letsencrypt-prod
postgres-database:
  postgresVersion: "17"
  instanceReplicas: 3
```

You install the chart by referencing the OCI registry and passing your overrides. The version tag corresponds to the release published by CI:

```bash
helm install matrix-homeserver oci://ghcr.io/hauke-cloud/charts/matrix-homeserver --version 0.0.1 -f my-values.yaml
```

After installation, you verify the deployment and access the homeserver through the generated Ingress resource. The chart enables TLS by default, so traffic is routed securely once cert-manager provisions the certificate. You can check the release status and list the associated Kubernetes resources:

```bash
helm status matrix-homeserver
kubectl get ingress -l app.kubernetes.io/instance=matrix-homeserver
```

Review the pod logs for `element-web` or `synapse` to confirm the Matrix homeserver is serving requests on your configured domain.

</llm>


## :wrench: Configuration

<llm configuration hint="A table of the top-level values from values.yaml: key, default, description. Point at values.yaml for the full set.">

This chart exposes a configuration surface primarily through its top-level `values.yaml`, which passes overrides down to three OCI sub-charts (`matrix-stack`, `postgres-database`, and `matrix-hookshot`). You can control core deployment behavior, database scaling, backup parallelism, and optional component inclusion using the following key values:

| Name | Default | Description |
|---|---|---|
| `postgres-database.enabled` | `true` | Enables or disables the postgres-database sub-chart. |
| `matrix-stack.matrixRTC.enabled` | `true` | Enables the MatrixRTC component. |
| `matrix-stack.matrixRTC.replicas` | `3` | Number of MatrixRTC replicas. |
| `postgres-database.instanceReplicas` | `3` | Number of Postgres data replicas (enables HA when greater than one). |
| `postgres-database.monitoring` | `true` | Enables metrics exporter Prometheus scrape targets. |
| `postgres-database.asyncWalHandling.workersGet` | `2` | pgBackRest archive-get parallelism workers. |
| `postgres-database.asyncWalHandling.workersPush` | `2` | pgBackRest archive-push parallelism workers. |
| `matrix-stack.elementWeb.enabled` | `false` | Deploys Element Web alongside the homeserver. |
| `matrix-stack.matrixAuthenticationService.enabled` | `false` | Deploys the Matrix Authentication Service component. |

</llm>


## 📄 License

This Project is licensed under the GNU General Public License v3.0

- see the [LICENSE](LICENSE) file for details.


## :coffee: Contributing

To become a contributor, please check out the [CONTRIBUTING](CONTRIBUTING.md) file.


## :email: Contact

For any inquiries or support requests, please open an issue in this
repository or contact us at [contact@hauke.cloud](mailto:contact@hauke.cloud).
