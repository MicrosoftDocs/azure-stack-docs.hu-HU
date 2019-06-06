---
title: Understanding IDN formátumú tartománynevek az Azure Stackben |} A Microsoft Docs
description: IDN formátumú tartománynevek funkciók és képességek az Azure Stack ismertetése
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 841d8607999f4135a86e0cd6668c79dddb80bd83
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691948"
---
# <a name="introducing-idns-for-azure-stack"></a>IDN formátumú tartománynevek az Azure Stack bemutatása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

iDNS az Azure Stack hálózati szolgáltatása, amely lehetővé teszi, hogy külső DNS-neveket (például https:\//www.bing.com.) Azt is lehetővé teszi, hogy kell regisztrálni a belső virtuális hálózat nevét. Ezzel a módszerrel oldhatja meg virtuális gépek ugyanazon a virtuális hálózaton lévő IP-címet, hanem nevét. Ez a megközelítés hogy meg kell adni az egyéni DNS-kiszolgálói bejegyzésekkel eltávolítja. a DNS szolgáltatással kapcsolatos további információkért lásd: a [Azure DNS áttekintését](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Mire IDN formátumú tartománynevek?

Az IDN formátumú tartománynevek az Azure Stack az alábbi képességeket egyéni DNS-kiszolgálói bejegyzésekkel megadása nélkül szolgáltatás:

- Megosztott DNS névfeloldási szolgáltatást bérlői számítási feladatok esetében.
- A névfeloldás és a bérlői virtuális hálózat DNS-regisztráció mérvadó DNS-szolgáltatás.
- A rekurzív DNS szolgáltatás a bérlő virtuális gépek Internet-nevek feloldását. Bérlők számára már nem kell megadnia az egyéni DNS-bejegyzéseket Internet névfeloldás (például www.bing.com.)

Továbbra is a saját DNS használata, és használja az egyéni DNS-kiszolgálókat. Azonban IDN formátumú tartománynevek használatával internetes DNS-nevek és a más virtuális gépekhez az azonos virtuális hálózatba, nem kell egyéni DNS-bejegyzéseket létrehozni.

## <a name="what-doesnt-idns-do"></a>Mire nem IDN formátumú tartománynevek?

Milyen IDN formátumú tartománynevek nem teszi lehetővé tesz, nem egy nevet, amely a virtuális hálózaton kívül feloldható legyen a DNS-rekord létrehozása.

Az Azure-ban lehetősége van egy nyilvános IP-címmel társított DNS-név címke megadásával. Kiválaszthatja, hogy a címke (előtag), de az Azure úgy dönt, az utótag, a régiót, amelyben a nyilvános IP-cím létrehozása alapuló.

![Példa egy DNS-névcímke](media/azure-stack-understanding-dns-in-tp2/image3.png)

Ahogy az előző képen látható, az Azure hoz létre az "A" rekord a DNS-ben a zóna alatt megadott DNS-névcímke **westus.cloudapp.azure.com**. Az előtag és az utótag együttes írása egy [teljesen minősített tartománynevét](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), amely feloldható a bárhol a nyilvános interneten.

Az Azure Stack csak támogatja belső névregisztráció, IDN formátumú tartományneveket, így nem tegye a következőket:

- Hozzon létre egy DNS-rekordot a meglévő üzemeltetett DNS-zóna (például local.azurestack.external.)
- (Például Contoso.com.) a DNS-zóna létrehozása
- Hozzon létre egy rekordot a saját egyéni DNS-zóna szerint.
- A tartománynevek vásárlásának támogatja.

## <a name="next-steps"></a>További lépések

[DNS használata az Azure Stackben](azure-stack-dns.md)
