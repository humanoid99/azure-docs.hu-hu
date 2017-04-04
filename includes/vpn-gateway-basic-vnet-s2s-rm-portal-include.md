Az alábbi lépésekkel hozhat létre egy VNetet a Resource Manager-alapú üzemi modellben az Azure Portallal. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. További információ a virtuális hálózatok használatáról: [Virtual Network Overview](../articles/virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).

1. Egy böngészőből lépjen az [Azure Portalra](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. Kattintson az **Új** lehetőségre. A **Piactér keresése** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és rákattintva nyissa meg a **Virtuális hálózat** panelt.
3. A Virtuális hálózat panel alján, a **Telepítési modell kiválasztása** listában válassza ki a **Resource Manager** elemet, majd kattintson a **Létrehozás** elemet.
4. A **Virtuális hálózat létrehozása** panelen konfigurálja a virtuális hálózat beállításait. A mezők kitöltése során a piros felkiáltójelből zöld pipa lesz, ha a mezőbe beírt karakterek érvényesek.
5. A **Virtuális hálózat létrehozása** panel a következő példához hasonlóan néz ki. Előfordulhatnak automatikusan kitöltött értékek is. Ebben az esetben cserélje ki az értékeket a saját értékeire.
   
    ![Virtuális hálózat létrehozása panel](./media/vpn-gateway-basic-vnet-s2s-rm-portal-include/createvnet.png "Virtuális hálózat létrehozása panel")
6. **Név**: adja meg a virtuális hálózat nevét.
7. **Címtér**: adja meg a címteret. Ha több címteret szeretne felvenni, vegye fel az elsőt. A virtuális hálózat létrehozása után később további címtereket is felvehet. Ellenőrizze, hogy a megadott címtér ne legyen átfedésben a helyszínen található címtérrel.
8. **Alhálózat neve**: adja meg az alhálózat nevét és az alhálózat címtartományát. A virtuális hálózat létrehozása után később további alhálózatokat is felvehet.
9. **Előfizetés**: ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg a listában. Az előfizetéseket a legördülő menüben módosíthatja.
10. **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének beírásával. Ha új csoportot hoz létre, a tervezett konfigurációs értékeknek megfelelően nevezze el az erőforráscsoportot. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
11. **Hely**: válassza ki a Vnet helyét. Ez a hely határozza meg a VNeten üzembe helyezett erőforrások helyét.
12. Ha szeretné könnyen megtalálni virtuális hálózatát az irányítópulton, akkor válassza a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Létrehozás** gombra.
13. A **Create** (Létrehozás) gombra kattintva létrejön egy csempe az irányítópulton, amely a VNet állapotát mutatja. A virtuális hálózat létrejöttével a csempe is módosul.