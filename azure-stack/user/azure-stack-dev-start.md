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
ms.openlocfilehash: 06c462ab46ce6bbae8d5c3bd6fcb757e14417edf
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617635"
---
# <a name="set-up-a-development-environment-in-azure-stack"></a>Az Azure Stack fejlesztői környezet beállítása 

Alkalmazást az Azure Stack egy Windows 10, Linux vagy macOS workstation használatával is fejleszthet. Ebben a cikkben áttekintjük: 

- A különböző környezetek, amelyben az alkalmazás fut, az Azure Stackben. 
- A Windows 10, Linux vagy macOS munkaállomás beállítása az első lépéseit. 
- A lépések az Azure stack-erőforrások létrehozását és üzembe helyezheti őket egy alkalmazáshoz. 

## <a name="azure-stack-context-and-your-code"></a>Azure Stack-környezet és a kódot 

Parancsfájlok és alkalmazásokat az Azure Stackben számos feladatok elvégzéséhez írhat. Azonban hasznos lehet korlátozni a hatókörrel, hogy a következő három mód: 

1. Az első mód alkalmazásokat, amelyek az Azure Stack-erőforrások kiépítése az Azure Resource Manager-sablonok használatával is létrehozhat. Például előfordulhat, hogy ír egy parancsfájlt, amely létrehoz egy Azure Resource Manager-sablon, amely létrehoz egy virtuális hálózatot és az alkalmazást futtató virtuális gépek. 

2. A második módban az ügyfelek közvetlenül végpontok a REST API és a egy REST-ügyféllel létrehozott a kódban. Ebben a módban kellene írni egy parancsfájlt, amely létrehoz egy virtuális hálózat és a virtuális gépek az API-k való kérések küldésével. 

3. A harmadik módban a kód használatával üzemeltetett alkalmazás létrehozása az Azure Stackben. Miután létrehozta az infrastruktúrát az Azure Stack az alkalmazás futtatásához, az infrastruktúra helyezi üzembe az alkalmazást. Általában fog a környezet előkészítése és az alkalmazás történő üzembe helyezéséhez. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Infrastruktúra-szolgáltatás és a szolgáltatásként nyújtott platformon 

A felhőplatform-megoldás, mint az Azure Stack is támogatja: 

- Szolgáltatott infrastruktúra (IaaS) 
- Platformszolgáltatás (PaaS) 

IaaS- és PaaS tájékoztatja a fejlesztői gépén beállítása. 

IaaS a a virtualizálás az Adatközpont részei, amelyek hálózati fogaskerék, a hálózat és a kiszolgálók származnak. Amikor telepít egy alkalmazást, amelyen a webkiszolgáló virtuális géphez, az IaaS-modell dolgozik. Ebben a modellben az Azure Stack kezeli a virtuális fogaskerék, és az alkalmazás virtuális kiszolgálón. Az Azure Stack erőforrás-szolgáltató támogatja a hálózati összetevők és a virtuális kiszolgálók. 

PaaS kivonatolja a infrastruktúra réteg, úgy, hogy az alkalmazás egy végpontot, amely ezután futtatja az alkalmazást üzembe helyezése. A PaaS-modell használatával tárolók üzemeltetéséhez az alkalmazást, és majd a tárolóalapú alkalmazás szolgáltatást, amely a tároló üzembe helyezése. Vagy előfordulhat, hogy küldje le az alkalmazás közvetlenül a egy szolgáltatás, amely futtatja az alkalmazást. Azure Stack segítségével az Azure App Service és a Kubernetes futtathat. 

### <a name="azure-stack-resource-manager"></a>Az Azure Stack Resource Manager 

A három korábban említett módok, valamint a PaaS vagy IaaS, engedélyezve vannak az Azure Stack az Azure Resource Manager verziója. Ez a felügyeleti keretrendszer üzembe helyezése, kezelése és monitorozása az Azure Stack-erőforrások teszi lehetővé. Lehetővé teszi egyetlen művelettel csoportként az erőforrásokkal való munka. Az Azure Stack Resource Managerrel működő kapcsolatos további információkért lásd: [kezelése API-verzióprofilok az Azure Stackben](azure-stack-version-profiles.md). 

### <a name="azure-stack-sdks"></a>Az Azure Stack SDK-k 

Az Azure Stack az Azure Resource Manager Azure Stack telepített verzióját használja. Segítséget a tetszőleges kód segítségével az Azure Stack Resource Managerrel működnek, SDK-k, beleértve számos mellékelt: 

- [.NET/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)

## <a name="before-you-start"></a>Előkészületek 

A környezet beállítása előtt szüksége: 

- Hozzáférés az Azure Stack felhasználói portálon. 
- A bérlő nevével. 
- Meghatározni, hogy használ az Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS), mint az identity manager. 

Ha az Azure stackről bármilyen kérdése van, lépjen kapcsolatba a felhő üzemeltetője. 

## <a name="windows-10"></a>Windows 10 

Ha Windows 10 rendszerű gépet használ, használhatja a PowerShell 5.0-s és a Visual Studióban. És dolgozik az Azure Stack Development Kit (ASDK), ha a környezet VPN-kapcsolaton keresztül kapcsolódhat. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Készüljön fel a PowerShell használatával. Útmutatásért lásd: [telepítse az Azure Stack Powershell](../operator/azure-stack-powershell-install.md). 

2. Töltse le az Azure Stack-eszközöket. Útmutatásért lásd: [töltse le az Azure Stack-eszközök a Githubról](../operator/azure-stack-powershell-download.md). 

3. Ha egy ASDK használ, telepítse és konfigurálja a [VPN-kapcsolat Azure stackhez](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Telepítse és konfigurálja az Azure parancssori felület. Útmutatásért lásd: [az Azure Stack az Azure CLI-vel használható API-verzióprofilok](azure-stack-version-profiles-azurecli2.md). 

5. Telepítse és konfigurálja az Azure Storage Explorerben. Storage Explorer egy önálló alkalmazás, amely lehetővé teszi, hogy az Azure Stack tárolási adatok. Útmutatásért lásd: [Storage Explorer csatlakoztatása Azure Stack-előfizetéshez vagy a storage-fiók](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztőkörnyezet (IDE), a kódbázis és szabályozó függően. 

     - Visual Studio Code (Python, Go, NodeJS). A Visual Studio Code letöltése a gépre az [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Töltse le a Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Töltse le az Eclipse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Telepítse az SDK a kódhoz: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Ha Linux rendszerű gépek használata esetén használhatja az Azure CLI-vel, a Visual Studio Code vagy a saját által választott integrált fejlesztői környezetben. 

> [!Note]   
> Az a ASDK használata Linux rendszerű gép, a távoli gépen kell lennie a ASDK ugyanazon a hálózaton található. Nem tud csatlakozni a virtuális magánhálózati kapcsolaton keresztül. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Telepítse és konfigurálja az Azure parancssori felület. Útmutatásért lásd: [az Azure Stack az Azure CLI-vel használható API-verzióprofilok](azure-stack-version-profiles-azurecli2.md). 

2. Telepítse és konfigurálja az Azure Storage Explorerben. Storage Explorer egy önálló alkalmazás, amely lehetővé teszi, hogy az Azure Stack tárolási adatok. Útmutatásért lásd: [Storage Explorer csatlakoztatása Azure Stack-előfizetéshez vagy a storage-fiók](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztőkörnyezet (IDE), a kódbázis és szabályozó függően. 

     - Visual Studio Code (Python, Go, NodeJS). A Visual Studio Code letöltése a gépre az [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Töltse le a Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Töltse le az Eclipse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Telepítse az SDK a kódhoz: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

A MacOS rendszerű gépén lehetővé teszi, hogy az Azure CLI és Visual Studio Code-ot, vagy a saját elsődleges az integrált fejlesztőkörnyezetből. 

> [!Note]   
> A ASDK a használja a MacOS rendszerű gépén, ha a távoli gépen kell lennie a ASDK ugyanazon a hálózaton található. Nem tud csatlakozni a virtuális magánhálózati kapcsolaton keresztül. 

### <a name="set-up-your-tools"></a>Az eszközök beállítása 

1. Telepítse és konfigurálja az Azure parancssori felület. Útmutatásért lásd: [az Azure Stack az Azure CLI-vel használható API-verzióprofilok](azure-stack-version-profiles-azurecli2.md). 

2. Telepítse és konfigurálja az Azure Storage Explorerben. Storage Explorer egy önálló alkalmazás, amely lehetővé teszi, hogy az Azure Stack tárolási adatok. Útmutatásért lásd: [Storage Explorer csatlakoztatása Azure Stack-előfizetéshez vagy a storage-fiók](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Az integrált fejlesztői környezet telepítése 

1. Telepítse az integrált fejlesztőkörnyezet (IDE), a kódbázis és szabályozó függően. 

     - Visual Studio Code (Python, Go, NodeJS). A Visual Studio Code letöltése a gépre az [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Töltse le a Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Töltse le az Eclipse [eclipse.org](https://www.eclipse.org/downloads/). 

2. Telepítse az SDK a kódhoz: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>További lépések 

Erőforrások az Azure Stack-alkalmazás üzembe helyezése, lásd: [közös üzemelő példányok az Azure Stack](azure-stack-dev-start-deploy-app.md).
