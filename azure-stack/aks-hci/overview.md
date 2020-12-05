---
title: Mi az az Azure Kubernetes Service a Azure Stack HCI-ben?
description: Az Azure Kubernetes Service on Azure Stack HCI az Azure Kubernetes szolgáltatás (ak) helyszíni megvalósítása, amely automatizálja a tároló alkalmazások méretezését.
ms.topic: overview
author: v-susbo
ms.author: v-susbo
ms.date: 12/02/2020
ms.openlocfilehash: a7d97d2454e9d77c1760180b46be9ce219d6af68
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612658"
---
# <a name="what-is-azure-kubernetes-service-on-azure-stack-hci"></a>Mi az az Azure Kubernetes Service a Azure Stack HCI-ben?
> A következőkre vonatkozik: Azure Stack HCI, AK Runtime a Windows Server 2019 Datacenter rendszeren

Az Azure Kubernetes Service on Azure Stack HCI az Azure Kubernetes szolgáltatás (ak) helyszíni megvalósítása, amely automatizálja a tároló alkalmazások méretezését. Az Azure Kubernetes Service mostantól előzetes verzióban érhető el a Azure Stack HCI és a Windows Server 2019 Datacenter rendszerhez, így a Linux-és Windows-tárolók az adatközpontban való üzemeltetésének megkezdése gyorsabb.

Az Azure Kubernetes Service helyszíni használatba [vételének](https://aka.ms/AKS-HCI-Evaluate) megkezdéséhez regisztráljon az előzetes verzióra (az előzetes verzióban nincs hozzáadott díj), majd [állítsa be az azure KUBERNETES szolgáltatást Azure stack HCI-re](setup.md). Az Azure Kubernetes Service használatával a felhőalapú tárolók összehangolása érdekében tekintse meg [Az Azure Kubernetes szolgáltatás az Azure-ban](/azure/aks/intro-kubernetes)című témakört.

Az alábbi részekben néhány olyan okot ismertetünk, amelyek az Azure Kubernetes Service Azure Stack HCI-re való használatát ismertetik, majd megválaszolják a szolgáltatással kapcsolatos gyakori kérdéseket és az első lépéseket. A tárolók hátterével kapcsolatban tekintse meg a [Windows és a tárolók](/virtualization/windowscontainers/about/) , valamint a Kubernetes hátterét ismertető témakört: [Kubernetes core Concepts](kubernetes-concepts.md) vagy [Kubernetes.IO](https://kubernetes.io).

## <a name="use-aks-to-automate-management-of-containerized-applications"></a>Az AK használata a tároló alkalmazások felügyeletének automatizálásához

Bár a Docker és a Windows használatával manuálisan is kezelhet néhány tárolót, az alkalmazások gyakran öt, tíz vagy akár több száz tárolót használnak, amelyekben a Kubernetes-Orchestrator bekerül.

A Kubernetes egy nyílt forráskódú Orchestrator, amely a tárolók felügyeletének automatizálására alkalmas a skálán. Az Azure Kubernetes szolgáltatás megkönnyíti a helyszíni Kubernetes üzembe helyezését azáltal, hogy varázslókat biztosít a Kubernetes és az alapvető bővítmények beállításához Azure Stack HCI-ben, valamint Kubernetes-fürtök létrehozását a számítási feladatok üzemeltetéséhez.

Íme néhány, az Azure Kubernetes szolgáltatás által biztosított funkció a Azure Stack HCI előzetes verziójában:

- Tárolón belüli alkalmazások üzembe helyezése a Azure Stack HCI-fürtön futó Kubernetes-fürtökön
- Linux-és Windows-alapú tárolós alkalmazások üzembe helyezése és kezelése
- Az AK üzembe helyezése Azure Stack HCI-ben a Windows felügyeleti központ vagy a PowerShell használatával
- Vertikális fel-vagy leskálázás a csomópontok Kubernetes-fürthöz való hozzáadásával vagy eltávolításával
- A Kubernetes-fürt tárterületének és hálózatkezelésének kezelése
- Automatikus frissítések biztosítása a Kubernetes üzembe helyezéséhez
- Frissítés a legújabb elérhető Kubernetes-verzióra
- A népszerű Azure-szolgáltatások használata az Azure arc for Kubernetes használatával

## <a name="simplify-setting-up-kubernetes"></a>Egyszerűsítse a Kubernetes beállítását

Az Azure Kubernetes szolgáltatás leegyszerűsíti a Kubernetes beállítását Azure Stack HCI és Windows Server 2019 Datacenter rendszeren, és a következő funkciókat tartalmazza:

- Egy Windows felügyeleti központ varázsló a Kubernetes és függőségeinek beállításához (például kubeadm, kubelet, kubectl és Pod Network-bővítmény)
- Windows felügyeleti központ varázsló Kubernetes-fürtök létrehozásához a tároló alkalmazások futtatásához
- PowerShell-parancsmagok a Kubernetes beállításához és a Kubernetes-fürtök létrehozásához abban az esetben, ha inkább parancsfájlt szeretne készíteni a gazdagép beállításáról és a Kubernetes-fürt létrehozásáról

## <a name="view-and-manage-kubernetes-using-on-premises-tools-or-azure-arc"></a>Kubernetes megtekintése és kezelése helyszíni eszközök vagy Azure arc használatával

Miután beállította a helyszíni Azure Kubernetes szolgáltatást, és létrehozta a Kubernetes-fürtöt, a Kubernetes-infrastruktúra felügyeletét és monitorozását néhány módon biztosítjuk:

- Helyszíni **eszközök, például a Kubectl és a Kubernetes irányítópult használatával** – egy nyílt forráskódú, webalapú felületen telepítheti az alkalmazásokat egy Kubernetes-fürtre, kezelheti a fürt erőforrásait, elháríthatja és megtekintheti a futó alkalmazásokat.
- **Az Azure arc használatával Azure Portal** az Azure arc használatával kezelheti az Kubernetes-fürtökön üzembe helyezett alkalmazásokat a felhőben és a helyszíni környezetekben. 
<br>Az Azure arc azt is lehetővé teszi, hogy a Kubernetes-fürtöket más Azure-szolgáltatásokkal is kezelhesse, beleértve a következőket:

  - Azure Monitor
  - Azure Policy
  - Szerepköralapú hozzáférés-vezérlés

## <a name="run-linux-and-windows-containers"></a>Linux-és Windows-tárolók futtatása

Az Azure Kubernetes szolgáltatás teljes mértékben támogatja a Linux-alapú és a Windows-alapú tárolókat is. Ha Azure Stack HCI-ben hoz létre Kubernetes-fürtöt, kiválaszthatja, hogy a rendszer létrehozza-e a Linux-tárolók, a Windows-tárolók vagy mindkettő futtatásához szükséges csomópont-készleteket (azonos Kubernetes-fürtöket). 

Az Azure Kubernetes szolgáltatás létrehozza a Linux és a Windows rendszerű csomópontokat, így nem kell közvetlenül a Linux vagy a Windows operációs rendszert felügyelni.

## <a name="secure-your-container-infrastructure"></a>A tároló-infrastruktúra biztonságossá tétele

Az Azure Kubernetes szolgáltatás számos funkciót tartalmaz a tároló-infrastruktúra biztonságossá tételéhez:

- A feldolgozó **csomópontok által használt hypervisor-alapú elkülönítés** – minden Kubernetes-fürt a saját dedikált és elkülönített virtuális gépeken fut, így a bérlők ugyanazt a fizikai infrastruktúrát tudják megosztani.
- A **Microsoft által karbantartott Linux-és Windows-rendszerképek a munkavégző csomópontok számára** – a feldolgozó csomópontjai a Microsoft által létrehozott Linux és Windows rendszerű virtuálisgép-rendszerképeket futtatnak az ajánlott biztonsági eljárásoknak megfelelően A Microsoft emellett a legújabb biztonsági frissítésekkel havonta frissíti ezeket a lemezképeket.

A biztonság az Azure Kubernetes Service előzetes kiadásának jelenleg Azure Stack HCI-ben való bevezetésének folyamatos része, így marad.

## <a name="where-can-i-run-azure-kubernetes-service"></a>Hol futtatható az Azure Kubernetes Service?

Az Azure Kubernetes szolgáltatás a következő platformokon érhető el:

- Az Azure-felhőben az Azure [Kubernetes Service](/azure/aks/intro-kubernetes) használatával az Azure-ban
- Helyszíni Azure Kubernetes szolgáltatással Azure Stack HCI-n (mi ez a cikk lényege)
- Helyszíni Azure Kubernetes Service Runtime használatával a Windows Serveren (ez a cikk a Windows Server AKSr is vonatkozik)
- A helyszínen egy Azure Stack hub-környezetben, a [Azure stack hub AK-motorjának](../user/azure-stack-kubernetes-aks-engine-overview.md)használatával.

## <a name="how-does-kubernetes-work-on-azure-stack-hci"></a>Hogyan működik a Kubernetes Azure Stack HCI-ben?

Az Azure Kubernetes szolgáltatás egy kicsit másképp működik, amikor Azure Stack HCI-nél futtat, mint amikor az Azure-felhőben használja:

- A Kubernetes szolgáltatás az Azure-ban egy üzemeltetett szolgáltatás, ahol a Kubernetes-kezelési infrastruktúra (vezérlő síkja) nagy részét felügyeli a rendszer. Mind a vezérlési, mind a tároló alkalmazások Azure-beli virtuális gépeken futnak.
- Az Azure Kubernetes Service on Azure Stack HCI szolgáltatásban közvetlenül a Azure Stack HCI-fürtön állíthatja be a szolgáltatást, így a vezérlési sík irányításával is elvégezheti a beszédet. A vezérlő síkja, a tárolós alkalmazások és az Azure Kubernetes szolgáltatás maga a hiperkonvergens-fürt által üzemeltetett virtuális gépeken fut.

Ha az Azure Kubernetes szolgáltatás be van állítva a Azure Stack HCI-fürtön, az az üzemeltetett Azure Kubernetes szolgáltatáshoz hasonlóan működik: a szolgáltatás használatával olyan Kubernetes-fürtöket hozhat létre, amelyek a tároló alkalmazásait futtatják. Ezek a Kubernetes-fürtök olyan virtuális gépek csoportjai, amelyek munkavégző csomópontként működnek az alkalmazás-tárolók futtatásával. A Kubernetes-fürt egy vezérlő síkot is tartalmaz, amely az Kubernetes előkészítésére szolgáló rendszerszolgáltatások áll.

Íme néhány egyszerűsített diagram, amely bemutatja, hogy az Azure Kubernetes szolgáltatás architektúrái hogyan hasonlítják össze az Azure-ban és Azure Stack HCI-ben való futtatáskor.

:::image type="content" source="media\overview\aks-azure-architecture.png" alt-text="Az Azure-ban üzemeltetett Azure Kubernetes Service architektúrája, amely bemutatja, hogyan kezeli az Azure a platform szolgáltatásait és a legtöbb vezérlési síkot, míg a Kubernetes-fürtöket az ügyfél kezeli." lightbox="media\overview\aks-azure-architecture.png":::

:::image type="content" source="media\overview\aks-hci-architecture.png" alt-text="Az Azure Kubernetes Service architektúrája Azure Stack HCI-on, amely bemutatja, hogy az Azure Stack HCI-fürtön hogyan fut minden, beleértve az Azure Kubernetes Service platformot, a vezérlési síkot, valamint a tároló alkalmazásait futtató Kubernetes-fürtöket." lightbox="media\overview\aks-hci-architecture.png":::

## <a name="what-you-need-to-get-started"></a>Mi szükséges a kezdéshez?

Az alábbi fejezetek összefoglalják, hogy mire van szükség az Azure Kubernetes szolgáltatás futtatásához Azure Stack HCI-on. A részletekért tekintse [meg az Azure Kubernetes szolgáltatás telepítése az Azure stack HCI](system-requirements.md)-re című témakört.

### <a name="on-your-windows-admin-center-system"></a>Windows felügyeleti központ rendszeren

A Windows felügyeleti központ átjáróját futtató gépen a következő követelmények vonatkoznak:

- Windows 10 rendszerű gép (jelenleg nem támogatott a Windows felügyeleti központ futtatása a Azure Stack HCI vagy a Windows Server 2019 Datacenter rendszeren.)
- 60 GB szabad terület
- Regisztrálva az Azure-ban
- Ugyanabban a tartományban, mint a Azure Stack HCI vagy a Windows Server 2019 Datacenter-fürt

### <a name="on-the-azure-stack-hci-cluster-or-windows-server-2019-datacenter-failover-cluster-that-hosts-azure-kubernetes-service"></a>Az Azure Kubernetes szolgáltatást futtató Azure Stack HCI-fürtön vagy a Windows Server 2019 Datacenter feladatátvevő fürtön

Az Azure Stack HCI-fürt vagy a Windows Server 2019 Datacenter feladatátvevő fürt követelményei a következők:

- A fürtben legfeljebb négy kiszolgáló érhető el ehhez az előzetes kiadáshoz
- 1 TB rendelkezésre álló kapacitás a Storage-készletben az Azure Kubernetes Service-hez
- Legalább 30 GB szabad memória az Azure Kubernetes Service-beli virtuális gépek futtatásához
- A fürtben lévő összes kiszolgálónak az előzetes kiadáshoz tartozó EN-US régiót és nyelvi beállítást kell használnia

A HCI rendszerkövetelményeinek általános Azure Stack lásd: [Azure stack HCI rendszerkövetelményei](../hci/concepts/system-requirements.md).

### <a name="the-network-configuration-for-azure-stack-hci"></a>Azure Stack HCI hálózati konfigurációja

Az Azure Stack HCI vagy a Windows Server 2019 Datacenter-fürtön található virtuális gépekhez csatlakozó hálózat dedikált hatókört igényel az Azure Kubernetes szolgáltatás számára elérhető DHCP IPv4-címek számára, és az Azure Stack HCI vagy a Windows Server 2019 Datacenter-fürtön elérhető virtuális gépek számára.

## <a name="next-steps"></a>További lépések

Az Azure Kubernetes Service Azure Stack HCI-ben való megkezdéséhez tekintse meg a következő cikkeket:

- [Követelmények áttekintése](system-requirements.md)
- [Az Azure Kubernetes szolgáltatás beállítása Azure Stack HCI-ben](create-kubernetes-cluster.md)
