---
title: Azure Stack HCI-fürt ellenőrzése
description: Ismerje meg a fürt érvényesítésének fontosságát, és azt, hogy mikor futtassa azt egy meglévő Azure Stack HCI-fürtön. Tekintse át a frissített kiszolgálófürt hibaelhárítási forgatókönyveit.
author: JohnCobb1
ms.author: v-johcob
ms.topic: article
ms.date: 10/16/2020
ms.openlocfilehash: fe49df76ccb2a90849587acd5d4df7a41e329efb
ms.sourcegitcommit: 301e571626f8e85556d9eabee3f385d0b81fdef4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92157699"
---
# <a name="validate-an-azure-stack-hci-cluster"></a>Azure Stack HCI-fürt ellenőrzése

>A következőkre vonatkozik: Azure Stack HCI, Version v20H2; Windows Server 2019

Ez a cikk azt ismerteti, hogy miért fontos a fürt érvényesítése, és mikor kell futtatni egy meglévő Azure Stack HCI-fürtön. Javasoljuk, hogy végezze el a fürt érvényesítését a következő elsődleges forgatókönyvek esetében:
- Kiszolgálófürt üzembe helyezése után futtassa az validate-DCB eszközt a hálózatkezelés teszteléséhez.
- A kiszolgálófürt frissítése után a forgatókönyvtől függően futtassa mindkét ellenőrzési lehetőséget a fürttel kapcsolatos problémák elhárításához.
- Miután beállította a replikálást a Storage-replikával, ellenőrizze, hogy a replikálás normálisan folytatódjon-e, ha néhány konkrét eseményt ellenőriz, és egy pár parancsot futtat.
- A kiszolgálófürt létrehozása után futtassa az validate-DCB eszközt az éles környezetbe helyezés előtt.

    Az Azure Stack HCI-fürtök telepítésével kapcsolatos további tudnivalókért tekintse meg az [üzembe helyezés áttekintése](deployment-overview.md)című témakört.

## <a name="what-is-cluster-validation"></a>Mi a fürt érvényesítése?
A fürt érvényesítése a hardveres vagy konfigurációs problémák észlelésére szolgál, mielőtt a fürt bekerül az éles környezetbe. A fürt érvényesítése segít biztosítani, hogy a telepíteni kívánt Azure Stack HCI-megoldás valóban megbízható legyen. A fürt érvényesítését a konfigurált feladatátvevő fürtökön is használhatja diagnosztikai eszközként.

## <a name="specific-validation-scenarios"></a>Adott ellenőrzési forgatókönyvek
Ez a szakasz azokat a forgatókönyveket ismerteti, amelyekben az érvényesítés is szükséges vagy hasznos.

- **Ellenőrzés a fürt konfigurálása előtt:**
  - **A kiszolgálók egy csoportja készen áll a feladatátvevő fürtre:** Ez a legegyszerűbb ellenőrzési forgatókönyv. A hardver-összetevők (rendszerek, hálózatok és tárolók) csatlakoztatva vannak, de a rendszerek még nem működnek fürtként. Ebben a helyzetben a tesztek futtatása nem befolyásolja a rendelkezésre állást.
 
  - **Kiszolgálói virtuális gépek:** A fürtben virtualizált kiszolgálók esetében a fürt érvényesítését ugyanúgy futtassa, mint bármely más új fürtön. A funkció futtatásának követelménye ugyanaz, mint a következő:
    - "Gazda fürt", ahol feladatátvétel történik két fizikai számítógép között.
    - "Vendég fürt", ahol feladatátvétel történik a vendég operációs rendszerek között ugyanazon a fizikai számítógépen.
 
- **Ellenőrzés a fürt konfigurálását és használatát követően:**

  - **Kiszolgáló a fürthöz való hozzáadása előtt:** Amikor kiszolgálót ad hozzá egy fürthöz, erősen ajánlott a fürt érvényesítése. A fürt érvényesítésének futtatásakor adja meg mind a meglévő fürt tagjait, mind az új kiszolgálót.
  
  - **Meghajtók hozzáadásakor:** Ha további meghajtókat ad hozzá a fürthöz, amely eltér a meghibásodott meghajtók cseréjétől, illetve a meglévő meghajtókon alapuló virtuális lemezek vagy kötetek létrehozásával, akkor a fürt érvényesítésével ellenőrizze, hogy az új tároló megfelelően működik-e.

  - **A belső vezérlőprogramot vagy illesztőprogramokat befolyásoló módosítások** végrehajtásakor: Ha frissíti vagy módosítja a belső vezérlőprogramot vagy illesztőprogramokat befolyásoló fürtöt, futtatnia kell a fürt érvényesítését annak ellenőrzéséhez, hogy a hardver, a belső vezérlőprogram, az illesztőprogramok és a szoftverek új kombinációja támogatja-e a feladatátvevő fürt működését.

  - **A rendszer biztonsági másolatból történő visszaállítása után:** A rendszer biztonsági másolatból történő visszaállítása után futtassa a fürt érvényesítését annak ellenőrzéséhez, hogy a rendszer megfelelően működik-e a fürt részeként.

## <a name="validate-networking"></a>Hálózatkezelés ellenőrzése
A Microsoft validate-DCB eszköz úgy van kialakítva, hogy ellenőrizze az adatközpont-áthidaló (DCB) konfigurációját a fürtön. Ehhez az eszköz a várt konfigurációt veszi fel bemenetként, majd teszteli a fürt összes kiszolgálóját. Ez a szakasz ismerteti, hogyan telepítheti és futtathatja az validate-DCB eszközt, áttekintheti az eredményeket, és elháríthatja az eszköz által azonosított hálózati hibákat.

A hálózaton a távoli közvetlen memória-hozzáférés (RDMA) az átszervezett Ethernet (RoCE) segítségével DCB-technológiákat igényel a hálózati hálók veszteségmentes kihasználása érdekében. A iWARP használata esetén a DCB nem kötelező. A DCB konfigurálása azonban összetett lehet, és a pontos konfigurálásra van szükség az egészben:
- A fürt minden kiszolgálója
- Minden hálózati port, amely a RDMA forgalmat továbbítja a Hálón

### <a name="prerequisites"></a>Előfeltételek
- Az érvényesíteni kívánt kiszolgálófürt hálózati beállítási adatai, beleértve a következőket:
    - Gazdagép vagy kiszolgálófürt neve
    - Virtuális kapcsoló neve
    - Hálózati adapterek nevei
    - A Priority Flow Control (PFC) és a bővített átviteli kiválasztási (ETS) beállításai
- Internetkapcsolat az eszköz moduljának letöltéséhez a Microsofttól a Windows PowerShellben.

### <a name="install-and-run-the-validate-dcb-tool"></a>Az validate-DCB eszköz telepítése és futtatása
Az validate-DCB eszköz telepítése és futtatása:
1. A felügyeleti számítógépen nyisson meg egy Windows PowerShell-munkamenetet rendszergazdaként, majd az alábbi parancs használatával telepítse az eszközt.

    ```powershell
    Install-Module Validate-DCB
    ```

1. Fogadja el az NuGet-szolgáltató használatára vonatkozó kéréseket, és az eszköz telepítéséhez nyissa meg a tárházat.
1. Miután a PowerShell csatlakozik a Microsoft-hálózathoz az eszköz letöltéséhez, írja be a parancsot, `Validate-DCB` majd nyomja le az **ENTER** billentyűt az eszköz varázsló elindításához.

    > [!NOTE]
    > Ha nem tudja futtatni az validate-DCB Tool szkriptet, előfordulhat, hogy módosítania kell a PowerShell végrehajtási szabályzatait. Az Get-ExecutionPolicy parancsmaggal tekintheti meg az aktuális parancsfájl-végrehajtási házirend beállításait. A végrehajtási házirendek a PowerShellben való beállításával kapcsolatos információkért lásd: [a végrehajtási szabályzatok](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7).

1. Az Üdvözöljük a validate-DCB konfigurálása varázsló lapon válassza a **tovább**lehetőséget.
1. A fürtök és csomópontok lapon írja be az érvényesíteni kívánt kiszolgálófürt nevét, válassza a **megoldás** elemet a lapon lévő listában, majd válassza a **tovább**lehetőséget.

    :::image type="content" source="../media/validate/clusters-and-nodes.png" alt-text="Az validate-DCB konfigurációs varázsló fürtök és csomópontok lapja":::

1. Az adapterek lapon:
   1. Jelölje be a **csatolt vSwitch** jelölőnégyzetet, és írja be a vSwitch nevét.
   1. Az **adapter neve**mezőbe írja be az egyes fizikai hálózati adapterek nevét, a **gazdagép vNIC neve**alatt, a virtuális hálózati adapterek nevét (VNIC) és a **VLAN**-t az egyes ADAPTERek esetében használt VLAN-azonosítóval.
   1. Bontsa ki a **RDMA típusa** legördülő listát, és válassza ki a megfelelő protokollt: **RoCE** vagy **iWARP**. Állítsa be a **Jumbo-kereteket** is a hálózatának megfelelő értékre, majd válassza a **tovább**lehetőséget.

    :::image type="content" source="../media/validate/adapters.png" alt-text="Az validate-DCB konfigurációs varázsló fürtök és csomópontok lapja" lightbox="../media/validate/adapters.png":::

    > [!NOTE]
    > - Ha többet szeretne megtudni arról, hogy az SR-IOV Hogyan javítja a hálózati teljesítményt, tekintse meg [az egyszintű I/O-virtualizálás (SR-IOV) áttekintését](/windows-hardware/drivers/network/overview-of-single-root-i-o-virtualization--sr-iov-).

1. Az adatközpont-áthidaló lapon módosítsa az értékeket úgy, hogy azok megfeleljenek a szervezet beállításainak a **prioritás**, a **Házirend neve**és a **sávszélesség-foglalás**számára, majd válassza a **tovább**lehetőséget.

    :::image type="content" source="../media/validate/data-center-bridging.png" alt-text="Az validate-DCB konfigurációs varázsló fürtök és csomópontok lapja" lightbox="../media/validate/data-center-bridging.png":::

    > [!NOTE]
    > Ha a varázsló előző lapján a RoCE RDMA fölé kattint, az összes hálózati adapteren és switchports a hálózat megbízhatóságának DCB kell lennie.

1. A Mentés és üzembe helyezés lap **konfigurációs fájljának elérési útja** mezőjében mentse a konfigurációs fájlt. ps1 kiterjesztéssel egy olyan helyre, ahol később szükség esetén újra használhatja, majd válassza az **Exportálás** lehetőséget az validate-DCB eszköz futtatásának megkezdéséhez.

   - Igény szerint telepítheti a konfigurációs fájlt úgy, hogy végrehajtja a **konfiguráció telepítése a csomópontokra** szakaszt a lapon, amely lehetővé teszi, hogy egy Azure Automation fiók használatával telepítse a konfigurációt, majd érvényesítse azt. A Azure Automation megkezdéséhez tekintse meg [Azure Automation fiók létrehozása](/azure/automation/automation-quickstart-create-account) című témakört.

    :::image type="content" source="../media/validate/save-and-deploy.png" alt-text="Az validate-DCB konfigurációs varázsló fürtök és csomópontok lapja":::

### <a name="review-results-and-fix-errors"></a>Eredmények áttekintése és hibák elhárítása
Az validate-DCB eszköz két egységet eredményez:
1. [Globális egység] az eredmények listájának előfeltételei és követelményei a modális tesztek futtatásához.
1. [Modális egység] az eredmények visszajelzéseket adnak az egyes fürtcsomópontok konfigurációjával és ajánlott eljárásaival kapcsolatban.

Ez a példa egy adott kiszolgáló sikeres vizsgálatának eredményeit jeleníti meg az összes előfeltétel és a modális egység tesztek esetében, ha a sikertelen 0 értéket jelez.

:::image type="content" source="../media/validate/global-unit-and-modal-unit-results.png" alt-text="Az validate-DCB konfigurációs varázsló fürtök és csomópontok lapja":::

A következő lépések bemutatják, hogyan azonosíthatók a Jumbo-vNIC SMB02 és kijavíthatók a következők:
1. Az validate-DCB eszköz által beolvasott eredmények az 1 sikertelen számú hibát mutatják.

    :::image type="content" source="../media/validate/failed-count-error-1.png" alt-text="Az validate-DCB konfigurációs varázsló fürtök és csomópontok lapja":::

1. Az eredmények görgetésével megjeleníthető a vörös színű hiba, amely azt jelzi, hogy a gazdagép S046036 SMB02-vNIC tartozó Jumbo-csomag az alapértelmezett 1514-as értékre van beállítva, de a 9014 értékre kell állítani.

    :::image type="content" source="../media/validate/jumbo-packet-setting-error.png" alt-text="Az validate-DCB konfigurációs varázsló fürtök és csomópontok lapja":::

1. A vNIC SMB02 **speciális** tulajdonságainak áttekintése a gazdagép S046036 azt mutatja, hogy a Jumbo-csomag az alapértelmezett **letiltott**értékre van állítva.

    :::image type="content" source="../media/validate/hyper-v-advanced-properties-jumbo-packet-setting.png" alt-text="Az validate-DCB konfigurációs varázsló fürtök és csomópontok lapja":::

1. A hiba elhárításához engedélyezni kell a Jumbo-csomag funkcióját, és 9014 bájtra kell módosítania a méretét. Ha újra futtatja a vizsgálatot a gazdagépen, a S046036 a sikertelen 0 értéket adja vissza.

    :::image type="content" source="../media/validate/jumbo-packet-error-fix-confirmation.png" alt-text="Az validate-DCB konfigurációs varázsló fürtök és csomópontok lapja":::

Ha többet szeretne megtudni az validate-DCB eszköz által azonosított hibák elhárításáról, tekintse meg az alábbi videót.

> [!VIDEO https://www.youtube.com/embed/cC1uACvhPBs]

## <a name="validate-the-cluster"></a>A fürt ellenőrzése
A következő lépések végrehajtásával érvényesítheti a meglévő fürt kiszolgálóit a Windows felügyeleti központban.

1. A Windows felügyeleti központban az **összes kapcsolat**területen jelölje ki az érvényesíteni kívánt Azure stack HCI-fürtöt, majd válassza a **Csatlakozás**lehetőséget.

    A **Fürtfelügyelő irányítópultja** a fürt áttekintési információit jeleníti meg.

1. A **Fürtfelügyelő irányítópult** **eszközök**területén válassza a **kiszolgálók**elemet.
1. A **leltár** lapon jelölje ki a fürtben lévő kiszolgálókat, majd bontsa ki a **további** almenüt, és válassza a **fürt ellenőrzése**lehetőséget.
1. A **fürt ellenőrzése** előugró ablakban válassza az **Igen**lehetőséget.

    :::image type="content" source="../media/validate/validate-cluster-pop-up.png" alt-text="Az validate-DCB konfigurációs varázsló fürtök és csomópontok lapja":::

1. A **hitelesítő adatok biztonsági szolgáltatója (CredSSP)** előugró ablakban válassza az **Igen**lehetőséget.
1. Adja meg a hitelesítő adatait a **CredSSP** engedélyezéséhez, majd válassza a **Folytatás**lehetőséget.<br> A fürt érvényesítése a háttérben fut, és értesítést küld, amikor elkészült, és ekkor megtekintheti az ellenőrzési jelentést a következő szakaszban leírtak szerint.

> [!NOTE]
> A fürt kiszolgálóinak ellenőrzése után biztonsági okokból le kell tiltania a CredSSP-t.

### <a name="disable-credssp"></a>CredSSP letiltása
A kiszolgálófürt sikeres ellenőrzése után le kell tiltania az egyes kiszolgálókon a hitelesítő adatok biztonsági támogató szolgáltató (CredSSP) protokollját biztonsági okokból. További információ: [CVE-2018-0886](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-0886).

1. A Windows felügyeleti központban a **minden kapcsolat**területen válassza ki az első kiszolgálót a fürtben, majd válassza a **Csatlakozás**lehetőséget.
1. Az **Áttekintés** lapon válassza a **CredSSP letiltása**lehetőséget, majd a **CredSSP letiltására** szolgáló előugró ablakban válassza az **Igen**lehetőséget.

    A 2. lépés eredménye eltávolítja a piros **CREDSSP engedélyező** szalagcímet a kiszolgáló **Áttekintés** oldalának tetején, és letiltja a CredSSP a többi kiszolgálón.

### <a name="view-validation-reports"></a>Ellenőrzési jelentések megtekintése
Most már készen áll a fürt-ellenőrzési jelentés megtekintésére.

Az ellenőrzési jelentések több módon is elérhetők:
- A **leltár** lapon bontsa ki a **további** almenü elemet, majd válassza az **ellenőrzési jelentések megtekintése**lehetőséget.


- A **Windows felügyeleti központ**jobb felső sarkában válassza az **értesítések** harang ikont az **értesítések** panel megjelenítéséhez.
Jelölje ki a **sikeresen érvényesített fürtre** vonatkozó értesítést, majd válassza a **Ugrás a feladatátvevő fürt ellenőrzési jelentésére**lehetőséget.

> [!NOTE]
> A kiszolgálófürt ellenőrzési folyamata hosszabb időt is igénybe vehet. A folyamat futása közben ne váltson másik eszközre a Windows felügyeleti központban. Az **értesítések** ablaktáblán a **fürt ellenőrzése** értesítés alatt látható állapotsor jelzi, hogy a folyamat mikor történt.

## <a name="validate-the-cluster-using-powershell"></a>A fürt ellenőrzése a PowerShell használatával
A Windows PowerShell használatával is futtathatja az ellenőrző teszteket a kiszolgálófürtön, és megtekintheti az eredményeket. A teszteket a fürt beállítása előtt és után is futtathatja.

Ha ellenőrző tesztet szeretne futtatni egy kiszolgálófürtön, adja ki a **Get-cluster** és a **test-cluster** <server clustername> PowerShell-parancsmagokat a felügyeleti számítógépről, vagy futtassa csak a **test-cluster** parancsmagot közvetlenül a fürtön:

```PowerShell
$Cluster = Get-Cluster -Name 'server-cluster1'
Test-Cluster -InputObject $Cluster -Verbose
```

További példákat és használati információkat a [test-cluster](/powershell/module/failoverclusters/test-cluster?view=win10-ps) dokumentációjában talál.

## <a name="validate-replication-for-storage-replica"></a>Tárolási replika replikálásának ellenőrzése
Ha tároló-replikát használ a kötetek replikálásához egy kifeszített fürtben vagy fürtben a fürtben, több esemény és parancsmag is használható a replikálás állapotának lekéréséhez. 

A következő forgatókönyvben a Storage-replikát úgy konfiguráltuk, hogy két helyhez hozzon létre replikációs csoportokat (RGs-ket), majd a hely1-ben (Kiszolgáló1, Kiszolgáló2) és a cél (replikált) kiszolgáló-csomópontok között a Site2 (Server3, Server4) is megadja az adatköteteket és a naplózási köteteket.

A hely1-beli Kiszolgáló1 replikálási folyamatának meghatározásához futtassa a Get-WinEvent parancsot, és vizsgálja meg a 5015, 5002, 5004, 1237, 5001 és 2200 eseményeket:

```powershell
Get-WinEvent -ComputerName Server1 -ProviderName Microsoft-Windows-StorageReplica -max 20
```

A Site2 Server3 a következő parancs futtatásával `Get-WinEvent` tekintheti meg a kapcsolat létrehozását bemutató tárolási replika eseményeket. Ez az esemény állapítja meg a másolt bájtok mennyiségét és az igénybe vett időt. Példa:

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | Where-Object {$_.ID -eq "1215"} | FL
```

A Site2 Server3 futtassa a parancsot, `Get-WinEvent` és vizsgálja meg az 5009, 1237, 5001, 5015, 5005 és 2200 eseményeket a feldolgozási folyamat megismerése érdekében. Ebben a sorozatban nem lehet hibajelzés. Sok 1237 esemény lesz – ezek a folyamat előrehaladását jelzik.

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

Másik lehetőségként a replikához tartozó célkiszolgáló-csoport jelzi a másolandó bájtok számát, és a PowerShell használatával kérdezhető le `Get-SRGroup` . Példa:

```powershell
(Get-SRGroup).Replicas | Select-Object numofbytesremaining
```

A Site2 csomópont-Server3 futtassa a következő parancsot, és vizsgálja meg a 5009, 1237, 5001, 5015, 5005 és 2200 eseményeket a replikálás előrehaladásának megismeréséhez. A hibák figyelmeztetése nem lehet. Azonban sok "1237" esemény fog megjelenni – ezek egyszerűen csak a folyamat előrehaladását jelzik.

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

Olyan folyamatjelző parancsfájlként, amely nem fog megszűnni:

```powershell
while($true) {
$v = (Get-SRGroup -Name "Replication2").replicas | Select-Object numofbytesremaining
[System.Console]::Write("Number of bytes remaining: {0}`r", $v.numofbytesremaining)
Start-Sleep -s 5
}
```

A kiterjesztett fürtön belüli replikációs állapot beszerzéséhez használja a következőt `Get-SRGroup` `Get-SRPartnership` :

```powershell
Get-SRGroup -Cluster ClusterS1
```

```powershell
Get-SRPartnership -Cluster ClusterS1
```

```powershell
(Get-SRGroup).replicas -Cluster ClusterS1
```

Miután megerősítették a sikeres adatreplikációt a helyek között, létrehozhat virtuális gépeket és más számítási feladatokat.

## <a name="see-also"></a>Lásd még
- Teljesítmény-tesztelés a szintetikus számítási feladatokhoz egy újonnan létrehozott tárolóhelyen DiskSpd.exe használatával. További információ: a [tárolóhelyek teljesítményének tesztelése szintetikus számítási feladatok használatával a Windows Serveren](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn894707(v=ws.11)).
- A Windows Server Assessment olyan ügyfeleink számára elérhető Premier szolgáltatás, akik a Microsofttal szeretnék áttekinteni a Windows Server 2019-es verziójának telepítését. További információért forduljon a Microsoft Premier szintű támogatáshoz. További információ: Első lépések a [Windows Server igény szerinti értékelésével (kiszolgáló, biztonság, Hyper-V, feladatátvevő fürt, IIS)](/services-hub/health/getting-started-windows-server).
