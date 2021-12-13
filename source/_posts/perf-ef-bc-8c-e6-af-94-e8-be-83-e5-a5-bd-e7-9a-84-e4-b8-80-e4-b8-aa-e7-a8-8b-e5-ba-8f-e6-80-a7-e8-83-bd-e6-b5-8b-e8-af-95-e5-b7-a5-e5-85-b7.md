---
title: 'perf ，比较好的一个程序性能测试工具'
date: 2014-04-24 11:06:57
tags: []
published: true
hideInList: false
feature: 
isTop: false
categories: 开发工具
---

面对一个问题程序，最好采用自顶向下的策略。先整体看看该程序运行时各种统计事件的大概，再针对某些方向深入细节。而不要一下子扎进琐碎细节，会一叶障目的。

对于优化自己写的代码，cpu bound 型 和 IO bound 型是不一样的：

*   cpu bound 型：所谓cpu bound型指的是程序大部分时间都在使用CPU。
*   IO bound 型：由cpu bound型的定义就不难推出了。

### **perf stat 命令用于统计进程总体的信息**
```
        /*******************************************************************************/
        $ perf stat ./Joseph_ring
         Performance counter stats for './Joseph_ring':
        
                 19.755435 task-clock                #    0.000 CPUs utilized          
                       429 context-switches          #    0.022 M/sec                  
                         5 CPU-migrations            #    0.000 M/sec                  
                       137 page-faults               #    0.007 M/sec                  
                27,255,530 cycles                    #    1.380 GHz                    
             <not counted> stalled-cycles-frontend 
             <not counted> stalled-cycles-backend  
                 6,521,404 instructions              #    0.24  insns per cycle        
                 1,157,604 branches                  #   58.597 M/sec                  
                   161,429 branch-misses             #   13.95% of all branches        
        
             439.901478124 seconds time elapsed
        /*******************************************************************************/
```

task-clock：CPU 利用率，该值高，说明程序的多数时间花费在 CPU 计算上而非 IO。

Context-switches：进程切换次数，记录了程序运行过程中发生了多少次进程切换，频繁的进程切换是应该避免的。

Cache-misses：程序运行过程中总体的 cache 利用情况，如果该值过高，说明程序的 cache 利用不好

CPU-migrations：表示进程 t1 运行过程中发生了多少次 CPU 迁移，即被调度器从一个 CPU 转移到另外一个 CPU 上运行。

Cycles：处理器时钟，一条机器指令可能需要多个 cycles

Instructions: 机器指令数目。

IPC：是 Instructions/Cycles 的比值，该值越大越好，说明程序充分利用了处理器的特性。

branches：待查

branch misses：待查

### **perf top 命令可查看系统的实时信息**

例如系统中最耗时的内核函数或某个用户进程：
```
/*******************************************************************************/
        $perf top
        
                     samples  pcnt function                       DSO
        
                     _______ _____ ______________________________ __________________
        
        
        
                      133.00  9.4% system_call                    [kernel.kallsyms] 
        
                      126.00  8.9% __ticket_spin_lock             [kernel.kallsyms] 
        
                       49.00  3.5% schedule                       [kernel.kallsyms] 
        
                       49.00  3.5% __ticket_spin_unlock           [kernel.kallsyms] 
        
                       43.00  3.0% pthread_mutex_lock             libpthread-2.13.so
        
                       40.00  2.8% _nv027676rm                    [nvidia]          
        
                       40.00  2.8% __pthread_mutex_unlock_usercnt libpthread-2.13.so
        
                       38.00  2.7% __memset_sse2                  libc-2.13.so      
        
                       32.00  2.3% unix_poll                      [kernel.kallsyms] 
        
                       25.00  1.8% __sincos                       libm-2.13.so      
        
                       24.00  1.7% update_curr                    [kernel.kallsyms] 
        
                       24.00  1.7% sched_clock_local              [kernel.kallsyms] 
        
        /**********************************************************************************/
```   

### **perf record** 
    

记录单个函数级别的统计信息，并使用 perf report 来显示统计结果，以此可以找到热点：
```
        /**********************************************************************************/
        # Events: 46  cpu-clock
        
        #
        
        # Overhead      Command      Shared Object                  Symbol
        
        # ........  ...........  .................  ......................
        
        #
        
            63.04%  Joseph_ring  [kernel.kallsyms]  [k] 0xc10e7ef2
        
             6.52%  Joseph_ring  libc-2.13.so       [.] __GI_vfprintf
        
             4.35%  Joseph_ring  libc-2.13.so       [.] __printf
        
             4.35%  Joseph_ring  libc-2.13.so       [.] _IO_new_file_xsputn
        
             2.17%  Joseph_ring  libc-2.13.so       [.] _itoa_word
        
        /**********************************************************************************/
```

perf record -e cpu-clock -g 给出函数的调用关系，以便于找到次级热点：
```
        /**********************************************************************************/
        
        # Events: 47  cpu-clock
        
        #
        
        # Overhead      Command      Shared Object                  Symbol
        
        # ........  ...........  .................  ......................
        
        #
        
            72.34%  Joseph_ring  [kernel.kallsyms]  [k] 0xc11017a7
        
                    |
        
                    --- 0xc150a125
        
                       |          
        
                       |--33.33%-- 0xc105e3a7
        
                       |          0xc105e4f6
        
        
        /**********************************************************************************/
```