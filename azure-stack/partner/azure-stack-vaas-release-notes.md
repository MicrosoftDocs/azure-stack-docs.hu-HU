---
title: Érvényesítés szolgáltatás kibocsátási megjegyzései
titleSuffix: Azure Stack Hub
description: A Azure Stack hub érvényesítésének kibocsátási megjegyzései szolgáltatásként.
author: mattbriggs
ms.topic: article
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 780c6a2e0f3235e2681ba4cf6cc01ce2f13eb3dc
ms.sourcegitcommit: 75a2e1a52d7582e26ce8eaf37a470c62f99b4da0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94580810"
---
# <a name="release-notes-for-validation-as-a-service"></a>Kibocsátási megjegyzések szolgáltatásként történő érvényesítéshez

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ez a cikk a Azure Stack hub szolgáltatásként történő érvényesítésének kibocsátási megjegyzéseit ismerteti.

## <a name="version-4432"></a>4.4.3.2 verziója

2020 február 2.

- Szolgáltatás-karbantartási frissítések.

- Hibajavítások
  - Rögzített tesztek Test101LinuxEmptyAttachedDiskManagedDisk, Test101WindowsEmptyAttachedDiskManagedDisk.

## <a name="version-4442"></a>4.4.4.2 verziója

2020 november 11

- A rendszer frissítette az ÜGYFÉLOLDALI szolgáltatás-ellenőrzési munkafolyamatot, hogy a teszt aláírt OEM-bővítmény csomagot automatikusan a teljes AzureStack-frissítés után telepítse.
  - A javítás előtt az AzureStack teljes frissítés után nem sikerül telepítenie egy Test-Signed OEM-bővítményt a bélyegzőn. Az Varga AzureStack-frissítést alkalmaz, majd kizárja a futtatást.
  - Ez meg lett javítva, és látnia kell, hogy a megadott AzureStack Update és test-aláírt OEM-bővítmény csomag telepítése ÜGYFÉLOLDALI ellenőrzési munkafolyamatot tartalmaz.
- OEM-csomag érvényesítési bővítményének hozzáadva az "OEM-ellenőrzési munkafolyamathoz"
  - Ez a bővítmény a bélyegző frissítéseinek kirúgása előtt fog futni.
  - A bővítmény ellenőrzi az OEM-bővítmény csomagjának tartalmát és a oemMetadata.xml elemeit.
  - Ha bármilyen hiba/probléma merült fel az OEM-bővítmény csomaggal kapcsolatban, azt a rendszer beolvassa, mielőtt az első lépések elindulnak.
  - Ezek az érvényesítések a csomag aláírásának időpontjában futottak, és az alapszolgáltatások tesztelését követően futtatták.  
- A AzureStack és a AzureRM PowerShell-modulok újabb verziójának telepítéséhez a reqs előzetes verziója frissítve lett
  - AzureStack PS-modul verziója 1.8.2
  - AzureRM PS-modul 2.5.0-es verzió
- Másodlagos szolgáltatások frissítései.

## <a name="version-443112"></a>4.4.3.112 verziója

2020 augusztus 23

- Szolgáltatás frissítései.
  - A szolgáltatás központi telepítésének frissítései.
  - A szolgáltatás hitelesítési módszereinek frissítése megtörtént.

## <a name="version-44368"></a>4.4.3.68 verziója

2020 június 30

- Szolgáltatás frissítései.
  - Áthelyezte a szolgáltatást Service Fabric-ben való futtatásra.

## <a name="version-4421"></a>4.4.2.1 verziója

2020 január 9

- Tartalmi frissítések tesztelése:
  - OEM-ellenőrzési munkafolyamat (verzió: 5.1.52.0-> 5.1.53.0): a szükséges paraméterek számának csökkentése a teszt ütemterve ablaktáblán.
  - Hibajavítás a számítási teszthez – TestVMOperations

- Ismert problémák:
  - Vegye fel vaashelp@microsoft.com a kapcsolatot, ha az alábbi tesztelési esetek nem futnak az OEM-ellenőrzési munkafolyamat során:
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk

2019 december 3

- Tartalmi frissítések tesztelése:
  - A havi Azure Stack hub frissítési munkafolyamatának és az OEM-csomag érvényesítési munkafolyamatának online dokumentációja frissült. Tekintse át a frissített dokumentációt az [OEM-csomagok ellenőrzése](azure-stack-vaas-validate-oem-package.md) és a Microsofttól származó [szoftverfrissítések ellenőrzése](azure-stack-vaas-validate-microsoft-updates.md) című dokumentumban.
  - Az alaprendszer-csomag érvényesítési munkafolyamatának frissítése: OEM ellenőrzési munkafolyamat az egyetlen teszt szükséges a havi Azure Stack hub Update ellenőrzéséhez és az OEM-csomagok ellenőrzéséhez. A teszt frissíti a bélyegzőt a megadott AzureStack/OEM-csomagokkal, és futtatja a felhőalapú szimulációs motor ellenőrzési tesztjét.
  - Az Varga PowerShell bővítmény frissítése: a csomag-ellenőrzési munkafolyamat automatizálása mostantól támogatott. A bővítmény használatára vonatkozó részletes információkért tekintse meg Azure Stack hub Varga automatizálása a PowerShell-lel című témakört.

- Ismert problémák:
  - Vegye fel vaashelp@microsoft.com a kapcsolatot, ha az alábbi tesztelési esetek nem futnak az OEM-ellenőrzési munkafolyamat során:
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk

## <a name="version-4353"></a>4.3.5.3 verziója

2019 november 7.

- Tartalmi frissítések tesztelése:
  - Havi Azure Stack hub frissítésének ellenőrzése (verzió: 5.1.46.0-> 5.1.49.0).
  - OEM kiterjesztési csomag ellenőrzése (5.1.46.0-> 5.1.49.0).
  - A 5.1.46.0 eredményei megmaradtak. Ha sikeresen futtatta a 5.1.46.0-t, akkor az vaashelp@microsoft.com eredmények elküldésekor értesítést kap.

- Hibajavítások
  - Kijavítva a hiba, ahol a Azure Stack hub frissítésének ellenőrzése nem sikerült, ha az Update. zip speciális karaktereket tartalmaz.

- Ismert problémák
  - Ha mstest.exe nem található, a rendszer nem ellenőrzi az alapkonfigurációt. Megkerülő megoldás:
    1. CTRL + C az ügynök a PowerShell-ablakban.
    1. A mstest.exe beírásával ellenőrizze, hogy a mstest.exe felismert program-e.
    1. Ha mstest.exe nem ismerhető fel, akkor a jelenlegi PowerShell-ablak bezárásához.
    1. Kattintson a Start gombra (ne a PowerShell elemre a tálcán), keresse meg a PowerShellt, és nyissa meg rendszergazdaként.
    1. Írja be mstest.exe, és ellenőrizze, hogy elérhető-e parancsként.
    1. Indítsa újra az ügynököt, és futtassa újra a tesztet.
  - Időnként a Cloud szimulációs motor a virtuális gépekkel kapcsolatos tesztek során hibát jelez \* . vaashelp@microsoft.comAz újrafuttatási kísérlet előtt lépjen kapcsolatba.


2019 október 29.

- A havi Azure Stack hub frissítési munkafolyamatának és az OEM-csomag érvényesítési munkafolyamatának online dokumentációja frissült.

    Tekintse át a frissített dokumentációt az [OEM-csomagok ellenőrzése](azure-stack-vaas-validate-oem-package.md) és a Microsofttól származó [szoftverfrissítések ellenőrzése](azure-stack-vaas-validate-microsoft-updates.md) című dokumentumban.
- Varga munkafolyamat frissítése: havonta Azure Stack hub frissítése (5.1.30.0-> 5.1.46.0) – a havi Azure Stack hub Update ellenőrzési teszt munkafolyamata frissítve lett.

    A munkafolyamatnak már nincs manuális beavatkozása, és a ütemezése zökkenőmentesen futtatható.
- Varga munkafolyamat frissítése: OEM-csomag ellenőrzése (5.1.30.0-> 5.1.46.0) – az OEM-csomag érvényesítési munkafolyamata frissítve lett.

    A munkafolyamatnak már nincs manuális beavatkozása, és a ütemezése zökkenőmentesen futtatható.
- Az OEM-csomag érvényesítési munkafolyamatában (5.1.30.0-> 5.1.46.0) található felhőalapú szimulációs motor frissült az érvényesítési idő meggyorsítása érdekében: futási idő 1 órára csökken.
- A felhőalapú szimulációs motor az OEM-csomag érvényesítési munkafolyamatában és az Azure Stack hub frissítési munkafolyamatában (5.1.30.0-> 5.1.46.0) megkövetelheti, hogy a frissítések a gyermek mappákban található többi frissítés nélkül két különböző szülő mappában legyenek érvényesítve.
- A felhőalapú szimulációs motor az OEM-csomag érvényesítési munkafolyamatában és az Azure Stack hub frissítési munkafolyamatában (5.1.30.0-> 5.1.46.0) megköveteli, hogy a tesztek a következő sorrendben legyenek ütemezve: havonta Azure Stack hub Update ellenőrzési teszt, OEM-bővítmény-ellenőrzési teszt, és végül a Cloud szimulációs motor.
- Varga-ügynök frissítése: a frissített Varga ügynök mostantól a Azure Stack hub felhőalapú rendszergazdai hitelesítő adataival kérdezi le a bélyegzőt, hogy lekérdezze a bélyegző adatait, hogy automatikusan töltse fel a munkafolyamatokat.

    Ehhez a frissítéshez az összes ügynök frissítése és újraindítása szükséges. A következő utasításokat követve frissítheti az Varga-ügynököt: https://docs.microsoft.com/azure-stack/partner/azure-stack-vaas-local-agent
- Az Varga portál felhasználói felületének frissítése: az ügynök kiválasztása tábla a tesztek ütemezése ablaktábla fölé került, hogy megkönnyítse a tesztelést.

    A feladatok ütemezésekor már nem szükséges megadnia a Stamp-adatokat, ha az adatbázis-ügynökök megfelelően frissültek.

## <a name="version-405"></a>4.0.5 verziója

2019 június 7.

- A csomag-ellenőrzési munkafolyamatban a felhőalapú szimulációs motor frissült az érvényesítési idő felgyorsításához:  
    Futási idő: 6 órán át csökkentve  
    Verzió: 5.1.13.0-> 5.1.22.0  


2019 január 17

- A lemez-azonosítási teszt frissítve lett a Storage-készlet következetlenségei között. Verzió: 5.1.14.0-> 5.1.15.0
- Azure Stack hub havi frissítésének ellenőrzése frissítve a jóváhagyott szoftverek és a tartalom-ellenőrzési következetlenségek kezeléséhez. Verzió: 5.1.14.0-> 5.1.17.0
- Az OEM-bővítmények ellenőrzése a Azure Stack hub frissítésének lépését megelőzően a szükséges ellenőrzések végrehajtásához lett frissítve. Verzió: 5.1.14.0-> 5.1.16.0
- Belső hibajavítások.

## <a name="version-403"></a>4.0.3 verziója

2019 január 7

Ha az Azure Stack hub havi frissítés-ellenőrzési munkafolyamatát futtatja, és az OEM-frissítési csomag verziószáma nem 1810 vagy újabb, hibaüzenet jelenik meg, ha megkapja az OEM-frissítés lépését. Ez a hiba egy hiba. Javítás fejlesztése folyamatban van. A kockázatcsökkentő lépések a következők:

1. Futtassa az OEM-frissítést a szokásos módon.
2. Test-AzureStack végrehajtása a csomag sikeres alkalmazása és a kimenet mentése után.
3. A teszt megszakítása.
4. Küldje el a mentett kimenetet, VaaSHelp@microsoft.com hogy megkapja a Futtatás eredményét.

## <a name="version-402"></a>4.0.2 verziója

2018 november 30

- Belső hibajavítások.

## <a name="version-401"></a>4.0.1 verzió

2018 október 8

- Az alapkövetelmények:

    `Install-VaaSPrerequisites` a továbbiakban nincs szükség a Felhőbeli rendszergazdai hitelesítő adatokra. Ha a parancsmag legújabb verzióját futtatja, tekintse meg a [helyi ügynök letöltése és telepítése](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) című témakört az előfeltételek telepítéséhez szükséges átdolgozott parancsokhoz. Íme a parancsok:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>4.0.0-s verzió

2018 augusztus 29.

- Az előfeltételeket és a VHD-frissítéseket:

    `Install-VaaSPrerequisites` a mostantól megköveteli a Felhőbeli rendszergazdai hitelesítő adatok megadását a csomag érvényesítése során. A [helyi ügynök letöltésére és telepítésére](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) szolgáló dokumentáció a következő kóddal frissült:

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
    > A `$CloudAdminCreds` parancsfájl által megkövetelt Azure stack hub-példány ellenőrzése folyamatban van. Nem az a Azure Active Directory hitelesítő adat, amelyet az Varga bérlő használ.

- Helyi ügynök frissítése:

    A helyi ügynök előző verziója nem kompatibilis a szolgáltatás aktuális 4.0.0-kiadásával. Minden felhasználónak frissítenie kell a helyi ügynököket. A legújabb ügynök telepítésével kapcsolatos utasításokért tekintse meg [a helyi ügynök letöltése és telepítése](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) című témakört.

- PowerShell Automation frissítése:

    Olyan PowerShell-parancsfájlok módosításai történtek, `LaunchVaaSTests` amelyek a parancsfájl-csomagok legújabb verzióját igénylik. A parancsfájl-kezelési csomag legújabb verziójának telepítésével kapcsolatos utasításokért tekintse meg [a test pass munkafolyamat elindítása](azure-stack-vaas-automate-with-powershell.md) című témakört.

- Érvényesítés szolgáltatás-portálként:

  - Csomagok aláírásával kapcsolatos értesítések

    Ha a csomag-ellenőrzési munkafolyamat részeként elküld egy OEM testreszabási csomagot, a rendszer érvényesíti a csomag formátumát, hogy az megfeleljen a közzétett specifikációnak. Ha a csomag nem felel meg a követelményeknek, a Futtatás sikertelen lesz. A rendszer e-mail-értesítéseket küld a bérlő regisztrált Azure Active Directory kapcsolattartójának e-mail-címére.

  - Interaktív teszt kategóriája:

    Az **interaktív** teszt kategóriája hozzá lett adva. Ezek a tesztek interaktív, nem automatizált Azure Stack hub-forgatókönyveket gyakorolnak.

  - Interaktív funkció ellenőrzése:

    Az egyes funkciókra vonatkozó célzott visszajelzések megadásának lehetősége már elérhető a test pass munkafolyamatban. A `OEM Update on Azure Stack Hub 1806 RC Validation 5.1.4.0` teszt ellenőrzi, hogy vannak-e konkrét frissítések alkalmazva, majd visszajelzéseket gyűjt.

## <a name="next-steps"></a>Következő lépések

- [A szolgáltatás érvényesítésének megoldása](azure-stack-vaas-troubleshoot.md)
