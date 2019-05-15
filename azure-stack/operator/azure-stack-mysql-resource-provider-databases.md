---
title: A MySQL-Adapter RP az AzureStack által biztosított adatbázisok használata |} A Microsoft Docs
description: Hogyan hozhat létre és kezelheti a MySQL-adatbázisok, a MySQL Adapter erőforrás-szolgáltató használatával építettek ki
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
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 4cf406461c7a231617d40c50d2b5491bb4427b63
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618636"
---
# <a name="create-mysql-databases"></a>MySQL-adatbázisok létrehozása
Az Azure Stack felhasználói, az előfizetett egy ajánlatra, amely tartalmazza a MySQL-adatbázisszolgáltatás, létrehozhat és kezelheti a MySQL-adatbázisok önkiszolgáló a felhasználói portálon.

## <a name="create-a-mysql-database"></a>MySQL-adatbázisok létrehozása

1. Jelentkezzen be az Azure Stack felhasználói portálon.
2. Válassza ki **+ erőforrás létrehozása** > **adatok + tárolás** > **MySQL-adatbázis** > **Hozzáadás**.
3. A **MySQL-adatbázis létrehozása**, adja meg az adatbázis nevét, és az egyéb beállításokat állítsa be a környezetéhez szükséges.

    ![Hozzon létre egy tesztet a MySQL-adatbázis](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. A **Create Database**válassza **Termékváltozat**. A **válassza ki a MySQL Termékváltozatot**, válassza ki az adatbázis a Termékváltozat.

    ![Válassza ki a MySQL a Termékváltozat](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Azure Stack üzemeltetési kiszolgáló van hozzáadva, mert hozzá vannak rendelve a Termékváltozat. Az üzemeltető kiszolgálók a Termékváltozat a készletben található adatbázisok jönnek létre.

5. A **bejelentkezési**válassza ***kötelező beállítások konfigurálása***.
6. Alatt **válassza ki a bejelentkezési**, választhat egy meglévő bejelentkezést, vagy válasszon **+ új bejelentkezés létrehozása** új bejelentkezés beállításához.  Adjon meg egy **adatbázis bejelentkezési** neve és **jelszó**, majd válassza ki **OK**.

    ![Hozzon létre egy új adatbázis-bejelentkezés](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Az adatbázis-bejelentkezési név hossza legfeljebb 32 karakter hosszú lehet a MySQL 5.7-es. Korábbi kiadások legfeljebb 16 karakter.

7. Válassza ki **létrehozás** az adatbázis beállításának befejezéséhez.

Az adatbázis telepítése után jegyezze fel a **kapcsolati karakterlánc** alatt **Essentials**. Ez a karakterlánc használhatja bármely olyan alkalmazásban, el kell érnie a MySQL-adatbázishoz.

![A MySQL-adatbázis a kapcsolati sztring lekérése](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>A rendszergazdai jelszó frissítése

A jelszót a MySQL server-példányon módosításával módosíthatja.

1. Válassza ki **felügyeleti erőforrások** > **üzemeltető kiszolgálók MySQL**. Válassza ki az üzemeltető kiszolgálót.
2. A **beállítások**válassza **jelszó**.
3. A **jelszó**, adja meg az új jelszót, majd **mentése**.

![A rendszergazdai jelszó frissítése](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>További lépések

[A MySQL típusú erőforrás-szolgáltató frissítése](azure-stack-mysql-resource-provider-update.md)
