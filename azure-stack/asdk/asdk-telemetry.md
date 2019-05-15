---
title: Az Azure Stack-telemetria |} A Microsoft Docs
description: Ismerteti a PowerShell-lel az Azure Stack telemetriai beállításainak konfigurálása.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 78ca05eb15570022e6eb25a7023d91c5ff8b817f
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617347"
---
# <a name="azure-stack-telemetry"></a>Az Azure Stack-telemetria

Az Azure Stack rendszer adatok vagy telemetria, automatikusan töltenek fel a Microsoft csapatához, a kapcsolódó felhasználói élményt. Összegyűjtött adatok alapján az Azure Stack-telemetria szolgál, elsősorban a a felhasználói élmény javítása érdekében a Microsoft teams és a biztonsági, egészségügyi, minőségi és teljesítménybeli elemzés céljából.

Az Azure Stack operátorait szerint telemetriai nyújthat értékes betekintést az vállalati üzemelő példánya, és a egy hang, amely segít az Azure Stack későbbi verzióiban alakzat biztosít.

> [!NOTE]
> Az Azure Stack is megadható előre használati adatokat az Azure-ba, a számlázáshoz. Ez azért szükséges, a több csomópontos Azure Stack ajánljuk, akik a számlázás használatalapú mint-akkor-használható. Használati jelentések szabályozott egymástól függetlenül telemetriai adatokból, és, nem szükséges, több csomópontos ajánljuk, akik a kapacitás modellt vagy az Azure Stack Development Kit felhasználókhoz. Ebben az esetben használati jelentések is ki legyen kapcsolva [a regisztrációs szkripttel](../operator/azure-stack-usage-reporting.md).

A Windows Server 2016 csatlakoztatott felhasználói felület és a Telemetria összetevőt, amely használja az Azure Stack-telemetria alapján a [esemény-nyomkövetése Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) nyomkövetési naplózás technológia segítségével gyűjtse össze és tárolja a telemetriai események és az adatok. Az Azure Stack-összetevők ugyanazt a technológiát naplózás használatával elvégezhető a közzététel események és az adatokat, amelyek nyilvános operációs rendszer eseménynaplózás és nyomon követése az API-k használatával gyűjti össze. Az Azure Stack-összetevők például a hálózati erőforrás-szolgáltató, Tárolásierőforrás-szolgáltató, erőforrás-szolgáltató figyelési és erőforrás-szolgáltató frissítése. A csatlakoztatott felhasználói élmény és a Telemetria összetevő az adatok SSL használatával titkosítja, és használja a tanúsítvány rögzítését továbbítani a telemetriai adatokat HTTPS-kapcsolaton keresztül a Microsoft Data Management szolgáltatással.

> [!NOTE]
> Támogatja a telemetria-adatfolyam, 443-as (HTTPS) nyissa meg a hálózaton kell lennie. A csatlakoztatott felhasználói élmény és a Telemetria összetevő csatlakozik a Microsoft adatkezelési szolgáltatás https://v10.vortex-win.data.microsoft.com. A csatlakoztatott felhasználói élmény és a Telemetria összetevő is csatlakozik https://settings-win.data.microsoft.com konfigurációs információk letöltése.

## <a name="privacy-considerations"></a>Adatvédelmi szempontok
Az ETW-szolgáltatás telemetriai adatokat vissza a védett tárolási irányítja. A telemetriai adatokat a legalacsonyabb jogosultsági szintű útmutatók hozzáférést elvét. Csak a Microsoft munkatársainak az érvényes üzleti igények hozzáférésük van a telemetriai adatokat. A Microsoft nem oszt meg semmilyen személyes adatait harmadik felekkel ügyfeleink kivételével belátása szerint az ügyfél vagy a korlátozott célokra a [Azure Stack adatvédelmi nyilatkozata](https://privacy.microsoft.com/PrivacyStatement). Az OEM-EK és összesített, anonimizált telemetriai adatokat tartalmazó üzleti jelentéseket osztjuk. Adatok megosztása döntések egy belső, többek között az adatok, jogi és adatvédelmi felügyeleti résztvevőket a Microsoft csapata által végzett.

A Microsoft úgy találja, hogy a, és információkat minimalizálásával eljárásokat. Igyekszünk csak adatok összegyűjtéséhez, hogy van szükségünk, és tároljuk, csak addig, szükség szerint adjon meg egy szolgáltatás vagy elemzés céljából. Hogyan működnek az Azure Stack rendszer és az Azure-szolgáltatásokkal kapcsolatos információk nagy részét hat hónapon belül törlődik. Összegezve, vagy hosszabb ideig őrzi meg összesített adatokat.

Tisztában vagyunk vele, hogy vevőink adatok biztonsága és védelme fontos. A példákon és átfogó megközelítés átirányította adatvédelem és az Azure Stack ügyféladatok védelméről. A rendszergazdák funkciókat és adatvédelmi beállítások testreszabása bármikor vezérlők rendelkezik. Elkötelezettségünk, átláthatóság és a megbízhatósági kapcsolat a következő törlése:
- Nyissa meg az ügyfelekkel kapcsolatos adatokat, hogy gyűjtse össze tudjuk.
- Vezérlőelem tárgyaljuk, a vállalati felhasználók – szabhatják testre a saját adatvédelmi beállítások.
- Adatvédelem és biztonság először tárgyaljuk.
- Jelenleg a transzparens kapcsolatos telemetriai adatokat lekérdezi.
- Telemetria felhasználói élmény javítására használjuk.

A Microsoft nem tervezi a bizalmas adatokat, például hitelkártyaszámokat tartalmazó, felhasználónevek és jelszavak, e-mail-címeket vagy más hasonló módon bizalmas adatokat gyűjteni. Ha azt állapítja meg, hogy bizalmas adatok véletlenül érkezett, hogy törölje azt.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Példák a telemetriai adatok Microsoft általi használatára
Telemetria lehetünk gyorsan azonosíthatja és megoldásának szintű megbízhatóság vevőink központi telepítések és konfigurációk a fontos szerepet játszik. A telemetriai adatokat, hogy gyűjtse össze betekintést segítsen szolgáltatásokkal vagy hardverkonfigurációk gyorsan azonosíthatja a problémákat. A Microsoft képes ügyfelek és a meghajtó fejlesztései az ökoszisztéma be ezen adatok beolvasásához segít az integrált Azure Stack-megoldások minőségének emelni.

Telemetria segít jobban megérteni, hogyan ügyfelek összetevők üzembe helyezése, funkciók és szolgáltatások használata az üzleti céljainak elérését a Microsoft is. Elemzések lekérése az adatok segítségével rangsorolja mérnöki befektetések területeken, hogy vevőink élményt és számítási feladatok közvetlen hatással lehet.

Néhány példa: a tárolók, tárolási és hálózati konfigurációk az Azure Stack-szerepkörökről rendelt ügyfelek általi használatot. Azt is használhatja az insights tevékenységéről, és a felügyeleti és figyelési megoldások intelligenciát. Ez segítséget nyújt az ügyfelek minőségi problémák diagnosztizálása és kevesebb támogatási tételével pénzt meghívja a Microsoftnak.

## <a name="manage-telemetry-collection"></a>Eszköztelemetria-gyűjtést kezelése
Nem ajánlott kikapcsolni a telemetriát a szervezetben, telemetriai adatokat adja meg az adatait, amely a továbbfejlesztett termékfunkciók és stabilitását. A rendszer ismeri fel azonban, hogy bizonyos esetekben erre akkor lehet szükség.

Ezekben az esetekben a beállításjegyzékbeli beállítások központi telepítés előtti, vagy pedig a telemetriai adatok végpontok üzembe helyezés utáni által a Microsoftnak küldött telemetriai szint is beállíthatja.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Telemetriai szint beállítása a Windows beállításjegyzékben
A Windows Beállításszerkesztőt segítségével állítsa be kézzel a telemetriai szint a fizikai gazdaszámítógépen Azure Stack üzembe helyezése előtt. Ha egy felügyeleti házirend már létezik, például a csoportházirend felülírja a következő beállításjegyzék-beállítást.

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

A telemetriai adatok szintek a következők: összegző és kategóriákkal, négy szint (0 – 3):

**0 (biztonság)**. Csak biztonsági adatok. Biztonságos annak érdekében, hogy az operációs rendszer szükséges információk, beleértve a kapcsolódó felhasználói élmény és a Telemetria-összetevő beállításait és a Windows Defender adatait. Ezen a szinten nem meghatározott Azure Stack-telemetria bocsásson ki.

**1 (alapszintű)**. A biztonsági adatokat, és alapszintű állapot- és minőségi adatokat. Alapszintű eszközadatok, például: minőségi adat, alkalmazás kompatibilitási, Alkalmazáshasználati adatokat és a biztonsági szint származó adatok. A telemetriai szint beállítása alapszintű lehetővé teszi, hogy az Azure Stack telemetriai. Ezen a szinten gyűjtött adatokat tartalmazza:

- **Alapszintű eszközadatokat** , típusok és az ökoszisztéma natív és virtuális Windows Server 2016-példányok konfigurációkkal kapcsolatos megismerése biztosít többek között:
  - Gépek attribútumait, például a gyártó, a modell,
  - Hálózatkezelés attribútumok, például számát és sebességű hálózati adapterek
  - Processzor és memória attribútumait, például a magok, memória méretét, számát
  - Tárolási attribútumok, például a meghajtók, típusú és méretű számát.
- **Telemetriai adatok funkció**, többek között százaléka feltöltött események eldobott események, valamint a legutóbbi feltöltés ideje.
- **Minőségi szolgáltatással kapcsolatos információkat** , amelyik segíti a Microsoft Azure Stack hogyan teljesít alapvető ismeretekkel fejlesztése. Ilyen például, egy adott hardverkonfigurációja a kritikus riasztások száma.
- **Kompatibilitási adatok**, amely segít biztosítanak arról, hogy mely erőforrás-szolgáltató telepítve vannak a rendszer és a virtuális gép, és azonosítja az esetleges kompatibilitási problémák megértéséhez.

**2. (bővített)**. További elemzésekhez, többek között: hogyan használja az operációs rendszer és más Azure Stack-szolgáltatásokat, azok teljesítményét, speciális megbízhatósági adatok és az alapszintű és a biztonsági szintek adatait.

**3. (teljes)**. Az összes azonosításához, és segít a problémák elhárításához szükséges adatokat, valamint az adatok a **biztonsági**, **alapszintű**, és **bővített** szinteket.

> [!NOTE]
> Az alapértelmezett telemetriai szint értéke 2 (bővített).

Windows és az Azure Stack telemetria kikapcsolása letiltja az SQL-telemetriát. A Windows Server telemetriai beállítások vonzataival kapcsolatban további információkért tekintse a [Windows Telemetria tanulmány](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Telemetria szintek csak a Microsoft Azure Stack-összetevők vonatkoznak. A nem a Microsofttól származó összetevők és szolgáltatások, az Azure Stack a hardvergyártó partnerektől, a hardver életciklus gazdagépen futó telemetriai szintek kívül a cloud services használatával kommunikálhatnak. Az Azure Stack hardver megoldásszolgáltató telemetriai szabályzat, és hogyan engedélyezve van a hibajelentések vagy kikapcsolhatja az újat megértéséhez együttműködve kell.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Engedélyezheti vagy tilthatja le a telemetria üzembe helyezés után

Engedélyezi vagy letiltja a telemetria üzembe helyezés után, az Ön hozzáféréssel kell rendelkeznie, az emelt szintű végpontját (EGP) amely ERCS virtuális gépeken érhető el.
1.  Engedélyezése: `Set-Telemetry -Enable`
2.  Letiltása: `Set-Telemetry -Disable`

A paraméter részletei a következők:
> . A paraméter engedélyezése – kapcsolja be a telemetriai adatok feltöltése
> 
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
[Elindíthatja és leállíthatja a ASDK](asdk-start-stop.md)
