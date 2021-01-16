---
title: A moduláris adatközpont (MDC) üzembe helyezésének áttekintése és beállítása az Azure Stack hub Hardware Lifecycle Host (HLH) felügyeleti kiszolgáló számára | Microsoft Docs
description: Megtudhatja, mire számíthat a moduláris adatközpontok (MDC-EK) sikeres helyszíni üzembe helyezésének megtervezése és a telepítés utáni üzembe helyezése között.
services: azure-stack
documentationcenter: ''
author: asganesh
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 5d59532308239f9eda95ba9f81599235a8c8f61c
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255554"
---
# <a name="mdc-requirements-overview"></a>A MDC-követelmények áttekintése

Ez az útmutató ismerteti a moduláris adatközpont (MDC) telepítéséhez és konfigurálásához szükséges követelményeket. 

Az útmutató célkitűzései a következők:

- Az összetevők telepítése előtt ellenőrizze, hogy az összes előfeltétel teljesült-e.
- Egy MDC fő összetevőinek bemutatása.
- Ellenőrizze az ügyfél központi telepítését.

A virtualizáció, a kiszolgálók, az operációs rendszerek, a Hálózatkezelés és a tárolási megoldások technikai tapasztalata szükséges ahhoz, hogy teljes mértékben megértsék az útmutató tartalmát. 

Ez az útmutató az Microsoft Azure Stack hub alapvető összetevőinek üzembe helyezését és a MDC-megoldás sajátosságait ismerteti. Az útmutató nem ismerteti az Azure Stack hub működési eljárásait, és nem fedi le Azure Stack hub összes funkcióját. 

## <a name="introduction"></a>Introduction (Bevezetés)

A MDC egy standard 40-Foot Metal szállítási tárolóba csomagolt Azure Stack hub integrált ajánlata. A tároló tartalmazza a klíma-ellenőrzési egységet, a világítást és a riasztási rendszerét. Az alapszintű Azure Stack hub-összetevők három független hüvelybe vannak telepítve: a pod 1, a rack 1 és a rack 2, a pod 2, a rack 1 és a rack 2, valamint a pod 3, a rack 1 és a rack 2.

Mindegyik Pod két 42U állványból áll. A pod a Top-of-rack (ToR) kapcsolók, az Edge-kapcsolók és a alaplapi felügyeleti vezérlő (BMC) kapcsolót is tartalmazza. Emellett minden egyes Pod tartalmaz egy hardveres életciklus-gazdagépet (HLH) és egy soros port koncentrátort is. Az alapszintű számítási és tárolási kapacitást Azure Stack hub Scale Units (SU), amely nyolc robusztus peremhálózati berendezésű (REA) R840-kiszolgálóból áll. Az 48-Isilon további tárolókapacitást biztosít a Storage-csomópontok. Az összes hüvely fizikai konfigurációja azonos.

## <a name="terminology"></a>Terminológia

A következő táblázat felsorolja az útmutatóban használt néhány kifejezést.

|Kifejezés    |Definíció |
|-------|-----------|
|Hardver életciklus-állomása (HLH)|    A HLH a kezdeti üzembe helyezési rendszerindításhoz használt fizikai kiszolgáló, valamint az Azure Stack hub-infrastruktúra folyamatos hardveres felügyelete, támogatása és biztonsági mentése. A HLH asztali felhasználói felülettel és Hyper-V szerepkörrel futtatja a Windows Server 2019-es verzióját. A kiszolgáló a hardver-felügyeleti eszközök, a felügyeleti eszközök, az Azure Stack hub-partneri eszközkészlet és a telepítési virtuális gép üzemeltetésére szolgál. |
|Üzembe helyezési virtuális gép (DVM)|    A DVM egy virtuális gép, amely az Azure Stack hub szoftver központi telepítésének időtartama alatt jön létre a HLH. A DVM Azure Stack hub szoftvertelepítő motort futtat, amely az Enterprise Cloud Engine (EGB) használatával telepíti és konfigurálja a Azure Stack hub Fabric-infrastruktúra szoftverét az összes Azure Stack hub-méretezési egység kiszolgálóin a hálózaton keresztül.|
|Azure Stack hub partneri eszközkészlet|    Az ügyfél-specifikus bemeneti paraméterek rögzítéséhez és az Azure Stack hub telepítésének és konfigurálásának kezdeményezéséhez használt szoftvereszközök gyűjteménye. Tartalmazza a telepítési munkalapot, amely egy grafikus felhasználói felület (GUI) eszköz, amely a Azure Stack hub telepítéséhez konfigurálható paraméterek rögzítésére és tárolására szolgál. Emellett tartalmazza a hálózati konfigurációs generátor eszközt is, amely az üzembe helyezési munkalap bemeneteit használja a megoldásban lévő összes fizikai hálózati eszköz hálózati konfigurációs fájljainak létrehozásához.|
|OEM-bővítmény csomag    |A belső vezérlőprogram, eszközillesztők és hardveres felügyeleti eszközök csomagját Azure Stack hub által a kezdeti telepítés és a frissítés során használt speciális formátumban.|
|Soros port koncentrátor    |Minden Pod-ban telepített fizikai eszköz, amely a hálózati kapcsolók soros portjaihoz való hálózati hozzáférést biztosít üzembe helyezési és felügyeleti célokra.|
|Méretezési egység    |Azure Stack hub alapvető összetevője, amely számítási és tárolási erőforrásokat biztosít Azure Stack hub-háló infrastruktúra és munkaterhelések számára. Mindegyik Pod nyolc MDC R840-kiszolgálót tartalmaz, más néven csomópontokat.|
|Isilon-tároló |    Egy Azure Stack hub-összetevő, amely a MDC-megoldásra jellemző. A Isilon további blob-és file Storage-t biztosít Azure Stack hub munkaterhelésekhez. Mindegyik Pod 48 Isilon-tároló csomópontot tartalmaz.|
|Pod    |A MDC kontextusában a pod egy független logikai egység, amely két összekapcsolt fizikai állványból áll. A teljes megoldás három, egyetlen tárolóban telepített hüvelyt tartalmaz.|

## <a name="deployment-workflow"></a>Üzembe helyezési munkafolyamat

Magas szinten a MDC üzembe helyezési folyamata a következő fázisokból áll:

### <a name="planning-phase"></a>Tervezési fázis
1. Az adatközpont energiagazdálkodásának tervezése.
1. Azure Stack hub logikai hálózati konfigurációjának megtervezése.
1. Az [adatközpont hálózati integrációjának](../operator/azure-stack-network.md)megtervezése.
1. [Identitás-és biztonsági integráció](/azure/security/fundamentals/identity-management-best-practices)tervezése.
1. PKI- [tanúsítványok](../operator/azure-stack-pki-certs.md)tervezése.

### <a name="preparation-phase"></a>Előkészítési fázis
1. Leltár gyűjtése.
1. A megoldás csatlakoztatása és az áramellátás.
1. A HVAC-rendszerek állapotának ellenőrzése.
1. A tűzvédelmi és a riasztási rendszer állapotának ellenőrzése.
1. A fizikai hardver állapotának ellenőrzése.

### <a name="execution-phase--separately-for-each-of-the-three-pods"></a>Végrehajtási fázis – külön a három hüvely esetében
1. A hardver életciklus-gazdagép konfigurálása.
1. Hálózati kapcsolók konfigurálása.
1. Datacenter hálózati integráció.
1. A fizikai hardver beállításainak konfigurálása.
1. Isilon-tároló konfigurálása.
1. Azure Stack hub Fabric-infrastruktúra üzembe helyezése.
1. Adatközpont identitásának integrációja.
1. Bővítmények telepítése kiterjesztett funkciókhoz.

### <a name="validation-phase--separately-for-each-of-the-three-pods"></a>Ellenőrzési fázis – külön a három hüvely esetében
1. Telepítés utáni állapot ellenőrzése.
1. Azure Stack hub regisztrálása a Microsofttal.
1. Azure Stack hub-ügyfél számára.