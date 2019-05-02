---
title: Az Azure Stack Development Kit architektúrája |} A Microsoft Docs
description: Az Azure Stack Development Kit (ASDK) architektúrát ismerteti.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/21/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: c69b124f84e87e8f0b937dfa275378c376894f9b
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64289999"
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


## <a name="next-steps"></a>További lépések
[Alapszintű ASDK adminisztrációs feladatok megismerése](asdk-admin-basics.md)
