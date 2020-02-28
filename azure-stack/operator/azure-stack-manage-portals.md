---
title: A Azure Stack hub felügyeleti portáljának használata
description: Ismerje meg, hogyan használhatja a Azure Stack hub felügyeleti portálját.
author: justinha
ms.topic: quickstart
ms.date: 06/07/2019
ms.author: justinha
ms.reviewer: efemmano
ms.lastreviewed: 06/07/2019
ms.openlocfilehash: 358837da19ad34f82f81a94da0c89b165574ea49
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699252"
---
# <a name="use-the-administrator-portal-in-azure-stack-hub"></a>A Azure Stack hub felügyeleti portáljának használata

Azure Stack hub két portálon érhető el: a felügyeleti Portálon és a felhasználói portálon. Azure Stack hub-kezelőként a felügyeleti portálon használhatja Azure Stack hub napi felügyeletét és műveleteit.

## <a name="access-the-administrator-portal"></a>Hozzáférés a felügyeleti portálhoz

A felügyeleti portál eléréséhez keresse meg a portál URL-címét, és jelentkezzen be az Azure Stack hub-kezelő hitelesítő adataival. Integrált rendszer esetén a portál URL-címe a Azure Stack hub-telepítés régiójának neve és külső teljes tartományneve (FQDN) alapján változik. A felügyeleti portál URL-címe mindig megegyezik a Azure Stack Development Kit (ASDK) üzemelő példányok esetében.

| Környezet | Felügyeleti portál URL-címe |   
| -- | -- | 
| AZURE STACK FEJLESZTŐI KÉSZLET (ASDK)| https://adminportal.local.azurestack.external  |
| Integrált rendszerek | https://adminportal.&lt; *régió*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> ASDK-környezet esetén először győződjön meg arról, hogy Távoli asztali kapcsolat vagy virtuális magánhálózati (VPN) [kapcsolaton keresztül tud csatlakozni a fejlesztői csomag gazdagépéhez](../asdk/asdk-connect.md) .

 ![Azure Stack hub felügyeleti portál](media/azure-stack-manage-portals/admin-portal.png)

Az összes Azure Stack hub üzemelő példány alapértelmezett időzónája az egyezményes világidő (UTC) szerint van beállítva.

A felügyeleti portálon a következőkhöz hasonló műveleteket végezhet:

* [Azure Stack hub regisztrálása az Azure-ban](azure-stack-registration.md)
* [A piactér feltöltése](azure-stack-download-azure-marketplace-item.md)
* [Csomagok, ajánlatok és előfizetések létrehozása felhasználók számára](service-plan-offer-subscription-overview.md)
* [Állapotfelügyelet és riasztások](azure-stack-monitor-health.md)
* [Azure Stack hub frissítéseinek kezelése](azure-stack-updates.md)

A gyors üzembe helyezési **útmutató** csempéje a leggyakoribb feladatokhoz kapcsolódó online dokumentációra mutató hivatkozásokat tartalmaz.

Bár az operátorok olyan erőforrásokat is létrehozhatnak, mint például a virtuális gépek, a virtuális hálózatok és a Storage-fiókok a felügyeleti portálon, az erőforrások létrehozásához és teszteléséhez be kell [jelentkezniük a felhasználói portálra](../user/azure-stack-use-portal.md) .

>[!NOTE]
>A gyors üzembe helyezési oktatóanyag csempén a **virtuális gép létrehozása** hivatkozásra kattintva létrehoz egy virtuális gépet a felügyeleti portálon, de ez csak annak ellenőrzésére szolgál, hogy a Azure stack hub telepítése sikeresen megtörtént-e.

## <a name="understand-subscription-behavior"></a>Az előfizetés viselkedésének megismerése

A felügyeleti portálon alapértelmezés szerint három előfizetés jön létre: a felhasználás, az alapértelmezett szolgáltató és a mérés. Operátorként többnyire az *alapértelmezett szolgáltatói előfizetést*fogja használni. Nem adhat hozzá más előfizetéseket, és nem használhatja azokat a felügyeleti portálon.

Más előfizetéseket a felhasználók a felhasználói portálon hozhatnak létre a számukra létrehozott csomagok és ajánlatok alapján. A felhasználói portál azonban nem biztosít hozzáférést a felügyeleti portál bármely adminisztratív vagy működési képességéhez.

A rendszergazdák és a felhasználói portálok a Azure Resource Manager külön példányain vannak támogatva. A Azure Resource Manager elkülönítése miatt az előfizetések nem kereszteznek portálokat. Ha például Azure Stack hub-kezelőként jelentkezik be a felhasználói portálra, az *alapértelmezett szolgáltatói előfizetés*nem érhető el. Habár nem rendelkezik hozzáféréssel egyetlen rendszergazdai feladathoz sem, létrehozhat előfizetéseket saját magához az elérhető nyilvános ajánlatok közül. Ha bejelentkezett a felhasználói portálra, akkor a bérlői felhasználónak minősül.

  >[!NOTE]
  >ASDK-környezetben, ha a felhasználó ugyanahhoz a bérlői címtárhoz tartozik, mint az Azure Stack hub operátor, nem blokkolja a rendszer a felügyeleti portálra való bejelentkezést. Azonban nem férhetnek hozzá a rendszergazdai funkciókhoz, vagy előfizetések hozzáadásával hozzáférhetnek a felhasználói portálon elérhető ajánlatokhoz.

## <a name="administrator-portal-tips"></a>Rendszergazdai portál – tippek

### <a name="customize-the-dashboard"></a>Az irányítópult testreszabása

Az irányítópult az alapértelmezett csempék készletét tartalmazza. Az irányítópult **szerkesztése** lehetőség kiválasztásával módosíthatja az alapértelmezett irányítópultot, vagy kiválaszthat egy **új irányítópultot** egyéni irányítópult hozzáadásához. Csempéket is hozzáadhat az irányítópulthoz. Válassza például a **+ erőforrás létrehozása**lehetőséget, kattintson a jobb gombbal az **ajánlatok + csomagok**lehetőségre, majd válassza **a rögzítés az irányítópulton**lehetőséget.

Időnként előfordulhat, hogy megjelenik egy üres irányítópult a portálon. Az irányítópult helyreállításához kattintson az **irányítópult szerkesztése**lehetőségre, majd kattintson a jobb gombbal, és válassza **a visszaállítás az alapértelmezett állapotba**lehetőséget.

### <a name="quick-access-to-online-documentation"></a>Gyors hozzáférés az online dokumentációhoz

Az Azure Stack hub-kezelő dokumentációjának eléréséhez használja a Súgó és támogatás ikont (kérdőjel) a felügyeleti portál jobb felső sarkában. Vigye a kurzort az ikonra, majd válassza a **Súgó + támogatás**lehetőséget.

### <a name="quick-access-to-help-and-support"></a>Gyors hozzáférés a súgóhoz és támogatáshoz

Ha a felügyeleti portál jobb felső sarkában található Súgó ikonra (kérdőjel) kattint, kattintson a **Súgó + támogatás**lehetőségre, majd az **új támogatási kérelem** a **támogatás**alatt lehetőségre, a következő eredmények egyike történik:

- Ha integrált rendszerű szolgáltatást használ, akkor a művelet megnyit egy webhelyet, ahol közvetlenül nyithat egy támogatási jegyet a Microsoft ügyfélszolgálati szolgáltatásaival (CSS). Ha segítségre van szüksége annak megismeréséhez, hogy a Microsoft támogatási szolgálata vagy az eredeti berendezésgyártó (OEM) hardvergyártó támogatását használja, olvassa el a következő [témakört](azure-stack-manage-basics.md#where-to-get-support) :.
- Ha a ASDK használja, akkor ez a művelet közvetlenül az [Azure stack hub fórumok helyét](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack) nyitja meg. Ezeket a fórumokat rendszeresen figyelik. Mivel a ASDK egy kiértékelési környezet, a Microsoft CSS-ben nem érhető el hivatalos támogatás.

### <a name="quick-access-to-the-azure-roadmap"></a>Gyors hozzáférés az Azure-ütemtervhez

Ha a felügyeleti portál jobb felső sarkában a **Súgó és támogatás** (kérdőjel) lehetőséget választja, majd kiválasztja az **Azure-útitervet**, megnyílik egy új böngésző lap, amely az Azure-útitervre mutat. Ha beírja **Azure stack hub** kifejezést a **Products (termékek** ) keresőmezőbe, az összes Azure stack hub-útiterv frissítését láthatja.

## <a name="next-steps"></a>Következő lépések

[Regisztrálja Azure stack](azure-stack-registration.md) központját az Azure-ban, és töltse fel [Azure stack hub Marketplace](azure-stack-marketplace.md) -et a felhasználók számára elérhető elemek használatával.
