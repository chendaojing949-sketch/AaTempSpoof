v10.3 修复与优化内容：
Bug修复：手动关闭被 Watchdog 重启
新增 user_stopped 标志文件机制：
stop_service() 第一步就写入 $MODDIR/user_stopped
Watchdog 每次检测前先判断此文件是否存在，存在则跳过重启，只记录日志
start_service() 调用时删除此文件，Watchdog 恢复正常监控
开机时自动清除该文件（新会话默认自动启动伪装）
功耗优化
Watchdog 轮询间隔从 30s → 60s，减少一半系统唤醒次数
Watchdog 循环自身执行 renice +10，降低 CPU 优先级，不抢占前台资源
去掉冗余的 get_little_core_mask（效率核扫描在 Watchdog 循环中无意义）
充电速度增强
新增快充协议节点写入：pd_allow、vooc_allow、fast_chg_allow、voocphy_enable
新增 setprop：vooc.allow、voocphy.allow、svooc.allow、flash_charge.allow、powerhal.thermal.disabled