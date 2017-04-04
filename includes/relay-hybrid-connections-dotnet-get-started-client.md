### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása
* Indítsa el a Visual Studiót, majd hozzon létre egy új konzolalkalmazást.

### <a name="add-the-relay-nuget-package"></a>A Relay NuGet-csomag hozzáadása
1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Kattintson a **Browse** (Tallózás) fülre, keressen a „Microsoft.Azure.Relay” kifejezésre, majd válassza ki a **Microsoft Azure Relay** elemet. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.

### <a name="write-some-code-to-send-messages"></a>Írjon egy kódrészletet üzenetek küldéséhez
1. A Program.cs fájl elején cserélje le a meglévő `using` utasításokat az alábbi utasításokra:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    ```
2. Adjon állandókat a `Program` osztályhoz a Hibrid kapcsolat részleteivel. Cserélje le a zárójelben lévő helyőrzőket a hibrid gyűjtemény létrehozásakor beszerzett megfelelő értékekre. Ügyeljen arra, hogy a teljes névtérnevet használja:
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. Adja hozzá a következő új metódust a `Program` osztályhoz:
   
    ```csharp
    private static async Task RunAsync()
    {
        Console.WriteLine("Enter lines of text to send to the server with ENTER");
   
        // Create a new hybrid connection client
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var client = new HybridConnectionClient(new Uri(String.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Initiate the connection
        var relayConnection = await client.CreateConnectionAsync();
   
        // We run two concurrent loops on the connection. One 
        // reads input from the console and writes it to the connection 
        // with a stream writer. The other reads lines of input from the 
        // connection with a stream reader and writes them to the console. 
        // Entering a blank line will shut down the write task after 
        // sending it to the server. The server will then cleanly shut down
        // the connection which will terminate the read task.
   
        var reads = Task.Run(async () => {
            // Initialize the stream reader over the connection
            var reader = new StreamReader(relayConnection);
            var writer = Console.Out;
            do
            {
                // Read a full line of UTF-8 text up to newline
                string line = await reader.ReadLineAsync();
                // if the string is empty or null, we are done.
                if (String.IsNullOrEmpty(line))
                    break;
                // Write to the console
                await writer.WriteLineAsync(line);
            }
            while (true);
        });
   
        // Read from the console and write to the hybrid connection
        var writes = Task.Run(async () => {
            var reader = Console.In;
            var writer = new StreamWriter(relayConnection) { AutoFlush = true };
            do
            {
                // Read a line form the console
                string line = await reader.ReadLineAsync();
                // Write the line out, also when it's empty
                await writer.WriteLineAsync(line);
                // Quit when the line was empty
                if (String.IsNullOrEmpty(line))
                    break;
            }
            while (true);
        });
   
        // Wait for both tasks to complete
        await Task.WhenAll(reads, writes);
        await relayConnection.CloseAsync(CancellationToken.None);
    }
    ```
4. Adja hozzá a következő kódsort a `Main` metódushoz a `Program` osztályban.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    A Program.cs fájlnak így kell kinéznie.
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
                Console.WriteLine("Enter lines of text to send to the server with ENTER");
   
                // Create a new hybrid connection client
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var client = new HybridConnectionClient(new Uri(String.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
                // Initiate the connection
                var relayConnection = await client.CreateConnectionAsync();
   
                // We run two conucrrent loops on the connection. One 
                // reads input from the console and writes it to the connection 
                // with a stream writer. The other reads lines of input from the 
                // connection with a stream reader and writes them to the console. 
                // Entering a blank line will shut down the write task after 
                // sending it to the server. The server will then cleanly shut down
                // the connection which will terminate the read task.
   
                var reads = Task.Run(async () => {
                    // Initialize the stream reader over the connection
                    var reader = new StreamReader(relayConnection);
                    var writer = Console.Out;
                    do
                    {
                        // Read a full line of UTF-8 text up to newline
                        string line = await reader.ReadLineAsync();
                        // If the string is empty or null, we are done.
                        if (String.IsNullOrEmpty(line))
                            break;
                        // Write to the console
                        await writer.WriteLineAsync(line);
                    }
                    while (true);
                });
   
                // Read from the console and write to the hybrid connection
                var writes = Task.Run(async () => {
                    var reader = Console.In;
                    var writer = new StreamWriter(relayConnection) { AutoFlush = true };
                    do
                    {
                        // Read a line form the console
                        string line = await reader.ReadLineAsync();
                        // Write the line out, also when it's empty
                        await writer.WriteLineAsync(line);
                        // Quit when the line was empty
                        if (String.IsNullOrEmpty(line))
                            break;
                    }
                    while (true);
                });
   
                // Wait for both tasks to complete
                await Task.WhenAll(reads, writes);
                await relayConnection.CloseAsync(CancellationToken.None);
            }
        }
    }
    ```

