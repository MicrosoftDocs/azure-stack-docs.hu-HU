---
title: A Kubernetes telepítése Azure Stack tárolók használatára | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe Kubernetes a tárolók Azure Stack használatával történő használatához.
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
ms.openlocfilehash: 74cad0b1f41c5c764bef361f3f521162eec59198
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277654"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>A Kubernetes üzembe helyezése tárolók használatára Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

> [!Note]  
> A Azure Stack Kubernetes előzetes verzióban érhető el. Az előzetes verzió jelenleg nem támogatja Azure Stack leválasztott forgatókönyv használatát. Csak fejlesztési és tesztelési forgatókönyvek esetén használja a Piactéri elemeket.

A cikk lépéseit követve üzembe helyezheti és állíthatja be a Kubernetes erőforrásait egyetlen, koordinált műveletben. A lépések Azure Resource Manager megoldási sablont használnak. Össze kell gyűjtenie a szükséges információkat a Azure Stack telepítéséről, elő kell állítania a sablont, majd üzembe kell helyeznie a felhőben. A Azure Stack sablon nem ugyanazt a felügyelt AK-szolgáltatást használja, mint a globális Azure-ban.

## <a name="kubernetes-and-containers"></a>Kubernetes és tárolók

A Kubernetes a Azure Stack AK-motor által létrehozott Azure Resource Manager sablonok használatával telepítheti. A [Kubernetes](https://kubernetes.io) egy nyílt forráskódú rendszer a tárolókban lévő alkalmazások üzembe helyezésének, méretezésének és kezelésének automatizálására. Egy [tároló](https://www.docker.com/what-container) egy rendszerképben található. A tároló képe hasonló a virtuális géphez (VM), azonban a virtuális gépektől eltérően a tároló csak az alkalmazás futtatásához szükséges erőforrásokat tartalmazza, például a kódot, a futtatókörnyezetet, hogy végrehajtsa a kódot, adott könyvtárakat és beállításokat.

A Kubernetes a következőre használhatja:

- Nagy mértékben méretezhető, nem alkalmazható, másodpercek alatt üzembe helyezhető alkalmazások fejlesztése. 
- Egyszerűsítse az alkalmazás kialakítását, és javítsa a megbízhatóságát különböző Helm-alkalmazásokkal. A [Helm](https://github.com/kubernetes/helm) egy nyílt forráskódú csomagolási eszköz, amely segítséget nyújt a Kubernetes-alkalmazások életciklusának telepítéséhez és kezeléséhez.
- Egyszerűen figyelheti és diagnosztizálhatja alkalmazásai állapotát.

Csak a fürtöt támogató csomópontok által igényelt számítási használatért kell fizetnie. További információ: [használat és számlázás Azure Stackban](../operator/azure-stack-billing-and-chargeback.md).

## <a name="deploy-kubernetes-to-use-containers"></a>Kubernetes üzembe helyezése tárolók használatához

A Kubernetes-fürtök Azure Stack-on való üzembe helyezésének lépései az Identity Management szolgáltatástól függenek. Győződjön meg arról, hogy a Azure Stack-példánya által használt Identitáskezelés-felügyeleti megoldás. Forduljon a Azure Stack rendszergazdájához, és ellenőrizze az Identity Management szolgáltatást.

- **Azure Active Directory (Azure AD)**  
A fürt Azure AD-vel történő telepítésével kapcsolatos útmutatásért lásd: [Kubernetes telepítése Azure Stack Azure Active Directory (Azure ad) használatával](azure-stack-solution-template-kubernetes-azuread.md).

- **Active Directory összevont szolgáltatások (AD FS)**  
A fürt AD FS használatával történő telepítésével kapcsolatos útmutatásért lásd: a [Kubernetes telepítése Azure Stack Active Directory összevont szolgáltatások (AD FS) használatával](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Kapcsolódás a fürthöz

Most már készen áll a fürthöz való kapcsolódásra. A főkiszolgáló a fürterőforrás-csoportban található, és a neve `k8s-master-<sequence-of-numbers>`. Használjon SSH-ügyfelet a főkiszolgálóhoz való kapcsolódáshoz. A főkiszolgálón használhatja a **kubectl**, a Kubernetes parancssori ügyfelet a fürt felügyeletéhez. Útmutatásért lásd: [Kubernetes.IO](https://kubernetes.io/docs/reference/kubectl/overview).

A **Helm** csomagkezelő is hasznos lehet az alkalmazások fürtön való telepítéséhez és üzembe helyezéséhez. A Helm fürthöz való telepítésével és használatával kapcsolatos utasításokért lásd: [Helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Következő lépések

[A Kubernetes irányítópult engedélyezése](azure-stack-solution-template-kubernetes-dashboard.md)

[Kubernetes hozzáadása a piactérhez (Azure Stack operátorhoz)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes üzembe helyezése Azure Stack Azure Active Directory (Azure AD) használatával](azure-stack-solution-template-kubernetes-azuread.md)

[Kubernetes üzembe helyezése Azure Stack Active Directory összevont szolgáltatások (AD FS) használatával](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes az Azure-ban](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
