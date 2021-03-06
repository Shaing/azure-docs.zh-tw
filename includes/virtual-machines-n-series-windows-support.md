---
title: 包含檔案
description: 包含檔案
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ab68fc6533be5e3241de2e49652251fea5fe2f7d
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780651"
---
## <a name="supported-operating-systems-and-drivers"></a>支援的作業系統和驅動程式

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) 驅動程式

只有下表所列的作業系統才支援 NC、NCv2、NCv3、ND 及 NDv2 系列 VM (針對 NV 系列為選擇性) 的 NVIDIA Tesla (CUDA) 驅動程式。 以下是本文章發行時的最新驅動程式下載連結。 如需最新的驅動程式，請瀏覽 [NVIDIA](https://www.nvidia.com/) 網站。

> [!TIP]
> 在 Windows Server VM 上手動安裝 CUDA 驅動程式的替代方案，就是部署 Azure [資料科學虛擬機器](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 適用於 Windows Server 2016 的 DSVM 版本會預先安裝 NVIDIA CUDA 驅動程式、CUDA 深度類神經網路程式庫和其他工具。


| OS | 驅動程式 |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 驅動程式

Microsoft 會將適用于 NV 和 NVv3 系列 Vm 的 NVIDIA GRID 驅動程式安裝程式重新散發為虛擬工作站或虛擬應用程式。 僅在 Azure NV 系列 Vm 上安裝這些方格驅動程式，僅適用于下表所列的作業系統。 這些驅動程式包含在 Azure 的 GRID 虛擬 GPU 軟體的授權中。 您不需要設定 NVIDIA vGPU 軟體授權服務器。

請注意，Nvidia 延伸模組一律會安裝最新的驅動程式。 我們會為客戶提供舊版的連結，其相依于較舊的版本。

適用于 Windows Server 2019、Windows Server 2016 和 Windows 10 （最高組建1909）：
- [方格10.0 （441.66）](https://go.microsoft.com/fwlink/?linkid=874181) （.exe）
- [方格9.0 （431.02）](https://download.microsoft.com/download/8/C/C/8CC88D54-EB07-44D3-8FA9-B797B173ED04/431.02_grid_win10_server2016_server2019_64bit_international.exe) （.exe） 

Windows Server 2012 R2、Windows Server 2008 R2、Windows 8 和 Windows 7： 
- [方格10.0 （441.66）](https://go.microsoft.com/fwlink/?linkid=874184) （.exe）
- [方格9.1 （431.79）](https://download.microsoft.com/download/8/6/e/86ef2daa-b31e-43ad-90f2-bd795384b71e/431.79_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) （.exe）  
