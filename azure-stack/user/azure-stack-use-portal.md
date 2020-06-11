---
title: Az Azure Stack hub felhasználói portál használata
description: Megtudhatja, hogyan érheti el és használhatja a Azure Stack hub felhasználói portálját.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 26225b1b6ba55ed0f5934130e361f1dbab4b664e
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631386"
---
# <a name="use-the-azure-stack-hub-user-portal"></a>Az Azure Stack hub felhasználói portál használata

Az Azure Stack hub portálon fizethet elő nyilvános ajánlatokra, és használhatja az ajánlatok által biztosított szolgáltatásokat. Ha a globális Azure Portal használta, már ismeri a hely működését.

## <a name="access-the-portal"></a>Hozzáférés a portálhoz

A Azure Stack hub-operátor (a szervezet egyik szolgáltatója vagy rendszergazdája) lehetővé teszi a megfelelő URL-cím elérését a portálhoz való hozzáféréshez.

- Az integrált rendszerek esetében az URL-cím az operátor régiója és a külső tartománynév alapján változik, és https://portal.&lt a formátuma* a következő: régió* &gt; . &lt; *FQDN* &gt; .
- Ha a Azure Stack Development Kit (ASDK) használja, a portál címe a következő: `https://portal.local.azurestack.external` .
- Az összes Azure Stack hub üzemelő példány alapértelmezett időzónája az egyezményes világidő (UTC) szerint van beállítva. Kiválaszthat egy időzónát Azure Stack hub telepítésekor, de a telepítés során automatikusan visszavált az UTC értékre.

## <a name="customize-the-dashboard"></a>Az irányítópult testreszabása

Az irányítópult a csempék alapértelmezett készletét tartalmazza. Válassza az **irányítópult szerkesztése** lehetőséget az alapértelmezett irányítópult módosításához, vagy válassza az **új irányítópult** lehetőséget az egyéni irányítópult létrehozásához. Az irányítópultok egyszerűen testreszabhatók csempék hozzáadásával vagy eltávolításával. Például egy számítási csempe hozzáadásához válassza az **+ erőforrás létrehozása**lehetőséget. kattintson a jobb gombbal a **számítás**elemre, majd válassza **a rögzítés az irányítópulton**lehetőséget.

![Az Azure Stack hub felhasználói portál képernyőfelvétele](media/azure-stack-use-portal/userportal.png)

Az irányítópult visszaállítása az eredeti beállításokra:
1.  Válassza az **irányítópult szerkesztése**lehetőséget. 
2.  Kattintson a jobb gombbal, és válassza **a visszaállítás az alapértelmezett állapotba**lehetőséget.

## <a name="create-subscription-and-browse-available-resources"></a>Előfizetés létrehozása és elérhető erőforrások tallózása

Ha még nem rendelkezik előfizetéssel, elő kell fizetnie egy ajánlatra. Ezután böngészheti a rendelkezésre álló erőforrásokat. Az erőforrások tallózásához és létrehozásához használja az alábbi módszerek bármelyikét:

- Válassza ki a **piactér** csempét az irányítópulton.
- A **minden erőforrás** csempén válassza az **erőforrások létrehozása**lehetőséget.
- A bal oldali navigációs panelen válassza az **+ erőforrás létrehozása**lehetőséget.

## <a name="learn-how-to-use-available-services"></a>Ismerje meg, hogyan használhatja az elérhető szolgáltatásokat

Ha útmutatásra van szüksége az elérhető szolgáltatások használatához, lehetséges, hogy az Ön számára különböző lehetőségek állnak rendelkezésre.

- A szervezet vagy szolgáltató a saját dokumentációját is megadhatja, ami általában akkor fordul elő, ha testreszabott szolgáltatásokat vagy alkalmazásokat biztosítanak.
- A harmadik féltől származó alkalmazások saját dokumentációval rendelkeznek.
- Az Azure-konzisztens szolgáltatások esetében javasoljuk, hogy először tekintse át az Azure Stack hub dokumentációját. Az Azure Stack hub felhasználói dokumentációjának eléréséhez válassza a Súgó ikont (**?**), majd kattintson a **Súgó és támogatás**elemre.

    ![Súgó és támogatás lehetőség a felhasználói felületen](media/azure-stack-use-portal/HelpAndSupport.png)

    Javasoljuk, hogy az első lépésekhez tekintse át a következő cikkeket:

    - [Fontos szempontok: szolgáltatások használata vagy alkalmazások Azure stack hub-hoz való kiépítése](azure-stack-considerations.md).
    - A dokumentáció **use Services (szolgáltatások használata** ) szakaszában az egyes szolgáltatásokra vonatkozó megfontolások szerepelnek. A szempontok oldal ismerteti az Azure-ban kínált szolgáltatás és az Azure Stack hub szolgáltatásban kínált szolgáltatások közötti különbségeket. Példaként lásd a virtuális gépekkel [kapcsolatos szempontokat](azure-stack-vm-considerations.md). Előfordulhat, hogy a **szolgáltatások használata** részben Azure stack hub egyedi információi vannak.

      Az Azure dokumentációját általános referenciáként használhatja egy szolgáltatáshoz, de tisztában kell lennie ezekkel a különbségekkel. Ismerje meg, hogy a dokumentáció a gyors **útmutató oktatóanyagokat** tartalmazó csempén az Azure-dokumentációra mutat.

## <a name="get-support"></a>Támogatás kérése

Ha támogatásra van szüksége, segítségért forduljon a szervezethez vagy a szolgáltatóhoz.

Ha a Azure Stack Development Kit (ASDK) használja, a [Azure stack hub-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) az egyetlen támogatott támogatási forrás.

## <a name="next-steps"></a>Következő lépések

[Főbb szempontok: szolgáltatások használata vagy alkalmazások készítése Azure Stack hubhoz](azure-stack-considerations.md)
