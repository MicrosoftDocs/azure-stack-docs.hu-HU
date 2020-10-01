---
title: Gyors útmutató Kubernetes-fürt létrehozásához a Windows felügyeleti központban
description: Megtudhatja, hogyan hozhat létre Kubernetes-fürtöt a Windows felügyeleti központban
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 7ef2f0e0532ee342e8821b362b16433f755bc072
ms.sourcegitcommit: a1e2003fb9c6dacdc76f97614ff5a26a5b197b49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91623268"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>Gyors útmutató: Kubernetes-fürt létrehozása Azure Stack HCI-ben a Windows felügyeleti központtal

> A következőkre vonatkozik: Azure Stack HCI

Az Azure Kubernetes Service Host beállítása után létrehozhat egy Kubernetes-fürtöt a Windows felügyeleti központból. A PowerShell használata helyett tekintse meg [a Kubernetes-fürt létrehozása a PowerShell](create-kubernetes-cluster-powershell.md)használatával című témakört.

Kezdjünk hozzá:

1. [Állítsa be az Azure Kubernetes szolgáltatást](setup.md) , és ellenőrizze a [rendszerkövetelményeket](system-requirements.md) , ha még nem tette meg.
1. A Kubernetes-fürt Windows felügyeleti központban való létrehozásának megkezdéséhez kattintson a **Hozzáadás** gombra az átjáró képernyőjén.
2. Az **erőforrások hozzáadása vagy létrehozása** panelen a Kubernetes- **fürt (előzetes verzió)** területen válassza az **új létrehozása** lehetőséget a Kubernetes-fürt varázsló elindításához. Míg a Kubernetes-fürtök "Hozzáadás" gombja megtalálható a nyilvános előzetes verzióban, nem működik. A Kubernetes-fürt létrehozási folyamata során bármikor kiléphet a varázslóból, azonban a folyamat nem lesz mentve. 

    ![A Windows felügyeleti központban a "erőforrások hozzáadása vagy létrehozása" panelt mutatja be, amely most már tartalmazza a Kubernetes-fürtökhöz tartozó új csempét.](.\media\create-kubernetes-cluster\add-connection.png)

3. Tekintse át a Kubernetes-fürtöt és a Windows felügyeleti központot futtató rendszer előfeltételeit. Ha elkészült, kattintson a **Tovább gombra**. 
4. Az **alapvető beállítások** lapon konfigurálja a fürtre vonatkozó információkat, például az Azure arc-integrációt, az Azure Kubernetes Service Host információit és az elsődleges csomópontok készletének méretét.  Az Azure Kubernetes Service Host információi és az elsődleges csomópontok készletének mérete. Az Azure Kubernetes Service Host mezőhöz meg kell adni annak a Azure Stack HCI-fürtnek a teljes tartománynevét, amelyre a Kubernetes-fürtöt telepíteni kívánja. A rendszerhez az Azure Kubernetes szolgáltatás eszközén kell végrehajtania a gazdagép telepítését. Nyilvános előzetes verzióban a csomópontok száma nem módosítható, és az alapértelmezett érték 2, de a csomópontok mérete nagyobb számítási feladatokhoz is konfigurálható. Ha elkészült, kattintson a **Tovább gombra**.

    ![A Kubernetes-fürt varázsló alapjai oldalát mutatja be.](.\media\create-kubernetes-cluster\basics.png)

5. További csomópont-készleteket is beállíthat a számítási feladatok futtatásához a **Node Pools** lapon. A nyilvános előzetes verzióban egy Windows-csomópontot és egy linuxos csomópont-készletet (a rendszercsomópont-készleten kívül) is hozzáadhat. Ha később szeretné engedélyezni az Azure arc-integrációt a varázslóban, szüksége lesz legalább egy olyan Linux-csomópontra, amely nem az elsődleges csomópont-készlet. Ha elkészült, kattintson a **Tovább gombra**.
6. Adja meg a hálózati konfigurációt a **hálózatkezelés** lapon. Ha a "speciális" lehetőséget választja, testreszabhatja a fürt csomópontjaihoz virtuális hálózat létrehozásakor használt címtartományt. Ha az "alapszintű" lehetőséget választja, a fürt csomópontjaihoz tartozó virtuális hálózatok alapértelmezett címtartományt fognak létrehozni. A hálózati beállítások (terheléselosztó, hálózati házirend és HTTP-alkalmazás útválasztása) nem módosíthatók nyilvános előzetes verzióban. Ha elkészült, kattintson a **Tovább gombra**.

    ![A Kubernetes-fürt varázsló hálózatkezelés lapját mutatja be.](.\media\create-kubernetes-cluster\networking.png)

7. Az **integráció** lapon további szolgáltatásokkal, például a Kubernetes-irányítópulttal, az állandó tárterülettel és a Azure Monitorával is összekapcsolhatja a fürtöt. Az állandó tárterület az egyetlen olyan szolgáltatás, amelyet ezen a lapon kell konfigurálni. A nyilvános előzetes verzióban az állandó tárolási hely alapértelmezett értéke a gazdagép beállítása során kiválasztott tárolási hely lesz. Ez a mező jelenleg nem szerkeszthető. Ha elkészült, kattintson a **Tovább gombra**.
8. Tekintse át a beállításokat a **felülvizsgálat + létrehozás** oldalon. Ha elégedett, válassza a **Létrehozás** lehetőséget az üzembe helyezés megkezdéséhez. A telepítési folyamat a lap tetején jelenik meg. 
9. A telepítés befejezése után a következő lépések oldalon részletesen **ismertetjük** a fürt kezelését. Ezen a lapon az SSH-kulcs és a Kubernetes irányítópult-token is szerepel. Ha úgy döntött, hogy letiltja a Kubernetes-irányítópultot vagy az Azure arc-integrációt az előző lépésben, előfordulhat, hogy az ezen a lapon található információk és utasítások nem lesznek elérhetők vagy működőképesek.

> [!IMPORTANT] 
> Javasoljuk, hogy az SSH-kulcs és a Kubernetes irányítópult-tokenjét biztonságos, elérhető helyen mentse.

Ebben a rövid útmutatóban egy Azure Kubernetes Service-gazdagépet állít be, és üzembe helyez egy Kubernetes-fürtöt. 

Ha többet szeretne megtudni az Azure Kubernetes Service-ről Azure Stack HCI-n, és megismerheti a Linux-alkalmazások üzembe helyezését és felügyeletét az Azure Kubernetes Service-ben Azure Stack HCI-en, folytassa ezt az oktatóanyagot.
