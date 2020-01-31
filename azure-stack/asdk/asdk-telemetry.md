---
title: Azure Stack telemetria
description: Megtudhatja, hogyan konfigurálhatja Azure Stack telemetria beállításait a PowerShell használatával.
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 4ab0323b38d538e1c064bc998a84a26eae267a52
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873447"
---
# <a name="azure-stack-telemetry"></a>Azure Stack telemetria

A rendszer automatikusan feltölti a Microsoftot a csatlakoztatott felhasználói felülettel Azure Stack rendszerértékeket vagy telemetria. Az Azure Stack telemetria összegyűjtött adatokat a Microsoft csapata elsődlegesen a felhasználói élmény javítására használja fel. Biztonsági, egészségügyi, minőségi és teljesítménybeli elemzésre is használható.

Azure Stack operátorként a telemetria értékes betekintést biztosíthat a vállalati környezetbe, és olyan hangvételt tesz lehetővé, amely a Azure Stack jövőbeli verzióinak formálásában nyújt segítséget.

> [!NOTE]
> A Azure Stack konfigurálható úgy is, hogy a használati adatokat az Azure-ba továbbítsa a számlázáshoz. Ez olyan többcsomópontos Azure Stack ügyfelek esetében szükséges, akik az utólagos használatú számlázást választják. A használati jelentések a telemetria függetlenül vannak szabályozva, és nem szükségesek a többcsomópontos ügyfelek számára, akik kiválasztják a kapacitás modellt vagy a Azure Stack Development Kit (ASDK) felhasználók számára. Ezekben a forgatókönyvekben a használati jelentéskészítés kikapcsolható [a regisztrációs parancsfájl használatával](../operator/azure-stack-usage-reporting.md).

Azure Stack telemetria a *Windows Server 2016 csatlakoztatott felhasználói felületén és a Telemetr*y összetevőn alapul, amely a [Windows esemény-nyomkövetés (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) nyomkövetési naplózási technológiát használja a telemetria-események és-adatok gyűjtésére és tárolására. Azure Stack-összetevők ugyanazt a naplózási technológiát használják a nyilvános operációs rendszer eseménynaplózási és nyomkövetési API-jai használatával összegyűjtött események és adatok közzétételéhez. Azure Stack összetevők közé tartoznak például a hálózati erőforrás-szolgáltató, a tárolási erőforrás-szolgáltató, a figyelés erőforrás-szolgáltatója és az erőforrás-szolgáltató frissítése. A csatlakoztatott felhasználói élmény és telemetria összetevő az SSL használatával titkosítja az adatokat, és a tanúsítvány-rögzítés használatával továbbítja a telemetria adatokat a HTTPS protokollon keresztül a Microsoft adatkezelés szolgáltatásnak.

> [!NOTE]
> A telemetria-adatfolyamok támogatásához a 443-as (HTTPS) portnak nyitva kell lennie a hálózaton. A csatlakoztatott felhasználói élmény és telemetria összetevő a Microsoft adatkezelés szolgáltatáshoz csatlakozik a https://v10.vortex-win.data.microsoft.com címen, és https://settings-win.data.microsoft.com a konfigurációs adatok letöltéséhez is.

## <a name="privacy-considerations"></a>Adatvédelmi megfontolások
A ETW szolgáltatás a telemetria-adatküldést a védett felhőalapú tárolóba irányítja. A legalacsonyabb jogosultsági szintű útmutatók a telemetria-adatbázisokhoz való hozzáférésének elve. Csak az érvényes üzleti igényekkel rendelkező Microsoft-munkatársak férhetnek hozzá a telemetria-adatbázishoz. A Microsoft nem osztja meg az ügyfél személyes adatait harmadik felekkel, kivéve az ügyfél belátása szerint vagy a [Azure stack adatvédelmi nyilatkozatban](https://privacy.microsoft.com/PrivacyStatement)leírt korlátozott célokra. Üzleti jelentéseket osztunk meg olyan számítógépgyártókkal és partnerekkel, amelyek összesített, anonim telemetria-adatokat tartalmaznak. Az adatmegosztási döntéseket egy belső Microsoft-csapat végzi, beleértve az adatvédelmi, jogi és adatkezelési érdekelt feleket.

A Microsoft hisz az információk minimalizálása terén. Arra törekszünk, hogy csak a szükséges információkat gyűjtsük, és csak akkor tároljuk, ha a szolgáltatás nyújtásához vagy elemzéshez szükséges. A Azure Stack rendszer és az Azure-szolgáltatások működésével kapcsolatos információk nagy részét hat hónapon belül töröljük. Az összegzett vagy összesített adatokat hosszabb ideig őrzi meg a rendszer.

Tisztában vagyunk azzal, hogy az ügyfelek adatainak védelme és biztonsága fontos. Átgondolt és átfogó megközelítést tettünk az ügyfelek adatainak védelme érdekében, és az ügyféladatok védelmét a Azure Stack. A rendszergazdák a funkciók és az adatvédelmi beállítások bármikori testreszabására szolgáló vezérlőkkel rendelkeznek. Az átláthatóság és a bizalom iránti elkötelezettségünk egyértelmű:
- Nyitottunk ügyfeleinkkel az összegyűjtött adatok típusairól.
- A nagyvállalati ügyfeleket a vezérléssel tesszük elérhetővé, és testre szabhatja a saját adatvédelmi beállításait.
- Először a vásárlói adatvédelmet és a biztonságot tesszük.
- Transzparensek vagyunk a telemetria használatáról.
- A telemetria segítségével javíthatja az ügyfelek tapasztalatait.

A Microsoft nem szeretne bizalmas adatokat gyűjteni, például hitelkártyaszám, felhasználónevek és jelszavak, e-mail-címek. Ha azt állapítjuk meg, hogy a bizalmas adatokat véletlenül fogadták, töröljük.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Példák arra, hogyan használja a Microsoft a telemetria-adatmennyiséget
A telemetria fontos szerepet játszik abban, hogy gyorsan azonosítsa és javítsa a kritikus fontosságú megbízhatósági problémákat az ügyfelek üzembe helyezései és konfigurációi terén. Betekintést nyerhet az összegyűjtött telemetria-adatokba, hogy gyorsan azonosítsa a szolgáltatásokkal vagy a hardveres konfigurációkkal kapcsolatos problémákat. A Microsoft ezen adatoknak az ügyfelektől való beszerzésére, valamint az ökoszisztémába való fejlesztésre való képességgel segíti a mércét az integrált Azure Stack-megoldások minőségének növelésében.

A telemetria azt is segíti a Microsoftot, hogy jobban megértse, hogyan telepítik az ügyfelek az összetevőket, hogyan használhatják a szolgáltatásokat, és hogyan használják az üzleti célokat. Az adatok elemzésével olyan területeken végezheti el a mérnöki beruházások rangsorolását, amelyek közvetlenül befolyásolhatják ügyfeleink tapasztalatait és munkaterheléseit.

Ilyenek például a tárolók, a tárolók és a Azure Stack szerepkörökhöz társított hálózati konfigurációk felhasználói használata. Az elemzések segítségével a fejlesztéseket és az intelligenciát is elvégezheti a felügyeleti és monitorozási megoldásokban. Ez a fejlesztés segít az ügyfeleknek a minőségi problémák diagnosztizálásában és a pénz megtakarításában azáltal, hogy kevesebb támogatási hívást kezdeményeznek a Microsoftnak.

## <a name="manage-telemetry-collection"></a>Telemetria-gyűjtemény kezelése
Nem javasoljuk, hogy kapcsolja ki a telemetria a munkahelyen, mivel a telemetria olyan információkat biztosít, amelyek javítják a termék működőképességét és stabilitását. Felismerjük azonban, hogy erre bizonyos helyzetekben szükség lehet.

Ezekben a példányokban konfigurálhatja a Microsoft számára a beállításjegyzék-beállítások előzetes központi telepítésével vagy a telemetria-végpontok telepítés utáni üzembe helyezésével eljuttatott telemetria szintet.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Telemetria szint beállítása a Windows beállításjegyzékben
A Windows Rendszerleíróadatbázis-szerkesztővel manuálisan állíthatja be a telemetria szintjét a fizikai gazdaszámítógépen a Azure Stack telepítése előtt. Ha már létezik felügyeleti házirend, például Csoportházirend, akkor felülbírálja ezt a beállításjegyzék-beállítást.

Mielőtt telepítené a Azure Stack a ASDK-gazdagépen, indítsa el a CloudBuilder. vhdx, és futtassa a következő parancsfájlt egy emelt szintű PowerShell-ablakban:

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

A telemetria szintek kumulatívak, és négy szintre vannak kategorizálva (0-3):

**0 (biztonság)** : csak biztonsági adatként. Az operációs rendszer biztonságának megőrzéséhez szükséges információk, beleértve a csatlakoztatott felhasználói felülettel és a telemetria és a Windows Defender szolgáltatással kapcsolatos adatokat is. Ezen a szinten nincs kibocsátva Azure Stack adott telemetria.

**1 (alapszintű)** : biztonsági adatok és alapvető állapot-és minőségi adatok. Alapszintű eszköz adatai, beleértve a minőséggel kapcsolatos adatokat, az alkalmazások kompatibilitását, az alkalmazások használati adatait és a biztonsági szintről származó adatokat. A telemetria szintjének alapszintű értékre állításával Azure Stack telemetria. Az ezen a szinten összegyűjtött adatok a következők:

- **Alapszintű eszköz adatai** , amelyek segítségével megismerheti a natív és virtualizált Windows Server 2016-példányok típusait és konfigurációit az ökoszisztémában, beleértve a következőket:
  - Számítógép-attribútumok, például OEM és modell.
  - Hálózati attribútumok, például a hálózati adapterek száma és sebessége.
  - Processzor-és memória-attribútumok, például a magok és a memória méretének száma.
  - Tárolási attribútumok, például a meghajtók, a típus és a méret száma.
- A **telemetria funkciói**, beleértve a feltöltött események százalékos arányát, az eldobott eseményeket és a legutóbbi feltöltési időt.
- A **minőséggel kapcsolatos információk** , amelyek segítenek a Microsoft számára a Azure stack végrehajtásának alapvető megismerésében. Ilyen például a kritikus riasztások száma egy adott hardverkonfiguráció esetében.
- **Kompatibilitási információk**, amelyek segítségével megismerheti, hogy mely erőforrás-szolgáltatók vannak telepítve a rendszerre és a virtuális gépre, és azonosítja a lehetséges kompatibilitási problémákat.

**2 (bővített)** : további elemzések, többek között az operációs rendszer és az egyéb Azure stack szolgáltatások használatának módja, a teljesítményük és a speciális megbízhatósági adatok, valamint az alapszintű és a biztonsági szintekből származó adatok.

**3 (teljes)** : minden olyan adat, amely a problémák azonosításához és javításához, valamint a biztonsági, alapszintű és magasabb szintű adatokhoz szükséges.

> [!NOTE]
> Az alapértelmezett telemetria érték 2 (bővített).

A Windows és a Azure Stack telemetria kikapcsolása letiltja az SQL-telemetria. A Windows Server telemetria beállításainak következményeiről további információt a [Windows telemetria](https://aka.ms/winservtelemetry)tanulmányában olvashat.

> [!IMPORTANT]
> Ezek a telemetria-szintek csak Microsoft Azure Stack-összetevőkre vonatkoznak. A hardveres életciklus-gazdagépen futó, nem a Microsofttól származó szoftver-összetevők és szolgáltatások Azure Stack hardveres partnerek a telemetria-szinteken kívül is kommunikálhatnak a felhőalapú szolgáltatásaikkal. Az Azure Stack hardveres megoldás-szolgáltatóval együttműködve megismerheti a telemetria-szabályzatot, és hogyan engedélyezheti vagy letilthatja.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Telemetria engedélyezése vagy letiltása az üzembe helyezés után

Az üzembe helyezést követően a telemetria engedélyezéséhez vagy letiltásához hozzáféréssel kell rendelkeznie a ERCS virtuális gépeken elérhető privilegizált végponthoz (PEP).
1.  Engedélyezés: `Set-Telemetry -Enable`
2.  A letiltáshoz: `Set-Telemetry -Disable`

PARAMÉTER részletei:
> . PARAMÉTER engedélyezése – a telemetria-adatok feltöltésének bekapcsolása
> 
> . PARAMÉTER letiltása – a telemetria-adatok feltöltésének kikapcsolása  

**Parancsfájl a telemetria engedélyezéséhez:**
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

**A telemetria letiltására szolgáló parancsfájl:**
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

## <a name="next-steps"></a>Következő lépések
[A ASDK elindítása és leállítása](asdk-start-stop.md)
