---
title: Gyors útmutató Kubernetes-fürt létrehozásához a Windows felügyeleti központban
description: Megtudhatja, hogyan hozhat létre Kubernetes-fürtöt a Windows felügyeleti központban
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 73cb3a7d0ba7677948a7e96cf30872c3c6ac7582
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557178"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>Gyors útmutató: Kubernetes-fürt létrehozása Azure Stack HCI-ben a Windows felügyeleti központtal

> A következőkre vonatkozik: Azure Stack HCI, Windows Server 2019 Datacenter

Az Azure Kubernetes Service Host beállítása után létrehozhat egy Kubernetes-fürtöt a Windows felügyeleti központból. A PowerShell használata helyett tekintse meg [a Kubernetes-fürt létrehozása a PowerShell](create-kubernetes-cluster-powershell.md)használatával című témakört.

Mielőtt továbblép a Kubernetes-fürt létrehozása varázslóba, győződjön meg arról, hogy [beállította az Azure Kubernetes szolgáltatást](setup.md) , és ellenőrizze a [rendszerkövetelményeket](system-requirements.md) , ha még nem tette meg. A Kubernetes-fürt létrehozása varázsló a [minden kapcsolat](#creating-a-kubernetes-cluster-from-the-all-connections-page) lapon vagy az [Azure Kubernetes Service Host irányítópultján](#creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard)érhető el.

## <a name="creating-a-kubernetes-cluster-from-the-all-connections-page"></a>Kubernetes-fürt létrehozása a minden kapcsolat lapról 

Kubernetes-fürtöt kétféleképpen lehet létrehozni a Windows felügyeleti központban. Ezen beállítások közül az egyiket a **minden kapcsolat** lapon teheti meg. Hajtsa végre az alábbi lépéseket, majd folytassa a [Kubernetes-fürt létrehozása varázslóval](#the-kubernetes-cluster-create-wizard) című szakaszt: 

1. A Kubernetes-fürt Windows felügyeleti központban való létrehozásának megkezdéséhez kattintson a **Hozzáadás** gombra az átjáró képernyőjén. 
2. Az **erőforrások hozzáadása vagy létrehozása** panelen a Kubernetes- **fürt (előzetes verzió)** területen válassza az **új létrehozása** lehetőséget a Kubernetes-fürt varázsló elindításához. Míg a Kubernetes-fürtök "Hozzáadás" gombja megtalálható a nyilvános előzetes verzióban, nem működik. A Kubernetes-fürt létrehozása során bármikor kiléphet a varázslóból, de a folyamat nem lesz mentve. 


    ![Bemutatja az erőforrások hozzáadása vagy létrehozása panelt a Windows felügyeleti központban, amely most már tartalmazza az új csempét a Kubernetes-fürtökhöz.](.\media\create-kubernetes-cluster\add-connection.png)
  
## <a name="creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard"></a>Kubernetes-fürt létrehozása az Azure Kubernetes Service Host irányítópultján  

Kubernetes-fürtöt az Azure Kubernetes Service Host irányítópultján is létrehozhat. Ez az irányítópult az Azure Kubernetes szolgáltatás eszközében érhető el, ha az Azure Kubernetes Service hostt használó rendszerhez csatlakozik. Hajtsa végre az alábbi lépéseket, majd folytassa a [Kubernetes-fürt létrehozása varázslóval](#the-kubernetes-cluster-create-wizard) című szakaszt: 

1. Kapcsolódjon arra a rendszerre, ahol létre szeretné hozni a Kubernetes-fürtöt, és navigáljon az **Azure Kubernetes Service** Tool eszközhöz. A rendszernek már be kell állítania egy Azure Kubernetes Service-gazdagépet.
2. Válassza a **fürt hozzáadása** gombot a **Kubernetes-fürt** fejléce alatt.

![Bemutatja az Azure Kubernetes szolgáltatás eszközének irányítópultját, amely az Azure Kubernetes Service Host beállítása után jelenik meg.](.\media\setup\dashboard.png)
  
## <a name="the-kubernetes-cluster-create-wizard"></a>A Kubernetes-fürt létrehozása varázsló
Elérte a Kubernetes-fürt létrehozása varázslót a **minden kapcsolat** lapon vagy az Azure Kubernetes szolgáltatás eszközén. Kezdjünk hozzá:  

1. Tekintse át a Kubernetes-fürtöt és a Windows felügyeleti központot futtató rendszer előfeltételeit. Ha elkészült, kattintson a **Tovább gombra**. 
2. Az **alapvető beállítások** lapon konfigurálja a fürtre vonatkozó információkat, például az Azure arc-integrációt, az Azure Kubernetes Service Host információit és az elsődleges csomópontok készletének méretét. Az Azure Kubernetes Service Host mezőnek a [telepítési](setup.md) lapon való átjáráskor használt Azure stack HCI vagy Windows Server 2019 Datacenter-fürt teljes tartománynevét kell használnia. A rendszerhez az Azure Kubernetes szolgáltatás eszközén kell végrehajtania a gazdagép telepítését. Nyilvános előzetes verzióban a csomópontok száma nem szerkeszthető, és az alapértelmezett érték 2, de a csomópontok mérete nagyobb számítási feladatokhoz is konfigurálható. Ha elkészült, kattintson a **Tovább gombra**.

 [A ![ Kubernetes-fürt varázsló alapjai oldalát mutatja be. ](.\media\create-kubernetes-cluster\basics.png)](.\media\create-kubernetes-cluster\basics.png#lightbox)
 
3. További csomópont-készleteket is beállíthat a számítási feladatok futtatásához a **Node Pools** lapon. A nyilvános előzetes verzióban egy Windows-csomópontot és egy linuxos csomópont-készletet (a rendszercsomópont-készleten kívül) is hozzáadhat. Ha később szeretné engedélyezni az Azure arc-integrációt a varázslóban, szüksége lesz legalább egy olyan Linux-csomópontra, amely nem az elsődleges csomópont-készlet. Ha elkészült, kattintson a **Tovább gombra**.
4. Adja meg a hálózati konfigurációt a **hálózatkezelés** lapon. Ha a "speciális" lehetőséget választja, testreszabhatja a fürt csomópontjaihoz virtuális hálózat létrehozásakor használt címtartományt. Ha az "alapértékek" lehetőséget választja, a fürt csomópontjaihoz tartozó virtuális hálózatok alapértelmezett címtartományból lesznek létrehozva. A hálózati beállítások (terheléselosztó, hálózati házirend és HTTP-alkalmazás útválasztása) nem módosíthatók nyilvános előzetes verzióban. Ha elkészült, kattintson a **Tovább gombra**.

    [A ![ Kubernetes-fürt varázsló hálózatkezelés lapját mutatja be. ](.\media\create-kubernetes-cluster\networking.png)](\media\create-kubernetes-cluster\networking.png#lightbox)

5. Az **integráció** lapon a fürtöt további szolgáltatásokkal (például állandó tárterülettel) kell összekapcsolni. Ezen a lapon be kell állítania az állandó tárterületet. A nyilvános előzetes verzióban az állandó tárolási hely alapértelmezés szerint a gazdagép telepítése során kiválasztott tárolási helyre kerül. Ez a mező jelenleg nem szerkeszthető. Ha elkészült, kattintson a **Tovább gombra**.
6. Tekintse át a beállításokat a **felülvizsgálat + létrehozás** oldalon. Ha elégedett, válassza a **Létrehozás** lehetőséget az üzembe helyezés megkezdéséhez. A telepítési folyamat a lap tetején jelenik meg. 
7. A telepítés befejezése után a következő lépések oldalon részletesen **ismertetjük** a fürt kezelését. Ezen a lapon az SSH-kulcs is szerepel. Ha úgy döntött, hogy letiltja az Azure arc-integrációt az előző lépésben, előfordulhat, hogy az ezen a lapon található információk és utasítások nem lesznek elérhetők vagy működőképesek.

> [!IMPORTANT] 
> Javasoljuk, hogy az SSH-kulcsot biztonságos, elérhető helyen mentse.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban üzembe helyezett egy Kubernetes-fürtöt. Ha többet szeretne megtudni az Azure Kubernetes Service-ről Azure Stack HCI-ről, és végigvezeti a Linux-alkalmazások üzembe helyezésének és kezelésének lépésein az AK-on Azure Stack HCI-n, folytassa a következő oktatóanyaggal:

- [Oktatóanyag: linuxos alkalmazások üzembe helyezése az Azure Kubernetes szolgáltatásban Azure Stack HCI-ben](deploy-linux-application.md)
