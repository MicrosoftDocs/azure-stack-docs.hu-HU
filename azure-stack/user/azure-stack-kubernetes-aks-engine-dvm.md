---
title: A Marketplace-elem fürtjének áthelyezése az AK-motorba Azure Stack hub-on
description: Megtudhatja, hogyan helyezheti át Marketplace-elemeit a Azure Stack hub AK-beli motorján.
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: f15c870a1b256ffa546672a3abde2fc68f9baa4f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "80069010"
---
# <a name="move-your-marketplace-item-cluster-to-the-aks-engine-on-azure-stack-hub"></a>A Marketplace-elem fürtjének áthelyezése az AK-motorba Azure Stack hub-on

A Kubernetes Azure Stack hub Marketplace-elem egy Azure Resource Manager sablonnal helyez üzembe egy üzembe helyezési virtuális gépet (VM) az AK-motor letöltéséhez és telepítéséhez, valamint a fürt leírásához használt bemeneti API-modell létrehozásához, miután a virtuális hálózati vezérlő fut a virtuális gépen és a telepített fürtön. Ebből a cikkből megtudhatja, hogyan érheti el az AK-motort és a hozzá tartozó fájlokat, így a segítségével frissítheti és méretezheti a Kubernetes-fürtön található műveleteket.

## <a name="access-aks-engine-in-the-dvm"></a>Az AK-motor elérése a DVM

Miután a Kubernetes Azure Stack hub Marketplace-elem sikeresen kezdeményezte az üzembe helyezést, megtalálhatja a fürthöz megadott erőforráscsoporthoz telepített üzembe helyező virtuális gépen a fürt üzembe helyezéséhez használt AK-motort, ez a virtuális gép nem része a Kubernetes-fürtnek, hanem a saját VNet jön létre. A virtuális gép megtalálásának és a benne található AK-motor megkeresésének lépései:

1.  Nyissa meg az Azure Stack hub felhasználói portált, és keresse meg a Kubernetes-fürthöz megadott erőforráscsoportot.
2.  Az erőforráscsoporthoz keresse meg az üzembe helyezési virtuális gépet. A név az előtaggal kezdődik: **VMD-**.
3.  Válassza ki az üzembe helyezési virtuális gépet. Az Áttekintés területen * * keresse meg a nyilvános IP-címet. Használja ezt a internetcímet és a konzolos alkalmazást, például a PuTTY-t egy SSH-munkamenet létrehozásához a virtuális géphez.
4.  Az üzembe helyezési virtuális gépen lévő munkamenetben a következő elérési úton találja meg az AK-motort:`./var/lib/waagent/custom-script/download/0/bin/aks-engine`
5.  Keresse meg `.json` az AK-motorba bemenetként használt fürtöket leíró fájlt. A fájlt a következő `/var/lib/waagent/custom-script/download/0/bin/azurestack.json`helyen:. Vegye figyelembe, hogy a fájl rendelkezik a fürt üzembe helyezéséhez használt egyszerű szolgáltatás hitelesítő adataival. Ha úgy dönt, hogy megőrzi a fájlt, ügyeljen arra, hogy a fájlt egy védett tárolóba helyezze át.
6.  Keresse meg az AK-motor által létrehozott kimeneti könyvtárat `/var/lib/waagent/custom-script/download/0/_output/<resource group name>`a következő helyen:. Ebben a könyvtárban keresse meg a kimenetet `apimodel.json` az elérési úton. `/var/lib/waagent/custom-script/download/0/bin/apimodel.json` A könyvtár és `apimodel.json` a fájl tartalmazza az összes generált tanúsítványt, kulcsot és a Kubernetes-fürt üzembe helyezéséhez szükséges hitelesítő adatokat. Tárolja biztonságos helyen ezeket az erőforrásokat.
7.  Keresse meg a Kubernetes konfigurációs fájlját, amelyet gyakran **kubeconfig** -fájlként neveznek, az `$HOME/<output dir>/kubeconfig/kubeconfing.<location>.json`elérési úton, ahol ** \<a hely>** megfelel az Azure stack hub-hely azonosítójának. Ez a fájl akkor lehet hasznos, ha be szeretné állítani a **kubectl** -t a Kubernetes-fürt eléréséhez.

## <a name="use-the-aks-engine-with-your-newly-created-cluster"></a>Az AK-motor használata az újonnan létrehozott fürttel

Miután megtalálta az AK-motort, a bemeneti apimodel. JSON fájlt, a kimeneti könyvtárat és a kimeneti apimodel. JSON fájlt, tárolja őket biztonságos helyen, az AK-motor bináris fájlját és `apimodel.json` kimenetét bármely linuxos virtuális gépen használhatja.

1.  Az AK-motor használatának folytatásához, például a **frissítéshez** és a **méretezéshez**, másolja az **AK-motor** bináris fájlját a célszámítógépen. Ha ugyanazt a **VMD-** gépet használja egy címtárhoz.

2.  Hozzon létre egy könyvtárat annak a fürtnek vagy más mnemotechnic a nevével, amely az új fürtre hivatkozik, és mentse a kimeneti apimodel. JSON fájlt. Győződjön meg arról, hogy védett hely, mivel a fájl hitelesítő adatokat tartalmaz. Ezután futtathatja az AK-motort olyan műveletek futtatásához, mint a [Méretezés](azure-stack-kubernetes-aks-engine-scale.md) vagy a [frissítés](azure-stack-kubernetes-aks-engine-upgrade.md) .

## <a name="next-steps"></a>További lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)  
- [Az AK-motor hibáinak megoldása Azure Stack hub-on](azure-stack-kubernetes-aks-engine-troubleshoot.md)  

