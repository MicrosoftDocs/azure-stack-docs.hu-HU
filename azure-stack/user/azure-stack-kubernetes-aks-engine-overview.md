---
title: Mi a Azure Stack AK-motorja? | Microsoft Docs
description: Ismerje meg, hogyan helyezhet üzembe és kezelhet egy Kubernetes-fürtöt az Azure-ban és a Azure Stackban az AK motor parancssori eszköz használatával.
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
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 93a835b6d3eff233ccbd421930f9618325126ea4
ms.sourcegitcommit: 58e1911a54ba249a82fa048c7798dadedb95462b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73057773"
---
# <a name="what-is-the-aks-engine-on-azure-stack"></a>Mi a Azure Stack AK-motorja?

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

A Kubernetes-fürtök Azure Stack Azure-beli üzembe helyezéséhez és kezeléséhez használhatja az KABAi motor parancssori eszközét is. A Azure Resource Manager natív fürtök létrehozásához, frissítéséhez és méretezéséhez használja az AK motort. A motorral a csatlakoztatott és a leválasztott környezetekben is telepíthet fürtöt. Ez a cikk áttekintést nyújt az AK-motorról, a motor és a Azure Stack használatának támogatott forgatókönyveit, valamint az olyan műveletek bevezetését, mint például a telepítés, a frissítés és a skálázás.

> [!IMPORTANT]
> Az AK-motor jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-the-aks-engine"></a>Az AK-motor áttekintése

Az alKuberneteson- [motor](https://github.com/Azure/aks-engine) egy parancssori eszközt biztosít az Azure-beli és Azure stack-alapú rendszerindítási fürtökhöz. A Azure Resource Manager használatával az KABAi motor segítségével virtuális gépeken, virtuális hálózatokon és más infrastruktúra-szolgáltatásbeli (IaaS) erőforrásokon futó fürtöket hozhat létre és kezelhet Azure Stackokban.

## <a name="aks-engine-on-azure-stack-considerations"></a>AK-motor Azure Stack megfontolások alapján

Mielőtt a Azure Stack az AK-motort használja, fontos megérteni a Azure Stack és az Azure közötti különbségeket. Ez a szakasz a Kubernetes-fürt felügyeletéhez használt Azure Stack és a fő szempontokat ismerteti.

További információ a Azure Stack AK-motorjának jellemzőiről, valamint az Azure-ra vonatkozó eltérésekről: az [alAzure Stackn](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md)található az AK-motor.

## <a name="supported-scenarios-with-the-aks-engine"></a>Támogatott forgatókönyvek az AK-motorral

A Azure Stack támogatási csapat az alábbi forgatókönyveket támogatja:

1.  Az AK-motor az ebben a dokumentációban található irányelveket követve üzembe helyezi a fürt összes összetevőjét, és a [következő sablont](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack)használja.
2.  Az AK-motor üzembe helyezi a fürtöt egy meglévő VNET. További információ: [Egyéni virtuális hálózat használata az Kabai motorral](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md).
3.  [Frissítési](azure-stack-kubernetes-aks-engine-upgrade.md) és [méretezési](azure-stack-kubernetes-aks-engine-scale.md) műveletek.

További információ az AK-motorról és a Azure Stackről: az [AK-motorra vonatkozó támogatási szabályzatok Azure stack](azure-stack-kubernetes-aks-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Az AK-motor telepítése és a Kubernetes-fürt üzembe helyezése

Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stackon:

1. [Az AK-motor előfeltételeinek beállítása](azure-stack-kubernetes-aks-engine-set-up.md)
2. Telepítse az AK-motort egy olyan gépre, amely hozzáfér a Azure Stack-környezetéhez.
     - [Az AK-motor telepítése Windows rendszeren Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Az KABAi motor telepítése Linux rendszeren Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Kubernetes-fürt üzembe helyezése az AK-motorral Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az AK-motor előfeltételeinek beállítása](azure-stack-kubernetes-aks-engine-set-up.md)