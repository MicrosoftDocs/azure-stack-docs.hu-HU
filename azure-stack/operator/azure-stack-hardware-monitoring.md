---
title: A hardver állapotának figyelése Azure Stack | Microsoft Docs
description: Megtudhatja, hogyan figyelheti Azure Stack hardveres összetevők állapotát.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: b41a60aaf45d4b4fbbbf00945a4048dbf9dfb13a
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308223"
---
# <a name="monitor-azure-stack-hardware-components"></a>Azure Stack hardver-összetevők figyelése

Azure Stack állapot-és figyelési rendszere már figyeli a tárolási alrendszer állapotát, és szükség szerint riasztásokat küld. A Azure Stack 1910-es kiadásával az állapot-és monitorozási rendszer mostantól riasztásokat is felvehet a következő hardver-összetevőkre:

- Rendszerventilátorok
- Rendszerhőmérséklet
- Tápegység
- CPU-k
- Memory (Memória)
- Rendszerindító meghajtók

> [!NOTE]
> A funkció engedélyezése előtt ellenőriznie kell a hardveres partnerével, hogy készen állnak-e. A hardveres partner a BMC-ben a funkció engedélyezésének részletes lépéseit is megadja.

## <a name="snmp-listener-scenario"></a>SNMP-figyelő forgatókönyve

Az SNMP v3-figyelő mindhárom ERCS-példányon fut a 162-es TCP-porton. Az alaplapi felügyeleti vezérlőt (BMC) úgy kell konfigurálni, hogy SNMP-trapeket küldjön a Azure Stack figyelőnek. A három PEP IP-címet a felügyeleti portálról kérheti le a régió tulajdonságai nézet megnyitásával.

A figyelőhöz tartozó trapek küldéséhez hitelesítés szükséges, és ugyanazokat a hitelesítő adatokat kell használnia, mint az alapszintű BMC eléréséhez.

Ha a 162-es TCP-port bármelyik három ERCS-példányán SNMP-trap érkezik, az OID belső egyeztetést végez, és riasztást vált ki. A Azure Stack állapot-és figyelési rendszer csak a hardvereszköz által meghatározott OID-ket fogadja el. Ha egy OID ismeretlen Azure Stack, akkor nem felel meg a riasztásnak.

A hibás összetevő cseréje után egy eseményt küld a BMC-ből az SNMP-figyelőnek, amely jelzi az állapotváltozás állapotát, és a riasztás automatikusan bezáródik Azure Stackban.

> [!NOTE]
> A meglévő riasztások nem lesznek automatikusan lezárva, amikor a teljes csomópontot vagy az alaplapot lecserélik. Ugyanez vonatkozik arra az esetre, amikor a BMC elveszti a konfigurációját. például a gyári beállítások visszaállítása miatt.

## <a name="next-steps"></a>További lépések

[Tűzfal-integráció](azure-stack-firewall.md)
