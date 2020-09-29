---
title: Azure Stack hub Datacenter DNS-integráció
description: Ismerje meg, hogyan integrálhatja Azure Stack hub DNS-t az adatközpont DNS szolgáltatásával.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 04/10/2020
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 08/21/2019
ms.openlocfilehash: be6ea1e8dbf8b17e02a4117f5f2d20cb9cfbbcfe
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488722"
---
# <a name="azure-stack-hub-datacenter-dns-integration"></a>Azure Stack hub Datacenter DNS-integráció

Ahhoz, hogy hozzáférhessen Azure Stack hub-végpontokhoz, például a **portálhoz**, a **adminportal**, a **felügyelethez**és a **adminmanagement** kívülről Azure stack Hub-on, integrálnia kell a Azure stack hub DNS-szolgáltatásait azokkal a DNS-kiszolgálókkal, amelyek az Azure stack hub-ban használni kívánt DNS-zónákat futtatják.

## <a name="azure-stack-hub-dns-namespace"></a>Azure Stack hub DNS-névtere

A Azure Stack hub telepítésekor a DNS szolgáltatással kapcsolatos fontos információkat kell megadnia.


|Mező  |Leírás  |Példa|
|---------|---------|---------|
|Region|Az Azure Stack hub üzembe helyezésének földrajzi helye.|`east`|
|Külső tartomány neve|Az Azure Stack hub üzembe helyezéséhez használni kívánt zóna neve.|`cloud.fabrikam.com`|
|Belső tartomány neve|Az Azure Stack hub infrastruktúra-szolgáltatásaihoz használt belső zóna neve. A címtár-szolgáltatás integrált és magánjellegű (nem érhető el az Azure Stack hub üzembe helyezésén kívül).|`azurestack.local`|
|DNS-továbbítók|Azok a DNS-kiszolgálók, amelyek a DNS-lekérdezések, a DNS-zónák és a Azure Stack hub rendszeren kívül futtatott rekordok továbbítására szolgálnak a vállalati intraneten vagy a nyilvános interneten. A DNS-továbbító értékét a telepítés után a [ **set-AzSDnsForwarder** parancsmaggal](#editing-dns-forwarder-ips) módosíthatja. 
|Elnevezési előtag (nem kötelező)|Az a névadási előtag, amelyre az Azure Stack hub-infrastruktúra szerepkör-példányához tartozó számítógépnévnek szüksége van.  Ha nincs megadva, az alapértelmezett érték `azs` .|`azs`|

A Azure Stack hub központi telepítésének teljes tartományneve (FQDN) és a végpontok a régió paraméter és a külső tartománynév paraméter kombinációja. Az előző táblázatban szereplő példák értékeit használva a Azure Stack hub központi telepítésének teljes tartományneve a következő lesz:

`east.cloud.fabrikam.com`

Ilyenek például a központi telepítés egyes végpontjai a következő URL-címekhez hasonlóak:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Ha ezt a példát DNS-névteret szeretné használni egy Azure Stack hub-telepítéshez, a következő feltételek szükségesek:

- A zóna a névfeloldási `fabrikam.com` követelményektől függően egy tartományregisztráló, egy belső vállalati DNS-kiszolgáló vagy mindkettő regisztrálva van.
- Az alárendelt tartomány `cloud.fabrikam.com` létezik a zónában `fabrikam.com` .
- A zónákat üzemeltető DNS-kiszolgálók `fabrikam.com` és `cloud.fabrikam.com` a Azure stack hub üzemelő példányból érhetők el.

Ahhoz, hogy fel tudja oldani a Azure Stack hub-végpontok és-példányok DNS-neveit Azure Stack hub-on kívülről, integrálnia kell azokat a DNS-kiszolgálókat, amelyek a Azure Stack hub külső DNS-zónáját futtatják a használni kívánt szülő zónát futtató DNS-kiszolgálókkal.

### <a name="dns-name-labels"></a>DNS-nevek címkéi

Azure Stack hub támogatja a DNS-név címke nyilvános IP-címhez való hozzáadását, hogy engedélyezze a névfeloldást a nyilvános IP-címek számára. A DNS-címkék kényelmes módszert biztosítanak a felhasználóknak a Azure Stack hub-ban üzemeltetett alkalmazások és szolgáltatások elérésére név szerint. A DNS-név címkéje némileg eltérő névteret használ, mint az infrastruktúra-végpontok. Az előző példában szereplő névtér után a DNS-nevek címkéjének névtere a következőképpen jelenik meg:

`*.east.cloudapp.cloud.fabrikam.com`

Ezért ha egy bérlő egy nyilvános IP-cím erőforrás DNS- **SajátPr** mezőjében egy értéket jelez, az egy rekordot hoz létre a **sajátpr** számára az Azure stack hub külső DNS-kiszolgálójának **East.cloudapp.Cloud.fabrikam.com** . Az eredményül kapott teljes tartománynév a következőképpen jelenik meg:

`myapp.east.cloudapp.cloud.fabrikam.com`

Ha szeretné kihasználni ezt a funkciót, és ezt a névteret használja, integrálnia kell azokat a DNS-kiszolgálókat, amelyek a külső DNS-zónát futtatják Azure Stack hub számára a használni kívánt szülő zónát futtató DNS-kiszolgálókkal. Ez az Azure Stack hub szolgáltatási végpontok névteréhez képest eltérő névtér, ezért létre kell hoznia egy további delegálási vagy feltételes továbbítási szabályt.

A DNS-név feliratának működéséről a [DNS használata Azure stack központban](../user/azure-stack-dns.md)című témakörben talál további információt.

## <a name="resolution-and-delegation"></a>Feloldás és delegálás

Kétféle DNS-kiszolgáló létezik:

- A mérvadó DNS-kiszolgáló üzemelteti a DNS-zónákat. Csak az ezekben a zónákban található rekordokra irányuló DNS-lekérdezéseket válaszolja meg.
- A rekurzív DNS-kiszolgáló nem üzemeltet DNS-zónákat. Minden DNS-lekérdezést megválaszol a mérvadó DNS-kiszolgálók adatait összegyűjtve.

Azure Stack hub mind a mérvadó, mind a rekurzív DNS-kiszolgálókat tartalmazza. A rekurzív kiszolgálók a belső privát zóna és a Azure Stack hub központi telepítésének külső nyilvános DNS-zónája kivételével az összes név feloldására szolgálnak.

![Azure Stack hub DNS-architektúrája](media/azure-stack-integrate-dns/Integrate-DNS-01.svg)

## <a name="resolving-external-dns-names-from-azure-stack-hub"></a>Külső DNS-nevek feloldása Azure Stack hubhoz

Az Azure Stack hub-on kívüli végpontok DNS-neveinek feloldásához (például: www \. Bing.com) olyan DNS-kiszolgálókat kell megadnia, amelyekkel Azure stack hub olyan DNS-kérelmek továbbítására használható, amelyek esetében a Azure stack hub nem mérvadó. Központi telepítés esetén a Azure Stack hub által küldött kérelmeket a központi telepítési munkalapon kell megadni (a DNS-továbbító mezőben). A hibatűréshez legalább két kiszolgálót adjon meg ebben a mezőben. Ezen értékek nélkül a Azure Stack hub üzembe helyezése sikertelen lesz. A DNS-továbbító értékeit a telepítés után a [ **set-AzSDnsForwarder** parancsmaggal](#editing-dns-forwarder-ips) módosíthatja. 

### <a name="configure-conditional-dns-forwarding"></a>Feltételes DNS-továbbítás konfigurálása

> [!IMPORTANT]
> Ez csak egy AD FS üzemelő példányra vonatkozik.

Ha engedélyezni szeretné a névfeloldást a meglévő DNS-infrastruktúrával, konfigurálja a feltételes továbbítást.

Feltételes továbbító hozzáadásához a privilegizált végpontot kell használnia.

Ehhez az eljáráshoz használjon olyan számítógépet az adatközpont-hálózaton, amely képes kommunikálni az Azure Stack hub privilegizált végpontján.

1. Nyisson meg egy emelt szintű Windows PowerShell-munkamenetet (Futtatás rendszergazdaként), és kapcsolódjon a privilegizált végpont IP-címéhez. Használja a hitelesítő adatokat a CloudAdmin-hitelesítéshez.

   ```PowerShell
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Miután kapcsolódott a Kiemelt végponthoz, futtassa a következő PowerShell-parancsot. Helyettesítse be a használni kívánt DNS-kiszolgálók tartománynevével és IP-címeivel megadott értékeket.

   ```PowerShell
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-hub-dns-names-from-outside-azure-stack-hub"></a>Azure Stack hub DNS-neveinek feloldása Azure Stack hubhoz kívülről
A mérvadó kiszolgálók azok, amelyek a külső DNS-zóna információit és a felhasználó által létrehozott zónákat használják. Integrálhatja ezeket a kiszolgálókat a Zónák delegálása vagy a feltételes továbbítás engedélyezéséhez Azure Stack hub DNS-neveinek a Azure Stack hub-on kívülről való feloldásához.

## <a name="get-dns-server-external-endpoint-information"></a>DNS-kiszolgáló külső végpontjának adatainak beolvasása

Az Azure Stack hub központi telepítésének a DNS-infrastruktúrával való integrálásához a következő információkra lesz szüksége:

- DNS-kiszolgálói teljes tartománynevek
- DNS-kiszolgáló IP-címei

Az Azure Stack hub DNS-kiszolgálói teljes tartománynevek formátuma a következő:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

A minták értékeit használva a DNS-kiszolgálók teljes tartománynevei a következők:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Ezek az információk az összes Azure Stack hub központi telepítésének végén is létrejönnek egy nevű fájlban `AzureStackStampInformation.json` . Ez a fájl az `C:\CloudDeployment\logs` üzembe helyezési virtuális gép mappájában található. Ha nem biztos abban, hogy milyen értékeket használt a Azure Stack hub központi telepítéshez, itt állíthatja be az értékeket.

Ha az üzembe helyezési virtuális gép már nem érhető el vagy nem érhető el, az értékeket az emelt szintű végponthoz való csatlakozással és a PowerShell-parancsmag futtatásával szerezheti be `Get-AzureStackStampInformation` . További információ: [privilegizált végpont](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack-hub"></a>Az Azure Stack Hubra történő feltételes továbbítás beállítása

Az Azure Stack hub és a DNS-infrastruktúra integrálásának legegyszerűbb és legbiztonságosabb módja a zóna feltételes továbbítása a szülő zónát futtató kiszolgálóról. Ez a módszer akkor javasolt, ha közvetlen vezérléssel látja el a Azure Stack hub külső DNS-névtérhez tartozó szülő zónát futtató DNS-kiszolgálókat.

Ha nem tudja, hogyan végezheti el a feltételes továbbítást a DNS-sel, tekintse meg a következő TechNet-cikket: [feltételes továbbító kiosztása egy tartománynévhez](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794735(v=ws.10))vagy a DNS-megoldás dokumentációja.

Olyan esetekben, amikor a külső Azure Stack hub DNS-zónáját úgy adta meg, hogy a vállalati tartománynévhez hasonló gyermektartomány legyen, a feltételes továbbítás nem használható. A DNS-delegálást konfigurálni kell.

Példa:

- Vállalati DNS-tartománynév: `contoso.com`
- Azure Stack hub külső DNS-tartományneve: `azurestack.contoso.com`

## <a name="editing-dns-forwarder-ips"></a>DNS-továbbító IP-címeinek szerkesztése

A DNS-továbbító IP-címei az Azure Stack hub üzembe helyezése során állíthatók be. Ha azonban a továbbítói IP-címeket valamilyen okból frissíteni kell, az értékeket a privilegizált végponthoz való csatlakozással és a `Get-AzSDnsForwarder` PowerShell-parancsmagok futtatásával módosíthatja `Set-AzSDnsForwarder [[-IPAddress] <IPAddress[]>]` . További információ: [privilegizált végpont](azure-stack-privileged-endpoint.md).

## <a name="delegating-the-external-dns-zone-to-azure-stack-hub"></a>A külső DNS-zóna delegálása az Azure Stack Hubba

Ahhoz, hogy a DNS-nevek feloldhatók legyenek Azure Stack hub-telepítésen kívülről, be kell állítania a DNS-delegálást.

Minden tartományregisztráló a saját DNS-kezelési eszközeit használja a tartományok névkiszolgálói rekordjainak módosítására. A regisztrátor DNS-kezelés lapján szerkessze az NS-rekordokat, és cserélje le a zóna NS rekordjait Azure Stack hub-ban lévők helyére.

A legtöbb DNS-regisztráló megköveteli, hogy legalább két DNS-kiszolgálót adjon meg a delegálás befejezéséhez.

## <a name="next-steps"></a>Következő lépések

[Tűzfal-integráció](azure-stack-firewall.md)
