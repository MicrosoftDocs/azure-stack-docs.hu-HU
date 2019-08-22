---
title: Azure Stack biztonsági mentésének engedélyezése a felügyeleti portálról | Microsoft Docs
description: Engedélyezze a Infrastructure Backup szolgáltatást a felügyeleti portálon keresztül, hogy a rendszer meghibásodás esetén visszaállítsa Azure Stack.
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
ms.date: 08/21/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 08/21/2019
ms.openlocfilehash: d3ac538109f48e38f6483cd1ecae4896f1d3e635
ms.sourcegitcommit: 250689d6d09acc677bf59de76510d5d5f1c6190e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69896368"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Azure Stack biztonsági mentésének engedélyezése a felügyeleti portálról
Engedélyezze a Infrastructure Backup szolgáltatást a felügyeleti portálon keresztül, hogy a Azure Stack infrastruktúra-biztonsági másolatokat lehessen készíteni. A hardvereszközök a biztonsági másolatok használatával visszaállíthatják a környezetet a Felhőbeli helyreállítással [végzetes hiba](./azure-stack-backup-recover-data.md)esetén. A Felhőbeli helyreállítás célja annak biztosítása, hogy az operátorok és a felhasználók a helyreállítás befejezése után vissza tudják jelentkezni a portálra. A felhasználók előfizetéseit vissza kell állítani, beleértve a szerepköralapú hozzáférési engedélyeket, a szerepköröket, az eredeti csomagokat, az ajánlatokat, valamint a korábban meghatározott számítási, tárolási, hálózati kvótákat és Key Vault titkokat.

A Infrastructure Backup szolgáltatás azonban nem készít biztonsági mentést a IaaS virtuális gépekről, a hálózati konfigurációkról és a tárolási erőforrásokról, például a Storage-fiókokról, a blobokról, a táblákról és így tovább, így a felhasználók a Felhőbeli helyreállítást követően jelentkeznek be, és nem fogják látni a korábban meglévőket erőforrások. A szolgáltatás nem készít biztonsági másolatot a platformról (a szolgáltatásról (Pásti) származó erőforrásokról és az információkról. 

A rendszergazdák és a felhasználók felelősek a IaaS és a Péter-erőforrások biztonsági mentéséhez és visszaállításához az infrastruktúra biztonsági mentési folyamataitól függetlenül. A IaaS és a Péter-erőforrások biztonsági mentéséről a következő hivatkozásokon talál további információt:

- [Virtuális gépek](../user/azure-stack-manage-vm-protect.md)
- [APP SERVICE](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Biztonsági mentés engedélyezése vagy újrakonfigurálása

1. Nyissa meg a [Azure stack felügyeleti portált](azure-stack-manage-portals.md).
2. Válassza a **minden szolgáltatás**lehetőséget, majd az **Adminisztráció** kategóriában válassza az **infrastruktúra biztonsági mentése**lehetőséget. Válassza a **konfiguráció** lehetőséget az **infrastruktúra biztonsági mentése** panelen.
3. Adja meg a **biztonsági mentési tár helyének**elérési útját. Használjon egy univerzális elnevezési konvenció (UNC) karakterláncot a különálló eszközön tárolt fájlmegosztás elérési útjához. Az UNC-karakterlánc megadja az erőforrások, például a megosztott fájlok vagy eszközök helyét. A szolgáltatáshoz használhat IP-címet. A biztonsági mentési adatmennyiségnek a katasztrófa utáni rendelkezésre állásának biztosításához az eszköznek külön helyen kell lennie.

    > [!Note]  
    > Ha a környezet támogatja a névfeloldást a Azure Stack infrastruktúra-hálózatról a vállalati környezetre, az IP helyett teljes tartománynevet használhat.

4. Írja be a felhasználónevet a tartomány és a Felhasználónév használatával, amely megfelelő hozzáféréssel rendelkezik az olvasási és írási fájlokhoz. Például: `Contoso\backupshareuser`.
5. Adja meg a felhasználó **jelszavát** .
6. Írja be ismét a jelszót a **jelszó megerősítéséhez**.
7. Az **órák gyakorisága** határozza meg, hogy milyen gyakran jönnek létre a biztonsági másolatok. Az alapértelmezett érték 12. A Scheduler legfeljebb 12 és legalább 4 értéket támogat. 
8. A **megőrzési időtartam napokban** határozza meg, hogy a biztonsági másolatok hány napja maradnak meg a külső helyen. Az alapértelmezett érték 7. A Scheduler legfeljebb 14 és minimum 2 értéket támogat. A megőrzési időtartamnál régebbi biztonsági másolatokat a rendszer automatikusan törli a külső helyről.

    > [!Note]  
    > Ha a biztonsági mentéseket a megőrzési időtartamnál régebbi verzióra szeretné archiválni, mindenképpen készítsen biztonsági másolatot a fájlokról, mielőtt az ütemező törli a biztonsági mentéseket. Ha csökkenti a biztonsági másolatok megőrzési időtartamát (például 7 nap és 5 nap között), akkor az ütemező törli az új megőrzési időtartamnál régebbi összes biztonsági mentést. Az érték frissítése előtt győződjön meg arról, hogy a biztonsági mentések törlése megtörtént. 

9. A titkosítási beállítások mezőben adja meg a tanúsítványt a Certificate. cer fájlban. A biztonságimásolat-fájlok titkosítva vannak a tanúsítvány ezen nyilvános kulcsával. Olyan tanúsítványt kell megadnia, amely csak a nyilvános kulcs részét tartalmazza a biztonsági mentési beállítások konfigurálásakor. Miután először beállította a tanúsítványt, vagy a későbbiekben elforgatta a tanúsítványt, csak a tanúsítvány ujjlenyomatát tekintheti meg. A feltöltött tanúsítványfájl nem tölthető le vagy nem tekinthető meg. A tanúsítványfájl létrehozásához futtassa a következő PowerShell-parancsot egy önaláírt tanúsítvány nyilvános és titkos kulccsal való létrehozásához, valamint egy olyan tanúsítvány exportálásához, amely csak a nyilvános kulcs részét képezi. A tanúsítvány bárhol elhelyezhető, amely a felügyeleti portálról érhető el.

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
   > **1901 és újabb**verziók: Azure Stack elfogadja az infrastruktúra biztonsági mentési adatai titkosításához szükséges tanúsítványt. Ügyeljen arra, hogy a tanúsítványt egy biztonságos helyen tárolja a nyilvános és a titkos kulccsal. Biztonsági okokból nem ajánlott a tanúsítvány és a titkos kulcs használata a biztonsági mentési beállítások konfigurálásához. További információ a tanúsítvány életciklusának kezeléséről: [Infrastructure Backup szolgáltatás ajánlott eljárásai](azure-stack-backup-best-practices.md).
   > 
   > **1811 vagy korábbi**: Azure Stack elfogad egy szimmetrikus kulcsot az infrastruktúra biztonsági mentési adatai titkosításához. Kulcs létrehozásához használja a [New-AzsEncryptionKey64 parancsmagot](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). Az 1811-ról 1901-re való frissítés után a biztonsági mentési beállítások megőrzik a titkosítási kulcsot. Javasoljuk, hogy frissítse a biztonsági mentési beállításokat a tanúsítvány használatára. A titkosítási kulcs támogatása már elavult. A tanúsítvány használatához legalább 3 kiadással kell frissítenie a beállításokat. 

10. A biztonsági mentési vezérlő beállításainak mentéséhez kattintson **az OK gombra** .

![Azure Stack – a biztonsági mentési vezérlő beállításai](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>Biztonsági mentés indítása
A biztonsági mentés elindításához kattintson a **biztonsági mentés most** gombra egy igény szerinti biztonsági mentés elindításához. Az igény szerinti biztonsági mentés nem módosítja a következő ütemezett biztonsági mentés idejét. A feladat befejezése után megerősítheti az **alapvető**beállítások beállításait:

![Igény szerinti Azure Stack biztonsági mentés](media/azure-stack-backup/scheduled-backup.png)

A **Start-AzsBackup PowerShell-** parancsmagot a Azure stack felügyeleti számítógépén is futtathatja. További információ: [Azure stack biztonsági mentése](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Automatikus biztonsági mentések engedélyezése vagy letiltása
A biztonsági mentéseket a rendszer automatikusan ütemezi a biztonsági mentés engedélyezésekor. A következő ütemezett biztonsági mentés időpontját a **essentialsben**tekintheti meg. 

![Igény szerinti Azure Stack biztonsági mentés](media/azure-stack-backup/on-demand-backup.png)

Ha le kell tiltania a jövőbeli ütemezett biztonsági mentéseket, kattintson az **automatikus biztonsági mentések letiltása**lehetőségre. Az automatikus biztonsági mentések letiltása megőrzi a biztonsági mentési beállításokat, és megőrzi a biztonsági mentés ütemezését. Ez a művelet egyszerűen azt jelzi, hogy a Feladatütemező kihagyja a jövőbeli biztonsági mentéseket. 

![Azure Stack – ütemezett biztonsági mentések letiltása](media/azure-stack-backup/disable-auto-backup.png)

Győződjön meg arról, hogy a jövőbeli ütemezett biztonságimentések le vannak tiltva az essentialsben:

![Azure Stack – a biztonsági másolatok megerősítése le lettek tiltva](media/azure-stack-backup/confirm-disable.png)

Az **automatikus biztonsági mentések engedélyezése** lehetőségre kattintva értesítheti a Feladatütemezőt a jövőbeli biztonsági mentések ütemezett időpontban történő elindításáról. 

![Azure Stack – ütemezett biztonsági mentések engedélyezése](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Ha a 1807-es frissítés előtt konfigurálta az infrastruktúra biztonsági mentését, az automatikus biztonsági mentések le lesznek tiltva. Így a Azure Stack által elindított biztonsági másolatok nem ütköznek a külső feladatütemezés által indított biztonsági mentésekkel. Ha letiltotta bármelyik külső Feladatütemezőt, kattintson az **automatikus biztonsági mentések engedélyezése**lehetőségre.

## <a name="update-backup-settings"></a>Biztonsági mentési beállítások frissítése
A 1901-as és a titkosítási kulcs támogatása elavult. Ha a biztonsági mentést az 1901-as első alkalommal konfigurálja, akkor tanúsítványt kell használnia. Azure Stack csak akkor támogatja a titkosítási kulcsot, ha a kulcs konfigurálva van a 1901-es frissítés előtt. A visszafelé kompatibilitási mód három kiadás esetén folytatódik. Ezt követően a titkosítási kulcsok többé nem lesznek támogatottak. 

### <a name="default-mode"></a>Alapértelmezett mód
Ha a titkosítási beállításokban az infrastruktúra biztonsági mentését az 1901-es verzió telepítése vagy frissítése után első alkalommal konfigurálja, akkor a biztonsági mentést egy tanúsítvánnyal kell konfigurálnia. A titkosítási kulcs használata már nem támogatott. 

A biztonsági mentési adatok titkosításához használt tanúsítvány frissítéséhez feltölthet egy újat. A nyilvános kulcs részét képező CER-fájl, majd a beállítások mentéséhez kattintson az OK gombra. 

Az új biztonsági mentések a nyilvános kulcsot fogják használni az új tanúsítványban. Az előző tanúsítvánnyal létrehozott összes biztonsági mentésre nincs hatással. Ügyeljen arra, hogy a régebbi tanúsítvány biztonságos helyen maradjon, ha szüksége van rá a Felhőbeli helyreállításhoz.

![Azure Stack – tanúsítvány ujjlenyomatának megtekintése](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Visszamenőleges kompatibilitási mód
Ha a biztonsági mentést a 1901-es verzióra való frissítés előtt konfigurálta, a beállítások változása nem változik. Ebben az esetben a titkosítási kulcs támogatja a visszamenőleges kompatibilitást. Frissítheti a titkosítási kulcsot, vagy átválthat egy tanúsítvány használatára. A titkosítási kulcs frissítésének folytatásához legalább három kiadásra lesz szüksége. Ezt az időt használhatja a tanúsítványra való áttéréshez. Új titkosítási kulcs létrehozásához használja a [New-AzsEncryptionKeyBase64](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64).

![Azure Stack – a titkosítási kulcs visszamenőleges kompatibilitási módban való használata](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> A titkosítási kulcsról a tanúsítványra történő frissítés egy egyirányú művelet. A módosítást követően nem válthat vissza a titkosítási kulcsra. Az összes meglévő biztonsági mentés titkosítva marad a korábbi titkosítási kulccsal. 

![Azure Stack – titkosítási tanúsítvány használata visszafelé kompatibilitási módban](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>További lépések

Útmutató a biztonsági másolatok futtatásához. Lásd: [Azure stack biztonsági mentése](azure-stack-backup-back-up-azure-stack.md)

Útmutató a biztonsági mentés futtatásának ellenőrzéséhez. Lásd: [a biztonsági mentés megerősítése befejeződött az adminisztrációs portálon](azure-stack-backup-back-up-azure-stack.md)
