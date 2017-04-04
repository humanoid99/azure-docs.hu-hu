## <a name="what-is-blob-storage"></a>Mi az a Blob Storage?
Az Azure Blob Storage szolgáltatás strukturálatlan adatok, például szövegek vagy bináris adatok nagy mennyiségben történő tárolására szolgál, amelyek HTTP- vagy HTTPS-kapcsolaton keresztül a világon bárhonnan elérhetők. A Blob Storage segítségével bárki számára nyilvánosan elérhetővé tehet adatokat, vagy privát módon tárolhat alkalmazásadatokat.

A Blob Storage gyakori használati módjai többek között:

* Képek vagy dokumentumok közvetlen szolgáltatása a böngésző számára
* Fájlok tárolása megosztott hozzáférésre
* Video- és hangtartalom online továbbítása
* Adattárolás biztonsági mentésekhez és helyreállításhoz, vészhelyreállításhoz és archiváláshoz
* Adattárolás egy helyszíni vagy egy Azure által üzemeltetett szolgáltatás elemzéseihez

## <a name="blob-service-concepts"></a>A Blob szolgáltatással kapcsolatos fogalmak
A Blob szolgáltatás az alábbi összetevőkből áll:

![Blob-architektúra](./media/storage-blob-concepts-include/blob1.png)

* **Tárfiók:** Minden Azure Storage-hozzáférés tárfiókon keresztül valósítható meg. Ez a tárfiók lehet **általános célú tárfiók** vagy **Blob Storage-fiók**, amely kifejezetten objektumok/blobok tárolására szolgál. További információk: [Tudnivalók az Azure Storage-fiókokról](../articles/storage/storage-create-storage-account.md).
* **Tároló:** A tárolók blobkészletek csoportosítását biztosítják. Az összes blobnak tárolóban kell lennie. Egy fiók korlátlan számú tárolót tartalmazhat. Egy tároló korlátlan számú blob tárolására használható. Vegye figyelembe, hogy a tároló neve csak kisbetűket tartalmazhat.
* **Blob:** Bármilyen típusú és bármekkora méretű fájl. Az Azure Storage háromféle blobot biztosít: blokkblobokat, lapblobokat és hozzáfűző blobokat.
  
    A *blokkblobok* ideálisak szövegek és bináris fájlok, például dokumentumok és médiafájlok tárolására. A *hozzáfűző blobok* a blokkblobokhoz hasonlóan blokkokból épülnek fel, de műveletek hozzáfűzésére vannak optimalizálva, ezért hasznosak lehetnek a naplózási forgatókönyvekben. Egyetlen blokkblob akár 50 000, egyenként 100 MB méretű blokkot is tartalmazhat, a maximális méretük ezért valamivel több mint 4,75 TB (100 MB * 50 000 blokk). Egyetlen hozzáfűző blob akár 50 000, egyenként 4 MB méretű blokkot is tartalmazhat, a maximális méretük ezért valamivel több mint 195 GB (4 MB * 50 000 blokk).
  
    A *lapblobok* akár 1 TB méretűek is lehetnek, és hatékonyabbak a gyakori írási/olvasási műveletek esetén. Az Azure virtuális gépek lapblobokat használnak operációs rendszerként és adatlemezként.
  
    A tárolók és blobok elnevezésével kapcsolatos részletekért lásd: [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/fileservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) (Tárolók, blobok és metaadatok elnevezése és hivatkozása).

