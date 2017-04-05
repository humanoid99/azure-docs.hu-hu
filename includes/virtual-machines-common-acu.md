



Az Azure-alapú számítási egység (Azure Compute Unit, ACU) fogalmát azért vezettük be, hogy lehetséges legyen összehasonlítani az egyes Azure-termékváltozatok számítási (CPU-) teljesítményét. Így könnyebben választhatja ki a teljesítményigényeinek leginkább megfelelő termékváltozatot.  Az ACU jelenlegi standard alapjaként a Kisméretű (Standard_A1) virtuális gép 100-as értéket képvisel, és a többi termékváltozat értéke ehhez képest jelöli, hogy mennyivel gyorsabban futtatja az adott termékváltozat a standard teljesítménytesztet. 

> [!IMPORTANT]
> Az ACU csupán iránymutatóként szolgál.  Az egyes számítási terhelések eredményei ettől eltérhetnek. 
> 
> 

<br>

| Termékváltozat-család | ACU/mag |
| --- | --- |
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 |
| [A1–A4](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A5–A7](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A1_v2–A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A2m_v2–A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A8-A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* |
| [D1–D14](../articles/virtual-machines/windows/sizes-general.md) |160 |
| [D1_v2–D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210 - 250* |
| [DS1–DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 |
| [DS1_v2–DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210-250* |
| [F1-F16](../articles/virtual-machines/windows/sizes-compute.md) |210-250* |
| [F1s-F16s](../articles/virtual-machines/windows/sizes-compute.md) |210-250* |
| [G1–G5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* |
| [GS1–GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180 - 240* |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290 - 300* |
| [L4s-L32s](../articles/virtual-machines/windows/sizes-storage.md) |180 - 240* |

A * jelzésű ACU-értékek Intel® Turbo technológia használatával növelik a processzor órajelét, és ezáltal a teljesítményt.  A növekedés mértéke a virtuális gép méretétől és terhelésétől, valamint az ugyanazon a gazdagépen futó számítási feladatoktól függően eltérő lehet.
