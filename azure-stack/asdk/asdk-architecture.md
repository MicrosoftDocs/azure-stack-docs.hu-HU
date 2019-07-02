---
title: Az Azure Stack Development Kit architektúrája |} A Microsoft Docs
description: Az Azure Stack Development Kit (ASDK) architektúrát ismerteti.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 4cd20ed4c76b062ea2fcfdf3d314ef2bcdecaff0
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492348"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>A Microsoft Azure Stack Development Kit architektúrája
Az Azure Stack Development Kit (ASDK) az egyetlen csomópontos üzemelő példánya egy egyetlen számítógépen futó Azure Stack. Edge útválasztási összetevők a NAT- és VPN-képességeket biztosít az Azure Stack gazdagépen van telepítve. Az Azure Stack infrastruktúra-szerepkörök futtatása a Hyper-V réteget a fizikai gazdaszámítógépen.


## <a name="virtual-machine-roles"></a>Virtuálisgép-szerepkörök
A ASDK használatával a következő virtuális gépek a development kit gazdagépen futó szolgáltatásokat nyújtja:

| Name (Név) | Leírás |
| ----- | ----- |
| **AzS-ACS01** | Az Azure Stack-tárolási szolgáltatások.|
| **AzS-ADFS01** | Active Directory összevonási szolgáltatások (ADFS).  |
| **AzS-CA01** | A tanúsítványszolgáltatás a szerepkör-szolgáltatások az Azure Stack szolgáltató szolgáltatásaival.|
| **AzS-DC01** | A Microsoft Azure Stackhez az Active Directory, DNS és DHCP szolgáltatások.|
| **AzS-ERCS01** | Vészhelyzeti helyreállítási konzol virtuális Gépet. |
| **AzS-GWY01** | Edge-átjáró szolgáltatásokon, például a VPN-helyek közötti kapcsolatok bérlői hálózatok számára.|
| **AzS-NC01** | Hálózati vezérlő, amely kezeli az Azure Stack hálózati szolgáltatások.  |
| **AzS-SLB01** | A terheléselosztás multiplexer szolgáltatások az Azure Stackben egyaránt bérlők és az Azure Stack-infrastruktúra-szolgáltatások.  |
| **AzS-SQL01** | Belső adatok tárolása az Azure Stack-infrastruktúra-szerepkörök.  |
| **AzS-WAS01** | Az Azure Stack felügyeleti portálon, és Azure Resource Manager-szolgáltatásokat.|
| **AzS-WASP01**| Az Azure Stack (bérlő) felhasználói portál és Azure Resource Manager-szolgáltatások.|
| **AzS-XRP01** | A Microsoft Azure Stack, beleértve a számítási, hálózati és tárolási erőforrás-szolgáltató infrastruktúra felügyeletvezérlő.|
| **AzS-SRNG01** | Támogatási kör virtuális Gépet üzemeltető a naplózási szolgáltatást az Azure Stackhez. |

## <a name="next-steps"></a>További lépések
[Alapszintű ASDK adminisztrációs feladatok megismerése](asdk-admin-basics.md)
