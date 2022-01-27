# Ghost-Azure 
## Why Ghost-Azure?
Straight out of the box, the current 1.x and 2.x versions of Ghost aren't compatible with the Azure App Service. Ghost-Azure resolves this by providing a production-ready template which can be hosted directly on Azure App Service. In the background, an Azure Function ([Ghost-Release-Uploader](https://github.com/YannickRe/Ghost-Release-Uploader)) makes sure that this repository stays up-to-date with the latest releases of Ghost.
This template is a proof of concept that builds on forked from YannickRe/Ghost-Azure (node code adapted for a single webapp), and tries to make it into a load balanced regional outage tollerent architecture.

### One-click deploy
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdashanan13%2FGhost-Azure-public%2Ftesting%2Fazuredeploy.json)

### Sample Architecture
![Architecture](https://github.com/dashanan13/Ghost-Azure-public/blob/master/templates/architecture.png?raw=true)

**The solution consists of the following parts:**
1. Azure Webapps hosted in 2 different regions, such that if one goes down the other one can remain active.
2. Azure Frontdoor hosted routing traffic to each webapp and load balancing, this will be handy if a web app goes down (regional outage?).
3. Azure Storage account hosted and configured as backup location for webapps in seperate containers periodically.
4. Azure Insights connected to Azure Webapps to collect activity data from Azure Webapps, this can be used ot troubleshoot webapps.
5. Azure Network to restrict access to Storage account and keep the backup access limited.

**The architecture is**
1. Secure: As the access is streamlined , 
- Webapps can only be accessed via Fontdoor and not individually
- WAF policies monitor traffic and keep acting on the threats classified under OWASP.
- Backup can only be access by a device deployed ot Virtual network in Azure Resource group.

2. Scalable: 
- It is possible to add more such regional Webapps to improve latency in other regions for dynamic content
- Each Webapp is deployed with metric based autoscalling, such that if load increases the infra responds by providing more capability.

3. Developer friendly:
- Each webapp provides Slots that can and have been configured to various branches of the repository making it easier to develop and test for different environments.

## Installation method
In any case I suggest forking my repository into your own, this to avoid changes I make to my repository to negatively impact your installation.

This installation contains two sections
1.  Azure deployment for establishing resources along with the compiled code via azuredeploy.json and a convienent One-click deploy button.
    It will deploy 2 sets of (Azure webapp, application insight for the webapp and a plan for the webapp) and both those webapps will be fronted by Azure Front door and a WAF

2.  The other part is a pipeline to keep the Webapps updated with latest code via a pipeline file azure-pipelines.yml (**Work in progress**)
    This is a Azure pipelines file that can be used with Azure Devops.
    Azure devops should have a connection to the subscription being deployed, pipeline will needs the following variables
    - **AzureSubscription**: Name of the connection that was established between Azure devops and azure subscription [How?](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml)
    - **RESOURCEGROUP**: Target resource group name
    - **WEBAPP1**:   Name of first Webapp
    - **UrlProduction1**: URL of first Webapp
    - **UrlStaging1**:   Name of first Webapp slot named "staging"
    - **WEBAPP2**:   Name of Second Webapp
    - **UrlProduction2**: URL of first Webapp
    - **UrlStaging2**:   Name of first Webapp slot named "staging"
    - **WEBJOBNAME**: 'redeploy'
    - **SlotNameStaging**:   'staging'

Note: The website starts to warmup only when it is accessed and it takes a few minutes to warm up.

### Azure App Service Deployment Center
More info on [Microsoft Docs](https://docs.microsoft.com/en-us/azure/app-service/deploy-continuous-deployment#deploy-continuously-from-github)
