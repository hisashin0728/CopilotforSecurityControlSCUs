# CopilotforSecurityControlSCUs
> This repository provides to Create/Update SCUs by your shcueduled time and Delete Copilot for Security. 

このレポジトリは、ロジックアプリを用いて Microsoft Copilot for Security のリソースを作成したり、セキュリティ コンピューティング ユニット(SCU) を時間帯で調整することを想定しています。

# How to Deploy
> Details how to deploy logicapps by ARM templates

ロジックアプリは ARM テンプレートを用いて展開されます。テンプレートは 2 つのロジックアプリ用に作成しています。<BR>
- CfSDeployUpdate.json
  - Copilot for Security を作成・および SCU 値を増減させるために用いるテンプレートです。
  - 初期値では土日を除いた ``10:00 JST`` に起動する設定になっています。詳細や変更方法については後述を参照してください。

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhisashin0728%2FCopilotforSecurityControlSCUs%2Fmain%2FCfSDeployUpdate.json)

- CfSDeployDelete.json
  - Copilot for Security のリソースを削除するために用いるテンプレートです。
  - 初期値では毎日 ``16:55 JST`` に起動する設定になっています。詳細や変更方法については後述を参照してください。

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhisashin0728%2FCopilotforSecurityControlSCUs%2Fmain%2FCfSDeployDelete.json)

# Parameters
> Details parameter in ARM Templates

ARM テンプレートでは、導入に際してパラメータを設定する必要があります。

| Parameter | 説明 |
| ---- | ---- |
| ``PlaybookName`` | ロジックアプリの名称です。ご自分の環境に合わせて変更可能です。 |
| ``DeployCfSTargetResourceGroup`` | Microsoft Copilot for Security をデプロイする先の対象リソースグループです。<BR>※ロジックアプリはこのリソースグループに対して CfS を作成・削除します。**リソースグループは事前に作成しておいてください** |
| ``Geo`` | Copilot for Security のデプロイ時の Region 指定です。本テンプレートでは ``eastus`` / ``US`` で設定されています |
| ``capacityName`` | Copilot for Security デプロイ時の名前です。変更する際は 2 つのロジックアプリの本名称が同一のものであるように設定して下さい |
| ``numberofSCUs`` | 追加・アップデート・削除する際の SCU 数です。<BR>※型の warning が表示されますが、仕様のため無視してください |

![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/c09fb338-88ef-402d-9378-c30e70ac80c1)

# After Install ARM Template
> Check your logic apps!

ARM テンプレートの導入後、以下をチェックして下さい。<br>
- リソースグループに 2 つのリソース / ARM テンプレートが作成されているか？ (ロジックアプリ / API 接続)

ロジックアプリデザイナーを開き、エラーが無いことを確認して下さい。<br>
![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/367cd303-bce9-4a72-ac6e-763d00f55c8f)
![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/060f3bfb-9370-4f58-8bbc-104004010d27)

- ロジックアプリ上部メニューの「パラメーター」を開いて、ロジックアプリ内で用いるパラメーター値がセットされているかどうかご確認下さい。
  - ``DeployCfSSubscriptionId``
  - ``DeployCfSTagetResourceGroup``
  - ``Geo``
  - ``capacityName``
  - ``crossGeoCompute``
  - ``numberOfSCUs``
<img width="674" alt="image" src="https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/407dc46d-31ce-424f-ad1a-0b505b351e6b">

- 本ロジックアプリは Managed ID を用いて ``Co-Owner`` 権限が必要になりますので、権限を付与して下さい
  - ロジックアプリが対象のリソースグループに対して Copilot for Security のデプロイ/削除を行うため、権限付与が必要です。
  - 左側「ID」より「Azure ロールの割り当て」を設定し、「共同所有者 (Co-Owner)」ロールの付与を行って下さい。

![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/ae4b0660-7895-4016-80af-499444a8c565)
 
# Tuning
> Tuning the deploy / Delete Scheule

初期設定では、ロジックアプリは土日を除いた ``10:00 JST`` に毎日 Copilot for Security を起動させます。<br>
スケジュールを変更したい場合は ``Recuurence`` フローをクリックし、起動時間を編集して下さい。<br>

![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/bcf0c750-00a5-413a-b03d-92c4bb7f9458)<br>

※土日を除く設定については、後段の ``Condition`` 条件内に含まれています。
  - ``0`` は日曜日
  - ``6`` は土曜日
![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/da098254-0662-4234-a4cb-6ce81cebcdcd)

# Deploy Sample
- 平日 10:00 - 16:55、土日除く構成（ SCU 1 Unit で \96,000-/Month の予算枠を想定)
  - CfSDeployUpdate.json
    - 10:00 起動 / 土日除く 
  - CfSDeployUpdate.json
    - 16:55 起動
