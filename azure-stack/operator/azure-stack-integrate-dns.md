---
title: Azure Stack Datacenter-integráció – DNS
description: Ismerje meg, hogyan integrálhatja Azure Stack DNS-t az adatközpont DNS-sel
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/09/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 05/09/2019
keywords: ''
ms.openlocfilehash: 748da2aa4391d7f28e6d4273830d8d024021bb79
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417486"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Azure Stack Datacenter-integráció – DNS

Azure Stack végpontokhoz való hozzáféréshez (**portál**, **adminportal**, **felügyelet**, **adminmanagement**stb.)  a Azure Stackon kívülről integrálnia kell a Azure Stack DNS-szolgáltatásokat azokkal a DNS-kiszolgálókkal, amelyek a Azure Stack-ben használni kívánt DNS-zónákat futtatják.

## <a name="azure-stack-dns-namespace"></a>Azure Stack DNS-névtér

A Azure Stack telepítésekor a DNS szolgáltatással kapcsolatos fontos információkat kell megadnia.


|Mező  |Leírás  |Példa|
|---------|---------|---------|
|Régió|A Azure Stack központi telepítésének földrajzi helye.|`east`|
|Külső tartomány neve|A Azure Stack-telepítéshez használni kívánt zóna neve.|`cloud.fabrikam.com`|
|Belső tartomány neve|A Azure Stack infrastruktúra-szolgáltatásaihoz használt belső zóna neve.  Ez a címtárszolgáltatás-integrált és privát (nem érhető el a Azure Stack üzemelő példányon kívülről).|`azurestack.local`|
|DNS Forwarder|A DNS-lekérdezések, DNS-zónák és-rekordok továbbítására használt DNS-kiszolgálók, amelyek a vállalati intraneten vagy a nyilvános interneten keresztül Azure Stackon kívül futnak.|`10.57.175.34`<br>`8.8.8.8`|
|Elnevezési előtag (nem kötelező)|Az a névadási előtag, amelyre a Azure Stack infrastruktúra szerepkör-példányának számítógépnevét kell megneveznie.  Ha nincs megadva, az alapértelmezett érték `azs`.|`azs`|

A Azure Stack központi telepítésének teljes tartományneve (FQDN) és a végpontok a régió paraméter és a külső tartománynév paraméter kombinációja. Az előző táblázatban szereplő példák értékeit használva a Azure Stack üzemelő példány teljes tartományneve a következő lesz:

`east.cloud.fabrikam.com`

Ilyenek például a központi telepítés egyes végpontjai a következő URL-címekhez hasonlóak:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Ha ezt a példát DNS-névteret szeretné használni egy Azure Stack központi telepítéshez, a következő feltételek szükségesek:

- A zóna `fabrikam.com` a névfeloldási követelményektől függően egy tartományregisztráló, egy belső vállalati DNS-kiszolgáló vagy mindkettő regisztrálva van.
- Az alárendelt tartomány `cloud.fabrikam.com` létezik a zónában `fabrikam.com`.
- A zónákat `fabrikam.com` üzemeltető DNS-kiszolgálók és `cloud.fabrikam.com` a Azure stack üzemelő példányból érhetők el.

Ahhoz, hogy fel tudja oldani a Azure Stack végpontok és példányok DNS-neveit a Azure Stack kívülről, integrálnia kell a külső DNS-zónát üzemeltető DNS-kiszolgálókat a Azure Stack a használni kívánt szülő zónát futtató DNS-kiszolgálókkal.

### <a name="dns-name-labels"></a>DNS-nevek címkéi

A Azure Stack támogatja a DNS-név címke nyilvános IP-címhez való hozzáadását, hogy engedélyezze a névfeloldást a nyilvános IP-címek számára. Ez kényelmes módszer lehet arra, hogy a felhasználók a Azure Stack által üzemeltetett alkalmazásokat és szolgáltatásokat elérjék a név alapján. A DNS-név címkéje némileg eltérő névteret használ, mint az infrastruktúra-végpontok. Az előző példában szereplő névtér után a DNS-nevek címkéjének névtere a következőképpen jelenik meg:

`*.east.cloudapp.cloud.fabrikam.com`

Ezért ha egy bérlő egy nyilvános IP- **SajátPr** DNS-név címkéjén lévő értéket jelez, az egy rekordot hoz létre a **SajátPr** a külső DNS-kiszolgálón található Azure stack **East.cloudapp.Cloud.fabrikam.com** . Az eredményül kapott teljes tartománynév a következőképpen jelenik meg:

`myapp.east.cloudapp.cloud.fabrikam.com`

Ha ezt a funkciót szeretné használni, és ezt a névteret használja, integrálnia kell azokat a DNS-kiszolgálókat, amelyek a külső DNS-zónát futtatják Azure Stack a használni kívánt szülő zónát futtató DNS-kiszolgálókkal. Ez egy másik névtér, mint a Azure Stack szolgáltatási végpontok névtere, ezért létre kell hoznia egy további delegálási vagy feltételes továbbítási szabályt.

A DNS-név feliratának működéséről a [DNS használata a Azure Stackban](../user/azure-stack-dns.md)című témakörben talál további információt.

## <a name="resolution-and-delegation"></a>Feloldás és delegálás

Kétféle DNS-kiszolgáló létezik:

- Egy mérvadó DNS-kiszolgáló DNS-zónákat üzemeltet. Csak az ezekben a zónákban található rekordokra irányuló DNS-lekérdezéseket válaszolja meg.
- A recursive DNS server does not host DNS zones. Minden DNS-lekérdezést megválaszol a mérvadó DNS-kiszolgálók adatait összegyűjtve.

A Azure Stack a mérvadó és a rekurzív DNS-kiszolgálókat is tartalmazza. A rekurzív kiszolgálók a belső privát zóna és a külső nyilvános DNS-zóna kivételével minden, a Azure Stack üzemelő példányhoz tartozó nevek feloldására szolgálnak. 

![Azure Stack DNS-architektúra](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Külső DNS-nevek feloldása Azure Stack

A Azure stackon kívüli végpontok DNS-neveinek feloldásához (\.például: www Bing.com) olyan DNS-kiszolgálókat kell megadnia, amelyek segítségével a Azure stack továbbíthatja azokat a DNS-kéréseket, amelyekhez a Azure stack nem mérvadó. Az üzembe helyezéshez olyan DNS-kiszolgálók szükségesek, amelyeknek a Azure Stack továbbítja a kérelmeket a központi telepítési munkalapon (a DNS-továbbító mezőben). A hibatűréshez legalább két kiszolgálót adjon meg ebben a mezőben. Ezen értékek nélkül Azure Stack üzemelő példány sikertelen lesz.

### <a name="configure-conditional-dns-forwarding"></a>Feltételes DNS-továbbítás konfigurálása

> [!IMPORTANT]
> Ez csak egy AD FS üzemelő példányra vonatkozik.

Ha engedélyezni szeretné a névfeloldást a meglévő DNS-infrastruktúrával, konfigurálja a feltételes továbbítást.

Feltételes továbbító hozzáadásához a privilegizált végpontot kell használnia.

Ehhez az eljáráshoz használjon olyan számítógépet az adatközpont-hálózaton, amely képes kommunikálni a rendszerjogosultságú végponttal Azure Stack.

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), és kapcsolódjon a privilegizált végpont IP-címéhez. Használja a hitelesítő adatokat a CloudAdmin-hitelesítéshez.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Miután kapcsolódott a Kiemelt végponthoz, futtassa a következő PowerShell-parancsot. Helyettesítse be a használni kívánt DNS-kiszolgálók tartománynevével és IP-címeivel megadott értékeket.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Azure Stack DNS-nevek feloldása kívülről Azure Stack
A mérvadó kiszolgálók azok, amelyek a külső DNS-zóna információit és a felhasználó által létrehozott zónákat használják. Integrálhatja ezeket a kiszolgálókat a Zónák delegálása vagy a feltételes továbbítás engedélyezéséhez Azure Stack DNS-nevek feloldásához Azure Stack kívülről.

## <a name="get-dns-server-external-endpoint-information"></a>DNS-kiszolgáló külső végpontjának adatainak beolvasása

A Azure Stack üzembe helyezésének a DNS-infrastruktúrával való integrálásához a következő információkra lesz szüksége:

- DNS-kiszolgálói teljes tartománynevek
- DNS server IP addresses

A Azure Stack DNS-kiszolgálókhoz tartozó teljes tartománynevek formátuma a következő:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

A minták értékeit használva a DNS-kiszolgálók teljes tartománynevei a következők:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Ezek az információk az összes Azure Stack-telepítés végén is létrejönnek egy nevű `AzureStackStampInformation.json`fájlban. Ez a fájl az üzembe helyezési virtuális gép `C:\CloudDeployment\logs` mappájában található. Ha nem biztos abban, hogy milyen értékeket használt a Azure Stack központi telepítéshez, itt érheti el az értékeket.

Ha az üzembe helyezési virtuális gép már nem érhető el vagy nem érhető el, az értékeket az emelt szintű végponthoz való csatlakozással és a `Get-AzureStackStampInformation` PowerShell-parancsmag futtatásával szerezheti be. További információ: [privilegizált végpont](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Feltételes továbbítás beállítása Azure Stackre

A Azure Stack és a DNS-infrastruktúra integrálásának legegyszerűbb és legbiztonságosabb módja a zóna feltételes továbbítása a szülő zónát futtató kiszolgálóról. Ez a módszer akkor javasolt, ha a Azure Stack külső DNS-névtérhez tartozó szülő zónát üzemeltető DNS-kiszolgálókat közvetlen vezérléssel látja el.

Ha nem ismeri a feltételes továbbítást a DNS-sel, tekintse meg a következő TechNet-cikket: [Rendeljen hozzá egy feltételes továbbítót egy tartománynévhez](https://technet.microsoft.com/library/cc794735)vagy a DNS-megoldáshoz tartozó dokumentációhoz.

Olyan esetekben, amikor a külső Azure Stack DNS-zónát úgy adta meg, hogy a vállalati tartománynévhez hasonló alárendelt tartományhoz hasonlítson, a feltételes továbbítás nem használható. A DNS-delegálást konfigurálni kell.

Példa:

- Vállalati DNS-tartománynév:`contoso.com`
- Azure Stack külső DNS-tartománynév:`azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>A külső DNS-zóna delegálása Azure Stack

Ahhoz, hogy a DNS-nevek feloldhatók legyenek Azure Stack központi telepítésen kívülről, be kell állítania a DNS-delegálást.

Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. A regisztrátor DNS-kezelés lapján szerkessze az NS-rekordokat, és cserélje le a zóna NS rekordjait a Azure Stack.

A legtöbb DNS-regisztráló megköveteli, hogy legalább két DNS-kiszolgálót adjon meg a delegálás befejezéséhez.

## <a name="next-steps"></a>További lépések

[Tűzfal-integráció](azure-stack-firewall.md)
