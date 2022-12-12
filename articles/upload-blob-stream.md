---
title: "Blob Storage に少しずつ文字列を書き込みする"
emoji: "💬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["C#", "Azure", "Blob Storage"]
published: true
---

## Azurite エミュレーターを使用する

### 環境情報

```powershell
PS C:\zenn-content> node -v
v17.9.0
PS C:\zenn-content> npm -v
8.5.5
PS C:\zenn-content> azurite -v
3.20.1
```

### [コマンド ラインからの Azurite の実行する](https://learn.microsoft.com/ja-jp/azure/storage/common/storage-use-azurite?tabs=visual-studio#running-azurite-from-the-command-line)

```node
azurite --blobHost 127.0.0.1
```

## コンソール アプリケーションを作成する

[Azure Blob Storage と .NET の作業を始める](https://learn.microsoft.com/ja-jp/azure/storage/blobs/storage-blob-dotnet-get-started)

### パッケージ マネージャー コンソール

```powershell
Install-Package Azure.Storage.Blobs
```

### Program.cs

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;

namespace ConsoleApp14;

internal class Program
{
    static async Task Main(string[] args)
    {
        var connectionString = "AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;DefaultEndpointsProtocol=http;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;";
        await UploadAsync(connectionString, "test", "hoge.json");
    }

    private static async Task UploadAsync(string connectionString, string containerName, string blobName)
    {
        var storageAccount = new BlobServiceClient(connectionString);
        var container = storageAccount.GetBlobContainerClient(containerName);
        var blockBlob = container.GetBlockBlobClient(blobName);

        await using var stream = await blockBlob.OpenWriteAsync(overwrite: true);
        await using var streamWriter = new StreamWriter(stream);

        foreach (var i in Enumerable.Range(0, 100))
        {
            Console.WriteLine($"------ {i} ------");
            var person = new Person($"item{i}", i);
            await streamWriter.WriteLineAsync(person.ToString());
        }

        await streamWriter.FlushAsync();
        streamWriter.Close();

        await blockBlob.SetHttpHeadersAsync(new BlobHttpHeaders { ContentType = "application/json" });
    }

    private record Person(string Name, int Age);
}
```
