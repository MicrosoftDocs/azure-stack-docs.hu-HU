---
title: Azure Stack HCI összekötése az Azure-ba
description: Azure Stack HCI regisztrálása az Azure-ban.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/24/2020
ms.openlocfilehash: 8e5f1a07425224c8e343d4193cee2d721ae1f0b6
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92297891"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Azure Stack HCI összekötése az Azure-ba

> A következőkre vonatkozik: Azure Stack HCI v20H2

Azure Stack HCI-t Azure-szolgáltatásként szállítjuk, és az Azure Online Services használati feltételeinek megfelelően regisztrálni kell a telepítést követő 30 napon belül. Ez a témakör azt ismerteti, hogyan regisztrálhat a Azure Stack HCI-fürtöt az [Azure arc](https://azure.microsoft.com/services/azure-arc/) használatával figyelésre, támogatásra, számlázásra és hibrid szolgáltatásokra. A regisztráció során egy Azure Resource Manager erőforrás jön létre, amely az egyes helyszíni Azure Stack HCI-fürtöket jelöli, és hatékonyan kiterjeszti az Azure felügyeleti síkot Azure Stack HCI-re. A rendszer rendszeres időközönként szinkronizálja az adatokat az Azure-erőforrás és a helyszíni fürt között. 

## <a name="prerequisites-for-registration"></a>Regisztráció előfeltételei

Nem fog tudni regisztrálni az Azure-ban, amíg létre nem Azure Stack HCI-fürtöt. A csomópontok lehetnek fizikai gépek vagy virtuális gépek, de Unified Extensible Firmware Interface (UEFI) szükségesek, ami azt jelenti, hogy nem használhatja a Hyper-V 1. generációs virtuális gépeket. Az Azure arc regisztrációja Azure Stack HCI natív funkciója, így nincs szükség ügynökre.

### <a name="internet-access"></a>Internet-hozzáférés

Az Azure Stack HCI-csomópontoknak csatlakozniuk kell a felhőhöz az Azure-hoz való csatlakozás érdekében. Például egy kimenő pingelésnek sikeresnek kell lennie:

```PowerShell
C:\> ping bing.com
```

### <a name="azure-subscription"></a>Azure-előfizetés

Ha még nem rendelkezik Azure-fiókkal, [hozzon létre egyet](https://azure.microsoft.com/). 

Bármilyen típusú meglévő előfizetést használhat:
- Ingyenes fiók Azure-kreditekkel [diákoknak](https://azure.microsoft.com/free/students/) vagy [Visual Studio-előfizetőknek](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)
- [Pay-as-go](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) -előfizetés bankkártyával
- Nagyvállalati Szerződés (EA) által beszerzett előfizetés
- A Cloud Solution Provider (CSP) program keretében beszerzett előfizetés

### <a name="azure-active-directory-permissions"></a>Engedélyek Azure Active Directory

A regisztrációs folyamat befejezéséhez Azure Active Directory engedélyekre van szükség. Ha még nem rendelkezik velük, kérje meg az Azure AD-rendszergazdát, hogy adjon meg engedélyeket, vagy delegálja azokat Önnek. További információt az [Azure-regisztráció kezelése](../manage/manage-azure-registration.md#azure-active-directory-permissions) című témakörben talál.

## <a name="register-using-powershell"></a>Regisztrálás a PowerShell használatával

Az alábbi eljárás segítségével regisztrálhat egy Azure Stack HCI-fürtöt az Azure-ban:

1. Kapcsolódjon a fürtcsomópontok egyikéhez egy PowerShell-munkamenet megnyitásával és a következő parancs beírásával:

   ```PowerShell
   Enter-PSSession <server-name>
   ```

2. Telepítse a PowerShell-modult Azure Stack HCI-hez:

   ```PowerShell
   Install-WindowsFeature RSAT-Azure-Stack-HCI
   ```

3. Telepítse a szükséges parancsmagokat:

   ```PowerShell
   Install-Module Az.StackHCI
   ```

   > [!NOTE]
   > 1. Előfordulhat, hogy megjelenik egy üzenet, például "szeretné, hogy a PowerShellGet telepítse és importálja most a NuGet-szolgáltatót?" , amelyhez az igen (Y) értéket kell megadnia.
   > 2. Előfordulhat, hogy a rendszer kéri a következőt: "biztos, hogy telepíti a modulokat a" PSGallery "?", amelyhez az igen (Y) értéket kell megadnia.
   > 3. Végezetül feltételezheti, hogy a teljes az modul telepítése magában foglalja a **StackHCI** **almodult** , de ez nem így van. Az előzetes verzióban található almodulok nem szerepelnek automatikusan a standard Azure PowerShell konvenciónak megfelelően, ezért explicit módon kell megadnia az az **. StackHCI** -t a fent látható módon.

4. A tényleges regisztráció végrehajtása:

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" [-ResourceName] [-ResourceGroupName]
   ```

   Ez a szintaxis regisztrálja a helyi fürtöt (amelynek a helyi kiszolgáló a tagja), az aktuális felhasználóként az alapértelmezett Azure-régióval és felhőalapú környezettel, valamint intelligens alapértelmezett neveket használ az Azure-erőforráshoz és az erőforráscsoporthoz, de paramétereket adhat hozzá ehhez a parancshoz, ha szeretné, adja meg ezeket az értékeket.

   Ne feledje, hogy a `Register-AzStackHCI` parancsmagot futtató felhasználónak [Azure Active Directory engedélyekkel](../manage/manage-azure-registration.md#azure-active-directory-permissions)kell rendelkeznie, vagy a regisztrációs folyamat nem fejeződik be, hanem kilép, és elhagyja a regisztrációra váró rendszergazdai jóváhagyást. Az engedélyek megadása után egyszerűen futtassa újra `Register-AzStackHCI` a regisztráció befejezését.

5. Hitelesítés az Azure-ral

   A regisztrációs folyamat befejezéséhez hitelesítenie kell magát (bejelentkezést) az Azure-fiókjával. A regisztráció folytatásához a fióknak hozzáféréssel kell rendelkeznie a fenti 4. lépésben megadott Azure-előfizetéshez. Másolja a megadott kódot, navigáljon a microsoft.com/devicelogin egy másik eszközön (például a SZÁMÍTÓGÉPén vagy a telefonján), írja be a kódot, és jelentkezzen be. Ez ugyanaz a tapasztalat, ha a Microsoft más eszközökre korlátozott beviteli módokat használ, például Xbox.

A regisztrációs munkafolyamat felismeri, amikor bejelentkezett, és folytatja a befejezést. Ezután látnia kell a fürtöt a Azure Portalban.

## <a name="next-steps"></a>Következő lépések

Most már készen áll a következőkre:

- [A fürt ellenőrzése](validate.md)
- [Kötetek létrehozása](../manage/create-volumes.md)
