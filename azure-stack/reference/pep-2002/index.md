---
title: Azure Stack hub privilegizált végpontjának referenciája
description: Hivatkozás a PowerShell Azure Stack Kiemelt végpontja számára
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 76eb340da04e9254bcf8d8a626822c65362f44d2
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486036"
---
# <a name="azure-stack-hub-privileged-endpoint-reference"></a>Azure Stack hub privilegizált végpontjának referenciája

A PowerShell Azure Stack rendszerjogosultságú végpont (PEP) egy előre konfigurált távoli PowerShell-konzol, amely elegendő képességgel rendelkezik a szükséges feladatok elvégzéséhez. A végpont PowerShell-JEA (elég felügyelet) használ, hogy csak korlátozott számú parancsmagot tegyen elérhetővé.

## <a name="privilege-endpoint-cmdlets"></a>Jogosultsági végponti parancsmagok

| Parancsmag | Leírás |
| --- | --- |
| [Bezárás – PrivilegedEndpoint](Close-PrivilegedEndpoint.md) | Nincs leírás. |
| [Get-ActionStatus](Get-ActionStatus.md) | A művelethez tartozó legújabb művelet állapotának beolvasása a megadott függvény nevével. |
| [Get-AzureStackLog](Get-AzureStackLog.md) | Naplók beolvasása a AzureStack különböző szerepköreiről időtúllépéssel. |
| [Get-AzureStackStampInformation](Get-AzureStackStampInformation.md) | A bélyegző információinak beolvasása. |
| [Get-AzureStackSupportConfiguration](Get-AzureStackSupportConfiguration.md) | A támogatási szolgáltatás konfigurációs beállításainak beolvasása. |
| [Get-CloudAdminPasswordRecoveryToken](Get-CloudAdminPasswordRecoveryToken.md) | Nincs leírás. |
| [Get-CloudAdminUserList](Get-CloudAdminUserList.md) | Nincs leírás. |
| [Get-ClusterLog](Get-ClusterLog.md) | Nincs leírás. |
| [Get-GraphApplication](Get-GraphApplication.md) | A Get-GraphApplication egy burkoló függvény, amely beolvassa a Graph alkalmazás információit a megadott alkalmazás nevéhez vagy azonosítóhoz. |
| [Get-StorageJob](Get-StorageJob.md) | Nincs leírás. |
| [Get-SupportSessionInfo](Get-SupportSessionInfo.md) | Nincs leírás. |
| [Get-SupportSessionToken](Get-SupportSessionToken.md) | Nincs leírás. |
| [Get-SyslogClient](Get-SyslogClient.md) | A syslog-ügyfél beállításainak beolvasása. |
| [Get-SyslogServer](Get-SyslogServer.md) | A syslog-kiszolgáló végpontjának beolvasása. |
| [Get-ThirdPartyNotices](Get-ThirdPartyNotices.md) | Nincs leírás. |
| [Get-TLSPolicy](Get-TLSPolicy.md) | Nincs leírás. |
| [Get-VirtualDisk](Get-VirtualDisk.md) | Nincs leírás. |
| [Meghívás – AzureStackOnDemandLog](Invoke-AzureStackOnDemandLog.md) | Igény szerinti naplók generálása AzureStack-szerepkörökből, ha vannak ilyenek. |
| [Új – AzureBridgeServicePrincipal](New-AzureBridgeServicePrincipal.md) | Új servcie hoz létre Azure Active Directoryban. |
| [Új – AzureStackActivation](New-AzureStackActivation.md) | Azure Stack aktiválása. |
| [Új – CloudAdminUser](New-CloudAdminUser.md) | Nincs leírás. |
| [Új – GraphApplication](New-GraphApplication.md) | A New-GraphApplication egy burkoló függvény, amely az ADFS Graph-parancsmagokat AD FSon hívja meg. |
| [Új – RegistrationToken](New-RegistrationToken.md) | Új regisztrációs jogkivonat létrehozása |
| [Regisztráció – CustomAdfs](Register-CustomAdfs.md) | Parancsfájl az egyéni Active Directory összevonási szolgáltatás (ADFS) jogcím-szolgáltatóként való regisztrálásához Azure Stack AD FS. |
| [Regisztráció – CustomDnsServer](Register-CustomDnsServer.md) | Parancsfájl Azure Stack DNS-sel rendelkező egyéni DNS-kiszolgálók regisztrálásához. |
| [Regisztráció – DirectoryService](Register-DirectoryService.md) | Parancsfájl az ügyfél Active Directory a Graph szolgáltatásban való regisztrálásához. |
| [Remove-AzureStackActivation](Remove-AzureStackActivation.md) | Nincs leírás. |
| [Remove-CloudAdminUser](Remove-CloudAdminUser.md) | Nincs leírás. |
| [Remove-GraphApplication](Remove-GraphApplication.md) | A Remove-GraphApplication egy burkoló függvény, amely az ADFS Graph-parancsmagokat AD FSon hívja meg. |
| [Javítás – VirtualDisk](Repair-VirtualDisk.md) | Nincs leírás. |
| [Alaphelyzetbe állítás – DatacenterIntegrationConfiguration](Reset-DatacenterIntegrationConfiguration.md) | Az adatközpont-integráció változásainak visszaállítására szolgáló parancsfájl. |
| [Küldés – AzureStackDiagnosticLog](Send-AzureStackDiagnosticLog.md) | Azure Stack diagnosztikai naplókat küld a Microsoftnak. |
| [Set-CloudAdminUserPassword](Set-CloudAdminUserPassword.md) | Nincs leírás. |
| [Set-GraphApplication](Set-GraphApplication.md) | A set-GraphApplication egy burkoló függvény, amely az ADFS Graph-parancsmagokat AD FSon hívja meg. |
| [Set-ServiceAdminOwner](Set-ServiceAdminOwner.md) | A szolgáltatás-rendszergazda frissítésére szolgáló parancsfájl. |
| [Set-SyslogClient](Set-SyslogClient.md) | A syslog-ügyfél végpont-tanúsítványának importálása és alkalmazása. |
| [Set-SyslogServer](Set-SyslogServer.md) | Beállítja a syslog-kiszolgáló végpontját. |
| [Set-telemetria](Set-Telemetry.md) | Engedélyezheti vagy letilthatja a telemetria-adatok átvitelét a Microsoftnak. |
| [Set-TLSPolicy](Set-TLSPolicy.md) | Nincs leírás. |
| [Start – AzureStack](Start-AzureStack.md) | Elindítja az összes Azure Stack-szolgáltatást. |
| [Start – SecretRotation](Start-SecretRotation.md) | Elindít egy bélyegzőn a titkos elforgatást. |
| [Leállítás – AzureStack](Stop-AzureStack.md) | Az összes Azure Stack szolgáltatás leállítása. |
| [Test-AzureStack](Test-AzureStack.md) | Azure Stack állapotának ellenőrzése. |
| [Zárolás feloldása – SupportSession](Unlock-SupportSession.md) | Nincs leírás. |

## <a name="next-steps"></a>Következő lépések

További információ: Azure Stack hub Kiemelt jogosultságokkal rendelkező végpontja, [Azure stack-ban található privilegizált végpont használata](../../operator/azure-stack-privileged-endpoint.md).
