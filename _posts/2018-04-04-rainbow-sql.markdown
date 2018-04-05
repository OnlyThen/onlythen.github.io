---
layout:     post
title:      "我是如何利用SQLite来存储彩虹表的"
subtitle:   "代码笔记第五话"
date:       2018-04-04
author:     "XiaoYu"
header-img: "img/post-sqlite.jpeg"
tags:
    - 数据库
    - 经验
    - Code
---

> 三月二十号离开深圳前往成都，复试后直接回学校答辩。路途多舛，不表也罢！谨以此文纪念陪伴我走南闯北的iPhone`5s`

## 痒点or痛点

之前参考GitHub上他人的项目，完成了彩虹表算法的设计与实现，主要的数据操作是通过读写二进制文件来解决的，生成表格时要先将产生的链的数据全部保存在内存中，破解时则读取文件内容直内存中，其中内存的寻址通过结构体来实现。那么问题来了，现在按照最优参数生成的5位密码的彩虹表的大小是1.2MB，针对6位则达到了17.3MB，而且密码的集合还是数字和小写字母，如果包括所有的可输入字符，且密码长度达到9位，将需要GB级别的内存，这对于单一计算平台是不现实的，基于此，选择了数据库来进行数据存储。

---

## SQLite3

顾名思义，这是一个轻量级的结构化查询语言Structure Query Language，一开始了解到数据库这个东西的时候，我就觉得特别适合彩虹表的存储，毕竟都是关系型数据的存取和查询，一开始打算使用MySQL，后来发现不需要这么大的一个库，于是接触到了SQLite，发现果然很轻量，在其官网上的介绍是这样的：SQLite是一个自包含的，高可靠性的，嵌入式的，全功能的，属于公共领域的SQL数据库引擎， SQLite是世界上使用最多的数据库引擎。

这几天完成了使用SQLite来实现彩虹表算法的程序，遇到了很多问题并一一解决，现在将其记录下来。

## 构想阶段

想法很简单，就是创建一个表，定义三列数据，分别是ID、START、END，其他的操作等同与使用文件存储，在查询上可以直接使用序号。但实际使用时发现了很多问题。

### 问题一

第一个问题和数据库关系不大，是关于随机数产生的问题，我定义了一个函数来生成开始的字符串，结果发现每次产生的都是一样的，代码如下：
```
void generateStartStr(uint32_t length, char* key, uint32_t count, const char* charSet) {
    
    //srand((unsigned int)time(NULL));
    for (int index = 0; index < length; index += 1, key++) {
        //long index = rand() % count;
        *key = charSet[rand() % count];
        //printf("%ld\n", index);
    }
}
```
注意到我注释掉的函数，因为是通过循环调用这个函数的，导致每次设置的种子都是一样的，把设置种子的函数放到循环之外就可以了。

### 问题二

这次是关于排序的，之前的程序是使用`qsort`函数将结束节点进行排序，之后使用二分法查找相应字符串，后面我本来也打算采用这种方式，后来看到数据库也可以排序，使用这一条语句：`SELECT * FROM COMPANY ORDER BY END ASC`，后来发现这只是用来查询时使用的，并不能保存排序后的数据到数据库中，这样就无法使用二分查找了，后来只好采用遍历查询，用了一个笨办法，获取每一个END字符串并于目标进行比较，这样就执行了N次SQL语句，十分缓慢，后来发现可以这样做：
```
int binaryFind(char* string, sqlite3* db) {
    char* sql = "SELECT * from RAINBOWTABLE WHERE END = ?";
    sqlite3_stmt* stmt;
    int point;
    sqlite3_prepare(db,sql,(int)strlen(sql),&stmt,NULL);
    sqlite3_bind_text(stmt, 1, string, 6, SQLITE_STATIC);
    if (sqlite3_step(stmt) == SQLITE_ROW) {
        point = sqlite3_column_int(stmt, 0);
        sqlite3_finalize(stmt);
        return point;
    } else {
        sqlite3_finalize(stmt);
        return -1;
    }
}

```
这样就只需执行一次SQL语句。

### 问题三

这样终于生成了彩虹表，下一步要进行破解了，这里首先遇到了一个逻辑问题，也就是要写好合适的循环来依次进行尝试，我之前就弄错了顺序，导致无法破解。

### 问题四

在C语言中调用SQLite是很简单的，当涉及到变量时使用如下的几个函数：
```
sqlite3_prepare()
sqlite3_bind_value()
sqlite3_step()
sqlite3_column_text()
sqlite3_finalize()
```
这里我一开始是定义一个char* 的指针来获取sqlite3_column_text()返回的值，结果一直输出一些奇怪的内容，查了文档后才发现问题所在，文档上是这样描述的：
`Do not pass the pointers returned from sqlite3_column_blob(), sqlite3_column_text(), etc. into sqlite3_free().`
也就是说返回的指针是野指针，最后我使用`memcpy`函数来拷贝出返回值的内容，终于成功。这里的内部实现还需要好好研究一下。

## 总结

实现算是实现了，但效率是远不如使用文件的，比较那种是在内存中操作，后续我要找寻适用于数据库的场景。



