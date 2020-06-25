# Interview
面试复习资料汇总
# 计算机网络：
## 1. SSL (Secure Socket Layer)，TLS (Transport Layer Security)
- [浅谈SSL/TLS工作原理](https://zhuanlan.zhihu.com/p/36981565)
对称加密算法： AES, DES
非对称加密算法：RSA
哈希算法：SHA MD5
保证public key的真实性：CA（Certificate Authority）
A和B都各有一个public key和一个private key，这些key根据相应的算法已经生成好了。private key只保留在各自的本地，public key传给对方。A要给B发送网络数据，那么A先使用自己的private key（只有A知道）加密数据的hash值，之后再用B的public key加密数据。之后，A将加密的hash值和加密的数据再加一些其他的信息，发送给B。B收到了之后，先用自己的private key（只有B知道）解密数据，本地运算一个hash值，之后用A的public key解密hash值，对比两个hash值，以检验数据的完整性。

SSL记录层协议(SSL Record Protocol)
SSL握手协议(SSL Handshake Protocol)允许服务方和客户方互相认证

## 2. 知识点总结

具体过程
1. 双方协商SSL版本，加密算法，压缩算法
2. 双方交换数字证书(认证过的公钥)
3. 双方协商DH算法，分别产生session-key,用于对双方SSL数据流加密和解密，加密算法AES，再产生哈希算法SHA, MD5
4. 然后就是用哈希算法对数据报文进行加密，添加到原始报文后面，再用对称加密算法加密
5. 发送

作用：1. 验证双方身份 2. 加密数据 3. 保持数据完整性

## 2. 多路复用
1. I/O多路复用：单个线程，通过记录跟踪每个I/O流(sock)的状态，来同时管理多个I/O流。select, poll, epoll
select: 轮询检查，频繁在内核和用户空间复制文件符，最大处理文件描述符是1024，不是线程安全。
poll：轮询检查，频繁在内核和用户空间复制文件符，通过链表突破最大处理文件描述符是1024的限制，不是线程安全。
epoll: 水平触发 LT 边缘触发ET，epoll_ctl(),epoll_wait() -- 返回触发的描述符


# 数据库：
## 1. Index Introduction （B+Tree, B Tree）

- [MySQL索引背后的数据结构及算法原理](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)
- [B树和B+树](https://my.oschina.net/u/4116286/blog/3107389)

# 操作系统：
## 1. 线程与进程

- [多线程还是多进程的选择及区别](https://blog.csdn.net/lishenglong666/article/details/8557215)
- [Linux进程和线程关系浅析 （转载）](https://www.cnblogs.com/wangfenphph2/p/10198687.html)
- [转载_Linux进程与线程的区别](https://www.cnblogs.com/fah936861121/articles/8043187.html)

# C++
## 1. 虚析构函数：
- 解决基类的指针指向派生类对象，并用基类的指针删除派生类对象。 
- delete释放内存时，先调用子类析构函数，再调用基类析构函数，防止内存泄漏。

## 2. 纯虚函数 virtual int A() = 0;
- 纯虚函数是一种特殊的虚函数，在基类中不能对虚函数给出有意义的实现，而把它声明为纯虚函数，它的实现留给该基类的派生类去做。
- 纯虚函数只是一个接口，是个函数的声明而已，它要留到子类里去实现。
- 虚函数在子类里面可以不重写；但纯虚函数必须在子类实现才可以实例化子类。
- 带纯虚函数的类叫抽象类或者虚基类，这种类不能直接生成对象，而只有被继承，并重写其虚函数后，才能使用。抽象类被继承后，子类可以继续是抽象类，也可以是普通类。
- 它们必须在继承类中重新声明函数（不要后面的＝0，否则该派生类也不能实例化），而且它们在抽象类中往往没有定义。

## 3. 虚继承 
- 虚继承用于解决多继承条件下的菱形继承问题（浪费存储空间、存在二义性）。

## 4. 智能指针 
- [智能指针](https://blog.csdn.net/flowing_wind/article/details/81301001)
- raw pointer 如果栈空间分配失败，会抛出bad_alloc异常，应该使用try_catch捕获异常，如果不想抛出异常，int* p = new (nothrow) int 
- shared_ptr: 引用计数，使用直接初始化形式，或者使用make_shared<int>(new int)，赋值时候，例如 p=q，则左边的计数器--，右边的计数器++，方法
  p.reset() | p.reset(q) | p = q.get(), p是raw指针, p.use_count()。
- week_ptr: 解决多个类对象中shared_ptr循环引用造成的数据声明周期异常，导致对象内存不能由share_ptr正确释放的问题。方法有 q = p.lock() | p.expired() | shared_ptr<int> p = q.lock() week_ptr转换为shared_ptr | p.use_count()
- unique_ptr: 采用独占式拥有对象, 禁止使用赋值拷贝函数和拷贝赋值函数，具有移动拷贝构造函数，能转移对象所属权。能在编译过程中解决auto_ptr的问题，而且能在容器中使用和能管理动态数组，能作为函数返回值（临时右值）。方法有 unique_ptr<int> p2(p1.release()) | p2.reset(p3.release()) p3设置为NULL；
- auto_ptr: 与unique_ptr相类似，采用独占式拥有对象，但是并没有禁止赋值拷贝函数，考虑auto_ptr<int> atpr[5], 如果存在auto_ptr<int> tmp=atpr[2]; 则atpr[2]所指向的对象的归属前已经转移到tmp，运行过程中的访问*atpr[2]就会出错。
  
## 5. malloc底层原理  
- [malloc的底层原理](https://blog.csdn.net/ypshowm/article/details/89071869)
可以通过系统调用sbrk(位移量)能一定brk指针的位置，同时返回brk指针的位置，达到申请内存的目。brk(void * addr)系统调用可以直接将brk设置为某个地址，成功返回0，不成功返回-1。而rlimit则是限制进程堆内存容量的指针。 malloc 函数的实质是它有一个将可用的内存块连接为一个长长的列表的所谓空闲链表

## 6. map和unorder_map的底层实现
- [你不得不知道的哈希表](https://zhuanlan.zhihu.com/p/77533501)
开放寻址法 -- 数组实现 负载因子小于1 哈希碰撞：线性探测法，二次检查法，双重散列法，时间复杂度O(1)，最坏O(n)
线性探测法：插入，首先通过散列函数计算key，如果所在位置已经被占据，则从该位置开始往后寻找第一个空闲位置进行插入。 查找：，首先通过散列函数计算key，如果所在位置已经被占据，则从该位置开始往后查找，如果遇到空闲位置，则说明该元素在map中不存在。 删除：设置为deleted，防止搜索元素停止。
优点： 数组在内存中连续，提高CPU利用率。 缺点：哈希冲突代价高，删除比较麻烦。
拉链法 -- 链表实现 负载因子大于1 哈希碰撞：链表连接 

# 数据库
## 1. 索引
- 定义：数据库索引，是数据库管理系统中一个排序的数据结构，以协助快速查询、更新数据库表中数据。索引的实现通常使用B树及其变种B+树。）
- 大大加快数据的检索速度，使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。
- 时间方面：创建索引和维护索引要耗费时间，具体地，当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，会降低增/改/删的执行效率；
- 空间方面：索引需要占物理空间。

## 2. 存储过程
- 存储过程可以说是一个记录集吧，它是由一些T-SQL语句组成的代码块，这些T-SQL语句代码像一个方法一样实现一些功能（对单表或多表的增删改查），然后再给这个代码块取一个名字，在用到这个功能的时候调用他就行了。
- 数据库执行动作时，是先编译后执行的。然而存储过程是一个编译过的代码块，所以执行效率要比T-SQL语句高。
- 一个存储过程替代大量T_SQL语句 ，可以降低网络通信量，提高通信速率，可以一定程度上确保数据安全。

## 3. 事务
- 事务（Transaction）是并发控制的基本单位。所谓的事务，它是一个操作序列，这些操作要么都执行，要么都不执行，它是一个不可分割的工作单位。事务是数据库维护数据一致性的单位，在每个事务结束时，都能保持数据一致性。
- [数据库事务](https://blog.csdn.net/zdwzzu2006/article/details/5947062)

## 4. 数据库的乐观锁和悲观锁
- 悲观锁：假定会发生并发冲突，屏蔽一切可能违反数据完整性的操作。
在关系数据库管理系统里，悲观并发控制（又名“悲观锁”，Pessimistic Concurrency Control，缩写“PCC”）是一种并发控制的方法。它可以阻止一个事务以影响其他用户的方式来修改数据。如果一个事务执行的操作都某行数据应用了锁，那只有当这个事务把锁释放，其他事务才能够执行与该锁冲突的操作。悲观并发控制主要用于数据争用激烈的环境，以及发生并发冲突时使用锁保护数据的成本要低于回滚事务的成本的环境中。悲观锁，正如其名，它指的是对数据被外界（包括本系统当前的其他事务，以及来自外部系统的事务处理）修改持保守态度(悲观)，因此，在整个数据处理过程中，将数据处于锁定状态。 悲观锁的实现，往往依靠数据库提供的锁机制 （也只有数据库层提供的锁机制才能真正保证数据访问的排他性，否则，即使在本系统中实现了加锁机制，也无法保证外部系统不会修改数据）
- 乐观锁：假设不会发生并发冲突，只在提交操作时检查是否违反数据完整性。
在关系数据库管理系统里，乐观并发控制（又名“乐观锁”，Optimistic Concurrency Control，缩写“OCC”）是一种并发控制的方法。它假设多用户并发的事务在处理时不会彼此互相影响，各事务能够在不产生锁的情况下处理各自影响的那部分数据。在提交数据更新之前，每个事务会先检查在该事务读取数据后，有没有其他事务又修改了该数据。如果其他事务有更新的话，正在提交的事务会进行回滚。相对于悲观锁，在对数据库进行处理的时候，乐观锁并不会使用数据库提供的锁机制。一般的实现乐观锁的方式就是记录数据版本。

## 5.drop、delete与truncate区别
- [drop、truncate和delete的区别](https://blog.csdn.net/ws0513/article/details/49980547)

## 6. SQL 标准定义了四个隔离级别
- READ-UNCOMMITTED(读取未提交)： 最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。

读取数据不需要加共享锁，这样就不会跟被修改的数据上的排他锁

- READ-COMMITTED(读取已提交)： 允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生。

读操作需要加共享锁，但是在语句执行完以后释放共享锁；

- REPEATABLE-READ(可重复读)： 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。

读操作需要加共享锁，但是在事务提交之前并不释放共享锁，也就是必须等待事务执行完毕以后才释放共享锁。

- SERIALIZABLE(可串行化)： 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。

制性最强的隔离级别，因为该级别锁定整个范围的键，并一直持有锁，直到事务完成。

- [MyISAM和InnoDB的区别](https://www.cnblogs.com/zhangchaoyang/articles/4214237.html)
- [mysql中innodb和myisam对比及索引原理区别](https://blog.csdn.net/qq_27607965/article/details/79925288)


# 操作系统
## 1. 
用户态下和内核态下工作的程序有很多差别，但最重要的差别就在于特权级的不同。 系统调用|异常|外围设备的中断 ---> 中断机制
First - 从当前进程的描述符中提取其内核栈的ss0及esp0信息。

Seconde - 使用ss0和esp0指向的内核栈将当前进程的cs,eip,eflags,ss,esp信息保存起来，这个过程也完成了由用户栈到内核栈的切换过程，同时保存了被暂停执行的程序的下一条指令。

Third - 将先前由中断向量检索得到的中断处理程序的cs,eip信息装入相应的寄存器，执行中断处理程序，这时就转到了内核态的程序执行了。

SS：记录的是段选择器，用户程序不可以进程修改。| ESP：指向堆栈内部特定位置的32位的指针。
CS：代码段指针。| EIP：指令地址寄存器。




