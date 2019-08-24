---
title: Azure Stack Windows Serverrel kapcsolatos gyakori kérdések | Microsoft Docs
description: A Windows Serverhez készült Azure Stack Marketplace – gyakori kérdések listája
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/12/2018
ms.openlocfilehash: 21364595b30c62f47c293e38bdcb9c5663c56e90
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008320"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Windows Server Azure Stack piactéren – gyakori kérdések

Ez a cikk a [Azure stack piactéren](azure-stack-marketplace.md)elérhető Windows Server-rendszerképekkel kapcsolatos gyakori kérdésekre ad választ.

## <a name="marketplace-items"></a>Marketplace-elemek

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Hogyan frissíteni egy újabb Windows-rendszerképre?

Először határozza meg, hogy a Azure Resource Manager-sablonok adott verzióra vonatkoznak-e. Ha igen, frissítse ezeket a sablonokat, vagy tartsa meg a régebbi rendszerkép-verziókat. A legjobb a következő **verzió használata: legújabb**.

Ezután, ha bármelyik virtuálisgép-méretezési csoport egy adott verzióra hivatkozik, gondolja át, hogy a méretezés később történik-e, és döntse el, hogy megtartja-e a régebbi verziókat. Ha egyik feltétel sem érvényes, törölje a régebbi lemezképeket a piactéren az újabb verziók letöltése előtt. A piactér-kezelés használatával törölje azokat, ha az eredeti Letöltés módja. Ezután töltse le az újabb verziót.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Mik a Windows Server Marketplace rendszerképeinek licencelési lehetőségei a Azure Stack-on?

A Microsoft a Windows Server rendszerképeinek két verzióját kínálja a Azure Stack piactéren. A rendszerképnek csak egy verziója használható Azure Stack környezetben.  

- **Fizetés a használat**során: Ezek a lemezképek a Windows teljes díjszabását futtatják.
   Kinek kell használnia: Nagyvállalati Szerződés (EA) ügyfelek, akik a használati *Számlázási modellt*használják; Azok a kriptográfiai szolgáltatók, akik nem szeretnék használni a SPLA-licencelést.
- **Saját licenc használata (BYOL)** : Ezek a képek alapszintű mérőórákat futtatnak.
   Kinek kell használnia: Nagyvállalati szerződéssel rendelkező ügyfelek Windows Server licenccel; A SPLA licencelést használó CSP-ket.

A Azure Hybrid Use Benefit (AHUB) nem támogatott Azure Stackon. A "Capacity" modellen keresztül licenccel rendelkező ügyfeleknek a BYOL-rendszerképet kell használniuk. Ha a Azure Stack Development Kitt (ASDK) teszteli, használhatja ezeket a lehetőségeket.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Mi a teendő, ha nem megfelelő verziót Letöltöttem a bérlők/felhasználók számára?

Először törölje a helytelen verziót a piactér kezelése szolgáltatásban. Várjon, amíg befejeződik (tekintse meg az értesítéseket, és ne a **piactér-kezelő** panelt). Ezután töltse le a megfelelő verziót.

Ha a lemezkép mindkét verzióját letölti, a piactér-katalógusban csak a legújabb verzió látható a végfelhasználók számára.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Mi a teendő, ha a felhasználó helytelenül ellenőrizte a korábbi Windows-buildek "licencem" mezőjét, és nem rendelkezik licenccel?

A következő szkript futtatásával módosíthatja a licencelési modell attribútumot, hogy a saját licencét (BYOL) az utólagos elszámolású (TB) modellre váltson:

```powershell
vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

A virtuális gép licencének típusát a következő parancsok futtatásával tekintheti meg. Ha a **Windows_Server**azt mondja, hogy a Windows-licencet a TB-modell szerint kell fizetnie:

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Mi a helyzet akkor, ha egy régebbi rendszerképem van, és a felhasználó elfelejtettem a "licenccel rendelkezem" jelölőnégyzetet, vagy saját rendszerképeket használunk, és rendelkezünk Nagyvállalati Szerződés jogosultsággal?

A következő parancsok futtatásával módosíthatja a licencelési modell attribútumot a saját licenc használatára:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Mi a helyzet a Windows Servert használó más virtuális gépekkel, például az SQL vagy a Machine Learning Server?

Ezek a lemezképek a **licenseType** paramétert használják, így a használat során kell fizetniük. Ezt a paramétert beállíthatja (lásd az előző GYIK-választ). Ez csak a Windows Server szoftverre vonatkozik, nem pedig a rétegzett termékekre, például az SQL-re, amelyekhez saját licenc szükséges. A licenccel való fizetés nem vonatkozik a rétegzett szoftvertermékek használatára.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Van egy Nagyvállalati Szerződés (EA), és a saját EA Windows Server-licencem lesz; Hogyan gondoskodom arról, hogy a lemezképek számlázása helyesen történjen?

LicenseType adhat hozzá **: Windows_Server** egy Azure Resource Manager sablonban. Ezt a beállítást minden egyes virtuálisgép-erőforrás blokkhoz hozzá kell adni.

## <a name="activation"></a>Aktiválás

A Windows Server rendszerű virtuális gépek Azure Stackon való aktiválásához a következő feltételeknek kell teljesülnie:

- Az OEM a Azure Stack összes gazdagépén beállítja a megfelelő BIOS-jelölőt.
- A Windows Server 2012 R2 és a Windows Server 2016 rendszernek a [virtuális gépek automatikus aktiválását](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))kell használnia. A kulcskezelő szolgáltatás (KMS) és más aktiválási szolgáltatások nem támogatottak Azure Stackon.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Hogyan lehet ellenőrizni, hogy a virtuális gép aktiválva van-e?

Futtassa a következő parancsot egy rendszergazda jogú parancssorból:

```shell
slmgr /dlv
```

Ha helyesen van aktiválva, a rendszer világosan jelezte, hogy az állomásnév megjelenik `slmgr` a kimenetben. Nem függnek a kijelzőn látható vízjelektől, mivel előfordulhat, hogy nem naprakészek, vagy egy másik, a tiéd mögötti virtuális gépről mutatnak.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>A virtuális gép nincs beállítva a AVMA használatára, Hogyan javíthatom?

Futtassa a következő parancsot egy rendszergazda jogú parancssorból:

```shell
slmgr /ipk <AVMA key>
```

A rendszerképhez használandó kulcsok [automatikus virtuálisgép](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) -aktiválásáról szóló cikkben talál.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Létrehozom a saját Windows Server-lemezképeket, Hogyan biztosíthatom, hogy a AVMA használják?

Javasoljuk, hogy `slmgr /ipk` a parancs `sysprep` futtatása előtt futtassa a parancssort a megfelelő kulccsal. Vagy adja meg a AVMA kulcsot az Unattend. exe telepítési fájljában.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Megpróbálom használni az Azure-ban létrehozott Windows Server 2016-es rendszerképet, és nem aktiválja vagy nem használ KMS-aktiválást.

Futtassa a következő parancsot: `slmgr /ipk`. Előfordulhat, hogy az Azure-lemezképek nem fognak megfelelően visszaesni a AVMA, de ha elérik az Azure KMS-rendszerét, aktiválva lesznek. Javasoljuk, hogy a virtuális gépek AVMA használatára legyenek beállítva.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>Elvégeztem ezeket a lépéseket, de a virtuális gépek még nem aktiválva vannak.

Lépjen kapcsolatba a hardver szállítójával, és ellenőrizze, hogy telepítve vannak-e a megfelelő BIOS-jelölők.

### <a name="what-about-earlier-versions-of-windows-server"></a>Mi a helyzet a Windows Server korábbi verzióival?

A Windows Server korábbi verzióiban a [virtuális gépek automatikus aktiválása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) nem támogatott. A virtuális gépeket manuálisan kell aktiválni.

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket:

- [A Azure Stack Marketplace áttekintése](azure-stack-marketplace.md)
- [Azure Marketplace-elemek letöltése az Azure-ból az Azure Stackbe](azure-stack-download-azure-marketplace-item.md)
