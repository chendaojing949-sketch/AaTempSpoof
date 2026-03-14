# AaTempSpoof v11.6
全局多功能伪装温度模块

适用全机型 部分小米机型会出现usb充电
原因 模块开启全部充电接口 不影响快充和功率
##支持 Magisk / KernelSU / APatch（不支持 Delta）

## 这个模块做什么

用 bind-mount 和 emul_temp 节点伪装系统温度与电池信息，让热管控框架和 BatteryService 读到假数据，从而阻止温控降频、维持快充满功率。不修改任何系统分区，卸载后完全还原。

## 功能

**温度伪装**
自动枚举所有可写热区，按 CPU / GPU / 内存 / 电池 / 外设分类写入目标温度，同时禁用相关 governor 并清零 cooling device，防止系统从其他路径触发限流。MTK 设备额外解除 PPM 和 FPSGO 温控限制。

**电池节点伪装**
bind-mount 覆盖电池温度、BCC 参数（仅替换温度字段，其余保留）、循环次数（兼容 OPLUS 专有节点）、显示电量。每 30s 检测挂载是否仍然有效，掉挂自动重挂。

**SuperVOOC 握手保护**
检测充电器插拔事件，插入后开 16s 保护窗口，期间屏蔽 `dumpsys battery set`，避免干扰 SuperVOOC/WARP 握手导致功率锁死。

**充电插拔守护**
定时写 `mmi_charging_enable` 模拟拔插（断开 2s 后恢复），强制 OPLUS vooc 状态机复位重新握手，解决长时间充电后功率掉落的问题。触发间隔和电量上限可配置。

**触控采样率守护**
通过 `touchHidlTest` 写寄存器，支持 125 / 240 / 360 / 600Hz 共 7 档预设。用 inotify 监听配置文件，改完即时生效，不占 CPU。