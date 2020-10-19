---
title: Moduláris adatközpont (MDC) hálózatkezelési függelék
description: A MDC hálózatkezelési függelékei.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/30/2019
ms.lastreviewed: 12/30/2019
ms.openlocfilehash: 10de658f7e5f8621d44baaa16f2181f58f47811e
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182965"
---
# <a name="mdc-network-appendix"></a>MDC hálózati függelék

A függelék a MDC hardverhez biztosít Device paramétert és identitási adatokat.

## <a name="technical-device-parameters"></a>Technikai eszköz paraméterei

### <a name="cisco-93360yc-fx2--cisco-9348gc-fxp"></a>Cisco 93360YC-FX2 & Cisco 9348GC – FXP

| **Szolgáltatás** | **Cisco Nexus 93360YC – FX2** | **Cisco Nexus 9348GC – FXP** |
|---|---|---|
| Portok | 96 x 1/10/25 GB és 12 x 40/100 – Gbps QSFP28 port | 48 x 1 – GBASE-T-portok, 4 x 1/10/25 – Gbps SFP28-portok és 2 x 40/100 QSFP28-portok |
| Támogatott sebességek | 1/10/25 – GB/s a downlink, 40/100 – Gbps a Kikapcsolások esetében, feltörés támogatott portok, 97-108:4x10/25G | 100 – Mbps és 1 GB/s sebesség |
| CPU | Négy mag | Négy mag |
| Rendszermemória | Legfeljebb 24 GB | 24 GB |
| SSD-meghajtó | 128 GB | 128 GB |
| Rendszerpuffer | 40 MB | 40 MB |
| Felügyeleti portok | Két port: egy RJ-45 és egy SFP + | Két port: egy RJ-45 és egy SFP + |
| USB-portok | Eggyel | Eggyel |
| RS-232 soros portok | Eggyel | Eggyel |
| Tápegységek (legfeljebb kettő) | 1200 wattos áram (AC), 1200 W HVAC/HVDC | 350 W AC, 440 W DC |
| Tipikus teljesítmény (AC) | 404 W | 178 W |
| Maximális teljesítmény (AC) | 900 W | 287 W |
| Bemeneti feszültség (AC) | 100 v – 240 V | 100 v – 240 V |
| Bemeneti feszültség (nagy feszültségű AC [HVAC]) | 100 v – 277 V | 90 v – 305 V |
| Bemeneti feszültség (DC) | – 40 v – 72 V | \-36 v – 72 V |
| Bemeneti feszültség (nagy feszültségű DC [HVDC]) | – 240V – 380V | 192 v – 400 V |
| Gyakoriság (AC) | 50 Hz – 60 Hz | 50 Hz – 60 Hz |
| Ventilátor | Három ventilátoros tálca | Három |
| Légáramlás | Port-oldali bevitel és kipufogó | Port-oldali bevitel és kipufogó |
| Fizikai méretek | 3,38 x 17,41 x 24.14 a alkalmazásban. (8,59 x 44,23 x 61,31 cm) | 1,72 x 17,3 x 19,7 a-ben. |
| (H x W x D) | | (4,4 x 43,9 x 49,9 cm) |
| Akusztika | 76,7 dBA a 40%-os ventilátor sebességgel, 88,7 dBA, 70% ventilátor sebessége és 97,4 dBA a 100% ventilátor sebessége | 67,5 dBA a 50%-os ventilátor sebességgel, 73,2 dBA, 70% ventilátor sebessége és 81,6 dBA a 100% ventilátor sebessége |
| RoHS-megfelelőség | Igen | Igen |
| MTBF | 320 040 óra | 257 860 óra |
| Minimális ACI-rendszerkép | ACI – N9KDK9 – 14.1.2 | ACI-N9KDK9-13.0 |
| A minimális NX – operációsrendszer-rendszerkép | NXOS-9.3 (1) | NXOS – 703I 7.1 |

### <a name="juniper-mx204"></a>Juniper MX204

|  |  |  |
|--|--|--|
| Layout | Rendszerkapacitás | 3 Tbps |
|        | Tárolóhely tájolása | NA |
|        | Szerelési | Elöl vagy középen |
| Fizikai specifikáció | Méretek (W x H x D) | 17,45 "x 8,71" x 24,5 "(44,3 x 22,1 x 62,2 cm) |
|                        | A súlyozás teljesen betöltve | 130 LB/59 kg |
|                        | Eltávolított súlyozás | 52 lbs/23.6 kg |
| Útválasztási motor | Alapértelmezett memória | 2x16 MB vagy flash Storage; 64 GB DDR4 RAM; 2x50 GB SSD |
|                | Magok száma | 6 mag |
| Redundancia | Összetevők | Tápegységek, REs, ventilátorok |
| Környezeti | Légáram | Egymás mellett |
|               | Működési hőmérséklet | 32 ° – 115 ° F (0 ° – 46 ° C) tengeri szinten |
|               | Működési páratartalom | 5% – 90% |
|               | Működési magasság | 10 000 Ft (3048 m) |
| Tanúsítványok | NEBS | • GR-1089-Core EMC és elektromos biztonság |
|                | | • Közös kötési hálózat (CBN) |
|                | | • Nemzeti elektromos kód (NEC) |
|                | | • GR-63 – alapvető fizikai védelem |

### <a name="dell-emc-s4148f-on"></a>Dell EMC S4148F – bekapcsolva

Funkciók:

- 48 10 Gigabit Ethernet SFP + portok
- 6 40 Gigabit Ethernet QSFP + portok
- Egy RJ45 konzol/felügyeleti port, RS232-jelzéssel 
- Egy RJ45 Micro-USB-B konzol portja 
- Egy RJ45 10/100/1000Base-T felügyeleti Ethernet port 

Specifikációk

- Méret: 
  - Egy RU, 1,75 "(h) x 17" (w) x 18 "(d) (4,4 cm (h) x 43,1 cm (w) x 45,7 cm (d)) 
  - S4112:1.7 "(h) x 8.28" (w) x 18 "(d) (4,125 cm (h) x 20,9 cm (w) x 45 cm (d) 
- Tápegység: 100 – 240 VAC 50/60 Hz
- Tápegység (DC), amely a S4412-re vonatkozik: névleges-40 VDC – 72 VDC
- A jelenlegi rajzolási szint maximális száma: 6A/5A, 100/120V AC; 3A/2.5 A: 200/240V AC
- S4112:2A/1.7 A, 100/120V AC; 1A/0,8 A: 200/240V AC. 
- S4112 (DC):-40V/5A,-48V/4.2 A,-72V/2.8 A 

Működési előírások maximális száma:
- Működési hőmérséklet: 41 ° – 104 ° F (5 ° – 40 ° C)
- Működési páratartalom: 5% – 85% (RH), nem kondenzációs 

Nem működési előírások maximális száma:
- Tárolási hőmérséklet:-40 ° – 149 °F (-40 ° C – 65 ° C) 
- Tárolási páratartalom: 5% – 95% (RH), nem kondenzációs


## <a name="identity"></a>Identitás

Minden hálózati eszköz úgy lett kialakítva, hogy működjön a Access-Control rendszerű (TACACS) terminálszolgáltatásokkal. A TACACS központosított identitási élményt biztosít. A TACACS-kiszolgálót a kezdeti konfiguráció részeként kell megadni.

Figyelje meg, hogy a TACACS-kiszolgáló nem érhető el, az eszközök az alább felsorolt helyi identitásokra kerülnek vissza. Normál működés esetén ezek a fiókok le vannak tiltva.


A következő felhasználói fiókok kilépése eszközönként:

| Eszköz | Felhasználónév | Alapértelmezett jelszó |
|---------|--------------|----------------------|
| 1. Edge | legfelső szintű | Ügyfél által hozzárendelve |
| 2. Edge | Gyökér | Ügyfél által hozzárendelve |
| Tor 1 | Gyökér | Ügyfél által hozzárendelve |
| Tor 2 | Gyökér | Ügyfél által hozzárendelve |
| BMC | Gyökér | Ügyfél által hozzárendelve |
| PDU 1 | legfelső szintű | Ügyfél által hozzárendelve |
| PDU 2 | legfelső szintű | Ügyfél által hozzárendelve |
| Sorozatszám | Gyökér | Ügyfél által hozzárendelve |
| Avocent | Root &-rendszergazda | Ügyfél által hozzárendelve |
