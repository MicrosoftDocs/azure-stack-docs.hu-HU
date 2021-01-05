---
title: A ASDK telepítése
description: Ismerje meg, hogyan telepítheti a Azure Stack Development Kitt (ASDK).
author: PatAltimore
ms.topic: article
ms.date: 05/06/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: fe485a3eebee3e44e19173fdf68440057de7d783
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873418"
---
# <a name="install-the-asdk"></a>A ASDK telepítése
A [ASDK gazdagépének előkészítése](asdk-prepare-host.md)után a Azure stack Development Kit (ASDK) üzembe helyezhető a CloudBuilder. vhdx lemezképben a jelen cikkben ismertetett lépések alapján.

## <a name="install-the-asdk"></a>A ASDK telepítése
A cikkben ismertetett lépések bemutatják, hogyan helyezheti üzembe a ASDK az **asdk-installer.ps1** PowerShell-parancsfájl letöltésével és futtatásával elérhető grafikus felhasználói felületen.

> [!NOTE]
> A ASDK telepítő felhasználói felülete egy, a WCF és a PowerShell-alapú, nyílt forráskódú parancsfájl.


1. Miután a gazdaszámítógép sikeresen beindult a CloudBuilder. vhdx lemezképbe, jelentkezzen be a ASDK-gazdaszámítógép ASDK-telepítésre [való előkészítésekor](asdk-prepare-host.md) megadott rendszergazdai hitelesítő adatokkal. Ennek meg kell egyeznie a ASDK gazdagép helyi rendszergazdai hitelesítő adataival.
2. Nyisson meg egy rendszergazda jogú PowerShell-konzolt, és futtassa a meghajtóbetűjel **&lt; # C0\AzureStack_Installer\asdk-installer.ps1** PowerShell-parancsfájlt. Vegye figyelembe, hogy a parancsfájl mostantól egy másik meghajtón is lehet, mint a C:\ az CloudBuilder. vhdx képen. Kattintson az **Install** (Telepítés) gombra.

    ![A ASDK telepítése](media/asdk-install/1.PNG) 

3. Az identitás-szolgáltató **típusa** legördülő listában válassza az **Azure China Cloud**, az **azure US government Cloud**, **AD FS** vagy az **Azure Cloud** lehetőséget. A **helyi rendszergazda jelszava** mezőbe írja be a helyi rendszergazdai jelszót (amelynek meg kell egyeznie az aktuálisan beállított helyi rendszergazdai jelszóval) a **jelszó** mezőben, majd kattintson a **tovább** gombra.

    ![Identitás-szolgáltató típusa legördülő lista a ASDK](media/asdk-install/2.PNG) 
  
    Ha az Azure-előfizetéshez tartozó identitás-szolgáltatót választja, szüksége lesz egy internetkapcsolatra, egy Azure AD-címtárbeli bérlő teljes nevére a *tartománynév*. onmicrosoft.com vagy egy Azure ad által ellenőrzött egyéni tartománynév formájában. Globális rendszergazdai hitelesítő adatokra is szüksége van a megadott könyvtárhoz.

    Az üzembe helyezést követően a Azure Active Directory (Azure AD) globális rendszergazdai jogosultsága nem szükséges. Egyes műveletek esetében azonban szükség lehet a globális rendszergazdai hitelesítő adatokra. Például egy erőforrás-szolgáltatói telepítő parancsfájl vagy egy új szolgáltatás, amely engedély megadását igényli. Ideiglenesen visszaállíthatja a fiók globális rendszergazdai engedélyeit, vagy használhat egy különálló globális rendszergazdai fiókot, amely az *alapértelmezett szolgáltatói előfizetés* tulajdonosa.

    A AD FS identitás-szolgáltatóként való használatakor a rendszer az alapértelmezett Stamp Directory szolgáltatást használja. A szolgáltatással való bejelentkezéshez használt alapértelmezett fiók azurestackadmin@azurestack.local , és a használni kívánt jelszó a telepítés részeként megadott.

   > [!NOTE]
   > A legjobb eredmény érdekében akkor is érdemes a ASDK telepíteni, ha a leválasztott Azure Stack-környezetet a AD FS identitás-szolgáltató használatával kívánja használni. Így a Windows Server 2016 próbaverziója a ASDK-telepítésben is aktiválható a telepítéskor.

4. Válassza ki a ASDK használni kívánt hálózati adaptert, majd kattintson a **tovább** gombra.

    ![Hálózati adapter kiválasztása a ASDK](media/asdk-install/3.PNG)

5. A **hálózati konfiguráció** lapon adjon meg egy érvényes **időkiszolgáló IP-** címet. Ez a kötelező mező állítja be a ASDK által használandó időkiszolgálót. Ezt a paramétert érvényes Time Server IP-címnek kell megadni. A kiszolgálók nevei nem támogatottak.

      > [!TIP]
      > Az időkiszolgáló IP-címének megkereséséhez látogasson el a [ntppool.org](https://www.ntppool.org/) vagy a ping Time.Windows.com. 

    **Opcionálisan** megadhat egy DNS- **továbbító** IP-címet is. A rendszer a Azure Stack központi telepítésének részeként létrehoz egy DNS-kiszolgálót. Annak engedélyezéséhez, hogy a megoldásban lévő számítógépek a bélyegzőn kívül is feloldják a neveket, adja meg a meglévő infrastruktúra DNS-kiszolgálóját. A in-Stamp DNS-kiszolgáló ismeretlen névfeloldási kérelmeket továbbít erre a kiszolgálóra.

    ![DNS-továbbító és hálózati konfiguráció a ASDK-ben](media/asdk-install/4.PNG)

6. A **hálózati kártya ellenőrzésének tulajdonságai** lapon egy folyamatjelző sáv jelenik meg. Az ellenőrzés befejezésekor kattintson a **tovább** gombra.

    ![Hálózati kártya tulajdonságainak ellenőrzése a ASDK-ben](media/asdk-install/5.PNG)

7. Az **Összefoglalás** lapon kattintson a **telepítés** elemre a ASDK telepítésének elindításához a ASDK gazdaszámítógépen.

    ![Parancsfájl-összefoglalás a ASDK üzembe helyezése előtt](media/asdk-install/6.PNG)

    > [!TIP]
    > Itt is másolhatja a PowerShell telepítési parancsait, amelyeket a rendszer a ASDK telepítéséhez fog használni. Ez akkor hasznos, ha a PowerShell használatával bármikor újra kell [telepítenie a ASDK a gazdagépen](asdk-deploy-powershell.md).

8. Ha Azure AD-telepítést hajt végre, a telepítés megkezdése után néhány perc múlva meg kell adnia az Azure AD globális rendszergazdai fiókjának hitelesítő adatait.

9. Az üzembe helyezési folyamat néhány órát is igénybe vehet, amíg a gazdaszámítógép automatikusan újraindul. Ha figyelni szeretné az üzembe helyezés folyamatát, jelentkezzen be a azurestack\AzureStackAdmin a ASDK-állomás újraindítása után. Ha az üzembe helyezés sikeres, a PowerShell-konzol a következőt jeleníti meg: **Befejezés: művelet "központi telepítés"**. 
    > [!IMPORTANT]
    > Ha helyi rendszergazdaként jelentkezik be, miután a gép csatlakozott a azurestack tartományhoz, nem fogja látni a telepítési folyamatot. Ne futtassa újra az üzembe helyezést, hanem jelentkezzen be azurestack\AzureStackAdmin, hogy ellenőrizze, hogy fut-e.

    ![ASDK sikeres üzembe helyezése](media/asdk-install/7.PNG)

Gratulálunk, sikeresen telepítette a ASDK!

Ha a központi telepítés valamilyen okból meghiúsul, újból [üzembe helyezheti a rendszert](asdk-redeploy.md) , vagy a következő PowerShell-parancsokkal újraindíthatja az üzembe helyezést az utolsó sikeres lépés után. A parancsok ugyanazon emelt szintű PowerShell-ablakból is használhatók:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>További lépések
[Üzembe helyezés utáni konfiguráció](asdk-post-deploy.md)
