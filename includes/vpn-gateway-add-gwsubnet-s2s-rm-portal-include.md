1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. A virtuális hálózat paneljének **Beállítások** részén az **Alhálózatok** elemre kattintva bontsa ki az Alhálózatok panelt.
3. Az **Alhálózatok** panel tetején kattintson a **+Átjáróalhálózat** elemre. Ekkor megnyílik az **Alhálózat hozzáadása** panel. 
   
    ![Az átjáró alhálózatának hozzáadása](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/addgwsubnet.png "Az átjáró alhálózatának hozzáadása")
4. Az alhálózat **nevénél** automatikusan megjelenik a „GatewaySubnet” érték. Ez az érték szükséges ahhoz, hogy az Azure felismerje, hogy az alhálózat egy átjáró alhálózata. Módosítsa úgy a **címtartomány** automatikusan kitöltött értékeit, hogy megfeleljenek a konfigurációs követelményeinek.

    ![Az alhálózat hozzáadása](./media/vpn-gateway-add-gwsubnet-s2s-rm-portal-include/gwsubnet.png "Az alhálózat hozzáadása")
5. A panel alján található **OK** gombra kattintva hozza létre az alhálózatot.