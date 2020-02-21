---
title: Az Azure Stack hub kapcsoló konfigurációjának adott beállításainak módosítása
description: Ismerje meg, hogy mi testreszabható a Azure Stack hub switch konfigurációjában. Miután az eredeti berendezésgyártó (OEM) létrehozta a konfigurációt, ne változtassa meg a SZÁMÍTÓGÉPGYÁRTÓ vagy a Microsoft Azure Stack hub Engineering csapat beleegyezike nélkül.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 11/11/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 5071af80c5063db8d4fc127f2f64311b21785f12
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77510078"
---
#  <a name="modify-specific-settings-on-your-azure-stack-hub-switch-configuration"></a>Az Azure Stack hub kapcsoló konfigurációjának adott beállításainak módosítása

Az Azure Stack hub kapcsoló konfigurációjának néhány környezeti beállítása módosítható. Az eredeti berendezésgyártó (OEM) által létrehozott sablonban megváltoztatható beállítások közül választhat. Ez a cikk ismerteti ezeket a testreszabható beállításokat, valamint azt, hogy a módosítások milyen hatással lehetnek az Azure Stack hub-ra. A beállítások közé tartozik a jelszó frissítése, a syslog-kiszolgáló, az SNMP-figyelés, a hitelesítés és a hozzáférés-vezérlési lista. 

Az Azure Stack hub megoldás üzembe helyezése során az eredeti berendezésgyártó (OEM) létrehozza és alkalmazza a kapcsoló konfigurációját mind a befogadói, mind a BMC-hez. Az OEM az Azure Stack hub Automation eszköz használatával ellenőrzi, hogy a szükséges konfigurációk megfelelően vannak-e beállítva az eszközökön. A konfiguráció a Azure Stack hub [üzembe helyezési munkalapon](azure-stack-deployment-worksheet.md)található információk alapján történik. Miután az OEM létrehozta a konfigurációt, **ne** változtassa meg a konfigurációt a számítógépgyártó vagy a Microsoft Azure stack hub Engineering csapatának beleegyező engedélye nélkül. A hálózati eszköz konfigurációjának módosítása jelentős hatással lehet a Azure Stack hub-példány hálózati problémáinak működésére vagy hibaelhárítására.

Vannak azonban olyan értékek, amelyek hozzáadhatók, eltávolíthatók vagy módosíthatók a hálózati kapcsolók konfigurációjában.

>[!Warning]  
> **Ne változtassa** meg a konfigurációt a számítógépgyártó vagy a Microsoft Azure stack hub Engineering csapat beleegyezike nélkül. A hálózati eszköz konfigurációjának módosítása jelentős hatással lehet a Azure Stack hub-példány hálózati problémáinak működésére vagy hibaelhárítására.
>
> Ha többet szeretne megtudni ezekről a függvényekről a hálózati eszközön, hogyan hajthatja végre ezeket a módosításokat, forduljon az OEM-szolgáltatóhoz vagy a Microsoft ügyfélszolgálatához. A SZÁMÍTÓGÉPGYÁRTÓ rendelkezik az Automation eszköz által létrehozott konfigurációs fájllal, amely az Azure Stack hub üzembe helyezési munkalapján alapul. 

## <a name="password-update"></a>Jelszó frissítése

Az operátor bármikor frissítheti a hálózati kapcsolók bármelyik felhasználójának jelszavát. Nem kell módosítania az Azure Stack hub rendszerre vonatkozó információkat, vagy a [titkok elforgatásának lépéseit Azure stack központban](azure-stack-rotate-secrets.md).

## <a name="syslog-server"></a>Syslog-kiszolgáló

A kezelők átirányítják a kapcsoló naplóit egy syslog-kiszolgálóra az adatközpontjában. Ezzel a konfigurációval ellenőrizheti, hogy egy adott időpontból származó naplók használhatók-e hibaelhárításhoz. Alapértelmezés szerint a rendszer a naplókat a kapcsolókon tárolja. a naplók tárolásának kapacitása korlátozott. Tekintse át a [hozzáférés-vezérlési lista frissítései](#access-control-list-updates) szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

## <a name="snmp-monitoring"></a>SNMP-figyelés

Az operátor konfigurálhatja a hálózati eszközök figyelésére és a trapek küldését egy hálózati figyelő alkalmazásba az adatközpontban. Biztonsági okokból a SNMPv3-t használja, mivel ez a v2-nél biztonságosabb. A szükséges MIB és konfigurációval kapcsolatban forduljon az OEM-hardvereszközökhöz. Tekintse át a [hozzáférés-vezérlési lista frissítései](#access-control-list-updates) szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

## <a name="authentication"></a>Authentication

Az operátor a hálózati eszközök hitelesítésének kezeléséhez RADIUS-vagy TACACS konfigurálhat. A támogatott módszerekhez és konfigurációhoz forduljon az OEM-hardveres szolgáltatóhoz.  Tekintse át a [hozzáférés-vezérlési lista frissítései](#access-control-list-updates) szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

## <a name="access-control-list-updates"></a>Hozzáférés-vezérlési lista frissítései

> [!NOTE]
> A 1910-es verziótól kezdődően az üzembe helyezési munkalap egy új mezővel fog rendelkezni az **engedélyezett hálózatokra** vonatkozóan, amelyek lecserélik azokat a manuális lépéseket, amelyek lehetővé teszik a hálózati eszközök felügyeleti felületeinek elérését, valamint a hardveres életciklus-GAZDAGÉP (HLH) megbízható adatközpont Az új szolgáltatással kapcsolatos további információkért tekintse meg a [Azure stack hub hálózati integrációjának megtervezését](azure-stack-network.md#permitted-networks)ismertető témakört.

Az operátor módosíthatja a hozzáférés-vezérlési listák (ACL-ek) egy részét, hogy lehetővé tegye a hálózati eszközök felügyeleti felületéhez és a hardveres életciklus-gazdagéphez (HLH) való hozzáférést egy megbízható adatközpont hálózati tartományában. A hozzáférés-vezérlési listával az operátor engedélyezheti a felügyeleti Jumpbox egy adott hálózati tartományon belül, hogy hozzáférjenek a Switch Management felületéhez, a HLH operációs rendszerhez és a HLH BMC-hez.

## <a name="next-steps"></a>Következő lépések

[Azure Stack hub Datacenter-integráció – DNS](azure-stack-integrate-dns.md)
