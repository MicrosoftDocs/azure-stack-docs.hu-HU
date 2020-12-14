---
title: A hardveres életciklus-gazdagép használatának szükséges ismerete
description: Ismerje meg a hardveres életciklus-gazdagép használatának szükséges ismereteit
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f4dc52cff6f04cdfdafedd845ccf40843687e7a3
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97391018"
---
# <a name="required-knowledge-for-working-with-the-hardware-lifecycle-host"></a>A hardveres életciklus-gazdagép használatának szükséges ismerete

A cserélhető eljárások elvégzéséhez ismernie kell a következő fogalmakat és útmutatókat, és képesnek kell lennie az elérésére.

## <a name="hardware-lifecycle-host"></a>Hardver életciklus-állomása

A hardver életciklus-állomása (HLH) egy fizikai felügyeleti kiszolgáló, amely az Azure Stack hub-állvány tetején található. A gazdagép eléréséhez a következő három módszer egyikével csatlakozhat:

* Közvetlen (Crash cart)
* iDRAC (szolgáltatási port)
* iDRAC (IP-hozzáférés)

Ha az adatközponton belül van, akkor közvetlenül kapcsolódhat a HLH a VGA-és USB-portok használatával. Például egy összeomlási kosár csatlakoztatása.

Ha az adatközponton belül van, csatlakoztassa a laptopot a iDRAC 9 szolgáltatási portjához egy Micro USB-kábel használatával. További információ: a iDRAC felület elérése közvetlen USB-kapcsolaton keresztül.

Működjön együtt az ügyféllel, hogy a felügyeleti hálózatról és a felügyeleti munkaállomásról a iDRAC IP-címhez csatlakozzanak a HLH.

> [!NOTE]
> Csak azok a hálózatok kapcsolódhatnak közvetlenül a HLH-iDRAC, amelyek korábban hozzá lettek adva a Switch ACL-hez.

## <a name="credentials"></a>Hitelesítő adatok

Az ügyféllel együttműködve szerezze be a hitelesítő adatokat a következőkhöz:

* HLH
* adminisztrátor
* iDRAC-fiók (nem kötelező)

Egy teljes körű rendszergazdai jogosultságokkal rendelkező Windows-fiók.

Ha nem kapcsolódik közvetlenül a kiszolgálóhoz egy összeomlási kosár használatával, akkor a iDRAC fiók hitelesítő adataira lesz szüksége, hogy hozzáférjen a virtuális KVM-hoz.

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>A Dell EMC PowerEdge R640 telepítése és szervizelési kézikönyve

A megfelelő hardverek fizikai cseréjével kapcsolatos részletekért tekintse meg a PowerEdge R640 cserélhető helyettesítő eljárásokat a [Dell](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) 
 [EMC PowerEdge R640 telepítés és szolgáltatás kézikönyvében](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Keresse meg a [rendszerösszetevők telepítése és törlése](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) szakaszt.

## <a name="microsoft-azure-stack-hub-tactical-cloud-appliance-service-manual"></a>Microsoft Azure Stack hub taktikai Cloud Appliance szolgáltatás manuális

Az Microsoft Azure Stack hub taktikai Cloud Appliance szolgáltatásának kézikönyve a Tracewell taktikai hüvelyből való eltávolítására vonatkozó utasításokat tartalmaz.

## <a name="dell-emc-poweredge-idrac"></a>Dell EMC PowerEdge iDRAC

Ismernie kell a Dell EMC PowerEdge iDRAC webes felületének navigálása és használata című témakört.

