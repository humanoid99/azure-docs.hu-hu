

A virtuálisgép-bővítmények a következőkben lehetnek a segítségére:

* Biztonsági és identitásszolgáltatások (például a fiókértékek visszaállítása vagy a kártevőirtó szoftverek használata) módosítása
* Figyelés és diagnosztika indítása, leállítása és konfigurálása
* Kapcsolati szolgáltatások (például az RDP vagy az SSH) visszaállítása vagy telepítése
* A virtuális gépek diagnosztizálása, megfigyelése és felügyelete

Számos egyéb szolgáltatás is elérhető, és rendszeresen jelennek meg új, virtuálisgép-bővítményekkel kapcsolatos szolgáltatások. Ez a cikk a Windows és Linux rendszerhez való Azure virtuálisgép-ügynököket ismerteti, illetve azt, hogy miként támogatják a virtuálisgép-bővítmények szolgáltatásait. A virtuálisgép-bővítmények szolgáltatási kategóriák szerint rendezett listájáért tekintse át az [Azure virtuálisgép-bővítményekkel és szolgáltatásaikkal](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) foglalkozó témakört.

## <a name="azure-vm-agents-for-windows-and-linux"></a>Windows és Linux rendszerhez való Azure virtuálisgép-ügynökök
Az Azure virtuálisgép-ügynök (VM-ügynök) egy olyan biztonságos, egyszerűsített folyamat, amely virtuálisgép-bővítményeket telepít, konfigurál vagy távolít el Azure virtuálisgép-példányokon. A virtuálisgép-ügynök az Azure virtuális gép biztonságos, helyi vezérlési szolgáltatásaként működik. Az ügynök által betöltött bővítmények az adott példány hatékony használatának elősegítéséhez biztosítanak speciális szolgáltatásokat.

Kétféle Azure virtuálisgép-ügynök létezik, egy a Windows, egy pedig a Linux rendszerű virtuális gépek számára.

Ha azt szeretné, hogy az adott virtuálisgép-példány egy vagy több virtuálisgép-bővítményt használjon, a példánynak telepített virtuálisgép-ügynökkel kell rendelkeznie. Az Azure Portal és a **Marketplace-ről** származó rendszerkép használatával létrehozott virtuálisgép-rendszerképek a létrehozási folyamat során automatikusan telepítik a virtuálisgép-ügynököt. Ha egy virtuális gép nem rendelkezik virtuálisgép-ügynökkel, a virtuálisgép-példány létrehozását követően telepítheti a virtuálisgép-ügynököt. Azt is megteheti, hogy egy egyéni virtuálisgép-rendszerképre telepíti az ügynököt, amelyet aztán feltölt.

> [!IMPORTANT]
> Ezek a virtuálisgép-ügynökök rendkívül leegyszerűsített szolgáltatások, amelyek lehetőséget adnak a virtuálisgép-példányok biztonságos felügyeletére. Előfordulhatnak olyan esetek, amikor nincs szükség a virtuálisgép-ügynökre. Ilyen esetekben olyan virtuális gépeket hozzon létre az Azure CLI vagy a PowerShell használatával, amelyeken a virtuálisgép-ügynök nincs telepítve. Bár a virtuálisgép-ügynök fizikailag is eltávolítható, a virtuálisgép-bővítmények viselkedése a példányon nincs meghatározva. Ennek következtében a telepített virtuálisgép-ügynökök eltávolítása nem támogatott.
>

A virtuálisgép-ügynök a következő helyzetekben van engedélyezve:

* Ha az Azure Portal és egy, a **Marketplace-ről** származó rendszerkép használatával hoz létre egy virtuálisgép-példányt.
* Ha a [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) vagy a [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx) parancsmag használatával hoz létre egy virtuálisgép-példányt. Ha virtuálisgép-ügynök nélkül szeretne virtuális gépet létrehozni, adja a **–DisableGuestAgent** paramétert az [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) parancsmaghoz.

* Ha manuálisan tölti le és telepíti a virtuálisgép-ügynököt egy meglévő VM-példányon, és a **ProvisionGuestAgent** paramétert **true** értékűre állítja. Ez a módszer Windows- és Linux-ügynökök esetében alkalmazható egy PowerShell-parancs vagy REST-hívás használatával. (Ha nem állítja be a **ProvisionGuestAgent** értékét a virtuálisgép-ügynök manuális telepítését követően, az ügynök hozzáadását a rendszer nem fogja megfelelően észlelni.) A következő példakód bemutatja, hogy ez hogyan hajtható végre a PowerShell használatával, ha a `$svc` és a `$name` argumentum már meg lett határozva:

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* Ha egy olyan virtuálisgép-rendszerképet hoz létre, amely tartalmaz egy telepített virtuálisgép-ügynököt. Amint a virtuálisgép-ügynököt tartalmazó rendszerkép létrejött, feltöltheti az Azure-ba. Windows-alapú virtuális gépek esetében töltse le a [Windows virtuálisgép-ügynök .msi fájlját](http://go.microsoft.com/fwlink/?LinkID=394789), és telepítse a virtuálisgép-ügynököt. Linux-alapú virtuális gépek esetében a virtuálisgép-ügynököt a GitHub-adattárból telepítheti a <https://github.com/Azure/WALinuxAgent> helyről. A virtuálisgép-ügynök Linux rendszeren való telepítésével kapcsolatos további információkért lásd [az Azure Linux virtuálisgép-ügynökökkel kapcsolatos felhasználói kézikönyvet](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Szolgáltatásként nyújtott platformok (PaaS) esetében a virtuálisgép-ügynök neve **WindowsAzureGuestAgent**, és mindig elérhető a webes és feldolgozói szerepkörű virtuális gépeken. (További információ: [Azure szerepkör-architektúra](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx).) A szerepkörrel rendelkező virtuális gépek virtuálisgép-ügynöke mostantól ugyanúgy adhat bővítményeket a felhőszolgáltatásbeli virtuális gépekhez, mint a perzisztens virtuális gépekhez. A legnagyobb különbség a szerepkörrel rendelkező és a perzisztens virtuális gépek virtuálisgép-bővítményei között a bővítmények hozzáadásában van. A szerepkörrel rendelkező virtuális gépek esetében a bővítmények először a felhőszolgáltatáshoz lesznek hozzáadva, majd a felhőszolgáltatásban lévő üzemelő példányokhoz.
>
> A [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) parancsmaggal megjelenítheti a szerepkörrel rendelkező virtuális gépek összes elérhető bővítményének listáját.
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>Virtuálisgép-bővítmények keresése, hozzáadása, frissítése és eltávolítása
Az ezekkel a feladatokkal kapcsolatos részletekért lásd: [Azure virtuálisgép-bővítmények hozzáadása, keresése, frissítése és eltávolítása](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
