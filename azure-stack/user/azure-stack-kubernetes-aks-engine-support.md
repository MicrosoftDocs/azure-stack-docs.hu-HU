---
title: Támogatási szabályzatok az AK motorhoz Azure Stack hub-on
description: Ez a témakör a Azure Stack hub-ban található AK-motor támogatási szabályzatait tartalmazza.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/0102020
ms.openlocfilehash: 0322d72f3cd28a8e499b2a900d57e4e9d2e9bce4
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246724"
---
# <a name="support-policies-for-aks-engine-on-azure-stack-hub"></a>Támogatási szabályzatok az AK motorhoz Azure Stack hub-on

Ez a cikk részletesen ismerteti a technikai támogatási szabályzatokat és a Azure Stack hub-ben található AK-motorra vonatkozó korlátozásokat. A cikk a Kubernetes Marketplace-elemek, a harmadik féltől származó nyílt forráskódú összetevők, valamint a biztonság vagy a javítások kezelését is ismerteti. 

## <a name="self-managed-kubernetes-clusters-on-azure-stack-hub-with-aks-engine"></a>Önfelügyelt Kubernetes-fürtök Azure Stack hub-ban az AK-motorral

Az infrastruktúra-szolgáltatás (IaaS) Felhőbeli összetevői, például a számítási vagy hálózatkezelési összetevők hozzáférést biztosítanak a felhasználóknak az alacsony szintű vezérlőkhöz és a testreszabási lehetőségekhez. Az AK-motor lehetővé teszi, hogy a felhasználó dobás a Kubernetes-fürtöket a IaaS-összetevők transzparens módon történő felhasználásával, így a felhasználók hozzáférhetnek és befolyásolhatják az üzemelő példányok összes aspektusát.

Fürt létrehozásakor az ügyfél meghatározza a Kubernetes-főkiszolgálókat és a munkavégző csomópontokat, amelyeket az AK-motor hoz létre. Az ügyfelek számítási feladatait ezeken a csomópontokon hajtja végre. A saját ügyfelei, és megtekinthetik és módosíthatják a fő-és munkavégző csomópontokat. A gondatlanul módosított csomópontok adatvesztést okozhatnak az adatok és a munkaterhelések esetében, és a fürt nem működőképes állapotba helyezhetők. Emellett az KABAi motor műveletei, például a verziófrissítés vagy a skálázás felülírják az összes nem kötött módosítást. Ha például a fürtön statikus hüvely található, akkor ezek nem őrződnek meg az AK-motor frissítési művelete után.

Mivel az ügyfél-fürtcsomópontok saját kódokat hajtanak végre, és bizalmas adatokat tárolnak, Microsoft ügyfélszolgálata csak korlátozott módon férhetnek hozzájuk. Microsoft ügyfélszolgálata nem tud bejelentkezni a alkalmazásba, végrehajthatja a parancsokat a-ben, vagy megtekintheti a csomópontok naplóit kifejezett ügyfél engedélye vagy segítsége nélkül.

## <a name="version-support"></a>Verziók támogatása

Az AK-motor verziószámának támogatása ugyanazt a mintát követi, amelyet az Azure Stack hub-támogatási szabályzat további része is meghatároz, amely az AK-os motor Azure Stack hub-beli verziójának támogatása az n-2 képleten alapul. Ha például az AK-motor legújabb verziója v 0.55.0, a támogatott verziók készlete a következő: 0.48.0, 0.51.0, 0.55.0. Azt is fontos, hogy kövesse az Azure Stack hub frissítési verzióját és a megfelelő leképezést az AK-motor támogatott verziójára, ez az [AK-motor kibocsátási megjegyzései](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping)között marad.

## <a name="aks-engine-supported-areas"></a>AK-motor által támogatott területek

A Microsoft technikai támogatást nyújt a következőkhöz:

-  Az KABAi motor parancsaival kapcsolatos problémák: üzembe helyezés, előállítás, frissítés és méretezés. Az eszköznek konzisztensnek kell lennie az Azure-beli viselkedésével.
-  A Kubernetes-fürtökkel kapcsolatos problémák az [AK-motor áttekintése](azure-stack-kubernetes-aks-engine-overview.md)után.
-  Más Azure Stack hub-szolgáltatásokhoz való kapcsolódással kapcsolatos problémák. 
-  Problémák az Kubernetes API-kapcsolattal.
-  A Azure Stack hub Kubernetes-szolgáltató funkcióinak és a Azure Resource Manager kapcsolatának problémái.
-  Problémák merültek fel az Azure Stack hub olyan natív összetevőinek, mint például a Load Balancer, a hálózati biztonsági csoportok, a virtuális hálózatok, az alhálózatok, a hálózati adapterek, az útválasztási táblázat, a rendelkezésre állási csoportok, a nyilvános IP-címek, a Storage-fiók és a virtuálisgép-gépek esetében. 
-  Hálózati teljesítménnyel és késéssel kapcsolatos problémák.
-  A kapcsolat nélküli üzemelő példányok által használt AK-beli alapképpel kapcsolatos problémák. 

## <a name="aks-engine-areas-not-supported"></a>Az AK-motor területei nem támogatottak

A Microsoft nem biztosít technikai támogatást a következőkhöz:

-  Az AK-motor használata az Azure-ban.
-  Azure Stack hub Kubernetes Marketplace-eleme.
-  A következő, az e-motort kezelő fürt definíciójának beállításai és a bővítmények használata.
    -  Nem támogatott bővítmények:  
            – Azure AD Pod-identitás  
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
-  Az éles környezeteknek csak a magasan elérhető Kubernetes-fürtöket kell használniuk, azaz a legalább három főkiszolgálóval és három ügynök-csomóponttal üzembe helyezett fürtöket. Az éles környezetekben nem támogatottak a kevésbé támogatott elemek.

##  <a name="security-issues-and-patching"></a>Biztonsági problémák és javítás

Ha a rendszer egy vagy több, a Azure Stack hub-hoz tartozó Kubernetes-szolgáltató egy vagy több összetevőjében talál biztonsági hibát, a Microsoft elérhetővé teszi az ügyfelek számára az érintett fürtök javítását a probléma enyhítése érdekében. Azt is megteheti, hogy a csapat a felhasználók számára is frissíti az útmutatót. Figyelje meg, hogy a javításokhoz a fürt leállására lehet szükség. Ha újraindításra van szükség, a Microsoft értesíti az ügyfeleket erről a követelményről. Ha a felhasználók a Microsoft útmutatása szerint nem alkalmazzák a javításokat, a fürt továbbra is sebezhető marad a biztonsági problémákkal kapcsolatban.

## <a name="kubernetes-marketplace-item"></a>Kubernetes Marketplace-tétel

A felhasználók letöltheti a Kubernetes Marketplace-et, amely lehetővé teszi a felhasználók számára, hogy a Azure Stack hub felhasználói portálon keresztül közvetve központilag telepítsenek Kubernetes-fürtöket az AK-motor használatával. Ez egyszerűbbé teszi az AK-motor közvetlen használatát. A Kubernetes Marketplace-elem hasznos eszköz a fürtök gyors beállításához a bemutatók, a tesztelés és a fejlesztés érdekében. Nem éles környezethez készült, ezért nem szerepel a Microsoft által támogatott elemek készletében.

## <a name="preview-features"></a>Előzetes verziójú funkciók

A kiterjesztett tesztelést és felhasználói visszajelzéseket igénylő funkciók és funkciók esetében a Microsoft új előzetes verziójú funkciókat vagy szolgáltatásokat bocsát ki a szolgáltatás jelölője mögött. Ezeket a funkciókat előzetes vagy bétaverziós funkciókként érdemes figyelembe venni. Az előzetes verziójú funkciók vagy a szolgáltatás-jelölő funkciók nem éles környezetben használhatók. A funkcionalitással kapcsolatos változások és viselkedés, hibajavítások és egyéb változások instabil fürtöket és állásidőt okozhatnak. A Microsoft nem támogatja ezeket a szolgáltatásokat.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)