---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 11/20/2020
ms.reviewer: bryanla
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 6d4bb587ad28fb26adf60689989d0e1858519cfe
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920681"
---
### <a name="create-a-device-root-ca-certificate"></a>Eszköz legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának létrehozása

Hozza létre az eszköz legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát és a kulcshoz szükséges fájlokat: 

1. Térjen vissza a bash-parancssorba a PuTTY-munkamenetben.
2. Navigáljon arra az adatkönyvtárra, ahová a tanúsítvány-létrehozási parancsfájlokat az előző szakaszban helyezte el.
3. Futtassa az alábbi parancsot:

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

4. Az eszköz legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát a következő fájl tárolja: `<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem` .

### <a name="create-the-iot-edge-device-ca-certificate"></a>A IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványának létrehozása

Az éles IoT Edge eszközöknek szüksége van egy eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványra, amelyre a config. YAML fájl hivatkozik. Az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványa felelős az eszközön futó modulok tanúsítványainak létrehozásához. Az átjáró-forgatókönyvek esetében is szükséges, mivel az eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát a IoT Edge eszköz használja az identitásnak az alárendelt eszközökhöz való ellenőrzéséhez.

Az IoT Edge-eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány fájljainak létrehozása:

1. Térjen vissza a bash-parancssorba a PuTTY-munkamenetben.
2. Futtassa a következő parancsfájlt, amely számos eszköz HITELESÍTÉSSZOLGÁLTATÓI tanúsítványát és kulcs fájlját hozza létre: 

   ```bash
   # Navigate to data directory
   cd <DATA-DIR>
   
   # Generate IoT Edge device CA certificate 
   ./certGen.sh create_edge_device_ca_certificate <DEVICE-CA-CERT-NAME>
   ```

3.  A következő tanúsítvány-és kulcspár-fájlokat a config. YAML fájl későbbi részében kell hivatkozni:

    `<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem`  
    `<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem`


