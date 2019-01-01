# 详解Hibernate.cfg.xml

* 配置C3P0数据库连接池到配置文件

  1. 在 hibernate 中使用 C3P0 数据源:

1\). 导入 jar 包:

hibernate-release-4.2.4.Final\lib\optional\c3p0\\*.jar



2\). 加入配置:

hibernate.c3p0.max\_size: 数据库连接池的最大连接数

hibernate.c3p0.min\_size: 数据库连接池的最小连接数

hibernate.c3p0.acquire\_increment: 当数据库连接池中的连接耗尽时, 同一时刻获取多少个数据库连接

hibernate.c3p0.timeout:   数据库连接池中连接对象在多长时间没有使用过后，就应该被销毁

hibernate.c3p0.idle\_test\_period:  表示连接池检测线程多长时间检测一次池内的所有链接对象是否超时.

连接池本身不会把自己从连接池中移除，而是专门有一个线程按照一定的时间间隔来做这件事，

这个线程通过比较连接对象最后一次被使用时间和当前时间的时间差来和 timeout 做对比，进而决定是否销毁这个连接对象。

hibernate.c3p0.max\_statements:  缓存 Statement 对象的数量

温馨提示：在配置xml文件的时候，一定要将property标签放到mapping标签前面，否则不能解析该文件

