# 作用

配置了DMA的设备可以直接访问内存，访问的地址是IOVA，经过SMMU转化成为PA。

每个设备在SMMU里面各自有一个页表。

每个设备对应的一个或者多个StreamID，StreamID来找到对应页表。

# 组成

- TBU：有TLB，多个TBU
- TCU：地址转换单元，只有一个
- DTI：链接TBU到TCU

# 设备信息

对于PCIe设备，streamID[15:0]==RequestID[15:0]

非PCIe设备，通过设备树获取

# STE

STE：s2转换的页表基地址 + 指向了描述符CD表，每个CD中包含一个s1转换的页表基地址

# hvisor要做的

填充STE
