---
title: Azure Stack HCI kibocsátási megjegyzései
description: A Azure Stack HCI, Version 20H2 kibocsátási megjegyzései.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/8/2020
ms.openlocfilehash: e28ff46596e2669327a3fa1d3b76fca346658b21
ms.sourcegitcommit: 85827a2227eb2d1ed1ed44bb9f00e28d96818c84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96868986"
---
# <a name="release-notes-for-azure-stack-hci-public-preview"></a>A Azure Stack HCI nyilvános előzetes kiadásának kibocsátási megjegyzései

> A következőkre vonatkozik: Azure Stack HCI, Version 20H2

Ez a cikk a Azure Stack HCI nyilvános előzetes frissítési csomagjainak tartalmát ismerteti.

## <a name="december-8-2020-security-update-kb4592441"></a>2020. december 8. biztonsági frissítés (KB4592441)

Ez a frissítés a Azure Stack HCI legújabb kiadásának javításait és javításait tartalmazza. 

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a frissítés a belső operációsrendszer-funkciókra vonatkozó különféle biztonsági funkciókat tartalmaz. A biztonsági réseket úgy oldja meg, hogy megakadályozza, hogy a rendszerfiókként futó alkalmazások a "fájl:" portokra nyomtassanak. A probléma jövőbeli megoldásához győződjön meg arról, hogy az alkalmazások vagy szolgáltatások egy adott felhasználói vagy szolgáltatási fiókkal futnak.

Ebben a kiadásban nincsenek dokumentálva további problémák.

A megoldott biztonsági rések részletes ismertetését a [biztonsági frissítési útmutatóban](https://portal.msrc.microsoft.com/security-guidance)találja.

### <a name="known-issues-in-this-update"></a>A frissítés ismert problémái
A Microsoft jelenleg nem ismeri a frissítéssel kapcsolatos problémákat.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült december 8, 2020 biztonsági frissítés (KB4592441) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1408) a 4592441-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/2/7/2/272ea75f-1657-43ce-a7a4-a17d51463a94/4592441.csv)letöltésével töltheti le.

   > [!NOTE]
   > Egyes fájlok hibásan rendelkeznek "nem alkalmazható" értékkel a CSV-fájl "file version" oszlopában. Ez hamis pozitív vagy hamis negatív értéket eredményezhet, ha valamilyen harmadik féltől származó vizsgálati eszközt használ a Build ellenőrzéséhez.

## <a name="november-23-2020-preview-update-kb4586852"></a>November 23, 2020 előzetes frissítés (KB4586852)

Ez a frissítés a Azure Stack HCI legújabb kiadásának javításait és javításait tartalmazza. 

   > [!IMPORTANT]
   > Ha Azure Stack HCI-fürtöket a nyilvános előzetes rendszerkép használatával konfigurálták és regisztrálták, akkor az KB4586852-frissítés telepítése után ki kell javítania az Azure-regisztrációt, hogy használni lehessen a frissítés által kínált új szolgáltatásokat. A frissítés telepítése után hajtsa végre az alábbi lépéseket az egyes fürtökön:
   >
   > 1. Győződjön meg arról, hogy a fürt összes kiszolgálója frissült a KB4586852. Ha nem, akkor a javítás sikertelen lesz, és jelzi a frissíteni kívánt csomópont (oka) t.
   >
   > 2. Kapcsolódjon helyileg vagy a következő fürtcsomópontok egyikéhez sem `Enter-PSSession <server-name>`
   >
   > 3. Töltse le a AzStackHCI v 0.4.1 regisztrációs modult a PowerShell-galéria. `Install-Module -Name Az.StackHCI`A legújabb modul beszerzéséhez futtassa a parancsot.
   >
   > 4. A regisztráció kijavításához futtassa a következő parancsot. Használja a fürt eredeti regisztrálásához használt előfizetés-azonosítót. `Get-AzureStackHCI` Megjeleníti az aktuális ARM URI-t, amely tartalmazza az előfizetési adatokat.
   >
   >   ```PowerShell
   >   Register-AzStackHCI -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -RepairRegistration
   >   ```
   > 

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a nem biztonsági frissítés tartalmazza a minőségi frissítéseket. A legfontosabb változások a következők: 

- Ezzel a frissítéssel Azure Stack HCI-ügyfelek, akik érvényes Windows Server 2019 Datacenter Edition licenccel rendelkeznek, a Azure Stack HCI-ben üzemeltetett virtuális gépek (VM-EK) kényelmes aktiválásához használhatják őket anélkül, hogy az egyes virtuális gépekhez tartozó termékkulcsot kellene kezelnie. A Windows felügyeleti központot vagy a PowerShellt használhatja a nem használt Windows Server 2019 Datacenter Edition aktiválási kulcs beírásához közvetlenül a Azure Stack HCI-gazdagépre, hogy engedélyezze az automatikus VM-aktiválást (AVMA). A Windows Server 2019-es vagy korábbi verzióját futtató virtuális gépek ezután öröklik az aktiválást a gazdagépről. A megadott kulcs lehet a mennyiségi licencelési központból beszerzett több aktiválási kulcs (MAK), az OEM-kiszolgálóra alkalmazott eredetiséget igazoló tanúsítvány (COA) matricán lévő kulcs, vagy a Windows Server 2019 Datacenter Edition kereskedelmi dobozos példányának kulcsa. Ebben a kiadásban az általános mennyiségi licenckulcs (GVLK) nem támogatott.

- Azure Stack HCI most gyűjti a szükséges diagnosztikai adatokat, amelyek minimálisan szükségesek ahhoz, hogy az eszköz biztonságos, naprakész és a várt módon legyen végrehajtva. A szükséges diagnosztikai adatok egy korlátozott adathalmazt gyűjtenek, amely kritikus fontosságú az eszköz és a konfigurációjának megismeréséhez. Ez az érték segít azonosítani az adott hardver-vagy szoftver-konfiguráción esetlegesen felmerülő problémákat.  

### <a name="known-issues-in-this-update"></a>A frissítés ismert problémái
A Microsoft jelenleg nem ismeri a frissítéssel kapcsolatos problémákat.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése 
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült november 23, 2020 biztonsági frissítés (KB4586852) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1381) a 4586852-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/5/c/6/5c6f8c37-3e0b-4239-a6d9-9c709e18e869/4586852.csv)letöltésével töltheti le.

   > [!NOTE]
   > Egyes fájlok hibásan rendelkeznek "nem alkalmazható" értékkel a CSV-fájl "file version" oszlopában. Ez hamis pozitív vagy hamis negatív értéket eredményezhet, ha valamilyen harmadik féltől származó vizsgálati eszközt használ a Build ellenőrzéséhez.

## <a name="november-10-2020-security-update-kb4586811"></a>November 10, 2020 biztonsági frissítés (KB4586811)

Ez a frissítés a Azure Stack HCI legújabb kiadásának javításait és javításait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a frissítés a belső operációsrendszer-funkciókra vonatkozó különféle biztonsági funkciókat tartalmaz. A biztonsági réseket úgy oldja meg, hogy megakadályozza a rendszerfiókként futó alkalmazások nyomtatását a fájlokra mutató helyi portokra. Sikertelen nyomtatási feladatok naplójának 50-as hibája: "a kérelem nem támogatott" a 372-es AZONOSÍTÓJÚ eseménynél a PrintService\Admin-eseménynaplóban. A probléma jövőbeli megoldásához győződjön meg arról, hogy az alkalmazások vagy szolgáltatások egy adott felhasználói vagy szolgáltatási fiókkal futnak.

Ebben a kiadásban nincsenek dokumentálva további problémák.

A megoldott biztonsági rések részletes ismertetését a [biztonsági frissítési útmutatóban](https://portal.msrc.microsoft.com/security-guidance)találja.

### <a name="known-issues-in-this-update"></a>A frissítés ismert problémái
A Microsoft jelenleg nem ismeri a frissítéssel kapcsolatos problémákat.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült november 10, 2020 biztonsági frissítés (KB4586811) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1345) a 4586811-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/8/f/2/8f2ce4bb-e113-4abc-b3ff-f0f4c4c71403/4586811.csv)letöltésével töltheti le.

   > [!NOTE]
   > Egyes fájlok hibásan rendelkeznek "nem alkalmazható" értékkel a CSV-fájl "file version" oszlopában. Ez hamis pozitív vagy hamis negatív értéket eredményezhet, ha valamilyen harmadik féltől származó vizsgálati eszközt használ a Build ellenőrzéséhez.

## <a name="november-10-2020-servicing-stack-update-kb4590242"></a>2020. november 10. karbantartási verem frissítése (KB4590242)

Ez a frissítés a Azure Stack HCI legújabb kiadásának minőségi újdonságait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a frissítés minőségi javításokat végez a karbantartási veremben, amely a frissítéseket telepítő összetevő. A karbantartási verem frissítései (SSU) gondoskodnak arról, hogy robusztus és megbízható karbantartási veremmel rendelkezzen, hogy az eszközök megkapják és telepítsék a Microsoft-frissítéseket.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült November, 2020 karbantartási verem frissítése (KB4590242) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="restart-information"></a>Újraindítási információ
A frissítés alkalmazása után nem kell újraindítani a számítógépet.

### <a name="removal-information"></a>Eltávolítási információ
A karbantartási verem frissítései (SSU) módosítják a frissítések telepítését, és nem távolíthatók el az eszközről.

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1342) a 4590242-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/b/b/4/bb4fb4f5-c0ba-4e55-bada-d72310857982/4590242.csv)letöltésével töltheti le.

### <a name="references"></a>Hivatkozások

A SSU kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Karbantartási verem frissítései](/windows/deployment/update/servicing-stack-updates)
- [Karbantartási verem frissítései (SSU): gyakori kérdések](https://support.microsoft.com/help/4535697)

Ismerje meg, hogy a Microsoft milyen [terminológiát](https://support.microsoft.com/help/824684) használ a szoftverfrissítések leírásához.

## <a name="october-20-2020-preview-update-kb4580388"></a>Október 20, 2020 előzetes frissítés (KB4580388)

Ez a frissítés a Azure Stack HCI legújabb kiadásának javításait és javításait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a nem biztonsági frissítés tartalmazza a minőségi frissítéseket. A legfontosabb változások a következők:

- Ezzel a frissítéssel Azure Stack HCI-ügyfelek, akik érvényes Windows Server 2019 Datacenter Edition licenccel rendelkeznek, a Azure Stack HCI-ben üzemeltetett virtuális gépek (VM-EK) kényelmes aktiválásához használhatják őket anélkül, hogy az egyes virtuális gépekhez tartozó termékkulcsot kellene kezelnie.

### <a name="known-issues-in-this-update"></a>A frissítés ismert problémái

A Microsoft tisztában van a frissítéssel kapcsolatos egyik problémával.

#### <a name="symptom"></a>Hibajelenség
Ha Élő áttelepítés használatával helyez át egy virtuális gépet a Windows Server és a Azure Stack HCI operációs rendszerek között, akkor a következő hibaüzenet jelenhet meg: "a virtuális gép áttelepítésének letiltása, <vmname> mert a különböző Windows-kiadások közötti VM-Migrálás nem támogatott (virtuális gép azonosítója)."

Azt is megteheti, hogy a fürt Aware frissítési (CAU) művelete meghiúsul, ha a CAU során a virtuális gépek bármelyikének el kell végeznie Élő áttelepítés.

#### <a name="workaround"></a>Áthidaló megoldás

Élő áttelepítés helyett használjon gyors áttelepítést. CAU használata esetén átmenetileg módosítsa az alapértelmezett viselkedést, hogy a CAU gyors áttelepítést használjon.

Példa:

```powershell
Get-ClusterResourceType "Virtual Machine" | Set-ClusterParameter MoveTypeThreshold 3001
```

Javasoljuk, hogy térjen vissza az előző `MoveTypeThreshold` értékre, miután a Cau sikeresen befejeződött.

További információ: [a virtuális gépek áthelyezésének beállítása a csomópontok kiürítése során](https://techcommunity.microsoft.com/t5/failover-clustering/configuring-how-vms-are-moved-when-a-node-is-drained/ba-p/371848).

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült Windows Update október 20, 2020 biztonsági frissítés (KB4580388) a következőn keresztül érhető el:. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1321) a 4580388-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/2/f/b/2fb766d3-c4c8-4279-8718-8efbd0b6f211/4580388.csv)letöltésével töltheti le.

   > [!NOTE]
   > Egyes fájlok hibásan rendelkeznek "nem alkalmazható" értékkel a CSV-fájl "file version" oszlopában. Ez hamis pozitív vagy hamis negatív értéket eredményezhet, ha valamilyen harmadik féltől származó vizsgálati eszközt használ a Build ellenőrzéséhez.

## <a name="october-13-2020-security-update-kb4580363"></a>Október 13., 2020 biztonsági frissítés (KB4580363)

Ez a frissítés a Azure Stack HCI legújabb kiadásának javításait és javításait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a frissítés a belső operációsrendszer-funkciókra vonatkozó különféle biztonsági funkciókat tartalmaz. Ebben a kiadásban nincsenek dokumentálva további problémák.

A megoldott biztonsági rések részletes ismertetését a [biztonsági frissítési útmutatóban](https://portal.msrc.microsoft.com/security-guidance)találja.

### <a name="known-issues-in-this-update"></a>A frissítés ismert problémái
A Microsoft jelenleg nem ismeri a frissítéssel kapcsolatos problémákat.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült október 13, 2020 biztonsági frissítés (KB4580363) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1288) a 4580363-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/f/7/8/f78801f0-e7e5-4a3d-a971-f642ccd24ee4/4580363.csv)letöltésével töltheti le.

   > [!NOTE]
   > Egyes fájlok hibásan rendelkeznek "nem alkalmazható" értékkel a CSV-fájl "file version" oszlopában. Ez hamis pozitív vagy hamis negatív értéket eredményezhet, ha valamilyen harmadik féltől származó vizsgálati eszközt használ a Build ellenőrzéséhez.

## <a name="october-13-2020-servicing-stack-update-kb4583287"></a>Október 13., 2020 karbantartási verem frissítése (KB4583287)

Ez a frissítés a Azure Stack HCI legújabb kiadásának minőségi újdonságait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a frissítés minőségi javításokat végez a karbantartási veremben, amely a frissítéseket telepítő összetevő. A karbantartási verem frissítései (SSU) gondoskodnak arról, hogy robusztus és megbízható karbantartási veremmel rendelkezzen, hogy az eszközök megkapják és telepítsék a Microsoft-frissítéseket.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült október 13, 2020 karbantartási verem frissítése (KB4583287) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="restart-information"></a>Újraindítási információ
A frissítés alkalmazása után nem kell újraindítani a számítógépet.

### <a name="removal-information"></a>Eltávolítási információ
A karbantartási verem frissítései (SSU) módosítják a frissítések telepítését, és nem távolíthatók el az eszközről.

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1287) a 4583287-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/b/8/5/b85160fb-85d9-49f9-b9d5-7dbc0158a944/4583287.csv)letöltésével töltheti le.

### <a name="references"></a>Hivatkozások

A SSU kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Karbantartási verem frissítései](/windows/deployment/update/servicing-stack-updates)
- [Karbantartási verem frissítései (SSU): gyakori kérdések](https://support.microsoft.com/help/4535697)

Ismerje meg, hogy a Microsoft milyen [terminológiát](https://support.microsoft.com/help/824684) használ a szoftverfrissítések leírásához.

## <a name="september-17-2020-preview-update-kb4577629"></a>Szeptember 17, 2020 előzetes frissítés (KB4577629)

Ez a frissítés a Azure Stack HCI legújabb kiadásának javításait és javításait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a nem biztonsági frissítés tartalmazza a minőségi frissítéseket. A legfontosabb változások a következők:
- Probléma történt, ha a multiplexeren áthaladó szoftveres Load Balancer (SLB) forgalom átirányítható egy másik gazdagépre, amely az alkalmazás kapcsolódási hibáját okozhatja.

### <a name="known-issues-in-this-update"></a>A frissítés ismert problémái
A Microsoft jelenleg nem ismeri a frissítéssel kapcsolatos problémákat.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült szeptember 17-2020 biztonsági frissítés (KB4577629) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1259) a 4577629-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/9/1/a/91addcbb-2b36-408c-ab88-736de42edb98/4577629.csv) letöltése

   > [!NOTE]
   > Egyes fájlok hibásan rendelkeznek "nem alkalmazható" értékkel a CSV-fájl "file version" oszlopában. Ez hamis pozitív vagy hamis negatív értéket eredményezhet, ha valamilyen harmadik féltől származó vizsgálati eszközt használ a Build ellenőrzéséhez.

## <a name="september-8-2020-security-update-kb4577470"></a>2020. szeptember 8. biztonsági frissítés (KB4577470)

Ez a frissítés a Azure Stack HCI legújabb kiadásának javításait és javításait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a frissítés a belső operációsrendszer-funkciókra vonatkozó különféle biztonsági funkciókat tartalmaz. Ebben a kiadásban nincsenek dokumentálva további problémák.

A megoldott biztonsági rések részletes ismertetését a [biztonsági frissítési útmutatóban](https://portal.msrc.microsoft.com/security-guidance)találja.

### <a name="known-issues-in-this-update"></a>A frissítés ismert problémái
A Microsoft jelenleg nem ismeri a frissítéssel kapcsolatos problémákat.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült szeptember 8., 2020-os biztonsági frissítés (KB4577470) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1226) a 4577470-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/3/c/4/3c468525-5867-4cc3-8d34-dba88989adab/4577470.csv)letöltésével töltheti le.

   > [!NOTE]
   > Egyes fájlok hibásan rendelkeznek "nem alkalmazható" értékkel a CSV-fájl "file version" oszlopában. Ez hamis pozitív vagy hamis negatív értéket eredményezhet, ha valamilyen harmadik féltől származó vizsgálati eszközt használ a Build ellenőrzéséhez.

## <a name="september-8-2020-servicing-stack-update-kb4577558"></a>2020. szeptember 8. karbantartási verem frissítése (KB4577558)

Ez a frissítés a Azure Stack HCI legújabb kiadásának minőségi újdonságait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a frissítés minőségi javításokat végez a karbantartási veremben, amely a frissítéseket telepítő összetevő. A karbantartási verem frissítései (SSU) gondoskodnak arról, hogy robusztus és megbízható karbantartási veremmel rendelkezzen, hogy az eszközök megkapják és telepítsék a Microsoft-frissítéseket.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
Az [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült szeptember 8., 2020-es karbantartási verem frissítése (KB4577558) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="restart-information"></a>Újraindítási információ 
A frissítés alkalmazása után nem kell újraindítani a számítógépet.

### <a name="removal-information"></a>Eltávolítási információ
A karbantartási verem frissítései (SSU) módosítják a frissítések telepítését, és nem távolíthatók el az eszközről.

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1220) a 4577558-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/8/f/6/8f612a9b-cb4e-4832-9397-156760848592/4577558.csv)letöltésével töltheti le.

### <a name="references"></a>Hivatkozások

A SSU kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Karbantartási verem frissítései](/windows/deployment/update/servicing-stack-updates)
- [Karbantartási verem frissítései (SSU): gyakori kérdések](https://support.microsoft.com/help/4535697)

Ismerje meg, hogy a Microsoft milyen [terminológiát](https://support.microsoft.com/help/824684) használ a szoftverfrissítések leírásához.

## <a name="august-11-2020-security-update-kb4574585"></a>2020. augusztus 11. biztonsági frissítés (KB4574585)

Ez a frissítés a Azure Stack HCI legújabb kiadásának javításait és javításait tartalmazza.

### <a name="improvements-and-fixes"></a>Javítások és javítások
Ez a frissítés a belső operációsrendszer-funkciókra vonatkozó különféle biztonsági funkciókat tartalmaz. Ebben a kiadásban nincsenek dokumentálva további problémák.

A megoldott biztonsági rések részletes ismertetését a [biztonsági frissítési útmutatóban](https://portal.msrc.microsoft.com/security-guidance)találja.

### <a name="known-issues-in-this-update"></a>A frissítés ismert problémái
A Microsoft jelenleg nem ismeri a frissítéssel kapcsolatos problémákat.

### <a name="how-to-get-this-update"></a>A frissítés beszerzése
A [Azure stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) -hoz készült augusztus 11, 2020 biztonsági frissítés (KB4574585) Windows Update keresztül érhető el. A Azure Stack HCI-fürtön való telepítéséhez lásd: [Azure stack HCI-fürtök frissítése](manage/update-cluster.md).

### <a name="file-information"></a>Fájl adatai
Az ebben a frissítésben megadott fájlok listáját (operációsrendszer-Build 17784,1162) a 4574585-es [összesítő frissítéshez tartozó Fájlinformációk](https://download.microsoft.com/download/7/f/4/7f451def-76c5-4cc0-9929-0c5efeb27d2f/4574585.csv)letöltésével töltheti le.

   > [!NOTE]
   > Egyes fájlok hibásan rendelkeznek "nem alkalmazható" értékkel a CSV-fájl "file version" oszlopában. Ez hamis pozitív vagy hamis negatív értéket eredményezhet, ha valamilyen harmadik féltől származó vizsgálati eszközt használ a Build ellenőrzéséhez.