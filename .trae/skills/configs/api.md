## API 返回数据

- GET   {{url}}/api/v1/config

```
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "id": 1,
      "key": "app_name",
      "value": "Squirrel"
    },
    {
      "id": 2,
      "key": "app_version",
      "value": "1.0.0"
    }
  ]
}
```

- GET   {{url}}/api/v1/config/1

```
{
  "code": 0,
  "message": "success",
  "data": {
    "id": 1,
    "key": "app_name",
    "value": "Squirrel"
  }
}
```

- DELETE {{url}}/api/v1/config/1

```
{
  "code": 0,
  "message": "success",
  "data": "success"
}
```

- POST {{url}}/api/v1/config

创建配置

请求数据

```
{
  "key": "app_name",
  "value": "Squirrel"
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

- POST    {{url}}/api/v1/config/1

更新配置

请求数据

```
{
  "id": 1,
  "key": "app_name",
  "value": "Squirrel Dev"
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
