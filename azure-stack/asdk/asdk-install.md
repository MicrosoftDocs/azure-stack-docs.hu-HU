---
title: Az Azure Stack Development Kit (ASDK) telepítése |} A Microsoft Docs
description: Az Azure Stack Development Kit (ASDK) telepítését ismerteti.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: f0aef7d1699065664f2013076ed2b12b24a06d4d
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172444"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Az Azure Stack Development Kit (ASDK) telepítése
Miután [a ASDK számítógép előkészítése](asdk-prepare-host.md), a ASDK is üzembe helyezhetők az alábbi lépéseket követve ebben a cikkben a CloudBuilder.vhdx lemezképpel.

## <a name="install-the-asdk"></a>Telepítse a ASDK
A jelen cikkben ismertetett lépések bemutatják, hogyan helyezhet üzembe a grafikus felhasználói felületen (GUI) megadott letöltésével és futtatásával ASDK a **asdk-installer.ps1** PowerShell-parancsfájlt.

> [!NOTE]
> Az Azure Stack Development Kit telepítő felhasználói felületének, egy nyílt forráskódú szkript alapján a WCF és a PowerShell használatával.


1. Miután a számítógép sikeresen CloudBuilder.vhdx lemezképpel indul el, jelentkezzen be rendszergazdai hitelesítő adatok használatával megadni, ha, [előkészítve a development kit gazdaszámítógép](asdk-prepare-host.md) ASDK telepítéséhez. Ez legyen ugyanaz, mint a development kit gazdagép helyi rendszergazdai hitelesítő adatokkal.
2. Nyisson meg egy rendszergazda jogú PowerShell-konzolt, és futtassa a  **&lt;rendszermeghajtó betűjele > \AzureStack_Installer\asdk-installer.ps1** PowerShell-parancsfájlt. Vegye figyelembe, hogy a parancsfájl előfordulhat, hogy most már a CloudBuilder.vhdx kép C:\ meghajtón található. Kattintson az **Install** (Telepítés) gombra.

    ![](media/asdk-install/1.PNG) 

3. Az identitásszolgáltató **típus** legördülő mezőben válassza ki **Azure China Cloud**, **Azure US Government Cloud**, **az AD FS**, vagy **Azure-felhő**. A **helyi rendszergazdai jelszó** (amelynek meg kell egyeznie a jelenlegi konfigurált helyi rendszergazda jelszavát) a helyi rendszergazdai jelszót a **jelszó** mezőbe, majd kattintson a  **Tovább**.

    ![](media/asdk-install/2.PNG) 
  
   Ha úgy dönt, hogy az Azure-előfizetés identitásszolgáltatókhoz, szüksége van-e internetkapcsolat, a teljes neve az Azure AD directory-bérlő formájában *domainname*. onmicrosoft.com vagy egy Azure AD ellenőrizte az egyéni tartomány nevét és globális a megadott könyvtár rendszergazdai hitelesítő adataival.<br><br>Az üzembe helyezést követően az Azure Active Directory globális rendszergazdának, nem szükséges. Egyes műveletek azonban szükség lehet a globális rendszergazdai hitelesítő adatok. Például egy erőforrás-szolgáltató szoftvertelepítő parancsfájl vagy egy új szolgáltatást igénylő engedélyt kell adni. Ideiglenesen érvényességének visszaállítása a fiók globális rendszergazdai jogosultságokkal, vagy használjon egy külön globális rendszergazdai fiókkal, amely a tulajdonosa a *szolgáltatói előfizetés alapértelmezett*.<br><br>Az AD FS használata Identitásszolgáltatóként, az alapértelmezett stamp címtárszolgáltatás szolgál. Jelentkezzen be az alapértelmezett fiók azurestackadmin@azurestack.local, és a jelszó használata a telepítés során megadottal.

   > [!NOTE]
   > A legjobb eredmények érdekében még akkor is, ha az AD FS használata Identitásszolgáltatóként, kapcsolat nélküli Azure Stack-környezet használni kívánt célszerű telepíteni a ASDK, ha az internethez csatlakozik. Ezzel a módszerrel a development kit telepítést része a Windows Server 2016 próbaverzió a központi telepítéskor aktiválható.

4. Válassza ki a csomagot használja, és kattintson egy hálózati adaptert **tovább**.

    ![](media/asdk-install/3.PNG)

5. Az a **hálózati konfiguráció** területén adjon meg egy érvényes **idő a kiszolgáló IP-címe** címet. Ez szükséges mező állítja be a kiszolgálót a csomag által használható. Ez a paraméter egy érvényes idő-kiszolgáló IP-címét meg kell adni. Kiszolgálók nevei nem támogatottak.

      > [!TIP]
      > Egy idő-kiszolgáló IP-cím található, a Microsoft [ntppool.org](https://www.ntppool.org/) , illetve pingelni time.windows.com. 

    **Igény szerint**, megadhat egy **DNS-továbbító** IP-címet. DNS-kiszolgáló létrehozása az Azure Stack üzembe helyezés részeként. Ahhoz, hogy a számítógépek belül a megoldás a stamp kívül nevek feloldása, adja meg a meglévő infrastruktúra DNS-kiszolgáló. A stamp a DNS-kiszolgáló ezen a kiszolgálón ismeretlen névfeloldási kérelmeket továbbítja.

    ![](media/asdk-install/4.PNG)

6. Az a **ellenőrzése a hálózati kártya tulajdonságai** lapon látni fog egy folyamatjelző sávot. Ellenőrzési befejeződése után kattintson a **tovább**.

    ![](media/asdk-install/5.PNG)

7. A **összefoglalás** kattintson **telepítés** ASDK telepítésének elindítása a development kit gazdagépen.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Itt is másolhatja a PowerShell telepítő-parancsokat a csomag telepítéséhez használt. Ez akkor hasznos, ha a átállítására lenne szükség [ismételt üzembe helyezése a PowerShell használatával a gazdagépen ASDK](asdk-deploy-powershell.md).

8. Az Azure AD központi telepítésének hajt végre, ha meg kéri, adja meg az Azure AD globális rendszergazdai fiók hitelesítő adatait, a telepítő indítása után néhány percet.

9. Az üzembe helyezési folyamat eltarthat néhány óráig, amely idő alatt a számítógép automatikusan újraindul egyszer. Ha azt szeretné, a telepítési folyamat állapotának monitorozásához, jelentkezzen be felhasználóként azurestack\AzureStackAdmin a development kit gazdagép újraindítása után. Az üzembe helyezés sikeres, a PowerShell-konzolon jeleníti meg: **HAJTSA VÉGRE: A művelet "Telepítés"**. 
    > [!IMPORTANT]
    > Ha bejelentkezik egy helyi rendszergazdaként után a gép az azurestack tartományhoz csatlakozik, akkor nem jelenik meg az üzembehelyezési folyamatot. Futtassa újra a központi telepítés, ne inkább jelentkezhessen be azurestack\AzureStackAdmin ellenőrzése, hogy fut-e.

    ![](media/asdk-install/7.PNG)

Gratulálunk, sikeresen telepítette a ASDK!

Ha a központi telepítés valamilyen okból nem sikerül, akkor [ismételt üzembe helyezése](asdk-redeploy.md) teljesen új vagy használja az alábbi PowerShell-parancsokat, a azonos emelt szintű PowerShell-ablakban, indítsa újra a telepítésben az utolsó sikeres lépésből:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>További lépések
[Üzembe helyezés utáni konfigurálásra](asdk-post-deploy.md)
