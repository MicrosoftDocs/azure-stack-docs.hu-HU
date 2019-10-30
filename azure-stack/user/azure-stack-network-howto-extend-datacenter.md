---
title: Az adatközpont kiterjesztése a Azure Stackra | Microsoft Docs
description: Ismerje meg, hogyan bővítheti az adatközpontot Azure Stackon.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 94a9398a68be06d4735e2c082e8dc0a02281b6eb
ms.sourcegitcommit: 58e1911a54ba249a82fa048c7798dadedb95462b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064789"
---
# <a name="how-to-extend-the-data-center-on-azure-stack"></a>Az adatközpont kiterjesztése Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a cikk Azure Stack tárolási infrastruktúrával kapcsolatos információkat tartalmaz, amelyek segítségével eldöntheti, hogyan integrálhatja Azure Stack a meglévő hálózati környezetbe. A tárolóhely Azure Stack korlátozott. A tárolót integrálhatja a Azure Stackon kívüli tárolóval és a helyi adatközpontba. Az adatközpont kibővítésével kapcsolatos általános vitát követően a cikk két különböző forgatókönyvet mutat be. Csatlakozhat egy Windows file Storage-kiszolgálóhoz. Egy Windows iSCSI-kiszolgálóhoz is csatlakozhat.

## <a name="overview-of-extending-storage-to-azure-stack"></a>A tároló Azure Stackra való kiterjesztésének áttekintése

Az ábrán egy olyan forgatókönyv látható, amelyben egyetlen virtuális gép fut, amely a munkaterhelést, a csatlakozást és a külső (a virtuális gép, a Azure Stack saját maga) tárolót használja az adatok olvasására/írására stb. Ebben a cikkben a fájlok egyszerű lekérésére koncentrálhat, de ezt a példát kiterjesztheti összetettebb forgatókönyvek, például az adatbázisfájlok távoli tárolása érdekében.

![Azure Stack tárterület kiterjesztése](./media/azure-stack-network-howto-extend-datacenter/image1.png)

A diagramon láthatja, hogy a Azure Stack rendszerű virtuális gép több hálózati adapterrel lett telepítve. Mind a redundancia, hanem a tárolási ajánlott eljárás is fontos, hogy több útvonal legyen a cél és a cél között. Ahol a dolgok összetettebbek lesznek, a Azure Stackban lévő virtuális gépek nyilvános és magánhálózati IP-címekkel is rendelkeznek, ugyanúgy, mint az Azure-ban. Ha a külső tárterület szükséges a virtuális gép eléréséhez, akkor csak a nyilvános IP-címen keresztül végezhető el, mivel a magánhálózati IP-címek elsődlegesen a virtuális hálózatok és az alhálózatokon belüli Azure Stack rendszerekben vannak használatban. A külső tároló nem tud kommunikálni a virtuális gép magánhálózati IP-címével, kivéve, ha a helyek közötti VPN-kapcsolaton halad át, hogy magához a vNet kapcsolódjon. Ebben a példában a nyilvános IP-címen keresztül folytatott kommunikációra koncentrálunk. Az egyik dolog, ami a grafikában a nyilvános IP-címmel kapcsolatos, az, hogy 2 különböző nyilvános IP-címkészlet alhálózata van. Alapértelmezés szerint a Azure Stack csak egy készletet igényel a nyilvános IP-címekhez, de megfontolandó, hogy a redundáns útválasztáshoz egy másodpercet adjon hozzá. Egy adott készletből azonban nem lehet IP-címet kijelölni, így előfordulhat, hogy az azonos készletből származó virtuális gépeket több virtuális hálózati kártyán is megtalálhatja.

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

13. A DNS-név területen válassza a **Konfigurálás** lehetőséget, és adja meg a DNS-név címkét, a **vm001** , majd a **Mentés**lehetőséget, majd válassza a **vm001** lehetőséget a navigációs panelen az *Áttekintés* panelre való visszatéréshez.

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

4)  Bontsa ki a **fájl-és tárolási szolgáltatások**, majd a fájl **& az iSCSI-szolgáltatások** elemet, és jelölje be a **fájlkiszolgáló** mezőt. Ha elkészült, zárjuk be a **Kiszolgálókezelő eszközt**.

5)  Nyissa meg újra a **Kiszolgálókezelő eszközt** , és válassza a **fájl-és tárolási szolgáltatások**lehetőséget.

6)  Válassza a **megosztások**lehetőséget.

7)  A **megosztások mezőben**jelölje ki a **fájlmegosztás létrehozását, majd indítsa el az új megosztás varázsló** hivatkozását.

8)  Az **új megosztás varázslóban** válassza az **SMB-megosztás – gyors**lehetőséget, majd a **tovább**gombot, és folytassa a varázslót, és válassza a **C:\\\\ kötet**lehetőséget.

9)  Adja meg a megosztás nevét, **TestStorage** majd válassza a **tovább**lehetőséget.

10) Vissza az **új megosztás varázslóban**válassza a **tovább** , majd a **Létrehozás**, majd a **Bezárás**lehetőséget.

Ezzel létrehozta a fájlmegosztást a fájlkiszolgálón.

### <a name="testing-file-storage-performance-and-connectivity"></a>A fájlok tárolási teljesítményének és kapcsolatának tesztelése

A kommunikáció ellenőrzéséhez és néhány kezdetleges teszt futtatásához jelentkezzen be a Azure Stack felhasználói portálra a **Azure stack** rendszeren, és lépjen a **VM001** **Áttekintés** paneljére.

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

Ha még nem telepítette a Windows Server 2019 rendszerű virtuális gépet Azure Stackon, kövesse a [Windows server 2019 virtuális gép telepítése Azure stackon](#deploy-the-windows-server-2019-vm-on-azure-stack)című témakör lépéseit. Ezután beállíthatja a Windows Server 2019 iSCSI-tárolót.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>A Windows Server 2019 iSCSI-tároló konfigurálása

A második forgatókönyv esetében ellenőrizze, hogy van-e olyan konfiguráció, amelyben a Windows Server 2019 iSCSI-tároló a Hyper-V-n futó virtuális gép. A virtuális gép nyolc virtuális processzorral, egyetlen VHDX-fájllal és a legfontosabb két virtuális hálózati adapterrel lesz konfigurálva. Ideális esetben ezek a hálózati adapterek eltérő irányítható alhálózatokkal rendelkeznek, de ebben a tesztben a hálózati adapterek ugyanazon az alhálózaton lesznek.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

Az iSCSI-tárolóhoz a Windows Server 2016-es vagy 2019-es, fizikai vagy virtuális, Hyper-V-, VMware-vagy más platformon futó operációs rendszer lehet. A legfontosabb szempont, hogy a Azure Stack rendszerhez való kapcsolódási és kimenő kapcsolattal rendelkezik, azonban a forrás és a cél között több útvonal is van, mivel ez további redundanciát és átviteli sebességet biztosít.

Frissítse a fájlkiszolgáló legújabb összesítő frissítéseit és javításait, majd indítsa újra a rendszert az iSCSI-célkiszolgáló konfigurációjának folytatása előtt.

A frissítést és az újraindítást követően ezt a kiszolgálót már iSCSI-célkiszolgálóként is konfigurálhatja.

## <a name="next-steps"></a>Következő lépések

[Különbségek és szempontok Azure Stack hálózatkezeléshez](azure-stack-network-differences.md)  