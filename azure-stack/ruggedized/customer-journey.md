---
title: Vásárlói utazás a vásárlástól a Azure Stack hub üzembe helyezése után | Microsoft Docs
description: Megtudhatja, mire számíthat a Microsofttól robusztus Azure Stack hub sikeres helyszíni üzembe helyezésének megtervezése az üzembe helyezés után.
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
ms.openlocfilehash: f34a449490ab7b57beacc942584f9616fd6d543c
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598317"
---
# <a name="customer-journey"></a>Ügyfél utazása

Ez a cikk a Azure Stack hub Datacenter integrációjának teljes körű folyamatát ismerteti az üzembe helyezés után. Az integráció egy együttműködési projekt az ügyfél és a Microsoft között. A következő szakaszok a projekt ütemtervének különböző fázisait és a projekt tagjainak konkrét lépéseit fedik le.

## <a name="introduction"></a>Bevezetés

Az alábbi táblázat az üzembe helyezés különböző szakaszaiban várhatóan megjelenő tudnivalókat mutatja be.

|   |Megrendelés folyamata  |Üzembe helyezés előtti |Integráció, ellenőrzés, szállítás |Helyszíni üzembe helyezés  |Telepítés utáni |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
| **Microsoft** |-Az USA-beli kézbesítési jel<br>-Azure Stack hub strapabíró: 10 nap |Az adatközpont-követelmények összegyűjtéséhez szükséges eszközök és dokumentáció megadása  |– Konfigurációs összetevők ellenőrzése és az érvényesítési eredmények ellenőrzése<br>– Hardverek kézbesítésének biztosítása  |-Rack és stack<br>– Hálózati integráció<br>– Azure Stack hub üzembe helyezése<br>-Kikapcsolás az ügyfélnek    |Regisztráció és Marketplace Syndication|
| **Ügyfél** |Jelek vásárlása    |-Kitölti a hálózat részleteit a telepítési munkalapon<br>– Tanúsítványok gyűjtése<br>– Azure AD-fiókok beszerzése<br>-A megadott érvényesítési eszközök futtatása    |Győződjön meg arról, hogy a hely készen áll a hálózatra, a tápellátásra, a hűtés előfeltételeire    |-Készüljön fel az üzembe helyezési konfigurációs összetevőkre<br>-Az ügyfél hálózati mérnöke elérhető   |     |


## <a name="order-process"></a>Megrendelés folyamata

A szervezet a Microsofttal együttműködve rendelést rendel a lefoglalt számú rendszerhez. Miután elvégezte a rendelést, a Microsoft 10 nap múlva továbbítja az Azure Stack hub-t az USA-beli helyre. A Microsoft gondoskodik arról, hogy minden biztonságos ellátási láncra vonatkozó követelmény teljesülni fog. 

>[!NOTE] 
>A számlázás a hardver elszállítása után 14 nappal kezdődik.


Azure Stack hub-erőforrás létrehozásához hajtsa végre a következő lépéseket a Azure Portal.

1. A Microsoft Azure hitelesítő adataival jelentkezzen be a Azure Portalba ezen az URL-címen: [https://portal.azure.com](https://portal.azure.com) .
1. A bal oldali panelen válassza az **+ erőforrás létrehozása** lehetőséget. Keresse meg és válassza ki **Azure stack hub robusztus**. Válassza a **Létrehozás** lehetőséget.
1. Válassza ki az Azure Stack hub-eszközhöz használni kívánt előfizetést. Válassza ki azt az országot, ahová a fizikai eszközt el szeretné szállítani. Válassza az **eszközök megjelenítése** lehetőséget.
1. Megjelenik egy rövid űrlap. Töltse ki az űrlapot, és válassza a **Küldés** lehetőséget. A Microsoft engedélyezi az előfizetést.
1. Az előfizetés engedélyezése után képesnek kell lennie az erőforrás-létrehozás folytatására. Az **eszköz típusának kiválasztása** panelen válassza a **kiválasztás** lehetőséget. 
1. Az **alapvető beállítások** lapon adja meg vagy válassza ki a következő **projekt részleteit**.
    
    |Beállítás  |Érték  |
    |---------|---------|
    |Előfizetés    |Ezt a rendszer automatikusan kitölti a korábbi kiválasztás alapján. A számlázási fiókhoz társított előfizetés. |
    |Erőforráscsoport  |Válasszon ki egy meglévő csoportot, vagy hozzon létre egy újat.   |

1. Adja meg vagy válassza ki a következő **példány részleteit**.

    |Beállítás  |Érték  |
    |---------|---------|
    |Név   | Az erőforrást azonosító valódi név.<br>A névnek 2–50 karakter hosszúságúnak kell lennie, és csak betűket, számokat, illetve kötőjelet tartalmazhat.<br> A névnek betűvel vagy számmal kell kezdődnie és végződnie.        |
    |Régió     |Az Azure Stack hub-erőforrást tartalmazó régiók listájának megtekintéséhez tekintse meg a régiók [által elérhető Azure-termékeket](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Azure Government használata esetén az összes kormányzati régió elérhető az [Azure-régiókban](https://azure.microsoft.com/global-infrastructure/regions/)látható módon.<br> Az eszköz üzembe helyezésének földrajzi régiójához legközelebb eső helyet válasszon.|


1. Válassza a **Next (tovább): szállítási címet**.

    - Ha már rendelkezik egy eszközzel, válassza az **Azure stack hub-eszközhöz** tartozó kombinált listát.

    - Ha ez az új eszköz, amelyet Ön megrendelt, adja meg a kapcsolattartó nevét, a vállalatot, az eszköz szállítását és a kapcsolattartási adatokat.

1. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre.
1. A **felülvizsgálat + létrehozás** lapon tekintse át a **díjszabás részleteit**, **használati feltételek** és az erőforrás részleteit. Válassza ki az **adatvédelmi feltételeket áttekintő** kombinált listát.
1. Válassza a **Létrehozás** lehetőséget.

Az erőforrás létrehozása néhány percet vesz igénybe. Az erőforrás sikeres létrehozása és üzembe helyezése után értesítést kap. Válassza az **Erőforrás megnyitása** lehetőséget.

A megrendelés elhelyezése után a Microsoft áttekinti a rendelést, és elküldi Önt (e-mailben) a szállítási adatokkal.

## <a name="pre-deployment"></a>Üzembe helyezés előtti

Ön dönti el, hogyan integrálhatja Azure Stack hub-t az adatközpontba. A Microsoft közzétett egy [üzembe helyezési munkalapot](../operator/azure-stack-deployment-worksheet.md) , amely végigvezeti Önt az adatközpontba való sikeres integráláshoz szükséges összes szükséges információ összegyűjtésén. Emellett bizonyos tanúsítványokra van szükség az üzembe helyezéskor. A tanúsítványok beszerzésének megkönnyítéséhez a Microsoft az [Azure stack hub Readiness-ellenőrző](../operator/azure-stack-validation-report.md)eszközének nevezett eszközt nyújt. Ez az eszköz segít létrehozni a belső HITELESÍTÉSSZOLGÁLTATÓ számára elérhető tanúsítvány-aláírási kérelmeket (munkatársakat). 

>[!Important]
>A rendszer az összes előfeltételt érvényesíti, hogy megakadályozza az üzembe helyezés késését. Az Előfeltételek ellenőrzése időt vehet igénybe, és megkövetelheti a szervezeten belüli különböző részlegek koordinálását és az adatok gyűjtését.

A következő elemek közül választhat:

- **Azure Stack hub-kapcsolatok modelljének és identitás-szolgáltatójának.** Dönthet úgy, hogy Azure Stack hubot [az internethez (és az Azure-hoz) csatlakozik,](../operator/azure-stack-connected-deployment.md) vagy [le van választva](../operator/azure-stack-disconnected-deployment.md). A Azure Stack hub előnyeinek kihasználása érdekében, beleértve a hibrid forgatókönyveket is, az Azure-hoz való csatlakozást szeretné telepíteni. Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy Azure Active Directory (Azure AD) kiválasztása egy egyszeri döntés, amelyet a telepítéskor kell elvégeznie. **Az identitás-szolgáltatót később nem módosíthatja a teljes rendszer újbóli üzembe helyezése nélkül.**
- **Hálózati integráció.** A [hálózati integráció](../operator/azure-stack-network.md) elengedhetetlen a Azure stack hub rendszerek üzembe helyezéséhez, működtetéséhez és felügyeletéhez. Számos szempontot figyelembe kell venni, hogy az Azure Stack hub-megoldás rugalmas, és a működésének támogatásához egy magasan elérhető fizikai infrastruktúrát biztosít.
- **Tűzfal-integráció.** Javasoljuk, hogy a Azure Stack hub biztonságossá tétele érdekében [Használjon tűzfalat](../operator/azure-stack-firewall.md) . A tűzfalak segítenek megakadályozni a DDOS-támadásokat, a behatolás észlelését és a tartalom vizsgálatát. Azt azonban érdemes megjegyezni, hogy az Azure Storage-szolgáltatások számára az átviteli sebesség szűk keresztmetszete lehet.
- **A tanúsítványokra vonatkozó követelmények.** Fontos, hogy az összes [szükséges tanúsítvány](../operator/azure-stack-pki-certs.md) elérhető legyen, mielőtt egy helyszíni mérnök megérkezik az adatközpontba üzembe helyezéshez.

Ha az összes előfeltételként szükséges információt az üzembe helyezési munkalapon gyűjti össze, a Microsoft gondoskodik arról, hogy minden ellenőrzési eszközt futtasson, és segítsen az esetlegesen felmerülő további kérdésekhez. 

## <a name="hardware-delivery"></a>Hardveres kézbesítés

A Microsoft Önnel együttműködve gondoskodik arról, hogy az összes szükséges hardver az Egyesült államokbeli helyre jusson a megadott időn belül.  

**Rendkívül fontos** , hogy az előfeltételként szükséges összes érték zárolva legyen és elérhető legyen, *mielőtt a helyszíni Microsoft Deployment mérnök megérkezik a megoldás üzembe helyezéséhez.*

- Az üzembe helyezési munkalap minden adattal kitöltött. 
- Minden tanúsítványt érvényesíteni kell, és készen kell állnia.
- A régió nevét meg kell határozni.
- Minden hálózati integrációs paraméter véglegesítve lett.

>[!Tip]
>Ha bármelyik információ módosult, ügyeljen arra, hogy működjön együtt a belső szervezettel annak biztosítása érdekében, hogy az adatok a helyszíni üzembehelyezési mérnök megérkezése előtt frissüljenek. Ez megakadályozza a telepítési folyamat késéseit.

## <a name="onsite-deployment"></a>Helyszíni üzembe helyezés

Azure Stack hub üzembe helyezéséhez a Microsoft központi telepítési mérnöke fog megjelenni a telepítés elindításához. A helyszíni üzembe helyezés időtartama alatt a szervezet hálózati mérnöke is elérhetővé válik.

A következő ellenőrzéseket kell elvárnia a helyszíni mérnöktől az üzembe helyezési élményben:

- A hardver kicsomagolása és leltározása
- Az áramellátás és a megoldás csatlakoztatása
- A fizikai hardver állapotának ellenőrzése
- Ellenőrizze az összes kábelezést és a szegélyek kapcsolatát, és győződjön meg arról, hogy a megoldás megfelelően össze van szerelve, és megfelel az igényeinek.
- Konfigurálja a megoldás HLH (hardver életciklus-gazdagép).
- Datacenter hálózati integráció
- Győződjön meg arról, hogy az összes fizikai hardverkonfiguráció helyes.
- Győződjön meg arról, hogy az összes összetevő belső vezérlőprogramja a megoldás legújabb jóváhagyott verziója.
- Indítsa el a telepítést. 

## <a name="post-deployment"></a>Üzembe helyezés utáni

A megoldásnak a felhasználónak való átadása előtt több lépést kell végrehajtania a Microsoft üzembehelyezési mérnökének. Ebben a fázisban az érvényesítés fontos annak biztosítása érdekében, hogy a rendszer üzembe helyezése és megfelelő végrehajtása sikeres legyen.

A Microsoft Deployment Engineer által végrehajtandó műveletek:

- Érték engedélyezése – erőforrás-szolgáltatók hozzáadása (RPs).
- Futtassa a [test-azurestack](../operator/azure-stack-diagnostic-test.md).
- [Regisztráció](../operator/azure-stack-registration-role.md) az Azure-ban.
- [Piactéri hírszolgáltatás](../operator/azure-stack-marketplace.md).
- A Backup kapcsoló konfigurációs és HLH konfigurációs fájljai.
- Készítse elő az ügyfél összegzését az üzembe helyezéshez.
- A [frissítések ellenőrzésével](../operator/azure-stack-updates.md) győződjön meg arról, hogy a megoldás szoftvere frissítve lett a legújabb verzióra.

## <a name="next-steps"></a>További lépések

További információ a [Microsoft Azure stack hub robusztus telepítésének és konfigurálásának lépéseiről](deployment-overview.md).

