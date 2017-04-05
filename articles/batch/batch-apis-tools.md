---
title: "Azure Batch API-k és eszközök használata nagyméretű párhuzamos feldolgozási megoldások kifejlesztéséhez | Microsoft Docs"
description: "Megismerheti az Azure Batch szolgáltatással történő megoldásfejlesztéshez elérhető API-kat és eszközöket."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 405cb202e2b6da0dd24e6f7b8ed55ce8c56e0fe1
ms.lasthandoff: 03/31/2017

---


# <a name="overview-of-batch-apis-and-tools"></a>A Batch API-k és eszközök áttekintése

A párhuzamos számítási feladatok Azure Batch használatával végzett feldolgozása általában programozott módon történik az egyik [Batch API-val](#batch-development-apis). Az Ön által készített ügyfélalkalmazások vagy szolgáltatások a Batch API-k használatával kommunikálhatnak a Batch szolgáltatással. A Batch API-kkal számítási csomópontok készletét, virtuális gépeket vagy felhőszolgáltatásokat hozhat létre és felügyelhet. Ezt követően pedig a feladatok és tevékenységek ütemezésével futtathatja őket ezeken a csomópontokon. 

Hatékonyan dolgozhat fel nagyméretű számítási feladatokat a szervezet számára, vagy szolgáltatási előtérrendszert nyújthat az ügyfeleknek, hogy feladatokat és tevékenységeket futtathassanak – igény szerint vagy ütemterv alapján – egyetlen, több száz vagy akár több ezer csomóponton. Az Azure Batch szolgáltatást a nagyobb munkafolyamatok részeként is felügyelheti olyan eszközökkel, mint például az [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Amikor készen áll a Batch API megismerésére, az általa nyújtott szolgáltatások alaposabb elsajátítása érdekében tekintse meg [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md) című témakört.
> 
> 

## <a name="azure-accounts-youll-need"></a>Szükséges Azure-fiókok
Batch-megoldások fejlesztésekor a következő fiókokat fogja használni a Microsoft Azure-ban.

* **Azure-fiók és -előfizetés** – Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit][msdn_benefits], vagy regisztrálhat egy [ingyenes Azure-fiókot][free_account]. Fiók létrehozásakor a rendszer egy alapértelmezett előfizetést hoz létre.
* **Batch-fiók** – Az Azure Batch-erőforrások, például a készletek, számítási csomópontok, feladatok és tevékenységek, egy Batch-fiókkal vannak társítva. Amikor az alkalmazás egy kérelmet továbbít a Batch szolgáltatás felé, a hitelesítést az Azure Batch-fiók, a fiók URL-címe és egy hozzáférési kulcs használatával hajtja végre a szolgáltatás. Az Azure Portalon [hozhat létre Batch-fiókot](batch-account-create-portal.md).
* **Storage-fiók** – A Batch beépített támogatást kínál az [Azure Storage][azure_storage] fájljainak használatához. Szinte mindegyik Batch-forgatókönyv az Azure Blob Storage-ot használja a tevékenységek által futtatott programok és feldolgozott adatok átmeneti tárolásához, valamint a tevékenységek által létrehozott kimeneti adatok tárolásához. A Storage-fiók létrehozásával kapcsolatban tekintse meg a [Tudnivalók az Azure Storage-fiókokról](../storage/storage-create-storage-account.md) című témakört.

## <a name="batch-development-apis"></a>Batch fejlesztési API-k
Az alkalmazások és szolgáltatások közvetlen REST API-hívásokat hajthatnak végre, illetve a következő ügyfélkódtárak legalább egyikének használatával futtathatják és kezelhetik az Azure Batch számítási feladatait.

| API | API-referencia | Letöltés | Oktatóanyag | Kódminták | További információ |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[MSDN][batch_rest] |N/A |- |- | [Támogatott verziók](https://docs.microsoft.com/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Oktatóanyag](batch-dotnet-get-started.md) |[GitHub][api_sample_net] | [Kibocsátási megjegyzések](https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/Client/changelog.md) |
| **Batch Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[Oktatóanyag](batch-python-tutorial.md)|[GitHub][api_sample_python] | [Olvass el](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- | [Olvass el](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** (előzetes verzió) |[github.io][api_java] |[Maven][api_java_jar] |- |[Olvass el][api_sample_java] | [Olvass el](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-command-line-tools"></a>A Batch parancssori eszközei

A fejlesztői API-k által biztosított funkciók szintén elérhetők a parancssori eszközök használatával: 

* [Batch PowerShell-parancsmagok][batch_ps]: Az [Azure PowerShell](/powershell/azureps-cmdlets-docs) modulban található Azure Batch-parancsmagokkal felügyelheti a Batch-erőforrásokat a PowerShell használatával.
* [Azure CLI](/cli/azure/overview): Az Azure parancssori felület (Azure CLI) egy többplatformos eszközkészlet, amely rendszerhéjparancsokat biztosít sok Azure-szolgáltatással, például a Batch szolgáltatással való interakcióhoz.

## <a name="batch-resource-management"></a>Batch-erőforráskezelés

A Batch Azure Resource Manager API-jai programozott hozzáférést biztosítanak a Batch-fiókokhoz. Ezen API-k használatával programozott módon kezelheti a Batch-fiókokat, a kvótákat és az alkalmazáscsomagokat.  

| API | API-referencia | Letöltés | Oktatóanyag | Kódminták |
| --- | --- | --- | --- | --- |
| **Batch Resource Manager REST** |[docs.microsoft.com][api_rest_mgmt] |N/A |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Resource Manager .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Oktatóanyag](batch-management-dotnet.md) |[GitHub][api_sample_net] |


## <a name="batch-tools"></a>Batch-eszközök
Noha nem kötelező a Batch szolgáltatással létrehozni a megoldásokat, itt megtalálható néhány hasznos eszköz a Batch-alkalmazások és -szolgáltatások létrehozásához és hibakereséséhez.

* [Azure Portal][portal]: Batch-készleteket, -feladatokat és -tevékenységeket hozhat létre, figyelhet meg és törölhet az Azure Portal Batch-paneljein. Megtekintheti ezen és más erőforrások állapotinformációit a feladatok futtatásakor, és fájlokat is letölthet a készletekben található számítási csomópontokról (letöltheti például egy sikertelen feladat `stderr.txt` fájlját hibaelhárításkor). Távoli asztali (RDP-) fájlokat is letölthet, amelyekkel bejelentkezhet a számítási csomópontokba.
* [Azure Batch Explorer][batch_explorer]: A Batch Explorer hasonló Batch-erőforráskezelő funkciókat nyújt, mint az Azure Portal, de egy különálló Windows megjelenítési alaprendszer (WPF) ügyfélalkalmazás részeként. A [GitHubon][github_samples] elérhető egyik Batch .NET mintaalkalmazás, amelyet a Visual Studio 2015-ös vagy újabb verziójával építhet ki, majd a használatával megkeresheti és felügyelheti az erőforrásokat a Batch-fiókban a Batch-megoldások fejlesztésekor és hibakeresésekor. Megtekintheti a feladatok, készletek és tevékenységek részleteit, fájlokat tölthet le a számítási csomópontokról, és távolról csatlakozhat csomópontokhoz a Batch Explorerrel letölthető távoli asztali (RDP-) fájlokkal.
* [Microsoft Azure Storage Explorer][storage_explorer]: Bár nem kimondottan Azure Batch-eszköz, a Storage Explorer egy másik értékes eszköz a Batch-megoldások fejlesztésekor és hibakeresésekor.

## <a name="next-steps"></a>Következő lépések

* Olvassa el [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md) című témakört, amely hasznos információkkal szolgál a Batch használatára készülőknek. A cikk a Batch szolgáltatás erőforrásainak, például a készleteknek, csomópontoknak, feladatoknak, tevékenységeknek és sok olyan API funkciónak a részletesebb információit tartalmazza, amelyeket a Batch-alkalmazás kiépítésekor használhat.
* [Ismerkedjen meg az Azure Batch .NET-es kódtárával](batch-dotnet-get-started.md), hogy megtudja, hogyan használhatja a C# nyelvet és a Batch .NET-es kódtárat egy egyszerű számítási feladat végrehajtásához egy általános Batch-munkafolyamattal. Ennek a cikknek az áttekintése legyen az egyik első lépés, amikor igyekszik elsajátítani a Batch használatát. Az oktatóanyagnak [Python verziója](batch-python-tutorial.md) is van.
* Töltse le a [GitHubon található kódmintákat][github_samples], hogy lássa, hogyan használható a C# és a Python a Batch eszközzel a mintául szolgáló számítási feladatok ütemezése és feldolgozása során.
* Tekintse meg a [Batch képzési tervet][learning_path], amelyben megismerheti a Batch használatának elsajátítása során igénybe vehető erőforrásokat.


[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/\rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/resourcemanager/azurerm.batch/v2.7.0/azurerm.batch
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

