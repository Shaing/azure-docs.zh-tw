---
title: 使用 Resource Manager 範本在 Azure 中建立診斷設定
description: 使用 Resource Manager 範本來建立診斷設定，以將 Azure 平臺記錄轉送至 Azure 監視器記錄、Azure 儲存體或 Azure 事件中樞。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 2a171ae89e8314684eddf29f78b9b09bc52f9c9b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977563"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>使用 Resource Manager 範本在 Azure 中建立診斷設定
Azure 監視器中的[診斷設定](diagnostic-settings.md)會指定 azure 資源所收集的[平臺記錄](platform-logs-overview.md)檔，以及它們所依賴的 azure 平臺。 本文提供詳細資料和範例，說明如何使用[Azure Resource Manager 範本](../../azure-resource-manager/templates/template-syntax.md)來建立和設定診斷設定，以將平臺記錄檔收集到不同的目的地。

> [!NOTE]
> 因為您無法使用 PowerShell 或 CLI （例如其他 Azure 資源的診斷設定）來建立 Azure 活動記錄的[診斷設定](diagnostic-settings.md)，請使用本文中的資訊建立活動記錄的 Resource Manager 範本，並使用 POWERSHELL 或 CLI 來部署範本。

## <a name="deployment-methods"></a>部署方法
您可以使用任何有效的方法（包括 PowerShell 和 CLI）來部署 Resource Manager 範本。 活動記錄的診斷設定必須使用適用于 CLI 的 `az deployment create` 或適用于 PowerShell 的 `New-AzDeployment`，部署至訂用帳戶。 資源記錄的診斷設定必須使用適用于 CLI 的 `az group deployment create` 或適用于 PowerShell 的 `New-AzResourceGroupDeployment`，部署到資源群組。

如需詳細資訊，請參閱[使用 Resource Manager 範本部署資源和 Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)和[使用 Resource Manager 範本部署資源和 Azure CLI](../../azure-resource-manager/templates/deploy-cli.md) 。 





## <a name="resource-logs"></a>資源記錄
針對資源記錄，請將 `<resource namespace>/providers/diagnosticSettings` 類型的資源新增至範本。 [屬性] 區段會遵循 [[診斷設定-建立或更新](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)] 中所述的格式。 針對您想要收集的資源，在 [`logs`] 區段中提供 `category`。 新增 `metrics` 屬性，以在[資源支援計量](metrics-supported.md)時，將資源計量收集到相同的目的地。

以下範本會將特定資源的資源記錄類別收集到 Log Analytics 工作區、儲存體帳戶和事件中樞。

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>範例
以下範例會建立自動調整設定的診斷設定，以啟用將資源記錄串流至事件中樞、儲存體帳戶和 Log Analytics 工作區。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>活動記錄
針對 [Azure 活動記錄檔]，新增 `Microsoft.Insights/diagnosticSettings`類型的資源。 可用的類別會列在[[活動記錄](activity-log-view.md#categories-in-the-activity-log)] 的 [類別] 中。 以下範本會將所有活動記錄類別收集到 Log Analytics 工作區、儲存體帳戶和事件中樞。


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>後續步驟
* 深入瞭解[Azure 中的平臺記錄](platform-logs-overview.md)。
* 深入瞭解[診斷設定](diagnostic-settings.md)。
