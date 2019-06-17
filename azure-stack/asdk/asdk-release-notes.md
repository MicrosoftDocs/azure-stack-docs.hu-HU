---
title: A Microsoft Azure Stack Development Kit kibocsátási megjegyzései |} A Microsoft Docs
description: Fejlesztések, javításokat és az Azure Stack Development Kit kapcsolatos ismert problémák.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: 0de27634ac6167637dcdc487474f16d3ef2b2c95
ms.sourcegitcommit: 427b534634d902b164e7d54dfd97b63c31563084
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67145179"
---
# <a name="asdk-release-notes"></a>ASDK kibocsátási megjegyzései

Ez a cikk a módosításokat, javításokat és ismert problémák az Azure Stack Development Kit (ASDK) kapcsolatos információkat. Ha nem biztos abban, hogy melyik verziót futtatja, akkor az [ellenőrizhető a portál használatával](../operator/azure-stack-updates.md#determine-the-current-version).

What's new in a ASDK való feliratkozással vétele a [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [RSS-hírcsatorna](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11905040"></a>1\.1905.0.40 összeállítása

<!-- ### Changes -->

### <a name="new-features"></a>Új funkciók

- Ebben a kiadásban új szolgáltatások listáját lásd: [ebben a szakaszban](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) kiadási megjegyzések az Azure Stack.

### <a name="fixed-and-known-issues"></a>Rögzített méretű és ismert problémák

- Kijavítva, amelyben szerkesztheti kellett a **RegisterWithAzure.psm1** annak érdekében, hogy a PowerShell-parancsprogram [regisztrálja a ASDK](asdk-register.md) sikeresen megtörtént.
- Más Azure Stack kapcsolatos problémák listáját, javítva a kiadásban, lásd: [ebben a szakaszban](../operator/azure-stack-release-notes-1905.md#fixes) kiadási megjegyzések az Azure Stack.
- Ismert problémák listáját lásd: [Ez a cikk](../operator/azure-stack-release-notes-known-issues-1905.md).
- Vegye figyelembe, hogy [elérhető az Azure Stack-gyorsjavítások](../operator/azure-stack-release-notes-1905.md#hotfixes) nem használhatók az Azure Stack ASDK.

## <a name="build-11904036"></a>1\.1904.0.36 összeállítása

<!-- ### Changes -->

### <a name="new-features"></a>Új funkciók

- Ebben a kiadásban új szolgáltatások listáját lásd: [ebben a szakaszban](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) kiadási megjegyzések az Azure Stack.

### <a name="fixed-and-known-issues"></a>Rögzített méretű és ismert problémák

- Szolgáltatás egyszerű időtúllépése miatt annak érdekében, hogy a regisztrációs parancsfájl futtatásakor [regisztrálja a ASDK](asdk-register.md) sikeresen szerkesztenie kell a **RegisterWithAzure.psm1** PowerShell-parancsfájlt. Tegye a következőket:

  1. A ASDK állomás számítógépen nyissa meg a fájlt **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** emelt jogosultsági szintű egy szövegszerkesztőben.
  2. 1249\. sorban, adjon hozzá egy `-TimeoutInSeconds 1800` paraméter a végén. Ez azért szükséges, a szolgáltatás egyszerű időtúllépés miatt, a regisztráció parancsfájl futtatásakor. Sor 1249 meg kell jelennie a következő:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- A VPN-kapcsolat azonosított probléma kijavítva [itt kiadásának 1902](#known-issues).

- Más Azure Stack kapcsolatos problémák listáját, javítva a kiadásban, lásd: [ebben a szakaszban](../operator/azure-stack-release-notes-1904.md#fixes) kiadási megjegyzések az Azure Stack.
- Ismert problémák listáját lásd: [Ez a cikk](../operator/azure-stack-release-notes-known-issues-1904.md).
- Vegye figyelembe, hogy [elérhető az Azure Stack-gyorsjavítások](../operator/azure-stack-release-notes-1904.md#hotfixes) nem használhatók az Azure Stack ASDK.

## <a name="build-1903"></a>1903 összeállítása

A 1903 hasznos adatai nem tartalmaz egy ASDK kiadást.

## <a name="build-11902069"></a>1\.1902.0.69 összeállítása

### <a name="new-features"></a>Új funkciók

- A 1902 build vezet be az Azure Stack rendszergazdai portál csomagok, ajánlatok, kvóták és kiegészítő csomagok létrehozására szolgáló új felhasználói felületet. További információk, többek között a képernyőképek: [csomagok, ajánlatok és kvóták létrehozása](../operator/azure-stack-create-plan.md).

- Az egyéb módosítások és fejlesztések azzal kapcsolatban, ebben a kiadásban, lásd: [ebben a szakaszban](../operator/azure-stack-update-1902.md#improvements) az Azure Stack a kibocsátási megjegyzéseket.

<!-- ### New features

- For a list of new features in this release, see [this section](../operator/azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../operator/azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../operator/azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../operator/azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Ismert problémák

- Probléma-és a ASDK használata a lépések egy másik gazdagépre a VPN-kapcsolat létesítése [Ez a cikk](asdk-connect.md). Amikor megkísérli a VPN-ügyfél csatlakozhat a ASDK környezet, látni fogja a hiba, amely **helytelen a felhasználónév vagy jelszó**, akkor is, ha megbizonyosodott arról, hogy rendelkezik a megfelelő fiókot használja, és helyesen írta be a jelszót. A probléma nem rendelkező hitelesítő adatait, de inkább a ASDK a VPN-kapcsolathoz használt hitelesítési protokoll módosítás van. A probléma megkerüléséhez végezze el az alábbi lépéseket:

   Győződjön meg róla, módosítások ASDK kiszolgálóoldali használt hitelesítési protokoll:

   1. RDP-vel a ASDK gazdagép.
   2. Nyisson meg egy rendszergazda jogú PowerShell-munkamenetet, mint AzureStack\AzureStackAdmin, akkor adja meg a központi telepítés a jelszóval jelentkezik be.
   3. Futtassa az alábbi parancsot:

      ```powershell
      netsh nps set np name = "Connections to Microsoft Routing and Remote Access server" profileid = "0x100a" profiledata = "1A000000000000000000000000000000" profileid = "0x1009" profiledata = "0x5"
      restart-service remoteaccess -force
      ```

   Ezután módosítsa a kapcsolat ügyféloldali parancsfájlt. Ennek legegyszerűbb módja, hogy módosításokat közvetlenül a C:\AzureStack-Tools-master\connect\azurestack.connect.psm1 modul skriptu:

   1. Módosítsa a **Add-AzsVpnConnection** parancsmaggal módosíthatja a `AuthenticationMethod` paramétert `MsChapv2` való `EAP`:

      ```powershell
      $connection = Add-VpnConnection -Name $ConnectionName -ServerAddress $ServerAddress -TunnelType L2tp -EncryptionLevel Required -AuthenticationMethod Eap -L2tpPsk $PlainPassword -Force -RememberCredential -PassThru -SplitTunneling
      ```

   2. Módosítsa a **Connect-AzsVpn** parancsmag használatával `rasdial @ConnectionName $User $PlainPassword` használatával `rasphone`, ahogy az EAP szükséges interaktív bejelentkezés:

      ```powershell
      rasphone $ConnectionName
      ```

   3. Mentse a módosításokat, és ezután importálja újra a **azurestack.connect.psm1** modul.
   4. Kövesse a [Ez a cikk](asdk-connect.md#set-up-vpn-connectivity).
   5. A VPN-en keresztül ASDK való csatlakozáskor csatlakozáshoz nyissa meg a Windows **hálózat és Internet beállítások**, majd **VPN**, ahelyett hogy elindítani a tálcáról, győződjön meg arról, hogy meg kell adni hitelesítő adatok.

- Hibát talált, amelyben a csomagok több mint 1450 bájt egy belső Load Balancer (ILB), a rendszer elveti. A probléma okozza a gazdagépen, hogy túl alacsony, hogy megfeleljen a szerepkört, amely 1901 től került, a gazdagép áthaladó VXLAN beágyazású csomagok MTU-beállítását. Legalább két forgatókönyv közül választhat, amelyek akkor fordulhatnak, amelyben úgy találtuk, manifest magát a probléma:

  - SQL-lekérdezéseket az SQL Always-On, amely egy belső Load Balancer (ILB) mögött, és több mint 660 bájt.
  - Kubernetes üzembe helyezések meghiúsulnak, ha több főkiszolgálót engedélyezését.  

  A probléma akkor fordul elő, ha egy virtuális Gépet és a egy ILB közötti kommunikáció az azonos virtuális hálózatba, de különböző alhálózatokon. A probléma megkerüléséhez az alábbi parancsokat egy rendszergazda jogú parancssorban a ASDK gazdagépen futó:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```
