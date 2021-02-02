---
title: Azure Stack 1908 kibocsátási megjegyzések | Microsoft Docs
description: Tudnivalók a Azure Stack 1908 integrált rendszerek frissítéseiről
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
ms.openlocfilehash: 6716b31431106e0af43785177376e4980b864f81
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248308"
---
# <a name="azure-stack-updates-1908-release-notes"></a>Azure Stack frissítések: 1908 kibocsátási megjegyzések

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

Ez a cikk a Azure Stack frissítési csomagjainak tartalmát ismerteti. A frissítés a Azure Stack ezen kiadásának újdonságait és javításait tartalmazza.

Ha egy másik verzió kibocsátási megjegyzéseit szeretné elérni, a bal oldali tartalomjegyzéknél használja a verziószám legördülő menüt.

> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kit.

> [!IMPORTANT]  
> Ha a Azure Stack-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [Ismert problémák](known-issues-1908.md)
- [Biztonsági frissítések](../release-notes-security-updates.md)
- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](../release-notes-checklist.md)

A frissítésekkel és a frissítési folyamattal kapcsolatos segítségért tekintse meg a [javítások és a frissítési problémák elhárítása Azure stack](../azure-stack-updates-troubleshoot.md).

## <a name="1908-build-reference"></a>1908-Build referenciája

A Azure Stack 1908 frissítési Build száma **1.1908.4.33**.

### <a name="update-type"></a>Frissítéstípus

A 1908 esetében a Azure Stack futtatását futtató operációs rendszer frissítve lett a Windows Server 2019-es verziójára. Ez lehetővé teszi az alapvető fontosságú fejlesztéseket, valamint a közeljövőben Azure Stack további képességeket is.

A Azure Stack 1908 frissítési Build típusa **megtelt**. Ennek eredményeképpen az 1908-es frissítés hosszabb futtatókörnyezettel rendelkezik, mint az expressz frissítések, például a 1906 és a 1907. A teljes frissítésekhez tartozó pontos futtatókörnyezetek általában a Azure Stack-példány által tartalmazott csomópontok számától függenek, a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha csatlakozik az internethez) és a rendszerhardver konfigurációja alapján. Az 1908-es frissítés a belső tesztelés következő várt futtatókörnyezeteit használta: 4 csomópont – 42 óra, 8 csomópont – 50 óra, 12 csomópont – 60 óra, 16 csomópont – 70 óra. A várt értéknél hosszabb ideig tartó frissítési futtatókörnyezetek nem szokatlanok, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure Stackban](../azure-stack-updates.md).

- A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszer hardveres konfigurációja alapján használt kapacitástól függenek.
- A vártnál hosszabb ideig tartó futtatókörnyezetek nem szokatlanok, és nem igényelnek beavatkozást Azure Stack operátorok, kivéve, ha a frissítés meghiúsul.
- Ez a futásidejű közelítés az 1908-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack frissítésekkel.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- 1908 esetében vegye figyelembe, hogy az alapul szolgáló operációs rendszer, amelyen Azure Stack fut, frissítve lett a Windows Server 2019-es verziójára. Ez lehetővé teszi az alapvető fontosságú fejlesztéseket, valamint a közeljövőben Azure Stack további képességeket is.
- Azure Stack infrastruktúra összes összetevője FIPS 140-2 módban működik.
- Azure Stack operátorok mostantól eltávolíthatják a portál felhasználói adatfájljait. További információ: [a portál felhasználói adatainak törlése Azure Stackról](../azure-stack-portal-clear.md).

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->
- A Azure Stack Rest titkosítású adatainak fejlesztése a fizikai csomópontok hardveres platformmegbízhatósági modul (TPM) számára a titkok megőrzése érdekében.

### <a name="changes"></a>Módosítások

- A hardveres szolgáltatók a 2,1-es vagy újabb verziójú OEM-bővítményt a 1908-es verzióval megegyező Azure Stack időben szabadítják fel. Az OEM-bővítmény 2,1-es vagy újabb verziója a 1908-es Azure Stack-verzió előfeltétele. Az OEM-bővítmény 2,1-es vagy újabb verziójának letöltésével kapcsolatos további információkért forduljon a rendszer hardver-szolgáltatójához, és tekintse meg az [OEM-frissítések](../azure-stack-update-oem.md#oem-contact-information) című cikket.  

### <a name="fixes"></a>Javítások

- A jövőbeli Azure Stack OEM-frissítésekkel való kompatibilitással és az ügyfél felhasználói lemezképeit használó virtuálisgép-telepítéssel kapcsolatos probléma javítva. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Kijavított egy problémát az OEM belső vezérlőprogram frissítésével és a Test-AzureStack a Fabric-gyűrű állapotának javításával. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Kijavított egy problémát az OEM belső vezérlőprogram frissítési folyamatával kapcsolatban. Ez a probléma a 1907-es verzióban található, és javítva lett a gyorsjavítások [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack ezen frissítésében található biztonsági frissítésekről: [Azure stack biztonsági frissítések](../release-notes-security-updates.md).

## <a name="download-the-update"></a><a name="download-the-update-1908"></a>A frissítés letöltése

A Azure Stack 1908 frissítési csomagot [a Azure stack letöltési oldaláról](https://aka.ms/azurestackupdatedownload)töltheti le.

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack a gyorsjavításokat rendszeresen kiadják. Győződjön meg arról, hogy a 1907-es Azure Stack legújabb gyorsjavítást telepíti a 1908-es Azure Stack frissítése előtt.

Azure Stack gyorsjavítások csak Azure Stack integrált rendszerekre alkalmazhatók. Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Előfeltételek: az 1908-es frissítés alkalmazása előtt

A Azure Stack 1908-es kiadását a 1907-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1907.17.54](https://support.microsoft.com/help/4523826)

A Azure Stack 1908 frissítéshez **Azure stack OEM 2,1-es vagy újabb verzió** szükséges a rendszer hardver-szolgáltatójából. Az OEM-frissítések a Azure Stack rendszer hardverének illesztőprogram-és belső vezérlőprogram-frissítéseit tartalmazzák. További információ az OEM-frissítések alkalmazásáról: [Azure stack eredeti berendezésgyártó frissítéseinek alkalmazása](../azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>Az 1908-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást. További információkért tekintse meg a [karbantartási szabályzatot](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack gyorsjavítás 1.1908.6.37](https://support.microsoft.com/help/4527372)
