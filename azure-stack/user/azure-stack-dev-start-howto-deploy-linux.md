---
title: Linux rendszerű virtuális gép üzembe helyezése az Azure Stackhez |} A Microsoft Docs
description: Az Azure Stack-alkalmazás üzembe helyezése.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 61d9f21f35edf1a0e8ebf61c81580c4d53218970
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617670"
---
# <a name="deploy-a-linux-vm-to-host-a-web-app-in-azure-stack"></a>Linux virtuális gép üzembe helyezése az Azure Stackben webalkalmazás üzemeltetéséhez

Hozzon létre, és az alapszintű Linux rendszerű virtuális gép üzembe helyezése (VM) által az Ubuntu-rendszerkép használata az Azure Marketplace-en, amely egy webes keretrendszer használatával létrehozott webalkalmazás üzemeltetéséhez. 

Ez a virtuális gép üzemeltethet webalkalmazásokat használatával:

- **Python**: Közös Python webes keretrendszert a Flask-, Bottle és Django közé tartozik.
- **Nyissa meg**: Közös nyissa meg a keretrendszerek Revel, Martini, Gocraft/webes és Gorilla tartalmazza. 
- **Ruby**: Állítsa be a Ruby on Rails keretet másként a Ruby webalkalmazásokhoz küldéséhez. 
- **Java**: A Java webalkalmazásokat, amely az Apache Tomcat kiszolgálót az való fejlesztéséhez. Tomcat telepítése Linux rendszeren, és ezután közvetlenül üzembe helyezhetők a Java WAR-fájlokat a kiszolgálón. 

Ez a cikk utasításokat segítségével elsajátíthatja a használatát bármely web app, keretrendszer és a háttér-technológia, amely a Linux operációs rendszer. Ezután használhatja az Azure Stack az infrastruktúra kezelését, és a felügyeleti eszközök használata a technológia belül a karbantartási feladatok az alkalmazások kezeléséhez.

## <a name="deploy-a-linux-vm-for-a-web-app"></a>Linux rendszerű virtuális gép egy webalkalmazás üzembe helyezése

Ebben a folyamatban, hozzon létre egy titkos kulcsot, az alaprendszerképet a Linux rendszerű virtuális gép használja, adja meg a virtuális gép meghatározott attribútumok és majd a virtuális gép létrehozásához. Miután létrehozta a virtuális gép, nyissa meg a portokat, amely ahhoz szükséges, a virtuális gép használata a virtuális gép az alkalmazás futtatásához. Ezután hozzon létre DNS-nevét. Végül csatlakozzon a virtuális Géphez, és a gép frissítse az apt-get-segédprogram használatával. A folyamat elvégzése után rendelkezni fog egy virtuális Gépet, hogy a webalkalmazás üzemeltetéséhez készen áll az Azure Stack-példányában.

Mielőtt elkezdené, győződjön meg arról, hogy minden, amit helyen rendelkezésére.

## <a name="prerequisites"></a>Előfeltételek

- Azure Stack-előfizetéshez, az Ubuntu Server 16.04 LTS-rendszerképhez hozzáférést. A rendszerkép egy újabb verzióját is használhat, de ezek az utasítások a 16.04 LTS szem előtt az írt. Ha ez a rendszerkép nem rendelkezik, lépjen kapcsolatba a felhő üzemeltetője a kép lekérésének az Azure Stack piactéren.

## <a name="deploy-the-vm-by-using-the-portal"></a>Telepítse a virtuális Gépet a portál használatával

A virtuális gép üzembe helyezéséhez kövesse az utasításokat a következő néhány szakaszban.

### <a name="create-your-vm"></a>A virtuális gép létrehozása

1. Hozzon létre egy Secure Shell (SSH) a kiszolgáló nyilvános kulcsot. További információkért lásd: [nyilvános SSH-kulcs használatával](azure-stack-dev-start-howto-ssh-public-key.md).
1. Válassza ki az Azure Stack portálon **erőforrás létrehozása** > **számítási** > **Ubuntu Server 16.04 LTS**.

    ![Virtuális géphez az Azure Stack-webalkalmazás üzembe helyezése](media/azure-stack-dev-start-howto-deploy-linux/001-portal-compute.png)

4. Az a **hozzon létre egy virtuális gépet** panelen a **1. Konfigurálja az alapbeállításokat**:

    a. Adja meg a **a virtuális gép nevét**.

    b. Válassza ki a **virtuális merevlemez típusa**, vagy **prémium szintű SSD** (a prémium szintű lemezek [SSD]) vagy **Standard HDD** (a standard szintű lemezek [HDD]).

    c. Adja meg a **felhasználónév**.

    d. Válassza ki a **hitelesítési típus** , **nyilvános SSH-kulcsból**.

    e. Az Ön által létrehozott nyilvános SSH-kulcs lekéréséhez. Nyissa meg egy szövegszerkesztőben, másolja a kulcsot, és illessze be azt a **nyilvános SSH-kulcs** mezőbe. A szöveg `---- BEGIN SSH2 PUBLIC KEY ----` való `---- END SSH2 PUBLIC KEY ----`. Illessze be a teljes szöveges kódblokkot kulcs:

    ```text  
    ---- BEGIN SSH2 PUBLIC KEY ----
    Comment: "rsa-key-20190207"
    <Your key block>
    ---- END SSH2 PUBLIC KEY ----
    ```

    f. Válassza ki az előfizetést az Azure Stack-példány.

    g. Hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt, attól függően, hogyan szeretné az alkalmazás számára az erőforrások rendszerezéséhez.

    h. Válassza ki a helyet. Az Azure Stack Development Kit (ASDK) általában egy *helyi* régióban. A hely az Azure Stack-példány függ.
1. A **2. Méret**, írja be:
    - Válassza ki az adatok méretétől és RAM a virtuális gép, amely az Azure Stack-példány érhető el.
    - Vagy megnyithatja a lista vagy a szűrő által a virtuális gép méretének **számítási típus**, **processzorok**, és **tárolóhely**.
    
    > [!NOTE]
    > - Az árak a helyi pénznemben számított becslések. Csak az Azure infrastrukturális költségeit és előfizetésekhez és helyekhez járó kedvezményeket tartalmazzák. Ezek magukban a szoftverek vonatkozó díjait nem tartalmazza. 
    > - Az ajánlott méreteket a kiválasztott lemezkép kiadója határozza meg, és a hardver- és szoftverkövetelményeiről alapulnak.
    > - A standard szintű (HDD) ahelyett, hogy a felügyelt lemezek prémium szintű (SSD) hatással lehet az operációs rendszer teljesítményét.

1. A **3. Opcionális konfigurálása** funkciók, írja be:

    a. A **magas rendelkezésre állás érdekében** válassza ki a rendelkezésre állási csoportban. Az alkalmazás redundanciájának biztosítsa érdekében, hogy csoportban legalább két virtuális gépet egy rendelkezésre állási csoportban. Ez a konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép fog érhető el, és megfeleljen a 99,95 %-os Azure szolgáltatásiszint-szerződés (SLA). A rendelkezésre állási csoport egy virtuális gép a létrehozása után nem módosítható.

    b. A **tárolási**válassza **felügyelt lemezek prémium szintű (SSD)** vagy **a standard szintű lemezek (HDD)**. Felügyelt lemezek prémium szintű (SSD) élvezik SSD-meghajtókat, és egységes, közel valós idejű teljesítményt. Ezek a legjobb ára és teljesítménye közötti egyensúlyt biztosítanak, és ideálisak a nagy I/O-igényes alkalmazások és a termelési számítási feladatokhoz. A standard szintű lemezek mágneses meghajtókon biztonsági és alkalmazások általában hol adatokhoz ritkán. A zónaredundáns lemezeket a zónaredundáns tárolás (ZRS), amely az adatokat több zónában replikálja, élvezik, és azok elérhetők, még akkor is, ha egy zóna nem működik. 

    c. Válassza ki **felügyelt lemezek használata**. Ha engedélyezi ezt a funkciót, az Azure automatikusan kezeli a lemezt a rendelkezésre állási. Saját storage-fiókok létrehozása és kezelése nélkül a adatredundanciát és hibatűrést, előnyeit. Felügyelt lemezeket nem érhető el minden régióban. További információkért lásd: [Bevezetés az Azure-ba, felügyelt lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

    d. A hálózat konfigurálásához válasszon **virtuális hálózat**. Virtuális hálózatok logikailag teljesen elkülönülnek egymástól az Azure-ban. Konfigurálhatja az IP címtartományok, az alhálózatok, útválasztási táblázatokat, átjárók és biztonsági beállítások, a hagyományos az Adatközpont hálózatának hasonlóan. Az azonos virtuális hálózatban lévő virtuális gépek képesek elérni egymást alapértelmezés szerint. 

    e. Az alhálózat konfigurálásához válasszon **alhálózati**. Egy alhálózat egy IP-címtartományt a virtuális hálózaton. Egy alhálózat használatával elkülöníteni a virtuális gépek egymástól vagy az internetről. 

    f. A virtuális géphez, vagy a virtuális Gépen futó szolgáltatásokhoz való hozzáférés konfigurálásához válasszon **nyilvános IP-cím**. Nyilvános IP-cím használatával kommunikálni a virtuális gépet a virtuális hálózaton kívülről. 

    g. Válassza ki **hálózati biztonsági csoport**, **alapszintű**, vagy **speciális**. Állítsa be a szabályokat, amelyek engedélyezik vagy megtagadják a hálózati forgalmat a virtuális géphez. 

    h. Az általános és egyéni protokollokat hozzáférés beállítása a virtuális gép, jelölje be **nyilvános bejövő portok**. A szolgáltatás a szabály a cél protokoll és -porttartományát határozza meg. Választhat egy előre definiált szolgáltatást, például a távoli asztal protokoll (RDP) vagy ssh-t, vagy megadhat egy egyéni porttartományt. 
        A webkiszolgáló (80-as) HTTP, HTTPS (443) és az SSH (22) nyissa meg használni. Ha azt tervezi, a gép RDP-kapcsolatok kezeléséhez, nyissa meg a 3389-es portot.

    i. Bővítmények hozzáadása a virtuális gép, jelölje be a **bővítmények**. Bővítmények hozzáadása a virtuális gép új funkciói, például a konfigurációkezelés vagy a víruskeresés. 

    j. Letiltani vagy engedélyezni **figyelés**. Indítási hibák diagnosztizálásához érdekében használhatja figyelése a soros konzol kimenetét és a egy gazdagépen futó virtuális gépek képernyőképek rögzítése. 

    k. A storage-fiók csatlakoztatásához a metrikákat, adja meg **diagnosztikai tárfiók**. Metrikák írt storage-fiókba, hogy a saját eszközökkel elemezheti őket. 

    l. Kattintson az **OK** gombra.

1. Felülvizsgálat **4. Összefoglalás**:
    - A portál érvényesíti a beállításokat.
    - Újból felhasználhatja az Azure Resource Manager-munkafolyamat a beállítások, töltse le az Azure Resource Manager-sablon a virtuális géphez.
    - Ha az érvényesítés rendelkezik, válassza ki a **OK**. Virtuális gép üzembe helyezése néhány percet vesz igénybe.

### <a name="specify-the-open-ports-and-dns-name"></a>Adja meg a nyitott portok és a DNS-név

Ahhoz, hogy a webes alkalmazás a felhasználók a hálózat, nyissa meg a gép csatlakozhat, és adja hozzá például a DNS egy rövid nevet használt portokon keresztüli *mywebapp.local.cloudapp.azurestack.external*, hogy a felhasználók használhatnak-e a webböngésző .

#### <a name="open-inbound-ports"></a>Bejövő portok megnyitása

Egy előre definiált szolgáltatást, például az RDP vagy ssh-t, a cél protokoll és -porttartományát módosítása, vagy megadhat egy egyéni porttartományt. Például érdemes együttműködni a porttartomány a webes keretrendszer. GO-t, például a 3000 porton kommunikál.

1. Nyissa meg az Azure Stack portálon a bérlő számára.

1. Keresse meg a virtuális gép. Akkor lehet, hogy rendelkezik a virtuális gép az irányítópulton rögzítve, vagy Ön is megkeresheti azt az a **erőforrások keresése** mezőbe.

1. Válassza ki **hálózatkezelés** a virtuális gép ablaktáblán.

1. Válassza ki **vegye fel a bejövő portot** szabályt, amely megnyit egy portot.

1. A **forrás**, hagyja az alapértelmezett **bármely**.

1. A **forrás porttartomány**, hagyja meg a helyettesítő karakter (*).

1. A **Célporttartomány**, adja meg a portot, amelyet szeretne megnyitni, mint például **3000**.

1. A **protokoll**, hagyja az alapértelmezett **bármely**.

1. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.

1. A **prioritású**, hagyja meg az alapértelmezett.

1. Adjon meg egy **neve** és **leírás** segítséget ne feledje, hogy miért a port nyitva-e.

1. Válassza a **Hozzáadás** lehetőséget.

#### <a name="add-a-dns-name-for-your-server"></a>A kiszolgáló DNS-név hozzáadása

A kiszolgáló egy DNS-név emellett úgy, hogy a felhasználók kapcsolódhatnak a webhely URL-cím használatával hozhat létre.

1. Nyissa meg az Azure Stack portálon a bérlő számára.

1. Keresse meg a virtuális gép. Akkor lehet, hogy rendelkezik a virtuális gép az irányítópulton rögzítve, vagy Ön is megkeresheti azt az a **erőforrások keresése** mezőbe.

1. Válassza az **Áttekintés** lehetőséget.

1. A **VM**válassza **konfigurálása**.

1. A **hozzárendelés**válassza **dinamikus**.

1. Adja meg például a DNS-névcímke **mywebapp**, így a teljes URL-cím lesz *mywebapp.local.cloudapp.azurestack.external* (az alkalmazás ASDK).

### <a name="connect-via-ssh-to-update-your-vm"></a>Csatlakozás ssh-KAPCSOLATOT a virtuális gép frissítése

1. Ugyanazon a hálózaton az Azure Stack-példány nyissa meg az SSH-ügyfél. További információkért lásd: [nyilvános SSH-kulcsot használ](azure-stack-dev-start-howto-ssh-public-key.md).

1. A következő parancsokat:

    ```bash  
        sudo apt-get update
        sudo apt-get -y upgrade
    ```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [állítsa be a fejlesztési környezetet az Azure Stackben](azure-stack-dev-start.md).
