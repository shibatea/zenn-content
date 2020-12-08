---
title: "#Terraform インフラストラクチャの変更"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Terraform", "Azure"]
published: true
---

# インフラストラクチャの変更

Terraform は、`terraform.tfstate` ファイルの状態と、対象リソースの現在の状態を比較することによって、実行プランを構築します。

1. 構成を更新する
2. 構成を計画する
3. 変更を適用する

## 1. 構成を更新する

作成したリソース グループにタグを追加します。  
構成ファイル `main.tf` の `azurerm_resource_group.rg` に、次のように `tags` ブロックを追加します。

```
resource "azurerm_resource_group" "rg" {
    name     = "myTFResourceGroup"
    location = "westus2"

    tags = {
        Environment = "Terraform Getting Started"
        Team = "DevOps"
    }
}
```

## 2. 構成を計画する

設定が変更された場合、実行プランには Terraform がどのようなアクションをとって変更を反映させるかが表示されます。  
`-out=newplan` オプションを指定することで、現在のディレクトリに新しい実行プランを `newplan` という名前で保存します。実行プランを名前付けすることにより、適用する実行プランを明示的に指定することができます。

```
> terraform plan -out=newplan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.

azurerm_resource_group.rg: Refreshing state... [id=/subscriptions/c9ed8610-47a3-4107-a2b2-a322114dfb29/resourceGroups/myTFResourceGroup]

------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # azurerm_resource_group.rg will be updated in-place
  ~ resource "azurerm_resource_group" "rg" {
        id       = "/subscriptions/c9ed8610-47a3-4107-a2b2-a322114dfb29/resourceGroups/myTFResourceGroup"
        location = "westus2"
        name     = "myTFResourceGroup"
      ~ tags     = {
          + "Environment" = "Terraform Getting Started"
          + "Team"        = "DevOps"
        }
    }

Plan: 0 to add, 1 to change, 0 to destroy.

------------------------------------------------------------------------

This plan was saved to: newplan

To perform exactly these actions, run the following command to apply:
    terraform apply "newplan"
```

## 3. 変更を適用する

実行プラン `newplan` を適用します。

```
$ terraform apply "newplan"
azurerm_resource_group.rg: Modifying... [id=/subscriptions/c9ed8610-47a3-4107-a2b2-a322114dfb29/resourceGroups/myTFResourceGroup]
azurerm_resource_group.rg: Modifications complete after 1s [id=/subscriptions/c9ed8610-47a3-4107-a2b2-a322114dfb29/resourceGroups/myTFResourceGroup]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.

The state of your infrastructure has been saved to the path
below. This state is required to modify and destroy your
infrastructure, so keep it safe. To inspect the complete state
use the `terraform show` command.

State path: terraform.tfstate
```

`terraform show` コマンドを使用して、このリソース グループに関連付けられた新しい値を表示します。

```
> terraform show
# azurerm_resource_group.rg:
resource "azurerm_resource_group" "rg" {
    id       = "/subscriptions/c9ed8610-47a3-4107-a2b2-a322114dfb29/resourceGroups/myTFResourceGroup"
    location = "westus2"
    name     = "myTFResourceGroup"
    tags     = {
        "Environment" = "Terraform Getting Started"
        "Team"        = "DevOps"
    }
}
```

## 参考

- [de:code 2019 CD91 HashiCorp Terraform Azure Provider チュートリアル](https://eventmarketing.blob.core.windows.net/decode2019-after/decode19_PDF_CD91.pdf)
- [hashicorp/azurerm | Terraform Registry](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
- [Azure で Terraform を使用する | Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/developer/terraform/overview)
- [Get Started - Azure | Terraform - HashiCorp Learn](https://learn.hashicorp.com/collections/terraform/azure-get-started)
