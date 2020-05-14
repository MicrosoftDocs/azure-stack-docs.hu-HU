---
title: App Service elforgatása Azure Stack hub-titkokon és-tanúsítványokon
description: Megtudhatja, hogyan forgathatja el a Azure App Service által használt titkokat és tanúsítványokat Azure Stack hub-on.
author: apwestgarth
ms.topic: article
ms.date: 04/09/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/09/2020
ms.openlocfilehash: 3a39329ba8c99e919b2466705553e11b49424c45
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374732"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>App Service elforgatása Azure Stack hub-titkokon és-tanúsítványokon

Ezek az utasítások csak az Azure Stack hub Azure App Serviceére vonatkoznak. A Azure App Service Azure Stack hub-titkokon való elforgatása nem része a Azure Stack hub központi titkos elforgatási eljárásának. A kezelők a rendszeren belüli titkos kódok érvényességét, a legutóbbi frissítés dátumát és a titkok lejárta előtt hátralévő időt is megfigyelheti.

> [!Important]
> A kezelők nem kapnak riasztásokat az Azure Stack hub irányítópulton a titkos lejáratra vonatkozóan, mivel az Azure Stack hub-beli Azure App Service nincs integrálva az Azure Stack hub riasztási szolgáltatásával. A kezelőknek rendszeresen meg kell figyelniük a titkot az Azure Stack hub felügyeleti portálon Azure Stack hub felügyeleti felületének Azure App Service használatával.

Ez a dokumentum a következő titkok elforgatásának eljárását tartalmazza:

* A Azure Stack hub Azure App Serviceon belül használt titkosítási kulcsok.
* Az Azure Stack hub Azure App Service által használt adatbázis-kapcsolódási hitelesítő adatok az üzemeltetési és mérési adatbázisokkal való kommunikációhoz.
* Az Azure Stack hub Azure App Service által használt tanúsítványok a Azure Active Directory (Azure AD) vagy a Active Directory összevonási szolgáltatások (AD FS) (AD FS) identitás-tanúsítványok végpontjának és elforgatásának biztosításához.
* Rendszerhitelesítő adatok Azure App Service Azure Stack hub-infrastruktúra szerepköreihez.

## <a name="rotate-encryption-keys"></a>Titkosítási kulcsok elforgatása

A Azure Stack hub Azure App Serviceon belül használt titkosítási kulcsok elforgatásához hajtsa végre a következő lépéseket:

1. Nyissa meg a App Service felügyeleti felületét az Azure Stack hub felügyeleti portálján.

1. Lépjen a **titkok** menüpontra.

1. Kattintson a **forgatás** gombra a titkosítási kulcsok szakaszban.

1. Az elforgatási eljárás elindításához kattintson **az OK gombra** .

1. A rendszer elforgatja a titkosítási kulcsokat, és frissíti az összes szerepkör-példányt. Az operátor az **állapot** gomb használatával tudja megtekinteni az eljárás állapotát.

## <a name="rotate-connection-strings"></a>A kapcsolatok karakterláncának elforgatása

A App Service üzemeltetési és mérési adatbázisokhoz tartozó adatbázis-kapcsolódási karakterlánc hitelesítő adatainak frissítéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a App Service felügyeleti felületét az Azure Stack hub felügyeleti portálján.

1. Lépjen a **titkok** menüpontra.

1. Kattintson a **forgatás** gombra a kapcsolatok karakterláncok szakaszban.

1. Adja meg az **SQL SA felhasználónevét** és **jelszavát** , majd kattintson az **OK gombra** az elforgatási eljárás elindításához.

1. A hitelesítő adatokat a rendszer a Azure App Servicei szerepkör példányain keresztül forgatja el. Az operátor az **állapot** gomb használatával tudja megtekinteni az eljárás állapotát.

## <a name="rotate-certificates"></a>Tanúsítványok váltása

A Azure Stack hub Azure App Serviceon belül használt tanúsítványok elforgatásához hajtsa végre a következő lépéseket:

1. Nyissa meg a App Service felügyeleti felületét az Azure Stack hub felügyeleti portálján.

1. Lépjen a **titkok** menüpontra.

1. Kattintson a **forgatás** gombra a tanúsítványok szakaszban.

1. Adja meg az elforgatni kívánt tanúsítványok **tanúsítványát** és a hozzá tartozó **jelszót** , majd kattintson **az OK gombra**.

1. A tanúsítványokat a rendszer szükség szerint elforgatja a Azure App Service Azure Stack hub-szerepkör példányain. Az operátor az **állapot** gomb használatával tudja megtekinteni az eljárás állapotát.

Az Identity Application-tanúsítvány elforgatásakor az Azure AD-ben vagy AD FSban található megfelelő alkalmazást is frissíteni kell az új tanúsítvánnyal.

> [!IMPORTANT]
> Nem sikerült frissíteni az Identity alkalmazást az új tanúsítvánnyal, miután elforgatta a felhasználói portál felületét Azure Functions, megakadályozhatja, hogy a felhasználók használhassanak a KUDU fejlesztői eszközöket, és megakadályozzák, hogy a rendszergazdák a App Service felügyeleti felületről kezelhesse a feldolgozói rétegek méretezési csoportjait.

### <a name="rotate-credential-for-the-azure-ad-identity-application"></a>Az Azure AD Identity alkalmazás hitelesítő adatainak elforgatása

Az Identity alkalmazást az operátor hozza létre a Azure App Service Azure Stack hub-beli üzembe helyezése előtt. Ha az alkalmazás azonosítója ismeretlen, kövesse az alábbi lépéseket a felderítéséhez:

1. Nyissa meg az **Azure stack hub felügyeleti portált**.

1. Lépjen az **előfizetések** elemre, és válassza az **alapértelmezett szolgáltatói előfizetés**lehetőséget.

1. Válassza a **Access Control (iam)** lehetőséget, és válassza ki a **app Service** alkalmazást.

1. Jegyezze fel az **alkalmazás azonosítóját**, ez az érték a személyazonossági alkalmazás azon alkalmazás-azonosítója, amelyet frissíteni kell az Azure ad-ben.

Az alkalmazás tanúsítványának az Azure AD-ben való elforgatásához kövesse az alábbi lépéseket:

1. Lépjen a **Azure Portalra** , és jelentkezzen be az Azure stack hub telepítéséhez használt globális rendszergazda használatával.

1. Lépjen a **Azure Active Directoryra** , és keresse meg az **alkalmazások regisztrációját**.

1. Keresse meg az **alkalmazás azonosítóját**, majd adja meg az azonosító alkalmazás azonosítóját.

1. Válassza ki az alkalmazást, majd lépjen a **tanúsítványok & Secrets**elemre.

1. Válassza a **tanúsítvány feltöltése** lehetőséget, és töltse fel az Identity Application új tanúsítványát az alábbi fájltípusok egyikével:. cer,. PEM,. CRT.

1. Győződjön meg arról, hogy az **ujjlenyomat** megfelel a Azure stack hub felügyeleti portál app Service adminisztrációs felületén.

1. Törölje a régi tanúsítványt.

### <a name="rotate-certificate-for-ad-fs-identity-application"></a>AD FS Identity alkalmazás tanúsítványának elforgatása

Az Identity alkalmazást az operátor hozza létre a Azure App Service Azure Stack hub-beli üzembe helyezése előtt. Ha az alkalmazás objektumazonosító ismeretlen, az alábbi lépéseket követve derítheti fel:

1. Nyissa meg az **Azure stack hub felügyeleti portált**.

1. Lépjen az **előfizetések** elemre, és válassza az **alapértelmezett szolgáltatói előfizetés**lehetőséget.

1. Válassza a **Access Control (iam)** lehetőséget, és válassza ki a **AzureStack-AppService- <guid> ** Application elemet.

1. Jegyezze fel az **objektumazonosító**értékét, ez az érték annak az egyszerű szolgáltatásnak az azonosítója, amelyet AD FS kell frissíteni.

Az alkalmazás tanúsítványának AD FS-ben való elforgatásához hozzáféréssel kell rendelkeznie a privilegizált végponthoz (PEP). Ezután frissítse a tanúsítvány hitelesítő adatait a PowerShell használatával, és cserélje le a saját értékeit a következő helyőrzőre:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| `<PepVM>` | A rendszerjogosultságú végpont virtuális gép neve a Azure Stack hub-példányon. | "AzS-ERCS01" |
| `<CertificateFileLocation>` | A X509-tanúsítvány helye a lemezen. | "d:\certs\sso.cer" |
| `<ApplicationObjectId>` | Az Identity alkalmazáshoz rendelt azonosító. | "S-1-5-21-401916501-2345862468-1451220656-1451" |

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsfájlt:

    ```powershell
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint
    $Creds = Get-Credential

    # Create a new Certificate object from the identity application certificate exported as .cer file
    $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<CertificateFileLocation>")

    # Create a new PSSession to the PrivelegedEndpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with the App Service identity application
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<ApplicationObjectId>" -ClientCertificates $using:Cert}
    $Session | Remove-PSSession

    # Output the updated service principal details
    $SpObject

    ```

1. A parancsfájl befejeződése után megjeleníti a frissített alkalmazás-regisztrációs adatokat, beleértve a tanúsítvány ujjlenyomatának értékét is.

    ```shell
    ApplicationIdentifier : S-1-5-21-401916501-2345862468-1451220656-1451
    ClientId              : 
    Thumbprint            : FDAA679BF9EDDD0CBB581F978457A37BFD73CA3B
    ApplicationName       : Azurestack-AppService-d93601c2-1ec0-4cac-8d1c-8ccde63ef308
    ClientSecret          : 
    PSComputerName        : AzS-ERCS01
    RunspaceId            : cb471c79-a0d3-40ec-90ba-89087d104510
    ```

## <a name="rotate-system-credentials"></a>Rendszerszintű hitelesítő adatok elforgatása

A Azure App Service Azure Stack hub-on belül használt hitelesítő adatok elforgatásához hajtsa végre a következő lépéseket:

1. Nyissa meg a App Service felügyeleti felületét az Azure Stack hub felügyeleti portálján.

1. Lépjen a **titkok** menüpontra.

1. Kattintson a **forgatás** gombra a rendszerhitelesítő adatok szakaszban.

1. Válassza ki az Ön által forgó rendszerhitelesítő adatok **hatókörét** . A kezelők dönthetnek úgy, hogy elforgatják a rendszer hitelesítő adatait az összes szerepkörhöz vagy egyedi szerepkörhöz.

1. Adjon meg egy **új helyi rendszergazdai felhasználónevet** és egy új **jelszót**. Erősítse meg a **jelszót** , majd kattintson **az OK gombra**.

1. A hitelesítő adatokat a rendszer a szükséges módon elforgatja a megfelelő Azure App Service Azure Stack hub szerepkör-példányon. Az operátor az **állapot** gomb használatával tudja megtekinteni az eljárás állapotát.
