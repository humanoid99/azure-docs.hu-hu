---
title: "A Java SDK használata Azure Data Lake Store-alkalmazások fejlesztéséhez | Microsoft Docs"
description: "Data Lake Store-fiók létrehozása és alapszintű műveletek végrehajtása a Data Lake Store-ban az Azure Data Lake Store Java SDK használatával"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 3e4302e7c5b54a46994eccaa8cf9ecbb0a84446d
ms.lasthandoff: 03/03/2017


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>Az Azure Data Lake Store használatának első lépései a Java használatával
> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

A cikkből megtudhatja, hogyan végezhet el olyan alapvető műveleteket az Azure Data Lake Store Java SDK segítségével, mint például mappák létrehozása vagy adatfájlok le- és feltöltése. További információk a Data Lake-ről: [Azure Data Lake Store](data-lake-store-overview.md).

Az Azure Data Lake Store Java SDK API-dokumentációja [Az Azure Data Lake Store Java API-dokumentációja](https://azure.github.io/azure-data-lake-store-java/javadoc/) című témakörben található.

## <a name="prerequisites"></a>Előfeltételek
* Java-fejlesztőkészlet (JDK 7 vagy újabb, Java 1.7 vagy újabb verzió használatával)
* Azure Data Lake Store-fiók. Kövesse [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md) című témakör utasításait.
* [Maven](https://maven.apache.org/install.html). Ez az oktatóanyag a Mavent használja a build- és projektfüggőségek kezeléséhez. Bár lehetséges olyan rendszerek nélkül fejleszteni, mint például a Maven vagy a Gradle, ezekkel a fejlesztőrendszerekkel sokkal egyszerűbb a függőségek kezelése.
* (Nem kötelező) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) vagy hasonló integrált fejlesztőkörnyezet.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hogyan végezhető el a hitelesítés az Azure Active Directory használatával?
Ebben az oktatóanyagban az Azure AD-alkalmazás titkos ügyfélkulcsát használjuk az Azure Active Directory-jogkivonat beolvasásához (szolgáltatások közötti hitelesítés). A jogkivonat használatával hozzuk létre a fájl- és könyvtárműveletek végrehajtására szolgáló Data Lake Store-ügyfélobjektumot. Az Azure Data Lake Store-hitelesítés titkos ügyfélkulccsal való elvégzésének információiért az alábbi magas szintű lépéseket hajtjuk végre:

1. Azure AD-webalkalmazás létrehozása
2. Szerezze be az Azure AD-webalkalmazáshoz tartozó ügyfélazonosítót, a titkos ügyfélkulcsot és a jogkivonat végpontját.
3. Végezze el az Azure AD-webalkalmazás hozzáférésének konfigurálását abban a Data Lake Store-fájlban vagy mappában, amelyet a létrehozandó Java-alkalmazásból szeretne elérni.

A lépések végrehajtásával kapcsolatos útmutatóért tekintse meg az [Active Directory-alkalmazás létrehozása](data-lake-store-authenticate-using-active-directory.md) című témakört.

Az Azure Active Directory egyéb lehetőségeket is kínál a jogkivonat beszerzésére. Számos különböző hitelesítési mechanizmus közül választhat az adott forgatókönyvnek megfelelően, például böngészőben futó alkalmazás, asztali alkalmazásként terjesztett alkalmazás, illetve helyszínen vagy Azure-beli virtuális gépen futó kiszolgálói alkalmazás. Továbbá különböző típusú hitelesítő adatok közül választhat, például jelszavak, tanúsítványok, kétfaktoros hitelesítés, stb. Ezen túlmenően az Azure Active Directory lehetővé teszi a helyszíni Active Directory-felhasználók felhővel való szinkronizálását is. További információt a [Hitelesítési eljárások az Azure Active Directoryhoz](../active-directory/active-directory-authentication-scenarios.md) című témakörben talál. 

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
A [GitHubon](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) elérhető kódminta végigvezeti a fájlok tárolóban való létrehozásának, a fájlok összetűzésének, a fájlok letöltésének és az egyes fájlok tárolóból való törlésének folyamatán. A cikk ezen szakasza a kód fő részeit mutatja be.

1. Hozzon létre egy Maven-projektet az [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) használatával parancssorból vagy az integrált fejlesztőkörnyezetből. A Java-projektek IntelliJ használatával való létrehozási útmutatójáért [kattintson ide](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). A projektek Eclipse használatával való létrehozási útmutatójáért [kattintson ide](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 
2. Illessze be a következő függőségeket a Maven **pom.xml** nevű fájljába. Illessze be a következő szövegrészletet a **\</version>** és a **\</project>** címkék közé:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.4</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    Az első függőség a Data Lake Store SDK (`azure-data-lake-store-sdk`) használata a Maven tárházból. A második függőség (`slf4j-nop`) az alkalmazással használandó naplózási keretrendszer meghatározása. A Data Lake Store SDK az [slf4j](http://www.slf4j.org/) naplózási megoldást használja, amellyel számos elterjedt naplózási keretrendszer közül választhat (például log4j, Java-naplózás, Logback vagy nincs naplózás). Ebben a példában kikapcsoljuk a naplózást, mivel az **Slf4j Nop Binding** nevű eszközt használjuk. Az alkalmazásban való egyéb naplózási lehetőségek használatáról [itt talál információt](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Az alkalmazáskód hozzáadása
A kód három fő részből áll.

1. Az Azure Active Directory-jogkivonat beszerzése
2. A jogkivonat használatával hozhat létre egy Data Lake Store-ügyfelet.
3. Használja a Data Lake Store-ügyfelet a műveletek végrehajtásához.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>1. lépés: Az Azure Active Directory-jogkivonat beszerzése.
A Data Lake Store SDK kényelmes megoldásaival kezelheti a Data Lake Store-fiókkal való kommunikációhoz szükséges biztonsági jogkivonatokat. Azonban az SDK nem írja elő, hogy kizárólag ezek a módszerek használhatók. A jogkivonat beszerzésére más eszközöket is használhat, például az [Active Directory SDK-t](https://github.com/AzureAD/azure-activedirectory-library-for-java) , vagy saját egyéni kódot.

A Data Lake Store SDK és az `ClientCredsTokenProvider` egyik alosztályának (az alábbi példa a `AccessTokenProvider` osztályt használja) statikus metódusainak használatával szerezheti be a korábban létrehozott Active Directory-webalkalmazás jogkivonatát. A jogkivonat-szolgáltató a memóriában gyorsítótárazza a jogkivonat beszerzéséhez használt hitelesítő adatokat, és automatikusan megújítja azt, ha közeleg a lejárati ideje. Létrehozhatja az `AccessTokenProvider` saját alosztályait is, így a jogkivonatokat beszerezheti az ügyfélkódja segítségével, de egyelőre az SDK-ban biztosított alosztályt használjuk.

Cserélje ki a **FILL-IN-HERE** értéket az Azure Active Directory-webalkalmazáshoz tartozó tényleges értékkel.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>2. lépés: Az Azure Data Lake Store-ügyfél (ADLStoreClient) objektumának létrehozása
Az [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) objektum létrehozásakor meg kell adnia a Data Lake Store-fiók nevét és az előző lépésben létrehozott jogkivonat-szolgáltatót. Vegye figyelembe, hogy a Data Lake Store-fióknév csak teljes tartománynév lehet. A **FILL-IN-HERE** értéket például a következő tartománynévre cserélheti ki: **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>3. lépés: Az ADLStoreClient használata fájl- és könyvtárműveletek végrehajtására
Az alábbi példakód néhány gyakori művelet kódrészletét tartalmazza. Az egyéb műveleteket az **ADLStoreClient** objektumhoz tartozó [Data Lake Store Java SDK teljes API-dokumentációjában](https://azure.github.io/azure-data-lake-store-java/javadoc/) tekintheti meg.

Vegye figyelmembe, hogy a fájlok olvasása és írása szabványos Java-adatfolyamok használatával történik. Ez azt jelenti, hogy bármilyen Java-adatfolyamot rétegként helyezhet el az Data Lake Store-adatfolyam felett, így kihasználhatja a szabványos Java-funkciókat (például adatfolyamok megjelenítése formázott kimenő adatként, valamint a további funkcionalitás érdekében tömörítési vagy titkosítási adatfolyamok, stb.).

     // create file and write some content
     String filename = "/a/b/c.txt";
     OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
     PrintStream out = new PrintStream(stream);
     for (int i = 1; i <= 10; i++) {
         out.println("This is line #" + i);
         out.format("This is the same line (%d), but using formatted output. %n", i);
     }
     out.close();
    
    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>4. lépés: Az alkalmazás fordítása és futtatása
1. Az integrált fejlesztőkörnyezetben történő futtatáshoz keresse meg a **Futtatás** gombot, és kattintson rá. A Mavenben történő futtatáshoz használja az [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html) beépülő modult.
2. Parancssorból futtatható, különálló jar-fájlt az összes függőség és a [Maven Assembly Plugin](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html) használatával hozhat létre. A [GitHubon található mintaforráskód](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) pom.xml fájlja egy példát tartalmaz a fenti műveletre.

## <a name="next-steps"></a>Következő lépések
* [A Java SDK JavaDoc-dokumentációjának áttekintése](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)


