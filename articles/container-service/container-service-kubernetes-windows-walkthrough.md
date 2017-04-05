---
title: "Azure Kubernetes-fürt Windows rendszeren | Microsoft Docs"
description: "Kubernetes-fürt üzembe helyezése Windows-tárolókhoz az Azure Container Service-ben és az első lépések"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: eb3af43b8a13eaaebfa9147848383ff889119d97
ms.lasthandoff: 04/03/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Bevezetés a Kubernetes és a Windows-tárolók használatába a Container Service-ben


Ez a cikk bemutatja, hogyan hozhat létre olyan Kubernetes-fürtöt az Azure Container Service-ben, amely Windows-csomópontokat tartalmaz Windows-tárolók futtatásához. 

> [!NOTE]
> Az Azure Container Service-ben a Windows-tárolók Kubernetes-szel való használatának támogatása előzetes verzióban érhető el. Az Azure Portallal vagy egy Resource Manager-sablonnal hozzon létre egy Kubernetes-fürtöt Windows-csomópontokkal. Ez a funkció jelenleg nem támogatott az Azure CLI 2.0-s verziójával.
>



A következő kép egy Kubernetes-fürt architektúráját mutatja be az Azure Container Service-ben. A fürt egy Linux-főcsomóponttal és két Windows-ügyfélcsomóponttal rendelkezik. 

* A Linux-főcsomópont szolgálja ki a Kubernetes REST API-t, és SSH-val a 22. porton, a `kubectl` használatával pedig a 443. porton érhető el. 
* A Windows-ügyfélcsomópontok egy Azure rendelkezésre állási csoportba vannak besorolva, és a tárolókat futtatják. A Windows-csomópontok RDP SSH-alagúttal érhetők el a főcsomóponton keresztül. A rendszer automatikusan adja hozzá az Azure Load Balancer-szabályokat a fürthöz a közzétett szolgáltatásoktól függően.


![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

Minden virtuális gép ugyanazon a privát virtuális hálózaton található, és teljes mértékben elérhetők egymás számára. Minden virtuális gép egy kubeletet, Dockert és egy proxyt futtat.

## <a name="prerequisites"></a>Előfeltételek


* **Nyilvános SSH RSA-kulcs**: Ha a portálon vagy valamelyik Azure-beli gyorsindítási sablonnal végzi az üzembe helyezést, meg kell adnia az Azure Container Service virtuális gépeivel történő hitelesítésre szolgáló SSH RSA nyilvános kulcsot. A Secure Shell (SSH) RSA-kulcsok létrehozásával kapcsolatban lásd az [OS X és Linux](../virtual-machines/linux/mac-create-ssh-keys.md) vagy a [Windows](../virtual-machines/linux/ssh-from-windows.md) rendszerhez készült útmutatót. 

* **Egyszerű szolgáltatás ügyfél-azonosítója és kulcsa**: További információ és útmutatás: [Tudnivalók a Kubernetes-fürthöz tartozó egyszerű szolgáltatásról](container-service-kubernetes-service-principal.md).




## <a name="create-the-cluster"></a>A fürt létrehozása

Az Azure Portallal [létrehozhat egy Kubernetes-fürtöt](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal) Windows-ügyfélcsomópontokkal. A fürt létrehozásakor ügyeljen a következő beállításokra:

* Az **Alapvető beállítások** panelen a **Vezénylő** részben válassza a **Kubernetes** lehetőséget. 

  ![Kubernetes vezénylő kiválasztása](media/container-service-kubernetes-windows-walkthrough/portal-select-kubernetes.png)

* A **Fő konfiguráció** panelen adja meg a felhasználói hitelesítő adatokat és az egyszerű szolgáltatás hitelesítő adatait a fő Linux-csomópontokhoz. 1, 3 vagy 5 fő csomópontot válasszon.

* Az **Ügynökkonfiguráció** panel **Operációs rendszer** területén válassza a **Windows (előnézet)** lehetőséget. Adja meg a rendszergazdai hitelesítő adatokat a Windows-ügyfélcsomópontokhoz.

  ![Windows-ügynökök kiválasztása](media/container-service-kubernetes-windows-walkthrough/portal-select-windows.png)

További részletekért lásd: [Azure Container Service-fürt üzembe helyezése](container-service-deployment.md).

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A `kubectl` parancssori eszközzel csatlakozzon a helyi számítógépről a Kubernetes-fürt főcsomópontjához. A `kubectl` telepítésének és beállításának lépései: [Csatlakozás Azure Container Service-fürthöz](container-service-connect.md#connect-to-a-kubernetes-cluster). A `kubectl` parancsokkal elérheti a Kubernetes webes felhasználói felületét, és kezelheti Windows-tárolók számítási feladatait.

## <a name="create-your-first-kubernetes-service"></a>Az első Kubernetes-szolgáltatás létrehozása

A fürt létrehozása és a `kubectl` használatával való kapcsolódás után megpróbálhat elindítani és közzétenni az interneten egy alapszintű Windows-webappot. Ebben a példában egy YAML-fájllal határozza meg a tároló erőforrásait, majd létrehozza a `kubctl apply` paranccsal.

1. A csomópontok listájának megtekintéséhez írja be a következőt: `kubectl get nodes`. A csomópontok összes részletének megtekintéséhez írja be a következőt:  

    ```
    kubectl get nodes -o yaml
    ```

2. Hozzon létre egy `simpleweb.yaml` nevű fájlt, és másolja a következőket. Ez a fájl beállít egy webappot a Windows Server 2016 Server Core alap operációsrendszer-képpel a [Docker Hubból](https://hub.docker.com/r/microsoft/windowsservercore/).  

```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add('http://*:80/') ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at http://*:80/') ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

      
> [!NOTE] 
> A konfiguráció tartalmazza a következőt: `type: LoadBalancer`. Ez a beállítás azt eredményezi, hogy az Azure Load Balanceren közzéteszi a szolgáltatást az interneten. További információ: [Tárolók terheléselosztása Kubernetes-fürtön az Azure Container Service-ben](container-service-kubernetes-load-balancing.md).
>

## <a name="start-the-application"></a>Az alkalmazás elindítása

1. A alkalmazás elindításához írja be a következőt:  

    ```
    kubectl apply -f simpleweb.yaml
    ```  
  
  
2. A szolgáltatás üzembe helyezésének ellenőrzéséhez (ez körülbelül 30 másodpercet vesz igénybe) írja be a következőt:  

    ```
    kubectl get pods
    ```

3. A szolgáltatás elindítása után a szolgáltatás belső és külső IP-címeinek megtekintéséhez írja be a következőt:

    ```
    kubectl get svc
    ``` 
  
    ![Windows -szolgáltatás IP-címei](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

    A külső IP-cím hozzáadása több percet igényel. Mielőtt a terheléselosztó konfigurálja a külső címet, a következőképpen jelenik meg: `<pending>`.

4. Miután elérhetővé válik a külső IP-cím, elérheti a szolgáltatást a webböngészőjében.

    ![Windows Server-app böngészőben](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>A Windows-csomópontok elérése
A Windows-csomópontok a helyi Windows-számítógépekről távoli asztali kapcsolaton keresztül érhetők el. RDP SSH-alagút használatát javasoljuk a főcsomóponton keresztül. 

Windows-rendszeren az SSH-alagutak többféleképpen is létrehozhatók. Ez a témakör ismerteti, hogyan hozható létre az alagút a PuTTY használatával.

1. [Töltse le a PuTTY alkalmazást](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) Windows rendszerére.

2. Futtassa az alkalmazást.

3. Adjon meg egy állomásnevet, amely a fürt rendszergazdai felhasználónevéből és a fürt első főkiszolgálójának nyilvános DNS-nevéből áll. A **Host Name** (Gazdagép neve) a következőhöz hasonló: `adminuser@PublicDNSName`. A **Port** mezőben adja meg a 22-es értéket.

  ![A PuTTY-konfigurálásának 1. lépése](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. Válassza az **SSH > Auth** (SSH > Hitelesítés) parancsot. Adja meg a hitelesítéshez használandó titkos kulcsfájl (.ppk) elérési útját. Ez a fájl a [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) vagy egy hasonló eszköz segítségével hozható létre a fürt létrehozásakor használt SSH-kulcsból.

  ![A PuTTY-konfigurálásának 2. lépése](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. Válassza az **SSH > Tunnels** (SSH > Alagutak) elemet, és konfigurálja a továbbított portokat. Mivel a helyi Windows-gép már használja a 3389-es portot, ajánlott a következő beállításokat használni a 0. Windows-csomópont és az 1. Windows-csomópont eléréséhez. (A további Windows-csomópontok hasonló minta szerint használhatók.)

    **0. Windows csomópont**

    * **Forrásport:** 3390
    * **Cél:** 10.240.245.5:3389

    **1. Windows-csomópont**

    * **Forrásport:** 3391
    * **Cél:** 10.240.245.6:3389

    ![Windows RDP-alagutak képe](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. Amikor elkészült, a **Session > Save** (Munkamenet > Mentés) paranccsal mentse a kapcsolat konfigurációját.

7. A PuTTY-munkamenethez az **Open** (Megnyitás) gombra kattintva csatlakozhat. Létesítsen kapcsolatot a fő csomóponttal.

8. Indítsa el a távoli asztali kapcsolatot. Ha az első Windows-csomóponthoz szeretne csatlakozni, a **Számítógép** területen adja meg a `localhost:3390` értéket, majd kattintson a **Csatlakozás** elemre. (Ha a másodikhoz szeretne csatlakozni, adja meg a `localhost:3390` értéket és így tovább.) A kapcsolat létesítéséhez adja meg az üzembe helyezéskor konfigurált helyi Windows rendszergazdai jelszavát.


## <a name="next-steps"></a>Következő lépések

Az alábbi javasolt hivatkozások további információkat nyújtanak a Kubernetesről:

* [Kubernetes Bootcamp](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) – bemutatja, hogyan lehet üzembe helyezni, méretezni és frissíteni a tárolóalapú alkalmazásokat, illetve hibakeresést végezni rajtuk.
* [Kubernetes felhasználói útmutató](http://kubernetes.io/docs/user-guide/) – információkat tartalmaz a programok meglévő Kubernetes-fürtökben való futtatásáról.
* [Kubernetes-példák](https://github.com/kubernetes/kubernetes/tree/master/examples) – példákkal szolgál a valódi alkalmazások Kubernetes használatával való futtatására.
