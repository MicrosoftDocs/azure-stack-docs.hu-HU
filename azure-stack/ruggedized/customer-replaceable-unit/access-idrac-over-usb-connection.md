---
title: IDRAC elérése USB-kapcsolaton keresztül
description: Ismerje meg, hogyan érheti el az iDRAC USB-kapcsolaton keresztül
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 5c270de88d78bb8cb7ba1f7b9216c4a160a4aae8
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910058"
---
# <a name="accessing-the-idrac-interface-over-a-direct-usb-connection"></a>A iDRAC felület elérése közvetlen USB-kapcsolaton keresztül

A iDRAC Direct szolgáltatással közvetlenül csatlakoztatható a laptop a iDRAC USB-porthoz. Ez a funkció lehetővé teszi a közvetlen interakciót a iDRAC felületekkel, például a webes felülettel, a RACADM és a WSMan a speciális kiszolgálók kezeléséhez és karbantartásához.



Ha a iDRAC felületet az USB-porton keresztül szeretné elérni, tegye a következőket a laptopról.

**Lépések**

1.  A laptopról kapcsolja ki a vezeték nélküli hálózatokat, és szakítsa meg a kapcsolatot bármely más vezetékes hálózattal.

2.  Csatlakoztasson egy Micro USB-kábelt a laptopról a iDRAC Direct portra, amely a kiszolgáló elején található.
    Tekintse át a diagram 4. tételét.

    ![A főkapcsoló gombot, az U S B és a Micro U S B portot bemutató diagram.](media/image-67.png)

3.  Várjon, amíg a laptop megvásárolja az IP-169.254.0.4.

    Az IP-címek beszerzése több másodpercig is eltarthat. A iDRAC beszerzi az IP-169.254.0.3.

4.  Kapcsolódjon a iDRAC webes felületéhez.

    A iDRAC webes felületének eléréséhez nyisson meg egy böngészőt, és nyissa meg a 169.254.0.3.

5.  Végezze el a szükséges tevékenységeket.

    

    > [!NOTE]
    > Ha a iDRAC az USB-portot használja, amely a fenti ábrán szereplő 3. elem, a LED villog, amely a tevékenységre mutat. A villogás gyakorisága négy másodpercenként.
    
6.  Válassza le a Micro USB-kábelt.

    A kívánt műveletek befejezése után válassza le az USB-kábelt a rendszerből. A LED kikapcsol.
    
