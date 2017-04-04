---
title: "Ismerkedés a Windowshoz készült Azure File Storage szolgáltatással | Microsoft Docs"
description: "A felhőben tárolhatja a fájljait az Azure File Storage használatával, valamint a felhőalapú fájlmegosztását Azure virtuális gépről vagy Windowst futtató helyszíni alkalmazásokból csatlakoztathatja."
services: storage
documentationcenter: .net
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6a889ee1-1e60-46ec-a592-ae854f9fb8b6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: renash
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: fcdeac53c79551000b48a47a1afc65e082bcc692
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-azure-file-storage-on-windows"></a>Get started with Azure File storage on Windows (Ismerkedés a Windowshoz készült Azure File Storage szolgáltatással)
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

[!INCLUDE [storage-file-overview-include](../../includes/storage-file-overview-include.md)]

A File Storage Linuxszal való használatáról további információt itt talál: [How to use Azure File Storage with Linux](storage-how-to-use-files-linux.md) (Az Azure File Storage használata Linuxszal).

További részletekért a File Storage méretezési és teljesítménycéljaival kapcsolatban lásd: [Azure Storage Scalability and Performance Targets](storage-scalability-targets.md#scalability-targets-for-blobs-queues-tables-and-files) (Az Azure Storage méretezhetőségi és teljesítménycéljai).

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

## <a name="video-using-azure-file-storage-with-windows"></a>Videó: Az Azure File storage használata Windows rendszeren
Ez a videó azt mutatja be, hogyan hozhat létre Azure fájlmegosztásokat Windows rendszeren.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-File-Storage-with-Windows/player]
> 
> 

## <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ez az alapszintű bemutató a Microsoft Azure File Storage alapvető funkcióit mutatja be. Az útmutató a következő lépéseket fedi le:

* Az Azure Portal vagy a PowerShell használata: új Azure-fájlmegosztás létrehozása, könyvtár hozzáadása, helyi fájl feltöltése a megosztásba és a könyvtár fájljainak listázása.
* Fájlmegosztás csatlakoztatása, mintha SMB-megosztást csatlakoztatna.
* Hozzáférés fájlmegosztáshoz helyszíni alkalmazásból az Azure Storage .NET-hez készült ügyféloldali kódtárával. Konzolalkalmazás létrehozása és a következő műveletek végrehajtása a fájlmegosztással:
  * A megosztásban található fájl tartalmának írása a konzolablakba.
  * Egy fájlmegosztás kvótájának (maximális méretének) beállítása.
  * Közös hozzáférésű jogosultságkód létrehozása egy, a megosztásban meghatározott megosztott elérési házirendet használó fájlhoz.
  * Fájl másolása másik fájlba egy tárfiókon belül.
  * Fájl másolása blobba egy tárfiókon belül.
* Hibaelhárítás az Azure Storage Metrics segítségével

A fájlmegosztás mostantól minden tárfiókhoz lehetséges, így használhat meglévő tárfiókot használ, vagy létrehozhat egy újat. Új tárfiók létrehozása: [How to create a storage account](storage-create-storage-account.md#create-a-storage-account) (Tárfiók létrehozása).

## <a name="use-the-azure-portal-to-manage-a-file-share"></a>Fájlmegosztás kezelése az Azure Portallal
Az [Azure Portal](https://portal.azure.com) kezelőfelületet nyújt a felhasználók számára a fájlmegosztásokhoz. A portál a következőket teszi lehetővé:

* Fájlmegosztás létrehozása
* Fájlok fel- és letöltése a fájlmegosztásból
* Az egyes fájlmegosztások valós használatának figyelése
* A megosztás méretkvótájának módosítása
* A `net use` parancs lekérése fájlmegosztás csatlakoztatásához Windows ügyfélről

### <a name="create-file-share"></a>Fájlmegosztás létrehozása
1. Jelentkezzen be az Azure portálra.
2. A navigációs menüben kattintson a **Tárfiókok** vagy a **Tárfiókok (klasszikus)** elemre.
   
    ![Képernyőkép a fájlmegosztás létrehozásáról a portálon](./media/storage-dotnet-how-to-use-files/files-create-share-0.png)
3. Válassza ki a tárfiókját.
   
    ![Képernyőkép a fájlmegosztás létrehozásáról a portálon](./media/storage-dotnet-how-to-use-files/files-create-share-1.png)
4. Válassza a „Fájlok” szolgáltatást.
   
    ![Képernyőkép a fájlmegosztás létrehozásáról a portálon](./media/storage-dotnet-how-to-use-files/files-create-share-2.png)
5. Kattintson a „Fájlmegosztások” elemre, és a hivatkozás segítségével hozza létre az első fájlmegosztását.
   
    ![Képernyőkép a fájlmegosztás létrehozásáról a portálon](./media/storage-dotnet-how-to-use-files/files-create-share-3.png)
6. Adja meg a fájlmegosztás nevét és méretét (legfeljebb 5120 GB), és hozza létre első fájlmegosztását. Ha a fájlmegosztás létrejött, bármely, SMB 2.1 vagy SMB 3.0 protokollt támogató fájlrendszerről csatlakoztathatja azt.
   
    ![Képernyőkép a fájlmegosztás létrehozásáról a portálon](./media/storage-dotnet-how-to-use-files/files-create-share-4.png)

### <a name="upload-and-download-files"></a>Fájlok fel- és letöltése
1. Válasszon egy már létrehozott fájlmegosztást.
   
    ![Képernyőkép a fájlok feltöltéséről és letöltéséről a portálon](./media/storage-dotnet-how-to-use-files/files-upload-download-1.png)
2. Kattintson a **Feltöltés** gombra a fájlok feltöltésére szolgáló felület megnyitásához.
   
    ![Képernyőkép a fájlok feltöltéséről a portálon](./media/storage-dotnet-how-to-use-files/files-upload-download-2.png)
3. A jobb gombbal kattintson a fájlra, és válassza a **Letöltés** lehetőséget a helyi fájlba való letöltéséhez.
   
    ![Képernyőkép a fájlok letöltéséről a portálon](./media/storage-dotnet-how-to-use-files/files-upload-download-3.png)

### <a name="manage-file-share"></a>A fájlmegosztás kezelése
1. Kattintson a **Kvóta** elemre a fájlmegosztás méretének módosításához (legfeljebb 5120 GB).
   
    ![Képernyőkép a fájlmegosztás méretének beállításáról a portálon](./media/storage-dotnet-how-to-use-files/files-manage-1.png)
2. Kattintson a **Csatlakoztatás** gombra a fájlmegosztás Windows rendszeren való csatlakoztatásához szükséges parancssor lekéréséhez.
   
    ![Képernyőkép a fájlmegosztás csatlakoztatásáról](./media/storage-dotnet-how-to-use-files/files-manage-2.png)
   
    ![Képernyőkép a fájlmegosztás csatlakoztatásáról](./media/storage-dotnet-how-to-use-files/files-manage-3.png)
   
   > [!TIP]
   > A csatlakoztatáshoz szükséges tárelérési kulcsokat a tárfiók **Beállítások** menüjében, a **Tárelérési kulcsok** gombbal tekintheti meg.
   > 
   > 
   
    ![Képernyőkép a tárfiók tárelérési kulcsainak megtekintéséről](./media/storage-dotnet-how-to-use-files/files-manage-4.png)
   
    ![Képernyőkép a tárfiók tárelérési kulcsainak megtekintéséről](./media/storage-dotnet-how-to-use-files/files-manage-5.png)

## <a name="use-powershell-to-manage-a-file-share"></a>Fájlmegosztás kezelése a PowerShell-lel
Az Azure PowerShell szolgáltatást is használhatja fájlmegosztások létrehozására és kezelésére.

### <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Az Azure Storage PowerShell-parancsmagjainak telepítése
A PowerShell használatának előkészítéseként töltse le és telepítse az Azure PowerShell-parancsmagokat. A telepítési helyre és a telepítésre vonatkozó utasításokért lásd: [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Az Azure PowerShell telepítése és konfigurálása).

> [!NOTE]
> Javasoljuk, hogy frissítsen a legújabb Azure PowerShell modulra, vagy töltse le és telepítse azt.
> 
> 

Kattintson a **Start** gombra, és írja be a **Windows PowerShell** kifejezést egy Azure PowerShell ablak megnyitásához. A PowerShell-ablak betölti az Azure PowerShell modult.

### <a name="create-a-context-for-your-storage-account-and-key"></a>Környezet létrehozása a tárfiókhoz és a fiókkulcshoz
Hozza létre a tárfiók környezetét. A környezet magában foglalja a tárfiók nevét és a fiókkulcsot. Útmutatás a fiókkulcs átmásolásához az [Azure Portalról](https://portal.azure.com): [A tárelérési kulcs megtekintése és másolása](storage-create-storage-account.md#view-and-copy-storage-access-keys).

Az alábbi példában cserélje ki a `storage-account-name` és a `storage-account-key` elemet a tárfiók nevére és kulcsára.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

### <a name="create-a-new-file-share"></a>Új fájlmegosztás létrehozása
Ezután hozzon létre egy `logs` nevű új megosztást.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

Így létrejött egy fájlmegosztás a fájltárolóban. A következő lépésben hozzá kell adnia egy könyvtárat és egy fájlt.

> [!IMPORTANT]
> A fájlmegosztás nevében csak kisbetű szerepelhet. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).
> 
> 

### <a name="create-a-directory-in-the-file-share"></a>Könyvtár létrehozása a fájlmegosztásban
Hozzon létre a megosztásban egy könyvtárat. Az alábbi példában szereplő könyvtár neve `CustomLogs`.

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

### <a name="upload-a-local-file-to-the-directory"></a>Helyi fájl feltöltése a könyvtárba
Töltsön fel egy helyi fájlt a könyvtárba. Az alábbi példa a következő helyről tölt fel egy fájlt: `C:\temp\Log1.txt`. A fájl elérési útját úgy szerkessze, hogy egy, a helyi gépen található érvényes fájlra mutasson.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

### <a name="list-the-files-in-the-directory"></a>A könyvtárban található fájlok listázása
Ha látni szeretné a fájlt a könyvtárban, listázhatja a könyvtárban található összes fájlt. A parancs visszaadja a CustomLogs könyvtárban található összes fájlt és alkönyvtárt (ha van alkönyvtár).

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

A Get-AzureStorageFile parancs bármilyen átadott könyvtárobjektum fájljait és könyvtárait listázza. A „Get-AzureStorageFile -Share $s” parancs a gyökérkönyvtár fájljait és könyvtárait listázza. Ha egy alkönyvtár fájljait szeretné listázni, meg az alkönyvtárat kell megadnia a Get-AzureStorageFile parancsnak. Így a parancs függőleges vonalig tartó első része visszaadja a CustomLogs alkönyvtár egy könyvtárpéldányát, amelyet aztán átad a Get-AzureStorageFile parancsnak, ami visszaadja a CustomLogs könyvtárban található fájlok és könyvtárak listáját.

### <a name="copy-files"></a>Fájlok másolása
Az Azure PowerShell 0.9.7-es verziójától kezdve másolhat egy fájlt egy másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. Alább bemutatjuk, hogyan hajthatja végre ezeket a másolási műveleteket a PowerShell-parancsmagokkal.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```

## <a name="mount-the-file-share"></a>A fájlmegosztás csatlakoztatása
Az SMB 3.0 támogatásával a File Storage mostantól támogatja a titkosítást és az SMB 3.0-ügyfelek állandó leíróit. A titkosítás támogatása azt jelenti, hogy az SMB 3.0-ügyfelek bárhonnan csatlakoztathatnak fájlmegosztást, beleértve az alábbiakat:

* Ugyanabba a régióba tartozó Azure virtuális gép (ezt az SMB 2.1 is támogatja)
* Másik régióhoz tartozó Azure virtuális gép (csak SMB 3.0 esetében)
* Helyszíni ügyfélalkalmazás (csak SMB 3.0 esetében)

Ha egy ügyfél hozzáfér a File Storage-hoz, a használt SMB verziója az operációs rendszer által támogatott SMB-verziótól függ. Az alábbi táblázat a Windows-ügyfelek támogatását összegzi. Az [SMB-verziókról](http://blogs.technet.com/b/josebda/archive/2013/10/02/windows-server-2012-r2-which-version-of-the-smb-protocol-smb-1-0-smb-2-0-smb-2-1-smb-3-0-or-smb-3-02-you-are-using.aspx) ebben a blogbejegyzésben olvashat részletesebben.

| Windows-ügyfél | Támogatott SMB-verzió |
|:--- |:--- |
| Windows 7 |SMB 2.1 |
| Windows Server 2008 R2 |SMB 2.1 |
| Windows 8 |SMB 3.0 |
| Windows Server 2012 |SMB 3.0 |
| Windows Server 2012 R2 |SMB 3.0 |
| Windows 10 |SMB 3.0 |

### <a name="mount-the-file-share-from-an-azure-virtual-machine-running-windows"></a>Fájlmegosztás csatlakoztatása egy Windowst futtató Azure virtuális gépről
Az Azure fájlmegosztások csatlakoztatásának bemutatásához létrehozunk egy Windowst futtató Azure virtuális gépet, és távolról jelentkezünk be a megosztás csatlakoztatásához.

1. Először hozzon létre egy új Azure-beli virtuális gépet a [Windows rendszerű virtuális gép létrehozása az Azure Portalon](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) útmutatásait követve.
2. Ezután lépjen be távolról a virtuális gépre a [Bejelentkezés Windows rendszerű virtuális gépre az Azure Portal segítségével](../virtual-machines/virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) útmutatásai alapján.
3. Nyisson meg egy PowerShell-ablakot a virtuális gépen.

### <a name="persist-your-storage-account-credentials-for-the-virtual-machine"></a>A tárfiók hitelesítő adatainak megőrzése a virtuális gépen
A fájlmegosztáshoz való csatlakozás előtt gondoskodjon róla, hogy a virtuális gép megőrizze a tárfiók hitelesítő adatait. Ez a lépés lehetővé teszi, hogy a Windows automatikusan újracsatlakozzon a fájlmegosztáshoz, amikor a virtuális gép újraindul. A fiók hitelesítő adatainak megőrzéséhez futtassa a `cmdkey` parancsot a virtuális gép PowerShell-ablakából. A `<storage-account-name>` kifejezést cserélje a tárfiókja nevére, a `<storage-account-key>` kifejezést pedig a tárfiók kulcsára. Az „AZURE” tartományt explicit módon kell megadni, ahogy az alábbi mintában is látható. 

```
cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
```

A Windows így újból csatlakozik a fájlmegosztáshoz, amikor a virtuális gép újraindul. A megosztás újracsatlakozásának ellenőrzéséhez futtassa a `net use` parancsot a PowerShell-ablakból.

Vegye figyelembe, hogy a hitelesítő adatok csak abban a környezetben őrződnek meg, amelyben a `cmdkey` fut. Ha szolgáltatásként futó alkalmazást fejleszt, a hitelesítő adatokat abban a környezetben is meg kell őriznie.

### <a name="mount-the-file-share-using-the-persisted-credentials"></a>A fájlmegosztás csatlakoztatása a megőrzött hitelesítő adatokkal
Ha létrejött a távoli kapcsolat a virtuális géppel, az alábbi szintaxissal futtathatja a `net use` parancsot a fájlmegosztás csatlakoztatásához. A `<storage-account-name>` kifejezést cserélje a tárfiókja, a `<share-name>` kifejezést pedig a File Storage-megosztás nevére.

```
net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>

example :
net use z: \\samples.file.core.windows.net\logs
```

Mivel az előző lépésben megőrizte a tárfiók hitelesítő adatait, nem kell megadnia őket a `net use` paranccsal. Ha még nem őrizte meg a hitelesítő adatait, szerepeltesse őket `net use` parancsnak átadott paraméterként, az alábbi példában látható módon.

```
net use <drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /u:AZURE\<storage-account-name> <storage-account-key>

example :
net use z: \\samples.file.core.windows.net\logs /u:AZURE\samples <storage-account-key>
```

Mostantól ugyanúgy használhatja a File Storage-megosztást a virtuális gépről, mint bármilyen más meghajtóról. A parancssorból kiadhat szabványos fájlparancsokat, vagy megtekintheti a csatlakoztatott megosztást és tartalmát a Fájlkezelőből. Olyan kódot is futtathat a virtuális gépen, amely a fájlmegosztáshoz szabványos (például a .NET-keretrendszerben a [System.IO névterek](http://msdn.microsoft.com/library/gg145019.aspx) által biztosított) Windows fájl I/O API-kkal fér hozzá.

A fájlmegosztást csatlakoztathatja egy Azure felhőszolgáltatást futtató szerepkörről is, ha távolról csatlakozik a szerepkörhöz.

### <a name="mount-the-file-share-from-an-on-premises-client-running-windows"></a>Fájlmegosztás csatlakoztatása egy Windowst futtató helyszíni ügyfélhez
Ha helyszíni ügyfélről szeretne fájlmegosztást csatlakoztatni, először hajtsa végre az alábbi lépéseket:

* Telepítsen egy SMB 3.0-t támogató Windows-verziót. A Windows az SMB 3.0 titkosítást fogja használni, hogy biztonságosan továbbítsa az adatokat a helyszíni ügyfél és a felhőben található Azure fájlmegosztás között.
* Az SMB protokoll előírásai szerint nyisson internetkapcsolatot a helyi hálózat 445-ös portján (TCP, kimenő).

> [!NOTE]
> Bizonyos internetszolgáltatók blokkolhatják a 445-ös portot. Ez esetben lépjen kapcsolatba a szolgáltatójával.
> 
> 

## <a name="develop-with-file-storage"></a>Fejlesztés a File Storage segítségével
A File Storage-t meghívó kód megírásához használhatja a Storage .NET-hez és Javához elérhető klienskódtárait, vagy az Azure Storage REST API-ját. A jelen szakaszban ismertetett példa bemutatja, hogy hogyan használható egy fájlmegosztás [az Azure Storage .NET-hez készült ügyféloldali kódtára](https://msdn.microsoft.com/library/mt347887.aspx) segítségével egy egyszerű, az asztalon futó konzolalkalmazással.

### <a name="create-the-console-application-and-obtain-the-assembly"></a>A konzolalkalmazás létrehozása és az összeállítás elérése
Hozzon létre egy új Windows-konzolalkalmazást a Visual Studióban. A következő lépések azt mutatják be, hogyan hozhat létre konzolalkalmazást a Visual Studio 2017-ben, de a lépések a Visual Studio más verziói esetén is hasonlók.

1. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.
2. Válassza az **Installed** (Telepítve) > **Templates** (Sablonok) > **Visual C#** > **Windows Classic Desktop** (Windows klasszikus asztal) lehetőséget
3. Válassza a **Console App (.NET Framework)** (Konzolalkalmazás (.NET keretrendszer) lehetőséget
4. Írja be az alkalmazás nevét a **Name:** (Név:) mezőbe
5. Kattintson az **OK** gombra.

Az oktatóanyagban szereplő példák hozzáadhatók a konzolalkalmazás `Program.cs` fájljában található `Main()` metódushoz.

Az Azure Storage ügyféloldali kódtárat bármilyen típusú .NET-alkalmazásban használhatja, ideértve az Azure-felhőszolgáltatásokat vagy -webappokat és az asztali és mobilalkalmazásokat is. Ebben az útmutatóban az egyszerűség kedvéért egy konzolalkalmazást használunk.

### <a name="use-nuget-to-install-the-required-packages"></a>A szükséges csomagok telepítése a NuGettel
Két csomagra kell hivatkoznia a projektben az oktatóanyag teljesítéséhez:

* [A Microsoft Azure Storage ügyféloldali kódtára a .NET-hez](https://www.nuget.org/packages/WindowsAzure.Storage/): Ez a csomag programozott hozzáférést biztosít a tárfiókja adatforrásaihoz.
* [A Microsoft Azure Configuration Manager könyvtár a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): Ez a csomag egy osztályt biztosít a konfigurációs fájlban található kapcsolati karakterlánc elemzéséhez, függetlenül attól, hogy az alkalmazás hol fut.

A NuGettel mindkét csomagot beszerezheti. Kövesse az alábbi lépéseket:

1. Kattintson a jobb gombbal a projektjére a **Megoldáskezelőben**, és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Keressen rá az interneten a „WindowsAzure.Storage” kifejezésre, és kattintson az **Install** (Telepítés) gombra a Storage ügyféloldali kódtár és függőségeinek telepítéséhez.
3. Keresse rá az interneten a „WindowsAzure.ConfigurationManager” kifejezésre, és kattintson az **Install** (Telepítés) gombra az Azure Configuration Manager telepítéséhez.

### <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>A tárfiók hitelesítő adatainak mentése az app.config fájlba
Mentse el a hitelesítő adatokat a projekt app.config fájljába. Szerkessze az app.config fájlt úgy, hogy hasonló legyen az alábbi példában bemutatotthoz. Ehhez cserélje ki a `myaccount` elemet a tárfiók nevére, a `mykey` elemet pedig a tárfiók kulcsára.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> Az Azure Storage Emulator legújabb verziója nem támogatja a File Storage-t. A kapcsolati karakterláncnak egy Azure Storage-tárfiókot kell céloznia a felhőben, hogy használni lehessen a File Storage szolgáltatással.
> 
> 

### <a name="add-using-directives"></a>Hozzáadás irányelvekkel
Nyissa meg a `Program.cs` fájlt a Solution Explorer (Megoldáskezelő) felületén, és irányelvekkel adja hozzá az alábbiakat a fájl elejéhez.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.WindowsAzure.Storage; // Namespace for Storage Client Library
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage
using Microsoft.WindowsAzure.Storage.File; // Namespace for File storage
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="access-the-file-share-programmatically"></a>A fájlmegosztás szoftveres elérése
Adja hozzá az alábbi kódot a `Main()` módszerhez (a fent látható kód után) a kapcsolati karakterlánc lekéréséhez. Ez a kód lekér egy hivatkozást a korábban létrehozott fájlra, és eredményül megjeleníti annak tartalmát a konzolablakban.

```csharp
// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

A kimenet megtekintéséhez futtassa a konzolalkalmazást.

### <a name="set-the-maximum-size-for-a-file-share"></a>Egy fájlmegosztás maximális méretének beállítása
Az Azure Storage ügyféloldali kódtár 5.x-es verziójától kezdve megadhat gigabájtban kifejezve egy kvótát (vagy maximális méretet) egy fájlmegosztáshoz. Azt is ellenőrizheti, hogy aktuálisan mennyi adatot tárol a fájlmegosztás.

Ha beállít egy kvótát egy megosztáshoz, korlátozhatja a megosztáson tárolt fájlok összesített méretét. Ha a megosztásban található fájlok teljes mérete meghaladja a megosztáshoz beállított kvótát, az ügyfelek nem növelhetik tovább a meglévő fájlok méretét, és csak olyan új fájlokat hozhatnak létre, amelyek üresek.

Az alábbi példa bemutatja, hogyan ellenőrizheti egy megosztás aktuális kihasználását, és hogyan adhat meg hozzá kvótát.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.WindowsAzure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Közös hozzáférésű jogosultságkód létrehozása egy fájlhoz vagy fájlmegosztáshoz
Az Azure Storage ügyféloldali kódtár 5.x-es verziójától kezdve létrehozhat egy közös hozzáférésű jogosultságkódot (shared access signature, SAS) egy fájlmegosztáshoz vagy fájlhoz. Létrehozhat egy megosztott elérési házirendet is egy fájlmegosztáshoz, hogy kezelni tudja a közös hozzáférésű jogosultságkódokat. Azért érdemes létrehozni megosztott elérési házirendet, mert annak az eszközeivel vissza lehet hívni az SAS-t, amennyiben sérülne a biztonsága.

Az alábbi példa létrehoz egy megosztott elérési házirendet egy megosztáson, majd felhasználja a házirendet egy, a megosztásban található fájlhoz tartozó SAS korlátozására.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authenticated via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

További információk a közös hozzáférésű jogosultságkód létrehozásáról és használatáról: [Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) (Közös hozzáférésű jogosultságkódok (SAS)) és [Create and use a SAS with Blob storage](storage-dotnet-shared-access-signature-part-2.md) (SAS létrehozása a Blob Storage segítségével).

### <a name="copy-files"></a>Fájlok másolása
Az Azure Storage ügyféloldali kódtár 5.x-es verziójától kezdve másolhat egy fájlt egy másik fájlba, egy fájlt egy blobba vagy egy blobot egy fájlba. A következő szakaszokban bemutatjuk, hogyan hajthatók végre szoftveresen ezek a másolási műveletek.

Az AzCopy segítségével is átmásolhat egy fájlt egy másikba, egy blobot egy fájlba vagy fordítva. Lásd: [Transfer data with the AzCopy Command-Line Utility](storage-use-azcopy.md) (Adatátvitel az AzCopy parancssori segédprogrammal).

> [!NOTE]
> Ha egy blobot másol egy fájlba vagy egy fájlt egy blobba, akkor is a közös hozzáférésű jogosultságkóddal (SAS) kell hitelesítenie a forrásobjektumot, ha egy tárfiókon belül történik a másolás.
> 
> 

**Fájl másolása másik fájlba**

Az alábbi példa megosztáson belül másol át egy fájlt egy másikba. Mivel ez a másolási művelet tárfiókon belül másolja a fájlokat, a másoláshoz a megosztott kulcsos hitelesítést is használhatja.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

**Fájl másolása blobba**

Az alábbi példa létrehoz egy fájlt, és átmásolja egy ugyanabban a tárfiókban található blobba. A példa létrehoz egy SAS-t a forrásfájlhoz, amelynek a segítségével a szolgáltatás hitelesíti a forrásfájl hozzáférését a másolási művelet alatt.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to File storage.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authenticate access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Ugyanígy másolhat blobokat fájlokba. Ha a forrásobjektum egy blob, hozzon létre egy SAS-t, amely hitelesíti a blobhoz való hozzáférést a másolási művelet során.

## <a name="troubleshooting-file-storage-using-metrics"></a>A File Storage hibaelhárítása mérőszámok segítségével
Az Azure Storage Analytics mostantól a File Storage esetén is támogatja a mérőszámok használatát. A metrikai adatok segítségével nyomon követheti a kéréseket, és diagnosztizálhatja a problémákat.

A File Storage mérőszámait az [Azure Portalon](https://portal.azure.com) engedélyezheti. A mérőszámokat szoftveresen is lehet engedélyezni. Ehhez hívja meg a Set File Service Properties (Fájl szolgáltatástulajdonságainak beállítása) műveletet a REST API vagy valamelyik, a Storage ügyféloldali kódtárában megtalálható megfelelője segítségével.

Az alábbi példakód bemutatja, hogyan használható a Storage .NET-hez készült ügyféloldali kódtára arra, hogy engedélyezze a mérőszámok használatát a File Storage-hoz.

Először adja hozzá a következő `using` irányelveket a `Program.cs` fájlhoz a fentiekben hozzáadottak kiegészítéseként:

```csharp
using Microsoft.WindowsAzure.Storage.File.Protocol;
using Microsoft.WindowsAzure.Storage.Shared.Protocol;
```

Ne feledje, hogy amíg a Blob, Table és Queue Storage a megosztott `ServiceProperties` típust használja a `Microsoft.WindowsAzure.Storage.Shared.Protocol` névtérben, a File Storage a saját típusát, a `FileServiceProperties` típust használja a `Microsoft.WindowsAzure.Storage.File.Protocol` névtérben. Mindkét névtérre hivatkozni kell a kódban, hogy az alábbi kód fordítható legyen.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.WindowsAzure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Emellett olvassa el az [Azure-fájlok hibaelhárításáról szóló cikk](storage-troubleshoot-file-connection-problems.md) átfogó hibaelhárítási útmutatóját. 

## <a name="file-storage-faq"></a>A File Storage-ra vonatkozó gyakran ismételt kérdések
1. **Támogatja a File Storage az Active Directory-alapú hitelesítést?**
   
    Jelenleg nem támogatjuk az AD-alapú hitelesítést vagy az ACL-eket, de a támogatás már szerepel a funkciókra vonatkozó kérések listáján. Egyelőre az Azure Storage-fiókkulcsok segítségével hitelesíthető a fájlmegosztás. Létezik egy megkerülő megoldás is, amely közös hozzáférésű jogosultságkódokat (SAS) használt a REST API vagy a klienskódtárak segítségével. Az SAS segítségével létrehozhat különleges engedélyekkel rendelkező jogkivonatokat, amelyek egy adott időintervallumban érvényesek. Létrehozhat például egy jogkivonatot, amelynek csak olvasási hozzáférése van egy adott fájlhoz. Aki birtokolja ezt a jogkivonatot a megadott érvényességi idő alatt, olvasási hozzáférést kap a fájlhoz.
   
    Az SAS csak REST API vagy klienskódtárak használatával támogatott. Amikor SMB-protokollal csatlakoztatja a fájlmegosztást, nem használhatja az SAS-t a tartalmához való hozzáférés delegálására. 

2. **Hogyan biztosíthatok hozzáférést egy bizonyos fájlhoz egy webböngészőben?**
   Az SAS segítségével létrehozhat különleges engedélyekkel rendelkező jogkivonatokat, amelyek egy adott időintervallumban érvényesek. Létrehozhat például egy jogkivonatot, amely csak olvasási hozzáféréssel rendelkezik egy bizonyos fájlhoz egy megadott ideig. Amíg ez érvényes, addig bárki, aki ismeri ezt az URL-címet, közvetlenül letöltheti a fájlt bármilyen webböngészőből. Az SAS-kulcsok egyszerűen létrehozhatóak például olyan felhasználói felületekkel, mint a Storage Explorer.

3.   **Milyen módszerekkel lehet hozzáférni az Azure File Storage-ban tárolt fájlokhoz?**
    Csatlakoztathatja a fájlmegosztást a helyi gépen az SMB 3.0 protokoll használatával, vagy a [Storage Explorer](http://storageexplorer.com/), a Cloudberry vagy hasonló eszköz használatával is hozzáférhet a fájlmegosztásban található fájlokhoz. Az alkalmazásból használhatja az ügyfélkódtárakat, a REST API-kat vagy a PowerShellt az Azure-fájlmegosztásban található fájlok eléréséhez.
    
4.   **Hogyan csatlakoztathatom a helyi gépemen az Azure-fájlmegosztást?** A fájlmegosztást az SMB protokoll segítségével csatlakoztathatja, ha a 445-ös port (TCP, kimenő) meg van nyitva, és az ügyfél támogatja az SMB 3.0 protokollt (*például* a Windows 8 vagy a Windows Server 2012). A port zárolásának feloldásához lépjen kapcsolatba a helyi internetszolgáltatóval. A fájlok ideiglenesen a Storage Explorer, illetve más, külső gyártótól származó platform (például a Cloudberry) használatával tekinthetők meg.

5. **Külső, az előfizetésen felüli díjfizetéssel járó sávszélességnek számít az Azure virtuális gép és egy fájlmegosztás közötti hálózati forgalom?**
   
    Ha a fájlmegosztás és a virtuális gép eltérő régióban van, a köztük jelentkező adatforgalom külső sávszélességnek minősül, és díjkötelesnek számít.
6. **Az azonos régióban található virtuális gép és fájlmegosztás közötti hálózati forgalom ingyenes?**
   
    Igen. Az adatforgalom ingyenes, ha régión belül marad.
7. **A helyszíni virtuális gépek csatlakozása az Azure File Storage-hoz az Azure ExpressRoute-tól függ?**
   
    Nem. Akkor is hozzáférhet a fájlmegosztáshoz a helyszíni virtuális gépekről, ha nem rendelkezik ExpressRoute-tal, feltéve, hogy tud a 445-ös (TCP, kimenő) porton keresztül csatlakozni az internethez. Ha szeretné, természetesen az ExpressRoute-ot is használhatja a File Storage szolgáltatással.
8. **Egy feladatátvevő fürt „Tanúsító fájlmegosztása” lehet az Azure File Storage egyik használati esete?**
   
    Nem. Ez a használati mód jelenleg nem támogatott.
9. **A File Storage jelenleg csak LRS-en vagy GRS-en keresztül replikálódik, igaz?**  
   
    Az RA-GRS támogatása szerepel a terveink között, de a bevezetés időpontja még nem ismert.
10. **Mikor használhatok már létező tárfiókokat az Azure File Storage-hoz?**
   
    Az Azure File Storage mostantól minden tárfiókhoz engedélyezett.
11. **A REST API kiegészül átnevezési művelettel?**
   
    Az átnevezés még nem támogatott a REST API-n.
12. **Létrehozható beágyazott megosztás, azaz megosztás egy megosztásban?**
    
    Nem. A fájlmegosztás a virtuális meghajtó, amelyet csatlakoztathat, így nem támogatott a beágyazott megosztás.
13. **Lehetséges csak olvasási vagy csak írási engedélyeket adni a megosztáson belüli mappákhoz?**
    
    Ha a fájlmegosztást SMB-protokollal csatlakoztatja, nem kezelheti ilyen szinten a fájlmegosztást. Az viszont megoldás lehet, ha létrehoz közös hozzáférésű jogosultságkódokat (SAS) a REST API-n vagy a klienskódtáron keresztül.  
14. **A teljesítmény nem volt kielégítő, amikor fájlokat próbáltam kicsomagolni a File Storage-ban. Mit tegyek?**
    
    Ha nagyszámú fájlt kell átvinnie a File Storage-ba, javasoljuk, hogy használja az AzCopy, az Azure PowerShell (Windows) vagy az Azure CLI (Linux/Unix) eszközt, mert ezek hálózati adatátvitelre lettek optimalizálva.
15. **Az Azure Files teljesítményével kapcsolatos problémák megoldásához kiadott javítás**
    
    A Windows csapata nemrégiben megjelentetett egy javítást a teljesítménnyel kapcsolatos problémák kezelésére, amelyek akkor jelentkeznek, amikor az ügyfél Windows 8.1 vagy Windows Server 2012 R2 rendszerről fér hozzá az Azure Files Storage-hoz. További információt a következő KB-cikkben talál: [Slow performance when you access Azure Files Storage from Windows 8.1 or Server 2012 R2](https://support.microsoft.com/kb/3114025) (Lassú teljesítmény az Azure Files Storage használatakor Windows 8.1 vagy Server 2012 R2 rendszerről).
16. **Az Azure File Storage használata az IBM MQ-val**
    
    Az IBM kiadott egy útmutató dokumentumot az IBM MQ ügyfelei számára, amely segítséget nyújt az Azure File Storage konfigurálásához az általuk használt szolgáltatással. További információk: [How to setup IBM MQ Multi instance queue manager with Microsoft Azure File Service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service) (Az IBM MQ többpéldányos üzenetsor-kezelőjének beállítása a Microsoft Azure File szolgáltatással).
17. **Azure-fájlok tárolási hibáinak elhárítása**
    
    Olvassa el az [Azure fájlok hibaelhárításáról szóló cikk](storage-troubleshoot-file-connection-problems.md) átfogó hibaelhárítási útmutatóját.               

18. **Hogyan lehet engedélyezni a kiszolgálóoldali titkosítást az Azure Files-hoz?**

    Az Azure Files [kiszolgálóoldali titkosítása](storage-service-encryption.md) jelenleg előzetes verzióban működik. Az előzetes verzióban csak az [Azure Portalon](https://portal.azure.com) létrehozott Azure Resource Manager-tárfiókokon engedélyezheti ezt a funkciót. A szolgáltatás díjmentesen engedélyezhető. Ha engedélyezi az Azure File Storage-hoz a Storage szolgáltatás titkosítását, az adatai automatikusan titkosítva lesznek. 
    
    A jövőben az [Azure PowerShell-lel](/powershell/resourcemanager/azurerm.storage/v2.7.0/azurerm.storage), az [Azure CLI-vel](storage-azure-cli.md) és az [Azure Storage erőforrás-szolgáltató REST API-jával](/rest/api/storagerp/storageaccounts) tervezzük támogatni a fájltárolás titkosításának engedélyezését. 
    A [Storage szolgáltatás titkosításával](storage-service-encryption.md) kapcsolatos témakörben talál további információt az inaktív állapotban végzett titkosításról az Azure Storage-ban, és ha kérdése van az előzetes verzióról, írjon a következő címre: ssediscussions@microsoft.com.

## <a name="next-steps"></a>Következő lépések
Az alábbi hivatkozások további információkat tartalmaznak az Azure File Storage-ról.

### <a name="conceptual-articles-and-videos"></a>Elméleti cikkek és videók
* [Azure Files Storage: a frictionless cloud SMB file system for Windows and Linux (Azure Files Storage: zökkenőmentes felhőalapú SMB fájlrendszer Windows és Linux rendszerekhez)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [How to use Azure File Storage with Linux (Az Azure File Storage használata Linuxszal)](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage-eszköztámogatás
* [Using Azure PowerShell with Azure Storage (Az Azure PowerShell és az Azure Storage együttes használata)](storage-powershell-guide-full.md)
* [How to use AzCopy with Microsoft Azure Storage (Az AzCopy használata a Microsoft Azure Storage szolgáltatással)](storage-use-azcopy.md)
* [Using the Azure CLI with Azure Storage (Az Azure parancssori felülete és az Azure Storage együttes használata)](storage-azure-cli.md#create-and-manage-file-shares)
* [Azure File Storage-problémák hibaelhárítása](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referencia
* [Az Azure Storage .NET-hez készült ügyféloldali kódtára – referencia](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Referencia a fájlszolgáltatás REST API-jához](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>Blogbejegyzések
* [Azure File storage is now generally available (Mostantól általánosan elérhető az Azure File Storage)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage (Az Azure File Storage ismertetése)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introducing Microsoft Azure File Service (A Microsoft Azure File szolgáltatás bemutatása)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persisting connections to Microsoft Azure Files (A Microsoft Azure Files-kapcsolatok megőrzése)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)

