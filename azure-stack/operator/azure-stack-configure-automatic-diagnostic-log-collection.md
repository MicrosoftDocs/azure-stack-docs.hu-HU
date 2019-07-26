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
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 428ae61f2de684d84fd39d7e21b0bd1b47394edf
ms.sourcegitcommit: 82d09bbae3e5398d2fce7e2f998dfebff018716c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68497916"
---
# <a name="configure-automatic-azure-stack-diagnostic-log-collection"></a>A diagnosztikai naplók automatikus Azure Stackának konfigurálása

*Vonatkozik: Integrált rendszerek Azure Stack*

Javasoljuk, hogy az automatikus diagnosztikai napló gyűjtési funkciójának konfigurálásával egyszerűsítse a naplók gyűjtését és az ügyfél-támogatási élményt. Ha a rendszerállapot-feltételek kivizsgálására van szükség, a rendszer automatikusan feltöltheti a naplókat a Microsoft Customer Support Services (CSS) által végzett elemzéshez. 

## <a name="create-an-azure-blob-container-sas-url"></a>Azure Blob Container SAS URL-cím létrehozása 

Az automatikus naplók konfigurálása előtt meg kell kapnia egy közös hozzáférési aláírást (SAS) a blob-tárolóhoz. Az SAS lehetővé teszi, hogy a fiók kulcsainak megosztása nélkül hozzáférést biztosítson a Storage-fiók erőforrásaihoz. Azure Stack naplófájlokat a blob-tárolóba mentheti az Azure-ban, majd megadhatja a SAS URL-címét, ahol a CSS képes gyűjteni a naplókat. 

### <a name="prerequisites"></a>Előfeltételek

Új vagy meglévő BLOB-tárolót használhat az Azure-ban. BLOB-tárolónak az Azure-ban való létrehozásához legalább a [Storage blob közreműködő szerepkörre](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) vagy a [megadott engedélyre](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)van szükség. A globális rendszergazdák is rendelkeznek a szükséges engedélyekkel. 

Az automatikus naplózási tárolási fiók paramétereinek kiválasztásával kapcsolatos ajánlott eljárásokért tekintse meg az [ajánlott eljárások az automatikus Azure stack a naplók gyűjtéséhez](azure-stack-best-practices-automatic-diagnostic-log-collection.md)című témakört. További információ a Storage-fiókok típusairól: az [Azure Storage-fiók áttekintése](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

### <a name="create-a-blob-storage-account"></a>BLOB Storage-fiók létrehozása
 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Kattintson a **Storage-fiókok** > **Hozzáadás**elemre. 
1. Hozzon létre egy BLOB-tárolót a következő beállításokkal:
   - **Előfizetés**: Válassza ki az Azure-előfizetését.
   - **Erőforráscsoport**: Válasszon erőforráscsoportot.
   - **Storage-fiók neve**: Adjon meg egyedi nevet a Storage-fiók számára.
   - **Hely**: Válasszon egy adatközpontot a vállalati házirendnek megfelelően.
   - **Teljesítmény**: Válassza a standard lehetőséget.
   - **Fiók típusa** Válassza a StorageV2 (általános célú v2) lehetőséget. 
   - **Replikáció**: Helyileg redundáns tárolás (LRS) kiválasztása
   - **Hozzáférési szintek**: Jó választás

   ![A blob-tároló tulajdonságait ábrázoló képernyőkép](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Kattintson a **felülvizsgálat + létrehozás** , majd a **Létrehozás**elemre.  

### <a name="create-a-blob-container"></a>Blobtároló létrehozása 

1. Miután az üzembe helyezés sikeres volt, kattintson az **erőforrás keresése**elemre. A Storage-fiókot az irányítópulton is rögzítheti a könnyű hozzáférés érdekében. 
1. Kattintson a **Storage Explorer (előnézet)** gombra, kattintson a jobb gombbal a **blob**-tárolók elemre, majd kattintson a **blob-tároló létrehozása**elemre 
1. Adja meg az új tároló nevét, majd kattintson **az OK**gombra.

## <a name="create-a-sas-url"></a>SAS URL-cím létrehozása

1. Kattintson a jobb gombbal a tárolóra, majd kattintson a **megosztott hozzáférés aláírásának**beolvasása elemre. 
   
   ![A blob-tároló megosztott hozzáférési aláírásának beszerzését bemutató képernyőkép](media/azure-stack-automatic-log-collection/get-sas.png)

1. Válassza ki a következő tulajdonságokat:
   - Kezdő időpont: Igény szerint áthelyezheti a kezdési időpontot 
   - Lejárati idő: Két év
   - Időzóna: UTC
   - Engedélyek: Olvasás, írás és Listázás

   ![A megosztott hozzáférési aláírás tulajdonságait bemutató képernyőfelvétel](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Kattintson a **Create** (Létrehozás) gombra.  

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

- **Gyűjtés időpontja**: A napló-gyűjtési művelet megkezdése után 
- **Kezdő dátum**: Annak az időszaknak a kezdete, amelynek a gyűjtését el szeretné indítani
- **A mai napig**: Az időtartam vége

![Képernyőfelvétel a naplókról](media/azure-stack-automatic-log-collection/azure-stack-log-collection.png)

Ha a diagnosztikai napló gyűjtése sikertelen, ellenőrizze, hogy érvényes-e az SAS URL-címe. Ha a hiba továbbra is fennáll, vagy több hiba jelenik meg, kérjen segítséget a Microsoft CSS-ben. 

Az operátorok is megnézhetik a Storage-fiókot az automatikusan összegyűjtött naplókhoz. Ez a képernyőkép például a naplózási gyűjteményeket jeleníti meg a Azure Portal Storage Explorer előzetes verziójának használatával:

![Képernyőfelvétel a naplókról](media/azure-stack-automatic-log-collection/check-storage-account.png)







