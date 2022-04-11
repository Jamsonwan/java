# Redis

## 1、基本数据类型

### 1.1 String

* 基本格式**简单动态字符串**（simple dynamic string，SDS）

* ```c
  typedef struct sdshdr{
      int len; // 记录buf数组中已使用的字节长度数量，等于SDS所保存的字符串长度
      int free; // 记录buf数组中未使用字节的数量
      char buf[]; // 字节数组，用于保存字符串,注意以'\0'结尾
  };
  ```

* 杜绝缓冲区溢出（len）、空间预分配、惰性空间释放、二进制安全（len）、可以使用部分<string.h>库函数



### 1.2 List

* 单个节点

  ```c
  typedef struct listNode{
      //前置节点
      struct listNode *prev;
      // 后置节点
      struct listNode *next;
      // 节点值
      void *value;
  }listNode;
  ```

* list

  ```c
  typedef struct list{
      // 链表头
      listNode *head;
      // 链表尾部
      listNode *tail;
      // 链表所包含的节点数
      unsigned long len;
      // 节点复制函数
      void *(*dup) (void *ptr);
      // 节点值释放函数
      void (*free) (void *ptr);
      // 节点对比函数
      int (*match) (void *ptr, void *key);
  }list;
  ```



### 1.3 字典dict

* 哈希表节点

  ```c
  typedef struct dictEntry{
      void *key; // 键
      union{ // 值
          void *val;
          uint64_t u64;
          int64_t s64;
      } v;
      
      struct dictEntry *next; // 指向下个哈希表节点，形成链表,解决冲突
  }dictEntry;
  ```

*  哈希表

  ```c
  typedef struct dictht{
      // 哈希表数组
      dictEntry **table;
      // 哈希表大小
      unsigned long size;
      // 哈希表大小掩码，用于计算索引值
      // 总是等于size - 1；
      unsigned long sizemask;
      
      // 该哈希表已有节点的数量
      unsigned long uesed;
  }dictht;
  ```

* 字典dict

  ```c
  typedef struct dict{
      // 类型特定函数
      dictType *type;
      // 私有数据
      void *private;
      // 哈希表
      dictht ht[2];
      // rehash索引
      // 当rehash不在进行时，值为-1
      int rehashidx;
  }dict;
  ```

  ```c
  typedef struct dictType{
      // 计算哈希值的函数
      unsigned int (*hashFunction)(const void *key);
      // 复制键函数
      void *(*keyDup)(void *privdata, const void *key);
      // 复制值函数
      void *(*valDup)(void *privdata, const void *obj);
      ...
  }dictType;
  ```

* 字典只用ht[0]哈希表，ht[1]哈希表只会在对ht[0]进行rehash的时候使用。链地址法解决hash冲突。

