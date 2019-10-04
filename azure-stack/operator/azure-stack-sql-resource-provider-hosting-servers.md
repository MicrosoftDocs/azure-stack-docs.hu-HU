---
title: SQL üzemeltetési kiszolgálók a Azure Stackon | Microsoft Docs
description: SQL-példányok hozzáadása az SQL-adapter erőforrás-szolgáltatóján keresztüli üzembe helyezéshez.
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
ms.openlocfilehash: 24271bf3f4155433980972df19e541dbb77fa908
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909471"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Üzemeltetési kiszolgálók hozzáadása az SQL erőforrás-szolgáltatóhoz

Létrehozhat SQL Server adatbázis-üzemeltetési kiszolgálókat [Azure stack](azure-stack-overview.md)vagy a Azure stack-környezeten kívüli virtuális gépen, feltéve, hogy az SQL erőforrás-szolgáltató csatlakozni tud a példányhoz.

> [!NOTE]
> Az SQL-erőforrás-szolgáltatót az alapértelmezett szolgáltatói előfizetésben kell létrehozni, míg az SQL üzemeltetési kiszolgálókat számlázható, felhasználói előfizetésben kell létrehozni. Az erőforrás-szolgáltatói kiszolgálót nem szabad használni a felhasználói adatbázisok üzemeltetéséhez.

## <a name="overview"></a>Áttekintés

SQL üzemeltetési kiszolgáló hozzáadása előtt tekintse át a következő kötelező és általános követelményeket.

### <a name="mandatory-requirements"></a>Kötelező követelmények

* Engedélyezze az SQL-hitelesítést a SQL Server példányon. Mivel az SQL Resource Provider virtuális gép nincs tartományhoz csatlakoztatva, csak SQL-hitelesítéssel tud csatlakozni az üzemeltetési kiszolgálókhoz.
* Konfigurálja az SQL-példányok IP-címeit nyilvánosként, ha Azure Stack telepítik. Az erőforrás-szolgáltató és a felhasználók, például a Web Apps, kommunikációt végeznek a felhasználói hálózaton keresztül, így a hálózatban található SQL-példányhoz kell kapcsolódnia.

### <a name="general-requirements"></a>Általános követelmények

* Fordítsa el az SQL-példányt az erőforrás-szolgáltató és a felhasználói munkaterhelések használatára. Nem használhat olyan SQL-példányt, amelyet más fogyasztó használ. Ez a korlátozás a App Servicesra is vonatkozik.
* Konfiguráljon egy fiókot az erőforrás-szolgáltató megfelelő jogosultsági szintjeivel (lásd alább).
* Ön felelős az SQL-példányok és a gazdagépek kezeléséért.  Az erőforrás-szolgáltató például nem alkalmazza a frissítéseket, kezeli a biztonsági mentéseket, vagy kezeli a hitelesítő adatok elforgatását.

### <a name="sql-server-virtual-machine-images"></a>Virtuálisgép-rendszerképek SQL Server

Az SQL IaaS virtuálisgép-lemezképek a piactér felügyeleti funkciójával érhetők el. Ezek a lemezképek ugyanazok, mint az Azure-ban elérhető SQL virtuális gépek.

Győződjön meg arról, hogy mindig letölti az **SQL IaaS-bővítmény** legújabb verzióját, mielőtt üzembe helyezi az SQL virtuális gépet egy Marketplace-elemmel. A IaaS-bővítmény és a kapcsolódó portál továbbfejlesztése további funkciókat is biztosít, például az automatikus javítást és a biztonsági mentést. További információ erről a bővítményről: [felügyeleti feladatok automatizálása az Azure Virtual Machines a SQL Server Agent bővítménnyel](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> Az SQL IaaS bővítmény _szükséges_ a piactéren található összes SQL-lemezképhez. Ha nem letöltötte a bővítményt, a virtuális gép üzembe helyezése sikertelen lesz. A Linux-alapú SQL virtuálisgép-rendszerképekkel nem használható.

Az SQL-alapú virtuális gépek üzembe helyezéséhez más lehetőségek is rendelkezésre állnak, beleértve a [Azure stack Gyorsindítás galériájában](https://github.com/Azure/AzureStack-QuickStart-Templates)található sablonokat is.

> [!NOTE]
> A több csomópontos Azure Stack telepített üzemeltetési kiszolgálókat felhasználói előfizetésből kell létrehozni, nem az alapértelmezett szolgáltatói előfizetéshez. A felhasználókat a felhasználói portálról vagy egy megfelelő bejelentkezési azonosítóval rendelkező PowerShell-munkamenetből kell létrehozni. Minden üzemeltetési kiszolgáló számlázható virtuális gépek, és megfelelő SQL-licenccel kell rendelkeznie. A szolgáltatás rendszergazdája az előfizetés tulajdonosa lehet.

### <a name="required-privileges"></a>Szükséges jogosultságok

Létrehozhat olyan rendszergazda felhasználót, amely alacsonyabb jogosultságokkal rendelkezik, mint az SQL sysadmin. A felhasználónak csak a következő műveletekhez szükséges engedélyekkel kell rendelkeznie:

* Adatbázis Létrehozás, módosítás, tárolás (csak always on), drop, Backup
* Rendelkezésre állási Csoport: Adatbázis módosítása, csatlakoztatása, hozzáadása/eltávolítása
* Bejelentkezési Létrehozás, kijelölés, módosítás, eldobás, visszavonás
* Válassza a műveletek: \[master @ no__t-1. \[sys @ no__t-3. \[availability_group_listeners @ no__t-5 (AlwaysOn), sys. availability_replicas (AlwaysOn), sys. Databases, \[master @ no__t-7. \[sys @ no__t-9. \]0dm_os_ sys_memory @ no__t-11, SERVERPROPERTY, 2master @ no__t-13. 4sys @ no__t-15. 6availability_groups @ no__t-17 (AlwaysOn), sys. master_files

### <a name="additional-security-information"></a>További biztonsági információk

A következő információk további biztonsági útmutatást nyújtanak:

* Az összes Azure Stack tárterület titkosítása a BitLocker használatával történik, így a Azure Stack bármely SQL-példánya titkosított BLOB-tárolót fog használni.
* Az SQL erőforrás-szolgáltató teljes mértékben támogatja a TLS 1,2-et. Győződjön meg arról, hogy az SQL RP-n keresztül felügyelt SQL Server _csak_ a TLS 1,2 használatára van konfigurálva, és az RP alapértelmezett értéke a következő lesz:. A SQL Server összes támogatott verziója támogatja a TLS 1,2-et, lásd: [tls 1,2 támogatás Microsoft SQL Serverhoz](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* A **ForceEncryption** beállítás megadásával biztosíthatja, hogy az összes SQL Server-alapú kommunikáció mindig titkosítva legyen a SQL Server konfigurációkezelő használatával. Lásd: [a kiszolgáló konfigurálása a titkosított kapcsolatok kényszerítésére](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#to-configure-the-server-to-force-encrypted-connections).
* Gondoskodjon arról, hogy minden ügyfélalkalmazás egy titkosított kapcsolaton keresztül is kommunikáljon.
* Az RP úgy van konfigurálva, hogy megbízzon a SQL Server példányok által használt tanúsítványokban.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Kapacitás biztosítása egy önálló üzemeltetési SQL Server-kiszolgálóhoz való csatlakozással

A SQL Server 2014 vagy SQL Server 2016 bármely kiadásával önálló (nem HA) SQL Servert használhat. Ellenőrizze, hogy rendelkezik-e rendszergazdai jogosultságokkal rendelkező fiók hitelesítő adataival.

Egy már beállított önálló üzemeltetési kiszolgáló hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Stack Operator portál szolgáltatás-rendszergazdaként.

2. Válassza **a minden szolgáltatás** &gt; **felügyeleti erőforrások** &gt; **SQL üzemeltetési kiszolgálók**elemet.

   ![SQL Hosting Servers](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Az **SQL üzemeltetési kiszolgálók**területen az SQL erőforrás-szolgáltatót az erőforrás-szolgáltatói háttérként szolgáló SQL Server-példányokhoz is összekapcsolhatja.

   ![SQL-adapter irányítópultja](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. Kattintson a **Hozzáadás** gombra, majd adja meg a SQL Server-példány kapcsolati adatait az **SQL üzemeltetési kiszolgáló hozzáadása** panelen.

   ![SQL-üzemeltetési kiszolgáló hozzáadása](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Megadhatja a példány nevét, és megadhatja a portszámot, ha a példány nincs az alapértelmezett 1433-as porthoz rendelve.

   > [!NOTE]
   > Ha az SQL-példányt a felhasználó és a rendszergazda Azure Resource Manager is elérheti, akkor az erőforrás-szolgáltató irányítása alá helyezhető. Az SQL- példányt kizárólag az erőforrás-szolgáltató számára kell lefoglalni.

4. A kiszolgálók hozzáadásakor hozzá kell rendelnie őket egy meglévő SKU-hoz, vagy létre kell hoznia egy új SKU-t. Az **üzemeltetési kiszolgáló hozzáadása**területen válassza az **SKU**-ket.

   * Meglévő SKU használatához válasszon egy elérhető SKU-t, majd válassza a **Létrehozás**lehetőséget.
   * SKU létrehozásához válassza az **+ új SKU létrehozása**elemet. A **SKU létrehozása**területen adja meg a szükséges információkat, majd kattintson **az OK gombra**.

     ![SKU létrehozása](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Magas rendelkezésre állás biztosítása az SQL always on rendelkezésre állási csoportok használatával

Az SQL always on-példányok konfigurálása további lépéseket igényel, és három virtuális gépet (vagy fizikai gépeket) igényel. Ez a cikk azt feltételezi, hogy már rendelkezik az Always On rendelkezésre állási csoportok alapos megismerésével. További információkért tekintse át a következő cikkeket:

* [SQL Server always on rendelkezésre állási csoportok bemutatása Azure-beli virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On rendelkezésre állási csoportok (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Az SQL-adapter erőforrás-szolgáltatója _csak_ az SQL 2016 SP1 Enterprise vagy újabb példányokat támogatja az Always On rendelkezésre állási csoportokhoz. Ehhez az adapter-konfigurációhoz új SQL-funkciók szükségesek, például automatikus előkészítés.

### <a name="automatic-seeding"></a>Automatikus előkészítés

Az egyes rendelkezésre állási csoportokon engedélyezni kell az [automatikus](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) kivetést SQL Server minden egyes példánya esetében.

Ha engedélyezni szeretné az automatikus betöltést az összes példányon, szerkessze és futtassa a következő SQL-parancsot az elsődleges replikán minden másodlagos példány esetében:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

A rendelkezésre állási csoportot szögletes zárójelek közé kell foglalni.

A másodlagos csomópontokon futtassa a következő SQL-parancsot:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Tárolt adatbázis-hitelesítés konfigurálása

Egy tárolt adatbázis rendelkezésre állási csoportba való felvétele előtt győződjön meg arról, hogy a tárolt adatbázis-hitelesítési kiszolgáló beállítás értéke 1 minden olyan kiszolgálópéldány esetében, amely rendelkezésre állási replikát futtat a rendelkezésre állási csoport számára. További információ: [tárolt adatbázis-hitelesítési kiszolgáló konfigurációs beállítása](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

A következő parancsokkal állíthatja be a tárolt adatbázis-hitelesítési kiszolgáló beállítást az egyes példányok esetében:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>SQL always on üzemeltetési kiszolgálók hozzáadása

1. Jelentkezzen be a Azure Stack felügyeleti portálra szolgáltatás-rendszergazdaként.

2. Válassza a **Tallózás** &gt; **felügyeleti erőforrások** &gt; **SQL üzemeltetési kiszolgálók** &gt; **+ Hozzáadás**lehetőséget.

   Az **SQL üzemeltetési kiszolgálók**területen összekapcsolhatja a SQL Server erőforrás-szolgáltatót az erőforrás-szolgáltató háttereként szolgáló SQL Server tényleges példányaival.

3. Töltse ki az űrlapot a SQL Server példányának kapcsolati adataival. Ügyeljen arra, hogy a mindig a figyelő teljes tartománynevét használja (és a portszámot és a példány nevét). Adja meg a rendszergazdai jogosultságokkal konfigurált fiók adatait.

4. Az Always On rendelkezésre állási csoport jelölőnégyzet bejelölésével engedélyezheti az SQL always on rendelkezésre állási csoport példányainak támogatását.

   ![Az Always On engedélyezése](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Adja hozzá az SQL always on-példányt egy SKU-hoz.

   > [!IMPORTANT]
   > Egyazon SKU-beli példányokon nem lehet különálló kiszolgálókat összekeverni. Az első üzemeltetési kiszolgáló hozzáadását követően a rendszer nem eredményez hibát.

## <a name="sku-notes"></a>SKU-megjegyzések
Használjon olyan SKU-nevet, amely leírja az SKU-ban található kiszolgálók képességeit, például a kapacitást és a teljesítményt. A név segítséget nyújt arra, hogy a felhasználók a megfelelő SKU-ban telepíthessék az adatbázisaikat. Az alábbi jellemzőkkel rendelkezhet például az SKU-nevek használatával a szolgáltatási ajánlatok megkülönböztetéséhez:
  
* nagy kapacitás
* nagy teljesítményű
* Magas rendelkezésre állás

Az ajánlott eljárás szerint az SKU-ban lévő összes üzemeltetési kiszolgálónak ugyanazzal az erőforrással és teljesítménnyel kapcsolatos tulajdonságokkal kell rendelkeznie.

A SKU-t nem lehet hozzárendelni meghatározott felhasználókhoz vagy csoportokhoz.

A SKU akár egy órát is igénybe vehet, hogy megjelenjenek a portálon. A felhasználók nem tudnak adatbázist létrehozni, amíg az SKU teljesen létre nem jön.

Az SKU szerkesztéséhez lépjen a **minden szolgáltatás** > **SQL** > -adapterek**SKU**-ra. Válassza ki a módosítandó SKU-t, végezze el a szükséges módosításokat, majd kattintson a **Mentés** gombra a módosítások mentéséhez. 

A már nem szükséges SKU törléséhez lépjen a **minden szolgáltatás** > **SQL** > -adapterek**SKU**-ra. Kattintson a jobb gombbal az SKU nevére, és válassza a **Törlés** lehetőséget a törléshez.

> [!IMPORTANT]
> Akár egy óráig is eltarthat, amíg az új SKU elérhetővé válik a felhasználói portálon.

## <a name="make-sql-databases-available-to-users"></a>SQL-adatbázisok elérhetővé tétele a felhasználók számára

Terveket és ajánlatokat hozhat létre, amelyekkel elérhetővé teheti az SQL-adatbázisokat a felhasználók számára. Adja hozzá a **Microsoft. SqlAdapter** szolgáltatást a csomaghoz, és hozzon létre egy új kvótát.

> [!IMPORTANT]
> Akár két óráig is eltarthat, amíg az új kvóták elérhetővé válnak a felhasználói portálon, vagy a módosított kvóta érvénybe léptetése előtt.

## <a name="next-steps"></a>További lépések

[Adatbázisok hozzáadása](azure-stack-sql-resource-provider-databases.md)
