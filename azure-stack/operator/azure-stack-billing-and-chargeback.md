---
title: A számlázás a vásárlók és a költséghelyi elszámolás az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan kérheti le az erőforrás-használati adatokat az Azure Stack.
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
ms.date: 03/21/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 8e47022d826d5983369fe7849cbde8c00f18838e
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64290978"
---
# <a name="usage-and-billing-in-azure-stack"></a>Használat és számlázás az Azure Stackben

Ez a cikk bemutatja, hogyan Azure Stack felhasználói díjköteles erőforrás-használatot, és hogyan érhető el a számlázási adatokat analytics és a díj vissza.

Az Azure Stack gyűjt, és a csoportok által használt erőforrások használati adatait. Ezután az Azure Stack az Azure kereskedelmi továbbítja ezeket az adatokat. Az Azure kereskedelmi verziójánál díjat Azure Stack-használat az Azure-használat bankkártyáján, azonos módon.

Használati adatok és az exportálás, hogy a saját számlázási vagy költséghelyi elszámolási adapter használatával biztonsági másolatot a rendszer, vagy exportálhatja, és a egy üzleti intelligencia eszköz, például a Microsoft Power BI is kaphat.

## <a name="usage-pipeline"></a>Használati folyamat

Mindegyik erőforrás-szolgáltató az Azure Stackben közzéteszi a használati adatok erőforrás-használat. Rendszeres időközönként a használatmérő szolgáltatást (óránként, és naponta) összesíti használati adatait, és a használati adatbázisban tárolja azokat. Az Azure Stack operátorai és felhasználói tárolt használati adatok hozzáférhet az Azure Stack erőforrás-használati API-k használatával.

Ha rendelkezik [regisztrálva az Azure Stack-példány az Azure-ral](azure-stack-registration.md ), az Azure Stack a használati adatok küldése az Azure kereskedelmi van konfigurálva. Az adatok feltöltése az Azure-ba, után is férhet hozzá a számlázási portálon keresztül vagy az Azure erőforrás-használati API-k használatával. Milyen használati adatokat az Azure-bA jelentett kapcsolatos további információkért lásd: [használati adatok jelentése](azure-stack-usage-reporting.md).  

Az alábbi képen látható a használati folyamat a legfontosabb összetevők:

![Használati folyamat](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Milyen használati adatokat is megkeresheti, és hogyan?

Az Azure Stack erőforrás-szolgáltatók (például számítási, tárolási és hálózati), az egyes előfizetésekhez óránkénti időközönként használati adatok létrehozásához. A használati adatok használja, mint például az erőforrás nevét, a használt előfizetés és a felhasznált mennyiség az erőforrással kapcsolatos információkat tartalmazza. A mérőszámok esetében ID erőforrások kapcsolatos további információkért tekintse meg a [használati API – gyakori kérdések](azure-stack-usage-related-faq.md).

A használati adatok begyűjtését követően van [jelentett az Azure-bA](azure-stack-usage-reporting.md) számla, amely az Azure számlázási portálján tekinthetők létrehozásához.

> [!NOTE]  
> Használati adatok jelentése, nem szükséges, az az Azure Stack Development Kit (ASDK) és a kapacitás modellben licenc Azure Stackkel integrált rendszer felhasználók. Az Azure Stackben licenceléssel kapcsolatos további tudnivalókért tekintse meg a [formátumokat támogató csomagolási és díjszabás adatlap](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Az Azure számlázási portálján felszámítható erőforrásokat használati adatait jeleníti meg. Mellett a felszámítható erőforrásokat az Azure Stack erőforrásokat, amelyek keresztül elérhető REST API-k vagy a PowerShell-parancsmagok az Azure Stack környezettel szélesebb körű készletéhez használati adatait rögzíti. Az Azure Stack-operátorok a használati adatokat kaphat az összes felhasználói előfizetések. Egyéni felhasználók számára csak beszerezheti a saját használati adatait.

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Jelentéskészítés a több-bérlős Felhőszolgáltatók használat

Egy több-bérlős Felhőszolgáltató (CSP) rendelkező Azure Stack használatával számos ügyfél érdemes külön-külön mindegyik ügyfél használati jelentést, hogy a szolgáltató az Azure-előfizetések használati díjat.

Minden ügyfél-azonosítót fog kérni egy másik Azure Active Directory (Azure AD) bérlő által képviselt rendelkezik. Az Azure Stack egy CSP előfizetés hozzárendelése minden egyes Azure AD-bérlő támogatja. Az alapszintű Azure Stack-regisztráció a bérlők és azok az előfizetések adhat hozzá. Az alap regisztrációját, készen áll az összes Azure Stack-példányok. Ha egy előfizetés nincs regisztrálva a bérlő számára, a felhasználó továbbra is használhatja az Azure Stack, és használatuk küld a kiinduló regisztráció használt előfizetés.

## <a name="next-steps"></a>További lépések

- [Regisztráljon az Azure Stack használatával](azure-stack-registration.md)
- [Az Azure Stack használati adatainak jelentése az Azure-nak](azure-stack-usage-reporting.md)
- [Szolgáltatói erőforrás-használati API](azure-stack-provider-resource-api.md)
- [Bérlői erőforrás-használati API](azure-stack-tenant-resource-usage-api.md)
- [Használattal kapcsolatos gyakori kérdések](azure-stack-usage-related-faq.md)