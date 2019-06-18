---
title: Az Azure Stackben magas rendelkezésre állású SQL-adatbázisok biztosítása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy SQL Server erőforrás-szolgáltató gazdaszámítógép és a magas rendelkezésre állású SQL AlwaysOn adatbázisok az Azure Stack használatával.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: justinha
ms.reviewer: quying
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: c78a1e1be525d67576665e7e8099489ae4eb05d8
ms.sourcegitcommit: 593d40bccf1b2957a763017a8a2d7043f8d8315c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152458"
---
# <a name="tutorial-offer-highly-available-sql-databases"></a>Oktatóanyag: Magas rendelkezésre állású SQL-adatbázisok biztosítása

Kezelőként Azure Stack konfigurálhatja úgy a kiszolgáló virtuális gépeket, SQL Server-adatbázisok üzemeltetéséhez. Miután egy SQL üzemeltetési kiszolgálója sikeresen létrehozott, és kezeli az Azure Stack, felhasználók, akik SQL szolgáltatások feliratkozott könnyedén hozhat létre SQL Database-adatbázisok.

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Azure Stack gyorsindítási sablon használatával egy [SQL Server AlwaysOn rendelkezésre állási csoport](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), adja hozzá az Azure Stack SQL üzemeltető kiszolgálót, és ezután hozzon létre egy magas rendelkezésre állású SQL-adatbázis.

Amiről tanulni fog:

> [!div class="checklist"]
> * Egy SQL Server AlwaysOn rendelkezésre állási csoport létrehozása sablonból
> * Az Azure Stack SQL üzemeltető kiszolgáló létrehozása
> * Magas rendelkezésre állású SQL-adatbázis létrehozása

Ebben az oktatóanyagban egy két virtuális gép az SQL Server AlwaysOn rendelkezésre állási csoport jön létre, és konfigurálva a rendelkezésre álló Azure Stack piactéren elemek használatával. 

Ebben az oktatóanyagban a lépések előtt ellenőrizze, hogy a [erőforrás-szolgáltató SQL Server](azure-stack-sql-resource-provider-deploy.md) sikeresen telepítve lett, és az Azure Stack piactéren elérhető következő elemeket:

> [!IMPORTANT]
> Az alábbi szükségesek az Azure Stack gyorsindítási sablon használható.

- [A Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/MicrosoftWindowsServer.WindowsServer) Piactéri lemezképhez.
- SQL Server 2016 SP1 vagy SP2 (Enterprise, Standard vagy fejlesztői) a Windows Server 2016 server-lemezképet. Ebben az oktatóanyagban a [SQL Server 2016 SP2 Enterprise Windows Server 2016 rendszeren](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) Piactéri lemezképhez.
- [Az SQL Server IaaS-bővítményt](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) 1.2.30 verzió vagy újabb verziója. Az SQL IaaS-bővítményt, amelyek szükségesek az összes Windows-verzió esetén a Marketplace-en az SQL Server elemeket szükséges összetevők telepítése. Lehetővé teszi az SQL-specifikus beállításokat kell konfigurálni az SQL virtuális gépek. Ha a bővítmény nincs telepítve a helyi piactéren, az SQL üzembe helyezés sikertelen lesz.
- [A Windows egyéni szkriptek futtatására szolgáló bővítmény](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) 1.9.1 verzió vagy újabb verziója. Egyéni szkriptek futtatására szolgáló bővítmény olyan eszköz, amely automatikusan elindítja az üzembe helyezés utáni virtuális gépek testreszabási feladatainak használható.
- [PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) 2.76.0.0 verzió vagy újabb verziója. DSC egy felügyeleti platform a Windows PowerShell parancsmag, amely lehetővé teszi, hogy üzembe helyezése és konfigurációs adatok szoftveres szolgáltatások felügyelete és kezelése a környezetben, ahol ezek a szolgáltatások futnak.

Az Azure Stack piactéren elemek hozzáadásával kapcsolatos további tudnivalókért tekintse meg a [Azure Stack piactéren – áttekintés](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Egy SQL Server AlwaysOn rendelkezésre állási csoport létrehozása
Ez a szakasz a lépéseket követve üzembe helyezése az SQL Server AlwaysOn rendelkezésre állási csoport használatával a [sql-2016-alwayson Azure Stack-gyorssablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Ez a sablon üzembe helyez egy Always On rendelkezésre állási csoport két SQL Server Enterprise, Standard vagy fejlesztői példányból. A következő erőforrásokat hozza létre:

- A hálózati biztonsági csoport
- Virtuális hálózat
- Négy storage-fiókok (az Active Directory (AD) egy, az egyik az SQL, egyet a tanúsító fájlmegosztás és a Virtuálisgép-diagnosztika)
- Négy nyilvános IP-címek (egy Active Directory, az egyes SQL virtuális gép, és a egy SQL AlwaysOn-figyelő kötött nyilvános load Balancer két)
- Egy külső terheléselosztó az SQL virtuális gépek nyilvános IP-címmel van kötve az SQL AlwaysOn-figyelő
- Konfigurálva, a tartományvezérlő egy új erdő egyetlen tartományt egy VM (Windows Server 2016)
- Két virtuális gép (Windows Server 2016) konfigurált SQL Server 2016 SP1 vagy SP2 Enterprise, Standard vagy a Developer Edition és a fürtözött. Marketplace-rendszerképek kell lenniük.
- Egy virtuális gép (Windows Server 2016) konfigurálva, a fürt tanúsító fájlmegosztás
- Rendelkezésre állási csoporthoz, amely tartalmazza az SQL és a fájl tanúsító fájlmegosztás virtuális gépek  

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Válassza ki **\+** **erőforrás létrehozása** > **egyéni**, majd **sablonalapú telepítés**.

   ![Egyéni sablon telepítése](media/azure-stack-tutorial-sqlrp/1.png)


3. Az a **egyéni üzembe helyezés** panelen válassza ki **szerkesztési sablon** > **gyorsindítási sablon** , majd a legördülő listából válassza ki a rendelkezésre álló egyéni sablonok, Válassza ki a **sql-2016-alwayson** sablont, kattintson a **OK**, majd **mentése**.

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Válassza ki a gyorsindítási sablon")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. Az a **egyéni üzembe helyezés** panelen válassza ki **paraméterek szerkesztése** , és tekintse át az alapértelmezett értékeket. Szükség esetén adja meg az összes kötelező paraméter információkat, majd kattintson az értékek módosítása **OK**.<br><br> Minimum:

    - Adja meg túl összetett a ADMINPASSWORD SQLSERVERSERVICEACCOUNTPASSWORD és SQLAUTHPASSWORD paraméterek.
    - Adja meg a DNS-utótag névkeresési csupa kisbetűvel, a DNS-UTÓTAGJA paraméter (**azurestack.external** ASDK telepítések).
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Egyéni telepítési paraméterek szerkesztése")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. Az a **egyéni üzembe helyezés** panelen válassza ki az előfizetést használhatja, és hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot, a egyéni központi telepítés.<br><br> Ezután válassza ki az erőforráscsoport helye (**helyi** ASDK telepítések) majd **létrehozás**. Az egyéni üzembehelyezési beállítások lesznek érvényesítve, és a központi telepítés elindítása.

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Egyéni központi telepítés létrehozása")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. A felügyeleti portálon, válassza ki a **erőforráscsoportok** pedig az erőforráscsoport nevét, az egyéni üzembe helyezés (**erőforráscsoport** ebben a példában). Győződjön meg arról, központi telepítések sikeresen befejeződött az üzembe helyezés állapotának megtekintéséhez.<br><br>Ezután tekintse át az erőforrás-csoport elemeket, és válassza ki a **SQLPIPsql\<erőforráscsoport-név\>**  nyilvános IP-cím elemet. Jegyezze fel a nyilvános IP-cím és a load balancer nyilvános IP-cím teljes Tartománynevét. Szüksége lesz a rendszergazdájuknak ezt az Azure Stack-operátorokról, kihasználva az SQL AlwaysOn rendelkezésre állási csoport SQL Állomáskiszolgálót is létrehozhatnak.

   > [!NOTE]
   > A sablon üzembe helyezéséhez több óráig tart.

   ![Egyéni üzembe helyezés kész](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Automatikus összehangolása engedélyezése
Miután sikeresen üzembe helyezve és konfigurálva az SQL AlwaysON rendelkezésre állási csoport a sablont, engedélyeznie kell a [automatikus összehangolása](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) a rendelkezésre állási csoportot az SQL Server mindegyik példányán. 

Rendelkezésre állási csoport automatikus összehangolása hoz létre, amikor az SQL Server hozza létre automatikusan a másodlagos replikák minden adatbázis a csoport más manuális beavatkozás nélkül az AlwaysOn adatbázisok magas rendelkezésre állás biztosításához szükséges.

A SQL-parancsok használatához konfigurálása az AlwaysOn rendelkezésre állási csoport automatikus összehangolása. Cserélje le \<InstanceName\> az elsődleges példány SQL Server nevét és a < availability_group_name > szükség szerint az AlwaysOn rendelkezésre állási csoport nevét. 

Az elsődleges SQL-példányon:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Elsődleges SQL-példány parancsfájl](./media/azure-stack-tutorial-sqlrp/sql1.png)

A másodlagos SQL-példányok:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Másodlagos SQL-példány parancsfájl](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Tartalmazott adatbázis-hitelesítés konfigurálása
Mielőtt hozzáadná egy tartalmazott adatbázis egy rendelkezésre állási csoport, győződjön meg arról, hogy a tartalmazott adatbázis hitelesítése kiszolgálói beállítás értéke 1 a minden a rendelkezésre állási csoport egy rendelkezésre állási másodpéldányt futtató kiszolgálópéldány. További információkért lásd: [tartalmazott adatbázis hitelesítése](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Ezeket a parancsokat használja a tartalmazott adatbázis hitelesítési kiszolgáló lehetőséget minden egyes SQL Server-példány beállítása a rendelkezésre állási csoportban:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Beállítása a tartalmazott adatbázis hitelesítése](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Az Azure Stack SQL üzemeltető kiszolgáló létrehozása
Miután létrehozta az SQL Server AlwayOn rendelkezésre állási csoport, és megfelelően konfigurálva, az Azure Stack-operátorokról Azure Stack SQL üzemeltető kiszolgálót a további kapacitás adatbázisok létrehozására a felhasználók számára is elérhetővé kell létrehoznia. 

Ügyeljen arra, hogy a nyilvános IP-cím vagy teljes Tartománynevet az SQL terheléselosztó a nyilvános IP-cím az SQL AlwaysOn rendelkezésre állási csoport erőforráscsoport létrehozásának korábban rögzített (**SQLPIPsql\<erőforráscsoport-név\>** ). Ezenkívül akkor ismernie kell az SQL Server az AlwaysOn rendelkezésre állási csoportban található SQL Server-példányok eléréséhez használt hitelesítő adatok.

> [!NOTE]
> Ebben a lépésben egy Azure Stack operátorait szerint kell futtatni az Azure Stack felügyeleti portálról.

Az SQL AlwaysOn rendelkezésre állási csoport load balancer figyelő nyilvános IP-cím és az SQL hitelesítési bejelentkezési adatait, és az Azure Stack – operátor mostantól [hozzon létre egy SQL üzemeltető kiszolgálót az SQL AlwaysOn rendelkezésre állási csoport használata](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Emellett győződjön meg arról, hogy már létrehozott terveket, és felajánlja, hogy az SQL AlwaysOn adatbázis létrehozása a felhasználók számára elérhetővé tenni. Az operátor hozzá kell adnia a **Microsoft.SqlAdapter** díjcsomagra szolgáltatást, és hozzon létre egy új kvóta kifejezetten a magas rendelkezésre állású adatbázisok. Csomag létrehozásával kapcsolatos további információkért lásd: [csomag, ajánlat, kvóta és előfizetés áttekintése](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> A **Microsoft.SqlAdapter** szolgáltatás nem lesz elérhető, amíg csomagok hozzáadása a [SQL Server erőforrás-szolgáltató van telepítve](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Magas rendelkezésre állású SQL-adatbázis létrehozása
Után az SQL AlwaysOn rendelkezésre állási csoport létrehozása, konfigurálásához és egy Azure Stack az SQL futtató kiszolgálóval hozzáadta az Azure Stack-operátorokról egy bérlő felhasználót egy előfizetést, beleértve az SQL Server adatbázis-képességeket a hozhat létre SQL Database-adatbázisok támogatása A jelen szakaszban ismertetett lépéseket követve AlwaysOn funkcióját. 

> [!NOTE]
> Futtassa ezeket a lépéseket az Azure Stack felhasználói portálról bérlői felhasználói biztosít az SQL Server képességet (Microsoft.SQLAdapter szolgáltatás) előfizetéssel.

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Válassza ki **\+** **erőforrás létrehozása** > **adatok \+ tárolási**, majd **SQL Database**.<br><br>Adja meg a szükséges adatbázis-tulajdonság információkat, beleértve a neve, rendezés, maximális méretét, és az előfizetés, erőforráscsoport és hely, a központi telepítéshez használni. 

   ![SQL-adatbázis létrehozása](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Válassza ki **Termékváltozat** és válassza ki a megfelelő SQL üzemeltető kiszolgáló Termékváltozat használatára. Ebben a példában az Azure Stack-operátorokról hozott létre a **vállalati – magas rendelkezésre ÁLLÁS** Termékváltozat az SQL AlwaysOn rendelkezésre állási csoportokra vonatkozó magas rendelkezésre állás megvalósításához.

   ![SKU kiválasztása](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Válassza ki **bejelentkezési** > **hozzon létre egy új bejelentkezést** , és adja meg az SQL hitelesítés hitelesítő adatokat, amelyek az új adatbázisnak. Ha befejezte, kattintson a **OK** , majd **létrehozás** az adatbázis üzembe helyezés megkezdéséhez.

   ![Bejelentkezés létrehozása](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Ha az SQL-adatbázis üzembe helyezése sikeresen befejeződött, tekintse át az adatbázis tulajdonságainak felderítése a kapcsolati karakterláncot az új magas rendelkezésre állású adatbázishoz való kapcsolódáskor használandó. 

   ![Kapcsolati karakterlánc megtekintése](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Egy SQL Server AlwaysOn rendelkezésre állási csoport létrehozása sablonból
> * Az Azure Stack SQL üzemeltető kiszolgáló létrehozása
> * Magas rendelkezésre állású SQL-adatbázis létrehozása

A következő oktatóanyaggal, amelyben tudatjuk a felhasználókkal hogyan:
> [!div class="nextstepaction"]
> [Magas rendelkezésre állású MySQL-adatbázisok létrehozása](azure-stack-tutorial-mysql.md)