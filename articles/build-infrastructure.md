---
title: "#Terraform インフラストラクチャの構築"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Terraform", "Azure"]
published: true
---

# インフラストラクチャの構築

1. 初期構成の作成
2. Terraform の構成を初期化する
3. Terraform の実行を計画する
4. Terraform の構成を適用する
5. 状態を検査する

## 1. 初期構成の作成

ワークディレクトリに `main.tf` というテキストファイルを作成します。  
HCL（HashiCorp Configuration Language）で記述します。

例として、リソース グループを作成する定義を記述します。

```
# Configure the Azure provider
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = ">= 2.26"
    }
  }
}

provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "myTFResourceGroup"
  location = "westus2"
}
```

Terraform の設定においてリソース識別子のフォーマットは次のとおりです。  

```
<_type_>.<_name_>
```

今回の例に当てはめると次のとおりです。

```
azurerm_resource_group.rg
```

今回作成したリソース グループ `rg` は、Terraform 構成全体を通して参照される名前であり、Azure に作成されたリソース グループの名前ではありません。

## 2. Terraform の構成を初期化する

初期化処理で、必要なプロバイダーを自動的にダウンロードします。

```
> terraform init
```

出力は次のようになります。

```
Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/azurerm versions matching ">= 2.26.*"...
- Installing hashicorp/azurerm v2.38.0...
- Installed hashicorp/azurerm v2.38.0 (signed by HashiCorp)

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

## 3. Terraform の実行を計画する

`terraform plan` コマンドを使用して、Terraform で構成されるインフラストラクチャの実行プランを表示します。

```
> terraform plan
```

実行プランは次のようになります。

```
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # azurerm_resource_group.rg will be created
  + resource "azurerm_resource_group" "rg" {
      + id       = (known after apply)
      + location = "westus2"
      + name     = "myTFResourceGroup"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

## 4. Terraform の構成を適用する

`terraform apply` コマンドを使用して、設定を適用します。

```
> terraform apply
```

この出力は実行プランを表示します。  
本当に実行していいかどうか `Enter a value:` で確認されるので、`yes` と入力します。

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # azurerm_resource_group.rg will be created
  + resource "azurerm_resource_group" "rg" {
      + id       = (known after apply)
      + location = "westus2"
      + name     = "myTFResourceGroup"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```

`yes` と入力した後、Terraform はリソース グループを作成します。

```
azurerm_resource_group.rg: Creating...
azurerm_resource_group.rg: Creation complete after 1s [id=/subscriptions/c9ed8610-47a3-4107-a2b2-a322114dfb29/resourceGroups/myTFResourceGroup]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

ブラウザから Azure Portal に移動して、リソース グループが作成されたか確認します。

## 5. 状態を検査する

設定を適用すると、Terraform は `terraform.tfstate` ファイルにデータを書き込みます。このファイルには、Terraform が作成したリソースの ID とプロパティが含まれています。`terraform.tfstate` ファイルには、設定のすべてのデータが含まれており、プレーンテキストで機密性の高い値が含まれている可能性があります。

チームや大規模なプロジェクトでは、`terraform.tfstate` をリモートで保存することを検討してください。

現在の状態を確認するには、`terraform show` コマンドを使用します。

```
> terraform show
# azurerm_resource_group.rg:
resource "azurerm_resource_group" "rg" {
    id       = "/subscriptions/c9ed8610-47a3-4107-a2b2-a322114dfb29/resourceGroups/myTFResourceGroup"
    location = "westus2"
    name     = "myTFResourceGroup"
}
```

Terraform はこのリソース グループを作成したとき、リソースのプロパティとメタデータも収集しています。

`terraform.tfstate` ファイルの情報を確認するには、`terafform state list` コマンドを使用します。

```
> terraform state list
azurerm_resource_group.rg
```

`terraform state` コマンドを実行すると、状態の設定を表示したり操作したりするためのコマンドの一覧が表示されます。

```
> terraform state
Usage: terraform state <subcommand> [options] [args]

  This command has subcommands for advanced state management.

  These subcommands can be used to slice and dice the Terraform state.
  This is sometimes necessary in advanced cases. For your safety, all
  state management commands that modify the state create a timestamped
  backup of the state prior to making modifications.

  The structure and output of the commands is specifically tailored to work
  well with the common Unix utilities such as grep, awk, etc. We recommend
  using those tools to perform more advanced state tasks.

Subcommands:
    list                List resources in the state
    mv                  Move an item in the state
    pull                Pull current state and output to stdout
    push                Update remote state from a local state file
    replace-provider    Replace provider in the state
    rm                  Remove instances from the state
    show                Show a resource in the state
```

## 参考

- [de:code 2019 CD91 HashiCorp Terraform Azure Provider チュートリアル](https://eventmarketing.blob.core.windows.net/decode2019-after/decode19_PDF_CD91.pdf)
- [hashicorp/azurerm | Terraform Registry](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
- [Azure で Terraform を使用する | Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/developer/terraform/overview)
- [Get Started - Azure | Terraform - HashiCorp Learn](https://learn.hashicorp.com/collections/terraform/azure-get-started)
