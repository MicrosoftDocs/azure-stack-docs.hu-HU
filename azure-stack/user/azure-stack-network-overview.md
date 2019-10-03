---
title: Bevezetés a Azure Stack hálózatkezelésbe | Microsoft Docs
description: Tudnivalók a Azure Stack hálózatkezelésről
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: e47079b100487331434a201a05bbcae7645de4f1
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824164"
---
# <a name="introduction-to-azure-stack-networking"></a>Az Azure Stack-hálózat bemutatása

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack különböző típusú hálózati képességeket biztosít, amelyek együtt vagy külön is használhatók:

- **Azure Stack erőforrások közötti kapcsolat**  
    Azure-erőforrások összekapcsolása egy biztonságos és privát virtuális hálózaton a felhőben.
- **Internetkapcsolat**  
    Azure Stack-erőforrásokkal kommunikálhat az interneten keresztül.
- **Helyszíni kapcsolatok**  
    Helyszíni hálózat csatlakoztatása az erőforrások virtuális magánhálózaton (VPN) keresztül, vagy a Azure Stackhoz való dedikált kapcsolaton keresztüli Azure Stackához.
- **Terheléselosztás és forgalom iránya**  
    A forgalom terheléselosztása ugyanazon a helyen található kiszolgálókra, és a különböző helyekre irányuló kiszolgálók közötti közvetlen forgalom.
- **Biztonság**  
    A hálózati alhálózat vagy az egyes virtuális gépek közötti hálózati forgalom szűrése.
- **Útválasztás**  
    Használja az alapértelmezett útválasztást vagy az Útválasztás teljes vezérlését a Azure Stack és a helyszíni erőforrások között.
- **Kezelhetőség**  
    Azure Stack hálózati erőforrások monitorozása és kezelése.
- **Üzembe helyezési és konfigurációs eszközök**  
    A hálózati erőforrások üzembe helyezéséhez és konfigurálásához használjon webalapú portált vagy platformfüggetlen parancssori eszközöket.


## <a name="next-steps"></a>További lépések

* [Az Azure Stack-hálózatkezelés szempontjai](azure-stack-network-differences.md)