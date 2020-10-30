---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 900d7ac882a37e229bec6dc916653cf55992cccb
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "93049757"
---
Ezután hozza létre vagy újítsa meg a TLS-tanúsítványt az erőforrás-szolgáltatói végpontok hozzáadásához:

1. Végezze el az erőforrás-szolgáltató [tanúsítvány-megújításához szükséges tanúsítvány-aláírási kérések (munkatársak) előállításának](../operator/azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal) lépéseit. Itt az Azure Stack hub Readiness-ellenőrző eszközt használja a CSR létrehozásához. Ügyeljen arra, hogy a megfelelő parancsmagot futtassa az erőforrás-szolgáltatóhoz, a "tanúsítványkérelmek előállítása más Azure Stack hub-szolgáltatásokhoz" című lépésben. Például a `New-AzsHubEventHubsCertificateSigningRequest` Event Hubshoz van használatban. Ha elkészült, elküldheti a generált jelentést. A hitelesítésszolgáltató (CA) számára az új tanúsítványra vonatkozó kötelező fájl.

2. Miután megkapta a tanúsítványfájl a HITELESÍTÉSSZOLGÁLTATÓTÓL, hajtsa végre a [tanúsítványok előkészítése üzembe helyezéshez vagy rotációhoz](../operator/azure-stack-prepare-pki-certs.md)című témakör lépéseit. A készültség-ellenőrző eszközt újra használhatja a HITELESÍTÉSSZOLGÁLTATÓ által visszaadott fájl feldolgozásához.

3. Végül végezze el a [Azure stack hub PKI-tanúsítványok érvényesítésének](../operator/azure-stack-validate-pki-certs.md)lépéseit. A készültség-ellenőrző eszközt még egyszer használhatja az új tanúsítványon elvégzett ellenőrző tesztek végrehajtásához.


