---
title: Windows N szintű alkalmazás Azure Stack hub-on SQL Server
description: Megtudhatja, hogyan futtathat egy Windows N szintű alkalmazást Azure Stack hubhoz a SQL Server használatával.
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: b543a94c75ac50c7b0e75f5635956093340b970d
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2021
ms.locfileid: "97973579"
---
# <a name="windows-n-tier-application-on-azure-stack-hub-with-sql-server"></a>Windows N szintű alkalmazás Azure Stack hub-on SQL Server

Ez a hivatkozási architektúra bemutatja, hogyan helyezhet üzembe virtuális gépeket (VM) és egy [N szintű](/azure/architecture/guide/architecture-styles/n-tier) alkalmazáshoz konfigurált virtuális hálózatot az adatrétegen a Windows SQL Server használatával. 

## <a name="architecture"></a>Architektúra

Az architektúra a következő összetevőket tartalmazza.

![A diagram egy olyan virtuális hálózatot mutat be, amely hat alhálózatot tartalmaz: Application Gateway, felügyelet, webes réteg, üzleti réteg, adatréteg és Active Directory. Az adatcsomag alhálózata Felhőbeli tanúsító használ. Három terheléselosztó van.](./media/iaas-architecture-windows-sql-n-tier/image1.png)

## <a name="general"></a>Általános kérdések

-   **Erőforráscsoport**. Az [erőforráscsoportok](/azure/azure-resource-manager/resource-group-overview) az Azure-erőforrások csoportosítására használhatók, így élettartamuk, tulajdonosuk vagy egyéb feltételek szerint kezelhetők.

-   **Rendelkezésre állási csoport.** A rendelkezésre állási csoport egy adatközpont-konfiguráció, amely a virtuális gépek redundanciát és rendelkezésre állását biztosítja. A Azure Stack hub-bélyegzőn belüli konfiguráció biztosítja, hogy a tervezett vagy nem tervezett karbantartási események során legalább egy virtuális gép elérhető legyen. A virtuális gépek olyan rendelkezésre állási csoportba kerülnek, amely több tartalék tartományba (Azure Stack hub gazdagépre) terjed ki.

## <a name="networking-and-load-balancing"></a>Hálózatkezelés és terheléselosztás

-   **Virtuális hálózat és alhálózatok**. Minden Azure-beli virtuális gép üzembe helyezése egy, az alhálózatokra szegmentált virtuális hálózatba történik. Hozzon létre egy külön alhálózatot minden egyes szinthez.

-   **7. rétegbeli Load Balancer.** Mivel a Application Gateway még nem érhető el az Azure stack hub-on, a [Azure stack hub piacon](../operator/azure-stack-marketplace-azure-items.md) elérhető alternatívák is rendelkezésre állnak, például: [Kemp Loadmaster Load Balancer ADC Content Switch](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure) /  [F5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) vagy [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Terheléselosztó.** A [Azure Load Balancer ](/azure/load-balancer/load-balancer-overview)használatával terjesztheti a webes szinten lévő hálózati forgalmat az üzleti szintjére, valamint az üzleti szintjétől a SQL Serverig.

-   **Hálózati biztonsági csoportok** (NSG). A NSG használata a virtuális hálózaton belüli hálózati forgalom korlátozására. Az itt látható háromrétegű architektúrában például az adatbázis-réteg nem fogadja el a webes kezelőfelületről érkező forgalmat, csak az üzleti rétegből és a felügyeleti alhálózatból.

-   **DNS**. Azure Stack hub nem biztosítja a saját DNS-üzemeltetési szolgáltatását, ezért használja a DNS-kiszolgálót a HOZZÁADÁShoz.

**Virtuális gépek**

-   **SQL Server always on rendelkezésre állási csoport**. Magas rendelkezésre állást biztosít az adatszinten a replikáció és a feladatátvétel engedélyezésével. A feladatátvételhez a Windows Server feladatátvételi fürt (WSFC) technológiáját használja.

-   **(AD DS) Active Directory Domain Services-kiszolgálók** A feladatátvevő fürt és a hozzá tartozó fürtözött szerepkörök számítógép-objektumai a Active Directory tartományi szolgáltatásokban (AD DS) jönnek létre. Az azonos virtuális hálózatban lévő virtuális gépeken AD DS-kiszolgálók beállítása előnyben részesített módszer a más virtuális gépekhez való csatlakozásra AD DS. A virtuális gépeket a meglévő vállalati AD DShoz is csatlakoztathatja, ha VPN-kapcsolattal csatlakozik a vállalati hálózathoz. Mindkét módszer esetében módosítania kell a virtuális hálózat DNS-jét a AD DS DNS-kiszolgálóra (virtuális hálózaton vagy meglévő vállalati hálózatban) a AD DS tartomány teljes tartománynevének feloldásához.

-   **Felhőbeli tanúsító**. A feladatátvevő fürtök több mint felet igényelnek a csomópontok futtatásához, amely kvórumnak ismert. Ha a fürt csak két csomóponttal rendelkezik, a hálózati partíciók az egyes csomópontok esetében úgy gondolják, hogy ez a fő csomópont. Ebben az esetben szükség van egy *tanúra* a kapcsolatok megszakításához és a kvórum létrehozásához. A tanúsító egy olyan erőforrás, például egy megosztott lemez, amely döntetlen-MEGSZAKÍTÓKÉNT működhet kvórum létrehozásához. A Felhőbeli tanúsító olyan tanúsító típus, amely az Azure Blob Storage-t használja. A kvórum fogalmával kapcsolatos további tudnivalókért tekintse meg a [fürt és a készlet Kvórumának ismertetése](/windows-server/storage/storage-spaces/understand-quorum)című témakört. A Felhőbeli tanúsító szolgáltatással kapcsolatos további információkért lásd: [Felhőbeli tanúsító üzembe helyezése feladatátvevő fürtön](/windows-server/failover-clustering/deploy-cloud-witness). Azure Stack központban a Felhőbeli tanúsító végpont különbözik a globális Azure-tól. 

A következőhöz hasonló lehet:

- Globális Azure esetén:  
  `https://mywitness.blob.core.windows.net/`

- Azure Stack hub esetében:  
  `https://mywitness.blob.<region>.<FQDN>`

-   **Jumpbox**. Más néven [bástyagazdagép](https://en.wikipedia.org/wiki/Bastion_host). A hálózaton található biztonságos virtuális gép, amelyet a rendszergazdák a többi virtuális géphez való kapcsolódásra használnak. A jumpbox olyan NSG-vel rendelkezik, amely csak a biztonságos elemek listáján szereplő nyilvános IP-címekről érkező távoli forgalmat engedélyezi. Az NSG-nek engedélyeznie kell a távoli asztali (RDP) forgalmat.

## <a name="recommendations"></a>Javaslatok

Az Ön követelményei eltérhetnek az itt leírt architektúrától. Ezeket a javaslatokat tekintse kiindulópontnak.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

A virtuális gépek konfigurálásával kapcsolatos javaslatokért lásd: [Windows rendszerű virtuális gép futtatása Azure stack hub-on](iaas-architecture-vm-windows.md).

### <a name="virtual-network"></a>Virtuális hálózat

A virtuális hálózat létrehozásakor határozza meg, hogy hány IP-cím szükséges az egyes alhálózatokban lévő erőforrásokhoz. A szükséges IP-címekhez a [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) -jelölés használatával egy alhálózati maszkot és egy hálózati címtartományt kell megadni. Használjon a szabványos [magánhálózati IP-címblokkokba](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces) eső címterületet. Ezek az IP-címblokkok a következők: 10.0.0.0/8, 172.16.0.0/12 és 192.168.0.0/16.

Olyan címtartományt válasszon, amely nincs átfedésben a helyszíni hálózattal, ha később be kell állítania egy átjárót a virtuális hálózat és a helyszíni hálózat között. A virtuális hálózat létrehozása után a címtartomány nem módosítható.

Az alhálózatokat a funkciók és a biztonsági követelmények szem előtt tartásával tervezze meg. Minden ugyanazon szinthez vagy szerepkörhöz tartozó virtuális gépnek egyazon alhálózaton kell lennie. Ez az alhálózat biztonsági korlátot is képezhet. A virtuális hálózatok és alhálózatok tervezésével kapcsolatos további információkért lásd: [Azure Virtual Networks tervezése és kialakítása](/azure/virtual-network/virtual-network-vnet-plan-design-arm).

### <a name="load-balancers"></a>Terheléselosztók

Ne tegye elérhetővé a virtuális gépeket közvetlenül az internethez, hanem adjon hozzá minden virtuális gépet egy magánhálózati IP-címhez. Az ügyfelek a 7. rétegbeli Load Balancerhoz társított nyilvános IP-cím használatával csatlakoznak.

Adja meg a terheléselosztó a virtuális gépek felé irányuló közvetlen hálózati forgalomra vonatkozó szabályait. Ha például engedélyezni szeretné a HTTP-forgalmat, a 80-as portot az előtér-konfigurációból a 80-es portra a háttér-címkészlet esetében. Amikor egy ügyfél HTTP-kérelmet küld a 80-as port felé, a terheléselosztó kiválaszt egy háttérbeli IP-címet egy [kivonatoló algoritmus](/azure/load-balancer/concepts#limitations) használatával, amely tartalmazza a forrás IP-címét. Az ügyfelek kérései a háttérbeli címkészlet összes virtuális gépe között oszlanak meg.

### <a name="network-security-groups"></a>Hálózati biztonsági csoportok

A szintek közötti forgalmat NSG-szabályokkal korlátozhatja. A fentiekben bemutatott háromrétegű architektúrában a webes réteg nem kommunikál közvetlenül az adatbázis szintjével. A szabály betartatásához az adatbázis-szinten le kell tiltani a webes szintű alhálózatról érkező bejövő forgalmat.

1.  A virtuális hálózatról érkező összes bejövő forgalom megtagadása. (Használja a szabály VIRTUAL_NETWORK címkéjét.)

2.  Az üzleti szintű alhálózatról érkező bejövő forgalom engedélyezése.

3.  Engedélyezze a bejövő forgalmat az adatbázis-rétegek alhálózatán. Ez a szabály lehetővé teszi az adatbázis-alapú virtuális gépek közötti kommunikációt, amely az adatbázis-replikációhoz és a feladatátvételhez szükséges.

4.  Engedélyezze az RDP-forgalmat (3389-es port) a Jumpbox alhálózaton. Ez lehetővé teszi, hogy a rendszergazdák csatlakozni tudjanak az adatbázisszinthez a jumpboxból.

Hozzon létre a 2 – 4. szabályt magasabb prioritással, mint az első szabály, ezért bírálják felül.

## <a name="sql-server-always-on-availability-groups"></a>SQL Server Always On rendelkezésre állási csoportok

Magas rendelkezésre állású SQL Server esetén az [Always On rendelkezésre állási csoportok](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-ver15&preserve-view=true) használatát javasoljuk. A Windows Server 2016-nál régebbi kiadásokban az Always On rendelkezésre állási csoportok tartományvezérlőt igényelnek, és a rendelkezésre állási csoport összes csomópontjának azonos AD-tartományban kell lennie.

A virtuálisgép-réteg magas rendelkezésre állása esetén minden SQL virtuális gépnek rendelkezésre állási csoportnak kell lennie.

A többi szint egy [rendelkezésre állási csoport figyelőjén](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover?view=sql-server-ver15&preserve-view=true) keresztül csatlakozik az adatbázishoz. A figyelő lehetővé teszi, hogy az SQL-ügyfél anélkül csatlakozzon, hogy ismerné az SQL-kiszolgáló fizikai példányának nevét. Az adatbázishoz hozzáférő virtuális gépeket csatlakozni kell a tartományhoz. Az ügyfél (ebben az esetben egy másik szint) DNS használatával oldja fel a figyelő virtuális hálózati nevét IP-címekké.

A SQL Server Always On rendelkezésre állási csoportot az alábbiak szerint konfigurálja:

1.  Hozzon létre egy Windows Server feladatátvételi fürtszolgáltatási (WSFC-) fürtöt, egy SQL Server Always On rendelkezésre állási csoportot és egy elsődleges replikát. További információ: [Ismerkedés az Always On rendelkezésre állási csoportokkal](/sql/database-engine/availability-groups/windows/getting-started-with-always-on-availability-groups-sql-server?view=sql-server-ver15&preserve-view=true).

2.  Hozzon létre egy belső terheléselosztót statikus magánhálózati IP-címmel.

3.  Hozzon létre egy figyelőt a rendelkezésre állási csoporthoz, és rendelje hozzá a figyelő DNS-nevét a belső terheléselosztó IP-címéhez.

4.  Hozzon létre egy terheléselosztó szabályt az SQL Server figyelőportjához (alapértelmezés szerint az 1433-as TCP-port). A terheléselosztó szabálynak engedélyeznie kell a *nem fix IP-címek* használatát, más néven a közvetlen kiszolgálói választ. Ezáltal a virtuális gép közvetlenül válaszol az ügyfélnek, és közvetlen kapcsolatot hozható létre az elsődleges replikával.

> [!NOTE]
> Ha a nem fix IP engedélyezve van, az előtérbeli portszámnak egyeznie kell a terheléselosztó szabály háttérbeli portszámával.

Ha egy SQL-ügyfél csatlakozni próbál, a terheléselosztó továbbítja az elsődleges replikának a kapcsolódási kérelmet. Ha van feladatátvétel egy másik replikára, a terheléselosztó automatikusan új elsődleges replikára irányítja az új kéréseket. További információ: [ILB-figyelő konfigurálása SQL Server Always On rendelkezésre állási csoportokhoz](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

A feladatátvétel során a meglévő ügyfélkapcsolatok lezárulnak. A feladatátvétel befejezése után a rendszer az új elsődleges replikára irányítja az új kapcsolatokat.

Ha az alkalmazás több olvasási műveletet tesz lehetővé, mint az írás, a csak olvasható lekérdezések egy részét kiszervezheti egy másodlagos replikára. Lásd: [Csak olvasási másodlagos replikához való csatlakozás figyelővel (csak olvasási útválasztás)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover?view=sql-server-ver15&preserve-view=true#ConnectToSecondary).

[Kényszerítse](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-ver15&preserve-view=true) a rendelkezésre állási csoport manuális feladatátvételét az üzemelő példány teszteléséhez.

Az SQL-teljesítmény optimalizálása [érdekében a Azure stack hub teljesítményének optimalizálásához az SQL Server ajánlott eljárásait](./azure-stack-sql-server-vm-considerations.md)is megtekintheti.

**Jumpbox**

Ne engedélyezze az RDP-hozzáférést a nyilvános internetről az alkalmazás számítási feladatait futtató virtuális gépekre. Ehelyett ezeknek a virtuális gépeknek az összes RDP-elérését át kell lépnie a Jumpbox. A rendszergazda először bejelentkezik a jumpboxba, majd azon keresztül bejelentkezik a többi virtuális gépbe. A jumpbox engedélyezi az internetről érkező RDP-forgalmat, de csak az ismert, biztonságos IP-címekről.

A Jumpbox minimális teljesítménnyel kapcsolatos követelményekkel rendelkezik, ezért válassza ki a kis méretű virtuális gép méretét. Hozzon létre egy [nyilvános IP-címet](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) a jumpbox számára. Helyezze a Jumpbox ugyanabban a virtuális hálózatban, mint a többi virtuális gépet, de egy különálló felügyeleti alhálózaton.

A Jumpbox biztonságossá tételéhez adjon hozzá egy olyan NSG-szabályt, amely csak a biztonságos nyilvános IP-címekről engedélyezi az RDP-kapcsolatokat. Állítsa be az NSG-t a többi alhálózathoz is úgy, hogy engedélyezzék a felügyeleti alhálózatból érkező RDP-forgalmat.

## <a name="scalability-considerations"></a>Méretezési szempontok

### <a name="scale-sets"></a>Méretezési csoportok

A webes és az üzleti szintek esetében érdemes lehet [virtuálisgép-méretezési csoportokat](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview) használni a különálló virtuális gépek üzembe helyezése helyett. A méretezési csoport megkönnyíti az azonos virtuális gépek készletének üzembe helyezését és kezelését. Ha gyorsan ki szeretné bővíteni a virtuális gépeket, vegye figyelembe a méretezési csoportokat.

A méretezési csoportokban üzembe helyezett virtuális gépek konfigurálásának két alapvető módja van:

-   A bővítmények használatával konfigurálhatja a virtuális gépet a telepítés után. Ezzel a módszerrel az új virtuálisgép-példányok indítása több időt vehet igénybe, mint a bővítmények nélküli virtuális gépeké.

-   Üzembe helyezhet egy [felügyelt lemezt](./azure-stack-managed-disk-considerations.md) egy egyéni rendszerképpel. Ez a módszer gyorsabban kivitelezhető. Azonban a rendszerképek naprakészen tartása szükséges.

További információ: [tervezési szempontok a méretezési csoportokhoz](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview). Ez a kialakítási szempont általában a Azure Stack hub esetében igaz, de vannak bizonyos figyelmeztetések:

-   A Azure Stack hub virtuálisgép-méretezési csoportjai nem támogatják a túlzott kiépítést vagy a működés közbeni frissítést.

-   Azure Stack hub-beli virtuálisgép-méretezési csoportok nem méretezhetők át.

-   Javasoljuk, hogy felügyelt lemezeket használjon Azure Stack hub-on a virtuálisgép-méretezési csoport nem felügyelt lemezei helyett

-   Jelenleg egy 700 virtuális gép korlátja van Azure Stack hub-ra, amely az összes Azure Stack hub-infrastruktúra, az egyes virtuális gépek és a méretezési csoport példányainak fiókja.

## <a name="subscription-limits"></a>Előfizetés korlátai

Minden Azure Stack hub-bérlői előfizetés alapértelmezett korláttal rendelkezik, beleértve az Azure Stack hub-operátor által konfigurált régiónként engedélyezett maximális számú virtuális gépet. További információ: [Azure stack hub-szolgáltatások, csomagok, ajánlatok, előfizetések áttekintése](../operator/service-plan-offer-subscription-overview.md). Tekintse meg az [Azure stack hub kvótáinak típusait](../operator/azure-stack-quota-types.md)is.

## <a name="security-considerations"></a>Biztonsági szempontok

A virtuális hálózatok forgalomelkülönítési határok az Azure-ban. Alapértelmezés szerint az egyik virtuális hálózatban lévő virtuális gépek nem tudnak közvetlenül kommunikálni egy másik virtuális hálózatban lévő virtuális gépekkel.

**NSG**. [Hálózati biztonsági csoportok](/azure/virtual-network/virtual-networks-nsg) (NSG) használata az internetre irányuló és onnan érkező forgalom korlátozására. További információ: [A Microsoft felhőszolgáltatásai és hálózati biztonság](/azure/best-practices-network-security).

**DMZ**. Érdemes lehet hozzáadnia egy hálózati virtuális berendezést (network virtual appliance, NVA), hogy DMZ-t lehessen létrehozni az internet és az Azure-beli virtuális hálózat között. Az NVA egy általános kifejezés egy olyan virtuális berendezésre, amely hálózatokhoz kapcsolódó feladatokat lát el, például gondoskodik a tűzfalról, a csomagvizsgálatról, a naplózásról és az egyéni útválasztásról.

**Titkosítás**. Titkosítsa a bizalmas adatokat, és a [Azure stack Hub Key Vault](./azure-stack-key-vault-manage-portal.md) használatával kezelheti az adatbázis-titkosítási kulcsokat. További információkért lásd: [Configure Azure Key Vault Integration for SQL Server on Azure VMs](/azure/azure-sql/virtual-machines/windows/azure-key-vault-integration-configure) Az Azure Key Vault-integráció konfigurálása az SQL Serverhez Azure virtuális gépeken. Javasoljuk továbbá, hogy az alkalmazás-titkokat, például az adatbázis-kapcsolódási karakterláncokat a Key Vault tárolja.

## <a name="next-steps"></a>Következő lépések

- Az Azure Cloud Patterns szolgáltatással kapcsolatos további információkért lásd: [Felhőbeli tervezési minták](/azure/architecture/patterns).
