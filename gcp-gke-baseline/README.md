# GCP GKE Baseline Cluster InfraChart

The GCP GKE Baseline Cluster InfraChart provides a streamlined approach to deploying a secure, private Kubernetes
cluster on Google Cloud Platform (GCP). It automates the setup of essential infrastructure components, simplifying the
process for development teams to quickly provision production-ready Kubernetes environments.

The resources defined by this chart are available in the [templates](templates) folder. Configuration parameters are
managed through [values.yaml](values.yaml).

---

## Included Cloud Resources

This GCP GKE Baseline Chart creates the following GCP resources:

1. **GCP Project**:

    * Configures the GCP project with specific APIs enabled:

        * Compute Engine API
        * Kubernetes Engine API
        * Cloud DNS API
        * IAM API
    * Disables the default network for enhanced security

2. **Custom VPC Network**:

    * Regional routing mode
    * Manual subnet creation (auto subnet creation disabled)

3. **Subnet Configuration**:

    * Primary CIDR range specified explicitly
    * Secondary IP ranges defined for Kubernetes Pods and Services
    * Private Google Access enabled

4. **Cloud NAT (Network Address Translation)**:

    * Managed NAT service for secure internet egress from private clusters

5. **GKE Kubernetes Cluster (Private)**:

    * Private cluster endpoint
    * Specific CIDR for master nodes
    * Node autoscaling configured
    * Configurable public node access (disabled by default)
    * Option to enable or disable network policies (Calico)
    * Workload Identity integration (enabled by default)

6. **GKE Node Pool**:

    * Autoscaling node pools with configurable machine types and disk sizes
    * Supports standard, SSD, or balanced persistent disks
    * Optional support for Spot (preemptible) instances

---

## Chart Input Values

The following values must be provided or will default as specified in [values.yaml](values.yaml):

| Input Parameter                 | Description                            | Example            | Required/Default |
|---------------------------------|----------------------------------------|--------------------|------------------|
| `project_id`                    | GCP Project ID                         | my-gke-demo-123    | Required         |
| `parent_type`                   | Parent type (organization/folder)      | organization       | Required         |
| `parent_id`                     | Numeric ID for parent resource         | 123456789012       | Required         |
| `billing_account_id`            | GCP Billing Account ID                 | 0123AB-4567CD-89EF | Required         |
| `vpc_name`                      | Custom VPC name                        | gke-vpc            | Required         |
| `region`                        | GCP region                             | us-central1        | Required         |
| `subnet_name`                   | Name of the custom subnet              | gke-subnet         | Required         |
| `subnet_primary_cidr`           | Primary CIDR range for subnet          | 10.0.0.0/17        | Required         |
| `pods_secondary_range_name`     | Secondary CIDR range name for Pods     | pods               | Required         |
| `pods_secondary_cidr`           | CIDR range for Pods                    | 10.1.0.0/17        | Required         |
| `services_secondary_range_name` | Secondary CIDR range name for Services | services           | Required         |
| `services_secondary_cidr`       | CIDR range for Services                | 10.3.0.0/22        | Required         |
| `private_ip_google_access`      | Enable Private Google Access           | true               | Required         |
| `router_nat_name`               | Name for Cloud NAT                     | gke-nat            | Required         |
| `cluster_name`                  | Name of GKE cluster                    | gke-demo           | Required         |
| `cluster_location`              | Location for GKE cluster (region/zone) | us-central1        | Required         |
| `master_ipv4_cidr`              | CIDR for master nodes                  | 172.16.0.16/28     | Required         |
| `enable_public_nodes`           | Nodes with external IPs                | false              | Required         |
| `release_channel`               | GKE release channel                    | REGULAR            | Required         |
| `disable_network_policy`        | Disable Calico network policies        | false              | Required         |
| `disable_workload_identity`     | Disable Workload Identity              | false              | Required         |
| `node_pool_name`                | Node pool name                         | default-pool       | Required         |
| `node_pool_machine_type`        | Machine type for node pool             | e2-medium          | Required         |
| `node_pool_disk_size_gb`        | Disk size for node pool (GB)           | 100                | Required         |
| `node_pool_disk_type`           | Disk type for node pool                | pd-standard        | Required         |
| `node_pool_image_type`          | Node OS image type                     | COS\_CONTAINERD    | Required         |
| `node_pool_spot`                | Enable Spot VMs                        | false              | Required         |
| `node_pool_min_nodes`           | Minimum nodes in autoscaler            | 1                  | Required         |
| `node_pool_max_nodes`           | Maximum nodes in autoscaler            | 3                  | Required         |

---

## Chart Customization

Post-deployment modifications are supported and can be performed directly within your GCP console or via Planton Cloud
for further customization.

---

## Important Notes

* The GKE Baseline Chart is designed for initial setup. Subsequent management of individual resources should be
  conducted directly through GCP.
* Ensure CIDR block ranges do not overlap existing networks to prevent connectivity issues.

---

© 2025 Planton Cloud. All rights reserved.
