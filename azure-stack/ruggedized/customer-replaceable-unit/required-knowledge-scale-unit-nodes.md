---
title: Szükséges ismeretek a méretezési egység csomópontjainak működéséhez robusztus felhőalapú berendezésben
description: További információ a méretezési egység csomópontjainak robusztus felhőalapú berendezésben való használatához szükséges ismeretekről
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 5407a6097b4b7a602a70f9ce24928df4456b151f
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99571126"
---
# <a name="required-knowledge-for-working-with-scale-unit-nodes-in-a-ruggedized-cloud-appliance"></a>Szükséges ismeretek a méretezési egység csomópontjainak működéséhez robusztus felhőalapú berendezésben

A cserélhető eljárások elvégzéséhez ismernie kell, és el kell tudnia érni a következő fogalmakat, útmutatókat és webhelyeket.

## <a name="privileged-access-workstation-and-the-privileged-endpoint"></a>Emelt szintű hozzáférési munkaállomás és a privilegizált végpont

Az emelt szintű hozzáférési munkaállomás (PAW) egy dedikált munkaállomás, amely az Internet és a külső veszélyforrások vektorai által védett. A szolgáltatás a hardveres életciklus-gazdagépen (HLH) vagy kívülről is elhelyezhető az ügyfél hálózatán, és az Azure Stack hub skálázási egység csomópontjaira irányítható.

A PAW eléréséhez Távoli asztal használatával kell bejelentkeznie. Szerezze be a hitelesítő adatokat és az IP-címet az ügyféltől.

Erről a gépről a Kiemelt végpontot (PEP) is elérheti.
További információ: privilegizált hozzáférésű munkaállomás és privilegizált végpont-hozzáférés.

## <a name="azure-stack-hub-administrator-portal"></a>Azure Stack hub felügyeleti portál

Szerezze be a rendszergazdai portál hitelesítő adatait és URL-címét az ügyféltől.
További információ: Azure stack hub [felügyeleti portáljának használata](../../operator/azure-stack-manage-portals.md) 
 [](../../operator/azure-stack-manage-portals.md).

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>A Dell EMC PowerEdge R640 telepítése és szervizelési kézikönyve

A megfelelő hardverek fizikai cseréjével kapcsolatos további információkért tekintse meg a PowerEdge R640 rendszer-összetevő telepítése és eltávolítása a [Dell EMC PowerEdge R640 telepítésének és szervizelésének kézikönyvét](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Keresse meg a rendszerösszetevők [telepítése és törlése](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) 
 [](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) szakaszt.

## <a name="microsoft-azure-stack-hub-ruggedized-cloud-appliance-service-manual"></a>Microsoft Azure Stack hub robusztus Cloud Appliance Service-kézikönyv

Az Microsoft Azure Stack hub robusztus Cloud Appliance szolgáltatásának kézikönyve a Tracewell robusztus hüvelyből származó skálázási egység csomópontjainak eltávolítására vonatkozó utasításokat tartalmaz.

## <a name="dell-emc-poweredge-idrac"></a>Dell EMC PowerEdge iDRAC

Ismernie kell a Dell EMC PowerEdge iDRAC webes felületének navigálása és használata című témakört. A iDRAC használatával kapcsolatos további információkért tekintse meg a következő témakört: [Integrated Dell távelérési vezérlő 9 felhasználói \' útmutató](https://www.dell.com/support/manuals/us/en/04/poweredge-r840/idrac9_4.00.00.00_ug_new/overview-of-idrac?guid=guid-a03c2558-4f39-40c8-88b8-38835d0e9003).
