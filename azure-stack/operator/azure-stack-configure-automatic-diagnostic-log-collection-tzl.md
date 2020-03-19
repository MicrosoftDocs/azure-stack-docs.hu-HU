---
title: Diagnosztikai naplók proaktív gyűjtése, ha Azure Stack hub riasztást kap
description: Az előjelzéses naplózási gyűjtemény konfigurálása Azure Stack hub Súgó és támogatás szolgáltatásában.
author: justinha
ms.topic: article
ms.date: 02/12/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/12/2020
ms.openlocfilehash: 0c6bfe4dba2b3795e1069419d90624c8e3b55cda
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520264"
---
# <a name="send-azure-stack-hub-diagnostic-logs-proactively"></a>Azure Stack hub diagnosztikai naplói interaktív küldése

Az ügyfélszolgálattal időt takaríthat meg, ha proaktív módon összegyűjti a diagnosztikai naplókat, amikor Azure Stack hub riasztást kap.
Ha a rendszerállapot-feltételek kivizsgálására van szükség, a naplók automatikusan feltölthetők az elemzéshez, mielőtt a Microsoft terméktámogatási szolgálatával (CSS) megnyitják a támogatási esetet. 

## <a name="steps-to-configure-proactive-log-collection"></a>A proaktív naplózási gyűjtemény konfigurálásának lépései

A következő lépésekkel konfigurálhatja a proaktív naplózási gyűjteményt. Az automatikus naplózási gyűjtemény letiltható, és bármikor újra engedélyezhető.  

1. Jelentkezzen be az Azure Stack hub felügyeleti portálján.
1. Nyissa meg **a Súgó és támogatás áttekintést**.
1. Ha megjelenik a szalagcím, kattintson a **proaktív naplók engedélyezése**lehetőségre. 

   ![Képernyőkép: a naplók használatának engedélyezése a súgóban és támogatásban](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)


   Vagy kattintson a **Beállítások** lehetőségre, és állítsa be az előjelzéses **naplók gyűjteményét** az **engedélyezéshez** , majd kattintson a **Mentés**gombra.

   ![Képernyőkép: a naplók használatának engedélyezése a súgóban és támogatásban](media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png)


## <a name="view-log-collection"></a>Napló-gyűjtemény megtekintése

A Azure Stack hub-ból gyűjtött naplók előzményei a Súgó és támogatás **napló gyűjtemény** lapján jelennek meg a következő dátumokkal és időpontokkal:

- **Gyűjtés időpontja**: a naplózási művelet megkezdése után
- **Állapot**: vagy folyamatban vagy kész
- **Naplók kezdete**: a begyűjteni kívánt időszak kezdete
- **Naplók vége**: az időtartam vége
- **Típus**: Ha manuális vagy proaktív naplózási gyűjtemény 


![Képernyőfelvétel a naplókról](media/azure-stack-help-and-support/azure-stack-log-collection.png)


## <a name="proactive-diagnostic-log-collection-alerts"></a>Proaktív diagnosztikai naplók gyűjtésével kapcsolatos riasztások 

Ha engedélyezve van, a proaktív naplók gyűjteménye csak akkor tölti fel a naplókat, ha az alábbi események egyike következik be. 

A **frissítés sikertelen volt** például egy olyan riasztás, amely előidézi a proaktív diagnosztikai naplók gyűjtését. Ha engedélyezve van, a rendszer proaktív módon rögzíti a diagnosztikai naplókat a frissítés során, így segít a CSS-sel kapcsolatos problémák elhárításában. A rendszer csak akkor gyűjti a diagnosztikai naplókat, ha a frissítésre vonatkozó riasztást **nem sikerült** megemelni. 

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


## <a name="see-also"></a>Lásd még

[Azure Stack hub-napló és az ügyfelek adatkezelése](azure-stack-data-collection.md)





