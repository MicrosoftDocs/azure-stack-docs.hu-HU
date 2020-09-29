---
title: Tanúsító fürt beállítása
description: Ismerje meg, hogyan állíthat be egy tanúsító fürtöt
author: v-dasis
ms.topic: how-to
ms.date: 08/11/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 43d63e4e565a36c30837e2a8b460e0d73a35c0a5
ms.sourcegitcommit: 7d518629bd55f24e7459404bb19b7db8a54f4b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2020
ms.locfileid: "88145536"
---
# <a name="set-up-a-cluster-witness"></a>Tanúsító fürt beállítása

> A következőre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A tanúsító erőforrások beállítása minden fürt esetében kötelező, és közvetlenül a fürt létrehozása után kell beállítani. A két csomópontos fürtöknek egy tanúsító elemre van szükségük, hogy a kiszolgáló offline állapotba kerüljön, a másik csomópont is elérhetetlenné válik. A három és a magasabb csomópontos fürtöknek egy tanúsító kell lenniük ahhoz, hogy elbírjanak két kiszolgáló meghibásodását vagy offline állapotba helyezését.  

Használhat egy SMB-fájlmegosztást tanúsító, vagy használhat egy Azure-beli Felhőbeli tanúsító is. Egy Azure-beli Felhőbeli tanúsító használata ajánlott, ha a fürtben található összes kiszolgáló-csomópont megbízható internetkapcsolattal rendelkezik. További információ: [Felhőbeli tanúsító üzembe helyezése feladatátvevő fürtön](/windows-server/failover-clustering/deploy-cloud-witness).

A fájl-megosztási tanúk esetében a fájlkiszolgáló követelményei vannak. További információkért lásd: [Azure stack HCI telepítése előtt](before-you-start.md) .

## <a name="set-up-a-witness-using-windows-admin-center"></a>Tanú beállítása a Windows felügyeleti központtal

1. A Windows felügyeleti központban válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. A **fürt kapcsolatai**területen válassza ki a fürtöt.
1. Az **eszközök**területen válassza a **Beállítások**lehetőséget.
1. A jobb oldali ablaktáblán válassza a **tanúsító**elemet.
1. A **tanúsító típusa**beállításnál válassza a következők egyikét:
      - **Felhőbeli tanúsító** – adja meg az Azure Storage-fiók nevét, a hozzáférési kulcsot és a végpont URL-címét az alább leírtak szerint.
      - **Tanúsító fájlmegosztás** – adja meg a fájlmegosztás elérési útját: "(//Server/share)"

> [!NOTE]
> A harmadik lehetőség, a **tanúsító lemez**nem alkalmas a kifeszített fürtökben való használatra.

## <a name="create-an-azure-storage-account-to-use-as-a-cloud-witness"></a>Felhőbeli tanúsító használandó Azure Storage-fiók létrehozása

Ez a szakasz azt ismerteti, hogyan hozhat létre egy Storage-fiókot, és hogyan tekinthet meg és másolhat végponti URL-címeket és hozzáférési kulcsokat a fiókhoz.

A Felhőbeli tanúsító szolgáltatás konfigurálásához érvényes Azure Storage-fiókkal kell rendelkeznie, amely a blob-fájl (választottbírósági eljáráshoz használt) tárolására használható. A Felhőbeli tanúsító a Microsoft Storage-fiókban létrehoz egy jól ismert tárolót ( **MSFT-Cloud-tanúsító** ). A Felhőbeli tanúsító egy olyan blob-fájlt ír be, amely a megfelelő fürt egyedi AZONOSÍTÓját használja, amely az ezen **MSFT-Cloud-tanúsító** tárolóban lévő blob fájl neveként szerepel. Ez azt jelenti, hogy ugyanazt a Microsoft Azure Storage fiókot használhatja a Felhőbeli tanúk több különböző fürthöz való konfigurálásához.

Ha ugyanazt az Azure Storage-fiókot használja a Felhőbeli tanúsító több különböző fürthöz való konfigurálásához, az egyetlen **MSFT-Cloud-tanúsító** tároló automatikusan létrejön. Ez a tároló fürtön egy blob-fájlt fog tartalmazni.

> [!NOTE]  
> A Felhőbeli tanúsító a HTTPS protokollt használja (az alapértelmezett 443-as port) az Azure Blob Service-szel folytatott kommunikációhoz. Győződjön meg arról, hogy a HTTPS-port elérhető a hálózati proxyn keresztül.

### <a name="to-create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A központi menüben válassza a New-> az adattároló > a Storage-fiók lehetőséget.
1. A Storage-fiók létrehozása lapon tegye a következőket:
    1. Adja meg a tárfiók nevét.
    <br>A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. A Storage-fiók nevének az Azure-on belül is egyedinek kell lennie.
    1. A **Fiók típusa**területen válassza az **általános célú**lehetőséget.
    <br>Nem használhat blob Storage-fiókot Felhőbeli tanúsító számára.
    1. A **Teljesítmény** mezőben válassza a **Standard** lehetőséget.
    <br>Az Azure Premium Storage nem használható a Felhőbeli tanúk számára.
    1. A **replikáláshoz**válassza a **helyileg REDUNDÁNS tárolás (LRS)** lehetőséget.
    <br>A feladatátvételi fürtszolgáltatás a blob-fájlt használja választottbírósági pontként, amely némi konzisztencia-garanciát igényel az adatolvasáskor. Ezért ki kell választania a **helyileg redundáns tárolást** a **replikálási** típushoz.

### <a name="view-and-copy-storage-access-keys-for-your-azure-storage-account"></a>Az Azure Storage-fiókhoz tartozó Storage-hozzáférési kulcsok megtekintése és másolása

Microsoft Azure Storage fiók létrehozásakor a rendszer két, automatikusan az elsődleges elérési kulcs és a másodlagos elérési kulcs által generált hozzáférési kulcshoz van társítva. A Felhőbeli tanúk első alkalommal történő létrehozásához használja az **elsődleges hozzáférési kulcsot**. Nincs korlátozás arra vonatkozóan, hogy melyik kulcsot használja a Felhőbeli tanúsító.  

#### <a name="to-view-and-copy-storage-access-keys"></a>Tároló-hozzáférési kulcsok megtekintése és másolása

A Azure Portal navigáljon a Storage-fiókjához, kattintson az **összes beállítás** elemre, majd kattintson a **hozzáférési kulcsok** elemre a fiók hozzáférési kulcsainak megtekintéséhez, másolásához és újralétrehozásához. Az Elérési kulcsok panel is tartalmaz az elsődleges és másodlagos kulcsok segítségével előre konfigurált kapcsolati sztringekat, amelyeket az alkalmazásokban történő használat céljából másolhat.

:::image type="content" source="media/witness/cloud-witness-1.png" alt-text="Felhőbeli tanúsító hozzáférési kulcsok" lightbox="media/witness/cloud-witness-1.png":::

### <a name="view-and-copy-endpoint-url-links"></a>Végpont URL-hivatkozásainak megtekintése és másolása

A Storage-fiók létrehozásakor a következő URL-címek jönnek létre a formátum használatával: `https://<Storage Account Name>.<Storage Type>.<Endpoint>`  

A Felhőbeli tanúsító mindig a **blobot** használja tárolási típusként. Az Azure a **. Core.Windows.net** -t használja végpontként. A Felhőbeli tanúsító beállításakor lehetséges, hogy egy másik végponttal konfigurálja azt a forgatókönyv szerint (például a Kínában Microsoft Azure adatközpont egy másik végponttal rendelkezik).  

> [!NOTE]  
> A végponti URL-címet a Felhőbeli tanúsító erőforrás automatikusan generálja, és az URL-címhez nem szükséges további konfigurációs lépés.  

#### <a name="to-view-and-copy-endpoint-url-links"></a>Végpont URL-hivatkozásainak megtekintése és másolása

A Azure Portal navigáljon a Storage-fiókjához, kattintson a **minden beállítás** elemre, majd kattintson a **Tulajdonságok** elemre a végponti URL-címek megtekintéséhez és másolásához.  

:::image type="content" source="media/witness/cloud-witness-2.png" alt-text="Felhőbeli tanúsító végpont URL-címe" lightbox="media/witness/cloud-witness-2.png":::  

## <a name="set-up-a-witness-using-windows-powershell"></a>Tanúsító beállítása a Windows PowerShell használatával

Ha egy tanúsító fürtöt a PowerShell használatával szeretne beállítani, futtassa az alábbi parancsmagok egyikét.

Az alábbi parancsmaggal hozzon létre egy Azure-beli Felhőbeli tanúsító:

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

A következő parancsmag használatával hozzon létre egy tanúsító fájlmegosztást:

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>Következő lépések

- A fürt kvórumával kapcsolatos további információkért lásd: [a fürt és a készlet Kvórumának ismertetése Azure stack HCI](../concepts/quorum.md)-ben.

- Az Azure Storage-fiókok létrehozásával és kezelésével kapcsolatos további információkért lásd: [Tudnivalók az Azure Storage-fiókokról](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/).
