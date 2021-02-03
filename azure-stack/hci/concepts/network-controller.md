---
title: A hálózati vezérlő üzembe helyezésének megtervezése
description: Ez a témakör bemutatja, hogyan tervezheti meg a hálózati vezérlő üzembe helyezését a Windows felügyeleti központban a virtuális gépek (VM-EK) készletén.
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: bfea9216cefdc64c7749f8b49d5ecc3a422e5130
ms.sourcegitcommit: 0e58c5cefaa81541d9280c0e8a87034989358647
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99510788"
---
# <a name="plan-to-deploy-network-controller"></a>A hálózati vezérlő üzembe helyezésének megtervezése

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019; Windows Server 2016

A hálózati vezérlő Windows felügyeleti központon keresztül történő telepítésének megtervezéséhez a Azure Stack HCI vagy a Windows Server operációs rendszert futtató virtuális gépek (VM-EK) szükségesek. A hálózati vezérlő egy magas rendelkezésre állású és méretezhető kiszolgálói szerepkör, amelynek legalább három virtuális gépnek kell lennie ahhoz, hogy magas rendelkezésre állást biztosítson a hálózaton.

   >[!NOTE]
   > Javasoljuk, hogy a hálózati vezérlőt saját dedikált virtuális gépekre telepítse.

## <a name="network-controller-requirements"></a>Hálózati vezérlőre vonatkozó követelmények
A hálózati vezérlő telepítéséhez a következők szükségesek:
- Virtuális merevlemez (VHD) a Azure Stack HCI operációs rendszer számára a hálózati vezérlő virtuális gépek létrehozásához.
- A hálózati vezérlő virtuális gépek tartományhoz való csatlakoztatásához szükséges tartománynév és hitelesítő adatok.
- Legalább egy virtuális kapcsoló, amelyet a Windows felügyeleti központban, a fürt létrehozása varázslóval konfigurál.
- Olyan fizikai hálózati konfiguráció, amely megfelel a jelen szakasz egyik topológiai beállításának.

    A Windows felügyeleti központ létrehozza a konfigurációt a Hyper-V-gazdagépen belül. A felügyeleti hálózatnak azonban a következő három lehetőség egyike alapján kell csatlakoznia a gazdagép fizikai adapteréhez:

    **1. lehetőség**: a felügyeleti hálózat fizikailag el van különítve a munkaterhelés-hálózatokból. Ez a beállítás egyetlen virtuális kapcsolót használ a számítási és a tárolási kapacitáshoz:

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="1. lehetőség a hálózati vezérlő fizikai hálózatának létrehozásához." lightbox="./media/network-controller/topology-option-1.png":::

    **2. lehetőség**: a felügyeleti hálózat fizikailag el van különítve a munkaterhelés-hálózatokból. Ez a beállítás csak a számítási műveletek egyetlen virtuális kapcsolóját használja:

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="2. lehetőség a hálózati vezérlő fizikai hálózatának létrehozásához." lightbox="./media/network-controller/topology-option-2.png":::

    **3. lehetőség**: a felügyeleti hálózat fizikailag el van különítve a munkaterhelés-hálózatokból. Ez a beállítás két virtuális kapcsolót használ, egyet a számításhoz, egyet pedig a tároláshoz:

    :::image type="content" source="./media/network-controller/topology-option-3.png" alt-text="3. lehetőség a hálózati vezérlőhöz tartozó fizikai hálózat létrehozásához." lightbox="./media/network-controller/topology-option-3.png":::

- Azt is megteheti, hogy a felügyeleti fizikai adapterek ugyanazt a felügyeleti kapcsolót használják. Ebben az esetben a jelen szakasz egyik lehetőségének használatával továbbra is ajánlott használni.
- A hálózati vezérlő által a Windows felügyeleti központtal és a Hyper-V-gazdagépekkel való kommunikációhoz használt felügyeleti hálózati információk.
- DHCP-vagy statikus hálózati alapú címzés a hálózati vezérlő virtuális gépekhez.
- A reprezentációs állapot átadása (REST) teljes tartományneve (FQDN) a hálózati vezérlőhöz, amelyet a felügyeleti ügyfelek a hálózati vezérlővel való kommunikációra használnak.

   >[!NOTE]
   > A Windows felügyeleti központ jelenleg nem támogatja a hálózati vezérlő hitelesítését, vagy a REST-ügyfelekkel folytatott kommunikációhoz, vagy a hálózati vezérlő virtuális gépek közötti kommunikációhoz. Kerberos-alapú hitelesítést használhat, ha a PowerShell használatával telepíti és felügyeli azt.

## <a name="configuration-requirements"></a>Konfigurációs követelmények
A hálózati vezérlő fürtcsomópontok ugyanazon az alhálózaton vagy más alhálózatokon is üzembe helyezhetők. Ha a hálózati vezérlő fürtcsomópontok különböző alhálózatokon való üzembe helyezését tervezi, meg kell adnia a hálózati vezérlő REST DNS-nevét a telepítési folyamat során.

További információ: [dinamikus DNS-regisztráció konfigurálása a hálózati vezérlőhöz](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Következő lépések
Most már készen áll a hálózati vezérlő üzembe helyezésére a virtuális gépeken.

## <a name="see-also"></a>Lásd még
- [Azure Stack HCI-fürt létrehozása](../deploy/create-cluster.md)
- [SDN-infrastruktúra üzembe helyezése az SDN Express használatával](../manage/sdn-express.md)
- [Hálózati vezérlő – áttekintés](network-controller-overview.md)
- [Hálózati vezérlő magas rendelkezésre állása](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)