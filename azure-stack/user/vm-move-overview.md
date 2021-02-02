---
title: Virtuális gép áthelyezése Azure Stack hubhoz
description: Ismerje meg a virtuális gépek Azure Stack hubhoz való áthelyezésének különböző módszereit.
author: mattbriggs
ms.topic: conceptual
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: cfcb5b494374fedaa5bdced131a8a42203aa5eb0
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246571"
---
# <a name="move-a-vm-to-azure-stack-hub-overview"></a>Virtuális gép áthelyezése Azure Stack hub-ra – áttekintés

A virtuális gépeket (VM) a környezetből Azure Stack hubhoz helyezheti át. A számítási feladatok áthelyezésének megtervezése során bizonyos korlátozásokat kell megvárnia. Ez a cikk az Azure Stack hub virtuális merevlemezének (VHD-k) követelményeit sorolja fel. Az Azure Stack hub egy 1. generációs VHD-t igényel. A virtuális gépnek általánosított vagy specializált értékűnek kell lennie. A Azure Stack-ben létrehozott virtuális gépekhez használja az általánosított virtuális gépeket Base-mágusként. A speciális virtuális gép felhasználói fiókokat tartalmaz. A virtuális merevlemez átállításához, előkészítéséhez és letöltéséhez ellenőrizze, hogy a VHD megfelel-e a követelményeknek, töltse fel a rendszerképet egy Azure Stack hub Storage-fiókjába, majd hozza létre a virtuális gépet a felhőben. Ha összetettebb áttelepítési feladattal rendelkezik, a *migrálás Azure stack hub* -tanulmányba című témakörben talál teljes vitát.

Az egyéni lemezképek két formában jelennek meg: **általánosított** és **specializált**.

- **Általánosított rendszerkép**  
  Egy általánosított lemezkép az egyik, amely készen áll a **Sysprep** használatával az egyedi információk (például a felhasználói fiókok) eltávolítására, amely lehetővé teszi, hogy újból felhasználja a több virtuális gép létrehozásához. Az általánosított VHD-k jó illeszkedést biztosítanak az olyan rendszerképek létrehozásához, amelyeket az Azure Stack hub Cloud Operator a Piactéri elemekként való használatra tervez. A felügyeleti portálon vagy a rendszergazdai végpontokon keresztül kínált rendszerképek **platform-lemezképek**.

- **Speciális rendszerkép**  
  A speciális lemezképek a virtuális merevlemezek (VHD-k) másolatai egy meglévő virtuális gépről, amely tartalmazza az eredeti virtuális gépről származó felhasználói fiókokat, alkalmazásokat és egyéb állapotinformációkat. Ez általában a virtuális gépek áttelepítésének formátuma Azure Stack hubhoz. A speciális VHD-k jó illeszkedést biztosítanak, ha a helyszíni rendszerből Azure Stack hubhoz kell áttelepítenie a virtuális gépeket.

Ha egy rendszerképet Azure Stack hubhoz helyez át, gondolja át, hogyan szeretné használni a rendszerképet.

- **Személyes munkaterhelés**  
    Előfordulhat, hogy a helyszíni környezetben vagy a globális Azure-ban rendelkezik egy olyan géppel, amelyet fejlesztési vagy konkrét feladatokhoz használ, és szeretné kihasználni a Azure Stack hub-beli privát felhőben való üzemeltetését. Meg szeretné őrizni az adat-és felhasználói fiókokat a gépen. Ezt a gépet speciális képként szeretné áthelyezni a Azure Stack hubhoz.

- **Arany-rendszerkép**  
    Érdemes lehet közös virtuálisgép-konfigurációt és alkalmazások készletét megosztani a munkacsoporton belül. Nem kell megosztania a rendszerképet a Azure Stack hub-tartományon (a címtár-bérlőn) kívüli felhasználókkal. Ebben az esetben a rendszerképet az adatok és a felhasználói fiókok eltávolításával szeretné általánosítani. Ezt a rendszerképet a bérlő más felhasználóival is megoszthatja.

- **Azure Stack hub Marketplace-ajánlat**  
    A Felhőbeli operátor általánosított képet használhat a Piactéri ajánlatok alapján. Miután előkészítette a rendszerképet, a Felhőbeli operátor az egyéni rendszerkép használatával létrehozhatja az Azure Stack hub-példány Marketplace-ajánlatát. A felhasználók saját virtuális gépet hozhatnak létre a rendszerképből, mivel a piactéren bármilyen más ajánlat lenne. Az ajánlat létrehozásához a felhőalapú operátorral kell dolgoznia.

## <a name="verify-vhd-requirements"></a>VHD-követelmények ellenőrzése

> [!IMPORTANT]  
> A virtuális merevlemez előkészítésekor a következő követelményeknek kell megfelelnie, vagy nem fogja tudni használni a VHD-t Azure Stack központban.
> A rendszerkép feltöltése előtt vegye figyelembe a következőket:
> - Azure Stack hub csak az 1. generációs virtuális gépek lemezképeit támogatja.
> - A VHD rögzített típusú. Azure Stack hub nem támogatja a dinamikus lemezes virtuális merevlemezeket.
> - A VHD minimális virtuális mérete legalább 20 MB.
> - A VHD igazított, azaz a virtuális méretnek 1 MB-nál többnek kell lennie.
> - VHD blob hossza = virtuális méret + VHD-lábléc hossza (512). A blob végén található kisméretű lábléc a virtuális merevlemez tulajdonságait írja le. 

A virtuális merevlemez kijavításához szükséges lépések a [VHD ellenőrzésekor](vm-move-from-azure.md#verify-your-vhd) találhatók

## <a name="methods-of-moving-a-vm"></a>A virtuális gép áthelyezésének módszerei

A virtuális gépet manuálisan is áthelyezheti Azure Stack hubhoz a következő esetekben:

| Eset | Utasítások |
| --- | --- |
| Globális Azure – Azure Stack hub | Készítse elő a VHD-t a globális Azure-ban, majd töltse fel Azure Stack hubhoz. További információ: [virtuális gép áthelyezése az Azure-ból Azure stack hub](vm-move-from-azure.md)-ra. |
| Helyi általános – Azure Stack hub | Készítse elő a VHD-t, és általánosítsa a VHD-t helyileg a Hyper-V-ben, majd töltse fel Azure Stack hubhoz. További információ: [általánosított virtuális gép áthelyezése a helyszínről Azure stack hubhoz](vm-move-generalized.md). |
| Helyi Azure Stack hub | Készítse elő a speciális virtuális merevlemezt helyileg a Hyper-V-ben, majd töltse fel Azure Stack hubhoz. További információ: [speciális virtuális gép áthelyezése a helyszínről Azure stack hubhoz](vm-move-specialized.md). |

## <a name="migrate-to-azure-stack-hub"></a>Áttelepítés Azure Stack hubhoz

Az Azure Global AzureCAT szakértői által írt útmutatóban részletes információkat, feladatlistákat és ajánlott eljárásokat talál a munkaAzure Stack terhelések megtervezéséhez és áttelepítéséhez. Az útmutató a fizikai kiszolgálókon vagy a meglévő virtualizációs platformokon futó meglévő alkalmazások áttelepítésére koncentrál. A számítási feladatok Azure Stack Hub IaaS-környezetbe való áthelyezésével a csapatok használhatják a zökkenőmentes műveletek, az önkiszolgáló üzembe helyezés, a szabványosított hardverkonfigurációk és az Azure konzisztenciája nyújtotta előnyöket.

[A tanulmány beolvasása.](https://azure.microsoft.com/resources/migrate-to-azure-stack-hub-patterns-and-practices-checklists/)

A felhőre vonatkozó bevezetési keretrendszerben is találhat útmutatást a Migrálás szolgáltatásról. További információ: [a Azure stack hub áttelepítésének megtervezése](/azure/cloud-adoption-framework/scenarios/azure-stack/plan). 

## <a name="next-steps"></a>Következő lépések

[Azure Stack hub virtuális gépek bemutatása](azure-stack-compute-overview.md)

[Egyéni virtuálisgép-rendszerkép hozzáadása Azure Stack hubhoz](../operator/azure-stack-add-vm-image.md)