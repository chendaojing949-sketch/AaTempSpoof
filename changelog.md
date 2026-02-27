AaTempSpoof v10.1 更新内容
移除8e适配 稳定版

去掉的东西
charging_monitor.sh / mtk_spoof.sh / temp_daemon.sh 这几个脚本全砍了，逻辑全部塞进二进制和 service.sh 里，以后进程更干净
日用和充电两个配置文件合并成一个 Aa自定义配置.txt，放在 /data/adb/modules/AAaTempSpoof/ 下，改完 30 秒自动生效不用重启

新加的东西
正式加入去温控，之前只是骗数字，这版会直接关掉充电热控节点、禁用慢充、同时允许 PPS/UFCS 快充协议，充电速度拉满
service 启动时会自动扫所有 thermal 节点，记录哪些可写哪些不行，方便排查问题
守护进程现在会自动绑小核运行，省电
新增 post-fs-data 早期挂载，兼容性应该会好一点

配置变化
现在只开放 CPU / GPU / 内存 三个温度自定义，电池固定 36°C 不让改了，其余热区也固定 36°C，比之前简单很多

升级注意
安装时会自动清掉旧版 AaTempSpoof 和 AaTempdc 的残留，直接刷就行不用手动删