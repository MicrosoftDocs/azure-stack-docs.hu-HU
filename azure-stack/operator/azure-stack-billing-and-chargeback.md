---
title: Ügyfél számlázása és jóváírása Azure Stack központban
description: Ismerje meg, hogyan történik a Azure Stack hub-felhasználók számlázása az erőforrás-használatért, és hogy a számlázási adatok hogyan érhetők el az elemzéshez és a jóváíráshoz.
author: sethmanheim
ms.topic: article
ms.date: 11/09/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 066538eb3bccb6e9e296a394400f2d1de0583f93
ms.sourcegitcommit: 980be7813e6f39fb59926174a5d3e0d392b04293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414095"
---
# <a name="usage-and-billing-in-azure-stack-hub"></a>Használat és számlázás Azure Stack központban

Ez a cikk azt ismerteti, hogyan történik a Azure Stack hub-felhasználók számlázása az erőforrás-használatért, és hogy a számlázási információk Hogyan érhetők el az elemzéshez és a jóváíráshoz.

Azure Stack hub a felhasznált erőforrások használati adatait gyűjti és csoportosítja, majd továbbítja ezeket az adatokat az Azure Commerce szolgáltatásnak. Az Azure Commerce a Azure Stack hub használatát ugyanúgy számlázza, mint az Azure-használatért.

Használhatja a használati adatokat is, és exportálhatja a saját számlázási vagy jóváírási rendszerébe egy számlázási adapter használatával, vagy exportálhatja azt egy üzleti intelligencia eszközre, például a Microsoft Power BIra.

## <a name="usage-pipeline"></a>Használati folyamat

Azure Stack hub minden erőforrás-szolgáltatója erőforrás-használat alapján könyveli a használati adatokat. A használati szolgáltatás rendszeres időközönként (óránként és naponta) összesíti a használati adatokat, és a használati adatbázisban tárolja azokat. Azure Stack hub-operátorok és a felhasználók a tárolt használati adatokat a Azure Stack hub erőforrás-használati API-kon keresztül érhetik el.

Ha [regisztrálta Azure stack hub-példányát az Azure](azure-stack-registration.md)-ban, Azure stack hub úgy van konfigurálva, hogy a használati adatokat az Azure Commerce szolgáltatásba küldje. Miután feltöltötte az adatokat az Azure-ba, elérheti azt a számlázási portálon vagy az Azure Resource használati API-k használatával. További információ arról, hogy milyen használati adatokat jelentettek az Azure-ban: [használati adatok jelentése](azure-stack-usage-reporting.md).  

Az alábbi ábrán a használati folyamat legfontosabb összetevői láthatók:

![Használati folyamat](media/azure-stack-billing-and-chargeback/usagepipeline.svg)

## <a name="what-usage-information-can-i-find-and-how"></a>Milyen használati információk találhatók meg, és hogyan?

Az Azure Stack hub erőforrás-szolgáltatói (például a számítási, tárolási és hálózati) használati adatokat óránként, az egyes előfizetésekhez tartozó időközönként hoznak. A használati adatok a felhasznált erőforrással kapcsolatos információkat tartalmaznak, például az erőforrás nevét, a felhasznált előfizetést és a felhasznált mennyiséget. A fogyasztásmérők azonosító erőforrásairól a [használati API – gyakori kérdések](azure-stack-usage-related-faq.md)című témakörben olvashat bővebben.

A használati adatok gyűjtése után a rendszer [jelentést küld az Azure-nak](azure-stack-usage-reporting.md) a számla létrehozásához, amelyet az Azure számlázási portálján tekinthet meg.

> [!NOTE]  
> A Azure Stack Development Kit (ASDK) és a kapacitási modellben licenccel rendelkező, Azure Stack hub integrált rendszerfelhasználók számára nem szükséges a használati adatok jelentése. A Azure Stack hub licencelésével kapcsolatos további tudnivalókért tekintse meg a [csomagolási és díjszabási adatlapot](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Az Azure számlázási portál a felszámítható erőforrások használati adatait jeleníti meg. A felszámítható erőforrásokon kívül a Azure Stack hub az erőforrások szélesebb készletére is rögzíti a használati adatokat, amelyek REST API-kon vagy PowerShell-parancsmagokon keresztül érhetők el a Azure Stack hub-környezetben. Azure Stack hub-operátorok beszerezhetik az összes felhasználói előfizetés használati adatait. Az egyes felhasználók csak a saját használati adataikat vehetik igénybe.

## <a name="usage-reporting-for-multi-tenant-cloud-solution-providers"></a>Használati jelentéskészítés a több-bérlős felhőalapú megoldás-szolgáltatók számára

Az Azure Stack hub használatával több-bérlős felhőalapú megoldás-szolgáltató (CSP) lehet, hogy külön jelentést szeretne készíteni az egyes ügyfelek használatáról, így a szolgáltató a különböző Azure-előfizetésekhez is felszámíthatja a használatot.

Minden ügyfél identitását egy másik Azure Active Directory (Azure AD) bérlő képviseli. Azure Stack hub támogatja az egyes Azure AD-bérlők egy CSP-előfizetésének hozzárendelését. Bérlőket és előfizetéseket adhat hozzá az alap Azure Stack hub-regisztrációhoz. Az alapszintű regisztráció minden Azure Stack hub-példány esetében elkészült. Ha egy előfizetés nincs regisztrálva a bérlő számára, akkor a felhasználó továbbra is használhatja Azure Stack hub-t, és a használatuk az alapregisztrációhoz használt előfizetéshez lesz küldve.

## <a name="next-steps"></a>További lépések

- [Regisztrálás Azure Stack hubhoz](azure-stack-registration.md)
- [Azure Stack hub használati adatok jelentése az Azure-ba](azure-stack-usage-reporting.md)
- [Szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md)
- [Bérlői erőforrás-használati API](azure-stack-tenant-resource-usage-api.md)
- [Használattal kapcsolatos gyakori kérdések](azure-stack-usage-related-faq.md)
