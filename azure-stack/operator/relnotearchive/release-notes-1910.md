---
title: Azure Stack hub 1910 kibocsátási megjegyzései
description: 1910 kibocsátási megjegyzések a Azure Stack hub integrált rendszereihez, beleértve a frissítéseket és hibajavításokat.
author: sethmanheim
ms.topic: article
ms.date: 01/25/2021
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ROBOTS: NOINDEX
ms.openlocfilehash: ced055a2983fb4aabb1c31f314ccb4587f3801cc
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248307"
---
# <a name="azure-stack-hub-1910-release-notes"></a>Azure Stack hub 1910 kibocsátási megjegyzései

Ez a cikk Azure Stack hub frissítési csomagjainak tartalmát ismerteti. A frissítés az Azure Stack hub legújabb kiadásának javításait és javításait is tartalmazza.

> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack hub integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kitra (ASDK).

> [!IMPORTANT]  
> Ha a Azure Stack hub-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](../release-notes-checklist.md)
- [Ismert problémák](../known-issues.md)
- [Gyorsjavítások](#hotfixes)
- [Biztonsági frissítések](../release-notes-security-updates.md)

A frissítésekkel és a frissítési folyamattal kapcsolatos segítségért lásd: [a Azure stack hub javításával és frissítésével kapcsolatos hibák elhárítása](../azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>A frissítés letöltése

Az Azure Stack hub frissítési csomagot [az Azure stack hub Update Downloader Tool](https://aka.ms/azurestackupdatedownload)használatával töltheti le.

## <a name="1910-build-reference"></a>1910-Build referenciája

Az Azure Stack hub 1910 Update Build száma **1.1910.0.58**.

### <a name="update-type"></a>Frissítéstípus

Az 1908-es verziójától kezdve a mögöttes operációs rendszer, amelyen Azure Stack hub fut, a Windows Server 2019-re frissült. Ez a frissítés lehetővé teszi az alapszintű fejlesztéseket és az Azure Stack hub további képességeinek kihasználását.

Az Azure Stack hub 1910 Update Build típusa **expressz**.

Az 1910-es frissítési csomag mérete nagyobb a korábbi frissítésekhez képest, ami hosszabb letöltési időt eredményez. A frissítés hosszú ideig az **előkészítési** állapotban marad, és a kezelők elvárhatják, hogy ez a folyamat továbbra is a korábbi frissítéseknél tovább tartson. Az 1910-es frissítés befejezésének várható időpontja körülbelül 10 óra, az Azure Stack hub-környezetben található fizikai csomópontok számától függetlenül. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek. A várt értéknél hosszabb ideig tartó futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek műveletet Azure Stack hub-operátorok, kivéve, ha a frissítés meghiúsul. Ez a futtatókörnyezeti közelítés az 1910-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack hub-frissítésekkel.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack hub-ban](../azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- A felügyeleti portál mostantól megjeleníti a Kiemelt végpontok IP-címeit a régió tulajdonságok menüjében, amely megkönnyíti a felderítést. Emellett megjeleníti az aktuálisan konfigurált időkiszolgálót és a DNS-továbbítókat. További információkat [a kiemelt végpont Azure Stack Hubbeli használatát ismertető részben](../azure-stack-privileged-endpoint.md) talál.

- A Azure Stack hub állapot-és monitorozási rendszere mostantól hibát okozhat a különböző hardver-összetevőkön. Ezek a riasztások további konfigurálást igényelnek. További információ: [Azure stack hub hardver-összetevők figyelése](../azure-stack-hardware-monitoring.md).

- [Cloud-init támogatás az Azure stack hub számára](/azure/virtual-machines/linux/using-cloud-init): a Cloud-init egy széles körben használt módszer a Linux RENDSZERű virtuális gépek első indításához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a Cloud-init a kezdeti rendszerindítási folyamat során hívásra kerül, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához. A piactéren lévő Ubuntu-lemezképek frissítve lettek a Cloud-init támogatásához a kiépítés érdekében.

- Az Azure Stack hub mostantól támogatja az összes Windows Azure Linux-ügynök verzióját az Azure-ban.

- A Azure Stack hub rendszergazdai PowerShell-moduljainak új verziója érhető el. <!-- For more information, see -->

- A Azure Stack hub új Azure PowerShell bérlői moduljait 2020. április 15-én adták ki. A jelenleg használt Azure RM-modulok továbbra is működni fognak, de az 2002-es Build után már nem lesznek frissítve.

- Hozzáadta a **set-AzSDefenderManualUpdate** parancsmagot az emelt szintű végponthoz (PEP) a Windows Defender-definíciók manuális frissítésének konfigurálásához az Azure stack hub-infrastruktúrában. További információ: [a Windows Defender víruskereső frissítése Azure stack hub-on](../azure-stack-security-av.md).

- Hozzáadta a **set-AzSDnsForwarder** parancsmagot a Kiemelt jogosultságú végponthoz (PEP) a Azure stack hub DNS-kiszolgálóinak továbbító beállításainak módosításához. A DNS-konfigurációval kapcsolatos további információkért lásd: [Azure stack hub Datacenter DNS-integráció](../azure-stack-integrate-dns.md).

- A **Kubernetes-fürtök** az [AK-motor](../../user/azure-stack-kubernetes-aks-engine-overview.md)használatával történő felügyeletének támogatása. Ettől a frissítéstől kezdve az ügyfelek üzembe helyezhetik a termelési Kubernetes-fürtöket. Az AK-motor lehetővé teszi a felhasználók számára a következőket:
  - Kezelheti a Kubernetes-fürtök életciklusát. Létrehozhatják, frissíthetik és méretezhetik a fürtöket.
  - A fürtöket az AK és a Azure Stack hub csapatai által készített felügyelt lemezképek használatával kezelheti.
  - Használjon olyan Azure Resource Manager integrált Kubernetes-szolgáltatót, amely natív Azure-erőforrásokkal rendelkező fürtöket hoz létre.
  - Fürtök üzembe helyezése és kezelése csatlakoztatott vagy leválasztott Azure Stack hub-bélyegzővel.
  - Azure Hybrid-funkciók használata:
    - Integráció az Azure Arcmal.
    - Integráció a Azure Monitor for containers szolgáltatással.
  - Használjon Windows-tárolókat az AK-motorral.
  - Microsoft ügyfélszolgálata és mérnöki támogatás fogadása az üzemelő példányokhoz.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- Az Azure Stack hub javította a frissítési hibák vagy a megakadályozta, hogy az operátorok egy Azure Stack hub-frissítés kezdeményezésének elindításához szükséges javítási és frissítési problémákat. Ennek eredményeképpen a **test-AzureStack-UpdateReadiness** csoport kevesebb tesztet tartalmaz. További információ: [Azure stack hub rendszer állapotának ellenőrzése](../azure-stack-diagnostic-test.md#groups). A következő három teszt marad a **UpdateReadiness** csoportban:

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Olyan naplózási szabályt adott hozzá, amely azt jelenti, hogy egy külső eszköz (például egy USB-kulcs) csatlakoztatva van az Azure Stack hub-infrastruktúra egy csomópontjához. A napló a syslog használatával lett kibocsátva, és a **Microsoft-Windows-Security-audit: 6416 | Plug and Play események**. A syslog-ügyfél konfigurálásával kapcsolatos további információkért lásd: [syslog-továbbítás](../azure-stack-integrate-security.md).

- Azure Stack hub a belső tanúsítványok 4096 bites RSA-kulcsaira vált. A belső titkos kód futtatásával a régi 2048 bites tanúsítványokat a 4096 bites tanúsítványokkal helyettesíti. További információ a Azure Stack hub Secret rotációs szolgáltatásáról: [titkok Elforgatása Azure stack központban](../azure-stack-rotate-secrets.md).

- A titkosítási algoritmusok és a kulcs erősségének frissítése több belső összetevő esetében, hogy megfeleljen a nemzetbiztonsági rendszerek Bizottságának – Policy 15 (CNSSP-15), amely ajánlott eljárásokat biztosít a nyilvános szabványok biztonságos információk megosztására való használatához. A tökéletesítések közé tartozik a Kerberos-hitelesítés és a VPN-titkosítás SHA384 AES256. A CNSSP-15-re vonatkozó további információkért tekintse [meg a nemzeti biztonsági rendszerek, szabályzatok oldalának Bizottságát](http://www.cnss.gov/CNSS/issuances/Policies.cfm).

- A fenti frissítés miatt Azure Stack hub mostantól új alapértelmezett értékeket tartalmaz az IPsec/IKEv2 konfigurációkhoz. Az Azure Stack hub oldalon használt új alapértelmezett értékek a következők:

   **Az IKE 1. fázis (Elsődleges mód) paraméterei**

   | Tulajdonság              | Érték|
   |-|-|
   | IKE verziószám           | IKEv2 |
   |Diffie-Hellman Group   | ECP384 |
   | Hitelesítési módszer | Előre megosztott kulcs |
   |Titkosító és kivonatoló algoritmus | AES256, SHA384 |
   |SA élettartama (Idő)     | 28 800 másodperc|

   **Az IKE 2. fázis (Gyors mód) paraméterei**

   | Tulajdonság| Érték|
   |-|-|
   |IKE verziószám |IKEv2 |
   |Titkosítási & kivonatoló algoritmusok (titkosítás)     | GCMAES256|
   |Titkosítási & kivonatolási algoritmusok (hitelesítés) | GCMAES256|
   |SA élettartama (Idő)  | 27 000 másodperc  |
   |SA élettartama (kilobájt) | 33 553 408     |
   |Sérülés utáni titkosságvédelem (PFS) | ECP384 |
   |Kapcsolat megszakadásának észlelése | Támogatott|

   Ezek a változások az [alapértelmezett IPSec/IKE-javaslat](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters) dokumentációjában is megjelennek.

- Az infrastruktúra-biztonsági mentési szolgáltatás javítja a logikát, amely kiszámítja a biztonsági mentések kívánt szabad területét, ahelyett, hogy rögzített küszöbértékre kellene támaszkodnia. A szolgáltatás a külső tárolási hely biztonsági mentési, adatmegőrzési szabályzatának, fenntartásának és aktuális kihasználtságának méretét fogja használni annak megállapítására, hogy egy figyelmeztetést kell-e kiemelni a kezelőnek.

### <a name="changes"></a>Módosítások

- A Piactéri elemek Azure-ból Azure Stack hubhoz való letöltésekor új felhasználói felület áll rendelkezésre, amely lehetővé teszi az elem egy verziójának megadását, ha több verzió létezik. Az új felhasználói felület a csatlakoztatott és a leválasztott forgatókönyvekben is elérhető. További információ: [Marketplace-elemek letöltése az Azure-ból Azure stack hubhoz](../azure-stack-download-azure-marketplace-item.md).  

- Az 1910-es kiadástól kezdve a Azure Stack hub rendszernek további/20 magánhálózati belső IP-tárterületre **van szüksége** . További információért lásd: [a hálózati integráció megtervezése Azure stack](../azure-stack-network.md) .
  
- Az infrastruktúra-biztonsági mentési szolgáltatás törli a részlegesen feltöltött biztonsági mentési adatok mennyiségét, ha a külső tárolóhely a feltöltési eljárás során elfogy a kapacitáson.  

- Az infrastruktúra-biztonsági mentési szolgáltatás hozzáadja az Identity Service-t a HRE-környezetek biztonsági mentési hasznos adataihoz.  

- Az Azure Stack hub PowerShell-modul a 1910-es kiadásban a 1.8.0-es verzióra frissült.<br>A változások a következők:
   - **Új DRP adminisztrációs modul**: Az üzembehelyezési erőforrás-szolgáltató (Deployment Resource Provider, DRP) lehetővé teszi az erőforrás-szolgáltatók az Azure Stack Hubban való vezényelt üzembe helyezését. Ezek a parancsok az Azure Resource Manager rétegén keresztül kezelik a DRP-t.
   - **BRP**: <br />
           – Az Azure-beli verem-infrastruktúra biztonsági mentésének egyetlen szerepkör-visszaállítási funkciójának támogatása. <br />
           -Paraméter hozzáadása `RoleName` a parancsmaghoz `Restore-AzsBackup` .
   - **FRP**: a **meghajtó** -és **kötet** -erőforrások a következő API-verzióval történő megszakítása `2019-05-01` . A funkciókat az Azure Stack Hub 1910-es és újabb verziói támogatják: <br />
            – A, a `ID` és a érték `Name` `HealthStatus` `OperationalStatus` módosult. <br />
            – Támogatott új tulajdonságok `FirmwareVersion` ,,, `IsIndicationEnabled` `Manufacturer` és `StoragePool` a **meghajtó** erőforrásaihoz. <br />
            – A tulajdonságok `CanPool` és `CannotPoolReason` a **meghajtó** erőforrásai elavultak; használja `OperationalStatus` helyette.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

- Kijavítva egy olyan problémát, amely megakadályozta a TLS 1,2 házirend betartatását az Azure Stack hub 1904 kiadása előtt üzembe helyezett környezetekben.
- A probléma kijavítva, ha az SSH-hitelesítéssel létrehozott Ubuntu 18,04-es virtuális gép nem teszi lehetővé az SSH-kulcsok használatát a bejelentkezéshez.
- A **jelszó alaphelyzetbe állítása** a virtuálisgép-méretezési csoport felhasználói felületéről eltávolítva.
- Kijavítva a probléma, amelyben a terheléselosztó törlése a portálról nem eredményezte az objektum törlését az infrastruktúra rétegében.
- Kijavított egy problémát, amely az átjáró-készlet kihasználtsági riasztásának pontatlan százalékát mutatta a felügyeleti portálon.
<!-- Fixed an issue where adding more than one public IP on the same NIC on a Virtual Machine resulted in internet connectivity issues. Now, a NIC with two public IPs should work as expected.[This fix actually didn't go in 1910 due to build issues, commenting out until next build (2002) ] -->

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack hub ezen frissítésében található biztonsági frissítésekről: [Azure stack hub biztonsági frissítései](../release-notes-security-updates.md).

A kiadás Qualys biztonsági réseit tartalmazó jelentés letölthető a [Qualys webhelyről](https://www.qualys.com/azure-stack/).

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack hub rendszeresen bocsát ki gyorsjavításokat. Győződjön meg arról, hogy a 1908-es legújabb Azure Stack hub gyorsjavítását telepíti a 1910-es Azure Stack hub frissítése előtt.

> [!NOTE]
> Azure Stack hub gyorsjavítási kiadásai kumulatívak; a legújabb gyorsjavítást csak akkor kell telepíteni, ha az adott verzióhoz tartozó korábbi gyorsjavítások összes javítását beolvassa.

Azure Stack hub-gyorsjavítások csak Azure Stack hub integrált rendszerekre érvényesek; Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="prerequisites-before-applying-the-1910-update"></a>Előfeltételek: az 1910-es frissítés alkalmazása előtt

Azure Stack hub 1910-es kiadását a 1908-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1908.51.133](https://support.microsoft.com/help/4574734)

### <a name="after-successfully-applying-the-1910-update"></a>Az 1910-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1910.63.186](https://support.microsoft.com/help/4574735)
