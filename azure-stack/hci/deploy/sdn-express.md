---
title: SDN-infrastruktúra üzembe helyezése az SDN Express használatával
description: Az Sdn-infrastruktúra üzembe helyezésének megismerése az SDN Express használatával
author: v-dasis
ms.topic: how-to
ms.date: 12/16/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 968782c4fd5ac0f4e057815c526fbde8f90164af
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254840"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>SDN-infrastruktúra üzembe helyezése az SDN Express használatával

> A következőre vonatkozik Azure Stack HCI, Version 20H2

Ebben a témakörben egy teljes körű szoftveres hálózati (SDN) infrastruktúrát helyez üzembe az SDN Express PowerShell-parancsfájlok használatával. Az infrastruktúra tartalmaz egy magas rendelkezésre állású (HA) hálózati vezérlőt, egy magas rendelkezésre állású szoftvert Load Balancer (SLB) és egy magas rendelkezésre állású átjárót.  

A parancsfájlok támogatják a többfázisú telepítést, ahol csak a hálózati vezérlőt telepítheti a minimális hálózati követelményekkel rendelkező alapvető funkciók eléréséhez. A hálózati vezérlőt a fürt létrehozása varázslóval is üzembe helyezheti a Windows felügyeleti központban. Más SDN-összetevők, például a SLB és az átjáró üzembe helyezéséhez azonban az SDN Express szkripteket kell használnia.

Az SDN-infrastruktúrát System Center Virtual Machine Manager (VMM) használatával is üzembe helyezheti. További információ: [az Sdn-erőforrások kezelése a VMM-hálóban](/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Előkészületek

Az SDN üzembe helyezésének megkezdése előtt tervezze meg és konfigurálja a fizikai és a gazda hálózati infrastruktúrát. Hivatkozzon a következő cikkekre:

- [Fizikai hálózati követelmények](../concepts/physical-network-requirements.md)
- [Gazdagép hálózati követelményei](../concepts/host-network-requirements.md)
- [Fürt létrehozása a Windows felügyeleti központtal](create-cluster.md)
- [Fürt létrehozása a Windows PowerShell használatával](create-cluster-powershell.md)
- [Szoftveresen definiált hálózati infrastruktúra megtervezése](../concepts/plan-software-defined-networking-infrastructure.md)

Nem kell telepítenie az összes SDN-összetevőt. Tekintse meg a [szoftver által meghatározott hálózati infrastruktúra megtervezése](../concepts/plan-software-defined-networking-infrastructure.md) című szakaszos [üzembe helyezés](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment) szakaszt a szükséges infrastruktúra-összetevők meghatározásához, majd futtassa a parancsfájlokat ennek megfelelően.

Győződjön meg arról, hogy minden gazdagépen telepítve van a Azure Stack HCI operációs rendszer. Lásd: [a Azure stack HCI operációs rendszer üzembe helyezése](operating-system.md) .

## <a name="run-the-sdn-express-scripts"></a>Az SDN Express parancsfájljainak futtatása

1. Nyissa meg az [Sdn Express](https://github.com/microsoft/SDN) GitHub-tárházat.

1. Töltse le a fájlokat a tárházból a kijelölt üzembe helyezési számítógépre. Válassza a **klón** vagy a **zip letöltése** lehetőséget.

    > [!NOTE]
    > A kijelölt központi telepítési számítógépnek Windows Server 2016 vagy újabb rendszert kell futtatnia.

1. Bontsa ki a ZIP-fájlt, és másolja a `SDNExpress` mappát az üzembe helyezési számítógép `C:\` mappájába.

1. Lépjen a `C:\SDNExpress\scripts` mappába.

1. Futtassa a `SDNExpress.ps1` parancsfájlt. Ez a szkript egy PowerShell-telepítési (PSD) fájlt használ bemenetként a különböző konfigurációs beállításokhoz. A `README.md` szkript futtatásával kapcsolatos útmutatásért tekintse meg a fájlt.  

1. Használjon olyan VHDX, amely a Azure Stack HCI operációs rendszert tartalmazza az SDN virtuális gépek létrehozásának forrásaként. Ha letöltötte és telepítette az Azure Stack HCI operációs rendszert egy ISO-ból, akkor a segédprogrammal létrehozhatja ezt a VHDX a `convert-windowsimage` dokumentációban leírtak alapján.

1. Szabja testre a `SDNExpress\scripts\MultiNodeSampleConfig.psd1` fájlt úgy, hogy az adott értékeket úgy módosítja, hogy azok megfeleljenek az infrastruktúrának, beleértve a gazdagépek nevét, a tartományneveket, a felhasználóneveket és a jelszavakat, valamint a megadott hálózatokra vonatkozó hálózati információkat a [szoftveres hálózati infrastruktúra megtervezése](../concepts/plan-software-defined-networking-infrastructure.md) című témakörben Ez a fájl konkrét információkkal szolgál arról, hogy mit kell kitölteni az alapján, hogy csak a hálózati vezérlő összetevőt, vagy a szoftveres terheléselosztó és az átjáró összetevőit telepíti.

1. Futtassa a következő parancsfájlt egy rendszergazdai hitelesítő adatokkal rendelkező felhasználói fiókból a Hyper-V gazdagépeken:

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. A hálózati vezérlő virtuális gépek létrehozása után konfigurálja a dinamikus DNS-frissítéseket a hálózati vezérlő fürtjének neveként a DNS-kiszolgálón. További információ: a [hálózati vezérlő üzembe helyezésére vonatkozó követelmények](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller)3. lépése.

## <a name="next-steps"></a>További lépések

A virtuális gépek kezelése. További információ: [virtuális gépek kezelése](../manage/vm.md)