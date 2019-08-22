---
title: A Azure Stack Development Kit-(ASDK-) gazdagép-számítógép előkészítése | Microsoft Docs
description: Leírja, hogyan kell előkészíteni a Azure Stack Development Kit (ASDK) gazdagépet a ASDK telepítéséhez.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 291042e0a7af78ed2431c901901e7e44b1f05de1
ms.sourcegitcommit: fc7da38321736e952b2cc6d5d07f276d095dc8d1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887155"
---
# <a name="prepare-the-asdk-host-computer"></a>A ASDK-gazda számítógép előkészítése
Mielőtt telepítené a ASDK a gazdaszámítógépre, elő kell készítenie a ASDK-gazdagépet a telepítéshez. A fejlesztői készlet gazdagépének előkészítése után a rendszer a CloudBuilder. vhdx virtuális gép merevlemezéről indítja el a ASDK üzembe helyezésének megkezdéséhez.

## <a name="prepare-the-development-kit-host-computer"></a>A fejlesztői csomag gazdagépének előkészítése
Mielőtt telepítené a ASDK a gazdagépen, elő kell készítenie a ASDK-gazda számítógépének környezetét.
1. Jelentkezzen be helyi rendszergazdaként a fejlesztői csomag gazdagépének számítógépén.
2. Győződjön meg arról, hogy a CloudBuilder. vhdx fájl át lett helyezve a C:\ gyökerébe meghajtó (C:\CloudBuilder.vhdx).
3. Futtassa a következő szkriptet a fejlesztői csomag telepítőjének (asdk-Installer. ps1) letöltéséhez a [Azure stack GitHub Tools adattárból](https://github.com/Azure/AzureStack-Tools) a fejlesztői készlet gazdagépének **C:\AzureStack_Installer** mappájába:

   > [!IMPORTANT]
   > Ügyeljen arra, hogy minden alkalommal letöltse a asdk-Installer. ps1 fájlt, amikor telepíti a ASDK. A szkript gyakori módosításokat hajt végre, és a legújabb verziót kell használni minden ASDK-telepítéshez. Előfordulhat, hogy a parancsfájl régebbi verziói nem működnek a legfrissebb kiadással.

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

4. Egy emelt szintű PowerShell-konzolon indítsa el a **C:\AzureStack_Installer\asdk-Installer.ps1** parancsfájlt, majd kattintson a **környezet előkészítése**elemre.

    ![Képernyőfelvétel a környezet előkészítéséről](media/asdk-prepare-host/1.PNG) 

5. A telepítő **Cloudbuilder Vhdx kiválasztása** lapján keresse meg és válassza ki a **Cloudbuilder. vhdx** fájlt, amelyet [az előző lépésekben](asdk-download.md)letöltött és kibontott. Ezen a lapon opcionálisan engedélyezheti az **illesztőprogramok hozzáadása** jelölőnégyzetet, ha további illesztőprogramokat kell hozzáadnia a fejlesztői csomag gazdagépéhez. Kattintson a **Tovább** gombra.  

    ![Képernyőkép a Select Cloudbuilder. vhdx](media/asdk-prepare-host/2.PNG)

6. A **választható beállítások** lapon adja meg a fejlesztői csomag gazdagépének helyi rendszergazdai fiókjának adatait, majd kattintson a **tovább**gombra.<br><br>Ha nem adja meg a helyi rendszergazdai hitelesítő adatokat ebben a lépésben, a számítógép újraindítása után közvetlenül vagy KVM-hozzáféréssel kell rendelkeznie a gazdagéphez a fejlesztői csomag beállítása során.

   ![Képernyőkép a választható beállításokról](media/asdk-prepare-host/3.PNG)

    A következő opcionális beállításokra vonatkozó értékeket is megadhat:
    - **Számítógépnév**: Ez a beállítás beállítja a fejlesztői készlet gazdagépének nevét. A névnek meg kell felelnie az FQDN követelményeinek, és legfeljebb 15 karakter hosszúságú lehet. Az alapértelmezett érték a Windows által generált véletlenszerű számítógépnév.
    - **Statikus IP-konfiguráció**: Az üzembe helyezést statikus IP-cím használatára állítja be. Ellenkező esetben, ha a telepítő újraindítja a cloudbuilder. vhdx, a hálózati adapterek DHCP-vel vannak konfigurálva. Ha statikus IP-konfigurációt használ, további lehetőségek is megjelennek, ahol a következőket kell megadnia:
      - Válasszon ki egy hálózati adaptert. Mielőtt a **tovább**gombra kattint, ellenőrizze, hogy tud-e csatlakozni az adapterhez.

        ![A hálózati adapter beállításainak képernyőképe](media/asdk-prepare-host/step-four-network-adapter.png)

      - Győződjön meg arról, hogy a megjelenített **IP-cím**, az **átjáró**és a **DNS-** érték helyes, adjon meg egy érvényes időkiszolgáló **IP-** címet, majd kattintson a **tovább**gombra.

        >[!TIP]
        >Az időkiszolgáló IP-címének megkereséséhez látogasson el a [ntppool.org](https://www.ntppool.org/) vagy a ping Time.Windows.com. 

        ![Az IP-konfigurációs beállítások képernyőképe](media/asdk-prepare-host/step-five-host-ip-config.png)

7. Az előkészítési folyamat elindításához kattintson a **tovább** gombra.
8. Ha az előkészítés **befejezését**jelzi, kattintson a **tovább**gombra.

    ![A befejezett képernyő képernyőképe](media/asdk-prepare-host/4.PNG)

9. Az **Újraindítás** gombra kattintva indítsa el a fejlesztői készletet futtató számítógépet a cloudbuilder. vhdx, és [folytassa a telepítési folyamatot](asdk-install.md).

    ![Képernyőkép az újraindítás most](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>További lépések
[A ASDK telepítése](asdk-install.md)
