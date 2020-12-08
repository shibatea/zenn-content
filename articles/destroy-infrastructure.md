---
title: "#Terraform インフラストラクチャの破棄"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Terraform", "Azure"]
published: true
---

# インフラストラクチャの破棄

Terraform で管理されたリソースグループを完全に破棄します。

## インフラストラクチャを破棄する

`terraform destroy` コマンドを使用することで、Azure クラウドから Terraform で管理されたインフラストラクチャを削除します。

```
> terraform destroy

azurerm_resource_group.rg: Refreshing state... [id=/subscriptions/c9ed8610-47a3-4107-a2b2-a322114dfb29/resourceGroups/myTFResourceGroup]

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # azurerm_resource_group.rg will be destroyed
  - resource "azurerm_resource_group" "rg" {
      - id       = "/subscriptions/c9ed8610-47a3-4107-a2b2-a322114dfb29/resourceGroups/myTFResourceGroup" -> null
      - location = "westus2" -> null
      - name     = "myTFResourceGroup" -> null
      - tags     = {
          - "Environment" = "Terraform Getting Started"
          - "Team"        = "DevOps"
        } -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value:
```

`-` プレフィックスは、対象リソースが破棄されることを示します。  
`terraform apply` と同様に、変更を行う前に確認を待ちます。

プロンプトが表示されたら、`yes` と入力することでインフラストラクチャの破棄が実行されます。

## 参考

- [de:code 2019 CD91 HashiCorp Terraform Azure Provider チュートリアル](https://eventmarketing.blob.core.windows.net/decode2019-after/decode19_PDF_CD91.pdf)
- [hashicorp/azurerm | Terraform Registry](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)
- [Azure で Terraform を使用する | Microsoft Docs](https://docs.microsoft.com/ja-jp/azure/developer/terraform/overview)
- [Get Started - Azure | Terraform - HashiCorp Learn](https://learn.hashicorp.com/collections/terraform/azure-get-started)
