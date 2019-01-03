# 一些java类型和Hibernate、SQL间的映射关系

![](/assets/hiber-5-2.png)

![](/assets/hiber-5-1.png)![](/assets/hiber-5-3.png)2. 映射 Java 的时间, 日期类型



1\). 两个基础知识:



I. 在 Java 中, 代表时间和日期的类型包括: java.util.Date 和 java.util.Calendar. 

此外, 在 JDBC API 中还提供了 3 个扩展了 java.util.Date 类的子类: java.sql.Date, java.sql.Time 

和 java.sql.Timestamp, 这三个类分别和标准 SQL 类型中的 DATE, TIME 和 TIMESTAMP 类型对应



II. 在标准 SQL 中, DATE 类型表示日期, TIME 类型表示时间, TIMESTAMP 类型表示时间戳, 同时包含日期和时间信息. 



2\). 如何进行映射 ?



I. 因为 java.util.Date 是 java.sql.Date, java.sql.Time 和 java.sql.Timestamp 的父类, 所以 java.util.Date

可以对应标准 SQL 类型中的 DATE, TIME 和 TIMESTAMP



II. 基于 I, 所以在设置持久化类的 Date 类型是, 设置为 java.util.Date. 



III. 如何把 java.util.Date 映射为 DATE, TIME 和 TIMESTAMP ?



可以通过 property 的 type 属性来进行映射: 



例如:

```

<property name="date" type="timestamp">
    <column name="DATE" />
</property>

<property name="date" type="data">
    <column name="DATE" />
</property>

<property name="date" type="time">
    <column name="DATE" />
</property>
```

其中 timestamp, date, time 既不是 Java 类型, 也不是标准 SQL 类型, 而是 hibernate 映射类型. 



![](/assets/hiber-5-4.png)![](/assets/hiber-5-5.png)

