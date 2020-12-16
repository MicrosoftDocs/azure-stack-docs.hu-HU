---
title: A MDC Azure Stack hálózati telepítése
description: A MDC-eszköz Azure Stack hálózati telepítésének megismerése.
author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 01/17/2020
ms.openlocfilehash: 8744ddd518094026afdba678d524ca981b138610
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598657"
---
# <a name="network-deployment"></a>Hálózati telepítés

Ez a témakör a TOR-kapcsolókkal, az IP-címek hozzárendeléseivel és egyéb hálózati telepítési feladatokkal kapcsolatos hozzáférési engedélyeket ismerteti.

## <a name="plan-configuration-deployment"></a>Konfiguráció központi telepítésének megtervezése

A következő fejezetek az engedélyek és az IP-címek hozzárendeléseit fedik le.

### <a name="physical-switch-access-control-list"></a>Fizikai kapcsoló hozzáférés-vezérlési listája

A Azure Stack-megoldás érdekében a TOR-kapcsolókon Implementáljuk a hozzáférés-vezérlési listákat (ACL-eket). Ez a szakasz a biztonság megvalósításának módját ismerteti. Az alábbi táblázat a Azure Stack-megoldáson belüli összes hálózat forrásait és célhelyeit mutatja be:

![A TOR-kapcsolók hozzáférés-vezérlési listáinak ábrája](media/network-deployment/acls.png)

Az alábbi táblázat összekapcsolja az ACL-referenciákat a Azure Stack hálózatokkal.

| BMC mgmt belső                            | A forgalom csak belső használatra korlátozódik.                                                                                                                                      |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| BMC mgmt – külső                            | Az ACL engedélyezheti a hozzáférését a szegélyen kívüli eszközön.                                                                                                                            |   |   |   |   |   |
| Bővített Storage mgmt                        | Dedikált felügyeleti felületek a kiterjesztett tárolási rendszerhez                                                                                                           |   |   |   |   |   |
| Mgmt kapcsoló                                  | Dedikált kapcsoló felügyeleti felületek.                                                                                                                                   |   |   |   |   |   |
| "Azure Stack infrastruktúra"                 | Azure Stack infrastruktúra-szolgáltatások és virtuális gépek korlátozott hálózata                                                                                                          |   |   |   |   |   |
| Azure Stack infrastruktúra nyilvános (PEP/ERCS) | Azure Stack védett végpont, vész-helyreállítási konzol kiszolgálója. Az ügyfél megnyithatja az ACL-t, hogy engedélyezze a forgalmat az adatközpont-felügyeleti hálózatán.                        |   |   |   |   |   |
| Tor1,Tor2 RouterIP                           | A SLB és Switch/router közötti BGP-társításhoz használt kapcsoló visszacsatolási felülete. Az ügyfélnek meg kell bíznia ezen IP-címek tűzfalon való kikapcsolásával a határon. |   |   |   |   |   |
| Storage                                      | Nem a régión kívülre irányított magánhálózati IP-címek                                                                                                                             |   |   |   |   |   |
| Belső VIP-címek                                | Nem a régión kívülre irányított magánhálózati IP-címek                                                                                                                             |   |   |   |   |   |
| Nyilvános VIP-címek                                  | A hálózati vezérlő által kezelt bérlői hálózati címtartomány.                                                                                                           |   |   |   |   |   |
| Nyilvános rendszergazdai VIP-címek                            | A bérlői készletben lévő címek kis részhalmaza, amelyek a Internal-VIPs és az Azure Stack infrastruktúrával való kommunikációhoz szükségesek                                                    |   |   |   |   |   |
| Engedélyezett hálózatok                           | Ügyfél által definiált hálózat.                                                                                                                                                 |   |   |   |   |   |
| 0.0.0.0                                      | Azure Stack 0.0.0.0 szempontból a szegély eszköz.                                                                                                         |   |   |   |   |   |
| **Engedélyezése**                                   | Engedélyezve van a forgalom engedélyezése, de az SSH-hozzáférés alapértelmezés szerint le van tiltva.                                                                                                           |   |   |   |   |   |
| **Nincs útvonal**                                     | Az útvonalak nincsenek propagálva a Azure Stack környezeten kívül.                                                                                                         |   |   |   |   |   |
| **MUX ACL**                                      | Azure Stack MUX ACL-eket használunk.                                                                                                                                       |   |   |   |   |   |
| **N.A.**                                          | Nem része a VLAN ACL-nek.                                                                                                                                                 |   |   |   |   |   |
|                                              |                                                                                                                                                                           |   |   |   |   |   |

### <a name="ip-address-assignments"></a>IP-címek hozzárendelései

A központi telepítés munkalapon a következő hálózati címeket kell megadnia a Azure Stack telepítési folyamatának támogatásához. Az üzembe helyezési csapat a központi telepítési munkalap eszköz használatával bontja ki az IP-hálózatokat a rendszer által igényelt kisebb hálózatokra.

Ebben a példában az üzembe helyezési munkalap hálózati beállítások lapját fogjuk kitölteni a következő értékekkel:

-   BMC-hálózat: 10.193.132.0/27

-   Magánhálózat hálózati & belső VIP-címek: 11.11.128.0/20

-   Infrastruktúra-hálózat: 12.193.130.0/24

-   Nyilvános virtuális IP-(VIP-) hálózat: 13.200.132.0/24

-   Infrastruktúra-hálózat váltása: 10.193.132.128/26

Amikor futtatja a központi telepítési munkalap eszköz létrehozási funkcióját, a két új lapot hoz létre a számolótáblán. Az első lap az alhálózat összefoglalása, és azt mutatja, hogy a rendszer által igényelt összes hálózat létrehozásához hogyan oszlanak meg a alhálózatok. Az alábbi példában csak az ezen a lapon található oszlopok egy részhalmaza látható. A tényleges eredmény további részleteket tartalmaz a felsorolt hálózatokról:

| **Kiszolgálószekrény** | **Alhálózat típusa** | **Név**                                   | **IPv4-alhálózat**   | **IPv4-címek** |
|----------|-----------------|--------------------------------------------|-------------------|--------------------|
| Szegély   | P2P-kapcsolat        | P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 | 4                  |
| Szegély   | P2P-kapcsolat        | P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 | 4                  |
| Szegély   | P2P-kapcsolat        | P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 | 4                  |
| Szegély   | P2P-kapcsolat        | P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 | 4                  |
| Szegély   | P2P-kapcsolat        | P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 | 4                  |
| Szegély   | P2P-kapcsolat        | P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 | 4                  |
| Rack1    | Visszacsatolási        | Loopback0_Rack1_TOR1                       | 10.193.132.152/32 | 1                  |
| Rack1    | Visszacsatolási        | Loopback0_Rack1_TOR2                       | 10.193.132.153/32 | 1                  |
| Rack1    | Visszacsatolási        | Loopback0_Rack1_BMC                        | 10.193.132.154/32 | 1                  |
| Rack1    | P2P-kapcsolat        | P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 | 4                  |
| Rack1    | P2P-kapcsolat        | P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 | 4                  |
| Rack1    | VLAN            | BMCMgmt                                    | 10.193.132.0/27   | 32                 |
| Rack1    | VLAN            | SwitchMgmt                                 | 10.193.132.168/29 | 8                  |
| Rack1    | VLAN            | CL01-RG01-SU01-Storage                     | 11.11.128.0/25    | 128                |
| Rack1    | VLAN            | CL01-RG01-SU01-infra                       | 12.193.130.0/24   | 256                |
| Rack1    | Egyéb           | CL01-RG01-SU01-VIP-CÍMEK                        | 13.200.132.0/24   | 256                |
| Rack1    | Egyéb           | CL01-RG01-SU01-InternalVIPS                | 11.11.128.128/25  | 128                |

A második lap az **IP-címek használata** , és azt mutatja, hogyan használják az IP-címeket:

#### <a name="bmc-network"></a>BMC-hálózat

A BMC-hálózat címtartományhatárrá legalább egy/26 hálózat szükséges. Az átjáró a hálózat első IP-címét, majd az állványon található BMC-eszközöket fogja használni. A hardver életciklus-állomása több, a hálózaton hozzárendelt címmel rendelkezik, és az állvány üzembe helyezéséhez, figyeléséhez és támogatásához használható. Ezek az IP-címek 3 csoportba vannak osztva: DVM, InternalAccessible és ExternalAccessible.

- Rack: Rack1         
- Név: BMCMgmt      

| **A jog birtokosa**      | **IPv4-címek** |
|----------------------|------------------|
| Network (Hálózat)              | 10.193.132.0     |
| Átjáró              | 10.193.132.1     |
| HLH – BMC              | 10.193.132.2     |
| AzS-Node01           | 10.193.132.3     |
| AzS-Node02           | 10.193.132.4     |
| AzS-Node03           | 10.193.132.5     |
| AzS-Node04           | 10.193.132.6     |
| ExternalAccessible – 1 | 10.193.132.19    |
| ExternalAccessible – 2 | 10.193.132.20    |
| ExternalAccessible – 3 | 10.193.132.21    |
| ExternalAccessible – 4 | 10.193.132.22    |
| ExternalAccessible – 5 | 10.193.132.23    |
| InternalAccessible – 1 | 10.193.132.24    |
| InternalAccessible – 2 | 10.193.132.25    |
| InternalAccessible – 3 | 10.193.132.26    |
| InternalAccessible – 4 | 10.193.132.27    |
| InternalAccessible – 5 | 10.193.132.28    |
| CL01-RG01-SU01-DVM00 | 10.193.132.29    |
| HLH – OPERÁCIÓS RENDSZER               | 10.193.132.30    |
| Adás            | 10.193.132.31    |

#### <a name="storage-network"></a>Tárolóhálózat

A Storage-hálózat egy magánhálózat, és nem az állványon túli útvonalra van szánva. Ez a magánhálózati címtartományhatárrá első fele, amelyet az alábbi táblázatban látható módon az elosztott kapcsoló használ. Az átjáró az alhálózat első IP-címe. A belső virtuális magánhálózatok második fele a Azure Stack SLB által felügyelt címek privát készlete, nem jelenik meg az IP-címek használata lapon. Ezek a hálózatok támogatják a Azure Stack és a TOR-kapcsolók hozzáférés-vezérlési listáival, amelyek megakadályozzák, hogy ezek a hálózatok a megoldáson kívül legyenek meghirdetve és/vagy elérhetők.

- Rack: Rack1                                    
- Név: CL01-RG01-SU01-Storage 

| **A jog birtokosa**              | **IPv4-címek** |
|------------------------------|------------------|
| Network (Hálózat)                      | 11.11.128.0      |
| Átjáró                      | 11.11.128.1      |
| TOR1                         | 11.11.128.2      |
| TOR2                         | 11.11.128.3      |
| Adás                    | 11.11.128.127    |

#### <a name="azure-stack-infrastructure-network"></a>Infrastruktúra-hálózat Azure Stack

Az infrastruktúra-hálózat címtartományhatárrá egy/24 hálózatra van szükség, és ez továbbra is a/24, miután a telepítési munkalap eszköz fut. Az átjáró az alhálózat első IP-címe lesz.

- Rack: Rack1               
- Név: CL01-RG01-SU01-infra 

| **A jog birtokosa**            | **IPv4-címek** |
|----------------------------|------------------|
| Network (Hálózat)                    | 12.193.130.0     |
| Átjáró                    | 12.193.130.1     |
| TOR1                       | 12.193.130.2     |
| TOR2                       | 12.193.130.3     |
| Adás                  | 12.193.130.255   |

#### <a name="switch-infrastructure-network"></a>Infrastruktúra-hálózat váltása

Az infrastruktúra-hálózat több hálózatra van bontva, amelyet a fizikai kapcsoló infrastruktúrája használ. Ez eltér a Azure Stack infrastruktúrától, amely csak a Azure Stack szoftvert támogatja. A Switch infra Network csak a fizikai kapcsoló infrastruktúráját támogatja. Az infra által támogatott hálózatok a következők:

| **Név**                                   | **IPv4-alhálózat**   |
|--------------------------------------------|-------------------|
| P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 |
| P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 |
| P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 |
| P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 |
| P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 |
| P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 |
| Loopback0_Rack1_TOR1                       | 10.193.132.152/32 |
| Loopback0_Rack1_TOR2                       | 10.193.132.153/32 |
| Loopback0_Rack1_BMC                        | 10.193.132.154/32 |
| P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 |
| P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 |
| SwitchMgmt                                 | 10.193.132.168/29 |
|                                            |                   |

-   Pont – pont (P2P): ezek a hálózatok lehetővé teszik az összes kapcsoló közötti kapcsolat használatát. Az alhálózat mérete egy/30 hálózat minden egyes P2P számára. A legalacsonyabb IP-címet a rendszer mindig a felsőbb rétegbeli (Észak) eszközhöz rendeli hozzá a veremben.

-   Visszacsatolás: ezek a címek a rackben használt egyes kapcsolókhoz rendelt/32 hálózatok. A szegélyek eszközei nem rendelkeznek visszacsatolási ponttal, mert nem várhatóan a Azure Stack megoldás részét képezik.

-   Az mgmt vagy a Switch Management váltása: Ez a/29 hálózat támogatja a kapcsolók dedikált felügyeleti felületét az állványon. Az IP-címeket a következőképpen rendeli hozzá a rendszer: Ez a tábla a telepítési munkalap IP-cím használata lapján is megtalálható:

- Rack: Rack1      
- Név: SwitchMgmt

| **A jog birtokosa**  | **IPv4-címek** |
|------------------|------------------|
| Network (Hálózat)          | 10.193.132.168   |
| Átjáró          | 10.193.132.169   |
| TOR1             | 10.193.132.170   |
| TOR2             | 10.193.132.171   |
| Adás        | 10.193.132.175   |

## <a name="prepare-environment"></a>A környezet előkészítése

A hardver életciklusára szolgáló gazdagép rendszerképe tartalmazza a fizikai hálózati kapcsoló konfigurációjának létrehozásához szükséges Linux-tárolót.

A legújabb partner-telepítési eszközkészlet tartalmazza a tároló legújabb lemezképét.
A hardver életciklus-állomásán található tároló képe lecserélhető, ha szükség van egy frissített kapcsoló konfigurációjának előállítására.

A következő lépésekkel frissítheti a tároló rendszerképét:

1.  A tároló rendszerképének letöltése

2.  A tároló rendszerképének cseréje a következő helyre

## <a name="generate-configuration"></a>Konfiguráció készítése

Itt végigvezeti a JSON-fájlok és a hálózati kapcsoló konfigurációs fájljainak létrehozásának lépésein:

1.  Az üzembe helyezési munkalap megnyitása

2.  Az összes kötelező mező kitöltése az összes lapon

3.  Hívja meg a "generált" függvényt a központi telepítés munkalapon.  
    A létrehozott IP-alhálózatok és hozzárendelések megjelenítéséhez két további lap jön létre.

4.  Tekintse át az összes adatát, és a megerősítést követően hívja meg az "export" függvényt.  
    A rendszer arra kéri, hogy adjon meg egy mappát, amelyben a JSON-fájlokat menteni fogja.

5.  Hajtsa végre a tárolót a Invoke-SwitchConfigGenerator.ps1 használatával. Ehhez a parancsfájlhoz egy emelt szintű PowerShell-konzolra van szükség, és a következő paramétereket kell végrehajtania.

    -   ContainerName – a kapcsoló konfigurációit létrehozó tároló neve.

    -   ConfigurationData – a telepítési munkalapon exportált fájl ConfigurationData.jselérési útja.

    -   Outputdirectory paraméterben – a kimeneti könyvtár elérési útja.

    -   Offline – azt jelzi, hogy a parancsfájl offline módban fut.

    ```powershell
    C:\WINDOWS\system32> .\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\ConfigurationData.json -OutputDirectory c:\temp -Offline
    ```

A szkript befejezésekor a rendszer létrehoz egy zip-fájlt a munkalapon használt előtaggal. 

```console
C:\WINDOWS\system32> .\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\ConfigurationData.json -OutputDirectory c:\temp -Offline                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
Seconds : 2
Section : Validation
Step    : WindowsRequirement
Status  : True
Detail  : @{CurrentImage=10.0.18363.0}


Seconds : 2
Section : Validation
Step    : DockerService
Status  : True
Detail  : @{Status=Running}


Seconds : 9
Section : Validation
Step    : DockerSetup
Status  : True
Detail  : @{CPU=4; Memory=4139085824; OS=Docker Desktop; OSType=linux}


Seconds : 9
Section : Validation
Step    : DockerImage
Status  : True
Detail  : @{Container=generalonrampacr.azurecr.io/master:1.1910.78.1}


Seconds : 10
Section : Run
Step    : Container
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c; ExternalPort=32768}


Seconds : 38
Section : Generate
Step    : Config
Status  : True
Detail  : @{OutputFile=c:\temp\N22R19.zip}


Seconds : 38
Section : Exit
Step    : StopContainer
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c}
```

### <a name="custom-configuration"></a>Egyéni konfiguráció

Módosíthatja a Azure Stack kapcsoló konfigurációjának néhány környezeti beállítását. A sablonban megváltoztatható beállítások közül választhat. Ez a cikk ismerteti ezeket a testreszabható beállításokat, valamint azt, hogy a módosítások milyen hatással lehetnek a Azure Stackra. A beállítások közé tartozik a jelszó frissítése, a syslog-kiszolgáló, az SNMP-figyelés, a hitelesítés és a hozzáférés-vezérlési lista.

Az Azure Stack-megoldás üzembe helyezése során az eredeti berendezésgyártó (OEM) létrehozza és alkalmazza a kapcsoló konfigurációját mind a beléptetési, mind a BMC-hez. Az OEM a Azure Stack Automation eszköz használatával ellenőrzi, hogy a szükséges konfigurációk megfelelően vannak-e beállítva az eszközökön. A konfiguráció a Azure Stack telepítési munkalapon található információk alapján történik. 

>[!NOTE]
>**Ne módosítsa a** konfigurációt a számítógépgyártó vagy a Microsoft Azure stack mérnöki csapat beleegyezike nélkül. A hálózati eszköz konfigurációjának módosítása jelentős hatással lehet a Azure Stack-példány hálózati problémáinak működésére vagy hibaelhárítására. Ha többet szeretne megtudni ezekről a függvényekről a hálózati eszközön, hogyan hajthatja végre ezeket a módosításokat, forduljon az OEM-szolgáltatóhoz vagy a Microsoft ügyfélszolgálatához. A SZÁMÍTÓGÉPGYÁRTÓ rendelkezik az Automation eszköz által létrehozott konfigurációs fájllal a Azure Stack üzembe helyezési munkalap alapján. 

Vannak azonban olyan értékek, amelyek hozzáadhatók, eltávolíthatók vagy módosíthatók a hálózati kapcsolók konfigurációjában.

#### <a name="password-update"></a>Jelszó frissítése

Az operátor bármikor frissítheti a hálózati kapcsolók bármelyik felhasználójának jelszavát. Nincs szükség a Azure Stack rendszerre vonatkozó információk módosítására, vagy a titkok elforgatásának lépéseire Azure Stackban.

#### <a name="syslog-server"></a>Syslog-kiszolgáló

A kezelők átirányítják a kapcsoló naplóit egy syslog-kiszolgálóra az adatközpontjában.
Ezzel a konfigurációval ellenőrizheti, hogy egy adott időpontból származó naplók használhatók-e hibaelhárításhoz. Alapértelmezés szerint a rendszer a naplókat a kapcsolókon tárolja. a naplók tárolásának kapacitása korlátozott. Tekintse át a hozzáférés-vezérlési lista frissítései szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

#### <a name="snmp-monitoring"></a>SNMP-figyelés

Az operátor konfigurálhatja a hálózati eszközök figyelésére és a trapek küldését egy hálózati figyelő alkalmazásba az adatközpontban. Biztonsági okokból a SNMPv3-t használja, mivel ez a v2-nél biztonságosabb. A szükséges MIB és konfigurációval kapcsolatban forduljon az OEM-hardvereszközökhöz.
Tekintse át a hozzáférés-vezérlési lista frissítései szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

#### <a name="authentication"></a>Hitelesítés

Az operátor a hálózati eszközök hitelesítésének kezeléséhez RADIUS-vagy TACACS konfigurálhat. A támogatott módszerekhez és konfigurációhoz forduljon az OEM-hardveres szolgáltatóhoz. Tekintse át a hozzáférés-vezérlési lista frissítései szakaszt, és tekintse át, hogyan konfigurálhatja a Switch Management-hozzáférés engedélyeit.

#### <a name="access-control-list-updates"></a>Hozzáférés-vezérlési lista frissítései

Az operátor módosíthatja a hozzáférés-vezérlési listák (ACL-ek) egy részét, hogy lehetővé tegye a hálózati eszközök felügyeleti felületéhez és a hardveres életciklus-gazdagéphez (HLH) való hozzáférést egy megbízható adatközpont hálózati tartományában. A kezelő kiválaszthatja, hogy melyik összetevő érhető el, és honnan. A hozzáférés-vezérlési listával az operátor engedélyezheti a felügyeleti Jumpbox egy adott hálózati tartományon belül, hogy hozzáférjenek a Switch Management felületéhez, valamint a HLH operációs rendszerhez és a HLH BMC-hez.

További részletek: a [fizikai kapcsoló hozzáférés-vezérlési listája](#physical-switch-access-control-list).

#### <a name="tacacs-radius-and-syslog"></a>TACACS, RADIUS és syslog

A Azure Stack megoldás nem lesz elküldve TACACS vagy RADIUS-megoldással az olyan eszközök hozzáférés-vezérléséhez, mint a kapcsolók és az útválasztók, illetve a rendszernapló-naplók rögzítésére szolgáló syslog megoldás, de ezek az eszközök támogatják ezeket a szolgáltatásokat.
Ha egy meglévő TACACS, RADIUS-és/vagy syslog-kiszolgálóval szeretne integrálni a környezetében, egy extra fájlt adunk meg a hálózati kapcsoló konfigurációjában, amely lehetővé teszi a mérnök számára, hogy testreszabja a váltást az ügyfél igényeinek megfelelően.

## <a name="next-steps"></a>További lépések

[Hálózatintegráció](network-integration.md)
