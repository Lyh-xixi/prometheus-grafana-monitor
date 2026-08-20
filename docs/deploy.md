# 部署步骤 Prometheus+Grafana+node_exporter
> 虚拟机环境 CentOS9

## 1.关闭防火墙/放行端口
```bash
firewall-cmd --add-port={3000,9090,9100}/tcp --permanent
firewall-cmd --reload
