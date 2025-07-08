# GLAuth Helm Chart

GLAuth is a lightweight LDAP server for development, home use, or CI. 

This Helm chart allows you to deploy GLAuth in a Kubernetes cluster with your backend of choice, either as internal cluster infrastructure (e.g., mated to Keycloak in an OIDC environment) or exposed outside your cluster as a high availability authentication server.

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

## What changed?

- [x] Added support of [PostgresOperator](https://github.com/movetokube/postgres-operator) for database creation and secret management.
- [x] Refactored the chart to cover all the configuration options.

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

### Common Parameters

| Name                     | Description                                        | Value           |
| ------------------------ | -------------------------------------------------- | --------------- |
| `replicaCount`           | Number of GLAuth replicas to deploy               | `1`             |
| `nameOverride`           | String to partially override common.names.name    | `""`            |
| `fullnameOverride`       | String to fully override common.names.fullname    | `""`            |

### GLAuth Image Parameters

| Name                | Description                                          | Value                     |
| ------------------- | ---------------------------------------------------- | ------------------------- |
| `image.repository`  | GLAuth image repository                              | `ghcr.io/nnstd/glauth`   |
| `image.tag`         | GLAuth image tag (immutable tags are recommended)   | `v2.4.44`                 |
| `image.pullPolicy`  | GLAuth image pull policy                             | `IfNotPresent`            |
| `imagePullSecrets`  | GLAuth image pull secrets                            | `[]`                      |

### GLAuth Configuration Parameters

| Name                           | Description                                                                      | Value        |
| ------------------------------ | -------------------------------------------------------------------------------- | ------------ |
| `config.debug`                 | Enable debug mode                                                               | `false`      |
| `config.systemLogging`         | Enable system logging                                                           | `false`      |
| `config.structuredLogging`     | Enable structured logging                                                       | `false`      |

### Storage Configuration

| Name                           | Description                                                                      | Value        |
| ------------------------------ | -------------------------------------------------------------------------------- | ------------ |
| `config.storage.size`          | Size of the persistent volume for GLAuth data                                   | `20G`        |
| `config.storage.className`     | Storage class name for the persistent volume                                    | `""`         |
| `config.storage.accessMode`    | Access mode for the persistent volume                                           | `""`         |
| `config.storage.existingClaim` | Use an existing PVC for GLAuth data                                             | `false`      |

### LDAP Configuration

| Name                           | Description                                                                      | Value        |
| ------------------------------ | -------------------------------------------------------------------------------- | ------------ |
| `config.ldap.enabled`          | Enable LDAP service                                                             | `true`       |
| `config.ldap.listen`           | Listen address for LDAP (default: "0.0.0.0:3893")                             | `""`         |

### LDAPS Configuration

| Name                           | Description                                                                      | Value        |
| ------------------------------ | -------------------------------------------------------------------------------- | ------------ |
| `config.ldaps.enabled`         | Enable LDAPS service                                                            | `false`      |
| `config.ldaps.listen`          | Listen address for LDAPS (default: "0.0.0.0:3894")                            | `""`         |
| `config.ldaps.cert`            | Path to the certificate file for LDAPS                                          | `""`         |
| `config.ldaps.key`             | Path to the key file for LDAPS                                                  | `""`         |

### API Configuration

| Name                           | Description                                                                      | Value        |
| ------------------------------ | -------------------------------------------------------------------------------- | ------------ |
| `config.api.enabled`           | Enable API service                                                               | `true`       |
| `config.api.internals`         | Enable internal API for debugging application performance                       | `true`       |
| `config.api.tls`               | Whether to enable TLS for API                                                   | `false`      |
| `config.api.listen`            | Listen address for the API (default: "0.0.0.0:5555")                          | `""`         |
| `config.api.cert`              | Path to the certificate file for API TLS                                        | `""`         |
| `config.api.key`               | Path to the key file for API TLS                                                | `""`         |

### Behavior Configuration

| Name                                     | Description                                                                      | Value        |
| ---------------------------------------- | -------------------------------------------------------------------------------- | ------------ |
| `config.behaviors.ignoreCapabilities`   | Ignore all capabilities restrictions                                             | `false`      |
| `config.behaviors.limitFailedBinds`     | Enable "fail2ban" type backoff mechanism                                        | `true`       |
| `config.behaviors.numberOfFailedBinds`  | How many failed login attempts before ban                                       | `3`          |
| `config.behaviors.periodOfFailedBinds`  | Time window for failed login attempts (seconds)                                 | `10`         |
| `config.behaviors.blockFailedBindsFor`  | Ban duration (seconds)                                                           | `60`         |
| `config.behaviors.pruneSourceTableEvery`| Clean learnt IP addresses every N seconds                                       | `600`        |
| `config.behaviors.pruneSourcesOlderThan`| Clean learnt IP addresses not seen in N seconds                                 | `600`        |

### Backend Configuration

| Name                           | Description                                                                      | Value              |
| ------------------------------ | -------------------------------------------------------------------------------- | ------------------ |
| `config.backend.type`          | Backend type for GLAuth (config or database)                                    | `database`         |
| `config.backend.file`          | Custom configuration file name (stored in ConfigMap)                            | `""`               |
| `config.backend.baseDN`        | Base DN for LDAP structure                                                       | `dc=glauth,dc=com` |
| `config.backend.nameFormat`    | Name format for LDAP entries                                                     | `cn`               |
| `config.backend.groupFormat`   | Group format for LDAP entries                                                    | `ou`               |
| `config.backend.anonymousDSE`  | Enable anonymous DSE for clients like SSSD                                      | `false`            |
| `config.backend.sshKeyAttr`    | SSH key attribute name (e.g., 'ipaSshPubKey' for IPA compatibility)            | `""`               |

### Database Configuration

| Name                                     | Description                                                                      | Value              |
| ---------------------------------------- | -------------------------------------------------------------------------------- | ------------------ |
| `config.database.engine`                | Database engine (sqlite or postgres)                                            | `sqlite`           |

### SQLite Database Configuration

| Name                                     | Description                                                                      | Value              |
| ---------------------------------------- | -------------------------------------------------------------------------------- | ------------------ |
| `config.database.sqlite.shell`          | Enable SQLite shell pod for database management                                 | `false`            |

### PostgreSQL Database Configuration

| Name                                     | Description                                                                      | Value              |
| ---------------------------------------- | -------------------------------------------------------------------------------- | ------------------ |
| `config.database.postgres.connectionString`| PostgreSQL connection string (required when createResources is false)       | `""`               |
| `config.database.postgres.createResources` | Create PostgreSQL CRD resources (Postgres and PostgresUser)                  | `false`            |
| `config.database.postgres.secretName`      | Secret name for PostgreSQL credentials                                        | `postgres-user`    |
| `config.database.postgres.existingSecretName`| Existing secret name created by PostgreSQL operator                        | `""`               |

### Service Configuration

| Name                               | Description                                                                      | Value              |
| ---------------------------------- | -------------------------------------------------------------------------------- | ------------------ |
| `service.name`                     | GLAuth service name                                                              | `glauth`           |
| `service.type`                     | GLAuth service type                                                              | `NodePort`         |
| `service.ports`                    | GLAuth service ports configuration                                               | See values.yaml    |

### Service Account Configuration

| Name                                 | Description                                                            | Value    |
| ------------------------------------ | ---------------------------------------------------------------------- | -------- |
| `serviceAccount.create`              | Specifies whether a ServiceAccount should be created                  | `true`   |
| `serviceAccount.name`                | The name of the ServiceAccount to use                                 | `""`     |
| `serviceAccount.annotations`         | Additional Service Account annotations                                 | `{}`     |

### Pod Configuration

| Name                       | Description                                                      | Value   |
| -------------------------- | ---------------------------------------------------------------- | ------- |
| `podAnnotations`           | Annotations for GLAuth pods                                      | `{}`    |
| `podSecurityContext`       | GLAuth pods' Security Context                                    | `{}`    |
| `securityContext`          | GLAuth containers' Security Context                              | `{}`    |

### Ingress Configuration

| Name                  | Description                                               | Value                    |
| --------------------- | --------------------------------------------------------- | ------------------------ |
| `ingress.enabled`     | Enable ingress record generation for GLAuth              | `false`                  |
| `ingress.className`   | IngressClass that will be used to implement the Ingress  | `""`                     |
| `ingress.annotations` | Additional annotations for the Ingress resource          | `{}`                     |
| `ingress.hosts`       | An array with hosts and paths                            | See values.yaml          |
| `ingress.tls`         | TLS configuration for the Ingress                        | `[]`                     |

### Resource Management

| Name                       | Description                                                      | Value   |
| -------------------------- | ---------------------------------------------------------------- | ------- |
| `resources`                | The resources limits and requests for the GLAuth containers     | `{}`    |

### Autoscaling Configuration

| Name                                         | Description                                                      | Value   |
| -------------------------------------------- | ---------------------------------------------------------------- | ------- |
| `autoscaling.enabled`                        | Enable Horizontal POD autoscaling for GLAuth                    | `false` |
| `autoscaling.minReplicas`                    | Minimum number of GLAuth replicas                               | `1`     |
| `autoscaling.maxReplicas`                    | Maximum number of GLAuth replicas                               | `100`   |
| `autoscaling.targetCPUUtilizationPercentage` | Target CPU utilization percentage                                | `80`    |

### Other Configuration

| Name                       | Description                                                      | Value   |
| -------------------------- | ---------------------------------------------------------------- | ------- |
| `nodeSelector`             | Node labels for GLAuth pods assignment                          | `{}`    |
| `tolerations`              | Tolerations for GLAuth pods assignment                          | `[]`    |
| `affinity`                 | Affinity for GLAuth pods assignment                             | `{}`    |

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
config:
  backend:
    type: database
  database:
    engine: sqlite
    sqlite:
      shell: true  # Enable shell pod for database management
```

Setting `shell: true` creates a companion pod that allows you to manage the SQLite database:

```bash
kubectl exec -it glauth-sqlite-client -- sqlite3 /root/db/gl.db
```

#### PostgreSQL

For PostgreSQL backend with external database:

```yaml
config:
  backend:
    type: database
  database:
    engine: postgres
    postgres:
      connectionString: "host=my-postgres-host port=5432 dbname=glauth user=glauth password=secretpassword sslmode=require"
      createResources: false
```

For PostgreSQL backend with PostgreSQL Operator:

```yaml
config:
  backend:
    type: database
  database:
    engine: postgres
    postgres:
      createResources: true
      secretName: "postgres-user"
```

### Service Configuration

GLAuth exposes three main ports:
- **3893**: LDAP (unencrypted)
- **3894**: LDAPS (encrypted)
- **5555**: Web interface/API

The default service configuration uses NodePort:

```yaml
service:
  type: NodePort
  ports:
    - name: ldap
      internal: 3893
      external: 3893
      node: 30389
    - name: ldaps
      internal: 3894
      external: 3894
      node: 30636
    - name: web
      internal: 5555
      external: 5555
      node: 30555
```

### LDAPS Configuration

To enable LDAPS, you need to provide certificates:

```yaml
config:
  ldaps:
    enabled: true
    cert: "/path/to/glauth.crt"
    key: "/path/to/glauth.key"
```

Generate a certificate with:
```bash
openssl req -x509 -newkey rsa:4096 -keyout glauth.key -out glauth.crt -days 365 -nodes -subj '/CN=`hostname`'
```

### Persistence

GLAuth uses persistent volumes to store:
- Configuration files
- SQLite databases (when using SQLite backend)
- SSL certificates for LDAPS

Configure persistence using the `config.storage` section:

```yaml
config:
  storage:
    size: 20G
    className: "fast-ssd"
    accessMode: "ReadWriteOnce"
```

### Security Features

GLAuth includes built-in security features:

1. **Failed Login Protection**: Implements a "fail2ban" style mechanism
2. **Rate Limiting**: Configurable thresholds and ban durations
3. **IP Address Management**: Automatic cleanup of learned IP addresses

Configure these via the `config.behaviors` section.

### Security

For production deployments, consider:
- Using LDAPS (port 3894) instead of plain LDAP
- Configuring proper network policies
- Using secrets for database credentials
- Disabling the SQLite shell pod when not needed
- Enabling TLS for the API endpoint

## Troubleshooting

### Common Issues

1. **Pod not starting**: Check resource limits and node capacity
2. **Database connection issues**: Verify connection string and network policies
3. **LDAP authentication failures**: Check user configuration and base DN settings
4. **Persistence issues**: Verify storage class and PVC creation

### Debug Commands

```bash
# Check pod status
kubectl get pods -l app=glauth

# View logs
kubectl logs -l app=glauth

# Check service endpoints
kubectl get svc glauth

# Test LDAP connectivity (if using NodePort)
ldapsearch -x -H ldap://node-ip:30389 -b "dc=glauth,dc=com"

# Access SQLite shell (if enabled)
kubectl exec -it glauth-sqlite-client -- sqlite3 /root/db/gl.db
```

## Upgrading

### To Latest Version

When upgrading, review the changelog and:

1. Check for breaking changes in configuration
2. Update your `values.yaml` if using custom configurations
3. Consider backup of your data before upgrading
4. Test in a non-production environment first

## Examples

### Basic SQLite Setup

```yaml
config:
  backend:
    type: database
  database:
    engine: sqlite
    sqlite:
      shell: true
  storage:
    size: 10Gi
```

### PostgreSQL with External Database

```yaml
config:
  backend:
    type: database
  database:
    engine: postgres
    postgres:
      connectionString: "host=postgres.example.com port=5432 dbname=glauth user=glauth password=secret sslmode=require"
      createResources: false
```

### LDAPS with Custom Certificates

```yaml
config:
  ldaps:
    enabled: true
    cert: "/app/config/tls.crt"
    key: "/app/config/tls.key"
  storage:
    size: 5Gi
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This Helm chart is licensed under the Apache 2.0 license.

## Support

For support and questions:
- [GitHub Repository](https://github.com/nnstd/glauth)
- [Helm Chart Repository](https://github.com/nnstd/helm-glauth)
