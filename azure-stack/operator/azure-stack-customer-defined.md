---
title: Azure Stack hub kapcsoló konfigurációjának beállításainak módosítása
description: Ismerje meg, hogy milyen beállítások módosíthatók az Azure Stack hub kapcsoló konfigurációjában.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 9fcbd0203684afbbae9d11ed00db12402d8040dd
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836002"
---
# <a name="change-settings-on-your-azure-stack-hub-switch-configuration"></a>Azure Stack hub kapcsoló konfigurációjának beállításainak módosítása

Az Azure Stack hub kapcsoló konfigurációjának néhány környezeti beállítása módosítható. Az eredeti berendezésgyártó (OEM) által létrehozott sablonban megváltoztatható beállítások közül választhat. Ez a cikk ismerteti ezeket a testreszabható beállításokat, és azt, hogy a módosítások milyen hatással lehetnek az Azure Stack hub-ra. Ezek a beállítások közé tartoznak a jelszó frissítése, a syslog-kiszolgáló, a Simple Network Management Protocol (SNMP) figyelése, a hitelesítés és a hozzáférés-vezérlési lista.

Az Azure Stack hub megoldás üzembe helyezése során az eredeti berendezésgyártó (OEM) létrehozza és alkalmazza a kapcsoló konfigurációját mind a befogadói, mind a BMC-hez. Az OEM az Azure Stack hub Automation eszköz használatával ellenőrzi, hogy a szükséges konfigurációk megfelelően vannak-e beállítva az eszközökön. A konfiguráció az Azure Stack hub [üzembe helyezési munkalapján](azure-stack-deployment-worksheet.md)található információkon alapul.

> [!Warning]  
> Miután az OEM létrehozta a konfigurációt, **ne** változtassa meg a konfigurációt a számítógépgyártó vagy a Microsoft Azure stack hub Engineering csapatának beleegyező engedélye nélkül. A hálózati eszköz konfigurációjának módosítása jelentős hatással lehet a Azure Stack hub-példány hálózati problémáinak működésére vagy hibaelhárítására.
>
> További információ ezekről a függvényekről a hálózati eszközön, a módosítások elvégzéséhez forduljon az OEM-szolgáltatóhoz vagy a Microsoft ügyfélszolgálatához. A SZÁMÍTÓGÉPGYÁRTÓ rendelkezik az Automation eszköz által létrehozott konfigurációs fájllal, amely az Azure Stack hub üzembe helyezési munkalapján alapul.

Vannak azonban olyan értékek, amelyek hozzáadhatók, eltávolíthatók vagy módosíthatók a hálózati kapcsolók konfigurációjában.

## <a name="password-update"></a>Jelszó frissítése

Az operátor bármikor frissítheti a hálózati kapcsolók bármelyik felhasználójának jelszavát. Nem kell módosítania az Azure Stack hub rendszerre vonatkozó információkat, vagy a [titkok Elforgatása Azure stack hub-ban](azure-stack-rotate-secrets.md)című témakörben leírt lépéseket.

## <a name="syslog-server"></a>Syslog-kiszolgáló

A kezelők átirányítják a kapcsoló naplóit egy syslog-kiszolgálóra az adatközpontjában. Ezzel a konfigurációval ellenőrizheti, hogy egy adott időpontból származó naplók használhatók-e hibaelhárításhoz. Alapértelmezés szerint a rendszer a naplókat a kapcsolókon tárolja, de a naplók tárolásának kapacitása korlátozott. Tekintse át a [hozzáférés-vezérlési lista frissítései](#access-control-list-updates) szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

## <a name="snmp-monitoring"></a>SNMP-figyelés

Az operátor konfigurálhatja az SNMP v2 vagy v3 verzióját a hálózati eszközök figyelésére és a trapek küldésére az adatközpontban található Hálózatfigyelő alkalmazásba. Biztonsági okokból a SNMPv3-t használja, mivel ez a v2-nél biztonságosabb. A szükséges MIB és konfigurációval kapcsolatban forduljon az OEM-hardvereszközökhöz. Tekintse át a [hozzáférés-vezérlési lista frissítései](#access-control-list-updates) szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

## <a name="authentication"></a>Hitelesítés

Az operátor a hálózati eszközök hitelesítésének kezeléséhez RADIUS-vagy TACACS konfigurálhat. A támogatott módszerekhez és konfigurációhoz forduljon az OEM-hardveres szolgáltatóhoz. Tekintse át a [hozzáférés-vezérlési lista frissítései](#access-control-list-updates) szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

## <a name="access-control-list-updates"></a>Hozzáférés-vezérlési lista frissítései

> [!NOTE]
> A 1910-es verziótól kezdődően az üzembe helyezési munkalap egy új mezővel fog rendelkezni az **engedélyezett hálózatokra** vonatkozóan, amelyek lecserélik azokat a manuális lépéseket, amelyek lehetővé teszik a hálózati eszközök felügyeleti felületeinek elérését, valamint a hardveres életciklus-GAZDAGÉP (HLH) megbízható adatközpont További információ erről az új szolgáltatásról: [Azure stack hub hálózati integrációjának megtervezése](azure-stack-network.md#permitted-networks).

A kezelő bizonyos hozzáférés-vezérlési listákat (ACL-ek) módosíthat, hogy engedélyezze a hozzáférést a hálózati eszközök felügyeleti felületéhez és a hardveres életciklus-gazdagéphez (HLH) egy megbízható adatközpont-hálózati tartományból. A hozzáférés-vezérlési listával az operátor engedélyezheti a felügyeleti Jumpbox egy adott hálózati tartományon belül, hogy hozzáférjen a Switch Management felületéhez, a HLH operációs rendszerhez és a HLH BMC-hez.

## <a name="next-steps"></a>További lépések

[Azure Stack hub Datacenter-integráció – DNS](azure-stack-integrate-dns.md)
