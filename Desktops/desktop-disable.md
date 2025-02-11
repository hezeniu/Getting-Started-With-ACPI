# 禁用桌面不支持的gpu (SSDT-GPU-DISABLE)

所以这主要是在macOS不支持的GPU上需要的，主要是那些希望为macOS使用配对AMD GPU的Nvidia用户。虽然WhateverGreen确实支持引导参数 `-wegnoegpu`，但这只在iGPU上运行时有效，所以对于我们其他人来说，我们需要创建一个SSDT。

所以要禁用特定的GPU，我们需要找到一些东西:

* CPI GPU路径
* [SSDT-GPU-DISABLE](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/decompiled/SSDT-GPU-DISABLE.dsl.zip)

## 查找GPU的ACPI路径

找到GPU的PCI路径是相当简单的，最好的方法是运行Windows:

* 打开设备管理器
* 选择显示适配器，然后右键单击您的GPU并选择属性
* 在详细资料标签下，搜寻“位置路径”
  * 注意一些gpu可能隐藏在“BIOS设备名称”下

![](../images/Desktops/amd.png)

![Credit to 1Revenger1 for the image](../images/Desktops/nvidia.png)

第二个“ACPI”是我们关心的:

```
ACPI(_SB_)#ACPI(PC02)#ACPI(BR2A)#ACPI(PEGP)#PCI(0000)#PCI(0000)
```

现在将其转换为ACPI路径非常简单，删除`#ACPI`和`#PCI(0000)`:

```
`_SB_.PC02.BR2A.PEGP
```

瞧!我们已经找到了ACPI路径，现在我们已经准备好了一切

## 制作SSDT

获取我们的 [SSDT-GPU-DISABLE](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/decompiled/SSDT-GPU-DISABLE.dsl.zip)并打开它。这里有几点需要改变:

```
External (_SB_.PCI0.PEG0.PEGP, DeviceObj)
Method (_SB.PCI0.PEG0.PEGP._DSM, 4, NotSerialized)
```

在我们的例子中，我们将更改所有提及的内容:

* `PCI0` with `PC02`
* `PEG0` with `BR2A`

提示:如果您的ACPI路径比示例短一点，那么这是可以的。只要确保ACPI路径对你的设备是正确的，一些用户可能还需要根据他们的路径调整`_SB_`

## [现在您已经准备好编译SSDT!](/Manual/compile.md)
