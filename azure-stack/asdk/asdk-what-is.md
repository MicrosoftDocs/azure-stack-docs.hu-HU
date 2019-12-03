---
title: Mi az Azure Stack hub Development Kit (ASDK)? | Microsoft Docs
description: Ismerje meg az Azure Stack hub fejlesztői készletét, valamint azt, hogy a Azure Stack hub kiértékeléséhez hogyan használják.
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
ms.date: 11/27/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: fb9dab302e20d436be7f3cf9a76de913bbfe22e3
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689827"
---
# <a name="what-is-the-azure-stack-hub-development-kit-asdk"></a>Mi az Azure Stack hub Development Kit (ASDK)?
A [Microsoft Azure stack hub integrált rendszereinek](../operator/azure-stack-overview.md) mérete 4-16 csomópontokból áll, és a hardvereszközök és a Microsoft közösen támogatja őket. Azure Stack hub integrált rendszerek használatával új forgatókönyveket engedélyezhet az éles számítási feladatokhoz. Ha Ön Azure Stack hub-kezelő, aki felügyeli az integrált rendszerek infrastruktúráját, és szolgáltatásokat kínál, tekintse meg az [operátori dokumentációt](/azure-stack/operator).

A ASDK az Azure Stack hub egycsomópontos telepítése, amelyet **ingyenesen**letöltheti és használhatja. Minden ASDK-összetevő telepítve van egy olyan virtuális gépre (VM), amely egyetlen gazdagépen fut, és meg kell felelnie a [minimális hardverkövetelmények követelményeinek](asdk-deploy-considerations.md#hardware). A ASDK célja, hogy olyan környezetet biztosítson, amelyben kiértékelheti Azure Stack hub-t, és modern alkalmazásokat fejleszthet API-k használatával, és az Azure-t *nem éles* környezetben lehet kialakítani. 

> [!IMPORTANT]
> A ASDK nem használható éles környezetben, vagy nem támogatott.

Mivel az összes ASDK-összetevő egyetlen gazdaszámítógépre van telepítve, korlátozott számú fizikai erőforrás érhető el. A ASDK üzemelő példányok esetében a Azure Stack hub infrastruktúra-és a bérlői virtuális gépek egyaránt ugyanazon a kiszolgálón találhatók. Ez a konfiguráció nem méretezhető vagy teljesítmény kiértékelésére szolgál.

A ASDK úgy lett kialakítva, hogy Azure-konzisztens hibrid felhőalapú élményt nyújtson a következőhöz:
- **Rendszergazdák** (Azure stack hub-operátorok): a ASDK nagyszerű erőforrás az elérhető Azure stack hub-szolgáltatások kiértékeléséhez és megismeréséhez.
- **Fejlesztők**: a ASDK hibrid vagy modern alkalmazások fejlesztésére is használhatók a helyszínen (fejlesztési/tesztelési környezetekben). Ez a rugalmasság a fejlesztési élmény megismételhetőségét kínálja a Azure Stack hub éles üzembe helyezése előtt vagy mellett.

Tekintse meg ezt a rövid videót, ahol további információkat olvashat a ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-hub-differences"></a>ASDK és többcsomópontos Azure Stack hub-különbségek
Az egycsomópontos ASDK üzemelő példányok számos különböző módon különböznek a több csomópontos Azure Stack hub-telepítéstől:

|Leírás|AZURE STACK FEJLESZTŐI KÉSZLET (ASDK)|Több csomópontos Azure Stack hub|
|-----|-----|-----|
|**Méretezés**|Az összes összetevő egyetlen csomópontos kiszolgálói számítógépen van telepítve.|4-16 csomópontokból is kiterjedhet.|
|**Rugalmasság**|Az egycsomópontos konfiguráció nem biztosít magas rendelkezésre állást|A magas rendelkezésre állási képességek támogatottak.|
|**Hálózat**|A ASDK-gazdagép az összes ASDK hálózati forgalmat irányítja. Nincsenek további kapcsolókra vonatkozó követelmények.|A többcsomópontos üzemelő példányokban összetettebb [hálózati útválasztási infrastruktúra](../operator/azure-stack-network.md#network-infrastructure) szükséges, beleértve a Top-of-rack (TOR), a alaplapi felügyeleti vezérlő (bmc) és a Border (Datacenter Network) kapcsolókat.|
|**Javítási és frissítési folyamat**|A ASDK új verziójára való áttéréshez újra kell telepítenie a ASDK a ASDK-gazdaszámítógépen.|A telepített Azure Stack hub verziójának frissítéséhez használt [javítási és frissítési](../operator/azure-stack-updates.md) folyamat.|
|**Támogatás**|MSDN Azure Stack fórum. A Microsoft ügyfél-és támogatási (CSS) támogatása *nem* érhető el nem éles környezetekben.|[MSDN Azure stack fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) és teljes körű CSS-támogatás.|
| | |

## <a name="learn-about-available-services"></a>Az elérhető szolgáltatások ismertetése
Azure Stack hub-operátorként tudnia kell, hogy mely szolgáltatásokat teheti elérhetővé a felhasználók számára. Azure Stack hub az Azure-szolgáltatások egy részhalmazát támogatja, és a támogatott szolgáltatások listája az idő múlásával továbbra is fejlődni fog.

### <a name="foundational-services"></a>Alapszolgáltatások
Alapértelmezés szerint a Azure Stack hub a következő "alapszolgáltatásokat" tartalmazza a ASDK telepítésekor:
- Számítási szolgáltatások
- Adattárolás
- Hálózatkezelés
- Key Vault

Ezekkel az alapszolgáltatásokkal minimális konfigurációval biztosíthat infrastruktúra-szolgáltatást (IaaS) a felhasználók számára.

### <a name="additional-services"></a>Kiegészítő szolgáltatások
Jelenleg a következő kiegészítő platform-szolgáltatásként nyújtott szolgáltatások támogatottak:
- App Service
- Azure Functions
- SQL-és MySQL-adatbázisok

> [!NOTE]
> Ezeknek a szolgáltatásoknak további konfigurációra van szükségük ahhoz, hogy elérhetővé tegyék azokat a felhasználók számára, és alapértelmezés szerint nem érhetők el a ASDK telepítésekor.

## <a name="service-roadmap"></a>Szolgáltatási ütemterv
Azure Stack hub további Azure-szolgáltatások támogatását is támogatja. Ha többet szeretne megtudni az Azure Stack hub-ról, tekintse meg a [Azure stack ütemtervet](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Következő lépések
Az Azure Stack hub kiértékelésének megkezdéséhez először le kell [töltenie a legújabb ASDK](asdk-download.md) , és elő kell készítenie a ASDK-gazdagépet. Ezután telepítheti a ASDK, és bejelentkezhet a rendszergazda és a felhasználói portálra az Azure Stack hub használatának megkezdéséhez.