---
title: Azure Kubernetes Service Azure Stack HCI-követelményekre
description: Mielőtt elkezdené az Azure Kubernetes szolgáltatást a Azure Stack HCI-on
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 09/22/2020
ms.openlocfilehash: b4f79281987b425b907126081e74ce32bf9ee14d
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948951"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes Service rendszerkövetelményei a Azure Stack HCI rendszeren

> A következőkre vonatkozik: Azure Stack HCI

Ez a cikk az Azure Kubernetes Service Azure Stack HCI-ben való beállításával és a Kubernetes-fürtök létrehozásához szükséges követelményeket ismerteti. Az Azure Kubernetes Service Azure Stack HCI-on való áttekintését lásd: [ak Azure stack HCI – áttekintés](overview.md).

## <a name="determine-hardware-requirements"></a>Hardverkövetelmények meghatározása

A Microsoft azt javasolja, hogy a partnereinktől kapott hitelesített Azure Stack HCI hardver/szoftver megoldást. Ezek a megoldások a hivatkozási architektúránk alapján lettek kialakítva, összeállítva és érvényesítve, így biztosítva a kompatibilitást és a megbízhatóságot, így gyorsan üzembe helyezhető. Győződjön meg arról, hogy a használt rendszerek, összetevők, eszközök és illesztőprogramok a Windows Server Catalog szolgáltatásban a Windows Server 2019 Certified minősítéssel rendelkeznek. Látogasson el az [Azure stack HCI Solutions](https://azure.microsoft.com/overview/azure-stack/hci) webhelyére az ellenőrzött megoldásokhoz.

### <a name="general-requirements"></a>Általános követelmények

Ahhoz, hogy a Azure Stack HCI-ben az Azure Kubernetes szolgáltatás optimálisan működjön egy Active Directory környezetben, győződjön meg arról, hogy teljesülnek az alábbi követelmények: 

 - Győződjön meg arról, hogy az időszinkronizálás beállítása beállítás, és az eltérés nem nagyobb, mint 2 perc az összes fürtcsomóponton és a tartományvezérlőn. Az időszinkronizálás beállításával kapcsolatos információkért látogasson el a [Windows időszolgáltatásra](/windows-server/networking/windows-time-service/windows-time-service-top). 

 - Győződjön meg arról, hogy a (z) Azure Stack HCI-fürtökön az Azure Kubernetes szolgáltatás hozzáadását, frissítését és felügyeletét biztosító felhasználói fiók megfelelő engedélyekkel rendelkezik Active Directory. Ha szervezeti egységeket (OU-ket) használ a kiszolgálókhoz és szolgáltatásokhoz tartozó csoportházirendek kezelésére, a felhasználói fiók (ok) listának, olvasási, módosítási és törlési engedélyekkel kell rendelkeznie a szervezeti egység összes objektumához. 

 - Javasoljuk, hogy használjon külön szervezeti egységet azon kiszolgálók és szolgáltatások számára, amelyekhez hozzáadja az Azure Kubernetes szolgáltatást Azure Stack HCI-fürtökön. Ez lehetővé teszi a hozzáférés és az engedélyek további részletességgel történő szabályozását.

 - Ha a Active Directory lévő tárolók GPO-sablonjait használ, győződjön meg arról, hogy az AK-HCI üzembe helyezése mentesül a szabályzat alól. A kiszolgáló megerősítése egy későbbi előzetes kiadásban lesz elérhető.

### <a name="compute-requirements"></a>Számítási követelmények

 - Egy Azure Stack HCI-fürt, amely legfeljebb négy kiszolgálóval rendelkezik a fürtben. Azt javasoljuk, hogy a fürt minden kiszolgálóján legalább 24 CPU-mag és legalább 512 GB RAM legyen.

 - Noha az Azure Kubernetes szolgáltatást technikailag egyetlen csomóponton Azure Stack HCI-kiszolgálón is futtathatja, ezt nem javasoljuk.

 - Az Azure Kubernetes Service Azure Stack HCI-re vonatkozó egyéb számítási követelményei összhangban vannak Azure Stack HCI követelményeivel. Az Azure Stack HCI-kiszolgáló követelményeivel kapcsolatos további részletekért látogasson el Azure Stack a HCI-re [vonatkozó követelményekre](../hci/deploy/before-you-start.md) .  

 - Ehhez az előzetes kiadáshoz az Azure Stack HCI operációs rendszert kell telepíteni a fürt minden egyes kiszolgálójára az EN-US régió és a nyelv kiválasztása alapján. a telepítés után történő módosítás jelenleg nem elegendő.

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények 

Az Azure Kubernetes Service on Azure Stack HCI-ben megbízható, nagy sávszélességű, kis késleltetésű hálózati kapcsolatra van szükség az egyes kiszolgálói csomópontok között. Ellenőrizze a következőket: 

 - Ellenőrizze, hogy van-e meglévő, külső virtuális kapcsoló konfigurálva, ha a Windows felügyeleti központot használja. Azure Stack HCI-fürtök esetében ennek a kapcsolónak meg kell egyeznie az összes fürtcsomóponton. 

 - Ellenőrizze, hogy az összes hálózati adapteren letiltotta-e az IPv6 protokollt. 

 - A hálózatnak rendelkeznie kell egy elérhető DHCP-kiszolgálóval, amely TCP/IP-címeket biztosít a virtuális gépek és a virtuálisgép-gazdagépek számára. A DHCP-kiszolgálónak az NTP és a DNS-gazdagép adatait is tartalmaznia kell. 

 - Azt javasoljuk továbbá, hogy a Azure Stack HCI-fürt által elérhető IPv4-címek dedikált hatókörű DHCP-kiszolgáló legyen. Lefoglalhatja például az alapértelmezett átjáró 10.0.1.1, 10.0.1.2 a Kubernetes-szolgáltatások 10.0.1.102, és 10.0.1.103-10.0.1.254 használhat a Kubernetes-fürtön futó virtuális gépekhez. 

 - A DHCP-kiszolgáló által megadott IPv4-címeknek irányíthatónak kell lenniük, és a virtuális gépek frissítése vagy újraépítése esetén az IP-kapcsolat elvesztésének elkerülése érdekében a rendszer 7 napos bérlettel rendelkezik.  

 - A VLAN-címkék nem ajánlottak. Használjon hozzáférési vagy címkézetlen portokat a Azure Stack HCI-fürt hálózati kapcsolókon. 

 - Nem ajánlott dedikált statikus virtuális IP-készletet használni a terheléselosztó virtuális IP-készletéhez a telepítés során. A rendszer a DHCP IP-készletet használja a virtuális gépekhez, míg a virtuális IP-készlet a terheléselosztó számára van használatban, és irányíthatónak kell lennie. A DHCP IP-készletnek nem kell irányíthatónak lennie a külső internethez.

 - A DNS-névfeloldás szükséges ahhoz, hogy az összes csomópont kommunikálni tudjon egymással. A Kubernetes külső névfeloldás esetén a DHCP-kiszolgáló által biztosított DNS-kiszolgálókat használjuk az IP-cím beszerzése során. A Kubernetes belső névfeloldáshoz az alapértelmezett Kubernetes Core DNS-alapú megoldást használjuk. 

 - Ebben az előzetes kiadásban nem támogatjuk a proxykiszolgáló használatát a Windows felügyeleti központ, Azure Stack HCI fürtcsomópontok és az Azure Kubernetes szolgáltatás Azure Stack HCI-fürtcsomópontokon az interneten való összekapcsolásához.

### <a name="network-port-and-url-requirements"></a>A hálózati port és az URL-cím követelményei 

Ha Azure Stack HCI-on hoz létre Azure Kubernetes-fürtöt, a rendszer automatikusan megnyitja a következő tűzfal-portokat a fürt minden kiszolgálóján. 


| Tűzfal portja               | Leírás         | 
| ---------------------------- | ------------ | 
| 45000           | wssdagent GPRC-kiszolgáló portja           |
| 45001             | wssdagent GPRC hitelesítési port  | 
| 55000           | wssdcloudagent GPRC-kiszolgáló portja           |
| 55001             | wssdcloudagent GPRC hitelesítési port  | 


A tűzfal URL-címére vonatkozó kivételek a Windows felügyeleti központ számítógépén és az Azure Stack HCI-fürt összes csomópontján szükségesek. 

| URL-cím        | Port | Szolgáltatás | Jegyzetek |
| ---------- | ---- | --- | ---- |
https://get.helm.sh/  | 443 | Ügynök letöltése, WAC | A Helm bináris fájljainak letöltésére használatos 
https://storage.googleapis.com/  | 443 | Felhőbeli init | Kubernetes bináris fájljainak letöltése 
https://azurecliprod.blob.core.windows.net/ | 443 | Felhőbeli init | Bináris fájlok és tárolók letöltése 
https://aka.ms/installazurecliwindows | 443 | WAC | Az Azure CLI letöltése 
*. api.cdp.microsoft.com, *. dl.delivery.mp.microsoft.com, *. emdl.ws.microsoft.com | 80, 443 | Ügynök letöltése | Metaadatok letöltése 
*. dl.delivery.mp.microsoft.com, *. do.dsp.mp.microsoft.com. | 80, 443 | Ügynök letöltése | VHD-lemezképek letöltése 
ecpacr.azurecr.io | 443 | Kubernetes | Tároló lemezképének letöltése 

### <a name="storage-requirements"></a>Tárolási követelmények 

Az Azure Kubernetes Service a következő tárolási implementációkat támogatja az Azure Stack HCI-ben: 

|  Name                         | Tárhelytípusa | Szükséges kapacitás |
| ---------------------------- | ------------ | ----------------- |
| Azure Stack HCI-fürt          | CSV          | 1 TB              |
| Egy csomópontos Azure Stack HCI | Közvetlenül csatlakoztatott tároló | 500 GB|

### <a name="review-maximum-supported-hardware-specifications"></a>A támogatott hardverek maximális specifikációjának áttekintése 

Az Azure Kubernetes szolgáltatás a következő specifikációkat meghaladó Azure Stack HCI üzemelő példányokon nem támogatott: 

| Erőforrás                     | Maximum |
| ---------------------------- | --------|
| Fizikai kiszolgálók/fürt | 4       |
| Kubernetes-fürtök            | 4       |
| Virtuális gépek teljes száma          | 200     |

### <a name="windows-admin-center"></a>Windows felügyeleti központ 

A Windows felügyeleti központ a felhasználói felület az Azure Kubernetes szolgáltatás létrehozásához és kezeléséhez Azure Stack HCI-on. Ha Azure Stack HCI-on szeretné használni a Windows felügyeleti központot az Azure Kubernetes szolgáltatással, meg kell felelnie az alábbi lista összes feltételének. 

#### <a name="on-your-windows-admin-center-system"></a>Windows felügyeleti központ rendszeren

A Windows felügyeleti központ átjáróját futtató gépnek a következőket kell tennie: 

 - Windows 10 (jelenleg nem támogatjuk a Windows felügyeleti központ kiszolgálóit)
 - 60 GB szabad terület
 - Regisztrálva az Azure-ban
 - Ugyanabban a tartományban, mint a Azure Stack HCI-fürt

## <a name="next-steps"></a>Következő lépések 

Miután teljesítette a fenti előfeltételeket, beállíthat egy Azure Kubernetes Service hostt Azure Stack HCI-re a következő használatával:
 - [Windows felügyeleti központ](setup.md)
 - [PowerShell](setup-powershell.md)
