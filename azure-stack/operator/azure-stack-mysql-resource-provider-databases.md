---
title: MySQL-adatbázisok létrehozása Azure Stack központban
description: Megtudhatja, hogyan hozhat létre és kezelhet MySQL-adatbázisokat az Azure Stack hub MySQL-adapter erőforrás-szolgáltatójának használatával.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 2da344a5699357d008fcc4494992cfcc67047971
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698878"
---
# <a name="create-mysql-databases-in-azure-stack-hub"></a>MySQL-adatbázisok létrehozása Azure Stack központban
A MySQL-adatbázis szolgáltatást tartalmazó ajánlatra előfizetett Azure Stack hub-felhasználó önkiszolgáló MySQL-adatbázisokat hozhat létre és kezelhet a felhasználói portálon.

## <a name="create-a-mysql-database"></a>MySQL-adatbázis létrehozása

1. Jelentkezzen be az Azure Stack hub felhasználói portálra.
2. Válassza **az + erőforrás** > létrehozása **+ tároló** > **MySQL-adatbázis** > **hozzáadása**elemet.
3. A **MySQL-adatbázis létrehozása**területen adja meg az adatbázis nevét, és konfigurálja a környezetéhez szükséges egyéb beállításokat.

    ![Teszt MySQL-adatbázis létrehozása](./media/azure-stack-mysql-rp-deploy/mysql-create-db-a.png)

4. Az **adatbázis létrehozása**területen válassza az **SKU**lehetőséget. A **MySQL SKU kiválasztása**területen válassza ki az adatbázishoz tartozó SKU-t.

    ![MySQL SKU kiválasztása](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Mivel az üzemeltetési kiszolgálókat Azure Stack hubhoz adja hozzá, a rendszer hozzárendel egy SKU-t. Az adatbázisok az SKU-ban üzemeltetett kiszolgálók készletében jönnek létre.

5. A **Bejelentkezés**területen válassza a ***kötelező beállítások konfigurálása***elemet.
6. A **Bejelentkezés kiválasztása**területen válasszon egy meglévő felhasználónevet, vagy válassza az **+ új bejelentkezés létrehozása** lehetőséget egy új bejelentkezés beállításához.  Adja meg az **adatbázis bejelentkezési** nevét és **jelszavát**, majd kattintson **az OK gombra**.

    ![Új adatbázis-bejelentkezés létrehozása](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Az adatbázis-bejelentkezési név hossza nem haladhatja meg a 32 karaktert a MySQL 5,7-ben. A korábbi kiadásokban nem lehet hosszabb 16 karakternél.

7. Válassza a **Létrehozás** lehetőséget az adatbázis beállításának befejezéséhez.

Az adatbázis üzembe helyezését követően jegyezze fel a **kapcsolatok karakterláncot** az **alapvető**erőforrások területen. Ezt a sztringet bármely olyan alkalmazásban használhatja, amelynek hozzá kell férnie a MySQL-adatbázishoz.

![A MySQL-adatbázishoz tartozó kapcsolatok karakterláncának beolvasása](./media/azure-stack-mysql-rp-deploy/mysql-db-created-a.png)

## <a name="update-the-administrative-password"></a>A rendszergazdai jelszó frissítése

A jelszót a MySQL-kiszolgáló példányán módosítva módosíthatja.

1. Válassza a **felügyeleti erőforrások** > **MySQL üzemeltetési kiszolgálók**elemet. Válassza ki az üzemeltetési kiszolgálót.
2. A **Beállítások**területen válassza a **jelszó**lehetőséget.
3. A **jelszó**területen adja meg az új jelszót, majd kattintson a **Mentés**gombra.

![A rendszergazdai jelszó frissítése](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [használhatók a kiválóan elérhető MySQL-adatbázisok](azure-stack-tutorial-mysql.md).
