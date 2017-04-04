A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Az Azure-ral számos különböző VPN-eszköz használható. A VPN-eszközökkel és a konfigurációs beállításokkal kapcsolatos információért tekintse meg a [VPN-eszközökkel foglalkozó szakaszt](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). A VPN-eszköz konfigurálása előtt ellenőrizze, hogy a használni kívánt VPN-eszközre nem vonatkoznak-e [ismert eszközkompatibilitási problémák](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known). A VPN-eszköz konkrét konfigurációs információiért forduljon az eszköz gyártójához.

A VPN-eszköz konfigurálásakor a következő elemekre lesz szüksége:

- A virtuális hálózati átjáró **nyilvános IP-címe**.

    -  A nyilvános IP-cím Azure Portal használatával történő megkereséséhez válassza a **Virtual network gateways** (Virtuális hálózati átjárók) elemet, majd kattintson az átjárója nevére. 

    - A virtuális hálózati átjáró IP-címének PowerShellel való megkereséséhez használja a következő példát (az értékeket cserélje le a sajátjaira).

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- **Megosztott kulcs**. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor fog megadni. A példákban igen alapvető megosztott kulcsot használunk. Ennél összetettebb kulcsot kell létrehoznia.




