---
title: A portál használatával kezelheti a Key Vault az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti a Key Vault az Azure Stack a portál használatával
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/04/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: bc73e80acddaac8676b4cb47c0b9ced2467cdc44
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64301010"
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>A portál használatával kezelheti a Key Vault az Azure Stackben

Key Vault az Azure Stack az Azure Stack portal használata kezelheti. Ez a cikk bemutatja, hogyan hozhat létre és kezelhet a key vault az Azure Stackben.

## <a name="prerequisites"></a>Előfeltételek

Meg kell előfizetés egy ajánlatra, amely magában foglalja az Azure Key Vault szolgáltatást.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

1. Jelentkezzen be a [felhasználói portál](https://portal.local.azurestack.external).

2. Az irányítópulton, válassza ki a **+ erőforrás létrehozása**, majd **biztonság + identitás**, majd **Key Vault**.

    ![A Key Vault képernyő](media/azure-stack-key-vault-manage-portal/image1.png)

3. Az a **kulcstartó létrehozása** panelen rendelje hozzá egy **neve** a tároló számára. Tároló neve csak alfanumerikus karaktereket és kötőjelet (-) karaktert tartalmazhat. Egy szám, nem kezdődhet.

4. Válasszon egy **előfizetés** elérhető előfizetések listájából. Minden előfizetés, a Key Vault szolgáltatás által a legördülő listában jelennek meg.

5. Válasszon egy meglévő **erőforráscsoport**, vagy hozzon létre egy újat.

6. Válassza ki a **tarifacsomag**. Az az Azure Stack Development Kit (ASDK), a kulcstartók támogatási **Standard** csak az SKU-k.

7. Válasszon egyet a meglévő **hozzáférési házirendek** , vagy hozzon létre egy újat. Hozzáférési házirend lehetővé teszi egy felhasználó, alkalmazás vagy egy biztonsági csoportot, ez a kulcstartó-műveletek végrehajtásához engedélyeit.

8. Szükség esetén válasszon egy **speciális hozzáférési szabályzat** szolgáltatások elérésének engedélyezéséhez. Például: virtuális gépek (VM) üzembe helyezés Resource Manager-sablon üzembe helyezési és tárkötet-titkosítást az Azure Disk Encryption való hozzáférést.

9. Miután konfigurálta a beállításokat, jelölje ki **OK**, majd válassza ki **létrehozása**. Ekkor elindul a key vault-telepítésben.

## <a name="manage-keys-and-secrets"></a>Kulcsok és titkos kulcsok kezelése

Miután létrehozott egy tárolót, a következő eljárással hozhat létre és kezelheti a kulcsokat és titkos kulcsok a tárolóban.

### <a name="create-a-key"></a>Kulcs létrehozása

1. Jelentkezzen be a [felhasználói portál](https://portal.local.azurestack.external).

2. Az irányítópulton, válassza ki a **összes erőforrás**, jelölje be a kulcstartóhoz, amely a korábban létrehozott, és válassza a **kulcsok** csempére.

3. Az a **kulcsok** ablaktáblán válassza előbb **Hozzáadás**.

4. Az a **hozzon létre egy kulcsot** panel listájában **beállítások**, válassza ki a kulcs létrehozásához használni kívánt módszert. Is **Generate** egy új kulcsot, **feltöltése** egy meglévő kulcs, vagy használjon **biztonsági másolat visszaállítása** kulcs biztonsági másolatának kiválasztásához.

5. Adjon meg egy **neve** a kulcshoz. A kulcs neve csak alfanumerikus karaktereket és kötőjelet (-) karaktert tartalmazhat.

6. Igény szerint állítsa be a **aktiválási dátum megadása** és **lejárati dátum megadása** a kulcs értékeit.

7. Válassza ki **létrehozás** a üzembe helyezésének megkezdéséhez.

A kulcs sikeres létrehozása után kiválaszthatja azt a **kulcsok** és megtekintéséhez vagy a hozzá tartozó tulajdonságok módosításával. A Tulajdonságok szakaszának tartalmazza a **azonosítója**, azaz egy egységes erőforrás-azonosító (URI), amely a külső alkalmazások használatával férhet hozzá a kulcshoz. Ezt a kulcsot a műveletek számának korlátozásához adja meg a beállításokat a **engedélyezett műveletek**.

![A kulcs URI-t](media/azure-stack-key-vault-manage-portal/image4.png)

### <a name="create-a-secret"></a>Titkos kulcs létrehozása

1. Jelentkezzen be a [felhasználói portál](https://portal.local.azurestack.external).

2. Az irányítópulton, válassza ki a **összes erőforrás**, jelölje be a kulcstartóhoz, amely a korábban létrehozott, és válassza a **titkos kódok** csempére.

3. A **titkos kódok**válassza **Hozzáadás**.

4. A **titkos kulcs létrehozása**, listájából **feltöltési beállítások**, válasszon egy lehetőséget, amellyel a titkos kulcs létrehozása szeretné. Létrehozhat egy titkos kulcsot **manuálisan** Ha megad egy értéket a titkos kulcsot vagy a feltöltési egy **tanúsítvány** a helyi gépen.

5. Adjon meg egy **neve** számára a titkos kulcsot. A titkos kód neve csak alfanumerikus karaktereket és kötőjelet (-) karaktert tartalmazhat.

6. Szükség esetén adja meg a **tartalom típusa**, és konfigurálja az értékeket **aktiválási dátum megadása** és **lejárati dátum megadása** számára a titkos kulcsot.

7. Válassza ki **létrehozás** a üzembe helyezésének megkezdéséhez.

A titkos kód sikeres létrehozása után kiválaszthatja azt a **titkok** és megtekintéséhez vagy a hozzá tartozó tulajdonságok módosításával. A **titkos azonosító** egy URI-t, amely a külső alkalmazások a titkos kulcs elérésére szolgál.

![URI-secret](media/azure-stack-key-vault-manage-portal/image5.png)

## <a name="next-steps"></a>További lépések

* [Virtuális gép üzembe helyezése a Key vaultban tárolt jelszó beolvasásával](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Virtuális gép üzembe helyezése a Key Vault-tanúsítvánnyal](azure-stack-key-vault-push-secret-into-vm.md)
