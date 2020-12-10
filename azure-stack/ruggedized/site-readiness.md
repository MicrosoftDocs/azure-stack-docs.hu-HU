---
title: Azure Stack hub robusztus helyének felkészültsége Azure Stack hub esetében | Microsoft Docs
description: Megtudhatja, hogy a hely készültségi előírásai egy Azure Stack hub számára robusztusak.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: wamota
ms.reviewer: wamota
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 02e549d4cae9dc0c543d4a21c48e972231257ced
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939968"
---
# <a name="azure-stack-hub-ruggedized-site-readiness"></a>Azure Stack hub strapabíró helyének készültsége

Ez a témakör az Azure Stack hub-ra vonatkozó környezeti és PDU-előfeltételeket ismerteti. 

>[!NOTE]
>Ezek az értékek kizárólag a létesítmény tervezési céljaira szolgálnak, és közelítik és konzervatívak. A tényleges követelmények eltérőek lehetnek.

## <a name="environmental-requirements"></a>Környezeti követelmények

Az alábbi táblázat a Azure Stack hub robusztus megoldás környezeti követelményeit sorolja fel a következő konfigurációval:

- 14 TB-os méretezési egység
- 200 volt AC bemeneti feszültség
- 35 °C maximális környezeti hőmérséklet

*1. táblázat. Magas/alacsony konfigurációs környezeti követelmények*

| Objektum                         | Azure Stack hub robusztus követelményei               |
|--------------------------------|--------------------------------|
|Működési hőmérséklet           | Azure Stack hub strapabíró üzemeltetési hőmérséklete (fűtő követelményekkel):-32 °C (-25.6 °F) és 43 °C között (109 °F).    |
|Páratartalom és nedvesség           | Tárolás: 5% – 95% RH, 33 °C-ig (91 °F) maximális harmatpont. A légkörnek mindig nem lecsapódó kell lennie. <br> Üzemeltetés: 5% – 85% RH 29 °C-ig (84.2 °F) maximális harmatpont.
|Fizikai kapcsolat           | Azure Stack hub strapabíró lehet fizikailag csatlakoztatva a következőn keresztül: <br>4x10G SR SFP + <br>4x1000BASE – SX <br>4x 1000BASE-T
|Energiaellátás bemenete                     | Max 4,981 kW, AVG 4,391 KW<br> Bemeneti összekötő C13/C14<br> Bemenet: 100-240V 50/60Hz

## <a name="pdu-power-drop-requirements"></a>PDU-energiaellátási követelmények

A következő táblázat az Azure Stack hub strapabíró működéséhez szükséges adatvesztéseket sorolja fel.

*2. táblázat Szükséges áramkimaradások száma*

| Konfiguráció  | Egy fázis  | Háromfázisú Delta |Háromfázisú Wye |
|----------------|---------------|-------------------|----------------|
|Magas/alacsony        | 2             | 2                 | 2              |

Az Azure Stack integrált rendszer lehetővé teszi, hogy különböző PDU-összekötőket használjon az adatközpontba való legjobb integrációhoz. Az alábbi táblázat az összekötők típusait sorolja fel:

*3. táblázat. PDU-és összekötő-beállítások*

| Hely     | Egy fázis                                | Háromfázisú Delta                                   | Háromfázisú Wye                                        |
|--------------|---------------------------------------------|-----------------------------------------------------|-----------------------------------------------------------|
|Észak-Amerika |- L630P<br>-L7-30P<br>- Russellstoll 3750DP |-Hubbell Pro CS8365L<br>-Russellstoll 9P54U2T/1100 |– Hubbell C530P6S<br>-ABL Sursum S52S0A<br>-Repülő érdeklődők |


