---
title: Kapcsolódás iSCSI-tárolóhoz Azure Stack hub használatával
description: Megtudhatja, hogyan csatlakozhat az iSCSI-tárolóhoz Azure Stack hub használatával.
author: mattbriggs
ms.topic: how-to
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 214b1d2cd06f70e9787c36c974ae4d1d18225924
ms.sourcegitcommit: 9557a5029cf329599f5b523c68e8305b876108d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88965126"
---
# <a name="connect-to-iscsi-storage-with-azure-stack-hub"></a>Kapcsolódás iSCSI-tárolóhoz Azure Stack hub használatával

A cikkben szereplő sablonnal egy Azure Stack hub virtuális gépet (VM) csatlakoztathat egy helyszíni iSCSI-tárolóhoz úgy, hogy a virtuális gépet a Azure Stack hub és az adatközpont más részein kívül üzemeltetett tároló használatára állítsa be. Ez a cikk a Windows rendszerű gépek iSCSI-célként való használatát tekinti át.

A sablont az [Azure intelligens Edge Pattern](https://github.com/lucidqdreams/azure-intelligent-edge-patterns) GitHub-tárház **lucidqdreams** villájában találja. A sablon a **Storage-iSCSI** mappában található. A sablon úgy lett kialakítva, hogy beállítsa az Azure Stack hub oldalán szükséges infrastruktúrát egy iSCSI-tárolóhoz való kapcsolódáshoz. Ebbe beletartozik egy virtuális gép, amely iSCSI-kezdeményezőként fog működni, valamint a hozzá tartozó VNet, NSG, PIP és Storage szolgáltatással. A sablon üzembe helyezését követően két PowerShell-parancsfájlt kell futtatni a konfigurálás befejezéséhez. A rendszer egy parancsfájlt futtat a helyszíni virtuális gépen (cél), és az egyiket a Azure Stack hub virtuális gépen (kezdeményezőn) futtatja. Ha ezek befejeződik, a helyszíni tárterületet a Azure Stack hub virtuális géphez is hozzáadhatja. 

## <a name="overview"></a>Áttekintés

Az ábrán egy Azure Stack hub-ban üzemeltetett virtuális gép látható egy iSCSI-csatlakoztatott lemezzel egy helyszíni Windows-gépről (fizikai vagy virtuális gépen), amely lehetővé teszi, hogy az Azure Stack hub-on kívüli tárolás az iSCSI protokollon keresztül az Azure Stack hub által üzemeltetett virtuális gépen legyen.

![Az ábrán egy külső iSCSI-csatlakoztatott lemezhez hozzáférő Azure Stack hub-ban üzemeltetett virtuális gép látható.](./media/azure-stack-network-howto-iscsi-storage/overview-iscsi2.svg)

### <a name="requirements"></a>Követelmények

- Egy helyszíni (fizikai vagy virtuális) számítógép, amelyen Windows Server 2016 Datacenter vagy Windows Server 2019 Datacenter fut.
- Szükséges Azure Stack hub Marketplace-elemek:
    -  Windows Server 2016 Datacenter vagy Windows Server 2019 Datacenter (a legújabb Build ajánlott).
    -  PowerShell DSC-bővítmény.
    -  Egyéni szkriptek bővítménye.
    -  Egy meglévő virtuális vagy fizikai gép. Ideális esetben ez a gép két hálózati adapterrel fog rendelkezni. Ez lehet egy másik iSCSI-cél is, például egy SAN-példány.

### <a name="things-to-consider"></a>Megfontolandó dolgok

- A rendszer a sablon alhálózatára alkalmazza a hálózati biztonsági csoportot. Tekintse át ezt, és szükség esetén végezze el a további kedvezményeket.
- A rendszer RDP-megtagadási szabályt alkalmaz az alagút NSG, és engedélyezni kell, hogy a virtuális gépeket a nyilvános IP-címen keresztül kívánja elérni.
- Ez a megoldás nem veszi figyelembe a DNS-feloldást.
- Meg kell változtatnia a Chapusername és a Chappassword. A Chappassword 12 – 16 karakter hosszúnak kell lennie.
- Ez a sablon statikus IP-címet használ a virtuális géphez, mivel az iSCSI-kapcsolatok a konfiguráció helyi címét használják.
- Ez a sablon BYOL Windows-licencet használ.
- A Linux-alapú rendszerek az iSCSI-tárolókra is csatlakoztathatók. Az Ubuntu dokumentációjában az [iSCSI-kezdeményező](https://help.ubuntu.com/lts/serverguide/iscsi-initiator.html) című cikkben talál útmutatást.

### <a name="options"></a>Beállítások

- A saját blob Storage-fiókját és SAS-tokenjét a **_artifactsLocation** és a **_artifactsLocationSasToken** paraméterek használatával saját tároló-blobot használhat sas-jogkivonattal.
- Ez a sablon a VNet elnevezési és IP-címzési alapértelmezett értékeit tartalmazza.
- Ennek a konfigurációnak csak egy iSCSI-hálózati adaptere van, amely az iSCSI-ügyfélről származik. Számos konfigurációt Teszteltünk a különálló alhálózatok és hálózati adapterek kihasználása érdekében, azonban több átjáróval kapcsolatos problémákba ütközött, és egy különálló tárolási alhálózatot próbáltak létrehozni a forgalom elkülönítéséhez, és ténylegesen valóban redundánsnak kell lennie. 
- Ügyeljen arra, hogy ezeket az értékeket a jogi alhálózaton és a címtartományból is megőrizze, mivel a telepítés sikertelen lehet. 
- A PowerShell DSC-csomagok elsődleges célja a függőben lévő újraindítások keresése. Ezt a DSC-t szükség esetén tovább lehet testreszabni. További információ: [omputerManagementDsc](https://github.com/PowerShell/ComputerManagementDsc/).

### <a name="resource-group-template-iscsi-client"></a>Erőforráscsoport-sablon (iSCSI-ügyfél)

A diagramon a sablon alapján központilag telepített erőforrások láthatók az iSCSI-tárolóhoz való kapcsolódáshoz használható iSCSI-ügyfél létrehozásához. Ez a sablon telepíti a virtuális gépet és más erőforrásokat is, és futtatja a prepare-iSCSIClient.ps1, majd újraindítja a virtuális gépet.

![Az ábrán a sablon alapján telepített erőforrások láthatók, amelyekkel az iSCSI-ügyfél létrehozható az iSCSI-tárolóhoz való kapcsolódáshoz. Egy belső alhálózattal és hálózati ADAPTERrel (hálózati kártyával), belső PIP (Private Internet Protocol) és NSG (hálózati biztonsági csoport) rendelkező fájlkiszolgálón jelenik meg.](./media/azure-stack-network-howto-iscsi-storage/iscsi-file-server.svg)

### <a name="the-deployment-process"></a>Az üzembe helyezési folyamat

Az erőforráscsoport-sablon kimenetet hoz létre, amely a következő lépés bemenetének a célja. Elsősorban a kiszolgáló nevére és a Azure Stack hub nyilvános IP-címére összpontosít, ahol az iSCSI-forgalom származik. Ehhez a példához:

1. Telepítse az infrastruktúra-sablont.
2. Helyezzen üzembe egy Azure Stack hub virtuális gépet egy, az adatközpontban máshol üzemeltetett virtuális gépre. 
3. Futtassa az `Create-iSCSITarget.ps1` IP-cím és a kiszolgálónév kimenetét a sablonból az iSCSI-tárolóban lévő parancsfájl kimeneti paramétereként, amely lehet virtuális gép vagy fizikai kiszolgáló.
4. Az iSCSI-célkiszolgáló külső IP-címét vagy címét használja bemenetként a parancsfájl futtatásához `Connect-toiSCSITarget.ps1` . 

![Az ábrán a fenti négy lépés első három látható, valamint bemenetek és kimenetek is szerepelnek. A lépések a következők: infrastruktúra üzembe helyezése, iSCSI-tároló létrehozása és az iSCSI-hez való kapcsolódás.](./media/azure-stack-network-howto-iscsi-storage/process.svg)

### <a name="inputs-for-azuredeployjson"></a>Bemenetek azuredeploy.json

|**Paraméterek**|**alapértelmezett**|**Leírás**|
|------------------|---------------|------------------------------|
|WindowsImageSKU         |2019 – Datacenter   |Válassza ki az alapszintű Windowsos virtuális gép rendszerképét
|VMSize                  |Standard_D2_v2    |Adja meg a virtuális gép méretét
|VMName                  |FileServer        |a virtuális gép neve
|adminUsername           |storageadmin      |Az új virtuális gép rendszergazdájának neve
|adminPassword           |                  |Az új virtuális gépek rendszergazdai fiókjának jelszava. Az alapértelmezett érték az előfizetés azonosítója
|VNetName                |Storage           |A VNet neve. Ezt fogja használni az erőforrások címkézéséhez
|VNetAddressSpace        |10.10.0.0/23      |Címterület a VNet
|VNetInternalSubnetName  |Belső          |VNet belső alhálózat neve
|VNetInternalSubnetRange |10.10.1.0/24      |Címtartomány a belső alhálózat VNet
|InternalVNetIP          |10.10.1.4         |Statikus cím a fájlkiszolgáló belső IP-címéhez.
|_artifactsLocation      ||
|_artifactsLocationSasToken||

### <a name="deployment-steps"></a>A központi telepítés lépései

1. ISCSI-ügyfél infrastruktúra üzembe helyezése a használatával `azuredeploy.json`
2. Futtassa a parancsot a helyszíni `Create-iSCSITarget.ps1` kiszolgáló iSCSI-tárolóján. A sablon befejezése után futtatnia kell a Create-iSCSITarget.ps1 a helyszíni kiszolgáló iSCSI-tárolóján az első lépés kimenetével.
3. Futtassa a parancsot `Connect-toiSCSITarget.ps1` az iSCSI-ügyfélen. az iSCSI-ügyfél Connect-toiSCSITarget.ps1 az iSCSI-tároló részleteivel

## <a name="adding-iscsi-storage-to-existing-vms"></a>ISCSI-tároló hozzáadása meglévő virtuális gépekhez

A meglévő virtuális gépek parancsfájljait is futtathatja, hogy az iSCSI-ügyfélről iSCSI-tárolóhoz kapcsolódjon. Ez a folyamat akkor áll fenn, ha saját maga hozza létre az iSCSI-tárolót. Ez az ábra a PowerShell-parancsfájlok végrehajtási folyamatát mutatja be. Ezek a szkriptek a parancsfájl könyvtárában találhatók:

![Az ábrán az alábbiakban tárgyalt három szkript látható. A végrehajtás sorrendjében ezek a következők: Prepare-iSCSIClient.ps1, (végrehajtja az ügyfelet), létrehozza iSCSITarget.ps1 (a célokon végrehajtja a célokat), és Connect-toiSCSITarget.ps1 (exectutes az ügyfélen).](./media/azure-stack-network-howto-iscsi-storage/script-flow.svg)

### <a name="prepare-iscsiclientps1"></a>Prepare-iSCSIClient.ps1

A `Prepare-iSCSIClient.ps1` parancsfájl az iSCSI-ügyfélre telepíti az előfeltételeket, beleértve a következőket:
- Többutas-i/o-szolgáltatások telepítése
- az iSCSI-kezdeményező szolgáltatás automatikus indításának beállítása
- többutas MPIO támogatásának engedélyezése iSCSI-hez
- Az összes iSCSI-kötet automatikus igénylésének engedélyezése
- A lemez időtúllépésének beállítása 60 másodpercre

Az Előfeltételek telepítése után fontos a rendszer újraindítása. Az MPIO-terheléselosztási házirend újraindítást igényel, hogy be lehessen állítani.

### <a name="create-iscsitargetps1"></a>Create-iSCSITarget.ps1

A `Create-iSCSITarget.ps1` parancsfájlt a Storage-kiszolgálón kell futtatni. A kezdeményezők által korlátozottan több lemezt és célt is létrehozhat. Ezt a parancsfájlt többször is futtathatja, hogy több virtuális lemezt hozzon létre, amelyek különböző célokhoz kapcsolhatók. Több lemezt is összekapcsolhat egy célhoz. 

|**Bevitel**|**alapértelmezett**|**Leírás**|
|------------------|---------------|------------------------------|
|RemoteServer         |FileServer               |Az iSCSI-tárolóhoz csatlakozó kiszolgáló neve
|RemoteServerIPs      |1.1.1.1                  |Az az IP-cím, amelyet az iSCSI-forgalom a következőtől fog származni:
|DiskFolder           |C:\iSCSIVirtualDisks     |A mappát és a meghajtót, ahová a rendszer a virtuális lemezeket tárolni fogja
|DiskName             |DiskName                 |A lemez VHDX-fájljának neve
|DiskSize             |5 GB                      |A VHDX lemez mérete
|TargetName           |RemoteTarget01           |Az iSCSI-ügyfél cél konfigurációjának definiálásához használt cél neve. 
|ChapUsername         |username                 |A CHAP-hitelesítéshez használt Felhasználónév neve
|ChapPassword         |userP@ssw0rd!            |A CHAP-hitelesítéshez használt jelszó neve. 12 és 16 karakter közöttinek kell lennie

### <a name="connect-toiscsitargetps1"></a>Connect-toiSCSITarget.ps1

A a `Connect-toiSCSITarget.ps1` végső parancsfájl, amely az iSCSI-ügyfélen fut, és az iSCSI-tároló által az iSCSI-ügyfél számára bemutatott lemezt csatlakoztatja.

|**Bevitel**|**alapértelmezett**|**Leírás**|
|------------------|---------------|------------------------------|
|TargetiSCSIAddresses   |"2.2.2.2", "2.2.2.3"    |Az iSCSI-tároló IP-címei
|LocalIPAddresses       |"10.10.1.4"            |Ez az a belső IP-cím, amelyet az iSCSI-forgalom a következő helyről fog származni:
|LoadBalancePolicy      |C:\iSCSIVirtualDisks   |Az az IP-cím, amelyet az iSCSI-forgalom a következőtől fog származni:
|ChapUsername           |username               |A CHAP-hitelesítéshez használt Felhasználónév neve
|ChapPassword           |userP@ssw0rd!          |A CHAP-hitelesítéshez használt jelszó neve. 12 és 16 karakter közöttinek kell lennie

## <a name="next-steps"></a>Következő lépések

[A Azure Stack hub hálózatkezelésével kapcsolatos különbségek és megfontolások](azure-stack-network-differences.md)  