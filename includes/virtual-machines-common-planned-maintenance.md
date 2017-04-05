

## <a name="memory-preserving-updates"></a>Memóriamegőrző frissítések
A Microsoft Azure-ban elérhető frissítések egy része semmilyen hatással nincs az ügyfelek futó virtuális gépeinek teljesítményére. A frissítések sok esetben olyan összetevőkhöz vagy szolgáltatásokhoz tartoznak, amelyek frissíthetők a futó példány zavarása nélkül. A frissítések néhány esetben platforminfrastruktúra-frissítések a gazdagép operációs rendszerén, amelyek a virtuális gépek teljes újraindítása nélkül alkalmazhatók.

Ezeket a frissítéseket olyan technológia biztosítja, amely lehetővé teszi a helyszíni élő áttelepítést, más néven „memóriamegőrző” frissítést. A frissítés közben a virtuális gép „szüneteltetett” állapotba kerül, megőrizve a memóriát RAM-ban, amíg az alapjául szolgáló gazda operációs rendszer megkapja a szükséges frissítéseket és javításokat. A virtuális gép a szüneteltetés után 30 másodpercen belül folytatja a működését. A virtuális gép órája a folytatás után automatikusan szinkronizálódik.

Nem minden frissítés helyezhető üzembe ezzel a mechanizmussal, de a rövid felfüggesztési időszak miatt a frissítések ilyen telepítése nagymértékben csökkenti a virtuális gépekre gyakorolt hatást.

A többpéldányos frissítések (a rendelkezésre állási csoportokban lévő virtuális gépeken) végrehajtása frissítési tartományonként történik.  

## <a name="virtual-machine-configurations"></a>Virtuálisgép-konfigurációk
Kétféle virtuálisgép-konfiguráció létezik: többpéldányos és egypéldányos. A többpéldányos konfigurációban hasonló virtuális gépek kerülnek egy rendelkezésre állási csoportba.

A többpéldányos konfiguráció redundanciát biztosít a fizikai gépek, a teljesítmény és a hálózat terén, és ez az ajánlott konfiguráció az alkalmazás rendelkezésre állásának biztosításához. A rendelkezésre állási csoportban található összes virtuális gépnek ugyanazt a célt kell szolgálnia az alkalmazásban.

További információ a virtuális gépek magas rendelkezésre állásra való konfigurálásáról: [Windows rendszerű virtuális gépek rendelkezésre állásának kezelése](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vagy [Linux rendszerű virtuális gépek rendelkezésre állásának kezelése](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ezzel szemben az egypéldányos konfiguráció különálló, rendelkezésre állási csoportba nem tartozó virtuális gépekhez használható. Ezek a virtuális gépek nem felelnek meg a szolgáltatói szerződésnek (SLA), amely szerint legalább két virtuális gépet kell ugyanabban a rendelkezésre állási csoportban üzembe helyezni.

Az SLA-król a [Szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/) „felhőszolgáltatásokra és virtuális gépekre” vonatkozó szakaszában talál további információt.

## <a name="multi-instance-configuration-updates"></a>Többpéldányos konfigurációfrissítések
A tervezett karbantartás során az Azure platform először a többpéldányos konfigurációban futó virtuális gépeket frissíti. A frissítés miatt ezek a virtuális gépek újraindulnak, körülbelül 15 perces állásidővel.

A többpéldányos konfigurációfrissítés feltételezi, hogy minden virtuálisgép-kiszolgáló hasonló funkciót tölt be, mint a többi gép a rendelkezésre állási csoportban. Ebben a helyzetben a virtuális gépek úgy frissülnek, hogy a folyamat során biztosítva legyen a rendelkezésre állás.

A mögöttes Azure platform a rendelkezésre állási csoportban lévő mindegyik virtuális gépnek kioszt egy frissítési tartományt és egy tartalék tartományt. Minden frissítési tartomány olyan virtuális gépek csoportjából áll, amelyek ugyanabban az időtartományban indulnak újra. A tartalék tartományok azonos tápforrással és hálózati kapcsolóval rendelkező virtuális gépek csoportjai.


További tudnivalók a frissítési tartományokról és a tartalék tartományokról: [Több virtuális gép rendelkezésre állási csoportba konfigurálása a redundancia biztosítása érdekében](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

A rendelkezésre állás fenntartása érdekében frissítés közben az Azure frissítési tartományonként végzi el a karbantartást, és egyszerre csak egy tartományt frissít. A karbantartás a frissítési tartományokban található összes virtuális gép leállításából, a frissítés gazdagépekre való alkalmazásából és a virtuális gépek újraindításából áll. Ha a tartományban befejeződött a karbantartás, az Azure megismétli a folyamatot a következő frissítési tartománnyal, és a folyamat addig folytatódik, amíg nem frissül minden tartomány.

A frissítési tartományok újraindítása nem haladhat szekvenciálisan a tervezett karbantartás során, hanem csak egyetlen frissítési tartományt lehet újraindítani egyszerre. Az Azure jelenleg egyhetes előzetes értesítést küld a többpéldányos konfigurációban található virtuális gépek tervezett karbantartásáról.

Egy virtuális gép visszaállítása után a Windows eseménynaplója például a következőt jelenítheti meg:

<!--Image reference-->
![][image2]


Az eseménynaplóval jelentést készíthet azokról a virtuális gépekről, amelyek többpéldányos konfigurációban vannak konfigurálva az Azure Portal, az Azure PowerShell vagy az Azure CLI használatával. Az Azure Portallal például hozzáadhatja a _Rendelkezésre állási csoportot_ a **Virtuális gépek (klasszikus)** böngésző-párbeszédpanelhez. Az ugyanazon rendelkezésre állási csoportról jelentést készítő virtuális gépek egy többpéldányos konfigurációhoz tartoznak. A következő példában a többpéldányos konfiguráció az SQLContoso01 és az SQLContoso02 virtuális gépekből áll.

<!--Image reference-->
  ![Virtuális gépek (klasszikus) nézet az Azure Portalról][image4]

## <a name="single-instance-configuration-updates"></a>Egypéldányos konfigurációfrissítések
Miután a többpéldányos konfigurációfrissítések befejeződtek, az Azure végrehajtja az egypéldányos konfigurációfrissítéseket. Ezek a frissítések is újraindítják azokat a virtuális gépeket, amelyek nem rendelkezésre állási csoportokban futnak.

> [!NOTE]
> Ha egy rendelkezésre állási csoportban csak egy virtuálisgép-példány fut, az Azure platform többpéldányos konfigurációfrissítésként kezeli.
>

Az egypéldányos konfigurációban a karbantartás a gazdagépen futó összes virtuális gép leállításából, a gazdagép frissítéséből, majd a virtuális gépek újraindításából áll. A karbantartáshoz körülbelül 15 perc állásidő szükséges. A tervezett karbantartási esemény régiónként minden virtuális gépen ugyanabban a karbantartási időszakban fut.


A tervezett karbantartási események hatással vannak az egypéldányos konfigurációkhoz tartozó alkalmazás rendelkezésre állására. Az Azure egyhetes előzetes értesítést küld az egypéldányos konfigurációban található virtuális gépek tervezett karbantartásáról.

## <a name="email-notification"></a>E-mailes értesítés
Az Azure csak az egypéldányos és többpéldányos virtuálisgép-konfigurációk esetén küld e-mailben figyelmeztetést az elkövetkező tervezett karbantartásról (egy héttel előre). Ezt az e-mailt az előfizetés adminisztrátorának és társadminisztrátora e-mail-fiókjába küldi el a rendszer. Példa az ilyen típusú e-mailre:

<!--Image reference-->
![][image1]

## <a name="region-pairs"></a>Régiópárok

A karbantartás végrehajtása közben az Azure csak a párjához tartozó, egy régióban található virtuálisgép-példányokat frissíti. Például ha az USA északi középső régiójában található virtuális gépeket frissíti, az Azure nem fogja frissíteni az USA déli középső régiójában található virtuális gépeket ugyanabban az időben. Ez egy másik időpontra lesz ütemezve a régiók közötti feladatátvétel vagy terheléselosztás érdekében. Azonban más régiók (például Észak-Európa) karbantarthatók ugyanabban az időben, mint az USA keleti régiója.

Az aktuális régiópárokat a következő táblázatban tekintheti meg:

| 1. régió | 2. régió |
|:--- | ---:|
| USA keleti régiója |USA nyugati régiója |
| USA 2. keleti régiója |USA középső régiója |
| USA északi középső régiója |USA déli középső régiója |
| USA nyugati középső régiója |USA nyugati régiója, 2. |
| Kelet-Kanada |Közép-Kanada |
| Dél-Brazília |USA déli középső régiója |
| USA-beli államigazgatás – Iowa |USA-beli államigazgatás – Virginia |
| US DoD – Kelet |US DoD – Középső régió |
| Észak-Európa |Nyugat-Európa |
| Az Egyesült Királyság nyugati régiója |Az Egyesült Királyság déli régiója |
| Közép-Németország |Északkelet-Németország |
| Délkelet-Ázsia |Kelet-Ázsia |
| Délkelet-Ausztrália |Kelet-Ausztrália |
| Közép-India |Dél-India |
| Nyugat-India |Dél-India |
| Kelet-Japán |Nyugat-Japán |
| Korea középső régiója |Korea déli régiója |
| Kelet-Kína |Észak-Kína |


<!--Anchors-->
[image1]: ./media/virtual-machines-common-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-common-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-common-planned-maintenance/availabilitysetexample.png


<!--Link references-->
[Virtual Machines Manage Availability]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md

[Understand planned versus unplanned maintenance]: ../articles/virtual-machines/windows/manage-availability.md#Understand-planned-versus-unplanned-maintenance/
