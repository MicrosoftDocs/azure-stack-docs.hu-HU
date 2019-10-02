---
title: Üzembe helyezés a Visual Studio Code-ban a Azure Stackhoz | Microsoft Docs
description: Felhasználóként hozzon létre egy Azure Resource Manager sablont a Visual Studio Code-ban, és a központi telepítési séma használatával készítsen elő egy olyan sablont, amely kompatibilis a saját Azure Stack verziójával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 914e3e8db57009d58a14aa87d24ff86a8291e52b
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71711083"
---
# <a name="deploy-with-visual-studio-code-to-azure-stack"></a>Üzembe helyezés a Visual Studio Code-ban Azure Stack

A Visual Studio Code és a Azure Resource Manager Tools bővítmény használatával olyan Azure Resource Manager-sablonokat hozhat létre és szerkeszthet, amelyek a Azure Stack verziójával fognak működni. Resource Manager-sablonokat a Visual Studio Code-ban a bővítmény nélkül is létrehozhat, a bővítmény azonban rendelkezik olyan automatikus kiegészítési szolgáltatásokkal, amelyek megkönnyítik a sablonok fejlesztését. Emellett olyan központi telepítési sémát is megadhat, amely megkönnyíti a Azure Stack elérhető erőforrások megértését.

Ebben a cikkben egy Windows rendszerű virtuális gépet fog telepíteni.

## <a name="concepts-for-azure-stack-resource-manager"></a>A Azure Stack Resource Managerrel kapcsolatos fogalmak

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager

Az Azure-megoldások Azure Stackban való üzembe helyezésével és kezelésével kapcsolatos fogalmak megismeréséhez tekintse meg a [Azure Resource Manager sablonok használata a Azure stack-ban](azure-stack-arm-templates.md)című témakört.

### <a name="api-profiles"></a>API-profilok
Az erőforrás-szolgáltatók koordinálásával kapcsolatos fogalmak megismeréséhez Azure Stack tekintse meg a [Azure stack API-verziók profiljainak kezelése](azure-stack-version-profiles.md)című témakört.

### <a name="the-deployment-schema"></a>A központi telepítési séma

A Azure Stack központi telepítési séma támogatja a hibrid profilokat a Visual Studio Code Azure Resource Manager sablonjain keresztül. A JSON-sablon egyik sorát megváltoztathatja a séma hivatkozásához, majd az IntelliSense használatával ellenőrizheti az Azure-kompatibilis erőforrásokat. A sémával tekintse át a Azure Stack verziójában támogatott erőforrás-szolgáltatókat, típusokat és API-verziókat. A séma a Azure Stack verziójában támogatott erőforrás-szolgáltatók API-végpontok adott verzióinak beolvasására szolgáló API-profiltól függ. A Word befejezését használhatja a típus-és apiVersion, majd a apiVersion és az API-profil számára elérhető erőforrástípusok használatára lesz korlátozva.

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio Code](https://code.visualstudio.com/)
- Hozzáférés Azure Stack
- A felügyeleti végpontokat elérő gépre [telepített Azure stack PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json)

## <a name="install-resource-manager-tools-extension"></a>Resource Manager-eszközök bővítményének telepítése

A Resource Manager-eszközök bővítményének telepítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studio Code-ot.
2. Nyomja le a CTRL + SHIFT + X billentyűkombinációt a bővítmények panel megnyitásához.
3. Keressen rá a `Azure Resource Manager Tools` kifejezésre, majd válassza a **telepítés**lehetőséget.
4. A bővítmény telepítésének befejezéséhez válassza az **Újrabetöltés** lehetőséget.

## <a name="get-a-template"></a>Sablon beszerzése

Ahelyett, hogy teljesen új sablont hozna létre, megnyit egy sablont a AzureStack-Gyorsindítás-sablonokból (https://github.com/Azure/AzureStack-QuickStart-Templates). AzureStack-rövid útmutató – a sablonok olyan Resource Manager-sablonok tárházai, amelyek erőforrásokat telepítenek a Azure Stackba. 

A cikkben `101-vm-windows-create` nevű sablon található. A sablon egy Windows rendszerű virtuális gép alapszintű központi telepítését határozza meg Azure Stack.  Ez a sablon egy virtuális hálózatot (DNS-t), egy hálózati biztonsági csoportot és egy hálózati adaptert is üzembe helyez.

1. Nyissa meg a Visual Studio Code-ot, és navigáljon egy munkamappához a gépen.
2. Nyissa meg a git bash-terminált a Visual Studio Code-ban.
3. Futtassa a következő parancsot a Azure Stack rövid útmutató adattárának beolvasásához.
    ```bash  
    Git clone https://github.com/Azure/AzureStack-QuickStart-Templates.git
    ```
4. Nyissa meg a tárházat tartalmazó könyvtárat.
    ```bash  
    CD AzureStack-QuickStart-Templates
    ```
5. A **Megnyitás** gombra kattintva megnyithatja az adattárban található `/101-vm-windows-create/azuredeploy.json` fájlt.
6. Mentse a fájlt a saját munkaterületére, vagy ha létrehozott egy ágat a tárházból, akkor dolgozhat a helyén.
7. Ha a fájl még meg van nyitva, módosítsa a `$Schema` mezőt `https://schema.management.azure.com/schemas/2019-03-01-hybrid/deploymentTemplate.json#` értékre.
8. A apiProfile mező értékének törlésével megtekintheti, hogy a központi telepítési séma működik-e.
    ```JSON  
    "apiProfile": ""
    ```
9. Vigye a kurzort az üres idézőjelek közé, majd nyomja le a CTRL + SZÓKÖZ billentyűkombinációt. A Azure Stack központi telepítési sémájának érvényes API-profiljai közül választhat. Ezt a műveletet a sablon összes erőforrás-szolgáltatóján végrehajthatja.

    ![Resource Manager-alapú központi telepítési séma Azure Stack](./media/azure-stack-resource-manager-deploy-template-vscode/azure-stack-resource-manager-vscode-schema.png)

10. Ha elkészült, üzembe helyezheti a sablont a PowerShell használatával. Kövesse az [üzembe helyezés a PowerShell használatával](azure-stack-deploy-template-powershell.md)című témakör utasításait. Adja meg a sablon helyét a parancsfájlban.
11. A Windows rendszerű virtuális gép üzembe helyezése után navigáljon a Azure Stack-portálra, és keresse meg az erőforráscsoportot. Ha törölni szeretné a gyakorlat eredményét a Azure Stack, törölje az erőforráscsoportot.

## <a name="next-steps"></a>További lépések

- További információ a [Azure stack Resource Manager-sablonokról](azure-stack-arm-templates.md).  
- További információ az [Azure stack API-profiljairól](azure-stack-version-profiles.md).