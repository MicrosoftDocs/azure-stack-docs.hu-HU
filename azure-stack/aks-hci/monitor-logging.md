---
title: Az Azure Kubernetes Service figyelése Azure Stack HCI-fürtökön
description: Az Azure Kubernetes Service naplózási adatai monitorozása és megtekintése Azure Stack HCI-fürtökön
author: v-susbo
ms.topic: how-to
ms.date: 01/26/2021
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: 728f23954d99f6e8cd9373467ed8d104a457e636
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873852"
---
# <a name="monitor-aks-on-azure-stack-hci-clusters"></a>AK figyelése Azure Stack HCI-fürtökön

Kétféle figyelési és naplózási megoldás érhető el az AK-hoz Azure Stack HCI-ben. A két megoldás összehasonlítása: 

| Megoldás  | Azure-kapcsolat  | Támogatás és szolgáltatás  | Költségek | Üzembe helyezés |
| ------- |  ------------  | ---------  | --------------  | ---------------- |
| Azure Monitor | Ehhez csatlakoztatni kell az AK-t Azure Stack HCI-fürtön az Azure-ba az Azure-Kubernetes használatával | A Microsoft által teljes mértékben támogatott és szervizelt | Regisztrálnia kell Azure Monitor szolgáltatásra |  Az Azure arc használata a [fürtök figyeléséhez](#monitor-clusters-using-azure-monitor) |
| Helyszíni figyelés és naplózás | Nem igényel Azure-kapcsolatot | A Microsoft nyílt forráskódú szoftverként támogatott (támogatási szerződés vagy SLA nélkül), közösségi és/vagy külső szállító  | Szállítótól függő | Ügyfél által vezérelt, lásd: [fürtök figyelése helyszíni figyelés használatával](#monitor-clusters-using-on-premises-monitoring) |

## <a name="monitor-clusters-using-azure-monitor"></a>Fürtök figyelése Azure Monitor használatával
Ha Azure Stack HCI-fürtökön a Azure Monitort AK-val szeretné használni, kövesse az alábbi két témakör lépéseit: 

- [A fürt összekötése az Azure-val az Azure arc Kubernetes használatával](./connect-to-arc.md)  
- [Azure Monitor engedélyezése Azure arc-kompatibilis Kubernetes-fürtön](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters) 

## <a name="monitor-clusters-using-on-premises-monitoring"></a>Fürtök figyelése helyszíni figyelés használatával

A Vezérlőpult-csomópontok és munkaterhelések állapotának, teljesítményének és erőforrás-felhasználásának figyelése rendkívül fontos az alkalmazások éles környezetben való futtatásakor. A helyszíni figyelési megoldás beállításával kapcsolatos további információkért lásd: a [Prometheus és a Grafana telepítése](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring). Ez a figyelési megoldás a következő két eszközt tartalmazza: 

- A **Prometheus** egy figyelési és riasztási eszközkészlet, amely a tárolók számítási feladatainak figyelésére használható. A Prometheus különböző típusú gyűjtőket és ügynököket használ a metrikák gyűjtéséhez és a jelentések megtekintésére szolgáló adatbázisban való tárolásához. 

- A **Grafana** olyan eszköz, amely a Grafana-irányítópultokon található mérőszámok megtekintésére, lekérdezésére és megjelenítésére szolgál. Előre konfigurálva van, hogy a Prometheus-t használja adatforrásként. 

### <a name="install-monitoring-tools"></a>Figyelési eszközök telepítése

Ha gyorsan be szeretne állítani egy egyszerű figyelési megoldást a környezetében, futtassa a [Setup-Monitoring.ps1](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#easy-steps-to-setup-monitoring-to-use-local-port-forward-to-access-grafana) PowerShell-parancsfájlt. A parancsfájl tartalmazza a figyelési megoldás gyors üzembe helyezéséhez szükséges összes konfigurációs lépést. 

Ha be szeretne vonni egy bejövő adatvezérlőt a Grafana-mel, kövesse a [Bejövő forgalom vezérlő használatára vonatkozó lépéseket a Grafana eléréséhez](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#detailed-steps-to-setup-monitoring-to-use-ingress-controller-to-access-grafana). A bejövő vezérlő olyan szoftver, amely fordított proxyt, konfigurálható forgalmi útválasztást és Transport Layer Security (TLS) leállást biztosít a Kubernetes-szolgáltatásokhoz.

### <a name="on-premises-logging"></a>Helyszíni naplózás

A naplózás kulcsfontosságú a hibaelhárítás és a diagnosztika szempontjából. Azure Stack HCI-ben a naplózási megoldás a Elasticsearch, a Fluent bites és a Kibana (EFK) alapján történik. Ezek az összetevők tárolóként vannak telepítve: 

- A Fluent bit a log processzor és a továbbító, amely különböző forrásokból gyűjt adatokat és naplókat, majd formázza, egyesíti és tárolja azokat a Elasticsearch-ben. 
- A Elasticsearch egy elosztott keresési és elemzési motor, amely képes a naplók központi tárolására a gyors keresésekhez és az adatelemzésekhez.  
- A Kibana interaktív vizualizációkat biztosít egy webes irányítópulton. Ez az eszköz lehetővé teszi a Elasticsearch tárolt naplók megtekintését és lekérdezését, majd a diagramok és irányítópultok használatával történő megjelenítését.

Helyszíni naplózási megoldás beállításához tekintse meg az alábbi lépéseket a [naplózás beállításához a Kibana eléréséhez](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging#easy-steps-to-setup-logging-to-use-local-port-forward-to-access-kibana). Ez a cikk az összes olyan összetevőt tartalmazza, amelyek szükségesek a tároló-naplók összegyűjtéséhez, összesítéséhez és lekérdezéséhez a fürtön. 

A speciális konfigurációs lépésekért tekintse meg a [Windows naplózása](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging#detailed-steps-to-setup-logging)című témakört.

## <a name="next-steps"></a>Következő lépések

- [Linux-alkalmazások üzembe helyezése Kubernetes-fürtön](./deploy-linux-application.md).
- [Windows Server-alkalmazás üzembe helyezése Kubernetes-fürtön](./deploy-windows-application.md).
- A [Kubernetes alapvető fogalmai](kubernetes-concepts.md).