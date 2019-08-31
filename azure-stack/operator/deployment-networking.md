---
title: Azure Stack központi telepítési hálózati forgalom | Microsoft Docs
description: Ez a cikk azt ismerteti, mire számíthat Azure Stack központi telepítési hálózati folyamatokkal kapcsolatban.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: a0829f2bc8cb45bdfd6f68ac15418a05adcc7afb
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188337"
---
# <a name="about-deployment-network-traffic"></a>Tudnivalók a központi telepítési hálózati forgalomról
Azure Stack központi telepítés során a hálózati forgalom megismerése segíti a központi telepítés sikerességét. Ez a cikk végigvezeti a hálózati forgalom folyamatán a telepítési folyamat során, hogy tudja, mire számíthat.

Ez az ábra a telepítési folyamat során felmerülő összes összetevőt és kapcsolatot mutatja:

![Azure Stack központi telepítési hálózati topológiája](media/deployment-networking/figure1.png)

> [!NOTE]
> Ez a cikk a csatlakoztatott központi telepítésre vonatkozó követelményeket ismerteti, ha további információt szeretne az egyéb telepítési módszerekről, lásd: [Azure stack telepítési kapcsolati modellek](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Az üzembe helyezési virtuális gép
A Azure Stack megoldás olyan kiszolgálók egy csoportját tartalmazza, amelyek a Azure Stack-összetevők és a hardveres életciklus-gazdagép (HLH) nevű további kiszolgáló üzemeltetésére szolgálnak. Ez a kiszolgáló a megoldás életciklusának üzembe helyezésére és kezelésére szolgál, és az üzembe helyezési virtuális gépet (DVM) üzemelteti a telepítés során.

Azure Stack a megoldás-szolgáltatók további felügyeleti virtuális gépeket is kiépíthetnek. Erősítse meg a megoldás szolgáltatóját, mielőtt módosítja a felügyeleti virtuális gépeket egy megoldás-szolgáltatótól.

## <a name="deployment-requirements"></a>Üzembe helyezési követelmények
Az üzembe helyezés megkezdése előtt bizonyos minimális követelmények érvényesek, amelyeket az OEM az üzembe helyezés sikeres végrehajtásának biztosítása érdekében érvényesít:

-   [Tanúsítványok](azure-stack-pki-certs.md)
-   [Azure-előfizetés](https://azure.microsoft.com/free/?b=17.06)
-   Internetelérés
-   DNS
-   NTP

> [!NOTE]
> Ez a cikk az utolsó három követelményre összpontosít. Az első kettőről a fenti hivatkozásokon talál további információt.

## <a name="deployment-network-traffic"></a>Központi telepítési hálózati forgalom
A DVM a BMC-hálózat IP-címével van konfigurálva, és hálózati hozzáférést igényel az internethez. Bár a BMC-hálózati összetevők egyike sem igényel külső útválasztást vagy internet-hozzáférést, bizonyos OEM-specifikus összetevők, amelyek IP-címeket használnak a hálózatról, az is szükséges lehet.

Az üzembe helyezés során a DVM az előfizetéshez tartozó Azure-fiókkal hitelesíti a Azure Active Directory (Azure AD) szolgáltatását. Ennek érdekében a DVM internet-hozzáférésre van szüksége a [megadott portok és URL-címek](azure-stack-integrate-endpoints.md)listájához. A DVM egy DNS-kiszolgálót fog használni a belső összetevők által külső URL-címekre küldött DNS-kérések továbbítására. A belső DNS továbbítja ezeket a kéréseket a SZÁMÍTÓGÉPGYÁRTÓ által az üzembe helyezés előtt megadott DNS-továbbító címnek. Ugyanez az NTP-kiszolgáló esetében is igaz, megbízható időkiszolgáló szükséges az összes Azure Stack-összetevő konzisztencia-és időszinkronizálásának fenntartásához.

Az üzembe helyezés során a DVM által igényelt internet-hozzáférés csak kimenő, és az üzembe helyezés során nem történik bejövő hívás. Ne feledje, hogy az IP-címét forrásként használja, és a Azure Stack nem támogatja a proxy konfigurációját. Ezért szükség esetén transzparens proxyt vagy NAT-t kell megadnia az Internet eléréséhez. Az üzembe helyezés során egyes belső összetevők a nyilvános VIP-címek használatával megkezdik az internet elérését a külső hálózaton keresztül. Az üzembe helyezést követően az Azure és a Azure Stack közötti összes kommunikációt a külső hálózaton keresztül, nyilvános VIP-címek használatával hajtja végre.

A Azure Stack kapcsolók hálózati konfigurációi olyan hozzáférés-vezérlési listákat (ACL-eket) tartalmaznak, amelyek korlátozzák a forgalmat bizonyos hálózati források és célhelyek között. A DVM az egyetlen olyan összetevő, amely korlátlan hozzáféréssel rendelkezik; még a HLH is nagyon korlátozott. Megkérheti az OEM-t a testreszabási lehetőségekkel, így egyszerűbbé teheti a felügyeletet és a hozzáférést a hálózatokból. Ezen ACL-ek miatt fontos, hogy ne módosítsa a DNS-és az NTP-kiszolgáló címét a központi telepítés idején. Ha ezt teszi, újra kell konfigurálnia a megoldás összes kapcsolóját.

Az üzembe helyezés befejezése után a rendszer a megadott DNS-és NTP-kiszolgálói címeket továbbra is a külső hálózat használatával fogja használni az SDN-n keresztül. Ha például a központi telepítés befejezése után megtekinti a DNS-kéréseket, a forrás a DVM IP-címről egy nyilvános VIP-re fog váltani.

## <a name="next-steps"></a>További lépések
[Azure-regisztráció ellenőrzése](azure-stack-validate-registration.md)
