# tf_k8deploy

<!-- TOC -->

- [tf_k8deploy](#tf_k8deploy)
- [Deploy a New Cluster with Terraform](#deploy-a-new-cluster-with-terraform)
  - [Requirements](#requirements)
  - [Step 1 - Deploy GKE Cluster](#step-1---deploy-gke-cluster)
  - [Configure access to your GKE Cluster with kubectl](#configure-access-to-your-gke-cluster-with-kubectl)

<!-- /TOC -->
---
# Deploy a New Cluster with Terraform

We will deploy a new GKE cluster with **Terraform** to Google Cloud with GitHub Actions.

## Requirements

* Google Cloud Account
  * Create a [New GCP Account](https://cloud.google.com/free) with $300 in free credits (credit card required)
* Google Cloud `project_id`
* Google Cloud Region
  * Find the cheapest instance by provider and `region` at [Instance Pricing](https://www.instance-pricing.com)
* Google Cloud Storage `tf-k8deploy`
* Google Cloud SDK `gcloud`
* `kubectl`
* `terraform`

## Step 1 - Deploy GKE Cluster
Navigate to code repository for GKE Deployment 

```
cd learn-terraform-provision-gke-cluster 
```
Initialize the Terraform code with the following command

```
terraform init
gcloud auth application-default login
```
Open the file `terraform.tfvars` and change the values to your GCP.

```
project_id = "tf-k8deploy"
region     = "us-central1"
```
Provision the GKE Cluster with the following command 

```
terraform plan
terraform apply
``` 
The above will run through the code and confirm that terraform will deploy a total number of resources (2-node separately managed node pool GKE cluster using Terraform. This GKE cluster will be distributed across multiple zones for high availability. it will also create VPC) you should respond "yes" to this in the "enter a value" prompt if you wish to auto approve this you could run 

```
terraform apply --auto-approve
```
The above process will likely take around 5-10 minutes to complete successfully. Ensure that the following objects were created using command `terraform state list`.

```
google_compute_network.vpc
google_compute_subnetwork.subnet
google_container_cluster.primary
google_container_node_pool.primary_nodes
```
Delete the GKE Cluster when not in used to save on cost.

```
terraform destroy
```

## Configure access to your GKE Cluster with kubectl 

``` 
gcloud auth login
gcloud config set project PROJECT_ID
gcloud container clusters get-credentials $(terraform output -raw kubernetes_cluster_name) --region $(terraform output -raw region)
```

confirm now that you have connected to the correct GKE Cluster with the following 

```
kubectl get nodes
```

You can also check the contexts available within the KUBECONFIG file by using the following command 

```
kubectl config get-contexts
```

> At this stage we have a fully working Kubernetes cluster running in Google Cloud.