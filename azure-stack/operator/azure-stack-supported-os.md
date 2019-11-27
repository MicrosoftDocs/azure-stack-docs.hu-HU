---
title: A Azure Stack támogatott vendég operációs rendszerek | Microsoft Docs
description: Ezek a vendég operációs rendszerek Azure Stack használhatják.
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
ms.date: 08/14/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: fc3eecb8f4e06ce02737e513c48d946f2a667c21
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74298765"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure Stack támogatott vendég operációs rendszerek

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

## <a name="windows"></a>Windows

Azure Stack támogatja az alábbi táblázatban felsorolt Windows vendég operációs rendszereket:

| Operációs rendszer | Leírás | Elérhető a piactéren |
| --- | --- | --- |
| Windows Server, 1709-es verzió | 64 bites | Mag tárolókkal |
| A Windows Server 2019 | 64 bites |  Datacenter, Datacenter Core, adatközpont tárolókkal |
| Windows Server 2016 | 64 bites |  Datacenter, Datacenter Core, adatközpont tárolókkal |
| Windows Server 2012 R2 | 64 bites |  Adatközpont |
| Windows Server 2012 | 64 bites |  Adatközpont |
| Windows Server 2008 R2 SP1 | 64 bites |  Adatközpont |
| Windows Server 2008 SP2 | 64 bites |  Saját rendszerkép használata |
| Windows 10 *(lásd az 1. megjegyzést)* | 64 bites, Pro és Enterprise | Saját rendszerkép használata |

> [!NOTE]
> Ahhoz, hogy a Windows 10-es ügyfél operációs rendszereit Azure Stack telepítse, a [Windows felhasználónkénti licencelése](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) vagy egy minősített több-bérlős szolgáltató ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)) használatával kell megvásárolnia.

A piactéren elérhető rendszerképek a használaton kívüli vagy BYOL (EA/SPLA) licenceléshez érhetők el. Mindkettő használata egyetlen Azure Stack példányon nem támogatott. Az üzembe helyezés során Azure Stack beinjektálja a vendég ügynök megfelelő verzióját a rendszerképbe.

Az adatközpont kiadásai letölthetők a piactéren. az ügyfelek saját kiszolgálói lemezképeket is létrehozhatnak, beleértve a többi kiadást is. A Windows-ügyfelek lemezképei nem érhetők el a piactéren.

## <a name="linux"></a>Linux

A piactéren elérhető Linux-disztribúciók tartalmazzák a szükséges Windows Azure Linux-ügynököt (WALA). Ha a saját lemezképét Azure Stackja, kövesse a [Linux-rendszerképek hozzáadása a Azure Stackhoz](azure-stack-linux.md)című témakör útmutatását.

> [!NOTE]
> Az egyéni lemezképeket a legújabb nyilvános WALA-verzióval kell felépíteni (a 1903 Azure Stack Build és újabb verziókon, vagy a 1901/1902-es gyorsjavítással), vagy pedig a 2.2.20. Előfordulhat, hogy a 2.2.20 és a 2.2.21 és a 2.2.34 (beleértve) közötti verziók nem működnek megfelelően a Azure Stackon. Azure Stack 1910-es kiadáson és azon kívül minden Azure WALA-ügynök verziója működni fog a Azure Stack.
>
> a [Cloud-init](https://cloud-init.io/) Azure stack 1910-es kiadáson és ezen az időponton túl támogatott.

| Disztribúció | Leírás | Kiadó | Piactér |
| --- | --- | --- | --- |
| CentOS-alapú 6,9 | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 7,5 | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 7.3 | 64 bites | Gazember hullám | Igen |
| ClearLinux | 64 bites | ClearLinux.org | Igen |
| CoreOS Linux (stabil) |  64 bites | CoreOS | Igen |
| Debian 8 "Megbéklyóz" | 64 bites | credativ |  Igen |
| Debian 9 "stretch" | 64 bites | credativ | Igen |
| Oracle Linux | 64 bites | Oracle | Igen |
| Red Hat Enterprise Linux 7,1 (és újabb verziók) | 64 bites | Red Hat | Saját rendszerkép használata |
| SLES 11SP4 | 64 bites | SUSE | Igen |
| SLES 12SP3 | 64 bites | SUSE | Igen |
| Ubuntu 14,04 – LTS | 64 bites | Canonical | Igen |
| Ubuntu 16.04-LTS | 64 bites | Canonical | Igen |
| Ubuntu 18.04-LTS | 64 bites | Canonical | Igen |

Red Hat Enterprise Linux támogatási információkat a [Red Hat és a Azure stack: gyakori kérdések](https://access.redhat.com/articles/3413531)című témakörben talál.

## <a name="next-steps"></a>További lépések

A Azure Stack Marketplace-ről további információt a következő cikkekben talál:

- [Piactéri termékek letöltése](azure-stack-download-azure-marketplace-item.md)  
- [Marketplace-elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
