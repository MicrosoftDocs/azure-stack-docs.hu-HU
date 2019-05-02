---
title: Azure Active Directory (Azure AD) használatával az Azure Stack üzembe helyezése Kubernetes |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe Kubernetes az Azure Stackhez az Azure Active Directory (Azure AD) használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 6e4402be7108f242e1d285ebe91dfece744f0805
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64301967"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>Üzembe helyezése Kubernetes az Azure Stackhez az Azure Active Directoryval

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!Note]  
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban. Az Azure Stack kapcsolat nélküli forgatókönyv jelenleg nem érhető el az előzetes verzió.

Ebben a cikkben üzembe helyezéséhez és az erőforrások beállítása a Kubernetes esetében az Azure Active Directory (Azure AD) használatakor, mivel az identity management szolgáltatás egyetlen, koordinált művelet a lépésekkel.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként ellenőrizze, hogy a megfelelő engedélyekkel rendelkezik, és, hogy készen áll-e az Azure Stack.

1. Győződjön meg arról, hogy hozhat létre alkalmazásokat az Azure Active Directory (Azure AD) bérlő. A Kubernetes üzembe helyezési ezekkel az engedélyekkel kell rendelkeznie.

    Engedélyek ellenőrzése kapcsolatos utasításokért lásd: [ellenőrizze az Azure Active Directory-engedélyek](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

1. Hozzon létre nyilvános és titkos ssh-kulcs, jelentkezzen be a Linux rendszerű virtuális gép az Azure Stacken. A fürt létrehozásakor kell a nyilvános kulcsot.

    -Kulcs létrehozásával kapcsolatos utasításokért lásd: [SSH kulcs generálása](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Ellenőrizze, hogy az Azure Stack-bérlői portálon érvényes előfizetéssel rendelkezik, és, hogy rendelkezik-e elegendő nyilvános IP-címek adhatók hozzá az új alkalmazások.

    A fürt nem telepíthető az Azure Stackkel **rendszergazda** előfizetés. Szüksége lesz egy **felhasználói** előfizetés. 

1. Ha a Kubernetes-fürt nem rendelkezik a Marketplace-en, forduljon az Azure Stack rendszergazdai.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Állítsa be egy egyszerű szolgáltatást az Azure-ban. Egyszerű szolgáltatás Azure Stack-erőforrások az alkalmazás-hozzáférést biztosít.

1. Jelentkezzen be globális [az Azure portal](https://portal.azure.com).

1. Ellenőrizze, hogy Ön az Azure Stack-példányhoz társított Azure AD-bérlő jelentkezzen be. Az Azure eszköztárában található szűrő ikonra kattintva válthat a bejelentkezés.

    ![Válassza ki, hogy AD-bérlő](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Hozzon létre egy Azure AD-alkalmazást.

    a. Válassza ki **az Azure Active Directory** > **+ Alkalmazásregisztrációk** > **új Alkalmazásregisztráció**.

    b. Adjon meg egy **neve** az alkalmazás.

    c. Válassza ki **webalkalmazás / API**.

    d. Adja meg `http://localhost` számára a **bejelentkezési URL-**.

    c. Kattintson a **Create** (Létrehozás) gombra.

1. Jegyezze fel az **alkalmazás azonosítóját**. A fürt létrehozásakor kell a azonosítója. Az azonosító néven van hivatkozott **egyszerű szolgáltatás ügyfél-azonosító**.

1. Válassza ki **beállítások** > **kulcsok**.

    a. Adja meg a **leírás**.

    b. Válassza ki **soha nem jár le** a **lejárat**.

    c. Kattintson a **Mentés** gombra. Győződjön meg, vegye figyelembe a kulcs karakterláncát. Szüksége lesz a kulcs karakterláncát a fürt létrehozásakor. A kulcs hivatkozik a **egyszerű szolgáltatás titkos Ügyfélkód**.

## <a name="give-the-service-principal-access"></a>A szolgáltatás egyszerű hozzáférést

A szolgáltatás egyszerű hozzáférést biztosít az előfizetéshez, hogy a rendszerbiztonsági tag erőforrásokat hozhat létre.

1.  Jelentkezzen be a [Azure Stack portálon](https://portal.local.azurestack.external/).

1. Válassza ki **minden szolgáltatás** > **előfizetések**.

1. Válassza ki az előfizetést, az üzemeltető által létrehozott a Kubernetes-fürt használatával.

1. Válassza ki **hozzáférés-vezérlés (IAM)** > Válasszon **szerepkör-hozzárendelés hozzáadása**.

1. Válassza ki a **közreműködői** szerepkör.

1. Válassza ki az egyszerű szolgáltatásnév a szolgáltatás számára létrehozott alkalmazás nevét. A Keresés mezőbe írja be a név lehet.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="deploy-kubernetes"></a>Kubernetes üzembe helyezése

1. Nyissa meg a [Azure Stack portálon](https://portal.local.azurestack.external).

1. Válassza ki **+ erőforrás létrehozása** > **számítási** > **Kubernetes-fürt**. Kattintson a **Create** (Létrehozás) gombra.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Alapvető beállítások

1. Válassza ki **alapjai** a Kubernetes-fürt létrehozása.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Válassza ki a **előfizetés** azonosítóját.

1. Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevét kell lennie a alfanumerikus- és nagybetűket.

1. Válassza ki a **hely** az erőforráscsoport. Ez az a régió úgy dönt, az Azure Stack-telepítés.

### <a name="2-kubernetes-cluster-settings"></a>2. Kubernetes-fürt beállításai

1. Válassza ki **Kubernetes-fürt beállítások** a Kubernetes-fürt létrehozása.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. Adja meg a **Linux rendszerű virtuális gép rendszergazdai felhasználónevét**. A Linux rendszerű virtuális gépek, a Kubernetes-fürt részét képező és a DVM felhasználóneve.

1. Adja meg a **SSH Public Key** használt a hitelesítéshez a Kubernetes-fürt és a DVM részeként létrehozott összes Linux rendszerű gépen.

1. Adja meg a **fő profil DNS-előtagja** , amely egyedi a régióban. Ez egy régió egyedi nevet, például kell lennie `k8s-12345`. Próbálja meg úgy döntött, hogy ugyanaz, mint az erőforráscsoport neve ajánlott eljárás.

    > [!Note]  
    > Ha mindegyik fürthöz egy új és egyedi fő profil DNS-előtagot használja.

1. Válassza ki a **Kubernetes fő készlet profil darabszám**. A szám a fő készletben lévő csomópontok számát tartalmazza. Nem lehet 1-től 7. Ez az érték páratlan számúaknak kell lennie.

1. Válassza ki **a fő Kubernetes-virtuálisgép az VMSize**.

1. Válassza ki a **Kubernetes csomópontok készlet profil száma**. A száma a fürtben található ügynökök számát tartalmazza. 

1. Válassza ki a **Tárolóprofil**. Választhat **Blob lemez** vagy **Managed Disk**. Azt határozza meg a virtuális gép méretét a Kubernetes csomópont azon virtuális gépeit. 

1. Válassza ki **Azure ad-ben** számára a **Azure Stack identitásrendszer** az Azure Stack-telepítés. 

1. Adja meg a **szolgáltatásnév ClientId** ezt használja a Kubernetes Azure felhőszolgáltató. Az ügyfél-azonosító az Alkalmazásazonosítót azonosította az eseményt, az egyszerű szolgáltatás létrehozásakor.

1. Adja meg a **egyszerű szolgáltatás titkos Ügyfélkód** , hogy létrehozta az egyszerű szolgáltatás létrehozásakor.

1. Adja meg a **Kubernetes az Azure Cloud szolgáltató verziója**. Ez az a verzió a Kubernetes Azure-szolgáltatóhoz. Az Azure Stack kiad egy egyéni Kubernetes-build minden egyes Azure Stack-verzió.

### <a name="3-summary"></a>3. Összegzés

1. Válassza ki a összegzése. A panel a Kubernetes-fürt konfigurációk beállítások érvényesítése üzenetet jelenít meg.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Tekintse át a beállításokat.

3. Válassza ki **OK** a fürt üzembe helyezéséhez.

> [!TIP]  
>  Ha az üzembe helyezéssel kapcsolatos kérdése van, tegye fel a kérdéseit, vagy tekintse meg, ha valaki már megválaszolta a kérdést a [Azure Stack fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).


## <a name="next-steps"></a>További lépések

[Csatlakozás a fürthöz](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[A Kubernetes-irányítópult engedélyezése](azure-stack-solution-template-kubernetes-dashboard.md)
