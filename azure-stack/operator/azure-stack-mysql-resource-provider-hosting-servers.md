---
title: MySQL üzemeltetési kiszolgálók hozzáadása a Azure Stack-ben | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá MySQL-üzemeltetési kiszolgálókat a MySQL-adapter erőforrás-szolgáltatóján keresztül történő üzembe helyezéshez.
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
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 897cb42ad2a84f3802f4d35e97a03d4976800121
ms.sourcegitcommit: bbe1048682c7dccc6cebde542462c14ee1f3d0d1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75677875"
---
# <a name="add-mysql-hosting-servers-in-azure-stack"></a>MySQL üzemeltetési kiszolgálók hozzáadása a Azure Stack

A virtuális gépen (VM) üzemeltetheti a MySQL üzemeltetési kiszolgáló példányát [Azure stack](azure-stack-overview.md)vagy a Azure stack-környezeten kívüli virtuális gépen, ha a MySQL erőforrás-szolgáltató csatlakozni tud a példányhoz.

> [!NOTE]
> A MySQL erőforrás-szolgáltatót az alapértelmezett szolgáltatói előfizetésben kell létrehozni, míg a MySQL üzemeltetési kiszolgálókat számlázva, felhasználói előfizetésekben kell létrehozni. Az erőforrás-szolgáltatói kiszolgálót nem szabad használni a felhasználói adatbázisok üzemeltetéséhez.

Az üzemeltetési kiszolgálók esetében a 5,6, 5,7 és 8,0 MySQL-verziók is használhatók. A MySQL RP nem támogatja a caching_sha2_password hitelesítést; a következő kiadásban lesz hozzáadva. A MySQL 8,0-kiszolgálókat mysql_native_password használatára kell konfigurálni. A MariaDB is támogatott.

## <a name="connect-to-a-mysql-hosting-server"></a>Kapcsolódás MySQL üzemeltetési kiszolgálóhoz

Ellenőrizze, hogy rendelkezik-e a rendszergazdai jogosultságokkal rendelkező fiók hitelesítő adataival.

> [!NOTE]
> A MySQL 8,0-es és újabb verzióiban a távoli hozzáférés alapértelmezés szerint nincs engedélyezve. Létre kell hoznia egy új felhasználói fiókot, és biztosítania kell a távoli hozzáférés previledge a felhasználói fiókhoz, mielőtt kiszolgálóként hozzáadja azt.

Üzemeltetési kiszolgáló hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a Azure Stack felügyeleti portálra szolgáltatás-rendszergazdaként.
2. Válassza az **Összes szolgáltatás** elemet.
3. A **felügyeleti erőforrások** kategóriában válassza a **MySQL üzemeltetési kiszolgálók** >  **+ Hozzáadás**lehetőséget. Megnyílik a **MySQL üzemeltetési kiszolgáló hozzáadása** párbeszédpanel, amely az alábbi képernyőfelvételen látható.

   ![MySQL üzemeltetési kiszolgáló konfigurálása](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Adja meg a MySQL-kiszolgáló példányának kapcsolati adatait.

   * A **MySQL üzemeltetési kiszolgáló neve mezőben**adja meg a teljes tartománynevet (FQDN) vagy egy érvényes IPv4-címeket. Ne használja a rövid virtuális gép nevét.
   * A Azure Stack piactéren elérhető Bitnami MySQL-lemezképek alapértelmezett rendszergazdai **felhasználóneve** a *root*.
   * Ha nem ismeri a legfelső szintű **jelszót**, tekintse meg a [Bitnami dokumentációját](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials) , amelyből megtudhatja, hogyan kérheti le.
   * Nincs megadva alapértelmezett MySQL-példány, ezért az **üzemeltetési kiszolgáló méretét GB-ban**kell megadnia. Adjon meg egy olyan méretet, amely az adatbázis-kiszolgáló kapacitásához közeledik.
   * Az **előfizetés**alapértelmezett beállításának megtartása.
   * **Erőforráscsoport**esetén hozzon létre egy újat, vagy használjon egy meglévő csoportot.

   > [!NOTE]
   > Ha a MySQL-példányt a bérlő és a felügyeleti Azure Resource Manager is elérheti, azt az erőforrás-szolgáltató felügyelete alá helyezheti. A MySQL **-példányt azonban** kizárólag az erőforrás-szolgáltatóhoz kell lefoglalni.

5. Válassza az **SKU** -t az **SKU létrehozása** párbeszédpanel megnyitásához.

   ![MySQL SKU létrehozása](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   Az SKU **nevének** tükröznie kell az SKU tulajdonságait, hogy a felhasználók a megfelelő SKU-ban tudják telepíteni az adatbázisaikat.

6. Az SKU létrehozásához kattintson **az OK gombra** .
   > [!NOTE]
   > A SKU akár egy órát is igénybe vehet, hogy megjelenjenek a portálon. Nem hozható létre adatbázis az SKU üzembe helyezése és futtatása előtt.

7. **A MySQL üzemeltetési kiszolgáló hozzáadása**területen válassza a **Létrehozás**lehetőséget.

A kiszolgálók hozzáadásakor rendeljen hozzá egy új vagy egy meglévő SKU-t a szolgáltatási ajánlatok megkülönböztetéséhez. Létrehozhat például egy MySQL nagyvállalati példányt, amely fokozott adatbázist és automatikus biztonsági mentést biztosít. Ezt a nagy teljesítményű kiszolgálót a szervezet különböző osztályai számára is fenntarthatja.

## <a name="security-considerations-for-mysql"></a>Biztonsági megfontolások a MySQL-hez

Az alábbi információk az RP-és MySQL-üzemeltetési kiszolgálókra vonatkoznak:

* Győződjön meg arról, hogy az összes üzemeltetési kiszolgáló konfigurálva van a TLS 1,1 használatával történő kommunikációra. Lásd: [a MySQL beállítása titkosított kapcsolatok használatára](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* [Transzparens adattitkosítás](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html)alkalmaz.
* A MySQL RP nem támogatja a caching_sha2_password hitelesítést.

## <a name="increase-backend-database-capacity"></a>Háttérbeli adatbázis kapacitásának bővítése

A háttérbeli adatbázis kapacitása a Azure Stack portálon további MySQL-kiszolgálók üzembe helyezésével növelhető. Adja hozzá ezeket a kiszolgálókat egy új vagy egy meglévő SKU-hoz. Ha hozzáad egy kiszolgálót egy meglévő SKU-hoz, győződjön meg arról, hogy a kiszolgáló jellemzői ugyanazok, mint az SKU többi kiszolgálója.

## <a name="sku-notes"></a>SKU-megjegyzések
Használjon olyan SKU-nevet, amely leírja az SKU-ban található kiszolgálók képességeit, például a kapacitást és a teljesítményt. A név segítséget nyújt arra, hogy a felhasználók a megfelelő SKU-ban telepíthessék az adatbázisaikat. Az alábbi jellemzőkkel rendelkezhet például az SKU-nevek használatával a szolgáltatási ajánlatok megkülönböztetéséhez:
  
* nagy kapacitás
* nagy teljesítmény
* magas rendelkezésre állás

Az ajánlott eljárás szerint az SKU-ban lévő összes üzemeltetési kiszolgálónak ugyanazzal az erőforrással és teljesítménnyel kapcsolatos tulajdonságokkal kell rendelkeznie.

A SKU-t nem lehet hozzárendelni meghatározott felhasználókhoz vagy csoportokhoz.

Az SKU szerkesztéséhez lépjen a **minden szolgáltatás** > **MySQL-adapter** > **SKU**-ra. Válassza ki a módosítandó SKU-t, végezze el a szükséges módosításokat, majd kattintson a **Mentés** gombra a módosítások mentéséhez. 

A már nem szükséges SKU törléséhez lépjen a **minden szolgáltatás** > **MySQL-adapter** > **SKU**-k elemre. Kattintson a jobb gombbal az SKU nevére, és válassza a **Törlés** lehetőséget a törléshez.

> [!IMPORTANT]
> Akár egy óráig is eltarthat, amíg az új SKU elérhetővé válik a felhasználói portálon.

## <a name="make-mysql-database-servers-available-to-your-users"></a>A MySQL adatbázis-kiszolgálók elérhetővé tétele a felhasználók számára

Terveket és ajánlatokat hozhat létre a MySQL adatbázis-kiszolgálók felhasználók számára történő elérhetővé tételéhez. Adja hozzá a Microsoft. MySqlAdapter szolgáltatást a csomaghoz, és hozzon létre egy új kvótát. A MySQL nem teszi lehetővé az adatbázisok méretének korlátozását.

> [!IMPORTANT]
> Akár két óráig is eltarthat, amíg az új kvóták elérhetővé válnak a felhasználói portálon, vagy a módosított kvóta érvénybe léptetése előtt.

## <a name="next-steps"></a>Következő lépések

[MySQL-adatbázis létrehozása](azure-stack-mysql-resource-provider-databases.md)
