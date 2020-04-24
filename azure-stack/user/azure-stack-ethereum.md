---
title: Ethereum blockchain-hálózat üzembe helyezése Azure Stack hub-on
description: Oktatóanyag egyéni megoldási sablonok használatával a konzorcium Ethereum blockchain-hálózatának üzembe helyezéséhez és konfigurálásához Azure Stack hub-on.
author: PatAltimore
ms.author: patricka
ms.date: 06/03/2019
ms.topic: tutorial
ms.reviewer: seyadava
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: a0a13af01fc539788d76377f357ca28db9b05234
ms.sourcegitcommit: bdd4d529bd3e115a9f76eece62b1613448d5d020
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "77703077"
---
# <a name="deploy-an-ethereum-blockchain-network-on-azure-stack-hub"></a>Ethereum blockchain-hálózat üzembe helyezése Azure Stack hub-on

A Ethereum-megoldás sablonja úgy lett kialakítva, hogy megkönnyítse és meggyorsítsa a több tagú konzorcium Ethereum blockchain-hálózat üzembe helyezését és konfigurálását minimális Azure-és Ethereum-ismeretekkel.

Egy kis felhasználói bemenettel és egy kattintással történő üzembe helyezéssel az Azure Stack hub-bérlői portálon keresztül minden tag kiépítheti hálózati lábnyomát. Az egyes tagok hálózati lábnyoma három dolgot tartalmaz:

1. Elosztott terhelésű tranzakciós csomópontok készlete, amelyekkel az alkalmazás vagy a felhasználó műveleteket végezhet a tranzakciók elküldéséhez.
2. A tranzakciókat rögzítő adatbányászati csomópontok halmaza.
3. Egy hálózati virtuális berendezés (NVA).

Egy újabb kapcsolódási lépés összekapcsolja a NVA egy teljesen konfigurált többtagú blockchain-hálózat létrehozásához.

A beállításhoz:

- Válasszon egy telepítési architektúrát.
- Önálló, konzorciumi vezető vagy konzorciumi tagkiszolgáló üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

Töltse le a legújabb elemeket [a piactérről](../operator/azure-stack-download-azure-marketplace-item.md):

- Ubuntu Server 16,04 LTS
- Windows Server 2016
- Egyéni parancsfájl a Linux 2,0-hez
- Egyéniszkript-bővítmény Windows rendszerre

A blockchain forgatókönyvekkel kapcsolatos további információkért lásd: [Ethereum-szolgáltatói konzorcium megoldási sablonja](/azure/blockchain/templates/ethereum-poa-deployment).

## <a name="deployment-architecture"></a>Üzembe helyezési architektúra

Ez a megoldási sablon telepíthet egyetlen vagy több tagú Ethereum Consortium-hálózatot. A virtuális hálózat egy hálózati virtuális berendezést és kapcsolati erőforrásokat használó láncbeli topológiában van csatlakoztatva.

## <a name="deployment-use-cases"></a>Üzembe helyezési használati esetek

A sablon számos módon képes üzembe helyezni a Ethereum consortiumt a vezető és a tag számára. Itt láthatók a teszteltek:

- Egy több csomópontos Azure Stack hub esetében, az Azure AD-vel vagy a AD FS-vel, az érdeklődőket és a tagokat ugyanazon előfizetés vagy eltérő előfizetések használatával helyezheti üzembe.
- Egy csomópontos Azure Stack hubhoz (az Azure AD-vel) telepítse az érdeklődőt és a tagot ugyanazzal az előfizetéssel.

### <a name="standalone-and-consortium-leader-deployment"></a>Önálló és konzorciumi vezető üzembe helyezés

A Consortium Leader-sablon konfigurálja az első tag hálózati lábnyomát. 

1. Töltse le a [Leader-sablont a githubról](https://aka.ms/aa6z619).
2. Az Azure Stack hub-bérlői portálon válassza az **+ erőforrás létrehozása > template Deployment** lehetőséget az egyéni sablonból való üzembe helyezéshez.
3. Válassza a **Sablon szerkesztése** lehetőséget az új egyéni sablon szerkesztéséhez.
4. A jobb oldali szerkesztési ablaktáblán másolja és illessze be a korábban letöltött vezető sablon JSON-fájlját.
    
    ![Vezető sablon szerkesztése](./media/azure-stack-ethereum/edit-leader-template.png)

5. Kattintson a **Mentés** gombra.
6. Válassza a **Paraméterek szerkesztése** lehetőséget, és fejezze be az üzemelő példányhoz tartozó sablon paramétereit.
    
    ![A Leader-sablon paramétereinek szerkesztése](./media/azure-stack-ethereum/edit-leader-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | A telepített erőforrások elnevezésének alapjául szolgáló sztring. | Alfanumerikus karakterek, 1 és 6 közötti hosszúságú. | ETH
    AUTHTYPE | A virtuális géphez való hitelesítés módszere. | Jelszó vagy nyilvános SSH-kulcs. | Jelszó
    ADMINUSERNAME | Az egyes telepített virtuális gépek rendszergazdai felhasználóneve. | 1-64 karakter. | gethadmin
    ADMINPASSWORD (hitelesítés típusa = jelszó)| Az egyes üzembe helyezett virtuális gépek rendszergazdai fiókjának jelszava. A jelszónak tartalmaznia kell a következő követelményeket: 1 nagybetűs karakter, 1 kisbetűs karakter, 1 szám és 1 speciális karakter. <br />Habár a virtuális gépek kezdetben ugyanazzal a jelszóval rendelkeznek, a kiépítés után megváltoztathatja a jelszót.|12-72 karakter. |
    ADMINSSHKEY (hitelesítés típusa = sshPublicKey) | A távoli bejelentkezéshez használt Secure Shell-kulcs. | |
    GENESISBLOCK | Egyéni Genesis-blokkot jelölő JSON-karakterlánc.  A paraméter értékének megadása nem kötelező. | |
    ETHEREUMACCOUNTPSSWD | A Ethereum-fiók biztonságossá tételéhez használt rendszergazdai jelszó. | |
    ETHEREUMACCOUNTPASSPHRASE | A Ethereum-fiókhoz társított titkos kulcs létrehozásához használt jelszó. | |
    ETHEREUMNETWORKID | A konzorcium hálózati azonosítója. | Tetszőleges értéket használhat 5 és 999 999 999 között. | 72
    CONSORTIUMMEMBERID | A konzorcium-hálózat egyes tagjaihoz tartozó azonosító.   | Ennek az AZONOSÍTÓnak egyedinek kell lennie a hálózaton. | 0
    NUMMININGNODES | A bányászati csomópontok száma. | 2 és 15 között. | 2
    MNNODEVMSIZE | A bányászati csomópontok virtuálisgép-mérete. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | A bányászati csomópontok tárolási teljesítménye. | | Standard_LRS
    NUMTXNODES | A tranzakciós csomópontok száma. | 1 és 5 között. | 1
    TXNODEVMSIZE | A tranzakciós csomópontok virtuálisgép-mérete. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | A tranzakciós csomópontok tárolási teljesítménye. | | Standard_LRS
    BASEURL | Az alap URL-cím, amelyből beolvashatók a központi telepítési sablonok. | Ha testre szeretné szabni a központi telepítési sablonokat, használja az alapértelmezett értéket. | 

7. Kattintson az **OK** gombra.
8. Az **Egyéni telepítés**területen válassza az **előfizetés**, az **erőforráscsoport**és az **erőforráscsoport helyét**.
    
    ![Vezető telepítési paraméterek](./media/azure-stack-ethereum/leader-deployment-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    Előfizetés | Az előfizetés, amelyre a konzorcium-hálózatot telepíteni kell. | | Fogyasztási előfizetés
    Erőforráscsoport | Az az erőforráscsoport, amelyre a konzorcium-hálózatot telepíteni kell. | | EthereumResources
    Hely | Az erőforráscsoport Azure-régiója. | | helyi

8. Kattintson a **Létrehozás** gombra.

Az üzembe helyezés akár 20 percet vagy hosszabb időt is igénybe vehet.

Az üzembe helyezés befejezése után tekintse át a **Microsoft. template** központi telepítési összegzését az erőforráscsoport telepítési szakaszában. Az összefoglalás a konzorcium tagjaihoz való csatlakozáshoz használt kimeneti értékeket tartalmazza.

A Leader üzembe helyezésének ellenőrzéséhez lépjen a vezető rendszergazdai webhelyére. A felügyeleti hely címe a **Microsoft. template** telepítésének output (kimenet) szakaszában található.  

![A Leader üzembe helyezésének összefoglalása](./media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>A konzorciumi tagok központi telepítésének csatlakoztatása

1. Töltse le a [konzorciumi tag sablonját a githubról](https://aka.ms/aa6zkua).
2. Az Azure Stack hub-bérlői portálon válassza az **+ erőforrás létrehozása > template Deployment** lehetőséget az egyéni sablonból való üzembe helyezéshez.
3. Válassza a **Sablon szerkesztése** lehetőséget az új egyéni sablon szerkesztéséhez.
4. A jobb oldali szerkesztési ablaktáblán másolja és illessze be a korábban letöltött vezető sablon JSON-fájlját.
5. Kattintson a **Mentés** gombra.
6. Válassza a **Paraméterek szerkesztése** lehetőséget, és fejezze be az üzemelő példányhoz tartozó sablon paramétereit.

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | A telepített erőforrások elnevezésének alapjául szolgáló sztring. | Alfanumerikus karakterek, 1 és 6 közötti hosszúságú. | ETH
    AUTHTYPE | A virtuális géphez való hitelesítés módszere | Jelszó vagy nyilvános SSH-kulcs. | Jelszó
    ADMINUSERNAME | Az egyes telepített virtuális gépek rendszergazdai felhasználóneve | 1-64 karakter. | gethadmin
    ADMINPASSWORD (hitelesítés típusa = jelszó)| Az egyes üzembe helyezett virtuális gépek rendszergazdai fiókjának jelszava. A jelszónak tartalmaznia kell a következő követelményeket: 1 nagybetűs karakter, 1 kisbetűs karakter, 1 szám és 1 speciális karakter. <br />Habár a virtuális gépek kezdetben ugyanazzal a jelszóval rendelkeznek, a kiépítés után megváltoztathatja a jelszót.|12-72 karakter. |
    ADMINSSHKEY (hitelesítés típusa = sshPublicKey) | A távoli bejelentkezéshez használt Secure Shell-kulcs. | |
    CONSORTIUMMEMBERID | A konzorcium-hálózat egyes tagjaihoz tartozó azonosító.   | Ennek az AZONOSÍTÓnak egyedinek kell lennie a hálózaton. | 0
    NUMMININGNODES | A bányászati csomópontok száma. | 2 és 15 között. | 2
    MNNODEVMSIZE | A bányászati csomópontok virtuálisgép-mérete. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | A bányászati csomópontok tárolási teljesítménye. | | Standard_LRS
    NUMTXNODES | A tranzakciós csomópontok száma. | 1 és 5 között. | 1
    TXNODEVMSIZE | A tranzakciós csomópontok virtuálisgép-mérete. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | A tranzakciós csomópontok tárolási teljesítménye. | | Standard_LRS
    CONSORTIUMDATA | Az URL-cím, amely a másik tag üzemelő példánya által biztosított vonatkozó konzorcium-konfigurációs adatokat mutat. Ez az érték a vezető telepítési kimenetén található. | |
    REMOTEMEMBERVNETADDRESSSPACE | A vezető NVA IP-címe. Ez az érték a vezető telepítési kimenetén található. | | 
    REMOTEMEMBERNVAPUBLICIP | A vezető NVA IP-címe. Ez az érték a vezető telepítési kimenetén található. | | 
    CONNECTIONSHAREDKEY | A konzorciumi hálózat azon tagjai között, amelyek kapcsolatot hoznak létre, előre elkészített titok. | |
    BASEURL | A sablon alap URL-címe. | Ha testre szeretné szabni a központi telepítési sablonokat, használja az alapértelmezett értéket. | 

7. Kattintson az **OK** gombra.
8. Az **Egyéni telepítés**területen válassza az **előfizetés**, az **erőforráscsoport**és az **erőforráscsoport helyét**.

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    Előfizetés | Az előfizetés, amelyre a konzorcium-hálózatot telepíteni kell. | | Fogyasztási előfizetés
    Erőforráscsoport | Az az erőforráscsoport, amelyre a konzorcium-hálózatot telepíteni kell. | | MemberResources
    Hely | Az erőforráscsoport Azure-régiója. | | helyi

8. Kattintson a **Létrehozás** gombra.

Az üzembe helyezés akár 20 percet vagy hosszabb időt is igénybe vehet.

Az üzembe helyezés befejezése után tekintse át a **Microsoft. template** központi telepítési összegzését az erőforráscsoport telepítési szakaszában. Az összefoglalás a konzorcium tagjaihoz való kapcsolódáshoz használt kimeneti értékeket tartalmazza.

A tag üzembe helyezésének ellenőrzéséhez tallózzon a tag felügyeleti webhelyén. A felügyeleti hely címe a **Microsoft. template** telepítésének kimenet szakaszában található.

![Tag központi telepítésének összegzése](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Ahogy a képen is látható, a tag csomópontjainak állapota **nem fut**. Ez az állapot azért van, mert a tag és a vezető közötti kapcsolat nincs létrehozva. A tag és a vezető közötti kapcsolat kétirányú kapcsolat. A tag központi telepítésekor a sablon automatikusan létrehozza a csatlakozást a tag és a vezető között. A vezető és a tag közötti kapcsolatok létrehozásához lépjen a következő lépésre.

### <a name="connect-member-and-leader"></a>A tag és a vezető összekötése

Ez a sablon létrehoz egy, a vezető és egy távoli tag közötti kapcsolatokat. 

1. Töltse le a [csatlakozási tag és a Leader-sablonját a githubról](https://aka.ms/aa6zdyt).
2. Az Azure Stack hub-bérlői portálon válassza az **+ erőforrás létrehozása > template Deployment** lehetőséget az egyéni sablonból való üzembe helyezéshez.
3. Válassza a **Sablon szerkesztése** lehetőséget az új egyéni sablon szerkesztéséhez.
4. A jobb oldali szerkesztési ablaktáblán másolja és illessze be a korábban letöltött vezető sablon JSON-fájlját.
    
    ![Csatlakozási sablon szerkesztése](./media/azure-stack-ethereum/edit-connect-template.png)

5. Kattintson a **Mentés** gombra.
6. Válassza a **Paraméterek szerkesztése** lehetőséget, és fejezze be az üzemelő példányhoz tartozó sablon paramétereit.
    
    ![Csatlakozási sablon paramétereinek szerkesztése](./media/azure-stack-ethereum/edit-connect-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | A vezető nevének előtagja. Ez az érték a vezető telepítési kimenetén található.  | Alfanumerikus karakterek, 1 és 6 közötti hosszúságú. | |
    MEMBERROUTETABLENAME | A vezető útválasztási táblázatának neve. Ez az érték a vezető telepítési kimenetén található. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | A tag címterület. Ez az érték a tag telepítési kimenetén található. | |
    CONNECTIONSHAREDKEY | A konzorciumi hálózat azon tagjai között, amelyek kapcsolatot hoznak létre, előre elkészített titok.  | |
    REMOTEMEMBERNVAPUBLICIP | A tag NVA IP-címe. Ez az érték a tag telepítési kimenetén található. | |
    MEMBERNVAPRIVATEIP | A vezető magánhálózati NVA IP-címe. Ez az érték a vezető telepítési kimenetén található. | |
    HELY | Az Azure Stack hub-környezet helye. | | helyi
    BASEURL | A sablon alap URL-címe. | Ha testre szeretné szabni a központi telepítési sablonokat, használja az alapértelmezett értéket. | 

7. Kattintson az **OK** gombra.
8. Az **Egyéni telepítés**területen válassza az **előfizetés**, az **erőforráscsoport**és az **erőforráscsoport helyét**.
    
    ![Központi telepítési paraméterek összekötése](./media/azure-stack-ethereum/connect-deployment-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    Előfizetés | A vezető előfizetése. | | Fogyasztási előfizetés
    Erőforráscsoport | A vezető erőforráscsoport. | | EthereumResources
    Hely | Az erőforráscsoport Azure-régiója. | | helyi

8. Kattintson a **Létrehozás** gombra.

Az üzembe helyezés befejezése után néhány percet vesz igénybe, hogy a vezető és a tag is megkezdje a kommunikációt. A központi telepítés ellenőrzéséhez frissítse a tag felügyeleti webhelyét. A tag csomópontjainak állapotának futnia kell.

![A telepítés ellenőrzése](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>További lépések

További információ a Ethereum és az Azure-ról: [Blockchain Technology and Applications](https://azure.microsoft.com/solutions/blockchain/).
