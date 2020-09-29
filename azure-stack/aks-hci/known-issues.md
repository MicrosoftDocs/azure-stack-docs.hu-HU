---
title: Az Azure Kubernetes szolgáltatás ismert problémái a Azure Stack HCI-ben
description: Az Azure Kubernetes szolgáltatás ismert problémái a Azure Stack HCI-ben
author: abha
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 89324bbfe2c1f28e268a22a9b31c880daaddecd6
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948903"
---
# <a name="known-issues-for-azure-kubernetes-service-on-azure-stack-hci-public-preview"></a>Az Azure Kubernetes szolgáltatás ismert problémái a Azure Stack HCI nyilvános előzetes verziójában
Ez a cikk az Azure Kubernetes szolgáltatás nyilvános előzetes kiadásával kapcsolatos ismert problémákat ismerteti Azure Stack HCI-ben.

## <a name="recovering-from-a-failed-aks-on-azure-stack-hci-deployment"></a>Helyreállítás sikertelen AK-ról Azure Stack HCI üzemelő példányon
Ha üzembe helyezési problémák léptek fel, vagy alaphelyzetbe szeretné állítani a telepítést, győződjön meg róla, hogy az uninstall-AksHci PowerShell felügyeleti ablakból való futtatása előtt bezárta az Azure Kubernetes Azure Stack szolgáltatáshoz kapcsolódó összes Windows felügyeleti központot.

## <a name="when-using-kubectl-to-delete-a-node-the-associated-vm-might-not-be-deleted"></a>Ha a kubectl használatával töröl egy csomópontot, előfordulhat, hogy a társított virtuális gép nem törlődik.
Az alábbi lépéseket követve teljesítheti ezt a problémát:
* Kubernetes-fürt létrehozása
* A fürt skálázása kettőnél több csomópontra
* Csomópont törlése a kubectl <csomópont-név> használatával 
* Kubectl-lekérési csomópontok futtatása. Az eltávolított csomópont nem szerepel a kimenetben
* PowerShell-felügyeleti ablak megnyitása
* Futtassa a Get-VM parancsot. Az eltávolított csomópont továbbra is megjelenik

Ez azt eredményezi, hogy a rendszer nem ismeri fel a csomópontot, és egy új csomópont nem fog elindulni. Ez egy jövőbeli kiadásban lesz kijavítva

## <a name="time-synchronization-must-be-configured-across-all-physical-cluster-nodes-and-in-hyper-v"></a>Az időszinkronizálást az összes fizikai fürtcsomóponton és a Hyper-V szolgáltatásban kell konfigurálni
A gMSA és az AD-hitelesítés működésének biztosítása érdekében győződjön meg arról, hogy a Azure Stack HCI-fürtcsomópontok úgy vannak konfigurálva, hogy a tartományvezérlővel vagy más időforrással szinkronizálják az idejüket, és a Hyper-V úgy legyen konfigurálva, hogy a virtuális gépek számára is szinkronizáljon időt.

## <a name="special-active-directory-permissions-are-needed-for-domain-joined-azure-stack-hci-nodes"></a>Speciális Active Directory engedélyek szükségesek a tartományhoz csatlakoztatott Azure Stack HCI-csomópontokhoz 
Az Azure Kubernetes Service-t Azure Stack HCI-re telepítő felhasználóknak "teljes hozzáférés" engedéllyel kell rendelkezniük az AD-objektumok létrehozásához az Active Directory tárolóban, ahol a kiszolgáló és a szolgáltatás objektumai jönnek létre. 

## <a name="get-akshcilogs-command-may-fail"></a>A Get-AksHciLogs parancs sikertelen lehet
Nagyméretű fürtök esetén a Get-AksHciLogs parancs kivételt okozhat, a csomópontok enumerálása nem sikerült, vagy nem fog c:\wssd\wssdlogs.zip kimeneti fájlt előállítani.
Ennek az az oka, hogy a fájlok zip-fájljának a PowerShell-parancsa `Compress-Archive` 2 GB-os kimeneti fájlméretet tartalmaz. Ezt a problémát egy későbbi kiadásban rögzíti a rendszer.

## <a name="azure-kubernetes-service-powershell-deployment-doesnt-check-for-available-memory-before-creating-a-new-target-cluster"></a>Az Azure Kubernetes Service PowerShell üzembe helyezése nem keres rendelkezésre álló memóriát az új célként megadott fürt létrehozása előtt
Az AK-HCI PowerShell-parancsok nem ellenőrzik a gazdagép-kiszolgáló rendelkezésre álló memóriáját a Kubernetes-csomópontok létrehozása előtt. Ez a memória-kimerültség és a virtuális gépek elindításához vezethet. Ez a hiba jelenleg nem megfelelően van kezelve, és a központi telepítés nem egyértelmű hibaüzenetet kap.
Ha olyan központi telepítéssel rendelkezik, amely úgy tűnik, hogy a `Eventviewer` virtuális gép elindításához nincs elég memória a Hyper-V-vel kapcsolatos hibaüzenetek megadásához.
Ezt a problémát egy későbbi kiadásban rögzíti a rendszer

## <a name="azure-kubernetes-service-deployment-fails-on-an-azure-stack-hci-configured-with-static-ips-vlans-sdn-or-proxies"></a>Az Azure Kubernetes szolgáltatás telepítése nem sikerül egy statikus IP-címmel, VLAN-okra, SDN-re vagy proxyra konfigurált Azure Stack HCI-ben.
Az Azure Kubernetes szolgáltatás üzembe helyezése egy olyan Azure Stack HCI-fürtön, amelyen statikus IP-címek, VLAN-ok, SDN vagy proxyk vannak, a telepítés a fürt létrehozásakor meghiúsul. Ezt a problémát egy későbbi kiadásban rögzíti a rendszer.

## <a name="ipv6-must-be-disabled-in-the-hosting-environment"></a>Az IPv6-ot a üzemeltetési környezetben le kell tiltani
Ha az IPv4-és IPv6-címek is a fizikai hálózati adapterhez vannak kötve, a `cloudagent` fürtözési szolgáltatás az IPv6-címet használja a kommunikációhoz. A telepítési keretrendszer többi összetevője csak az IPv4 protokollt használja. Ez azt eredményezi, hogy a Windows felügyeleti központ nem tud csatlakozni a fürthöz, és a rendszer a számítógéphez való csatlakozásra tett kísérlet során távelérési hibát jelez.
Megkerülő megoldás: tiltsa le az IPv6-t a fizikai hálózati adaptereken.
Ezt a problémát egy későbbi kiadásban rögzíti a rendszer

## <a name="moving-virtual-machines-between-azure-stack-hci-cluster-nodes-quickly-leads-to-vm-startup-failures"></a>A virtuális gépek Azure Stack HCI-fürtcsomópontok közötti áthelyezése gyorsan megkezdi a VM indítási hibáit
Ha a Fürtfelügyelő eszköz használatával helyez át egy virtuális gépet az egyik csomópontról (A csomópontból) egy másik csomópontra (B csomópont) a Azure Stack HCI-fürtben, előfordulhat, hogy a virtuális gép nem indul el az új csomóponton. Miután visszahelyezte a virtuális gépet az eredeti csomópontra, az nem fog elindulni.
Ez a probléma azért fordul elő, mert az első áttelepítés törlésére szolgáló logika aszinkron módon fut. Ennek eredményeképpen az Azure Kubernetes-szolgáltatás "VM-hely frissítése" logikája megkeresi a virtuális gépet az A csomóponton található eredeti Hyper-V-n, és törli a regisztráció törlése helyett.
Áthidaló megoldás: Győződjön meg arról, hogy a virtuális gép sikeresen elindult az új csomóponton, mielőtt visszahelyezi azt az eredeti csomópontra.
Ezt a problémát egy későbbi kiadásban rögzíti a rendszer

## <a name="load-balancer-in-azure-kubernetes-service-requires-dhcp-reservation"></a>A Load Balancer az Azure Kubernetes szolgáltatásban DHCP-foglalást igényel
Az Azure Kubernetes Service on Azure Stack HCI terheléselosztási megoldása DHCP használatával rendeli hozzá az IP-címeket a szolgáltatási végpontokhoz. Ha a szolgáltatás-végpont IP-címe a szolgáltatás újraindítása miatt megváltozik, a DHCP-bérlet rövid lejárati idő miatt lejár. A szolgáltatás ezért elérhetetlenné válik, mert a Kubernetes konfigurációjának IP-címe eltér a végponttól. Ennek eredményeként előfordulhat, hogy a Kubernetes-fürt elérhetetlenné válik.
A probléma megkerüléséhez használjon MAC-címkészletet a terheléselosztási szolgáltatás végpontjai számára, és foglaljon le adott IP-címeket a készlet minden MAC-címéhez.
Ezt a problémát egy későbbi kiadásban rögzíti a rendszer.

## <a name="cannot-deploy-azure-kubernetes-service-to-an-environment-that-has-separate-storage-and-compute-clusters"></a>Az Azure Kubernetes szolgáltatás nem telepíthető olyan környezetbe, amely külön tárolási és számítási fürtöket tartalmaz
A Windows felügyeleti központ nem telepíti az Azure Kubernetes szolgáltatást külön tárolási és számítási fürtökkel rendelkező környezetbe, mivel a számítási és tárolási erőforrásokat ugyanazon fürt biztosítja. A legtöbb esetben nem fog megjelenni a számítási fürt által elérhetővé tett CSV, és a rendszer elutasítja a telepítés folytatását.
Ezt a problémát egy későbbi kiadásban rögzíti a rendszer.
