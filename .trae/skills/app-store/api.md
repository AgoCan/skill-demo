## API 返回数据

### 获取应用商店列表

- GET   {{url}}/api/v1/app-store

```
{
  "code": 0,
  "message": "success",
  "data": [
    {
      "id": 1,
      "name": "Redis",
      "description": "Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache, message broker, and streaming engine",
      "type": "compose",
      "category": "database",
      "icon": "https://redis.io/images/redis-white.png",
      "version": "7.2.1",
      "content": "version: '3'\nservices:\n  redis:\n    image: redis:7.2.1-alpine\n    ports:\n      - '6379:6379'\n    volumes:\n      - redis_data:/data\n    command: redis-server --appendonly yes\n\nvolumes:\n  redis_data:",
      "tags": "database,cache,in-memory,persistence",
      "author": "Redis Labs",
      "repo_url": "https://github.com/redis/redis",
      "homepage_url": "https://redis.io",
      "is_official": true,
      "downloads": 0,
      "status": "active"
    },
    {
      "id": 2,
      "name": "Kafka",
      "description": "Kafka",
      "type": "compose",
      "category": "database",
      "icon": "",
      "version": "1.36.2",
      "content": "services:\n  postgresql:\n    image: postgres:14\n    hostname: postgresql\n    volumes:\n      - pg_data:/var/lib/postgresql/data\n    environment:\n      POSTGRES_DB: \"conduktor-console\"\n      POSTGRES_USER: \"conduktor\"\n      POSTGRES_PASSWORD: \"some_password\"\n      POSTGRES_HOST_AUTH_METHOD: \"scram-sha-256\"\n  conduktor-console:\n    image: conduktor/conduktor-console:1.36.2\n    ports:\n      - \"8080:8080\"\n    volumes:\n      - conduktor_data:/var/conduktor\n    environment:\n      CDK_DATABASE_URL: \"postgresql://conduktor:some_password@postgresql:5432/conduktor-console\"\n      CDK_CLUSTERS_0_ID: \"default\"\n      CDK_CLUSTERS_0_NAME: \"My Local Kafka Cluster\"\n      CDK_CLUSTERS_0_COLOR: \"#0013E7\"\n      CDK_CLUSTERS_0_BOOTSTRAPSERVERS: \"PLAINTEXT://kafka1:19092\"\n  kafka1:\n    image: confluentinc/cp-kafka:8.0.0\n    hostname: kafka1\n    container_name: kafka1\n    ports:\n      - \"9092:9092\"\n      - \"29092:29092\"\n      - \"9999:9999\"\n      - \"9099:9099\"\n    environment:\n      CLUSTER_ID: MkU3OEVBNTcwNTJENDM2Qk\n      KAFKA_NODE_ID: 1\n      KAFKA_PROCESS_ROLES: broker,controller\n      KAFKA_CONTROLLER_QUORUM_VOTERS: \"1@kafka1:9099\"\n      KAFKA_LISTENERS: INTERNAL://0.0.0.0:19092,EXTERNAL://0.0.0.0:9092,DOCKER://0.0.0.0:29092,CONTROLLER://0.0.0.0:9099\n      KAFKA_ADVERTISED_LISTENERS: INTERNAL://kafka1:19092,EXTERNAL://${DOCKER_HOST_IP:-127.0.0.1}:9092,DOCKER://host.docker.internal:29092\n      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT,DOCKER:PLAINTEXT,CONTROLLER:PLAINTEXT\n      KAFKA_CONTROLLER_LISTENER_NAMES: CONTROLLER\n      KAFKA_INTER_BROKER_LISTENER_NAME: INTERNAL\n      KAFKA_LOG4J_LOGGERS: \"kafka.controller=INFO,kafka.producer.async.DefaultEventHandler=INFO,state.change.logger=INFO\"\n      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1\n      KAFKA_TRANSACTION_STATE_LOG_MIN_ISR: 1\n      KAFKA_TRANSACTION_STATE_LOG_REPLICATION_FACTOR: 1",
      "tags": "database,cache,in-memory",
      "author": "Redis Labs",
      "repo_url": "https://github.com/kafka/kafka",
      "homepage_url": "https://kafka.apache.org/",
      "is_official": true,
      "downloads": 0,
      "status": "active"
    }
  ]
}
```

### 获取应用商店详情

- GET   {{url}}/api/v1/app-store/1

```
{
  "code": 0,
  "message": "success",
  "data": {
    "id": 1,
    "name": "Redis",
    "description": "Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache, message broker, and streaming engine",
    "type": "compose",
    "category": "database",
    "icon": "https://redis.io/images/redis-white.png",
    "version": "7.2.1",
    "content": "version: '3'\nservices:\n  redis:\n    image: redis:7.2.1-alpine\n    ports:\n      - '6379:6379'\n    volumes:\n      - redis_data:/data\n    command: redis-server --appendonly yes\n\nvolumes:\n  redis_data:",
    "tags": "database,cache,in-memory,persistence",
    "author": "Redis Labs",
    "repo_url": "https://github.com/redis/redis",
    "homepage_url": "https://redis.io",
    "is_official": true,
    "downloads": 0,
    "status": "active"
  }
}
```

### 删除应用商店

- DELETE {{url}}/api/v1/app-store/1

```
{
  "code": 0,
  "message": "success",
  "data": "success"
}
```

### 创建应用商店

- POST {{url}}/api/v1/app-store

请求数据

```
{
  "name": "Redis",
  "description": "Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache, message broker, and streaming engine - Updated version",
  "type": "compose",
  "category": "database",
  "icon": "https://redis.io/images/redis-white.png",
  "version": "7.2.1",
  "content": "version: '3'\nservices:\n  redis:\n    image: redis:7.2.1-alpine\n    ports:\n      - '6379:6379'\n    volumes:\n      - redis_data:/data\n    command: redis-server --appendonly yes\n\nvolumes:\n  redis_data:",
  "tags": "database,cache,in-memory,persistence",
  "author": "Redis Labs",
  "repo_url": "https://github.com/redis/redis",
  "homepage_url": "https://redis.io",
  "is_official": true,
  "downloads": 0,
  "status": "active"
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

### 更新应用商店

- POST {{url}}/api/v1/app-store/1

请求数据

```
{
  "id": 1,
  "name": "Redis",
  "description": "Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache, message broker, and streaming engine - Updated version",
  "type": "compose",
  "category": "database",
  "icon": "https://redis.io/images/redis-white.png",
  "version": "7.2.1",
  "content": "version: '3'\nservices:\n  redis:\n    image: redis:7.2.1-alpine\n    ports:\n      - '6379:6379'\n    volumes:\n      - redis_data:/data\n    command: redis-server --appendonly yes\n\nvolumes:\n  redis_data:",
  "tags": "database,cache,in-memory,persistence",
  "author": "Redis Labs",
  "repo_url": "https://github.com/redis/redis",
  "homepage_url": "https://redis.io",
  "is_official": true,
  "downloads": 0,
  "status": "active"
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

## 错误码

| 错误码 | 说明 |
|--------|------|
| 70001 | invalid compose content |
| 70002 | unsupported application type |
| 70003 | application store not found |
| 70004 | application store already exists |

## 字段说明

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| id | number | 否 | 应用ID（更新时必填） |
| name | string | 是 | 应用名称 |
| description | string | 是 | 应用描述 |
| type | string | 是 | 应用类型（compose、k8s） |
| category | string | 是 | 应用分类（database、cache、message、web、devops、other） |
| icon | string | 否 | 应用图标URL |
| version | string | 是 | 应用版本 |
| content | string | 是 | 应用内容（compose类型为docker-compose.yml内容） |
| tags | string | 是 | 应用标签，逗号分隔 |
| author | string | 是 | 应用作者 |
| repo_url | string | 否 | 仓库地址 |
| homepage_url | string | 否 | 主页地址 |
| is_official | boolean | 是 | 是否官方应用 |
| downloads | number | 是 | 下载量 |
| status | string | 是 | 状态（active、inactive、draft） |
