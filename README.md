总结Java开发技术面试常问的问题，持续更新中~

* [数据库](#数据库)
    * [MySQL](#MySQL)
    * [Redis](#Redis)
* [Java](#Java)
    * [Java虚拟机](#Java虚拟机)
    * [Java并发](#Java并发)
    * [Java容器](#Java容器)
    * [Java基础](#Java基础)
* [计算机网络](#计算机网络)
* [数据结构与算法](#数据结构与算法)
* [Linux](#Linux)
* [设计模式](#设计模式)
* [场景题和设计题](#场景题和设计题)

-------------------
# 数据库


----------


## MySQL

----------
### 四种隔离级别
① Serializable (串行化)：可避免脏读、不可重复读、幻读的发生。
② Repeatable read (可重复读)：可避免脏读、不可重复读的发生。
③ Read committed (读已提交)：可避免脏读的发生。
④ Read uncommitted (读未提交)：最低级别，任何情况都无法保证。


----------
### 什么是存储过程

简单的说，就是一组SQL语句集，功能强大，可以实现一些比较复杂的逻辑功能，类似于JAVA语言中的方法；

ps:存储过程跟触发器有点类似，都是一组SQL集，但是存储过程是主动调用的，且功能比触发器更加强大，触发器是某件事触发后自动调用；

有哪些特性
1、有输入输出参数，可以声明变量，有if/else, case,while等控制语句，通过编写存储过程，可以实现复杂的逻辑功能；
2、函数的普遍特性：模块化，封装，代码复用；
3、速度快，只有首次执行需经过编译和优化步骤，后续被调用可以直接执行，省去以上步骤；

```
DROP PROCEDURE IF EXISTS `proc_adder`;
DELIMITER ;;
CREATE DEFINER=`root`@`localhost` PROCEDURE `proc_adder`(IN a int, IN b int, OUT sum int)
BEGIN
    #Routine body goes here...

    DECLARE c int;
    if a is null then set a = 0; 
    end if;
  
    if b is null then set b = 0;
    end if;

    set sum  = a + b;
END
;;
DELIMITER ;

set @b=5;
call proc_adder(0,@b,@s);
SELECT @s as sum;



create table tab2(
   tab2_id varchar(11)
);

DROP TRIGGER if EXISTS t_ai_on_tab1;
create TRAILING t_ai_on_tab1
AFTER INSERT ON tab1
for EACH ROW
BEGIN
   INSERT INTO tab2(tab2_id) values(new.tab1_id);
end;

INSERT INTO tab1(tab1_id) values('0001');

SELECT * FROM tab2;
```


----------
### 行级锁定的优点：
·         当在许多线程中访问不同的行时只存在少量锁定冲突。
·         回滚时只有少量的更改。9哦了，
·         可以长时间锁定单一的行。
行级锁定的缺点：
·         比页级或表级锁定占用更多的内存。
·         当在表的大部分中使用时，比页级或表级锁定速度慢，因为你必须获取更多的锁。
·         如果你在大部分数据上经常进行GROUP BY操作或者必须经常扫描整个表，比其它锁定明显慢很多。
·         用高级别锁定，通过支持不同的类型锁定，你也可以很容易地调节应用程序，因为其锁成本小于行级锁定。

1、共享锁：读
2、排他锁：删除、更新



----------
### MySQL 触发器简单实例
CREATE TRIGGER <触发器名称>  --触发器必须有名字，最多64个字符，可能后面会附有分隔符.它和MySQL中其他对象的命名方式基本相象.
{ BEFORE | AFTER }  --触发器有执行的时间设置：可以设置为事件发生前或后。
{ INSERT | UPDATE | DELETE }  --同样也能设定触发的事件：它们可以在执行insert、update或delete的过程中触发。
ON <表名称>  --触发器是属于某一个表的:当在这个表上执行插入、 更新或删除操作的时候就导致触发器的激活. 我们不能给同一张表的同一个事件安排两个触发器。
FOR EACH ROW  --触发器的执行间隔：FOR EACH ROW子句通知触发器 每隔一行执行一次动作，而不是对整个表执行一次。
<触发器SQL语句>  --触发器包含所要触发的SQL语句：这里的语句可以是任何合法的语句， 包括复合语句，但是这里的语句受的限制和函数的一样。

--你必须拥有相当大的权限才能创建触发器（CREATE TRIGGER），如果你已经是Root用户，那么就足够了。这跟SQL的标准有所不同。


----------


### MySQL优化
1、开启查询缓存，优化查询
2、explain你的select查询，这可以帮你分析你的查询语句或是表结构的性能瓶颈。EXPLAIN 的查询结果还会告诉你你的索引主键被如何利用的，你的数据表是如何被搜索和排序的
3、当只要一行数据时使用limit 1，MySQL数据库引擎会在找到一条数据后停止搜索，而不是继续往后查少下一条符合记录的数据
4、为搜索字段建索引
5、使用 ENUM 而不是 VARCHAR，如果你有一个字段，比如“性别”，“国家”，“民族”，“状态”或“部门”，你知道这些字段的取值是有限而且固定的，那么，你应该使用 ENUM 而不是VARCHAR。
6、Prepared Statements
Prepared Statements很像存储过程，是一种运行在后台的SQL语句集合，我们可以从使用 prepared statements 获得很多好处，无论是性能问题还是安全问题。Prepared Statements 可以检查一些你绑定好的变量，这样可以保护你的程序不会受到“SQL注入式”攻击
7、垂直分表
8、选择正确的存储引擎


----------
### key和index的区别
1 ：key 是数据库的物理结构，它包含两层意义和作用，一是约束（偏重于约束和规范数据库的结构完整性），二是索引（辅助查询用的）。包括primary key, unique key, foreign key 等
2： index是数据库的物理结构，它只是辅助查询的，它创建时会在另外的表空间（mysql中的innodb表空间）以一个类似目录的结构存储。索引要分类的话，分为前缀索引、全文本索引等；


----------
### Mysql 中 MyISAM 和 InnoDB 的区别有哪些？

区别：

1. InnoDB支持事务，MyISAM不支持，对于InnoDB每一条SQL语言都默认封装成事务，自动提交，这样会影响速度，所以最好把多条SQL语言放在begin和commit之间，组成一个事务；  

2. InnoDB支持外键，而MyISAM不支持。对一个包含外键的InnoDB表转为MYISAM会失败；  

3. InnoDB是聚集索引，数据文件是和索引绑在一起的，必须要有主键，通过主键索引效率很高。但是辅助索引需要两次查询，先查询到主键，然后再通过主键查询到数据。因此，主键不应该过大，因为主键太大，其他索引也都会很大。而MyISAM是非聚集索引，数据文件是分离的，索引保存的是数据文件的指针。主键索引和辅助索引是独立的。 

4. InnoDB不保存表的具体行数，执行select count(*) from table时需要全表扫描。而MyISAM用一个变量保存了整个表的行数，执行上述语句时只需要读出该变量即可，速度很快；  

5. Innodb不支持全文索引，而MyISAM支持全文索引，查询效率上MyISAM要高；   



如何选择：

1. 是否要支持事务，如果要请选择innodb，如果不需要可以考虑MyISAM；

2. 如果表中绝大多数都只是读查询，可以考虑MyISAM，如果既有读写也挺频繁，请使用InnoDB。

3. 系统奔溃后，MyISAM恢复起来更困难，能否接受；

 4. MySQL5.5版本开始Innodb已经成为Mysql的默认引擎(之前是MyISAM)，说明其优势是有目共睹的，如果你不知道用什么，那就用InnoDB，至少不会差。


----------
### 数据库表创建注意事项
一、      字段名及字段配制合理性

1、        剔除关系不密切的字段

2、        字段命名要有规则及相对应的含义（不要一部分英文，一部分拼音，还有类似a.b.c这样不明含义的字段）

3、        字段命名尽量不要使用缩写（大多数缩写都不能明确字段含义）

4、        字段不要大小写混用（想要具有可读性，多个英文单词可使用下划线形式连接）

5、        字段名不要使用保留字或者关键字

6、        保持字段名和类型的一致性

7、        慎重选择数字类型

8、        给文本字段留足余量

二、      系统特殊字段处理及建成后建议

1、        添加删除标记（例如操作人、删除时间）

2、        建立版本机制

三、      表结构合理性配置

1、        多型字段的处理

就是表中是否存在字段能够分解成更小独立的几部分（例如：人可以分为男人和女人）

2、        多值字段的处理

可以将表分为三张表

这样使得检索和排序更加有调理，且保证数据的完整性！

四、      其它建议

1、        对于大数据字段，独立表进行存储，以便影响性能（例如：简介字段）

2、        使用varchar类型代替char，因为varchar会动态分配长度，char指定长度是固定的。

3、        给表创建主键，对于没有主键的表，在查询和索引定义上有一定的影响。

4、        避免表字段运行为null，建议设置默认值（例如：int类型设置默认值为0）在索引查询上，效率立显！

5、        建立索引，最好建立在唯一和非空的字段上，建立太多的索引对后期插入、更新都存在一定的影响（考虑实际情况来创建）。


----------
## Redis


----------

### 使用redis有哪些好处？
(1) 速度快，因为数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)
(2) 支持丰富数据类型，支持string，list，set，sorted set，hash
(3) 支持事务，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行
(4) 丰富的特性：可用于缓存，消息，按key设置过期时间，过期后将会自动删除


----------


###  redis相比memcached有哪些优势？
(1) memcached所有的值均是简单的字符串，redis作为其替代者，支持更为丰富的数据类型
(2) redis的速度比memcached快很多
(3) redis可以持久化其数据


----------


###  Redis 常见的性能问题都有哪些？如何解决？
1).Master写内存快照，save命令调度rdbSave函数，会阻塞主线程的工作，当快照比较大时对性能影响是非常大的，会间断性暂停服务，所以Master最好不要写内存快照。
2).Master AOF持久化，如果不重写AOF文件，这个持久化方式对性能的影响是最小的，但是AOF文件会不断增大，AOF文件过大会影响Master重启的恢复速度。Master最好不要做任何持久化工作，包括内存快照和AOF日志文件，特别是不要启用内存快照做持久化,如果数据比较关键，某个Slave开启AOF备份数据，策略为每秒同步一次。
3).Master调用BGREWRITEAOF重写AOF文件，AOF在重写的时候会占大量的CPU和内存资源，导致服务load过高，出现短暂服务暂停现象。
4). Redis主从复制的性能问题，为了主从复制的速度和连接的稳定性，Slave和Master最好在同一个局域网内

----------


### Redis的快照功能
Redis是基于内存的数据库，同时也提供了若干持久化的方案，允许用户把内存中的数据，写入本地文件系统，以备下次重启或者当机之后继续使用

1 用户发送bgsave命令(此时redis会fork一个子进程,子进程负责生成硬盘文件,父进程负责继续接受命令)
2 用户发送save命令(和bgsave命令不同,发送save命令后,到系统创建快照完成之前系统不会再接收新的命令,换句话说save命令会阻塞后面的命令,而bgsave不会)
3 用户在配置文件了配置了类似这样的命令 

设置Redis.conf配置文件
   Redis快照写入的频率
    save 900 1
    save 300 10
    save 60 10000
  快照文件名称设置
        dbfilename dump.rdb
  快照目录色绘制
        dir /opt/redis
  停用aof， 由于aof的优先级高于快照，所以，将其设为off之后，才可以使用snapshot
    appendonly no
    


----------


### redis的持久化
1、RDB 持久化可以在指定的时间间隔内生成数据集的时间点快照（point-in-time snapshot）
2、AOF 持久化记录服务器执行的所有写操作命令，并在服务器启动时，通过重新执行这些命令来还原数据集。 
3、Redis 还可以同时使用 AOF 持久化和 RDB 持久化。


----------


### redis 提供 6种数据淘汰策略

volatile-lru：从已设置过期时间的数据集（server.db[i].expires）中挑选最近最少使用的数据淘汰
volatile-ttl：从已设置过期时间的数据集（server.db[i].expires）中挑选将要过期的数据淘汰
volatile-random：从已设置过期时间的数据集（server.db[i].expires）中任意选择数据淘汰
allkeys-lru：从数据集（server.db[i].dict）中挑选最近最少使用的数据淘汰
allkeys-random：从数据集（server.db[i].dict）中任意选择数据淘汰
no-enviction（驱逐）：禁止驱逐数据

----------
# Java


----------
## Java虚拟机

----------
### 什么时候会触发full gc
1、System.gc()方法的调用
2、老年代空间不足
3、永生区空间不足（JVM规范中运行时数据区域中的方法区，在HotSpot虚拟机中又被习惯称为永生代或者永生区，Permanet Generation中存放的为一些class的信息、常量、静态变量等数据）
4、CMS GC时出现promotion failed和concurrent mode failure
5、统计得到的Minor GC晋升到旧生代平均大小大于老年代剩余空间
6、堆中分配很大的对象

----------
### 可以作为root的对象：
1.类中的静态变量，当它持有一个指向一个对象的引用时，它就作为root
2.活动着的线程，可以作为root
 3.一个Java方法的参数或者该方法中的局部变量，这两种对象可以作为root
4.JNI方法中的局部变量或者参数，这两种对象可以作为root


例子：下述的Something和Apple都可以作为root对象。

```
public AClass{
 
  public static Something;
  public static final Apple;
   ''''''
}
```

 Java方法的参数和方法中的局部变量，可以作为root.

```
public Aclass{

public void doSomething(Object A){
    ObjectB b = new ObjectB; 
    }
 }
```
##新生代转移到老年代的触发条件
1、长期存活的对象
2、大对象直接进入老年代
3、minor gc后，survivor仍然放不下
4、动态年龄判断 ，大于等于某个年龄的对象超过了survivor空间一半 ，大于等于某个年龄的对象直接进入老年代


----------


### CMS收集器过程
1、初始标记
2、并发标记
3、重新标记
4、并发清除


----------


优点：并发收集、低停顿
### G1运作步骤
1、初始标记
2、并发标记
3、最终标记
4、筛选回收
特点：分代回收、可预测的停顿、并行于并发


----------
##双亲委派模型中有哪些方法。用户如何自定义类加载器 。怎么打破双亲委托机制
1、双亲委派模型中用到的方法：
findLoadedClass(),
loadClass()
findBootstrapClassOrNull()
findClass()
defineClass()：把二进制数据转换成字节码。
resolveClass()
自定义类加载器的方法：继承 ClassLoader 类,重写 findClass()方法 。


2、继承ClassLoader覆盖loadClass方法
原顺序
1、findLoadedClass
2、委托parent加载器加载（这里注意bootstrap加载器的parent为null)
3、自行加载
打破委派机制要做的就是打乱2和3的顺序，通过类名筛选自己要加载的类，其他的委托给parent加载器。


----------
### 即时编译器的优化方法
字节码可以通过以下两种方式转换成合适的语言：1、解释器，2、即时编译器
即时编译器把**整段字节码编译成本地代码**，执行本地代码比一条一条进行解释执行的速度快很多，因为本地代码是保存在缓存里的


----------
### 编译过程的五个阶段
第一阶段：词法分析
第二阶段：语法分析
第三阶段:词义分析与中间代码产生
第四阶段：优化
第五阶段：目标代码生成


----------


### java应用系统运行速度慢的解决方法
 问题解决思路：
        1，查看部署应用系统的系统资源使用情况，CPU,内存，IO这几个方面去看。找到对就的进程。
        2，使用jstack,jmap等命令查看是JVM是在在什么类型的内存空间中做GC（内存回收），和查看GC日志查看是那段代码在占用内存。
         首先，调节内存的参数设置，如果还是一样的问题，就要定位到相应的代码。
        3，定位代码，修改代码（一般是代码的逻辑问题，或者代码获取的数据量过大。）


----------
### 内存溢出是什么，什么原因导致的
内存溢出是指应用系统中存在无法回收的内存或使用的内存过多，最终使得程序运行要用到的内存大于虚拟机能提供的最大内存。为了解决Java中内存溢出问题，我们首先必须了解Java是如何管理内存的。Java的内存管理就是对象的分配和释放问题。在Java中，内存的分配是由程序完成的，而内存的释放是由垃圾收集器(Garbage Collection，GC)完成的，程序员不需要通过调用GC函数来释放内存，因为不同的JVM实现者可能使用不同的算法管理GC，有的是内存使用到达一定程度时，GC才开始工作，也有定时执行的，有的是中断式执行GC。但GC只能回收无用并且不再被其它对象引用的那些对象所占用的空间。Java的内存垃圾回收机制是从程序的主要运行对象开始检查引用链，当遍历一遍后发现没有被引用的孤立对象就作为垃圾回收。

引起内存溢出的原因有很多种，常见的有以下几种：

1、内存中加载的数据量过于庞大，如一次从数据库取出过多数据；

2、  集合类中有对对象的引用，使用完后未清空，使得JVM不能回收；

3、代码中存在死循环或循环产生过多重复的对象实体；

4、使用的第三方软件中的BUG；

5、 启动参数内存值设定的过小；


----------
### 内存溢出的解决
内存溢出虽然很棘手，但也有相应的解决办法，可以按照从易到难，一步步的解决。

第一步，就是修改JVM启动参数，直接增加内存。这一点看上去似乎很简单，但很容易被忽略。JVM默认可以使用的内存为64M，Tomcat默认可以使用的内存为128MB，对于稍复杂一点的系统就会不够用。在某项目中，就因为启动参数使用的默认值，经常报“OutOfMemory”错误。因此，-Xms，-Xmx参数一定不要忘记加。

第二步，检查错误日志，查看“OutOfMemory”错误前是否有其它异常或错误。在一个项目中，使用两个数据库连接，其中专用于发送短信的数据库连接使用DBCP连接池管理，用户为不将短信发出，有意将数据库连接用户名改错，使得日志中有许多数据库连接异常的日志，一段时间后，就出现“OutOfMemory”错误。经分析，这是由于DBCP连接池BUG引起的，数据库连接不上后，没有将连接释放，最终使得DBCP报“OutOfMemory”错误。经过修改正确数据库连接参数后，就没有再出现内存溢出的错误。

查看日志对于分析内存溢出是非常重要的，通过仔细查看日志，分析内存溢出前做过哪些操作，可以大致定位有问题的模块。

第三步，安排有经验的编程人员对代码进行走查和分析，找出可能发生内存溢出的位置。重点排查以下几点：

1、  检查代码中是否有死循环或递归调用。

2、 检查是否有大循环重复产生新对象实体。

3、 检查对数据库查询中，是否有一次获得全部数据的查询。一般来说，如果一次取十万条记录到内存，就可能引起内存溢出。这个问题比较隐蔽，在上线前，数据库中数据较少，不容易出问题，上线后，数据库中数据多了，一次查询就有可能引起内存溢出。因此对于数据库查询尽量采用分页的方式查询。

l         检查List、MAP等集合对象是否有使用完后，未清除的问题。List、MAP等集合对象会始终存有对对象的引用，使得这些对象不能被GC回收。

第四步，使用内存查看工具动态查看内存使用情况。某个项目上线后，每次系统启动两天后，就会出现内存溢出的错误。这种情况一般是代码中出现了缓慢的内存泄漏，用上面三个步骤解决不了，这就需要使用内存查看工具了。

内存查看工具有许多，比较有名的有：Optimizeit Profiler、JProbe Profiler、JinSight和Java1.5的Jconsole等。它们的基本工作原理大同小异，都是监测Java程序运行时所有对象的申请、释放等动作，将内存管理的所有信息进行统计、分析、可视化。开发人员可以根据这些信息判断程序是否有内存泄漏问题。一般来说，一个正常的系统在其启动完成后其内存的占用量是基本稳定的，而不应该是无限制的增长的。持续地观察系统运行时使用的内存的大小，可以看到在内存使用监控窗口中是基本规则的锯齿形的图线，如果内存的大小持续地增长，则说明系统存在内存泄漏问题。通过间隔一段时间取一次内存快照，然后对内存快照中对象的使用与引用等信息进行比对与分析，可以找出是哪个类的对象在泄漏。

通过以上四个步骤的分析与处理，基本能处理内存溢出的问题。当然，在这些过程中也需要相当的经验与敏感度，需要在实际的开发与调试过程中不断积累。

总体上来说，产生内存溢出是由于代码写的不好造成的，因此提高代码的质量是最根本的解决办法。有的人认为先把功能实现，有BUG时再在测试阶段进行修正，这种想法是错误的。正如一件产品的质量是在生产制造的过程中决定的，而不是质量检测时决定的，软件的质量在设计与编码阶段就已经决定了，测试只是对软件质量的一个验证，因为测试不可能找出软件中所有的BUG。


----------

## Java并发


----------
##讲一下CAS
CAS有3个操作数，内存值V，旧的预期值A，要修改的新值B。当且仅当预期值A和内存值V相同时，将内存值V修改为B，否则什么都不做。

JDK文档说cas同时具有volatile读和volatile写的内存语义。

缺点：
1、因为CAS需要在操作值的时候检查下值有没有发生变化，如果没有发生变化则更新，但是如果一个值原来是A，变成了B，又变成了A，那么使用CAS进行检查时会发现它的值没有发生变化

2、循环时间长开销大。自旋CAS如果长时间不成功，会给CPU带来非常大的执行开销。


**自旋锁**是采用让当前线程不停地的在循环体内执行实现的，当循环的条件被其他线程改变时 才能进入临界区。


----------
### 线程池
一个线程池包括以下四个基本组成部分：
1、线程池管理器（ThreadPool）：用于创建并管理线程池，包括 创建线程池，销毁线程池，添加新任务；
2、工作线程（PoolWorker）：线程池中线程，在没有任务时处于等待状态，可以循环的执行任务；
3、任务接口（Task）：每个任务必须实现的接口，以供工作线程调度任务的执行，它主要规定了任务的入口，任务执行完后的收尾工作，任务的执行状态等；
4、任务队列（taskQueue）：用于存放没有处理的任务。提供一种缓冲机制。


----------


### 为什么要用线程池:

1、减少了创建和销毁线程的次数，每个工作线程都可以被重复利用，可执行多个任务。
2、可以根据系统的承受能力，调整线程池中工作线线程的数目，防止因为消耗过多的内存，而把服务器累趴下(每个线程需要大约1MB内存，线程开的越多，消耗的内存也就越大，最后死机)。 
3、Java里面线程池的顶级接口是Executor，但是严格意义上讲Executor并不是一个线程池，而只是一个执行线程的工具。真正的线程池接口是ExecutorService。


----------


### 对象锁和静态锁之间的区别
1、对象锁用于对象实例方法，
2、类锁用于类的静态方法或一个类的class对象。
3、类的对象实例可以有很多，不同对象实例的对象锁互不干扰，而每个类只有一个类锁 


----------


###  简述volatile字
两个特性
1 ） 保证了不同线程对这个变量进行 读取 时的可见性，即一个线程修改
了某个变量的值 ， 这新值对其他线程来说是立即可见的 。(volatile 解决了
线程间 共享变量
2 ）禁止进行指令重排序 ，阻止编译器对代码的优化

**要想并发程序正确地执行，必须要保证原子性、可见性以及有序性，锁保证了原子性，而volatile保证可见性和有序性**


----------
### 三．happens-before 原则（先行发生原则）：
1、 程序次序规则：一个线程内，按照代码顺序，书写在前面的操作先行发生于书写在
后面的操作
2、锁定规则：一个 unLock 操作先行发生于后面对同一个锁的 lock 操作
3、volatile 变量规则：对一个变量的写操作先行发生于后面对这个变量的读操作
4、 传递规则：如果操作 A 先行发生于操作 B，而操作 B 又先行发生于操作 C，则可以
得出操作 A 先行发生于操作 C
5、 线程启动规则：Thread 对象的 start()方法先行发生于此线程的每个一个动作
6、 线程中断规则：对线程 interrupt()方法的调用先行发生于被中断线程的代码检测
到中断事件的发生
7、 线程终结规则：线程中所有的操作都先行发生于线程的终止检测，我们可以通过 T
hread.join()方法结束、Thread.isAlive()的返回值手段检测到线程已经终止执行
8、对象终结规则：一个对象的初始化完成先行发生于他的 finalize()方法的开始


----------
##Lock 和synchronized 的区别
1）Lock 是一个 接口，而 synchronized 是 Java 中的 关键字，
synchronized 是 内置的语言实现；
2）synchronized 在 发生异常时，会 自动释放线程占有的锁，因此 不会导
致死锁现象发生；而 Lock 在发生异常时，如果没有主动通过 unLock()去释放
锁，则很 可能造成死锁现象，因此用 使用 Lock 时需要在 finally 块中释放锁；

3）Lock 可以让 等待锁的线程响应中断 （可中断锁），而 synchronized
却不行，使用 synchronized 时，等待的线程会一直等待下去， 不能够响应中
断 （不可中断锁）；
4）通过 Lock 可以知道 有没有成功获取锁 （tryLock （ ） 方法 ： 如果获取
了锁 ，回 则返回 true ；回 否则返回 false e, , 也就说这个方法无论如何都会立即返回 。
在拿不到锁时不会一直在那等待。 ），而 synchronized 却无法办到。
5）Lock 可以提高 多个线程进行读操作的效率（ 读写锁）。
6）Lock 可以实现 公平锁，synchronized 不保证公平性。
在性能上来说，如果线程竞争资源不激烈时，两者的性能是差不多的，而
当竞争资源非常激烈时（即有大量线程同时竞争），此时 Lock 的性能要远远优
于 synchronized。所以说，在具体使用时要根据适当情况选择。


----------
### CountDownLatch和CyclicBarrier区别
1、CountDownLatch类位于java.util.concurrent包下，利用它可以实现类似计数器的功能。比如有一个任务A，它要等待其他4个任务执行完毕之后才能执行
2、CyclicBarrier字面意思回环栅栏，通过它可以实现让一组线程等待至某个状态之后再全部同时执行。叫做回环是因为当所有等待线程都被释放以后，CyclicBarrier可以被重用。我们暂且把这个状态就叫做barrier，当调用await()方法之后，线程就处于barrier了


----------
### ThreadLocal(线程变量副本)
Synchronized实现内存共享，ThreadLocal为每个线程维护一个本地变量。
采用空间换时间，它用于线程间的数据隔离，为每一个使用该变量的线程提供一个副本，每个线程都可以独立地改变自己的副本，而不会和其他线程的副本冲突。
ThreadLocal类中维护一个Map，用于存储每一个线程的变量副本，Map中元素的键为线程对象，而值为对应线程的变量副本。
ThreadLocal在Spring中发挥着巨大的作用，在管理Request作用域中的Bean、事务管理、任务调度、AOP等模块都出现了它的身影。
Spring中绝大部分Bean都可以声明成Singleton作用域，采用ThreadLocal进行封装，因此有状态的Bean就能够以singleton的方式在多线程中正常工作了。


----------
### 通过Callable和Future创建线程

Java 5在concurrency包中引入了java.util.concurrent.Callable 接口，它和Runnable接口很相似，但它可以返回一个对象或者抛出一个异常。

Callable接口**使用泛型去定义它的返回类型**。Executors类提供了**一些有用的方法去在线程池中执行Callable内的任务**。由于Callable任务是并行的，我们必须等待它返回的结果。java.util.concurrent.Future对象为我们解决了这个问题。在线程池**提交Callable任务**后**返回了一个Future对象**，使用它我们可以知道Callable任务的状态和得到Callable返回的执行结果。**Future提供了get()方法让我们可以等待Callable结束并获取它的执行结果**。

（1）创建Callable接口的实现类，并实现call()方法，该call()方法将作为线程执行体，并且有返回值。
（2）创建Callable实现类的实例，**使用FutureTask类来包装Callable对象**，该FutureTask对象封装了该Callable对象的call()方法的返回值。
（3）**使用FutureTask对象作为Thread对象的target创建并启动新线程**。
（4）调用FutureTask对象的get()方法来获得子线程执行结束后的返回值


----------

###  什么是FutureTask?

FutureTask是Future的一个基础实现，我们可以将它同Executors使用处理异步任务。通常我们不需要使用FutureTask类，单当我们打算重写Future接口的一些方法并保持原来基础的实现是，它就变得非常有用。我们可以仅仅继承于它并重写我们需要的方法。阅读Java FutureTask例子，学习如何使用它。


----------
### 什么叫守护线程，用什么方法实现守护线程（Thread.setDeamon()的含义）
在Java中有两类线程：User Thread(用户线程)、Daemon Thread(守护线程) 
用个比较通俗的比如，任何一个守护线程都是整个JVM中所有非守护线程的保姆：
只要当前JVM实例中尚存在任何一个非守护线程没有结束，守护线程就<font color=red>全部工作</font>；只有当最后一个非守护线程结束时，<font color=red>守护线程随着JVM一同结束工作。</font>
JVM内部的实现是如果运行的程序<font color=red>只剩下守护线程的话，程序将终止运行，直接结束。</font>所以守护线程是作为辅助线程存在的，主要的作用是提供计数等等辅助的功能。


----------
### 如何停止一个线程？
终止线程的三种方法：
1. 使用退出标志，使线程正常退出，也就是当run方法完成后线程终止。
在定义退出标志exit时，使用了一个Java关键字volatile，这个关键字的目的是使exit同步，也就是说在同一时刻只能由一个线程来修改exit的值， 

```
  thread.exit = true;  // 终止线程thread 
```

2. 使用stop方法强行终止线程（这个方法不推荐使用，因为stop和suspend、resume一样，也可能发生不可预料的结果）。
使用stop方法可以强行终止正在运行或挂起的线程。我们可以使用如下的代码来终止线程： 
thread.stop(); 
虽然使用上面的代码可以终止线程，但使用stop方法是很危险的，就象突然关闭计算机电源，而不是按正常程序关机一样，可能会产生不可预料的结果，因此，并不推荐使用stop方法来终止线程。 




3. 使用interrupt方法中断线程。
 使用interrupt方法来终端线程可分为两种情况： 

 （1）线程处于阻塞状态，如使用了sleep方法。 

 （2）使用while（！isInterrupted（））{……}来判断线程是否被中断。 
    在第一种情况下使用interrupt方法，sleep方法将抛出一个InterruptedException例外，而在第二种情况下线程将直接退出。


<font color=red>注意：在Thread类中有两个方法可以判断线程是否通过interrupt方法被终止。一个是静态的方法interrupted（），一个是非静态的方法isInterrupted（），这两个方法的区别是interrupted用来判断当前线是否被中断，而isInterrupted可以用来判断其他线程是否被中断。因此，while （！isInterrupted（））也可以换成while （！Thread.interrupted（））。</font>


----------
### 解释是一下什么是线程安全？举例说明一个线程不安全的例子。解释Synchronized关键字的作用。
（1）线程安全就是多线程访问时，采用了加锁机制，当一个线程访问该类的某个数据时，进行保护，其他线程不能进行访问直到该线程读取完，其他线程才可使用。不会出现数据不一致或者数据污染。
（2）线程不安全就是不提供数据访问保护，有可能出现多个线程先后更改数据造成所得到的数据是脏数据
在多线程的情况下，由于同一进程的多个线程共享同一片存储空间，在带来方便的同时，也带来了访问冲突这个严重的问题。Java语言提供了专门机制以解决这种冲突，有效避免了同一个数据对象被多个线程同时访问。
（3）由于我们可以通过 private 关键字来保证数据对象只能被方法访问，所以我们只需针对方法提出一套机制，这套机制就是 synchronized 关键字，它包括两种用法：synchronized 方法和 synchronized 块。 

### Executors类是什么？
Executor框架是一个根据一组**执行策略调用，调度，执行和控制**的异步任务的框架。

Executors为Executor，ExecutorService，ScheduledExecutorService，ThreadFactory和Callable类提供了一些工具方法。

Executors可以用于方便的创建线程池


----------


## Java容器


----------
### HashSet和TreeSet区别
**HashSet**
HashSet有以下特点
1、不能保证元素的排列顺序，顺序有可能发生变化
2、不是同步的
3、 集合元素可以是null,但只能放入一个null
当向HashSet结合中存入一个元素时，HashSet会调用该对象的hashCode()方法来得到该对象的hashCode值，然后根据 hashCode值来决定该对象在HashSet中存储位置。
**TreeSet**
TreeSet是SortedSet接口的唯一实现类，TreeSet可以确保集合元素处于排序状态。TreeSet支持两种排序方式，自然排序 和定制排序，其中自然排序为默认的排序方式。向TreeSet中加入的应该是同一个类的对象


----------
### #简单讲一下LinkedHashMap
LinkedHashMap的实现就是HashMap+LinkedList的实现方式，以HashMap维护数据结构，以LinkList的方式维护数据插入顺序

LinkedHashMap保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的。
在遍历的时候会比HashMap慢TreeMap能够把它保存的记录根据键排序，默认是按升序排序，也可以指定排序的比较器

利用LinkedHashMap实现LRU算法缓存（
1、LinkedList首先它是一个Map，Map是基于K-V的，和缓存一致
2、LinkedList提供了一个boolean值可以让用户指定是否实现LRU）


----------
### Java8 中HashMap的优化（引入红黑树的数据结构和扩容的优化）
1、if (binCount >= TREEIFY_THRESHOLD - 1) 
当符合这个条件的时候，把链表变成treemap红黑树，这样查找效率从o(n)变成了o(log n) ，在JDK1.8的实现中，优化了高位运算的算法，通过hashCode()的高16位异或低16位实现的：
2、我们使用的是2次幂的扩展(指长度扩为原来2倍)，所以，元素的位置要么是在原位置，要么是在原位置再移动2次幂的位置


这里的Hash算法本质上就是三步：取key的hashCode值、高位运算、取模运算。



**元素在重新计算hash之后，因为n变为2倍，那么n-1的mask范围在高位多1bit(红色)，因此新的index就会发生这样的变化：**
hashMap 1.8 哈希算法例图2
![这里写图片描述](http://img.blog.csdn.net/20170930102913241?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvanl4bXVzdA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
**因此，我们在扩充HashMap的时候，不需要像JDK1.7的实现那样重新计算hash，只需要看看原来的hash值新增的那个bit是1还是0就好了，是0的话索引没变，是1的话索引变成“原索引+oldCap”**


----------
###  Map遍历的keySet()和entrySet()性能差异原因
keySet（）循环中通过key获取对应的value的时候又会进行循环。所以它的性能会比entrySet（）差点。所以遍历map的话还是用entrySet()来遍历


----------
## Java基础


----------
### 创建一个类的几种方法?

（1）使用new关键字	} → 调用了构造函数

（2）使用Class类的newInstance方法	} → 调用了构造函数


Employee emp2 = (Employee)
Class.forName("org.programming.mitra.exercises.Employee").newInstance();`

（3）使用Constructor类的newInstance方法	} → 调用了构造函数



Constructor<Employee> constructor = Employee.class.getConstructor();
Employee emp3 = constructor.newInstance();



（4）使用clone方法	} → 没有调用构造函数
（5）使用反序列化	} → 没有调用构造函数


ObjectInputStream in = new ObjectInputStream(new FileInputStream("data.obj"));
Employee emp5 = (Employee) in.readObject();

----------
### Redirect和forward

1、上图所示的间接转发请求的过程如下：
浏览器向Servlet1发出访问请求；
Servlet1调用sendRedirect()方法，将浏览器重定向到Servlet2；
浏览器向servlet2发出请求；
最终由Servlet2做出响应。 

2、 上图所示的直接转发请求的过程如下：
浏览器向Servlet1发出访问请求；
Servlet1调用forward()方法，在服务器端将请求转发给Servlet2；
最终由Servlet2做出响应。


----------
###  Java泛型中的标记符含义： 
 E - Element (在集合中使用，因为集合中存放的是元素)
 T - Type（Java 类）
 K - Key（键）
 V - Value（值）
 N - Number（数值类型）
？ -  表示不确定的java类型
 S、U、V  - 2nd、3rd、4th types


----------


### Object跟这些标记符代表的java类型有啥区别呢？  
Object是所有类的根类，任何类的对象都可以设置给该Object引用变量，使用的时候可能需要类型强制转换，但是用使用了泛型T、E等这些标识符后，在实际用之前类型就已经确定了，不需要再进行类型强制转换。


----------
### Error类和Exception类区别
1、Error类和Exception类的父类都是throwable类，他们的区别是：
Error类一般是指与虚拟机相关的问题，如系统崩溃，虚拟机错误，内存空间不足，方法调用栈溢等。对于这类错误的导致的应用程序中断，仅靠程序本身无法恢复和和预防，遇到这样的错误，建议让程序终止。
Exception类表示程序可以处理的异常，可以捕获且可能恢复。遇到这类异常，应该尽可能处理异常，使程序恢复运行，
而不应该随意终止异常。
2、Exception类又分为运行时异常（Runtime Exception）和受检查的异常(Checked Exception )，运行时异常;ArithmaticException,IllegalArgumentException，编译能通过，但是一运行就终止了，程序不会处理运行时异常，出现这类异常，程序会终止。**而受检查的异常，要么用try。。。catch捕获，要么用throws字句声明抛出，交给它的父类处理，否则编译不会通过。**


----------
### throw和throws区别
**throw：（针对对象的做法）**
 抛出一个异常，可以是系统定义的，也可以是自己定义的
 

```
public void yichang(){
    NumberFormatException e = new NumberFormatException();
    throw e;
}
```
**throws：（针对一个方法抛出的异常）**
抛出一个异常，可以是系统定义的，也可以是自己定义的。

```
public void yichang() throws NumberFormatException{
    int a = Integer.parseInt("10L");
}
```
1、throws出现在方法函数头；而throw出现在函数体。
2、throws表示出现异常的一种可能性，并不一定会发生这些异常；throw则是抛出了异常，执行throw则一定抛出了某种异常。
3、两者都是消极处理异常的方式（这里的消极并不是说这种方式不好），只是抛出或者可能抛出异常，但是不会由函数去处理异常，真正的处理异常由函数的上层调用处理。

----------
### .class 文件是什么类型文件
class文件是一种8位字节的二进制流文件


----------
## java中序列化之子类继承父类序列化
父类实现了Serializable，子类不需要实现Serializable

  相关注意事项
    a）序列化时，只对对象的状态进行保存，而不管对象的方法；
    b）当一个父类实现序列化，子类自动实现序列化，不需要显式实现Serializable接口；
    **c）当一个对象的实例变量引用其他对象，序列化该对象时也把引用对象进行序列化；**
    d）并非所有的对象都可以序列化，至于为什么不可以，有很多原因了,比如：
        1.安全方面的原因，比如一个对象拥有private，public等field，对于一个要传输的对象，比如写到文件，或者进行rmi传输等等，在序列化进行传输的过程中，这个对象的private等域是不受保护的。
       2. 资源分配方面的原因，比如socket，thread类，如果可以序列化，进行传输或者保存，也无法对他们进行重新的资源分配，而且，也是没有必要这样实现。

2,反过来父类未实现Serializable，子类实现了，序列化子类实例的时候，父类的属性是直接被跳过不保存，还是能保存但不能还原？（答案：值不保存）

 

解：父类实现接口后，所有派生类的属性都会被序列化。子类实现接口的话，父类的属性值丢失。

java中序列化之子类继承父类序列化


----------
### 标识符
标识符可以包括这4种字符：字母、下划线、$、数字；开头不能是数字；不能是关键字


----------


##Integer i=new Integer(127);和Integer i=127;的区别
Integer i = 127的时候,被翻译成-> Integer i5 = Integer.valueOf(127);对于-128到127之间的数，会进行缓存，Integer i5 = 127时，会将127进行缓存，下次再写Integer i6 = 127时，就会直接从缓存中取，就不会new了。所以结果为true,
```
	  int i1=128;
	  Integer i2=128;
	  Integer i3=new Integer(128);//自动拆箱
	  
	  System.out.println(i1==i2);//true
	  System.out.println(i1==i3);//true
	  
	  Integer i5=127;
	  Integer i6=127;
	  System.out.println(i5==i6);//true
	  
	  
	  Integer i5=127;
	  Integer ii5=new Integer(127);
	  System.out.println(i5==ii5);//false
	  
	  Integer i7=new Integer(127);
	  Integer i8=new Integer(127);
	  System.out.println(i7==i8);//false
```


----------
### 手写单例模式
最好的单例模式是静态内部类，不要写双重检验
```
private static class LazySomethingHolder {
  public static Something something = new Something();
}

public static Something getInstance() {
  return LazySomethingHolder.something;
}
```


----------


### 为什么线程通信的方法wait(), notify()和notifyAll()被定义在Object类里？

Java的每个对象中都有一个锁(monitor，也可以成为监视器) 并且wait()，notify()等方法用于等待对象的锁或者通知其他线程对象的监视器可用。在Java的线程中并没有可供任何对象使用的锁和同步器。这就是为什么这些方法是Object类的一部分，这样Java的每一个类都有用于线程间通信的基本方法


----------
### hashCode和equals方法的关系
在有些情况下，程序设计者在设计一个类的时候为需要重写equals方法，比如String类，但是千万要注意，在重写equals方法的同时，必须重写hashCode方法。
也就是说对于两个对象，如果调用equals方法得到的结果为true，则两个对象的hashcode值必定相等；
如果equals方法得到的结果为false，则两个对象的hashcode值不一定不同；
如果两个对象的hashcode值不等，则equals方法得到的结果必定为false；
如果两个对象的hashcode值相等，则equals方法得到的结果未知。
### Object类中有哪些方法，列举3个以上（可以引导）
Object方法：equals()、toString()、finalize()、hashCode()、getClass()、clone()、wait()、notify()、notifyAll()

----------
### String s=new String("xyz")究竟创建String Object分为两种情况：
1.如果String常理池中，已经创建"xyz"，则不会继续创建，此时只创建了一个对象new String("xyz")；
2.如果String常理池中，没有创建"xyz"，则会创建两个对象，一个对象的值是"xyz"，一个对象new String("xyz")。


----------
### 什么是值传递和引用传递
值传递
```
public class TempTest {

	private void test1(int a) {
		a = 5;
		System.out.println("test1方法中的a=" + a);
	}

	public static void main(String[] args) {
		TempTest t = new TempTest();
		int a = 3;
		t.test1(11);
		System.out.println("main方法中a=" + a);
	}

}
```
test1方法中的a=5
main方法中a=3
值传递：传递的是值的拷贝，传递后就互不相关了
引用传递：传递的是变量所对应的内存空间的地址

```
public class TempTest {
	private void test1(A a) {
		a.age = 20;
		System.out.println("test1方法中a=" + a.age);
	}

	public static void main(String[] args) {
		TempTest t = new TempTest();
		A a = new A();
		a.age = 10;
		t.test1(a);
		System.out.println("main方法中a=" + a.age);
	}
}

class A {
	public int age = 0;
}
```
test1方法中a=20
main方法中a=20
传递前和传递后都指向同一个引用（同一个内存空间）
如果不互相影响，方法是在test1方法里面新new一个实例就可以了


----------
### 讲一下netty
netty通过Reactor模型基于多路复用器接收并处理用户请求，内部实现了两个线程池，boss线程和work线程池，其中boss线程池的线程负责处理请求的accept事件，当接收到accept事件的请求，把对应的socket封装到一个NioSocketChannel中，并交给work线程池，其中work线程池负责请求的read和write事件

----------
### Nio的原理（同步非阻塞）
1、由一个专门的线程来处理所有的IO事件，并负责分发
2、事件驱动机制：事件到的时候触发，而不是同步的去监视事件
3、线程通讯：线程之间通过wait、notify等方式通讯，保证每次上下文切换都是有意义的，减少无谓的线程切换
![这里写图片描述](http://img.blog.csdn.net/20171006211005301?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvanl4bXVzdA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
（注：每个线程的处理流程大概都是读取数据、解码、计算处理、编码、发送响应。）

java NIO采用了双向通道（channel）进行数据传输，而不是单向的流（stream），在通道上可以注册我们感兴趣的事件。一共有以下四种事件：
	 
服务端和客户端各自维护一个管理通道的对象，我们称之为selector，该对象能检测一个或多个通道 (channel) 上的事件。我们以服务端为例，如果服务端的selector上注册了读事件，某时刻客户端给服务端发送了一些数据，阻塞I/O这时会调用read()方法阻塞地读取数据，而NIO的服务端会在selector中添加一个读事件。服务端的处理线程会轮询地访问selector，如果访问selector时发现有感兴趣的事件到达，则处理这些事件，如果没有感兴趣的事件到达，则处理线程会一直阻塞直到感兴趣的事件到达为止。

----------
# 计算机网络


----------
### GET 和 POST 区别
**（GET）**
请注意，查询字符串（名称/值对）是在 GET 请求的 URL 中发送的：
/test/demo_form.asp?name1=value1&name2=value2

GET 请求可被缓存
GET 请求保留在浏览器历史记录中
GET 请求可被收藏为书签
GET 请求不应在处理敏感数据时使用
GET 请求有长度限制
GET 请求只应当用于取回数据
POST 方法
**（POST）**
请注意，查询字符串（名称/值对）是在 POST 请求的 HTTP 消息主体中发送的：
POST /test/demo_form.asp HTTP/1.1
Host: w3schools.com
name1=value1&name2=value2

POST 请求不会被缓存
POST 请求不会保留在浏览器历史记录中
POST 不能被收藏为书签
POST 请求对数据长度没有要求


----------
### 幂等
一个幂等操作的特点是其任意多次执行所产生的影响均与一次执行的影响相同。幂等函数，或幂等方法，是指可以使用相同参数重复执行，并能获得相同结果的函数。这些函数不会影响系统状态，也不用担心重复执行会对系统造成改变。例如，“getUsername()和setTrue()”函数就是一个幂等函数.


----------
### Cookies和session区别
1、Cookies是一种能够让网站服务器把少量数据储存到客户端的硬盘或内存，或是从客户端的硬盘读取数据的一种技术。Cookies是当你浏览某网站时，由Web服务器置于你硬盘上的一个非常小的文本文件，它可以记录你的用户ID、密码、浏览过的网页、停留的时间等信息。 
session: 当用户请求来自应用程序的 Web 页时，如果该用户还没有会话，则 Web 服务器将自动创建一个 Session 对象。当会话过期或被放弃后，服务器将终止该会话。
cookie机制：采用的是在客户端保持状态的方案，而session机制采用的是在服务端保持状态的方案。同时我们看到由于服务器端保持状态的方案在客户端也需要保存一个标识，所以session机制可能需要借助cookie机制来达到保存标识的目的。

2、Session是服务器用来跟踪用户的一种手段，每个Session都有一个唯一标识：session ID。当服务器创建了Session时，给客户端发送的响应报文包含了Set-cookie字段，其中有一个名为sid的键值对，这个键值Session ID。客户端收到后就把Cookie保存浏览器，并且之后发送的请求报表都包含SessionID。HTTP就是通过Session和Cookie这两个发送一起合作来实现跟踪用户状态，Session用于服务端，Cookie用于客户端

----------
### TCP粘包和拆包产生的原因

1. 应用程序写入数据的字节大小大于套接字发送缓冲区的大小
2. 进行MSS大小的TCP分段。MSS是最大报文段长度的缩写。MSS是TCP报文段中的数据字段的最大长度。数据字段加上TCP首部才等于整个的TCP报文段。所以MSS并不是TCP报文段的最大长度，而是：MSS=TCP报文段长度-TCP首部长度
3. 以太网的payload大于MTU进行IP分片。MTU指：一种通信协议的某一层上面所能通过的最大数据包大小。如果IP层有一个数据包要传，而且数据的长度比链路层的MTU大，那么IP层就会进行分片，把数据包分成托干片，让每一片都不超过MTU。注意，IP分片可以发生在原始发送端主机上，也可以发生在中间路由器上。

### TCP粘包和拆包的解决策略

1. 消息定长。例如100字节。
2. 在包尾部增加回车或者空格符等特殊字符进行分割，典型的如FTP协议
3. 将消息分为消息头和消息尾。
4. 其它复杂的协议，如RTMP协议等。


----------
### 三次握手
第一次握手：建立连接时，客户端发送syn包(syn=j)到服务器，并进入SYN_SEND状态，等待服务器确认； 

第二次握手：服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入SYN_RECV状态； 

第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=k+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。 

完成三次握手，客户端与服务器开始传送数据


----------
### 一次完整的HTTP请求过程
域名解析 --> 发起TCP的3次握手 --> 建立TCP连接后发起http请求 --> 服务器响应http请求，浏览器得到html代码 --> 浏览器解析html代码，并请求html代码中的资源（如js、css、图片等） --> 浏览器对页面进行渲染呈现给用户


----------
### 讲一下长连接
 1.基于http协议的长连接
         在HTTP1.0和HTTP1.1协议中都有对长连接的支持。其中HTTP1.0需要在request中增加”Connection： keep-alive“ header才能够支持，而HTTP1.1默认支持.
      http1.0请求与服务端的交互过程:
      a)客户端发出带有包含一个header：”Connection： keep-alive“的请求
      b)服务端接收到这个请求后,根据http1.0和”Connection： keep-alive“判断出这是一个长连接,就会在response的header中也增加”Connection： keep-alive“,同是不会关闭已建立的tcp连接.
      c)客户端收到服务端的response后,发现其中包含”Connection： keep-alive“，就认为是一个长连接，不关闭这个连接。并用该连接再发送request.转到a)

发心跳包。每隔几秒就发一个数据包过去


----------
### TCP是提供的可靠服务
1、有确认机制来保证数据包的可靠达到
2、有CRC校验机制来保证数据包的无差错性，UDP的CRC是可选的
3、会重新排序乱序的数据包和丢弃重复的数据
4、能够提供流量控制机制，使用滑动窗口算法
5、能提供拥塞控制与恢复机制，存在多种tcp拥塞控制模型
6、能协商发送的数据报文长度

网际层协议：包括：IP协议、ICMP协议、ARP协议、RARP协议。
传输层协议：TCP协议、UDP协议。
应用层协议：FTP、Telnet、SMTP、HTTP、RIP、NFS、DNS。


----------
# 数据结构与算法


----------


### 动态规划的思想
动态规划过程是：每次决策依赖于当前状态，又随即引起状态的转移。一个决策序列就是在变化的状态中产生出来的，所以，这种多阶段最优化决策解决问题的过程就称为动态规划。

----------
### 快速排序的思想
在数组中找到一个基准数（pivot）
分区，将数组中比基准数大的放到它的右边，比基准数小的放到它的左边
继续对左右区间重复第二步，直到各个区间只有一个数，这时候，数组也就有序了。

### 快速排序算法是不稳定的算法
27 23 27 3
以第一个27作为pivot中心点，则27与后面那个3交换，形成
3 23 27 27，排序经过一次结束，但最后那个27在排序之初先于初始位置3那个27，所以不稳定。

----------
### 堆排序的思想
   利用大顶堆(小顶堆)堆顶记录的是最大关键字(最小关键字)这一特性，使得每次从无序中选择最大记录(最小记录)变得简单。
    其基本思想为(大顶堆)：
    1)将初始待排序关键字序列(R1,R2....Rn)构建成大顶堆，此堆为初始的无须区；
    2)将堆顶元素R[1]与最后一个元素R[n]交换，此时得到新的无序区(R1,R2,......Rn-1)和新的有序区(Rn),且满足R[1,2...n-1]<=R[n]; 
    3)由于交换后新的堆顶R[1]可能违反堆的性质，因此需要对当前无序区(R1,R2,......Rn-1)调整为新堆，然后再次将R[1]与无序区最后一个元素交换，得到新的无序区(R1,R2....Rn-2)和新的有序区(Rn-1,Rn)。不断重复此过程直到有序区的元素个数为n-1，则整个排序过程完成。

----------


### 字典树
<img class="thumbimage" src="http://upload.wikimedia.org/wikipedia/commons/thumb/b/be/Trie_example.svg/375px-Trie_example.svg.png" alt="" width="250" height="234" data-file-width="400" data-file-height="375">
 
字典树主要有如下三点性质：
1. 根节点不包含字符，除根节点意外每个节点只包含一个字符。
2. 从根节点到某一个节点，路径上经过的字符连接起来，为该节点对应的字符串。
3. 每个节点的所有子节点包含的字符串不相同。

----------



# Linux


----------
### Linux中软链接和硬链接的区别
ln -s source     dist     #  建立软连接
ln     source     dist     #   建立硬连接
建立硬链接时，链接文件和被链接文件必须位于同一个文件系统中，并且不能建立指向目录的硬链接
1、硬连接就像一个文件有多个文件名，
2、软连接就是产生一个新文件(这个文件内容,实际上就是记当要链接原文件路径的信息)，这个文件指向另一个文件的位置


----------


# 设计模式


----------

# 场景题和设计题


----------
### 场景题：设计判断论文抄袭的系统
一类是基于字符串比较的方法；另一类是基于词频统计的方法。
基于字符串比较的方法也称为数字指纹法，这类方法通过某种选取策略在文档中取一些字符串作为“指纹”，把指纹映射到Hash 表中，最后统计Hash 表中相同的指纹数目或者比率，作为文本相似度依据。
基于词频统计的方法也称为基于语义的方法。词频统计法源于信息检索技术中的**向量空间模型**，该类方法首先都要统计每篇文档中各个单词的出现次数，然后根据单词频度构成文档特征向量，最后采用点积、余弦或者类似方式度量两篇文档的特征向量，以此作为文档相似度的依据。




----------
### 设计一个即时聊天的系统
（1）用户通过客户端进入系统，向服务器发出消息，请求登陆。
（2）服务器收到请求后，向客户端返回应答消息，表示同意接受该用户加入，并顺带将自己服务线程所在的监听端口号告诉用户。
（3）客户端按照服务器应答中给出的端口号与服务器建立稳定的连接。
（4）服务器通过该连接将当前在线用户的列表信息传给新加入的客户端。
（5）客户端获得了在线用户列表，就可以独立自主地与在线的其他用户通信了。
（6）当用户退出系统时要及时地通知服务器。


----------
##分布式系统事务一致性解决方案
分布式系统事务一致性解决方案
MQ（事务消息）

举个例子，Bob向Smith转账，那我们到底是先发送消息，还是先执行扣款操作？

好像都可能会出问题。如果先发消息，扣款操作失败，那么Smith的账户里面会多出一笔钱。反过来，如果先执行扣款操作，后发送消息，那有可能扣款成功了但是消息没发出去，Smith收不到钱。除了上面介绍的通过异常捕获和回滚的方式外，还有没有其他的思路呢？

下面以阿里巴巴的RocketMQ中间件为例，分析下其设计和实现思路。

RocketMQ第一阶段发送Prepared消息时，会拿到消息的地址，第二阶段执行本地事物，第三阶段通过第一阶段拿到的地址去访问消息，并修改状态。细心的读者可能又发现问题了，如果确认消息发送失败了怎么办？RocketMQ会定期扫描消息集群中的事物消息，这时候发现了Prepared消息，它会向消息发送者确认，Bob的钱到底是减了还是没减呢？如果减了是回滚还是继续发送确认消息呢？RocketMQ会根据发送端设置的策略来决定是回滚还是继续发送确认消息。这样就保证了消息发送与本地事务同时成功或同时失败。如下图：
![这里写图片描述](http://img.blog.csdn.net/20171006210237011?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvanl4bXVzdA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


----------
##设计高并发的系统？
1.HTML 页面静态化
访问频率较高但内容变动较小，使用网站 HTML 静态化方案来优化访问速度。将社区
内的帖子、文章进行实时的静态化，有更新的时候再重新静态化也是大量使用的策略。
优势：
一、减轻服务器负担。
二、加快页面打开速度， 静态页面无需 访问 数据库，打开速度较动态页面有明显提高；
三、很多搜索引擎都会优先收录静态页面，不仅被收录的快，还收录的全，容易被搜
索引擎找到；
四、HTML 静态页面不会受程序相关漏洞的影响，减少攻击 ，提高安全性。
2.图片服务器和应用服务器相分离
现在很多的网站上都会用到大量的图片，而图片是网页传输中占主要的数据量,也是影
响网站性能的主要因素。因此很多网站都会将图片存储从网站中分离出来，另外架构一个
或多个服务器来存储图片，将图片放到一个虚拟目录中，而网页上的图片都用一个 URL 地
址来指向这些服务器上的图片的地址，这样的话网站的性能就明显提高了。
优势：
一、分担 Web 服务器的 I/O 负载-将耗费资源的图片服务分离出来，提高服务器的性能
和稳定性。
二、 能够专门对图片服务器进行优化-为图片服务设置有针对性的 缓存方案，减少带宽
成本，提高访问速度。
三、 提高网站的可扩展性-通过增加图片服务器，提高图片吞吐能力。
3.数据库
见“数据库部分的---如果有一个特别大的访问量到数据库上，怎么做优化？”。
4.缓存
尽量使用缓存，包括用户缓存，信息缓存等，多花点内存来做缓存，可以大量减少与
数据库的交互，提高性能。
假如我们能减少数据库频繁的访问，那对系统肯定大大有利的。比如一个电子商务系
统的商品搜索，如果某个关键字的商品经常被搜，那就可以考虑这部分商品列表存放到缓
存（内存中去），这样不用每次访问数据库，性能大大增加。
5.镜像
镜像是冗余的一种类型，一个磁盘上的数据在另一个磁盘上存在一个完全相同的副本
即为镜像。
6.负载均衡
在网站高并发访问的场景下，使用负载均衡技术（负载均衡服务器）为一个应用构建
一个由多台服务器组成的服务器集群，将并发访问请求分发到多台服务器上处理，避免单
一服务器因负载压力过大而响应缓慢，使用户请求具有更好的响应延迟特性。
7.并发控制
加锁，如乐观锁和悲观锁。
8. 消息队列
通过 mq 一个一个排队方式，跟 12306 一样。

----------
### 设计高负载的系统
1、应用无状态
2、有效使用缓存
3、应用拆分
4、数据库拆分
5、异步通信
6、非结构化数据存储 ( TFS,NOSQL)
7、监控、预警系统
8、配置统一管理


----------
### 订票系统，某车次只有一张火车票，假定有 1w 个人同时打开 12306 网站来订票，如何解决并发问题？（可扩展到任何高并发网站要考虑的 并发读写问题
使用乐观锁，乐观锁意思是不锁定表的情况下，利用业务的控制来解决并发问题，这样既保证数据的并发 可读性 ，又保证保存数据的 排他性，保证性能的同时解决了并发带来
的脏数据问题。hibernate 中实现乐观锁。

