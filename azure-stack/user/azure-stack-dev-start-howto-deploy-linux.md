---
title: Linux rendszerű virtuális gép üzembe helyezése Azure Stack hubhoz
description: Alkalmazás üzembe helyezése Azure Stack hubhoz.
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 63fffbf1a9dcc5048286f93b18b20eb72174b8cd
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "77704199"
---
# <a name="deploy-a-linux-vm-to-host-a-web-app-in-azure-stack-hub"></a>Linux rendszerű virtuális gép üzembe helyezése Azure Stack hub webalkalmazásának üzemeltetéséhez

Létrehozhat és üzembe helyezhet egy alapszintű linuxos virtuális gépet (VM) az Azure Marketplace-en található Ubuntu-rendszerkép használatával, amely egy webes keretrendszerrel létrehozott webalkalmazást üzemeltet. 

A virtuális gép a következő használatával tud webalkalmazásokat üzemeltetni:

- **Python**: a közös Python webes keretrendszerek közé tartozik a lombik, a palack és a Django.
- **Go**: a Common go keretrendszerek közé tartozik a mulatság, a Martini, a Gocraft/web és a Gorilla. 
- **Ruby**: a Ruby on Rails beállítása keretrendszerként a Ruby-webalkalmazások kézbesítéséhez. 
- **Java**: a Java használatával olyan webalkalmazásokat fejleszthet, amelyeket egy Apache Tomcat-kiszolgálóra küld. A Tomcat telepíthető Linux rendszeren, majd a Java WAR-fájljait közvetlenül a kiszolgálóra helyezheti üzembe. 

A cikk utasításait követve megkezdheti a Linux operációs rendszert használó webalkalmazások, keretrendszerek és háttérrendszer-technológiák használatát. Ezután a Azure Stack hub használatával kezelheti az infrastruktúrát, és a technológián belüli felügyeleti eszközök segítségével kezelheti az alkalmazás karbantartási feladatait.

## <a name="deploy-a-linux-vm-for-a-web-app"></a>Linux rendszerű virtuális gép üzembe helyezése egy webalkalmazáshoz

Ebben a folyamatban egy titkos kulcsot hoz létre, a linuxos virtuális gép alapképét használva adja meg a virtuális gép adott attribútumait, majd hozza létre a virtuális gépet. A virtuális gép létrehozása után megnyithatja a virtuális géppel való munkához szükséges portokat, valamint a virtuális gép számára az alkalmazás üzemeltetését. Ezután hozza létre a DNS-nevet. Végezetül pedig a virtuális géphez csatlakozik, és az apt-get segédprogram használatával frissítse a gépet. A folyamat elvégzése után egy virtuális gép fog rendelkezni a Azure Stack hub-példányon, amely készen áll a webalkalmazás üzemeltetésére.

Mielőtt elkezdené, ellenőrizze, hogy van-e minden, amire szüksége van.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Stack hub-előfizetés, amely hozzáférést biztosít az Ubuntu Server 16,04 LTS-rendszerképhez. Használhatja a rendszerkép újabb verzióját, de ezeket az utasításokat a 16,04 LTS-nek kell megírnia. Ha nem rendelkezik ezzel a képpel, forduljon a felhőalapú kezelőhöz, és kérje le a rendszerképet a Azure Stack hub piactérre.

## <a name="deploy-the-vm-by-using-the-portal"></a>A virtuális gép üzembe helyezése a portál használatával

A virtuális gép üzembe helyezéséhez kövesse a következő néhány szakaszban található utasításokat.

### <a name="create-your-vm"></a>A virtuális gép létrehozása

1. Hozzon létre egy Secure Shell (SSH) nyilvános kulcsot a kiszolgáló számára. További információkért lásd: [SSH nyilvános kulcs használata](azure-stack-dev-start-howto-ssh-public-key.md).
1. Az Azure stack hub portálon válassza az **erőforrás** > létrehozása**számítási** > **Ubuntu Server 16,04 LTS**elemet.

    ![Webalkalmazás üzembe helyezése Azure Stack hub-beli virtuális gépen](media/azure-stack-dev-start-howto-deploy-linux/001-portal-compute.png)

4. A **virtuális gép létrehozása** panelen **1. Alapszintű beállítások konfigurálása**:

    a. Adja meg a **virtuális gép nevét**.

    b. Válassza ki a **virtuális gép lemezének típusát** **prémium SSD** (prémium szintű lemezek esetében [SSD]) vagy **standard HDD** (standard lemezekhez [HDD]).

    c. Adja meg a **felhasználónevét**.

    d. Válassza ki a **hitelesítési típust** **nyilvános SSH-kulcsként**.

    e. Kérje le a létrehozott nyilvános SSH-kulcsot. Nyissa meg egy szövegszerkesztőben, másolja a kulcsot, majd illessze be az **SSH nyilvános kulcs** mezőbe. Adja meg `---- END SSH2 PUBLIC KEY ----`a szöveget `---- BEGIN SSH2 PUBLIC KEY ----` a következőre:. Illessze be a teljes blokkot a mezőbe:

    ```text  
    ---- BEGIN SSH2 PUBLIC KEY ----
    Comment: "rsa-key-20190207"
    <Your key block>
    ---- END SSH2 PUBLIC KEY ----
    ```

    f. Válassza ki az Azure Stack hub-példány előfizetését.

    g. Hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt, attól függően, hogyan szeretné rendezni az alkalmazás erőforrásait.

    h. Válassza ki a tartózkodási helyét. A Azure Stack Development Kit (ASDK) általában egy *helyi* régióban található. A hely a Azure Stack hub-példánytól függ.
1. **2. Méret**, írja be a következőt:
    - Válassza ki az Azure Stack hub-példányban elérhető virtuális gép adatmennyiségét és a RAM-ot.
    - Böngészheti a listát, vagy szűrheti a virtuális gép méretét a **számítási típus**, a **processzorok**és a **tárolóhely**alapján.
    
    > [!NOTE]
    > - A bemutatott díjak a helyi pénznemben számított becslések. Csak az Azure-infrastruktúra költségeit és az előfizetés és a hely kedvezményeit tartalmazzák. Nem tartoznak bele a szoftverre vonatkozó költségek. 
    > - Az ajánlott méreteket a kiválasztott rendszerkép közzétevője határozza meg, és a hardverre és a szoftverre vonatkozó követelmények alapján történik.
    > - A standard lemezek (HDD-k) prémium szintű lemezekkel (SSD) való használata hatással lehet az operációs rendszer teljesítményére.

1. **3. **Adja meg a választható szolgáltatásokat, írja be a következőt:

    a. A **magas rendelkezésre állás** érdekében válassza ki a rendelkezésre állási készletet. Ha redundanciát szeretne biztosítani az alkalmazás számára, a két vagy több virtuális gépet egy rendelkezésre állási csoportban csoportosítva adja meg. Ez a konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető legyen, és teljesítse a 99,95%-os Azure-beli szolgáltatói szerződést (SLA). A virtuális gép rendelkezésre állási csoportja nem módosítható a létrehozása után.

    b. A **Storage**esetében válassza a **prémium szintű lemezek (SSD)** vagy a **standard lemez (HDD)** lehetőséget. A prémium szintű lemezeket (SSD-ket) stabil állapotú meghajtók végzik, és konzisztens, kis késleltetésű teljesítményt nyújtanak. A legjobb egyensúlyt biztosítják az árak és a teljesítmény között, és ideálisak a nagy I/O-igényű alkalmazások és a termelési feladatok számára. A standard szintű lemezeket a mágneses meghajtók végzik, és az olyan alkalmazások esetében előnyösek, amelyekben az adatelérés nem gyakori. A Zone-redundáns lemezeket a zóna-redundáns tárolás (ZRS) támogatja, amely több zónában replikálja az adatait, és akkor is elérhető, ha egy zóna nem működik. 

    c. Válassza a **felügyelt lemezek használata**lehetőséget. Ha engedélyezi ezt a funkciót, az Azure automatikusan kezeli a lemezek rendelkezésre állását. Az adatredundancia és a hibatűrés előnyeit a Storage-fiókok saját maga általi létrehozása és kezelése nélkül veheti igénybe. Előfordulhat, hogy a felügyelt lemezek nem érhetők el minden régióban. További információ: [Bevezetés az Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)szolgáltatásba.

    d. A hálózat konfigurálásához válassza a **virtuális hálózat**lehetőséget. A virtuális hálózatok logikailag el vannak különítve egymástól az Azure-ban. Az IP-címtartományok, az alhálózatok, az útválasztási táblák, az átjárók és a biztonsági beállítások ugyanúgy konfigurálhatók, mint az adatközpont hagyományos hálózata. Az azonos virtuális hálózatban lévő virtuális gépek alapértelmezés szerint hozzáférhetnek egymáshoz. 

    e. Az alhálózat konfigurálásához válassza az **alhálózat**lehetőséget. Az alhálózat a virtuális hálózatban található IP-címek tartománya. Az alhálózatok segítségével elkülönítheti a virtuális gépeket egymástól vagy az internettől. 

    f. A virtuális géphez vagy a virtuális gépen futó szolgáltatásokhoz való hozzáférés konfigurálásához válassza a **nyilvános IP-cím**lehetőséget. Nyilvános IP-cím használatával kommunikálhat a virtuális géppel a virtuális hálózaton kívülről. 

    g. Válassza a **hálózati biztonsági csoport**, **alapszintű**vagy **speciális**lehetőséget. Olyan szabályok beállítása, amelyek engedélyezik vagy megtagadják a virtuális gép felé irányuló hálózati forgalmat. 

    h. Ha az általános vagy egyéni protokollok hozzáférését szeretné beállítani a virtuális géphez, válassza a **nyilvános bejövő portok**lehetőséget. A szolgáltatás megadja a szabály céljának protokollját és a porttartomány tartományát. Kiválaszthat egy előre definiált szolgáltatást, például RDP protokoll (RDP) vagy SSH-t, vagy egyéni porttartomány is megadhat. 
        A webkiszolgálón nyissa meg a HTTP (80), a HTTPS (443) és az SSH (22) használatát. Ha a gép RDP-kapcsolaton keresztüli felügyeletét tervezi, nyissa meg a 3389-es portot.

    i. Ha bővítményeket szeretne hozzáadni a virtuális géphez, válassza a **bővítmények**lehetőséget. A bővítmények új szolgáltatásokat, például konfiguráció-felügyeleti vagy vírusvédelmi védelmet biztosítanak a virtuális géphez. 

    j. A **figyelés**letiltása vagy engedélyezése. Az indítási problémák diagnosztizálásához a figyelés használatával rögzítheti egy gazdagépen futó virtuális gép soros konzoljának kimenetét és képernyőképeit. 

    k. A mérőszámokat tároló Storage-fiók megadásához válassza a **diagnosztika Storage-fiók**lehetőséget. A metrikák egy Storage-fiókba íródnak, így a saját eszközeivel is elemezheti őket. 

    l. Kattintson az **OK** gombra.

1. Tekintse át a **4. Összefoglalás**:
    - A portál ellenőrzi a beállításokat.
    - Ha Azure Resource Manager munkafolyamattal szeretné felhasználni a beállításokat, letöltheti a virtuális gép Azure Resource Manager sablonját.
    - Az ellenőrzés eltelte után kattintson **az OK gombra**. A virtuális gép üzembe helyezése néhány percet vesz igénybe.

### <a name="specify-the-open-ports-and-dns-name"></a>A nyitott portok és a DNS-név megadása

Annak érdekében, hogy a webalkalmazás elérhető legyen a hálózaton lévő felhasználók számára, nyissa meg a számítógéphez való csatlakozáshoz használt portokat, és adjon hozzá egy felhasználóbarát DNS-nevet, például *mywebapp. local. cloudapp. azurestack. external*, amelyet a felhasználók használhatnak a böngészőben.

#### <a name="open-inbound-ports"></a>Bejövő portok megnyitása

Egy előre definiált szolgáltatás (például RDP vagy SSH) esetében módosíthatja a célként megadott protokollt és a porttartomány, vagy egyéni porttartomány is megadható. Előfordulhat például, hogy a webes keretrendszer porttartomány alapján szeretne dolgozni. LÉPJEN kapcsolatba például a 3000-es porton.

1. Nyissa meg a Azure Stack hub portált a bérlőhöz.

1. Keresse meg a virtuális gépet. Lehet, hogy rögzítette a virtuális gépet az irányítópulton, vagy megkeresi az **erőforrások keresése** mezőben.

1. Válassza a **hálózatkezelés** lehetőséget a virtuális gép ablaktáblán.

1. Válassza a **bejövő Portszabály hozzáadása** lehetőséget a port megnyitásához.

1. A **Source (forrás**) beállításnál hagyja meg az alapértelmezett **beállítást.**

1. A **forrás porttartomány**mezőben hagyja meg a helyettesítő karaktert (*).

1. A **célport tartománya**mezőben adja meg a megnyitni kívánt portot, például **3000**.

1. A **protokoll**beállításnál hagyja meg az alapértelmezett **beállítást.**

1. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.

1. A **prioritás**beállításnál hagyja meg az alapértelmezett beállítást.

1. Adjon meg egy **nevet** és egy **leírást** , amely segít megjegyeznünk, hogy a port miért van nyitva.

1. Válassza a **Hozzáadás** lehetőséget.

#### <a name="add-a-dns-name-for-your-server"></a>DNS-név hozzáadása a kiszolgálóhoz

Emellett létrehozhat egy DNS-nevet is a kiszolgálóhoz, így a felhasználók URL-cím használatával csatlakozhatnak a webhelyhez.

1. Nyissa meg a Azure Stack hub portált a bérlőhöz.

1. Keresse meg a virtuális gépet. Lehet, hogy rögzítette a virtuális gépet az irányítópulton, vagy megkeresi az **erőforrások keresése** mezőben.

1. Válassza az **Áttekintés** lehetőséget.

1. A **virtuális gép**területen válassza a **Konfigurálás**lehetőséget.

1. A **hozzárendelés**beállításnál válassza a **dinamikus**lehetőséget.

1. Adja meg a DNS-név címkéjét (például **mywebapp**), így a teljes URL-cím *mywebapp. local. cloudapp. azurestack. external* lesz (ASDK-alkalmazás esetén).

### <a name="connect-via-ssh-to-update-your-vm"></a>Kapcsolódás SSH-n keresztül a virtuális gép frissítéséhez

1. Az Azure Stack hub-példánnyal azonos hálózaton nyissa meg az SSH-ügyfelet. További információ: [nyilvános SSH-kulcs használata](azure-stack-dev-start-howto-ssh-public-key.md).

1. Adja meg a következő parancsokat:

    ```bash  
        sudo apt-get update
        sudo apt-get -y upgrade
    ```

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [állíthatja be a fejlesztési környezetet az Azure stack hub-ban](azure-stack-dev-start.md).
