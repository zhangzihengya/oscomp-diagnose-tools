# diagnose-tools 工具使用说明

## 安装 diagnose-tools 工具

1､快速上手

  建议在 Linux 5.19 内核版本中进行实验。

  第一步、使用如下命令clone代码：

    git clone https://gitlab.eduxiji.net/202311664111382/project1466467-176202.git
    
  第二步、在diagnose-tools目录中运行如下命令初始化编译环境：
  
    sudo make devel        # 安装编译过程中需要的包
    
    make deps         # 编译依赖库，目前主要是编译java agent，以支持用户态java符号表解析
    
  第三步、编译工具：
  
    sudo make
    
    这一步实际上会完成rpm的安装，你也可以用如下命令分别完成相应的工作：
    
    make module       # 编译内核模块
    
    make tools        # 编译用户态命令行工具
    
    make java_agent   # 编译java agent
    
    make pkg          # 制作rpm安装包
    
## 安装和卸载KO

在使用模块功能之前，需要用如下命令安装KO模块：
```
diagnose-tools install
```
安装成功后，控制台有如下提示：
```
installed successfully
```

在使用完模块功能后，需要用如下命令卸载KO模块：
```
diagnose-tools uninstall
```
卸载成功后，控制台有如下提示：
```
uninstalled successfully
```

## 功能模块

目前，我们已经基本实现了开源工具diagnose-tools在高版本（Linux 5.19内核）中的复现和源码优化，以及在原有的功能基础上进行扩充，经汇总后的diagnose-tools工具介绍如下所示，一共17个子工具：

| 工具名称        | 功能描述                                                     |
| :-------------- | :----------------------------------------------------------- |
| 实用小工具pupil | 按照tid查询特定线程在主机上的PID/进程名称/进程链/堆栈等等。**基于此工具，我们又扩充了不同业务进程的画像功能** |
| sys-delay       | 监控syscall长时间运行引起调度不及时。间接引起系统Load高、业务RT高 |
| sys-cost        | 统计系统调用的次数及时间                                     |
| irq-delay       | 监控中断被延迟的时间                                         |
| irq-stats       | 统计中断/软中断执行次数及时间                                |
| irq-trace       | 跟踪系统中IRQ/定时器的执行                                   |
| load-monitor    | 监控系统Load值。每10ms查看一下系统当前Load，超过特定值时，输出任务堆栈。这个功能多次在线上抓到重大BUG。**基于此工具，我们又扩充了负载变化趋势图象功能和异常时间点的确定功能** |
| run-trace       | 监控进程在某一段时间段内，在用户态/内核态运行情况            |
| perf            | 对线程/进程进行性能采样，抓取用户态/内核态调用链             |
| kprobe          | 在内核任意函数中，利用kprobe监控其执行，并输出火焰图         |
| uprobe          | 在用户态应用程序中使用探针，在应用中挂接钩子                 |
| exit-monitor    | 监控任务退出。在退出时，打印任务的用户态堆栈信息             |
| mutex-monitor   | 监控长时间持有mutex的流程                                    |
| exec-monitor    | 监控进程调用exec系统调用创建新进程                           |
| alloc-top       | 统计内存分配数量，按序输出内存分配多的进程                   |
| high-order      | 监控分配大内存的调用链                                       |
| reboot          | 监控系统重启信息，打印出调用sys_reboot系统调用的进程名称以及进程链 |
