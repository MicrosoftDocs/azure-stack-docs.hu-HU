---
title: Mi a Azure Stack hub AK-motorja? | Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe és kezelhet Kubernetes-fürtöt az Azure-ban és Azure Stack hub-ban az AK motor parancssori eszköz használatával.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 1071bd3a1bda2b5a50f5ba963ff8927cf92734e5
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820168"
---
# <a name="what-is-the-aks-engine-on-azure-stack-hub"></a>Mi a Azure Stack hub AK-motorja?

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack Development Kit*

A Kubernetes-fürtök Azure-beli és Azure Stack hub-ban való üzembe helyezéséhez és kezeléséhez használhatja az AK motor parancssori eszközét. A Azure Resource Manager natív fürtök létrehozásához, frissítéséhez és méretezéséhez használja az AK motort. A motorral a csatlakoztatott és a leválasztott környezetekben is telepíthet fürtöt. Ez a cikk áttekintést nyújt az AK-motorról, a motor és a Azure Stack hub használatának támogatott forgatókönyveit, valamint az olyan műveletek bevezetését, mint például a telepítés, a frissítés és a skálázás.

## <a name="overview-of-the-aks-engine"></a>Az AK-motor áttekintése

Az alKuberneteson- [motor](https://github.com/Azure/aks-engine) parancssori eszközt biztosít az Azure-beli és Azure stack hub-beli rendszerindítási fürtökhöz. A Azure Resource Manager használatával az AK-motor segítségével virtuális gépeken, virtuális hálózatokon és más infrastruktúra-szolgáltatásbeli (IaaS) erőforrásokon futó fürtöket hozhat létre és kezelhet Azure Stack hub-ban.

## <a name="aks-engine-on-azure-stack-hub-considerations"></a>AK-motor Azure Stack hub-megfontolásokhoz

Az AK-motor Azure Stack hub-on való használata előtt fontos megérteni az Azure Stack hub és az Azure közötti különbségeket. Ez a szakasz a Kubernetes-fürt felügyeletéhez használt Azure Stack hub és az AK-motor használatával különböző funkciókat és kulcsfontosságú szempontokat tartalmaz.

További információ a Azure Stack hub AK-motorjának adatairól, valamint az Azure-ra vonatkozó eltérésekről: az [Azure stack hub-on található AK-motor](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md).

## <a name="supported-scenarios-with-the-aks-engine"></a>Támogatott forgatókönyvek az AK-motorral

Az Azure Stack hub támogatási csapata a következő forgatókönyveket támogatja:

1.  Az AK-motor az ebben a dokumentációban található irányelveket követve üzembe helyezi a fürt összes összetevőjét, és a [következő sablont](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack)használja.
2.  Az AK-motor üzembe helyezi a fürtöt egy meglévő VNET. További információ: [Egyéni virtuális hálózat használata az Kabai motorral](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md).
3.  [Frissítési](azure-stack-kubernetes-aks-engine-upgrade.md) és [méretezési](azure-stack-kubernetes-aks-engine-scale.md) műveletek.

További információ az AK-motorról és a Azure Stack hub-ról: a [Azure stack hub-os motor támogatási szabályzatai](azure-stack-kubernetes-aks-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Az AK-motor telepítése és a Kubernetes-fürt üzembe helyezése

Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack hub-on:

1. [Az AK-motor előfeltételeinek beállítása](azure-stack-kubernetes-aks-engine-set-up.md)
2. Telepítse az AK-motort egy olyan gépre, amely hozzáfér az Azure Stack hub-környezetéhez.
     - [Az AK-motor telepítése a Windows rendszerű Azure Stack központban](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Az AK-motor telepítése Linux rendszeren Azure Stack hub-ban](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack hub-on](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az AK-motor előfeltételeinek beállítása](azure-stack-kubernetes-aks-engine-set-up.md)