1. Keressem meg a virtuális hálózati átjárót.
2. Kattintson a **Kapcsolatok** gombra. A Kapcsolatok panelen kattintson a **+Hozzáadás** elemre a **Kapcsolat hozzáadása** panel megnyitásához.
   
    ![Helyek közötti kapcsolat létrehozása](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection.png)
3. Az **Add Connection** (Kapcsolat hozzáadása) panelen **nevezze el** a kapcsolatot. 
4. A **Connection type** (Kapcsolat típusa) beállításnál válassza a **Site-to-site(IPSec)** Helyek közötti (IPSec) típust.
5. A **Virtual network gateway** (Virtuális hálózati átjáró) értéke fix, mivel erről az átjáróról csatlakozik.
6. A **Local network gateway** (Helyi hálózati átjáró) beállításnál kattintson a **Choose a local network gateway** (Helyi hálózati átjáró kiválasztása) elemre, és válassza ki a használni kívánt helyi hálózati átjárót. 
7. A **Shared Key** (Megosztott kulcs) értékének egyeznie kell azzal az értékkel, amit a helyi helyszíni VPN-eszközhöz használ. A példában az „abc123” értéket használtuk, de érdemesebb egy ennél összetettebb értéket használni. Vegye figyelembe, hogy az itt megadott értéknek meg kell egyeznie a VPN-eszköz konfigurálásakor meghatározott értékkel.
8. A fennmaradó **Subscription** (Előfizetés), **Resource Group** (Erőforráscsoport) és **Location** (Hely) mezők értékei rögzítettek.
9. A kapcsolat létrehozásához kattintson az **OK** gombra. A *Creating Connection* (Kapcsolat létrehozása) üzenet elkezd villogni a képernyőn.
10. Amikor a kapcsolat elkészült, megjelenik a virtuális hálózat átjárójának **Connections** (Kapcsolatok) paneljén.
    
    ![Helyek közötti kapcsolat létrehozása](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connectionstatus450.png)

