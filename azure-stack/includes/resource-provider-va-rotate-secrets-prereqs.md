---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 294f0f5b1f6ac45d53f5d3eebc222b3edba23776
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "93049758"
---
> [!NOTE]
> Az érték – erőforrás-szolgáltatók hozzáadására szolgáló titkos rotációs szolgáltatás jelenleg csak a PowerShell használatával támogatott. 

Az Azure Stack hub-infrastruktúrához hasonlóan a Value – erőforrás-szolgáltatók is a belső és külső titkokat használják. A titkok több űrlapot is igénybe vehetnek, beleértve a jelszavakat és a X509-tanúsítványok által karbantartott titkosítási kulcsokat is. Operátorként a következőkért felelős:

- A frissített külső titkok, például az erőforrás-szolgáltatói végpontok biztonságossá tételéhez használt új TLS-tanúsítvány biztosítása.
- Az erőforrás-szolgáltatói titok rotációjának kezelése rendszeresen történik.

Felkészülés a rotációs folyamatra:

1. A X509-tanúsítvány beszerzése vagy megújítása előtt tekintse át [Azure stack hub nyilvánoskulcs-infrastruktúrájának (PKI) tanúsítványokra vonatkozó követelményeit](../operator/azure-stack-pki-certs.md#certificate-requirements) , beleértve a szükséges pfx-formátum részleteit is. Tekintse át a nem kötelezően megjelenő, a megadott érték – erőforrás-szolgáltató hozzáadása [című szakaszban](../operator/azure-stack-pki-certs.md#optional-paas-certificates)megadott követelményeket is.

2. Ha még nem tette meg, a folytatás előtt [telepítse a PowerShellt az Azure stack hub modulhoz](../operator/powershell-install-az-module.md) . Az Azure Stack hub Secret rotációs szolgáltatáshoz a 2.0.2 – Preview vagy újabb verzió szükséges. További információ: [áttelepítés a AzureRM-ből Azure PowerShell az Azure stack hub-ban](../operator/migrate-azurerm-az.md).
