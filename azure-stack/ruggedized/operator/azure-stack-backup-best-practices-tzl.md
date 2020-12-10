---
title: Infrastructure Backup szolgáltatás ajánlott eljárásai a Azure Stackhoz | Microsoft Docs
description: A következő ajánlott eljárásokat követve telepítheti és felügyelheti Azure Stack az adatvesztés enyhítése érdekében, ha végzetes hiba történt.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: 7b899ab7f0916d34a76f64dd6b89c9d45e2a9d5a
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939856"
---
# <a name="infrastructure-backup-service-best-practices"></a>Infrastructure Backup Service – ajánlott eljárások

*A következőkre vonatkozik: moduláris adatközpont, Azure Stack hub robusztus*

Rendszeresen tekintse át ezeket az ajánlott eljárásokat annak ellenőrzéséhez, hogy a telepítés még mindig megfelel-e a műveleti folyamat változásainak. Ha problémák merülnek fel az ajánlott eljárások megvalósítása során, lépjen kapcsolatba Microsoft ügyfélszolgálata segítségért.

## <a name="configuration-best-practices"></a>Ajánlott eljárások a konfigurációhoz

Az infrastruktúra biztonsági mentése alapértelmezés szerint engedélyezve van egy új rendszer telepítése során, és belsőleg tárolja őket. A Azure Stack portál vagy a PowerShell használatával külső tárolási helyet is megadhat a biztonsági másolatok másodlagos helyre való exportálásához.

### <a name="networking"></a>Hálózat

Az elérési úthoz az univerzális elnevezési konvenció (UNC) karakterláncának teljes tartománynevet (FQDN) kell használnia. Az IP-cím akkor használható, ha a névfeloldás nem \' lehetséges. Az UNC-karakterlánc megadja az erőforrások, például a megosztott fájlok vagy eszközök helyét.

### <a name="encryption"></a>Titkosítás

A titkosítási tanúsítvány a külső tárolóba exportált biztonsági másolatok titkosítására szolgál. A tanúsítvány lehet önaláírt tanúsítvány, mivel a tanúsítvány csak a kulcsok továbbítására szolgál. A tanúsítványok létrehozásával kapcsolatos további információkért tekintse meg a New-SelfSignedCertificate.

A tanúsítványt biztonságos helyen kell tárolni. A tanúsítvány CER formátuma kizárólag az adattitkosításra szolgál, és nem használható a kommunikáció létrehozásához.

## <a name="operational-best-practices"></a>Ajánlott üzemeltetési eljárások

### <a name="backups"></a>Biztonsági másolatok

- A biztonsági mentési feladatok a rendszer futása közben futnak, így \' nincs leállás a kezelési élményekhez vagy a felhasználói alkalmazásokhoz. Várhatóan a biztonsági mentési feladatok 20-40 percet vesznek igénybe egy \' ésszerű terhelésű megoldás esetében.

- A hálózati kapcsolók és a hardveres életciklus-gazdagép (HLH) manuális biztonsági mentésére vonatkozó további utasítások.

### <a name="folder-names"></a>Mappák nevei

- Az infrastruktúra automatikusan hozza létre a MASBACKUP mappát. Ez egy Microsoft által felügyelt megosztás. A megosztásokat a MASBACKUP azonos szinten is létrehozhatja. \'Nem ajánlott olyan mappák vagy tárolási adattárolók létrehozása a MASBACKUP belül, amelyek Azure stack nem \' hozható létre.

- A felhasználói FQDN és a mappa neve a különböző felhőkből származó biztonsági mentési adatok megkülönböztetéséhez. A Azure Stack központi telepítésének és végpontjának teljes tartományneve a régió paraméter és a külső tartománynév paraméter kombinációja. További információ: [Azure stack Datacenter Integration-DNS](../../operator/azure-stack-integrate-dns.md).

A biztonsági másolat megosztását például a fileserver01.contoso.com AzSBackups tárolja. Ebben a fájlmegosztásban előfordulhat, hogy a külső tartománynévvel és a régió nevét használó almappában Azure Stack központi telepítés mappája.

- Teljes tartománynév: contoso.com
- Régió: NYC

```shell
\\fileserver01.contoso.com\AzSBackups
\\fileserver01.contoso.com\AzSBackups\contoso.com
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup
```

Az a `MASBackup` mappa, ahol a Azure stack tárolja a biztonsági másolati adatkészletet. Ne használja ezt a mappát a saját adatai tárolásához. A számítógépgyártóknak nem szabad ezt a mappát használniuk a biztonsági másolatok tárolásához.

A számítógépgyártóknak javasoljuk, hogy a régió mappájában tárolja az összetevőinek biztonsági mentési adatait. A hálózati kapcsolók, a hardveres életciklus-állomás (HLH) és így tovább is tárolhatók a saját almappájában. Például:

```shell
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration
```

### <a name="monitoring"></a>Figyelés

A rendszer a következő riasztásokat támogatja:

| Riasztás                                                   | Leírás                                                                                     | Szervizelés                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| A biztonsági mentés nem sikerült, mert a fájlmegosztás kapacitása nem működik. | A fájlmegosztás nem kapacitású, és a biztonságimásolat-vezérlő nem tudja exportálni a biztonságimásolat-fájlokat a helyre. | Vegyen fel további tárolókapacitást, és próbálkozzon újra a biztonsági mentéssel. A meglévő biztonsági másolatok törlése (az elsőtől kezdve) szabadítson fel lemezterületet.                    |
| Csatlakozási problémák miatt nem sikerült a biztonsági mentés.             | A Azure Stack és a fájlmegosztás közötti hálózat problémákba ütközik.                          | Oldja meg a hálózati problémát, és próbálkozzon újra a biztonsági mentéssel.                                                                                            |
| A biztonsági mentés nem sikerült, mert hiba történt az elérési úton.                | A fájlmegosztás elérési útja nem oldható fel.                                                          | Képezze le a megosztást egy másik számítógépről, hogy a megosztás elérhető legyen. Előfordulhat, hogy frissítenie kell az elérési utat, ha már nem érvényes.       |
| A biztonsági mentés hitelesítési probléma miatt nem sikerült.               | Előfordulhat, hogy probléma van a hitelesítő adatokkal vagy a hitelesítést befolyásoló hálózati problémával.    | Képezze le a megosztást egy másik számítógépről, hogy a megosztás elérhető legyen. Előfordulhat, hogy frissítenie kell a hitelesítő adatokat, ha már nem érvényesek. |
| A biztonsági mentés általános hiba miatt nem sikerült.                    | A sikertelen kérést egy átmeneti probléma okozhatja. Próbálkozzon újra a biztonsági mentéssel.                    | Hívja a támogatási szolgálatot.                                                                                                                               |

### <a name="infrastructure-backup-service-components"></a>Infrastructure Backup szolgáltatás-összetevők

A Infrastructure Backup szolgáltatás a következő összetevőket tartalmazza:

- **Infrastructure Backup vezérlő**: a rendszer a Infrastructure Backup vezérlőt minden Azure stack felhőben hozza létre és tárolja.

- **Biztonsági mentési erőforrás-szolgáltató**: a biztonsági mentési erőforrás-szolgáltató (Backup RP) a felhasználói felületből és az API-k alapszintű biztonsági mentési funkcióit kikerülő, a Azure stack-infrastruktúrára vonatkozó

### <a name="infrastructure-backup-controller"></a>Infrastructure Backup vezérlő

A Infrastructure Backup vezérlő egy Service Fabric-szolgáltatás, amely egy Azure Stack-felhő példányaihoz lesz létrehozva. A biztonsági mentési erőforrások regionális szinten jönnek létre, és az Active Directory, a CA, a Azure Resource Manager, a CRP, a szervizcsomag, az NRP, az Key Vault és a RBAC régióra jellemző szolgáltatási adatait rögzítik.

### <a name="backup-resource-provider"></a>Biztonsági mentési erőforrás-szolgáltató

A biztonsági mentési erőforrás-szolgáltató felhasználói felületet biztosít a Azure Stack portálon a biztonsági mentési erőforrások alapszintű konfigurálásához és listázásához. A kezelők a következő műveleteket végezhetik el a felhasználói felületen:

- A biztonsági mentés első alkalommal történő engedélyezése külső tárolási hely, hitelesítő adatok és titkosítási kulcs biztosításával.
- A befejezett létrehozott biztonsági mentési erőforrások és az állapot-erőforrások megtekintése a létrehozás alatt.
- Módosítsa a tárolási helyet, ahol a biztonságimásolat-vezérlő helyre helyezi a biztonsági mentési adatforrásokat.
- Módosítsa azokat a hitelesítő adatokat, amelyeket a Backup vezérlő használ a külső tároló helyének eléréséhez.
- Módosítsa a biztonságimásolat-vezérlő által a biztonsági másolatok titkosításához használt titkosítási tanúsítványt.

## <a name="backup-controller-requirements"></a>A biztonsági mentési vezérlőre vonatkozó követelmények

Ez a szakasz a Infrastructure Backup szolgáltatással kapcsolatos fontos követelményeket ismerteti. Javasoljuk, hogy figyelmesen tekintse át az adatokat, mielőtt engedélyezi a Azure Stack példány biztonsági mentését, majd az üzembe helyezés és az azt követő művelet során szükség szerint visszatekinti azt.

A követelmények a következők:

- A **szoftverre vonatkozó követelmények**: a támogatott tárolási helyszínek és a méretezési útmutatók ismertetése.
- **Hálózati követelmények**: a különböző tárolási helyszínekre vonatkozó hálózati követelményeket ismerteti.

### <a name="software-requirements"></a>Szoftverkövetelmények

#### <a name="supported-storage-locations"></a>Támogatott tárolási helyszínek

| Tárolási hely                                                                  | Részletek                                                                                                                                                  |
|-----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| A megbízható hálózati környezetben található tárolóeszközön tárolt SMB-fájlmegosztás. | SMB-megosztás ugyanabban az adatközpontban, ahol a Azure Stack telepítve van, vagy egy másik adatközpontban. Több Azure Stack példány is használhatja ugyanazt a fájlmegosztást. |
| SMB-fájlmegosztás az Azure-ban.                                                          | Jelenleg nem támogatott.                                                                                                                                 |
| BLOB Storage az Azure-ban.                                                            | Jelenleg nem támogatott.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Támogatott SMB-verziók

|SMB  |Verzió  |
|---------|---------|
|SMB     |   3. x      |

#### <a name="smb-encryption"></a>SMB-titkosítás

A Infrastructure Backup szolgáltatás támogatja a biztonsági mentési adat átvitelét egy külső tárolási helyre, amelyen engedélyezve van az SMB-titkosítás a kiszolgáló oldalán. Ha a kiszolgáló nem támogatja az SMB-titkosítást, vagy nincs engedélyezve a funkció, a Infrastructure Backup szolgáltatás visszatérhet a titkosítatlan adatforgalomhoz. A külső tárolóhelyre helyezett biztonsági mentési adatok mindig titkosítva maradnak, és nem függenek az SMB-titkosítástól.

#### <a name="storage-location-sizing"></a>Tárolási hely méretezése

Javasoljuk, hogy naponta legalább kétszer készítsen biztonsági másolatot, és tartsa meg a biztonsági mentések legfeljebb hét napját. Ez az alapértelmezett viselkedés, ha engedélyezi a Azure Stack az infrastruktúra biztonsági mentését.


|Környezeti skála  |A biztonsági mentés tervezett mérete  |A szükséges lemezterület teljes mennyisége  |
|---------|---------|---------|
|4-16 csomópont     |  20 GB       |  280 GB       |
<!-- TZLASDKFIX 
|ASDK     |   10 GB      |   140 GB     |
-->
### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

|Tárolási hely  |Részletek  |
|---------|---------|
|A megbízható hálózati környezetben található tárolóeszközön tárolt SMB-fájlmegosztás.     |  A 445-es port megadása kötelező, ha a Azure Stack-példány tűzfallal védett környezetben található. Infrastructure Backup vezérlő az 445-as porton keresztül kezdeményezi a kapcsolódást az SMB-fájlkiszolgálón.       |
| A fájlkiszolgáló teljes tartománynevének használatához a névnek feloldhatónak kell lennie a PEP-ből.     |         |

> [!NOTE]
> Nincs szükség bejövő port megnyitására.

### <a name="encryption-requirements"></a>Titkosítási követelmények

A Infrastructure Backup szolgáltatás nyilvános kulccsal rendelkező tanúsítványt használ (. CER) a biztonsági mentési adatai titkosításához. A tanúsítvány a kulcsok átviteléhez használatos, és nem a biztonságos hitelesített kommunikáció létrehozásához használatos. Emiatt a tanúsítvány önaláírt tanúsítvány lehet. Azure Stack nem kell ellenőriznie a tanúsítvány gyökerét vagy megbízhatóságát, ezért nincs szükség külső internet-hozzáférésre.

Az önaláírt tanúsítvány két részből áll, egyet a nyilvános kulccsal, egy pedig a titkos kulccsal:

- A biztonsági mentési adatai titkosítása: a (z) nyilvános kulccsal rendelkező tanúsítvány. CER-fájl) a biztonsági mentési adatai titkosítására szolgál.
- Biztonsági másolatok visszafejtése: a titkos kulccsal rendelkező tanúsítvány (exportálva. PFX-fájl) a biztonsági másolatok visszafejtésére szolgál.

A nyilvános kulccsal rendelkező tanúsítvány (. A CER) nem a belső titok rotációja által felügyelt. A tanúsítvány elforgatásához létre kell hoznia egy új önaláírt tanúsítványt, és frissítenie kell a biztonsági mentési beállításokat az új fájllal (. CER).

Az összes meglévő biztonsági mentés titkosítva marad a korábbi nyilvános kulcs használatával. Az új biztonsági másolatok az új nyilvános kulcsot használják.

Biztonsági okokból a titkos kulccsal () a Felhőbeli helyreállítás során használt tanúsítvány (. A PFX) Azure Stack nem őrzi meg.

## <a name="infrastructure-backup-limits"></a>Infrastructure Backup korlátok

Vegye figyelembe ezeket a korlátokat a Microsoft Azure Stack példányok tervezése, üzembe helyezése és üzemeltetése során. A következő táblázat ezeket a korlátozásokat ismerteti.

|Korlátazonosító  |Korlát  |Megjegyzések  |
|---------|---------|---------|
|Biztonsági mentés típusa     | Csak teljes        | Infrastructure Backup vezérlő csak a teljes biztonsági mentést támogatja. A növekményes biztonsági mentések nem támogatottak.        |
|Ütemezett biztonsági mentések     | Ütemezett és manuális        | A Backup vezérlő támogatja az ütemezett és igény szerinti biztonsági mentéseket.        |
|Egyidejű biztonsági mentési feladatok maximális száma      | 1        | A biztonsági mentési vezérlő példányain csak egy aktív biztonsági mentési feladatok támogatottak.        |
|Hálózati kapcsoló konfigurációja     | Nincs a hatókörben         | A rendszergazdának OEM-eszközök használatával kell biztonsági mentést készítenie a hálózati kapcsoló konfigurációjában. Tekintse meg az egyes OEM-gyártók által biztosított Azure Stack dokumentációját.        |
|Hardver életciklus-állomása     | Nincs a hatókörben         | A rendszergazdának OEM-eszközökkel kell biztonsági mentést készítenie a hardveres életciklus-gazdagépről. Tekintse meg az egyes OEM-gyártók által biztosított Azure Stack dokumentációját.        |
|Fájlmegosztás maximális száma     | 1        | A biztonsági másolatok tárolására csak egy fájlmegosztást lehet használni.        |
|Biztonsági mentési érték – erőforrás-szolgáltatók hozzáadása     | Hatókör        | Az infrastruktúra biztonsági mentése magában foglalja Event Hubs RP, IoT Hub RP, Data Box Edge RP biztonsági mentését.        |

## <a name="next-steps"></a>Következő lépések

- További információ a [Infrastructure Backup szolgáltatásról](../../operator/azure-stack-backup-reference.md).
