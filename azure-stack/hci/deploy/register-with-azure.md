---
title: Az Azure Stack HCI csatlakoztatása az Azure-hoz
description: Azure Stack HCI-fürtök regisztrálása az Azure-ban.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/28/2020
ms.openlocfilehash: b2576bed615d6a65a5e0c61e5e3ccbc2c052132b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872704"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Az Azure Stack HCI csatlakoztatása az Azure-hoz

> A következőkre vonatkozik: Azure Stack HCI v20H2

Azure Stack HCI-t Azure-szolgáltatásként szállítjuk, és az Azure Online Services használati feltételeinek megfelelően regisztrálni kell a telepítést követő 30 napon belül. Ez a témakör azt ismerteti, hogyan regisztrálhat a Azure Stack HCI-fürtöt az [Azure arc](https://azure.microsoft.com/services/azure-arc/) használatával figyelésre, támogatásra, számlázásra és hibrid szolgáltatásokra. A regisztráció során egy Azure Resource Manager erőforrás jön létre, amely az egyes helyszíni Azure Stack HCI-fürtöket jelöli, és hatékonyan kiterjeszti az Azure felügyeleti síkot Azure Stack HCI-re. A rendszer rendszeres időközönként szinkronizálja az adatokat az Azure-erőforrás és a helyszíni fürt (ek) között.

   > [!IMPORTANT]
   > Az Azure-ban való regisztrálás kötelező, és a fürt nem teljes mértékben támogatott, amíg a regisztráció nem aktív. Ha az üzembe helyezés során nem regisztrálja a fürtöt az Azure-ban, vagy ha a fürt regisztrálva van, de 30 napnál nem kapcsolódik az Azure-hoz, a rendszer nem engedélyezi az új virtuális gépek (VM-EK) létrehozását vagy hozzáadását. Ebben az esetben a következő hibaüzenet jelenik meg, amikor virtuális gépeket próbál létrehozni:
   >
   > *Nem sikerült konfigurálni a virtuális gépi szerepkört a következőhöz: "vmname". A feladatot nem sikerült végrehajtani. Hiba történt a (z) vmname fürtözött szerepkörök megnyitásakor. Az elérni kívánt szolgáltatás adott számú kapcsolatra van licenccel. Jelenleg nem hozhatók létre több kapcsolat a szolgáltatással, mert már annyi kapcsolat van, amennyit a szolgáltatás elfogad.*
   >
   > A megoldás az, hogy engedélyezze a kimenő kapcsolatot az Azure-ban, és ellenőrizze, hogy a fürt regisztrálva van-e a témakörben leírtak szerint.

## <a name="prerequisites-for-registration"></a>Regisztráció előfeltételei

Nem fog tudni regisztrálni az Azure-ban, amíg létre nem Azure Stack HCI-fürtöt. Ahhoz, hogy a fürt támogatni lehessen a fürtöt, a fürt csomópontjainak fizikai kiszolgálóknak kell lenniük. A virtuális gépek teszteléshez használhatók, de támogatniuk kell Unified Extensible Firmware Interface (UEFI), ami azt jelenti, hogy nem használhatja a Hyper-V 1. generációs virtuális gépeket. Az Azure arc-regisztráció a Azure Stack HCI operációs rendszer natív funkciója, így a regisztrációhoz nincs szükség ügynökre.

### <a name="internet-access"></a>Internet-hozzáférés

Azure Stack HCI-nek rendszeresen csatlakoznia kell az Azure nyilvános felhőhöz. Ha a kimenő kapcsolatot a külső vállalati tűzfal vagy a proxykiszolgáló korlátozza, azt úgy kell konfigurálni, hogy csak korlátozott számú, jól ismert Azure-beli IP-címen engedélyezze a kimenő hozzáférést a 443-es porthoz (HTTPS). A tűzfalak előkészítésével kapcsolatos további információkért lásd: [tűzfalak konfigurálása Azure stack HCI-hez](../concepts/configure-firewalls.md).

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

## <a name="register-using-powershell"></a>Regisztrálás a PowerShell használatával

A következő eljárással regisztrálhat egy Azure Stack HCI-fürtöt az Azure-ban egy felügyeleti számítógép használatával.

1. Telepítse a szükséges parancsmagokat a felügyeleti SZÁMÍTÓGÉPére. Ha egy Azure Stack HCI aktuális általánosan elérhető (GA) rendszerképében üzembe helyezett fürtöt regisztrál, egyszerűen futtassa az alábbi parancsot. Ha a fürt üzembe helyezése a nyilvános előzetes lemezképből történt, győződjön meg arról, hogy az Azure-ban való regisztrálás megkísérlése előtt a fürt minden kiszolgálójára vonatkozóan a 2020. november 23. (KB4586852) frissítést telepítette.

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > - Előfordulhat, hogy megjelenik egy üzenet, például "szeretné, hogy a PowerShellGet telepítse és importálja most a NuGet-szolgáltatót?" , amelyhez az igen (Y) értéket kell megadnia.
   > - Előfordulhat, hogy a rendszer kéri a következőt: "biztos, hogy telepíti a modulokat a" PSGallery "?", amelyhez az igen (Y) értéket kell megadnia.

2. A regisztrációt a fürt bármely kiszolgálójának nevével hajtsa végre. Az Azure-előfizetés AZONOSÍTÓjának beszerzéséhez látogasson el a [Portal.Azure.com](https://portal.azure.com)webhelyre, keresse fel az előfizetéseket, és másolja/illessze be az azonosítót a listából.

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" -ComputerName Server1 [–Credential] [-ResourceName] [-ResourceGroupName] [-Region]
   ```

   Ez a szintaxis regisztrálja a fürtöt (amely a Kiszolgáló1 tagja), mivel az aktuális felhasználó az alapértelmezett Azure-régióval és felhőalapú környezettel rendelkezik, és intelligens alapértelmezett neveket használ az Azure-erőforráshoz és az erőforráscsoporthoz, de paramétereket adhat hozzá ehhez a parancshoz, ha szeretné, adja meg ezeket az értékeket.

   Ne feledje, hogy a `Register-AzStackHCI` parancsmagot futtató felhasználónak [Azure Active Directory engedélyekkel](../manage/manage-azure-registration.md#azure-active-directory-app-permissions)kell rendelkeznie, vagy a regisztrációs folyamat nem fejeződik be, hanem kilép, és elhagyja a regisztrációra váró rendszergazdai jóváhagyást. Az engedélyek megadása után egyszerűen futtassa újra `Register-AzStackHCI` a regisztráció befejezését.

3. Hitelesítés az Azure-ral

   A regisztrációs folyamat befejezéséhez hitelesítenie kell magát (bejelentkezést) az Azure-fiókjával. A regisztráció folytatásához a fióknak hozzáféréssel kell rendelkeznie a fenti 4. lépésben megadott Azure-előfizetéshez. Másolja a megadott kódot, navigáljon a microsoft.com/devicelogin egy másik eszközön (például a SZÁMÍTÓGÉPén vagy a telefonján), írja be a kódot, és jelentkezzen be. Ez ugyanaz a tapasztalat, ha a Microsoft más eszközökre korlátozott beviteli módokat használ, például Xbox.

A regisztrációs munkafolyamat felismeri, amikor bejelentkezett, és folytatja a befejezést. Ezután látnia kell a fürtöt a Azure Portalban.

## <a name="next-steps"></a>További lépések

Most már készen áll a következőkre:

- [A fürt ellenőrzése](validate.md)

