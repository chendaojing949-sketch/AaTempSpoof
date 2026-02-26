AaTempSpoof v9.6

🔴 IRQ 爆满 / 卡顿根因与修复（service.sh 完全重写）
根因：守护进程以默认 SCHED_OTHER 优先级高频轮询 emul_temp 节点 → 触发 thermal 驱动大量内核中断 → irq/kworker 进程 CPU 飙升 → 输入事件被延迟 → 卡顿。8gen3/8Elite 的 thermal 中断路由比旧平台更敏感，所以新芯片上症状更严重。
修复：启动后自动检测效率核（读 cpuinfo_max_freq 最低频率核），然后对守护进程施加：
chrt -i（SCHED_IDLE，内核级最低调度类，不抢占任何进程）
renice +19（用户空间最低优先级）
ionice -c 3（I/O idle，sysfs 写入不抢 I/O）
taskset 绑定到效率核（减少大核 IRQ 路由）
写入 cpuset/background/tasks（调度器级隔离）
新增适配（+10 个高通平台代号）
pineapple(8Gen3)、sun(8Elite)、cliffs(7+Gen3)、crow、kona(865/870)、lahaina(888)、shima、yupik、anorak、ravelin
