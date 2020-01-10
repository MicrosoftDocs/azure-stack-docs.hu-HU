---
title: SQL-adatbázisok létrehozása
titleSuffix: Azure Stack Hub
description: Ismerje meg, hogyan hozhat létre és kezelhet SQL-erőforrás-szolgáltatói adapter használatával kiépített SQL-adatbázisokat.
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
ms.openlocfilehash: f6c030767ba64aa3c8acd47d5b358a4b385785ac
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814541"
---
# <a name="create-sql-databases"></a>SQL-adatbázisok létrehozása

Önkiszolgáló adatbázisokat a felhasználói portálon hozhat létre és kezelhet. Az Azure Stack hub-felhasználóknak olyan előfizetésre van szükségük, amely tartalmazza az SQL Database szolgáltatást.

1. Jelentkezzen be az [Azure stack hub](azure-stack-overview.md) felhasználói portálra.

2. Válassza az **+ új** &gt;**adattároló** &gt; **SQL Server adatbázis** &gt; **Hozzáadás**lehetőséget.

3. Az **adatbázis létrehozása**területen adja meg a szükséges adatokat, például az **adatbázis nevét** és **a maximális méretet MB-ban**.

   >[!NOTE]
   >Az adatbázis méretének legalább 64 MB-nak kell lennie, amelyet az adatbázis üzembe helyezése után növelheti.

   Konfigurálja a környezetéhez szükséges egyéb beállításokat.

4. Az **adatbázis létrehozása**területen válassza az **SKU**lehetőséget. Az **SKU kiválasztása**területen válassza ki az adatbázishoz tartozó SKU-t.

   ![Adatbázis létrehozása Azure Stack hub felhasználói portálon.](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Mivel az üzemeltetési kiszolgálókat Azure Stack hubhoz adja hozzá, a rendszer hozzárendel egy SKU-t. Az adatbázisok az SKU-ban üzemeltetett kiszolgálók készletében jönnek létre.

5. Válassza a **Bejelentkezés**lehetőséget.

6. A **Bejelentkezés kiválasztása**területen válasszon egy meglévő felhasználónevet, vagy válassza az **+ új bejelentkezés létrehozása**lehetőséget.

7. Az **új bejelentkezés**területen adja meg az **adatbázis-bejelentkezés** és a **jelszó**nevét.

   >[!NOTE]
   >Ezek a beállítások az adatbázishoz való hozzáféréshez létrehozott SQL-hitelesítési hitelesítő adatok. A bejelentkezési felhasználónévnek globálisan egyedinek kell lennie. A bejelentkezési beállításokat felhasználhatja más olyan adatbázisok esetén, amelyek ugyanazt az SKU-t használják.

   ![Új adatbázis-bejelentkezés létrehozása Azure Stack hub felhasználói portálon](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Az adatbázis központi telepítésének befejezéséhez kattintson **az OK gombra** .

Jegyezze fel a **kapcsolatok sztringjét**az **alapok**területen, amely az adatbázis központi telepítése után jelenik meg. Ezt a sztringet bármely olyan alkalmazásban használhatja, amelynek hozzá kell férnie az SQL Server-adatbázishoz.

![A SQL Server adatbázishoz tartozó kapcsolatok karakterláncának beolvasása](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL always on Databases

A tervezés szerint az Always On adatbázisokat a különálló kiszolgálói környezetekben eltérően kezelik. További információ: [SQL Server always on rendelkezésre állási csoportok bemutatása az Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)szolgáltatásban.

### <a name="verify-sql-always-on-databases"></a>SQL AlwaysOn adatbázisok ellenőrzése

A következő képernyőfelvétel azt mutatja be, hogyan használható a SQL Server Management Studio az SQL always on adatbázis-állapotának megkereséséhez.

![AlwaysOn-adatbázis állapota SQL Server Management Studio](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

A always on Databases-nek **szinkronizált** és elérhetőnek kell lennie az összes SQL-példányon, és megjelenik a **rendelkezésre állási csoportokban**. Az előző képernyőképen az adatbázis például newdb1, és az állapota **newdb1 (szinkronizálva)** .

### <a name="delete-an-alwayson-database"></a>AlwaysOn-adatbázis törlése

Ha SQL always on adatbázist töröl az erőforrás-szolgáltatóból, az SQL törli az adatbázist az **elsődleges** replikából és a rendelkezésre állási csoportból.

Az SQL ezután visszaállítja az adatbázist a többi replikán lévő **visszaállítási** állapotba, és csak akkor dobja el az adatbázist, ha az aktiválva van. Ha az adatbázis nincs elvetve, a másodlagos replikák **nem szinkronizálási** állapotba kerülnek.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [használhatók a magasan elérhető SQL-adatbázisok](azure-stack-tutorial-sql.md)
