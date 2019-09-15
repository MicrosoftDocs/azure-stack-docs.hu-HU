---
title: Nyilvános IP-címek hozzáadása a Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá nyilvános IP-címeket Azure Stackhoz.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: scottnap
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 090c532541ffe6ae6221958d395145c5215c3112
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974945"
---
# <a name="add-public-ip-addresses"></a>Nyilvános IP-címek hozzáadása
*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*  

Ebben a cikkben a külső címekre a nyilvános IP-címekre hivatkozunk. A Azure Stack kontextusában a nyilvános IP-cím olyan IP-cím, amely Azure Stackon kívülről érhető el. Azt határozza meg, hogy a külső hálózat nyilvános internetre irányítható-e, vagy intraneten van-e, és a saját címtartomány használata nem számít a jelen cikk céljaira – a lépések ugyanazok.

> [!IMPORTANT]
> A cikkben ismertetett lépések csak a partner Toolkit 1809-es vagy újabb verziójával telepített rendszerekre vonatkoznak. Az 1809-es verzió előtt üzembe helyezett rendszerek megkövetelik a (TOR) kapcsoló hozzáférés-vezérlési listái (ACL-ek) frissítését, hogy ENGEDÉLYEZZék az új nyilvános VIP-készlet tartományát. Ha régebbi kapcsoló-konfigurációkat futtat, akkor a SZÁMÍTÓGÉPGYÁRTÓval együttműködve adja hozzá a megfelelő engedélyezési ACL-eket az új nyilvános IP-készlethez, vagy konfigurálja újra a kapcsolót a legújabb partneri eszközkészlet használatával, hogy megakadályozza az új nyilvános IP-címek blokkolását.

## <a name="add-a-public-ip-address-pool"></a>Nyilvános IP-címkészlet hozzáadása
A Azure Stack rendszer kezdeti telepítése után bármikor hozzáadhat nyilvános IP-címeket a Azure Stack rendszerhez. Tekintse meg, hogyan [tekintheti meg a nyilvános IP-címek felhasználását](azure-stack-viewing-public-ip-address-consumption.md) , hogy megtudja, mi a jelenlegi használati és nyilvános IP-cím rendelkezésre állása a Azure stack.

Magas szinten az új nyilvános IP-címterület hozzáadásának folyamata Azure Stack így néz ki:

 ![IP-folyamat hozzáadása](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>A Címterület beszerzése a szolgáltatótól
Az első dolog, amit meg kell tennie, hogy beszerezze a Azure Stack hozzáadni kívánt címterület elérését. Attól függően, hogy honnan szerzi be a címtartományt, gondolja át, mi az az átvezetési idő, és kezelje ezt a sebességet, amellyel a Azure Stack nyilvános IP-címeit használja.

> [!IMPORTANT]
> Azure Stack fogadja el az Ön által megadott címterület-blokkot, ha az érvényes címterület, és nem fedi át a Azure Stack meglévő címtartomány-tartományát. Győződjön meg arról, hogy olyan érvényes címterület-blokkot szerez be, amely nem átfedésben van a külső hálózattal, amelyhez Azure Stack csatlakoztatva van. Miután hozzáadta a tartományt a Azure Stackhoz, nem távolíthatja el.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Adja hozzá az IP-címtartományt Azure Stack

1. A böngészőben nyissa meg a felügyeleti portál irányítópultját. Ebben a példában a következőt fogjuk használni https://adminportal.local.azurestack.external:.
2. Jelentkezzen be a Azure Stack felügyeleti portálra Felhőbeli operátorként.
3. Az alapértelmezett irányítópulton keresse meg a régió-felügyeleti listát, és válassza ki a kezelni kívánt régiót. Ebben a példában a helyit használjuk.
4. Keresse meg az erőforrás-szolgáltatók csempét, és kattintson a hálózati erőforrás-szolgáltatóra.
5. Kattintson a nyilvános IP-készletek használat csempére.
6. Kattintson az IP-készlet hozzáadása gombra.
7. Adja meg az IP-készlet nevét. A kiválasztott név megkönnyíti az IP-készlet azonosítását. Célszerű megtenni a nevet a címtartomány megadásával, de ez nem kötelező.
8. Adja meg a CIDR-jelöléssel felvenni kívánt címterület-blokkot. Példa: 192.168.203.0/24
9. Ha érvényes CIDR-tartományt ad meg a címtartomány (CIDR blokk) mezőben, a kezdő IP-cím, a záró IP-cím és az elérhető IP-címek mezők automatikusan feltöltve lesznek. Ezek csak olvashatók, és automatikusan létrejönnek, így nem módosíthatja ezeket a mezőket a címtartomány mező értékének módosítása nélkül.
10. Miután áttekinti az adatokat a panelen, és ellenőrizze, hogy minden helyesnek tűnik-e, kattintson az **OK** gombra a módosítás véglegesítéséhez, és adja hozzá a címtartományt a Azure Stackhoz.


## <a name="next-steps"></a>További lépések 
[Tekintse át a méretezési egység csomópontjának műveleteit](azure-stack-node-actions.md).
