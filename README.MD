# Deploying InvenTree on Kubernetes

This repository provides manifests for easily deploying InvenTree (stable version) on Kubernetes.

This repository is provided under the MIT License.

## Disclaimer

**Please note:** Please note that the provided manifests will deploy all components to the Kubernetes default namespace, unless changed.

## Initial Setup

To create the nginx configuration used by the inventree-proxy, run the following command from the root of the repository:

```bash
kubectl create configmap inventree-nginx-config --from-file=config/ --dry-run=client -o=yaml | kubectl apply -f -
```

Warning: This command will create or overwrite a ConfigMap named `inventree-nginx-config`.

Configuration
-------------

Before deploying, you will need to manually update the following variables in `inventree-config-configmap.yaml`:

-   `INVENTREE_DB_ENGINE`: "mysql" or "postgresql"
-   `INVENTREE_DB_HOST`
-   `INVENTREE_DB_PORT`
-   `INVENTREE_DB_NAME`
-   `INVENTREE_DB_USER`
-   `INVENTREE_DB_PASSWORD`
-   `INVENTREE_ADMIN_USER`
-   `INVENTREE_ADMIN_EMAIL`
-   `INVENTREE_ADMIN_PASSWORD`

Here's an example configuration:


```
INVENTREE_DB_ENGINE: "mysql"
INVENTREE_DB_HOST: "your_database_host"
INVENTREE_DB_PORT: "your_database_port"
INVENTREE_DB_NAME: "inventree"
INVENTREE_DB_USER: "your_database_user"
INVENTREE_DB_PASSWORD: "your_database_password"
INVENTREE_ADMIN_USER: "your_admin_username"
INVENTREE_ADMIN_EMAIL: "your_admin_email"
INVENTREE_ADMIN_PASSWORD: "your_admin_password"
```

Replace the example values with your actual configuration.

Deployment
----------

After you have updated the configuration, use `kubectl apply -f` on each of these files:

-   `inventree-app-deployment.yaml`
-   `inventree-app-service.yaml`
-   `inventree-cache-deployment.yaml`
-   `inventree-cache-service.yaml`
-   `inventree-config-configmap.yaml`
-   `inventree-data-persistentvolumeclaim.yaml`
-   `inventree-worker-deployment.yaml`

The container will automatically run any required database migrations upon startup.

Additional Information
----------------------

The `inventree-app-deployment.yaml` contains both `inventree-server`, which serves the actual application, and `inventree-proxy`, which serves static assets such as scripts and images. This is because some environments only provide persistent volumes that cannot be mounted to multiple pods simultaneously (e.g. hcloud).

This repository also includes an `ingress-example.yaml` file. However, this file assumes that you are using nginx as the ingress class and a LetsEncrypt ClusterIssuer. You will still need to specify your public hostname and set up the appropriate DNS records.

Please note, some users might require a LoadBalancer resource instead of an Ingress. This is not included in the repository and will need to be configured separately as per your environment and requirements.