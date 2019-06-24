---
title: Hozzáférés a Kubernetes-irányítópulthoz az Azure Stackben |} A Microsoft Docs
description: Az Azure Stack a Kubernetes-irányítópult elérése
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
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: ecd0d3c79edc2359cf82aa9c52fb9021d7fc7a6f
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308678"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Hozzáférés a Kubernetes-irányítópultot az Azure Stackben 

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete* 
> [!Note]   
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban. Az Azure Stack kapcsolat nélküli forgatókönyv jelenleg nem érhető el az előzetes verzió. Csak a Piactéri elem használata fejlesztéshez és teszteléshez.

Kubernetes webes irányítópultot is használhatja az alapvető felügyeleti műveletet tartalmaz. Ez az irányítópult lehetővé teszi az alapszintű állapot-állapot és az alkalmazások metrikáinak megtekintése, létrehozása és -szolgáltatások telepítését és szerkesztheti a meglévő alkalmazásokat. Ez a cikk bemutatja, hogyan állítható be a Kubernetes-irányítópultot az Azure Stacken.

## <a name="prerequisites-for-kubernetes-dashboard"></a>A Kubernetes-irányítópult előfeltételei

* Az Azure Stack Kubernetes-fürt

    Azure Stack üzembe egy Kubernetes-fürtöt kell. További információkért lásd: [üzembe helyezése Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* SSH-ügyfél

    SSH-ügyfelet, a security kell csatlakozni a a fürt fő csomópontjának. Ha Windows használata esetén használhatja [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Szüksége lesz a titkos kulcsot, a Kubernetes-fürt üzembe helyezésekor alkalmazott.

* FTP (PSCP)

    Az FTP-ügyfél, amely támogatja az SSH és az SSH File Transfer Protocol viheti át a tanúsítványokat a fő csomópontot az Azure Stack felügyeleti géphez is szükség lehet. Használhat [Filezillát](https://filezilla-project.org/download.php?type=client). Szüksége lesz a titkos kulcsot, a Kubernetes-fürt üzembe helyezésekor alkalmazott.

## <a name="overview-of-steps-to-enable-dashboard"></a>További lépések elvégzésével irányítópult áttekintése

1.  A Kubernetes tanúsítványokat exportálhat a fürt fő csomópontja. 
2.  A tanúsítványok importálása az Azure Stack felügyeleti gépére.
2.  Nyissa meg a Kubernetes webes irányítópultot. 

## <a name="export-certificate-from-the-master"></a>Exportálja a tanúsítványt a főágból 

Az irányítópult URL-CÍMÉT a fő csomópont kérheti le a fürtben.

1. Szerezze be a nyilvános IP-cím és a felhasználónév a fürt fő az Azure Stack-irányítópultról. Ezek az információk lekéréséhez:

    - Jelentkezzen be a [Azure Stack portálon](https://portal.local.azurestack.external/)
    - Válassza ki **minden szolgáltatás** > **összes erőforrás**. A fő található a fürterőforrás-csoportot. A fő nevű `k8s-master-<sequence-of-numbers>`. 

2. Nyissa meg a portálon a fő csomóponttal. Másolás a **nyilvános IP-cím** címet. Kattintson a **Connect** található a felhasználó nevének lekérése a **virtuális gép helyi fiókjával bejelentkezési** mezőbe. Ez az a fürt létrehozásakor beállított ugyanazt a felhasználónevet. A connect panelen felsorolt magánhálózati IP-cím helyett a nyilvános IP-címet használja.

3.  Nyisson meg egy SSH-ügyfél a főcsomóponthoz való kapcsolódáshoz. Ha Windows dolgozik, akkor használhatja [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) a kapcsolat létrehozásához. Ön a fő csomóponttal, a felhasználónév a nyilvános IP-címet használja, és adja hozzá a titkos kulcsot, a fürt létrehozásakor használt.

4.  Amikor a terminál csatlakozik, írja be a `kubectl` megnyitásához a Kubernetes parancssori ügyfelét.

5. Futtassa a következő parancsot:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Az URL-cím az irányítópulton található. Példa: `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Bontsa ki az önaláírt tanúsítvány, és a PFX-formátumba konvertálja. Futtassa a következő parancsot:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  A titkos kulcsok lekéréséhez a **kube rendszer** névtér. Futtassa a következő parancsot:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Jegyezze fel a kubernetes-irányítópult-jogkivonat -\<XXXXX > érték. 

8.  A token beszerzéséhez, és mentse azt. Frissítés a `kubernetes-dashboard-token-<####>` az előző lépésben a titkos értékkel.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Importálja a tanúsítványt

1. Nyissa meg a Filezillát, és kapcsolódjon a fő csomóponttal. Szüksége lesz a:

    - a főcsomópont nyilvános IP-cím
    - a felhasználónév
    - a privát titkos kulcs
    - Használat **SFTP - SSH File Transfer Protocol**

2. Másolás `/etc/kubernetes/certs/client.pfx` és `/etc/kubernetes/certs/ca.crt` az Azure Stack felügyeleti gépére.

3. Jegyezze meg a fájlok helyét. A parancsfájl frissítse a helyeket, és a PowerShell nyisson meg egy rendszergazda jogú parancssorba. Futtassa a frissített parancsfájlt:  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>A Kubernetes-irányítópult megnyitása 

1. Tiltsa le az előugró ablakok a böngészőben.

2. Az URL-címet a böngésző jelezve, hogy a parancs futtatásakor pont `kubectl cluster-info`. Például: https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard: / proxy 
3. Jelölje be az ügyféltanúsítványt.
4. Adja meg a jogkivonatot. 
5. A bash parancssorban, a fő csomópont újra, és engedélyeket biztosíthat a `kubernetes-dashboard`. Futtassa a következő parancsot:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    A parancsprogram megjeleníti `kubernetes-dashboard` felhőalapú rendszergazdai jogosultságokkal. További információkért lásd: [az RBAC-kompatibilis fürtök](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Használhatja az irányítópultot. A Kubernetes-irányítópult további információkért lásd: [Kubernetes webes felhasználói felületének irányítópultja](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Az Azure Stack a Kubernetes-irányítópult](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>További lépések 

[Az Azure Stack üzembe helyezése Kubernetes](azure-stack-solution-template-kubernetes-deploy.md)  

[Kubernetes-fürt hozzáadása a Marketplace-en (az Azure Stack-operátorokról)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes az Azure-ban](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
