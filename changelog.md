# AaTempSpoof v12.0 更新日志
全局自定义伪装
---
## 新增

**color_tombstone 守护进程**
移植 Color OS 墓碑冻结功能。开关文件 `mubei.txt`（默认开启）。开启时通过 resetprop 写入目标属性，并对 cgroup frozen/unfrozen 目录写入 `cgroup.freeze=1`；关闭时还原快照的原始属性值。inotify 实时响应，失败降级轮询。

**极致伪装模式（jzwz.txt）**
开关文件 `jzwz.txt`（默认关闭）。开启时射频/modem 类 thermal zone 不再过滤，纳入 governor 禁用范围并写入 emul_temp；关闭时维持原有过滤行为。主循环动态轮询，变化时立即重新 discover 并应用，无需重启。

**亮屏实时响应**
自动检测背光节点路径，亮屏事件（背光 0→非零）触发时立即重施 governor 禁用、cooling device 清零和 emul_temp 写入，不等轮询周期。

**OnePlus 兼容模式（nooneplus 标记）**
安装时可选创建 `nooneplus` 标记文件。存在时 service.sh 跳过 cb、touch_daemon、color_tombstone 的启动与看门狗，适配非 OnePlus 设备。

## 主守护进程（main.c）

- 运行时临时文件（pid、fake\_\* 系列）迁移至独立 `pid/` 目录，与配置文件分离
- governor/cooling 清除和 `scaling_max_freq` 解锁周期从 60s 缩短至 10s；新增遍历 cpufreq policy 写回各 cluster 频率上限，防止热调频持续压低 gold+ 频率
- `thermal_kill()` 改为单次 `run_async()`，不再阻塞主线程
- 伪装停止时 `select()` 超时从 2s 延长至 10s，降低空转占用
- `master_off()` 补加 `unbind_cycle_count()`，修复关闭总开关时循环次数绑挂载未卸载的问题
- rebind 健康检测（tick%15）改调 `update_fake_batt()` 而非 `mount_batt()`，避免触发 `force_batt_reread` 导致反复出现充电动画
- `refresh_cap_spoof()` 关闭时先读真实电量再 unbind，unbind 后同步还原显示
- `dumpsys_batt_temp()` 移除 `set ac`/`set usb`，防止触发二次 `ACTION_POWER_CONNECTED`

## 插拔守护进程（cb.c）

- 新增模式 2：仅在检测到 OPLUS 快充协议（`fast_charge=1` 或 `voocchg_ing=1`）时执行插拔；模式 1 保持原有不限协议行为
- 新增充电状态变化、协议变化、每 5% 电量变化的状态日志
- PID 文件迁移至 `pid/` 目录，日志改为共用 `tempspoof.log`

## 触控守护进程（touch_daemon.c）

- `handle_charging()` 改为充电接入时实时快照 horae 当前值，修复充电期间手动修改 horae 后停充无法正确恢复的问题
- PID 文件迁移至 `pid/` 目录，日志改为共用 `tempspoof.log`

## service.sh

- 新增 `battery/temp` 有效值等待（最多 20s），确保驱动完全初始化后再启动守护进程
- `start_daemon()` 改为轮询 PID 文件验证进程存活，失败时 fallback pgrep
- 新增 20s 后台 SIGHUP 补发，处理节点延迟初始化导致 bind-mount 失败的情况
- 新增 60s 完整重初始化（--stop + --start），在 Android init 重启热管理服务稳定后重跑 master_on()，取得最终写入权

## 安装脚本（customize.sh）

- 新增市场名（MARKET）读取，设备信息界面补充显示
- 新增控制台名称选择：音量+ 使用「${设备名} 伪装控制台」，音量- 保持「AaTempSpoof」，结果写入 module.prop
- 新增 OnePlus 兼容模式选择（见上）
- 新增旧版配置迁移：检测到旧配置目录时提示，音量+ 复制所有 .txt/.conf 文件至新目录
- KsuWebUI 改为跳转浏览器下载页，移除内置 APK（包体积减小约 2MB）
