---
title: Töltse le és csomagolja ki az Azure Stack Development Kit (ASDK) |} A Microsoft Docs
description: Ismerteti, hogyan tölthet le és csomagolja ki az Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 08/10/2018
ms.openlocfilehash: 5fdd9fd23c2740171f59e9d6b20c775e59e14733
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984467"
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Töltse le és csomagolja ki az Azure Stack Development Kit (ASDK)
Miután meggyőződött arról, hogy a fejlesztői csomag fogadó számítógép megfelel-e a ASDK telepítésére vonatkozó minimális követelményeinek, a következő lépés az töltse le és csomagolja ki a ASDK központi telepítési csomag beolvasni a Cloudbuilder.vhdx.

## <a name="download-the-asdk"></a>Töltse le a ASDK
1. A letöltés a Kezdés előtt győződjön meg arról, hogy a számítógép megfelel-e a következő előfeltételek vonatkoznak:

   - A számítógépnek rendelkeznie kell legalább 60 GB lemezterület elérhető négy különálló, azonos logikai merevlemezek emellett ingyenesen az operációsrendszer-lemez.
   - [.NET-keretrendszer 4.6 (vagy újabb verzió)](https://dotnet.microsoft.com/download/dotnet-framework-runtime/net46) kell telepíteni.

2. [Nyissa meg az első lépések lap](https://azure.microsoft.com/overview/azure-stack/try/?v=try) ahol az Azure Stack Development Kit letöltése, adja meg az adatait és kattintson a **küldés**.
3. Töltse le és futtassa a [Deployment Checker for Azure Stack Development Kit](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) előfeltétel-ellenőrző parancsfájlt. Ez a szkript önálló halad át az előfeltételeket ellenőrzi az Azure Stack Development Kit telepítője által végzett. Lehetővé teszi annak ellenőrzéséhez, hogy a hardver- és szoftverkövetelmények, a nagyobb csomag letöltése az Azure Stack Development Kit előtt találkoznak.
4. A **a szoftverfrissítések letöltéséhez**, kattintson a **Azure Stack Development Kit**.

   > [!NOTE]
   > A ASDK letöltése (AzureStackDevelopmentKit.exe) körülbelül 10 GB-os.

## <a name="extract-the-asdk"></a>Bontsa ki a ASDK
1. A letöltés befejezése után kattintson a **futtatása** a ASDK mappába (AzureStackDevelopmentKit.exe) elindításához.
2. Tekintse át és fogadja el a megjelenített licencszerződése a **licencszerződés** az mappába varázslót, majd kattintson az oldal **tovább**.
3. Tekintse át az adatvédelmi nyilatkozat adatokat jelenik meg a **fontos figyelmeztetés** az mappába varázslót, majd kattintson az oldal **tovább**.
4. Válassza ki a helyet, az Azure Stack telepítőfájlokat a kinyerni a **célhely megadása** az mappába varázslót, majd kattintson az oldal **tovább**. Az alapértelmezett hely a *aktuális mappa*\Azure Stack Development Kit. 
5. Tekintse át az összefoglalót célhelye a **készen áll a kinyerési** mappába varázslót, és kattintson az oldal **kinyerése** kibontani a CloudBuilder.vhdx (körülbelül 28 GB) és a ThirdPartyLicenses.rtf fájlokat. Ez a folyamat befejezése hosszabb időt vesz igénybe.
6. Másolja, vagy helyezze át a CloudBuilder.vhdx fájlt a C:\ meghajtó (C:\CloudBuilder.vhdx) gyökérkönyvtárában ASDK a gazdagépen.

> [!NOTE]
> Miután a fájlok kibontásához, törölheti a. Exe-fájl és. DOBOZ fájlok helyreállítása a merevlemez-területet. Vagy, így nem kell újra letölteni a fájlokat, ha újbóli üzembe helyezése a ASDK szeretne biztonsági másolatot készíteni ezeket a fájlokat.


## <a name="next-steps"></a>További lépések
[A ASDK számítógép előkészítése](asdk-prepare-host.md)