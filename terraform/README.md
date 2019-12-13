# Create a Azure Kubernetes Service using Terraform

This repository contain terraform code to provision Azure Kubernetes Service (AKS) and it required components.

## Required Tooling
- Terraform >= 0.12
- Azure CLI >= 2.0

## Pre-requisites

Create a service principal for AKS

```sh
az ad sp create-for-rbac -n "aks-sp" --skip-assignment

Changing "aks-sp" to a valid URI of "http://aks-sp", which is the required format used for service principal names
AppId                                 DisplayName    Name           Password                              Tenant
------------------------------------  -------------  -------------  ------------------------------------  ------------------------------------
907c0864-ed2c-4643-b5b9-000000000000  aks-sp         http://aks-sp  00000000-0000-0000-0000-000000000000  00000000-0000-0000-0000-000000000000
```

Update `main.tf` file and replace service principal `client_id` with `AppId` and `client_secret` with `Password`. 

For Example:

```sh
 service_principal = {
    client_id     = "2f61810e-7f8d-49fd-8c0e-000000000000"
    client_secret = "57f8b670-012d-42b2-a0f8-000000000000"
  }
```

## Provisioning with Terraform

Go to terraform directory and run the following commands:


```sh
terraform init    # => Initializes Terraform, gets modules...
terraform plan    # => Shows the plan (what is going to be created...)
terraform apply   # => Apply changes
```

## Configure `kubectl` to see your new AKS cluster
Run the following commands to configure kubernetes clients using kubeconfig output by Terraform

```sh
terraform output kube_config > ~/.kube/aksconfig
export KUBECONFIG=~/.kube/aksconfig
```

### Verify that your AKS cluster is healthy

It is possible to verify the health of the AKS cluster deployment by looking at the status.

```sh
kubectl get nodes
NAME                              STATUS   ROLES   AGE     VERSION
aks-default-14145696-vmss000000   Ready    agent   3m43s   v1.13.12
aks-default-14145696-vmss000001   Ready    agent   3m47s   v1.13.12
```

## Cleanup
Finally, you can cleanup the Terraform managed infrastructure using destroy command:

```sh
terraform destroy -force
```