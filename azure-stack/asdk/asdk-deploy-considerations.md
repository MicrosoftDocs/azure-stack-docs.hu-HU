---
title: Az Azure Stack Development Kit (ASDK) központi telepítés előfeltételei |} A Microsoft Docs
description: Tekintse át a vonatkozó környezeti és hardverkövetelményeket az Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: 3a5b506cdb7441ef60d4731718cafa8aa267c078
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172434"
---
# <a name="azure-stack-deployment-planning-considerations"></a>Tervezési megfontolások az Azure Stack üzemelő példányához
Az Azure Stack Development Kit (ASDK), üzembe helyezése előtt győződjön meg arról, a fejlesztői csomag fogadó számítógép megfelel-e az ebben a cikkben ismertetett követelményeknek.


## <a name="hardware"></a>Hardver
| Összetevő | Minimális | Ajánlott |
| --- | --- | --- |
| Lemezmeghajtók: Operációs rendszer: |1 operációsrendszer-lemez, legalább 200 GB szabad rendszerpartíció (SSD vagy HDD) |1 rendszerlemez legalább 200 GB szabad tárhellyel a rendszerpartícióhoz (SSD vagy HDD) |
| Lemezmeghajtók: Általános development kit adatok<sup>*</sup>  |4 lemez. Mindegyik lemez legalább 240 GB tárhellyel (SSD vagy HDD) biztosít. Az összes rendelkezésre álló lemezek használhatók. |4 lemez. Mindegyik lemez legalább 400 GB tárhellyel (SSD vagy HDD) biztosít. Az összes rendelkezésre álló lemezek használhatók. |
| Számítási: CPU |Kettős foglalat: 16 fizikai mag (összesen) |Kettős foglalat: 20 fizikai mag (összesen) |
| Számítási: Memória |192 GB RAM |256 GB RAM |
| Számítási: BIOS-BAN |Hyper-V engedélyezve (SLAT-támogatással) |Hyper-V engedélyezve (SLAT-támogatással) |
| Hálózat: Hálózati adapter |A Windows Server 2012 R2 hitelesítésével. Speciális jellemzők nem szükségesek |A Windows Server 2012 R2 hitelesítésével. Speciális jellemzők nem szükségesek |
| Hardveres tanúsítványembléma |[Certified for Windows Server 2012 R2 rendszerben](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certified for Windows Server 2016-ban](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> Kell több mint kapacitás ez javasolt, ha azt tervezi, hogy számos hozzáadása a [Piactéri elemek](../operator/azure-stack-create-and-publish-marketplace-item.md) az Azure-ból.

**Adatlemez-meghajtó konfigurációja:** Minden adatmeghajtónak ugyanolyan típusú (csak SAS, minden SATA vagy minden NVMe) és kapacitást kell lennie. Ha SAS-lemezmeghajtókat használ, egyetlen elérési úttal kell őket összekapcsolnia (az MPIO nem engedélyezett, a többutas működés támogatása biztosított).

**HBA-konfigurációs lehetőségek**

* (Előnyben részesített) Egyszerű HBA
* RAID HBA - adaptert "átmenő" módba kell konfigurálni
* RAID HBA - lemezeket kell beállítani, mint egyetlen-lemez, RAID-0

**Támogatott busz- és adathordozó-típuskombinációk**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD (ha az adathordozó-típusa nem meghatározott/ismeretlen<sup>*</sup>)
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD
* NVMe

<sup>*</sup> Az átmenő képesség nélküli RAID-vezérlők nem ismerik fel az adathordozó típusát. Az ilyen vezérlők a HDD és SSD nincs meghatározva megjelölni. Ebben az esetben az SSD állandó tárolóként gyorsítótáreszköz helyett szolgál. Ezért a csomagban található ezeken az SSD meghajtókon is telepítheti.

**Példa HBA**: LSI 9207-8i, LSI-9300-8i vagy LSI-9265-8i átmenő módban

OEM mintakonfigurációk is elérhetők.

## <a name="operating-system"></a>Operációs rendszer
|  | **Követelmények** |
| --- | --- |
| **Operációsrendszer-verzió** |A Windows Server 2016 vagy újabb. Az operációs rendszer verziója nem kritikus fontosságú az üzembe helyezés megkezdése előtt, akkor a számítógép lesz indítsa el a a VHD-t, amely az Azure Stack-telepítés része. Az operációs rendszer és az összes szükséges javítást már integrálva van a lemezképpel. Ne használjon bármely kulcsok aktiválásához a csomagban használt bármilyen Windows Server-példányon. |

> [!TIP]
> Az operációs rendszer telepítése után is használhatja a [Deployment Checker for Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) , győződjön meg arról, hogy a hardver megfelel-e az összes követelményének.

## <a name="account-requirements"></a>Fiókra vonatkozó követelmények
Általában a csomag üzembe internetkapcsolattal, ahol a Microsoft Azure csatlakozhat. Ebben az esetben konfigurálnia kell egy Azure Active Directory (Azure AD-) fiókot a csomag telepítéséhez.

Ha a környezet nem kapcsolódik az internethez, vagy nem szeretné használni az Azure ad-ben, az Active Directory összevonási szolgáltatások (AD FS) használatával telepítheti az Azure Stack. A csomag tartalmazza a saját AD FS és az Active Directory Domain Services-példányokat. Ha ez a beállítás használatával telepít, nem kell előre fiókok beállítása.

> [!NOTE]
> Ha az AD FS lehetőség használatával telepíti, újra kell telepítenie az Azure Stack az Azure AD-váltás.

### <a name="azure-active-directory-accounts"></a>Az Azure Active Directory-fiókok
Az Azure Stack üzembe helyezése egy Azure AD-fiókkal, el kell készítenie az Azure AD-fiókot, az üzembe helyezés PowerShell-parancsfájl futtatása előtt. Ezt a fiókot az Azure AD-bérlő számára a globális rendszergazdája lesz. Üzembe helyezhető, és delegálja az alkalmazások és az egyszerű szolgáltatások Azure Stack-szolgáltatásokhoz, amelyek együttműködnek az Azure Active Directory és a Graph API használatos. Az alapértelmezett szolgáltatója előfizetést (ez később módosíthatja) tulajdonosaként is használható. Bejelentkezhet az Azure Stack rendszer rendszergazdai portál e fiók használatával.

1. Hozzon létre egy Azure AD-fiókot, amely a címtár-rendszergazdája legalább egy Azure AD-hez. Ha már rendelkezik ilyen fiókkal, használhatja azt. Ellenkező esetben létrehozhat egy ingyenes [ https://azure.microsoft.com/free/ ](https://azure.microsoft.com/free/) (Kínából <https://go.microsoft.com/fwlink/?LinkID=717821> helyett). Ha azt tervezi, hogy később [regisztrálása az Azure Stack az Azure-ral](asdk-register.md), rendelkeznie kell egy előfizetés az újonnan létrehozott fiók.
   
    Mentse ezeket a hitelesítő adatokat a szolgáltatás-rendszergazdaként. Ez a fiók konfigurálhatja és kezelheti a erőforrásfelhőket, felhasználói fiókokat, bérlői csomagokat, kvótákat és díjszabás. A portálon létrehozhatnak felhőket webhelyhez, magánfelhőket virtuális géphez, csomagokat, és kezelhetik a felhasználói előfizetéseket.
1. Hozzon létre legalább egy tesztfelhasználói fiókja az Azure AD-ben, így bejelentkezhet bérlőként development Kit.
   
   | **Az Azure Active Directory-fiók** | **Támogatott?** |
   | --- | --- |
   | Munkahelyi vagy iskolai fiók érvényes globális Azure-előfizetéssel |Igen |
   | Érvényes globális Azure-előfizetéssel rendelkező Microsoft-Account |Igen |
   | Munkahelyi vagy iskolai fiók érvényes China Azure-előfizetéssel |Igen |
   | Munkahelyi vagy iskolai fiók érvényes US Government Azure-előfizetéssel |Igen |

Az üzembe helyezést követően az Azure Active Directory globális rendszergazdának, nem szükséges. Egyes műveletek azonban szükség lehet a globális rendszergazdai hitelesítő adatok. Például egy erőforrás-szolgáltató szoftvertelepítő parancsfájl vagy egy új szolgáltatást igénylő engedélyt kell adni. Ideiglenesen érvényességének visszaállítása a fiók globális rendszergazdai jogosultságokkal, vagy használjon egy külön globális rendszergazdai fiókkal, amely a tulajdonosa a *szolgáltatói előfizetés alapértelmezett*.

## <a name="network"></a>Network (Hálózat)
### <a name="switch"></a>Kapcsoló
Egy elérhető port a kapcsolón a fejlesztői csomag gépen.  

A fejlesztői csomag gép támogatja a csatlakozást egy kapcsoló hozzáférési portjához vagy trönkportjához. A kapcsoló esetében nem szükségesek speciális jellemzők. Ha trönkportot használ, vagy ha egy VLAN-azonosítót kell beállítania, üzembehelyezési paraméterként kell megadnia a VLAN-azonosítót.

### <a name="subnet"></a>Alhálózat
A következő alhálózatok ne csatlakoztassa a fejlesztői csomag gépen:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Ezen alhálózatok a development kit környezetben található belső hálózatok számára vannak fenntartva.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Kizárólag az IPv4 használata támogatott. Nem hozhat létre IPv6-hálózatokat.

### <a name="dhcp"></a>DHCP
Győződjön meg róla, hogy egy DHCP-kiszolgáló elérhető azon a hálózaton, amelyhez a NIC csatlakozik. Ha a DHCP nem elérhető el, egy kiegészítő, az állomás által használt melletti, statikus IPv4-hálózatot kell előkészítenie. Üzembehelyezési paraméterként meg kell adnia azt az IP-címet és átjárót.

### <a name="internet-access"></a>Internetelérés
Az Azure Stack internet-hozzáférésre van szüksége, közvetlenül vagy a transzparens proxy használatával. Az Azure Stack nem támogatja az Internet-hozzáférés engedélyezése egy webalkalmazás-proxy konfigurálása. A gazdagép IP-cím és a AzS-BGPNAT01 (által a DHCP vagy statikus IP-cím) rendelt új IP az Internet eléréséhez képesnek kell lennie. 80-as és 443-as portok a graph.windows.net és login.microsoftonline.com tartományok alatt vannak használatban.


## <a name="next-steps"></a>További lépések
[Töltse le a ASDK központi telepítési csomag](asdk-download.md)
