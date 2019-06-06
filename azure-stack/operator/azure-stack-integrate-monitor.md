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
ms.date: 06/05/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: e0c3c4740a1bc8073e827ff9809cf1aafa029792
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691692"
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

Állítsa be, és konfigurálja a Nagios beépülő modul a Microsoft Azure Stackhez készült.

A beépülő modul figyelési Nagios fejlesztette ki együtt a partnermegoldások Cloudbase érhető el a megengedő ingyenes szoftverlicenc - MIT (Massachusetts Institute of Technology) alatt.

A beépülő modul Python nyelven van megírva, és az egészségügyi erőforrás-szolgáltató REST API-t használja. Alapszintű funkció lekéréséhez és a riasztások bezárása az Azure Stack kínál. Például a System Center felügyeleti csomag lehetővé teszi a több Azure Stack központi telepítéseket adhat hozzá és értesítések küldéséhez.

Az 1.2-es verziója az Azure Stack – Nagios beépülő modult használja a Microsoft ADAL-könyvtár, és támogatja a hitelesítést egy titkos kulcsot vagy a tanúsítványt az egyszerű szolgáltatás használatával. Emellett a konfigurációs egyszerűsítettük egyetlen konfigurációs fájl használatával új paraméterekkel. Azure Stack üzemelő példányok AAD & AD FS használatával identitás rendszer mostantól támogatja.

A beépülő modul együttműködik Nagios XI és a 4 x. Letöltheti a [Itt](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). A letöltési hely telepítési és konfigurációs részleteket is tartalmaz.

### <a name="requirements-for-nagios"></a>Nagios-követelményei

1.  Minimális Nagios verziószáma 4.x

2.  A Microsoft Azure Active Directory Python-kódtár. Ez is telepíthető, Python PIP használatával.

```bash  
sudo pip install adal pyyaml six
```

### <a name="install-plugin"></a>Beépülő modul telepítése

Ez a szakasz ismerteti az alapértelmezett telepítés Nagios, feltéve, hogy az Azure Stack beépülő modul telepítése.

A beépülő csomag tartalmazza a következő fájlokat:

```
  azurestack_plugin.py
  azurestack_handler.sh
  samples/etc/azurestack.cfg
  samples/etc/azurestack_commands.cfg
  samples/etc/azurestack_contacts.cfg
  samples/etc/azurestack_hosts.cfg
  samples/etc/azurestack_services.cfg
```

1.  Másolja a beépülő modul `azurestack_plugin.py` a következő könyvtárba `/usr/local/nagios/libexec`.

2.  Másolja a kezelő `azurestack_handler.sh` a következő könyvtárba `/usr/local/nagios/libexec/eventhandlers`.

3.  Győződjön meg arról, a beépülő modul fájlt is futtatható legyen van beállítva.

    ```bash
      sudo cp azurestack_plugin.py <PLUGINS_DIR>
      sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>Beépülő modul konfigurálása

Az alábbi paramétereket kell konfigurálni a azurestack.cfg fájlban érhetők el. A félkövérrel szedett paraméterek konfigurálni kell a választott hitelesítési modellből független.

Részletes információkat, hogyan hozhat létre egy egyszerű Szolgáltatásnevet dokumentált [Itt](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals).

| Paraméter | Leírás | Hitelesítés |
| --- | --- | --- |
| **External_domain_fqdn ** | Külső tartomány teljes Tartományneve |    |
| ** régió: ** | Régiónév |    |
| **tenant_id: ** | Bérlő azonosítója\* |    |
| client_id: | Ügyfél-azonosító | Titkos kulcs az egyszerű szolgáltatásnév |
| client_secret: | Ügyfél-jelszó | Titkos kulcs az egyszerű szolgáltatásnév |
| client_cert\*\*: | Tanúsítvány elérési útja | SPN-tanúsítvánnyal |
| client_cert_thumbprint\*\*: | Tanúsítvány ujjlenyomata | SPN-tanúsítvánnyal |

\*Bérlő azonosítója, nem szükséges az AD FS-Azure Stack-telepítésekhez.

\*\* Ügyféltanúsítvány titkos ügyfélkulcsot és olyan kölcsönösen kizárják egymást.

Az egyéb konfigurációs fájlokat választható konfigurációs beállításokat tartalmaznak, mivel azok konfigurálható Nagios is.

> [!Note]  
> Ellenőrizze a hely cél azurestack_hosts.cfg és azurestack_services.cfg.

| Konfiguráció | Leírás |
| --- | --- |
| azurestack_commands.cfg | Kezelő konfigurációs módosítások követelmény |
| azurestack_contacts.cfg | Értesítési beállítások |
| azurestack_hosts.cfg | Az Azure Stack üzembe helyezési elnevezése |
| azurestack_services.cfg | A szolgáltatás konfigurációját |

### <a name="setup-steps"></a>A telepítő lépéseit

1.  A konfigurációs fájl módosítása

2.  Másolja a módosított konfigurációs fájlt a következő `/usr/local/nagios/etc/objects`.

### <a name="update-nagios-configuration"></a>Nagios-konfiguráció frissítése

Az Azure Stack biztosítása érdekében frissíteni kell a Nagios konfiguráció – Nagios beépülő modul be töltve.

1.  A következő fájl megnyitása

```bash  
/usr/local/nagios/etc/nagios.cfg
```

1.  A következő bejegyzés hozzáadása

```bash  
  #load the Azure Stack Plugin Configuration
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
```

1.  Töltse be újra a Nagios

```bash  
sudo service nagios reload
```

### <a name="manually-close-active-alerts"></a>Aktív riasztások lezárása manuálisan

Aktív riasztások értesítő üzenet egyéni szövegében funkciójával Nagios belül bezárható. Az egyéni értesítést kell lennie:

```
  /close-alert <ALERT_GUID>
```

Riasztás is lehet lezárni a terminál segítségével a következő paranccsal:

```bash
  /usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Close --alert-id <ALERT_GUID>
```

### <a name="troubleshooting"></a>Hibaelhárítás

Hibaelhárítás a beépülő modul lehet hívása a beépülő modul manuális parancsot egy terminálban a kész. Használja a következő metódust:

```bash
  /usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

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

## <a name="learn-more"></a>Részletek

További információ a beépített állapot-ellenőrzés: [figyelni és riasztásokat az Azure Stackben](azure-stack-monitor-health.md).

## <a name="next-steps"></a>További lépések

[Biztonsági integrálása](azure-stack-integrate-security.md)
