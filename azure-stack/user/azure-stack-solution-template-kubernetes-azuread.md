---
title: A Kubernetes üzembe helyezése Azure Stack Azure Active Directory (Azure AD) használatával | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe Kubernetes Azure Stack Azure Active Directory (Azure AD) használatával.
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
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: e625ba27e683dc11cd8a825441ef73ef37d00f0a
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277708"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>Kubernetes üzembe helyezése Azure Stack a Azure Active Directory használatával

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

> [!Note]  
> A Azure Stack Kubernetes előzetes verzióban érhető el. Az előzetes verzió jelenleg nem támogatja Azure Stack leválasztott forgatókönyv használatát. Csak fejlesztési és tesztelési forgatókönyvek esetén használja a Piactéri elemeket.

A cikk lépéseit követve üzembe helyezheti és beállíthatja a Kubernetes erőforrásait, ha az Azure Active Directory (Azure AD) használja az Identitáskezelés szolgáltatásként, egyetlen, koordinált műveletben.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként győződjön meg arról, hogy rendelkezik a megfelelő engedélyekkel, és hogy a Azure Stack készen áll.

1. Ellenőrizze, hogy létrehozhat-e alkalmazásokat a Azure Active Directory (Azure AD) bérlőben. Ezekre az engedélyekre szüksége lesz a Kubernetes telepítéséhez.

    Az engedélyek ellenőrzésével kapcsolatos utasításokért tekintse meg az [Azure Active Directory engedélyek ellenőrzése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)című témakört.

1. Nyilvános és titkos SSH-kulcspár létrehozása a Linux rendszerű virtuális gépre való bejelentkezéshez Azure Stackon. A fürt létrehozásakor szüksége lesz a nyilvános kulcsra.

    A kulcsok létrehozásával kapcsolatos utasításokért lásd: [SSH-kulcs létrehozása](azure-stack-dev-start-howto-ssh-public-key.md).

1. Győződjön meg arról, hogy érvényes előfizetése van a Azure Stack bérlői portálon, és hogy az új alkalmazások hozzáadásához elegendő nyilvános IP-cím áll rendelkezésre.

    A fürt nem telepíthető Azure Stack **rendszergazdai** előfizetésre. **Felhasználói** előfizetést kell használnia. 

1. Ha nem rendelkezik Kubernetes-fürttel a piactéren, forduljon a Azure Stack rendszergazdájához.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Egyszerű szolgáltatásnév beállítása az Azure-ban. Az egyszerű szolgáltatás hozzáférést biztosít az alkalmazáshoz Azure Stack erőforrásokhoz.

1. Jelentkezzen be a globális [Azure Portalba](https://portal.azure.com).

1. Győződjön meg arról, hogy a Azure Stack-példányhoz társított Azure AD-Bérlővel jelentkezett be. A bejelentkezést az Azure eszköztár szűrő ikonjára kattintva állíthatja be.

    ![Válassza ki az AD-bérlőt](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Hozzon létre egy Azure AD-alkalmazást.

    a. Jelentkezzen be az Azure-fiókjába a [Azure Portalon](https://portal.azure.com)keresztül.  
    b. Válassza a **Azure Active Directory** > **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.  
    c. Adja meg az alkalmazás nevét és URL-címét.  
    d. Válassza ki a **támogatott fióktípus-típusokat**.  
    e.  Adja hozzá a `http://localhost` értéket az alkalmazás URI-ja számára. Válassza a **web** lehetőséget a létrehozni kívánt alkalmazás típusához. Az értékek beállítása után válassza a **regisztráció**lehetőséget.

1. Jegyezze fel az **alkalmazás azonosítóját**. A fürt létrehozásakor szüksége lesz az AZONOSÍTÓra. Az azonosító az **egyszerű szolgáltatásnév ügyfél-azonosítójaként**van hivatkozva.

1. A szolgáltatás alapelveinek paneljén válassza az **új ügyfél titkot**. **Beállítások**@no__t – 1**kulcs**. A szolgáltatási elv hitelesítési kulcsát kell létrehoznia.

    a. Adja meg a **leírást**.

    b. Válassza a **soha nem jár** le **lejár**lehetőséget.

    c. Válassza a **Hozzáadás** lehetőséget. Jegyezze fel a kulcs sztringjét. A fürt létrehozásakor szüksége lesz a kulcs sztringre. A kulcs az **egyszerű szolgáltatás ügyfél-titkos**kulcsaként van hivatkozva.

## <a name="give-the-service-principal-access"></a>Egyszerű szolgáltatás elérésének biztosítása

Adja meg a szolgáltatás egyszerű hozzáférését az előfizetéséhez, hogy a rendszerbiztonsági tag erőforrásokat hozzon létre.

1.  Jelentkezzen be a [Azure stack portálra](https://portal.local.azurestack.external/).

1. Válassza **a minden szolgáltatás** > **előfizetések**lehetőséget.

1. Válassza ki az operátor által a Kubernetes-fürt használatára létrehozott előfizetést.

1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget > válassza a **szerepkör-hozzárendelés hozzáadása**elemet.

1. Válassza ki a **közreműködő** szerepkört.

1. Válassza ki az egyszerű szolgáltatásnév számára létrehozott alkalmazás nevét. Előfordulhat, hogy be kell írnia a nevet a keresőmezőbe.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="deploy-kubernetes"></a>Kubernetes üzembe helyezése

1. Nyissa meg a [Azure stack portált](https://portal.local.azurestack.external).

1. Válassza **az + erőforrás létrehozása** > **számítási** > **Kubernetes-fürt**elemet. Kattintson a  **Create** (Létrehozás) gombra.

    ![Megoldás sablonjának üzembe helyezése](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. alapismeretek

1. Válassza az **alapok** lehetőséget a Kubernetes-fürt létrehozása területen.

    ![Megoldás sablonjának üzembe helyezése](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Válassza ki az **előfizetés** -azonosítóját.

1. Adja meg egy új erőforráscsoport nevét, vagy válasszon ki egy meglévő erőforráscsoportot. Az erőforrás nevének alfanumerikusnak és kisbetűsnek kell lennie.

1. Válassza ki az erőforráscsoport **helyét** . Ez az a régió, amelyet a Azure Stack telepítéséhez választ.

### <a name="2-kubernetes-cluster-settings"></a>2. Kubernetes-fürt beállításai

1. Válassza a **Kubernetes** lehetőséget a Kubernetes-fürt létrehozása területen.

    ![Megoldás sablonjának üzembe helyezése](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. Adja meg a Linux rendszerű **virtuális gép rendszergazdai felhasználónevét**. A Kubernetes-fürt és a DVM részét képező Linux Virtual Machines felhasználóneve.

1. Adja meg a Kubernetes-fürt és a DVM részeként létrehozott összes linuxos gép engedélyezéséhez használt **nyilvános SSH-kulcsot** .

1. Adja meg a **fő profil DNS-előtagját** , amely a régió egyedi. Ennek a régió-egyedi névnek kell lennie, például `k8s-12345`. Az ajánlott eljárás szerint válassza ki az erőforráscsoport nevét.

    > [!Note]  
    > Minden egyes fürthöz használjon egy új és egyedi Master profil DNS-előtagot.

1. Válassza ki a **Kubernetes-főkiszolgáló profiljának darabszámát**. A darabszám a főkészletben lévő csomópontok számát tartalmazza. 1 és 7 közötti érték adható meg. Ennek az értéknek páratlan számnak kell lennie.

1. Válassza ki **a Kubernetes fő virtuális gépek VMSize**. Ez határozza meg a Kubernetes fő virtuális gépek virtuálisgép-méretét. 

1. Válassza ki a **Kubernetes-csomópontok profiljának darabszámát**. A szám a fürtben található ügynökök számát tartalmazza. 

1. Válassza ki a **Kubernetes-csomópont virtuális gépek VMSize**. Ez határozza meg a virtuális gépek Kubernetes-csomópontjának méretét. 

1. Válassza ki az **Azure ad** -t a **Azure stack Identity rendszer** számára a Azure stack telepítéséhez.

1. Adja meg az **egyszerű szolgáltatásnév clientId** , amelyet a Kubernetes Azure Cloud Provider használ. Az ügyfél-azonosító az alkalmazás-AZONOSÍTÓként van azonosítva, amikor a Azure Stack rendszergazdája létrehozta a szolgáltatásnevet.

1. Adja meg az **egyszerű szolgáltatásnév ügyfél titkát**. Ez a szolgáltatás létrehozásakor beállított ügyfél-titkos kulcs.

1. Adja meg a **Kubernetes verzióját**. Ez az Azure-szolgáltató Kubernetes verziója. Azure Stack egy egyéni Kubernetes-buildet szabadít fel minden Azure Stack-verzióhoz.

### <a name="3-summary"></a>3. Összefoglalás

1. Válassza az összefoglalás lehetőséget. A panel egy érvényesítési üzenetet jelenít meg a Kubernetes-fürt konfigurációjának beállításaihoz.

    ![Megoldás sablonjának üzembe helyezése](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Tekintse át a beállításokat.

3. A fürt üzembe helyezéséhez kattintson **az OK gombra** .

> [!TIP]  
>  Ha kérdése van az üzemelő példányával kapcsolatban, közzéteheti a kérdést, vagy megtekintheti, hogy valaki már megválaszolta-e a kérdést a [Azure stack fórumban](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).


## <a name="next-steps"></a>Következő lépések

[Kapcsolódás a fürthöz](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[A Kubernetes irányítópult engedélyezése](azure-stack-solution-template-kubernetes-dashboard.md)
