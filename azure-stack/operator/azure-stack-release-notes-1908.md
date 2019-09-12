---
title: Azure Stack 1908 kibocsátási megjegyzések | Microsoft Docs
description: Ismerkedjen meg a 1908-es frissítéssel Azure Stack integrált rendszerek esetében, beleértve az újdonságokat, az ismert problémákat, valamint a frissítés letöltésének helyét.
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
ms.date: 09/05/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 08/30/2019
monikerRange: azs-1908
ms.openlocfilehash: ad059e09bbd544e4709a237ee0c51cafc2a734b4
ms.sourcegitcommit: 9438b559ea314cc8c19fd710e1b2a6ceb15e50c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70912655"
---
# <a name="azure-stack-1908-update"></a>Azure Stack 1908 frissítés

*Vonatkozik: Integrált rendszerek Azure Stack*

Ez a cikk az 1908-es frissítési csomag tartalmát ismerteti. A frissítés a Azure Stack ezen kiadásának újdonságait és javításait tartalmazza.

> [!IMPORTANT]  
> Ez a csomag csak olyan Azure Stack integrált rendszerek. Ez a csomag nem vonatkoznak az Azure Stack Development Kit.

## <a name="previous-release-notes"></a>Korábbi kibocsátási megjegyzések

A 1908-es verziótól kezdődően a kibocsátási megjegyzések korábbi verziói már nem láthatók a bal oldali tartalomjegyzékben. A kibocsátási megjegyzések régebbi verzióinak eléréséhez válasszon egy másik cikket (például a [Azure stack áttekintést](azure-stack-overview.md)), majd válassza a 1905, 1906, 1907 vagy 1908 elemet a tartalomjegyzék tetején a bal oldalon. A kibocsátási megjegyzések korábbi verzióival kapcsolatban tekintse meg az [archivált kibocsátási megjegyzések](#archived-release-notes) szakaszt.

## <a name="build-reference"></a>Hivatkozás létrehozása

A Azure Stack 1908 frissítési Build száma **1.1908.0.20**.

### <a name="update-type"></a>Frissítés típusa

A 1908 esetében a Azure Stack futtatását futtató operációs rendszer frissítve lett a Windows Server 2019-es verziójára. Ez lehetővé teszi az alapvető fontosságú fejlesztéseket, valamint a közeljövőben Azure Stack további képességeket is.

A Azure Stack 1908 frissítési Build típusa **megtelt**. Ennek eredményeképpen az 1908-es frissítés hosszabb futtatókörnyezettel rendelkezik, mint az expressz frissítések, például a 1906 és a 1907. A teljes frissítésekhez tartozó pontos futtatókörnyezetek általában a Azure Stack-példány által tartalmazott csomópontok számától függenek, a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha csatlakozik az internethez) és a rendszer hardvere Configuration. Az 1908-es frissítés a belső tesztelés során a következő várt futtatókörnyezeteket használta: 4 csomópont – 42 óra, 8 csomópont – 50 óra, 12 csomópont – 60 óra, 16 csomópont – 70 óra. A várt értéknél hosszabb ideig tartó frissítési futtatókörnyezetek nem szokatlanok, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure Stackban](azure-stack-updates.md).

- A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszer hardveres konfigurációja alapján használt kapacitástól függenek.
- A vártnál hosszabb ideig tartó futtatókörnyezetek nem szokatlanok, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul.
- Ez a futásidejű közelítés az 1908-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- 1908 esetében vegye figyelembe, hogy az alapul szolgáló operációs rendszer, amelyen Azure Stack fut, frissítve lett a Windows Server 2019-es verziójára. Ez lehetővé teszi az alapvető fontosságú fejlesztéseket, valamint a közeljövőben Azure Stack további képességeket is.
- Azure Stack infrastruktúra összes összetevője FIPS 140-2 módban működik.
- Azure Stack operátorok mostantól eltávolíthatják a portál felhasználói adatfájljait. További információ: [a portál felhasználói adatainak törlése Azure Stackról](azure-stack-portal-clear.md).

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->
- A Azure Stack Rest titkosítású adatainak fejlesztése a fizikai csomópontok hardveres platformmegbízhatósági modul (TPM) számára a titkok megőrzése érdekében.

### <a name="changes"></a>Módosítások

- A hardveres szolgáltatók a 2,1-es vagy újabb verziójú OEM-bővítményt a 1908-es verzióval megegyező Azure Stack időben szabadítják fel. Az OEM-bővítmény 2,1-es vagy újabb verziója a 1908-es Azure Stack-verzió előfeltétele. Az OEM-bővítmény 2,1-es vagy újabb verziójának letöltésével kapcsolatos további információkért forduljon a rendszer hardver-szolgáltatójához, és tekintse meg az [OEM-frissítések](azure-stack-update-oem.md#oem-contact-information) című cikket.  

### <a name="fixes"></a>Javítások

- A jövőbeli Azure Stack OEM-frissítésekkel való kompatibilitással és az ügyfél felhasználói lemezképeit használó virtuálisgép-telepítéssel kapcsolatos probléma javítva. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Kijavított egy problémát az OEM belső vezérlőprogram frissítésével, és javította a AzureStack a Fabric Ring Health szolgáltatásban. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Kijavított egy problémát az OEM belső vezérlőprogram frissítési folyamatával kapcsolatban. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)


<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack ezen frissítésében található biztonsági frissítésekről: [Azure stack biztonsági frissítések](azure-stack-release-notes-security-updates.md).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](azure-stack-release-notes-known-issues-1908.md)
- [Biztonsági frissítések](azure-stack-release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>A frissítés letöltése

A Azure Stack 1908 frissítési csomagot [a Azure stack letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1907-es Azure Stack legújabb gyorsjavítást telepíti a 1908-es Azure Stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Előfeltételek: Az 1908-es frissítés alkalmazása előtt

A Azure Stack 1908-es kiadását a 1907-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1907.12.44](https://support.microsoft.com/help/4517473)

A Azure Stack 1908 frissítéshez **Azure stack OEM 2,1-es vagy újabb verzió** szükséges a rendszer hardver-szolgáltatójából. Az OEM-frissítések a Azure Stack rendszer hardverének illesztőprogram-és belső vezérlőprogram-frissítéseit tartalmazzák. További információ az OEM-frissítések alkalmazásáról: [Azure stack eredeti berendezésgyártó frissítéseinek alkalmazása](azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>Az 1908-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Nem érhető el gyorsjavítás a 1908-es kiadáshoz.

## <a name="automatic-update-notifications"></a>Automatikus frissítési értesítések

Az infrastruktúra-hálózatról az internetre elérő rendszerek az **elérhető frissítés** üzenetét fogják látni az operátori portálon. Az internet-hozzáféréssel nem rendelkező rendszerek a megfelelő. xml fájllal tölthetik le és importálhatók a. zip fájlt.

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
