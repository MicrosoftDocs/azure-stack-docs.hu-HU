---
title: Azure Stack hub robusztus üzembe helyezése és beállítása az Azure Stack hub Hardware Lifecycle Host (HLH) felügyeleti kiszolgáló számára | Microsoft Docs
description: Megtudhatja, mire számíthat egy Azure Stack hub sikeres helyszíni üzembe helyezéséhez, a tervezéstől a telepítést követően.
services: azure-stack
documentationcenter: ''
author: ashika789
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 0d788e1a41ed512a1d39c7a3e3680d193fdaecf2
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939864"
---
# <a name="azure-stack-hub-ruggedized-deployment-overview"></a>Azure Stack hub robusztus üzembe helyezésének áttekintése

Ez a telepítési útmutató a Azure Stack hub robusztus telepítésének és konfigurálásának lépéseit ismerteti. 

Az útmutató célkitűzései a következők:

- Az összetevők telepítése előtt ellenőrizze, hogy az összes előfeltétel teljesült-e.
- Az Azure Stack hub kulcsfontosságú összetevőinek bevezetése robusztus.
- A legfontosabb összetevők telepítésének és konfigurálásának leírása.
- Ellenőrizze az ügyfél központi telepítését.

Ez az üzembe helyezési útmutató a Microsoft Field Professional csapata számára készült, amelynek feladata az Azure Stack hub üzembe helyezése az ügyfél webhelyén.

A virtualizáció, a kiszolgálók, az operációs rendszerek, a Hálózatkezelés és a tárolási megoldások technikai tapasztalata szükséges ahhoz, hogy teljes mértékben megértsék az útmutató tartalmát. A központi telepítési mérnöknek a Hyper-V, Azure Stack hub, az Azure és a Microsoft PowerShell használatával ismernie kell a Microsoft Windows Server 2019-es verziójának ismeretét.

Ez az útmutató az Microsoft Azure Stack hub alapvető összetevőinek üzembe helyezését, valamint a Azure Stack hub robusztus megoldásának sajátosságait ismerteti. Az útmutató nem ismerteti az Azure Stack hub működési eljárásait, és nem fedi le Azure Stack hub összes funkcióját. További információ: [Azure stack hub-kezelői útmutató](https://docs.microsoft.com/azure-stack/operator/).

## <a name="introduction"></a>Bevezetés

Azure Stack hub robusztus, robusztus és mezővel telepíthető Microsoft Azure Stack hub-ajánlat. Az alapvető összetevők, például a kiszolgálók és kapcsolók a hüvelyek nevű tranzit esetekben szerepelnek.

A pod egy olyan 4U rack-tároló, amely kisebb méretekkel rendelkezik, mint a normál 4U rack. Egy felügyeleti Pod és két skálázási egység (SU) hüvelye van. A felügyeleti Pod tartalmazza a hardveres életciklus-gazdagépet (HLH), a 2 25 GbE, valamint a alaplapi felügyeleti vezérlő (BMC) kapcsolót.

Minden SU Pod két Azure Stack hub robusztus R640 SU-kiszolgálóval rendelkezik. Egy R640-kiszolgáló 2U állványt foglal a pod-ban. Az üzembe helyezés során az SU-hüvelyben található kiszolgálók BMC-és ToR-kapcsolókhoz kapcsolódnak a felügyeleti Pod-ban.

## <a name="terminology"></a>Terminológia

A következő táblázat felsorolja az útmutatóban használt néhány kifejezést.

|Időszak   | Definíció |
|-------|------------|
|Hardver életciklus-állomása (HLH)| A HLH a kezdeti üzembe helyezési rendszerindításhoz használt fizikai kiszolgáló, valamint az Azure Stack hub-infrastruktúra folyamatos hardveres felügyelete, támogatása és biztonsági mentése. A HLH asztali felhasználói felülettel és Hyper-V szerepkörrel futtatja a Windows Server 2019-es verzióját. A kiszolgáló a hardver-felügyeleti eszközök, a felügyeleti eszközök, az Azure Stack hub-partneri eszközkészlet és a telepítési virtuális gép üzemeltetésére szolgál. |
|Üzembe helyezési virtuális gép (DVM)|  A DVM egy virtuális gép, amely az Azure Stack hub szoftver központi telepítésének időtartama alatt jön létre a HLH. A DVM Azure Stack hub szoftvertelepítő motort futtat, amely az Enterprise Cloud Engine (EGB) használatával telepíti és konfigurálja a Azure Stack hub Fabric-infrastruktúra szoftverét az összes Azure Stack hub-méretezési egység kiszolgálóin a hálózaton keresztül.|
|Azure Stack hub partneri eszközkészlet|   Az ügyfél-specifikus bemeneti paraméterek rögzítéséhez és az Azure Stack hub telepítésének és konfigurálásának kezdeményezéséhez használt szoftvereszközök gyűjteménye. Tartalmazza a telepítési munkalapot, amely egy grafikus felhasználói felület (GUI) eszköz, amely a Azure Stack hub telepítéséhez konfigurálható paraméterek rögzítésére és tárolására szolgál. Emellett tartalmazza a hálózati konfigurációs generátor eszközt is, amely az üzembe helyezési munkalap bemeneteit használja a megoldásban lévő összes fizikai hálózati eszköz hálózati konfigurációs fájljainak létrehozásához.|
|OEM-bővítmény csomag  |A belső vezérlőprogram, eszközillesztők és hardveres felügyeleti eszközök csomagját Azure Stack hub által a kezdeti telepítés és a frissítés során használt speciális formátumban.|
|Integrált Dell-távelérési vezérlő (iDRAC)|  Az életciklus-vezérlővel rendelkező iDRAC egy alaplapi felügyeleti vezérlő, amely minden Azure Stack hub robusztus R640-kiszolgálóra van beágyazva. a iDRAC sávon kívüli felügyeleti funkciókat biztosít Azure Stack hub-kiszolgálók üzembe helyezéséhez, frissítéséhez, figyeléséhez és karbantartásához.|
|Méretezési egység |Azure Stack hub alapvető összetevője, amely számítási és tárolási erőforrásokat biztosít Azure Stack hub-háló infrastruktúra és munkaterhelések számára. Négy Azure Stack hub robusztus R640-kiszolgálóból (más néven csomópontokból) áll, és akár 16 csomópontra is dinamikusan méretezhető.|
|Pod    |Azure Stack hub robusztus környezete kontextusában a pod egy olyan fizikai robusztus tároló, amely két személy számára készült, amely rack-rögzítő zárójeleket és lengéscsillapítókat tartalmaz, hogy megvédje Azure Stack hub robusztus hardvert a környezeti fizikai terheléstől. Magában foglalja az előre és vissza továbbítási eseteket is, amelyek a hardver szállítására telepíthetők és lezárhatók. A minimális konfigurációban a teljes megoldás három hüvelyt tartalmaz.|


## <a name="deployment-overflow"></a>Központi telepítés túlcsordulása

Magas szinten a Azure Stack hub robusztus üzembe helyezési folyamata a következő lépésekből áll.

1. Tervezési fázis:
   1. Az adatközpont teljesítményének és hűtésének tervezése.
   1. Azure Stack hub logikai hálózati konfigurációjának megtervezése.
   1. Az adatközpont hálózati integrációjának megtervezése.
   1. Identitás-és biztonsági integráció tervezése.
   1. PKI-tanúsítványok tervezése.
1. Előkészítési fázis:
   1. A kicsomagolás és a leltár begyűjtése.
   1. A megoldás csatlakoztatása és az áramellátás.
   1. A fizikai hardver állapotának ellenőrzése.
1. Végrehajtási fázis:
   1. A hardver életciklus-gazdagép konfigurálása.
   1. Hálózati kapcsolók konfigurálása.
   1. Datacenter hálózati integráció.
   1. A fizikai hardver beállításainak konfigurálása.
   1. Azure Stack hub Fabric-infrastruktúra üzembe helyezése.
   1. Adatközpont identitásának integrációja.
   1. Bővítmények telepítése kiterjesztett funkciókhoz.
1. Ellenőrzési fázis:
   1. Telepítés utáni állapot ellenőrzése.
   1. Azure Stack hub regisztrálása a Microsofttal.
   1. Azure Stack hub operátora.
   
A fenti témakörök mindegyikét részletesebben ismertetjük ebben az útmutatóban.
