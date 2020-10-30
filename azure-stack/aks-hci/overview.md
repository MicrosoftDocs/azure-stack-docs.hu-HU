---
title: Mi az az Azure Kubernetes Service a Azure Stack HCI-ben?
description: Az Azure Kubernetes Service on Azure Stack HCI az Azure Kubernetes szolgáltatás (ak) helyszíni megvalósítása, amely automatizálja a tároló alkalmazások méretezését.
ms.topic: overview
author: jasongerend
ms.author: jgerend
ms.date: 09/22/2020
ms.openlocfilehash: ef2cc07ca9c228b9d427d11f8065e2d943e1626b
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064616"
---
# <a name="what-is-azure-kubernetes-service-on-azure-stack-hci"></a>Mi az az Azure Kubernetes Service a Azure Stack HCI-ben?

Az Azure Kubernetes Service on Azure Stack HCI az Azure Kubernetes szolgáltatás (ak) helyszíni megvalósítása, amely automatizálja a tároló alkalmazások méretezését. Az Azure Kubernetes szolgáltatás jelenleg előzetes verzióban érhető el a Azure Stack HCI-ben, így gyorsabban megkezdheti a Linux és Windows rendszerű tárolók üzemeltetését az adatközpontban.

Az Azure Kubernetes Service helyszíni üzembe helyezésének megkezdéséhez [regisztráljon az előzetes](https://aka.ms/AKS-HCI-Evaluate) verzióra (az előzetes verzióban nincs további díj), majd tekintse [meg az Azure Kubernetes szolgáltatás beállítása Azure stack HCI-re](setup.md)című témakört. Az Azure Kubernetes Service használatával a felhőalapú tárolók összehangolása érdekében tekintse meg [Az Azure Kubernetes szolgáltatás az Azure-ban](/azure/aks/intro-kubernetes)című témakört.

Az alábbi részekben néhány olyan okot ismertetünk, amelyek az Azure Kubernetes Service Azure Stack HCI-re való használatát ismertetik, majd megválaszolják a szolgáltatással kapcsolatos gyakori kérdéseket és az első lépéseket. A tárolók hátteréről lásd: [Windows és tárolók](/virtualization/windowscontainers/about/).

## <a name="automate-management-of-containerized-applications"></a>A tároló alkalmazások felügyeletének automatizálása

Bár a Docker és a Windows használatával manuálisan is kezelhet néhány tárolót, az alkalmazások gyakran öt, tíz vagy akár több száz tárolót használnak, amelyekben a Kubernetes-Orchestrator bekerül.

A Kubernetes egy nyílt forráskódú Orchestrator, amely a tárolók felügyeletének automatizálására alkalmas a skálán. Az Azure Kubernetes szolgáltatás megkönnyíti a helyszíni Kubernetes üzembe helyezését azáltal, hogy varázslókat biztosít a Kubernetes és az alapvető bővítmények beállításához Azure Stack HCI-ben, valamint Kubernetes-fürtök létrehozását a számítási feladatok üzemeltetéséhez.

Íme néhány, az Azure Kubernetes szolgáltatás által biztosított funkció a Azure Stack HCI előzetes verziójában:

- Tárolóban lévő alkalmazások üzembe helyezése nagy méretekben a Azure Stack HCI-fürtön futó virtuális gépek (úgynevezett Kubernetes-fürt) fürtje számára
- Feladatátvétel, ha a Kubernetes-fürt egyik csomópontja meghiúsul
- Linux-és Windows-alapú tárolós alkalmazások üzembe helyezése és kezelése
- Számítási feladatok ütemterve
- Az állapot monitorozása
- Vertikális fel-vagy leskálázás a csomópontok Kubernetes-fürthöz való hozzáadásával vagy eltávolításával
- Hálózatkezelés felügyelete
- Szolgáltatások felderítése
- Alkalmazás-frissítések koordinálása
- Hüvelyek társítása fürt csomópontjaihoz a fürtcsomópont-affinitással

További információ a Kubernetes: [Kubernetes.IO](https://kubernetes.io).

## <a name="simplify-setting-up-kubernetes"></a>Egyszerűsítse a Kubernetes beállítását

Az Azure Kubernetes szolgáltatás leegyszerűsíti a Kubernetes beállítását Azure Stack HCI-ben, és a következő funkciókat tartalmazza:

- Egy Windows felügyeleti központ varázsló a Kubernetes és függőségeinek beállításához (például kubeadm, kubelet, kubectl és Pod Network-bővítmény)
- Windows felügyeleti központ varázsló Kubernetes-fürtök létrehozásához a tároló alkalmazások futtatásához
- PowerShell-parancsmagok a Kubernetes beállításához és a Kubernetes-fürtök létrehozásához abban az esetben, ha inkább parancsfájlt szeretne készíteni a gazdagép beállításáról és a Kubernetes-fürt létrehozásáról

## <a name="view-and-manage-kubernetes-using-on-premises-tools-or-azure-arc"></a>Kubernetes megtekintése és kezelése helyszíni eszközök vagy Azure arc használatával

Miután beállította az Azure Kubernetes szolgáltatást a Azure Stack HCI-fürtön, és létrehozott egy Kubernetes-fürtöt, a Kubernetes-infrastruktúra felügyeletét és monitorozását néhány módon biztosítjuk:

- Helyszíni **eszközök, például a Kubectl és a Kubernetes irányítópult használatával** – egy nyílt forráskódú webes felületen keresztül telepíthet alkalmazásokat egy Kubernetes-fürtre, kezelheti a fürt erőforrásait, elháríthatja és megtekintheti a futó alkalmazásokat.
- **A Azure Portal az Azure arc használatával** – Azure-szolgáltatással felügyelheti az Azure Kubernetes szolgáltatást és a felhőben és a helyszíni környezetekben üzembe helyezett Kubernetes-fürtöket. Az Azure arc használatával Kubernetes-fürtöket, valamint csomópontokat adhat hozzá egy Kubernetes-fürthöz, módosíthatja a hálózati beállításokat, és telepítheti a bővítményeket.
<br>Az Azure arc azt is lehetővé teszi, hogy a Kubernetes-fürtöket más Azure-szolgáltatásokkal is kezelhesse, beleértve a következőket:

  - Azure Monitor
  - Azure Policy
  - Szerepköralapú hozzáférés-vezérlés

## <a name="run-linux-and-windows-containers"></a>Linux-és Windows-tárolók futtatása

Az Azure Kubernetes szolgáltatás teljes mértékben támogatja a Linux-alapú és a Windows-alapú tárolókat is. Ha Azure Stack HCI-ben hoz létre Kubernetes-fürtöt, kiválaszthatja, hogy a rendszer létrehozza-e a csomópont-készleteket (azonos virtuális gépek csoportjai) Linux-tárolók, Windows-tárolók vagy mindkettő futtatásához. 

Az Azure Kubernetes szolgáltatás létrehozza a Linux és a Windows rendszerű virtuális gépeket, így nem kell közvetlenül felügyelni a Linux vagy a Windows operációs rendszert.

## <a name="secure-your-container-infrastructure"></a>A tároló-infrastruktúra biztonságossá tétele

Az Azure Kubernetes szolgáltatás számos funkciót tartalmaz a tároló-infrastruktúra biztonságossá tételéhez:

- A feldolgozó **csomópontok által használt hypervisor-alapú elkülönítés** – minden Kubernetes-fürt a saját dedikált és elkülönített virtuális gépeken fut, így a bérlők ugyanazt a fizikai infrastruktúrát tudják megosztani.
- A **Microsoft által karbantartott Linux-és Windows-rendszerképek a munkavégző csomópontok számára** – a feldolgozó csomópontjai a Microsoft által létrehozott Linux és Windows rendszerű virtuálisgép-rendszerképeket futtatnak az ajánlott biztonsági eljárásoknak megfelelően A Microsoft emellett a legújabb biztonsági frissítésekkel havonta frissíti ezeket a lemezképeket.

A biztonság az Azure Kubernetes Service előzetes kiadásának jelenleg Azure Stack HCI-ben való bevezetésének folyamatos része, így marad.

## <a name="where-can-i-run-azure-kubernetes-service"></a>Hol futtatható az Azure Kubernetes Service?

Az Azure Kubernetes szolgáltatás a következő platformokon érhető el:

- Az Azure-felhőben az Azure [Kubernetes Service](/azure/aks/intro-kubernetes) használatával az Azure-ban
- Helyszíni Azure Kubernetes szolgáltatással Azure Stack HCI-n (mi ez a cikk lényege)
- A helyszínen egy Azure Stack hub-környezetben, a [Azure stack hub AK-motorjának](../user/azure-stack-kubernetes-aks-engine-overview.md)használatával.

## <a name="how-does-kubernetes-work-on-azure-stack-hci"></a>Hogyan működik a Kubernetes Azure Stack HCI-ben?

Az Azure Kubernetes szolgáltatás egy kicsit másképp működik, amikor Azure Stack HCI-nél futtat, mint amikor az Azure-felhőben használja:

- A Kubernetes szolgáltatás az Azure-ban egy üzemeltetett szolgáltatás, ahol a Kubernetes-kezelési infrastruktúra (vezérlő síkja) nagy részét felügyeli a rendszer. Mind a vezérlési, mind a tároló alkalmazások Azure-beli virtuális gépeken futnak.
- Az Azure Kubernetes Service on Azure Stack HCI szolgáltatásban közvetlenül a Azure Stack HCI-fürtön állíthatja be a szolgáltatást, így a vezérlési sík irányításával is elvégezheti a beszédet. A vezérlő síkja, a tárolós alkalmazások és az Azure Kubernetes szolgáltatás maga a hiperkonvergens-fürt által üzemeltetett virtuális gépeken fut.

Ha az Azure Kubernetes szolgáltatás be van állítva a Azure Stack HCI-fürtön, az az üzemeltetett Azure Kubernetes szolgáltatáshoz hasonlóan működik: a szolgáltatás használatával olyan Kubernetes-fürtöket hozhat létre, amelyek a tároló alkalmazásait futtatják. Ezek a Kubernetes-fürtök olyan virtuális gépek csoportjai, amelyek munkavégző csomópontként működnek az alkalmazás-tárolók futtatásával. A Kubernetes-fürt egy vezérlő síkot is tartalmaz, amely az Kubernetes előkészítésére szolgáló rendszerszolgáltatások áll.

Íme néhány egyszerűsített diagram, amely bemutatja, hogy az Azure Kubernetes szolgáltatás architektúrái hogyan hasonlítják össze az Azure-ban és Azure Stack HCI-ben való futtatáskor.

:::image type="content" source="media\overview\aks-azure-architecture.png" alt-text="Az Azure-ban üzemeltetett Azure Kubernetes Service architektúrája, amely bemutatja, hogyan kezeli az Azure a platform szolgáltatásait és a legtöbb vezérlési síkot, míg a Kubernetes-fürtöket az ügyfél kezeli." lightbox="media\overview\aks-azure-architecture.png":::

:::image type="content" source="media\overview\aks-hci-architecture.png" alt-text="Az Azure-ban üzemeltetett Azure Kubernetes Service architektúrája, amely bemutatja, hogyan kezeli az Azure a platform szolgáltatásait és a legtöbb vezérlési síkot, míg a Kubernetes-fürtöket az ügyfél kezeli." lightbox="media\overview\aks-hci-architecture.png":::

## <a name="what-you-need-to-get-started"></a>Mi szükséges a kezdéshez?

Az alábbi fejezetek összefoglalják, hogy mire van szükség az Azure Kubernetes szolgáltatás futtatásához Azure Stack HCI-on. A részletekért tekintse [meg az Azure Kubernetes szolgáltatás telepítése az Azure stack HCI](system-requirements.md)-re című témakört.

### <a name="on-your-windows-admin-center-system"></a>Windows felügyeleti központ rendszeren

A Windows felügyeleti központ felügyeleti rendszerének követelményei a következők:

- Windows 10 (jelenleg nem támogatjuk a Windows felügyeleti központ kiszolgálóit)
- 60 GB szabad terület
- Regisztrálva az Azure-ban
- Ugyanabban a tartományban, mint a Azure Stack HCI-fürt

### <a name="on-the-azure-stack-hci-cluster-that-hosts-azure-kubernetes-service"></a>Az Azure Kubernetes szolgáltatást futtató Azure Stack HCI-fürtön

A Azure Stack HCI vagy újabb verziójú 20H2 futtató fürt a következő követelményekkel rendelkezik:

- A fürtben legfeljebb négy kiszolgáló érhető el ehhez az előzetes kiadáshoz
- 1 TB rendelkezésre álló kapacitás a Storage-készletben az Azure Kubernetes Service-hez
- Legalább 30 GB szabad memória az Azure Kubernetes Service-beli virtuális gépek futtatásához
- A fürtben lévő összes kiszolgálónak az előzetes kiadáshoz tartozó EN-US régiót és nyelvi beállítást kell használnia

A HCI rendszerkövetelményeinek általános Azure Stack lásd: [Azure stack HCI rendszerkövetelményei](../hci/concepts/system-requirements.md).

### <a name="the-network-configuration-for-azure-stack-hci"></a>Azure Stack HCI hálózati konfigurációja

Az Azure Stack HCI-fürtön található virtuális gépekhez csatlakozó hálózat dedikált hatókört igényel az Azure Kubernetes szolgáltatás számára elérhető DHCP IPv4-címek számára, és az Azure Stack HCI-fürtön elérhető virtuális gépek számára érhető el.

A VLAN-címkék nem használhatók a hálózaton az Azure Kubernetes Service-hez Azure Stack HCI-ben. Használja a hálózati kapcsolók hozzáférési (címkézetlen) portját Azure Stack HCI és az Azure Kubernetes Service virtuális gépek által használt hálózathoz.

## <a name="next-steps"></a>Következő lépések

Az Azure Kubernetes Service Azure Stack HCI-ben való megkezdéséhez tekintse meg a következő cikkeket:

- [Követelmények áttekintése](system-requirements.md)
- [Az Azure Kubernetes szolgáltatás beállítása Azure Stack HCI-ben](create-kubernetes-cluster.md)
