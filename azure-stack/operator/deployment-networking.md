---
title: Az Azure Stack üzembe helyezési hálózati forgalom |} A Microsoft Docs
description: Ez a cikk azt ismerteti, milyen hatással vannak az Azure Stack üzembe helyezési hálózati folyamatokkal kapcsolatos.
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
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 20ec9e6ea8831e6667a282074ba28b16da3180f9
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618995"
---
# <a name="about-deployment-network-traffic"></a>Üzembe helyezés hálózati forgalomról
Hálózati forgalom adatfolyamait során az Azure Stack ismertetése telepítési létfontosságú sikeres bevezetését. Ez a cikk végigvezeti várt hálózati forgalom megismerése milyen szolgáltatásokat biztosít a telepítési folyamat során.

Ezen az ábrán az összetevők és a kapcsolatok a telepítési folyamat:

![Az Azure Stack üzembe helyezési hálózati topológia](media/deployment-networking/figure1.png)

> [!NOTE]
> Ez a cikk ismerteti a csatlakoztatott telepítési követelményei, más központi telepítési módszerekkel kapcsolatos további információkért lásd: [Azure Stack üzembe helyezési kapcsolati modellek](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Az üzembe helyezés virtuális gép
Az Azure Stack egy Azure Stack-összetevők futtatásához használt kiszolgáló és a egy extra kiszolgáló az életciklus állomás (HLH) nevű csoportot tartalmaz. A kiszolgáló üzembe helyezése és kezelése, hogy a megoldás az életciklus szolgál, és üzemelteti a központi telepítés (DVM) virtuális gép üzembe helyezése során.

## <a name="deployment-requirements"></a>Központi telepítésére vonatkozó követelmények
Üzembe helyezés megkezdése előtt vannak bizonyos minimális követelményeknek, amelyek érvényesítheti az OEM annak biztosítása érdekében az üzembe helyezés sikeresen befejeződött. Understanding, ezek a követelmények segítségével készítse elő a környezetet, és győződjön meg arról, hogy az érvényesítés sikeres, a következők:

-   [Tanúsítványok](azure-stack-pki-certs.md)
-   [Azure előfizetés](https://azure.microsoft.com/free/?b=17.06)
-   Internetelérés
-   DNS
-   NTP

> [!NOTE]
> Ez a cikk az utolsó három követelmények összpontosít. Az első két további információkért tekintse meg a fenti hivatkozásokat.

## <a name="deployment-network-traffic"></a>Üzembe helyezés a hálózati forgalom
A DVM egy hálózati BMC IP-címmel van konfigurálva, és az internetes hálózati hozzáférést igényel. A BMC-hálózati összetevők nem az összes szükséges külső útválasztás vagy Internet-hozzáférés, de néhány OEM-specifikus összetevőt a hálózatból IP-címek használatával is szükség lehet azt.

Üzembe helyezés során a a DVM hitelesíti az Azure Active Directory (Azure AD) használatával az Azure-fiók az előfizetésből. Ehhez a DVM igényel internet-hozzáféréssel egy adott portot és az URL-címek listáját. A teljes listát megtalálhatja a [végpontok közzététele](azure-stack-integrate-endpoints.md) dokumentációját. A DVM felhasznál egy DNS-kiszolgáló külső URL-címek belső összetevők által végzett DNS-kérelmeket továbbítja. A belső DNS-ben a DNS-továbbító címe telepítené az OEM egészíti ki ezeket a kérelmeket továbbítja. A Ugyanez vonatkozik az NTP-kiszolgáló, egy megbízható idő kiszolgálóra szükség a konzisztencia és az idő szinkronizálása az összes Azure Stack-összetevők karbantartása.

Az üzembe helyezés során a DVM által igényelt internet-hozzáférés csak akkor kimenő forgalomról beszélünk, bejövő hívások nem végzett üzembe helyezés során. Vegye figyelembe, hogy az IP azt használja forrásként, illetve, hogy az Azure Stack nem támogatja a webproxy beállításai. Ezért szükség esetén meg kell adnia egy transzparens proxy vagy a NAT az internet eléréséhez. Üzembe helyezés során az egyes belső összetevőket indul el a nyilvános virtuális IP-címek segítségével a külső hálózaton keresztül éri el az internetet. Üzembe helyezés befejezése után az Azure és az Azure Stack közötti minden kommunikáció használatával jön létre a külső hálózaton keresztül nyilvános virtuális IP-címeket.

Azure Stack-kapcsolókra hálózati konfigurációk tartalmaznak, hozzáférés-vezérlési listák (ACL), amelyek korlátozzák a forgalmat között bizonyos hálózati forrásaként és céljaként. A DVM korlátlan hozzáférés; csak összetevő még a HLH nagyon korlátozódik. Az OEM lehet feltenni testreszabási beállításokat, és a hálózati hozzáférés megkönnyítése érdekében. Ezen ACL miatt fontos ne módosítsa a DNS és az NTP-kiszolgáló címei üzembe helyezéskor. Ha így tesz, szüksége lesz a kapcsolók a megoldás összes újrakonfigurálása.

Üzembe helyezés befejezése után a megadott DNS- és az NTP-kiszolgálócímek továbbra is a rendszer összetevői által közvetlenül használni. Például ha a DNS-kérésekre üzembe helyezés befejezése után ellenőrizheti, a forrás változik a DVM IP-cím egy külső hálózat címtartományából címre.

Üzembe helyezés befejezése után a megadott DNS- és az NTP-kiszolgálócímek továbbra is használhatják a rendszer összetevőit az SDN segítségével a külső hálózaton keresztül. Például ha a DNS-kérésekre üzembe helyezés befejezése után ellenőrizheti, a forrás változik a DVM IP-címről egy nyilvános virtuális IP-cím.

## <a name="next-steps"></a>További lépések
[Azure-regisztráció ellenőrzése](azure-stack-validate-registration.md)
