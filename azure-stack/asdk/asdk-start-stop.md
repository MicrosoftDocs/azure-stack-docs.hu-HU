---
title: A ASDK elindítása és leállítása | Microsoft Docs
description: Útmutató a Azure Stack Development Kit elindításához és leállításához (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 5232b1087414d6e7149157063a253b18a6b6e13a
ms.sourcegitcommit: 7968f9f0946138867323793be9966ee2ef99dcf4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70025813"
---
# <a name="start-and-stop-the-asdk"></a>A ASDK elindítása és leállítása
Nem ajánlott egyszerűen újraindítani a ASDK-gazdagépet. Ehelyett kövesse a cikkben ismertetett eljárásokat a ASDK-szolgáltatások megfelelő leállításához és újraindításához.

## <a name="stop-azure-stack"></a>Azure Stack leállítása 
Azure Stack szolgáltatások és a ASDK-gazdagép megfelelő leállításához használja a következő PowerShell-parancsokat:

1. Jelentkezzen be AzureStack\AzureStackAdmin-ként a ASDK-gazdaszámítógépen.
2. Nyissa meg a PowerShellt rendszergazdaként (nem PowerShell ISE).
3. A következő parancsok futtatásával hozzon létre egy kiemelt jogosultságú végponti (PEP) munkamenetet: 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Ezután a PEP-munkamenetben használja a **stop-AzureStack** parancsmagot Azure stack szolgáltatások leállításához és a ASDK-gazdaszámítógép leállításához:

   ```powershell
   Stop-AzureStack
   ```
5. Tekintse át a PowerShell kimenetét, és győződjön meg arról, hogy az összes Azure Stack szolgáltatás sikeresen le van állítva a ASDK-gazdaszámítógép leállítása előtt. A leállítási folyamat több percet is igénybe vehet.

## <a name="start-azure-stack"></a>Kezdés Azure Stack 
A ASDK szolgáltatásoknak automatikusan el kell indulniuk a gazdaszámítógép indításakor. Az ASDK infrastruktúra-szolgáltatások indítási ideje azonban a ASDK gazdagép számítógépének teljesítményén alapul. Bizonyos esetekben több óráig is eltarthat, amíg az összes szolgáltatás sikeresen újraindul.

Függetlenül attól, hogy a ASDK hogyan állt le, a következő lépésekkel ellenőrizheti, hogy az összes Azure Stack szolgáltatás elindult-e, és hogy a gazdagép bekapcsolása után teljesen működőképes-e: 

1. Kapcsolja be a ASDK gazdagép számítógépét. 
2. Jelentkezzen be AzureStack\AzureStackAdmin-ként a ASDK-gazdaszámítógépen.
3. Nyissa meg a PowerShellt rendszergazdaként (nem PowerShell ISE).
4. A következő parancsok futtatásával hozzon létre egy kiemelt jogosultságú végponti (PEP) munkamenetet:

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Ezután a PEP-munkamenetben futtassa a következő parancsokat a Azure Stack szolgáltatások indítási állapotának vizsgálatához:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Tekintse át a kimenetet, és győződjön meg arról, hogy Azure Stack szolgáltatások újraindítása sikeresen megtörtént.

Ha többet szeretne megtudni a Azure Stack szolgáltatások megfelelő leállításához és újraindításához szükséges ajánlott eljárásokról, olvassa el a [Azure stack elindítása és leállítása](../operator/azure-stack-start-and-stop.md)című témakört.

## <a name="troubleshoot-startup-and-shutdown"></a>Indítási és leállítási hibák 
Ezeket a lépéseket akkor hajtsa végre, ha Azure Stack szolgáltatások a ASDK gazdagép bekapcsolása után két órán belül nem indulnak el sikeresen:

1. Jelentkezzen be AzureStack\AzureStackAdmin-ként a ASDK-gazdaszámítógépen.
2. Nyissa meg a PowerShellt rendszergazdaként (nem PowerShell ISE).
3. A következő parancsok futtatásával hozzon létre egy kiemelt jogosultságú végponti (PEP) munkamenetet:

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Ezután a PEP-munkamenetben futtassa a következő parancsokat a Azure Stack szolgáltatások indítási állapotának vizsgálatához:

   ```powershell
   Test-AzureStack
   ```
5. Tekintse át a kimenetet, és javítsa ki az esetleges hibákat. További információ: [Azure stack érvényesítési tesztének futtatása](../operator/azure-stack-diagnostic-test.md).
6. Indítsa újra Azure Stack szolgáltatásokat a PEP-munkamenetből a **Start-AzureStack** parancsmag futtatásával:

   ```powershell
   Start-AzureStack
   ```

Ha a **Start-AzureStack** futtatása hibát eredményez, látogasson el a [Azure stack támogatási fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) , ahol lekérdezheti a ASDK hibaelhárítási támogatását. 

## <a name="next-steps"></a>További lépések 
További információ a Azure Stack diagnosztikai eszközről és a naplózási problémákról: [Azure stack diagnosztikai eszközök](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep).
