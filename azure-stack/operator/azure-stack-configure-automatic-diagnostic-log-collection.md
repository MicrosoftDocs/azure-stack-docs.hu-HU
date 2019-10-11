---
title: Automatikus Azure Stack naplózási gyűjtemény konfigurálása | Microsoft Docs
description: Az automatikus naplók konfigurálása a Azure Stack Súgó és támogatás szolgáltatásban.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: e9ee5d3b8ad67c7955fa4da7b64d2c0962f21a15
ms.sourcegitcommit: 534117888d9b7d6d363ebe906a10dcf0acf8b685
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173081"
---
# <a name="configure-automatic-azure-stack-diagnostic-log-collection"></a>A diagnosztikai naplók automatikus Azure Stackának konfigurálása

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Javasoljuk, hogy az automatikus diagnosztikai napló gyűjtési funkciójának konfigurálásával egyszerűsítse a naplók gyűjtését és az ügyfél-támogatási élményt. Ha a rendszerállapot-feltételek kivizsgálására van szükség, a rendszer automatikusan feltöltheti a naplókat a Microsoft Customer Support Services (CSS) által végzett elemzéshez. 

## <a name="create-an-azure-blob-container-sas-url"></a>Azure Blob Container SAS URL-cím létrehozása 

Az automatikus naplók konfigurálása előtt meg kell kapnia egy közös hozzáférési aláírást (SAS) a blob-tárolóhoz. Az SAS lehetővé teszi, hogy a fiók kulcsainak megosztása nélkül hozzáférést biztosítson a Storage-fiók erőforrásaihoz. Azure Stack naplófájlokat a blob-tárolóba mentheti az Azure-ban, majd megadhatja a SAS URL-címét, ahol a CSS képes gyűjteni a naplókat. 

### <a name="prerequisites"></a>Előfeltételek

Új vagy meglévő BLOB-tárolót használhat az Azure-ban. BLOB-tárolónak az Azure-ban való létrehozásához legalább a [Storage blob közreműködő szerepkörre](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) vagy a [megadott engedélyre](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)van szükség. A globális rendszergazdák is rendelkeznek a szükséges engedélyekkel. 

Az automatikus naplózási tárolási fiók paramétereinek kiválasztásával kapcsolatos ajánlott eljárásokért tekintse meg az [ajánlott eljárások az automatikus Azure stack a naplók gyűjtéséhez](azure-stack-best-practices-automatic-diagnostic-log-collection.md)című témakört. További információ a Storage-fiókok típusairól: az [Azure Storage-fiók áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

### <a name="create-a-blob-storage-account"></a>BLOB Storage-fiók létrehozása
 
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Kattintson a **Storage-fiókok** > **Hozzáadás**elemre. 
1. Hozzon létre egy BLOB-tárolót a következő beállításokkal:
   - **Előfizetés**: válassza ki az Azure-előfizetését
   - **Erőforráscsoport**: erőforráscsoport meghatározása
   - **Storage-fiók neve**: adjon meg egy egyedi nevet a Storage-fiókhoz
   - **Hely**: válasszon egy adatközpontot a vállalati házirendnek megfelelően
   - **Teljesítmény**: válassza a standard lehetőséget
   - **Fiók típusa** StorageV2 kiválasztása (általános célú v2) 
   - **Replikáció**: válassza a helyileg redundáns tárolás (LRS) lehetőséget
   - **Hozzáférési szint**: válassza a jó lehetőséget

   ![A blob-tároló tulajdonságait ábrázoló képernyőkép](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Kattintson a **felülvizsgálat + létrehozás** , majd a **Létrehozás**elemre.  

### <a name="create-a-blob-container"></a>Blobtároló létrehozása 

1. Miután az üzembe helyezés sikeres volt, kattintson az **erőforrás keresése**elemre. A Storage-fiókot az irányítópulton is rögzítheti a könnyű hozzáférés érdekében. 
1. Kattintson a **Storage Explorer (előnézet)** gombra, kattintson a jobb gombbal a **blob-tárolók**elemre, majd kattintson a **blob-tároló létrehozása**elemre 
1. Adja meg az új tároló nevét, majd kattintson **az OK**gombra.

## <a name="create-a-sas-url"></a>SAS URL-cím létrehozása

1. Kattintson a jobb gombbal a tárolóra, majd kattintson a **megosztott hozzáférés aláírásának beolvasása**elemre. 
   
   ![A blob-tároló megosztott hozzáférési aláírásának beszerzését bemutató képernyőkép](media/azure-stack-automatic-log-collection/get-sas.png)

1. Válassza ki a következő tulajdonságokat:
   - Kezdési idő: igény szerint áthelyezheti a kezdési időt 
   - Lejárati idő: két év
   - Időzóna: UTC
   - Engedélyek: olvasás, írás és Listázás

   ![A megosztott hozzáférési aláírás tulajdonságait bemutató képernyőfelvétel](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Kattintson a  **Create** (Létrehozás) gombra.  

Másolja az URL-címet, és adja meg az [automatikus napló-gyűjtemény konfigurálásakor](azure-stack-configure-automatic-diagnostic-log-collection.md). A SAS URL-címekkel kapcsolatos további információkért lásd: [közös hozzáférésű aláírások (SAS) használata](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 


## <a name="steps-to-configure-automatic-log-collection"></a>Az automatikus naplózási gyűjtemény konfigurálásának lépései

Az alábbi lépéseket követve adja hozzá az SAS URL-címet a log Collection felhasználói felületéhez: 

1. Jelentkezzen be a Azure Stack felügyeleti portálra.
1. Nyissa meg **a Súgó és támogatás áttekintést**.
1. Kattintson az **automatikus gyűjtési beállítások**elemre.

   ![Képernyőkép: a naplók használatának engedélyezése a súgóban és támogatásban](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. Állítsa be az automatikus naplózási gyűjteményt, hogy **engedélyezve**legyen.
1. Adja meg a Storage-fiók blob-tárolójának közös hozzáférés-aláírási (SAS) URL-címét.

   ![Képernyőfelvétel a blob SAS URL-címét jeleníti meg](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>Az automatikus naplózási gyűjtemény letiltható, és bármikor újra engedélyezhető. A SAS URL-cím konfigurációja nem változik. Ha az automatikus naplózási gyűjtemény újra engedélyezve van, a korábban beírt SAS URL-cím ugyanazokat az ellenőrzési ellenőrzéseket fogja végezni, és a rendszer elutasítja a lejárt SAS URL-címet. 

## <a name="view-log-collection"></a>Napló-gyűjtemény megtekintése

A Azure Stack gyűjtött naplók előzményei a Súgó és támogatás **napló gyűjtemény** lapján jelennek meg a következő dátumokkal és időpontokkal:

- **Gyűjtés időpontja**: a naplózási művelet megkezdése után 
- Kezdő **dátum**: annak az időszaknak a kezdete, amelynek a gyűjtését el szeretné indítani
- Záró **dátum**: az időszak vége

![Képernyőfelvétel a naplókról](media/azure-stack-automatic-log-collection/azure-stack-log-collection.png)

Ha a diagnosztikai napló gyűjtése sikertelen, ellenőrizze, hogy érvényes-e az SAS URL-címe. Ha a hiba továbbra is fennáll, vagy több hiba jelenik meg, kérjen segítséget a Microsoft CSS-ben. 

Az operátorok is megnézhetik a Storage-fiókot az automatikusan összegyűjtött naplókhoz. Ez a képernyőkép például a naplózási gyűjteményeket jeleníti meg a Azure Portal Storage Explorer előzetes verziójának használatával:

![Képernyőfelvétel a naplókról](media/azure-stack-automatic-log-collection/check-storage-account.png)

## <a name="automatic-diagnostic-log-collection-alerts"></a>Diagnosztikai naplók automatikus gyűjtésével kapcsolatos riasztások 

Ha engedélyezve van, az automatikus diagnosztikai napló gyűjtése csak akkor történik meg, ha szükséges. Csak a következő táblázatban található riasztások gyűjteménye. 

A **frissítés sikertelen volt** például egy olyan riasztás, amely elindítja az automatikus diagnosztikai naplók gyűjtését. Ha engedélyezve van az automatikus gyűjtés, a rendszer proaktív módon rögzíti a diagnosztikai naplókat a frissítés során a probléma elhárítása érdekében. A rendszer csak akkor gyűjti a diagnosztikai naplókat, ha a frissítésre vonatkozó riasztást **nem sikerült** megemelni. 

|Riasztás címe  | FaultIdType|    
|-------------|------------|
|Nem lehet csatlakozni a távoli szolgáltatáshoz |  UsageBridge.NetworkError|
|Sikertelen frissítés |    Urp.UpdateFailure   |          
|Tárolási erőforrás-szolgáltatói infrastruktúra/függőségek nem érhetők el |  StorageResourceProviderDependencyUnavailable     |     
|A csomópont nem csatlakozik a vezérlőhöz|  ServerHostNotConnectedToController   |     
|Útvonal-közzétételi hiba |    SlbMuxRoutePublicationFailure | 
|A tárolási erőforrás-szolgáltató belső adattára nem érhető el |    StorageResourceProvider. DataStoreConnectionFail     |       
|Hiba az adattároló eszközön | Microsoft. Health. hibatípushoz. előzőtől. leválasztva   |      
|Az állapotvezérlő nem éri el a tárfiókot | Microsoft. Health. hibatípushoz. StorageError |    
|A fizikai lemezzel létesített kapcsolat megszakadt |    Microsoft. Health. hibatípushoz. lemez. LostCommunication    |    
|A blob szolgáltatás nem fut csomóponton. | A StorageService. The. blob. Service. nem fut. on. a. csomópont – kritikus | 
|Infrastruktúra-szerepkör sérült |    Microsoft. Health. hibatípushoz. GenericExceptionFault |        
|Hibák a Table Service-ben | StorageService. table. Service. errors – kritikus |              
|A fájlmegosztás több mint 80%-ot használ |    Microsoft. Health. hibatípushoz. fájlmegosztás. Capacity. warning. infra |       
|A skálázásiegység-csomópont offline állapotban van | FRP. Szívverés. PhysicalNode |  
|Az infrastruktúra-szerepkör példánya nem érhető el | FRP. Szívverés. InfraVM   |    
|Az infrastruktúra-szerepkör példánya nem érhető el  |    FRP. Szívverés. NonHaVm     |        
|Az infrastruktúra-szerepkör, a címtár-kezelés, az idő szinkronizációs hibáit jelentette |  DirectoryServiceTimeSynchronizationError |     
|Külső tanúsítvány lejárata miatt függőben |  CertificateExpiration. ExternalCert. warning |
|Külső tanúsítvány lejárata miatt függőben |  CertificateExpiration. ExternalCert. Critical |
|Az adott osztályú és méretű virtuális gépek az alacsony memóriakapacitás miatt nem építhetők ki |  AzureStack. ComputeController. VmCreationFailure. LowMemory |
|A csomópont nem érhető el a virtuális gép elhelyezéséhez |  AzureStack. ComputeController. HostUnresponsive | 
|Sikertelen biztonsági mentés  | AzureStack. BackupController. BackupFailedGeneralFault |    
|Az ütemezett biztonsági mentés a sikertelen műveletekkel való ütközés miatt kimaradt  | AzureStack. BackupController. BackupSkippedWithFailedOperationFault |   


## <a name="see-also"></a>Lásd még:

[A naplók és az ügyfelek adatkezelésének Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Közös hozzáférésű jogosultságkódok (SAS) használata](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Ajánlott eljárások az automatikus Azure Stack naplók gyűjtéséhez](azure-stack-best-practices-automatic-diagnostic-log-collection.md)





