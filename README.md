## What is this

Using this helm chart, you will be able to run GLAuth with your backend(s) of choice, either as an internal piece of cluster infrastructure  (e.g. mated to Keycloak in an OIDC environment), or exposed outside your cluster as a high availability authentication server.

## Usage

[Helm](https://helm.sh) must be installed to use the charts.  Please refer to
Helm's [documentation](https://helm.sh/docs) to get started.

Once Helm has been set up correctly, add the repo as follows:

```
helm repo add glauth https://nnstd.github.io/helm-glauth
```

If you had already added this repo earlier, run `helm repo update` to retrieve
the latest versions of the packages.  You can then run `helm search repo
glauth` to see the charts.

To install the glauth chart:

    helm install my-glauth glauth/glauth

To uninstall the chart:

    helm delete my-glauth

## Configuration

### Configuration Philosophy

The current configuration philosophy is to remain fully compatible with the config files already supported by GLAuth.

In the future, GLAuth may be adapted to read Kubernetes secrets, etc. However, this would grow the project's code base quite significantly and I am thus focusing on creating an operator instead (should there be a demand!) -- this operator would reconcile the creation/deletion of secrets for user management purpose.

### values.yaml

While it is perfectly feasible to install GLAuth quickly using the `install` command listed earlier, I would recommend retrieving a local copy of `values.yaml` and modifying it to fit your needs:

```
curl https://raw.githubusercontent.com/glauth/helm-glauth/main/charts/glauth/values.yaml
# edit to your heart's content; save
helm install -f values.yaml my-glauth glauth/glauth
```

As with the natively installed GLAuth instances, you can:

- use a local config file
- connect with OwnCloud
- query a database backend
- proxy queries to an LDAP server.

### replicas

You can run 1 (default) or more replicas. Since GLAuth is mostly reading data, there are no race conditions.

### repository

If you specify "glauth/glauh" you will be pulling the lighter version of GLAuth, which does not support databases.

If you specify "glauth/glauth-plugins" you will be getting the kitchen sink.

You can even specify your own container if you created a personalized version of GLAuth.

Right now, the recommended `tag` is "nightly" which is built from the `dev` branch.

### storage

This section lets you specify a size, access mode and other quirks of the persistent volume created by GLAuth to store its configuration data, including SQLite databases, if any, and certificates.

You can also specify `existingClaim: true` to use a persistent volume that you will have created yourself.

### backend

This will determine which predefined configuration file (`sample-simple.cfg` or `sample-sqlite.cfg`) will be installed in the configuration map used by GLAuth to retrieve its configuration.

When you are ready to run in production, you should create your own configuration file and use it as `file: <your file name>`

### database

#### SQLite

If using a SQLite database, setting `shell` to "true" means that a companion pod will be running. Using this pod you will be able to update the content of the database.

For instance, as described in the plugins' readme file, this is how you can add users, groups, etc. to your database.

You can get rid of this pod when not needed, as it is more exposed than GLAuth's own pod, which was created on top of a 'distroless' image to enhance security:

```
kubectl exec -it gauth-sqlite-client -- sqlite3 gl.db
# if testing, you can copy the example SQL code from pkg/plugins/README.txt here
# then, exit the pod
# edit values.yaml to set 'shell: false'
# "update" our cluster to get rid of the pod
helm update -f values.yaml my-glauth glauth/glauth
```

#### PostgreSQL

To use PostgreSQL as the backend, configure your `values.yaml` as follows:

```yaml
backend:
  type: database

database:
  engine: postgres
  postgres:
    # Connection string for your PostgreSQL instance
    connectionString: "host=my-postgres-host port=5432 dbname=glauth user=glauth password=secretpassword sslmode=require"
    
    # Plugin configuration (usually defaults are fine)
    plugin: "bin/postgres-linux-amd64.so"
    pluginHandler: "NewPostgresHandler"
    
    # LDAP structure configuration
    baseDN: "dc=glauth,dc=com"
    nameformat: "cn"
    groupformat: "ou"
    
    # Optional: SSH key attribute name
    # sshkeyattr: "sshPublicKey"
    
    # Set to true if you want to create PostgreSQL CRD resources
    # (requires db.movetokube.com/v1alpha1 CRDs installed)
    createResources: false
```

**Connection String Format:**
- `host`: PostgreSQL server hostname or IP
- `port`: PostgreSQL server port (default: 5432)
- `dbname`: Database name
- `user`: Database username
- `password`: Database password
- `sslmode`: SSL mode (disable, require, verify-ca, verify-full)

**PostgreSQL Setup:**
Before using PostgreSQL with GLAuth, ensure your database is properly configured:

```sql
-- Create database
CREATE DATABASE glauth;

-- Create user with password
CREATE USER glauth WITH PASSWORD 'your-secure-password';

-- Grant privileges
GRANT ALL PRIVILEGES ON DATABASE glauth TO glauth;
```

**CRD Resources (Optional):**
If you have the `db.movetokube.com/v1alpha1` CRDs installed in your cluster, you can set `createResources: true` to automatically create `Postgres` and `PostgresUser` resources. This is useful for GitOps workflows where database provisioning is handled by operators.

When `createResources: true` is set:
- The PostgreSQL connection string will be automatically sourced from the generated secret (`postgres-user`)
- The secret contains a `POSTGRES_CONNECTION_STRING` environment variable with the full connection details
- The `connectionString` value in `values.yaml` will be ignored in favor of the secret-based configuration

For more details on PostgreSQL configuration, see the [official GLAuth PostgreSQL plugin documentation](https://raw.githubusercontent.com/glauth/glauth-postgres/refs/heads/main/sample-psql.cfg).

### service type

This is standard Kubernetes-type services. You can specify "ClusterIP" / "LoadBalancer" / etc.

If you specify "NodePort" you will need to include a `node` value in order to consistently expose the same port to the rest of the world.

### ingress

This is very much untested. It should work with Nginx, Haproxy, and more, as these can forward both HTTP and TCP connections.

### On the topic of cert and key files

Any files you need persisted, you can push to your persistent volume, and point the configuration file to them.

Such configuration would likely look like:

```
[ldaps]
enabled = true
listen = "0.0.0.0:3894"
cert = "/app/config/glauth.crt"
key = "/app/config/glauth.key"
```

Keep in mind that you can edit this volume's content using the companion pod.







