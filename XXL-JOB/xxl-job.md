# XXL-JOB 笔记

作者：Yan

---

## 一、📝 FRP 内网穿透 + XXL-JOB 执行器调试笔记

## 🔹 场景

- **调度中心（xxl-job-admin）**：部署在云服务器（公网可访问，端口 9002）。
- **执行器（SpringBoot 项目）**：运行在本地笔记本（监听 9999 端口）。
- **问题**：调度中心需要回调执行器，但笔记本通常没有公网 IP，云上无法直连本地 `9999`。
- **解决方案**：使用 **FRP 内网穿透**，在云服务器和本地之间建立一条隧道，把本机 `9999` 映射成云服务器的 `19999` 端口，供调度中心访问。

## 🔹 原因

1. **网络隔离**
   - 本地笔记本一般在 NAT/路由器后，没有公网 IP。
   - 云服务器无法直接访问到本机端口（9999）。
2. **调度中心访问机制**
   - 调度中心触发任务时，会根据执行器注册地址（`xxl.job.executor.address`）来请求执行器的 HTTP 服务。
   - 如果这个地址是内网 IP（如 `192.168.x.x`），云服务器根本访问不到 → 执行器离线/调度失败。
3. **FRP 的作用**
   - **frps**（服务端，跑在云服务器）监听固定端口（如 19999）。
   - **frpc**（客户端，跑在本机）主动连上 frps，把本机 `9999` 映射到云端 `19999`。
   - 外部（调度中心）访问 `http://云IP:19999` → frps → frpc → 本地 `9999`。
   - 相当于给本机执行器“套”了一个云端的公网端口。

## 🔹 解决方式

1. 创建`FRP`文件夹

```bash
mkdir -p /opt/frp
cd /opt/frp
```

2. 新建 `frps.ini`文件

```ini
[common]
bind_port = 7000
token = frp-very-secret

# 可选：Dashboard 面板
dashboard_port = 7500
dashboard_user = admin
dashboard_pwd = admin123

# 可选：限制只允许 19999 端口映射
allow_ports = 19999
```

3. Docker 启动FRP，在 `/opt/frp` 下执行：

```bash
docker run -d --name frps --restart unless-stopped \
  -p 7000:7000 -p 7500:7500 -p 19999:19999 \
  -v /opt/frp/frps.ini:/etc/frp/frps.ini \
  snowdreamtech/frps:latest -c /etc/frp/frps.ini
```

```java
/**
说明：
    -p 7000:7000 → frpc 客户端连 frps 用
    -p 7500:7500 → Dashboard（可选）
    -p 19999:19999 → 你本机执行器 9999 将被映射成云端 19999
    -v /opt/frp/frps.ini:/etc/frp/frps.ini → 把你写好的配置文件挂载进去	
*/
```

4. windows（本机）下载FRPC

> https://github.com/fatedier/frp/releases

5. 新建 `D:\frp\frpc.ini`（确保 token 与 frps.ini 相同）

```ini
[common]
server_addr = 49.233.93.83
server_port = 7000
token = frp-very-secret

[xxl-job-executor]
type = tcp
local_ip = 127.0.0.1
local_port = 9999
remote_port = 19999
```

6. 启动FRP

```bash
cd /d D:\frp
frpc.exe -c frpc.ini
```

7. 把执行器注册地址改为 云IP + remote_port：

```yaml
xxl:
  job:
    admin:
      addresses: http://49.233.93.83:9002/xxl-job-admin
      accessToken: pingzhuyan.test
    executor:
      appname: xxl-job-executor-sample
      address: http://49.233.93.83:19999   # ★ 关键：用云端的 19999
      ip:
      port: 9999                           # 本地仍监听 9999
      logpath: ./logs/xxl-job/jobhandler
      logretentiondays: 30
```

