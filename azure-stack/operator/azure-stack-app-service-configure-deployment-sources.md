---
title: Központi telepítési források konfigurálása a App Serviceshoz Azure Stack hub-on | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja az üzembe helyezési forrásokat (git, GitHub, BitBucket, DropBox és OneDrive) Azure Stack hub App Services.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 0115e726e8922b94eae437cb76e23f4e77199d97
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75880906"
---
# <a name="configure-deployment-sources-for-app-services-on-azure-stack-hub"></a>Központi telepítési források konfigurálása App Serviceshoz Azure Stack hub-on

A App Service on Azure Stack hub több forrásoldali vezérlő szolgáltatótól is támogatja az igény szerinti telepítést. Ez a funkció lehetővé teszi, hogy az alkalmazások fejlesztői közvetlenül a forrásoldali vezérlő tárházból telepítsenek. Ha a felhasználók App Servicet szeretnének konfigurálni a tárházhoz való csatlakozáshoz, akkor a Felhőbeli operátornak először konfigurálnia kell az integrációt az Azure Stack hub és a forrásoldali vezérlő szolgáltatója App Service között.  

A helyi git mellett a következő verziókövetés-szolgáltatók is támogatottak:

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Központi telepítési források megtekintése App Service felügyeletben

1. Jelentkezzen be az Azure Stack hub felügyeleti portálra (https://adminportal.local.azurestack.external) szolgáltatás-rendszergazdaként.
2. Tallózással keresse meg az **összes szolgáltatást** , és válassza ki a **app Service**.
    ![App Service erőforrás-szolgáltatói rendszergazda][1]
3. Kattintson a **verziókövetés konfigurációja**elemre. Láthatja az összes konfigurált üzembe helyezési forrás listáját.
    ![App Service erőforrás-szolgáltató felügyeleti forrásának vezérlési konfigurációja][2]

## <a name="configure-github"></a>GitHub konfigurálása

A feladat elvégzéséhez GitHub-fiókkal kell rendelkeznie. Személyes fiók helyett érdemes lehet egy fiókot használni a szervezet számára.

1. Jelentkezzen be a GitHubba, keresse meg https://www.github.com/settings/developers , majd kattintson **az új alkalmazás regisztrálása**lehetőségre.
    ![GitHub – új alkalmazás regisztrálása][3]
2. Adja meg az **alkalmazás nevét**. Például **App Service Azure stack hub-on**.
3. Adja meg a **Kezdőlap URL-címét**. A Kezdőlap URL-címének a Azure Stack hub portáljának kell lennie. Például: https://portal.local.azurestack.external.
4. Adja meg az **alkalmazás leírását**.
5. Adja meg az **engedélyezési visszahívási URL-címet**. Egy alapértelmezett Azure Stack hub-telepítésben az URL-cím a https://portal.local.azurestack.external/TokenAuthorize formában van. Ha másik tartományban fut, váltson a helyi. azurestack. external tartomány nevére.
6. Kattintson az **alkalmazás regisztrálása**elemre. Megjelenik egy lap, amely felsorolja az alkalmazás **ügyfél-azonosítóját** és az **ügyfél titkos kulcsát** .
    ![GitHub – az alkalmazás regisztrációja befejeződött][5]
7. Az új böngésző lapon vagy ablakban jelentkezzen be az Azure Stack hub felügyeleti portálra (https://adminportal.local.azurestack.external) szolgáltatás-rendszergazdaként.
8. Tallózással keresse meg az **erőforrás**-szolgáltatókat, majd válassza ki a **app Service erőforrás-szolgáltatói rendszergazdát**.
9. Kattintson a **verziókövetés konfigurációja**elemre.
10. Másolja és illessze be az **ügyfél-azonosítót** és az **ügyfél titkát** a GitHub megfelelő beviteli mezőibe.
11. Kattintson a **Mentés** gombra.

## <a name="configure-bitbucket"></a>BitBucket konfigurálása

A feladat végrehajtásához rendelkeznie kell egy BitBucket-fiókkal. Személyes fiók helyett érdemes lehet egy fiókot használni a szervezet számára.

1. Jelentkezzen be a BitBucket-be, és keresse meg a fiókjában található **integrációkat** .
    ![BitBucket-irányítópult – integrációk][7]
2. Kattintson a **OAuth** elemre a hozzáférés-kezelés területen, és **adjon hozzá fogyasztót**.
    ![BitBucket OAuth-fogyasztó hozzáadása][8]
3. Adja meg a fogyasztó **nevét** . Például **App Service Azure stack hub-on**.
4. Adja meg az alkalmazás **leírását** .
5. Adja meg a **visszahívási URL-címet**. Az Azure Stack hub alapértelmezett központi telepítése során a visszahívási URL-cím a https://portal.local.azurestack.external/TokenAuthorize formában van. Ha másik tartományban fut, a tartománynevet a azurestack. local névre cserélje. Ahhoz, hogy a BitBucket-integráció sikeres legyen, az URL-címnek követnie kell az itt felsorolt nagybetűket.
6. Adja meg az **URL-címet**. Az URL-címnek az Azure Stack hub portál URL-címének kell lennie. Például: https://portal.local.azurestack.external.
7. Válassza ki a szükséges **engedélyeket** :
    - **Adattárak**: *olvasás*
    - **Webhookok**: *olvasás és írás*
8. Kattintson a **Mentés** gombra. Ekkor megjelenik az új alkalmazás, valamint a **kulcs** és a **titok**a OAuth- **fogyasztók**területen.
    ![BitBucket][9]
9.  Az új böngésző lapon vagy ablakban jelentkezzen be az Azure Stack hub felügyeleti portálra (https://adminportal.local.azurestack.external) szolgáltatás-rendszergazdaként.
10.  Keresse meg az **erőforrás** -szolgáltatót, és válassza ki a **app Service erőforrás-szolgáltatói rendszergazdát**.
11. Kattintson a **verziókövetés konfigurációja**elemre.
12. Másolja ki és illessze be a kulcsot az **ügyfél-azonosító** beviteli mezőbe, és **titkos** **kódot** a BitBucket **ügyfél titkos** beviteli mezőjébe.
13. Kattintson a **Mentés** gombra.

## <a name="configure-onedrive"></a>OneDrive konfigurálása

A feladat elvégzéséhez egy OneDrive-fiókhoz csatolt Microsoft-fiókkal kell rendelkeznie.  Személyes fiók helyett érdemes lehet egy fiókot használni a szervezet számára.

> [!NOTE]
> A OneDrive for Business-fiókok jelenleg nem támogatottak.

1. Keresse meg https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm , és jelentkezzen be Microsoft-fiókjával.
2. **Az alkalmazások**területen kattintson az **alkalmazás hozzáadása**elemre.
![OneDrive-alkalmazások][10]
3. Adja meg az új alkalmazás regisztrációjának **nevét** : írja be a **app Serviceot Azure stack hub-ra**, majd kattintson az **alkalmazás létrehozása**elemre.
4. A következő képernyő felsorolja az új alkalmazás tulajdonságait. Mentse az **alkalmazás azonosítóját** néhány ideiglenes helyre.
![OneDrive-alkalmazás tulajdonságai][11]
5. Az **Alkalmazáskulcsok** alatt kattintson az **Új jelszó generálása** lehetőségre. Jegyezze fel a **létrehozott új jelszót**. Ez a jelszó az alkalmazás titkos kulcsa, és az **OK**gombra kattintás után nem lehet lekérni.
6. A **platformok**területen kattintson a **platform hozzáadása**, majd a **web**elemre.
7. Adja meg az **átirányítási URI**-t. Az Azure Stack hub alapértelmezett központi telepítése során az átirányítási URI a https://portal.local.azurestack.external/TokenAuthorize formában van. Ha egy másik tartományban fut, váltson át a azurestack. local névre.
![OneDrive alkalmazás – webes platform hozzáadása][12]
8. Adja hozzá a **Microsoft Graph engedélyeket** - **delegált engedélyekhez**.
    - **Files. ReadWrite. AppFolder**
    - **Felhasználói. Olvasni**  
      ![OneDrive alkalmazás – Graph-engedélyek][13]
9. Kattintson a **Mentés** gombra.
10.  Az új böngésző lapon vagy ablakban jelentkezzen be az Azure Stack hub felügyeleti portálra (https://adminportal.local.azurestack.external) szolgáltatás-rendszergazdaként.
11.  Keresse meg az **erőforrás** -szolgáltatót, és válassza ki a **app Service erőforrás-szolgáltatói rendszergazdát**.
12. Kattintson a **verziókövetés konfigurációja**elemre.
13. Másolja és illessze be az **alkalmazás azonosítóját** az **ügyfél-azonosító** beviteli mezőbe, és adja meg a **jelszót** a OneDrive **ügyfél titkos** beviteli mezőjében.
14. Kattintson a **Mentés** gombra.

## <a name="configure-dropbox"></a>A DropBox konfigurálása

> [!NOTE]
> A feladat elvégzéséhez DropBox-fiókkal kell rendelkeznie. Személyes fiók helyett érdemes lehet egy fiókot használni a szervezet számára.

1. Keresse meg https://www.dropbox.com/developers/apps , és jelentkezzen be a DropBox-fiókja hitelesítő adataival.
2. Kattintson az **alkalmazás létrehozása**elemre.

    ![Dropbox-alkalmazások][14]

3. Válassza a **Dropbox API**elemet.
4. Állítsa be a hozzáférési szintet az **alkalmazás mappájába**.
5. Adja meg az alkalmazás **nevét** .
![Dropbox-alkalmazás regisztrációja][15]
6. Kattintson az **alkalmazás létrehozása**elemre. Megjelenik egy olyan oldal, amely felsorolja az alkalmazás beállításait, beleértve az **alkalmazás kulcsát** és az **alkalmazás titkos**kódját is.
7. Győződjön meg arról, hogy az **alkalmazás mappájának neve** **Azure stack hub app Service**értékre van beállítva.
8. Állítsa be a **OAuth 2 átirányítási URI** -t, majd kattintson a **Hozzáadás**gombra. Az Azure Stack hub alapértelmezett központi telepítése során az átirányítási URI a https://portal.local.azurestack.external/TokenAuthorize formában van. Ha másik tartományban fut, váltson a tartományra a azurestack. local számára.
![Dropbox alkalmazás konfigurációjának][16]
9.  Az új böngésző lapon vagy ablakban jelentkezzen be az Azure Stack hub felügyeleti portálra (https://adminportal.local.azurestack.external) szolgáltatás-rendszergazdaként.
10.  Keresse meg az **erőforrás** -szolgáltatót, és válassza ki a **app Service erőforrás-szolgáltatói rendszergazdát**.
11. Kattintson a **verziókövetés konfigurációja**elemre.
12. Másolja és illessze be az **alkalmazás kulcsát** az **ügyfél-azonosító** beviteli mezőbe és az **alkalmazás titkos** kódját a Dropbox **ügyfél titkos** beviteli mezőjébe.
13. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

A felhasználók mostantól használhatják a központi telepítési forrásokat olyan dolgokhoz, mint a [folyamatos üzembe helyezés](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment), a [helyi git-telepítés](https://docs.microsoft.com/azure/app-service/deploy-local-git)és a [Felhőbeli mappák szinkronizálása](https://docs.microsoft.com/azure/app-service/deploy-content-sync).

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png
