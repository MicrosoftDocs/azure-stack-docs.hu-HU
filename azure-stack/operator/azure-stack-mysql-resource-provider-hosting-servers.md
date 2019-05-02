---
title: A MySQL üzemeltető kiszolgálók az Azure Stackben |} A Microsoft Docs
description: MySQL-példányok az üzembe helyezés a MySQL Adapter erőforrás-szolgáltató hozzáadása
services: azure-stack
documentationCenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/26/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: quying
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: b563bc542c970fa71b9718ac070e2258abb80930
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64293882"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Üzemeltetési kiszolgáló hozzáadása a MySQL erőforrás-szolgáltató

A MySQL server-példány egy virtuális gépen (VM) futtató üzemeltethet a [Azure Stack](azure-stack-overview.md), vagy egy virtuális Gépen az Azure Stack környezettel, amennyiben a MySQL erőforrás-szolgáltató csatlakozhat a példány kívül.

> [!NOTE]
> A MySQL erőforrás-szolgáltató MySQL üzemeltetési kiszolgáló számlázható, felhasználói előfizetések hozza létre az alapértelmezett szolgáltatója előfizetésben kell létrehozni. Az erőforrás-szolgáltató kiszolgáló nem használandó felhasználói adatbázisok üzemeltetéséhez.

Az üzemeltetési kiszolgáló 5.6-os, 5.7-es és 8.0 MySQL verziók használhatók. A MySQL-RP nepodporuje caching_sha2_password hitelesítés; amely a következő kiadásban fog bővülni. MySQL 8.0 kiszolgálók mysql_native_password használatára kell konfigurálni. A MariaDB használata is támogatott.

## <a name="connect-to-a-mysql-hosting-server"></a>Csatlakozhat a MySQL-üzemeltetési kiszolgáló

Győződjön meg arról, hogy rendszergazdai jogosultságokkal rendelkező fiók hitelesítő adatait. Üzemeltetési kiszolgáló hozzáadásához kövesse az alábbi lépéseket:

1. A szolgáltatás-rendszergazdaként jelentkezzen be az Azure Stack operátori portálon
2. Válassza az **Összes szolgáltatás** elemet.
3. Alatt a **felügyeleti erőforrások** kategória kiválasztása **MySQL üzemeltető kiszolgálók** > **+ Hozzáadás**. Ekkor megnyílik a **üzemeltető MySQL-kiszolgáló hozzáadása** párbeszédpanelen, az alábbi képernyőfelvételen is látható.

   ![Üzemeltetési kiszolgáló konfigurálása](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Adja meg a MySQL-kiszolgáló példány kapcsolati adatait.

   * A **MySQL-kiszolgáló neve üzemeltető**, adja meg a teljesen minősített tartománynevét (FQDN) vagy egy érvényes IPv4-címet. Ne használja a virtuális gép rövid nevét.
   * Az alapértelmezett rendszergazdai **felhasználónév** tartozó a Bitnami MySQL lemezképeket az Azure Stack piactéren *legfelső szintű*. 
   * Ha nem ismeri a legfelső szintű **jelszó**, tekintse meg a [Bitnami dokumentáció](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials) megtudhatja, hogyan tehet szert. 
   * A MySQL alapértelmezett példány nincs megadva, ezért meg kell adnia a **mérete az üzemeltető kiszolgáló GB-ban**. Adja meg, hogy az adatbázis-kiszolgáló kapacitása megközelíti a méretét.
   * Tartsa meg az alapértelmezett beállítás a **előfizetés**.
   * A **erőforráscsoport**, hozzon létre egy új vagy egy meglévő csoportot.

   > [!NOTE]
   > A MySQL-példányt a bérlői és az Azure Resource Manager-rendszergazda is hozzáférhet, ha az erőforrás-szolgáltató ellenőrzése alá helyezheti azt. De a MySQL-példányt **kell** kizárólag az erőforrás-szolgáltató kiosztását.

5. Válassza ki **termékváltozatok** megnyitásához a **Termékváltozat létrehozása** párbeszédpanel.

   ![Hozzon létre egy MySQL-Termékváltozat](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   A Termékváltozat **neve** Termékváltozat tulajdonságainak tükröznie kell, így a felhasználók adatbázisaikat telepítheti a megfelelő Termékváltozatot.

6. Válassza ki **OK** a Termékváltozat létrehozása.
   > [!NOTE]
   > SKU-k órát is igénybe vehet egy lesznek láthatók a portálon. Egy adatbázis nem hozható létre, amíg a Termékváltozat nem telepítésének és futásának.

7. A **üzemeltető MySQL-kiszolgáló hozzáadása**válassza **létrehozás**.

Kiszolgálók hozzáadása, hozzárendelheti azokat egy új vagy meglévő Termékváltozat szolgáltatásajánlatok megkülönböztetéséhez. Például rendelkezhet egy MySQL-vállalati példányt, amely nagyobb mértékű adatbázis és az automatikus biztonsági mentést biztosít. A szervezet különböző részlegeinek a nagy teljesítményű kiszolgáló tartható fenn.

## <a name="security-considerations-for-mysql"></a>MySQL-hez készült biztonsági szempontok

A következő információkat az RP és a MySQL-kiszolgálót üzemeltető vonatkozik:

* Győződjön meg arról, hogy minden üzemeltetési kiszolgáló kommunikációhoz a TLS 1.2 használatával vannak konfigurálva. Lásd: [konfigurálása a MySQL használatára a titkosított kapcsolatokat](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* Szoftverbiztonsági [transzparens adattitkosítás](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html).
* A MySQL-RP nem támogatja a caching_sha2_password hitelesítést.

## <a name="increase-backend-database-capacity"></a>Növeli a háttér adatbázis-kapacitást

Az Azure Stack portálon további MySQL-kiszolgálók üzembe helyezésével növelheti a háttérbeli adatbázis-kapacitást. Ezek a kiszolgálók hozzáadása egy új vagy meglévő Termékváltozat. Ha egy kiszolgálót ad hozzá egy meglévő Termékváltozat, ügyeljen arra, hogy a kiszolgáló jellemzőkkel ugyanazok, mint a többi kiszolgáló, a termékváltozat.

## <a name="sku-notes"></a>Termékváltozat-megjegyzések
Használja a Termékváltozat a kiszolgálók, a termékváltozat, például a kapacitás és teljesítmény funkcióit írja le. A név, amely a felhasználókat a megfelelő termékváltozatra adatbázisaikat üzembe ábrázolt funkcionál. Használhatja például a Termékváltozat neve megkülönböztetéséhez szolgáltatásajánlatok szerint a következő jellemzőkkel:
  
* nagy kapacitású
* nagy teljesítményű
* magas rendelkezésre állás

Ajánlott eljárásként a üzemeltetési kiszolgáló, a Termékváltozat az azonos erőforrás és teljesítménybeli jellemzőit kell rendelkeznie.

SKU-k az adott felhasználók vagy csoportok nem lehet hozzárendelni.

A Termékváltozat szerkesztéséhez lépjen a **minden szolgáltatás** > **MySQL Adapter** > **termékváltozatok**. Válassza ki a Termékváltozat módosítása, végezze el a szükséges módosításokat, majd kattintson a **mentése** módosítások mentéséhez. 

A Termékváltozat, amely már nem szükséges törléséhez lépjen a **minden szolgáltatás** > **MySQL Adapter** > **termékváltozatok**. Kattintson a jobb gombbal a Termékváltozat nevét, és válassza ki **törlése** törli-e.

> [!IMPORTANT]
> Elérhető lesz a felhasználói portál az új termékváltozatokra egy órát is igénybe vehet.

## <a name="make-mysql-database-servers-available-to-your-users"></a>MySQL-adatbázis-kiszolgálók elérhetővé tétele a felhasználók számára

Hozzon létre a csomagok és ajánlatok típusa a MySQL-adatbázis-kiszolgálók elérhetővé tétele a felhasználók számára. Vegye fel a Microsoft.MySqlAdapter a terv és a egy új kvóta létrehozása. MySQL nem engedélyezi az adatbázisok méretét korlátozó.

> [!IMPORTANT]
> Új kvóták csak akkor érhető el, a felhasználói portálon, vagy előtt módosított kvóták érvényesítése akár két órát is igénybe vehet.

## <a name="next-steps"></a>További lépések

[MySQL-adatbázis létrehozása](azure-stack-mysql-resource-provider-databases.md)

<!-- Update_Description: wording update -->