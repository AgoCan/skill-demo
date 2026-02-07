## API 返回数据

- GET   {{url}}/api/v1/application

默认data是null

```
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "id": 1,
      "name": "nginx",
      "description": "Nginx web server",
      "type": "compose",
      "content": "version: '3'\nservices:\n  nginx:\n    image: nginx:latest\n    container_name: nginx-server\n    restart: always\n    ports:\n      - \"80:80\"\n    volumes:\n      - nginx_html:/usr/share/nginx/html\nvolumes:\n  nginx_html:",
      "version": "1.25.0"
    },
    {
      "id": 2,
      "name": "mysql",
      "description": "MySQL database",
      "type": "compose",
      "content": "version: '3'\nservices:\n  mysql:\n    image: mysql:8.0\n    environment:\n      MYSQL_ROOT_PASSWORD: root\n    ports:\n      - \"3306:3306\"",
      "version": "8.0.35"
    }
  ]
}
```

- GET   {{url}}/api/v1/application/1

```
{
  "code": 0,
  "message": "success",
  "data": {
    "id": 1,
    "name": "nginx",
    "description": "Nginx web server",
    "type": "compose",
    "content": "version: '3'\nservices:\n  nginx:\n    image: nginx:latest\n    container_name: nginx-server\n    restart: always\n    ports:\n      - \"80:80\"\n    volumes:\n      - nginx_html:/usr/share/nginx/html\nvolumes:\n  nginx_html:",
    "version": "1.25.0"
  }
}
```

- DELETE {{url}}/api/v1/application/1

```
{
  "code": 0,
  "message": "success",
  "data": "success"
}
```

- POST {{url}}/api/v1/application

创建应用

请求数据

```
{
  "name": "nginx",
  "description": "Nginx web server",
  "type": "compose",
  "content": "version: '3'\nservices:\n  nginx:\n    image: nginx:latest\n    container_name: nginx-server\n    restart: always\n    ports:\n      - \"80:80\"\n    volumes:\n      - nginx_html:/usr/share/nginx/html\nvolumes:\n  nginx_html:",
  "version": "1.25.0"
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

- POST    {{url}}/api/v1/application/1

更新应用

请求数据

```
{
  "id": 1,
  "name": "nginx",
  "description": "Nginx web server updated",
  "type": "compose",
  "content": "version: '3'\nservices:\n  nginx:\n    image: nginx:1.25\n    ports:\n      - '80:80'",
  "version": "1.0.1"
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
