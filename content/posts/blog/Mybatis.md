---
title: "Mybatis"  
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: flase # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
date: 2022-05-10T13:18:37+08:00
lastmod: 2022-05-10T13:18:37+08:00	#更新文章的时候手动改一下时间就可以
tags: 
- Mybatis
- 框架 
description: "关于Mybatis的学习笔记。"
weight:  # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""

cover:
    image: "/img/Mybatis.webp"
    caption: ""
    alt: ""
    relative: false
---
  
<br>  

### Mybatis是什么
  
Mybatis是一个半ORM（对象关系映射）框架，内部封装了JDBC、加载驱动、创建连接、创建statement等一系列与数据库交互的繁杂步骤。
<hr> 

### Mybatis优缺点

  **优点**

  1. 灵活，sql写在xml中与程序代码解耦，便于统一管理，支持动态sql，并可重用。
  2. 与JDBC相比，减少了至少50%的代码量，消除了JDBC大量冗余代码，不需要手动开关连接。
  3. 与各种数据库兼容。（因为内部封装了JDBC，所以JDBC支持的所有数据库都和Mybatis兼容）
  4. 与Spring能够很好的集成。

  **缺点**

  1. SQL语句编写工作量大，尤其字段多，关联表多时对开发人员编写SQL功底有一定要求。
  2. SQL语句依赖数据库，导致数据库可移植性差，不能随意更换数据库。
<hr>  

**JDBC**：是一个用于执行SQL的Java API，可为多种关系型数据库提供统一访问。

<hr>  

### Hibernate特点  
是一个ORM（Object Relation Mapping：对象关系映射）框架，只需要操作对象，处理好了映射关系后不怎么需要操作数据库。但是缺点有：

1. Hibernate的完全封装导致无法使用数据的一些功能。
2. 对代码的耦合度高。
3. 找bug难，因为封装完美。
4. 批量操作数据需要大量内存空间而且执行过程对象太多。
<hr> 

### Mybatis的使用  
 mybatis-config略过，写一个dao接口但是不用实现，在对应的xml文件写sql，创建一个实体类对应xml中的字段，在mapper.xml中namespace就是对应dao的路径，mapper文件里面写sql语句、resultType、id等参数。 在mapper.xml中sql里面 #{name}的字段是由上层（service层）对实体类对象调用setName方法传进来的，实体类做一个中转站。

在使用Mybatis时，时常实体类属性名和表字段名不匹配，那我们就封装一个结果集，如果能一一对应，系统就相当于自动封装了一个结果集。也可以as取别名。
<hr>

### 动态 sql 小问题
**动态sql**在本人工作过程中**使用**出现的**问题**：<if xx != null>内一直把and或or写在句尾，但是最后一个为null时，上一个 if 末尾的 and 或 or 会导致报错。

**解决方法**：在整体 if 外嵌套一个 where 标签会自动去除所有 if 语句的整体的开头的 and 或 or，但是注意必须写在开头而不是像未使用 where 标签时一样写在末尾。

<hr>

sqlSession：与数据库的会话

<hr>

### 一些属性
flushCache：用来表示当前sql语句的结果是否进入二级缓存;  
**statementType**：用于选择执行sql语句的方式
  1. statement：最基本的jdbc操作，用来表示一个sql语句，不能防止sql注入。
  2. prepared：采用预编译方式，能防止sql注入，设置参数的时候需要该对象来进行设置。
  3. callback：调用存储过程。 

**resultType**：用的不多，因为只能返回一个实体类的类型，多数使用reslutMap自定义结果集

<hr> 

### 预编译
**预编译定义**：SQL预编译指的是数据库驱动在发送SQL语句和参数给DBMS之前对SQL语句进行编译，这样DBMS执行SQL时，就不需要重新编译。

**为什么需要预编译**：预编译阶段可以优化SQL的执行。预编译之后的SQL多数情况下可以直接执行，DBMS不需要再次编译，越复杂的SQL，编译的复杂度将越大，预编译阶段可以合并多次操作为一个操作。同时预编译语句对象可以重复利用。
<hr>

### #{}与${}的区别

1.  #{}的处理方式使用了参数预编译，被解析为一个参数占位符 。  
获取数据：使用 #{} 获取必须用对象的属性名，多个参数时用arg0、arg1.....原因是mybatis在传入多个参数时，会将这些参数的结果封装到map结构中，在map中key就是{arg0，arg1....}。如果想使用参数别名，可以用@param("别名") Integer empAge在dao的interface中。 
2. ${}是直接拼接 sql语句得到对应的 sql语句，仅仅为一个纯碎的 string 替换，会有 sql注入的危险，所以推荐 #{}的方式。  
${}的使用场景：动态传入表名，order by ${列名}。注意：这是动态传值，所以必须用 @param。

    注意：sql注入：传入的参数始终为true，密码验证就无效了。
<hr>

### Mybatis缓存机制

1. 一级缓存：表示sqlSession级别的缓存，每次查询的时候会开启一个会话，此会话相当于一次连接，该连接关闭之后对应的一级缓存自动失效。
2. 二级缓存：全局范围内的缓存，sqlSession关闭之后才会生效。
3. 第三方缓存：继承第三方的组件，来充当缓存的作用。
- 一级缓存：表示将数据存储在sqlSession中，关闭之后自动失效，默认情况下是开启的。在同一个会话之内，如果执行了多个相同的sql语句，那么除了第一个之外，所有的数据都是从缓存中进行查询的。在某些情况下一级缓存可能失效？
    1. 在同一个方法中，可能开启多个会话，此时注意，会话和方法没有关系，不是一个方法就只有一个会话，所以严格记住，缓存的数据是保存在sqlSession中的  。
    2. 当传递同一个对象但是对象属性值不同也不会走缓存。  
    3. 在同一个连接中，如果修改了数据，那么缓存会失效，不同连接不受影响。
    4. 在一个会话过程，手动清除缓存，也会失效。
- 二级缓存：全局缓存，**必须等到sqlSession关闭之后才会生效**。默认不开启，如果开启需要如下设置。
    1. 修改全局配置文件，在setting中添加 ``` <setting name="cacheEnabled" value="true"/> ```
    2. 指定在哪个映射文件中使用缓存的配置：加上 ```<cache></cache>```。
    3. 对应的Java实体类必须实现序列化接口。
- 二级缓存属性：
    1. eviction：缓存淘汰机制：
        1. LRU（默认）：最近最少使用。
        2. FIFO：先进先出，按照添加缓存的顺序执行。
        3. SOFT：软引用：基于垃圾回收器状态和软引用规则移除对象。
        4. WEAK：弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象。
    1. flushInterval：设置多长时间进行缓存刷新
    2. size：引用的条数，是一个正整数，缓存中可以存储多少个对象，一般不设置，如果设置的话不要太大，会导致内存溢出。
    3. readonly：只读属性
        - true：只读缓存，会给所有的调用方法返回该对象的实例，不安全。
        - false：读写缓存，只是返回缓存对象的拷贝，比较安全。

    一二级缓存会不会同时存在数据?  
      不会，一级缓存存在sqlSession，二级缓存是全局缓存，必须等到sqlSession关闭之后才会生效。
     <br>  
<br>
    查询数据时，先查询一级缓存，还是二级缓存？  
      二级缓存 &nbsp;&nbsp;  --> &nbsp;&nbsp; 一级缓存 &nbsp;&nbsp;  -->  &nbsp;&nbsp; 数据库

	![](/img/Mybatis_缓存机制.jpg)
<hr>

### Mybatis工作原理
工作原理如下：
	![](/img/Mybatis_工作原理.webp)  

大致分为：读取配置文件、解析Mapper配置文件、创建SqlSession、执行SQL语句、处理结果集和关闭SqlSession
### Mybatis分页方式

  逻辑分页：使用Mybatis自带的RowBounds进行分页，他会一次性的查出多条数据，然后检索分页中的数据，具体一次查出多少条数据，由封装的JDBC的fetch-size决定。

  物理分页：大家都在用的分页方式，一般用的是pagerHelper实现的就是物理分页。

<hr>

### Mybatis的Executor执行器

  Mybatis有三种基本的Executor执行器，都作用在SqlSession生命周期范围内。

  1. SimpleExecutor：每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。
  2. ReuseExecutor：执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map<String, Statement>内，供下一次使用。简言之，就是重复使用Statement对象。
  3. BatchExecutor：执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。

<hr>
  
 
	
>持续更新中........    敬请期待
  

