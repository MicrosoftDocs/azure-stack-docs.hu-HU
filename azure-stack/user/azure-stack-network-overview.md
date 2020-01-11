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
ms.openlocfilehash: 0682a698dadf79feb2e22eaa683b723e7ae23717
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883354"
---
# <a name="introduction-to-azure-stack-hub-networking"></a>Az Azure Stack hub hálózatkezelésének bemutatása

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