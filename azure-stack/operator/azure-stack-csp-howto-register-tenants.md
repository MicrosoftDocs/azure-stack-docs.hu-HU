---
title: Bérlők hozzáadása a használathoz és a számlázáshoz Azure Stack | Microsoft Docs
description: A szükséges lépésekkel hozzáadhat egy végfelhasználót egy felhőalapú szolgáltató (CSP) által kezelt Azure Stackhoz.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 06/07/2019
ms.openlocfilehash: 9f35a2bef6e5aa3b9ae1866927be007d58532b74
ms.sourcegitcommit: 5703255b4647ff0ebec23658a3f5c25d67f076a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70749963"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Bérlő hozzáadása a használathoz és a számlázáshoz Azure Stack

*Vonatkozik: Integrált rendszerek Azure Stack*

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek ahhoz, hogy egy végfelhasználót egy felhőalapú szolgáltató (CSP) által kezelt Azure Stack-telepítéshez vegyen fel. Amikor az új bérlő erőforrásokat használ, Azure Stack a jelentések használatát a CSP-előfizetéséhez.

A kriptográfiai szolgáltatók gyakran kínálnak szolgáltatásokat több végfelhasználónak (bérlőnek) a Azure Stack üzembe helyezésük során. Ha bérlőket ad hozzá a Azure Stack-regisztrációhoz, azzal biztosítja, hogy minden bérlői használatot jelenteni kell, és a megfelelő CSP-előfizetésnek kell fizetnie. Ha nem hajtja végre a cikkben leírt lépéseket, a bérlői használatért a Azure Stack első regisztrációjában használt előfizetésre kell fizetni. Ahhoz, hogy a végfelhasználók Azure Stack a használat nyomon követéséhez és a bérlők felügyeletéhez, a Azure Stackt CSP-ként kell konfigurálnia. A lépéseket és erőforrásokat lásd: a [használat és a számlázás kezelése Azure stack felhőalapú](azure-stack-add-manage-billing-as-a-csp.md)szolgáltatóként.

Az alábbi ábra azokat a lépéseket mutatja be, amelyekkel a CSP-nek követnie kell, hogy egy új ügyfél használhassa a Azure Stack, valamint az ügyfél használati követésének beállítását. A végfelhasználók hozzáadásával a Azure Stack erőforrásai is kezelhetők. Az erőforrások kezeléséhez két lehetősége van:

- Megtarthatja a végfelhasználót, és megadhatja a végfelhasználó számára a helyi Azure Stack előfizetéshez tartozó hitelesítő adatokat.  
- A végfelhasználó az előfizetését helyileg is használhatja, és a CSP-t a tulajdonosi engedélyekkel rendelkező vendégként adja hozzá.  

## <a name="add-an-end-customer"></a>Végfelhasználó hozzáadása

A következő ábrán látható módon vegyen fel egy végfelhasználót az alábbi lépésekkel:

![A felhőalapú szolgáltató beállítása a használat nyomon követéséhez és a végfelhasználói fiók kezeléséhez](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Új ügyfél létrehozása a partner Centerben

A partner Centerben hozzon létre egy új Azure-előfizetést az ügyfél számára. Útmutatásért lásd: [új ügyfél hozzáadása](/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Azure-előfizetés létrehozása a végfelhasználók számára

Miután létrehozta az ügyfelet a partner Centerben, értékesítheti a katalógusban lévő termékek előfizetéseit. Útmutatásért tekintse meg az [ügyfél-előfizetések létrehozása, felfüggesztése vagy megszakítása](/partner-center/create-a-new-subscription)című témakört.

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Vendég felhasználó létrehozása a végfelhasználói címtárban

Alapértelmezés szerint Ön, mint CSP, nem férhet hozzá a végfelhasználói Azure Stack előfizetéséhez. Ha azonban az ügyfél szeretné kezelni erőforrásaikat, akkor a fiókját tulajdonosként vagy közreműködőként felveheti a Azure Stack-előfizetésbe. Ehhez hozzá kell adnia a fiókját vendég felhasználóként a HRE-bérlőhöz. Javasoljuk, hogy az Azure CSP-fiókjától eltérő fiókot használjon az ügyfél Azure Stack-előfizetésének kezeléséhez, így biztosítva, hogy ne veszítse el az ügyfél Azure-előfizetésének elérését.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>A regisztráció frissítése a végfelhasználói előfizetéssel

Frissítse a regisztrációt az új ügyfél-előfizetéssel. Az Azure a partner Center ügyfél-identitását használva jelenti az ügyfelek használatát. Ezzel a lépéssel biztosítható, hogy minden ügyfél használati adatait az adott ügyfél egyéni CSP-előfizetése alatt jelentse. Így egyszerűbbé válik a felhasználók használatának és számlázásának nyomon követése.

> [!NOTE]  
> Ennek a lépésnek a végrehajtásához először [regisztrálnia kell Azure stack](azure-stack-registration.md).

1. Nyissa meg a Windows PowerShellt egy emelt szintű parancssorral, és futtassa a következőket:  
    `Add-AzureRmAccount`
2. Adja meg az Azure-beli hitelesítő adatait.
3. A PowerShell-munkamenetben futtassa a következőket:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
   ```

### <a name="new-azurermresource-powershell-parameters"></a>New-AzureRmResource PowerShell-paraméterek

A következő szakasz a **New-AzureRmResource** parancsmag paramétereit ismerteti:

| Paraméter | Leírás |
| --- | --- |
|registrationSubscriptionID | A Azure Stack első regisztrálásához használt Azure-előfizetés.|
| customerSubscriptionID | A regisztrálni kívánt ügyfélhez tartozó Azure-előfizetés (nem Azure Stack). Létre kell hozni a CSP-ajánlatban; a gyakorlatban ez a partner centeren keresztül történik. Ha egy ügyfél több Azure Active Directory Bérlővel rendelkezik, ezt az előfizetést a Azure Stackba való bejelentkezéshez használt bérlőben kell létrehozni. Az ügyfél-előfizetés AZONOSÍTÓjának kisbetűs karaktereket kell használnia. |
| resourceGroup | Az Azure-beli erőforráscsoport, amelyben a rendszer a regisztrációt tárolja. |
| registrationName | A Azure Stack regisztrációjának neve. Ez egy, az Azure-ban tárolt objektum. |
| properties | Megadja az erőforrás tulajdonságait. Ezzel a paraméterrel adhatja meg az erőforrástípus jellemző tulajdonságok értékeit.

> [!NOTE]  
> A bérlőket regisztrálni kell az általuk használt összes Azure Stack. Ha két Azure Stack üzemelő példánya van, és a bérlő mindkettőt használja, frissítenie kell az egyes üzemelő példányok kezdeti regisztrációját a bérlői előfizetéssel.

### <a name="onboard-tenant-to-azure-stack"></a>Bérlő bevezetése Azure Stack

A Azure Stack konfigurálható úgy, hogy több Azure AD-bérlő felhasználói számára is támogassa a Azure Stack szolgáltatások használatát. Útmutatásért lásd: [a több-bérlő engedélyezése a Azure Stackban](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Helyi erőforrás létrehozása a Azure Stack végfelhasználói bérlőben

Miután hozzáadta az új ügyfelet a Azure Stackhoz, vagy a végfelhasználói bérlő engedélyezte a vendég fiókját a tulajdonosi jogosultságokkal, ellenőrizze, hogy létrehozhat-e erőforrást a bérlőben. [Létrehozhatnak például egy Windows rendszerű virtuális gépet a Azure stack-portálon](../user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>További lépések

- Ha a regisztrációs folyamat során aktiválják a hibaüzeneteket, tekintse át a [bérlői regisztrációs hibaüzeneteket](azure-stack-registration-errors.md).
- Ha többet szeretne megtudni a Azure Stack erőforrás-használati adatok lekéréséről, tekintse meg a [használat és a számlázás Azure Stackban](azure-stack-billing-and-chargeback.md)című témakört.
- Ha szeretné áttekinteni, hogy a végfelhasználók Hogyan vehetik fel Önt, mint a CSP-t a Azure Stack bérlője vezetőjeként, tekintse meg a [felhőalapú szolgáltató engedélyezése a Azure stack-előfizetés kezeléséhez](../user/azure-stack-csp-enable-billing-usage-tracking.md)című témakört.
