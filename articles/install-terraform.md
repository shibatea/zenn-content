---
title: "#Terraform をインストールする"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Terraform", "Azure"]
published: true
---

# Terraform をインストールする

## 前提条件

- [terraform](https://www.terraform.io/downloads.html)
- [Azure CLI](https://aka.ms/installazurecliwindows)
- VS Code 拡張機能
  - [HashiCorp Terraform](https://marketplace.visualstudio.com/items?itemName=HashiCorp.terraform)
  - [Azure Terraform](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureterraform)

## インストール

- terraform
  - ダウンロードサイトから zip ファイルをダウンロードします。
  - zip ファイルを解凍して terraform.exe があることを確認します。
  - C:\terraform フォルダ配下に terraform.exe を配置します。
  - C:\terraform をシステム環境変数 PATH に追加します。
- Azure CLI
  - インストーラーをダウンロードしてインストールします。

## 動作確認

```
> terraform -v
Terraform v0.14.0

> az -v
azure-cli                         2.15.1
```

## Azure CLI を使用した認証

ターミナルで Azure CLI を使用して、アカウントのアクセス許可をローカルに設定します。  

```
> az login
```

ブラウザーが開き、Azure ログイン資格情報の入力を求められます。  
認証が成功すると、ターミナルにサブスクリプション情報が表示されます。  

```
You have logged in. Now let us find all the subscriptions to which you have access...

[
  {
    "cloudName": "AzureCloud",
    "homeTenantId": "0envbwi39-home-Tenant-Id",
    "id": "35akss-subscription-id",
    "isDefault": true,
    "managedByTenants": [],
    "name": "Subscription-Name",
    "state": "Enabled",
    "tenantId": "0envbwi39-TenantId",
    "user": {
      "name": "your-username@domain.com",
      "type": "user"
    }
  }
]
```

## 参考

- [de:code 2019 CD91 HashiCorp Terraform Azure Provider チュートリアル](https://eventmarketing.blob.core.windows.net/decode2019-after/decode19_PDF_CD91.pdf)
- [hashicorp/azurerm | Terraform Registry](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
- [Azure で Terraform を使用する | Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/developer/terraform/overview)
- [Get Started - Azure | Terraform - HashiCorp Learn](https://learn.hashicorp.com/collections/terraform/azure-get-started)
