---
title: Linux-lemezképek hozzáadása az Azure Stackhez
description: Ismerje meg, hogyan adhat hozzá a Linux-rendszerképeket az Azure Stackhez.
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
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 40a60c5207494ae70ccdfd051c8a223493b704c5
ms.sourcegitcommit: 6fcd5df8b77e782ef72f0e1419f1f75ec8c16c04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991110"
---
# <a name="add-linux-images-to-azure-stack"></a>Linux-lemezképek hozzáadása az Azure Stackhez

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Linux rendszerű virtuális gépeket úgy helyezhet üzembe az Azure Stackben, hogy hozzáad egy Linux-alapú rendszerképet az Azure Stack-piactérhez. A leggyorsabban a Piactérkezelőn keresztül adhat hozzá Linux rendszerképet az Azure Stackhez. Ezek a rendszerképek elő lettek készítve az Azure Stackben történő használathoz, és ellenőrizve lett a kompatibilitásuk.

## <a name="marketplace-management"></a>Marketplace-en kezelése

Töltse le a Linux-rendszerképeket az Azure Marketplace-ről, kövesse a a [Piactéri termékek letöltése az Azure-ból az Azure Stackhez](azure-stack-download-azure-marketplace-item.md) cikk. Válassza ki, hogy a felhasználók számára az Azure Stack kívánt Linux-lemezképekhez.

Gyakori frissítések érhetők el ezeket a lemezképeket, ezért érdemes ellenőrizni a Marketplace-en felügyeleti gyakran és naprakész állapotban tarthatja.

## <a name="prepare-your-own-image"></a>A saját lemezkép előkészítése

Amikor csak lehetséges, töltse le a képek Marketplace Management szolgáltatáson keresztül érhető el. Ezeket a lemezképeket készíteni, és tesztelni az Azure Stackhez.

### <a name="azure-linux-agent"></a>Azure Linux-ügynök

Az Azure Linux-ügynök (általános nevén `WALinuxAgent` vagy `walinuxagent`) van az ügynök munka az Azure Stacken szükséges, és nem minden verzióit. 2.2.20 és 2.2.35 verziói nem támogatottak az Azure Stacken. Használja a legújabb ügynökverziók 2.2.35 fent, 1901 gyorsjavítás/1902 gyorsjavítást, vagy frissítse az Azure Stack a 1903 (vagy újabb). Vegye figyelembe, hogy [a cloud-init](https://cloud-init.io/) jelenleg nem támogatott az Azure Stacken.

| Az Azure Stack-build | Az Azure Linux ügynök-build |
| ------------- | ------------- |
| 1.1901.0.99 és a korábbi verziók | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 vagy újabb |
| 1.1902.2.73  | 2.2.35 vagy újabb |
| 1.1903.0.35  | 2.2.35 vagy újabb |
| Nem támogatott | 2.2.21-2.2.34 |

Saját Linux-rendszerképek, az alábbi utasítások segítségével készítheti elő:

* [CentOS-alapú disztribúciók](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES és openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>A lemezkép felvétele a Marketplace-en

Hajtsa végre a [adja hozzá a lemezképet a Marketplace-en](azure-stack-add-vm-image.md). Győződjön meg arról, hogy a `OSType` paraméter értéke `Linux`.

A lemezképet a Marketplace-en való felvételét, Piactéri elem jön létre, és a felhasználók üzembe helyezhetnek egy Linux rendszerű virtuális gépet.

## <a name="next-steps"></a>További lépések

* [Azure Marketplace-elemek letöltése az Azure-ból az Azure Stackbe](azure-stack-download-azure-marketplace-item.md)
* [Az Azure Stack piactéren – áttekintés](azure-stack-marketplace.md)
