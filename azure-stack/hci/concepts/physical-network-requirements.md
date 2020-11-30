---
title: A Azure Stack HCI fizikai hálózati követelményei
description: A Azure Stack HCI fizikai hálózati követelményeinek megismerése
author: v-dasis
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: a8eea75c58f597a1026b5075537d415c0694dc3c
ms.sourcegitcommit: 422c3f19d817037fa6ce73cca253742bbdec147e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321999"
---
# <a name="physical-network-requirements-for-azure-stack-hci"></a>A Azure Stack HCI fizikai hálózati követelményei

> A következőre vonatkozik Azure Stack HCI, Version 20H2

Ez a témakör a fizikai (háló) hálózati szempontokat és a Azure Stack HCI-re vonatkozó követelményeket ismerteti, különösen a hálózati kapcsolók esetében.

> [!NOTE]
> A jövőbeli Azure Stack HCI-verziókra vonatkozó követelmények változhatnak.

## <a name="network-switches-for-azure-stack-hci"></a>Azure Stack HCI hálózati kapcsolók

A Microsoft az alábbi, a **hálózati kapcsolókra vonatkozó követelmények** szakaszban azonosított szabványoknak és protokolloknak Azure stack a HCI-t. Noha a Microsoft nem hitelesíti a hálózati kapcsolókat, a gyártókkal együttműködve azonosíthatja a Azure Stack HCI-követelményeket támogató eszközöket.

Ezeket a követelményeket a [Windows hardverkompatibilitási programjának specifikációi és szabályzatai](https://docs.microsoft.com/windows-hardware/design/compatibility/whcp-specifications-policies)is közzéteszik.  Válassza a **specifikációk és szabályzatok letöltése** elemet, a 1809-es verziót, nyissa meg a zip-fájlt, nyissa meg **WHCP-Components-Peripherals-Specification-1809.pdf**, majd lásd a **Device. Network. Switch. SDDC** szakaszt.

> [!IMPORTANT]
> Míg más, az itt nem felsorolt technológiákat és protokollokat használó hálózati kapcsolók működhetnek, a Microsoft nem tudja garantálni, hogy a Azure Stack HCI-mel működjenek együtt, és előfordulhat, hogy nem tud segíteni a hibák elhárításában.

Hálózati kapcsolók vásárlása esetén lépjen kapcsolatba a kapcsoló gyártójával, és ellenőrizze, hogy az eszközök megfelelnek-e az összes Azure Stack HCI-követelménynek. A következő szállítók (betűrendben) megerősítették, hogy a kapcsolók támogatják Azure Stack HCI követelményeiket:

|Szállító|10 GbE|25 GbE|100 GbE|
|-----|-----|-----|-----|
|Dell|[S41xx sorozat](https://www.dell.com/learn/us/en/45/shared-content~data-sheets~en/documents~dell-emc-networking-s4100-series-spec-sheet.pdf)|[S52xx sorozat](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|[S52xx sorozat](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|
|Lenovo|[G8272](https://lenovopress.com/tips1267-lenovo-rackswitch-g8272), [NE1032](https://lenovopress.com/lp0605-thinksystem-ne1032-rackswitch)|[NE2572](https://lenovopress.com/lp0608-lenovo-thinksystem-ne2572-rackswitch) |[NE10032](https://lenovopress.com/lp0609-lenovo-thinksystem-ne10032-rackswitch) |

> [!IMPORTANT]
> A Microsoft frissíti ezt a listát, mert a hálózati kapcsolók gyártói által végrehajtott változásokról értesül.

Ha a kapcsolót nem tartalmazza, lépjen kapcsolatba a kapcsoló gyártójával, és győződjön meg arról, hogy a Switch modell és az operációs rendszer verziója támogatja a következő szakaszban foglalt követelményeket.

## <a name="network-switch-requirements"></a>Hálózati kapcsolókra vonatkozó követelmények

Ez a szakasz azokat az iparági szabványokat sorolja fel, amelyek az összes Azure Stack HCI-környezetben használt hálózati kapcsolók esetében kötelezőek. Ezek a szabványok biztosítják a Azure Stack HCI-fürtök csomópontjai közötti megbízható kommunikációt.

> [!NOTE]
> A számítási, tárolási és felügyeleti forgalomhoz használt hálózati adapterekhez Ethernet szükséges.

Itt láthatók a kötelező IEEE-szabványok és-specifikációk:

### <a name="standard-ieee-8021q"></a>Standard: IEEE 802.1 Q

Az Ethernet-kapcsolóknak meg kell felelniük a VLAN-okat definiáló IEEE 802.1 Q specifikációnak. A VLAN-ok a Azure Stack HCI számos aspektusához szükségesek, és minden esetben szükségesek.

### <a name="standard-ieee-8021qbb"></a>Standard: IEEE 802.1 Qbb

Az Ethernet-kapcsolóknak meg kell felelniük az IEEE 802.1 Qbb specifikációnak, amely meghatározza a prioritási folyamat vezérlését (PFC). A PFC megadása szükséges az adatközpont-áthidalás (DCB) használata esetén. Mivel a DCB használható mind a RoCE, mind a iWARP RDMA-forgatókönyvben, minden esetben 802.1 Qbb szükséges. Legalább három szolgáltatási (CoS) prioritásra van szükség a kapcsolók vagy a portok sebességének visszalépése nélkül. Ezeknek a forgalmi osztályoknak legalább egy veszteségmentes kommunikációt kell biztosítania.

### <a name="standard-ieee-8021qaz"></a>Standard: IEEE 802.1 Qaz

Az Ethernet-kapcsolóknak meg kell felelniük az IEEE 802.1 Qaz specifikációnak, amely meghatározza a bővített átvitel kiválasztása (ETS) beállítást. Az ETS-t a DCB használata esetén kell megadni. Mivel a DCB használható mind a RoCE, mind a iWARP RDMA-forgatókönyvben, minden esetben 802.1 Qaz szükséges. A kapcsolók vagy a portok sebességének visszalépése nélkül legalább három CoS-prioritás szükséges.

> [!NOTE]
> A Hyper-konvergens infrastruktúra nagy mértékben támaszkodik East-West 2. rétegbeli kommunikációra ugyanazon a racken belül, ezért az ETS szükséges. A Microsoft nem teszteli a Azure Stack HCI-t differenciált szolgáltatási kóddal (DSCP). 

### <a name="standard-ieee-8021ab"></a>Standard: IEEE 802.1 AB

Az Ethernet-kapcsolóknak meg kell felelniük az IEEE 802.1 AB specifikációnak, amely meghatározza a link Layer Discovery protokollt (LLDP). LLDP szükséges a Azure Stack HCI-hez, és támogatja az összevonást a kapcsolók konfigurációjának felderítéséhez.

A LLDP-típus (TLVs) konfigurációjának dinamikusan engedélyezettnek kell lennie. Ezek a kapcsolók nem igényelnek további konfigurálást egy adott TLV-es konfiguráción túl. Például, ha a 3. altípust engedélyezi, a 3-as érték automatikusan Hirdessen minden olyan VLAN-t, 802,1 amely elérhető a kapcsoló portjain

### <a name="custom-tlv-requirements"></a>Egyéni TLV-követelmények

A LLDP lehetővé teszi, hogy a szervezetek definiálják és kódolják saját egyéni TLVs. Ezeket Szervezetian meghatározott TLVs nevezzük. Minden Szervezetian megadott TLVs LLDP TLV típusú, 127-as értékkel kezdődik. Az alábbi táblázat bemutatja, hogy mely szervezeti specifikus egyéni TLV (TLV Type 127) altípusok szükségesek:

|Szükséges verzió|Szervezet|TLV altípus|
|-----|-----|-----|-----|
|20H2 és újabb verziók|IEEE 802,1|VLAN neve (altípus = 3)|
|20H2 és újabb verziók|IEEE 802,3|Keret maximális mérete (altípus = 4)|

## <a name="network-traffic-and-architecture"></a>Hálózati forgalom és architektúra

Ez a szakasz elsősorban a hálózati rendszergazdák számára érhető el.

Azure Stack HCI különböző adatközpont-architektúrákban működhet, beleértve a kétrétegű (gerinc-Leaf) és a 3 szintű (Core-aggregációs-Access) funkciókat. Ez a szakasz a Spine-Leaf topológiával kapcsolatos olyan fogalmakat tartalmaz, amelyeket általában a Hyper-konvergens infrastruktúrában, például a Azure Stack HCI-ben használt munkaterhelésekhez használnak.

## <a name="network-models"></a>Hálózati modellek

A hálózati forgalom a saját irányával osztályozható. A hagyományos Tárolóhálózati környezetek nagy mértékben North-South, ahol a forgalom a számítási szintről a tárolási rétegre áramlik egy 3. rétegbeli (IP-) határon keresztül. A hiperkonvergens-infrastruktúra nagyobb mértékben East-West, ahol a forgalom jelentős része egy 2. rétegbeli (VLAN-) határon belül marad.

> [!IMPORTANT]
>Kifejezetten azt javasoljuk, hogy egy adott helyen lévő összes fürtcsomópont fizikailag ugyanabban az állványban legyen, és ugyanahhoz a Top-of-rack (ToR) kapcsolókhoz kapcsolódjon.

### <a name="north-south-traffic-for-azure-stack-hci"></a>Azure Stack HCI North-South forgalma

North-South forgalom a következő jellemzőkkel rendelkezik:

- A forgalom egy ToR-kapcsolóról a gerincre vagy a gerincről egy ToR-kapcsolóra áramlik.
- A forgalom elhagyja a fizikai állványt, vagy keresztezi a 3. rétegbeli határt (IP)
- A következőket tartalmazza: felügyelet (PowerShell, Windows felügyeleti központ), számítási (VM) és helyek közötti kiterjesztett fürt forgalma
- Ethernet-kapcsolót használ a fizikai hálózathoz való csatlakozáshoz

### <a name="east-west-traffic-for-azure-stack-hci"></a>Azure Stack HCI East-West forgalma

East-West forgalom a következő jellemzőkkel rendelkezik:

- A forgalom a ToR-kapcsolókon és a 2. rétegbeli határon (VLAN) belül marad
- Magában foglalja a tárolási forgalmat, vagy Élő áttelepítés forgalmat ugyanazon a fürtben lévő csomópontok között, és (ha kifeszített fürtöt használ) ugyanazon a helyen belül
- A következő két szakaszban leírtak szerint Ethernet-kapcsolót (kikapcsolt) vagy közvetlen (kapcsoló nélküli) kapcsolatokat is használhat.

## <a name="using-switches"></a>Kapcsolók használata

North-South forgalomhoz kapcsolók használata szükséges. A Azure Stack HCI számára szükséges protokollokat támogató Ethernet-kapcsoló mellett a legfontosabb szempont a hálózati háló megfelelő méretezése.

Fontos megérteni az Ethernet-kapcsolók által támogatott "nem blokkoló" háló sávszélességet, valamint azt, hogy a hálózat előfizetését a lehető legkisebbre (vagy annak kiiktatására) csökkentse.

Az alhálózatok és a VLAN-ok megfelelő használatával elkerülhetők a gyakori torlódási pontok és a túlfizetések, például az elérési út redundancia érdekében használt [többplatformos hivatkozás-összesítési csoport](https://en.wikipedia.org/wiki/Multi-chassis_link_aggregation_group) .

A hálózati szolgáltatóval vagy a hálózati támogatási csapattal együttműködve gondoskodhat arról, hogy a hálózati kapcsolók megfelelően legyenek méretezve a futtatni kívánt számítási feladatok esetében.

## <a name="using-switchless"></a>A kapcsoló használata

A Azure Stack HCI minden fürt mérete esetében támogatja a nem megfelelő (közvetlen) kapcsolatokat East-West forgalom számára, feltéve, hogy a fürt minden csomópontja redundáns kapcsolattal rendelkezik a fürt minden csomópontja számára. Ezt nevezzük "teljes háló"-nek.

> [!NOTE]
>A leváltott központi telepítések előnyeit a három csomópontnál nagyobb méretű fürtök csökkentik a szükséges hálózati adapterek száma miatt.

### <a name="advantages-of-switchless-connections"></a>A kapcsolókkal létesített kapcsolatok előnyei

- East-West forgalomhoz nincs szükség kapcsolók megvásárlására. North-South adatforgalomhoz kapcsoló szükséges. Ez csökkentheti a CAPEX költségeket, azonban a fürt csomópontjainak számától függ.
- Mivel nincs kapcsoló, a konfiguráció a gazdagépre korlátozódik, ami csökkentheti a szükséges konfigurációs lépések lehetséges számát. Ez az érték csökkenti a fürt méretének növekedését.

### <a name="disadvantages-of-switchless-connections"></a>A kapcsolókkal való kapcsolat hátránya

- Ahogy a fürt csomópontjainak száma nő, a hálózati adapterek díja túllépheti a hálózati kapcsolók használatának költségeit.
- Az IP-és alhálózat-címzési sémák esetében további tervezésre van szükség.
- A csak a helyi tárolók elérését biztosítja. A virtuális gép forgalma, a felügyeleti forgalom és a North-South hozzáférést igénylő egyéb forgalom nem használhatja ezeket az adaptereket.
- Általában a három csomópontos fürtökön kívül nem méretezhető.

## <a name="next-steps"></a>További lépések

- A hálózati adapter és a gazdagép követelményeinek megismerése. Lásd a [gazdagép hálózati követelményeit](physical-network-requirements.md).
- A feladatátvételi fürtszolgáltatás alapjai. Lásd: a [feladatátvételi fürtszolgáltatás hálózatkezelésének alapjai](https://techcommunity.microsoft.com/t5/failover-clustering/.failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09).
- Ecset a SET használatával. Tekintse [meg a távoli közvetlen memória-hozzáférés (RDMA) című témakört, és kapcsolja be a beágyazott összevonást (set)](https://docs.microsoft.com/windows-server/virtualization/.hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).
- A telepítéssel kapcsolatban lásd: [fürt létrehozása a Windows felügyeleti központból](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster).
- A telepítéshez lásd: [fürt létrehozása a Windows PowerShell használatával](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell).