---
title: Bérlők regisztrálása a használat nyomon követéséhez Azure Stack központban
description: Megtudhatja, hogyan regisztrálhat bérlőket, és hogyan követheti nyomon a bérlők használatát Azure Stack központban.
author: sethmanheim
ms.topic: article
ms.date: 11/17/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 11/17/2020
ms.openlocfilehash: 990ac200fdf36e951dbee9d2fed96f4918a9a8bd
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517803"
---
# <a name="register-tenants-for-usage-tracking-in-azure-stack-hub"></a>Bérlők regisztrálása a használat nyomon követéséhez Azure Stack központban

Ez a cikk a regisztrációs műveletekkel kapcsolatos részleteket tartalmazza. A következő műveletekkel végezheti el a műveleteket:

- Bérlői regisztrációk kezelése.
- Bérlői használat követésének kezelése.

## <a name="add-tenant-to-registration"></a>Bérlő hozzáadása a regisztrációhoz

Ezt a műveletet akkor használhatja, ha új bérlőt szeretne hozzáadni a regisztrációhoz. A bérlői használatot a Azure Active Directory (Azure AD) bérlőhöz kapcsolódó Azure-előfizetések jelentik.

Ezzel a művelettel a bérlőhöz társított előfizetést is módosíthatja. Hívja a PUT vagy a **New-AzResource PowerShell-** parancsmagot az előző leképezés felülírásához. Ha a AzureRM PowerShell-modult használja, használja a **New-AzureResource PowerShell-** parancsmagot.

Egyetlen Azure-előfizetést is hozzárendelhet egy bérlőhöz. Ha egy második előfizetést próbál hozzáadni egy meglévő bérlőhöz, a rendszer felülírja az első előfizetést.

### <a name="use-api-profiles"></a>API-profilok használata

A következő regisztrációs parancsmagokhoz meg kell adnia egy API-profilt a PowerShell futtatásakor. Az API-profilok Azure-erőforrás-szolgáltatókat és API-verziókat jelölnek. Segítségükkel az API megfelelő verzióját használhatja, ha több Azure-felhővel is működik. Ha például több felhővel dolgozik, amikor a globális Azure-t és Azure Stack hubot dolgozik, az API-profilok olyan nevet határoznak meg, amely megfelel a kiadási dátumnak. Az **2017-09-03** profilt használja.

Az Azure Stack hub-és API-profilokkal kapcsolatos további információkért lásd: [az API-verziók profiljainak kezelése Azure stack központban](../user/azure-stack-version-profiles.md).

### <a name="parameters"></a>Paraméterek

| Paraméter                  | Leírás |
|---                         | --- |
| registrationSubscriptionID | A kezdeti regisztrációhoz használt Azure-előfizetés. |
| customerSubscriptionID     | A regisztrálni kívánt ügyfélhez tartozó Azure-előfizetés (nem Azure Stack hub). A partner centeren keresztül kell létrehozni a Cloud Solution Provider (CSP) ajánlatban. Ha egy ügyfél több Bérlővel rendelkezik, hozzon létre egy előfizetést a bérlőnek Azure Stack hubhoz való bejelentkezéshez. Az ügyfél-előfizetés azonosítója megkülönbözteti a kis-és nagybetűket. |
| resourceGroup              | Az Azure-beli erőforráscsoport, amelyben a rendszer a regisztrációt tárolja. |
| registrationName           | Az Azure Stack hub regisztrációjának neve. Ez egy, az Azure-ban tárolt objektum. A név általában a **azurestack-CloudID** formában van, ahol a **CloudID** az Azure stack hub üzemelő példányának felhő-azonosítója. |

> [!NOTE]  
> A bérlőknek regisztrálniuk kell az összes általuk használt Azure Stack hub-telepítéssel. Ha egy bérlő egynél több Azure Stack hubot használ, frissítse az egyes központi telepítések kezdeti regisztrációját a bérlői előfizetéssel.


### <a name="powershell"></a>PowerShell
### <a name="az-modules"></a>[Az modulok](#tab/az1)

Bérlő hozzáadásához használja a **New-AzResource** parancsmagot. [Kapcsolódjon az Azure-hoz](/powershell/azure/get-started-azureps), majd egy emelt szintű parancssorból futtassa a következő parancsot:

```powershell  
New-AzResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```
### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm1)

Bérlő hozzáadásához használja a **New-AzureRmResource** parancsmagot. [Kapcsolódjon az Azure-hoz](/powershell/azure/get-started-azureps), majd egy emelt szintű parancssorból futtassa a következő parancsot:

```powershell  
New-AzResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

---
### <a name="api-call"></a>API-hívás

**Művelet**: Put  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Válasz**: 201 létrehozva  
**Válasz törzse**: üres  

## <a name="list-all-registered-tenants"></a>Az összes regisztrált bérlő listázása

A regisztrációhoz hozzáadott összes bérlő listájának beolvasása.

 > [!NOTE]  
 > Ha nincs regisztrálva bérlő, nem kap választ.

### <a name="parameters"></a>Paraméterek

| Paraméter                  | Leírás          |
|---                         | ---                  |
| registrationSubscriptionId | A kezdeti regisztrációhoz használt Azure-előfizetés.   |
| resourceGroup              | Az Azure-beli erőforráscsoport, amelyben a rendszer a regisztrációt tárolja.    |
| registrationName           | Az Azure Stack hub-telepítés regisztrációjának neve. Ez egy, az Azure-ban tárolt objektum. A név általában **azurestack-CloudID** formában van, ahol a **CloudID** az Azure stack hub üzemelő példányának felhő-azonosítója.   |

### <a name="powershell"></a>PowerShell

### <a name="az-modules"></a>[Az modulok](#tab/az2)

Használja a **Get-AzResource** parancsmagot az összes regisztrált bérlő listázásához. [Kapcsolódjon Azure stack hubhoz](azure-stack-powershell-configure-admin.md), majd egy emelt szintű parancssorból futtassa a következő parancsmagot:

```powershell
Get-AzResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm2)

Használja a **Get-AzureRMResource** parancsmagot az összes regisztrált bérlő listázásához. [Kapcsolódjon Azure stack hubhoz](azure-stack-powershell-configure-admin.md), majd egy emelt szintű parancssorból futtassa a következő parancsmagot:

```powershell
Get-AzureRMResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

---
### <a name="api-call"></a>API-hívás

A lekérési művelettel lekérheti az összes bérlői hozzárendelés listáját.

**Művelet**: Get  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?api-version=2017-06-01 HTTP/1.1`  
**Válasz**: 200  
**Válasz törzse**:

```json
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

## <a name="remove-a-tenant-mapping"></a>Bérlői leképezés eltávolítása

Törölheti a regisztrációhoz hozzáadott bérlőket. Ha a bérlő továbbra is az Azure Stack hub erőforrásait használja, a használatuk a kezdeti Azure Stack hub-regisztráció során használt előfizetésre lesz terhelve.

### <a name="parameters"></a>Paraméterek

| Paraméter                  | Leírás          |
|---                         | ---                  |
| registrationSubscriptionId | A regisztrációhoz tartozó előfizetés-azonosító.   |
| resourceGroup              | A regisztrációhoz használt erőforráscsoport.   |
| registrationName           | A regisztráció neve.  |
| customerSubscriptionId     | Az ügyfél-előfizetés azonosítója. Az ügyfél-előfizetés azonosítója megkülönbözteti a kis-és nagybetűket.  |

### <a name="powershell"></a>PowerShell

### <a name="az-modules"></a>[Az modulok](#tab/az3)

Bérlő eltávolításához használja a **Remove-AzResource** parancsmagot. [Kapcsolódjon Azure stack hubhoz](azure-stack-powershell-configure-admin.md), majd egy emelt szintű parancssorból futtassa a következő parancsmagot:

```powershell
Remove-AzResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm3)

Bérlő eltávolításához használja a **Remove-AzureRMResource** parancsmagot. [Kapcsolódjon Azure stack hubhoz](azure-stack-powershell-configure-admin.md), majd egy emelt szintű parancssorból futtassa a következő parancsmagot:

```powershell
Remove-AzureRMResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

---

### <a name="api-call"></a>API-hívás

A TÖRLÉSi művelettel eltávolíthatja a bérlői leképezéseket.

**Művelet**: törlés  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Válasz**: 204 nincs tartalom  
**Válasz törzse**: üres

## <a name="next-steps"></a>Következő lépések

- [Erőforrás-használati adatok lekérése Azure Stack hub-ból](azure-stack-billing-and-chargeback.md)
