---
title: Az Active Directory összevonási szolgáltatásokban (AD FS) használatával az Azure Stack üzembe helyezése Kubernetes |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe Kubernetes az Azure Stackhez az Active Directory összevonási szolgáltatásokban (AD FS) használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: nav
ms.topic: article
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 738a8ab4c43eac9cc68deb63f44577df0f512f0b
ms.sourcegitcommit: 759a01b566597a71b18fca25902cacc983a5a63b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67298064"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Az Active Directory összevont szolgáltatásokat az Azure Stack üzembe helyezése Kubernetes

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!Note]  
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban. Az Azure Stack kapcsolat nélküli forgatókönyv jelenleg nem érhető el az előzetes verzió. Csak a Piactéri elem használata fejlesztéshez és teszteléshez. A [AKS motor](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md) éles környezetben is használható.

A regisztrálással kapcsolatban, telepítse és állítsa be az erőforrásokat a Kubernetes esetében ez a cikk. Az Active Directory összevonási szolgáltatásokban (AD FS) az identity management-szolgáltatás az alábbi lépéseket követve.

## <a name="prerequisites"></a>Előfeltételek 

Első lépésként ellenőrizze, hogy a megfelelő engedélyekkel rendelkezik, és, hogy készen áll-e az Azure Stack.

1. Hozzon létre nyilvános és titkos ssh-kulcs, jelentkezzen be a Linux rendszerű virtuális gép az Azure Stacken. A fürt létrehozásakor kell a nyilvános kulcsot.

    -Kulcs létrehozásával kapcsolatos utasításokért lásd: [SSH kulcs generálása](azure-stack-dev-start-howto-ssh-public-key.md).

1. Ellenőrizze, hogy az Azure Stack-bérlői portálon érvényes előfizetéssel rendelkezik, és, hogy rendelkezik-e elegendő nyilvános IP-címek adhatók hozzá az új alkalmazások.

    A fürt nem telepíthető az Azure Stackkel **rendszergazda** előfizetés. Szüksége lesz egy **felhasználói** előfizetés. 

1. Ha a Kubernetes-fürt nem rendelkezik a Marketplace-en, forduljon az Azure Stack rendszergazdai.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az AD FS-identitáskezelési megoldásként használatakor az egyszerű szolgáltatás beállítása az Azure Stack rendszergazdával együttműködve kell. Egyszerű szolgáltatás Azure Stack-erőforrások az alkalmazás-hozzáférést biztosít.

1. Az Azure Stack rendszergazdai biztosít a szolgáltatásnév adatait. A szolgáltatásnév adatait hasonlóan kell kinéznie:

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       ClientSecret          : <your client secret>
       Thumbprint            : <often this value is empty>
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : 192.168.200.224
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

2. Közreműködője szerepkör az új egyszerű szolgáltatás hozzárendelése az előfizetéshez. Útmutatásért lásd: [szerepkör hozzárendelése](../operator/azure-stack-add-users-adfs.md).

## <a name="deploy-kubernetes"></a>Kubernetes üzembe helyezése

1. Nyissa meg a [Azure Stack portálon](https://portal.local.azurestack.external).

1. Válassza ki **+ erőforrás létrehozása** > **számítási** > **Kubernetes-fürt**. Kattintson a **Létrehozás** gombra.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Alapvető beállítások

1. Válassza ki **alapjai** a Kubernetes-fürt létrehozása.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Válassza ki a **előfizetés** azonosítóját.

1. Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevét kell lennie a alfanumerikus- és nagybetűket.

1. Válassza ki a **hely** az erőforráscsoport. Ez az a régió úgy dönt, az Azure Stack-telepítés.

### <a name="2-kubernetes-cluster-settings"></a>2. Kubernetes-fürt beállításai

1. Válassza ki **Kubernetes-fürt beállítások** a Kubernetes-fürt létrehozása.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Adja meg a **Linux rendszerű virtuális gép rendszergazdai felhasználónevét**. A Linux rendszerű virtuális gépek, a Kubernetes-fürt részét képező és a DVM felhasználóneve.

1. Adja meg a **SSH Public Key** használt a hitelesítéshez a Kubernetes-fürt és a DVM részeként létrehozott összes Linux rendszerű gépen.

1. Adja meg a **fő profil DNS-előtagja** , amely egyedi a régióban. Ez egy régió egyedi nevet, például kell lennie `k8s-12345`. Próbálja meg úgy döntött, hogy ugyanaz, mint az erőforráscsoport neve ajánlott eljárás.

    > [!Note]  
    > Ha mindegyik fürthöz egy új és egyedi fő profil DNS-előtagot használja.

1. Válassza ki a **Kubernetes főkiszolgálók készlet profil száma**. A szám a fő készletben lévő csomópontok számát tartalmazza. Nem lehet 1-től 7. Ez az érték páratlan számúaknak kell lennie.

1. Válassza ki **a fő Kubernetes-virtuálisgép az VMSize**.

1. Válassza ki a **Kubernetes csomópontok készlet profil száma**. A száma a fürtben található ügynökök számát tartalmazza. 

1. Válassza ki a **a Kubernetes-csomópont virtuális gépek VMSize**. Azt határozza meg a virtuális gép méretét a Kubernetes csomópont azon virtuális gépeit. 

1. Válassza ki **ADFS** számára a **Azure Stack identitásrendszer** az Azure Stack-telepítés.

1. Adja meg a **szolgáltatás egyszerű clientId** ezt használja a Kubernetes Azure felhőszolgáltató. Az Alkalmazásazonosítót azonosította az eseményt az egyszerű szolgáltatás létrehozásakor az Azure Stack rendszergazdai ügyfél-azonosító.

1. Adja meg a **egyszerű szolgáltatás titkos ügyfélkódja**. Ez az az ügyfél titkos kulcsát, az egyszerű szolgáltatásnév az AD FS által biztosított Azure Stack-rendszergazdához.

1. Adja meg a **Kubernetes-verzió**. Ez az a verzió a Kubernetes Azure-szolgáltatóhoz. Az Azure Stack kiad egy egyéni Kubernetes-build minden egyes Azure Stack-verzió.

### <a name="3-summary"></a>3. Összefoglalás

1. Válassza ki a összegzése. A panel a Kubernetes-fürt konfigurációk beállítások érvényesítése üzenetet jelenít meg.

    ![Megoldássablon telepítése](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Tekintse át a beállításokat.

3. Válassza ki **OK** a fürt üzembe helyezéséhez.

> [!TIP]  
>  Ha az üzembe helyezéssel kapcsolatos kérdése van, tegye fel a kérdéseit, vagy tekintse meg, ha valaki már megválaszolta a kérdést a [Azure Stack fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>További lépések

[Csatlakozás a fürthöz](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[A Kubernetes-irányítópult engedélyezése](azure-stack-solution-template-kubernetes-dashboard.md)