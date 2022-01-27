[![Build Status](https://dev.azure.com/u51282/ghost-azure/_apis/build/status/dashanan13.Ghost-Azure?branchName=master)](https://dev.azure.com/u51282/ghost-azure/_build/latest?definitionId=1&branchName=master)

# Ghost-Azure 
## Why Ghost-Azure?
Straight out of the box, the current 1.x and 2.x versions of Ghost aren't compatible with the Azure App Service. Ghost-Azure resolves this by providing a production-ready template which can be hosted directly on Azure App Service. In the background, an Azure Function ([Ghost-Release-Uploader](https://github.com/YannickRe/Ghost-Release-Uploader)) makes sure that this repository stays up-to-date with the latest releases of Ghost.
This template is a proof of concept that builds on forked from YannickRe/Ghost-Azure (node code adapted for a single webapp), and tries to make it into a load balanced regional outage tollerent architecture.

### Sample Architecture
![Architecture](https://github.com/dashanan13/Ghost-Azure-public/blob/master/templates/environment-diagram.png?raw=true)

### One-click deploy (testing)
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdashanan13%2FGhost-Azure-public%2Ftesting%2Fazuredeploy.json)

## Installation method
In any case I suggest forking my repository into your own, this to avoid changes I make to my repository to negatively impact your installation.

This installation contains two sections
1.  Azure deployment for establishing resources along with the compiled code via azuredeploy.json and a convienent One-click deploy button.
    It will deploy 2 sets of (Azure webapp, application insight for the webapp and a plan for the webapp) and both those webapps will be fronted by Azure Front door and a WAF

2.  The other part is a pipeline to keep the Webapps updated with latest code via a pipeline file azure-pipelines.yml
    This is a Azure pipelines file that can be used with Azure Devops.
    Azure devops should have a connection to the subscription being deployed, pipeline will needs the following variables
    - **AzureSubscription**: Name of the connection that was established between Azure devops and azure subscription [How?](https://docs.microsoft.com/en-us/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml)
    - **RESOURCEGROUP**: RG that you want ot deploy to
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
