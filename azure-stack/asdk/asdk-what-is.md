---
title: Bevezetés az Azure Stack Development Kit (ASDK) való |} A Microsoft Docs
description: Leírja, mi a ASDK és gyakori alkalmazási esetei kiértékelését a Microsoft Azure Stackhez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
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
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 08f5d51eb4ded47d1e52ec53d4c75903d13c8815
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617684"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Mi az az Azure Stack Development Kit?
[A Microsoft Azure Stack integrált rendszerek](../operator/azure-stack-overview.md) mérete 4 és 16 közötti csomópontjáról tartományt, és közösen egy hardver partner és a Microsoft által támogatott. Azure Stack integrált rendszerek használatával engedélyezhető az új forgatókönyvek használhatók a termelési számítási feladatokhoz. Ha Ön az Azure Stack operátorait, akik az integrált rendszerek infrastruktúrát felügyeli, és szolgáltatásokat kínál, tekintse meg a [operátori dokumentációja](/azure-stack/operator).

Az Azure Stack Development Kit (ASDK) az egyetlen csomópontos üzemelő példánya, letöltheti és használhatja az Azure Stack **ingyenes**. Minden ASDK-összetevő telepítve van a virtuális gépek egy egyetlen számítógépen futó kell elérik vagy túllépik a [hardverre vonatkozó minimális](asdk-deploy-considerations.md#hardware). A ASDK hivatott környezet, amelyben az Azure Stack értékeli és API-k használatával, és az Azure-eszközkészlet modern alkalmazások fejlesztése a *nem éles* környezetben. 

> [!IMPORTANT]
> A ASDK nem készült fel-vagy éles környezetben használható.

Minden ASDK összetevője van telepítve egyetlen development kit gazdagépen, mert nincsenek korlátozott fizikai erőforrásokat. Az ASDK központi, az Azure Stack-infrastruktúra virtuális gépeinek és a bérlői virtuális gépek megtalálhatók a kiszolgáló-számítógépen. Ez a konfiguráció nem célja a méretezési csoport vagy a teljesítmény értékeléséhez.

Adjon meg egy Azure-konzisztens hibrid felhőalapú megoldás a ASDK célja:
- **A rendszergazdák** (az Azure Stack-operátorok). A ASDK egy nagyszerű forrás kiértékeléséhez, és ismerje meg a rendelkezésre álló Azure Stack-szolgáltatások.
- **A fejlesztők**. A ASDK fejleszthet hibrid, illetve a modern alkalmazások a helyszíni (fejlesztési-tesztelési környezetben) használható. Ez kínál a fejlesztési környezetet biztosít előtt, vagy az Azure Stack éles környezetekben üzemelő példányok mellett az ismételhetőség. 

Ez a rövid videóban további információ a ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK és a több csomópontos az Azure Stack különbségek
Egy csomópontos ASDK központi telepítések érdemes figyelembe vennie, néhány fontos módon különböznek a több csomópontos Azure Stack üzemelő példányok.

|Leírás|ASDK|Több csomópontos az Azure Stack|
|-----|-----|-----|
|**Méretezés**|Az összes összetevő egy egycsomópontos kiszolgálói számítógépre van telepítve.|A 4 és 16 közötti csomópontok mérete terjedhet.|
|**Rugalmasságának biztosításával**|Egy csomópontos konfigurációja nem biztosít magas rendelkezésre állás|[Magas rendelkezésre állású](../operator/azure-stack-overview.md#providing-high-availability) funkciót támogatnak.|
|**Hálózat**|A ASDK gazdagép ASDK minden hálózati forgalmat irányítja. Nem vonatkoznak további kapcsoló követelmények.|Összetettebb [hálózati útválasztási infrastruktúra](../operator/azure-stack-network.md#network-infrastructure) több csomópontos központi telepítések szükség, például a Top-Of-Rack (TOR), a alaplapi felügyeleti vezérlő (BMC) és a kapcsolók szegély (adatközpont-hálózat).|
|**Javítási és frissítési folyamat**|Helyezze át a ASDK új verziójára, ismét üzembe kell helyeznie a ASDK a development kit gazdagépen.|[Javítása és frissítése](../operator/azure-stack-updates.md) a telepített Azure Stack-verzió frissítésére szolgáló folyamat.|
|**Támogatás**|Fórum az MSDN Azure Stack. A Microsoft ügyfélszolgálata és a támogatási szolgálathoz (CSS) támogatás *nem* nem éles környezetekben érhető el.|[MSDN Azure Stack forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) és teljes CSS támogatja.|
| | |

## <a name="learn-about-available-services"></a>Elérhető szolgáltatások ismertetése
Kezelőként Azure Stack kell tudni, hogy mely szolgáltatások elérhetővé teheti a felhasználók számára. Az Azure Stack egy Azure-szolgáltatások részét támogatja, és a támogatott szolgáltatások teljes listájának továbbra is verzióinformációk.

### <a name="foundational-services"></a>Alapvető szolgáltatások
Alapértelmezés szerint az Azure Stack tartalmazza a következő "alapvető szolgáltatások" az ASDK telepítésekor:
- Compute
- Storage
- Hálózat
- Key Vault

Alapvető szolgáltatások infrastruktúra--szolgáltatásként (IaaS) elérhetővé teheti a felhasználók minimális konfigurációval.

### <a name="additional-services"></a>További szolgáltatások
Jelenleg a következő további Platform--szolgáltatásként (PaaS) szolgáltatások támogatottak:
- App Service
- Azure Functions
- SQL- és MySQL-adatbázisok

> [!NOTE]
> Ezek a szolgáltatások további konfigurációt igényelnek, is elérhetővé tétele a felhasználók számára, és nem érhetők el alapértelmezés szerint a ASDK telepítésekor.

## <a name="service-roadmap"></a>Szolgáltatás-ütemterv
További Azure-szolgáltatások támogatása az Azure Stack továbbra is. Az Azure Stack újdonságokat kapcsolatos további információkért tekintse meg a [Azure Stack ütemterv](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>További lépések
Ismerkedés az Azure Stack kiértékelése, először szüksége [töltse le a legújabb ASDK](asdk-download.md) és készítse elő a ASDK gazdaszámítógépen. Miután előkészítette a development kit gazdagép, a ASDK telepítse, és az Azure Stack használatának megkezdéséhez jelentkezzen be a rendszergazda és a felhasználói portálon.