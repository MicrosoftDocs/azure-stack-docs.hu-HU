---
title: Vásárlói utazás a vásárlástól a Azure Stack hub üzembe helyezése után | Microsoft Docs
description: Megtudhatja, mire számíthat a moduláris adatközpontok (MDC-EK) sikeres helyszíni üzembe helyezésének megtervezése és a telepítés utáni üzembe helyezése között.
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
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 11/04/2020
ms.openlocfilehash: 12631a118600e67611294c87636cd2fa62bfaaea
ms.sourcegitcommit: ecd98662194d2cdb15c22f8b1f99812fc5f4c15a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93344779"
---
# <a name="modular-data-center-integration-overview"></a>Moduláris adatközpont-integráció – áttekintés

Ez a cikk a MDC-integráció teljes körű folyamatát ismerteti az üzembe helyezés után. Az integráció egy együttműködési projekt az ügyfél és a Microsoft között. A következő szakaszok a projekt ütemtervének különböző fázisait és a projekt tagjainak konkrét lépéseit fedik le.

## <a name="introduction"></a>Bevezetés

A következő táblázat felsorolja, hogy mi várható az üzembe helyezés különböző fázisaiban.

|   |Megrendelés folyamata  |Üzembe helyezés előtti |Integráció, ellenőrzés, szállítás |Helyszíni üzembe helyezés  |Telepítés utáni |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
|Microsoft  |-Az USA-beli kézbesítési jel    |Az adatközpont-követelmények összegyűjtéséhez szükséges eszközök és dokumentáció megadása  |– Konfigurációs összetevők ellenőrzése és az érvényesítési eredmények ellenőrzése<br>– Hardverek kézbesítésének biztosítása  |-Rack és stack<br>– Hálózati integráció<br>– Azure Stack hub üzembe helyezése<br>-Kikapcsolás az ügyfélnek    |Regisztráció és Marketplace Syndication|
|Ügyfél   |Jelek vásárlása   |-Kitölti a hálózat részleteit a telepítési munkalapon<br>– Tanúsítványok gyűjtése<br>– Azure AD-fiókok beszerzése<br>-A megadott érvényesítési eszközök futtatása    |Győződjön meg arról, hogy a hely készen áll a hálózatra, a tápellátásra, a hűtés előfeltételeire    |-Készüljön fel az üzembe helyezési konfigurációs összetevőkre<br>-Az ügyfél hálózati mérnöke elérhető   |     |


## <a name="order-process"></a>Megrendelés folyamata

A szervezet a Microsofttal együttműködve rendelést rendel a lefoglalt számú rendszerhez. Miután elvégezte a rendelést, a Microsoft továbbítja a MDC az USA-beli helyre. A Microsoft gondoskodik arról, hogy minden biztonságos ellátási láncra vonatkozó követelmény teljesülni fog. 

## <a name="hardware-delivery"></a>Hardveres kézbesítés

A Microsoft Önnel együttműködve gondoskodik arról, hogy az összes szükséges hardver az Egyesült államokbeli helyre jusson a megadott időn belül.  

**Rendkívül fontos** , hogy az előfeltételként szükséges összes érték zárolva legyen és elérhető legyen, *mielőtt a helyszíni Microsoft Deployment mérnök megérkezik a megoldás üzembe helyezéséhez.*

- Az üzembe helyezési munkalap minden adattal kitöltött. 
- Minden tanúsítványt érvényesíteni kell, és készen kell állnia.
- A régió nevét meg kell határozni.
- Minden hálózati integrációs paraméter véglegesítve lett.

>[!Tip]
>Ha bármelyik információ módosult, ügyeljen arra, hogy működjön együtt a belső szervezettel annak biztosítása érdekében, hogy az adatok a helyszíni üzembehelyezési mérnök megérkezése előtt frissüljenek. Ez megakadályozza a telepítési folyamat késéseit.

## <a name="pre-deployment"></a>Központi telepítés előtti

Ön dönti el, hogyan integrálhatja Azure Stack hub-t az adatközpontba. A Microsoft közzétett egy [üzembe helyezési munkalapot](../operator/azure-stack-deployment-worksheet.md) , amely végigvezeti Önt az adatközpontba való sikeres integráláshoz szükséges összes szükséges információ összegyűjtésén. Emellett bizonyos tanúsítványokra van szükség az üzembe helyezéskor. A tanúsítványok beszerzésének megkönnyítéséhez a Microsoft az [Azure stack hub Readiness-ellenőrző](../operator/azure-stack-validation-report.md)eszközének nevezett eszközt nyújt. Ez az eszköz segít létrehozni a belső HITELESÍTÉSSZOLGÁLTATÓ számára elérhető tanúsítvány-aláírási kérelmeket (munkatársakat). 

>[!Important]
>A rendszer az összes előfeltételt érvényesíti, hogy megakadályozza az üzembe helyezés késését. Az Előfeltételek ellenőrzése időt vehet igénybe, és megkövetelheti a szervezeten belüli különböző részlegek koordinálását és az adatok gyűjtését.

A következő elemek közül választhat:

- **Azure Stack hub-kapcsolatok modelljének és identitás-szolgáltatójának.** Dönthet úgy, hogy Azure Stack hubot [az internethez (és az Azure-hoz) csatlakozik,](../operator/azure-stack-connected-deployment.md) vagy [le van választva](../operator/azure-stack-disconnected-deployment.md). A Azure Stack hub előnyeinek kihasználása érdekében, beleértve a hibrid forgatókönyveket is, az Azure-hoz való csatlakozást szeretné telepíteni. Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy Azure Active Directory (Azure AD) kiválasztása egy egyszeri döntés, amelyet a telepítéskor kell elvégeznie. **Az identitás-szolgáltatót később nem módosíthatja a teljes rendszer újbóli üzembe helyezése nélkül.**
- **Hálózati integráció.** A [hálózati integráció](../operator/azure-stack-network.md) elengedhetetlen a Azure stack hub rendszerek üzembe helyezéséhez, működtetéséhez és felügyeletéhez. Számos szempontot figyelembe kell venni, hogy az Azure Stack hub-megoldás rugalmas, és a működésének támogatásához egy magasan elérhető fizikai infrastruktúrát biztosít.
- **Tűzfal-integráció.** Javasoljuk, hogy a Azure Stack hub biztonságossá tétele érdekében [Használjon tűzfalat](../operator/azure-stack-firewall.md) . A tűzfalak segítenek megakadályozni a DDOS-támadásokat, a behatolás észlelését és a tartalom vizsgálatát. Azt azonban érdemes megjegyezni, hogy az Azure Storage-szolgáltatások számára az átviteli sebesség szűk keresztmetszete lehet.
- **A tanúsítványokra vonatkozó követelmények.** Fontos, hogy az összes [szükséges tanúsítvány](../operator/azure-stack-pki-certs.md) elérhető legyen, mielőtt egy helyszíni mérnök megérkezik az adatközpontba üzembe helyezéshez.

Ha az összes előfeltételként szükséges információt az üzembe helyezési munkalapon gyűjti össze, a Microsoft gondoskodik arról, hogy minden ellenőrzési eszközt futtasson, és segítsen az esetlegesen felmerülő további kérdésekhez. 

## <a name="onsite-deployment"></a>Helyszíni üzembe helyezés

Azure Stack hub üzembe helyezéséhez a Microsoft központi telepítési mérnöke fog megjelenni a telepítés elindításához. A helyszíni üzembe helyezés időtartama alatt a szervezet hálózati mérnöke is elérhetővé válik.

A következő ellenőrzéseket kell elvárnia a helyszíni mérnöktől az üzembe helyezési élményben:

- A hardver kicsomagolása és leltározása
- Az áramellátás és a megoldás csatlakoztatása
- A fizikai hardver állapotának ellenőrzése
- Ellenőrizze az összes kábelezést és a szegélyek kapcsolatát, és győződjön meg arról, hogy a megoldás megfelelően van összekapcsolva, és megfelel az igényeinek
- A megoldás HLH konfigurálása (hardver életciklus-állomása)
- Datacenter hálózati integráció
- Győződjön meg arról, hogy az összes fizikai hardverkonfiguráció helyes
- Győződjön meg arról, hogy az összes összetevő belső vezérlőprogramja a megoldás legújabb jóváhagyott verziója.
- Az üzembe helyezés elindítása

## <a name="post-deployment"></a>Üzembe helyezés után

A megoldásnak a felhasználónak való átadása előtt több lépést kell végrehajtania a Microsoft üzembehelyezési mérnökének. Ebben a fázisban az érvényesítés fontos annak biztosítása érdekében, hogy a rendszer üzembe helyezése és megfelelő végrehajtása sikeres legyen.

A Microsoft Deployment Engineer által végrehajtandó műveletek:

- Érték engedélyezése – erőforrás-szolgáltatók hozzáadása (RPs)
- [Teszt futtatása – azurestack](../operator/azure-stack-diagnostic-test.md)
- [Regisztrálás](../operator/azure-stack-registration-role.md) az Azure-ban
- [Piactéri hírszolgáltatás](../operator/azure-stack-marketplace.md)
- A Backup kapcsoló konfigurációs és HLH konfigurációs fájljai
- Ügyfél-összefoglalás előkészítése az üzembe helyezéshez
- A [frissítések ellenőrzése](../operator/azure-stack-updates.md) annak biztosításához, hogy a megoldás szoftvere frissítve legyen a legújabb verzióra

## <a name="next-steps"></a>Következő lépések

A [moduláris adatközpontok üzembe helyezésének áttekintése](deployment-overview.md).

