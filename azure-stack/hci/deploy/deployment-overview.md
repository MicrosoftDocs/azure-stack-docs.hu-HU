---
title: Azure Stack HCI-telepítés áttekintése
description: Az Azure Stack HCI telepítési folyamat áttekintése.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/09/2020
ms.openlocfilehash: 34a93a65d45861c7c7ff1727347cc95465968151
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572517"
---
# <a name="what-is-the-deployment-process-for-azure-stack-hci"></a>Mi a Azure Stack HCI telepítési folyamata?

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a témakör részletes áttekintést nyújt az Azure Stack HCI telepítési folyamatról, és részletesebb információkra mutató hivatkozásokat tartalmaz.

## <a name="plan"></a>Felkészülés

A üzembe helyezés előtt gondosan tervezze meg a többhelyes fürtözéshez szükséges tárhelyet, hálózatkezelést és követelményeket (ha van ilyen).

### <a name="plan-storage"></a>Tárterület tervezése

Azure Stack a HCI az iparági szabványnak megfelelő kiszolgálókat használ helyi csatlakoztatott meghajtókkal, amelyek kiválóan elérhető, jól méretezhető, szoftveresen definiált tárhelyet hoznak létre. A teljesítményre és a kapacitásra vonatkozó követelmények teljesítéséhez gondosan [válassza a meghajtók lehetőséget](../concepts/choose-drives.md) , és [tervezze meg a köteteket](../concepts/plan-volumes.md).

### <a name="plan-networking"></a>Hálózatkezelés megtervezése

Jegyezze fel a kiszolgáló nevét, tartománynevét, RDMA protokollját és verzióját, valamint a VLAN-azonosítót a telepítéshez.

### <a name="plan-stretched-clusters"></a>Kifeszített fürtök megtervezése

Ha a Azure Stack HCI üzembe helyezése több helyre is kiterjed, határozza meg, hogy hány kiszolgálóra lesz szüksége az egyes helyeken, és hogy a fürt konfigurációja aktív/passzív vagy aktív/aktív lesz-e. További információ: [a kifeszített fürtök ismertetése](../concepts/stretched-clusters.md).

## <a name="deploy"></a>Üzembe helyezés

### <a name="1-before-you-begin"></a>1. a Kezdés előtt

Mielőtt elkezdené, [állapítsa meg, hogy a hardver megfelel-e az alapkövetelményeknek, és összegyűjtse](before-you-start.md) a Azure stack HCI telepítéséhez szükséges információkat. Ezután [telepítse a Windows felügyeleti központot](/windows-server/manage/windows-admin-center/deploy/install) a Azure stack HCI-fürt kezelésére.

### <a name="2-deploy-azure-stack-hci"></a>2. Azure Stack HCI üzembe helyezése

[Töltse le Azure stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -t, és telepítse a Azure stack HCI [operációs rendszert](operating-system.md) a fürtbe felvenni kívánt összes kiszolgálóra.

### <a name="3-create-the-cluster"></a>3. a fürt létrehozása

Hozzon létre egy feladatátvevő fürtöt a [Windows felügyeleti központtal](create-cluster.md) vagy a [PowerShell](create-cluster-powershell.md)-lel. A natív vész-helyreállítás és az üzletmenet folytonossága érdekében olyan kihelyezett [fürtöt](../concepts/stretched-clusters.md) helyezhet üzembe, amely két földrajzilag különálló helyet ölel fel.

### <a name="4-set-up-a-cluster-witness"></a>4. tanúsító fürt beállítása

[A tanúsító erőforrások beállítása](witness.md) minden fürt esetében kötelező. A két csomópontos fürtöknek egy tanúsító elemre van szükségük, hogy a kiszolgáló offline állapotba kerüljön, a másik csomópont is elérhetetlenné válik. A három és a magasabb csomópontos fürtöknek egy tanúsító kell lenniük ahhoz, hogy elbírjanak két kiszolgáló meghibásodását vagy offline állapotba helyezését. 

### <a name="5-register-with-azure"></a>5. regisztráció az Azure-ban

Azure Stack HCI kapcsolatra van szükség az Azure-hoz. A fürt az Azure-hoz való összekapcsolásával kapcsolatban lásd: [Azure stack HCI regisztrálása az Azure](register-with-azure.md)-ban. A regisztrálás után a fürt automatikusan csatlakozik a háttérben.

### <a name="6-validate-the-cluster"></a>6. a fürt ellenőrzése

A fürt létrehozása és regisztrálása után [futtassa a fürt-ellenőrzési teszteket](validate.md) a hardveres vagy konfigurációs problémák észleléséhez, mielőtt a fürt bekerül az éles környezetbe.

### <a name="7-deploy-storage"></a>7. tároló üzembe helyezése

[Hozzon létre köteteket](../manage/create-volumes.md) egy egyhelyes fürtön, vagy [hozzon létre köteteket, és állítsa be a replikálást egy kifeszített fürtön](../manage/create-stretched-volumes.md).

### <a name="8-deploy-workloads"></a>8. munkaterhelések üzembe helyezése

Most már készen áll a [virtuális gépek létrehozására](../manage/vm.md) és a számítási feladatok üzembe helyezésére Azure stack HCI-ben a Windows felügyeleti központon keresztül.

## <a name="next-steps"></a>További lépések

Megtudhatja, mit kell tennie a Azure Stack HCI üzembe helyezése előtt.

> [!div class="nextstepaction"]
> [Előkészületek](before-you-start.md)
