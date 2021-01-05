---
title: A helyi ügynök üzembe helyezése
titleSuffix: Azure Stack Hub
description: Megtudhatja, hogyan helyezheti üzembe a helyi ügynököt Azure Stack hub érvényesítéséhez szolgáltatásként.
author: mattbriggs
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ef131c1b7b54c05b6feee9336a0875f3507f5174
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868369"
---
# <a name="deploy-the-local-agent"></a>A helyi ügynök üzembe helyezése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Megtudhatja, hogyan használhatók az érvényesítési szolgáltatásként szolgáló helyi ügynök az érvényesítési tesztek futtatásához. Az ellenőrzési tesztek futtatása előtt telepíteni kell a helyi ügynököt.

> [!Note]  
> Győződjön meg arról, hogy a helyi ügynököt futtató számítógép nem veszíti el a kimenő hozzáférést az internethez. Ez a gép csak olyan felhasználók számára érhető el, akik jogosultak az Varga használatára a bérlő nevében.

A helyi ügynök üzembe helyezése:

1. Töltse le és telepítse a helyi ügynököt.
2. A tesztek megkezdése előtt a józan ész-ellenőrzéseket végezze.
3. Futtassa a helyi ügynököt.

## <a name="download-and-start-the-local-agent"></a>A helyi ügynök letöltése és elindítása

Töltse le az ügynököt egy olyan gépre, amely megfelel az adatközpont előfeltételeinek, és hozzáfér az összes Azure Stack hub-végponthoz. Ez a gép nem lehet a Azure Stack hub rendszer része, vagy az Azure Stack hub-felhőben üzemel.

### <a name="machine-prerequisites"></a>A gép előfeltételei

Győződjön meg arról, hogy a gép megfelel a következő feltételeknek:

- Hozzáférés az összes Azure Stack hub-végponthoz.
- A .NET 4,6 és a PowerShell 5,0 telepítve van.
- Legalább 8 GB RAM.
- Legalább 8 magos processzor.
- Minimális 200 – GB szabad lemezterület.
- Állandó hálózati kapcsolat az internettel.

### <a name="download-and-install-the-local-agent"></a>A helyi ügynök letöltése és telepítése

1. Nyissa meg a Windows PowerShellt egy emelt szintű parancssorban azon a gépen, amelyet a tesztek futtatásához fog használni.
2. Futtassa a következő parancsot a helyi ügynök függőségeinek letöltéséhez és telepítéséhez, és másolja a nyilvános rendszerkép-tárház (PIR) lemezképeit (az operációs rendszer VHD-jét) a Azure Stack hub-környezetbe.

    ```powershell
    # Review and update the following five parameters
    $RootFolder = "c:\VaaS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $AadServiceAdminUserName = "<AAD service admin user name>"
    $AadServiceAdminPassword = "<AAD service admin password>"

    if (-not(Test-Path($RootFolder))) {
        mkdir $RootFolder
    }
    Set-Location $RootFolder
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "$rootFolder\OnPremAgent.zip"
    Expand-Archive -Path "$rootFolder\OnPremAgent.zip" -DestinationPath "$rootFolder\VaaSOnPremAgent" -Force
    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"

    $cloudAdminCredential = New-Object System.Management.Automation.PSCredential($cloudAdmindUserName, (ConvertTo-SecureString $cloudAdminPassword -AsPlainText -Force))
    $getStampInfoUri = "https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation" 
    $stampInfo = Invoke-RestMethod -Method Get -Uri $getStampInfoUri -Credential $cloudAdminCredential -ErrorAction Stop
    $serviceAdminCreds = New-Object System.Management.Automation.PSCredential $aadServiceAdminUserName, (ConvertTo-SecureString $aadServiceAdminPassword -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $stampInfo.AADTenantID `
                            -ServiceAdminCreds $serviceAdminCreds `
                            -ArmEndpoint $stampInfo.AdminExternalEndpoints.AdminResourceManager `
                            -Region $stampInfo.RegionName
    ```

> [!Note]  
> `Install-VaaSPrerequisites` a parancsmag nagyméretű virtuálisgép-lemezképfájlokat tölt le. Ha lassú a hálózati sebesség, letöltheti a fájlokat a helyi fájlkiszolgálón, és manuálisan is hozzáadhat virtuálisgép-lemezképeket a tesztelési environemnt. További információ: [lassú hálózati kapcsolat kezelése](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).

**Paraméterek**

| Paraméter | Leírás |
| --- | --- |
| `AadServiceAdminUser` | Az Azure AD-bérlő globális rendszergazdai felhasználója. Például: vaasadmin@contoso.onmicrosoft.com. |
| `AadServiceAdminPassword` | A globális rendszergazda felhasználó jelszava. |
| `CloudAdminUserName` | A Felhőbeli rendszergazda felhasználó, aki elérheti és futtathatja az engedélyezett parancsokat a Kiemelt végponton belül. Például: AzusreStack\CloudAdmin. További információ: [általános munkafolyamat-paraméterek az Varga-hez](azure-stack-vaas-parameters.md). |
| `CloudAdminPassword` | A Felhőbeli rendszergazdai fiók jelszava.|

![Helyi ügynök előfeltételeinek letöltése](media/installing-prereqs.png)

## <a name="perform-sanity-checks-before-starting-the-tests"></a>A tesztek elkezdése előtt a józan ész ellenőrzése

A tesztek távoli műveleteket futtatnak. A teszteket futtató gépnek hozzá kell férnie az Azure Stack hub-végpontokhoz, ellenkező esetben a tesztek nem fognak működni. Ha az alaprendszer-kezelő helyi ügynököt használja, használja az ügynököt futtató gépet. A következő ellenőrzések futtatásával ellenőrizheti, hogy a számítógépe rendelkezik-e hozzáféréssel a Azure Stack hub-végpontokhoz:

1. Győződjön meg arról, hogy az alapszintű URI elérhető. Nyisson meg egy parancssort vagy bash-rendszerhéjat, és futtassa a következő parancsot, és cserélje le a- `<EXTERNALFQDN>` környezet külső teljes tartománynevére (FQDN):

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Nyisson meg egy böngészőt, és lépjen a `https://adminportal.<EXTERNALFQDN>` lehetőségre, és győződjön meg arról, hogy a Mas portál elérhető.

3. Jelentkezzen be az Azure AD szolgáltatás-rendszergazda nevével és jelszavával, amely a tesztelési fázis létrehozásakor van megadva.

4. Ellenőrizze a rendszer állapotát a **test-AzureStack** PowerShell-parancsmag futtatásával az [Azure stack hub ellenőrzési teszt futtatása](../operator/azure-stack-diagnostic-test.md)című témakörben leírtak szerint. A tesztek elindítása előtt javítsa ki a figyelmeztetéseket és a hibákat.

## <a name="run-the-local-agent"></a>A helyi ügynök futtatása

1. Nyissa meg a Windows PowerShellt egy emelt szintű parancssorban.

2. Futtassa az alábbi parancsot:

    ```powershell
   # Review and update the following five parameters
    $RootFolder = "c:\VAAS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $VaaSUserId = "<VaaS user ID>"
    $VaaSTenantId = "<VaaS tenant ID>"

    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u $VaaSUserId -t $VaaSTenantId -x $CloudAdmindUserName -y $CloudAdminPassword
    ```

      **Paraméterek**  

    | Paraméter | Leírás |
    | --- | --- |
    | `CloudAdminUserName` | A Felhőbeli rendszergazda felhasználó, aki elérheti és futtathatja az engedélyezett parancsokat a Kiemelt végponton belül. Például: AzusreStack\CloudAdmin. További információért lásd: [a közös munkafolyamat-paraméterek az Varga](azure-stack-vaas-parameters.md) -hoz című témakörben. |
    | `CloudAdminPassword` | A Felhőbeli rendszergazdai fiók jelszava.|
    | `VaaSUserId` | Az Azure Stack hub ellenőrzési portálján való bejelentkezéshez használt felhasználói azonosító. Például: UserName \@ contoso.com). |
    | `VaaSTenantId` | Azure AD-bérlő azonosítója az érvényesítés szolgáltatásként regisztrált Azure-fiókhoz. |

    > [!Note]  
    > Ha futtatja az ügynököt, az aktuális munkakönyvtárnak a Task Engine Host végrehajtható fájljának helyének kell lennie, **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Ha nem jelenik meg a jelentett hibák, akkor a helyi ügynök sikeres volt. A konzol ablakban a következő példában szereplő szöveg jelenik meg.

`Heartbeat was sent successfully.`

![Ügynök elindítva](media/started-agent.png)

Az ügynököket a neve egyedileg azonosítja. Alapértelmezés szerint a a gép FQDN-nevét használja az indítás helyétől kezdve. Kis méretűre kell állítani az ablakot, hogy elkerülje a véletlen kijelölést az ablakon, mivel a fókusz megváltoztatásával szünetelteti az összes többi műveletet.

## <a name="next-steps"></a>További lépések

- [A szolgáltatás érvényesítésének megoldása](azure-stack-vaas-troubleshoot.md)
- [Érvényesítés szolgáltatási kulcs fogalmai szerint](azure-stack-vaas-key-concepts.md)
- [Rövid útmutató: az első teszt a Azure Stack hub ellenőrzési portál használatával ütemezhetők.](azure-stack-vaas-schedule-test-pass.md)