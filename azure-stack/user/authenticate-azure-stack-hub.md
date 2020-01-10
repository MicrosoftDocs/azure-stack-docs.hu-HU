---
title: A Azure Stack hub hitelesítési adatainak beszerzése | Microsoft Docs
description: Tudnivalók a Azure Stack hub hitelesítési adatainak beszerzéséről
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: d10fbc7ad009456459a8719fbc6e6c8ce51529f1
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75816479"
---
# <a name="how-to-get-authentication-information-for-azure-stack-hub"></a>Azure Stack hub hitelesítési adatainak beolvasása

Azure Stack hub hitelesítéséhez előfizetés-azonosítót, bérlői azonosítót és helyet kell megadnia, valamint Azure Stack hub Resource Manager-végpontot. Ezek az értékek a Azure Stack hub [Azure stack hub Resource Manager-végpontján](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1910#the-azure-stack-hub-resource-manager-endpoint) szerezhetők be. Vagy a cikkben ismertetett lépésekkel kérheti le őket.

## <a name="values-needed-to-authenticate"></a>A hitelesítéshez szükséges értékek

A következő adatokra lesz szüksége:

-   **Előfizetés azonosítója**  

    Az előfizetés-AZONOSÍTÓval érheti el az ajánlatokat az Azure Stack hub szolgáltatásban.

-   **Bérlő azonosítója**

    A címtár egy olyan tároló, amely a felhasználókra, alkalmazásokra, csoportokra és egyszerű szolgáltatásokra vonatkozó információkat tartalmazza. A címtár-bérlő egy szervezet, például a Microsoft vagy a saját vállalata.

-   **Hely**

    A hely vagy régió a késés által meghatározott kerületen belül üzembe helyezett adatközpontok készlete, és egy dedikált regionális, kis késleltetésű hálózaton keresztül csatlakozik. Az Azure Stack hub esetében a tartózkodási hely egy helyi adatközpontot is tartalmazhat, és nem egy Azure-régiót.

-   **Azure Stack hub Resource Manager-végpont**

    A Microsoft Azure Resource Manager egy felügyeleti keretrendszer, amely lehetővé teszi a rendszergazdáknak az Azure-erőforrások üzembe helyezését, kezelését és figyelését. A Azure Resource Manager a feladatokat nem külön, hanem csoportként, egyetlen művelet során is kezelhetik.

## <a name="get-the-subscription-id"></a>Előfizetés AZONOSÍTÓjának beolvasása

Az előfizetés AZONOSÍTÓjának lekérése:

1.  Jelentkezzen be az Azure Stack hub felhasználói portálra.

2.  Válassza **a minden szolgáltatás**lehetőséget.

    > ![Azure Stack hub Auth info előfizetés-azonosító bérlő azonosítója](./media/authenticate-azure-stack-hub/azure-stack-hub-auth-info.png)

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

Integrált rendszer esetén a Azure Resource Manager végpont URL-címe a következő:<br>`https://management.<location>.<fqdn>.com`

A metaadatok végpontja, amely olyan tulajdonságokra mutat, mint a katalógus végpontja, a gráf végpontja, a portál végpontja, a bejelentkezési végpont és a célközönség, az URL-cím: `<ResourceManager>/metadata/endpoints?api-version=1.0`

## <a name="next-steps"></a>Következő lépések

További információ az [Azure stack hub Resource Manager](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1910) Azure stack hub használatával történő használatáról.
