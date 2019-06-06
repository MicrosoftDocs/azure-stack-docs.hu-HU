---
title: App Service-ben az Azure Stack – áttekintés |} A Microsoft Docs
description: Az Azure Stack App Service-ben – áttekintés
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
ms.author: mabrigg
ms.reviewer: anwestg
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 4ae72e7f75147fc3891731da48ee210fc546bd97
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692048"
---
# <a name="app-service-on-azure-stack-overview"></a>Az Azure Stack App Service szolgáltatásának áttekintése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure App Service az Azure Stacken egy elérhető az Azure Stack a Microsoft Azure platform –-szolgáltatásként (PaaS) ajánlat. A szolgáltatás lehetővé teszi, hogy a belső vagy külső ügyfelei számára, hogy hozzon létre a webes API-t és az Azure Functions alkalmazások bármilyen platformra vagy eszközre. Integrálhatják alkalmazásaikat a helyszíni megoldásokkal, valamint automatizálhatják üzleti folyamataikat. Az Azure Stack-felhőüzemeltetők teljes körűen felügyelt virtuális gépeken futtathatják az ügyfelek alkalmazásait, választásuk szerint vagy közös virtuálisgép-erőforrásokkal, vagy dedikált virtuális gépekkel.

Az Azure App Service lehetővé teszi, hogy az üzleti folyamatok automatizálásához és felhőalapú API-k üzemeltetéséhez. Egyetlen integrált szolgáltatásként az Azure App Service lehetővé teszi a különféle összetevők, például a websites, a REST API-k és az üzleti folyamatok, egyetlen megoldás kombinálhat.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Miért kínálnak az Azure App Service az Azure Stack?

Az App Service legfontosabb funkciói és képességei többek között az alábbiak:

- **Több nyelv és keretrendszer**: Az App Service kiváló támogatást nyújt az ASP.NET, Node.js, Java, PHP és Python rendelkezik. Az App Service virtuális gépeken a Windows PowerShell és egyéb parancsfájlokat vagy futtatható fájlokat is futtathatja.
- **DevOps-optimalizálás**: Állítsa be a folyamatos integrációt és üzembe helyezés a GitHub, a helyi Git vagy a bitbucket használatával. Frissítések tesztelési és átmeneti környezetek támogatása, és az App Service-ben az alkalmazások kezelése az Azure PowerShell-lel vagy a platformfüggetlen parancssori felület (CLI) használatával.
- **Visual Studio-integráció**: A Visual Studio dedikált eszközei leegyszerűsítik a létrehozása és alkalmazások telepítése.

## <a name="app-types-in-app-service"></a>Alkalmazástípusok az App Service-ben

Az App Service számos típusú, alkalmazások, amelyek célja, hogy egy adott számítási feladatot kínálja:

- [Web Apps](/azure/app-service/overview) webhelyek és webalkalmazások üzemeltetéséhez.
- [Az API Apps](/azure/app-service/overview) REST API-k üzemeltetéséhez.
- Az Azure Functions eseményvezérelt, kiszolgáló nélküli számítási feladatok futtatásához.

A word *alkalmazás* számítási feladatok futtatására elkülönített üzemeltetési erőforrásokat jelenti. Véve *webalkalmazás* például fájlszerkesztés egy webalkalmazást, a számítási erőforrások és az alkalmazás a böngészőben együttesen funkció code szokták vagyunk. Az App Service-ben webalkalmazás az Azure Stack kínál az alkalmazás kódjában üzemeltető számítási erőforrásokat.

Az alkalmazás több, különböző típusú App Service-alkalmazások is állhat. Például ha az alkalmazás webes kezelőfelületből épül fel, és vissza egy REST API-end, akkor:

- mindkettőt (a kezelőfelületet és az api-t is) üzembe helyezheti egyetlen webalkalmazásban;
- a kezelőfelület kódját üzembe helyezheti egy webalkalmazásban, a háttérrendszerét pedig egy API-alkalmazásban.

   [![App Service-ben – áttekintés a monitorozási adatok](media/azure-stack-app-service-overview/image01.png "figyelési adatok az App Service áttekintése")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>Mi az App Service-csomag?

Az App Service erőforrás-szolgáltató használja ugyanazt a kódot, hogy az Azure App Service használja, és így oszt meg néhány gyakori fogalmak. Az App Service-ben a díjszabási tároló alkalmazások neve a *App Service-csomag*. Azt jelöli, hogy a dedikált virtuális gépek halmazát jelenti az alkalmazások tárolására szolgál. Egy adott előfizetésen belül több App Service-csomagok is rendelkezhet.

Az Azure-ban vannak közös vagy dedikált feldolgozó. Megosztott feldolgozói nagy sűrűségű több-bérlős alkalmazás üzemeltetését is támogatja, és megosztott munkavállalók csak egy halmaza. Dedikált kiszolgálókat csak egy bérlő által használt, és három méretben származnak: kicsi, közepes és nagy. A helyi ügyfelek igényeit mindig nem ismerteti ezeket a kifejezéseket használatával. Az App Service az Azure Stacken erőforrás-szolgáltató rendszergazdák adhatja meg a feldolgozói rétegek szeretne elérhetővé tenni. Üzemeltetési egyedi igényei alapján, megadhatja a megosztott feldolgozó több példányban, vagy dedikált feldolgozó más-más részhalmazához. Feldolgozó szintű definíciók használatával, majd definiálhat a saját díjköteles termékváltozatok.

## <a name="portal-features"></a>A portál jellemzői

Az Azure Stack App Service-ben használja ugyanazt a felhasználói felületet, amely az Azure App Service-ben. Ugyanez vonatkozik a háttérrendszerrel. Néhány funkció azonban le vannak tiltva, az Azure Stackben. Az Azure-ra vonatkozó elvárásokat vagy ezek a funkciók igénylő szolgáltatások nem érhetők el jelenleg az Azure Stackben.

## <a name="next-steps"></a>További lépések

- [Mielőtt elkezdené, az Azure Stack App Service-szel](azure-stack-app-service-before-you-get-started.md)
- [Az App Service erőforrás-szolgáltató telepítése](azure-stack-app-service-deploy.md)

Is kipróbálhatja más [platform platformszolgáltatási (PaaS) szolgáltatásokra](azure-stack-offer-services-overview.md), mint például a [erőforrás-szolgáltató SQL Server](azure-stack-sql-resource-provider-deploy.md) és a [MySQL erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md).
