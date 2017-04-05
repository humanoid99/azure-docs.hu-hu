# Áttekintés

## [Bevezetés](storage-introduction.md)

# Első lépések

## [Első lépések](storage-getting-started-guide.md)
## [Tárfiók létrehozása](storage-create-storage-account.md)

## Blob Storage
### [.NET](storage-dotnet-how-to-use-blobs.md)
### [Java](storage-java-how-to-use-blob-storage.md)
### [Node.js](storage-nodejs-how-to-use-blob-storage.md)
### [C++](storage-c-plus-plus-how-to-use-blobs.md)
### [Python](storage-python-how-to-use-blob-storage.md)
### [PHP](storage-php-how-to-use-blobs.md)
### [Ruby](storage-ruby-how-to-use-blob-storage.md)
### [iOS](storage-ios-how-to-use-blob-storage.md)
### [Xamarin](storage-xamarin-blob-storage.md)

## Queue Storage
### [.NET](storage-dotnet-how-to-use-queues.md)
### [Java](storage-java-how-to-use-queue-storage.md)
### [Node.js](storage-nodejs-how-to-use-queues.md)
### [C++](storage-c-plus-plus-how-to-use-queues.md)
### [Python](storage-python-how-to-use-queue-storage.md)
### [PHP](storage-php-how-to-use-queues.md)
### [Ruby](storage-ruby-how-to-use-queue-storage.md)

## Table Storage
### [.NET](storage-dotnet-how-to-use-tables.md)
### [Java](storage-java-how-to-use-table-storage.md)
### [Node.js](storage-nodejs-how-to-use-table-storage.md)
### [C++](storage-c-plus-plus-how-to-use-tables.md)
### [Python](storage-python-how-to-use-table-storage.md)
### [PHP](storage-php-how-to-use-table-storage.md)
### [Ruby](storage-ruby-how-to-use-table-storage.md)

## File Storage
### [Windows, .NET, PowerShell](storage-dotnet-how-to-use-files.md)
### [Linux](storage-how-to-use-files-linux.md)
### [Java](storage-java-how-to-use-file-storage.md)
### [C++](storage-c-plus-plus-how-to-use-files.md)
### [Python](storage-python-how-to-use-file-storage.md)

## Lemezes tárolás 
### [Virtuális gép létrehozása a Resource Manager és a PowerShell használatával](../virtual-machines/virtual-machines-windows-ps-create.md)
### [Linux-alapú virtuális gép létrehozása az Azure CLI 2.0-s verziójával](../virtual-machines/linux/quick-create-cli.md)
### [Felügyelt lemez csatolása Windows rendszerű virtuális géphez PowerShell használatával](../virtual-machines/windows/attach-disk-ps.md)
### [Felügyelt lemez hozzáadása Linux rendszerű virtuális géphez](../virtual-machines/linux/add-disk.md)
### [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Windows alatt](../virtual-machines/windows/snapshot-copy-managed-disk.md)
### [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Linux alatt](../virtual-machines/linux/snapshot-copy-managed-disk.md)

# Útmutató
## [Tárfiók létrehozása](storage-create-storage-account.md)
## Blobok használata
### [Szolgáltatás áttekintése](https://msdn.microsoft.com/library/dd179376.aspx)
### [Gyakran és ritkán használt adatok rétege](storage-blob-storage-tiers.md)
### [Egyéni tartományok](storage-custom-domain-name.md)
### [Névtelen hozzáférés a blobokhoz](storage-manage-access-to-resources.md)
### [Példák](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob)
## Üzenetsorok használata
### [Alapelvek](https://msdn.microsoft.com/library/dd179353.aspx)
### [Példák](https://azure.microsoft.com/documentation/samples/?service=storage&term=queue)
## Táblák használata
### [Áttekintés](https://msdn.microsoft.com/library/dd179463.aspx)
### [Útmutató a táblatervezéshez](storage-table-design-guide.md)
### [Példák](https://azure.microsoft.com/documentation/samples/?service=storage&term=table)
## Fájlok használata
### [Áttekintés](/rest/api/storageservices/fileservices/File-Service-Concepts)
### [Azure-fájlok hibaelhárítása](storage-troubleshoot-file-connection-problems.md)
### [Példák](https://azure.microsoft.com/documentation/samples/?service=storage&term=file)
## Lemezek használata
### [Lemezek és virtuális merevlemezek Windows rendszerű virtuális gépekhez](storage-about-disks-and-vhds-windows.md)
### [Lemezek és virtuális merevlemezek Linux rendszerű virtuális gépekhez](storage-about-disks-and-vhds-linux.md)
### [Azure Managed Disks – áttekintés](storage-managed-disks-overview.md)
### [Azure-beli virtuális gépek áttelepítése az Azure Managed Disksbe](../virtual-machines/windows/migrate-to-managed-disks.md)
### [Áttelepítés AWS-ről és egyéb platformokról a Managed Disksbe](../virtual-machines/windows/on-prem-to-azure.md)
### [Gyakori kérdések az Azure IaaS virtuálisgép-lemezekről](storage-faq-for-disks.md)
### Prémium szintű Storage
#### [Nagy teljesítményű Premium Storage szolgáltatás virtuálisgép-lemezekhez](storage-premium-storage.md)
#### [Nagy teljesítményű rendszer tervezése](storage-premium-storage-performance.md)
### Standard szintű Storage
#### [Költséghatékony Standard Storage, valamint nem felügyelt és felügyelt virtuálisgép-lemezek](storage-standard-storage.md)
### Nem felügyelt lemezek használata
#### [Áttelepítés a Premium Storage-ba](storage-migration-to-premium-storage.md)
#### [Nem felügyelt virtuálisgép-lemezek biztonsági mentése növekményes pillanatképekkel](storage-incremental-snapshots.md)
## Tervezés és kialakítás
### [Replikáció](storage-redundancy.md)
### [Méretezhetőségi és teljesítménycélok](storage-scalability-targets.md)
### [Teljesítmény és méretezhetőség – ellenőrzőlista](storage-performance-checklist.md)
### [Párhuzamosság](storage-concurrency.md)
## Fejlesztés
### Példák
#### [.NET](storage-samples-dotnet.md)
#### [Java](storage-samples-java.md)
### [HA-alkalmazások tervezése RA-GRS használatával](storage-designing-ha-apps-with-ragrs.md)
### [Kapcsolati karakterláncok konfigurálása](storage-configure-connection-string.md)
### [A Storage Emulator használata](storage-use-emulator.md)
### [Tulajdonságok és metaadatok beállítása és lekérése](storage-properties-metadata.md)
## Kezelés
### [PowerShell](storage-powershell-guide-full.md)
### [Azure CLI 2.0](storage-azure-cli.md)
### [Azure CLI 1.0](storage-azure-cli-nodejs.md)
### [Azure Automation](automation-manage-storage.md)
## Biztonságos
### [Biztonsági útmutató](storage-security-guide.md)
### [Inaktív adatok titkosítása](storage-service-encryption.md)
### [Megosztott kulcsos hitelesítés](https://msdn.microsoft.com/library/dd179428.aspx)
### [Közös hozzáférésű jogosultságkódok (SAS)](storage-dotnet-shared-access-signature-part-1.md)
### [Oktatóanyag: Blobok titkosítása és visszafejtése az Azure Key Vaulttal](storage-encrypt-decrypt-blobs-key-vault.md)
### Ügyféloldali titkosítás
#### [.NET](storage-client-side-encryption.md)
#### [Java](storage-client-side-encryption-java.md)
#### [Python](storage-client-side-encryption-python.md)
## Figyelés és hibaelhárítás
### Metrikák és naplózás
#### [Storage Analytics](storage-analytics.md)
#### [Metrikák engedélyezése és megtekintése](storage-enable-and-view-metrics.md)
#### [Figyelés, diagnosztika és hibaelhárítás](storage-monitoring-diagnosing-troubleshooting.md)
#### [Oktatóanyag a hibaelhárításhoz](storage-e2e-troubleshooting.md)
### Lemeztörlési hibák elhárítása
#### [Egy Azure Resource Manager-alapú üzemelő példányon](storage-resource-manager-cannot-delete-storage-account-container-vhd.md)
#### [Egy klasszikus üzemelő példányon](storage-cannot-delete-storage-account-container-vhd.md)
### [A File Storage hibaelhárítása](storage-troubleshoot-file-connection-problems.md)
### [Vész-helyreállítási útmutató](storage-disaster-recovery-guidance.md)
## Adatátvitel
### [Adatok áthelyezése a Storage-ba és a Storage-ból](storage-moving-data.md)
### [AzCopy parancssori segédprogram](storage-use-azcopy.md)
### [Az Import/Export szolgáltatás használata](storage-import-export-service.md)
### [Az Import/Export eszköz használata](storage-import-export-tool-how-to.md)
#### [Az Import/Export eszköz telepítése](storage-import-export-tool-setup.md)
#### [Merevlemezek előkészítése importálási feladatokhoz](storage-import-export-tool-preparing-hard-drives-import.md)
##### [Tulajdonságok és metaadatok beállítása az importálási folyamat során](storage-import-export-tool-setting-properties-metadata-import.md)
##### [Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
##### [Az importálási feladatokhoz használt gyakori parancsok gyors áttekintése](storage-import-export-tool-quick-reference.md)
#### [Lemezhasználat előnézete exportálási feladatokhoz](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Importálási feladat javítása](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Exportálási feladat javítása](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [Az Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
#### [Az Import/Export szolgáltatásjegyzékének fájlformátuma](storage-import-export-file-format-manifest.md)
#### [Az Import/Export szolgáltatás metaadat- és tulajdonságfájljainak formátuma](storage-import-export-file-format-metadata-and-properties.md)
#### [Az Import/Export szolgáltatás naplófájljainak formátuma](storage-import-export-file-format-log.md)
### [Az Import/Export eszköz használata (v1)](storage-import-export-tool-how-to-v1.md)
#### [Az Import/Export eszköz telepítése](storage-import-export-tool-setup-v1.md)
#### [Merevlemezek előkészítése importálási feladatokhoz](storage-import-export-tool-preparing-hard-drives-import-v1.md)
##### [Tulajdonságok és metaadatok beállítása az importálási folyamat során](storage-import-export-tool-setting-properties-metadata-import-v1.md)
##### [Munkafolyamat-minta a merevlemezek importálási feladatokhoz való előkészítésére](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
##### [Az importálási feladatokhoz használt gyakori parancsok gyors áttekintése](storage-import-export-tool-quick-reference-v1.md)
#### [Lemezhasználat előnézete exportálási feladatokhoz](storage-import-export-tool-previewing-drive-usage-export-v1.md)
#### [Feladatok állapotának áttekintése a másolási naplófájlok segítségével](storage-import-export-tool-reviewing-job-status-v1.md)
#### [Importálási feladat javítása](storage-import-export-tool-repairing-an-import-job-v1.md)
#### [Exportálási feladat javítása](storage-import-export-tool-repairing-an-export-job-v1.md)
#### [Az Import/Export eszköz hibaelhárítása](storage-import-export-tool-troubleshooting-v1.md)
#### [Az Import/Export szolgáltatásjegyzékének fájlformátuma](storage-import-export-file-format-manifest.md)
#### [Az Import/Export szolgáltatás metaadat- és tulajdonságfájljainak formátuma](storage-import-export-file-format-metadata-and-properties.md)
#### [Az Import/Export szolgáltatás naplófájljainak formátuma](storage-import-export-file-format-log.md)
### [Az Azure Import/Export szolgáltatás REST API-jának használata](storage-import-export-using-the-rest-api.md)
#### [Importálási feladat létrehozása](storage-import-export-creating-an-import-job.md)
#### [Exportálási feladat létrehozása](storage-import-export-creating-an-export-job.md)
#### [Feladat állapotinformációinak lekérése](storage-import-export-retrieving-state-info-for-a-job.md)
#### [Feladatok számbavétele](storage-import-export-enumerating-jobs.md)
#### [Feladatok megszakítása és törlése](storage-import-export-cancelling-and-deleting-jobs.md)
#### [Meghajtójegyzékek biztonsági mentése](storage-import-export-backing-up-drive-manifests.md)
#### [Import/Export-feladatok diagnosztizálása és hibajavítása](storage-import-export-diagnostics-and-error-recovery.md)
# Referencia
## [PowerShell](/powershell/storage)
## [Azure CLI](/cli/azure/storage)
## .NET
### [Resource Manager](/dotnet/api/microsoft.azure.management.storage)
### [Adatáthelyezés](/dotnet/api/microsoft.windowsazure.storage.datamovement)
### [Blobok, üzenetsorok, táblák és fájlok](https://msdn.microsoft.com/library/azure/mt347887.aspx)
## [Java](http://azure.github.io/azure-storage-java/)
## [Node.js](http://azure.github.io/azure-storage-node)
## [Ruby](http://azure.github.io/azure-storage-ruby)
## [Python](https://azure-storage.readthedocs.io/en/latest/index.html)
## [C++](http://azure.github.io/azure-storage-cpp)
## [iOS](http://azure.github.io/azure-storage-ios/)
## [Android](http://azure.github.io/azure-storage-android)
## REST
### [Blobok, üzenetsorok, táblák és fájlok](/rest/api/storageservices/fileservices/azure-storage-services-rest-api-reference)
### [Erőforrás-szolgáltató](/rest/api/storagerp)
### [Importálás és exportálás](/rest/api/storageimportexport)

# Kapcsolódó
## Klasszikus portál
### [Tárfiók létrehozása](storage-create-storage-account-classic-portal.md)
### [Metrikák engedélyezése és megtekintése](storage-enable-and-view-metrics-classic-portal.md)
### [Figyelés, diagnosztika és hibaelhárítás](storage-monitoring-diagnosing-troubleshooting-classic-portal.md)
### [Oktatóanyag a hibaelhárításhoz](storage-e2e-troubleshooting-classic-portal.md)

# Erőforrások
## [Díjszabás](https://azure.microsoft.com/pricing/details/storage/blobs/)
## [Azure Storage-ügyféleszközök](storage-explorers.md)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage)
## [Fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=storage)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=storage)

## Azure Storage Explorer
### [Storage Explorer (előzetes verzió)](../vs-azure-tools-storage-manage-with-storage-explorer.md)
### [Blobok kezelése a Storage Explorerrel (előzetes verzió)](../vs-azure-tools-storage-explorer-blobs.md)
### [A Storage Explorer (előzetes verzió) használata az Azure File Storage szolgáltatással](../vs-azure-tools-storage-explorer-files.md)

## NuGet-csomagok
### [Az Azure Storage .NET-hez készült ügyféloldali kódtára](https://www.nuget.org/packages/WindowsAzure.Storage/)
### [Azure Storage adatátviteli könyvtár](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)
### [Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)

## Forráskód
### .NET
#### [Blob, üzenetsor, tábla és fájl](https://github.com/Azure/azure-storage-net)
#### [Adatáthelyezés](https://github.com/Azure/azure-storage-net-data-movement)
#### [Erőforrás-szolgáltató](https://github.com/Azure/azure-sdk-for-net)
### [Node.js](http://azure.github.io/azure-storage-node/)
### [Java](https://github.com/Azure/azure-storage-java)
### [C++](https://github.com/Azure/azure-storage-cpp)
### [PHP](https://github.com/Azure/azure-storage-php)
### [Ruby](https://github.com/Azure/azure-storage-ruby)
### [Python](https://github.com/Azure/azure-storage-python)
### [iOS](https://github.com/Azure/azure-storage-ios)
