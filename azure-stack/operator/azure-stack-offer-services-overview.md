---
title: Szolgáltatásajánlatok készítése az Azure Stackben |} A Microsoft Docs
description: Felhő-felelősként a szolgáltatások elérhetővé teheti a felhasználók számára.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: a85f7398e492a2d49c2f6c1b686f2e15042b0444
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268370"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Az Azure Stackben szolgáltatásajánlások áttekintése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

[A Microsoft Azure Stack](azure-stack-overview.md) egy hibrid felhőplatform, amely lehetővé teszi szolgáltatások továbbítására az adatközpontból. Szolgáltatóként a szolgáltatások elérhetővé teheti a bérlők számára. Egy adott üzleti vagy kormányzati szerv helyszíni szolgáltatások elérhetővé teheti az alkalmazottak. 

Elérhetővé teheti [infrastruktúra-szolgáltatás](https://azure.microsoft.com/overview/what-is-iaas/) , amelyek lehetővé teszik a felhasználók hozhat létre egy igény szerinti számítási infrastruktúrát (IaaS) szolgáltatások kiépítése és felügyelete az Azure Stack felhasználói portálon.

Ezenkívül telepítheti [szolgáltatásként nyújtott platformon](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) a Microsoft és más 3. külső szolgáltatók az Azure Stack-szolgáltatások. A szolgáltatások is nyújthatnak, amelyek közé tartozik, de nem korlátozódik:

- [App Service erőforrás-szolgáltató hozzáadása az Azure Stackhez](azure-stack-app-service-overview.md)

- [Az SQL Server erőforrás-szolgáltató hozzáadása az Azure Stackhez](azure-stack-sql-resource-provider-deploy.md)

- [A MySQL-kiszolgáló erőforrás-szolgáltató hozzáadása az Azure Stackhez](azure-stack-mysql-resource-provider-deploy.md)


Kombinálhatja szolgáltatásokkal integrálható, és összetett megoldásokat a különböző felhasználók számára.

Ezek a szolgáltatások biztosításához, a felhasználók számára, létre kell hoznia [csomagok, ajánlatok és kvóták](azure-stack-plan-offer-quota-overview.md). A felhasználók ezután is előfizethetnek azokra a a szolgáltatások használatára.

## <a name="plan-your-service-offers"></a>A szolgáltatási ajánlatok megtervezése

Ha tervezi az ajánlatok, vegye figyelembe a következőket:

**Próbaverziós**: Próbaverziós segítségével szerezzen új felhasználók, akik további szolgáltatásokat, majd frissítheti. A próbaverziós ajánlat, hozzon létre egy kis [ALAPCSOMAG](azure-stack-plan-offer-quota-overview.md#base-plan) és a egy választható nagyobb kiegészítő csomag.

**Kapacitástervezés**: Előfordulhat, hogy az érintett felhasználókkal, erőforrások és a rendszer az összes felhasználó eltömődés nagy mennyiségű helyezésre. Teljesítmény érdekében is [konfigurálása a csomagok kvótái](azure-stack-plan-offer-quota-overview.md#plans) maximális kihasználtsága.

**Delegált szolgáltatók**: Mások képes létrehozni kínál biztosíthat a környezetben. Például ha Ön a szolgáltató, akkor [delegálása](azure-stack-delegated-provider.md) a viszonteladók ezt a lehetőséget. Vagy, ha egy szervezet, delegálhatja más szakaszainak körvonalszínét leányvállalatai.

## <a name="next-steps"></a>További lépések

[Hozzon létre egy ajánlatot az Azure Stackben](azure-stack-create-offer.md)
