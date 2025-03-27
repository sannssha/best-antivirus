# 使用雨云服务器搭建MCSM面板和我的世界服务器完整指南

本教程将详细介绍如何在雨云NAT云服务器上通过Docker部署MCSM面板，并搭建Minecraft Mohist 1.16.5版本服务器（其他版本可参考类似流程）。

## MCSM面板核心优势

- **分布式架构**：支持多物理服务器集中管理
- **全中文界面**：开箱即用，降低使用门槛
- **Docker支持**：轻松管理不同Java版本的MC服务器
- **安全权限系统**：完善的多用户权限控制

MCSManager（简称MCSM）是目前中文社区最流行的Minecraft服务端管理面板之一，特别适合需要同时管理多个游戏实例的用户。

## 服务器准备建议

👉 [【点击查看】2025年最新雨云优惠码及特价云服务器方案汇总](https://bit.ly/RainYun)

**硬件配置推荐**：
- 1.17+版本：建议5900X/13900K高主频CPU，4G+内存
- 1.16.5及以下：1核2G基础配置即可
- MOD服/多人联机：需相应提升配置

**系统选择**：
- 推荐使用Debian系统
- 默认登录账号：root
- 密码可在雨云控制台查看

> 注：游戏云区服务器默认提供15条端口映射，如需独立公网IP或150G高防服务可额外开通

## 详细搭建步骤

### 1. 端口映射配置
需映射以下关键端口：
- MCSM面板：23333、24444
- Minecraft服务端：25565

bash
# 关闭系统防火墙（确保安全组已配置）
systemctl stop firewalld
systemctl disable firewalld
service iptables stop

### 2. MCSM面板安装

**快速安装方案**：
bash
apt update && apt install -y wget git
wget -qO- https://gitee.com/mcsmanager/script/raw/master/setup.sh | bash

安装完成后：
- 启动服务：`systemctl start mcsm-{daemon,web}`
- 开机自启：`systemctl enable mcsm-{daemon,web}.service`
- 访问地址：`http://服务器IP:映射端口`

### 3. Docker环境部署
bash
# 安装Docker
apt install -y docker.io
systemctl enable --now docker

# 配置国内镜像源
cat > /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": ["https://docker.nju.edu.cn"]
}
EOF
systemctl restart docker

### 4. JDK镜像配置
根据MC版本选择对应JDK：
- 1.12.2及以下：JDK 8
- 1.16.5：需自定义JDK 11
- 1.17+：JDK 17

### 5. 服务端部署流程
1. 下载服务端核心（如mohist-1.16.5.jar）
2. 通过MCSM面板创建新实例
3. 上传核心文件并配置启动参数：
   bash
   java -Xmx4G -server -Dfile.encoding=UTF-8 -jar ${ProgramName}
   
4. 选择Docker虚拟化运行

## 常见问题处理

**EULA协议同意**：
- 通过控制台输入`true`
- 或修改eula.txt文件

**端口连接问题**：
- 确保server.properties中`online-mode`设置匹配验证方式
- 检查所有端口映射是否正确

## 游戏连接指南
客户端添加服务器时使用格式：
`服务器IP:映射端口`

> 提示：建议定期备份world文件夹，可通过MCSM的"文件管理"功能操作

通过本方案，您可以获得：
- 更安全的容器化运行环境
- 便捷的多实例管理能力
- 灵活的版本切换方案