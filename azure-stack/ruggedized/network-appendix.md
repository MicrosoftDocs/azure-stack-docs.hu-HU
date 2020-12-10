---
title: Azure Stack hub robusztus hálózatkezelési függeléke
description: A Azure Stack hub robusztus hálózatkezelésének függelékei.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 2a5acdf50a310fe2bda89e4472c38af2c75f9487
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939752"
---
# <a name="azure-stack-hub-ruggedized-network-appendix"></a>Azure Stack hub robusztus hálózati függeléke

A (z) függelék eszköz paramétereit és azonosító adatait tartalmazza Azure Stack hub robusztus hardverhez.

## <a name="technical-device-parameters"></a>Technikai eszköz paraméterei

### <a name="dell-s5048f-on"></a>Dell S5048F – bekapcsolva

Portok: 

- 48 sebességű 25 – Gigabit Ethernet SFP28-portok. 
- Hat line-Rate 100 – Gigabit Ethernet QSFP28-portok. 
- Egy RJ45 konzol/felügyeleti port, amely RS232-jeleket jelez.
- Egy Micro-USB Type B opcionális konzol portja. 
- Egy 10/100/1000 Base-T Ethernet-port, amely felügyeleti portként van használatban. 
- Egy USB-típus a külső háttértárhoz tartozó port. 

Specifikációk

- Méret: 1RU, 1.72 "(h) x 17.1" (w) x 18 "(d) (4.4 (h) x 43.4 (w) x 45.7 (d) cm). 
- Súly: 22 lbs (9,98 kg) 
- ISO 7779 A súlyozott hangnyomásszint: 59.6 dBA, 73.4 °F (23 °C).  
- Tápegység: 100 – 240 VAC 50/60 Hz. 
- Maximális termikus kimenet: 1956 BTU/h. 
- A jelenlegi rajzolási műveletek maximális száma másodpercenként: 
  - 5.73 a/4.8 A a 100/120V váltakozó áram (AC). 
  - 2.87 a/2.4 A: 200/240V AC. 
- Maximális energiafogyasztás: 573 watt (AC). 
- Jellemző energiafogyasztás: 288 watt (AC) minden betöltött optikával.  
- Működési előírások maximális száma: 
  - Működési hőmérséklet: 32 ° – 113 °F (0 ° és 45 °C között). 
  - Működési páratartalom: 10% – 90% (RH), nem kondenzációs. 
  - A friss levegő 45 °C-nak felel meg. 
- Nem működési előírások maximális száma: 
  - Tárolási hőmérséklet: – 40 ° – 158 °F (– 40 ° – 70 °C). 
  - Tárolási páratartalom: 5% – 95% (RH), nem kondenzációs.

### <a name="dell-3048-on"></a>Dell 3048 – bekapcsolva

Portok:

- 48 line-Rate 1000BASE-T portok.  
- Négy GbE SFP + port. 
- Egy RJ45 konzol/felügyeleti port, amely RS232-jeleket jelez.  

Specifikációk

- Méret: egy RU, 1.71 "(h) x 17.09" (w) x 12.6 "(d) (4.4 (h) x 43.4 (w) x 32.0 (d) s cm).  
- Súly: 12,8 lbs (5,84 kg) egy tápegységgel, 14,8 lbs (6,74 kg) két tápegységgel. 
- ISO 7779 A-súlyozott hangnyomásszint: \< 36 DBA 78.8 °c-on (26 °c). 
- Tápegység: 90 – 264 VAC 50/60 Hz. 
  1) AC-továbbítási légáram.  
  2) AC fordított légáram. 
- Maximális termikus kimenet: 290 BTU/h. 
- A jelenlegi rajzolási műveletek maximális száma másodpercenként:  
  - \<1A, 100/120V VAC. 
  - \<0,5 a 200/240 VAC.  
- Maximális energiafogyasztás: 87 W. 
- Jellemző energiafogyasztás: 65 watt. 
- Működési előírások maximális száma:  
  - Működési hőmérséklet: 32 ° – 113 °F (0 ° és 45 °C között).  
  - Működési páratartalom: 5% – 85% (RH), nem kondenzációs.    
  - Működési magasság: 0 Ft – 10 000 Ft a tengerszint felett.  
- Nem működési előírások maximális száma: 
  - Tárolási hőmérséklet: – 40 ° – 158 °F (– 40 ° – 70 °C). 
  - Tárolási páratartalom: 5% – 95% (RH), nem kondenzációs.   

## <a name="identity"></a>Identitás

Minden hálózati eszköz úgy lett kialakítva, hogy működjön a Access-Control rendszerű (TACACS) terminálszolgáltatásokkal. A TACACS központosított identitási élményt biztosít. A TACACS-kiszolgálót a kezdeti konfiguráció részeként kell megadni.

Figyelje meg, hogy a TACACS-kiszolgáló nem érhető el, az eszközök az alább felsorolt helyi identitásokra kerülnek vissza. Normál működés esetén ezek a fiókok le vannak tiltva.

A következő felhasználói fiókok kilépése eszközönként:

| Eszköz | Felhasználónév | Alapértelmezett jelszó     |
|--------|----------|----------------------|
| Tor 1  | Gyökér     | Ügyfél által hozzárendelve |
| Tor 2  | Gyökér     | Ügyfél által hozzárendelve |
| BMC    | Gyökér     | Ügyfél által hozzárendelve |
