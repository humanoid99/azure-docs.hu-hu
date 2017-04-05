1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal felső sarokban kattintson az **Új > Számítás > Windows Server 2016 Datacenter** elemre.

    ![Navigálás az Azure virtuálisgép-rendszerképekre a portálon](./media/virtual-machines-common-portal-create-fqdn/marketplace-new.png)

3. Válassza a klasszikus üzemi modellt a Windows Server 2016 Datacenter panelén. Kattintson a Létrehozás gombra.

    ![Képernyőkép a portálon elérhető Azure virtuálisgép-rendszerképekről](./media/virtual-machines-common-portal-create-fqdn/deployment-classic-model.png)

## <a name="1-basics-blade"></a>1. Alapvető beállítások panel

Az Alapvető beállítások panelen felügyeleti információkat kell megadni a virtuális gép számára.

1. Adjon meg egy **nevet** a virtuális gép számára. A példában a virtuális gép neve _HeroVM_. A névnek 1–15 karakter hosszúnak kell lennie, és nem tartalmazhat különleges karaktereket.

2. Adjon meg egy **felhasználónevet** és egy erős **jelszót**, amelyeket a helyi fióknak a virtuális gépen való létrehozásához használ a szolgáltatás. A helyi fiókkal jelentkezhet be a virtuális gépre és kezelheti azt. A példában a felhasználónév az _azureuser_.

 A jelszónak 8–123 karakter hosszúnak kell lennie, és meg kell felelnie a következő négy összetettségi feltétel közül háromnak: egy kisbetű, egy nagybetű, egy szám és egy különleges karakter. További információk a [felhasználónév- és jelszókövetelményekről](../articles/virtual-machines/windows/faq.md).

3. Az **Előfizetés** nem kötelező. Gyakori beállítás a „használatalapú fizetés”.

4. Válasszon ki egy létező **Erőforráscsoportot**, vagy adja meg egy új csoport nevét. A példában az erőforráscsoport neve _HeroVMRG_.

5. Válassza ki egy Azure-adatközpont **helyét**, ahol a virtuális gépet szeretné futtatni. A példában a választott hely az **USA keleti régiója**.

6. Ha végzett, kattintson a **Tovább** gombra a következő panelre lépéshez.

    ![Képernyőkép az Alapvető beállítások panel beállításairól az Azure-beli virtuális gép konfigurálásához](./media/virtual-machines-common-portal-create-fqdn/basics-blade-classic.png)

## <a name="2-size-blade"></a>2. Méret panel

A Méret panel megadja a virtuális gép konfigurációs részleteit, és különböző választási lehetőségeket ajánl fel többek között az operációs rendszerre, a processzorok számára, a lemezes tárolás típusára és a becsült havi használati költségekre vonatkozóan.  

Válassza ki a virtuális gép méretét, majd kattintson a **Kijelölés** elemre a folytatáshoz. Ebben a példában a virtuális gép mérete _DS1_\__V2 Standard_.

  ![Képernyőkép a Méret panelen kiválasztható Azure virtuálisgép-méretekről](./media/virtual-machines-common-portal-create-fqdn/vm-size-classic.png)


## <a name="3-settings-blade"></a>3. Beállítások panel

A Beállítások panelen a tárolási és hálózati beállítások adhatók meg. Elfogadhatja az alapértelmezett beállításokat. Az Azure szükség szerint létrehozza a megfelelő bejegyzéseket.

Ha megfelelő méretet választott a virtuális gépnek, kipróbálhatja az Azure Premium Storage-ot – ehhez válassza a Prémium (SSD) elemet a Lemez típusa részen.

A módosítások végrehajtása után kattintson az **OK** gombra.

## <a name="4-summary-blade"></a>4. Összefoglalás panel

Az Összefoglalás panel felsorolja az előző paneleken megadott beállításokat. Kattintson az **OK** gombra, amikor készen áll a rendszerkép létrehozására.

 ![Az Összefoglalás panel jelentése, amely megadja a virtuális gép beállításait](./media/virtual-machines-common-portal-create-fqdn/summary-blade-classic.png)

A virtuális gép létrejötte után a portál megjeleníti az új virtuális gépet a **Minden erőforrás** részben, és megjeleníti a virtuális gép csempéjét az irányítópulton. A rendszer emellett létrehozza és felsorolja a megfelelő felhőszolgáltatást és tárfiókot is. Mind a virtuális gép, mind a felhőszolgáltatás automatikusan elindul, és **Fut** állapotúként jelenik meg.

 ![A virtuálisgép-ügynök és a virtuális gép végpontjainak konfigurálása](./media/virtual-machines-common-portal-create-fqdn/portal-with-new-vm.png)
