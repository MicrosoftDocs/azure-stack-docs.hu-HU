---
title: Az Azure Stack HCI csatlakoztatása az Azure-hoz
description: Azure Stack HCI-fürtök regisztrálása az Azure-ban.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/10/2020
ms.openlocfilehash: 3711a0e11bac59f00ce51027ea9544f6858dd297
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487323"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Az Azure Stack HCI csatlakoztatása az Azure-hoz

> A következőkre vonatkozik: Azure Stack HCI v20H2

Azure Stack HCI-t Azure-szolgáltatásként szállítjuk, és az Azure Online Services használati feltételeinek megfelelően regisztrálni kell a telepítést követő 30 napon belül. Ez a témakör azt ismerteti, hogyan regisztrálhat a Azure Stack HCI-fürtöt az [Azure arc](https://azure.microsoft.com/services/azure-arc/) használatával figyelésre, támogatásra, számlázásra és hibrid szolgáltatásokra. A regisztráció során egy Azure Resource Manager erőforrás jön létre, amely az egyes helyszíni Azure Stack HCI-fürtöket jelöli, és hatékonyan kiterjeszti az Azure felügyeleti síkot Azure Stack HCI-re. A rendszer rendszeres időközönként szinkronizálja az adatokat az Azure-erőforrás és a helyszíni fürt (ek) között. Az Azure arc-regisztráció a Azure Stack HCI operációs rendszer natív funkciója, így a regisztrációhoz nincs szükség ügynökre.

   > [!IMPORTANT]
   > Az Azure-ban való regisztrálás kötelező, és a fürt nem teljes mértékben támogatott, amíg a regisztráció nem aktív. Ha az üzembe helyezés során nem regisztrálja a fürtöt az Azure-ban, vagy ha a fürt regisztrálva van, de 30 napnál nem kapcsolódik az Azure-hoz, a rendszer nem engedélyezi az új virtuális gépek (VM-EK) létrehozását vagy hozzáadását. Ebben az esetben a következő hibaüzenet jelenik meg, amikor virtuális gépeket próbál létrehozni:
   >
   > *Nem sikerült konfigurálni a virtuális gépi szerepkört a következőhöz: "vmname". A feladatot nem sikerült végrehajtani. Hiba történt a (z) vmname fürtözött szerepkörök megnyitásakor. Az elérni kívánt szolgáltatás adott számú kapcsolatra van licenccel. Jelenleg nem hozhatók létre több kapcsolat a szolgáltatással, mert már annyi kapcsolat van, amennyit a szolgáltatás elfogad.*
   >
   > A megoldás az, hogy engedélyezze a kimenő kapcsolatot az Azure-ban, és ellenőrizze, hogy a fürt regisztrálva van-e a témakörben leírtak szerint.

## <a name="prerequisites-for-registration"></a>Regisztráció előfeltételei

Nem fog tudni regisztrálni az Azure-ban, amíg létre nem Azure Stack HCI-fürtöt. Ahhoz, hogy a fürt támogatni lehessen a fürtöt, a fürt csomópontjainak fizikai kiszolgálóknak kell lenniük. A virtuális gépek teszteléshez használhatók, de támogatniuk kell Unified Extensible Firmware Interface (UEFI), ami azt jelenti, hogy nem használhatja a Hyper-V 1. generációs virtuális gépeket.

A legegyszerűbb regisztrációs élmény érdekében az Azure AD-rendszergazda a Windows felügyeleti központtal vagy a PowerShell-lel végezheti el a regisztrációt.

   > [!IMPORTANT]
   > Azure Stack HCI-fürt Windows felügyeleti központon keresztüli regisztrálásához először [regisztrálnia kell a Windows felügyeleti központot az Azure](../manage/register-windows-admin-center.md) -ba a fürt regisztrálásához használni kívánt Azure Active Directory (bérlői) azonosító használatával.

### <a name="internet-access"></a>Internet-hozzáférés

Az Azure Stack HCI-nek rendszeresen kapcsolódnia kell az Azure nyilvános felhőhöz. Ha a kimenő kapcsolatot a külső vállalati tűzfal vagy a proxykiszolgáló korlátozza, azt úgy kell konfigurálni, hogy csak korlátozott számú, jól ismert Azure-beli IP-címen engedélyezze a kimenő hozzáférést a 443-es porthoz (HTTPS). További információ a tűzfalak előkészítéséről és a proxykiszolgáló beállításáról: [tűzfalak konfigurálása Azure stack HCI számára](../concepts/configure-firewalls.md).

   > [!NOTE]
   > A regisztrációs folyamat megpróbálja felvenni a kapcsolatot a PowerShell-galéria annak ellenőrzéséhez, hogy rendelkezik-e a szükséges PowerShell-modulok legújabb verziójával, például az az és a AzureAD. Bár a PowerShell-galéria az Azure-ban üzemelteti, jelenleg nem rendelkezik szolgáltatási címkével. Ha nem tudja futtatni a fenti parancsmagot olyan felügyeleti gépről, amely rendelkezik kimenő internet-hozzáféréssel, javasoljuk, hogy töltse le a modulokat, és manuálisan vigye át azokat egy fürtcsomóponton, ahol a parancsot futtatni fogja `Register-AzStackHCI` . Azt is megteheti, hogy [a modulokat leválasztott forgatókönyvben is telepíti](/powershell/scripting/gallery/how-to/working-with-local-psrepositories?view=powershell-7.1#installing-powershellget-on-a-disconnected-system).

### <a name="azure-subscription-and-permissions"></a>Azure-előfizetés és-engedélyek

Ha még nem rendelkezik Azure-fiókkal, [hozzon létre egyet](https://azure.microsoft.com/).

Bármilyen típusú meglévő előfizetést használhat:
- Ingyenes fiók Azure-kreditekkel [diákoknak](https://azure.microsoft.com/free/students/) vagy [Visual Studio-előfizetőknek](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)
- [Pay-as-go](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) -előfizetés bankkártyával
- Nagyvállalati Szerződés (EA) által beszerzett előfizetés
- A Cloud Solution Provider (CSP) program keretében beszerzett előfizetés

A fürtöt regisztráló felhasználónak az Azure-előfizetéssel kell rendelkeznie a következőhöz:

- Erőforrás-szolgáltató regisztrálása
- Azure-erőforrások és-erőforráscsoportok létrehozása/lekérése/törlése

Ha az Azure-előfizetése egy nagyvállalati szerződésen vagy CSP-n keresztül történik, a legegyszerűbben az Azure-előfizetés rendszergazdájának kell megkérnie, hogy rendeljen hozzá egy beépített "tulajdonos" vagy "közreműködő" Azure-szerepkört az előfizetéséhez. Előfordulhat azonban, hogy egyes rendszergazdák szigorúbb megoldást is előnyben részesítettek. Ebben az esetben lehetséges, hogy az alábbi lépéseket követve létrehozhat egy egyéni Azure-szerepkört, amely Azure Stack HCI-regisztrációra vonatkozik:

1. Hozzon létre egy **customHCIRole.js** nevű JSON-fájlt az alábbi tartalommal. Győződjön meg arról, hogy az <subscriptionID> Azure-előfizetés azonosítóját módosítja. Az előfizetés-azonosító beszerzéséhez látogasson el a [Portal.Azure.com](https://portal.azure.com)webhelyre, keresse fel az előfizetéseket, és másolja/illessze be az azonosítót a listából.

   ```json
   {
     "Name": "Azure Stack HCI registration role”,
     "Id": null,
     "IsCustom": true,
     "Description": "Custom Azure role to allow subscription-level access to register Azure Stack HCI",
     "Actions": [
       "Microsoft.Resources/subscriptions/resourceGroups/write",
       "Microsoft.Resources/subscriptions/resourceGroups/read",
       "Microsoft.Resources/subscriptions/resourceGroups/delete",
       "Microsoft.AzureStackHCI/register/action",
       "Microsoft.AzureStackHCI/Unregister/Action",
       "Microsoft.AzureStackHCI/clusters/*"
     ],
     "NotActions": [
     ],
   "AssignableScopes": [
       "/subscriptions/<subscriptionId>"
     ]
   }
   ```

2. Hozza létre az egyéni szerepkört:

   ```powershell
   New-AzRoleDefinition -InputFile <path to customHCIRole.json>
   ```

3. Rendelje hozzá az egyéni szerepkört a felhasználóhoz:

   ```powershell
   $user = get-AzAdUser -DisplayName <userdisplayname>
   $role = Get-AzRoleDefinition -Name "Azure Stack HCI registration role"
   New-AzRoleAssignment -ObjectId $user.Id -RoleDefinitionId $role.Id -Scope /subscriptions/<subscriptionid>
   ```

### <a name="azure-active-directory-permissions"></a>Engedélyek Azure Active Directory

A regisztrációs folyamat befejezéséhez szükség van a megfelelő Azure Active Directory engedélyekre is. Ha még nem rendelkezik velük, kérje meg az Azure AD-rendszergazdát, hogy adja meg az engedélyt, vagy delegálja az engedélyeket. További információt az [Azure-regisztráció kezelése](../manage/manage-azure-registration.md#azure-active-directory-app-permissions) című témakörben talál.

## <a name="register-a-cluster-using-windows-admin-center"></a>Fürt regisztrálása a Windows felügyeleti központtal

Azure Stack HCI-fürt regisztrálásának legegyszerűbb módja a Windows felügyeleti központ használata. Ne feledje, hogy a felhasználónak [Azure Active Directory engedélyekkel](../manage/manage-azure-registration.md#azure-active-directory-app-permissions)kell rendelkeznie, vagy a regisztrációs folyamat nem fejeződik be; Ehelyett kilép, és elhagyja a regisztráció függőben lévő rendszergazdai jóváhagyását.

1. A regisztrációs folyamat megkezdése előtt először [regisztrálnia kell a Windows felügyeleti központot az Azure](../manage/register-windows-admin-center.md)-ban, ha még nem tette meg.

   > [!IMPORTANT]
   > Ha a Windows felügyeleti központot az Azure-ban regisztrálja, fontos, hogy ugyanazt a Azure Active Directory-(bérlői) azonosítót használja, amelyet a fürt tényleges regisztrálásához kíván használni. Az Azure AD-bérlői azonosító a fiókokat és csoportokat tartalmazó Azure AD-példányokat jelöli, míg az Azure-előfizetés azonosítója az Azure-erőforrások használatára vonatkozó szerződést jelöl, amelyért a díjak felmerülnek. A bérlő AZONOSÍTÓjának megkereséséhez látogasson el a [Portal.Azure.com](https://portal.azure.com) webhelyre, és válassza a **Azure Active Directory** lehetőséget. A bérlői azonosító a **bérlői adatok** területen jelenik meg. Az Azure-előfizetés AZONOSÍTÓjának beszerzéséhez navigáljon az **előfizetések** elemre, és másolja/illessze be az azonosítót a listából.

2. Nyissa meg a Windows felügyeleti központot, és válassza a bal oldali **eszközök** menü alján található **Beállítások** lehetőséget. Ezután válassza ki **Azure stack HCI regisztráció** elemet a **Beállítások** menü alján. Ha a fürt még nincs regisztrálva az Azure-ban, akkor a **regisztráció állapota** **nem lesz regisztrálva**. A folytatáshoz kattintson a **regisztráció** gombra.

3. A regisztrációs folyamat befejezéséhez hitelesítenie kell magát (bejelentkezést) az Azure-fiókjával. A fióknak hozzáféréssel kell rendelkeznie ahhoz az Azure-előfizetéshez, amelyet a Windows felügyeleti központ átjárójának regisztrálásakor adott meg a regisztráció folytatásához. Másolja a megadott kódot, navigáljon a microsoft.com/devicelogin egy másik eszközön (például a SZÁMÍTÓGÉPén vagy a telefonján), írja be a kódot, és jelentkezzen be. A regisztrációs munkafolyamat érzékeli, amikor bejelentkezik, és folytatja a folyamat befejezését. A fürt ezután megtalálható lesz az Azure Portalon.

## <a name="register-a-cluster-using-powershell"></a>Fürt regisztrálása a PowerShell-lel

A következő eljárással regisztrálhat egy Azure Stack HCI-fürtöt az Azure-ban egy felügyeleti számítógép használatával.

1. Telepítse a szükséges parancsmagokat a felügyeleti SZÁMÍTÓGÉPére. Ha az Azure Stack HCI jelenlegi Általános rendelkezésre állásának (GA) rendszerképéből üzembe helyezett fürtöt regisztrál, egyszerűen futtassa a következő parancsot. Ha a fürt üzembe helyezése a nyilvános előzetes lemezképből történt, győződjön meg arról, hogy az Azure-ban való regisztrálás megkísérlése előtt a fürt minden kiszolgálójára vonatkozóan a 2020. november 23. (KB4586852) frissítést telepítette.

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > - Előfordulhat, hogy megjelenik egy üzenet, például azt **szeretné, hogy a PowerShellGet telepítse és importálja most a NuGet-szolgáltatót? ekkor az** igen (Y) **értéket** kell megadnia.
   > - Előfordulhat, hogy a rendszer kéri, **hogy telepítse a modulokat a "PSGallery" webhelyről?** , amelyhez az igen (Y) **értéket** kell megadnia.

2. A regisztrációt a fürt bármely kiszolgálójának nevével hajtsa végre. Az Azure-előfizetés AZONOSÍTÓjának beszerzéséhez látogasson el a [Portal.Azure.com](https://portal.azure.com)címre, lépjen az **előfizetések** elemre, és másolja/illessze be az azonosítót a listából.

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" -ComputerName Server1
   ```

   Ez a szintaxis regisztrálja a fürtöt (amely a Kiszolgáló1 tagja), az aktuális felhasználóként az alapértelmezett Azure-régióval és felhőalapú környezettel, valamint intelligens alapértelmezett neveket használ az Azure-erőforráshoz és az erőforráscsoporthoz. Ezen `-Region` értékek megadásához a választható, a és a paramétereket is hozzáadhatja `-ResourceName` ehhez a `-ResourceGroupName` parancshoz.

   Ne feledje, hogy a `Register-AzStackHCI` parancsmagot futtató felhasználónak [Azure Active Directory engedélyekkel](../manage/manage-azure-registration.md#azure-active-directory-app-permissions)kell rendelkeznie, vagy a regisztrációs folyamat nem fejeződik be, hanem kilép, és elhagyja a regisztráció függőben lévő rendszergazdai jóváhagyását. Az engedélyek megadása után egyszerűen futtassa újra `Register-AzStackHCI` a regisztráció befejezését.

3. Hitelesítés az Azure-ral

   A regisztrációs folyamat befejezéséhez hitelesítenie kell magát (bejelentkezést) az Azure-fiókjával. A fióknak hozzáféréssel kell rendelkeznie a fenti 2. lépésben megadott Azure-előfizetéshez a regisztráció folytatásához. Másolja a megadott kódot, navigáljon a microsoft.com/devicelogin egy másik eszközön (például a SZÁMÍTÓGÉPén vagy a telefonján), írja be a kódot, és jelentkezzen be. A regisztrációs munkafolyamat érzékeli, amikor bejelentkezik, és folytatja a folyamat befejezését. A fürt ezután megtalálható lesz az Azure Portalon.

## <a name="next-steps"></a>Következő lépések

Most már készen áll a következőkre:

- [A fürt ellenőrzése](validate.md)

