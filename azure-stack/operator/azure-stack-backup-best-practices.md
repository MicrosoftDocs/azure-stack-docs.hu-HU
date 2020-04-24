---
title: Infrastructure Backup az Azure Stack hub szolgáltatás ajánlott eljárásai
description: Az adatvesztés enyhítése érdekében az Azure Stack hub üzembe helyezése és kezelése során kövesse ezeket az ajánlott eljárásokat, ha végzetes hiba történt.
author: justinha
ms.topic: article
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: a141beed4df6b34175f37d9e1e60e694f3ab71f2
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77700510"
---
# <a name="infrastructure-backup-service-best-practices"></a>Infrastructure Backup Service – ajánlott eljárások

Az adatvesztés enyhítése érdekében az Azure Stack hub üzembe helyezése és kezelése során kövesse ezeket az ajánlott eljárásokat, ha végzetes hiba történt.

Rendszeresen tekintse át az ajánlott eljárásokat annak ellenőrzéséhez, hogy a telepítés még mindig megfelel-e a műveleti folyamat változásainak. Ha bármilyen probléma merül fel az ajánlott eljárások megvalósítása során, lépjen kapcsolatba Microsoft ügyfélszolgálata segítségért.

## <a name="configuration-best-practices"></a>Ajánlott eljárások a konfigurációhoz

### <a name="deployment"></a>Üzembe helyezés

Infrastructure Backup engedélyezése az egyes Azure Stack hub-felhő üzembe helyezése után. Azure Stack hub PowerShell használatával bármely ügyfél/kiszolgáló biztonsági mentését ütemezheti az operátori felügyeleti API-végponthoz való hozzáféréssel.

### <a name="networking"></a>Hálózat

Az elérési úthoz az univerzális elnevezési konvenció (UNC) karakterláncának teljes tartománynevet (FQDN) kell használnia. Az IP-cím akkor használható, ha a névfeloldás nem lehetséges. Az UNC-karakterlánc megadja az erőforrások, például a megosztott fájlok vagy eszközök helyét.

### <a name="encryption"></a>Titkosítás

#### <a name="version-1901-and-newer"></a>1901-es és újabb verzió

A titkosítási tanúsítvány a külső tárolóba exportált biztonsági másolatok titkosítására szolgál. A tanúsítvány lehet önaláírt tanúsítvány, mivel a tanúsítvány csak a kulcsok továbbítására szolgál. A tanúsítványok létrehozásával kapcsolatos további információkért tekintse meg a New-SelfSignedCertificate című témakört.
  
A kulcsot biztonságos helyen kell tárolni (például globális Azure Key Vault tanúsítvány). A tanúsítvány CER-formátuma az adattitkosításra szolgál. A PFX-formátumot a Azure Stack hub Cloud Recovery telepítése során kell használni a biztonsági mentési adatmennyiség visszafejtéséhez.

![A tanúsítvány tárolása biztonságos helyen történik.](media/azure-stack-backup/azure-stack-backup-encryption-store-cert.png)

#### <a name="1811-and-older"></a>1811 és régebbi

A titkosítási kulcs segítségével titkosíthatja a külső tárolóba exportált biztonsági mentési adataikat. A kulcs a [Azure stack hub PowerShell-lel történő biztonsági mentésének engedélyezésének](azure-stack-backup-enable-backup-powershell.md)részeként jön létre.

A kulcsot biztonságos helyen kell tárolni (például globális Azure Key Vault titkos kulcs). Ezt a kulcsot Azure Stack hub újratelepítése során kell használni.

![A kulcs tárolása biztonságos helyen történik.](media/azure-stack-backup/azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Ajánlott üzemeltetési eljárások

### <a name="backups"></a>Biztonsági másolatok

 - A biztonsági mentési feladatok a rendszer futása közben futnak, így nincs leállás a kezelési élmények vagy a felhasználói alkalmazások esetében. A biztonsági mentési feladatok 20-40 percet vesznek igénybe egy ésszerű terhelés alá tartozó megoldás esetében.
 - A SZÁMÍTÓGÉPGYÁRTÓ által megadott utasításokat, a hálózati kapcsolók manuális biztonsági mentését, valamint a hardveres életciklus-gazdagépet (HLH) ugyanazon a biztonsági mentési megosztáson kell tárolni, ahol a Infrastructure Backup vezérlő tárolja a vezérlési sík biztonsági mentési információit. Érdemes lehet kapcsoló-és HLH-konfigurációkat tárolni a régió mappában. Ha több Azure Stack hub-példánya van ugyanabban a régióban, érdemes lehet azonosítót használni a méretezési egységhez tartozó minden egyes konfigurációhoz.

### <a name="folder-names"></a>Mappák nevei

 - Az infrastruktúra automatikusan hozza létre a MASBACKUP mappát. Ez egy Microsoft által felügyelt megosztás. A megosztásokat a MASBACKUP azonos szinten is létrehozhatja. Nem ajánlott mappák vagy tárolási adattárolót létrehozni a MASBACKUP belül, hogy Azure Stack hub ne hozzon létre.
 -  A felhasználói FQDN és a mappa neve a különböző felhőkből származó biztonsági mentési adatok megkülönböztetéséhez. A Azure Stack hub központi telepítésének és végpontjának teljes tartományneve a régió paraméter és a külső tartománynév paraméter kombinációja. További információ: [Azure stack hub Datacenter Integration-DNS](azure-stack-integrate-dns.md).

A biztonsági másolat megosztását például a fileserver01.contoso.com AzSBackups tárolja. Ebben a fájlmegosztásban előfordulhat, hogy a külső tartománynévvel és a régió nevét használó almappában Azure Stack hub központi telepítésének mappája.

Teljes tartománynév: contoso.com  
Régió: NYC


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

A MASBackup mappa a Azure Stack hub tárolja a biztonsági másolati adatbázisokat. Ne használja ezt a mappát a saját adatai tárolásához. A számítógépgyártók nem használhatják ezt a mappát az összes biztonsági mentési érték tárolására.

A számítógépgyártóknak javasoljuk, hogy a régió mappájában tárolja az összetevőinek biztonsági mentési adatait. A hálózati kapcsolók, a hardveres életciklus-állomás (HLH) és így tovább is tárolhatók a saját almappájában. Például:

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Figyelés

A rendszer a következő riasztásokat támogatja:

| Riasztás                                                   | Leírás                                                                                     | Kockázatcsökkentés                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| A biztonsági mentés nem sikerült, mert a fájlmegosztás kapacitása nem működik. | A fájlmegosztás nem kapacitású, és a biztonságimásolat-vezérlő nem tudja exportálni a biztonságimásolat-fájlokat a helyre. | Vegyen fel további tárolókapacitást, és próbálkozzon újra a biztonsági mentéssel. A meglévő biztonsági másolatok törlése (az elsőtől kezdve) szabadítson fel lemezterületet.                    |
| Csatlakozási problémák miatt nem sikerült a biztonsági mentés.             | A Azure Stack hub és a fájlmegosztás közötti hálózat problémákba ütközik.                          | Oldja meg a hálózati problémát, és próbálkozzon újra a biztonsági mentéssel.                                                                                            |
| A biztonsági mentés nem sikerült, mert hiba történt az elérési úton.                | A fájlmegosztás elérési útja nem oldható fel.                                                          | Képezze le a megosztást egy másik számítógépről, hogy a megosztás elérhető legyen. Előfordulhat, hogy frissítenie kell az elérési utat, ha már nem érvényes.       |
| A biztonsági mentés hitelesítési probléma miatt nem sikerült.               | Előfordulhat, hogy probléma van a hitelesítő adatokkal vagy a hitelesítést befolyásoló hálózati problémával.    | Képezze le a megosztást egy másik számítógépről, hogy a megosztás elérhető legyen. Előfordulhat, hogy frissítenie kell a hitelesítő adatokat, ha már nem érvényesek. |
| A biztonsági mentés általános hiba miatt nem sikerült.                    | A sikertelen kérést egy átmeneti probléma okozhatja. Próbálkozzon újra a biztonsági mentéssel.                    | Hívja a támogatási szolgálatot.                                                                                                                               |

## <a name="next-steps"></a>További lépések

Tekintse át a [Infrastructure Backup szolgáltatás](azure-stack-backup-reference.md)hivatkozási anyagát.

Engedélyezze a [Infrastructure Backup szolgáltatást](azure-stack-backup-enable-backup-console.md).
