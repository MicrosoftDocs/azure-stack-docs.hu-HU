---
title: Az Azure Stack hub hálózatkezelésének bemutatása | Microsoft Docs
description: Tudnivalók a Azure Stack hub hálózatkezeléséről
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 0def34d0eb7209cdeb7d13e0e84529862a0ae71d
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75816037"
---
# <a name="introduction-to-azure-stack-hub-networking"></a>Az Azure Stack hub hálózatkezelésének bemutatása

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack Development Kit*

Azure Stack hub különböző típusú hálózati képességeket biztosít, amelyek együtt vagy külön is használhatók:

- **Azure Stack hub-erőforrások közötti kapcsolat**  
    Azure-erőforrások összekapcsolása egy biztonságos és privát virtuális hálózaton a felhőben.
- **Internetkapcsolat**  
    Kommunikáció Azure Stack hub-erőforrásokkal az interneten keresztül.
- **Helyszíni kapcsolatok**  
    Helyszíni hálózat csatlakoztatása Azure Stack hub-erőforrásokhoz virtuális magánhálózati (VPN) kapcsolaton keresztül az interneten keresztül, vagy az Azure Stack hub-hoz való dedikált kapcsolaton keresztül.
- **Terheléselosztás és forgalom iránya**  
    A forgalom terheléselosztása ugyanazon a helyen található kiszolgálókra, és a különböző helyekre irányuló kiszolgálók közötti közvetlen forgalom.
- **Biztonság**  
    A hálózati alhálózat vagy az egyes virtuális gépek közötti hálózati forgalom szűrése.
- **Útválasztás**  
    Használja az alapértelmezett útválasztást vagy az Útválasztás teljes vezérlését az Azure Stack hub és a helyszíni erőforrások között.
- **Kezelhetőség**  
    Felügyelheti és kezelheti Azure Stack hub hálózati erőforrásait.
- **Üzembe helyezési és konfigurációs eszközök**  
    A hálózati erőforrások üzembe helyezéséhez és konfigurálásához használjon webalapú portált vagy platformfüggetlen parancssori eszközöket.


## <a name="next-steps"></a>Következő lépések

* [A Azure Stack hub hálózatkezelésével kapcsolatos megfontolások](azure-stack-network-differences.md)