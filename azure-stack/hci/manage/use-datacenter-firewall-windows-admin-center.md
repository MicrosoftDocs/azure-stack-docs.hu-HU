---
title: Hozzáférés-vezérlési listák konfigurálása a Windows felügyeleti központtal a Datacenter tűzfal használatával
description: Ez a témakör azt ismerteti, hogyan használható a Windows felügyeleti központ hozzáférés-vezérlési listák (ACL-ek) létrehozásához és konfigurálásához az adatforgalom áramlását a Datacenter tűzfal és a szoftveresen megadott hálózati (SDN) logikai és virtuális hálózatok ACL-jei használatával.
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 03/04/2021
ms.openlocfilehash: 342f2771d74a2347cd53fa9364089a934885c671
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102194247"
---
# <a name="use-datacenter-firewall-to-configure-acls-with-windows-admin-center"></a>Hozzáférés-vezérlési listák konfigurálása a Windows felügyeleti központtal a Datacenter tűzfal használatával

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019, Windows Server 2016

Ez a témakör részletesen ismerteti, hogyan használhatja a Windows felügyeleti központot hozzáférés-vezérlési listák (ACL-ek) létrehozásához és konfigurálásához az adatforgalom adatforgalmának a Datacenter tűzfal használatával történő kezeléséhez. Útmutatást nyújt továbbá a szoftveresen definiált hálózati (SDN) logikai és virtuális hálózatok hozzáférés-vezérlési listáinak kezeléséhez. Az adatközpont tűzfalát az ACL-ek létrehozásával és egy alhálózatra vagy egy hálózati adapterre való alkalmazásával engedélyezheti és konfigurálhatja. További információ: [Mi az az adatközpont tűzfala?](../concepts/datacenter-firewall-overview.md) A PowerShell-parancsfájlok használatához lásd: az [adatközpont tűzfalának használata az ACL-ek konfigurálásához a PowerShell használatával](use-datacenter-firewall-powershell.md).

Az ACL-ek konfigurálása előtt telepítenie kell a hálózati vezérlőt. A hálózati vezérlővel kapcsolatos további tudnivalókért tekintse meg a [Mi az a hálózati vezérlő?](../concepts/network-controller-overview.md) című témakört. A hálózati vezérlő PowerShell-parancsfájlok használatával történő telepítéséhez lásd: [Sdn-infrastruktúra üzembe helyezése](sdn-express.md).

Emellett, ha az SDN logikai hálózat ACL-jeit is alkalmazni kívánja, először létre kell hoznia egy logikai hálózatot. Hasonlóképpen, ha ACL-eket szeretne alkalmazni egy SDN virtuális hálózatra, először létre kell hoznia egy virtuális hálózatot. További információ:
- [Bérlői logikai hálózatok kezelése](tenant-logical-networks.md)
- [Bérlői virtuális hálózatok kezelése](tenant-virtual-networks.md)

## <a name="create-an-acl"></a>ACL létrehozása
Egyszerűen létrehozhat egy ACL-t a Windows felügyeleti központban.

:::image type="content" source="./media/access-control-lists/create-acl.png" alt-text="Képernyőfelvétel a Windows felügyeleti központ kezdőlapján a Access Control lista neve mezővel." lightbox="./media/access-control-lists/create-acl.png":::

1. A Windows felügyeleti központ kezdőlapjának **minden kapcsolat** területén válassza ki azt a fürtöt, amelyhez az ACL-t létre kívánja hozni.
1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **hozzáférés-vezérlési listát**.
1. A **hozzáférés-vezérlési lista** területen válassza a **leltár** fület, majd válassza az **új** lehetőséget.
1. A **Access Control lista** panelen írja be az ACL nevét, majd válassza a **Submit (Küldés**) lehetőséget.
1. A **hozzáférés-vezérlési listák** területen ellenőrizze, hogy az új ACL **kiépítési állapota** **sikeres** volt-e.

## <a name="create-acl-rules"></a>ACL-szabályok létrehozása
Az ACL létrehozása után készen áll az ACL-szabályok létrehozására. Ha ACL-szabályokat kíván alkalmazni a bejövő és a kimenő forgalomra is, két szabályt kell létrehoznia.

:::image type="content" source="./media/access-control-lists/create-acl-rules.png" alt-text="Képernyőkép a Windows felügyeleti központról, amely a Access Control szabály panelt mutatja." lightbox="./media/access-control-lists/create-acl-rules.png":::

1. A Windows felügyeleti központ kezdőlapjának **minden kapcsolat** területén válassza ki azt a fürtöt, amelyhez az ACL-t létre kívánja hozni.
1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **hozzáférés-vezérlési listát**.
1. A **hozzáférés-vezérlési listák** területen válassza a **leltár** fület, majd válassza ki az imént létrehozott ACL-t.
1. A **Access Control szabály** területen válassza az **új** lehetőséget.
1. A **Access Control szabály** ablaktáblán adja meg a következő adatokat:
    1. A szabály **neve** .
    1. A szabály **prioritása** – elfogadható értékek: **101** – **65000**. Az alacsonyabb érték magasabb prioritást jelöl.
    1. **Típusok** – ez lehet bejövő vagy kimenő.
    1. **Protokoll** – a bejövő vagy kimenő csomagnak megfelelő protokoll meghatározása. Elfogadható értékek: **mind**, **TCP** és **UDP**.
    1. **Forráscím előtagja** – Itt adhatja meg a bejövő vagy kimenő csomagnak megfelelő forráscím-előtagot. Ha a *-t adja meg, az az összes forrásoldali címet jelöli.
    1. **Forrásport-tartomány** – a bejövő vagy kimenő csomagnak megfelelő forrásport-tartomány meghatározása. Ha a *-t adja meg, az az összes forrásport kijelölhető.
    1. **Célcím előtagja** – Itt adhatja meg a bejövő vagy kimenő csomagnak megfelelő célcím-előtagot. Ha a *-t adja meg, az az összes célhelyet jelöli.
    1. **Célport tartománya** – Itt adhatja meg a bejövő vagy kimenő csomagnak megfelelő célport tartományát. Ha a *-t adja meg, az az összes célport jelölését jelöli.
    1. **Műveletek** – ha a fenti feltételek teljesülnek, a csomag engedélyezéséhez vagy letiltásához válassza a megfelelő lehetőséget. Az elfogadható értékek: **Allow** és **deny**.
    1. **Naplózás** – a szabály naplózásának engedélyezéséhez vagy letiltásához adjon meg egy vagy több beállítást. Ha engedélyezve van a naplózás, a szabálynak megfelelő összes forgalmat naplózza a rendszer a gazdagépen.
1. Válassza a **Küldés** lehetőséget.

## <a name="apply-an-acl-to-a-virtual-network"></a>ACL alkalmazása virtuális hálózatra
Miután létrehozott egy ACL-t és a hozzá tartozó szabályokat, alkalmaznia kell őket egy virtuális hálózati alhálózatra, egy logikai hálózati alhálózatra vagy egy hálózati adapterre.

:::image type="content" source="./media/access-control-lists/apply-acl-virtual-network.png" alt-text="A virtuális alhálózat panelt bemutató Windows felügyeleti központ képernyőképe." lightbox="./media/access-control-lists/apply-acl-virtual-network.png":::

1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **virtuális hálózatok** elemet.
1. Válassza a **leltár** fület, majd válasszon ki egy virtuális hálózatot. A következő lapon válasszon ki egy virtuális hálózati alhálózatot, majd válassza a **Beállítások** lehetőséget.
1. Válasszon ki egy ACL-t a legördülő listából, majd válassza a **Submit (Küldés**) lehetőséget.

    Az utolsó lépés végrehajtásával társítja a ACL-t a virtuális hálózati alhálózathoz, és alkalmazza azt a virtuális hálózati alhálózathoz csatlakoztatott összes számítógépre.

## <a name="apply-an-acl-to-a-network-interface"></a>ACL alkalmazása hálózati adapterre
Az ACL-t egy hálózati adapterre alkalmazhatja egy virtuális gép (VM) létrehozásakor vagy később is.

:::image type="content" source="./media/access-control-lists/apply-acl-network-interface.png" alt-text="Képernyőkép a Windows felügyeleti központról, amely a hálózati beállítással társít egy ACL-t egy hálózati adapterhez." lightbox="./media/access-control-lists/apply-acl-network-interface.png":::

1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **virtuális gépek** lehetőséget.
1. Válassza a **leltár** lapot, válasszon ki egy virtuális gépet, majd válassza a **Beállítások** lehetőséget.
1. A **Beállítások** lapon válassza a **hálózatok** elemet.
1. Görgessen le a **hozzáférés-vezérlési listához**, bontsa ki a legördülő listát, válasszon ki egy ACL-t, majd válassza a **hálózati beállítások mentése** elemet.

    Az utolsó lépés végrehajtásával társítja az ACL-t a hálózati adapterhez, és a hálózati adapter összes bejövő és kimenő forgalmát alkalmazza.

## <a name="get-a-list-of-acls"></a>ACL-ek listájának beolvasása
A fürt összes ACL-listáját egyszerűen megtekintheti egy listában.

:::image type="content" source="./media/access-control-lists/get-acl-list.png" alt-text="A Windows felügyeleti központ képernyőképe, amely a leltár lapon található ACL-ek listáját jeleníti meg." lightbox="./media/access-control-lists/get-acl-list.png":::

1. A Windows felügyeleti központ kezdőlapjának **minden kapcsolat** területén válassza ki azt a fürtöt, amelyre vonatkozóan meg szeretné tekinteni az ACL-ek listáját.
1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **hozzáférés-vezérlési listát**.
1. A **leltár** lapon a fürtön elérhető ACL-ek listája látható, és olyan parancsokat biztosít, amelyek segítségével kezelheti az egyes ACL-eket a listában. A következőket teheti:
    - Az ACL-ek listájának megtekintése.
    - Megtekintheti az egyes ACL-listákra vonatkozó szabályok számát, valamint az egyes ACL-re alkalmazott alkalmazott alhálózatok és hálózati adapterek számát.
    - Az egyes ACL-ek **kiépítési állapotának** megtekintése (**sikeres**, **sikertelen**).
    - Hozzáférés-vezérlési lista törlése.
    - Ha kijelöl egy ACL-t a listában, megtekintheti annak szabályait. Ezután hozzáadhatja, törölheti vagy módosíthatja az ACL-szabályok beállításait.

## <a name="delete-an-acl"></a>Hozzáférés-vezérlési lista törlése
Ha már nincs szüksége rá, törölheti az ACL-t.

>[!NOTE]
> Miután töröl egy ACL-t az ACL-ek listájáról, győződjön meg arról, hogy az nem tartozik alhálózathoz vagy hálózati adapterhez.

:::image type="content" source="./media/access-control-lists/delete-acl.png" alt-text="A Windows felügyeleti központ képernyőképe, amely a törlés megerősítési kérését jeleníti meg az ACL törléséhez." lightbox="./media/access-control-lists/delete-acl.png":::

1. Az **eszközök** alatt görgessen le a **hálózat** területére, és válassza a **hozzáférés-vezérlési listát**.
1. Válassza a **leltár** lapot, válasszon ki egy ACL-t a listából, majd válassza a **Törlés** lehetőséget.
1. A **Törlés megerősítő** kérdésnél válassza az **Igen** lehetőséget.
1. A keresőmező mellett válassza a **frissítés** lehetőséget az ACL törlésének biztosításához.

## <a name="next-steps"></a>Következő lépések
További információért lásd még:
- [Szoftveres hálózatkezelés (SDN) a Azure Stack HCI-ben és a Windows Serverben](../concepts/software-defined-networking.md)
