---
title: Azure Stack hub biztonsági mentésének engedélyezése a felügyeleti portálról
description: Megtudhatja, hogyan engedélyezheti a Infrastructure Backup szolgáltatást a felügyeleti portálról, hogy a Azure Stack hub visszaállítható, ha hiba történt.
author: PatAltimore
ms.topic: article
ms.date: 08/21/2019
ms.author: patricka
ms.reviewer: hectorl
ms.lastreviewed: 08/21/2019
ms.openlocfilehash: 4ec0aebf0fcf46973a4f371d659aece8e51eb2c7
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871854"
---
# <a name="enable-backup-for-azure-stack-hub-from-the-administrator-portal"></a>Biztonsági mentés engedélyezése Azure Stack hub számára a felügyeleti portálról

A Infrastructure Backup szolgáltatást a felügyeleti portálon engedélyezheti, hogy Azure Stack hub képes legyen infrastruktúra-biztonsági mentést készíteni. A hardvereszközök a biztonsági másolatok használatával visszaállíthatják a környezetet a Felhőbeli helyreállítással [végzetes hiba](./azure-stack-backup-recover-data.md)esetén. A Felhőbeli helyreállítás célja annak biztosítása, hogy az operátorok és a felhasználók a helyreállítás befejezése után vissza tudják jelentkezni a portálra. A felhasználók előfizetését vissza kell állítani, beleértve a következőket:

- Szerepköralapú hozzáférési engedélyek és szerepkörök.
- Eredeti csomagok és ajánlatok.
- Korábban definiált számítási, tárolási és hálózati kvóták.
- Key Vault titkokat.

A Infrastructure Backup szolgáltatás azonban nem készít biztonsági mentést a IaaS virtuális gépekről, a hálózati konfigurációkról és a tárolási erőforrásokról, például a Storage-fiókokról, a blobokról, a táblákról stb. A felhőalapú helyreállítást követően bejelentkezett felhasználók nem látják a korábban meglévő erőforrások egyikét sem. A szolgáltatás nem készít biztonsági másolatot a platformról (a szolgáltatásról (Pásti) származó erőforrásokról és az információkról.

A rendszergazdák és a felhasználók felelősek a IaaS és a Péter-erőforrások biztonsági mentéséhez és visszaállításához az infrastruktúra biztonsági mentési folyamataitól függetlenül. A IaaS és a Péter-erőforrások biztonsági mentésével kapcsolatos információkért tekintse meg az alábbi hivatkozásokat:

- [Azure Stack hub-on üzembe helyezett virtuális gépek elleni védelem](../user/azure-stack-manage-vm-protect.md)
- [Adatok biztonsági mentése az Azure-ban](/azure/app-service/manage-backup)
- [Mi az Azure-beli virtuális gépek SQL Server? Windows](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Biztonsági mentés engedélyezése vagy újrakonfigurálása

1. Nyissa meg az [Azure stack hub felügyeleti portált](azure-stack-manage-portals.md).
2. Válassza a **minden szolgáltatás** lehetőséget, majd az **Adminisztráció** kategóriában válassza az **infrastruktúra biztonsági mentése** lehetőséget. Válassza a **konfiguráció** lehetőséget az **infrastruktúra biztonsági mentése** panelen.
3. Adja meg a **biztonsági mentési tár helyének** elérési útját. Használjon egy univerzális elnevezési konvenció (UNC) karakterláncot a különálló eszközön tárolt fájlmegosztás elérési útjához. Az UNC-karakterlánc megadja az erőforrások, például a megosztott fájlok vagy eszközök helyét. A szolgáltatáshoz használhat IP-címet. A biztonsági mentési adatmennyiségnek a katasztrófa utáni rendelkezésre állásának biztosításához az eszköznek külön helyen kell lennie.

    > [!Note]  
    > Ha a környezet támogatja a névfeloldást az Azure Stack hub infrastruktúra-hálózatról a vállalati környezetre, az IP helyett teljes tartománynevet (FQDN) is használhat.

4. Írja be a **felhasználónevet** a tartomány és a Felhasználónév használatával, amely megfelelő hozzáféréssel rendelkezik az olvasási és írási fájlokhoz. Például: `Contoso\backupshareuser`.
5. Adja meg a felhasználó **jelszavát** .
6. Írja be ismét a jelszót a **jelszó megerősítéséhez**.
7. Az **órák gyakorisága** határozza meg, hogy milyen gyakran jönnek létre a biztonsági másolatok. Az alapértelmezett érték 12. A Scheduler legfeljebb 12 és legalább 4 értéket támogat. 
8. A **megőrzési időtartam napokban** határozza meg, hogy a biztonsági másolatok hány napja maradnak meg a külső helyen. Az alapértelmezett érték 7. A Scheduler legfeljebb 14 és minimum 2 értéket támogat. A megőrzési időtartamnál régebbi biztonsági másolatokat a rendszer automatikusan törli a külső helyről.

    > [!Note]  
    > Ha a biztonsági mentéseket a megőrzési időtartamnál régebbi verzióra szeretné archiválni, mindenképpen készítsen biztonsági másolatot a fájlokról, mielőtt az ütemező törli a biztonsági mentéseket. Ha csökkenti a biztonsági másolatok megőrzési időtartamát (például 7 nap és 5 nap között), akkor az ütemező törli az új megőrzési időtartamnál régebbi összes biztonsági mentést. Az érték frissítése előtt győződjön meg arról, hogy az OK gombra kattintott a biztonsági másolatok törléséhez.

9. A titkosítási beállítások területen adjon meg egy tanúsítványt a Certificate. cer fájl mezőben. A biztonságimásolat-fájlok titkosítva vannak a tanúsítvány ezen nyilvános kulcsával. Olyan tanúsítványt adjon meg, amely csak a nyilvános kulcs részét tartalmazza a biztonsági mentési beállítások konfigurálásakor. Miután először beállította a tanúsítványt, vagy a későbbiekben elforgatta a tanúsítványt, csak a tanúsítvány ujjlenyomatát tekintheti meg. A feltöltött tanúsítványfájl nem tölthető le vagy nem tekinthető meg. A tanúsítványfájl létrehozásához futtassa a következő PowerShell-parancsot egy önaláírt tanúsítvány nyilvános és titkos kulccsal való létrehozásához, valamint egy olyan tanúsítvány exportálásához, amely csak a nyilvános kulcs részét képezi. A tanúsítvány bármikor menthető, amely a felügyeleti portálról érhető el.

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
   > **1901 és újabb** verzió: Azure stack hub elfogad egy tanúsítványt az infrastruktúra biztonsági mentési adatai titkosításához. Ügyeljen arra, hogy a tanúsítványt egy biztonságos helyen tárolja a nyilvános és a titkos kulccsal. Biztonsági okokból nem ajánlott a tanúsítvány és a titkos kulcs használata a biztonsági mentési beállítások konfigurálásához. További információ a tanúsítvány életciklusának kezeléséről: [Infrastructure Backup szolgáltatás ajánlott eljárásai](azure-stack-backup-best-practices.md).
   > 
   > **1811 vagy korábbi**: Azure stack hub elfogad egy szimmetrikus kulcsot az infrastruktúra biztonsági mentési adatai titkosításához. [Kulcs létrehozásához használja a New-AzsEncryptionKey64 parancsmagot](/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). Az 1811-ról 1901-re való frissítés után a biztonsági mentési beállítások megőrzik a titkosítási kulcsot. Javasoljuk, hogy frissítse a biztonsági mentési beállításokat a tanúsítvány használatára. A titkosítási kulcs támogatása már elavult. Legalább 3 kiadással frissíti a beállításokat a tanúsítvány használatára.

10. A biztonsági mentési vezérlő beállításainak mentéséhez kattintson **az OK gombra** .

![Azure Stack hub – biztonsági mentési vezérlő beállításai](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>Biztonsági mentés indítása
A biztonsági mentés elindításához kattintson a **biztonsági mentés most** gombra egy igény szerinti biztonsági mentés elindításához. Az igény szerinti biztonsági mentés nem módosítja a következő ütemezett biztonsági mentés idejét. A feladat befejezése után megerősítheti az **alapvető** beállítások beállításait:

![Képernyőkép, amely bemutatja, hogyan indíthat el egy igény szerinti biztonsági mentést.](media/azure-stack-backup/scheduled-backup.png)

A **Start-AzsBackup PowerShell-** parancsmagot a Azure stack hub felügyeleti számítógépén is futtathatja. További információ: [Azure stack hub biztonsági mentése](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Automatikus biztonsági mentések engedélyezése vagy letiltása
A biztonsági mentéseket a rendszer automatikusan ütemezi a biztonsági mentés engedélyezésekor. A következő ütemezett biztonsági mentés időpontját a **essentialsben** tekintheti meg. 

![Igény szerinti Azure Stack-alapú biztonsági mentés](media/azure-stack-backup/on-demand-backup.png)

Ha le kell tiltania a jövőbeli ütemezett biztonsági mentéseket, kattintson az **automatikus biztonsági mentések letiltása** lehetőségre. Az automatikus biztonsági mentések letiltása megőrzi a biztonsági mentési beállításokat, és megőrzi a biztonsági mentés ütemezését. Ez a művelet egyszerűen azt jelzi, hogy a Feladatütemező kihagyja a jövőbeli biztonsági mentéseket.

![Azure Stack hub – ütemezett biztonsági mentések letiltása](media/azure-stack-backup/disable-auto-backup.png)

Győződjön meg arról, hogy a jövőbeli ütemezett biztonsági mentések le vannak tiltva az **essentialsben**:

![Azure Stack hub – biztonsági másolatok megerősítése le lettek tiltva](media/azure-stack-backup/confirm-disable.png)

Az **automatikus biztonsági mentések engedélyezése** lehetőségre kattintva értesítheti a Feladatütemezőt a jövőbeli biztonsági mentések ütemezett időpontban történő elindításáról. 

![Azure Stack hub – ütemezett biztonsági mentések engedélyezése](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Ha a 1807-es frissítés előtt konfigurálta az infrastruktúra biztonsági mentését, az automatikus biztonsági mentések le lesznek tiltva. Így a Azure Stack hub által indított biztonsági másolatok nem ütköznek a külső feladatütemezés által indított biztonsági mentésekkel. Ha letiltotta bármelyik külső Feladatütemezőt, kattintson az **automatikus biztonsági mentések engedélyezése** lehetőségre.

## <a name="update-backup-settings"></a>Biztonsági mentési beállítások frissítése
A 1901-as és a titkosítási kulcs támogatása elavult. Ha a biztonsági mentést az 1901-as első alkalommal konfigurálja, akkor tanúsítványt kell használnia. Azure Stack hub csak akkor támogatja a titkosítási kulcsot, ha a kulcs konfigurálva van a 1901-es frissítés előtt. A visszafelé kompatibilitási mód három kiadás esetén folytatódik. Ezt követően a titkosítási kulcsok többé nem lesznek támogatottak.

### <a name="default-mode"></a>Alapértelmezett mód
Ha a titkosítási beállításokban az infrastruktúra biztonsági mentését az 1901-es verzió telepítése vagy frissítése után első alkalommal konfigurálja, akkor a biztonsági mentést egy tanúsítvánnyal kell konfigurálnia. A titkosítási kulcs használata már nem támogatott.

A biztonsági mentési adatok titkosításához használt tanúsítvány frissítéséhez töltsön fel egy újat. A nyilvános kulcs részét képező CER-fájl, majd a beállítások mentéséhez kattintson az OK gombra.

Az új biztonsági mentések a nyilvános kulcsot fogják használni az új tanúsítványban. Az előző tanúsítvánnyal létrehozott összes meglévő biztonsági mentésre nincs hatással. Ügyeljen arra, hogy a régebbi tanúsítvány biztonságos helyen maradjon, ha szüksége van rá a Felhőbeli helyreállításhoz.

![Azure Stack hub – tanúsítvány ujjlenyomatának megtekintése](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Visszamenőleges kompatibilitási mód
Ha a biztonsági mentést a 1901-es verzióra való frissítés előtt konfigurálta, a beállítások változása nem változik. Ebben az esetben a titkosítási kulcs támogatja a visszamenőleges kompatibilitást. Frissítheti a titkosítási kulcsot, vagy átválthat egy tanúsítvány használatára. Legalább három kiadása van a titkosítási kulcs frissítésének folytatásához. Ezt az időt használhatja a tanúsítványra való áttéréshez. Új titkosítási kulcs létrehozásához használja a [New-AzsEncryptionKeyBase64](/powershell/module/azs.backup.admin/new-azsencryptionkeybase64).

![Azure Stack hub – titkosítási kulcs használata visszafelé kompatibilitási módban](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> A titkosítási kulcsról a tanúsítványra történő frissítés egy egyirányú művelet. A módosítást követően nem válthat vissza a titkosítási kulcsra. Az összes meglévő biztonsági mentés titkosítva marad a korábbi titkosítási kulccsal.

![Azure Stack hub – titkosítási tanúsítvány használata visszamenőleges kompatibilitási módban](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>További lépések

Útmutató a biztonsági másolatok futtatásához. Lásd: [Azure stack hub biztonsági mentése](azure-stack-backup-back-up-azure-stack.md).

Útmutató a biztonsági mentés futtatásának ellenőrzéséhez. Lásd: [a biztonsági mentés megerősítése befejeződött a felügyeleti portálon](azure-stack-backup-back-up-azure-stack.md).
