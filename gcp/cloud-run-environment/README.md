# GCP Cloud Run Environment

The **GCP Cloud Run Environment** InfraChart provisions all cloud resources required to run containerized services on Google Cloud Run—with optional support for PostgreSQL database, storage bucket, Docker repository, service account, and DNS zone.

Like the AWS ECS chart, it leverages **Jinja-based conditionals**, so you can turn features on or off with boolean flags, making it flexible for different environment needs.

Chart manifests live in the [`templates`](templates) directory; every tunable value is documented in [`values.yaml`](values.yaml).

---

## Included Cloud Resources (conditional)

| Resource                       | Always created | Controlled by boolean flag   |
|--------------------------------|----------------|------------------------------|
| **Frontend Cloud Run Service** | Yes            | —                            |
| **Backend Cloud Run Service**  | *No*           | `backendServiceEnabled`      |
| **DNS Zone**                   | *No*           | `dnsZoneEnabled`             |
| **Docker Repository**          | *No*           | `dockerRepoEnabled`          |
| **PostgreSQL Database**        | *No*           | `postgresEnabled`            |
| **Storage Bucket**             | *No*           | `storageBucketEnabled`       |
| **Service Account**            | *No*           | `serviceAccountEnabled`      |

### How the Boolean Flags Work

Each optional resource is controlled by a boolean flag in `values.yaml`:

* **`backendServiceEnabled: true`** → Creates a second Cloud Run service for backend applications
* **`dnsZoneEnabled: true`** → Creates a GCP DNS Zone for the specified domain
* **`dockerRepoEnabled: true`** → Creates an Artifact Registry Docker repository for container images
* **`postgresEnabled: true`** → Creates a Cloud SQL PostgreSQL instance
* **`storageBucketEnabled: true`** → Creates a GCS bucket for object storage
* **`serviceAccountEnabled: true`** → Creates a service account with a JSON key

Set any flag to `false` to skip that resource entirely.

---

## Chart Input Values

Booleans are shown as **unquoted YAML booleans** (`true`/`false`) to avoid string/boolean casting issues.

### GCP Configuration

| Parameter           | Description                  | Example / Default          | Required / Default         |
|---------------------|------------------------------|----------------------------|----------------------------|
| **gcp_project_id**  | GCP Project ID               | `planton-cloud-testing`    | Required                   |
| **gcp_region**      | GCP region for all resources | `us-central1`              | Default `us-central1`      |

### Service Configuration

| Parameter                    | Description                           | Example / Default | Required / Default   |
|------------------------------|---------------------------------------|-------------------|----------------------|
| **frontend_service_name**    | Name of the frontend Cloud Run service| `frontend`        | Default `frontend`   |
| **frontend_service_port**    | Port for the frontend service         | `8080`            | Default `8080`       |
| **backend_service_name**     | Name of the backend Cloud Run service | `backend`         | Default `backend`    |
| **backend_service_port**     | Port for the backend service          | `8080`            | Default `8080`       |
| **backendServiceEnabled**    | Create backend Cloud Run service      | `true` / `false`  | **Default:** `true`  |

### Optional DNS Zone

| Parameter           | Description                    | Example / Default | Required / Default   |
|---------------------|--------------------------------|-------------------|----------------------|
| **domain_name**     | Custom domain name for DNS zone| `example.com`     | Default `example.com`|
| **dnsZoneEnabled**  | Create GCP DNS Zone            | `true` / `false`  | **Default:** `true` |

### Optional Docker Repository

| Parameter             | Description                  | Example / Default | Required / Default   |
|-----------------------|------------------------------|-------------------|----------------------|
| **dockerRepoEnabled** | Create Docker repository     | `true` / `false`  | **Default:** `true` |
| **docker_repo_name**  | Name of the Docker repository| `docker-repo`     | Default `docker-repo`|

### Optional PostgreSQL Database

| Parameter                    | Description                         | Example / Default          | Required / Default              |
|------------------------------|-------------------------------------|----------------------------|---------------------------------|
| **postgresEnabled**          | Create PostgreSQL database instance | `true` / `false`           | **Default:** `true`            |
| **postgres_instance_name**   | Name of the PostgreSQL instance     | `postgres`                 | Default `postgres`              |
| **postgres_tier**            | PostgreSQL machine tier             | `db-f1-micro`              | Default `db-f1-micro`           |
| **postgres_storage_gb**      | Storage size in GB                  | `10`                       | Default `10`                    |
| **postgres_version**         | PostgreSQL version                  | `POSTGRES_15`              | Default `POSTGRES_15`           |
| **postgres_root_password**   | Root password (rotate after deploy) | `change-me-immediately`    | Default `change-me-immediately` |

### Optional Storage Bucket

| Parameter                 | Description              | Example / Default  | Required / Default   |
|---------------------------|--------------------------|--------------------|----------------------|
| **storageBucketEnabled**  | Create storage bucket    | `true` / `false`   | **Default:** `true` |
| **storage_bucket_name**   | Name of the storage bucket| `storage-bucket`  | Default `storage-bucket`|

### Optional Service Account

| Parameter                  | Description                       | Example / Default      | Required / Default   |
|----------------------------|-----------------------------------|------------------------|----------------------|
| **serviceAccountEnabled**  | Create Service Account with JSON key| `true` / `false`     | **Default:** `true` |
| **service_account_id**     | Service account ID                | `app-service-account`  | Default `app-service-account`|

> **Tip:** All resources are enabled by default for a complete environment setup. Toggle feature flags to `false` per environment if you don't need certain resources (e.g., disable PostgreSQL for frontend-only deployments).

---

## Service Configuration Details

Both the frontend and backend Cloud Run services are configured with:

- **Default Image**: `nginx:latest` (replace with your actual container images after deployment)
- **Resources**: 1 CPU, 512MB memory
- **Scaling**: Min 0, Max 1 replica (adjust as needed)
- **Concurrency**: 80 requests per container
- **Authentication**: Public (unauthenticated access allowed)

### Environment Variables

Each service includes placeholder environment variables that you should customize:

- **`SERVICE_NAME`**: Set to the service name (frontend or backend)
- **`ENV`**: Set to the environment slug (dev, staging, prod)

You can add additional environment variables or replace these after deployment by updating the Cloud Run service configuration.

---

## IAM Permissions Note

When `serviceAccountEnabled: true`, the chart creates a service account with a JSON key but **does not assign IAM permissions**.

**Required Manual Steps:**

1. After deployment, retrieve the service account email from the outputs
2. Grant necessary IAM roles using GCP Console or `gcloud` CLI
3. Example: To grant Storage Admin permissions:
   ```bash
   gcloud projects add-iam-policy-binding PROJECT_ID \
     --member="serviceAccount:SERVICE_ACCOUNT_EMAIL" \
     --role="roles/storage.admin"
   ```
4. Download the JSON key from the GCP Console or retrieve it via API
5. Share the key securely with developers for application authentication

---

## PostgreSQL Database Configuration

When `postgresEnabled: true`, the PostgreSQL instance is created with:

- **Authorized Networks**: `0.0.0.0/0` (open to all IPs by default)
- **Root Password**: Uses the value from `postgres_root_password`

### Important Security Steps

1. **Rotate the root password immediately** after deployment
2. **Restrict authorized networks** to only your application IPs or VPC
3. **Create application-specific database users** instead of using root
4. **Enable Cloud SQL Proxy** for secure connections from Cloud Run services

---

## Docker Repository (Artifact Registry)

When `dockerRepoEnabled: true`, an Artifact Registry Docker repository is created for storing container images.

**Usage:**

1. Authenticate Docker to the registry:
   ```bash
   gcloud auth configure-docker REGION-docker.pkg.dev
   ```
2. Tag your images:
   ```bash
   docker tag my-app:latest REGION-docker.pkg.dev/PROJECT_ID/REPO_NAME/my-app:latest
   ```
3. Push images:
   ```bash
   docker push REGION-docker.pkg.dev/PROJECT_ID/REPO_NAME/my-app:latest
   ```
4. Update the Cloud Run service to use your pushed images

---

## Customization & Management

* Toggle feature flags per environment (dev vs prod) in a higher-priority `values.yaml`
* Change service ports to expose different container ports
* All resources are independent (no cross-resource dependencies), so they deploy in parallel
* Update container images, CPU, memory, and scaling parameters post-deployment as needed
* Add custom domains to Cloud Run services by enabling the `dns` block in the service templates

---

## Important Notes

* **DNS Zone**: Ensure your domain is registered and delegated to GCP before enabling `dnsZoneEnabled`
* **PostgreSQL Password**: The default password `change-me-immediately` should be rotated immediately after deployment for security
* **Authorized Networks**: The default `0.0.0.0/0` allows connections from anywhere. Restrict this in production environments
* **Service Account**: IAM permissions must be granted manually after the service account is created
* **Container Images**: Both services default to `nginx:latest`. Replace with your actual application images after deployment
* **Parallel Deployment**: All resources are independent, so they deploy in parallel (no dependency graph)
* **Environment Variables**: Customize the placeholder `SERVICE_NAME` and `ENV` variables for your applications

---

© 2025 Planton Cloud. All rights reserved.
