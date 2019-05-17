---
title: Regisztrálja a bérlők a használat nyomon követése az Azure Stackben |} A Microsoft Docs
description: Bérlői regisztráció, és hogyan Azure Stack bérlő használat nyomon követése kezelésére szolgáló műveletek részleteit.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: faacd3faa954c1cad76bdc4601020e5d6c547537
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782245"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Az Azure Stack bérlő regisztrációs kezelése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk a regisztráció műveletek részleteit tartalmazza. Ezeket a műveleteket, használhatja:
- Bérlő regisztrációk kezelése
- Kezelheti a bérlői használat nyomon követése

Adja hozzá a listában, vagy távolítsa el a bérlő leképezések részleteit is megtalálhatja. Használhatja PowerShell vagy a számlázási API-végpontokat kezelheti a használat nyomon követése. Adja hozzá a listában, vagy távolítsa el a bérlő leképezések részleteit is megtalálhatja. Használhatja PowerShell vagy a számlázási API-végpontokat kezelheti a használat nyomon követése.

## <a name="add-tenant-to-registration"></a>A regisztrációhoz bérlő hozzáadása

Ha a regisztráció ad hozzá egy új bérlőt szeretne, használja a műveletet. A bérlők jelentett csatlakozik az Azure Active Directory (Azure AD) bérlő az Azure-előfizetés alatt.

Ha meg szeretné változtatni az előfizetés a bérlőhöz tartozó, használhatja a műveletet. PUT és New-AzureRMResource felülírja az előző leképezési hívjuk.

Egy Azure-előfizetéssel is társíthat egy bérlőt. Ha előfizetést szeretne hozzáadni egy második meglévő bérlő, az első előfizetés túlterhelt írásos.

### <a name="use-api-profiles"></a>API-profilok használatához

A regisztrációs parancsmagok futtatásához szükséges, adjon meg egy API-profilt, amikor futtatja a Powershellt. API-profilok az Azure erőforrás-szolgáltatók és az API-verziók csoportját képviselik. Ezek segítenek megfelelő verzióját használja, az API-t több Azure-felhők való interakció során. Például azt használni több felhőt az Azure és az Azure Stack globális használatakor. Profilok adjon meg egy nevet, amely megfelel a kiadási dátum. Meg kell használnia a **2017-09-03** profilt.

Az Azure Stacket és API-profilokkal kapcsolatos további információkért lásd: [kezelése API-verzióprofilok az Azure Stackben](../user/azure-stack-version-profiles.md).

### <a name="parameters"></a>Paraméterek

| Paraméter                  | Leírás |
|---                         | --- |
| registrationSubscriptionID | Az Azure-előfizetés, amely a kezdeti regisztráció lett megadva. |
| customerSubscriptionID     | Az Azure-előfizetést (nem az Azure Stack) regisztrálni kell az ügyfélhez tartozó. Létre kell hozni a Felhőszolgáltató (CSP) szerződéssel Partner Centeren keresztül. Ha egy ügyfél egynél több bérlőhöz, létrehozott egy előfizetést, jelentkezzen be az Azure Stack a bérlő rendelkezik. |
| resourceGroup              | Az erőforráscsoport az Azure-ban, amely tárolja a regisztrációt. |
| registrationName           | Az Azure Stack-regisztrációk neve. Az Azure-ban tárolt objektum. A név általában az űrlap azurestack-CloudID, ahol CloudID-e az Azure Stack üzembe helyezés a Felhőbeli azonosító van. |

> [!Note]  
> A bérlőknek kell regisztrálni kell az egyes Azure Stack használata. Ha egy bérlőt használ több Azure Stack, frissítenie a kezdeti regisztrációkat az egyes telepítések a bérlői előfizetéssel.

### <a name="powershell"></a>PowerShell

A New-AzureRmResource-parancsmag segítségével adjon hozzá egy bérlőt. [Csatlakozás az Azure Stack](azure-stack-powershell-configure-admin.md), és a egy rendszergazda jogú parancssorból a következő parancsmag használatával:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API-hívás

**A művelet**: PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Válasz**: 201 Created  
**Választörzs**: Üres  

## <a name="list-all-registered-tenants"></a>Az összes regisztrált bérlők a listában

Egyetlen bérlő számára, amely egy regisztrációs lettek hozzáadva listájának beolvasása.

 > [!Note]  
 > Ha nem-bérlő regisztrálva, akkor nem kap választ.

### <a name="parameters"></a>Paraméterek

| Paraméter                  | Leírás          |
|---                         | ---                  |
| registrationSubscriptionId | Az Azure-előfizetés, amely a kezdeti regisztráció lett megadva.   |
| resourceGroup              | Az erőforráscsoport az Azure-ban, amely tárolja a regisztrációt.    |
| registrationName           | Az Azure Stack-regisztrációk neve. Az Azure-ban tárolt objektum. A név általában formájában **azurestack**-***CloudID***, ahol ***CloudID*** Cloud az Azure Stack üzemelő példány azonosítója.   |

### <a name="powershell"></a>PowerShell

A Get-AzureRmResource parancsmaggal listázhatja az összes regisztrált bérlők. [Csatlakozás az Azure Stack](azure-stack-powershell-configure-admin.md), és a egy rendszergazda jogú parancssorból a következő parancsmag használatával:

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API-hívás

Megtekintheti a GET művelettel az összes bérlő hozzárendelések listáját.

**A művelet**: GET  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Válasz**: 200  
**Választörzs**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}",
            "name": " cspSubscriptionId 1",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}",
            "name": " cspSubscriptionId2 ",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Egy bérlő leképezés eltávolítása

Egy bérlő, amely hozzá van adva egy regisztrációs távolíthatja el. A bérlői továbbra is az erőforrások felhasználását az Azure Stacken, ha azok használatáért a kezdeti Azure Stack-regisztráció használt előfizetés.

### <a name="parameters"></a>Paraméterek

| Paraméter                  | Leírás          |
|---                         | ---                  |
| registrationSubscriptionId | A regisztráció előfizetés-azonosítója.   |
| resourceGroup              | Az erőforráscsoport, a regisztráció.   |
| registrationName           | A regisztráció neve.  |
| customerSubscriptionId     | Az ügyfél-előfizetés azonosítóját.  |

### <a name="powershell"></a>PowerShell

A Remove-AzureRmResource-parancsmag segítségével távolítsa el a bérlőt. [Csatlakozás az Azure Stack](azure-stack-powershell-configure-admin.md), és a egy rendszergazda jogú parancssorból a következő parancsmag használatával:

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API-hívás

Bérlő leképezések a törlési művelet használatával távolíthatja el.

**A művelet**: DELETE  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Válasz**: 204 Nincs tartalom  
**Választörzs**: Üres

## <a name="next-steps"></a>További lépések

 - Erőforrás-használati adatok lekérése az Azure Stack kapcsolatos további tudnivalókért lásd: [használat és számlázás az Azure Stackben](azure-stack-billing-and-chargeback.md).
