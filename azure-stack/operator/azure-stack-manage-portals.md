---
title: A felügyeleti portál használatával az Azure Stackben |} A Microsoft Docs
description: Az Azure Stack operátorait szerint megtudhatja, hogyan használhatja a felügyeleti portált.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2019
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 71932642d8c44d2043fe185eab3ebda25be7bb3f
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618618"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>Gyors útmutató: az Azure Stack felügyeleti portál használata

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

Nincsenek két portált az Azure Stackben; a felügyeleti portál és a felhasználói portál (más néven a *bérlői* portálon.) Az Azure Stack-operátori: használhatja a felügyeleti portálon a napi szintű felügyeletéért és az Azure Stack műveletek.

## <a name="access-the-administrator-portal"></a>A felügyeleti portál elérése

A felügyeleti portálhoz való hozzáféréshez nyissa meg a portál URL-címét, és jelentkezzen be egy Azure Stack-operátor hitelesítő adataival. Az integrált rendszer a portál URL-cím változik alapul a régió neve és a külső teljesen minősített tartománynevét (FQDN) az Azure Stack üzembe helyezés. A felügyeleti portál URL-cím mindig azonos, az Azure Stack Development Kit (ASDK) központi telepítéséhez. 

| Környezet | Felügyeleti portál URL-címe |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| Integrált rendszerek | https://adminportal.&lt; *régió*&gt;.&lt; *Teljes Tartományneve*&gt; | 
| | |

> [!TIP]
> A ASDK környezetekben, először győződjön meg arról, hogy kell [development kit állomás csatlakozni](../asdk/asdk-connect.md) távoli asztali kapcsolaton keresztül, vagy virtuális magánhálózati (VPN) keresztül.

 ![A felügyeleti portál](media/azure-stack-manage-portals/admin-portal.png)

Az alapértelmezett időzóna az összes Azure Stack üzemelő példányhoz van beállítva az egyezményes világidő (UTC). 

A felügyeleti portálon teheti többek között:

* [Az Azure Stack regisztrálása az Azure-ban](azure-stack-registration.md)
* [A piactér feltöltése](azure-stack-download-azure-marketplace-item.md)
* [Hozzon létre a csomagok, ajánlatok és előfizetések számára](azure-stack-plan-offer-quota-overview.md)
* [Állapotfelügyelet és riasztások](azure-stack-monitor-health.md)
* [Azure Stack frissítéseinek kezelése](azure-stack-updates.md)

A **a rövid útmutató** csempe hivatkozások a leggyakoribb feladatokat az online dokumentációt.

Bár az operátornak a felügyeleti portálon hozhat létre erőforrásokat, például a virtuális gépek, virtuális hálózatok és tárfiókok, akkor [jelentkezzen be a felhasználói portál](../user/azure-stack-use-portal.md) hozhat létre, és tesztelje az erőforrásokat.

>[!NOTE]
>A **hozzon létre egy virtuális gépet** csatolása a rövid útmutató csempe rendelkezik, hozzon létre egy virtuális gépet a felügyeleti portálon, de ez csak a ellenőrzése, hogy az Azure Stack sikeresen üzembe helyezték szolgál.

## <a name="understand-subscription-behavior"></a>Előfizetés a viselkedés értelmezése

A felügyeleti portálon; alapértelmezés szerint létrehozott három előfizetés használat alapértelmezett szolgáltató és mérési. Kezelőként többnyire használhatja a *szolgáltatói előfizetés alapértelmezett*. Nem lehet bármely más előfizetéseket, és használhatja azokat a felügyeleti portálon. 

Egyéb előfizetések a felhasználók a felhasználói portálon, a csomagok és ajánlatok hoz létre, azok alapján jönnek létre. A felhasználói portál azonban nem biztosít hozzáférést a felügyeleti portál felügyeleti vagy működési képességeit.

A felügyeleti és a felhasználói portált különálló példány az Azure Resource Manager élvezik. Az Azure Resource Manager-elkülönítés, mert az előfizetések nem lépi túl portálokat. Például, mint az Azure Stack-operátori jelentkezik be a felhasználói portálra, ha nem fér hozzá a *szolgáltatói előfizetés alapértelmezett*. Bár nem kell minden olyan felügyeleti funkciók elérését, létrehozhat saját előfizetések elérhető nyilvános ajánlatokat. Mindaddig, amíg a felhasználói portálra van bejelentkezve, egy bérlő felhasználói minősülnek.

  >[!NOTE]
  >ASDK környezetben ha egy felhasználó tartozik, az Azure Stack-operátori bérlői könyvtárába, nincs letiltva a jelentkezik be a felügyeleti portálon. Nem lehet azonban, fér hozzá a felügyeleti funkciók egyik, vagy adja hozzá az előfizetés eléréséhez kínál, amelyek számára elérhető a felhasználói portálon.

## <a name="administration-portal-tips"></a>Felügyeleti portál tippek

### <a name="customize-the-dashboard"></a>Az irányítópult testreszabásával

Az irányítópult tartalmaz az alapértelmezett csempék jelennek meg. Választhat **irányítópult szerkesztése** módosítsa az alapértelmezett irányítópult, vagy válasszon **új irányítópult** egyéni irányítópult hozzáadása. Egyszerűen hozzáadhatja csempét az irányítópulton. Kiválaszthatja például **+ erőforrás létrehozása**, kattintson a jobb gombbal **ajánlatok és csomagok**, majd válassza ki **rögzítés az irányítópulton**.

Egyes esetekben jelenhet meg egy üres irányítópult, a portálon. Az irányítópult helyreállítani, kattintson **irányítópult szerkesztése**, majd kattintson a jobb gombbal, és válassza ki **visszaállítja az alapértelmezett állapotba**.

### <a name="quick-access-to-online-documentation"></a>Online dokumentáció gyors elérése

Hozzáférhet az Azure Stack operátori dokumentációja, használja a Súgó és ikonra (kérdőjel) támogatja a felügyeleti portál jobb felső sarkában. Helyezze a kurzort a ikonra, és válassza ki **súgó + támogatás**.

### <a name="quick-access-to-help-and-support"></a>Súgó és támogatás gyors elérése

Ha a felügyeleti portál jobb felső sarkában válassza ki a Súgó és támogatás ikonra (kérdőjel), és válassza **új támogatási kérelem**, egyet az alábbi eredményeket történik:

- Integrált rendszer használja, ha ez a művelet megnyitja a hely, ahol közvetlenül megnyithatja egy támogatási jegyet a Microsoft ügyfél támogatási szolgálat (CSS). Tekintse meg [kérhet támogatást Where](azure-stack-manage-basics.md#where-to-get-support) megértéséhez, hogy mikor kell lépjen a Microsoft támogatási vagy a számítógépgyártó (OEM) hardver szállítójával támogatási keresztül.
- Ha a ASDK használ, ez a művelet megnyitja a [Azure Stack-fórumok](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack) közvetlenül. Ezek a fórumok rendszeresen figyeli a program. Mivel a ASDK kiértékelési környezet, rendszer nem hivatalos támogatja a Microsoft CSS keresztül érhető el.

### <a name="quick-access-to-the-azure-roadmap"></a>Gyors elérése az Azure-ütemterv

Ha **Súgó és támogatás** (a kérdőjel) a felügyeleti portálra, és majd kattintson a jobb felső sarokban **Azure ütemterve**, egy új böngészőlapon nyílik meg, és megnyílik az Azure-ütemterv. Beírásával **Azure Stack** a a **termékek** keresőmezőbe, láthatja az összes Azure Stack ütemterv frissítés.

## <a name="next-steps"></a>További lépések

[Regisztráljon az Azure Stack az Azure-ral](azure-stack-registration.md) , és töltse fel a [Azure Stack piactéren](azure-stack-marketplace.md) elemek nyújtani a felhasználóknak. 
