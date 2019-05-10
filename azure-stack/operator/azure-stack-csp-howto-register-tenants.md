---
title: Adja hozzá a bérlők használat és számlázás az Azure Stackhez |} A Microsoft Docs
description: A szükséges lépéseket a felhasználó hozzáadása egy felhőalapú szolgáltatás Felhőszolgáltató (CSP) által felügyelt Azure Stack.
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
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 5f03b80b871d3df467bc52b735432ce5568a3ad8
ms.sourcegitcommit: a78c0d143eadcab65a601746b9ea24be28091ad2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212297"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Adja hozzá a bérlői használat és számlázás az Azure Stackhez

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti a lépéseket, meg kell adnia a felhasználó egy olyan felhőalapú szolgáltatás Felhőszolgáltató (CSP) által felügyelt Azure Stack üzemelő. Ha az új bérlő erőforrást használ, az Azure Stack használati jelenti a CSP-előfizetésének.

CSP-k szolgáltatások gyakran kínálnak a több végfelhasználók számára (bérlők) az Azure Stack üzemelő példányához. Bérlők felvétele az Azure Stack-regisztráció biztosítja, hogy minden bérlő használati jelentett és a megfelelő CSP-előfizetésre számítunk fel. Nem fejeződnek be a jelen cikkben ismertetett lépések, ha bérlői használat díját a kezdeti regisztráció az Azure Stack használt előfizetés. Mielőtt a használat nyomon követése az Azure Stack egy végfelhasználói is hozzáadhat, és kezelheti a bérlőt, konfigurálnia kell az Azure Stack, egy CSP-hez. Lépések és erőforrások: [kezelése a használati és számlázási Azure stack-felhő szolgáltató](azure-stack-add-manage-billing-as-a-csp.md).

A következő ábrán látható, amelyet egy CSP-hez Azure Stack, és állítsa be a használat nyomon követése az ügyfél új ügyfél engedélyezéséhez kövesse a lépéseket. A végfelhasználó ügyfél ad hozzá, akkor is alkalmasak az Azure Stack-erőforrások kezeléséhez. Az erőforrások kezelésére két lehetősége van:

- Megőrizheti a végfelhasználó ügyfél és a helyi Azure Stack-előfizetés hitelesítő adatai a végfelhasználók számára.  
- A végfelhasználó ügyfél is együttműködik a helyileg előfizetésüket, és adja hozzá a CSP tulajdonosi engedélyekkel rendelkező vendégként.  

## <a name="add-an-end-customer"></a>Adjon hozzá egy végfelhasználói

Hajtsa végre az alábbi ábrán címsávnál hozzáadása egy végfelhasználói az alábbi lépéseket:

![Felhőszolgáltató beállítása, a használat nyomon követése és a teljes felhasználói fiók kezelése](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>A Partner Center új ügyfél létrehozása

A Partner Center hozzon létre egy új Azure-előfizetést az ügyfél számára. Útmutatásért lásd: [adjon hozzá egy új ügyfél](/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>A végfelhasználó ügyfél az Azure-előfizetés létrehozása

Miután létrehozta az ügyfél egy rekordot a Partner Center, értékesítés őket a katalógusban szereplő termékek előfizetéseinek. Útmutatásért lásd: [létrehozása, felfüggesztése, vagy szakítsa meg az ügyfél-előfizetések](/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>A teljes ügyfél címtárban Vendég felhasználó létrehozása

Ha a végfelhasználó ügyfél kezeli a saját fiókot, hozzon létre egy Vendég felhasználót a címtárban, és küldje el azokat a. A végfelhasználó majd hozzáadja a Vendég, és a Vendég számára emelt **tulajdonos** az Azure Stack CSP-fiók.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Frissítse a regisztrációt a teljes ügyfél-előfizetés

Frissítse a regisztrációt az új ügyfél-előfizetés. Az Azure az ügyfelek általi használatot a felhasználói identitás alapján Partnerközpont jelentések. Ez a lépés biztosítja, hogy minden egyes ügyfelek általi használatot jelentett adott ügyfél által az egyes CSP előfizetéshez tartozik. Ez megkönnyíti a felhasználó használat nyomon követése és a számlázás.

> [!NOTE]  
> Ez a lépés végrehajtásához rendelkeznie kell [regisztrálva az Azure Stack](azure-stack-registration.md).

1. Nyissa meg a Windows PowerShell egy rendszergazda jogú parancssort, és futtassa:  
    `Add-AzureRmAccount`
2. Írja be Azure hitelesítő adatait.
3. A PowerShell-munkamenetben futtassa:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
   ```

### <a name="new-azurermresource-powershell-parameters"></a>Új-AzureRmResource PowerShell-paramétereket

Az alábbi szakasz ismerteti a paramétereket a **New-AzureRmResource** parancsmagot:

| Paraméter | Leírás |
| --- | --- |
|registrationSubscriptionID | Az Azure-előfizetés, amely a kezdeti regisztráció az Azure Stack-lett megadva.|
| customerSubscriptionID | Az Azure-előfizetést (nem az Azure Stack) regisztrálni kell az ügyfélhez tartozó. Meg kell létrehozni a CSP-ajánlat; a gyakorlatban ez azt jelenti, hogy Partner Centeren keresztül. Ha egy ügyfél több Azure Active Directory-bérlővel rendelkezik, a bérlő Azure Stack-ba való bejelentkezéshez használt ebben az előfizetésben kell létrehozni. Az ügyfél-előfizetési azonosító kisbetűket kell használnia. |
| resourceGroup | Az erőforráscsoport az Azure-ban, amely tárolja a regisztrációt. |
| registrationName | Az Azure Stack-regisztrációk neve. Az Azure-ban tárolt objektum. |
| Tulajdonságok | Az erőforrás tulajdonságainak megadása Ez a paraméter használatával adja meg az adott erőforrástípus tulajdonságának értékét.

> [!NOTE]  
> Bérlők regisztrálni kell az egyes Azure Stack használata. Ha két Azure Stack üzembe helyezés rendelkezik, és a egy bérlőt használ, mindkettő, frissítenie kell a kezdeti regisztrációkat az egyes telepítések a bérlői előfizetéssel.

### <a name="onboard-tenant-to-azure-stack"></a>Azure Stack bérlő előkészítése

Azure Stack-szolgáltatások használatára az Azure Stackben több Azure AD-bérlő felhasználók támogatására konfigurálja. Útmutatásért lásd: [az Azure Stackben több bérlős üzemmód engedélyezése](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Hozzon létre egy helyi erőforrás a teljes ügyfél bérlő az Azure Stackben

Miután hozzáadta az új ügyfél az Azure Stackhez, vagy az end ügyfélbérlőn tulajdonosi jogosultságokkal a Vendég fiók engedélyezve van, győződjön meg arról, hogy a bérlőjükhöz hozhat létre egy erőforrást. Ha például a következőkre [Windows virtuális gép létrehozása az Azure Stack portal](../user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>További lépések

- Tekintse át a hibaüzeneteket, ha a regisztrációs folyamat során el, lásd: [bérlő regisztrációs hibaüzenetek](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
- Erőforrás-használati adatok lekérése az Azure Stack kapcsolatos további tudnivalókért lásd: [használat és számlázás az Azure Stackben](azure-stack-billing-and-chargeback.md).
- Tekintse át, hogy egy végfelhasználói előfordulhat, hogy vegye fel, a kriptográfiai Szolgáltató, mint az Azure Stack-bérlő, a kezelő lásd [engedélyezése az Azure Stack-előfizetés kezelése céljából Felhőszolgáltató](../user/azure-stack-csp-enable-billing-usage-tracking.md).
