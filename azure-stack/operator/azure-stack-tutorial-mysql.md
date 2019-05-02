---
title: Ajánlat magas rendelkezésre állású MySQL-adatbázisok az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy MySQL-kiszolgáló erőforrás-szolgáltató gazdaszámítógép és a magas rendelkezésre állású MySQL-adatbázisok az Azure Stack használatával.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: f620b8f2d1abe4d656e8f7cf8c34a5798d517e2a
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64300515"
---
# <a name="tutorial-offer-highly-available-mysql-databases"></a>Oktatóanyag: Magas rendelkezésre állású MySQL-adatbázisok ajánlat

Kezelőként Azure Stack server rendszerű virtuális gépekhez, amelyiken a MySQL-kiszolgáló is beállíthatja. A MySQL után fürt van közé tartoznak sikeresen létrehozott, és kezeli az Azure Stack, felhasználók, akik a MySQL-szolgáltatásai feliratkozott könnyedén hozhat létre magas rendelkezésre állású MySQL-adatbázisok.

Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure Stack piactéren elemek használatával egy [MySQL-fürt replikációs](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). A megoldás több virtuális gép használja, az adatbázisok replikálása a fő csomópont replikák konfigurálható száma. Miután létrehozott, a fürt majd hozzáadhat egy Azure Stack MySQL futtató kiszolgálóval, és a felhasználók hozhatnak létre egy magas rendelkezésre állású MySQL-adatbázisok.

> [!IMPORTANT]
> A **MySQL-replikáció** Azure Stack Piactéri elem nem feltétlenül érhető el az összes Azure-felhő előfizetési környezetet. Ellenőrizze, hogy a Piactéri elem előtt hajtsa végre a fennmaradó részében tutoral ebben az előfizetésben elérhető-e.

Amiről tanulni fog:

> [!div class="checklist"]
> * Hozzon létre egy MySQL-kiszolgáló fürtöt a marketplace-elemek
> * Hozzon létre egy Azure Stack-MySQL-kiszolgálót futtató
> * Magas rendelkezésre állású MySQL-adatbázis létrehozása

Ebben az oktatóanyagban egy három virtuális gép MySQL Server-fürt jön létre, és a rendelkezésre álló Azure Stack piactéren elemek konfiguráltak. 

Ebben az oktatóanyagban a lépések előtt ellenőrizze, hogy a [MySQL-kiszolgáló erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md) sikeresen telepítve lett, és hogy a következő elemek érhetők el az Azure Stack piactéren:

> [!IMPORTANT]
> Az alábbiak mindegyike a MySQL-fürt létrehozásához szükséges.

- [MySQL-replikáció](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Ez az a Bitnami-megoldássablon, a MySQL-fürt üzembe helyezéséhez használt.
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian). Debian 8 credativ által biztosított Microsoft Azure portolások kernel "Jessie". Debian GNU/Linux egyike a népszerű Linux-disztribúció.
- [Egyéni parancsfájl Linux 2.0](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview). Egyéni szkriptek futtatására szolgáló bővítmény egy olyan eszköz, a virtuális gépek testreszabási feladatok post virtuális gép kiépítése végrehajtásához. Ezzel a bővítménnyel való felvételekor egy virtuális gépet, akkor szkriptjeinek letöltése az Azure storage-ból, és a virtuális gépen futtatni őket. Egyéni szkriptek futtatására szolgáló bővítmény feladatokat az Azure PowerShell-parancsmagok és az Azure többplatformos parancssori felület (xPlat-CLI) használatával is automatizálható.
- Virtuálisgép-hozzáférési 1.4.7 Linux-bővítmény. A Virtuálisgép-hozzáférési bővítmény lehetővé teszi, hogy alaphelyzetbe a jelszót, SSH-kulcs vagy az SSH-konfigurációk, így a virtuális gép is hozzáférhet. Jelszó vagy SSH-kulcsot az új felhasználó hozzáadása, vagy törli egy felhasználó e bővítmény használatával is. Ez a bővítmény a Linux rendszerű virtuális gépek célozza.

Az Azure Stack piactéren elemek hozzáadásával kapcsolatos további tudnivalókért tekintse meg a [Azure Stack piactéren – áttekintés](azure-stack-marketplace.md).

SSH-ügyfelet, például is kell [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) telepítésük után a Linux virtuális gépre bejelentkezni.

## <a name="create-a-mysql-server-cluster"></a>A MySQL Server-fürt létrehozása 
Ebben a szakaszban a MySQL-kiszolgáló telepítése a lépéseket a fürt használatával használja a [MySQL-replikáció](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) Piactéri elem. Ez a sablon üzembe helyezi a magas rendelkezésre állású MySQL fürtben konfigurált három MySQL Server-példányok. Alapértelmezés szerint létrehoz az alábbi forrásanyagokat:

- Virtuális hálózat
- A hálózati biztonsági csoport
- Storage-fiók
- Rendelkezésre állási csoport
- Három hálózati adapterek (egy, az alapértelmezett virtuális gépek mindegyike esetében)
- Egy nyilvános IP-cím (az elsődleges MySQL-fürt virtuális gép)
- Három Linux rendszerű virtuális gépek, a MySQL-fürt üzemeltetéséhez

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Válassza ki **\+** **erőforrás létrehozása** > **számítási**, majd **MySQL-replikáció**.

   ![Egyéni sablon telepítése](media/azure-stack-tutorial-mysqlrp/1.png)

3. Alapszintű központi telepítést információt biztosítsanak a **alapjai** lapot. Tekintse át az alapértelmezett értékeket, és szükség szerint változtassa meg, és kattintson a **OK**.<br><br>Legalább a következőket biztosítják:
   - Központi telepítés nevét (alapértelmezés szerint a mymysql)
   - Alkalmazás gyökér szintű jelszó. Adjon meg egy 12 karakteres alfanumerikus jelszó **semmilyen különleges karakterek**
   - Alkalmazás-adatbázis neve (alapértelmezés szerint a bitnami)
   - MySQL-adatbázis-replikát hozhat létre virtuális gépek száma (az alapértelmezett érték 2)
   - Válassza ki a használni kívánt előfizetést
   - Válassza ki az erőforráscsoportot, vagy hozzon létre egy újat
   - Válassza ki azt a helyet (alapértelmezés szerint a helyi a ASDK)

   [![](media/azure-stack-tutorial-mysqlrp/2-sm.PNG "Telepítési alapelvei")](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. Az a **környezet konfigurációjának** lapon adja meg a következő információkat, majd kattintson **OK**: 
   - Jelszó vagy SSH nyilvános kulcs a secure shell-(SSH) hitelesítéshez használandó. Ha jelszót használ, akkor tartalmaznia kell betűket, számokat és **is** tartalmazhat speciális karaktereket
   - Virtuálisgép-méret (alapértelmezés szerint a Standard D1 v2 virtuális gép)
   - Adatlemez méretét GB-os kattintson **OK**

   [![](media/azure-stack-tutorial-mysqlrp/3-sm.PNG "Környezet konfigurálása")](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. Tekintse át a központi telepítés **összefoglalás**. Ha szeretné, a testreszabott sablon és paraméterek letöltése, és kattintson **OK**.

   [![](media/azure-stack-tutorial-mysqlrp/4-sm.PNG "Összefoglalás")](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. Kattintson a **létrehozás** a a **vásárlása** lapon a telepítés elindításához.

   ![Vásárlás](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > A telepítés körülbelül egy óra alatt vesz igénybe. Győződjön meg arról, hogy a telepítés véget ért, és a MySQL-fürt teljesen konfigurálva a folytatás előtt. 

7. Miután az összes központi telepítés sikeresen befejeződött, tekintse át az erőforrás-csoport elemeket, és válassza ki a **mysqlip** nyilvános IP-cím elemet. Jegyezze fel a nyilvános IP-cím és a nyilvános IP-cím a fürt teljes Tartománynevét.<br><br>Szüksége lesz egy Azure Stack-operátorokról biztosít ez így is létrehozhatnak, a üzemeltetési MySQL-kiszolgálóhoz, kihasználva a MySQL-fürt.


### <a name="create-a-network-security-group-rule"></a>Biztonságicsoport-szabály létrehozása
Alapértelmezés szerint nincs nyilvános hozzáférés van konfigurálva MySQL-hez, a gazdagép virtuális gép. Az Azure Stack MySQL erőforrás-szolgáltató csatlakoztatása és a MySQL-fürt kezelése egy bejövő hálózati biztonsági csoport (NSG) szabályt kell létrehozni.

1. A felügyeleti portálon keresse meg a MySQL-fürt üzembe helyezésekor létrehozott erőforráscsoportot, és válassza ki a hálózati biztonsági csoport (**alapértelmezett-alhálózat-sg**):

   ![nyitás](media/azure-stack-tutorial-mysqlrp/6.png)

2. Válassza ki **bejövő biztonsági szabályok** majd **Hozzáadás**.<br><br>Adja meg **3306-os** a a **Célporttartomány** , és megadhat egy leírást a a **neve** és **leírás** mezőket. Kattintson a Hozzáadás gombra kattintva zárja be a bejövő biztonsági szabály párbeszédpanel.

   ![nyitás](media/azure-stack-tutorial-mysqlrp/7.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>A MySQL-fürt külső hozzáférés konfigurálása
A MySQL-fürt hozzáadása az Azure Stack MySQL-kiszolgáló gazdagépként, előtt külső hozzáférés engedélyezve kell lennie.

1. SSH-ügyfelet használ, ez a példa [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), jelentkezzen be az elsődleges MySQL gép olyan számítógépről, amely hozzáférhet a nyilvános IP-cím. Az elsődleges MySQL virtuális gép neve általában végződik **0** és a egy nyilvános IP-cím hozzárendelve.<br><br>Használja a nyilvános IP, és jelentkezzen be, amelynek a virtuális **bitnami** és speciális karakterek nélküli korábban létrehozott alkalmazás jelszavát.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. Az SSH-ügyfél ablakban a következő parancs használatával győződjön meg arról, a bitnami szolgáltatás nem aktív, és futó. Amikor a rendszer kéri, adja meg újra a bitnami jelszót:

   `sudo service bitnami status`

   ![Ellenőrizze a szolgáltatás](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Csatlakozhat a MySQL-hez, és zárja be az SSH-ügyfél az Azure Stack MySQL-üzemeltető kiszolgáló által használandó távelérési felhasználói fiók létrehozása.<br><br>Futtassa az alábbi parancsokat rendszergazdaként, a korábban létrehozott rendszergazdai jelszó használata a MySQL bejelentkezni, és hozzon létre egy új rendszergazdai felhasználóhoz, cserélje le *\<felhasználónév\>* és *\<jelszó\>* a környezetéhez szükséges. Ebben a példában a felhasználó a létrehozandó nevű **sqlsa** és a egy erős jelszót használja:

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![Rendszergazda felhasználó létrehozása](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. Jegyezze fel az új MySQL-felhasználó információt.<br><br>Adja meg ezt a felhasználónevet és jelszót, és a nyilvános IP-cím vagy teljes Tartománynevet a fürthöz, a nyilvános IP-cím, így is létrehozhatnak, a üzemeltetési MySQL-kiszolgálóhoz, a MySQL-fürt használatával az Azure Stack-operátorokról kell.


## <a name="create-an-azure-stack-mysql-hosting-server"></a>Hozzon létre egy Azure Stack-MySQL-kiszolgálót futtató
Miután az MySQL Server-fürt létrehozása, és megfelelően konfigurálva, az Azure Stack-operátorokról létre kell hoznia egy Azure Stack MySQL üzemeltető kiszolgálót, hogy a megnövelt kapacitás adatbázisok létrehozására a felhasználók számára elérhetővé tenni. 

Ügyeljen arra, hogy a nyilvános IP-címét a MySQL-fürt elsődleges virtuális gép rögzítve korábban a MySQL-fürt erőforráscsoport létrehozásakor használja a nyilvános IP-cím vagy teljes Tartománynevet (**mysqlip**). Emellett az operátor a MySQL-kiszolgálóhoz a MySQL-adatbázis fürt távoli eléréséhez létrehozott hitelesítő adatok ismernie kell.

> [!NOTE]
> Ebben a lépésben egy Azure Stack operátorait szerint kell futtatni az Azure Stack felügyeleti portálról.

A MySQL-fürt nyilvános IP-cím és a MySQL hitelesítési bejelentkezési adatok használata esetén az Azure Stack – operátor mostantól [MySQL-üzemeltető kiszolgáló létrehozása az új MySQL-fürt használatával](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server). 

Emellett győződjön meg arról, hogy már létrehozott terveket, és felajánlja, hogy a MySQL-adatbázis létrehozása a felhasználók számára elérhetővé tenni. Az operátornak hozzá kell adnia a **Microsoft.MySqlAdapter** díjcsomagra szolgáltatást, és hozzon létre egy új kvóta kifejezetten a magas rendelkezésre állású adatbázisok. Csomag létrehozásával kapcsolatos további információkért lásd: [csomag, ajánlat, kvóta és előfizetés áttekintése](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> A **Microsoft.MySqlAdapter** szolgáltatás nem lesz elérhető, amíg csomagok hozzáadása a [MySQL-kiszolgáló erőforrás-szolgáltató van telepítve](azure-stack-mysql-resource-provider-deploy.md).



## <a name="create-a-highly-available-mysql-database"></a>Magas rendelkezésre állású MySQL-adatbázis létrehozása
A MySQL-fürt létrehozása, konfigurálása és Azure Stack MySQL üzemeltető kiszolgálót az Azure Stack – operátor által hozzáadott, után a bérlői felhasználó egy előfizetéssel, beleértve a MySQL-kiszolgáló adatbázis-képességeket hozhat létre a magas rendelkezésre állású MySQL-adatbázisok Ebben a szakaszban leírt lépések végrehajtásával. 

> [!NOTE]
> Futtassa ezeket a lépéseket az Azure Stack felhasználói portálról bérlői felhasználói biztosít a MySQL-kiszolgáló képességet (Microsoft.MySQLAdapter szolgáltatás) előfizetéssel.

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Válassza ki **\+** **erőforrás létrehozása** > **adatok \+ tárolási**, majd **MySQL-adatbázis** .<br><br>Adja meg a szükséges adatbázis-tulajdonság információkat, beleértve a neve, rendezés, a használni kívánt előfizetést és helyet a központi telepítéshez használni. 

   ![MySQL-adatbázis létrehozása](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Válassza ki **Termékváltozat** és válassza ki a megfelelő MySQL üzemeltető kiszolgáló Termékváltozat használatára. Ebben a példában az Azure Stack-operátorokról hozott létre a **MySQL – magas rendelkezésre ÁLLÁS** Termékváltozat, a MySQL-fürt adatbázisok magas rendelkezésre állás megvalósításához.

   ![SKU kiválasztása](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Válassza ki **bejelentkezési** > **hozzon létre egy új bejelentkezést** , és adja meg a MySQL hitelesítés hitelesítő adatokat, amelyek az új adatbázisnak. Ha befejezte, kattintson a **OK** , majd **létrehozás** az adatbázis üzembe helyezés megkezdéséhez.

   ![Bejelentkezés felvétele](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. Ha a MySQL-adatbázis üzembe helyezése sikeresen befejeződött, tekintse át az adatbázis tulajdonságainak felderítése a kapcsolati karakterláncot az új magas rendelkezésre állású adatbázishoz való kapcsolódáskor használandó. 

   ![Kapcsolati karakterlánc megtekintése](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Hozzon létre egy MySQL-kiszolgáló fürtöt a marketplace-elemek
> * Hozzon létre egy Azure Stack-MySQL-kiszolgálót futtató
> * Magas rendelkezésre állású MySQL-adatbázis létrehozása

A következő oktatóanyaggal, amelyben tudatjuk a felhasználókkal hogyan:
> [!div class="nextstepaction"]
> [Webalkalmazások biztosítása](azure-stack-tutorial-app-service.md)
