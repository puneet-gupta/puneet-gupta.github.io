---
title: "Enable Custom Auto-Heal settings for an Azure App via PowerShell" 
---

[Auto-healing](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html) is a powerful feature of Azure App Service and is used a lot by Azure App Service customers. Once a requirement came to me asking if it was possible to configure Auto-Healing settings for an app via PowerShell. Since all the AutoHeal settings are stored in ARM (Azure Resource Manager), you can easily write a PowerShell script that configures autohealing for your app and use the script to set the same settings across all your apps.

It took me some time while writing such a script so I thought of sharing it on this blog.

```powershell
$subscriptionId = "Your_Subscription_Id"    # Your App's SubscriptionId
$webapp = "App Name"                        # Your App's Name
$rg = "Resource Group"                      # Your App's Resource Group

Select-AzureRmSubscription -SubscriptionId $subscriptionId

$autohealRules = @{
    triggers=@{privateBytesInKB=777777;requests=@{count=1000;timeInterval="00:02:39"}};
    actions=@{actionType="Recycle";minProcessExecutionTime="00:01:39"};
}

$PropertiesObject = @{
     autoHealEnabled = $true;
    autoHealRules = $autohealRules
}

Set-AzureRmResource -PropertyObject$PropertiesObject -ResourceGroupName $rg -ResourceType Microsoft.Web/sites/config -ResourceName "$webapp/web" -ApiVersion 2018-02-01 -Force

```

The above script configures these two auto-heal rules and sets the action to **Recyle** whenever the conditions are met

1. Whenever Private Bytes for an app cross 777777 KB.
2. Whenever 1000 requests are served in a time interval of 2 minutes 39 seconds.

> Note: The script is provided as an example to demonstrate configuring AutoHeal rules via PowerShell. It is *in no way* recommended for all apps and you should fine tune it per your requirement.

**Hope this helps!**