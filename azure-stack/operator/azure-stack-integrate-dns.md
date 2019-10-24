---
title: Azure Stack Datacenter DNS-integrációja | Microsoft Docs
description: Ismerje meg, hogyan integrálhatja Azure Stack DNS-t az adatközpont DNS-sel.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/21/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 08/21/2019
keywords: ''
ms.openlocfilehash: 1f84eeffffae3c103c33b366a5c503a907cf6715
ms.sourcegitcommit: 4a2318ad395b2a931833ccba4430d8d04cdd8819
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72780488"
---
# <a name="azure-stack-datacenter-dns-integration"></a>Azure Stack Datacenter DNS-integrációja

Ahhoz, hogy hozzáférhessenek Azure Stack-végpontokhoz, például a **portálhoz**, a **adminportal**, a **felügyelethez**és a **adminmanagement** Azure Stack KÍVÜLről, integrálnia kell a Azure stack DNS-szolgáltatásokat a DNS-zónákat üzemeltető DNS-kiszolgálókkal. a Azure Stackban kívánja használni.

## <a name="azure-stack-dns-namespace"></a>Azure Stack DNS-névtér

A Azure Stack telepítésekor a DNS-sel kapcsolatos fontos információkat kell megadnia.


|Mező  |Leírás  |Példa|
|---------|---------|---------|
|Region (Régió)|A Azure Stack központi telepítésének földrajzi helye.|`east`|
|Külső tartomány neve|A Azure Stack-telepítéshez használni kívánt zóna neve.|`cloud.fabrikam.com`|
|Belső tartomány neve|A Azure Stack infrastruktúra-szolgáltatásaihoz használt belső zóna neve. A címtár-szolgáltatás integrált és magán (nem érhető el a Azure Stack üzemelő példányon kívülről).|`azurestack.local`|
|DNS-továbbítók|A DNS-lekérdezések, DNS-zónák és a Azure Stackon kívül futtatott rekordok továbbítására használt DNS-kiszolgálók a vállalati intraneten vagy a nyilvános interneten. A DNS-továbbító értékét a telepítés után a [ **set-AzSDnsForwarder** parancsmaggal](#editing-dns-forwarder-ips) módosíthatja. 
|Elnevezési előtag (nem kötelező)|Az a névadási előtag, amelyre a Azure Stack infrastruktúra szerepkör-példányának számítógépnevét kell megneveznie.  Ha nincs megadva, az alapértelmezett érték `azs`.|`azs`|

A Azure Stack központi telepítésének teljes tartományneve (FQDN) és a végpontok a régió paraméter és a külső tartománynév paraméter kombinációja. Az előző táblázatban szereplő példák értékeit használva a Azure Stack üzemelő példány teljes tartományneve a következő lesz:

`east.cloud.fabrikam.com`

Ilyenek például a központi telepítés egyes végpontjai a következő URL-címekhez hasonlóak:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Ha ezt a példát DNS-névteret szeretné használni egy Azure Stack központi telepítéshez, a következő feltételek szükségesek:

- A zóna `fabrikam.com` a névfeloldási követelményektől függően egy tartományregisztráló, egy belső vállalati DNS-kiszolgáló vagy mindkettő regisztrálva van.
- A gyermektartomány `cloud.fabrikam.com` a zóna `fabrikam.com` alatt található.
- A zónákat üzemeltető DNS-kiszolgálók `fabrikam.com` és `cloud.fabrikam.com` elérhetők a Azure Stack telepítésből.

Ahhoz, hogy fel tudja oldani a Azure Stack végpontok és példányok DNS-neveit a Azure Stack kívülről, integrálnia kell a külső DNS-zónát üzemeltető DNS-kiszolgálókat a Azure Stack a használni kívánt szülő zónát futtató DNS-kiszolgálókkal.

### <a name="dns-name-labels"></a>DNS-nevek címkéi

A Azure Stack támogatja a DNS-név címke nyilvános IP-címhez való hozzáadását, hogy engedélyezze a névfeloldást a nyilvános IP-címek számára. A DNS-címkék kényelmes módszert biztosítanak a felhasználók számára a Azure Stack által üzemeltetett alkalmazások és szolgáltatások elérésére név szerint. A DNS-név címkéje némileg eltérő névteret használ, mint az infrastruktúra-végpontok. Az előző példában szereplő névtér után a DNS-nevek címkéjének névtere a következőképpen jelenik meg:

`*.east.cloudapp.cloud.fabrikam.com`

Ezért ha egy bérlő egy nyilvános IP- **SajátPr** DNS-név címkéjén lévő értéket jelez, az egy rekordot hoz létre a **SajátPr** a külső DNS-kiszolgálón található Azure stack **East.cloudapp.Cloud.fabrikam.com** . Az eredményül kapott teljes tartománynév a következőképpen jelenik meg:

`myapp.east.cloudapp.cloud.fabrikam.com`

Ha ezt a funkciót szeretné használni, és ezt a névteret használja, integrálnia kell azokat a DNS-kiszolgálókat, amelyek a külső DNS-zónát futtatják Azure Stack a használni kívánt szülő zónát futtató DNS-kiszolgálókkal. Ez egy másik névtér, mint a Azure Stack szolgáltatási végpontok névtere, ezért létre kell hoznia egy további delegálási vagy feltételes továbbítási szabályt.

A DNS-név feliratának működéséről a [DNS használata a Azure Stackban](../user/azure-stack-dns.md)című témakörben talál további információt.

## <a name="resolution-and-delegation"></a>Feloldás és delegálás

Kétféle DNS-kiszolgáló létezik:

- Egy mérvadó DNS-kiszolgáló DNS-zónákat üzemeltet. Csak az ezekben a zónákban található rekordokra irányuló DNS-lekérdezéseket válaszolja meg.
- A rekurzív DNS-kiszolgáló nem üzemeltet DNS-zónákat. Minden DNS-lekérdezést megválaszol a mérvadó DNS-kiszolgálók adatait összegyűjtve.

A Azure Stack a mérvadó és a rekurzív DNS-kiszolgálókat is tartalmazza. A rekurzív kiszolgálók a belső privát zóna és a külső nyilvános DNS-zóna kivételével minden, a Azure Stack üzemelő példányhoz tartozó nevek feloldására szolgálnak.

![Azure Stack DNS-architektúra](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Külső DNS-nevek feloldása Azure Stack

A Azure Stackon kívüli végpontok DNS-neveinek feloldásához (például: www \.bing. com) olyan DNS-kiszolgálókat kell megadnia, amelyekkel a Azure Stack a DNS-kérelmek továbbítására használhatók, amelyek Azure Stack nem mérvadók. Az üzembe helyezéshez olyan DNS-kiszolgálók szükségesek, amelyeknek a Azure Stack továbbítja a kérelmeket a központi telepítési munkalapon (a DNS-továbbító mezőben). A hibatűréshez legalább két kiszolgálót adjon meg ebben a mezőben. Ezen értékek nélkül Azure Stack üzemelő példány sikertelen lesz. A DNS-továbbító értékeit a telepítés után a [ **set-AzSDnsForwarder** parancsmaggal](#editing-dns-forwarder-ips) módosíthatja. 



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
- DNS-kiszolgáló IP-címei

A Azure Stack DNS-kiszolgálókhoz tartozó teljes tartománynevek formátuma a következő:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

A minták értékeit használva a DNS-kiszolgálók teljes tartománynevei a következők:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Ezek az információk az összes Azure Stack-telepítés végén is létrejönnek `AzureStackStampInformation.json` nevű fájlban. Ez a fájl a központi telepítési virtuális gép `C:\CloudDeployment\logs` mappájában található. Ha nem biztos abban, hogy milyen értékeket használt a Azure Stack központi telepítéshez, itt érheti el az értékeket.

Ha az üzembe helyezési virtuális gép már nem érhető el vagy nem érhető el, az értékeket az emelt szintű végponthoz való csatlakozással és a `Get-AzureStackStampInformation` PowerShell-parancsmag futtatásával szerezheti be. További információ: [privilegizált végpont](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Feltételes továbbítás beállítása Azure Stackre

A Azure Stack és a DNS-infrastruktúra integrálásának legegyszerűbb és legbiztonságosabb módja a zóna feltételes továbbítása a szülő zónát futtató kiszolgálóról. Ez a módszer akkor javasolt, ha a Azure Stack külső DNS-névtérhez tartozó szülő zónát üzemeltető DNS-kiszolgálókat közvetlen vezérléssel látja el.

Ha nem tudja, hogyan végezheti el a feltételes továbbítást a DNS-sel, tekintse meg a következő TechNet-cikket: [feltételes továbbító kiosztása egy tartománynévhez](https://technet.microsoft.com/library/cc794735)vagy a DNS-megoldás dokumentációja.

Olyan esetekben, amikor a külső Azure Stack DNS-zónát úgy adta meg, hogy a vállalati tartománynévhez hasonló gyermektartomány legyen, a feltételes továbbítás nem használható. A DNS-delegálást konfigurálni kell.

Példa:

- Vállalati DNS-tartománynév: `contoso.com`
- Azure Stack külső DNS-tartomány neve: `azurestack.contoso.com`

## <a name="editing-dns-forwarder-ips"></a>DNS-továbbító IP-címeinek szerkesztése

A DNS-továbbító IP-címei a Azure Stack telepítése során állíthatók be. Ha azonban a továbbítói IP-címeket valamilyen okból frissíteni kell, az értékeket szerkesztheti az emelt szintű végponthoz való csatlakozással, valamint a `Get-AzSDnsForwarder` és `Set-AzSDnsForwarder [[-IPAddress] <IPAddress[]>]` PowerShell-parancsmagok futtatásával. További információ: [privilegizált végpont](azure-stack-privileged-endpoint.md).

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>A külső DNS-zóna delegálása Azure Stack

Ahhoz, hogy a DNS-nevek feloldhatók legyenek Azure Stack központi telepítésen kívülről, be kell állítania a DNS-delegálást.

Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. A regisztrátor DNS-kezelés lapján szerkessze az NS-rekordokat, és cserélje le a zóna NS rekordjait a Azure Stack.

A legtöbb DNS-regisztráló megköveteli, hogy legalább két DNS-kiszolgálót adjon meg a delegálás befejezéséhez.

## <a name="next-steps"></a>Következő lépések

[Tűzfal-integráció](azure-stack-firewall.md)
