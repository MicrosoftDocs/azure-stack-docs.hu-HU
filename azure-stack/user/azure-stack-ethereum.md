---
title: Ethereum blockchain-hálózat üzembe helyezése Azure Stack hub-on
description: Oktatóanyag egyéni megoldási sablonok használatával a konzorcium Ethereum blockchain-hálózatának üzembe helyezéséhez és konfigurálásához Azure Stack hub-on.
author: PatAltimore
ms.author: patricka
ms.date: 10/07/2020
ms.topic: tutorial
ms.reviewer: seyadava
ms.lastreviewed: 10/07/2020
ms.openlocfilehash: f409a2629243c0eb1cfe15ad857aa3e229832bb5
ms.sourcegitcommit: 7b189e5317b8fe5f8ad825565da3607a39a1b899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94493703"
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

A sablon számos módon képes üzembe helyezni a Ethereum consortiumt a vezető és a tag számára. Itt láthatók a teszteltek:

- Egy több csomópontos Azure Stack hub esetében, az Azure AD-vel vagy a AD FS-vel, az érdeklődőket és a tagokat ugyanazon előfizetés vagy eltérő előfizetések használatával helyezheti üzembe.
- Egy csomópontos Azure Stack hubhoz (az Azure AD-vel) telepítse az érdeklődőt és a tagot ugyanazzal az előfizetéssel.

## <a name="standalone-and-consortium-leader-deployment"></a>Önálló és konzorciumi vezető üzembe helyezés

A Consortium Leader-sablon konfigurálja az első tag hálózati lábnyomát. 

1. Töltse le a [Leader-sablont a githubról](https://aka.ms/aa6z619).
1. Az Azure Stack hub-bérlői portálon válassza az **+ erőforrás létrehozása > template Deployment** lehetőséget az egyéni sablonból való üzembe helyezéshez.
1. Válassza a **saját sablon létrehozása lehetőséget a szerkesztőben** az új egyéni sablon szerkesztéséhez.
1. A jobb oldali szerkesztési ablaktáblán másolja és illessze be a korábban letöltött vezető sablon JSON-fájlját.
    
    [![Sablon szerkesztése a Leader-sablonnal beillesztett](./media/azure-stack-ethereum/edit-leader-template.png)](./media/azure-stack-ethereum/edit-leader-template.png#lightbox)

1. Kattintson a **Mentés** gombra.
1. Az **alapvető** beállítások lapon végezze el a következő beállításokat.

    Paraméter neve | Leírás | Mintaérték
    ---------------|-------------|-------------
    Előfizetés | Az előfizetés, amelyre a konzorcium-hálózatot telepíteni kell. | Fogyasztási előfizetés
    Erőforráscsoport | Az az erőforráscsoport, amelyre a konzorcium-hálózatot telepíteni kell. | EthereumResources
    Régió | Az Azure-régió erőforrásaihoz. | helyi
    Név előtagja | A telepített erőforrások elnevezésének alapjául szolgáló sztring. Legfeljebb hat alfanumerikus karaktert használhat. | ETH
    Hitelesítés típusa | A virtuális géphez való hitelesítés módszere. Az engedélyezett értékek jelszó vagy nyilvános SSH-kulcs. | Jelszó
    Rendszergazdai felhasználónév | Az egyes telepített virtuális gépek rendszergazdai felhasználóneve. Egy 64 karakterből állhat. | gethadmin
    Rendszergazdai jelszó (hitelesítés típusa = jelszó)| Az egyes üzembe helyezett virtuális gépek rendszergazdai fiókjának jelszava. A jelszónak tartalmaznia kell a következő követelményeket: 1 nagybetűs karakter, 1 kisbetűs karakter, 1 szám és 1 speciális karakter. <br />Habár a virtuális gépek kezdetben ugyanazzal a jelszóval rendelkeznek, a kiépítés után megváltoztathatja a jelszót. 12 – 72 karakter hosszú lehet. |
    Rendszergazdai SSH-kulcs (hitelesítés típusa = sshPublicKey) | A biztonságos rendszerhéj RSA nyilvánoskulcs-karakterlánca, amely távoli bejelentkezéshez használatos. |
    Genesis-blokk | Egyéni Genesis-blokkot jelölő JSON-karakterlánc.  A paraméter értékének megadása nem kötelező. |
    Ethereum fiók jelszava | A Ethereum-fiók biztonságossá tételéhez használt rendszergazdai jelszó. |
    Ethereum-fiók jelszava | A Ethereum-fiókhoz társított titkos kulcs létrehozásához használt jelszó. Egy erős titkos kulcs biztosításához vegye figyelembe a megfelelő véletlenszerű jelszóval rendelkező jelszót. |
    Ethereum hálózati azonosító | A konzorcium hálózati azonosítója. Tetszőleges értéket használhat 5 és 999 999 999 között. | 72
    Konzorciumi tag azonosítója | A konzorcium-hálózat egyes tagjaihoz tartozó azonosító. Ennek az AZONOSÍTÓnak egyedinek kell lennie a hálózaton. | 0
    Bányászati csomópontok száma | Az egyes konzorciumi tagok adatbányászati csomópontjainak száma. Használjon 2 és 15 közötti értéket. | 2
    Adatbányászati csomópont virtuális gép mérete | A bányászati csomópontok virtuálisgép-mérete. | Standard_A1
    Adatbányászati tár fióktípus | A bányászati csomópontok tárolási teljesítménye. | Standard_LRS
    TX-csomópontok száma | Elosztott terhelésű tranzakciós csomópontok száma 1 és 5 közötti értéket használjon. | 1
    TX-csomópont virtuális gép mérete | A tranzakciós csomópontok virtuálisgép-mérete. | Standard_A1
    TX Storage-fiók típusa | A tranzakciós csomópontok tárolási teljesítménye. | Standard_LRS
    Kiindulási URL-cím | A telepítési sablonok beszerzésének alap URL-címe. Ha testre szeretné szabni a központi telepítési sablonokat, használja az alapértelmezett értéket. |

1. Válassza a **Felülvizsgálat + létrehozás** lehetőséget. A sikeres ellenőrzés után válassza a **Létrehozás** lehetőséget.

Az üzembe helyezés akár 20 percet vagy hosszabb időt is igénybe vehet.

Az üzembe helyezés befejezése után tekintse át a **Microsoft. template** központi telepítési összegzését az erőforráscsoport telepítési szakaszában. Az összefoglalás a konzorcium tagjaihoz való csatlakozáshoz használt kimeneti értékeket tartalmazza.

A Leader üzembe helyezésének ellenőrzéséhez lépjen a vezető rendszergazdai webhelyére. A felügyeleti hely címe a **Microsoft. template** telepítésének output (kimenet) szakaszában található.  

![A Leader üzembe helyezésének összefoglalása](./media/azure-stack-ethereum/ethereum-node-status.png)

## <a name="joining-consortium-member-deployment"></a>A konzorciumi tagok központi telepítésének csatlakoztatása

1. Töltse le a [konzorciumi tag sablonját a githubról](https://aka.ms/aa6zkua).
1. Az Azure Stack hub-bérlői portálon válassza az **+ erőforrás létrehozása > template Deployment** lehetőséget az egyéni sablonból való üzembe helyezéshez.
1. Válassza a **saját sablon létrehozása lehetőséget a szerkesztőben** az új egyéni sablon szerkesztéséhez.
1. A jobb oldali szerkesztési ablaktáblán másolja ki és illessze be a korábban letöltött konzorciumi tag sablonját.
1. Kattintson a **Mentés** gombra.
1. Az **alapvető** beállítások lapon végezze el a következő beállításokat.

    Paraméter neve | Leírás | Mintaérték
    ---------------|-------------|-------------
    Előfizetés | Az előfizetés, amelyre a konzorcium-hálózatot telepíteni kell. | Fogyasztási előfizetés
    Erőforráscsoport | Az az erőforráscsoport, amelyre a konzorcium-hálózatot telepíteni kell. | EthereumResources
    Régió | Az Azure-régió erőforrásaihoz. | helyi
    Név előtagja | A telepített erőforrások elnevezésének alapjául szolgáló sztring. Legfeljebb hat alfanumerikus karaktert használhat. | ETH
    Hitelesítés típusa | A virtuális géphez való hitelesítés módszere. Az engedélyezett értékek jelszó vagy nyilvános SSH-kulcs. | Jelszó
    Rendszergazdai felhasználónév | Az egyes telepített virtuális gépek rendszergazdai felhasználóneve. Egy 64 karakterből állhat. | gethadmin
    Rendszergazdai jelszó (hitelesítés típusa = jelszó)| Az egyes üzembe helyezett virtuális gépek rendszergazdai fiókjának jelszava. A jelszónak tartalmaznia kell a következő követelményeket: 1 nagybetűs karakter, 1 kisbetűs karakter, 1 szám és 1 speciális karakter. <br />Habár a virtuális gépek kezdetben ugyanazzal a jelszóval rendelkeznek, a kiépítés után megváltoztathatja a jelszót. 12 – 72 karakter hosszú lehet. |
    Rendszergazdai SSH-kulcs (hitelesítés típusa = sshPublicKey) | A biztonságos rendszerhéj RSA nyilvánoskulcs-karakterlánca, amely távoli bejelentkezéshez használatos. |
    Genesis-blokk | Egyéni Genesis-blokkot jelölő JSON-karakterlánc. A paraméter értékének megadása nem kötelező. |
    Ethereum fiók jelszava | A Ethereum-fiók biztonságossá tételéhez használt rendszergazdai jelszó. |
    Ethereum-fiók jelszava | A Ethereum-fiókhoz társított titkos kulcs létrehozásához használt jelszó. Egy erős titkos kulcs biztosításához vegye figyelembe a megfelelő véletlenszerű jelszóval rendelkező jelszót. |
    Konzorciumi tag azonosítója | A konzorcium-hálózat egyes tagjaihoz tartozó azonosító. Ennek az AZONOSÍTÓnak egyedinek kell lennie a hálózaton. | 0
    Bányászati csomópontok száma | Az egyes konzorciumi tagok adatbányászati csomópontjainak száma. Használjon 2 és 15 közötti értéket. | 2
    Adatbányászati csomópont virtuális gép mérete | A bányászati csomópontok virtuálisgép-mérete. | Standard_A1
    Adatbányászati tár fióktípus | A bányászati csomópontok tárolási teljesítménye. | Standard_LRS
    TX-csomópontok száma | Elosztott terhelésű tranzakciós csomópontok száma 1 és 5 közötti értéket használjon. | 1
    TX-csomópont virtuális gép mérete | A tranzakciós csomópontok virtuálisgép-mérete. | Standard_A1
    TX Storage-fiók típusa | A tranzakciós csomópontok tárolási teljesítménye. | Standard_LRS
    Konzorcium-adatkészletek | Az URL-cím, amely a másik tag üzemelő példánya által biztosított vonatkozó konzorcium-konfigurációs adatokat mutat. Ez az érték a vezető telepítési kimenetén található. |
    Távoli tag VNET | A vezető VNET-címtartomány. Ez az érték a vezető telepítési kimenetén található. |
    Távoli tag NVA nyilvános IP-címe | A vezető NVA IP-címe. Ez az érték a vezető telepítési kimenetén található. |
    Megosztott kulcs összekapcsolása | Egy előre létrehozott titok a konzorciumi hálózat azon tagjai között, amelyek átjáró-kapcsolatot hoznak létre. |
    Kiindulási URL-cím | A telepítési sablonok beszerzésének alap URL-címe. Ha testre szeretné szabni a központi telepítési sablonokat, használja az alapértelmezett értéket. |
1. Válassza a **Felülvizsgálat + létrehozás** lehetőséget. A sikeres ellenőrzés után válassza a **Létrehozás** lehetőséget.

Az üzembe helyezés akár 20 percet vagy hosszabb időt is igénybe vehet.

Az üzembe helyezés befejezése után tekintse át a **Microsoft. template** központi telepítési összegzését az erőforráscsoport telepítési szakaszában. Az összefoglalás a konzorcium tagjaihoz való kapcsolódáshoz használt kimeneti értékeket tartalmazza.

A tag üzembe helyezésének ellenőrzéséhez tallózzon a tag felügyeleti webhelyén. A felügyeleti hely címe a **Microsoft. template** telepítésének kimenet szakaszában található.

![Tag központi telepítésének összegzése](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Ahogy a képen is látható, a tag csomópontjainak állapota **nem fut**. Ez az állapot azért van, mert a tag és a vezető közötti kapcsolat nincs létrehozva. A tag és a vezető közötti kapcsolat kétirányú kapcsolat. A tag központi telepítésekor a sablon automatikusan létrehozza a csatlakozást a tag és a vezető között. A vezető és a tag közötti kapcsolatok létrehozásához lépjen a következő lépésre.

## <a name="connect-member-and-leader"></a>A tag és a vezető összekötése

Ez a sablon létrehoz egy, a vezető és egy távoli tag közötti kapcsolatokat. 

1. Töltse le a [csatlakozási tag és a Leader-sablonját a githubról](https://aka.ms/aa6zdyt).
1. Az Azure Stack hub-bérlői portálon válassza az **+ erőforrás létrehozása > template Deployment** lehetőséget az egyéni sablonból való üzembe helyezéshez.
1. Válassza a **saját sablon létrehozása lehetőséget a szerkesztőben** az új egyéni sablon szerkesztéséhez.
1. A jobb oldali szerkesztési ablaktáblán másolja ki és illessze be a korábban letöltött konzorciumi tag sablonját.
1. Kattintson a **Mentés** gombra.
1. Hajtsa végre a következő beállításokat.

    Paraméter neve | Leírás | Mintaérték
    ---------------|-------------|-------------
    Előfizetés | Az előfizetés, amelyre a konzorcium-hálózatot telepíteni kell. | Fogyasztási előfizetés
    Erőforráscsoport | Az az erőforráscsoport, amelyre a konzorcium-hálózatot telepíteni kell. | EthereumResources
    Régió | Az Azure-régió erőforrásaihoz. | helyi
    Tag neve előtag | A telepített erőforrások elnevezésének alapjául szolgáló sztring. Legfeljebb hat alfanumerikus karaktert használhat. | ETH
    Tag útválasztási táblázatának neve | A vezető útválasztási táblázatának neve. Ez az érték a vezető telepítési kimenetén található. |
    Távoli tag VNET | A tag címterület. Ez az érték a tag telepítési kimenetén található. |
    Távoli tag NVA nyilvános IP-címe | A NVA IP-címe, amelyhez csatlakozni kíván. Ez az érték a tag telepítési kimenetén található. |
    Megosztott kulcs összekapcsolása | A konzorciumi hálózat azon tagjai között, amelyek kapcsolatot hoznak létre, előre elkészített titok.  |
    Tag NVA magánhálózati IP-címe | A tag NVA IP-címe. Ez az érték a tag telepítési kimenetén található. |
    Kiindulási URL-cím | A telepítési sablonok beszerzésének alap URL-címe. Ha testre szeretné szabni a központi telepítési sablonokat, használja az alapértelmezett értéket. |
1. Válassza a **Felülvizsgálat + létrehozás** lehetőséget. A sikeres ellenőrzés után válassza a **Létrehozás** lehetőséget.

Az üzembe helyezés befejezése után néhány percet vesz igénybe, hogy a vezető és a tag is megkezdje a kommunikációt. A központi telepítés ellenőrzéséhez frissítse a tag felügyeleti webhelyét. A tag csomópontjainak állapotának futnia kell.

![Az üzemelő példány ellenőrzése](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>További lépések

További információ a Ethereum és az Azure-ról: [Blockchain Technology and Applications](https://azure.microsoft.com/solutions/blockchain/).
