---
title: Állítsa be a fejlesztési környezetet az Azure Stackben |} A Microsoft Docs
description: Ismerkedés az Azure Stack-alkalmazások fejlesztéséhez használható.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 9b3d58e67d7c6ca7016b3ecb51c09171aae7c06a
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490020"
---
# <a name="set-up-a-development-environment-on-azure-stack"></a>Állítsa be a fejlesztési környezetet az Azure Stackben 

Alkalmazást az Azure Stack használatával a Windows 10, Linux vagy macOS munkaállomás fejleszthet. Ebben a cikkben áttekintjük: 

- A különböző környezetek, amelyben az alkalmazás fut, az Azure Stacken. 
- A lépéseket követheti ahhoz, hogy állítsa be a Windows 10, Linux vagy macOS munkaállomás. 
- Lépések az Azure Stack-erőforrás létrehozásához és üzembe helyezett alkalmazást. 

## <a name="azure-stack-context-and-your-code"></a>Azure Stack-környezet és a kódot 

Parancsfájlok és mindent az Azure Stackben végrehajtására képes alkalmazásokat írhat. Azonban célszerű korlátozni, mit kívánt három különböző módokban való elvégzését. 

1. Az első módban, amely kiépíti az erőforrásokat az Azure Stack az Azure Resource Manager-sablonok használatával alkalmazásokat hozhat létre. Ha például egy parancsfájlt, amelynek fejlesztés az Azure Resource Manager-sablon, amely létrehoz egy virtuális hálózatot és az alkalmazást futtató virtuális gépek is létrehozhat. 

2. A második módban az ügyfelek közvetlenül a REST API és a egy REST-ügyfél létrehozása a kódban végpontok. Ebben a módban egy parancsfájlt, amely létrehoz egy virtuális hálózat és a virtuális gépek az API-k való kérések küldésével hozna létre. 

3. A harmadik módban a kód használatával üzemeltetett alkalmazás létrehozása az Azure Stackben. Miután az infrastruktúrát az Azure Stack az alkalmazás futtatására, az alkalmazás üzembe helyezése az infrastruktúra. Általában fogja a környezet előkészítése és az alkalmazás üzembe helyezése abban a környezetben. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Infrastruktúra--szolgáltatásként, és a Platform-– szolgáltatásként 

Felhő, mint az Azure Stack is támogatja: 

- Infrastruktúra--szolgáltatásként (IaaS) 
- Platform-as-a-Service (PaaS) 

IaaS- és PaaS tájékoztatja, hogyan szeretné beállítani a fejlesztői gépére. 

IaaS a a virtualizálás az Adatközpont hálózati fogaskerék, a hálózat és a kiszolgálók részei. Amikor telepít egy alkalmazást, amelyen a webkiszolgáló virtuális géphez, az egy IaaS paradigmát dolgozik. Az összeállítást, az Azure Stack kezeli a virtuális fogaskerék és az alkalmazás virtuális kiszolgálón. Az Azure Stack erőforrás-szolgáltató támogatja a hálózati összetevők és a virtuális kiszolgálók. 

PaaS kivonatolja a infrastruktúra réteget, úgy, hogy az alkalmazás egy végpontot, amely ezután futtatja az alkalmazás üzembe helyezése. A PaaS paradigm, előfordulhat, hogy az alkalmazás üzemeltetése a tárolók használatával, és tárolóalapú alkalmazás a szolgáltatást, amely a tároló üzembe helyezése, vagy előfordulhat, hogy küldje le az alkalmazás közvetlenül a szolgáltatást, amely az alkalmazás. Az Azure Stack az Azure App Service-ben és a Kubernetes futtatásához használható. 

### <a name="azure-stack-resource-manager"></a>Az Azure Stack Resource Manager 

Ezek három mód, valamint a PaaS vagy IaaS engedélyezve vannak az Azure Stack az Azure Resource Manager-verzió. A felügyeleti keretrendszer üzembe helyezése, kezelése és monitorozása az Azure Stack-erőforrások teszi lehetővé. A kezelő lehetővé teszi, hogy ezeket az elemeket az egyetlen művelettel csoportként kezelheti. További információ a az Azure Stack Resource Manager használata esetén lásd: [kezelése API-verzióprofilok az Azure Stackben](azure-stack-version-profiles.md). 

### <a name="azure-stack-development-kits"></a>Az Azure Stack development Kit 

Az Azure Stack az Azure Resource Manager az Azure Stack telepített verzióját használja.  Annak érdekében, hogy használatához nyújt segítséget az az Azure Stack Resource Manager használatával a kódot, szoftverfejlesztői készletekkel számos adtunk meg. Ezek a következők: 

- [.NET /C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)

## <a name="before-you-start"></a>Előkészületek 

A következőkre lesz szükség: 

- Hozzáférés az Azure Stack felhasználói portálon. 
- A bérlő nevével. 
- Tudni, hogy ha használ az Azure Active directory (Azure AD) vagy az Active Directory összevonási szolgáltatásokban (AD FS), az identity Manager. 

Ha bármilyen kérdése van az Azure Stack kapcsolatban, lépjen kapcsolatba a felhő üzemeltetője. 

## <a name="windows-10"></a>Windows 10 

Windows 10 rendszerű gépet lehetővé teszi, hogy a PowerShell 5.0-, a Visual Studio dolgozhat, és ha dolgozik egy ASDK a környezet VPN-kapcsolaton keresztül csatlakozni. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Készüljön fel a PowerShell használatával. További információk, kövesse a [telepítse az Azure Stack Powershell](../operator/azure-stack-powershell-install.md). 

2. Töltse le az Azure Stack-eszközöket. Útmutatás, kövesse a [töltse le az Azure Stack-eszközök a Githubról](../operator/azure-stack-powershell-download.md). 

3. Ha egy ASDK használ, telepítse és konfigurálja a [VPN-kapcsolat az Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Telepítse és konfigurálja az Azure parancssori felület. Útmutatás, kövesse a [az Azure CLI-vel az Azure Stack használata API-verzióprofilok](azure-stack-version-profiles-azurecli2.md). 

5. Telepítse és konfigurálja az Azure storage Explorerben. Az Azure storage explorer egy önálló alkalmazás, amely lehetővé teszi, hogy az Azure Stack tárolási adatok.  Útmutatás, kövesse a [storage explorer csatlakoztatása Azure Stack-előfizetéshez vagy a storage-fiók](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztőkörnyezet (IDE), a kódbázis és szabályozó függően. 

     - Visual Studio Code (Python, Go, NodeJS). A Visual Studio Code letöltése a gépre az [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - A Visual Studio (.Net /C#). Töltse le a Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Töltse le az Eclipse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Telepítse a szoftverfejlesztői készlet (SDK) a kódhoz. 

     - [.NET /C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Lehetővé teszi a Linux rendszerű gép Azure CLI és Visual Studio Code-ot, vagy a saját által választott integrált fejlesztői környezetben dolgozhat. 

> [!Note]   
> Az a ASDK használata Linux rendszerű gép, a távoli gép kell lennie a ASDK ugyanazon a hálózaton. Nem lehet csatlakozni egy virtuális magánhálózati kapcsolaton keresztül. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Telepítse és konfigurálja az Azure parancssori felület. Útmutatás, kövesse a [az Azure CLI-vel az Azure Stack használata API-verzióprofilok](azure-stack-version-profiles-azurecli2.md). 

2. Telepítse és konfigurálja az Azure storage Explorerben. Az Azure storage explorer egy önálló alkalmazás, amely lehetővé teszi, hogy az Azure Stack tárolási adatok.  Útmutatás, kövesse a [storage explorer csatlakoztatása Azure Stack-előfizetéshez vagy a storage-fiók](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztőkörnyezet (IDE), a kódbázis és szabályozó függően. 

     - Visual Studio Code (Python, Go, NodeJS). A Visual Studio Code letöltése a gépre az [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - A Visual Studio (.Net /C#). Töltse le a Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Töltse le az Eclipse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Telepítse a szoftverfejlesztői készlet (SDK) a kódhoz. 

     - [.NET /C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOs 

A MacOS rendszerű gépén lehetővé teszi az Azure CLI és Visual Studio Code-ot, vagy a saját által választott integrált fejlesztői környezetben dolgozhat. 

> [!Note]   
> A ASDK a használja a MacOS rendszerű gépén, ha a távoli gépen kell lennie a ASDK ugyanazon a hálózaton. Nem lehet csatlakozni egy virtuális magánhálózati kapcsolaton keresztül. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Telepítse és konfigurálja az Azure parancssori felület. Útmutatás, kövesse a [az Azure CLI-vel az Azure Stack használata API-verzióprofilok](azure-stack-version-profiles-azurecli2.md). 

2. Telepítse és konfigurálja az Azure storage Explorerben. Az Azure storage explorer egy önálló alkalmazás, amely lehetővé teszi, hogy az Azure Stack tárolási adatok. Útmutatás, kövesse a [storage explorer csatlakoztatása Azure Stack-előfizetéshez vagy a storage-fiók](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztőkörnyezet (IDE), a kódbázis és szabályozó függően. 

     - Visual Studio Code (Python, Go, NodeJS). A Visual Studio Code letöltése a gépre az [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - A Visual Studio (.Net /C#). Töltse le a Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Töltse le az Eclipse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Telepítse a szoftverfejlesztői készlet (SDK) a kódhoz. 

     - [.NET /C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>További lépések 

Erőforrások az Azure Stack-alkalmazás üzembe helyezése. A lépéseket annak [közös üzemelő példányok az Azure Stack](azure-stack-dev-start-deploy-app.md).