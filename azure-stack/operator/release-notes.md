---
title: Azure Stack hub kibocsátási megjegyzései
description: Kibocsátási megjegyzések a Azure Stack hub integrált rendszereihez, beleértve a frissítéseket és a hibajavításokat is.
author: sethmanheim
ms.topic: article
ms.date: 02/18/2021
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: 0d1b3f65f36e3aae5095fc3535f5df6290cb51f7
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840830"
---
# <a name="azure-stack-hub-release-notes"></a>Azure Stack hub kibocsátási megjegyzései

Ez a cikk Azure Stack hub frissítési csomagjainak tartalmát ismerteti. A frissítés az Azure Stack hub legújabb kiadásának javításait és javításait is tartalmazza.

Ha egy másik verzió kibocsátási megjegyzéseit szeretné elérni, a bal oldali tartalomjegyzéknél használja a verziószám legördülő menüt.

::: moniker range=">=azs-2002"
> [!IMPORTANT]  
> Ez a frissítési csomag csak Azure Stack hub integrált rendszerekhez használható. Ezt a frissítési csomagot ne alkalmazza a Azure Stack Development Kitra (ASDK).
::: moniker-end
::: moniker range="<azs-2002"
> [!IMPORTANT]  
> Ha a Azure Stack hub-példánya több mint két frissítés mögött van, akkor az nem felel meg a megfelelőségnek. A [támogatás fogadásához legalább a támogatott verzióra kell frissítenie](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Frissítés tervezése

A frissítés alkalmazása előtt mindenképpen tekintse át a következő információkat:

- [A frissítés alkalmazása előtti és utáni tevékenységek ellenőrzőlistája](release-notes-checklist.md)
- [Ismert problémák](known-issues.md)
- [Gyorsjavítások](#hotfixes)
- [Biztonsági frissítések](release-notes-security-updates.md)

A frissítésekkel és a frissítési folyamattal kapcsolatos segítségért lásd: [a Azure stack hub javításával és frissítésével kapcsolatos hibák elhárítása](azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>A frissítés letöltése

Az Azure Stack hub frissítési csomagot [az Azure stack hub Update Downloader Tool](https://aka.ms/azurestackupdatedownload)használatával töltheti le.

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2008"
## <a name="2008-build-reference"></a>2008-Build referenciája

Az Azure Stack hub 2008 Update Build száma **1.2008.13.88**.

### <a name="update-type"></a>Frissítéstípus

Az Azure Stack hub 2008 Update Build típusa **megtelt**.

Az 2008-es frissítési csomag mérete nagyobb a korábbi frissítésekhez képest. A megnövelt méret a letöltendő időt eredményezi. A frissítés hosszú ideig az **előkészítési** állapotban marad, és a kezelők a korábbi frissítéseknél hosszabb időt vehetnek igénybe. Az 2008-es frissítés a belső tesztelési környezet következő várt futtatókörnyezeteit használta: 13-20 óra, 8 csomópont: 16-26 óra, 12 csomópont: 19-32 óra, 16 csomópont: 22-38 óra. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek. A várt értéknél rövidebb vagy hosszabb futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack hub-operátorok számára, kivéve, ha a frissítés meghiúsul. Ez a futtatókörnyezeti közelítés az 2008-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack hub-frissítésekkel.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack hub-ban](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->
- Azure Stack hub mostantól támogatja a VNET-társítást, amely hálózati virtuális berendezés (NVA) nélkül képes csatlakozni a virtuális hálózatok-hez. További információkért tekintse meg az [új VNET](../user/virtual-network-peering.md)-társítási dokumentációt.
- Azure Stack hub blob Storage mostantól lehetővé teszi a felhasználók számára, hogy egy nem módosítható blobot használjanak. Ha nem módosítható szabályzatokat állít be egy tárolón, az üzleti szempontból kritikus fontosságú adatobjektumokat egy féreg (egyszer írható, olvasás) állapotba is tárolhatja. Ebben a kiadásban a nem módosítható házirendek csak a REST API vagy az ügyféloldali SDK-k használatával állíthatók be. Ebben a kiadásban nem lehet hozzáfűzni a Blobok írásait is. A nem módosítható Blobokkal kapcsolatos további információkért lásd: [az üzleti szempontból kritikus blob-adatok tárolása a](/azure/storage/blobs/storage-blob-immutable-storage)nem módosítható tárolóval.
- Az Azure Stack hub Storage mostantól támogatja az Azure Storage Services API **2019-07-07**-es verzióját. Az új REST API verziójával kompatibilis Azure-ügyfél kódtárait lásd: [Azure stack hub Storage fejlesztői eszközök](../user/azure-stack-storage-dev.md#azure-client-libraries). Az Azure Storage Services Felügyeleti API-jai esetében a **2018-02-01** -es számú támogatással rendelkezik, és az összes elérhető funkció egy részhalmazát tartalmazza.
- A Azure Stack hub számítási funkciója mostantól támogatja az Azure számítási API-k **2020-06-01**-es verzióját, az összes elérhető funkció egy részhalmazával.
- Azure Stack hub által felügyelt lemezek mostantól támogatják az Azure Disk APIs **2019-03-01**-es verzióját, az elérhető funkciók egy részhalmazával.
- A Windows felügyeleti központ előzetes verziója, amely most már csatlakozhat Azure Stack központhoz, és részletes betekintést nyújt az infrastruktúrába a támogatási műveletek során (az adattörés szükséges).
- Lehetőség a bejelentkezési szalagcím hozzáadására a rendszerjogosultságú végponthoz (PEP) a központi telepítési időpontban.
- Kiadta a több **exkluzív műveleti** szalagcímet, amelyek javítják a rendszeren jelenleg zajló műveletek láthatóságát, és letiltják a felhasználók számára, hogy minden más kizárólagos műveletet kezdeményezzenek (és ezt követően sikertelenek).
- Két új szalagcím bevezetése az egyes Azure Stack hub Marketplace-elemek Product (termék) oldalán. Ha a piactér letöltése sikertelen, a kezelők megtekinthetik a hibák részleteit, és megkísérlik a probléma megoldására a javasolt lépéseket.
- Kiadta a minősítési eszközt az ügyfelek számára a visszajelzések megadásához. Ez lehetővé teszi Azure Stack hub számára a felhasználói élmény mérését és optimalizálását.
- Azure Stack hub ezen kiadása az Azure Kubernetes Service (ak) és a Azure Container Registry (ACR) privát előzetes verzióját tartalmazza. A privát előzetes verzió célja, hogy visszajelzést gyűjtsön a Azure Stack hub-on található AK-ra és ACR-re vonatkozó minőségi, funkciókkal és felhasználói élménysel kapcsolatban.
- Ebben a kiadásban az Azure CNI és a Windows-tárolók nyilvános előzetes verziója szerepel az [AK Engine v 0.55.4](../user/kubernetes-aks-engine-release-notes.md)használatával. Az API-modellben való használatról a következő [példában talál példát a githubon](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-windows.json).
- A [Istio 1,3 üzembe helyezése](https://github.com/Azure/aks-engine/tree/master/examples/service-mesh) mostantól támogatott az [AK Engine v 0.55.4](../user/kubernetes-aks-engine-release-notes.md)által üzembe helyezett fürtökön. További információkért [tekintse meg az itt található utasításokat](../user/kubernetes-aks-engine-service-account.md).
- Mostantól támogatott a [magánhálózatok](https://github.com/Azure/aks-engine/blob/master/docs/topics/features.md#private-cluster) üzembe helyezése az [AK Engine v 0.55.4](../user/kubernetes-aks-engine-release-notes.md)használatával.
- Ez a kiadás támogatja az Azure-beli és Azure Stack hub Key Vault-példányok [Kubernetes-konfigurációs titkainak beszerzését](https://github.com/Azure/aks-engine/blob/master/docs/topics/keyvault-secrets.md#use-key-vault-as-the-source-of-cluster-configuration-secrets) .

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->
- Megvalósított belső figyelés a hálózati vezérlőhöz és a SLB, így a szolgáltatások automatikusan szervizelésre kerülnek, ha azok bármikor leállított állapotba kerülnek.
- Active Directory összevonási szolgáltatások (AD FS) (AD FS) most lekéri az új jogkivonat-aláíró tanúsítványt, miután az ügyfél elforgatta azt a saját AD FS kiszolgálón. A már konfigurált rendszerek új képességének kihasználásához a AD FS integrációt újra kell konfigurálni. További információ: [AD FS identitás integrálása az Azure stack hub-adatközpontba](azure-stack-integrate-identity.md).
- Az indítási és leállítási folyamat változásai az infrastruktúra-szerepkör példányain és azok függőségei a méretezési egység csomópontjain. Ez növeli a Azure Stack hub indítási és leállítási megbízhatóságát.
- A **test-AzureStack** Validation Tool **AzSScenarios** csomagja frissítve lett, hogy a Cloud Service Providers futtassa ezt a Suite-t, hogy az összes ügyfél fiókján engedélyezve legyen a többtényezős hitelesítés.
- A riasztások megbízhatóságának növelése az életciklus-műveletek során felmerülő 29 ügyfél-riasztások felszámolási logikájának hozzáadásával.
- Most már megtekintheti a napló-gyűjtemény részletes HTML-jelentését, amely részletesen ismerteti a naplók szerepköreinek, időtartamának és állapotának részleteit. A jelentés célja, hogy segítséget nyújtson a felhasználóknak a gyűjtött naplók összefoglalásában. A Microsoft ügyfélszolgálati szolgálatai ezután gyorsan felhasználhatják a jelentést a naplófájlok kiértékeléséhez, és segítenek a rendszerproblémák elhárításában és enyhítésében.
- Az infrastruktúra-hibák észlelésének lefedettsége már 7 új figyelő hozzáadásával bővült a felhasználói forgatókönyvek (például a CPU-kihasználtság és a memóriahasználat) között, ami segít a hibák észlelésének megbízhatóságának növelésében.

### <a name="changes"></a>Módosítások

- A **2016-01-01** -es és a **2016-05-01** -es verzióban az **supportHttpsTrafficOnly** Storage-fiók erőforrástípus tulajdonsága engedélyezve lett, de Azure stack hub nem támogatja ezt a tulajdonságot.
- A kötet kapacitása kihasználtsági riasztási küszöbértéke 80% (figyelmeztetés) és 90% (kritikus) – 90% (figyelmeztetés) és 95% (kritikus) értékre nőtt. További információ: [tárolóhelyek riasztásai](azure-stack-manage-storage-shares.md#storage-space-alerts)
- Az AD Graph konfigurációs lépései ebben a kiadásban változnak. További információ: [AD FS identitás integrálása az Azure stack hub-adatközpontba](azure-stack-integrate-identity.md).
- A Windows Server 2019-hez definiált jelenlegi ajánlott eljárásokhoz való igazodás érdekében Azure Stack hub úgy módosul, hogy egy további adatforgalmi osztályt vagy prioritást használ, hogy további különálló kiszolgálót kapjon a kiszolgálók közötti kommunikációhoz a feladatátvételi fürtszolgáltatás-vezérlési kommunikáció támogatásához. A változások eredménye nagyobb rugalmasságot biztosít a feladatátvevő fürt kommunikációjához. Ezt a forgalmi osztályt és sávszélesség-foglalási konfigurációt az Azure Stack hub-megoldás és a Azure Stack hub gazdagépén vagy kiszolgálóin lévő, a rack (ToR) kapcsolóinak változása hajtja végre.

  Vegye figyelembe, hogy ezek a módosítások egy Azure Stack hub rendszer gazdagép szintjén lesznek hozzáadva. Forduljon a SZÁMÍTÓGÉPGYÁRTÓhoz, és gondoskodjon arról, hogy a szükséges módosítások a ToR-hálózati kapcsolókon legyenek. Ez a ToR-változás a 2008-es kiadásra való frissítés előtt vagy a 2008-es frissítés után végezhető el. További információt a [hálózati integráció dokumentációjában](azure-stack-network.md)talál.

- A GPU-t támogató virtuálisgép-méretek **NCas_v4 (NVIDIA T4)** leváltották ebben a buildben a VM-méretekkel **NCasT4_v3**, hogy konzisztensek legyenek az Azure-ban. Vegye figyelembe, hogy ezek a portálon még nem láthatók, és csak Azure Resource Manager-sablonokon keresztül használhatók.

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Kijavított egy problémát, amelyben nem sikerült egy futó virtuális géphez csatlakoztatott hálózati adapter NSG törlése.
- Kijavítva egy olyan problémát, amely egy olyan nyilvános IP-cím **IdleTimeoutInMinutes** -értékének módosítását állítja be, amely egy terheléselosztó társítva a nyilvános IP-címet hibás állapotba helyezi.
- A **Get-AzsDisk** parancsmag kijavítva, hogy a megfelelő **csatolt** állapotot a **OnlineMigration** helyett a csatlakoztatott felügyelt lemezekre állítsa vissza.

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack hub ezen frissítésében található biztonsági frissítésekről: [Azure stack hub biztonsági frissítései](release-notes-security-updates.md).

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack hub rendszeresen bocsát ki gyorsjavításokat. Győződjön meg arról, hogy a legújabb 2005-es gyorsjavítást telepíti a 2008-es frissítés előtt. Emellett a 2005 kiadástól kezdve, amikor új főverzióra frissít (például 1.2005. x – 1.2008. x), a legújabb gyorsjavítások (ha vannak ilyenek a csomag letöltésekor) az új főverzióban automatikusan települnek. Az 2008-es telepítés az összes gyorsjavítással együtt naprakész. Ettől a ponttól kezdve, ha a 2008-es gyorsjavítás megjelent, telepítse azt.

> [!NOTE]
> Azure Stack hub gyorsjavítási kiadásai kumulatívak; a legújabb gyorsjavítást csak akkor kell telepíteni, ha az adott verzióhoz tartozó korábbi gyorsjavítások összes javítását beolvassa.

További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

Azure Stack hub-gyorsjavítások csak Azure Stack hub integrált rendszerekre érvényesek; Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

> [!TIP]
> Ha értesítést szeretne kapni az egyes gyorsjavítási kiadásokról, fizessen elő az [**RSS-hírcsatornára**](https://azurestackhubdocs.azurewebsites.net/xml/hotfixes.rss) , hogy értesítést kapjon az egyes gyorsjavítások kiadásáról.

### <a name="after-successfully-applying-the-2008-update"></a>Az 2008-es frissítés sikeres alkalmazása után

Mivel Azure Stack hub-gyorsjavítások összesítő jellegűek, ajánlott eljárásként telepítenie kell a buildhez kiadott összes gyorsjavítást, hogy biztosítsa a legfontosabb kiadások közötti legjobb frissítési élményt. Amikor új főverzióra frissít (például 1.2005. x – 1.2008. x), a legújabb gyorsjavítások (ha vannak ilyenek a csomag letöltésekor) az új főverzióban automatikusan települnek.

Ha a 2008-es verzió telepítése után a rendszer a 2008-es gyorsjavításokat is felszabadítja, telepítse őket:

- [Azure Stack hub gyorsjavítási 1.2008.26.116](hotfix-1-2008-26-116.md)
::: moniker-end

::: moniker range="azs-2005"
## <a name="2005-build-reference"></a>2005-Build referenciája

Az Azure Stack hub 2005 Update Build száma **1.2005.6.53**.

### <a name="update-type"></a>Frissítéstípus

Az Azure Stack hub 2005 Update Build típusa **megtelt**.

Az 2005-es frissítési csomag mérete nagyobb a korábbi frissítésekhez képest. A megnövelt méret a letöltendő időt eredményezi. A frissítés hosszú ideig az **előkészítési** állapotban marad, és a kezelők a korábbi frissítéseknél hosszabb időt vehetnek igénybe. Az 2005-es frissítés a belső tesztelési környezet következő várt futtatókörnyezeteit használta: 13-20 óra, 8 csomópont: 16-26 óra, 12 csomópont: 19-32 óra, 16 csomópont: 22-38 óra. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek. A várt értéknél rövidebb vagy hosszabb futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack hub-operátorok számára, kivéve, ha a frissítés meghiúsul. Ez a futtatókörnyezeti közelítés az 2005-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack hub-frissítésekkel.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack hub-ban](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->
- Ez a Build a következő 3 új GPU virtuálisgép-típust támogatja: NCv3 (NVIDIA V100), NVv4 (AMD MI25) és NCas_v4 (NVIDIA T4) VM-méretek. A virtuális gépek központi telepítése sikeres lesz azok számára, akik rendelkeznek a megfelelő hardverrel, és az Azure Stack hub GPU előzetes programba vannak beépítve. Ha érdekli, regisztráljon a GPU előzetes programjára a következő címen: https://aka.ms/azurestackhubgpupreview . További információ [:](../user/gpu-vms-about.md).
- Ez a kiadás egy új szolgáltatást biztosít, amely lehetővé teszi egy önálló gyógyító képességet, amely észleli a hibákat, értékeli a hatásokat, és biztonságosan csökkenti a rendszerproblémákat. Ezzel a szolgáltatással a rendszer manuális beavatkozás nélkül is nagyobb rendelkezésre állásra dolgozunk. A 2005-es és újabb verziókban az ügyfelek a riasztások számának csökkenését fogják tapasztalni. Az ebben a folyamatban lévő hibák nem igénylik a Azure Stack hub operátorok beavatkozását, kivéve, ha értesítést kapnak.
- Új lehetőség van a Azure Stack hub felügyeleti portál gapped/leválasztott Azure Stack hub-ügyfelei számára, hogy helyileg mentse a naplókat. A naplókat helyi SMB-megosztásban is tárolhatja, ha Azure Stack hub le van választva az Azure-ból.
- A Azure Stack hub felügyeleti portál mostantól blokkolja bizonyos műveleteket, ha már folyamatban van egy rendszerművelet. Ha például egy frissítés folyamatban van, nem lehet új méretezési egység csomópontot felvenni.
- Ez a kiadás az Azure-ra épülő, a 1910 előtti virtuális gépekkel kapcsolatos további hálós konzisztenciát biztosít. 1910-ben a Microsoft bejelentette, hogy minden újonnan létrehozott virtuális gép a wireserver protokollt fogja használni, amely lehetővé teszi, hogy az ügyfelek ugyanazt a WALA-ügynököt és a Windows-ügynököt használják az Azure-ban, így könnyebben használhatja az Azure images szolgáltatást Azure Stack hub-on Ebben a kiadásban az 1910-nál korábbinál korábban létrehozott virtuális gépeket a rendszer automatikusan áttelepíti a wireserver protokoll használatára. Ez a virtuális gépek és a virtuálisgép-bővítmények üzembe helyezését, valamint az állandó állapotú üzemidő fejlesztését is lehetővé teszi.
- Az Azure Stack hub Storage mostantól támogatja az Azure Storage Services API 2019-02-02-es verzióját. Az Azure ügyféloldali kódtárak esetében, amelyek kompatibilisek az új REST API verziójával. További információ: [Azure stack hub Storage fejlesztői eszközök](../user/azure-stack-storage-dev.md#azure-client-libraries).
- Az Azure Stack hub mostantól támogatja a CreateUiDefinition legújabb verzióját [(2. verzió)](/azure/azure-resource-manager/managed-applications/create-uidefinition-overview).
- Új útmutató a kötegelt virtuális gépek üzembe helyezéséhez. További információkért [tekintse meg ezt a cikket](../operator/azure-stack-capacity-planning-compute.md).
- Az Azure Stack hub Marketplace CoreOS-tároló linuxos eleme a teljes [élettartamot közelíti](https://azure.microsoft.com/updates/flatcar-in-azure/)meg. További információ: [áttelepítés a CoreOS Container Linux rendszerből](https://docs.flatcar-linux.org/os/migrate-from-container-linux/).

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- A Storage infrastruktúra-fürtszolgáltatás naplói és eseményeinek fejlesztése. A Storage infrastruktúra-fürtszolgáltatás naplóit és eseményeit legfeljebb 14 napig őrzi meg a rendszer a jobb diagnosztika és hibaelhárítás érdekében.
- Az Azure Stack hub indításának és leállításának megbízhatóságát növelő újítások.
- A frissítési futtatókörnyezetet a deközpontosító használatával és a függőségek eltávolításával csökkentheti. Az 2002-es frissítéssel összehasonlítva a 4 csomópontos időbélyeg-frissítési idő 15-42 óra és 13-20 óra között csökken. 8 csomópont 20-50 óra és 16-26 óra között csökkent. 12 csomópont 20-60 óra és 19-32 óra között csökkent. 16 csomópont 25-70 óra és 22-38 óra között csökkent. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek.
- A frissítés mostantól leáll, ha van bizonyos Helyreállíthatatlan hiba.
- A frissítési csomag továbbfejlesztett rugalmassága az internetről való letöltéskor.
- Javult a virtuális gép felszabadításának leállítása.
- A hálózati vezérlő gazdagép-ügynökének javított rugalmassága.
- További mezők lettek hozzáadva a syslog-üzenetek CEF hasznos adataihoz a forrás IP-cím és a privilegizált végponthoz és a helyreállítási végponthoz való kapcsolódáshoz használt fiók jelentéséhez. A részletekért lásd: [Azure stack hub integrálása figyelési megoldásokkal a syslog forwarding használatával](azure-stack-integrate-security.md) .
- Windows Defender-események (eseményazonosító: 5001, 5010, 5012) hozzáadása a syslog-ügyfélen keresztül kibocsátott események listájához.
- Riasztások hozzáadása a Azure Stack felügyeleti portálon a Windows Defender szolgáltatással kapcsolatos eseményekhez, a Defender platform és az aláírások verziójának inkonzisztencia-adatainak jelentéséhez, valamint az észlelt kártevők elleni műveletek elmulasztásához.
- A 4 határos eszköz támogatása a Azure Stack hub adatközpontba való integrálásakor.

### <a name="changes"></a>Módosítások

- Eltávolította a leállításhoz, leállításhoz és az infrastruktúra szerepkör-példányának újraindításához szükséges műveleteket a felügyeleti portálról. A kapcsolódó API-k is el lettek távolítva a háló erőforrás-szolgáltatóban. A következő PowerShell-parancsmagok a rendszergazda RM-modulban és az előzetes verzióban Azure Stack hub-ban már nem működnek: **stop-AzsInfrastructureRoleInstance**, **disable-InfrastructureRoleInstance**, és **restart-InfrastructureRoleInstance**. Ezek a parancsmagok el lesznek távolítva a Azure Stack hub következő rendszergazdájától az Module kiadásával.
- A Azure Stack hub 2005 mostantól csak [az Azure stack hub 2020 (87. x verzió) App Service](app-service-release-notes-2020-Q2.md)támogatja.
- A hardver figyeléséhez szükséges felhasználói titkosítási beállítás a DES-ról az AES-ra változott a biztonság fokozása érdekében. Forduljon a hardveres partnerhez, és Ismerje meg, hogyan módosíthatja az alapszintű felügyeleti vezérlő (BMC) beállítását. Előfordulhat, hogy a BMC-ben történt módosítás után a **BmcCredential** újra kell futtatnia a rendszerjogosultságú végpont használatával. További információ: [titkok Elforgatása Azure stack központban](azure-stack-rotate-secrets.md)

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Kijavítva egy olyan hibát, amely miatt a javítási méretezési egység csomópontja meghiúsul, mert nem találta meg az alap operációs rendszer rendszerképének elérési útját.
- Kijavítottuk a méretezési egység csomópontjainak kijavításával kapcsolatos lépcsőzetes hatással rendelkező támogatási infrastruktúra szerepkörre kiterjedő, skálázással kapcsolatos problémát.
- Kijavítva egy probléma, amelyben a. A VHD-bővítmény (a. vhd helyett) nem engedélyezett, ha az operátorok a Azure Stack hub felügyeleti portálra felvettek saját lemezképeket az **összes szolgáltatásban > számítási > virtuálisgép-lemezképek > Hozzáadás gombra**.
- Kijavítva egy probléma, amelyben egy korábbi virtuális gép újraindítási művelete egy későbbi, váratlan újraindítást okozott a virtuális gép frissítési művelete után (lemezek, címkék stb. hozzáadása).
- Kijavított egy olyan problémát, amelyben egy duplikált DNS-zóna létrehozása miatt a portál nem válaszol. Ekkor a megfelelő hibát kell megmutatnia.
- Kijavított egy problémát, amelyben a **Get-AzureStackLogs** nem gyűjtötte össze a szükséges naplókat a hálózati problémák elhárításához. 
- Kijavított egy problémát, amelyben a portálon kevesebb hálózati adapter csatlakoztatható, mint amit valójában engedélyez. 
- Rögzített kód-integritási házirend, amely nem bocsát ki szabálysértési eseményeket bizonyos belső szoftverekhez. Ez csökkenti a syslog-ügyfélen keresztül kibocsátott kód integritásának megsértésével kapcsolatos események okozta zajt.
- Rögzített **set-TLSPolicy** parancsmag az új házirend betartatásához a https-szolgáltatás újraindítása vagy a gazdagép újraindítása nélkül.
- Kijavítva egy probléma, amelyben a Linux NTP-kiszolgáló hibásan hoz létre riasztásokat a felügyeleti portálon.  
- Kijavítva a hiba, ahol a biztonságimásolat-vezérlő szolgáltatási példányának feladatátvétele az automatikus biztonsági mentések letiltását eredményezte.
- Kijavítva a probléma, amelyben a belső titkos rotáció meghiúsul, ha az infrastrukturális szolgáltatások nem rendelkeznek internetkapcsolattal.
- Kijavított egy problémát, amelyben a felhasználók nem tekinthetik meg az előfizetési engedélyeket az Azure Stack hub-portálok használatával.

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack hub ezen frissítésében található biztonsági frissítésekről: [Azure stack hub biztonsági frissítései](release-notes-security-updates.md).

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack hub rendszeresen bocsát ki gyorsjavításokat. Az 2005-es kiadástól kezdve, amikor új főverzióra frissít (például: 1.2002. x – 1.2005. x), a rendszer automatikusan telepíti a legújabb gyorsjavításokat (ha vannak ilyenek) az új főverzióban. Ettől a ponttól kezdve, ha a buildhez kiadott egy gyorsjavítást, telepítse azt.

> [!NOTE]
> Azure Stack hub gyorsjavítási kiadásai kumulatívak; a legújabb gyorsjavítást csak akkor kell telepíteni, ha az adott verzióhoz tartozó korábbi gyorsjavítások összes javítását beolvassa.

További információkért tekintse meg a [karbantartási szabályzatot](azure-stack-servicing-policy.md).

Azure Stack hub-gyorsjavítások csak Azure Stack hub integrált rendszerekre érvényesek; Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

### <a name="prerequisites-before-applying-the-2005-update"></a>Előfeltételek: az 2005-es frissítés alkalmazása előtt

Azure Stack hub 2005-es kiadását a 2002-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

- [Azure Stack hub gyorsjavítási 1.2002.66.173](hotfix-1-2002-66-173.md)

### <a name="after-successfully-applying-the-2005-update"></a>Az 2005-es frissítés sikeres alkalmazása után

Az 2005-es kiadástól kezdve, amikor új főverzióra frissít (például: 1.2002. x – 1.2005. x), a rendszer automatikusan telepíti a legújabb gyorsjavításokat (ha vannak ilyenek) az új főverzióban.

Ha a 2005-es verzió telepítése után a rendszer a 2005-es gyorsjavításokat is felszabadítja, telepítse őket:

- [Azure Stack hub gyorsjavítási 1.2005.30.102](hotfix-1-2005-30-102.md)
::: moniker-end

::: moniker range="azs-2002"
## <a name="2002-build-reference"></a>2002-Build referenciája

Az Azure Stack hub 2002 Update Build száma **1.2002.0.35**.

> [!IMPORTANT]  
> A Azure Stack hub 2002-es frissítésével a Microsoft átmenetileg kiterjeszti [Azure stack hub támogatási szabályzatának utasításait](azure-stack-servicing-policy.md).  A COVID-19-re válaszoló világbeli ügyfelekkel dolgozunk, akik fontos döntéseket hozhatnak a Azure Stack hub-rendszerekkel kapcsolatban, hogyan frissülnek és felügyelnek, és ennek eredményeképpen gondoskodnak arról, hogy az adatközpont üzleti műveletei továbbra is megfelelően működjenek. Ügyfeleink támogatásában a Microsoft egy ideiglenes támogatási szabályzatot kínál, amely három korábbi frissítési verziót tartalmaz.  Ennek eredményeképpen az újonnan kiadott 2002-es frissítés és a három korábbi frissítési verzió (például a 1910, az 1908 és az 1907) közül bármelyiket támogatja a rendszer.

### <a name="update-type"></a>Frissítéstípus

Az Azure Stack hub 2002 Update Build típusa **megtelt**.

Az 2002-es frissítési csomag mérete nagyobb a korábbi frissítésekhez képest. A megnövelt méret a letöltendő időt eredményezi. A frissítés hosszú ideig az **előkészítési** állapotban marad, és a kezelők a korábbi frissítéseknél hosszabb időt vehetnek igénybe. Az 2002-es frissítés a belső tesztelési környezet következő várt futtatókörnyezeteit használta: 15-42 óra, 8 csomópont: 20-50 óra, 12 csomópont: 20-60 óra, 16 csomópont: 25-70 óra. A pontos frissítési futtatókörnyezetek jellemzően a rendszeren a bérlői munkaterhelések, a rendszer hálózati kapcsolata (ha az internethez csatlakoztatva) és a rendszerhardver-specifikációk alapján használt kapacitástól függenek. A várt értéknél rövidebb vagy hosszabb futtatókörnyezetek nem ritkán fordulnak elő, és nem igényelnek beavatkozást Azure Stack hub-operátorok számára, kivéve, ha a frissítés meghiúsul. Ez a futtatókörnyezeti közelítés az 2002-es frissítésre vonatkozik, és nem hasonlítható össze más Azure Stack hub-frissítésekkel.

További információ a frissítési buildek típusairól: [frissítések kezelése Azure stack hub-ban](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Újdonságok

<!-- What's new, also net new experiences and features. -->

- Elérhető a AzureRM-alapú Azure Stack hub rendszergazdai PowerShell-modulok új verziója (1.8.1).
- A Azure Stack hub felügyeleti REST API új verziója érhető el. A végpontokkal kapcsolatos részleteket és az [API-referenciák](/rest/api/azure-stack/)változásait is megtalálhatja.
- Az új Azure PowerShell bérlői modulok a Azure Stack hub 2020. április 15-én lesznek közzétéve. A jelenleg használt Azure RM-modulok továbbra is működni fognak, de az 2002-es Build után már nem lesznek frissítve.
- Új figyelmeztető riasztás hozzáadva a Azure Stack hub felügyeleti portálján a konfigurált syslog-kiszolgálóval fennálló csatlakozási problémák jelentéséhez. A riasztás címe **a syslog-ügyfél hálózati hibát észlelt a syslog-üzenet küldése közben**.
- Új figyelmeztető riasztás hozzáadva az Azure Stack hub felügyeleti portálján a Network Time Protocol (NTP) kiszolgáló csatlakozási problémáinak jelentéséhez. A riasztás címe **érvénytelen időforrást mutat a következőn: [csomópont neve]**.
- A [Java SDK](https://azure.microsoft.com/develop/java/) új csomagokat adott közzé a TLS-korlátozásokkal kapcsolatos 2002-es megszakítási változás miatt. Telepítenie kell az új Java SDK-függőséget. Az utasításokat a [Java és az API-verziók profiljaiban](../user/azure-stack-version-profiles-java.md?view=azs-2002&preserve-view=true#java-and-api-version-profiles)találja.
- Az System Center Operations Manager-Azure Stack hub felügyeleti csomag új verziója (1.0.5.10) elérhető és szükséges a 2002-at futtató összes rendszerhez az API-változások megszakítása miatt. Az API megváltoztatja a biztonsági mentési és a tárolási teljesítmény irányítópultokat, és azt javasoljuk, hogy a felügyeleti csomag frissítése előtt frissítse az összes rendszert a 2002-re.

### <a name="improvements"></a>Fejlesztései

<!-- Changes and product improvements with tangible customer-facing value. -->

- Ez a frissítés a frissítési folyamat olyan változásait tartalmazza, amelyek jelentősen javítják a jövőbeli teljes frissítések teljesítményét. Ezek a változások a 2002-es kiadás utáni következő teljes frissítéssel lépnek érvénybe, és konkrétan a gazdagép operációs rendszereinek frissítésére szolgáló teljes frissítés fázisának teljesítményét célozzák meg. A gazda operációsrendszer-frissítések teljesítményének javítása jelentősen csökkenti azt az időpontot, amelyben a bérlői munkaterhelések hatással vannak a teljes frissítések során.
- Az Azure Stack hub Readiness-ellenőrző eszköz mostantól ellenőrzi az AD Graph-integrációt az AD Graph számára lefoglalt összes TCP IP-port használatával.
- Az offline szindikált eszköz frissítése megbízhatósági frissítésekkel történt. Az eszköz már nem érhető el a GitHubon, és [át lett helyezve a PowerShell-galériaba](https://www.powershellgallery.com/packages/Azs.Syndication.Admin/). További információ: [Marketplace-elemek letöltése Azure stack hubhoz](azure-stack-download-azure-marketplace-item.md).
- Új figyelési képesség bevezetése folyamatban van. A fizikai gazdagépek és az infrastruktúra-virtuális gépek alacsony lemezterület-riasztása automatikusan kijavítja a platformot, és csak akkor, ha ez a művelet meghiúsul, a riasztás látható lesz az Azure Stack hub felügyeleti portálján, hogy az operátor végrehajtsa a műveletet.
- A [diagnosztikai naplók gyűjtésének](./diagnostic-log-collection.md?preserve-view=true&view=azs-2002)fejlesztése. Az új felület egyszerűsíti és egyszerűbbé teszi a diagnosztikai naplók gyűjtését azáltal, hogy eltávolítja a blob Storage-fiók előzetes konfigurálásának szükségességét. A tárolási környezet előre konfigurálva van, így naplók küldhetők a támogatási eset megnyitása előtt, és kevesebb időt kell fordítani a támogatási hívásokra.
- Az előjelzéses [naplók és az igény szerinti naplók gyűjtésére](./diagnostic-log-collection.md?preserve-view=true&view=azs-2002) vonatkozó idő 80%-kal csökkent. A naplózási gyűjtési idő hosszabb időt vehet igénybe a várt értéknél, de Azure Stack hub-operátorok beavatkozása nem szükséges, kivéve, ha a naplózási gyűjtemény sikertelen.
- Egy Azure Stack hub-frissítési csomag letöltési folyamata mostantól látható a frissítés panelen a frissítés kezdeményezése után. Ez csak azokra a csatlakoztatott Azure Stack hub rendszerekre vonatkozik, amelyek a [frissítési csomagok automatikus letöltéssel történő előkészítését](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages)választják.
- A hálózati vezérlő gazdagép-ügynökének megbízhatósági fejlesztése.
- Egy új, DNS-Orchestrator nevű Micro-szolgáltatást vezetett be, amely javítja a belső DNS-szolgáltatások rugalmassági logikáját a javítás és a frissítés során.
- Új kérelem érvényesítése sikertelen volt, mert a virtuális gépek létrehozásakor a rendszer érvénytelen blob URI-azonosítókat adott a rendszerindítási diagnosztikai Storage-fiók paraméteréhez.
- A Rdagent és a Host Agent automatikus szervizelési és naplózási funkcióinak hozzáadása – a gazdagép két szolgáltatása, amely megkönnyíti a virtuális gépek SZIFILISZét.
- Új szolgáltatás hozzáadása a piactér-kezeléshez, amely lehetővé teszi, hogy a Microsoft olyan attribútumokat adjon hozzá, amelyek letiltják a rendszergazdáknak, hogy a különböző tulajdonságok, például a Azure Stack vagy a számlázási modell miatt nem kompatibilisek legyenek a Azure Stack. Csak a Microsoft adhatja hozzá ezeket az attribútumokat. További információ: [a Marketplace-elemek letöltésére szolgáló portál használata](azure-stack-download-azure-marketplace-item.md#use-the-portal-to-download-marketplace-items).

### <a name="changes"></a>Módosítások

- A felügyeleti portál most azt jelzi, hogy egy művelet folyamatban van-e, és a Azure Stack régió melletti ikonra kattint. Amikor a ikon fölé viszi a kurzort, megjelenik a művelet neve. Ez lehetővé teszi a futó rendszer hátterében lévő műveletek azonosítását; például egy biztonsági mentési feladatokra vagy egy tárolási bővítésre, amely több órán keresztül is futtatható.

- A következő rendszergazdai API-k elavultak:

  | Erőforrás-szolgáltató       | Erőforrás              | Verzió            |
  |-------------------------|-----------------------|--------------------|
  | Microsoft. Storage. admin | farmok                 | 2015-12-01 – előzetes verzió |
  | Microsoft. Storage. admin | farmok/beszerzések    | 2015-12-01 – előzetes verzió |
  | Microsoft. Storage. admin | farmok/megosztások          | 2015-12-01 – előzetes verzió |
  | Microsoft. Storage. admin | farmok/storageaccounts | 2015-12-01 – előzetes verzió |

- A következő rendszergazdai API-kat egy újabb verzió váltotta fel (2018-09-01):

  | Erőforrás-szolgáltató      | Erőforrás              | Verzió    |
  |------------------------|-----------------------|------------|
  | Microsoft. backup. admin | backupLocation         | 2016-05-01 |
  | Microsoft. backup. admin | mentések                | 2016-05-01 |
  | Microsoft. backup. admin | műveletek             | 2016-05-01 |

- Windows rendszerű virtuális gép PowerShell használatával történő létrehozásakor ügyeljen arra, hogy adja hozzá a `provisionvmagent` jelzőt, ha azt szeretné, hogy a virtuális gép bővítményeket helyezzen üzembe. A jelző nélkül a virtuális gép a vendég ügynök nélkül jön létre, és nem távolítható el a virtuálisgép-bővítmények üzembe helyezésének lehetősége:

   ```powershell
   $VirtualMachine = Set-AzureRmVMOperatingSystem `
     -VM $VirtualMachine `
     -Windows `
     -ComputerName "MainComputer" `
     -Credential $Credential -ProvisionVMAgent
  ```

### <a name="fixes"></a>Javítások

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Kijavított egy problémát, amelyben a virtuális gép ugyanazon hálózati adapterén található több nyilvános IP-cím hozzáadása internetkapcsolattal kapcsolatos problémákat eredményezett. A két nyilvános IP-címmel rendelkező hálózati adapter a várt módon működik.
- Kijavított egy problémát, amely miatt a rendszer riasztást küld, ami azt jelzi, hogy az Azure AD-kezdőkönyvtár konfigurálása szükséges.
- Kijavítva egy olyan hibát, amely miatt a riasztás nem zárult le automatikusan. A riasztás azt jelezte, hogy az Azure AD-kezdőkönyvtár konfigurálása kötelező, de a probléma enyhítése után sem zárult le.
- Kijavítva egy olyan problémát, amely a frissítés előkészítési fázisában hibát okozott a frissítések erőforrás-szolgáltatójának belső meghibásodása miatt.
- Kijavított egy problémát, ami az erőforrás-szolgáltatói műveletek Azure Stack hub titkos elforgatása után meghiúsul.
- Kijavított egy problémát, amely az Azure Stack hub frissítési hibáinak gyakori okát okozta a ERCS szerepkörön történt memória-nyomás miatt.
- Kijavított egy hibát abban a frissítési panelen, amelyben a frissítési állapot az Azure Stack hub-frissítés előkészítési fázisában való **felkészülés** helyett **telepítésként** látható.
- Kijavított egy hibát, amelyben a virtuális kapcsolók RSC funkciója létrehozta a inkonzisztenciák, és eldobott egy terheléselosztó használatával áramló forgalmat. A RSC szolgáltatás alapértelmezés szerint le van tiltva.
- Kijavítva a hiba, ahol a hálózati adapter több IP-konfigurációja okozta a forgalmat, és megakadályozta a kimenő kapcsolatot. 
- Kijavított egy hibát, amikor egy hálózati adapter MAC-címe gyorsítótárazva lett, és az adott címnek egy másik erőforráshoz való hozzárendelésének oka a virtuális gép üzembe helyezésének meghibásodása volt.
- Kijavítva a hiba, hogy a kereskedelmi csatornán futó Windows rendszerbeli rendszerképeket nem lehetett aktiválni a AVMA.
- Kijavított egy hibát, amelyben a virtuális gépeket nem lehet létrehozni, ha a virtuális gép által igényelt virtuális magok száma a csomópont fizikai magokkal egyenlő. Mostantól engedélyezzük, hogy a virtuális magok egyenlőek legyenek a csomópont fizikai magokkal, vagy kisebbek legyenek.
- Kijavított egy problémát, amelyben nem engedélyezzük, hogy a licenc "NULL" értékűre legyen állítva az utólagos elszámolású lemezképek BYOL való váltásához.
- Kijavított egy problémát, amely lehetővé teszi a bővítmények hozzáadását a virtuálisgép-méretezési csoportokhoz.

## <a name="security-updates"></a>Biztonsági frissítések

További információ a Azure Stack hub ezen frissítésében található biztonsági frissítésekről: [Azure stack hub biztonsági frissítései](release-notes-security-updates.md).

## <a name="hotfixes"></a>Gyorsjavítások

Azure Stack hub rendszeresen bocsát ki gyorsjavításokat. Győződjön meg arról, hogy a 1910-es legújabb Azure Stack hub gyorsjavítását telepíti a 2002-es Azure Stack hub frissítése előtt.

> [!NOTE]
> Azure Stack hub gyorsjavítási kiadásai kumulatívak; a legújabb gyorsjavítást csak akkor kell telepíteni, ha az adott verzióhoz tartozó korábbi gyorsjavítások összes javítását beolvassa.

Azure Stack hub-gyorsjavítások csak Azure Stack hub integrált rendszerekre érvényesek; Ne kísérelje meg a gyorsjavítások telepítését a ASDK.

A gyorsjavításokkal kapcsolatos további információkért tekintse meg a [Azure stack hub karbantartási házirendjét](azure-stack-servicing-policy.md#hotfixes).

### <a name="prerequisites-before-applying-the-2002-update"></a>Előfeltételek: az 2002-es frissítés alkalmazása előtt

Azure Stack hub 2002-es kiadását a 1910-es kiadásra kell alkalmazni a következő gyorsjavításokkal:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.1910.84.230](https://support.microsoft.com/help/4592243)

### <a name="after-successfully-applying-the-2002-update"></a>Az 2002-es frissítés sikeres alkalmazása után

A frissítés telepítése után telepítse az összes vonatkozó gyorsjavítást.

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hub gyorsjavítási 1.2002.66.173](hotfix-1-2002-66-173.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-release-notes"></a>1910 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1908"
## <a name="1908-archived-release-notes"></a>1908 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-release-notes"></a>1907 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1906"
## <a name="1906-archived-release-notes"></a>1906 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1905"
## <a name="1905-archived-release-notes"></a>1905 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>1904 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>1903 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>1902 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>1901 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>1811 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>1809 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>1808 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>1807 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>1805 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>1804 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>1803 archivált kibocsátási megjegyzések
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>1802 archivált kibocsátási megjegyzések
::: moniker-end

::: moniker range="<azs-2002"
Az Azure Stack hub kibocsátási megjegyzései régebbi verziói a bal oldali tartalomjegyzékben érhetők el, az [**erőforrások > kibocsátási megjegyzések archívuma**](./relnotearchive/release-notes-1910.md)alatt. Ezek az archivált cikkek kizárólag referencia jellegűek, és nem jelentenek támogatást ezekhez a verziókhoz. További információ az Azure Stack hub támogatásáról: [Azure stack hub karbantartási szabályzata](azure-stack-servicing-policy.md). További segítségért forduljon a Microsoft ügyfél-támogatási szolgálatához.
::: moniker-end