# K8S on Azure with AKS-Engine - unmanaged cluster

## Overview:

This demo will help you to deploy a simple **unmanaged** K8S Cluster on Azure. You can either use Azure *Cloud Shell* or your **system** (windows/Linux) as your development and deployment machine. The 'Steps' section below was not tested end to end with Cloud shell.

## Pre-requisites:

1. You already have an active Azure Subscription
2. You have a Github account and you perform basic operations
3. You have basic understanding of git operations.
4. You have AKS-ENGINE, Kubectl and Azure CLI installed on your system. 
To run aks-engine, download and install aks-engine. Follow instruction from here: https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md#install


## Steps:

1. Create a project folder in your system
2. Login to your github account and fork this repo: https://github.com/Bapic/CreateAKSonAzurewithAKSEngine.git 
3. Download the kubernetes.json file from the repo to your project folder
4. Login to Azure portal and copy your subscription ID
5. Launch Visual Studio code or bash terminal or powershell as an admin
6. Run the below command to ensure you have aks-engine installed fine

        aks-engine
    
7.  Declare variables

        $subscriptionId='your subscription id'
        $location='eastus'
        $resourceGroupName='k8sresgroup'
        $dnsPrefix=$resourceGroupName
        az login
        az account set --subscription $subscriptionId

8. Create a service principal in your subscription capture the properties of the output of spn-id and spn-password

	az ad sp create-for-rbac --skip-assignment
	
	NOTE: use - az ad sp delete --id 'app id'  to delete the spn later
	
	$spnAppId="your spn id"

	$spnAppPassword="your spn password"

	az role assignment create --role Contributor --assignee $spnAppId --scope /subscriptions/$subscriptionId

9. Set the apiModel for your K8S deployment, derived from step no. 3. above

        $apimodel = "path to your \Kubernetes.json" 

10. Run the below command to create an unmanaged cluster on Azure

        aks-engine deploy --subscription-id $subscriptionId --resource-group $resourceGroupName --client-id $spnAppId  --client-secret $spnAppPassword  --dns-prefix $dnsPrefix --location $location --api-model $apimodel --force-overwrite

11. Verify that the cluster was created using Azure portal
12. To use kubectl to manage the cluster, set KUBECONFIG to the config file
(AKS-Engine creates this file). For example: 'C:\Windows\System32\_output\resource group name\kubeconfig\kubeconfig.eastus.json' 

        $env:KUBECONFIG = <absolute file location>
	e.g., $env:KUBECONFIG="C:\Users\user name\_output\k8sresgroup\kubeconfig\kubeconfig.eastus.json"
    
            kubectl cluster-info
            kubectl get pods
            kubectl get svc
            kubectl get nodes