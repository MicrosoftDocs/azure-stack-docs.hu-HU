---
title: Azure Stack hub diagnosztikai naplóinak küldése az Azure-ba a privilegizált végpont (PEP) használatával
description: Megtudhatja, hogyan küldhet Azure Stack hub diagnosztikai naplókat az Azure-ba az emelt szintű végpont (PEP) használatával.
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: ca6240cb4f1e54c5e5fbfda79c46697909612063
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "79520297"
---
# <a name="send-azure-stack-hub-diagnostic-logs-to-azure-using-the-privileged-endpoint-pep"></a>Azure Stack hub diagnosztikai naplóinak küldése az Azure-ba a privilegizált végpont (PEP) használatával

Ennek a módszernek a használata esetén nem kell megadnia a cél tárolási helyét. Ha a **fromDate** és a **toDate** paraméterek nincsenek megadva, a rendszer alapértelmezés szerint az utolsó 4 órát adja meg a végrehajtás időpontjában. 

Megadhatja a **FilterByRole** vagy a **FilterByResourceProvider** paramétert is, ha csak bizonyos szerepkört vagy értéket szeretne megadni az RP-naplók hozzáadásához. 

Az alábbi példa egy parancsfájlt futtat, amely a PEP használatával gyűjti össze a naplókat egy integrált rendszeren. 


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP address. 
 
$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force 
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password) 
 
$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred 
 
$fromDate = (Get-Date).AddHours(-8) # Optional. 
$toDate = (Get-Date).AddHours(-2) # Optional. Provide the time that includes the period for your issue 
 
Invoke-Command -Session $session {Send-AzureStackDiagnosticLog -FromDate $using:fromDate -ToDate $using:toDate} 
 
if ($session) { 
    Remove-PSSession -Session $session 
} 
```

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


## <a name="next-steps"></a>További lépések

[A rendszerjogosultságú végpont (PEP) használata Azure Stack hub diagnosztikai naplói küldéséhez](azure-stack-get-azurestacklog.md)
