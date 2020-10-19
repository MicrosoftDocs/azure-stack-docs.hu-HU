---
title: MDC hálózati konfiguráció életciklus-kezelése
description: Ismerje meg a MDC-eszköz hálózati konfigurációjának életciklus-kezelését.
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 78e4a98a606b100e42d4beb465874d4d0648c971
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182959"
---
# <a name="configuration-lifecycle-management"></a>A konfiguráció életciklusának kezelése

Ez a témakör a hálózati konfiguráció életciklus-kezelését ismerteti.

## <a name="update-configuration"></a>Konfiguráció frissítése


A mezőben lévő kapcsolók konfigurációjának frissítésére szolgáló módszer. Ez az összes és bármilyen konfigurációs frissítésre vonatkozik. A vezérlőelemen kívüli számos ismeretlen változó miatt, például az ügyfelek által alkalmazott manuális konfigurációk vagy az OEM ez egy több lépésből álló manuális folyamat lesz. Jelenleg nem garantálható a rendelkezésre állás, és a frissítéseket a karbantartási időszakban kell elvégezni.

** \# 1 –** készítsen biztonsági másolatot a edg1, a edge2, a tor1, a TOR2 és a bmc kapcsolók aktuális konfigurációs fájljairól. Másolja ki ezeket a fájlokat a kapcsolóból.

** \# 2 –** jegyezze fel az eszközkészlet Build-számának meglévő konfigurációját.
Az összes konfigurációnak szerepelnie kell a **motd** -szalagcímben. Keressen rá a *"BUILDNUMBER"* kifejezésre

** \# 3 –** a kezdeti konfigurációk újbóli előállítása ugyanazzal az eszközkészlet-verzióval a Step \# használatával 2.

** \# 4 –** a 3. lépésben szereplő konfigurációk betöltése a \# megfelelő kapcsolókra.
Ennek a célja, hogy az eszközeink által generált konfigurációt az alapértékek lekérése érdekében egy kapcsolón keresztül *mossa* . Ezt elvégezheti az OEM külön hardveren, például az OEM laborban, vagy a helyszínen az ügyféllel.

** \# 5 –** készítsen biztonsági másolatot a konfigurációs fájlról a 4. lépésben \# , és másolja azt egy távoli helyre

** \# 6 –** ha az elemet a diff eszközre szeretné használni, hasonlítsa össze az 1. lépésben az aktuális konfiguráció biztonsági másolatát az \# 5. lépésben megadott alapkonfiguráció biztonsági másolatából \# . Jegyezze fel/másolja az összes olyan különbséget, amelyet eszközre kell átadni a frissített kapcsolók konfigurációjának.

** \# 7 –** futtassa az új eszközkészletet, amely a frissített kapcsolók konfigurációját fogja előállítani.

** \# 8 –** egyesítse a 6. lépés különbségeit az új kapcsolók konfigurációjában.

** \# 9 –** töltse be az új konfigurációkat a megfelelő kapcsolókra, és futtassa a kimeneti könyvtárban megadott utólagos érvényesítési parancsokat az eszközről.

** \# 10 –** a konfigurációk mentése.

## <a name="ntp"></a>NTP

A rendszerjogosultságú végpont (PEP) használatával frissítheti Azure Stack időkiszolgálóját. Olyan állomásnevet használjon, amely két vagy több NTP-kiszolgáló IP-címére van feloldva.

A Azure Stack a Network Time Protocol (NTP) protokollt használja az internetes időkiszolgálókhoz való kapcsolódáshoz. Az NTP-kiszolgálók pontos rendszeridőt biztosítanak. Az idő Azure Stack fizikai hálózati kapcsolók, a hardver életciklus-gazdagép, az infrastruktúra-szolgáltatás és a virtuális gépek között használatos. Ha az óra nincs szinkronizálva, Azure Stack a hálózattal és a hitelesítéssel kapcsolatos súlyos problémák léphetnek fel. A naplófájlok, dokumentumok és egyéb fájlok helytelen időbélyegzővel hozhatók létre.

A szinkronizálás időpontjának Azure Stack egy időkiszolgáló (NTP) megadása szükséges.
Azure Stack telepítésekor meg kell adnia egy NTP-kiszolgáló címeit. Az idő egy kritikus adatközpont-infrastruktúra szolgáltatás. Ha a szolgáltatás megváltozik, frissítenie kell az időt.

Azure Stack támogatja az idő szinkronizálását csak egyszeres kiszolgálóval (NTP). Nem adhat meg több NTPs a Azure Stack számára az idő szinkronizálásához. Ajánlott olyan DNS-bejegyzést beállítani, amely több NTP-kiszolgálóra is feloldható. 


### <a name="update-ntp-post-deployment"></a>NTP utáni üzembe helyezés frissítése

1.  Kapcsolódjon a privilegizált végponthoz (PEP). Nem kell megnyitnia egy támogatási jegyet a Kiemelt végpont zárolásának feloldásához. 

2.  Futtassa a következő parancsot az aktuálisan konfigurált NTP-kiszolgáló áttekintéséhez:

    ```powershell
    Get-AzsTimeSource
    ```

3.  Futtassa az alábbi parancsot a Azure Stack frissítéséhez az új NTP-kiszolgáló használatára és az idő azonnali szinkronizálásához

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

    >[!NOTE] 
    >Ez az eljárás nem frissíti az időkiszolgálót a fizikai kapcsolókon. 


## <a name="dns"></a>DNS

Ez a szakasz ismerteti, hogyan lehet frissíteni a DNS-továbbítókat a külső nevek feloldásához.

### <a name="update-the-dns-forwarder-in-azure-stack"></a>A DNS-továbbító frissítése Azure Stack

Legalább egy elérhető DNS-továbbító szükséges ahhoz, hogy a Azure Stack-infrastruktúra fel tudja oldani a külső neveket. Azure Stack telepítéséhez meg kell adni egy DNS-továbbítót. Ez a bemenet a Azure Stack belső DNS-kiszolgálóinak továbbítóként való használata, valamint a külső névfeloldás engedélyezése olyan szolgáltatások esetében, mint a hitelesítés, a piactér kezelése vagy a használat.

A DNS egy kritikus adatközpont-infrastruktúra szolgáltatás, amely megváltoztathatja, és ha igen, Azure Stack frissítenie kell.

Ez a cikk a rendszerjogosultságú végpont (PEP) használatával frissíti a DNS-továbbítót a Azure Stackban. Javasoljuk, hogy két megbízható DNS-továbbító IP-címet használjon.

1.  Kapcsolódjon a Kiemelt végponthoz. Vegye figyelembe, hogy egy támogatási jegy megnyitásával nem szükséges feloldani a Kiemelt végpontot.

2.  A következő parancs futtatásával tekintse át az aktuálisan konfigurált DNS-továbbítót. Másik lehetőségként használhatja a felügyeleti portál régiójának tulajdonságait is:

    ```powershell
    Get-AzsDnsForwarder 
    ```

3.  Futtassa a következő parancsot a Azure Stack frissítéséhez az új DNS-továbbító használatára:

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2"
    ``` |

4.  Review the output of the command for any errors.
