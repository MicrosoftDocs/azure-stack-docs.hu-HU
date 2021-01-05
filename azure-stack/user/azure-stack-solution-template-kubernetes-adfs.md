---
title: Kubernetes üzembe helyezése Azure Stack hubhoz Active Directory összevont szolgáltatások (AD FS) használatával
description: Megtudhatja, hogyan helyezhet üzembe Kubernetes az Azure Stack hubhoz Active Directory összevont szolgáltatások (AD FS) használatával.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/12/2020
ms.openlocfilehash: 268157d491b1605ecce442cc4cb348e76657fec2
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873945"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-active-directory-federated-services"></a>Kubernetes üzembe helyezése Azure Stack hubhoz Active Directory összevont szolgáltatások használatával

> [!NOTE]  
> A fürtök Kubernetes való üzembe helyezése csak a Azure Stack Marketplace-elemmel használható. Azure Stack támogatott Kubernetes-fürtök esetében használja [az AK-motort](azure-stack-kubernetes-aks-engine-overview.md).

A cikk lépéseit követve üzembe helyezheti és beállíthatja a Kubernetes erőforrásait. Ezeket a lépéseket akkor használja, ha Active Directory összevont szolgáltatások (AD FS) a személyazonosság-kezelési szolgáltatás.

## <a name="prerequisites"></a>Előfeltételek 

Első lépésként győződjön meg arról, hogy rendelkezik a megfelelő engedélyekkel, és hogy az Azure Stack hub készen áll.

1. Nyilvános és titkos SSH-kulcspár létrehozása a Linux rendszerű virtuális gépre való bejelentkezéshez Azure Stack hub-on. A fürt létrehozásakor szükség van a nyilvános kulcsra.

    A kulcsok létrehozásával kapcsolatos utasításokért lásd: [SSH-kulcs létrehozása](azure-stack-dev-start-howto-ssh-public-key.md).

1. Győződjön meg arról, hogy érvényes előfizetése van a Azure Stack hub bérlői portálon, és hogy az új alkalmazások hozzáadásához elegendő nyilvános IP-cím áll rendelkezésre.

    A fürtöt nem lehet központilag telepíteni Azure Stack hub **rendszergazdai** előfizetésre. **Felhasználói** előfizetést kell használnia. 

1. Ha nem rendelkezik Kubernetes-fürttel a piactéren, forduljon a Azure Stack hub rendszergazdájához.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az Azure Stack hub rendszergazdájával kell dolgoznia, hogy beállítsa a szolgáltatásnevet, amikor a AD FS használja az identitási megoldásként. Az egyszerű szolgáltatás hozzáférést biztosít az alkalmazáshoz Azure Stack hub-erőforrásokhoz.

1. Az Azure Stack hub rendszergazdája megadja az egyszerű szolgáltatásnév adatait. Az egyszerű szolgáltatás információinak a következőhöz hasonlóan kell kinézniük:

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       ClientSecret          : <your client secret>
       Thumbprint            : <often this value is empty>
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : 192.168.200.224
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

2. Rendeljen hozzá egy szerepkört az új egyszerű szolgáltatáshoz az előfizetése közreműködőiként. Útmutatásért lásd: [szerepkör társítása](../operator/azure-stack-add-users-adfs.md).

## <a name="deploy-kubernetes"></a>Kubernetes üzembe helyezése

1. Nyissa meg az Azure Stack hub portált `https://portal.local.azurestack.external` .

1. Válassza **az + erőforrás létrehozása**  >  **számítási**  >  **Kubernetes-fürt** lehetőséget. Kattintson a **Létrehozás** gombra.

    ![Képernyőkép, amely megjeleníti a Kubernetes-fürt létrehozási oldalát.](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. alapismeretek

1. Válassza az **alapok** lehetőséget a Kubernetes-fürt létrehozása területen.

    ![Képernyőfelvétel: a Kubernetes-fürtre vonatkozó alapvető információk hozzáadása.](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Válassza ki az **előfizetés** -azonosítóját.

1. Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevének alfanumerikusnak és kisbetűsnek kell lennie.

1. Válassza ki az erőforráscsoport **helyét** . Ez az a régió, amelyet a Azure Stack hub telepítéséhez választ.

### <a name="2-kubernetes-cluster-settings"></a>2. Kubernetes-fürt beállításai

1. Válassza a **Kubernetes** lehetőséget a Kubernetes-fürt létrehozása területen.

    ![Képernyőkép, amely a Kubernetes-fürt beállításainak konfigurálási lépését jeleníti meg.](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Adja meg a Linux rendszerű **virtuális gép rendszergazdai felhasználónevét**. A Kubernetes-fürt és a DVM részét képező Linux Virtual Machines felhasználóneve.

1. Adja meg a Kubernetes-fürt és a DVM részeként létrehozott összes linuxos gép engedélyezéséhez használt **nyilvános SSH-kulcsot** .

1. Adja meg a **fő profil DNS-előtagját** , amely a régió egyedi. Ennek a régió-egyedi névnek kell lennie, például: `k8s-12345` . Az ajánlott eljárás szerint válassza ki az erőforráscsoport nevét.

    > [!NOTE]  
    > Minden egyes fürthöz használjon egy új és egyedi Master profil DNS-előtagot.

1. Válassza ki a **Kubernetes-főkiszolgáló profiljának darabszámát**. A darabszám a főkészletben lévő csomópontok számát tartalmazza. 1 és 7 közötti érték adható meg. Ennek az értéknek páratlan számnak kell lennie.

1. Válassza ki **a Kubernetes fő virtuális gépek VMSize**.

1. Válassza ki a **Kubernetes-csomópontok profiljának darabszámát**. A szám a fürtben található ügynökök számát tartalmazza. 

1. Válassza ki a **Kubernetes-csomópont virtuális gépek VMSize**. Ez határozza meg a virtuális gépek Kubernetes-csomópontjának méretét. 

1. Válassza az **ADFS** lehetőséget az Azure stack hub-telepítés **Azure stack hub Identity rendszeréhez** .

1. Adja meg az **egyszerű szolgáltatásnév clientId** , amelyet a Kubernetes Azure Cloud Provider használ. Az ügyfél-azonosító az alkalmazás AZONOSÍTÓJAként van azonosítva, amikor a Azure Stack hub rendszergazdája létrehozta a szolgáltatásnevet.

1. Adja meg az **egyszerű szolgáltatásnév ügyfél titkát**. Ez az ügyfél titkos kulcsa, amelyet a Azure Stack hub-rendszergazdától kapott AD FS szolgáltatási elv számára.

1. Adja meg a **Kubernetes verzióját**. Ez az Azure-szolgáltató Kubernetes verziója. Azure Stack hub az egyes Azure Stack hub-verziókhoz egyéni Kubernetes-buildet bocsát ki.

### <a name="3-summary"></a>3. Összefoglalás

1. Válassza az összefoglalás lehetőséget. A panel egy érvényesítési üzenetet jelenít meg a Kubernetes-fürt konfigurációjának beállításaihoz.

    ![Megoldás sablonjának üzembe helyezése](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Tekintse át a beállításokat.

3. A fürt üzembe helyezéséhez kattintson **az OK gombra** .

> [!TIP]  
>  Ha kérdése van az üzemelő példányával kapcsolatban, közzéteheti a kérdést, vagy megtekintheti, hogy valaki már megválaszolta-e a kérdést az [Azure stack hub fórumában](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>További lépések

[Kapcsolódás a fürthöz](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[A Kubernetes irányítópult engedélyezése](azure-stack-solution-template-kubernetes-dashboard.md)