AaTempSpoofv10.3 

优化内容：

功耗优化
Watchdog 轮询间隔 60s → 120s，系统唤醒次数再减一半
Watchdog 循环本体 renice +10，低优先级运行不抢占资源
Watchdog 检测到 STOP_FLAG 时完全跳过（零 I/O），不写日志
彻底移除从未被调用的 get_little_core_mask() 函数（每次调用要遍历所有 CPU 节点）
maximize_charging() 改为先读后写：节点值已正确时不写入，避免无效 sysfs I/O
service.sh 的 Thermal 节点扫描阶段去掉逐节点详细日志，只记录总数（减少大量 I/O）

充电速度提升
charging_*txt 温度阈值偏移量 +80 → +100（更激进）

新增充电节点：wired_boost_enable、stop_chg、super_endurance_mode（防省电限速）

快充协议节点覆盖到 customize 安装阶段和 service 运行时双重写入