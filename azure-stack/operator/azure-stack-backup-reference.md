---
title: Infrastructure Backup szolgáltatás leírása
description: Az Azure Stack hub Infrastructure Backup szolgáltatásához tartozó anyagminta.
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: cdbe5150b72a720fa527d3bb1b1e32f5a66a6955
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703604"
---
# <a name="infrastructure-backup-service-reference"></a>Infrastructure Backup szolgáltatás leírása

## <a name="azure-backup-infrastructure"></a>Azure Backup-infrastruktúra

Azure Stack hub számos olyan szolgáltatást tartalmaz, amelyek a portált (Azure Resource Manager) és a teljes infrastruktúra-kezelési élményt alkotják. Azure Stack hub alkalmazások közötti felügyeleti tapasztalata a megoldás kezelője számára elérhető bonyolultság csökkentését összpontosítja.

A Infrastructure Backup szolgáltatás úgy lett kialakítva, hogy az infrastruktúra-szolgáltatások biztonsági mentésének és visszaállításának összetettségét kihasználja, így a kezelők a megoldás felügyeletére és a felhasználókra vonatkozó SLA-k fenntartására összpontosítanak.

A biztonsági mentések külső megosztásra való exportálásának elkerüléséhez a biztonsági másolatok ugyanazon a rendszeren való tárolása szükséges. A külső megosztás megkövetelése lehetővé teszi a rendszergazda számára, hogy megtudja, hol tárolja az adattárolást a meglévő BC/DR-szabályzatok alapján.

### <a name="infrastructure-backup-service-components"></a>Infrastructure Backup szolgáltatás-összetevők

Infrastructure Backup szolgáltatás a következő összetevőket tartalmazza:

 - **Infrastructure Backup vezérlő**  
 A Infrastructure Backup vezérlő a-ben és a-ben található minden Azure Stack hub-felhőben.
 - **Biztonsági mentési erőforrás-szolgáltató**  
 A biztonsági mentési erőforrás-szolgáltató (Backup RP) a felhasználói felületből és API-kkal tevődik össze Azure Stack hub-infrastruktúra alapszintű biztonsági mentési funkcióit.

#### <a name="infrastructure-backup-controller"></a>Infrastructure Backup vezérlő

A Infrastructure Backup-vezérlő egy Service Fabric-szolgáltatás, amely egy Azure Stack hub-felhő példányaihoz lesz létrehozva. A biztonsági mentési erőforrások regionális szinten jönnek létre, és a régióra jellemző szolgáltatási adatok az AD, CA, Azure Resource Manager, CRP, szervizcsomag, NRP, Key Vault, RBAC.

### <a name="backup-resource-provider"></a>Biztonsági mentési erőforrás-szolgáltató

A biztonsági mentési erőforrás-szolgáltató felhasználói felületet biztosít a Azure Stack hub portálon a biztonsági mentési erőforrások alapszintű konfigurálásához és listázásához. A kezelők a következő műveleteket végezhetik el a felhasználói felületen:

 - A biztonsági mentés első alkalommal történő engedélyezése külső tárolási hely, hitelesítő adatok és titkosítási kulcs biztosításával.
 - A befejezett létrehozott biztonsági mentési erőforrások és az állapot-erőforrások megtekintése a létrehozás alatt.
 - Módosítsa a tárolási helyet, ahol a biztonságimásolat-vezérlő helyre helyezi a biztonsági mentési adatforrásokat.
 - Módosítsa azokat a hitelesítő adatokat, amelyeket a Backup vezérlő használ a külső tároló helyének eléréséhez.
 - Módosítsa a biztonságimásolat-vezérlő által a biztonsági másolatok titkosításához használt titkosítási kulcsot.


## <a name="backup-controller-requirements"></a>A biztonsági mentési vezérlőre vonatkozó követelmények

Ez a szakasz a Infrastructure Backup szolgáltatás fontos követelményeit ismerteti. Javasoljuk, hogy figyelmesen tekintse át az adatokat, mielőtt engedélyezi a Azure Stack hub-példány biztonsági mentését, majd az üzembe helyezés és az azt követő művelet során szükség szerint visszautalja azt.

A követelmények a következők:

  - A **szoftverre vonatkozó követelmények** – a támogatott tárolási helyszínek és a méretezési útmutatások ismertetése. 
  - **Hálózati követelmények** – a különböző tárolási helyszínekre vonatkozó hálózati követelményeket ismerteti.  

### <a name="software-requirements"></a>Szoftverkövetelmények

#### <a name="supported-storage-locations"></a>Támogatott tárolási helyszínek

| Tárolási hely                                                                 | Részletek                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| A megbízható hálózati környezetben található tárolóeszközön tárolt SMB-fájlmegosztás. | SMB-megosztás ugyanabban az adatközpontban, amelyben Azure Stack hub üzembe helyezése vagy egy másik adatközpontban található. Több Azure Stack hub-példány is használhatja ugyanazt a fájlmegosztást. |
| SMB-fájlmegosztás az Azure-ban.                                                          | Jelenleg nem támogatott.                                                                                                                                 |
| BLOB Storage az Azure-ban.                                                            | Jelenleg nem támogatott.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Támogatott SMB-verziók

| SMB | Verzió |
|-----|---------|
| SMB | 3.x     |

#### <a name="smb-encryption"></a>SMB-titkosítás

**1907 és azon túl**

A Infrastructure Backup szolgáltatás támogatja a biztonsági mentési adat átvitelét egy külső tárolóhelyre, amelyen engedélyezve van az SMB-titkosítás a kiszolgáló oldalán. Ha a kiszolgáló nem támogatja az SMB-titkosítást, vagy nincs engedélyezve a funkció, Infrastructure Backup szolgáltatás visszakerül a titkosítatlan adatforgalomra. A külső tárolási helyre helyezett biztonsági mentési adatok mindig inaktív állapotban vannak, és nem függenek az SMB-titkosítástól.

#### <a name="storage-location-sizing"></a>Tárolási hely méretezése

Javasoljuk, hogy naponta kétszer készítsen biztonsági mentést, és a biztonsági mentések legfeljebb hét napján maradjanak. Ez az alapértelmezett viselkedés, ha engedélyezi az infrastruktúra biztonsági mentését Azure Stack központban.

**1907 és azon túl**

***Az Azure AD Identity Provider szolgáltatáshoz csatlakoztatottrendszer***

| Környezeti skála | A biztonsági mentés tervezett mérete | A szükséges lemezterület teljes mennyisége |
|-------------------|--------------------------|--------------------------------|
| 4-16 csomópontok/ASDK   | 1 GB                     | 20 GB                          |

***A vállalati AD-identitás-szolgáltatóhoz csatlakozó rendszer az ADFS-n keresztül***

| Környezeti skála | A biztonsági mentés tervezett mérete | A szükséges lemezterület teljes mennyisége |
|-------------------|--------------------------|--------------------------------|
| 4-16 csomópont        | 20 GB                    | 280 GB                        |
| AZURE STACK FEJLESZTŐI KÉSZLET (ASDK)              | 10 GB                    | 140 GB                        |

**1907 előtti**

| Környezeti skála | A biztonsági mentés tervezett mérete | A szükséges lemezterület teljes mennyisége |
|-------------------|--------------------------|--------------------------------|
| 4-16 csomópont        | 20 GB                    | 280 GB                        |
| AZURE STACK FEJLESZTŐI KÉSZLET (ASDK)              | 10 GB                    | 140 GB                        |

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

| Tárolási hely                                                                 | Részletek                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A megbízható hálózati környezetben található tárolóeszközön tárolt SMB-fájlmegosztás. | A 445-es portra akkor van szükség, ha a Azure Stack hub-példány tűzfallal védett környezetben található. Infrastructure Backup vezérlő az 445-as porton keresztül kezdeményezi a kapcsolódást az SMB-fájlkiszolgálón. |
| A fájlkiszolgáló teljes tartománynevének használatához a névnek feloldhatónak kell lennie a PEP-ből.             |                                                                                                                                                                                         |

> [!Note]  
> Nincs szükség bejövő port megnyitására.

### <a name="encryption-requirements"></a>Titkosítási követelmények

A 1901-től kezdődően a Infrastructure Backup szolgáltatás nyilvános kulccsal rendelkező tanúsítványt fog használni (. CER) a biztonsági mentési és a titkos kulccsal rendelkező tanúsítvány titkosítása (. PFX) a biztonsági mentési állapot visszafejtése a felhőalapú helyreállítás során.

 - A tanúsítvány a kulcsok átviteléhez használatos, és nem a biztonságos hitelesített kommunikáció létrehozásához használatos. Emiatt a tanúsítvány önaláírt tanúsítvány lehet. Azure Stack hubhoz nem szükséges ellenőrizni a tanúsítvány gyökerét vagy megbízhatóságát, hogy a külső internet-hozzáférés nem szükséges.

Az önaláírt tanúsítvány két részből áll, egyet a nyilvános kulccsal, egy pedig a titkos kulccsal:

 - A biztonsági mentési adatai titkosítása: a (z) nyilvános kulccsal rendelkező tanúsítvány. CER-fájl) a biztonsági mentési adatai titkosítására szolgál.
 - Biztonsági másolatok visszafejtése: a titkos kulccsal rendelkező tanúsítvány (exportálva. PFX-fájl) a biztonsági másolatok visszafejtésére szolgál.

A nyilvános kulccsal rendelkező tanúsítvány (. A CER) nem a belső titok rotációja által felügyelt. A tanúsítvány elforgatásához létre kell hoznia egy új önaláírt tanúsítványt, és frissítenie kell a biztonsági mentési beállításokat az új fájllal (. CER). 
 
 - Az összes meglévő biztonsági mentés titkosítva marad a korábbi nyilvános kulcs használatával. Az új biztonsági másolatok az új nyilvános kulcsot használják.
 
A titkos kulccsal történő felhőalapú helyreállítás során használt tanúsítvány (. A PFX-t a Azure Stack hub biztonsági okokból nem őrzi meg. Ezt a fájlt explicit módon kell megadni a Felhőbeli helyreállítás során.  

**Visszamenőleges kompatibilitási mód** A 1901-től kezdődően a titkosítási kulcs támogatása elavult, és egy későbbi kiadásban el lesz távolítva. Ha a 1811-es verzióról a titkosítási kulccsal már engedélyezett biztonsági mentést használ, Azure Stack hub továbbra is a titkosítási kulcsot fogja használni. A visszamenőleges kompatibilitási módot legalább három kiadásban támogatja a rendszer. Ezt követően egy tanúsítványra lesz szükség.

 * A titkosítási kulcsról a tanúsítványra történő frissítés egy egyirányú művelet.  
 * Minden meglévő biztonsági mentés titkosítva marad a titkosítási kulccsal. Az új biztonsági mentések a tanúsítványt fogják használni. 

## <a name="infrastructure-backup-limits"></a>Infrastructure Backup korlátok

Ezeket a korlátokat a Microsoft Azure Stack hub-példányok tervezése, üzembe helyezése és üzemeltetése során érdemes figyelembe venni. A következő táblázat ezeket a korlátozásokat ismerteti.

### <a name="infrastructure-backup-limits"></a>Infrastructure Backup korlátok

| Korlátazonosító                                                 | Korlát        | Megjegyzések                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Biztonsági mentés típusa                                                      | Csak teljes    | Infrastructure Backup vezérlő csak a teljes biztonsági mentést támogatja. A növekményes biztonsági mentések nem támogatottak.                                          |
| Ütemezett biztonsági mentések                                                | Ütemezett és manuális  | A Backup vezérlő támogatja az ütemezett és igény szerinti biztonsági mentéseket.                                                                                 |
| Egyidejű biztonsági mentési feladatok maximális száma                                   | 1            | A biztonsági mentési vezérlő példányain csak egy aktív biztonsági mentési feladatok támogatottak.                                                                  |
| Hálózati kapcsoló konfigurációja                                     | Nincs a hatókörben | A rendszergazdának OEM-eszközök használatával kell biztonsági mentést készítenie a hálózati kapcsoló konfigurációjában. Az egyes OEM-gyártók által biztosított Azure Stack hub dokumentációjában talál további információt. |
| Hardver életciklus-állomása                                          | Nincs a hatókörben | A rendszergazdának OEM-eszközökkel kell biztonsági mentést készítenie a hardveres életciklus-gazdagépről. Az egyes OEM-gyártók által biztosított Azure Stack hub dokumentációjában talál további információt.      |
| Fájlmegosztás maximális száma                                    | 1            | A biztonsági másolatok tárolására csak egy fájlmegosztást lehet használni.                                                                                        |
| Backup App Services, függvény, SQL, MySQL erőforrás-szolgáltatói adat | Nincs a hatókörben | Tekintse meg a Microsoft által létrehozott RPs érték bevezetéséhez és kezeléséhez közzétett útmutatót.                                                  |
| Külső gyártótól származó erőforrás-szolgáltatók biztonsági mentése                              | Nincs a hatókörben | Tekintse meg a harmadik féltől származó gyártók által létrehozott, az érték hozzáadása és kezelése című témakörben közzétett útmutatót.                                          |

## <a name="next-steps"></a>Következő lépések

 - Ha többet szeretne megtudni a Infrastructure Backup szolgáltatásról, tekintse meg [a Azure stack hub biztonsági mentése és adathelyreállítása című témakört a Infrastructure Backup szolgáltatással](azure-stack-backup-infrastructure-backup.md).
