---
title: Üzembe helyezése egy C# ASP.NET-webalkalmazás egy virtuális géphez az Azure Stackben |} A Microsoft Docs
description: Üzembe helyezése egy C# egy virtuális géphez az Azure Stack ASP.NET-webalkalmazásban.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: b41c64d64a2c2abe6d1f145f11c2d4d84686b207
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617700"
---
# <a name="deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack"></a>Üzembe helyezése egy C# egy virtuális géphez az Azure Stack ASP.NET-webalkalmazás

Létrehozhat egy virtuális gép (VM) a gazdagépen a C# ASP.NET-webalkalmazás létrehozása az Azure Stack. Ez a cikk ismerteti a kiszolgáló beállításakor kövesse az utasításokat, hogy a gazdagép konfigurálása a C# ASP.NET-WebApp, és telepíteni az alkalmazást közvetlenül a Visual Studióból.

C#egy általános célú, több paradigmát programozási nyelv kitérve erős beírni, betűrendbe hatókörrel rendelkező, feltétlenül szükséges, deklaratív, működési, általános, objektumorientált, és programozási oktatnak összetevő-orientált van. Ismerje meg, hogy a C# programozási nyelvet, és keresse meg a további erőforrások az C#, tekintse meg a [ C# útmutató](https://docs.microsoft.com/dotnet/csharp/).

Ez a cikk egy C# 6.0 Windows 2016-kiszolgálón futó ASP.NET Core 2.2 használó alkalmazásokban.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Hozzon létre egy [Windows Server virtuális gép](azure-stack-quick-windows-portal.md).

1. A virtuális Gépen a (a felügyeleti konzol) az IIS és ASP.NET 4.6 összetevők telepítéséhez futtassa az alábbi parancsfájlt:

    ```PowerShell  
    # Install IIS (with Management Console)
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Install ASP.NET 4.6
    Install-WindowsFeature Web-Asp-Net45
    
    # Install Web Management Service
    Install-WindowsFeature -Name Web-Mgmt-Service
    ```

1. Töltse le [Web Deploy v3.6](https://www.microsoft.com/download/details.aspx?id=43717). Telepítheti az MSI-fájlból, és engedélyez összes funkcióját.

1. Telepítse a .NET Core 2.2-üzemeltetési csomag a kiszolgálón. Útmutatásért lásd: [.NET Core-telepítő](https://dotnet.microsoft.com/download/dotnet-core/2.2). Győződjön meg arról, a fejlesztői gépén és a célkiszolgálón is .NET Core ugyanazt a verzióját használja-e.

1. Az Azure Stack portálon nyissa meg a portot, amelyet a hálózati beállításokat a virtuális gép szerepel.

    a. Nyissa meg az Azure Stack portálon a bérlő számára.

    b. Keresse meg a virtuális gép. Akkor lehet, hogy rendelkezik a virtuális gép az irányítópulton rögzítve, vagy Ön is megkeresheti azt az a **erőforrások keresése** mezőbe.

    c. Válassza ki **hálózatkezelés**.

    d. Válassza ki **bejövőport-szabály hozzáadása** virtuális gép alapján.

    e. Adjon hozzá egy bejövő biztonsági szabályt a következő portokat:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az alkalmazásprotokoll elosztott, együttműködő, hipermédia információs rendszerekhez. Az ügyfelek a webalkalmazás nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 443 | HTTPS | Hypertext Transfer Protocol biztonságos (HTTPS) az kiterjesztése a Hypertext Transfer Protocol (HTTP). A számítógép hálózaton szolgál a biztonságos kommunikáció érdekében. Az ügyfelek a webalkalmazás nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 22 | SSH | Secure Shell (SSH) egy olyan titkosítási hálózati protokoll, a hálózati szolgáltatások biztonságos működő-nem biztonságos hálózaton keresztül. Ezt a kapcsolatot használja az SSH-ügyfelet konfigurálja a virtuális Gépet, és az alkalmazás üzembe helyezéséhez. |
    | 3389 | RDP | Választható. Távoli asztal protokoll (RDP) lehetővé teszi, hogy a grafikus felhasználói felület használata a gép egy távoli asztali kapcsolatot.   |

    Minden egyes porthoz:

    a. A **forrás**válassza **bármely**.

    b. A **forrás porttartomány**, írjon be egy csillag (**\***).

    c. A **cél**válassza **bármely**.

    d. A **Célporttartomány**, adja hozzá a megnyitni kívánt portot.

    e. A **protokoll**válassza **bármely**.

    f. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.

    g. A **prioritású**, hagyja meg az alapértelmezett.

    h. Adjon meg egy **neve** és **leírás** segítséget ne feledje, hogy miért a port nyitva-e.

    i. Válassza a **Hozzáadás** lehetőséget.

1.  Az a **hálózatkezelés** az Azure Stackben, a virtuális gép beállításait, hozzon létre egy DNS-nevet a kiszolgáló számára. Felhasználók a webhelynek az URL-cím használatával csatlakozni tud.

    a. Nyissa meg az Azure Stack portálon a bérlő számára.

    b. Keresse meg a virtuális gép. Akkor lehet, hogy rendelkezik a virtuális gép az irányítópulton rögzítve, vagy Ön is megkeresheti azt az a **erőforrások keresése** mezőbe.

    c. Válassza az **Áttekintés** lehetőséget.

    d. A **VM**válassza **konfigurálása**.

    e. A **hozzárendelés**válassza **dinamikus**.

    f. Adja meg például a DNS-névcímke **mywebapp**, így a teljes URL-cím lesz *mywebapp.local.cloudapp.azurestack.external*.

## <a name="create-an-app"></a>Alkalmazás létrehozása 

Használhatja a saját webes alkalmazás vagy a következő példa [ASP.NET Core-alkalmazás közzététele az Azure-bA a Visual Studióval](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
). A cikk azt ismerteti, hogyan hozhat létre és ASP.NET-webalkalmazás közzététele az Azure virtuális gépként az Azure Virtual Machines-közzétételi szolgáltatás használatával a Visual Studio 2017. Miután telepítette, és ellenőrizze, hogy az alkalmazás helyileg fut-e, frissíteni fogja a közzététel célpontja a Windows virtuális gépre az Azure Stack-példányban.

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

A közzétételi célként a virtuális gép létrehozása az Azure Stackben.

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza **közzététel**.

    ![Azure Stack üzembe helyezése egy ASP.NET-webalkalmazás közzététele](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

1. Az a **közzététel** ablakban válassza **új profil**.
1. Válassza ki **IIS**, **FTP**, és így tovább.
1. Kattintson a **Publish** (Közzététel) elemre.
1. A **metódus közzététele**válassza **Web Deploy**.
1. A **kiszolgáló** adja meg a DNS-név, amelyet korábban, mint például *w21902.local.cloudapp.azurestack.external*.
1. A **helynév**, adja meg **Default Web Site**.
1. A **felhasználónév**, adja meg a felhasználónevet, a gép.
1. A **jelszó**, adja meg a jelszót a gép.
1. A **cél URL-címe**, mint például a hely, adja meg az URL-cím *mywebapp.local.cloudapp.azurestack.external*.

    ![ASP.NET-webalkalmazás üzembe helyezése – Web Deploy konfigurálása](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

1. Ellenőrzése a válassza ki a web deploy konfigurációja **-kapcsolat ellenőrzése**, majd válassza ki **tovább**.
1. Állítsa be **konfigurációs** , **kiadási**.
1. Állítsa be **keretrendszer cél** , **netcoreapp2.2**.
1. Állítsa be **cél futtatókörnyezet** , **hordozható**.
1. Kattintson a **Mentés** gombra.
1. Kattintson a **Publish** (Közzététel) elemre.
1. Nyissa meg az új kiszolgálóra. Megtekintheti a futó webalkalmazás.

    ```HTTP  
        mywebapp.local.cloudapp.azurestack.external
    ```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [állítsa be a fejlesztési környezetet az Azure Stackben](azure-stack-dev-start.md).
- Ismerje meg [közös üzemelő példányok az Azure stack-beli iaas](azure-stack-dev-start-deploy-app.md).
