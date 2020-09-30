---
title: Azure Stack HCI biztonsági megfontolások
description: Ez a témakör útmutatást nyújt a Azure Stack HCI operációs rendszer biztonsági szempontjaihoz.
author: JohnCobb1
ms.author: v-johcob
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 181eb53d4b0e5c95065371e6b87e470a5e413d06
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572670"
---
# <a name="azure-stack-hci-security-considerations"></a>Azure Stack HCI biztonsági megfontolások

>A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör a Azure Stack HCI operációs rendszerrel kapcsolatos biztonsági szempontokat és javaslatokat tartalmaz:
- Az 1. rész az operációs rendszer megerősítő alapszintű biztonsági eszközeit és technológiáit, valamint az adatok és identitások védelmét nyújtja a szervezete biztonságos alapjainak hatékony kiépítéséhez.
- A 2. rész a Azure Security Centeron keresztül elérhető erőforrásokat fedi le.
- A 3. rész több speciális biztonsági szempontot is tartalmaz, amelyekkel tovább erősíthető a szervezete biztonsági helyzete ezekben a területeken.

## <a name="why-are-security-considerations-important"></a>Miért fontos a biztonsági megfontolások?
A biztonság a szervezeten belüli mindenkire hatással van a felső szintű felügyelettől az információs feldolgozóig. A nem megfelelő biztonság valós kockázat a szervezetek számára, mivel a biztonsági rések megzavarják az összes szokásos üzleti tevékenységet, és leállítják a szervezetet. Minél hamarabb észlelheti a lehetséges támadásokat, annál gyorsabban csökkentheti a biztonságot.

A környezet gyenge pontjainak kihasználása után a támadók általában 24 – 48 órán belül elérhetik a kezdeti kompromisszumos jogosultságokat a hálózatban található rendszerek felügyeletének megkezdéséhez. A megfelelő biztonsági intézkedések megerősítik a környezet rendszereit annak érdekében, hogy a támadók az idő múlásával akár hetekig, akár hónapokig is átvehetik az irányítást a támadó mozgásának blokkolásával. Az ebben a témakörben található biztonsági javaslatok bevezetésével a szervezetnek a lehető leggyorsabban kell felderíteni és reagálnia az ilyen támadásokra.

## <a name="part-1-build-a-secure-foundation"></a>1. rész: biztonságos alaprendszer létrehozása
A következő fejezetek a biztonsági eszközöket és technológiákat ismertetik a környezetében a Azure Stack HCI operációs rendszert futtató kiszolgálók biztonságos alapjainak létrehozásához.

### <a name="harden-the-environment"></a>A környezet megerősítése
Ez a szakasz azt ismerteti, hogyan biztosítható az operációs rendszeren futó szolgáltatások és virtuális gépek (VM-EK) elleni védelem:
- **Azure stack a HCI tanúsítvánnyal rendelkező hardverek** biztosítják az egységes biztonságos rendszerindítási, UEFI-és TPM-beállításokat a dobozból. A virtualizálás-alapú biztonság és a tanúsítvánnyal rendelkező hardverek kombinálásával megvédheti a biztonság szempontjából érzékeny munkaterheléseket. Ezt a megbízható infrastruktúrát úgy is összekapcsolhatja, hogy Azure Security Center a viselkedési elemzések és jelentéskészítés aktiválásához a gyorsan változó munkaterhelések és fenyegetések miatt.

    - A *biztonságos rendszerindítás* a számítógépes iparág által kifejlesztett biztonsági szabvány, amellyel biztosítható, hogy egy eszköz csak az eredeti BERENDEZÉSGYÁRTÓ (OEM) által megbízhatónak ítélt szoftvereket használjon. További információ: [biztonságos rendszerindítás](/windows-hardware/design/device-experiences/oem-secure-boot).
    - Az *egyesült Extensible Firmware Interface (UEFI)* szabályozza a kiszolgáló rendszerindítási folyamatát, majd átirányítja a vezérlést Windows vagy más operációs rendszerre. További információért lásd az [UEFI belső vezérlőprogram-követelményeit](/windows-hardware/design/device-experiences/oem-uefi)ismertető témakört.
    - A *platformmegbízhatósági modul (TPM)* technológia hardveres, biztonsággal kapcsolatos funkciókat biztosít. A TPM-lapka egy biztonságos kriptográfiai processzor, amely a titkosítási kulcsok használatát állítja elő, tárolja és korlátozza. További információ: [platformmegbízhatósági modul Technology – áttekintés](/windows/security/information-protection/tpm/trusted-platform-module-overview).

    Ha többet szeretne megtudni a Azure Stack HCI Certified-szolgáltatókról, tekintse meg a következő témakört: [Azure stack HCI Solutions](https://azure.microsoft.com/products/azure-stack/hci/) webhelye.

- **Device Guard** és **hitelesítőadat-őr**. Az Eszközkezelő olyan kártevők elleni védelmet biztosít, amelyeknek nincs ismert aláírása, aláíratlan kód, valamint a kernelhez való hozzáférést biztosító kártevők bizalmas információk rögzítésére vagy a rendszer károsodására. A Windows Defender hitelesítőadat-őr virtualizálás-alapú biztonságot használ a titkok elkülönítésére, így csak a rendszerjogosultságú rendszerszoftverek férhetnek hozzájuk.

    További információ: a [Windows Defender hitelesítő adatainak kezelése](/windows/security/identity-protection/credential-guard/credential-guard-manage) és az [Eszközkezelő és a Hitelesítőadat-kezelő hardveres készültségi eszköz](https://www.microsoft.com/download/details.aspx?id=53337)letöltése.

- A **Windows** és a **belső vezérlőprogram** frissítései elengedhetetlenek a fürtök, a kiszolgálók (beleértve a vendég virtuális gépeket) és a számítógépek számára, így biztosítva, hogy az operációs rendszer és a rendszer hardvere is védve legyen a támadók ellen. A Windows felügyeleti központ **frissítések** eszközével az egyes rendszereken is alkalmazhat frissítéseket. Ha a hardveres szolgáltató a Windows felügyeleti központot is támogatja az illesztőprogram, a belső vezérlőprogram és a megoldás frissítéseinek beszerzéséhez, akkor a frissítéseket a Windows frissítéseivel megegyező időpontban is beszerezheti, ellenkező esetben közvetlenül a gyártótól kérheti le őket.

    További információ: [a fürt frissítése](../manage/update-cluster.md).

    Ha egyszerre több fürt és kiszolgáló frissítéseit szeretné kezelni, érdemes lehet előfizetni az opcionális Azure Update Management szolgáltatásra, amely integrálva van a Windows felügyeleti központba. További információ: [Azure Update Management a Windows felügyeleti központban](https://www.thomasmaurer.ch/2018/11/azure-update-management-windows-admin-center).

### <a name="protect-data"></a>Adatok védelme
Ez a szakasz azt ismerteti, hogyan használható a Windows felügyeleti központ az adatok és a számítási feladatok az operációs rendszeren való védelme érdekében:

- **A tárolóhelyek BitLocker szolgáltatás** védi a nyugalmi állapotban lévő adatok védelmét. A BitLocker használatával titkosíthatja a tárolóhelyek adatköteteinek tartalmát az operációs rendszeren. Az adatvédelemre szolgáló BitLocker használatával a szervezetek a kormányzati, regionális és iparági szabványoknak megfelelő, például az FIPS 140-2-es és a HIPAA-szabványoknak is eleget tesznek.
 
    Ha többet szeretne megtudni a BitLocker használatáról a Windows felügyeleti központban, tekintse meg a [mennyiségi titkosítás, a deduplikálás és a tömörítés engedélyezése](../manage/volume-encryption-deduplication.md) című témakört.

- A Windows hálózat **SMB** -titkosítása védi az átvitt adatforgalmat. A *kiszolgáló-üzenetblokk (SMB)* egy hálózati fájlmegosztás protokoll, amely lehetővé teszi, hogy az alkalmazások a számítógépeken a fájlok olvasását és írását, illetve a számítógép-hálózatban lévő kiszolgálói programoktól származó szolgáltatásokat kérjenek.

    Az SMB-titkosítás engedélyezéséhez tekintse meg az [SMB biztonsági fejlesztéseit](/windows-server/storage/file-server/smb-security)ismertető témakört.

- A Windows **Defender Antivirus** a Windows felügyeleti központban védi az operációs rendszert az ügyfeleken és a kiszolgálókon a vírusok, kártevők, kémprogramok és más fenyegetések ellen. További információ: [Microsoft Defender Antivirus a Windows Server 2016 és 2019](/windows/security/threat-protection/microsoft-defender-antivirus/microsoft-defender-antivirus-on-windows-server-2016).

### <a name="protect-identities"></a>Identitások elleni védelem
Ebből a szakaszból megtudhatja, hogyan használhatja a Windows felügyeleti központot a Kiemelt identitások védelemmel való ellátásához:

- A **hozzáférés-vezérlés** növelheti a felügyeleti környezet biztonságát. Ha Windows felügyeleti központú kiszolgálót használ (Windows 10 rendszerű számítógépen futtatva), akkor a Windows felügyeleti központhoz való hozzáférés két szintjét vezérelheti: átjáró-felhasználók és átjáró-rendszergazdák. Az átjáró-rendszergazdai identitás-szolgáltató beállításai a következők:
    - Active Directory vagy helyi számítógép-csoportok az intelligens kártya hitelesítésének érvényesítéséhez.
    - Azure Active Directory a feltételes hozzáférés és a többtényezős hitelesítés érvénybe léptetéséhez.
 
    További információ: [felhasználói hozzáférési beállítások a Windows felügyeleti központban](/windows-server/manage/windows-admin-center/plan/user-access-options) és a [felhasználói Access Control és engedélyek konfigurálása](/windows-server/manage/windows-admin-center/configure/user-access-control).

- A Windows felügyeleti központba való **böngésző-forgalom** HTTPS protokollt használ. A Windows felügyeleti központ és a felügyelt kiszolgálók közötti forgalom szabványos PowerShell és Windows Management Instrumentation (WMI) protokollt használ a Rendszerfelügyeleti webszolgáltatások (WinRM) használatával. A Windows felügyeleti központ támogatja a helyi rendszergazdai jelszó megoldását (körök), az erőforrás-alapú korlátozott delegálást, az átjáró hozzáférés-vezérlését Active Directory (AD) vagy Microsoft Azure Active Directory (Azure AD) és szerepköralapú hozzáférés-vezérlés (RBAC) használatával a célkiszolgáló kezeléséhez.

    A Windows felügyeleti központ támogatja a Microsoft Edge (Windows 10, 1709-es vagy újabb verzió), a Google Chrome és a Microsoft Edge Insider használatát a Windows 10 rendszeren. A Windows felügyeleti központot Windows 10 rendszerű vagy Windows Server rendszerű számítógépre is telepítheti.

    Ha a Windows felügyeleti központot egy olyan kiszolgálóra telepíti, amely átjáróként fut, a gazdagépen nincs felhasználói felület. Ebben az esetben a rendszergazdák egy HTTPS-munkameneten keresztül jelentkezhetnek be a kiszolgálóra, amelyet a gazdagépen egy önaláírt biztonsági tanúsítvány biztosít. Azonban jobb, ha egy megbízható hitelesítésszolgáltatótól származó megfelelő SSL-tanúsítványt használ a bejelentkezési folyamathoz, mert a támogatott böngészők nem biztonságosként kezelik az önaláírt kapcsolatokat, még akkor is, ha a kapcsolat egy helyi IP-címhez csatlakozik egy megbízható VPN-en keresztül.

    Ha többet szeretne megtudni a szervezet telepítési lehetőségeiről, tekintse meg az [Ön számára legmegfelelőbb telepítési típust](/windows-server/manage/windows-admin-center/plan/installation-options).

- A **CredSSP** egy hitelesítési szolgáltató, amelyet a Windows felügyeleti központ néhány esetben használ a hitelesítő adatoknak a felügyelni kívánt kiszolgálón kívüli gépekre való továbbítására. A Windows felügyeleti központban jelenleg a következő CredSSP szükséges:
    - Hozzon létre egy új fürtöt.
    - A **frissítési** eszköz eléréséhez használja a feladatátvételi fürtszolgáltatást vagy a fürtöket támogató frissítési funkciókat.
    - A nem aggregált SMB-tárolók kezelése a virtuális gépeken.

    További információ: a [Windows felügyeleti központ használata a CredSSP?](/windows-server/manage/windows-admin-center/understand/faq#does-windows-admin-center-use-credssp)

- A Windows felügyeleti központban a **szerepköralapú hozzáférés-vezérlés (RBAC)** lehetővé teszi a felhasználók számára a felügyelethez szükséges kiszolgálók hozzáférését a teljes helyi rendszergazdák helyett. Ha a RBAC-t a Windows felügyeleti központban szeretné használni, az egyes felügyelt kiszolgálókat egy elég rendszergazdai végponttal kell konfigurálnia.

    További információ: [szerepköralapú hozzáférés-vezérlés](/windows-server/manage/windows-admin-center/plan/user-access-options#role-based-access-control) és [elég felügyelet](/powershell/scripting/learn/remoting/jea/overview).

- Az identitások kezeléséhez és védelméhez használható Windows felügyeleti központban található **biztonsági eszközök** közé tartozik a Active Directory, a tanúsítványok, a tűzfal, a helyi felhasználók és a csoportok.

    További információ: [kiszolgálók kezelése a Windows felügyeleti központtal](/windows-server/manage/windows-admin-center/use/manage-servers).

## <a name="part-2-use-azure-security-center"></a>2. rész: a Azure Security Center használata
*Azure Security Center* egy egységes infrastruktúra-alapú biztonsági felügyeleti rendszer, amely erősíti az adatközpontok biztonsági állapotát, és komplex veszélyforrások elleni védelmet biztosít a felhőben és a helyszínen lévő hibrid számítási feladatokban. A Security Center eszközöket biztosít a hálózat biztonsági állapotának felméréséhez, a munkaterhelések védelméhez, a biztonsági riasztások megadásához, valamint a támadások elhárításához és a jövőbeli fenyegetések megoldásához szükséges konkrét javaslatok követéséhez. Security Center az Azure-szolgáltatásokkal való automatikus kiépítés és védelem nélkül hajtja végre az összes ilyen szolgáltatást nagy sebességgel a felhőben.

Security Center védi a virtuális gépeket a Windows-kiszolgálók és a Linux-kiszolgálók számára, ha telepíti az Log Analytics ügynököt ezekre az erőforrásokra. Az Azure összekapcsolja azokat az eseményeket, amelyekkel az ügynökök begyűjtik a munkaterhelések biztonságossá tételéhez szükséges javaslatokat (megerősítő feladatok). Az ajánlott biztonsági eljárások alapján megerősítő feladatok közé tartozik a biztonsági házirendek kezelése és érvényesítése. Ezután nyomon követheti az eredményeket, és kezelheti a megfelelőséget és a szabályozást az idő múlásával Security Center figyeléssel, miközben csökkenti a támadási felületet az összes erőforráson.

Annak kezelése, hogy ki férhet hozzá az Azure-erőforrásokhoz és -előfizetésekhez, az Azure-szabályozási stratégia fontos részét képezi. Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-beli hozzáférések kezelésének első számú módja. További információ: [Az Azure-környezethez való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel](/azure/cloud-adoption-framework/ready/azure-setup-guide/manage-access).

Az Security Center a Windows felügyeleti központban való használata Azure-előfizetést igényel. Első lépésként tekintse meg [a Azure Security Center integrálása a Windows felügyeleti központtal](/azure/security-center/windows-admin-center-integration)című témakört.

A regisztrálás után a Security Center a Windows felügyeleti központban: a **minden kapcsolat** lapon válasszon ki egy kiszolgálót vagy virtuális gépet, az **eszközök**területen válassza a **Azure Security Center**lehetőséget, majd válassza a **Bejelentkezés az Azure-ba**lehetőséget.

További információ: [What is Azure Security Center?](/azure/security-center/security-center-intro)

## <a name="part-3-add-advanced-security"></a>3. rész: fokozott biztonság hozzáadása
A következő részekben a speciális biztonsági eszközöket és technológiákat ajánljuk a környezetében a Azure Stack HCI operációs rendszert futtató kiszolgálók további megerősítéséhez.

### <a name="harden-the-environment"></a>A környezet megerősítése
- A **Microsoft biztonsági** alapkonfigurációi a Microsoft által a kereskedelmi szervezetekkel és az Egyesült Államok kormányával, például a védelmi minisztériumtal való együttműködés során szerzett biztonsági javaslatokon alapulnak. A biztonsági alapkonfigurációk a Windows tűzfal, a Windows Defender és sok más ajánlott biztonsági beállításait tartalmazzák.

    A biztonsági alapkonfigurációk Csoportházirend objektumként (GPO) vannak megadva, amelyeket importálhat Active Directory tartományi szolgáltatásokba (AD DS), majd üzembe helyezheti a tartományhoz csatlakoztatott kiszolgálókon, hogy megerősítse a környezetet. Helyi parancsfájl-eszközöket is használhat a biztonsági alapkonfigurációkkal rendelkező önálló (nem tartományhoz csatlakozó) kiszolgálók konfigurálásához. A biztonsági alapkonfigurációk használatának megkezdéséhez töltse le a [Microsoft biztonsági megfelelőségi eszközkészlet 1,0](https://www.microsoft.com/download/details.aspx?id=55319)-es verziójára.

    További információ: [Microsoft biztonsági](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines)alapkonfigurációk.

### <a name="protect-data"></a>Adatok védelme
- **A Hyper-V környezet megkeményedése** megköveteli, hogy a virtuális gépen futó Windows Server megerősítse a fizikai kiszolgálón futó operációs rendszert. Mivel a virtuális környezetek jellemzően több, ugyanazon a fizikai gazdagépen található virtuális géppel rendelkeznek, elengedhetetlen a fizikai gazdagép és a rajta futó virtuális gépek elleni védelem. Egy gazdagépet veszélyeztető támadó több virtuális gépre is hatással lehet, és nagyobb hatással van a munkaterhelésekre és szolgáltatásokra. Ez a szakasz a Windows Server Hyper-V környezetben való megerősítő következő módszereit ismerteti:

    - A **védett hálók és a védett virtuális gépek** megerősítik a Hyper-V környezetekben futó virtuális gépek biztonságát azáltal, hogy a támadók nem módosítják a virtuálisgép-fájlokat. A *védett háló* egy olyan gazda Guardian-szolgáltatásból (HGS) áll, amely általában három csomópontból álló fürt, egy vagy több védett gazdagép és egy védett virtuális gép egy készlete. Az igazolási szolgáltatás értékeli a gazdagépek kérelmének érvényességét, míg a kulcskezelő szolgáltatás meghatározza, hogy ki kell-e szabadítani azokat a kulcsokat, amelyeket a védett gazdagépek használhatnak a védett virtuális gép elindításához.

        További információt a [védett hálók és a védett virtuális gépek áttekintése](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms)című témakörben talál.
     
     - A Windows Server **Virtual platformmegbízhatósági modul (vTPM)** támogatja a TPM használatát a virtuális gépek számára, amely lehetővé teszi a speciális biztonsági technológiák, például a BitLocker használata a virtuális gépeken. A TPM-támogatást bármely 2. generációs Hyper-V virtuális gépen engedélyezheti a Hyper-V kezelőjével vagy a `Enable-VMTPM` Windows PowerShell-parancsmag használatával.
     
        További információ: [enable-VMTPM](/powershell/module/hyper-v/enable-vmtpm).
     
     - A Azure Stack HCI és a Windows Server rendszerekben a **szoftveres hálózatkezelés (Sdn)** központilag konfigurálja és felügyeli a fizikai és virtuális hálózati eszközöket, például az útválasztókat, a kapcsolókat és az adatközpontban lévő átjárókat. A virtuális hálózati elemek, például a Hyper-V virtuális kapcsoló, a Hyper-V hálózati virtualizálás és a RAS-átjáró úgy vannak kialakítva, hogy az SDN-infrastruktúra szerves elemei legyenek.

        További információ: [szoftveresen definiált hálózatkezelés (Sdn)](/windows-server/networking/sdn/).

### <a name="protect-identities"></a>Identitások elleni védelem
- A **helyi rendszergazda jelszavas megoldás (kör)** egy egyszerű mechanizmus a tartományhoz csatlakoztatott rendszerek Active Directory, amelyek rendszeres időközönként beállítja az egyes számítógépek helyi rendszergazdai fiókjának jelszavát egy új véletlenszerű és egyedi értékre. A jelszavak tárolása a Active Directory megfelelő számítógép-objektumának biztonságos bizalmas attribútumában történik, ahol csak a kifejezetten jogosult felhasználók kérhetik le őket. A körök a távoli számítógépek felügyeletére szolgáló helyi fiókokat használják olyan módon, amely némi előnyt biztosít a tartományi fiókok használatával kapcsolatban. További információ [: a helyi fiókok távoli használata: a körök mindent megváltoztatnak](/archive/blogs/secguide/remote-use-of-local-accounts-laps-changes-everything).

    A körök használatának megkezdéséhez töltse le a [helyi rendszergazda jelszavas megoldását (kör)](https://www.microsoft.com/download/details.aspx?id=46899).

- A **Microsoft Advanced Threat Analytics (ATA)** egy helyszíni termék, amellyel felderítheti a rendszerjogosultságú identitásokat veszélyeztető támadókat. Az ATA elemzi a hitelesítési, engedélyezési és információgyűjtési protokollok (például a Kerberos és a DNS) hálózati forgalmát. Az ATA az adatelemzéssel a felhasználók és más entitások viselkedési profilját használja a hálózatban, hogy észlelje a rendellenességeket és az ismert támadási mintákat.
    
    További információ: [Mi az Advanced Threat Analytics?](/advanced-threat-analytics/what-is-ata).

- A **Windows Defender távoli hitelesítő adatok** védelme távoli asztal kapcsolaton keresztül védi a hitelesítő adatokat azáltal, hogy a Kerberos-kérelmeket visszairányítja a kapcsolódást kérő eszközre. Emellett egyszeri bejelentkezést (SSO) is biztosít Távoli asztal munkamenetekhez. Egy Távoli asztal-munkamenet során, ha a céleszköz sérült, a hitelesítő adatai nem jelennek meg, mert a hitelesítő adatok és a hitelesítő adatok származtatása nem történik meg a hálózaton a célként megadott eszközön.

    További információ: a [Windows Defender hitelesítőadat-őr kezelése](/windows/security/identity-protection/credential-guard/credential-guard-manage).

## <a name="next-steps"></a>További lépések
A biztonsággal és a szabályozás megfelelőségével kapcsolatos további információkért lásd még:
- [Biztonság és ellenőrzés](/windows-server/security/security-and-assurance)
- [Ajánlott biztonsági eljárások Azure-megoldásokhoz](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)
