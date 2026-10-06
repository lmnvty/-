放宽了啥
提高手机性能50度以下cpu不会降频
性能开销0.338几乎可以忽略不计
满血充电什么的
小米红米通用

项目	节点	改法
CPU 免温控上限	thermal_message/cpu_nolimit_temp	48000 改 50000

      WiFi 过热降速	thermal_message/wifi_limit	1 改 0
      温控降亮度	thermal_message/thermal_max_brightness	1 改 0
      背光	backlight/.../brightness	拉满 4095
      商店下载限速	thermal_message/market_download_limit	1 改 0
      充电温度限流	cooling_device/battery/cur_state	拉到 max_state

充电那条只是去掉温度对电流的压制，硬件本身的电流电压上限没动

怎么维持的

不是无脑写，是先读再写

亮屏的时候检查一次，过 5 秒再检查一次，之后每 90 秒检查一次
灭屏就每 120 秒只看充电和 CPU，亮度不管，省点电

每次检查就判断一件事：值还是模块设的就不管，被原厂改回去了就写回来

所以日志平时是空的，只有真被改回去才会记一行

安装

KernelSU / Magisk 管理器里选本地安装，然后重启

自检

----sh-----
su -c "sh /data/adb/modules/thermal_relax/test.sh"

调参

改 config.sh，重启生效

----sh-----
NOLIMIT_TEMP=50000   # 45000 温和 / 48000 原厂 / 50000 推荐 / 52000 激进
RELAX_WIFI=1
RELAX_BRIGHT=1
RELAX_MARKET=1
RELAX_CHARGE=1
ON_INTERVAL=90
FIRST_REWRITE_DELAY=5
OFF_POLL=120

提醒

亮度和充电是内核 cooling device，原厂守护会回写，靠模块检查后重写顶住
