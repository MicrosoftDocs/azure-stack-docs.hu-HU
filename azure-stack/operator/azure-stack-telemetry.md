---
title: Azure Stack telemetria | Microsoft Docs
description: Útmutató Azure Stack telemetria beállításainak konfigurálásához a PowerShell használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: comartin
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 439a058ea4153e0b6f74932a0ad027a0f5f8a42b
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829257"
---
# <a name="azure-stack-telemetry"></a>Azure Stack telemetria

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack a telemetria automatikusan feltölti a rendszeradatokat a Microsoftnak a csatlakoztatott felhasználói felületen keresztül. A Microsoft Teams azokat az adatokat használja, amelyeket Azure Stack telemetria gyűjt, hogy javítsa az ügyfelek élményét. Ezek az adat a biztonság, az állapot, a minőség és a teljesítmény elemzéséhez is használatos.

Egy Azure Stack operátor esetében a telemetria értékes betekintést nyújt a vállalati környezetbe, és olyan hangvételt tesz lehetővé, amely a Azure Stack jövőbeli verzióinak formálásában nyújt segítséget.

> [!NOTE]
> A Azure Stack is konfigurálhatja a használati információk Azure-ba történő továbbításához a számlázáshoz. Ez olyan többcsomópontos Azure Stack ügyfelek esetében szükséges, akik az utólagos használatú számlázást választják. A használati jelentéseket a telemetria-től függetlenül kell vezérelni, és nem szükséges a több csomópontot használó ügyfelek számára, akik kiválasztják a kapacitás modellt vagy Azure Stack Development Kit felhasználók számára. Ezekben a forgatókönyvekben a használati jelentéskészítés kikapcsolható [a regisztrációs parancsfájl használatával](azure-stack-usage-reporting.md).

Azure Stack telemetria a Windows Server 2016 csatlakoztatott felhasználói felületén és a telemetria összetevőn alapul, amely az [Windows esemény-nyomkövetés (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging technológiát használja az események és adatok összegyűjtésére és tárolására. Azure Stack-összetevők ugyanazt a technológiát használják a nyilvános operációs rendszer eseménynaplózási és nyomkövetési API-jai használatával összegyűjtött események és adatok közzétételéhez. Ilyen Azure Stack-összetevők például a következő szolgáltatók: Hálózati erőforrás, tárolási erőforrás, figyelési erőforrás és frissítési erőforrás. A csatlakoztatott felhasználói élmény és telemetria összetevő az SSL protokollal titkosítja az adatokat, és a tanúsítvány-rögzítés használatával továbbítja az adatokat a HTTPS protokollon keresztül a Microsoft adatkezelés szolgáltatásnak.

> [!IMPORTANT]
> A telemetria-adatfolyam engedélyezéséhez a 443-as (HTTPS) portnak nyitva kell lennie a hálózaton. A csatlakoztatott felhasználói élmény és telemetria összetevő a Microsoft adatkezelés szolgáltatáshoz csatlakozik a következő helyen: https://v10.vortex-win.data.microsoft.com. A csatlakoztatott felhasználói élmény és a telemetria összetevő a https://settings-win.data.microsoft.com fájlhoz is kapcsolódik a konfigurációs adatok letöltéséhez.

## <a name="privacy-considerations"></a>Adatvédelmi megfontolások

A ETW szolgáltatás átirányítja a telemetria az adatvédelmet a védett felhőalapú tárolóba. A legkevesebb jogosultsági szintű útmutató telemetria-adatelérést biztosít. Csak az érvényes üzleti igényekkel rendelkező Microsoft-munkatársak férhetnek hozzá a telemetria-adathoz. A Microsoft nem osztja meg a személyes ügyféladatokat harmadik felekkel, az ügyfél belátása szerint vagy a [Microsoft adatvédelmi nyilatkozatában](https://privacy.microsoft.com/PrivacyStatement)leírt korlátozott célokra. A számítógépgyártókkal és partnerekkel megosztott üzleti jelentések összesített, anonim adatokat tartalmaznak. Az adatmegosztási döntéseket egy belső Microsoft-csapat végzi, beleértve az adatvédelmi, jogi és adatkezelési érdekelt feleket.

A Microsoft hisz a-ban, és gyakorlatok minimálisra csökkenti az információkat. Arra törekszünk, hogy csak a szükséges információkat gyűjtsük, és csak akkor tároljuk, ha a szolgáltatáshoz vagy elemzéshez szükséges. A Azure Stack rendszer és az Azure-szolgáltatások működésével kapcsolatos számos információ hat hónapon belül törlődik. Az összegzett vagy összesített adatokat hosszabb ideig őrzi meg a rendszer.

Tisztában vagyunk azzal, hogy fontos az ügyféladatok védelme és biztonsága.  A Microsoft átgondolt és átfogó megközelítést biztosít az ügyfelek adatainak védelméhez, valamint az ügyféladatok védelmét Azure Stackban. A rendszergazdák a funkciók és az adatvédelmi beállítások bármikori testreszabására szolgáló vezérlőkkel rendelkeznek. Az átláthatóság és a bizalom iránti elkötelezettségünk egyértelmű:

- Nyitottunk ügyfeleinkkel az összegyűjtött adatok típusairól.
- A nagyvállalati ügyfeleket a vezérléssel tesszük elérhetővé, és testre szabhatja a saját adatvédelmi beállításait.
- Először a vásárlói adatvédelmet és a biztonságot tesszük.
- Transzparensek vagyunk a telemetria-adatfelhasználással kapcsolatban.
- A felhasználói élmény fokozása érdekében a telemetria-adatgyűjtést használjuk.

A Microsoft nem szeretne bizalmas adatokat gyűjteni, például hitelkártyaszám, felhasználónevek és jelszavak, e-mail-címek vagy hasonló bizalmas információk gyűjtésére. Ha azt állapítjuk meg, hogy a bizalmas adatokat véletlenül fogadták, töröljük.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Példák arra, hogyan használja a Microsoft a telemetria-adatmennyiséget

A telemetria fontos szerepet játszik a kritikus fontosságú megbízhatósági problémák gyors azonosításában és javításában az ügyfelek központi telepítésében és konfigurációjában. A telemetria adatokkal kapcsolatos megállapítások segíthetnek azonosítani a szolgáltatásokkal vagy a hardveres konfigurációkkal kapcsolatos problémákat. A Microsoft azon képessége, hogy ezeket az adatmennyiséget az ügyfelektől, illetve az ökoszisztéma fejlesztéséhez is felkínálja, az integrált Azure Stack-megoldások minőségének sávját emeli ki.

A telemetria azt is segíti a Microsoftot, hogy jobban megértse, hogyan telepítik az ügyfelek az összetevőket, hogyan használhatják a szolgáltatásokat, és hogyan használják az üzleti célokat. Ezek az ismeretek segítenek rangsorolni a mérnöki beruházásokat olyan területeken, amelyek közvetlenül befolyásolhatják az ügyfelek élményeit és munkaterheléseit.

Ilyenek például a tárolók, a tárolók és a Azure Stack szerepkörökhöz társított hálózati konfigurációk felhasználói használata. A fejlesztéseket és az intelligenciát is felhasználjuk Azure Stack felügyeleti és monitorozási megoldásokra. Ezek a tökéletesítések megkönnyítik az ügyfelek számára a problémák diagnosztizálását és a pénz megtakarítását azáltal, hogy kevesebb támogatási hívást végeznek a Microsoftnak.

## <a name="manage-telemetry-collection"></a>Telemetria-gyűjtemény kezelése

Nem javasoljuk, hogy kapcsolja ki a telemetria a szervezetben. Bizonyos esetekben azonban szükség lehet erre.

Ezekben a forgatókönyvekben a Azure Stack telepítése előtt, vagy a Azure Stack telepítése után az telemetria-végpontok használatával konfigurálhatja a Microsoftnak eljuttatott telemetria-szintet a beállításjegyzék-beállításokkal.

### <a name="telemetry-levels-and-data-collection"></a>Telemetria szintek és adatgyűjtés

A telemetria beállításainak módosítása előtt ismernie kell a telemetria szintjét, és az egyes szinteken gyűjtött adatokat.

A telemetria-beállítások négy szintre vannak csoportosítva (0-3), amelyek kumulatívak, és az alábbiak szerint vannak kategorizálva:

**0 (biztonság)**</br>
Csak biztonsági adatként. Az operációs rendszer biztonságának megőrzéséhez szükséges információk. Ide tartoznak a csatlakoztatott felhasználói felülettel kapcsolatos információk, valamint a telemetria és a Windows Defender. Ezen a szinten nincs kibocsátva a Azure Stackra vonatkozó telemetria.

**1 (alapszintű)**</br>
Biztonsági adatok és alapvető állapot-és minőségi adatok. Alapvető eszközbeállítások, beleértve a minőséggel kapcsolatos adatokat, az alkalmazások kompatibilitását, az alkalmazások használati adatait és a **biztonsági** szintről származó adatokat. A telemetria szintjének alapszintű értékre állításával Azure Stack telemetria. Az ezen a szinten összegyűjtött adatok a következők:

- *Alapvető eszközbeállítások* , amely a natív és virtuális Windows Server 2016-példányok típusait és konfigurációit ismerteti az ökoszisztémában. Az érintett műveletek közé tartoznak az alábbiak:

  - Számítógép-attribútumok, például OEM és modell.
  - Hálózati attribútumok, például a hálózati adapterek száma és sebessége.
  - Processzor-és memória-attribútumok, például a magok száma és a telepített memória mennyisége.
  - Tárolási attribútumok, például a meghajtók száma, a meghajtó típusa és a meghajtó mérete.

- *Telemetria funkció*, beleértve a feltöltött események százalékos arányát, az eldobott eseményeket és az utolsó adatfeltöltési időt.
- A *minőséggel kapcsolatos információk* , amelyek segítenek a Microsoft számára a Azure stack végrehajtásának alapvető megismerésében. Például egy adott hardverkonfiguráció kritikus értesítéseinek száma.
- *Kompatibilitási információk* , amelyek segítségével megismerheti, hogy mely erőforrás-szolgáltatók vannak telepítve a rendszerre és a virtuális gépekre. Ez azonosítja a lehetséges kompatibilitási problémákat.

**2 (bővített)**</br>
További elemzések, többek között a következők: az operációs rendszer és a Azure Stack szolgáltatások használatának módja, hogyan működnek ezek a szolgáltatások, a speciális megbízhatósági adatok és a **biztonsági** és alapszintű adatok.

> [!NOTE]
> Ez az alapértelmezett telemetria-beállítás.

**3 (teljes)**</br>
A problémák azonosításához és javításához szükséges összes adat, valamint a **biztonsági**, alapszintű és **továbbfejlesztett** szintekből származó adatok.

> [!IMPORTANT]
> Ezek a telemetria-szintek csak Microsoft Azure Stack-összetevőkre vonatkoznak. A hardveres életciklus-gazdagépen futó, nem a Microsofttól származó szoftver-összetevők és szolgáltatások Azure Stack hardveres partnerek a telemetria-szinteken kívül is kommunikálhatnak a felhőalapú szolgáltatásaikkal. Az Azure Stack hardveres megoldás-szolgáltatóval együttműködve megismerheti a telemetria-szabályzatot, és hogyan engedélyezheti vagy letilthatja.

A Windows és a Azure Stack telemetria kikapcsolása letiltja az SQL-telemetria is. A Windows Server telemetria beállításainak következményeivel kapcsolatos további információkért tekintse meg a [Windows telemetria](https://aka.ms/winservtelemetry)szóló tanulmányt.

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: a telemetria szintjének beállítása a Windows beállításjegyzékben

A Windows Rendszerleíróadatbázis-szerkesztővel manuálisan állíthatja be a telemetria szintjét a fizikai gazdagépen a Azure Stack telepítése előtt. Ha már létezik felügyeleti házirend, például Csoportházirend, akkor felülbírálja ezt a beállításjegyzék-beállítást.

Mielőtt telepítené a Azure Stack a fejlesztői csomag gazdagépén, indítsa el a CloudBuilder. vhdx, és futtassa a következő parancsfájlt egy emelt szintű PowerShell-ablakban:

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

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK és több csomópont: a telemetria engedélyezése vagy letiltása az üzembe helyezés után

Az üzembe helyezést követően a telemetria engedélyezéséhez vagy letiltásához hozzáféréssel kell rendelkeznie a ERCS virtuális gépeken elérhető privilegizált végponthoz (PEP).

1. Engedélyezés: `Set-Telemetry -Enable`
2. A letiltáshoz: `Set-Telemetry -Disable`

PARAMÉTER részletei:
> . PARAMÉTER engedélyezése – a telemetria-adatok feltöltésének bekapcsolása</br>
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

## <a name="next-steps"></a>További lépések

[Az Azure Stack regisztrálása az Azure-ban](azure-stack-registration.md)