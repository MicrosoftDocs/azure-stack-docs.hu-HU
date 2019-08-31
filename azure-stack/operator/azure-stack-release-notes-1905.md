---
title: Azure Stack 1905 kibocsátási megjegyzések | Microsoft Docs
description: Ismerkedjen meg a 1905-es frissítéssel Azure Stack integrált rendszerek esetében, beleértve az újdonságokat, az ismert problémákat, valamint a frissítés letöltésének helyét.
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
monikerRange: azs-1905
ms.openlocfilehash: 27a41069c3406cdb843e263ee49a63e5c5919477
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188321"
---
# <a name="azure-stack-1905-update"></a>Azure Stack 1905 frissítés

*Vonatkozik: Integrált rendszerek Azure Stack*

Ez a cikk az 1905-es frissítési csomag tartalmát ismerteti. A frissítés a Azure Stack ezen kiadásának újdonságait és javításait tartalmazza. Ez a cikk a következő információkat tartalmazza:

- [Az Újdonságok, a javítások, a javítások és a biztonsági frissítések leírása](#whats-in-this-update)
- [Frissítés tervezése](#update-planning)

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

A Azure Stack 1905 frissítési Build száma **1.1905.0.40**.

### <a name="update-type"></a>Frissítés típusa

A Azure Stack 1905 frissítési Build típusa **megtelt**. Ennek eredményeképpen az 1905-es frissítés hosszabb futtatókörnyezettel rendelkezik, mint az expressz frissítések, például a 1903 és a 1904. A teljes frissítésekhez tartozó pontos futtatókörnyezetek általában a Azure Stack-példány által tartalmazott csomópontok számától függenek, a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha csatlakozik az internethez) és a rendszer hardvere Configuration. Az 1905-es frissítés a belső tesztelés során a következő várt futtatókörnyezeteket használta: 4 csomópont – 35 óra, 8 csomópont – 45 óra, 12 csomópont – 55 óra, 16 csomópont – 70 óra. a várt értéknél hosszabb ideig tartó 1905-es futtatókörnyezet nem ritka, és nem igényel beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul. További információ a frissítési buildek típusairól: [frissítések kezelése Azure Stackban](azure-stack-updates.md).

## <a name="whats-in-this-update"></a>A frissítés ismertetése

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Ezzel a frissítéssel a Azure Stack frissítési motorja frissítheti a skálázási egység csomópontjainak belső vezérlőprogramját. Ehhez a hardveres partnereknek megfelelő frissítési csomagot kell megadnia. A rendelkezésre állással kapcsolatos részletekért forduljon a hardveres partnerhez.

- A Windows Server 2019 mostantól támogatott, és elérhető a Azure Stack piactéren.
Ezzel a frissítéssel a Windows Server 2019 sikeresen aktiválható egy 2016-gazdagépen.

- Az új [Azure-fiók Visual Studio Code-bővítmény](../user/azure-stack-dev-start-vscode-azure.md) lehetővé teszi a fejlesztők számára, hogy az előfizetések beléptetésével és megtekintésével, valamint számos más szolgáltatással célozzák meg Azure stack. Az Azure-fiók bővítmény Azure Active Directory (Azure AD) és AD FS környezetekben is működik, és csak kis változást igényel a Visual Studio Code felhasználói beállításaiban. A Visual Studio Code megköveteli, hogy egy egyszerű szolgáltatásnév engedélyt kapjon a környezet futtatásához. Ehhez importálja az Identity scriptet, és futtassa az [Azure Stack több-bérlőben](../operator/azure-stack-enable-multitenancy.md)megadott parancsmagokat. Ehhez frissíteni kell a kezdőkönyvtárat, és regisztrálnia kell a vendég bérlői könyvtárat minden címtárhoz. A 1905-es vagy újabb verzióra történő frissítés után riasztás jelenik meg annak a kezdőkönyvtár-bérlőnek a frissítéséhez, amelyhez a Visual Studio Code egyszerű szolgáltatásnév tartozik. 

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->
- A TLS 1,2 Azure Stackon való betartatásának részeként a következő bővítmények frissültek az alábbi verziókra:

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - Microsoft. antimalware – Windows-ARM-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  Töltse le azonnal a bővítmények ezen verzióit, hogy a bővítmény új telepítései ne legyenek sikertelenek, ha a TLS 1,2 egy későbbi kiadásban van érvényben. Mindig állítsa be a **autoUpgradeMinorVersion = True** értéket, hogy a rendszer a bővítmények (például 1,8 – 1,9) alverziójának frissítéseit automatikusan elvégezze.

- A Azure Stack-portál új **Súgó és támogatás áttekintése** megkönnyíti az operátorok számára a támogatási lehetőségek ellenőrzését, szakértői segítség kérését, és további információ a Azure stack. Az integrált rendszereken a támogatási kérelem létrehozásakor Azure Stack szolgáltatás előválasztása is megtörténik. Javasoljuk, hogy az ügyfelek ezt a lehetőséget használják a jegyek küldésére a globális Azure Portal használata helyett. További információ: [Azure stack Súgó és támogatás](azure-stack-help-and-support-overview.md).

- Ha több Azure Active Directory van bevezetve ( [ezzel a folyamattal](azure-stack-enable-multitenancy.md)), akkor figyelmen kívül hagyhatja a parancsfájl újbóli futtatását, amikor bizonyos frissítések történnek, vagy ha az Azure ad-szolgáltatás engedélyének módosítása miatt a jogosultságok hiányoznak. Ez különféle problémákat okozhat, a letiltott hozzáféréstől bizonyos szolgáltatások esetében, így olyan diszkrét hibákhoz vezethet, amelyek nehezen követhetik vissza az eredeti problémát. Ennek megelőzése érdekében a 1905 egy új szolgáltatást vezet be, amely ellenőrzi ezeket az engedélyeket, és riasztást hoz létre, amikor bizonyos konfigurációs problémák találhatók. Ez az ellenőrzés óránként fut, és megjeleníti a probléma megoldásához szükséges szervizelési műveleteket. A riasztás akkor zárul le, ha az összes bérlő kifogástalan állapotban van.

- Az infrastruktúra biztonsági mentési műveleteinek jobb megbízhatósága a szolgáltatás feladatátvétele során.

- A [Azure stack Nagios beépülő modul](azure-stack-integrate-monitor.md#integrate-with-nagios) új verziója érhető el, amely a [Azure Active Directory Authentication librarys](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) protokollt használja a hitelesítéshez. A beépülő modul mostantól támogatja az Azure AD és a Active Directory összevonási szolgáltatások (AD FS) (AD FS) Azure Stack üzembe helyezését is. További információ: a [Nagios beépülő modul Exchange](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details) -webhelye.

- Megjelent egy új hibrid profil **2019-03-01 hibrid** , amely a Azure stack összes legújabb funkcióját támogatja. A Azure PowerShell és az Azure CLI egyaránt támogatja az **2019-03-01-Hybrid** profilt. A .NET, a Ruby, a Node. js, a Go és a Python SDK olyan közzétett csomagokat tartalmaz, amelyek támogatják az **2019-03-01-Hybrid** profilt. A megfelelő dokumentáció és néhány minta frissítve lett, hogy tükrözze a módosításokat.

- A [Node. js SDK](https://www.npmjs.com/search?q=2019-03-01-hybrid) mostantól támogatja az API-profilokat. Az **2019-03-01-Hybrid** profilt támogató csomagok közzé lesznek téve.

- Az 1905 Azure Stack Update két új infrastruktúra-szerepkört biztosít a platform megbízhatóságának és támogatásának javításához:

  - **Infrastruktúra-gyűrű**: A jövőben az infrastruktúra-gyűrű a meglévő infrastruktúra-szerepkörök (például XRP) tároló verzióit fogja üzemeltetni, amelyekhez jelenleg saját kijelölt infrastruktúra-virtuális gépek szükségesek. Ez növeli a platform megbízhatóságát, és csökkenti a Azure Stack által igényelt infrastruktúra-virtuális gépek számát. Ez a későbbiekben csökkenti a Azure Stack infrastruktúra-szerepköreinek teljes erőforrás-felhasználását a jövőben.
  - **Támogatási kör**: A jövőben a támogatási gyűrűt az ügyfelek számára továbbfejlesztett támogatási forgatókönyvek kezelésére fogjuk használni.  

  Emellett a tartományvezérlő virtuális gép egy további példányát is hozzáadta a szerepkör jobb rendelkezésre állásához.

  Ezek a változások a következő módokon növelik Azure Stack infrastruktúra erőforrás-felhasználását:
  
    | Azure Stack SKU | A számítási felhasználás növekedése | Megnövekedett memória-felhasználás |
    | -- | -- | -- |
    |4 csomópont|22 vCPU|28 GB|
    |8 csomópont|38 vCPU|44 GB|
    |12 csomópont|54 vCPU|60 GB|
    |16 csomópont|70 vCPU|76 GB|
  
### <a name="changes"></a>Módosítások

- A tervezett és nem tervezett karbantartási forgatókönyvek megbízhatóságának és rendelkezésre állásának növeléséhez Azure Stack egy további infrastrukturális szerepkör-példányt hoz létre a tartományi szolgáltatásokhoz.

- Ezzel a frissítéssel a javítás és a csomópontok hozzáadása művelet során a rendszer ellenőrzi a hardvert, hogy a méretezési egységen belül a homogén méretezési egység csomópontjai legyenek.

- Ha az ütemezett biztonsági mentések sikertelenek, és a megadott megőrzési idő túllépve, az infrastruktúra-biztonságimásolat-vezérlő biztosítja, hogy legalább egy sikeres biztonsági mentést őrizzen meg. 

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Kijavítva egy probléma, amelyben a **számítási gazdagép ügynöke** figyelmeztetést kapott egy csomópontnak a skálázási egységben való újraindítása után.

- A felügyeleti portálon a piactér felügyeletének kijavított hibái, amelyek helytelen eredményeket mutattak a szűrők alkalmazása során, és a közzétevő szűrőben ismétlődő közzétevők nevét mutatták. A teljesítménnyel kapcsolatos előrelépések is megtörténtek az eredmények gyorsabb megjelenítéséhez.

- A rendelkezésre álló biztonsági mentési panelen rögzített hiba történt, amely egy új elérhető biztonsági mentést jelzett, mielőtt befejezte a feltöltést a külső tároló helyére. A rendelkezésre álló biztonsági mentés mostantól megjelenik a listában, miután sikeresen feltöltötte azt a tárolási helyre. 

<!-- ICM: 114819337; Task: 4408136 -->
- Kijavítva a helyreállítási kulcsok biztonsági mentési művelet közben történő beolvasásával kapcsolatos probléma. 

<!-- Bug: 4525587 -->
- Kijavítva a hiba az OEM-frissítésben "nem definiált" verzióként az operátori portálon.

### <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack ezen frissítésében található biztonsági frissítésekről: [Azure stack biztonsági frissítések](azure-stack-release-notes-security-updates.md).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](azure-stack-release-notes-known-issues-1905.md)
- [Biztonsági frissítések](azure-stack-release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1905 frissítési csomagot [a Azure stack letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le. A letöltött eszköz használatakor ügyeljen arra, hogy a legújabb verziót használja, és ne a gyorsítótárból származó másolatot a letöltések könyvtárból.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1904-es Azure Stack legújabb gyorsjavítást telepíti a 1905-es Azure Stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="before-applying-the-1905-update"></a>Az 1905-es frissítés alkalmazása előtt

A Azure Stack 1905-es kiadását a 1904-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>Az 1905-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1905.3.48](https://support.microsoft.com/help/4510078)

## <a name="automatic-update-notifications"></a>Automatikus frissítési értesítések

Az infrastruktúra-hálózatról az internetre elérő rendszerekkel rendelkező ügyfelek az **elérhető frissítés** üzenet jelenik meg az operátori portálon. Az internet-hozzáféréssel nem rendelkező rendszerek a megfelelő. xml fájllal tölthetik le és importálhatók a. zip fájlt.

> [!TIP]  
> Feliratkozás a következő *RSS* -vagy *Atom* -hírcsatornára Azure stack gyorsjavítások megtartásához:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivált kibocsátási megjegyzések

[A Azure stack kibocsátási megjegyzések régebbi verzióit a TechNet Gallery webhelyen](http://aka.ms/azsarchivedrelnotes)tekintheti meg. Ezek az archivált kibocsátási megjegyzések kizárólag referencia jellegűek, és nem jelentenek támogatást ezekhez a verziókhoz. További információ a Azure Stack támogatásáról: [Azure stack karbantartási szabályzat](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálatához.

## <a name="next-steps"></a>További lépések

- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  

