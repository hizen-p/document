# WSL 2 手动安装 Ubuntu 18.04

> Ubuntu 18.04 已结束标准支持，仅建议用于旧项目兼容环境。

## 1. 安装 WSL 2

使用管理员 PowerShell 执行：

```powershell
wsl --install --no-distribution
wsl --update
wsl --set-default-version 2
```

如果系统提示重启，重启 Windows 后继续。

## 2. 下载并导入 Ubuntu 18.04

<http://cdimage.ubuntu.com/ubuntu-base/releases/18.04/release/>

使用 PowerShell 执行：

```powershell
$Rootfs = "D:\Desktop\ubuntu-base-18.04.5-base-amd64.tar.gz"
$InstallDir = "D:\WSL\Ubuntu18"

curl.exe -L "https://cdimage.ubuntu.com/ubuntu-base/releases/18.04/release/ubuntu-base-18.04.5-base-amd64.tar.gz" -o $Rootfs
New-Item -ItemType Directory -Force -Path $InstallDir | Out-Null

wsl --import Ubuntu18.04 $InstallDir $Rootfs --version 2
wsl --list --verbose
wsl --distribution Ubuntu18.04
```

## 3. 配置清华软件源

进入 Ubuntu 后，以 `root` 身份执行：

```bash
[ -f /etc/apt/sources.list ] && cp /etc/apt/sources.list /etc/apt/sources.list.bak

cat > /etc/apt/sources.list <<'EOF'
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
EOF

apt-get clean
apt-get update
apt-get install -y sudo ca-certificates locales curl wget git build-essential bison flex gawk
```

## 4. 创建用户并设置密码

将第一行的 `dev` 改为需要创建的用户名，然后执行：

```bash
USERNAME=dev

useradd -m -s /bin/bash "$USERNAME"
passwd "$USERNAME"
usermod -aG sudo "$USERNAME"

cat > /etc/wsl.conf <<EOF
[user]
default=$USERNAME
EOF

exit
```

## 5. 重启并验证

回到 PowerShell 执行：

```powershell
wsl --terminate Ubuntu18.04
wsl --distribution Ubuntu18.04
```

进入 Ubuntu 后验证：

```bash
whoami
cat /etc/os-release
sudo apt-get update
```
