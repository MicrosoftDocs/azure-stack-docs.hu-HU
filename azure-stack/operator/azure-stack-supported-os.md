---
title: Az Azure Stack Hub által támogatott vendég operációs rendszerek
titleSuffix: Azure Stack
description: Ismerje meg, hogy mely vendég operációs rendszerek használhatók Azure Stack központban.
author: sethmanheim
ms.topic: article
ms.date: 02/01/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: 336a4d27adf5f0ab52fffc6b624ce84f7fd0b53c
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99245908"
---
# <a name="guest-operating-systems-supported-on-azure-stack-hub"></a>Az Azure Stack Hub által támogatott vendég operációs rendszerek

## <a name="windows"></a>Windows

Azure Stack hub az alábbi táblázatban felsorolt Windows vendég operációs rendszereket támogatja:

| Operációs rendszer | Leírás | Elérhető a Azure Stack hub piactéren |
| --- | --- | --- |
| Windows Server, 1709-es verzió | 64 bites | Mag tárolókkal |
| Windows Server 2019 | 64 bites |  Datacenter, Datacenter Core, adatközpont tárolókkal |
| Windows Server 2016 | 64 bites |  Datacenter, Datacenter Core, adatközpont tárolókkal |
| Windows Server 2012 R2 | 64 bites |  Adatközpont |
| Windows Server 2012 | 64 bites |  Adatközpont |
| Windows Server 2008 R2 SP1 | 64 bites |  Adatközpont |
| Windows Server 2008 SP2 | 64 bites |  Saját rendszerkép használata |
| Windows 10 *(lásd az 1. megjegyzést)* | 64 bites, Pro és Enterprise | Saját rendszerkép használata |

> [!NOTE]
> Ahhoz, hogy a Windows 10-es ügyfél operációs rendszereit Azure Stack hubhoz telepítse, [Windows felhasználónkénti licenceléssel](https://www.microsoft.com/licensing/product-licensing/windows10.aspx) kell rendelkeznie, vagy egy [minősített több-bérlős szolgáltatón (QMTH)](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)keresztül kell megvásárolnia.

A piactéren elérhető rendszerképek a használaton kívüli vagy BYOL (EA/SPLA) licenceléshez érhetők el. A mindkettő egyetlen Azure Stack hub-példányon való használata nem támogatott. Az üzembe helyezés során Azure Stack hub a vendég ügynök megfelelő verzióját adja a rendszerképbe.

Az adatközpont kiadásai a Azure Stack hub piactéren érhetők el Letöltés céljából. az ügyfelek saját kiszolgálói lemezképeket is létrehozhatnak, beleértve a többi kiadást is. A Windows-ügyfelek lemezképei nem érhetők el Azure Stack hub piactéren.

## <a name="linux"></a>Linux

A Azure Stack hub piactéren elérhetőként felsorolt Linux-disztribúciók közé tartozik a szükséges Windows Azure Linux-ügynök (WALA). Ha a saját lemezképét Azure Stackja, kövesse a [Linux-rendszerképek hozzáadása a Azure Stackhoz](azure-stack-linux.md)című témakör útmutatását.

> [!NOTE]
> Az egyéni lemezképeket a legújabb nyilvános WALA-verzióval kell létrehozni (az 1903 Azure Stack hub Build és újabb verziójában, vagy a 1901/1902 gyorsjavítással), vagy a 2.2.20 verzióval. Előfordulhat, hogy a 2.2.20 és a 2.2.21 és a 2.2.34 (beleértve) közötti verziók nem működnek megfelelően az Azure Stack központban. Azure Stack hub 1910-es és újabb verzióiban az Azure WALA-ügynök összes verziója együttműködik Azure Stack hub-val.
>
> a [Cloud-init](https://cloud-init.io/) a Azure Stack hub 1910-es vagy újabb verziójában támogatott.

| Disztribúció | Leírás | Publisher | Azure Stack hub piactér |
| --- | --- | --- | --- |
| CentOS-alapú 8,0 | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 7,8 | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 7,7 LVM | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 7,7 | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 7,6 | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 7,5 | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 7,5 LVM | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 7,4 | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 7.3 | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 6,9 | 64 bites | Gazember hullám | Igen |
| CentOS-alapú 6,10 | 64 bites | Gazember hullám | Igen |
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
| Ubuntu Server 20,04 LTS | 64 bites | Canonical | Igen |

Red Hat Enterprise Linux támogatási információkat a [Red Hat és a Azure stack hub: gyakori kérdések](https://access.redhat.com/articles/3413531)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

Az Azure Stack hub Marketplace-ről további információt a következő cikkekben talál:

- [Marketplace-elemek letöltése](azure-stack-download-azure-marketplace-item.md)  
- [Marketplace-elemek létrehozása és közzététele](azure-stack-create-and-publish-marketplace-item.md)
