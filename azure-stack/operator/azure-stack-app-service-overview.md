---
title: A Azure Stack áttekintése – áttekintés | App Service Microsoft Docs
description: A Azure Stack App Service áttekintése.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: BryanLa
ms.reviewer: anwestg
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 14c37e894d87d7a19ff6edc91b9c827ce53c08e8
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282634"
---
# <a name="app-service-on-azure-stack-overview"></a>Az Azure Stack App Service szolgáltatásának áttekintése

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure App Service on Azure Stack a Microsoft Azure elérhető platform-szolgáltatás, amely Azure Stack érhető el. A szolgáltatás lehetővé teszi, hogy a belső vagy külső ügyfelei webes, API-és Azure Functions alkalmazásokat hozzanak létre bármely platformra vagy eszközre. Integrálhatja az alkalmazásokat a helyszíni alkalmazásokkal, és automatizálhatja az üzleti folyamatait. Azure Stack a Felhőbeli operátorok a teljes körűen felügyelt virtuális gépeken (VM-EK) futtathatják az ügyfélalkalmazások számára a megosztott virtuálisgép-erőforrásokat vagy a dedikált virtuális gépeket.

A Azure App Service lehetővé teszi az üzleti folyamatok automatizálását és a felhőalapú API-k üzemeltetését. Egyetlen integrált szolgáltatásként a Azure App Service lehetővé teszi, hogy a különböző összetevőket, például webhelyeket, REST API-kat és üzleti folyamatokat egyetlen megoldásba egyesítse.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Miért ajánljuk a Azure App Service Azure Stack?

Az App Service legfontosabb funkciói és képességei többek között az alábbiak:

- **Több nyelv és keretrendszer**: app Service rendelkezik a ASP.net, a Node. js, a Java, a PHP és a Python első osztályú támogatásával. A Windows PowerShell és más parancsfájlok vagy végrehajtható fájlok App Service virtuális gépeken is futtathatók.
- **DevOps-optimalizálás**: a folyamatos integráció és üzembe helyezés beállítása a GitHub, a helyi git vagy a BitBucket számára. A frissítések tesztelési és átmeneti környezetekben, valamint az alkalmazások App Service való kezelése Azure PowerShell vagy a többplatformos parancssori felület (CLI) használatával.
- **Visual Studio-integráció**: a Visual Studióban a dedikált eszközök megkönnyítik az alkalmazások létrehozásának és üzembe helyezésének munkáját.

## <a name="app-types-in-app-service"></a>Alkalmazástípusok az App Service-ben

App Service számos különböző típusú alkalmazást kínál, amelyek mindegyike egy adott számítási feladat üzemeltetésére szolgál:

- Webhelyeket és webalkalmazásokat üzemeltető [Web Apps](/azure/app-service/overview) .
- [API apps](/azure/app-service/overview) a REST API-k üzemeltetéséhez.
- Azure Functions az eseményen alapuló, kiszolgáló nélküli számítási feladatok üzemeltetéséhez.

A Word *alkalmazás* a számítási feladatok futtatására kijelölt üzemeltetési erőforrásokra hivatkozik. Tegyük fel, hogy a *webalkalmazást* például úgy gondolja, hogy egy webalkalmazást használ a számítási erőforrások és az alkalmazás kódjának, amelyek együttesen biztosítják a funkcionalitást a böngészőben. App Service a webalkalmazás az alkalmazás kódjának üzemeltetéséhez Azure Stack által biztosított számítási erőforrás.

Az alkalmazás több App Service különböző típusú alkalmazásból is állhat. Ha például az alkalmazás webes kezelőfelületből és REST API háttérből áll, a következőket teheti:

- Mindkettő (előtér és API) üzembe helyezése egyetlen webalkalmazásban
- a kezelőfelület kódját üzembe helyezheti egy webalkalmazásban, a háttérrendszerét pedig egy API-alkalmazásban.

   [![App Service áttekintése a monitorozási](media/azure-stack-app-service-overview/image01.png "adatApp Service áttekintésével")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>Mi az App Service terv?

A App Service erőforrás-szolgáltató ugyanazt a kódot használja, amelyet Azure App Service használ, és így közös fogalmakat oszt meg. App Service az alkalmazások díjszabási tárolójának neve *app Service terv*. Az alkalmazások tárolására használt dedikált virtuális gépek készletét jelöli. Egy adott előfizetésen belül több App Service-csomag is megadható.

Az Azure-ban megosztott és dedikált feldolgozók vannak. A közös feldolgozók támogatják a nagy sűrűségű és a több-bérlős alkalmazások üzemeltetését, és csak egy közös feldolgozót lehet létrehozni. A dedikált kiszolgálókat csak egy bérlő használja, és három méretben érhető el: kicsi, közepes és nagy. A helyszíni ügyfelek igényeit nem lehet mindig az említett feltételekkel ismertetni. Azure Stack App Serviceban az erőforrás-szolgáltatói rendszergazdák meghatározhatják az elérhetővé tenni kívánt munkavégző szinteket. Az egyedi üzemeltetési igények alapján több közös feldolgozót vagy különálló feldolgozót is meghatározhat. Ezeknek a feldolgozói rétegbeli definícióknak a használatával meghatározhatják saját díjszabási SKU-ket.

## <a name="portal-features"></a>Portál funkciói

A App Service on Azure Stack ugyanazt a felhasználói felületet használja, amelyet a Azure App Service használ. Ugyanez igaz a háttérrel. Néhány szolgáltatás azonban le van tiltva a Azure Stackban. A szolgáltatások által igényelt Azure-specifikus elvárások vagy szolgáltatások jelenleg nem érhetők el Azure Stackban.

## <a name="next-steps"></a>Következő lépések

- [A App Service telepítésének előfeltételei a Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [A App Service erőforrás-szolgáltató telepítése](azure-stack-app-service-deploy.md)

Kipróbálhatja a más [platformként nyújtott szolgáltatások (Pásti) szolgáltatásait](service-plan-offer-subscription-overview.md)is, például a [SQL Server erőforrás-szolgáltatót](azure-stack-sql-resource-provider-deploy.md) és a [MySQL erőforrás-szolgáltatót](azure-stack-mysql-resource-provider-deploy.md).
