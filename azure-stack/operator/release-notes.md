---
title: Azure Stack kibocsátási megjegyzések | Microsoft Docs
description: Ismerkedjen meg Azure Stack integrált rendszerek frissítéseivel, beleértve az újdonságokat, valamint a frissítés letöltésének helyét.
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
ms.date: 12/18/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 11/22/2019
ms.openlocfilehash: 8c124f44ed938108a4b23cea44642b7e275eb04d
ms.sourcegitcommit: c0fe1ac8c38d0475621ad8022db96983f9e5ae79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2019
ms.locfileid: "75188400"
---
# <a name="azure-stack-updates-release-notes"></a>Azure Stack frissítések: kibocsátási megjegyzések

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk a Azure Stack frissítési csomagjainak tartalmát ismerteti. A frissítés a Azure Stack ezen kiadásának újdonságait és javításait tartalmazza.

Ha egy másik verzió kibocsátási megjegyzéseit szeretné elérni, a bal oldali tartalomjegyzéknél használja a verziószám legördülő menüt.

::: moniker range=">=azs-1906"
> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.
::: moniker-end
::: moniker range="<azs-1906"
> [!IMPORTANT]  
> Ha a Azure Stack-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](known-issues.md)
- [Biztonsági frissítések](release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](release-notes-checklist.md)

A frissítésekkel és a frissítési folyamattal kapcsolatos segítségért tekintse meg a [javítások és a frissítési problémák elhárítása Azure stack](azure-stack-updates-troubleshoot.md).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>1910-Build referenciája

A Azure Stack 1910 frissítési Build száma **1.1910.0.58**.

### <a name="update-type"></a>Frissítés típusa

Az 1908-es verziójától kezdve a mögöttes operációs rendszer, amelyen a Azure Stack fut, a Windows Server 2019-re frissült. Ez lehetővé teszi az alapvető fontosságú fejlesztéseket, valamint a közeljövőben Azure Stack további képességeket is.

A Azure Stack 1910 frissítési Build típusa **expressz**.

Az 1910-es frissítési csomag mérete nagyobb a korábbi frissítésekhez képest. A megnövelt méret a letöltendő időt eredményezi. A frissítés hosszú ideig az **előkészítési** állapotban marad, és a kezelők a korábbi frissítéseknél hosszabb időt vehetnek igénybe. Az 1910-es frissítés befejezéséhez szükséges idő körülbelül 10 óra, a Azure Stack-környezetben található fizikai csomópontok számától függetlenül. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek. A várt értéknél hosszabb ideig tartó futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul. Ez a futásidejű közelítés az 1910-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure Stackban](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- A felügyeleti portál mostantól megjeleníti a Kiemelt végpontok IP-címeit a régió tulajdonságok menüjében, amely megkönnyíti a felderítést. Emellett megjeleníti az aktuálisan konfigurált időkiszolgálót és a DNS-továbbítókat.

- Ha hiba történik, a Azure Stack állapot-és figyelési rendszer a különböző hardver-összetevőkre vonatkozó riasztásokat is képes létrehozni. Ehhez további konfigurálásra van szükség. További információ: [Azure stack hardver-összetevők figyelése](azure-stack-hardware-monitoring.md).

- [Cloud-init támogatás a Azure Stackhoz](/azure/virtual-machines/linux/using-cloud-init): a Cloud-init egy széles körben használt módszer a Linux RENDSZERű virtuális gépek első indítására. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a Cloud-init a kezdeti rendszerindítási folyamat során hívásra kerül, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához. A piactéren lévő Ubuntu-lemezképek frissítve lettek a Cloud-init támogatásához a kiépítés érdekében.

- A Azure Stack mostantól támogatja az összes Windows Azure Linux-ügynök verzióját az Azure-ban.

- Azure Stack rendszergazdai PowerShell-modulok új verziója érhető el. <!-- For more information, see -->

- Hozzáadta a **set-AzSDefenderManualUpdate** parancsmagot az emelt szintű végponthoz (PEP) a Windows Defender-definíciók manuális frissítésének konfigurálásához a Azure stack-infrastruktúrában. További információ: [a Windows Defender víruskereső frissítése Azure stackon](azure-stack-security-av.md).

- Hozzáadta a **Get-AzSDefenderManualUpdate** parancsmagot a Kiemelt jogosultságú végponthoz (PEP) a Azure stack infrastruktúrájában a Windows Defender-definíciók manuális frissítésének konfigurálásához. További információ: [a Windows Defender víruskereső frissítése Azure stackon](azure-stack-security-av.md).

- Hozzáadta a **set-AzSDnsForwarder** parancsmagot az emelt szintű végponthoz (PEP) a Azure stack DNS-kiszolgálóinak továbbító beállításainak módosításához. További információ a DNS-konfigurációról: [Azure stack Datacenter DNS-integráció](azure-stack-integrate-dns.md).

- Hozzáadta a **Get-AzSDnsForwarder** parancsmagot a Kiemelt jogosultságú végponthoz (PEP) a Azure stack DNS-kiszolgálóinak továbbító beállításainak lekéréséhez. További információ a DNS-konfigurációról: [Azure stack Datacenter DNS-integráció](azure-stack-integrate-dns.md).

- A **Kubernetes-fürtök** az [AK motorral](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)való felügyeletének támogatása. Ettől a frissítéstől kezdve az ügyfelek üzembe helyezhetik a termelési Kubernetes-fürtöket. Az AK-motor lehetővé teszi a felhasználók számára a következőket:
   - Kezelheti a Kubernetes-fürtök életciklusát. Létrehozhatják, frissíthetik és méretezhetik a fürtöket.
   - A fürtöket az AK és a Azure Stack csapatok által készített felügyelt rendszerképek használatával kezelheti.
   - Kihasználhatja az olyan Azure Resource Manager integrált Kubernetes-szolgáltatót, amely az Azure natív erőforrásaival rendelkező fürtöket hoz létre.
   - Fürtök üzembe helyezése és kezelése csatlakoztatott vagy leválasztott Azure Stack stampekben.
   - Használja az Azure Hybrid-szolgáltatásokat:
      * Integráció az Azure arc-nal (a privát előzetes verzió hamarosan elérhető)
      * Integráció a Azure Monitor for containers szolgáltatással (nyilvános előzetes verzió)
   - A Windows-tárolók használata az KABAi motorral (privát előzetes verzió).
   - Fogadja a CSS-és PG-támogatást az üzemelő példányokhoz.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- A Azure Stack továbbfejlesztette a frissítési hibákat okozó javítási és frissítési hibák javítását, illetve megakadályozta, hogy a kezelők Azure Stack frissítést kezdeményezzenek. Ennek eredményeképpen a **test-AzureStack-UpdateReadiness** csoport kevesebb tesztet tartalmaz. További információ: [Azure stack rendszer állapotának ellenőrzése](azure-stack-diagnostic-test.md#groups). A következő három teszt marad a **UpdateReadiness** csoportban:

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Új naplózási szabály, amely akkor jelent jelentést, ha egy külső eszköz (például egy USB-kulcs) csatlakoztatva van a Azure Stack infrastruktúra egy csomópontjához. A napló a syslog használatával lett kibocsátva, és a **Microsoft-Windows-Security-audit: 6416 | Plug and Play események**. A syslog-ügyfél konfigurálásával kapcsolatos további információkért lásd: [syslog-továbbítás](azure-stack-integrate-security.md).

- A Azure Stack a belső tanúsítványok 4096 bites RSA-kulcsaira vált. A belső titkos kód futtatásával a régi 2048 bites tanúsítványokat a 4096 bites tanúsítványokkal helyettesíti. További információ a Azure Stack Secret rotációs szolgáltatásáról: [titkok Elforgatása Azure stack](azure-stack-rotate-secrets.md).

- A titkosítási algoritmusok és a kulcs erősségének frissítése több belső összetevő esetében, hogy megfeleljen a nemzetbiztonsági rendszerek Bizottságának – Policy 15 (CNSSP-15), amely ajánlott eljárásokat biztosít a nyilvános szabványok biztonságos használatához Információmegosztás. A tökéletesítések közé tartozik a Kerberos-hitelesítés AES256, valamint a SHA384 a VPN-titkosításhoz. A CNSSP-15-re vonatkozó további információkért tekintse [meg a nemzeti biztonsági rendszerek, szabályzatok oldalának Bizottságát](http://www.cnss.gov/CNSS/issuances/Policies.cfm).

- A fenti frissítés eredményeképpen a Azure Stack mostantól új alapértelmezett értékeket tartalmaz az IPsec/IKEv2 konfigurációkhoz. A Azure Stack oldalon használt új alapértelmezett értékek a következők:

   **IKE 1. fázis (fő módú) paraméterek**

   | Tulajdonság              | Value (Díj)|
   |-|-|
   | IKE verziószám           | IKEv2 |
   |Diffie-Hellman Group   | ECP384 |
   | Hitelesítési módszer | Előre megosztott kulcs |
   |Titkosító és kivonatoló algoritmus | AES256, SHA384 |
   |SA élettartama (Idő)     | 28 800 másodperc|

   **IKE 2. fázis (gyors mód) paraméterei**

   | Tulajdonság| Value (Díj)|
   |-|-|
   |IKE verziószám |IKEv2 |
   |Titkosítási & kivonatoló algoritmusok (titkosítás)     | GCMAES256|
   |Titkosítási & kivonatolási algoritmusok (hitelesítés) | GCMAES256|
   |SA élettartama (Idő)  | 27 000 másodperc  |
   |SA élettartama (kilobájt) | 33 553 408     |
   |Sérülés utáni titkosságvédelem (PFS) | ECP384 |
   |Kapcsolat megszakadásának észlelése | Támogatott|

   Ezek a változások az [alapértelmezett IPSec/IKE-javaslat](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters) dokumentációjában is megjelennek.

- Az infrastruktúra-biztonsági mentési szolgáltatás javítja a szükséges szabad lemezterületet a biztonsági mentésekhez, nem rögzített küszöbértékre való támaszkodás helyett. A szolgáltatás a külső tárolási hely biztonsági mentési, adatmegőrzési szabályzatának, fenntartásának és aktuális kihasználtságának méretét fogja használni annak megállapítására, hogy a rendszer figyelmeztetést kell-e kiemelni a működtető számára. 

### <a name="changes"></a>Változások

- Ha a Piactéri elemeket az Azure-ból Azure Stackba tölti le, a rendszer új felhasználói felületet biztosít, amely lehetővé teszi az elem verziójának megadását, ha több verzió is létezik. Az új felhasználói felület a csatlakoztatott és a leválasztott forgatókönyvekben is elérhető. További információ: [Marketplace-elemek letöltése az Azure-ból Azure stack](azure-stack-download-azure-marketplace-item.md).  

- Az 1910-es kiadástól kezdve a Azure Stack rendszernek további/20 belső IP-tárterületre **van szüksége** . Ez a hálózat a Azure Stack rendszer számára érhető el, és az adatközponton belül több Azure Stack rendszeren újra felhasználható. Amíg a hálózat privát Azure Stack, nem lehet átfedésben az adatközpontban található hálózattal. A/20 magánhálózati IP-terület több olyan hálózatra oszlik, amelyek lehetővé teszik a Azure Stack-infrastruktúra futtatását a tárolókban (az [1905 kibocsátási megjegyzésekben](release-notes.md?view=azs-1905)korábban említettek szerint). A Azure Stack infrastruktúra tárolókban való futtatásának célja a kihasználtság optimalizálása és a teljesítmény növelése. Emellett a/20 magánhálózati IP-terület is lehetővé teszi, hogy a folyamatban lévő erőfeszítéseket az üzembe helyezés előtt csökkentse a szükséges irányítható IP-területet.

  - Vegye figyelembe, hogy a/20 bemenet a 1910 után a következő Azure Stack frissítés előtti előfeltételként szolgál. Ha a következő Azure Stack frissítést a 1910-es kiadás után, és kísérlet történt a telepítésére, a frissítés meghiúsul, ha nem végezte el a/20 bemenetet a szervizelés lépéseiben leírtak szerint. A felügyeleti portálon riasztás jelenik meg, amíg a fenti szervizelési lépések be nem fejeződik. Az új privát terület felhasználásának megismeréséhez tekintse meg az [Datacenter hálózati integrációs](azure-stack-network.md#private-network) című cikket. 

  - Javítási lépések: a szervizeléshez kövesse az utasításokat a PEP- [munkamenet megnyitásához](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Készítse elő a (z)/20. [magánhálózati belső IP-címtartományt](azure-stack-network.md#logical-networks) , és futtassa a következő parancsmagot (csak 1910-től kezdődően érhető el) a PEP-munkamenetben a következő példa használatával: `Set-AzsPrivateNetwork -UserSubnet 100.87.0.0/20`. Ha a művelet sikeresen elvégezve, a rendszer a **konfigurációhoz hozzáadott belső hálózati AZS**üzenetet fogja kapni. Ha a művelet sikeresen befejeződött, a riasztás bezáródik a felügyeleti portálon. A Azure Stack rendszer most már frissíthető lesz a következő verzióra.
  
- Az infrastruktúra-biztonsági mentési szolgáltatás törli a részlegesen feltöltött biztonsági mentési adatok mennyiségét, ha a külső tárolóhely a feltöltési eljárás során elfogy a kapacitáson.  

- Az infrastruktúra-biztonsági mentési szolgáltatás a HRE üzemelő példányok biztonsági mentési hasznos adataihoz hozzáadja az Identity Service-t.  

- A AzureStack PowerShell-modul a 1910-es verzióra frissült a 1.8.0-es verziójára.<br>A módosítások a következők:
   - **Új DRP felügyeleti modul**: a telepítési erőforrás-szolgáltató (DRP) lehetővé teszi az erőforrás-szolgáltatók összehangolt központi telepítésének Azure stack. Ezek a parancsok a Azure Resource Manager réteget használják a DRP való kommunikációhoz.
   - **Brp**: <br />
           – Az Azure-beli verem-infrastruktúra biztonsági mentésének egyetlen szerepkör-visszaállítási funkciójának támogatása. <br />
           -`RoleName` paraméter hozzáadása a parancsmag `Restore-AzsBackup`hoz.
   - **FRP**: a **meghajtó** -és **kötet** -erőforrások a `2019-05-01`API-verzióval végzett módosításának megszakítása. A funkciókat a Azure Stack 1910-es és újabb verziói támogatják: <br />
            – A `ID`, `Name`, `HealthStatus` és `OperationalStatus` értékének módosítása megtörtént. <br />
            – Támogatott új tulajdonságok `FirmwareVersion`, `IsIndicationEnabled`, `Manufacturer`és `StoragePool` a **meghajtó** erőforrásaihoz. <br />
            – A **meghajtó** erőforrásainak `CanPool` és `CannotPoolReason` elavultak; Ehelyett használja a `OperationalStatus`.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Kijavítva egy olyan problémát, amely megakadályozta a TLS 1,2 házirend betartatását a Azure Stack 1904 kiadás előtt üzembe helyezett környezetekben.
- A probléma kijavítva, ha az SSH-hitelesítéssel létrehozott Ubuntu 18,04 virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez. 
- A **jelszó alaphelyzetbe állítása** a virtuálisgép-méretezési csoport felhasználói felületéről eltávolítva.
- Kijavítva a probléma, amelyben a terheléselosztó a portálról való törlése nem eredményezte az objektum az infrastruktúra-rétegből való törlését.
- Kijavított egy problémát, amely az átjáró-készlet kihasználtsági riasztásának pontatlan százalékát mutatta a felügyeleti portálon.
- Kijavított egy problémát, amelyben a virtuális gép ugyanazon hálózati adapterén található több nyilvános IP-cím hozzáadása internetkapcsolattal kapcsolatos problémákat eredményezett. A két nyilvános IP-címmel rendelkező hálózati adapternek most a várt módon kell működnie.

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack ezen frissítésében található biztonsági frissítésekről: [Azure stack biztonsági frissítések](release-notes-security-updates.md).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](known-issues.md)
- [Biztonsági frissítések](release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](release-notes-checklist.md)

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1910 frissítési csomagot [a Azure stack letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1908-es Azure Stack legújabb gyorsjavítást telepíti a 1910-es Azure Stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="prerequisites-before-applying-the-1910-update"></a>Előfeltételek: az 1910-es frissítés alkalmazása előtt

A Azure Stack 1910-es kiadását a 1908-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1908.11.47](https://support.microsoft.com/help/4535000)

### <a name="after-successfully-applying-the-1910-update"></a>Az 1910-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1910.8.76](https://support.microsoft.com/help/4534999)
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908-Build referenciája

A Azure Stack 1908 frissítési Build száma **1.1908.4.33**.

### <a name="update-type"></a>Frissítés típusa

A 1908 esetében a Azure Stack futtatását futtató operációs rendszer frissítve lett a Windows Server 2019-es verziójára. Ez lehetővé teszi az alapvető fontosságú fejlesztéseket, valamint a közeljövőben Azure Stack további képességeket is.

A Azure Stack 1908 frissítési Build típusa **megtelt**. Ennek eredményeképpen az 1908-es frissítés hosszabb futtatókörnyezettel rendelkezik, mint az expressz frissítések, például a 1906 és a 1907. A teljes frissítésekhez tartozó pontos futtatókörnyezetek általában a Azure Stack-példány által tartalmazott csomópontok számától függenek, a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha csatlakozik az internethez) és a rendszer hardvere Configuration. Az 1908-es frissítés a belső tesztelés következő várt futtatókörnyezeteit használta: 4 csomópont – 42 óra, 8 csomópont – 50 óra, 12 csomópont – 60 óra, 16 csomópont – 70 óra. A várt értéknél hosszabb ideig tartó frissítési futtatókörnyezetek nem szokatlanok, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure Stackban](azure-stack-updates.md).

- A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszer hardveres konfigurációja alapján használt kapacitástól függenek.
- A vártnál hosszabb ideig tartó futtatókörnyezetek nem szokatlanok, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul.
- Ez a futásidejű közelítés az 1908-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- 1908 esetében vegye figyelembe, hogy az alapul szolgáló operációs rendszer, amelyen Azure Stack fut, frissítve lett a Windows Server 2019-es verziójára. Ez lehetővé teszi az alapvető fontosságú fejlesztéseket, valamint a közeljövőben Azure Stack további képességeket is.
- Azure Stack infrastruktúra összes összetevője FIPS 140-2 módban működik.
- Azure Stack operátorok mostantól eltávolíthatják a portál felhasználói adatfájljait. További információ: [a portál felhasználói adatainak törlése Azure Stackról](azure-stack-portal-clear.md).

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->
- A Azure Stack Rest titkosítású adatainak fejlesztése a fizikai csomópontok hardveres platformmegbízhatósági modul (TPM) számára a titkok megőrzése érdekében.

### <a name="changes"></a>Változások

- A hardveres szolgáltatók a 2,1-es vagy újabb verziójú OEM-bővítményt a 1908-es verzióval megegyező Azure Stack időben szabadítják fel. Az OEM-bővítmény 2,1-es vagy újabb verziója a 1908-es Azure Stack-verzió előfeltétele. Az OEM-bővítmény 2,1-es vagy újabb verziójának letöltésével kapcsolatos további információkért forduljon a rendszer hardver-szolgáltatójához, és tekintse meg az [OEM-frissítések](azure-stack-update-oem.md#oem-contact-information) című cikket.  

### <a name="fixes"></a>Javítások

- A jövőbeli Azure Stack OEM-frissítésekkel való kompatibilitással és az ügyfél felhasználói lemezképeit használó virtuálisgép-telepítéssel kapcsolatos probléma javítva. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Kijavított egy problémát az OEM belső vezérlőprogram frissítésével, és javította a AzureStack a Fabric Ring Health szolgáltatásban. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Kijavított egy problémát az OEM belső vezérlőprogram frissítési folyamatával kapcsolatban. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack ezen frissítésében található biztonsági frissítésekről: [Azure stack biztonsági frissítések](release-notes-security-updates.md).

## <a name="download-the-update-1908"></a>A frissítés letöltése

A Azure Stack 1908 frissítési csomagot [a Azure stack letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1907-es Azure Stack legújabb gyorsjavítást telepíti a 1908-es Azure Stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Előfeltételek: az 1908-es frissítés alkalmazása előtt

A Azure Stack 1908-es kiadását a 1907-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1907.18.56](https://support.microsoft.com/help/4528552)

A Azure Stack 1908 frissítéshez **Azure stack OEM 2,1-es vagy újabb verzió** szükséges a rendszer hardver-szolgáltatójából. Az OEM-frissítések a Azure Stack rendszer hardverének illesztőprogram-és belső vezérlőprogram-frissítéseit tartalmazzák. További információ az OEM-frissítések alkalmazásáról: [Azure stack eredeti berendezésgyártó frissítéseinek alkalmazása](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>Az 1908-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1908.11.47](https://support.microsoft.com/help/4535000)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907-Build referenciája

A Azure Stack 1907 frissítési Build száma **1.1907.0.20**.

### <a name="update-type"></a>Frissítés típusa

A Azure Stack 1907 frissítési Build típusa **expressz**. További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack](azure-stack-updates.md) cikkben. A belső tesztelés alapján a 1907-es frissítés befejezéséhez szükséges idő körülbelül 13 óra.

- A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszer hardveres konfigurációja alapján használt kapacitástól függenek.
- A vártnál hosszabb ideig tartó futtatókörnyezetek nem szokatlanok, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul.
- Ez a futásidejű közelítés az 1907-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

## <a name="whats-in-this-update"></a>A frissítés ismertetése

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- A Azure Stack diagnosztikai napló gyűjtési szolgáltatásának általánosan elérhető kiadása a diagnosztikai naplók gyűjtésének megkönnyítéséhez és fejlesztéséhez. A Azure Stack diagnosztikai napló gyűjtése szolgáltatás lehetővé teszi a diagnosztikai naplók összegyűjtését és megosztását a Microsoft ügyfél-támogatási szolgálatával (CSS). Ez a diagnosztikai napló-gyűjtési szolgáltatás új felhasználói élményt nyújt a Azure Stack felügyeleti portálon, amely lehetővé teszi, hogy a kezelők automatikusan feltöltsék a diagnosztikai naplókat egy tárolási blobba, amikor bizonyos kritikus riasztások bekövetkeznek, vagy ugyanezt a műveletet hajtják végre. igény szerinti művelet. További információkért lásd a [diagnosztikai napló gyűjtését](azure-stack-diagnostic-log-collection-overview.md) ismertető cikket.

- A Azure Stack hálózati infrastruktúra ellenőrzésének általánosan elérhető kiadása az Azure Stack Validation Tool **test-AzureStack**részeként. Azure Stack hálózati infrastruktúra a **test-AzureStack**részét képezi, annak megállapításához, hogy hiba történik-e a Azure stack hálózati infrastruktúráján. A teszt ellenőrzi a hálózati infrastruktúra kapcsolatát, ha megkerüli a Azure Stack szoftver által meghatározott hálózatot. Egy nyilvános VIP kapcsolatát mutatja be a konfigurált DNS-továbbítókkal, az NTP-kiszolgálókkal és az identitás-végpontokkal. Emellett ellenőrzi az Azure-hoz való csatlakozást, ha az Azure AD-t az identitás-szolgáltatóként vagy az összevont kiszolgálóként használja az ADFS használatakor. További információkért tekintse meg a [Azure stack ellenőrzési eszközét](azure-stack-diagnostic-test.md) ismertető cikket.

- Egy belső titkos elforgatási eljárás lett hozzáadva a belső SQL TLS-tanúsítványok szükség szerinti elforgatásához a rendszer frissítése során.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- Az Azure Stack Update (frissítés) panel most megjeleníti az aktív frissítések **utolsó lépésének befejezési** idejét. Ezt úgy tekintheti meg, ha a frissítés panelre kattint, és rákattint egy futó frissítésre. Az **utolsó lépés befejeződött** , majd elérhető a **frissítés futtatása részletek** szakaszban.

- A **Start-AzureStack** és a **stop-AzureStack** operátor műveleteinek fejlesztése. Azure Stack elindításának ideje a 50%-os átlagos értékkel csökkent. A leállítási idő Azure Stack átlagosan 30%-kal csökkent. Az átlagos indítási és leállítási idők változatlanok maradnak, mint a csomópontok száma a méretezési egységekben.

- Javított hibakezelés a leválasztott piactér eszközhöz. Ha a letöltés sikertelen vagy részlegesen sikeres az **export-AzSOfflineMarketplaceItem**használatakor, a rendszer részletes hibaüzenetet jelenít meg, amely további részleteket tartalmaz a hibával és a kockázatcsökkentő lépésekkel kapcsolatban, ha van ilyen.

- Javította a felügyelt lemezek létrehozásának teljesítményét egy nagyméretű oldal blob/pillanatkép alapján. Korábban egy nagyméretű lemez létrehozásakor időtúllépést váltott ki.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Továbbfejlesztett virtuális lemez állapot-ellenőrzését a csomópont leállítása előtt, hogy elkerülje a virtuális lemezek váratlan leválasztását.

- A belső naplók továbbfejlesztett tárolása rendszergazdai műveletekhez. Ez a felügyeleti műveletek során jobb teljesítményt és megbízhatóságot eredményez azáltal, hogy minimalizálja a belső naplózási folyamatok memória-és tárterület-felhasználását. Emellett a felügyeleti portál frissítés paneljének továbbfejlesztett oldal betöltési idejét is megfigyelheti. Ennek a fejlesztésnek a részeként a 6 hónaposnál régebbi frissítési naplók többé nem lesznek elérhetők a rendszeren. Ha a frissítések esetében naplókra van szüksége, ügyeljen arra, hogy az 1907-es frissítés végrehajtása előtt [töltse le az](azure-stack-apply-updates.md) összes frissítési művelet összefoglalóját 6 hónapnál régebbi verzióra.

### <a name="changes"></a>Változások

- Az 1907-es verzió olyan figyelmeztető riasztást tartalmaz, amely arra utasítja a kezelőket, hogy az 1908-as verzióra való frissítés előtt frissítse a rendszer OEM-csomagját a 2,1-es vagy újabb verzióra. Azure Stack Az Azure Stack OEM-frissítések alkalmazásával kapcsolatos további információkért lásd: [Azure stack eredeti berendezésgyártó frissítésének alkalmazása](azure-stack-update-oem.md).

- Új kimenő szabály (HTTPS) lett hozzáadva a Azure Stack diagnosztikai napló-gyűjtési szolgáltatás kommunikációjának engedélyezéséhez. További információ: [Azure stack Datacenter Integration – közzétételi végpontok](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Az infrastruktúra-biztonsági mentési szolgáltatás törli a részlegesen feltöltött biztonsági mentéseket, ha a külső tárolóhely kapacitása elfogy.

- Az infrastruktúra biztonsági mentései már nem tartalmazzák a tartományi szolgáltatások adatai biztonsági mentését. Ez csak azokra a rendszerekre vonatkozik, amelyek a Azure Active Directory használják az identitás-szolgáltatóként.

- Most ellenőrizjük, hogy egy, a **számítási > virtuálisgép-lemezképek** panelén betöltött kép oldal blob típusú.

- A Kiemelt jogosultságú Endpoint parancs **set-BmcCredential** most frissíti a hitelesítő adatokat a alaplapi felügyeleti vezérlőben.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Kijavított egy olyan problémát, amelyben a közzétevőt, az ajánlatot és az SKU-t egy Resource Manager-sablonban bizalmasként kezeli a rendszer: a rendszerkép nem lett beolvasva a központi telepítéshez, kivéve, ha a képparaméterek nem egyeznek meg a közzétevő, az ajánlat és az SKU esetében.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- A tárolási szolgáltatás metaadatainak biztonsági mentésének időtúllépése miatt nem sikerült a **PartialSucceeded** hibaüzenettel rendelkező biztonsági mentések hibájának kijavítása.  

- Kijavítva egy probléma, amelyben a felhasználói előfizetések törlése árva erőforrásokat eredményezett.

- Kijavított egy olyan problémát, amelyben a leírás mező nem lett mentve az ajánlat létrehozásakor.

- Kijavítva egy probléma, amelyben a **csak olvasási** jogosultsággal rendelkező felhasználók létrehozhatják, szerkeszthetik és törölhetik az erőforrásokat. Most a felhasználó csak akkor tud erőforrásokat létrehozni, ha a **közreműködői** engedély hozzá van rendelve. 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- Kijavítva egy olyan problémát, amely miatt a frissítés sikertelen volt, mert a WMI-szolgáltató gazdagépe zárolta a DLL-fájlt.

- Kijavított egy problémát a frissítési szolgáltatásban, amely megakadályozta az elérhető frissítések megjelenítését a frissítés csempén vagy erőforrás-szolgáltatón. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4511282](https://support.microsoft.com/help/4511282/).

- Kijavítva egy olyan problémát, amely miatt a frissítés sikertelen volt, mert a felügyeleti sík helytelen konfiguráció miatt nem megfelelő állapotba kerül. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4512794](https://support.microsoft.com/help/4512794/).

- Kijavított egy problémát, amely megakadályozza, hogy a felhasználók elvégezzék a harmadik féltől származó rendszerképek üzembe helyezését a piactéren. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4511259](https://support.microsoft.com/help/4511259/).

- Kijavítva egy olyan problémát, amely miatt a virtuális gép létrehozása a felügyelt lemezképből sikertelen volt a felhasználói rendszerkép-kezelő szolgáltatás összeomlása miatt. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4512794](https://support.microsoft.com/help/4512794/)

- Kijavítva egy probléma, amelyben a virtuális gépek szifilisz-műveletei sikertelenek voltak, mert az App Gateway gyorsítótára nem a várt módon frissült. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4513119](https://support.microsoft.com/en-us/help/4513119/)

- Kijavított egy problémát az állapot-erőforrás-szolgáltatóban, amely hatással volt a régió és a riasztási panel elérhetőségére a felügyeleti portálon. Ez a probléma a 1906-es verzióban található, és javítva lett a gyorsjavítások [KB4512794](https://support.microsoft.com/help/4512794).

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack ezen frissítésében található biztonsági frissítésekről: [Azure stack biztonsági frissítések](release-notes-security-updates.md).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](known-issues.md)
- [Biztonsági frissítések](release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](release-notes-checklist.md)

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1907 frissítési csomagot [a Azure stack letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1906-es Azure Stack legújabb gyorsjavítást telepíti a 1907-es Azure Stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="before-applying-the-1907-update"></a>Az 1907-es frissítés alkalmazása előtt

A Azure Stack 1907-es kiadását a 1906-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Az 1907-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1907.18.56](https://support.microsoft.com/help/4528552)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>1906-Build referenciája

A Azure Stack 1906 frissítési Build száma **1.1906.0.30**.

### <a name="update-type"></a>Frissítés típusa

A Azure Stack 1906 frissítési Build típusa **expressz**. További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack](azure-stack-updates.md) cikkben. Az 1906-es frissítés befejezéséhez szükséges idő körülbelül 10 óra, a Azure Stack-környezetben található fizikai csomópontok számától függetlenül. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek. A várt értéknél hosszabb ideig tartó futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul. Ez a futásidejű közelítés az 1906-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

## <a name="whats-in-this-update"></a>A frissítés ismertetése

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Egy **set-TLSPolicy** parancsmagot adott hozzá a privilegizált végponthoz (PEP) a TLS 1,2 kényszerítéséhez az összes végponton. További információ: [Azure stack biztonsági vezérlők](azure-stack-security-configuration.md).

- Hozzáadott egy **Get-TLSPolicy** parancsmagot a Privileged ENDPOINT (PEP) alkalmazásban az alkalmazott TLS-szabályzat lekéréséhez. További információ: [Azure stack biztonsági vezérlők](azure-stack-security-configuration.md).

- Belső titkos elforgatási eljárás hozzáadva a belső TLS-tanúsítványok rendszerfrissítés során történő elforgatásához.

- A belső titkokat a belső titkok kiváltásának megakadályozása érdekében hozzáadott egy olyan védelmet, amely miatt a rendszer figyelmen kívül hagyja a kritikus riasztást a lejáró titkos kulcsok esetében. Ez nem használható rendszeres működési eljárásként. A titkok rotációját a karbantartási időszakban kell tervezni. További információ: [Azure stack Secret rotációs](azure-stack-rotate-secrets.md).

- A Visual Studio Code mostantól támogatja a Azure Stack a AD FS használatával történő üzembe helyezést.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- A privilegizált végpont **Get-GraphApplication** parancsmagja mostantól megjeleníti az aktuálisan használt tanúsítvány ujjlenyomatát. Ez javítja az egyszerű szolgáltatásnév felügyeletét, ha a Azure Stack AD FStal van üzembe helyezve.

- Új állapot-figyelési szabályok lettek hozzáadva az AD Graph és a AD FS rendelkezésre állásának ellenőrzéséhez, beleértve a riasztások megadásának lehetőségét is.

- A biztonsági mentési erőforrás-szolgáltató megbízhatóságának fejlesztése, ha az infrastruktúra-mentési szolgáltatás egy másik példányra kerül át.

- A külső titkos rotációs eljárás teljesítményének optimalizálása egy egységes végrehajtási idő biztosításához a karbantartási időszak ütemezésének megkönnyítése érdekében.

- A **test-AzureStack** parancsmag mostantól jelentést készít a hamarosan lejáró belső titkokról (kritikus riasztások).

- Új paraméter érhető el a (z) **CustomAdfs** parancsmaghoz a privilegizált végponton, amely lehetővé teszi a visszavont tanúsítványok listájának ellenőrzését a AD FS összevonási megbízhatóságának konfigurálásakor.

- Az 1906-es kiadás nagyobb láthatóságot mutat be a frissítés folyamatában, így biztos lehet benne, hogy a frissítések nem szünetelnek. Ez a **frissítés** panelen megjelenő frissítési lépések teljes számának növekedését eredményezi. Előfordulhat, hogy a korábbi frissítéseknél párhuzamosan további frissítési lépések is előfordulhatnak.

#### <a name="networking-updates"></a>Hálózatkezelési frissítések

- A DHCP-válaszadóban beállított címbérleti idő konzisztens az Azure-ban.

- Továbbfejlesztett újrapróbálkozási díjak az erőforrás-szolgáltatónak az erőforrások sikertelen üzembe helyezése esetén.

- A **standard** SKU lehetőség el lett távolítva a terheléselosztó és a nyilvános IP-cím közül, mivel ez jelenleg nem támogatott.

### <a name="changes"></a>Változások

- A Storage-fiókokkal kapcsolatos felhasználói élmény mostantól konzisztens az Azure-ban.

- Módosított riasztási eseményindítók a belső titkok lejáratához:
  - A figyelmeztetési riasztások mostantól 90 nappal a titkok lejárta előtt jelennek meg.
  - A kritikus riasztások mostantól 30 nappal a titkok lejárta előtt jelennek meg.

- Frissített karakterláncok az infrastruktúra biztonsági mentési erőforrás-szolgáltatójában konzisztens terminológiához.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Kijavított egy problémát, amelyben a felügyelt lemezes virtuális gép átméretezése egy **belső működési hiba**miatt meghiúsult.

- Kijavítva a hiba, amikor egy sikertelen felhasználói lemezkép létrehozásakor a lemezképet kezelő szolgáltatás helytelen állapotban van. Ez blokkolja a sikertelen lemezkép törlését és az új rendszerképek létrehozását. Ezt a 1905-es gyorsjavítás is rögzíti.

- A belső titkos kulcsok sikeres végrehajtása után a rendszer mostantól automatikusan bezárja az aktív riasztásokat a lejáró belső titkokon.

- Kijavítva egy probléma, amelyben a frissítés időtartama a frissítési Előzmények lapon az első számjegyet fogja kimetszeni, ha a frissítés több mint 99 órán keresztül futott.

- A **frissítés** panel tartalmaz egy **folytatási** lehetőséget a sikertelen frissítésekhez.

- A rendszergazda és a felhasználói portálon kijavíthatja a problémát a piactéren, amelyben a Docker-bővítmény helytelenül tért vissza a keresésből, de nem végezhető el további művelet, mivel Azure Stack nem érhető el.

- Kijavított egy problémát a sablon telepítési felhasználói felületén, amely nem adja meg a paramétereket, ha a sablon neve _ aláhúzással kezdődik.

- Kijavítva a probléma, hogy a virtuálisgép-méretezési csoport létrehozása során a CentOS-alapú 7,2-es verzió üzembe helyezési lehetőséget biztosít. A CentOS 7,2 Azure Stackon nem érhető el. Mostantól a CentOS 7,5-es verzióval biztosítjuk az üzembe helyezési lehetőséget

- Mostantól eltávolíthat egy méretezési csoportot a **virtuálisgép-méretezési** csoportok paneljéről.

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack ezen frissítésében található biztonsági frissítésekről: [Azure stack biztonsági frissítések](release-notes-security-updates.md).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](known-issues.md)
- [Biztonsági frissítések](release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](release-notes-checklist.md)

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1906 frissítési csomagot [a Azure stack letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1905-es Azure Stack legújabb gyorsjavítást telepíti a 1906-es Azure Stack frissítése előtt. A frissítés után telepítse a [1906 összes elérhető gyorsjavítását](#after-successfully-applying-the-1906-update).

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="before-applying-the-1906-update"></a>Az 1906-es frissítés alkalmazása előtt

A Azure Stack 1906-es kiadását a 1905-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Az 1906-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1906.15.60](https://support.microsoft.com/help/4524559)
::: moniker-end

::: moniker range=">=azs-1906"
## <a name="automatic-update-notifications"></a>Automatikus frissítési értesítések

Az infrastruktúra-hálózatról az internetre elérő rendszerek az **elérhető frissítés** üzenetét fogják látni az operátori portálon. Az internet-hozzáféréssel nem rendelkező rendszerek a megfelelő. xml fájllal tölthetik le és importálhatók a. zip fájlt.

> [!TIP]  
> Feliratkozás a következő *RSS* -vagy *Atom* -hírcsatornára Azure stack gyorsjavítások megtartásához:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archívum

Ha egy régebbi verzióhoz szeretne hozzáférni az archivált kibocsátási megjegyzésekhez, használja a bal oldalon a tartalomjegyzék legördülő menüjét, és válassza ki a megjeleníteni kívánt verziót.

## <a name="next-steps"></a>Következő lépések

- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](azure-stack-updates.md).  
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](azure-stack-apply-updates.md).
- A Azure Stack integrált rendszerek karbantartási szabályzatának áttekintéséhez, valamint arról, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon, tekintse meg [Azure stack karbantartási házirendet](azure-stack-servicing-policy.md).  
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1905"
## <a name="1905-archived-release-notes"></a>1905 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>1904 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>1903 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>1902 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>1901 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>1811 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>1809 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>1808 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>1807 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>1805 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>1804 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>1803 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>1802 archivált kibocsátási megjegyzések
::: moniker-end

::: moniker range="<azs-1906"
[A Azure stack kibocsátási megjegyzések régebbi verzióit a TechNet Gallery webhelyen](https://aka.ms/azsarchivedrelnotes)érheti el. Ezek az archivált dokumentumok kizárólag referencia jellegűek, és nem jelentenek támogatást ezekhez a verziókhoz. További információ a Azure Stack támogatásáról: [Azure stack karbantartási szabályzat](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálatához.
::: moniker-end


