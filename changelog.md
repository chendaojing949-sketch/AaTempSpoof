AaTempSpoof v9.4

更新内容：

重写伪装核心逻辑，改为独立二进制接管 

尝试适配天机处理器等机型 action.sh增加充电显示和平台状态

新增充电模式 充电配置文件target_temp_charging.txt 

customize.sh刷入时自动检测平台状态 节点信息 检测到天机处理器发送标识 触发mtk专属伪装进程

伪装逻辑调整 更新配置识别是否合理

温度显示逻辑优化

CPU_REAL="--"; GPU_REAL="--"; MEM_REAL="--"; BATT_REAL="--"更改