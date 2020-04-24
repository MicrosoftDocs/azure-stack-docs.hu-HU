---
title: Központi telepítési hálózati forgalom
titleSuffix: Azure Stack Hub
description: Ismerkedjen meg a hálózati forgalom folyamatával Azure Stack hub üzembe helyezése során.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 12/05/2019
ms.openlocfilehash: a663544b21fee671e57483e13030639c7b5edd3b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81243771"
---
# <a name="deployment-network-traffic"></a>Központi telepítési hálózati forgalom

A Azure Stack hub üzembe helyezése során felmerülő hálózati forgalom megismerése az üzembe helyezés sikerességét teszi lehetővé. Ez a cikk végigvezeti a hálózati forgalom folyamatán a telepítési folyamat során, hogy tudja, mire számíthat.

Ez az ábra a telepítési folyamat során felmerülő összes összetevőt és kapcsolatot mutatja:

![Azure Stack hub központi telepítési hálózati topológiája](media/deployment-networking/figure1.svg)

> [!NOTE]
> Ez a cikk a csatlakoztatott központi telepítésre vonatkozó követelményeket ismerteti. További információ az üzembe helyezési módszerekről: [Azure stack hub üzembe helyezési kapcsolatainak modelljei](azure-stack-connection-models.md).

## <a name="the-deployment-vm"></a>Az üzembe helyezési virtuális gép

Az Azure Stack hub-megoldás olyan kiszolgálók egy csoportját tartalmazza, amelyek a Azure Stack hub-összetevők és a hardveres életciklus-gazdagép (HLH) nevű további kiszolgáló üzemeltetésére szolgálnak. Ez a kiszolgáló a megoldás életciklusának üzembe helyezésére és kezelésére szolgál, és az üzembe helyezési virtuális gépet (DVM) üzemelteti a telepítés során.

Azure Stack hub-megoldás szolgáltatói további felügyeleti virtuális gépeket is kiépíthetnek. Erősítse meg a megoldás szolgáltatóját, mielőtt módosítja a felügyeleti virtuális gépeket egy megoldás-szolgáltatótól.

## <a name="deployment-requirements"></a>Üzembe helyezésre vonatkozó követelmények

Az üzembe helyezés megkezdése előtt bizonyos minimális követelmények érvényesek, amelyeket az OEM az üzembe helyezés sikeres végrehajtásának biztosítása érdekében érvényesít:

- [Tanúsítványok](azure-stack-pki-certs.md).
- [Azure-előfizetés](azure-stack-validate-registration.md). Előfordulhat, hogy ellenőriznie kell az előfizetését.
- Internet-hozzáférés.
- DNS.
- NTP.

> [!NOTE]
> Ez a cikk az utolsó három követelményre összpontosít. Az első kettőről a fenti hivatkozásokon talál további információt.

## <a name="about-deployment-network-traffic"></a>Az üzemi környezet hálózati forgalma

A DVM a BMC-hálózat IP-címével van konfigurálva, és hálózati hozzáférést igényel az internethez. Bár az összes BMC-hálózati összetevő nem igényel külső útválasztást vagy internet-hozzáférést, előfordulhat, hogy bizonyos, a hálózat IP-címeit használó OEM-összetevők is szükségesek.

Az üzembe helyezés során a DVM az előfizetéshez tartozó Azure-fiókkal hitelesíti a Azure Active Directory (Azure AD) szolgáltatását. Ennek érdekében a DVM internet-hozzáférésre van szüksége a [megadott portok és URL-címek](azure-stack-integrate-endpoints.md)listájához. A DVM egy DNS-kiszolgálót fog használni a belső összetevők által külső URL-címekre küldött DNS-kérések továbbítására. A belső DNS az üzembe helyezés előtt továbbítja ezeket a kéréseket a SZÁMÍTÓGÉPGYÁRTÓ által megadott DNS-továbbító címnek. Ugyanez igaz az NTP-kiszolgáló esetében is: megbízható időkiszolgáló szükséges az összes Azure Stack hub-összetevő konzisztencia-és időszinkronizálásának fenntartásához.

Az üzembe helyezés során a DVM által igényelt internet-hozzáférés csak kimenő, és az üzembe helyezés során nem történik bejövő hívás. Ne feledje, hogy az IP-címét forrásként használja, és Azure Stack hub nem támogatja a proxy konfigurációját. Ezért szükség esetén transzparens proxyt vagy NAT-t kell megadnia az Internet eléréséhez. Az üzembe helyezés során egyes belső összetevők a nyilvános VIP-címek használatával megkezdik az internet elérését a külső hálózaton keresztül. Az üzembe helyezést követően az Azure és az Azure Stack hub közötti összes kommunikáció a nyilvános virtuális IP-címek használatával történik a külső hálózaton.

Az Azure Stack hub kapcsolók hálózati konfigurációi olyan hozzáférés-vezérlési listákat (ACL-eket) tartalmaznak, amelyek korlátozzák a forgalmat bizonyos hálózati források és célhelyek között. A DVM az egyetlen olyan összetevő, amely korlátlan hozzáféréssel rendelkezik; még a HLH is korlátozott. Megkérheti az OEM-t a testreszabási lehetőségekkel, így egyszerűbbé teheti a felügyeletet és a hozzáférést a hálózatokból. Ezen ACL-ek miatt fontos elkerülni a DNS-és az NTP-kiszolgáló címének módosítását a központi telepítés ideje alatt. Ha így tesz, újra kell konfigurálnia a megoldás összes kapcsolóját.

Az üzembe helyezés befejezése után a rendszer a megadott DNS-és NTP-kiszolgálói címeket továbbra is a külső hálózat használatával fogja használni az SDN-n keresztül. Ha például a központi telepítés befejezése után megtekinti a DNS-kéréseket, a forrás a DVM IP-címről egy nyilvános VIP-re fog váltani.

## <a name="next-steps"></a>További lépések

[Azure-regisztráció ellenőrzése](azure-stack-validate-registration.md)
