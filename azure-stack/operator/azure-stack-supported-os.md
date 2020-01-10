---
title: A Azure Stack hub támogatott vendég operációs rendszerei | Microsoft Docs
description: Ezek a vendég operációs rendszerek Azure Stack központban is használhatók.
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
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: a7cd59775d6ca8232eeb820dc6f84a96b5d9c62a
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814048"
---
# <a name="guest-operating-systems-supported-on-azure-stack-hub"></a>Azure Stack hub által támogatott vendég operációs rendszerek

*A következőkre vonatkozik: Azure Stack hub integrált rendszerek és Azure Stack Development Kit*

## <a name="windows"></a>Windows

Azure Stack hub az alábbi táblázatban felsorolt Windows vendég operációs rendszereket támogatja:

| Operációs rendszer | Leírás | Elérhető a piactéren |
| --- | --- | --- |
| A Windows Server 1709-es verziója. | 64 bites | Mag tárolókkal |
| Windows Server 2019 | 64 bites |  Datacenter, Datacenter Core, adatközpont tárolókkal |
| Windows Server 2016 | 64 bites |  Datacenter, Datacenter Core, adatközpont tárolókkal |
| Windows Server 2012 R2 | 64 bites |  Adatközpont |
| Windows Server 2012 | 64 bites |  Adatközpont |
| Windows Server 2008 R2 SP1 | 64 bites |  Adatközpont |
| Windows Server 2008 SP2 | 64 bites |  Saját rendszerkép használata |
| Windows 10 *(lásd az 1. megjegyzést)* | 64 bites, Pro és Enterprise | Saját rendszerkép használata |

> [!NOTE]
> Ahhoz, hogy a Windows 10-es ügyfél operációs rendszereit Azure Stack hubhoz telepítse, [Windows felhasználónkénti licenceléssel](https://www.microsoft.com/licensing/product-licensing/windows10.aspx) kell rendelkeznie, vagy egy minősített több-bérlős szolgáltatón ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)) keresztül kell megvásárolnia.

A piactéren elérhető rendszerképek a használaton kívüli vagy BYOL (EA/SPLA) licenceléshez érhetők el. Mindkettő használata egyetlen Azure Stack hub-példányon nem támogatott. Az üzembe helyezés során Azure Stack hub a vendég ügynök megfelelő verzióját adja a rendszerképbe.

Az adatközpont kiadásai letölthetők a piactéren. az ügyfelek saját kiszolgálói lemezképeket is létrehozhatnak, beleértve a többi kiadást is. A Windows-ügyfelek lemezképei nem érhetők el a piactéren.

## <a name="linux"></a>Linux

A piactéren elérhető Linux-disztribúciók tartalmazzák a szükséges Windows Azure Linux-ügynököt (WALA). Ha a saját lemezképét Azure Stack hubhoz viszi, kövesse a [Linux-rendszerképek hozzáadása Azure stack hub-hoz](azure-stack-linux.md)című témakör útmutatását.

> [!NOTE]
> Az egyéni lemezképeket a legújabb nyilvános WALA-verzióval kell létrehozni (az 1903 Azure Stack hub Build és újabb verziójában, vagy a 1901/1902 gyorsjavítással), vagy a 2.2.20 verzióval. Előfordulhat, hogy a 2.2.20 és a 2.2.21 és a 2.2.34 (beleértve) közötti verziók nem működnek megfelelően az Azure Stack központban. Azure Stack hub 1910-es és újabb verzióiban az Azure WALA-ügynök összes verziója együttműködik Azure Stack hub-val.
>
> a [Cloud-init](https://cloud-init.io/) a Azure Stack hub 1910-es vagy újabb verziójában támogatott.

| Terjesztés | Leírás | Gyártó/kiadó | Piactér |
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
| Ubuntu 16,04 – LTS | 64 bites | Canonical | Igen |
| Ubuntu 18,04 – LTS | 64 bites | Canonical | Igen |

Red Hat Enterprise Linux támogatási információkat a [Red Hat és a Azure stack hub: gyakori kérdések](https://access.redhat.com/articles/3413531)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

Az Azure Stack hub Marketplace-ről további információt a következő cikkekben talál:

- [Piactéri termékek letöltése](azure-stack-download-azure-marketplace-item.md)  
- [Marketplace-elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
