---
title: A HCI-adatgyűjtés Azure Stack
description: Ez a témakör a Azure Stack HCI által gyűjtött diagnosztikai adatokra vonatkozó tervezési és szabályzatokat ismerteti.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: 91f578bcb0dc3a9d2debd086e6ed95cb591de90d
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97053083"
---
# <a name="azure-stack-hci-data-collection"></a>A HCI-adatgyűjtés Azure Stack

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a témakör azokat a szükséges adatokat ismerteti, amelyeket a rendszer a Azure Stack HCI biztonságos, naprakész és az általános elérhetőségének (GA) a várt módon való működésének érdekében gyűjtött a 2020 decemberében.

Az alábbiakban ismertetett információk szükségesek ahhoz, hogy a Microsoft Azure Stack HCI-t szolgáltasson. Ezeket az adatokat naponta egyszer gyűjtik, és az adatgyűjtési eseményeket az eseménynaplókban lehet megtekinteni. Azure Stack HCI a fürtök naprakész, biztonságos és megfelelő működésének biztosításához szükséges minimális adatokat gyűjti.

   > [!IMPORTANT]
   > A Azure Stack HCI által összegyűjtött adatok függetlenek a Windows diagnosztikai adatoktól, amelyek különböző szintű gyűjteményekhez konfigurálhatók. Azure Stack HCI-ben a Windows diagnosztikai adatgyűjtés alapértelmezett beállítása a biztonság (kikapcsolva), ami azt jelenti, hogy a rendszer nem küld Windows diagnosztikai adatszolgáltatást, kivéve, ha a rendszergazda módosítja a diagnosztikai adatbeállításokat. További információ: [Windows diagnosztikai adatok konfigurálása a szervezetben](/windows/privacy/configure-windows-diagnostic-data-in-your-organization). A Microsoft a Azure Stack HCI-vel kapcsolatban gyűjtött bármely Windows diagnosztikai adatok független vezérlője. A Microsoft a [Microsoft adatvédelmi nyilatkozatának](https://privacy.microsoft.com/privacystatement)megfelelően kezeli a Windows diagnosztikai adatokat.

## <a name="data-collection-and-residency"></a>Adatgyűjtés és tartózkodási hely

Ez a Azure Stack HCI-adathalmaz: 

- a rendszer nem továbbítja a Microsoftnak, amíg a termék be nem jelentkezett az Azure-ba. Ha Azure Stack HCI regisztrációja megszűnik, ez az adatgyűjtés leáll.
- a rendszer naplózza a Microsoft-AzureStack-HCI/analitikai esemény csatornára. 
- JSON formátumban van, így a rendszergazdák megvizsgálhatják és elemezhetik az elküldött adatvesztést.
- a Egyesült Államok egy biztonságos, Microsoft által üzemeltetett adatközpontban tárolódik.

## <a name="data-retention"></a>Adatmegőrzés

Miután Azure Stack HCI gyűjti ezeket az adatokat, 90 napig megőrzi őket. Az összesített, de azonosított adatokat továbbra is megtarthatják.

## <a name="what-data-is-collected"></a>Milyen adatokat gyűjt?

Azure Stack HCI a következőket gyűjti:

- A kiszolgálókkal kapcsolatos információk, például az operációs rendszer verziószáma, a processzor típusa, a processzorok száma, a memória mérete, a fürt azonosítója és a hardver azonosító kivonata
- A telepített Azure Stack HCI-kiszolgálói funkciók (például a BitLocker) listája
- Az Azure Stack HCI operációs rendszer megbízhatóságának kiszámításához szükséges információk
- Az állapotadatok adatainak megbízhatóságának kiszámításához szükséges információk
- Adott hibák esetén az eseménynaplóból összegyűjtött információk, például a frissítés letöltése nem sikerült
- A számítási tár megbízhatóságával kapcsolatos információk
- Információk a fizikai lemez megbízhatóságának kiszámításához
- Információk a mennyiségi titkosítás megbízhatóságának kiszámításához
- Információk a tárolóhelyek megbízhatóságának és teljesítményének kijavításához
- Az Azure Stack HCI operációs rendszer biztonságának ellenőrzésére szolgáló információk
- Információk az Azure Stack HCI operációs rendszer víruskereső/kártevő-állapotának kiszámításához
- A hálózatkezelési összetevők megbízhatóságának korrelációs leírása
- A hálózati teljesítmény összekapcsolására szolgáló információk
- A frissítések és a telepítések megbízhatóságának korrelációs leírása
- A Hyper-V megbízhatóságának mérésére szolgáló információk
- A fürtszolgáltatási összetevők megbízhatóságának mérésére/összekapcsolására szolgáló információk
- A CAU-funkció sikerességének nyomon követésére szolgáló információk
- A vész-helyreállítási funkció megbízhatóságának mérésére/összekapcsolására szolgáló információk
- Az Azure Stack HCI-kiszolgálókra alkalmazott SMB sávszélesség-korlátok leírására szolgáló információk

## <a name="view-this-data"></a>Az adatmegjelenítés

1. Engedélyezze az elemzési naplót a következő PowerShell-parancs használatával:

   ```PowerShell
   wevtutil sl Microsoft-AzureStack-HCI/Analytic /e:True
   ```

2. Tekintse meg a naplót az összegyűjtött adatok megtekintéséhez:

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest
   ```

3. Az exportáláshoz használt adatformátum:

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest `
   | Where-Object Id -eq 802 `
   | ForEach-Object { 
       [pscustomobject] @{
           TimeCreated = $_.TimeCreated 
           EventName=$_.Properties[0].Value 
           Value=$_.Properties[1].Value 
       } 
   }
   ```
 
Ezután a következőhöz hasonló eredményt kell kapnia:

```shell
TimeCreated            EventName                                                  Value
-----------            ---------                                                  -----
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.Core                   {"OEMName":"Microsoft Corporation"...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ProductFeatures        {"InstalledFeatures":["Server-Core...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.OSReliability          {"DailyDirtyRestarts":0,"WeeklyDir...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.DiagnosticHealth       {"DailySuccessfulDiagnosticUploads...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ErrorSummary           {"ErrorSummary":[{"EventName":"Win...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.VolumeSummary          {"VolumeCount":2,"HealthyVolumeCou...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.DiskSummary            {"DiskCount":33,"Summary":[]}
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.BitlockerVolumeSummary {"BitlockerVolumeCount":0,"Summary...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageErrors          {"ErrorSummary":[{"EventName":"Sto...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageRepairSummary   {"DailyRepairStartCount":0,"Weekly...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.TrustedPlatformModule  {"Manufacturer":"MSFT","Manufactur...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.MicrosoftDefender      {"AMEngineVersion":"1.1.17600.5","...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkInfo            {"NetworkDirect":true,"NetworkDire...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkAdapterSummary  {"NetworkAdapterGroup":[{"DriverNa...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.OSDeploy               {"OSInstallType":0}
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.ClusterProperties      {"Id":"fd2fc061-b924-4d61-a45b-3b3...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.DisasterRecovery       {"IsDisasterRecoveryEnabled":false...
```
