## API 返回数据

- GET   {{url}}/api/v1/server

```
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "id": 1,
      "hostname": "hank",
      "ip_address": "127.0.0.1",
      "ssh_username": "root",
      "ssh_port": 22,
      "auth_type": "password",
      "status": "online",
      "server_info": null
    }
  ]
}
```

- GET   {{url}}/api/v1/server/1

```
{
  "code": 0,
  "message": "success",
  "data": {
    "id": 1,
    "hostname": "hank",
    "ip_address": "127.0.0.1",
    "ssh_username": "root",
    "ssh_port": 22,
    "auth_type": "password",
    "status": "",
    "server_info": {
      "architecture": "x86_64",
      "hostname": "hank",
      "ipAddresses": [
        {
          "ipv4": [
            "192.168.37.20"
          ],
          "ipv6": [
            "fe80::20c:29ff:fe06:ee92"
          ],
          "name": "ens33"
        }
      ],
      "kernelVersion": "6.8.0-90-generic",
      "os": "linux",
      "platform": "ubuntu",
      "platformVersion": "24.04",
      "uptime": 467850,
      "uptimeStr": "5天9小时57分钟"
    }
  }
}
```

- DELETE {{url}}/api/v1/server/1

```
{
  "code": 0,
  "message": "success",
  "data": "success"
}
```

- POST {{url}}/api/v1/server

创建服务器

请求数据

```
{
  "ip_address": "192.168.1.10",
  "ssh_username": "root",
  "ssh_port": 22,
  "ssh_password": "123",
  "ssh_private_key": "",
  "auth_type": "password",
  "status": "active",
  "server_alias": "WebProd"
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

- POST    {{url}}/api/v1/server/2

更新服务器

请求数据

```
{
  "ip_address": "192.168.1.10",
  "ssh_username": "root",
  "ssh_port": 22,
  "auth_type": "password",
  "status": "active",
  "server_alias": "WebProd"
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

- GET {{url}}/ws/server/:id

ssh terminal 连接服务器
