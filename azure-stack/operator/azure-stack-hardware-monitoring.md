---
title: A hardver állapotának figyelése Azure Stack központban
description: Megtudhatja, hogyan figyelheti Azure Stack hub hardveres összetevőinek állapotát.
author: sethmanheim
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: dff881fe332367b0911c21c0e23745210feb9051
ms.sourcegitcommit: c9737939f4e437f1d954e163db972d58b3f98ffd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/16/2020
ms.locfileid: "84813761"
---
# <a name="monitor-azure-stack-hub-hardware-components"></a>Azure Stack hub hardver-összetevőinek figyelése

Az Azure Stack hub állapot-és figyelési rendszere figyeli a tárolási alrendszer állapotát, és szükség szerint riasztásokat küld. Az állapot-és monitorozási rendszer riasztásokat is felvehet a következő hardver-összetevőkre:

- Rendszerventilátorok
- Rendszerhőmérséklet
- Tápegység
- CPU-k
- Memory (Memória)
- Rendszerindító meghajtók

> [!NOTE]
> A szolgáltatás engedélyezése előtt ellenőriznie kell a hardveres partnerét, hogy készen állnak-e. A hardveres partner emellett részletesen ismerteti a funkció engedélyezésének lépéseit a alaplapi felügyeleti vezérlőben (BMC).

## <a name="snmp-listener-scenario"></a>SNMP-figyelő forgatókönyve

Az SNMP v3-figyelő mindhárom ERCS-példányon fut a 162-es TCP-porton. A BMC-t úgy kell konfigurálni, hogy SNMP-trapeket küldjön az Azure Stack hub-figyelőnek. A három PEP IP-címet a felügyeleti portálról kérheti le a régió tulajdonságai nézet megnyitásával.

A figyelőhöz tartozó trapek küldéséhez hitelesítés szükséges, és ugyanazokat a hitelesítő adatokat kell használnia, mint az alapszintű BMC eléréséhez.

Ha a 162-es TCP-port bármelyik három ERCS-példányán SNMP-trap érkezik, az OID belső egyeztetést végez, és riasztást vált ki. Az Azure Stack hub állapot-és figyelési rendszere csak a hardvereszköz által definiált OID-ket fogadja el. Ha egy OID ismeretlen az Azure Stack hub számára, nem egyezik meg a riasztással.

A hibás összetevő cseréje után a rendszer egy eseményt továbbít a BMC-ből az SNMP-figyelőnek, amely jelzi az állapot változását. A riasztás ezután automatikusan bezáródik Azure Stack központban.

> [!NOTE]
> A meglévő riasztások nem zárulnak automatikusan, ha a teljes csomópontot vagy az alaplapot lecserélik. Ugyanez vonatkozik arra az esetre, amikor a BMC elveszti a konfigurációját. például a gyári beállítások visszaállítása miatt.

## <a name="next-steps"></a>További lépések

[Tűzfal-integráció](azure-stack-firewall.md)
