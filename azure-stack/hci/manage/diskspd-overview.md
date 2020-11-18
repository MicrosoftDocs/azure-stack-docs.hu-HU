---
title: A DISKSPD használata a munkaterhelés tárolási teljesítményének teszteléséhez
description: Ez a témakör útmutatást nyújt arról, hogyan használható a DISKSPD a számítási feladatok tárolási teljesítményének tesztelésére.
author: jasonnyi
ms.author: jasonyi
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 03d5bf97e29009c67e9520ea59a802c55659db3b
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94811230"
---
# <a name="use-diskspd-to-test-workload-storage-performance"></a>A DISKSPD használata a munkaterhelés tárolási teljesítményének teszteléséhez

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör útmutatást nyújt arról, hogyan használható a DISKSPD a számítási feladatok tárolási teljesítményének tesztelésére. Van beállítva Azure Stack HCI-fürt, az összes készen áll. Nagyszerű, de Honnan tudhatja, hogy a megígért teljesítmény-mérőszámokat használja-e, legyen szó késésről, átviteli sebességről vagy IOPS? Ez akkor fordulhat elő, ha a DISKSPD-re szeretne csatlakozni. A témakör elolvasása után megismerheti a DISKSPD futtatását, a paraméterek egy részhalmazát, a kimenet értelmezését és a számítási feladatok tárolási teljesítményét befolyásoló változók általános megismerését.

## <a name="what-is-diskspd"></a>Mi az a DISKSPD?
A DISKSPD egy I/O-generáló, parancssori eszköz a Micro-benchmarking szolgáltatáshoz. Igen, mit jelent mindez a feltételek? Egy Azure Stack HCI-fürtöt vagy fizikai kiszolgálót beállító személynek van valamilyen oka. Előfordulhat, hogy egy webes üzemeltetési környezetet állít be, vagy virtuális asztalokat futtat az alkalmazottak számára. A valós használati eset lehet, hogy a tényleges alkalmazás üzembe helyezése előtt valószínűleg szimulálni szeretné a teszteket. Az alkalmazás valós forgatókönyvben való tesztelése azonban gyakran nehéz – ez az a hely, ahol a DISKSPD bekerül.

A DISKSPD egy olyan eszköz, amelyet testreszabhat saját szintetikus számítási feladatainak létrehozásához, és tesztelheti az alkalmazást az üzembe helyezés előtt. Az eszközhöz tartozó nagyszerű dolog, hogy megadja a paraméterek konfigurálását és finomhangolását a valós számítási feladathoz hasonló konkrét forgatókönyv létrehozásához. A DISKSPD megtekintheti, hogy a rendszer milyen képességgel rendelkezik az üzembe helyezés előtt. A DISKSPD a legfontosabb, hogy egyszerűen egy csomó olvasási és írási műveletet ad ki.

Most már ismeri a DISKSPD, de mikor érdemes használni? A DISKSPD bonyolult munkaterheléseket emulál. A DISKSPD azonban nagyszerű, ha a munkaterhelést nem közelíti meg szorosan az egyszálas fájlmásolás, és szüksége van egy egyszerű eszközre, amely elfogadható alapértékeket eredményez.

## <a name="quick-start-install-and-run-diskspd"></a>Gyors üzembe helyezés: a DISKSPD telepítése és futtatása
További lépések nélkül kezdjük az első lépéseket:

1. A felügyeleti SZÁMÍTÓGÉPén nyissa meg a PowerShellt rendszergazdaként, és kapcsolódjon a DISKSPD használatával tesztelni kívánt számítógéphez, majd írja be a következő parancsot, majd nyomja le az ENTER billentyűt.

     ```powershell
     Enter-PSSession -ComputerName <TARGET_COMPUTER_NAME>
    ```

    Ebben a példában egy "csomópont1" nevű virtuális gépet (VM) futtatunk.

1. A DISKSPD eszköz letöltéséhez írja be a következő parancsokat, majd nyomja le az ENTER billentyűt:

     ```powershell
     $client = new-object System.Net.WebClient
    ```

     ```powershell
     $client.DownloadFile("https://github.com/Microsoft/diskspd/releases/download/v2.0.21a/DiskSpd-2.0.21a.zip","<ENTER_PATH>\DiskSpd-2.0.21a.zip")
    ```

1. Az alábbi paranccsal bontsa ki a letöltött fájlt:

     ```powershell
     Expand-Archive -LiteralPath <ENTERPATH>\DiskSpd-2.0.21a.zip -DestinationPath C:\DISKSPD
    ```

1. Módosítsa a könyvtárat a DISKSPD könyvtárba, és keresse meg a Windows operációs rendszernek azt a megfelelő végrehajtható fájlját, amelyen a célszámítógép fut.

    Ebben a példában az AMD64 verziót használjuk.

    > [!NOTE]
    > A DISKSPD eszközt közvetlenül is letöltheti a GitHub- [tárházból](https://github.com/microsoft/diskspd) , amely tartalmazza a nyílt forráskódú kódot, valamint egy wiki-oldalt, amely az összes paramétert és specifikációt részletezi. A tárházban a **kiadások** területen válassza a zip-fájl automatikus letöltésére szolgáló hivatkozást.

    A ZIP-fájlban három almappa jelenik meg: amd64 (64 bites rendszerek), x86 (32 bites rendszerek) és ARM64 (ARM-rendszerek). Ezekkel a beállításokkal minden Windows-ügyfél vagy-kiszolgáló verziója futtathatja az eszközt.

    :::image type="content" source="media/diskspd/download-directory.png" alt-text="A DISKSPD. zip fájl letöltéséhez szükséges könyvtár." lightbox="media/diskspd/download-directory.png":::

1. Futtassa a DISKSPD a következő PowerShell-paranccsal. Cserélje le a szögletes zárójelben lévő mindent, beleértve a megfelelő beállításokkal együtt zárójeleket is.

    ```powershell
     .\[INSERT_DISKSPD_PATH] [INSERT_SET_OF_PARAMETERS] [INSERT_CSV_PATH_FOR_TEST_FILE] > [INSERT_OUTPUT_FILE.txt]
    ```

    Az alábbi példa egy parancs futtatására használható:

    ```powershell
     .\diskspd -t2 -o32 -b4k -r4k -w0 -d120 -Sh -D -L -c5G C:\ClusterStorage\test01\targetfile\IO.dat > test01.txt
    ```

    > [!NOTE]
    > Ha nem rendelkezik tesztelési fájllal, a **-c** paraméter használatával hozzon létre egyet. Ha ezt a paramétert használja, ügyeljen arra, hogy a fájl nevét adja meg az elérési út megadásakor. Például: [INSERT_CSV_PATH_FOR_TEST_FILE] = C:\ClusterStorage\CSV01\IO.dat. A példában szereplő parancsban az IO. dat a test fájl neve, a test01.txt pedig a DISKSPD kimeneti fájljának neve.

## <a name="specify-key-parameters"></a>Kulcs paramétereinek megadása
Nos, ez egyszerű volt? Sajnos ennél több van. Csomagolja ki a csomagot. Először is vannak olyan paraméterek, amelyekkel bütykölhető, és a megadott adatokkal is rendelkezhet. Azonban a következő alapkonfiguráció-paramétereket használtuk:

> [!NOTE]
> A DISKSPD paraméterek megkülönböztetik a kis-és nagybetűket.

**-T2**: megadja a szálak számát a cél/tesztelési fájl alapján. Ez a szám gyakran a CPU-magok száma alapján történik. Ebben az esetben két szálat használtak az összes CPU-mag kihangsúlyozása érdekében.

**-O32**: a függőben lévő I/O-kérések számát adja meg egy szál alapján. Ezt a várólista mélységét is nevezik, és ebben az esetben 32-et használtak a processzor kihangsúlyozása érdekében.

**-b4K**: a blokk méretét adja meg bájtban, KiB, MIB vagy GIB értékben. Ebben az esetben a 4K-blokk méretét egy véletlenszerű I/O-teszt szimulálása céljából használták.

**-r4K**: Ez azt jelzi, hogy a véletlenszerű I/O-érték a megadott méretnél (bájt, KiB, MIB, GIB vagy blokkok) van igazítva (felülbírálja a **-s** paramétert). A rendszer a gyakori 4K-bájtok méretét használta a blokk méretének megfelelő igazításhoz.

**-W0**: megadja az írási kérelmek százalékos arányát (a-w0 értéke 100% Read). Ebben az esetben 0%-os írást használtak egy egyszerű teszt céljára.

**-D120**: megadja a teszt időtartamát, amely nem tartalmazza a lehűtés vagy a bemelegítést. Az alapértelmezett érték 10 másodperc, de ajánlott legalább 60 másodpercet használni bármilyen súlyos számítási feladathoz. Ebben az esetben a kiugró értékek minimalizálásához 120 másodpercet használtak.

**-SUW**: letiltja a szoftveres és a hardveres írási gyorsítótárazást (egyenértékű a **-sh** értékkel).

**-D**: rögzíti a IOPS statisztikáit, például a szórást ezredmásodpercben (/szálban, célként).

**-L**: mértékek késleltetési statisztikái.

**-c5g**: a tesztben használt minta fájlméret beállítása. A készlet mérete bájt, KiB, MiB, GiB vagy blokkok lehet. Ebben az esetben egy 5 GB-nyi célfájl lett használva.

A paraméterek teljes listájáért tekintse meg a [GitHub-tárházat](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

## <a name="understand-the-environment"></a>A környezet megismerése
A teljesítmény nagy mértékben függ a környezettől. Mi a környezet? A specifikációnk egy Azure Stack HCI-fürtöt foglal magában, amely a következő: Storage Pool és Közvetlen tárolóhelyek (S2D). Pontosabban öt virtuális gép van: DC, csomópont1, Csomópont2, csomópont3 és a felügyeleti csomópont. A fürt egy három csomópontot tartalmazó fürt, amely egy háromutas tükrözött rugalmassági struktúrával rendelkezik. Ezért a rendszer három adatmásolatot tart fenn. A fürt minden "csomópontja" egy Standard_B2ms virtuális gép, amelynek maximális IOPS-korlátja 1920. Az egyes csomópontokon belül négy prémium P30 SSD-meghajtó van, amely maximális IOPS 5000-os korláttal rendelkezik. Végül minden SSD-meghajtó 1 TB memóriával rendelkezik.

A tesztelési fájlt az egyesített névtér alatt, a Fürt megosztott kötete (CSV) biztosítja (C:\ClusteredStorage) a meghajtók teljes készletének használatára.

>[!NOTE]
> A példában szereplő környezet *nem* rendelkezik Hyper-V-vagy beágyazott virtualizálási struktúrával.

Ahogy látni fogja, a virtuális gép vagy a IOPS teljes mértékben függetlenül elérheti a maximális értéket vagy a sávszélesség felső határát. Ezért fontos, hogy megértse a virtuális gép méretét és a meghajtó típusát, mivel mindkettő rendelkezik a maximális IOPS korláttal és a sávszélesség felső határával. Ez az információ segít a szűk keresztmetszetek megtalálásában és a teljesítménybeli eredmények megismerésében. Ha többet szeretne megtudni arról, hogy milyen méretűek lehetnek a számítási feladatok, tekintse meg a következő forrásokat:

- [A virtuális gépek mérete](https://docs.microsoft.com/azure/virtual-machines/sizes-general?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json)
- [Lemez típusa](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="understand-the-output"></a>A kimenet ismertetése
A paraméterek és a környezet megismerésével feldolgozhatja a kimenet értelmezését. Először is a korábbi teszt célja, hogy a IOPS max. Így vizuálisan láthatja, hogy eléri-e a mesterséges IOPS korlátot az Azure-on belül. Ha grafikusan szeretné megjeleníteni a teljes IOPS, használja a Windows felügyeleti központ vagy a Feladatkezelő eszközt.

Az alábbi ábrán látható, hogy a DISKSPD folyamat hogyan néz ki a példaként szolgáló környezetben. Egy példa a nem koordinátori csomópontról 1 MiB-írási műveletre mutat. A háromutas rugalmassági struktúra, valamint a nem koordinátor csomópontok művelete két hálózati ugrást eredményez, ami csökkenti a teljesítményt. Ha kíváncsi a koordinátori csomópontra, ne aggódjon! Erről [a következő témakörben](#things-to-consider) tájékozódhat:. A piros négyzetek a virtuális gépet és a szűk keresztmetszeteket jelölik.

:::image type="content" source="media/diskspd/environment.png" alt-text="A DISKSPD-vel való teljesítmény tesztelésére szolgáló mintavételi környezet." lightbox="media/diskspd/environment.png":::

Most, hogy megismerte a vizuális ismereteket, vizsgáljuk meg a. txt fájl kimenetének négy fő részét:
1. Bemeneti beállítások
   
    Ez a szakasz a futtatott parancsot, a bemeneti paramétereket és a teszt futtatásának további részleteit ismerteti.

    :::image type="content" source="media/diskspd/command-input-parameters.png" alt-text="Példa a kimenetre parancs-és bemeneti paramétereket mutat." lightbox="media/diskspd/command-input-parameters.png":::

1. CPU-kihasználtság részletei
   
    Ez a szakasz olyan információkat tartalmaz, mint például a tesztelési idő, a szálak száma, a rendelkezésre álló processzorok száma, valamint a CPU-mag átlagos kihasználtsága a tesztelés során. Ebben az esetben két CPU-mag van, amelyek átlaga körülbelül 4,67%-os használatot.

    :::image type="content" source="media/diskspd/cpu-details.png" alt-text="Példa a CPU részleteire." lightbox="media/diskspd/cpu-details.png":::

1. Összes I/O
   
    Ennek a szakasznak három alszakasza van. Az első szakasz kiemeli a teljes teljesítményadatokat, beleértve az olvasási és írási műveleteket is. A második és a harmadik szakasz az olvasási és írási műveleteket külön kategóriákra osztja fel.

    Ebben a példában láthatja, hogy a teljes I/O-mennyiség 234408 volt a 120-Second időtartam alatt. Így IOPS = 234408/120 = 1953,30. Az átlagos késés 32,763 ezredmásodperc volt, és az átviteli sebesség 7,63 MiB/s volt. A korábbi információk alapján tudjuk, hogy az 1953,30 IOPS a Standard_B2ms virtuális gép 1920-es IOPS-korlátozásának közelében van. Nem hiszem? Ha ezt a tesztet különböző paraméterek használatával futtatja újra, például a várólista mélységének növelésével, a rendszer azt tapasztalja, hogy az eredmények ezen a számon továbbra is korlátozottak.

    Az utolsó három oszlop a IOPS szórását mutatja a 17,72-as számnál (a-D paraméter alapján), a késés szórását 20,994 ezredmásodpercben (from-L paraméter) és a fájl elérési útját.

      :::image type="content" source="media/diskspd/total-io.png" alt-text="Példa a teljes I/O-teljesítményadatokat mutatja." lightbox="media/diskspd/total-io.png":::

    Az eredményekből gyorsan meghatározhatja, hogy a fürtkonfiguráció borzasztó-e. Láthatja, hogy az 1920-os virtuális gép a 5000-es SSD-korlátozás előtt elérte a VM-korlátozást. Ha a virtuális gép helyett az SSD korlátozta, akkor akár 20000 IOPS-t (4 meghajtót * 5000) is kihasználhat, ha a tesztelési fájlt több meghajtón is át szeretné állítani.

    A végén el kell döntenie, hogy az adott számítási feladathoz milyen értékek elfogadhatók. Az alábbi ábra néhány fontos kapcsolatot mutat be a kompromisszumok megvizsgálása érdekében:

    :::image type="content" source="media/diskspd/tradeoffs.png" alt-text="Az ábra a munkaterhelés-kapcsolati kompromisszumokat mutatja." lightbox="media/diskspd/tradeoffs.png":::

    Az ábra második kapcsolata fontos, és néha kis törvénynek is nevezik. A törvény bemutatja, hogy három olyan jellemző van, amelyek a folyamat viselkedését szabályozzák, és hogy csak az egyiket kell megváltoztatnia a másik kettő befolyásolására, így a teljes folyamatra. Így ha nem elégedett a rendszer teljesítményével, három dimenziója van, amely befolyásolhatja azt. A Little 's Law azt diktálja, hogy a példánkban a IOPS a "teljesítmény" (bemeneti kimeneti műveletek másodpercenkénti száma), a késés a "Sorbanállási idő", a várólista mélysége pedig a "leltár".

1. Késleltetési percentilis elemzése
   
    Ez az utolsó szakasz a minimális értéktől a maximális értékig terjedően részletezi a tárolási teljesítmény műveleti típusának százalékos késleltetését.

    Ez a szakasz azért fontos, mert meghatározza a IOPS minőségét. Azt mutatja, hogy az I/O-műveletek hányan képesek elérni egy bizonyos késési értéket. Ez a százalékos érték arra az esetre, ha az adott percentilis elfogadható késését szeretné eldönteni.

    Emellett a "Nines" a Kilencek számát is említi. Például a "3 – Nines" érték megegyezik a esetek 99% percentilis értékével. A kilences érték azt mutatja, hogy hány I/O művelet futott le az adott százalékos értéknél. Végül egy olyan ponthoz jut, ahol már nem érdemes komolyan venni a késési értékeket. Ebben az esetben láthatja, hogy a késési értékek "4 – kilenc" után állandóak maradnak. Ezen a ponton a késési érték az 234408 műveletből csak egy I/O-műveleten alapul.

    :::image type="content" source="media/diskspd/storage-performance.png" alt-text="Példa a tárolási teljesítmény típusának százalékos késleltetését jeleníti meg." lightbox="media/diskspd/storage-performance.png":::

## <a name="things-to-consider"></a>Megfontolandó dolgok
Most, hogy megkezdte a DISKSPD használatát, több dolgot is figyelembe kell vennie a valós tesztelési eredmények megismerése érdekében. Ezek közé tartozik a beállított paraméterek, a tárolóhelyek állapota és a változók, a CSV-tulajdonos, valamint a DISKSPD és a fájlmásolás közötti különbség.

### <a name="diskspd-vs-real-world"></a>DISKSPD és valós világ
A DISKSPD mesterséges tesztelése viszonylag összehasonlítható eredményeket biztosít a valós számítási feladatokhoz. Azonban Kiemelt figyelmet kell fordítania a beállított paraméterekre, valamint attól, hogy megfelelnek-e a valós forgatókönyvnek. Fontos tisztában lenni azzal, hogy a szintetikus munkaterhelések soha nem fogják tökéletesen képviselni az alkalmazás valós számítási feladatait az üzembe helyezés során.

### <a name="preparation"></a>Előkészítés
A DISKSPD-teszt futtatása előtt néhány javasolt művelet van. Ezek közé tartozik a tárolóhely állapotának ellenőrzése, az erőforrás-használat ellenőrzése, hogy egy másik program ne zavarja a tesztet, és ha további adatokat szeretne gyűjteni, készítse elő a Teljesítményfigyelőt. Mivel azonban ennek a témakörnek a célja, hogy gyorsan lekérje a DISKSPD, nem tárgyalja a műveletek sajátosságait. További információ: a [tárolóhelyek teljesítményének tesztelése szintetikus számítási feladatok használatával a Windows Serveren](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn894707(v=ws.11)).

### <a name="variables-that-affect-performance"></a>A teljesítményt befolyásoló változók
A tárolási teljesítmény kényes dolog. Ez azt jelenti, hogy számos változó befolyásolja a teljesítményt. Ezért valószínű, hogy olyan számmal találkozhat, amely nem felel meg az elvárásainak. A következőkben néhány olyan változó szerepel, amely hatással van a teljesítményre, bár ez nem egy átfogó lista:
- Hálózati sávszélesség
- Rugalmassági választás
- Tárolási lemez konfigurációja: NVME, SSD, HDD
- I/O-puffer
- Gyorsítótár
- RAID-konfiguráció
- Hálózati ugrások
- Merevlemez orsójának sebessége

### <a name="csv-ownership"></a>CSV-tulajdonos
A csomópontok a kötet tulajdonosaként vagy a **koordinátori** csomópontként is ismertek (a nem koordinátor csomópontok olyan csomópontok, amelyek nem rendelkeznek egy adott kötettel). Minden standard kötet hozzá van rendelve egy csomóponthoz, és a többi csomópont a hálózati ugrások segítségével érheti el ezt a standard kötetet, ami lassabb teljesítményt (nagyobb késést) eredményez.

Hasonlóképpen, egy Fürt megosztott kötete (CSV) is rendelkezik egy "tulajdonossal". A CSV azonban a "dinamikus" abban az értelemben, hogy a rendszer megkezdi a változásokat, és minden egyes újraindításkor megváltoztatja a tulajdonost (RDP). Ennek eredményeképpen fontos ellenőrizni, hogy a DISKSPD a CSV-t birtokló koordinátori csomópontról fut-e. Ha nem, akkor lehet, hogy manuálisan módosítania kell a CSV-tulajdonost.

A CSV tulajdonjogának megerősítése:
1. A tulajdonjog ellenőrzéséhez futtassa a következő PowerShell-parancsot:

    ```powershell
     Get-ClusterSharedVolume
    ```

1. Ha a CSV-fájl tulajdonjoga helytelen (például a Csomópont1-on, de a Csomópont2 tulajdonosa a CSV), akkor a következő PowerShell-parancs futtatásával helyezze át a CSV-t a megfelelő csomópontra:

    ```powershell
     Get-ClusterSharedVolume <INSERT_CSV_NAME> | Move-ClusterSharedVolume <INSERT _NODE_NAME>
    ```
### <a name="file-copy-vs-diskspd"></a>Fájlmásolás és DISKSPD
Egyesek úgy vélik, hogy egy gigantikus fájl másolásával és beillesztésével, valamint a folyamat időtartamának mérésével képesek "a tárolási teljesítmény tesztelésére". Ennek a megközelítésnek a fő oka a legvalószínűbb, mert egyszerű és gyors. Az ötlet nem stimmel abban az értelemben, hogy egy adott számítási feladatot tesztel, de nehéz kategorizálni ezt a metódust "a tárolási teljesítmény tesztelése".

Ha a valós cél a fájlmásolás teljesítményének tesztelése, akkor ez a módszernek tökéletesen érvényes oka lehet. Ha azonban a cél a tárolási teljesítmény mérése, javasoljuk, hogy ezt a módszert ne használja. A fájlmásolás folyamatát úgy tekintheti meg, hogy a párhuzamos jellemző "paraméterek" (például üzenetsor, stb.) eltérő készletét használja.

Az alábbi rövid összefoglalás azt ismerteti, hogy miért érdemes a fájl másolását a tárolási teljesítmény mérésére használni, és nem biztosítja a keresett eredményeket:
- **Előfordulhat, hogy a fájlok másolása nem optimalizált,** Kétféle párhuzamossági szint létezik, egy belső és egy másik külső. Belsőleg, ha a fájl másolása egy távoli cél számára történik, a CopyFileEx motor bizonyos párhuzamos alkalmaz. Külsőleg a CopyFileEx motor meghívásának különböző módjai. A Fájlkezelőből például egyetlen szálból másolhatók, de a Robocopy többszálú. Ezen okok miatt fontos tisztában lennie azzal, hogy milyen hatással van a tesztre.
- **Minden másolat két oldalból áll.** Ha egyszerűen másol és beilleszt egy fájlt, lehet, hogy két lemezt használ: a forrás lemezt és a céllemez. Ha az egyik lassabb, mint a másik, lényegében a lassabb lemez teljesítményét méri. Más esetekben előfordulhat, hogy a forrás, a cél és a másolási motor közötti kommunikáció egyedi módon hatással lehet a teljesítményre.
    
    További információ: a [fájlok másolásának használata a tárolási teljesítmény mérésére](https://docs.microsoft.com/archive/blogs/josebda/using-file-copy-to-measure-storage-performance-why-its-not-a-good-idea-and-what-you-should-do-instead?ranMID=24542&ranEAID=je6NUbpObpQ&ranSiteID=je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q&epi=je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q&irgwc=1&OCID=AID2000142_aff_7593_1243925&tduid=%28ir__rcvu3tufjwkftzjukk0sohzizm2xiezdpnxvqy9i00%29%287593%29%281243925%29%28je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q%29%28%29&irclickid=_rcvu3tufjwkftzjukk0sohzizm2xiezdpnxvqy9i00).

## <a name="experiments-and-common-workloads"></a>Kísérletek és gyakori számítási feladatok
Ez a szakasz néhány példát, kísérleteket és számítási feladatokat tartalmaz.

### <a name="confirming-the-coordinator-node"></a>A koordinátor csomópontjának megerősítése
Ahogy korábban említettük, ha a jelenleg tesztelés alatt álló virtuális gép nem rendelkezik a CSV-vel, a teljesítmény csökkenése (IOPS, átviteli sebesség és késés) jelenik meg, ha a csomópont tulajdonosa a CSV-fájl. Ennek az az oka, hogy az I/O-műveletek minden egyes kiosztásakor a rendszer egy hálózati ugrást hajt végre a koordinátor csomóponton a művelet végrehajtásához.

Három csomópontos, háromutas tükrözés esetén az írási műveletek mindig hálózati ugrást tesznek lehetővé, mivel a három csomóponton lévő összes meghajtón tárolt adatok tárolására van szükség. Ezért az írási műveletek hálózati ugrást tesznek elérhetővé függetlenül. Ha azonban más rugalmassági struktúrát használ, ez változhat.

Alább bemutatunk egy példát:
- **Helyi csomóponton fut:** .\DiskSpd-2.0.21a\amd64\diskspd.exe-T4-O32-b4k-R4K-W0-sh-D-L C:\ClusterStorage\test01\targetfile\IO.dat
- Nem **helyi csomóponton fut:** .\DiskSpd-2.0.21a\amd64\diskspd.exe-T4-O32-b4k-R4K-W0-sh-D-L C:\ClusterStorage\test01\targetfile\IO.dat

Ebből a példából megtudhatja, hogy az alábbi ábra a késés csökkenését, a IOPS növekedését és az átviteli sebesség növekedését eredményezte-e, ha a koordinátor csomópontja a CSV-t tartalmazza.

:::image type="content" source="media/diskspd/coordinator-node-data.png" alt-text="Példa a koordinátori csomópont adatkimenetét mutatja." lightbox="media/diskspd/coordinator-node-data.png":::

### <a name="online-transaction-processing-oltp-workload"></a>Online tranzakció-feldolgozási (OLTP) munkaterhelés
Online tranzakciós feldolgozási (OLTP) munkaterhelés-lekérdezések (frissítés, Beszúrás, törlés), Fókuszban a tranzakció-orientált feladatok. Az online analitikus feldolgozáshoz (OLAP) képest a OLTP a tárolási késéstől függ. Mivel az egyes műveletek kis I/O-műveleteket okoznak, a másodpercenkénti műveletek száma.

A OLTP számítási feladatok tesztelésével a véletlenszerű, kisméretű I/O-teljesítményre koncentrálhat. Ezekhez a tesztekhez koncentráljon arra, hogy meddig lehet leküldeni az átviteli sebességet az elfogadható késések fenntartása mellett.

A számítási feladatok teszteléséhez szükséges alapszintű kialakításnak legalább a következőket kell tartalmaznia:
- 8 KB-os blokk mérete => hasonlít az adatfájlok SQL Server által használt oldalméret méretére
- 70% olvasás, 30% írás => a tipikus OLTP viselkedéshez hasonlít

### <a name="online-analytical-processing-olap-workload"></a>Online analitikus feldolgozási (OLAP) munkaterhelés
Az OLAP-munkaterhelések az adatok lekérésére és elemzésére összpontosítanak, így a felhasználók összetett lekérdezéseket végezhetnek a többdimenziós adatok kinyeréséhez. A OLTP ellentétben ezek a számítási feladatok nem érzékenyek a tárolási késésekre. Nagy mennyiségű műveletet hangsúlyoznak a sávszélességgel kapcsolatos gondok nélkül. Ennek eredményeképpen az OLAP-munkaterhelések gyakran hosszú feldolgozási időt eredményeznek.

Létrehozhat egy OLAP számítási feladatot, amely szekvenciális, nagyméretű I/O-teljesítményre koncentrálhat. Ezekben a tesztekben a IOPS száma helyett a másodpercenként feldolgozott adatmennyiségre koncentrálhat. A késési követelmények szintén kevésbé fontosak, de ez szubjektív.

A számítási feladatok teszteléséhez szükséges alapszintű kialakításnak legalább a következőket kell tartalmaznia:
- 512 KB-os blokk mérete => hasonlít az I/O-méretre, ha a SQL Server egy 64-adatoldalból álló köteget tölt be egy táblázatos vizsgálathoz a olvasható eljárás használatával.
- 1 szál/fájl => jelenleg csak egy szálra kell korlátoznia a tesztelést, mivel a DISKSPD során problémák merülhetnek fel több szekvenciális szál tesztelésekor.
    Ha egynél több szálat használ, mondjuk kettőt és az **-s** paramétert, a szálak a nem determinisztikus módon megkezdik az I/O-műveletek kiküldését ugyanazon a helyen belül. Ennek az az oka, hogy mindegyik nyomon követi a saját szekvenciális eltolását.

    A probléma megoldásához két "megoldás" van:
    - Az első megoldás a **-si** paraméter használatát foglalja magában. Ezzel a paraméterrel mindkét szál egyetlen egymással összekapcsolt eltolással rendelkezik, így a szálak a célfájl elérésének egyetlen szekvenciális mintáját adnak ki. Ez lehetővé teszi, hogy a fájl egyetlen pontján sem lehessen egynél többször működni. Mivel azonban továbbra is versenyben vannak egymással, hogy az I/O-műveleteket a várólistára bocsássák, a műveletek eltérhetnek a rendeléstől.

        Ez a megoldás jól működik, ha egy szál CPU-korlátozott lesz. Érdemes lehet második szálat is felvenni egy második CPU-mag fölé, hogy a CPU-rendszer számára nagyobb tárolási I/O-teljesítményt nyújtson a további telítettség érdekében.

    - A második megoldás a-T használatával jár\<offset>\. Ez lehetővé teszi, hogy megadhatja az eltolási méretet (több-I/O-hézagot) a különböző szálak által ugyanazon a célhelyen végrehajtott I/O-műveletek között. Például a szálak általában a 0 eltolásnál kezdődnek, de ez a specifikáció lehetővé teszi a két szál közötti távolságot, így azok nem fedik át egymást. A többszálú környezetekben a szálak valószínűleg a működő cél különböző részein lesznek, és ez a helyzet szimulálása.

## <a name="next-steps"></a>Következő lépések
További információt és részletes példákat a rugalmassági beállítások optimalizálásával kapcsolatban a következő témakörben talál:
- [OLTP és OLAP](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn894707(v=ws.11))
- [Rugalmassági választás](https://techcommunity.microsoft.com/t5/storage-at-microsoft/volume-resiliency-and-efficiency-in-storage-spaces-direct/ba-p/425831)
