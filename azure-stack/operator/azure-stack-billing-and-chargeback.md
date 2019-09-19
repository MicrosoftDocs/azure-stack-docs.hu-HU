---
title: Ügyfél-számlázás és-jóváírás a Azure Stackban | Microsoft Docs
description: Ismerje meg, hogyan történik a Azure Stack-felhasználók számlázása az erőforrás-használatért, és hogyan érhető el a számlázási adatok az elemzéshez és a jóváíráshoz.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: bcdf43f7be95c76cfd4fc454d85e08ad197551a6
ms.sourcegitcommit: c196463492732218d2474d3a964f88e995272c80
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094342"
---
# <a name="usage-and-billing-in-azure-stack"></a>Használat és számlázás Azure Stack

Ez a cikk azt ismerteti, hogyan történik az Azure Stack-felhasználók számlázása az erőforrás-használatért, és hogy a számlázási információk Hogyan érhetők el az elemzéshez és a jóváíráshoz.

Azure Stack gyűjti és csoportosítja a felhasznált erőforrások használati adatait, majd továbbítja ezeket az adatokat az Azure Commerce szolgáltatásnak. Az Azure Commerce az Azure-használathoz hasonló módon számláz Azure Stack használatért.

Használhatja a használati adatokat is, és exportálhatja a saját számlázási vagy jóváírási rendszerébe egy számlázási adapter használatával, vagy exportálhatja azt egy üzleti intelligencia eszközre, például a Microsoft Power BIra.

## <a name="usage-pipeline"></a>Használati folyamat

A Azure Stack minden erőforrás-szolgáltatója erőforrás-használat alapján könyveli a használati adatokat. A használati szolgáltatás rendszeres időközönként (óránként és naponta) összesíti a használati adatokat, és a használati adatbázisban tárolja azokat. Azure Stack operátorok és felhasználók a tárolt használati adatokat a Azure Stack erőforrás-használati API-k használatával érhetik el.

Ha az Azure-ban [regisztrálta Azure stack-példányát](azure-stack-registration.md), Azure stack úgy van konfigurálva, hogy a használati adatokat az Azure Commerce szolgáltatásba küldje el. Miután feltöltötte az adatokat az Azure-ba, elérheti azt a számlázási portálon vagy az Azure Resource használati API-k használatával. További információ arról, hogy milyen használati adatokat jelentettek az Azure-ban: [használati adatok jelentése](azure-stack-usage-reporting.md).  

A következő képen a használati folyamat legfontosabb összetevői láthatók:

![Használati folyamat](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Milyen használati információk találhatók meg, és hogyan?

Az erőforrás-szolgáltatók (például a számítás, a tárolás és a hálózat) Azure Stack az egyes előfizetések óránkénti időközönkénti használati adatait hozhatják ki. A használati adatok a felhasznált erőforrással kapcsolatos információkat tartalmaznak, például az erőforrás nevét, a felhasznált előfizetést és a felhasznált mennyiséget. A fogyasztásmérők azonosító erőforrásairól a [használati API – gyakori kérdések](azure-stack-usage-related-faq.md)című témakörben olvashat bővebben.

A használati adatok gyűjtése után a rendszer [jelentést küld az Azure-nak](azure-stack-usage-reporting.md) a számla létrehozásához, amelyet az Azure számlázási portálján tekinthet meg.

> [!NOTE]  
> A Azure Stack Development Kit (ASDK) és a kapacitási modellben licencet nem igénylő Azure Stack integrált rendszerfelhasználók számára nem szükséges a használati adatok jelentése. A Azure Stack licencelésével kapcsolatos további tudnivalókért tekintse meg a [csomagolási és díjszabási adatlapot](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Az Azure számlázási portál a felszámítható erőforrások használati adatait jeleníti meg. A felszámítható erőforrások mellett Azure Stack a használati adatokat a szélesebb körű erőforrások számára is rögzíti, amelyeket REST API-kon vagy PowerShell-parancsmagokon keresztül elérhet a Azure Stack környezetben. Azure Stack operátorok megkapják az összes felhasználói előfizetés használati adatait. Az egyes felhasználók csak a saját használati adataikat vehetik igénybe.

## <a name="usage-reporting-for-multi-tenant-cloud-service-providers"></a>Használati jelentéskészítés a több-bérlős felhőalapú szolgáltatók számára

Az Azure Stackt használó több-bérlős felhőalapú megoldás-szolgáltató (CSP) külön jelentést szeretne készíteni az egyes ügyfelek használatáról, így a szolgáltató a különböző Azure-előfizetésekhez is felszámíthatja a használatot.

Minden ügyfél identitását egy másik Azure Active Directory (Azure AD) bérlő képviseli. Azure Stack támogatja az egyes Azure AD-bérlők egy CSP-előfizetésének hozzárendelését. Bérlőket és előfizetéseket adhat hozzá az alapszintű Azure Stack-regisztrációhoz. Az alapszintű regisztráció minden Azure Stack példány esetében elkészült. Ha egy előfizetés nincs regisztrálva a bérlőhöz, a felhasználó továbbra is használhatja a Azure Stackt, és a használatuk az alapregisztrációhoz használt előfizetésnek lesz elküldve.

## <a name="next-steps"></a>További lépések

- [Regisztrálás Azure Stack](azure-stack-registration.md)
- [Az Azure Stack használati adatainak jelentése az Azure-nak](azure-stack-usage-reporting.md)
- [Szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md)
- [Bérlői erőforrás-használati API](azure-stack-tenant-resource-usage-api.md)
- [Használattal kapcsolatos gyakori kérdések](azure-stack-usage-related-faq.md)
