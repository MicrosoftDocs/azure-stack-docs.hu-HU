---
title: Az érvényesítés Azure Stack szolgáltatás kibocsátási megjegyzései | Microsoft Docs
description: Azure Stack érvényesítése szolgáltatás kibocsátási megjegyzésként.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: d42b1f161d1b74633c4e9924e45677a57b3493e1
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277817"
---
# <a name="release-notes-for-validation-as-a-service"></a>Kibocsátási megjegyzések szolgáltatásként történő érvényesítéshez

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ez a cikk a Azure Stack érvényesítésére szolgáló kibocsátási megjegyzésekkel szolgál.

## <a name="version-405"></a>4\.0.5 verziója

2019 június 7.

- A csomag-ellenőrzési munkafolyamatban a felhőalapú szimulációs motor frissült az érvényesítési idő felgyorsításához:  
    Futási idő: 6 órán át csökkentve  
    Verzió: 5.1.13.0-> 5.1.22.0  


2019 január 17

- A lemez-azonosítási teszt frissítve lett a Storage-készlet következetlenségei között. Verzió: 5.1.14.0-> 5.1.15.0
- Azure Stack a havi frissítés ellenőrzése frissült a jóváhagyott szoftverek és a tartalom-ellenőrzési következetlenségek kezeléséhez. Verzió: 5.1.14.0-> 5.1.17.0
- Az OEM-bővítmények ellenőrzése a Azure Stack Update lépés előtt frissült a szükséges ellenőrzések végrehajtásához. Verzió: 5.1.14.0-> 5.1.16.0
- Belső hibajavítások

## <a name="version-402"></a>4\.0.2 verziója

2019 január 7

Ha a Azure Stack havi frissítés-ellenőrzési munkafolyamatot futtatja, és az OEM-frissítési csomag verziószáma nem 1810 vagy magasabb, hibaüzenet jelenik meg, ha megkapja az OEM-frissítés lépéseit. Ez egy hiba. Javítás fejlesztése folyamatban van. A kockázatcsökkentő lépések a következők:

1. Futtassa az OEM-frissítést a szokásos módon.
2. Tesztelje a AzureStack a csomag sikeres alkalmazása után, és mentse a kimenetet.
3. A teszt megszakítása.
4. Küldje el a mentett kimenetet VaaSHelp@microsoft.com értékre a futtatási eredmények fogadásához.

## <a name="version-402"></a>4\.0.2 verziója

2018 november 30

- Belső hibajavítások

## <a name="version-401"></a>4\.0.1 verzió

2018 október 8

- Az alapkövetelmények

    @no__t – 0 már nem igényel Felhőbeli rendszergazdai hitelesítő adatokat. Ha a parancsmag legújabb verzióját futtatja, az előfeltételek telepítéséhez tekintse meg az [ügynök letöltése és telepítése](azure-stack-vaas-local-agent.md#download-and-install-the-agent) című témakört a felülvizsgált parancsokhoz. Íme a parancsok:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>4\.0.0-s verzió

2018 augusztus 29.

- Az előfeltételeket és a VHD-frissítéseket

    @no__t – 0 most megköveteli a Felhőbeli rendszergazdai hitelesítő adatok megadását a csomagok ellenőrzése során felmerülő problémák megoldásához. Az [ügynök letöltésére és telepítésére](azure-stack-vaas-local-agent.md#download-and-install-the-agent) szolgáló dokumentáció a következővel frissült:

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
    > A parancsfájl által igényelt @no__t – 0 az érvényesített Azure Stack-példányra vonatkozóan szükséges. Nem az a Azure Active Directory hitelesítő adat, amelyet az alaprendszer-bérlő használ.

- Helyi ügynök frissítése

    A helyi ügynök előző verziója nem kompatibilis a szolgáltatás aktuális 4.0.0-kiadásával. Minden felhasználónak frissítenie kell a helyi ügynököket. A legújabb ügynök telepítésével kapcsolatos utasításokért tekintse meg [az ügynök letöltése és telepítése](azure-stack-vaas-local-agent.md#download-and-install-the-agent) című témakört.

- PowerShell Automation frissítése

    Olyan `LaunchVaaSTests` PowerShell-parancsfájlok módosításai történtek, amelyek a parancsfájl-csomagok legújabb verzióját igénylik. A parancsfájl-kezelési csomag legújabb verziójának telepítésével kapcsolatos utasításokért tekintse meg [a test pass munkafolyamat elindítása](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow) című témakört.

- Érvényesítés szolgáltatás-portálként

  - Csomagok aláírásával kapcsolatos értesítések

    Ha a csomag-ellenőrzési munkafolyamat részeként elküld egy OEM testreszabási csomagot, a rendszer érvényesíti a csomag formátumát, hogy az megfeleljen a közzétett specifikációnak. Ha a csomag nem felel meg a követelményeknek, a Futtatás sikertelen lesz. A rendszer e-mail-értesítéseket küld a bérlő regisztrált Azure Active Directory kapcsolattartójának e-mail-címére.

  - Interaktív tesztelési kategória

    Az **interaktív** teszt kategóriája hozzá lett adva. Ezek a tesztek interaktív, nem automatizált Azure Stack-forgatókönyveket gyakorolnak.

  - Interaktív funkció ellenőrzése

    Az egyes funkciókra vonatkozó célzott visszajelzések megadásának lehetősége már elérhető a test pass munkafolyamatban. A `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` teszt ellenőrzi, hogy vannak-e konkrét frissítések alkalmazva, majd visszajelzéseket gyűjt.

## <a name="next-steps"></a>Következő lépések

- [A szolgáltatás érvényesítésének megoldása](azure-stack-vaas-troubleshoot.md)
