---
title: Üzembe helyezési munkalap Azure Stack hub integrált rendszerekhez
description: Megtudhatja, hogyan telepítheti és használhatja az üzembe helyezési munkalap eszközt Azure Stack hub üzembe helyezéséhez.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 04/19/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: 8c13121f5d591abca8a6c83771848d97cc9106db
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700068"
---
# <a name="deployment-worksheet-for-azure-stack-hub-integrated-systems"></a>Üzembe helyezési munkalap Azure Stack hub integrált rendszerekhez

Az Azure Stack hub üzembe helyezési munkalap egy Windows Forms alkalmazás, amely egy helyen összesíti az összes szükséges központi telepítési információt és döntést. A telepítési munkalapot a tervezési folyamat során végezheti el, és áttekintheti a telepítés megkezdése előtt.

A munkalapon megkövetelt információk a hálózatkezelési, a biztonsági és az identitási információkra terjednek ki. Fontos döntéseket igényel, amelyeknek számos különböző területen ismerete lehet. Ezért előfordulhat, hogy a munkalapra való betöltéshez érdemes megtekinteni az ezekben a területeken szaktudással rendelkező csapatokat.

A munkalap kitöltésekor előfordulhat, hogy a hálózati környezetbe be kell állítania a telepítés előtti konfigurációs módosításokat. Ebbe beletartozik az Azure Stack hub-megoldás IP-címeinek megőrzése, valamint az útválasztók, kapcsolók és tűzfalak konfigurálása az új Azure Stack hub-megoldáshoz való kapcsolódás előkészítéséhez.

> [!NOTE]
> Az üzembe helyezési munkalap eszközének végrehajtásával kapcsolatos további információkért tekintse meg [ezt a cikket a Azure stack hub dokumentációjában](azure-stack-datacenter-integration.md).

[![Üzembe helyezési munkalap](media/azure-stack-deployment-worksheet/depworksheet.png "Üzembe helyezési munkalap")](media/azure-stack-deployment-worksheet/depworksheet.png)

## <a name="installing-the-windows-powershell-module"></a>A Windows PowerShell-modul telepítése

Az üzembe helyezési munkalap minden egyes kiadásához végre kell hajtania egy PowerShell-modul egyszeri telepítését minden olyan gépen, amelyen használni szeretné a központi telepítési munkalapot.

> [!NOTE]  
> Ennek a módszernek a működéséhez a számítógépnek csatlakoznia kell az internethez.

1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort.

2. A PowerShell-ablakban telepítse a modult a [PowerShell-galériából](https://www.powershellgallery.com/packages/Azs.Deployment.Worksheet/):

   ```PowerShell
   Install-Module -Name Azs.Deployment.Worksheet -Repository PSGallery
   ```

Ha nem megbízható tárházból történő telepítésről kap üzenetet, a telepítés folytatásához nyomja le az **Y** billentyűt.

## <a name="use-the-deployment-worksheet-tool"></a>Az üzembe helyezési munkalap eszköz használata

Az üzembe helyezési munkalap elindításához és használatához olyan számítógépen, amelyen a központi telepítési munkalap PowerShell-modulját telepítette, hajtsa végre a következő lépéseket:

1. Indítsa el a Windows PowerShellt (ne használja a PowerShell ISE-t, mert nem várt eredmények merülhetnek fel). A PowerShellt rendszergazdaként nem kell futtatni.

2. Importálja a **AzS. Deployment. munkalap** PowerShell-modult:

   ```PowerShell
   Import-Module AzS.Deployment.Worksheet
   ```

3. A modul importálása után indítsa el a telepítési munkalapot:

   ```PowerShell
   Start-DeploymentWorksheet
   ```

Az üzembe helyezési munkalap külön lapokat tartalmaz a környezeti beállítások (például az **Ügyfél beállításai**, a **hálózati beállítások**és a **skálázási egység**) összegyűjtéséhez. A konfigurációs adatfájlok előállítása előtt minden lapon meg kell adnia az összes olyan értéket (kivéve a jelölést, amely nem **kötelező**). Miután az összes szükséges értéket beírta az eszközre, a **művelet** menüben **importálhatja**, **exportálhatja**és **létrehozhatja**a következőt:. A telepítéshez szükséges JSON-fájlok a következők:

**Importálás**: lehetővé teszi egy olyan Azure stack hub konfigurációs adatfájl (ConfigurationData. JSON) importálását, amelyet az eszköz hozott létre, vagy amelyeket a telepítési munkalap korábbi kiadásával hoztak létre. Az importálási művelet alaphelyzetbe állítja az űrlapokat, és törli a korábban megadott beállításokat vagy generált adatok.

**Exportálás**: érvényesíti az űrlapokra aktuálisan beírt adatokat, létrehozza az IP-alhálózatokat és a hozzárendeléseket, majd a tartalmat JSON formátumú konfigurációs fájlokként menti. Ezután ezeket a fájlokat használhatja a hálózati konfiguráció létrehozásához és Azure Stack hub telepítéséhez.

**Létrehozás**: ellenőrzi az aktuálisan megadott adatforrásokat, és a telepítési JSON-fájlok exportálása nélkül hozza létre a hálózati térképet. Két új lap jön létre, ha a **Létrehozás** sikeres: **alhálózati összefoglalás** és **IP-hozzárendelések**. A lapokon található adatok elemzésével ellenőrizheti, hogy a hálózati hozzárendelések a várt módon működnek-e.

**Összes törlése**: törli az űrlapon aktuálisan beírt összes adathalmazt, és visszaadja azokat az alapértelmezett értékekre.

**Mentse vagy nyissa meg a folyamatban lévő munkát**: a **Fájl-> Mentés** és a **Fájl-> nyitott** menük használatával mentheti és megnyithatja a részben megadott adatait. Ez eltér az **importálási** és **exportálási** függvényektől, mivel az összes adat bevitele és érvényesítése szükséges. A Megnyitás/Mentés nem ellenőrzi az érvényességet, és nincs szükség az összes mező beírására a munka folyamatban lévő mentéséhez.

**Naplózási és figyelmeztető üzenetek**: amíg az űrlap használatban van, előfordulhat, hogy a PowerShell-ablakban nem kritikus fontosságú figyelmeztető üzenetek jelennek meg. A kritikus hibák előugró üzenetként jelennek meg. A részletes naplózás, beleértve a lemezre írt naplót is, engedélyezhető, hogy segítséget nyújtson a hibaelhárítási problémák megoldásához.

Az eszköz elindítása részletes naplózással:

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

A mentett napló az aktuális felhasználó **temp** könyvtárában található; például: **c:\users\me\appdata\local\temp\ Microsoft_AzureStack \ DeploymentWorksheet_Log. txt**.

## <a name="next-steps"></a>Következő lépések

* [Azure Stack hub üzembe helyezési kapcsolatainak modelljei](azure-stack-connection-models.md)
