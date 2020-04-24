---
title: A felhő AZONOSÍTÓjának megkeresése
description: A Felhőbeli azonosító megkeresése Azure Stack hub Súgó és támogatás szolgáltatásában.
author: justinha
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: e0045ae6bf76b6b4e5973f65c6c0a5f758e0d46b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520407"
---
# <a name="find-your-cloud-id"></a>A felhő AZONOSÍTÓjának megkeresése

Ez a témakör azt ismerteti, hogyan érheti el a Felhőbeli azonosítót a felügyeleti portál vagy a privilegizált végpont (PEP) használatával. 

## <a name="use-the-administrator-portal"></a>A felügyeleti portál használata

1. Nyissa meg a felügyeleti portált. 
1. Kattintson a **régió kezelése**elemre.

   ![Képernyőkép az irányítópultról](./media/azure-stack-automatic-log-collection/dashboard.png)

1. Kattintson a **Tulajdonságok** elemre, és másolja a **bélyeg Felhőbeli azonosítóját**.

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

* [Naplók interaktív küldése](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)
* [Naplók küldése most](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)






