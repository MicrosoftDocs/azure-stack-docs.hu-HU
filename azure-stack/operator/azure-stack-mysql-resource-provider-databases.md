---
title: A MySQL-adapter (RP) által biztosított adatbázisok használata Azure Stackon | Microsoft Docs
description: A MySQL-adapter erőforrás-szolgáltatójának használatával kiépített MySQL-adatbázisok létrehozása és kezelése
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 594d1f45e19717bdbbc5f9fee56cf253c03b6efb
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283468"
---
# <a name="create-mysql-databases"></a>MySQL-adatbázisok létrehozása
A MySQL-adatbázis szolgáltatást tartalmazó ajánlatra előfizetett Azure Stack felhasználó a felhasználói portálon hozhat létre és kezelhet önkiszolgáló MySQL-adatbázisokat.

## <a name="create-a-mysql-database"></a>MySQL-adatbázisok létrehozása

1. Jelentkezzen be a Azure Stack felhasználói portálra.
2. Válassza az **+ erőforrás létrehozása** > **adattároló** > **MySQL-adatbázis** > **Hozzáadás**lehetőséget.
3. A **MySQL-adatbázis létrehozása**területen adja meg az adatbázis nevét, és konfigurálja a környezetéhez szükséges egyéb beállításokat.

    ![Teszt MySQL-adatbázis létrehozása](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Az **adatbázis létrehozása**területen válassza az **SKU**lehetőséget. A **MySQL SKU kiválasztása**területen válassza ki az adatbázishoz tartozó SKU-t.

    ![MySQL SKU kiválasztása](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Mivel a szolgáltatói kiszolgálók hozzáadódnak a Azure Stackhoz, egy SKU-t kapnak hozzájuk. Az adatbázisok az SKU-ban üzemeltetett kiszolgálók készletében jönnek létre.

5. A **Bejelentkezés**területen válassza a ***kötelező beállítások konfigurálása***elemet.
6. A **Bejelentkezés kiválasztása**területen válasszon egy meglévő felhasználónevet, vagy válassza az **+ új bejelentkezés létrehozása** lehetőséget egy új bejelentkezés beállításához.  Adja meg az **adatbázis bejelentkezési** nevét és **jelszavát**, majd kattintson **az OK gombra**.

    ![Új adatbázis-bejelentkezés létrehozása](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Az adatbázis-bejelentkezési név hossza nem haladhatja meg a 32 karaktert a MySQL 5,7-ben. A korábbi kiadásokban nem lehet hosszabb 16 karakternél.

7. Válassza a **Létrehozás** lehetőséget az adatbázis beállításának befejezéséhez.

Az adatbázis üzembe helyezését követően jegyezze fel a **kapcsolatok karakterláncot** az **alapvető**erőforrások területen. Ezt a sztringet bármely olyan alkalmazásban használhatja, amelynek hozzá kell férnie a MySQL-adatbázishoz.

![A MySQL-adatbázishoz tartozó kapcsolatok karakterláncának beolvasása](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>A rendszergazdai jelszó frissítése

A jelszót a MySQL-kiszolgáló példányán módosítva módosíthatja.

1. Válassza a **felügyeleti erőforrások**@no__t – 1**MySQL üzemeltetési kiszolgálók**elemet. Válassza ki az üzemeltetési kiszolgálót.
2. A **Beállítások**területen válassza a **jelszó**lehetőséget.
3. A **jelszó**területen adja meg az új jelszót, majd kattintson a **Mentés**gombra.

![A rendszergazdai jelszó frissítése](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [kínálhat kiválóan elérhető MySQL-adatbázisokat](azure-stack-tutorial-mysql.md)
