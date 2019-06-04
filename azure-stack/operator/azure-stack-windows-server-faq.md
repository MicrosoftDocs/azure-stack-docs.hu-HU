---
title: Az Azure Stack a Windows Server kapcsolatos gyakori kérdések |} A Microsoft Docs
description: A Windows Server az Azure Stack-piactér – gyakori kérdések listája
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
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/12/2018
ms.openlocfilehash: ffb58392075521b81d9b0cd71928ac0db680991f
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2019
ms.locfileid: "66459069"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>A Windows Server az Azure Stack-piactér – gyakori kérdések

Ebben a cikkben megválaszolunk néhány – gyakori kérdések a Windows Server-rendszerképeket a [Azure Stack piactéren](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Marketplace-elemek

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Hogyan frissíthetek egy újabb Windows-lemezképet?

Először határozza meg, ha minden olyan Azure Resource Manager-sablonok hivatkoznak verzióját. Ha igen, frissítse ezeket a sablonokat, vagy régebbi lemezkép-verzió megtartása. Érdemes használni **verzió: legfrissebb**.

Ezután ha bármely virtuálisgép-méretezési csoportok hivatkozik egy adott verziót, gondolja hogy ezek később lesz skálázva, és döntse el, hogy a régebbi verziójú-e. Ezek a feltételek egyike sem a alkalmazni, ha újabb eszközök letöltése előtt törölje a régebbi rendszerképek a Marketplace-en. Marketplace-en felügyeleti használatával törölheti őket, ha ez hogyan lett letöltve az eredeti. Ezután töltse le az újabb verzióra.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Mik azok a Windows Server Piactérről származó rendszerképek az Azure Stacken licencelési lehetőségeinek?

A Microsoft kínál a Windows Server-rendszerképeket az Azure Stack piactéren két verziója:

- **Használja, a felhasznált**: Ezek a lemezképek futtassa a teljes díj Windows mérőszámok.
   Ki kell használnia: Nagyvállalati Szerződés (EA) használók a *Használatalapú számlázási modell*; CSP-k számára nem szeretne a SPLA-licenc.
- **Hozott licences (BYOL)** : Ezek a lemezképek alapvető mérőszámok futtassa.
   Ki kell használnia: Nagyvállalati szerződéssel rendelkező ügyfelek a Windows Server-licenc; CSP-k számára a SPLA-licenc.

Az Azure hibrid használati Benefit (AHUB) nem támogatott az Azure Stacken. Ügyfelek, akik a "Kapacitás" modellel licenc a BYOL-lemezképet kell használnia. Ha teszteli az Azure Stack Development Kit (ASDK), ezek a beállítások bármelyikét használhatja.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Mi történik, ha a helytelen verziók kínálni a bérlők és felhasználók letöltöttem?

A nem megfelelő verziójú először törölje Marketplace Management szolgáltatáson keresztül. Várjon, amíg teljesen végrehajtásához (tekintse meg az értesítéseket befejezésére, nem a **Marketplace felügyeleti** panelen). Ezután töltse le a megfelelő verziót.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Mi történik, ha a felhasználó nem megfelelően be van jelölve a "Rendelkezem egy licenc" mezőben az előző Windows épít, és nem rendelkeznek licenccel?

Lásd: [konvertálás Windows Serveres virtuális gépek vissza a használatalapú fizetés benefittel](/azure/virtual-machines/windows/hybrid-use-benefit-licensing#powershell-1).

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Mi történik, ha van egy régebbi képet, és a felhasználó elfelejtette ellenőrizze "Rendelkezem egy licenc" mezőbe, vagy hogy használhat saját lemezképeket és a nagyvállalati szerződés jogosultság van?

Lásd: [Azure Hybrid Benefit a Windows Server egy meglévő virtuális gép konvertálása](/azure/virtual-machines/windows/hybrid-use-benefit-licensing#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server). Vegye figyelembe, hogy az Azure Hybrid Benefit nem vonatkozik az Azure Stackhez, de ez a beállítás hatását a alkalmazni.

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Mi a helyzet a más virtuális gépeket, amelyek használják a Windows Server, mint az SQL vagy a Machine Learning-kiszolgáló?

Ezek a lemezképek a alkalmazni a **licenseType** paramétert, így azok használatalapú használata. Beállíthatja, hogy ezt a paramétert (az előző gyakori kérdések között talál választ). Ez csak a Windows Server szoftverre, például az SQL, szintekre osztott termékekre, amelyek megkövetelik a saját licenc használata nem érvényes. Használatalapú licencelési használata nem vonatkozik a réteges szoftvertermékeire vonatkozik.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Rendelkezem egy nagyvállalati szerződés (EA), és használja majd a nagyvállalati szerződéssel rendelkező Windows Server-licenc; Hogyan ellenőrizhetem, hogy képeket megfelelően számlázása?

Hozzáadhat **licenseType: Windows_Server** egy Azure Resource Manager-sablonban. Ez a beállítás minden virtuális gép erőforrás-címblokkra hozzá kell adni.

## <a name="activation"></a>Aktiválás

Egy Windows Server virtuális gépet az Azure Stacken aktiválásához a következő feltételeknek kell teljesülniük:

- Az OEM be van állítva a megfelelő BIOS jelölő minden gazdagép rendszeren az Azure Stackben.
- A Windows Server 2012 R2 és Windows Server 2016-ra kell használniuk [virtuális gépek automatikus aktiválása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Kulcskezelő szolgáltatás (KMS) és egyéb aktiválási szolgáltatások nem támogatottak az Azure Stacken.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Hogyan ellenőrizhetem, hogy aktiválva van-e a virtuális gépem?

Futtassa a következő parancsot egy rendszergazda jogú parancssorból:

```shell
slmgr /dlv
```

Ha megfelelően aktiválva van, ezt fogja látni a világosan jelezni, és a gazdagép neve megjelenik a `slmgr` kimeneti. Nem függ a Megjelenítés a vízjelek nem naprakész, vagy egy másik virtuális gépéről mögött Öné láthatók.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>Virtuális gép nincs beállítva az AVMA, hogyan javíthatom azt használni?

Futtassa a következő parancsot egy rendszergazda jogú parancssorból:

```shell
slmgr /ipk <AVMA key>
```

Tekintse meg a [virtuális gépek automatikus aktiválása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) a kulcsokat a rendszerkép használata című cikket.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Hozhatok létre saját Windows Server-rendszerképeket, hogyan biztosítható az AVMA használata?

Javasoljuk, hogy végrehajtása az `slmgr /ipk` parancssor futtatása előtt a megfelelő kulccsal a `sysprep` parancsot. Vagy a felvenni az AVMA kulcsot bármely Unattend.exe telepítőfájlt.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>A tapasztalataimat szeretném használni a Windows Server 2016 rendszerképet az Azure-ban létrehozott, és nem aktiválás vagy KMS-aktiválást használ.

Futtassa a következő parancsot: `slmgr /ipk`. Azure-rendszerképek előfordulhat, hogy nem megfelelően visszatér az AVMA, de elérnék az Azure KMS rendszer, ha azok aktiválásához. Ezek a virtuális gépek úgy vannak beállítva, az AVMA használata érdekében ajánlott.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>I végzett összes ezeket a lépéseket, de saját virtuális gépek továbbra is okait.

Lépjen kapcsolatba a hardver ellenőrzése, hogy megtörtént-e a megfelelő BIOS jelölők.

### <a name="what-about-earlier-versions-of-windows-server"></a>Mi a helyzet a Windows Server korábbi verzióiban?

[Virtuális gépek automatikus aktiválása](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) nem támogatott a Windows Server korábbi verzióit. A virtuális gépeket manuálisan kell aktiválni.

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket:

- [Az Azure Stack piactéren – áttekintés](azure-stack-marketplace.md)
- [Azure Marketplace-elemek letöltése az Azure-ból az Azure Stackbe](azure-stack-download-azure-marketplace-item.md)
