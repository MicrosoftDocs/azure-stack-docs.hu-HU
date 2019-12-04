---
title: Azure Stack biztonsági vezérlők
titleSuffix: Azure Stack
description: További információ a Azure Stack alkalmazott biztonsági testhelyzetekről és ellenőrzésekről.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 4050a33be2436b919ffe4b4668b38a595523bd0d
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780796"
---
# <a name="azure-stack-infrastructure-security-controls"></a>Azure Stack infrastruktúra biztonsági vezérlői

*A következőkre vonatkozik: Azure Stack integrált rendszerek*

A biztonsági szempontok és a megfelelőségi szabályozások a hibrid felhők használatának elsődleges okai között vannak. Az Azure Stack az ehhez hasonló forgatókönyvekre lett kialakítva. Ez a cikk a Azure Stack vonatkozó biztonsági vezérlőket ismerteti.

Azure Stack két biztonsági testhelyzeti réteg létezik. Az első réteg a Azure Stack infrastruktúra, amely magában foglalja a Azure Resource Managerhoz tartozó hardver-összetevőket is. Az első réteg tartalmazza a rendszergazdát és a bérlői portálokat. A második rétegből a bérlők által létrehozott, telepített és kezelt munkaterhelések állnak. A második réteg olyan elemeket tartalmaz, mint a virtuális gépek és a App Services webhelyek.

## <a name="security-approach"></a>Biztonsági megközelítés

A Azure Stack biztonsági helyzete úgy lett kialakítva, hogy megvédje a modern fenyegetésekkel szemben, és hogy megfeleljen a főbb megfelelőségi szabványok követelményeinek. Ennek eredményeképpen a Azure Stack infrastruktúra biztonsági helyzete két pillérre épül:

- **Szabálysértés feltételezése**  
    Abból a feltételezésből, hogy a rendszer már megsértette a szolgáltatást, koncentráljon arra, hogy *észlelje és korlátozza a szabálysértések hatását, és* csak a támadásokat próbálja megakadályozni.

- **Alapértelmezés szerint megerősítve**  
    Mivel az infrastruktúra jól meghatározott hardvereken és szoftvereken fut, a Azure Stack alapértelmezés szerint *engedélyezi, konfigurálja és ellenőrzi az összes biztonsági funkciót* .

Mivel a Azure Stackt integrált rendszerként szállítják, a Azure Stack-infrastruktúra biztonsági állapotát a Microsoft határozza meg. Csakúgy, mint az Azure-ban, a bérlők felelősek a bérlői számítási feladatok biztonsági állapotának meghatározásához. Ez a dokumentum alapvető ismereteket biztosít a Azure Stack infrastruktúra biztonsági állapotáról.

## <a name="data-at-rest-encryption"></a>Inaktív adatok titkosítása

Az összes Azure Stack infrastruktúra-és bérlői adatok titkosítva maradnak a BitLocker használatával. Ez a titkosítás a Azure Stack Storage-összetevők fizikai elvesztése vagy ellopása elleni védelmet nyújt. További információ: inaktív [adatok titkosítása Azure Stackban](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Adatátviteli titkosítás

A Azure Stack infrastruktúra-összetevők a TLS 1,2-mel titkosított csatornák használatával kommunikálnak. A titkosítási tanúsítványokat az infrastruktúra önállóan kezeli.

Az összes külső infrastruktúra-végpont, például a REST-végpontok vagy a Azure Stack-portál támogatja a TLS 1,2-et a biztonságos kommunikációhoz. Ezeknek a végpontoknak meg kell adni a titkosítási tanúsítványokat egy harmadik féltől vagy a vállalati hitelesítésszolgáltatótól.

Míg ezek a külső végpontok önaláírt tanúsítványokat is használhatnak, a Microsoft nyomatékosan javasolja a használatot.

## <a name="secret-management"></a>Titkos kód kezelése

Azure Stack-infrastruktúra számos titkot, például jelszavakat használ a működéshez. A legtöbb esetben a rendszer automatikusan elforgatja őket, mert csoportosan felügyelt szolgáltatásfiókok (gMSA-EK), amelyek 24 óránként forognak.

A nem gMSA megmaradó titkokat manuálisan, a privilegizált végponton lévő parancsfájllal lehet elforgatni.

## <a name="code-integrity"></a>Kód integritása

Azure Stack a legújabb Windows Server 2016 biztonsági funkciókat használja. Ezek egyike a Windows Defender Device Guard, amely az alkalmazások engedélyezési listáját biztosítja, és gondoskodik arról, hogy csak az engedélyezett kódok fussanak a Azure Stack infrastruktúrán belül.

A hitelesített kódot a Microsoft vagy az OEM-partner aláírja. Az aláírt hitelesített kód szerepel a Microsoft által meghatározott szabályzatban megadott engedélyezett szoftverek listáján. Ez azt jelenti, hogy csak azok a szoftverek hajthatók végre, amelyeket a Azure Stack-infrastruktúrában történő futtatásra jóváhagytak. A jogosulatlan kód végrehajtására tett kísérletek le vannak tiltva, és a rendszer naplózást generál.

A Device Guard-szabályzat emellett megakadályozza, hogy a harmadik féltől származó ügynökök vagy szoftverek fussanak a Azure Stack infrastruktúrában.

## <a name="credential-guard"></a>Hitelesítőadat-őr

Azure Stack egy másik Windows Server 2016 biztonsági funkciója a Windows Defender hitelesítőadat-őr, amely a pass-The-hash és a pass-The-Ticket típusú támadásokkal szemben Azure Stack infrastruktúra hitelesítő adatainak védelmére szolgál.

## <a name="antimalware"></a>Kártevőirtó

Azure Stack összes összetevője (Hyper-V-gazdagépek és virtuális gépek) védett a Windows Defender víruskeresővel.

A csatlakoztatott forgatókönyvekben a víruskereső-definíció és a motor frissítései naponta többször kerülnek alkalmazásra. A leválasztott forgatókönyvekben a kártevő szoftverek frissítései a havi Azure Stack frissítések részeként lesznek alkalmazva. További információ: [a Windows Defender víruskereső frissítése Azure stackon](azure-stack-security-av.md).

## <a name="constrained-administration-model"></a>Korlátozott adminisztrációs modell

Azure Stack felügyeletét három belépési pont vezérli, amelyek mindegyike konkrét céllal van:

- A felügyeleti [portál](azure-stack-manage-portals.md) egy pont-és kattintásos élményt nyújt a napi kezelési műveletekhez.
- Azure Resource Manager a felügyeleti portál összes felügyeleti műveletét egy, a PowerShell és az Azure CLI által használt REST API használatával teszi elérhetővé.
- Az alacsony szintű műveletek (például az adatközpont-integráció vagy a támogatási forgatókönyvek) esetében Azure Stack egy [Kiemelt végpont](azure-stack-privileged-endpoint.md)nevű PowerShell-végpontot tesz elérhetővé. Ez a végpont csak a megadott parancsmagok engedélyezési készletét teszi elérhetővé, és a rendszer erősen naplózza.

## <a name="network-controls"></a>Hálózati vezérlők

A Azure Stack-infrastruktúra több rétegbeli hálózati Access Control-listát (ACL) tartalmaz. Az ACL-ek megakadályozzák az infrastruktúra-összetevők jogosulatlan elérését, és az infrastruktúra-kommunikációt csak a működéséhez szükséges elérési utakra korlátozza.

A hálózati ACL-ek három rétegben vannak kikényszerítve:

- 1\. réteg: a rack-kapcsolók teteje
- 2\. réteg: szoftveresen definiált hálózat
- 3\. réteg: a gazdagép és a virtuális gép operációs rendszerének tűzfala

## <a name="regulatory-compliance"></a>Jogszabályi megfelelés

A Azure Stack egy harmadik féltől származó független könyvvizsgáló cég által készített formális értékelésen ment keresztül. Ennek eredményeképpen a Azure Stack infrastruktúra megfelel a számos jelentős megfelelőségi szabványnak megfelelő ellenőrzéseknek. A dokumentáció nem a Azure Stack minősítése, mert a szabványok több, a személyzettel kapcsolatos és folyamattal kapcsolatos vezérlőt tartalmaznak. Az ügyfelek Ehelyett ezt a dokumentációt használhatják a minősítési folyamat megkezdéséhez.

Az értékelések a következő szabványokat tartalmazzák:

- A [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) a fizetési kártya iparágát kezeli.
- A [CSA Cloud Control Matrix](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) egy átfogó leképezés több szabványon belül, beleértve a FedRAMP mérsékelt, ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53 és egyebeket.
- [FedRAMP magas](https://www.fedramp.gov/fedramp-releases-high-baseline/) a kormányzati ügyfelek számára.

A megfelelőségi dokumentáció a [Microsoft szolgáltatás megbízhatósági portálján](https://servicetrust.microsoft.com/ViewPage/Blueprint)érhető el. A megfelelőségi útmutatók védett erőforrások, és megkövetelik, hogy jelentkezzen be az Azure Cloud Service hitelesítő adataival.

## <a name="next-steps"></a>Következő lépések

- [Azure Stack biztonsági vezérlők konfigurálása](azure-stack-security-configuration.md)
- [Tudja meg, hogyan rotálhatja az Azure Stack titkos kulcsait](azure-stack-rotate-secrets.md)
- [PCI-DSS és a CSA-CCM dokumentumok Azure Stack](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [DoD-és NIST-dokumentumok a Azure Stack](https://servicetrust.microsoft.com/ViewPage/Blueprint)
