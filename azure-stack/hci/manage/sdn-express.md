---
title: SDN-infrastruktúra üzembe helyezése az SDN Express használatával
description: Az Sdn-infrastruktúra üzembe helyezésének megismerése az SDN Express használatával
author: v-dasis
ms.topic: how-to
ms.date: 02/01/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: b8431b7e2cf2cad3d238386619839a760b722042
ms.sourcegitcommit: d74f6d8630783de49667956f39cac67e1968a89d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99473191"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>SDN-infrastruktúra üzembe helyezése az SDN Express használatával

> A következőre vonatkozik Azure Stack HCI, Version 20H2

Ebben a témakörben egy teljes körű szoftveres hálózati (SDN) infrastruktúrát helyez üzembe az SDN Express PowerShell-parancsfájlok használatával. Az infrastruktúra tartalmazhat egy magas rendelkezésre állású (HA) hálózati vezérlőt (NC), egy magas rendelkezésre állású szoftvert Load Balancer (SLB) és egy magas rendelkezésre állású átjárót is.  A parancsfájlok támogatják a többfázisú telepítést, ahol csak a hálózati vezérlőt telepítheti a minimális hálózati követelményekkel rendelkező alapvető funkciók eléréséhez. 

Az SDN-infrastruktúrát System Center Virtual Machine Manager (VMM) használatával is üzembe helyezheti. További információ: [az Sdn-erőforrások kezelése a VMM-hálóban](/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Előkészületek

Az SDN üzembe helyezésének megkezdése előtt tervezze meg és konfigurálja a fizikai és a gazda hálózati infrastruktúrát. Hivatkozzon a következő cikkekre:

- [Fizikai hálózati követelmények](../concepts/physical-network-requirements.md)
- [Gazdagép hálózati követelményei](../concepts/host-network-requirements.md)
- [Fürt létrehozása a Windows felügyeleti központtal](../deploy/create-cluster.md)
- [Fürt létrehozása a Windows PowerShell használatával](../deploy/create-cluster-powershell.md)
- [Szoftveresen definiált hálózati infrastruktúra megtervezése](../concepts/plan-software-defined-networking-infrastructure.md)

Nem kell telepítenie az összes SDN-összetevőt. Tekintse meg a [szoftver által meghatározott hálózati infrastruktúra megtervezése](../concepts/plan-software-defined-networking-infrastructure.md) című szakaszos [üzembe helyezés](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment) szakaszt a szükséges infrastruktúra-összetevők meghatározásához, majd futtassa a parancsfájlokat ennek megfelelően.

Győződjön meg arról, hogy minden gazdagépen telepítve van a Azure Stack HCI operációs rendszer. Lásd: [a Azure stack HCI operációs rendszer üzembe helyezése](../deploy/operating-system.md) .

## <a name="requirements"></a>Követelmények

Sikeres SDN-telepítés esetén a következő követelményeknek kell teljesülniük:

- Minden gazdagép-kiszolgálónak engedélyezve kell lennie a Hyper-V-nek
- Minden gazdagép-kiszolgálónak csatlakoznia kell Active Directory
- Létre kell hozni egy virtuális kapcsolót
- A fizikai hálózatot konfigurálni kell a konfigurációs fájlban definiált alhálózatok és VLAN-ok számára.
- A konfigurációs fájlban megadott VHDX-fájlnak elérhetőnek kell lennie azon a központi telepítési számítógépen, amelyen az SDN Express parancsfájl fut

## <a name="create-the-vdx-file"></a>A VDX-fájl létrehozása

Az SDN egy VHDX-fájlt használ, amely a Azure Stack HCI operációs rendszert tartalmazza az SDN virtuális gépek (VM-EK) létrehozásának forrásaként. A VHDX operációs rendszerének verziószámának meg kell egyeznie a Hyper-V-gazdagépek által használt verzióval.

Ha letöltötte és telepítette az Azure Stack HCI OS-t egy ISO-ból, a segédprogrammal létrehozhatja ezt a VHDX a `Convert-WindowsImage` [script Centerben](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f)leírtak alapján.

Az alábbi példa egy példát mutat be a használatával `Convert-WindowsImage` :

 ```powershell
$wimpath = "d:\sources\install.wim"
$vhdpath = "c:\temp\WindowsServerDatacenter.vhdx"
$Edition = 4   # 4 = Full Desktop, 3 = Server Core

import-module ".\convert-windowsimage.ps1"

Convert-WindowsImage -SourcePath $wimpath -Edition $Edition -VHDPath $vhdpath -SizeBytes 500GB -DiskLayout UEFI
```

## <a name="download-the-github-repository"></a>A GitHub-adattár letöltése

Az SDN Express parancsfájl fájljai a GitHubban élnek. Az első lépés a szükséges fájlok és mappák beszerzése a telepítési számítógépre.

1. Nyissa meg az [Sdn Express](https://github.com/microsoft/SDN) GitHub-tárházat.

1. Töltse le a fájlokat a tárházból a kijelölt üzembe helyezési számítógépre. Válassza a **klón** vagy a **zip letöltése** lehetőséget.

    > [!NOTE]
    > A kijelölt központi telepítési számítógépnek Windows Server 2016 vagy újabb rendszert kell futtatnia.

1. Bontsa ki a ZIP-fájlt, és másolja a `SDNExpress` mappát az üzembe helyezési számítógép `C:\` mappájába.

## <a name="edit-the-configuration-file"></a>A konfigurációs fájl szerkesztése

A PowerShell `MultiNodeSampleConfig.psd1` konfigurációs adatfájlja tartalmazza a különböző paraméterek és konfigurációs beállítások bemenetként az Sdn Express-parancsfájlhoz szükséges paramétereket és beállításokat. Ez a fájl konkrét információkkal szolgál arról, hogy mit kell kitölteni az alapján, hogy csak a hálózati vezérlő összetevőt, vagy a szoftveres terheléselosztó és az átjáró összetevőit telepíti.

Részletes információkat a [szoftveres hálózati infrastruktúra megtervezése](../concepts/plan-software-defined-networking-infrastructure.md) című témakörben talál.

Kezdjük:

1. Lépjen a `C:\SDNExpress\scripts` mappába.

1. Nyissa meg a `MultiNodeSampleConfig.psd1` fájlt a kedvenc szövegszerkesztőben.

1. Módosítsa az adott paraméterérték értékét az infrastruktúra méretének megfelelően.

## <a name="run-the-deployment-script"></a>Az üzembe helyezési szkript futtatása

Az SDN Express-szkript üzembe helyezi az SDN-infrastruktúrát. A szkript befejezésekor az infrastruktúra készen áll a számítási feladatok központi telepítésére.

1. Tekintse át a `README.md` fájlt az üzembe helyezési parancsfájl futtatásával kapcsolatos további információkért.  

1. Futtassa a következő parancsot egy olyan felhasználói fiókból, amely rendszergazdai hitelesítő adatokkal rendelkezik a fürt kiszolgálóihoz:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. A hálózati vezérlő virtuális gépek létrehozása után konfigurálja a dinamikus DNS-frissítéseket a hálózati vezérlő fürtjének neveként a DNS-kiszolgálón. További információ: a [hálózati vezérlő üzembe helyezésére vonatkozó követelmények](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller)3. lépése.

## <a name="next-steps"></a>Következő lépések

A virtuális gépek kezelése. További információ: [virtuális gépek kezelése](../manage/vm.md)