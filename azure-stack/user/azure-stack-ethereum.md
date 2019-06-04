---
title: Az Azure Stack Ethereum blockchain megoldássablon
description: Az oktatóanyag egy egyéni megoldás-sablonok használatával telepítheti és konfigurálhatja a consortium Ethereum hálózati blockchain az Azure Stackben
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 06/03/2019
ms.topic: tutorial
ms.service: azure-stack
ms.reviewer: seyadava
ms.custom: mvc
manager: femila
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 8f173ad486ed7fc1a4dae3c9a1c57f77ca623f0d
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2019
ms.locfileid: "66459093"
---
# <a name="deploy-an-ethereum-blockchain-network-on-azure-stack"></a>Az Azure Stack Ethereum blockchain hálózatok üzembe helyezése

Az Ethereum-megoldássablon célja, hogy egyszerűbb és gyorsabb üzembe helyezése és konfigurálása egy többtagú consortium Ethereum blockchain hálózati minimális Azure-ban és az Ethereum ismeretekkel.

Felhasználói adatok és a egy kattintással üzembe helyezést, az Azure Stack-bérlői portál néhány minden tagja a hálózati erőforrás-igényű helyezhet üzembe. Minden tag hálózati erőforrás-igényű tranzakciós kiegyenlített terhelésű csomópontok egy készletét áll rendelkező, amelyeket egy alkalmazás vagy felhasználó használhatja elküldeni a tranzakciók, a tranzakciókat. adatbányászati csomópontok egy készletét és a egy hálózati virtuális készüléket (NVA). A későbbi csatlakozási lépés kapcsolódik az nva-k teljes konfigurációjú többtagú blockchain-hálózat létrehozása.

Beállítása:

- Válasszon egy üzembe helyezési architektúrája
- Egy önálló, consortium vezető vagy consortium tag hálózati üzembe helyezése

## <a name="prerequisites"></a>Előfeltételek

Töltse le a legújabb elemeket [a Marketplace-ről](../operator/azure-stack-download-azure-marketplace-item.md):

- Ubuntu Server 16.04 LTS
- Windows Server 2016
- Egyéni parancsfájl 2.0 linuxhoz
- A Windows egyéni szkriptek futtatására szolgáló bővítmény

Blockchain-forgatókönyvekkel kapcsolatos további információkért lásd: [Ethereum koncepció jogosultság consortium megoldássablon](/azure/blockchain/templates/ethereum-poa-deployment).

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

Ez a megoldássablon telepíthet egy vagy több rétegből tag Ethereum consortium network. A virtuális hálózathoz van csatlakoztatva, egy hálózati virtuális készüléket és a kapcsolati erőforrás lánc topológiában. 

## <a name="deployment-use-cases"></a>Üzembe helyezés alkalmazási helyzetek

A sablon Ethereum consortium vezető és tag illesztés, számos módon telepítheti, Íme az általunk tesztelt azokat:

- A több csomópontos Azure Stack az Azure AD vagy az AD FS-ben és üzembe helyezése érdeklődő tag gyermekeihez ugyanabba az előfizetésbe, vagy eltérő előfizetésekben.
- Egy egycsomópontos Azure Stack (az Azure AD), az érdeklődő és a tag gyermekeihez ugyanabban az előfizetésben üzembe helyezése.

### <a name="standalone-and-consortium-leader-deployment"></a>Önálló és consortium vezető üzembe helyezés

A consortium vezető sablon az első tagtól erőforrás-igényű konfigurálja a hálózatban. 

1. Töltse le a [vezető sablont a Githubból](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json)
2. Válassza ki az Azure Stack-bérlői portálon **+ erőforrás létrehozása > sablonalapú telepítés** egy egyéni sablon üzembe helyezéséhez.
3. Válassza ki **szerkesztési sablon** szerkesztése az új egyéni sablont.
4. A szerkesztési ablaktáblán a jobb oldali másolja és illessze be a vezető sablon korábban letöltött JSON.
    
    ![Vezető sablon szerkesztése](./media/azure-stack-ethereum/edit-leader-template.png)

5. Kattintson a **Mentés** gombra.
6. Válassza ki **paraméterek szerkesztése** fejezze be a sablon paramétereit az üzembe helyezéshez.
    
    ![Vezető sablon paraméterek szerkesztése](./media/azure-stack-ethereum/edit-leader-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | A telepített erőforrások elnevezési alapként használt karakterlánc. | Alfanumerikus karakterek és a hossza 1 – 6 | ETH
    AUTHTYPE | A módszert a virtuális géphez. | Jelszó vagy SSH nyilvános kulcs | Jelszó
    ADMINUSERNAME | Minden üzembe helyezett virtuális gép rendszergazdai felhasználóneve | 1 – 64 karakter | gethadmin
    ADMINPASSWORD (hitelesítési típus = jelszó)| Az egyes üzembe helyezett virtuális gépek a rendszergazdai fiók jelszava. A jelszónak tartalmaznia kell a 3 az alábbi követelményeket: 1 nagybetűt, 1 kisbetűt, 1 szám és 1 különleges karakter. <br />Minden virtuális gép kezdetben van ugyanazt a jelszót, üzembe helyezés után módosíthatja a jelszót.|12 – 72 karakter|
    ADMINSSHKEY (hitelesítési típus = sshPublicKey) | A secure shell-kulcsot a távoli bejelentkezéshez használt. | |
    GENESISBLOCK | Egyéni képződés blokk jelölő JSON-karakterlánc.  Ez a paraméter értékének megadása nem kötelező. | |
    ETHEREUMACCOUNTPSSWD | A rendszergazdai jelszót, Ethereum-fiókhoz használt. | |
    ETHEREUMACCOUNTPASSPHRASE | Az Ethereum-fiókjához társított titkos kulcs létrehozásához használt jelszót. | |
    ETHEREUMNETWORKID | A consortium network azonosítója. | 5 és 999,999,999 között bármilyen érték | 72
    CONSORTIUMMEMBERID | Minden egyes tagja a consortium network társított azonosítója.   | Ezt az Azonosítót a hálózatban egyedinek kell lennie. | 0
    NUMMININGNODES | Adatbányászati csomópontok száma. | 2. és 15 között. | 2
    MNNODEVMSIZE | Az adatbányászati csomópontok Virtuálisgép-méretet. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Tárolási teljesítmény adatbányászati csomópont. | | Standard_LRS
    NUMTXNODES | Tranzakció-csomópontok száma. | 1 – 5. | 1
    TXNODEVMSIZE | A tranzakció csomópontok Virtuálisgép-méretet. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | A tranzakció csomópontok Storage teljesítményét. | | Standard_LRS
    BASEURL | Alap URL-cím a függően sablon beszerzését. | Használja az alapértelmezett értéket, kivéve, ha testre szeretné szabni a központi telepítési sablonok. | 

7. Kattintson az **OK** gombra.
8. A **egyéni üzembe helyezés**, adja meg **előfizetés**, **erőforráscsoport**, és **erőforráscsoport helye**.
    
    ![Vezető üzembe helyezéshez megadott paraméterek](./media/azure-stack-ethereum/leader-deployment-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    Előfizetés | Az előfizetés, melyben szeretné üzembe helyezni a consortium network | | Használatalapú előfizetés
    Erőforráscsoport | Az erőforráscsoport, melyben szeretné üzembe helyezni a consortium network. | | EthereumResources
    Location egység | Az Azure-régió erőforráscsoport. | | helyi

8. Kattintson a **Létrehozás** gombra.

Üzembe helyezés 20 percet is igénybe vehet, vagy hosszabb.

Üzembe helyezés befejezése után megtekintheti a központi telepítés összegzése **Microsoft. Sablon** az erőforráscsoport üzembe helyezési szakaszában. Az összefoglalás kimeneti értékeket tartalmaz, amelyek segítségével csatlakozzon consortium tagok.

Vezető üzembe helyezés ellenőrzéséhez keresse meg a vezető felügyeleti webhely. Felügyeleti webhely címe találhatja meg a kimeneti szakaszában **Microsoft.Template** központi telepítés.  

![Vezető a központi telepítés összegzése](./media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Csatlakozás consortium tag központi telepítés

1. Töltse le a [consortium tag sablont a Githubból](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json)
2. Válassza ki az Azure Stack-bérlői portálon **+ erőforrás létrehozása > sablonalapú telepítés** egy egyéni sablon üzembe helyezéséhez.
3. Válassza ki **szerkesztési sablon** szerkesztése az új egyéni sablont.
4. A szerkesztési ablaktáblán a jobb oldali másolja és illessze be a vezető sablon korábban letöltött JSON.
5. Kattintson a **Mentés** gombra.
6. Válassza ki **paraméterek szerkesztése** fejezze be a sablon paramétereit az üzembe helyezéshez.

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | A telepített erőforrások elnevezési alapként használt karakterlánc. | Alfanumerikus karakterek és a hossza 1 – 6 | ETH
    AUTHTYPE | A módszert a virtuális géphez. | Jelszó vagy SSH nyilvános kulcs | Jelszó
    ADMINUSERNAME | Minden üzembe helyezett virtuális gép rendszergazdai felhasználóneve | 1 – 64 karakter | gethadmin
    ADMINPASSWORD (hitelesítési típus = jelszó)| Az egyes üzembe helyezett virtuális gépek a rendszergazdai fiók jelszava. A jelszónak tartalmaznia kell a 3 az alábbi követelményeket: 1 nagybetűt, 1 kisbetűt, 1 szám és 1 különleges karakter. <br />Minden virtuális gép kezdetben van ugyanazt a jelszót, üzembe helyezés után módosíthatja a jelszót.|12 – 72 karakter|
    ADMINSSHKEY (hitelesítési típus = sshPublicKey) | A secure shell-kulcsot a távoli bejelentkezéshez használt. | |
    CONSORTIUMMEMBERID | Minden egyes tagja a consortium network társított azonosítója.   | Ezt az Azonosítót a hálózatban egyedinek kell lennie. | 0
    NUMMININGNODES | Adatbányászati csomópontok száma. | 2. és 15 között. | 2
    MNNODEVMSIZE | Az adatbányászati csomópontok Virtuálisgép-méretet. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Tárolási teljesítmény adatbányászati csomópont. | | Standard_LRS
    NUMTXNODES | Tranzakció-csomópontok száma. | 1 – 5. | 1
    TXNODEVMSIZE | A tranzakció csomópontok Virtuálisgép-méretet. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | A tranzakció csomópontok Storage teljesítményét. | | Standard_LRS
    CONSORTIUMDATA | A tag egy másik telepítés által biztosított megfelelő consortium adatok mutató URL-címe. Ez az érték található a vezető telepítési kimenetet. | |
    REMOTEMEMBERVNETADDRESSSPACE | A vezető NVA IP-címét. Ez az érték található a vezető telepítési kimenetet. | | 
    REMOTEMEMBERNVAPUBLICIP | A vezető NVA IP-címét. Ez az érték található a vezető telepítési kimenetet. | | 
    CONNECTIONSHAREDKEY | Egy előre meghatározott titkos kulcsot, amely kapcsolatot létesít a consortium network tagjai között. | |
    BASEURL | Alap URL-címe a sablont. | Használja az alapértelmezett értéket, kivéve, ha testre szeretné szabni a központi telepítési sablonok. | 

7. Kattintson az **OK** gombra.
8. A **egyéni üzembe helyezés**, adja meg **előfizetés**, **erőforráscsoport**, és **erőforráscsoport helye**.

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    Előfizetés | Az előfizetés, melyben szeretné üzembe helyezni a consortium network | | Használatalapú előfizetés
    Erőforráscsoport | Az erőforráscsoport, melyben szeretné üzembe helyezni a consortium network. | | MemberResources
    Location egység | Az Azure-régió erőforráscsoport. | | helyi

8. Kattintson a **Létrehozás** gombra.

Üzembe helyezés 20 percet is igénybe vehet, vagy hosszabb.

Üzembe helyezés befejezése után megtekintheti a központi telepítés összegzése **Microsoft.Template** az erőforráscsoport üzembe helyezési szakaszában. Az összefoglalás consortium tagok connect segítségével kimeneti értékeket tartalmaz.

Tag üzembe helyezés ellenőrzéséhez keresse meg a tag felügyeleti webhely. Felügyeleti webhely címe Microsoft.Template üzembe helyezés a kimeneti szakaszban találhatja.

![Tag központi telepítés összegzése](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Amint a képen is látható, tagok csomópontok állapota **nem fut**. Ez az állapot azért, hogy a tag és vezető közötti kapcsolat nem jön létre. Tag és vezető közötti kapcsolat egy kétirányú kapcsolat. Tag központi telepítésekor a sablon automatikusan létrehozza a vezető tag a kapcsolatot. A kapcsolat létrehozására vezető és tag, nyissa meg a következő lépéssel.

### <a name="connect-member-and-leader"></a>Csatlakozás a tag és vezető

Ez a sablon létrehoz egy kapcsolatot a vezető távoli tagja. 

1. Töltse le a [tag és vezető sablon csatlakozzon a Githubról](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json)
2. Válassza ki az Azure Stack-bérlői portálon **+ erőforrás létrehozása > sablonalapú telepítés** egy egyéni sablon üzembe helyezéséhez.
3. Válassza ki **szerkesztési sablon** szerkesztése az új egyéni sablont.
4. A szerkesztési ablaktáblán a jobb oldali másolja és illessze be a vezető sablon korábban letöltött JSON.
    
    ![Szerkesztés sablon csatlakoztatása](./media/azure-stack-ethereum/edit-connect-template.png)

5. Kattintson a **Mentés** gombra.
6. Válassza ki **paraméterek szerkesztése** fejezze be a sablon paramétereit az üzembe helyezéshez.
    
    ![Szerkesztés csatlakozás sablon paraméterei](./media/azure-stack-ethereum/edit-connect-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Vezető előtagja. Ez az érték található a vezető telepítési kimenetet.  | Alfanumerikus karakterek és a hossza 1 – 6 | |
    MEMBERROUTETABLENAME | A vezető útvonal tábla neve. Ez az érték található a vezető telepítési kimenetet. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | A tag címtér. Ezt az értéket a tag központi telepítési kimenetet található. | |
    CONNECTIONSHAREDKEY | Egy előre meghatározott titkos kulcsot, amely kapcsolatot létesít a consortium network tagjai között.  | |
    REMOTEMEMBERNVAPUBLICIP | A tag NVA IP-címe. Ezt az értéket a tag központi telepítési kimenetet található. | |
    MEMBERNVAPRIVATEIP | Vezető titkos NVA IP-cím. Ez az érték található a vezető telepítési kimenetet. | |
    HELY | Az Azure Stack környezettel helye. | | helyi
    BASEURL | Alap URL-címe a sablont. | Használja az alapértelmezett értéket, kivéve, ha testre szeretné szabni a központi telepítési sablonok. | 

7. Kattintson az **OK** gombra.
8. A **egyéni üzembe helyezés**, adja meg **előfizetés**, **erőforráscsoport**, és **erőforráscsoport helye**.
    
    ![Csatlakozás az üzembe helyezéshez megadott paraméterek](./media/azure-stack-ethereum/connect-deployment-parameters.png)

    Paraméter neve | Leírás | Megengedett értékek | Mintaérték
    ---------------|-------------|----------------|-------------
    Előfizetés | A vezető előfizetés. | | Használatalapú előfizetés
    Erőforráscsoport | A vezető erőforráscsoport. | | EthereumResources
    Location egység | Az Azure-régió erőforráscsoport. | | helyi

8. Kattintson a **Létrehozás** gombra.

Üzembe helyezés befejezése után indítsa el a kommunikációt a vezető, a tag néhány percet vesz igénybe. Az üzembe helyezés ellenőrzéséhez frissítse a tag felügyeleti webhely. A tag csomópontok állapotát kell futtatnia. 

![Az üzemelő példány ellenőrzése](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>További lépések

Ethereum- és Azure kapcsolatos további tudnivalókért lásd: [Blockchain-technológia és az alkalmazások](https://azure.microsoft.com/solutions/blockchain/).
