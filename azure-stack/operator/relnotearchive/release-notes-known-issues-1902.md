---
title: Azure Stack 1902 frissítés | Microsoft Docs
description: Ismerkedjen meg a 1902-es frissítéssel Azure Stack integrált rendszerek esetében, beleértve az újdonságokat, az ismert problémákat, valamint a frissítés letöltésének helyét.
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
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 9eef90875909f60bc906d238a9da09f2dc7ec83e
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009026"
---
# <a name="azure-stack-1902-update"></a>Azure Stack 1902 frissítés

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk az 1902-es frissítési csomag tartalmát ismerteti. A frissítés tartalmazza a Azure Stack ezen verziójának javításait, javításait és új funkcióit. Ez a cikk a kiadás ismert problémáit is ismerteti, és tartalmaz egy hivatkozást a frissítés letöltéséhez. Az ismert problémák a frissítési folyamathoz közvetlenül kapcsolódó problémákra vannak osztva, és a build (telepítés után) problémái vannak.

> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

## <a name="build-reference"></a>Build referenciája

A Azure Stack 1902 frissítési Build száma **1.1902.0.69**.

### <a name="update-type"></a>Frissítéstípus

A Azure Stack 1902 frissítési Build típusa **megtelt**. További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack](../azure-stack-updates.md) cikkben.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1901-es [Azure stack legújabb gyorsjavítást](#azure-stack-hotfixes) telepíti a 1902-es Azure stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

> [!TIP]  
> Feliratkozás a következő *RSS* -vagy *Atom* -hírcsatornára Azure stack gyorsjavítások megtartásához:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack gyorsjavítások

- **1901**: [KB 4500636 – Azure stack gyorsjavítás 1.1901.5.109](https://support.microsoft.com/help/4500636)
- **1902**: [KB 4500637 – Azure stack gyorsjavítás 1.1902.3.75](https://support.microsoft.com/help/4500637)

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Az 1902-es verzióját közvetlenül a **1.1901.0.95** vagy a **1.1901.0.99** kiadásból is telepítheti anélkül, hogy a 1901-es gyorsjavítást telepítené. Ha azonban telepítette a régebbi **1901.2.103** gyorsjavítást, akkor a 1902-es folytatás előtt telepítenie kell az újabb [1901.3.105-gyorsjavítást](https://support.microsoft.com/help/4495662) .

- A frissítés telepítésének megkezdése előtt futtassa a [test-AzureStack](../azure-stack-diagnostic-test.md) parancsot a következő paraméterekkel a Azure stack állapotának ellenőrzéséhez és a talált működési problémák megoldásához, beleértve az összes figyelmeztetést és hibát. Tekintse át az aktív riasztásokat is, és hárítsa el a szükséges műveleteket:

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

  Ha a `AzsControlPlane` paramétert a **test-AzureStack** végrehajtásakor is tartalmazza, a következő hiba jelenik meg a **test-AzureStack** kimenetben: **Fail Azure stack Control Plane websites Summary**. Ezt az adott hibát nyugodtan figyelmen kívül hagyhatja.

- Ha a Azure Stack System Center Operations Manager (SCOM) felügyelete alatt áll, akkor a 1902 alkalmazása előtt frissítse a Microsoft Azure Stack verziójának [felügyeleti csomagját](https://www.microsoft.com/download/details.aspx?id=55184) .

- A Azure Stack frissítéshez tartozó csomag formátuma a **. bin/. exe/. XML** fájlból **. zip/. XML** értékre változott a 1902-es kiadástól kezdődően. A csatlakoztatott Azure Stack skálázási egységekkel rendelkező ügyfelek az **elérhető frissítés** üzenetet fogják látni a portálon. A nem csatlakoztatott ügyfelek mostantól egyszerűen le is tölthetik és importálják a. zip fájlt a megfelelő. xml fájllal.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Fejlesztései

- Az 1902-es Build egy új felhasználói felületet vezet be a Azure Stack felügyeleti portálon a csomagok, ajánlatok, kvóták és kiegészítő csomagok létrehozásához. További információt, például képernyőképeket a [csomagok, ajánlatok és kvóták létrehozása](../azure-stack-create-plan.md)című témakörben talál.

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- A kapacitás bővítésének megbízhatósága a csomópont hozzáadása művelet során, amikor a méretezési egység állapotát "a tárterület bővítése" értékről "fut" állapotúra vált.

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- A csomagok integritásának és biztonságának javítása, valamint az offline betöltés egyszerűbb kezelése érdekében a Microsoft a. exe és a. bin fájlokból származó frissítési csomag formátumát a. zip fájlba változtatta. Az új formátum további megbízhatóságot biztosít a kicsomagolási folyamathoz, amely időnként a frissítés előkészítését eredményezheti. Ugyanez a csomag formátuma is érvényes az OEM-ből származó frissítési csomagokra.
- Ha a AzureStack futtatásával szeretné javítani a Azure Stack kezelői élményt, a kezelők egyszerűen használhatják a "test-AzureStack-Group UpdateReadiness" parancsot, amely nem haladhat meg tíz további paramétert egy include utasítás után.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- A frissítési folyamat során az alapvető infrastruktúra-szolgáltatások általános megbízhatóságának és rendelkezésre állásának javítása érdekében a natív frissítési erőforrás-szolgáltató a frissítési műveleti terv részeként felismeri és meghívja az automatikus globális szervizelést. A globális szervizelési "javítás" munkafolyamatok a következők:

  - Olyan infrastruktúra-alapú virtuális gépek ellenőrzése, amelyek nem optimális állapotban vannak, és szükség esetén megkísérlik kijavítani őket.
  - Keresse meg az SQL-szolgáltatással kapcsolatos problémákat a vezérlési terv részeként, és próbálkozzon meg a szükséges javítással.
  - A hálózati vezérlő (NC) részeként tekintse meg a Software Load Balancer (SLB) szolgáltatás állapotát, és próbálja meg szükség szerint kijavítani őket.
  - Tekintse meg a hálózati vezérlő (NC) szolgáltatás állapotát, és próbálja meg kijavítani a szükséges módon.
  - Tekintse meg a vész-helyreállítási konzol szolgáltatás (ERCS) Service Fabric-csomópontjainak állapotát, és szükség szerint javítsa ki őket.
  - Tekintse át az infrastruktúra-szerepkör állapotát, és szükség esetén javítsa ki a javítást.
  - Tekintse át az Azure konzisztens tárolási (ACS) Service Fabric-csomópontok állapotát, és szükség szerint javítsa ki őket.

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Az Azure stack diagnosztikai eszközeinek fejlesztései a naplók megbízhatóságának és teljesítményének javítására. További naplózás a Hálózatkezelés és az Identity Services számára. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- A Secret rotációs készültségi tesztek Test-AzureStack megbízhatóságának fejlesztése.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- Az AD Graph megbízhatóságának növelése az ügyfél Active Directory környezetével folytatott kommunikáció során

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- A hardveres leltár gyűjteményének fejlesztése a Get-AzureStackStampInformation-ben.

- A ERCS-infrastruktúrán futó műveletek megbízhatóságának növelése érdekében az egyes ERCS-példányok memóriája 8 GB-ról 12 GB-ra nő. Azure Stack integrált rendszerek telepítésekor ez az érték 12 GB-os növekedést eredményez.

<!-- 110303935 IcM Reported by HKEX -->
- 1902 a hálózati vezérlők VSwitch szolgáltatásában problémát javít, amelyben az adott csomóponton lévő összes virtuális gép offline állapotba került.  A probléma oka az volt, hogy elakadt az elsődleges veszteségi állapot, amely során az elsődleges nem lehet kapcsolatba lépni, de a szerepkör nem lett átmentve egy másik, kifogástalan példányra, amelyet csak a Microsoft terméktámogatási szolgáltatásával lehet feloldani.

> [!IMPORTANT]
> Annak ellenőrzéséhez, hogy a javítás és a frissítés folyamata a bérlői állásidő legkevesebb mennyiségét eredményezi-e, ellenőrizze, hogy a Azure Stack Stamp több mint 12 GB szabad területtel rendelkezik-e a **kapacitás** panelen. Ezt a memóriát a frissítés sikeres telepítése után a **kapacitás** panelen tekintheti meg.

## <a name="common-vulnerabilities-and-exposures"></a>Gyakori sebezhetőségek és kitettségek

Ez a frissítés a következő biztonsági frissítéseket telepíti:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

A biztonsági rések részletes ismertetését az előző hivatkozásokra kattintva vagy a Microsoft Tudásbázis [4487006](https://support.microsoft.com/en-us/help/4487006)-es cikkeiben tekintheti meg.

## <a name="known-issues-with-the-update-process"></a>A frissítési folyamattal kapcsolatos ismert problémák

- Azure Stack frissítés telepítésének megkísérlése során előfordulhat, hogy a frissítés állapota meghiúsul, és a **PreparationFailed** állapotra vált. Ezt az okozza, hogy a frissítési erőforrás-szolgáltató (URP) nem tudja megfelelően átvinni a fájlokat a tárolóból egy belső infrastruktúra-megosztásba a feldolgozáshoz. A 1901-es (1.1901.0.95) verziótól kezdődően megkerülheti ezt a problémát úgy, hogy a **Frissítés most** lehetőségre kattint (nem **folytatódik**). A URP ezután törli a fájlokat az előző kísérletből, és újra elindítja a letöltést.

- A [test-AzureStack](../azure-stack-diagnostic-test.md)futtatásakor a rendszer figyelmeztető üzenetet jelenít meg a alaplapi felügyeleti vezérlőről (bmc). Az üzenet biztonságosan figyelmen kívül hagyható –

- <!-- 2468613 - IS --> A frissítés telepítése során megjelenhetnek a riasztások a címmel, melyekkel `Error - Template for FaultType UserAccounts.New is missing.`  nyugodtan figyelmen kívül hagyhatják ezeket a riasztásokat. A riasztások automatikusan lezárulnak a frissítés telepítése után.

## <a name="post-update-steps"></a>Frissítés utáni lépések

- A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információ: [gyorsjavítások](#hotfixes)és [karbantartási szabályzat](../azure-stack-servicing-policy.md).  

- Az adatok lekérése a REST titkosítási kulcsokban, és biztonságosan tárolható a Azure Stack központi telepítésén kívül. Kövesse a [kulcsok lekérésének utasításait](../azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés után)

Az alábbiakban a build verziójának ismert problémáit ismertetjük.

### <a name="portal"></a>Portál

<!-- 2930820 - IS ASDK --> 
- Ha a "Docker" kifejezésre keres a rendszergazda és a felhasználói portálon, az elem helytelenül lett visszaadva. Azure Stack nem érhető el. Ha megpróbálja létrehozni, megjelenik egy hibaüzenet, amely a hiba jelzésével jelenik meg. 

<!-- 2931230 - IS  ASDK --> 
- A felhasználói előfizetéshez bővítményként hozzáadott csomagok nem törölhetők, még akkor is, ha eltávolítja a csomagot a felhasználói előfizetésből. A csomag addig marad, amíg a kiegészítő csomagra hivatkozó előfizetések is törlődnek. 

<!-- TBD - IS ASDK --> 
- Az 1804-es verzióval bevezetett két felügyeleti előfizetési típus nem használható. Az előfizetés típusa a **mérési előfizetés** és a **fogyasztási előfizetés**. Ezek az előfizetési típusok a 1804-es verziótól kezdődően új Azure Stack környezetekben láthatók, de még nem állnak készen a használatra. Továbbra is használja az **alapértelmezett szolgáltatói** előfizetés típusát.

<!-- 3557860 - IS ASDK --> 
- A felhasználói előfizetések törlése árva erőforrásokat eredményezett. Áthidaló megoldásként először törölje a felhasználói erőforrásokat vagy a teljes erőforráscsoportot, majd törölje a felhasználói előfizetéseket.

<!-- 1663805 - IS ASDK --> 
- Az előfizetéshez tartozó engedélyeket nem tekintheti meg az Azure Stack-portálok használatával. Áthidaló megoldásként használja [a PowerShellt az engedélyek ellenőrzéséhez](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- Daniel 3/28 -->
- Ha a felhasználói portálon egy, a Storage-fiókban található blobra navigál, és a navigációs fában próbál megnyitni **hozzáférési szabályzatot** , a következő ablak betöltése sikertelen lesz. A probléma megkerüléséhez a következő PowerShell-parancsmagokkal engedélyezheti a hozzáférési házirendek létrehozását, beolvasását, beállítását és törlését:

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Új Windowsos virtuális gép (VM) létrehozásakor a következő hibaüzenet jelenhet meg:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   A hiba akkor fordul elő, ha engedélyezi a rendszerindítási diagnosztika szolgáltatást egy virtuális gépen, de törli a rendszerindítási diagnosztika Storage-fiókját. A probléma megkerüléséhez hozza létre újra a Storage-fiókot ugyanazzal a névvel, mint amit korábban használt.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A virtuálisgép-méretezési csoport létrehozási felülete a CentOS-alapú 7,2-es lehetőséget kínálja az üzembe helyezéshez. Mivel ez a rendszerkép nem érhető el Azure Stackon, válasszon másik operációs rendszert az üzemelő példányhoz, vagy használjon egy Azure Resource Manager sablont, amely egy másik, a piactéren az operátor általi üzembe helyezés előtt letöltött CentOS-rendszerképet ad meg.  

<!-- TBD - IS ASDK --> 
- Az 1902-es frissítés alkalmazása után a következő problémák merülhetnek fel a virtuális gépek Managed Disks-vel való telepítésekor:

   - Ha az előfizetés az 1808-es frissítés előtt lett létrehozva, akkor a Managed Disks-t tartalmazó virtuális gép üzembe helyezése belső hibaüzenettel meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket az egyes előfizetésekhez:
      1. A bérlői portálon lépjen az **előfizetések** elemre, és keresse meg az előfizetést. Válassza az **erőforrás-szolgáltatók** lehetőséget, majd válassza a **Microsoft. számítás** lehetőséget, majd kattintson az **újbóli regisztrálás** gombra.
      2. Ugyanebben az előfizetésben lépjen a **Access Control (iam)** elemre, és ellenőrizze, hogy a **Azure stack által felügyelt lemez szerepel-** e a listáján.
   - Ha több-bérlős környezetet konfigurált, akkor előfordulhat, hogy a virtuális gépeknek a vendég címtárhoz társított előfizetésben való telepítése belső hibaüzenettel meghiúsul. A hiba elhárításához kövesse a [cikk](../azure-stack-enable-multitenancy.md#register-a-guest-directory) következő lépéseit az egyes vendég-címtárak újrakonfigurálásához.

- Az SSH-hitelesítéssel létrehozott Ubuntu 18,04-es virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez. Áthidaló megoldásként használja a virtuális gép hozzáférését a Linux-bővítményhez az SSH-kulcsok üzembe helyezése után, vagy használjon jelszó alapú hitelesítést.

- Méretezési csoport nem távolítható el a **Virtual Machine Scale sets** panelről. Áthidaló megoldásként válassza ki az eltávolítani kívánt méretezési készletet, majd kattintson a **Törlés** gombra az **Áttekintés** ablaktáblán.

- A virtuális gépeket 3 tartalék tartományba tartozó rendelkezésre állási csoportba kell létrehozni, és a virtuálisgép-méretezési csoport példányainak létrehozása **FabricVmPlacementErrorUnsupportedFaultDomainSize** hibával meghiúsul a 4 csomópontos Azure stack környezet frissítési folyamata során. Egy rendelkezésre állási csoportba egyetlen virtuális gépet hozhat létre, amelynek 2 tartalék tartománya sikeresen megtörtént. A méretezési csoport példányának létrehozása azonban még nem érhető el a 4 csomópontos Azure Stack frissítési folyamata során.

### <a name="networking"></a>Hálózatkezelés  

<!-- 3239127 - IS, ASDK -->
- Ha a Azure Stack-portálon olyan IP-konfigurációhoz tartozó statikus IP-címet módosít, amely egy virtuálisgép-példányhoz csatolt hálózati adapterhez van kötve, egy figyelmeztető üzenet jelenik meg, amely szerint az állapot 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

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

- Ha a felhasználói portálon egy **háttér-készletet** próbál hozzáadni egy **Load Balancerhoz**, a művelet meghiúsul, és a hibaüzenet **nem tudta frissíteni a Load Balancer.**  ...  A probléma megkerüléséhez a PowerShell, a CLI vagy egy Azure Resource Manager sablon használatával társítsa a háttér-készletet egy terheléselosztó-erőforráshoz.

- Ha a felhasználói portálon egy **Load Balancer** **bejövő NAT-szabályát** kísérli meg létrehozni, a művelet meghiúsul, és a hibaüzenet **nem frissül Load Balancer.** ..  A probléma megkerüléséhez a PowerShell, a CLI vagy egy Azure Resource Manager sablon használatával társítsa a háttér-készletet egy terheléselosztó-erőforráshoz.

- A felhasználói portálon, a **létrehozás Load Balancer** ablakban a **standard** Load Balancer SKU létrehozási lehetősége látható. Ez a beállítás Azure Stack esetén nem támogatott.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Regisztrálnia kell a Storage erőforrás-szolgáltatót, mielőtt létrehozza az első Azure-függvényt az előfizetésben.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Rendszernapló 

- A syslog-konfiguráció nem marad meg egy frissítési cikluson keresztül, így a syslog-ügyfél elveszti a konfigurációját, és a syslog-üzenetek továbbítása leáll. Ez a probléma az Azure Stack összes verziójára érvényes a syslog-ügyfél (1809) óta. A probléma megkerüléséhez konfigurálja újra a syslog-ügyfelet egy Azure Stack frissítés alkalmazása után.

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1902 frissítési [csomagot innen töltheti le.](https://aka.ms/azurestackupdatedownload) 

Csak a csatlakoztatott forgatókönyvek esetében Azure Stack üzemelő példányok rendszeresen ellenőrzi a biztonságos végpontot, és automatikusan értesíti Önt, ha elérhető egy frissítés a felhőhöz. További információ: [Azure stack frissítéseinek kezelése](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Következő lépések

- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).  
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).
- A Azure Stack integrált rendszerek karbantartási szabályzatának áttekintéséhez, valamint arról, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon, tekintse meg [Azure stack karbantartási házirendet](../azure-stack-servicing-policy.md).  
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).  
