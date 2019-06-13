---
title: Támogatott vendég operációs rendszereket az Azure Stackhez |} A Microsoft Docs
description: Ezek a vendég operációs rendszerek az Azure Stacken használható.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2018
ms.openlocfilehash: 53acfa3a2d85e73b5d8136c7a1f42ee00d8ed1be
ms.sourcegitcommit: 07c51a03f07a6a3ee2721aa942d31a7a4c6a339b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2019
ms.locfileid: "67028263"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Vendég operációs rendszereket támogatják az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

## <a name="windows"></a>Windows

Az Azure Stack támogatja az alábbi táblázatban felsorolt Windows vendég operációs rendszerek:

| Operációs rendszer | Leírás | A Marketplace-en elérhető |
| --- | --- | --- |
| A Windows Server 1709-es verzió | 64 bites | A tárolók Core |
| A Windows Server 2019 | 64 bites |  Datacenter, Datacenter mag, Datacenter tárolókkal |
| Windows Server 2016 | 64 bites |  Datacenter, Datacenter mag, Datacenter tárolókkal |
| Windows Server 2012 R2 | 64 bites |  Adatközpont |
| Windows Server 2012 | 64 bites |  Adatközpont |
| Windows Server 2008 R2 SP1 | 64 bites |  Adatközpont |
| Windows Server 2008 SP2 | 64 bites |  Saját kép használata |
| Windows 10-es *(lásd az 1. megjegyzést)* | 64 bites, Pro és Enterprise | Saját kép használata |

> [!NOTE]
> Windows 10-es ügyfél operációs rendszereken az Azure Stack üzembe helyezéséhez rendelkeznie kell [Windows felhasználói licencelés](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) , vagy vásároljon egy minősített több-Bérlős szolgáltató keresztül ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

Marketplace-rendszerképek használatalapú mint-akkor-használható vagy BYOL (nagyvállalati szerződés/SPLA) licencelési érhetők el. Mindkét egyetlen Azure Stack-példány használata nem támogatott. Üzembe helyezés során az Azure Stack kódtárba a vendégügynök megfelelő verzióját a lemezképpel.

Datacenter kiadások érhetők el le; a piactéren ügyfelek kihasználhatják a saját kiszolgáló-lemezképekhez, beleértve a többi kiadás voltát. Windows ügyfél képek nem érhetők el a piactéren.

## <a name="linux"></a>Linux

Linux-disztribúciók elérhetőként szerepel a Marketplace-en tartalmazza a szükséges Windows Azure Linux ügynök (WALA). Ha saját rendszerképet az Azure Stackhez, irányelvekhez [hozzáadása típusú Linux-rendszerképeket az Azure Stackhez](azure-stack-linux.md).

> [!NOTE]
> Egyéni rendszerképek a legújabb nyilvános WALA verzióját (az Azure Stack 1903 build és a fenti vagy 1901/1902 gyorsjavítással), vagy 2.2.20 verzióval kell építeni. Előfordulhat, hogy előtt 2.2.20 és 2.2.20 és 2.2.35 (exkluzív) közötti verziókat nem működik megfelelően az Azure Stacken.
>
> [a cloud-init](https://cloud-init.io/) jelenleg nem támogatott az Azure Stacken.

| Disztribúció | Leírás | Kiadó | Piactér |
| --- | --- | --- | --- |
| CentOS-alapú 6.9. | 64 bites | Rogue Wave | Igen |
| CentOS-alapú 7.5 | 64 bites | Rogue Wave | Igen |
| CentOS-alapú 7.3 | 64 bites | Rogue Wave | Igen |
| ClearLinux | 64 bites | ClearLinux.org | Igen |
| Tároló Linux |  64 bites | CoreOS | Stable |
| Debian 8 "Jessie" | 64 bites | credativ |  Igen |
| Debian 9 "Stretch" | 64 bites | credativ | Igen |
| Red Hat Enterprise Linux 7.1-es (és újabb verziók) | 64 bites | Red Hat | Saját kép használata |
| SLES 11SP4 | 64 bites | SUSE | Igen |
| SLES 12SP3 | 64 bites | SUSE | Igen |
| Ubuntu 14.04-LTS | 64 bites | Canonical | Igen |
| Ubuntu 16.04-LTS | 64 bites | Canonical | Igen |
| Ubuntu 18.04-LTS | 64 bites | Canonical | Igen |

Red Hat Enterprise Linux-támogatás információkért lásd: [Red Hat és az Azure Stack: Gyakori kérdések](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>További lépések

Az Azure Stack piactéren kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Piactéri termékek letöltése](azure-stack-download-azure-marketplace-item.md)  
- [Hozzon létre, és a Piactéri elem közzététele](azure-stack-create-and-publish-marketplace-item.md)
