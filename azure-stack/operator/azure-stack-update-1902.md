---
title: Azure Stack 1902 frissítése |} A Microsoft Docs
description: Ismerje meg Azure Stack integrált rendszerek, beleértve az újdonságokat, 1902 frissítésével kapcsolatos ismert problémák, valamint a helyét, a frissítés letöltése.
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
ms.openlocfilehash: 29f8a3a6a620f197ba79d75bc41738e03f8f2342
ms.sourcegitcommit: 07cc716d97bf484c7260eb165ae205ae25e09589
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66453396"
---
# <a name="azure-stack-1902-update"></a>Azure Stack 1902 frissítése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti a 1902 csomag tartalmát. A frissítési fejlesztései, javításokat és új funkciók az Azure Stack ezen verziója tartalmazza. Ez a cikk is ebben a kiadásban az ismert problémákat, és tartalmaz egy hivatkozást a frissítés letöltése. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat, és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="archived-release-notes"></a>Archivált kibocsátási megjegyzések

Látható [Azure Stack régebbi verzióinak kibocsátási megjegyzések a TechNet-katalógusban](http://aka.ms/azsarchivedrelnotes). Ezek a kibocsátási megjegyzések kizárólag tájékoztatási célt szolgálnak, és jelenti ezen verziók támogatása archivált. További segítségért forduljon a Microsoft ügyfél-támogatási szolgálathoz.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure Stack 1902 frissítés buildszáma **1.1902.0.69**.

## <a name="hotfixes"></a>Gyorsjavítások

Az Azure Stack rendszeresen gyorsjavításait. Ne felejtse el telepíteni a [legújabb Azure Stack-gyorsjavítás](#azure-stack-hotfixes) az Azure Stack 1902 frissítése előtt 1901.

Az Azure Stack-gyorsjavítások csak alkalmazhatók az Azure Stackkel integrált rendszerek. Ne kísérelje meg a ASDK gyorsjavítások telepítése.

> [!TIP]  
> Fizessen elő a következő *RSS* vagy *Atom* tartani az Azure Stack gyorsjavítások-hírcsatornák:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Az Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Az Azure Stack-gyorsjavítások

- **1901**: [KB 4500636 – az Azure Stack gyorsjavítás 1.1901.5.109](https://support.microsoft.com/help/4500636)
- **1902**: [KB 4500637 – az Azure Stack gyorsjavítás 1.1902.3.75](https://support.microsoft.com/help/4500637)

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> 1902 telepítheti közvetlenül a vagy a [1.1901.0.95 vagy 1.1901.0.99](azure-stack-update-1901.md#build-reference) kiadási, bármely 1901 a gyorsjavítás telepítése nélkül. Azonban ha már telepítette a korábbi **1901.2.103** gyorsjavítás, telepítenie kell az újabb [1901.3.105 gyorsjavítás](https://support.microsoft.com/help/4495662) 1902 előtt folytatja.

- A frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) az Azure Stack állapotának érvényesítéséhez, és hárítsa el a működési hibákat talált a következő paraméterekkel, többek között az összes figyelmeztetések és hibák esetén. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő:

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

  Ha a `AzsControlPlane` paraméter megtalálható mikor **Test-AzureStack** van hajtja végre, látni fogja a következő hiba a **Test-AzureStack** kimenete: **SIKERTELEN az Azure Stack vezérlési sík webhelyek összefoglalás**. Ez a hiba nyugodtan figyelmen kívül hagyhatja.

- Ha az Azure Stack a System Center Operations Manager (SCOM) által felügyelt, ügyeljen arra, hogy frissítse a [felügyeleti csomag a Microsoft Azure Stackhez készült](https://www.microsoft.com/download/details.aspx?id=55184) verzióra 1.0.3.11 1902 alkalmazása előtt.

- Az Azure Stack-frissítési csomag formátuma megváltozott **.bin/.exe/.xml** való **.zip/.xml** a 1902 kiadástól kezdve. A csatlakoztatott Azure Stack skálázási egységek ügyfelek fogják látni a **frissítés érhető el** üzenet a portálon. Nem csatlakozó ügyfelek mostantól egyszerűen töltse le, és importálja a .zip-fájlt a megfelelő .xml.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Fejlesztései

- A 1902 build vezet be az Azure Stack rendszergazdai portál csomagok, ajánlatok, kvóták és kiegészítő csomagok létrehozására szolgáló új felhasználói felületet. További információk, többek között a képernyőképek: [csomagok, ajánlatok és kvóták létrehozása](azure-stack-create-plan.md).

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Fejlesztések a megbízhatóságot, egyszerű kapacitásbővítést téve lehetővé a skálázási egység állapot "Expanding tároló" "Fut" váltáskor Hozzáadás csomópont művelet során.

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Csomag integritás és a biztonsági, valamint offline támogatunk könnyebb kezelhetőség javítása érdekében a Microsoft megváltozott a csomag formátuma .exe és .bin fájlt egy .zip-fájlt. Az új formátum hozzáadja a kicsomagolása folyamat, amely időnként okozhat a frissítés előkészítése stagnálni további megbízhatóságát. A csomag formátuma is vonatkozik, az OEM-csomagok frissítése.
- Test-AzureStack futtatásakor az Azure Stack-kezelői élmény javításához operátorok most már egyszerűen használhatják, "Test-AzureStack-csoport UpdateReadiness" figyelésekor tíz további paraméterek átadása egy Belefoglalás utasítás után.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- A teljes megbízhatóságot és alapvető infrastruktúra-szolgáltatások rendelkezésre állásának javítása a frissítési folyamat során, a natív frissítés erőforrás-szolgáltató, a frissítési művelet terv részeként érzékeli, és igény szerint az automatikus globális szervizelések meghívása. Globális szervizelési "javítás" a munkafolyamatok a következők:

  - Keresése nem optimális állapotban van, és próbálja meg őket igény szerint virtuális gépeket.
  - SQL-szolgáltatási problémák keresése a vezérlő terv részeként, és próbálja meg őket igény szerint.
  - A szoftveres terheléselosztó (SLB) szolgáltatás állapotának ellenőrzése során, a hálózati vezérlő (NC), és próbálja meg őket igény szerint.
  - Ellenőrizze a hálózati vezérlő (NC) szolgáltatás állapotát, és próbálja meg helyreállítani igény szerint
  - Ellenőrizze a válságkezelési helyreállítási konzol szolgáltatás (ERCS) service fabric csomópontok állapotát, és javítsa ki őket igény szerint.
  - Az infrastruktúra-szerepkör állapotának ellenőrzése és javítása, igény szerint.
  - Ellenőrizze az Azure-konzisztens tároló (ACS) service fabric csomópontok állapotát, és javítsa ki őket igény szerint.

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Fejlesztések az Azure stack diagnosztikai eszközök napló gyűjtése megbízhatóságának és teljesítményének javítása érdekében. További naplózás a hálózati és identitáskezelési szolgáltatásokat. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- Érintő fejlesztések a megbízhatóságot a Test-AzureStack titkos Elforgatás készültségének teszteléséhez.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- AD Graph megbízhatóságának növelése, ha az ügyfél Active Directory-környezetbe való kommunikáció közben fejlesztései

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Fejlesztések hardverleltározást a Get-AzureStackStampInformation.

- ERCS infrastruktúrán futó műveletek megbízhatóságának javítása érdekében a memória ERCS példányonként növeli a 8 GB-tól 12 GB. Az Azure Stackkel integrált rendszerek telepítés esetén az eredmény egy 12 GB-os növekedést teljes.

<!-- 110303935 IcM Reported by HKEX -->
- 1902 javít egy problémát a hálózati vezérlők VSwitch szolgáltatás, amely az adott csomóponton lévő összes virtuális gép offline állapotba.  A probléma miatt nem elsődleges adatveszteség állapotba, ha az elsődleges nem érhető el, de a szerepkör nem lett feladatátadása megtörtént egy másik, megfelelő példányához, amely kapcsolatba lép a Microsoft támogatási szolgálatához csak oldható elakad.

> [!IMPORTANT]
> Ahhoz, hogy a javítási és frissítési folyamat a lehető legkevesebb bérlői állásidőt eredményez, ellenőrizze, hogy az Azure Stack-blokk 12 GB-nál több szabad terület a rendelkezik a **kapacitás** panelen. Láthatja, hogy ez a memória növelése megjelennek a **kapacitás** panelen a frissítés sikeres telepítése után.

## <a name="common-vulnerabilities-and-exposures"></a>Gyakori biztonsági rések és besorolási

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

További információ a biztonsági rések, kattintson a fenti hivatkozásokat a, vagy tekintse meg a Microsoft Tudásbázis cikkei [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái

- Próbál telepíteni egy Azure Stack-frissítést, ha a frissítés állapota sikertelen lehet, hogy, és az állapotváltozáshoz **PreparationFailed**. A frissítés erőforrás-szolgáltató (URP) nem képes megfelelően viheti át a fájlokat a storage-tárolót egy belső infrastruktúrát megosztáshoz feldolgozási okozza. 1901 (1.1901.0.95) verziójával kezdődően is használhatja a probléma megoldásához kattintva **frissítés most** újra (nem **folytatása**). A URP ezután törli a fájlokat az előző próbálkozás, és a letöltés ismét elindul.

- Futtatásakor [Test-AzureStack](azure-stack-diagnostic-test.md), megjelenik egy figyelmeztető üzenet az alaplapi felügyeleti vezérlő (BMC). Biztonságosan figyelmen kívül hagyhatja ezt a figyelmeztetést.

- <!-- 2468613 - IS --> Ez a frissítés telepítése során láthatja a címmel riasztások `Error - Template for FaultType UserAccounts.New is missing.` biztonságosan figyelmen kívül hagyhatja ezeket a riasztásokat. A riasztás automatikusan bezáródik a frissítés a telepítés befejezése után.

## <a name="post-update-steps"></a>Frissítés utáni lépések

- Ez a frissítés telepítése után bármely alkalmazandó gyorsjavítások telepítéséről. További információkért lásd: [gyorsjavítások](#hotfixes), valamint a [karbantartási szabályzat](azure-stack-servicing-policy.md).  

- Az adatok rest titkosítási kulcsok lekéréséhez és tárolja azokat biztonságosan kívül az Azure Stack üzemelő példányához. Kövesse a [útmutatást nyújt a kulcsok lekéréséhez](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Az alábbiakban a telepítés utáni ismert hibáit a build-verziószáma.

### <a name="portal"></a>Portál

<!-- 2930820 - IS ASDK --> 
- A rendszergazda és a felhasználói portálon Ha a "Docker," a cikk helytelenül adja vissza. Ez nem áll rendelkezésre, az Azure Stackben. Ha megpróbálja létrehozni, megjelenik egy panel, hibát jelezve. 

<!-- 2931230 - IS  ASDK --> 
- Nem lehet törölni a felhasználói előfizetés, mint egy kiegészítő csomag hozzáadott tervek, akkor is, ha a csomag eltávolítása a felhasználói előfizetés. A terv marad mindaddig, amíg az előfizetéseket, hogy a kiegészítő csomagot is törlődik. 

<!-- TBD - IS ASDK --> 
- A két felügyeleti előfizetés-típus verziójú 1804-es verzióban bevezetett nem használható. Az előfizetés-típusok a következők **előfizetés mérési**, és **Használatalapú előfizetés**. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a **alapértelmezett szolgáltató** előfizetés-típus.

<!-- 3557860 - IS ASDK --> 
- Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és ezután törölje a felhasználói előfizetések.

<!-- 1663805 - IS ASDK --> 
- Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként használja [engedélyek ellenőrzése érdekében PowerShell](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- Daniel 3/28 -->
- A felhasználói portálon lépjen egy blobba egy tárfiókon belül, és megpróbálja megnyitni **hozzáférési szabályzat** a navigációs fán, az azt követő ablak nem sikerül betölteni. A probléma megkerüléséhez az alábbi PowerShell-parancsmagok létrehozása, beolvasása, beállítást és hozzáférési házirendeket, illetve törlése engedélyezése:

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Amikor egy új Windows virtuális gép (VM) hoz létre, a következő hiba jelenhet meg:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   A hiba akkor fordul elő, ha a virtuális gép rendszerindítási diagnosztika engedélyezése, de a rendszerindítás-diagnosztikai tárfiók törlése. A probléma megkerüléséhez hozza létre újból a tárfiókot, a korábban használt azonos néven.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A virtual machine scale set (VMSS) létrehozási folyamatának 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. A rendszerképet az Azure Stacken nem érhető el, mert az üzembe helyezéshez használt egy másik operációs rendszert, vagy egy Azure Resource Manager-sablonnal, adjon meg egy másik CentOS rendszerképet, amely szerint a marketplace-ről üzembe helyezés előtt lett letöltve a operátor.  

<!-- TBD - IS ASDK --> 
- Frissítse a 1902 alkalmazása után, a következő problémák léphetnek a felügyelt lemezekkel rendelkező virtuális gépek üzembe helyezésekor:

   - Ha az előfizetés korábban jött létre a 1808 frissítése, a felügyelt lemezekkel rendelkező virtuális gép üzembe helyezése egy belső hiba miatt sikertelen lehet. A hiba elhárításához kövesse ezeket a lépéseket minden egyes előfizetés esetén:
      1. A bérlői portálon lépjen a **előfizetések** , és keresse meg az előfizetés. Válassza ki **erőforrás-szolgáltatók**, majd **Microsoft.Compute**, és kattintson a **újraregisztrálni**.
      2. Lépjen az azonos előfizetéshez tartozó **hozzáférés-vezérlés (IAM)** , és ellenőrizze, hogy **Azure Stack – felügyelt lemez** szerepel a listán.
   - Egy több-bérlős környezet van beállítva, ha a virtuális gépek üzembe helyezéséhez egy adott előfizetéshez tartozó és a Vendég címtár egy belső hiba miatt meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket a [Ez a cikk](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) újrakonfigurálása a Vendég címtárak mindegyike.

- Egy Ubuntu 18.04 létrehozott virtuális gép SSH-engedélyezési engedélyezve van a nem teszi lehetővé, hogy jelentkezzen be az SSH-kulcsok használata. Áthidaló megoldásként használja a Linux-bővítményt a Virtuálisgép-hozzáférés SSH-kulcsok megvalósításához a kiépítés után, vagy jelszóalapú hitelesítés használatára.

- A méretezési készlet nem távolítható el a **Virtual Machine Scale Sets** panelen. Áthidaló megoldásként válassza ki a méretezési csoport, hogy el kívánja távolítani, majd kattintson a **törlése** gombra a **áttekintése** ablaktáblán.

- Virtuális gépek létrehozása egy rendelkezésre állási csoport 3 tartalék tartomány az a és a VMSS-példány létrehozása sikertelen, és egy **FabricVmPlacementErrorUnsupportedFaultDomainSize** hiba történt egy 4 csomópontos Azure Stack-környezet a frissítési folyamat során. Egy rendelkezésre állási csoport sikeresen 2 hibatűrési tartományt is létrehozhat önálló virtuális gépek. Azonban VMSS-példány létrehozása nem még nem érhető el a frissítés során az egy 4 csomópontos Azure Stacken.

### <a name="networking"></a>Hálózat  

<!-- 3239127 - IS, ASDK -->
- Az Azure Stack portálon Ha módosít egy statikus IP-címet az IP-konfiguráció a Virtuálisgép-példányhoz csatolt hálózati adapterhez kötött látni fogja egy figyelmeztető üzenet arról, hogy 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Biztonságosan figyelmen kívül hagyhatja ezt az üzenetet; az IP-cím megváltozik, akkor is, ha a Virtuálisgép-példány nem indítja újra.

<!-- 3632798 - IS, ASDK -->
- A portálon, ha egy bejövő biztonsági szabály felvétele, és válassza a **Szolgáltatáscímke** forrásaként, több lehetőség megjelennek a **forráscímke** listája, amelyek nem érhetők el az Azure Stackhez. Érvényes, az Azure Stack csak lehetőségek a következők:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  A többi beállítást az Azure Stackben forrás nem támogatottak. Hasonlóképpen ha adjon hozzá egy kimenő biztonsági szabályt, és válassza **Szolgáltatáscímke** célhelyeként, ugyanezt a listát lehetőségei **forráscímke** jelenik meg. A csak érvényes beállítások ugyanazok, mint a **forráscímke**, amint az az előző listában.

- Hálózati biztonsági csoportok (NSG) nem működik az Azure Stackhez ugyanolyan módon globális Azure-ban. Az Azure-ban, több port adhatók meg egy NSG-szabály (a portal, PowerShell, használatával és a Resource Manager-sablonok). Az Azure Stackben azonban nem állíthatja be több portot egy NSG-szabályt a portálon keresztül. A probléma megkerüléséhez használja egy Resource Manager-sablon vagy a PowerShell segítségével ezek további szabályokat állíthat be.

<!-- 3203799 - IS, ASDK -->
- Az Azure Stack nem támogatja a több mint 4 hálózati adapter (NIC) való csatlakoztatás egy Virtuálisgép-példánnyal, a példány méretétől függetlenül.

- A felhasználói portálon, ha megpróbálja hozzáadni egy **Háttérkészlet** , egy **terheléselosztó**, a művelet meghiúsul, a hibaüzenet **nem sikerült frissíteni a Load Balancer...**  A probléma megkerüléséhez használja a PowerShell, a parancssori felület vagy az Azure Resource Manager-sablon a háttérkészlet társítása egy terheléselosztó-erőforráshoz.

- A felhasználói portálon, ha megpróbál létrehozni egy **bejövő NAT-szabály** számára egy **Load Balancer**, a művelet meghiúsul, a hibaüzenet **nem sikerült frissíteni a Load Balancer...**  A probléma megkerüléséhez használja a PowerShell, a parancssori felület vagy az Azure Resource Manager-sablon a háttérkészlet társítása egy terheléselosztó-erőforráshoz.

- A felhasználói portálon a **Load Balancer létrehozása** ablakban látható lehetőség hozzon létre egy **Standard** terheléselosztó Termékváltozat. Ez a beállítás nem támogatott az Azure Stackben.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- A storage erőforrás-szolgáltatót regisztrálnia kell, az előfizetés az első Azure-függvény létrehozása előtt.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Rendszernapló 

- A syslog-konfiguráció nincs megőrizve egy frissítés tartózkodnak okoz a syslog-ügyfél elveszíti a konfigurációját, és leállítja a továbbított syslog-üzeneteket. A probléma az Azure Stack minden verziójára vonatkozik, a syslog-ügyfél (1809) a nyilvánosan elérhető verzió óta. A probléma elkerüléséhez konfigurálja újra a syslog-ügyfél az Azure Stack-frissítés telepítését követően.

## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti az Azure Stack 1902 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload). 

Csak csatlakoztatott forgatókönyvekben Azure Stack üzemelő példányok rendszeres időnként ellenőrzik egy biztonságos végpontot, és automatikusan értesíti, ha egy frissítés érhető el a felhőben. További információkért lásd: [frissítések kezelése az Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>További lépések

- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  
