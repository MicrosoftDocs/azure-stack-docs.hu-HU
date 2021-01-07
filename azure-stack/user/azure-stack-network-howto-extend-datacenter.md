---
title: Az adatközpont kiterjesztése Azure Stack hub-on
description: Ismerje meg, hogyan bővítheti az adatközpontot Azure Stack hub-on.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 02a3fdb862f3a4c04a63d5e5694487b62435ee16
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974098"
---
# <a name="extending-storage-to-azure-stack-hub"></a>Tároló kiterjesztése Azure Stack hubhoz

Ez a cikk Azure Stack hub tárolási infrastruktúrájának adatait ismerteti, amelyek segítségével eldöntheti, hogyan integrálhatja Azure Stack hub-t a meglévő hálózati környezetbe. Az adatközpont kibővítésével kapcsolatos általános vitát követően a cikk két különböző forgatókönyvet mutat be. Csatlakozhat egy Windows file Storage-kiszolgálóhoz. Egy Windows iSCSI-kiszolgálóhoz is csatlakozhat.

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>A Storage Azure Stack hub-ra való kiterjesztésének áttekintése

Vannak olyan helyzetek, amikor a nyilvános felhőben található adatok nem elegendőek. Lehet, hogy van egy nagy számítási igényű virtualizált adatbázis-munkaterhelés, a késések és a nyilvános felhőbe való oda-és visszautazási idő befolyásolhatja az adatbázis számítási feladatának teljesítményét. Előfordulhat, hogy a helyszíni adatok egy fájlkiszolgálón, NAS-on vagy iSCSI Storage-tömbben vannak tárolva, amelyet a helyszíni munkaterhelések számára kell elérni, és a helyszíni rendszernek kell megfelelnie a szabályozási vagy megfelelőségi célok teljesítéséhez. Ez csupán két olyan forgatókönyv, amelyben az adatok a helyszínen találhatók, számos szervezet számára fontosak maradnak.

Tehát miért nem csak a Storage-fiókokban lévő, Azure Stack hub-beli vagy virtualizált fájlkiszolgálókba tartozó, Azure Stack hub rendszeren futó adattárakat tárolja? Az Azure-ban eltérően Azure Stack hub-tároló véges. A használathoz rendelkezésre álló kapacitás a megvásárolt csomópontok számától függ, és az Ön által használt csomópontok száma mellett teljes mértékben kihasználható. Mivel Azure Stack hub egy Hyper-Converged megoldás, ha a tárolási kapacitást szeretné növelni a használati igények kielégítése érdekében, a csomópontok hozzáadásával is növelnie kell a számítási lábnyomot. Ez potenciálisan költséges lehet, különösen abban az esetben, ha a további kapacitásra van szükség, amely a Azure Stack hub rendszeren kívüli alacsony költségeket eredményezheti.

Ez az alábbi forgatókönyvhöz nyújt segítséget. Hogyan csatlakoztatható Azure Stack hub-rendszerek, az Azure Stack hub-on futó virtualizált munkaterhelések egyszerűen és hatékonyan, az Azure Stack hub-on kívüli, hálózaton keresztül elérhető tárolási rendszerekhez.

### <a name="design-for-extending-storage"></a>A tárterület bővítésének tervezése

Az ábrán egy olyan forgatókönyv látható, amelyben egyetlen virtuális gép fut, amely a munkaterhelést, a csatlakozást és a külső (a virtuális gép és a Azure Stack hub) tárolót használja, és az adatok olvasását, írását stb. Ebben a cikkben a fájlok egyszerű lekérésére koncentrálhat, de ezt a példát kiterjesztheti összetettebb forgatókönyvek, például az adatbázisfájlok távoli tárolása érdekében.

![Az Azure Stack hub rendszerbeli számítási feladatok virtuális gépe hozzáfér a külső tárolóhoz. A virtuális gép két hálózati adapterrel rendelkezik, amelyek mindegyike nyilvános és magánhálózati IP-címmel is rendelkezik.](./media/azure-stack-network-howto-extend-datacenter/azure-stack-network-howto-extend-datacenter-image1.svg)

A diagramon láthatja, hogy a Azure Stack hub rendszerű virtuális gép több hálózati adapterrel lett telepítve. Mind a redundancia, hanem a tárolási ajánlott eljárás is fontos, hogy a cél és a cél között több útvonal is legyen. Ahol a dolgok összetettebbek lesznek, az Azure Stack hub-ban lévő virtuális gépek nyilvános és magánhálózati IP-címekkel is rendelkeznek, akárcsak az Azure-ban. Ha a külső tárterület szükséges a virtuális gép eléréséhez, csak a nyilvános IP-címen keresztül teheti meg, mivel a magánhálózati IP-címek elsődlegesen a virtuális hálózatok és az alhálózatokon belüli Azure Stack hub rendszerekben használatosak. A külső tároló nem tud kommunikálni a virtuális gép magánhálózati IP-címével, kivéve, ha a helyről a VPN-re halad át, hogy a vNet magát. Ebben a példában a nyilvános IP-címen keresztül folytatjuk a kommunikációt. A diagramon a nyilvános IP-címmel kapcsolatban az egyik dolog, hogy két különböző nyilvános IP-címkészlet alhálózata van. Alapértelmezés szerint az Azure Stack hub csak egy készletet igényel a nyilvános IP-címekhez, de megfontolandó, hogy a redundáns útválasztáshoz egy másodpercet kell hozzáadnia. Jelenleg azonban nem lehet kijelölni egy adott készletből származó IP-címet, ezért előfordulhat, hogy az azonos készletben lévő virtuális gépeket a több virtuális hálózati kártyán is megtalálhatja.

Ebben a vitában feltételezzük, hogy a Border Devices és a külső tároló közötti útválasztás gondoskodik a hálózatról, és a forgalom megfelelő módon képes bejárni a hálózaton. Ebben a példában nem számít, hogy a gerinc 1 GbE, 10 GbE, 25 GbE vagy még gyorsabb, azonban érdemes figyelembe vennie az integráció megtervezését, hogy a külső tárolóhoz hozzáférő alkalmazások teljesítménybeli igényeit is kezelni lehessen.

## <a name="connect-to-a-windows-server-iscsi-target"></a>Kapcsolódás Windows Server iSCSI-tárolóhoz

Ebben a forgatókönyvben egy Windows Server 2019 rendszerű virtuális gépet helyezünk üzembe és konfigurálunk Azure Stack hub-on, és előkészítjük azt egy külső iSCSI-tárolóhoz való csatlakozásra, amely a Windows Server 2019-et is futtatja. Adott esetben a virtuális gép és a külső tároló közötti teljesítmény és kapcsolat optimalizálása érdekében a legfontosabb funkciókat, például az MPIO-t is engedélyezzük.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack-hub"></a>A Windows Server 2019 virtuális gép üzembe helyezése Azure Stack hub-on

1.  A **Azure stack hub felügyeleti portálján**, feltéve, hogy ez a rendszer megfelelően regisztrálva van, és csatlakoztatva van a piactérhez, válassza a **piactér-kezelés** lehetőséget, majd feltételezve, hogy még nem rendelkezik Windows Server 2019 lemezképpel, válassza a Hozzáadás az Azure- **ból** lehetőséget, majd keressen rá a **Windows Server 2019** lehetőségre, és adja hozzá a **Windows Server 2019 Datacenter** rendszerképet.

    ![Az "irányítópult > piactér-kezelő – piactér elemek > Hozzáadás az Azure-ból" párbeszédpanel a "Windows Server 2019" kifejezést jeleníti meg a keresőmezőbe, valamint azoknak az elemeknek a listáját, amelyeknek a neve tartalmazza ezt a karakterláncot.](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    Egy Windows Server 2019-rendszerkép letöltése hosszabb időt is igénybe vehet.

2.  Ha már rendelkezik Windows Server 2019 rendszerképpel a Azure Stack hub-környezetben, **Jelentkezzen be a Azure stack hub felhasználói portálra**.

3.  Miután bejelentkezett az Azure Stack hub felhasználói portálra, ellenőrizze, hogy van-e [előfizetése az ajánlathoz](../operator/azure-stack-subscribe-plan-provision-vm.md), amely lehetővé teszi a IaaS-erőforrások (számítási, tárolási és hálózati) kiépítését.

4.  Miután elérhetővé vált egy előfizetést, az Azure Stack hub felhasználói portál **irányítópultján** kattintson az **erőforrás létrehozása** elemre, válassza a **számítás** lehetőséget, majd válassza a **Windows Server 2019 Datacenter Gallery elemet**.

5.  Az **alapvető beállítások** panelen végezze el az alábbi információkat:

    a.  **Név**: VM001

    b.  **Felhasználónév**: localadmin

    c.  **Jelszó** és jelszó **megerősítése**: \<password of your choice>

    d.  **Előfizetés**: \<subscription of your choice, with compute/storage/network resources> .

    e.  **Erőforráscsoport**: storagetesting (új létrehozása)

    f.  Válassza az **OK** gombot.

6.  A **méret kiválasztása** panelen válasszon ki egy **Standard_F8s_v2** , majd válassza a **kiválasztás** lehetőséget.

7.  A **Beállítások** panelen válassza ki a **virtuális hálózatot** , és a **virtuális hálózat létrehozása** panelen állítsa be a **10.10.10.0/23** értékre, és frissítse az alhálózat címtartományt **10.10.10.0/24** értékre, majd kattintson **az OK gombra**.

8.  Válassza ki a **nyilvános IP-címet**, és a **nyilvános IP-cím létrehozása** panelen válassza a **statikus** választógombot.

9.  A **nyilvános bejövő portok kiválasztása** legördülő menüben válassza az **RDP (3389)** lehetőséget.

10. Hagyja meg a többi alapértelmezett értéket, majd kattintson **az OK gombra**.

    ![Az "irányítópult > új > virtuális gép létrehozása > összegzése" párbeszédpanel kijelenti, hogy az "érvényesítése sikeres", és megjeleníti a VM001 kapcsolatos információkat.](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. Olvassa el az összegzést, várja meg az érvényesítést, majd kattintson az **OK** gombra az üzembe helyezés megkezdéséhez. A központi telepítés körülbelül 10 percen belül elvégezhető.

12. Miután az üzembe helyezés befejeződött, az **erőforrás** területen válassza ki a virtuális gép nevét, **VM001** az **Áttekintés** megnyitásához.

    ![Az áttekintő képernyő a VM001 kapcsolatos információkat jeleníti meg.](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. A DNS-név területen válassza a **Konfigurálás** lehetőséget, és adja meg a DNS-név címkét, a **Vm001** és a **Mentés** lehetőséget, majd válassza a **vm001** lehetőséget.

14. Az Áttekintés panel jobb oldalán válassza a **storagetesting-vnet/default** lehetőséget a virtuális hálózat/alhálózat szövege alatt.

15. Az storagetesting-vnet panelen válassza az **alhálózatok** , **majd az alhálózat lehetőséget**, majd az új alhálózat hozzáadása panelen adja meg a következő információkat, majd kattintson az **OK gombra**:

    a.  **Név**: subnet2 alhálózattal

    b.  **Címtartomány (CIDR-blokk)**: 10.10.11.0/24

    c.  **Hálózati biztonsági csoport**: nincs

    d.  **Útválasztási táblázat**: nincs

16. A mentés után válassza a **VM001** lehetőséget.

17. Az Áttekintés panel bal oldali részén válassza a **hálózatkezelés** lehetőséget.

18. Válassza a **hálózati adapter csatolása** , majd a **hálózati adapter létrehozása** lehetőséget.

19. A **hálózati adapter létrehozása** panelen adja meg a következő adatokat.

    a.  **Név**: vm001nic2

    b.  **Alhálózat**: gondoskodjon arról, hogy az alhálózat 10.10.11.0/24 legyen

    c.  **Hálózati biztonsági csoport**: VM001-NSG

    d. **Erőforráscsoport**: storagetesting

20. A sikeres csatolást követően válassza a **VM001**  elemet, és válassza a **Leállítás** lehetőséget a virtuális gép leállításához.

21. Ha a virtuális gép le van állítva (fel van foglalva), az Áttekintés panel bal oldalán válassza a **hálózatkezelés** lehetőséget, válassza a **hálózati adapter csatolása** lehetőséget, majd válassza a **Vm001nic2** lehetőséget, majd kattintson **az OK gombra**. A további hálózati adapterek néhány pillanat alatt hozzá lesznek adva a virtuális géphez.

22. Továbbra is a **hálózatkezelés** panelen válassza a **vm001nic2** lapot, majd válassza a **hálózati adapter: vm001nic2** lehetőséget.

23. A vm001nic felületén válassza az **IP-konfigurációk** lehetőséget, majd a panel közepén válassza a **ipconfig1** lehetőséget.

24. A ipconfig1-beállítások panelen válassza a nyilvános IP-cím **engedélyezése** lehetőséget, és válassza a **szükséges beállítások konfigurálása**, **új létrehozása** lehetőséget, és adja meg a vm001nic2pip nevet, válassza a **statikus** lehetőséget, majd kattintson **az OK** , majd a **Mentés** gombra.

25. A sikeres mentés után térjen vissza a VM001 áttekintés panelre, és válassza az **Indítás** lehetőséget a konfigurált Windows Server 2019 virtuális gép elindításához.

26. Az indítás után **hozzon létre egy RDP-munkamenetet** a VM001.

27. A virtuális gépen való csatlakozás után nyissa meg a **cmd** (rendszergazda) lehetőséget, és írja be az **állomásnév** nevet az operációs rendszer számítógépnevét. Meg **kell egyeznie a VM001**. Jegyezze fel később ezt a megjegyzést.

### <a name="configure-second-network-adapter-on-windows-server-2019-vm-on-azure-stack-hub"></a>A második hálózati adapter konfigurálása a Windows Server 2019 virtuális gépen Azure Stack hub-on

Alapértelmezés szerint a Azure Stack hub egy alapértelmezett átjárót rendel hozzá a virtuális géphez csatolt első (elsődleges) hálózati adapterhez. Azure Stack hub nem rendel hozzá alapértelmezett átjárót a virtuális géphez csatlakoztatott további (másodlagos) hálózati adapterekhez. Alapértelmezés szerint ezért nem lehetséges a kommunikáció olyan erőforrásokkal, amelyek a másodlagos hálózati adaptert tartalmazó alhálózaton kívül vannak. A másodlagos hálózati adapterek azonban az alhálózaton kívüli erőforrásokkal is kommunikálhatnak, bár a kommunikáció engedélyezésének lépései eltérőek a különböző operációs rendszereken.

1.  Ha még nem rendelkezik nyitott kapcsolattal, hozzon létre egy RDP-kapcsolatot a **VM001**-ben.

2.  Nyissa meg a **parancssort** rendszergazdaként, és futtassa a **route print** parancsot, amely a virtuális gépen belül a két felületet (Hyper-V hálózati adaptereket) adja vissza.

    ![Az "útvonal nyomtatása" kimenet egy olyan illesztőfelület-lista, amely két Hyper-V hálózati adaptert tartalmaz: az interface 6 a Hyper-V hálózati adapter #2, a 7-es csatoló pedig az adapter #3.](./media/azure-stack-network-howto-extend-datacenter/image5.png)

3.  Most futtassa az **ipconfig parancsot** a másodlagos hálózati adapterhez HOZZÁRENDELT IP-cím megtekintéséhez. Ebben a példában a 10.10.11.4 a 6. felülethez van rendelve. A másodlagos hálózati adapterhez nem érkezik alapértelmezett átjáró-címet.

    ![A részleges ipconfig-lista azt mutatja, hogy az Ethernet-adapter 2-es IP-10.10.11.4 rendelkezik.](./media/azure-stack-network-howto-extend-datacenter/image6.png)

4.  Ha a másodlagos hálózati adapter alhálózatán kívüli címekre irányuló összes forgalmat át szeretné irányítani az alhálózathoz tartozó átjáróra, futtassa a következő parancsot a **cmd:** paranccsal.

    ```CMD  
    route add -p 0.0.0.0 MASK 0.0.0.0 <ipaddress> METRIC 5015 IF <interface>
    ```

    A az `<ipaddress>` aktuális alhálózat 1-címe, és `<interface>` az illesztőfelület száma.

    ![Az útvonal hozzáadása parancs a 10.10.11.1 IP-cím értékkel és a 6. csatolóval van kiállítva.](./media/azure-stack-network-howto-extend-datacenter/image7.png)

5.  Ha ellenőrizni szeretné, hogy a hozzáadott útvonal szerepel-e az útválasztási táblában, adja meg a **route print** parancsot.

    ![A hozzáadott útvonal állandó útvonalként jelenik meg az átjáró 10.10.11.1 és a 5015-es metrikával.](./media/azure-stack-network-howto-extend-datacenter/image8.png)

6.  A kimenő kommunikációt a ping parancs futtatásával is érvényesítheti:  
    `ping 8.8.8.8 -S 10.10.11.4`  
    A `-S` jelző lehetővé teszi, hogy megadja a forrás címét, ebben az esetben a 10.10.11.4 az alapértelmezett átjáróval rendelkező hálózati adapter IP-címe.

7.  A **cmd** bezárásához.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>A Windows Server 2019 iSCSI-tároló konfigurálása

Ebben a forgatókönyvben egy olyan konfigurációt kell érvényesíteni, amelyben a Windows Server 2019 iSCSI-tároló a Hyper-V-n futó virtuális gép a Azure Stack hub-környezeten kívül. A virtuális gép nyolc virtuális processzorral, egyetlen VHDX-fájllal és a legfontosabb két virtuális hálózati adapterrel lesz konfigurálva. Ideális esetben ezek a hálózati adapterek eltérő irányítható alhálózatokkal rendelkezhetnek, azonban ebben az ellenőrzésben az azonos alhálózaton található hálózati adapterek lesznek.

![A részleges ipconfig parancs kimenete két Ethernet-adaptert mutat ugyanazon az alhálózaton. az IP-címek a következők: 10.33.131.15 és 10.33.131.16.](./media/azure-stack-network-howto-extend-datacenter/image9.png)

Az iSCSI-célkiszolgáló esetében ez lehet Windows Server 2016 vagy 2019, fizikai vagy virtuális, Hyper-V-n, VMware-en vagy egy tetszőleges alternatív berendezésen, például egy dedikált fizikai iSCSI SAN-gépen. A legfontosabb szempont, hogy az Azure Stack hub rendszerbe való csatlakozással és onnan kifelé irányuló kapcsolattal rendelkezik, azonban a forrás és a cél között több útvonal is van, mivel ez további redundanciát biztosít, és lehetővé teszi a fejlettebb képességek használatát nagyobb teljesítmény érdekében, például az MPIO-t.

Azt javasoljuk, hogy frissítse a Windows Server 2019 iSCSI-tárolót a legújabb összesítő frissítésekkel és javításokkal, szükség esetén újraindítás után, mielőtt továbblépne a fájlmegosztás konfigurálására.

Miután frissítette és újraindította a kiszolgálót, mostantól iSCSI-tárolóként is konfigurálhatja ezt a kiszolgálót.

1.  Nyissa meg a **Kiszolgálókezelő alkalmazást** , és válassza a **kezelés**, majd a **szerepkörök és szolgáltatások hozzáadása** lehetőséget.

2.  A megnyitást követően válassza a **tovább** lehetőséget, válassza a **szerepköralapú vagy a szolgáltatáson alapuló telepítés** lehetőséget, majd folytassa a beállításokat, amíg el nem éri a **kiszolgálói szerepkörök kiválasztása** lapot.

3.  Bontsa ki a **fájl-és tárolási szolgáltatások**, majd a **fájl & az iSCSI-szolgáltatások** elemet, és jelölje be az **iSCSI-célkiszolgáló** jelölőnégyzetet, és fogadja el az új funkciók hozzáadására vonatkozó előugró utasításokat, majd folytassa a befejezést

    ![A szerepkörök és szolgáltatások hozzáadása varázsló Megerősítés lapján a "telepítési beállítások megerősítése" cím szerepel. A fájl-és tárolási szolgáltatások kibővültek a fájl-és iSCSI-szolgáltatások megjelenítéséhez, amelyek kiterjeszthetők az iSCSI-célkiszolgáló megjelenítésére.](./media/azure-stack-network-howto-extend-datacenter/image10.png)

    Ha elkészült, zárjuk be a **Kiszolgálókezelő eszközt.**

4.  Nyissa meg a **fájlkezelőt,** navigáljon a C: gombra, \\ és **hozzon létre egy új**, **iSCSI** nevű mappát.

5.  Nyissa meg újra a **Kiszolgálókezelő alkalmazást** , és válassza a **fájl-és tárolási szolgáltatások** lehetőséget a bal oldali menüben.

6.  Válassza az **iSCSI** lehetőséget, majd a jobb oldali ablaktáblán válassza a "**virtuális iSCSI-lemez létrehozása, az új virtuális iSCSI-lemez varázsló indítása**" hivatkozást. Válassza ki. Megjelenik egy varázsló.

7.  Az **iSCSI virtuális lemez helyének kiválasztása** lapon jelölje be a választógombot **Egyéni elérési út megadásához** , és keresse meg a **C: \\ iSCSI** elemet, és kattintson a **tovább** gombra.

8.  Adja meg az iSCSI virtuális lemez **iSCSIdisk1** nevét és opcionálisan egy leírást, majd válassza a **tovább** lehetőséget.

9.  Állítsa a virtuális lemez méretét **10 GB** -ra, és válassza a **rögzített méret** lehetőséget, majd kattintson a **tovább** gombra.

    ![Az új iSCSI virtuális lemez varázsló iSCSI virtuális lemez mérete lapja rögzített méretű 10GB-ot határoz meg, a "virtuális lemez kiosztásának törlése" beállítás pedig be van jelölve.](./media/azure-stack-network-howto-extend-datacenter/image11.png)

10) Mivel ez egy új cél, válassza az **új iSCSI-tároló** elemet, és kattintson a **Tovább gombra**.

11) A **cél nevének megadása** lapon adja meg a **TARGET1** nevet, és kattintson a **Tovább gombra**.

12) A **hozzáférési kiszolgálók megadása** lapon válassza a **Hozzáadás** lehetőséget. Ekkor megnyílik egy párbeszédpanel, amely megadja az iSCSI-tárolóhoz való kapcsolódásra engedélyt igénylő **kezdeményezőket** .

13) A **kezdeményező azonosítójának hozzáadása ablakban** válassza a **megadott típus értékének megadása** lehetőséget, majd a legördülő menüben **jelölje be a** IQN jelölőnégyzetet. Adja meg a **IQN. 1991-05. com. \<computername> Microsoft:** where \<computername> a **VM001** **számítógép nevét** , majd válassza a **tovább** lehetőséget.

    ![A "kezdeményező azonosító hozzáadása" ablak a kezdeményező AZONOSÍTÓjának megadására szolgáló értékeket jeleníti meg.](./media/azure-stack-network-howto-extend-datacenter/image12.png)

14) A **hitelesítés engedélyezése** lapon hagyja üresen a mezőket, majd kattintson a **tovább** gombra.

15) Erősítse meg a beállításokat, majd kattintson a **Létrehozás**, majd a bezárás elemre. Meg kell jelennie a Kiszolgálókezelő alkalmazásban létrehozott iSCSI virtuális lemeznek.

    ![Az új virtuális iSCSI-lemez varázsló eredmények lapján látható, hogy az ISCSI virtuális lemez létrehozása sikeres volt.](./media/azure-stack-network-howto-extend-datacenter/image13.png)

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>A Windows Server 2019 iSCSI-kezdeményező és az MPIO konfigurálása

Az iSCSI-kezdeményező beállításához először jelentkezzen be az **Azure stack hub felhasználói portálra** az **Azure stack hub** rendszeren, és lépjen a VM001 **Áttekintés** paneljére **.**

1.  Hozzon létre egy RDP-kapcsolatot a VM001. Csatlakozás után nyissa meg a **Kiszolgálókezelő alkalmazást**.

2.  Válassza a **szerepkörök és szolgáltatások hozzáadása** lehetőséget, és fogadja el az alapértelmezett értékeket, amíg el nem éri a **szolgáltatások** lapot.

3.  A **szolgáltatások** lapon adja hozzá a **többutas I/O** elemet, majd kattintson a **tovább** gombra.

    ![A szerepkörök és szolgáltatások hozzáadása varázsló szolgáltatások lapján megjelenik az egyik funkció, a többutas I/O, a kiválasztva.](./media/azure-stack-network-howto-extend-datacenter/image14.png)

4.  Jelölje be a **célkiszolgáló automatikus újraindítása, ha szükséges** jelölőnégyzetet, majd válassza a **telepítés**, majd a **Bezárás lehetőséget.** Valószínűleg újraindításra lesz szükség, így ha elkészült, újra csatlakozhat a VM001.

5.  Vissza a **Kiszolgálókezelőben**, várja meg, amíg az **MPIO telepítése befejeződik**, válassza a **Bezárás**, majd az **eszközök** lehetőséget, és válassza az **MPIO** lehetőséget.

6.  Válassza a **több útvonal felderítése** fület, és jelölje be a jelölőnégyzetet az **iSCSI-eszközök támogatásának hozzáadásához** , majd válassza a **Hozzáadás** lehetőséget, majd válassza az **Igen** lehetőséget a VM001 **újraindításához** . Ha nem kap ablakot, válassza **az OK,** majd a manuális újraindítás lehetőséget.

    ![Az MPIO-párbeszédpanel felderítés több útvonala lapján látható, hogy be van jelölve az "iSCSI-eszközök támogatásának hozzáadása" beállítás. Van egy Hozzáadás gomb.](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7.  Az újraindítás után hozzon létre egy **új RDP-kapcsolatot a VM001**.

8.  Csatlakozás után nyissa meg a **Kiszolgálókezelő** eszközt, válassza az **eszközök** lehetőséget, és válassza az **iSCSI-kezdeményező** lehetőséget.

9.  Amikor megjelenik egy Microsoft iSCSI-ablak, válassza az **Igen** lehetőséget, hogy az iSCSI szolgáltatás alapértelmezés szerint fusson.

    ![A Microsoft iSCSI párbeszédpanel azt jelenti, hogy az iSCSI szolgáltatás nem fut; a szolgáltatás elindításához igen gomb szükséges.](./media/azure-stack-network-howto-extend-datacenter/image16.png)

10. Az iSCSI-kezdeményező tulajdonságai ablakban válassza a **felderítés** lapot.

11. Ekkor két célt fog hozzáadni, ezért először válassza a **felderítési portál** gombot.

12. Adja meg az iSCSI-célkiszolgáló első IP-címét, és válassza a **speciális** lehetőséget.

    ![A felderítési cél portál a 10.33.131.15 az "IP-cím vagy DNS-név:" szövegmezőben és a 3260 (az alapértelmezett) mezőben jeleníti meg a port szövegmezőben. Speciális gomb van.](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. A **Speciális beállítások** ablakban válassza ki az alábbiakat, majd kattintson az **OK gombra**.

    a.  **Helyi adapter**: Microsoft iSCSI-kezdeményező.

    b.  **Kezdeményező IP-címe**: 10.10.10.4.

14. A cél- **portál felderítése** ablakban kattintson az **OK gombra**.

15. Ismételje meg a folyamatot a következővel:

    a. * * IP-cím * *: a második iSCSI cél IP-címe.

    b.  **Helyi adapter**: Microsoft iSCSI-kezdeményező.

    c.  **Kezdeményező IP-címe**: 10.10.11.4.

16. A cél-portáloknak így kell kinézniük, a saját iSCSI cél IP- **címeivel a cím** oszlopban.

    ![A "cél portálok" párbeszédpanel az imént létrehozott két portált jeleníti meg. Az IP-címek a következők: 10.33.131.15 és 10.33.131.16.](./media/azure-stack-network-howto-extend-datacenter/image18.png)

17. A **célok** lapon válassza ki az iSCSI-tárolót az ablak közepéről, majd válassza a **kapcsolat** lehetőséget.

18. A **Kapcsolódás a célhelyhez** ablakban jelölje be a **több útvonal engedélyezése** jelölőnégyzetet, majd válassza a **speciális** lehetőséget.

    ![A "kapcsolódás a célhoz" párbeszédpanel a megadott értékeket jeleníti meg. Van egy speciális gomb és egy OK gomb.](./media/azure-stack-network-howto-extend-datacenter/image19.png)

19. Adja meg a következő információkat, majd kattintson az **OK gombra**, majd a **Kapcsolódás a célhelyhez** ablakban kattintson az **OK gombra**.

    a.  **Helyi adapter**: Microsoft iSCSI-kezdeményező.

    b.  **Kezdeményező IP-címe**: 10.10.10.4.

    c.  **Cél-portál IP-címe**: \<your first iSCSI Target IP / 3260> .

    ![A "kapcsolat használata" párbeszédpanellel a célzott portál 10.33.131.15/3260 megadott információi láthatók.](./media/azure-stack-network-howto-extend-datacenter/image20.png)

1.  Ismételje meg a folyamatot a második kezdeményező/cél kombinációnál.

    a. * * Helyi adapter * *: Microsoft iSCSI-kezdeményező.

    b.  **Kezdeményező IP-címe**: 10.10.11.4.

    c.  **Cél-portál IP-címe**: \<your second iSCSI Target IP / 3260> .

    ![A "kapcsolat használata" párbeszédpanellel a célzott portál 10.33.131.16/3260 megadott információi láthatók.](./media/azure-stack-network-howto-extend-datacenter/image21.png)

2.  Válassza a **kötetek és eszközök** fület, majd kattintson az **automatikus konfigurálás** lehetőségre – a rendszer az MPIO-kötetet mutatja be:

    ![A kötetek listája ablak a kötet nevét, a csatlakoztatási pontot és az eszközt jeleníti meg egyetlen köteten.](./media/azure-stack-network-howto-extend-datacenter/image22.png)

3.  A **célok** lapon válassza az **eszközök** lehetőséget, és 2 kapcsolatot kell látnia a korábban létrehozott iSCSI VHD-vel.

    ![Az eszközök párbeszédpanel két sorban jeleníti meg a 2. lemezt. A cél 0 az első sorban, 1 a másodikban.](./media/azure-stack-network-howto-extend-datacenter/image23.png)

4.  Kattintson a **többutas i/o (MPIO) gombra** a terheléselosztási szabályzattal és elérési utakkal kapcsolatos további információk megjelenítéséhez.

    ![Az eszközök részletek párbeszédpanelének MPIO-lapja a "terheléselosztási házirend" ciklikus időszeletelését jeleníti meg, és két eszközt listáz.](./media/azure-stack-network-howto-extend-datacenter/image24.png)

5.  Kattintson háromszor az **OK gombra** a Windows és az iSCSI-kezdeményező kilépéséhez.

6.  Nyissa meg a Lemezkezelés (diskmgmt. msc) párbeszédpanelt, és a rendszer a **lemez inicializálása** ablakban kéri a kérdést.

    ![A lemez inicializálása párbeszédpanel megjeleníti a 2. lemezt, valamint a partíció stílusaként kiválasztott MBR-t (fő rendszerindító rekordot). Van egy OK gomb.](./media/azure-stack-network-howto-extend-datacenter/image25.png)

7.  Az alapértelmezett beállítások elfogadásához kattintson az **OK** gombra, majd görgessen le az új lemezre, kattintson a jobb gombbal, és válassza az **új egyszerű kötet** lehetőséget.

8.  A varázsló végigvezeti az alapértelmezett beállítások elfogadásával. Módosítsa a kötet címkéjét a **iSCSIdisk1** értékre, majd válassza a **Befejezés** lehetőséget.

    ![Az új egyszerű kötet varázsló párbeszédpanelen látható, hogy a kötetnek NTFS-nek kell lennie az alapértelmezett kiosztási egység méretével és a "iSCSIdisk1" kötet címkéjével. A gyorsformázás beállítás van kiválasztva. Van egy következő gomb.](./media/azure-stack-network-howto-extend-datacenter/image26.png)

9.  A meghajtót ezután formáznia kell, és meg kell adni egy meghajtóbetűjelet.

10. Nyissa meg a **fájlkezelőt** , és válassza ki **ezt a számítógépet** , és tekintse meg a VM001-hez csatolt új meghajtót.

### <a name="testing-external-storage-connectivity"></a>Külső tárterület kapcsolatának tesztelése

A kommunikáció ellenőrzéséhez és a fájl-másolási teszt futtatásához először jelentkezzen be az **Azure stack hub felhasználói portálra** az **Azure stack hub** rendszeren, és navigáljon a **VM001** **Áttekintés** paneljére

1.  Válassza a **Kapcsolódás** lehetőséget RDP-kapcsolat létrehozásához a **VM001** -ben

2.  A **Feladatkezelő** megnyitásához válassza a **teljesítmény** fület, majd az ablakot az RDP-munkamenet jobb oldalán.

3.  Nyissa meg a **Windows PowerShell integrált parancsprogram-kezelési környezet** rendszergazdaként, és pattintsa az RDP-munkamenet bal oldali oldalára. Az ISE jobb oldalán kattintson a **parancsok** ablaktábla bezárásához, majd a **parancsfájl** gombra, és bontsa ki a fehér parancsfájl panelt az ISE ablak tetején.

4.  Ebben a virtuális gépen nincsenek natív PowerShell-modulok egy virtuális merevlemez létrehozásához, amelyet nagy fájlként fogunk használni az iSCSI-tárolóba való fájlátvitel teszteléséhez. Ebben az esetben a DiskPartt egy VHD-fájl létrehozásához fogjuk futtatni. Az ISE-ben futtassa a következőt:

    1. `Start-Process Diskpart`

    2. Ekkor megnyílik egy új CMD ablak, majd írja be a következőket:  
        `**Create vdisk file="c:\\test.vhd" type=fixed maximum=5120**`
    
    ![A CMD ablakban látható, hogy a megadott parancs a DiskPart számára lett kiállítva, amely sikeresen befejeződött, és létrehozza a virtuális lemez fájlját.](./media/azure-stack-network-howto-extend-datacenter/image27.png)
    
    3.  A létrehozás eltarthat néhány másodpercig. Miután létrehozta a létrehozását, a létrehozás ellenőrzéséhez nyissa meg a **fájlkezelőt** , és navigáljon a C:-ra. Ekkor \\ megjelenik az új test. vhd, és mérete 5 GB.

    ![A fájl tesztelése. vhd a várt módon jelenik meg a C: értékben \, , és a megadott méret.](./media/azure-stack-network-howto-extend-datacenter/image28.png)

    4. Lépjen be a CMD ablakba, és térjen vissza az ISE-hez, majd írja be a következő parancsot a parancsfájl ablakába. Az F: helyére írja be \\ a korábban alkalmazott iSCSI-tároló betűjelét.

    5. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    6. Válassza ki a sort a parancsfájl ablakban, majd az F8 billentyű lenyomásával futtassa a parancsot.

    7. A parancs futása közben figyelje meg a két hálózati adaptert, és tekintse át a VM001-ben mindkét hálózati adapteren zajló adatátvitelt. Azt is figyelje meg, hogy minden hálózati adapternek egyenletesen kell megosztania a terhelést.

    ![Mindkét adapter 2,6 Mbps terhelést mutat.](./media/azure-stack-network-howto-extend-datacenter/image29.png)

Ennek a forgatókönyvnek a célja, hogy kiemelje az Azure Stack hub-on futó munkaterhelés és egy külső tárolási tömb, ebben az esetben egy Windows Server-alapú iSCSI-tároló közötti kapcsolatot. Ez nem teljesítménytesztnek lett kialakítva, és nem tükrözheti azokat a lépéseket, amelyeket végre kell hajtania, ha alternatív iSCSI-alapú készüléket használ, de a munkaterhelések Azure Stack hubhoz való üzembe helyezéséhez szükséges alapvető szempontokat is kiemeli, és csatlakoztatja azokat a Azure Stack hub-környezeten kívüli tárolási rendszerekhez.

## <a name="next-steps"></a>Következő lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md)
