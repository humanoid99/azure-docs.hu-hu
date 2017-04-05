
## <a name="about-vhds"></a>Tudnivalók a VHD-kről

Az Azure-ban használt VHD-k .vhd fájlok, amelyek lapblobként vannak tárolva egy standard vagy prémium szintű Azure-tárfiókban. A lapblobokkal kapcsolatos további részletekért tekintse meg [a blokkblobokat és a lapblobokat bemutató cikket](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). A prémium szintű tárolással kapcsolatos részletekért tekintse meg [a nagy teljesítményű Premium Storage szolgáltatással és az Azure virtuális gépekkel kapcsolatos cikket](../articles/storage/storage-premium-storage.md).

Az Azure támogatja a rögzített lemezes VHD formátumot. A rögzített formátum a logikai lemezt lineárisan helyezi el a fájlon belül, így az X lemezeltolás az X blobeltolásnál van tárolva. A blob végén egy kis lábléc írja le a VHD tulajdonságait. A rögzített formátum gyakran helypazarló, mivel a legtöbb lemezen nagy méretű kihasználatlan tartományok találhatóak. Az Azure azonban egy ritka formátumban tárolja a .vhd fájlokat, így a rögzített és a dinamikus lemezek előnyei egyaránt kihasználhatók. További részletekért tekintse meg [a virtuális merevlemezek használatába bevezető cikket](https://technet.microsoft.com/library/dd979539.aspx).

Az Azure-ban minden olyan .vhd fájl írásvédett, amelyet forrásként szeretne használni lemezek vagy rendszerképek létrehozásához. Lemez vagy rendszerkép létrehozásakor az Azure másolatot készít a .vhd fájlokról. Ezek a másolatok csak olvashatók vagy olvashatók és írhatók is lehetnek a VHD használatától függően.

Amikor egy virtuális gépet hoz létre egy rendszerképből, az Azure létrehoz egy lemezt a virtuális gép számára, amely a forrás .vhd fájl másolata. A véletlen törlés elleni védelem érdekében az Azure egy bérlést helyez minden olyan forrás .vhd fájlra, amelyet egy rendszerkép, egy operációsrendszer-lemez vagy egy adatlemez létrehozásához használtak.

Mielőtt a forrás .vhd fájlt törölhetné, el kell távolítania a bérlést, ehhez pedig törölnie kell a lemezt vagy a rendszerképet. Ha törölni szeretne egy virtuális gép által operációsrendszer-lemezként használt .vhd fájlt, egyszerre is törölheti a virtuális gépet, az operációsrendszer-lemezt és a forrás .vhd fájlt a virtuális gép és az összes hozzá tartozó lemez törlésével. Egy adatlemez forrásaként használt .vhd fájl törléséhez azonban több lépést is el kell végezni, egy megadott sorrendben. Először válassza le a lemezt a virtuális gépről, majd törölje a lemezt, és végül törölje a .vhd fájlt.

> [!WARNING]
> Ha töröl egy forrás .vhd fájlt a tárolóból, vagy törli a saját tárfiókját, a Microsoft nem tudja visszaállítani az adatait.
> 

## <a name="types-of-disks"></a>Lemeztípusok 

A lemezek létrehozásakor kétféle teljesítményszint közül választhat: Standard Storage és Premium Storage. Emellett két lemeztípus használható, a nem felügyelt és a felügyelt, amelyek mindkét teljesítményszinten elérhetőek.  

### <a name="standard-storage"></a>Standard szintű Storage 

A merevlemez-meghajtókra épülő Standard Storage költséghatékony tárolási megoldás, amely emellett jó teljesítményt nyújt. A Standard Storage replikálható helyileg egy adatközpontban, vagy lehet georedundáns egy elsődleges és egy másodlagos adatközponttal. A tárolók replikálásával kapcsolatos további információkért tekintse át [az Azure Storage replikációjával kapcsolatos cikket](../articles/storage/storage-redundancy.md). 

A Standard Storage és a VM-lemezek együttes használatával kapcsolatos információkért tekintse át [a Standard Storage és a lemezek együttes használatát bemutató cikket](../articles/storage/storage-standard-storage.md).

### <a name="premium-storage"></a>Prémium szintű Storage 

Az SSD-kre épülő Premium Storage nagy teljesítményű, kis késleltetésű lemeztámogatást biztosít a nagy adatátviteli teljesítményt igénylő számítási feladatokat futtató virtuális gépek számára. A Premium Storage a DS, DSv2, GS és FS sorozatú Azure virtuális gépekhez használható. További információk: [Premium Storage](../articles/storage/storage-premium-storage.md).

### <a name="unmanaged-disks"></a>Nem felügyelt lemezek

A nem felügyelt lemez a virtuális gépek által használt hagyományos lemeztípus. Az ilyen lemezekkel létrehozhatja a saját tárfiókját, és a lemez létrehozásakor azt a tárfiókot adhatja meg. Ügyeljen arra, hogy ne helyezzen túl sok lemezt ugyanabba a tárfiókba, mert azzal túllépheti a tárfiók [skálázási célértékeit](../articles/storage/storage-scalability-targets.md) (pl. 20 000 IOPS), amivel korlátozhatja a virtuális gépeket. Nem felügyelt lemezek esetén Önnek kell felmérnie, hogyan maximalizálhatja egy vagy több tárfiók kihasználtságát, hogy a virtuális gépeiből a lehető legjobb teljesítményt hozza ki.

### <a name="managed-disks"></a>Felügyelt lemezek 

A felügyelt lemezek a háttérben végzik a tárfiók létrehozását és kezelését, hogy Önnek ne kelljen foglalkoznia a tárfiók skálázási korlátaival. Egyszerűen csak adja meg a lemez méretét és teljesítményszintjét (Standard vagy prémium), és az Azure létrehozza és felügyeli a lemezt Ön helyett. Akkor sem kell a használt tárterület miatt aggódnia, ha lemezeket ad hozzá vagy fel-/leskálázza a virtuális gépet. 

Az egyéni rendszerképeit Azure-régiónként egyetlen tárfiókban is felügyelheti, és a használatukkal több száz virtuális gépet hozhat létre egy adott előfizetésben. A felügyelt lemezekkel kapcsolatos további információért tekintse meg [a felügyelt lemezek áttekintésével kapcsolatos cikket](../articles/storage/storage-managed-disks-overview.md).

Javasoljuk, hogy az új virtuális gépeihez használja az Azure Managed Disks szolgáltatást, és a korábbi, nem felügyelt lemezeit is alakítsa felügyeltté, hogy kihasználhassa a Managed Disks által kínált számos szolgáltatást.

### <a name="disk-comparison"></a>Lemezek összehasonlítása

A következő tábla összehasonlítja a Prémium és a Standard szintet a nem felügyelt és a felügyelt lemezek esetén, így könnyebben eldöntheti, melyiket érdemes használni.

|    | Prémium szintű Azure-lemez | Standard szintű Azure-lemez |
|--- | ------------------ | ------------------- |
| Lemez típusa | SSD-k | HDD-k  |
| Áttekintés  | SSD-alapú, nagy teljesítményű, kis késleltetésű lemeztámogatás a nagy adatátviteli teljesítményt igénylő számítási feladatokat vagy az üzletmenet szempontjából kritikus fontosságú éles környezeteket futtató virtuális gépek számára | HDD-alapú, költséghatékony lemeztámogatás fejlesztési/tesztelési virtuálisgép-forgatókönyvekhez |
| Forgatókönyv  | Éles, teljesítményérzékeny számítási feladatok | Fejlesztés/tesztelés, nem kritikus, <br>rendszertelen hozzáférés |
| Lemezméret | P10: 128 GB<br>P20: 512 GB<br>P30: 1024 GB | Nem felügyelt lemezek: 1 GB – 1 TB <br><br>Managed Disks:<br> S4: 32 GB <br>S6: 64 GB <br>S10: 128 GB <br>S20: 512 GB <br>S30: 1024 GB |
| Lemezenkénti maximális átviteli sebesség | 200 MB/s | 60 MB/s |
| Lemezenkénti maximális IOPS-érték | 5000 IOPS | 500 IOPS |
