---
title: A IoT Hub telepítésének előfeltételei Azure Stack hubhoz
description: Ismerje meg a szükséges előfeltételeket, mielőtt telepítené IoT Hub erőforrás-szolgáltatót Azure Stack hubhoz.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 953f8df1ce76b5765ba0c200ea9f2a78c7ee72b6
ms.sourcegitcommit: e432e7f0a790bd6419987cbb5c5f3811e2e7a4a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102515658"
---
# <a name="prerequisites-for-installing-iot-hub-on-azure-stack-hub"></a>A IoT Hub telepítésének előfeltételei Azure Stack hubhoz

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

A következő előfeltételeket kell végrehajtania ahhoz, hogy telepíteni tudja a IoT Hubt Azure Stack hub-ra. Az összes lépés végrehajtásához **több nap vagy hét szükséges** .

> [!IMPORTANT]
> Ezek az előfeltételek feltételezik, hogy már üzembe helyezte legalább egy 4 csomópontos Azure Stack hub integrált rendszerét, a 1.2005.6.53 vagy a magasabb **értéket** . A IoT Hub erőforrás-szolgáltató nem támogatott az Azure Stack hub Development Kit (ASDK) szolgáltatásban.

## <a name="common-prerequisites"></a>Gyakori előfeltételek

[!INCLUDE [Common RP prerequisites](../includes/resource-provider-prerequisites.md)]

## <a name="dependency-prerequisites"></a>Függőség előfeltételei

1. Töltse le és [telepítse a Event Hubst](event-hubs-rp-install.md) a piactéren. A Event Hubs üzembe helyezése előtt a IoT Hub üzembe helyezése előtt kell történnie.
2. A IoT Hub gyorsabb letöltéséhez és telepítéséhez töltse le a piactéren a következő függő elemeket a IoT Hub csomag letöltése előtt. Ellenkező esetben IoT Hub üzemelő példány megkísérli letölteni a függő csomagokat:
    * Egyéni szkriptbővítmény
    * PowerShell kívánt állapotának konfigurálása
    * Ingyenes licenc: SQL Server 2016 SP2 Express Windows Server 2016 rendszeren
    * SQL IaaS bővítmény
    * Azure Stack Add-On RP Windows Server
3. Várjon legalább 10 percet a Event Hubs sikeres telepítése után, mielőtt folytatná a IoT Hub üzembe helyezését.

## <a name="certificate-requirements"></a>Tanúsítványkövetelmények

1. Nyilvános kulcsokra épülő infrastruktúra (PKI) TLS/SSL-tanúsítvány beszerzése IoT Hubhoz. A tanúsítvány **tárgya** mezőnek meg kell felelnie a következő elnevezési mintának: `CN=*.mgmtiothub.<region>.<fqdn>` . A részletes követelmények teljes listájáért tekintse meg a [PKI-tanúsítványokra vonatkozó követelményeket](azure-stack-pki-certs.md) .

   ![IOT hub-tanúsítvány – példa](media\iot-hub-rp-prerequisites\certificate.png)

2. Ügyeljen rá, hogy ellenőrizze [a tanúsítvány érvényességét](azure-stack-validate-pki-certs.md). A cikk bemutatja, hogyan készítheti elő és ellenőrizheti a IoT Hub erőforrás-szolgáltatóhoz használt tanúsítványokat. 

## <a name="dns-configuration-requirements"></a>DNS-konfigurációs követelmények
 
Ahhoz, hogy a IoT Hub megfelelően működjön a hálózaton a Azure Stack központban, a hálózati rendszergazdának konfigurálnia kell a DNS-t. Keresse meg a DNS-kezelő eszköz DNS feltételes továbbítás beállítását, és adjon hozzá egy feltételes továbbítási szabályt, amely engedélyezi a forgalmat a következőhöz: `<region>.cloudapp.<externaldomainname>` . Például: `ussouth.cloudapp.contoso.com`.

## <a name="next-steps"></a>Következő lépések

Ezután telepítse a IoT Hub erőforrás-szolgáltatót a [csatlakoztatott Azure stack](iot-hub-rp-install.md).
