---
title: Vásárlói utazás a vásárlástól a Azure Stack hub üzembe helyezése után | Microsoft Docs
description: Ismerje meg, mire számíthat az Azure moduláris adatközpont (MDC) sikeres helyszíni üzembe helyezésének megtervezése az üzembe helyezés után.
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
ms.date: 11/04/2020
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 11/04/2020
ms.openlocfilehash: e69881e5f898b0c1f397a4a6d407da5995dd3049
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872347"
---
# <a name="modular-datacenter-integration-overview"></a>A moduláris adatközpont integrációjának áttekintése

Ez a cikk az Azure Modular Datacenter (MDC) integrációjának teljes körű folyamatát ismerteti, amely az üzembe helyezés után vásárolható meg. Az integráció egy együttműködési projekt az ügyfél és a Microsoft között. A következő szakaszok a projekt ütemtervének különböző fázisait és a projekt tagjainak konkrét lépéseit fedik le.

## <a name="introduction"></a>Bevezetés

A következő táblázat felsorolja, hogy mi várható az üzembe helyezés különböző fázisaiban.

| Résztvevő |Megrendelés folyamata |Üzembe helyezés előtt |Integráció, ellenőrzés, szállítás |Helyszíni üzembe helyezés |Üzembe helyezés utáni |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
|Microsoft  | Az USA-beli kézbesítéshez szükséges jel.    |Adja meg a szükséges eszközöket és dokumentációt az adatközpont-követelmények összegyűjtéséhez. |– A konfigurációs összetevők érvényesítése és az érvényesítési eredmények ellenőrzése.<br>– Gondoskodjon a hardverek kézbesítéséről.    |-Rack és stack.<br>– Hálózati integráció.<br>– Azure Stack hub üzembe helyezése.<br>-Kikapcsolás az ügyfélnek.    |Regisztráció és Azure Stack hub Marketplace Syndication.|
|Ügyfél   |Jel vásárlása.   |-Adja meg a hálózat részleteit a telepítési munkalapon.<br>– Tanúsítványok gyűjtése.<br>– Azure Active Directory (Azure AD-) fiókok beszerzése.<br>– Futtasson a megadott érvényesítési eszközöket.   |Győződjön meg arról, hogy a hely készen áll a hálózat, a teljesítmény és a hűtés előfeltételeire.   |-Készüljön fel az üzembe helyezési konfigurációs összetevőkre.<br>– Győződjön meg arról, hogy az ügyfél hálózati mérnöke elérhető.   |     |


## <a name="order-process"></a>Megrendelés folyamata

A szervezet a Microsofttal együttműködve rendelést rendel a lefoglalt számú rendszerhez. A megrendelés elvégzése után a Microsoft továbbítja a MDC az Egyesült államokbeli tartózkodási helyükre. A Microsoft gondoskodik arról, hogy minden biztonságos ellátási láncra vonatkozó követelmény teljesülni fog.

## <a name="hardware-delivery"></a>Hardveres kézbesítés

A Microsoft Önnel együttműködve gondoskodik arról, hogy az összes szükséges hardver az Egyesült államokbeli helyre jusson a megadott időn belül.

*Rendkívül fontos* , hogy az előfeltételként szükséges összes érték zárolva legyen és elérhető legyen, *mielőtt a helyszíni Microsoft Deployment mérnök megérkezik a megoldás üzembe helyezéséhez.*

- Az üzembe helyezési munkalap minden adattal kitöltött.
- Minden tanúsítványt érvényesíteni kell, és készen kell állnia.
- A régió nevét meg kell határozni.
- Minden hálózati integrációs paraméter véglegesítve lett.

>[!Tip]
>Ha bármelyik információ módosult, ügyeljen arra, hogy működjön együtt a belső szervezettel annak biztosítása érdekében, hogy az adatok a helyszíni üzembehelyezési mérnök megérkezése előtt frissüljenek. Az adatok frissítése megakadályozza a telepítési folyamat késéseit.

## <a name="predeployment"></a>Üzembe helyezés előtt

Ön dönti el, hogyan integrálhatja Azure Stack hub-t az adatközpontba. A Microsoft közzétett egy [üzembe helyezési munkalapot](../operator/azure-stack-deployment-worksheet.md) , amely végigvezeti Önt az adatközpontba való sikeres integráláshoz szükséges összes szükséges információ összegyűjtésén. Emellett bizonyos tanúsítványokra van szükség a telepítéskor. A tanúsítványok beszerzéséhez a Microsoft biztosítja az [Azure stack hub Readiness-ellenőrzőt](../operator/azure-stack-validation-report.md). Ez az eszköz segít létrehozni a belső hitelesítésszolgáltató számára elérhető tanúsítvány-aláírási kérelmeket (munkatársakat).

>[!Important]
>A rendszer az összes előfeltételt érvényesíti, hogy megakadályozza az üzembe helyezés késését. Az Előfeltételek ellenőrzése időt vehet igénybe, és megkövetelheti a szervezeten belüli különböző részlegek koordinálását és az adatok gyűjtését.

A következő elemek közül választhat:

- **Azure Stack hub-kapcsolatok modelljének és identitás-szolgáltatójának.** Dönthet úgy, hogy Azure Stack hubot [az internethez (és az Azure-hoz) csatlakozik,](../operator/azure-stack-connected-deployment.md) vagy [le van választva](../operator/azure-stack-disconnected-deployment.md). Az Azure Stack hub előnyeinek kihasználása érdekében, beleértve a hibrid forgatókönyveket is, az Azure-hoz való csatlakozást szeretné telepíteni. A Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy az Azure AD használata egy egyszeri döntés, amelyet a telepítéskor kell elvégeznie. *Az identitás-szolgáltatót később nem módosíthatja a teljes rendszer újbóli üzembe helyezése nélkül.*
- **Hálózati integráció.** A [hálózati integráció](../operator/azure-stack-network.md) elengedhetetlen a Azure stack hub rendszerek üzembe helyezéséhez, működtetéséhez és felügyeletéhez. Számos szempontot figyelembe kell venni, hogy az Azure Stack hub-megoldás rugalmas, és a működésének támogatásához egy magasan elérhető fizikai infrastruktúrát biztosít.
- **Tűzfal-integráció.** Javasoljuk, hogy a Azure Stack hub biztonságossá tételéhez [Használjon tűzfalat](../operator/azure-stack-firewall.md) . A tűzfalak segítenek megakadályozni a DDoS-támadásokat, a behatolás észlelését és a tartalom vizsgálatát. Vegye figyelembe, hogy a tűzfalak az Azure Storage-szolgáltatások adatforgalmának szűk keresztmetszetei lehetnek.
- **A tanúsítványokra vonatkozó követelmények.** Fontos, hogy az összes [szükséges tanúsítvány](../operator/azure-stack-pki-certs.md) elérhető legyen, mielőtt egy helyszíni mérnök megérkezik az adatközpontba üzembe helyezéshez.

Miután az összes előfeltételként szükséges információt összegyűjtötte az üzembe helyezési munkalapon, a Microsoft gondoskodik arról, hogy minden ellenőrzési eszközt futtasson, és segítséget nyújtson az esetlegesen felmerülő további kérdésekhez.

## <a name="onsite-deployment"></a>Helyszíni üzembe helyezés

Azure Stack hub üzembe helyezéséhez a Microsoft központi telepítési mérnöke fog megjelenni a telepítés elindításához. A helyszíni üzembe helyezés során is szükség van egy hálózati mérnökre a szervezettől.

A következő ellenőrzéseket kell elvárnia a helyszíni mérnöktől az üzembe helyezési élményben:

- Testetlen és leltári hardver.
- Az áramellátás és a teljesítmény összekapcsolása a megoldással.
- A fizikai hardver állapotának ellenőrzése.
- Ellenőrizze az összes kábelezést és a szegélyek kapcsolatát, és győződjön meg arról, hogy a megoldás megfelelően össze van szerelve, és megfelel az igényeinek.
- Konfigurálja a megoldás hardveres életciklus-gazdagépét (HLH).
- Integrálja az adatközpont hálózatát.
- Győződjön meg arról, hogy az összes fizikai hardverkonfiguráció helyes.
- Győződjön meg arról, hogy az összes összetevő belső vezérlőprogramja a megoldás legújabb jóváhagyott verziója.
- Indítsa el a telepítést.

## <a name="post-deployment"></a>Üzembe helyezés utáni

A megoldásnak a felhasználónak való átadása előtt több lépést kell végrehajtania a Microsoft üzembehelyezési mérnökének. Ebben a fázisban az érvényesítés fontos annak biztosítása érdekében, hogy a rendszer üzembe helyezése és megfelelő végrehajtása sikeres legyen.

A Microsoft Deployment Engineer által végrehajtandó műveletek:

- Érték engedélyezése – erőforrás-szolgáltatók hozzáadása.
- Futtassa a [test-azurestack](../operator/azure-stack-diagnostic-test.md).
- [Regisztráljon](../operator/azure-stack-registration-role.md) az Azure-ban.
- Győződjön meg arról, [Azure stack hub Marketplace Syndication](../operator/azure-stack-marketplace.md).
- A kapcsolók konfigurációjának és a HLH konfigurációs fájljainak biztonsági mentése.
- Készítse elő az ügyfél összegzését az üzembe helyezéshez.
- A [frissítések ellenőrzésével](../operator/azure-stack-updates.md) győződjön meg arról, hogy a megoldás szoftvere frissítve lett a legújabb verzióra.

## <a name="next-steps"></a>További lépések

[A moduláris adatközpontok üzembe helyezésének áttekintése](deployment-overview.md)

