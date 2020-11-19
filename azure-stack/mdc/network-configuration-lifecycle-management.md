---
title: MDC hálózati konfiguráció életciklus-kezelése
description: Ismerje meg a hálózati konfiguráció életciklusának kezelését az Azure moduláris adatközpontban.
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: b88511f47064f0ac55571c81924cca13eb65e9f6
ms.sourcegitcommit: 25b234330df4e753ed2dd480c208ec88cd90234c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94924350"
---
# <a name="configuration-lifecycle-management"></a>A konfiguráció életciklusának kezelése

Ez a cikk az Azure moduláris adatközpontok hálózati konfigurációjának életciklus-kezelését ismerteti.

## <a name="update-configuration"></a>Konfiguráció frissítése

Ez a módszer a kapcsolók konfigurációjának frissítésére az összes és bármilyen típusú konfigurációs frissítésre vonatkozik. A vezérlőelemen kívüli számos ismeretlen változó miatt, például az ügyfelek vagy az OEM által alkalmazott manuális konfigurációk esetében ennek a manuális folyamatnak több lépése is van. Jelenleg nincs garancia a rendelkezésre állásra. A frissítéseket a karbantartási időszak alatt kell végrehajtani.

1. Készítsen biztonsági másolatot a edge1, a edge2, a tor1, a tor2 és a BMC kapcsolók aktuális konfigurációs fájljairól. Másolja ki ezeket a fájlokat a kapcsolóból.

1. Jegyezze fel a meglévő konfiguráció eszközkészletének összeállítási számát. Az összes konfigurációnak ezt a számot kell tartalmaznia a **motd** -szalagcímben. Keressen rá a **BUILDNUMBER**.

1. A kezdeti konfigurációk újbóli előállítása a 2. lépésben megegyező eszközkészlet-verzió használatával.

1. Töltse be a 3. lépésben szereplő konfigurációkat a megfelelő kapcsolókra. Ennek a műveletnek a lényege, hogy az eszközeink által generált konfigurációt az alapértékek lekérése érdekében egy kapcsolón keresztül *mossa* . Ezt a műveletet az OEM külön hardveren, például az OEM laborban, vagy az ügyféllel együtt is elvégezheti.

1. Készítsen biztonsági másolatot a konfigurációs fájlról a 4. lépésből. Másolja egy távoli helyre.

1. A választott diff eszköz használatával hasonlítsa össze az 1. lépésben az aktuális konfiguráció biztonsági mentését az 5. lépésből származó alapkonfiguráció biztonsági másolatából. Jegyezze fel vagy másolja az összes olyan különbséget, amelyet az eszközön lévő frissített kapcsolók konfigurációjában kell végrehajtani.

1. Futtassa az új eszközkészletet, amely a frissített kapcsolók konfigurációját fogja előállítani.

1. Egyesítse a 6. lépés különbségeit az új kapcsolók konfigurációjában.

1. Töltse be az új konfigurációkat a megfelelő kapcsolókra. Futtassa a kimeneti könyvtárban megadott utólagos érvényesítési parancsokat az eszközökről.

1. Mentse a konfigurációkat.

## <a name="ntp"></a>NTP

A rendszerjogosultságú végpont (PEP) használatával frissítheti Azure Stack időkiszolgálóját. Olyan állomásnevet használjon, amely két vagy több Network Time Protocol (NTP) kiszolgáló IP-címére van feloldva.

A Azure Stack az NTP használatával csatlakozik az internethez tartozó időkiszolgálóhoz. Az NTP-kiszolgálók pontos rendszeridőt biztosítanak. Az idő Azure Stack fizikai hálózati kapcsolók, a hardver életciklus-gazdagép, az infrastruktúra-szolgáltatás és a virtuális gépek között használatos. Ha az óra nincs szinkronizálva, Azure Stack a hálózattal és a hitelesítéssel kapcsolatos súlyos problémákba ütközhet. Előfordulhat, hogy a naplófájlok, dokumentumok és egyéb fájlok helytelen időbélyegekkel hozhatók létre.

A szinkronizálás időpontjának Azure Stack egy időkiszolgáló (NTP) megadása szükséges. Azure Stack telepítésekor meg kell adnia egy NTP-kiszolgáló címeit. Az idő egy kritikus adatközpont-infrastruktúra szolgáltatás. Ha a szolgáltatás megváltozik, frissítenie kell az időt.

Azure Stack támogatja az idő szinkronizálását csak egyszeres kiszolgálóval (NTP). Nem adhat meg több NTPs a Azure Stack számára az idő szinkronizálásához. Javasoljuk, hogy hozzon létre egy olyan DNS-bejegyzést, amely több NTP-kiszolgálóra is feloldható.

### <a name="update-ntp-post-deployment"></a>NTP utáni üzembe helyezés frissítése

1. Kapcsolódjon a PEP-hez. Nem kell megnyitnia egy támogatási jegyet a zárolás feloldásához.

1. Futtassa a következő parancsot az aktuálisan konfigurált NTP-kiszolgáló áttekintéséhez.

    ```powershell
    Get-AzsTimeSource
    ```

1. A következő parancs futtatásával frissítse Azure Stack az új NTP-kiszolgáló használatára és az idő azonnali szinkronizálására.

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

>[!NOTE]
>Ez az eljárás nem frissíti az időkiszolgálót a fizikai kapcsolókon.

## <a name="dns"></a>DNS

Ez a szakasz ismerteti, hogyan lehet frissíteni a DNS-továbbítókat a külső nevek feloldásához.

### <a name="update-the-dns-forwarder-in-azure-stack"></a>A DNS-továbbító frissítése Azure Stack

Legalább egy elérhető DNS-továbbító szükséges ahhoz, hogy a Azure Stack-infrastruktúra fel tudja oldani a külső neveket. Azure Stack telepítéséhez meg kell adni egy DNS-továbbítót. Ez a bemenet a Azure Stack belső DNS-kiszolgálókhoz továbbítóként használható, és lehetővé teszi a külső névfeloldást olyan szolgáltatások esetében, mint a hitelesítés, a piactér felügyelete vagy a használat.

A DNS egy kritikus adatközpont-infrastruktúra szolgáltatás, amely megváltozhat. Ha módosítja, Azure Stack frissítenie kell.

Ez a cikk azt ismerteti, hogyan lehet a PEP használatával frissíteni a DNS-továbbítót a Azure Stackban. Javasoljuk, hogy két megbízható DNS-továbbító IP-címet használjon.

1. Kapcsolódjon a PEP-hez. Egy támogatási jegy megnyitásával nem szükséges a PEP feloldása.

1. A következő parancs futtatásával tekintse át az aktuálisan konfigurált DNS-továbbítót. Másik lehetőségként használhatja a felügyeleti portál régiójának tulajdonságait is.

    ```powershell
    Get-AzsDnsForwarder 
    ```

1. Futtassa a következő parancsot a Azure Stack frissítéséhez az új DNS-továbbító használatára.

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2"
    ```

1. Az esetleges hibákért tekintse át a parancs kimenetét.
