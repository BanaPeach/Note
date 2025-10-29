# Docker 笔记

作者：Yan

## 一、Docker 安装和配置

> 参考资料：
>
> https://blog.csdn.net/weixin_42541479/article/details/146209159
>
> https://blog.csdn.net/JR521314/article/details/149915853

1.  确保yum包更新到最新

```bash
sudo yum update
```

2.  安装docker相关的yum工具包

```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

3.  配置docker的yum软件包的清华国内源

```bash
# 配置清华源镜像
sudo yum-config-manager \
        --add-repo \
        https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/docker-ce.repo
sudo sed -i 's|https://download.docker.com|https://mirrors.tuna.tsinghua.edu.cn/docker-ce|g' /etc/yum.repos.d/docker-ce.repo
# 重构缓存
sudo yum makecache fast
```

4. 安装docker

```bash
sudo yum install -y docker-ce
```

5. 配置镜像加速

- 编辑 `/etc/docker/daemon.json` 文件：

```bash
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": [
    "https://alzgoonw.mirror.aliyuncs.com",
    "https://docker.m.daocloud.io",
    "https://dockerhub.icu",
    "https://docker.anyhub.us.kg",
    "https://docker.1panel.live"
  ]
}

EOF

```

- 重新加载`deamon.json`配置文件

```bash
sudo systemctl daemon-reload
```

- 重启Docker

```
sudo systemctl restart docker
```

