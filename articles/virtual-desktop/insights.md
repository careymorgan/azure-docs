---
title: Use Monitor Azure Virtual Desktop Monitor - Azure
description: How to use Azure Virtual Desktop Insights.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2020
ms.author: helohr
manager: femila
---
# Use Azure Virtual Desktop Insights to monitor your deployment

Azure Virtual Desktop Insights is a dashboard built on Azure Monitor Workbooks that helps IT professionals understand their Azure Virtual Desktop environments. This topic will walk you through how to set up Azure Virtual Desktop Insights to monitor your Azure Virtual Desktop environments.

## Requirements

Before you start using Azure Virtual Desktop Insights, you'll need to set up the following things:

- All Azure Virtual Desktop environments you monitor must be based on the latest release of Azure Virtual Desktop that’s compatible with Azure Resource Manager.
- At least one configured Log Analytics Workspace. Use a designated Log Analytics workspace for your Azure Virtual Desktop session hosts to ensure that performance counters and events are only collected from session hosts in your Azure Virtual Desktop deployment.
- Enable data collection for the following things in your Log Analytics workspace:
    - Diagnostics from your Azure Virtual Desktop environment
    - Recommended performance counters from your Azure Virtual Desktop session hosts
    - Recommended Windows Event Logs from your Azure Virtual Desktop session hosts

 The data setup process described in this article is the only one you'll need to monitor Azure Virtual Desktop. You can disable all other items sending data to your Log Analytics workspace to save costs.

Anyone monitoring Azure Virtual Desktop Insights for your environment will also need the following read-access permissions:

- Read-access to the Azure resource groups that hold your Azure Virtual Desktop resources.
- Read-access to the subscription's resource groups that hold your Azure Virtual Desktop session hosts.
- Read access to the Log Analytics workspace. In the case that multiple Log Analytics workspaces are used, read access should be granted to each to allow viewing data.

> [!NOTE]
> Read access only lets admins view data. They'll need different permissions to manage resources in the Azure Virtual Desktop portal.

## Open Azure Virtual Desktop Insights

You can open Azure Virtual Desktop Insights with one of the following methods:

- Go to [aka.ms/avdi](https://aka.ms/avdi).
- Search for and select **Azure Virtual Desktop** from the Azure portal, then select **Insights**.
- Search for and select **Azure Monitor** from the Azure portal. Select **Insights Hub** under **Insights**, then select **Azure Virtual Desktop**.
Once you have the page open, enter the **Subscription**, **Resource group**, **Host pool**, and **Time range** of the environment you want to monitor.

>[!NOTE]
>Azure Virtual Desktop currently only supports monitoring one subscription, resource group, and host pool at a time. If you can't find the environment you want to monitor, see [our troubleshooting documentation](troubleshoot-insights.md) for known feature requests and issues.

## Log Analytics settings

To start using Azure Virtual Desktop Insights, you'll need at least one Log Analytics workspace. Use a designated Log Analytics workspace for your Azure Virtual Desktop session hosts to ensure that performance counters and events are only collected form session hosts in your Azure Virtual Desktop deployment. If you already have a workspace set up, skip ahead to [Set up using the configuration workbook](#set-up-using-the-configuration-workbook). To set one up, see [Create a Log Analytics workspace in the Azure portal](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Standard data storage charges for Log Analytics will apply. To start, we recommend you choose the pay-as-you-go model and adjust as you scale your deployment and take in more data. To learn more, see [Azure Monitor pricing](https://azure.microsoft.com/pricing/details/monitor/).

## Set up using the configuration workbook

If it's your first time opening Azure Virtual Desktop Insights, you'll need set up Azure Virtual Desktop Insights for your Azure Virtual Desktop environment. To configure your resources:

1. Open Azure Virtual Desktop Insights in the Azure portal at [aka.ms/avdi](https://aka.ms/avdi), then select **configuration workbook**.
2. Select an environment to configure under **Subscription**, **Resource Group**, and **Host Pool**.

The configuration workbook sets up your monitoring environment and lets you check the configuration after you've finished the setup process. It's important to check your configuration if items in the dashboard aren't displaying correctly, or when the product group publishes updates that require new settings.

### Resource diagnostic settings

To collect information on your Azure Virtual Desktop infrastructure, you'll need to enable several diagnostic settings on your Azure Virtual Desktop host pools and workspaces (this is your Azure Virtual Desktop workspace, not your Log Analytics workspace). To learn more about host pools, workspaces, and other Azure Virtual Desktop resource objects, see our [environment guide](environment-setup.md).

You can learn more about Azure Virtual Desktop diagnostics and the supported diagnostic tables at [Send Azure Virtual Desktop diagnostics to Log Analytics](diagnostics-log-analytics.md).

To set your resource diagnostic settings in the configuration workbook: 

1. Select the **Resource diagnostic settings** tab in the configuration workbook. 
2. Select **Log Analytics workspace** to send Azure Virtual Desktop diagnostics. 

#### Host pool diagnostic settings

To set up host pool diagnostics using the resource diagnostic settings section in the configuration workbook:

1. Under **Host pool**, check to see whether Azure Virtual Desktop diagnostics are enabled. If they aren't, an error message will appear that says "No existing diagnostic configuration was found for the selected host pool." You'll need to enable the following supported diagnostic tables:

    - Checkpoint
    - Error
    - Management
    - Connection
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > If you don't see the error message, you don't need to do steps 2 through 4.

2. Select **Configure host pool**.
3. Select **Deploy**.
4. Refresh the configuration workbook.

#### Workspace diagnostic settings 

To set up workspace diagnostics using the resource diagnostic settings section in the configuration workbook:

 1. Under **Workspace**, check to see whether Azure Virtual Desktop diagnostics are enabled for the Azure Virtual Desktop workspace. If they aren't, an error message will appear that says "No existing diagnostic configuration was found for the selected workspace." You'll need to enable the following supported diagnostics tables:
 
    - Checkpoint
    - Error
    - Management
    - Feed
    
    >[!NOTE]
    > If you don't see the error message, you don't need to do steps 2-4.

2. Select **Configure workspace**.
3. Select **Deploy**.
4. Refresh the configuration workbook.

### Session host data settings

To collect information on your Azure Virtual Desktop session hosts, you'll need to install the Log Analytics agent on all session hosts in the host pool, make sure the session hosts are sending to a Log Analytics workspace, and configure your Log Analytics agent settings to collect performance data and Windows Event Logs.

The Log Analytics workspace you send session host data to doesn't have to be the same one you send diagnostic data to. If you have Azure session hosts outside of your Azure Virtual Desktop environment, we recommend having a designated Log Analytics workspace for the Azure Virtual Desktop session hosts. 

To set the Log Analytics workspace where you want to collect session host data: 

1. Select the **Session host data settings** tab in the configuration workbook. 
2. Select the **Log Analytics workspace** you want to send session host data to. 

#### Session hosts

You'll need to install the Log Analytics agent on all session hosts in the host pool and send data from those hosts to your selected Log Analytics workspace. If Log Analytics isn't configured for all the session hosts in the host pool, you'll see a **Session hosts** section at the top of **Session host data settings** with the message "Some hosts in the host pool are not sending data to the selected Log Analytics workspace."

>[!NOTE]
> If you don't see the **Session hosts** section or error message, all session hosts are set up correctly. Skip ahead to set up instructions for [Workspace performance counters](#workspace-performance-counters). Currently automated deployment is limited to 1000 session hosts or fewer.

To set up your remaining session hosts using the configuration workbook:

1. Select **Add hosts to workspace**. 
2. Refresh the configuration workbook.

>[!NOTE]
>For larger host pools (> 1000 session hosts), or if there are deployment issues, it is recommended to install the Log Analytics agent at [time of session host creation](../virtual-machines/extensions/oms-windows.md#extension-schema) through the use of an ARM template.

#### Workspace performance counters

You'll need to enable specific performance counters to collect performance information from your session hosts and send it to the Log Analytics workspace.

If you already have performance counters enabled and want to remove them, follow the instructions in [Configuring performance counters](../azure-monitor/agents/data-sources-performance-counters.md). You can add and remove performance counters in the same location.

To set up performance counters using the configuration workbook: 

1. Under **Workspace performance counters** in the configuration workbook, check **Configured counters** to see the counters you've already enabled to send to the Log Analytics workspace. Check **Missing counters** to make sure you've enabled all required counters.
2. If you have missing counters, select **Configure performance counters**.
3. Select **Apply Config**.
4. Refresh the configuration workbook.
5. Make sure all the required counters are enabled by checking the **Missing counters** list. 

#### Configure Windows Event Logs

You'll also need to enable specific Windows Event Logs to collect errors, warnings, and information from the session hosts and send them to the Log Analytics workspace.

If you've already enabled Windows Event Logs and want to remove them, follow the instructions in [Configuring Windows Event Logs](../azure-monitor/agents/data-sources-windows-events.md#configure-windows-event-logs).  You can add and remove Windows Event Logs in the same location.

To set up Windows Event Logs using the configuration workbook:

1. Under **Windows Event Logs configuration**, check **Configured Event Logs** to see the Event Logs you've already enabled to send to the Log Analytics workspace. Check **Missing Event Logs** to make sure you've enabled all Windows Event Logs.
2. If you have missing Windows Event Logs, select **Configure Events**.
3. Select **Deploy**.
4. Refresh the configuration workbook.
5. Make sure all the required Windows Event Logs are enabled by checking the **Missing Event Logs** list. 

>[!NOTE]
>If automatic event deployment fails, select **Open agent configuration** in the configuration workbook to manually add any missing Windows Event Logs. 

## Optional: configure alerts

Azure Virtual Desktop Insights allows you to monitor Azure Monitor alerts happening within your selected subscription in the context of your Azure Virtual Desktop data. Azure Monitor alerts are an optional feature on your Azure subscriptions, and you need to set them up separately from Azure Virtual Desktop Insights. You can use the Azure Monitor alerts framework to set custom alerts on Azure Virtual Desktop events, diagnostics, and resources. To learn more about Azure Monitor alerts, see [Azure Monitor Log Alerts](../azure-monitor/alerts/alerts-log.md).

## Diagnostic and usage data

Microsoft automatically collects usage and performance data through your use of the Azure Virtual Desktop Insights service. Microsoft uses this data to improve the quality, security, and integrity of the service.

To provide accurate and efficient troubleshooting capabilities, the collected data includes the portal session ID, Azure Active Directory user ID, and the name of the portal tab where the event occurred. Microsoft doesn't collect names, addresses, or other contact information.

For more information about data collection and usage, see the [Microsoft Online Services Privacy Statement](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>To learn about viewing or deleting your personal data collected by the service, see [Azure Data Subject Requests for the GDPR](/microsoft-365/compliance/gdpr-dsr-azure). For more information about GDPR, see [the GDPR section of the Service Trust portal](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## Next steps

Now that you’ve configured Azure Virtual Desktop Insights for your Azure Virtual Desktop environment, here are some resources that might help you start monitoring your environment:

- Check out our [glossary](insights-glossary.md) to learn more about terms and concepts related to Azure Virtual Desktop Insights.
- To estimate, measure, and manage your data storage costs, see [Estimate Azure Virtual Desktop Insights costs](insights-costs.md).
- If you encounter a problem, check out our [troubleshooting guide](troubleshoot-insights.md) for help and known issues.
- To see what's new in each version update, see [What's new in Azure Virtual Desktop Insights](whats-new-insights.md).
