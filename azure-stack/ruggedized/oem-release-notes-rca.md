---
title: Azure Stack hub robusztus OEM kibocsátási megjegyzései
description: Azure Stack hub robusztus OEM kibocsátási megjegyzései
author: sethmanheim
ms.topic: article
ms.date: 12/08/2020
ms.author: sethm
ms.reviewer: danlewi
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: fac088f56dc7de04d8f78bfef1ab63c87b83f336
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939739"
---
# <a name="azure-stack-hub-ruggedized-2008-oem-release-notes"></a>Azure Stack hub robusztus 2008 OEM kibocsátási megjegyzések

Ez a cikk a Azure Stack hub kiadási és verziószámával kapcsolatos információkat tartalmaz.

## <a name="overview"></a>Áttekintés

Ez a dokumentum ismerteti Azure Stack hub robusztus, belső vezérlőprogram és illesztőprogramok számára készített frissítéseinek tartalmát. Ez a frissítés az Azure Stack hub legújabb kiadásának javításait és javításait tartalmazza. Alább láthatók a letöltési hivatkozások:

* https://aka.ms/azshwpackage
* https://aka.ms/azshwmanifest

## <a name="baseline-and-document-history"></a>Alapkonfiguráció és dokumentum előzményei

| Kiadás | Dátum       | A módosítások leírása         |
|---------|------------|--------------------------------|
| 2008    | 10/13/2020 | 2.2.2010.5 OEM-csomagok frissítései |

## <a name="firmware"></a>Belső vezérlőprogram

### <a name="bios"></a>BIOS

| Kiadás verziója | Belső vezérlőprogram verziója | Módosítások                                                                                                                                                                                                                                                                                                                                                                                                       |   |   |
|-----------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 2008            | 2.8.2            | Javítva lett egy iparági probléma, amely a BIOS-verziókon 2.6.4 a 2.8.1-on keresztül, ahol a rendszer alaphelyzetbe állíthatja a rendszerindítási képernyőn, amikor a "memória konfigurálása" üzenet jelenik meg. A probléma a DDR4 és az NVDIMM-N memória konfigurációjában alkalmazható.<br><br>A biztonsági rések (gyakori biztonsági rések és kitettségek – CVE) megoldásának továbbfejlesztése, például: CVE-2020-0545, CVE-2020-0548 és CVE-2020-0549. |   |   |
| 2005            | 2.7.7            |                                                                                                                                                                                                                                                                                                                                                                                                               |   |   |
|                 |                  |                                                                                                                                                                                                                                                                                                                                                                                                               |   |   |

### <a name="idrac"></a>IDRAC

| Kiadás verziója | Belső vezérlőprogram verziója | Módosítások                                                                                                                                                                                                                                                                          |   |   |
|-----------------|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 2008            | 4.22.0.0         | 167411: kijavított egy problémát, amely a kényszerített rendszer újraindítását okozta a belső vezérlőprogram Redfish API-n keresztüli frissítésekor.<br><br>155376: kijavított egy problémát, amely a iDRAC újraindítását okozta a SupportAssist-naplók gyűjtése során.<br><br>162778: a virtuális konzol szolgáltatás miatt a iDRAC alacsony memóriabeli feltételt rögzített. |   |   |
| 2005            | 4.10.10.10       |                                                                                                                                                                                                                                                                                  |   |   |
|                 |                  |                                                                                                                                                                                                                                                                                  |   |   |

### <a name="nic-azure-stack-nodes"></a>Stack-csomópontok NIC-Azure

| Kiadás verziója    | Belső vezérlőprogram verziója    | Módosítások                                                                                                          |
|--------------------|---------------------|------------------------------------------------------------------------------------------------------------------|
|     2008           |     14.27.60.08     | Rögzített egy belső vezérlőprogram végzetes állítása, amely egy IRISC leállást mutatott, mert init_hca várakozik az időzítők flow zárolási kiadására. |
|     2005           |     14.26.60.00     |                                                                                                                  |

### <a name="nic-hlh"></a>HÁLÓZATI ADAPTER – HLH

| Kiadás verziója    | Belső vezérlőprogram verziója    | Módosítások                                                                                                                                                                   |
|--------------------|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     2008           |     19.5.12         | Az Intel (R) Ethernet X710 10Gb-adapterek problémája megoldódott, amelyben az eszköz LED-je a villogás után villog, ha az eszköz LED-je az F2 eszköz beállításainál a Blink módra van beállítva. |
|     2005           |     19.0.12         |                                                                                                                                                                           |

### <a name="hba-azure-stack-node-capacity-drives"></a>Stack Node-meghajtók HBA-Azure

| Kiadás verziója    | Belső vezérlőprogram verziója    | Módosítások    |
|--------------------|---------------------|------------|
|     2008           |     16.17.01.00     |            |
|     2005           |     16.17.00.05     |            |

### <a name="hba-hlh-capacity-drives"></a>HBA – HLH kapacitású meghajtók

|     Kiadás verziója |     Belső vezérlőprogram verziója |     Módosítások                                                                                           |
|---------------------|----------------------|-------------------------------------------------------------------------------------------------------|
| 2005, 2008          | 25.5.7.0005          | Kijavítva a hiba, amikor egy vezérlő lefagyhat a rendszerindítás során, mert hiányos a nem felejtő memória-konfiguráció. |

### <a name="hba---boot-drives"></a>HBA-rendszerindító meghajtók

| Kiadás verziója | Belső vezérlőprogram verziója | Módosítások                                                                                   |
|-----------------|------------------|-------------------------------------------------------------------------------------------|
| 2005, 2008      | 2.5.13.3024      | Rögzített PPID eltérés a iDRAC-hardver leltározási lapjának M. 2 meghajtójának a főnök vezérlőjénél |

### <a name="cpld"></a>CPLD

| Kiadás verziója | Belső vezérlőprogram verziója | Módosítások                                                                                                                                                                                                |
|-----------------|------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     2008        |     9.0.6        | Címek lehetséges IDRAC a belső vezérlőprogram frissítése közben.<br> A jelzett hibák szűrésével megakadályozhatja a hamis hibák jelentését.<br> A gazdagép-memória leképezése úgy módosult, hogy megakadályozza a lehetséges elülső USB-portok kitartását. |
|     2005        |     1.0.6        |                                                                                                                                                                                                        |

### <a name="drive-fw"></a>Meghajtó FW

| Kiadás verziója | Belső vezérlőprogram verziója | Módosítások                                |
|-----------------|------------------|----------------------------------------|
| 2008            | S402             | A jövőbeli online belső vezérlőprogram frissítéseinek engedélyezése |
| 2005            | S401             |                                        |

## <a name="drivers"></a>Illesztőprogramok

### <a name="nic"></a>Hálózati adapter

| Kiadás verziója | Belső vezérlőprogram verziója | Módosítások                                |
|-----------------|------------------|----------------------------------------|
| 2008            | 2.40.22511.0    |  |
| 2005            | 2.30.21713.0 |                                        |

### <a name="hba---capacity-drives"></a>HBA – kapacitású meghajtók

| Kiadás verziója | Belső vezérlőprogram verziója | Módosítások |
|-----------------|------------------|---------|
|  2005, 2008   |  2.51.25.02  |         |

### <a name="hba---boot-drives"></a>HBA-rendszerindító meghajtók

| Kiadás verziója | Belső vezérlőprogram verziója | Módosítások |
|-----------------|------------------|---------|
|  2005, 2008   |  1.2.0.1051 |         |

### <a name="intel-chipset"></a>Intel lapkakészlet

| Kiadás verziója | Belső vezérlőprogram verziója | Módosítások |
|-----------------|------------------|---------|
|  2005, 2008   | 10.1.18243.8188 |         |
