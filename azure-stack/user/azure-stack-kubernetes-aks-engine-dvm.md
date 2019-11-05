---
title: A Marketplace-elem fürtjének áthelyezése a Azure Stack AK-motorba | Microsoft Docs
description: Megtudhatja, hogyan helyezheti át Marketplace-elemeit a Azure Stack.
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
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/29/2019
ms.openlocfilehash: 4236f1dff939621aa391927341c0ee1136a64b58
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595313"
---
# <a name="move-your-marketplace-item-cluster-to-the-aks-engine-on-azure-stack"></a>A Marketplace-elem fürtjének áthelyezése az AK-motorba Azure Stack

A Kubernetes Azure Stack Marketplace-elem egy Azure Resource Manager sablonnal helyez üzembe egy üzembe helyezési virtuális gépet (VM) az AK-motor letöltéséhez és telepítéséhez, valamint a fürt leírásához használt bemeneti API-modell létrehozásához, miután az AK-motor fut a virtuális gépen és az üzembe helyezett fürtöt. Ebből a cikkből megtudhatja, hogyan érheti el az AK-motort és a hozzá tartozó fájlokat, így a segítségével frissítheti és méretezheti a Kubernetes-fürtön található műveleteket.

## <a name="access-aks-engine-in-the-dvm"></a>Az AK-motor elérése a DVM

Ha a Kubernetes-Azure Stack Marketplace-elem által kezdeményezett telepítést sikeresen befejezte, megtalálhatja a fürthöz megadott erőforráscsoporthoz telepített üzembe helyező virtuális gépen a fürt üzembe helyezéséhez használt AK-motort, ez a virtuális gép nem része a következőnek: a Kubernetes-fürt a saját VNet jön létre. A virtuális gép megtalálásának és a benne található AK-motor megkeresésének lépései:

1.  Nyissa meg a Azure Stack felhasználói portált, és keresse meg a Kubernetes-fürthöz megadott erőforráscsoportot.
2.  Az erőforráscsoporthoz keresse meg az üzembe helyezési virtuális gépet. A név az előtaggal kezdődik: **VMD-** .
3.  Válassza ki az üzembe helyezési virtuális gépet. Az Áttekintés területen * * keresse meg a nyilvános IP-címet. Használja ezt a internetcímet és a konzolos alkalmazást, például a PuTTY-t egy SSH-munkamenet létrehozásához a virtuális géphez.
4.  Az üzembe helyezési virtuális gépen lévő munkamenetben a következő elérési úton találja meg az AK-motort: `./var/lib/waagent/custom-script/download/0/bin/aks-engine`
5.  Keresse meg az AK-motorba bemenetként használt fürtöket leíró `.json` fájlt. A fájl `/var/lib/waagent/custom-script/download/0/bin/azurestack.json`. Vegye figyelembe, hogy a fájl rendelkezik a fürt üzembe helyezéséhez használt egyszerű szolgáltatás hitelesítő adataival. Ha úgy dönt, hogy megőrzi a fájlt, ügyeljen arra, hogy a fájlt egy védett tárolóba helyezze át.
6.  Keresse meg az AK-motor által létrehozott kimeneti könyvtárat a következő címen: `/var/lib/waagent/custom-script/download/0/_output/<resource group name>`. Ebben a könyvtárban keresse meg a kimeneti `apimodel.json` a Path `/var/lib/waagent/custom-script/download/0/bin/apimodel.json`. A könyvtár és a `apimodel.json` fájl tartalmazza a Kubernetes-fürt üzembe helyezéséhez szükséges összes generált tanúsítványt, kulcsot és hitelesítő adatot. Tárolja biztonságos helyen ezeket az erőforrásokat.
7.  Keresse meg a Kubernetes konfigurációs fájlját, amelyet gyakran **kubeconfig** -fájlként neveznek, az elérési úton:, ahol az megfelel a Azure stack hely azonosítójának. Ez a fájl akkor lehet hasznos, ha be szeretné állítani a **kubectl** -t a Kubernetes-fürt eléréséhez.

## <a name="use-the-aks-engine-with-your-newly-created-cluster"></a>Az AK-motor használata az újonnan létrehozott fürttel

Miután megtalálta az AK-motort, a bemeneti apimodel. JSON fájlt, a kimeneti könyvtárat és a kimeneti apimodel. JSON fájlt, tárolja azokat biztonságos helyen, az AK-motor bináris fájljait és a kimeneti `apimodel.json` bármely linuxos virtuális gépen használhatja.

1.  Az AK-motor használatának folytatásához, például a **frissítéshez** és a **méretezéshez**, másolja az **AK-motor** bináris fájlját a célszámítógépen. Ha ugyanazt a **VMD-** gépet használja egy címtárhoz.

2.  Hozzon létre egy könyvtárat annak a fürtnek vagy más mnemotechnic a nevével, amely az új fürtre hivatkozik, és mentse a kimeneti apimodel. JSON fájlt. Győződjön meg arról, hogy védett hely, mivel a fájl hitelesítő adatokat tartalmaz. Ezután futtathatja az AK-motort olyan műveletek futtatásához, mint a [Méretezés](azure-stack-kubernetes-aks-engine-scale.md) vagy a [frissítés](azure-stack-kubernetes-aks-engine-upgrade.md) .

## <a name="next-steps"></a>További lépések

- További információ a [Azure stack AK-beli motorról](azure-stack-kubernetes-aks-engine-overview.md)  
- [Az AK-motor hibáinak megoldása Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)  

