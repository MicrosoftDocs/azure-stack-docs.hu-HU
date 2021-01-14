---
title: Azure Kubernetes Service Azure Stack HCI-követelményekre
description: Mielőtt elkezdené az Azure Kubernetes szolgáltatást a Azure Stack HCI-on
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 12/02/2020
ms.openlocfilehash: 71c842cf44963988da7926003646b246bf80f802
ms.sourcegitcommit: 8776cbe4edca5b63537eb10bcd83be4b984c374a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2021
ms.locfileid: "98175736"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes Service rendszerkövetelményei a Azure Stack HCI rendszeren

> A következőkre vonatkozik: Azure Stack HCI, Windows Server 2019 Datacenter

Ez a cikk az Azure Kubernetes Service Azure Stack HCI-ben vagy a Windows Server 2019 Datacenter rendszerben való beállításának követelményeit, valamint a Kubernetes-fürtök létrehozását ismerteti. Az Azure Kubernetes Service Azure Stack HCI-on való áttekintését lásd: [ak Azure stack HCI – áttekintés](overview.md).

## <a name="determine-hardware-requirements"></a>Hardverkövetelmények meghatározása

A Microsoft azt javasolja, hogy a partnereinktől kapott hitelesített Azure Stack HCI hardver/szoftver megoldást. Ezek a megoldások a hivatkozási architektúránk alapján lettek kialakítva, összeállítva és érvényesítve, így biztosítva a kompatibilitást és a megbízhatóságot, így gyorsan üzembe helyezhető. Győződjön meg arról, hogy a használt rendszerek, összetevők, eszközök és illesztőprogramok a Windows Server Catalog szolgáltatásban a Windows Server 2019 Certified minősítéssel rendelkeznek. Látogasson el az [Azure stack HCI Solutions](https://azure.microsoft.com/overview/azure-stack/hci) webhelyére az ellenőrzött megoldásokhoz.

## <a name="general-requirements"></a>Általános követelmények

Az Azure Kubernetes Service on Azure Stack HCI vagy a Windows Server 2019 Datacenter optimális működéséhez Active Directory környezetben ellenőrizze, hogy teljesülnek-e az alábbi követelmények: 

 - Győződjön meg arról, hogy az időszinkronizálás beállítása be van állítva, és az eltérés nem nagyobb, mint 2 perc az összes fürtcsomóponton és a tartományvezérlőn. Az időszinkronizálás beállításával kapcsolatos információkért lásd: [Windows Time Service](/windows-server/networking/windows-time-service/windows-time-service-top). 

 - Győződjön meg arról, hogy a felhasználói fiók (ok) frissíti a frissítéseket, és felügyeli az Azure Kubernetes szolgáltatást Azure Stack HCI vagy a Windows Server 2019 Datacenter-fürtökön a megfelelő engedélyekkel Active Directory. Ha szervezeti egységeket (OU-ket) használ a kiszolgálókhoz és szolgáltatásokhoz tartozó csoportházirendek kezelésére, a felhasználói fiók (ok) listának, olvasási, módosítási és törlési engedélyekkel kell rendelkeznie a szervezeti egység összes objektumához. 

 - Javasoljuk, hogy a kiszolgálók és szolgáltatások különálló szervezeti egységét használja, amelyhez hozzáadja az Azure Kubernetes szolgáltatást Azure Stack HCI vagy Windows Server 2019 Datacenter-fürtökhöz. Egy külön szervezeti egység használatával több részletességgel vezérelheti a hozzáférést és az engedélyeket.

 - Ha a Active Directory lévő tárolók GPO-sablonjait használ, győződjön meg arról, hogy az AK-HCI üzembe helyezése mentesül a szabályzat alól. A kiszolgáló megerősítése egy későbbi előzetes kiadásban lesz elérhető.

## <a name="compute-requirements"></a>Számítási követelmények

 - Egy Azure Stack HCI-fürt vagy egy Windows Server 2019 Datacenter feladatátvevő fürt, amely legfeljebb négy kiszolgálóval rendelkezik a fürtben. Azt javasoljuk, hogy a fürt minden kiszolgálóján legalább 24 CPU-mag és legalább 256 GB RAM legyen.

 - Noha az Azure Kubernetes szolgáltatást technikailag egyetlen, Windows Server 2019 Datacenter rendszerű csomóponton is futtathatja, ezt nem javasoljuk. Az Azure Kubernetes szolgáltatást azonban egyetlen, Windows Server 2019 Datacenter rendszerű csomóponton is futtathatja értékelés céljából.

 - Az Azure Kubernetes Service Azure Stack HCI-re vonatkozó egyéb számítási követelményei összhangban vannak Azure Stack HCI követelményeivel. Azure Stack HCI-kiszolgáló követelményeivel kapcsolatos további információkért látogasson el [Azure stack HCI rendszerkövetelményeit](../hci/concepts/system-requirements.md#server-requirements) .

 - Ehhez az előzetes kiadáshoz az Azure Stack HCI operációs rendszert kell telepíteni a fürt minden egyes kiszolgálójára az EN-US régió és a nyelv kiválasztása alapján. a telepítés után történő módosítás jelenleg nem elegendő.

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények 

Az alábbi követelmények egy Azure Stack HCI-fürtre és egy Windows Server 2019 Datacenter-fürtre vonatkoznak: 

 - Ellenőrizze, hogy van-e meglévő, külső virtuális kapcsoló konfigurálva, ha a Windows felügyeleti központot használja. Azure Stack HCI-fürtök esetében ennek a kapcsolónak és nevének meg kell egyeznie az összes fürtcsomóponton. 

 - Ellenőrizze, hogy az összes hálózati adapteren letiltotta-e az IPv6 protokollt. 

 - Sikeres telepítés esetén a Azure Stack HCI-fürtcsomópontok és a Kubernetes-fürt virtuális gépei külső internetkapcsolattal kell rendelkezniük.
  
 - Ellenőrizze, hogy van-e hálózati kapcsolat a Azure Stack HCI-gazdagépek és a bérlői virtuális gépek között.

 - A DNS-névfeloldás szükséges ahhoz, hogy az összes csomópont kommunikálni tudjon egymással. A Kubernetes külső névfeloldáshoz használja a DHCP-kiszolgáló által biztosított DNS-kiszolgálókat, amikor az IP-cím beszerzése megtörténik. A belső névfeloldás Kubernetes használja az alapértelmezett Kubernetes Core DNS-alapú megoldást. 

 - Ebben az előzetes kiadásban csak egyetlen VLAN-támogatást biztosítunk a teljes telepítéshez. 

 - Ebben az előzetes kiadásban korlátozott a PowerShell használatával létrehozott Kubernetes-fürtök proxy-támogatása. 
 
### <a name="ip-address-assignment"></a>IP-cím hozzárendelése  
 
A Azure Stack HCI üzembe helyezésének sikeres AK-ra vonatkozó részeként azt javasoljuk, hogy a DHCP-kiszolgálóval konfigurálja a virtuális IP-címkészlet tartományát. Azt is javasoljuk, hogy az összes munkaterhelés-fürthöz három – öt, magasan elérhető vezérlési sík-csomópontot konfiguráljon. 

> [!NOTE]
> A statikus IP-címek hozzárendelésének használata önmagában nem támogatott. Az előzetes kiadás részeként konfigurálnia kell egy DHCP-kiszolgálót.

#### <a name="dhcp"></a>DHCP
Kövesse ezeket a követelményeket, miközben DHCP-t használ az IP-címek fürtön belüli hozzárendeléséhez:  

 - A hálózatnak rendelkeznie kell egy elérhető DHCP-kiszolgálóval a virtuális gépek és a virtuális gépek gazdagépei számára a TCP/IP-címek biztosításához. A DHCP-kiszolgálónak tartalmaznia kell a Network Time Protocol (NTP) és a DNS-gazdagép adatait is.
 
 - Az Azure Stack HCI-fürt által elérhető IPv4-címek dedikált hatókörű DHCP-kiszolgáló.
 
 - A DHCP-kiszolgáló által megadott IPv4-címeknek irányíthatónak kell lenniük, és 30 napos bérlettel kell rendelkezniük, hogy elkerüljék az IP-kapcsolat elvesztését a virtuális gépek frissítése vagy újraépítése esetén.  

Legalább a következő számú DHCP-címet le kell foglalni:

| Fürt típusa  | Vezérlési sík csomópont | Munkavégző csomópont | Frissítés | Terheléselosztóval  |
| ------------- | ------------------ | ---------- | ----------| -------------|
| AK-gazdagép |  1  |  0  |  2  |  0  |
| Munkaterhelés-fürt  |  1/csomópont  | 1/csomópont |  5  |  1  |

Megtekintheti, hogy a szükséges IP-címek száma milyen változó a környezetében lévő munkaterhelés-fürtök és a vezérlési sík és a munkavégző csomópontok számától függően. Javasoljuk, hogy a DHCP IP-készletben 256 IP-címet (/24 alhálózatot) őrizzen meg.
  
    
#### <a name="vip-pool-range"></a>VIP-készlet tartománya

A virtuális IP-(VIP-) készletek használata erősen ajánlott egy AK-ra Azure Stack HCI-környezetben. A VIP-készletek a hosszú élettartamú üzemelő példányok számára fenntartott statikus IP-címek, amelyek biztosítják, hogy a központi telepítés és az alkalmazás számítási feladatai mindig elérhetők legyenek. Jelenleg csak IPv4-címeket támogatunk, ezért ellenőriznie kell, hogy az összes hálózati adapteren le van-e tiltva az IPv6. Győződjön meg arról is, hogy a virtuális IP-címek nem részei a DHCP IP-címének.

Legalább egy IP-címet le kell foglalni a fürtön (munkaterhelés és AK-gazdagép) és egy IP-címet a Kubernetes szolgáltatásban. A VIP-készlet tartományában a szükséges IP-címek száma a környezetében használt munkaterhelés-fürtök és Kubernetes-szolgáltatások számától függ. Javasoljuk, hogy 16 statikus IP-címet őrizzen meg az AK-HCI üzembe helyezéshez. 

Az AK-gazdagép beállításakor a és a `-vipPoolStartIp` `-vipPoolEndIp` paraméter használatával `Set-AksHciConfig` hozzon létre egy VIP-készletet.

#### <a name="mac-pool-range"></a>MAC-készlet tartománya
Javasoljuk, hogy a tartományon belül legalább 16 MAC-címet engedélyezzen, hogy az egyes fürtökön több vezérlési sík csomópont legyen. Az AK-gazdagép beállításakor használja a `-macPoolStart` és a `-macPoolEnd` paramétert a `Set-AksHciConfig` Kubernetes-szolgáltatások DHCP Mac-KÉSZLETÉBEN található MAC-címek lefoglalásához.
  
### <a name="network-port-and-url-requirements"></a>A hálózati port és az URL-cím követelményei 

Ha Azure Stack HCI-ben hoz létre Azure Kubernetes-fürtöt, a rendszer automatikusan megnyitja a következő tűzfal-portokat a fürt minden kiszolgálóján. 


| Tűzfal portja               | Leírás     | 
| ---------------------------- | ------------ | 
| 45000           | wssdagent GPRC-kiszolgáló portja     |
| 45001             | wssdagent GPRC hitelesítési port  | 
| 55000           | wssdcloudagent GPRC-kiszolgáló portja      |
| 65000            | wssdcloudagent GPRC hitelesítési port  | 


A tűzfal URL-címére vonatkozó kivételek a Windows felügyeleti központ számítógépén és az Azure Stack HCI-fürt összes csomópontján szükségesek. 

| URL-cím        | Port | Szolgáltatás | Jegyzetek |
| ---------- | ---- | --- | ---- |
https://helm.sh/blog/get-helm-sh/  | 443 | Ügynök letöltése, WAC | A Helm bináris fájljainak letöltésére használatos 
https://storage.googleapis.com/  | 443 | Felhőbeli init | Kubernetes bináris fájljainak letöltése 
https://azurecliprod.blob.core.windows.net/ | 443 | Felhőbeli init | Bináris fájlok és tárolók letöltése 
https://aka.ms/installazurecliwindows | 443 | WAC | Az Azure CLI letöltése 
https://:443 | 443 | TCP | Az Azure arc-ügynökök támogatásához használatos  
*.blob.core.windows.net | 443 | TCP | Letöltésekhez szükséges
*. api.cdp.microsoft.com, *. dl.delivery.mp.microsoft.com, *. emdl.ws.microsoft.com | 80, 443 | Ügynök letöltése | Metaadatok letöltése 
*. dl.delivery.mp.microsoft.com, *. do.dsp.mp.microsoft.com. | 80, 443 | Ügynök letöltése | VHD-lemezképek letöltése 
ecpacr.azurecr.io | 443 | Kubernetes | Tároló lemezképének letöltése 
git://:9418 | 9418 | TCP | Az Azure arc-ügynökök támogatásához használatos 

## <a name="storage-requirements"></a>Tárolási követelmények 

Az Azure Kubernetes Service a következő tárolási implementációkat támogatja az Azure Stack HCI-ben: 

|  Név                         | Tárolási típus | Szükséges kapacitás |
| ---------------------------- | ------------ | ----------------- |
| Azure Stack HCI-fürt          | CSV          | 1 TB              |
| Windows Server 2019 Datacenter feladatátvevő fürt          | CSV          | 1 TB              |
| Egyetlen csomópontos Windows Server 2019 Datacenter | Közvetlenül csatlakoztatott tároló | 500 GB|

## <a name="review-maximum-supported-hardware-specifications"></a>A támogatott hardverek maximális specifikációjának áttekintése 

Az Azure Kubernetes szolgáltatás a következő specifikációkat meghaladó Azure Stack HCI üzemelő példányokon nem támogatott: 

| Erőforrás                     | Maximum |
| ---------------------------- | --------|
| Fizikai kiszolgálók/fürt | 4       |
| Kubernetes-fürtök            | 4       |
| Virtuális gépek teljes száma          | 200     |

## <a name="windows-admin-center-requirements"></a>A Windows felügyeleti központ követelményei

A Windows felügyeleti központ a felhasználói felület az Azure Kubernetes szolgáltatás létrehozásához és kezeléséhez Azure Stack HCI-on. Ha Azure Stack HCI-on szeretné használni a Windows felügyeleti központot az Azure Kubernetes szolgáltatással, meg kell felelnie az alábbi lista összes feltételének. 

Az alábbi követelmények vonatkoznak a Windows felügyeleti központ átjáróját futtató gépre: 

 - Windows 10 vagy Windows Server rendszerű számítógép (jelenleg nem támogatott a Windows felügyeleti központ futtatása a Azure Stack HCI vagy a Windows Server 2019 Datacenter-fürtön)
 - 60 GB szabad terület
 - Regisztrálva az Azure-ban
 - Ugyanabban a tartományban, mint a Azure Stack HCI vagy a Windows Server 2019 Datacenter-fürt

## <a name="next-steps"></a>Következő lépések 

Miután teljesítette a fenti előfeltételeket, beállíthat egy Azure Kubernetes Service hostt Azure Stack HCI-re a következő használatával:
 - [Windows felügyeleti központ](setup.md)
 - [PowerShell](setup-powershell.md)