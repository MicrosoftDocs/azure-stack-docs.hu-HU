---
title: A helyi ügynök üzembe helyezése
description: Telepítse a helyi ügynököt Azure Stack érvényesítéshez szolgáltatásként.
author: mattbriggs
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a84f2495b0e2195ac143dc8fd9ac4ea4668392b4
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884088"
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

Töltse le az ügynököt egy olyan gépre, amely megfelel az adatközpont előfeltételeinek, és hozzáfér az összes Azure Stack-végponthoz. Ez a gép nem lehet a Azure Stack rendszer részét képező vagy a Azure Stack-felhőben üzemeltetett számítógép.

### <a name="machine-prerequisites"></a>A gép előfeltételei

Győződjön meg arról, hogy a gép megfelel a következő feltételeknek:

- Hozzáférés az összes Azure Stack végponthoz
- .NET 4,6 és PowerShell 5,0 telepítve
- Legalább 8 GB RAM
- Legalább 8 magos processzor
- Minimális 200 – GB szabad lemezterület
- Állandó hálózati kapcsolat az internettel

### <a name="download-and-install-the-local-agent"></a>A helyi ügynök letöltése és telepítése

1. Nyissa meg a Windows PowerShellt egy emelt szintű parancssorban azon a gépen, amelyet a tesztek futtatásához fog használni.
2. Futtassa a következő parancsot a helyi ügynök függőségeinek letöltéséhez és telepítéséhez, és másolja a nyilvános rendszerkép-tárház (PIR) lemezképeit (az operációs rendszer VHD-jét) a Azure Stack környezetbe.

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
> Az install-VaaSPrerequisites parancsmag nagyméretű virtuálisgép-lemezképfájlokat tölt le. Ha lassú a hálózati sebesség, letöltheti a fájlokat a helyi fájlkiszolgálón, és manuálisan is hozzáadhat virtuálisgép-lemezképeket a tesztelési environemnt. További információt a [lassú hálózati kapcsolat kezelése](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) című témakörben talál.

**Paraméterek**

| Paraméter | Leírás |
| --- | --- |
| AadServiceAdminUser | Az Azure AD-bérlő globális rendszergazdai felhasználója. Előfordulhat például, hogy vaasadmin@contoso.onmicrosoft.com. |
| AadServiceAdminPassword | A globális rendszergazda felhasználó jelszava. |
| CloudAdminUserName | A Felhőbeli rendszergazda felhasználó, aki elérheti és futtathatja az engedélyezett parancsokat a Kiemelt végponton belül. Ilyen lehet például a AzusreStack\CloudAdmin. További információért lásd [itt](azure-stack-vaas-parameters.md) . |
| CloudAdminPassword | A Felhőbeli rendszergazdai fiók jelszava.|

![Előfeltételek letöltése](media/installing-prereqs.png)

## <a name="perform-sanity-checks-before-starting-the-tests"></a>A tesztek elkezdése előtt a józan ész ellenőrzése

A tesztek távoli műveleteket futtatnak. A teszteket futtató gépnek hozzá kell férnie az Azure Stack-végpontokhoz, ellenkező esetben a tesztek nem fognak működni. Ha az az Varga helyi ügynököt használja, akkor használja az ügynököt futtató gépet. A következő ellenőrzések futtatásával ellenőrizheti, hogy a számítógépe rendelkezik-e hozzáféréssel a Azure Stack végpontokhoz:

1. Győződjön meg arról, hogy az alapszintű URI elérhető. Nyisson meg egy parancssort vagy bash rendszerhéjat, és futtassa a következő parancsot, és cserélje le a `<EXTERNALFQDN>`t a környezet külső teljes tartománynevére:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Nyisson meg egy webböngészőt, és lépjen a `https://adminportal.<EXTERNALFQDN>`re, és győződjön meg arról, hogy a MAS portál elérhető.

3. Jelentkezzen be az Azure AD szolgáltatás-rendszergazda nevével és jelszavával, amely a tesztelési fázis létrehozásakor van megadva.

4. Ellenőrizze a rendszer állapotát a **test-AzureStack** PowerShell-parancsmag futtatásával a [Azure stack ellenőrzési teszt futtatása](../operator/azure-stack-diagnostic-test.md)című témakörben leírtak szerint. A tesztek elindítása előtt javítsa ki a figyelmeztetéseket és a hibákat.

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
    | CloudAdminUserName | A Felhőbeli rendszergazda felhasználó, aki elérheti és futtathatja az engedélyezett parancsokat a Kiemelt végponton belül. Ilyen lehet például a AzusreStack\CloudAdmin. További információért lásd [itt](azure-stack-vaas-parameters.md) . |
    | CloudAdminPassword | A Felhőbeli rendszergazdai fiók jelszava.|
    | VaaSUserId | Az Contoso.com-portálra való bejelentkezéshez használt felhasználói azonosító (például UserName\@) |
    | VaaSTenantId | Azure AD-bérlő azonosítója az érvényesítés szolgáltatásként regisztrált Azure-fiókhoz. |

    > [!Note]  
    > Az ügynök futtatásakor az aktuális munkakönyvtárnak a Task Engine Host végrehajtható fájljának, a **Microsoft. VaaSOnPrem. TaskEngineHost. exe** helynek kell lennie.

Ha nem jelenik meg a jelentett hibák, akkor a helyi ügynök sikeres volt. A konzol ablakban a következő példában szereplő szöveg jelenik meg.

`Heartbeat was sent successfully.`

![Ügynök elindítva](media/started-agent.png)

Az ügynököket a neve egyedileg azonosítja. Alapértelmezés szerint a a gép teljes tartománynevét (FQDN) használja, ahonnan az elindult. Kis méretűre kell állítani az ablakot, hogy elkerülje a véletlen kijelölést az ablakon, mivel a fókusz megváltoztatásával szünetelteti az összes többi műveletet.

## <a name="next-steps"></a>Következő lépések

- [A szolgáltatás érvényesítésének megoldása](azure-stack-vaas-troubleshoot.md)
- [Érvényesítés szolgáltatási kulcs fogalmai szerint](azure-stack-vaas-key-concepts.md)
- [Gyors útmutató: az ellenőrzés használata szolgáltatás-portálként az első teszt beütemezett végrehajtásához](azure-stack-vaas-schedule-test-pass.md)