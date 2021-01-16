---
title: A felhő AZONOSÍTÓjának megkeresése
description: A Felhőbeli azonosító megkeresése Azure Stack hub Súgó és támogatás szolgáltatásában.
author: PatAltimore
ms.topic: article
ms.date: 10/08/2019
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 6da70761e8a37967d3debdff28b67447596c632a
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255962"
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

* [Naplók interaktív küldése](./diagnostic-log-collection.md#send-logs-proactively)
* [Naplók küldése most](./diagnostic-log-collection.md#send-logs-now)