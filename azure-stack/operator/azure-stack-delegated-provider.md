---
title: Ajánlatok delegálása Azure Stackban | Microsoft Docs
description: Megtudhatja, hogyan delegálhat olyan feladatokat, mint az ajánlatok létrehozása és a felhasználók regisztrálása.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: eee3a928f8f3c6f376e9019af6da71a77ab09450
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829235"
---
# <a name="delegate-offers-in-azure-stack"></a>Ajánlat delegálása az Azure Stackben

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Azure Stack operátorként érdemes lehet más személyeket is felvenni a felhasználók regisztrálására és előfizetések létrehozására. Ha például Ön szolgáltató, érdemes lehet viszonteladókat regisztrálni az ügyfelek regisztrálásához és az Ön nevében történő felügyeletéhez. Ha a vállalatnál egy központi informatikai csoport tagja, akkor érdemes lehet delegálást végezni a felhasználók más informatikai személyzetének.

A delegálással könnyebben érheti el és kezelheti a több felhasználót, mint amennyit saját maga is használhat, ahogy az a következő ábrán látható:

![A Azure Stack delegálásának szintjei](media/azure-stack-delegated-provider/image1.png)

A delegálással a delegált szolgáltató felügyeli az ajánlatokat (úgynevezett *delegált ajánlat*), és a végfelhasználók a rendszergazda közreműködése nélkül kapják meg az ajánlat keretében előfizetéseket.

## <a name="delegation-roles"></a>Delegálási szerepkörök

A következő szerepkörök a delegálás részét képezik:

* A *Azure stack operátor* kezeli a Azure stack infrastruktúrát, és létrehoz egy ajánlati sablont. A kezelő másokat delegál, hogy ajánlatokat biztosítson a bérlőknek.

* A delegált Azure Stack operátorok tulajdonosi vagy *közreműködői* jogokkal rendelkező felhasználók a *delegált szolgáltatók*nevű előfizetésekben. Más szervezetekhez, például más Azure Active Directory (Azure AD) bérlőhöz tartozhatnak.

* A *felhasználók* regisztrálhatnak az ajánlatokra, és felhasználhatják őket a számítási feladatok kezeléséhez, a virtuális gépek létrehozásához, az adatok tárolásához stb.

## <a name="delegation-steps"></a>Delegálás lépései

A delegálás beállításának két alapvető lépése van:

1. **Delegált szolgáltatói előfizetés létrehozása**: Fizessen elő egy felhasználót olyan ajánlatra, amely csak az előfizetések szolgáltatást tartalmazza. Az erre az ajánlatra előfizetett felhasználók ezt követően kiterjeszthetik a delegált ajánlatokat más felhasználóknak az ajánlatokra való regisztrálással.

2. **Ajánlat delegálása a delegált szolgáltató számára**: Ez az ajánlat lehetővé teszi, hogy a delegált szolgáltató előfizetéseket hozzon létre, vagy bővítse az ajánlatot a felhasználók számára. A delegált szolgáltató most már megteheti az ajánlatot, és felkínálhatja azokat más felhasználóknak.

A következő ábra a delegálás beállításának lépéseit mutatja be:

![A delegált szolgáltató létrehozásához szükséges lépések, valamint a felhasználók regisztrációjának engedélyezése Azure Stack](media/azure-stack-delegated-provider/image2.png)

### <a name="delegated-provider-requirements"></a>Delegált szolgáltatói követelmények

Ahhoz, hogy meghatalmazott szolgáltatóként működjön, a felhasználó egy előfizetés létrehozásával kapcsolatot létesít a fő szolgáltatóval. Ez az előfizetés azonosítja a delegált szolgáltatót úgy, hogy jogosult legyen a delegált ajánlatokat a fő szolgáltató nevében bemutatni.

A kapcsolat létrejötte után a Azure Stack operátor delegálhat egy ajánlatot a delegált szolgáltatónak. A delegált szolgáltató elvégezheti az ajánlatot, átnevezheti (de nem változtathatja meg az anyagot), és felkínálhatja azt az ügyfeleinek.

## <a name="delegation-walkthrough"></a>Delegálási útmutató

A következő szakaszokban ismertetjük a delegált szolgáltató beállítását, az ajánlat delegálását, valamint annak ellenőrzését, hogy a felhasználók regisztrálhatnak-e a delegált ajánlatra.

### <a name="set-up-roles"></a>Szerepkörök beállítása

Az útmutató használatához két Azure AD-fiókra van szükség a Azure Stack-kezelő fiókon kívül. Ha nem rendelkezik ezzel a két fiókkal, létre kell hoznia azokat. A fiókok bármely Azure AD-felhasználóhoz tartozhatnak, és a meghatalmazott szolgáltatóként és a felhasználóként is hivatkoznak rájuk.

| **Szerepkör** | **Szervezeti jogosultságok** |
| --- | --- |
| Delegált szolgáltató |Felhasználó |
| Felhasználó |Felhasználó |

 > [!NOTE]
 > CSP-viszonteladó esetén a delegált szolgáltató létrehozásához az szükséges, hogy ezek a felhasználók a bérlői könyvtárban legyenek (a felhasználó Azure AD). A Azure Stack operátornak [először](azure-stack-enable-multitenancy.md) elő kell készítenie a bérlő Azure ad-t, majd a következő [lépések végrehajtásával](azure-stack-csp-howto-register-tenants.md)be kell állítania a használatot és a számlázást.

### <a name="identify-the-delegated-provider"></a>A delegált szolgáltató azonosítása

1. Jelentkezzen be a felügyeleti portálra Azure Stack operátorként.

1. Olyan ajánlat létrehozása, amely lehetővé teszi, hogy a felhasználó delegált szolgáltató legyen:

   a.  [Hozzon létre egy csomagot](azure-stack-create-plan.md).
       A tervnek csak az előfizetési szolgáltatást kell tartalmaznia. Ez a cikk egy **PlanForDelegation** nevű csomagot használ példaként.

   b.  [Hozzon létre egy ajánlatot](azure-stack-create-offer.md) a csomag alapján. Ez a cikk egy **OfferToDP** nevű ajánlatot használ példaként.

   c.  Adja hozzá a meghatalmazott szolgáltatót előfizetőként ehhez az ajánlathoz azelőfizetések, majd a **Hozzáadás**, majd az **új bérlői előfizetés**kiválasztásával.

   ![A delegált szolgáltató hozzáadása előfizetőként Azure Stack felügyeleti portálon](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Ahogy az összes Azure Stack ajánlathoz, lehetősége van arra, hogy az ajánlat nyilvános legyen, és lehetővé teszi a felhasználók számára a regisztrációt, illetve a magánjellegűvé tételét, valamint a Azure Stack operátor számára a regisztráció felügyeletét. A delegált szolgáltatók általában egy kis csoport. Azt szeretné szabályozni, hogy ki fogadja el, hogy a legtöbb esetben ez az ajánlat magánjellegű legyen.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Azure Stack operátor létrehozza a delegált ajánlatot

A következő lépés az, hogy létrehozza a csomagot és az ajánlatot, amelyet delegálni fog, és hogy a felhasználók használni fogják. Javasoljuk, hogy ezt az ajánlatot úgy határozza meg, ahogy azt szeretné, hogy a felhasználók lássák, mert a delegált szolgáltató nem módosíthatja az általa foglalt csomagokat és kvótákat.

1. Azure Stack operátorként [hozzon létre egy csomagot](azure-stack-create-plan.md) és [egy ajánlatot](azure-stack-create-offer.md) a csomag alapján. Ez a cikk egy **DelegatedOffer** nevű ajánlatot használ példaként.

   > [!NOTE]
   > Az ajánlatnak nem kell nyilvánosnak lennie, de azt nyilvánosan is elvégezheti. A legtöbb esetben azonban csak azt szeretné, hogy a delegált szolgáltatók hozzáférjenek az ajánlathoz. Miután delegált egy privát ajánlatot az alábbi lépésekben leírtak szerint, a delegált szolgáltató hozzáfér hozzá.

2. Az ajánlat delegálása. Nyissa meg a **DelegatedOffer**. A **Beállítások**területen válassza a **delegált szolgáltatók**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

3. Válassza ki az előfizetést a delegált szolgáltatóhoz a legördülő listából, majd válassza a **delegálás**lehetőséget.

   ![Meghatalmazott szolgáltató hozzáadása Azure Stack felügyeleti portálon](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>A delegált szolgáltató testreszabja az ajánlatot

Jelentkezzen be a felhasználói portálra meghatalmazott szolgáltatóként, majd hozzon létre egy új ajánlatot sablonként a delegált ajánlat használatával.

1. Válassza az **+ erőforrás létrehozása**, majd a **bérlői ajánlatok + csomagok**, majd az **ajánlat**lehetőséget.

    ![Új ajánlat létrehozása Azure Stack felhasználói portálon](media/azure-stack-delegated-provider/image5.png)

2. Rendeljen egy nevet az ajánlathoz. Ez a példa az **ResellerOffer**-t használja. Válassza ki azt a delegált ajánlatot, amelyre alapozni szeretné, majd válassza a **Létrehozás**lehetőséget.

   ![Név kiosztása Azure Stack felhasználói portálon](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Fontos tisztában lenni azzal, hogy a delegált szolgáltatók csak olyan ajánlatokat választhatnak, amelyek hozzá vannak delegálva. Nem módosíthatják ezeket az ajánlatokat. Csak egy Azure Stack operátor módosíthatja ezeket az ajánlatokat. Például csak egy operátor módosíthatja a csomagjait és kvótáit. A meghatalmazott szolgáltatók nem készítenek ajánlatot az alapcsomagokból és a kiegészítő csomagokból.

3. A delegált szolgáltató a saját portál URL-címén keresztül nyilvánosan elérhetővé teheti ezeket az ajánlatokat. Az ajánlat nyilvános létrehozásához válassza a **Tallózás**lehetőséget, majd az **ajánlatokat**. Válassza ki az ajánlatot, majd válassza az **Állapot módosítása**lehetőséget.

4. A nyilvános delegált ajánlatok már csak a delegált portálon keresztül láthatók. Az URL-cím megkeresése és módosítása:

    a.  Válassza a **Tallózás**, majd az **összes szolgáltatás**lehetőséget, majd az **általános** kategóriában válasszaaz előfizetések lehetőséget. Válassza ki a delegált szolgáltatói előfizetést (például **DPSubscription**), majd a **Tulajdonságok**elemet.

    b.  Másolja a portál URL-címét egy másik helyre, például a Jegyzettömbbe.

    ![Válassza ki a delegált szolgáltatói előfizetést Azure Stack felhasználói portálon](media/azure-stack-delegated-provider/dpportaluri.png)  

   A delegált ajánlat meghatalmazott szolgáltatóként való létrehozása befejeződött. Jelentkezzen ki meghatalmazott szolgáltatóként, és zárjuk be a böngészőablakot.

### <a name="sign-up-for-the-offer"></a>Regisztráljon az ajánlatra

1. Egy új böngészőablakban lépjen az előző lépésben mentett delegált portál URL-címére. Jelentkezzen be a portálra felhasználóként.

   >[!NOTE]
   >A delegált ajánlatok nem láthatók, kivéve, ha a delegált portált használja.

1. Az irányítópulton válassza az **előfizetés beszerzése**lehetőséget. Láthatja, hogy csak a delegált szolgáltató által létrehozott delegált ajánlatok jelennek meg a felhasználó számára.

   ![Ajánlatok megtekintése és kiválasztása Azure Stack felhasználói portálon](media/azure-stack-delegated-provider/image8.png)

Az ajánlatok delegálásának folyamata befejeződött. Mostantól a felhasználók regisztrálhatnak erre az ajánlatra az előfizetés beszerzésével.

## <a name="move-subscriptions-between-delegated-providers"></a>Előfizetések áthelyezése delegált szolgáltatók között

Ha szükséges, egy előfizetést át lehet helyezni az új vagy a meglévő delegált szolgáltatói előfizetések között, amelyek ugyanahhoz a címtár-bérlőhöz tartoznak. Ezeket a [Move-AzsSubscription PowerShell-](/powershell/module/azs.subscriptions.admin)parancsmag használatával helyezheti át.

Az előfizetések áthelyezése akkor hasznos, ha:

* Egy új csapattagot kell bevezetni, amely a delegált szolgáltatói szerepkörbe kerül, és az alapértelmezett szolgáltatói előfizetésben korábban létrehozott, a csapattagok felhasználói előfizetéseit kívánja hozzárendelni.
* Több delegált szolgáltatói előfizetése van ugyanabban a címtár-bérlőben (Azure AD), és felhasználói előfizetéseket kell áthelyeznie közöttük. Ez a helyzet akkor fordulhat elő, ha egy csapattag a csapatok és az előfizetésük között van kiosztva az új csapatnak.

## <a name="next-steps"></a>További lépések

* [Virtuális gép kiépítése](../user/azure-stack-create-vm-template.md)
