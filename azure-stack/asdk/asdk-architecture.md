---
title: ASDK architektúra
description: Ismerkedjen meg a Azure Stack Development Kit (ASDK) architektúrával.
author: justinha
ms.topic: article
ms.date: 06/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 207b99b9a3cbb6c030a6e79137d036820b3b3f60
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "79294208"
---
# <a name="asdk-architecture"></a>ASDK architektúra
A Azure Stack Development Kit (ASDK) egy egycsomópontos üzemelő példány, Azure Stack egyetlen gazdagépen fut. A peremhálózati útválasztási összetevőket a rendszer a gazdagépen telepíti a NAT-és VPN-képességek biztosításához a Azure Stack számára. Azure Stack infrastruktúra-szerepkörök a fizikai gazdagép Hyper-V rétegében futnak.


## <a name="virtual-machine-roles"></a>Virtuális gépi szerepkörök
A ASDK a következő virtuális gépeket használó szolgáltatásokat kínálja a fejlesztői csomag gazdagépén:

| Name (Név) | Leírás |
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
