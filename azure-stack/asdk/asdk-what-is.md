---
title: Mi a ASDK? | Microsoft Docs
description: Ismerje meg a Azure Stack Development Kit (ASDK) és a Azure Stack kiértékelésének módját.
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
ms.topic: overview
ms.custom: mvc
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 50a08480a098c9ca5a6d50e94dd6dc7dbfc78b09
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118625"
---
# <a name="what-is-the-asdk"></a>Mi a ASDK?
[Microsoft Azure stack az integrált rendszerek](../operator/azure-stack-overview.md) mérete 4-16 csomópontok között, és a hardvereszközök és a Microsoft közösen támogatottak. Azure Stack integrált rendszerek használatával új forgatókönyveket engedélyezhet az éles számítási feladatokhoz. Ha Ön Azure Stack kezelő, aki felügyeli az integrált rendszerek infrastruktúráját, és szolgáltatásokat kínál, [](/azure-stack/operator)tekintse meg az operátori dokumentációt.

A Azure Stack Development Kit (ASDK) a Azure Stack egy egycsomópontos telepítése, amelyet **ingyenesen**letölthet és használhat fel. Minden ASDK-összetevő telepítve van egy olyan virtuális gépre (VM), amely egyetlen gazdagépen fut, és meg kell felelnie a [minimális hardverkövetelmények követelményeinek](asdk-deploy-considerations.md#hardware). A ASDK célja, hogy olyan környezetet biztosítson, amelyben kiértékelheti Azure Stack és modern alkalmazásokat fejleszthet API-k használatával, és az Azure-t *nem éles* környezetben is kialakíthatja. 

> [!IMPORTANT]
> A ASDK nem használható éles környezetben, vagy nem támogatott.

Mivel az összes ASDK-összetevő egyetlen gazdaszámítógépre van telepítve, korlátozott számú fizikai erőforrás érhető el. A ASDK üzemelő példányok esetében a Azure Stack infrastruktúra virtuális gépei és a bérlői virtuális gépek ugyanazon a kiszolgálón vannak. Ez a konfiguráció nem méretezhető vagy teljesítmény kiértékelésére szolgál.

A ASDK úgy lett kialakítva, hogy Azure-konzisztens hibrid felhőalapú élményt nyújtson a következőhöz:
- **Rendszergazdák** (Azure Stack operátorok): A ASDK egy nagyszerű erőforrás, amellyel kiértékelheti és megismerheti az elérhető Azure Stack-szolgáltatásokat.
- **Fejlesztők**: A ASDK hibrid vagy modern alkalmazások fejlesztésére használható a helyszínen (fejlesztési/tesztelési környezetekben). Ez a rugalmasság a fejlesztési élmény megismételhetőségét kínálja a Azure Stack éles környezetekben való üzembe helyezése előtt, illetve azok mellett.

Tekintse meg ezt a rövid videót, ahol további információkat olvashat a ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK és több csomópontos Azure Stack különbségek
Az egycsomópontos ASDK-telepítések több csomópontos Azure Stack üzemelő példánytól különböznek, néhány fontos módon:

|Leírás|ASDK|Több csomópontos Azure Stack|
|-----|-----|-----|
|**Méretezés**|Az összes összetevő egyetlen csomópontos kiszolgálói számítógépen van telepítve.|4-16 csomópontokból is kiterjedhet.|
|**Rugalmasság**|Az egycsomópontos konfiguráció nem biztosít magas rendelkezésre állást|A [magas rendelkezésre állási](../operator/azure-stack-overview.md#providing-high-availability) képességek támogatottak.|
|**Hálózat**|A ASDK-gazdagép az összes ASDK hálózati forgalmat irányítja. Nincsenek további kapcsolókra vonatkozó követelmények.|A többcsomópontos üzemelő példányokban összetettebb [hálózati útválasztási infrastruktúra](../operator/azure-stack-network.md#network-infrastructure) szükséges, beleértve a Top-of-rack (TOR), a alaplapi felügyeleti vezérlő (bmc) és a Border (Datacenter Network) kapcsolókat.|
|**Javítási és frissítési folyamat**|A ASDK új verziójára való áttéréshez újra kell telepítenie a ASDK a ASDK-gazdaszámítógépen.|A telepített Azure Stack verziójának frissítéséhez használt [javítási és frissítési](../operator/azure-stack-updates.md) folyamat.|
|**Támogatás**|MSDN Azure Stack fórum. A Microsoft ügyfél-és támogatási (CSS) támogatása *nem* érhető el nem éles környezetekben.|[MSDN Azure stack fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) és teljes körű CSS-támogatás.|
| | |

## <a name="learn-about-available-services"></a>Az elérhető szolgáltatások ismertetése
Azure Stack operátorként tudnia kell, hogy mely szolgáltatásokat teheti elérhetővé a felhasználók számára. Azure Stack támogatja az Azure-szolgáltatások egy részhalmazát, és a támogatott szolgáltatások listája az idő múlásával továbbra is fejlődni fog.

### <a name="foundational-services"></a>Alapszolgáltatások
Alapértelmezés szerint a Azure Stack a következő "alapszolgáltatásokat" tartalmazza a ASDK telepítésekor:
- Compute
- Storage
- Hálózat
- Key Vault

Ezekkel az alapszolgáltatásokkal minimális konfigurációval biztosíthat infrastruktúra-szolgáltatást (IaaS) a felhasználók számára.

### <a name="additional-services"></a>További szolgáltatások
Jelenleg a következő kiegészítő platform-szolgáltatásként nyújtott szolgáltatások támogatottak:
- App Service
- Azure Functions
- SQL-és MySQL-adatbázisok

> [!NOTE]
> Ezeknek a szolgáltatásoknak további konfigurációra van szükségük ahhoz, hogy elérhetővé tegyék azokat a felhasználók számára, és alapértelmezés szerint nem érhetők el a ASDK telepítésekor.

## <a name="service-roadmap"></a>Szolgáltatási ütemterv
Azure Stack továbbra is támogatja a további Azure-szolgáltatásokat. Ha többet szeretne megtudni a Azure Stack következő lépésekről, tekintse meg a [Azure stack ütemtervet](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>További lépések
A Azure Stack kiértékelésének megkezdéséhez először le kell [töltenie a legújabb ASDK](asdk-download.md) , és elő kell készítenie a ASDK-gazdagépet. Ezután telepítheti a ASDK, és bejelentkezhet a rendszergazda és a felhasználói portálra a Azure Stack használatának megkezdéséhez.