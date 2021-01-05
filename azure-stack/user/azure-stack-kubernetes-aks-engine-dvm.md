---
title: Marketplace-elem fürtjének áthelyezése az AK-motorba Azure Stack hub-on
description: Megtudhatja, hogyan helyezheti át Marketplace-elemeit a Azure Stack hub AK-beli motorján.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 112862f5e73806d2e73d809b70e4bd7fd2e315b5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874183"
---
# <a name="move-your-marketplace-item-cluster-to-the-aks-engine-on-azure-stack-hub"></a>A Marketplace-elem fürtjének áthelyezése az AK-motorba Azure Stack hub-on

A Kubernetes Azure Stack hub Marketplace-elem egy Azure Resource Manager sablonnal helyez üzembe egy üzembe helyezési virtuális gépet (VM) az AK-motor letöltéséhez és telepítéséhez, valamint a fürt leírásához használt bemeneti API-modell létrehozásához, miután a virtuális hálózati vezérlő fut a virtuális gépen és a telepített fürtön. Ebből a cikkből megtudhatja, hogyan érheti el az AK-motort és a hozzá tartozó fájlokat, így a segítségével frissítheti és méretezheti a Kubernetes-fürtön található műveleteket.

## <a name="access-aks-engine-in-the-dvm"></a>Az AK-motor elérése a DVM

Miután a Kubernetes Azure Stack hub Marketplace-elem sikeresen kezdeményezte az üzembe helyezést, megtalálhatja a fürthöz megadott erőforráscsoporthoz telepített üzembe helyező virtuális gépen a fürt üzembe helyezéséhez használt AK-motort, ez a virtuális gép nem része a Kubernetes-fürtnek, hanem a saját VNet jön létre. A virtuális gép megtalálásának és a benne található AK-motor megkeresésének lépései:

1.  Nyissa meg az Azure Stack hub felhasználói portált, és keresse meg a Kubernetes-fürthöz megadott erőforráscsoportot.
2.  Az erőforráscsoporthoz keresse meg az üzembe helyezési virtuális gépet. A név az előtaggal kezdődik: **VMD-**.
3.  Válassza ki az üzembe helyezési virtuális gépet. Az Áttekintés területen * * keresse meg a nyilvános IP-címet. Használja ezt a internetcímet és a konzolos alkalmazást, például a PuTTY-t egy SSH-munkamenet létrehozásához a virtuális géphez.
4.  Az üzembe helyezési virtuális gépen lévő munkamenetben a következő elérési úton találja meg az AK-motort: `./var/lib/waagent/custom-script/download/0/bin/aks-engine`
5.  Keresse meg az `.json` AK-motorba bemenetként használt fürtöket leíró fájlt. A fájlt a következő helyen: `/var/lib/waagent/custom-script/download/0/bin/azurestack.json` . Vegye figyelembe, hogy a fájl rendelkezik a fürt üzembe helyezéséhez használt egyszerű szolgáltatás hitelesítő adataival. Ha úgy dönt, hogy megőrzi a fájlt, ügyeljen arra, hogy a fájlt egy védett tárolóba helyezze át.
6.  Keresse meg az AK-motor által létrehozott kimeneti könyvtárat a következő helyen: `/var/lib/waagent/custom-script/download/0/_output/<resource group name>` . Ebben a könyvtárban keresse meg a kimenetet `apimodel.json` az elérési úton `/var/lib/waagent/custom-script/download/0/bin/apimodel.json` . A könyvtár és a `apimodel.json` fájl tartalmazza az összes generált tanúsítványt, kulcsot és a Kubernetes-fürt üzembe helyezéséhez szükséges hitelesítő adatokat. Tárolja biztonságos helyen ezeket az erőforrásokat.
7.  Keresse meg a Kubernetes konfigurációs fájlját, amelyet gyakran **kubeconfig** -fájlként neveznek, az elérési úton `/var/lib/waagent/custom-script/download/0/_output/k8smpi00/kubeconfig/kubeconfig.<location>.json` , ahol a **\<location>**  Azure stack hub-hely azonosítójának felel meg. Ez a fájl akkor lehet hasznos, ha be szeretné állítani a **kubectl** -t a Kubernetes-fürt eléréséhez.


## <a name="use-the-aks-engine-with-your-newly-created-cluster"></a>Az AK-motor használata az újonnan létrehozott fürttel

Miután megtalálta az AK-motort, a fájlra, a kimeneti könyvtárra és a kimeneti apimodel.jsre apimodel.jsbemeneti adatokat, tárolja azokat biztonságos helyen, az AK-motor bináris fájljait és kimenetét `apimodel.json` bármely linuxos virtuális gépen használhatja.

1.  Az AK-motor használatának folytatásához, például a **frissítéshez** és a **méretezéshez**, másolja az **AK-motor** bináris fájlját a célszámítógépen. Ha ugyanazt a **VMD-** gépet használja egy címtárhoz.

2.  Hozzon létre egy könyvtárat annak a fürtnek vagy más mnemotechnic a nevével, amely az új fürtre hivatkozik, és mentse a kimeneti apimodel.jsa fájlba. Győződjön meg arról, hogy védett hely, mivel a fájl hitelesítő adatokat tartalmaz. Ezután futtathatja az AK-motort olyan műveletek futtatásához, mint a [Méretezés](azure-stack-kubernetes-aks-engine-scale.md) vagy a [frissítés](azure-stack-kubernetes-aks-engine-upgrade.md) .

## <a name="next-steps"></a>További lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)  
- [Az AK-motor hibáinak megoldása Azure Stack hub-on](azure-stack-kubernetes-aks-engine-troubleshoot.md)  
