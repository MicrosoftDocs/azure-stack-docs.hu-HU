---
title: A Event Hubs telepítése Azure Stack hubhoz
description: Ismerje meg, hogyan telepítheti a Event Hubs erőforrás-szolgáltatót az Azure Stack hub-ra.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e07d311c8edbe140834a020af489ae49d8380d86
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423970"
---
# <a name="how-to-install-event-hubs-on-azure-stack-hub"></a>A Event Hubs telepítése Azure Stack hubhoz

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Ez a cikk bemutatja, hogyan töltheti le és telepítheti az Event Hubs erőforrás-szolgáltatót, így elérhetővé teheti az ügyfelek számára az előfizetést.

## <a name="download-packages"></a>Csomagok letöltése

Mielőtt telepítené a Event Hubst a Azure Stack hubhoz, le kell töltenie az erőforrás-szolgáltatót és annak függő csomagjait. A helyzettől vagy a követelményektől függően két lehetőség közül választhat:

- Event Hubs letöltése csatlakoztatott forgatókönyvben.
- Event Hubs letöltése leválasztott vagy részben csatlakoztatott forgatókönyvben.

Ha nem ismeri az Azure Stack hub felügyeleti portál **piactér-felügyeleti** szolgáltatását, akkor időt kell fordítania a Marketplace- [elemek letöltésére az Azure-ból, és közzé kell tennie az Azure stack hub](azure-stack-download-azure-marketplace-item.md)-ban. Ez a cikk végigvezeti az Azure-beli elemek az Azure Stack hub piactéren való letöltésének folyamatán. A csatlakoztatott és a leválasztott forgatókönyvekre egyaránt vonatkozik. 

### <a name="download-event-hubs---connected-scenario"></a>Event Hubs csatlakoztatott forgatókönyv letöltése

> [!NOTE]
> A letöltési folyamat akár 30 percet is igénybe vehet, attól függően, hogy milyen hálózati késést és meglévő csomagokat használ a Azure Stack hub-példányon. 

Kövesse ezeket az utasításokat, ha az Azure Stack hub internetkapcsolattal rendelkezik:

1. Jelentkezzen be az Azure Stack hub felügyeleti portálján.
2. Válassza a **piactér-kezelés** lehetőséget a bal oldalon.
3. Válassza ki az **Erőforrás-szolgáltatók** elemet.
4. Válassza **a + Hozzáadás az Azure-ból**lehetőséget.
5. Keressen rá a "Event Hubs" kifejezésre a keresősáv használatával.
6. Válassza ki a "Event Hubs" sort a keresési eredmények között. 
7. A "Event Hubs" letöltési oldalon válassza ki a telepíteni kívánt Event Hubs verziót, majd kattintson a lap alján található **Letöltés** gombra. 
   [![Marketplace felügyeleti csomagok](media/event-hubs-rp-install/1-marketplace-management-download.png)](media/event-hubs-rp-install/1-marketplace-management-download.png#lightbox)

Figyelje meg, hogy a további szoftvercsomagok a Event Hubs együtt tölthetők le, beleértve a következőket:

- Microsoft Azure Stack hub-bővítmény RP Windows Server csak belső
- PowerShell Desired State Configuration

A letöltési folyamat befejezése után ugorjon az [Előfeltételek telepítése szakaszra](#install-prerequisites).

### <a name="download-event-hubs---disconnected-or-partially-connected-scenario"></a>Event Hubs letöltése – leválasztott vagy részben csatlakoztatott forgatókönyv

Először töltse le a csomagokat a helyi gépre, majd importálja őket a Azure Stack hub-példányba.

1. Ha még nem tette meg, kövesse a [Marketplace-elemek letöltése – leválasztott vagy részben csatlakoztatott forgatókönyvek](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)című témakör utasításait. Itt letöltheti és futtathatja a Marketplace Syndication eszközt, amely lehetővé teszi a Event Hubs csomagok letöltését.
2. Miután a szindikált eszköz "Azure Marketplace-elemek" ablaka megnyílik, keresse meg és válassza a "Event Hubs" lehetőséget a szükséges csomagok letöltéséhez a helyi gépre.
3. A letöltés befejeződése után importálja a csomagokat a Azure Stack hub-példányba, és tegye közzé a piactéren. 

## <a name="installation"></a>Telepítés 

1. Ha még nem tette meg, jelentkezzen be az Azure Stack hub felügyeleti portálján.
2. Válassza ki a **piactér-felügyelet** elemet a bal oldalon, majd válassza az **erőforrás-szolgáltatók**lehetőséget.
3. A Event Hubs és az egyéb szükséges szoftverek letöltése után a **piactér-kezelésnek** a "nincs telepítve" állapotú "Event Hubs" csomagokat kell megjelenítenie. Előfordulhat, hogy más csomagok is a "letöltött" állapotot jelenítik meg. Válassza ki a telepíteni kívánt "Event Hubs" sort.
   [![Marketplace-kezelő letöltött csomagjai](media/event-hubs-rp-install/2-marketplace-management-downloaded.png)](media/event-hubs-rp-install/2-marketplace-management-downloaded.png#lightbox)
 
4. A Event Hubs install csomag oldalon egy kék szalagcímnek kell megjelennie az egész tetején. A Event Hubs telepítésének megkezdéséhez kattintson a szalagcímre.
   [![piactér felügyeleti esemény-hubok – telepítés indítása](media/event-hubs-rp-install/3-marketplace-management-install-ready.png)](media/event-hubs-rp-install/3-marketplace-management-install-ready.png#lightbox)

### <a name="install-prerequisites"></a>Az előfeltételek telepítése

1. Ezután a telepítés lapra kerül át. A telepítési folyamat megkezdéséhez válassza az **Előfeltételek telepítése** lehetőséget.
   ![piactér felügyeleti esemény-hubok – előfeltételek](media/event-hubs-rp-install/4-marketplace-management-install-prereqs-start.png)
 
2. Várjon, amíg az Előfeltételek telepítése sikeres lesz. A következő lépés végrehajtása előtt egy zöld pipa jelenik meg a **telepítés előfeltételeinek telepítése** előtt.

   ![Piactér-felügyeleti esemény-hubok – előfeltételek](media/event-hubs-rp-install/5-marketplace-management-install-prereqs-succeeded.png)

### <a name="prepare-secrets"></a>Titkok előkészítése 

1. A **2 alatt. Készítse el a titkokat** lépést, válassza a **tanúsítvány hozzáadása**lehetőséget, és megjelenik a **tanúsítvány hozzáadása** panel.
   ![Marketplace felügyeleti esemény-hubok – a titkok előkészítése](media/event-hubs-rp-install/6-marketplace-management-install-prepare-secrets.png)

2. A tanúsítvány **hozzáadása**lapon kattintson a Tallózás gombra a tanúsítvány neve mező jobb oldalán.
3. Válassza ki az előfeltételek teljesítése során beszerzett. pfx tanúsítványfájl-fájlt. További információ: [a telepítés előfeltételei](event-hubs-rp-prerequisites.md). 

4. Adja meg az SSL-tanúsítvány Event Hubs biztonságos karakterlánc létrehozásához megadott jelszót. Ezután válassza a **Hozzáadás** lehetőséget.
   ![piactér felügyeleti esemény-hubok – tanúsítvány hozzáadása](media/event-hubs-rp-install/7-marketplace-management-install-prepare-secrets-add-cert.png)

### <a name="install-resource-provider"></a>Erőforrás-szolgáltató telepítése

1. Ha a tanúsítvány telepítése sikeres, egy zöld pipa jelenik meg a **titkok előkészítése** elem mellett, mielőtt továbblép a következő lépésre. Most kattintson a **telepítés** gombra a **3. erőforrás-szolgáltató telepítése**lehetőség mellett.
   ![piactér felügyeleti esemény-hubok – telepítés megkezdése](media/event-hubs-rp-install/8-marketplace-management-install-start.png)
 
2. Ezután a következő oldal jelenik meg, amely azt jelzi, hogy Event Hubs erőforrás-szolgáltató van telepítve.
   [![piactér felügyeleti esemény-hubok – telepítés](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png)](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png#lightbox)
 
3. Várjon, amíg a telepítés befejeződött. Ez a folyamat általában egy vagy több órát vesz igénybe a Azure Stack hub típusától függően. 
   [![piactér felügyeleti esemény-hubok – a telepítés befejeződött](media/event-hubs-rp-install/10-marketplace-management-install-complete.png)](media/event-hubs-rp-install/10-marketplace-management-install-complete.png#lightbox)

4. Ellenőrizze, hogy a Event Hubs telepítése sikeres volt-e, és térjen vissza a **piactér-kezelés**, erőforrás- **szolgáltatók** lapra. Event Hubs állapotának a "telepített" értéknek kell megjelennie.
   ![Marketplace felügyeleti esemény hubok elérhetők](media/event-hubs-rp-install/11-marketplace-management-rps-installed.png)

## <a name="register-event-hubs"></a>Event Hubs regisztrálása

Most regisztrálnia kell a Event Hubs erőforrás-szolgáltatót. A regisztráció lehetővé teszi, hogy a szolgáltatás kezeléséhez használja a Event Hubs felügyeleti lapot.

1. A felügyeleti portálon válassza a **minden szolgáltatás** lehetőséget a bal felső sarokban.
2. Válassza az **Előfizetések** lehetőséget. Megjelenik az előfizetések listája. 
   > [!NOTE]
   > Ügyeljen arra, hogy ne válasszon **felhasználói előfizetéseket**
3. Válassza az **alapértelmezett szolgáltatói előfizetés**lehetőséget az **előfizetések** lapon.
4. Válassza az **erőforrás-szolgáltatók**lehetőséget az **alapértelmezett szolgáltató előfizetés** lapjának bal oldalán.
5. A fenti **szűrés név alapján** mezőben keresse meg a "EventHub" karakterláncot.
6. Tekintse meg a "Microsoft. EventHub" és a "Microsoft. EventHub. admin" erőforrás-szolgáltató sorainak **állapot** oszlopát.
7. Ha bármelyikük "regisztráció nélkül" állapotú, válassza ki az egyes szolgáltatókat, majd válassza a **regisztráció**lehetőséget. 
   nem regisztrált erőforrás-szolgáltatók ![](media/event-hubs-rp-install/12-default-subscription-rps-unregistered.png)
8. Néhány másodperc elteltével válassza a **frissítés**lehetőséget. Ekkor látnia kell az erőforrás-szolgáltatót a regisztrált állapottal. 
9. Ekkor látnia kell a Microsoft. EventHub és a Microsoft. EventHub. admin nevű "regisztrált" állapotot.
   ![regisztrált erőforrás-szolgáltatók](media/event-hubs-rp-install/13-default-subscription-rps-registered.png)

10. Térjen vissza a **minden szolgáltatás** lapra.
11. Keressen rá a "Event Hubs" kifejezésre. Ekkor megjelenik a "Event Hubs", amely a Event Hubs adminisztrációs oldal belépési pontja. 
   ![elérhető szolgáltatások – Event hubok](media/event-hubs-rp-install/14-all-service-event-hubs.png)
 
## <a name="next-steps"></a>Következő lépések

Mielőtt a felhasználók üzembe helyezhetik Event Hubs erőforrásait, létre kell hoznia egy vagy több csomagot, ajánlatot és előfizetést. 

- Ha első alkalommal nyújt szolgáltatást, kezdje az [ajánlati szolgáltatások a felhasználók számára](tutorial-offer-services.md) oktatóanyaggal. Ezután folytassa a következő oktatóanyaggal, [tesztelje a szolgáltatási ajánlatot](tutorial-test-offer.md).
- Ha már ismeri a szolgáltatás nyújtásának koncepcióját, hozzon létre egy ajánlatot és egy csomagot, amely tartalmazza a Event Hubs erőforrás-szolgáltatót. Ezután hozzon létre egy előfizetést a felhasználók számára, vagy adja meg nekik az ajánlat adatait, hogy saját maguk is létrehozhatnak. A hivatkozásokat követve a [szolgáltatás, a terv, az ajánlat és az előfizetés áttekintésében](service-plan-offer-subscription-overview.md)is elvégezheti a cikkek sorát.

A frissítések kereséséhez [Azure stack Hub Event Hubs frissítése](resource-provider-apply-updates.md).

Ha el kell távolítania az erőforrás-szolgáltatót, olvassa el [a Event Hubs erőforrás-szolgáltató eltávolítása](event-hubs-rp-remove.md) című témakört.

Ha többet szeretne megtudni a felhasználói élményről, látogasson el a [Event Hubs Azure stack hub áttekintése című témakörben](../user/event-hubs-overview.md) a felhasználói dokumentumokban.