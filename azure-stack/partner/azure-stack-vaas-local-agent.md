---
title: A helyi ügynök üzembe helyezése | Microsoft Docs
description: Telepítse a helyi ügynököt Azure Stack érvényesítéshez szolgáltatásként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b1a658b428d13cdd12c16b767430f87a80e89fdc
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418371"
---
# <a name="deploy-the-local-agent"></a>A helyi ügynök üzembe helyezése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Megtudhatja, hogyan használhatók az érvényesítési szolgáltatásként szolgáló helyi ügynök az érvényesítési tesztek futtatásához. Az ellenőrzési tesztek futtatása előtt telepíteni kell a helyi ügynököt.

> [!Note]  
> Győződjön meg arról, hogy a helyi ügynököt futtató számítógép nem veszíti el a kimenő hozzáférést az internethez. Ez a gép csak olyan felhasználók számára érhető el, akik jogosultak az Varga használatára a bérlő nevében.

A helyi ügynök üzembe helyezése:

1. Telepítse a helyi ügynököt.
2. Józan ész-ellenőrzések végrehajtása.
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

### <a name="download-and-install-the-agent"></a>Az ügynök letöltése és telepítése

1. Nyissa meg a Windows PowerShellt egy emelt szintű parancssorban azon a gépen, amelyet a tesztek futtatásához fog használni.
2. Futtassa a következő parancsot a helyi ügynök letöltéséhez:

    ```powershell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Futtassa a következő parancsot a helyi ügynök függőségeinek telepítéséhez:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **Paraméterek**

    | Paraméter | Leírás |
    | --- | --- |
    | aadServiceAdminUser | Az Azure AD-bérlő globális rendszergazdai felhasználója. Ilyen lehet például vaasadmin@contoso.onmicrosoft.coma következő:. |
    | aadServiceAdminPassword | A globális rendszergazda felhasználó jelszava. |
    | AadTenantId | Azure AD-bérlő azonosítója az érvényesítés szolgáltatásként regisztrált Azure-fiókhoz. |
    | ExternalFqdn | A teljes tartománynevet a konfigurációs fájlból kérheti le. Útmutatásért tekintse [meg a gyakori munkafolyamat-paraméterek Azure stack érvényesítése szolgáltatásként](azure-stack-vaas-parameters.md)című témakört. |
    | Régió | Az Azure AD-bérlő régiója. |

A parancs letölti a nyilvános rendszerkép-tárház (PIR) rendszerképét (az operációs rendszer VHD-jét), és egy Azure Blob Storage-ból másolja a Azure Stack tárolóba.

![Előfeltételek letöltése](media/installingprereqs.png)

> [!Note]
> Ha lassú hálózati sebességet tapasztal a lemezképek letöltésekor, töltse le őket külön egy helyi megosztásra, és adja meg a **-LocalPackagePath** *FileShareOrLocalPath*paramétert. A PIR letöltésével kapcsolatos további útmutatást a következő témakörben talál: az [Érvényesítés](azure-stack-vaas-troubleshoot.md) [lassú hálózati kapcsolatának kezelése](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) szolgáltatásként.

## <a name="checks-before-starting-the-tests"></a>Ellenőrzések a tesztek megkezdése előtt

A tesztek távoli műveleteket futtatnak. A teszteket futtató gépnek hozzá kell férnie az Azure Stack-végpontokhoz, ellenkező esetben a tesztek nem fognak működni. Ha az az Varga helyi ügynököt használja, akkor használja az ügynököt futtató gépet. A következő ellenőrzések futtatásával ellenőrizheti, hogy a számítógépe rendelkezik-e hozzáféréssel a Azure Stack végpontokhoz:

1. Győződjön meg arról, hogy az alapszintű URI elérhető. Nyisson meg egy parancssort vagy bash-rendszerhéjat, és futtassa a `<EXTERNALFQDN>` következő parancsot, és cserélje le a-környezet külső teljes tartománynevére:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Nyisson meg egy webböngészőt, `https://adminportal.<EXTERNALFQDN>` és lépjen a lehetőségre, és győződjön meg arról, hogy a Mas portál elérhető.

3. Jelentkezzen be az Azure AD szolgáltatás-rendszergazda nevével és jelszavával, amely a tesztelési fázis létrehozásakor van megadva.

4. Ellenőrizze a rendszer állapotát a **test-AzureStack** PowerShell-parancsmag futtatásával a [Azure stack ellenőrzési teszt futtatása](../operator/azure-stack-diagnostic-test.md)című témakörben leírtak szerint. A tesztek elindítása előtt javítsa ki a figyelmeztetéseket és a hibákat.

## <a name="run-the-agent"></a>Az ügynök futtatása

1. Nyissa meg a Windows PowerShellt egy emelt szintű parancssorban.

2. Futtassa a következő parancsot:

    ```powershell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **Paraméterek**  

    | Paraméter | Leírás |
    | --- | --- |
    | VaaSUserId | Az contoso.com-portálra való bejelentkezéshez használt felhasználói azonosító (például username:\@Felhasználónév) |
    | VaaSTenantId | Azure AD-bérlő azonosítója az érvényesítés szolgáltatásként regisztrált Azure-fiókhoz. |

    > [!Note]  
    > Az ügynök futtatásakor az aktuális munkakönyvtárnak a Task Engine Host végrehajtható fájljának, a **Microsoft. VaaSOnPrem. TaskEngineHost. exe** helynek kell lennie.

Ha nem jelenik meg a jelentett hibák, akkor a helyi ügynök sikeres volt. A konzol ablakban a következő példában szereplő szöveg jelenik meg.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Ügynök elindítva](media/startedagent.png)

Az ügynököket a neve egyedileg azonosítja. Alapértelmezés szerint a a gép teljes tartománynevét (FQDN) használja, ahonnan az elindult. Kis méretűre kell állítani az ablakot, hogy elkerülje a véletlen kijelölést az ablakon, mivel a fókusz megváltoztatásával szünetelteti az összes többi műveletet.

## <a name="next-steps"></a>További lépések

- [A szolgáltatás érvényesítésének megoldása](azure-stack-vaas-troubleshoot.md)
- [Érvényesítés szolgáltatási kulcs fogalmai szerint](azure-stack-vaas-key-concepts.md)
- [Rövid útmutató: Az érvényesítés a szolgáltatás-portálon az első teszt beütemezhetés céljából](azure-stack-vaas-schedule-test-pass.md)