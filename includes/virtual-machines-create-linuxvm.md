
1. Jelentkezzen be Azure-előfizetésébe a [Csatlakozás az Azure-hoz az Azure CLI 1.0-s verziójáról](../articles/xplat-cli-connect.md) című cikkben felsorolt lépések végrehajtásával.

2. Az alábbiak szerint győződjön meg róla, hogy a klasszikus üzembe helyezési módban van:

    ```azurecli
    azure config mode asm
    ```

3. Az alábbiak szerint keresse meg az elérhető rendszerképek közül a betölteni kívánt Linux-rendszerképeket:

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    Windows-parancsablakokban a grep helyett használja a **find** kifejezést.
   
4. Az `azure vm create` paranccsal hozzon létre egy virtuális gépet az előző listából kiválasztott Linux-rendszerképpel. Ezzel a lépéssel létrehoz egy felhőszolgáltatást és egy tárfiókot. A `-c` kapcsolóval egy meglévő felhőszolgáltatáshoz is csatlakoztathatja ezt a virtuális gépet. Az `-e` kapcsolóval létrehozhat egy SSH-végpontot a Linux-alapú virtuális gépre való bejelentkezéshez. Az alábbi példa egy `myVM` nevű virtuális gépet hoz létre az `Ubuntu-14_04_4-LTS` rendszerkép használatával a `West US` helyen, és hozzáadja a következő felhasználónevet: `ops`
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    A kimenet a következő példához hasonló:

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > Linuxos virtuális gép esetén meg kell adnia az `-e` kapcsolót a `vm create` parancsban. A virtuális gép létrehozása után nem lehet engedélyezni az SSH-t. További részletek az SSH-val kapcsolatban: [SSH használata Linuxon az Azure-on](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

5. Az `azure vm show` parancs használatával ellenőrizheti a virtuális gép attribútumait. Az alábbi példa felsorolja a `myVM` nevű virtuális géppel kapcsolatos információkat:

    ```azurecli   
    azure vm show myVM
    ```

6. Indítsa el a virtuális gépet az `azure vm start` paranccsal a következőképpen:

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>Következő lépések
További részletek az Azure CLI 1.0 virtuális gépekre vonatkozó parancsairól: [Az Azure CLI 1.0 használata a klasszikus üzembehelyezési API-val](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

