---
title: A HCI-számlázás és-fizetés Azure Stack
description: Hogyan működik a számlázás és a fizetés Azure Stack HCI-ben.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/19/2020
ms.openlocfilehash: 0cdd8a7f6c0c59eddbe1e8dd93158eb7a280e8ab
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010719"
---
# <a name="azure-stack-hci-billing-and-payment"></a>A HCI-számlázás és-fizetés Azure Stack

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

A Azure Stack HCI egy Azure-szolgáltatás, amely a többi Azure-szolgáltatáshoz hasonlóan az Azure-előfizetésre is érvényes. Nincs szükség hagyományos helyszíni szoftverlicenc használatára, bár a vendég virtuális gépek (VM-EK) egyéni operációsrendszer-licencelést igényelhetnek. Az Azure kereskedelmi számlázási platform központilag kezeli a pénznemeket és a kedvezményeket, és az ügyfél egy egységes, részletezett számlát kap a hónap végén.

## <a name="what-does-azure-stack-hci-charge-for"></a>Mit jelent a Azure Stack HCI díja?

Azure Stack a HCI felhőalapú számlázási modellje konzisztens, ismerős és könnyen használható az Azure-t vagy bármely más felhőalapú szolgáltatást használó ügyfelek számára. A számlázás egy Azure Stack HCI-fürtön a fizikai processzor alapszintű alapdíja alapján történik (ha más Azure-szolgáltatásokat is használ, akkor további használati díjak is érvényesek).

A díjszabási modell különbözik a virtuális gépek számától függően használatos vagy fogyasztáson alapuló számlázástól. Míg a virtuális processzor magok száma (vCPU) a hónap során változhat, nem befolyásolja a Azure Stack HCI esetében fizetendő díjat: a fürtben lévő fizikai magok száma alapján továbbra is fizetnie kell.

## <a name="advantages-of-the-azure-stack-hci-billing-model"></a>Az Azure Stack HCI számlázási modell előnyei

- Egyszerű: nincs minimum, nincs maximum, és nincs olyan matematikai memória-, tárterület-vagy hálózati bejövő vagy kimenő forgalom.
- A virtualizációs infrastruktúrát hatékonyabban futtató ügyfeleknek, magasabb virtuális – fizikai sűrűséggel jutalmazza.
- Könnyen kideríthető, hogy a helyszíni Azure Stack HCI üzembe helyezése Mennyibe kerül, a költségek pedig kiszámíthatóan méretezhetők a hálózat szélétől az adatközpontig.

## <a name="how-the-number-of-processor-cores-is-assessed"></a>A processzor-magok számának értékelése

Annak megállapításához, hogy hány mag található a fürtben, Azure Stack a HCI rendszeresen ellenőrzi a fizikai magok számát, és jelentést készít az Azure-nak. Ha csak alkalmanként csatlakozik, vagy a kapcsolat megszakad, ne aggódjon; később bármikor próbálkozhat. Az alapvető adatmennyiség napok vagy hetek egyszerre tölthetők fel. Az ügyfeleknek 30 naponta legalább egyszer csatlakozniuk kell az Azure-hoz a számlázás engedélyezéséhez.

Az alapvető adatok Azure-ba történő manuális feltöltéséhez használja a **`Sync-AzureStackHCI`** parancsmagot.

## <a name="faq"></a>GYIK

- Ha már van Azure-előfizetésem, használhatom Azure Stack HCI-hez? **Igen**
- Ha a szervezetem pénzügyi részlege már jóváhagyta az Azure-t, az Azure Stack HCI-re vonatkozik? **Igen**
- Ha az Azure-ra vonatkozó kötelezettségvállalásom elkölthető, használhatom Azure Stack HCI-re? **Igen**
- Ha az Azure-kreditek (például tanulók vagy nyeremények) esetében használhatom a Azure Stack HCI-t? **Igen**
- Ha a szervezetem Nagyvállalati Szerződés kedvezményt egyeztetett, akkor a Azure Stack HCI-re vonatkozik? **Igen**
- Működnek a Azure Portal Cost Management-eszközök a Azure Stack HCI esetében? **Igen**
- Az Azure számlázási API-kkal készített harmadik féltől származó vagy egyéni eszközök működnek Azure Stack HCI esetében? **Igen**

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [A díjszabás áttekintése – az Azure díjszabásának működése](https://azure.microsoft.com/pricing/)
- [A Azure Cost Management és a számlázás áttekintése](/azure/cost-management-billing/cost-management-billing-overview)
