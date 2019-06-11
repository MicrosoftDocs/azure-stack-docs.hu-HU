---
title: Az Azure Stack érvényesítési szolgáltatásként kibocsátási megjegyzések |} A Microsoft Docs
description: Az Azure Stack érvényesítési szolgáltatásként kibocsátási megjegyzéseket.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 9360e8e5f4db14fbebba2642ed11ca2e3830bdf1
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828400"
---
# <a name="release-notes-for-validation-as-a-service"></a>Kibocsátási megjegyzések a szolgáltatás érvényesítése

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ez a cikk a kibocsátási megjegyzéseket tartalmaz az Azure Stack érvényesítési szolgáltatásként.

## <a name="version-405"></a>Verzió 4.0.5

2019 június 7

- Felhőbeli szimuláció motor a csomag érvényesítése munkafolyamat érvényesítési idejének lerövidítéséhez frissült:  
    Futási idő: Csökken, 6 óra  
    Verzió: 5.1.13.0 -> 5.1.22.0  


2019 17. január

- Lemez azonosítása teszt cím tárolási készlet inkonzisztenciához frissítése. Verzió: 5.1.14.0 -> 5.1.15.0
- Az Azure Stack havi frissítés ellenőrzés címet a frissített jóváhagyott szoftvereket és tartalmakat érvényesítési inkonzisztenciát. Verzió: 5.1.14.0 -> 5.1.17.0
- OEM kiterjesztésű csomag ellenőrzési frissítése előtt az Azure Stack-frissítés lépés szükséges ellenőrzések elvégzéséhez. Verzió: 5.1.14.0 -> 5.1.16.0
- Belső hibajavítások

## <a name="version-402"></a>Verzió 4.0.2

2019. január 7

Ha az Azure Stack havi frissítése ellenőrzési Munkafolyamat futtatja, és az OEM-frissítési csomag verziószáma nem 1810 vagy újabb, hiba kap, miután a számítógép-Gyártói frissítési lépést. Ez a hiba. A javítás fejlesztése folyamatban van. A kárenyhítési lépések a következők:

1. Futtassa az OEM frissítése a szokásos módon.
2. Hajtsa végre a Test-AzureStack csomag sikeres alkalmazás óta, és mentse a kimenetet.
3. A vizsgálat megszakítása
4. A mentett kimenetként VaaSHelp@microsoft.com a Futtatás eredményeit megadásának fogadásához.

## <a name="version-402"></a>Verzió 4.0.2

2018. November 30.

- Belső hibajavítások

## <a name="version-401"></a>Verzió 4.0.1

2018. október 8.

- VaaS előfeltételei

    `Install-VaaSPrerequisites` többé nem kell felhőalapú rendszergazdai hitelesítő adataival. Ha ez a parancsmag legújabb verzióját futtatja, lásd: [töltse le és telepítse az ügynököt](azure-stack-vaas-local-agent.md#download-and-install-the-agent) a módosított parancsok előfeltételek telepítése. Íme a parancsok:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>4.0.0-s verzió

2018. augusztus 29.

- VaaS Előfeltételek és a VHD-frissítések

    `Install-VaaSPrerequisites` Mostantól csak felhőalapú rendszergazdai hitelesítő adatok a probléma megoldására csomag érvényesítése során. A dokumentációban a [töltse le és telepítse az ügynököt](azure-stack-vaas-local-agent.md#download-and-install-the-agent) a következőre frissült:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > A `$CloudAdminCreds` szükséges, a parancsfájl által vannak az Azure stack-példányt kell kiértékelni. Azok nem a VaaS bérlő által használt Azure Active Directory hitelesítő adatokat.

- Helyi ügynök frissítése

    Az előző a helyi ügynök verziója nem kompatibilis a jelenlegi 4.0.0 verzióra a szolgáltatás kiadása. Minden felhasználó frissítenie kell a helyi ügynökök. Lásd: [töltse le és telepítse az ügynököt](azure-stack-vaas-local-agent.md#download-and-install-the-agent) a legújabb ügynök telepítésével kapcsolatos utasításokat.

- PowerShell automation update

    Módosítások történtek `LaunchVaaSTests` PowerShell-parancsfájlok, amelyek a parancsfájl-kezelési csomagok legújabb verziója szükséges. Lásd: [indítsa el a Tesztmenetek munkafolyamat](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow) a legújabb verzióra a parancsfájl-kezelési csomag telepítésével kapcsolatos utasításokat.

- Ellenőrzés, a portálon

  - Csomagaláíró értesítések

    A csomag érvényesítése munkafolyamat részeként egy számítógép-Gyártói testreszabási csomag elküldésekor a csomag formátuma érvényesíti annak érdekében, hogy követi-e a közzétett specifikációnak. Ha a csomag nem felel meg, a futtatás sikertelen lesz. A bérlő regisztrált az Azure Active Directory kapcsolattartó e-mail-címre a e-mail értesítéseket küldeni szeretné.

  - Interaktív vizsgálati kategória

    A **interaktív** vizsgált kategória hozzá lett adva. Ezek a tesztek interaktív, az nem automatikus az Azure Stack-forgatókönyvekhez.

  - Interaktív funkció ellenőrzése

    Az egyes szolgáltatások célzott visszajelzés biztosítani már elérhető a Tesztmenetek munkafolyamatban. A `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` teszt ellenőrzi, hogy ha adott frissítések alkalmazása megfelelő volt, és ezután gyűjti a visszajelzéseket.

## <a name="next-steps"></a>További lépések

- [Érvényesítési szolgáltatás hibaelhárítása](azure-stack-vaas-troubleshoot.md)
