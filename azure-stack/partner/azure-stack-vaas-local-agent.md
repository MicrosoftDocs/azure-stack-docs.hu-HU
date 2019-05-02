---
title: A helyi ügynök telepítése |} A Microsoft Docs
description: Szolgáltatásként az Azure Stack érvényesítési a helyi ügynök telepítése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1e194583b583bfc442a3c7b99a842ee788fc423c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "64298942"
---
# <a name="deploy-the-local-agent"></a>A helyi ügynök telepítése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ismerje meg, az érvényesítési használata a Service (VaaS) helyi ügynök Érvényesítési tesztek futtatásához. A helyi ügynök ellenőrző tesztek futtatása előtt kell telepíteni.

> [!Note]  
> Győződjön meg arról, hogy a gép, amelyen fut a helyi ügynök nem veszíti el kimenő hozzáférést az internethez. Lehet, hogy a gép csak a nevében a bérlő VaaS használatára jogosult felhasználók számára elérhető.

A helyi ügynök telepítéséhez:

1. A helyi ügynök telepítése.
2. Hajtsa végre a megerősítések.
3. A helyi ügynök futtatásával.

## <a name="download-and-start-the-local-agent"></a>Töltse le és indítsa el a helyi ügynök

Töltse le az ügynök olyan gépre, amely megfelel az előfeltételeknek, a helyi adatközpontban, és az Azure Stack-végpontokra irányuló hozzáféréssel rendelkezik. Ezt a gépet nem lehet az Azure Stack rendszer része, vagy az Azure Stack-felhő üzemeltet.

### <a name="machine-prerequisites"></a>Gép előfeltételei

Ellenőrizze, hogy a gép megfelel-e a következő feltételeknek:

- Az összes Azure Stack-végpontra való hozzáférés
- .NET 4.6 és a PowerShell 5.0-s telepítése
- Legalább 8 GB RAM
- Legalább 8 processzormagokkal
- 200 GB-os minimális szabad lemezterület
- Állandó hálózati kapcsolat az internethez

### <a name="download-and-install-the-agent"></a>Az ügynök letöltése és telepítése

1. Nyisson meg egy rendszergazda jogú parancssort a gépen a tesztek futtatásához használhat Windows PowerShell.
2. Futtassa a következő parancsot a helyi ügynök letöltéséhez:

    ```powershell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Futtassa a következő parancsot a helyi ügynök függőségek telepítéséhez:

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
    | aadServiceAdminUser | A globális rendszergazda az Azure AD-bérlője számára. Elképzelhető például vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | A globális rendszergazda jelszava. |
    | AadTenantId | Az Azure AD-bérlő azonosítója az Azure-fiók regisztrálva van szolgáltatásként érvényesítése. |
    | ExternalFqdn | A konfigurációs fájlban szereplő kaphat a teljes tartománynevét. További útmutatást lásd: [szolgáltatásként az Azure Stack érvényesítési általános munkafolyamat-paraméterek](azure-stack-vaas-parameters.md). |
    | Régió | Az Azure AD-bérlő régiója. |

A parancs letölti egy nyilvános rendszerkép tárház (PIR) rendszerkép (operációs rendszer VHD), és másolja egy Azure blob storage-ból az Azure Stack storage.

![Előfeltételek letöltése](media/installingprereqs.png)

> [!Note]
> Ha a hálózat lassúsága tapasztal, ezek a lemezképek letöltésekor, külön-külön letölteni azokat egy helyi megosztással, és adja meg a paramétert **- LocalPackagePath** *FileShareOrLocalPath*. Annak további útmutatást a PIR letöltési szakaszában [leírók lassú hálózati kapcsolat](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) , [szolgáltatás hibaelhárítása érvényesítési](azure-stack-vaas-troubleshoot.md).

## <a name="checks-before-starting-the-tests"></a>A vizsgálatok elindítása előtt ellenőrzi

A tesztek futtatása távoli műveletek. A gép, amely futtatja a teszteket az Azure Stack-végpontok hozzáféréssel kell rendelkeznie, különben a tesztek nem fog működni. Ha a VaaS helyi ügynök használ, használja a gép ahol az ügynök futni fog. Ellenőrizheti, hogy a gép az Azure Stack-végpontok hozzáféréssel rendelkezik a következő ellenőrzések futtatásával:

1. Ellenőrizze, hogy az alap URI-t érhető el. Nyisson meg egy parancssort vagy a bash rendszerhéjat, és futtassa a következő parancsot, és cserélje le `<EXTERNALFQDN>` az a környezet külső teljes Tartománynevét:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Nyisson meg egy webböngészőt, majd `https://adminportal.<EXTERNALFQDN>` annak érdekében, hogy ellenőrizze, hogy a MAS-portálon érhető el.

3. Jelentkezzen be az Azure ad szolgáltatás a tesztelési fázisban létrehozásakor megadott rendszergazdai és a jelszó értékét.

4. A rendszer állapotának ellenőrzéséhez futtassa a **Test-AzureStack** leírtak szerint PowerShell-parancsmag [egy ellenőrző teszt futtatása az Azure Stack](../operator/azure-stack-diagnostic-test.md). Javítsa ki a figyelmeztetések és hibák bármilyen vizsgálatok elindítása előtt.

## <a name="run-the-agent"></a>Az ügynök futtatása

1. Nyissa meg a Windows Powershellt rendszergazda jogú parancssorban.

2. Futtassa az alábbi parancsot:

    ```powershell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **Paraméterek**  

    | Paraméter | Leírás |
    | --- | --- |
    | VaaSUserId | Felhasználói azonosító segítségével jelentkezzen be a VaaS portálra (például felhasználónév\@Contoso.com) |
    | VaaSTenantId | Az Azure AD-bérlő azonosítója az Azure-fiók regisztrálva van szolgáltatásként érvényesítése. |

    > [!Note]  
    > Az ügynök futtatásakor az aktuális munkakönyvtárban kell lennie a végrehajtható fájl, tevékenység motor gazdagép helyét **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Ha nem látja a jelentett hibákat, majd a helyi ügynök sikeres volt. A konzolablakban megjelenik az alábbi példa szöveget.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Az ügynök elindítva](media/startedagent.png)

Az ügynök egyedileg azonosítja a neve. Alapértelmezés szerint a teljesen minősített neve (FQDN) nevét, ahol lett elindítva a gép használja. Kell minimálisra csökkenthető az ablakban bármely véletlen kiválasztja az ablakot, a elkerülése érdekében minden egyéb művelet módosítja a fókuszt szünetelteti.

## <a name="next-steps"></a>További lépések

- [Érvényesítési szolgáltatás hibaelhárítása](azure-stack-vaas-troubleshoot.md)
- [Mint szolgáltatásra kulcs érvényesítése](azure-stack-vaas-key-concepts.md)
- [Rövid útmutató: Az érvényesítés használja egy portált, az első vizsgálat ütemezése](azure-stack-vaas-schedule-test-pass.md)