AaTempSpoof v11

更新重点：

1. 热管理重构
伪装温度时直接禁用对应 Thermal Zone，并把硬件 Trip Point 提高到 125°C，解决 8 Gen3 / 8 Elite 平台因 TSENS 中断导致的 irq 高占用、掉帧、锁大核问题。

2. 电量伪装
新增 cap_en / fake_cap，可自定义电量显示（伪装 capacity、chip_soc）。
新增 supersave_en，可绕过低电量强制关机和超级省电。

3. 运行优化
每 2 秒读取真实电流、电压同步到 WebUI。
优化 thermal zone 识别，避免误作用到 cam、ambient、skin 等区域。

4. 使用优化
WebUI 操作简化。
配置文件统一为 AaTempSpoof.txt。
模块退出自动恢复所有 Thermal 设置。

5. 自动伪装充电插拔（破除充电定时器）
充电且 电量 ≤80% 时：
每 180 秒将
/sys/devices/virtual/oplus_chg/battery/mmi_charging_enable
**0 → 1（间隔1.5秒）**模拟重新插充，保持快充功率。

总结：
全机型可用，已适配 8 Elite（8e）。