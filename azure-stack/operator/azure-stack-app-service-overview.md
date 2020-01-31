---
title: Azure App Service Azure Stack áttekintés
description: A Azure Stack hub Azure App Service és Azure Functions áttekintése.
author: BryanLa
ms.topic: article
ms.date: 01/13/2020
ms.author: BryanLa
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: 76acf85ac6bdfa1576416a9fc583463a71ad95d8
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876116"
---
# <a name="azure-app-service-and-azure-functions-on-azure-stack-hub-overview"></a>Azure App Service és Azure Functions a Azure Stack hub-on – áttekintés

A Azure App Service on Azure Stack hub az Azure Stack hub-on Microsoft Azure elérhető platform-szolgáltatásként nyújtott szolgáltatások. A szolgáltatás lehetővé teszi, hogy a belső vagy külső ügyfelei webes, API-és Azure Functions alkalmazásokat hozzanak létre bármely platformra vagy eszközre. Integrálhatja az alkalmazásokat a helyszíni alkalmazásokkal, és automatizálhatja az üzleti folyamatait. Azure Stack hub felhőalapú operátorai a teljes körűen felügyelt virtuális gépeken (VM-EK) futtathatják az ügyfélalkalmazások számára a megosztott virtuálisgép-erőforrásokat vagy a dedikált virtuális gépeket.

A Azure App Service lehetővé teszi az üzleti folyamatok automatizálását és a felhőalapú API-k üzemeltetését. Egyetlen integrált szolgáltatásként a Azure App Service lehetővé teszi, hogy a különböző összetevőket (például a webhelyeket, a REST API-kat és az üzleti folyamatokat) egyetlen megoldásba egyesítse.

## <a name="why-offer-azure-app-service-on-azure-stack-hub"></a>Miért ajánljuk a Azure App Servicet Azure Stack hub-on?

A Azure App Service főbb funkciói és képességei:

- **Több nyelv és keretrendszer**: Azure app Service rendelkezik a ASP.net, a Node. js, a Java, a PHP és a Python első osztályú támogatásával. A Windows PowerShell és más parancsfájlok vagy végrehajtható fájlok App Service virtuális gépeken is futtathatók.
- **DevOps-optimalizálás**: a folyamatos integráció és üzembe helyezés beállítása a GitHub, a helyi git vagy a BitBucket számára. A frissítések tesztelési és átmeneti környezetekben, valamint az alkalmazások App Service való kezelése Azure PowerShell vagy a többplatformos parancssori felület (CLI) használatával.
- **Visual Studio-integráció**: a Visual Studióban a dedikált eszközök megkönnyítik az alkalmazások létrehozásának és üzembe helyezésének munkáját.

## <a name="app-types-in-app-service"></a>Alkalmazástípusok az App Service-ben

App Service számos különböző típusú alkalmazást kínál, amelyek mindegyike egy adott számítási feladat üzemeltetésére szolgál:

- Webhelyeket és webalkalmazásokat üzemeltető [Web Apps](/azure/app-service/overview) .
- [API apps](/azure/app-service/overview) a REST API-k üzemeltetéséhez.
- Azure Functions az eseményen alapuló, kiszolgáló nélküli számítási feladatok üzemeltetéséhez.

A Word *alkalmazás* a számítási feladatok futtatására kijelölt üzemeltetési erőforrásokra hivatkozik. Tegyük fel, hogy a *webalkalmazást* például úgy gondolja, hogy egy webalkalmazást használ a számítási erőforrások és az alkalmazás kódjának, amelyek együttesen biztosítják a funkcionalitást a böngészőben. Azure App Service a webalkalmazás a Azure Stack hub által az alkalmazás kódjának üzemeltetéséhez biztosított számítási erőforrás.

Az alkalmazás több App Service különböző típusú alkalmazásból is állhat. Ha például az alkalmazás webes kezelőfelületből és REST API háttérből áll, a következőket teheti:

- Telepítsen mindkettőt (előtér és API) egyetlen webalkalmazásra.
- a kezelőfelület kódját üzembe helyezheti egy webalkalmazásban, a háttérrendszerét pedig egy API-alkalmazásban.

   [![App Service áttekintése monitorozási adattal](media/azure-stack-app-service-overview/image01.png "App Service áttekintése monitorozási adattal")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>Mi az App Service terv?

A App Service erőforrás-szolgáltató ugyanazt a kódot használja, amelyet Azure App Service használ, és így közös fogalmakat oszt meg. App Service az alkalmazások díjszabási tárolójának neve *app Service terv*. Az alkalmazások tárolására használt dedikált virtuális gépek készletét jelöli. Egy adott előfizetésen belül több App Service-csomag is megadható.

Az Azure-ban megosztott és dedikált feldolgozók vannak. A közös feldolgozók támogatják a nagy sűrűségű és a több-bérlős alkalmazások üzemeltetését, és csak egy közös feldolgozót lehet létrehozni. A dedikált kiszolgálókat csak egy bérlő használja, és három méretben érhető el: kicsi, közepes és nagy. A helyszíni ügyfelek igényeit nem lehet mindig az említett feltételekkel ismertetni. Az Azure Stack hub App Serviceban az erőforrás-szolgáltatói rendszergazdák határozzák meg az elérhetővé tenni kívánt feldolgozói szinteket. Az egyedi üzemeltetési igények alapján több közös feldolgozót vagy különálló feldolgozót is meghatározhat. Ezeknek a feldolgozói rétegbeli definícióknak a használatával meghatározhatják saját díjszabási SKU-ket.

## <a name="portal-features"></a>Portál funkciói


A Azure App Service on Azure Stack hub ugyanazt a felhasználói felületet használja, amelyet a Azure App Service használ. Ugyanez igaz a háttérrel. Néhány szolgáltatás azonban le van tiltva az Azure Stack központban. A szolgáltatások által igényelt Azure-specifikus elvárások vagy szolgáltatások jelenleg nem érhetők el Azure Stack hub-ban.

## <a name="next-steps"></a>Következő lépések

- [Az App Service Azure Stack hub-beli üzembe helyezésének előfeltételei](azure-stack-app-service-before-you-get-started.md)
- [A Azure App Service erőforrás-szolgáltató telepítése](azure-stack-app-service-deploy.md)

Kipróbálhatja a más [platformként nyújtott szolgáltatások (Pásti) szolgáltatásait](service-plan-offer-subscription-overview.md)is, például a [SQL Server erőforrás-szolgáltatót](azure-stack-sql-resource-provider-deploy.md) és a [MySQL erőforrás-szolgáltatót](azure-stack-mysql-resource-provider-deploy.md).
