---
title: Az Azure Stack-telemetria |} A Microsoft Docs
description: Ismerteti a PowerShell-lel az Azure Stack telemetriai beállításainak konfigurálása.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: jeffgilb
ms.reviewer: comartin
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: ce77e27cf6d7be70cb3ff4b9be0e0366beca0498
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984899"
---
# <a name="azure-stack-telemetry"></a>Az Azure Stack-telemetria

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack-telemetria automatikusan tölt fel adatokat a rendszer a Microsoft csapatához, a kapcsolódó felhasználói élményt. A Microsoft teams használja az adatok, amelyek az Azure Stack telemetriai adatokat gyűjt a felhasználói élmény javítása érdekében. Ezeket az adatokat a biztonsági, egészségügyi, minőségi és Teljesítményelemzés is szolgál.

Az Azure Stack operátorait, a telemetriai nyújthat értékes betekintést az vállalati üzemelő példánya, és a egy hang, amely segít az Azure Stack későbbi verzióiban alakzat biztosít.

> [!NOTE]
> Azure Stack használati adatok továbbítása az Azure-bA a számlázási is konfigurálhatja. Ez azért szükséges, a több csomópontos Azure Stack ajánljuk, akik a számlázás használatalapú mint-akkor-használható. Használati jelentések szabályozott egymástól függetlenül telemetriai adatokból, és, nem szükséges, több csomópontos ajánljuk, akik a kapacitás modellt vagy az Azure Stack Development Kit felhasználókhoz. Ebben az esetben használati jelentések is ki legyen kapcsolva [a regisztrációs szkripttel](azure-stack-usage-reporting.md).

A Windows Server 2016 csatlakoztatott felhasználói felület és a Telemetria összetevőt, amely használja az Azure Stack-telemetria alapján a [esemény-nyomkövetése Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging technológia segítségével gyűjtse össze és tárolja az eseményeket és az adatok. Az Azure Stack-összetevők események és a nyilvános operációs rendszer eseménynaplózás és nyomon követése az API-k használatával gyűjtött adatokat közzé ugyanazt a technológiát használja. Az Azure Stack-összetevők közé e szolgáltatók: Hálózati erőforrás, a Storage-erőforrások, a Monitoring erőforrás és erőforrás frissítése. A csatlakoztatott felhasználói élmény és a Telemetria összetevő az adatok SSL használatával titkosítja, és használja a tanúsítvány rögzítését HTTPS-kapcsolaton keresztül adatokat továbbítson a Microsoft Data Management szolgáltatáshoz.

> [!IMPORTANT]
> Ahhoz, hogy a telemetria-adatfolyam, 443-as (HTTPS) nyitva a hálózaton kell lennie. A csatlakoztatott felhasználói élmény és a Telemetria összetevő csatlakozik a Microsoft adatkezelési szolgáltatás https://v10.vortex-win.data.microsoft.com. A csatlakoztatott felhasználói élmény és a Telemetria összetevő is csatlakozik https://settings-win.data.microsoft.com konfigurációs információk letöltése.

## <a name="privacy-considerations"></a>Adatvédelmi szempontok

Az ETW-szolgáltatás telemetriai adatokat vissza a védett tárolási irányítja. A legalacsonyabb jogosultsági egyszerű hozzáférés végigvezeti a telemetriai adatokat. Csak a Microsoft munkatársainak az érvényes üzleti igények kapjanak hozzáférést a telemetriai adatokat. A Microsoft nem személyes vásárlói adatokat harmadik féllel kivételével az ügyfél belátása vagy megosztása leírt korlátozott céljából a [Microsoft adatvédelmi nyilatkozatát](https://privacy.microsoft.com/PrivacyStatement). Üzleti az OEM-EK és megosztott jelentések közé tartozik a összesített, anonimizált adatok. Adatok megosztása döntések egy belső, többek között az adatok, jogi és adatvédelmi felügyeleti résztvevőket a Microsoft csapata által végzett.

A Microsoft szerint a, és információkat minimalizálásával eljárásokat. Igyekszünk van szükség, és tárolja a csak addig, amíg szükség ahhoz, hogy egy szolgáltatás vagy az elemzési adatok összegyűjtéséhez. Hogyan működnek az Azure Stack rendszer és az Azure-szolgáltatásokkal kapcsolatos információk nagy részét hat hónapon belül törlődik. Összegezve, vagy hosszabb ideig összesített adatokat tárolni fogja.

Tisztában vagyunk vele, hogy az ügyfél-információk biztonsága és védelme fontos.  A Microsoft adatvédelem és a vásárlói adatokat az Azure Stackben védelméről a példákon és átfogó megközelítés vesz igénybe. A rendszergazdák funkciókat és adatvédelmi beállítások testreszabása bármikor vezérlők rendelkezik. Elkötelezettségünk, átláthatóság és a megbízhatósági kapcsolat a következő törlése:

- Sajnáljuk, nyissa meg az ügyfeleinek, hogy gyűjtse össze az adattípusokat.
- Vezérlőelem tárgyaljuk, a vállalati felhasználók – szabhatják testre a saját adatvédelmi beállítások.
- Adatvédelem és biztonság először tárgyaljuk.
- Sajnáljuk, átlátható kapcsolatos telemetriai adatokat lekérdezi.
- Telemetriai adatok felhasználói élmény javítására használjuk.

A Microsoft nem kíván a bizalmas adatokat, például hitelkártyaszámokat tartalmazó, felhasználónevek és jelszavak, e-mail-címeket vagy hasonló bizalmas adatokat gyűjteni. Ha azt állapítja meg, hogy bizalmas adatok véletlenül érkezett, hogy törölje azt.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Példák a telemetriai adatok Microsoft általi használatára

Telemetria abban, hogy gyorsan azonosíthatja és javítsa ki a kritikus fontosságú megbízhatósággal kapcsolatos problémákat, az ügyfél központi telepítések és konfigurációk fontos szerepet játszik. Származó telemetriai adatok segítségével azonosíthatja a problémákat szolgáltatások vagy hardverkonfigurációk. A Microsoft lehessen az adatok ügyfelek és a meghajtó fejlesztései a rendszerre, az integrált Azure Stack megoldásokat a minőség sáv vet fel.

Telemetria segít jobban megérteni, hogyan ügyfelek összetevők üzembe helyezése, funkciók és szolgáltatások használata az üzleti céljainak elérését a Microsoft is. Ezen elemzési segítségével rangsorolhatja a területeken, amelyek közvetlen hatással lehet az ügyfelek és munkaterhelések mérnöki befektetéseit.

Néhány példa: ügyfél használata a tárolók, tárolási és hálózati konfigurációkat a társított Azure Stack-szerepkörökről. Azt is használhatja az insights tevékenységéről, és az Azure Stack-kezelési és figyelési megoldások intelligenciát. Ezek a fejlesztések diagnosztizálhatja a problémákat, hogy az ügyfelek könnyebben és kevesebb támogatási tételével pénzt meghívja a Microsoftnak.

## <a name="manage-telemetry-collection"></a>Eszköztelemetria-gyűjtést kezelése

A Microsoft nem ajánlott módszerrel kikapcsolni a telemetriát a szervezet. Azonban bizonyos esetekben erre akkor lehet szükség.

Ezekben az esetekben a telemetriai szint küld a Microsoftnak a beállításjegyzék-beállítások az Azure Stack üzembe helyezése előtt, vagy a telemetriai adatok végpontok az Azure Stack üzembe helyezése után is beállíthatja.

### <a name="telemetry-levels-and-data-collection"></a>Telemetriai szint és az adatgyűjtés

Mielőtt módosítaná a telemetriai beállítások, tisztában kell lennie a telemetriai szint és az egyes szinteken gyűjtött adatokat.

A telemetriai beállítások négy szint (0 – 3), amelyek halmozott és kategorizált az alábbiak szerint vannak csoportosítva:

**0 (biztonság)**</br>
Csak biztonsági adatok. Az operációs rendszer biztonsága érdekében szükséges információk. Ez magában foglalja a kapcsolódó felhasználói élmény és a Telemetria-összetevő beállításait és a Windows Defender adatait. Nem működik a telemetria adott, az Azure Stackhez ezen a szinten áll rendelkezésre.

**1 (alapszintű)**</br>
A biztonsági adatokat, és alapszintű állapot- és minőségi adatokat. Alapvető információkat, beleértve: minőségi kapcsolatos adatokat, az alkalmazás kompatibilitási, az Alkalmazáshasználati adatokat, és adatait a **biztonsági** szintjét. A telemetriai szint beállítása alapszintű lehetővé teszi, hogy az Azure Stack telemetriai. Ezen a szinten gyűjtött adatokat tartalmazza:

- *Alapszintű eszközadatokat* , amely biztosítja, típusok és az ökoszisztéma natív és virtuális Windows Server 2016-példányok konfigurációkkal kapcsolatos megismerése. Az érintett műveletek közé tartoznak az alábbiak:

  - Gép attribútumok, például az OEM, és a modell.
  - Hálózati attribútumok, például a hálózati adapterek száma és a sebességét.
  - Processzor és memória attribútumok, például a magok számát és a telepített memória mennyiségét.
  - Tárolási attribútumok, hány meghajtót, például írja be a meghajtón, és a meghajtó méretét.

- *Telemetriai adatok funkció*, beleértve a feltöltött események, az eldobott események és az utolsó adatok aránya feltöltés ideje.
- *Minőségi szolgáltatással kapcsolatos információkat* , amelyik segíti a Microsoft Azure Stack hogyan teljesít alapvető ismeretekkel fejlesztése. Ha például egy adott hardverkonfigurációja a kritikus riasztások száma.
- *Kompatibilitási adatok* , hogy a rendszer és a egy virtuális gépet telepített erőforrás-szolgáltatók megismerése biztosít. Ez azonosítja a potenciális kompatibilitási problémákat.

**2 (speciális)**</br>
További elemzésekhez, többek között: hogyan használhatók az operációs rendszer és az Azure Stack-szolgáltatásokat, hogyan hajthat végre ezeket a szolgáltatásokat, speciális megbízhatóság adatokat, és adatokat a **biztonsági** és **alapszintű** szintek.

> [!NOTE]
> Ez a telemetriai alapértelmezett beállítása.

**3. (teljes)**</br>
Az összes azonosításához, és segít a problémák elhárításához szükséges adatokat, valamint az adatok a **biztonsági**, **alapszintű**, és **bővített** szinteket.

> [!IMPORTANT]
> Telemetria szintek csak a Microsoft Azure Stack-összetevők vonatkoznak. A nem a Microsofttól származó összetevők és szolgáltatások, az Azure Stack a hardvergyártó partnerektől, a hardver életciklus gazdagépen futó telemetriai szintek kívül a cloud services használatával kommunikálhatnak. Az Azure Stack hardver megoldásszolgáltató telemetriai szabályzat, és hogyan engedélyezve van a hibajelentések vagy kikapcsolhatja az újat megértéséhez együttműködve kell.

Windows és az Azure Stack telemetria kikapcsolása is letiltja az SQL-telemetria. A Windows Server telemetriai beállítások vonzataival kapcsolatban további információkért lásd: a [Windows Telemetria tanulmány](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: a telemetriai szint beállítása a Windows beállításjegyzékben

A Windows beállításjegyzék-szerkesztő segítségével állítsa be kézzel a telemetriai szint a fizikai gazdaszámítógépen Azure Stack üzembe helyezése előtt. Ha egy felügyeleti házirend már létezik, például a csoportházirend felülírja a következő beállításjegyzék-beállítást.

A fejlesztői csomag gazdagépen az Azure Stack üzembe helyezése előtt indítsa el a CloudBuilder.vhdx, és futtassa a következő szkriptet egy emelt szintű PowerShell-ablakban:

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK és a több csomópontos: engedélyezheti vagy tilthatja le a telemetria üzembe helyezés után

Engedélyezi vagy letiltja a telemetria üzembe helyezés után, az Ön hozzáféréssel kell rendelkeznie, az emelt szintű végpontját (EGP) amely ERCS virtuális gépeken érhető el.

1. Engedélyezése: `Set-Telemetry -Enable`
2. Letiltása: `Set-Telemetry -Disable`

PARAMÉTEREKKEL kapcsolatos részletek:
> . A paraméter engedélyezése – kapcsolja be a telemetriai adatok feltöltése</br>
> . A paraméter Disable - kapcsolja ki a telemetriai adatok feltöltése  

**Parancsfájl telemetria engedélyezése:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Parancsfájl a telemetriai adatokat:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>További lépések

[Az Azure Stack regisztrálása az Azure-ban](azure-stack-registration.md)