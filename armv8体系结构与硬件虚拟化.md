# 架构

- v7 出现了cortex-M R A处理器，支持硬件虚拟化
- v8 64位
- v9 兼容v8

# 特权等级 CPU的运行状态

- EL0 app
- EL1 guest
- EL2 hypervisor
- EL3
- 安全模式暂时不深究

# 寄存器

- 通用reg 31个 不包括sp
- pstate寄存器 一堆
- csr 系统寄存器

和hypervisor相关：

- HCR_EL2：中断路由
- SCTLR_ELx：MMU的开关
- TTBR0_ELx：地址翻译
- SPSR_ELx：硬件自动保存
- ELR_ELx：异常发生的地址
- ESR_ELx：异常发生的原因
- VBAR_ELx：异常向量表基地址

# GICv2

中断：

- SPI：虚拟设备、网卡，共享外设
- PPI：PE自己的私有中断 比如时钟
- SGI：核间中断 软件触发

GIC组成：

- GICD：接收并分发
- GICC：发到vCPU

GIC虚拟化：

- GICV：虚拟的GICC，直通给guest
- GICH：控制GIC虚拟化的功能，注入中断等等

# 虚拟化

- type1：裸机上的 比如bao
- type2：kvm qemu，vmware，在host os之上

## armv8提供的硬件虚拟化

- MMU
- GICV,GICH
- DMA：SMMU

## stage2地址翻译

VA IPA PA

## DMA SMMU

hypervisor：配置并解析guest的命令

（我猜是拦截guest对stage1的配置，做一个代理？

## 中断虚拟化

- 接管GICD GICC，配置GICH
- 模拟GICD，直通GICV作为GICC
- 设置HCR_EL2：中断都路由到EL2，后再注入

注入过程：

- 物理外设产生中断
- 收到、判断是谁的
- 通过GICH注入
- eret返回EL1，vCPU开始执行
- GIC向vCPU发出虚拟中断
