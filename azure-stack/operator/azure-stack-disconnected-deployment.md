---
title: Az Azure leválasztott telepítési döntések meghozatalában az Azure Stack integrált rendszerek |} A Microsoft Docs
description: Tervezési megfontolások a több csomópontos, az Azure Stack Azure-kapcsolattal rendelkező központi telepítések központi telepítési határozza meg.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: a3986bcdff911fb70957dcb7529a07f77b586c0a
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131509"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Az Azure leválasztott telepítés tervezési megfontolások az Azure Stack integrált rendszerek
Miután döntött [hogyan fogja integrálja az Azure Stack a hibridfelhő-környezet](azure-stack-connection-models.md), az Azure Stack telepítési döntések is véglegesítése.

Telepítheti és használhatja az Azure Stack Internet kapcsolat nélkül. Azonban egy kapcsolat nélküli telepítés pedig csak az AD FS ügyfélidentitás-tárolóval, és a kapacitás-alapú számlázási modell. Több-bérlős módhoz az Azure Active Directory (Azure AD) használatát igényli, mert kapcsolat nélküli telepítés esetén nem támogatott több-bérlős módhoz. 

Válassza ezt a beállítást, ha Ön:
- Biztonsági vagy egyéb korlátozásokat, amelyek megkövetelik olyan környezetben, amely nem csatlakozik az internethez az Azure Stack üzembe helyezése rendelkezik.
- Szeretné blokkolni data (használati adatok is beleértve) az Azure-ba történő elküldését.
- Szeretné használni az Azure Stack csak, egy magánfelhő-alapú megoldás, amely telepíti a vállalati intranethez, és nem érdeklik a hibrid környezetek.

> [!TIP]
> Egyes esetekben az ilyen típusú környezetet is nevezik egy *tenger forgatókönyv*.

Egy kapcsolat nélküli üzembe helyezés nem korlátozza az Ön később kapcsolódjon az Azure Stack-példány az Azure hibrid bérlői Virtuálisgép-forgatókönyvekhez. Az azt jelenti, hogy nincs kapcsolat az Azure-ban üzembe helyezés során, vagy nem kívánja az Azure AD használatára az ügyfélidentitás-tárolóval.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Az funkciók, amelyek korlátozott vagy nem érhető el a leválasztott környezetekhez 
Az Azure Stack való csatlakozáskor az Azure-ba, ezért fontos megjegyezni, hogy egyes szolgáltatások és funkciók, amelyek korlátozott, vagy teljesen nem érhető el, a kapcsolat nélküli üzemmódban működnek a legjobban úgy lett kialakítva. 

|Funkció|Kapcsolat nélküli módban gyakorolt hatás|
|-----|-----|
|A DSC-bővítmény használatával konfigurálja a virtuális gép üzembe helyezés után a virtuális gép üzembe helyezése|Sérült - DSC bővítmény keres az internethez a WMF legújabb Verziójára.|
|Virtuális gép üzembe helyezése a Docker-parancsok futtatásához Docker-bővítmény|Sérült – Docker keresi a legújabb verziót az internethez, és ez az ellenőrzés sikertelen lesz.|
|Az Azure Stack portálon dokumentációra mutató hivatkozások|Nem érhető el - hivatkozások mint visszajelzés segítségre van szüksége, a rövid útmutatóban egy internetes URL-cím nem fognak működni használják stb.|
|Riasztási szervizelési/megoldás, amely hivatkozik egy online szervizelési útmutató|Nem érhető el – bármely riasztás szervizelése hivatkozások használó internetes URL-cím nem fog működni.|
|Marketplace – válassza ki, és adja hozzá a katalógus-csomagokat közvetlenül az Azure Marketplace-ről|Sérült – Azure Stack (nélkül bármely internetkapcsolat) kapcsolat nélküli üzemmódban történő telepítésekor Piactéri elemek nem lehet letölteni az Azure Stack-portál használatával. Használhatja azonban a [marketplace tartalomtípus-gyűjtési eszköz](azure-stack-download-azure-marketplace-item.md) töltse le a marketplace-elemek olyan gépre, amely rendelkezik internetkapcsolattal, és utána az Azure Stack környezettel.|
|Azure Active Directory összevonási fiókok használata kezelheti az Azure Stack üzemelő példányához|Nem érhető el – ez a funkció kapcsolódnia kell az Azure-bA. Ehelyett az AD FS egy helyi Active Directory-példányt kell használni.|
|App Services|Sérült - WebApps kérhet Internet-hozzáférés frissített tartalom.|
|Parancssori felület (CLI)|Sérült – parancssori felület csökkentette a funkciót, hitelesítési és az egyszerű szolgáltatások kiépítését.|
|Visual Studio – a Cloud discovery|Sérült – a Cloud Discovery vagy fogja felderíteni a különböző felhőkben, vagy egyáltalán nem működnek.|
|Visual Studio – Active Directory összevonási szolgáltatások|Sérült – csak a Visual Studio Enterprise támogatja az AD FS.
Telemetria|Nem érhető el - Telemetriai adatokat az Azure Stack, és minden külső katalóguscsomagok, amely telemetriai adatokat függ.|
|Tanúsítványok|Nem érhető el – az internetkapcsolat szükség a visszavont tanúsítványok listája (CRL), és Online tanúsítvány állapotának protokoll (OSCP) szolgáltatások HTTPS kontextusában.|
|Key-Vault|Sérült – Key vault gyakori használati eset az, hogy egy alkalmazás futásidőben titkos kódok olvasását. Ennek az alkalmazásnak kell egy egyszerű szolgáltatást a címtárban. Az Azure Active Directoryban hozzáadható a szolgáltatásnevek alapértelmezés szerint, normál felhasználók (nem rendszergazda jogosultságú). Az Active Directory (AD FS használatával) nincsenek. Ez egy küszöbértéket a helyezi a teljes körű felhasználói élményt, mert az egyik mindig haladjon végig a directory-rendszergazda bármely alkalmazás hozzáadása.| 

## <a name="learn-more"></a>Részletek
- További információ a használati esetek, vásárlás, partnerek és OEM hardverszállítók: a [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) termékoldalán.
- Az ütemterv és a rendelkezésre állás földrajzi információ az Azure Stack integrált rendszerek, tekintse meg a: [Azure Stack: Azure-bővítmény](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- A Microsoft Azure Stack csomagolás és a díjszabással kapcsolatos további [töltse le a .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>További lépések
[Adatközpont hálózati integráció](azure-stack-network.md)
