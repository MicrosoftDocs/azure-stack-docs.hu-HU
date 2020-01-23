---
title: Üzembe helyezés a Visual Studio Code-ban Azure Stack hub-ban | Microsoft Docs
description: Felhasználóként hozzon létre egy Azure Resource Manager sablont a Visual Studio Code-ban, és a központi telepítési séma használatával készítsen elő egy olyan sablont, amely kompatibilis az Azure Stack hub saját verziójával.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 12f7b1f0dad8e1d7b452fcfe0e1785fcd851b04b
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536453"
---
# <a name="deploy-with-visual-studio-code-to-azure-stack-hub"></a>Üzembe helyezés a Visual Studio Code-ban Azure Stack hub-ban

A Visual Studio Code és a Azure Resource Manager Tools bővítmény használatával olyan Azure Resource Manager-sablonokat hozhat létre és szerkeszthet, amelyek az Azure Stack hub verziójával fognak működni. Resource Manager-sablonokat a Visual Studio Code-ban a bővítmény nélkül is létrehozhat, a bővítmény azonban rendelkezik olyan automatikus kiegészítési szolgáltatásokkal, amelyek megkönnyítik a sablonok fejlesztését. Emellett olyan központi telepítési sémát is megadhat, amely segítséget nyújt az Azure Stack hub-on elérhető erőforrások megismerésében.

Ebben a cikkben egy Windows rendszerű virtuális gépet fog telepíteni.

## <a name="concepts-for-azure-stack-hub-resource-manager"></a>Az Azure Stack hub Resource Managerrel kapcsolatos fogalmak

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack hub Resource Manager

Az Azure-megoldások Azure Stack hub-beli üzembe helyezésével és kezelésével kapcsolatos fogalmak megismeréséhez tekintse meg a következő témakört: [Azure Resource Manager-sablonok használata az Azure stack hub-ban](azure-stack-arm-templates.md).

### <a name="api-profiles"></a>API-profilok
Az Azure Stack hub erőforrás-szolgáltatóinak koordinálásával kapcsolatos fogalmak megismeréséhez lásd: az [API-verziók profiljainak kezelése Azure stack központban](azure-stack-version-profiles.md).

### <a name="the-deployment-schema"></a>A központi telepítési séma

Az Azure Stack hub központi telepítési séma támogatja a hibrid profilokat a Visual Studio Code-ban található Azure Resource Manager-sablonokon keresztül. A JSON-sablon egyik sorát megváltoztathatja a séma hivatkozásához, majd az IntelliSense használatával ellenőrizheti az Azure-kompatibilis erőforrásokat. A sémával tekintse át az Azure Stack hub verziójában támogatott erőforrás-szolgáltatókat, típusokat és API-verziókat. A séma attól függ, hogy a Azure Stack hub-verziójában támogatott erőforrás-szolgáltatók API-végpontjai adott verzióit szeretné-e lekérni az API-profiltól. A Word befejezését használhatja a típus-és apiVersion, majd a apiVersion és az API-profil számára elérhető erőforrástípusok használatára lesz korlátozva.

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio Code](https://code.visualstudio.com/)
- Hozzáférés Azure Stack hubhoz
- [Azure stack hub PowerShell telepítve](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json) a felügyeleti végpontokat elérő gépre

## <a name="install-resource-manager-tools-extension"></a>Resource Manager-eszközök bővítményének telepítése

A Resource Manager-eszközök bővítményének telepítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studio Code-ot.
2. Nyomja le a CTRL + SHIFT + X billentyűkombinációt a bővítmények panel megnyitásához.
3. Keresse meg `Azure Resource Manager Tools`, majd válassza a **telepítés**lehetőséget.
4. A bővítmény telepítésének befejezéséhez válassza az **Újrabetöltés** lehetőséget.

## <a name="get-a-template"></a>Sablon beszerzése

A sablon létrehozása helyett a AzureStack-Gyorsindítás-sablonok (https://github.com/Azure/AzureStack-QuickStart-Templates). AzureStack-Gyorsindítás – a sablonok olyan Resource Manager-sablonok tárházai, amelyek erőforrásokat telepítenek Azure Stack hubhoz. 

A cikkben szereplő sablon `101-vm-windows-create`. A sablon egy Windows rendszerű virtuális gép alapszintű telepítését határozza meg Azure Stack hubhoz.  Ez a sablon egy virtuális hálózatot (DNS-t), egy hálózati biztonsági csoportot és egy hálózati adaptert is üzembe helyez.

1. Nyissa meg a Visual Studio Code-ot, és navigáljon egy munkamappához a gépen.
2. Nyissa meg a git bash-terminált a Visual Studio Code-ban.
3. Futtassa a következő parancsot az Azure Stack hub gyors üzembe helyezési tárházának beolvasásához.
    ```bash  
    Git clone https://github.com/Azure/AzureStack-QuickStart-Templates.git
    ```
4. Nyissa meg a tárházat tartalmazó könyvtárat.
    ```bash  
    CD AzureStack-QuickStart-Templates
    ```
5. A **Megnyitás** gombra kattintva megnyithatja a fájlt `/101-vm-windows-create/azuredeploy.json` a tárházban.
6. Mentse a fájlt a saját munkaterületére, vagy ha létrehozott egy ágat a tárházból, akkor dolgozhat a helyén.
7. Ha a fájl továbbra is meg van nyitva, módosítsa a `$Schema` mezőt `https://schema.management.azure.com/schemas/2019-03-01-hybrid/deploymentTemplate.json#`re.
8. A apiProfile mező értékének törlésével megtekintheti, hogy a központi telepítési séma működik-e.
    ```JSON  
    "apiProfile": ""
    ```
9. Vigye a kurzort az üres idézőjelek közé, majd nyomja le a CTRL + SZÓKÖZ billentyűkombinációt. Az Azure Stack hub központi telepítési sémájának érvényes API-profiljai közül választhat. Ezt a műveletet a sablon összes erőforrás-szolgáltatóján végrehajthatja.

    ![Azure Stack hub Resource Manager-alapú üzembe helyezési séma](./media/azure-stack-resource-manager-deploy-template-vscode/azure-stack-resource-manager-vscode-schema.png)

10. Ha elkészült, üzembe helyezheti a sablont a PowerShell használatával. Kövesse az [üzembe helyezés a PowerShell használatával](azure-stack-deploy-template-powershell.md)című témakör utasításait. Adja meg a sablon helyét a parancsfájlban.
11. A Windows rendszerű virtuális gép üzembe helyezése után navigáljon az Azure Stack hub portálra, és keresse meg az erőforráscsoportot. Ha törölni szeretné a gyakorlat eredményét az Azure Stack hub-ból, törölje az erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub Resource Manager-sablonokról](azure-stack-arm-templates.md).  
- További információ az [Azure stack hub API-profiljairól](azure-stack-version-profiles.md).