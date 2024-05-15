# CopilotforSecurityControlSCUs
> This repository provides to Create/Update SCUs by your shcueduled time and Delete Copilot for Security. 

このレポジトリは、ロジックアプリを用いて Microsoft Copilot for Security のリソースを作成したり、セキュリティ コンピューティング ユニット(SCU) を時間帯で調整することを想定しています。

# How to Deploy
> Details how to deploy logicapps by ARM templates

ロジックアプリは ARM テンプレートを用いて展開されます。テンプレートは 2 つのロジックアプリ用に作成しています。<BR>
- CfSDeployUpdate.json
  - Copilot for Security を作成・および SCU 値を増減させるために用いるテンプレートです。
  - 初期値では土日を除いた ``10:00 JST`` に起動する設定になっています。詳細や変更方法については後述を参照してください。
- CfSDeployDelete.json
  - Copilot for Security のリソースを削除するために用いるテンプレートです。
  - 初期値では毎日 ``16:55 JST`` に起動する設定になっています。詳細や変更方法については後述を参照してください。

展開については、以下の DeployToAzure ボタンをクリックしてください。

- CfSDeployUpdate.json

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhisashin0728%2FCopilotforSecurityControlSCUs%2Fmain%2FCfSDeployUpdate.json)
<p>
  - CfSDeployDelete.json

  [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhisashin0728%2FCopilotforSecurityControlSCUs%2Fmain%2FCfSDeployDelete.json)

# Parameters
> Details parameter in ARM Templates

