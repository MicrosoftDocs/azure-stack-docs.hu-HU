---
title: Biztonsági mentés engedélyezése az Azure Stack a felügyeleti portálról |} A Microsoft Docs
description: Engedélyezze az infrastruktúra Backup szolgáltatás a felügyeleti portálon keresztül, úgy, hogy az Azure Stack állíthatók, ha hiba történik.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: eefd393fa12814260711590f028c9a787811d8af
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269030"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Biztonsági mentés engedélyezése az Azure Stack a felügyeleti portálról
Engedélyezze az infrastruktúra Backup szolgáltatás a felügyeleti portálon keresztül, úgy, hogy az Azure Stack infrastruktúrájának biztonsági mentéseket hozhat létre. A hardver partner ezeket a biztonsági másolatokat segítségével visszaállíthatja az-környezet a felhőbeli helyreállítási [végzetes hiba](./azure-stack-backup-recover-data.md). A felhőbeli helyreállítási célja, hogy győződjön meg arról, hogy az operátorok és a felhasználók is jelentkezzen be újra a portál recovery befejeződése után. Felhasználók visszaállítása, beleértve a szerepköralapú hozzáférési engedélyek és szerepkörök, eredeti csomagok, ajánlatok, és a korábban meghatározott számítási, tárolási, hálózati kvóták, és a Key Vault titkos megszűnni lesz.

Azonban az infrastruktúra biztonsági mentési szolgáltatás nem IaaS virtuális gépek biztonsági mentése, hálózati konfigurációját, és tárolási erőforrások, például a storage-fiókok, blobok, táblák, és így tovább, így felhőalapú helyreállítás befejezése után bejelentkezett felhasználók nem látják bármelyik korábban meglévő az erőforrásokat. Platform (PaaS) szolgáltatás-erőforrások és adatok is nem készül biztonsági másolat a szolgáltatás által. 

A rendszergazdák és felhasználók felelőssége biztonsági mentése és visszaállítása IaaS és PaaS-erőforrásokat külön-külön, a biztonsági mentési infrastruktúra-folyamatok. IaaS és PaaS-erőforrások biztonsági mentésével kapcsolatos további információkért lásd az alábbi hivatkozásokat:

- [Virtuális gépek](../user/azure-stack-manage-vm-protect.md)
- [APP SERVICE](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Engedélyezi, vagy konfigurálja újra a biztonsági mentés

1. Nyissa meg a [Azure Stack felügyeleti portálon](azure-stack-manage-portals.md).
2. Válassza ki **minden szolgáltatás**, majd a **felügyeleti** kategória kiválasztása **infrastruktúra biztonsági mentését**. Válasszon **konfigurációs** a a **infrastruktúra biztonsági mentését** panelen.
3. Írja be a elérési útját a **biztonsági mentési tárhelyet**. Egy univerzális elnevezési konvenció (UNC) karakterlánc használata egy különálló eszköz található fájlmegosztás elérési útját. Karakterláncnak UNC helyét adja meg az erőforrások, például megosztott fájlokhoz vagy eszközökön. A szolgáltatás IP-címet is használhatja. A biztonsági mentési adatok rendelkezésre állásának biztosításához egy vészhelyzetet követően, hogy az eszköz egy külön helyen kell lennie.

    > [!Note]  
    > Ha a környezet támogatja a vállalati környezetben az Azure Stack infrastruktúra-hálózaton a névfeloldást, használhatja a IP-cím helyett a teljes Tartománynevet.

4. Írja be a **felhasználónév** segítségével tartomány és felhasználónév megfelelő szintű hozzáféréssel rendelkező fájlok olvasását és írását. Például: `Contoso\backupshareuser`.
5. Írja be a **jelszó** a felhasználó számára.
6. Írja be újra a jelszót **jelszó megerősítése**.
7. A **gyakorisága (óra)** határozza meg, hogy milyen gyakran jönnek létre biztonsági mentéseket. Az alapértelmezett érték: 12. A Scheduler támogatja a legfeljebb 12 és a egy legalább 4. 
8. A **megőrzési időszak napban** határozza meg, hány napig a biztonsági mentések megmaradnak a külső helyen. Az alapértelmezett értéke a 7. A Scheduler támogatja a legfeljebb 14 és a egy legalább 2. Biztonsági másolatok régebbi, mint a megőrzési időszak a külső helyről automatikusan törlődnek.

    > [!Note]  
    > Ha a megőrzési időszak régebbi biztonsági másolatok archiválni kívánja, ügyeljen arra, hogy a fájlok biztonsági mentését, mielőtt az ütemező törli a biztonsági mentéseket. Ha csökkenti a biztonsági másolat megőrzési idejének (pl. az 5 napos 7 napig), az ütemező törli a régebbi, mint az új megőrzési időszaknál minden biztonsági mentés. Ellenőrizze, hogy ok a a biztonsági mentések törlődik, ez az érték frissítése előtt. 

9. Adjon meg egy tanúsítványt a tanúsítvány .cer fájl mezőbe a titkosítási beállítások. Biztonságimásolat-fájlokat a nyilvános kulcs az a tanúsítvány vannak titkosítva. Egy tanúsítványt, amely csak a biztonsági mentési beállításainak konfigurálásakor tartalmazza a nyilvános kulcs részét kell biztosítania. Állítsa be ezt a tanúsítványt először, vagy a jövőben elforgatása a tanúsítványt, ha csak megtekinteni lehet a tanúsítvány ujjlenyomatát. Nem töltse le és a feltöltött tanúsítvány megtekintése. A tanúsítványfájl létrehozásához futtassa a következő PowerShell-paranccsal önaláírt tanúsítvány létrehozása a nyilvános és titkos kulcsokat a, és csak a nyilvános kulcs rész tanúsítvány exportálása.

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

   > [!Note]
   > **1901 és a fenti**: Az Azure Stack infrastruktúrájának biztonsági mentési adatok titkosításához tanúsítvány fogad el. Ellenőrizze, hogy a tanúsítványt tárolja biztonságos helyen a nyilvános és titkos kulccsal. Biztonsági okokból nem ajánlott, hogy a tanúsítványt használ a nyilvános és titkos kulcsok biztonsági mentési beállítások konfigurálása. Ez a tanúsítvány életciklusának kezeléséről további információkért lásd: [infrastruktúra biztonsági mentési szolgáltatás ajánlott eljárások](azure-stack-backup-best-practices.md).
   > 
   > **1811 vagy korábbi**: Az Azure Stack infrastruktúra az adatok biztonsági másolatának titkosításához szimmetrikus kulcs fogad el. Használja a [New-AzsEncryptionKey64 parancsmaggal hozzon létre egy kulcsot](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). Amikor frissít a 1811 1901, biztonsági mentési beállítások megőrzik a titkosítási kulcs. Ajánljuk, hogy a tanúsítvány használata a biztonsági mentési beállításainak frissítése. Titkosítási kulcs támogatása elavult. Tanúsítvány-beállítások frissítése legalább 3 kiadások kell. 

10. Válassza ki **OK** a biztonsági mentés vezérlő beállítások mentéséhez.

![Az Azure Stack - biztonsági mentést vezérlő beállítások](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>Biztonsági mentés indítása
A biztonsági mentés indításához kattintson a **biztonsági mentés** egy igény szerinti biztonsági mentés elindításához. Egy igény szerinti biztonsági mentés nem módosítják a következő ütemezett biztonsági mentés időpontját. A feladat befejezése után ellenőrizheti a beállítások **Essentials**:

![Az Azure Stack - igény szerinti biztonsági mentés](media/azure-stack-backup/scheduled-backup.png)

A PowerShell-parancsmagot is futtathatja **Start-AzsBackup** az Azure Stack felügyeleti számítógépen. További információkért lásd: [biztonsági mentése az Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Engedélyezi vagy letiltja az automatikus biztonsági mentés
Biztonsági mentés automatikusan van ütemezve, amikor a biztonsági mentés engedélyezése. A következő biztonsági mentési ütemezés alkalommal ellenőrizheti **Essentials**. 

![Az Azure Stack - igény szerinti biztonsági mentés](media/azure-stack-backup/on-demand-backup.png)

Ha a jövőbeli ütemezett biztonsági mentések tiltása van szüksége, kattintson a **automatikus biztonsági mentések tiltása**. Letiltását automatikus biztonsági mentést fogja megőrizni a biztonsági mentési beállítások konfigurálása, és megőrzi a biztonsági mentés ütemezése. Ez a művelet egyszerűen arra utasítja a jövőbeli biztonsági mentések kihagyja az ütemezőnek. 

![Az Azure Stack - letiltása az ütemezett biztonsági mentések](media/azure-stack-backup/disable-auto-backup.png)

Győződjön meg arról, hogy jövőbeli ütemezett biztonsági mentések le vannak tiltva a **Essentials**:

![Az Azure Stack - erősítse meg a biztonsági mentések le vannak tiltva.](media/azure-stack-backup/confirm-disable.png)

Kattintson a **engedélyezze az automatikus biztonsági mentés** tájékoztatja az ütemező jövőbeli biztonsági mentések elindítani az ütemezett időpontban. 

![Az Azure Stack - enable ütemezett biztonsági mentések](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Ha konfigurálta az infrastruktúra biztonsági mentését 1807 frissítése előtt, az automatikus biztonsági mentés le lesz tiltva. Ezzel a módszerrel a lépések: Azure Stack biztonsági mentések nem ütköznek a biztonsági mentések indítja el külső Feladatütemező motor. Ha letiltja a bármely külső Feladatütemezőt, kattintson a **engedélyezze az automatikus biztonsági mentés**.

## <a name="update-backup-settings"></a>Biztonsági mentés beállításainak frissítése
Kezdődően 1901 a titkosítási kulcs elavult támogatása. Biztonsági mentés 1901 első alkalommal állítja be, ha egy tanúsítványt kell használnia. Az Azure Stack támogatja a titkosítási kulcs csak akkor, ha a kulcs 1901 frissítése előtt van konfigurálva. Előző verziókkal való kompatibilitási módban a három kiadásokhoz továbbra is. Ezt követően titkosítási kulcsok rendszer már nem támogatott. 

### <a name="default-mode"></a>Alapértelmezett mód
A titkosítási beállítások konfigurálásakor infrastruktúra biztonsági mentése első alkalommal után telepítése vagy frissítése az 1901, konfigurálnia kell biztonsági mentést tanúsítvánnyal. Titkosítási kulcs használata már nem támogatott. 

Az adatok biztonsági másolatának titkosításához használt tanúsítvány frissítéséhez tölthet fel egy új. CER-fájlt a nyilvános kulcs része, és válassza az OK gombra a beállítások mentéséhez. 

Új biztonsági másolatok megkezdi az új tanúsítvány található nyilvános kulcs használatát. Ez nincs hatással az előző tanúsítvány használatával létrehozott összes meglévő biztonsági mentést. Ellenőrizze, hogy a régi tanúsítvány körül tartsa biztonságos helyen, abban az esetben szüksége lesz rá a felhőbeli.

![Az Azure Stack - nézet tanúsítvány ujjlenyomata](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Visszamenőleges kompatibilitási mód
Ha konfigurálta a biztonsági mentés 1901 frissítése előtt, a beállítások átkerülnek a a működésében nincs változás. Ebben az esetben titkosítási kulcs támogatott a visszamenőleges kompatibilitás. Lehetősége van a titkosítási kulcs frissítése vagy váltás tanúsítvány használatára. A titkosítási kulcs frissítésének folytatásához legalább három kiadások kell. Hogy való áttérés egy tanúsítványt használja. Hozhat létre egy új titkosítási kulcs használatát a [New-AzsEncryptionKeyBase64 parancsmag](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64).

![Az Azure Stack - titkosítási kulcs az előző verziókkal való kompatibilitási módban](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> A titkosítási kulcs frissítése a tanúsítvány egy egyirányú művelet. A módosítás elvégzése után nem válthat vissza a titkosítási kulcs. Az összes meglévő biztonsági másolatok a korábbi titkosítási kulccsal titkosított marad. 

![Az Azure Stack - titkosítási tanúsítvány használata az előző verziókkal való kompatibilitási módban](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>További lépések

Ismerje meg a biztonsági mentés futtatása. Lásd: [biztonsági mentése az Azure Stackben](azure-stack-backup-back-up-azure-stack.md)

Ismerje meg, annak ellenőrzéséhez, hogy futtatta-e a biztonsági mentés. Lásd: [megerősítése a biztonsági mentés a felügyeleti portál](azure-stack-backup-back-up-azure-stack.md)
