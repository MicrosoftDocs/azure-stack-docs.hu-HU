---
title: A fürt ellenőrzési jelentéskészítésének hibáinak megoldása
description: A fürt-ellenőrzési jelentéskészítés és a QoS-beállítások konfigurációjának ellenőrzése Azure Stack HCI-fürtökhöz
author: khdownie
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.author: v-kedow
ms.reviewer: JasonGerend
ms.openlocfilehash: a5b6ef03701daf1c1f4115f88a2a4e44bac1bd61
ms.sourcegitcommit: 0e2c814cf2c154ea530a4e51d71aaf0835fb2b5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918637"
---
# <a name="troubleshoot-cluster-validation-reporting"></a>A fürt ellenőrzési jelentéskészítésének hibáinak megoldása

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2; Windows Server 2019

Ez a témakör segítséget nyújt a hálózati és tárolási QoS (szolgáltatásminőség) beállításainak a Azure Stack HCI-fürtben lévő kiszolgálókon történő ellenőrzésével kapcsolatos hibák megoldásához, és ellenőrzi, hogy a fontos szabályok definiálva vannak-e. Az optimális kapcsolat és teljesítmény érdekében a fürt ellenőrzési folyamata ellenőrzi, hogy az adatközpont-áthidaló (DCB) QoS-konfiguráció konzisztens-e, és ha meg van adva, a feladatátvételi fürtszolgáltatással és az SMB/SMB Direct forgalmi osztályokkal kapcsolatos megfelelő szabályokat tartalmazza.

DCB szükséges a RDMA konvergált Ethernet (RoCE) hálózatokon, és nem kötelező (de ajánlott) az Internet Wide RDMA Protocol (iWARP) hálózatokhoz.

## <a name="install-data-center-bridging"></a>Az adatközpont-áthidalás telepítése

A QoS-specifikus parancsmagok használatához telepíteni kell az adatközpont-áthidalás szolgáltatást. A következő parancsmag futtatásával ellenőrizhető, hogy az adatközpont-áthidaló szolgáltatás már telepítve van-e egy kiszolgálón:

```PowerShell
Get-WindowsFeature -Name Data-Center-Bridging -ComputerName Server1
```

Ha nincs telepítve az adatközpont-áthidalás, telepítse a következő parancsmag futtatásával a fürt minden kiszolgálóján:

```PowerShell
Install-WindowsFeature –Name Data-Center-Bridging -ComputerName Server1
```

## <a name="run-a-cluster-validation-test"></a>Fürt-ellenőrzési teszt futtatása

Az érvényesítés funkciót a Windows felügyeleti központban válassza az **eszközök > kiszolgálók > leltár > a fürt ellenőrzése** lehetőséget, vagy futtassa a következő PowerShell-parancsot:

```PowerShell
Test-Cluster –Node Server1, Server2
```

Többek között a teszt ellenőrzi, hogy a DCB QoS-konfiguráció konzisztens-e, és hogy a fürtben lévő összes kiszolgáló azonos számú forgalmi osztályt és QoS-szabályt tartalmaz. Azt is ellenőrzi, hogy az összes kiszolgáló rendelkezik-e QoS-szabályokkal a feladatátvételi fürtszolgáltatáshoz, illetve az SMB/SMB Direct forgalmi osztályokhoz.

Az ellenőrzési jelentést a Windows felügyeleti központban tekintheti meg, vagy egy naplófájlhoz férhet hozzá az aktuális munkakönyvtárban. Például: C:\Users \<username> \AppData\Local\Temp\

A jelentés alján a "QoS-beállítások ellenőrzése" és a fürt minden egyes kiszolgálójára vonatkozó jelentés jelenik meg.

Ha meg szeretné tudni, hogy mely forgalmi osztályok vannak beállítva egy kiszolgálón, használja a `Get-NetQosTrafficClass` parancsmagot.

További információ: [Azure stack HCI-fürt ellenőrzése](../deploy/validate.md).

## <a name="validate-networking-qos-rules"></a>Hálózatkezelési QoS-szabályok ellenőrzése

Ellenőrizze, hogy a fürtben lévő kiszolgálók között a DCB hajlandó állapotának és prioritási folyamatának vezérlési állapotára vonatkozó beállítások konzisztensek-e.

### <a name="dcb-willing-status"></a>DCB hajlandó állapota

Az adatközpont-áthidaló képesség Exchange Protocol (DCBX) szolgáltatást támogató hálózati adapterek képesek fogadni a konfigurációkat egy távoli eszközről. Ennek a funkciónak az engedélyezéséhez a hálózati adapteren lévő DCB-bitet igaz értékre kell állítani. Ha a kívánt bit hamis értékre van állítva, akkor az eszköz elutasítja a távoli eszközökről érkező összes konfigurációs próbálkozást, és csak a helyi konfigurációkat kényszeríti ki. Ha konvergált Ethernet-(RoCE-) adaptereken keresztül RDMA használ, akkor a kívánt bitet minden kiszolgálón false értékre kell állítani.

Egy Azure Stack HCI-fürtben lévő összes kiszolgálónak ugyanúgy kell beállítania a DCB.

A `Set-NetQosDcbxSetting` parancsmag használatával állítsa be a DCB a True vagy a False értékre, az alábbi példában látható módon:

```PowerShell
Set-NetQosDcbxSetting –Willing $false
```

### <a name="dcb-flow-control-status"></a>DCB állapotának ellenőrzése

A prioritásalapú forgalomszabályozás létfontosságú, ha a felső rétegbeli protokoll, például a Fiber Channel, veszteségmentes átvitelt feltételez az alsóbb rétegben. A DCB-folyamatvezérlés engedélyezhető vagy letiltható akár globálisan, akár különálló hálózati adapterek esetén is. Ha engedélyezve van, lehetővé teszi a QoS-házirendek létrehozását, amelyek rangsorolják az egyes alkalmazások forgalmát.

Ahhoz, hogy a QoS-házirendek zökkenőmentesen működjenek a feladatátvétel során, az Azure Stack HCI-fürt összes kiszolgálójának ugyanazzal a folyamat-vezérlési állapottal kell rendelkeznie. Ha RoCE-adaptereket használ, akkor az összes kiszolgálón engedélyezni kell a prioritási folyamat vezérlését.

A `Get-NetQosFlowControl` folyamat aktuális vezérlési konfigurációjának lekéréséhez használja a parancsmagot. Alapértelmezés szerint minden prioritás le van tiltva.

A `Enable-NetQosFlowControl` és a `Disable-NetQosFlowControl` parancsmagot a-Priority paraméterrel használva kapcsolja be vagy ki a prioritási folyamat vezérlését. A következő parancs például lehetővé teszi a flow-vezérlést a 3. prioritással jelölt forgalomban:

```PowerShell
Enable-NetQosFlowControl –Priority 3
```

## <a name="validate-storage-qos-rules"></a>Tárolási QoS-szabályok ellenőrzése

Ellenőrizze, hogy minden csomópont rendelkezik-e QoS-szabállyal a feladatátvételi fürtszolgáltatáshoz, illetve az SMB vagy az SMB Direct szolgáltatáshoz. Ellenkező esetben előfordulhat, hogy a kapcsolati problémák és a teljesítménnyel kapcsolatos problémák merülhetnek fel.

### <a name="qos-rule-for-failover-clustering"></a>QoS-szabály a feladatátvételi fürtszolgáltatáshoz

Ha **a** tárolási QoS-szabályok egy fürtben vannak definiálva, akkor a feladatátvételi fürtszolgáltatás QoS-szabályának jelen kell lennie, vagy kapcsolódási problémák merülhetnek fel. Új QoS-szabály a feladatátvételi fürtszolgáltatáshoz való hozzáadásához használja a `New-NetQosPolicy` parancsmagot az alábbi példában látható módon:

```PowerShell
New-NetQosPolicy "Cluster" -IPDstPort 3343 -Priority 6
```

### <a name="qos-rule-for-smb"></a>QoS-szabály SMB-hez

Ha egy vagy több csomóponton van definiálva QOS-szabály, de nem rendelkezik QOS-szabállyal az SMB-hez, ez az SMB kapcsolódási és teljesítménnyel kapcsolatos problémái miatt fordulhat elő. Új hálózati QoS-szabály az SMB-hez való hozzáadásához használja a `New-NetQosPolicy` parancsmagot az alábbi példában látható módon:

```PowerShell
New-NetQosPolicy -Name "SMB" -SMB -PriorityValue8021Action 3
```

### <a name="qos-rule-for-smb-direct"></a>QoS-szabály a közvetlen SMB-hez

Az SMB Direct megkerüli a hálózati veremet, ehelyett RDMA metódusokat használ az adatok átviteléhez. Ha egy vagy több csomóponton van definiálva QOS-szabály, de nem rendelkezik QOS-szabállyal a közvetlen SMB-hez, ez a kapcsolati problémákhoz vezethet a közvetlen SMB esetében. Ha új QoS-házirendet szeretne létrehozni az SMB Direct szolgáltatáshoz, adja ki a következő parancsokat:

```PowerShell
New-NetQosPolicy "SMB Direct" –NetDirectPort 445 –Priority 3
```

## <a name="next-steps"></a>Következő lépések

A kapcsolódó információkkal kapcsolatban lásd még:

- [Adatközponthíd-képzés](/windows-server/networking/technologies/dcb/dcb-top)
- [Az adatközpont-áthidalás kezelése](/windows-server/networking/technologies/dcb/dcb-manage)
- [A QoS gyakori konfigurációi](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj735302(v=ws.11))