---
title: Az intelligens peremhálózaton egyaránt az Azure Stack használatával több felhőre kiterjedő méretezési mintája |} A Microsoft Docs
description: További tudnivalók az intelligens peremhálózaton egyaránt az Azure Stack a több felhőre kiterjedő méretezési mintája
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 2b97be7ecfffe83e560d32fbaac6480c8c17bb88
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856375"
---
# <a name="cross-cloud-scaling-pattern"></a>A felhőbe irányuló skálázási minta

Erőforrások automatikus hozzáadása egy meglévő alkalmazást a terhelés növekedését befogadásához.

## <a name="context-and-problem"></a>Kontextus és probléma

Az alkalmazás nem tudja növelni a kapacitásuk váratlan az igény nő. Ennek eredményeképpen a felhasználók nem éri el az alkalmazás használati csúcsidőben. Az alkalmazás felhasználók rögzített számú tud kiszolgálni.

Globális vállalatok biztonságos, megbízható és elérhető felhőalapú alkalmazások van szükség. Értekezlet az igény nő, és fontos infrastruktúrájukkal, hogy a megfelelő infrastruktúra használatával. Vállalkozások kihívást jelent a vállalati adatok biztonságát, a storage és a valós idejű rendelkezésre állási és a karbantartási költségek kiegyensúlyozása.

Nem lehet tudni futtatni az alkalmazást a nyilvános felhőben. Azonban nem lehet az alkalmazás iránti kereslet kiugrások kezelésére a helyszíni környezetben szükséges kapacitást fenntartásához a vállalati gazdaságosan megvalósítható. Ezzel a mintával használhatja a nyilvános felhő rugalmasságát a saját helyszíni megoldás.

## <a name="solution"></a>Megoldás

A több felhőre kiterjedő horizontális minta kibővíti a nyilvános felhőbeli erőforrásokat egy helyi felhőben található alkalmazás. A minta növelését által aktivált vagy csökkentse az igény, és rendre hozzáadása vagy eltávolítása erőforrások a felhőben. Ezeket az erőforrásokat biztosítanak redundanciát, a gyors rendelkezésre állás és a geo-kompatibilis útválasztó.

![A felhőbe irányuló skálázási minta](media/azure-stack-edge-pattern-cross-cloud-scaling/cross-cloud-scaling.png)

> Megjegyzés:  
> Ez a minta csak állapotmentes alkalmazásokra vonatkozik.

A több felhőre kiterjedő skálázási minta az alábbi összetevőkből áll.

**Traffic Manager**  
** A fenti ábrán szereplő Ez a nyilvános felhőben csoport kívül, de azt tudja a forgalmat a helyi adatközpontban és a nyilvános felhőben is működnie kell. A terheléselosztó biztosít magas rendelkezésre állású alkalmazás végpontok monitorozására, és szükség esetén feladatátvételi újraelosztás megadásával.

**Tartománynévrendszer (DNS)**  
A Domain Name System, vagy a DNS-beli felelős fordítása (vagy feloldása) az IP-címét a webhely vagy szolgáltatás nevét.

### <a name="cloud"></a>Felhő

**Üzemeltetett Buildelési kiszolgáló**  
A létrehozási folyamat üzemeltetési környezetet.

**Alkalmazás-erőforrások**  
Az alkalmazás-erőforrásokat kell tudni a méretezési csoport és bővítés, például a virtuális gép ScaleSets és a tárolók.

**Egyéni tartománynév**  
Az útválasztási kérelmek helyettesítése egyéni tartománynevet használja.

**Nyilvános IP-címek**  
A bejövő forgalom a traffic manager átirányítása a nyilvános felhőben alkalmazásvégpont erőforrások nyilvános IP-címek használhatók.  

### <a name="local-cloud"></a>Helyi felhő

**Üzemeltetett Buildelési kiszolgáló**  
A létrehozási folyamat üzemeltetési környezetet.

**Alkalmazás-erőforrások**  
Az alkalmazás-erőforrásokat kell tudni a méretezési csoport és bővítés, például a virtuális gép ScaleSets és a tárolók.

**Egyéni tartománynév**  
Az útválasztási kérelmek helyettesítése egyéni tartománynevet használja.

**Nyilvános IP-címek**  
A bejövő forgalom a traffic manager átirányítása a nyilvános felhőben alkalmazásvégpont erőforrások nyilvános IP-címek használhatók. 

## <a name="issues-and-considerations"></a>Problémák és megfontolandó szempontok


A minta megvalósítása során az alábbi pontokat vegye figyelembe:

### <a name="scalability-considerations"></a>Méretezési szempontok

A több felhőre kiterjedő skálázás fő összetevője rendszer azon képessége, hogy az igény szerinti méretezés a nyilvános és a helyi infrastruktúrát, az igény szerinti által előírt, amelyek bizonyítják következetes és megbízható szolgáltatás között.

### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Győződjön meg arról helyileg telepített alkalmazások magas rendelkezésre állású keresztül a helyszíni hardver konfiguráció és a szoftverek központi telepítési vannak konfigurálva.

### <a name="manageability-considerations"></a>Felügyeleti szempontok

A több felhőre kiterjedő minta biztosítja a zökkenőmentes kezelés és ismerős felületet környezetek között.

#### <a name="when-to-use-this-pattern"></a>Mikor érdemes ezt a mintát használni?

Használja a következő mintát a következő helyzetekben:

-   Ha szeretne növelheti az alkalmazás kapacitásának váratlan a növekvő igények szerint vagy igény szerint a rendszeres igényeinek.

-   Ha nem szeretné, hogy be, amely során a maximális szám csak használható erőforrásokat. Csak a valóban használt funkciókért kell fizetni.

Ez a minta nem ajánlott, ha:

-   A megoldáshoz szükséges az interneten keresztül csatlakozó felhasználók.

-   Az üzleti rendelkezik helyi szabályzat, amely megköveteli, hogy az eredeti kapcsolatot egy helyszíni hívás származnak.

-   A hálózat rendszeres szűk keresztmetszeteket, amely korlátozná a teljesítmény, a méretezés során lép.

-   A környezet nem kapcsolódik az internethez, és nem tudja elérni a nyilvános felhőben.

## <a name="example"></a>Példa

Ismerje meg, hogyan hozhat létre a több felhőre kiterjedő megoldásokat biztosít egy manuálisan aktivált folyamatot az Azure Stackkel való váltás futtatott webalkalmazás, egy Azure-ban üzemeltetett web Apps és az automatikus skálázást annak biztosítása, rugalmas és skálázható felhőalapú segédprogram terhelés alatt, a traffic manager-n keresztül.

[A felhőbe irányuló méretezési megoldások létrehozása az Azure-ral](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-cloud-burst)

## <a name="next-steps"></a>További lépések

Ismerje meg [hibrid felhő tervezési minták az Azure Stackhez](azure-stack-edge-pattern-overview.md)
