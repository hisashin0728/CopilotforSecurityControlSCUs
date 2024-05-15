# CopilotforSecurityControlSCUs
> This repository provides to Create/Update SCUs by your shcueduled time and Delete Copilot for Security. 

[**日本語 Readme はこちら**](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/blob/main/READMEJP.md)<BR>

This repository is intended to use Logic Apps to create Microsoft Copilot for Security resources or adjust Security Compute Units (SCUs) based on time slots. It is intended to be used to optimize actual operations or for Proof of Concept (PoC) purposes by adjusting the SCUs based on time slots.<br>
![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/326a9c6d-330e-4f80-803c-05988da1fd82)

---
Although it is possible to delete Copilot for Security resources based on time slots for PoC purposes, please limit it to testing purposes only, as it may cause various disadvantages. Deleting resources has the following limitations:
- During that time, accessing from the standalone portal and resources that utilize Copilot for Security (such as Defender XDR) will fail due to insufficient capacity.
- Custom plugins and custom prompt books created will be erased, making it impossible to reuse them.
---

# How to Deploy
> Details how to deploy logicapps by ARM templates

This Logic Apps will deploy resources using ARM templates. Two templates have been created for Logic Apps<BR>
- CfSDeployUpdate.json
  - This template is used to create Copilot for Security and adjust the SCU values.
  - By default, it's set to start at ``10:00 JST`` on weekdays, excluding weekends. Refer to the following for details and how to change it

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhisashin0728%2FCopilotforSecurityControlSCUs%2Fmain%2FCfSDeployUpdate.json)

- CfSDeployDelete.json
  - This template is used to delete Copilot for Security resources.
  - By default, it's set to start at ``16:55 JST`` every day. Refer to the following for details and how to change it.

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhisashin0728%2FCopilotforSecurityControlSCUs%2Fmain%2FCfSDeployDelete.json)

# Parameters
> Details parameter in ARM Templates

In ARM templates, you need to set parameters for installation.

| Parameter | Description |
| ---- | ---- |
| ``PlaybookName`` | The name of the Logic App. You can change it to suit your environment. |
| ``DeployCfSTargetResourceGroup`` | **You need to create a target resource group to deploy Microsoft Copilot for Security separately.** <br>※ The Logic App will create/delete CfS in this resource group. <br> **※ Please create the resource group in advance** |
| ``Geo`` | Specifies the region when deploying Copilot for Security. This template is set to `eastus` / `US`. |
| ``capacityName`` | The name when deploying Copilot for Security. If you change it, please make sure that the names in both Logic Apps are the same. |
| ``numberofSCUs`` | The number of SCUs to add, update, or delete. <br> ※ A type warning is displayed, but please ignore it as it is by design. |

![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/c09fb338-88ef-402d-9378-c30e70ac80c1)

# After Install ARM Template
> Check your logic apps!

After installing the ARM template, please check the following:<br>
- Are there two resources / ARM templates created in the resource group? (Logic App / API connection)<br>
- Open the Logic App Designer and check for errors.<br>
![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/367cd303-bce9-4a72-ac6e-763d00f55c8f)
![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/060f3bfb-9370-4f58-8bbc-104004010d27)

- Open the Parameters in the top menu of the Logic App and check that the parameter values used in the Logic App are set.
  - ``DeployCfSSubscriptionId``
  - ``DeployCfSTagetResourceGroup``
  - ``Geo``
  - ``capacityName``
  - ``crossGeoCompute``
  - ``numberOfSCUs``
<img width="674" alt="image" src="https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/407dc46d-31ce-424f-ad1a-0b505b351e6b">

- This Logic App requires ``Co-Owner`` role permission using Managed ID, so please grant the permission.
  - The Logic App needs permission to deploy/delete Copilot for Security in the target resource group.
  - From Identity on the left, set Azure Role Assignment and assign the ``Co-Owner`` role.

![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/ae4b0660-7895-4016-80af-499444a8c565)
 
# Tuning
> Tuning the deploy / Delete Scheule

In the initial settings,the Logic App starts Copilot for Security every day at 10:00 JST on weekdays except weekends.<br>
If you want to change the schedule, click the \"Recurrence\" flow and edit the start time.。<br>

![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/bcf0c750-00a5-413a-b03d-92c4bb7f9458)<br>

> The weekend exclusion setting is included in the \"Condition\" condition later on.
  - ``0`` is Sunday
  - ``6`` is Saturday
![image](https://github.com/hisashin0728/CopilotforSecurityControlSCUs/assets/55295601/da098254-0662-4234-a4cb-6ce81cebcdcd)

# Customize Your Environment
> Tune your suitable Copilot for Secuirity environment!

- Usecase-1 : SCU Adjustment Model / Weekday 10:00 - 16:55 with 2 SCU Units, 1 SCU Unit on weekends.
  - CfSDeployUpdate.json (Parameter: SCU = 2)
    - Starts at 10:00 / excluding weekend (Sunday/Saturday)
  - CfSDeployUpdate.json (Parameter: SCU = 1)
    - Starts at 16:55

- Usecase-2 : PoC / Trial Copilot for Security / Weekday 10:00 - 16:55 with 1 SCU Unit, excluding Weekends - Low costs
  - CfSDeployUpdate.json
    - Starts at 10:00 / excluding weekend (Sunday/Saturday)
  - CfSDeployUpdate.json
    - Starts at 16:55 as resources deletion
