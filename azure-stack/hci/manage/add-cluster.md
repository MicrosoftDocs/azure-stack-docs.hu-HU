---
title: Kiszolgálók hozzáadása vagy eltávolítása egy Azure Stack HCI-fürthöz
description: Ismerje meg, hogyan adhat hozzá vagy távolíthat el kiszolgálói csomópontokat egy fürtből Azure Stack HCI-ben
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 01/06/2021
ms.openlocfilehash: 8b27859b7afab0a6e279774e43d0269f6d58065a
ms.sourcegitcommit: 1465bca8b7f87ea6f24faf47e86c2ba497943b28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2021
ms.locfileid: "98103134"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Kiszolgálók hozzáadása vagy eltávolítása egy Azure Stack HCI-fürthöz

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Azure Stack HCI-ben könnyedén hozzáadhat vagy eltávolíthat kiszolgálókat egy fürtből. Ne feledje, hogy minden egyes új fizikai kiszolgálónak szorosan meg kell egyeznie a fürtben lévő többi kiszolgálóval, amikor a processzor típusa, a memória, a meghajtók száma, valamint a meghajtók típusa és mérete is megfelel.

Amikor kiszolgálót ad hozzá vagy távolít el, a fürt érvényesítését is végre kell hajtania, hogy a fürt megfelelően működjön. Ez a nem kifeszített és a kibővített fürtökre is vonatkozik.

## <a name="obtain-oem-hardware"></a>OEM-hardver beszerzése

Az első lépés az új HCI-hardver beszerzése az eredeti OEM-ből. Mindig tekintse meg a SZÁMÍTÓGÉPGYÁRTÓ által biztosított dokumentációt, amikor új kiszolgálói hardvert ad hozzá a fürtben való használathoz.

1. Helyezze az új fizikai kiszolgálót az állványba, és csatlakoztassa megfelelően.
1. Engedélyezheti a fizikai kapcsoló portjait, és módosíthatja a hozzáférés-vezérlési listákat (ACL) és a VLAN-azonosítókat.
1. Konfigurálja a megfelelő IP-címet a alaplapi felügyeleti vezérlőben (BMC), és az összes BIOS-beállítást alkalmazza OEM-utasítások alapján.
1. Alkalmazza a jelenlegi belső vezérlőprogram-alapkonfigurációt az összes összetevőre az OEM által biztosított eszközök használatával.
1. Futtassa az OEM-ellenőrző teszteket a meglévő fürtcsomópontok egységességének biztosításához.

## <a name="add-a-server-to-a-cluster"></a>Kiszolgáló hozzáadása fürthöz

Ha a kiszolgáló megfelelően megpördült, a Windows felügyeleti központban csatlakoztassa a kiszolgálót a fürthöz.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Kiszolgáló hozzáadása képernyő" lightbox="media/manage-cluster/add-server.png":::

1. A **Windows felügyeleti központban** válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. A **fürt kapcsolatai** területen válassza ki a fürtöt.
1. Az **eszközök** területen válassza a **kiszolgálók** elemet.
1. A **kiszolgálók** területen válassza a **leltár** lapot.
1. A **leltár** lapon válassza a **Hozzáadás** lehetőséget.
1. A **kiszolgáló neve** mezőben adja meg a hozzáadni kívánt kiszolgáló teljes tartománynevét, kattintson a **Hozzáadás** gombra, majd a lap alján kattintson a **Hozzáadás** gombra.
1. Győződjön meg arról, hogy a kiszolgáló sikeresen hozzá lett adva a fürthöz.

## <a name="remove-a-server-from-a-cluster"></a>Kiszolgáló eltávolítása fürtből

A kiszolgálók fürtből való eltávolításának lépései hasonlóak a kiszolgálók fürthöz való hozzáadásához.

Ne feledje, hogy amikor eltávolít egy kiszolgálót, a kiszolgálóhoz társított összes virtuális gépet, meghajtót és munkaterhelést is el fogja távolítani.

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Kiszolgáló eltávolítása párbeszédpanel" lightbox="media/manage-cluster/remove-server.png":::

1. A **Windows felügyeleti központban** válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. A **fürt kapcsolatai** területen válassza ki a fürtöt.
1. Az **eszközök** területen válassza a **kiszolgálók** elemet.
1. A **kiszolgálók** területen válassza a **leltár** lapot.
1. A **leltár** lapon válassza ki az eltávolítani kívánt kiszolgálót, majd kattintson az **Eltávolítás** gombra.
1. Ha az összes kiszolgálói meghajtót is el szeretné távolítani a tárolóból, engedélyezze ezt a jelölőnégyzetet.
1. Ellenőrizze, hogy a kiszolgáló sikeresen el lett-e távolítva a fürtből.

Amikor kiszolgálókat ad hozzá vagy távolít el egy fürtből, akkor győződjön meg róla, hogy utána futtat egy fürt-ellenőrzési tesztet.

## <a name="add-server-pairs-to-a-stretched-cluster"></a>Kiszolgálói párok hozzáadása egy kifeszített fürthöz

A kifeszített fürtökhöz azonos számú kiszolgáló-csomópont és azonos számú meghajtó szükséges az egyes helyekhez. Ha egy kiszolgáló pár-t egy kibővített fürthöz ad hozzá, a rendszer azonnal hozzáadja a meghajtókat a kihelyezett fürt mindkét helyének tárolási készletéből. Ha az egyes helyeken lévő tárolók mérete nem egyezik a Hozzáadás időpontjában, a rendszer elveti. Ennek az az oka, hogy a tárolási készlet méretének azonosnak kell lennie a helyek között.

Több percet is igénybe vehet, és megtekintheti a kiszolgáló-csomópontok kiterjesztett fürthöz való hozzáadásának videóját:

> [!VIDEO https://www.youtube.com/embed/AVHPkRmsZ5Y]

Kiszolgálókat adhat hozzá vagy távolíthat el egy kifeszített fürthöz a Windows PowerShell használatával. A [Get-ClusterFaultDomainXML](https://docs.microsoft.com/powershell/module/failoverclusters/get-clusterfaultdomainxml) és a [set-ClusterFaultDomainXML](https://docs.microsoft.com/powershell/module/failoverclusters/set-clusterfaultdomainxml) parancsmagok használatával a kiszolgálók hozzáadása előtt először a hely (tartalék tartomány) adatait kell módosítania.

Ezután hozzáadhatja a kiszolgálói párt az egyes helyekhez az [Add-fürtcsomópont](https://docs.microsoft.com/powershell/module/failoverclusters/add-clusternode) parancsmaggal egyidejűleg, amely lehetővé teszi, hogy minden új kiszolgáló meghajtója egy időben is felvehető legyen.

A fürtöket általában egy távoli számítógépről kell kezelni, nem pedig a fürtben lévő kiszolgálókon. Ezt a távoli számítógépet felügyeleti számítógépnek nevezzük.

> [!NOTE]
> Ha felügyeleti számítógépről futtat PowerShell-parancsokat, a `-Cluster` paramétert a kezelt fürt nevével együtt kell megadni.

Ok, kezdjük:

1. A fürt állapotának meghatározásához használja a következő PowerShell-parancsmagokat:

    A fürtben lévő aktív kiszolgálók listáját adja vissza:

     ```powershell
    Get-ClusterNode
    ```

    A fürt tárolási készletének statisztikáit adja vissza:

    ```powershell
    Get-StoragePool pool*
    ```

    Felsorolja, hogy mely kiszolgálókon találhatók a hely (tartalék tartomány):

    ```powershell
    Get-ClusterFaultDomain
    ```

1. Nyissa meg a `Sites.xml` fájlt a Jegyzettömbben vagy más szövegszerkesztőben:

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. Navigáljon oda, ahol a `Sites.xml` fájl helyileg található a felügyeleti számítógépen, és nyissa meg a fájlt. A `Sites.xml` fájl a következőhöz hasonlóan fog kinézni:

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
        </Site>
    <Topology>
    ```

1. Ebben a példában a következő módon kell hozzáadnia egy kiszolgálót az egyes helyekhez ( `Server5` `Server6` ):

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
            <Node Name="Server5" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
            <Node Name="Server6" Description="" Location="">
        </Site>
    <Topology>
    ```

1. Az aktuális hely (tartalék tartomány) adatainak módosítása.  Az első parancs egy változót állít be a fájl tartalmának beolvasásához `Sites.xml` és a kimenethez. A második parancs a változó alapján állítja be a módosítást `$XML` .

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. Ellenőrizze, hogy helyesek-e az elvégzett módosítások:

    ```
    Get-ClusterFaultDomain
    ```

1. Adja hozzá a kiszolgálói párt a fürthöz a következő `Add-ClusterNode` parancsmag használatával:

    ```
    Add-ClusterNode -Name Server5,Server6
    ```

A kiszolgálók sikeres hozzáadása után a rendszer automatikusan hozzáadja a társított meghajtókat az egyes helyekhez. Végül a Állapotfigyelő szolgáltatás létrehoz egy tárolási feladatot az új meghajtók belefoglalásához.

## <a name="remove-server-pairs-from-a-stretched-cluster"></a>Kiszolgálói párok eltávolítása egy kifeszített fürtből

Egy kiszolgálói pár egy kiterjesztett fürtből való eltávolítása hasonló folyamat egy kiszolgálói pár hozzáadásához, de ehelyett a [Remove-fürtcsomópont](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusternode) parancsmagot használja.

1. A fürt állapotának meghatározásához használja a következő PowerShell-parancsmagokat:

    A fürtben lévő aktív kiszolgálók listáját adja vissza:

     ```powershell
    Get-ClusterNode
    ```

    A fürt tárolási készletének statisztikáit adja vissza:

    ```powershell
    Get-StoragePool pool*
    ```

    Felsorolja, hogy mely kiszolgálókon találhatók a hely (tartalék tartomány):

    ```powershell
    Get-ClusterFaultDomain
    ```

1. Nyissa meg a `Sites.xml` fájlt a Jegyzettömbben vagy más szövegszerkesztőben:

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. Az előző példa használatával a `Sites.xml` fájlban távolítsa el a `<Node Name="Server5" Description="" Location="">` és az  `<Node Name="Server6" Description="" Location="">` XML-bejegyzést az egyes helyekhez.
1. Módosítsa az aktuális hely (tartalék tartomány) adatait a következő két parancsmag használatával:

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. Ellenőrizze, hogy helyesek-e az elvégzett módosítások:

    ```
    Get-ClusterFaultDomain
    ```
1. Távolítsa el a kiszolgálói párokat a fürtből a következő `Remove-ClusterNode` parancsmag használatával:

    ```
    Remove-ClusterNode -Name Server5,Server6
    ```

A kiszolgálók sikeres eltávolítása után a rendszer automatikusan eltávolítja a társított meghajtókat a hely készletből. Végül a Állapotfigyelő szolgáltatás létrehoz egy tárolási feladatot a meghajtók eltávolításához.

## <a name="next-steps"></a>Következő lépések

- A kiszolgáló hozzáadásával vagy eltávolításával érvényesítse a fürtöt. További információ: [a fürt érvényesítése](../deploy/validate.md) további információért.