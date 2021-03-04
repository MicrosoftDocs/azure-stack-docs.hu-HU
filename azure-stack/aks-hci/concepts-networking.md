---
title: Fogalmak – hálózatkezelés az Azure Kubernetes Servicesben (ak) Azure Stack HCI-ben
description: Ismerje meg a hálózatkezelést az Azure Kubernetes szolgáltatásban (ak) Azure Stack HCI-n, beleértve a statikus IP-címet és a DHCP hálózatkezelését és a terheléselosztó szolgáltatást.
author: abha
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: abha
ms.custom: fasttrack-edit
ms.openlocfilehash: a8e96783be3228aae525027855c1498b0269bd57
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873812"
---
# <a name="network-concepts-for-deploying-azure-kubernetes-service-aks-on-azure-stack-hci"></a>Hálózati fogalmak az Azure Kubernetes szolgáltatás (ak) üzembe helyezéséhez Azure Stack HCI-ben

Az alkalmazások fejlesztésére szolgáló, tárolón alapuló mikroprocesszorok esetében az alkalmazás-összetevőknek együtt kell működniük a feladataik feldolgozásához. A Kubernetes különböző erőforrásokat biztosít az alkalmazás kommunikációjának engedélyezéséhez. Az alkalmazások belsőleg vagy külsőleg is csatlakoztathatók és elérhetők. A magasan elérhető alkalmazások kiépítéséhez terheléselosztást használhat az alkalmazásaiban. 

Ez a cikk bemutatja azokat az alapvető fogalmakat, amelyek az alkalmazásokban a Azure Stack HCI-ben való hálózatkezelését teszik lehetővé. Ezt követően különböző üzembe helyezési modelleken és példákon keresztül végigvezeti a legjobb hálózati infrastruktúra beállításán a helyszíni AK-beli üzembe helyezéshez.

## <a name="kubernetes-on-azure-stack-hci-basics"></a>A Kubernetes Azure Stack HCI alapjai

Az alkalmazásokhoz való hozzáférés engedélyezéséhez, illetve az alkalmazás-összetevők közötti kommunikációhoz a Kubernetes egy absztrakt réteget biztosít a virtuális hálózat számára. A Kubernetes-csomópontok egy virtuális hálózathoz csatlakoznak, és a hüvelyek számára biztosítanak bejövő és kimenő kapcsolatokat. A *Kube-proxy* összetevő minden csomóponton fut, hogy megadja ezeket a hálózati szolgáltatásokat.

A Kubernetes-ben a *szolgáltatások* logikailag csoportosítják a hüvelyeket, amelyek lehetővé teszik a közvetlen hozzáférést egy IP-cím vagy DNS-név és egy adott porton keresztül. A forgalom *a terheléselosztó használatával is terjeszthető.* 

Az Azure Stack HCI platformmal a Azure Stack HCI-fürtökön az AK virtuális hálózatkezelését is leegyszerűsítheti. Ha Azure Stack HCI-ben hoz létre egy fürtöt az AK-ban, létrejön egy mögöttes `HAProxy` terheléselosztó-erőforrás. A Kubernetes-fürtökön futó alkalmazások létrehozásakor az IP-címek konfigurálva vannak a Kubernetes-szolgáltatásokhoz.

## <a name="aks-on-azure-stack-hci-resources"></a>AK Azure Stack HCI-erőforrásokon 

Az alkalmazások számítási feladatainak hálózati konfigurációjának leegyszerűsítése érdekében az Azure Stack HCI-ben lévő ak az IP-címeket az üzemelő példány következő objektumaihoz rendeli hozzá: 

- **Kubernetes-fürt API-kiszolgálója** – az API-kiszolgáló a Kubernetes-vezérlési sík olyan összetevője, amely a Kubernetes API-t teszi elérhetővé. Az API-kiszolgáló a Kubernetes-vezérlő síkja eleje. Az API-kiszolgálók mindig statikus IP-címeket foglalnak le, a mögöttes hálózati modelltől függetlenül.

- **Kubernetes-csomópontok (virtuális gépeken)** – a Kubernetes-fürtök olyan munkavégző gépekből állnak, amelyek egy csomópontnak nevezett, tárolóba állított alkalmazásokat futtatnak. Minden fürt rendelkezik legalább egy feldolgozói csomóponttal. A Kubernetes-csomópontok a virtuális gépeken belül futnak a Azure Stack HCI-ben. Ezek a virtuális gépek a Azure Stack HCI-fürtön lesznek létrehozva. 

- **Kubernetes Services** – a Kubernetes-ben a *szolgáltatások* logikailag csoportosítják a közvetlen hozzáférést egy IP-cím vagy DNS-név és egy adott porton keresztül. A forgalom *a terheléselosztó használatával is terjeszthető.* A Kubernetes Services mindig statikus IP-címeket foglal le, függetlenül az alapul szolgáló hálózati modelltől.

- **HAProxy Load Balancer** – a HAProxy (magas rendelkezésre állású proxy) egy TCP/http terheléselosztó és proxykiszolgáló, amely lehetővé teszi, hogy a webkiszolgáló több végponton is terjesszen a bejövő kérelmeket. Az alHAProxyon-HCI minden munkaterhelés-fürtje egy saját speciális virtuális gépen futó terheléselosztó.

- **Microsoft helyszíni Cloud Service** – ez az Azure stack HCI Cloud Provider, amely helyszíni Azure stack HCI-fürtön futtatja a Kubernetes-t. A hálózati modell, majd az Azure Stack HCI-fürt meghatározza az objektum IP-címének kiosztási módszerét.


## <a name="virtual-networks"></a>Virtuális hálózatok

Azure Stack HCI-ben található AK-ban a virtuális hálózatok IP-címeket foglalnak le az azokat igénylő Kubernetes-erőforrásokhoz a fent felsoroltak szerint. Két hálózati modell közül választhat, attól függően, hogy a kívánt AK-t Azure Stack HCI hálózati architektúrán szeretné-e kiválasztani. Vegye figyelembe, hogy a Azure Stack HCI-környezetekben az AK-ra vonatkozóan meghatározott virtuális hálózati architektúra eltér az adatközpontban található mögöttes fizikai hálózati architektúrától.

- Statikus IP-hálózatkezelés – a virtuális hálózat statikus IP-címeket foglal le a Kubernetes-fürt API-kiszolgálójára, a Kubernetes-csomópontokra, a mögöttes virtuális gépekre, a terheléselosztó és a fürtön futtatott bármely Kubernetes-szolgáltatásra.

- DHCP-hálózatkezelés – a virtuális hálózat dinamikus IP-címeket foglal le a Kubernetes-csomópontok, a mögöttes virtuális gépek és a terheléselosztó számára egy DHCP-kiszolgáló használatával. A Kubernetes-fürt API-kiszolgálója és a fürt tetején futtatott bármely Kubernetes-szolgáltatás továbbra is statikus IP-címeket foglal le.

### <a name="virtual-ip-pool"></a>Virtuális IP-készlet
A virtuális IP-cím (VIP) készlete kötelező a (z) Azure Stack HCI üzembe helyezéséhez tartozó AK-ra vonatkozóan. A VIP-készlet a fenntartott statikus IP-címek tartománya, amelyek IP-címek kiosztására szolgálnak a Kubernetes-fürt API-kiszolgálójára és a Kubernetes-szolgáltatások külső IP-címeire, így garantálva, hogy az alkalmazások mindig elérhetők. A virtuális hálózati modelltől függetlenül ki kell építenie egy VIP-készletet az AK-gazdagép üzembe helyezéséhez. A VIP-készlet IP-címeinek száma az üzemelő példányban futó munkaterhelés-fürtök és a Kubernetes-szolgáltatások, valamint a statikus és a DHCP hálózati modellek számától függ. A VIP-készlet azonban a következő módokon különbözik a hálózati modelltől függően:

- Statikus IP – ha statikus IP-címet használ, győződjön meg arról, hogy a virtuális IP-címek ugyanahhoz az alhálózathoz tartoznak. 
- DHCP – ha a hálózat DHCP-vel van konfigurálva, akkor működnie kell a hálózati rendszergazdával, és le kell foglalnia a VIP-készlethez használni kívánt IP-címtartományt.

### <a name="kubernetes-node-ip-pool"></a>Kubernetes csomópont IP-készlete
A fentiekben leírtaknak megfelelően a Kubernetes-csomópontok speciális virtuális gépeken futnak egy AK-ban Azure Stack HCI üzemelő példányon. A Azure Stack HCI-ben lévő ak az IP-címeket a Kubernetes-csomópontok közötti kommunikációhoz rendeli hozzá a virtuális gépekhez. DHCP hálózati modell esetén nem kell megadnia a Kubernetes Node virtuálisgép-készletet, mert a hálózati DHCP-kiszolgáló dinamikusan lefoglalja a Kubernetes-csomópontok IP-címeit. Statikus IP-alapú hálózati modell esetén meg kell adnia egy Kubernetes-csomópont IP-címkészlet tartományát. Az ebben a tartományban található IP-címek száma a Kubernetes-csomópontok teljes számával függ az AK-gazdagépek és a munkaterhelés Kubernetes-fürtök számára.

### <a name="virtual-network-with-static-ip-networking"></a>Virtuális hálózat statikus IP-hálózattal

Ez a hálózati modell létrehoz egy virtuális hálózatot, amely statikus IP-címeket foglal le az üzemelő példány összes objektumához. A statikus IP-hálózatkezelés használatának előnye, hogy a hosszú élettartamú üzemelő példányok és az alkalmazások számítási feladatainak mindig elérhetőnek kell lenniük. Javasoljuk, hogy hozzon létre egy statikus IP virtuális hálózati modellt az AK-hoz Azure Stack HCI-környezetben.

Statikus IP-konfigurációval rendelkező virtuális hálózat definiálásakor a következő paramétereket kell megadnia:

- Name (név): a virtuális hálózat neve
- AddressPrefix: az alhálózathoz használandó IP-cím előtagja.
- Átjáró: az alhálózat alapértelmezett átjárójának IP-címe.
- DNS-kiszolgáló: az alhálózathoz használandó DNS-kiszolgálókra mutató IP-címek tömbje. Legalább egy és legfeljebb 3 kiszolgáló adható meg.
- Kubernetes Node IP-készlet: a Kubernetes-csomópont virtuális gépei számára használandó IP-címek folyamatos tartománya.
- Virtuális IP-készlet: a Kubernetes-fürt API-kiszolgálójához és a külső IP-címet igénylő Kubernetes-szolgáltatásokhoz használandó IP-címek folyamatos tartománya.
- vLAN-azonosító: a virtuális hálózat vLAN-azonosítója. Ha nincs megadva, a virtuális hálózat nem lesz címkézve.

### <a name="virtual-network-with-dhcp-networking"></a>DHCP-hálózatkezeléssel rendelkező virtuális hálózat

Ez a hálózati modell létrehoz egy virtuális hálózatot, amely dinamikus IP-címeket foglal le az üzemelő példány összes objektumához.  

Statikus IP-konfigurációval rendelkező virtuális hálózat definiálásakor a következő paramétereket kell megadnia:

- Name (név): a virtuális hálózat neve
- Virtuális IP-készlet: a Kubernetes-fürt API-kiszolgálójához és a külső IP-címet igénylő Kubernetes-szolgáltatásokhoz használandó IP-címek folyamatos tartománya.
- vLAN-azonosító: a virtuális hálózat vLAN-azonosítója. Ha nincs megadva, a virtuális hálózat nem lesz címkézve.

## <a name="microsoft-on-premises-cloud-service"></a>Microsoft helyszíni felhőalapú szolgáltatás

A Microsoft helyszíni felhő (MOC) új felügyeleti verem, amely lehetővé teszi a virtuális gépek kezelését Azure Stack HCI-és Windows Server-alapú SDDC-felhőkben. A MOC a következőkből áll:

- egy, a fürtön üzembe helyezett, magasan elérhető szolgáltatás egyetlen példánya `cloud agent` . Ez az ügynök az Azure Stack HCI-fürt egyik csomópontján fut. 
- egy `node agent` fut minden Azure stack HCI fizikai csomóponton. 

Az a `-cloudserviceCIDR` paraméter a `Set-AksHciConfig` virtuális IP-cím hozzárendeléséhez, amely biztosítja, hogy elérhető legyen a felhőalapú ügynök.

A MOC szolgáltatás IP-címének lefoglalása a mögöttes hálózati modelltől függ, amelyet a Azure Stack HCI-fürt üzembe helyezése követ. **A MOC szolgáltatás IP-címének kiosztása független a virtuális hálózati modelltől, és csak a mögöttes fizikai hálózattól függ, amely IP-címeket biztosít az adatközpontban található Azure Stack HCI-fürtcsomópontok számára.**

### <a name="azure-stack-hci-cluster-nodes-with-a-dhcp-based-ip-address-allocation-model"></a>Azure Stack HCI-fürtcsomópontok DHCP-alapú IP-cím-kiosztási modellel

Ha a Azure Stack HCI-csomópontok IP-címet kapnak a fizikai hálózaton található DHCP-kiszolgálóról, akkor nem kell explicit módon megadnia a MOC szolgáltatáshoz tartozó IP-címet.

### <a name="azure-stack-hci-cluster-nodes-with-a-static-ip-allocation-model"></a>HCI-fürtcsomópontok Azure Stack statikus IP-kiosztási modellel 

Ha a Azure Stack HCI-fürtcsomópontok statikus IP-címekhez vannak rendelve, akkor explicit módon meg kell adnia egy IP-címet a MOC Cloud Service számára. A MOC szolgáltatás ezen IP-címének ugyanabban az alhálózatban kell lennie, mint a HCI-fürtcsomópontok Azure Stack. Ha explicit módon szeretne hozzárendelni egy IP-címet a MOC szolgáltatáshoz, használja a `-cloudserviceCIDR` paramétert a `Set-AksHciConfig` parancsban. Győződjön meg arról, hogy az IP-címet CIDR formátumban adja meg, például: "10.11.23.45/16".

## <a name="compare-network-models"></a>Hálózati modellek összehasonlítása

A DHCP és a statikus IP-cím is hálózati kapcsolatot biztosít az AK-hoz Azure Stack HCI-telepítéshez. Vannak azonban előnyei és hátrányai is. Magas szinten a következő szempontokat kell figyelembe venni:

* **DHCP**
    - A nem garantálja a hosszú élettartamú IP-címeket a Azure Stack HCI üzembe helyezéséhez tartozó AK-beli erőforrástípusok esetében.
    - Támogatja a fenntartott DHCP IP-címek kibővítését, ha a központi telepítés a kezdetben vártnál nagyobb értéket kap.
    
* **Statikus IP-cím**
    - Hosszú élettartamú IP-címeket garantál a Azure Stack HCI üzembe helyezéséhez tartozó AK-beli összes erőforráshoz.
    - Mivel nem támogatjuk a Kubernetes csomópont IP-készletének automatikus bővítését, előfordulhat, hogy nem tud új fürtöket létrehozni, ha már kimerítette a Kubernetes-csomópont IP-készletét.


A következő táblázat összehasonlítja a statikus IP-cím és a DHCP hálózati modellek közötti IP-címek kiosztását:

| Képesség                                                                                   | Statikus IP-cím   | DHCP |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Kubernetes-fürt API-kiszolgálója                                             | Statikusan hozzárendelve a VIP-készlettel | Statikusan hozzárendelve a VIP-készlettel |
| Kubernetes-csomópontok (virtuális gépeken)                                                                         | Hozzárendelve a Kubernetes Node IP-készlettel | Dinamikusan kiosztva |
| Kubernetes-szolgáltatások                                          | Statikusan hozzárendelve a VIP-készlettel | Statikusan hozzárendelve a VIP-készlettel |
| HAProxy Load Balancer virtuális gép                                          | Hozzárendelve a Kubernetes Node IP-készlettel | Dinamikusan kiosztva |
| Microsoft helyszíni Cloud Service                                               | A Azure Stack HCI-fürtcsomópontok fizikai hálózati konfigurációjától függ | A Azure Stack HCI-fürtcsomópontok fizikai hálózati konfigurációjától függ |
| VIP-készlet                                             | Kötelező | Kötelező |
| Kubernetes csomópont IP-készlete | Kötelező | Nem támogatott |


## <a name="minimum-ip-address-reservations-for-an-aks-on-azure-stack-hci-deployment"></a>A Azure Stack HCI üzemelő példányok minimális IP-címeinek foglalása egy AK-hoz
A kiépítési modelltől függetlenül a fenntartott IP-címek száma változatlan marad. Ez a szakasz a fenntartott IP-címek számát tárgyalja a Azure Stack HCI-alapú üzemi modellben található AK-ra vonatkozóan.

### <a name="minimum-ip-address-reservation"></a>Minimális IP-cím foglalása

A telepítéshez legalább a következő IP-címeket kell fenntartania:

| Fürt típusa  | Vezérlési sík csomópont | Munkavégző csomópont | Frissítési műveletekhez | Terheléselosztóval  |
| ------------- | ------------------ | ---------- | ----------| -------------|
| AK-gazdagép |  1 IP |  NA  |  2 IP-CÍM |  NA  |
| Munkaterhelés-fürt  |  1 IP/csomópont  | 1 IP/csomópont |  5 IP-CÍM  |  1 IP |

Emellett a VIP-készlethez a következő IP-címek számát kell fenntartania:

| Erőforrás típusa  | IP-címek száma 
| ------------- | ------------------
| Fürt API-kiszolgálója |  1/fürt 
| Kubernetes-szolgáltatások  |  1/szolgáltatás  

Amint láthatja, a szükséges IP-címek száma változó a Azure Stack HCI architektúra és a Kubernetes-fürtön futtatott szolgáltatások számától függően. Javasoljuk, hogy az üzemelő példányhoz összesen 256 IP-címet (/24 alhálózatot) őrizzen meg.

### <a name="walking-through-an-example-deployment"></a>Példa a központi telepítésre

Jane egy informatikai rendszergazda, amely a Azure Stack HCI-ből származó AK-val kezdődik. Két Kubernetes-fürtöt szeretne üzembe helyezni – a Kubernetes-fürt A és A B Kubernetes-fürtöt Azure Stack HCI-fürtön. Egy szavazási alkalmazást is szeretne futtatni a fürt tetején. Ez az alkalmazás 3 példányban fut a két fürtön és a háttér-adatbázis 1 példányán futó előtér-KEZELŐFELÜLETen.

- Az A Kubernetes-fürt 3 vezérlési sík csomóponttal és 5 feldolgozó csomóponttal rendelkezik
- A B Kubernetes-fürt 1 vezérlési sík csomóponttal és 3 feldolgozó csomóponttal rendelkezik
- az előtér-KEZELŐFELÜLET 3 példánya (443-es port)
- 1 a háttérrendszer-adatbázis egy példánya (80-es port)

A fenti táblázat alapján le kell foglalnia a következőket:

- 3 IP-cím az AK-gazdagéphez (1 IP a vezérlési sík csomóponthoz és 2 IP a frissítési műveletek futtatásához)
- 3 IP-cím a vezérlési sík csomópontjaihoz az A fürtben (1 IP/vezérlési sík csomópont)
- 5 IP-cím a munkavégző csomópontok számára az A fürtben (1 IP/vezérlési sík csomópont)
- 6 IP-cím az A fürthöz (5 IP-cím a frissítési műveletek futtatásához és 1 IP a Load Balancerhez)
- 1 IP-cím a vezérlési sík csomópontjaihoz a B fürtben (1 IP/vezérlési sík csomópont)
- 3 IP-cím a munkavégző csomópontok számára a B fürtben (1 IP/vezérlési sík csomópont)
- 6 IP-cím továbbá a B fürthöz (5 IP-cím a frissítési műveletek futtatásához és 1 IP a Load Balancerhez)
- 2 IP-cím a Kubernetes-fürt API-kiszolgálóihoz (1 IP Kubernetes-fürtön)
- 3 IP-cím a Kubernetes szolgáltatáshoz (az előtér-felhasználói felület egy példányának 1 IP-címe, mivel mindegyik ugyanazt a portot használja. A háttér-adatbázis a 3 IP-cím bármelyikét használhatja, mivel az egy másik porton található)

A fentiekben leírtak szerint Jane összesen 32 IP-címet igényel. Jane ezért a virtuális hálózata számára fenntart egy/26 alhálózatot. 

### <a name="splitting-reserved-ip-addresses-based-on-a-static-ip-network-model"></a>Fenntartott IP-címek felosztása statikus IP-hálózati modell alapján

Míg a fenntartott IP-címek teljes száma változatlan marad, a telepítési modell határozza meg, hogy ezek az IP-címek hogyan oszlanak el az IP-csoportok között. Ahogy korábban is beszéltünk, a statikus IP-hálózati modell 2 IP-készlettel rendelkezik:

- Kubernetes Node IP-készlet – a Kubernetes Node virtuális gépek és a terheléselosztó virtuális gép számára. Ez az IP-címkészlet a frissítési műveletek futtatásához szükséges IP-címet is tartalmazza.
- Virtuális IP-készlet – a Kubernetes API-kiszolgáló és a Kubernetes-szolgáltatások számára

A fenti példában Jane-nek tovább kell osztania ezeket az IP-címeket a VIP-készletek és a Kubernetes csomópont IP-készletei között:
- 5 (2 a Kubernetes-fürt API-kiszolgálója és a Kubernetes-szolgáltatások esetében 3) a virtuális IP-címkészlet 32 IP-címei közül.
- 27 (az összes IP-cím a Kubernetes-csomópontok és a mögöttes virtuális gépek, a terheléselosztó virtuális gépei és a frissítési műveletek) számára a Kubernetes-csomópont IP-készletéből.

### <a name="splitting-reserved-ip-addresses-based-on-a-dhcp-network-model"></a>Fenntartott IP-címek felosztása DHCP hálózati modell alapján

Míg a fenntartott IP-címek teljes száma változatlan marad, a telepítési modell határozza meg, hogy ezek az IP-címek hogyan oszlanak el az IP-csoportok között. Ahogy korábban is beszéltünk, a DHCP hálózati modell 1 IP-készlettel rendelkezik:

- Virtuális IP-készlet – a Kubernetes API-kiszolgáló és a Kubernetes-szolgáltatások számára

A fenti példa használata:
- Jane-nek összesen 32 IP-címet vagy egy/26 alhálózatot kell fenntartania a DHCP-kiszolgálón. 
- A VIP-készlet 32 IP-címei közül 5 (2 a Kubernetes-fürt API-kiszolgálója és 3 a Kubernetes-szolgáltatások számára) esetében le kell foglalni. 

## <a name="next-steps"></a>Következő lépések

Ebben a témakörben megtanulta azokat az alapvető fogalmakat, amelyek a Azure Stack HCI-ben az alkalmazásokban hálózatkezelést biztosítanak az alkalmazásaihoz. Következő lépésként a következőket teheti:

- [Proxybeállítások konfigurálása az AK-on Azure Stack HCI-on](./proxy.md)
- [Rendszerkövetelmények](./system-requirements.md)

