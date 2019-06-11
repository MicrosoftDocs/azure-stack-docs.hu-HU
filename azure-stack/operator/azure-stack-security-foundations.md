---
title: Megismerheti az Azure Stack biztonsági vezérlőket
description: Szolgáltatás-rendszergazdaként a alkalmazni az Azure Stack biztonsági ellenőrzések ismertetése
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
ms.openlocfilehash: db73f0b787b9b163f39d5af89e260ca6a30855bd
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828128"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Az Azure Stack infrastruktúrájának biztonsági rendszer kialakításához

*Vonatkozik: Az Azure Stack integrált rendszerek*

Biztonsági szempontok és a megfelelőségi szabályzat is a fő illesztőprogramokat a hibrid felhők között. Ezek a forgatókönyvek az Azure Stack tervezték. Ez a cikk ismerteti a helyen az Azure Stack biztonsági vezérlők.

Két biztonsági rendszer kialakításához rétegek futhatnak az Azure Stackben. Az első réteg az Azure Stack-infrastruktúra, amely tartalmazza a hardverösszetevők, akár az Azure Resource Manager. Az első réteget tartalmaz, a rendszergazda és a bérlői portált. A második réteg a számítási feladatok létre, telepítheti és kezelheti a bérlők által áll. A második réteg például a virtuális gépek és alkalmazásszolgáltatások webhelyeket tartalmaz.

## <a name="security-approach"></a>Biztonsági módszer

Az Azure stack biztonsági állapotáról célja, hogy a modern fenyegetésekkel szemben, és a fontos megfelelőségi előírásoknak, a követelmények teljesítéséhez lett létrehozva. Ennek eredményeképpen a biztonsági állapotáról, az Azure Stack-infrastruktúra két oszloppal épül:

 - **Feltételezése**  
Feltételezzük, hogy a rendszer még nem tettek kezdve összpontosíthat *észlelése, a problémák hatását korlátozása* helyett csak próbál támadások megelőzése érdekében. 
 - **Alapértelmezés szerint megerősített**  
Mivel az infrastruktúra meghatározott hardveren és szoftverekről, Azure Stack *lehetővé teszi, hogy konfigurálja és érvényesíti a biztonsági funkciók* alapértelmezés szerint.

Mivel az Azure Stack egy integrált rendszer kerül, a Microsoft határozza meg az Azure Stack-infrastruktúra biztonsági állapotát. Csakúgy, mint az Azure-ban, bérlők felelős azok bérlői számítási feladatok biztonsági rendszerébe. Ez a dokumentum Ez a alapvető ismeretek az Azure Stack-infrastruktúra biztonsági állapotát.

## <a name="data-at-rest-encryption"></a>Az adatok inaktív adatok titkosítása
Az összes Azure Stack infrastruktúra és a bérlői adatok titkosítása a BitLocker használatával. A titkosítás fizikai elvesztése / eltulajdonítása Azure Stack tároló-összetevők ellen védi. További információkért lásd: [adatok inaktív adatok titkosítása az Azure Stackben](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Adatok az átvitel során titkosítás
Az Azure Stack infrastruktúra-összetevőket a TLS 1.2 titkosított csatornákon használatával kommunikálni. Titkosítási tanúsítványok önálló kezeli az infrastruktúrát. 

Minden külső infrastruktúra-végpontokra, például a REST-végpontokat vagy az Azure Stack portálon támogatja a TLS 1.2-es biztonságos kommunikációhoz. Titkosítási tanúsítványok, a harmadik féltől származó, vagy a vállalati hitelesítésszolgáltatóval, azokat a végpontokat kell ellátni. 

Önaláírt tanúsítványokat külső végpontokkal is használható, amíg a Microsoft erősen tanácsolja elleni használja őket. 

## <a name="secret-management"></a>Titkos kódok kezelése
Az Azure Stack-infrastruktúra titkos adatait, például jelszavak, számos függvényt használja. Ezek többsége automatikusan vannak elforgatott gyakran, mivel azok Group-Managed szolgáltatásfiókok, amelyek 24 óránként elforgatása.

A fennmaradó titkos kódok, amelyek nem Group-Managed szolgáltatásfiókok manuálisan is rotációja parancsprogrammal Rendszerjogosultságú végpontját.

## <a name="code-integrity"></a>Kódintegritás
Az Azure Stack felhasznál a legújabb Windows Server 2016 biztonsági funkciói. Őket egyik, a Windows Defender Device Guard, amely biztosítja az alkalmazások engedélyezési listáinak, és biztosítja, hogy, hogy csak hitelesített a kód lefut az Azure Stack-infrastruktúrán belül. 

Microsoft vagy az OEM-partner meghatalmazott kódot írt alá. Az aláírt jogosult kódot a Microsoft által meghatározott házirendben megadott engedélyezett szoftverek listáját tartalmazza. Más szóval csak olyan szoftver, amely az Azure Stack-infrastruktúra futtatásához jóvá lett hagyva hajthatók végre. Jogosulatlan kódvégrehajtást tett bármilyen kísérlet le van tiltva, és a egy naplózási jön létre.

A Device Guard-szabályzat is megakadályozza, hogy külső ügynökeinek és szoftverének futtatása az Azure Stack-infrastruktúra.

## <a name="credential-guard"></a>Credential Guard
Egy másik Windows Server 2016 biztonsági szolgáltatás az Azure Stackben a Windows Defender Credential Guard, amelyet az Azure Stack infrastruktúra hitelesítő adatok védelme a Pass-the-Hash és Pass-the-ticket típusú támadásokkal szemben.

## <a name="antimalware"></a>Kártevőirtó
Az Azure Stackben (Hyper-V-gazdagépek és virtuális gépek) minden összetevő a Windows Defender víruskereső használható védett.

A csatlakoztatott esetben víruskereső definíció- és keresőmotor-frissítéseket érvényesek naponta több alkalommal. Kapcsolat nélküli esetekben használható, a kártevőirtó-frissítések alkalmazása a havi Azure Stack-frissítés részeként. További információkért lásd: [frissítése a Windows Defender víruskereső használható az Azure Stacken](azure-stack-security-av.md).

## <a name="constrained-administration-model"></a>Korlátozott adminisztrációs modell
Az Azure Stackben felügyeleti mindegyike adott célt három belépési pontok segítségével lehet szabályozni: 
1. A [adminisztrátori portál](azure-stack-manage-portals.md) pont kattintással élményt nyújt a napi felügyeleti műveleteket.
2. Az Azure Resource Manager a felügyeleti portál egy REST API, PowerShell és az Azure CLI által használt összes felügyeleti műveletet tesz elérhetővé. 
3. Adott alacsony szintű műveletek, például data center integration vagy forgatókönyvek támogatása, Azure Stack egy nevű PowerShell-végpontot tesz közzé [kiemelt végponthoz](azure-stack-privileged-endpoint.md). Ezt a végpontot tesz elérhetővé, csak engedélyezési listához hozzáadni kívánt parancsmagok, és erősen ellenőrzi.

## <a name="network-controls"></a>Hálózati vezérlők
Az Azure Stack-infrastruktúra hálózati hozzáférés-vezérlési lista (ACL) több réteget tartalmaz. Az ACL-ek jogosulatlan hozzáférés elkerülése érdekében az infrastruktúra-összetevőket, és csak az elérési utak a működéséhez szükséges infrastruktúra kommunikációt korlátozza. 

Hálózati hozzáférés-vezérlési listák érvényben vannak, a három réteg:
1.  Tor-kapcsolók
2.  Szoftveralapú hálózat
3.  Gazdagép és a virtuális gép operációsrendszer-tűzfalak

## <a name="regulatory-compliance"></a>Előírásoknak való megfelelés

Az Azure Stack egy harmadik féltől származó független naplózási cég távozott a formális értékelés keresztül. Ennek eredményeképpen a hogyan az Azure Stack-infrastruktúra megfelel-e a alkalmazni beállításait számos fontos megfelelőségi előírásoknak dokumentációja is elérhető. A dokumentáció nem áll az Azure Stack a minősítési miatt a szabványokat, beleértve a több csoporthoz kapcsolódó és a folyamat kapcsolatos vezérlőelem. Inkább ügyfelek használhatják ezt a dokumentációt gyorsan elindíthatja a minősítési folyamatot.

Az értékelések az alábbi szabályok betartása tartalmazza:

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) a iparágával címek.
- [CSA Cloud Control Matrix](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) el az átfogó leképezése több szabványokat, többek között FedRAMP mérsékelt ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53-as és mások.
- [Magas szintű FedRAMP](https://www.fedramp.gov/fedramp-releases-high-baseline/) kormányzati ügyfelek számára.

A megfelelőségi dokumentációjában találhatók a [Microsoft Szolgáltatásmegbízhatósági portált](https://servicetrust.microsoft.com/ViewPage/Blueprint). A megfelelőségi útmutatók védett erőforrásokhoz és igényel, hogy jelentkezzen be az Azure cloud service hitelesítő adataival.

## <a name="next-steps"></a>További lépések

- [Ismerje meg, az Azure Stack titkos kulcsainak rotálása](azure-stack-rotate-secrets.md)
- [PCI-DSS és a CSA-CCM dokumentumokat az Azure Stackhez](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [Az Azure Stack és védelmi Minisztérium a NIST dokumentumok](https://servicetrust.microsoft.com/ViewPage/Blueprint)
