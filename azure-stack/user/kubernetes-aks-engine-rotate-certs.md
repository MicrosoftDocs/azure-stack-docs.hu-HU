---
title: Kubernetes-tanúsítványok elforgatása Azure Stack központban
description: Megtudhatja, hogyan forgathatja el a Kubernetes-tanúsítványokat Azure Stack hubhoz.
author: mattbriggs
ms.topic: how-to
ms.date: 3/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/1/2021
ms.openlocfilehash: fa46de76f59e664fb3dd4a1c771ad7b835f97ca4
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102233848"
---
# <a name="rotate-kubernetes-certificates-on-azure-stack-hub"></a>Kubernetes-tanúsítványok elforgatása Azure Stack központban

Ebből a dokumentumból megtudhatja, hogyan forgathatja el a tanúsítványokat egy meglévő AK-beli motor-fürtön, és hogyan teheti elérhetővé az eszköz bevezetését `aks-engine rotate-certs` .

> [!IMPORTANT]  
> Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató azt feltételezi, hogy már üzembe helyezte a fürtöt az AK-motor használatával, és a fürt kifogástalan állapotban van.
## <a name="planning-for-certificate-rotation"></a>A tanúsítvány elforgatásának tervezése

Ha ezt a funkciót használja, vegye figyelembe, hogy a Kubernetes vezérlő síkja nem lesz elérhető a frissítés, az érvényesítés és az újraindítás lépései során. Ennek megfelelően tervezze meg a karbantartási műveletet. Azt is tervezze meg, hogy a műveletet olyan átmeneti környezetben hajtsa végre, amely az éles környezetben való kipróbálás előtt azonos konfigurációval rendelkezik az éles környezetben.

A művelet megkísérlése előtt tekintse át a következő szempontokat:

-  Ehhez hozzá kell férnie a parancsok által generált API-modellhez ( `apimodel.json` ) `aks-engine deploy` `aks-engine generate` . Alapértelmezés szerint ez a fájl egy relatív könyvtárba kerül, például: `_output/<clustername>/` .
-  Egy `aks-engine rotate-certs` művelet az API-kiszolgáló leállását okozza.
-  `aks-engine rotate-certs` egy olyan API-modellt vár, amely megfelel a fürt aktuális állapotának. `aks-engine rotate-certs` végrehajtja a fürt csomópontjain a távoli parancsokat, és az API-modell információit használva biztonságos SSH-kapcsolatot hoz létre. `aks-engine rotate-certs` arra is támaszkodik, hogy egyes erőforrások neve az eredeti telepítésnek megfelelően történjen `aks-engine` , például a virtuális gépeknek a által megadott elnevezést kell követniük `aks-engine` .
-  `aks-engine rotate-certs` a tanúsítvány elforgatása során a fürt vezérlő síkjával való munkakapcsolaton alapul:
    - A folyamat egyes lépéseinek érvényesítéséhez.
    - A fürt erőforrásainak, például a **Kube-rendszer** és a szolgáltatásfiók-tokenek újraindításához/újbóli létrehozásához.

    Ha egy VNet lévő fürt tanúsítványait a hozzáférésen kívülre bontotta, akkor `aks-engine rotate-certs` olyan gazda virtuális gépről kell futnia, amely hálózati hozzáféréssel rendelkezik a vezérlési síkon, például egy Jumpbox virtuális gép, amely a fő virtuális gépekkel megegyező VNet található.

- Ha éles környezetben használja `aks-engine rotate-certs` , javasolt egy tanúsítvány-rotációs tesztet végrehajtani egy olyan fürtön, amely ugyanahhoz a specifikációhoz készült. Ez azt is lehetővé teszi, hogy a fürt ugyanazzal a fürtkonfiguráció, az AK-motor parancssori eszköz ugyanazzal a verziójával, valamint a tanúsítvány elforgatása előtt a termelési fürttel megegyező, engedélyezett bővítményekkel van létrehozva. Az AK-motor támogatja a különböző fürtkonfiguráció-konfigurációkat, valamint azt, hogy az AK-motor csapatának által futtatott végpontok közötti tesztelés mekkora mértékben nem fedi le gyakorlatilag az összes lehetséges konfigurációt. Ezért javasoljuk, hogy egy átmeneti környezetben ellenőrizze, hogy az adott fürtkonfiguráció működik-e, `aks-engine rotate-certs` mielőtt megkísérli a műveletet az üzemi fürtön.
-  `aks-engine rotate-certs` a **nem garantálja a** visszamenőleges kompatibilitást. Ha a 0.60. x-ben telepítette az AK-motort, inkább a 0.60. x verzióval rendelkező tanúsítvány-elforgatási folyamatot kell végrehajtania.
-  Ezen a ponton a tanúsítványok új készletének beolvasása nem támogatott Key Vault.
- Használjon megbízható hálózati kapcsolatokat. `aks-engine rotate-certs` több távoli parancs végrehajtása szükséges, amelyek a lehetséges hibákra vonatkoznak, főleg akkor, ha a fürtcsomópontok kapcsolata nem megbízható. A `aks-engine rotate-certs` cél Azure stack bélyegzőn futó virtuális gépről futtatva csökkentheti az átmeneti problémák előfordulását.

## <a name="parameters"></a>Paraméterek

| Paraméter           | Kötelező | Leírás |
| --- | --- | --- |
| --API-Model             | igen          | Az API-modell (a fürt definíciója) relatív elérési útja, amely deklarálja a fürt várt konfigurációját.       |
| --SSH-gazdagép              | igen          | Egy olyan SSH-figyelő teljes tartományneve (FQDN) vagy IP-címe, amely a fürt összes csomópontja számára elérhetővé teheti.                            |
| --Linux-SSH-Private-Key | igen          | Egy érvényes titkos SSH-kulcs elérési útja a fürt linuxos csomópontjainak eléréséhez.                                        |
| --hely              | igen          | Az Azure-beli hely, ahol a fürt üzembe van helyezve.                                                               |
| --előfizetés-azonosító       | igen          | Az Azure-előfizetés, amelyben a fürt infra rendszer van telepítve.                                                     |
| --Resource-Group        | igen          | Az az Azure-erőforráscsoport, amelyben a fürt infra rendszer van telepítve.                                                   |
| – ügyfél-azonosító             | függ      | Az egyszerű szolgáltatásnév ügyfél-azonosítója. Kötelező, ha az Auth-Method client_secret vagy client_certificate értékre van állítva. |
| --Client-Secret         | függ      | Az egyszerű szolgáltatásnév ügyfél titka. Kötelező, ha az Auth-Method értéke client_secret.                   |
| --Azure-env             | függ      | A cél felhő neve. Nem kötelező, ha a cél felhő AzureCloud.                                              |
| --Certificate-Profile   | nem           | A tanúsítványok új készletét tartalmazó JSON-fájl relatív elérési útja.                                        |
| – kényszerített                 | nem           | Akkor is kényszerítse a végrehajtást, ha az API-kiszolgáló nem válaszol.                                                       |

## <a name="simple-steps-to-rotate-certificates"></a>A tanúsítványok elforgatásának egyszerű lépései

Ha elolvasta az összes [követelményt](https://github.com/Azure/aks-engine/blob/master/docs/topics/rotate-certs.md#pre-requirements), futtassa `aks-engine rotate-certs` a megfelelő argumentumokat:

```bash  
./bin/aks-engine rotate-certs \
  --location <resource-group-location> \
  --api-model <generated-apimodel.json> \
  --linux-ssh-private-key <private-SSH-key> \
  --ssh-host <apiserver-URI> \
  --resource-group <resource-group-name> \
  --client-id <service-principal-id> \
  --client-secret <service-principal-secret> \
  --subscription-id <subscription-id> \
  --azure-env <cloud-name>
```

Például:

```bash  
./bin/aks-engine rotate-certs \
  --location "westus2" \
  --api-model "_output/my-cluster/apimodel.json" \
  --linux-ssh-private-key "~/.ssh/id_rsa" \
  --ssh-host "my-cluster.westus2.cloudapp.azure.com"\
  --resource-group "my-cluster" \
  --client-id "12345678-XXXX-YYYY-ZZZZ-1234567890ab" \
  --client-secret "12345678-XXXX-YYYY-ZZZZ-1234567890ab" \
  --subscription-id "12345678-XXXX-YYYY-ZZZZ-1234567890ab" \
  --azure-env "AzureStackCloud" # optional if targeting AzureCloud
```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tanúsítvány-elforgatási folyamat leáll a hiba vagy átmeneti probléma miatti befejezés előtt, például a hálózati kapcsolat, a jelző használatával biztonságosan újrafuttathatja `aks-engine rotate-certs` `--force` .

Azt is figyelje `aks-engine rotate-certs` meg, hogy naplózza a fájl `/var/log/azure/rotate-certs.log` (Linux) és a `c:\\k\\rotate-certs.log` (Windows) minden lépésének kimenetét.

További információ arról, hogy mi történik a motorháztető alatt a művelet futtatásakor vagy további Testreszabás esetén: [a motorháztető alatt](https://github.com/Azure/aks-engine/blob/master/docs/topics/rotate-certs.md#under-the-hood).

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure stack hub-beli AK-motorról](azure-stack-kubernetes-aks-engine-overview.md)  
