---
title: Támogatási szabályzatok az AK motorhoz a Azure Stack-on | Microsoft Docs
description: Ez a témakör a Azure Stack-on található AK-motor támogatási szabályzatait tartalmazza.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 8c83e0b006904482634e50bdf217ec5d236e4e5c
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73594958"
---
# <a name="support-policies-for-aks-engine-on-azure-stack"></a>Támogatási szabályzatok az AK motorhoz Azure Stack

*A következőkre vonatkozik: Azure Stack integrált rendszerek és Azure Stack Development Kit*

Ez a cikk részletesen ismerteti a technikai támogatási szabályzatokat és a Azure Stack-on található AK-motorra vonatkozó korlátozásokat. A cikk a Kubernetes Marketplace-elemek, a harmadik féltől származó nyílt forráskódú összetevők, valamint a biztonság vagy a javítások kezelését is ismerteti. 

> [!IMPORTANT]
> Az AK-motor jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="self-managed-kubernetes-clusters-on-azure-stack-with-aks-engine"></a>Önfelügyelt Kubernetes-fürtök Azure Stack az AK-motorral

Az infrastruktúra-szolgáltatás (IaaS) Felhőbeli összetevői, például a számítási vagy hálózatkezelési összetevők hozzáférést biztosítanak a felhasználóknak az alacsony szintű vezérlőkhöz és a testreszabási lehetőségekhez. A dobás lehetővé teszi a felhasználó számára, hogy a Kubernetes-fürtöket transzparens módon használja, így a felhasználók hozzáférhetnek és befolyásolhatják az üzembe helyezésük minden aspektusát.

Fürt létrehozásakor az ügyfél meghatározza a Kubernetes-főkiszolgálókat és a munkavégző csomópontokat, amelyeket az AK-motor hoz létre. Az ügyfelek számítási feladatait ezeken a csomópontokon hajtja végre. A saját ügyfelei, és megtekinthetik és módosíthatják a fő-és munkavégző csomópontokat. A gondatlanul módosított csomópontok adatvesztést okozhatnak az adatok és a munkaterhelések esetében, és a fürt nem működőképes állapotba helyezhetők. Emellett az KABAi motor műveletei, például a verziófrissítés vagy a skálázás felülírják az összes nem kötött módosítást. Ha például a fürt statikus hüvelyrel rendelkezik, ezek nem lesznek megtartva az AK-motor frissítési művelete után.

Mivel az ügyfél-fürtcsomópontok saját kódokat hajtanak végre, és bizalmas adatokat tárolnak, Microsoft ügyfélszolgálata csak korlátozott módon férhetnek hozzájuk. Microsoft ügyfélszolgálata nem tud bejelentkezni a alkalmazásba, végrehajthatja a parancsokat a-ben, vagy megtekintheti a csomópontok naplóit kifejezett ügyfél engedélye vagy segítsége nélkül.

## <a name="aks-engine-supported-areas"></a>AK-motor által támogatott területek

A Microsoft technikai támogatást nyújt a következőkhöz:

-  Az KABAi motor parancsaival kapcsolatos problémák: üzembe helyezés, előállítás, frissítés és méretezés. Az eszköznek konzisztensnek kell lennie az Azure-beli viselkedésével.
-  A Kubernetes-fürtökkel kapcsolatos problémák az [AK-motor áttekintése](azure-stack-kubernetes-aks-engine-overview.md)után.
-  Más Azure Stack szolgáltatásokhoz való kapcsolódással kapcsolatos problémák 
-  Kubernetes API-kapcsolattal kapcsolatos problémák
-  A Azure Stack Kubernetes-szolgáltató funkcióinak és a Azure Resource Managerkel való kapcsolatának problémái
-  A Azure Stack natív összetevők, például a terheléselosztó, a hálózati biztonsági csoportok, a virtuális hálózatok, az alhálózatok, a hálózati adapterek, az útválasztási táblázat, a rendelkezésre állási csoportok, a nyilvános IP-címek, a Storage-fiók és a virtuálisgép-gépek által generált, AK-val kapcsolatos problémák 
-  Hálózati teljesítménnyel és késéssel kapcsolatos problémák
-  A kapcsolat nélküli üzemelő példányok által használt AK-beli alapképpel kapcsolatos problémák. 

## <a name="aks-engine-areas-not-supported"></a>Az AK-motor területei nem támogatottak

A Microsoft nem biztosít technikai támogatást a következőkhöz:

-  Az AK-motor használata az Azure-ban.
-  Azure Stack Kubernetes Marketplace-eleme.
-  A következő, az e-motort kezelő fürt definíciójának beállításai és a bővítmények használata.
    -  Nem támogatott bővítmények:  
            -HRE Pod-identitás  
            – ACI-összekötő  
            -Blobfuse rugalmasidő-kötet  
            -Fürt autoskálázása  
            – Tároló figyelése  
            -Kulcstartó rugalmasidő-kötete  
            – NVIDIA-eszköz beépülő modul  
            – Átütemezett  
            – SMB-Flex kötet  
        
    -  Nem támogatott fürt-definíciós beállítások:  
            – A KubernetesConfig alatt:  
                    - cloudControllerManagerConfig  
                    - enableDataEncryptionAtRest  
                    - enableEncryptionWithExternalKms  
                    - enablePodSecurityPolicy  
                    - etcdEncryptionKey  
                    - useInstanceMetadata  
                    - useManagedIdentity  
                    - azureCNIURLLinux  
                    - azureCNIURLWindows  
            – A masterProfile alatt:  
                    - availabilityZones  
            – A agentPoolProfiles alatt:  
                    - availabilityZones  
                    – singlePlacementGroup  
                    - scaleSetPriority  
                    - scaleSetEvictionPolicy  
                    - acceleratedNetworkingEnabled  
                    - acceleratedNetworkingEnabledWindows

-  A Kubernetes-konfiguráció módosításai a Kubernetes konfigurációs tároló etcd kívül maradtak. Például a fürt csomópontjain futó statikus hüvelyek.
-  Kérdések a Kubernetes használatáról. A Microsoft ügyfélszolgálata például nem ad tanácsot az egyéni bejövő vezérlők létrehozásához, az alkalmazások számítási feladatainak használatához, vagy harmadik féltől származó vagy nyílt forráskódú szoftvercsomagok vagy eszközök alkalmazásához.
-  Harmadik féltől származó nyílt forráskódú projektek, amelyek nem az AK-motor által üzembe helyezett Kubernetes-fürt részeként vannak megadva. Ilyen projektek lehetnek például a Kubeadm, a Kubespray, a Native, a Istio, a Helm, a megbízottat vagy mások.
-  Az KABAi motor használata használati eseteken kívül, a [támogatott forgatókönyvekben az AK-motorral](azure-stack-kubernetes-aks-engine-overview.md#supported-scenarios-with-the-aks-engine)megadott esetekben.
-  Harmadik féltől származó szoftver. Ez a szoftver biztonsági ellenőrzési eszközöket és hálózati eszközöket, illetve szoftvereket tartalmazhat.
-  A többfelhős vagy a többgyártós kiépítéssel kapcsolatos problémák. A Microsoft például nem támogatja az összevont többnyilvános felhő-gyártói megoldás futtatásával kapcsolatos problémákat.
-  A hálózati testreszabások, amelyek nem szerepelnek az [AK-motor által támogatott területek](#aks-engine-supported-areas) szakaszban.

##  <a name="security-issues-and-patching"></a>Biztonsági problémák és javítás

Ha a rendszer biztonsági hibát talál a Azure Stackhoz tartozó AK-motor vagy Kubernetes-szolgáltató egy vagy több összetevőjében, a Microsoft elérhetővé tesz egy javítást az ügyfelek számára az érintett fürtök javításához a probléma enyhítése érdekében. Azt is megteheti, hogy a csapat a felhasználók számára is frissíti az útmutatót. Figyelje meg, hogy a javításokhoz a fürt leállására lehet szükség. Ha újraindításra van szükség, a Microsoft értesíti az ügyfeleket erről a követelményről. Ha a felhasználók a Microsoft útmutatása szerint nem alkalmazzák a javításokat, a fürt továbbra is sebezhető marad a biztonsági problémákkal kapcsolatban.

## <a name="kubernetes-marketplace-item"></a>Kubernetes Marketplace-tétel

A felhasználók letölthetik a Kubernetes Marketplace-elemeket, amelyek lehetővé teszik a felhasználók számára, hogy a Azure Stack felhasználói portálon keresztül közvetve központilag telepítsenek Kubernetes-fürtöket, így egyszerűbb, mint az AK-motor közvetlen használata. Ez egy hasznos eszköz, amellyel gyorsan beállíthatja a fürtöket a bemutatók, a tesztelés és a fejlesztés számára. Nem éles környezethez készült, mert nem szerepel a Microsoft által támogatott elemek készletében.

## <a name="preview-features"></a>Előzetes verziójú funkciók

A kiterjesztett tesztelést és felhasználói visszajelzéseket igénylő funkciók és funkciók esetében a Microsoft új előzetes verziójú funkciókat vagy szolgáltatásokat bocsát ki a szolgáltatás jelölője mögött. Ezeket a funkciókat előzetes vagy bétaverziós funkciókként érdemes figyelembe venni. Az előzetes verziójú funkciók vagy a szolgáltatás-jelölő funkciók nem éles környezetben használhatók. A funkcionalitással kapcsolatos változások és viselkedés, hibajavítások és egyéb változások instabil fürtöket és állásidőt okozhatnak. A Microsoft nem támogatja ezeket a szolgáltatásokat.

## <a name="next-steps"></a>További lépések

- További információ a [Azure stack AK-beli motorról](azure-stack-kubernetes-aks-engine-overview.md)