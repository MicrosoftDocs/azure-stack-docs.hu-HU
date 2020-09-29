---
title: A hálózati vezérlő üzembe helyezésének megtervezése
description: Ez a témakör azt ismerteti, hogyan lehet a hálózati vezérlőt a Windows felügyeleti központon keresztül telepíteni a Azure Stack HCI operációs rendszert futtató virtuális gépeken (VM-EK).
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 785665c9edc3af3230b4813e6da6bceddc43bd0a
ms.sourcegitcommit: b147d617c32cea138b5bd4bab568109282e44317
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90010832"
---
# <a name="plan-to-deploy-the-network-controller"></a>A hálózati vezérlő üzembe helyezésének megtervezése

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019 

A hálózati vezérlő Windows felügyeleti központon keresztüli üzembe helyezésének megtervezéséhez a Azure Stack HCI operációs rendszert futtató virtuális gépeket (VM) kell futtatni. A hálózati vezérlő egy magas rendelkezésre állású és méretezhető kiszolgálói szerepkör, amelynek legalább három virtuális gépnek kell lennie, hogy magas rendelkezésre állást biztosítson a hálózaton.

   >[!NOTE]
   > Javasoljuk, hogy a hálózati vezérlőt saját dedikált virtuális gépén telepítse.

## <a name="network-controller-requirements"></a>Hálózati vezérlőre vonatkozó követelmények
A hálózati vezérlő telepítéséhez a következők szükségesek:
- Egy VHD a Azure Stack HCI operációs rendszer számára a hálózati vezérlő virtuális gépek létrehozásához.
- A hálózati vezérlő virtuális gépek tartományhoz való csatlakoztatásához szükséges tartománynév és hitelesítő adatok.
- Olyan fizikai hálózati konfiguráció, amely megfelel az ebben a szakaszban szereplő két topológiai lehetőség egyikének.

    A Windows felügyeleti központ létrehozza a konfigurációt a Hyper-V-gazdagépen belül. A felügyeleti hálózatnak azonban a következő két lehetőség egyike alapján kell csatlakoznia a gazdagép fizikai adapteréhez:

    **1. lehetőség**: egyetlen fizikai kapcsoló csatlakoztatja a felügyeleti hálózatot egy fizikai felügyeleti adapterhez a gazdagépen, és a virtuális kapcsoló által használt fizikai adapterek törzsét:

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="1. lehetőség a hálózati vezérlő fizikai hálózatának létrehozásához." lightbox="./media/network-controller/topology-option-1.png":::

    **2. lehetőség**: Ha a felügyeleti hálózat fizikailag el van különítve a munkaterhelés-hálózatokból, akkor két virtuális kapcsolóra van szükség:

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="2. lehetőség a hálózati vezérlő fizikai hálózatának létrehozásához." lightbox="./media/network-controller/topology-option-1.png":::

- A hálózati vezérlő által a Windows felügyeleti központtal és a Hyper-V-gazdagépekkel való kommunikációhoz használt felügyeleti hálózati információk.
- DHCP-vagy statikus hálózati alapú címzés a hálózati vezérlő virtuális gépei számára.
- A reprezentációs állapot átadása (REST) teljes tartományneve (FQDN) ahhoz a hálózati vezérlőhöz, amelyet a felügyeleti ügyfelek a hálózati vezérlővel való kommunikációra használnak.

   >[!NOTE]
   > A Windows felügyeleti központ jelenleg nem támogatja a hálózati vezérlő hitelesítését, vagy a REST-ügyfelekkel folytatott kommunikációhoz, vagy a hálózati vezérlő virtuális gépek közötti kommunikációhoz. Kerberos-alapú hitelesítést használhat, ha a PowerShell használatával telepíti és felügyeli azt.

## <a name="configuration-requirements"></a>Konfigurációs követelmények
A hálózati vezérlő fürtcsomópontok ugyanazon az alhálózaton vagy más alhálózatokon is üzembe helyezhetők. Ha a hálózati vezérlő fürtcsomópontok különböző alhálózatokon való üzembe helyezését tervezi, meg kell adnia a hálózati vezérlő REST DNS-nevét a telepítési folyamat során.

További információ: [dinamikus DNS-regisztráció konfigurálása a hálózati vezérlőhöz](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).


## <a name="next-steps"></a>Következő lépések
Most már készen áll a hálózati vezérlő üzembe helyezésére a Azure Stack HCI operációs rendszert futtató virtuális gépeken.

További tudnivalókért lásd:
- [Azure Stack HCI-fürt létrehozása](../deploy/create-cluster.md)

## <a name="see-also"></a>Lásd még
- [Hálózati vezérlő](/windows-server/networking/sdn/technologies/network-controller/network-controller)
- [Hálózati vezérlő magas rendelkezésre állása](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)