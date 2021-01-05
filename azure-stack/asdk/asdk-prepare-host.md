---
title: Az ASDK-gazdagép előkészítése
description: Megtudhatja, hogyan készítheti elő a Azure Stack Development Kit (ASDK) gazdagépet a ASDK telepítéséhez.
author: PatAltimore
ms.topic: article
ms.date: 08/28/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 08/28/2019
ms.openlocfilehash: 5745d7efc5a953abaaa4e1d878677131c1c6e709
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873333"
---
# <a name="prepare-the-asdk-host-computer"></a>Az ASDK-gazdagép előkészítése
Mielőtt telepítené a Azure Stack Development Kit (ASDK) a gazdaszámítógépre, elő kell készítenie a ASDK-gazdagépet a telepítéshez. A gazdagép előkészítése után a rendszer a CloudBuilder. vhdx virtuális gép (VM) merevlemezéről indítja el a ASDK üzembe helyezésének megkezdéséhez.

## <a name="prepare-the-development-kit-host-computer"></a>A fejlesztői csomag gazdagépének előkészítése
Mielőtt telepítené a ASDK a gazdagépen, elő kell készítenie a ASDK-gazda számítógépének környezetét. A környezet előkészítéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be helyi rendszergazdaként a ASDK-gazdagépen.
2. Győződjön meg arról, hogy a CloudBuilder. vhdx fájl át lett helyezve a C:\ gyökerébe meghajtó ( `C:\CloudBuilder.vhdx` ).
3. Futtassa a következő szkriptet a ASDK Installer-fájl (asdk-installer.ps1) letöltéséhez a [Azure stack GitHub Tools adattárból](https://github.com/Azure/AzureStack-Tools) a ASDK-gazdaszámítógépen lévő **c:\ AzureStack_Installer** mappájába:

   > [!IMPORTANT]
   > Ügyeljen rá, hogy minden alkalommal töltse le a asdk-installer.ps1 fájlt, amikor telepíti a ASDK. A szkript gyakori módosításokat hajt végre, és a legújabb verziót kell használni minden ASDK-telepítéshez. Előfordulhat, hogy a parancsfájl régebbi verziói nem működnek a legfrissebb kiadással.

   ```powershell
   # Variables
   $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
   $LocalPath = 'C:\AzureStack_Installer'
   # Create folder
   New-Item $LocalPath -Type directory
   # Enforce usage of TLSv1.2 to download from GitHub
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   # Download file
   Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
   ```

4. Egy emelt szintű PowerShell-konzolon indítsa el a **C:\AzureStack_Installer\asdk-installer.ps1** parancsfájlt, majd kattintson a **környezet előkészítése** elemre.

    ![A környezet előkészítése a ASDK-ben](media/asdk-prepare-host/1.PNG) 

5. A telepítő **Cloudbuilder Vhdx kiválasztása** lapján keresse meg és válassza ki a **Cloudbuilder. vhdx** fájlt, amelyet [az előző lépésekben](asdk-download.md)letöltött és kibontott. Ezen a lapon engedélyezheti az **illesztőprogramok hozzáadása** jelölőnégyzetet is, ha további illesztőprogramokat kell hozzáadnia a ASDK Kit-gazdagéphez. Kattintson a **Tovább** gombra.  

    ![Válassza a cloudbuilder. vhdx elemet, és adjon hozzá illesztőprogramokat a ASDK](media/asdk-prepare-host/2.PNG)

6. A **választható beállítások** lapon adja meg a ASDK gazdagép számítógépének helyi rendszergazdai fiókjának adatait, majd kattintson a **tovább** gombra.

    Ha nem adja meg a helyi rendszergazdai hitelesítő adatokat ebben a lépésben, a számítógép újraindítása után közvetlen vagy KVM-hozzáféréssel kell rendelkeznie a gazdagéphez a ASDK beállításának részeként.

   ![Választható beállítások a ASDK-ben – helyi rendszergazdai fiók adatainak megadása](media/asdk-prepare-host/3.PNG)

    A következő opcionális beállításokra vonatkozó értékeket is megadhat:
    - **Számítógépnév**: Ez a beállítás megadja a ASDK gazdagép nevét. A névnek meg kell felelnie az FQDN követelményeinek, és legfeljebb 15 karakter hosszúságú lehet. Az alapértelmezett érték a Windows által generált véletlenszerű számítógépnév.

        - Válasszon ki egy hálózati adaptert. Mielőtt a **tovább** gombra kattint, ellenőrizze, hogy tud-e csatlakozni az adapterhez.

            ![A hálózati adapter beállításainak képernyőképe](media/asdk-prepare-host/step-four-network-adapter.png)

        - Győződjön meg arról, hogy a megjelenített **IP-cím**, az **átjáró** és a **DNS-** érték helyes, adjon meg egy érvényes **időkiszolgáló IP-** címet, majd kattintson a **tovább** gombra.

            >[!TIP]
            >Az időkiszolgáló IP-címének megkereséséhez látogasson el a [ntppool.org](https://www.ntppool.org/) vagy a ping Time.Windows.com. 

            ![Az IP-konfigurációs beállítások képernyőképe](media/asdk-prepare-host/step-five-host-ip-config.png)

7. Az előkészítési folyamat elindításához kattintson a **tovább** gombra.
8. Ha az előkészítés **befejezését** jelzi, kattintson a **tovább** gombra.

    ![A környezet előkészítése a ASDK](media/asdk-prepare-host/4.PNG)

9. Az **Újraindítás most** gombra kattintva indítsa el a ASDK-gazdaszámítógépet a cloudbuilder. vhdx, és [folytassa a telepítési folyamatot](asdk-install.md).

    ![A ASDK újraindítása](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>További lépések
[A ASDK telepítése](asdk-install.md)
