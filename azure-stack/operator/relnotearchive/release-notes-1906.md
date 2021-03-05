---
title: Azure Stack 1906 kibocsátási megjegyzések | Microsoft Docs
description: Tudnivalók a Azure Stack integrált rendszerek frissítéseiről 1906
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 9a51d7c7b1470e95fd9c8b6905c2ac31f59bf7b9
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187248"
---
# <a name="azure-stack-updates-1906-release-notes"></a>Azure Stack frissítések: 1906 kibocsátási megjegyzések

Ez a cikk a Azure Stack frissítési csomagjainak tartalmát ismerteti. A frissítés a Azure Stack ezen kiadásának újdonságait és javításait tartalmazza.

Ha egy másik verzió kibocsátási megjegyzéseit szeretné elérni, a bal oldali tartalomjegyzéknél használja a verziószám legördülő menüt.

> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

> [!IMPORTANT]  
> Ha a Azure Stack-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](known-issues-1906.md)
- [Biztonsági frissítések](../release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](../release-notes-checklist.md)

A frissítésekkel és a frissítési folyamattal kapcsolatos segítségért tekintse meg a [javítások és a frissítési problémák elhárítása Azure stack](../azure-stack-troubleshooting.md).

## <a name="1906-build-reference"></a>1906-Build referenciája

A Azure Stack 1906 frissítési Build száma **1.1906.0.30**.

### <a name="update-type"></a>Frissítéstípus

A Azure Stack 1906 frissítési Build típusa **expressz**. További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack](../azure-stack-updates.md) cikkben. Az 1906-es frissítés befejezéséhez szükséges idő körülbelül 10 óra, a Azure Stack-környezetben található fizikai csomópontok számától függetlenül. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek. A várt értéknél hosszabb ideig tartó futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul. Ez a futásidejű közelítés az 1906-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

## <a name="whats-in-this-update"></a>A frissítés ismertetése

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Egy **set-TLSPolicy** parancsmagot adott hozzá a privilegizált végponthoz (PEP) a TLS 1,2 kényszerítéséhez az összes végponton. További információ: [Azure stack biztonsági vezérlők](../azure-stack-security-configuration.md).

- Hozzáadott egy **Get-TLSPolicy** parancsmagot a Privileged ENDPOINT (PEP) alkalmazásban az alkalmazott TLS-szabályzat lekéréséhez. További információ: [Azure stack biztonsági vezérlők](../azure-stack-security-configuration.md).

- Belső titkos elforgatási eljárás hozzáadva a belső TLS-tanúsítványok rendszerfrissítés során történő elforgatásához.

- A belső titkokat a belső titkok kiváltásának megakadályozása érdekében hozzáadott egy olyan védelmet, amely miatt a rendszer figyelmen kívül hagyja a kritikus riasztást a lejáró titkos kulcsok esetében. Ez nem használható rendszeres működési eljárásként. A titkok rotációját a karbantartási időszakban kell tervezni. További információ: [Azure stack Secret rotációs](../azure-stack-rotate-secrets.md).

- A Visual Studio Code mostantól támogatja a Azure Stack a AD FS használatával történő üzembe helyezést.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- A privilegizált végpont **Get-GraphApplication** parancsmagja mostantól megjeleníti az aktuálisan használt tanúsítvány ujjlenyomatát. Ez javítja az egyszerű szolgáltatásnév felügyeletét, ha a Azure Stack AD FStal van üzembe helyezve.

- Új állapot-figyelési szabályok lettek hozzáadva az AD Graph és a AD FS rendelkezésre állásának ellenőrzéséhez, beleértve a riasztások megadásának lehetőségét is.

- A biztonsági mentési erőforrás-szolgáltató megbízhatóságának fejlesztése, ha az infrastruktúra-mentési szolgáltatás egy másik példányra kerül át.

- A külső titkos rotációs eljárás teljesítményének optimalizálása egy egységes végrehajtási idő biztosításához a karbantartási időszak ütemezésének megkönnyítése érdekében.

- A **test-AzureStack** parancsmag mostantól jelentést készít a hamarosan lejáró belső titkokról (kritikus riasztások).

- Új paraméter érhető el a (z) **CustomAdfs** parancsmaghoz a privilegizált végponton, amely lehetővé teszi a visszavont tanúsítványok listájának ellenőrzését a AD FS összevonási megbízhatóságának konfigurálásakor.

- Az 1906-es kiadás nagyobb láthatóságot mutat be a frissítés folyamatában, így biztos lehet benne, hogy a frissítések nem szünetelnek. Ez a **frissítés** panelen megjelenő frissítési lépések teljes számának növekedését eredményezi. Előfordulhat, hogy a korábbi frissítéseknél párhuzamosan további frissítési lépések is előfordulhatnak.

#### <a name="networking-updates"></a>Hálózatkezelési frissítések

- A DHCP-válaszadóban beállított címbérleti idő konzisztens az Azure-ban.

- Továbbfejlesztett újrapróbálkozási díjak az erőforrás-szolgáltatónak az erőforrások sikertelen üzembe helyezése esetén.

- A **standard** SKU lehetőség el lett távolítva a terheléselosztó és a nyilvános IP-cím közül, mivel ez jelenleg nem támogatott.

### <a name="changes"></a>Módosítások

- A Storage-fiókokkal kapcsolatos felhasználói élmény mostantól konzisztens az Azure-ban.

- Módosított riasztási eseményindítók a belső titkok lejáratához:
  - A figyelmeztetési riasztások mostantól 90 nappal a titkok lejárta előtt jelennek meg.
  - A kritikus riasztások mostantól 30 nappal a titkok lejárta előtt jelennek meg.

- Frissített karakterláncok az infrastruktúra biztonsági mentési erőforrás-szolgáltatójában konzisztens terminológiához.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Kijavított egy problémát, amelyben a felügyelt lemezes virtuális gép átméretezése egy **belső működési hiba** miatt meghiúsult.

- Kijavítva a hiba, amikor egy sikertelen felhasználói lemezkép létrehozásakor a lemezképet kezelő szolgáltatás helytelen állapotban van. Ez blokkolja a sikertelen lemezkép törlését és az új rendszerképek létrehozását. Ezt a 1905-es gyorsjavítás is rögzíti.

- A belső titkos kulcsok sikeres végrehajtása után a rendszer mostantól automatikusan bezárja az aktív riasztásokat a lejáró belső titkokon.

- Kijavítva egy probléma, amelyben a frissítés időtartama a frissítési Előzmények lapon az első számjegyet fogja kimetszeni, ha a frissítés több mint 99 órán keresztül futott.

- A **frissítés** panel tartalmaz egy **folytatási** lehetőséget a sikertelen frissítésekhez.

- A rendszergazda és a felhasználói portálon kijavíthatja a problémát a piactéren, amelyben a Docker-bővítmény helytelenül tért vissza a keresésből, de nem végezhető el további művelet, mivel Azure Stack nem érhető el.

- Kijavított egy problémát a sablon telepítési felhasználói felületén, amely nem adja meg a paramétereket, ha a sablon neve _ aláhúzással kezdődik.

- Kijavítva a probléma, hogy a virtuálisgép-méretezési csoport létrehozása során a CentOS-alapú 7,2-es verzió üzembe helyezési lehetőséget biztosít. A CentOS 7,2 Azure Stackon nem érhető el. Mostantól a CentOS 7,5-es verzióval biztosítjuk az üzembe helyezési lehetőséget

- Mostantól eltávolíthat egy méretezési csoportot a **virtuálisgép-méretezési** csoportok paneljéről.

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack ezen frissítésében található biztonsági frissítésekről: [Azure stack biztonsági frissítések](../release-notes-security-updates.md).

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1906 frissítési csomagot [a Azure stack letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1905-es Azure Stack legújabb gyorsjavítást telepíti a 1906-es Azure Stack frissítése előtt. A frissítés után telepítse a [1906 összes elérhető gyorsjavítását](#after-successfully-applying-the-1906-update).

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="before-applying-the-1906-update"></a>Az 1906-es frissítés alkalmazása előtt

A Azure Stack 1906-es kiadását a 1905-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Az 1906-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1906.15.60](https://support.microsoft.com/help/4524559)

## <a name="next-steps"></a>Következő lépések

- A Azure Stack Update Management áttekintését lásd: a [frissítések kezelése Azure stack áttekintés](../azure-stack-updates.md).  
- A frissítések Azure Stack használatával történő alkalmazásával kapcsolatos további információkért lásd: [frissítések alkalmazása a Azure Stackban](../azure-stack-apply-updates.md).
- A Azure Stack integrált rendszerek karbantartási szabályzatának áttekintéséhez, valamint arról, hogy mit kell tennie, hogy a rendszer támogatott állapotban maradjon, tekintse meg [Azure stack karbantartási házirendet](../azure-stack-servicing-policy.md).  
- Ha a jogosultsági szintű végpontot (PEP) szeretné használni a frissítések figyeléséhez és folytatásához, tekintse meg a következő témakört: [frissítések figyelése Azure stack az emelt szintű végpont használatával](../azure-stack-monitor-update.md).