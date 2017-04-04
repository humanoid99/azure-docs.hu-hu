1. A portáloldal bal oldalán kattintson a **+** gombra, és írja be a „Virtuális hálózati átjáró” kifejezést a keresőmezőbe. Az **Eredmények** között keresse meg a **Virtuális hálózati átjáró** elemet, és kattintson rá. A **Virtuális hálózati átjáró** panel alján kattintson a **Létrehozás** gombra. Ez megnyitja a **Virtuális hálózati átjáró létrehozása** panelt.
2. A **Virtuális hálózati átjáró létrehozása** panelen töltse ki a virtuális hálózati átjáró értékeit.

    ![Virtuális hálózati átjáró létrehozása panel mezői](./media/vpn-gateway-add-gw-s2s-rm-portal-include/newgw.png "Új átjáró")
3. **Név**: adjon nevet az átjárónak. Ez nem ugyanaz, mint egy átjáró alhálózatának elnevezése. Ez a létrehozni kívánt átjáróobjektum neve.
4. **Átjáró típusa**: válassza ki a **VPN** elemet. A VPN-átjárók a **VPN** virtuális hálózati átjárótípust használják. 
5. **VPN típusa**: válassza ki a konfigurációjához megadott VPN-típust. A legtöbb konfigurációhoz útvonalalapú VPN-típus szükséges.
6. **SKU**: válassza ki az átjáró SKU-ját a legördülő listából. A legördülő listában szereplő SKU-k a kiválasztott VPN-típustól függenek.
7. **Hely**: Lehetséges, hogy le kell görgetnie, hogy megjelenjen a Hely. Állítsa be úgy a **Hely** mezőt, hogy a virtuális hálózat helyére mutasson. Ha a hely nem egyezik meg azzal a régióval, ahol a virtuális hálózat található, a virtuális hálózat nem jelenik meg a következő lépésben a Virtuális hálózat választása legördülő menüben.
8. **Virtuális hálózat**: Válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót. Kattintson a **Virtuális hálózat** elemre a **Virtuális hálózat választása** panel megnyitásához. Válassza ki a VNet elemet. Ha a VNet nem jelenik meg, ellenőrizze, hogy a **Hely** mező arra a régióra mutat-e, amelyikben a virtuális hálózata található.
9. **Nyilvános IP-cím**: Ez a panel létrehoz egy nyilvános IP-címobjektumot, amelyhez dinamikusan hozzá lesz rendelve egy nyilvános IP-cím. A **Nyilvános IP-cím** elemre kattintva nyissa meg a **Nyilvános IP-cím választása** panelt. Ezután az **+Új létrehozása** elemre kattintva nyissa meg a **Nyilvános IP-cím létrehozása** panelt. Adjon egy nevet a nyilvános IP-címnek. Kattintson az **OK** gombra a panel módosításainak mentéséhez.

    ![Nyilvános IP-cím létrehozása](./media/vpn-gateway-add-gw-s2s-rm-portal-include/createpip.png "PIP létrehozása")
10. **Előfizetés**: ellenőrizze, hogy a megfelelő előfizetés van-e kiválasztva.
11. **Erőforráscsoport**: ezt a beállítást a kiválasztott virtuális hálózat határozza meg. 
12. Ne módosítsa a **Helyet** az előbbi beállítások megadása után.
13. Ellenőrizze a beállításokat. Ha azt szeretné, hogy az átjáró megjelenjen az irányítópulton, válassza a panel alján lévő **Rögzítés az irányítópulton** elemet.
14. A **Létrehozás** gombra kattintva hozzákezdhet az átjáró létrehozásához. A rendszer érvényesíti a beállításokat, és az irányítópulton megjelenik a „Deploying Virtual network gateway” (Virtuális hálózati átjáró üzembe helyezése) csempe. Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.
    
    ![Átjáró létrehozása](./media/vpn-gateway-add-gw-s2s-rm-portal-include/creategw.png "Átjáró létrehozása")
15. Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni. A csatlakoztatott eszközre (azaz a virtuális hálózati átjáróra) kattintva további információkat jeleníthet meg.