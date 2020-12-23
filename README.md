xRedis-ext
======
详情请转至 https://github.com/0xsky/xredis.git 查看，在xredis基础上添加select功能。

Redis C++ client, support the data slice storage, support the connection pool

xRedis 是一个C++开发的redis客户端，是对hiredis的C++封装，提供易用的redis命令操作接口.

***功能与特点：***
* 支持数据多节点分布存储，可自定义分片规则;
* 支持连接到官方集群，支持自动计算节点索引位置;
* 支持同时连接到每个分片的主从节点，支持主从读写分离;
* 支持对每个存储节点建立连接池;
* 支持同时连接多个数据分片集群;
* 提供简单易用的C++接口封装，已实现大部分REDIS命令;
* 只依赖hiredis库;
* 多线程安全
* 支持带密码连接;
 

### Dependencies

xredis 依赖 hiredis ,  在使用xRedis前需要安装hiredis库

### Install

第一步 安装libhiredis
 在Debian系统上:
```bash
sudo apt-get install libhiredis-dev
```

xRedis源码安装
```bash
git clone https://github.com/0xsky/xredis
cd xredis
make
sudo make install
```
使用说明
```C++
#使用 xRedisClient类 访问 redis节点

#include <cstdio>
#include "xRedisClient.h"

using namespace xrc;

enum {
 CACHE_TYPE_1, 
 CACHE_TYPE_2,
 CACHE_TYPE_MAX,
};

/* 配置单个redis存储节点 修改节点序号为nodeIndex，添加dbIndex redis库index*/
RedisNode redisNode={0,"172.16.3.252", 6379, "6N538goFaDSdmrqRzM5cyo8TFkGuaYokM6zpUnB87LRiiCViuG9Z5Y4jioA5", 1, 1, 5, 0};

int main(int argc, char **argv) {
    (void)argc;(void)argv;
    
    xRedisClient xRedis;
    xRedis.Init(CACHE_TYPE_MAX);

    xRedis.ConnectRedisCache(&redisNode, CACHE_TYPE_1);
    const char *key = "test";
    const char *value = "test value";
    SliceIndex index(&xRedis, CACHE_TYPE_1);
    index.Create(key);

    bool bRet = xRedis.set(index, key, value); 
    if(bRet){
        printf("success \r\n");
    } else {
        printf("error [%s] \r\n", index.GetErrInfo());
    }

    std::string strValue;
    bRet = xRedis.get(index, key, strValue);
    if (bRet) {
        printf("%s \r\n", strValue.c_str());
    } else {
        printf("error [%s] \r\n", index.GetErrInfo());
    }

    return 0;
}
```

更多使用示例
使用示例 [examples]目录(https://github.com/bingyangok/xredis-ext/tree/master/examples)

demo.cpp              使用xredisclient访问单个redis节点示例
demo_slice.cpp        使用xredisclient访问单组多分片(节点)redis分片集群示例
demo_multi_slice.cpp  使用xredisclient访问多组多分片(节点)redis分片集群示例
xredis-example.cpp    使用xredisclient 批量操作示例 
xredis-example-Consistent-hash xredisclient使用一致性hash，自定义数据分片存储demo

redis-cluster-client.cpp      使用xRedisClusterClient访问redis官方集群示例
xRedisClusterClient_test.cpp  使用xRedisClusterClient实现简单的redis官方集群客户端示例

/test/xredis-test.cpp   多个redis命令的使用示例

