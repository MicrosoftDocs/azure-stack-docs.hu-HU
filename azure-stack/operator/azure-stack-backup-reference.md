---
title: Azure Stack Infrastructure Backup szolgáltatás leírása | Microsoft Docs
description: Ez a cikk a Azure Stack Infrastructure Backup szolgáltatáshoz kapcsolódó referenciaanyagok leírását tartalmazza.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: d45b11eb70533125ff8136763be24a3333c1f7dc
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493952"
---
# <a name="infrastructure-backup-service-reference"></a>Infrastructure Backup szolgáltatás leírása

## <a name="azure-backup-infrastructure"></a>Azure Backup-infrastruktúra

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure Stack számos olyan szolgáltatást tartalmaz, amelyek a portál, a Azure Resource Manager és az infrastruktúra-kezelés felhasználói felületét alkotják. Az Azure Stack készülékhez hasonló kezelési élmény a megoldás kezelője számára elérhető bonyolultság csökkentését összpontosítja.

A Infrastructure Backup úgy lett kialakítva, hogy az infrastruktúra-szolgáltatások biztonsági mentésének és visszaállításának összetettségét kihasználja, így a kezelők a megoldás felügyeletére és a felhasználókra vonatkozó SLA-k fenntartására összpontosítanak.

A biztonsági mentések külső megosztásra való exportálásának elkerüléséhez a biztonsági másolatok ugyanazon a rendszeren való tárolása szükséges. A külső megosztás megkövetelése lehetővé teszi a rendszergazda számára, hogy megtudja, hol tárolja az adattárolást a meglévő BC/DR-szabályzatok alapján. 

### <a name="infrastructure-backup-components"></a>Összetevők Infrastructure Backup

Infrastructure Backup a következő összetevőket tartalmazza:

 - **Infrastructure Backup vezérlő**  
 A Infrastructure Backup vezérlő a-ben és a-ban található minden Azure Stack-felhőben.
 - **Biztonsági mentési erőforrás-szolgáltató**  
 A biztonsági mentési erőforrás-szolgáltató (Backup RP) a felhasználói felület és az alkalmazásprogramozási felületek (API-k) és a Azure Stack-infrastruktúra alapszintű biztonsági mentési funkciója.

#### <a name="infrastructure-backup-controller"></a>Infrastructure Backup vezérlő

A Infrastructure Backup vezérlő egy Service Fabric-szolgáltatás, amely egy Azure Stack felhő példányát kapja meg. A biztonsági mentési erőforrások regionális szinten jönnek létre, és a régióra jellemző szolgáltatási adatok rögzítése AD, CA, Azure Resource Manager, CRP, RBAC, NRP, Key Vault, 

### <a name="backup-resource-provider"></a>Biztonsági mentési erőforrás-szolgáltató

A biztonsági mentési erőforrás-szolgáltató a Azure Stack portál felhasználói felületét mutatja be a biztonsági mentési erőforrások alapszintű konfigurálásához és listázásához. A kezelő a következő műveleteket hajthatja végre a felhasználói felületen:

 - A biztonsági mentés első alkalommal történő engedélyezése külső tárolási hely, hitelesítő adatok és titkosítási kulcs biztosításával
 - A befejezett létrehozott biztonsági mentési erőforrások és az állapot-erőforrások megtekintése a létrehozás alatt
 - A tárolási hely módosítása, ahol a biztonságimásolat-vezérlő a biztonsági mentési adatforrásokat helyezi
 - A biztonsági mentési vezérlő által a külső tároló helyének eléréséhez használt hitelesítő adatok módosítása
 - A biztonságimásolat-vezérlő által a biztonsági másolatok titkosításához használt titkosítási kulcs módosítása 


## <a name="backup-controller-requirements"></a>A biztonsági mentési vezérlőre vonatkozó követelmények

Ez a szakasz a Infrastructure Backup fontos követelményeit ismerteti. Javasoljuk, hogy körültekintően tekintse át az információkat, mielőtt engedélyezi a Azure Stack példány biztonsági mentését, majd az üzembe helyezés és az azt követő művelet során szükség szerint visszautalja azt.

A követelmények a következők:

  - A **szoftverre vonatkozó követelmények** – a támogatott tárolási helyszínek és a méretezési útmutatások ismertetése. 
  - **Hálózati követelmények** – a különböző tárolási helyszínekre vonatkozó hálózati követelményeket ismerteti.  

### <a name="software-requirements"></a>Szoftverkövetelmények

#### <a name="supported-storage-locations"></a>Támogatott tárolási helyszínek

| Tárolási hely                                                                 | Részletek                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Az SMB-fájlmegosztás a megbízható hálózati környezetben tárolt tárolóeszközön található. | SMB-megosztás ugyanabban az adatközpontban, ahol a Azure Stack telepítve van, vagy egy másik adatközpontban. Több Azure Stack példány is használhatja ugyanazt a fájlmegosztást. |
| SMB-fájlmegosztás az Azure-ban                                                          | Jelenleg nem támogatott.                                                                                                                                 |
| BLOB Storage az Azure-ban                                                            | Jelenleg nem támogatott.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Támogatott SMB-verziók

| SMB | Version |
|-----|---------|
| SMB | 3.x     |

#### <a name="smb-encryption"></a>SMB-titkosítás

**1907 és azon túl**

Az infrastruktúra-biztonsági mentési szolgáltatás támogatja a biztonsági mentési adat átvitelét egy külső tárolóhelyre, amelyen engedélyezve van az SMB-titkosítás a kiszolgálói oldalon. Ha a kiszolgáló nem támogatja az SMB-titkosítást, vagy nincs engedélyezve a funkció, az infrastruktúra-biztonsági mentési szolgáltatás visszakerül a titkosítatlan adatforgalomra. A külső tárolóhelyre helyezett biztonsági mentési adatok mindig titkosítva maradnak, és nem függenek az SMB-titkosítástól. 

#### <a name="storage-location-sizing"></a>Tárolási hely méretezése 

Javasoljuk, hogy naponta kétszer készítsen biztonsági mentést, és a biztonsági mentések legfeljebb hét napján maradnak meg. Ez az alapértelmezett viselkedés, ha engedélyezi a Azure Stack az infrastruktúra biztonsági mentését. 

**1907 és azon túl**

***Az Azure AD Identity Provider szolgáltatáshoz csatlakoztatottrendszer***

| Környezeti skála | A biztonsági mentés tervezett mérete | A szükséges lemezterület teljes mennyisége |
|-------------------|--------------------------|--------------------------------|
| 4-16 csomópontok/ASDK   | 1 GB                     | 20 GB                          |

***A vállalati AD-identitás-szolgáltatóhoz csatlakozó rendszer az ADFS-n keresztül***

| Környezeti skála | A biztonsági mentés tervezett mérete | A szükséges lemezterület teljes mennyisége |
|-------------------|--------------------------|--------------------------------|
| 4-16 csomópont        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

**1907 előtti**

| Környezeti skála | A biztonsági mentés tervezett mérete | A szükséges lemezterület teljes mennyisége |
|-------------------|--------------------------|--------------------------------|
| 4-16 csomópont        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

| Tárolási hely                                                                 | Részletek                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Az SMB-fájlmegosztás a megbízható hálózati környezetben tárolt tárolóeszközön található. | A 445-es port megadása kötelező, ha a Azure Stack-példány tűzfallal védett környezetben található. Infrastructure Backup vezérlő az 445-as porton keresztül kezdeményezi a kapcsolódást az SMB-fájlkiszolgálón. |
| A fájlkiszolgáló teljes tartománynevének használatához a névnek feloldhatónak kell lennie a PEP-ből             |                                                                                                                                                                                         |

> [!Note]  
> Nincs szükség bejövő port megnyitására.

### <a name="encryption-requirements"></a>Titkosítási követelmények

A 1901-től kezdődően az infrastruktúra-biztonsági mentési szolgáltatás egy nyilvános kulccsal rendelkező tanúsítványt fog használni (. CER) a biztonsági mentési és a titkos kulccsal rendelkező tanúsítvány titkosítása (. PFX) a biztonsági mentési állapot visszafejtése a felhőalapú helyreállítás során.   
 - A tanúsítvány a kulcsok átviteléhez használatos, és nem a biztonságos hitelesített kommunikáció létrehozásához használatos. Emiatt a tanúsítvány önaláírt tanúsítvány lehet. Azure Stack nem kell ellenőriznie a tanúsítvány gyökerét vagy megbízhatóságát, hogy a külső internet-hozzáférés nem szükséges.
 
Az önaláírt tanúsítvány két részből áll, egyet a nyilvános kulccsal, egy pedig a titkos kulccsal:
 - Biztonsági mentési adatai titkosítása: A nyilvános kulccsal rendelkező tanúsítvány (a következőre lett exportálva:. CER-fájl) a biztonsági mentési adatai titkosítására szolgál
 - Biztonsági másolatok visszafejtése: A titkos kulccsal rendelkező tanúsítvány (a következőre lett exportálva:. PFX-fájl) a biztonsági másolatok visszafejtésére szolgál

A nyilvános kulccsal rendelkező tanúsítvány (. A CER) nem a belső titok rotációja által felügyelt. A tanúsítvány elforgatásához létre kell hoznia egy új önaláírt tanúsítványt, és frissítenie kell a biztonsági mentési beállításokat az új fájllal (. CER).  
 - Az összes meglévő biztonsági mentés titkosítva marad a korábbi nyilvános kulcs használatával. Az új biztonsági mentések az új nyilvános kulcsot fogják használni. 
 
A titkos kulccsal történő felhőalapú helyreállítás során használt tanúsítvány (. A PFX-t a Azure Stack biztonsági okokból nem őrzi meg. Ezt a fájlt explicit módon kell megadni a Felhőbeli helyreállítás során.  

**Visszamenőleges kompatibilitási mód** A 1901-től kezdődően a titkosítási kulcs támogatása elavult, és egy későbbi kiadásban el lesz távolítva. Ha a 1811-as verzióról a titkosítási kulccsal már engedélyezett biztonsági mentést használ, a Azure Stack továbbra is a titkosítási kulcsot fogja használni. A visszamenőleges kompatibilitási módot legalább 3 kiadásban támogatni fogja a rendszer. Ezt követően egy tanúsítványra lesz szükség. 
 * A titkosítási kulcsról a tanúsítványra történő frissítés egy egyirányú művelet.  
 * Minden meglévő biztonsági mentés titkosítva marad a titkosítási kulccsal. Az új biztonsági mentések a tanúsítványt fogják használni. 

## <a name="infrastructure-backup-limits"></a>Infrastructure Backup korlátok

Vegye figyelembe ezeket a korlátokat a Microsoft Azure Stack példányok tervezése, üzembe helyezése és üzemeltetése során. A következő táblázat ezeket a korlátozásokat ismerteti.

### <a name="infrastructure-backup-limits"></a>Infrastructure Backup korlátok

| Korlátazonosító                                                 | Korlát        | Megjegyzések                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Biztonsági mentés típusa                                                      | Csak teljes    | Infrastructure Backup vezérlő csak a teljes biztonsági mentést támogatja. A növekményes biztonsági mentések nem támogatottak.                                          |
| Ütemezett biztonsági mentések                                                | Ütemezett és manuális  | A Backup vezérlő támogatja az ütemezett és igény szerinti biztonsági mentéseket                                                                                 |
| Egyidejű biztonsági mentési feladatok maximális száma                                   | 1            | A biztonsági mentési vezérlő példányain csak egy aktív biztonsági mentési feladatok támogatottak.                                                                  |
| Hálózati kapcsoló konfigurációja                                     | Nincs a hatókörben | Az OEM-eszközökkel a rendszergazdának biztonsági mentést kell készítenie a hálózati kapcsolók konfigurációjában. Tekintse meg az egyes OEM-gyártók által biztosított Azure Stack dokumentációját. |
| Hardver életciklus-állomása                                          | Nincs a hatókörben | A rendszergazdának OEM-eszközökkel kell biztonsági mentést készítenie a hardveres életciklus-gazdagépről. Tekintse meg az egyes OEM-gyártók által biztosított Azure Stack dokumentációját.      |
| Fájlmegosztás maximális száma                                    | 1            | Csak egy fájlmegosztás használható a biztonsági másolatok tárolására                                                                                        |
| Backup App Services, függvény, SQL, MySQL erőforrás-szolgáltatói adat | Nincs a hatókörben | Tekintse meg a Microsoft által létrehozott RPs érték bevezetéséhez és kezeléséhez közzétett útmutatót.                                                  |
| Külső gyártótól származó erőforrás-szolgáltatók biztonsági mentése                              | Nincs a hatókörben | Tekintse meg a harmadik féltől származó gyártók által létrehozott, az érték hozzáadása és kezelése című témakörben közzétett útmutatót.                                          |

## <a name="next-steps"></a>További lépések

 - Ha többet szeretne megtudni a Infrastructure Backup szolgáltatásról, tekintse meg [a Azure stack biztonsági mentése és helyreállítása a Infrastructure Backup szolgáltatással](azure-stack-backup-infrastructure-backup.md)című témakört.
