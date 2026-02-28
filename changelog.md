v10.2 修复内容汇总：

Bug①：概率重启后不启动伪装
根因：service.sh 检测 daemon.pid 时只判断 /proc/$PID 目录存在，但重启后旧 PID 可能已被系统其他进程复用，导致误判为"守护进程已运行"→ exit 0，实际上什么都没启动。
修复：
读取 /proc/$PID/cmdline 验证进程名确实是 AaTempSpoof 才信任
开机时强制清理残留的 daemon.pid 和 fake_batt_temp 两个跨重启 stale 文件

Bug②：概率掉伪装
根因：service.sh 只在开机时运行一次，守护进程崩溃后没有任何机制重启它。
修复：在 service.sh 末尾加入 Watchdog 循环（取代原来的 exit 0）：
每 30 秒检测一次守护进程是否存活
检测逻辑同样带 cmdline 验证，防止 PID 复用误报
发现进程死亡后自动重启 + 重新 maximize_charging
连续失败超过 5 次停止自动重启（防止硬件异常时无限循环）