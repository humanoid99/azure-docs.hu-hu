---
title: "Az Azure Active Directory architektúrájának ismertetése | Microsoft Docs"
description: "Ismerteti az Azure AD-bérlő fogalmát, valamint az Azure kezelését az Azure Active Directory felületén keresztül"
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 5c60fa737c0133482af8b653f795bf9086c39969
ms.lasthandoff: 03/28/2017


---
# <a name="understand-azure-active-directory-architecture"></a>Az Azure Active Directory architektúrájának ismertetése
Az Azure Active Directory (Azure AD) lehetővé teszi, hogy biztonságosan kezelje az Azure-szolgáltatások és -erőforrások elérését a felhasználók számára. Az Azure AD-ben megtalálható az identitáskezelési megoldások teljes palettája. Az Azure AD-funkciókkal kapcsolatos információért lásd: [Mi az az Azure Active Directory?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-whatis)

Az Azure AD-vel felhasználókat és csoportokat hozhat létre és kezelhet, valamint engedélyeket adhat meg a vállalati erőforrások hozzáférésének engedélyezéséhez és megtagadásához. Az identitáskezeléssel kapcsolatos információért tekintse meg az [Azure-identitáskezelés alapjait leíró szakaszt](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity).

## <a name="azure-ad-architecture"></a>Azure AD-architektúra
Az Azure AD földrajzilag eltérő helyeken található architektúrája a széles körű megfigyelés, az automatikus átirányítás, a feladatátvétel és a helyreállítási funkciók kombinálásával lehetővé teszi, hogy vállalati szintű rendelkezésre állást és teljesítményt nyújthatunk az ügyfeleink számára.

Ez a cikk a következő architektúraelemeket tárgyalja:
 *    Szolgáltatásarchitektúra kialakítása
 *    Méretezhetőség 
 *    Folyamatos rendelkezésre állás
 *    Adatközpontok

### <a name="service-architecture-design"></a>Szolgáltatásarchitektúra kialakítása
A méretezhető, magas rendelkezésre állású, adatokban gazdag rendszerek felépítésének legáltalánosabb módszere a független építőelemek vagy skálázási egységek alkalmazása. Az Azure AD adatrétege esetében a skálázási egységeket *partícióknak* nevezik. 

Az adatréteg több előtér-szolgáltatással rendelkezik, amelyek olvasási-írási képességeket nyújtanak. Az alábbi ábra bemutatja, hogyan vannak elosztva egy egy címtárból álló partíció összetevői a földrajzilag elosztott adatközpontokban. 

  ![Egy címtárból álló partíciók](./media/active-directory-architecture/active-directory-architecture.png)

Az Azure AD architektúra összetevői között egy elsődleges replika és másodlagos replikák szerepelnek.

**Elsődleges replika**

Az *elsődleges replika* fogadja azon partíció összes *írását*, amelyhez tartozik. Minden írási művelet azonnal replikálódik egy másik adatközpontban lévő másodlagos replikára, mielőtt a rendszer sikert jelezne a hívónak. Ezáltal biztosítható az írások georedundáns tartóssága.

**Másodlagos replikák**

Minden *címtárolvasás* *másodlagos replikákból* van szolgáltatva, amelyek különböző földrajzi helyeken lévő adatközpontokban találhatók. Sok másodlagos replika van, mivel az adatok replikálása aszinkron módon történik. A címtárolvasások, például a hitelesítési kérések az ügyfeleinkhez közeli adatközpontokból vannak szolgáltatva. A másodlagos replikák felelősek az olvasás méretezhetőségéért.

### <a name="scalability"></a>Méretezhetőség

A méretezhetőség azon képesség, amellyel a szolgáltatások ki tudnak bővülni a növekvő teljesítményigények kielégítése érdekében. Az írás méretezhetősége az adatok particionálásával érhető el. Az olvasás méretezhetősége az adatok egy partícióról több, a világ számos részén elosztott másodlagos replikákra történő replikálásával érhető el.

A címtáralkalmazásokból érkező kérések általában a fizikailag legközelebbi adatközponthoz vannak irányítva. Az írások transzparens módon vannak átirányítva az elsődleges replikához az írás-olvasás konzisztenciájának biztosítása érdekében. A másodlagos replikák jelentősen kibővítik a partíciók méretét, mert a címtárak leginkább olvasásokat szolgáltatnak.

A címtáralkalmazások a legközelebbi adatközpontokhoz csatlakoznak. Ez javítja a teljesítményt, és így lehetséges a méretezés. Mivel egy másodlagos partíció sok másodlagos replikával rendelkezhet, a másodlagos replikák közelebb helyezhetők a címtár ügyfeleihez. Csak a belső címtárszolgáltatás írásigényes összetevői célozzák meg közvetlenül az aktív elsődleges replikát.

### <a name="continuous-availability"></a>Folyamatos rendelkezésre állás

A rendelkezésre állás (vagy üzemidő) határozza meg, hogy egy rendszer mennyire tud zavartalanul működni. Az Azure AD magas rendelkezésre állásának kulcsa, hogy a szolgáltatásaink gyorsan képesek áthelyezni a forgalmat több, földrajzilag elosztott adatközpont között. Mindegyik adatközpont független, ami lehetővé teszi a nem összefüggő hibaállapotokat.

Az Azure AD partíciójának kialakítása a vállalati AD kialakításához képest egyszerű, ami kritikus a rendszer méretezése szempontjából. Egyetlen főkialakítást alkalmaztunk, amely egy körültekintően összehangolt és determinisztikus feladatátvételi folyamatot is magában foglal az elsődleges replika számára.

**Hibatűrés**

A rendszereknek jobb a rendelkezésre állása, ha képesek tűrni a hardveres, hálózati és szoftverhibákat. A címtárban lévő összes partícióhoz tartozik egy magas rendelkezésre állású főreplika. Ez az elsődleges replika. Ezen a replikán csak a partíció írása történik. Ez a replika folyamatos megfigyelés alatt áll, és az írások azonnal egy másik replikára helyezhetők (amely az új elsődleges replikává válik), ha a rendszer hibát észlel. Feladatátvétel esetén általában 1-2 percig megszűnhet az írás rendelkezésre állása. Ez nincs hatással az olvasás rendelkezésre állására.

Az olvasási műveletek (amelyek száma nagyságrendekkel meghaladja az írásokét) csak a másodlagos replikákhoz érkeznek. Mivel a másodlagos replikák idempotensek, egy adott partíció bármely replikájának elvesztése könnyedén orvosolható az olvasások egy másik replikára történő irányításával, általában ugyanabban az adatközpontban.

**Adatok tartóssága**

Az írásoknak legalább két adatközpontban tartósan használatban kell lenniük a nyugtázásuk előtt. Ehhez először véglegesíteni kell az írást az elsődleges replikán, majd azonnal replikálni kell legalább egy másik adatközpontba. Ez biztosítja, hogy az elsődleges replikát üzemeltető adatközpont lehetséges végzetes elvesztése ne eredményezzen adatvesztést.

Az Azure AD a [Helyreállítási időre vonatkozó célkitűzés (RTO)](https://en.wikipedia.org/wiki/Recovery_time_objective) tekintetében a nulla értékét tartja fenn a jogkivonatok kiadásához és a címtárolvasásokhoz, a címtárírások esetében pedig perces nagyságrendű értéket (~5 perc). A [Helyreállítási időkorláthoz (RPO)](https://en.wikipedia.org/wiki/Recovery_point_objective) is nulla értéket tartunk fenn, így nem veszítünk adatokat a feladatátvételek során.

### <a name="data-centers"></a>Adatközpontok

Az Azure AD replikái a világ számos részén található adatközpontokban vannak tárolva. További információk: [Azure adatközpontok](https://azure.microsoft.com/en-us/overview/datacenters).

Az Azure AD a következő jellemzőkkel rendelkező adatközpontokon működik:

 * A hitelesítés, a Graph és a további AD-szolgáltatások az átjárószolgáltatás mögött helyezkednek el. Az átjáró kezeli ezen szolgáltatások terheléselosztását. Automatikus feladatátvételt hajt végre, ha a rendszer a tranzakciós állapot-mintavétel használatával nem megfelelő állapotú kiszolgálókat észlel. Ezen állapot-mintavételek alapján az átjáró dinamikusan átirányítja a forgalmat a kifogástalan állapotú adatközpontokhoz.
 * Az *olvasások* esetében a címtár másodlagos replikákkal és megfelelő előtér-szolgáltatásokkal rendelkezik egy több adatközpontban működő aktív-aktív konfigurációban. A teljes adatközpont meghibásodása esetén a rendszer automatikusan átirányítja a forgalmat egy másik adatközpontra.
 *    Az *írások* esetében a címtár tervezett (az új elsődleges replika szinkronizálása a régi elsődleges replikára) vagy vészhelyzeti feladatátvételi eljárásokon keresztül hajtja végre elsődleges (fő-) replika feladatátvételét az adatközpontokon. Az adatok tartóssága minden véglegesítés legalább két adatközpontra történő replikálásával valósul meg.

**Adatkonzisztencia**

A címtármodell végleges konzisztenciával rendelkezik. Az elosztott aszinkron replikációs rendszerek egyik általános problémája, hogy egy „adott” replikáról lekért adatok nem feltétlenül naprakészek. 

Az Azure AD olvasási-írási konzisztenciát biztosít a másodlagos replikát megcélzó alkalmazások számára az írások az elsődleges replikára történő irányításával, illetve az írások a másodlagos replikára történő szinkron módon történő visszahúzásával.

Az Azure AD Graph API-t használó alkalmazások írásai számára nem kötelező az affinitás fenntartása egy címtár replikája felé az olvasási-írási konzisztencia érdekében. Az Azure AD Graph szolgáltatása egy logikai munkamenetet tart fenn, amely affinitással rendelkezik egy olvasáshoz használt másodlagos replikához. Az affinitás egy „replika-jogkivonatban” van rögzítve, amelyet a Graph szolgáltatás egy elosztott gyorsítótár használatával gyorsítótáraz. A jogkivonat ezután a logikai munkamenet következő műveleteihez lesz felhasználva. 

 >[!NOTE]
 >Az írásokat a rendszer azonnal replikálja arra másodlagos replikára, amelyre a logikai munkamenet olvasásai ki lettek adva.
 >

**Biztonsági másolatok védelme**

A címtár a végleges törlés helyett helyreállítható törlést valósít meg a felhasználók és bérlők számára, amely lehetővé teszi az egyszerű helyreállítást, ha az ügyfél esetleg véletlenül törölne valamit. Ha a bérlői rendszergazda véletlenül töröl egy felhasználót, könnyedén visszavonhatja azt, és helyreállíthatja a törölt felhasználót. 

Az Azure AD naponta biztonsági másolatot készít az összes adatról, ezért képes az adatok mérvadó visszaállítására bármilyen logikai törlés vagy sérülés esetében. Az adatrétegünk hibajavító kódokat alkalmaz, így ellenőrizni tudja a hibákat, és képes automatikusan kijavítani bizonyos típusú lemezhibákat.

**Mérőszámok és figyelők**

Egy magas rendelkezésre állású szolgáltatás futtatásához világszínvonalú mérési és megfigyelési képességekre van szükség. Az Azure AD folyamatosan elemzi a szolgáltatások állapotával kapcsolatos legfontosabb mérőszámokat és az egyes szolgáltatások sikerességi feltételeit, illetve jelentést készít azokról. Folyamatosan fejlesztjük és hangoljuk az egyes forgatókönyvekhez tartozó mérőszámokat, figyelést és riasztást az egyes Azure AD szolgáltatásokban, illetve az összes szolgáltatásban.

Ha bármelyik Azure AD szolgáltatás nem a várt módon működik, azonnal munkához látunk a működőképesség lehető leggyorsabb helyreállítása érdekében. Az Azure AD által nyomon követett legfontosabb mérőszám az, hogy milyen gyorsan tudjuk észlelni és elhárítani egy ügyféllel vagy egy élő webhellyel kapcsolatos problémát. Komoly erőfeszítéseket teszünk a megfigyelés és a riasztások területén, hogy minimalizáljuk az észleléshez szükséges időt (cél: <5 perc), illetve a működésre való készenlét terén is, hogy minimálisra csökkentsük az elhárításhoz szükséges időt (cél: <30 perc).

**Biztonságos műveletek**

Műveleti vezérlőket, például többtényezős hitelesítést (MFA) alkalmazunk minden művelet esetében, valamint naplózzuk az összes műveletet. Emellett igényalapú szintemelési rendszert használunk ahhoz, hogy folyamatosan meg tudjuk adni a szükséges ideiglenes hozzáférést bármilyen igény szerinti műveleti feladathoz. További információkért lásd: [The Trusted Cloud](https://azure.microsoft.com/en-us/support/trust-center) (A megbízható felhő).

## <a name="next-steps"></a>Következő lépések
[Az Azure Active Directory fejlesztői útmutatója](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)


