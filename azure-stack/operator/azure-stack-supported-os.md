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
ms.date: 02/17/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: aca2517a49936296f73c34392ab4c5439909872f
ms.sourcegitcommit: 75b13158347963063b7ee62b0ec57894b542c1be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66748969"
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
> Windows 10-es ügyfél operációs rendszereken az Azure Stack üzembe helyezéséhez rendelkeznie kell [Windows felhasználói licencelés](https://www.microsoft.com/en-us/Licensing/product-licensing/windows10.aspx) , vagy vásároljon egy minősített több-Bérlős szolgáltató keresztül ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

Marketplace-rendszerképek Használatalapú mint-akkor-használható vagy BYOL (nagyvállalati szerződés/SPLA) licencelési érhetők el. Az Azure Stack egypéldányos használata nem támogatott. Üzembe helyezés során az Azure Stack kódtárba a vendégügynök megfelelő verzióját a lemezképpel.

Datacenter kiadások érhetők el le; a piactéren ügyfelek kihasználhatják a saját kiszolgáló-lemezképekhez, beleértve a többi kiadás voltát. Windows ügyfél képek nem érhetők el a piactéren.

## <a name="linux"></a>Linux

Linux-disztribúciók elérhetőként szerepel a Marketplace-en tartalmazza a szükséges Windows Azure Linux ügynök (WALA). Ha saját rendszerképet az Azure Stackhez, irányelvekhez [hozzáadása típusú Linux-rendszerképeket az Azure Stackhez](azure-stack-linux.md).

> [!NOTE]
> Egyéni rendszerképek legújabb nyilvános WALA verziójával (1903 az azure stack-build és a fenti vagy 1901/1902 gyorsjavítás) kell építeni vagy 2.2.20 verziójával. Előfordulhat, hogy előtt 2.2.20 és 2.2.20 és 2.2.35 (exkluzív) közötti verziókat nem működik megfelelően az Azure Stacken. 
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

Az Azure Stack piactéren kapcsolatos további információkért lásd a következő cikkeket:

- [Piactéri termékek letöltése](azure-stack-download-azure-marketplace-item.md)  
- [Hozzon létre, és a Piactéri elem közzététele](azure-stack-create-and-publish-marketplace-item.md)
