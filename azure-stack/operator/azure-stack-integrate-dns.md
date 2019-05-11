---
title: Az Azure Stack adatközpont integrációja - DNS
description: 'Útmutató: Azure Stack DNS integrálhatók az adatközponttaql DNS'
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/09/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 05/09/2019
keywords: ''
ms.openlocfilehash: a0b41e88fb7c56eec83b4e9e60f30d4f2f16cb4f
ms.sourcegitcommit: 2b6a0b3b4dc63c26df3d0535d630d640ff232fb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521325"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Az Azure Stack adatközpont integrációja - DNS

A érhetik el az Azure Stack-végpontok (**portál**, **adminportal**, **felügyeleti**, **adminmanagement**stb.)  a külső Azure Stack az Azure Stack DNS szolgáltatások integrálását a DNS-kiszolgálók, amelyek a DNS-zónák az Azure Stackben használni kívánt kell.

## <a name="azure-stack-dns-namespace"></a>Azure Stack DNS namespace

Ön szükséges néhány, az Azure Stack üzembe helyezése során a DNS kapcsolatos fontos információkat tartalmaznak.


|Mező  |Leírás  |Példa|
|---------|---------|---------|
|Régió|Az Azure Stack üzemelő példányához földrajzi helye.|`east`|
|External Domain Name|Az Azure Stack üzembe helyezéshez használni kívánt a zóna nevét.|`cloud.fabrikam.com`|
|Belső tartomány neve|Az infrastruktúra-szolgáltatások az Azure Stackben használt belső zóna nevét.  Címtárszolgáltatás integrált és személyes (nem érhető el a kívül az Azure Stack üzembe helyezés).|`azurestack.local`|
|DNS Forwarder|Segítségével az DNS-lekérdezések, DNS-zónák és -rekordok Azure Stack, a vállalati intranet vagy a nyilvános interneten kívül üzemeltetett DNS-kiszolgálók.|`10.57.175.34`<br>`8.8.8.8`|
|Elnevezési előtag (opcionális)|Azt szeretné, hogy az Azure Stack infrastruktúra példány gép szerepkörneveket kell elnevezési előtag.  Ha nincs megadva, az alapértelmezett érték `azs`.|`azs`|

A teljesen minősített tartományneve (FQDN) az Azure Stack üzembe helyezés és a végpontok a régiót és a külső tartománynév paraméter együttes használata. A teljes tartománynév, az üzembe helyezés az Azure Stack használatával a példákban szereplő értékek alapján az előző táblázatban, a következő név a következő lesz:

`east.cloud.fabrikam.com`

Példák a végpontok az üzembe helyezés, a következő URL-címek jelenne meg:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Ebben a példában DNS-névteret használja az Azure Stack üzemelő példányához, az alábbi feltételek szükségesek:

- A zóna `fabrikam.com` regisztrálva van a tartományregisztráló, a belső vállalati DNS-kiszolgálók vagy mindkét, attól függően, a névfeloldási követelményeit.
- A gyermektartomány `cloud.fabrikam.com` létezik a zóna `fabrikam.com`.
- A zónákat a DNS-kiszolgálók `fabrikam.com` és `cloud.fabrikam.com` érhető el az Azure Stack-telepítésből.

Tudják feloldani a DNS-nevek Azure Stack-végpontok és a külső Azure Stack, a DNS-kiszolgálók, amelyek a külső DNS-zónát az Azure Stack a használni kívánt szülő zónát futtató DNS-kiszolgálókkal integrálnia kell.

### <a name="dns-name-labels"></a>DNS name labels

DNS hozzáadása az Azure Stack támogatja, hogy a névfeloldás a nyilvános IP-címeket nyilvános IP-cím felirat neve. Ez lehet egy kényelmes módot kínálnak a felhasználók alkalmazásokat és név szerint az Azure Stack-ben üzemeltetett szolgáltatások eléréséhez. A DNS-névcímke használja, mint az infrastruktúra-végpontokra némileg eltérő névtér. Az előző példa-névteret, az alábbi DNS-név címke névterét a következőképpen jelenik meg:

`*.east.cloudapp.cloud.fabrikam.com`

Ezért ha egy bérlő azt jelzi, hogy egy érték **Myapp** DNS neve felirat mezőjében egy nyilvános IP-cím erőforrás, egy A rekordot hoz létre **myapp** zónában **east.cloudapp.cloud.fabrikam.com**  az Azure Stack külső DNS-kiszolgálón. Az eredményül kapott teljesen minősített tartománynevét a következőképpen jelenik meg:

`myapp.east.cloudapp.cloud.fabrikam.com`

Ha azt szeretné, ez a funkció használatát, és használja ezt a névteret, a DNS-kiszolgálók, amelyek a külső DNS-zónát az Azure Stack, amelyek a szülőzónában, valamint a használni kívánt DNS-kiszolgálókkal kell integrálni. Erre azért, mint a névteret, az Azure Stack Szolgáltatásvégpontok másik névteret, hogy létre kell hoznia egy további delegálás vagy feltételes továbbítás szabály.

A DNS-név címke működésével kapcsolatos további információkért lásd: [DNS használata az Azure Stackben](../user/azure-stack-dns.md).

## <a name="resolution-and-delegation"></a>Feloldás és delegálás

Kétféle DNS-kiszolgáló létezik:

- Egy mérvadó DNS-kiszolgáló DNS-zónákat üzemeltet. Csak az ezekben a zónákban található rekordokra irányuló DNS-lekérdezéseket válaszolja meg.
- A recursive DNS server does not host DNS zones. Minden DNS-lekérdezést megválaszol a mérvadó DNS-kiszolgálók adatait összegyűjtve.

Az Azure Stack mérvadó is tartalmaz, és a rekurzív DNS-kiszolgálók. A rekurzív kiszolgálók névfeloldását minden, a kivételével a belső, saját zóna és a külső nyilvános DNS-zónát, hogy az Azure Stack üzemelő példányához szolgálnak. 

![Azure Stack DNS-architektúra](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Az Azure Stack külső DNS-nevek feloldása

Azure Stack kívüli végpontok DNS-nevek feloldására (például: www.bing.com), meg kell adnia a DNS-kiszolgálók az Azure Stack használatával, amelynek az Azure Stack nem mérvadó DNS-kérelmeket továbbítja. A telepítéshez szükséges DNS-kiszolgálók az Azure Stack továbbítja a kérelmeket az üzembehelyezési munkalap (a DNS-továbbító mezőben). A hibatűrés legalább két kiszolgálót ebben a mezőben adja meg. Ezek az értékek nélkül Azure Stack üzembe helyezés sikertelen lesz.

### <a name="configure-conditional-dns-forwarding"></a>Feltételes DNS-továbbító konfigurálása

> [!IMPORTANT]
> Ez csak egy AD FS üzembe helyezése vonatkozik.

Ahhoz, hogy a névfeloldás a meglévő DNS-infrastruktúrával, konfigurálja a feltételes továbbítás.

Adja hozzá a feltételes továbbítók, a kiemelt jogosultságú végpontot kell használnia.

Ebben az eljárásban az Adatközpont-hálózatát, amely képes kommunikálni az Azure Stack a rendszerjogosultságú végpont számítógépet használni.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), és csatlakozzon a rendszerjogosultságú végpont IP-címét. A hitelesítő adatok használata a CloudAdmin hitelesítéshez.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. A kiemelt végponthoz való csatlakozás után futtassa a következő PowerShell-parancsot. Helyettesítse be a tartomány neve és IP-címek a használni kívánt DNS-kiszolgálók megadott mintaértékeket.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Külső Azure Stack az Azure Stack DNS-nevek feloldása
A mérvadó kiszolgálók, amelyekre a a külső DNS-zóna adatainak tárolásához, és minden olyan felhasználó által létrehozott zónák. Ezek a kiszolgálók zónadelegálás vagy feltételes továbbítás külső Azure Stack az Azure Stack DNS névfeloldás engedélyezése integrálható.

## <a name="get-dns-server-external-endpoint-information"></a>DNS-kiszolgáló külső végpont információk lekérése

Az Azure Stack üzemelő példányához integrálása a DNS-infrastruktúra, szüksége van a következő információkat:

- DNS-kiszolgáló teljesen minősített tartománynevek
- DNS server IP addresses

Az Azure Stack DNS-kiszolgálók teljes tartományneveit formátuma a következő:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

A minta értékeivel, a teljes tartományneveket, a DNS-kiszolgálók vannak:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Ezt az információt is létrehoz egy fájlt a központi telepítések Azure Stack végén `AzureStackStampInformation.json`. Ez a fájl a `C:\CloudDeployment\logs` mappát a telepítési virtuális gép. Ha nem biztos abban, hogy milyen értékeket használt az Azure Stack üzembe helyezéshez, itt beszerezheti az értékeket.

Ha az üzembe helyezés virtuális gép már nem érhető el, vagy nem érhető el, úgy szerezhet az értékeket a kiemelt végponthoz csatlakozik, és fut a `Get-AzureStackStampInformation` PowerShell-parancsmagot. További információkért lásd: [kiemelt végponthoz](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Feltételes továbbítás az Azure Stackhez beállítása

A legegyszerűbb és legbiztonságosabb módját, az Azure Stack integrálható a DNS-infrastruktúra ehhez a zóna feltételes továbbítás a szülőzónát futtató kiszolgálón. Ez a megközelítés akkor ajánlott, ha rendelkezik a DNS-kiszolgálók közvetlenül lehet szabályozni, amelyeken a szülőzónában az Azure Stack külső DNS-névtérben.

Ha még nem ismeri a szeretne feltételes DNS-továbbítást, tekintse meg a következő TechNet-cikk: [Rendelje hozzá a feltételes továbbítók tartománynév](https://technet.microsoft.com/library/cc794735), vagy a DNS-megoldás adott dokumentációjában.

Olyan esetekben, ahol a külső Azure Stack DNS-zónához a következő módon a vállalati tartománynév gyermektartomány megadott feltételes továbbítás nem használható. DNS-delegálást konfigurálni kell.

Példa:

- Vállalati DNS-tartománynév: `contoso.com`
- Az Azure Stack külső DNS-tartománynév számára: `azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>A külső DNS-zónáját az Azure Stack delegálása

DNS-nevek kívül az Azure Stack üzemelő példányához feloldhatónak lennie meg kell DNS-delegálás beállítása.

Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. A regisztráló DNS-kezelési oldalán szerkessze a Névkiszolgálói rekordokat, és cserélje le a Névkiszolgálói rekordokat a zóna azokat az Azure Stackben.

A legtöbb DNS-regisztráló szervezetek esetén meg kell adni legalább két DNS-kiszolgáló a delegálás befejezéséhez.

## <a name="next-steps"></a>További lépések

[Tűzfal-integráció](azure-stack-firewall.md)
