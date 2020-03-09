---
title: A Kubernetes telepítése Azure Stack hub-tárolók használatára
description: Megtudhatja, hogyan helyezhet üzembe Kubernetes a tárolók Azure Stack hubhoz való használatához.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 5fa9c506b2e030adbf521191a623579f56f1ae0f
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364360"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack-hub"></a>A Kubernetes üzembe helyezése a tárolók Azure Stack hubhoz való használatához

> [!Note]  
> A fürtök Kubernetes való üzembe helyezése csak a Azure Stack Marketplace-elemmel használható. Azure Stack támogatott Kubernetes-fürtök esetében használja [az AK-motort](azure-stack-kubernetes-aks-engine-overview.md).

A cikk lépéseit követve üzembe helyezheti és állíthatja be a Kubernetes erőforrásait egyetlen, koordinált műveletben. A lépések Azure Resource Manager megoldási sablont használnak. Össze kell gyűjtenie az Azure Stack hub telepítésére vonatkozó szükséges információkat, elő kell állítania a sablont, majd üzembe kell helyeznie a felhőben. Az Azure Stack hub-sablon nem ugyanazt a felügyelt AK-szolgáltatást használja, mint a globális Azure-ban.

## <a name="kubernetes-and-containers"></a>Kubernetes és tárolók

A Kubernetes a Azure Stack hub-ban található AK-motor által generált Azure Resource Manager-sablonok használatával telepítheti. A [Kubernetes](https://kubernetes.io) egy nyílt forráskódú rendszer a tárolókban lévő alkalmazások üzembe helyezésének, méretezésének és kezelésének automatizálására. Egy [tároló](https://www.docker.com/what-container) egy rendszerképben található. A tároló képe hasonló a virtuális géphez (VM), azonban a virtuális gépektől eltérően a tároló csak az alkalmazás futtatásához szükséges erőforrásokat tartalmazza, például a kódot, a futtatókörnyezetet, hogy végrehajtsa a kódot, adott könyvtárakat és beállításokat.

A Kubernetes a következőre használhatja:

- Rugalmasan méretezhető, bővíthető, másodpercek alatt telepíthető alkalmazásokat fejleszthet. 
- Egyszerűsítse az alkalmazás kialakítását, és javítsa a megbízhatóságát különböző Helm-alkalmazásokkal. A [Helm](https://github.com/kubernetes/helm) egy nyílt forráskódú csomagolási eszköz, amely segítséget nyújt a Kubernetes-alkalmazások életciklusának telepítéséhez és kezeléséhez.
- Egyszerűen figyelheti és diagnosztizálhatja alkalmazásai állapotát.

Csak a fürtöt támogató csomópontok által igényelt számítási használatért kell fizetnie. További információ: [használat és számlázás Azure stack központban](../operator/azure-stack-billing-and-chargeback.md).

## <a name="deploy-kubernetes-to-use-containers"></a>Kubernetes üzembe helyezése tárolók használatához

A Kubernetes-fürt Azure Stack hub-on való üzembe helyezésének lépései a személyazonosság-kezelési szolgáltatástól függenek. Ellenőrizze az Azure Stack hub telepítése által használt identitáskezelési megoldást. Forduljon az Azure Stack hub rendszergazdájához, és ellenőrizze az Identity Management szolgáltatást.

- **Azure Active Directory (Azure AD)**  
A fürt Azure AD-vel történő telepítésével kapcsolatos útmutatásért lásd: [Kubernetes telepítése Azure stack hubhoz Azure Active Directory (Azure ad) használatával](azure-stack-solution-template-kubernetes-azuread.md).

- **Active Directory összevont szolgáltatások (AD FS)**  
A fürt AD FS használatával történő telepítésével kapcsolatos útmutatásért lásd: a [Kubernetes telepítése Azure stack hubhoz Active Directory összevont szolgáltatások (AD FS) használatával](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Kapcsolódás a fürthöz

Most már készen áll a fürthöz való kapcsolódásra. A főkiszolgáló a fürterőforrás-csoportban található, és a neve `k8s-master-<sequence-of-numbers>`. Használjon SSH-ügyfelet a főkiszolgálóhoz való kapcsolódáshoz. A főkiszolgálón használhatja a **kubectl**, a Kubernetes parancssori ügyfelet a fürt felügyeletéhez. Útmutatásért lásd: [Kubernetes.IO](https://kubernetes.io/docs/reference/kubectl/overview).

A **Helm** csomagkezelő is hasznos lehet az alkalmazások fürtön való telepítéséhez és üzembe helyezéséhez. A Helm fürthöz való telepítésével és használatával kapcsolatos utasításokért lásd: [Helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Következő lépések

[A Kubernetes irányítópult engedélyezése](azure-stack-solution-template-kubernetes-dashboard.md)

[Kubernetes hozzáadása a piactérhez (Azure Stack hub-kezelőhöz)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes üzembe helyezése Azure Stack hubhoz Azure Active Directory (Azure AD) használatával](azure-stack-solution-template-kubernetes-azuread.md)

[Kubernetes üzembe helyezése Azure Stack hubhoz Active Directory összevont szolgáltatások (AD FS) használatával](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes az Azure-ban](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
