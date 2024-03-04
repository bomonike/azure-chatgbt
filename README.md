This repo provides a hands-on step-by-step cookbook, with commentary, on how to create a system for the "Cognitive Fifth Industrial Revolution" (5IR) enabled by AI "collaborative robots".

Our motivation is to avoid this:

> "It took us 4 days to come up with a ChatGPT app, then it took 4 months to take it public to paying customers."

The complexity of the diagram below is why it takes so long. And it doesn't include everything.

TODO: Click on this diagram for a video that gradually reveals each component and feature.

<a target="_blank" href="https://res.cloudinary.com/dcajqrroq/image/upload/v1709526045/azure-chatgpt-arch-240303-1920x1080_fn2lv5.png"><img alt="azure-chatgpt-arch-240303-1920x1080.png" src="https://res.cloudinary.com/dcajqrroq/image/upload/v1709526045/azure-chatgpt-arch-240303-1920x1080_fn2lv5.png"></a>

1. This GitHub repo README shows all the steps and code to customize our template for
1. technical people: Administrators, Platform Engineers, and Developers to quickly create a scalable and secure AI system for use by non-technical <strong>customers</strong>.

1. Before running the automation, at GoDaddy or another name registrar, pay for <strong>Domain names</strong> to be used.
1. Domain names and IP Addresses are pasted in the <strong>terraform.tfvars</strong> file, which Terraform modules reference to obtain customized values during deployment.
1. Use the Azure Portal GUI to obtain Azure accounts and the Subscription to use.
1. So that this and other secrets never end up being exposed in GitHub, save them in the <strong>Akeyless</strong> cloud because your instance of <strong>Azure Key Vault</strong> can be easily deleted.
1. The same goes for Storage Accounts. 

1. The Subscription ID is provided to login to run batch Bicep, Terraform, Helm, and Shell scripts. The (roles_build) script defines admin users and their permissions.

1. There is not yet automation to agree to Microsoft's Responsible AI for Cognitive AI utilities, so that has to be done using the Portal GUI.
1. Additional prerequisites are to manually obtain "capacity units" for Microsoft Fabric, a service name for Azure OpenAI, API keys, etc.

1. When Terraform runs, it sets up the <strong>virtual network</strong> and all resources that run within it, including Private DNS Zones, Public IP addresses for the bastion_host used to enter the network.

1. File <strong>00-variables.sh</strong> contains custom values for Helm and Ansible to build within Kubernetes.

1. Setup Bicep and Terraform Infrastructure code to be <strong>scanned<strong> so that security vulnerabilities can be identified before resources are created.
1. Setup application code to be <strong>Dockerize</strong> as containers into the Azure Container Registry (ACR).
1. Application containers are loaded by the Kubernetes <strong>kub-system</strong>
1. AKS (Azure Kubernetes Service) provides management functionality to Kubernetes.
1. AKS manages two node pools: a System Agent Pool and a User Agent Pool, each in a separate Subnet.
1. An API Server subnet runs the PodSubnet that house apps.

1. The IaC creates all resources within a <strong>virtual_network</strong>, aka VNet.
1. Developers and Administrators reach servers using a public IP via a Bastion Host (jumpbox) VM created dynamically.
1. For better security, a VM Subnet, if enabled, holds a virtual machine for a jumpbox in the same virtual network of the AKS cluster.

1. When the sample apps are loaded -- the sample Q&A chatbot (like a "Magic 8 ball" from the 1970s) and a doc query app.

   NOTE: <a target="_blank" href="https://learn.microsoft.com/en-us/training/modules/introduction-power-virtual-agents/3-build-basic-chatbot">Microsoft Power Virtual Agents</a> can create "bots". But its logic is defined programmatically. ChatGPT bots interact using Natural Language based on new LLM containing a large corpus of vectored words.

1. The app is used by customers, so we have a registered <strong>domain name</strong> with a public IP address. 
1. For scalability, we have a load balancer in front of a firewall reaching an NGINX Ingress Controller cluster.
1. Their GUI is generated using Chainlit that looks like OpenAI's ChatGPT web GUI.
1. The apps are intelligent because they recognize English language, enabled by API calls to the OpenAI LLM ChatGPT, but augmented by private custom data (using LangChain to reference vectors in a ChromaDB vector store).

1. The custom vector store is updated in real-time when Eventstreams to a Microsoft Fabric KQL database trigger alerts from a Reflex within Data Activator.
1. Automated responses include the creation and calendar invite to a new meeting based on lookups of participant availability in Microsoft Graph, orchestrated by Power Automate.
1. <a target="_blank" href="https://www.youtube.com/watch?v=JVaPK2iXVPI">VIDEO</a>: <a target="_blank" href="https://www.serverlesssql.com/fabric-link-for-dataverse-whats-in-the-box/">Microsoft Fabric linked to Dataverse in Dynamics 365 and Power Apps</a> <a target="_blank" href="https://adatis.co.uk/dataverse-integration-with-microsoft-fabric/">integration with Dataverse</a> "Common Data Model" in Power apps.

1. SMS texts & RCS videos to mobile phones throughout the world are sent through a NAT Gateway to the Twilio service.
1. The Synapse workspace is within a <a target="_blank" href="https://www.serverlesssql.com/synapse-analytics-managed-vnet-for-the-developer/">managed VNet</a> (Virtual Network) with Managed Private Endpoints to keep the Data Lake Gen2 storage away from public access.
1. Developers are granted access based their IP address being on the IP AllowList (Whitelist).

1. The cluster is monitored by Prometheus feeding Grafana dashboards watched by Kubernetes Monitoring Engineers.

1. Some Terraform (main.tf) files define least-privilege RBAC (Role-Based Access Controls) to reduce the "blast radius" in case credentials are stolen due to phishing, etc. 
<br /><br />

<hr />

## Phases and Steps

<a href="#A.">A. Establish Prerequisites:</a> domain name, keys, etc.<br />
<a href="#B.">B. Run Terraform to establish Azure resources</a><br />
<a href="#C.">C. Run Shell Scripts:</a><br />

<a href="#D.">D. Clean up resources</a><br />
<a href="#E.">E. Use Docker and Helm to establish Kubernetes cluster</a><br />
<a href="#F.">F. Test functionality and performance</a><br />
<a href="#G.">G. Roadmap for more</a><br />

<hr />

The steps to make this happen.

Commentary about the technology used is presented to explain the configurations.

<a name="A."></a>

### A. Establish Prerequisites:

1. <a href="#Install-Prerequisite-Utilities">Install Perequisite Utilities</a>
   1. <a href="#download-from-github">Download from GitHub</a>
1. <a href="#files-and-folders-in-this-repo">Files and Folders in This Repo</a>
1. <a href="#Secrets+Management">Secrets Management</a>
1. <a href="#Customize-Variable-Values">Customize Variable Values</a>
   1. <a href="#Domain">Domain host name & emails</a> dns_zone_name&dns_zone_resource_group_name
   1. <a href="#Establish-Azure-Account">Azure Account</a>
   1. <a href="#00-variables.sh">00-variables.sh</a>
   1. <a href="#terraform.tfvars">terraform.tfvars</a>

   1. <a href="#domain">domain, subdomain</a>
   1. ssh_public_key 
   1. grafana_admin_user_object_id&service_account_name 
   1. chainlit&service_account_name 

<a name="B."></a>

### B. Run Terraform IaC to Establish Azure resources:

1. <a href="#Terraform+Providers">Terraform Providers</a>
1. <a href="#AKS+Resources+Deployed">AKS Resources Deployed</a>
   1. <a href="#Subnet+">Subnet IP Addresses</a>
1. <a href="#Infrastructure+Architecture-Diagram">Infrastructure Architecture Diagram</a>
1. <a href="#Terraform+Modules">Terraform Modules</a>
   1. <a href="#IAM">IAM</a>
   1. <a href="#Networking">Networking</a>
   1. <a href="#Private+Endpoints">Private Endpoints</a>
   1. <a href="#Azure+Private+DNS+Zones">Azure Private DNS Zones</a>
   1. <a href="#Network+Security+Groups">Network Security Groups</a>
   1. <a href="#Azure+Storage+Account">Azure Storage Account</a>
   1. <a href="#Azure+Key+Vault">Azure Key Vault</a>
   1. <a href="#Azure+OpenAI+Service">Azure OpenAI Service</a>
   1. <a href="#Monitoring">Monitoring</a>
   1. <a href="#Compute">Compute</a>

<a name="C"></a>

### C. Run Shell Scripts:

1. <a href="#01-build-docker-images.sh">01-build-docker-images.sh</a> 
1. <a href="#02-run-docker-container.sh">02-run-docker-container.sh</a> 
1. <a href="#03-push-docker-image.sh">03-push-docker-image.sh</a>

1. <a href="#04-create-nginx-ingress-controller.sh">04-create-nginx-ingress-controller.sh</a> 
1. <a href="#05-install-cert-manager.sh">05-install-cert-manager.sh</a>

1. <a href="#06-create-cluster-issuers.sh">06-create-cluster-issuers.sh</a> ?
1. <a href="#07-create-workload-managed-identity.sh">07-create-workload-managed-identity.sh</a> 

1. <a href="#08-create-service-account.sh">08-create-service-account.sh</a> 
1. <a href="#09-deploy-apps.sh">09-deploy-apps.sh</a> ?
1. <a href="#10-configure-dns.sh">10-configure-dns.sh</a> ?

<a name="D."></a>

D. <a href="#Clean+up+resources">Clean up resources</a>

<a name="E."></a>

### E. Use Docker and Helm to establish Kubernetes cluster:

1. <a href="#install-packages-for-chainlit-demo.sh">install-packages-for-chainlit-demo.sh</a> 
   1. <a href="#The-ChatGPT-Applications">The ChatGPT Applications</a>
   1. <a href="#OpenAI+LLM+Service">OpenAI LLM Service</a>
   1. <a href="#Prompts">Prompts</a>
   1. <a href="#Chainlit">Chainlit</a>
   1. <a href="#LangChain">LangChain</a>
   1. <a href="#ChromaDB">ChromaDB</a>
   1. <a href="#Other+Vector+Databases">Other Vector Databases</a>
   <br /><br />
1. <a href="#Fabric-Data+to-Vector-store">Fabric Data to Vector store</a>

1. <a href="#GitHub+Workflow+actions">GitHub Workflow Actions</a>
1. <a href="#Build+Custom+Docker+Conatainers+into+ACR">Build Custom Docker Containers into ACR</a>

1. <a href="#Kubernetes+Components">Kubernetes Components</a>

1. <a href="#Run+Deployment+Scripts">Run Deployment Scripts</a>

1. <a href="#Review+AKS+Resources+Deployed">Review AKS Resources Deployed</a>


<a name="F."></a>

### F. <a href="Test">Test</a> functionality and performance:

1. <a href="#Use-ChatGPT+Application">Use ChatGPT Application</a>


<hr />

<a name="Install-Prerequisite-Utilities"></a>

## Install Perequisite Utilities

This repo runs on Apple MacBook running macOS.

But NGINX commands in scripts run on Linux machines.

On your macOS machine, install these utilities, perhaps in one run of my <a target="https://wilsonmar.github.io/mac-setup/">mac-setup.zsh</a>:

1. macOS command-line (xcode-install)
1. Ruby, Homebrew
1. <tt>brew install --cask vscode</tt>
   * [Visual Studio Code](https://code.visualstudio.com/) installed on one of the [supported platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms) along with the [HashiCorp Terraform](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform).
   <br /><br />
1. brew install jq, git, python, miniconda, ai cli, terraform, curl, .net, prometheus, grafana
   * [Terraform v1.5.2 or later](https://developer.hashicorp.com/terraform/downloads).
   <br /><br />
1. <tt>brew install azurecli</tt>
   * Install Azure CLI version 2.49.0 or later installed. To install or upgrade, see [Install Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli).
   <br /><br />
   To verify the azcli version:<br />
   <tt>az --version</tt>

   ### Admin email 

   ### Licenses

1. An active [Azure subscription](https://docs.microsoft.com/en-us/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing). If you don't have one, create a [free Azure account](https://azure.microsoft.com/free/) before you begin.

   Microsoft Fabric

   PowerBI

   Microsoft 365

   ### Cognitive Responsible AI

1. Use the Azure Portal GUI to sign up for a <a href="https://wilsonmar.github.io/microsoft-ai/#services-table">Cognitive Multi-Service Resource</a> and check the box acknowledging terms of Responsible AI.

   <a name="Login"></a>

   ### Login into the Azure cloud

1. The deployment must be started by a `User Access Administrator` or `Owner` who has sufficient permissions to assign roles.

1. Set the Subscription:

   <pre><strong>az account set -s "${SUBSCRIPTION_ID}"
   </strong></pre>

1. Login to Azure using the default browser (such as Edge or Chrome):

   <pre><strong>az login</strong></pre>

   Alternately,

   <pre><strong>az login --use-device-code</strong></pre>

   In response to:
   <pre>To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <strong>FC3MU3JCX</strong> to authenticate.</pre>

   Highlight the code and press Command+C to copy to your invisible Clipboard.

   <pre><strong>open <a target="_blank" href="https://microsoft.com/devicelogin">https://microsoft.com/devicelogin</a></strong></pre>

   Paste the code and select the email of the administrator.

   Provide the password for the admin account.

   "Enter the code displayed in the authenticator app on your mobile phone."

   Open Auth0 to select "Microsoft" to get the code to type in the browser page.

   When you see "You have logged into Microsoft Azure!", close the browser tab created.

   If "No subscriptions found" appears, use the Azure Portal GUI to create a Subscription.

1. Assign Azure account `Microsoft.Resources/deployments/write` permissions at the subscription level.


<hr />

<a name="download-from-github"></a>

## Download from GitHub

1. Open a Terminal.
1. Navigate (after creating a folder) to the folder where you want to create a repository from GitHub.com.

   PROTIP: Our team uses the <tt>bomonike</tt> GitHub organization, which is why that name is part of the URL. That folder is at the root so we have a separate account ssh and gpg for it.

1. Open an internet browser window to this URL:

   <pre><strong>open https://github.com/bomonike/azure-chatgbt.git</strong></pre>

1. Optionally, click "Fork" in GitHub

1. Click the green "Code" button

1. Optionally, click the "Fork" button if you intend on contributing back.

1. Switch back to your Terminal app.

1. If you forked, download without forking:

   <pre><strong>git clone https://github.com/bomonike/azure-chatgbt.git</strong></pre>

   CAUTION: If that repository is not found (being private), please connect with me at<br />
   <a target="_blank" href="https://linkedin.com/in/wilsonmar">https://linkedin.com/in/wilsonmar</a>


<hr/>

<a name="files-and-folders-in-this-repo"></a>

## Files and Folders in This Repo

At the repo's root folder are these standard files from <tt>ls -al</tt>

   * <a href="#.env">.env</a>
   * <a href="#.gitattributes">.gitattributes</a>
   * <a href="#.gitignore">.gitignore</a>
   *	CHANGELOG.md
   *	CODE_OF_CONDUCT.md
   *	CONTRIBUTING.md
   *	LICENSE.md
   *	README.md
   *	<a href="#robot.png">robot.png</a>
   <br /><br />
   
where all variables are declared; these might or might not have a default value.
   File <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/02-run-docker-container.sh">CONTRIBUTING.md</a> describes both the above standard files and the contents of folder:
   * <strong>.vscode</strong> contains a 
   <br /><br />

NOTE: GitHub was designed to house text, not images. So images referenced in this README are retrieved from a cloudinary.com account so image sizing can be done dynamically adjusted for different screen sizes.

<a name="robot.png"></a>

### robot.png

An image for the repo should be a PNG, JPG, or GIF file under 1 MB in size. 
For the best quality rendering, the <a target="_blank" href="https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/customizing-your-repositorys-social-media-preview">recommended size</a> is at least 640 by 320 pixels (1280 by 640 pixels for best display).


<a name=".gitattributes"></a>

### .gitattributes

<a target="_blank" href="https://stackoverflow.com/questions/73086622/is-a-gitattributes-file-really-necessary-for-git">PROTIP</a>:
Since file names ending in ".bat" are almost always edited in Windows,
enforce their line endings have Carriage Return and Line Feed special characters:

```
*.bat text eol=crlf
```

- <a target="_blank" href="https://git-scm.com/docs/gitattributes">Technical specification on .gitattributes</a>


<hr />

<a name="Folders"></a>

### Folders

<strong>Folders</strong> in this repo:

* <a href="#GitHub+Workflow+Actions">.github/workflow</strong></a> holds GitHub Actions yaml files specifying automation upon git push
*	<tt>scripts</tt> holds CLI shell (.sh) and PowerShell (.psm) files
*	<a href="#Chainlit">scripts/.chainlit</a>
*	<tt>scripts/.vscode</tt> to hold yml files to configure Kubernetes
*	<a href="#Terraform">terraform</a> HCL-format (defined by HashiCorp) to hold IaC (Infrastructure as Code) to automate the creation of resources
<br /><br />

<hr />

<a name="Terraform"></a>

## Terraform folder

See <a target="_blank" href="https://wilsonmar.github.io/terraform/">my notes about Terraform</a> files.

NOTE: Terraform is not the only IaC technology. <a target="_blank" href="https://learn.microsoft.com/en-us/training/modules/build-first-bicep-template/">Microsoft's Bicep</a> <a target="_blank" href="https://www.youtube.com/watch?v=MP60ND7Upn4">templates</a> enable <a target="_blank" href="https://learn.microsoft.com/en-us/azure/templates/microsoft.servicefabric/clusters?pivots=deployment-language-bicep">Microsoft Fabric clusters</a>.

The basic files in each resource created by Terraform are:

* <strong>main.tf</strong> specifies the resources to be provisioned the how to configure each.
* <strong>variables.tf</strong> declares all variables (which might have a default value).
* <a href="#terraform.tfvars"><strong>terraform.tfvars</strong></a> overrides values in variables.tf
* <strong>outputs.tf</strong> which defines the variables and values generated within Terraform scripts. 
* <a href="#terraform/providers.tf">terraform.tf</a> 
<br /><br />

<hr />

<a name="terraform/providers.tf">providers.tf</a> 

### terraform/providers.tf

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/providers.tf">VIEW: terraform/providers.tf</a>.  Its contents:

   <pre>
   provider "azurerm" {
  features {}
}
   </pre>

   The file defines features of the Terraform providers specified in the file of the same name as the file at the root.

   There is also a <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/providers.tf">kubernetes/providers.tf</a>

   * <a target="_blank" href="#register-preview-features.sh">register-preview-features.sh</a> described in the next section.
   <br /><br />


<hr />

<a name="Establish-Azure-Account"></a>

## Establish Azure Account

This needs to be done manually. 

Results from this effort are pasted in env (environment) files referenced by automation scripts.

See https://wilsonmar.github.io/azure-onboarding

1. Obtain a domain name.

1. Obtain/specify email addresses.


<a name="register-preview-features.sh"></a>

### terraform/register-preview-features.sh

1. To register any preview feature used by the AKS cluster, run the `register-preview-features.sh` Bash script in the `terraform` folder?\:

   <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/register-preview-features.sh">
   VIEW: register-preview-features.sh</a>

1. Install <tt><strong>az-preview</strong></tt> extensions (of version 0.5.140 or later, such as 1.0.0b6):

   <pre><strong>az extension add --name aks-preview --allow-preview true</strong></pre>

   PROTIP: At time of writing, "No suitable stable version of 'aks-preview' to install. Add `--allow-preview` to try preview versions". So if that's not specified:

   <pre>Default enabled including preview versions for extension installation now. Disabled in May 2024. Use '--allow-preview true' to enable it specifically if needed. Use '--allow-preview false' to install stable version only.
The installed extension 'aks-preview' is in preview.
   </pre>

   Later, to update to the latest version of the extension released:

   <pre>az extension update --name aks-preview --allow-preview true</pre>

1. The script refreshes the registration of the <tt>Microsoft.ContainerService</tt> resource provider...

   <tt>az provider register --namespace Microsoft.ContainerService</tt>


<hr />

<a name="Secrets-Management"></a>

## Secrets Management

To avoid storing secrets in GitHub, 
establish an instance of a Secrets Management service.
Options:

* Within AKeyless.com in the cloud.
* Within the HashiCorp cloud service
<br /><br />



<a name="Terraform-Providers"></a>

### Terraform Providers

Terraform Providers provide the logic for how the <tt>terraform</tt> client communicates with <a target="_blank" href="https://registry.terraform.io/browse/providers">cloud providers</a> (including Azure, AWS, GCP, etc.). 

This repo's <a href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/providers.tf">providers.tf</a> file contains:

```terraform
provider "azurerm" {
  features {}
}
```

See https://github.com/hashicorp/terraform-provider-azurerm

OpenTofu (at <a target="_blank" href="https://opentofu.org/">opentofu.org</a>) is a fork of Terraform v1.5.6 (as of January, 2024) after HashiCorp placed Terraform under the BUSL license. OpenTofu is open-source, community-driven, and managed by the Linux Foundation.

Terraform's Azure Provider "azurerm" (Resource manager), documented at <a target="_blank" href="https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs">https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs</a> specifies that
when <tt>terraform init</tt> is run, the Terraform client creates a <tt><strong>.terraform</strong></tt> folder with sub-folders <tt>modules</tt> and <tt>terraorm</tt>. Into those sub-folders are downloaded "mappings" the terraform client uses to call Azure Resource Manager (ARM) API's. and [Manage resources in Microsoft Azure](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resources-rest?tabs=azure-cli).


<a name=".gitignore"></a>

### .gitignore 

Because the file <tt><strong>terraform.lock.hcl</strong></tt> and folder <tt>.terraform</tt> are created <strong>dynamically</strong>, an entry in this repo's 

   <ul><a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/.gitignore/"><tt>.gitignore</tt> file</a>
   </ul>

keeps them from being uploaded to GitHub.

   - https://www.toptal.com/developers/gitignore/api/terraform
   - https://www.freecodecamp.org/news/gitignore-what-is-it-and-how-to-add-to-repo/
   - https://www.atlassian.com/git/tutorials/saving-changes/gitignore
   - https://github.com/github/gitignore
   <br /><br />

For more information on the [data sources](https://www.terraform.io/docs/configuration/data-sources.html) and [resources](https://www.terraform.io/docs/configuration/resources.html) supported by the Azure Provider, see [Hashicorp's documentation](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs). 

To learn the basics of Terraform using this provider, follow the hands-on [get started tutorials](https://learn.hashicorp.com/tutorials/terraform/infrastructure-as-code?in=terraform/azure-get-started). If you are interested in the [Azure Provider](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)'s latest features, see the [changelog](https://github.com/hashicorp/terraform-provider-azurerm/blob/main/CHANGELOG.md) for version information and release notes.


<hr />

<a name="Customize-Variable-Values"></a>

## Customize Variable Values

https://github.com/paolosalvatori/aks-openai-chainlit-terraform/

ATTIBUTION: This repo is adapted from several examples. Paolo's <a target="_blank" href="https://github.com/paolosalvatori/aks-openai-chainlit-terraform/">github</a> as referenced <a target="_blank" href="https://techcommunity.microsoft.com/t5/fasttrack-for-azure/create-an-azure-openai-langchain-chromadb-and-chainlit-chat-app/ba-p/4024070createdJan8-2024">in his blog</a>.


<a name="terraform.tfvars"></a>

### terraform.tfvars

Terraform modules contain variables specified in these files:

???


   * As described at <a target="_blank" href="https://developer.hashicorp.com/terraform/language/values/variables#variable-definitions-tfvars-files">https://developer.hashicorp.com/terraform/language/values/variables#variable-definitions-tfvars-files</a>,
   <br /><br />

1. PROTIP: To switch between use of dev, stage, demo and prod environments over the lifecycle of assets, making a change in a tfvars file into GitHub is NOT necessary because the environment to use can be specified in values overridden at run-time. That can be achieved by having a separate shell file to run for each stage of development. For example, during "dev", this command would be used:

   <pre><strong>TF_VAR_environment="dev" \
TF_VAR_delete_lock="false" \
TF_VAR_instance_type="t2.micro" \
TF_VAR_instance_count=1 \
TF_VAR_tags='{"project":"123457"}' \
terraform plan
   </strong></pre>

   Alternately,
   
   <pre><strong>terraform plan -var="environment=dev"
   </strong></pre>

   Since there are dozens of specifications in a complex setup such as what is in this repo:

1. Open a Text Editor (such as Visual Studio Code) to edit the file embedded in all scripts to consistently provide values to system variables referenced:

   <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/00-variables.sh">
   VIEW: 00-variables.sh</a>

1.  <a target="_blank" https="https://github.com/bomonike/azure-chatgbt/blob/main/terraform/terraform.tfvars">VIEW terraform.tfvars</a>

   Specify custom values for variables in the file:

   ```terraform
name_prefix                              = "Atum"
domain                                   = "babosbird.com"
subdomain                                = "sally"
kubernetes_version                       = "1.28.3"  
ssh_public_key                           = "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDRHrICSTesKCNyH6vN4K3YwhDUO89cqnEDz2bcZ0sLn9mU6hwyXHna5vME8Y/A8Jbj4XiMyePbAJsX8R/Yyq5pZSiqYpPqSdRGOGqKxQPMBE8WFN1RZmrbrb0ElVQtdWWhlCis4PyMn76fbH6Q8zf/cPzzm9GTimVw62BGhdqdHHru7Sy3I+WRGVA3Z2xHqpda+4nd9LYQW3zkHP98TbIM5OW8kVhRUtmg3c0tOViU6CsGP9w4FU8TU7wLWoeig69kv6UgikwnJYXkItiLecCbVqOeGwbHZQmawcqEY674E3jgJkJ5nQVblCODR0ODNCtrCDVyT6pX0Hdt1ivIpkf"
vm_enabled                               = true
location                                 = "eastus"
admin_group_object_ids                   = ["4e4d0501-e693-4f3e-965b-5bec6c410c03"]
web_app_routing_enabled                  = true
dns_zone_name                            = "babosbird.com"
dns_zone_resource_group_name             = "DnsResourceGroup"
namespace                                = "chainlit"
service_account_name                     = "chainlit-sa"
#deployment_script_primary_script_uri     = "https://paolosalvatori.blob.core.windows.net/scripts/install-packages-for-chainlit-demo.sh"
grafana_admin_user_object_id             = "0c5267b2-01f3-4a59-970e-0d9218d5412e"
vnet_integration_enabled                 = true
openai_deployments      = [
  {
    name = "gpt-35-turbo-16k"
    model = {
      name = "gpt-35-turbo-16k"
      version = "0613"
    }
  },
  {
    name = "text-embedding-ada-002"
    model = {
      name = "text-embedding-ada-002"
      version = "2"
    }
  }
]
   ```

   For the magic8ball:

   ```terraform
name_prefix            = "magic8ball"
domain                 = "contoso.com"
subdomain              = "magic"
namespace              = "magic8ball"
service_account_name   = "magic8ball-sa"
ssh_public_key         = "XXXXXXX"
vm_enabled             = true
location               = "westeurope"
admin_group_object_ids = ["XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"] 
   ```

   - `name_prefix` specifies a prefix for all the Azure resources.

   - `domain`: specifies the domain part (e.g., subdomain.domain) of the hostname of the ingress object used to expose the chatbot via the [NGINX Ingress Controller](https://docs.nginx.com/nginx-ingress-controller/).

   - `subdomain`: specifies the subdomain part of the hostname of the ingress object used to expose the chatbot via the [NGINX Ingress Controller](https://docs.nginx.com/nginx-ingress-controller/).

   - `namespace`: specifies the namespace of the workload application that accesses the Azure OpenAI Service.

   - `ssh_public_key`: specifies the SSH public key used for the AKS nodes and jumpbox virtual machine. It's created by _____

   - `vm_enabled`: a boleean value that specifies whether deploying or not a jumpbox virtual machine in the same virtual network of the AKS cluster.

   - `location`: specifies the region (e.g., westeurope) where deploying the Azure resources.

   - `admin_group_object_ids`: when deploying an AKS cluster with Entra and Azure RBAC integration, this array parameter contains the list of Entra group object IDs that will have the admin role of the cluster.

   azure_active_directory_role_based_access_control

   <a name="service_account_name"></a>
   
   ### service_account_name

   - `service_account_name`: specifies the name of the service account of the workload application that accesses the <strong>Azure OpenAI</strong> Service. This is not the API key from <a target="_blank" href="https://platform.openai.com/api-keys">https://platform.openai.com/api-keys</a>. Use this:

1. Get to the Azure OpenAI service at:

   <a target="_blank" href="https://portal.azure.com/#view/Microsoft_Azure_ProjectOxford/CognitiveServicesHub/~/OpenAI">https://portal.azure.com/#view/Microsoft_Azure_ProjectOxford/CognitiveServicesHub/~/OpenAI</a>   

1. Click "Create Azure OpenAI" for message:

   Azure OpenAI Service is currently available to customers via an application form. The selected subscription has not been enabled for use of the service and does not have quota for any pricing tiers. Click here to request access to Azure OpenAI service.

1. Click "Click here to request access to Azure OpenAI service" for the form at:

   https://auth0.openai.com/u/login/identifie...

1. Fill out the form.

1. When you get a reply, copy the code in file <a href="#terraform.tfvars">terraform/terraform.tfvars</a>

   <pre>service_account_name                     = "chainlit-sa"</pre>


   <a name="00-variables.sh"></a>

   ### scripts/00-variables.sh

   <tt>00-variables.sh</tt> contain values to variables within <strong>helm files</strong>.

1. Open a Text Editor (such as Visual Studio Code) to edit the file embedded in all shell scripts to consistently provide values to system variables referenced:

   <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/00-variables.sh">VIEW: 00-variables.sh</a>

   <pre>
# Variables
acrName="CoralAcr"
acrResourceGrougName="CoralRG"
location="FranceCentral"
attachAcr=false
&nbsp;
imageName="magic8ball"
tag="v2"
containerName="magic8ball"
federatedIdentityName="Magic8BallFederatedIdentity"
&nbsp;
image="$acrName.azurecr.io/$imageName:$tag"
imagePullPolicy="IfNotPresent" # Always, Never, IfNotPresent
managedIdentityName="OpenAiManagedIdentity"
&nbsp;
# Azure Subscription and Tenant
subscriptionId=$(az account show --query id --output tsv)
subscriptionName=$(az account show --query name --output tsv)
   # Value: "Pay-As-You-Go"
tenantId=$(az account show --query tenantId --output tsv)
&nbsp;
# Parameters
title="Magic 8 Ball"
label="Pose your question and cross your fingers!"
temperature="0.9"
imageWidth="80"
&nbsp;
# OpenAI
openAiName="CoralOpenAi"
openAiResourceGroupName="CoralRG"
openAiType="azure_ad"
openAiBase="https://coralopenai.openai.azure.com/"
openAiModel="gpt-35-turbo"
openAiDeployment="gpt-35-turbo"
&nbsp;
# Nginx Ingress Controller
nginxNamespace="ingress-basic"
nginxRepoName="ingress-nginx"
nginxRepoUrl="https://kubernetes.github.io/ingress-nginx"
nginxChartName="ingress-nginx"
nginxReleaseName="nginx-ingress"
nginxReplicaCount=3
&nbsp;
# Certificate Manager
cmNamespace="cert-manager"
cmRepoName="jetstack"
cmRepoUrl="https://charts.jetstack.io"
cmChartName="cert-manager"
cmReleaseName="cert-manager"
&nbsp;
# Cluster Issuer
email="paolos@microsoft.com"
clusterIssuerName="letsencrypt-nginx"
clusterIssuerTemplate="cluster-issuer.yml"
&nbsp;
# AKS Cluster
aksClusterName="CoralAks"
aksResourceGroupName="CoralRG"
&nbsp;
# Sample Application
namespace="magic8ball"
serviceAccountName="magic8ball-sa"
deploymentTemplate="deployment.yml"
serviceTemplate="service.yml"
configMapTemplate="configMap.yml"
secretTemplate="secret.yml"
&nbsp;
# Ingress and DNS
ingressTemplate="ingress.yml"
ingressName="magic8ball-ingress"
dnsZoneName="contoso.com"
dnsZoneResourceGroupName="DnsResourceGroup"
subdomain="magic8ball"
host="$subdomain.$dnsZoneName"
   </pre>

1. Customize the value of each variable: 

   1. # Azure Subscription and Tenant
   1. # Variables
      acrName="PaolosAcr"
      acrResourceGrougName="PaolosRG"
      location="eastus"
   1. # Python Files
   1. # Docker images
   1. # Arrays
   1. # OpenAI
   1. # Certificate Manager
   1. # Cluster Issuer
   1. # AKS Cluster
   1. # App Parameters
   1. # Ingress and DNS
   1. # Nginx Ingress Controller
   1. # NGINX
   1. # Sample Application
   <br /><br />


<hr />

<a name="AKS-Resources-Deployed"></a>

### List AKS Resources Deployed

When fully deployed, these resources should be listed using the Azure portal, Azure CLI, or Azure PowerShell:

To list the deployed resources in the resource group defined as 

   <ul><tt>"${aksResourceGroupName}":</tt></ul>

* Using Azure CLI:

   ```azurecli
az resource list --resource-group "${aksResourceGroupName}"
   ```

* Alternately, using Azure CLI:

   ```azurepowershell
Get-AzResource -ResourceGroupName "${aksResourceGroupName}"
   ```

<pre><strong>terraform plan -var-file="test.tfvars"
</strong></pre>

<pre><strong>terraform plan -var-file="demo.tfvars"
</strong></pre>

<pre><strong>terraform plan -var-file="prod.tfvars"
</strong></pre>

<hr />

## Run Terraform

Here's the sequence of actions to run Terraform (without the error checking code):

```bash
# Navigate to the teraform folder
# Initialize Terraform:
terraform init

# Validate Terraform configuration files:
terraform validate

# Format Terraform configuration files:
terraform fmt

# Review the terraform plan:
terraform plan

# Create Resources:
terraform apply

# Verify Resources:
1. Resource Group Name
2. Resource Group Location
3. Virtual Network Name
4. Virtual Network Subnet Name 
5. Compare with names present in c2-variables.tf to reconfirm it has overrided it and took from terraform.tfvars
```


<hr />

<a name="terraform-Init"></a>

### terraform init

```
Initializing the backend...
Initializing modules...
- acr_private_dns_zone in modules/private_dns_zone
- acr_private_endpoint in modules/private_endpoint
- aks_cluster in modules/aks
- bastion_host in modules/bastion_host
- blob_private_dns_zone in modules/private_dns_zone
- blob_private_endpoint in modules/private_endpoint
- container_registry in modules/container_registry
- grafana in modules/grafana
- key_vault in modules/key_vault
- key_vault_private_dns_zone in modules/private_dns_zone
- key_vault_private_endpoint in modules/private_endpoint
- kubernetes in modules/kubernetes
- log_analytics_workspace in modules/log_analytics
- nat_gateway in modules/nat_gateway
- node_pool in modules/node_pool
- openai in modules/openai
- openai_private_dns_zone in modules/private_dns_zone
- openai_private_endpoint in modules/private_endpoint
- prometheus in modules/prometheus
- storage_account in modules/storage_account
- virtual_machine in modules/virtual_machine
- virtual_network in modules/virtual_network

Initializing provider plugins...
- Finding hashicorp/helm versions matching ">= 2.7.1"...
- Finding latest version of hashicorp/random...
- Finding hashicorp/azurerm versions matching "3.85.0"...
- Finding gavinbunney/kubectl versions matching ">= 1.7.0"...
- Finding hashicorp/kubernetes versions matching ">= 2.16.0"...
- Installing hashicorp/azurerm v3.85.0...
- Installed hashicorp/azurerm v3.85.0 (signed by HashiCorp)
- Installing gavinbunney/kubectl v1.14.0...
- Installed gavinbunney/kubectl v1.14.0 (self-signed, key ID AD64217B5ADD572F)
- Installing hashicorp/kubernetes v2.26.0...
- Installed hashicorp/kubernetes v2.26.0 (signed by HashiCorp)
- Installing hashicorp/helm v2.12.1...
- Installed hashicorp/helm v2.12.1 (signed by HashiCorp)
- Installing hashicorp/random v3.6.0...
- Installed hashicorp/random v3.6.0 (signed by HashiCorp)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```


<a name="Terraform-State-Files"></a>

### Terraform State Files

When <tt>terraform apply</tt> runs, it creates a state file named ".terraform.*" which contains a definition of all the resources and their configurations, including secrets. So the file is specified in <a href="#.gitignore">.gitignore</a> to so it does not get uploaded to GitHub.

However, since terraform is declarative, the file is used to determine what resources are updated when terraform files are changed.

We need to use one of these mechanisms to store it securely:

* in an encrypted file still in the GitHub (and unencrypted when needed), with the unencryption key retrieved from the Akeyless cloud.
* in an Azure blob;
* in HashiCorp's cloud
* in <a target="_blank" href="https://spacelift.io/terraform-at-scale">spacelift.io</a>
<br /><br />

For additional information, see:
   - https://terraformguru.com/terraform-certification-using-azure-cloud/22-Input-Variables-Assign-with-terraform-tfvars/

<hr />

<a name="01-build-docker-images.sh"></a>

## 01-build-docker-image.sh

Run `01-build-docker-image.sh` in the `scripts` folder to build container images using the `Dockerfile` referencing various <tt>yaml</tt> files in the same folder:

1. Assuming that the Docker Agent was installed as part of Prerequisites.

1. Change the value of <tt>$tag</tt> within <a href="#00-variables.sh">00-variables.sh</a>.

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/01-build-docker-images.sh">
VIEW: 01-build-docker-images.sh</a> 

   ```bash
#!/bin/bash

# Variables
source ./00-variables.sh

# Use a for loop to build the docker images using the array index
for index in ${!images[@]}; do
  # Build the docker image
  docker build -t ${images[$index]}:$tag -f Dockerfile --build-arg FILENAME=${filenames[$index]} --build-arg PORT=$port .
done

# PREVIOUSLY:Build the docker image
docker build -t $imageName:$tag -f Dockerfile .
  ```

1. Ensure that the Docker client is running.

1. Review the Dockerfile (at time of this writing, the file's contents):

   <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/Dockerfile">
   VIEW: Dockerfile</a> 

1. TODO: Instead of using VirtualEnv, substitute use of Miniconda3 and download from the Conda rather than PyPi :
 
   <tt>RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"
   </tt>

1. Instead: TODO: Setup Conda environment "py311" for Python 3.11 from Conda.

1. TODO: Use Conda to load packages in the Conda library specified in the <tt>requirements.txt</tt> file at:

   <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/requirements.txt">
   VIEW: /scripts/requirements.txt</a>


1. QUESTION: <tt>ENTRYPOINT ["streamlit"</tt>


<hr/>

### 02-ACR

The [Azure Container Registry (ACR)](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/container_registry) is used to build, store, and manage container images and other artefacts in a private registry for deployments to Kubernetes. See https://wilsonmar.github.io/kubernetes

1. Under your Azure Subscription, create a ACR (Azure Container Registry) instance (instead of using Docker Hub, Quey, Artifactory, etc.):

For additional information about ACR:

   * https://medium.com/@anjkeesari/create-azure-container-registry-acr-using-terraform-bf5b3dbabb97
   * https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/container_registry.html
   <br /><br />


<hr />

## The ChatGPT Applications 

End-users interact with a <strong>Chat application</strong> (on the "front-end") to hold conversations with users via interactive chat.

A traditional Chat app, such as the iMessage app on iPhones between people, has a human on both ends of the conversation.

Two applications are constructed by this repo:

<a target="_blank" href="https://res.cloudinary.com/dcajqrroq/image/upload/v1708723967/openai/containers.png"><img alt="containers.png" src="https://res.cloudinary.com/dcajqrroq/image/upload/v1708723967/openai/containers.png"></a>
 
### Python

1. Both apps are written in Python and run within a <strong>Docker Container</strong> orchestrated as a pod within the <a href="Kubernetes">Kubernetes container orchestrator</a>.

   ### chat.py

   View the "front-end" <strong>Chat app</strong>  Python program source at:

   <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/chat.py">
   chat.py</a>

   The app is used by humans to interact with a non-human "Docs Application".

   ### doc.py

   View the Docs app Python program source at:

   <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/doc.py">
   doc.py</a>

   REMEMBER: imports in Python source assume that when the <a href="#Dockerfile</a>Dockerfile</a> 
   was run, it downloaded <strong>dependencies</strong> specified in the <tt>Requirements.txt</tt> file
   and included them all in the Docker container generated.

   Both apps make API calls to two AI (Artificial Intelligence) services.

   ### OpenAI LLM Service

1. The [Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/overview) uses OpenAI's [large language models (LLMs)](https://en.wikipedia.org/wiki/Large_language_model) that became famous in 2023 for its ability to <strong>generate</strong> new content based on existing content. LLMs also enable [content summarization](https://www.zdnet.com/article/how-to-use-chatgpt-to-summarize-a-book-article-or-research-paper/), semantic search, and natural language to code translation. Users can access the service through REST APIs, Python SDK, or the web-based interface in the Azure OpenAI Studio.

   The version of OpenAI used is specified in the <a href="#terraform.tfvars">terraform.tfvars</a> file:

   <pre>openai_deployments = [
  {
    name = "gpt-35-turbo-16k"
    model = {
      name = "gpt-35-turbo-16k"
      version = "0613"
    }
  },
  {
    name = "text-embedding-ada-002"
    model = {
      name = "text-embedding-ada-002"
      version = "2"
    }
  }
]
   </pre>

   This is made possible by [large language models (LLMs)](https://en.wikipedia.org/wiki/Large_language_model) like OpenAI ChatGPT, which are deep learning algorithms capable of recognizing, summarizing, translating, predicting, and generating text and other content. LLMs leverage the knowledge acquired from extensive datasets, enabling them to perform tasks that go beyond teaching AI human languages. These models have found success in diverse domains, including understanding proteins, writing software code, and more. Apart from their applications in natural language processing, such as translation, chatbots, and AI assistants, large language models are also extensively employed in healthcare, software development, and various other fields.

   The OpenAI platform services provide "cognitive services" powered by models created by [OpenAI](https://openai.com/). One of the models available through this service is the [ChatGPT-4](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#gpt-4-models) 

   Azure OpenAI Service listens and responds to REST API access to several of OpenAI's language models:
   * [GPT-3](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#gpt-3-models), 
   * [Codex](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#codex-models) 
   * [Embeddings](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#codex-models)
   * [GPT-4](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#gpt-4-models) is designed for interactive conversational tasks. It allows developers to integrate natural language understanding and generation capabilities into their applications.
   * [ChatGPT-35 Turbo](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#chatgpt-gpt-35-turbo)
   <br /><br /> 

   The [Chat Completion API](https://platform.openai.com/docs/api-reference/chat/create) of the Azure OpenAI Service provides a dedicated interface for interacting with the [ChatGPT](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#chatgpt-gpt-35-turbo) and [GPT-4 models](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#gpt-4-models). This API is currently in preview and is the preferred method for accessing these models. The GPT-4 models can only be accessed through this API.

   ### Prompts
   
   [GPT-3](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#gpt-3-models), [GPT-3.5](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#chatgpt-gpt-35-turbo), and [GPT-4](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#gpt-4-models) models from OpenAI are prompt-based. With prompt-based models, the user interacts with the model by entering a text prompt, to which the model responds with a text completion. This completion is the model’s continuation of the input text. While these models are extremely powerful, their behavior is also very sensitive to the prompt. This makes prompt construction an important skill to develop. For more information, see [Introduction to prompt engineering](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/prompt-engineering).

   Prompt construction can be difficult. In practice, the prompt acts to configure the model weights to complete the desired task, but it's more of an art than a science, often requiring experience and intuition to craft a successful prompt. The goal of this article is to help get you started with this learning process. It attempts to capture general concepts and patterns that apply to all GPT models. However it's important to understand that each model behaves differently, so the learnings may not apply equally to all models.

   Prompt engineering refers to the process of creating instructions called prompts for Large Language Models (LLMs), such as OpenAI’s ChatGPT. With the immense potential of LLMs to solve a wide range of tasks, leveraging prompt engineering can empower us to save significant time and facilitate the development of impressive applications. It holds the key to unleashing the full capabilities of these huge models, transforming how we interact and benefit from them. For more information, see [Prompt engineering techniques](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/advanced-prompt-engineering?pivots=programming-language-chat-completions).

   For more about the Azure OpenAI Service and Large Language Models (LLMs), see:

   - [What is Azure OpenAI Service?](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/overview)
   - [Azure OpenAI Service models](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models)
   - [Large Language Model](https://en.wikipedia.org/wiki/Large_language_model)
   <br /><br />


   ### Chainlit

2. The <strong>Chainlit service</strong> creates ChatGPT-like user interfaces (UIs) for AI applications. 
   
   Chainlit is an open-source Python package based on the "Streamlit" general-purpose UI library, but purpose-built for AI applications to enable conversational experiences and information retrieval. 

   Chainlit can trace its own [Chain of Thought](https://docs.chainlit.io/concepts/chain-of-thought), which allows users to explore the reasoning process directly within the UI. This feature enhances transparency and enables users to understand how the AI arrives at its responses or recommendations.

   Chainlit is configured based on this (toml <a target="_blank" href="https://www.wikiwand.com/en/TOML">Tom's Obvious Minimal Language</a>)file: 
   <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/.chainlit/config.toml">VIEW: /scripts/.chainlit/config.toml</a>

   * true or false values sets each feature on or off.
   <br /><br />

   Chainlit integrates with <a href="#LangChain">LangChain (described below)</a>, [LlamaIndex](https://gpt-index.readthedocs.io/en/latest/), and [LangFlow](https://github.com/logspace-ai/langflow).

   For more information about Chainlit, see:
   - [Documentation](https://docs.chainlit.io/overview)
   - [Examples](https://docs.chainlit.io/examples/community)
   - [API Reference](https://docs.chainlit.io/api-reference/on-message)
   - [Cookbook](https://docs.chainlit.io/examples/cookbook)
   <br /><br />

   The "Docs Application" container setup and run by this repo additionally contains an instance of the LangChain and <a href="#ChromaDB">"ChromaDB"</a>  <a href="#Vector+Databases">Vector Database</a>.

   ### LangChain

1. LangChain adds <strong>custom vector embeddings</strong> for "Retrieval-Augmented Generation (RAG)" by augmenting LLMs such as OpenAI's GPT-x, <a target="_blank" href="https://ai.google/discover/palm2/">Google's PaLM (Pathways Language Model)</a>, <a target="_blank" href="https://claude.ai/">Anthropic's Claude</a>, <a target="_blank" href="https://bit.ly/4bPLwMq">VIEW</a>: <a target="_blank" href="https://llama.meta.com/">Facebook's LLaMa</a>, Hugging Face, etc. 

   LangChain facilitates applications such as document analysis, summarization, chatbots, and code analysis.

   LangChain's integrations cover an extensive range of systems, tools, and services, making it a comprehensive solution for language model-based applications. LangChain integrates with the major cloud platforms and with API wrappers for various purposes like news, movie information, and weather, as well as support for Bash, web scraping, and more. 
   
   LangChain offers various functionalities for document handling, code generation, analysis, debugging, and interaction with databases and other data sources.

   LangChain can reference vector embeddings in memory as <tt>np.array</tt> for small amounts of data.
   
   But at scale, LangChain references data in a <a target="_blank" href="https://python.langchain.com/docs/integrations/text_embedding">vector embedding model (database)</a> such as ChromaDB.

   Vectors retrieved from the custom vector embeddings are added to the question text/instructions.

   This approach also enables the most up-to-date information added from the custom store.

   - <a target="_blank" href="https://learning.oreilly.com/library/view/-/9781835083468/">BOOK: Generative AI with LangChain</a> By Ben Auffarth, from Packt Publishing December 2023. 360 pages. References <a target="_blank" href="https://github.com/benman1/generative_ai_with_langchain">github.com/benman1/generative_ai_with_langchain</a>
   - [Introduction](https://python.langchain.com/docs/get_started/introduction.html)
   - https://www.youtube.com/@LangChain
   - <a target="_blank" href="https://www.youtube.com/watch?v=_v_fgW2SkkQ&list=PLqZXAkvF1bPNQER9mLmDbntNfSpzdDIU5">Playlist by Greg Kamradt (Data Indy)</a>
   - https://www.youtube.com/watch?v=ySus5ZS0b94

   ### ChromaDB

1. ChromaDB (at <a target="_blank" href="https://docs.trychroma.com/">docs.trychroma.com</a>) is a vector embeddings database that augments LLMs with custom private data.

   ChromaDB is open-source. It competes with MongoDB Atlas, startup Pinecone, etc.

   A [vector database](https://learn.microsoft.com/en-us/semantic-kernel/memories/vector-db) is a new type of database for the AI era because it <strong>defines relationships between words</strong> using vector arrays. 

   <a target="_blank" href="https://www.youtube.com/watch?v=dN0lsF2cvm4">As this diagram</a> illustrates: in a vector database, each word is stored as an array of many dimensions. One dimension may be "fruitiness", "a type of game", etc. 
   
   <a target="_blank" href="https://res.cloudinary.com/dcajqrroq/image/upload/v1709304122/embeddings-3608x1420_w3t9xo.png"><img alt="embeddings-3608x1420.png" src="https://res.cloudinary.com/dcajqrroq/image/upload/v1709304122/embeddings-3608x1420_w3t9xo.png"></a>

   Conceptually similar words would have similar vector values in one or more dimensions.
   
   The relationship between words can be measured with a search for <a target="_blank" href="https://www.wikiwand.com/en/Cosine_similarity">cosine similarity</a> using an algorithm such as "nearest neighbor search". The smaller the cumulative distance (across dimensions) between two arrays, the more alike they are with each other. 
   
   After words are attached to images, this technique is used to find visually similar images.
   This capability is used for reverse image search or content-based image retrieval.

   This technique is how vector databases provides private search engines and recommendation systems to suggest relevant articles or products based on user interests.

   Unusual patterns in vector embeddings can detect fraud and other anomalies.
   
   1. ChromaDB is installed when packages listed in the <a href="requirements.txt">requirements.txt</a> file are downloaded.

   1. Create a collection similar to tables in the relations database. By default, Chroma converts  text into the embeddings

   The default <tt>all-MiniLM-L6-v2</tt>, one of <a target="_blank" href="https://www.sbert.net/docs/pretrained_models.html">many</a> <a target="_blank" href="https://www.sbert.net/">sentence transformer</a>, maps sentences & paragraphs to a 384 dimensional dense vector space trained on a diverse dataset comprising over 1 billion training pairs. But be aware that input text longer than 256-word pieces is truncated. It is compatible with https://platform.openai.com/docs/api-reference

   2. Add text documents to the newly created collection with metadata and a unique ID. The collection converts it into embedding.

   3. Query the collection by text or embedding to receive similar documents. Results can be filtered out based on metadata.

   For more, see
   * https://www.datacamp.com/tutorial/chromadb-tutorial-step-by-step-guide?utm_source=google
   * https://hackernoon.com/vector-databases-getting-started-with-chromadb-and-more
   * https://amusatomisin65.medium.com/chromadb-vector-database-a-guide-on-extending-the-knowledge-of-llm-models-with-rag-4dedb26930f8
   - [Azure Cache for Redis Enterprise](https://learn.microsoft.com/en-us/azure/azure-cache-for-redis/cache-overview). See [Vector Similarity Search with Azure Cache for Redis Enterprise](https://techcommunity.microsoft.com/t5/azure-developer-community-blog/vector-similarity-search-with-azure-cache-for-redis-enterprise/ba-p/3822059) 

<hr />

### Other Vector Databases

- [Facebook AI Similarity Search (FAISS)](https://faiss.ai/index.html) is a widely used vector database because Facebook AI Research develops it and offers highly optimized algorithms for similarity search and clustering of vector embeddings. FAISS is known for its speed and scalability, making it suitable for large-scale applications. It offers different indexing methods like flat, IVF (Inverted File System), and HNSW (Hierarchical Navigable Small World) to organize and search vector data efficiently.

- [SingleStore](https://www.singlestore.com/): SingleStore aims to deliver the world’s fastest distributed SQL database for data-intensive applications: SingleStoreDB, which combines transactional + analytical workloads in a single platform.

- [Astra DB](https://www.datastax.com/products/datastax-astra): [DataStax](https://www.datastax.com/) Astra DB is a cloud-native, multi-cloud, fully managed database-as-a-service based on Apache Cassandra, which aims to accelerate application development and reduce deployment time for applications from weeks to minutes.

- [Milvus](https://milvus.io/): Milvus is an open source vector database built to power embedding similarity search and AI applications. Milvus makes unstructured data search more accessible and provides a consistent user experience regardless of the deployment environment. Milvus 2.0 is a cloud-native vector database with storage and computation separated by design. All components in this refactored version of Milvus are stateless to enhance elasticity and flexibility.

- [Qdrant](https://qdrant.tech/): Qdrant is a vector similarity search engine and database for AI applications. Along with open-source, Qdrant is also available in the cloud. It provides a production-ready service with an API to store, search, and manage points—vectors with an additional payload. Qdrant is tailored to extended filtering support. It makes it useful for all sorts of neural-network or semantic-based matching, faceted search, and other applications.

- [Pinecone](https://www.pinecone.io/): Pinecone is a fully managed vector database that makes adding vector search to production applications accessible. It combines state-of-the-art vector search libraries, advanced features such as filtering, and distributed infrastructure to provide high performance and reliability at any scale.

- [Vespa](https://vespa.ai/): Vespa is a platform for applications combining data and AI, online. By building such applications on Vespa helps users avoid integration work to get features, and it can scale to support any amount of traffic and data. To deliver that, Vespa provides a broad range of query capabilities, a computation engine with support for modern machine-learned models, hands-off operability, data management, and application development support. It is free and open source to use under the Apache 2.0 license.

- [Zilliz](https://zilliz.com/): Milvus is an open-source vector database, with over 18,409 stars on GitHub and 3.4 million+ downloads. Milvus supports billion-scale vector search and has over 1,000 enterprise users. Zilliz Cloud provides a fully-managed Milvus service made by the creators of Milvus. This helps to simplify the process of deploying and scaling vector search applications by eliminating the need to create and maintain complex data infrastructure. As a DBaaS, Zilliz simplifies the process of deploying and scaling vector search applications by eliminating the need to create and maintain complex data infrastructure.

- [Weaviate](https://weaviate.io/): Weaviate is an open-source vector database used to store data objects and vector embeddings from ML-models, and scale into billions of data objects from the same name company in Amsterdam. Users can index billions of data objects to search through and combine multiple search techniques, such as keyword-based and vector search, to provide search experiences.


<hr />

## Fabric Data to Vector store

TODO: Extract Finance, Customer, Telemetry, or Semantic KPIs data stored in <a target="_blank" href="https://wilsonmar.github.io/microsoft-fabric/">Microsoft Fabric</a> OneLake to load as additional <strong>embeddings</strong> in the ChromaDB vector store to influence formulation of responses by OpenAI.

We are aiming for converational search that makes use of Real-Time Telemetry data from Microsoft Fabric.

We add custom vector embeddings so that the Chat can answer questions about telemetry statistics compared against    SLIs (Service Level Indicators) - the real numbers that indicate actual performance:

   * SLAs (Sevice Level Agreements) - the agreements made with clients or users

   * SLOs (Service Level Objectives) - the objectives that must be achieved to meet the SLA
   <br /><br />

For more about this topic:
   * https://www.datacamp.com/tutorial/chromadb-tutorial-step-by-step-guide
   <br /><br />

NOTE: The scope of apps here are not voice-enabled such as <a target="_blank" href="https://wilsonmar.github.io/alexa">Amazon Alexa</a>, Apple Siri, etc. which makes use of the <a target="_blank" href="https://azure.microsoft.com/en-us/products/ai-services/ai-speech/#features">Azure AI Speech service</a> through the <a target="_blank" href="https://speech.microsoft.com/portal">Azure Speech Studio</a> SDK:

   * Speaker voice Recognition
   * <a target="_blank" href="https://speech.microsoft.com/portal/whisperspeechtotext">OpenAI Whisper</a> Speech-to-Text
   * Speech translation
   * Text-to-Speech
   <br /><br />




<hr />

<a name="Diagram"></a>

## Infrastructure Architecture Diagram

The github repo downloaded provides a set of scripts invoking Terraform modules to deploy the following resources within Azure:

<a target="_blank" href="https://res.cloudinary.com/dcajqrroq/image/upload/v1709349970/azure-chatgpt-240301-1920x1080_gjn6er.png"><img alt="azure-chatgpt-240301-1920x1080.png" src="https://res.cloudinary.com/dcajqrroq/image/upload/v1709349970/azure-chatgpt-240301-1920x1080_gjn6er.png"></a>

The diagram above is based on Paolo's <a target="_blank" href="https://github.com/paolosalvatori/aks-openai-chainlit-terraform/blob/main/visio/architecture.vsdx">vsdx</a> as referenced <a target="_blank" href="https://techcommunity.microsoft.com/t5/fasttrack-for-azure/create-an-azure-openai-langchain-chromadb-and-chainlit-chat-app/ba-p/4024070createdJan8-2024">in his blog</a>.

Items in red are <a href="#Terraform-Modules">Terraform Modules (below)</a>.



<a name="Terraform-Modules"></a>

### Terraform Modules

Terraform (.tf modules to deploy an [Azure Kubernetes Service(AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes) cluster and [Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/overview) and how to deploy a Python chatbot that authenticates against Azure OpenAI using [Entra workload identity](https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview) and calls the [Chat Completion API](https://platform.openai.com/docs/api-reference/chat) of the [ChatGPT model](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#chatgpt-gpt-35-turbo). [Azure Kubernetes Service(AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes) cluster communicates with [Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/overview) via an [Azure Private Endpoint](https://docs.microsoft.com/en-us/azure/private-link/private-endpoint-overview). 

Within folder terraform/modules are module folders, listed alphabetically below.
Each (of 112 Azure resources at last counting) is documented by Terraform under:

<a target="_blank" href="https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs">
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs</a>

Within each folder are several basic Terraform files (main.tf, outputs.tf, variables.tf).

* VIEW each <tt>main.tf</tt> file:

   1. <a href="#aks">aks</a>
   1. <a href="#bastion_host">bastion_host</a>
   1. <a href="#container_registry">container_registry</a>
   1. <a href="#deployment_script">deployment_script</a>
   1. <a href="#diagnostic_setting">diagnostic_setting</a>
   1. <a href="#firewall">firewall</a>
   1. <a href="#grafana">grafana</a>
   1. <a href="#key_vault">key_vault</a>
   1. <a href="#kubernetes">kubernetes</a>
   1. <a href="#log_analytics">log_analytics</a>
   1. <a href="#nat_gateway">nat_gateway</a>
   1. <a href="#network_security_group">network_security_group</a>
   1. <a href="#node_pool">node_pool</a>
   1. <a href="#openai">openai</a>
   1. <a href="#private_dns_zone">private_dns_zone</a>
   1. <a href="#private_endpoint">private_endpoint</a>
   1. <a href="#prometheus">prometheus</a>
   1. <a href="#route_table">route_table</a>
   1. <a href="#storage_account">storage_account</a>
   1. <a href="#virtual_machine">virtual_machine</a>
   1. <a href="#virtual_network">virtual_network</a>
   1. <a href="#virtual_network_peering">virtual_network_peering</a>
   <br /><br />

   PROTIP: Internally, Terraform arranges the sequence which resources are created.

   <a name="aks"></a>

   ### aks

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/aks/main.tf">aks</a> defines these Azure resources:

   - "azurerm_user_assigned_identity" "aks_identity" {
   - "azurerm_kubernetes_cluster" "aks_cluster" {
   - "azurerm_monitor_diagnostic_setting" "settings" {
   <br /><br />


   <a name="bastion_host"></a>

   ### bastion_host 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/bastion_host/main.tf">bastion_host</a> defines these Azure resources:

   - "<a target="_blank" href="https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/data-sources/public_ip">azurerm_public_ip</a>" "public_ip" {
   - "azurerm_bastion_host" "bastion_host" {
   - "azurerm_monitor_diagnostic_setting" "settings" {
   - "azurerm_monitor_diagnostic_setting" "pip_settings" {
   <br /><br />

   https://learn.microsoft.com/en-us/training/modules/advanced-security-compute/2-remote-access-public-endpoints-include-azure-bastion


   <a name="container_registry"></a>

   ### container_registry 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/container_registry/main.tf">container_registry</a> defines these Azure resources:

   - "azurerm_container_registry" "acr" {
   - "azurerm_user_assigned_identity" "acr_identity" {
   - "azurerm_monitor_diagnostic_setting" "settings" {
   <br /><br />

   
   <a name="deployment_script"></a>

   ### deployment_script 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/deployment_script/main.tf">deployment_script</a> defines these Azure resources:

   - "azurerm_user_assigned_identity" "script_identity" {
   - "azurerm_role_assignment" "network_contributor_assignment" {
   - "azurerm_resource_deployment_script_azure_cli" "script" {
   <br /><br />

   
   <a name="diagnostic_setting"></a>

   ### diagnostic_setting 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/diagnostic_setting/main.tf">diagnostic_setting</a> defines these Azure resources:

   - "azurerm_monitor_diagnostic_setting" "settings" {
   <br /><br />
   

   <a name="firewall"></a>

   ### firewall 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/firewall/main.tf">firewall</a> defines these Azure resources:

   - "azurerm_public_ip" "pip" {
   - "azurerm_firewall" "firewall" {
   - "azurerm_firewall_policy" "policy" {
   - "azurerm_firewall_policy_rule_collection_group" "policy" {
   - "azurerm_monitor_diagnostic_setting" "settings" {
   - "azurerm_monitor_diagnostic_setting" "pip_settings" {
   <br /><br />

   
   <a name="grafana"></a>

   ### grafana 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/grafana/main.tf">grafana</a> defines these Azure resources:

   - "azurerm_dashboard_grafana" "grafana" { 
   - "azurerm_role_assignment" "grafana" {
   - "azurerm_role_assignment" "grafana_admin" {
   <br /><br />

   <a target="_blank" href="https://sandervandevelde.wordpress.com/2023/12/05/microsoft-fabric-real-time-analytics-exploration-managed-grafana-integration/">render real-time ingested IoT Eventstream through a KQL database to Grafana</a>, using a <a target="_blank" href="https://azure-samples.github.io/raspberry-pi-web-simulator/">virtual Raspberry Pi IoT Hub Simulator</a> assessing Microsoft Fabric Azure Data Explorer  via a Power BI Pro license.

   
   <a name="key_vault"></a>

   ### key_vault 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/key_vault/main.tf">key_vault</a> defines these Azure resources:

   - "azurerm_key_vault" "key_vault" {
   - "azurerm_monitor_diagnostic_setting" "settings" {
   - 
   - 
   <br /><br />


   <a name="kubernetes"></a>

   ### kubernetes 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/main.tf">kubernetes</a> contains these *.tf (Terraform HCL) files:

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/certificate_manager.tf">kubernetes/certificate_manager.tf</a>
1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/cluster_issuers.tf">kubernetes/cluster_issuers.tf</a>
1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/managed_prometheus.tf">kubernetes/managed_prometheus.tf</a>
1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/nginx_ingress_controller.tf">kubernetes/nginx_ingress_controller.tf</a>
1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/prometheus.tf">kubernetes/prometheus.tf</a>
1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/providers.tf">kubernetes/providers.tf</a>
1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/variables.tf">kubernetes/variables.tf</a>
1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/workload.tf">kubernetes/workload.tf</a>
   <br /><br />

   Additionally, ConfigMap yaml for use by Prometheus:

   - <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/yaml/ama-metrics-prometheus-config-configmap.yaml">ama-metrics-prometheus-config-configmap.yaml</a>
   - <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/yaml/ama-metrics-settings-configmap.yaml">ama-metrics-settings-configmap.yaml</a>
   - <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/kubernetes/yaml/kube-prometheus-stack-custom-values.yaml">kube-prometheus-stack-custom-values.yaml</a>
   <br /><br />
   

   <a name="log_analytics"></a>

   ### log_analytics 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/log_analytics/main.tf">log_analytics</a> defines these Azure resources:

   - "azurerm_log_analytics_workspace" "log_analytics_workspace" {
   - "azurerm_log_analytics_solution" "la_solution" {
   <br /><br />


   <a name="nat_gateway"></a>

   ### nat_gateway 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/nat_gateway/main.tf">nat_gateway</a> defines these Azure resources:

   - "azurerm_public_ip" "nat_gategay_public_ip" {
   - "azurerm_nat_gateway" "nat_gateway" {
   - "azurerm_nat_gateway_public_ip_association" 
   - "azurerm_subnet_nat_gateway_association" "nat-avd-sessionhosts" {
   <br /><br />


   <a name="network_security_group"></a>

   ### network_security_group 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/network_security_group/main.tf">network_security_group</a> defines these Azure resources:

   - "azurerm_network_security_group" "nsg" {
   - "azurerm_monitor_diagnostic_setting" "settings" {
   <br /><br />


   <a name="node_pool"></a>

   ### node_pool 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/node_pool/main.tf">node_pool</a> defines these Azure resources:

   - "azurerm_kubernetes_cluster_node_pool" "node_pool" {
   <br /><br />
   

   <a name="openai"></a>

   ### openai 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/openai/main.tf">openai</a> defines these Azure resources:

   - "azurerm_cognitive_account" "openai" {
   - "azurerm_cognitive_deployment" "deployment" {
   - "azurerm_monitor_diagnostic_setting" "settings" {
   <br /><br />


   <a name="private_dns_zone"></a>

   ### private_dns_zone 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/private_dns_zone/main.tf">private_dns_zone</a> defines these Azure resources:

   - "azurerm_private_dns_zone" "private_dns_zone" {
   - "azurerm_private_dns_zone_virtual_network_link" "link" {
   <br /><br />


   <a name="private_endpoint"></a>

   ### private_endpoint 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/private_endpoint/main.tf">private_endpoint</a> defines these Azure resources:

   - "azurerm_private_endpoint" "private_endpoint" {
   <br /><br />


   <a name="prometheus"></a>

   ### prometheus 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/prometheus/main.tf">prometheus</a> defines these Azure resources:

   - "azurerm_monitor_workspace" "workspace" {
   - "azurerm_monitor_data_collection_endpoint" "dce" {
   - "azurerm_monitor_data_collection_rule" "dcr" {
   - "azurerm_monitor_data_collection_rule_association" "dcra" {
   - "azurerm_monitor_alert_prometheus_rule_group" "node_recording_rules_rule_group" {
   - "azurerm_monitor_alert_prometheus_rule_group" "kubernetes_recording_rules_rule_group" {
   - "azurerm_monitor_alert_prometheus_rule_group" "node_and_kubernetes_recording_rules_rule_group_win" {
   - "azurerm_monitor_alert_prometheus_rule_group" "node_recording_rules_rule_group_win" {
   <br /><br />

   
   <a name="route_table"></a>

   ### route_table 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/route_table/main.tf">route_table</a> defines these Azure resources:

   - "azurerm_route_table" "rt" {
   - "azurerm_subnet_route_table_association" "subnet_association" {
   - (no output.tf)
   <br /><br />

   
   <a name="storage_account"></a>

   ### storage_account 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/storage_account/main.tf">storage_account</a> defines these Azure resources:

   - "azurerm_storage_account" "storage_account" {
   <br /><br />

   
   <a name="virtual_machine"></a>

   ### virtual_machine 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/virtual_machine/main.tf">virtual_machine</a> defines these Azure resources:

   - "azurerm_public_ip" "public_ip" {
   - "azurerm_network_security_group" "nsg" {
   - "azurerm_network_interface" "nic" {
   - "azurerm_network_interface_security_group_association" "nsg_association" {
   - "azurerm_linux_virtual_machine" "virtual_machine" {
   - "azurerm_virtual_machine_extension" "azure_monitor_agent" {
   - "azurerm_monitor_data_collection_rule" "linux" {
   - "azurerm_monitor_data_collection_rule_association" 
   <br /><br />

   
   <a name="virtual_network"></a>

   ### virtual_network 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/virtual_network/main.tf">virtual_network</a> defines these Azure resources:

   - "azurerm_virtual_network" "vnet" {
   - "azurerm_subnet" "subnet" {
   - "azurerm_monitor_diagnostic_setting" "settings" {
   <br /><br />

   
   <a name="virtual_network_peering"></a>
   
   ### virtual_network_peering 

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/terraform/modules/virtual_network_peering/main.tf">virtual_network_peering</a> defines these Azure resources:

   - [no output.tf]
   - "azurerm_virtual_network_peering" "peering" {
   - "azurerm_virtual_network_peering" "peering-back" {
   <br /><br />



<hr />

### IAM

1. [User-defined Managed Identity](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/user_assigned_identity): a user-defined managed identity used by the AKS cluster to create additional resources like load balancers and managed disks in Azure.

2. [User-defined Managed Identity](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/user_assigned_identity): a user-defined managed identity used by the chatbot application to acquire a security token via [Microsoft Entra Workload ID](https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview) to call the [Chat Completion API](https://platform.openai.com/docs/api-reference/chat) of the [ChatGPT model](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#chatgpt-gpt-35-turbo) provided by the [Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/overview).

### Networking

Terraform modules are parametric, so you can choose any network plugin:

   - [Azure CNI with static IP allocation](https://learn.microsoft.com/en-us/azure/aks/configure-azure-cni)
   - [Azure CNI with dynamic IP allocation](https://learn.microsoft.com/en-us/azure/aks/configure-azure-cni-dynamic-ip-allocation)
   - [Azure CNI Powered by Cilium](https://learn.microsoft.com/en-us/azure/aks/azure-cni-powered-by-cilium)
   - [Azure CNI Overlay](https://learn.microsoft.com/en-us/azure/aks/azure-cni-overlay)
   - [BYO CNI](https://learn.microsoft.com/en-us/azure/aks/use-byo-cni?tabs=azure-cli)
   - [Kubenet](https://learn.microsoft.com/en-us/azure/aks/configure-kubenet)
   <br /><br />

1. [Azure Bastion Host](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/bastion_host): a separate Azure Bastion is deployed in the AKS cluster virtual network to provide SSH connectivity to both agent nodes and virtual machines.

1. [Azure NAT Gateway](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/nat_gateway): a bring-your-own (BYO) [Azure NAT Gateway](https://learn.microsoft.com/en-us/azure/virtual-network/nat-gateway/nat-overview) to manage outbound connections initiated by AKS-hosted workloads. The NAT Gateway is associated to the `SystemSubnet`, `UserSubnet`, and `PodSubnet` subnets. The [outboundType](https://learn.microsoft.com/en-us/azure/aks/egress-outboundtype#outbound-type-of-managednatgateway-or-userassignednatgateway) property of the cluster is set to `userAssignedNatGateway` to specify that a BYO NAT Gateway is used for outbound connections. NOTE: you can update the `outboundType` after cluster creation and this will deploy or remove resources as required to put the cluster into the new egress configuration. For more information, see [Updating outboundType after cluster creation](https://learn.microsoft.com/en-us/azure/aks/egress-outboundtype#updating-outboundtype-after-cluster-creation-preview).

### Private Endpoints

1. [Azure Private Endpoints](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/private_endpoint): an [Azure Private Endpoint](https://docs.microsoft.com/en-us/azure/private-link/private-endpoint-overview) is created for each of the following resources:
   - Azure OpenAI Service
   - Azure Container Registry
   - Azure Key Vault
   - Azure Storage Account
   - API Server when deploying a private AKS cluster.
   <br /><br />

   The `main.tf` module creates [Azure Private Endpoints](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/private_endpoint) and [Azure Private DNDS Zones](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/data-sources/private_dns_zone) for each of the following resources:

   - Azure OpenAI Service
   - Azure Container Registry
   - Azure Key Vault
   - Azure Storage Account
   <br >/><br />

   In particular, it creates an [Azure Private Endpoint](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/private_endpoint) and [Azure Private DNS Zone](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/data-sources/private_dns_zone) to the [Azure OpenAI Service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/cognitive_account) as shown in the following code snippet:

   ```terraform
module "openai_private_dns_zone" {
  source                       = "./modules/private_dns_zone"
  name                         = "privatelink.openai.azure.com"
  resource_group_name          = azurerm_resource_group.rg.name
  tags                         = var.tags
  virtual_networks_to_link     = {
    (module.virtual_network.name) = {
      subscription_id = data.azurerm_client_config.current.subscription_id
      resource_group_name = azurerm_resource_group.rg.name
    }
  }
}

module "openai_private_endpoint" {
  source                         = "./modules/private_endpoint"
  name                           = "${module.openai.name}PrivateEndpoint"
  location                       = var.location
  resource_group_name            = azurerm_resource_group.rg.name
  subnet_id                      = module.virtual_network.subnet_ids[var.vm_subnet_name]
  tags                           = var.tags
  private_connection_resource_id = module.openai.id
  is_manual_connection           = false
  subresource_name               = "account"
  private_dns_zone_group_name    = "AcrPrivateDnsZoneGroup"
  private_dns_zone_group_ids     = [module.acr_private_dns_zone.id]
}
   ```

### Azure Private DNS Zones

1. [Azure Private DNS Zones](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/data-sources/private_dns_zone): an [Azure Private DNS Zone](https://docs.microsoft.com/en-us/azure/dns/private-dns-overview) is created for each of the following resources:
   - Azure OpenAI Service
   - Azure Container Registry
   - Azure Key Vault
   - Azure Storage Account
   - API Server when deploying a private AKS cluster.
   <br /><br />

### Network Security Groups

1. [Azure Network Security Groups](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/data-sources/network_security_group): subnets hosting virtual machines and Azure Bastion Hosts are protected by [Azure Network Security Groups](https://docs.microsoft.com/en-us/azure/virtual-network/network-security-groups-overview) that are used to filter inbound and outbound traffic.


1. Workload namespace and service account: the [Kubectl Terraform Provider](https://registry.terraform.io/providers/cpanato/kubectl/latest/docs) and [Kubernetes Terraform Provider](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs) are used to create the namespace and service account used by the chat applications.


### Azure Storage Account

1. [Azure Storage Account](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/storage_account): this storage account is used to store the boot diagnostics logs of both the service provider and service consumer virtual machines. Boot Diagnostics is a debugging feature that allows you to view console output and screenshots to diagnose virtual machine status.


<em><strong>Utility Services:</strong></em>

### Azure Key Vault

1. [Azure Key Vault](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/key_vault): an Azure Key Vault used to store secrets, certificates, and keys that can be mounted as files by pods using [Azure Key Vault Provider for Secrets Store CSI Driver](https://github.com/Azure/secrets-store-csi-driver-provider-azure). For more information, see [Use the Azure Key Vault Provider for Secrets Store CSI Driver in an AKS cluster](https://learn.microsoft.com/en-us/azure/aks/csi-secrets-store-driver) and [Provide an identity to access the Azure Key Vault Provider for Secrets Store CSI Driver](https://learn.microsoft.com/en-us/azure/aks/csi-secrets-store-identity-access).

   TODO: Read sensitive configuration data such as passwords or SSH keys from a pre-existing Azure Key Vault resource. For more information, see [Referencing Azure Key Vault secrets in Terraform](https://thomasthornton.cloud/2022/02/26/referencing-azure-key-vault-secrets-in-terraform/).


### Azure OpenAI Service

1. [Azure OpenAI Service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/cognitive_account): an [Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/overview) with a [GPT-3.5](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#chatgpt-gpt-35-turbo) model used by the chatbot application. Azure OpenAI Service gives customers advanced language AI with OpenAI GPT-4, GPT-3, Codex, and DALL-E models with the security and enterprise promise of Azure. Azure OpenAI co-develops the APIs with OpenAI, ensuring compatibility and a smooth transition from one to the other.


### Monitoring

1. Azure Monitor ConfigMaps for [Azure Monitor managed service for Prometheus](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/prometheus-metrics-overview) and `cert-manager` [Cluster Issuer](https://cert-manager.io/docs/configuration/) are deployed using the [Kubectl Terraform Provider](https://registry.terraform.io/providers/cpanato/kubectl/latest/docs) and [Kubernetes Terraform Provider](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs).`

1. [Azure Monitor workspace](https://registry.terraform.io/providers/hashicorp/azurerm/3.83.0/docs/resources/monitor_workspace): An [Azure Monitor workspace](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/azure-monitor-workspace-overview) is a unique environment for data collected by [Azure Monitor](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/data-platform-metrics). Each workspace has its own data repository, configuration, and permissions. Log Analytics workspaces contain logs and metrics data from multiple Azure resources, whereas Azure Monitor workspaces currently contain only metrics related to [Prometheus](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/prometheus-metrics-overview). Azure Monitor managed service for Prometheus allows you to collect and analyze metrics at scale using a Prometheus-compatible monitoring solution, based on the [Prometheus](https://aka.ms/azureprometheus-promio). This fully managed service allows you to use the [Prometheus query language (PromQL)](https://aka.ms/azureprometheus-promio-promql) to analyze and alert on the performance of monitored infrastructure and workloads without having to operate the underlying infrastructure. The primary method for visualizing Prometheus metrics is [Azure Managed Grafana](https://learn.microsoft.com/en-us/azure/managed-grafana/overview). You can connect your [Azure Monitor workspace](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/azure-monitor-workspace-overview) to an [Azure Managed Grafana](https://learn.microsoft.com/en-us/azure/managed-grafana/overview) to visualize Prometheus metrics using a set of built-in and custom Grafana dashboards.

1. [Azure Managed Grafana](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/dashboard_grafana): an [Azure Managed Grafana](https://learn.microsoft.com/en-us/azure/managed-grafana/overview) instance used to visualize the [Prometheus metrics](https://learn.microsoft.com/en-us/azure/azure-monitor/containers/prometheus-metrics-enable?tabs=azure-portal) generated by the [Azure Kubernetes Service(AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes) cluster deployed by the Bicep modules. [Azure Managed Grafana](https://learn.microsoft.com/en-us/azure/managed-grafana/overview) is a fully managed service for analytics and monitoring solutions. It's supported by Grafana Enterprise, which provides extensible data visualizations. This managed service allows to quickly and easily deploy Grafana dashboards with built-in high availability and control access with Azure security.

1. [Prometheus](https://prometheus.io/): the AKS cluster is configured to collect metrics to the [Azure Monitor workspace](https://learn.microsoft.com/en-us/azure/azure-monitor/essentials/azure-monitor-workspace-overview) and [Azure Managed Grafana](https://learn.microsoft.com/en-us/azure/managed-grafana/overview). Nonetheless, the [kube-prometheus-stack](https://artifacthub.io/packages/helm/prometheus-community/kube-prometheus-stack) Helm chart is used to install [Prometheus](https://prometheus.io/) and [Grafana](https://grafana.com/) on the AKS cluster.

1. [Azure Log Analytics Workspace](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/log_analytics_workspace): a centralized [Azure Log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/log-analytics-workspace-overview) workspace is used to collect the diagnostics logs and metrics from all the Azure resources:
   - Azure OpenAI Service
   - Azure Kubernetes Service cluster
   - Azure Key Vault
   - Azure Network Security Group
   - Azure Container Registry
   - Azure Storage Account
   - Azure jump-box virtual machine
   <br /><br />


<a name="Compute"></a>

### Compute

1. [NGINX Ingress Controller](https://docs.nginx.com/nginx-ingress-controller/): this sample compares the managed and unmanaged NGINX Ingress Controller. While the managed version is installed using the [Application routing add-on](https://learn.microsoft.com/en-us/azure/aks/app-routing), the unmanaged version is deployed using the [Helm Terraform Provider](https://registry.terraform.io/providers/hashicorp/helm/latest/docs). You can use the Helm provider to deploy software packages in Kubernetes. The provider needs to be configured with the proper credentials before it can be used.

1. [Azure Virtual Machine](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/linux_virtual_machine): Terraform modules can optionally create a jump-box virtual machine to manage the private AKS cluster.


> **NOTE**  
> In a production environment, we strongly recommend deploying a [private AKS cluster](https://docs.microsoft.com/en-us/azure/aks/private-clusters) with [Uptime SLA](https://docs.microsoft.com/en-us/azure/aks/uptime-sla). For more information, see [private AKS cluster with a Public DNS address](https://docs.microsoft.com/en-us/azure/aks/private-clusters#create-a-private-aks-cluster-with-a-public-dns-address). Alternatively, you can deploy a public AKS cluster and secure access to the API server using [authorized IP address ranges](https://learn.microsoft.com/en-us/azure/aks/api-server-authorized-ip-ranges).



<hr />

<a name="GitHub-Workflow-Actions"></a>

## GitHub Workflow Actions

<a target="_blank" href="https://www.youtube.com/watch?v=yfBtjLxn_6k">VIDEO</a>:
<a target="_blank" href="https://www.youtube.com/watch?v=-hVG9z0fCac&list=PLArH6NjfKsUhvGHrpag7SuPumMzQRhUKY&pp=iAQB">VIDEO</a>:
TODO: In the <tt>.github/workflow</tt> folder are GitHub Actions yaml files that define how to work.

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/.github/workflows/generator-generic-ossf-slsa3-publish.yml">VIEW: file <strong>generator-generic-ossf-slsa3-publish.yml</strong>

   The workflow (at <a target="_blank" href="https://github.com/slsa-framework/slsa-github-generator/">github.com/slsa-framework/slsa-github-generator
</a>) generates a supply-chain provenance file of the project to meet <a target="_blank" href="https://wilsonmar.github.io/slsa/">SLSA</a> for "level 3" as defined in <a target="_blank" href="https://slsa.dev/spec/v0.1/requirements">v0.1/requirements</a> from <a target="_blank" href="https://slsa.dev">slsa.dev</a>, an initiative of the <a target="_blank" href="https://www.openssf.org/">OpenSSF</a>.
   
Workflows are automatically invoked when a <tt>git push</tt> is performed.

For more on this topic:
   * https://wilsonmar.github.io/github-actions
   * https://github.com/actions
   * https://github.blog/2022-02-02-build-ci-cd-pipeline-github-actions-four-steps/
   * https://github.com/marketplace?category=&query=&type=actions&verification=
   <br /><br />

1. TODO: Add a GitHub Workflow to scan IaC code for vulnerabilities

   The ability to check for vulnerabilities before they are even created is a major benefit for using IaC (Terraform, Azure Resource Manager, Bicep) code.

   WARNING: Scanning by a third-party in the cloud means your code is read and possibly retained by those third-parties, a potential security risk.


1. TODO: Add a step in the GitHub workflow to create a SBOM (Software Bill of Materials) by iteratively (exhaustively) following the chain of packages referenced in each program. This would add many more to the list of 138 in the file. This enables raising an alert if any package:
   
   * is not of the latest version ;
   * has an open <a target="_blank" href="https://github.com/marketplace/actions/dependency-review">GitHub Dependabot alert</a>; 
   * has been found <a target="_blank" href="https://github.com/marketplace/actions/aqua-security-trivy">by Aqua Trivy</a> or <a target="_blank" href="https://github.com/marketplace/actions/snyk">Snyk</a> to have a vulnerability reported to CISA as a CVE;
   * contains code that can potentially be malicious actions such as exfiltration of data to a possible adversary (such as China, Russia, North Korea, Iran, etc.)
   * contains <a target="_blank" href="https://github.com/marketplace/actions/markdown-link-check">markdown text with broken links</a>
   <br /><br />

1. TODO: Use <a target="_blank" href="https://github.com/marketplace/actions/gitguardian-shield-action">Git Guardian</a> to highlight code that appears to have gibberish that might be a password, API key, or other secret;

1. TODO: <a target="_blank" href="https://github.com/marketplace/actions/git-semantic-version">Determine the semantic version of a repo</a> based on git history.

1. TODO: <a target="_blank" href="https://github.com/marketplace/actions/gh-release">Create a new release</a>
1. TODO: Run <a target="_blank" href="https://github.com/marketplace/actions/gitguardian-shield-action">Git Guardian</a>

1. TODO: Add a step to <a target="_blank" href="https://github.com/marketplace/actions/todo-to-issue">convert TODO comments in code to GitHub Issues.

<hr />



<a name="Subnet-IP-Addresses"></a>

### Subnet IP Addresses 

As specified in the diagram (below):

   * 10.0.0.2/8 = VNet
   * 10.240.0.0/16 = SystemSubnet
   * 10.241.0.0/16 = UserSubnet
   * 10.242.0.0/16 = PodSubnet
   * 10.243.0.0/27 = ApiServerSubnet
   * 10.243.2.0/24 = AzureBastion
   * 10.243.1.0/24 = VMSubnet
   * 10.243.1.0/24 = PodSubnet
   <br /><br  />



<a name="Shell-Scripts"></a>

## Shell Scripts


<a name="install-nginx-via-helm-and-create-sa.sh"></a>

## install-nginx-via-helm-and-create-sa.sh

The [Azure Deployment Script <tt><strong>resource_deployment_script_azure_cli</strong></tt> at [https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_deployment_script_azure_cli) is used to run the </tt><strong>install-nginx-via-helm-and-create-sa.sh</strong></tt> Bash script which:

   * creates the namespace and service account for the sample application and
   * installspackages to the AKS cluster via [Helm](https://helm.sh/). For more information from Terraform about deployment scripts, see [azurerm_resource_deployment_script_azure_cli](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_deployment_script_azure_cli)

   - [NGINX Ingress Controller](https://docs.nginx.com/nginx-ingress-controller/)
   - [Cert-Manager](https://cert-manager.io/docs/)
   - [Prometheus](https://prometheus.io/)
   <br /><br />

For more about Terraform:
   - [Azure OpenAI Terraform deployment for sample chatbot](https://github.com/Azure-Samples/azure-openai-terraform-deployment-sample/)
   - [Terraform module for deploying Azure OpenAI Service.](https://registry.terraform.io/modules/Azure/openai/azurerm/latest)
   - <a target="_blank" href="https://www.udemy.com/course/azure-kubernetes-service-with-azure-devops-and-terraform/learn/lecture/23628292#overview">Terraform install</a>
   <br /><br /> 


<hr />

## Kubernetes Components

Each cluster contains the following Docker containers and Kubernetes services:

<a target="_blank" href="https://res.cloudinary.com/dcajqrroq/image/upload/v1708723970/openai/workload.png"><img alt="workload.png" src="https://res.cloudinary.com/dcajqrroq/image/upload/v1708723970/openai/workload.png"></a>

All the containers above are collectively represented by one of group of <strong>purple icons</strong> at the right side of the diagram above and at the <a href="#Infrastructure-Architecture-Diagram">Infrastructure Architecture Diagram (below)</a>.

NOTE: <a target="_blank" href="https://learn.microsoft.com/en-us/azure/container-apps/overview">Azure Container Apps</a> serverless can

For more:
   - https://learn.microsoft.com/en-us/azure/aks/


<a name="install-packages-for-chainlit-demo.sh"></a>

### install-packages-for-chainlin-demo.sh

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/install-packages-for-chainlin-demo.sh">
<strong>VIEW: install-packages-for-chainlin-demo.sh</strong></a> which installs:
* kubectl as $clusterName using az commands to $subscriptionId
* helm using curl to download from the internet
   helm is used to install: https://cert-manager.io/docs/installation/helm/
* prometheus

* jetstack
   * https://github.com/jetstack
   * Jetstack is cert-manager inventor/maintainer James Munnelly

* cert-manager jetstack [v1.8.0] at 
   * https://cert-manager.io/docs/releases/ 
   * https://artifacthub.io/packages/search?org=cert-manager
   * https://medium.com/@dikshantdevops/how-to-setup-cert-manager-in-your-cluster-358d519da2c5
   * issues and renewes X.509 certificates from within Kubernetes clusters
   * seamlessly integrates with Let’s Encrypt, a free and widely trusted certificate authority, 
   * https://venafi.com/open-source/cert-manager/

   <pre>kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.12.0/cert-manager.crds.yaml
helm repo add jetstack https://charts.jetstack.io
helm repo update
kubectl get cert-manager -n cert-manager
   </pre>

   That's in:
   * terraform/modules/kubernetes/cluster_issuers.tf 
   * scripts/cluster-issuer-nginx.yml 
   * scripts/cluster-issuer-webapprouting.yml /Users/wilsonmar/bomonike/azure-chatgbt/scripts/install-packages-for-chainlit-demo.sh

* $namespace using kubectl

* $serviceAccountName
   <br /><br />



<hr />

<a name="09-deploy-apps.sh"></a>

### 09-deploy-apps.sh

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/09-deploy-apps.sh">
<strong>VIEW: 09-deploy-apps.sh</strong></a> runs kubectl apply by referencing:

   * <a target="_blank" href="#docs-configmap.yml">docs-configmap.yml</a> ?
   * <a target="_blank" href="#docs-deployment.yml">docs-deployment.yml</a>
   * <a target="_blank" href="#docs-ingress.yml">docs-ingress.yml</a> ?
   * <a target="_blank" href="#docs-service.yml">docs-service.yml</a> ?

   * <a target="_blank" href="#chat-configmap.yml">chat-configmap.yml</a>  ?
   * <a target="_blank" href="#chat-deployment.yml">chat-deployment.yml</a>
   * <a target="_blank" href="#chat-ingress.yml">chat-ingress.yml</a>  ?
   * <a target="_blank" href="#chat-service.yml">chat-service.yml</a>

   * <a target="_blank" href="#cluster-issuer-webapprouting.yml">cluster-issuer-webapprouting.yml</a> ?
   * <a target="_blank" href="#cluster-issuer-nginx.yml">cluster-issuer-nginx.yml</a> ?

   * <a target="_blank" href="#docs.py">docs.py</a> ?
   * <a target="_blank" href="#chainlit.md">chainlit.md</a>
   <br /><br />

<hr />

<a name="10-configure-dns.sh"></a>

### 10-configure-dns.sh

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/10-configure-dns.sh">
VIEW: 10-configure-dns.sh</a>

<tt>apt install -y jq</tt>

   * Choose the ingress controller to use based on $ingressClassName
   * Retrieve the public IP address of the NGINX ingress controller based on $publicIpAddress
For each $subdomain 
   * Remove the A record
   * Create the A record


<hr />

<a name="chat-configmap.yml"></a>

### chat-configmap.yml

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/chat-configmap.yml">VIEW: chat-configmap.yml</a>


<a name="chat-ingress.yml"></a>

### chat-ingress.yml

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/chat-ingress.yml">VIEW: chat-ingress.yml</a>


<a name="chat-service"></a>

### chat-service.yml

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/chat-service.yml">VIEW: chat-service.yml</a>


<a name="cluster-issuer-nginx.yml"></a>

### cluster-issuer-webapprouting.yml

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/cluster-issuer-webapprouting.yml">VIEW: cluster-issuer-webapprouting.yml</a>


<a name="cluster-issuer-webapprouting.yml"></a>

### cluster-issuer-nginx.yml

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/cluster-issuer-nginx.yml">VIEW: cluster-issuer-nginx.yml</a>


<hr />

## docs app

<a name="docs.py"></a>

### docs.py

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/docs.py">docs.py</a>


<a name="docs-configmap.yml"></a>

### docs-configmap.yml

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/docs-configmap.yml">VIEW: docs-configmap.yml</a>


<a name="docs-ingress.yml"></a>

### docs-ingress.yml

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/docs-ingress.yml">VIEW: docs-ingress.yml</a>


<a name="docs-service.yml"></a>

### docs-service.yml

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/docs-service.yml">VIEW: docs-service.yml</a>


<a name="chainlit.md"></a>

### chainlit.md

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/chainlit.md">chainlit.md</a>


<hr />

<a name="03-push-docker-image.sh"></a>

### 03-push-docker-image.sh

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/03-push-docker-image.sh">
VIEW: 03-push-docker-image.sh</a> that it references from 00-variables.sh:

   * $acrName
Creates:
   * $loginServer

```bash
#!/bin/bash

# Variables
source ./00-variables.sh

# Login to ACR
az acr login --name $acrName 

# Retrieve ACR login server. Each container image needs to be tagged with the loginServer name of the registry. 
loginServer=$(az acr show --name $acrName --query loginServer --output tsv)

# Tag the local image with the loginServer of ACR
docker tag ${imageName,,}:$tag $loginServer/${imageName,,}:$tag

# Push latest container image to ACR
docker push $loginServer/${imageName,,}:$tag
```


<a name="Cert-Manager"></a>

### Cert-Manager

1. [Cert-Manager](https://cert-manager.io/docs/): the Kubernetes `cert-manager` package and [Let's Encrypt](https://letsencrypt.org/) certificate authority are used to issue a TLS/SSL certificate to the chat applications.

   ### Let's Encrypt

1. Let's encrypt creates free SSL/TLS certificate so websites can communicate using HTTPS (secure HTTP).

<a target="_blank" href="https://learn.microsoft.com/en-us/entra/identity-platform/howto-create-self-signed-certificate">Create a self-signed public certificate to authenticate your application</a>  Microsoft identity platform

<hr />

<a name="Build-Kubernetes"></a>

## Build Kubernetes

   The containers are retrieved by the
   [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes) built upon the open-source Kubernetes framework from Google.

   To continue running with a cluster of containers when another one fails, Kubernetes runs several clusters, each consists of the same set of "pods":

   Each cluster communicates with the [Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/overview) via an [Azure Private Endpoint](https://docs.microsoft.com/en-us/azure/private-link/private-endpoint-overview). 

   - [Azure Kubernetes Service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/kubernetes_cluster):  A public or private [Azure Kubernetes Service(AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes) cluster composed of a:
   - A `system` node pool in a dedicated subnet. The default node pool hosts only critical system pods and services. The worker nodes have node taint which prevents application pods from beings scheduled on this node pool.
   - A `user` node pool hosting user workloads and artifacts in a dedicated subnet.
   <br /><br />
   

<hr />

## Configure Terraform Variables

The repo provides a template to deploy an [Azure Kubernetes Service(AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes) cluster and [Azure OpenAI Service](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/overview) using [Terraform](https://www.terraform.io/) modules using the [Azure Provider](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs) Terraform Provider.

How to deploy a Python chatbot that authenticates against Azure OpenAI using [Entra workload identity](https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview) and calls the [Chat Completion API](https://platform.openai.com/docs/api-reference/chat) of a [ChatGPT model](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#chatgpt-gpt-35-turbo). For a Bicep version of the demo, see [How to deploy and run an Azure OpenAI ChatGPT application on AKS via Bicep](https://github.com/Azure-Samples/aks-openai).

In a production environment, we strongly recommend deploying a [private AKS cluster](https://docs.microsoft.com/en-us/azure/aks/private-clusters) with [Uptime SLA](https://docs.microsoft.com/en-us/azure/aks/uptime-sla). For more information, see [private AKS cluster with a Public DNS address](https://docs.microsoft.com/en-us/azure/aks/private-clusters#create-a-private-aks-cluster-with-a-public-dns-address). Alternatively, you can deploy a public AKS cluster and secure access to the API server using [authorized IP address ranges](https://learn.microsoft.com/en-us/azure/aks/api-server-authorized-ip-ranges).


### openai

The following table contains the code from the `openai.tf` Terraform module used to deploy the [Azure OpenAI Service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/cognitive_account).

zzz

```terraform
resource "azurerm_cognitive_account" "openai" {
  name                          = var.name
  location                      = var.location
  resource_group_name           = var.resource_group_name
  kind                          = "OpenAI"
  custom_subdomain_name         = var.custom_subdomain_name
  sku_name                      = var.sku_name
  public_network_access_enabled = var.public_network_access_enabled
  tags                          = var.tags

  identity {
    type = "SystemAssigned"
  }

  lifecycle {
    ignore_changes = [
      tags
    ]
  }
}

resource "azurerm_cognitive_deployment" "deployment" {
  for_each             = {for deployment in var.deployments: deployment.name => deployment}

  name                 = each.key
  cognitive_account_id = azurerm_cognitive_account.openai.id

  model {
    format  = "OpenAI"
    name    = each.value.model.name
    version = each.value.model.version
  }

  scale {
    type = "Standard"
  }
}

resource "azurerm_monitor_diagnostic_setting" "settings" {
  name                       = "DiagnosticsSettings"
  target_resource_id         = azurerm_cognitive_account.openai.id
  log_analytics_workspace_id = var.log_analytics_workspace_id

  enabled_log {
    category = "Audit"

    retention_policy {
      enabled = true
      days    = var.log_analytics_retention_days
    }
  }

  enabled_log {
    category = "RequestResponse"

    retention_policy {
      enabled = true
      days    = var.log_analytics_retention_days
    }
  }

  enabled_log {
    category = "Trace"

    retention_policy {
      enabled = true
      days    = var.log_analytics_retention_days
    }
  }

  metric {
    category = "AllMetrics"

    retention_policy {
      enabled = true
      days    = var.log_analytics_retention_days
    }
  }
}
```

Azure Cognitive Services use custom subdomain names for each resource created through the [Azure portal](https://portal.azure.com), [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), [Azure CLI](/cli/azure/install-azure-cli), [Bicep](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview), [Azure Resource Manager (ARM)](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/overview), or [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/cognitive_account). Unlike regional endpoints, which were common for all customers in a specific Azure region, custom subdomain names are unique to the resource. Custom subdomain names are required to enable features like Azure Active Directory (Entra) for authentication. In our case, we need to specify a custom subdomain for our [Azure OpenAI Service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/cognitive_account) as our chatbot application will use an Entra security token to access it. By default, the `main.tf` module sets the value of the `custom_subdomain_name` parameter to the lowercase name of the Azure OpenAI resource. For more information on custom subdomains, see [Custom subdomain names for Cognitive Services](https://learn.microsoft.com/en-us/azure/cognitive-services/cognitive-services-custom-subdomains?source=docs).

This terraform module allows you to pass an array containing the definition of one or more model deployments in the `deployments` parameter. For more information on model deployments, see [Create a resource and deploy a model using Azure OpenAI](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/how-to/create-resource?pivots=web-portal).

As an alternative, you can use the [Terraform module for deploying Azure OpenAI Service.](https://registry.terraform.io/modules/Azure/openai/azurerm/latest) to deploy an [Azure OpenAI Service](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/cognitive_account).



### private_dns_zone

Below you can read the code of the `private_dns_zone` and `private_endpoint` modules used, respectively, to create the [Azure Private Endpoints](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/private_endpoint) and [Azure Private DNDS Zones](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/data-sources/private_dns_zone).

```terraform
resource "azurerm_private_dns_zone" "private_dns_zone" {
  name                = var.name
  resource_group_name = var.resource_group_name
  tags                = var.tags

  lifecycle {
    ignore_changes = [
      tags
    ]
  }
}

resource "azurerm_private_dns_zone_virtual_network_link" "link" {
  for_each = var.virtual_networks_to_link

  name                  = "link_to_${lower(basename(each.key))}"
  resource_group_name   = var.resource_group_name
  private_dns_zone_name = azurerm_private_dns_zone.private_dns_zone.name
  virtual_network_id    = "/subscriptions/${each.value.subscription_id}/resourceGroups/${each.value.resource_group_name}/providers/Microsoft.Network/virtualNetworks/${each.key}"

  lifecycle {
    ignore_changes = [
      tags
    ]
  }
}
```

### private_endpoint

```terraform
resource "azurerm_private_endpoint" "private_endpoint" {
  name                = var.name
  location            = var.location
  resource_group_name = var.resource_group_name
  subnet_id           = var.subnet_id
  tags                = var.tags

  private_service_connection {
    name                           = "${var.name}Connection"
    private_connection_resource_id = var.private_connection_resource_id
    is_manual_connection           = var.is_manual_connection
    subresource_names              = try([var.subresource_name], null)
    request_message                = try(var.request_message, null)
  }

  private_dns_zone_group {
    name                 = var.private_dns_zone_group_name
    private_dns_zone_ids = var.private_dns_zone_group_ids
  }

  lifecycle {
    ignore_changes = [
      tags
    ]
  }
}
```

## AKS Workload User-Defined Managed Identity

The following code snippet from the `main.tf` Terraform module creates the user-defined managed identity used by the chatbot to acquire a security token from Azure Active Directory via [Entra workload identity](https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview).

```terraform
resource "azurerm_user_assigned_identity" "aks_workload_identity" {
  name                = var.name_prefix == null ? "${random_string.prefix.result}${var.workload_managed_identity_name}" : "${var.name_prefix}${var.workload_managed_identity_name}"
  resource_group_name = azurerm_resource_group.rg.name
  location            = var.location
  tags                = var.tags

  lifecycle {
    ignore_changes = [
      tags
    ]
  }
}

resource "azurerm_role_assignment" "cognitive_services_user_assignment" {
  scope                = module.openai.id
  role_definition_name = "Cognitive Services User"
  principal_id         = azurerm_user_assigned_identity.aks_workload_identity.principal_id
  skip_service_principal_aad_check = true
}

resource "azurerm_federated_identity_credential" "federated_identity_credential" {
  name                = "${title(var.namespace)}FederatedIdentity"
  resource_group_name = azurerm_resource_group.rg.name
  audience            = ["api://AzureADTokenExchange"]
  issuer              = module.aks_cluster.oidc_issuer_url
  parent_id           = azurerm_user_assigned_identity.aks_workload_identity.id
  subject             = "system:serviceaccount:${var.namespace}:${var.service_account_name}"
}
```

The above code snippet performs the following steps:

- Creates a new user-defined managed identity.
- Assign the new managed identity to the Cognitive Services User role with the resource group as a scope.
- Federate the managed identity with the service account used by the chatbot. The following information is necessary to create the federated identity credentials:
  - The Kubernetes service account name.
  - The Kubernetes namespace that will host the chatbot application.
  - The URL of the OpenID Connect (OIDC) token issuer endpoint for [Entra workload identity](https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview)

For more information, see the following resources:

- [How to configure Azure OpenAI Service with managed identities](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/how-to/managed-identity)
- [Use Entra workload identity with Azure Kubernetes Service (AKS)](https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview)


## Validate the deployment

Open the Azure Portal, and navigate to the resource group. Open the Azure Open AI Service resource, navigate to `Keys and Endpoint`, and check that the endpoint contains a custom subdomain rather than the regional Cognitive Services endpoint.

<a target="_blank" href="https://res.cloudinary.com/dcajqrroq/image/upload/v1708723968/openai/openai.png"><img alt="OpenAI Key and Endpoint" src="https://res.cloudinary.com/dcajqrroq/image/upload/v1708723968/openai/openai.png"></a>

Open to the `<Prefix>WorkloadManagedIdentity` managed identity, navigate to the `Federated credentials`, and verify that the federated identity credentials for the `magic8ball-sa` service account were created correctly, as shown in the following picture.

<a target="_blank" href="https://res.cloudinary.com/dcajqrroq/image/upload/v1708723967/openai/federatedidentitycredentials.png"><img alt="Federated Identity Credentials" src="https://res.cloudinary.com/dcajqrroq/image/upload/v1708723967/openai/federatedidentitycredentials.png"></a>


## Use Entra workload identity with Azure Kubernetes Service (AKS)

Workloads deployed on an Azure Kubernetes Services (AKS) cluster require Azure Active Directory (Entra) application credentials or managed identities to access Entra protected resources, such as Azure Key Vault and Microsoft Graph. Entra workload identity integrates with the capabilities native to Kubernetes to federate with external identity providers.

[Entra workload identity](https://learn.microsoft.com/en-us/azure/active-directory/develop/workload-identities-overview) uses [Service Account Token Volume Projection](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#serviceaccount-token-volume-projection) to enable pods to use a Kubernetes service account. When enabled, the [AKS OIDC Issuer](https://learn.microsoft.com/en-us/azure/aks/use-oidc-issuer) issues a service account security token to a workload and [OIDC federation](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#openid-connect-tokens) enables the application to access Azure resources securely with Entra based on annotated service accounts.

Entra workload identity works well with the [Azure Identity client libraries](#azure-identity-client-libraries) and the [Microsoft Authentication Library (MSAL)](https://learn.microsoft.com/en-us/azure/active-directory/develop/msal-overview) collection if you use a [registered application](https://learn.microsoft.com/en-us/azure/active-directory/develop/application-model#register-an-application) instead of a managed identity. Your workload can use any of these libraries to seamlessly authenticate and access Azure cloud resources.

For more information, see the following resources:

- [Azure Workload Identity open-source project](https://azure.github.io/azure-workload-identity)
- [Use an Entra workload identity on Azure Kubernetes Service (AKS](https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview)
- [Deploy and configure workload identity on an Azure Kubernetes Service (AKS) cluster](https://learn.microsoft.com/en-us/azure/aks/workload-identity-deploy-cluster)
- [Modernize application authentication with workload identity sidecar](https://learn.microsoft.com/en-us/azure/aks/workload-identity-migration-sidecar)
- [Tutorial: Use a workload identity with an application on Azure Kubernetes Service (AKS)](https://learn.microsoft.com/en-us/azure/aks/learn/tutorial-kubernetes-workload-identity)
- [Workload identity federation](https://docs.microsoft.com/azure/active-directory/develop/workload-identity-federation)
- [Use Entra Workload Identity for Kubernetes with a User-Assigned Managed Identity](https://techcommunity.microsoft.com/t5/fasttrack-for-azure/use-azure-ad-workload-identity-for-kubernetes-with-a-user/ba-p/3654928)
- [Use Entra workload identity for Kubernetes with an Entra registered application](https://techcommunity.microsoft.com/t5/fasttrack-for-azure/use-azure-ad-workload-identity-for-kubernetes-in-a-net-standard/ba-p/3576218)
- [Azure Managed Identities with Workload Identity Federation](https://blog.identitydigest.com/azuread-federate-mi/)
- [Entra workload identity federation with Kubernetes](https://blog.identitydigest.com/azuread-federate-k8s/)
- [Azure Active Directory Workload Identity Federation with external OIDC Identy Providers](https://arsenvlad.medium.com/azure-active-directory-workload-identity-federation-with-external-oidc-idp-4f06c9205a26)
- [Minimal Entra Workload identity federation](https://cookbook.geuer-pollmann.de/azure/workload-identity-federation)

## Azure Identity client libraries

In the Azure Identity client libraries, you can choose one of the following approaches:

- Use `DefaultAzureCredential`, which will attempt to use the `WorkloadIdentityCredential`.
- Create a `ChainedTokenCredential` instance that includes `WorkloadIdentityCredential`.
- Use `WorkloadIdentityCredential` directly.

The following table provides the **minimum** package version required for each language's client library.

| Language   | Library                                                                                      | Minimum Version | Example                                                                                           |
|------------|----------------------------------------------------------------------------------------------|-----------------|---------------------------------------------------------------------------------------------------|
| .NET       | [Azure.Identity](/dotnet/api/overview/azure/identity-readme)      | 1.9.0    | [Link](https://github.com/Azure/azure-workload-identity/tree/main/examples/azure-identity/dotnet) |
| Go         | [azidentity](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/sdk/azidentity)            | 1.3.0    | [Link](https://github.com/Azure/azure-workload-identity/tree/main/examples/azure-identity/go)     |
| Java       | [azure-identity](/java/api/overview/azure/identity-readme)        | 1.9.0    | [Link](https://github.com/Azure/azure-workload-identity/tree/main/examples/azure-identity/java)   |
| JavaScript | [@azure/identity](/javascript/api/overview/azure/identity-readme) | 3.2.0    | [Link](https://github.com/Azure/azure-workload-identity/tree/main/examples/azure-identity/node)   |
| Python     | [azure-identity](/python/api/overview/azure/identity-readme)      | 1.13.0        | [Link](https://github.com/Azure/azure-workload-identity/tree/main/examples/azure-identity/python) |

## Microsoft Authentication Library (MSAL)

The following client libraries are the **minimum** version required

| Language | Library | Image | Example | Has Windows |
|-----------|-----------|----------|----------|----------|
| .NET | [microsoft-authentication-library-for-dotnet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ghcr.io/azure/azure-workload-identity/msal-net | [Link](https://github.com/Azure/azure-workload-identity/tree/main/examples/msal-net/akvdotnet) | Yes |
| Go | [microsoft-authentication-library-for-go](https://github.com/AzureAD/microsoft-authentication-library-for-go) | ghcr.io/azure/azure-workload-identity/msal-go | [Link](https://github.com/Azure/azure-workload-identity/tree/main/examples/msal-go) | Yes |
| Java | [microsoft-authentication-library-for-java](https://github.com/AzureAD/microsoft-authentication-library-for-java) | ghcr.io/azure/azure-workload-identity/msal-java | [Link](https://github.com/Azure/azure-workload-identity/tree/main/examples/msal-java) | No |
| JavaScript | [microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) | ghcr.io/azure/azure-workload-identity/msal-node | [Link](https://github.com/Azure/azure-workload-identity/tree/main/examples/msal-node) | No |
| Python | [microsoft-authentication-library-for-python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | ghcr.io/azure/azure-workload-identity/msal-python | [Link](https://github.com/Azure/azure-workload-identity/tree/main/examples/msal-python) | No |

## Deployment Script

The sample makes use of a [Deployment Script](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_deployment_script_azure_cli) to run the `install-nginx-via-helm-and-create-sa.sh` Bash script that creates the namespace and service account for the sample application and installs the following packages to the AKS cluster via [Helm](https://helm.sh/).

- [NGINX Ingress Controller](https://docs.nginx.com/nginx-ingress-controller/)
- [Cert-Manager](https://cert-manager.io/docs/)
- [Prometheus](https://prometheus.io/)

This sample uses the [NGINX Ingress Controller](https://docs.nginx.com/nginx-ingress-controller/) to expose the chatbot to the public internet.

```bash
# Install kubectl
az aks install-cli --only-show-errors

# Get AKS credentials
az aks get-credentials \
  --admin \
  --name $clusterName \
  --resource-group $resourceGroupName \
  --subscription $subscriptionId \
  --only-show-errors

# Check if the cluster is private or not
private=$(az aks show --name $clusterName \
  --resource-group $resourceGroupName \
  --subscription $subscriptionId \
  --query apiServerAccessProfile.enablePrivateCluster \
  --output tsv)

# Install Helm
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 -o get_helm.sh -s
chmod 700 get_helm.sh
./get_helm.sh &>/dev/null

# Add Helm repos
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo add jetstack https://charts.jetstack.io

# Update Helm repos
helm repo update

if [[ $private == 'true' ]]; then
  # Log whether the cluster is public or private
  echo "$clusterName AKS cluster is public"

  # Install Prometheus
  command="helm install prometheus prometheus-community/kube-prometheus-stack \
  --create-namespace \
  --namespace prometheus \
  --set prometheus.prometheusSpec.podMonitorSelectorNilUsesHelmValues=false \
  --set prometheus.prometheusSpec.serviceMonitorSelectorNilUsesHelmValues=false"

  az aks command invoke \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --subscription $subscriptionId \
    --command "$command"

  # Install NGINX ingress controller using the internal load balancer
  command="helm install nginx-ingress ingress-nginx/ingress-nginx \
    --create-namespace \
    --namespace ingress-basic \
    --set controller.replicaCount=3 \
    --set controller.nodeSelector.\"kubernetes\.io/os\"=linux \
    --set defaultBackend.nodeSelector.\"kubernetes\.io/os\"=linux \
    --set controller.metrics.enabled=true \
    --set controller.metrics.serviceMonitor.enabled=true \
    --set controller.metrics.serviceMonitor.additionalLabels.release=\"prometheus\" \
    --set controller.service.annotations.\"service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path\"=/healthz"

  az aks command invoke \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --subscription $subscriptionId \
    --command "$command"

  # Install certificate manager
  command="helm install cert-manager jetstack/cert-manager \
    --create-namespace \
    --namespace cert-manager \
    --set installCRDs=true \
    --set nodeSelector.\"kubernetes\.io/os\"=linux"

  az aks command invoke \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --subscription $subscriptionId \
    --command "$command"

  # Create cluster issuer
  command="cat <<EOF | kubectl apply -f -
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-nginx
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: $email
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
EOF"

  az aks command invoke \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --subscription $subscriptionId \
    --command "$command"

  # Create workload namespace
  command="kubectl create namespace $namespace"

  az aks command invoke \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --subscription $subscriptionId \
    --command "$command"

  # Create service account
  command="cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    azure.workload.identity/client-id: $workloadManagedIdentityClientId
    azure.workload.identity/tenant-id: $tenantId
  labels:
    azure.workload.identity/use: "true"
  name: $serviceAccountName
  namespace: $namespace
EOF"

  az aks command invoke \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --subscription $subscriptionId \
    --command "$command"

else
  # Log whether the cluster is public or private
  echo "$clusterName AKS cluster is private"

  # Install Prometheus
  helm install prometheus prometheus-community/kube-prometheus-stack \
    --create-namespace \
    --namespace prometheus \
    --set prometheus.prometheusSpec.podMonitorSelectorNilUsesHelmValues=false \
    --set prometheus.prometheusSpec.serviceMonitorSelectorNilUsesHelmValues=false

  # Install NGINX ingress controller using the internal load balancer
  helm install nginx-ingress ingress-nginx/ingress-nginx \
    --create-namespace \
    --namespace ingress-basic \
    --set controller.replicaCount=3 \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.metrics.enabled=true \
    --set controller.metrics.serviceMonitor.enabled=true \
    --set controller.metrics.serviceMonitor.additionalLabels.release="prometheus" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path"=/healthz

  helm install $nginxReleaseName $nginxRepoName/$nginxChartName \
    --create-namespace \
    --namespace $nginxNamespace

  # Install certificate manager
  helm install cert-manager jetstack/cert-manager \
    --create-namespace \
    --namespace cert-manager \
    --set installCRDs=true \
    --set nodeSelector."kubernetes\.io/os"=linux

  # Create cluster issuer
  cat <<EOF | kubectl apply -f -
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-nginx
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: $email
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
EOF

  # Create workload namespace
  kubectl create namespace $namespace

  # Create service account
  cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    azure.workload.identity/client-id: $workloadManagedIdentityClientId
    azure.workload.identity/tenant-id: $tenantId
  labels:
    azure.workload.identity/use: "true"
  name: $serviceAccountName
  namespace: $namespace
EOF

fi

# Create output as JSON file
echo '{}' |
  jq --arg x $namespace '.namespace=$x' |
  jq --arg x $serviceAccountName '.serviceAccountName=$x' |
  jq --arg x 'prometheus' '.prometheus=$x' |
  jq --arg x 'cert-manager' '.certManager=$x' |
  jq --arg x 'ingress-basic' '.nginxIngressController=$x' >$AZ_SCRIPTS_OUTPUT_PATH
```

The `install-nginx-via-helm-and-create-sa.sh` Bash script can run on a public AKS cluster or on a private AKS cluster using the [az aks command invoke](<https://learn.microsoft.com/en-us/cli/azure/aks/command?view=azure-cli-latest#az-aks-command-invoke>). For more information, see [Use command invoke to access a private Azure Kubernetes Service (AKS) cluster](https://learn.microsoft.com/en-us/azure/aks/command-invoke).

The `install-nginx-via-helm-and-create-sa.sh` Bash script returns the following outputs to the deployment script:

- Namespace hosting the chatbot sample. You can change the default `magic8ball` namespace by assigning a different value to the `namespace` variable in the `terraform.tfvars` file.
- Service account name
- Prometheus namespace
- Cert-manager namespace
- NGINX ingress controller namespace

You can find the `install-nginx-via-helm-and-create-sa.sh` file under the `terraform` folder.

<hr />

## Chatbot Application

The chatbot is a Python application inspired by the sample code in the [It’s Time To Create A Private ChatGPT For Yourself Today](https://levelup.gitconnected.com/its-time-to-create-a-private-chatgpt-for-yourself-today-6503649e7bb6) arctiel. The application is contained in a single file called `app.py`. 

This application makes use of the following libraries:

- [OpenAPI](https://github.com/openai/openai-python): The OpenAI Python library provides convenient access to the OpenAI API from applications written in the Python language. It includes a pre-defined set of classes for API resources that initialize themselves dynamically from API responses which makes it compatible with a wide range of versions of the OpenAI API. You can find usage examples for the OpenAI Python library in our [API reference](https://beta.openai.com/docs/api-reference?lang=python) and the [OpenAI Cookbook](https://github.com/openai/openai-cookbook/).

- [Azure Identity](https://learn.microsoft.com/en-us/python/api/overview/azure/identity-readme?view=azure-python): The Azure Identity library provides [Azure Active Directory (Entra)](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-whatis) token authentication support across the Azure SDK. It provides a set of [TokenCredential](https://learn.microsoft.com/en-us/python/api/azure-core/azure.core.credentials.tokencredential?view=azure-python) implementations, which can be used to construct Azure SDK clients that support Entra token authentication.

- [Streamlit](https://github.com/streamlit/streamlit): Streamlit is an open-source Python library that makes it easy to create and share beautiful, custom web apps for machine learning and data science. In just a few minutes you can build and deploy powerful data apps. For more information, see [Streamlit documentation](https://docs.streamlit.io/)

- [Streamlit-chat](https://github.com/AI-Yash/st-chat): a Streamlit component that provides a configurable user interface for chatbot applications.

- [Dotenv](https://github.com/theskumar/python-dotenv): Python-dotenv reads key-value pairs from a .env file and can set them as environment variables. It helps in the development of applications following the [12-factor](http://12factor.net/) principles.

The `requirements.txt` file under the `scripts` folder contains the list of packages used by the `app.py` application that you can restore using the following command:

   ```bash
pip install -r requirements.txt --upgrade
   ```

<a name=".env"></a>

### .env

* <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/.env">VIEW: .env</a> - the fle 

   ```
AZURE_OPENAI_TYPE=azure_ad
   ```


<a name="app.py"></a>

### app.py

1. <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/app.py">VIEW: app.py</a> - Python code for the `app.py` chatbot:

   ```python
# Import packages
import os
import sys
import time
import openai
import logging
import streamlit as st
from streamlit_chat import message
from azure.identity import DefaultAzureCredential
from dotenv import load_dotenv
from dotenv import dotenv_values

# Load environment variables from .env file
if os.path.exists(".env"):
  load_dotenv(override=True)
  config = dotenv_values(".env")

# Read environment variables
assistan_profile = """
You are the infamous Magic 8 Ball. You need to randomly reply to any question with one of the following answers:

- It is certain.
- It is decidedly so.
- Without a doubt.
- Yes definitely.
- You may rely on it.
- As I see it, yes.
- Most likely.
- Outlook good.
- Yes.
- Signs point to yes.
- Reply hazy, try again.
- Ask again later.
- Better not tell you now.
- Cannot predict now.
- Concentrate and ask again.
- Don't count on it.
- My reply is no.
- My sources say no.
- Outlook not so good.
- Very doubtful.

Add a short comment in a pirate style at the end! Follow your heart and be creative! 
For mor information, see https://en.wikipedia.org/wiki/Magic_8_Ball
"""
title = os.environ.get("TITLE", "Magic 8 Ball")
text_input_label = os.environ.get("TEXT_INPUT_LABEL", "Pose your question and cross your fingers!")
image_file_name = os.environ.get("IMAGE_FILE_NAME", "magic8ball.png")
image_width = int(os.environ.get("IMAGE_WIDTH", 80))
temperature = float(os.environ.get("TEMPERATURE", 0.9))
system = os.environ.get("SYSTEM", assistan_profile)
api_base = os.getenv("AZURE_OPENAI_BASE")
api_key = os.getenv("AZURE_OPENAI_KEY")
api_type = os.environ.get("AZURE_OPENAI_TYPE", "azure")
api_version = os.environ.get("AZURE_OPENAI_VERSION", "2023-05-15")
engine = os.getenv("AZURE_OPENAI_DEPLOYMENT")
model = os.getenv("AZURE_OPENAI_MODEL")

# Configure OpenAI
openai.api_type = api_type
openai.api_version = api_version
openai.api_base = api_base 

# Set default Azure credential
default_credential = DefaultAzureCredential() if openai.api_type == "azure_ad" else None

# Configure a logger
logging.basicConfig(stream = sys.stdout, 
                    format = '[%(asctime)s] {%(filename)s:%(lineno)d} %(levelname)s - %(message)s',
                    level = logging.INFO)
logger = logging.getLogger(__name__)

# Log variables
logger.info(f"title: {title}")
logger.info(f"text_input_label: {text_input_label}")
logger.info(f"image_file_name: {image_file_name}")
logger.info(f"image_width: {image_width}")
logger.info(f"temperature: {temperature}")
logger.info(f"system: {system}")
logger.info(f"api_base: {api_base}")
logger.info(f"api_key: {api_key}")
logger.info(f"api_type: {api_type}")
logger.info(f"api_version: {api_version}")
logger.info(f"engine: {engine}")
logger.info(f"model: {model}")

# Authenticate to Azure OpenAI
if openai.api_type == "azure":
  openai.api_key = api_key
elif openai.api_type == "azure_ad":
  openai_token = default_credential.get_token("https://cognitiveservices.azure.com/.default")
  openai.api_key = openai_token.token
  if 'openai_token' not in st.session_state:
    st.session_state['openai_token'] = openai_token
else:
  logger.error("Invalid API type. Please set the AZURE_OPENAI_TYPE environment variable to azure or azure_ad.")
  raise ValueError("Invalid API type. Please set the AZURE_OPENAI_TYPE environment variable to azure or azure_ad.")

# Customize Streamlit UI using CSS
st.markdown("""
<style>

div.stButton > button:first-child {
    background-color: #eb5424;
    color: white;
    font-size: 20px;
    font-weight: bold;
    border-radius: 0.5rem;
    padding: 0.5rem 1rem;
    border: none;
    box-shadow: 0 0.5rem 1rem rgba(0,0,0,0.15);
    width: 300 px;
    height: 42px;
    transition: all 0.2s ease-in-out;
} 

div.stButton > button:first-child:hover {
    transform: translateY(-3px);
    box-shadow: 0 1rem 2rem rgba(0,0,0,0.15);
}

div.stButton > button:first-child:active {
    transform: translateY(-1px);
    box-shadow: 0 0.5rem 1rem rgba(0,0,0,0.15);
}

div.stButton > button:focus:not(:focus-visible) {
    color: #FFFFFF;
}

@media only screen and (min-width: 768px) {
  /* For desktop: */
  div {
      font-family: 'Roboto', sans-serif;
  }

  div.stButton > button:first-child {
      background-color: #eb5424;
      color: white;
      font-size: 20px;
      font-weight: bold;
      border-radius: 0.5rem;
      padding: 0.5rem 1rem;
      border: none;
      box-shadow: 0 0.5rem 1rem rgba(0,0,0,0.15);
      width: 300 px;
      height: 42px;
      transition: all 0.2s ease-in-out;
      position: relative;
      bottom: -32px;
      right: 0px;
  } 

  div.stButton > button:first-child:hover {
      transform: translateY(-3px);
      box-shadow: 0 1rem 2rem rgba(0,0,0,0.15);
  }

  div.stButton > button:first-child:active {
      transform: translateY(-1px);
      box-shadow: 0 0.5rem 1rem rgba(0,0,0,0.15);
  }

  div.stButton > button:focus:not(:focus-visible) {
      color: #FFFFFF;
  }

  input {
      border-radius: 0.5rem;
      padding: 0.5rem 1rem;
      border: none;
      box-shadow: 0 0.5rem 1rem rgba(0,0,0,0.15);
      transition: all 0.2s ease-in-out;
      height: 40px;
  }
}
</style>
""", unsafe_allow_html=True)

# Initialize Streamlit session state
if 'prompts' not in st.session_state:
  st.session_state['prompts'] = [{"role": "system", "content": system}]

if 'generated' not in st.session_state:
  st.session_state['generated'] = []

if 'past' not in st.session_state:
  st.session_state['past'] = []

# Refresh the OpenAI security token every 45 minutes
def refresh_openai_token():
  if st.session_state['openai_token'].expires_on < int(time.time()) - 45 * 60:
      st.session_state['openai_token'] = default_credential.get_token("https://cognitiveservices.azure.com/.default")
      openai.api_key = st.session_state['openai_token'].token

# Send user prompt to Azure OpenAI 
def generate_response(prompt):
  try:
    st.session_state['prompts'].append({"role": "user", "content": prompt})

    if openai.api_type == "azure_ad":
      refresh_openai_token()

    completion = openai.ChatCompletion.create(
      engine = engine,
      model = model,
      messages = st.session_state['prompts'],
      temperature = temperature,
    )
    
    message = completion.choices[0].message.content
    return message
  except Exception as e:
    logging.exception(f"Exception in generate_response: {e}")

# Reset Streamlit session state to start a new chat from scratch
def new_click():
  st.session_state['prompts'] = [{"role": "system", "content": system}]
  st.session_state['past'] = []
  st.session_state['generated'] = []
  st.session_state['user'] = ""

# Handle on_change event for user input
def user_change():
  # Avoid handling the event twice when clicking the Send button
  chat_input = st.session_state['user']
  st.session_state['user'] = ""
  if (chat_input == '' or
      (len(st.session_state['past']) > 0 and chat_input == st.session_state['past'][-1])):
    return
  
  # Generate response invoking Azure OpenAI LLM
  if chat_input !=  '':
    output = generate_response(chat_input)
    
    # store the output
    st.session_state['past'].append(chat_input)
    st.session_state['generated'].append(output)
    st.session_state['prompts'].append({"role": "assistant", "content": output})

# Create a 2-column layout. Note: Streamlit columns do not properly render on mobile devices.
# For more information, see https://github.com/streamlit/streamlit/issues/5003
col1, col2 = st.columns([1, 7])

# Display the robot image
with col1:
  st.image(image = os.path.join("images", image_file_name), width = image_width)

# Display the title
with col2:
  st.title(title)

# Create a 3-column layout. Note: Streamlit columns do not properly render on mobile devices.
# For more information, see https://github.com/streamlit/streamlit/issues/5003
col3, col4, col5 = st.columns([7, 1, 1])

# Create text input in column 1
with col3:
  user_input = st.text_input(text_input_label, key = "user", on_change = user_change)

# Create send button in column 2
with col4:
  st.button(label = "Send")

# Create new button in column 3
with col5:
  st.button(label = "New", on_click = new_click)

# Display the chat history in two separate tabs
# - normal: display the chat history as a list of messages using the streamlit_chat message() function 
# - rich: display the chat history as a list of messages using the Streamlit markdown() function
if st.session_state['generated']:
  tab1, tab2 = st.tabs(["normal", "rich"])
  with tab1:
    for i in range(len(st.session_state['generated']) - 1, -1, -1):
      message(st.session_state['past'][i], is_user = True, key = str(i) + '_user', avatar_style = "fun-emoji", seed = "Nala")
      message(st.session_state['generated'][i], key = str(i), avatar_style = "bottts", seed = "Fluffy")
  with tab2:
    for i in range(len(st.session_state['generated']) - 1, -1, -1):
      st.markdown(st.session_state['past'][i])
      st.markdown(st.session_state['generated'][i])
```

The application makes use of an [internal cascading style sheet (CSS)](https://www.javatpoint.com/internal-css) inside an [st.markdown](https://docs.streamlit.io/library/api-reference/text/st.markdown) element to add a unique style to the Streamlit chatbot for mobile and desktop devices. For more information on how to tweak the user interface of a Streamlit application, see [3 Tips to Customize your Streamlit App](https://python.plainenglish.io/three-tips-to-improve-your-streamlit-app-a4c94b4d2b30).

```bash
streamlit run app.py
```

<hr />

## Working with the ChatGPT and GPT-4 models

The `generate_response` function creates and sends the prompt to the [Chat Completion API](https://platform.openai.com/docs/api-reference/chat) of the [ChatGPT model](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/concepts/models#chatgpt-gpt-35-turbo).

```python
def generate_response(prompt):
  try:
    st.session_state['prompts'].append({"role": "user", "content": prompt})

    if openai.api_type == "azure_ad":
      refresh_openai_token()

    completion = openai.ChatCompletion.create(
      engine = engine,
      model = model,
      messages = st.session_state['prompts'],
      temperature = temperature,
    )
    
    message = completion.choices[0].message.content
    return message
  except Exception as e:
    logging.exception(f"Exception in generate_response: {e}")
```

OpenAI trained the ChatGPT and GPT-4 models to accept input formatted as a conversation. The messages parameter takes an array of dictionaries with a conversation organized by role or message: system, user, and assistant. The format of a basic Chat Completion is as follows:

```json
{"role": "system", "content": "Provide some context and/or instructions to the model"},
{"role": "user", "content": "The users messages goes here"},
{"role": "assistant", "content": "The response message goes here."}
```

The `system` role also known as the system message is included at the beginning of the array. This message provides the initial instructions to the model. You can provide various information in the system role including:

- A brief description of the assistant
- Personality traits of the assistant
- Instructions or rules you would like the assistant to follow
- Data or information needed for the model, such as relevant questions from an FAQ
- You can customize the system role for your use case or just include basic instructions.

The `system` role or message is optional, but it's recommended to at least include a basic one to get the best results. The `user` role or message represents an input or inquiry from the user, while the `assistant` message corresponds to the response generated by the GPT API. This dialog exchange aims to simulate a human-like conversation, where the user message initiates the interaction and the assistant message provides a relevant and informative answer. This context helps the chat model generate a more appropriate response later on. The last user message refers to the prompt currently requested.  For more information, see [Learn how to work with the ChatGPT and GPT-4 models](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/how-to/chatgpt?pivots=programming-language-chat-completions).

<hr />

## Application Configuration

Make sure to provide a value for the following environment variables when testing the `app.py` Python app locally, for example in Visual Studio Code. You can eventually define environment variables in a `.env` file in the same folder as the `app.py` file.

- `AZURE_OPENAI_TYPE`: specify `azure` if you want to let the application use the API key to authenticate against OpenAI. In this case, make sure to provide the Key in the `AZURE_OPENAI_KEY` environment variable. If you want to authenticate using an Entra security token, you need to specify `azure_ad` as a value. In this case, don't need to provide any value in the `AZURE_OPENAI_KEY` environment variable.

- `AZURE_OPENAI_BASE`: the URL of your Azure OpenAI resource. If you use the API key to authenticate against OpenAI, you can specify the regional endpoint of your Azure OpenAI Service (e.g., [https://eastus.api.cognitive.microsoft.com/](https://eastus.api.cognitive.microsoft.com/)). If you instead plan to use Entra security tokens for authentication, you need to deploy your Azure OpenAI Service with a subdomain and specify the resource-specific endpoint url (e.g., [https://myopenai.openai.azure.com/](https://myopenai.openai.azure.com/)).

- `AZURE_OPENAI_KEY`: the key of your Azure OpenAI resource.

- `AZURE_OPENAI_DEPLOYMENT`: the name of the ChatGPT deployment used by your Azure OpenAI resource, for example `gpt-35-turbo`.

- `AZURE_OPENAI_MODEL`: the name of the ChatGPT model used by your Azure OpenAI resource, for example `gpt-35-turbo`.

- `TITLE`: the title of the Streamlit app.

- `TEMPERATURE`: controls the "creativity" or "randomness" of the text generated. A high temperature of 0.9 results in more diverse output. A temperature of zero would make the model completely deterministic, always choosing the most likely token from all possible tokens in the model.

- `top-p-sampling` is an alternative to temperature. A top_p set to 0.1 would cause the selection of only the top 10% of the probability mass of the next token, to allow for dynamic vocabulary selection based on context.

- `SYSTEM`: gives the model instructions about how it should behave and any context it should reference when generating a response. Used to describe the assistant's personality.

When deploying the application to Azure Kubernetes Service (AKS) these values are provided in a Kubernetes [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/). For more information, see the next section.

<hr />

## OpenAI Library

In order to use the `openai` library with Microsoft Azure endpoints, you need to set the `api_type`, `api_base` and `api_version` in addition to the `api_key`. The `api_type` must be set to 'azure' and the others correspond to the properties of your endpoint. In addition, the deployment name must be passed as the engine parameter. In order to use OpenAI Key to authenticate to your Azure endpoint, you need to set the `api_type` to `azure` and pass the OpenAI Key to `api_key`.

```python
import openai
openai.api_type = "azure"
openai.api_key = "..."
openai.api_base = "https://example-endpoint.openai.azure.com"
openai.api_version = "2023-05-15"

# create a chat completion
chat_completion = openai.ChatCompletion.create(deployment_id="gpt-3.5-turbo", model="gpt-3.5-turbo", messages=[{"role": "user", "content": "Hello world"}])

# print the completion
print(completion.choices[0].message.content)
```

For a detailed example of how to use fine-tuning and other operations using Azure endpoints, please check out the following Jupyter notebooks:

- [Using Azure completions](https://github.com/openai/openai-cookbook/tree/main/examples/azure/completions.ipynb)
- [Using Azure fine-tuning](https://github.com/openai/openai-cookbook/tree/main/examples/azure/finetuning.ipynb)
- [Using Azure embeddings](https://github.com/openai/openai-cookbook/blob/main/examples/azure/embeddings.ipynb)

In order to use Microsoft Active Directory to authenticate to your Azure endpoint, you need to set the `api_type` to `azure_ad` and pass the acquired credential token to `api_key`. The rest of the parameters need to be set as specified in the previous section.

```python
from azure.identity import DefaultAzureCredential
import openai

# Request credential
default_credential = DefaultAzureCredential()
token = default_credential.get_token("https://cognitiveservices.azure.com/.default")

# Setup parameters
openai.api_type = "azure_ad"
openai.api_key = token.token
openai.api_base = "https://example-endpoint.openai.azure.com/"
openai.api_version = "2023-05-15"

# ...
```

<hr />

## App auth methods

Two different authentication methods in the `magic8ball` chatbot application:

- `API key`: set the `AZURE_OPENAI_TYPE` environment variable to `azure` and the `AZURE_OPENAI_KEY` environment variable to the key of your Azure OpenAI resource. You can use the regional endpoint, such as [https://eastus.api.cognitive.microsoft.com/](https://eastus.api.cognitive.microsoft.com/), in the `AZURE_OPENAI_BASE` environment variable, to connect to the Azure OpenAI resource.

- `Azure Active Directory`: set the `AZURE_OPENAI_TYPE` environment variable to `azure_ad` and use a service principal or managed identity with the [DefaultAzureCredential](https://learn.microsoft.com/en-us/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) object to acquire a security token from Azure Active Directory. For more information on the DefaultAzureCredential in Python, see [Authenticate Python apps to Azure services by using the Azure SDK for Python](https://docs.microsoft.com/en-us/azure/developer/python/azure-sdk-authenticate?tabs=cmd). Make sure to assign the `Cognitive Services User` role to the service principal or managed identity used to authenticate to your Azure OpenAI Service. For more information, see [How to configure Azure OpenAI Service with managed identities](https://learn.microsoft.com/en-us/azure/cognitive-services/openai/how-to/managed-identity). If you want to use Entra integrated security, you need to create a custom subdomain for your Azure OpenAI resource and use the specific endpoint containing the custom domain, such as [https://myopenai.openai.azure.com/](https://myopenai.openai.azure.com/) where myopenai is the custom subdomain. If you specify the regional endpoint, you get an error like the following: `Subdomain does not map to a resource`. Hence, pass the custom domain endpoint in the `AZURE_OPENAI_BASE` environment variable. In this case, you also need to refresh the security token periodically.


<hr />

## Run Deployment Scripts

There are two

   * Terraform
   * AKS
   <br /><br />

If you deployed the Azure infrastructure using the Terraform modules provided with this sample, you only need to deploy the application using the following scripts and YAML templates in the `scripts` folder.

**YAML manifests**

- `configMap.yml`
- `deployment.yml`
- `ingress.yml`
- `service.yml`

**Scripts**

- `09-deploy-app.sh`
- `10-create-ingress.sh`
- `11-configure-dns.sh`

Alternately, to deploy the application in your AKS cluster, you can use the following scripts to configure your environment.

<hr />


<a name="04-create-nginx-ingress-controller.sh"></a>

### 04-create-nginx-ingress-controller.sh

This script installs the `NGINX Ingress Controller` using Helm.

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/04-create-nginx-ingress-controller.sh">
VIEW: 04-create-nginx-ingress-controller.sh</a> 

```bash
#!/bin/bash

# Variables
source ./00-variables.sh

# Use Helm to deploy an NGINX ingress controller
result=$(helm list -n $nginxNamespace | grep $nginxReleaseName | awk '{print $1}')

if [[ -n $result ]]; then
  echo "[$nginxReleaseName] ingress controller already exists in the [$nginxNamespace] namespace"
else
  # Check if the ingress-nginx repository is not already added
  result=$(helm repo list | grep $nginxRepoName | awk '{print $1}')

  if [[ -n $result ]]; then
    echo "[$nginxRepoName] Helm repo already exists"
  else
    # Add the ingress-nginx repository
    echo "Adding [$nginxRepoName] Helm repo..."
    helm repo add $nginxRepoName $nginxRepoUrl
  fi

  # Update your local Helm chart repository cache
  echo 'Updating Helm repos...'
  helm repo update

  # Deploy NGINX ingress controller
  echo "Deploying [$nginxReleaseName] NGINX ingress controller to the [$nginxNamespace] namespace..."
  helm install $nginxReleaseName $nginxRepoName/$nginxChartName \
    --create-namespace \
    --namespace $nginxNamespace \
    --set controller.config.enable-modsecurity=true \
    --set controller.config.enable-owasp-modsecurity-crs=true \
    --set controller.config.modsecurity-snippet=\
'SecRuleEngine On
SecRequestBodyAccess On
SecAuditLog /dev/stdout
SecAuditLogFormat JSON
SecAuditEngine RelevantOnly
SecRule REMOTE_ADDR "@ipMatch 127.0.0.1" "id:87,phase:1,pass,nolog,ctl:ruleEngine=Off"' \
    --set controller.metrics.enabled=true \
    --set controller.metrics.serviceMonitor.enabled=true \
    --set controller.metrics.serviceMonitor.additionalLabels.release="prometheus" \
    --set controller.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.replicaCount=$replicaCount \
    --set defaultBackend.nodeSelector."kubernetes\.io/os"=linux \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path"=/healthz
fi
```

<a name="05-install-cert-manager.sh"></a>

### 05-install-cert-manager.sh

This script installs the `cert-manager` using Helm.

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/05-install-cert-manager.sh">
VIEW: 05-install-cert-manager.sh</a> 

```bash
#/bin/bash

# Variables
source ./00-variables.sh

# Check if the ingress-nginx repository is not already added
result=$(helm repo list | grep $cmRepoName | awk '{print $1}')

if [[ -n $result ]]; then
  echo "[$cmRepoName] Helm repo already exists"
else
  # Add the Jetstack Helm repository
  echo "Adding [$cmRepoName] Helm repo..."
  helm repo add $cmRepoName $cmRepoUrl
fi

# Update your local Helm chart repository cache
echo 'Updating Helm repos...'
helm repo update

# Install cert-manager Helm chart
result=$(helm list -n $cmNamespace | grep $cmReleaseName | awk '{print $1}')

if [[ -n $result ]]; then
  echo "[$cmReleaseName] cert-manager already exists in the $cmNamespace namespace"
else
  # Install the cert-manager Helm chart
  echo "Deploying [$cmReleaseName] cert-manager to the $cmNamespace namespace..."
  helm install $cmReleaseName $cmRepoName/$cmChartName \
    --create-namespace \
    --namespace $cmNamespace \
    --set installCRDs=true \
    --set nodeSelector."kubernetes\.io/os"=linux
fi
```

<a name="06-create-cluster-issuers.sh"></a>

### 06-create-cluster-issuer.sh

This script creates a cluster issuer for the `NGINX Ingress Controller` based on the `Let's Encrypt` ACME certificate issuer.

```bash
#/bin/bash

# Variables
source ./00-variables.sh

# Check if the cluster issuer already exists
result=$(kubectl get ClusterIssuer -o json | jq -r '.items[].metadata.name | select(. == "'$clusterIssuerName'")')

if [[ -n $result ]]; then
  echo "[$clusterIssuerName] cluster issuer already exists"
  exit
else
  # Create the cluster issuer
  echo "[$clusterIssuerName] cluster issuer does not exist"
  echo "Creating [$clusterIssuerName] cluster issuer..."
  cat $clusterIssuerTemplate |
    yq "(.spec.acme.email)|="\""$email"\" |
    kubectl apply -f -
fi
```

<a name="07-create-workload-managed-identity.sh"></a>

### 07-create-workload-managed-identity.sh

This script creates the managed identity used by the `magic8ball`chatbot and assigns it the `Cognitive Services User` role on the Azure OpenAI Service.

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/07-create-workload-managed-identity.sh">
VIEW: 07-create-workload-managed-identity.sh</a> 

   * $aksResourceGroupName

```bash
#!/bin/bash

# Variables
source ./00-variables.sh

# Check if the user-assigned managed identity already exists
echo "Checking if [$managedIdentityName] user-assigned managed identity actually exists in the [$aksResourceGroupName] resource group..."

az identity show \
  --name $managedIdentityName \
  --resource-group $aksResourceGroupName &>/dev/null

if [[ $? != 0 ]]; then
  echo "No [$managedIdentityName] user-assigned managed identity actually exists in the [$aksResourceGroupName] resource group"
  echo "Creating [$managedIdentityName] user-assigned managed identity in the [$aksResourceGroupName] resource group..."

  # Create the user-assigned managed identity
  az identity create \
    --name $managedIdentityName \
    --resource-group $aksResourceGroupName \
    --location $location \
    --subscription $subscriptionId 1>/dev/null

  if [[ $? == 0 ]]; then
    echo "[$managedIdentityName] user-assigned managed identity successfully created in the [$aksResourceGroupName] resource group"
  else
    echo "Failed to create [$managedIdentityName] user-assigned managed identity in the [$aksResourceGroupName] resource group"
    exit
  fi
else
  echo "[$managedIdentityName] user-assigned managed identity already exists in the [$aksResourceGroupName] resource group"
fi

# Retrieve the clientId of the user-assigned managed identity
echo "Retrieving clientId for [$managedIdentityName] managed identity..."
clientId=$(az identity show \
  --name $managedIdentityName \
  --resource-group $aksResourceGroupName \
  --query clientId \
  --output tsv)

if [[ -n $clientId ]]; then
  echo "[$clientId] clientId  for the [$managedIdentityName] managed identity successfully retrieved"
else
  echo "Failed to retrieve clientId for the [$managedIdentityName] managed identity"
  exit
fi

# Retrieve the principalId of the user-assigned managed identity
echo "Retrieving principalId for [$managedIdentityName] managed identity..."
principalId=$(az identity show \
  --name $managedIdentityName \
  --resource-group $aksResourceGroupName \
  --query principalId \
  --output tsv)

if [[ -n $principalId ]]; then
  echo "[$principalId] principalId  for the [$managedIdentityName] managed identity successfully retrieved"
else
  echo "Failed to retrieve principalId for the [$managedIdentityName] managed identity"
  exit
fi

# Get the resource id of the Azure OpenAI resource
openAiId=$(az cognitiveservices account show \
  --name $openAiName \
  --resource-group $openAiResourceGroupName \
  --query id \
  --output tsv)

if [[ -n $openAiId ]]; then
  echo "Resource id for the [$openAiName] Azure OpenAI resource successfully retrieved"
else
  echo "Failed to the resource id for the [$openAiName] Azure OpenAI resource"
  exit -1
fi

# Assign the Cognitive Services User role on the Azure OpenAI resource to the managed identity
role="Cognitive Services User"
echo "Checking if the [$managedIdentityName] managed identity has been assigned to [$role] role with [$openAiName] Azure OpenAI resource as a scope..."
current=$(az role assignment list \
  --assignee $principalId \
  --scope $openAiId \
  --query "[?roleDefinitionName=='$role'].roleDefinitionName" \
  --output tsv 2>/dev/null)

if [[ $current == $role ]]; then
  echo "[$managedIdentityName] managed identity is already assigned to the ["$current"] role with [$openAiName] Azure OpenAI resource as a scope"
else
  echo "[$managedIdentityName] managed identity is not assigned to the [$role] role with [$openAiName] Azure OpenAI resource as a scope"
  echo "Assigning the [$role] role to the [$managedIdentityName] managed identity with [$openAiName] Azure OpenAI resource as a scope..."

  az role assignment create \
    --assignee $principalId \
    --role "$role" \
    --scope $openAiId 1>/dev/null

  if [[ $? == 0 ]]; then
    echo "[$managedIdentityName] managed identity successfully assigned to the [$role] role with [$openAiName] Azure OpenAI resource as a scope"
  else
    echo "Failed to assign the [$managedIdentityName] managed identity to the [$role] role with [$openAiName] Azure OpenAI resource as a scope"
    exit
  fi
fi
```

<a name="08-create-service-account.sh"></a>

### 08-create-service-account.sh

This script creates the namespace and service account for the `magic8ball` chatbot and federate the service account with the user-defined managed identity created in the previous step.

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/08-create-service-account.sh">
VIEW: 08-create-service-account.sh</a> 
   * uses kubectl to verify whether the $namespace it uses exists
   * Retrieve the resource id of the user-assigned managed identity
   * Create the service account by name: $serviceAccountName
   * Create credentials under $federatedIdentityName


```bash
#!/bin/bash

# Variables for the user-assigned managed identity
source ./00-variables.sh

# Check if the namespace already exists
result=$(kubectl get namespace -o 'jsonpath={.items[?(@.metadata.name=="'$namespace'")].metadata.name'})

if [[ -n $result ]]; then
  echo "[$namespace] namespace already exists"
else
  # Create the namespace for your ingress resources
  echo "[$namespace] namespace does not exist"
  echo "Creating [$namespace] namespace..."
  kubectl create namespace $namespace
fi

# Check if the service account already exists
result=$(kubectl get sa -n $namespace -o 'jsonpath={.items[?(@.metadata.name=="'$serviceAccountName'")].metadata.name'})

if [[ -n $result ]]; then
  echo "[$serviceAccountName] service account already exists"
else
  # Retrieve the resource id of the user-assigned managed identity
  echo "Retrieving clientId for [$managedIdentityName] managed identity..."
  managedIdentityClientId=$(az identity show \
    --name $managedIdentityName \
    --resource-group $aksResourceGroupName \
    --query clientId \
    --output tsv)

  if [[ -n $managedIdentityClientId ]]; then
    echo "[$managedIdentityClientId] clientId  for the [$managedIdentityName] managed identity successfully retrieved"
  else
    echo "Failed to retrieve clientId for the [$managedIdentityName] managed identity"
    exit
  fi

  # Create the service account
  echo "[$serviceAccountName] service account does not exist"
  echo "Creating [$serviceAccountName] service account..."
  cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    azure.workload.identity/client-id: $managedIdentityClientId
    azure.workload.identity/tenant-id: $tenantId
  labels:
    azure.workload.identity/use: "true"
  name: $serviceAccountName
  namespace: $namespace
EOF
fi

# Show service account YAML manifest
echo "Service Account YAML manifest"
echo "-----------------------------"
kubectl get sa $serviceAccountName -n $namespace -o yaml

# Check if the federated identity credential already exists
echo "Checking if [$federatedIdentityName] federated identity credential actually exists in the [$aksResourceGroupName] resource group..."

az identity federated-credential show \
  --name $federatedIdentityName \
  --resource-group $aksResourceGroupName \
  --identity-name $managedIdentityName &>/dev/null

if [[ $? != 0 ]]; then
  echo "No [$federatedIdentityName] federated identity credential actually exists in the [$aksResourceGroupName] resource group"

  # Get the OIDC Issuer URL
  aksOidcIssuerUrl="$(az aks show \
    --only-show-errors \
    --name $aksClusterName \
    --resource-group $aksResourceGroupName \
    --query oidcIssuerProfile.issuerUrl \
    --output tsv)"

  # Show OIDC Issuer URL
  if [[ -n $aksOidcIssuerUrl ]]; then
    echo "The OIDC Issuer URL of the $aksClusterName cluster is $aksOidcIssuerUrl"
  fi

  echo "Creating [$federatedIdentityName] federated identity credential in the [$aksResourceGroupName] resource group..."

  # Establish the federated identity credential between the managed identity, the service account issuer, and the subject.
  az identity federated-credential create \
    --name $federatedIdentityName \
    --identity-name $managedIdentityName \
    --resource-group $aksResourceGroupName \
    --issuer $aksOidcIssuerUrl \
    --subject system:serviceaccount:$namespace:$serviceAccountName

  if [[ $? == 0 ]]; then
    echo "[$federatedIdentityName] federated identity credential successfully created in the [$aksResourceGroupName] resource group"
  else
    echo "Failed to create [$federatedIdentityName] federated identity credential in the [$aksResourceGroupName] resource group"
    exit
  fi
else
  echo "[$federatedIdentityName] federated identity credential already exists in the [$aksResourceGroupName] resource group"
fi
```

<a name="#09-deploy-app.sh"></a>

### 09-deploy-app.sh

This script creates the Kubernetes config map, deployment, and service used by the `magic8ball` chatbot.

```bash
#!/bin/bash

# Variables
source ./00-variables.sh

# Attach ACR to AKS cluster
if [[ $attachAcr == true ]]; then
  echo "Attaching ACR $acrName to AKS cluster $aksClusterName..."
  az aks update \
    --name $aksClusterName \
    --resource-group $aksResourceGroupName \
    --attach-acr $acrName
fi

# Check if namespace exists in the cluster
result=$(kubectl get namespace -o jsonpath="{.items[?(@.metadata.name=='$namespace')].metadata.name}")

if [[ -n $result ]]; then
  echo "$namespace namespace already exists in the cluster"
else
  echo "$namespace namespace does not exist in the cluster"
  echo "creating $namespace namespace in the cluster..."
  kubectl create namespace $namespace
fi

# Create config map
cat $configMapTemplate |
    yq "(.data.TITLE)|="\""$title"\" |
    yq "(.data.LABEL)|="\""$label"\" |
    yq "(.data.TEMPERATURE)|="\""$temperature"\" |
    yq "(.data.IMAGE_WIDTH)|="\""$imageWidth"\" |
    yq "(.data.AZURE_OPENAI_TYPE)|="\""$openAiType"\" |
    yq "(.data.AZURE_OPENAI_BASE)|="\""$openAiBase"\" |
    yq "(.data.AZURE_OPENAI_MODEL)|="\""$openAiModel"\" |
    yq "(.data.AZURE_OPENAI_DEPLOYMENT)|="\""$openAiDeployment"\" |
    kubectl apply -n $namespace -f -

# Create deployment
cat $deploymentTemplate |
    yq "(.spec.template.spec.containers[0].image)|="\""$image"\" |
    yq "(.spec.template.spec.containers[0].imagePullPolicy)|="\""$imagePullPolicy"\" |
    yq "(.spec.template.spec.serviceAccountName)|="\""$serviceAccountName"\" |
    kubectl apply -n $namespace -f -

# Create deployment
kubectl apply -f $serviceTemplate -n $namespace
```

### 10-create-ingress.sh

This script creates the ingress object to expose the service via the `NGINX Ingress Controller`

```bash
#/bin/bash

# Variables
source ./00-variables.sh

# Create the ingress
echo "[$ingressName] ingress does not exist"
echo "Creating [$ingressName] ingress..."
cat $ingressTemplate |
  yq "(.spec.tls[0].hosts[0])|="\""$host"\" |
  yq "(.spec.rules[0].host)|="\""$host"\" |
  kubectl apply -n $namespace -f -
```

### 11-configure-dns.sh

This script creates an A record in the Azure DNS Zone to expose the application via a given subdomain (e.g., [https://magic8ball.example.com](https://magic8ball.example.com))

```bash
# Variables
source ./00-variables.sh

# Retrieve the public IP address from the ingress
echo "Retrieving the external IP address from the [$ingressName] ingress..."
publicIpAddress=$(kubectl get ingress $ingressName -n $namespace -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

if [ -n $publicIpAddress ]; then
  echo "[$publicIpAddress] external IP address of the application gateway ingress controller successfully retrieved from the [$ingressName] ingress"
else
  echo "Failed to retrieve the external IP address of the application gateway ingress controller from the [$ingressName] ingress"
  exit
fi

# Check if an A record for todolist subdomain exists in the DNS Zone
echo "Retrieving the A record for the [$subdomain] subdomain from the [$dnsZoneName] DNS zone..."
ipv4Address=$(az network dns record-set a list \
  --zone-name $dnsZoneName \
  --resource-group $dnsZoneResourceGroupName \
  --query "[?name=='$subdomain'].arecords[].ipv4Address" \
  --output tsv)

if [[ -n $ipv4Address ]]; then
  echo "An A record already exists in [$dnsZoneName] DNS zone for the [$subdomain] subdomain with [$ipv4Address] IP address"

  if [[ $ipv4Address == $publicIpAddress ]]; then
    echo "The [$ipv4Address] ip address of the existing A record is equal to the ip address of the [$ingressName] ingress"
    echo "No additional step is required"
    exit
  else
    echo "The [$ipv4Address] ip address of the existing A record is different than the ip address of the [$ingressName] ingress"
  fi

  # Retrieving name of the record set relative to the zone
  echo "Retrieving the name of the record set relative to the [$dnsZoneName] zone..."

  recordSetName=$(az network dns record-set a list \
    --zone-name $dnsZoneName \
    --resource-group $dnsZoneResourceGroupName \
    --query "[?name=='$subdomain'].name" \
    --output name 2>/dev/null)

  if [[ -n $recordSetName ]]; then
    "[$recordSetName] record set name successfully retrieved"
  else
    "Failed to retrieve the name of the record set relative to the [$dnsZoneName] zone"
    exit
  fi

  # Remove the a record
  echo "Removing the A record from the record set relative to the [$dnsZoneName] zone..."

  az network dns record-set a remove-record \
    --ipv4-address $ipv4Address \
    --record-set-name $recordSetName \
    --zone-name $dnsZoneName \
    --resource-group $dnsZoneResourceGroupName

  if [[ $? == 0 ]]; then
    echo "[$ipv4Address] ip address successfully removed from the [$recordSetName] record set"
  else
    echo "Failed to remove the [$ipv4Address] ip address from the [$recordSetName] record set"
    exit
  fi
fi

# Create the a record
echo "Creating an A record in [$dnsZoneName] DNS zone for the [$subdomain] subdomain with [$publicIpAddress] IP address..."
az network dns record-set a add-record \
  --zone-name $dnsZoneName \
  --resource-group $dnsZoneResourceGroupName \
  --record-set-name $subdomain \
  --ipv4-address $publicIpAddress 1>/dev/null

if [[ $? == 0 ]]; then
  echo "A record for the [$subdomain] subdomain with [$publicIpAddress] IP address successfully created in [$dnsZoneName] DNS zone"
else
  echo "Failed to create an A record for the $subdomain subdomain with [$publicIpAddress] IP address in [$dnsZoneName] DNS zone"
fi
```

The scripts used to deploy the YAML template use the [yq](https://github.com/mikefarah/yq) tool to customize the manifests with the value of the variables defined in the `00-variables.sh` file. This tool is a lightweight and portable command-line YAML, JSON and XML processor that uses [jq](https://jqlang.github.io/jq/) like syntax but works with YAML files as well as json, xml, properties, csv and tsv. It doesn't yet support everything jq does - but it does support the most common operations and functions, and more is being added continuously.

<hr />

### YAML manifests

Below you can read the YAML manifests used to deploy the `magic8ball` chatbot to AKS.



## configmap.yml

The `configmap.yml` defines a value for the environment variables passed to the application container. The configmap does not define any environment variable for the OpenAI key as the container.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: magic8ball-configmap
data:
  TITLE: "Magic 8 Ball"
  LABEL: "Pose your question and cross your fingers!"
  TEMPERATURE: "0.9"
  IMAGE_WIDTH: "80"
  AZURE_OPENAI_TYPE: azure_ad
  AZURE_OPENAI_BASE: https://myopenai.openai.azure.com/
  AZURE_OPENAI_KEY: ""
  AZURE_OPENAI_MODEL: gpt-35-turbo
  AZURE_OPENAI_DEPLOYMENT: magic8ballGPT
```

These are the parameters defined by the configmap:

- `AZURE_OPENAI_TYPE`: specify `azure` if you want to let the application use the API key to authenticate against OpenAI. In this case, make sure to provide the Key in the `AZURE_OPENAI_KEY` environment variable. If you want to authenticate using an Entra security token, you need to specify `azure_ad` as a value. In this case, don't need to provide any value in the `AZURE_OPENAI_KEY` environment variable.

- `AZURE_OPENAI_BASE`: the URL of your Azure OpenAI resource. If you use the API key to authenticate against OpenAI, you can specify the regional endpoint of your Azure OpenAI Service (e.g., [https://eastus.api.cognitive.microsoft.com/](https://eastus.api.cognitive.microsoft.com/)). If you instead plan to use Entra security tokens for authentication, you need to deploy your Azure OpenAI Service with a subdomain and specify the resource-specific endpoint url (e.g., [https://myopenai.openai.azure.com/](https://myopenai.openai.azure.com/)).

- `AZURE_OPENAI_KEY`: the key of your Azure OpenAI resource. If you set `AZURE_OPENAI_TYPE` to `azure_ad` you can leave this parameter empty.

- `AZURE_OPENAI_DEPLOYMENT`: the name of the ChatGPT deployment used by your Azure OpenAI resource, for example `gpt-35-turbo`.

- `AZURE_OPENAI_MODEL`: the name of the ChatGPT model used by your Azure OpenAI resource, for example `gpt-35-turbo`.

- `TITLE`: the title of the Streamlit app.

- `TEMPERATURE`: the temperature used by the OpenAI API to generate the response.

- `SYSTEM`: give the model instructions about how it should behave and any context it should reference when generating a response. Used to describe the assistant's personality.



### chat-deployment.yml

The `deployment.yml` manifest is used create a Kubernetes [deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) that defines the application pods to create. [azure.workload.identity/use](https://learn.microsoft.com/en-us/azure/aks/workload-identity-overview#pod-labels) label is required in the pod template spec. Only pods with this label will be mutated by the azure-workload-identity mutating admission webhook to inject the Azure specific environment variables and the projected service account token volume.

<a target="_blank" href="https://github.com/bomonike/azure-chatgbt/blob/main/scripts/chat-deployment.yml">VIEW: chat-deployment.yml</a>

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: magic8ball
  labels:
    app: magic8ball
spec:
  replicas: 3
  selector:
    matchLabels:
      app: magic8ball
      azure.workload.identity/use: "true"
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  minReadySeconds: 5
  template:
    metadata:
      labels:
        app: magic8ball
        azure.workload.identity/use: "true"
        prometheus.io/scrape: "true"
    spec:
      serviceAccountName: magic8ball-sa
      topologySpreadConstraints:
      - maxSkew: 1
        topologyKey: topology.kubernetes.io/zone
        whenUnsatisfiable: DoNotSchedule
        labelSelector:
          matchLabels:
            app: magic8ball
      - maxSkew: 1
        topologyKey: kubernetes.io/hostname
        whenUnsatisfiable: DoNotSchedule
        labelSelector:
          matchLabels:
            app: magic8ball
      nodeSelector:
        "kubernetes.io/os": linux
      containers:
      - name: magic8ball
        image: paolosalvatori.azurecr.io/magic8ball:v1
        imagePullPolicy: Always
        resources:
          requests:
            memory: "128Mi"
            cpu: "250m"
          limits:
            memory: "256Mi"
            cpu: "500m"
        ports:
        - containerPort: 8501
        livenessProbe:
          httpGet:
            path: /
            port: 8501
          failureThreshold: 1
          initialDelaySeconds: 60
          periodSeconds: 30
          timeoutSeconds: 5
        readinessProbe:
          httpGet:
            path: /
            port: 8501
          failureThreshold: 1
          initialDelaySeconds: 60
          periodSeconds: 30
          timeoutSeconds: 5
        startupProbe:
          httpGet:
            path: /
            port: 8501
          failureThreshold: 1
          initialDelaySeconds: 60
          periodSeconds: 30
          timeoutSeconds: 5
        env:
        - name: TITLE
          valueFrom:
            configMapKeyRef:
                name: magic8ball-configmap
                key: TITLE
        - name: IMAGE_WIDTH
          valueFrom:
            configMapKeyRef:
                name: magic8ball-configmap
                key: IMAGE_WIDTH
        - name: LABEL
          valueFrom:
            configMapKeyRef:
                name: magic8ball-configmap
                key: LABEL
        - name: TEMPERATURE
          valueFrom:
            configMapKeyRef:
                name: magic8ball-configmap
                key: TEMPERATURE
        - name: AZURE_OPENAI_TYPE
          valueFrom:
            configMapKeyRef:
                name: magic8ball-configmap
                key: AZURE_OPENAI_TYPE
        - name: AZURE_OPENAI_BASE
          valueFrom:
            configMapKeyRef:
                name: magic8ball-configmap
                key: AZURE_OPENAI_BASE
        - name: AZURE_OPENAI_KEY
          valueFrom:
            configMapKeyRef:
                name: magic8ball-configmap
                key: AZURE_OPENAI_KEY
        - name: AZURE_OPENAI_MODEL
          valueFrom:
            configMapKeyRef:
                name: magic8ball-configmap
                key: AZURE_OPENAI_MODEL
        - name: AZURE_OPENAI_DEPLOYMENT
          valueFrom:
            configMapKeyRef:
                name: magic8ball-configmap
                key: AZURE_OPENAI_DEPLOYMENT
```


### service.yml

The application is exposed using a `ClusterIP` Kubernetes [service](https://kubernetes.io/docs/concepts/services-networking/service/).


```yaml
apiVersion: v1
kind: Service
metadata:
  name: magic8ball
  labels:
    app: magic8ball
spec:
  type: ClusterIP
  ports:
  - protocol: TCP
    port: 8501
  selector:
    app: magic8ball
```

## ingress.yml

The `ingress.yml` manifest defines a Kubernetes [ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/) object used to expose the service via the [NGINX Ingress Controller](https://docs.nginx.com/nginx-ingress-controller/).

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: magic8ball-ingress
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-nginx
    cert-manager.io/acme-challenge-type: http01 
    nginx.ingress.kubernetes.io/proxy-connect-timeout: "360"
    nginx.ingress.kubernetes.io/proxy-send-timeout: "360"
    nginx.ingress.kubernetes.io/proxy-read-timeout: "360"
    nginx.ingress.kubernetes.io/proxy-next-upstream-timeout: "360"
    nginx.ingress.kubernetes.io/configuration-snippet: |
      more_set_headers "X-Frame-Options: SAMEORIGIN";
spec:
  ingressClassName: nginx
  tls:
  - hosts:
    - magic8ball.contoso.com
    secretName: tls-secret
  rules:
  - host: magic8ball.contoso.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: magic8ball
            port:
              number: 8501
```

The ingress object defines the following annotations:

- [cert-manager.io/cluster-issuer](https://cert-manager.io/docs/usage/ingress/#supported-annotations): specifies the name of a cert-manager.io ClusterIssuer to acquire the certificate required for this Ingress. It does not matter which namespace your Ingress resides, as ClusterIssuers are non-namespaced resources. In this sample, the cert-manager is instructed to use the `letsencrypt-nginx` ClusterIssuer that you can create using the `06-create-cluster-issuer.sh` script.

- [cert-manager.io/acme-challenge-type](https://cert-manager.io/docs/usage/ingress/#supported-annotations): specifies the challend type.

- [nginx.ingress.kubernetes.io/proxy-connect-timeout](https://github.com/kubernetes/ingress-nginx/blob/main/docs/user-guide/nginx-configuration/annotations.md#custom-timeouts): specifies the connection timeout in seconds.

- [nginx.ingress.kubernetes.io/proxy-send-timeout](https://github.com/kubernetes/ingress-nginx/blob/main/docs/user-guide/nginx-configuration/annotations.md#custom-timeouts): specifies the send timeout in seconds.

- [nginx.ingress.kubernetes.io/proxy-read-timeout](https://github.com/kubernetes/ingress-nginx/blob/main/docs/user-guide/nginx-configuration/annotations.md#custom-timeouts): specifies the read timeout in seconds.

- [nginx.ingress.kubernetes.io/proxy-next-upstream-timeout](https://github.com/kubernetes/ingress-nginx/blob/main/docs/user-guide/nginx-configuration/annotations.md#custom-timeouts): specifies the next upstream timeout in seconds.

- [nginx.ingress.kubernetes.io/configuration-snippet](https://github.com/kubernetes/ingress-nginx/blob/main/docs/user-guide/nginx-configuration/annotations.md#configuration-snippet): allows additional configuration to the NGINX location.



<hr />

<a name="F."></a>

## F. Clean up resources

When you no longer need the resources you created, delete Azure's resource group.

1. Since Terraform was used to create the resources, also use terraform to destroy them:

   <pre># Destroy Resources and update .terraform state files:
terraform destroy -auto-approve
&nbsp;
# Delete Files
rm -rf .terraform*
rm -rf terraform.tfstate*
   </pre>

   The Terraform provider will issue these Azure CLI commands so you don't have to:

   ```azurecli
az group delete --name "${aksResourceGroupName}"
   ```

   Alternately, 

   ```azurepowershell
Remove-AzResourceGroup -Name "${aksResourceGroupName}"
   ```

<hr />

<a name="G."></a>

## G. Roadmap for more

1. Typo DNDS
   https://github.com/paolosalvatori/aks-openai-chainlit-terraform/pull/2

1. terraform/register-preview-features.sh -> needs --allow-preview true

   az extension update --name aks-preview  --allow-preview true

1. install-nginx-via-helm-and-create-sa.sh mentioned in README -  does file exist?
1. GitHub Actions ?
1. Diagram gradual reveal in PowerPoint
1. To run in macOS, use brew as well as apt for Linux within 10-configure-dns.sh
1. minicube3 & conda commands instead of pip env ?

1. dev, test, stg, prod env. to initiate run
1. customized chats on the licensed Open.ai App store

<hr />


WARNING: These are files in the script folder not yet documented in this article:

* <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/02-run-docker-container.sh">
VIEW: 02-run-docker-container.sh</a> 

* <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/03-push-docker-image.sh">
VIEW: 03-push-docker-image.sh</a> 

* <a target="_blank" href="https://github.com/bomonike/azure-chatgbt/tree/main/scripts/06-create-cluster-issuers.sh">
VIEW: 06-create-cluster-issuers.sh</a> 


<hr />

<a name="References"></a>

## References

<a target="_blank" href="https://github.com/stacksimplify/azure-aks-kubernetes-masterclass/tree/master/24-Azure-AKS-Terraform">https://github.com/stacksimplify/azure-aks-kubernetes-masterclass</a> explained in the <a target="_blank" href="https://www.udemy.com/course/azure-kubernetes-service-with-azure-devops-and-terraform/">Udemy class "Azure Kubernetes Service with Azure DevOps and Terraform"</a> by Kalyan Reddy Daida

<a target="_blank" href="https://learning.oreilly.com/library/view/-/9781484273289/">
Deep-Dive Terraform on Azure: Automated Delivery and Deployment of Azure Solutions</a>
Apress September 2021
By Ritesh Modi

By Ashley Davis
   * <a target="_blank" href="https://learning.oreilly.com/library/view/-/9781617297212/">Bootstrapping Microservices with Docker, Kubernetes, and Terraform</a> from Manning Publications February 2021

   * <a target="_blank" href="https://learning.oreilly.com/videos/-/10000MNLV202112/">Infrastructure as Code: Using Terraform to Deploy Kubernetes and Microservices</a> from Manning Publications  October 2020

https://learning.oreilly.com/videos/-/9780138230258/
Automating Kubernetes with GitOps
By Sander van Vugt
from Pearson April 2023
7h 40m

https://learning.oreilly.com/library/view/-/9781098142155/
Kubernetes Best Practices, 2nd Edition
By Brendan Burns, Eddie Villalba, Dave Strebel and 1 more
from O'Reilly Media, Inc. October 2023
322 pages

https://learning.oreilly.com/library/view/-/9781803248004/
Observability with Grafana
By Rob Chapman and Peter Holmes
from Packt Publishing January 2024
356 pages

https://sandervandevelde.wordpress.com/2023/12/05/microsoft-fabric-real-time-analytics-exploration-managed-grafana-integration/


END
