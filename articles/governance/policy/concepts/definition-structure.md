---
title: 原則定義結構的詳細資料
description: 說明如何使用原則定義來建立組織中 Azure 資源的慣例。
ms.date: 11/26/2019
ms.topic: conceptual
ms.openlocfilehash: e37ff6e1bde594014510880492c2572ad1634400
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904423"
---
# <a name="azure-policy-definition-structure"></a>Azure 原則定義結構

「Azure 原則」會使用資源原則定義為資源建立慣例。 每個定義都會描述資源合規性，以及當資源不符合規範時應套用的效果。
藉由定義慣例，您可以控制成本以及更輕鬆地管理您的資源。 例如，您可以指定僅允許特定類型的虛擬機器。 或者，您可以要求所有資源都有特定標籤。 原則會由所有子資源繼承。 如果將某個原則套用至資源群組，它會適用於該資源群組中的所有資源。

您可在這裡找到原則定義架構： [https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-06-01/policyDefinition.json)

使用 JSON 來建立原則定義。 原則定義中包含以下的項目︰

- mode
- 參數
- 顯示名稱
- description
- 原則規則
  - 邏輯評估
  - 效果

例如，下列 JSON 示範一個會限制資源部署位置的原則：

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

所有 Azure 原則範例都位於[Azure 原則的範例](../samples/index.md)。

## <a name="mode"></a>[模式]

根據原則是以 Azure Resource Manager 屬性或資源提供者屬性為目標，設定**模式**。

### <a name="resource-manager-modes"></a>Resource Manager 模式

**Mode** 決定原則要評估哪些資源類型。 支援的模式如下：

- `all`：評估資源群組和所有資源類型
- `indexed`：只評估支援標記和位置的資源類型

我們建議您在大部分的情況下都將 **mode** 設定為 `all`。 透過入口網站使用 `all` 模式建立的所有原則定義。 如果您是使用 PowerShell 或 Azure CLI，則可手動指定 **mode** 參數。 如果原則定義未包含 **mode** 值，則在 Azure PowerShell 中會預設為 `all`，而在 Azure CLI 中會預設為 `null`。 `null` 模式與使用 `indexed` 來支援回溯相容性相同。

建立會強制執行標籤或位置的原則時，應該使用 `indexed`。 雖然並非必要，但它可防止不支援標籤和位置的資源在合規性結果中顯示為不符合規範。 有一個例外，就是**資源群組**。 原則如果會在資源群組上強制執行位置或標籤，就應該將 **mode** 設定為 `all`，並明確地以 `Microsoft.Resources/subscriptions/resourceGroups` 類型作為目標。 如需範例，請參閱[強制執行資源群組標籤](../samples/enforce-tag-rg.md)。 如需支援標記的資源清單，請參閱[Azure 資源的標記支援](../../../azure-resource-manager/management/tag-support.md)。

### <a name="a-nameresource-provider-modes-resource-provider-modes-preview"></a><a name="resource-provider-modes" />資源提供者模式（預覽）

預覽期間目前支援下列資源提供者模式：

- `Microsoft.ContainerService.Data`，用於管理[Azure Kubernetes Service](../../../aks/intro-kubernetes.md)上的許可控制站規則。 使用此資源提供者模式的原則**必須**使用[EnforceRegoPolicy](./effects.md#enforceregopolicy)效果。
- `Microsoft.Kubernetes.Data`，用於管理 Azure 上的自我管理 AKS 引擎 Kubernetes 叢集。
  使用此資源提供者模式的原則**必須**使用[EnforceOPAConstraint](./effects.md#enforceopaconstraint)效果。
- `Microsoft.KeyVault.Data`，用於管理[Azure Key Vault](../../../key-vault/key-vault-overview.md)中的保存庫和憑證。

> [!NOTE]
> 資源提供者模式只支援內建原則定義，並在預覽期間不支援方案。

## <a name="parameters"></a>參數

參數可減少原則定義數量來幫助您簡化原則管理。 將參數想像成就像表單上的欄位一樣 – `name``address`、`city``state`。 這些參數一律保持不變，不過它們的值則會根據填寫表單的個人而有所變更。
在建立原則時，參數也是以相同的方式運作。 藉由在原則定義中納入參數，您便可以針對不同的案例使用不同的值，來重複使用該原則。

> [!NOTE]
> 參數可能會加入至現有的和指派的定義。 新的參數必須包含 **defaultValue** 屬性。 這可避免原則或計畫的現有指派間接地變成無效。

### <a name="parameter-properties"></a>參數屬性

參數有下列在原則定義中使用的屬性：

- **名稱**：參數的名稱。 由原則規則中的 `parameters` 部署函式使用。 如需詳細資訊，請參閱[使用參數值](#using-a-parameter-value)。
- `type`：判斷參數是否為**字串**、**陣列**、**物件**、**布林值**、**整數**、**浮點數**或**datetime**。
- `metadata`：定義主要由 Azure 入口網站用來顯示使用者易記資訊的子屬性：
  - `description`：參數用途的說明。 能用來提供可接受值的範例。
  - `displayName`：在入口網站中為參數顯示的易記名稱。
  - `strongType`：（選擇性）透過入口網站指派原則定義時使用。 提供內容感知清單。 如需詳細資訊，請參閱 [strongType](#strongtype)。
  - `assignPermissions`：（選擇性）設定為_true_ ，讓 Azure 入口網站在原則指派期間建立角色指派。 如果您想要在指派範圍之外指派許可權，此屬性會很有用。 原則中的每個角色定義（或計畫中的所有原則中的每個角色定義）都有一個角色指派。 參數值必須是有效的資源或範圍。
- `defaultValue`：（選擇性）如果未指定任何值，則會在指派中設定參數的值。
  更新已指派的現有原則定義時需要。
- `allowedValues`：（選擇性）提供值的陣列，此參數會在指派期間接受。

舉例來說，您可以定義一個原則定義來限制可部署資源的位置。 該原則定義的參數可為 **allowedLocations**。 原則定義的每個指派都會使用此參數來限制接受的值。 透過入口網站完成指派時，**strongType** 提供增強的體驗：

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>使用參數值

在原則規則中，您可以使用下列 `parameters` 函數語法來參考參數：

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

此範例參考[參數屬性](#parameter-properties)中示範的 **allowedLocations** 參數。

### <a name="strongtype"></a>strongType

在 `metadata` 屬性內，您可以使用 **strongType** 在 Azure 入口網站內提供可複選的選項清單。 **strongType** 所允許的值目前包括：

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>定義位置

建立方案或原則時，必須指定定義位置。 定義位置必須是管理群組或訂用帳戶。 此位置可決定方案或原則的指派範圍。 資源必須是定義位置階層內的直屬成員或其子系，才可作為指派的目標。

如果定義位置為：

- **訂用帳戶** - 只能將原則指派給該訂用帳戶內的資源。
- **管理群組** - 只能將原則指派給子管理群組與子訂用帳戶內的資源。 如果您打算將原則定義套用至數個訂用帳戶，則位置必須是包含那些訂用帳戶的管理群組。

## <a name="display-name-and-description"></a>顯示名稱和描述

您可以使用 **displayName** 和 **description** 來識別原則定義，以及提供其使用時機的內容。 **displayName** 的長度上限為 _128_ 個字元，**description** 的長度上限則為 _512_ 個字元。

> [!NOTE]
> 在建立或更新原則定義時，**識別碼**、**類型**和**名稱**是由 json 外部的屬性所定義，而且在 json 檔案中並不需要。 透過 SDK 提取原則定義會傳回**識別碼**、**類型**和**名稱**屬性做為 JSON 的一部分，但每個都是與原則定義相關的唯讀資訊。

## <a name="policy-rule"></a>原則規則

原則規則包含 **If** 和 **Then**區塊。 在 **If** 區塊中，您可以定義一個或多個指定強制執行此原則時間的條件。 您可以將邏輯運算子套用至這些條件，以精確地定義原則的案例。

在 **Then** 區塊中，定義當 **If** 條件履行時所發生的效果。

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>邏輯運算子

支援的邏輯運算子包括︰

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

**not** 語法會反轉條件的結果。 **allOf** 語法 (類似於邏輯**And** 作業) 需要所有的條件為 true。 **anyOf** 語法 (類似於邏輯**Or** 作業) 需要一或多個條件為 true。

您可以巢狀邏輯運算子。 下列範例顯示 **allOf** 作業中的巢狀 **not** 作業。

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>條件

條件會評估 **field** 或 **value** 存取子是否符合特定條件。 支援的條件如下︰

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

使用 **like** 和 **notLike** 條件時，您可以在值中提供 `*` 萬用字元。
值不應包含多個 `*` 萬用字元。

當使用**match**和**notMatch**條件時，請提供 `#` 來比對數位、為字母 `?`、`.` 比對任何字元，以及任何其他字元以符合該實際字元。
**match** 和 **notMatch** 都會區分大小寫。 不會區分大小寫的替代項目，可在 **matchInsensitively** 和 **notMatchInsensitively** 中取得。 如需範例，請參閱[允許數個名稱模式](../samples/allow-multiple-name-patterns.md)。

### <a name="fields"></a>欄位

條件是透過欄位所形成。 欄位會比對資源要求裝載中的屬性，並描述資源的狀態。

支援下列欄位：

- `name`
- `fullName`
  - 傳回資源的完整名稱。 資源的完整名稱是資源名稱前面加上任何父系資源名稱 (例如 "myServer/myDatabase")。
- `kind`
- `type`
- `location`
  - 針對不受特定位置限制的資源，請使用 **global**。 如需範例，請參閱[範例 - 允許的位置](../samples/allowed-locations.md)。
- `identity.type`
  - 傳回資源上所啟用[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)的類型。
- `tags`
- `tags['<tagName>']`
  - 此括號語法支援具有連字號、句號或空格等標點符號的標籤名稱。
  - 其中 **\<tagName\>** 是要接受條件驗證的標籤名稱。
  - 範例：`tags['Acct.CostCenter']`，其中 **Acct.CostCenter** 是標籤的名稱。
- `tags['''<tagName>''']`
  - 此括號語法能透過以雙引號進行逸出，來支援具有單引號的標籤名稱。
  - 其中 **'\<tagName\>'** 是要接受條件驗證的標籤名稱。
  - 範例： `tags['''My.Apostrophe.Tag''']`，其中 **' My. 單引號. tag '** 是標記的名稱。
- 屬性別名 - 如需清單，請參閱[別名](#aliases)。

> [!NOTE]
> `tags.<tagName>`、`tags[tagName]` 和 `tags[tag.with.dots]` 都仍是可接受的宣告標籤欄位方式。 不過，建議的運算式為上面所列的運算式。

#### <a name="use-tags-with-parameters"></a>搭配參數使用標籤

可以將參數值傳遞到標籤欄位。 將參數傳遞到標籤欄位能在原則指派期間提升原則定義的彈性。

在下列範例中，`concat` 被用來建立針對名稱為 **tagName** 參數值之標籤的標籤欄位查閱。 如果該標籤不存在，則會使用**修改**效果，透過使用 `resourcegroup()` lookup 函式，在已審核資源的父資源群組上使用相同的命名標記設定值來新增標記。

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>值

條件也可以使用 **value** 形成。 **value** 會檢查 [parameters](#parameters)、[支援的範本函式](#policy-functions)或常值的條件。
**value** 已和任何支援的 [condition](#conditions) 配對。

> [!WARNING]
> 如果_範本_函式的結果是錯誤，原則評估會失敗。 失敗的評估是隱含的**拒絕**。 如需詳細資訊，請參閱[避免範本失敗](#avoiding-template-failures)。

#### <a name="value-examples"></a>Value 範例

此原則規則範例使用 **value** 來將 `resourceGroup()` 函式和傳回的 **name** 屬性與 `*netrg` 的 **like** 條件比較。 此規則會拒絕名稱結尾為 `*netrg`的任何資源群組中不屬於 `Microsoft.Network/*`**類型**的任何資源。

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

此原則規則範例會使用**值**來檢查多個嵌套函式的結果是否**等於**`true`。 規則會拒絕沒有至少三個標籤的任何資源。

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>避免範本失敗

在**值**中_使用_樣板函式，可允許許多複雜的嵌套函數。 如果_範本_函式的結果是錯誤，原則評估會失敗。 失敗的評估是隱含的**拒絕**。 在某些情況下失敗的**值**範例：

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

上述範例原則規則使用[substring （）](../../../azure-resource-manager/templates/template-functions-string.md#substring)來比較**名稱**的前三個字元和**abc**。 如果**name**少於3個字元，`substring()` 函式會導致錯誤。 此錯誤會導致原則變成**拒絕**效果。

相反地，請使用[if （）](../../../azure-resource-manager/templates/template-functions-logical.md#if)函式來檢查**name**的前三個字元是否等於**abc** ，而不允許少於3個字元的**名稱**導致錯誤：

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

使用修改過的原則規則，`if()` 在嘗試取得值少於三個字元的 `substring()` 之前，檢查**名稱**的長度。 如果**名稱**太短，則會改為傳回值「不是以 abc 開頭」，並與**abc**比較。 簡短名稱不是**abc**開頭的資源仍會失敗原則規則，但在評估期間不會再造成錯誤。

### <a name="count"></a>計數

計算資源裝載中陣列成員數目符合條件運算式的條件，可以使用**計數**運算式來形成。 常見的案例是檢查「至少其中一個」、「全部」、「全部」或「無」陣列成員是否符合條件。 **count**會評估條件運算式的每個陣列成員，並加總_true_結果，然後再與運算式運算子進行比較。

**計數**運算式的結構為：

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

下列屬性會與**count**搭配使用：

- **count. field** （必要）：包含陣列的路徑，而且必須是陣列別名。 如果陣列遺失，運算式會評估為_false_ ，而不考慮條件運算式。
- **count。 where** （選擇性）： [\*\] alias](#understanding-the--alias)陣列成員的**count. 欄位**中，個別評估每個\[的條件運算式。 如果未提供此屬性，則會將路徑為 ' field ' 的所有陣列成員評估為_true_。 任何[條件](../concepts/definition-structure.md#conditions)都可以在此屬性內使用。
  [邏輯運算子](#logical-operators)可以在此屬性內使用，以建立複雜的評估需求。
- **\<條件\>** （必要）：值會與符合計數的專案數進行比較（ **where**條件運算式）。 應該使用數值[條件](../concepts/definition-structure.md#conditions)。

#### <a name="count-examples"></a>計數範例

範例1：檢查陣列是否為空的

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

範例2：只檢查一個陣列成員，以符合條件運算式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

範例3：檢查是否至少有一個陣列成員符合條件運算式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

範例4：檢查所有物件陣列成員是否符合條件運算式

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

範例5：檢查所有字串陣列成員是否符合條件運算式

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
            "like": "*@contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

範例6：在**值**中使用**欄位**，以檢查所有陣列成員是否符合條件運算式

```json
{
    "count": {
        "field": "Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]",
        "where": {
            "value": "[last(split(first(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]')), '@'))]",
            "equals": "contoso.com"
        }
    },
    "equals": "[length(field('Microsoft.Sql/servers/securityAlertPolicies/emailAddresses[*]'))]"
}
```

範例7：檢查至少有一個陣列成員符合條件運算式中的多個屬性

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>影響

Azure 原則支援下列類型的效果：

- **Append**：會在要求中加入一組已定義的欄位
- **Audit**：會在活動記錄中產生警告事件，但不會讓要求失敗
- **AuditIfNotExists**：如果相關資源不存在，則會在活動記錄中產生警告事件
- **Deny**：會在活動記錄中產生事件，並讓要求失敗
- **DeployIfNotExists**：部署相關資源（如果尚未存在）
- **Disabled**：不會評估資源是否符合原則規則的規範
- **EnforceOPAConstraint** （預覽）：針對 Azure 上的自我管理 Kubernetes 叢集，設定開啟原則代理程式許可控制器與閘道管理員 v3 （預覽）
- **EnforceRegoPolicy** （預覽）：在 Azure Kubernetes Service 中設定開啟的原則代理程式許可控制器與閘道管理員 v2
- **修改**：新增、更新或移除資源中已定義的標記

如需每個效果、評估順序、屬性和範例的完整詳細資料，請參閱[瞭解 Azure 原則效果](effects.md)。

### <a name="policy-functions"></a>原則函式

除了下列函數和使用者定義函數之外，所有[Resource Manager 範本](../../../azure-resource-manager/templates/template-functions.md)函式都可在原則規則中使用：

- copyIndex()
- deployment()
- list*
- newGuid （）
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

下列函式可在原則規則中使用，但與 Azure Resource Manager 範本中的用法不同：

- addDays （dateTime，numberOfDaysToAdd）
  - **datetime**： [Required] 通用 ISO 8601 dateTime 格式 ' Yyyy-mm-dd ' ddTHH： MM： Ss. ss.fffffffz ' 中的 string 字串
  - **numberOfDaysToAdd**： [必要] 整數-要加入的天數
- utcNow （）-與 Resource Manager 範本不同的是，這可以在 defaultValue 以外使用。
  - 傳回設定為目前日期和時間的字串，其為通用 ISO 8601 DateTime 格式 ' yyyy-mm-dd ' ddTHH： MM： ss. Ss.fffffffz '

此外，`field` 函式可用於原則規則。 `field` 主要是與 **AuditIfNotExists** 和 **DeployIfNotExists** 搭配使用，以參考所評估資源上的欄位。 如需此用法的範例，請參閱 [DeployIfNotExists 範例](effects.md#deployifnotexists-example)。

#### <a name="policy-function-example"></a>原則函式範例

此原則規則範例會使用 `resourceGroup` 資源函式來取得**名稱**屬性，並與 `concat` 陣列和物件函式結合來建置 `like` 條件，以強制資源名稱的開頭使用資源群組名稱。

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>別名

您可以使用屬性別名來存取資源類型的特定屬性。 別名可讓您針對資源上的屬性限制所允許的值或條件。 每個別名會對應至指定資源類型之不同 API 版本中的路徑。 在原則評估期間，原則引擎會取得該 API 版本的屬性路徑。

別名清單會不斷成長。 若要了解「Azure 原則」目前支援哪些別名，請使用下列其中一種方法：

- Visual Studio Code 的 Azure 原則延伸模組（建議）

  使用[Visual Studio Code 的 Azure 原則延伸](../how-to/extension-for-vscode.md)模組來查看和探索資源屬性的別名。

  ![Visual Studio Code 的 Azure 原則延伸模組](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

- Azure 資源圖表

  使用 [`project`] 運算子來顯示資源的**別名**。

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST API/ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>了解 [*] 別名

其中有幾個可用的別名，其版本會顯示為「一般」名稱，另一個則會附加 **\[\*\]** 。 例如：

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

' Normal ' 別名會將欄位表示為單一值。 此欄位適用于完全相符的比較案例，因為整個值集合必須完全依照定義，而不是更多或更少。

**\[\*\]** 別名可以比較陣列中每個元素的值，以及每個專案的特定屬性。 這個方法可讓您比較「如果沒有 '、' （如果有的話）」和「if 所有」案例的元素屬性。 如需更複雜的案例，請使用 [[計數](#count)條件] 運算式。 使用**ipRules\[\*\]** ，範例會驗證每個_動作_是否為「_拒絕_」，但不會擔心有多少規則存在或 IP_值_為何。
此範例規則會檢查是否有任何相符的**ipRules\[\*\]值** **，而且只有**在找不到至少一個相符專案時，才會套用**effectType** ：

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```



如需詳細資訊，請參閱[評估 [\*] 別名](../how-to/author-policies-for-arrays.md#evaluating-the--alias)。

## <a name="initiatives"></a>計畫

計畫可讓您將數個相關的原則定義組成群組來簡化指派和管理，因為您可以將一個群組當作單一項目來使用。 例如，您可以將相關的標籤原則定義組成單一方案。 您可以套用該計畫，而不個別指派每個原則。

下列範例說明如何建立一個處理兩個標籤 (`costCenter` 和 `productName`) 的計畫。 它會使用兩個內建的原則來套用預設標籤值。

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>後續步驟

- 如[Azure 原則範例](../samples/index.md)，請參閱範例。
- 檢閱[了解原則效果](effects.md)。
- 瞭解如何以程式設計[方式建立原則](../how-to/programmatically-create.md)。
- 瞭解如何[取得合規性資料](../how-to/get-compliance-data.md)。
- 瞭解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
