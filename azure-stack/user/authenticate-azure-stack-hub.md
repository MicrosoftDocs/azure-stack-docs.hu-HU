---
title: Azure Stack hub hitelesítési adatainak beolvasása
description: Tudnivalók a Azure Stack hub hitelesítési adatainak beszerzéséről
author: mattbriggs
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/05/2020
ms.openlocfilehash: 25245bff433d4cf92225ec20b5423b08a6ddfc12
ms.sourcegitcommit: 8ffa29f71d69191534d42f86f49f719b4198a097
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92354044"
---
# <a name="get-authentication-information-for-azure-stack-hub"></a>Azure Stack hub hitelesítési adatainak beolvasása

Azure Stack hub hitelesítéséhez előfizetés-azonosítót, bérlői azonosítót és helyet kell megadnia, valamint Azure Stack hub Resource Manager-végpontot. Ezek az értékek a Azure Stack hub [Azure stack hub Resource Manager-végpontján](./azure-stack-version-profiles-ruby.md?view=azs-1910#the-azure-stack-hub-resource-manager-endpoint) szerezhetők be. Vagy a cikkben ismertetett lépésekkel kérheti le őket.

## <a name="values-needed-to-authenticate"></a>A hitelesítéshez szükséges értékek

A következő adatokra lesz szüksége:

-   **Előfizetés azonosítója**  

    Az előfizetés-AZONOSÍTÓval érheti el az ajánlatokat az Azure Stack hub szolgáltatásban.

-   **Bérlőazonosító**

    A címtár egy olyan tároló, amely a felhasználókra, alkalmazásokra, csoportokra és egyszerű szolgáltatásokra vonatkozó információkat tartalmazza. A címtár-bérlő egy szervezet, például a Microsoft vagy a saját vállalata.

-   **Hely**

    A hely vagy régió a késés által meghatározott kerületen belül üzembe helyezett adatközpontok készlete, és egy dedikált regionális, kis késleltetésű hálózaton keresztül csatlakozik. Az Azure Stack hub esetében a tartózkodási hely egy helyi adatközpontot is tartalmazhat, és nem egy Azure-régiót.

-   **Azure Stack hub Resource Manager-végpont**

    A Microsoft Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi a rendszergazdáknak az Azure-erőforrások üzembe helyezését, kezelését és figyelését. A Azure Resource Manager a feladatokat nem külön, hanem csoportként, egyetlen művelet során is kezelhetik.

## <a name="get-the-subscription-id"></a>Előfizetés AZONOSÍTÓjának beolvasása

Az előfizetés AZONOSÍTÓjának lekérése:

1.  Jelentkezzen be az Azure Stack hub felhasználói portálra.

2.  Válassza **a minden szolgáltatás**lehetőséget.

    > ![Azure Stack hub Auth info előfizetés-azonosító bérlő azonosítója](./media/authenticate-azure-stack-hub/azure-stack-hub-auth-infoa.png)

3.  Válassza az **Előfizetések** lehetőséget.

4.  Válassza ki a használni kívánt előfizetést.

5.  Másolja az **előfizetés-azonosítót** az **áttekintésből**.

## <a name="get-the-tenant-id"></a>A bérlő AZONOSÍTÓjának beolvasása

A bérlő AZONOSÍTÓjának lekérése:

1.  Jelentkezzen be az Azure Stack hub felhasználói portálra.

2.  Vigye a kurzort a panel jobb felső sarkában található felhasználónevére.

3.  A **címtár-azonosító** a bérlő azonosítója.

## <a name="get-the-azure-resource-manager-endpoint"></a>Az Azure Resource Manager végpont beolvasása

A Azure Resource Manager végpont az Azure Stack hub üzembe helyezési és felügyeleti szolgáltatásának metaadat-végpontja. Olyan felügyeleti réteget biztosít, amely lehetővé teszi az Azure-előfizetésében lévő erőforrások létrehozását, frissítését és törlését.

Integrált rendszer esetén a Azure Resource Manager végpont URL-címe a következő:<br>`https://management.<location>.<fqdn>`

Az URL-cím a következő tulajdonságokkal rendelkező metaadatok végpontjának lekérése: gyűjtemény végpont, gráf végpont, portál végpont, bejelentkezési végpont és célközönség. `<ResourceManager>/metadata/endpoints?api-version=1.0`

## <a name="next-steps"></a>Következő lépések

További információ az [Azure stack hub Resource Manager](./azure-stack-version-profiles.md?view=azs-1910) Azure stack hub használatával történő használatáról.
