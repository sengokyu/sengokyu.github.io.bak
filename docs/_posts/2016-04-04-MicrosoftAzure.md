---
date: '2016-04-04T19:05:27+09:00'
layout: post
published: true
qiita_article_id: 1c3994ac8a2f0f0e88c5
tags:
- Microsoft
- Azure
- PowerShell
title: '[Microsoft Azure]勤務時間中だけ仮想マシンを動かす（スケジュールによる自動起動・停止）'
updated: '2018-04-23T13:01:42+09:00'

---
# 概要  
  
AutomationアカウントにPowerShellワークフローを登録し、スケジュールに合わせて仮想マシンを起動/停止します。  
  
仮想マシンの起動/停止は、サービスプリンシパルとして登録したActive Directoryアカウント権限で実行します。  
  
下記情報を参考にしました。  
  
- [AzureVMを自動起動/停止させる手順書（無料）](http://qiita.com/HHFactory/items/3d75a189e809a3e3d36c)  
- [Scheduling a runbook in Azure Automation](https://azure.microsoft.com/en-us/documentation/articles/automation-scheduling-a-runbook/)  
  
  
----  
**2016年4月7日追記:**  
下記手順はとても長くて面倒なので、モジュール化してPowerShell Galleryへ公開しました。  
  
こちらへどうぞ。 [【再】[Microsoft Azure]勤務時間中だけ仮想マシンを動かす（スケジュールによる自動起動・停止）](http://qiita.com/sengoku/items/99b14b4705528580a97a)   
  
----  
  
# 準備  
  
必要なPowerShellモジュールをインストールします。  
  
[PowerShellからMicrosoft Azureを管理する【準備編】](http://qiita.com/sengoku/items/614a1d8faf52061c95cc)  
  
# 手順  
  
## ステップ :zero:   
  
### ログイン  
  
まだログインしていなければログインします。  
  
```
Login-AzureRmAccount
```  
  
### 変数を定義  
  
後から使う変数をあらかじめ定義しておきます。  
アプリケーション名とパスワードはお好みで決めてください。  
以下の説明では、アプリケーション名とAutomationアカウント名は同一にしています。  
  
****  
```ps1:
$ApplicationName = "schevm" #ADアプリケーション名
$ApplicationPassword = "myPassword!" #ADアプリケーションパスワード
$Context = Get-AzureRmContext
$SubscriptionId = $Context.Subscription.SubscriptionId.ToString()
$TenantId = $Context.Tenant.TenantId.ToString()
$ResourceGroupName = "rg01" #リソースグループ名
$AutomationAccountName = "schevm" #Automationアカウント名
$StartRunbookName = "Start-SingleAzureRmVm" #RunBook名
$StopRunbookName = "Stop-SingleAzureRmVm" #RunBook名
$StartScheduleName = "Start Vm" #VM起動スケジュール名
$StopScheduleName = "Stop Vm" #VM停止スケジュール名
$ScheduleParams = @{ResourceGroupName=$ResourceGroupName;VmName="MyVmName"} #RunBook起動時のパラメータ。仮想マシン名を指定します。
```  
  
## ステップ :one: ADアプリケーションを作成  
  
ホームページとIdentifierUriは適当に決めます。後者は一意であればよいはずです。  
  
****  
```ps1:
$NewApp = New-AzureRmADApplication -DisplayName $ApplicationName -Password $ApplicationPassword -HomePage "https://management.azure.com/subscriptions/${SubscriptionId}/resourcegroups/${ResourceGroupName}/providers/Microsoft.Automation/automationAccounts/${AutomationAccountName}"  -IdentifierUris "https://spn/${SubscriptionId}/${ApplicationName}"
```  
  
## ステップ :two: ADサービスプリンシパルを作成  
  
****  
```ps1:
PS C:\> > $NewSp = New-AzureRmADServicePrincipal -ApplicationId $NewApp.ApplicationId
```  
  
## ステップ :three: ADサービスプリンシパルにロールを割り当て  
  
リソースの読み取りができるように`Reader`と、仮想マシンを起動/停止できるように`Virtual Machine Contributor`を割り当てました。  
  
****  
```ps1:
New-AzureRmRoleAssignment -ServicePrincipalName $NewSp.ServicePrincipalName -RoleDefinitionName "Reader" -ResourceGroupName $ResourceGroupName
New-AzureRmRoleAssignment -ServicePrincipalName $NewSp.ServicePrincipalName -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName $ResourceGroupName
```  
  
## ステップ :four: Automationアカウントを作成  
  
****  
```ps1:
New-AzureRmAutomationAccount -ResourceGroupName $ResourceGroupName -AutomationAccountName $AutomationAccountName -Location "Japan East"
```  
  
## ステップ :five: Automationアカウントへ資産を作成  
  
Credential資産と、Variable資産を作成します。  
後で出てくるRunBookの中で使っています。ここで作成する資産名を変更するときは、RunBook実行時のパラメータもあわせて変更してください。  
  
****  
```ps1:
$PsCred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $NewApp.ApplicationId,(ConvertTo-SecureString -AsPlainText -Force $ApplicationPassword)
New-AzureRmAutomationCredential -ResourceGroupName $ResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureCredential -Value $PsCred
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroupName -AutomationAccountName $AutomationAccountName -Name "TenantId" -Value $TenantId -Encrypted $false
```  
  
## ステップ :six: RunBookをインポート  
  
### 起動用PowerShellワークフロー  
  
次の内容を`適当な名前.ps1`という名前で保存します。  
  
**Start-SingleAzureRmVm.ps1**  
```ps1:Start-SingleAzureRmVm.ps1
Workflow Start-SingleAzureRmVm {
    Param(
        [parameter(mandatory=$false)]
        [String]
        $CredentialAssetName = "AzureCredential",

        [parameter(mandatory=$false)]
        [String]
        $TenantIdAssetName = "TenantId",

        [parameter(mandatory=$true)]
        [String]
        $ResourceGroupName,

        [parameter(mandatory=$true)]
        [String]
        $VmName,

        [parameter(mandatory=$false)]
        [Boolean]
        $DryRun = $false
    )


    <#
    We do not work on Sunday and Saturday
    #>
    function IsBusinessDay {
        $dow = (Get-Date).DayOfWeek

        !($dow -eq [DayOfWeek]::Sunday -or $dow -eq [DayOfWeek]::Saturday)
    }

    <#
    Login Azure RM
    #>
    function LoginAzureRm {
        param([String]$CredentialAssetName, [String]$TenantIdAssetName)

        $Cred = Get-AutomationPSCredential -Name $CredentialAssetName
        $TenantId = Get-AutomationVariable -Name $TenantIdAssetName

        if(!$Cred) {
            Throw "Could not find an Automation Credential Asset named '${CredentialAssetName}'. Make sure you have created one in this Automation Account."
        }

        Login-AzureRmAccount -ServicePrincipal -Credential $Cred -TenantId $TenantId | Out-Null
    }

    <#
    Returns true if Virtual Machine already started.
    #>
    function IsVmAllocated {
        param([String]$ResourceGroupName, [String]$VmName)
        
        $Vm = Get-AzureRmVM -Name $VmName -ResourceGroupName $ResourceGroupName -Status -ErrorAction Continue
        
        if (!$Vm) {
            Throw "Virtual machine '${VmName}' not found in Resource Group '${ResourceGroupName}'."
        }

        $Status = $Vm.Statuses | ?{ $_.Code -eq "PowerState/deallocated" }

        $Status -eq $null
    }

    <#
    Start virtual machine.
    Rertrying 3 times until success.
    #>
    function StartVm {
        param([String]$ResourceGroupName, [String]$VmName)        

        $TryCount = 0

        Do {
            $TryCount++

            Write-Output "Trying to start Virtual Machine '${VmName}', Count=${TryCount}"

            $Result = Start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VmName -ErrorAction Continue

            if ($Result.IsSuccessStatusCode) {
                Write-Output "Virtual Machine '${VmName}' started."
            }
            else {
                Write-Output "Virtual Machine '${VmName}' cannot start."
                Start-Sleep -Seconds 15
            }
        } while($TryCount -le 3 -and !$Result.IsSuccessStatusCode)
    }

    ############
    # Main
    ############
    if (IsBusinessDay) {
        LoginAzureRm -CredentialAssetName $CredentialAssetName -TenantIdAssetName $TenantIdAssetName

        if (IsVmAllocated -ResourceGroupName $ResourceGroupName -VmName $VmName) {
            Write-Output "Virtual machine '${VmName}' is already allocated."
        }
        else {
            if ($DryRun) {
                Write-Output "DryRun mode, nothing to do."
            } else {
                StartVm -ResourceGroupName $ResourceGroupName -VmName $VmName
            }
        }
    } else {
        Write-Output "Today is not a business day, nothing to do."
    }
}
```  
  
### 停止用PowerShellワークフロー  
  
次の内容を`適当な名前.ps1`という名前で保存します。  
  
**Stop-SingleAzureRmVm.ps1**  
```ps1:Stop-SingleAzureRmVm.ps1
Workflow Stop-SingleAzureRmVm {
    Param(
        [parameter(mandatory=$false)]
        [String]
        $CredentialAssetName = "AzureCredential",

        [parameter(mandatory=$false)]
        [String]
        $TenantIdAssetName = "TenantId",

        [parameter(mandatory=$true)]
        [String]
        $ResourceGroupName,

        [parameter(mandatory=$true)]
        [String]
        $VmName,

        [parameter(mandatory=$false)]
        [Boolean]
        $DryRun = $false
    )

    <#
    Login Azure RM
    #>
    function LoginAzureRm {
        param([String]$CredentialAssetName, [String]$TenantIdAssetName)

        $Cred = Get-AutomationPSCredential -Name $CredentialAssetName
        $TenantId = Get-AutomationVariable -Name $TenantIdAssetName

        if(!$Cred) {
            Throw "Could not find an Automation Credential Asset named '${CredentialAssetName}'. Make sure you have created one in this Automation Account."
        }

        Login-AzureRmAccount -ServicePrincipal -Credential $Cred -TenantId $TenantId | Out-Null
    }

    <#
    Returns true if Virtual Machine already started.
    #>
    function IsVmAllocated {
        param([String]$ResourceGroupName, [String]$VmName)
        
        $Vm = Get-AzureRmVM -Name $VmName -ResourceGroupName $ResourceGroupName -Status -ErrorAction Continue
        
        if (!$Vm) {
            Throw "Virtual machine '${VmName}' not found in Resource Group '${ResourceGroupName}'."
        }

        $Status = $Vm.Statuses | ?{ $_.Code -eq "PowerState/deallocated" }

        $Status -eq $null
    }

    <#
    Start virtual machine.
    Rertrying 3 times until success.
    #>
    function StopVm {
        param([String]$ResourceGroupName, [String]$VmName)        

        $TryCount = 0

        Do {
            $TryCount++

            Write-Output "Trying to stop Virtual Machine '${VmName}', Count=${TryCount}"

            $Result = Stop-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VmName -Force -ErrorAction Continue

            if ($Result.IsSuccessStatusCode) {
                Write-Output "Virtual Machine '${VmName}' stoped."
            }
            else {
                Write-Output "Virtual Machine '${VmName}' cannot stop."
                Start-Sleep -Seconds 15
            }
        } while($TryCount -le 3 -and !$Result.IsSuccessStatusCode)
    }

    ############
    # Main
    ############
    LoginAzureRm -CredentialAssetName $CredentialAssetName -TenantIdAssetName $TenantIdAssetName

    if (IsVmAllocated -ResourceGroupName $ResourceGroupName -VmName $VmName) {
        if ($DryRun) {
            Write-Output "DryRun mode, nothing to do."
        } else {
            StopVm -ResourceGroupName $ResourceGroupName -VmName $VmName
        }
    }
    else {
        Write-Output "Virtual machine '${VmName}' is not running."
    }
}
```  
  
### PowerShellワークフローをインポート  
  
保存したファイルをインポートします。  
  
****  
```ps1:
Import-AzureRmAutomationRunbook -ResourceGroupName $ResourceGroupName -AutomationAccountName $AutomationAccountName -Path .\Start-SingleAzureRmVm.ps1 -Name $StartRunBookName  -Type PowerShellWorkflow
Import-AzureRmAutomationRunbook -ResourceGroupName $ResourceGroupName -AutomationAccountName $AutomationAccountName -Path .\Stop-SingleAzureRmVm.ps1 -Name $StopRunBookName  -Type PowerShellWorkflow
```  
  
### :bulb: PowerShellワークフローのパラメータ  
  
上記でインポートしたPowerShellワークフローのパラメータです。  
  
| パラメータ名 | 説明 | 必須 | 既定値 |  
|------------|------|------|-------|  
|CredentialAssetName | 資格情報を定義した資産名です。 | |AzureCredential |  
|TenantIdAssetName | TenantIdを定義した資産名です。 | | TenantId |  
| ResourceGroupName | リソースグループ名です。 | ○ | |  
| VmName | 仮想マシン名です。 | ○ | |  
| DryRun | $trueにすると、実際に起動/停止はしません。 | | $false |  
  
  
### :bulb: テスト実行  
  
ここまでくれば、Azureポータル上からテスト実行できます。  
  
[すべてのリソース] > *AutoMaitionアカウント名* > [RunBook]  > *RunBook名* > [テストウィンドウ] と辿ると、テスト実行画面が表示されます。  
  
パラメータを設定して実行してみてください。  
  
![test-runbook.JPG](/assets/images/f979402a-f162-e10e-a002-97bfe2287565.jpeg)  
  
  
### :bulb: ワークフローを編集するときは……  
  
インポート後に編集するときは、Azureポータルからではなくファイルを編集して上書きインポート（`-Force`オプションをつける）するほうがよさそうです。  
Azureポータルで編集しても、反映されないことがあります。  
  
## ステップ :seven: RunBookを発行  
  
実使用できるように発行します。  
  
****  
```ps1:
Publish-AzureRmAutomationRunbook -ResourceGroupName $ResourceGroupName -AutomationAccountName $AutomationAccountName -Name $StartRunBookName
Publish-AzureRmAutomationRunbook -ResourceGroupName $ResourceGroupName -AutomationAccountName $AutomationAccountName -Name $StopRunBookName
```  
  
## ステップ :eight: スケジュールを設定  
  
### VM起動用スケジュールを作成  
  
9:00に起動することにしました。適当に変更してください。  
  
****  
```ps1:
New-AzureRmAutomationSchedule -ResourceGroupName $ResourceGroupName -AutomationAccountName $AutomationAccountName -Name $StartScheduleName -StartTime ((Get-Date "9:00").AddDays(1)) -DayInterval 1
```  
  
### VM停止用スケジュールを作成  
  
18:00に停止することにしました。適当に変更してください。  
  
****  
```ps1:
New-AzureRmAutomationSchedule -ResourceGroupName $ResourceGroupName -AutomationAccountName $AutomationAccountName -Name $StopScheduleName -StartTime ((Get-Date "18:00").AddDays(1)) -DayInterval 1
```  
  
### スケジュールとRunBookをリンク  
  
スケジュールとRunBookをリンクします。  
パラメータもここで指定します。  
  
****  
```ps1:
Register-AzureRmAutomationScheduledRunbook -ResourceGroupName $ResourceGroupName -AutomationAccountName $AutomationAccountName -Parameters $ScheduleParams -RunbookName $StartRunbookName -ScheduleName $StartScheduleName
Register-AzureRmAutomationScheduledRunbook -ResourceGroupName $ResourceGroupName -AutomationAccountName $AutomationAccountName -Parameters $ScheduleParams -RunbookName $StopRunbookName -ScheduleName $StopScheduleName
```  
  
# 最後に  
  
Windows 仮想マシンの起動/停止を繰り返すと、レジストリに未使用のネットワークインターフェース情報がたまっていき、不安定になるそうです。  
  
こちらに回避策があります。  
https://blogs.technet.microsoft.com/jpaztech/2016/02/18/delete-nic/  
