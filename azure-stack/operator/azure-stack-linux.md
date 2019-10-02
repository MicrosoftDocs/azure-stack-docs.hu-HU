---
title: Linuxos rendszerképek hozzáadása Azure Stackhoz
description: Megtudhatja, hogyan adhat hozzá linuxos lemezképeket Azure Stackhoz.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 309d3d7185bd225f58691d4996ba649e8df7b97a
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714615"
---
# <a name="add-linux-images-to-azure-stack"></a>Linuxos rendszerképek hozzáadása Azure Stackhoz

*Vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Linux rendszerű virtuális gépeket úgy helyezhet üzembe az Azure Stackben, hogy hozzáad egy Linux-alapú rendszerképet az Azure Stack-piactérhez. A leggyorsabban a Piactérkezelőn keresztül adhat hozzá Linux rendszerképet az Azure Stackhez. Ezek a rendszerképek elő lettek készítve az Azure Stackben történő használathoz, és ellenőrizve lett a kompatibilitásuk.

## <a name="marketplace-management"></a>Piactér-kezelés

Ha Linux-lemezképeket szeretne letölteni az Azure Marketplace-ről, használja a [Marketplace-elemek letöltése az Azure-ból Azure stack](azure-stack-download-azure-marketplace-item.md) cikk eljárásait. Válassza ki azokat a Linux-lemezképeket, amelyeken a felhasználók számára biztosítani szeretné a Azure Stack.

Ezek a lemezképek gyakran frissülnek, ezért a piactér-felügyeletet gyakran érdemes naprakészen tartani.

## <a name="prepare-your-own-image"></a>Saját rendszerkép előkészítése

Amikor lehetséges, a Marketplace-kezelőből töltse le az elérhető rendszerképeket. Ezek a rendszerképek elő lettek készítve az Azure Stackben történő használathoz, és tesztelésen estek át.

### <a name="azure-linux-agent"></a>Azure Linux-ügynök

Az Azure Linux-ügynököt (általában **WALinuxAgent** vagy **WALinuxAgent**) kötelező megadni, és az ügynök nem minden verziója működik Azure stackon. A 2.2.20 és a 2.2.35 közötti verziók nem támogatottak Azure Stackon. A legújabb ügynök-verziók 2.2.35 való használatához alkalmazza a 1901-es gyorsjavítást/1902-es gyorsjavítást, vagy frissítse a Azure Stack a 1903-es kiadásra (vagy újabb verzióra). Vegye figyelembe, hogy jelenleg nem támogatott a [Cloud-init](https://cloud-init.io/) Azure stack.

| Azure Stack Build | Azure Linux-ügynök létrehozása |
| ------------- | ------------- |
| 1.1901.0.99 vagy korábbi | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 vagy újabb |
| 1.1902.2.73  | 2.2.35 vagy újabb |
| 1.1903.0.35  | 2.2.35 vagy újabb |
| 1903 utáni buildek | 2.2.35 vagy újabb |
| Nem támogatott | 2.2.21-2.2.34 |

Készítse elő saját linuxos rendszerképét a következő utasítások használatával:

* [CentOS-alapú disztribúciók](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES és openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>Rendszerkép hozzáadása a piactérhez

Kövesse a [rendszerkép hozzáadása a piactérhez](azure-stack-add-vm-image.md)című témakört. Győződjön meg arról, hogy a `OSType` paraméter értéke `Linux`.

Miután hozzáadta a lemezképet a piactérhez, létrejön egy Marketplace-elem, és a felhasználók telepíthetnek egy linuxos virtuális gépet.

## <a name="next-steps"></a>További lépések

* [Azure Marketplace-elemek letöltése az Azure-ból az Azure Stackbe](azure-stack-download-azure-marketplace-item.md)
* [Azure Stack Marketplace – áttekintés](azure-stack-marketplace.md)
