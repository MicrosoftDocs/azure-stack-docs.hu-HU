---
title: Az SQL futtató kiszolgálókat az Azure Stackben |} A Microsoft Docs
description: Hogyan adja hozzá az SQL Server-példányok üzembe helyezés az SQL-Adapter erőforrás-szolgáltatón keresztül.
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
ms.date: 03/26/2019
ms.author: mabrigg
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: a56e1e8c3c5109c638432652adf9260f59f6a467
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618449"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Az erőforrás-szolgáltató SQL üzemeltetési kiszolgáló hozzáadása

Létrehozhat egy virtuális gépen (VM) kiszolgálókat üzemeltető SQL Server-adatbázis [Azure Stack](azure-stack-overview.md), vagy egy virtuális Gépen az Azure Stack környezettel, amennyiben az erőforrás-szolgáltató SQL csatlakozhat a példány kívül.

> [!NOTE]
> Az erőforrás-szolgáltató SQL SQL üzemeltetési kiszolgáló számlázható, felhasználói előfizetés hozza létre az alapértelmezett szolgáltatója előfizetésben kell létrehozni. Az erőforrás-szolgáltató kiszolgáló nem használandó felhasználói adatbázisok üzemeltetéséhez.

## <a name="overview"></a>Áttekintés

Mielőtt hozzáadja egy SQL server szoftvert futtatja, tekintse át a következő kötelező és az általános.

### <a name="mandatory-requirements"></a>Kötelező követelmények

* Az SQL Server-példány az SQL-hitelesítés engedélyezéséhez. Az SQL-erőforrás-szolgáltató virtuális gép nem csatlakozik tartományhoz, mert azt csak csatlakozhat egy SQL-hitelesítéssel üzemeltető kiszolgálót.
* Konfigurálja az IP-címek az SQL Server-példányok megegyeznek a nyilvános Azure Stackkel való telepítésekor. Az erőforrás-szolgáltató, és a felhasználók számára, például a Web Apps, felhasználói hálózati kommunikációra, így nem szükséges a kapcsolat SQL-példányhoz a hálózaton.

### <a name="general-requirements"></a>Általános követelmények

* Használja az SQL-példány dedikált a erőforrás-szolgáltató és a felhasználó munkaterhelések által. Bármely más felhasználói által használt SQL-példány nem használhat. Ez a korlátozás is vonatkozik, az App Servicesbe való.
* A megfelelő jogosultsággal rendelkező fiók konfigurálása az erőforrás-szolgáltató (lásd alább).
* Ön felelős az SQL Server-példányok és gazdagépeik kezelése.  Például az erőforrás-szolgáltató nem alkalmazza a frissítéseket, kezelni a biztonsági mentések vagy kezelni hitelesítőadat-elforgatás.

### <a name="sql-server-virtual-machine-images"></a>Az SQL Server virtuálisgép-lemezképek

Az SQL IaaS virtuálisgép-lemezképet a Marketplace-en felügyeleti szolgáltatáson keresztül érhetők el. Ezek a lemezképek ugyanazok, mint az SQL virtuális gépek az Azure-ban elérhető.

Győződjön meg arról, hogy mindig a legújabb verzióját töltse le a **SQL IaaS-bővítményt** Piactéri elem használatával SQL virtuális gép üzembe helyezése előtt. Az IaaS-bővítményt és a megfelelő portál fejlesztések például az automatikus javítás további alkalmazásszolgáltatások biztosítása érdekében, és készítsen biztonsági másolatot. Ez a bővítmény kapcsolatos további információkért lásd: [Azure virtuális gépeken az SQL Server Agent bővítmény a felügyeleti feladatok automatizálása](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> Az SQL IaaS-bővítményt van _szükséges_ a piactéren; Windows-rendszerképeket az összes SQL a virtuális gép meghiúsul, ha nem tölti le a bővítmény telepítése. Linux-alapú SQL VM-lemezképek nem használható.

SQL virtuális gépek, beleértve a sablonok üzembe helyezésének egyéb lehetőség van a [Azure Stack gyorsindítási galéria](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Bármely üzemeltetési kiszolgáló telepíthető a több csomópontos Azure Stack felhasználói előfizetés, és nem az alapértelmezett szolgáltatója előfizetést kell létrehozni. Azok a felhasználói portál vagy egy megfelelő bejelentkezési egy PowerShell-munkamenetet kell létrehoznia. Minden üzemeltetési kiszolgáló számlázható virtuális gépek és a megfelelő SQL-licenccel kell rendelkeznie. A szolgáltatás-rendszergazda _is_ lehet, hogy az előfizetés tulajdonosa.

### <a name="required-privileges"></a>Szükséges jogosultságok

Egy SQL SysAdmin (rendszergazda), mint az alacsonyabb szintű jogosultságokkal rendelkező rendszergazda felhasználó is létrehozhat. A felhasználó csak engedélyre van szüksége a következő műveletek:

* Adatbázis: Hozzon létre, Alter, amelyben a Containment (a Always On csak), dobja el, a biztonsági mentés
* Rendelkezésre állási csoporthoz: Az ALTER, csatlakozzon, adatbázis hozzáadása/eltávolítása
* Bejelentkezés: Hozzon létre, válassza ki, Alter, dobja el, visszavonása
* SELECT műveletek: \[fő\].\[ sys\].\[ availability_group_listeners\] (AlwaysOn), a sys.availability_replicas (AlwaysOn), a sys.databases, \[fő\].\[ sys\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[fő\].\[ sys\].\[ availability_groups\] (AlwaysOn), sys.master_files

### <a name="additional-security-information"></a>További biztonsági információkat

Az alábbi adatokat a további biztonsági útmutatást nyújt:

* Az összes Azure Stack-tároló a BitLocker titkosítja, így tetszőleges SQL-példányra, az Azure Stacken titkosított blobot a tárhelyet fogja használni.
* Az erőforrás-szolgáltató az SQL teljes mértékben támogatja a TLS 1.2. Győződjön meg arról, hogy bármely SQL Server, az SQL-RP-mel felügyelt konfigurálva van a TLS 1.2 _csak_ és a függő Entitás, amely alapértelmezés szerint. A TLS 1.2-es, az SQL Server támogatása az összes támogatott verzióit lásd: [a TLS 1.2 támogatása a Microsoft SQL Server](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* Használja az SQL Server Configuration Manager beállítása a **ForceEncryption** mindig lehetőséget annak biztosítása érdekében az SQL server felé minden kommunikáció titkosított. Lásd: [, konfigurálja a kiszolgálót, hogy a titkosított kapcsolatokat](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#ConfigureServerConnections).
* Győződjön meg róla, minden ügyfélalkalmazás is titkosított kapcsolaton keresztül kommunikál.
* Az SQL Server-példányok által használt tanúsítványok megbízhatósági kapcsolata konfigurálva van az RP-ből.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Adja meg a kapacitás csatlakozik egy különálló SQL server szoftvert futtatja

Használhatja az önálló (nem – magas rendelkezésre ÁLLÁS) bármely SQL Server 2014 vagy SQL Server 2016-os kiadása SQL-kiszolgálók. Győződjön meg arról, hogy egy rendszergazdai jogosultságokkal rendelkező fiók hitelesítő adatait.

Adjon hozzá egy önálló üzemeltető kiszolgálót, amely már be van állítva, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Stack operátori portál szolgáltatás-rendszergazdaként.

2. Válassza ki **minden szolgáltatás** &gt; **felügyeleti erőforrások** &gt; **SQL üzemeltető kiszolgálók**.

   ![SQL Hosting Servers](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   A **SQL üzemeltető kiszolgálók**, az erőforrás-szolgáltató SQL csatlakozhat az erőforrás-szolgáltató háttérrendszer erre a célra az SQL Server-példányok.

   ![SQL-csatoló irányítópult](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. Kattintson a **Hozzáadás** adja meg a kapcsolat részletek az SQL Server-példány az a **üzemeltető SQL-kiszolgáló hozzáadása** panelen.

   ![Add a SQL Hosting Server](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Szükség esetén adja meg a példány nevét, és adjon meg egy portszámot, ha a példány nincs hozzárendelve az alapértelmezett 1433-as portot.

   > [!NOTE]
   > Mindaddig, amíg az SQL-példány sorrendvezérlése előtt a felhasználó és az Azure Resource Manager rendszergazda, akkor az erőforrás-szolgáltató ellenőrzése alatt helyezhető. Az SQL-példány __kell__ kizárólag az erőforrás-szolgáltató kiosztását.

4. Kiszolgálók hozzáadása, hozzárendelheti őket egy meglévő Termékváltozat, vagy hozzon létre egy új Termékváltozatban. A **üzemeltető kiszolgáló hozzáadása a**válassza **termékváltozatok**.

   * Egy meglévő Termékváltozat használatához válassza ki a Termékváltozat érhető el, majd **létrehozás**.
   * A Termékváltozat létrehozásához válassza **+ létrehozni az új Termékváltozatot**. A **Termékváltozat létrehozása**, és adja meg a szükséges információkat, majd válassza ki **OK**.

     ![A Termékváltozat létrehozása](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Magas rendelkezésre állás SQL Always On rendelkezésre állási csoportok használatával

SQL Always On példányok konfigurálásához további lépéseket igényel, és megköveteli a három virtuális gép (vagy fizikai gépek.) Ez a cikk azt feltételezi, hogy már rendelkezik egy Always On rendelkezésre állási csoportjainak alapos ismerete. További információkért tekintse át a következő cikkeket:

* [SQL Server Always On rendelkezésre állási csoportok az Azure-beli virtuális gépek bemutatása](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On rendelkezésre állási csoportok (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Erőforrás-szolgáltató SQL adapter _csak_ támogatja az SQL 2016 SP1 Enterprise, vagy később-példány AlwaysOn rendelkezésre állási csoportok számára. Az adapter konfigurációs van szükség, például az automatikus összehangolása új SQL-szolgáltatások.

### <a name="automatic-seeding"></a>Automatikus összehangolása

Engedélyeznie kell a [automatikus összehangolása](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) az egyes rendelkezésre állási csoport, az SQL Server mindegyik példányán.

Ahhoz, hogy minden példány automatikus összehangolása, szerkesztése, és futtassa a következő SQL-parancsot minden egyes másodlagos példány esetében az elsődleges replikán:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

A rendelkezésre állási csoport szögletes zárójelek közé kell tenni.

A másodlagos csomópontokra, futtassa a következő SQL-parancsot:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Tartalmazott adatbázis-hitelesítés konfigurálása

Mielőtt hozzáadná egy tartalmazott adatbázis egy rendelkezésre állási csoport, győződjön meg arról, hogy a tartalmazott adatbázis hitelesítése kiszolgálói beállítás értéke 1 a minden a rendelkezésre állási csoport egy rendelkezésre állási másodpéldányt futtató kiszolgálópéldány. További információkért lásd: [tartalmazott adatbázis hitelesítése kiszolgálói konfigurációs beállítás](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Ezeket a parancsokat használja a tartalmazott adatbázis hitelesítési kiszolgáló beállítást, minden példány esetében:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>To add SQL Always On hosting servers

1. A szolgáltatás-rendszergazdaként jelentkezzen be az Azure Stack felügyeleti portálján

2. Válassza ki **Tallózás** &gt; **felügyeleti erőforrások** &gt; **kiszolgálókat üzemeltető SQL** &gt; **+ Hozzáadás**.

   A **SQL üzemeltető kiszolgálók**, az SQL Server erőforrás-szolgáltató csatlakozhat tényleges példányait, amelyeket az SQL Server, az erőforrás-szolgáltató háttérrendszer szolgálhat.

3. Adja meg az SQL Server-példány kapcsolati adatait tartalmazó képernyő. Ellenőrizze, hogy az mindig figyelő (és az opcionális port számát és a példány neve) FQDN címére használt. Adja meg az adatokat, a konfigurált rendszergazdai jogosultságokkal rendelkező fiók.

4. Az Always On rendelkezésre állási csoport jelölőnégyzetet az SQL Always On rendelkezésre állási csoport példányok támogatásának engedélyezése.

   ![A mindig engedélyezése](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Az SQL Always On példány hozzá egy Termékváltozatot.

   > [!IMPORTANT]
   > Az Always On instances ugyanazon Termékváltozat nem használhatók vegyesen önálló kiszolgálón. Kísérlet történt vegyesen típusok hozzáadása az első üzemeltetési kiszolgáló eredmények hiba után.

## <a name="sku-notes"></a>Termékváltozat-megjegyzések
Használja a Termékváltozat a kiszolgálók, a termékváltozat, például a kapacitás és teljesítmény funkcióit írja le. A név, amely a felhasználókat a megfelelő termékváltozatra adatbázisaikat üzembe ábrázolt funkcionál. Használhatja például a Termékváltozat neve megkülönböztetéséhez szolgáltatásajánlatok szerint a következő jellemzőkkel:
  
* nagy kapacitású
* nagy teljesítményű
* magas rendelkezésre állás

Ajánlott eljárásként a üzemeltetési kiszolgáló, a Termékváltozat az azonos erőforrás és teljesítménybeli jellemzőit kell rendelkeznie.

SKU-k az adott felhasználók vagy csoportok nem lehet hozzárendelni.

SKU-k órát is igénybe vehet egy lesznek láthatók a portálon. Felhasználók nem hozható létre adatbázis, amíg a Termékváltozat létrehozása befejeződött.

A Termékváltozat szerkesztéséhez lépjen a **minden szolgáltatás** > **SQL Adapter** > **termékváltozatok**. Válassza ki a Termékváltozat módosítása, végezze el a szükséges módosításokat, majd kattintson a **mentése** módosítások mentéséhez. 

A Termékváltozat, amely már nem szükséges törléséhez lépjen a **minden szolgáltatás** > **SQL Adapter** > **termékváltozatok**. Kattintson a jobb gombbal a Termékváltozat nevét, és válassza ki **törlése** törli-e.

> [!IMPORTANT]
> Elérhető lesz a felhasználói portál az új termékváltozatokra egy órát is igénybe vehet.

## <a name="make-sql-databases-available-to-users"></a>SQL-adatbázisok elérhetővé tétele a felhasználók számára

Hozzon létre a csomagok és ajánlatok az SQL Database-adatbázisok elérhetővé tétele a felhasználók számára. Adja hozzá a **Microsoft.SqlAdapter** a terv szolgáltatást, és a egy új kvóta létrehozása.

> [!IMPORTANT]
> Új kvóták csak akkor érhető el, a felhasználói portálon, vagy előtt módosított kvóták érvényesítése akár két órát is igénybe vehet.

## <a name="next-steps"></a>További lépések

[Adatbázisok hozzáadása](azure-stack-sql-resource-provider-databases.md)
