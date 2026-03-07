# API 接口

## 目录

- [基础监控数据](#基础监控数据)
- [磁盘 IO 数据](#磁盘-io-数据)
- [网络 IO 数据](#网络-io-数据)

## 基础监控数据

**GET** `/api/v1/monitor/base/:serverId/:page/:count`

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "list": [
      {
        "collect_time": "2026-02-08T13:20:01.012974844Z",
        "cpu_usage": 0.2500000000509317,
        "disk_total": 210194366464,
        "disk_usage": 9.495014360158033,
        "disk_used": 19957985280,
        "id": 1,
        "memory_total": 4055965696,
        "memory_usage": 52.012267906518304,
        "memory_used": 2109599744
      }
    ],
    "page": 1,
    "size": 5,
    "total": 1
  }
}
```

## 磁盘 IO 数据

**GET** `/api/v1/monitor/disk/:serverId/:page/:count`

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "list": [
      {
        "collect_time": "2026-02-08T13:20:01.014811192Z",
        "disk_name": "sr0",
        "id": 1,
        "io_time": 0,
        "iops_in_progress": 0,
        "read_bytes": 2142208,
        "read_count": 56,
        "read_time": 21,
        "weighted_io_time": 0,
        "write_bytes": 0,
        "write_count": 0,
        "write_time": 0
      },
      {
        "collect_time": "2026-02-08T13:20:01.014811192Z",
        "disk_name": "sda",
        "id": 2,
        "io_time": 0,
        "iops_in_progress": 0,
        "read_bytes": 3812750336,
        "read_count": 144018,
        "read_time": 43529,
        "weighted_io_time": 0,
        "write_bytes": 7137935360,
        "write_count": 164007,
        "write_time": 57935
      }
    ],
    "page": 1,
    "size": 5,
    "total": 5
  }
}
```

## 网络 IO 数据

**GET** `/api/v1/monitor/net/:serverId/:page/:count`

```json
{
  "code": 0,
  "message": "success",
  "data": {
    "list": [
      {
        "bytes_recv": 609802442,
        "bytes_sent": 482111675,
        "collect_time": "2026-02-08T13:20:01.014811192Z",
        "drop_in": 0,
        "drop_out": 0,
        "err_in": 0,
        "err_out": 0,
        "fifo_in": 0,
        "fifo_out": 0,
        "id": 1,
        "interface_name": "ens33",
        "packets_recv": 1014491,
        "packets_sent": 850813
      }
    ],
    "page": 1,
    "size": 5,
    "total": 1
  }
}
```
