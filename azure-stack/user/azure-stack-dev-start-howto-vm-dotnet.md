---
title: Üzembe helyezése egy C# ASP .net webalkalmazás, egy virtuális géphez az Azure Stackben |} A Microsoft Docs
description: Üzembe helyezése egy C# egy virtuális géphez az Azure Stack ASP.net-webalkalmazásban.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 3f925d7c6a7f08257dbcb054044403e1488d38cb
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482126"
---
# <a name="how-to-deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack"></a>Hogyan helyezhet üzembe egy C# egy virtuális géphez az Azure Stack ASP.net-webalkalmazás

Létrehozhat egy Virtuálisgép-gazdagépre a C# (ASP.NET) webalkalmazás létrehozása az Azure Stack. Ez a cikk a szükséges lépések fogja a kiszolgálót, állít be a gazdagép-kiszolgáló konfigurálása megvizsgálja a C# (ASP.NET) webalkalmazás, és üzembe kell helyezni az alkalmazást közvetlenül a Visual Studióból.

C#egy általános célú, több paradigmát programozási nyelv kitérve erős beírni, betűrendbe hatókörrel rendelkező, feltétlenül szükséges, deklaratív, működési, általános, objektumorientált, és programozási oktatnak összetevő-orientált van. Ismerje meg, hogy a C# programozási nyelvet, és keresse meg a további erőforrások az C#, tekintse meg a [ C# útmutató](https://docs.microsoft.com/dotnet/csharp/).

Ebben a cikkben egy C# 6.0 alkalmazást egy Windows 2016-kiszolgálón futó ASP.NET Core 2.2 használatával.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Hozzon létre egy [Windows Server virtuális gép](azure-stack-quick-windows-portal.md).

2. Futtassa a következő szkriptet a virtuális gép összetevőinek telepítését. A parancsfájlt:
      - Telepítse az IIS (a felügyeleti konzol).
      - Install ASP.NET 4.6.

        ```PowerShell  
        # Install IIS (with Management Console)
        Install-WindowsFeature -name Web-Server -IncludeManagementTools
        
        # Install ASP.NET 4.6
        Install-WindowsFeature Web-Asp-Net45
        
        # Install Web Management Service
        Install-WindowsFeature -Name Web-Mgmt-Service
        ```

3. Töltse le a [webes telepítéshez 3.6-os MSI](https://www.microsoft.com/download/details.aspx?id=43717). Az MSI telepítése, és engedélyeznie kell az összes funkcióját.

4. Telepítse a .NET Core-üzemeltetési csomag a 2.2-es a kiszolgálón. A lépésekért lásd: [.NET Core-telepítő](https://dotnet.microsoft.com/download/dotnet-core/2.2). Ellenőrizze, hogy a .NET Core ugyanazt a verzióját használja a fejlesztői gépén és a célkiszolgálón is.

5. Térjen vissza az Azure Stack portálon, és nyissa meg a portokat a virtuális gép hálózati beállításait.

    1. Nyissa meg az Azure Stack portálon a bérlő számára.
    2. A virtuális gép található. Előfordulhat, hogy rendelkezik a virtuális gép az irányítópulton rögzítve, vagy Ön is megkeresheti a virtuális géphez a **erőforrások keresése** mezőbe.
    3. Válassza ki **hálózatkezelés**.
    4. Válassza ki **bejövőport-szabály hozzáadása** virtuális gép alapján.
    1. Adjon hozzá egy bejövő biztonsági szabályt a következő portokat:

    | Port | Protocol | Leírás |
    | --- | --- | --- |
    | 80 | HTTP | Hypertext Transfer Protocol (HTTP) az alkalmazásprotokoll elosztott, együttműködő, hipermédia információs rendszerekhez. Az ügyfelek a webalkalmazás nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 443 | HTTPS | Hypertext Transfer Protocol biztonságos (HTTPS) az kiterjesztése a Hypertext Transfer Protocol (HTTP). A számítógép hálózaton szolgál a biztonságos kommunikáció érdekében. Az ügyfelek a web Apps, és a nyilvános IP- vagy DNS-nevét, illetve a virtuális gép fog csatlakozni. |
    | 22 | SSH | Secure Shell (SSH) egy olyan titkosítási hálózati protokoll, a hálózati szolgáltatások biztonságos működő-nem biztonságos hálózaton keresztül. Az SSH-ügyfelet konfigurálja a virtuális Gépet, és az alkalmazás üzembe helyezéséhez használandó ehhez a kapcsolathoz. |
    | 3389 | RDP | Választható. A távoli asztali protokoll lehetővé teszi egy távoli asztali kapcsolatot egy grafikus felhasználói felületen a gép.   |

    Minden egyes porthoz:

    1. Válassza ki **bármely** forrás.
    1. Típus `*` a forrásport-tartományt.
    1. Válassza ki **bármely** a **cél**.
    1. Adja hozzá a port, amelyet szeretne megnyitni **Célporttartomány**.
    1. Válassza ki **bármely** a **protokoll**.
    1. Válassza ki **engedélyezése** a **művelet**.
    1. Hagyja bejelölve az alapértelmezett **prioritású**.
    1. **Név** , és adja meg egy **leírás** , hogy egy jegyezze meg, miért nyitotta meg a portot.
    1. Válassza a hozzáadása.

5.  Az a **hálózatkezelés** az Azure Stackben, a virtuális gép beállításait, hozzon létre egy DNS-nevet a kiszolgáló számára. Felhasználók csatlakozhatnak a webhelyhez, az URL-cím használatával.

    1. Nyissa meg az Azure Stack portálon a bérlő számára.
    1. A virtuális gép található. Előfordulhat, hogy rendelkezik a virtuális gép az irányítópulton rögzítve, vagy Ön is megkeresheti a virtuális géphez a **erőforrások keresése** mezőbe.
    1. Válassza az **Áttekintés** lehetőséget.
    1. Válassza ki **konfigurálása** virtuális gép alapján.
    1. Válassza ki **dinamikus** a **hozzárendelés**.
    1. Írja be például a DNS-névcímke `mywebapp` úgy, hogy a teljes URL-cím lesz hasonló: `mywebapp.local.cloudapp.azurestack.external`.

## <a name="create-an-app"></a>Alkalmazás létrehozása 

Használhatja a saját webes alkalmazás vagy a következő példa [ASP.NET Core-alkalmazás közzététele az Azure-bA a Visual Studióval](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
).

A cikk bemutatja, hogyan hozhat létre, és a egy ASP.NET-alkalmazás egy Azure virtuális gép (VM) a Microsoft Azure Virtual Machines-közzétételi szolgáltatás használatával a Visual Studio 2017-ben való közzététele. Miután telepítette, és ellenőrizze az alkalmazás helyileg fut, a közzététel célpontja szeretne frissíteni a Windows virtuális gép az Azure zsák a.

## <a name="deploy-and-run-the-app"></a>Az alkalmazás üzembe helyezése és futtatása

A közzétételi célként a virtuális gép létrehozása az Azure Stackben.

1. Kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza ki **közzététel**.

    ![ASP.NET-webalkalmazás üzembe helyezése az Azure Stackhez közzététele](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

2.  A közzététel ablakban válassza ki az új profilt.
3. Válassza ki az IIS, FTP stb.
4. Válassza ki a közzé.

5.  Válassza ki **Web Deploy** számára a **metódus közzététele**.
6.  A **kiszolgáló** a korábban meghatározott DNS-nevet adja meg például a `w21902.local.cloudapp.azurestack.external`
7.  A **helynév** típus `Default Web Site`.
8.  A **felhasználónév** írja be a felhasználónevet, a gép.
9.  A **jelszó**, írja be a jelszót a gép.
10. A **cél URL-címe** írja be például a webhely URL-Címének `mywebapp.local.cloudapp.azurestack.external`.

    ![ASP.NET-webalkalmazás üzembe helyezése – Web Deploy konfigurálása](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

9. Válassza ki **-kapcsolat ellenőrzése** ellenőrzése az a web deploy konfigurációja. majd **tovább**.
10. Állítsa be a **konfigurációs** , **kiadási**.
11. Állítsa be **keretrendszer cél** , **netcoreapp2.2**.
12. Állítsa be **cél futtatókörnyezet** , **hordozható**.
13. Kattintson a **Mentés** gombra.
14. Kattintson a **Publish** (Közzététel) elemre.
15. Keresse meg az új kiszolgálóra. Megtekintheti a futó webalkalmazás.

```HTTP  
    mywebapp.local.cloudapp.azurestack.external
```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [fejlesztés az Azure Stackhez](azure-stack-dev-start.md)
- Ismerje meg [közös üzemelő példányok az Azure stack-beli iaas](azure-stack-dev-start-deploy-app.md).