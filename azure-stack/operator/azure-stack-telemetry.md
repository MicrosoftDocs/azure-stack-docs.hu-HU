---
title: Azure Stack hub-telemetria konfigurálása
titleSuffix: Azure Stack
description: Ismerkedjen meg Azure Stack hub telemetria, és hogyan konfigurálhatja a telemetria-beállításokat a PowerShell használatával.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/16/2020
ms.author: inhenkel
ms.reviewer: comartin
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 6bc12104ef8ce325fe9b1773373ef235a33919e5
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77696957"
---
# <a name="configure-azure-stack-hub-telemetry"></a>Azure Stack hub-telemetria konfigurálása

Azure Stack hub telemetria automatikusan feltölti a rendszeradatokat a Microsoftnak a csatlakoztatott felhasználói felületen keresztül. A Microsoft Teams azokat az adatokat használja, amelyeket Azure Stack hub telemetria gyűjt, hogy javítsa az ügyfelek élményét. Ezek az adat a biztonság, az állapot, a minőség és a teljesítmény elemzéséhez is használatos.

Az Azure Stack hub-operátorok esetében a telemetria értékes betekintést nyújt a vállalati környezetbe, és olyan hangvételt tesz lehetővé, amely a Azure Stack hub jövőbeli verzióinak formálásában nyújt segítséget.

> [!NOTE]
> Azure Stack hub-t úgy is konfigurálhat, hogy a használati adatokat továbbítsa az Azure-nak a számlázáshoz. Ez a többcsomópontos Azure Stack hub-ügyfelek esetében szükséges, akik fizetési díjas számlázást választanak. A használati jelentéseket a telemetria-től függetlenül kell vezérelni, és nem szükséges a több csomópontot használó ügyfelek számára, akik kiválasztják a kapacitás modellt vagy Azure Stack Development Kit felhasználók számára. Ezekben a forgatókönyvekben a használati jelentéskészítés kikapcsolható [a regisztrációs parancsfájl használatával](azure-stack-usage-reporting.md).

Azure Stack hub telemetria a Windows Server 2016 csatlakoztatott felhasználói felületén és a telemetria összetevőn alapul. Ez az összetevő a [Windows esemény-nyomkövetés (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging technológiát használja az események és adatok összegyűjtésére és tárolására. Azure Stack-összetevők ugyanazt a technológiát használják a nyilvános operációs rendszer eseménynaplózási és nyomkövetési API-jai használatával összegyűjtött események és adatok közzétételéhez. Ilyen Azure Stack hub-összetevők például a következő szolgáltatók: hálózati erőforrás, tárolási erőforrás, figyelési erőforrás és frissítési erőforrás. A csatlakoztatott felhasználói élmény és telemetria összetevő az SSL protokollal titkosítja az adatokat, és a tanúsítvány-rögzítés használatával továbbítja az adatokat a HTTPS protokollon keresztül a Microsoft adatkezelés szolgáltatásnak.

> [!IMPORTANT]
> A telemetria-adatfolyam engedélyezéséhez a 443-as (HTTPS) portnak nyitva kell lennie a hálózaton. A csatlakoztatott felhasználói élmény és telemetria összetevő a Microsoft adatkezelés szolgáltatáshoz kapcsolódik a `https://v10.events.data.microsoft.com`következő helyen:. A csatlakoztatott felhasználói élmény és telemetria összetevő a konfigurációs adatok `https://settings-win.data.microsoft.com` letöltéséhez is csatlakozik. Más diagnosztikai adatszolgáltatások `https://watson.telemetry.microsoft.com` csatlakoznak a hibajelentéshez.

## <a name="privacy-considerations"></a>Adatvédelmi megfontolások

A ETW szolgáltatás átirányítja a telemetria az adatvédelmet a védett felhőalapú tárolóba. A legkevesebb jogosultsági szintű útmutató telemetria-adatelérést biztosít. Csak az érvényes üzleti igényekkel rendelkező Microsoft-munkatársak férhetnek hozzá a telemetria-adathoz. A Microsoft nem osztja meg a személyes ügyféladatokat harmadik felekkel, az ügyfél belátása szerint vagy a [Microsoft adatvédelmi nyilatkozatában](https://privacy.microsoft.com/PrivacyStatement)leírt korlátozott célokra. A számítógépgyártókkal és partnerekkel megosztott üzleti jelentések összesített, anonim adatokat tartalmaznak. Az adatmegosztási döntéseket egy belső Microsoft-csapat végzi, beleértve az adatvédelmi, jogi és adatkezelési érdekelt feleket.

A Microsoft hisz a-ban, és gyakorlatok minimálisra csökkenti az információkat. Arra törekszünk, hogy csak a szükséges információkat gyűjtsük, és csak akkor tároljuk, ha a szolgáltatáshoz vagy elemzéshez szükséges. Az Azure Stack hub-rendszer és az Azure-szolgáltatások működésével kapcsolatos számos információ hat hónapon belül törlődik. Az összegzett vagy összesített adatokat hosszabb ideig őrzi meg a rendszer.

Tisztában vagyunk azzal, hogy fontos az ügyféladatok védelme és biztonsága.  A Microsoft átgondolt és átfogó megközelítést biztosít az ügyfelek adatvédelme és az ügyféladatok védelme Azure Stack központban. A rendszergazdák a funkciók és az adatvédelmi beállítások bármikori testreszabására szolgáló vezérlőkkel rendelkeznek. Az átláthatóság és a bizalom iránti elkötelezettségünk egyértelmű:

- Nyitottunk ügyfeleinkkel az összegyűjtött adatok típusairól.
- A nagyvállalati ügyfeleket a vezérléssel tesszük elérhetővé, és testre szabhatja a saját adatvédelmi beállításait.
- Először a vásárlói adatvédelmet és a biztonságot tesszük.
- Transzparensek vagyunk a telemetria-adatfelhasználással kapcsolatban.
- A felhasználói élmény fokozása érdekében a telemetria-adatgyűjtést használjuk.

A Microsoft nem kíván bizalmas adatokat gyűjteni, például hitelkártyaszám, felhasználónevek és jelszavak, e-mail-címek vagy hasonló bizalmas információk gyűjtésére. Ha azt állapítjuk meg, hogy a bizalmas adatokat véletlenül fogadták, töröljük.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Példák arra, hogyan használja a Microsoft a telemetria-adatmennyiséget

A telemetria fontos szerepet játszik a kritikus fontosságú megbízhatósági problémák gyors azonosításában és javításában az ügyfelek központi telepítésében és konfigurációjában. A telemetria adatokkal kapcsolatos megállapítások segíthetnek azonosítani a szolgáltatásokkal vagy a hardveres konfigurációkkal kapcsolatos problémákat. A Microsoft ezen adatoknak az ügyfelektől való beszerzésére, valamint az ökoszisztémák fejlesztésére való képességével az integrált Azure Stack hub-megoldások minőségét is kiválthatja.

A telemetria azt is segíti a Microsoftot, hogy jobban megértse, hogyan telepítik az ügyfelek az összetevőket, hogyan használhatják a szolgáltatásokat, és hogyan használják az üzleti célokat. Ezek az ismeretek segítenek rangsorolni a mérnöki beruházásokat olyan területeken, amelyek közvetlenül befolyásolhatják az ügyfelek élményeit és munkaterheléseit.

Ilyenek például a tárolók, a tárolók és a Azure Stack hub-szerepkörökhöz társított hálózati konfigurációk ügyfeleinek használata. A fejlesztéseket és az intelligenciát az Azure Stack hub felügyeleti és monitorozási megoldásaira is felhasználjuk. Ezek a tökéletesítések megkönnyítik az ügyfelek számára a problémák diagnosztizálását és a pénz megtakarítását azáltal, hogy kevesebb támogatási hívást végeznek a Microsoftnak.

## <a name="manage-telemetry-collection"></a>Telemetria-gyűjtemény kezelése

Nem javasoljuk, hogy kapcsolja ki a telemetria a szervezetben. Bizonyos helyzetekben azonban szükség lehet rá.

Ezekben a forgatókönyvekben a Azure Stack hub telepítése előtt, vagy az Azure Stack hub üzembe helyezése után az telemetria-végpontok használatával konfigurálhatja a Microsoft számára elküldett telemetria-szintet a beállításjegyzék beállításaival.

### <a name="telemetry-levels-and-data-collection"></a>Telemetria szintek és adatgyűjtés

A telemetria beállításainak módosítása előtt ismernie kell a telemetria szintjét, és az egyes szinteken gyűjtött adatokat.

A telemetria-beállítások négy szintre vannak csoportosítva (0-3), amelyek kumulatívak, és az alábbiak szerint vannak kategorizálva:

**0 (biztonság)**</br>
Csak biztonsági adatként. Az operációs rendszer biztonságának megőrzéséhez szükséges információk. Ide tartoznak a csatlakoztatott felhasználói felülettel kapcsolatos információk, valamint a telemetria és a Windows Defender. Az Azure Stack hub-ra vonatkozó telemetria ezen a szinten nincsenek kibocsátva.

**1 (alapszintű)**</br>
Biztonsági adatok és alapvető állapot-és minőségi adatok. Alapvető eszközbeállítások, beleértve a minőséggel kapcsolatos adatokat, az alkalmazások kompatibilitását, az alkalmazások használati adatait és a **biztonsági** szintről származó adatokat. A telemetria szintjének alapszintű értékre állításával Azure Stack hub-telemetria. Az ezen a szinten összegyűjtött adatok a következők:

- *Alapvető eszközbeállítások* , amely a natív és virtuális Windows Server 2016-példányok típusait és konfigurációit ismerteti az ökoszisztémában. Az érintett műveletek közé tartoznak az alábbiak:

  - Számítógép-attribútumok, például OEM és modell.
  - Hálózati attribútumok, például a hálózati adapterek száma és sebessége.
  - Processzor-és memória-attribútumok, például a magok száma és a telepített memória mennyisége.
  - Tárolási attribútumok, például a meghajtók száma, a meghajtó típusa és a meghajtó mérete.

- *Telemetria funkció*, beleértve a feltöltött események százalékos arányát, az eldobott eseményeket és az utolsó adatfeltöltési időt.
- A *minőséggel kapcsolatos információk* , amelyek segítenek a Microsoft számára az Azure stack hub működésének alapvető megismerésében. Például egy adott hardverkonfiguráció kritikus értesítéseinek száma.
- *Kompatibilitási információk* , amelyek segítségével megismerheti, hogy mely erőforrás-szolgáltatók vannak telepítve a rendszerre és a virtuális GÉPRE (VM). Ez azonosítja a lehetséges kompatibilitási problémákat.

**2 (bővített)**</br>
További elemzések, többek között a következők: az operációs rendszer és az Azure Stack központ szolgáltatásainak használata, hogyan működnek ezek a szolgáltatások, a speciális megbízhatósági adatok, valamint a **biztonsági** **és alapszintű** adatok.

> [!NOTE]
> Ez az alapértelmezett telemetria-beállítás.

**3 (teljes)**</br>
A problémák azonosításához és javításához szükséges összes adat, valamint a **biztonsági** **, alapszintű**és **továbbfejlesztett** szintekből származó adatok.

> [!IMPORTANT]
> Ezek a telemetria-szintek csak Microsoft Azure Stack hub-összetevőkre vonatkoznak. A nem a Microsofttól származó szoftveres összetevők és szolgáltatások, amelyek Azure Stack hub hardveres életciklus-állomásán futnak, a következő telemetria-szinteken kívül is kommunikálhatnak a felhőalapú szolgáltatásaikkal. Az Azure Stack hub hardveres megoldás-szolgáltatóval együttműködve Ismerje meg a telemetria-szabályzatot, valamint azt, hogy hogyan engedélyezheti vagy letilthatja.

A Windows és Azure Stack hub telemetria kikapcsolása letiltja az SQL-telemetria is. A Windows Server telemetria beállításainak következményeivel kapcsolatos további információkért tekintse meg a [Windows telemetria](https://aka.ms/winservtelemetry)szóló tanulmányt.

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: a telemetria szintjének beállítása a Windows beállításjegyzékben

Az Azure Stack hub üzembe helyezése előtt a Windows Rendszerleíróadatbázis-szerkesztővel manuálisan állíthatja be a telemetria szintjét a fizikai gazdaszámítógépen. Ha már létezik felügyeleti házirend, például Csoportházirend, akkor felülbírálja ezt a beállításjegyzék-beállítást.

Mielőtt telepítené Azure Stack hubot a fejlesztői csomag gazdagépén, indítsa el a CloudBuilder. vhdx parancsot, és futtassa a következő parancsfájlt egy emelt szintű PowerShell-ablakban:

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

- Engedélyezés:`Set-Telemetry -Enable`
- A letiltáshoz:`Set-Telemetry -Disable`

PARAMÉTER részletei:
- `.PARAMETER Enable`– A telemetria-adatok feltöltésének bekapcsolása
- `.PARAMETER Disable`– A telemetria-adatok feltöltésének kikapcsolása  

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

[Azure Stack hub regisztrálása az Azure-ban](azure-stack-registration.md)