---
title: A ASDK követelményei és szempontjai
description: További információ a Azure Stack Development Kit hardver-, szoftver-és környezeti követelményeiről (ASDK).
author: myoungerman
ms.topic: article
ms.date: 09/23/2020
ms.author: v-myoung
ms.reviewer: misainat
ms.lastreviewed: 09/23/2020
ms.openlocfilehash: dbd0398b7f6582c12288c2ae72137c4a673ddfcc
ms.sourcegitcommit: 950dbc793b3498173923d0bc9fe56662a349abf1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92082088"
---
# <a name="asdk-requirements-and-considerations"></a>A ASDK követelményei és szempontjai

A Azure Stack Development Kit (ASDK) telepítése előtt győződjön meg arról, hogy a ASDK-gazdagép megfelel a jelen cikkben ismertetett követelményeknek.

## <a name="hardware"></a>Hardver

| Összetevő                                             | Minimális                                                                                                 | Ajánlott                                                                                             |
|-------------------------------------------------------|---------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| Lemezmeghajtók: Operációs rendszer                         | 1 operációsrendszer-lemez, amely legalább 200 GB-nyi rendelkezésre áll a rendszerpartícióhoz (SSD vagy HDD).             | 1 operációsrendszer-lemez, amely legalább 200 GB-nyi rendelkezésre áll a rendszerpartícióhoz (SSD vagy HDD).                           |
| Lemezmeghajtók: általános fejlesztői csomag adatai<sup>*</sup> | 4 lemez. Mindegyik lemez legalább 240 GB kapacitást biztosít (SSD vagy HDD). Az összes rendelkezésre álló lemez használatban van. | 4 lemez. Mindegyik lemez legalább 400 GB kapacitást biztosít (SSD vagy HDD). Az összes rendelkezésre álló lemez használatban van. |
| Számítási igény: CPU                                          | Kettős szoftvercsatorna: 16 fizikai mag (összesen).                                                                 | Kettős szoftvercsatorna: 20 fizikai mag (összesen).                                                                 |
| Számítási igény: Memória                                       | 192 – GB RAM.                                                                                             | 256 – GB RAM.                                                                                             |
| Számítási igény: BIOS                                         | Hyper-V engedélyezve (LÉC támogatással).                                                                    | Hyper-V engedélyezve (LÉC támogatással).                                                                    |
| Hálózat: NIC                                          | Windows Server 2012 R2 minősítés. Nincs szükség speciális funkciókra.                                 | Windows Server 2012 R2 minősítés. Nincs szükség speciális funkciókra.                                 |
| Hardveres tanúsítványembléma                                 | [Windows Server 2012 R2 minősítéssel](https://www.windowsservercatalog.com/content.aspx?ctf=logo.htm)rendelkezik. | [A Windows Server 2016 minősítéssel](https://www.windowsservercatalog.com/content.aspx?ctf=logo.htm)rendelkezik.    |
  
<sup>*</sup> Ha az Azure-ban sok [Piactéri elem](../operator/azure-stack-create-and-publish-marketplace-item.md) hozzáadását tervezi, ennél nagyobb kapacitásra van szüksége.

### <a name="hardware-notes"></a>Hardver megjegyzései

**Adatlemez-meghajtó konfigurációja:** Minden adatmeghajtónak azonos típusúnak kell lennie (az összes SAS, az összes SATA vagy az összes NVMe) és a kapacitásnak. Ha SAS-lemezmeghajtókat használ, egyetlen elérési úttal kell őket összekapcsolnia (az MPIO nem engedélyezett, a többutas működés támogatása biztosított).

**HBA-konfigurációs lehetőségek**

* Előnyben részesített Egyszerű HBA.
* A RAID HBA-adaptert "pass through" módban kell konfigurálni.
* RAID HBA – a lemezeket egylemezes, RAID-0 értékként kell konfigurálni.

**Támogatott busz- és adathordozó-típuskombinációk**

* SATA HDD
* SAS HDD
* RAID HDD
* RAID SSD (ha az adathordozó típusa meghatározatlan/ismeretlen <sup>*</sup> )
* SATA SSD + SATA HDD
* SAS SSD + SAS HDD
* NVMe

<sup>*</sup> Az áteresztő képesség nélküli RAID-vezérlők nem ismerik fel az adathordozó típusát. Az ilyen vezérlők megjelölik a HDD-t és az SSD-t is meghatározatlan. Ebben az esetben az SSD a gyorsítótárazási eszközök helyett állandó tárolóként van használatban. Ezért a ASDK az SSD-meghajtókon is üzembe helyezheti.

**Példa HBA**: LSI 9207-8i, LSI-9300-8i vagy LSI-9265-8i átmenő módban.

OEM mintakonfigurációk is elérhetők.

### <a name="storage-resiliency-for-the-asdk"></a>Tárolási rugalmasság a ASDK

Egyetlen csomópontos rendszerként a ASDK nem egy Azure Stack integrált rendszer éles redundancia ellenőrzéséhez készült. Azonban a HDD-és SSD-meghajtók optimális kombinációja révén növelheti a ASDK mögöttes tárolási redundancia szintjét. A RAID1 hasonlóan kétirányú tükrözési konfigurációt is üzembe helyezhet, nem pedig egy egyszerű rugalmassági konfigurációt, amely a RAID0 hasonló. A mögöttes Közvetlen tárolóhelyek konfigurációhoz elegendő kapacitást, típust és meghajtót használjon.

Kétirányú tükrözési konfiguráció használata a tárolási rugalmasság érdekében:

- A HDD kapacitása nagyobb, mint két terabájt.
- Ha nem rendelkezik SSD-vel a ASDK, legalább nyolc HDD-t kell használnia a kétirányú tükrözési konfigurációhoz.
- Ha a ASDK SSD-k vannak, a HDD-k mellett legalább öt HDD-t kell használnia. Hat HHDs azonban ajánlott. Hat HDD esetén ajánlott legalább három megfelelő SSD-t használni a rendszeren, hogy legyen egy gyorsítótár-lemez (SSD) a két kapacitású meghajtó (HDD) kiszolgálásához.

Példa kétirányú tükrözési konfigurációra:

- Nyolc HDD
- Három SSD/6 HDD
- Négy SSD/8 HDD

## <a name="operating-system"></a>Operációs rendszer
|  | **Követelmények** |
| --- | --- |
| **Operációs rendszer verziója** |Windows Server 2016 vagy újabb. Az operációs rendszer verziója nem kritikus fontosságú az üzemelő példány elindítása előtt, mert a gazdagépet a Azure Stack telepítés részét képező virtuális merevlemezre indítja. Az operációs rendszer és az összes szükséges javítás már integrálva van a lemezképbe. Ne használjon kulcsokat a ASDK használt Windows Server-példányok aktiválásához. |

## <a name="account-requirements"></a>Fiókra vonatkozó követelmények
A ASDK általában internetkapcsolattal kell központilag telepíteni, ahol Microsoft Azurehoz csatlakozhat. Ebben az esetben konfigurálnia kell egy Azure Active Directory (Azure AD) fiókot a ASDK telepítéséhez.

Ha a környezet nem kapcsolódik az internethez, vagy nem kívánja használni az Azure AD-t, Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával telepítheti Azure Stack. A ASDK saját AD FS és Active Directory tartományi szolgáltatások példányokat tartalmaz. Ha ezt a beállítást használja, nem kell előre beállítania a fiókokat.

> [!NOTE]
> Ha az AD FS lehetőséggel végzi a telepítést, újra kell telepítenie Azure Stack az Azure AD-re való váltáshoz.

### <a name="azure-active-directory-accounts"></a>Fiókok Azure Active Directory
Azure Stack Azure AD-fiók használatával történő telepítéséhez elő kell készítenie egy Azure AD-fiókot az üzembe helyezési PowerShell-szkript futtatása előtt. Ez a fiók lesz az Azure AD-bérlő globális rendszergazdája. A szolgáltatás az Azure AD-vel és a Graph API-mel kommunikáló összes Azure Stack szolgáltatáshoz az alkalmazások és az egyszerű szolgáltatások kiépítésére és delegálására használatos. Az alapértelmezett szolgáltatói előfizetés tulajdonosaként is használatos (amelyet később is módosíthat). A fiók használatával bejelentkezhet a Azure Stack System felügyeleti portálján.

1. Hozzon létre egy Azure AD-fiókot, amely legalább egy Azure AD-címtár rendszergazdája. Ha már rendelkezik ilyen fiókkal, használhatja azt. Ellenkező esetben létrehozhat egyet ingyen a [https://azure.microsoft.com/free/](https://azure.microsoft.com/free/) (Kínában, a látogatás <https://go.microsoft.com/fwlink/?LinkID=717821> helyett). Ha azt tervezi, hogy később [regisztrálja Azure stack az Azure](asdk-register.md)-ban, akkor ehhez az újonnan létrehozott fiókhoz is rendelkeznie kell előfizetéssel.
   
    Mentse ezeket a hitelesítő adatokat a szolgáltatás-rendszergazdaként való használatra. Ez a fiók az erőforrás-felhőket, a felhasználói fiókokat, a bérlői terveket, a kvótákat és a díjszabást konfigurálhatja és kezelheti A portálon létrehozhatnak webhelyeket, virtuális gépeket és saját felhőket, csomagokat hozhatnak létre, és kezelhetik a felhasználói előfizetéseket.
1. Hozzon létre legalább egy tesztelési felhasználói fiókot az Azure AD-ben, hogy bérlőként jelentkezzen be a ASDK.
   
   | **Azure Active Directory-fiók** | **Támogatott?** |
   | --- | --- |
   | Munkahelyi vagy iskolai fiók érvényes globális Azure-előfizetéssel |Yes |
   | Microsoft-fiók érvényes globális Azure-előfizetéssel |Yes |
   | Munkahelyi vagy iskolai fiók érvényes China Azure-előfizetéssel |Yes |
   | Munkahelyi vagy iskolai fiók érvényes USA-beli kormányzati Azure-előfizetéssel |Yes |

Az üzembe helyezést követően az Azure AD globális rendszergazdai engedélye nem szükséges. Egyes műveletek esetében azonban szükség lehet a globális rendszergazdai hitelesítő adatokra. Ilyen művelet például egy erőforrás-szolgáltatót telepítő parancsfájl vagy egy olyan új szolgáltatás, amely engedély megadását igényli. Ideiglenesen visszaállíthatja a fiók globális rendszergazdai engedélyeit, vagy használhat egy különálló globális rendszergazdai fiókot, amely az *alapértelmezett szolgáltatói előfizetés*tulajdonosa.

## <a name="network"></a>Network (Hálózat)
### <a name="switch"></a>Kapcsoló (switch)
Egy elérhető port egy kapcsolón a ASDK gép számára.  

A ASDK-gép támogatja a csatlakozást egy kapcsoló hozzáférési portjához vagy a Trunk portjához. A kapcsoló esetében nem szükségesek speciális jellemzők.

### <a name="subnet"></a>Alhálózat
Ne kapcsolja a ASDK gépet a következő alhálózatokhoz:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Ezek az alhálózatok a ASDK-környezeten belüli belső hálózatok számára vannak fenntartva.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Kizárólag az IPv4 használata támogatott. Nem hozhat létre IPv6-hálózatokat.

### <a name="dhcp"></a>DHCP
Ellenőrizze, hogy van-e olyan DHCP-kiszolgáló a hálózaton, amelyhez a hálózati adapter csatlakozik. Ha a DHCP nem érhető el, egy további, a gazdagép által használt statikus IPv4-hálózatot kell előkészítenie. Üzembehelyezési paraméterként meg kell adnia azt az IP-címet és átjárót.

### <a name="internet-access"></a>Internet-hozzáférés
Azure Stack az internet elérését igényli közvetlenül vagy transzparens proxyn keresztül. A Azure Stack nem támogatja a webproxyk konfigurálását az Internet-hozzáférés engedélyezéséhez. A 80-es és a 443-es portok a graph.windows.net és a login.microsoftonline.com tartományok alatt használatosak.


## <a name="next-steps"></a>Következő lépések

- [Töltse le a ASDK központi telepítési csomagot](asdk-download.md).
- További információ a Közvetlen tárolóhelyekről: [közvetlen tárolóhelyek Overview (áttekintés](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)).
