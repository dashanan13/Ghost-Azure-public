[![Build Status](https://dev.azure.com/u51282/ghost-azure/_apis/build/status/dashanan13.Ghost-Azure?branchName=master)](https://dev.azure.com/u51282/ghost-azure/_build/latest?definitionId=1&branchName=master)

# Ghost-Azure 
## Why Ghost-Azure?
Straight out of the box, the current 1.x and 2.x versions of Ghost aren't compatible with the Azure App Service. Ghost-Azure resolves this by providing a production-ready template which can be hosted directly on Azure App Service. In the background, an Azure Function ([Ghost-Release-Uploader](https://github.com/YannickRe/Ghost-Release-Uploader)) makes sure that this repository stays up-to-date with the latest releases of Ghost.
This template is a prooof of concept that builds on forked from YannickRe/Ghost-Azure (which was a single deployed webapp), and tries to make it into a load balanced regional outage tollerent architecture.
Most of the work has been done by [Radoslav Gatev](https://www.gatevnotes.com/introducing-ghost-2-on-azure-web-app-service/) who created the deployment template and the release uploader. Due to unknown reasons his repository wasn't being kept up-to-date with the latest releases, so we forked it and ran our own processes.
https://github.com/YannickRe/Ghost-Azure was build on the repository from https://github.com/RadoslavGatev/Ghost-Azure and this repository build on YannickRe/Ghost-Azure.

## Installation method
In any case I suggest forking my repository into your own, this to avoid changes I make to my repository to negatively impact your installation.

This installation contains two sections
1.  Azure deployment for establishing resources along with the compiled code via azuredeploy.json and a convienent One-click deploy button.
    It will deploy 2 sets of (Azure webapp, application insight for the webapp and a plan for the webapp) and both those webapps will be fronted by Azure Front door and a WAF

2.  The other part is a pipeline to keep the Webapps updated with latest code via a pipeline file azure-pipelines.yml
    This is a Azure pipelines file that can be used with Azure Devops.
    Azure devopsshould have a connection to the subscription being deployed, pipeline will needs the following variables
    AzureSubscription:  Name of the connection that was established between Azure devops and azure subscription
    RESOURCEGROUP:  RG that you want ot deploy to
    WEBAPP1:    Name of first Webapp
    UrlProduction1: URL of first Webapp
    UrlStaging1:    Name of first Webapp slot named "staging"
    WEBAPP2:    Name of Second Webapp
    UrlProduction2: URL of first Webapp
    UrlStaging2:    Name of first Webapp slot named "staging"
    WEBJOBNAME: 'redeploy'
    SlotNameStaging:    'staging'
    

## Current issues
1.  ARM template is unable to restrict the call to the individual webapps from only Frontdoor due to problems accessing FrontDoor ID as a reference
2.  ARM template source code deployment to mostly 2nd webapp ends with a message 'conflict', this is solved mostly if resources are deleted and reployed
3.  Azure pipeline deployment of the modified repository to the staging leads to a failure somtimes as the website does not comeup and leads to warmup timeout (looks like a problem with initial repository too)

Note: The website only starts to warmup only when it is accessed and it takes a few minutes to warm up.

### One-click deploy
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdashanan13%2FGhost-Azure%2FARM%2Fazuredeploy.json)

### Azure App Service Deployment Center
More info on [Microsoft Docs](https://docs.microsoft.com/en-us/azure/app-service/deploy-continuous-deployment#deploy-continuously-from-github)
