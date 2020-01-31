---
title: Key Vault kezelése Azure Stack hub-ban a portál használatával
description: Megtudhatja, hogyan kezelheti Key Vault a Azure Stack hub-ban az Azure Stack hub portál használatával.
author: sethmanheim
ms.topic: article
ms.date: 01/10/2020
ms.author: sethm
ms.lastreviewed: 1/10/2020
ms.openlocfilehash: 34fc01bd117d8ab7b4dec9483f37f762a1c8d835
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884816"
---
# <a name="manage-key-vault-in-azure-stack-hub-using-the-portal"></a>Key Vault kezelése Azure Stack hub-ban a portál használatával

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet egy kulcstartót Azure Stack hub-ban az Azure Stack hub portál használatával.

## <a name="prerequisites"></a>Előfeltételek

Elő kell fizetnie egy olyan ajánlatra, amely tartalmazza a Azure Key Vault szolgáltatást.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

1. Jelentkezzen be a [felhasználói portálra](https://portal.local.azurestack.external).

2. Az irányítópulton válassza az **+ erőforrás létrehozása**, majd a **Biztonság és Identitáskezelés**, majd a **Key Vault**lehetőséget.

    ![Key Vault képernyő](media/azure-stack-key-vault-manage-portal/image1.png)

3. A **Key Vault létrehozása** panelen rendeljen hozzá egy **nevet** a tárolóhoz. A tárolók nevei csak alfanumerikus karaktereket és kötőjelet (-) tartalmazhatnak. Nem kezdődhet számmal.

4. Válasszon egy **előfizetést** az elérhető előfizetések listájából. A Key Vault szolgáltatást nyújtó összes előfizetés megjelenik a legördülő listában.

5. Válasszon ki egy meglévő **erőforráscsoportot**, vagy hozzon létre egy újat.

6. Válassza ki az **árképzési szintet**. A Azure Stack Development Kit (ASDK) a Key vaultok csak a **szabványos** SKU-ket támogatják.

7. Válasszon egyet a meglévő **hozzáférési szabályzatok** közül, vagy hozzon létre újat. A hozzáférési házirend lehetővé teszi, hogy engedélyeket adjon egy felhasználó, egy alkalmazás vagy egy biztonsági csoport számára, hogy műveleteket hajtson végre a tárban.

8. Igény szerint a szolgáltatásokhoz való hozzáférés engedélyezéséhez válasszon egy **speciális hozzáférési szabályzatot** . Például: virtuális gépek (VM-EK) üzembe helyezéshez, erőforrás-kezelő a sablonok üzembe helyezéséhez, valamint a Azure Disk Encryptionhoz való hozzáférés a kötetek titkosításához.

9. A beállítások konfigurálása után válassza **az OK**, majd a **Létrehozás**lehetőséget. Ez a lépés elindítja a Key Vault üzembe helyezését.

## <a name="manage-keys-and-secrets"></a>Kulcsok és titkos kódok kezelése

A kulcstároló létrehozása után a következő eljárással hozhat létre és kezelhet kulcsokat és titkos kulcsokat a tárolón belül:

### <a name="create-a-key"></a>Kulcs létrehozása

1. Jelentkezzen be az Azure Stack hub [felhasználói portálra](https://portal.local.azurestack.external).

2. Az irányítópulton válassza a **minden erőforrás**lehetőséget, válassza ki a korábban létrehozott kulcstartót, majd válassza a **kulcsok** csempét.

3. A **kulcsok** ablaktáblán válassza a **készítés/importálás**lehetőséget.

4. A **kulcs létrehozása** ablaktáblán a **Beállítások**listájából válassza ki a kulcs létrehozásához használni kívánt módszert. **Létrehozhat egy új** kulcsot, **feltöltheti** a meglévő kulcsot, vagy használhatja a **visszaállítás biztonsági mentést** a kulcsok biztonsági másolatának kiválasztásához.

5. Adja meg a kulcs **nevét** . A kulcs neve csak alfanumerikus karaktereket és kötőjel (-) karaktert tartalmazhat.

6. Megadhatja az **aktiválási dátum beállítása** és a kulcs **lejárati dátumának** beállítása beállítást is.

7. A telepítés elindításához válassza a **Létrehozás** lehetőséget.

A kulcs sikeres létrehozása után kiválaszthatja a **kulcsok** területen, és megtekintheti vagy módosíthatja a tulajdonságait. A Properties (Tulajdonságok) szakasz tartalmazza a **kulcs azonosítóját**, amely egy Uniform Resource Identifier (URI), amelyet a külső alkalmazások a kulcs eléréséhez használnak. A kulcs műveleteinek korlátozásához konfigurálja a beállításokat az **engedélyezett műveletek**területen.

![URI-kulcs](media/azure-stack-key-vault-manage-portal/image4.png)

### <a name="create-a-secret"></a>Titkos kulcs létrehozása

1. Jelentkezzen be a [felhasználói portálra](https://portal.local.azurestack.external).

2. Az irányítópulton válassza a **minden erőforrás**lehetőséget, válassza ki a korábban létrehozott kulcstartót, majd válassza a **titkok** csempét.

3. A **titkok**területen válassza a **Hozzáadás**lehetőséget.

4. A **titkos kulcs létrehozása**területen a **feltöltési beállítások**listájából válassza ki azt a lehetőséget, amellyel titkos kulcsot szeretne létrehozni. Ha a titkos kulcs értékét adja meg, vagy egy **tanúsítványt** tölt fel a helyi gépről, **manuálisan** is létrehozhatja a titkos kulcsot.

5. Adja meg a titok **nevét** . A titkos név csak alfanumerikus karaktereket és a kötőjel (-) karaktert tartalmazhatja.

6. Szükség esetén megadhatja a **tartalomtípust**, és konfigurálhatja az **aktiválási dátum beállítása** és a titkos kód **lejárati dátuma** beállítás értékét.

7. A telepítés elindításához válassza a **Létrehozás** lehetőséget.

A titkos kód sikeres létrehozása után kiválaszthatja a **titkok** szakaszban, és megtekintheti vagy módosíthatja a tulajdonságait. A **Titkos azonosító** egy URI, amelyet a külső alkalmazások használhatnak a titkos kulcs eléréséhez.

![URI-titok](media/azure-stack-key-vault-manage-portal/image5.png)

## <a name="next-steps"></a>Következő lépések

* [Virtuális gép üzembe helyezése a Key Vaultban tárolt jelszó beolvasásával](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Key Vault-ben tárolt tanúsítvánnyal rendelkező virtuális gép üzembe helyezése](azure-stack-key-vault-push-secret-into-vm.md)
