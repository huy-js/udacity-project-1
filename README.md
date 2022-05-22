# udacity-project-1

### Policy
# Create the Policy Definition
az policy definition create --name tagging-policy  --rules Policy/tagging_policy.rules.json --param Policy/tagging_policy.params.json

# Create the Policy Assignment
az policy assignment create --name tagging-policy --policy tagging-policy --param Policy/tagging-policy.assignment.json

az policy assignment list

### Packer

a. Create a resource group for your image (`udacity-packerimage-rg`)

```sh
az group create -l eastus -n udacity-packerimage-rg
```

b. Modify the variable sections into packer file `server.json` so the image builds in your preferred region, and use the resource group name created above

c. Create a RBAC (Service principal) account, using the following command:

```sh
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

```sh
az account show --query "{ subscription_id: id }" or client_id, client_secret, tenant_id etc...
```

d. Export the following variables that will be used in `server.json`

```sh
export CLIENT_ID="<rbac account client_id value from above command>"
export CLIENT_SECRET="<rbac account client_secret value from above command>"
export TENANT_ID="<rbac  account tenant_id value from above command>"
export SUBSCRIPTION_ID="<subscription_id value from above command> "

You can check variable: echo $CLIENT_ID or other variable
```

e. Run the following command to build your server image. 

```sh
packer build Packer/server.json
```

### Terraform
a. Therefore, basically use the `terraform.tfvars` to add yor variables

- use the same values , for example, of the commands that you executed already in packer

**NOTICE: use tagging-policy in policy part to create virtual machine**

```sh
az account show --query "{ subscription_id: id }"
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```
You can use client_id, client_secret... in Packer part 

a. Go to `Terraform` directory and Run `terraform init` to prepare your directory for terraform

```sh
cd Terraform
terraform init
```

b. To create a plan named "solution.plan"

```sh
terraform plan -out solution.plan
```

c. Apply the plan:

```sh
terraform apply "solution.plan"
```

d. You can get as an output result
Example:

```sh
Outputs:

lb_url = "http://52.165.135.210/"

access url result:
Hello World!!!
```

e. Many resources were created ( 17 in total in our case, using 3 VM)

### Destroying the Resources (deletion process)

- delete the Terraform resources

```sh
pwd
terraform plan -destroy -out solution.destroy.plan
terraform apply "solution.destroy.plan"
```

- delete the packer image

```sh
az image delete --name udacity-server-image  --resource-group udacity-packerimage-rg
```

- delete the Resource Group used in Packer

```sh
az group delete -n udacity-packerimage-rg
```

- delete the Policy Assignment

```sh
az policy assignment delete --name tagging-policy
```

- delete the Policy Definition

```sh
az policy definition delete --name tagging-policy
```