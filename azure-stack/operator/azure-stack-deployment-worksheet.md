---
title: Üzembehelyezési munkalap az Azure Stack integrált rendszerek |} A Microsoft Docs
description: Ismerje meg, hogyan telepítse, és a központi telepítési munkalap eszköz használatával az Azure Stack üzembe helyezése.
services: azure-stack
documentationcenter: ''
author: wamota
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2019
ms.author: wamota
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: d75915f110b6316f4621f66b1f91b010f735d165
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172669"
---
# <a name="deployment-worksheet-for-azure-stack-integrated-systems"></a>Az Azure Stack integrált rendszerek üzembehelyezési munkalap

Az Azure Stack üzembehelyezési munkalap egy Windows Forms-alkalmazást, amely összefoglalja az összes szükséges telepítési információi és egy helyen döntéseket hozhat. Végezze el a központi telepítési munkalapot a tervezési folyamat során, és az üzembe helyezés megkezdése előtt tekintse át.

A munkalap által előírt információkat a hálózatkezelés, biztonság és identitás kapcsolatos információkat tartalmaz. Szükséges ismeretek számos különböző területein; előfordulhat, hogy igénylő fontos döntések ezért érdemes tekintse meg a következő területeken szakértelmet rendelkező annak érdekében, hogy végezze el a munkalap csapatok számára.

A munkalap kitöltésekor szüksége lehet a hálózati környezet egyes központi telepítés előtti konfigurációs módosításokat hajthat végre. Ez magában foglalhatja foglalása az IP-címterek, az Azure Stack megoldás, és az útválasztók, kapcsolók és tűzfalak fel a kapcsolatot az új Azure Stack-megoldás konfigurálása.

> [!NOTE]
> Fejezze be a munkalap-telepítő eszköz kapcsolatos további információkért lásd: [Ez a cikk az Azure Stack dokumentáció](azure-stack-datacenter-integration.md).

[![Üzembehelyezési munkalap](media/azure-stack-deployment-worksheet/depworksheet.png "üzembehelyezési munkalap")](media/azure-stack-deployment-worksheet/depworksheet.png)

## <a name="installing-the-windows-powershell-module"></a>A Windows PowerShell-modul telepítése

Az üzembehelyezési munkalap egyes kiadásainak egy egyszeri minden gép, amelyen a az üzembehelyezési munkalap használni kívánt Powershell-modul telepítését kell végrehajtania.

> [!NOTE]  
> A számítógép kapcsolódnia kell az internethez a metódus.

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssorban.

2. A PowerShell-ablakban, a modul telepítéséhez az [PowerShell-galériából](https://www.powershellgallery.com/packages/Azs.Deployment.Worksheet/):

   ```PowerShell
   Install-Module -Name Azs.Deployment.Worksheet -Repository PSGallery
   ```

Ha egy nem megbízható tárházból telepítéséről szóló üzenetet kap, nyomja le az **Y** a telepítés folytatásához.

## <a name="use-the-deployment-worksheet-tool"></a>A központi telepítési munkalap eszközzel

Indítsa el, és használja az üzembehelyezési munkalap egy számítógépen, amelyen az üzembe helyezés munkalap PowerShell modult telepítette, hajtsa végre az alábbi lépéseket:

1. Indítsa el a Windows Powershellt (ne használja a PowerShell ISE-ben, a nem várt eredmények akkor fordulhat elő,). Nem kell futtatnia a Powershellt rendszergazdaként.

2. Importálás a **AzS.Deployment.Worksheet** PowerShell-modult:

   ```PowerShell
   Import-Module AzS.Deployment.Worksheet
   ```

3. A modul importálása után indítsa el a központi telepítési munkalap:

   ```PowerShell
   Start-DeploymentWorksheet
   ```

Az üzembehelyezési munkalap külön lapokra környezeti beállítások, például összegyűjtése áll **felhasználói beállításai**, **hálózati beállítások**, és **skálázási egység #**. Meg kell adnia az összes értéket (kivéve bármely megjelölt **nem kötelező**) minden lapon előtt minden konfigurációs adatfájlok generálhat. Amikor az összes szükséges értékeket tartozik az eszközt, akkor használhatja a **művelet** menü **importálás**, **exportálása**, és **Generate**. A telepítéshez szükséges JSON-fájlok a következők:

**Importálás**: Lehetővé teszi az Azure Stack konfigurációs adatfájlt (ConfigurationData.json), amely ezt az eszközt, vagy bármely korábbi verzióját az üzembehelyezési munkalap által létrehozott hozta importálását. Az importálás elvégzése alaphelyzetbe állítja a képernyők és töröl bármely korábban megadott beállítást vagy adatokat generált.

**Exportálás**: Jelenleg az űrlapokon megadott adatokat érvényesíti, állít elő, az IP-alhálózatok és -hozzárendelések, és ezután menti a tartalmat JSON formátumú konfigurációs fájlok. Ezután használhatja ezeket a fájlokat a hálózati konfiguráció létrehozása és telepítése az Azure Stack.

**Hozzon létre**: Érvényesíti a jelenleg megadott adatokat, és a központi telepítési JSON-fájlok exportálása nélkül állít elő, a hálózati térkép létrehozásához. Két új lapok jönnek létre, ha **Generate** sikeres: **Alhálózat összefoglalás** és **IP-Címek hozzárendelése**. Elemezheti az adatokat a hálózati feladatok meg a várt módon ezeken a lapokon.

**Összes törlése**: Az űrlapokon megadott jelenleg az összes adat törlése, és visszaállítja az alapértelmezett értékeket.

**A munka folyamatban megnyitni vagy menteni**: Mentheti, és nyissa meg a részben megadott adat, dolgozunk rajta, használja a **File -> Mentés** és **File -> Open** menün kellene végiglépkednie. Ez eltér a **importálás** és **exportálása** módon működik-e, ilyen esetekben a megadott és érvényesített minden adat. Nyissa meg/mentése nem ellenőrzi, és nem igényel minden mező a folyamatban lévő munka mentéséhez megadni.

**Naplózás és a figyelmeztető üzeneteket**: Miközben az űrlap használatban van, nem kritikus fontosságú, a PowerShell-ablakban megjelenő figyelmeztető üzenetek jelenhet meg. Kritikus hibák, egy előugró üzenet jelennek meg. Nem kötelező részletes naplózás, beleértve a naplót kell írni lemezre, is engedélyezhető, amelyek segítik a problémák megoldásában.

A részletes naplózást az eszköz indításához:

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

Az aktuális felhasználó találhatja meg a mentett naplóbeli **Temp** directory; például: **C:\Users\me\AppData\Local\Temp\Microsoft_AzureStack\DeploymentWorksheet_Log.txt**.

## <a name="next-steps"></a>További lépések

* [Azure Stack üzemelő példányainak kapcsolati modelljei](azure-stack-connection-models.md)
