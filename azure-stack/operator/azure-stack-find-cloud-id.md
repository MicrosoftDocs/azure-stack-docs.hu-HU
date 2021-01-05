---
title: A felhő AZONOSÍTÓjának megkeresése
description: A Felhőbeli azonosító megkeresése Azure Stack hub Súgó és támogatás szolgáltatásában.
author: PatAltimore
ms.topic: article
ms.date: 10/08/2019
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 5483aa6859e839b978da7449d124811d3e2fb0bb
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870579"
---
# <a name="find-your-cloud-id"></a>A felhő AZONOSÍTÓjának megkeresése

Ez a témakör azt ismerteti, hogyan kérheti le a Felhőbeli AZONOSÍTÓját a felügyeleti portál vagy a privilegizált végpont (PEP) használatával. A felhő-azonosító az egyedi azonosító, amely az adott skálázási egységből feltöltött, támogatási adatok követésére szolgál. Ha a rendszer a diagnosztikai naplókat feltöltötte a támogatási elemzésre, a felhő azonosítója, hogy a naplók hogyan vannak társítva a skálázási egységhez.

## <a name="use-the-administrator-portal"></a>A felügyeleti portál használata

1. Nyissa meg a felügyeleti portált. 
1. Válassza a **régió kezelése** lehetőséget.

   ![Képernyőkép az irányítópultról](./media/azure-stack-automatic-log-collection/dashboard.png)

1. Válassza a **Tulajdonságok** lehetőséget, és másolja a **bélyeg Felhőbeli azonosítóját**.

   ![Képernyőkép a régió tulajdonságairól a Stamp Cloud ID azonosítóval](media/azure-stack-automatic-log-collection/region-properties-blade-with-stamp-cloud-id.png)


## <a name="use-the-privileged-endpoint"></a>Az emelt szintű végpont használata

1. Nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, és futtassa az alábbi szkriptet. Cserélje le a PEP virtuális gép és a Felhőbeli rendszergazdai hitelesítő adatok IP-címét a környezetéhez szükséges módon. 

   ```powershell
   $ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

   $password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
   $cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)
   $session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

   $stampInfo = Invoke-Command -Session $session { Get-AzureStackStampInformation }
   if ($session) {
       Remove-PSSession -Session $session
   }

   $stampInfo.CloudID
   ```

## <a name="next-steps"></a>További lépések

* [Naplók interaktív küldése](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively)
* [Naplók küldése most](./azure-stack-diagnostic-log-collection-overview.md#send-logs-now)
