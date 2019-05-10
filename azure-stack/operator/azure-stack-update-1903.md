---
title: Azure Stack 1903 frissítése |} A Microsoft Docs
description: Ismerje meg Azure Stack integrált rendszerek, beleértve az újdonságokat, 1903 frissítésével kapcsolatos ismert problémák, valamint a helyét, a frissítés letöltése.
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
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/20/2019
ms.openlocfilehash: 04cec74873869f19c7bd762753f7fe89f51c184d
ms.sourcegitcommit: 39ba6d18781aed98b29ac5e08aac2d75c37bf18c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65386748"
---
# <a name="azure-stack-1903-update"></a>Azure Stack 1903 frissítése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti a 1903 csomag tartalmát. A frissítési fejlesztései, javításokat és új funkciók az Azure Stack ezen verziója tartalmazza. Ez a cikk is ebben a kiadásban az ismert problémákat, és tartalmaz egy hivatkozást a frissítés letöltése. Ismert problémákkal kapcsolatban közvetlenül a frissítési folyamat, és a build (telepítés utáni) problémái vannak felosztva.

> [!IMPORTANT]
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure Stack 1903 frissítés buildszáma **1.1903.0.35**.

> [!IMPORTANT]
> A 1903 hasznos adatai nem tartalmaz egy ASDK kiadást.

## <a name="hotfixes"></a>Gyorsjavítások

Az Azure Stack rendszeresen gyorsjavításait. Ne felejtse el telepíteni a [legújabb Azure Stack-gyorsjavítás](#azure-stack-hotfixes) az Azure Stack 1903 frissítése előtt 1902.

Az Azure Stack-gyorsjavítások csak alkalmazhatók az Azure Stackkel integrált rendszerek. Ne kísérelje meg a ASDK gyorsjavítások telepítése.

> [!TIP]
> Fizessen elő a következő *RSS* vagy *Atom* tartani az Azure Stack gyorsjavítások-hírcsatornák:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Az Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Az Azure Stack-gyorsjavítások

- **1902**: [KB 4500637 – az Azure Stack gyorsjavítás 1.1902.3.75](https://support.microsoft.com/help/4500637)
- **1903**: [KB 4500638 – az Azure Stack gyorsjavítás 1.1903.2.39](https://support.microsoft.com/help/4500638)

## <a name="improvements"></a>Fejlesztései

- A frissítés 1903 hasznos Azure Stack az Azure Stack gazdagépek nem tartalmazza az alapul szolgáló operációs rendszer összetevői frissítését tartalmazza. Ez lehetővé teszi az egyes frissítések felvenni a hatókörbe. Ennek eredményeképpen a várt idő vesz igénybe a 1903 frissítés végrehajtásához kisebb (KB. 16 órán át, de a pontos idő változhat). Futásidejű ez csökkenése a 1903 frissítést és későbbi frissítések tartalmazhatnak az operációs rendszert, úgy a különböző modulok frissítései. Jövőbeli frissítések hasonló útmutatást nyújtanak a frissítés várható idejét az adattartalomban szereplő függően.

- Kijavítva a hiba, a hálózat, amely ebben az esetben a módosítások a **üresjárat időkorlátja (perc)** értékét egy **nyilvános IP-cím** a lépnek érvénybe. Korábban a módosításokat erre az értékre figyelmen kívül hagyott, így a végrehajtott módosításokat, függetlenül az érték 4 perces alapértelmezett. Ez a beállítás határozza meg, hány percig, hogy a TCP-kapcsolat megnyitása nélkül az ügyfelek életben tartási üzenetek küldéséhez. Megjegyzés: Ez a hiba csak az érintett példány szintű nyilvános IP-címek, nem a nyilvános IP-címek hozzárendelve a terheléselosztóhoz.

- A frissítési motorral, beleértve a gyakori problémák automatikus szervizelését, hogy a frissítések alkalmazása megszakítás nélkül, a megbízhatóság fejlesztései.

- Az észlelési és alacsony lemezterületre vonatkozó feltételek szervizelésének fejlesztései.

- Az Azure Stack már támogatja a Windows Azure Linux-ügynökök verzió 2.2.35 nagyobb. Ez a támogatás lehetővé teszi az egységes Linux-rendszerképeket az Azure és az Azure Stack közötti fenntartásához. Ez a 1901 és 1902 gyorsjavítások részeként lett hozzáadva.

### <a name="secret-management"></a>Titkos kódok kezelése

- Az Azure Stack mostantól támogatja a főtanúsítvány titkos Elforgatás külső tanúsítványok által használt rotációját. További információ [ebben a cikkben](azure-stack-rotate-secrets.md).

- 1903 javított teljesítménye, a titkos Elforgatás belső titkos elforgatás végrehajtásához szükséges időt csökkentő tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

> [!IMPORTANT]
> Telepítse a [legújabb Azure Stack-gyorsjavítás](#azure-stack-hotfixes) esetében (ha vannak) 1902 1903 frissítése előtt.

- Ügyeljen arra, hogy a legújabb verzióját használja a [Azure Stack kapacitását planner](https://aka.ms/azstackcapacityplanner) ehhez a számítási feladatok tervezésekor és méretezése. A legújabb verzió hibajavításokat tartalmaz, és olyan megjelent új funkciókat biztosít minden egyes Azure Stack a frissítéssel.

- A frissítés telepítésének megkezdése előtt futtassa [Test-AzureStack](azure-stack-diagnostic-test.md) az Azure Stack állapotának érvényesítéséhez, és hárítsa el a működési hibákat talált a következő paraméterrel, többek között az összes figyelmeztetések és hibák esetén. Emellett tekintse át az aktív riasztások, és oldja meg az esetleges beavatkozást igénylő:

    ```powershell
   Test-AzureStack -Group UpdateReadiness
    ```

- Ha az Azure Stack System Center Operations Manager által felügyelt, ügyeljen arra, hogy frissítse a [felügyeleti csomag a Microsoft Azure Stackhez készült](https://www.microsoft.com/download/details.aspx?id=55184) verzióra 1.0.3.11 1903 alkalmazása előtt.

- Az Azure Stack-frissítési csomag formátuma megváltozott **.bin/.exe/.xml** való **.zip/.xml** a 1902 kiadástól kezdve. A csatlakoztatott Azure Stack skálázási egységek ügyfelek fogják látni a **frissítés érhető el** üzenet a portálon. Nem csatlakozó ügyfelek mostantól egyszerűen töltse le, és importálja a .zip-fájlt a megfelelő .xml.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>A frissítési folyamat ismert problémái

- Futtatásakor [Test-AzureStack](azure-stack-diagnostic-test.md), megjelenik egy figyelmeztető üzenet az alaplapi felügyeleti vezérlő (BMC). Biztonságosan figyelmen kívül hagyhatja ezt a figyelmeztetést.

<!-- 2468613 - IS -->
- Ez a frissítés telepítése során láthatja a címmel riasztások **hiba – typu FaultType UserAccounts sablont. Új nebyl nalezen.** Ezek a riasztások nyugodtan figyelmen kívül hagyhatja. A riasztás automatikusan bezáródik a frissítés a telepítés befejezése után.

## <a name="post-update-steps"></a>Frissítés utáni lépések

- Ez a frissítés telepítése után bármely alkalmazandó gyorsjavítások telepítéséről. További információkért lásd: [gyorsjavítások](#hotfixes), valamint a [karbantartási szabályzat](azure-stack-servicing-policy.md).

- Az adatok rest titkosítási kulcsok lekéréséhez és tárolja azokat biztonságosan kívül az Azure Stack üzemelő példányához. Kövesse a [útmutatást nyújt a kulcsok lekéréséhez](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Ismert problémák (telepítés utáni)

Az alábbiakban a telepítés utáni ismert hibáit a build-verziószáma.

### <a name="portal"></a>Portál

- Az a felhasználói portál irányítópultján kattintson a megkísérlésekor a **visszajelzés** csempét, egy üres böngészőlapon nyílik meg. Áthidaló megoldásként használhatja [Azure Stack felhasználói visszajelzési webhelyen](https://aka.ms/azurestackuservoice) a felhasználó hangalapú kérésben.

<!-- 2930820 - IS ASDK -->
- A rendszergazda és a felhasználói portálon Ha a "Docker," a cikk helytelenül adja vissza. Ez nem áll rendelkezésre, az Azure Stackben. Ha megpróbálja létrehozni, megjelenik egy panel, hibát jelezve.

<!-- 2931230 - IS  ASDK -->
- Nem lehet törölni a felhasználói előfizetés, mint egy kiegészítő csomag hozzáadott tervek, akkor is, ha a csomag eltávolítása a felhasználói előfizetés. A terv marad mindaddig, amíg az előfizetéseket, hogy a kiegészítő csomagot is törlődik.

<!-- TBD - IS ASDK -->
- A két felügyeleti előfizetés-típus verziójú 1804-es verzióban bevezetett nem használható. Az előfizetés-típusok a következők **előfizetés mérési**, és **Használatalapú előfizetés**. Ezek a típusok előfizetés új Azure Stack-környezetek verziójától kezdve az 1804 láthatók, de még nem használatra kész. Ön továbbra is használja a **alapértelmezett szolgáltató** előfizetés-típus.

<!-- 3557860 - IS ASDK -->
- Felhasználói előfizetések eredmények az árva erőforrások törlése. Áthidaló megoldásként először törölje a felhasználó vagy a teljes erőforráscsoportot, és ezután törölje a felhasználói előfizetések.

<!-- 1663805 - IS ASDK --> 
- Engedélyek nem tekintheti meg az Azure Stack portálok használata az előfizetéshez. Áthidaló megoldásként használja [engedélyek ellenőrzése érdekében PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).

<!-- Daniel 3/28 -->
- A felhasználói portálon lépjen egy blobba egy tárfiókon belül, és megpróbálja megnyitni **hozzáférési szabályzat** a navigációs fán, az azt követő ablak nem sikerül betölteni. A probléma megkerüléséhez az alábbi PowerShell-parancsmagok létrehozása, beolvasása, beállítást és hozzáférési házirendeket, illetve törlése engedélyezése:

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

  
<!-- Daniel 3/28 -->
- A felhasználói portálon, amikor megpróbálja feltölteni egy blob használatával a **OAuth(preview)** beállítást, a feladat egy hibaüzenettel meghiúsul. A probléma megkerüléséhez feltöltése a blob használatával a **SAS** lehetőséget.

- Ha bejelentkezett az Azure Stack portálok jelenhet meg a nyilvános Azure-portállal kapcsolatos értesítések. Biztonságosan figyelmen kívül hagyhatja ezeket az értesítéseket, mert nem jelenleg vonatkoznak az Azure Stackhez (például "1 új frissítés - elérhetők a következő frissítéseket: Az Azure portál április 2019 frissítése").

- A felhasználói portál irányítópultján kiválasztásakor a **visszajelzés** csempét, egy üres böngészőlapon nyílik meg. Áthidaló megoldásként használhatja [Azure Stack felhasználói visszajelzési webhelyen](https://aka.ms/azurestackuservoice) a User Voice kérésben.

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Amikor egy új Windows virtuális gép (VM) hoz létre, a következő hiba jelenhet meg:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   A hiba akkor fordul elő, ha a virtuális gép rendszerindítási diagnosztika engedélyezése, de a rendszerindítás-diagnosztikai tárfiók törlése. A probléma megkerüléséhez hozza létre újból a tárfiókot, a korábban használt azonos néven.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- A Virtual Machine Scale Set létrehozási folyamatának 7.2 CentOS-alapú üzembe helyezési lehetőséget biztosít. A rendszerképet az Azure Stacken nem érhető el, mert az üzembe helyezéshez használt egy másik operációs rendszert, vagy egy Azure Resource Manager-sablonnal, adjon meg egy másik CentOS rendszerképet, amely szerint a marketplace-ről üzembe helyezés előtt lett letöltve a operátor.

<!-- TBD - IS ASDK -->
- Frissítse a 1903 alkalmazása után, a következő problémák léphetnek a felügyelt lemezekkel rendelkező virtuális gépek üzembe helyezésekor:

   - Ha az előfizetés korábban jött létre a 1808 frissítése, a felügyelt lemezekkel rendelkező virtuális gép üzembe helyezése egy belső hiba miatt sikertelen lehet. A hiba elhárításához kövesse ezeket a lépéseket minden egyes előfizetés esetén:
      1. A bérlői portálon lépjen a **előfizetések** , és keresse meg az előfizetés. Válassza ki **erőforrás-szolgáltatók**, majd **Microsoft.Compute**, és kattintson a **újraregisztrálni**.
      2. Lépjen az azonos előfizetéshez tartozó **hozzáférés-vezérlés (IAM)**, és ellenőrizze, hogy **Azure Stack – felügyelt lemez** szerepel a listán.
   - Egy több-bérlős környezet van beállítva, ha a virtuális gépek üzembe helyezéséhez egy adott előfizetéshez tartozó és a Vendég címtár egy belső hiba miatt meghiúsulhat. A hiba elhárításához kövesse az alábbi lépéseket a [Ez a cikk](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) újrakonfigurálása a Vendég címtárak mindegyike.

- Egy Ubuntu 18.04 létrehozott virtuális gép SSH-engedélyezési engedélyezve van a nem teszi lehetővé, hogy az SSH-kulcsokkal való bejelentkezéshez használ. Áthidaló megoldásként használja a Linux-bővítményt a Virtuálisgép-hozzáférés SSH-kulcsok megvalósításához a kiépítés után, vagy jelszóalapú hitelesítés használatára.

- Ha egy életciklus állomás (HLH) nem rendelkezik: build 1902, mielőtt kellett állítani csoportházirend **Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirend\Biztonsági beállítások** való **LM- és NTLM - NTLMv2 munkamenet használja, ha az egyeztetett**. Build 1902, mivel azt hagyja **nincs definiálva** vagy - **csak a válasz küldése NTLMv2** (azaz az alapértelmezett érték). Ellenkező esetben nem lehet hozzon létre egy távoli PowerShell-munkamenetet, és megjelenik egy **a hozzáférés megtagadva** hiba:

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

- A méretezési készlet nem távolítható el a **Virtual Machine Scale Sets** panelen. Áthidaló megoldásként válassza ki a méretezési csoport, hogy el kívánja távolítani, majd kattintson a **törlése** gombra a **áttekintése** ablaktáblán.

### <a name="networking"></a>Hálózat

<!-- 3239127 - IS, ASDK -->
- Az Azure Stack portálon Ha módosít egy statikus IP-címet az IP-konfiguráció a Virtuálisgép-példányhoz csatolt hálózati adapterhez kötött látni fogja egy figyelmeztető üzenet arról, hogy

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

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

Letöltheti az Azure Stack 1903 frissítési csomagot [Itt](https://aka.ms/azurestackupdatedownload).

Csak csatlakoztatott forgatókönyvekben Azure Stack üzemelő példányok rendszeres időnként ellenőrzik egy biztonságos végpontot, és automatikusan értesíti, ha egy frissítés érhető el a felhőben. További információkért lásd: [frissítések kezelése az Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>További lépések

- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).
