---
title: A Azure Stack felhasználói portál használata | Microsoft Docs
description: Megtudhatja, hogyan érheti el és használhatja a Azure Stack a felhasználói portált.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 392d850e5d74a1bd069653aae3b9def6438c5288
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909578"
---
# <a name="use-the-azure-stack-user-portal"></a>A Azure Stack felhasználói portál használata

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Azure Stack portálon fizethet elő nyilvános ajánlatokra, és használhatja azokat a szolgáltatásokat, amelyeket az ajánlatok biztosítanak. Ha a globális Azure Portal használta, már ismeri a hely működését.

## <a name="access-the-portal"></a>Hozzáférés a portálhoz

A Azure Stack operátor (vagy egy szolgáltató, vagy a szervezet rendszergazdája) lehetővé teszi, hogy megismerje a portál eléréséhez szükséges helyes URL-címet.

- Az integrált rendszerek esetében az URL-cím az operátor régiója és a külső tartománynév alapján változik, és a formátuma https://portal.&lt *a következő: régió.* &gt;&lt; *Teljes tartománynév* &gt;.
- Ha a Azure Stack Development Kit (ASDK) használja, a portál címe https://portal.local.azurestack.external a következő:.
- Az összes Azure Stack üzemelő példány alapértelmezett időzónája az egyezményes világidő (UTC) szerint van beállítva. Azure Stack telepítésekor kiválaszthatja az időzónát, de a telepítés során a rendszer automatikusan visszavált az UTC értékre.

## <a name="customize-the-dashboard"></a>Az irányítópult testreszabása

Az irányítópult a csempék alapértelmezett készletét tartalmazza. Válassza az **irányítópult szerkesztése** lehetőséget az alapértelmezett irányítópult módosításához, vagy válassza az **új irányítópult** lehetőséget az egyéni irányítópult létrehozásához. Az irányítópultok egyszerűen testreszabhatók csempék hozzáadásával vagy eltávolításával. Például egy számítási csempe hozzáadásához válassza az **+ erőforrás létrehozása**lehetőséget. kattintson a jobb gombbal a **számítás**elemre, majd válassza **a rögzítés az irányítópulton**lehetőséget.

![A Azure Stack felhasználói portál képernyőfelvétele](media/azure-stack-use-portal/userportal.png)

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
- Az Azure-konzisztens szolgáltatások esetében javasoljuk, hogy először tekintse át a Azure Stack dokumentációját. A Azure Stack felhasználói dokumentációjának eléréséhez válassza a Súgó ikont ( **?** ), majd kattintson a **Súgó és támogatás**lehetőségre.

    ![Súgó és támogatás lehetőség a felhasználói felületen](media/azure-stack-use-portal/HelpAndSupport.png)

    Javasoljuk, hogy az első lépésekhez tekintse át a következő cikkeket:

    - [Legfontosabb szempontok: Szolgáltatások használata vagy alkalmazások kiépítése a](azure-stack-considerations.md)Azure Stackhoz.
    - A dokumentáció **use Services (szolgáltatások használata** ) szakaszában az egyes szolgáltatásokra vonatkozó megfontolások szerepelnek. A szempontok oldal ismerteti az Azure-ban kínált szolgáltatás és a Azure Stackban kínált szolgáltatások közötti különbségeket. Példaként lásd a virtuális gépekkel [kapcsolatos szempontokat](azure-stack-vm-considerations.md). Előfordulhat, hogy a **szolgáltatások használata** részben Azure stack egyedi információk találhatók.

      Az Azure dokumentációját általános referenciáként használhatja egy szolgáltatáshoz, de tisztában kell lennie ezekkel a különbségekkel. Ismerje meg, hogy a dokumentáció a gyors **útmutató oktatóanyagokat** tartalmazó csempén az Azure-dokumentációra mutat.

## <a name="get-support"></a>Támogatás kérése

Ha támogatásra van szüksége, segítségért forduljon a szervezethez vagy a szolgáltatóhoz.

Ha a Azure Stack Development Kit (ASDK) használja, a [Azure stack fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) az egyetlen támogatási forrás.

## <a name="next-steps"></a>További lépések

[Legfontosabb szempontok: Szolgáltatások használata vagy alkalmazások kiépítése Azure Stack](azure-stack-considerations.md)
