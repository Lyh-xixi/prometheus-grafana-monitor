# 部署步骤 Prometheus+Grafana+node_exporter
> 虚拟机环境 CentOS9

## 1.关闭防火墙/放行端口
> 需要放行端口：3000(Grafana)、9090(Prometheus)、9100(node_exporter)
```bash
firewall-cmd --add-port={3000,9090,9100}/tcp --permanent
firewall-cmd --reload
```
## 2.部署Prometheus（二进制包）
### 下载解压
```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.53.0/prometheus-2.53.0.linux-amd64.tar.gz
tar -zxvf prometheus-2.53.0.linux-amd64.tar.gz
mv prometheus-2.53.0.linux-amd64 /usr/local/prometheus
cd /usr/local/prometheus
```
### 加载服务、启动、设置开机自启
```bash
systemctl daemon-reload
systemctl start prometheus
systemctl enable prometheus
```
### 查看状态
```bash
systemctl status prometheus
```
访问地址：http://虚拟机IP:9090

## 3.部署 node_exporter（采集主机硬件指标）
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
tar -zxvf node_exporter-1.8.2.linux-amd64.tar.gz
mv node_exporter-1.8.2.linux-amd64 /usr/local/node_exporter
cd /usr/local/node_exporter
```
### 加载服务、启动、设置开机自启
```bash
systemctl daemon-reload
systemctl start node_exporter
systemctl enable node_exporter
```
访问指标地址：http://虚拟机IP:9100/metrics

## 4. 修改 prometheus.yml 配置
修改虚拟机配置文件：`/usr/local/prometheus/prometheus.yml`，添加采集 job 任务。
配置文件参考本仓库：`config/prometheus.yml`

修改配置后重启服务生效：
```bash
systemctl restart prometheus
```

## 5. 部署 Grafana
### 安装grafana rpm包
```bash
wget https://dl.grafana.com/oss/release/grafana-11.2.0-1.x86_64.rpm
rpm -ivh grafana-11.2.0-1.x86_64.rpm

systemctl start grafana-server
systemctl enable grafana-server
```
访问地址：http://虚拟机IP:3000

## 6. Grafana 配置监控面板
1. 添加数据源：选择 Prometheus，填写地址 `http://127.0.0.1:9090`
2. 导入 Linux 主机监控 Dashboard 模板（ID：1860）
3. 选择 `linux‑node` 对应的 job，即可展示 CPU、内存、磁盘、网络监控图表

### 全部启动
```bash
systemctl start prometheus node_exporter grafana-server
```
### 全部停止
```bash
systemctl stop prometheus node_exporter grafana-server
```
### 查看各组件运行状态
```bash
systemctl status prometheus
systemctl status node_exporter
systemctl status grafana-server
```
