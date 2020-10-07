---
title: Diagnosztikai naplók gyűjteménye Azure Stack hub-ban
description: Ismerje meg a diagnosztikai naplók gyűjtését Azure Stack hub Súgó + támogatás szolgáltatásában.
author: myoungerman
ms.topic: article
ms.date: 08/24/2020
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: 88f2b267f493c05fdcd8a5419718d08d5b6efe37
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778257"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Diagnosztikai naplók gyűjteménye Azure Stack hub-ban

Az Azure Stack hub mind a Windows-összetevők, mind a helyszíni Azure-szolgáltatások gyűjteménye, amelyek egymással együttműködnek. Ezek az összetevők és szolgáltatások saját naplókat hoznak létre. Mivel Microsoft ügyfélszolgálata ezeket a naplókat használja a problémák hatékony azonosításához és kijavításához, diagnosztikai naplókat is kínálunk. A diagnosztikai napló gyűjteménye segítségével gyorsan gyűjthet és oszthat meg diagnosztikai naplókat Microsoft ügyfélszolgálata egy egyszerű felhasználói felületen, amely nem igényel PowerShellt. A naplók gyűjtése akkor is történik, ha más infrastrukturális szolgáltatások nem állnak le.  

Javasoljuk, hogy ezt a módszert használja a naplók gyűjtésére, és csak akkor [használja a Kiemelt végpontot (PEP)](azure-stack-get-azurestacklog.md) , ha a felügyeleti portál vagy a Súgó és támogatás panel nem érhető el. 

>[!NOTE]
>Azure Stack hub-t regisztrálni kell a diagnosztikai naplók használatához. Ha Azure Stack hub nincs regisztrálva, használja [a Kiemelt végpontot (PEP)](azure-stack-get-azurestacklog.md) a naplók megosztásához. 

## <a name="collection-options-and-data-handling"></a>Gyűjtési lehetőségek és adatkezelés

::: moniker range=">= azs-2005"

Az Azure-hoz való kapcsolódástól függően Azure Stack hub megfelelő módon gyűjtheti be, mentheti és küldheti el a diagnosztikai naplókat a CSS-be. Ha Azure Stack hub tud csatlakozni az Azure-hoz, az ajánlott módszer az előjelzéses naplók engedélyezése, amely automatikusan felveszi a diagnosztikai naplókat egy Microsoft által vezérelt Storage-blobba az Azure- **ban, ha**kritikus riasztás válik szükségessé. Az igény szerinti naplókat a **naplók küldése**lehetőséggel is összegyűjtheti, vagy helyileg is mentheti a naplókat, ha Azure stack hub le van választva az Azure-ból. 

A következő szakaszokban ismertetjük az egyes beállításokat, és az egyes esetekben az adatkezelés módját. 

::: moniker-end

A diagnosztikai napló gyűjteménye szolgáltatás két módszert kínál a naplók küldésére:
* Naplók interaktív küldése
* Naplók küldése most

A naplókat helyileg is mentheti.

Ez azt mutatja, hogy a diagnosztikai naplók küldésének milyen lehetősége van az egyes esetekben. 

![A folyamatábra bemutatja, hogyan küldhet naplókat most a Microsoftnak](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

A következő szakaszokban ismertetjük az egyes beállításokat, és az egyes esetekben az adatkezelés módját. 

### <a name="send-logs-proactively"></a>Naplók interaktív küldése

A proaktív naplók gyűjtése a támogatási eset megnyitása előtt automatikusan összegyűjti és elküldi a Azure Stack hub diagnosztikai naplóit a Microsoftnak. Ezeket a naplókat csak akkor gyűjti a rendszer, ha egy [rendszerállapot-riasztást](#proactive-diagnostic-log-collection-alerts) emelnek fel, és csak Microsoft ügyfélszolgálata egy támogatási eset kontextusában érik el őket.

Az előjelzéses naplók gyűjtése letiltható, és bármikor újra engedélyezhető. Az alábbi lépéseket követve állíthatja be a proaktív naplózási gyűjteményt.

1. Jelentkezzen be az Azure Stack Hub felügyeleti portálra.
1. Nyissa meg a **Súgó + támogatás áttekintést**.
1. Ha megjelenik a szalagcím, válassza a **proaktív naplózási gyűjtemény engedélyezése**lehetőséget. Vagy válassza a **Beállítások** lehetőséget, és állítsa be az előjelzéses **naplók gyűjteményét** az **engedélyezéshez**, majd válassza a **Mentés**lehetőséget.

>[!NOTE]
>Ha a naplózási hely beállításai helyi fájlmegosztás használatára vannak konfigurálva, győződjön meg arról, hogy az életciklus-kezelési házirendek megakadályozzák, hogy a megosztási tárolók elérjék a méreteik kvótáját. Azure Stack hub nem figyeli a helyi fájlmegosztást, vagy nem kényszeríti ki az adatmegőrzési házirendeket.   

### <a name="send-logs-now"></a>Naplók küldése most

> [!TIP]
> Időt takaríthat meg a naplók elküldése helyett a [proaktív naplózási gyűjtemény](#send-logs-proactively) használatával.

A naplók elküldése lehetőséggel manuálisan gyűjthet és tölthet fel diagnosztikai naplókat Azure Stack hub-ból, általában a támogatási eset megnyitása előtt.

A diagnosztikai naplókat manuálisan is elküldheti Microsoft ügyfélszolgálata a felügyeleti portál vagy a PowerShell használatával. Ha Azure Stack hub csatlakozik az Azure-hoz, javasoljuk, hogy használja a felügyeleti portált, mert ez a legegyszerűbb módszer a naplók közvetlen elküldésére a Microsoftnak. Ha a portál nem érhető el, inkább a PowerShell használatával küldje el a naplókat.

Naplók elküldése most:

1. Nyissa meg a **Súgó + támogatás > naplózási gyűjteményt, > küldje el a naplókat most**. 
1. A naplók kezdési és befejezési időpontjának megadása. 
1. Válassza ki a helyi időzónát.
1. Válassza **a gyűjtés és feltöltés**lehetőséget.

Ha nem kapcsolódik az internethez, vagy csak helyileg szeretné menteni a naplókat, használja a [Get-AzureStackLog](azure-stack-get-azurestacklog.md) metódust a naplók elküldéséhez. 

::: moniker range=">= azs-2005"

### <a name="save-logs-locally"></a>Naplók helyi mentése

A naplókat a helyi kiszolgáló üzenetblokk (SMB) megosztásba mentheti, ha Azure Stack hub le van választva az Azure-ból. A **Settings (beállítások** ) panelen adja meg az elérési utat és egy felhasználónevet és jelszót, amely jogosult a megosztásba való írásra. A támogatási esetekben a Microsoft ügyfélszolgálata részletesen ismerteti az átvitt helyi naplók beolvasásának lépéseit. Ha a felügyeleti portál nem érhető el, a [Get-AzureStackLog](azure-stack-get-azurestacklog.md) használatával helyileg mentheti a naplókat.

![A diagnosztikai naplók gyűjtési lehetőségeinek képernyőképe](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

## <a name="bandwidth-considerations"></a>Sávszélességgel kapcsolatos megfontolások

A diagnosztikai napló-gyűjtemény átlagos mérete attól függően változik, hogy proaktív módon vagy manuálisan fut-e. A **proaktív naplók** átlagos mérete körülbelül 2 GB. A **küldési naplók** gyűjteményének mérete mostantól attól függ, hogy hány órát gyűjt a rendszer.

Az alábbi táblázat az Azure-hoz korlátozott vagy mért kapcsolattal rendelkező környezetekre vonatkozó szempontokat sorolja fel.

| Hálózati kapcsolat | Hatás |
|----|---|
| Alacsony sávszélességű/nagy késleltetésű kapcsolat | A napló feltöltése hosszabb időt vesz igénybe. |
| Megosztott kapcsolatok | A feltöltés hatással lehet más alkalmazásokra, illetve a hálózati kapcsolatokat megosztó felhasználókra is. |
| Mért kapcsolatok | A további hálózati használatért az INTERNETSZOLGÁLTATÓ felár ellenében vehető igénybe. |

## <a name="parameter-considerations"></a>Paraméterekkel kapcsolatos szempontok 

* A **FromDate** és a **ToDate** paraméterek egy adott időszakra vonatkozó naplók összegyűjtésére használhatók. Ha ezek a paraméterek nincsenek megadva, a rendszer alapértelmezés szerint a naplókat az elmúlt négy órára gyűjti.

* A naplók számítógép neve alapján történő szűréséhez használja a **FilterByNode** paramétert. Például:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByNode azs-xrp01
  ```

* A naplók típus szerinti szűréséhez használja a **FilterByLogType** paramétert. Dönthet úgy, hogy fájl, megosztás vagy WindowsEvent alapján végez szűrést. Például:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByLogType File
  ```

* A **FilterByResourceProvider** paraméter használatával diagnosztikai naplókat küldhet az érték-hozzáadási erőforrás-szolgáltatóhoz (RPs). Az általános szintaxis a következőket használja:
 
  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider <<value-add RP name>>
  ```
 
  Diagnosztikai naplók küldése IoT Hub számára: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider IotHub
  ```
 
  Diagnosztikai naplók küldése Event Hubs számára:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider eventhub
  ```
 
  Diagnosztikai naplók küldése az Azure Stack Edge számára:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvide databoxedge
  ```

* A **FilterByRole** paraméter használatával diagnosztikai naplókat küldhet a VirtualMachines és a BareMetal szerepkörökből:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal
  ```

* Ha diagnosztikai naplókat szeretne küldeni a VirtualMachines és a BareMetal szerepkörökből, a naplófájlok dátum szerinti szűrésével az elmúlt 8 órában:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* Ha diagnosztikai naplókat szeretne küldeni a VirtualMachines-és BareMetal-szerepkörökből, a naplófájlok dátum szerinti szűrésével a 8 órája és 2 órája között eltelt időszakban:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

Időt takaríthat meg az ügyfélszolgálattal, ha proaktív módon gyűjti a diagnosztikai naplókat, amikor Azure Stack hub riasztást kap.

Ha a rendszerállapot-feltételek kivizsgálására van szükség, a naplók automatikusan feltölthetők az elemzéshez, mielőtt a támogatási esetet megnyitják Microsoft ügyfélszolgálata.

>[!NOTE]
>Ha nem kapcsolódik az internethez, vagy csak helyileg szeretné menteni a naplókat, használja a [Get-AzureStackLog](azure-stack-get-azurestacklog.md) metódust a naplók elküldéséhez. 

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

## <a name="how-the-data-is-handled"></a>Az adatkezelés módja

Ha engedélyezi a **naplók interaktív küldését**, Ön elfogadja, hogy a Microsoft rendszeres időközönként automatikus naplózási gyűjteményeket fogad el a Azure stack hub rendszerállapot-riasztásai alapján. A naplók feltöltését és megőrzését egy, a Microsoft által kezelt és felügyelt Azure Storage-fiókban is elfogadja és megtartja.

A rendszer csak a rendszerállapot-riasztások hibaelhárítását fogja használni, és nem használja fel az Ön engedélye nélkül marketing-, reklámozási vagy egyéb kereskedelmi célokra. Az adatok akár 90 napig is megtekinthetők, a Microsoft által gyűjtött adatok pedig az [általános adatvédelmi gyakorlatnak](https://privacy.microsoft.com/)megfelelően lesznek kezelve.

Az engedély visszavonása a korábban a beleegyezéssel gyűjtött adatokat nem érinti.

## <a name="see-also"></a>Lásd még

[Azure Stack hub-napló és az ügyfelek adatkezelése](./azure-stack-data-collection.md)
