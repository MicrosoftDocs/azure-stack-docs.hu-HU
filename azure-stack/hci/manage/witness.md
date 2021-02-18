---
title: Tanúsító fürt beállítása
description: Ismerje meg, hogyan állíthat be egy tanúsító fürtöt
author: v-dasis
ms.topic: how-to
ms.date: 02/17/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 32d0f717e987d757f5315cfe048c75300ae9c776
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647903"
---
# <a name="set-up-a-cluster-witness"></a>Tanúsító fürt beállítása

> A következőre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

A tanúsító erőforrások beállítása minden fürt esetében kifejezetten ajánlott, és közvetlenül a fürt létrehozása után kell beállítani. A két csomópontos fürtöknek egy tanúsító elemre van szükségük, hogy a kiszolgáló offline állapotba kerüljön, a másik csomópont is elérhetetlenné válik. A három és a magasabb csomópontos fürtöknek egy tanúsító kell lenniük ahhoz, hogy elbírjanak két kiszolgáló meghibásodását vagy offline állapotba helyezését.  

Az SMB-fájlmegosztást tanúsító vagy Azure-beli Felhőbeli tanúsító is használhatja. Egy Azure-beli Felhőbeli tanúsító használata ajánlott, ha a fürtben található összes kiszolgáló-csomópont megbízható internetkapcsolattal rendelkezik. Ez a cikk a Felhőbeli tanúsító létrehozásával foglalkozik.

## <a name="before-you-begin"></a>Előkészületek

A Felhőbeli tanúsító létrehozása előtt rendelkeznie kell egy Azure-fiókkal és-előfizetéssel, és regisztrálnia kell az Azure Stack HCI-fürtöt az Azure-ban. További információt a következő cikkekben talál:

- [Azure-fiók létrehozása](https://docs.microsoft.com/dotnet/azure/create-azure-account)
- Ha van ilyen, [hozzon létre egy további Azure-előfizetést](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription)
- [Az Azure Stack HCI csatlakoztatása az Azure-hoz](../deploy/register-with-azure.md)

A fájlmegosztást tanúsító fájlmegosztás esetében követelmények vonatkoznak a fájlkiszolgálón. További információt a [rendszerkövetelmények](../concepts/system-requirements.md) című témakörben talál.

## <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

Ez a szakasz azt ismerteti, hogyan hozható létre Azure Storage-fiók. Ez a fiók egy adott fürthöz való választottbírósági eljáráshoz használt Azure Blob-fájl tárolására szolgál. Ugyanazzal az Azure Storage-fiókkal több fürthöz is konfigurálhat Felhőbeli tanúsító.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A Azure Portal kezdőlapon az Azure- **szolgáltatások** területen válassza a **Storage-fiókok** lehetőséget. Ha hiányzik az ikon, válassza az **erőforrás létrehozása** lehetőséget a Storage- *fiókok* erőforrásának első létrehozásához.

    :::image type="content" source="media/witness/cloud-witness-home.png" alt-text="Azure Portal kezdőképernyő" lightbox="media/witness/cloud-witness-home.png":::

1. A **Storage-fiókok** lapon válassza az **új** lehetőséget.

    :::image type="content" source="media/witness/cloud-witness-create.png" alt-text="Azure új Storage-fiók" lightbox="media/witness/cloud-witness-create.png":::

1. A **Storage-fiók létrehozása** oldalon hajtsa végre a következőket:
    1. Válassza ki azt az Azure- **előfizetést** , amelyre alkalmazni szeretné a Storage-fiókot.
    1. Válassza ki azt az Azure- **erőforráscsoportot** , amelyre alkalmazni szeretné a Storage-fiókot.
    1. Adjon meg egy nevet a **Storage-fióknak**.
    <br>A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Ennek a névnek egyedinek kell lennie az Azure-on belül is.
    1. Válassza ki a fizikailag legközelebb eső **helyet** .
    1. A **Teljesítmény** mezőben válassza a **Standard** lehetőséget.
    1. A **Fiók típusa** területen válassza az **általános célú tárolás** lehetőséget.
    1. A **replikáláshoz** válassza a **helyileg REDUNDÁNS tárolás (LRS)** lehetőséget.
    1. Ha elkészült, kattintson a **felülvizsgálat + létrehozás** gombra.

    :::image type="content" source="media/witness/cloud-witness-create-storage-account.png" alt-text="Azure Storage-fiók létrehozása" lightbox="media/witness/cloud-witness-create-storage-account.png":::

1. Győződjön meg arról, hogy a Storage-fiók ellenőrzi az ellenőrzést, majd tekintse át a Fiókbeállítások beállítást. Ha végzett, kattintson a **Létrehozás** gombra.

    :::image type="content" source="media/witness/cloud-witness-validation.png" alt-text="Azure Storage-fiók ellenőrzése" lightbox="media/witness/cloud-witness-validation.png":::

1. Eltarthat néhány másodpercig, amíg a fiókok üzembe helyezése az Azure-ban történik. Ha a telepítés befejeződött, kattintson **az erőforrás** megnyitása lehetőségre.

    :::image type="content" source="media/witness/cloud-witness-deployment.png" alt-text="Azure Storage-fiók üzembe helyezése" lightbox="media/witness/cloud-witness-deployment.png":::

## <a name="copy-the-access-key-and-endpoint-url"></a>A hozzáférési kulcs és a végpont URL-címének másolása

Azure Storage-fiók létrehozásakor a folyamat automatikusan létrehoz két hozzáférési kulcsot, egy elsődleges kulcsot (key1) és egy másodlagos kulcsot (key2). Ha első alkalommal hoz létre egy Felhőbeli tanúsító, a rendszer **key1** használ. A végpont URL-címe is automatikusan létrejön.

Az Azure-beli Felhőbeli tanúsító egy blob-fájlt használ a tároláshoz, és az *storage_account_name. blob. Core. Windows. net* formátumú végpontot hozza létre végpontként. 

> [!NOTE]  
> Az Azure-beli Felhőbeli tanúsító a HTTPS protokollt használja (az alapértelmezett 443-as port) az Azure Blob Service-szel folytatott kommunikáció érdekében. Győződjön meg arról, hogy a HTTPS-port elérhető.

### <a name="copy-the-account-name-and-access-key"></a>A fiók nevének és elérési kulcsának másolása

1. A Azure Portal a **Beállítások** területen válassza a **hozzáférési kulcsok** elemet.
1. Válassza a **kulcsok megjelenítése** lehetőséget a legfontosabb információk megjelenítéséhez.
1. Kattintson a **Storage-fiók neve** és a **key1** mező jobb oldalán lévő másolás és beillesztés ikonra, és illessze be az egyes szöveges karakterláncokat a Jegyzettömbbe vagy más szövegszerkesztőbe.

    :::image type="content" source="media/witness/cloud-witness-access-keys.png" alt-text="Azure Storage-fiók hozzáférési kulcsainak" lightbox="media/witness/cloud-witness-access-keys.png":::

### <a name="copy-the-endpoint-url-optional"></a>A végpont URL-címének másolása (nem kötelező)

A végpont URL-címe nem kötelező, és előfordulhat, hogy nem szükséges egy Felhőbeli tanúsító.

1. A Azure Portal válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **kulcsok megjelenítése** lehetőséget a végponti információk megjelenítéséhez.
1. A **blob Service** alatt kattintson a **blob Service** mező jobb oldalán lévő másolás és beillesztés ikonra, és illessze be a szöveges karakterláncot a Jegyzettömbbe vagy más szövegszerkesztőbe.

    :::image type="content" source="media/witness/cloud-witness-blob-service.png" alt-text="Azure Blob-végpont" lightbox="media/witness/cloud-witness-blob-service.png":::

## <a name="create-a-cloud-witness-using-windows-admin-center"></a>Felhőbeli tanú létrehozása a Windows felügyeleti központtal

Most már készen áll a Windows felügyeleti központ használatával létrehozni egy tanúsító példányt a fürthöz.

1. A Windows felügyeleti központban válassza ki a **Fürtfelügyelő** elemet a felső legördülő listából.
1. A **fürt kapcsolatai** területen válassza ki a fürtöt.
1. Az **eszközök** területen válassza a **Beállítások** lehetőséget.
1. A jobb oldali ablaktáblán válassza a **tanúsító** elemet.
1. A **tanúsító típusa** beállításnál válassza a következők egyikét:
      - **Felhőbeli tanúsító** – adja meg az Azure Storage-fiók nevét, a hozzáférési kulcsot és a végpont URL-címét az előzőekben leírtak szerint.
      - **Tanúsító fájlmegosztás** – adja meg a fájlmegosztás elérési útját: "(//Server/share)"
1. Felhőbeli tanúsító esetén a következő mezőkbe illessze be a korábban átmásolt szöveges karakterláncokat:
    1. **Azure Storage-tárfiók neve**
    1. **Azure Storage-hozzáférési kulcs**
    1. **Azure szolgáltatási végpont**

    :::image type="content" source="media/witness/cloud-witness-1.png" alt-text="Felhőbeli tanúsító hozzáférési kulcsok" lightbox="media/witness/cloud-witness-1.png":::

1. Amikor végzett, kattintson a **Mentés** gombra. Eltarthat egy kis ideig, amíg az információk propagálni tudnak az Azure-ba.

> [!NOTE]
> A harmadik lehetőség, a **tanúsító lemez** nem alkalmas a kifeszített fürtökben való használatra.

## <a name="create-a-cloud-witness-using-windows-powershell"></a>Felhőbeli tanúsító létrehozása a Windows PowerShell használatával

Azt is megteheti, hogy a PowerShell használatával létrehoz egy tanúsító példányt a fürthöz.

A következő parancsmaggal hozzon létre egy Azure-beli Felhőbeli tanúsító. Adja meg az Azure Storage-fiók nevét és a hozzáférési kulcs adatait az előzőekben leírtak szerint:

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

A tanúsító fájlmegosztás létrehozásához használja a következő parancsmagot. Adja meg a fájlkiszolgáló megosztásának elérési útját:

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>Következő lépések

- A fürt kvórumával kapcsolatos további információkért lásd: [a fürt és a készlet Kvórumának ismertetése Azure stack HCI](../concepts/quorum.md)-ben.

- További információ az Azure Storage-fiókok létrehozásáról és kezeléséről: [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-account-create).