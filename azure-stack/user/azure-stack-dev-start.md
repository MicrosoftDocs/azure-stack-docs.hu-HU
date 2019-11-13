---
title: Fejlesztési környezet beállítása Azure Stackban | Microsoft Docs
description: Ismerkedjen meg Azure Stack-alkalmazások fejlesztésével.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 437963355c8077c31ca5f0f55acdd344bb0060e2
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955726"
---
# <a name="set-up-a-development-environment-in-azure-stack"></a>Fejlesztési környezet beállítása Azure Stack 

Windows 10, Linux vagy macOS rendszerű munkaállomás használatával Azure Stack alkalmazásokat fejleszthet. Ebben a cikkben a következőket tekintjük át: 

- A különböző kontextusokban, amelyekben az alkalmazás a Azure Stackban fut. 
- A következő lépésekkel állíthatja be a Windows 10, Linux vagy macOS rendszerű munkaállomásokat. 
- A Azure Stack erőforrásainak létrehozásához és az alkalmazásokhoz való telepítéséhez szükséges lépések. 

## <a name="azure-stack-context-and-your-code"></a>Azure Stack környezet és a kód 

Parancsfájlokat és alkalmazásokat is írhat a Azure Stack számos feladatának elvégzéséhez. Azonban hasznos lehet a hatókört a következő három módra korlátozni: 

1. Az első módban olyan alkalmazásokat hozhat létre, amelyek Azure Resource Manager sablonok használatával kiosztják az erőforrásokat a Azure Stackban. Írhat például egy olyan parancsfájlt, amely létrehoz egy Azure Resource Manager sablont, amely létrehozza a virtuális hálózatot és az alkalmazást futtató virtuális gépeket is. 

2. Második módban a végpontokkal közvetlenül fog működni a kódban létrehozott REST API és REST-ügyfél használatával. Ebben a módban olyan parancsfájlt írhat, amely létrehoz egy virtuális hálózatot és a virtuális gépeket, ha kéréseket küld az API-knak. 

3. A harmadik módban a programkód használatával létrehozhat egy Azure Stackban üzemeltetett alkalmazást. Miután létrehozta az infrastruktúrát Azure Stack az alkalmazás üzemeltetéséhez, üzembe helyezheti az alkalmazást az infrastruktúrában. Általában előkészíti a környezetet, majd üzembe helyezi az alkalmazást. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Szolgáltatásként és platformként szolgáló infrastruktúra 

A Cloud platform termékként Azure Stack a következőket támogatja: 

- Infrastruktúra-szolgáltatás (IaaS) 
- Platformszolgáltatás (PaaS) 

A IaaS és a Péter is tájékoztatja a fejlesztői gép beállításáról. 

A IaaS az adatközpont azon részeinek virtualizálása, amelyek a hálózati eszközökről, a hálózatról és a kiszolgálókról származnak. Amikor alkalmazást telepít egy webkiszolgálót üzemeltető virtuális gépre, egy IaaS-modellben dolgozik. Ebben a modellben a Azure Stack kezeli a virtuális felszerelést, és az alkalmazás egy virtuális kiszolgálón található. Azure Stack az erőforrás-szolgáltatók támogatják a hálózati összetevőket és a virtuális kiszolgálókat. 

Péter elvonta az infrastruktúra rétegét, hogy az alkalmazást egy olyan végponton telepítse, amely ezután futtatja az alkalmazást. A Pásti modellben tárolókat használhat az alkalmazás üzemeltetéséhez, majd a tároló alkalmazást a tárolót futtató szolgáltatáshoz. Az alkalmazást közvetlenül az alkalmazást futtató szolgáltatáshoz is leküldheti. A Azure App Service és a Kubernetes futtatásához Azure Stack is használhatja. 

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager 

A három korábban említett módot, valamint a Pétert vagy a IaaS-t a Azure Resource Manager Azure Stack verziója engedélyezte. Ez a felügyeleti keretrendszer lehetővé teszi Azure Stack-erőforrások üzembe helyezését, kezelését és figyelését. Lehetővé teszi, hogy egyetlen művelettel működjön együtt az erőforrásokkal csoportként. További információ a Azure Stack Resource Manager használatáról: az [API-verziók profiljainak kezelése a Azure stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-sdks"></a>SDK-k Azure Stack 

Azure Stack a Azure Resource Manager Azure Stack verzióját használja. Ha segítségre van szüksége a Azure Stack Resource Managerrel való együttműködéshez a választott kóddal, számos SDK-t adtunk meg, többek között: 

- [NETC#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)
- [Node.js](azure-stack-version-profile-nodejs.md)

## <a name="before-you-start"></a>Előkészületek 

A környezet beállításának megkezdése előtt a következőkre lesz szüksége: 

- Hozzáférés a Azure Stack felhasználói portálhoz. 
- A bérlő neve. 
- Annak megállapításához, hogy az Azure Active Directory (Azure AD) vagy a Active Directory összevonási szolgáltatások (AD FS) (AD FS) használja-e az Identity Manager. 

Ha bármilyen kérdése van a Azure Stackával kapcsolatban, lépjen kapcsolatba a Felhőbeli operátorral. 

## <a name="windows-10"></a>Windows 10 

Ha Windows 10 rendszerű gépet használ, a PowerShell 5,0 és a Visual Studio segítségével dolgozhat. Ha Azure Stack Development Kit (ASDK) használatával dolgozik, csatlakozhat a környezetéhez VPN-kapcsolattal. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Állítsa be a PowerShell használatával. Útmutatásért lásd: [Azure stack PowerShell telepítése](../operator/azure-stack-powershell-install.md). 

2. Azure Stack eszközök letöltése. Útmutatásért lásd: [Azure stack Tools letöltése a githubról](../operator/azure-stack-powershell-download.md). 

3. Ha ASDK használ, telepítsen és konfiguráljon egy [VPN-kapcsolat Azure stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Telepítse és konfigurálja az Azure CLI-t. Útmutatásért lásd: az [API-verziók profiljainak használata az Azure CLI-vel Azure Stackban](azure-stack-version-profiles-azurecli2.md). 

5. Azure Storage Explorer telepítése és konfigurálása. A Storage Explorer egy önálló alkalmazás, amely lehetővé teszi a Azure Stack Storage-adatmennyiség használatát. Útmutatásért lásd: [Storage Explorer összekötése egy Azure stack-előfizetéssel vagy egy Storage-fiókkal](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztői környezetet (IDE) a kód alapja és a preferencia alapján. 

     - Visual Studio Code (Python, go, NodeJS). Töltse le a [code.VisualStudio.com](https://code.visualstudio.com/Download)a Visual Studio Code-ot a gépre. 
     - Visual Studio (.NET/C#). Töltse le a Visual Studio Community Edition verziót a [VisualStudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)webhelyről. 
     - Eclipse (Java). Töltse le az Eclipse-et a [Eclipse.org](https://www.eclipse.org/downloads/)webhelyről. 

2. Telepítse az SDK-t a kódhoz: 

     - [NETC#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Ha Linux rendszerű gépet használ, használhatja az Azure CLI-t, a Visual Studio Code-ot vagy a saját előnyben részesített integrált fejlesztői környezetét. 

> [!Note]   
> Ha Linux rendszerű gépet használ a ASDK, a távoli gépnek ugyanabban a hálózaton kell lennie, mint a ASDK. Nem lehet csatlakozni virtuális magánhálózati kapcsolat használatával. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Telepítse és konfigurálja az Azure CLI-t. Útmutatásért lásd: az [API-verziók profiljainak használata az Azure CLI-vel Azure Stackban](azure-stack-version-profiles-azurecli2.md). 

2. Azure Storage Explorer telepítése és konfigurálása. A Storage Explorer egy önálló alkalmazás, amely lehetővé teszi a Azure Stack Storage-adatmennyiség használatát. Útmutatásért lásd: [Storage Explorer összekötése egy Azure stack-előfizetéssel vagy egy Storage-fiókkal](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztői környezetet (IDE) a kód alapja és a preferencia alapján. 

     - Visual Studio Code (Python, go, NodeJS). Töltse le a [code.VisualStudio.com](https://code.visualstudio.com/Download)a Visual Studio Code-ot a gépre. 
     - Visual Studio (.NET/C#). Töltse le a Visual Studio Community Edition verziót a [VisualStudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)webhelyről. 
     - Eclipse (Java). Töltse le az Eclipse-et a [Eclipse.org](https://www.eclipse.org/downloads/)webhelyről. 

2. Telepítse az SDK-t a kódhoz: 

     - [NETC#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

A macOS rendszerű gépek lehetővé teszik az Azure CLI és a Visual Studio Code vagy a saját előnyben részesített integrált fejlesztési környezet használatát. 

> [!Note]   
> Ha macOS rendszerű gépet használ a ASDK, a távoli gépnek ugyanabban a hálózaton kell lennie, mint a ASDK. Nem lehet csatlakozni virtuális magánhálózati kapcsolat használatával. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Telepítse és konfigurálja az Azure CLI-t. Útmutatásért lásd: az [API-verziók profiljainak használata az Azure CLI-vel Azure Stackban](azure-stack-version-profiles-azurecli2.md). 

2. Azure Storage Explorer telepítése és konfigurálása. A Storage Explorer egy önálló alkalmazás, amely lehetővé teszi a Azure Stack Storage-adatmennyiség használatát. Útmutatásért lásd: [Storage Explorer összekötése egy Azure stack-előfizetéssel vagy egy Storage-fiókkal](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztői környezetet (IDE) a kód alapja és a preferencia alapján. 

     - Visual Studio Code (Python, go, NodeJS). Töltse le a [code.VisualStudio.com](https://code.visualstudio.com/Download)a Visual Studio Code-ot a gépre. 
     - Visual Studio (.NET/C#). Töltse le a Visual Studio Community Edition verziót a [VisualStudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)webhelyről. 
     - Eclipse (Java). Töltse le az Eclipse-et a [Eclipse.org](https://www.eclipse.org/downloads/)webhelyről. 

2. Telepítse az SDK-t a kódhoz: 

     - [NETC#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Következő lépések 

Az alkalmazások Azure Stackban lévő erőforrásokra való telepítéséhez lásd: [a Azure stack általános telepítései](azure-stack-dev-start-deploy-app.md).
