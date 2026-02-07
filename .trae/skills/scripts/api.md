## API 返回数据

- GET   {{url}}/api/v1/scripts

获取所有脚本列表

```
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "id": 1,
      "name": "install-docker-compose",
      "content": "#!/bin/bash\n#\n# 安装 Docker 和 Docker Compose 脚本\n# 支持 Ubuntu/Debian 和 CentOS/RHEL 系统\n#\n\nset -e\n\n# 颜色输出\nRED='\\033[0;31m'\nGREEN='\\033[0;32m'\nYELLOW='\\033[1;33m'\nNC='\\033[0m' # No Color\n\n# 日志函数\nlog_info() {\n    echo -e \"${GREEN}[INFO]${NC} $1\"\n}\n\nlog_warn() {\n    echo -e \"${YELLOW}[WARN]${NC} $1\"\n}\n\nlog_error() {\n    echo -e \"${RED}[ERROR]${NC} $1\"\n}\n\n# 检测操作系统类型\ndetect_os() {\n    if [ -f /etc/os-release ]; then\n        . /etc/os-release\n        OS=$ID\n        VERSION=$VERSION_ID\n    else\n        log_error \"无法检测操作系统类型\"\n        exit 1\n    fi\n    log_info \"检测到操作系统: $OS $VERSION\"\n}\n\n# 卸载旧版本\nuninstall_old() {\n    log_info \"检查并卸载旧版本 Docker...\"\n    if command -v docker &> /dev/null; then\n        case $OS in\n            ubuntu|debian)\n                sudo apt-get remove -y docker docker-engine docker.io containerd runc 2>/dev/null || true\n                ;;\n            centos|rhel|fedora)\n                sudo yum remove -y docker docker-client docker-client-latest docker-common \\\n                    docker-latest docker-latest-logrotate docker-logrotate docker-engine 2>/dev/null || true\n                ;;\n        esac\n        log_info \"旧版本 Docker 已卸载\"\n    else\n        log_info \"未检测到旧版本 Docker\"\n    fi\n}\n\n# 安装 Docker\ninstall_docker() {\n    log_info \"开始安装 Docker...\"\n\n    case $OS in\n        ubuntu|debian)\n            # Ubuntu/Debian 安装\n            sudo apt-get update\n            sudo apt-get install -y \\\n                ca-certificates \\\n                curl \\\n                gnupg \\\n                lsb-release\n\n            # 添加 Docker 官方 GPG 密钥\n            sudo mkdir -p /etc/apt/keyrings\n            curl -fsSL https://download.docker.com/linux/$OS/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg\n\n            # 设置 Docker 仓库\n            echo \\\n                \"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/$OS \\\n                $(. /etc/os-release && echo \"$VERSION_CODENAME\") stable\" | \\\n                sudo tee /etc/apt/sources.list.d/docker.list > /dev/null\n\n            # 安装 Docker\n            sudo apt-get update\n            sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin\n            ;;\n        centos|rhel|fedora)\n            # CentOS/RHEL/Fedora 安装\n            sudo yum install -y yum-utils\n            sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo\n            sudo yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin\n            ;;\n    esac\n    log_info \"Docker 安装完成\"\n}\n\n# 启动 Docker 服务\nstart_docker() {\n    log_info \"启动 Docker 服务...\"\n    sudo systemctl start docker\n    sudo systemctl enable docker\n    log_info \"Docker 服务已启动并设置为开机自启\"\n}\n\n# 验证安装\nverify_installation() {\n    log_info \"验证 Docker 安装...\"\n    if sudo docker run --rm hello-world; then\n        log_info \"Docker 安装验证成功！\"\n    else\n        log_error \"Docker 安装验证失败\"\n        exit 1\n    fi\n}\n\n# 主函数\nmain() {\n    detect_os\n    uninstall_old\n    install_docker\n    start_docker\n    verify_installation\n    log_info \"Docker 和 Docker Compose 安装完成！\"\n}\n\n# 执行主函数\nmain"
    },
    {
      "id": 2,
      "name": "test-loop",
      "content": "#!/bin/bash\n\nfor i in {1..10}\ndo\n  echo \"Loop $i\"\ndone"
    }
  ]
}
```

- GET   {{url}}/api/v1/scripts/1

获取指定 ID 的脚本详情

```
{
  "code": 0,
  "message": "success",
  "data": {
    "id": 1,
    "name": "install-docker-compose",
    "content": "#!/bin/bash\n#\n# 安装 Docker 和 Docker Compose 脚本\n# 支持 Ubuntu/Debian 和 CentOS/RHEL 系统\n#\n\nset -e\n\n# 颜色输出\nRED='\\033[0;31m'\nGREEN='\\033[0;32m'\nYELLOW='\\033[1;33m'\nNC='\\033[0m' # No Color\n\n# 日志函数\nlog_info() {\n    echo -e \"${GREEN}[INFO]${NC} $1\"\n}\n\nlog_warn() {\n    echo -e \"${YELLOW}[WARN]${NC} $1\"\n}\n\nlog_error() {\n    echo -e \"${RED}[ERROR]${NC} $1\"\n}\n\n# 检测操作系统类型\ndetect_os() {\n    if [ -f /etc/os-release ]; then\n        . /etc/os-release\n        OS=$ID\n        VERSION=$VERSION_ID\n    else\n        log_error \"无法检测操作系统类型\"\n        exit 1\n    fi\n    log_info \"检测到操作系统: $OS $VERSION\"\n}\n\n# 卸载旧版本\nuninstall_old() {\n    log_info \"检查并卸载旧版本 Docker...\"\n    if command -v docker &> /dev/null; then\n        case $OS in\n            ubuntu|debian)\n                sudo apt-get remove -y docker docker-engine docker.io containerd runc 2>/dev/null || true\n                ;;\n            centos|rhel|fedora)\n                sudo yum remove -y docker docker-client docker-client-latest docker-common \\\n                    docker-latest docker-latest-logrotate docker-logrotate docker-engine 2>/dev/null || true\n                ;;\n        esac\n        log_info \"旧版本 Docker 已卸载\"\n    else\n        log_info \"未检测到旧版本 Docker\"\n    fi\n}\n\n# 安装 Docker\ninstall_docker() {\n    log_info \"开始安装 Docker...\"\n\n    case $OS in\n        ubuntu|debian)\n            # Ubuntu/Debian 安装\n            sudo apt-get update\n            sudo apt-get install -y \\\n                ca-certificates \\\n                curl \\\n                gnupg \\\n                lsb-release\n\n            # 添加 Docker 官方 GPG 密钥\n            sudo mkdir -p /etc/apt/keyrings\n            curl -fsSL https://download.docker.com/linux/$OS/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg\n\n            # 设置 Docker 仓库\n            echo \\\n                \"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/$OS \\\n                $(. /etc/os-release && echo \"$VERSION_CODENAME\") stable\" | \\\n                sudo tee /etc/apt/sources.list.d/docker.list > /dev/null\n\n            # 安装 Docker\n            sudo apt-get update\n            sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin\n            ;;\n        centos|rhel|fedora)\n            # CentOS/RHEL/Fedora 安装\n            sudo yum install -y yum-utils\n            sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo\n            sudo yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin\n            ;;\n    esac\n    log_info \"Docker 安装完成\"\n}\n\n# 启动 Docker 服务\nstart_docker() {\n    log_info \"启动 Docker 服务...\"\n    sudo systemctl start docker\n    sudo systemctl enable docker\n    log_info \"Docker 服务已启动并设置为开机自启\"\n}\n\n# 验证安装\nverify_installation() {\n    log_info \"验证 Docker 安装...\"\n    if sudo docker run --rm hello-world; then\n        log_info \"Docker 安装验证成功！\"\n    else\n        log_error \"Docker 安装验证失败\"\n        exit 1\n    fi\n}\n\n# 主函数\nmain() {\n    detect_os\n    uninstall_old\n    install_docker\n    start_docker\n    verify_installation\n    log_info \"Docker 和 Docker Compose 安装完成！\"\n}\n\n# 执行主函数\nmain"
  }
}
```

- DELETE {{url}}/api/v1/scripts/1

删除指定 ID 的脚本

```
{
  "code": 0,
  "message": "success",
  "data": "success"
}
```

- POST {{url}}/api/v1/scripts

创建脚本

请求数据

```
{
  "name": "install-docker-compose",
  "content": "#!/bin/bash\n#\n# 安装 Docker 和 Docker Compose 脚本\n# 支持 Ubuntu/Debian 和 CentOS/RHEL 系统\n#\n\nset -e\n\n# 颜色输出\nRED='\\033[0;31m'\nGREEN='\\033[0;32m'\nYELLOW='\\033[1;33m'\nNC='\\033[0m' # No Color\n\n# 日志函数\nlog_info() {\n    echo -e \"${GREEN}[INFO]${NC} $1\"\n}\n\nlog_warn() {\n    echo -e \"${YELLOW}[WARN]${NC} $1\"\n}\n\nlog_error() {\n    echo -e \"${RED}[ERROR]${NC} $1\"\n}\n\n# 检测操作系统类型\ndetect_os() {\n    if [ -f /etc/os-release ]; then\n        . /etc/os-release\n        OS=$ID\n        VERSION=$VERSION_ID\n    else\n        log_error \"无法检测操作系统类型\"\n        exit 1\n    fi\n    log_info \"检测到操作系统: $OS $VERSION\"\n}\n\n# 卸载旧版本\nuninstall_old() {\n    log_info \"检查并卸载旧版本 Docker...\"\n    if command -v docker &> /dev/null; then\n        case $OS in\n            ubuntu|debian)\n                sudo apt-get remove -y docker docker-engine docker.io containerd runc 2>/dev/null || true\n                ;;\n            centos|rhel|fedora)\n                sudo yum remove -y docker docker-client docker-client-latest docker-common \\\n                    docker-latest docker-latest-logrotate docker-logrotate docker-engine 2>/dev/null || true\n                ;;\n        esac\n        log_info \"旧版本 Docker 已卸载\"\n    else\n        log_info \"未检测到旧版本 Docker\"\n    fi\n}\n\n# 安装 Docker\ninstall_docker() {\n    log_info \"开始安装 Docker...\"\n\n    case $OS in\n        ubuntu|debian)\n            # Ubuntu/Debian 安装\n            sudo apt-get update\n            sudo apt-get install -y \\\n                ca-certificates \\\n                curl \\\n                gnupg \\\n                lsb-release\n\n            # 添加 Docker 官方 GPG 密钥\n            sudo mkdir -p /etc/apt/keyrings\n            curl -fsSL https://download.docker.com/linux/$OS/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg\n\n            # 设置 Docker 仓库\n            echo \\\n                \"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/$OS \\\n                $(. /etc/os-release && echo \"$VERSION_CODENAME\") stable\" | \\\n                sudo tee /etc/apt/sources.list.d/docker.list > /dev/null\n\n            # 安装 Docker\n            sudo apt-get update\n            sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin\n            ;;\n        centos|rhel|fedora)\n            # CentOS/RHEL/Fedora 安装\n            sudo yum install -y yum-utils\n            sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo\n            sudo yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin\n            ;;\n    esac\n    log_info \"Docker 安装完成\"\n}\n\n# 启动 Docker 服务\nstart_docker() {\n    log_info \"启动 Docker 服务...\"\n    sudo systemctl start docker\n    sudo systemctl enable docker\n    log_info \"Docker 服务已启动并设置为开机自启\"\n}\n\n# 验证安装\nverify_installation() {\n    log_info \"验证 Docker 安装...\"\n    if sudo docker run --rm hello-world; then\n        log_info \"Docker 安装验证成功！\"\n    else\n        log_error \"Docker 安装验证失败\"\n        exit 1\n    fi\n}\n\n# 主函数\nmain() {\n    detect_os\n    uninstall_old\n    install_docker\n    start_docker\n    verify_installation\n    log_info \"Docker 和 Docker Compose 安装完成！\"\n}\n\n# 执行主函数\nmain"
}
```

返回数据

```
{
  "code": 0,
  "message": "success",
  "data": "success"
}
```

错误情况 - 脚本内容为空

```
{
  "code": 40001,
  "message": "invalid script content",
  "data": null
}
```

错误情况 - 脚本内容没有 shebang

```
{
  "code": 40001,
  "message": "invalid script content",
  "data": null
}
```

- POST    {{url}}/api/v1/scripts/1

更新脚本

请求数据

```
{
  "id": 1,
  "name": "install-docker-compose",
  "content": "#!/bin/bash\n#\n# 安装 Docker 和 Docker Compose 脚本 - 更新版本\n# 支持 Ubuntu/Debian 和 CentOS/RHEL 系统\n#\n\nset -e\n\necho 'This is an updated version of the Docker installation script'"
}
```

返回数据

```
{
  "code": 0,
  "message": "success",
  "data": "success"
}
```

- POST {{url}}/api/v1/scripts/execute

执行脚本

请求数据

```
{
  "script_id": 1,
  "server_id": 1
}
```

返回数据

```
{
  "code": 0,
  "message": "success",
  "data": "脚本执行任务已提交"
}
```

错误情况 - 脚本不存在

```
{
  "code": 40002,
  "message": "script not found",
  "data": null
}
```

错误情况 - 服务器不存在

```
{
  "code": 40003,
  "message": "server not found",
  "data": null
}
```

错误情况 - 脚本执行失败

```
{
  "code": 40004,
  "message": "script execution failed",
  "data": null
}
```

- GET {{url}}/api/v1/scripts/1/results

获取脚本执行结果

```
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "id": 1,
      "task_id": 1234567890,
      "script_id": 1,
      "server_id": 1,
      "server_ip": "192.168.1.100",
      "agent_port": 8080,
      "output": "Script output here...",
      "status": "success",
      "error_message": "",
      "created_at": "2024-01-01 12:00:00"
    },
    {
      "id": 2,
      "task_id": 1234567891,
      "script_id": 1,
      "server_id": 2,
      "server_ip": "192.168.1.101",
      "agent_port": 8080,
      "output": "",
      "status": "running",
      "error_message": "",
      "created_at": "2024-01-01 12:05:00"
    },
    {
      "id": 3,
      "task_id": 1234567892,
      "script_id": 1,
      "server_id": 3,
      "server_ip": "192.168.1.102",
      "agent_port": 8080,
      "output": "",
      "status": "failed",
      "error_message": "Connection timeout",
      "created_at": "2024-01-01 12:10:00"
    }
  ]
}
```

## API 错误码说明

| 错误码 | 说明 |
|--------|------|
| 0 | 成功 |
| 40001 | 无效的脚本内容（内容为空或没有 shebang） |
| 40002 | 脚本不存在 |
| 40003 | 服务器不存在 |
| 40004 | 脚本执行失败 |
