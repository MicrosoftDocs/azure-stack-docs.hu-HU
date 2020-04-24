---
title: App Service elforgatása Azure Stack hub-titkokon és-tanúsítványokon
description: Megtudhatja, hogyan forgathatja el a Azure App Service által használt titkokat és tanúsítványokat Azure Stack hub-on
author: apwestgarth
ms.topic: article
ms.date: 04/09/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/09/2020
ms.openlocfilehash: edbc0a0e9d0382f53503dddf4362a371b6369ebc
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81005399"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>App Service elforgatása Azure Stack hub-titkokon és-tanúsítványokon

Ezek az utasítások csak az Azure Stack hub Azure App Serviceére vonatkoznak.  A Azure App Service Azure Stack hub-titkokon való elforgatása nem része a Azure Stack hub központi titkos elforgatási eljárásának.  A kezelők a rendszeren belüli titkos kódok érvényességét, a legutóbbi frissítés dátumát és a titkok lejárta előtt hátralévő időt tudják figyelni.

> [!Important]
> Az operátorok nem kapják meg a titkos kulcs lejáratára vonatkozó riasztásokat az Azure Stack hub irányítópultján, mivel az Azure Stack hub-beli Azure App Service nincs integrálva az Azure Stack hub riasztási szolgáltatásával.  A kezelőknek rendszeresen meg kell figyelniük a titkot az Azure Stack hub felügyeleti portálján Azure Stack hub felügyeleti felületének Azure App Service használatával.

Ez a dokumentum a következő titkok elforgatásának eljárását tartalmazza:

* A Azure Stack hub Azure App Serviceon belül használt titkosítási kulcsok;
* Az Azure Stack hub Azure App Service által használt adatbázis-kapcsolódási hitelesítő adatok az üzemeltetési és mérési adatbázisokkal való kommunikációhoz;
* Az Azure Stack hub Azure App Service által használt tanúsítványok az Identity Application-tanúsítvány végpontjának és rotációjának biztonságossá tételéhez Azure Active Directory (Azure AD) vagy Active Directory összevonási szolgáltatások (AD FS) (ADFS)
* Rendszerhitelesítő adatok Azure App Service Azure Stack hub-infrastruktúra szerepköreihez.

## <a name="rotate-encryption-keys"></a>Titkosítási kulcsok elforgatása

A Azure Stack hub Azure App Serviceon belül használt titkosítási kulcsok elforgatásához hajtsa végre a következő lépéseket:

1. Lépjen a App Service felügyeleti felületre az Azure Stack hub rendszergazdák portálján.

1. Navigáljon a **titkok** menüpontra

1. Kattintson a **forgatás** gombra a titkosítási kulcsok szakaszban.

1. Az elforgatási eljárás elindításához kattintson **az OK** gombra.

1. A rendszer elforgatja a titkosítási kulcsokat, és frissíti az összes szerepkör-példányt. A kezelők az **állapot** gomb használatával tudják figyelni az eljárás állapotát.

## <a name="rotate-connection-strings"></a>A kapcsolatok karakterláncának elforgatása

A App Service üzemeltetési és mérési adatbázisokhoz tartozó adatbázis-kapcsolódási karakterlánc hitelesítő adatainak frissítéséhez hajtsa végre a következő lépéseket:

1. Lépjen a App Service felügyeleti felületre az Azure Stack hub rendszergazdák portálján.

1. Navigáljon a **titkok** menüpontra

1. Kattintson a **forgatás** gombra a kapcsolatok karakterláncok szakaszban

1. Adja meg az **SQL SA felhasználónevét** és **jelszavát** , majd kattintson az **OK** gombra az elforgatási eljárás elindításához. 

1. A hitelesítő adatokat a rendszer a Azure App Service szerepkör példányaiban fogja elforgatni. A kezelők az **állapot** gomb használatával tudják figyelni az eljárás állapotát.

## <a name="rotate-certificates"></a>Tanúsítványok váltása

A Azure Stack hub Azure App Serviceon belül használt tanúsítványok elforgatásához hajtsa végre a következő lépéseket:

1. Lépjen a App Service felügyeleti felületre az Azure Stack hub rendszergazdák portálján.

1. Navigáljon a **titkok** menüpontra

1. Kattintson a **forgatás** gombra a tanúsítványok szakaszban

1. Adja meg az elforgatni kívánt tanúsítványok **tanúsítványának fájlját** és a hozzá tartozó **jelszót** , majd kattintson **az OK**gombra.

1. A tanúsítványokat a rendszer szükség szerint elforgatja a Azure App Service Azure Stack hub-szerepkör példányain.  A kezelők az **állapot** gomb használatával tudják figyelni az eljárás állapotát.

Az Identity Application-tanúsítvány elforgatásakor a HRE-ben vagy az ADFS-ben lévő megfelelő alkalmazást is frissíteni kell az új tanúsítvánnyal.

> [!IMPORTANT]
> Ha nem sikerül frissíteni az Identity alkalmazást az új tanúsítvánnyal, az elforgatás után megszakítja a felhasználói portál felületét Azure Functions, megakadályozhatja, hogy a felhasználók használhassanak a KUDU fejlesztői eszközöket, és megakadályozzák, hogy a rendszergazdák a App Service felügyeleti kezelőfelületen tudják kezelni a feldolgozói rétegek méretezési csoportjait.

### <a name="rotate-credential-for-the-azure-ad-identity-application"></a>Az Azure AD Identity alkalmazás hitelesítő adatainak elforgatása 

Az Identity alkalmazást az operátor hozza létre a Azure App Service Azure Stack hub-beli üzembe helyezése előtt.  Ha az alkalmazás azonosítója ismeretlen, kövesse az alábbi lépéseket a felderítéséhez:

1. Ugrás az **Azure stack hub felügyeleti portálra**

1. Ugrás az **előfizetésekre** és az **alapértelmezett szolgáltatói előfizetés** kiválasztása

1. Válassza a **Access Control (iam)** lehetőséget, és válassza ki a **app Service** alkalmazást

1. Jegyezze fel az **alkalmazás azonosítóját**, ez az érték az Identity alkalmazás azon alkalmazás-azonosítója, amelyet frissíteni kell Azure Active Directoryban.

Az alkalmazás tanúsítványának Active Directoryban való elforgatásához kövesse az alábbi lépéseket:

1. Lépjen a **Azure Portalra** , és jelentkezzen be az Azure stack hub telepítéséhez használt globális rendszergazda használatával

1. Nyissa meg **Azure Active Directory** és keresse meg az **alkalmazás regisztrációját**

1. Keresse meg az **alkalmazás azonosítóját**, és határozza meg az azonosító alkalmazás azonosítóját.

1. Válassza ki az alkalmazást, majd lépjen a **tanúsítványok & Secrets** elemre.

1. Kattintson a **tanúsítvány feltöltése** lehetőségre, és töltse fel az Identity Application új tanúsítványát az alábbi fájltípusok egyikével:. cer,. PEM,. CRT

1. Erősítse meg, hogy a Azure Stack hub felügyeleti portálján a App Service felügyeleti felületén felsorolt **ujjlenyomat** -egyezések szerepelnek.

1. A régi tanúsítvány törlése

### <a name="rotate-certificate-for-adfs-identity-application"></a>A tanúsítvány elforgatása az ADFS Identity Application alkalmazáshoz

Az Identity alkalmazást az operátor hozza létre a Azure App Service Azure Stack hub-beli üzembe helyezése előtt.  Ha az alkalmazás objektumazonosító ismeretlen, az alábbi lépéseket követve derítheti fel:

1. Ugrás az **Azure stack hub felügyeleti portálra**

1. Ugrás az **előfizetésekre** és az **alapértelmezett szolgáltatói előfizetés** kiválasztása

1. Válassza a **Access Control (iam)** lehetőséget, és válassza ki a **AzureStack-AppService-<guid> ** Application elemet

1. Jegyezze fel az **objektumazonosító**értékét, ez az érték annak az egyszerű szolgáltatásnak az azonosítója, amelyet frissíteni kell az ADFS-ben.

Az alkalmazás tanúsítványának az ADFS-ben való elforgatásához hozzáféréssel kell rendelkeznie a privilegizált végponthoz (PEP), majd frissítenie kell a tanúsítvány hitelesítő adatait a PowerShell használatával, és be kell helyettesítenie a saját értékeit a következő helyőrzők esetében:

| Helyőrző | Leírás | Példa |
| ----------- | ----------- | ------- |
| \<PepVM\> | A rendszerjogosultságú végpont virtuális gép neve a Azure Stack hub-példányon. | "AzS-ERCS01" |
| \<CertificateFileLocation\> | A X509-tanúsítvány helye a lemezen. | "d:\certs\sso.cer" |
| \<ApplicationObjectId\> | Az Identity alkalmazáshoz rendelt azonosító. | "S-1-5-21-401916501-2345862468-1451220656-1451" |

 1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet, és futtassa a következő parancsfájlt

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

1. Lépjen a App Service felügyeleti felületre az Azure Stack hub rendszergazdák portálján.

1. Navigáljon a **titkok** menüpontra

1. Kattintson a **forgatás** gombra a rendszer hitelesítő adatai szakaszban.

1. Válassza ki a használt rendszer hitelesítő adatainak **hatókörét** .  A kezelők dönthetnek úgy, hogy elforgatják a rendszer hitelesítő adatait az összes szerepkörhöz vagy egyedi szerepkörhöz.

1. Adjon meg egy **új helyi rendszergazda felhasználónevet**és új **jelszót** , majd erősítse meg a **jelszót** , és kattintson **az OK** gombra.

1. A hitelesítő adatokat a rendszer szükség szerint elforgatja a Azure Stack hub szerepkör-példány megfelelő Azure App Service.  A kezelők az **állapot** gomb használatával tudják figyelni az eljárás állapotát.



