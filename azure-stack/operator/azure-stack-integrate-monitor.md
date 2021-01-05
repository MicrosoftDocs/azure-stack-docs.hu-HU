---
title: Külső figyelési megoldás integrálása Azure Stack hubhoz
description: Ismerje meg, hogyan integrálhatja Azure Stack hubot egy külső figyelési megoldással az adatközpontjában.
author: PatAltimore
ms.topic: article
ms.date: 11/18/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 11/18/2020
ms.openlocfilehash: 79fd494996c87aa513fc7aa4ab0554449c5770d5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870902"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack-hub"></a>Külső figyelési megoldás integrálása Azure Stack hubhoz

Az Azure Stack hub-infrastruktúra külső figyeléséhez figyelnie kell a Azure Stack hub szoftverét, a fizikai számítógépeket és a fizikai hálózati kapcsolókat. Ezen területek mindegyike az állapot-és riasztási adatok lekérésére szolgáló módszert kínál:

- Az Azure Stack hub szoftver REST-alapú API-t kínál az állapot és a riasztások lekéréséhez. A szoftverek által meghatározott technológiák, például a Közvetlen tárolóhelyek, a tárolási állapot és a riasztások használata a szoftverek figyelésének részét képezik.
- A fizikai számítógépek az alaplapi felügyeleti vezérlők (bmc-EK) segítségével elérhetővé tehetik az állapot-és riasztási információkat.
- A fizikai hálózati eszközök az SNMP protokollon keresztül elérhetővé tehetik az állapot-és riasztási információkat.

Minden Azure Stack hub-megoldás hardveres életciklus-állomással rendelkezik. Ez a gazdagép futtatja az eredeti berendezésgyártó (OEM) hardvergyártó által felügyelt szoftvert a fizikai kiszolgálók és a hálózati eszközök számára. Ha a figyelési megoldásai integrálva vannak az adatközpontban meglévő figyelési megoldásokkal, akkor ellenőrizze az OEM-szolgáltatót.

> [!IMPORTANT]
> A használt külső figyelési megoldásnak ügynök nélkül kell lennie. Azure Stack hub-összetevőkön belül nem telepíthet külső gyártótól származó ügynököket.

Az alábbi ábrán egy Azure Stack hub integrált rendszer, a hardver életciklus-gazdagép, egy külső figyelési megoldás, valamint egy külső Ticketing/adatgyűjtő rendszer közötti adatforgalom látható.

![Az Azure Stack hub, a monitoring és a Ticketing megoldás közötti forgalmat bemutató ábra.](media/azure-stack-integrate-monitor/monitoringintegration.svg)  

> [!NOTE]
> A külső figyelési integráció közvetlenül a fizikai kiszolgálókkal nem engedélyezett és aktívan le van tiltva Access Control listák (ACL-ek) használatával. A külső monitorozási integráció közvetlenül a fizikai hálózati eszközökkel támogatott. A funkció engedélyezéséhez érdeklődjön az OEM-szolgáltatónál.

Ez a cikk azt ismerteti, hogyan integrálható Azure Stack hub külső figyelési megoldásokkal, például a System Center Operations Manager és a Nagios szolgáltatással. Azt is ismerteti, hogyan használhatók a riasztások programozott módon a PowerShell vagy a REST API-hívások segítségével.

## <a name="integrate-with-operations-manager"></a>Integrálás Operations Manager

A Operations Manager az Azure Stack hub külső monitorozására is használható. A Microsoft Azure Stack hub System Center felügyeleti csomagja lehetővé teszi több Azure Stack hub-telepítés figyelését egyetlen Operations Manager-példánnyal. A felügyeleti csomag az állapot erőforrás-szolgáltatót használja, és az erőforrás-szolgáltató REST API-k használatával kommunikál Azure Stack hubhoz. Ha azt tervezi, hogy megkerüli a hardver életciklus-gazdagépén futó OEM-figyelési szoftvert, akkor a fizikai kiszolgálók figyeléséhez telepítheti a szállítói felügyeleti csomagokat. A hálózati kapcsolók figyeléséhez Operations Manager hálózati eszközök felderítését is használhatja.

Az Azure Stack hub felügyeleti csomagja a következő képességeket biztosítja:

- Több Azure Stack hub-telepítést is kezelhet.
- A Azure Active Directory (Azure AD) és a Active Directory összevonási szolgáltatások (AD FS) (AD FS) támogatása is támogatott.
- A riasztásokat lekérheti és lezárhatja.
- Van egy állapot és egy kapacitás irányítópult.
- Az automatikus karbantartási mód észlelését tartalmazza, ha a javítás és frissítés (P&U) folyamatban van.
- A központi telepítés és a régió esetében kényszerített frissítési feladatokat tartalmaz.
- Egyéni adatokat adhat hozzá egy régióhoz.
- A támogatja az értesítéseket és a jelentéskészítést.

A System Center felügyeleti csomag és a hozzá tartozó felhasználói útmutató letöltéséhez tekintse [meg a System Center felügyeleti csomag letöltése Microsoft Azure stack hubhoz](https://www.microsoft.com/en-us/download/details.aspx?id=55184)című témakört. Közvetlenül a Operations Managerból is letöltheti.

A Ticketing megoldáshoz a Operations Managert integrálhatja System Center Service Manager használatával. Az integrált termék-összekötő lehetővé teszi a kétirányú kommunikációt, amely lehetővé teszi a riasztások bezárását Azure Stack hub-ban, és Operations Manager, miután feloldotta egy szolgáltatási kérelmet a Service Manager.

A következő ábra az Azure Stack hub meglévő System Center-telepítéssel való integrálását mutatja be. A System Center Orchestrator vagy a Service Management Automation (SMA) Service Manager tovább automatizálhatja a műveleteket a Azure Stack hub-ban.

![Az OM, a Service Manager és az SMA integrációját bemutató ábra.](media/azure-stack-integrate-monitor/systemcenterintegration.svg)

## <a name="integrate-with-nagios"></a>Integrálás a Nagios-nal

Beállíthatja és konfigurálhatja a Microsoft Azure Stack hub-hoz készült Nagios beépülő modult.

A Nagios-figyelési beépülő modul a partneri Cloudbase-megoldásokkal együtt lett kifejlesztve, amely a megengedő ingyenes szoftverlicenc-MIT (Massachusetts Institute of Technology) keretében érhető el.

A beépülő modul Pythonban van megírva, és kihasználja az állapot erőforrás-szolgáltató REST API. Alapszintű funkciókat biztosít a riasztások lekéréséhez és bezárásához Azure Stack központban. A System Center felügyeleti csomaghoz hasonlóan több Azure Stack hub üzemelő példány hozzáadását és értesítések küldését is lehetővé teszi.

Az 1,2-es verzióban az Azure Stack hub-Nagios beépülő modul a Microsoft ADAL-függvénytárat használja, és támogatja a hitelesítést az egyszerű szolgáltatás használatával titkos vagy tanúsítvánnyal. Emellett a konfigurációt egy új paraméterekkel rendelkező, egyetlen konfigurációs fájllal egyszerűsítettük. Mostantól támogatja Azure Stack hub üzemelő példányait az Azure AD-vel, és az identitásrendszer AD FS.

> [!IMPORTANT]
> AD FS csak az interaktív bejelentkezési munkameneteket támogatja. Ha nem interaktív bejelentkezésre van szüksége egy automatikus forgatókönyvhöz, SPN-t kell használnia.

A beépülő modul az 4x-es és a XI-as verzióval működik. A beépülő modul letöltéséhez lásd: [Azure stack hub-riasztások figyelése](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). A letöltési hely tartalmazza a telepítési és konfigurációs adatokat is.

### <a name="requirements-for-nagios"></a>A Nagios követelményei

1. A Nagios minimális verziója 4. x

2. Microsoft Azure Active Directory Python-függvénytárat. Ezt a kódtárat a Python PIP használatával lehet telepíteni.

    ```bash  
    sudo pip install adal pyyaml six
    ```

### <a name="install-plugin"></a>Beépülő modul telepítése

Ez a szakasz azt ismerteti, hogyan telepítheti az Azure Stack hub beépülő modult, amely a Nagios alapértelmezett telepítését feltételezi.

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

1. Másolja a beépülő modult `azurestack_plugin.py` a következő könyvtárba: `/usr/local/nagios/libexec` .

2. Másolja a kezelőt `azurestack_handler.sh` a következő könyvtárba: `/usr/local/nagios/libexec/eventhandlers` .

3. Győződjön meg arról, hogy a beépülő modul fájlja végrehajtható:

    ```bash
    sudo cp azurestack_plugin.py <PLUGINS_DIR>
    sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>Beépülő modul konfigurálása

A következő paraméterek konfigurálhatók a azurestack. cfg fájlban. A félkövérrel szedett paramétereket a kiválasztott hitelesítési modelltől függetlenül kell konfigurálni.

Az egyszerű szolgáltatásnév létrehozásával kapcsolatos további információkért lásd: [alkalmazás-identitás használata az erőforrásokhoz való hozzáféréshez](azure-stack-create-service-principals.md).

| Paraméter | Leírás | Hitelesítés |
| --- | --- | --- |
| **External_domain_fqdn** | Külső tartomány teljes tartományneve |    |
| **régió** | Régiónév |    |
| **tenant_id:** | Bérlőazonosító\* |    |
| client_id: | Ügyfél-azonosító | SPN titkos kulccsal |
| client_secret: | Ügyfél jelszava | SPN titkos kulccsal |
| client_cert \* \* : | Tanúsítvány elérési útja | SPN tanúsítvánnyal |
| client_cert_thumbprint \* \* : | Tanúsítvány ujjlenyomata | SPN tanúsítvánnyal |

\*A bérlői azonosító nem szükséges Azure Stack központi telepítéshez AD FS.

\*\* Az ügyfél titkos kulcsa és az ügyfél tanúsítványa kölcsönösen kizárják egymást.

A többi konfigurációs fájl opcionális konfigurációs beállításokat tartalmaz, mivel azok a Nagios-ben is konfigurálhatók.

> [!Note]  
> Keresse meg a hely célhelyét azurestack_hosts. cfg és azurestack_services. cfg fájlon.

| Konfiguráció | Leírás |
| --- | --- |
| azurestack_commands. cfg | A kezelő konfigurációjában nincs szükség módosításra |
| azurestack_contacts. cfg | Értesítési beállítások |
| azurestack_hosts. cfg | Azure Stack hub központi telepítésének elnevezése |
| azurestack_services. cfg | A szolgáltatás konfigurációja |

### <a name="setup-steps"></a>A beállítás lépései

1. Módosítsa a konfigurációs fájlt.

2. Másolja a módosított konfigurációs fájlokat a következő mappába: `/usr/local/nagios/etc/objects` .

### <a name="update-nagios-configuration"></a>A Nagios konfigurációjának frissítése

A Nagios konfigurációját frissíteni kell annak érdekében, hogy az Azure Stack hub-Nagios beépülő modul betöltődik.

1. Nyissa meg a következő fájlt:

   ```bash  
   /usr/local/nagios/etc/nagios.cfg
   ```

2. Szúrja be a következő bejegyzést:

   ```bash  
   # Load the Azure Stack Hub Plugin Configuration
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
   ```

3. A Nagios újratöltése.

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

### <a name="troubleshooting"></a>Hibaelhárítás

A beépülő modul hibaelhárítása úgy történik, hogy manuálisan hívja meg a beépülő modult egy terminálon. Használja a következő módszert:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Az állapot és a riasztások figyelése a PowerShell használatával

Ha nem használ Operations Manager, a Nagios vagy a Nagios-alapú megoldást, a PowerShell segítségével számos figyelési megoldást engedélyezhet a Azure Stack hub-nal való integráláshoz.

### <a name="az-modules"></a>[Az modulok](#tab/az)

1. A PowerShell használatához győződjön meg arról, hogy a [PowerShell telepítve van és konfigurálva](powershell-install-az-module.md) van egy Azure stack hub-kezelő környezethez. Telepítse a PowerShellt egy helyi számítógépre, amely elérheti a Resource Manager-(rendszergazdai) végpontot ( https://adminmanagement . [ régió]. [External_FQDN]).

2. Futtassa a következő parancsokat az Azure Stack hub-környezethez Azure Stack hub-operátorként való kapcsolódáshoz:

   ```powershell
   Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Connect-AzAccount -EnvironmentName "AzureStackAdmin"
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

### <a name="azurerm-modules"></a>[AzureRM modulok](#tab/azurerm)

1. A PowerShell használatához győződjön meg arról, hogy a [PowerShell telepítve van és konfigurálva](powershell-install-az-module.md) van egy Azure stack hub-kezelő környezethez. Telepítse a PowerShellt egy helyi számítógépre, amely elérheti a Resource Manager-(rendszergazdai) végpontot ( https://adminmanagement . [ régió]. [External_FQDN]).

2. Futtassa a következő parancsokat az Azure Stack hub-környezethez Azure Stack hub-operátorként való kapcsolódáshoz:

   ```powershell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Connect-AzureRMAccount -EnvironmentName "AzureStackAdmin"
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

---

## <a name="learn-more"></a>További információ

További információ a beépített állapot-figyelésről: [az állapot és a riasztások figyelése Azure stack központban](azure-stack-monitor-health.md).

## <a name="next-steps"></a>További lépések

[Biztonsági integráció](azure-stack-integrate-security.md)
