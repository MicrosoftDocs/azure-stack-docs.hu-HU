---
title: A Event Hubs telepítése Azure Stack hubhoz
description: Ismerje meg, hogyan telepítheti a Event Hubs erőforrás-szolgáltatót az Azure Stack hub-ra.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/02/2020
ms.reviewer: jfggdl
ms.lastreviewed: 09/02/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 975ab63a6af9f895f01d4607f998bca9fc52cebf
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343818"
---
# <a name="how-to-install-event-hubs-on-azure-stack-hub"></a>A Event Hubs telepítése Azure Stack hubhoz

Ez a cikk bemutatja, hogyan töltheti le és telepítheti az Event Hubs erőforrás-szolgáltatót, így elérhetővé teheti az ügyfelek számára az előfizetést.

## <a name="download-packages"></a>Csomagok letöltése

Mielőtt telepítené a Event Hubst a Azure Stack hubhoz, le kell töltenie az erőforrás-szolgáltatót és annak függő csomagjait a Marketplace felügyeleti szolgáltatás használatával. Ha nem ismeri a piactér felügyeletét, időt kell fordítania [a piactér-elemek letöltésére az Azure-ból, és közzé kell tennie a Azure stack hub](azure-stack-download-azure-marketplace-item.md)-ban. Ez a szakasz végigvezeti az elemek Azure piactéren való letöltésének folyamatán. 

> [!NOTE]
> A letöltési folyamat akár 30 percet is igénybe vehet, attól függően, hogy milyen hálózati késést és meglévő csomagokat használ a Azure Stack hub-példányon. 

::: zone pivot="state-connected"
A csatlakoztatott forgatókönyvek esetében közvetlenül az Azure Marketplace-ről töltheti le az elemeket az Azure Stack hub Marketplace-re:

1. Jelentkezzen be az Azure Stack Hub felügyeleti portálra.
2. Válassza a **piactér-kezelés** lehetőséget a bal oldalon.
3. Válassza ki az **Erőforrás-szolgáltatók** elemet.
4. Válassza **a + Hozzáadás az Azure-ból** lehetőséget.
5. Keressen rá a "Event Hubs" kifejezésre a keresősáv használatával.
6. Válassza ki a "Event Hubs" sort a keresési eredmények között. 
7. A "Event Hubs" letöltési oldalon válassza ki a telepíteni kívánt Event Hubs verziót, majd kattintson a lap alján található **Letöltés** gombra. 
   [![Marketplace felügyeleti csomagok](media/event-hubs-rp-install/1-marketplace-management-download.png)](media/event-hubs-rp-install/1-marketplace-management-download.png#lightbox)

Figyelje meg, hogy a további szoftvercsomagok a Event Hubs együtt tölthetők le, beleértve a következőket:

- Microsoft Azure Stack hub Add-On RP Windows Server csak belső
- PowerShell kívánt állapotának konfigurálása
::: zone-end

::: zone pivot="state-disconnected"
Leválasztott vagy részben csatlakoztatott forgatókönyv esetén a csomagokat a helyi gépre tölti le, majd importálja őket a Azure Stack hub Marketplace-re:

1. Ha még nem tette meg, kövesse a [Marketplace-elemek letöltése – leválasztott vagy részben csatlakoztatott forgatókönyvek](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected)című témakör utasításait. Itt letöltheti és futtathatja a Marketplace Syndication eszközt, amely lehetővé teszi a Event Hubs csomagok letöltését.
2. Miután a szindikált eszköz "Azure Marketplace-elemek" ablaka megnyílik, keresse meg és válassza a "Event Hubs" lehetőséget a szükséges csomagok letöltéséhez a helyi gépre.
3. A letöltés befejeződése után importálja a csomagokat a Azure Stack hub-példányba, és tegye közzé a piactéren. 
::: zone-end

## <a name="installation"></a>Telepítés 

1. Ha még nem tette meg, jelentkezzen be az Azure Stack hub felügyeleti portálra, válassza a bal oldali **piactér-kezelés** elemet, és válassza az **erőforrás-szolgáltatók** lehetőséget.
2. A Event Hubs és az egyéb szükséges szoftverek letöltése után a **piactér-kezelés** a "nincs telepítve" állapotú "Event Hubs" csomagokat jeleníti meg. Előfordulhat, hogy más csomagok is a "letöltött" állapotot jelenítik meg. Válassza ki a telepíteni kívánt "Event Hubs" sort.
   [![Marketplace-kezelő letöltött csomagjai](media/event-hubs-rp-install/2-marketplace-management-downloaded.png)](media/event-hubs-rp-install/2-marketplace-management-downloaded.png#lightbox)
 
3. A Event Hubs telepítési csomag lapon egy kék szalagcím jelenik meg az egész tetején. A Event Hubs telepítésének megkezdéséhez kattintson a szalagcímre.
   [![Piactér-felügyeleti esemény hubok – telepítés megkezdése](media/event-hubs-rp-install/3-marketplace-management-install-ready.png)](media/event-hubs-rp-install/3-marketplace-management-install-ready.png#lightbox)

### <a name="install-prerequisites"></a>Az előfeltételek telepítése

1. Ezután a telepítés lapra kerül át. A telepítési folyamat megkezdéséhez válassza az **Előfeltételek telepítése** lehetőséget.
   ![Piactér-felügyeleti esemény hubok – Előfeltételek telepítése](media/event-hubs-rp-install/4-marketplace-management-install-prereqs-start.png)
 
2. Várjon, amíg az Előfeltételek telepítése sikeres lesz. A következő lépés végrehajtása előtt egy zöld pipa jelenik meg a **telepítés előfeltételeinek telepítése** előtt.

   ![Piactér-felügyeleti esemény-hubok – az Előfeltételek telepítése sikerült](media/event-hubs-rp-install/5-marketplace-management-install-prereqs-succeeded.png)

### <a name="prepare-secrets"></a>Titkok előkészítése 

1. A **2 alatt. Készítse el a titkokat** lépést, válassza a **tanúsítvány hozzáadása** lehetőséget, és megjelenik a **tanúsítvány hozzáadása** panel.
   ![Piactér-felügyeleti esemény-hubok – titkok előkészítése](media/event-hubs-rp-install/6-marketplace-management-install-prepare-secrets.png)

2. A tanúsítvány **hozzáadása** lapon kattintson a Tallózás gombra a tanúsítvány neve mező jobb oldalán.
3. Válassza ki az előfeltételek teljesítése során beszerzett. pfx tanúsítványfájl-fájlt. További információ: [a telepítés előfeltételei](event-hubs-rp-prerequisites.md). 

4. Adja meg az SSL-tanúsítvány Event Hubs biztonságos karakterlánc létrehozásához megadott jelszót. Ezután válassza a **Hozzáadás** elemet.
   ![Piactér-felügyeleti esemény hubok – tanúsítvány hozzáadása](media/event-hubs-rp-install/7-marketplace-management-install-prepare-secrets-add-cert.png)

### <a name="install-resource-provider"></a>Erőforrás-szolgáltató telepítése

1. Ha a tanúsítvány telepítése sikeres, egy zöld pipa jelenik meg a **titkok előkészítése** elem mellett, mielőtt továbblép a következő lépésre. Most kattintson a **telepítés** gombra a **3. erőforrás-szolgáltató telepítése** lehetőség mellett.
   ![Piactér-felügyeleti esemény hubok – az RP telepítésének megkezdése](media/event-hubs-rp-install/8-marketplace-management-install-start.png)
 
2. Ezután a következő oldal jelenik meg, amely azt jelzi, hogy Event Hubs erőforrás-szolgáltató van telepítve.
   [![Piactér-felügyeleti esemény hubok – RP telepítése](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png)](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png#lightbox)
 
3. Várjon, amíg a telepítés befejeződött. Ez a folyamat általában egy vagy több órát vesz igénybe a Azure Stack hub típusától függően. 
   [![Piactér-felügyeleti esemény hubok – az RP telepítése befejeződött](media/event-hubs-rp-install/10-marketplace-management-install-complete.png)](media/event-hubs-rp-install/10-marketplace-management-install-complete.png#lightbox)

4. Ellenőrizze, hogy a Event Hubs telepítése sikeres volt-e, és térjen vissza a **piactér-kezelés**, erőforrás- **szolgáltatók** lapra. Event Hubs állapotának a "telepített" értéknek kell megjelennie.
   ![Piactér-felügyeleti esemény hubok elérhetők](media/event-hubs-rp-install/11-marketplace-management-rps-installed.png)

## <a name="next-steps"></a>Következő lépések

Mielőtt a felhasználók üzembe helyezhetik Event Hubs erőforrásait, létre kell hoznia egy vagy több csomagot, ajánlatot és előfizetést. 

- Ha első alkalommal nyújt szolgáltatást, kezdje az [ajánlati szolgáltatások a felhasználók számára](tutorial-offer-services.md) oktatóanyaggal. Ezután folytassa a következő oktatóanyaggal, [tesztelje a szolgáltatási ajánlatot](tutorial-test-offer.md).
- Ha már ismeri a szolgáltatás nyújtásának koncepcióját, hozzon létre egy ajánlatot és egy csomagot, amely tartalmazza a Event Hubs erőforrás-szolgáltatót. Ezután hozzon létre egy előfizetést a felhasználók számára, vagy adja meg nekik az ajánlat adatait, hogy saját maguk is létrehozhatnak. A hivatkozásokat követve a [szolgáltatás, a terv, az ajánlat és az előfizetés áttekintésében](service-plan-offer-subscription-overview.md)is elvégezheti a cikkek sorát.

A frissítések kereséséhez [Azure stack Hub Event Hubs frissítése](resource-provider-apply-updates.md).

Ha el kell távolítania az erőforrás-szolgáltatót, olvassa el [a Event Hubs erőforrás-szolgáltató eltávolítása](event-hubs-rp-remove.md) című témakört.

Ha többet szeretne megtudni a felhasználói élményről, látogasson el a [Event Hubs Azure stack hub áttekintése című témakörben](../user/event-hubs-overview.md) a felhasználói dokumentumokban.