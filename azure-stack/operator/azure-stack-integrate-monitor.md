---
title: Külső figyelő megoldás integrálása az Azure Stackkel való használathoz |} A Microsoft Docs
description: Ismerje meg, hogyan integrálhatja az Azure Stacket külső figyelési megoldással az adatközpontban.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/06/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 2871b5183833830368307c5d2b5152e3909fd3ea
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618833"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Külső figyelő megoldás integrálása az Azure Stack használatával

Külső figyelés az Azure Stack-infrastruktúra figyelése az Azure Stack szoftver, a fizikai számítógépek és a fizikai hálózati kapcsolók kell. Állapot és riasztási adatokat beolvasni metódus következő területeken nyújt:

- Az Azure Stack szoftver kínál egy REST-alapú API-t állapot és riasztások beolvasása. Szoftver-meghatározott technológiák, például a közvetlen tárolóhelyek, tárolási állapot és riasztások használata szoftver figyelési részét képezik.
- Fizikai számítógépek is elérhetővé állapot és riasztási adatokat az alaplapi felügyeleti vezérlővel (bmc) keresztül.
- Fizikai hálózati eszközöket is elérhetővé állapot és riasztási adatokat az SNMP protokollon keresztül.

Minden egyes Azure Stack megoldás hardver életciklus állomással letöltésként érhető el. Ezen a gazdagépen fut, a számítógépgyártó (OEM) hardver gyártója által biztosított felügyeleti szoftver a fizikai kiszolgálók és a hálózati eszközök. Ha azok figyelési megoldásokkal integrálható a meglévő figyelési megoldások az adatközpontban található Ellenőrizze a OEM-szolgáltatónál.

> [!IMPORTANT]
> A külső figyelési megoldást használ az ügynök nélküli kivételfigyelés kell lennie. Külső ügynökök belül az Azure Stack-összetevők nem telepíthető.

Az alábbi ábrán látható, az Azure Stackkel integrált rendszerek, a hardver életciklus-gazdagép, egy külső figyelési megoldás és egy külső jegykiadás/adatgyűjtési rendszer közötti adatforgalmat.

![Azure Stack, figyelés, és hibajegy-kezelési megoldás közötti forgalom bemutató diagram.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> Külső figyelési integráció közvetlenül a fizikai kiszolgálók nem engedélyezett, aktívan blokkolja a hozzáférés-vezérlési listák (ACL).  Külső figyelési integrációs közvetlenül a fizikai hálózati eszközöket támogatott, ellenőrizze a Ez a funkció engedélyezése az OEM-szolgáltatónál.

Ez a cikk bemutatja, hogyan integrálható az Azure Stack külső figyelési megoldásokkal, például a System Center Operations Manager és a Nagios. Hogyan használható a riasztásokat programozott módon PowerShell vagy REST API-hívások is tartalmaz.

## <a name="integrate-with-operations-manager"></a>Az Operations Manager integrálása

Az Operations Manager Azure Stack külső figyelésére használható. A System Center Management Pack for Microsoft Azure Stack segítségével több Azure Stack üzemelő példányokat, az Operations Manager egyetlen példánnyal. A felügyeleti csomag az erőforrás-szolgáltató állapota és a frissítés erőforrás-szolgáltató REST API-k segítségével kommunikál az Azure Stack. Ha azt tervezi, a hardver életciklus gazdagépen futó szoftverek monitorozását az OEM megkerülése, gyártói felügyeleti csomagok fizikai kiszolgálók figyelése is telepítheti. Az Operations Manager hálózati eszközök felderítési használatával figyelheti a hálózati kapcsolókat.

A felügyeleti csomag az Azure Stackhez az alábbi képességeket biztosítja:

- Kezelheti a több Azure Stack-telepítés
- A program támogatja az Azure Active Directory (Azure AD) és az Active Directory összevonási szolgáltatások (AD FS)
- Beolvashatja és riasztások bezárása
- Van egy állapotát és a egy kapacitás-irányítópult
- Ha javítás- és (P & U) frissítése folyamatban van az automatikus karbantartás mód észlelési tartalmaz
- Kényszerített frissítési feladatok üzembe helyezéséhez és a régiót tartalmaz
- Egyéni adatokat adhat hozzá egy régió
- Támogatja az értesítések és jelentések

A System Center felügyeleti csomag letöltheti a Microsoft Azure Stack és a társított [felhasználói útmutató](https://www.microsoft.com/en-us/download/details.aspx?id=55184), vagy közvetlenül az Operations Managerből.

Hibajegykezelő megoldások integrálható az Operations Manager-System Center Service Manager. A beépített termék-összekötő lehetővé teszi, hogy a kétirányú kommunikációt, amely lehetővé teszi, hogy az Azure Stack és az Operations Manager riasztás bezárása egy szolgáltatási kérelmet a Service Manager megoldása után.

Az alábbi ábrán látható, az Azure Stack integrálása meglévő System Center-környezet. A Service Manager további a System Center Orchestrator vagy a Service Management Automation (SMA) műveletek futtatásához az Azure Stackben automatizálható.

![Integráció az OM, a Service Manager és az SMA bemutató diagram.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Nagios integrálása

A beépülő modul figyelési Nagios fejlesztette ki együtt a partnermegoldások Cloudbase érhető el a megengedő ingyenes szoftverlicenc - MIT (Massachusetts Institute of Technology) alatt.

A beépülő modul Python nyelven van megírva, és az egészségügyi erőforrás-szolgáltató REST API-t használja. Alapszintű funkció lekéréséhez és a riasztások bezárása az Azure Stack kínál. Például a System Center felügyeleti csomag lehetővé teszi a több Azure Stack központi telepítéseket adhat hozzá és értesítések küldéséhez.

A beépülő modul a Nagios vállalati és Nagios Core működik. Letöltheti a [Itt](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). A letöltési hely telepítési és konfigurációs részleteket is tartalmaz.

### <a name="plugin-parameters"></a>Beépülő modul paraméterei

A beépülőmodul-fájlt "Azurestack_plugin.py" adja meg a következő paraméterekkel:

| Paraméter | Leírás | Példa |
|---------|---------|---------|
| *arm_endpoint* | Az Azure Resource Manager (rendszergazda) végpontja | https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Az Azure Resource Manager (rendszergazda) végpontja  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | Felügyeleti előfizetés-azonosító | Az adminisztrátori portál vagy a Powershellen keresztül beolvasása |
| *User_name* | Operátor előfizetés felhasználónév | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Operátor előfizetés jelszava | SajátJelszó |
| *Client_id* | Ügyfél | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Az Azure Stack régió neve | helyi |
|  |  |

* A PowerShell GUID, amely biztosítja az univerzális. Használhatja az egyes központi telepítések.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>A figyelő állapotát és a riasztások a PowerShell használatával

Ha nem használja az Operations Manager, Nagios, illetve a Nagios-alapú megoldás, a PowerShell használatával széles skálájával figyelési megoldásoknak integrálása az Azure-verem engedélyezése.

1. A PowerShell segítségével, győződjön meg arról, hogy rendelkezik-e [PowerShell telepített és konfigurált](azure-stack-powershell-install.md) számára az Azure Stack-üzemeltető környezet. Telepítse a PowerShell helyi számítógépre, amely képes elérni az erőforrás-kezelő (rendszergazda) végpont (https://adminmanagement. [régió]. [External_FQDN]).

2. Futtassa a következő parancsok futtatásával csatlakozhat az Azure Stack-környezet az Azure Stack operátorait szerint:

   ```powershell
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https:\//adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Riasztások például az alábbi példák parancsokat használja:
   ```powershell
    #Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    #Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    #Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról

További információ a beépített állapot-ellenőrzés: [figyelni és riasztásokat az Azure Stackben](azure-stack-monitor-health.md).

## <a name="next-steps"></a>További lépések

[Biztonsági integrálása](azure-stack-integrate-security.md)
