---
title: A Azure Stack kapcsolók konfigurációjának konkrét beállításainak módosítása | Microsoft Docs
description: Megtudhatja, mi testreszabható a Azure Stack kapcsolók konfigurációjában. Miután az eredeti berendezésgyártó (OEM) létrehozta a konfigurációt, ne változtassa meg a SZÁMÍTÓGÉPGYÁRTÓ vagy a Microsoft Azure Stack mérnöki csapat beleegyezike nélkül.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: Femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 1bd138be243c2803b5a280de2a3a8625e84db998
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955336"
---
#  <a name="modify-specific-settings-on-your-azure-stack-switch-configuration"></a>A Azure Stack kapcsoló konfigurációjának adott beállításainak módosítása

Módosíthatja a Azure Stack kapcsoló konfigurációjának néhány környezeti beállítását. Az eredeti berendezésgyártó (OEM) által létrehozott sablonban megváltoztatható beállítások közül választhat. Ez a cikk ismerteti ezeket a testreszabható beállításokat, valamint azt, hogy a módosítások milyen hatással lehetnek a Azure Stackra. A beállítások közé tartozik a jelszó frissítése, a syslog-kiszolgáló, az SNMP-figyelés, a hitelesítés és a hozzáférés-vezérlési lista. 

Az Azure Stack-megoldás üzembe helyezése során az eredeti berendezésgyártó (OEM) létrehozza és alkalmazza a kapcsoló konfigurációját mind a beléptetési, mind a BMC-hez. Az OEM a Azure Stack Automation eszköz használatával ellenőrzi, hogy a szükséges konfigurációk megfelelően vannak-e beállítva az eszközökön. A konfiguráció a Azure Stack [telepítési munkalapon](azure-stack-deployment-worksheet.md)található információk alapján történik. Miután az OEM létrehozta a konfigurációt, **ne** módosítsa a konfigurációt a számítógépgyártó vagy a Microsoft Azure stack mérnöki csapat beleegyezike nélkül. A hálózati eszköz konfigurációjának módosítása jelentős hatással lehet a Azure Stack-példány hálózati problémáinak működésére vagy hibaelhárítására.

Vannak azonban olyan értékek, amelyek hozzáadhatók, eltávolíthatók vagy módosíthatók a hálózati kapcsolók konfigurációjában.

>[!Warning]  
> **Ne módosítsa a** konfigurációt a számítógépgyártó vagy a Microsoft Azure stack mérnöki csapat beleegyezike nélkül. A hálózati eszköz konfigurációjának módosítása jelentős hatással lehet a Azure Stack-példány hálózati problémáinak működésére vagy hibaelhárítására.
>
> Ha többet szeretne megtudni ezekről a függvényekről a hálózati eszközön, hogyan hajthatja végre ezeket a módosításokat, forduljon az OEM-szolgáltatóhoz vagy a Microsoft ügyfélszolgálatához. A SZÁMÍTÓGÉPGYÁRTÓ rendelkezik az Automation eszköz által létrehozott konfigurációs fájllal a Azure Stack üzembe helyezési munkalap alapján. 

## <a name="password-update"></a>Jelszó frissítése

Az operátor bármikor frissítheti a hálózati kapcsolók bármelyik felhasználójának jelszavát. Nincs szükség a Azure Stack rendszerre vonatkozó információk módosítására, vagy a [titkok elforgatásának lépéseire Azure Stackban](azure-stack-rotate-secrets.md).

## <a name="syslog-server"></a>Syslog-kiszolgáló

A kezelők átirányítják a kapcsoló naplóit egy syslog-kiszolgálóra az adatközpontjában. Ezzel a konfigurációval ellenőrizheti, hogy egy adott időpontból származó naplók használhatók-e hibaelhárításhoz. Alapértelmezés szerint a rendszer a naplókat a kapcsolókon tárolja. a naplók tárolásának kapacitása korlátozott. Tekintse át a [hozzáférés-vezérlési lista frissítései](#access-control-list-updates) szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

## <a name="snmp-monitoring"></a>SNMP-figyelés

Az operátor konfigurálhatja a hálózati eszközök figyelésére és a trapek küldését egy hálózati figyelő alkalmazásba az adatközpontban. Biztonsági okokból a SNMPv3-t használja, mivel ez a v2-nél biztonságosabb. A szükséges MIB és konfigurációval kapcsolatban forduljon az OEM-hardvereszközökhöz. Tekintse át a [hozzáférés-vezérlési lista frissítései](#access-control-list-updates) szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

## <a name="authentication"></a>Hitelesítés

Az operátor a hálózati eszközök hitelesítésének kezeléséhez RADIUS-vagy TACACS konfigurálhat. A támogatott módszerekhez és konfigurációhoz forduljon az OEM-hardveres szolgáltatóhoz.  Tekintse át a [hozzáférés-vezérlési lista frissítései](#access-control-list-updates) szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

## <a name="access-control-list-updates"></a>Hozzáférés-vezérlési lista frissítései

Az operátor módosíthatja a hozzáférés-vezérlési listák (ACL-ek) egy részét, hogy lehetővé tegye a hálózati eszközök felügyeleti felületéhez és a hardveres életciklus-gazdagéphez (HLH) való hozzáférést egy megbízható adatközpont hálózati tartományában. A kezelő kiválaszthatja, hogy melyik összetevő érhető el, és honnan. A hozzáférés-vezérlési listával az operátor engedélyezheti a felügyeleti Jumpbox egy adott hálózati tartományon belül, hogy hozzáférjenek a Switch Management felületéhez, valamint a HLH operációs rendszerhez és a HLH BMC-hez.

## <a name="next-steps"></a>Következő lépések

[Azure Stack Datacenter-integráció – DNS](azure-stack-integrate-dns.md)