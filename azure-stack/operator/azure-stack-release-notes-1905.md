---
title: Az Azure Stack 1905 kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg Azure Stack integrált rendszerek, beleértve az újdonságokat, 1905 frissítésével kapcsolatos ismert problémák, valamint a helyét, a frissítés letöltése.
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
ms.date: 06/14/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: 4c7e48804b4336c658376b1c3f611dbe75cf8c1f
ms.sourcegitcommit: 593d40bccf1b2957a763017a8a2d7043f8d8315c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152468"
---
# <a name="azure-stack-1905-update"></a>Azure Stack 1905 frissítése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti a 1905 csomag tartalmát. A frissítés magában foglalja az új fejlesztések és javítások ebben a kiadásban az Azure Stack. Ez a cikk a következő információkat tartalmazza:

- [What's new leírása fejlesztései, a javításokat és a biztonsági frissítések](#whats-in-this-update)
- [Tervezés módosítása](#update-planning)

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure Stack 1905 frissítés buildszáma **1.1905.0.40**.

### <a name="update-type"></a>Frissítés típusa

Az Azure Stack 1905 update build típusa **teljes**. Ennek eredményeképpen a 1905 frissítés rendelkezik, mint például a 1903 és 1904 expressz frissítések egy hosszabb futásidejű. Teljes frissítések pontos modulok általában függenek a csomópontok számát, hogy az Azure Stack-példány tartalmaz, használja a rendszer a bérlői terhelések, a rendszer hálózati kapcsolatot (ha csatlakozik az internethez), a kapacitás és a rendszer hardver konfiguráció. A 1905 frissítés volt-e a következő várt futtatókörnyezetek közül a belső tesztelés: 4 csomóponttal – 35 üzemóra, 8 csomópont - óra 45, 12 csomópontok - 55, 16 csomóponttal - 70 óra. a várt értékek hosszabb ideig tart 1905 modulok nem ritka, és nem igényelnek Azure Stack operátorai művelet, kivéve, ha a frissítés sikertelen. További információt a frissítés build típusú, lásd: [kezelheti a frissítéseket az Azure Stackben](azure-stack-updates.md).

## <a name="whats-in-this-update"></a>Mi az a frissítés

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Ezzel a frissítéssel az Azure Stack a frissítési motorral a belső vezérlőprogram skálázási egység csomópontokat is frissítheti. Ehhez a megfelelő csomag a hardvergyártó partnerektől. Keresse fel a rendelkezésre állási adatait a hardver partneréhez.

- A Windows Server 2019 már az Azure Stack piactéren keresztül támogatott, és elérhető konzorcium.
Ez a frissítés a Windows Server 2019 sikeresen aktiválhatók 2016 gazdagépen.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->
- Az Azure Stack a TLS 1.2 kényszerítése részeként a következő bővítményeket, ezek a fájlok frissültek:

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  Töltse le a bővítmények ezen verziói azonnali, úgy, hogy a bővítmény új központi telepítéseknél sikertelen, ha a TLS 1.2 van érvényben egy későbbi kiadásban. Mindig **autoupdateminorversion = true** , hogy alverzió frissítések Extensions (például 1.8-as, 1.9) automatikusan megtörténik.

- Egy új **Súgó és támogatási áttekintése** az Azure stack portal megkönnyíti a operátorok ellenőrizze a támogatási lehetőségek, szakértői támogatás és további információ az Azure Stackről. Integrált rendszerek hozzon létre egy támogatási kérést fog előre kiválasztja az értékhez az Azure Stack szolgáltatás. Javasoljuk, hogy használják, ez a tapasztalat elküldeni a jegyek helyett a globális Azure portal használatával. További információkért lásd: [Azure Stack-Súgó és támogatás](azure-stack-help-and-support-overview.md).

- Ha több Azure Active-címtár-e előkészítve (keresztül [Ez a folyamat](azure-stack-enable-multitenancy.md)), lehetséges elmulasztják a parancsfájl megismétlése bizonyos frissítések esetén, vagy ha az AAD-szolgáltatásnév engedélyezési módosítása hatására a jogosultságokat kell Hiányzó. Ez különböző problémákat okozhat, letiltott hozzáférés az egyes funkciókat, további diszkrét hibákkal szemben, amelyek nehezen vissza az eredeti probléma a nyomkövetési. Ennek megelőzése érdekében 1905, amely ellenőrzi, hogy ezeket az engedélyeket, és létrehoz egy riasztást, ha bizonyos konfigurációs problémákat talált új szolgáltatást vezet be. Ellenőrzés óránként fut le, és a probléma megoldásához szükséges javítási műveleteket jeleníti meg. A riasztás bezárása után a bérlők számára a kifogástalan állapotban vannak.

- Az infrastruktúra biztonsági mentési műveletek során a szolgáltatás feladatátvételének megbízhatósága.

- Egy új verziója a [Azure Stack Nagios beépülő modul](azure-stack-integrate-monitor.md#integrate-with-nagios) van elérhető használó a [Azure Active Directory hitelesítési kódtárai](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) a hitelesítéshez. A beépülő modul mostantól is támogatja az Azure Stack az Azure Active Directory (AAD) és az Active Directory összevonási szolgáltatások (ADFS) központi telepítései. További információkért lásd: a [Nagios beépülő modul exchange](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details) hely.

- Új hibrid profil **2019-03-01-hibrid** jelent meg, amely támogatja a legújabb funkciók, az Azure Stackben. Azure PowerShell és az Azure CLI-támogatás a **2019-03-01-hibrid** profilt. A .NET, Ruby, Node.js, Go és a Python SDK-k közzétételét, amelyek támogatják a csomagok a **2019-03-01-hibrid** profilt. A megfelelő dokumentációt és bizonyos mintákat frissítve lett-e az elvégzett módosításoknak megfelelően.

- A [Node.js SDK](https://www.npmjs.com/search?q=2019-03-01-hybrid) mostantól támogatja az API-profilok. A támogatási csomagok a **2019-03-01-hibrid** profil vannak közzétéve.

- Az Azure Stack 1905 frissítés ad hozzá a két új infrastruktúra-szerepkörök platform megbízhatóság és támogathatóság javítása érdekében:

  - **Infrastruktúrával ring**: A jövőben az infrastruktúrával ring meglévő infrastruktúra-szerepkörök – például xrp – saját kijelölt infrastruktúra virtuális gépek jelenleg igénylő tárolóalapú verzióját fogja futtatni. Ezzel továbbfejlesztjük a platform megbízhatóságát, és csökkentse infrastruktúra Azure Stack igénylő virtuális gépek számát. Ez csökkenti a teljes erőforrás-használat az Azure Stack-infrastruktúra-szerepkörök a jövőben ezt követően.
  - **Támogatási kör**: A jövőben a támogatási kör használható ügyfelek számára továbbfejlesztett támogatást forgatókönyvek kezeléséhez.  

  Emellett a tartományvezérlő virtuális Gépnek a jobb rendelkezésre állás érdekében a szerepkör egy külön példányát hozzáadtunk.

  Ezek a változások növeli az erőforrás-használat az Azure Stack-infrastruktúra a következő módokon:
  
    | Az Azure Stack Termékváltozat | Növelheti a számítási fogyasztás | Növeli a memóriahasználat |
    | -- | -- | -- |
    |4 csomópont|22 vCPU|28 GB|
    |8 csomópont|38 vCPU|44 GB|
    |12 csomópontok|54 vCPU|60 GB|
    |16 csomóponttal|70 vCPU|76 GB|
  
- Most már rendelkezésre áll egy Azure Stack-bővítmény, amely a Visual Studio Code működik. Használatával a **Azure-fiók** bővítmény, a fejlesztők is a célzott Azure Stack jelentkezik be, és az előfizetések, valamint számos egyéb szolgáltatás megtekintésével. Az Azure-fiókkiterjesztés AAD-környezetek és is AD FS-környezetben működik, és módosítsa a Visual Studio Code az Azure Stack metaadatértékeket bemeneti felhasználói beállításait csak szükséges. További információ [lásd itt található dokumentáció](../user/azure-stack-dev-start-vscode-azure.md).

### <a name="changes"></a>Módosítások

- A tervezett és nem tervezett karbantartási helyzetekre során növeli a megbízhatóságot és rendelkezésre állást, Azure Stack ad tartományi szolgáltatások szerepkör további infrastruktúra példányát.

- Ez a frissítés, javítás során, és adja hozzá a csomópont műveletek, a hardver érvényesítése annak biztosítása érdekében a homogén skálázási egység csomópontjai a skálázási egység.

- Ha ütemezett biztonsági mentések végrehajtásához nem működnek, és a meghatározott adatmegőrzési időszak túllépése, a biztonsági mentési infrastruktúra-vezérlő biztosítja legalább egy sikeres mentés megmarad. 

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Javítva lett egy probléma, amelyben egy **számítási gazdagép-ügynök** figyelmeztetés a skálázási egységben egy csomópont újraindítását követően jelent meg.

- A piactér-kezelés a rendszergazdai portálon, amely szerint a hibás eredményeket szűrők lett alkalmazva, és bemutatta duplikált kiadó nevét, a közzétevő szűrő rögzített problémákat. Is teljesítménnyel kapcsolatos fejlesztések gyorsabb eredmények megjelenítéséhez.

- Kijavítva a hiba az elérhető biztonsági mentés panelen szereplő elérhető új biztonsági másolatot, mielőtt az a külső tárolási helyen való feltöltés befejeződött. Most az elérhető biztonsági másolat jelennek meg a listában, a tárolási hely sikeres feltöltés után. 

<!-- ICM: 114819337; Task: 4408136 -->
- Kijavítva a hiba helyreállítási kulcsok lekérése a biztonsági mentési művelet során. 

<!-- Bug: 4525587 -->
- OEM kapcsolatos hiba kijavítva operátor portal "nem definiált" megjelenítésének verzióra frissíteni.

### <a name="security-updates"></a>Biztonsági frissítések

Azure Stack ebben a frissítésben biztonsági frissítésekkel kapcsolatos információk: [Azure Stack biztonsági frissítések](azure-stack-release-notes-security-updates-1905.md).

## <a name="update-planning"></a>Tervezés módosítása

A frissítés alkalmazásához, mindenképpen tekintse át a következő információkat:

- [Ismert problémák](azure-stack-release-notes-known-issues-1905.md)
- [Biztonsági frissítések](azure-stack-release-notes-security-updates-1905.md)
- [Alkalmazása a frissítés előtti és utáni tevékenységek ellenőrzőlista](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti az Azure Stack 1905 frissítési csomagot [az Azure Stack letöltés oldal](https://aka.ms/azurestackupdatedownload). A letöltési segédprogramja eszköz használata esetén mindenképp a legújabb verzióra, és nem a letöltések címtárból gyorsítótárban található példányát használja.

## <a name="hotfixes"></a>Gyorsjavítások

Az Azure Stack rendszeresen gyorsjavításait. Mindenképpen a legújabb Azure Stack gyorsjavítás telepítése 1904 az Azure Stack 1905 frissítése előtt.

Az Azure Stack-gyorsjavítások csak alkalmazhatók az Azure Stackkel integrált rendszerek. Ne kísérelje meg a ASDK gyorsjavítások telepítése.

### <a name="before-applying-the-1905-update"></a>A 1905 alkalmazása előtt frissítése

Az Azure Stack 1905 kiadását kell alkalmazni a 1904 kiadás a következő gyorsjavításokat együtt:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Az Azure Stack gyorsjavítás 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>Frissítse a 1905 sikeres alkalmazása után

Ez a frissítés telepítése után bármely alkalmazandó gyorsjavítások telepítéséről. További információkért lásd: a [karbantartási szabályzat](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Nincs elérhető 1905 gyorsjavítást.

## <a name="automatic-update-notifications"></a>Az automatikus frissítési értesítések

Ügyfelek, a rendszerek, amelyek hozzáférnek az internethez, az infrastruktúra-hálózaton fogják látni a **frissítés érhető el** az operátor Portal webhelyen megjelenő. Internet-hozzáférés nélküli rendszerekhez letöltheti, és importálja a megfelelő .xml a .zip fájlt.

> [!TIP]  
> Fizessen elő a következő *RSS* vagy *Atom* tartani az Azure Stack gyorsjavítások-hírcsatornák:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Az Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivált kibocsátási megjegyzések

Látható [Azure Stack régebbi verzióinak kibocsátási megjegyzések a TechNet-katalógusban](http://aka.ms/azsarchivedrelnotes). Ezek a kibocsátási megjegyzések kizárólag tájékoztatási célt szolgálnak, és jelenti ezen verziók támogatása archivált. Azure Stack-támogatással kapcsolatos információkért lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálathoz.

## <a name="next-steps"></a>További lépések

- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  

