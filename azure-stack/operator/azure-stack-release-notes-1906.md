---
title: Az Azure Stack 1906 kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg Azure Stack integrált rendszerek, beleértve az újdonságokat, 1906 frissítésével kapcsolatos ismert problémák, valamint a helyét, a frissítés letöltése.
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
ms.date: 07/01/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/01/2019
ms.openlocfilehash: 0ed50a370fd3a47c6721777b07faa9146cea16b1
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492364"
---
# <a name="azure-stack-1906-update"></a>Azure Stack 1906 frissítése

*Vonatkozik: Az Azure Stack integrált rendszerek*

Ez a cikk ismerteti a 1906 csomag tartalmát. A frissítés magában foglalja az új fejlesztések és javítások ebben a kiadásban az Azure Stack.

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

Az Azure Stack 1906 frissítés buildszáma **1.1906.0.30**.

### <a name="update-type"></a>Frissítés típusa

Az Azure Stack 1906 update build típusa **Express**. További információt a frissítés build típusú, tekintse meg a [kezelheti a frissítéseket az Azure Stackben](azure-stack-updates.md) cikk. A 1906 frissítés befejezéséhez szükséges várt idő körülbelül 10 óra. Pontos frissítés modulok általában függ a kapacitást a bérlői terhelések, a rendszer hálózati kapcsolatot, (ha csatlakozik az internethez), a rendszer használja, és a rendszer hardverkonfigurációja. A várt érték hosszabb ideig tart a modulok nem ritka, és nem igényelnek Azure Stack operátorai művelet, kivéve, ha a frissítés sikertelen. A futtatókörnyezet előállításához a 1906 Update és más Azure Stack-frissítések nem hasonlíthatók.

## <a name="whats-in-this-update"></a>Mi az a frissítés

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Hozzáadott egy **Set-TLSPolicy** parancsmagot emelt szintű végpontját (EGP) a TLS 1.2 kényszerítése minden végponton. További információkért lásd: [Azure Stack biztonsági vezérlők](azure-stack-security-configuration.md).

- Hozzáadott egy **Get-TLSPolicy** parancsmagot emelt szintű végpontját (EGP) a TLS szabályzat lekéréséhez. További információkért lásd: [Azure Stack biztonsági vezérlők](azure-stack-security-configuration.md).

- Hozzáadott egy belső titkos Elforgatás eljárás rotálása belső TLS-tanúsítványokat a rendszer frissítés közben.

- Hozzáadott egy biztonságos, hogy a belső titkos kulcsok lejárat belső titkos kódok rotációja segítségként, abban az esetben a lejáró titkos kódok kritikus riasztást a rendszer figyelmen kívül hagyja. Ez kell nem lehet hivatkozni egy normál működési eljárás szerint. Titkos kódok rotációja meg kell tervezni a karbantartási időszak alatt. További információkért lásd: [Azure Stack titkos Elforgatás](azure-stack-rotate-secrets.md).

- A Visual Studio Code mostantól támogatott az Azure Stack telepítése az AD FS használatával.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- A **Get-GraphApplication** kiemelt végpontját a parancsmag mostantól megjeleníti a jelenleg használt tanúsítvány ujjlenyomatát. A tanúsítványkezelés, az egyszerű szolgáltatások Ez növeli a az AD FS az Azure Stack üzembe helyezésekor.

- Új figyelési állapotszabályok AD Graph és az Active Directory összevonási szolgáltatások, többek között legyen riasztás rendelkezésre állásának ellenőrzése lettek hozzáadva.

- A biztonsági mentési erőforrás-szolgáltatót, ha egy másik példánya kerül a biztonsági mentési infrastruktúra-szolgáltatás, a megbízhatóság fejlesztései.

- Külső titkos Elforgatás eljárás biztosít olyan egységes végrehajtási idő megkönnyítése érdekében a karbantartási időszak ütemezése a teljesítmény optimalizálása.

- A **Test-AzureStack** parancsmag mostantól hamarosan lejár (kritikus riasztások) belső titkokat kapcsolatos jelentések.

- Új paraméter érhető el a **Register-CustomAdfs** parancsmag, amely lehetővé teszi, hogy a rendszer kihagyja a visszavont tanúsítványok listájának ellenőrzése az AD FS összevonási megbízhatósági konfigurálásakor rendszerjogosultságú végpontját.

- A 1906 kiadás vezet jobb áttekinthetősége frissítés van folyamatban, így biztos lehet abban is, hogy a frissítések nem szünetel. Az eredmény látható az operátorok számára a frissítési lépéseket teljes számának növelését a **frissítése** panelen. Azt is megfigyelheti több frissítése történik a párhuzamos, mint a korábbi frissítés lépéseit.

#### <a name="networking-updates"></a>Hálózatkezelés frissítések

- Frissített bérleti idő beállítása a DHCP-válaszadó konzisztens Azure-ral.

- Továbbfejlesztett újrapróbálkozási díjait a forgatókönyvben az erőforrások telepítése sikertelen volt. az erőforrás-szolgáltatónál.

- Eltávolítja a **Standard** Termékváltozat, amely jelenleg nem támogatott mind a terheléselosztóról, mind a nyilvános IP-cím, a beállítást.

### <a name="changes"></a>Módosítások

- Most már Azure összhangban egy storage-fiók megoldást vezet be.

- A belső titkos kulcsok lejárati riasztás eseményindítók módosult:
  - Figyelmeztetési szintű riasztások most aktiválódnak a titkos kulcsok a lejárat előtt 90 nappal.
  - Kritikus riasztások most előállított titkos kódok lejárta előtt 30 nap.

- Biztonsági mentési erőforrás-szolgáltató infrastruktúra egységes terminológia frissített karakterláncokat.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Javítva lett egy probléma, ahol a felügyelt lemezes virtuális gép átméretezése sikertelen volt egy **belső működési hiba**.

- Javítva, ahol egy sikertelen felhasználói lemezkép létrehozása helyez a szolgáltatás, amely kezeli a lemezképek; rossz állapotban van Ez a beállítás megakadályozza a sikertelen lemezkép törlése és új-lemezképek létrehozása. Ez a 1905 gyorsjavítás is rögzíteni.

- Aktív riasztások a belső lejáró titkos most már automatikusan belső titkos elforgatásának szögét sikeres végrehajtása után bezárul.

- Kijavítva, amelyben a frissítés időtartama a frissítési előzmények lapon lenne trim az első számjegy, ha a frissítés több mint 99 óráig futott.

- A **frissítés** panelen többek között egy **folytatása** sikertelen frissítések lehetőséget.

- A rendszergazdai és felhasználói portálok, javítva a Marketplace-en, amelyet a Docker-bővítmény nem megfelelően visszaadásához a keresés, de további művelet sikerült kell venni, nem érhető el az Azure Stackben.

- Javítva lett egy probléma sablontelepítés felhasználói felület, amely nem tölti fel a paramétereket, ha a sablon neve kezdődik aláhúzásjellel (_).

## <a name="security-updates"></a>Biztonsági frissítések

Azure Stack ebben a frissítésben biztonsági frissítésekkel kapcsolatos információk: [Azure Stack biztonsági frissítések](azure-stack-release-notes-security-updates-1906.md).

## <a name="update-planning"></a>Tervezés módosítása

A frissítés alkalmazásához, mindenképpen tekintse át a következő információkat:

- [Ismert problémák](azure-stack-release-notes-known-issues-1906.md)
- [Biztonsági frissítések](azure-stack-release-notes-security-updates-1906.md)
- [Alkalmazása a frissítés előtti és utáni tevékenységek ellenőrzőlista](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>A frissítés letöltése

Letöltheti az Azure Stack 1906 frissítési csomagot [az Azure Stack letöltés oldal](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Gyorsjavítások

Az Azure Stack rendszeresen gyorsjavításait. Mindenképpen a legújabb Azure Stack gyorsjavítás telepítése 1905 az Azure Stack 1906 frissítése előtt.

Az Azure Stack-gyorsjavítások csak alkalmazhatók az Azure Stackkel integrált rendszerek. Ne kísérelje meg a ASDK gyorsjavítások telepítése.

### <a name="before-applying-the-1906-update"></a>A 1906 alkalmazása előtt frissítése

Az Azure Stack 1906 kiadását kell alkalmazni az a következő gyorsjavításokat 1905 kiadására:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Az Azure Stack gyorsjavítás 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Frissítse a 1906 sikeres alkalmazása után

Ez a frissítés telepítése után bármely alkalmazandó gyorsjavítások telepítéséről. További információkért lásd: a [karbantartási szabályzat](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Az Azure Stack gyorsjavítás 1.1906.1.35](https://support.microsoft.com/help/4511259)

## <a name="automatic-update-notifications"></a>Az automatikus frissítési értesítések

Ügyfelek, a rendszerek, amelyek hozzáférnek az internethez, az infrastruktúra-hálózaton fogják látni a **frissítés érhető el** az operátor Portal webhelyen megjelenő. Internet-hozzáférés nélküli rendszerekhez letöltheti, és importálja a megfelelő .xml a .zip fájlt.

> [!TIP]  
> Fizessen elő a következő *RSS* vagy *Atom* tartani az Azure Stack gyorsjavítások-hírcsatornák:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Az Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivált kibocsátási megjegyzések

Látható [Azure Stack régebbi verzióinak kibocsátási megjegyzések a TechNet-katalógusban](https://aka.ms/azsarchivedrelnotes). Ezek a kibocsátási megjegyzések kizárólag tájékoztatási célt szolgálnak, és jelenti ezen verziók támogatása archivált. Azure Stack-támogatással kapcsolatos információkért lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálathoz.

## <a name="next-steps"></a>További lépések

- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  
