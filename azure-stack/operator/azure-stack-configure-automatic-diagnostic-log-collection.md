---
title: Proaktív diagnosztikai naplók gyűjteménye Azure Stack hub-ban
description: Ismerje meg, hogyan konfigurálhatja a proaktív diagnosztikai naplókat Azure Stack hub Súgó + támogatás szolgáltatásában.
author: justinha
ms.topic: article
ms.date: 06/16/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 06/16/2020
ms.openlocfilehash: 436e84b2c46de48eba23faf9505cb26e99585c58
ms.sourcegitcommit: a1e2003fb9c6dacdc76f97614ff5a26a5b197b49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91623302"
---
# <a name="proactive-diagnostic-log-collection-in-azure-stack-hub"></a>Proaktív diagnosztikai naplók gyűjteménye Azure Stack hub-ban

::: moniker range=">= azs-2002"

Időt takaríthat meg az ügyfélszolgálattal, ha proaktív módon gyűjti a diagnosztikai naplókat, amikor Azure Stack hub riasztást kap.

Ha a rendszerállapot-feltételek kivizsgálására van szükség, a naplók automatikusan feltölthetők az elemzéshez, mielőtt a támogatási esetet megnyitják Microsoft ügyfélszolgálata.

>[!NOTE]
>Ha nem kapcsolódik az internethez, vagy csak helyileg szeretné menteni a naplókat, használja a [Get-AzureStackLog](azure-stack-get-azurestacklog.md) metódust a naplók elküldéséhez. 

## <a name="steps-to-configure-proactive-log-collection"></a>A proaktív naplózási gyűjtemény konfigurálásának lépései

A következő lépésekkel konfigurálhatja a proaktív naplózási gyűjteményt. Az előjelzéses naplók gyűjtése letiltható, és bármikor újra engedélyezhető.  

1. Jelentkezzen be az Azure Stack Hub felügyeleti portálra.
1. Nyissa meg a **Súgó + támogatás áttekintést**.
1. Ha megjelenik a szalagcím, válassza a **proaktív naplózási gyűjtemény engedélyezése**lehetőséget. =

   :::image type="content" source="media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png" alt-text="Képernyőkép a támogatási oldalról – áttekintés – naplók gyűjteménye. A lila szalagcím egy, a proaktív naplók használatának engedélyezése feliratú gombot tartalmaz." border="false":::

   Vagy válassza a **Beállítások** lehetőséget, és állítsa be az előjelzéses **naplók gyűjteményét** az **engedélyezéshez**, majd válassza a **Mentés**lehetőséget.

   :::image type="content" source="media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png" alt-text="Képernyőkép a támogatási oldalról – áttekintés – naplók gyűjteménye. A lila szalagcím egy, a proaktív naplók használatának engedélyezése feliratú gombot tartalmaz." border="false":::

::: moniker-end
::: moniker range="<= azs-1910"

Javasoljuk, hogy az automatikus diagnosztikai napló gyűjtési funkciójának konfigurálásával egyszerűsítse a naplók gyűjtését és az ügyfél-támogatási élményt.

Ha a rendszerállapot-feltételek kivizsgálására van szükség, a naplók Microsoft ügyfélszolgálata alapján automatikusan tölthetők fel elemzésre.

## <a name="create-an-azure-blob-container-sas-url"></a>Azure Blob Container SAS URL-cím létrehozása

Az automatikus naplók konfigurálása előtt meg kell kapnia egy közös hozzáférési aláírást (SAS) a blob-tárolóhoz. Az SAS lehetővé teszi, hogy a fiók kulcsainak megosztása nélkül hozzáférést biztosítson a Storage-fiók erőforrásaihoz.

Azure Stack hub-naplófájlokat a blob-tárolóba mentheti az Azure-ban, majd megadhatja a SAS URL-címét, ahol a Microsoft ügyfélszolgálata képes gyűjteni a naplókat.

### <a name="prerequisites"></a>Előfeltételek

Új vagy meglévő BLOB-tárolót használhat az Azure-ban. BLOB-tárolónak az Azure-ban való létrehozásához legalább a [Storage blob közreműködő szerepkörre](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) vagy a [megadott engedélyre](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)van szükség. A globális rendszergazdák is rendelkeznek a szükséges engedélyekkel.

Az automatikus naplózási tárolási fiók paramétereinek kiválasztásával kapcsolatos ajánlott eljárásokért tekintse meg az [ajánlott eljárások az automatikus Azure stack hub-napló gyűjtéséhez](./azure-stack-overview.md?view=azs-2002)című témakört. További információ a Storage-fiókok típusairól: az [Azure Storage-fiók áttekintése](/azure/storage/common/storage-account-overview).

### <a name="create-a-blob-storage-account"></a>BLOB Storage-fiók létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza a **Storage-fiókok** > **Hozzáadás**lehetőséget.
1. Hozzon létre egy BLOB-tárolót a következő beállításokkal:

   - **Előfizetés**: válassza ki az Azure-előfizetését.
   - **Erőforráscsoport**: válasszon erőforráscsoportot.
   - **Storage-fiók neve**: adjon meg egy egyedi nevet a Storage-fiókhoz.
   - **Hely**: válasszon egy adatközpontot a vállalati házirendnek megfelelően.
   - **Teljesítmény**: standard.
   - **Fiók típusa** StorageV2 (általános célú v2).
   - **Replikáció**: helyileg redundáns tárolás (LRS).
   - **Hozzáférési szint**: lassú.

   ![BLOB-tároló tulajdonságai](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget.  

### <a name="create-a-blob-container"></a>Blobtároló létrehozása

1. Miután az üzembe helyezés sikeres volt, válassza az **Ugrás erőforráshoz**lehetőséget. A Storage-fiókot az irányítópulton is rögzítheti a könnyű hozzáférés érdekében.
1. Válassza a **Storage Explorer (előnézet)** lehetőséget, kattintson a jobb gombbal a **blob-tárolók**elemre, majd válassza a **blob-tároló létrehozása**lehetőséget.
1. Adja meg az új tároló nevét, majd kattintson **az OK gombra**.

## <a name="create-a-sas-url"></a>SAS URL-cím létrehozása

1. Kattintson a jobb gombbal a tárolóra, majd válassza a **közös hozzáférési aláírás beolvasása**elemet.
   
   ![BLOB-tároló közös hozzáférésű aláírásának beszerzése](media/azure-stack-automatic-log-collection/get-sas.png)

1. Válassza ki a következő tulajdonságokat:

   - Kezdési idő: igény szerint áthelyezheti a kezdési időt
   - Lejárati idő: két év
   - Időzóna: UTC
   - Engedélyek: olvasás, írás és Listázás

   ![Közös hozzáférési aláírás tulajdonságai](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Kattintson a **Létrehozás** gombra.  

Másolja az URL-címet, és adja meg az [automatikus napló-gyűjtemény konfigurálásakor](?view=azs-2002). A SAS URL-címekkel kapcsolatos további információkért lásd: [közös hozzáférésű aláírások (SAS) használata](/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="steps-to-configure-automatic-log-collection"></a>Az automatikus naplózási gyűjtemény konfigurálásának lépései

Az alábbi lépéseket követve adja hozzá az SAS URL-címet a log Collection felhasználói felületéhez:

1. Jelentkezzen be az Azure Stack Hub felügyeleti portálra.
1. Nyissa meg a **Súgó + támogatás áttekintést**.
1. Kattintson az **automatikus gyűjtési beállítások**elemre.

   ![A naplófájlok gyűjtésének engedélyezése a Súgó és támogatás szolgáltatásban](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. Állítsa be az automatikus naplózási gyűjteményt, hogy **engedélyezve**legyen.
1. Adja meg a Storage-fiók blob-tárolójának közös hozzáférés-aláírási (SAS) URL-címét.

   ![BLOB SAS URL-címe](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>Az automatikus naplózási gyűjtemény letiltható, és bármikor újra engedélyezhető. A SAS URL-cím konfigurációja nem változik. Ha az automatikus naplózási gyűjtemény újra engedélyezve van, a korábban beírt SAS URL-cím ugyanazokat az ellenőrzési ellenőrzéseket fogja végezni, és a rendszer elutasítja a lejárt SAS URL-címet.

::: moniker-end

>[!NOTE]
>Ha a naplózási hely beállításai helyi fájlmegosztás használatára vannak konfigurálva, győződjön meg arról, hogy az életciklus-kezelési házirendek megakadályozzák, hogy a megosztási tárolók elérjék a méreteik kvótáját. Azure Stack hub nem figyeli a helyi fájlmegosztást, vagy nem kényszeríti ki az adatmegőrzési házirendeket.

## <a name="view-log-collection"></a>Napló-gyűjtemény megtekintése

A Azure Stack hub-ból gyűjtött naplók előzményei a **Súgó + támogatás** **napló gyűjtemény** lapján jelennek meg, a következő dátumokkal és időpontokkal:

- **Gyűjtés időpontja**: a naplózási művelet megkezdése után.
- **Állapot**: vagy folyamatban vagy kész.
- **Naplók kezdete**: annak az időtartamnak a kezdete, amelynek a gyűjtését el szeretné indítani.
- **Naplók vége**: az időtartam vége.
- **Írja be a következőt**: Ha manuális vagy proaktív napló-gyűjtemény.

![A Súgó és támogatás szolgáltatásban található gyűjtemények naplózása](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="proactive-diagnostic-log-collection-alerts"></a>Proaktív diagnosztikai naplók gyűjtésével kapcsolatos riasztások

Ha engedélyezve van, a proaktív naplók gyűjteménye csak akkor tölti fel a naplókat, ha az alábbi események egyike következik be.

A **frissítés sikertelen volt** például egy olyan riasztás, amely előidézi a proaktív diagnosztikai naplók gyűjtését. Ha engedélyezve van, a rendszer proaktív módon rögzíti a diagnosztikai naplókat a frissítés során, hogy segítsen Microsoft ügyfélszolgálata a probléma megoldásában. A rendszer csak akkor gyűjti a diagnosztikai naplókat, ha a frissítésre vonatkozó riasztást **nem sikerült** megemelni.

| Riasztás címe | FaultIdType |
|---|---|
|Nem lehet csatlakozni a távoli szolgáltatáshoz | UsageBridge.NetworkError|
|Sikertelen frissítés | Urp.UpdateFailure |
|Tárolási erőforrás-szolgáltatói infrastruktúra/függőségek nem érhetők el |    StorageResourceProviderDependencyUnavailable |
|A csomópont nem csatlakozik a vezérlőhöz| ServerHostNotConnectedToController |  
|Útvonal-közzétételi hiba | SlbMuxRoutePublicationFailure |
|A tárolási erőforrás-szolgáltató belső adattára nem érhető el |    StorageResourceProvider. DataStoreConnectionFail |
|Hiba az adattároló eszközön | Microsoft. Health. hibatípushoz. előzőtől. leválasztva |
|Az állapot-vezérlő nem fér hozzá a Storage-fiókhoz | Microsoft. Health. hibatípushoz. StorageError |
|A fizikai lemezzel létesített kapcsolat megszakadt | Microsoft. Health. hibatípushoz. lemez. LostCommunication |
|A blob szolgáltatás nem fut csomóponton. | A StorageService. The. blob. Service. nem fut. on. a. csomópont – kritikus |
|Infrastruktúra-szerepkör sérült | Microsoft. Health. hibatípushoz. GenericExceptionFault |
|Hibák a Table Service-ben | StorageService. table. Service. errors – kritikus |
|A fájlmegosztás több mint 80%-ot használ | Microsoft. Health. hibatípushoz. fájlmegosztás. Capacity. warning. infra |
|A skálázásiegység-csomópont offline állapotban van | FRP. Szívverés. PhysicalNode |
|Az infrastruktúra-szerepkör példánya nem érhető el | FRP. Szívverés. InfraVM |
|Az infrastruktúra-szerepkör példánya nem érhető el  | FRP. Szívverés. NonHaVm |
|Az infrastruktúra-szerepkör, a címtár-kezelés, az idő szinkronizációs hibáit jelentette | DirectoryServiceTimeSynchronizationError |
|Külső tanúsítvány lejárata miatt függőben | CertificateExpiration. ExternalCert. warning |
|Külső tanúsítvány lejárata miatt függőben | CertificateExpiration. ExternalCert. Critical |
|Az adott osztályú és méretű virtuális gépek az alacsony memóriakapacitás miatt nem építhetők ki | AzureStack. ComputeController. VmCreationFailure. LowMemory |
|A csomópont nem érhető el a virtuális gép elhelyezéséhez | AzureStack. ComputeController. HostUnresponsive |
|Sikertelen biztonsági mentés  | AzureStack. BackupController. BackupFailedGeneralFault |
|Az ütemezett biztonsági mentés a sikertelen műveletekkel való ütközés miatt kimaradt    | AzureStack. BackupController. BackupSkippedWithFailedOperationFault |

## <a name="see-also"></a>Lásd még

[Azure Stack hub-napló és az ügyfelek adatkezelése](azure-stack-data-collection.md)
