---
title: A ASDK letöltése és kibontása
description: Útmutató a Azure Stack Development Kit letöltéséhez és kibontásához (ASDK).
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/10/2018
ms.openlocfilehash: 99c43848af1d140c6698e1f4d1ae992665c0389a
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873838"
---
# <a name="download-and-extract-the-asdk"></a>A ASDK letöltése és kibontása
Miután meggyőződött arról, hogy a fejlesztői csomag gazdagépe megfelel a Azure Stack Development Kit (ASDK) telepítésének alapvető követelményeinek, a következő lépés az ASDK központi telepítési csomag letöltése és kibontása a Cloudbuilder. vhdx beszerzéséhez.

## <a name="download-the-asdk"></a>A ASDK letöltése
1. A letöltés megkezdése előtt győződjön meg arról, hogy a számítógép megfelel a következő előfeltételeknek:

   - A számítógépnek legalább 60 GB szabad lemezterülettel kell rendelkeznie az operációs rendszer lemezén kívül négy különálló és azonos logikai merevlemezen.
   - Telepíteni kell [a .NET-keretrendszer 4,6-es verzióját (vagy egy újabb verziót)](https://dotnet.microsoft.com/download/dotnet-framework-runtime/net46) .

2. [Lépjen az első lépések oldalra](https://azure.microsoft.com/overview/azure-stack/try/?v=try) , ahol letöltheti a ASDK, megadhatja az adatait, majd kattintson a **Submit (elküldés**) gombra.
3. Töltse le és futtassa a [Deployment-ellenőröket a ASDK](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) előfeltétel-ellenőrzési parancsfájlhoz. Ez az önálló parancsfájl végigvezeti a ASDK beállításával végzett előfeltételek ellenőrzéseken. Lehetővé teszi, hogy megerősítse a hardveres és a szoftverre vonatkozó követelményeket, mielőtt letölti a nagyobb csomagot a ASDK.
4. **A szoftver letöltése**területen kattintson a **Azure stack Development Kit**elemre.

   > [!NOTE]
   > A ASDK letöltése (AzureStackDevelopmentKit. exe) körülbelül 10 GB.

## <a name="extract-the-asdk"></a>A ASDK kibontása
1. A letöltés befejezése után kattintson a **Run (Futtatás** ) gombra a ASDK önkiszolgáló elindításához (AzureStackDevelopmentKit. exe).
2. Tekintse át és fogadja el a megjelenő licencszerződést az önkiszolgáló varázsló **licencszerződés** lapján, majd kattintson a **tovább**gombra.
3. Tekintse át az önkiszolgáló varázsló **Fontos figyelmeztetés** lapján megjelenő adatvédelmi nyilatkozat adatait, majd kattintson a **tovább**gombra.
4. Válassza ki az önkiszolgáló varázsló **célhely kiválasztása** lapján kinyerni kívánt Azure stack telepítési fájlok helyét, majd kattintson a **tovább**gombra. Az alapértelmezett hely a *jelenlegi mappa*\Azure stack Development Kit. 
5. Tekintse át a célhely összefoglalását az önkiszolgáló varázsló **kész kivonat** lapján, majd kattintson a **Kibontás** gombra a CloudBuilder. vhdx (körülbelül 28GB) és a ThirdPartyLicenses. rtf fájlok kibontásához. A folyamat elvégzése hosszabb időt vesz igénybe.
6. Másolja vagy helyezze át az CloudBuilder. vhdx fájlt a C:\ gyökerébe meghajtó (`C:\CloudBuilder.vhdx`) a ASDK gazdagépen.

> [!NOTE]
> A fájlok kibontása után törölheti a következőt:. EXE és. BIN-fájlok a merevlemez-terület helyreállításához. A fájlokat biztonsági mentéssel is elvégezheti, így nem kell újból letöltenie a fájlokat, ha újra kell telepítenie a ASDK.


## <a name="next-steps"></a>Következő lépések
[A ASDK-gazda számítógép előkészítése](asdk-prepare-host.md)