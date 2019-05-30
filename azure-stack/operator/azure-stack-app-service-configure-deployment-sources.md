---
title: Központi telepítés forrásának konfigurálása az App Services az Azure Stackben |} A Microsoft Docs
description: Hogyan szolgáltatás-rendszergazda képes központi telepítés forrásának konfigurálása (a Git, GitHub, BitBucket, DropBox és onedrive vállalati verzió) az App Services az Azure Stackben
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
ms.openlocfilehash: 8512568c709770f736d6740d83578dee7391adff
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269257"
---
# <a name="configure-deployment-sources"></a>Központi telepítés forrásának konfigurálása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack App Service támogatja az igény szerinti üzembe helyezést, több verziókövetési szolgáltatók az. Ez a funkció lehetővé teszi, hogy az alkalmazásfejlesztők forráskódtárházakból helyezheti üzembe közvetlenül a forrásból. Ha felhasználókat szeretne csatlakozni a tárházak az App Service konfigurálása, a felhő üzemeltetője először konfigurálnia kell az Azure Stack App Service-ben és a Forrásvezérlő-szolgáltatónál integrációjával.  

Helyi Git, valamint a következő verziókövetési szolgáltatók támogatottak:

* GitHub
* Bitbucket-alapú
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Az App Service-ben Adminisztráció központi telepítési források megtekintése

1. Jelentkezzen be az Azure Stack felügyeleti portálon (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
2. Keresse meg a **minden szolgáltatás** , és válassza ki a **App Service-ben**.
    ![Az App Service erőforrás-szolgáltató rendszergazda][1]
3. Kattintson a **verziókövetési konfiguráció**. Láthatja, hogy a konfigurált központi telepítési források felsorolása.
    ![Az App Service erőforrás szolgáltató rendszergazdai verziókövetési konfiguráció][2]

## <a name="configure-github"></a>Konfigurálja a Githubon

Ez a feladat végrehajtásához GitHub-fiókkal kell rendelkeznie. Érdemes egy személyes fiók, hanem a szervezet olyan fiókot használjon.

1. Jelentkezzen be a GitHub, keresse meg a https://www.github.com/settings/developers, és kattintson a **regisztrálhat új alkalmazásokat**.
    ![GitHub - egy új alkalmazás regisztrálása][3]
2. Adjon meg egy **alkalmazásnév**; például **App Service-ben az Azure Stacken**.
3. Adja meg a **kezdőlap URL-címe**. A kezdőlap URL-címe az Azure Stack portal címnek kell lennie. Például: https://portal.local.azurestack.external.
4. Adjon meg egy **alkalmazásleírás**.
5. Adja meg a **engedélyezési visszahívási URL-Címének**. Egy alapértelmezett Azure Stack üzemelő példányban, az URL-cím szerepel az űrlap https://portal.local.azurestack.external/TokenAuthorize. Ha egy másik tartományba futtatja, helyettesítse be a local.azurestack.external tartománynevére.
6. Kattintson a **alkalmazás regisztrálása**. Egy lap is megjelenik, listázás, a **ügyfél-azonosító** és **titkos Ügyfélkód** az alkalmazáshoz.
    ![GitHub - befejezett alkalmazás regisztrálása][5]
7.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure Stack felügyeleti portálon (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
8.  Keresse meg a **erőforrás-szolgáltatók**, és válassza ki a **App Service erőforrás szolgáltató rendszergazdai**.
9. Kattintson a **verziókövetési konfiguráció**.
10. Másolja és illessze be a **ügyfél-azonosító** és **titkos Ügyfélkód** be a megfelelő bemeneti állíthatunk be a github használatára.
11. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-bitbucket"></a>Bitbucket-alapú konfigurálása

Ez a feladat végrehajtásához egy BitBucket-fiókkal kell rendelkeznie. Érdemes egy személyes fiók, hanem a szervezet olyan fiókot használjon.

1. Jelentkezzen be a BitBucket, és keresse meg a **Integrációk** a fiókban.
    ![BitBucket irányítópult - Integrációk][7]
2. Kattintson a **OAuth** hozzáférés felügyelete alá, és **Hozzáadás fogyasztói**.
    ![Bitbucket-alapú OAuth fogyasztói hozzáadása][8]
3. Adjon meg egy **neve** a fogyasztók; például **App Service-ben az Azure Stacken**.
4. Adjon meg egy **leírás** az alkalmazáshoz.
5. Adja meg a **visszahívási URL-Címének**. Az alapértelmezett Azure Stack üzembe helyezés a visszahívási URL-cím neve a https://portal.local.azurestack.external/TokenAuthorize. Ha egy másik tartományba le, helyettesítse be a tartománynév azurestack.local. BitBucket-integráció sikeres az URL-cím az itt felsorolt nagybetűs kell követnie.
6. Adja meg a **URL-cím**. Az URL-címet kell lennie az Azure Stack portálon URL-cím; Ha például https://portal.local.azurestack.external.
7. Válassza ki a **engedélyek** szükséges:
    - **Tárházak**: *Olvasás*
    - **Webhookok**: *Olvasás és írás*
8. Kattintson a **Save** (Mentés) gombra. Az ekkor megjelenik az új alkalmazás a **kulcs** és **titkos**alatt **OAuth fogyasztók**.
    ![Bitbucket-alapú alkalmazás listázása][9]
9.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure Stack felügyeleti portálon (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
10.  Keresse meg a **erőforrás-szolgáltatók** , és válassza ki a **App Service erőforrás szolgáltató rendszergazdai**.
11. Kattintson a **verziókövetési konfiguráció**.
12. Másolja és illessze be a **kulcs** be a **ügyfél-azonosító** beviteli mezőt és **titkos** be a **titkos Ügyfélkód** beviteli mezőt a BitBucket.
13. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-onedrive"></a>Konfigurálja a onedrive vállalati verzió

Egy Microsoft Account befejezheti a feladatot egy OneDrive-fiókjához társított kell rendelkeznie.  Érdemes egy személyes fiók, hanem a szervezet olyan fiókot használjon.

> [!NOTE]
> OneDrive vállalati fiókok esetében jelenleg nem támogatottak.

1. Keresse meg a https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm , és jelentkezzen be Microsoft-Account.
2. A **alkalmazásaimat**, kattintson a **alkalmazás hozzáadása**.
![Onedrive vállalati alkalmazások][10]
3. Adjon meg egy **neve** az új alkalmazás regisztrálása a: Adja meg **futó Azure Stack App Service-** , és kattintson a **alkalmazás létrehozása**
4. A következő képernyőn az új alkalmazás tulajdonságai szerepelnek. Mentse a **Alkalmazásazonosító** egy ideiglenes helyre.
![Onedrive vállalati verzió alkalmazás tulajdonságai][11]
5. A **titkos Alkalmazáskulcsok**, kattintson a **új jelszó készítése**. Jegyezze fel a **új jelszó készült**. Ez az alkalmazás titkos kulcs és nem lekérdezhető kattintás után **OK**.
6. Alatt **platformok**, kattintson a **hozzáadása Platform**, majd válassza ki **webes**.
7. Adja meg a **átirányítási URI**. Az alapértelmezett Azure Stack üzembe helyezés az átirányítási URI formájában van https://portal.local.azurestack.external/TokenAuthorize. Ha egy másik tartományba le, helyettesítse be a tartománynév azurestack.local.
![Onedrive vállalati verzió alkalmazás – Webplatform hozzáadása][12]
8. Adja hozzá a **Microsoft Graph-engedélyek** - **delegált engedélyek**
    - **Files.ReadWrite.AppFolder**
    - **A felhasználó. Olvassa el**  
      ![Onedrive vállalati verzió alkalmazás – Graph-engedélyek][13]
9. Kattintson a **Save** (Mentés) gombra.
10.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure Stack felügyeleti portálon (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
11.  Keresse meg a **erőforrás-szolgáltatók** , és válassza ki a **App Service erőforrás szolgáltató rendszergazdai**.
12. Kattintson a **verziókövetési konfiguráció**.
13. Másolja és illessze be a **Alkalmazásazonosító** be a **ügyfél-azonosító** beviteli mezőt és **jelszó** be a **titkos Ügyfélkód** beviteli mezőt a onedrive vállalati verzióhoz.
14. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-dropbox"></a>DropBox konfigurálása

> [!NOTE]
> Befejezheti a feladatot egy DropBox-fiókkal kell rendelkeznie. Érdemes egy személyes fiók, hanem a szervezet olyan fiókot használjon.

1. Keresse meg a https://www.dropbox.com/developers/apps , és jelentkezzen be a DropBox-fiókja hitelesítő adataival.
2. Kattintson a **alkalmazás létrehozása**.

    ![Dropbox-alkalmazások][14]

3. Válassza ki **DropBox API**.
4. Állítsa a hozzáférés szintet **alkalmazás mappája**.
5. Adjon meg egy **neve** az alkalmazáshoz.
![Dropbox alkalmazás regisztrálása][15]
6. Kattintson a **alkalmazás létrehozása**. Megnyílik egy oldal, listázás, az alkalmazás beállításait például **Alkalmazáskulcs** és **titkos Alkalmazáskulcs**.
7. Győződjön meg arról, hogy a **alkalmazás mappanevet** értékre van állítva **App Service-ben az Azure Stacken**.
8. Állítsa be a **OAuth 2 átirányítási URI-t** majd **Hozzáadás**. Az alapértelmezett Azure Stack üzembe helyezés az átirányítási URI formájában van https://portal.local.azurestack.external/TokenAuthorize. Ha egy másik tartományba futtatja, helyettesítse be a azurestack.local a tartományt.
![Dropbox-alkalmazás konfigurációja][16]
9.  Egy új böngészőlapon vagy ablakban jelentkezzen be az Azure Stack felügyeleti portálon (https://adminportal.local.azurestack.external) a szolgáltatás-rendszergazdaként.
10.  Keresse meg a **erőforrás-szolgáltatók** , és válassza ki a **App Service erőforrás szolgáltató rendszergazdai**.
11. Kattintson a **verziókövetési konfiguráció**.
12. Másolja és illessze be a **Alkalmazáskulcsot** be a **ügyfél-azonosító** beviteli mezőt és **titkos Alkalmazáskulcs** be a **titkos Ügyfélkód** beviteli mező, a DropBox.
13. Kattintson a **Save** (Mentés) gombra.

## <a name="next-steps"></a>További lépések

A felhasználók mostantól használhatják a központi telepítés forrásának többek között a [folyamatos üzembe helyezés](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment), [Git helyi üzemelő példányának](https://docs.microsoft.com/azure/app-service/deploy-local-git), és [mappa szinkronizálása a felhő](https://docs.microsoft.com/azure/app-service/deploy-content-sync).

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
