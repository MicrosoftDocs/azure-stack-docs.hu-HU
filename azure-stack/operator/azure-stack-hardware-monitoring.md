---
title: Azure Stack hub hardver állapotának figyelése
description: Megtudhatja, hogyan figyelheti Azure Stack hub hardveres összetevőinek állapotát.
author: sethmanheim
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: dba47774205b4c0631f33de336e0efbfb73db9cf
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882216"
---
# <a name="monitor-azure-stack-hub-hardware-components"></a>Azure Stack hub hardver-összetevőinek figyelése

Azure Stack hub állapot-és monitorozási rendszere már figyeli a tárolási alrendszer állapotát, és szükség szerint riasztásokat küld. Az Azure Stack hub 1910-es kiadásával az állapot-és monitorozási rendszer mostantól riasztásokat is felvehet a következő hardver-összetevőkre:

- Rendszerventilátorok
- Rendszerhőmérséklet
- Tápegység
- CPU-k
- Memória
- Rendszerindító meghajtók

> [!NOTE]
> A funkció engedélyezése előtt ellenőriznie kell a hardveres partnerével, hogy készen állnak-e. A hardveres partner a BMC-ben a funkció engedélyezésének részletes lépéseit is megadja.

## <a name="snmp-listener-scenario"></a>SNMP-figyelő forgatókönyve

Az SNMP v3-figyelő mindhárom ERCS-példányon fut a 162-es TCP-porton. Az alaplapi felügyeleti vezérlőt (BMC) úgy kell konfigurálni, hogy SNMP-trapeket küldjön az Azure Stack hub-figyelőnek. A három PEP IP-címet a felügyeleti portálról kérheti le a régió tulajdonságai nézet megnyitásával.

A figyelőhöz tartozó trapek küldéséhez hitelesítés szükséges, és ugyanazokat a hitelesítő adatokat kell használnia, mint az alapszintű BMC eléréséhez.

Ha a 162-es TCP-port bármelyik három ERCS-példányán SNMP-trap érkezik, az OID belső egyeztetést végez, és riasztást vált ki. Az Azure Stack hub állapot-és figyelési rendszere csak a hardvereszköz által definiált OID-ket fogadja el. Ha az OID ismeretlen a Azure Stack hub számára, akkor az nem felel meg a riasztásnak.

A hibás összetevő cseréje után egy eseményt küld a BMC-ből az SNMP-figyelőnek, amely jelzi az állapotváltozás állapotát, és a riasztás automatikusan bezáródik Azure Stack központban.

> [!NOTE]
> A meglévő riasztások nem lesznek automatikusan lezárva, amikor a teljes csomópontot vagy az alaplapot lecserélik. Ugyanez vonatkozik arra az esetre, amikor a BMC elveszti a konfigurációját. például a gyári beállítások visszaállítása miatt.

## <a name="next-steps"></a>Következő lépések

[Tűzfal-integráció](azure-stack-firewall.md)
