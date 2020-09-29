---
title: A felhő AZONOSÍTÓjának megkeresése
description: A Felhőbeli azonosító megkeresése Azure Stack hub Súgó és támogatás szolgáltatásában.
author: justinha
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 647f731d1d82c41d26142ff88e56b8ec0717f5ef
ms.sourcegitcommit: dbc6739584aa407b26e4ad4921d967b7b608de38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2020
ms.locfileid: "90038811"
---
# <a name="find-your-cloud-id"></a>A felhő AZONOSÍTÓjának megkeresése

Ez a témakör azt ismerteti, hogyan kérheti le a Felhőbeli AZONOSÍTÓját a felügyeleti portál vagy a privilegizált végpont (PEP) használatával. A felhő-azonosító az egyedi azonosító, amely az adott skálázási egységből feltöltött, támogatási adatok követésére szolgál. Ha a rendszer a diagnosztikai naplókat feltöltötte a támogatási elemzésre, a felhő azonosítója, hogy a naplók hogyan vannak társítva a skálázási egységhez.

## <a name="use-the-administrator-portal"></a>A felügyeleti portál használata

1. Nyissa meg a felügyeleti portált. 
1. Válassza a **régió kezelése**lehetőséget.

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

## <a name="next-steps"></a>Következő lépések

* [Naplók interaktív küldése](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002)
* [Naplók küldése most](./azure-stack-configure-on-demand-diagnostic-log-collection-portal.md?view=azs-2002)
