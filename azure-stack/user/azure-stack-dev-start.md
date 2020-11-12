---
title: Fejlesztési környezet beállítása Azure Stack központban
description: Ismerkedjen meg Azure Stack hub-alkalmazások fejlesztésével.
author: mattbriggs
ms.topic: overview
ms.date: 10/16/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/16/2020
ms.openlocfilehash: aaf6e48a728659ee6e756cee674edb423362db67
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546123"
---
# <a name="set-up-a-development-environment-in-azure-stack-hub"></a>Fejlesztési környezet beállítása Azure Stack központban 

Azure Stack hub alkalmazásait Windows 10, Linux vagy macOS rendszerű munkaállomás használatával fejlesztheti. Ebben a cikkben a következőket tekintjük át: 

- A különböző kontextusokban, amelyekben az alkalmazás a Azure Stack központban fut. 
- A következő lépésekkel állíthatja be a Windows 10, Linux vagy macOS rendszerű munkaállomásokat. 
- Az erőforrások Azure Stack hub-ban való létrehozásának és alkalmazásként történő üzembe helyezésének lépései. 

## <a name="azure-stack-hub-context-and-your-code"></a>Azure Stack hub-környezet és a kód 

Parancsfájlokat és alkalmazásokat is írhat a Azure Stack hub számos feladatának elvégzéséhez. Azonban hasznos lehet a hatókört a következő három módra korlátozni: 

1. Az első módban olyan alkalmazásokat hozhat létre, amelyek a Azure Stack hub erőforrásait Azure Resource Manager sablonok használatával tudják kiépíteni. Írhat például egy olyan parancsfájlt, amely létrehoz egy Azure Resource Manager sablont, amely létrehozza a virtuális hálózatot és az alkalmazást futtató virtuális gépeket is. 

2. Második módban a végpontokkal közvetlenül fog működni a kódban létrehozott REST API és REST-ügyfél használatával. Ebben a módban olyan parancsfájlt írhat, amely létrehoz egy virtuális hálózatot és a virtuális gépeket, ha kéréseket küld az API-knak. 

3. A harmadik módban a programkód használatával hozhat létre Azure Stack hub-ban üzemeltetett alkalmazást. Miután létrehozta az infrastruktúrát Azure Stack hub-ban az alkalmazás üzemeltetéséhez, üzembe helyezheti az alkalmazást az infrastruktúrában. Általában előkészíti a környezetet, majd üzembe helyezi az alkalmazást. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Szolgáltatásként és platformként szolgáló infrastruktúra 

A Cloud platform termékként a Azure Stack hub a következőket támogatja: 

- Szolgáltatott infrastruktúra (IaaS) 
- Szolgáltatásként nyújtott platform (PaaS) 

A IaaS és a Péter is tájékoztatja a fejlesztői gép beállításáról. 

A IaaS az adatközpont azon részeinek virtualizálása, amelyek a hálózati eszközökről, a hálózatról és a kiszolgálókról származnak. Amikor alkalmazást telepít egy webkiszolgálót üzemeltető virtuális gépre, egy IaaS-modellben dolgozik. Ebben a modellben a Azure Stack hub kezeli a virtuális felszerelést, és az alkalmazás egy virtuális kiszolgálón található. Azure Stack hub erőforrás-szolgáltatók támogatják a hálózati összetevőket és a virtuális kiszolgálókat. 

Péter elvonta az infrastruktúra rétegét, hogy az alkalmazást egy olyan végponton telepítse, amely ezután futtatja az alkalmazást. A Pásti modellben tárolókat használhat az alkalmazás üzemeltetéséhez, majd a tároló alkalmazást a tárolót futtató szolgáltatáshoz. Az alkalmazást közvetlenül az alkalmazást futtató szolgáltatáshoz is leküldheti. Azure App Service-és Kubernetes futtatásához Azure Stack hub-t használhat. 

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack hub Resource Manager 

A három korábban említett módot, valamint a Pétert vagy a IaaS-t a Azure Resource Manager Azure Stack hub-verziója engedélyezte. Ez a felügyeleti keretrendszer lehetővé teszi Azure Stack hub-erőforrások üzembe helyezését, kezelését és figyelését. Lehetővé teszi, hogy egyetlen művelettel működjön együtt az erőforrásokkal csoportként. További információ az Azure Stack hub Resource Manager használatáról: az [API-verziók profiljainak kezelése a Azure stack központban](azure-stack-version-profiles.md). 

### <a name="azure-stack-hub-sdks"></a>Azure Stack hub SDK-k 

Azure Stack hub a Azure Resource Manager Azure Stack hub verzióját használja. Ha segítségre van szüksége a Azure Stack hub Resource Managerrel való együttműködéshez, számos SDK-t adtunk meg, többek között a következőket: 

- [.NET/C #](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Ugrás](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)
- [Node.js](azure-stack-version-profile-nodejs.md)

## <a name="before-you-start"></a>Előkészületek 

A környezet beállításának megkezdése előtt a következőkre lesz szüksége: 

- Hozzáférés az Azure Stack hub felhasználói portálhoz. 
- A bérlő neve. 
- Annak megállapításához, hogy az Azure Active Directory (Azure AD) vagy a Active Directory összevonási szolgáltatások (AD FS) (AD FS) használja-e az Identity Manager. 

Ha bármilyen kérdése van az Azure Stack hub szolgáltatással kapcsolatban, forduljon a felhő üzemeltetőjéhez. 

## <a name="windows-10"></a>Windows 10 

Ha Windows 10 rendszerű gépet használ, a PowerShell 5,0 és a Visual Studio segítségével dolgozhat. Ha Azure Stack Development Kit (ASDK) használatával dolgozik, csatlakozhat a környezetéhez VPN-kapcsolattal. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Állítsa be a PowerShell használatával. Útmutatásért lásd: [Azure stack hub PowerShell telepítése](../operator/powershell-install-az-module.md). 

2. Azure Stack hub-eszközök letöltése. Útmutatásért lásd: [Azure stack hub-eszközök letöltése a githubról](../operator/azure-stack-powershell-download.md). 

3. Ha ASDK használ, telepítse és konfigurálja a [VPN-kapcsolatokat Azure stack hubhoz](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn). 

4. Telepítse és konfigurálja az Azure CLI-t. Útmutatásért lásd: [az API-verziók profiljainak használata az Azure CLI-vel Azure stack hub-ban](azure-stack-version-profiles-azurecli2.md). 

5. Azure Storage Explorer telepítése és konfigurálása. A Storage Explorer egy önálló alkalmazás, amely lehetővé teszi Azure Stack hub Storage-beli adatkezelést. Útmutatásért lásd: a [Storage Explorer Azure stack hub-előfizetéshez vagy egy Storage-fiókhoz való kapcsolódása](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztői környezetet (IDE) a kód alapja és a preferencia alapján. 

     - Visual Studio Code (Python, go, NodeJS). Töltse le a [code.VisualStudio.com](https://code.visualstudio.com/Download)a Visual Studio Code-ot a gépre. 
     - Visual Studio (.NET/C#). Töltse le a Visual Studio Community Edition verziót a [VisualStudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)webhelyről. 
     - Eclipse (Java). Töltse le az Eclipse-et a [Eclipse.org](https://www.eclipse.org/downloads/)webhelyről. 

2. Telepítse az SDK-t a kódhoz: 

     - [.NET/C #](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Ugrás](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Ha Linux rendszerű gépet használ, használhatja az Azure CLI-t, a Visual Studio Code-ot vagy a saját előnyben részesített integrált fejlesztői környezetét. 

> [!NOTE]   
> Ha Linux rendszerű gépet használ a ASDK, a távoli gépnek ugyanabban a hálózaton kell lennie, mint a ASDK. Nem lehet csatlakozni virtuális magánhálózati kapcsolat használatával. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Telepítse és konfigurálja az Azure CLI-t. Útmutatásért lásd: [az API-verziók profiljainak használata az Azure CLI-vel Azure stack hub-ban](azure-stack-version-profiles-azurecli2.md). 

2. Azure Storage Explorer telepítése és konfigurálása. A Storage Explorer egy önálló alkalmazás, amely lehetővé teszi Azure Stack hub Storage-beli adatkezelést. Útmutatásért lásd: a [Storage Explorer Azure stack hub-előfizetéshez vagy egy Storage-fiókhoz való kapcsolódása](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztői környezetet (IDE) a kód alapja és a preferencia alapján. 

     - Visual Studio Code (Python, go, NodeJS). Töltse le a [code.VisualStudio.com](https://code.visualstudio.com/Download)a Visual Studio Code-ot a gépre. 
     - Visual Studio (.NET/C#). Töltse le a Visual Studio Community Edition verziót a [VisualStudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)webhelyről. 
     - Eclipse (Java). Töltse le az Eclipse-et a [Eclipse.org](https://www.eclipse.org/downloads/)webhelyről. 

2. Telepítse az SDK-t a kódhoz: 

     - [.NET/C #](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Ugrás](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

A macOS rendszerű gépek lehetővé teszik az Azure CLI és a Visual Studio Code vagy a saját előnyben részesített integrált fejlesztési környezet használatát. 

> [!NOTE]   
> Ha macOS rendszerű gépet használ a ASDK, a távoli gépnek ugyanabban a hálózaton kell lennie, mint a ASDK. Nem lehet csatlakozni virtuális magánhálózati kapcsolat használatával. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Telepítse és konfigurálja az Azure CLI-t. Útmutatásért lásd: [az API-verziók profiljainak használata az Azure CLI-vel Azure stack hub-ban](azure-stack-version-profiles-azurecli2.md). 

2. Azure Storage Explorer telepítése és konfigurálása. A Storage Explorer egy önálló alkalmazás, amely lehetővé teszi Azure Stack hub Storage-beli adatkezelést. Útmutatásért lásd: a [Storage Explorer Azure stack hub-előfizetéshez vagy egy Storage-fiókhoz való kapcsolódása](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztői környezetet (IDE) a kód alapja és a preferencia alapján. 

     - Visual Studio Code (Python, go, NodeJS). Töltse le a [code.VisualStudio.com](https://code.visualstudio.com/Download)a Visual Studio Code-ot a gépre. 
     - Visual Studio (.NET/C#). Töltse le a Visual Studio Community Edition verziót a [VisualStudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)webhelyről. 
     - Eclipse (Java). Töltse le az Eclipse-et a [Eclipse.org](https://www.eclipse.org/downloads/)webhelyről. 

2. Telepítse az SDK-t a kódhoz: 

     - [.NET/C #](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Ugrás](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Következő lépések 

Ha Azure Stack hub erőforrásait szeretné központilag telepíteni, tekintse meg [az Azure stack hub általános telepítéseit](azure-stack-dev-start-deploy-app.md)ismertető témakört.
