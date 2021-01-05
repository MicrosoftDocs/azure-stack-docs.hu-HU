---
title: Azure Stack hub üzembe helyezési munkalapja
description: Megtudhatja, hogyan telepítheti és használhatja az üzembe helyezési munkalap eszközt Azure Stack hub üzembe helyezéséhez.
author: PatAltimore
ms.topic: article
ms.date: 04/19/2019
ms.author: patricka
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: afdad104cad67e928e1d6d988138ab1c8555631c
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870885"
---
# <a name="deployment-worksheet-for-azure-stack-hub-integrated-systems"></a>Üzembe helyezési munkalap Azure Stack hub integrált rendszerekhez

Az Azure Stack hub üzembe helyezési munkalap egy Windows Forms alkalmazás, amely egy helyen összesíti az összes szükséges központi telepítési információt és döntést. A telepítési munkalapot a tervezési folyamat során végezheti el, és áttekintheti a telepítés megkezdése előtt.

A munkalapon megkövetelt információk a hálózatkezelési, a biztonsági és az identitási információkra terjednek ki. Ezeknek az információknak bizonyos területeken konkrét ismeretekre lehet szükségük, ezért javasoljuk, hogy forduljon szakértőkkel a munkalap befejezéséhez.

A munkalap kitöltésekor előfordulhat, hogy a hálózati környezetbe be kell állítania a telepítés előtti konfigurációs módosításokat. Ezek a változások közé tartozhatnak az Azure Stack hub-megoldás IP-címeinek megőrzése, valamint az útválasztók, kapcsolók és tűzfalak konfigurálása az új Azure Stack hub-megoldással való kapcsolat előkészítéséhez.

> [!NOTE]
> További információ az üzembe helyezési munkalap eszközének befejezéséről: az [adatközpont-integráció tervezési szempontjai Azure stack hub integrált rendszerekhez](azure-stack-datacenter-integration.md).

[![Azure Stack hub központi telepítésének üzembe helyezési munkalapja](media/azure-stack-deployment-worksheet/depworksheet.png "Üzembe helyezési munkalap")](media/azure-stack-deployment-worksheet/depworksheet.png)

## <a name="installing-the-windows-powershell-module"></a>A Windows PowerShell-modul telepítése

Az üzembe helyezési munkalap minden egyes kiadásához egy PowerShell-modul egyszeri telepítését kell végrehajtani minden olyan gépen, amelyre a telepítési munkalapot használni kívánja.

> [!NOTE]  
> Ennek a módszernek a működéséhez a számítógépnek csatlakoznia kell az internethez.

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.

2. A PowerShell-ablakban telepítse a modult a [PowerShell-galériából](https://www.powershellgallery.com/packages/Azs.Deployment.Worksheet/):

   ```PowerShell
   Install-Module -Name Azs.Deployment.Worksheet -Repository PSGallery
   ```

Ha nem megbízható tárházból történő telepítésről kap üzenetet, a telepítés folytatásához nyomja le az **Y** billentyűt.

## <a name="use-the-deployment-worksheet-tool"></a>Az üzembe helyezési munkalap eszköz használata

A következő lépések végrehajtásával indíthatja el és használhatja a központi telepítési munkalapot azon a számítógépen, amelyen telepítette a központi telepítési munkalap PowerShell-modulját:

1. Indítsa el a Windows PowerShellt (ne használja a PowerShell ISE-t, mert nem várt eredmények merülhetnek fel). A PowerShellt rendszergazdaként nem kell futtatni.

2. Importálja a **AzS. Deployment. munkalap** PowerShell-modult:

   ```PowerShell
   Import-Module AzS.Deployment.Worksheet
   ```

3. A modul importálása után indítsa el a telepítési munkalapot:

   ```PowerShell
   Start-DeploymentWorksheet
   ```

Az üzembe helyezési munkalap külön lapokat tartalmaz a környezeti beállítások, például az **Ügyfél beállításai**, a **hálózati beállítások** és a **skálázási egység (#**) összegyűjtéséhez. A konfigurációs adatfájlok előállítása előtt minden lapon meg kell adnia az összes olyan értéket (kivéve a jelölést, amely nem **kötelező**). Miután az összes szükséges értéket beírta az eszközre, a **művelet** menüben **importálhatja**, **exportálhatja** és **létrehozhatja** a következőt:. A telepítéshez szükséges JSON-fájlok a következők:

**Importálás**: lehetővé teszi az eszköz által létrehozott Azure stack hub konfigurációs adatfájl (ConfigurationData.js) importálását, vagy a telepítési munkalap korábbi kiadásával létrehozott fájlokat. Az importálás során a rendszer visszaállítja az űrlapokat, és törli a korábban megadott beállításokat vagy a generált összes adatát.

**Exportálás**: érvényesíti az űrlapokra aktuálisan beírt adatokat, létrehozza az IP-alhálózatokat és a hozzárendeléseket, majd a tartalmat JSON formátumú konfigurációs fájlokként menti. Ezután ezeket a fájlokat használhatja a hálózati konfiguráció létrehozásához és Azure Stack hub telepítéséhez.

**Létrehozás**: ellenőrzi az aktuálisan megadott adatforrásokat, és a telepítési JSON-fájlok exportálása nélkül hozza létre a hálózati térképet. Két új lap jön létre, ha a **Létrehozás** sikeres: **alhálózati összefoglalás** és **IP-hozzárendelések**. A lapokon található adatok elemzésével ellenőrizheti, hogy a hálózati hozzárendelések a várt módon működnek-e.

**Összes törlése**: törli az űrlapon aktuálisan beírt összes adathalmazt, és visszaadja azokat az alapértelmezett értékekre.

**Mentse vagy nyissa meg a folyamatban lévő munkát**: a **fájl->mentés** és a **fájl->nyitott** menük használatával mentheti és megnyithatja a részben megadott adatait. Ez a függvény különbözik az **importálási** és **exportálási** függvényektől, mert minden adat megadása és érvényesítése szükséges. A Megnyitás/Mentés nem ellenőrzi a munkát, és nincs szükség az összes mező beírására, hogy mentse a munkáját.

**Naplózási és figyelmeztető üzenetek**: amíg az űrlap használatban van, előfordulhat, hogy a PowerShell-ablakban nem kritikus fontosságú figyelmeztető üzenetek jelennek meg. A kritikus hibák előugró üzenetként jelennek meg. A részletes naplózás, beleértve a lemezre írt naplót is, engedélyezhető, hogy segítséget nyújtson a hibaelhárítási problémák megoldásához.

Az eszköz elindítása részletes naplózással:

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

A mentett napló az aktuális felhasználó **temp** könyvtárában található; például: **C:\Users\me\AppData\Local\Temp\Microsoft_AzureStack\DeploymentWorksheet_Log.txt**.

## <a name="next-steps"></a>További lépések

* [Azure Stack hub üzembe helyezési kapcsolatainak modelljei](azure-stack-connection-models.md)
