---
title: A Kubernetes-irányítópult elérése a Azure Stackban | Microsoft Docs
description: Útmutató a Kubernetes-irányítópult eléréséhez Azure Stack
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
ms.openlocfilehash: 2c1a762f002e5058e11857117b4210ad0b59e564
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277548"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>A Kubernetes-irányítópult elérése Azure Stack 

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit* 
> [!Note]   
> A Azure Stack Kubernetes előzetes verzióban érhető el. Az előzetes verzió jelenleg nem támogatja Azure Stack leválasztott forgatókönyv használatát. Csak fejlesztési és tesztelési forgatókönyvek esetén használja a Piactéri elemeket.

A Kubernetes tartalmaz egy webes irányítópultot, amely alapszintű felügyeleti műveletekhez használható. Ez az irányítópult lehetővé teszi az alkalmazások alapvető állapotának és mérőszámának megtekintését, szolgáltatások létrehozását és üzembe helyezését, valamint meglévő alkalmazások szerkesztését. Ez a cikk bemutatja, hogyan állíthatja be a Kubernetes irányítópultját Azure Stackon.

## <a name="prerequisites-for-kubernetes-dashboard"></a>A Kubernetes irányítópultjának előfeltételei

* Azure Stack Kubernetes-fürt

    Telepítenie kell egy Kubernetes-fürtöt, hogy Azure Stack. További információ: a [Kubernetes telepítése](azure-stack-solution-template-kubernetes-deploy.md).

* SSH-ügyfél

    Szüksége lesz egy SSH-ügyfélre a fürt fő csomópontjának biztonsági összekapcsolásához. Ha Windows rendszert használ, használhatja a [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm)-t. Szüksége lesz a Kubernetes-fürt üzembe helyezésekor használt titkos kulcsra.

* FTP (PSCP)

    Szükség lehet egy olyan FTP-ügyfélre is, amely támogatja az SSH-t és az SSH-t File Transfer Protocol a tanúsítványok a főcsomópontról a Azure Stack felügyeleti gépre való átviteléhez. A [FileZilla](https://filezilla-project.org/download.php?type=client)-t használhatja. Szüksége lesz a Kubernetes-fürt üzembe helyezésekor használt titkos kulcsra.

## <a name="overview-of-steps-to-enable-dashboard"></a>Az irányítópult engedélyezésének lépései – áttekintés

1.  Exportálja a Kubernetes tanúsítványokat a fürt fő csomópontján. 
2.  Importálja a tanúsítványokat a Azure Stack felügyeleti gépre.
2.  Nyissa meg a Kubernetes webes irányítópultját. 

## <a name="export-certificate-from-the-master"></a>Tanúsítvány exportálása a főkiszolgálóról 

Az irányítópult URL-címét a fürt fő csomópontjában kérheti le.

1. Szerezze be a fürt főkiszolgálójára vonatkozó nyilvános IP-címet és felhasználónevet a Azure Stack irányítópulton. A következő információk beszerzése:

    - Bejelentkezés a [Azure stack portálra](https://portal.local.azurestack.external/)
    - Válassza az **összes szolgáltatás** > **minden erőforrás**elemet. Keresse meg a főkiszolgálót a fürterőforrás-csoportban. A főkiszolgáló neve `k8s-master-<sequence-of-numbers>`. 

2. Nyissa meg a fő csomópontot a portálon. Másolja a **nyilvános IP-** címet. Kattintson a **Kapcsolódás** gombra a Felhasználónév beolvasásához a **Bejelentkezés a virtuális gép helyi fiókjával jelölőnégyzet használatával** . Ez ugyanaz a Felhasználónév, amelyet a fürt létrehozásakor beállított. A nyilvános IP-cím helyett használja a kapcsolat panelen felsorolt magánhálózati IP-címet.

3.  Nyisson meg egy SSH-ügyfelet a főkiszolgálóhoz való kapcsolódáshoz. Ha Windows rendszeren dolgozik, a [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) segítségével hozhatja létre a kapcsolódást. A főcsomóponthoz tartozó nyilvános IP-címet, a felhasználónevet és a fürt létrehozásakor használt titkos kulcsot kell felvennie.

4.  Ha a terminál csatlakozik, írja be a `kubectl` parancsot a Kubernetes parancssori ügyfél megnyitásához.

5. Futtassa az alábbi parancsot:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Keresse meg az irányítópult URL-címét. Például: `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Bontsa ki az önaláírt tanúsítványt, és alakítsa át a PFX formátumba. Futtassa az alábbi parancsot:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  A **Kube-System** névtérben található titkos kódok listájának beolvasása. Futtassa az alábbi parancsot:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Jegyezze fel a kubernetes-Dashboard-token-\<XXXXX > értéket. 

8.  Szerezze be a jogkivonatot, és mentse azt. Frissítse a `kubernetes-dashboard-token-<####>` értéket az előző lépésből származó titkos értékkel.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>A tanúsítvány importálása

1. Nyissa meg a FileZilla-t, és kapcsolódjon a fő csomóponthoz. A következőkre lesz szüksége:

    - a fő csomópont nyilvános IP-címe
    - a Felhasználónév
    - a titkos titok
    - Az **SFTP-SSH File Transfer Protocol** használata

2. Másolja a `/etc/kubernetes/certs/client.pfx` és a `/etc/kubernetes/certs/ca.crt` értéket a Azure Stack felügyeleti gépre.

3. Jegyezze fel a fájlok helyét. Frissítse a parancsfájlt a helyekkel, majd nyissa meg a PowerShellt egy emelt szintű parancssorral. Futtassa a frissített parancsfájlt:  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>A Kubernetes-irányítópult megnyitása 

1. Tiltsa le a webböngésző előugró ablakának blokkoló eszközét.

2. Irányítsa a böngészőt arra az URL-címre, amelyet a `kubectl cluster-info` parancs futtatásakor feljegyzett. Például: https: \//azurestackdomainnamefork8sdashboard/API/v1/névtér/Kube-System/Services/https: kubernetes-Dashboard:/proxy 
3. Válassza ki az ügyféltanúsítványt.
4. Adja meg a jogkivonatot. 
5. Kapcsolódjon újra a bash parancssorához a fő csomóponton, és adjon meg `kubernetes-dashboard` engedélyeket. Futtassa az alábbi parancsot:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    A parancsfájl `kubernetes-dashboard` Felhőbeli rendszergazdai jogosultságot biztosít. További információ: [RBAC-kompatibilis fürtök](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Használhatja az irányítópultot. A Kubernetes-irányítópulttal kapcsolatos további információkért lásd: [Kubernetes webes felhasználói felület irányítópultja](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Azure Stack Kubernetes-irányítópult](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Következő lépések 

[A Kubernetes üzembe helyezése Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[Kubernetes-fürt hozzáadása a piactérhez (Azure Stack operátor)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes az Azure-ban](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
