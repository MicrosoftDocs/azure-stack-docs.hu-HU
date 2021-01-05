---
title: Azure Stack hub biztonsági vezérlők
titleSuffix: Azure Stack Hub
description: Ismerje meg az Azure Stack hub-ra alkalmazott biztonsági helyzeteket és vezérlőket.
author: PatAltimore
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 04/07/2020
ms.openlocfilehash: e3bda80857a4c7026bac58f9e1d866b1d21168d5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869202"
---
# <a name="azure-stack-hub-infrastructure-security-controls"></a>Azure Stack hub-infrastruktúra biztonsági vezérlői

A biztonsági szempontok és a megfelelőségi szabályozások a hibrid felhők használatának elsődleges okai között vannak. Az Azure Stack hub ezeket a forgatókönyveket hivatott kialakítani. Ez a cikk a Azure Stack hub biztonsági ellenőrzéseit ismerteti.

Azure Stack központban két biztonsági testhelyzeti réteg létezik. Az első réteg a Azure Stack hub-infrastruktúra, amely magában foglalja a Azure Resource Managerhoz tartozó hardver-összetevőket is. Az első réteg tartalmazza a rendszergazdát és a felhasználói portálokat. A második rétegből a bérlők által létrehozott, telepített és kezelt munkaterhelések állnak. A második réteg olyan elemeket tartalmaz, mint a virtuális gépek és a App Services webhelyek.

## <a name="security-approach"></a>Biztonsági megközelítés

Az Azure Stack hub biztonsági helyzete úgy lett kialakítva, hogy megvédje a modern fenyegetésekkel szemben, és hogy megfeleljen a főbb megfelelőségi szabványok követelményeinek. Ennek eredményeképpen az Azure Stack hub-infrastruktúra biztonsági helyzete két pillérre épül:

- **Szabálysértés feltételezése**  
    Abból a feltételezésből, hogy a rendszer már megsértette a szolgáltatást, koncentráljon arra, hogy *észlelje és korlátozza a szabálysértések hatását, és* csak a támadásokat próbálja megakadályozni.

- **Alapértelmezés szerint megerősítve**  
    Mivel az infrastruktúra jól meghatározott hardvereken és szoftvereken fut, Azure Stack hub alapértelmezés szerint *engedélyezi, konfigurálja és érvényesíti az összes biztonsági funkciót* .

Mivel Azure Stack hub-t integrált rendszerként továbbítják, az Azure Stack hub-infrastruktúra biztonsági állapotát a Microsoft határozza meg. Csakúgy, mint az Azure-ban, a bérlők felelősek a bérlői számítási feladatok biztonsági állapotának meghatározásához. Ez a dokumentum alapvető ismereteket nyújt az Azure Stack hub-infrastruktúra biztonsági állapotáról.

## <a name="data-at-rest-encryption"></a>Inaktív adatok titkosítása

Az összes Azure Stack hub-infrastruktúra és a bérlői adatok titkosítva vannak a BitLocker használatával. Ez a titkosítás védi Azure Stack hub Storage-összetevők fizikai elvesztését vagy ellopását. További információ: inaktív [adatok titkosítása Azure stack hub-ban](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Adatátviteli titkosítás

Az Azure Stack hub infrastruktúra-összetevői a TLS 1,2-mel titkosított csatornák használatával kommunikálnak. A titkosítási tanúsítványokat az infrastruktúra önállóan kezeli.

Az összes külső infrastruktúra-végpont, például a REST-végpontok vagy az Azure Stack hub-portál támogatja a TLS 1,2-et a biztonságos kommunikációhoz. Ezeknek a végpontoknak meg kell adni a titkosítási tanúsítványokat egy harmadik féltől vagy a vállalati hitelesítésszolgáltatótól.

Míg ezek a külső végpontok önaláírt tanúsítványokat is használhatnak, a Microsoft nyomatékosan javasolja a használatot.
A TLS 1,2 Azure Stack hub külső végpontokon való betartatásával kapcsolatos további információkért lásd: [Azure stack hub biztonsági vezérlőinek konfigurálása](azure-stack-security-configuration.md).

## <a name="secret-management"></a>Titkos kódok kezelése

Azure Stack hub-infrastruktúra a működéséhez számos titkot, például jelszavakat és tanúsítványokat használ. A belső szolgáltatásfiókok által társított jelszavak többsége 24 óránként automatikusan el lesz forgatva, mert [csoportosan felügyelt szolgáltatásfiókok (gMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview), a közvetlenül a belső tartományvezérlő által felügyelt tartományi fiók típusa.

Azure Stack hub-infrastruktúra 4096 bites RSA-kulcsokat használ az összes belső tanúsítványához. A külső végpontokhoz ugyanaz a kulcs hosszúságú tanúsítvány is használható. A titkokkal és a tanúsítvány elforgatásával kapcsolatos további információkért tekintse meg a [titkok Elforgatása Azure stack központban](azure-stack-rotate-secrets.md)című témakört.

## <a name="windows-defender-application-control"></a>Windows Defender Alkalmazásvezérlés

Az Azure Stack Hub a Windows Server legújabb biztonsági funkcióit használja. Ezek közül az egyik a Windows Defender Application Control (WDAC, korábbi nevén Code Integrity), amely végrehajtható szűrést biztosít, és biztosítja, hogy csak az Azure Stack hub-infrastruktúrán belüli, jóváhagyott kód fusson.

A hitelesített kódot a Microsoft vagy az OEM-partner aláírja. Az aláírt hitelesített kód szerepel a Microsoft által meghatározott szabályzatban megadott engedélyezett szoftverek listáján. Ez azt jelenti, hogy csak az Azure Stack hub-infrastruktúrában való futtatásra jóváhagyott szoftverek hajthatók végre. A rendszer blokkol minden jogosulatlan kód futtatására tett kísérletet, és ilyen esetben riasztást küld. Az Azure Stack Hub a felhasználói módú kódintegritást (UMCI) és a hipervizor-kódintegritást (HVCI) egyaránt kényszeríti.

A WDAC szabályzat emellett megakadályozza, hogy a harmadik féltől származó ügynökök vagy szoftverek fussanak az Azure Stack hub-infrastruktúrában.
A WDAC kapcsolatos további információkért tekintse meg a [Windows Defender alkalmazás-vezérlés és a kód integritásának virtualizálás-alapú védelme](/windows/security/threat-protection/device-guard/introduction-to-device-guard-virtualization-based-security-and-windows-defender-application-control)című témakört.

## <a name="credential-guard"></a>Credential Guard

Az Azure Stack hub egy másik Windows Server biztonsági funkciója a Windows Defender hitelesítő adatai, amely a pass-The-hash és a pass-The-Ticket típusú támadásokkal szemben Azure Stack hub-infrastruktúra hitelesítő adatainak védelmére szolgál.

## <a name="antimalware"></a>Kártevőirtó

Azure Stack hub összes összetevője (Hyper-V-gazdagépek és virtuális gépek) védelme a Windows Defender víruskeresővel történik.

A csatlakoztatott forgatókönyvekben a víruskereső-definíció és a motor frissítései naponta többször kerülnek alkalmazásra. A leválasztott forgatókönyvekben a kártevők elleni frissítések a havi Azure Stack hub-frissítések részeként lesznek alkalmazva. Ha a Windows Defender definícióinak gyakoribb frissítése szükséges a leválasztott forgatókönyvekben, Azure Stack hub a Windows Defender frissítéseinek importálását is támogatja. További információ: [a Windows Defender víruskereső frissítése Azure stack hub-on](azure-stack-security-av.md).

## <a name="secure-boot"></a>Biztonságos rendszerindítás

Azure Stack hub a Hyper-V-gazdagépeken és az infrastruktúra-alapú virtuális gépeken a biztonságos rendszerindítást kényszeríti. 

## <a name="constrained-administration-model"></a>Korlátozott adminisztrációs modell

Az Azure Stack hub felügyeletét három belépési pont vezérli, amelyek mindegyike konkrét céllal rendelkezik:

- A felügyeleti [portál](azure-stack-manage-portals.md) egy pont-és kattintásos élményt nyújt a napi kezelési műveletekhez.
- Azure Resource Manager a felügyeleti portál összes felügyeleti műveletét egy, a PowerShell és az Azure CLI által használt REST API használatával teszi elérhetővé.
- Az alacsony szintű műveletek (például az adatközpont-integráció vagy a támogatási forgatókönyvek) esetében Azure Stack hub egy [Kiemelt végpont](azure-stack-privileged-endpoint.md)nevű PowerShell-végpontot tesz elérhetővé. Ez a végpont csak a parancsmagok engedélyezett készletét teszi elérhetővé, és a rendszer erősen naplózza.

## <a name="network-controls"></a>Hálózati vezérlők

A Azure Stack hub-infrastruktúra több rétegbeli hálózati Access Control-listát (ACL) tartalmaz. Az ACL-ek megakadályozzák az infrastruktúra-összetevők jogosulatlan elérését, és az infrastruktúra-kommunikációt csak a működéséhez szükséges elérési utakra korlátozza.

A hálózati ACL-ek három rétegben vannak kikényszerítve:

- 1. réteg: a rack-kapcsolók teteje
- 2. réteg: szoftveresen definiált hálózat
- 3. réteg: a gazdagép és a virtuális gép operációs rendszerének tűzfala

## <a name="regulatory-compliance"></a>Előírásoknak való megfelelés

Az Azure Stack hub egy harmadik féltől származó, független könyvvizsgáló cég által készített formális képesség-felmérésen ment keresztül. Ennek eredményeképpen a Azure Stack hub-infrastruktúra megfelel a számos jelentős megfelelőségi szabványnak megfelelő ellenőrzéseknek. A dokumentáció nem Azure Stack hub minősítése, mert a szabványok több, a személyzettel kapcsolatos és folyamattal kapcsolatos vezérlőt tartalmaznak. Az ügyfelek Ehelyett ezt a dokumentációt használhatják a minősítési folyamat megkezdéséhez.

Az értékelések a következő szabványokat tartalmazzák:

- A [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) a fizetési kártya iparágát kezeli.
- A [CSA Cloud Control Matrix](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) egy átfogó leképezés több szabványon belül, beleértve a FedRAMP mérsékelt, ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53 és egyebeket.
- [FedRAMP magas](https://www.fedramp.gov/fedramp-releases-high-baseline/) a kormányzati ügyfelek számára.

A megfelelőségi dokumentáció a [Microsoft szolgáltatás megbízhatósági portálján](https://aka.ms/azurestackcompliance)érhető el. A megfelelőségi útmutatók védett erőforrások, és megkövetelik, hogy jelentkezzen be az Azure Cloud Service hitelesítő adataival.

## <a name="next-steps"></a>További lépések

- [Azure Stack hub biztonsági vezérlőinek konfigurálása](azure-stack-security-configuration.md)
- [Megtudhatja, hogyan forgathatja el a titkokat Azure Stack hub-ban](azure-stack-rotate-secrets.md)
- [PCI-DSS és a CSA-CCM dokumentumok Azure Stack hub-hoz](https://aka.ms/azurestackcompliance)
