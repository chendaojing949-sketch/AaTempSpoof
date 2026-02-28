AaTempSpoof_v10.4

1. 核心功能增强 (service.sh)
​卸载逻辑优化：在 uninstall.sh 中增加了对 horae 服务的恢复逻辑，以及对 /data/vendor/thermal 目录及其配置的属性清理（使用 chattr -i），确保模块卸载后系统环境能更彻底地恢复原状。
​状态验证改进：在服务启动过程中增加了更详细的 thermal_zone 状态验证，会记录模拟温度（emul_temp）与实际温度（temp）的对比日志，方便排查伪装是否生效。
​2. 充电优化 (service.sh / customize.sh)
​充电路径覆盖更广：在 maximize_charging 函数中，新增了对更多充电控制节点的遍历，例如 /sys/class/hw_power/charger/charge_data/enable_charger 等，以进一步提升全机型的充电限速解锁能力。
​Oppo/OnePlus 专项优化：明确增加了对 persist.vendor.oplus.charge.cooldown 等属性的重置，旨在最大化这些设备的充电速度。
​3. 安装与兼容性 (customize.sh)
​严重的平台警告：针对 骁龙 8 至尊版 (SM8750) 增加了极其明显的红色警告信息，明确指出该平台目前完全不适配，强行安装可能导致 IRQ 中断风暴、系统温控失效及硬件损坏风险。
​清理逻辑升级：安装脚本现在会自动清理旧版本的配置文件（如 AaTempdc 或旧路径下的自定义配置），防止版本冲突。
​4. 界面与显示 (action.sh)
​UI 小版本号更新：终端操作界面（Action 界面）的标题已更新为 AaTempSpoof v10.4。
​状态反馈更精准：优化了守护进程（Daemon）的检测逻辑，使 action.sh 显示的状态信息与后台实际运行情况更一致。