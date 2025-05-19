---
title: GPU虚拟化技术系列一：设备虚拟化基础
date: 2025-03-29 21:10:57
tags: [云原生,云计算,AIInfra,GPU]
published: false
hideInList: false
feature: 
isTop: false
categories: AI
---

# 虚拟化基础

虚拟化技术的核心在于通过软件抽象层（VMM/Hypervisor）将物理硬件资源虚拟化为多个隔离的虚拟环境。要实现高效、安全的虚拟化，必须解决以下三大核心问题：

## CPU虚拟化：指令特权级的挑战

### 问题本质
- **特权指令困境**：x86架构CPU设计时未考虑虚拟化需求，存在17条敏感指令（如`LGDT`、`POPF`）在非特权模式下执行时不会触发异常，但会改变系统关键状态。
- **权限隔离需求**：需要让Guest OS运行在非特权环（Ring 1/3），同时保证Hypervisor对硬件的完全控制（Ring 0）。

### 解决方案演进
| **技术方案**       | **原理**                                                                 | **优缺点**                                                                 |
|--------------------|--------------------------------------------------------------------------|----------------------------------------------------------------------------|
| **全虚拟化**       | 通过二进制翻译（BT）动态替换敏感指令                                     | 兼容性好（无需修改Guest OS），但性能损失大（如VMware Workstation早期版本） |
| **半虚拟化**       | 修改Guest OS内核，通过Hypercall主动调用Hypervisor                       | 性能高（如Xen），但需定制OS（Windows不支持）                              |
| **硬件辅助虚拟化** | CPU新增虚拟化指令集（Intel VT-x/AMD-V），引入Root/Non-Root模式           | 原生支持特权隔离（VMXON指令进入虚拟化模式），性能接近物理机（现代主流方案）|

### 关键技术实现
- **VT-x扩展**：
  - **VMCS（虚拟机控制结构）**：保存/恢复CPU状态（每个vCPU对应一个VMCS）
  - **EPT（扩展页表）**：加速Guest物理地址→主机物理地址转换
- **多核调度**：
  - **vCPU绑定物理核**：避免跨核迁移导致的TLB刷新
  - **NUMA感知调度**：优化跨NUMA节点的vCPU分配

## 内存虚拟化：地址转换的嵌套难题

### 问题复杂性
- **双重地址转换**：
  ```
  Guest虚拟地址(GVA) → Guest物理地址(GPA) → 主机物理地址(HPA)
  ```
- **TLB一致性**：需要维护多级地址映射的缓存一致性

### 技术方案对比
| **实现方式**       | **工作原理**                                                                 | **性能影响**                              |
|--------------------|-----------------------------------------------------------------------------|------------------------------------------|
| **影子页表**       | Hypervisor为每个VM维护GVA→HPA的映射表                                        | 每次页表更新需陷入Hypervisor（开销大）   |
| **硬件辅助（EPT/NPT）** | CPU直接处理GVA→GPA→HPA转换（Intel EPT/AMD NPT）                            | 减少VM-exit次数（性能提升50%+）          |
| **大页（Huge Page）支持** | 使用2MB/1GB大页减少TLB Miss（需Guest和主机协同）                           | 降低地址转换开销（尤其对数据库负载有益） |

### 内存管理优化
- **Balloon驱动**：动态回收Guest闲置内存（如VMware Tools内置）
- **内存去重**：通过哈希比对合并相同内存页（KSM技术）
- **内存热插拔**：运行时调整VM内存配额（需Guest OS支持）

## 设备虚拟化：I/O性能的瓶颈突破

### 虚拟化层次
| **实现层级**       | **延迟水平**       | **典型场景**                              |
|--------------------|-------------------|------------------------------------------|
| **全模拟设备**     | 微秒级（μs）      | 兼容旧设备（如QEMU模拟的e1000网卡）       |
| **半虚拟化驱动**   | 亚微秒级（100ns） | 高性能场景（如Virtio-net/virtio-blk）     |
| **硬件直通（PCIe Passthrough）** | 纳秒级（ns）      | GPU虚拟化、NFV（如SR-IOV网卡）           |

### 关键技术
- **Virtio架构**：
  - **前端驱动**：Guest OS中的标准化驱动（如`virtio-scsi`）
  - **后端设备**：Hypervisor实现的虚拟设备（共享内存通信）
  - **vring队列**：基于环形缓冲区的异步I/O机制
- **SR-IOV（单根I/O虚拟化）**：
  - 物理设备虚拟化为多个VF（Virtual Function）
  - 每个VF可直接分配给不同VM（如NVIDIA GPU vGPU）
- **设备扩展协议**：
  - **vDPA（vhost Data Path Acceleration）**：将数据面卸载到硬件（如智能网卡）
  - **VFIO（Virtual Function I/O）**：安全访问直通设备的用户态框架

# QEMU/KVM架构图示

<img src="/images/qemu.png" />
<img src="/images/qemu_kvm.svg" />

- 虚拟机监管器(Virtual Machine Monitor)，也称为Hypervisor。QEMU/KVM就是一种VMM。
- CPU支持Root和Non-Root模式，宿主机运行于Root模式，虚拟机运行于Non-Root模式
- Root模式和Non-Root模式都有各自的Ring0-3特权级
- VMCS（Virtual Machine Control Structure，虚拟机控制结构）是Intel VT-x硬件虚拟化技术的核心数据结构，用于管理虚拟机的运行状态和切换流程
  - 上下文管理：保存虚拟机（Guest）和虚拟机监管器（Hypervisor）的 CPU 状态，包括寄存器、控制标志等。
  - 行为控制：定义虚拟机运行时的权限、中断处理、I/O 访问等规则，决定何时触发 VM Exit（退出到 Hypervisor）。
  - 状态切换：在 VM Entry（进入虚拟机）和 VM Exit 时，自动加载或保存上下文，实现虚拟机与 Hypervisor 的高效切换。
- QEMU位于用户空间负责设备模拟，KVM位于内核空间负责CPU和内存虚拟化。QEMU通过ioctl系统调用调用KVM的API。


# 实现设备虚拟化的三种方式

<img src="/images/qemu_dev_types.webp">

## 纯软件的设备模拟

纯软件模拟（全虚拟化）通过虚拟机监控器（VMM/Hypervisor）完全以软件形式模拟硬件设备的行为，客户机操作系统无需感知虚拟化环境。以下是其核心实现原理与技术细节：

- 陷阱与模拟（Trap-and-Emulate）

  - 特权指令截获：当客户机尝试执行特权指令（如 IN/OUT 访问 I/O 端口）时，触发 CPU 异常（如 #GP），VMM 捕获该异常并模拟设备响应。
  - 内存映射拦截：客户机访问设备内存区域时，VMM 通过内存管理单元（MMU）拦截操作，转发到虚拟设备的处理逻辑。

- 设备状态虚拟化

  - 寄存器模拟：为每个虚拟设备维护虚拟寄存器状态（如控制寄存器、状态寄存器），记录客户机的读写操作。
  - 中断模拟：当虚拟设备需通知客户机时（如数据到达），VMM 注入虚拟中断（如通过 KVM_IRQ_LINE 通知 KVM）。

- 数据通路仿真

  - I/O 请求处理：客户机的 I/O 请求（如磁盘读写）被 VMM 捕获，转发到宿主机文件或网络接口。
  - DMA 模拟：模拟直接内存访问（DMA）操作，维护客户机物理地址到宿主机物理地址的映射。

### 虚拟机（VM）中运行的代码

``` x86asm
start:
mov     $0x48, %al
outb    %al, $0xf1
mov     $0x65, %al
outb    %al, $0xf1
mov     $0x6c, %al
outb    %al, $0xf1
mov     $0x6c, %al
outb    %al, $0xf1
mov     $0x6f, %al
outb    %al, $0xf1
mov     $0x0a, %al
outb    %al, $0xf1

hlt
```
VM中没有操作系统，直接运行一段汇编代码。这段代码的功能是向端口`0xf1`发送字符串`"Hello\n"`，然后通过`hlt`指令使处理器停机。
`outb %al, $0xf1` 指令将AL寄存器中的字节数据通过OUT指令发送到端口`0xf1`。每个mov指令将ASCII字符的十六进制值加载到AL寄存器。
发送的字节序列依次为：
0x48 → H
0x65 → e
0x6c → l
0x6c → l
0x6f → o
0x0a → 换行符（\n）

组合后字符串为 "Hello\n"。

`hlt`指令停止处理器执行，进入休眠状态（通常用于程序终止）。

### VMM实现代码
``` c
# include <stdio.h>
# include <stdlib.h>
# include <string.h>
# include <sys/types.h>
# include <sys/stat.h>
# include <fcntl.h>
# include <sys/ioctl.h>
# include <sys/mman.h>
# include <linux/kvm.h>
# include <linux/kvm_para.h>
# include <unistd.h>

int main() {
    struct kvm_sregs sregs;
    int ret;

    int kvm_fd = open("/dev/kvm", O_RDWR);
    ret = ioctl(kvm_fd, KVM_GET_API_VERSION, NULL);
    printf("KVM API version: %d\n", ret);

    // 创建虚拟机
    int vm_fd = ioctl(kvm_fd, KVM_CREATE_VM, 0);

    // VMM用调用mmap申请一段内存用作虚拟机的虚拟内存
    unsigned char *ram = mmap(NULL, 0x1000, PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);

    // 将虚拟机中运行的程序加载到虚拟内存中
    int code = open("vm.bin", O_RDONLY);
    read(code, ram, 0x1000);

    // 为虚拟机配置内存区域
    struct kvm_userspace_memory_region region = {
        .slot = 0,
        .guest_phys_addr = 0,
        .memory_size = 0x1000,
        .userspace_addr = (unsigned long)ram,
    };
    ioctl(vm_fd, KVM_SET_USER_MEMORY_REGION, &region);

    // 创建虚拟CPU，KVM为vCPU分配VMCS结构，利用mmap将CPU信息映射到run起始的内存区域
    // 后续可以通过run来获取到VM_EXIT的原因
    int vcpu_fd = ioctl(vm_fd, KVM_CREATE_VCPU, 0);
    int mmap_size = ioctl(kvm_fd, KVM_GET_VCPU_MMAP_SIZE, NULL);
    struct kvm_run *run = mmap(NULL, mmap_size, PROT_READ | PROT_WRITE, MAP_SHARED, vcpu_fd, 0);

    // 将vCPU的代码段基地址及偏移量设置为0，即vm.bin所在的地址
    ret = ioctl(vcpu_fd, KVM_GET_SREGS, &sregs);
    sregs.cs.base = 0;
    sregs.cs.selector = 0;
    ioctl(vcpu_fd, KVM_SET_SREGS, &sregs);

    // 将指令指针寄存器设置为0，即vm.bin的起始地址
    struct kvm_regs regs = {
        .rip = 0,
    };
    ioctl(vcpu_fd, KVM_SET_REGS, &regs);

    while (1) {
        ioctl(vcpu_fd, KVM_RUN, NULL); // 进入Non-Root模式，运行虚拟机程序
        switch (run->exit_reason) { // 进入Root模式，捕获VM_EXIT的原因
            case KVM_EXIT_HLT:
                printf("KVM_EXIT_HLT\n");
                return 0;
            case KVM_EXIT_IO:
                if (run->io.direction == KVM_EXIT_IO_OUT && run->io.port == 0xf1 && run->io.size == 1) {
                    printf("%c", *((char *)run + run->io.data_offset));
                }
                break;
            default:
                printf("exit_reason: %d\n", run->exit_reason);
                return 1;
        }
    }
}
```

执行结果：

``` bash
$ gcc vmm.c -o vmm && ./vmm
KVM API version: 12
Hello
KVM_EXIT_HLT
```

这段代码实现了一个极简的KVM虚拟机监控程序（VMM），用于加载并运行一个预编译的虚拟机镜像（vm.bin），并处理虚拟机的I/O操作和停机指令。其核心功能如下：

1. KVM 环境初始化

- 打开`/dev/kvm`设备，获取KVM API版本。
- 创建虚拟机（KVM_CREATE_VM）和虚拟 CPU（KVM_CREATE_VCPU）。
- 通过 mmap 分配 4KB 内存（0x1000），并将 vm.bin 文件内容加载到该内存区域。
- 设置虚拟机内存映射（KVM_SET_USER_MEMORY_REGION），将客户机物理地址 0 映射到宿主机用户空间内存。

2. 虚拟机CPU配置

- 初始化虚拟CPU的段寄存器（KVM_GET_SREGS/KVM_SET_SREGS），将代码段（cs）基址设为 0。
- 设置指令指针寄存器 rip 为 0，使虚拟机从内存地址 0 开始执行代码。

3. 虚拟机运行与事件处理

- 通过 KVM_RUN 启动虚拟机执行，进入事件循环。
- 处理退出原因：
    - KVM_EXIT_HLT：当虚拟机执行 hlt 指令时，输出日志并正常退出。
    - KVM_EXIT_IO：监控虚拟机对端口 0xf1 的输出操作（OUT 指令），打印发送的字符（如 Hello\n）。
    - 其他退出原因：输出错误信息并终止。

## VirtIO设备模拟

## 设备直通(PCI(e) Passthrough)

# 参考文献

1. Qemu官方文档： https://wiki.qemu.org/Documentation/Architecture
2. 《QEMU/KVM源码解析与应用》 李强
