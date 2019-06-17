---
title: Az Azure Stack-tárolókkal Kubernetes üzembe helyezése |} A Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe Kubernetes-tárolók használata az Azure Stack használatával.
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
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 5ab223800e70bf5f942c42c51e2870fe0dac0a4d
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131607"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>Kubernetes-tárolók használata az Azure Stack üzembe helyezése

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!Note]  
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban. Az Azure Stack kapcsolat nélküli forgatókönyv jelenleg nem érhető el az előzetes verzió.

Ebben a cikkben üzembe helyezéséhez és a Kubernetes esetében egyetlen, koordinált műveletben állítsa be az erőforrásokat a lépésekkel. A lépések egy Azure Resource Manager-megoldássablon használják. Ön lesz kell az Azure Stack-telepítés, a szükséges információkat gyűjthet a hozza létre a sablont, és majd üzembe helyezése a felhőben. Az Azure Stack-sablon nem használ felügyelt AKS szolgáltatásának ugyanazt a globális Azure-ban érhető el.

## <a name="kubernetes-and-containers"></a>Kubernetes és a tárolók

A Kubernetes Azure Stack az AKS-motor által létrehozott Azure Resource Manager-sablonok használatával is telepítheti. [Kubernetes](https://kubernetes.io) üzembe helyezés automatizálásához egy nyílt forráskódú rendszer méretezés, és a tárolókban található alkalmazások felügyeletét. A [tároló](https://www.docker.com/what-container) van a képet. A tároló rendszerképét ellentétben a virtuális gép egy virtuális gépet (VM), azonban hasonlít, a tároló csak az alkalmazás, például a kódot, a kód, az adott könyvtárakat és a beállítások végrehajtásához futásidejű futtatásához szükséges erőforrásokat tartalmazza.

A Kubernetes használhatja:

- Rugalmasan méretezhető, bővíthető, másodpercek alatt telepíthető alkalmazásokat fejleszthet. 
- Leegyszerűsítheti az alkalmazást, és a megbízhatóság javításához különböző Helm-alkalmazások. [Helm](https://github.com/kubernetes/helm) egy nyílt forráskódú csomagolás eszköz, amely segít telepítése és a Kubernetes-alkalmazások életciklusának kezelését.
- Könnyedén figyelheti és diagnosztizálhatja az alkalmazások állapotát.

Ön csak kell fizetnie a számítási feladatok használatáért támogatása a fürt csomópontjai által igényelt. További információkért lásd: [használat és számlázás az Azure Stackben](../operator/azure-stack-billing-and-chargeback.md).

## <a name="deploy-kubernetes-to-use-containers"></a>Kubernetes használhatók a tárolók üzembe helyezése

A lépéseket az Azure Stacken Kubernetes-fürt üzembe helyezése az identity management-szolgáltatás függ. Ellenőrizze az identitáskezelési megoldás az Azure Stack telepítése által használt. Lépjen kapcsolatba az Azure Stack rendszergazdai az identity management szolgáltatás ellenőrzése.

- **Az Azure Active Directory (Azure AD)**  
A fürtön telepíteni, ha az Azure AD-vel kapcsolatos utasításokért lásd: [Kubernetes üzembe helyezése az Azure Stackhez az Azure Active Directory (Azure AD) használatával](azure-stack-solution-template-kubernetes-azuread.md).

- **Az Active Directory összevont szolgáltatások (AD FS)**  
A fürtön telepíteni, ha az AD FS használatával, lásd: [Kubernetes üzembe helyezése az Azure Stackhez az Active Directory összevont szolgáltatásokat (AD FS)](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Csatlakozás a fürthöz

Most már készen áll a fürthöz való csatlakozáshoz. A fő tekintheti meg az erőforráscsoportot, és nevű `k8s-master-<sequence-of-numbers>`. Használja az SSH-ügyfelet a főcsomóponthoz való kapcsolódáshoz. A fő használhatja **kubectl**, a fürt kezeléséhez a Kubernetes parancssori ügyfelét. Útmutatásért lásd: [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Azt is tapasztalhatja a **Helm** Csomagkezelő hasznos telepítéséhez és alkalmazások telepítése a fürtön. Telepítéséről és használatáról a Helm a fürttel kapcsolatos utasításokért lásd: [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>További lépések

[A Kubernetes-irányítópult engedélyezése](azure-stack-solution-template-kubernetes-dashboard.md)

[Kubernetes felvétele a Marketplace-en (az Azure Stack-operátorokról)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure Active Directory (Azure AD) használatával az Azure Stack üzembe helyezése Kubernetes](azure-stack-solution-template-kubernetes-azuread.md)

[Az Active Directory összevonási szolgáltatásokban (AD FS) használatával az Azure Stack üzembe helyezése Kubernetes](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes az Azure-ban](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
