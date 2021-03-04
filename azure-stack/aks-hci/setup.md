---
title: Gyors útmutató az Azure Kubernetes szolgáltatás beállításához Azure Stack HCI-ben a Windows felügyeleti központ használatával
description: Ismerje meg, hogyan állíthatja be az Azure Kubernetes szolgáltatást a Azure Stack HCI-ben a Windows felügyeleti központon keresztül
author: davannaw-msft
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: dawhite
ms.openlocfilehash: de54cf3f93462cfc63b6f7f2074343feacd25479
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840470"
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

Ha még nem töltötte le az előzetes verziójú szoftvert, tekintse [meg az AK kiértékelése Azure stack HCI](https://aka.ms/AKS-HCI-Evaluate)-ben című témakört. A rendszer a Azure Stack HCI és a Windows felügyeleti központ alkalmazásban is letölti az AK-t.

## <a name="setting-up-windows-admin-center"></a>A Windows felügyeleti központ beállítása

Ha még nem telepítette a Windows felügyeleti központot, tekintse meg a [Windows felügyeleti központ telepítése](/windows-server/manage/windows-admin-center/deploy/install)című témakört. Windows felügyeleti központot Windows 10-es gépen vagy kiszolgálón is futtathat. Az Azure Kubernetes Service on Azure Stack HCI funkció csak a Windows felügyeleti központ 2009-es vagy újabb verzióiban érhető el.

## <a name="installing-the-azure-kubernetes-service-extension"></a>Az Azure Kubernetes Service bővítmény telepítése

Miután megszerezte az Azure Kubernetes szolgáltatást Azure Stack HCI nyilvános előzetes verziójú fájlokon, a `.nupkg` fájlt helyileg vagy egy SMB-megosztáson kell mentenie, és fel kell vennie a fájl elérési útját a Windows felügyeleti központ bővítmény-kezelőjének "hírcsatornák" listájára. A `.nupkg` fájl egy NuGet-csomag, amely tartalmazza a Windows felügyeleti központ bővítményét.

A meglévő bővítmény-hírcsatorna eléréséhez nyissa meg a Windows felügyeleti központot, és válassza a képernyő jobb felső sarkában található fogaskerék lehetőséget. Ekkor megnyílik a beállítások menü. A bővítmények hírcsatornái a **bővítmények** menüjének **átjáró** szakasza alatt találhatók. Navigáljon a **hírcsatornák** lapra, és válassza a **Hozzáadás** lehetőséget. Ebben az ablaktáblában illessze be a fájl elérési útját az Azure Kubernetes szolgáltatás-bővítmény másolatához, és válassza a **Hozzáadás** lehetőséget. Ha a fájl elérési útja sikeresen hozzá lett adva, sikeres értesítést kap. 

Most, hogy hozzáadta a hírcsatornát, az Azure Kubernetes Service bővítmény elérhető lesz az elérhető bővítmények listájában. Miután kiválasztotta a bővítményt, válassza a **telepítés** lehetőséget a tábla tetején a bővítmény telepítéséhez. A telepítés befejezése után a Windows felügyeleti központ újratöltődik. 

[![Az elérhető bővítmények listájának megtekintése a Windows felügyeleti központ bővítmény-kezelőjében. ](.\media\setup\extension-manager.png)](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>Azure Kubernetes Service Host beállítása

A Kubernetes-fürt létrehozása előtt egy utolsó lépést kell végrehajtania. Be kell állítania egy Azure Kubernetes Service-gazdagépet azon a rendszeren, amelyre telepíteni szeretné a Kubernetes-fürtöt. Ezt a platform-szolgáltatások vagy a felügyeleti fürt beállításának is nevezzük.  

[![Egy architektúra-diagram képe, amely kiemeli a platform szolgáltatásainak részét.](.\media\setup\aks-hci-architecture-focused.png)](.\media\setup\aks-hci-architecture-focused.png) 

Ez a rendszer lehet Windows Server 2019 Datacenter-fürt, egyetlen csomópont Windows Server 2019 Datacenter vagy 2-4 csomópont Azure Stack HCI-fürthöz. 

> [!NOTE] 
> Az Azure Kubernetes Service-gazdagépek két független rendszeren való beállítása azzal a szándékkal, hogy a Kubernetes-fürt létrehozása során való egyesítésük nem támogatott. 

Ez a beállítás az új Azure Kubernetes szolgáltatás eszköz használatával végezhető el. 

Ez az eszköz telepíti és letölti a szükséges csomagokat, valamint létrehoz egy AK-beli gazdagép-fürtöt, amely alapvető Kubernetes szolgáltatásokat biztosít, és összehangolja az alkalmazások munkaterheléseit. 

Most, hogy ellenőrizte a rendszerbeállításokat, kezdjük a kezdéssel: 
1. A **Beállítás gombra kattintva** indítsa el a beállított varázslót.
2. Tekintse át az előfeltételeket a Windows felügyeleti központot futtató gépen, a fürthöz, amelyhez csatlakozik, valamint a hálózatot. Továbbá győződjön meg arról, hogy bejelentkezett egy Azure-fiókba a Windows felügyeleti központban, és hogy a használni kívánt Azure-előfizetés nem járt le. Ha elkészült, kattintson a **Tovább gombra**.

> [!WARNING]
> Ha 2-4 csomópontot használ Azure Stack HCI-fürtöt, győződjön meg arról, hogy legalább egy külső virtuális kapcsolót konfigurált, mielőtt folytatná a lépést, vagy nem fogja tudni sikeresen beállítani az Azure Kubernetes Service hostt.

3. A varázsló **rendszerellenőrzések** lapján végezze el a szükséges műveleteket, például [csatlakoztassa a Windows felügyeleti központ átjáróját az Azure-hoz](/windows-server/manage/windows-admin-center/azure/azure-integration). Ez a lépés ellenőrzi, hogy a Windows felügyeleti központ és az Azure Kubernetes Service-t futtató rendszer megfelelő konfigurációkkal rendelkezik-e a folytatáshoz. Ha végzett a művelettel, válassza a **tovább** lehetőséget.
4. Konfigurálja azt a gépet, amely az Azure Kubernetes szolgáltatást fogja tárolni a **gazdagép konfigurációs** lépéseként. Javasoljuk, hogy ebben a szakaszban válassza a **frissítések automatikus letöltése** lehetőséget. A varázsló ezen lépése a következő részletek konfigurálását kéri:
    * A **gazdagép adatait**, például az AK-gazdagép fürtjének nevét és a virtuálisgép-lemezképeket tároló lemezkép könyvtárat. A képkönyvtárnak egy megosztott tárolási útvonalra vagy egy, a gazdagép által elérhető SMB-megosztásra kell mutatnia.
    * Virtuálisgép-hálózatkezelés, amely a tárolók futtatásához és a tárolók kezelésének előkészítéséhez létrehozott összes Linux-és Windows-alapú virtuális **gépre**(csomópontra) érvényes lesz. Ebbe beletartozik az internetkapcsolattal rendelkező virtuális kapcsoló mezői, a virtuális helyi hálózatok azonosításának engedélyezése, az IP-cím kiosztási módszere és a Cloudagent IP-címe. A Cloudagent IP-cím használatával statikus IP-címet biztosíthat a CloudAgent szolgáltatásnak. Ez az IP-címek kiosztása beállítástól függetlenül érvényes. Ha a statikus IP-cím kiosztási módszert választotta, néhány további mezőt is meg kell adni:
      - **Alhálózati előtag**, egy IP-címtartomány, amely nem ütközik más címekkel
      - **Átjáró**, az átjáró, amelyen keresztül a csomagok a gépen kívülre lesznek irányítva
      - **DNS-kiszolgálók**, DNS-kiszolgálók IP-címei a hálózaton
      - A **Kubernetes csomópont IP-készletének indítása**, a Kubernetes-fürtök által használt IP-címek készletének kezdő tartománya
      - **Kubernetes Node IP-címkészlet vége**, a Kubernetes-fürtök által használt IP-címek készletének záró tartománya
    * A **terheléselosztó beállításai**, amelyek meghatározzák a külső szolgáltatásokhoz használt címek készletét. Ha a virtuálisgép-hálózatkezelés szakaszban a statikus konfigurációt választotta, a címkészlet kezdetének és befejezésének az adott szakaszban megadott alhálózati tartományon belül kell lennie. 

    ![Az Azure Kubernetes Service Host varázsló gazdagép-konfiguráció lépéseit mutatja be.](.\media\setup\host-configuration.png)
    
    Ha elkészült, kattintson a **Next (tovább) gombra** .

5. A varázsló **Azure-regisztráció** lapján adja meg a szolgáltatáshoz használni kívánt előfizetést és erőforráscsoportot. Habár az Azure Kubernetes szolgáltatás előzetes verzióban érhető el, nem számítunk fel díjat. Ha elkészült, kattintson a **Tovább gombra**.
6. Tekintse át az összes kijelölést a **felülvizsgálat + létrehozás** lépésben. Ha elégedett a kiválasztott beállításokkal, válassza a **tovább** lehetőséget a gazdagép telepítésének megkezdéséhez. 
7. A **telepítési folyamat** lapon megtekintheti a gazdagép telepítőjének állapotát. Ekkor a Windows felügyeleti központot egy új lapon nyithatja meg, és folytathatja a felügyeleti feladatokat. 
8. Ha az üzembe helyezés sikeres, válassza a *Befejezés* lehetőséget, és egy felügyeleti irányítópulttal fog megjelenni, ahol létrehozhatja és kezelheti a Kubernetes-fürtöket. Ez az irányítópult, mint a Azure Stack HCI-ben található többi Azure Kubernetes-szolgáltatás, előzetes verzióban érhető el, és a későbbi kiadásokban további funkciókkal frissül.
 
  ![Bemutatja az Azure Kubernetes-szolgáltatásokat Azure Stack HCI felügyeleti irányítópulton.](.\media\setup\dashboard.png)
 
## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban telepítette a Windows felügyeleti központot és az Azure Kubernetes Service bővítményt. Az Azure Kubernetes Service hostt is konfigurálta azon a rendszeren, amelyen a Kubernetes-fürtöt telepíteni fogja.

Most már készen áll arra, hogy [Kubernetes-fürtöt hozzon létre a Windows felügyeleti központban](create-kubernetes-cluster.md).
