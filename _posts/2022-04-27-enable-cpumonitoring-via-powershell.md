---
title: "Enable Proactive CPU monitoring for an Azure App via PowerShell" 
---

[Proactive CPU Monitoring](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html) is a diagnostic feature of Azure App Service that allows you to capture diagnostic data whenever an app is consuming high CPU. In this blog, I am going to share a sample PowerShell script that can be used to enable CPU monitoring on Azure Web app via Powershell.

```powershell
$subscriptionId = "Your_Subscription_Id"    # Your App's SubscriptionId
$webapp = "App Name"                        # Your App's Name
$rg = "Resource Group"                      # Your App's Resource Group

$path =  "/subscriptions/$subscriptionId/resourceGroups/$rg/providers/Microsoft.Web/sites/$webapp/extensions/daas/api/cpumonitoring?api-Version=2015-08-01"
$monitoringSettings = @{
    Mode="CollectAndKill";
    MonitorDuration=15;
    ThresholdSeconds=60;
    CpuThreshold=90;
    MonitorScmProcesses=$true;
    MaxActions=10;
    MaximumNumberOfHours=480;
}

$payload = $monitoringSettings | ConvertTo-Json
Invoke-AzRestMethod -Path $path -Method "POST" -Payload $payload
```

The above create a CPU monitoring rule with the below parameters

> When the site's process or any child processes of the site's process takes 90% of CPU for more than 60 seconds, collect a memory dump and kill the process. Evaluate CPU usage every 15 seconds. Collect a maximum of 10 memory dumps. Monitoring will stop automatically after 20 days. The worker process for the kudu site and the web jobs under this app will also be monitored.

The script is provided as an example to demonstrate configuring CPU Monitoring  via PowerShell. It is *in no way* recommended for all apps and you should fine tune it per your requirement.

> Note : Before running the above script the **WEBSITE_DAAS_STORAGE_SASURI** app setting must be configured with a valid SAS URI to the storage account where the dumps should be uploaded. Without this app setting, the CPU monitoring rule will not work.

**Hope this helps!**
