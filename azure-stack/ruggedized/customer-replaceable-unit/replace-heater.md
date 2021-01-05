---
title: A melegítő cseréje
description: Útmutató a melegítő cseréjéhez
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 66bebd5d8b4f88a8abc90face23d3722d45159d6
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868420"
---
# <a name="replacing-the-heater"></a>A melegítő cseréje

A régi melegítő eltávolítása előtt olvassa el a következő szakaszt, és telepítse az új melegítőt. 

## <a name="unpacking-and-taking-inventory-of-the-heater"></a>A melegítő kicsomagolása és leltárba vétele 

A taktikai 1U melegítő-v2 (a melegítő) általában a taktikai Cloud Appliance rendszer előre telepített alösszetevőjének számít. Külön szállítás esetén a melegítőt egy szabványos kereskedelmi szállítási kartondobozba szállítjuk, amely védelmi csomagolóanyagot tartalmaz. 

Óvatosan távolítsa el a melegítőt a szállítási kartondobozból. Ha egy szállítási kartondobozba csomagolták, a melegítő a váz összes összetevőjét telepíti. A melegítő általában egy árutovábbítási eset része, és az eszközök egy teljes csoportjába van integrálva egy tranzit esetben. Tartsa meg a szállítási kartondobozt és a csomagoló anyagokat, hogy a jövőben újra lehessen szállítani a melegítőt. Amíg a melegítő robusztus, gondoskodjon arról, hogy az egység megfelelően legyen csomagolva a szállításhoz. 

Győződjön meg arról, hogy a leltárban mindent figyelembe kell venni. Eltérések esetén forduljon a Microsofthoz. 

## <a name="heater-installation-planning"></a>A melegítő telepítésének megtervezése 

A telepítés megkezdése előtt győződjön meg arról, hogy ismeri a melegítő egység konfigurációs követelményeit. A telepítés megkezdése előtt: 

1. Készítse elő az átviteli esetet a melegítő telepítéséhez. 

2. Ellenőrizze, hogy rendelkezik-e minden szükséges csavart és alátéttel a melegítő telepítéséhez: 

    * Négy fekete csavar 10-32 x 7/8 négy osztott alátéttel és négy lapos alátéttel 
    
    * Négy ezüst 10-32 x 5/8 csavart négy osztott alátéttel és négy lapos alátéttel 
    
3. Ügyeljen arra, hogy a kábelek vagy a zsinórok ne legyenek a telepítés módjában. 

## <a name="heater-installation-notes"></a>A melegítő telepítési megjegyzései 

A melegítő olyan árutovábbítási esethez van csatlakoztatva, amely képes az egység megtartására. 

* Távolítsa el az árutovábbítási eset és az összes szállítási borító fedeleit, amíg a melegítő használatban van. 

* Gondoskodjon arról, hogy a fűtőtest elülső és hátsó részén legalább hat hüvelyk legyen a megfelelő légáram az egység körüli megtartásához. 

* Csatlakoztassa a melegítőt a rackbe az árutovábbítási esetben. 

* Csatlakoztassa a melegítőt a 19 hüvelykes rackbe az árutovábbítási esetben. Használjon két vagy több személyt az egység súlyozásának kezeléséhez a csatlakoztatási folyamat során. 

* Telepítse a többi szükséges összetevőt, például a csatlakozókat, a kábeleket vagy a szögletes zárójeleket. 

> [!NOTE]
> Győződjön meg arról, hogy a szoftvercsatorna-aljzat a berendezés közelében van telepítve, és könnyen elérhető.

## <a name="removing-the-heater"></a>A melegítő eltávolítása

1. Az első Pod-fedéltől kezdve a nyolc fedél zárolása balra, hogy kiszabadítsa a fedelet a hüvelyből. Tartsa a fedelet, vagy gondoskodjon arról, hogy az összes nyolc retesz kiadja. 

    ![](media/image-86.png)

1. Távolítsa el a pod Covert. 

    ![](media/image-87.png)

1. Ismételje meg az 1. és a 2. lépést a hátsó Pod-fedél eltávolításához. Most már elérheti az összetevőket a hüvelyeken belül. 

1. Teljes mértékben lazítsa a csavart, majd húzza le a szűrőt a kiszolgálóról, ahogy az a következő ábrán látható. 

    ![](media/image-88.png)

    ![](media/image-89.png)

1. A kilépési lapokat a Tactical 1U melegítő-v2-ből kell lekérnie, amíg a fedél fel nem szabadul, majd a fedél kifelé helyezi a hüvelyben lévő állványból. 

    ![](media/image-90.png)

1. Ha a csatlakozó alsó szakasza törli a kiindulási alapot, húzza ki a kifelé körülbelül 40 fokos értékre. A szög lehetővé teszi, hogy a csatlakozót a kiszolgáló vagy a kapcsolók irányába helyezze át, majd felfelé és lefelé, mielőtt kihúzza a csatlakozót a hüvelyből. 

    ![](media/image-91.png)

1. Az eset elején távolítsa el a zip-nyakkendőt a taktikai 1U heater-v2 alatt. Szükség esetén használja az átlós vágókat. 

    ![](media/image-92.png)

1. Nyomja le a Micro-Connector elülső lapját a hüvelykujj és az index ujjal, és húzza lefelé, amíg a csatlakozás nem áll le a melegítő alján. A kábel mozgatása a fűtési módszertől. 

    ![](media/image-93.png)

1. Az eset elején távolítsa el a négy 10-32 csavart, a zárolási alátéteket és a lapos alátéteket a felügyeleti kiszolgálóról vagy az alsó skálázási egység csomópont-kiszolgálóról. 

    ![](media/image-94.png)

    ![](media/image-95.png)

1. Az eset elején távolítsa el az első négy 10-32 csavart, a zárolási alátéteket és a fűtőtest lapos alátéteit.

    ![](media/image-96.png)

    ![](media/image-97.png)

1. Szüntesse meg a hátsó csatlakozótól származó Ethernet-kábelek kikapcsolását, majd távolítsa el a felügyeleti kiszolgálóhoz vagy az alsó léptékű csomópont-kiszolgálóhoz csatlakozó kábeleket. 

1. Az eset hátoldalán lazítsa meg a hat tépőzáras pántot, és húzza ki a kábeleket a tépőzárral. 

    ![](media/image-98.png)


1. Az eset hátulján a tépőzáras elem kilazítása után távolítsa el a kábeleket a kábelek tárolási zárójelei köréből. 

1. Az összes kábel kilépésének engedélyezése a csatlakozóról. A függő kábelek nem jelennek meg a következő ábrán. 

    ![](media/image-99.png)


1. Az eset hátoldalán gyorsan lehívhatja a csatlakozó ajtaját a csatlakozó ajtajának felső ajakának használatával. A csatlakozó ajtaját le kell kapcsolni a kapcsolatból, így az ajtó szabadon lefagyhat. 

1. Ha a kapcsolat továbbra is csatlakoztatva van, húzza lefelé a csatlakozót. 

    ![](media/image-100.png)

    ![](media/image-101.png)


1. Az eset hátoldalán bontsa ki a kiszolgáló tápkábeleit a Tactical 1U melegítő-v2-ből, és engedje, hogy szabadon fel lehessen akasztani őket. 

    ![](media/image-102.png)


1. Húzza ki a tápkábeleket a kiszolgálóról, és engedje, hogy szabadon fel lehessen akasztani őket. 

1. Az eset hátoldalán távolítsa el az alsó kiszolgáló alapkábelét. 

1. Helyezzen el egy elektrosztatikus mentesítést – biztonságos táskát vagy habot a melegítő nyíláson keresztül. 

1. A következő sorrendben távolítsa el a kiszolgálót a kiszolgálóról: 

    1. hexadecimális anya 
    
    1. mosó 
    
    1. csatlakoztatási terminál gyűrű 
    
    1. két alátét 

    ![](media/image-103.png)

    ![](media/image-104.png)


1. Helyezze vissza a hexadecimális anyát és a mosókat az újratelepítéshez az üzembe helyezés utáni vagy biztonságos helyre. 

1. A kis-és nagybetűket a megfelelő kitöltő zárójelet betöltő két rögzített csavart teljesen fel kell lazítani.

    ![](media/image-105.png)

    ![](media/image-106.png)

1. Húzza ki a megfelelő kitöltő zárójelet az eset középpontja felé az eltávolításhoz. 

1. A kis-és nagybetűket kibontva teljes mértékben kilazítja a bal oldali kötélpálya-zárójelet tároló két rögzített csavart. 

    ![](media/image-107.png)

    ![](media/image-108.png)


1. Húzza ki a bal oldali kötélpálya-zárójelet az eset középpontba való eltávolításához. 

1. Helyezze a szögletes zárójeleket biztonságos helyre az újratelepítéshez. 

1. Az eset hátoldalán távolítsa el az egy Cat 6 kék és 2 25 GB/s fekete Ethernet kábelt a felügyeleti vagy az alsó léptékű csomópont-kiszolgálóról. 

    ![](media/image-109.png)


1. Helyezze a kábeleket biztonságos helyre az újratelepítéshez. 

1. Az eset hátoldalán teljesen fel kell lazítani a két rögzített csavart, amely a bal oldali belső kötélpálya-zárójelet tartja. 

    ![](media/image-110.png)

    ![](media/image-111.png)


1. Húzza ki a bal oldali belső kötélpálya-zárójelet az eset középpontja felé az eltávolításhoz. 

1. Az eset hátsó részén távolítsa el a hat csuklópántos csavart a melegítő tetejéről, és az ajtót és a csuklópánt PIN-kódját egyetlen egységként hagyja el. 

    ![](media/image-112.png)

1. Helyezze a szögletes zárójelet és a csavarokat egy biztonságos helyre az újratelepítéshez. 

1. A pod hátoldalán távolítsa el a két 10-32 vasúti csavart, a zárolási alátéteket és a kiszolgáló sín védelmét biztosító lapos alátéteket a pod rackbe.

    ![](media/image-113.png)

    ![](media/image-114.png)


1. A pod hátoldalán távolítsa el a négy 10-32 csavart, a zárolási alátéteket és az olyan lapos alátéteket, amelyek védik a melegítőt a pod rackbe. 

    ![](media/image-115.png)

    ![](media/image-116.png)


1. Győződjön meg arról, hogy az összes kábel le van húzva, és a hálózatról szabadon lógott. 

1. A hüvely elején óvatosan csúsztassa ki a kiszolgálót. Tartsa a kiszolgáló középpontját még a súlyozási eloszláshoz, mert a rendszer kifelé helyezi át. 

    ![](media/image-117.png)


1. Helyezze a kiszolgálót egy elektrosztatikus kisülés – biztonságos helyre. 

1. A hüvely elülső részén húzza a melegítőt a rack aljáról. Tartsa a melegítő középpontját a súlyozási eloszláshoz, mert a rendszer kifelé helyezi át. 

    ![](media/image-118.png)


## <a name="installing-the-heater"></a>A melegítő telepítése

> [!WARNING]
> Áramütés veszélye. 


> [!NOTE]
> Ha a-kiszolgáló elöl vagy hátul található hardvert telepíti, helyezzen el egy elektrosztatikus mentesítési-biztonságos fedõlapot a melegítő szellőzők fölé, hogy megakadályozza a külső objektumok károsodását a melegítőben. 

> [!WARNING]
> A hüvely feloldásakor körültekintően járjon el a berendezések és a személyi sérülések károsodásának megelőzése érdekében. A legjobb eredmény érdekében használjon egy kétszemélyes lift-vagy teherfelvevő eszközt a személyes sérülések vagy az összetevők vagy a hüvely sérülésének csökkentéséhez. 

1. A hüvely elején csúsztassa el egy új zip-nyakkendőt, bár a taktikai 1U melegítő-v2 alsó részén lévő hurok, de ne zip a döntetlen a helyükre. 

    ![](media/image-119.png)


1. A hüvely elülső részén illessze be a Tactical 1U melegítő-v2-t úgy, hogy az elölről hátrafelé mutat. Ügyeljen arra, hogy a zip-nyakkendő ne érje el. 

1. A hüvely elején telepítse a Two. 064 hüvelykes alátéteket a rack és a Tactical 1U melegítő-v2 között. 

    ![](media/image-120.png)


1. A pod hátoldalán telepítse a Two. 064 hüvelykes alátéteket a rack és a Tactical 1U melegítő-v2 között. 

    ![](media/image-121.png)


1. A hüvely elején csatlakoztassa újra a négy 10-32 csavart, a zárolási alátéteket és a lapos alátéteket. Alkalmazza az alapozót és az alacsony szilárdságú threadlocker a csavaros szálakra, és a csavarokat 27 hüvelyk-font értékre. 

    ![](media/image-122.png)


1. A hüvely elülső részén telepítse a felügyeleti kiszolgálót vagy az alacsonyabb skálázási egység-kiszolgálót, majd a elölről hátrafelé. Győződjön meg arról, hogy a kiszolgáló nem rendelkezik laza kábelekkel. 

1. A hüvely elején csatlakoztassa újra a négy 10-32 csavart, a zárolási alátéteket és a lapos alátéteket. Alkalmazza az alapozót és az alacsony szilárdságú threadlocker a csavaros szálakra, és a csavarokat 27 hüvelyk-font értékre. 

    ![](media/image-123.png)


1. A pod hátoldalán csatlakoztassa újra a két kiszolgáló hátsó csatlakoztatási 10-32 csavart, a zárolási alátéteket és a lapos alátéteket. Alkalmazza az alapozót és az alacsony szilárdságú threadlocker a csavaros szálakra és a nyomatékot 27 hüvelykre. 

    ![](media/image-124.png)


1. A pod hátoldalán telepítse a Tactical 1U melegítő-v2 négy hátsó szerelvény 10-32 csavart, a zárolási alátéteket és a lapos alátéteket. Alkalmazza az alapozót és az alacsony szilárdságú threadlocker a csavaros szálakra, és a csavarokat 27 hüvelyk-font értékre. 

    ![](media/image-125.png)


1. A pod hátoldalán távolítsa el a két alátétet a Tactical 1U melegítő-v2 alá, és helyezze őket a melegítő készletbe későbbi használatra. 

1. A hüvely elején távolítsa el a két alátétet a Tactical 1U melegítő-v2 alá, és helyezze őket a melegítő készletbe későbbi használatra. 

1. A pod hátoldalán telepítse a hat 4-40 csavart, amely a csatlakozó zsanért csatolja a Tactical 1U melegítő-v2-hez. Alkalmazza az alapozót és az alacsony szilárdságú threadlocker a csavaros szálakra, és a csavarokat 4,5 inch-fontra. 

    > [!NOTE]
    > Helyezzen el egy elektrosztatikus mentesítési (ESD) zacskót vagy ESD-habot a Tactical 1U melegítő-v2-nyíláson, hogy megakadályozza a külső objektumok károsodását a melegítőben. 

1. A pod hátoldalán telepítse a bal oldali belső kötélpálya-konzolt. Miközben a szögletes zárójelet a rackbe bekapcsolva tartja, a fogságban lévő 6-32-es csavarok teljes mértékben megszigorítják a csavarokat, majd a csavart 9 hüvelykre 

1. Az eset hátoldalán telepítse az egy Cat 6 kék és 2 25 GB/s fekete Ethernet kábelt a felügyeleti vagy az alsó skálázási egység csomópont-kiszolgálójára. 

1. A pod hátoldalán telepítse a bal oldali kötélpálya-zárójelet az eredeti helyére. Ügyeljen arra, hogy az összes kábel visszakerüljön a pozícióba, miközben a szögletes zárójel befelé tart. 

1. Teljes mértékben szigorítsa a két rögzített 6-32 csavart, majd a csavarokat 9 hüvelykre nyomatékosan. 

1. A pod hátoldalán telepítse a megfelelő kitöltőt az eredeti helyére, miközben a szögletes zárójelbe kerül. Teljes mértékben szigorítsa a két rögzített 6-32 csavart, majd a csavarokat 9 hüvelykre nyomatékosan.

1. A pod hátoldalán telepítse a felügyeleti kiszolgálót vagy az alsóbb szintű csomópont-kiszolgáló területét a következő sorrendben: 

    1. két alátét 
    
    1. terminál gyűrűs kapcsolatai 
    
    1. mosó 
    
    1. hexadecimális anya 

1. Az anya és a 16 hüvelyk közötti nyomatékot. 

1. A pod hátoldalán kapcsolja újra a két kiszolgáló tápkábelét, majd a négy taktikai 1U melegítő-v2 tápkábelt az eredeti helyükre. 

1. A hüvely hátoldalán a kapcsolatnak fel kell vennie a labdát, és el kell végeznie a golyó lefoglalását, ha nem helyezi át a kapcsolati kart a szükséges pozícióba. 

    Tartsa stabilan a csatlakozó ajtaját, és állítsa be a kart a labda mögött. 
    
    ![](media/image-126.png)
    
    ![](media/image-127.png)
    

1. A pod hátoldalán nyomja le az A vagy A B helyet mindkét hüvelykujjon (egyszerre nyomja meg a bal és a jobb oldali, illetve az alsó és felső), amíg a rendszer hallható kattintást nem Hall a csatlakozót magával ragadó kapcsolaton. 

    ![](media/image-128.png)


1. A pod hátoldalán a hat Ethernet kábelt (két kék Cat 6 és négy fekete 25 GB/s kábelek) a kábelek tárolási sorba állítása és a kábeleken lévő hat tépőzáras szalag meghúzásával. 

1. A hüvely elején telepítse a Micro-összekötőt a kiszolgálói bemeneti hőmérséklet-érzékelőbe. Hallgassa meg a hallható kattintást. 

1. A hüvely elején irányítsa a kábelt a hurok alá, csomagolja ki a zip-kötést a kábel körül, és zip-fájlt a helyére. A felesleges zip-nyakkendő levágása. 

    ![](media/image-129.png)


1. A hüvely elülső részén telepítse az első csatlakozót úgy, hogy a csatlakozót körülbelül 40 fokos szögben (minimumon) tartja, miközben az index ujjait az első szögben elhelyezett lapos szakaszba helyezi. Útmutató a csatlakozó összehajtogatott fémből való kidobásához. 

    ![](media/image-130.png)


1. Ha megfelelően van igazítva, a kiadás lapokon helyezheti el a csatlakozót pozícióba. 

1. Nyomja meg és kapcsolja be a kiadási lapokat, hogy azok az eredeti helyükön legyenek, és zárolják a helyüket. 

1. A pod hátoldalán és előtt helyezze vissza a pod-t az eredeti helyükre, majd kapcsolja össze a zárolásokat, és állítsa be őket a hüvelybe. 

1. Tartsa meg vagy védje a fedelet úgy, hogy az összes nyolc retesz összekapcsolását. 

