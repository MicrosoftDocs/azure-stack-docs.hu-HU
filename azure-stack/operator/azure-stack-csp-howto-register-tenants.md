---
title: Bérlők hozzáadása a Azure Stack hubhoz való használathoz és számlázáshoz
description: Megtudhatja, hogyan adhat hozzá bérlőt a használathoz és a számlázáshoz Azure Stack hub-ra.
author: sethmanheim
ms.topic: article
ms.date: 04/24/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: d5a846d762d0dab8d07a16c7a7b6f147d8a92324
ms.sourcegitcommit: e5b587216a137819444680ec619281c90f37bad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82167024"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack-hub"></a>Bérlő hozzáadása a Azure Stack hubhoz való használathoz és számlázáshoz

Ez a cikk azt ismerteti, hogyan adhat hozzá bérlőt egy felhőalapú megoldás-szolgáltató (CSP) által kezelt Azure Stack hub-telepítéshez. Ha az új bérlő erőforrásokat használ, Azure Stack hub a CSP-előfizetését használja.

A kriptográfiai szolgáltatók gyakran kínálnak szolgáltatásokat több végfelhasználónak (bérlőnek) a Azure Stack hub üzembe helyezésén. A bérlők Azure Stack hub-regisztrációhoz való hozzáadásával biztosítható, hogy az egyes bérlők használatának jelentése és számlázása a megfelelő CSP-előfizetéssel történjen. Ha nem hajtja végre a jelen cikkben ismertetett lépéseket, a bérlői használatért az Azure Stack hub kezdeti regisztrációjában használt előfizetésre kell fizetni. Mielőtt felveszi a végfelhasználót Azure Stack hub-ba a használat nyomon követéséhez és a bérlők kezeléséhez, konfigurálnia kell Azure Stack hub-t CSP-ként. A lépéseket és erőforrásokat lásd: a [Azure stack hub használatának és számlázásának kezelése felhőalapú megoldási szolgáltatóként](azure-stack-add-manage-billing-as-a-csp.md).

Az alábbi ábra azokat a lépéseket mutatja be, amelyekkel a CSP-nek követnie kell, hogy az új végfelhasználók Azure Stack hubot használják, valamint az ügyfél használati követésének beállításához. A végfelhasználók hozzáadásával a Azure Stack hub erőforrásai is kezelhetők. Az erőforrások kezeléséhez két lehetősége van:

- Megtarthatja a végfelhasználót, és megadhatja a helyi Azure Stack hub-előfizetéshez tartozó hitelesítő adatokat a végfelhasználó számára.  
- A végfelhasználó az előfizetését helyileg is használhatja, és a CSP-t a tulajdonosi engedélyekkel rendelkező vendégként adja hozzá.

## <a name="add-an-end-customer"></a>Végfelhasználó hozzáadása

A végfelhasználók hozzáadása előtt engedélyeznie kell a több-bérlős számlázást a regisztrációnál. A több-bérlős számlázás engedélyezéséhez küldje el `azstcsp@microsoft.com`a regisztrációs előfizetés azonosítóját, az erőforráscsoport nevét és a regisztrációs nevet a következőnek:. Általában 1-2 munkanapot vesz igénybe, hogy lehetővé tegye a több-bérlőt.

A következő ábrán látható módon vegyen fel egy végfelhasználót az alábbi lépésekkel:

![Felhőalapú megoldás-szolgáltató beállítása a használat nyomon követéséhez és a végfelhasználói fiók kezeléséhez](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Új ügyfél létrehozása a partner Centerben

A partner Centerben hozzon létre egy új Azure-előfizetést az ügyfél számára. Útmutatásért lásd: [új ügyfél hozzáadása](/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Azure-előfizetés létrehozása a végfelhasználók számára

Miután létrehozta az ügyfelet a partner Centerben, értékesítheti a katalógusban lévő termékek előfizetéseit. Útmutatásért tekintse meg az [ügyfél-előfizetések létrehozása, felfüggesztése vagy megszakítása](/partner-center/create-a-new-subscription)című témakört.

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Vendég felhasználó létrehozása a végfelhasználói címtárban

Alapértelmezés szerint Ön, mint CSP, nem férhet hozzá a végfelhasználó Azure Stack hub-előfizetéséhez. Ha azonban az ügyfél szeretné kezelni erőforrásaikat, akkor a fiókját tulajdonosként vagy közreműködőként adhatja hozzá a Azure Stack hub-előfizetéséhez. Ehhez hozzá kell adnia a fiókját vendég felhasználóként az Azure AD-bérlőhöz. Javasoljuk, hogy az Azure CSP-fiókjától eltérő fiókot használjon az ügyfél Azure Stack hub-előfizetés kezeléséhez, hogy ne veszítse el az ügyfél Azure-előfizetéséhez való hozzáférést.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>A regisztráció frissítése a végfelhasználói előfizetéssel

Frissítse a regisztrációt az új ügyfél-előfizetéssel. Az Azure a partner Center ügyfél-identitását használva jelenti az ügyfelek használatát. Ezzel a lépéssel biztosítható, hogy minden ügyfél használati adatait az adott ügyfél egyéni CSP-előfizetése alatt jelentse. Így egyszerűbbé válik a használat és a számlázás. Ennek a lépésnek a végrehajtásához először [regisztrálnia kell Azure stack hubot](azure-stack-registration.md).

1. Nyisson meg egy emelt szintű parancssorban a Windows PowerShellt, és futtassa a következőket:  

   ```powershell
   Add-AzureRmAccount
   ```

   >[!NOTE]
   > Ha a munkamenet lejár, a jelszó módosult, vagy egyszerűen csak szeretné váltani a fiókokat, az **Add-AzureRmAccount**használatával történő bejelentkezés előtt futtassa a következő parancsmagot `Remove-AzureRmAccount-Scope Process`:.

2. Adja meg az Azure-beli hitelesítő adatait.
3. A PowerShell-munkamenetben futtassa a következőket:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
   ```

### <a name="new-azurermresource-powershell-parameters"></a>New-AzureRmResource PowerShell-paraméterek

A következő szakasz a **New-AzureRmResource** parancsmag paramétereit ismerteti:

| Paraméter | Leírás |
| --- | --- |
|registrationSubscriptionID | Az Azure Stack hub kezdeti regisztrálásához használt Azure-előfizetés.|
| customerSubscriptionID | A regisztrálni kívánt ügyfélhez tartozó Azure-előfizetés (nem Azure Stack hub). Létre kell hozni a CSP-ajánlatban. A gyakorlatban ez a partner centeren keresztül történik. Ha egy ügyfél több Azure Active Directory Bérlővel rendelkezik, ezt az előfizetést az Azure Stack hub-ba való bejelentkezéshez használt bérlőben kell létrehozni. Az ügyfél-előfizetés AZONOSÍTÓjának kisbetűs karaktereket kell használnia. |
| resourceGroup | Az Azure-beli erőforráscsoport, amelyben a rendszer a regisztrációt tárolja. |
| registrationName | Az Azure Stack hub regisztrációjának neve. Ez egy, az Azure-ban tárolt objektum. 

> [!NOTE]  
> A bérlőket az általuk használt Azure Stack-hubhoz kell regisztrálni. Ha két Azure Stack hub üzemelő példánya van, és a bérlő mindkettőt használja, frissítenie kell az egyes üzemelő példányok kezdeti regisztrációját a bérlői előfizetéssel.

### <a name="onboard-tenant-to-azure-stack-hub"></a>Bérlő beléptetése Azure Stack hubhoz

Konfigurálja Azure Stack hub-t több Azure AD-bérlő felhasználóinak támogatásához Azure Stack hub szolgáltatásainak használatához. Útmutatásért lásd: [a több-bérlő engedélyezése Azure stack központban](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack-hub"></a>Helyi erőforrás létrehozása a Azure Stack hub végfelhasználói bérlője

Miután hozzáadta az új ügyfelet Azure Stack hubhoz, vagy a végfelhasználói bérlő engedélyezte a vendég fiókját a tulajdonosi jogosultságokkal, ellenőrizze, hogy létrehozhat-e erőforrást a bérlőben. [Létrehozhatnak például egy Windows rendszerű virtuális gépet az Azure stack hub portál](../user/azure-stack-quick-windows-portal.md)használatával.

## <a name="next-steps"></a>További lépések

- Ha szeretné áttekinteni a regisztrációs folyamat során elindított hibaüzeneteket, tekintse meg a [bérlői regisztrációs hibaüzeneteket](azure-stack-registration-errors.md).
- Ha többet szeretne megtudni a Azure Stack hub erőforrás-használati adatainak lekéréséről, tekintse meg [a használat és a számlázás Azure stack hub-ban](azure-stack-billing-and-chargeback.md)című témakört.
- Ha szeretné áttekinteni, hogy a végfelhasználók Hogyan vehetik fel Önt, a CSP-t a Azure Stack hub-bérlőhöz vezetőként tekintse meg a [felhőalapú megoldások szolgáltatójának engedélyezése a Azure stack hub-előfizetés kezeléséhez](../user/azure-stack-csp-enable-billing-usage-tracking.md)című témakört.
