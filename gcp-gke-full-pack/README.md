# GCP GKE Cluster Full Pack InfraChart

The GCP GKE Cluster Full Pack InfraChart provides a comprehensive and fully loaded Kubernetes cluster on Google Cloud
Platform. It simplifies provisioning of critical infrastructure components necessary for deploying and managing robust
Kubernetes workloads, enabling quick and efficient deployments.

The chart resources and configuration parameters are managed through the [templates](templates)
and [values.yaml](values.yaml) files.

---

## Included Cloud Resources

This InfraChart provisions the following GCP resources:

### 1. GCP Project

* New or existing project setup with specified parent (organization or folder)
* Associated billing account configuration
* Pre-enabled APIs (Compute Engine, Kubernetes Engine, Cloud DNS, IAM)

### 2. Custom VPC Network

* Manually created subnetworks
* Regional routing mode
* Configured secondary IP ranges for Kubernetes Pods and Services

### 3. Cloud NAT

* Managed NAT configuration for private instances to access the internet

### 4. Private GKE Cluster

* Private control plane with customizable master IP CIDR
* Node pools without public IP addresses (optional)
* Configurable regional or zonal deployment
* Autoscaling node pools with custom machine types and disk specifications
* Workload Identity and Network Policy support

### 5. Kubernetes Add-ons

* Cert Manager
* Elastic Operator
* External DNS
* External Secrets
* Ingress NGINX
* Istio (Ingress Gateway)
* Kafka Operator
* PostgreSQL Operator
* Solr Operator

---

## Chart Input Values

The following values must be provided or defaulted as defined in [values.yaml](values.yaml):

| Input Parameter             | Description                                   | Example              | Required/Default |
|-----------------------------|-----------------------------------------------|----------------------|------------------|
| `project_id`                | GCP Project ID                                | my-gke-demo-123      | Required         |
| `parent_type`               | Parent resource type (organization or folder) | organization         | Required         |
| `parent_id`                 | Numeric ID for parent org/folder              | "123456789012"       | Required         |
| `billing_account_id`        | Billing Account ID                            | 0123AB-4567CD-89EFGH | Required         |
| `region`                    | GCP region for resources                      | us-central1          | Required         |
| `vpc_name`                  | VPC name                                      | gke-vpc              | Required         |
| `subnet_name`               | Subnetwork name                               | gke-subnet           | Required         |
| `subnet_primary_cidr`       | Primary CIDR block for subnet                 | 10.0.0.0/17          | Required         |
| `pods_secondary_cidr`       | CIDR for Kubernetes Pods                      | 10.1.0.0/17          | Required         |
| `services_secondary_cidr`   | CIDR for Kubernetes Services                  | 10.3.0.0/22          | Required         |
| `master_ipv4_cidr`          | CIDR block for GKE master endpoint            | 172.16.0.16/28       | Required         |
| `node_pool_machine_type`    | Node pool compute instance type               | e2-medium            | Required         |
| `node_pool_disk_size_gb`    | Node pool disk size in GB                     | 100                  | Required         |
| `node_pool_spot`            | Use spot instances for node pool              | false                | Default          |
| `release_channel`           | Kubernetes release channel                    | REGULAR              | Default          |
| `disable_workload_identity` | Disable Workload Identity                     | false                | Default          |

---

## Customization & Management

The GKE Cluster Full Pack InfraChart resources can be customized post-deployment directly through your Google Cloud
Console or managed through Planton Cloud.

---

## Important Notes

* Ensure DNS, NAT, and network configurations align with your organization's compliance and operational standards.
* Subsequent updates and management of Kubernetes addons and infrastructure should be handled carefully to maintain
  stability and security.

---

© 2025 Planton Cloud. All rights reserved.
