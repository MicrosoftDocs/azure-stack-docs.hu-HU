---
title: Elindíthatja és leállíthatja a az Azure Stack Development Kit (ASDK) |} A Microsoft Docs
description: Ismerje meg, hogyan indítsa el, és állítsa le az Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/14/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: a18584086f74788710d31777939e99d35748344c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64290065"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Elindíthatja és leállíthatja a az Azure Stack Development Kit (ASDK)
Egyszerűen indítsa újra a ASDK számítógép nem ajánlott. Ehelyett kövesse megfelelően állítsa le és indítsa újra a ASDK szolgáltatások ebben a cikkben szereplő eljárásokat. 

## <a name="stop-azure-stack"></a>Állítsa le az Azure Stack 
Megfelelően állítsa le az Azure Stack-szolgáltatásokat, és a ASDK gazdaszámítógépet, használja a következő PowerShell-parancsokat:

1. Jelentkezzen AzureStack\AzureStackAdmin ASDK a gazdagépen.
2. Nyissa meg a Powershellt rendszergazdaként (nem PowerShell ISE-ben).
3. A következő parancsokat egy emelt szintű végpont (EGP)-munkamenetet létrehozni: 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Ezután a EGP-munkamenetben használja a **Stop-AzureStack** parancsmag használatával állítsa le az Azure Stack-szolgáltatásokat, és állítsa le a ASDK számítógép:

   ```powershell
   Stop-AzureStack
   ```
5. Tekintse át a PowerShell-kimenetet, annak érdekében, hogy az összes Azure Stack-szolgáltatások rendszer leállítása sikeresen megtörtént a ASDK számítógép leállítása előtt. A leállítási folyamat több percig tart.

## <a name="start-azure-stack"></a>Indítsa el az Azure Stack 
ASDK szolgáltatások a gazdaszámítógép elkezdésének automatikusan elindul. Azonban ASDK infrastruktúra-szolgáltatások indítási idejének a ASDK fogadó számítógép hardverkonfigurációja teljesítményét alapján változik. Szolgáltatások újraindítása sikertelen bizonyos esetekben több órát is igénybe vehet.

Függetlenül attól, hogy a ASDK le lett állítva és ellenőrizze, hogy az összes Azure Stack-szolgáltatás elindult, és teljesen működőképes után a számítógép be van-e kapcsolva a következő lépéseket kell használnia: 

1. Energiagazdálkodási ASDK a gazdagépen. 
2. Jelentkezzen AzureStack\AzureStackAdmin ASDK a gazdagépen.
3. Nyissa meg a Powershellt rendszergazdaként (nem PowerShell ISE-ben).
4. A következő parancsokat egy emelt szintű végpont (EGP)-munkamenetet létrehozni:

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Ezután a EGP-munkamenetben futtassa a következő parancsokat az Azure Stack-szolgáltatások indítási állapotának ellenőrzéséhez:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Tekintse át a kimenetet, annak érdekében, hogy az Azure Stack-szolgáltatások újraindítása sikeresen megtörtént.

Megfelelően állítsa le és indítsa újra az Azure Stack-szolgáltatások az ajánlott eljárásokkal kapcsolatos további tudnivalókért lásd: [indítás és leállítás Azure Stack](../operator/azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Indítási és leállítási hibaelhárítása 
Hajtsa végre ezeket a lépéseket, ha az Azure Stack-szolgáltatások nem sikerült indul el a ASDK gazdaszámítógépen power két órán belül:

1. Jelentkezzen AzureStack\AzureStackAdmin ASDK a gazdagépen.
2. Nyissa meg a Powershellt rendszergazdaként (nem PowerShell ISE-ben).
3. A következő parancsokat egy emelt szintű végpont (EGP)-munkamenetet létrehozni:

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Ezután a EGP-munkamenetben futtassa a következő parancsokat az Azure Stack-szolgáltatások indítási állapotának ellenőrzéséhez:

   ```powershell
   Test-AzureStack
   ```
5. Tekintse át a kimenetet, és javítsa ki a hibákat. További információkért lásd: [futtatása az Azure Stack teszt](../operator/azure-stack-diagnostic-test.md).
6. Az Azure Stack-szolgáltatások EGP-munkameneten belül futtatásával indítsa újra a **Start-AzureStack** parancsmagot:

   ```powershell
   Start-AzureStack
   ```

Ha fut **Start-AzureStack** , hibát okoz, látogasson el a [Azure Stack támogatási fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) hibaelhárítási támogatás ASDK beolvasni. 

## <a name="next-steps"></a>További lépések 
További információ az Azure Stack diagnosztikai eszköz, és adja ki a naplózást, lásd: [Azure Stack diagnosztikai eszközök](../operator/azure-stack-diagnostics.md).
