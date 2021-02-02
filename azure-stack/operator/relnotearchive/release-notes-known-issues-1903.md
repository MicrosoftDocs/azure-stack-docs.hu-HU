---
title: Azure Stack 1903 frissítés | Microsoft Docs
description: Ismerkedjen meg a 1903-es frissítéssel Azure Stack integrált rendszerek esetében, beleértve az újdonságokat, az ismert problémákat, valamint a frissítés letöltésének helyét.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/20/2019
ms.openlocfilehash: 1ce083f8d264c24010def3005f92332b6ae7b2fd
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248418"
---
# <a name="azure-stack-1903-update"></a>Azure Stack 1903 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk az 1903-es frissítési csomag tartalmát ismerteti. A frissítés tartalmazza a Azure Stack ezen verziójának javításait, javításait és új funkcióit. Ez a cikk a kiadás ismert problémáit is ismerteti, és tartalmaz egy hivatkozást a frissítés letöltéséhez. Az ismert problémák a frissítési folyamathoz közvetlenül kapcsolódó problémákra vannak osztva, és a build (telepítés után) problémái vannak.

> [!IMPORTANT]
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája

A Azure Stack 1903 frissítési Build száma **1.1903.0.35**.

### <a name="update-type"></a>Frissítéstípus

A Azure Stack 1903 frissítési Build típusa **expressz**. További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack](../azure-stack-updates.md) cikkben. Az 1903-es frissítés befejezéséhez szükséges idő körülbelül 16 óra, de a pontos idők eltérőek lehetnek. Ez a futásidejű közelítés az 1903-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

> [!IMPORTANT]
> Az 1903-es adattartalom nem tartalmaz ASDK-kiadást.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1902-es Azure Stack legújabb gyorsjavítást telepíti a 1903-es Azure Stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="azure-stack-hotfixes"></a>Azure Stack gyorsjavítások

- **1902**: [KB 4500637 – Azure stack gyorsjavítás 1.1902.3.75](https://support.microsoft.com/help/4500637)
- **1903**: [KB 4500638 – Azure stack gyorsjavítás 1.1903.2.39](https://support.microsoft.com/help/4500638)

## <a name="improvements"></a>Fejlesztései

- Kijavított egy olyan hibát a hálózatban, amely megakadályozta a **nyilvános IP-cím** **üresjárati időkorlátjának (perc)** értékének módosítását. Korábban az érték módosításait a rendszer figyelmen kívül hagyta, így az elvégzett módosításoktól függetlenül az érték alapértelmezés szerint 4 percet vesz igénybe. Ezzel a beállítással szabályozhatja, hogy a TCP-kapcsolatok hány perc alatt maradjanak nyitva anélkül, hogy az ügyfelek a Keep-Alive üzenetek küldésére támaszkodnak. Megjegyzés: Ez a hiba csak az érintett példányok nyilvános IP-címeire vonatkozik, nem a terheléselosztó számára hozzárendelt nyilvános IP-címekre.

- A frissítési motor megbízhatóságának fejlesztése, beleértve a gyakori problémák automatikus szervizelését, hogy a frissítések megszakítás nélkül is érvényesek legyenek.

- Az alacsony lemezterület-feltételek észlelésének és szervizelésének fejlesztése.

- Azure Stack mostantól támogatja a 2.2.35 verziónál nagyobb Windows Azure Linux-ügynököket. Ez a támogatás lehetővé teszi, hogy az ügyfelek konzisztens Linux-rendszerképeket tartsanak az Azure és a Azure Stack között. Az 1901-es és 1902-as gyorsjavítások részeként lett hozzáadva.

### <a name="secret-management"></a>Titkos kódok kezelése

- Azure Stack mostantól támogatja a tanúsítványok által a külső titkos elforgatáshoz használt főtanúsítvány rotációját. További információkért [tekintse meg ezt a cikket](../azure-stack-rotate-secrets.md).

- 1903 a titkos elforgatás teljesítményének növelését, amely csökkenti a belső titkos kód végrehajtásához szükséges időt.

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> A 1903-re való frissítés előtt telepítse a 1902-es (ha van) legújabb Azure Stack gyorsjavítását.

- Győződjön meg arról, hogy a [Azure stack Capacity Planner](https://aka.ms/azstackcapacityplanner) legújabb verzióját használja a számítási feladatok tervezéséhez és méretezéséhez. A legújabb verzió hibajavításokat tartalmaz, és új funkciókat biztosít az egyes Azure Stack frissítésekhez.

- A frissítés telepítésének megkezdése előtt futtassa a [test-AzureStack](../azure-stack-diagnostic-test.md) parancsot a következő paraméterrel a Azure stack állapotának ellenőrzéséhez és a talált működési problémák megoldásához, beleértve az összes figyelmeztetést és hibát. Tekintse át az aktív riasztásokat is, és hárítsa el a szükséges műveleteket:

    ```powershell
   Test-AzureStack -Group UpdateReadiness
    ```

- Ha Azure Stack a System Center Operations Manager felügyeli, akkor a 1903 alkalmazása előtt győződjön meg róla, hogy a Microsoft Azure Stack verzió 1.0.3.11 frissíti a [felügyeleti csomagot](https://www.microsoft.com/download/details.aspx?id=55184) .

- A Azure Stack frissítéshez tartozó csomag formátuma a **. bin/. exe/. XML** fájlból **. zip/. XML** értékre változott a 1902-es kiadástól kezdődően. A csatlakoztatott Azure Stack skálázási egységekkel rendelkező ügyfelek az **elérhető frissítés** üzenetet fogják látni a portálon. A nem csatlakoztatott ügyfelek mostantól egyszerűen le is tölthetik és importálják a. zip fájlt a megfelelő. xml fájllal.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák

- Azure Stack frissítés telepítésének megkísérlése során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed** állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz. A 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti ezt a problémát úgy, hogy a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli a fájlokat az előző kísérletből, és újra elindítja a letöltést.

- A [test-AzureStack](../azure-stack-diagnostic-test.md)futtatásakor a rendszer figyelmeztető üzenetet jelenít meg a alaplapi felügyeleti vezérlőről (bmc). Az üzenet biztonságosan figyelmen kívül hagyható –

<!-- 2468613 - IS -->
- A frissítés telepítése során riasztások jelenhetnek meg a **hibatípushoz UserAccounts-sablon címének hibája alapján. Az új hiányzik.** Ezeket a riasztásokat nyugodtan figyelmen kívül hagyhatja. A riasztások automatikusan lezárulnak a frissítés telepítése után.

## <a name="post-update-steps"></a>Frissítés utáni lépések

- A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információ: [gyorsjavítások](#hotfixes)és [karbantartási szabályzat](../azure-stack-servicing-policy.md).

- Az adatok lekérése a REST titkosítási kulcsokban, és biztonságosan tárolható a Azure Stack központi telepítésén kívül. Kövesse a [kulcsok lekérésének utasításait](../azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

Az alábbiakban a build verziójának ismert problémáit ismertetjük.

### <a name="portal"></a>Portál

- Ha a felhasználói portál irányítópultján a **visszajelzés** csempére próbál rákattintani, megnyílik egy üres böngésző lap. Áthidaló megoldásként [Azure stack felhasználói](https://aka.ms/azurestackuservoice) hanghívást is használhat a felhasználói hangkérések fájlba való felvételéhez.

<!-- 2930820 - IS ASDK -->
- Ha a "Docker" kifejezésre keres a rendszergazda és a felhasználói portálon, az elem helytelenül lett visszaadva. Azure Stack nem érhető el. Ha megpróbálja létrehozni, megjelenik egy hibaüzenet, amely a hiba jelzésével jelenik meg.

<!-- 2931230 - IS  ASDK -->
- A felhasználói előfizetéshez bővítményként hozzáadott csomagok nem törölhetők, még akkor is, ha eltávolítja a csomagot a felhasználói előfizetésből. A csomag addig marad, amíg a kiegészítő csomagra hivatkozó előfizetések is törlődnek.

<!-- TBD - IS ASDK -->
- Az 1804-es verzióval bevezetett két felügyeleti előfizetési típus nem használható. Az előfizetés típusa a **mérési előfizetés** és a **fogyasztási előfizetés**. Ezek az előfizetési típusok a 1804-es verziótól kezdődően új Azure Stack környezetekben láthatók, de még nem állnak készen a használatra. Továbbra is használja az **alapértelmezett szolgáltatói** előfizetés típusát.

<!-- 3557860 - IS ASDK -->
- A felhasználói előfizetések törlése árva erőforrásokat eredményezett. Áthidaló megoldásként először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.

<!-- 1663805 - IS ASDK --> 
- Az előfizetéshez tartozó engedélyeket nem tekintheti meg az Azure Stack-portálok használatával. Áthidaló megoldásként használja [a PowerShellt az engedélyek ellenőrzéséhez](/powershell/module/azurerm.resources/get-azurermroleassignment).

<!-- Daniel 3/28 -->
- Ha a felhasználói portálon egy, a Storage-fiókban található blobra navigál, és a navigációs fában próbál megnyitni **hozzáférési szabályzatot** , a következő ablak betöltése sikertelen lesz. A probléma megkerüléséhez a következő PowerShell-parancsmagokkal engedélyezheti a hozzáférési házirendek létrehozását, beolvasását, beállítását és törlését:

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

  
<!-- Daniel 3/28 -->
- Ha a felhasználói portálon megpróbál feltölteni egy blobot a **OAuth (előzetes verzió)** beállítással, a feladat hibaüzenettel meghiúsul. A probléma megkerüléséhez töltse fel a blobot a **sas** kapcsoló használatával.

- Ha bejelentkezett a Azure Stack-portálra, előfordulhat, hogy a nyilvános Azure Portal kapcsolatos értesítések jelennek meg. Nyugodtan figyelmen kívül hagyhatja ezeket az értesítéseket, mivel azok jelenleg nem vonatkoznak a Azure Stackra (például "1 új frissítés – a következő frissítések érhetők el: Azure Portal április 2019 Update").

- A felhasználói portál irányítópultján a **visszajelzés** csempére kattintva megnyílik egy üres böngésző lap. Áthidaló megoldásként [Azure stack felhasználói](https://aka.ms/azurestackuservoice) hanghívást is használhat a felhasználói hangkérések fájlba való felvételéhez.

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját. A probléma megkerüléséhez hozza létre újra a Storage-fiókot ugyanazzal a névvel, mint amit korábban használt.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A virtuálisgép-méretezési csoport létrehozási felülete a CentOS-alapú 7,2-es lehetőséget kínálja az üzembe helyezéshez. Mivel ez a rendszerkép Azure Stack piactéren nem érhető el, válasszon másik operációs rendszert az üzemelő példányhoz, vagy használjon egy Azure Resource Manager sablont, amely egy másik, a piactér által a piactérről való üzembe helyezés előtt letöltött CentOS-rendszerképet ad meg.

<!-- TBD - IS ASDK -->
- Az 1903-es frissítés alkalmazása után a következő problémák merülhetnek fel a virtuális gépek Managed Disks-vel való telepítésekor:

   - Ha az előfizetés az 1808-es frissítés előtt lett létrehozva, akkor a Managed Disks-t tartalmazó virtuális gép üzembe helyezése belső hibaüzenettel meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket az egyes előfizetésekhez:
      1. A bérlői portálon lépjen az **előfizetések** elemre, és keresse meg az előfizetést. Válassza az **erőforrás-szolgáltatók** lehetőséget, majd válassza a **Microsoft. számítás** lehetőséget, majd kattintson az **újbóli regisztrálás** gombra.
      2. Ugyanebben az előfizetésben lépjen a **Access Control (iam)** elemre, és ellenőrizze, hogy a **Azure stack által felügyelt lemez szerepel-** e a listáján.
   - Ha több-bérlős környezetet konfigurált, akkor előfordulhat, hogy a virtuális gépeknek a vendég címtárhoz társított előfizetésben való telepítése belső hibaüzenettel meghiúsul. A hiba elhárításához kövesse a [cikk](../azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory) következő lépéseit az egyes vendég-címtárak újrakonfigurálásához.

- Az SSH-hitelesítéssel létrehozott Ubuntu 18,04-es virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez. Áthidaló megoldásként használja a virtuális gép hozzáférését a Linux-bővítményhez az SSH-kulcsok üzembe helyezése után, vagy használjon jelszó alapú hitelesítést.

- Ha nem rendelkezik hardveres életciklus-állomással (HLH): a Build 1902 előtt be kellett állítania a csoportházirend- **Számítógép konfigurációja \ Windows beállításai \ helyi** házirend \ \ kiszolgáló \ beállítások \ **& NTLM – NTLMv2-munkamenetek biztonsága, ha egyeztetve** van. A Build 1902 óta **nincs definiálva** , vagy állítsa be úgy, hogy **csak NTLMv2-válaszokat küldjön** (ez az alapértelmezett érték). Ellenkező esetben nem fog tudni távoli PowerShell-munkamenetet létrehozni, és a rendszer **megtagadja a hozzáférést** :

   ```powershell
   $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint -Credential $Cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

- Méretezési csoport nem távolítható el a **Virtual Machine Scale sets** panelről. Áthidaló megoldásként válassza ki az eltávolítani kívánt méretezési készletet, majd kattintson a **Törlés** gombra az **Áttekintés** ablaktáblán.

- A virtuális gépeket 3 tartalék tartományba tartozó rendelkezésre állási csoportba kell létrehozni, és a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack környezet frissítési folyamata során. Egy rendelkezésre állási csoportba egyetlen virtuális gépet hozhat létre, amelynek 2 tartalék tartománya sikeresen megtörtént. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack frissítési folyamata során.

### <a name="networking"></a>Hálózatkezelés

<!-- 3239127 - IS, ASDK -->
- Ha a Azure Stack-portálon olyan IP-konfigurációhoz tartozó statikus IP-címet módosít, amely egy virtuálisgép-példányhoz csatolt hálózati adapterhez van kötve, egy figyelmeztető üzenet jelenik meg, amely szerint az állapot

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

    Nyugodtan figyelmen kívül hagyhatja ezt az üzenetet; a rendszer akkor is megváltoztatja az IP-címet, ha a virtuálisgép-példány nem indul újra.

<!-- 3632798 - IS, ASDK -->
- Ha a portálon egy bejövő biztonsági szabályt ad hozzá, és kijelöli a **szolgáltatás címkéje** forrásként lehetőséget, több lehetőség is megjelenik a **forrás címke** listában, amelyek nem érhetők el Azure stack. A Azure Stackban érvényes beállítások a következők:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**

  A további beállítások nem támogatottak a Azure Stackban lévő forrásként szolgáló címkékként. Hasonlóképpen, ha hozzáad egy kimenő biztonsági szabályt, és kiválasztja a **szolgáltatás címkét** célként, a **forrás címkével** megegyező beállítások jelennek meg. Az egyetlen érvényes beállítás megegyezik a **forrás címkével**, az előző listában leírtak szerint.

- A hálózati biztonsági csoportok (NSG-EK) nem működnek a Azure Stack ugyanúgy, mint a globális Azure. Az Azure-ban több portot is beállíthat egy NSG-szabályhoz (a portál, a PowerShell és a Resource Manager-sablonok használatával). Azure Stack azonban nem állíthat be több portot egyetlen NSG-szabályon a portálon keresztül. A probléma megkerüléséhez a további szabályok megadásához használja a Resource Manager-sablont vagy a PowerShellt.

<!-- 3203799 - IS, ASDK -->
- A Azure Stack jelenleg nem támogatja több mint 4 hálózati adapter (NIC) csatolását egy virtuálisgép-példányhoz, a példány méretétől függetlenül.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK -->
- A bérlőknek regisztrálniuk kell a tárolási erőforrás-szolgáltatót, mielőtt az első Azure-függvényt létrehozná az előfizetésben.
- Néhány bérlői portál felhasználói felülete a 1903-es portál-keretrendszerrel való inkompatibilitás miatt megszakadt. a központi telepítéshez használható UX, az éles környezetben történő tesztelés és a helyi bővítmények. A probléma megkerüléséhez használja a [Azure app Service PowerShell-modult](/azure/app-service/deploy-staging-slots#automate-with-powershell) vagy az [Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest&preserve-view=true)-t. A portál felülete az Azure App Service következő kiadásában lesz visszaállítva, amely az Azure Stack 1.6-os (6. frissítés) verziójában jelenik meg.

<!-- ### Usage -->


<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Rendszernapló

- A syslog-konfiguráció nem marad meg egy frissítési cikluson keresztül, így a syslog-ügyfél elveszti a konfigurációját, és a syslog-üzenetek továbbítása leáll. Ez a probléma az Azure Stack összes verziójára érvényes a syslog-ügyfél (1809) óta. A probléma megkerüléséhez konfigurálja újra a syslog-ügyfelet egy Azure Stack frissítés alkalmazása után.

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1903 frissítési [csomagot innen töltheti le.](https://aka.ms/azurestackupdatedownload)

Csak a csatlakoztatott forgatókönyvek esetében Azure Stack üzemelő példányok rendszeresen ellenőrzi a biztonságos végpontot, és automatikusan értesíti Önt, ha elérhető egy frissítés a felhőhöz. További információ: [Azure stack frissítéseinek kezelése](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Következő lépések

- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).
- A Azure Stack integrált rendszerek karbantartási szabályzatának áttekintéséhez, valamint arról, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon, tekintse meg [Azure stack karbantartási házirendet](../azure-stack-servicing-policy.md).
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).