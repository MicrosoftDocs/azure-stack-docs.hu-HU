---
title: Nyilvános IP-címek hozzáadása az Azure Stack központban
description: Megtudhatja, hogyan adhat hozzá nyilvános IP-címeket Azure Stack hubhoz.
author: justinha
ms.topic: article
ms.date: 04/20/2020
ms.author: justinha
ms.reviewer: scottnap
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 8cd89a90cb29c802c79e900e07cdb50bfe9c0894
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847911"
---
# <a name="add-public-ip-addresses"></a>Nyilvános IP-címek hozzáadása

Ebben a cikkben a külső címekre a nyilvános IP-címekre hivatkozunk. Azure Stack hub kontextusában a nyilvános IP-cím olyan IP-cím, amely a Azure Stack hub-on kívülről érhető el. Azt határozza meg, hogy a külső hálózat nyilvános internetre irányítható-e, vagy intraneten van-e, és a saját címtartomány használata nem számít a jelen cikk céljaira – a lépések ugyanazok.

> [!IMPORTANT]
> A cikkben ismertetett lépések csak a partner Toolkit 1809-es vagy újabb verziójával telepített rendszerekre vonatkoznak. Az 1809-es verzió előtt üzembe helyezett rendszerek megkövetelik a (TOR) kapcsoló hozzáférés-vezérlési listái (ACL-ek) frissítését, hogy ENGEDÉLYEZZék az új nyilvános VIP-készlet tartományát. Ha régebbi kapcsoló-konfigurációkat futtat, akkor a SZÁMÍTÓGÉPGYÁRTÓval együttműködve adja hozzá a megfelelő engedélyezési ACL-eket az új nyilvános IP-készlethez, vagy konfigurálja újra a kapcsolót a legújabb partneri eszközkészlet használatával, hogy megakadályozza az új nyilvános IP-címek blokkolását.

## <a name="add-a-public-ip-address-pool"></a>Nyilvános IP-címkészlet hozzáadása
A Azure Stack hub rendszer kezdeti telepítése után bármikor hozzáadhat nyilvános IP-címeket a Azure Stack hub rendszerhez. Tekintse meg, hogyan [tekintheti meg a nyilvános IP-címek](azure-stack-viewing-public-ip-address-consumption.md) használatát, hogy megtudja, mi a jelenlegi használat és a nyilvános IP-cím rendelkezésre állása a Azure stack hub-on.

Magas szinten az új nyilvános IP-címterület hozzáadásának folyamata Azure Stack hubhoz így néz ki:

 ![IP-folyamat hozzáadása](media/azure-stack-add-ips/flow.svg)

## <a name="obtain-the-address-block-from-your-provider"></a>Szerezze be a címblokkot a szolgáltatójától
Az első dolog, amit meg kell tennie, hogy megszerezze a Azure Stack hub-hoz hozzáadni kívánt címterület beszerzését. Attól függően, hogy honnan szerzi be a címtartományt, gondolja át, milyen az átvezetési idő, és hogyan kezelheti ezt a Azure Stack hub nyilvános IP-címeinek felhasználására használt sebességgel.

> [!IMPORTANT]
> Az Azure Stack hub fogadja az Ön által megadott összes címtartományt, ha az érvényes címterület, és nem fedi át az Azure Stack hub egy meglévő címtartomány-tartományát. Győződjön meg arról, hogy olyan érvényes címterület-blokkot szerez be, amely nem átfedésben van a külső hálózattal, amelyhez Azure Stack hub csatlakozik. Miután hozzáadta a tartományt Azure Stack hubhoz, nem távolíthatja el.

## <a name="add-the-ip-address-range-to-azure-stack-hub"></a>IP-címtartomány hozzáadása Azure Stack hubhoz

1. A böngészőben nyissa meg a felügyeleti portál irányítópultját. Ebben a példában a következőt fogjuk használni `https://adminportal.local.azurestack.external`:.
2. Jelentkezzen be az Azure Stack hub felügyeleti portálján Felhőbeli operátorként.
3. Az alapértelmezett irányítópulton keresse meg a régió-felügyeleti listát, és válassza ki a kezelni kívánt régiót. Ebben a példában a helyit használjuk.
4. Keresse meg az erőforrás-szolgáltatók csempét, és kattintson a hálózati erőforrás-szolgáltatóra.
5. Kattintson a nyilvános IP-készletek használat csempére.
6. Kattintson az IP-készlet hozzáadása gombra.
7. Adja meg az IP-készlet nevét. A kiválasztott név megkönnyíti az IP-készlet azonosítását. Célszerű megtenni a nevet a címtartomány megadásával, de ez nem kötelező.
8. Adja meg a CIDR-jelöléssel felvenni kívánt címterület-blokkot. Például: 192.168.203.0/24
9. Ha érvényes CIDR-tartományt ad meg a címtartomány (CIDR blokk) mezőben, a kezdő IP-cím, a záró IP-cím és az elérhető IP-címek mezők automatikusan feltöltve lesznek. Ezek csak olvashatók, és automatikusan létrejönnek, így nem módosíthatja ezeket a mezőket a címtartomány mező értékének módosítása nélkül.
10. Miután áttekinti az adatokat a panelen, és ellenőrizze, hogy minden helyesnek tűnik-e, kattintson az **OK** gombra a módosítás véglegesítéséhez és a címtartomány Azure stack hubhoz való hozzáadásához.


## <a name="next-steps"></a>További lépések 
[Tekintse át a méretezési egység csomópontjának műveleteit](azure-stack-node-actions.md).
