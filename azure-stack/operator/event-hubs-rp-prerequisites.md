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
ms.openlocfilehash: 2b3fcfb0325689de08e77a9fbf599d023ba26a4c
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343513"
---
# <a name="prerequisites-for-installing-event-hubs-on-azure-stack-hub"></a>A Event Hubs telepítésének előfeltételei Azure Stack hubhoz

A következő előfeltételeket kell végrehajtania ahhoz, hogy telepíteni tudja a Event Hubst Azure Stack hub-ra. Az összes lépés végrehajtásához **több nap vagy hét szükséges** .

> [!IMPORTANT]
> Ezek az előfeltételek feltételezik, hogy már üzembe helyezte legalább egy 4 csomópontos Azure Stack hub integrált rendszerét. A Event Hubs erőforrás-szolgáltató nem támogatott a Azure Stack Development Kiton (ASDK).

> [!IMPORTANT]
> Event Hubs Azure Stack hub 2002 Build vagy újabb verzió szükséges. Vegye figyelembe, hogy Azure Stack hub-buildek növekményes. Ha például telepítve van a 1910-es verzió, akkor először a [2002](./release-notes.md?view=azs-2002&preserve-view=true#2002-build-reference)-ra, majd a 2005-ra kell frissítenie. Ez azt eredményezi, hogy nem lehet kihagyni a-ben létrehozott buildeket.

## <a name="common-prerequisites"></a>Gyakori előfeltételek

[!INCLUDE [Common RP prerequisites](../includes/resource-provider-prerequisites.md)]

## <a name="event-hubs-prerequisites"></a>Előfeltételek Event Hubs

1. Nyilvános kulcsokra épülő infrastruktúra (PKI) SSL-tanúsítványainak beszerzése Event Hubshoz. A tulajdonos alternatív nevének (SAN) be kell tartania a következő elnevezési mintát: `CN=*.eventhub.<region>.<fqdn>` . A tulajdonos neve megadható, de nem használja Event Hubs a tanúsítványok kezelésekor. A rendszer csak a tulajdonos alternatív nevét használja. A részletes követelmények teljes listájáért tekintse meg a [PKI-tanúsítványokra vonatkozó követelményeket](azure-stack-pki-certs.md) .  

   ![Példa tanúsítványra](media/event-hubs-rp-prerequisites/certificate-example.png)

   > [!NOTE]
   > **A pfx-fájloknak jelszóval védettnek kell lenniük**. A rendszer a telepítés során később kéri a jelszót.

2. Ügyeljen rá, hogy ellenőrizze [a tanúsítvány érvényességét](azure-stack-validate-pki-certs.md). A cikk bemutatja, hogyan készítheti elő és ellenőrizheti a Event Hubs erőforrás-szolgáltatóhoz használt tanúsítványokat. 

## <a name="next-steps"></a>Következő lépések

Ezután [telepítse a Event Hubs erőforrás-szolgáltatót](event-hubs-rp-install.md).
