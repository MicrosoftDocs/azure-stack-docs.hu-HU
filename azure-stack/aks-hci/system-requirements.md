---
title: Azure Kubernetes Service Azure Stack HCI-követelményekre
description: Mielőtt elkezdené az Azure Kubernetes szolgáltatást a Azure Stack HCI-on
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 02/02/2021
ms.openlocfilehash: 16d4e7b1de239ee1b08aa696696796fa6f12dff7
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839742"
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

## <a name="general-network-requirements"></a>Általános hálózati követelmények 

Az alábbi követelmények egy Azure Stack HCI-fürtre és egy Windows Server 2019 Datacenter-fürtre vonatkoznak: 

 - Ellenőrizze, hogy van-e meglévő, külső virtuális kapcsoló konfigurálva, ha a Windows felügyeleti központot használja. Azure Stack HCI-fürtök esetében ennek a kapcsolónak és nevének meg kell egyeznie az összes fürtcsomóponton. 

 - Ellenőrizze, hogy az összes hálózati adapteren letiltotta-e az IPv6 protokollt. 

 - Sikeres telepítés esetén a Azure Stack HCI-fürtcsomópontok és a Kubernetes-fürt virtuális gépei külső internetkapcsolattal kell rendelkezniük.
 
 - Győződjön meg arról, hogy a fürthöz definiált összes alhálózat egymás és az internet között irányítható.
  
 - Ellenőrizze, hogy van-e hálózati kapcsolat a Azure Stack HCI-gazdagépek és a bérlői virtuális gépek között.

 - A DNS-névfeloldás szükséges ahhoz, hogy az összes csomópont kommunikálni tudjon egymással. 

## <a name="ip-address-assignment"></a>IP-cím hozzárendelése  

Azure Stack HCI-ben található AK-ban a virtuális hálózatok IP-címeket foglalnak le az azokat igénylő Kubernetes-erőforrásokhoz a fent felsoroltak szerint. Két hálózati modell közül választhat, attól függően, hogy a kívánt AK-t Azure Stack HCI hálózati architektúrán szeretné-e kiválasztani. 

> [!NOTE]
 > Az Azure Stack HCI-környezetekben itt definiált virtuális hálózati architektúra különbözik az adatközpontban található mögöttes fizikai hálózati architektúrától.

- Statikus IP-hálózatkezelés – a virtuális hálózat statikus IP-címeket foglal le a Kubernetes-fürt API-kiszolgálójára, a Kubernetes-csomópontokra, a mögöttes virtuális gépekre, a terheléselosztó és a fürtön futtatott bármely Kubernetes-szolgáltatásra.

- DHCP-hálózatkezelés – a virtuális hálózat dinamikus IP-címeket foglal le a Kubernetes-csomópontok, a mögöttes virtuális gépek és a terheléselosztó számára egy DHCP-kiszolgáló használatával. A Kubernetes-fürt API-kiszolgálója és a fürt tetején futtatott bármely Kubernetes-szolgáltatás továbbra is statikus IP-címeket foglal le.

### <a name="minimum-ip-address-reservation"></a>Minimális IP-cím foglalása

A telepítéshez legalább a következő IP-címeket kell fenntartania:

| Fürt típusa  | Vezérlési sík csomópont | Munkavégző csomópont | Frissítési műveletekhez | Terheléselosztóval  |
| ------------- | ------------------ | ---------- | ----------| -------------|
| AK-gazdagép |  1 IP |  NA  |  2 IP-CÍM |  NA  |
| Munkaterhelés-fürt  |  1 IP/csomópont  | 1 IP/csomópont |  5 IP-CÍM  |  1 IP |

Emellett a VIP-készlethez a következő IP-címek számát kell fenntartania:

| Erőforrás típusa  | IP-címek száma 
| ------------- | ------------------
| Fürt API-kiszolgálója |  1/fürt 
| Kubernetes-szolgáltatások  |  1/szolgáltatás  

Amint láthatja, a szükséges IP-címek száma változó a Azure Stack HCI architektúra és a Kubernetes-fürtön futtatott szolgáltatások számától függően. Javasoljuk, hogy az üzemelő példányhoz összesen 256 IP-címet (/24 alhálózatot) őrizzen meg.

A hálózati követelményekkel kapcsolatos további információkért tekintse meg a [hálózati fogalmakat az AK-ban Azure stack HCI-ben](./concepts-networking.md).

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

|  Name                         | Tárolási típus | Szükséges kapacitás |
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

 - Windows 10 vagy Windows Server rendszerű számítógép
 - [Regisztrálva az Azure-ban](/windows-server/manage/windows-admin-center/azure/azure-integration)
 - Ugyanabban a tartományban, mint a Azure Stack HCI vagy a Windows Server 2019 Datacenter-fürt

## <a name="next-steps"></a>Következő lépések 

Miután teljesítette a fenti előfeltételeket, beállíthat egy Azure Kubernetes Service hostt Azure Stack HCI-re a következő használatával:
 - [Windows felügyeleti központ](setup.md)
 - [PowerShell](setup-powershell.md)
