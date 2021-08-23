# Lithops Actor Model metrics

Each CSV file contains average pings per second per execution.

## Environment

### Benchmarking

Hyperfine, as our command-line benchmarking tool: https://github.com/sharkdp/hyperfine

#### 3 warmups and 30 samples

```shell
#!/usr/bin/env bash
hyperfine --warmup 3 --min-runs 30 './venv/bin/python ./serverless-tests/pingpong.py'
```

#### 3 warmups and 30 samples with Redis flushall command for every single run

```shell
#!/usr/bin/env bash
hyperfine --warmup 3 --min-runs 30 'docker exec -i lithops-actors_redis_1 redis-cli -u redis://default:password@redis:6379 flushall && ./venv/bin/python ./serverless-tests/pingpong.py'
```

#### 3 warmups and 500 samples

```shell
#!/usr/bin/env bash
hyperfine --warmup 3 --min-runs 500 './venv/bin/python ./serverless-tests/pingpong.py'
```

### Populate CSV files

```shell
lithops logs poll | grep -e 'pings per second' | sed 's/pings per second//'
```

### Localhost

* Python: 3.8.10
* Redis Docker image: redislabs/redis:6.2.5-alpine
* RedisInsight - The GUI for Redis. Docker image: redislabs/redisinsight:1.10.1

### Seerverless

* AWS Lambda Python 3.8
* DigitalOcean Managed Databases Redis 6

## Steps

| ID | Step | Sample size | Ping-Pongs | Mode | CPU or Cloud Provider | RAM (GByte) | Redis flushall | RedisInsight's profiler | DigitalOcean Managed Redis | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 1 | 30 | 100 | Localhost | AMD Ryzen 5 3600 | 64 | false| false | |
| 2 | 1 | 500 | 100 | Localhost | AMD Ryzen 5 3600 | 64 | false | false | |
| 3 | 1 | 500 | 100 | Localhost | Intel Core i5-8259U | 32 | false | false | |
| 4 | 2 | 30 | 100 | Localhost | AMD Ryzen 5 3600 | 64 | true | false | |
| 5 | 3 | 30 | 100 | Localhost | AMD Ryzen 5 3600 | 64 | false | true | |
| 6 | 3 | 500 | 100 | Localhost | AMD Ryzen 5 3600 | 64 | false | true | |
| 7 | 4 | 30 | 100 | Localhost | AMD Ryzen 5 3600 | 64 | true | true | |
| 8 | 5 | 30 | 500 | Localhost | AMD Ryzen 5 3600 | 64 | false | false | |
| 9 | 5 | 500 | 500 | Localhost | AMD Ryzen 5 3600 | 64 | false | false | |
| 10 | 5 | 500 | 500 | Localhost | Intel Core i5-8259U | 32 | false | false | |
| 11 | 6 | 30 | 500 | Localhost | AMD Ryzen 5 3600 | 64 | true | false | |
| 12 | 7 | 30 | 1000 | Localhost | AMD Ryzen 5 3600 | 64 | false | false | |
| 13 | 7 | 500 | 1000 | Localhost | AMD Ryzen 5 3600 | 64 | false | false | |
| 14 | 7 | 500 | 1000 | Localhost | Intel Core i5-8259U | 32 | false | false | |
| 15 | 8 | 30 | 1000 | Localhost | AMD Ryzen 5 3600 | 64 | true | false | |
| 16 | 9 | 30 | 500 | Serverless | AWS Lambda | 0.256 | false | false | 1GB RAM 1vCPU |
| 17 | 9 | 500 | 500 | Serverless | AWS Lambda | 0.256 | false | false | 1GB RAM 1vCPU |
| 18 | 10 | 30 | 500 | Serverless | AWS Lambda | 0.256 | false | false | 2GB RAM 1vCPU |
| 19 | 11 | 30 | 500 | Serverless | AWS Lambda | 0.256 | false | false | 4GB RAM 2vCPU |

## Localhost mode

### AMD Ryzen 5 3600

#### 30 samples

| ID | Step | Ping-Pongs | RAM (GByte) | Redis flushall | RedisInsight's profiler | 
| --- | --- | --- | --- | --- | --- | 
| 1 | 1 | **100** | 64 | **false** | **false** |
| 4 | 2 | **100** |  64 | **true** | **false** |
| 5 | 3 | **100** |  64 | **false** | **true** |
| 7 | 4 | **100** |  64 | **true** | **true** |
| 8 | 5 | **500** |  64 | **false** | **false** |
| 11 | 6 | **500** |  64 | **true** | **false** |
| 12 | 7 | **1000** |  64 | **false** | **false** |
| 15 | 8 | **1000** |  64 | **true** | **false** |

#### 500 samples

| ID | Step | Ping-Pongs | RAM (GByte) | Redis flushall | RedisInsight's profiler | 
| --- | --- | --- | --- | --- | --- | 
| 2 | 1 | **100** |  64 | false | **false** |
| 6 | 3 | **100** |  64 | false | **true** |
| 9 | 5 | **500** |  64 | false | **false** |
| 13 | 7 | **1000** |  64 | false | **false** |

### Intel Core i5-8259U

#### 500 samples

| ID | Step | Ping-Pongs | RAM (GByte) | Redis flushall | RedisInsight's profiler | 
| --- | --- | --- | --- | --- | --- | 
| 3 | 1 | **100** | 32 | false | false |
| 10 | 5 | **500** | 32 | false | false |
| 14 | 7 | **1000** | 32 | false | false |

## Serverless mode

### AWS Lambda

#### 30 samples

| ID | Step | Ping-Pongs | RAM (MByte) | DigitalOcean Managed Redis | 
| --- | --- | --- | --- | --- |
| 16 | 9 | 500 | 256 | **1GB RAM 1vCPU** |
| 18 | 10 | 500 | 256 | **2GB RAM 1vCPU** |
| 19 | 11 | 500 | 256 | **4GB RAM 2vCPU** |

#### 500 samples

| ID | Step | Ping-Pongs | RAM (MByte) | DigitalOcean Managed Redis | 
| --- | --- | --- | --- | --- |
| 17 | 9 | 500 | 256 | 1GB RAM 1vCPU |
