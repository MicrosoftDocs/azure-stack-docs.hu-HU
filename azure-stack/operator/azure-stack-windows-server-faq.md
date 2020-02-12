---
title: Windows Server Azure Stack hub piactér – gyakori kérdések
titleSuffix: Azure Stack Hub
description: A Windows Serverhez készült Azure Stack hub Marketplace-re vonatkozó gyakori kérdések listája.
author: sethmanheim
ms.topic: article
ms.date: 12/27/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: 217bd904d7f0afb62f483f27ba2f800c6d93b6c7
ms.sourcegitcommit: 0a3c8b0bf9c116a5caaeca453a2bbc6e7f7cbfb9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77147787"
---
# <a name="windows-server-in-azure-stack-hub-marketplace-faq"></a>Windows Server Azure Stack hub piactér – gyakori kérdések

Ez a cikk az [Azure stack hub piactéren](azure-stack-marketplace.md)elérhető Windows Server-rendszerképekkel kapcsolatos gyakori kérdésekre ad választ.

## <a name="marketplace-items"></a>Marketplace-elemek

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Hogyan frissíteni egy újabb Windows-rendszerképre?

Először határozza meg, hogy a Azure Resource Manager-sablonok adott verzióra vonatkoznak-e. Ha igen, frissítse ezeket a sablonokat, vagy tartsa meg a régebbi rendszerkép-verziókat. A legjobb a következő **verzió használata: legújabb**.

Ezután, ha bármelyik virtuálisgép-méretezési csoport egy adott verzióra hivatkozik, gondolja át, hogy a méretezés később történik-e, és döntse el, hogy megtartja-e a régebbi verziókat. Ha egyik feltétel sem érvényes, törölje a régebbi rendszerképeket Azure Stack hub piactéren az újabb verziók letöltése előtt. A piactér-kezelés használatával törölje őket, ha az eredeti Letöltés módja. Ezután töltse le az újabb verziót.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack-hub"></a>Mik a Windows Server Marketplace rendszerképeinek licencelési lehetőségei Azure Stack hub-on?

A Microsoft a Windows Server rendszerképeinek két verzióját kínálja Azure Stack hub Marketplace-en keresztül. A rendszerképnek csak egy verziója használható Azure Stack hub-környezetben.  

- Utólagos **fizetés (TB)** : ezek a lemezképek a teljes díjszabású Windows-mérőszámokat futtatják.
   Ki használja ezt a lehetőséget: Nagyvállalati Szerződés (EA) ügyfelek, akik a használati *Számlázási modellt*használják; Azok a kriptográfiai szolgáltatók, akik nem szeretnék használni a SPLA-licencelést.
- **Saját licenc használata (BYOL)** : ezek a képek alapszintű mérőórákat futtatnak.
   Ki használja ezt a lehetőséget: nagyvállalati szerződéssel rendelkező ügyfelek Windows Server licenccel; A SPLA licencelést használó CSP-ket.

A Azure Hybrid Use Benefit (AHUB) nem támogatott Azure Stack hub-on. A "Capacity" modellen keresztül licenccel rendelkező ügyfeleknek a BYOL-rendszerképet kell használniuk. Ha a Azure Stack Development Kitt (ASDK) teszteli, használhatja ezeket a lehetőségeket.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Mi a teendő, ha nem megfelelő verziót Letöltöttem a bérlők/felhasználók számára?

Először törölje a helytelen verziót a piactér kezelése szolgáltatásban. Várjon, amíg befejeződik (tekintse meg az értesítéseket, és ne a **piactér-kezelő** panelt). Ezután töltse le a megfelelő verziót.

Ha a lemezkép mindkét verzióját letölti, akkor a Azure Stack hub piactéren csak a legújabb verzió látható a végfelhasználók számára.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Mi a teendő, ha a felhasználó helytelenül ellenőrizte a korábbi Windows-buildek "licencem" mezőjét, és nem rendelkezik licenccel?

A BYOL és a TB modell közötti váltáshoz a következő parancsfájl futtatásával módosíthatja a licencelési modell attribútumát:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

A virtuális gép licencének típusát a következő parancsok futtatásával tekintheti meg. Ha a licencelési modell **Windows_Server**, akkor a BYOL díját kell megfizetnie. Ellenkező esetben a Windows-mérőszámot a TB modell alapján számítjuk fel:

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Mi a helyzet akkor, ha egy régebbi rendszerképem van, és a felhasználó elfelejtettem a "licenccel rendelkezem" jelölőnégyzetet, vagy saját rendszerképeket használunk, és rendelkezünk Nagyvállalati Szerződés jogosultsággal?

A BYOL modellre a következő parancsok futtatásával módosíthatja a licencelési modell attribútumot:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Mi a helyzet a Windows Servert használó más virtuális gépekkel, például az SQL vagy a Machine Learning Server?

Ezek a lemezképek a **licenseType** paramétert alkalmazzák, így azok TB lesznek. Ezt a paramétert beállíthatja (lásd az előző GYIK-választ). Ez csak a Windows Server szoftverre vonatkozik, nem pedig a rétegzett termékekre, például az SQL-re, amelyekhez saját licenc szükséges. A TB licencelése nem vonatkozik a rétegzett szoftverek termékeire.

A SQL Server rendszerképek **licenseType** tulajdonsága csak akkor módosítható, ha a Azure stack hub piactéren a verzió XX. X. 20190410 vagy újabb. Ha a SQL Server rendszerképeinek egy régebbi verzióját futtatja Azure Stack hub piactéren, nem módosíthatja a **licenseType** attribútumot, és újra kell telepítenie az Azure stack hub Marketplace-ről származó legújabb SQL Server-lemezképek használatával.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Van egy Nagyvállalati Szerződés (EA), és a saját EA Windows Server-licencem lesz; Hogyan gondoskodom arról, hogy a lemezképek számlázása helyesen történjen?

**LicenseType: Windows_Server** is hozzáadhat egy Azure Resource Manager sablonban. Ezt a beállítást hozzá kell adni az egyes virtuális gépek (VM) erőforrás-blokkokhoz.

## <a name="activation"></a>Aktiválás

Ha Windows Server rendszerű virtuális gépet szeretne aktiválni Azure Stack hub-on, a következő feltételeknek kell teljesülnie:

- Az OEM a Azure Stack hub minden gazdagépén beállítja a megfelelő BIOS-jelölőt.
- A Windows Server 2012 R2 és a Windows Server 2016 rendszernek az [automatikus VM-aktiválást](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))kell használnia. A kulcskezelő szolgáltatás (KMS) és más aktiválási szolgáltatások nem támogatottak Azure Stack központban.

### <a name="how-can-i-verify-that-my-vm-is-activated"></a>Hogyan lehet ellenőrizni, hogy a virtuális gép aktiválva van-e?

Futtassa a következő parancsot egy rendszergazda jogú parancssorból:

```shell
slmgr /dlv
```

Ha helyesen van aktiválva, a rendszer világosan jelezte, hogy az állomásnév megjelenik a `slmgr` kimenetében. Nem függnek a kijelzőn látható vízjelektől, mivel előfordulhat, hogy nem naprakészek, vagy egy másik, a tiéd mögötti virtuális gépről mutatnak.

### <a name="my-vm-isnt-set-up-to-use-avma-how-can-i-fix-it"></a>A virtuális gép nincs beállítva a AVMA használatára, Hogyan javíthatom?

Futtassa a következő parancsot egy rendszergazda jogú parancssorból:

```shell
slmgr /ipk <AVMA key>
```

Tekintse meg az automatikus virtuálisgép- [aktiválást](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) ismertető cikket a rendszerképekhez használt kulcsokhoz.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Létrehozom a saját Windows Server-lemezképeket, Hogyan biztosíthatom, hogy a AVMA használják?

Javasoljuk, hogy a `sysprep` parancs futtatása előtt futtassa a `slmgr /ipk` parancssort a megfelelő kulccsal. Vagy adja meg a AVMA kulcsot az Unattend. exe telepítési fájljában.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-its-not-activating-or-using-kms-activation"></a>Megpróbálom használni az Azure-ban létrehozott Windows Server 2016-es rendszerképet, és nem aktiválja vagy nem használja a KMS-aktiválást.

Futtassa a következő parancsot: `slmgr /ipk`. Előfordulhat, hogy az Azure-lemezképek nem fognak megfelelően visszaesni a AVMA, de ha elérik az Azure KMS-rendszerét, aktiválva lesznek. Javasoljuk, hogy a virtuális gépek AVMA használatára legyenek beállítva.

### <a name="i-have-performed-all-of-these-steps-but-my-vms-are-still-not-activating"></a>Elvégeztem ezeket a lépéseket, de a virtuális gépek még nem aktiválva vannak.

Lépjen kapcsolatba a hardver szállítójával, és ellenőrizze, hogy telepítve vannak-e a megfelelő BIOS-jelölők.

### <a name="what-about-earlier-versions-of-windows-server"></a>Mi a helyzet a Windows Server korábbi verzióival?

Az [automatikus VM-aktiválás](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) nem támogatott a Windows Server korábbi verzióiban. A virtuális gépeket manuálisan kell aktiválni.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Az Azure Stack hub Marketplace áttekintése](azure-stack-marketplace.md)
- [Marketplace-elemek letöltése az Azure-ból Azure Stack hubhoz](azure-stack-download-azure-marketplace-item.md)
