---
title: A ASDK hibáinak megoldása
description: Ismerje meg, hogyan lehet elhárítani a Azure Stack Development Kitt (ASDK).
author: PatAltimore
ms.topic: article
ms.date: 01/06/2021
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 01/06/2021
ms.openlocfilehash: ab4a7166f0a6ae43259f3f1f919c3b24af017c1e
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255214"
---
# <a name="troubleshoot-the-asdk"></a>A ASDK hibáinak megoldása
Ez a cikk a Azure Stack Development Kit (ASDK) gyakori hibaelhárítási információit tartalmazza. Azure Stack integrált rendszerekkel kapcsolatos segítségért lásd: [Microsoft Azure stack hibaelhárítás](../operator/azure-stack-troubleshooting.md). 

Mivel a ASDK egy kiértékelési környezet, Microsoft ügyfélszolgálata nem nyújt támogatást. Ha olyan problémát tapasztal, amely nincs dokumentálva, segítséget kérhet a [Microsoft Q&a Azure Stackhoz tartozó](/answers/questions/topics/azure-stack-hub.html)szakértőktől.

## <a name="deployment"></a>Üzembe helyezés
### <a name="deployment-failure"></a>Üzembe helyezési hiba
Ha a telepítés során hiba lép fel, a központi telepítési parancsfájl újbóli beállításával újraindíthatja a telepítést a sikertelen lépéssel. Például:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>A telepítés végén a PowerShell-munkamenet továbbra is nyitva van, és nem jelenít meg kimenetet
Ez a viselkedés valószínűleg csak egy PowerShell-parancssorablak alapértelmezett viselkedésének eredménye, ha ki van választva. A ASDK központi telepítése sikeres volt, de a parancsfájlt az ablak kiválasztásakor szüneteltették. Ellenőrizze, hogy a telepítés befejeződött-e, ha a "kiválasztás" szót keresi a parancssori ablakban. Az ESC billentyű lenyomásával törölje a kijelölését, és a befejezési üzenetet is meg kell jeleníteni.

### <a name="template-validation-error-parameter-osprofile-is-not-allowed"></a>A sablon-érvényesítési hiba paraméterének osProfile nem engedélyezett

Ha a sablon érvényesítése során hibaüzenet jelenik meg, hogy a "osProfile" paraméter nem engedélyezett, ügyeljen arra, hogy az API-k megfelelő verzióit használja az alábbi összetevőkhöz:

- [Számítás](../user/azure-stack-profiles-azure-resource-manager-versions.md#microsoftcompute)
- [Hálózat](../user/azure-stack-profiles-azure-resource-manager-versions.md#microsoftnetwork)

A virtuális merevlemez Azure-ból Azure Stackba történő másolásához használja a [AzCopy 7.3.0](../user/azure-stack-storage-transfer.md#download-and-install-azcopy). Működjön együtt a gyártóval a rendszerképpel kapcsolatos problémák megoldásához. A Azure Stack WALinuxAgent követelményeivel kapcsolatos további információkért lásd: [Azure Linux Agent](../operator/azure-stack-linux.md#azure-linux-agent).

### <a name="deployment-fails-due-to-lack-of-external-access"></a>A telepítés sikertelen a külső hozzáférés hiánya miatt
Ha a telepítés sikertelen, és a külső hozzáférés kötelező, az alábbi példához hasonló kivételt ad vissza:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Ha ez a hiba történik, ellenőrizze, hogy teljesülnek-e az összes minimális hálózati követelmény a [központi telepítési hálózati forgalom dokumentációjának](../operator/deployment-networking.md)áttekintésével. A partneri eszközkészlet részeként egy hálózati ellenőrzési eszköz is elérhető a partnerek számára.

Az egyéb központi telepítési hibák általában az interneten található erőforrásokhoz való csatlakozással kapcsolatos problémák miatt jelentkeznek.

Az interneten található erőforrásokhoz való kapcsolódás ellenőrzéséhez hajtsa végre a következő lépéseket:

1. Nyissa meg a PowerShellt.
2. Enter-PSSession a WAS01 vagy a ERCs virtuális gépek bármelyikére.
3. Futtassa a következő parancsmagot: 
   ```powershell
   Test-NetConnection login.windows.net -port 443
   ```

Ha a parancs végrehajtása sikertelen, ellenőrizze, hogy a TOR kapcsoló és bármely más hálózati eszköz úgy van-e konfigurálva, hogy [engedélyezze a hálózati forgalmat](../operator/azure-stack-network.md).


## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
### <a name="default-image-and-gallery-item"></a>Alapértelmezett rendszerkép és gyűjtemény elem
A virtuális gépek Azure Stack-ben való üzembe helyezése előtt hozzá kell adni egy Windows Server-lemezképet és-gyűjteményi elemeket.

### <a name="after-restarting-my-azure-stack-host-some-vms-dont-automatically-start"></a>A Azure Stack gazdagép újraindítása után néhány virtuális gép nem indul el automatikusan
A gazdagép újraindítása után észreveheti, Azure Stack szolgáltatások nem azonnal érhetők el. Ennek az az oka, hogy Azure Stack infrastruktúra-alapú [virtuális gépek](asdk-architecture.md#virtual-machine-roles) és RPs a konzisztencia ellenőrzéséhez hosszabb időt vesz igénybe, de végül automatikusan elindul.

Azt is megfigyelheti, hogy a bérlői virtuális gépek nem indulnak el automatikusan a ASDK-gazdagép újraindítása után. Néhány manuális lépéssel online állapotba hozhatja őket:

1.  A ASDK-gazdagépen indítsa el **Feladatátvevőfürt-kezelő** a Start menüből.
2.  Válassza ki az **S-cluster. azurestack. local** fürtöt.
3.  Válassza a **szerepkörök** lehetőséget.
4.  A bérlői virtuális gépek *mentett* állapotban jelennek meg. Ha az összes infrastruktúra-virtuális gép fut, kattintson a jobb gombbal a bérlői virtuális gépekre, és válassza a **Start** lehetőséget a virtuális gép folytatásához.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>Töröltem néhány virtuális gépet, de továbbra is láthatók a lemezen lévő VHD-fájlok 
Ennek a viselkedésnek a kialakítása a következő:

* Ha töröl egy virtuális gépet, a VHD-k nem törlődnek. A lemezek különálló erőforrások az erőforráscsoporthoz.
* Ha a Storage-fiók törölve lesz, a törlés azonnal látható Azure Resource Manageron keresztül, de az esetlegesen tartalmazott lemezek továbbra is tárolás alatt maradnak a tárolóban, amíg a rendszer a szemetet nem fut.

Ha "árva" virtuális merevlemezeket lát, fontos tisztában lennie azzal, hogy egy törölt Storage-fiók mappájában vannak-e. Ha a Storage-fiók nem lett törölve, a virtuális merevlemezek továbbra is a megszokott módon maradnak.

További információk az adatmegőrzési küszöbérték és az igény szerinti, a [Storage-fiókok kezelése](../operator/azure-stack-manage-storage-accounts.md)című témakörben olvashatók.

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Tárhely-visszanyerés
Akár 14 órát is igénybe vehet, ha visszaigényelt kapacitást szeretne megjeleníteni a portálon. A lemezterület-visszanyerés a különböző tényezőktől függ, például a belső tároló fájljainak használati százaléka a blob-tárolóban. Ezért attól függően, hogy mennyi adattal törli a rendszer, nem garantálható, hogy a rendszer mennyi helyet szabadít fel a Garbage Collector futtatásakor.

## <a name="next-steps"></a>További lépések
[Látogasson el a Azure Stack támogatási fórumára](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)