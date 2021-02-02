---
title: Az Azure Stack hub hálózatkezelésének bemutatása
description: Tudnivalók a Azure Stack hub hálózatkezeléséről
author: mattbriggs
ms.topic: conceptual
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: ca8c93e983290e925d3af38bef0799d11c4a745a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247608"
---
# <a name="introduction-to-azure-stack-hub-networking"></a>Az Azure Stack hub hálózatkezelésének bemutatása

Azure Stack hub különböző típusú hálózati képességeket biztosít, amelyek együtt vagy külön is használhatók:

- **Azure Stack hub-erőforrások közötti kapcsolat**  
    Azure-erőforrások összekapcsolása egy biztonságos és privát virtuális hálózaton a felhőben.
- **Internetkapcsolat**  
    Kommunikáció Azure Stack hub-erőforrásokkal az interneten keresztül.
- **Helyszíni kapcsolatok**  
    Helyszíni hálózat csatlakoztatása Azure Stack hub-erőforrásokhoz virtuális magánhálózati (VPN) kapcsolaton keresztül az interneten keresztül, vagy az Azure Stack hub-hoz való dedikált kapcsolaton keresztül. 
    > [!IMPORTANT]
    > A helyszíni erőforrások eléréséhez létre kell hoznia egy VPN-vagy nyilvános IP-kapcsolatot.
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