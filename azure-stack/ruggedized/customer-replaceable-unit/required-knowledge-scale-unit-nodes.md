---
title: Szükséges ismeretek a méretezési egység csomópontjainak működéséhez egy taktikai felhőalapú berendezésben
description: További információ a méretezési egység csomópontjainak a taktikai felhőalapú berendezésben való használatához szükséges ismeretekről
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 4df7cfa84ff9b4a08d6751aa19ffdb1430a80605
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256132"
---
# <a name="required-knowledge-for-working-with-scale-unit-nodes-in-a-tactical-cloud-appliance"></a>Szükséges ismeretek a méretezési egység csomópontjainak működéséhez egy taktikai felhőalapú berendezésben

A cserélhető eljárások elvégzéséhez ismernie kell, és el kell tudnia érni a következő fogalmakat, útmutatókat és webhelyeket.

## <a name="privileged-access-workstation-and-the-privileged-endpoint"></a>Emelt szintű hozzáférési munkaállomás és a privilegizált végpont

Az emelt szintű hozzáférési munkaállomás (PAW) egy dedikált munkaállomás, amely az Internet és a külső veszélyforrások vektorai által védett. A szolgáltatás a hardveres életciklus-gazdagépen (HLH) vagy kívülről is elhelyezhető az ügyfél hálózatán, és az Azure Stack hub skálázási egység csomópontjaira irányítható.

A PAW eléréséhez Távoli asztal használatával kell bejelentkeznie. Szerezze be a hitelesítő adatokat és az IP-címet az ügyféltől.

Erről a gépről a Kiemelt végpontot (PEP) is elérheti.
További információ a privilegizált hozzáférésű munkaállomásról és a PEP-ról: privilegizált hozzáférésű munkaállomás és privilegizált végpont-hozzáférés.

## <a name="azure-stack-hub-administrator-portal"></a>Azure Stack hub felügyeleti portál

Szerezze be a rendszergazdai portál hitelesítő adatait és URL-címét az ügyféltől.
További információ: Azure stack [hub felügyeleti portáljának használata](../../operator/azure-stack-manage-portals.md) 
 [](../../operator/azure-stack-manage-portals.md).

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>A Dell EMC PowerEdge R640 telepítése és szervizelési kézikönyve

A megfelelő hardverek fizikai cseréjével kapcsolatos részletekért tekintse meg a PowerEdge R640 rendszer-összetevő telepítésével és eltávolításával foglalkozó témakört a [Dell EMC PowerEdge R640 telepítési és szervizelési kézikönyvében](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Keresse meg a rendszerösszetevők [telepítése és törlése](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) 
 [](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) szakaszt.

## <a name="microsoft-azure-stack-hub-tactical-cloud-appliance-service-manual"></a>Microsoft Azure Stack hub taktikai Cloud Appliance szolgáltatás manuális

Az Microsoft Azure Stack hub taktikai Cloud Appliance szolgáltatásának kézikönyve a Tracewell taktikai hüvelyből származó skálázási egység csomópontjainak eltávolítására vonatkozó utasításokat tartalmaz.

## <a name="dell-emc-poweredge-idrac"></a>Dell EMC PowerEdge iDRAC

Ismernie kell a Dell EMC PowerEdge iDRAC webes felületének navigálása és használata című témakört. A iDRAC használatával kapcsolatos további információkért tekintse meg az [integrált Dell távelérési vezérlő 9 felhasználói \' útmutatóját](https://www.dell.com/support/manuals/us/en/04/poweredge-r840/idrac9_4.00.00.00_ug_new/overview-of-idrac?guid=guid-a03c2558-4f39-40c8-88b8-38835d0e9003).