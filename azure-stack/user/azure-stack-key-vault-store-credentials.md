---
title: Az Azure Stack store egyszerű szolgáltatás hitelesítő adatai a Key Vault |} A Microsoft Docs
description: Ismerje meg, hogyan Key Vault tárolja az egyszerű szolgáltatás hitelesítő adatai az Azure Stackben
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: sethm
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 1a9fc71f6e57621dba4488821ea2ca8b1e119e48
ms.sourcegitcommit: 6fcd5df8b77e782ef72f0e1419f1f75ec8c16c04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991311"
---
# <a name="store-service-principal-credentials-in-key-vault"></a>Egyszerű szolgáltatás hitelesítő adatai Store a Key Vaultban

Általában az Azure Stack-alkalmazások fejlesztéséhez használható kell létrehozni egy szolgáltatásnevet, és ezeket a hitelesítő adatokat az üzembe helyezés előtt hitelesítést. Azonban gyakran szolgáltatásnév számára a tárolt hitelesítő adatok vannak elveszett. Ez a cikk ismerteti a szolgáltatásnév létrehozásához, és tárolja az értékeket az Azure Key Vaultban a későbbi beolvasásához.

A Key Vaulttal kapcsolatos további információkért lásd: [Ez a cikk](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Előfeltételek

- Előfizetés egy ajánlatra, amely magában foglalja az Azure Key Vault szolgáltatást.
- PowerShell az Azure Stackkel való használathoz van konfigurálva.

## <a name="key-vault-in-azure-stack"></a>A Key Vault az Azure Stackben

A Key Vault az Azure Stack segítségével biztosítja a titkosítási kulcsok védelmét, és a titkos kulcsok, amelyek felhőalapú alkalmazásokat és szolgáltatásokat használja. A Key Vault használatával a kulcsok és titkos kulcsok használatával titkosítsa.

Hozzon létre egy kulcstartót, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Stack portálon.

2. Az irányítópulton, válassza ki a **+ erőforrás létrehozása**, majd **biztonság + identitás**, majd **Key Vault.**

   ![Kulcstartó létrehozása](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. Az a **kulcstartó létrehozása** panelen rendelje hozzá egy **neve** a tároló számára. Tároló neve csak alfanumerikus karaktereket és kötőjelet (-) karaktert tartalmazhat. Egy szám, nem kezdődhet.

4. Válasszon egy előfizetést az elérhető előfizetések listájáról.

5. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.

6. Válassza ki a tarifacsomagot.

7. Válasszon egyet a meglévő hozzáférési szabályzatot, vagy hozzon létre egy újat. Hozzáférési házirend lehetővé teszi a felhasználó, alkalmazás vagy egy biztonsági csoportot, ez a kulcstartó-műveletek végrehajtásához engedélyeit.

8. Igény szerint válassza ki a speciális hozzáférési házirend engedélyezése funkciókhoz is hozzáférést.

9. Miután konfigurálta a beállításokat, jelölje ki **OK**, majd válassza ki **létrehozása**. A key vault üzembe helyezési kezdődik.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

1. Jelentkezzen be az Azure portal segítségével Azure-fiókjával.

2. Válassza ki **Azure Active Directory**, majd **alkalmazásregisztrációk**, majd **Hozzáadás**.

3. Adja meg az alkalmazás nevét és URL-címét. Válassza ki vagy **webalkalmazás / API** vagy **natív** szeretne létrehozni az alkalmazás számára. Miután beállította az értékeket, válassza ki a **létrehozás**.

4. Válassza ki **Active Directory**, majd **Alkalmazásregisztrációk**, és válassza ki az alkalmazását.

5. Másolja ki az **Alkalmazásazonosítót**, és tárolja az alkalmazás kódjában. Az alkalmazások a mintaalkalmazások használatához **ügyfél-azonosító** kontextusban való megnevezésekor a **Alkalmazásazonosító**.

6. A hitelesítési kulcs létrehozásához válassza a **Kulcsok** elemet.

7. Adjon meg egy leírást a kulcs és a egy időtartamot.

8. Kattintson a **Mentés** gombra.

9. Másolás a **kulcs** , amely után válik elérhetővé, amire kattintott **mentése**.

## <a name="store-the-service-principal-inside-key-vault"></a>A szolgáltatásnév a Key vaultban Store

1. Jelentkezzen be a felhasználói portál az Azure Stack, majd válassza ki a kulcstartóhoz, amely a korábban létrehozott, és válassza a **titkos** csempére.

2. Az a **titkos** ablaktáblán válassza előbb **létrehozás/importálás**.

3. Az a **titkos kulcs létrehozása** panelen válassza a beállítások listájában **manuális**. Ha a szolgáltatás egyszerű létrehozott tanúsítványokkal, válassza ki a tanúsítványokat a legördülő listából, és majd feltöltjük a fájlt.

4. Adja meg **az Alkalmazásazonosítót** a kulcs neveként az egyszerű szolgáltatás átmásolva. A kulcs neve csak alfanumerikus karaktereket és kötőjelet (-) karaktert tartalmazhat.

5. Illessze be az egyszerű szolgáltatás a kulcs értékét a **érték** fülre.

6. Válassza ki **szolgáltatásnév** számára a **tartalomtípus**.

7. Állítsa be a **aktiválási dátumot** és **lejárati dátum** a kulcs értékeit.

8. Válassza ki **létrehozás** a üzembe helyezésének megkezdéséhez.

A titkos kód sikeres létrehozása után a szolgáltatásnév adatait ott tárolhatók. Kiválaszthatja azt bármikor alatt **titkok**, és megtekintheti vagy a hozzá tartozó tulajdonságok módosításával. A Tulajdonságok szakaszának tartalma a titkos kulcs azonosítóját, amely egy egységes erőforrás-azonosító (URI), amellyel külső alkalmazások hozzáférhetnek a titkos kód.

## <a name="next-steps"></a>További lépések

- [Szolgáltatásnevek használata](azure-stack-create-service-principals.md)
- [A portál által kezelése a Key Vault az Azure Stackben](azure-stack-key-vault-manage-portal.md)  
- [A Key Vault az Azure Stackben kezelése a PowerShell használatával](azure-stack-key-vault-manage-powershell.md)