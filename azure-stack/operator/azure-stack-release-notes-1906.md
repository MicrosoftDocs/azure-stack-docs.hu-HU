---
title: Azure Stack 1906 kibocsátási megjegyzések | Microsoft Docs
description: Ismerkedjen meg a 1906-es frissítéssel Azure Stack integrált rendszerek esetében, beleértve az újdonságokat, az ismert problémákat, valamint a frissítés letöltésének helyét.
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
ms.date: 07/15/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/15/2019
ms.openlocfilehash: c646ca836728cc1963354afb66ee04ee0accf1b6
ms.sourcegitcommit: fc7da38321736e952b2cc6d5d07f276d095dc8d1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887111"
---
# <a name="azure-stack-1906-update"></a>Azure Stack 1906 frissítés

*Vonatkozik: Integrált rendszerek Azure Stack*

Ez a cikk az 1906-es frissítési csomag tartalmát ismerteti. A frissítés a Azure Stack ezen kiadásának újdonságait és javításait tartalmazza.

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="build-reference"></a>Hivatkozás létrehozása

A Azure Stack 1906 frissítési Build száma **1.1906.0.30**.

### <a name="update-type"></a>Frissítés típusa

A Azure Stack 1906 frissítési Build típusa **expressz**. További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack](azure-stack-updates.md) cikkben. Az 1906-es frissítés befejezéséhez szükséges idő körülbelül 10 óra, a Azure Stack-környezetben található fizikai csomópontok számától függetlenül. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek. A várt értéknél hosszabb ideig tartó futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul. Ez a futásidejű közelítés az 1906-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

## <a name="whats-in-this-update"></a>A frissítés ismertetése

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Egy **set-TLSPolicy** parancsmagot adott hozzá a privilegizált végponthoz (PEP) a TLS 1,2 kényszerítéséhez az összes végponton. További információ: [Azure stack biztonsági vezérlők](azure-stack-security-configuration.md).

- Hozzáadott egy **Get-TLSPolicy** parancsmagot a Privileged ENDPOINT (PEP) alkalmazásban az alkalmazott TLS-szabályzat lekéréséhez. További információ: [Azure stack biztonsági vezérlők](azure-stack-security-configuration.md).

- Belső titkos elforgatási eljárás hozzáadva a belső TLS-tanúsítványok rendszerfrissítés során történő elforgatásához.

- A belső titkokat a belső titkok kiváltásának megakadályozása érdekében hozzáadott egy olyan védelmet, amely miatt a rendszer figyelmen kívül hagyja a kritikus riasztást a lejáró titkos kulcsok esetében. Ez nem használható rendszeres működési eljárásként. A titkok rotációját a karbantartási időszakban kell tervezni. További információ: [Azure stack Secret rotációs](azure-stack-rotate-secrets.md).

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

- Kijavított egy problémát, amelyben a felügyelt lemezes virtuális gép átméretezése egy **belső működési hiba**miatt meghiúsult.

- Kijavítva a hiba, amikor egy sikertelen felhasználói lemezkép létrehozásakor a lemezképet kezelő szolgáltatás helytelen állapotban van. Ez blokkolja a sikertelen lemezkép törlését és az új rendszerképek létrehozását. Ezt a 1905-es gyorsjavítás is rögzíti.

- A belső titkos kulcsok sikeres végrehajtása után a rendszer mostantól automatikusan bezárja az aktív riasztásokat a lejáró belső titkokon.

- Kijavítva egy probléma, amelyben a frissítés időtartama a frissítési Előzmények lapon az első számjegyet fogja kimetszeni, ha a frissítés több mint 99 órán keresztül futott.

- A **frissítés** panel tartalmaz egy **folytatási** lehetőséget a sikertelen frissítésekhez.

- A rendszergazda és a felhasználói portálon kijavíthatja a problémát a piactéren, amelyben a Docker-bővítmény helytelenül tért vissza a keresésből, de nem végezhető el további művelet, mivel Azure Stack nem érhető el.

- Kijavított egy problémát a sablon telepítési felhasználói felületén, amely nem adja meg a paramétereket, ha a sablon neve _ aláhúzással kezdődik.

- Kijavítva a probléma, hogy a virtuálisgép-méretezési csoport létrehozása során a CentOS-alapú 7,2-es verzió üzembe helyezési lehetőséget biztosít. A CentOS 7,2 Azure Stackon nem érhető el. Mostantól a CentOS 7,5-es verzióval biztosítjuk az üzembe helyezési lehetőséget

- Mostantól eltávolíthat egy méretezési csoportot a **virtuálisgép** -méretezési csoportok paneljéről.

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack ezen frissítésében található biztonsági frissítésekről: [Azure stack biztonsági frissítések](azure-stack-release-notes-security-updates-1906.md).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](azure-stack-release-notes-known-issues-1906.md)
- [Biztonsági frissítések](azure-stack-release-notes-security-updates-1906.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](azure-stack-release-notes-checklist.md)

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

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1906.12.54](https://support.microsoft.com/help/4518365)

## <a name="automatic-update-notifications"></a>Automatikus frissítési értesítések

Az infrastruktúra-hálózatról az internetre elérő rendszerekkel rendelkező ügyfelek az **elérhető frissítés** üzenet jelenik meg az operátori portálon. Az internet-hozzáféréssel nem rendelkező rendszerek a megfelelő. xml fájllal tölthetik le és importálhatók a. zip fájlt.

> [!TIP]  
> Feliratkozás a következő *RSS* -vagy *Atom* -hírcsatornára Azure stack gyorsjavítások megtartásához:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivált kibocsátási megjegyzések

[A Azure stack kibocsátási megjegyzések régebbi verzióit a TechNet Gallery webhelyen](https://aka.ms/azsarchivedrelnotes)tekintheti meg. Ezek az archivált kibocsátási megjegyzések kizárólag referencia jellegűek, és nem jelentenek támogatást ezekhez a verziókhoz. További információ a Azure Stack támogatásáról: [Azure stack karbantartási szabályzat](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálatához.

## <a name="next-steps"></a>További lépések

- A frissítéskezelés az Azure Stackben áttekintését lásd: [kezelheti a frissítéseket az Azure Stack áttekintés](azure-stack-updates.md).  
- Az Azure Stack frissítések alkalmazásával kapcsolatos további információkért lásd: [alkalmazza a frissítéseket az Azure Stackben](azure-stack-apply-updates.md).
- Az Azure Stack integrált rendszerek, és hogy mit kell tennie, hogy a rendszer egy támogatott állapotban karbantartási házirend áttekintéséhez lásd: [karbantartási szabályzat az Azure Stack](azure-stack-servicing-policy.md).  
- Az emelt szintű végpontját (EGP) használatával figyelheti, és folytathatja a frissítéseket, lásd: [monitorozása az Azure Stack használatával a privilegizált végpont frissítések](azure-stack-monitor-update.md).  
