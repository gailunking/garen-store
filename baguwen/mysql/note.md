[note.md](https://github.com/gailunking/garen-store/files/14821681/note.md)[Uploading# 零基础自测面试题

## InnoDB和MyISAM有什么区别

1. InnoDB支持事务，MyISAM不支持事务 对于InnoDB每一条SQL语言都默认封装成事务，自动提交，这样会影响速度，所以最好把多条SQL语言放在begin和commit之间，组成一个事务；

- InnoDB：提供事务支持，支持ACID属性（原子性、一致性、隔离性、持久性），可以满足复杂的事务处理需求。
- MyISAM：不支持事务。如果发生故障或错误，MyISAM 表可能会在中间状态中留下损坏的数据。

1. InnoDb支持外键，MyISAM不支持外键。对一个包含外键的InnoDB表转为MYISAM会失败； 
2. InnoDB是聚簇索引，底层使用B+树，索引和原数据存储在一起，

MyISAM是非聚簇索引，底层也是B+树，但是索引和指向数据的指针放一起。主键索引和辅助索引是独立的。

也就是说：InnoDB的B+树主键索引的叶子节点就是数据文件，辅助索引的叶子节点是主键的值；而MyISAM的B+树主键索引和辅助索引的叶子节点都是数据文件的地址指针。

1.  表级锁定 vs. 行级锁定：

- InnoDB： 使用行级锁定，可以避免大部分的并发性能问题，允许多个事务在同一时间修改表中的不同行。
- MyISAM： 使用表级锁定，一次只允许一个事务对表进行写操作，这可能导致并发写入性能下降。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MmEwYjc4OWNjOGQ5NTQ1YTk2OThkNDlmMDRhZTMxMThfc213bGRiOWdOVUpnazFtN1lvekpob1Q3Q2NFTW9Mb1hfVG9rZW46QjNMdWI2UmE2b2p2eVZ4enZ2emNmNU80bjliXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

## 什么是数据库范式？为什么要反范式？

https://www.cnblogs.com/zhaosq/p/10026947.html

**数据库****范式****是数据库书写规定的针对数据库结构设计的一些规范，其中包括三大范式**

第一范式**（1NF）**：**无重复的列** 要求表一个字段同行内不能出现多个数据，这种情况需要分表存储数据，构造一对多和多对一的关系

如 学生表的课程字段出现了一个学生有语文，数学两个课程 这个时候要单独建立一个学生和课程的关系表，第一范式要求表中的每个字段都是单一的、不可再分的数据单元，不允许包含集合、数组或其他非原子性的数据结构。

第二范式**（2NF）**： **属性完全依赖于主键**。要求首先符合第一范式、

其次要求表中的字段必须依赖于主键，比如，订单表中不应该出现具体的订单项中某个产品的名称，而是应该存储对应ID

第三范式**3NF）**：**属性不传递依赖于其它非主属性****。**首先要求符合第二范式

其次它要求一个数据库表中的所有非主键列都应该直接依赖于主键，而不依赖于其他非主键列。简而言之，第三范式消除了表中的传递依赖。举个例子，第一列是成本，第二列是收入，还有一列是利润，利润受收入和成本总的影响，前者改变，利润不改就错了，换句话说，它依赖了非主键值，所以不符合第三范式，应该另创一个表来存放它。

##### 范式优缺点：

​        范式再给我们带来的上面的好处时，同时也伴随着一些不好的地方：按照范式的规范设计出来的表，等级越高的范式设计出来的表越多。如第一范式可能设计 出来的表可能只有一张表而已，再按照第二范式去设计这张表时就可能出来两张或更多张表，如果再按第三范式或更高的范式去设计这张表会出现更多比第二范式多 的表。表的数量越多，当我们去查询一些数据，必然要去多表中去查询数据，这样查询的时间要比在一张表中查询中所用的时间要高很多。

也就是说我们所用的范式越高，对数据操作的性能越低。所以我们在利用范式设计表的时候，要根据具体的需求再去权衡是否使用更高范式去设计表。在一般的项目中，我们用的最多也就是第三范式，第三范式也就可以满足我们的项目需求，性能好而且方便管理数据；

当我们的业务所涉及的表非常多，经常会有多表发生关系，并且我们对表的操作要时间上要尽量的快，这时可以考虑我们使用“**反****范式**”。

##### **反****范式****：**

顾名思义，反范式就是于范式化相反的一种设计思想，范式化要求减少数据冗余，提高数据一致性。而反范式化相反，他通过**增加数据冗余来提升数据库的性能，提高查询速率，也就是以空间换时间。**

**所谓反****范式**，故名思义，跟范式所要求的正好相反，在反范式的设计模式，我们可以允许适当的数据的冗余，用这个冗余去取操作数据时间的缩短。**也就是利用空间来换取时间**,把数据冗余在多个表中，当查询时可以减少或者是避免表之间的关联；

  **场景分析:**

如我们现在要对一个 学校的课程表进行操作，现在有两张表，一张是学生信息student（a_id,a_name,a_adress,b_id）表，一张是课程表 subject(b_id,b_subject)，现在我们需要一个这样的信息，把选择每个课程的的课程名称和学生姓名输出来：

SQL语句为：select  B.b_id,B.b_subject,A_a_name from student A ,subject B;

当上面的数据量不多时，我们这样去查询没有问题；当我们的两张表的数据都是在百万级的时候，我们去查上面的信息， 问题出现了，这个查询动不动就是几百毫秒，甚至更慢，这样的查询效率根本不能满足我们对于网页速度的要求（一般不能超过100毫秒），怎么办？当然要反范式，在课**程表里面添加冗余字段——学生姓名，这样我们**就可以通过下面的查询达到同样的目的：

SQL语句为：select  b_id,b_subject,a_name from subject B;

将两个查询放在一起查看执行计划，就会发现，第一个查询开销占了92%，而第二个才8%，也就是说，第二个查询比起第一个查询，效率上优化了10倍以上，成果显著啊。

## 事务的隔离级别有哪些，分别解决了什么问题？

1. 读未提交（RU）：没解决任何问题，指事务A读取了事务B未提交的事务，然后B回滚了，A发生脏读
2. 读已提交（RC）：解决了脏读，A只能读B已提交的事务，但是不可重复读，A事务期间内读了两次某一数据，但是中间阶段有其他事务修改了该数据，造成了两次读的结果不一致
3. 可重复读（RR）：解决了不可重复读，重复读的时候会保证读到的结果是一致的，但是如果中间有其他线程增删了数据，就会出现幻读现象，读出多余或者更少的数据，这个也是数据库的默认隔离级别。

**在****可重复读****隔离级别下，事务内的查询会看到事务开始时的一致性状态，即第一次读取的状态。（并不是直接调取第一次读的数据，而是通过快照读取）具体实现方式可能会使用****多版本并发控制****（****MVCC****）等技术。当事务开始时，会为该事务创建一个快照（snapshot），事务内的所有查询都将使用这个快照来读取数据。即使其他事务对相同的数据进行了修改，可重复读事务不会看到这些修改，从而解决了****不可重复读****的问题。**

1. 串行化：解决幻读的问题，将所有事务串行执行，并发度小

## 什么是脏读，脏写，不可重复读，幻读？

**脏读****，脏写：**本质上都是A事务对已经被B事务修改过但还未提交的数据进行了操作，操作完成后B事务回滚了这次操作，导致A事务的操作也失效。

**不可重复读****：**一次事务中对包含了两次对同一数据的读操作，中间有其他事务对该数据进行了修改，导致两次读取操作获取到的值不一致

**幻读****：**同上，发生在一个事务的两次范围查询内，只是另一个事务的读换成了插入和删除操作。

## 如何理解MVCC？

MVCC（Multi-Version Concurrency Control）全程多版本并发控制，我们分词来解释

多版本：是指针对于每条数据的操作，有一个版本链，如图，这是某一数据所有修改的事务id链

暂时无法在飞书文档外展示此内容

并发：是指可以多线程并发的进行操作

控制：控制是指针对于不同的隔离级别能对返回哪条数据做控制，然后我们对上图进行扩展

其中我们可以看到一个重要的组成，readview，它通常分为以下几个字段：

m_ids：目前活跃的事务就是指还没有commit的事务。它数据结构为一个List。

min_trx_id:是前最小活跃事务，是m_ids的最小值。

max_trx_id：表示生成ReadView时，系统中应该分配给下一个事务的id值。。

creator_trx_id：创建当前read view的事务ID

对于不同的隔离级别，我们分开讨论，例如：

1. 对于RC（读已提交），每次读取数据都会根据时间戳生**成一个readview**
2. 对于RR（可重复读），在一次事务中，只有第一**次读取时才会生成readview**

- 对于每一个readview，如果要读取的数据的事务id小于min_trx_id，那就一定能读（因为它一定已提交了）
- 如果要读取的事务id大于最大事务值（下一个要生成的事务id），那就一定不能读取（不在当前版本链中）
- 如果要读取的事务id等于当前事务id，说明时线程自己改的值，那么一定能读取
- 如果要读取的事务id在最小与最大之间，看它是否是活跃线程，如果是活跃事务则不读，否则读。

读已提交和不可重复度只是每次生成快**照的时机不同**，他俩判断这个数据该读哪个版本（undolog对于每一条记录的版本链）是**一模一样的判断逻辑**，比如读已提交级别读取一条数据，**这条数据对应的最新版本的事务id在活跃事务列表中，它就会向上查询旧的版本链记录**，直到id小于最小活跃事务。

### 两个关键点：

1. readview一旦生成，再生成新的readview之前，都不会改变，
2. 而undolog版本链是动态的，随时可以变化的

然后，只要是当前读，就按照readview读逻辑和版本链来读取。

而select for update 会查询到当前存在的所有数据。但不会生成版本链（因为没修改数据），所以再快照读可能读到的和同一个事务的for update的读的内容不一样

## mysql解决幻读的问题了吗？

解决了，但只是解决了一定程度上的幻读。如 ，在快照读期间对于一个第一次查不存在的数据，但是别的事务进行了插入，然后穿插一次update 再跟一次select读就会出现幻读现象，读到了第一次读到的数据。

或者刚开启事务后，先进行了一次普通读，然后此时别的事务插入了一条数据，再进行当前读，就能读到其他事务插入的那条数据（当前读会锁住这条记录直至事务提交）

MySQL InnoDB 引擎的可重复读隔离级别（默认隔离级），根据不同的查询方式，分别提出了避免幻读的方案：

- 针对**快照读（**普通 select 语句），是通过 MVCC 方式解决了幻读。
- 针对**当前读（**select ... for update 等语句），是通过 next-key lock（记录锁+间隙锁）方式解决了幻读。

我举例了两个发生幻读场景的例子。

第一个例子：对于快照读， MVCC 并不能完全避免幻读现象。因为当事务 A 更新了一条事务 B 插入的记录，那么事务 A 前后两次查询的记录条目就不一样了，所以就发生幻读。

第二个例子：对于当前读，如果事务开启后，并没有执行当前读，而是先快照读，然后这期间如果其他事务插入了一条记录，那么事务后续使用当前读进行查询的时候，就会发现两次查询的记录条目就不一样了，所以就发生幻读。

所以，**MySQL** **可重复读****隔离级别并没有彻底解决****幻读****，只是很大程度上避免了幻读现象的发生。**

要避免这类特殊场景下发生幻读的现象的话，就是尽量在开启事务之后，马上执行 select ... for update 这类当前读的语句，因为它会对记录加 next-key lock，从而避免其他事务插入一条新记录。

## 索引：

### 什么是索引？

索引是用来方便数据查询的一种结构

### 索引是越多越好吗？

非也，索引创建出来之后，数据的每次插入删除操作，都会使对应字段索引进行相应的更新，这个过程是很耗费资源的。

并非索引越多越好，而是需要在权衡索引的使用和性能之间找到适当的平衡。索引的设计需要根据具体的数据库和查询需求进行考虑。以下是一些需要注意的方面：

1. 查询性能：索引可以显著提高查询性能，特别是在涉及到大量数据的表中。但是，每个索引都会占用存储空间，并在数据更新时引入额外的开销。因此，过多的索引可能导致写入性能下降。
2. 存储空间：每个索引都需要额外的存储空间。如果表的数据量不大，而且查询性能已经足够，可能不需要过多的索引。然而，在大型表中，合理使用索引可以大幅提高查询速度。

### InnoDB中的索引类型

1. 主键索引（Primary Key Index）： 主键索引是用于唯一标识表中每一行的索引。每个InnoDB表都应该有一个主键索引。
   1. 如果没有显式定义主键，InnoDB会选择一个唯一非空的列作为主键，
   2. 如果没有这样的列，则会生成一个隐藏的主键列。

```
sqlCopy code
CREATE TABLE your_table (
  id INT PRIMARY KEY,
  name VARCHAR(255)
);
```

1. 唯一索引（Unique Index）： 唯一索引确保索引列中的所有值都是唯一的，但允许空值。如果一个字段已经有了重复值，那么给他添加唯一索引时会失败

```
sqlCopy code
CREATE TABLE your_table (
  email VARCHAR(255) UNIQUE,
  age INT
);
```

1. 普通索引（Index）： 普通索引是最基本的索引类型，用于加速查询。可以在一个或多个列上创建普通索引。

```
sqlCopy code
CREATE TABLE your_table (
  last_name VARCHAR(255),
  first_name VARCHAR(255),
  INDEX name_index (last_name, first_name)
);
```

1. 全文索引（Full-Text Index）： 全文索引用于在文本数据上执行全文本搜索。只有InnoDB的特定字符集支持全文索引。

```
sqlCopy code
CREATE TABLE your_table (
  content TEXT,
  FULLTEXT INDEX content_index (content)
) ENGINE=InnoDB;
```

1. 空间索引（Spatial Index）： 空间索引用于存储和查询空间数据，例如地理位置信息。

```
sqlCopy code
CREATE TABLE your_table (
  location POINT,
  SPATIAL INDEX location_index (location)
) ENGINE=InnoDB;
```

### 聚簇索引和非聚簇索引的区别？

聚簇索引是指索引和原始数据放在一起的索引，以主键值作为索引值

非聚簇索引是索引和原始数据不在一起，和指向原数据指针存放在一起的索引，以非主键字段作为索引值

### 索引的数据结构，B树和B+树的区别，为什么MYSQL选择了B+树

B+树和跳表的查询复杂度都是O（logN）

### 什么是回表，怎么减少回表次数？

### 什么是索引覆盖，索引下推？

**索引覆盖**是指**sql****语句要查询的字段被索引字段覆盖**，**不用执行回表操**作而是直接将索引数据返回即可

**索引下推** 定义： 索引下推是指数据库查询在执行过程中，尽可能地将**过滤条件**推送到**索引层级**，以减少实际数据访问的数据量。这避免了将所有数据加载到内存中进行过滤，而是在索引层级就过滤掉了不符合条件的数据。

是指在用到联合索引时，用到了其中的多个索引，而查询到的数据需要通过回表来获取，在第一条索引查询到时，不直接回表查询数据进行判断是否符合条件，而是先通过第二个索引判断条件是否符合，如果符合，回表返回原数据，否则跳过这条数据查询下一条

下推是指将**server层的工作下推到引擎层**完成，即条件判断不在server层做，而是放到存储引擎层，server层不做对此的解析工作，引擎层直接过滤就好了，

我们可以从执行计划中的 Extra=**Using index condition** 使用了索引下推功能。

### 索引失效场景？

1. **对索引使用左或者左右模糊匹配即%like 或者%like%** ，原因是什么呢？因为like%这种的话，在第一层能确定哪个区间内，然后在第二层能进一步确认区间，比如林某之类的，而前边带%的，比如某涛，某源，这样子，索引是根据字段首字符排序的，前边如果不确定的话，就要挨个遍历了
2. **对索引使用函数，**比如select where length（name）=4（name字段长为4的）；原因：索引是针对原数据建立的，对于使用函数计算之后的结果自然没法使用
3. **对索引使用表达式，**比如select where age+2=14；原因：基本同上，索引是针对原数据建立的，对于使用表达式 age+2没有建立索引，计算之后的结果自然没法使用。 但是：age=14-2是可以走索引的，因为这个结果的计算不是在索引中进行
4. **对索引隐式类型转换**：如 select where phone=123456 会**隐式的在phone索引字段上使用函数**，**将phone转化为整型**（phone字段表结构定义中是varchar），导致索引失效，而select where id=“1”则不会，因为这个是**针对1进行类型转化**，**将1转化为字符串类**型，**对索引字段没有影响。**（隐式类型转换是通**过 CAST 函数**实现的）
5. **联合索引****非最左匹配：**在使用联合索引时如（a，b，c），然后查询条件没有符合从左到右的查询如使用 where b=1，where c=2这种，但是有a的时候顺序反了没事，**查询优化器**会对此进行优化，除此之外where **a=1 and c=3**这种也可以使用**索引下推**进行优化。
6. **Where** 语句中的or条件，如果or条件两侧有一个字段没有索引，那就会走全盘扫描，因为要判断两者是否满足任意一个，解决方式就是使两个字段都添加索引即可。
7. mysql引擎优化器认为 走全盘扫描效率更高时，也不会走索引

### mysql一定遵循最左前匹配原则吗？

要看怎么定义最左前匹配原则了，如果说使用所有的索引才算遵循的话，那么我想这个问题的答案是no

如果最大限度的利用最左匹配就算遵循，那这个问题答案就是yes

### 锁具体锁的是什么？数据or索引

是索引，根据索引将那条记录上锁，锁间隙等

### 设计索引的时候有哪些原则？

1. 尽量精确而不注重数量
2. 建立索引的字段一般最多不超过6
3. 查询where次数很多的字段建立索引
4. 对于增删改特别频繁的表限制索引的添加
5. 字段重复度较高的值不应设立索引，如男女性别这种
6. 对于不合适的索引考虑建立联合索引或者新建其他索引
7. 尽量为order by groupby后面的字段建立索引

[我面试几乎必问:你设计索引的原则是什么?怎么避免索引失效? - 掘金](https://juejin.cn/post/6960470472077082638)

### sql执行分析的时候应该关注哪些信息？

type字段，看看数据查询是用了怎样的扫描方式，key，看看到用到的索引字段的总字节推断哪些索引生效了，

rows看遍历了多少行数据，extra看看有无额外提供信息

#### extra字段：

1. Using index：
   1. 表示查询使用了覆盖索引（Covering Index），即索引包含了查询所需的所有列，无需回表到原始数据行。这通常是一个好的情况，可以提高查询性能。
2. Using where：
   1. 表示查询中使用了 `WHERE` 子句进行条件过滤。
3. Using temporary：
   1. 表示查询需要使用临时表来存储中间结果集。这通常发生在 `ORDER BY` 和 `GROUP BY` 操作中，可能会影响性能。
4. Using filesort：
   1. 表示查询中使用了文件排序操作，通常发生在 `ORDER BY` 操作中。如果可以避免文件排序，查询性能可能会更好。
5. Using index condition：
   1. 表示查询中使用了索引下推，即通过索引来评估 `WHERE` 子句的部分条件。

### 什么是索引跳跃？

索引跳跃是一种在一定程度上打破了最左原则的机制，比如建立了（a，b）索引，然后执行以下语句 select b where b>5,会被转换成 select b where a=1 and b>5 UNION select b where a=2 and b>5

但是有一定的使用条件

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YTM0Yjg3YWE3NmY4ZDM5N2YzNzEyZjFlZGE2NmI0MGFfVnVUOFJUckJhNTJ3V2FpcGVHbkd1ZmdQVmdORnA3dExfVG9rZW46QndHYmJTNjUxb2l6NDN4RFhPRGNTc1AxbnpoXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

锁具体锁的是什么

## sql执行计划分析的时候，要关注哪些信息？

[SQL执行计划分析 - tiger_Boy - 博客园](https://www.cnblogs.com/tigerBoy/p/8776557.html#:~:text=%E6%89%A7%E8%A1%8C%E8%AE%A1%E5%88%92%E8%83%BD%E5%91%8A%E8%AF%89%E6%88%91%E4%BB%AC%E4%BB%80%E4%B9%88%EF%BC%9F%20EXPLAIN%E5%91%BD%E4%BB%A4%E6%98%AF%E6%9F%A5%E7%9C%8B%E6%9F%A5%E8%AF%A2%E4%BC%98%E5%8C%96%E5%99%A8%E5%A6%82%E4%BD%95%E5%86%B3%E5%AE%9A%E6%89%A7%E8%A1%8C%E6%9F%A5%E8%AF%A2%E7%9A%84%E4%B8%BB%E8%A6%81%E7%9A%84%E6%96%B9%E6%B3%95%EF%BC%8C%E5%AD%A6%E4%BC%9A%E8%A7%A3%E9%87%8AEXPLAIN%E5%B0%86%E5%B8%AE%E5%8A%A9%E6%88%91%E4%BB%AC%E4%BA%86%E8%A7%A3SQL%E4%BC%98%E5%8C%96%E5%99%A8%E6%98%AF%E5%A6%82%E4%BD%95%E5%B7%A5%E4%BD%9C%E7%9A%84%E3%80%82%20%E6%89%A7%E8%A1%8C%E8%AE%A1%E5%88%92%E5%8F%AF%E4%BB%A5%E5%91%8A%E8%AF%89%E6%88%91%E4%BB%ACSQL%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E7%B4%A2%E5%BC%95%EF%BC%8C%E8%BF%9E%E6%8E%A5%E6%9F%A5%E8%AF%A2%E7%9A%84%E6%89%A7%E8%A1%8C%E9%A1%BA%E5%BA%8F%EF%BC%8C%E6%9F%A5%E8%AF%A2%E7%9A%84%E6%95%B0%E6%8D%AE%E8%A1%8C%E6%95%B0%E3%80%82%20%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8%E6%89%A7%E8%A1%8C%E8%AE%A1%E5%88%92%EF%BC%9F,%E8%A6%81%E4%BD%BF%E7%94%A8EXPLAIN%2C%E5%8F%AA%E9%9C%80%E8%A6%81%E5%9C%A8%E6%9F%A5%E8%AF%A2%E7%9A%84SELECT%E5%85%B3%E9%94%AE%E5%AD%97%E4%B9%8B%E5%89%8D%E5%A2%9E%E5%8A%A0EXPLAIN%E8%BF%99%E4%B8%AA%E8%AF%8D%E3%80%82%20%E4%B8%8B%E9%9D%A2%E6%98%AF%E4%B8%80%E4%B8%AA%E7%AE%80%E5%8D%95EXPLAIN%E7%9A%84%E7%BB%93%E6%9E%9C%EF%BC%9A%20%E6%89%A7%E8%A1%8C%E8%AE%A1%E5%88%92%E9%87%8D%E7%82%B9%E5%85%B3%E6%B3%A8%20type%E3%80%81key%E3%80%81key_len%E3%80%81rows%E3%80%81extra)

# java中高级工程师面试自测

## 什么是排他锁，共享锁，意向锁

排他锁，又称独占锁,X锁，一般不能和任意线程或事务的SX锁共享

共享锁，又称S锁，可以和其他S锁共享，不能与X锁共享

意向锁，mysql中想要对某条记录上x锁或者S锁时，先对整个表加一个意向X锁/S锁，意向锁之间不互斥，也不和行级的锁互斥，只和共享表锁，独占表锁互斥

## mysql底层分别是怎么实现ACID的？

A，原子性：原子性要求事务内所有语句要不全都执行，要么全部执行，mysql通过undolog日志，其具有出错回滚特性，以此来实现原子性。

C，一致性：一致性要求数据之间是统一，举个例子，小明少了200元，小刚那边就要多200元，数据不会凭空消失，也不会凭空增加，是通过其他三个特性来保证的

I，隔离性：事务之间的操作是私有的，每个事务操纵自己的数据，相互之间不会互相干扰，对其他事务是不可见的，隔离性是通过mvcc和锁实现的

D，持久性：事务提交后的数据长久不变，不能因为系统故障丢失或没有更新，是通过redolog日志实现的，他能保存提交后的数据备份，在系统崩溃时写入磁盘

## 一条SQL的执行流程

1. 连接器建立连接
2. 解析器解析
3. 生成执行计划
4. 执行语句

首先由连接器校验用户名密码和权限后开始执行，如果时select语句的话先进行缓存查询，通过keyvalue的形式判断是否在缓存中，能找到的话直接返回，mysql8.0后移除了，（因为它并不好用，每次更新都会销毁缓存表），然后由解析器进行解析，包括语法解析，词法解析，生成解析树，方便接下来的表明字段名的使用，然后进入执行流程，包括预处理器预处理，优化器优化，执行器执行，预处理器会检查语句中的表明，字段名是否存在，优化器会根据语句，索引等选择合适的执行计划，执行器根据预先确定好的执行计划执行语句。

# java高级工程师

## MYSQL的几种日志？对应的功能是什么？

查询语句的那一套流程，更新语句也是同样会走一遍：不过，更新语句的流程会涉及到 undo log（回滚日志）、redo log（重做日志） 、binlog （归档日志）这三种日志：

- undo log（回滚日志）：是 Innodb 存储引擎层生成的日志，实现了事务中的**原子性**，主要用于**事务回滚和** **MVCC****。**
- redo log（重做日志）：是 Innodb 存储引擎层生成的日志，实现了事务中的**持久性**，主要用于**掉电等故障恢复；**
- binlog （归档日志）：是 Server 层生成的日志，主要用于**数据备份和主从复制**；

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MmJhOTNkMTVmNTA2ZmUzNTQzZjg2ZGIwMzE0ZmMwYzdfUm9HcUliZ1VlbUw0NkV5T20xUmJpTTk4WGFERWFJNWhfVG9rZW46V3Z3Z2I5Vm9Cb0FTNkl4NjY0M2M0VXI0bkVkXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

### 为什么需要undolog？

如果事务提交之前，系统发生错误，那么要怎么回到之前的状态呢？

 首先总结一下：innodb通过undolog和read view实现MVCC，此外undolog也用来实现事务回滚。

在事务没提交之前，会先生成一个undolog日志，他保存了行数据修改之前的状态，如果这次事务提交之前发生了意外崩溃，undolog日志就会回滚将之前的状态回滚到表中

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=N2NlMGNkNTdkOGJjOTIzODRmZWJkNDc4MDYxZmYwNjNfQ05tNTRsckg0Vk5IcWsxWUE1aTh2eXZ3b3N5SUxCMllfVG9rZW46REZrQ2JiTFJWb1JPVzF4TUh0U2M2Skl2blhjXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

每当 InnoDB 引擎对一条记录进行操作（修改、删除、新增）时，要把回滚时需要的信息都记录到 undo log 里，比如：

- 在插入一条记录时，要把这条记录的主键值记下来，这样之后回滚时只需要把这个主键值对应的记录删掉就好了；
- 在删除一条记录时，要把这条记录中的内容都记下来，这样之后回滚时再把由这些内容组成的记录插入到表中就好了；
- 在更新一条记录时，要把被更新的列的旧值记下来，这样之后回滚时再把这些列更新为旧值就好了

一条记录的每一次更新操作产生的 undo log 格式都有一个 **roll_pointer 指**针和一个 **trx_id 事务id：**

- 通过 trx_id 可以知道该记录是被哪个事务修改的；
- 通过 roll_pointer 指针可以将这些 undo log 串成一个链表，这个链表就被称为**版本链**；

版本链如下图：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NGQ5NDlmOWU3MWZiNGI5YjEwNThhZTg5ZTY5MWJjZTRfcjlST0Z2Q1RLT0pGSnBQNklYTTZzNDAxVzlITGg1ZDdfVG9rZW46TUQzMWIzZkF6b01xbEF4Vm5YNmNhaGVZbmxoXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

另外，undo log 还有一个作用，通过 ReadView + undo log 实现 MVCC（多版本并发控制）。

对于「读提交」和「可重复读」隔离级别的事务来说，它们的快照读（普通 select 语句）是通过 Read View + undo log 来实现的，它们的区别在于创建 Read View 的时机不同：

- 「读提交」隔离级别是在每个 select 都会生成一个新的 Read View，也意味着，**事务期间的多次读取同一条数据，前后两次读的数据可能会出现不一致**，因为可能这期间另外一个事务修改了该记录，并提交了事务。
- 「可重复读」隔离级别是启动事务时生成一个 Read View，然后**整个事务期间都在用这个 Read View，这样就保证了在事务期间读到的数据都是事务启动前的记录**。

### 为什么需要bufferpool

bufferpool缓存池，每次更新一条记录时，将记录从磁盘读取到内存中中修改，修改完直接留在内存中缓存起来，等下次访问数据时，如果能在缓存池中命中则直接返回，不必再去磁盘中查找

为此，Innodb 存储引擎设计了一个缓冲池（Buffer Pool），来提高数据库的读写性能。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YTk2Mzg1YjUwNzdhOTIyMTVhNmNhNjdjMzBmZGUwYWJfNHlsRllyNE9IZFVIbnZjdVZERG5MTkdaTk1JcDlpdm9fVG9rZW46Wkl2c2JieEhab2xUUDF4ak1SVGNqVFpobkplXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

有了 Buffer Pool 后：

- 当读取数据时，如果数据存在于 Buffer Pool 中，客户端就会直接读取 Buffer Pool 中的数据，读不到则再去磁盘中读取。
- 当修改数据时，如果数据存在于 Buffer Pool 中，那直接修改 Buffer Pool 中数据所在的页，然后将其页设置为脏页（**该页的****内存****数据和磁盘上的数据已经不一致），为了减少磁盘****I/O****，不会立即将脏页写入磁盘，后续由后台线程选择一个合适的时机将脏页写入到磁盘。**体会这句话 读取更新都是以页为单位，页是存储引擎操纵数据的最小单位

#### [#](https://xiaolincoding.com/mysql/log/how_update.html#buffer-pool-%E7%BC%93%E5%AD%98%E4%BB%80%E4%B9%88)Buffer Pool 缓存什么？

InnoDB 会把存储的数据划分为若干个「页」，以**页作为磁盘和****内存****交互的基本单位**，一个页的默认大小为 16KB。因此，Buffer Pool 同样需要按「页」来划分。

在 MySQL 启动的时候，**InnoDB 会为 Buffer Pool 申请一片连续的****内存****空间，然后按照默认的****16KB****的大小划分出一个个的页， Buffer Pool 中的页就叫做缓存页**。此时这些缓存页都是空闲的，之后随着程序的运行，才会有磁盘上的页被缓存到 Buffer Pool 中。

Buffer Pool 除了缓存「索引页」和「数据页」，还包括了 Undo 页，插入缓存、自适应哈希索引、锁信息等等。

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=N2U1Y2Y2OWUxODhmNzhiY2JjNDc4NmY3NGViZWRmMWVfWlB5RTRMaklPSU52aTV1aTFzTmNVZVlpb0s4TjlVTjNfVG9rZW46TG9vT2JLSHl0b09FRDB4dEtsZWNiVlVEbkFmXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

Undo 页是记录什么？

开启事务后，InnoDB 层更新记录前，首先要记录相应的 undo log，如果是更新操作，需要把被更新的列的旧值记下来，也就是要生成一条 undo log，undo log 会写入 Buffer Pool 中的 Undo 页面。

### 为什么需要 redo log ？

buffer pool将要更新的脏页放到内存中，固然提升了性能，但是内存是具有断电丢失的特性的，如何保证在断电后，已提交还未落盘的数据不会丢失呢？ InnoDB引擎是这样做的，为了防止断电导致数据丢失的问题，当有一条记录**需要更新**的时候，InnoDB 引擎就会先**更新内存（同时标记为脏页）**，然后将本次对这个页的修改以 **redo** **log** **的形式**记录下来，**这个时候更新就算完成**了。 后续，InnoDB 引擎会在适当的时候，由后台线程将缓存在 Buffer Pool 的脏页刷新到磁盘里，这就是 **WAL** **（****Write-Ahead Logging****）技术。** WAL 技术指的是， **MySQL** **的写操作并不是立刻写到磁盘上，而是先写日志，然后在合适的时间再写到磁盘上。** 过程如下图：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=N2I4ODgwYzA5ZTNlYzZhOTBmMzI1ODRmNzM1N2IwMTZfUGJKVGdXV2hpNWhLSnNaUlp3UFVsYnU5SnNHMjVyekRfVG9rZW46REdlQ2JVUXU5b09sR0N4ZzNpWmNZSU9ObnVoXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

什么是 redo log？

redo log 是物理日志，记录了某个数据页做了什么修改，比如**对 XXX** **表空间****中的 YYY 数据页 ZZZ 偏移量的地方做了****AAA** **更新**，每当执行一个事务就会产生这样的一条或者多条物理日志。

在**事务提交**时，只要**先将 redo** **log** **持久化到磁盘**即可，可以**不需要等到将缓存在 Buffer Pool 里的****脏页****数据持久化到磁盘**。

当系统崩溃时，虽然**脏页****数据没有持久化，但是 redo** **log** **已经持久化**，接着 MySQL 重启后，可以根据 redo log 的内容，将所有数据恢复到最新的状态。

被修改 Undo 页面，需要记录对应 redo log 吗？

需要的。

开启事务后，InnoDB 层更新记录前，首先要记录相应的 undo log，如果是更新操作，需要把被更新的列的旧值记下来，也就是要生成一条 undo log，**undo log** **会写入 Buffer Pool 中的** **Undo 页面。**

不过，**在****内存****修改该 Undo 页面后，需要记录对应的 redo** **log****。**

这两种日志是属于 InnoDB 存储引擎的日志，它们的区别在于：

- redo log 记录了此次事务**「完成后」**的数据状态，记录的**是更新之后**的值；
- undo log 记录了此次事务**「开始前」**的数据状态，记录的是**更新之前**的值；

事务提交之前发生了崩溃，重启后会通过 undo log 回滚事务，事务提交之后发生了崩溃，重启后会通过 redo log 恢复事务，如下图：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NjU2MGEyMjFiNTQxNDEyZTZkNjA3YTMxY2FjNjNiYzRfTXRZM214VU5iQ1FFT0g0anpORXZHeEd5TkZhNUVWY2pfVG9rZW46RTVFNmJXNlFqb1p6MzB4RDl1dmNJU3dabnVlXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

所以有了 redo log，再通过 WAL 技术，InnoDB 就可以保证即使数据库发生异常重启，之前已提交的记录都不会丢失，这个能力称为 **crash-safe（崩溃恢复）**。可以看出来， **redo log 保证了事务四大特性中的持久性。**

- 实现事务的持久性，让 MySQL 有 crash-safe 的能力，能够保证 MySQL 在任何时间段突然崩溃，重启后之前已提交的记录都不会丢失；
- 将写操作从「随机写」变成了「顺序写」，提升 MySQL 写入磁盘的性能

缓存在 redo log buffer 里的 redo log 还是在内存中，它什么时候刷新到磁盘？

主要有下面几个时机：

- MySQL 正常关闭时；
- 当 redo log buffer 中记录的写入量大于 redo log buffer 内存空间的一半时，会触发落盘；
- InnoDB 的后台线程每隔 1 秒，将 redo log buffer 持久化到磁盘。
- 每次事务提交时都将缓存在 redo log buffer 里的 redo log 直接持久化到磁盘（这个策略可由 innodb_flush_log_at_trx_commit 参数控制，下面会说）。

### 为什么需要 binlog ？

前面介绍的 undo log 和 redo log 这两个日志都是 Innodb 存储引擎生成的。

MySQL 在完成一条更新操作后，Server 层还会生成一条 binlog，等之后事务提交的时候，会将该事物执行过程中产生的所有 binlog 统一写 入 binlog 文件。

binlog 文件是记录了所有**数据库表结构变更和表数据修改的日志**，**不会记录查询类**的操作，比如 SELECT 和 SHOW 操作。

为什么有了 binlog， 还要有 redo log？

这个问题跟 MySQL 的时间线有关系。

最开始 MySQL 里并没有 InnoDB 引擎，MySQL 自带的引擎是 MyISAM，但是 MyISAM 没有 crash-safe 的能力，binlog 日志只能用于归档。

而 InnoDB 是另一个公司以插件形式引入 MySQL 的，既然只依靠 binlog 是没有 crash-safe 能力的，所以 InnoDB 使用 redo log 来实现 crash-safe 能力。

#### [#](https://xiaolincoding.com/mysql/log/how_update.html#redo-log-%E5%92%8C-binlog-%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)redo log 和 binlog 有什么区别？

这两个日志有四个区别。

**\*1、适用对象不同：***

- binlog 是 MySQL 的 Server 层实现的日志，所有存储引擎都可以使用；
- redo log 是 Innodb 存储引擎实现的日志；

**\*2、文件格式不同：***

- binlog 有 3 种格式类型，分别是 STATEMENT（默认格式）、ROW、 MIXED，区别如下：
  - STATEMENT：每一条修改数据的 SQL 都会被记录到 binlog 中（相当于记录了逻辑操作，所以针对这种格式， binlog 可以称为逻辑日志），主从复制中 slave 端再根据 SQL 语句重现。但 STATEMENT 有动态函数的问题，比如你用了 uuid 或者 now 这些函数，你在主库上执行的结果并不是你在从库执行的结果，这种随时在变的函数会导致复制的数据不一致；
  - ROW：记录行数据最终被修改成什么样了（这种格式的日志，就不能称为逻辑日志了），不会出现 STATEMENT 下动态函数的问题。但 ROW 的缺点是每行数据的变化结果都会被记录，比如执行批量 update 语句，更新多少行数据就会产生多少条记录，使 binlog 文件过大，而在 STATEMENT 格式下只会记录一个 update 语句而已；
  - MIXED：包含了 STATEMENT 和 ROW 模式，它会根据不同的情况自动使用 ROW 模式和 STATEMENT 模式；
- redo log 是物理日志，记录的是在某个数据页做了什么修改，比如对 XXX 表空间中的 YYY 数据页 ZZZ 偏移量的地方做了AAA 更新；

**\*3、写入方式不同：***

- binlog 是追加写，写满一个文件，就创建一个新的文件继续写，不会覆盖以前的日志，保存的是全量的日志。
- redo log 是循环写，日志空间大小是固定，全部写满就从头开始，保存未被刷入磁盘的脏页日志。

**\*4、用途不同：***

- binlog 用于备份恢复、主从复制；
- redo log 用于掉电等故障恢复。

如果不小心整个数据库的数据被删除了，能使用 redo log 文件恢复数据吗？

不可以使用 redo log 文件恢复，只能使用 binlog 文件恢复。

因为 redo log 文件是循环写，是会边写边擦除日志的，只记录未被刷入磁盘的数据的物理日志，已经刷入磁盘的数据都会从 redo log 文件里擦除。

binlog 文件保存的是全量的日志，也就是保存了所有数据变更的情况，理论上只要记录在 binlog 上的数据，都可以恢复，所以如果不小心整个数据库的数据被删除了，得用 binlog 文件恢复数据

#### 三个日志讲完了，至此我们可以先小结下，update 语句的执行过程。

当优化器分析出成本最小的执行计划后，执行器就按照执行计划开始进行更新操作。

具体更新一条记录 `UPDATE t_user SET name = 'xiaolin' WHERE id = 1;` 的流程如下:

1. 执行器负责具体执行，会调用存储引擎的接口，通过主键索引树搜索获取 id = 1 这一行记录：
   1. 如果 id=1 这一行所在的数据页本来就在 buffer pool 中，就直接返回给执行器更新；
   2. 如果记录不在 buffer pool，将数据页从磁盘读入到 buffer pool，返回记录给执行器。
2. 执行器得到聚簇索引记录后，会看一下更新前的记录和更新后的记录是否一样：
   1. 如果一样的话就不进行后续更新流程；
   2. 如果不一样的话就把更新前的记录和更新后的记录都当作参数传给 InnoDB 层，让 InnoDB 真正的执行更新记录的操作；
3. 开启事务， InnoDB 层更新记录前，首先要记录相应的 undo log，因为这是更新操作，需要把被更新的列的旧值记下来，也就是要生成一条 undo log，undo log 会写入 Buffer Pool 中的 Undo 页面，不过在内存修改该 Undo 页面后，需要记录对应的 redo log。
4. InnoDB 层开始更新记录，会先更新内存（同时标记为脏页），然后将记录写到 redo log 里面，这个时候更新就算完成了。为了减少磁盘I/O，不会立即将脏页写入磁盘，后续由后台线程选择一个合适的时机将脏页写入到磁盘。这就是 WAL 技术，MySQL 的写操作并不是立刻写到磁盘上，而是先写 redo 日志，然后在合适的时间再将修改的行数据写到磁盘上。
5. 至此，一条记录更新完了。
6. 在一条更新语句执行完成后，然后开始记录该语句对应的 binlog，此时记录的 binlog 会被保存到 binlog cache，并没有刷新到硬盘上的 binlog 文件，在事务提交时才会统一将该事务运行过程中的所有 binlog 刷新到硬盘。
7. 事务提交，剩下的就是「两阶段提交」的事情了，接下来就讲这个

### 为什么需要两阶段提交

事务提交后，redo log 和 binlog 都要持久化到磁盘，但是这两个是独立的逻辑，可能出现半成功的状态，这样就造成两份日志之间的逻辑不一致。

举个例子，假设 id = 1 这行数据的字段 name 的值原本是 'jay'，然后执行 `UPDATE t_user SET name = 'xiaolin' WHERE id = 1;` 如果在持久化 redo log 和 binlog 两个日志的过程中，出现了半成功状态，那么就有两种情况：

- **如果在将 redo** **log** **刷入到磁盘之后，** **MySQL** **突然宕机了，而 binlog 还没有来得及写入**。MySQL 重启后，通过 redo log 能将 Buffer Pool 中 id = 1 这行数据的 name 字段恢复到新值 xiaolin，但是 binlog 里面没有记录这条更新语句，在主从架构中，binlog 会被复制到从库，由于 binlog 丢失了这条更新语句，从库的这一行 name 字段是旧值 jay，**与主库的值不一致性**；
- **如果在将 binlog 刷入到磁盘之后，** **MySQL** **突然宕机了，而 redo** **log** **还没有来得及写入**。由于 redo log 还没写，崩溃恢复以后这个事务无效，所以 id = 1 这行数据的 name 字段还是旧值 jay，而 binlog 里面记录了这条更新语句，在主从架构中，binlog 会被复制到从库，从库执行了这条更新语句，那么这一行 name 字段是新值 xiaolin，与**主库的值不一致性**；

可以看到，在持久化 redo log 和 binlog 这两份日志的时候，如果出现半成功的状态，就会造成**主从环境的数据不一致性**。这是**因为 redo log 影响主库的数据，binlog 影响从库的数据，所以 redo log 和 binlog 必须保持一致才能保证主从数据一致**。

事务的两阶段提交是将redolog和binlog的写入提交分为两个阶段，prepare阶段和commit，

**在prepare阶段**将 **XID（内部 XA 事务的 ID）** 写入到 redo log，同时将 redo log 对应的事务状态设置为 prepare，然后将 redo log 持久化到磁盘（innodb_flush_log_at_trx_commit = 1 的作用）；

**在commit阶段**

- 把 XID 写入到 binlog，然后将 binlog 持久化到磁盘（sync_binlog = 1 的作用），接着调用引擎的提交事务接口，将 redo log 状态设置为 commit，此时该状态并不需要持久化到磁盘，只需要 write 到文件系统的 page cache 中就够了，因为只要 binlog 写磁盘成功，就算 redo log 的状态还是 prepare 也没有关系，一样会被认为事务已经执行成功；

### 异常重启会出现什么现象？

我们来看看在两阶段提交的不同时刻，MySQL 异常重启会出现什么现象？下图中有时刻 A 和时刻 B 都有可能发生崩溃：

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=YTllZGNmOTFjN2Y0Y2QxZDM5MjhiY2RlYjYyNTFjMGNfaGFFaTZydmFzUXZwOWZoQk4wQjR4VG5QeVh2MGp4TjVfVG9rZW46SXhKR2J0MzdWb1NiU0d4dUpKS2NQZzA1bmljXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

不管是时刻 A（redo log 已经写入磁盘， binlog 还没写入磁盘），还是时刻 B （redo log 和 binlog 都已经写入磁盘，还没写入 commit 标识）崩溃，此时的 redo log 都处于 prepare 状态。

在 MySQL 重启后会按顺序扫描 redo log 文件，碰到处于 prepare 状态的 redo log，就拿着 redo log 中的 XID 去 binlog 查看是否存在此 XID：

- 如果 binlog 中没有当前内部 XA 事务的 XID，说明 redolog 完成刷盘，但是 binlog 还没有刷盘，则回滚事务。对应时刻 A 崩溃恢复的情况。
- 如果 binlog 中有当前内部 XA 事务的 XID，说明 redolog 和 binlog 都已经完成了刷盘，则提交事务。对应时刻 B 崩溃恢复的情况。

可以看到，对于处于 prepare 阶段的 redo log，即可以提交事务，也可以回滚事务，这取决于是否能在 binlog 中查找到与 redo log 相同的 XID，如果有就提交事务，如果没有就回滚事务。这样就可以保证 redo log 和 binlog 这两份日志的一致性了。

所以说，两阶段提交是以 binlog 写成功为事务提交成功的标识，因为 binlog 写成功了，就意味着能在 binlog 中查找到与 redo log 相同的 XID。

## 死锁的问题，行锁的理解

死锁是

## 如何查看执行慢的sql

开启慢sql日志查询功能，它是mysql提供的一种帮忙查看执行时间超过阈值时间的日志，默认是关闭的，需要我们手动开启

## **MySQL****如果查询压力太大该怎么办？**

如果SQL语句已经足够优秀。那么就看请求压力是否符合二八原则，也就是说80%的压力都集中在20%的数据。

如果是，我们可以增加一层缓存，常用的实现是在MySQL前加个Redis缓存。当然，如果实在太大了，那么只能考虑分库分表啦。

## 模拟面试视频中补充问题：

### char和varchar的区别？

1. 长度不同：char是固定长度，而varchar是变长字符串
2. 使用场景不同，**char****存储的长度固定**，不会产生额外的空间碎片，所以它适合**存储固定长度的字符**，如**性别，日期，md5格式的密**码，查询效率高，而varchar适合存储长度经常变化的字符串
3. 存储不同，因为char是固定长度加上一个字节记录字符串长度，而varchar是字符串自身长度加上一个字节记录字符串长度
4. 一般来说char查找效率高于varchar

char最大字符255，varchar最大是 65535字节注意区别，一个是字符，一个是字节

在mysql中utf-8字符集下，每个**字符占1-4个字节**，如果定义**varchar（65535**），这在mysql的定义是指这个varchar类型字段最多可以存储65535个字节，注意是字节**不是字符**，所以这很明显是不合理的

Bob是三个字符，hello是5个字符

### DateTime，TimeStamp的区别？

相同点：

1. 两个类型存储的时间格式都一样，都是YYYY-MM-DD HH：mm：ss
2. 两个都包含日期和时间
3. 都精确到小数点后6位

不同点

1. 表示的时间范围不一样，DateTime是`1000-01-01 00:00:00.000000` 到 `9999-12-31 23:59:59.999999`

TimeStamp是1970到2038年

1. 空间占用，DateTime5.6.4之前占用8个字节，之后占用5个 TimeStamp占用四个
2. Datatime不会做时间转换，存储什么值就是什么值，而TimeStamp会做时区的转换按照UTC（可以答这个）

### 关于快照读和当前读

快照读就是读事务生成的readview快照，通过具体情况判断要读哪个版本的数据，所以快照读

### mysql分区表

[MySQL分区表：优化大型数据库性能的关键](https://zhuanlan.zhihu.com/p/635859056)

### 行溢出：

mysql的页中存放了

一个数据页拆分成了很多个部分：“文件头，数据页头，最小记录和最大记录，多个数据行，空闲空间，数据页目录，文件尾部”；【如图】

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=NzMwZjNmY2IxMTIyZGU3ZWYyZTA1ZGM5OWMxNDA4YWFfOE9DYzM1WHZ3eVV4N3JOemFyd3JqVWdGWnJlM3BWYlBfVG9rZW46VlJ4QWI3MVo2b2M5Mm94M1MyR2NhN1IxbnBnXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

而存储一个例如大varchar或者text字段，一行放不下，就会留有一部分大概20B的数据存放指针，指向其他数据页

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2ZkZGMwMDQ4MDVmYzc1NGMwYTBmY2ZlMDY5MWIwOGZfeExLaEpua09jYUJwSUtrRjN2WU45ODdxQWFuQ3JSZnVfVG9rZW46U3E2bWJiTFBNb3RYYmF4Vk9EY2NzU0lFbnJoXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

### 实现数据库备份

开启binlog日志，配备主从复制

### 大表如何实现分页查询

[cloud.tencent.com](https://cloud.tencent.com/developer/article/1884103)

mysql分页问题是指limit语句用来对大批量数据做偏移量查询时 如：

Select id，name，balance from account where update_time>"2020-09-19" limit 100000,10

从更新日期大于“2020-09-19"的数据中查询100000条，再往后取10条。这种正常的查询过程，耗时非常严重。原因是他的过程

 \1. 依据update_time索引查询出符号条件的id 

 2.回表找到满足记录的数据

 \3. 找到100010条，返回后十条，扔掉其他 所以这种**根据建立的索引查出，然后回表查询所有数据，返回后几条数据的**操作，十分麻烦。

所以针对于limit offset n，要回表offset+n次查询数据

解决方式：

1. 子查询：通过子查询查询自己的表，查找出第100001条符合条件的数据，返回它的id值，主查询拿到这个值直接再往后查询10条符合条件的
2. 延迟自关联查询：将自表设置为关联表，查询出100001-100010条符合条件的id与主表建立关联，返回主表的符号这些数据的id条件的字段
3. 标签记录法：根据某个具有自增属性的字段直接定位到要开始分页的那条数据向后取10条，如where id>10000 and update_time>”2020-09-19“ limit 10

1，2条是解决了回表次数过多，只需要回表指定的条数的次数的表就可以

3 是直接先定位到要分页的数据处，再进行查询

### sql注入

### 为什么要小表驱动大表？

小表驱动大表的本质就是，连接查询中，减少连表次数，驱动表是外层循环，被驱动表是内层循环，用驱动表的数据去匹配被驱动表，当驱动表数据量小（如20条），被驱动表数据量大（20万），只需要做20次连接即可，而大表做驱动表，则要进行20万次连接

#### In和Exists的区别：

in后E前口诀，in语句时，小表放到in后面，因为in语法是先执行in后面的子查询，拿着子查询去主查询匹配。

exists语法中，小表放到exists关键字前面， 因为exists是先执行前面语句，然后从exists后面判断是否为true

### 连表查询的原理？

[join连接的底层原理](https://zhuanlan.zhihu.com/p/150420895#:~:text=INNER,JOIN%EF%BC%9A%E6%9F%A5%E8%AF%A2%E4%B8%A4%E4%B8%AA%E8%A1%A8%E4%B9%8B%E9%97%B4%E7%9A%84%E4%BA%A4%E9%9B%86%20%E5%8F%96%E5%80%BC%E6%97%B6%E9%81%B5%E5%BE%AA%E7%AC%9B%E5%8D%A1%E5%B0%94%E4%B9%98%E7%A7%AF%EF%BC%8C%E5%8D%B3%E5%88%A9%E7%94%A8%E5%8F%8C%E5%B1%82%E5%BE%AA%E7%8E%AF%E9%81%8D%E5%8E%86%E4%B8%A4%E4%B8%AA%E8%A1%A8%E7%9A%84%E6%95%B0%E6%8D%AE%EF%BC%8C%E8%8B%A5table1%E7%9A%84%E7%BB%93%E6%9E%9C%E9%9B%86%E6%AF%94%E8%BE%83%E5%B0%91%EF%BC%8C%E9%82%A3%E4%B9%88%E5%B0%B1%E6%8B%BF%E5%AE%83%E5%BD%93%E4%BD%9C%E5%A4%96%E5%B1%82%E5%BE%AA%E7%8E%AF%EF%BC%8C%E7%A7%B0%E4%B8%BA%E9%A9%B1%E5%8A%A8%E8%A1%A8%EF%BC%8C%E5%A4%96%E5%B1%82%E5%BE%AA%E7%8E%AF%E6%AF%8F%E5%8F%96%E4%B8%80%E6%9D%A1%E6%95%B0%E6%8D%AE%EF%BC%8C%E5%B0%B1%E6%8B%BF%E8%AF%A5%E6%95%B0%E6%8D%AE%E5%8E%BB%E5%86%85%E5%B1%82%E5%BE%AA%E7%8E%AFtable2%E8%A1%A8%E4%B8%AD%E5%8C%B9%E9%85%8D%E7%BB%93%E6%9E%9C%E9%9B%86%EF%BC%8C%E6%AD%A4%E6%97%B6table2%E7%A7%B0%E4%B8%BA%E8%A2%AB%E9%A9%B1%E5%8A%A8%E8%A1%A8)

### 两阶段提交

[全网最牛X的！！！MySQL两阶段提交串讲](https://zhuanlan.zhihu.com/p/343449447)

### WAL

### 主从复制作读写分离，怎么保证数据及时更新到从库的，从从库中查数据，数据还没有同步过去

二、怎么保证从数据库读到最新的数据

在高并发场景或者网络不佳的场景，如果存在较大的主从同步数据延迟，这时候读请求去读从库，就会读到旧数据。这时候最简单暴力的方法，就是强制读主库。但是这样就违背了读写分离的初衷。

优化方案就是使用**缓存标记法**：

1. A发起写请求，更新主库数据，并在缓存中设置一个标记，表示数据已更新，设置一个唯一标记ID比如：userId+buziId。
2. 设置此标记，设置过期时间（主库和从库同步延迟的时间，这是个预估的时间值）
3. B发起读请求，先判断此请求，在缓存中有没有更新标记。
4. 如果存在标记，走主库；如果没有，请求走从库。

**本地缓存****标记**

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MjkzMWIyNDdkZDYwNTM3NjRhYzAxMGFiYmNlYmQxOGZfQTltUkFmaWZ5YVNiRlJNeFhScTNmNE1OQ2tIYnRzOVZfVG9rZW46TVBhUmJIdWR3b08wQlB4Z1Z2dWNQaDFobmRjXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

上图流程： 1）用户A发起写请求，更新了主库，并在客户端设置标记，过期时间（预估的主库和从库同步延迟的时间），可以使用cookie实现 2）用户A再发起读请求时，带上这个cookie 3）服务器处理请求时，获取请求传过来的数据，看有没有这个标记 4）有这个业务标记，走主库；没有走从库。

这个方案就保证了用户A的读请求肯定是数据一致的，而且没有性能问题，因为标记是本地客户端传过去的。 但是无法保证其他用户读数据是一致的，但是实际场景很少需要保持其他用户也保持强一致。延迟个几秒也没问题。

[mysql读写分离，以及保证保持数据一致性实现方案_读写分离怎么保证数据同步-CSDN博客](https://blog.csdn.net/JeekMrc/article/details/118887359)

### mysql中间件

帮忙做mysql的一些只能代理，主从配置，主从心跳检测切换

mycat

sharding jdbc

### union和union all的区别

union是将两个查询的语句的结果合并产出

Union会去重 unionall不会去重

### 为什么使用自增id当主键：

回答要点 页分裂

1. **可以避免页分裂**，页分裂就是指不使用自增主键id时，在数据超出页的大小时，不会像使用自增主键id一样，多余的数据直接写到另一张页，而是附带一些表数据的更改过去（就是指将上个数据页的部分数据捎带过去），这就是页分裂，页分裂会极大的降低性能。

> 使用自增主键有助于减少页分裂的发生，这是因为自增主键的插入模式有助于数据在数据库表中保持有序，避免在插入新记录时破坏数据页的顺序。
>
> 以下是自增主键减少页分裂的原因：
>
> 1. 有序插入： 自增主键的值通常是按顺序递增的。这导致新插入的记录很可能会被放置在表的末尾，而不是在表的中间或开头。这有助于避免在插入时破坏现有数据页的结构。
> 2. 数据页的紧凑性： 由于自增主键的有序性，新插入的记录通常会被放置在当前数据页的末尾，而不是在已满的数据页中间。这有助于保持数据页的紧凑性，减少了分裂的可能性。
> 3. 减少数据移动： 自增主键通常意味着新插入的记录会被放在表的末尾，而不需要移动现有记录。相比之下，如果主键是随机的或者没有明确的顺序，插入新记录可能需要移动现有记录，从而引发页分裂。
> 4. 更好的索引性能： 自增主键通常对于索引的性能更有利。数据库引擎可以更容易地管理和维护递增的整数索引，这有助于提高查询的速度。
>
> 尽管自增主键有这些优势，但在某些情况下，可能会选择其他类型的主键。例如，在需要使用自然键（natural key）的情况下，可能会选择业务逻辑中的一个有意义的唯一标识符。在选择主键类型时，需要综合考虑具体的应用需求和数据库设计的目标。

1. **id字段比较短**，效率比较快，如果用一些业务号，订单号当主键，字段值太长了，速率较低
2. **能保证顺序性，用**其他比如身份证号这种就不能保证这个特性，从而避免索引结构的不断改变

### 所有索引失效的场景都会被覆盖索引补救回来

![img](https://g1dqj43ocbb.feishu.cn/space/api/box/stream/download/asynccode/?code=MjUzY2Q4ODhhYmVhMjAwNmQ3NTRkY2JkNzgwMTYxM2JfMWpNZkx6MGFnYnkwbXNNVVY1S1RIbWt3MUtnc003MjdfVG9rZW46S1lQQmJlQmN5b2hSaEx4RFhKSWNPS21rblVmXzE3MTE5NjEwODY6MTcxMTk2NDY4Nl9WNA)

### mysql的count（*）（1）（column）有什么区别：

在大多数数据库中，`COUNT`函数用于计算行的数量。在使用`COUNT`时，有三种常见的方式：

1. `COUNT(*)`: 这会计算满足条件的所有行的数量，不考虑任何列的值。因为不涉及具体的列，它通常是最快的方式，因为数据库引擎不需要检查具体的列值。

```
sqlCopy code
SELECT COUNT(*) FROM your_table WHERE your_condition;
```

1. `COUNT(1)`: 这与`COUNT(*)`几乎相同，它也计算所有满足条件的行的数量。在实际中，数据库引擎通常会将`COUNT(1)`优化成`COUNT(*)`，因此在性能上几乎没有差异。

```
sqlCopy code
SELECT COUNT(1) FROM your_table WHERE your_condition;
```

1. `COUNT(column)`: 这会计算指定列（`column`）非空的行的数量。如果你想统计某一列非空值的数量，可以使用这种形式。

### DDL,DML,DQL,DCL等那四个

ddl：数据定义语言，更改表结构用到的，alter这种

dml：数据操纵语言，增删改，对数据进行这个，包括insert，update，delete

dql：数据查询语言，数据查询相关的，select，where，from

dcl：数据控制语言，通常用来设置修改数据库用户权限用的，比如GRANT （授权），REVOKE （取消权限）

### mysql8新特性

### mysql集群部署的方式有哪些

（2024-01-06晚罗刚面试代苏骞mysql）

### mysql行锁什么时候升级成表锁

（2024-01-06晚罗刚面试代苏骞mysql）

### insert等其他更新语句的执行流程

（2024-01-06晚罗刚面试代苏骞mysql）

## 问题：

解决快照程度下的幻读到底是什么意思（好像知道了）

redolog有日志磁盘文件，有redolog buffer cache undolog有undo区，也有undolog文件，binlog有缓存区也有binlog文件，各自什么时候写入缓存，又什么时候写入文件

## 相互提问： note.md…]()
