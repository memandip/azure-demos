# Provision an AKS Cluster (Azure)

> The Azure Kubernetes Service (AKS) is fully managed Kubernetes service for deploying, managing and scaling containterized applications of Azure.
> In this tutorial, we will deploy a 2 node **AKS** cluster on our default **VPC** using Terraform then access its Kubernetes dashboard.

## Why deploy with Terraform ?
> While we could use the built-in Azure provisioning processes (UI, CLI) for AKS clusters, Terraform provides you with several benefits:
- **Unified Workflow** - If you are already deploying infrastructure to Azure with Terraform, your AKS cluster can fit into that workflow. You can also deploy applications into your AKS cluster using Terraform.
- **Full Lifecycle Management** - Terraform doesn't only create resources, it updates, and deletes tracked resources without requiring you to inspect the API to identify those resources.
- **Graph of Relationships** - Terraform understands dependency relationships between resources. For example, an Azure Kubernetes cluster need to be associated with a resource group, Terraform won't attempt to create the cluster if the resource group failed to create.

## Prerequisites
- an [Aure account](https://portal.azure.com/#home)
- a configured Azure CLI
- `kubectl`

## Set up and initialize your Terraform workspace

Files used to provision the AKS cluster.
1. [aks-cluster.tf](./aks-cluster.tf) provisions a resource group and an AKS cluster. The `default_node_pool` defines the number of VMs and VM type the cluster uses.
2. [variales.tf](./variables.tf) declares the `appId` and `password` variables to authenticate to Azure.
3. [outputs.tf](./outputs.tf) declares values that can be useful to interact with your AKS cluster.
4. [versions.tf](./versions.tf) sets the Terraform version to at least 0.14 and defines the [required_provider](https://www.terraform.io/docs/language/providers/requirements.html#requiring-providers) block.

## Create an Active Directory service principal account
- There are many ways to authenticate to the Azure provider. IN this tutorial, we will use an Active Directory service principal account. We can learn how to authenticate using a different method [here](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs#authenticating-to-azure).
- First, we need to create an Active Directory service principal account using the Azure CLI.
  - ```
    az ad spcreate-for-rbac --skip-assignment
    ```

## Update your `terraform.tfvars` file
> Replace the values in your `terraform.tfvars` file with your `appId` and `password`. Terraform will use these values to authenticate to Azure before provisioning your resources. Your `terraform.tfvars` file should look like the following.
```
# terraform.tfvars
appId    = "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa"
password = "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa"
```    

## Initialize Terraform
> After your have saved your customized variables file, initialize your Terraform workspace, which will download the provider and initialize in with the values provided in your `terraform.tfvars` file.
```
terraform init
```

## Provision the AKS cluster
> In your initialized directory, run `terraform apply` and review the planned actions. Your terminal output should indicate the plan is running and what resources will be created.
```
terraform apply
```
- You will see this terraform apply will provision an Azure resource group and an AKS cluster. Confirm the apply with a `yes`.
- This process should take approximately 10 minutes. Upon successful application, your terminal printes the outputs defined in `outputs.tf`.

## Configure kubectl
- Now that we've provisioned our AKS cluster, we need to configure `kubectl`.
- Run the following command to retrieve the access credentials for your cluster and automatically configure `kubectl`.
```
az aks get-credentials --resource-group $(terraform output -raw resource_group_name) --name $(terraform output -raw kubernetes_cluster_name)
```
- The [resource group name](https://github.com/hashicorp/learn-terraform-provision-aks-cluster/blob/master/outputs.tf#L1) and [Kubernetes Cluster name](https://github.com/hashicorp/learn-terraform-provision-aks-cluster/blob/master/outputs.tf#L5) correspond to the output variables showed after the successful Terraform run.

## Access Kubernetes Dashoard
> To verify that your cluster's configuration, visit the Azure Portal's Kubernetes resource view. [Azure recommends](https://docs.microsoft.com/en-us/azure/aks/kubernetes-dashboard#start-the-kubernetes-dashboard) using this view over the default Kuernetes dashboard, since the AKS dashoard add-on is deprecated for Kubernetes versions 1.19+.
- Run the following command to generate the Azure portal link.
  - ```
    az aks browse --resource-group $(terraform output --raw resource_group_name) --name $(terraform output --raw kubernetes_cluster_name)
    ```
- Go to the URL in your preferred rowser to view the Kuernetes resource view.

## Clean up your workspace
> Remember to destroy any resources you create once you are done with this tutorial. Run `destroy` command and confirm with `yes` in your terminal.
```
terraform destroy
```