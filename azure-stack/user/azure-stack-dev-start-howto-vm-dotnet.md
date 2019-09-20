---
title: C# ASP.net-webalkalmazás üzembe helyezése egy virtuális gépen Azure Stackban | Microsoft Docs
description: Helyezzen üzembe C# egy ASP.net-webalkalmazást egy Azure stack-alapú virtuális gépen.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 08/09/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 08/09/2019
ms.openlocfilehash: cd7ca2e1a198cb66c5d7627029215b55af0de975
ms.sourcegitcommit: ae79b8bea670ea854ed00e9998d45f6b47fc8347
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71142576"
---
# <a name="deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack"></a>C# ASP.net-webalkalmazás üzembe helyezése Azure stack-beli virtuális gépen

Létrehozhat egy virtuális gépet (VM) a C# ASP.net-webalkalmazás üzemeltetéséhez Azure Stackban. Ez a cikk a kiszolgáló beállításakor követendő útmutatást ismerteti, konfigurálja a C# ASP.net-webalkalmazás üzemeltetéséhez, majd közvetlenül a Visual studióból helyezi üzembe az alkalmazást.

Ez a cikk egy C# olyan 6,0-alkalmazást használ, amely a Windows 2016 Serveren futó ASP.net Core 2,2-et használja.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Hozzon létre egy [Windows Server rendszerű virtuális gépet](azure-stack-quick-windows-portal.md).

1. Az IIS (felügyeleti konzol) és a ASP.NET 4,6-összetevők a virtuális gépen való telepítéséhez futtassa a következő parancsfájlt:

    ```PowerShell  
    # Install IIS (with Management Console)
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Install ASP.NET 4.6
    Install-WindowsFeature Web-Asp-Net45
    
    # Install Web Management Service
    Install-WindowsFeature -Name Web-Mgmt-Service
    ```

1. Töltse le a [web Deploy v 3.6](https://www.microsoft.com/download/details.aspx?id=43717)-ot. Telepítse az MSI-fájlból, majd engedélyezze az összes funkciót.

1. Telepítse a .NET Core 2,2 üzemeltetési csomagot a kiszolgálóra. Útmutatásért lásd: [.net Core Installer](https://dotnet.microsoft.com/download/dotnet-core/2.2). Győződjön meg arról, hogy a .NET Core azonos verzióját használja a fejlesztői gépen és a célkiszolgálón is.

1. A Azure Stack portálon nyissa meg a virtuális gép hálózati beállításaiban felsorolt portokat.

    a. Nyissa meg a Azure Stack portált a bérlő számára.

    b. Keresse meg a virtuális gépet. Lehet, hogy rögzítette a virtuális gépet az irányítópulton, vagy megkeresi az **erőforrások keresése** mezőben.

    c. Válassza a **hálózatkezelés**lehetőséget.

    d. Válassza a **bejövő Port hozzáadása szabály** a virtuális gép alatt lehetőséget.

    e. Adjon hozzá egy bejövő biztonsági szabályt a következő portokhoz:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) a weblapok kiszolgálókról történő kézbesítéséhez használt protokoll. Az ügyfelek HTTP-n keresztül csatlakoznak a DNS-név vagy IP-cím használatával. |
    | 443 | HTTPS | A Hypertext Transfer Protocol Secure (HTTPS) a HTTP olyan biztonságos verziója, amelynek biztonsági tanúsítványra van szüksége, és lehetővé teszi az adatok titkosított átvitelét.  |
    | 22 | SSH | A Secure Shell (SSH) egy titkosított hálózati protokoll a biztonságos kommunikációhoz. Ezt a kapcsolódást egy SSH-ügyféllel fogja használni a virtuális gép konfigurálásához és az alkalmazás üzembe helyezéséhez. |
    | 3389 | RDP | Nem kötelező. A RDP protokoll lehetővé teszi, hogy a távoli asztali kapcsolat grafikus felhasználói felületet használjon a gépen.   |
    | 8172 | Egyéni | A webdeploy által használt port. |

    Minden porthoz:

    a. A **forrás**mezőben válassza a **bármelyik**lehetőséget.

    b. A **forrásport-tartomány**mezőbe írjon be egy csillag **\*** () karaktert.

    c. A **cél**mezőben válassza a **bármelyik**lehetőséget.

    d. A **célport tartománya**mezőben adja meg a megnyitni kívánt portot.

    e. A **protokoll**területen válassza **a bármelyik**lehetőséget.

    f. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.

    g. A **prioritás**beállításnál hagyja meg az alapértelmezett beállítást.

    h. Adjon meg egy **nevet** és egy **leírást** , amely segít megjegyeznünk, hogy a port miért van nyitva.

    i. Válassza a **Hozzáadás** lehetőséget.

1.  A Azure Stackben lévő virtuális gép **hálózati** beállításainál hozzon létre egy DNS-nevet a kiszolgáló számára. A felhasználók az URL-cím használatával csatlakozhatnak a webhelyhez.

    a. Nyissa meg a Azure Stack portált a bérlő számára.

    b. Keresse meg a virtuális gépet. Lehet, hogy rögzítette a virtuális gépet az irányítópulton, vagy megkeresi az **erőforrások keresése** mezőben.

    c. Válassza az **Áttekintés** lehetőséget.

    d. A **virtuális gép**területen válassza a **Konfigurálás**lehetőséget.

    e. A **hozzárendelés**beállításnál válassza a **dinamikus**lehetőséget.

    f. Adja meg a DNS-név címkéjét (például **mywebapp**), így a teljes URL-cím *mywebapp. local. cloudapp. azurestack. external*lesz.

## <a name="create-an-app"></a>Alkalmazás létrehozása 

A Visual Studióval](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
)használhatja a saját webalkalmazást, vagy [egy ASP.net Core alkalmazás közzétételét az Azure-ban. A cikk azt ismerteti, hogyan hozhat létre és tehet közzé egy ASP.NET-webalkalmazást egy Azure-beli virtuális gépen a Visual Studio 2017 Azure Virtual Machines Publishing funkciójának használatával. Miután telepítette és ellenőrizte, hogy az alkalmazás helyileg fut-e, frissítenie kell a közzétételi célt a Windows rendszerű virtuális gépre a Azure Stack-példányban.

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

Hozzon létre egy közzétételi célt a virtuális géphez Azure Stack-ben.

1. **Megoldáskezelő**kattintson a jobb gombbal a projektre, majd válassza a **Közzététel**lehetőséget.

    ![ASP.NET-webalkalmazás üzembe helyezése Azure Stack közzétételhez](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

1. A **közzétételi** ablakban válassza az **új profil**lehetőséget.
1. Válassza **az IIS, FTP stb**. lehetőséget.
1. Kattintson a **Publish** (Közzététel) elemre.
1. **Közzétételi módszer**esetén válassza a **web Deploy (webes üzembe helyezés**) lehetőséget.
1. A **kiszolgáló** mezőben adja meg a korábban megadott DNS-nevet, például: *w21902. local. cloudapp. azurestack. external*.
1. A **hely neve**mezőbe írja be az **alapértelmezett**webhely értéket.
1. A **Felhasználónév**mezőben adja meg a számítógép felhasználónevét.
1. A **Password (jelszó**) mezőben adja meg a gép jelszavát.
1. A **cél URL-cím**mezőben adja meg a webhely URL-címét, például: *mywebapp. local. cloudapp. azurestack. external*.

    ![ASP.NET-webalkalmazás üzembe helyezése – webes telepítés konfigurálása](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

1. A web-Deploy konfiguráció ellenőrzéséhez válassza a **kapcsolat ellenőrzése**lehetőséget, majd kattintson a **tovább**gombra.
1. **Konfiguráció** beállítása **kiadásként**.
1. Állítsa be a **cél keretrendszert** **netcoreapp 2.2**-ként.
1. **Cél futtatókörnyezet** beállítása **hordozhatóként**.
1. Kattintson a **Mentés** gombra.
1. Kattintson a **Publish** (Közzététel) elemre.
1. Nyissa meg az új kiszolgálót. Látnia kell a futó webalkalmazást.

    ```http  
        mywebapp.local.cloudapp.azurestack.external
    ```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [állíthat be fejlesztési környezetet a Azure Stackban](azure-stack-dev-start.md).
- Ismerkedjen meg a [Azure stack IaaS való gyakori központi telepítésekkel](azure-stack-dev-start-deploy-app.md).
- A C# programozási nyelv megismeréséhez és a további erőforrásainak C#megkereséséhez [ C# ](https://docs.microsoft.com/dotnet/csharp/) tekintse meg a következő útmutatót:
