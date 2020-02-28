---
title: Érvényesítés szolgáltatás kibocsátási megjegyzései
titleSuffix: Azure Stack Hub
description: A Azure Stack hub érvényesítésének kibocsátási megjegyzései szolgáltatásként.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: da111d514946a3f4e28783c004778ce1d88c937d
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704590"
---
# <a name="release-notes-for-validation-as-a-service"></a>Kibocsátási megjegyzések szolgáltatásként történő érvényesítéshez

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ez a cikk a Azure Stack hub szolgáltatásként történő érvényesítésének kibocsátási megjegyzéseit ismerteti.

## <a name="version-4421"></a>4\.4.2.1 verziója

2020 január 9

- Tartalmi frissítések tesztelése:
  - OEM-ellenőrzési munkafolyamat (verzió: 5.1.52.0-> 5.1.53.0): a szükséges paraméterek számának csökkentése a teszt ütemterve ablaktáblán.
  - Hibajavítás a számítási teszthez – TestVMOperations

- Ismert problémák:
  - Vegye fel a kapcsolatot vaashelp@microsoft.com ha a következő tesztelési esetek nem futnak az OEM-ellenőrzési munkafolyamat során:
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk

2019 december 3

- Tartalmi frissítések tesztelése:
  - A havi Azure Stack hub frissítési munkafolyamatának és az OEM-csomag érvényesítési munkafolyamatának online dokumentációja frissült. Tekintse át a frissített dokumentációt az [OEM-csomagok ellenőrzése](azure-stack-vaas-validate-oem-package.md) és a Microsofttól származó [szoftverfrissítések ellenőrzése](azure-stack-vaas-validate-microsoft-updates.md) című dokumentumban.
  - Az alaprendszer-csomag érvényesítési munkafolyamatának frissítése: OEM ellenőrzési munkafolyamat az egyetlen teszt szükséges a havi Azure Stack hub Update ellenőrzéséhez és az OEM-csomagok ellenőrzéséhez. A teszt frissíti a bélyegzőt a megadott AzureStack/OEM-csomagokkal, és futtatja a felhőalapú szimulációs motor ellenőrzési tesztjét.
  - Az Varga PowerShell bővítmény frissítése: a csomag-ellenőrzési munkafolyamat automatizálása mostantól támogatott. A bővítmény használatára vonatkozó részletes információkért tekintse meg Azure Stack hub Varga automatizálása a PowerShell-lel című témakört.

- Ismert problémák:
  - Vegye fel a kapcsolatot vaashelp@microsoft.com ha a következő tesztelési esetek nem futnak az OEM-ellenőrzési munkafolyamat során:
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk


## <a name="version-4353"></a>4\.3.5.3 verziója

2019 november 7.

- Tartalmi frissítések tesztelése:
  - Havi Azure Stack hub frissítésének ellenőrzése (verzió: 5.1.46.0-> 5.1.49.0).
  - OEM kiterjesztési csomag ellenőrzése (5.1.46.0-> 5.1.49.0).
  - A 5.1.46.0 eredményei megmaradtak. Ha sikeresen futtatta a 5.1.46.0-t, értesítse vaashelp@microsoft.com az eredmények elküldésekor.

- Hibajavítások
  - Kijavítva a hiba, ahol a Azure Stack hub frissítésének ellenőrzése nem sikerült, ha az Update. zip speciális karaktereket tartalmaz.

- Ismert problémák
  - Az mstest. exe nem található. Megkerülő megoldás:
    1. CTRL + C az ügynök a PowerShell-ablakban.
    1. Írja be a mstest. exe fájlt annak ellenőrzéséhez, hogy a mstest. exe egy felismert program.
    1. Ha a mstest. exe nem ismerhető fel, akkor a jelenlegi PowerShell-ablak bezárásához.
    1. Kattintson a Start gombra (ne a PowerShell elemre a tálcán), keresse meg a PowerShellt, és nyissa meg rendszergazdaként.
    1. Írja be a mstest. exe parancsot, és ellenőrizze, hogy az elérhető-e parancsként.
    1. Indítsa újra az ügynököt, és futtassa újra a tesztet.
  - Alkalmanként a Cloud szimulációs motor \*VM-tesztekkel kapcsolatos hibákat fog jelenteni. Az újrafuttatás megkísérlése előtt lépjen kapcsolatba vaashelp@microsoft.com.


2019 október 29.

- A havi Azure Stack hub frissítési munkafolyamatának és az OEM-csomag érvényesítési munkafolyamatának online dokumentációja frissült.

    Tekintse át a frissített dokumentációt az [OEM-csomagok ellenőrzése](azure-stack-vaas-validate-oem-package.md) és a Microsofttól származó [szoftverfrissítések ellenőrzése](azure-stack-vaas-validate-microsoft-updates.md) című dokumentumban.
- Varga munkafolyamat frissítése: havonta Azure Stack hub frissítése (5.1.30.0-> 5.1.46.0) – a havi Azure Stack hub Update ellenőrzési teszt munkafolyamata frissítve lett.

    A munkafolyamatnak már nincs manuális beavatkozása, és a ütemezése zökkenőmentesen futtatható.
- Varga munkafolyamat frissítése: OEM-csomag ellenőrzése (5.1.30.0-> 5.1.46.0) – az OEM-csomag érvényesítési munkafolyamata frissítve lett.

    A munkafolyamatnak már nincs manuális beavatkozása, és a ütemezése zökkenőmentesen futtatható.
- Az OEM-csomag érvényesítési munkafolyamatában (5.1.30.0-> 5.1.46.0) található felhőalapú szimulációs motor frissült az érvényesítési idő meggyorsítása érdekében: futási idő 1 órára csökken.
- A felhőalapú szimulációs motor az OEM-csomag érvényesítési munkafolyamatában és az Azure Stack hub frissítési munkafolyamatában (5.1.30.0-> 5.1.46.0) megkövetelheti, hogy a frissítések a gyermek mappákban található többi frissítés nélkül két különböző szülő mappában legyenek érvényesítve.
- A felhőalapú szimulációs motor az OEM-csomag érvényesítési munkafolyamatában és az Azure Stack hub frissítési munkafolyamatában (5.1.30.0-> 5.1.46.0) megköveteli, hogy a tesztek a következő sorrendben legyenek ütemezve: havonta Azure Stack hub Update ellenőrzési teszt, OEM-bővítmény Csomag-ellenőrzési teszt, és végül a Cloud szimulációs motor.
- Varga-ügynök frissítése: a frissített Varga ügynök mostantól a Azure Stack hub felhőalapú rendszergazdai hitelesítő adataival kérdezi le a bélyegzőt, hogy lekérdezze a bélyegző adatait, hogy automatikusan töltse fel a munkafolyamatokat.

    Ehhez a frissítéshez az összes ügynök frissítése és újraindítása szükséges. A következő utasításokat követve frissítheti az alábbi útmutatókat: https://docs.microsoft.com/azure-stack/partner/azure-stack-vaas-local-agent
- Az Varga portál felhasználói felületének frissítése: az ügynök kiválasztása tábla a tesztek ütemezése ablaktábla fölé került, hogy megkönnyítse a tesztelést.

    A feladatok ütemezésekor már nem szükséges megadnia a Stamp-adatokat, ha az adatbázis-ügynökök megfelelően frissültek.

## <a name="version-405"></a>4\.0.5 verziója

2019 június 7.

- A csomag-ellenőrzési munkafolyamatban a felhőalapú szimulációs motor frissült az érvényesítési idő felgyorsításához:  
    Futási idő: 6 órán át csökkentve  
    Verzió: 5.1.13.0-> 5.1.22.0  


2019 január 17

- A lemez-azonosítási teszt frissítve lett a Storage-készlet következetlenségei között. Verzió: 5.1.14.0-> 5.1.15.0
- Azure Stack hub havi frissítésének ellenőrzése frissítve a jóváhagyott szoftverek és a tartalom-ellenőrzési következetlenségek kezeléséhez. Verzió: 5.1.14.0-> 5.1.17.0
- Az OEM-bővítmények ellenőrzése a Azure Stack hub frissítésének lépését megelőzően a szükséges ellenőrzések végrehajtásához lett frissítve. Verzió: 5.1.14.0-> 5.1.16.0
- Belső hibajavítások.

## <a name="version-403"></a>4\.0.3 verziója

2019 január 7

Ha az Azure Stack hub havi frissítés-ellenőrzési munkafolyamatát futtatja, és az OEM-frissítési csomag verziószáma nem 1810 vagy újabb, hibaüzenet jelenik meg, ha megkapja az OEM-frissítés lépését. Ez a hiba egy hiba. Javítás fejlesztése folyamatban van. A kockázatcsökkentő lépések a következők:

1. Futtassa az OEM-frissítést a szokásos módon.
2. Tesztelje a AzureStack a csomag sikeres alkalmazása után, és mentse a kimenetet.
3. A teszt megszakítása.
4. Küldje el VaaSHelp@microsoft.com a mentett kimenetet, hogy megkapja a Futtatás eredményét.

## <a name="version-402"></a>4\.0.2 verziója

2018 november 30

- Belső hibajavítások.

## <a name="version-401"></a>4\.0.1 verzió

2018 október 8

- Az alapkövetelmények:

    `Install-VaaSPrerequisites` többé nem szükségesek a Felhőbeli rendszergazdai hitelesítő adatok. Ha a parancsmag legújabb verzióját futtatja, tekintse meg a [helyi ügynök letöltése és telepítése](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) című témakört az előfeltételek telepítéséhez szükséges átdolgozott parancsokhoz. Íme a parancsok:

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

- Az előfeltételeket és a VHD-frissítéseket:

    `Install-VaaSPrerequisites` most a Felhőbeli rendszergazdai hitelesítő adatokkal kell rendelkeznie a csomagok ellenőrzése során felmerülő problémák megoldásához. A [helyi ügynök letöltésére és telepítésére](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) szolgáló dokumentáció a következő kóddal frissült:

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
    > A parancsfájl által igényelt `$CloudAdminCreds` a Azure Stack hub-példány ellenőrzése alatt áll. Nem az a Azure Active Directory hitelesítő adat, amelyet az Varga bérlő használ.

- Helyi ügynök frissítése:

    A helyi ügynök előző verziója nem kompatibilis a szolgáltatás aktuális 4.0.0-kiadásával. Minden felhasználónak frissítenie kell a helyi ügynököket. A legújabb ügynök telepítésével kapcsolatos utasításokért tekintse meg [a helyi ügynök letöltése és telepítése](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) című témakört.

- PowerShell Automation frissítése:

    Olyan `LaunchVaaSTests` PowerShell-parancsfájlok módosítása történt, amelyek a parancsfájl-csomagok legújabb verzióját igénylik. A parancsfájl-kezelési csomag legújabb verziójának telepítésével kapcsolatos utasításokért tekintse meg [a test pass munkafolyamat elindítása](azure-stack-vaas-automate-with-powershell.md) című témakört.

- Érvényesítés szolgáltatás-portálként:

  - Csomagok aláírásával kapcsolatos értesítések

    Ha a csomag-ellenőrzési munkafolyamat részeként elküld egy OEM testreszabási csomagot, a rendszer érvényesíti a csomag formátumát, hogy az megfeleljen a közzétett specifikációnak. Ha a csomag nem felel meg a követelményeknek, a Futtatás sikertelen lesz. A rendszer e-mail-értesítéseket küld a bérlő regisztrált Azure Active Directory kapcsolattartójának e-mail-címére.

  - Interaktív teszt kategóriája:

    Az **interaktív** teszt kategóriája hozzá lett adva. Ezek a tesztek interaktív, nem automatizált Azure Stack hub-forgatókönyveket gyakorolnak.

  - Interaktív funkció ellenőrzése:

    Az egyes funkciókra vonatkozó célzott visszajelzések megadásának lehetősége már elérhető a test pass munkafolyamatban. A `OEM Update on Azure Stack Hub 1806 RC Validation 5.1.4.0` teszt ellenőrzi, hogy az adott frissítések megfelelően lettek-e alkalmazva, majd visszajelzéseket gyűjt.

## <a name="next-steps"></a>Következő lépések

- [A szolgáltatás érvényesítésének megoldása](azure-stack-vaas-troubleshoot.md)
