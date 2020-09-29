---
title: Magasan elérhető SQL-adatbázisok létrehozása
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan hozhat létre SQL Server erőforrás-szolgáltatót futtató számítógépet és a jól elérhető SQL AlwaysOn-adatbázisokat Azure Stack hub használatával.
author: BryanLa
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2019
ms.openlocfilehash: ce3f6e0542678fe2d399e101a90a916cf412599f
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487719"
---
# <a name="create-highly-available-sql-databases-with-azure-stack-hub"></a>Magasan elérhető SQL-adatbázisok létrehozása Azure Stack hub-vel

Azure Stack hub-kezelőként beállíthatja a kiszolgálói virtuális gépeket SQL Server-adatbázisok üzemeltetéséhez. Ha az SQL-üzemeltetési kiszolgálót Azure Stack hub hozza létre és felügyeli, akkor az SQL-szolgáltatásra előfizetett felhasználók könnyedén létrehozhatnak SQL-adatbázisokat.

Ez a cikk bemutatja, hogyan használható egy Azure Stack hub gyors üzembe helyezési sablon egy [SQL Server AlAlwaysOnon rendelkezésre állási csoport](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)létrehozásához, Azure stack hub SQL üzemeltetési kiszolgálóként való hozzáadásához, majd egy magasan elérhető SQL-adatbázis létrehozásához.

Ismertetett témák:

> [!div class="checklist"]
> * Hozzon létre egy SQL Server alAlwaysOnon rendelkezésre állási csoportot egy sablonból.
> * Hozzon létre egy Azure Stack hub SQL üzemeltetési kiszolgálót.
> * Hozzon létre egy magasan elérhető SQL-adatbázist.

A rendszer két virtuálisgép-SQL Server alAlwaysOnon rendelkezésre állási csoportot hoz létre és konfigurál az elérhető Azure Stack Marketplace-elemek használatával.

A Kezdés előtt győződjön meg arról, hogy a [SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider-deploy.md) sikeresen telepítve van, és a következő elemek érhetők el Azure stack piactéren:

> [!IMPORTANT]
> Az Azure Stack hub gyors üzembe helyezési sablonjának használatához az alábbi lépések szükségesek.

- Windows Server 2016 Datacenter.
- SQL Server 2016 SP1 vagy SP2 (Enterprise, standard vagy Developer) a Windows Server 2016 Server rendszerképén. 
- [SQL Server IaaS-bővítmény](/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) 1.3.20180 vagy újabb verziója. Az SQL IaaS bővítmény telepíti a szükséges összetevőket, amelyekre szükség van a piactér SQL Server minden Windows-verzióhoz tartozó elemhez. Lehetővé teszi az SQL-specifikus beállítások konfigurálását az SQL-alapú virtuális gépeken (VM). Ha a bővítmény nincs telepítve a helyi piactéren, az SQL kiépítés sikertelen lesz.
- [Egyéni parancsfájl-bővítmény a Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) 1.9.1 vagy újabb verziójához. Az egyéni szkriptek bővítménye egy eszköz, amellyel automatikusan elindítható a telepítés utáni virtuális gépek testreszabási feladatai.
- A [PowerShell kívánt állapot-konfiguráció (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) 2.76.0.0 vagy újabb verziója. A DSC a Windows PowerShell egyik felügyeleti platformja, amely lehetővé teszi a szoftveres szolgáltatások konfigurációs információinak üzembe helyezését és kezelését. A platform emellett azt a környezetet is kezeli, amelyben ezek a szolgáltatások futnak.

Ha többet szeretne megtudni az elemek Azure Stack piactérről való hozzáadásáról, tekintse meg a [Azure stack hub Marketplace áttekintése](azure-stack-marketplace.md)című témakört.

## <a name="create-a-sql-server-alwayson-availability-group"></a>SQL Server alAlwaysOnon rendelkezésre állási csoport létrehozása

Az ebben a szakaszban ismertetett lépések segítségével telepítse a SQL Server alAlwaysOnon rendelkezésre állási csoportot az [SQL-2016-AlwaysOn Azure stack hub](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson)rövid útmutatójának használatával. Ez a sablon két SQL Server Enterprise-vagy fejlesztői példányt telepít egy always on rendelkezésre állási csoportba. A következő erőforrásokat hozza létre:

- Egy hálózati biztonsági csoport.
- Egy virtuális hálózat.
- Négy Storage-fiók (az egyik a Active Directory (AD), egy az SQL, egy a fájlmegosztás tanúsító, egy pedig a VM Diagnostics esetében).
- Négy nyilvános IP-cím (egy az AD-hoz, kettő az egyes SQL-alapú virtuális gépekhez, a másik pedig az SQL AlwaysOn-figyelőhöz kötött nyilvános Load Balancer).
- Egy külső terheléselosztó az SQL AlwaysOn-figyelőhöz kötött, nyilvános IP-címmel rendelkező SQL virtuális gépekhez.
- Egy virtuális gép (Windows Server 2016), amely tartományvezérlőként van konfigurálva egyetlen tartománnyal rendelkező új erdőhöz.
- Két virtuális gép (Windows Server 2016), amely SQL Server 2016 SP1 vagy SP2 Enterprise vagy Developer Edition rendszerrel van konfigurálva, és fürtözött. Ezeknek Piactéri lemezképeknek kell lenniük.
- Egy virtuális gép (Windows Server 2016), amely tanúsító fájlmegosztásként van konfigurálva a fürthöz.
- Egy rendelkezésre állási csoport, amely az SQL és a fájlmegosztás tanúsító virtuális gépeket tartalmazza.

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Válassza **\+** **az erőforrás létrehozása**  >  **Egyéni**lehetőséget, majd **template Deployment**.

   ![Egyéni sablonok üzembe helyezése Azure Stack hub felügyeleti portálján](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-1.png)

3. Az **Egyéni üzembe helyezés** panelen válassza a **sablon**-  >  **Gyorsindítás sablon** szerkesztése lehetőséget, majd az elérhető egyéni sablonok legördülő listájában válassza ki az **SQL-2016-AlwaysOn** sablont. Válassza az **OK**, majd a **Mentés** lehetőséget.

   [![Sablon szerkesztése az Azure Stack hub felügyeleti portálján](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-2.png "Gyorsindítás sablon kiválasztása")](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-2.png#lightbox)

4. Az **Egyéni telepítés** panelen válassza a **Paraméterek szerkesztése** lehetőséget, és tekintse át az alapértelmezett értékeket. Módosítsa az értékeket szükség szerint az összes kötelező paraméter információjának megadásához, majd kattintson **az OK gombra**.

    Legalább:
    - Összetett jelszavak megadása a ADMINPASSWORD, a SQLSERVERSERVICEACCOUNTPASSWORD és a SQLAUTHPASSWORD paraméterekhez.
    - Adja meg a névkeresési DNS-utótagot a DNSSUFFIX paraméterhez tartozó összes kisbetűs karakternél (**azurestack. external** , ASDK-telepítés esetén).
    
   [![Paraméterek szerkesztése Azure Stack hub felügyeleti portálon](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-3.png "Egyéni telepítési paraméterek szerkesztése")](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-3.png#lightbox)

5. Az **Egyéni telepítés** panelen válassza ki a használni kívánt előfizetést, és hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő erőforráscsoportot az egyéni telepítéshez.

    Ezután válassza ki az erőforráscsoport helyét (ASDK-telepítések esetén**helyi** ), majd kattintson a **Létrehozás**gombra. A rendszer érvényesíti az egyéni központi telepítési beállításokat, majd elindítja az üzemelő példányt.

    [![Előfizetés kiválasztása Azure Stack hub felügyeleti portálján](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-4.png "Egyéni központi telepítés létrehozása")](media/azure-stack-tutorial-sqlrp/aoag-template-deployment-4.png#lightbox)

6. A felhasználói portálon válassza az **erőforráscsoportok** lehetőséget, majd az egyéni telepítéshez létrehozott erőforráscsoport nevét (ehhez a példához tartozó**Erőforrás-csoport** ). Tekintse meg a központi telepítés állapotát, és győződjön meg arról, hogy az összes központi telepítés sikeresen befejeződött.
    
    Ezután tekintse át az erőforráscsoport elemeit, és válassza ki a **SQLPIPsql \<resource group name\> ** nyilvános IP-cím elemet. Jegyezze fel a nyilvános IP-címet és a terheléselosztó nyilvános IP-címének teljes TARTOMÁNYNEVÉt. Ezt az SQL alAlwaysOnon rendelkezésre állási csoportot kihasználó SQL-üzemeltetési kiszolgáló létrehozásához meg kell adnia egy Azure Stack hub-kezelőnek.

   > [!NOTE]
   > A sablon központi telepítése több órát is igénybe vesz.

### <a name="enable-automatic-seeding"></a>Automatikus előkészítés engedélyezése

A sablon sikeres üzembe helyezése és az SQL alAlwaysONon rendelkezésre állási csoport konfigurálása után engedélyeznie kell a rendelkezésre állási csoportban a SQL Server minden egyes példányán az [automatikus](/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) betöltést.

Ha automatikus beültetést tartalmazó rendelkezésre állási csoportot hoz létre, a SQL Server automatikusan létrehozza a másodlagos replikákat a csoportban lévő összes adatbázishoz anélkül, hogy más manuális beavatkozásra lenne szükség. Ez a mérték biztosítja a AlwaysOn-adatbázisok magas rendelkezésre állását.

Ezeket az SQL-parancsokat az alAlwaysOnon rendelkezésre állási csoport automatikus beültetésének konfigurálására használhatja. Cserélje le `<PrimaryInstanceName>` az értékét az elsődleges példányra SQL Server nevére, `<SecondaryInstanceName>` a másodlagos példánnyal SQL Server a nevet, az `<availability_group_name>` alAlwaysOnon rendelkezésre állási csoport nevét pedig szükség szerint.

Az elsődleges SQL-példányon:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<PrimaryInstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<SecondaryInstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

![Elsődleges SQL-példány parancsfájlja](./media/azure-stack-tutorial-sqlrp/sql1.png)

Másodlagos SQL-példányok esetén:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

![Másodlagos SQL-példány parancsfájlja](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Tárolt adatbázis-hitelesítés konfigurálása

Egy tárolt adatbázis rendelkezésre állási csoportba való felvétele előtt győződjön meg arról, hogy a tárolt adatbázis-hitelesítési kiszolgáló beállítás értéke 1 minden olyan kiszolgálópéldány esetében, amely rendelkezésre állási replikát futtat a rendelkezésre állási csoport számára. További információ: [tárolt adatbázis-hitelesítés](/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Ezekkel a parancsokkal állíthatja be a rendelkezésre állási csoportba tartozó egyes SQL Server-példányok tárolt adatbázis-hitelesítési kiszolgáló beállítását:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

![Tárolt adatbázis-hitelesítés beállítása](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-hub-sql-hosting-server"></a>Azure Stack hub SQL üzemeltetési kiszolgáló létrehozása

Az SQL Server alAlwayOnon rendelkezésre állási csoport létrehozása és megfelelő konfigurálása után egy Azure Stack hub-operátornak létre kell hoznia egy Azure Stack hub SQL üzemeltetési kiszolgálót. Az SQL üzemeltetési kiszolgáló teszi elérhetővé a további kapacitást a felhasználók számára adatbázisok létrehozásához.

Ügyeljen arra, hogy a nyilvános IP-címet vagy a teljes FQDN-t használja az SQL-AlwaysOn rendelkezésre állási csoportjának létrehozásakor (**SQLPIPsql \<resource group name\> **) korábban rögzített SQL Load Balancer nyilvános IP-címéhez. Emellett ismernie kell az alAlwaysOnon rendelkezésre állási csoportban található SQL-példányokhoz való hozzáféréshez használt SQL Server hitelesítő adatokat.

> [!NOTE]
> Ezt a lépést a Azure Stack hub felügyeleti portálján Azure Stack hub-kezelővel kell futtatni.

Az SQL alAlwaysOnon rendelkezésre állási csoport terheléselosztó figyelő nyilvános IP-címének és SQL-hitelesítésének bejelentkezési információinak használatával egy Azure Stack hub-kezelő [létrehozhat egy SQL-üzemeltetési kiszolgálót az SQL AlAlwaysOnon rendelkezésre állási csoport segítségével](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Győződjön meg arról is, hogy létrehozta a csomagokat és ajánlatokat, hogy az SQL AlwaysOn-adatbázis létrehozása elérhető legyen a felhasználók számára. Az operátornak hozzá kell adnia a **Microsoft. SqlAdapter** szolgáltatást egy csomaghoz, és egy új kvótát kell létrehoznia kifejezetten a nagy rendelkezésre állású adatbázisokhoz. A csomagok létrehozásával kapcsolatos további információkért lásd: [szolgáltatás, csomag, ajánlat, előfizetés áttekintése](service-plan-offer-subscription-overview.md).

> [!TIP]
> A **Microsoft. SqlAdapter** szolgáltatás nem lesz elérhető a tervekhez való hozzáadáshoz, amíg a [SQL Server erőforrás-szolgáltató nincs telepítve](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Magasan elérhető SQL-adatbázis létrehozása

Miután az SQL alAlwaysOnon rendelkezésre állási csoportot létrehozta, konfigurálta és hozzáadta Azure Stack hub SQL üzemeltetési kiszolgálóként egy Azure Stack hub-kezelővel, az előSQL Server fizetést tartalmazó bérlői felhasználó létrehozhat olyan SQL-adatbázisokat, amelyek támogatják a AlwaysOn funkciót. Ezek az adatbázisok a jelen szakasz lépéseinek követésével hozhatók létre.

> [!NOTE]
> Futtassa ezeket a lépéseket az Azure Stack hub felhasználói portálon bérlői felhasználóként egy olyan előfizetéssel, amely SQL Server képességeket (Microsoft. SQLAdapter szolgáltatás) biztosít.

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Válassza **\+** **az erőforrás létrehozása**  >  ** \+ adattároló**lehetőséget, majd **SQL Database**.

    Adja meg a szükséges adatbázis-tulajdonság adatait. Ez az információ tartalmazza a telepítéshez használandó nevet, rendezést, maximális méretet, valamint az előfizetést, az erőforráscsoportot és a helyet.

   ![SQL-adatbázis létrehozása Azure Stack hub felhasználói portálon](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Válassza az **SKU** lehetőséget, majd válassza ki a használni kívánt SQL üzemeltetési kiszolgáló SKU-t. Ebben a példában a Azure Stack hub-kezelő létrehozta a **vállalati-ha SKU-** t az SQL alAlwaysOnon rendelkezésre állási csoportok magas rendelkezésre állásának támogatásához.

   ![SKU kiválasztása Azure Stack hub felhasználói portálon](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Válassza a **Bejelentkezés**  >  **új bejelentkezés létrehozása** lehetőséget, majd adja meg az új adatbázishoz használni kívánt SQL-hitelesítési hitelesítő adatokat. Ha elkészült, válassza az **OK** , majd a **Létrehozás** lehetőséget az adatbázis-telepítési folyamat megkezdéséhez.

   ![Bejelentkezés létrehozása Azure Stack hub felhasználói portálon](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Ha az SQL-adatbázis központi telepítése sikeresen befejeződött, tekintse át az adatbázis tulajdonságait, és keresse meg az új, magasan elérhető adatbázishoz való csatlakozáshoz használandó kapcsolati karakterláncot.

   ![Azure Stack hub felhasználói portálján megtekintheti a kapcsolatok karakterláncát](./media/azure-stack-tutorial-sqlrp/createdb4.png)

## <a name="next-steps"></a>Következő lépések

[Az SQL típusú erőforrás-szolgáltató frissítése](azure-stack-sql-resource-provider-update.md)
