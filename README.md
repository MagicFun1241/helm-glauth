# GLAuth Helm Chart

GLAuth is a lightweight LDAP server for development, home use, or CI. This Helm chart allows you to deploy GLAuth in a Kubernetes cluster with your backend of choice, either as internal cluster infrastructure (e.g., mated to Keycloak in an OIDC environment) or exposed outside your cluster as a high availability authentication server.

## TL;DR

```bash
helm repo add glauth https://nnstd.github.io/helm-glauth
helm install my-glauth glauth/glauth
```

## Introduction

This chart bootstraps a GLAuth deployment on a [Kubernetes](https://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure (if persistence is enabled)

## Installing the Chart

To install the chart with the release name `my-glauth`:

```bash
helm install my-glauth glauth/glauth
```

The command deploys GLAuth on the Kubernetes cluster in the default configuration. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-glauth` deployment:

```bash
helm delete my-glauth
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Parameters

### Global parameters

| Name                      | Description                                     | Value |
| ------------------------- | ----------------------------------------------- | ----- |
| `global.imageRegistry`    | Global Docker image registry                    | `""`  |
| `global.imagePullSecrets` | Global Docker registry secret names as an array | `[]`  |
| `global.storageClass`     | Global StorageClass for Persistent Volume(s)   | `""`  |

### Common parameters

| Name                     | Description                                        | Value           |
| ------------------------ | -------------------------------------------------- | --------------- |
| `kubeVersion`            | Override Kubernetes version                        | `""`            |
| `nameOverride`           | String to partially override common.names.name    | `""`            |
| `fullnameOverride`       | String to fully override common.names.fullname    | `""`            |
| `namespaceOverride`      | String to fully override common.names.namespace   | `""`            |
| `commonLabels`           | Labels to add to all deployed objects             | `{}`            |
| `commonAnnotations`      | Annotations to add to all deployed objects        | `{}`            |
| `clusterDomain`          | Kubernetes cluster domain name                     | `cluster.local` |
| `extraDeploy`            | Array of extra objects to deploy with the release | `[]`            |

### GLAuth Image parameters

| Name                | Description                                          | Value                     |
| ------------------- | ---------------------------------------------------- | ------------------------- |
| `image.registry`    | GLAuth image registry                                | `docker.io`               |
| `image.repository`  | GLAuth image repository                              | `glauth/glauth-plugins`   |
| `image.tag`         | GLAuth image tag (immutable tags are recommended)   | `v2.4.0`                  |
| `image.digest`      | GLAuth image digest in the way sha256:aa....        | `""`                      |
| `image.pullPolicy`  | GLAuth image pull policy                             | `IfNotPresent`            |
| `image.pullSecrets` | GLAuth image pull secrets                            | `[]`                      |

### GLAuth Deployment parameters

| Name                                    | Description                                                                               | Value   |
| --------------------------------------- | ----------------------------------------------------------------------------------------- | ------- |
| `replicaCount`                          | Number of GLAuth replicas to deploy                                                      | `1`     |
| `containerPorts.ldap`                   | GLAuth LDAP container port                                                                | `3893`  |
| `containerPorts.ldaps`                  | GLAuth LDAPS container port                                                               | `3894`  |
| `containerPorts.web`                    | GLAuth web interface container port                                                       | `5555`  |
| `livenessProbe.enabled`                 | Enable livenessProbe on GLAuth containers                                                | `true`  |
| `livenessProbe.initialDelaySeconds`     | Initial delay seconds for livenessProbe                                                  | `10`    |
| `livenessProbe.periodSeconds`           | Period seconds for livenessProbe                                                         | `10`    |
| `livenessProbe.timeoutSeconds`          | Timeout seconds for livenessProbe                                                        | `5`     |
| `livenessProbe.failureThreshold`        | Failure threshold for livenessProbe                                                      | `3`     |
| `livenessProbe.successThreshold`        | Success threshold for livenessProbe                                                      | `1`     |
| `readinessProbe.enabled`                | Enable readinessProbe on GLAuth containers                                               | `true`  |
| `readinessProbe.initialDelaySeconds`    | Initial delay seconds for readinessProbe                                                 | `5`     |
| `readinessProbe.periodSeconds`          | Period seconds for readinessProbe                                                        | `10`    |
| `readinessProbe.timeoutSeconds`         | Timeout seconds for readinessProbe                                                       | `5`     |
| `readinessProbe.failureThreshold`       | Failure threshold for readinessProbe                                                     | `3`     |
| `readinessProbe.successThreshold`       | Success threshold for readinessProbe                                                     | `1`     |
| `startupProbe.enabled`                  | Enable startupProbe on GLAuth containers                                                 | `false` |
| `startupProbe.initialDelaySeconds`      | Initial delay seconds for startupProbe                                                   | `10`    |
| `startupProbe.periodSeconds`            | Period seconds for startupProbe                                                          | `10`    |
| `startupProbe.timeoutSeconds`           | Timeout seconds for startupProbe                                                         | `5`     |
| `startupProbe.failureThreshold`         | Failure threshold for startupProbe                                                       | `3`     |
| `startupProbe.successThreshold`         | Success threshold for startupProbe                                                       | `1`     |
| `customLivenessProbe`                   | Custom livenessProbe that overrides the default one                                      | `{}`    |
| `customReadinessProbe`                  | Custom readinessProbe that overrides the default one                                     | `{}`    |
| `customStartupProbe`                    | Custom startupProbe that overrides the default one                                       | `{}`    |
| `resources.limits`                      | The resources limits for the GLAuth containers                                           | `{}`    |
| `resources.requests`                    | The requested resources for the GLAuth containers                                        | `{}`    |
| `podSecurityContext.enabled`            | Enabled GLAuth pods' Security Context                                                    | `true`  |
| `podSecurityContext.fsGroup`            | Set GLAuth pod's Security Context fsGroup                                                | `1001`  |
| `containerSecurityContext.enabled`      | Enabled GLAuth containers' Security Context                                              | `true`  |
| `containerSecurityContext.runAsUser`    | Set GLAuth containers' Security Context runAsUser                                        | `1001`  |
| `containerSecurityContext.runAsNonRoot` | Set GLAuth containers' Security Context runAsNonRoot                                     | `true`  |
| `existingConfigmap`                     | The name of an existing ConfigMap with your custom configuration for GLAuth              | `""`    |
| `command`                               | Override default container command (useful when using custom images)                     | `[]`    |
| `args`                                  | Override default container args (useful when using custom images)                        | `[]`    |
| `hostAliases`                           | GLAuth pods host aliases                                                                  | `[]`    |
| `podLabels`                             | Extra labels for GLAuth pods                                                             | `{}`    |
| `podAnnotations`                        | Annotations for GLAuth pods                                                              | `{}`    |
| `podAffinityPreset`                     | Pod affinity preset. Ignored if `affinity` is set. Allowed values: `soft` or `hard`     | `""`    |
| `podAntiAffinityPreset`                 | Pod anti-affinity preset. Ignored if `affinity` is set. Allowed values: `soft` or `hard`| `soft`  |
| `pdb.create`                            | Enable/disable a Pod Disruption Budget creation                                          | `false` |
| `pdb.minAvailable`                      | Minimum number/percentage of pods that should remain scheduled                           | `1`     |
| `pdb.maxUnavailable`                    | Maximum number/percentage of pods that may be made unavailable                          | `""`    |
| `autoscaling.enabled`                   | Enable Horizontal POD autoscaling for GLAuth                                             | `false` |
| `autoscaling.minReplicas`               | Minimum number of GLAuth replicas                                                        | `1`     |
| `autoscaling.maxReplicas`               | Maximum number of GLAuth replicas                                                        | `100`   |
| `autoscaling.targetCPU`                 | Target CPU utilization percentage                                                        | `80`    |
| `autoscaling.targetMemory`              | Target Memory utilization percentage                                                     | `""`    |
| `nodeSelector`                          | Node labels for GLAuth pods assignment                                                   | `{}`    |
| `tolerations`                           | Tolerations for GLAuth pods assignment                                                   | `[]`    |
| `updateStrategy.type`                   | GLAuth statefulset strategy type                                                         | `RollingUpdate` |
| `priorityClassName`                     | GLAuth pods' priorityClassName                                                           | `""`    |
| `topologySpreadConstraints`             | Topology Spread Constraints for pod assignment spread across your cluster among failure-domains | `[]` |
| `schedulerName`                         | Name of the k8s scheduler (other than default) for GLAuth pods                          | `""`    |
| `terminationGracePeriodSeconds`         | Seconds Redmine pod needs to terminate gracefully                                        | `""`    |
| `lifecycleHooks`                        | for the GLAuth container(s) to automate configuration before or after startup           | `{}`    |
| `extraEnvVars`                          | Array with extra environment variables to add to GLAuth nodes                           | `[]`    |
| `extraEnvVarsCM`                        | Name of existing ConfigMap containing extra env vars for GLAuth nodes                   | `""`    |
| `extraEnvVarsSecret`                    | Name of existing Secret containing extra env vars for GLAuth nodes                      | `""`    |
| `extraVolumes`                          | Optionally specify extra list of additional volumes for the GLAuth pod(s)               | `[]`    |
| `extraVolumeMounts`                     | Optionally specify extra list of additional volumeMounts for the GLAuth container(s)    | `[]`    |
| `sidecars`                              | Add additional sidecar containers to the GLAuth pod(s)                                  | `[]`    |
| `initContainers`                        | Add additional init containers to the GLAuth pod(s)                                     | `[]`    |

### GLAuth Configuration parameters

| Name                           | Description                                                                      | Value        |
| ------------------------------ | -------------------------------------------------------------------------------- | ------------ |
| `backend.type`                 | Backend type for GLAuth (config or database)                                    | `database`   |
| `backend.file`                 | Custom configuration file name (stored in ConfigMap)                            | `""`         |
| `config.storage.size`          | Size of the persistent volume for GLAuth data                                   | `20Gi`       |
| `config.storage.className`     | Storage class name for the persistent volume                                    | `""`         |
| `config.storage.access`        | Access mode for the persistent volume                                           | `""`         |
| `config.existingClaim`         | Use an existing PVC for GLAuth data                                             | `false`      |

### Database Configuration parameters

| Name                                     | Description                                                                      | Value                    |
| ---------------------------------------- | -------------------------------------------------------------------------------- | ------------------------ |
| `database.engine`                        | Database engine (sqlite or postgres)                                            | `sqlite`                 |
| `database.shell`                         | Enable SQLite shell pod for database management                                 | `false`                  |
| `database.postgres.connectionString`    | PostgreSQL connection string                                                     | `""`                     |
| `database.postgres.plugin`              | PostgreSQL plugin path                                                           | `""`                     |
| `database.postgres.pluginHandler`       | PostgreSQL plugin handler                                                        | `""`                     |
| `database.postgres.baseDN`              | Base DN for LDAP structure                                                       | `dc=glauth,dc=com`       |
| `database.postgres.nameformat`          | Name format for LDAP entries                                                     | `cn`                     |
| `database.postgres.groupformat`         | Group format for LDAP entries                                                    | `ou`                     |
| `database.postgres.sshkeyattr`          | SSH key attribute name (optional)                                                | `""`                     |
| `database.postgres.createResources`     | Create PostgreSQL CRD resources                                                  | `false`                  |
| `database.postgres.secretName`          | Secret name for PostgreSQL credentials                                           | `postgres-user`          |
| `database.postgres.existingSecretName`  | Existing secret name created by PostgreSQL operator                             | `""`                     |

### Traffic Exposure Parameters

| Name                               | Description                                                                      | Value                    |
| ---------------------------------- | -------------------------------------------------------------------------------- | ------------------------ |
| `service.type`                     | GLAuth service type                                                              | `NodePort`               |
| `service.name`                     | GLAuth service name                                                              | `glauth`                 |
| `service.ports`                    | GLAuth service ports configuration                                               | `See values.yaml`        |
| `service.nodePorts.ldap`           | Node port for LDAP                                                              | `30389`                  |
| `service.nodePorts.ldaps`          | Node port for LDAPS                                                             | `30636`                  |
| `service.nodePorts.web`            | Node port for web interface                                                     | `30555`                  |
| `service.clusterIP`                | GLAuth service Cluster IP                                                       | `""`                     |
| `service.loadBalancerIP`           | GLAuth service Load Balancer IP                                                 | `""`                     |
| `service.loadBalancerSourceRanges` | GLAuth service Load Balancer sources                                            | `[]`                     |
| `service.externalTrafficPolicy`    | GLAuth service external traffic policy                                          | `Cluster`                |
| `service.annotations`              | Additional custom annotations for GLAuth service                                | `{}`                     |
| `service.extraPorts`               | Extra ports to expose in GLAuth service                                         | `[]`                     |
| `service.sessionAffinity`          | Control where web requests go, to the same pod or round-robin                   | `None`                   |
| `service.sessionAffinityConfig`    | Additional settings for the sessionAffinity                                     | `{}`                     |

### Ingress Parameters

| Name                  | Description                                               | Value                    |
| --------------------- | --------------------------------------------------------- | ------------------------ |
| `ingress.enabled`     | Enable ingress record generation for GLAuth              | `false`                  |
| `ingress.className`   | IngressClass that will be used to implement the Ingress  | `""`                     |
| `ingress.annotations` | Additional annotations for the Ingress resource          | `{}`                     |
| `ingress.hosts`       | An array with hosts and paths                            | `See values.yaml`        |
| `ingress.tls`         | TLS configuration for the Ingress                        | `[]`                     |

### RBAC Parameters

| Name                    | Description                                           | Value   |
| ----------------------- | ----------------------------------------------------- | ------- |
| `rbac.create`           | Specifies whether RBAC resources should be created   | `true`  |
| `rbac.rules`            | Custom RBAC rules to set                             | `[]`    |

### Service Account Parameters

| Name                                 | Description                                                            | Value    |
| ------------------------------------ | ---------------------------------------------------------------------- | -------- |
| `serviceAccount.create`              | Specifies whether a ServiceAccount should be created                  | `true`   |
| `serviceAccount.name`                | The name of the ServiceAccount to use                                 | `""`     |
| `serviceAccount.annotations`         | Additional Service Account annotations                                 | `{}`     |
| `serviceAccount.automountServiceAccountToken` | Automount service account token for the server service account | `false`  |

### Other Parameters

| Name                       | Description                                                      | Value   |
| -------------------------- | ---------------------------------------------------------------- | ------- |
| `networkPolicy.enabled`    | Specifies whether a NetworkPolicy should be created             | `false` |
| `networkPolicy.allowExternal` | Don't require server label for connections                   | `true`  |
| `networkPolicy.extraIngress` | Add extra ingress rules to the NetworkPolicy                 | `[]`    |
| `networkPolicy.extraEgress`  | Add extra egress rules to the NetworkPolicy                  | `[]`    |

## Configuration and Installation Details

### Configuration Philosophy

The current configuration philosophy is to remain fully compatible with the config files already supported by GLAuth. In the future, GLAuth may be adapted to read Kubernetes secrets, etc. However, this would grow the project's code base quite significantly.

### Backend Types

GLAuth supports two main backend types:

1. **Config Backend**: Uses a simple configuration file with embedded users and groups
2. **Database Backend**: Uses SQLite or PostgreSQL for storing user and group data

### Database Configuration

#### SQLite

When using SQLite as the backend:

```yaml
backend:
  type: database
database:
  engine: sqlite
  shell: true  # Enable shell pod for database management
```

Setting `shell: true` creates a companion pod that allows you to manage the SQLite database:

```bash
kubectl exec -it glauth-sqlite-client -- sqlite3 gl.db
```

#### PostgreSQL

For PostgreSQL backend:

```yaml
backend:
  type: database
database:
  engine: postgres
  postgres:
    connectionString: "host=my-postgres-host port=5432 dbname=glauth user=glauth password=secretpassword sslmode=require"
    baseDN: "dc=glauth,dc=com"
    nameformat: "cn"
    groupformat: "ou"
```

### Service Configuration

GLAuth exposes three main ports:
- **3893**: LDAP (unencrypted)
- **3894**: LDAPS (encrypted)
- **5555**: Web interface

You can configure the service type (ClusterIP, NodePort, LoadBalancer) based on your needs.

### Persistence

GLAuth uses persistent volumes to store:
- Configuration files
- SQLite databases (when using SQLite backend)
- SSL certificates for LDAPS

Configure persistence using the `config.storage` section.

### Security

For production deployments, consider:
- Using LDAPS (port 3894) instead of plain LDAP
- Configuring proper network policies
- Using secrets for database credentials
- Disabling the SQLite shell pod when not needed

## Troubleshooting

### Common Issues

1. **Pod not starting**: Check resource limits and node capacity
2. **Database connection issues**: Verify connection string and network policies
3. **LDAP authentication failures**: Check user configuration and base DN settings
4. **Persistence issues**: Verify storage class and PVC creation

### Debug Commands

```bash
# Check pod status
kubectl get pods -l app.kubernetes.io/name=glauth

# View logs
kubectl logs -l app.kubernetes.io/name=glauth

# Check service endpoints
kubectl get svc glauth

# Test LDAP connectivity
kubectl exec -it deploy/glauth -- ldapsearch -x -H ldap://localhost:3893 -b "dc=glauth,dc=com"
```

## Upgrading

### To 0.3.0

This version introduces PostgreSQL support and enhanced configuration options. When upgrading:

1. Review the new PostgreSQL configuration options
2. Update your `values.yaml` if using custom configurations
3. Consider enabling persistence if not already configured

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This Helm chart is licensed under the Apache 2.0 license.

## Support

For support and questions:
- [GLAuth GitHub Repository](https://github.com/glauth/glauth)
- [Helm Chart Repository](https://github.com/nnstd/helm-glauth)







