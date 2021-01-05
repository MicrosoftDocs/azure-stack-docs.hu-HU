---
title: ASDK architektúra
description: Ismerkedjen meg a Azure Stack Development Kit (ASDK) architektúrával.
author: PatAltimore
ms.topic: article
ms.date: 06/28/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: bd41198f2650518584b36c8346f398706e2cd78d
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873469"
---
# <a name="asdk-architecture"></a>ASDK architektúra
A Azure Stack Development Kit (ASDK) egy egycsomópontos üzemelő példány, Azure Stack egyetlen gazdagépen fut. A peremhálózati útválasztási összetevőket a rendszer a gazdagépen telepíti a NAT-és VPN-képességek biztosításához a Azure Stack számára. Azure Stack infrastruktúra-szerepkörök a fizikai gazdagép Hyper-V rétegében futnak.


## <a name="virtual-machine-roles"></a>Virtuális gépi szerepkörök
A ASDK a következő virtuális gépeket használó szolgáltatásokat kínálja a fejlesztői csomag gazdagépén:

| Név | Leírás |
| ----- | ----- |
| **AzS – ACS01** | Azure Stack tárolási szolgáltatások.|
| **AzS – ADFS01** | Active Directory összevonási szolgáltatások (AD FS) (ADFS).  |
| **AzS – CA01** | Hitelesítésszolgáltatói szolgáltatások Azure Stack szerepkör-szolgáltatásokhoz.|
| **AzS – DC01** | Active Directory-, DNS-és DHCP-szolgáltatások Microsoft Azure Stackhoz.|
| **AzS – ERCS01** | Vészhelyzeti helyreállítási konzol virtuális gépe. |
| **AzS – GWY01** | Peremhálózati átjáró-szolgáltatások, például VPN-helyek közötti kapcsolatok a bérlői hálózatok számára.|
| **AzS – NC01** | Azure Stack hálózati szolgáltatásokat kezelő hálózati vezérlő.  |
| **AzS – SLB01** | Azure Stack terheléselosztási szolgáltatások a bérlők és a Azure Stack infrastrukturális szolgáltatások esetében is.  |
| **AzS – SQL01** | Belső adattár Azure Stack infrastruktúra-szerepkörökhöz.  |
| **AzS – WAS01** | Azure Stack a felügyeleti portál és a Azure Resource Manager szolgáltatások.|
| **AzS – WASP01**| Azure Stack felhasználói (bérlői) portál és Azure Resource Manager szolgáltatások.|
| **AzS – XRP01** | Infrastruktúra-kezelő vezérlő Microsoft Azure Stackhoz, beleértve a számítási, hálózati és tárolási erőforrás-szolgáltatókat.|
| **AzS – SRNG01** | Támogatási gyűrűs virtuális gép, amely a Azure Stack naplózási szolgáltatását üzemelteti. |

## <a name="next-steps"></a>További lépések
[Tudnivalók az alapszintű ASDK-felügyeleti feladatokról](asdk-admin-basics.md)
