---
title: A Event Hubs telepítésének előfeltételei az Azure Stack hub-ban
description: Ismerje meg a szükséges előfeltételeket, mielőtt telepítené a Event Hubs erőforrás-szolgáltatót az Azure Stack hub-on.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 6b081f5285daf5f6963c7626a62993f9ea8587c4
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90572204"
---
# <a name="prerequisites-for-installing-event-hubs-on-azure-stack-hub"></a>A Event Hubs telepítésének előfeltételei Azure Stack hubhoz

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

A következő előfeltételeket kell végrehajtania ahhoz, hogy telepíteni tudja a Event Hubst Azure Stack hub-ra. Az összes lépés végrehajtásához **több nap vagy hét szükséges** .

> [!IMPORTANT]
> Ezek az előfeltételek feltételezik, hogy már üzembe helyezte legalább egy 4 csomópontos Azure Stack hub integrált rendszerét. A Event Hubs erőforrás-szolgáltató nem támogatott a Azure Stack Development Kiton (ASDK).

> [!IMPORTANT]
> Event Hubs Azure Stack hub 2002 Build vagy újabb verzió szükséges. Vegye figyelembe, hogy Azure Stack hub-buildek növekményes. Ha például telepítve van a 1910-es [verzió](./release-notes.md?view=azs-1910#1910-build-reference) , akkor először a [2002](./release-notes.md?view=azs-2002#2002-build-reference)-ra, majd a 2005-ra kell frissítenie. Ez azt eredményezi, hogy nem lehet kihagyni a-ben létrehozott buildeket.

## <a name="common-prerequisites"></a>Gyakori előfeltételek

[!INCLUDE [Common RP prerequisites](../includes/marketplace-resource-provider-prerequisites.md)]

## <a name="event-hubs-prerequisites"></a>Előfeltételek Event Hubs

1. Nyilvános kulcsokra épülő infrastruktúra (PKI) SSL-tanúsítványainak beszerzése Event Hubshoz. A tulajdonos alternatív nevének (SAN) be kell tartania a következő elnevezési mintát: `CN=*.eventhub.<region>.<fqdn>` . A tulajdonos neve megadható, de nem használja Event Hubs a tanúsítványok kezelésekor. A rendszer csak a tulajdonos alternatív nevét használja. A részletes követelmények teljes listájáért tekintse meg a [PKI-tanúsítványokra vonatkozó követelményeket](azure-stack-pki-certs.md) .  

   ![Példa tanúsítványra](media/event-hubs-rp-prerequisites/certificate-example.png)

   > [!NOTE]
   > **A pfx-fájloknak jelszóval védettnek kell lenniük**. A rendszer a telepítés során később kéri a jelszót.

2. Ügyeljen rá, hogy ellenőrizze [a tanúsítvány érvényességét](azure-stack-validate-pki-certs.md). A cikk bemutatja, hogyan készítheti elő és ellenőrizheti a Event Hubs erőforrás-szolgáltatóhoz használt tanúsítványokat. 

## <a name="next-steps"></a>Következő lépések

Ezután [telepítse a Event Hubs erőforrás-szolgáltatót](event-hubs-rp-install.md).
