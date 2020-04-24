---
title: Az egyszerű szolgáltatásnév hitelesítő adatainak tárolása Azure Stack központban Key Vault
description: Ismerje meg, hogyan tárolja Key Vault a szolgáltatás egyszerű hitelesítő adatait Azure Stack hub-on
author: sethmanheim
ms.topic: article
ms.date: 01/24/2020
ms.author: sethm
ms.lastreviewed: 01/16/2020
ms.openlocfilehash: 66d69fa3397fa748dfbc67377c3e52c47f35904c
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77702720"
---
# <a name="store-service-principal-credentials-in-azure-stack-hub-key-vault"></a>Az egyszerű szolgáltatásnév hitelesítő adatainak tárolása Azure Stack központban Key Vault

Az Azure Stack hub-on lévő alkalmazások fejlesztéséhez általában egyszerű szolgáltatásnevet kell létrehoznia, és ezeket a hitelesítő adatokat kell használnia a telepítés előtt történő hitelesítéshez. Előfordulhat azonban, hogy elveszíti az egyszerű szolgáltatásnév tárolt hitelesítő adatait. Ez a cikk azt ismerteti, hogyan hozható létre egy egyszerű szolgáltatásnév, és hogyan tárolhatja Azure Key Vaultban az értékeket későbbi lekéréshez.

További információ a Key Vaultről: a [Azure stack Hub Key Vault bemutatása](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Key Vault szolgáltatást tartalmazó ajánlat előfizetése.
- A PowerShell telepítve van és konfigurálva van Azure Stack hubhoz való használatra.

## <a name="key-vault-in-azure-stack-hub"></a>Key Vault az Azure Stack hub-ban

A Azure Stack hub Key Vault a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kódok védelmét segíti. A Key Vault használatával titkosíthatja a kulcsokat és a titkokat.

Key Vault létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Stack hub portálra.

2. Az irányítópulton válassza az **+ erőforrás létrehozása**, majd a **Biztonság és Identitáskezelés**, majd a **Key Vault lehetőséget.**

   ![Kulcstároló létrehozása](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. A **Key Vault létrehozása** panelen rendeljen hozzá egy **nevet** a tárolóhoz. A tárolók nevei csak alfanumerikus karaktereket és kötőjelet (-) tartalmazhatnak. Nem kezdődhet számmal.

4. Válasszon egy előfizetést az elérhető előfizetések listájáról.

5. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.

6. Válassza ki a tarifacsomagot.

7. Válasszon egy meglévő hozzáférési szabályzatot, vagy hozzon létre egy újat. A hozzáférési házirend lehetővé teszi, hogy egy felhasználó, alkalmazás vagy biztonsági csoport számára engedélyeket biztosítson a tárolóval kapcsolatos műveletek elvégzéséhez.

8. Igény szerint a szolgáltatásokhoz való hozzáférés engedélyezéséhez válasszon egy speciális hozzáférési szabályzatot.

9. A beállítások konfigurálása után válassza **az OK**, majd a **Létrehozás**lehetőséget. A Key Vault üzembe helyezése megkezdődik.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

1. Jelentkezzen be az Azure-fiókjába a Azure Portalon keresztül.

2. Válassza a **Azure Active Directory**, majd a **Alkalmazásregisztrációk**, majd a **Hozzáadás**lehetőséget.

3. Adja meg az alkalmazás nevét és URL-címét. Válassza ki a létrehozni kívánt alkalmazás típusához a **Web App/API** vagy a **natív** lehetőséget. Az értékek beállítása után válassza a **Létrehozás**lehetőséget.

4. Válassza a **Active Directory**, majd az alkalmazások **regisztrációja**lehetőséget, majd válassza ki az alkalmazást.

5. Másolja az **alkalmazás azonosítóját** , és tárolja azt az alkalmazás kódjában. A minta alkalmazások az **ügyfél-azonosítót** használják az **alkalmazás-azonosítóra**való hivatkozáskor.

6. A hitelesítési kulcs létrehozásához válassza a **Kulcsok** elemet.

7. Adja meg a kulcs leírását és időtartamát.

8. Kattintson a **Mentés** gombra.

9. Másolja az elérhetővé váló **kulcsot** a **Mentés**gombra való kattintás után.

## <a name="store-the-service-principal-inside-key-vault"></a>Az egyszerű szolgáltatásnév tárolása Key Vaulton belül

1. Jelentkezzen be Azure Stack hub felhasználói portálra, majd válassza ki a korábban létrehozott kulcstartót, majd válassza a **Secret (titkos** ) csempét.

2. A **titok** ablaktáblán válassza a **készítés/importálás**lehetőséget.

3. A **titkos kulcs létrehozása** panelen válassza a **manuális** lehetőséget a lehetőségek listájából. Ha a szolgáltatásnevet tanúsítványok használatával hozta létre, válassza ki a tanúsítványokat a legördülő listából, majd töltse fel a fájlt.

4. Adja meg az **alkalmazás azonosítóját** a kulcs neveként. A kulcs neve csak alfanumerikus karaktereket és kötőjel (-) karaktert tartalmazhat.

5. Illessze be a kulcs értékét az egyszerű szolgáltatásból a Value ( **érték** ) lapra.

6. Válasszon **szolgáltatásnevet a** **tartalomtípushoz**.

7. Állítsa be az **aktiválási dátum** és a **lejárati dátum** értékét a kulcshoz.

8. A telepítés elindításához válassza a **Létrehozás** lehetőséget.

A titkos kód sikeres létrehozása után az egyszerű szolgáltatásnév adatait itt tárolja. A **titkok**szakaszban bármikor kiválaszthatja, és megtekintheti vagy módosíthatja a tulajdonságait. A **Properties (Tulajdonságok** ) szakasz tartalmazza a titkos azonosítót, amely egy Uniform Resource Identifier (URI), amelyet a külső alkalmazások a titkos kulcs eléréséhez használnak.

## <a name="next-steps"></a>További lépések

- [Egyszerű szolgáltatások használata](azure-stack-create-service-principals.md)
- [Az Azure Stack hub Key Vault kezelése a portálon](azure-stack-key-vault-manage-portal.md)  
- [Azure Stack hub Key Vault kezelése a PowerShell használatával](azure-stack-key-vault-manage-powershell.md)
