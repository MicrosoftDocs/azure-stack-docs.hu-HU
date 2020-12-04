---
title: Fogalmak – az Azure Kubernetes Services (ak) Kubernetes alapjai Az Azure Stack HCI-ben
description: Ismerje meg a Kubernetes alapszintű fürt-és munkaterhelés-összetevőit, valamint azt, hogy azok hogyan kapcsolódnak az Azure Kubernetes Service-ben található szolgáltatásokhoz az Azure Stack HCI
author: daschott
ms.author: daschott
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: e17e75631a6daac8c7523a2340dd69d5914a82de
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557072"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-on-azure-stack-hci"></a>Az Azure Kubernetes Service-hez készült Kubernetes alapvető fogalmai Azure Stack HCI-ben

> A következőkre vonatkozik: Azure Stack HCI, AK Runtime a Windows Server 2019 Datacenter rendszeren

Az Azure Kubernetes Service on Azure Stack HCI egy nagyvállalati szintű Kubernetes-tároló platform, amely Azure Stack HCI-alapú. Ez magában foglalja a Microsoft által támogatott alapvető Kubernetes, bővítményeket, a Windows-tárolókat és a Microsoft által támogatott linuxos tároló-gazdagépet, amelynek célja, hogy **egyszerű üzembe helyezési és életciklus-kezelési élményt** biztosítson.

Ez a cikk bemutatja a legfontosabb Kubernetes infrastruktúra-összetevőket, például a vezérlési síkot, a csomópontokat és a csomópont-készleteket. A munkaterhelések erőforrásai, például a hüvelyek, a központi telepítések és a készletek, valamint az erőforrások névterek szerinti csoportosítása is bevezethető.

## <a name="kubernetes-cluster-architecture"></a>Kubernetes-fürt architektúrája
A Kubernetes az Azure Kubernetes szolgáltatás fő összetevője Azure Stack HCI-on. Az Azure Kubernetes Service on Azure Stack HCI előre meghatározott konfigurációk használatával helyezi üzembe a Kubernetes-fürtöket, és a méretezhetőséget szem előtt tartva.
 
Az üzembe helyezési művelet több linuxos vagy Windows rendszerű virtuális gépet hoz létre, és összekapcsolja ezeket a Kubernetes-fürt (ek) létrehozásához. 
 
A központilag telepített rendszer készen áll a szabványos Kubernetes-munkaterhelések fogadására, a számítási feladatok méretezésére, vagy a virtuális gépek számának méretezésére, valamint a fürtök számának igény szerinti felskálázására.

Az Azure Kubernetes Service-fürtök két fő összetevőre vannak osztva a Azure Stack HCI-ben:

- A *felügyeleti* fürt egy vagy több cél fürt üzembe helyezéséhez és kezeléséhez szükséges alapszintű előkészítési mechanizmust és felületet biztosítja.
- A *célcsoportok* (más néven munkaterhelés-fürtök) az alkalmazás munkaterheléseit futtatják, és egy felügyeleti fürt kezeli őket.

![Az Azure Kubernetes szolgáltatás technikai architektúráját mutatja be Azure Stack HCI-ben](.\media\concepts\architecture.png)

## <a name="management-cluster"></a>Felügyeleti fürt
Ha Azure Stack HCI-ben hoz létre egy Azure Kubernetes Service-fürtöt, a rendszer automatikusan létrehozza és konfigurálja a felügyeleti fürtöt. Ez a felügyeleti fürt a munkaterhelések futtatására szolgáló célszámítógépek kiépítési és kezelési feladata. A felügyeleti fürtök a következő alapvető Kubernetes összetevőket tartalmazzák:
- *API-kiszolgáló* – az API-kiszolgáló az alapul szolgáló Kubernetes API-k teszik elérhetővé. Ez az összetevő a felügyeleti eszközök (például a Windows felügyeleti központ, a PowerShell-modulok vagy a) interakcióját biztosítja `kubectl` .
- *Load Balancer* – a Load Balancer egyetlen dedikált linuxos virtuális gép, amely terheléselosztási szabályt biztosít a felügyeleti fürt API-kiszolgálójához.

### <a name="windows-admin-center"></a>Windows felügyeleti központ
A Windows felügyeleti központ intuitív felhasználói felületet kínál a Kubernetes operátor számára az Azure Kubernetes Service-fürtök életciklusának kezeléséhez Azure Stack HCI-on.

### <a name="powershell-module"></a>PowerShell-modul
A PowerShell-modul egyszerűen letöltheti, konfigurálhatja és üzembe helyezheti az Azure Kubernetes szolgáltatást Azure Stack HCI-ben. A PowerShell-modul Emellett támogatja a További célcsoportok üzembe helyezését és konfigurálását, valamint a meglévők újrakonfigurálását is.

## <a name="target-cluster"></a>Cél fürt
A cél (számítási feladat) fürt a Kubernetes széles körben elérhető központi telepítése Linux rendszerű virtuális gépeken a Kubernetes-vezérlési sík-összetevők és a Linux-feldolgozó csomópontok futtatásához. A Windows Server Core-alapú virtuális gépek a Windows Worker-csomópontok létrehozására használhatók. Egy felügyeleti fürt egy vagy több célként kezelt fürtöt is használhat.

### <a name="worker-nodes"></a>Munkavégző csomópontok
Az alkalmazások és a támogató szolgáltatások futtatásához szükség van egy Kubernetes-csomópontra. Az Azure Stack HCI-ben egy Azure Kubernetes Service Target-fürt egy vagy több munkavégző csomóponttal rendelkezik, amely egy virtuális gép (VM), amely a Kubernetes csomópont-összetevőket futtatja, valamint az alkalmazás számítási feladatait alkotó hüvelyeket és szolgáltatásokat.

### <a name="load-balancer"></a>Terheléselosztó
A Load Balancer egy Linux és HAProxy + életben futó virtuális gép, amely elosztott terhelésű szolgáltatásokat biztosít a felügyeleti fürt által üzembe helyezett cél fürtök számára.

Az Azure Kubernetes Service on Azure Stack HCI minden egyes cél fürthöz legalább egy terheléselosztó virtuális gépet (LB VM) fog hozzáadni. Ezen kívül egy másik terheléselosztó is létrehozható az API-kiszolgáló magas rendelkezésre állásához a célként megadott fürtön. Bármely, a Kubernetes létrehozott típusú szolgáltatás egy terheléselosztási `LoadBalancer` szabályt fog létrehozni az LB virtuális gépen.

### <a name="add-on-components"></a>Összetevők Add-On
Több opcionális bővítmény-összetevő is üzembe helyezhető bármely adott fürtben: az Azure arc, a Prometheus, a Grafana vagy a Kubernetes irányítópult.

## <a name="kubernetes-components"></a>Kubernetes-összetevők
Ez a szakasz bemutatja az Azure Kubernetes Azure Stack Service-ben üzembe helyezhető alapvető Kubernetes-munkaterhelési összetevőket, például a hüvelyeket, a központi telepítéseket és a készleteket, valamint az erőforrások névterekre való csoportosításának módját.

### <a name="pods"></a>hüvely

A Kubernetes *hüvelyek* használatával futtatja az alkalmazás egy példányát. A pod az alkalmazás egy példányát jelöli. a hüvelyek általában egy tárolóval rendelkező 1:1-es hozzárendeléssel rendelkeznek, bár a pod több tárolót is tartalmazhat. Ezek a többtárolós hüvelyek ugyanazon a csomóponton vannak ütemezve, és lehetővé teszik a tárolók számára a kapcsolódó erőforrások megosztását.

A pod létrehozásakor megadhatja az *erőforrás-kérelmeket* , hogy bizonyos mennyiségű CPU-vagy memória-erőforrást igényeljen. A Kubernetes ütemező megkísérli a hüvelyek ütemezését, hogy az elérhető erőforrásokkal rendelkező csomóponton fusson a kérelem teljesítése érdekében. Megadhatja a maximális erőforrás-korlátot is, amely megakadályozza, hogy egy adott Pod túl sok számítási erőforrást fogyasztson a mögöttes csomópontból. Az ajánlott eljárás az, hogy az összes hüvely erőforrás-korlátait tartalmazza, hogy a Kubernetes Scheduler megértse, mely erőforrások szükségesek és engedélyezettek.

További információ: [Kubernetes hüvelyek][kubernetes-pods] és [Kubernetes Pod életciklusa][kubernetes-pod-lifecycle].

A pod logikai erőforrás, de a tároló (k), ahol az alkalmazás munkaterhelései futnak. a hüvelyek általában elmúló, eldobható erőforrások, és egyénileg ütemezett hüvelyek hiányoznak az Kubernetes által biztosított magas rendelkezésre állási és redundancia-funkciók közül. Ehelyett a hüvelyeket Kubernetes- *vezérlők*, például a központi telepítési vezérlő helyezi üzembe és kezeli.

### <a name="deployments-and-yaml-manifests"></a>Központi telepítések és YAML-jegyzékek

A *központi telepítés* egy vagy több azonos hüvelyt jelöl, amelyet a Kubernetes telepítési vezérlő kezel. A központi telepítés meghatározza a létrehozandó *replikák* (hüvelyek) számát, és a Kubernetes ütemező gondoskodik arról, hogy ha a hüvelyek vagy a csomópontok problémákba ütköznek, a további hüvelyek az egészséges csomópontokon vannak ütemezve.

A központi telepítések segítségével módosíthatja a hüvelyek, a felhasznált tárolók vagy a csatolt tárolók konfigurációját. A központi telepítési vezérlő leállítja a megadott számú replikát, létrehozza a replikákat az új központi telepítési definícióból, és addig folytatja a folyamatot, amíg a központi telepítésben szereplő összes replikát nem frissíti.

A legtöbb állapot nélküli alkalmazásnak az egyes hüvelyek ütemezése helyett a telepítési modellt kell használnia. A Kubernetes nyomon követheti az üzemelő példányok állapotát és állapotát, így biztosítva, hogy a fürtön belül a szükséges számú replika fusson. Ha csak egyes hüvelyeket ütemezhet, a hüvelyek nem indulnak újra, ha probléma lép fel, és nem lettek átütemezett állapotba, ha az aktuális csomópont problémát észlel.

Ha egy alkalmazáshoz a példányok kvóruma szükséges, hogy mindig elérhetők legyenek a felügyeleti döntésekhez, nem szeretné, hogy a frissítési folyamat megszakítsa ezt a képességet. a *Pod-megszakadási költségkeretekkel* határozható meg, hogy egy központi telepítés hány replikáját lehet leállítani a frissítés vagy a csomópont frissítése során. Ha például *öt (5)* replikája van az üzemelő példányban, megadhatja a *4* . Pod-megszakadást, hogy egyszerre csak egy replikát töröljen vagy ütemezzen. A pod-erőforrások korlátaihoz hasonlóan a legjobb megoldás az, ha a pod-megszakadási költségvetést olyan alkalmazásokhoz határozza meg, amelyekhez legalább egy replika szükséges.

A központi telepítések általában a vagy a alkalmazással hozhatók létre és kezelhetők `kubectl create` `kubectl apply` . Központi telepítés létrehozásához meg kell adnia egy jegyzékfájlt a YAML (YAML nem Markup Language) formátumban. Az alábbi példa az NGINX webkiszolgáló alapszintű telepítését hozza létre. Az üzemelő példány *három (3)* replikát határoz meg, és megköveteli, hogy a *80* -es port nyitva legyen a tárolón. Az erőforrás-kérelmek és-korlátok a PROCESSZORhoz és a memóriához is meg vannak határozva.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Összetettebb alkalmazások is létrehozhatók, beleértve a YAML-jegyzékben található terheléselosztó szolgáltatásokat is.

További információ: Kubernetes- [telepítések][kubernetes-deployments]

##### <a name="mixed-os-deployments"></a>Vegyes operációs rendszerek központi telepítései

Ha egy adott Azure Kubernetes szolgáltatás Azure Stack HCI-alapú célszámítógépen a Linux és a Windows rendszerű munkavégző csomópontokból áll, a munkaterheléseket olyan operációs rendszerre kell ütemezni, amely támogatja a számítási feladat kiépítési folyamatát. A Kubernetes két mechanizmust biztosít annak biztosítására, hogy a munkaterhelések a cél operációs rendszerrel rendelkező csomópontokon legyenek kihelyezve:

- A *csomópont-választó* egy egyszerű mező a pod specifikációban, amely megkötése esetén a hüvelyek csak az operációs rendszernek megfelelő, kifogástalan állapotú csomópontokra lesznek ütemezve. 
- A *megfertőzés és a tolerancia* együttesen biztosítják, hogy a hüvelyek ne legyenek szándékosan ütemezve a csomópontokra. Egy csomópont "szennyezett" lehet, hogy ne fogadja el azokat a hüvelyeket, amelyek nem tudják explicit módon elviselni a megromlást a pod specban.

További információ: csomópont- [választók][node-selectors] és a [megfertőzés és a tolerancia][taints-tolerations].

### <a name="statefulsets-and-daemonsets"></a>StatefulSets és DaemonSets

A központi telepítési vezérlő a Kubernetes ütemező használatával futtat egy adott számú replikát az elérhető erőforrásokkal rendelkező bármely elérhető csomóponton. Az üzemelő példányok használatának ezen megközelítése elegendő lehet az állapot nélküli alkalmazások számára, de nem olyan alkalmazásokhoz, amelyek állandó elnevezési konvenciót vagy tárolót igényelnek. Azon alkalmazások esetében, amelyeknek replikát kell megadniuk az egyes csomópontokon vagy a kijelölt csomópontokon egy fürtön belül, a központi telepítési vezérlő nem vizsgálja meg, hogyan oszlanak meg a replikák a csomópontok között.

Az ilyen típusú alkalmazások kezeléséhez két Kubernetes-erőforrás szükséges:

- *StatefulSets* – az alkalmazások állapotának fenntartása egy adott Pod-életcikluson túl, például a Storage-ban.
- *DaemonSets* – ellenőrizze, hogy fut-e a futó példányok minden csomóponton, a Kubernetes rendszerindítási folyamatának elején.

### <a name="statefulsets"></a>StatefulSets

A modern alkalmazásfejlesztés gyakran az állapot nélküli alkalmazások megvalósítását célozza, de a *StatefulSets* állapot-nyilvántartó alkalmazásokhoz, például adatbázis-összetevőket tartalmazó alkalmazásokhoz is használható. Egy StatefulSet hasonló a központi telepítéshez, amely egy vagy több azonos hüvelyt hoz létre és felügyel. A StatefulSet lévő replikák az üzembe helyezés, a méretezés, a frissítések és a megszakítások kecses, szekvenciális megközelítését követik. A StatefulSet (mint replikák átütemezett) az elnevezési konvenció, a hálózati nevek és a tárterület megmaradnak.

Az alkalmazást YAML formátumban kell megadni `kind: StatefulSet` , és a StatefulSet vezérlő kezeli a szükséges replikák telepítését és felügyeletét. Az adatokat a rendszer állandó tárterületre írja, és a mögöttes tároló még a StatefulSet törlése után is megmarad.

További információ: [Kubernetes StatefulSets][kubernetes-statefulsets].

A StatefulSet lévő replikák ütemezése és futtatása a Azure Stack HCI-fürtön lévő Azure Kubernetes szolgáltatás bármely elérhető csomópontján történik. Ha meg kell győződnie arról, hogy a készletben legalább egy pod egy csomóponton fut, használhat egy Daemonset elemet.

### <a name="daemonsets"></a>DaemonSets

Bizonyos naplók vagy figyelési igények esetében előfordulhat, hogy egy adott Pod-t kell futtatnia az összes vagy a kijelölt csomóponton. Újra *daemonset elemet* egy vagy több azonos hüvely üzembe helyezéséhez, de a daemonset elemet vezérlő biztosítja, hogy a megadott csomópontok a pod egy példányát futtatják.

A Daemonset elemet-vezérlő a fürt rendszerindítási folyamata elején ütemezheti a hüvelyeket a csomópontokon, még az alapértelmezett Kubernetes-ütemező elindítása előtt. Ez a funkció biztosítja, hogy a Daemonset elemet lévő hüvelyek elindulnak, mielőtt a hagyományos hüvelyek egy központi telepítésben vagy StatefulSet vannak ütemezve.

A StatefulSets hasonlóan a Daemonset elemet a YAML definíciójának részeként van definiálva a használatával `kind: DaemonSet` .

További információ: [Kubernetes DaemonSets][kubernetes-daemonset].

### <a name="namespaces"></a>Névterek

A Kubernetes-erőforrások, például a hüvelyek és a központi telepítések logikailag vannak csoportosítva egy *névtérbe*. Ezek a csoportok lehetővé teszik az Azure Kubernetes-szolgáltatások logikai felosztását Azure Stack HCI-fürtön, és az erőforrások létrehozására, megtekintésére vagy kezelésére való hozzáférés korlátozását. Létrehozhat névtereket is az üzleti csoportok elkülönítéséhez, például:. A felhasználók csak a hozzájuk rendelt névterekben lévő erőforrásokkal tudnak kommunikálni.

Ha Azure Stack HCI-ben hoz létre egy Azure Kubernetes Service-fürtöt, a következő névterek érhetők el:

- *alapértelmezett* – ez a névtér, ahol a hüvelyek és a központi telepítések alapértelmezés szerint jönnek létre, ha nincs megadva. Kisebb környezetekben az alkalmazások közvetlenül az alapértelmezett névtérbe helyezhetők, további logikai elkülönítések létrehozása nélkül. Ha együttműködik a Kubernetes API-val, például a `kubectl get pods` -val, akkor az alapértelmezett névtér lesz használatban, ha nincs megadva.
- *Kube-System* – ez a névtér az alapvető erőforrások, például a DNS és a proxy, illetve a Kubernetes irányítópultja. Általában nem telepítheti saját alkalmazásait ebbe a névtérbe.
- *Kube – nyilvános* – ez a névtér általában nincs használatban, de a teljes fürtön látható erőforrások számára is használható, és bármely felhasználó megtekinthető.

További információ: Kubernetes- [névterek][kubernetes-namespaces].

[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[node-selectors]: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
[taints-tolerations]: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
