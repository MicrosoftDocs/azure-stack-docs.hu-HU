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
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 963944f2ade4db168c1b7f9070e72fe503bdfcf9
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954468"
---
# <a name="release-notes-for-validation-as-a-service"></a>Kibocsátási megjegyzések szolgáltatásként történő érvényesítéshez

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ez a cikk a Azure Stack érvényesítésére szolgáló kibocsátási megjegyzésekkel szolgál.

## <a name="version-4421"></a>4\.4.2.1 verziója

2019 december 3

- Tartalmi frissítések tesztelése
  - A havi Azure Stack frissítési munkafolyamat online dokumentációja és az OEM-csomag érvényesítési munkafolyamata frissítve lett. Tekintse át a frissített dokumentációt, itt ellenőrizze az OEM-csomagokat, és itt ellenőrizheti a Microsofttól származó szoftverfrissítéseket.
  - Az alaprendszer-csomag érvényesítési munkafolyamatának frissítése: az OEM-ellenőrzési munkafolyamat az egyetlen teszt, amely a havi Azure Stack frissítés ellenőrzése és az OEM-csomagok ellenőrzése során szükséges. A teszt frissíti a bélyegzőt a megadott AzureStack/OEM-csomagokkal, és futtatja a felhőalapú szimulációs motor ellenőrzési tesztjét.
  - Az Varga PowerShell bővítmény frissítése: a csomag-ellenőrzési munkafolyamat automatizálása mostantól támogatott. A bővítmény használatára vonatkozó részletes információkért tekintse meg az Azure Stack az alapszolgáltatások automatizálása a PowerShell használatával című témakört.

- Ismert problémák
  - Vegye fel a kapcsolatot vaashelp@microsoft.com ha a következő tesztelési esetek nem futnak az OEM-ellenőrzési munkafolyamat során:
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk


## <a name="version-4353"></a>4\.3.5.3 verziója

2019 november 7.

- Tartalmi frissítések tesztelése
  - Havi Azure Stack frissítés ellenőrzése (5.1.46.0-> 5.1.49.0)
  - OEM kiterjesztési csomag ellenőrzése (5.1.46.0-> 5.1.49.0)
  - A 5.1.46.0 eredményei megmaradtak. Ha sikeresen futtatta a 5.1.46.0-t, értesítse vaashelp@microsoft.com az eredmények elküldésekor.

- Hibajavítások
  - Kijavítva a hiba, Azure Stack ahol a frissítés ellenőrzése sikertelen volt, ha az Update. zip speciális karaktereket tartalmaz.

- Ismert problémák
  - Az mstest. exe nem található. Megkerülő megoldás:
    1. CTRL + C az ügynök a PowerShell-ablakban.
    1. Írja be a mstest. exe fájlt annak ellenőrzéséhez, hogy a mstest. exe egy felismert program.
    1. Ha a mstest. exe nem ismerhető fel, akkor a jelenlegi PowerShell-ablak bezárásához.
    1. Kattintson a Start gombra (ne a PowerShell elemre a tálcán), keresse meg a PowerShellt, és nyissa meg rendszergazdaként.
    1. Írja be a mstest. exe parancsot, és ellenőrizze, hogy elérhető-e parancsként.
    1. Indítsa újra az ügynököt, és futtassa újra a tesztet.
  - Alkalmanként a Cloud szimulációs motor \*VM-tesztekkel kapcsolatos hibákat fog jelenteni. A ismételt Futtatás megkísérlése előtt lépjen kapcsolatba vaashelp@microsoft.com. 


2019 október 29.

- A havi Azure Stack frissítési munkafolyamat online dokumentációja és az OEM-csomag érvényesítési munkafolyamata frissítve lett.

    Tekintse át a frissített dokumentációt, itt ellenőrizze az OEM-csomagokat, és itt ellenőrizheti a Microsofttól származó szoftverfrissítéseket.
- Varga munkafolyamat frissítése: havi Azure Stack frissítés (5.1.30.0-> 5.1.46.0) – a havi Azure Stack frissítés-ellenőrzési teszt munkafolyamata frissítve lett.

    A munkafolyamatnak már nincs manuális beavatkozása, és a ütemezése zökkenőmentesen futtatható.
- Varga munkafolyamat frissítése: OEM-csomag ellenőrzése (5.1.30.0-> 5.1.46.0) – az OEM-csomag érvényesítési munkafolyamata frissítve lett.

    A munkafolyamatnak már nincs manuális beavatkozása, és a ütemezése zökkenőmentesen futtatható.
- Az OEM-csomag érvényesítési munkafolyamatában (5.1.30.0-> 5.1.46.0) található felhőalapú szimulációs motor frissült az érvényesítési idő meggyorsítása érdekében: futási idő 1 órára csökken.
- A felhőalapú szimulációs motor az OEM-csomag érvényesítési munkafolyamatában és a Azure Stack frissítési munkafolyamatban (5.1.30.0-> 5.1.46.0) megköveteli, hogy a frissítések érvényesítése 2 különböző szülőmappa legyen, és ne legyen más frissítés a gyermek mappákban.
- Az OEM-csomag érvényesítési munkafolyamatában és a Azure Stack frissítési munkafolyamatban (5.1.30.0-> 5.1.46.0) lévő felhőalapú szimulációs motor megköveteli a tesztek ütemezését a következő sorrendben: havi Azure Stack frissítés-ellenőrzési teszt, OEM-bővítmény csomag Ellenőrzési teszt, és végül a Cloud szimulációs motor.
- Varga-ügynök frissítése: a frissített Varga ügynök mostantól a Azure Stack Cloud admin hitelesítő adatokkal kérdezi le a bélyegzőt, hogy lekérdezze a bélyegző adatait, hogy automatikusan feltöltse a munkafolyamatokat. 

    Ehhez a frissítéshez az összes ügynök frissítése és újraindítása szükséges. Az alábbi útmutatást követve frissítheti az Varga-ügynököt: https://docs.microsoft.com/azure-stack/partner/azure-stack-vaas-local-agent
- Az Varga portál felhasználói felületének frissítése: az ügynök kiválasztása tábla a tesztek ütemezése ablaktábla fölé került, hogy megkönnyítse a tesztelést.

    A feladatok ütemezésekor a továbbiakban nem kell megadnia a Stamp-adatokat, ha az alaprendszer-ügynökök megfelelően frissültek.


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
4. Küldje el VaaSHelp@microsoft.com a mentett kimenetet, hogy megkapja a Futtatás eredményét.

## <a name="version-402"></a>4\.0.2 verziója

2018 november 30

- Belső hibajavítások

## <a name="version-401"></a>4\.0.1 verzió

2018 október 8

- Az alapkövetelmények

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

- Az előfeltételeket és a VHD-frissítéseket

    `Install-VaaSPrerequisites` most a Felhőbeli rendszergazdai hitelesítő adatokkal kell rendelkeznie a csomagok ellenőrzése során felmerülő problémák megoldásához. A [helyi ügynök letöltésére és telepítésére](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) szolgáló dokumentáció frissítve lett a következővel:

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
    > A parancsfájl által igényelt `$CloudAdminCreds` az érvényesített Azure Stack-példányhoz van. Nem az a Azure Active Directory hitelesítő adat, amelyet az alaprendszer-bérlő használ.

- Helyi ügynök frissítése

    A helyi ügynök előző verziója nem kompatibilis a szolgáltatás aktuális 4.0.0-kiadásával. Minden felhasználónak frissítenie kell a helyi ügynököket. A legújabb ügynök telepítésével kapcsolatos utasításokért tekintse meg [a helyi ügynök letöltése és telepítése](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) című témakört.

- PowerShell Automation frissítése

    Olyan `LaunchVaaSTests` PowerShell-parancsfájlok módosítása történt, amelyek a parancsfájl-csomagok legújabb verzióját igénylik. A parancsfájl-kezelési csomag legújabb verziójának telepítésével kapcsolatos utasításokért tekintse meg [a test pass munkafolyamat elindítása](azure-stack-vaas-automate-with-powershell.md) című témakört.

- Érvényesítés szolgáltatás-portálként

  - Csomagok aláírásával kapcsolatos értesítések

    Ha a csomag-ellenőrzési munkafolyamat részeként elküld egy OEM testreszabási csomagot, a rendszer érvényesíti a csomag formátumát, hogy az megfeleljen a közzétett specifikációnak. Ha a csomag nem felel meg a követelményeknek, a Futtatás sikertelen lesz. A rendszer e-mail-értesítéseket küld a bérlő regisztrált Azure Active Directory kapcsolattartójának e-mail-címére.

  - Interaktív tesztelési kategória

    Az **interaktív** teszt kategóriája hozzá lett adva. Ezek a tesztek interaktív, nem automatizált Azure Stack-forgatókönyveket gyakorolnak.

  - Interaktív funkció ellenőrzése

    Az egyes funkciókra vonatkozó célzott visszajelzések megadásának lehetősége már elérhető a test pass munkafolyamatban. A `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` teszt ellenőrzi, hogy az adott frissítések megfelelően lettek-e alkalmazva, majd visszajelzéseket gyűjt.

## <a name="next-steps"></a>Következő lépések

- [A szolgáltatás érvényesítésének megoldása](azure-stack-vaas-troubleshoot.md)
