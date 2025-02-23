---
title: Limits and configuration reference guide
description: Reference guide to limits and configuration information for Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rohithah, rarayudu, azla
ms.topic: reference
ms.date: 07/29/2021
---

# Limits and configuration reference for Azure Logic Apps

> For Power Automate, see [Limits and configuration in Power Automate](/flow/limits-and-config).

This article describes the limits and configuration information for Azure Logic Apps and related resources. To create logic app workflows, you choose the **Logic App** resource type based on your scenario, solution requirements, the capabilities that you want, and the environment where you want to run your workflows.

> [!NOTE]
> Many limits are the same across these host environments, but differences are noted where they exist. 
> If your scenarios require different limits, [contact the Logic Apps team](mailto://logicappspm@microsoft.com) 
> to discuss your requirements.

The following table briefly summarizes differences between the original **Logic App (Consumption)** resource type and the **Logic App (Standard)** resource type. You'll also learn how the *single-tenant* environment compares to the *multi-tenant* and *integration service environment (ISE)* for deploying, hosting, and running your logic app workflows.

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="definition-limits"></a>

## Workflow definition limits

The following tables list the values for a single workflow definition:

| Name | Limit | Notes |
| ---- | ----- | ----- |
| Workflows per region per subscription | 1,000 workflows ||
| Triggers per workflow | 10 triggers | This limit applies only when you work on the JSON workflow definition, whether in code view or an Azure Resource Manager (ARM) template, not the designer. |
| Actions per workflow | 500 actions | To extend this limit, you can use nested workflows as necessary. |
| Actions nesting depth | 8 actions | To extend this limit, you can use nested workflows as necessary. |
| Trigger or action - Maximum name length | 80 characters ||
| Trigger or action - Maximum input or output size | 104,857,600 bytes <br>(105 MB) | For more information about editing the default limit in the single-tenant model, review [Edit host and app settings for logic apps in single-tenant Azure Logic Apps](edit-app-settings-host-settings.md). |
| Action - Maximum combined inputs and outputs size | 209,715,200 bytes <br>(210 MB) ||
| Expression character limit | 8,192 characters ||
| `description` - Maximum length | 256 characters ||
| `parameters` - Maximum number of items | 50 parameters ||
| `outputs` - Maximum number items | 10 outputs ||
| `trackedProperties` - Maximum size | 8,000 characters ||
||||

<a name="run-duration-retention-limits"></a>

## Run duration and retention history limits

The following table lists the values for a single workflow run:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Run history retention in storage | 90 days | 90 days | 366 days | The amount of time to keep a workflow's run history in storage after a run starts. <p>**Note**: If the workflow's run duration exceeds the retention limit, that run is removed from the run history in storage. If a run isn't immediately removed after reaching the retention limit, the run is removed within 7 days. <p><p>Whether a run completes or times out, run history retention is always calculated by using the run's start time and the current limit specified in the workflow setting, [**Run history retention in days**](#change-retention). No matter the previous limit, the current limit is always used for calculating retention. For more information, review [Change duration and run history retention in storage](#change-retention). <p><p>**Tip**: For scenarios that require different limits, [contact the Logic Apps team](mailto://logicappspm@microsoft.com) to discuss your requirements. |
| Run duration | 90 days | - Stateful workflow: 90 days <p><p>- Stateless workflow: 5 min | 366 days | The amount of time that a workflow can continue running before forcing a timeout. <p>The run duration is calculated by using a run's start time and the limit that's specified in the workflow setting, [**Run history retention in days**](#change-duration) at that start time. <p>**Important**: Make sure the run duration value is always less than or equal to the run history retention in storage value. Otherwise, run histories might be deleted before the associated jobs are complete. <p><p>For more information, review [Change run duration and history retention in storage](#change-duration). <p><p>**Tip**: For scenarios that require different limits, [contact the Logic Apps team](mailto://logicappspm@microsoft.com) to discuss your requirements. |
| Recurrence interval | - Min: 1 sec <p><p>- Max: 500 days | - Min: 1 sec <p><p>- Max: 500 days  | - Min: 1 sec <p><p>- Max: 500 days ||
||||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### Change run duration and history retention in storage

In the designer, the same setting controls the maximum number of days that a workflow can run and for keeping run history in storage.

* For the multi-tenant service, the 90-day default limit is the same as the maximum limit. You can only decrease this value.

* For the single-tenant service, you can decrease or increase the 90-day default limit. For more information, see [Create workflows for single-tenant Azure Logic Apps using Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md).

* For an integration service environment, you can decrease or increase the 90-day default limit.

> [!TIP]
> For scenarios that require different limits, [contact the Logic Apps team](mailto://logicappspm@microsoft.com) to discuss your requirements.

For example, suppose that you reduce the retention limit from 90 days to 30 days. A 60-day-old run is removed from the runs history. If you increase the retention period from 30 days to 60 days, a 20-day-old run stays in the runs history for another 40 days.

> [!IMPORTANT]
> If the run's duration exceeds the current run history retention limit, the run is removed from the runs history in storage. 
> To avoid losing run history, make sure that the retention limit is *always* more than the run's longest possible duration.

To change the default value or current limit for these properties, follow these steps:

#### [Portal (multi-tenant service)](#tab/azure-portal)

1. In the [Azure portal](https://portal.azure.com) search box, find and select **Logic apps**.

1. Find and open your logic app in the Logic App Designer.

1. On the logic app's menu, select **Workflow settings**.

1. Under **Runtime options**, from the **Run history retention in days** list, select **Custom**.

1. Drag the slider to change the number of days that you want.

1. When you're done, on the **Workflow settings** toolbar, select **Save**.

#### [Resource Manager template](#tab/azure-resource-manager)

If you use an Azure Resource Manager template, this setting appears as a property in your workflow's resource definition, which is described in the [Microsoft.Logic workflows template reference](/azure/templates/microsoft.logic/workflows):

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```
---

<a name="concurrency-looping-and-debatching-limits"></a>
<a name="looping-debatching-limits"></a>

## Looping, concurrency, and debatching limits

The following table lists the values for a single workflow run:

### Loop actions

<a name="for-each-loop"></a>

#### For each loop

The following table lists the values for a **For each** loop:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Array items | 100,000 items | - Stateful workflow: 100,000 items <p><p>- Stateless workflow: 100 items | 100,000 items | The number of array items that a **For each** loop can process. <p><p>To filter larger arrays, you can use the [query action](logic-apps-perform-data-operations.md#filter-array-action). |
| Concurrent iterations | Concurrency off: 20 <p><p>Concurrency on: <p>- Default: 20 <br>- Min: 1 <br>- Max: 50 | Concurrency off: 20 <p><p>Concurrency on: <p><p>- Default: 20 <br>- Min: 1 <br>- Max: 50 | Concurrency off: 20 <p><p>Concurrency on: <p>- Default: 20 <br>- Min: 1 <br>- Max: 50 | The number of **For each** loop iterations that can run at the same time, or in parallel. <p><p>To change this value in the multi-tenant service, see [Change **For each** concurrency limit](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency) or [Run **For each** loops sequentially](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each). |
||||||

<a name="until-loop"></a>

#### Until loop

The following table lists the values for an **Until** loop:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Iterations | - Default: 60 <br>- Min: 1 <br>- Max: 5,000 | Stateful workflow: <p><p>- Default: 60 <br>- Min: 1 <br>- Max: 5,000 <p><p>Stateless workflow: <p><p>- Default: 60 <br>- Min: 1 <br>- Max: 100 | - Default: 60 <br>- Min: 1 <br>- Max: 5,000 | The number of cycles that an **Until** loop can have during a workflow run. <p><p>To change this value, in the **Until** loop shape, select **Change limits**, and specify the value for the **Count** property. |
| Timeout | Default: PT1H (1 hour) | Stateful workflow: PT1H (1 hour) <p><p>Stateless workflow: PT5M (5 min) | Default: PT1H (1 hour) | The amount of time that the **Until** loop can run before exiting and is specified in [ISO 8601 format](https://en.wikipedia.org/wiki/ISO_8601). The timeout value is evaluated for each loop cycle. If any action in the loop takes longer than the timeout limit, the current cycle doesn't stop. However, the next cycle doesn't start because the limit condition isn't met. <p><p>To change this value, in the **Until** loop shape, select **Change limits**, and specify the value for the **Timeout** property. |
||||||

<a name="concurrency-debatching"></a>

### Concurrency and debatching

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Trigger - concurrent runs | Concurrency off: Unlimited <p><p>Concurrency on (irreversible): <p><p>- Default: 25 <br>- Min: 1 <br>- Max: 100 | Concurrency off: Unlimited <p><p>Concurrency on (irreversible): <p><p>- Default: 25 <br>- Min: 1 <br>- Max: 100 | Concurrency off: Unlimited <p><p>Concurrency on (irreversible): <p><p>- Default: 25 <br>- Min: 1 <br>- Max: 100 | The number of concurrent runs that a trigger can start at the same time, or in parallel. <p><p>**Note**: When concurrency is turned on, the **SplitOn** limit is reduced to 100 items for [debatching arrays](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). <p><p>To change this value in the multi-tenant service, see [Change trigger concurrency limit](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency) or [Trigger instances sequentially](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger). |
| Maximum waiting runs | Concurrency off: <p><p>- Min: 1 run <p>- Max: 50 runs <p><p>Concurrency on: <p><p>- Min: 10 runs plus the number of concurrent runs <p>- Max: 100 runs | Concurrency off: <p><p>- Min: 1 run <p>- Max: 50 runs <p><p>Concurrency on: <p><p>- Min: 10 runs plus the number of concurrent runs <p>- Max: 100 runs | Concurrency off: <p><p>- Min: 1 run <p>- Max: 50 runs <p><p>Concurrency on: <p><p>- Min: 10 runs plus the number of concurrent runs <p>- Max: 100 runs | The number of workflow instances that can wait to run when your current workflow instance is already running the maximum concurrent instances. <p><p>To change this value in the multi-tenant service, see [Change waiting runs limit](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs). |
| **SplitOn** items | Concurrency off: 100,000 items <p><p>Concurrency on: 100 items | Concurrency off: 100,000 items <p><p>Concurrency on: 100 items | Concurrency off: 100,000 items <p><p>Concurrency on: 100 items | For triggers that return an array, you can specify an expression that uses a **SplitOn** property that [splits or debatches array items into multiple workflow instances](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) for processing, rather than use a **For each** loop. This expression references the array to use for creating and running a workflow instance for each array item. <p><p>**Note**: When concurrency is turned on, the **SplitOn** limit is reduced to 100 items. |
||||||

<a name="throughput-limits"></a>

## Throughput limits

The following table lists the values for a single workflow definition:

### Multi-tenant & single-tenant

| Name | Limit | Notes |
| ---- | ----- | ----- |
| Action - Executions per 5-minute rolling interval | - Default: 100,000 executions <p><p>- High throughput mode: 300,000 executions  | To raise the default value to the maximum value for your workflow, see [Run in high throughput mode](#run-high-throughput-mode), which is in preview. Or, you can [distribute the workload across more than one workflow](handle-throttling-problems-429-errors.md#logic-app-throttling) as necessary. |
| Action - Concurrent outbound calls | ~2,500 calls | You can reduce the number of concurrent requests or reduce the duration as necessary. |
| Managed connector throttling | - Multi-tenant: Throttling limit varies based on connector <p><p>- Single-tenant: 50 requests per minute per connection | For multi-tenant, review [each managed connector's technical reference page](/connectors/connector-reference/connector-reference-logicapps-connectors). <p><p>For more information about handling connector throttling, review [Handle throttling problems ("429 - Too many requests" errors)](handle-throttling-problems-429-errors.md#connector-throttling). |
| Runtime endpoint - Concurrent inbound calls | ~1,000 calls | You can reduce the number of concurrent requests or reduce the duration as necessary. |
| Runtime endpoint - Read calls per 5 min  | 60,000 read calls | This limit applies to calls that get the raw inputs and outputs from a workflow's run history. You can distribute the workload across more than one workflow as necessary. |
| Runtime endpoint - Invoke calls per 5 min | 45,000 invoke calls | You can distribute workload across more than one workflow as necessary. |
| Content throughput per 5 min | 600 MB | You can distribute workload across more than one workflow as necessary. |
||||

<a name="run-high-throughput-mode"></a>

### Run in high throughput mode

For a single workflow definition, the number of actions that run every 5 minutes has a [default limit](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). To raise the default value to the [maximum value](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) for your workflow, which is three times the default value, you can enable high throughput mode, which is in preview. Or, you can [distribute the workload across more than one workflow](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling) as necessary.

#### [Portal (multi-tenant service)](#tab/azure-portal)

1. In the Azure portal, on your logic app's menu, under **Settings**, select **Workflow settings**.

1. Under **Runtime options** > **High throughput**, change the setting to **On**.

   ![Screenshot that shows logic app menu in Azure portal with "Workflow settings" and "High throughput" set to "On".](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

#### [Resource Manager Template](#tab/azure-resource-manager)

To enable this setting in an ARM template for deploying your logic app, in the `properties` object for your logic app's resource definition, add the `runtimeConfiguration` object with the `operationOptions` property set to `OptimizedForHighThroughput`:

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

For more information about your logic app resource definition, see [Overview: Automate deployment for Azure Logic Apps by using Azure Resource Manager templates](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition).

---

### Integration service environment (ISE)

* [Developer ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): Provides up to 500 executions per minute, but note these considerations:

  * Make sure that you use this SKU only for exploration, experiments, development, or testing - not for production or performance testing. This SKU has no service-level agreement (SLA), scale up capability, or redundancy during recycling, which means that you might experience delays or downtime.

  * Backend updates might intermittently interrupt service.

* [Premium ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level): The following table describes this SKU's throughput limits, but to exceed these limits in normal processing, or run load testing that might go above these limits, [contact the Logic Apps team](mailto://logicappsemail@microsoft.com) for help with your requirements.

  | Name | Limit | Notes |
  |------|-------|-------|
  | Base unit execution limit | System-throttled when infrastructure capacity reaches 80% | Provides ~4,000 action executions per minute, which is ~160 million action executions per month |
  | Scale unit execution limit | System-throttled when infrastructure capacity reaches 80% | Each scale unit can provide ~2,000 more action executions per minute, which is ~80 million more action executions per month |
  | Maximum scale units that you can add | 10 scale units | |
  ||||

<a name="gateway-limits"></a>

## Data gateway limits

Azure Logic Apps supports write operations, including inserts and updates, through the on-premises data gateway. However, these operations have [limits on their payload size](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="variables-action-limits"></a>

## Variables action limits

The following table lists the values for a single workflow definition:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Variables per workflow | 250 variables | 250 variables | 250 variables ||
| Variable - Maximum content size | 104,857,600 characters | Stateful workflow: 104,857,600 characters <p><p>Stateless workflow: 1,024 characters | 104,857,600 characters ||
| Variable (Array type) - Maximum number of array items | 100,000 items | 100,000 items | Premium SKU: 100,000 items <p><p>Developer SKU: 5,000 items ||
||||||

<a name="http-limits"></a>

## HTTP request limits

The following tables list the values for a single inbound or outbound call:

<a name="http-timeout-limits"></a>

### Timeout duration

By default, the HTTP action and APIConnection actions follow the [standard asynchronous operation pattern](/azure/architecture/patterns/async-request-reply), while the Response action follows the *synchronous operation pattern*. Some managed connector operations make asynchronous calls or listen for webhook requests, so the timeout for these operations might be longer than the following limits. For more information, review [each connector's technical reference page](/connectors/connector-reference/connector-reference-logicapps-connectors) and also the [Workflow triggers and actions](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) documentation.

> [!NOTE]
> For the **Logic App (Standard)** resource type in the single-tenant model, stateless workflows can only run *synchronously*.

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Outbound request | 120 sec <br>(2 min) | 230 sec <br>(3.9 min) | 240 sec <br>(4 min) | Examples of outbound requests include calls made by the HTTP trigger or action. <p><p>**Tip**: For longer running operations, use an [asynchronous polling pattern](../logic-apps/logic-apps-create-api-app.md#async-pattern) or an ["Until" loop](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action). To work around timeout limits when you call another workflow that has a [callable endpoint](logic-apps-http-endpoint.md), you can use the built-in Azure Logic Apps action instead, which you can find in the designer's operation picker under **Built-in**. |
| Inbound request | 120 sec <br>(2 min) | 230 sec <br>(3.9 min) | 240 sec <br>(4 min) | Examples of inbound requests include calls received by the Request trigger, HTTP Webhook trigger, and HTTP Webhook action. <p><p>**Note**: For the original caller to get the response, all steps in the response must finish within the limit unless you call another nested workflow. For more information, see [Call, trigger, or nest logic apps](../logic-apps/logic-apps-http-endpoint.md). |
||||||

<a name="message-size-limits"></a>

### Messages

| Name | Chunking enabled | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|------------------|--------------|-------------------------|---------------------------------|-------|
| Content download - Maximum number of requests | Yes | 1,000 requests | 1,000 requests | 1,000 requests ||
| Message size | No | 100 MB | 100 MB | 200 MB | To work around this limit, see [Handle large messages with chunking](../logic-apps/logic-apps-handle-large-messages.md). However, some connectors and APIs don't support chunking or even the default limit. <p><p>- Connectors such as AS2, X12, and EDIFACT have their own [B2B message limits](#b2b-protocol-limits). <p>- ISE connectors use the ISE limit, not the non-ISE connector limits. |
| Message size | Yes | 1 GB | 1,073,741,824 bytes <br>(1 GB) | 5 GB | This limit applies to actions that either natively support chunking or let you enable chunking in their runtime configuration. <p><p>If you're using an ISE, the Logic Apps engine supports this limit, but connectors have their own chunking limits up to the engine limit, for example, see the [Azure Blob Storage connector's API reference](/connectors/azureblob/). For more information about chunking, see [Handle large messages with chunking](../logic-apps/logic-apps-handle-large-messages.md). |
| Content chunk size | Yes | Varies per connector | 52,428,800 bytes (52 MB) | Varies per connector | This limit applies to actions that either natively support chunking or let you enable chunking in their runtime configuration. |
|||||||

### Character limits

| Name | Limit | Notes |
|------|-------|-------|
| Expression evaluation limit | 131,072 characters | The `@concat()`, `@base64()`, `@string()` expressions can't be longer than this limit. |
| Request URL character limit | 16,384 characters | |
||||

<a name="retry-policy-limits"></a>

### Retry policy

| Name | Limit | Notes |
| ---- | ----- | ----- |
| Retry attempts | - Default: 4 attempts <br> - Max: 90 attempts | To change the default, use the [retry policy parameter](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Retry max delay | - Default: 1 day | To change the default, use the [retry policy parameter](../logic-apps/logic-apps-workflow-actions-triggers.md). |
| Retry min delay | - Default: 5 sec | To change the default, use the [retry policy parameter](../logic-apps/logic-apps-workflow-actions-triggers.md). |
||||

<a name="authentication-limits"></a>

### Authentication limits

The following table lists the values for a workflow that starts with a Request trigger and enables [Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) for authorizing inbound calls to the Request trigger:

| Name | Limit | Notes |
| ---- | ----- | ----- |
| Azure AD authorization policies | 5 policies | |
| Claims per authorization policy | 10 claims | |
| Claim value - Maximum number of characters | 150 characters |
||||

<a name="switch-action-limits"></a>

## Switch action limits

The following table lists the values for a single workflow definition:

| Name | Limit | Notes |
| ---- | ----- | ----- |
| Maximum number of cases per action | 25 ||
||||

<a name="inline-code-action-limits"></a>

## Inline Code action limits

The following table lists the values for a single workflow definition:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Maximum number of code characters | 1,024 characters | 100,000 characters | 1,024 characters | To use the higher limit, create a **Logic App (Standard)** resource, which runs in single-tenant Azure Logic Apps, either [by using the Azure portal](create-single-tenant-workflows-azure-portal.md) or [by using Visual Studio Code and the **Azure Logic Apps (Standard)** extension](create-single-tenant-workflows-visual-studio-code.md). |
| Maximum duration for running code | 5 sec | 15 sec | 1,024 characters | To use the higher limit, create a **Logic App (Standard)** resource, which runs in single-tenant Azure Logic Apps, either [by using the Azure portal](create-single-tenant-workflows-azure-portal.md) or [by using Visual Studio Code and the **Azure Logic Apps (Standard)** extension](create-single-tenant-workflows-visual-studio-code.md). |
||||||

<a name="custom-connector-limits"></a>

## Custom connector limits

For multi-tenant and integration service environment only, you can create and use [custom managed connectors](/connectors/custom-connectors), which are wrappers around an existing REST API or SOAP API. For single-tenant only, you can create and use [custom built-in connectors](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272).

The following table lists the values for custom connectors:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| Custom connectors | 1,000 per Azure subscription | Unlimited | 1,000 per Azure subscription ||
| Requests per minute for a custom connector | 500 requests per minute per connection | Based on your implementation | 2,000 requests per minute per *custom connector* ||
| Connection timeout | 2 min | Idle connection: <br>4 min <p><p>Active connection: <br>10 min | 2 min ||
||||||

For more information, review the following documentation:

* [Custom managed connectors overview](/connectors/custom-connectors)
* [Enable built-in connector authoring - Visual Studio Code with Azure Logic Apps (Standard) extension](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)

<a name="managed-identity"></a>

## Managed identity limits

| Name | Limit |
|------|-------|
| Managed identities per logic app | Either the system-assigned identity or 1 user-assigned identity |
| Number of logic apps that have a managed identity in an Azure subscription per region | 1,000 |
|||

> [!NOTE] 
> By default, a Logic App (Standard) resource has its system-assigned managed identity automatically enabled to 
> authenticate connections at runtime. This identity differs from the authentication credentials or connection 
> string that you use when you create a connection. If you disable this identity, connections won't work at 
> runtime. To view this setting, on your logic app's menu, under **Settings**, select **Identity**.

<a name="integration-account-limits"></a>

## Integration account limits

Each Azure subscription has these integration account limits:

* One [Free tier](../logic-apps/logic-apps-pricing.md#integration-accounts) integration account per Azure region. This tier is available only for public regions in Azure, for example, West US or Southeast Asia, but not for [Azure China 21Vianet](/azure/china/overview-operations) or [Azure Government](../azure-government/documentation-government-welcome.md).

* 1,000 total integration accounts, including integration accounts in any [integration service environments (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) across both [Developer and Premium SKUs](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

* Each ISE, whether [Developer or Premium](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), can use a single integration account at no extra cost, although the included account type varies by ISE SKU. You can create more integration accounts for your ISE up to the total limit for an [extra cost](logic-apps-pricing.md#ise-pricing).

  | ISE SKU | Integration account limits |
  |---------|----------------------------|
  | **Premium** | 20 total accounts, including one Standard account at no extra cost. With this SKU, you can have only [Standard](../logic-apps/logic-apps-pricing.md#integration-accounts) accounts. No Free or Basic accounts are permitted. |
  | **Developer** | 20 total accounts, including one [Free](../logic-apps/logic-apps-pricing.md#integration-accounts) account (limited to 1). With this SKU, you can have either combination: <p>- A Free account and up to 19 [Standard](../logic-apps/logic-apps-pricing.md#integration-accounts) accounts. <br>- No Free account and up to 20 Standard accounts. <p>No Basic or more Free accounts are permitted. <p><p>**Important**: Use the [Developer SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) for experimenting, development, and testing, but not for production or performance testing. |
  |||

To learn how pricing and billing work for ISEs, see the [Logic Apps pricing model](../logic-apps/logic-apps-pricing.md#ise-pricing). For pricing rates, see [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="artifact-number-limits"></a>

### Artifact limits per integration account

The following tables list the values for the number of artifacts limited to each integration account tier. For pricing rates, see [Logic Apps pricing](https://azure.microsoft.com/pricing/details/logic-apps/). To learn how pricing and billing work for integration accounts, see the [Logic Apps pricing model](../logic-apps/logic-apps-pricing.md#integration-accounts).

> [!NOTE]
> Use the Free tier only for exploratory scenarios, not production scenarios. 
> This tier restricts throughput and usage, and has no service-level agreement (SLA).

| Artifact | Free | Basic | Standard |
|----------|------|-------|----------|
| EDI trading agreements | 10 | 1 | 1,000 |
| EDI trading partners | 25 | 2 | 1,000 |
| Maps | 25 | 500 | 1,000 |
| Schemas | 25 | 500 | 1,000 |
| Assemblies | 10 | 25 | 1,000 |
| Certificates | 25 | 2 | 1,000 |
| Batch configurations | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### Artifact capacity limits

| Artifact | Limit | Notes |
| -------- | ----- | ----- |
| Assembly | 8 MB | To upload files larger than 2 MB, use an [Azure storage account and blob container](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
| Map (XSLT file) | 8 MB | To upload files larger than 2 MB, use the [Azure Logic Apps REST API - Maps](/rest/api/logic/maps/createorupdate). <p><p>**Note**: The amount of data or records that a map can successfully process is based on the message size and action timeout limits in Azure Logic Apps. For example, if you use an HTTP action, based on [HTTP message size and timeout limits](#http-limits), a map can process data up to the HTTP message size limit if the operation completes within the HTTP timeout limit. |
| Schema | 8 MB | To upload files larger than 2 MB, use an [Azure storage account and blob container](../logic-apps/logic-apps-enterprise-integration-schemas.md). |
||||

<a name="integration-account-throughput-limits"></a>

### Throughput limits

| Runtime endpoint | Free | Basic | Standard | Notes |
|------------------|------|-------|----------|-------|
| Read calls per 5 min | 3,000 | 30,000 | 60,000 | This limit applies to calls that get the raw inputs and outputs from a logic app's run history. You can distribute the workload across more than one account as necessary. |
| Invoke calls per 5 min | 3,000 | 30,000 | 45,000 | You can distribute the workload across more than one account as necessary. |
| Tracking calls per 5 min | 3,000 | 30,000 | 45,000 | You can distribute the workload across more than one account as necessary. |
| Blocking concurrent calls | ~1,000 | ~1,000 | ~1,000 | Same for all SKUs. You can reduce the number of concurrent requests or reduce the duration as necessary. |
||||

<a name="b2b-protocol-limits"></a>

### B2B protocol (AS2, X12, EDIFACT) message size

The following table lists the message size limits that apply to B2B protocols:

| Name | Multi-tenant | Single-tenant | Integration service environment | Notes |
|------|--------------|---------------|---------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 25 MB | Unavailable | v2 - 200 MB <br>v1 - 25 MB | Applies to decode and encode |
| X12 | 50 MB | Unavailable | 50 MB | Applies to decode and encode |
| EDIFACT | 50 MB | Unavailable | 50 MB | Applies to decode and encode |
||||

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## Firewall configuration: IP addresses and service tags

If your environment has strict network requirements or firewalls that limit traffic to specific IP addresses, your environment or firewall needs to allow access for *both* the [inbound](#inbound) and [outbound](#outbound) IP addresses used by the Azure Logic Apps service or runtime in the Azure region where your logic app resource exists. *All* logic apps in the same region use the same IP address ranges.

For example, suppose your logic apps are deployed in the West US region. To support calls that your logic apps send or receive through built-in triggers and actions, such as the [HTTP trigger or action](../connectors/connectors-native-http.md), your firewall needs to allow access for *all* the Azure Logic Apps service inbound IP addresses *and* outbound IP addresses that exist in the West US region.

If your workflow also uses [managed connectors](../connectors/managed.md), such as the Office 365 Outlook connector or SQL connector, or uses [custom connectors](/connectors/custom-connectors/), the firewall also needs to allow access for *all* the [managed connector outbound IP addresses](#outbound) in your logic app's Azure region.

If you use custom connectors that access on-premises resources through the [on-premises data gateway resource in Azure](logic-apps-gateway-connection.md), you need to set up the gateway installation to allow access for the corresponding *managed connectors [outbound IP addresses](#outbound)*.

For more information about setting up communication settings on the gateway, see these topics:

* [Adjust communication settings for the on-premises data gateway](/data-integration/gateway/service-gateway-communication)
* [Configure proxy settings for the on-premises data gateway](/data-integration/gateway/service-gateway-proxy)

<a name="ip-setup-considerations"></a>

### Firewall IP configuration considerations

Before you set up your firewall with IP addresses, review these considerations:

* If you're using [Power Automate](/power-automate/getting-started), some actions, such as **HTTP** and **HTTP + OpenAPI**, go directly through the Azure Logic Apps service and come from the IP addresses that are listed here. For more information about the IP addresses used by Power Automate, see [Limits and configuration for Power Automate](/flow/limits-and-config#ip-address-configuration).

* For [Microsoft Azure operated by 21Vianet](/azure/china/), review the [documentation version for Azure operated by 21Vianet](https://docs.azure.cn/en-us/logic-apps/logic-apps-limits-and-config#firewall-ip-configuration).

* If your logic app workflows run in single-tenant Azure Logic Apps, you need to find the fully qualified domain names (FQDNs) for your connections. For more information, review the corresponding sections in these topics:

  * [Firewall permissions for single tenant logic apps - Azure portal](create-single-tenant-workflows-azure-portal.md#firewall-setup)
  * [Firewall permissions for single tenant logic apps - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)

* If your logic app workflows run in an [integration service environment (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), make sure that you [open these ports too](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise).

* To help you simplify any security rules that you want to create, you can optionally use [service tags](../virtual-network/service-tags-overview.md) instead, rather than specify IP address prefixes for each region. These tags work across the regions where the Logic Apps service is available:

  * **LogicAppsManagement**: Represents the inbound IP address prefixes for the Logic Apps service.

  * **LogicApps**: Represents the outbound IP address prefixes for the Logic Apps service.

  * **AzureConnectors**: Represents the IP address prefixes for managed connectors that make inbound webhook callbacks to the Logic Apps service and outbound calls to their respective services, such as Azure Storage or Azure Event Hubs.

* If your logic apps have problems accessing Azure storage accounts that use [firewalls and firewall rules](../storage/common/storage-network-security.md), you have [various other options to enable access](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

  For example, logic apps can't directly access storage accounts that use firewall rules and exist in the same region. However, if you permit the [outbound IP addresses for managed connectors in your region](../logic-apps/logic-apps-limits-and-config.md#outbound), your logic apps can access storage accounts that are in a different region except when you use the Azure Table Storage or Azure Queue Storage connectors. To access your Table Storage or Queue Storage, you can use the HTTP trigger and actions instead. For other options, see [Access storage accounts behind firewalls](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls).

<a name="inbound"></a>

### Inbound IP addresses

This section lists the inbound IP addresses for the Azure Logic Apps service only. If you have Azure Government, see [Azure Government - Inbound IP addresses](#azure-government-inbound).

> [!TIP]
> To help reduce complexity when you create security rules, you can optionally use the
> [service tag](../virtual-network/service-tags-overview.md), **LogicAppsManagement**,
> rather than specify inbound Logic Apps IP address prefixes for each region. Optionally, 
> you can also use the **AzureConnectors** service tag for managed connectors that make 
> inbound webhook callbacks to the Logic Apps service, rather than specify inbound managed 
> connector IP address prefixes for each region. These tags work across the regions where 
> the Logic Apps service is available.
>
> The following connectors make inbound webhook callbacks to the Logic Apps service:
>
> Adobe Creative Cloud, Adobe Sign, Adobe Sign Demo, Adobe Sign Preview, Adobe Sign Stage, 
> Azure Sentinel, Business Central, Calendly, Common Data Service, DocuSign, DocuSign Demo, 
> Dynamics 365 for Fin & Ops, LiveChat, Office 365 Outlook, Outlook.com, Parserr, SAP*, 
> Shifts for Microsoft Teams, Teamwork Projects, Typeform
>
> \* **SAP**: The return caller depends on whether the deployment environment is either 
> multi-tenant Azure or ISE. In the multi-tenant environment, the on-premises data gateway 
> makes the call back to the Logic Apps service. In an ISE, the SAP connector makes the 
> call back to the Logic Apps service.

<a name="multi-tenant-inbound"></a>

#### Multi-tenant & single-tenant - Inbound IP addresses

| Region | IP |
|--------|----|
| Australia East | 13.75.153.66, 104.210.89.222, 104.210.89.244, 52.187.231.161 |
| Australia Southeast | 13.73.115.153, 40.115.78.70, 40.115.78.237, 52.189.216.28 |
| Brazil South | 191.235.86.199, 191.235.95.229, 191.235.94.220, 191.234.166.198 |
| Brazil Southeast | 20.40.32.59, 20.40.32.162, 20.40.32.80, 20.40.32.49 |
| Canada Central | 13.88.249.209, 52.233.30.218, 52.233.29.79, 40.85.241.105 |
| Canada East | 52.232.129.143, 52.229.125.57, 52.232.133.109, 40.86.202.42 |
| Central India | 52.172.157.194, 52.172.184.192, 52.172.191.194, 104.211.73.195 |
| Central US | 13.67.236.76, 40.77.111.254, 40.77.31.87, 104.43.243.39 |
| East Asia | 168.63.200.173, 13.75.89.159, 23.97.68.172, 40.83.98.194 |
| East US | 137.135.106.54, 40.117.99.79, 40.117.100.228, 137.116.126.165 |
| East US 2 | 40.84.25.234, 40.79.44.7, 40.84.59.136, 40.70.27.253 |
| France Central | 52.143.162.83, 20.188.33.169, 52.143.156.55, 52.143.158.203 |
| France South | 52.136.131.145, 52.136.129.121, 52.136.130.89, 52.136.131.4 |
| Germany North | 51.116.211.29, 51.116.208.132, 51.116.208.37, 51.116.208.64 |
| Germany West Central | 51.116.168.222, 51.116.171.209, 51.116.233.40, 51.116.175.0 |
| Japan East | 13.71.146.140, 13.78.84.187, 13.78.62.130, 13.78.43.164 |
| Japan West | 40.74.140.173, 40.74.81.13, 40.74.85.215, 40.74.68.85 |
| Korea Central | 52.231.14.182, 52.231.103.142, 52.231.39.29, 52.231.14.42 |
| Korea South | 52.231.166.168, 52.231.163.55, 52.231.163.150, 52.231.192.64 |
| North Central US | 168.62.249.81, 157.56.12.202, 65.52.211.164, 65.52.9.64 |
| North Europe | 13.79.173.49, 52.169.218.253, 52.169.220.174, 40.112.90.39 |
| Norway East | 51.120.88.93, 51.13.66.86, 51.120.89.182, 51.120.88.77 |
| South Africa North | 102.133.228.4, 102.133.224.125, 102.133.226.199, 102.133.228.9 |
| South Africa West | 102.133.72.190, 102.133.72.145, 102.133.72.184, 102.133.72.173 |
| South Central US | 13.65.98.39, 13.84.41.46, 13.84.43.45, 40.84.138.132 |
| South India | 52.172.9.47, 52.172.49.43, 52.172.51.140, 104.211.225.152 |
| Southeast Asia | 52.163.93.214, 52.187.65.81, 52.187.65.155, 104.215.181.6 |
| Switzerland North | 51.103.128.52, 51.103.132.236, 51.103.134.138, 51.103.136.209 |
| Switzerland West | 51.107.225.180, 51.107.225.167, 51.107.225.163, 51.107.239.66 |
| UAE Central | 20.45.75.193, 20.45.64.29, 20.45.64.87, 20.45.71.213 |
| UAE North | 20.46.42.220, 40.123.224.227, 40.123.224.143, 20.46.46.173 |
| UK South | 51.140.79.109, 51.140.78.71, 51.140.84.39, 51.140.155.81 |
| UK West | 51.141.48.98, 51.141.51.145, 51.141.53.164, 51.141.119.150 |
| West Central US | 52.161.26.172, 52.161.8.128, 52.161.19.82, 13.78.137.247 |
| West Europe | 13.95.155.53, 52.174.54.218, 52.174.49.6 |
| West India | 104.211.164.112, 104.211.165.81, 104.211.164.25, 104.211.157.237 |
| West US | 52.160.90.237, 138.91.188.137, 13.91.252.184, 157.56.160.212 |
| West US 2 | 13.66.224.169, 52.183.30.10, 52.183.39.67, 13.66.128.68 |
|||

<a name="azure-government-inbound"></a>

#### Azure Government - Inbound IP addresses

| Azure Government region | IP |
|-------------------------|----|
| US Gov Arizona | 52.244.67.164, 52.244.67.64, 52.244.66.82 |
| US Gov Texas | 52.238.119.104, 52.238.112.96, 52.238.119.145 |
| US Gov Virginia | 52.227.159.157, 52.227.152.90, 23.97.4.36 |
| US DoD Central | 52.182.49.204, 52.182.52.106 |
|||

<a name="outbound"></a>

### Outbound IP addresses

This section lists the outbound IP addresses for the Azure Logic Apps service and managed connectors. If you have Azure Government, see [Azure Government - Outbound IP addresses](#azure-government-outbound).

> [!TIP]
> To help reduce complexity when you create security rules, you can optionally use the
> [service tag](../virtual-network/service-tags-overview.md), **LogicApps**, rather than 
> specify outbound Logic Apps IP address prefixes for each region. Optionally, you can 
> also use the **AzureConnectors** service tag for managed connectors that make outbound 
> calls to their respective services, such as Azure Storage or Azure Event Hubs, rather than 
> specify outbound managed connector IP address prefixes for each region. These tags work 
> across the regions where the Logic Apps service is available.

<a name="multi-tenant-outbound"></a>

#### Multi-tenant Azure - Outbound IP addresses

| Multi-tenant region | Logic Apps IP | Managed connectors IP |
|---------------------|---------------|-----------------------|
| Australia East | 13.75.149.4, 104.210.91.55, 104.210.90.241, 52.187.227.245, 52.187.226.96, 52.187.231.184, 52.187.229.130, 52.187.226.139 | 52.237.214.72, 13.72.243.10, 13.70.72.192 - 13.70.72.207, 13.70.78.224 - 13.70.78.255, 20.70.220.192 - 20.70.220.223, 20.70.220.224 - 20.70.220.239 |
| Australia Southeast | 13.73.114.207, 13.77.3.139, 13.70.159.205, 52.189.222.77, 13.77.56.167, 13.77.58.136, 52.189.214.42, 52.189.220.75 | 52.255.48.202, 13.70.136.174, 13.77.50.240 - 13.77.50.255, 13.77.55.160 - 13.77.55.191, 20.92.3.64 - 20.92.3.95, 20.92.3.96 - 20.92.3.111 |
| Brazil South | 191.235.82.221, 191.235.91.7, 191.234.182.26, 191.237.255.116, 191.234.161.168, 191.234.162.178, 191.234.161.28, 191.234.162.131 | 191.232.191.157, 104.41.59.51, 191.233.203.192 - 191.233.203.207, 191.233.207.160 - 191.233.207.191, 191.238.76.112 - 191.238.76.127, 191.238.76.128 - 191.238.76.159 |
| Brazil Southeast | 20.40.32.81, 20.40.32.19, 20.40.32.85, 20.40.32.60, 20.40.32.116, 20.40.32.87, 20.40.32.61, 20.40.32.113 | 23.97.120.109, 23.97.121.26, 20.206.0.0 - 20.206.0.63, 191.233.51.0 - 191.233.51.63 |
| Canada Central | 52.233.29.92, 52.228.39.244, 40.85.250.135, 40.85.250.212, 13.71.186.1, 40.85.252.47, 13.71.184.150 | 52.237.32.212, 52.237.24.126, 13.71.170.208 - 13.71.170.223, 13.71.175.160 - 13.71.175.191, 20.48.200.192 - 20.48.200.223, 20.48.200.224 - 20.48.200.239 |
| Canada East | 52.232.128.155, 52.229.120.45, 52.229.126.25, 40.86.203.228, 40.86.228.93, 40.86.216.241, 40.86.226.149, 40.86.217.241 | 52.242.30.112, 52.242.35.152, 40.69.106.240 - 40.69.106.255, 40.69.111.0 - 40.69.111.31, 52.139.111.0 - 52.139.111.31, 52.139.111.32 - 52.139.111.47 |
| Central India | 52.172.154.168, 52.172.186.159, 52.172.185.79, 104.211.101.108, 104.211.102.62, 104.211.90.169, 104.211.90.162, 104.211.74.145 | 52.172.212.129, 52.172.211.12, 20.43.123.0 - 20.43.123.31, 104.211.81.192 - 104.211.81.207, 20.192.168.64 - 20.192.168.95, 20.192.168.96 - 20.192.168.111 |
| Central US | 13.67.236.125, 104.208.25.27, 40.122.170.198, 40.113.218.230, 23.100.86.139, 23.100.87.24, 23.100.87.56, 23.100.82.16 | 52.173.241.27, 52.173.245.164, 13.89.171.80 - 13.89.171.95, 13.89.178.64 - 13.89.178.95, 40.77.68.110, 20.98.144.224 - 20.98.144.255, 20.98.145.0 - 20.98.145.15 |
| East Asia | 13.75.94.173, 40.83.127.19, 52.175.33.254, 40.83.73.39, 65.52.175.34, 40.83.77.208, 40.83.100.69, 40.83.75.165 | 13.75.110.131, 52.175.23.169, 13.75.36.64 - 13.75.36.79, 104.214.164.0 - 104.214.164.31, 20.205.67.48 - 20.205.67.63, 20.205.67.64 - 20.205.67.95, 104.214.165.128 - 104.214.165.191 |
| East US | 13.92.98.111, 40.121.91.41, 40.114.82.191, 23.101.139.153, 23.100.29.190, 23.101.136.201, 104.45.153.81, 23.101.132.208 | 40.71.249.139, 40.71.249.205, 40.114.40.132, 40.71.11.80 - 40.71.11.95, 40.71.15.160 - 40.71.15.191, 52.188.157.160, 20.88.153.176 - 20.88.153.191, 20.88.153.192 - 20.88.153.223 |
| East US 2 | 40.84.30.147, 104.208.155.200, 104.208.158.174, 104.208.140.40, 40.70.131.151, 40.70.29.214, 40.70.26.154, 40.70.27.236 | 52.225.129.144, 52.232.188.154, 104.209.247.23, 40.70.146.208 - 40.70.146.223, 40.70.151.96 - 40.70.151.127, 40.65.220.25, 20.98.192.80 - 20.98.192.95, 20.98.192.96 - 20.98.192.127 |
| France Central | 52.143.164.80, 52.143.164.15, 40.89.186.30, 20.188.39.105, 40.89.191.161, 40.89.188.169, 40.89.186.28, 40.89.190.104 | 40.89.186.239, 40.89.135.2, 40.79.130.208 - 40.79.130.223, 40.79.148.96 - 40.79.148.127, 51.138.215.48 - 51.138.215.63, 51.138.215.64 - 51.138.215.95 |
| France South | 52.136.132.40, 52.136.129.89, 52.136.131.155, 52.136.133.62, 52.136.139.225, 52.136.130.144, 52.136.140.226, 52.136.129.51 | 52.136.142.154, 52.136.133.184, 40.79.178.240 - 40.79.178.255, 40.79.180.224 - 40.79.180.255, 52.136.189.16 - 52.136.189.31, 52.136.189.32 - 52.136.189.63 |
| Germany North | 51.116.211.168, 51.116.208.165, 51.116.208.175, 51.116.208.192, 51.116.208.200, 51.116.208.222, 51.116.208.217, 51.116.208.51 | 51.116.60.192, 51.116.211.212, 51.116.59.16 - 51.116.59.31, 51.116.60.192 - 51.116.60.223, 51.116.55.240 - 51.116.55.255, 51.116.74.32 - 51.116.74.63 |
| Germany West Central | 51.116.233.35, 51.116.171.49, 51.116.233.33, 51.116.233.22, 51.116.168.104, 51.116.175.17, 51.116.233.87, 51.116.175.51 | 51.116.158.97, 51.116.236.78, 51.116.155.80 - 51.116.155.95, 51.116.158.96 - 51.116.158.127, 20.52.93.80 - 20.52.93.95, 20.52.93.96 - 20.52.93.127 |
| Japan East | 13.71.158.3, 13.73.4.207, 13.71.158.120, 13.78.18.168, 13.78.35.229, 13.78.42.223, 13.78.21.155, 13.78.20.232 | 13.73.21.230, 13.71.153.19, 13.78.108.0 - 13.78.108.15, 40.79.189.64 - 40.79.189.95, 20.89.11.48 - 20.89.11.63, 20.89.11.64 - 20.89.11.95 |
| Japan West | 40.74.140.4, 104.214.137.243, 138.91.26.45, 40.74.64.207, 40.74.76.213, 40.74.77.205, 40.74.74.21, 40.74.68.85 | 104.215.27.24, 104.215.61.248, 40.74.100.224 - 40.74.100.239, 40.80.180.64 - 40.80.180.95, 20.189.192.144 - 20.189.192.159, 20.189.192.160 - 20.189.192.191 |
| Korea Central | 52.231.14.11, 52.231.14.219, 52.231.15.6, 52.231.10.111, 52.231.14.223, 52.231.77.107, 52.231.8.175, 52.231.9.39 | 52.141.1.104, 52.141.36.214, 20.44.29.64 - 20.44.29.95, 52.231.18.208 - 52.231.18.223, 20.200.194.160 - 20.200.194.191, 20.200.194.192 - 20.200.194.207 |
| Korea South | 52.231.204.74, 52.231.188.115, 52.231.189.221, 52.231.203.118, 52.231.166.28, 52.231.153.89, 52.231.155.206, 52.231.164.23 | 52.231.201.173, 52.231.163.10, 52.231.147.0 - 52.231.147.15, 52.231.148.224 - 52.231.148.255, 52.147.117.32 - 52.147.117.63, 52.147.117.64 - 52.147.117.79 |
| North Central US | 168.62.248.37, 157.55.210.61, 157.55.212.238, 52.162.208.216, 52.162.213.231, 65.52.10.183, 65.52.9.96, 65.52.8.225 | 52.162.126.4, 52.162.242.161, 52.162.107.160 - 52.162.107.175, 52.162.111.192 - 52.162.111.223, 20.51.4.192 - 20.51.4.223, 20.51.4.224 - 20.51.4.239 |
| North Europe | 40.113.12.95, 52.178.165.215, 52.178.166.21, 40.112.92.104, 40.112.95.216, 40.113.4.18, 40.113.3.202, 40.113.1.181 | 52.169.28.181, 52.178.150.68, 94.245.91.93, 13.69.227.208 - 13.69.227.223, 13.69.231.192 - 13.69.231.223, 40.115.108.29, 20.82.246.112 - 20.82.246.127, 52.146.138.32 - 52.146.138.63 |
| Norway East | 51.120.88.52, 51.120.88.51, 51.13.65.206, 51.13.66.248, 51.13.65.90, 51.13.65.63, 51.13.68.140, 51.120.91.248 | 51.120.100.192, 51.120.92.27, 51.120.98.224 - 51.120.98.239, 51.120.100.192 - 51.120.100.223, 20.100.0.96 - 20.100.0.127, 20.100.0.128 - 20.100.0.143 |
| South Africa North | 102.133.231.188, 102.133.231.117, 102.133.230.4, 102.133.227.103, 102.133.228.6, 102.133.230.82, 102.133.231.9, 102.133.231.51 | 102.133.168.167, 40.127.2.94, 102.133.155.0 - 102.133.155.15, 102.133.253.0 - 102.133.253.31, 102.37.166.80 - 102.37.166.95, 102.37.166.96 - 102.37.166.127 |
| South Africa West | 102.133.72.98, 102.133.72.113, 102.133.75.169, 102.133.72.179, 102.133.72.37, 102.133.72.183, 102.133.72.132, 102.133.75.191 | 102.133.72.85, 102.133.75.194, 102.37.64.0 - 102.37.64.31, 102.133.27.0 - 102.133.27.15, 102.37.84.128 - 102.37.84.159, 102.37.84.160 - 102.37.84.175 |
| South Central US | 104.210.144.48, 13.65.82.17, 13.66.52.232, 23.100.124.84, 70.37.54.122, 70.37.50.6, 23.100.127.172, 23.101.183.225 | 52.171.130.92, 13.65.86.57, 13.73.244.224 - 13.73.244.255, 104.214.19.48 - 104.214.19.63, 20.97.33.48 - 20.97.33.63, 20.97.33.64 - 20.97.33.95, 104.214.70.191 |
| South India | 52.172.50.24, 52.172.55.231, 52.172.52.0, 104.211.229.115, 104.211.230.129, 104.211.230.126, 104.211.231.39, 104.211.227.229 | 13.71.127.26, 13.71.125.22, 20.192.184.32 - 20.192.184.63, 40.78.194.240 - 40.78.194.255, 20.192.152.64 - 20.192.152.95, 20.192.152.96 - 20.192.152.111, 52.172.80.0 - 52.172.80.63 |
| Southeast Asia | 13.76.133.155, 52.163.228.93, 52.163.230.166, 13.76.4.194, 13.67.110.109, 13.67.91.135, 13.76.5.96, 13.67.107.128 | 52.187.115.69, 52.187.68.19, 13.67.8.240 - 13.67.8.255, 13.67.15.32 - 13.67.15.63, 20.195.82.240 - 20.195.82.255, 20.195.83.0 - 20.195.83.31 |
| Switzerland North | 51.103.137.79, 51.103.135.51, 51.103.139.122, 51.103.134.69, 51.103.138.96, 51.103.138.28, 51.103.136.37, 51.103.136.210 | 51.103.142.22, 51.107.86.217, 51.107.59.16 - 51.107.59.31, 51.107.60.224 - 51.107.60.255, 51.107.246.112 - 51.107.246.127, 51.107.246.128 - 51.107.246.159 |
| Switzerland West | 51.107.239.66, 51.107.231.86, 51.107.239.112, 51.107.239.123, 51.107.225.190, 51.107.225.179, 51.107.225.186, 51.107.225.151, 51.107.239.83 | 51.107.156.224, 51.107.231.190, 51.107.155.16 - 51.107.155.31, 51.107.156.224 - 51.107.156.255, 51.107.254.32 - 51.107.254.63, 51.107.254.64 - 51.107.254.79 |
| UAE Central | 20.45.75.200, 20.45.72.72, 20.45.75.236, 20.45.79.239, 20.45.67.170, 20.45.72.54, 20.45.67.134, 20.45.67.135 | 20.45.67.45, 20.45.67.28, 20.37.74.192 - 20.37.74.207, 40.120.8.0 - 40.120.8.31, 20.45.90.208 - 20.45.90.223, 20.45.90.224 - 20.45.90.255 |
| UAE North | 40.123.230.45, 40.123.231.179, 40.123.231.186, 40.119.166.152, 40.123.228.182, 40.123.217.165, 40.123.216.73, 40.123.212.104 | 65.52.250.208, 40.123.224.120, 40.120.64.64 - 40.120.64.95, 65.52.250.208 - 65.52.250.223, 40.120.86.16 - 40.120.86.31, 40.120.86.32 - 40.120.86.63 |
| UK South | 51.140.74.14, 51.140.73.85, 51.140.78.44, 51.140.137.190, 51.140.153.135, 51.140.28.225, 51.140.142.28, 51.140.158.24 | 51.140.74.150, 51.140.80.51, 51.140.61.124, 51.105.77.96 - 51.105.77.127, 51.140.148.0 - 51.140.148.15, 20.90.129.0 - 20.90.129.31, 20.90.129.32 - 20.90.129.47 |
| UK West | 51.141.54.185, 51.141.45.238, 51.141.47.136, 51.141.114.77, 51.141.112.112, 51.141.113.36, 51.141.118.119, 51.141.119.63 | 51.141.52.185, 51.141.47.105, 51.141.124.13, 51.140.211.0 - 51.140.211.15, 51.140.212.224 - 51.140.212.255, 20.58.70.192 - 20.58.70.223, 20.58.70.224 - 20.58.70.239 |
| West Central US | 52.161.27.190, 52.161.18.218, 52.161.9.108, 13.78.151.161, 13.78.137.179, 13.78.148.140, 13.78.129.20, 13.78.141.75 | 52.161.101.204, 52.161.102.22, 13.78.132.82, 13.71.195.32 - 13.71.195.47, 13.71.199.192 - 13.71.199.223, 20.69.4.0 - 20.69.4.31, 20.69.4.32 - 20.69.4.47 |
| West Europe | 40.68.222.65, 40.68.209.23, 13.95.147.65, 23.97.218.130, 51.144.182.201, 23.97.211.179, 104.45.9.52, 23.97.210.126, 13.69.71.160, 13.69.71.161, 13.69.71.162, 13.69.71.163, 13.69.71.164, 13.69.71.165, 13.69.71.166, 13.69.71.167 | 52.166.78.89, 52.174.88.118, 40.91.208.65, 13.69.64.208 - 13.69.64.223, 13.69.71.192 - 13.69.71.223, 13.93.36.78, 20.86.93.32 - 20.86.93.63, 20.86.93.64 - 20.86.93.79 |
| West India | 104.211.164.80, 104.211.162.205, 104.211.164.136, 104.211.158.127, 104.211.156.153, 104.211.158.123, 104.211.154.59, 104.211.154.7 | 104.211.189.124, 104.211.189.218, 20.38.128.224 - 20.38.128.255, 104.211.146.224 - 104.211.146.239, 20.192.82.48 - 20.192.82.63, 20.192.82.64 - 20.192.82.95 |
| West US | 52.160.92.112, 40.118.244.241, 40.118.241.243, 157.56.162.53, 157.56.167.147, 104.42.49.145, 40.83.164.80, 104.42.38.32, 13.86.223.0, 13.86.223.1, 13.86.223.2, 13.86.223.3, 13.86.223.4, 13.86.223.5 | 13.93.148.62, 104.42.122.49, 40.112.195.87, 13.86.223.32 - 13.86.223.63, 40.112.243.160 - 40.112.243.175, 20.59.77.0 - 20.59.77.31, 20.66.6.112 - 20.66.6.127 |
| West US 2 | 13.66.210.167, 52.183.30.169, 52.183.29.132, 13.66.210.167, 13.66.201.169, 13.77.149.159, 52.175.198.132, 13.66.246.219 | 52.191.164.250, 52.183.78.157, 13.66.140.128 - 13.66.140.143, 13.66.145.96 - 13.66.145.127, 13.66.164.219, 20.83.220.208 - 20.83.220.223, 20.83.220.224 - 20.83.220.255 |
||||

<a name="azure-government-outbound"></a>

#### Azure Government - Outbound IP addresses

| Region | Logic Apps IP | Managed connectors IP |
|--------|---------------|-----------------------|
| US DoD Central | 52.182.48.215, 52.182.92.143 | 52.127.58.160 - 52.127.58.175, 52.182.54.8, 52.182.48.136, 52.127.61.192 - 52.127.61.223, 52.245.153.80 - 52.245.153.95, 52.245.153.96 - 52.245.153.127 |
| US Gov Arizona | 52.244.67.143, 52.244.65.66, 52.244.65.190 | 52.127.2.160 - 52.127.2.175, 52.244.69.0, 52.244.64.91, 52.127.5.224 - 52.127.5.255, 20.141.9.240 - 20.141.9.255, 20.141.10.0 - 20.141.10.31 |
| US Gov Texas | 52.238.114.217, 52.238.115.245, 52.238.117.119 | 52.127.34.160 - 52.127.34.175, 40.112.40.25, 52.238.161.225, 20.140.137.128 - 20.140.137.159, 20.140.146.192 - 20.140.146.223, 20.140.146.224 - 20.140.146.239 |
| US Gov Virginia | 13.72.54.205, 52.227.138.30, 52.227.152.44 | 52.127.42.128 - 52.127.42.143, 52.227.143.61, 52.227.162.91, 20.140.94.192 - 20.140.94.223, 52.235.252.144 - 52.235.252.159, 52.235.252.160 - 52.235.252.191 |
||||

## Next steps

* Learn how to [create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* Learn about [common examples and scenarios](../logic-apps/logic-apps-examples-and-scenarios.md)
