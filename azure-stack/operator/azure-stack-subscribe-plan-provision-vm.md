---
title: Előfizetés Azure Stack hub ajánlatára
description: Előfizetések létrehozása a Azure Stack hub ajánlatai számára
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: bryanla
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: 3a9798b82729a88b2ca3cf8ed2cf403a9dab34c7
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882861"
---
# <a name="create-subscriptions-to-offers-in-azure-stack-hub"></a>Előfizetések létrehozása a Azure Stack hub ajánlatai számára

Miután [létrehozott egy ajánlatot](azure-stack-create-offer.md), a felhasználóknak elő kell fizetniük rá, hogy használhassák. A felhasználók kétféleképpen fizethetnek elő egy ajánlatra:

- Felhőüzemeltetőként létrehozhat egy előfizetést egy felhasználó számára a felügyeleti portálon. A létrehozott előfizetések nyilvános és személyes ajánlatok részét is képezhetik.
- Bérlői felhasználóként a felhasználói portál használatakor feliratkozhat egy nyilvános ajánlatra.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Előfizetés létrehozása Felhőbeli kezelőként

A Felhőbeli operátorok a felügyeleti portál használatával előfizetést hozhatnak létre egy felhasználónak szóló ajánlathoz. Előfizetések hozhatók létre a saját címtár-bérlő tagjai számára. Ha engedélyezve van a [többszörös](azure-stack-enable-multitenancy.md) kisegítő lehetőség, létrehozhat előfizetéseket is a felhasználók számára további címtár-bérlők esetében.

Ha nem szeretné, hogy a bérlők saját előfizetéseket hozzanak létre, tegye a saját ajánlatait, majd hozzon létre előfizetéseket a bérlők számára. Ez a megközelítés gyakori a Azure Stack hub külső számlázási vagy Service Catalog rendszerekkel való integrálásakor.

Miután létrehozott egy előfizetést egy felhasználóhoz, bejelentkezhet a felhasználói portálra, és láthatja, hogy előfizetett az ajánlatra.  

### <a name="to-create-a-subscription-for-a-user"></a>Előfizetés létrehozása felhasználó számára

1. A felügyeleti portálon lépjen a **felhasználói előfizetések** elemre.
2. Válassza a **Hozzáadás** lehetőséget. Az **új felhasználói előfizetés**területen adja meg a következő adatokat:  

   - **Megjelenítendő név** – a *felhasználói előfizetés neveként*megjelenő előfizetés azonosítására szolgáló rövid név.
   - **Felhasználó** – válasszon egy felhasználót egy elérhető címtár-bérlőből ehhez az előfizetéshez. A Felhasználónév *tulajdonosként*jelenik meg.  A Felhasználónév formátuma a személyazonossági megoldástól függ. Példa:

     - **Azure ad:** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS:** `<user1>@<azurestack.local>`

   - **Címtár-bérlő** – válassza ki azt a címtár-bérlőt, amelyben a felhasználói fiók tartozik. Ha még nem engedélyezte a többszörös bérletet, csak a helyi címtár-bérlő érhető el.

3. Válassza az **ajánlat**lehetőséget. Az **ajánlatok**területen válasszon egy **ajánlatot** ehhez az előfizetéshez. Mivel egy felhasználóhoz tartozó előfizetést hoz létre, válassza a **magánjellegű** lehetőséget a kisegítő lehetőségek közül.

4. Válassza a **Létrehozás** lehetőséget az előfizetés létrehozásához. Az új előfizetés a **felhasználói előfizetés**területen jelenik meg. Amikor a felhasználó bejelentkezik a felhasználói portálra, láthatja az előfizetés részleteit.

### <a name="to-make-an-add-on-plan-available"></a>Kiegészítő csomag elérhetővé tétele

A Felhőbeli operátorok bármikor hozzáadhatnak egy csomagot egy korábban létrehozott előfizetéshez:

1. A felügyeleti portálon válassza a **minden szolgáltatás** lehetőséget, majd a **felügyeleti erőforrások** kategóriában válassza a **felhasználói előfizetések**lehetőséget. Válassza ki a módosítani kívánt előfizetést.

2. Válassza a **bővítmények** , majd a **+ Hozzáadás**lehetőséget.  

3. A **csomag hozzáadása**területen válassza ki azt a csomagot, amelyet bővítményként szeretne használni.

## <a name="create-a-subscription-as-a-user"></a>Előfizetés létrehozása felhasználóként

Felhasználóként bejelentkezhet a felhasználói portálra, ahol megkeresheti és előfizethet nyilvános ajánlatokat és kiegészítő csomagokat a címtár-bérlő (szervezet) számára.

>[!NOTE]
>Ha a Azure Stack hub-környezet támogatja a [több-](azure-stack-enable-multitenancy.md)bérlős szolgáltatást, előfizethet a távoli címtár-bérlői ajánlatokra is.

### <a name="to-subscribe-to-an-offer"></a>Előfizetés egy ajánlatra

1. Jelentkezzen be az Azure Stack hub felhasználói portálra, és válassza az **előfizetés beszerzése**lehetőséget.

   ![Előfizetés beszerzése](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. Az **előfizetés beolvasása**területen adja meg az előfizetés rövid nevét a **megjelenítendő névben**. Válassza az **ajánlat** lehetőséget, majd az **ajánlat választása**területen válasszon ajánlatot. Válassza a **Létrehozás** lehetőséget az előfizetés létrehozásához.

   ![Ajánlat létrehozása](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Az ajánlatra való feliratkozás után frissítse a portált, hogy megtekintse, mely szolgáltatások tartoznak az új előfizetéshez.

4. A létrehozott előfizetés megtekintéséhez válassza a **minden szolgáltatás** lehetőséget, majd az **általános** kategóriában válassza az **előfizetések**lehetőséget. Az előfizetés részleteinek megtekintéséhez válassza ki az előfizetést.  

### <a name="to-enable-an-add-on-plan-in-your-subscription"></a>Kiegészítő csomag engedélyezése az előfizetésben

Ha az előfizetett ajánlat egy kiegészítő csomaggal rendelkezik, akkor bármikor hozzáadhatja azt az előfizetéshez.  

1. A felhasználói portálon válassza a **minden szolgáltatás**lehetőséget. Ezután az **általános** kategóriában válassza az **előfizetések**lehetőséget, majd válassza ki a módosítani kívánt előfizetést. Ha elérhetők kiegészítő csomagok, a **+ csomag hozzáadása** aktív, és a **kiegészítő csomagok**csempéjét jeleníti meg.

   Ha a **+ hozzáadási terv** nem aktív, akkor az adott előfizetéshez társított ajánlathoz nem tartoznak kiegészítő csomagok.

1. Válassza a **+ csomag hozzáadása** vagy a **kiegészítő csomagok** csempét. A **kiegészítő csomagok**területen válassza ki a hozzáadni kívánt csomagot.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy a felhasználók hogyan telepíthetnek erőforrásokat az előfizetésbe: 
  - [Számos felhasználói](../user/azure-stack-quick-windows-portal.md) útmutató bemutatja, hogyan lehet Windows és Linux rendszerű virtuális gépeket kiépíteni a PowerShell, az Azure CLI és a felhasználói portál használatával. 
  - [Egy Azure Resource Manager sablont használó oktatóanyag](../user/azure-stack-create-vm-template.md) bemutatja, hogyan helyezhet üzembe egy Minikube-t futtató Ubuntu 16,04 virtuális gépet a kubenetes-fürt felügyeletéhez. 
