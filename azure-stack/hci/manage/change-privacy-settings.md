---
title: Adatvédelmi beállítások módosítása
description: Ez a témakör útmutatást nyújt az adatvédelmi beállítások módosításához a Azure Stack HCI operációs rendszerben vagy a Windows Serverben.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 10/05/2020
ms.openlocfilehash: 8055e02b702fb5e585b5570171c6dabb247b623f
ms.sourcegitcommit: 79be77eb2bebf314d956258fc9bc14b2014be190
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745144"
---
# <a name="change-privacy-settings-on-individual-servers"></a>Egyéni kiszolgálók adatvédelmi beállításainak módosítása

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019, Windows Server 2016

A következőképpen módosíthatja az adatvédelmi beállításokat a Azure Stack HCI operációs rendszert vagy a Windows Servert futtató kiszolgálókon. Ha az adatvédelmi beállításokat egyszerre több kiszolgálón Csoportházirend vagy a vállalat egészében szeretné kezelni, tekintse meg a [vállalati diagnosztikai adatok kezelése](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data)című témakört.

## <a name="azure-stack-hci-or-server-core"></a>Azure Stack HCI vagy Server Core
Ha a kiszolgáló a Azure Stack HCI operációs rendszert vagy a Windows Servert használja a Server Core telepítési lehetőséggel, kövesse az alábbi lépéseket:
1. Kapcsolódjon egy kiszolgálóhoz a Azure Stack HCI-fürtben Távoli asztal, egy távoli felügyeleti (fej-vagy BMC-) vezérlő használatával, KVM-vel, vagy a billentyűzettel és figyelővel helyileg történő bejelentkezéssel. 
1. Ha Azure Stack HCI-t futtató kiszolgálóhoz csatlakozik, a kiszolgáló-konfigurációs eszköz (Sconfig) automatikusan megnyílik. Ha Windows Servert futtató kiszolgálóhoz csatlakozik a Server Core használatával, írja be a parancssorba a következőt: `Sconfig` .
1. Az **adjon meg egy számot egy beállítás kiválasztásához:** parancssorba írja be a **10-es** értéket, majd nyomja le az ENTER billentyűt.
1. A **change telemetria** megerősítő üzenetben válassza az **Igen** lehetőséget a következő beállítások megjelenítéséhez:

    Elérhető telemetria-beállítások: **1 biztonság**, **2 alapszintű**, **3 bővített**, **4 teljes**

    >[!NOTE]
    > Azure Stack HCI alapértelmezett beállítása **1 biztonság**.

1. Az **adja meg az új telemetria beállítást:** parancssorba írja be a kívánt beállítást, majd nyomja le az ENTER billentyűt.

## <a name="full-desktop"></a>Teljes asztal
Ha a kiszolgáló Windows Server rendszert futtat, és a teljes asztali telepítés lehetőséggel rendelkezik, kövesse az alábbi lépéseket:
1. Kapcsolódjon a Windows Server Kiszolgálókezelő irányítópultján.

    A helyi hálózathoz billentyűzettel és figyelővel, illetve távfelügyeleti (fej-vagy BMC-) vezérlő használatával vagy Távoli asztal is csatlakozhat. 

1. A kiszolgáló kezelése az **irányítópulton**területen válassza a **helyi kiszolgáló**lehetőséget.
1. A kiszolgáló **Tulajdonságok** lapján, a **visszajelzés & diagnosztika**mellett válassza a **Beállítások**lehetőséget.

    A **beállítás** lapon a **visszajelzés gyakorisága** és a **diagnosztikai és használati adatok** beállításai láthatók. 
 
1. A **diagnosztikai és használati adatok** beállítás kibontásával válassza ki a következő lehetőségek egyikét:
    - **Szükséges diagnosztikai adatértékek**
    - **Továbbfejlesztett**
    - **Választható diagnosztikai adatértékek**

    >[!NOTE]
    > Ha a **Beállítások** lapon a **szervezet által felügyelt egyes beállítások** is megjelennek, akkor előfordulhat, hogy a **Diagnotic és a használati adatok** beállítás nem érhető el.

## <a name="next-steps"></a>További lépések
Ha az adatvédelmi beállításokat több kiszolgálón egyszerre vagy a vállalat egészében szeretné kezelni Csoportházirend, tekintse meg a következőt:
-   [Vállalati diagnosztikai adatszolgáltatások kezelése](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data)
