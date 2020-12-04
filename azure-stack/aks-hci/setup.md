---
title: Gyors útmutató az Azure Kubernetes szolgáltatás beállításához Azure Stack HCI-ben a Windows felügyeleti központ használatával
description: Ismerje meg, hogyan állíthatja be az Azure Kubernetes szolgáltatást a Azure Stack HCI-ben a Windows felügyeleti központon keresztül
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 155aa9948ceae350bed8ee8c7389b939e9099c18
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557402"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>Gyors útmutató: az Azure Kubernetes szolgáltatás beállítása Azure Stack HCI-ben a Windows felügyeleti központ használatával

> A következőkre vonatkozik: Azure Stack HCI, Windows Server 2019 Datacenter

Ebben a rövid útmutatóban az Azure Kubernetes szolgáltatást állítja be Azure Stack HCI-re a Windows felügyeleti központon keresztül. A PowerShell használata helyett lásd: [beállítás a PowerShell](setup-powershell.md)használatával.

A beállítás a következő feladatokat foglalja magában:

* Az Azure Kubernetes Service letöltése Azure Stack HCI-re
* Windows felügyeleti központ beállítása, ha még nem tette meg
* Az Azure Kubernetes Service bővítmény telepítése a Windows felügyeleti központhoz
* Állítson be egy Azure Kubernetes Service-gazdagépet azon a rendszeren, amelyen a Kubernetes-fürtöt telepíteni szeretné

Mielőtt elkezdené, győződjön meg arról, hogy teljesítette a [rendszerkövetelmények](.\system-requirements.md) lapon szereplő összes előfeltételt.

## <a name="download-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes Service letöltése Azure Stack HCI-re

Ha még nem töltötte le az előzetes verziójú szoftvert, tekintse [meg az AK kiértékelése Azure stack HCI](https://aka.ms/AKS-HCI-Evaluate)-ben című témakört. A rendszer kérni fogja az AK letöltését Azure Stack HCI-re és a Windows felügyeleti központra.

## <a name="setting-up-windows-admin-center"></a>A Windows felügyeleti központ beállítása

Ha még nem telepítette a Windows felügyeleti központot, tekintse meg a [Windows felügyeleti központ telepítése](/windows-server/manage/windows-admin-center/deploy/install)című témakört. A Azure Stack HCI-ben elérhető Azure Kubernetes Service nyilvános előzetes verziójához le kell töltenie és futtatnia kell a Windows felügyeleti központot egy Windows 10 rendszerű gépen. Az Azure Kubernetes Service on Azure Stack HCI funkció csak a Windows felügyeleti központ 2009-es vagy újabb verzióiban érhető el.

## <a name="installing-the-azure-kubernetes-service-extension"></a>Az Azure Kubernetes Service bővítmény telepítése

Miután megszerezte az Azure Kubernetes szolgáltatást Azure Stack HCI nyilvános előzetes verziójú fájlokon, a `.nupkg` fájlt helyileg vagy egy SMB-megosztáson kell mentenie, és fel kell vennie a fájl elérési útját a Windows felügyeleti központ bővítmény-kezelőjének "hírcsatornák" listájára. A `.nupkg` fájl egy NuGet-csomag, amely tartalmazza a Windows felügyeleti központ bővítményét.

A meglévő bővítmény-hírcsatorna eléréséhez nyissa meg a Windows felügyeleti központot, és válassza a képernyő jobb felső sarkában található fogaskerék lehetőséget. Ekkor megnyílik a beállítások menü. A bővítmények hírcsatornái a **bővítmények** menüjének **átjáró** szakasza alatt találhatók. Navigáljon a **hírcsatornák** lapra, és válassza a **Hozzáadás** lehetőséget. Ebben az ablaktáblában illessze be a fájl elérési útját az Azure Kubernetes szolgáltatás-bővítmény másolatához, és válassza a **Hozzáadás** lehetőséget. Ha a fájl elérési útja sikeresen hozzá lett adva, akkor sikeres értesítést kap. 

Most, hogy hozzáadta a hírcsatornát, az Azure Kubernetes Service bővítmény elérhető lesz az elérhető bővítmények listájában. Miután kiválasztotta a bővítményt, válassza a **telepítés** lehetőséget a tábla tetején a bővítmény telepítéséhez. A telepítés befejezése után a Windows felügyeleti központ újratöltődik. 

[![Az elérhető bővítmények listájának megtekintése a Windows felügyeleti központ bővítmény-kezelőjében. ](.\media\setup\extension-manager.png)](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>Azure Kubernetes Service Host beállítása

A Kubernetes-fürt létrehozása előtt egy utolsó lépést kell végrehajtania. Be kell állítania egy Azure Kubernetes Service-gazdagépet azon a rendszeren, amelyre telepíteni szeretné a Kubernetes-fürtöt. Ez a rendszer lehet Windows Server 2019 Datacenter-fürt, egyetlen csomópontos Windows Server 2019 Datacenter-fürt vagy egy 2-4-es csomópont Azure Stack HCI-fürthöz. 

> [!NOTE] 
> Az Azure Kubernetes Service-gazdagépek két független rendszeren való beállítása azzal a szándékkal, hogy a Kubernetes-fürt létrehozása során való egyesítésük nem támogatott. 

Ez a beállítás az új Azure Kubernetes szolgáltatás eszköz használatával végezhető el. 

Ez az eszköz telepíti és letölti a szükséges csomagokat, valamint létrehoz egy felügyeleti fürtöt, amely alapvető Kubernetes szolgáltatásokat biztosít, és összehangolja az alkalmazások számítási feladatait. 


Most, hogy ellenőrizte a rendszerbeállításokat, kezdjük a kezdéssel: 
1. A **Beállítás gombra kattintva** indítsa el a beállított varázslót.
2. Tekintse át az előfeltételeket a Windows felügyeleti központot futtató gépen, a fürthöz, amelyhez csatlakozik, valamint a hálózatot. Továbbá győződjön meg arról, hogy bejelentkezett egy Azure-fiókba a Windows felügyeleti központban, és hogy a használni kívánt Azure-előfizetés nem járt le. Ha elkészült, kattintson a **Tovább gombra**.
3. A varázsló **rendszerellenőrzések** lapján végezze el a szükséges műveleteket, például csatlakoztassa a Windows felügyeleti központ átjáróját az Azure-hoz. Ez a lépés ellenőrzi, hogy a Windows felügyeleti központ és az Azure Kubernetes Service-t futtató rendszer megfelelő konfigurációkkal rendelkezik-e a folytatáshoz. Ha végzett a művelettel, válassza a **tovább** lehetőséget.
4. Konfigurálja azt a gépet, amely az Azure Kubernetes szolgáltatást fogja tárolni a **gazdagép konfigurációs** lépéseként. Javasoljuk, hogy ebben a szakaszban válassza a **frissítések automatikus letöltése** lehetőséget. Ha elkészült, kattintson a **Next (tovább) gombra** . A varázsló ezen lépése a következő részletek konfigurálását kéri:
    * a gazdagép adatai, például a felügyeleti fürt neve és a virtuálisgép-lemezképek tárolására szolgáló mappa
    * Virtuálisgép-hálózatkezelés, amely a tárolók futtatásához és a tárolók kezelésének előkészítéséhez létrehozott összes Linux-és Windows-alapú virtuális GÉPRE (csomópontra) érvényes lesz. 
    * terheléselosztó beállításai, amelyek meghatározzák a külső szolgáltatásokhoz használt címek készletét.

    ![Az Azure Kubernetes Service Host varázsló gazdagép-konfiguráció lépéseit mutatja be.](.\media\setup\host-configuration.png)

5. Tekintse át az összes kijelölést a **felülvizsgálat + létrehozás** lépésben. Ha elégedett a kiválasztott beállításokkal, válassza a **beállítás** lehetőséget a gazdagép telepítésének megkezdéséhez. 
6. A **telepítési folyamat** lapon megtekintheti a gazdagép telepítőjének állapotát. Ekkor a Windows felügyeleti központot egy új lapon nyithatja meg, és folytathatja a felügyeleti feladatokat. 
7. Ha az üzembe helyezés sikeres, megjelenik egy felügyeleti irányítópult, ahol létrehozhatja és kezelheti a Kubernetes-fürtöket. Ez az irányítópult, mint a Azure Stack HCI-ben található többi Azure Kubernetes-szolgáltatás, előzetes verzióban érhető el, és a későbbi kiadásokban további funkciókkal frissül. 
 
  ![Bemutatja az Azure Kubernetes-szolgáltatásokat Azure Stack HCI felügyeleti irányítópulton.](.\media\setup\dashboard.png)
 
## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban telepítette a Windows felügyeleti központot és az Azure Kubernetes Service bővítményt. Az Azure Kubernetes Service hostt is konfigurálta azon a rendszeren, amelyen a Kubernetes-fürtöt telepíteni fogja.

Most már készen áll arra, hogy [Kubernetes-fürtöt hozzon létre a Windows felügyeleti központban](create-kubernetes-cluster.md).
