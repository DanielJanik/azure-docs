---
title: Set up diagnostic logs - Azure Event Hub | Microsoft Docs
description: Learn how to set up activity logs and diagnostic logs for event hubs in Azure.
keywords:
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager:
editor:

ms.assetid:
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 04/28/2020
ms.author: shvija

---
# Set up diagnostic logs for an Azure event hub

You can view two types of logs for Azure Event Hubs:

* **[Activity logs](../azure-monitor/platform/platform-logs-overview.md)**: These logs have information about operations done on a job. The logs are always enabled. You can see activity log entries by selecting **Activity log** in the left pane for your event hub namespace in the Azure portal. For example: "Create or Update Namespace", "Create or Update Event Hub".

    ![Activity log for an Event Hubs namespace](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Diagnostic logs](../azure-monitor/platform/platform-logs-overview.md)**: You can configure diagnostic logs for a richer view of everything that happens with a job. Diagnostic logs cover activities from the time the job is created until the job is deleted, including updates and activities that occur while the job is running.

    The following section shows you how to enable diagnostic logs for an Event Hubs namespace.

## Enable diagnostic logs
Diagnostic logs are disabled by default. To enable diagnostic logs, follow these steps:

1.	In the [Azure portal](https://portal.azure.com), navigate to your Event Hubs namespace. 
2. Select **Diagnostics settings** under **Monitoring** in the left pane, and then select **+ Add diagnostic setting**. 

    ![Diagnostic settings page - add diagnostic setting](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. In the **Category details** section, select the **types of diagnostic logs** that you want to enable. You'll find details about these categories later in this article. 
5. In the **Destination details** section, set the archive target (destination) that you want; for example, a storage account, an event hub, or a Log Analytics workspace.

    ![Add diagnostic settings page](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.	Select **Save** on the toolbar to save the diagnostics settings.

    New settings take effect in about 10 minutes. After that, logs appear in the configured archival target, in the **Diagnostics logs** pane.

    For more information about configuring diagnostics, see the [overview of Azure diagnostic logs](../azure-monitor/platform/platform-logs-overview.md).

## Diagnostic logs categories

Event Hubs captures diagnostic logs for the following categories:

- **Archive Logs**: logs related to Event Hubs archives, specifically, logs related to archive errors.
- **Operational Logs**: information about what is happening during Event Hubs operations, specifically, the operation type, including event hub creation, resources used, and the status of the operation.
- **Auto scale logs**: information about autoscaling operations done on an Event Hubs namespace. 
- **Kafka coordinator logs** - information about Kafka coordinator operations related to Event Hubs. 
- **Kafka user logs**: information about Kafka user operations related to Event Hubs. 
- **Event Hubs virtual network (VNet) connection event**: information about Event Hubs virtual network connection events. 
- **Customer-managed key user logs**: information about operations related to customer-managed key. 


    All logs are stored in JavaScript Object Notation (JSON) format. Each entry has string fields that use the format described in the following sections.

## Archive logs schema

Archive log JSON strings include elements listed in the following table:

Name | Description
------- | -------
TaskName | Description of the task that failed.
ActivityId | Internal ID, used for tracking.
trackingId | Internal ID, used for tracking.
resourceId | Azure Resource Manager resource ID.
eventHub | Event hub full name (includes namespace name).
partitionId | Event Hub partition being written to.
archiveStep | ArchiveFlushWriter
startTime | Failure start time.
failures | Number of times failure occurred.
durationInSeconds | Duration of failure.
message | Error message.
category | ArchiveLogs

The following code is an example of an archive log JSON string:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## Operational logs schema

Operational log JSON strings include elements listed in the following table:

Name | Description
------- | -------
ActivityId | Internal ID, used to track purpose.
EventName | Operation name.	 
resourceId | Azure Resource Manager resource ID.
SubscriptionId | Subscription ID.
EventTimeString | Operation time.
EventProperties | Operation properties.
Status | Operation status.
Caller | Caller of operation (Azure portal or management client).
category | OperationalLogs

The following code is an example of an operational log JSON string:

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## Autoscale logs schema
Autoscale log JSON includes elements listed in the following table:

| Name | Description |
| ---- | ----------- | 
| trackingId | Internal ID, which is used for tracing purposes |
| resourceId | Internal ID, which contains Azure subscription ID and namespace name |
| message | Informational message, which provides details about auto-inflate action. The message contains previous and current value of throughput unit for a given namespace and what triggered the inflate of the TU. |

## Kafka coordinator logs schema
Kafka coordinator log JSON includes elements listed in the following table:

| Name | Description |
| ---- | ----------- | 
| requestId | request ID, which is used for tracing purposes |
| resourceId | Internal ID, which contains Azure subscription ID and namespace name |
| operationName | Name of the operation that's done during the group coordination |
| clientId | Client ID |
| namespaceName | Namespace name | 
| subscriptionId | Azure subscription ID |
| message | Informational message, which provides details about actions done during the consumer group coordination. |

## Kafka user error logs schema
Kafka user error log JSON includes elements listed in the following table:

| Name | Description |
| ---- | ----------- |
| trackingId | tracking ID, which is used for tracing purposes. |
| namespaceName | Namespace name |
| eventhub | Event hub name |
| partitionId | Partition ID |
| groupId | Group ID |
| ClientId | Client ID |
| resourceId | Internal ID, which contains Azure subscription ID and namespace name |
| message | Informational message, which provides details about an error |

## Event Hubs virtual network connection event schema

Event Hubs virtual network (VNet) connection event JSON includes elements listed in the following table:

| Name | Description |
| ---  | ----------- | 
| subscriptionId | Azure subscription ID |
| namespaceName | Namespace name |
| ipAddress | IP address of a client connecting to the Event Hubs service |
| action | Action done by the Event Hubs service when evaluating connection requests. Supported actions are **AcceptConnection** and **RejectConnection**. |
| reason | Provides a reason why the action was done |
| count | Number of occurrences for the given action |
| resourceId | Internal resource ID, which contains subscription ID and namespace name. |

## Customer-managed key user logs
Customer-managed key user log JSON includes elements listed in the following table:

| Name | Description |
| ---- | ----------- | 
| category | Type of category for a message. It's one of the following values: **error** and **info** |
| resourceId | Internal resource ID, which includes Azure subscription ID and namespace name |
| keyVault | Name of the Key Vault resource |
| key | Name of the Key Vault key. |
| version | Version of the Key Vault key |
| operation | The name of an operation done to serve requests |
| code | Status code |
| message | Message, which provides details about an error or informational message |



## Next steps
- [Introduction to Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs API overview](event-hubs-api-overview.md)
- Get started with Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
