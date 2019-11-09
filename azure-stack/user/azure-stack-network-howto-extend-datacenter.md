---
title: Az adatközpont kiterjesztése Azure Stack hub-on | Microsoft Docs
description: Ismerje meg, hogyan bővítheti az adatközpontot Azure Stackon.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 92e82f549cddf51b1cbd6764cc122acc3ca8fdfc
ms.sourcegitcommit: ed44d477b9fd11573d1e0d1ed3a3c0ef4512df53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73845989"
---
# <a name="how-to-extend-the-data-center-on--azure-stack-hub"></a>Az adatközpont kiterjesztése Azure Stack hubhoz

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack hub Development Kit*

Ez a cikk Azure Stack hub tárolási infrastruktúrájának adatait ismerteti, amelyek segítségével eldöntheti, hogyan integrálhatja Azure Stack a meglévő hálózati környezetbe. Az adatközpont kibővítésével kapcsolatos általános vitát követően a cikk két különböző forgatókönyvet mutat be. Csatlakozhat egy Windows file Storage-kiszolgálóhoz. Egy Windows iSCSI-kiszolgálóhoz is csatlakozhat.

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>A Storage Azure Stack hub-ra való kiterjesztésének áttekintése

Vannak olyan helyzetek, amikor a nyilvános felhőben található adatok nem elegendőek. Lehet, hogy van egy nagy számítási igényű virtualizált adatbázis-munkaterhelés, a késések és a nyilvános felhőbe való oda-és visszautazási idő befolyásolhatja az adatbázis számítási feladatának teljesítményét. Előfordulhat, hogy a helyszíni adatok egy fájlkiszolgálón, NAS-on vagy iSCSI Storage-tömbben vannak tárolva, amelyet a helyszíni munkaterhelések számára kell elérni, és a helyszíni rendszernek kell megfelelnie a szabályozási vagy megfelelőségi célok teljesítéséhez.  Ez csupán két olyan forgatókönyv, amelyben az adatok a helyszínen találhatók, számos szervezet számára fontosak maradnak.

Tehát miért nem csak a Storage-fiókokba tartozó Azure Stack vagy a virtualizált fájlkiszolgálók belsejében futnak a Azure Stack rendszeren? Az Azure-ban eltérően Azure Stack a tárterület véges. A használathoz rendelkezésre álló kapacitás a megvásárolt csomópontok számától függ, és az Ön által használt csomópontok száma mellett teljes mértékben kihasználható. Mivel Azure Stack egy Hyper-konvergens megoldás, érdemes növelni a tárolási kapacitást a használati igények kielégítése érdekében, a csomópontok hozzáadásával is növelnie kell a számítási lábnyomot.  Ez potenciálisan költséges lehet, különösen abban az esetben, ha a felesleges kapacitásra van szükség, amely a Azure Stack rendszeren kívüli alacsony költségeket is felvehető.

Ez az alábbi forgatókönyvhöz nyújt segítséget. Hogyan csatlakozhat Azure Stack rendszerekhez, a Azure Stackon futó virtualizált számítási feladatokhoz, amelyek egyszerűen és hatékonyan, a hálózaton keresztül elérhető, a Azure Stackon kívüli tárolási rendszerekhez.

## <a name="design-for-extending-storage"></a>A tárterület bővítésének tervezése

Az ábrán egy olyan forgatókönyv látható, amelyben egyetlen virtuális gép fut, amely a munkaterhelést, a csatlakozást és a külső (a virtuális gép, a Azure Stack saját maga) tárolót használja az adatok olvasására/írására stb. Ebben a cikkben a fájlok egyszerű lekérésére koncentrálhat, de ezt a példát kiterjesztheti összetettebb forgatókönyvek, például az adatbázisfájlok távoli tárolása érdekében.

![Azure Stack tárterület kiterjesztése](./media/azure-stack-network-howto-extend-datacenter/image1.png)

A diagramon láthatja, hogy a Azure Stack rendszerű virtuális gép több hálózati adapterrel lett telepítve. Mind a redundancia, hanem a tárolási ajánlott eljárás is fontos, hogy több útvonal legyen a cél és a cél között. Ahol a dolgok összetettebbek lesznek, a Azure Stackban lévő virtuális gépek nyilvános és magánhálózati IP-címekkel is rendelkeznek, ugyanúgy, mint az Azure-ban. Ha a külső tárterület szükséges a virtuális gép eléréséhez, akkor csak a nyilvános IP-címen keresztül végezhető el, mivel a magánhálózati IP-címek elsődlegesen a virtuális hálózatok és az alhálózatokon belüli Azure Stack rendszerekben vannak használatban. A külső tároló nem tud kommunikálni a virtuális gép magánhálózati IP-címével, kivéve, ha a helyek közötti VPN-kapcsolaton halad át, hogy magához a vNet kapcsolódjon. Ebben a példában a nyilvános IP-címen keresztül folytatott kommunikációra koncentrálunk. Az egyik dolog, ami a grafikában a nyilvános IP-címmel kapcsolatos, az, hogy két különböző nyilvános IP-címkészlet alhálózata van. Alapértelmezés szerint a Azure Stack csak egy készletet igényel a nyilvános IP-címekhez, de megfontolandó, hogy a redundáns útválasztáshoz egy másodpercet adjon hozzá. Egy adott készletből azonban nem lehet IP-címet kijelölni, így előfordulhat, hogy az azonos készletből származó virtuális gépeket több virtuális hálózati kártyán is megtalálhatja.

Ebben a példában feltételezhető, hogy a szegélyek és a külső tároló közötti útválasztás gondoskodik a hálózatról, a forgalom pedig megfelelően áthaladhat a hálózaton. Ebben a példában nem számít, hogy a gerinc 1 GbE, 10 GbE, 25 GbE vagy még gyorsabb, azonban érdemes figyelembe vennie az integráció megtervezését, hogy a külső tárolóhoz hozzáférő alkalmazások teljesítménybeli igényeit is kezelni lehessen.

## <a name="connect-to-a-windows-server-file-server-storage"></a>Kapcsolódás Windows Server fájlkiszolgáló-tárolóhoz

Ebben az esetben telepítsen és konfiguráljon egy Windows Server 2019 rendszerű virtuális gépet Azure Stack, és készítse elő egy külső fájlkiszolgálón való csatlakozásra, amely a Windows Server 2019-et is futtathatja. Ha szükséges, állítsa be a legfontosabb funkciókat, például a többcsatornás SMB-t, hogy optimalizálja a teljesítményt és a kapcsolatot a virtuális gép és a külső tároló között.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>A Windows Server 2019 virtuális gép üzembe helyezése Azure Stack

1.  A **Azure stack felügyeleti portálról**, feltéve, hogy a rendszer megfelelően van regisztrálva, és csatlakoztatva van a piactérhez, válassza a **piactér-kezelés** lehetőséget, majd feltételezve, hogy még nem rendelkezik Windows Server 2019-lemezképpel, válassza **a Adja hozzá az Azure-ból** , majd keressen rá a **windows Server 2019**-re, és adja hozzá a **Windows Server 2019 Datacenter** -rendszerképet.

    ![](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    Egy Windows Server 2019-rendszerkép letöltése hosszabb időt is igénybe vehet.

2.  Ha Windows Server 2019 rendszerképpel rendelkezik a Azure Stack-környezetben, jelentkezzen be a Azure Stack felhasználói portálra * *.

3.  Miután bejelentkezett a Azure Stack felhasználói portálra, ellenőrizze, hogy van-e [előfizetése az ajánlathoz](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-1908), amely lehetővé teszi a IaaS-erőforrások (számítási, tárolási és hálózati) kiépítését.

4.  Miután elérhetővé vált egy előfizetést, a Azure Stack felhasználói portál **irányítópultján** kattintson az **erőforrás létrehozása**elemre, válassza a **számítás** lehetőséget, majd válassza a **Windows Server 2019 Datacenter Gallery elemet**.

5.  Az **alapvető beállítások** panelen:

    a.  **Név**: VM001

    b.  **Felhasználónév**: localadmin

    c.  **Jelszó és jelszó** **megerősítése**: az ön által választott jelszó \<

    d.  **Előfizetés**: az Ön által választott előfizetés \<számítási/tárolási/hálózati erőforrásokkal >

    e. **Erőforráscsoport**: új létrehozása: storagetesting

    f.  Ezután kattintson **az OK gombra** .

6.  A **méret kiválasztása** panelen válasszon ki egy **Standard_F8s_v2**.

7.  A **Beállítások** panelen válassza ki a **virtuális hálózatot** , és a **virtuális hálózat létrehozása** panelen állítsa be a **10.10.10.0/23** értékre, és frissítse az alhálózati címtartományt a **10.10.10.0/24** értékre, majd kattintson **az OK gombra.** .

8.  Válassza ki a **nyilvános IP-címet**, és a **nyilvános IP-cím létrehozása** panelen válassza a **statikus**lehetőséget.

9.  Válassza a **nyilvános bejövő portok kiválasztása**lehetőséget, majd az **RDP (3389)** elemet.

10. Hagyja meg a többi alapértelmezett értéket, majd kattintson **az OK gombra**.
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. Olvassa el az összegzést, várja meg az érvényesítést, majd kattintson az **OK** gombra az üzembe helyezés megkezdéséhez. A központi telepítés körülbelül 10 percen belül elvégezhető.

12. Miután az üzembe helyezés befejeződött, az **erőforrás** területen válassza ki a virtuális gép nevét, **VM001** az *Áttekintés* panelre léphet.
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. A DNS-név területen válassza a **Konfigurálás** lehetőséget, és adja meg a DNS-név címkéjét * * vm001, majd válassza a **Mentés**lehetőséget, majd válassza a **vm001** lehetőséget a navigációs panelen az *Áttekintés* panelre való visszatéréshez.

14. Az Áttekintés panel jobb oldalán válassza a **storagetesting-vnet/default** lehetőséget a virtuális hálózat/alhálózat szövege alatt.

15. Az storagetesting-vnet **panelen válassza az alhálózatok** , **majd az alhálózat lehetőséget**, majd az új alhálózat hozzáadása panelen adja meg a következő adatokat: 

    a.  **Név**: subnet2 alhálózattal

    b.  **Címtartomány (CIDR-blokk)** : 10.10.11.0/24

    c. **Hálózati biztonsági csoport**: nincs

    d.  **Útválasztási táblázat**: nincs

    e. Válassza **az OK gombot**:

16. A mentés után válassza a **VM001** lehetőséget a navigációs panelen az Áttekintés panelre való visszatéréshez.

17. Válassza a **hálózatkezelés**lehetőséget.

18. Válassza a **hálózati adapter csatolása** , majd a **hálózati adapter létrehozása**lehetőséget.

19. A **hálózati adapter létrehozása** panelen:

    a.  **Név**: vm001nic2

    b.  **Alhálózat**: gondoskodjon arról, hogy az alhálózat 10.10.11.0/24 legyen

    c. **Hálózati biztonsági csoport**: VM001-NSG

    d.  **Erőforráscsoport**: storagetesting

20. A sikeres létrehozás után válassza a **VM001** lehetőséget a navigációs menüben, majd válassza a **Leállítás** lehetőséget a virtuális gép leállításához.

21. Ha a virtuális gép leáll (fel van foglalva), válassza a **hálózatkezelés**lehetőséget, válassza a **hálózati adapter csatolása** lehetőséget, majd válassza a **Vm001nic2**lehetőséget, majd kattintson **az OK gombra**. Néhány perc múlva további hálózati adaptereket fog hozzáadni a virtuális géphez.

22. A **hálózatkezelés** panelen válassza a **vm001nic2** lapot, majd válassza a **hálózati adapter: vm001nic2**lehetőséget.

23. A vm001nic felületén válassza az **IP-konfigurációk**lehetőséget, majd a panel közepén válassza a **ipconfig1**lehetőséget.

24. A ipconfig1-beállítások panelen válassza a nyilvános IP-cím **engedélyezése** lehetőséget, és válassza a **szükséges beállítások konfigurálása**, **új létrehozása** lehetőséget, és adja meg a vm001nic2pip nevet, válassza a **statikus** lehetőséget, majd kattintson **az OK** , majd a **Mentés**gombra.

25. A sikeres mentés után térjen vissza a VM001 áttekintés panelre, és válassza az **Indítás** lehetőséget a konfigurált Windows Server 2019 virtuális gép elindításához.

### <a name="configure-the-windows-server-2019-file-server-storage"></a>A Windows Server 2019 fájlkiszolgáló tárolójának konfigurálása

Ebben az első példában egy olyan konfigurációt kell érvényesíteni, amelyben a Windows Server 2019 fájlkiszolgáló a Hyper-V-n futó virtuális gép. A virtuális gép nyolc virtuális processzorral, egyetlen VHDX-fájllal és a legfontosabb két virtuális hálózati adapterrel lesz konfigurálva. Ideális esetben ezeknek a hálózati adaptereknek különböző irányítható alhálózatai lesznek, de ebben a tesztben a hálózati adapterek ugyanazon az alhálózaton lesznek.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

A fájlkiszolgálón a Windows Server 2016-es vagy 2019-es, fizikai vagy virtuális, Hyper-V-, VMware-vagy más platformon futó kiszolgáló lehet. A legfontosabb szempont, hogy a Azure Stack rendszerből való kapcsolat be-és kikerül, azonban a forrás és a cél között több elérési út is rendelkezésre áll, mivel ez további redundanciát biztosít, és lehetővé teszi a fejlettebb képességek növelését teljesítmény, például többcsatornás SMB.

Frissítse a fájlkiszolgáló legújabb összesítő frissítéseit és javításait, majd a fájlmegosztás konfigurációjának folytatása előtt indítsa újra a rendszert.

Miután frissítette és újraindította a kiszolgálót, most már megadhatja azt fájlkiszolgálóként.

1) A fájlkiszolgáló számítógépén futtassa az **ipconfig/all** parancsot a **cmd** -ből, és jegyezze fel a fájlkiszolgáló által használt **DNS-kiszolgálót** .

2)  Nyissa meg a **Kiszolgálókezelő alkalmazást** , és válassza a **kezelés**, majd a **szerepkörök és szolgáltatások hozzáadása**lehetőséget.

3)  Válassza a **tovább**lehetőséget, válassza a **szerepköralapú vagy a szolgáltatáson alapuló telepítés**lehetőséget, és folytassa a beállításokat, amíg el nem éri a **kiszolgálói szerepkörök kiválasztása** lapot.

4)  Bontsa ki a **fájl-és tárolási szolgáltatások**, majd a fájl **& az iSCSI-szolgáltatások** elemet, és válassza a **fájlkiszolgáló**lehetőséget. Ha elkészült, zárjuk be a **Kiszolgálókezelő eszközt**.

5)  Nyissa meg újra a **Kiszolgálókezelő eszközt** , és válassza a **fájl-és tárolási szolgáltatások**lehetőséget.

6)  Válassza a **megosztások**lehetőséget.

7)  A **megosztások mezőben**jelölje ki a **fájlmegosztás létrehozását, majd indítsa el az új megosztás varázsló** hivatkozását.

8)  Az **új megosztás varázslóban** válassza az **SMB-megosztás – gyors**lehetőséget, majd a **tovább**gombot, és folytassa a varázslót, és válassza a **C:\\\\ kötet**lehetőséget.

9)  Adja meg a megosztás nevét, **TestStorage** majd válassza a **tovább**lehetőséget.

10) Vissza az **új megosztás varázslóban**válassza a **tovább** , majd a **Létrehozás**, majd a **Bezárás**lehetőséget.

Ezzel létrehozta a fájlmegosztást a fájlkiszolgálón.

### <a name="testing-file-storage-performance-and-connectivity"></a>A fájlok tárolási teljesítményének és kapcsolatának tesztelése

A kommunikáció ellenőrzéséhez és néhány kezdetleges teszt futtatásához jelentkezzen be a Azure Stack felhasználói portálra a **Azure stack** rendszeren, és keresse meg a **VM001** **Áttekintés** paneljét.

1)  Válassza a **Kapcsolódás** lehetőséget, hogy RDP-kapcsolatot hozzon létre a VM001-ben.

2)  A gazdagépen keresztüli kommunikációhoz szerkeszteni fogja a **hosts** fájlt; éles környezetben azonban a DNS optimálisan konfigurálható úgy, hogy a nevek automatikusan fel legyenek oldva. Használhat IP-címeket is. A példánkban azonban a **hosts** fájlt fogja szerkeszteni.

3)  Nyissa meg a **jegyzettömböt**, és győződjön meg arról, hogy **rendszergazdaként fut**.

4)  Miután megnyitotta a Jegyzettömb alkalmazást, válassza a **fájl**lehetőséget, **nyissa meg** a c:\\WINDOWSsystem32driversetc\\\, és válassza a Megnyitás párbeszédpanel jobb alsó sarkában található **összes fájl** elemet. Válassza ki a **hosts** fájlt, és válassza a **Megnyitás**lehetőséget.

5)  Szerkessze a Hosts fájlt a fájlkiszolgáló IP-címének és DNS-nevének hozzáadásával.

    ![](./media/azure-stack-network-howto-extend-datacenter/image6.png)

6)  Mentse a fájlt, és a Jegyzettömb bezárásával.

7)  Nyissa meg a **cmd parancsot** , és Pingelje a gazdagépek fájlban megadott fájlkiszolgáló nevét. Ennek a kiszolgálónak egy hálózati adapterének IP-címét kell visszaadnia, ezért az IP-cím pingelésével tesztelje manuálisan a kommunikációt a másik adapterrel.

## <a name="connect-to-a-windows-server-iscsi-target-server"></a>Kapcsolódás Windows Server iSCSI-célkiszolgálón

Ebben a forgatókönyvben egy Windows Server 2019 rendszerű virtuális gépet telepít és konfigurál Azure Stack, és előkészíti egy külső iSCSI-célkiszolgáló csatlakozására, amely a Windows Server 2019-et is futtatja.

> [!Note]  
> Ha már elvégezte az első forgatókönyvet, ugorjon előre a gépek testreszabásához.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>A Windows Server 2019 virtuális gép üzembe helyezése Azure Stack

Ha még nem telepítette a Windows Server 2019 rendszerű virtuális gépet Azure Stackon, kövesse a [Windows server 2019 virtuális gép Azure stack-on való üzembe helyezésének](#deploy-the-windows-server-2019-vm-on-azure-stack)lépéseit. Ezután beállíthatja a Windows Server 2019 iSCSI-tárolót.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>A Windows Server 2019 iSCSI-tároló konfigurálása

A második forgatókönyv esetében ellenőrizze, hogy van-e olyan konfiguráció, amelyben a Windows Server 2019 iSCSI-tároló a Hyper-V-n futó virtuális gép. A virtuális gép nyolc virtuális processzorral, egyetlen VHDX-fájllal és a legfontosabb két virtuális hálózati adapterrel lesz konfigurálva. Ideális esetben ezek a hálózati adapterek eltérő irányítható alhálózatokkal rendelkeznek, de ebben a tesztben a hálózati adapterek ugyanazon az alhálózaton lesznek.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

Az iSCSI-tárolóhoz a Windows Server 2016-es vagy 2019-es, fizikai vagy virtuális, Hyper-V-, VMware-vagy más platformon futó operációs rendszer lehet. A legfontosabb szempont, hogy a Azure Stack rendszerhez való kapcsolódási és kimenő kapcsolattal rendelkezik, azonban a forrás és a cél között több útvonal is van, mivel ez további redundanciát és átviteli sebességet biztosít.

Frissítse a fájlkiszolgáló legújabb összesítő frissítéseit és javításait, majd indítsa újra a rendszert az iSCSI-célkiszolgáló konfigurációjának folytatása előtt.

A frissítést és az újraindítást követően ezt a kiszolgálót már iSCSI-célkiszolgálóként is konfigurálhatja.

1. Nyissa meg a **kiszolgálókezelő** > **felügyelet** > **szerepkörök és szolgáltatások hozzáadása lehetőséget**.

2. A megnyitást követően válassza a **tovább**lehetőséget, válassza a **szerepköralapú vagy a szolgáltatáson alapuló telepítés**lehetőséget, és folytassa a beállításokat, amíg el nem éri a **kiszolgálói szerepkörök kiválasztása** lapot.

3. Bontsa ki a **fájl-és tárolási szolgáltatások**, majd a **Fájl & az iSCSI-szolgáltatások** elemet, és válassza ki az **iSCSI-célkiszolgáló**beállítást, fogadja el az új szolgáltatások hozzáadására vonatkozó utasításokat, majd folytassa a befejezést

    ![](./media/azure-stack-network-howto-extend-datacenter/image8.png)
    
    Ha elkészült, a Kiszolgálókezelő bezárásával **.**

4. Nyissa meg a **fájlkezelőt,** navigáljon a `C:\\`ra, és **hozzon létre egy új mappát**, amelyet `iSCSI`.

5. Nyissa meg újra a **kiszolgálókezelő** > **fájl-és tárolási szolgáltatásokat** a bal oldali menüből.

6. Válassza az **iSCSI** lehetőséget, majd válassza az "**iSCSI virtuális lemez létrehozása" lehetőséget, és indítsa el az új virtuális iSCSI-lemez varázslót**.

7. Az **iSCSI virtuális lemez helyének kiválasztása** lapon válassza az **Egyéni elérési út beírása** lehetőséget, és keresse meg a `C:\\iSCSI`. Kattintson a **Tovább** gombra.

8. Adja meg az iSCSI virtuális lemez nevét `iSCSIdisk1` és opcionálisan egy leírást, majd válassza a **tovább**lehetőséget.

9. Állítsa be a virtuális lemez méretét `10GB`re, és válassza a **rögzített méret** lehetőséget, majd kattintson a **tovább**gombra.

    ![](./media/azure-stack-network-howto-extend-datacenter/image9.png)

10. Mivel ez egy új cél, válassza az **új iSCSI-tároló** elemet, és kattintson a **Tovább gombra**.

11. A **cél nevének megadása** lapon adja meg a **TARGET1** nevet, és kattintson a **Tovább gombra**.

12. A **hozzáférési kiszolgálók megadása** lapon válassza a **Hozzáadás**lehetőséget. Ekkor megnyílik egy párbeszédpanel, amely megadja az iSCSI-tárolóhoz való kapcsolódásra engedélyt igénylő **kezdeményezőket** .

13. A legördülő menüben válassza **az érték megadása a kiválasztott típushoz** lehetőséget a **kezdeményező azonosítójának hozzáadása ablakban** , majd a **típus** biztosítja a IQN lehetőséget. Adja meg `iqn.1991-05.com.microsoft:<computername>`, hogy `<computername>` a **VM001** **számítógép neve** . Kattintson a **Tovább** gombra.

    ![](./media/azure-stack-network-howto-extend-datacenter/image10.png)

14. A **hitelesítés engedélyezése** lapon hagyja üresen a mezőket, majd kattintson a **tovább**gombra.

15. Erősítse meg a beállításokat, majd kattintson a **Létrehozás**, majd a bezárás elemre.

    ![](./media/azure-stack-network-howto-extend-datacenter/image11.png)

Meg kell jelennie a Kiszolgálókezelő alkalmazásban létrehozott iSCSI virtuális lemeznek.

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>A Windows Server 2019 iSCSI-kezdeményező és az MPIO konfigurálása

Az iSCSI-kezdeményező beállításához jelentkezzen be a **felhasználói portálra** a **Azure stack** rendszeren, és navigáljon a **VM001** **Áttekintés** paneljére.

1.  Hozzon létre egy RDP-kapcsolatot a VM001. Csatlakozás után nyissa meg a **Kiszolgálókezelő alkalmazást**.

2.  Válassza a **szerepkörök és szolgáltatások hozzáadása**lehetőséget, és fogadja el az alapértelmezett értékeket, amíg el nem éri a **szolgáltatások** lapot.

3.  A **szolgáltatások** lapon adja hozzá a **többutas I/O** elemet, majd kattintson a **tovább**gombra.

    ![](./media/azure-stack-network-howto-extend-datacenter/image12.png)

4.  Jelölje be a **célkiszolgáló automatikus újraindítása, ha szükséges** jelölőnégyzetet, majd válassza a **telepítés**, majd a **Bezárás lehetőséget.** Valószínűleg újraindításra lesz szükség, így ha elkészült, újra csatlakozhat a VM001.

5.  Vissza a **Kiszolgálókezelőben**, várja meg, amíg az **MPIO telepítése befejeződik**, válassza a **Bezárás**, majd az **eszközök** lehetőséget, és válassza az **MPIO**lehetőséget.

6.  Válassza a **több útvonal felderítése** fület, majd jelölje be a jelölőnégyzetet az **iSCSI-eszközök támogatásának hozzáadásához** > **hozzáadásához**, majd válassza az **Igen** lehetőséget a VM001 **újraindításához** . Válassza **az OK, majd a** manuális újraindítás lehetőséget.

    ![](./media/azure-stack-network-howto-extend-datacenter/image13.png)

1.  Az újraindítás után hozzon létre egy **új RDP-kapcsolatot a VM001**.

2.  Csatlakozás után nyissa meg a **Kiszolgálókezelő**eszközt, és válassza az **eszközök** > **iSCSI-kezdeményező**elemet.

3.  Válassza az **Igen** lehetőséget, amikor a Microsoft iSCSI megnyitja, hogy az iSCSI szolgáltatás alapértelmezés szerint fusson.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

4.  Válassza a **felderítés** lapot.

5.  Kattintson a **portál felderítése** gombra. Ekkor két célt fog hozzáadni.

6.  Adja meg az iSCSI-célkiszolgáló első IP-címét, és válassza a **speciális**lehetőséget.

    ![](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7. A **speciális beállításokhoz**válassza a következőt:

    - Helyi adapter: Microsoft iSCSI-kezdeményező

    - Kezdeményező IP-címe: 10.10.10.4

    - Ha be van állítva, kattintson **az OK gombra**.

8.  Kattintson **az OK gombra** a **felderítési portálon**.

9.  Ismételje meg a folyamatot a következővel:

    - IP-cím: a második iSCSI cél IP-címe

    - Helyi adapter: Microsoft iSCSI-kezdeményező

    - Kezdeményező IP-címe: 10.10.11.4

10. A cél-portáloknak így kell kinézniük, a saját iSCSI cél IP- **címeivel a cím** oszlopban.

    ![](./media/azure-stack-network-howto-extend-datacenter/image16.png)

11. Válassza a **célok** fület, majd válassza ki az iSCSI-tárolót. Kattintson a **Csatlakozás** gombra.

12. Válassza a **Multi-Path engedélyezése** a **Kapcsolódás cél számára**lehetőséget, majd válassza a **speciális**lehetőséget.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. A **Kapcsolódás céljához**adja meg a következő adatokat:

    - Helyi adapter: Microsoft iSCSI-kezdeményező

    - Kezdeményező IP-címe: 10.10.10.4

    - Cél-portál IP-címe: \<az első iSCSI cél IP-címét/3260 >

    - Kattintson az **OK** gombra.

    ![](./media/azure-stack-network-howto-extend-datacenter/image18.png)

1.  Ismételje meg a folyamatot a második kezdeményező/cél kombinációnál.

    - Helyi adapter: Microsoft iSCSI-kezdeményező

    - Kezdeményező IP-címe: 10.10.11.4

    - Cél-portál IP-címe: \<a második iSCSI cél IP-címét/3260 >

    ![](./media/azure-stack-network-howto-extend-datacenter/image19.png)

1.  Válassza a **kötetek és eszközök** fület, majd kattintson az **automatikus konfigurálás** lehetőségre – a rendszer az MPIO-kötetet mutatja be:

    ![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

2.  A **célok** lapon válassza az **eszközök** lehetőséget, és két kapcsolatot kell látnia a korábban létrehozott iSCSI VHD-vel.

    ![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

3.  Kattintson a **többutas i/o (MPIO) gombra** a terheléselosztási szabályzattal és elérési utakkal kapcsolatos további információk megjelenítéséhez.

    ![](./media/azure-stack-network-howto-extend-datacenter/image21.png)

4.  Kattintson háromszor az **OK gombra** a Windows és az iSCSI-kezdeményező kilépéséhez.

5.  Nyissa meg a Lemezkezelés (diskmgmt. msc) fájlt, és a rendszer egy **inicializálási lemez** előugró ablakát kéri.

    ![](./media/azure-stack-network-howto-extend-datacenter/image22.png)

6.  Az alapértelmezett beállítások elfogadásához kattintson az **OK** gombra, majd görgessen le az új lemezre, kattintson a jobb gombbal, majd válassza az **új egyszerű kötet**lehetőséget.

7.  A varázsló végigvezeti az alapértelmezett beállítások elfogadásával. Módosítsa a kötet címkéjét a **iSCSIdisk1** értékre, majd válassza a **Befejezés**lehetőséget.

    ![](./media/azure-stack-network-howto-extend-datacenter/image23.png)

8.  A meghajtót ezután formáznia kell, és meg kell adni egy meghajtóbetűjelet.

9.  Nyissa meg a **fájlkezelő** > **ezt a számítógépet** , és tekintse meg a VM001-hez csatolt új meghajtót.

### <a name="test-external-storage-connectivity"></a>Külső tárterület kapcsolatának tesztelése

A kommunikáció ellenőrzéséhez és a fájl-másolási teszt futtatásához jelentkezzen be a **felhasználói portálra** a **Azure stack** rendszeren, és navigáljon a **VM001** **Áttekintés** paneljére.

1. Válassza a **Kapcsolódás** lehetőséget, hogy RDP-kapcsolatot hozzon létre a **VM001**-ben.

2. Nyissa meg a **Feladatkezelő eszközt** , majd válassza a **teljesítmény** fület. az ablakot az RDP-munkamenet jobb oldalára kell igazítani.

3. Nyissa meg a **Windows PowerShell integrált parancsprogram-kezelési környezet** rendszergazdaként, és pattintsa az RDP-munkamenet bal oldali oldalára. Az ISE jobb oldalán kattintson a **parancsok** ablaktábla bezárásához, majd a **parancsfájl** gombra, és bontsa ki a fehér parancsfájl panelt az ISE ablak tetején.

4. Ebben a virtuális gépen nincsenek natív PowerShell-modulok egy virtuális merevlemez létrehozásához, amelyet nagy fájlként fog használni az iSCSI-tárolóba való fájlátvitel teszteléséhez. Ebben az esetben a DiskPart programot fogja futtatni egy VHD-fájl létrehozásához. Az ISE-ben futtassa a következőt:

    1. `Start-Process Diskpart`

    2. Megnyílik egy új CMD-parancssor. Adja meg a következő cmd-t:<br>`Create vdisk file="c:\\test.vhd" type=fixed maximum=5120`

    ![](./media/azure-stack-network-howto-extend-datacenter/image24.png)

    A létrehozás eltarthat néhány másodpercig. A létrehozást követően a fájl megnyitásához nyissa meg a **fájlkezelőt** , és keresse meg a C:\\-t, majd az új test. VHD és 5 GB méretűnek kell lennie.

    ![](./media/azure-stack-network-howto-extend-datacenter/image25.png)

    A CMD-parancssor bezárásához. Térjen vissza az ISE-be, adja meg a következő parancsot az ISE-ben. Cserélje le az F:\\ a korábban alkalmazott iSCSI-tároló betűjelére.

    1. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    2. Válassza ki a sort az ISE-ben. Nyomja le az **F8** billentyűt a billentyűzeten.

    3. A parancs futtatása közben nézze meg a két hálózati adaptert, és tekintse át az adatátvitelt a VM001 mindkét hálózati adapterén. Azt is figyelje meg, hogy minden hálózati adapternek egyenletesen kell megosztania a terhelést.

        ![](./media/azure-stack-network-howto-extend-datacenter/image26.png)

Ennek a forgatókönyvnek a célja, hogy kiemelje a Azure Stackon futó munkaterhelés és egy külső tároló-tömb, ebben az esetben egy Windows Server-alapú iSCSI-tároló közötti kapcsolatot. Ez nem teljesítménytesztnek lett kialakítva, és nem tükrözheti azokat a lépéseket, amelyeket végre kell hajtania, ha alternatív iSCSI-alapú készüléket használ, azonban a számítási feladatok üzembe helyezéséhez szükséges alapvető szempontokat is kiemeli a Azure Stack, és csatlakoztassa őket a Azure Stack-környezeten kívüli tárolási rendszerekhez.

## <a name="next-steps"></a>Következő lépések

[Különbségek és szempontok Azure Stack hálózatkezeléshez](azure-stack-network-differences.md)  