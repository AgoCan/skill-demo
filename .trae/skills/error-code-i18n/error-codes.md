# 错误码速查表

## 目录

- [Server 模块 (60000-60059)](#server-模块-60000-60059)
- [Config 模块 (65000-65019)](#config-模块-65000-65019)
- [Auth 模块 (66000-66019)](#auth-模块-66000-66019)
- [Application 模块 (71000-71019)](#application-模块-71000-71019)
- [Deployment 模块 (72000-72039)](#deployment-模块-72000-72039)
- [AppStore 模块 (73000-73019)](#appstore-模块-73000-73019)
- [Script 模块 (80000-80039)](#script-模块-80000-80039)
- [Monitor 模块 (81000-81019)](#monitor-模块-81000-81019)

## Server 模块 (60000-60059)

| 错误码 | 中文 | 英文 |
|--------|------|------|
| 60001 | 服务器未找到 | Server not found |
| 60002 | 服务器别名已存在 | Server alias already exists |
| 60003 | 通过UUID未找到服务器 | Server not found by UUID |
| 60004 | 服务器已存在 | Server already exists |
| 60005 | 服务器更新失败 | Server update failed |
| 60006 | 服务器删除失败 | Server delete failed |
| 60021 | 无效的参数 | Invalid parameter |
| 60022 | 无效的认证类型 | Invalid auth type |
| 60023 | 无效的SSH配置 | Invalid SSH configuration |
| 60024 | SSH连接测试失败 | SSH connection test failed |
| 60041 | 连接失败 | Connect failed |
| 60042 | Agent离线 | Agent is offline |
| 60043 | Agent请求失败 | Agent request failed |

## Config 模块 (65000-65019)

| 错误码 | 中文 | 英文 |
|--------|------|------|
| 65001 | 配置未找到 | Config not found |
| 65002 | 配置键已存在 | Config key already exists |
| 65003 | 无效的配置键 | Invalid config key |
| 65004 | 无效的配置值 | Invalid config value |
| 65005 | 配置更新失败 | Config update failed |
| 65006 | 配置删除失败 | Config delete failed |

## Auth 模块 (66000-66019)

| 错误码 | 中文 | 英文 |
|--------|------|------|
| 66001 | 认证失败 | Authentication failed |
| 66002 | 用户名或密码无效 | Invalid username or password |
| 66003 | 生成Token失败 | Failed to generate token |
| 66004 | 无效的Token | Invalid token |
| 66005 | Token已过期 | Token expired |

## Application 模块 (71000-71019)

| 错误码 | 中文 | 英文 |
|--------|------|------|
| 71001 | 应用未找到 | Application not found |
| 71002 | 应用已存在 | Application already exists |
| 71003 | 无效的应用名称 | Invalid application name |
| 71004 | 无效的应用类型 | Invalid application type |
| 71005 | 无效的应用配置 | Invalid application configuration |
| 71006 | 应用更新失败 | Application update failed |
| 71007 | 应用删除失败 | Application delete failed |

## Deployment 模块 (72000-72039)

| 错误码 | 中文 | 英文 |
|--------|------|------|
| 72001 | 部署未找到 | Deployment not found |
| 72002 | 应用已部署到此服务器 | Application already deployed to this server |
| 72003 | 应用未部署到此服务器 | Application not deployed to this server |
| 72004 | 生成部署ID失败 | Failed to generate deploy ID |
| 72005 | 创建部署记录失败 | Failed to create deployment record |
| 72006 | 无效的部署配置 | Invalid deployment configuration |
| 72007 | docker-compose容器名称冲突 | Docker-compose container name conflict detected |
| 72008 | docker-compose端口冲突 | Docker-compose port conflict detected |
| 72009 | docker-compose卷冲突 | Docker-compose volume conflict detected |
| 72010 | docker-compose网络冲突 | Docker-compose network conflict detected |
| 72021 | 发送请求到Agent失败 | Failed to send request to agent |
| 72022 | 解析Agent响应失败 | Failed to parse agent response |
| 72023 | Agent部署失败 | Agent deployment failed |
| 72024 | Agent删除应用失败 | Agent delete application failed |
| 72025 | Agent停止应用失败 | Agent stop application failed |
| 72026 | Agent启动应用失败 | Agent start application failed |
| 72027 | Agent操作失败 | Agent operation failed |

## AppStore 模块 (73000-73019)

| 错误码 | 中文 | 英文 |
|--------|------|------|
| 73001 | 应用商店未找到 | Application store not found |
| 73002 | 应用商店已存在 | Application store already exists |
| 73003 | 无效的compose内容 | Invalid compose content |
| 73004 | 不支持的应用类型 | Unsupported application type |
| 73005 | 无效的应用商店配置 | Invalid app store configuration |
| 73006 | 应用商店更新失败 | App store update failed |
| 73007 | 应用商店删除失败 | App store delete failed |

## Script 模块 (80000-80039)

| 错误码 | 中文 | 英文 |
|--------|------|------|
| 80001 | 脚本未找到 | Script not found |
| 80002 | 脚本已存在 | Script already exists |
| 80003 | 无效的脚本内容 | Invalid script content |
| 80004 | 不支持的脚本类型 | Unsupported script type |
| 80005 | 脚本未部署 | Script not deployed |
| 80021 | 脚本执行失败 | Script execution failed |
| 80022 | 脚本执行超时 | Script execution timeout |
| 80023 | 服务器未找到 | Server not found |

## Monitor 模块 (81000-81019)

| 错误码 | 中文 | 英文 |
|--------|------|------|
| 81001 | 监控请求失败 | Monitor request failed |
| 81002 | 无效的监控配置 | Invalid monitor configuration |
| 81003 | 监控数据未找到 | Monitor data not found |
| 81004 | 服务器未找到 | Server not found |

## 后端错误码定义位置

| 模块 | 文件路径 |
|------|----------|
| Server | `internal/squ-apiserver/handler/server/res/response_code.go` |
| Config | `internal/squ-apiserver/handler/config/res/response_code.go` |
| Auth | `internal/squ-apiserver/handler/auth/res/response_code.go` |
| Application | `internal/squ-apiserver/handler/application/res/response_code.go` |
| Deployment | `internal/squ-apiserver/handler/deployment/res/response_code.go` |
| AppStore | `internal/squ-apiserver/handler/app_store/res/response_code.go` |
| Script | `internal/squ-apiserver/handler/script/res/response_code.go` |
| Monitor | `internal/squ-apiserver/handler/monitor/res/response_code.go` |
