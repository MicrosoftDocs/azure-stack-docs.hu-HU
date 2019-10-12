---
title: Külső figyelési megoldás integrálása a Azure Stackrel | Microsoft Docs
description: Ismerje meg, hogyan integrálhatja a Azure Stackt egy külső figyelési megoldással az adatközpontjában.
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
ms.date: 06/05/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: aa9b20b9ee80cfdb17dba3020c03718085d8b625
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277176"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Külső figyelési megoldás integrálása a Azure Stack

A Azure Stack infrastruktúra külső figyeléséhez figyelnie kell a Azure Stack szoftvereket, a fizikai számítógépeket és a fizikai hálózati kapcsolókat. Ezen területek mindegyike az állapot-és riasztási adatok lekérésére szolgáló módszert kínál:

- Azure Stack szoftver egy REST-alapú API-t kínál az állapot és a riasztások lekéréséhez. A szoftverek által meghatározott technológiák, például a Közvetlen tárolóhelyek, a tárolási állapot és a riasztások használata a szoftverek figyelésének részét képezik.
- A fizikai számítógépek az alaplapi felügyeleti vezérlők (bmc-EK) segítségével elérhetővé tehetik az állapot-és riasztási információkat.
- A fizikai hálózati eszközök az SNMP protokollon keresztül elérhetővé tehetik az állapot-és riasztási információkat.

Minden Azure Stack megoldás egy hardveres életciklus-állomással rendelkezik. Ez a gazdagép futtatja az eredeti berendezésgyártó (OEM) hardvergyártó által felügyelt szoftvert a fizikai kiszolgálók és a hálózati eszközök számára. Ha a figyelési megoldásai integrálva vannak az adatközpontban meglévő figyelési megoldásokkal, akkor ellenőrizze az OEM-szolgáltatót.

> [!IMPORTANT]
> A használt külső figyelési megoldásnak ügynök nélkül kell lennie. Azure Stack-összetevőkön belül nem telepíthet harmadik féltől származó ügynököket.

Az alábbi ábrán egy Azure Stack integrált rendszer, a hardver életciklus-állomása, egy külső figyelési megoldás és egy külső Ticketing/adatgyűjtő rendszer közötti adatforgalom látható.

![A Azure Stack, a figyelés és a jegyeladási megoldás közötti forgalmat bemutató ábra.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> A külső figyelési integráció közvetlenül a fizikai kiszolgálókkal nem engedélyezett és aktívan le van tiltva Access Control listák (ACL-ek) használatával. A külső monitorozási integráció közvetlenül a fizikai hálózati eszközökkel támogatott. A funkció engedélyezéséhez érdeklődjön az OEM-szolgáltatónál.

Ez a cikk azt ismerteti, hogyan integrálható a Azure Stack külső figyelési megoldásokkal, például a System Center Operations Manager és a Nagios használatával. Azt is ismerteti, hogyan használhatók a riasztások programozott módon a PowerShell vagy a REST API-hívások segítségével.

## <a name="integrate-with-operations-manager"></a>Integrálás Operations Manager

A Azure Stack külső figyeléséhez Operations Manager is használhatja. A Microsoft Azure Stack System Center felügyeleti csomagja lehetővé teszi több Azure Stack központi telepítés figyelését egyetlen Operations Manager-példánnyal. A felügyeleti csomag az állapot-erőforrás-szolgáltatót használja, és az erőforrás-szolgáltató REST API-k használatával kommunikál a Azure Stackokkal. Ha azt tervezi, hogy megkerüli a hardver életciklus-gazdagépén futó OEM-figyelési szoftvert, akkor a fizikai kiszolgálók figyeléséhez telepítheti a szállítói felügyeleti csomagokat. A hálózati kapcsolók figyeléséhez Operations Manager hálózati eszközök felderítését is használhatja.

A Azure Stack felügyeleti csomagja a következő képességeket biztosítja:

- Több Azure Stack üzemelő példány is kezelhető.
- A Azure Active Directory (Azure AD) és a Active Directory összevonási szolgáltatások (AD FS) (AD FS) támogatása is támogatott.
- A riasztásokat lekérheti és lezárhatja.
- Van egy állapot és egy kapacitás irányítópult.
- Az automatikus karbantartási mód észlelését tartalmazza, ha a javítás és frissítés (P & U) folyamatban van.
- A központi telepítés és a régió esetében kényszerített frissítési feladatokat tartalmaz.
- Egyéni adatokat adhat hozzá egy régióhoz.
- A támogatja az értesítéseket és a jelentéskészítést.

A System Center felügyeleti csomag és a hozzá tartozó felhasználói útmutató letöltéséhez tekintse [meg a System Center felügyeleti csomag letöltése a Microsoft Azure Stackhoz](https://www.microsoft.com/en-us/download/details.aspx?id=55184)című témakört. Közvetlenül a Operations Managerból is letöltheti.

A Ticketing megoldáshoz a Operations Managert integrálhatja System Center Service Manager használatával. Az integrált termék-összekötő kétirányú kommunikációt tesz lehetővé, amely lehetővé teszi a riasztások bezárását Azure Stack és Operations Manager után, miután feloldotta egy szolgáltatási kérelmet a Service Manager.

A következő ábra a Azure Stack integrációját mutatja be egy meglévő System Center-telepítéssel. A System Center Orchestrator vagy a Service Management Automation (SMA) Service Manager további műveleteket is automatizálhat a Azure Stack-műveletek futtatásához.

![Az OM, a Service Manager és az SMA integrációját bemutató ábra.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrálás a Nagios-nal

Beállíthatja és konfigurálhatja a Microsoft Azure Stackhez készült Nagios beépülő modult.

A Nagios-figyelési beépülő modul a partneri Cloudbase-megoldásokkal együtt lett kifejlesztve, amely a megengedő ingyenes szoftverlicenc-MIT (Massachusetts Institute of Technology) keretében érhető el.

A beépülő modul Pythonban van megírva, és kihasználja az állapot erőforrás-szolgáltató REST API. Alapszintű funkciókat biztosít a riasztások lekéréséhez és bezárásához Azure Stackban. A System Center felügyeleti csomaghoz hasonlóan több Azure Stack üzemelő példány hozzáadására és értesítések küldésére is lehetőséget nyújt.

Az 1,2-es verzióban az Azure Stack – Nagios beépülő modul a Microsoft ADAL-függvénytárat használja, és támogatja a hitelesítést egy titkos vagy tanúsítvánnyal rendelkező egyszerű szolgáltatásnév használatával. Emellett a konfigurációt egy új paraméterekkel rendelkező, egyetlen konfigurációs fájllal egyszerűsítettük. A szolgáltatás mostantól támogatja az Azure AD-t használó Azure Stack üzemelő példányokat, és az identitásrendszer AD FS.

A beépülő modul az 4x-es és a XI-as verzióval működik. A beépülő modul letöltéséhez lásd: [Azure stack riasztások figyelése](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). A letöltési hely tartalmazza a telepítési és konfigurációs adatokat is.

### <a name="requirements-for-nagios"></a>A Nagios követelményei

1.  A Nagios minimális verziója 4. x

2.  Microsoft Azure Active Directory Python-függvénytárat. Ezt a kódtárat a Python PIP használatával lehet telepíteni.

    ```bash  
    sudo pip install adal pyyaml six
    ```

### <a name="install-plugin"></a>Beépülő modul telepítése

Ez a szakasz azt ismerteti, hogyan telepítheti a Azure Stack beépülő modult, amely a Nagios alapértelmezett telepítését feltételezi.

A beépülő modul a következő fájlokat tartalmazza:

```
azurestack_plugin.py
azurestack_handler.sh
samples/etc/azurestack.cfg
samples/etc/azurestack_commands.cfg
samples/etc/azurestack_contacts.cfg
samples/etc/azurestack_hosts.cfg
samples/etc/azurestack_services.cfg
```

1.  Másolja a `azurestack_plugin.py` beépülő modult a következő könyvtárba: `/usr/local/nagios/libexec`.

2.  Másolja a `azurestack_handler.sh` kezelőt a következő könyvtárba: `/usr/local/nagios/libexec/eventhandlers`.

3.  Győződjön meg arról, hogy a beépülő modul fájlja végrehajtható:

    ```bash
    sudo cp azurestack_plugin.py <PLUGINS_DIR>
    sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>Beépülő modul konfigurálása

A következő paraméterek konfigurálhatók a azurestack. cfg fájlban. A félkövérrel szedett paramétereket a kiválasztott hitelesítési modelltől függetlenül kell konfigurálni.

Az egyszerű szolgáltatásnév létrehozásával kapcsolatos további információkért lásd: [alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals).

| Paraméter | Leírás | Hitelesítés |
| --- | --- | --- |
| **External_domain_fqdn ** | Külső tartomány teljes tartományneve |    |
| \* * régió: * * | Régió neve |    |
| **tenant_id: ** | Bérlő azonosítója @ no__t-0 |    |
| client_id: | Ügyfél-azonosító | SPN titkos kulccsal |
| client_secret: | Ügyfél jelszava | SPN titkos kulccsal |
| client_cert @ no__t-0 @ no__t-1: | Tanúsítvány elérési útja | SPN tanúsítvánnyal |
| client_cert_thumbprint @ no__t-0 @ no__t-1: | Tanúsítvány ujjlenyomata | SPN tanúsítvánnyal |

\*Tenant-azonosító nem szükséges a (AD FS) Azure Stack üzemelő példányokhoz.

\* @ no__t-1 ügyfél titkos kulcsa és az ügyfél-tanúsítvány kölcsönösen kizárják egymást.

A többi konfigurációs fájl opcionális konfigurációs beállításokat tartalmaz, mivel azok a Nagios-ben is konfigurálhatók.

> [!Note]  
> Keresse meg a azurestack_hosts. cfg és a azurestack_services. cfg helyen található célhelyet.

| Konfiguráció | Leírás |
| --- | --- |
| azurestack_commands. cfg | A kezelő konfigurációjában nincs szükség módosításra |
| azurestack_contacts. cfg | Értesítési beállítások |
| azurestack_hosts. cfg | Azure Stack központi telepítés elnevezése |
| azurestack_services. cfg | A szolgáltatás konfigurációja |

### <a name="setup-steps"></a>Telepítési lépések

1.  Módosítsa a konfigurációs fájlt.

2.  Másolja a módosított konfigurációs fájlokat a következő mappába: `/usr/local/nagios/etc/objects`.

### <a name="update-nagios-configuration"></a>A Nagios konfigurációjának frissítése

A Nagios konfigurációját frissíteni kell annak érdekében, hogy az Azure Stack – Nagios beépülő modul betöltődik.

1.  Nyissa meg a következő fájlt:

```bash  
/usr/local/nagios/etc/nagios.cfg
```

2.  Adja hozzá a következő bejegyzést:

```bash  
# Load the Azure Stack Plugin Configuration
cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
```

3.  A Nagios újratöltése.

```bash  
sudo service nagios reload
```

### <a name="manually-close-active-alerts"></a>Aktív riasztások manuális lezárása

Az aktív riasztások az egyéni értesítési funkciók használatával zárhatók be a Nagios-n belül. Az egyéni értesítésnek a következőket kell tennie:

```
/close-alert <ALERT_GUID>
```

A riasztások a következő paranccsal is lezárhatók egy terminál használatával:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Close --alert-id <ALERT_GUID>
```

### <a name="troubleshooting"></a>Hibakeresés

A beépülő modul hibaelhárítása úgy történik, hogy manuálisan hívja meg a beépülő modult egy terminálon. Használja a következő módszert:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Az állapot és a riasztások figyelése a PowerShell használatával

Ha nem használ Operations Manager, a Nagios vagy a Nagios-alapú megoldást, a PowerShell használatával számos figyelési megoldással integrálhatja az Azure Stack.

1. A PowerShell használatához győződjön meg arról, hogy a [PowerShell telepítve van és konfigurálva](azure-stack-powershell-install.md) van egy Azure stack operátori környezethez. Telepítse a PowerShellt egy helyi számítógépen, amely elérheti a Resource Manager-(rendszergazda-) végpontot (https://adminmanagement. [ régió]. [External_FQDN]).

2. Futtassa a következő parancsokat a Azure Stack-környezethez Azure Stack operátorként való kapcsolódáshoz:

   ```powershell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Connect-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Használjon olyan parancsokat, mint például az alábbi példák a riasztásokkal való együttműködésre:
   ```powershell
    # Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    # Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    # Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    # Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>További információ

További információ a beépített állapot-figyelésről: [az állapot és a riasztások figyelése Azure Stackban](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Következő lépések

[Biztonsági integráció](azure-stack-integrate-security.md)
