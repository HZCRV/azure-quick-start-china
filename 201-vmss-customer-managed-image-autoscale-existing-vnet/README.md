### Autoscale a VM Scale Set For Windows Customer Image with Managed Disks###

## 操作系统准备 ##

- Windows 操作系统

打开目前执行 Sysprep.exe
c:\windows\system32\sysprep\sysprep.etc

1.Enter System Out-of-Box Experience (OOBE)
2.Generalize
3.Shutdown

- Linux 操作系统

sudo waagent-deprovision

## 捕获虚拟机镜像 ##

- 定义变量
$vmName = "myVM"
$rgName = "myresourcegroup"
$location = "chinanorth"
$imageName = "myImage"


- 保证虚拟机已经关机
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

- 通用化虚拟机
Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized

- 声明VM变量
$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName

$image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID

- 创建镜像
New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName

- 获得镜像ID
(Get-AzureRmImage -ResourceGroupName $rgName -ImageName $imageName).Id

• 镜像ID后续将添加到模板的参数中，形如:
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/xxxxx/providers/Microsoft.Compute/images/xxxxx


The Autoscale rules are configured as follows
- sample for CPU (\\Processor\\PercentProcessorTime) in each VM every 1 Minute
- if the Percent Processor Time is greater than 50% for 5 Minutes, then the scale out action (add more VM instances, one at a time) is triggered
- once the scale out action is completed, the cool down period is 1 Minute


<a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fdafoyiming%2Fazure-quick-start-china%2Fmeat%2F201-vmss-customer-managed-image-autoscale-existing-vnet%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>
<a href="http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-ubuntu-autoscale%2Fazuredeploy.json" target="_blank">
    <img src="http://armviz.io/visualizebutton.png"/>
</a>