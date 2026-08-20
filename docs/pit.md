# 项目踩坑记录 & 问题排查

## 坑1：Grafana导入1860模板Load失败，无法访问grafana.com
现象：输入1860点击Load没有反应，网络不通，虚拟机无法访问外网grafana.com
原因：虚拟机网络环境限制，无法访问外网grafana模板网站
解决：
1. 可以手动下载dashboard json文件本地上传导入；
2. 实验验证数据源连通成功即可，不强制在线导入。

##坑2：Grafana部分面板显示No data，没有监控数据
现象：部分仪表盘有No data，一部分图表有数据
排查步骤：
1. 检查node_exporter服务状态 systemctl status node_exporter，确认服务正常运行，9100端口监听
2. 打开prometheus targets页面，确认job `linux-node`状态为UP，如果DOWN代表采集失败
3. 检查prometheus.yml配置targets地址是否正确，重启prometheus服务。

##坑3：prometheus target显示DOWN
排查思路：
1. 先看exporter服务是否启动，端口是否监听 ss -tlnp
2. 防火墙端口是否放行
3. prometheus配置文件yaml缩进错误！yaml对缩进极其严格，缩进错误直接服务启动失败。

##坑4：prometheus启动失败
最常见：prometheus.yml yaml格式缩进错误，yaml不允许tab，只能空格。
排查命令：`promtool check config prometheus.yml` 校验配置语法。

##学习感悟
1. yaml配置缩进是高频踩坑点；
2. prometheus是pull拉模式，如果exporter没启动、端口不通就拿不到指标；
3. 出问题优先看服务日志 `journalctl -u prometheus`，不要盲目复制命令。
