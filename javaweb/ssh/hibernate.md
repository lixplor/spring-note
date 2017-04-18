# Hibernate

* 持久层ORM框架, 操作数据库
* 简化了开发, 提高效率
* 提供额外功能, 如缓存


## 环境搭建

### 下载

* 官网: [http://hibernate.org/](http://hibernate.org/)

### 添加依赖包

* jar包方式
    - 在`WEB-INF/lib`下, 加入Hibernate的`require/*`, `log4j`, `slf4`


## 快速入门

### JavaBean与数据库表的映射

(可选)创建JavaBean: 如`User.java`
    - 基本类型使用包装类, 因为默认值是`null`, 有助于判断
    - xml也可以自动生成JavaBean


创建映射文件: 创建与JavaBean同名的xml, 如`User.hbm.xml`(hbm = hibernate mapping)
* `<class>`标签: 将类与数据库表建立映射关系
    - `name`: 类的完整路径
    - `table`: 表名(如果类名和表名一致, 则可以省略该属性)
    - `catalog`: 数据库名称(如果配置了则可以省略)
* `<id>`标签: 将类中的属性与表中的主键建立映射, id标签用于配置主键
    - `name`: 属性名
    - `column`: 表中字段名(如果类中属性名和表中字段名一致, 则可以省略)
    - `length`: 字段的长度, 如果数据库已经建好, 则可以省略. 如果没有建好数据库, 最好指明
* `<property>`标签: 将类中的普通属性与表中的字段建立映射
    - `name`: 类中属性名
    - `column`: 表中字段名
    - `length`: 数据长度
    - `type`: java/hibernate数据类型
        - Hibernate: `string`
        - Java: `java.lang.String`
    - `sql-type`: sql数据类型

```xml
<?xml version"1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping ...>
<hibernate-mapping>
    <!-- 配置类和表的映射 -->
    <class name="com.package.User" table="tb_user">
        <!-- 配置id, name是JavaBean属性, column是对应的表的字段 -->
        <id name="id" column="id">
            <!-- 主键策略 -->
            <generator class="native"/>
        </id>
        <!-- 配置其他属性 -->
        <property name="name" column="name" type="string" length="25"/>
    </class>
</hibernate-mapping>
```

配置数据库连接
放在`src`目录下, 名为`hibernate.cfg.xml`. 配置可参考官方样例
* `property`
    - `hibernate.hbm2dl,auto`
        - `create-drop`: 重新创建表后再删除
        - `create`: 重新创建表
        - `update`: 更新已存在的表(开发中用这个)
        - `validate`: 校验JavaBean和表字段是否一致

```xml
<hibernate-configuration>
    <!-- 必须先配置SessionFactory标签, 一个数据库对应一个sessionFactory标签 -->
    <session-factory>
        <!-- 必须要配置的参数有5个: 4大连接参数, 1个数据库的方言 -->
        <property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
        <property name="hibernate.connection.url">jdbc:mysql:///xxx</property>
        <property name="hibernate.connection.username">root</property>
        <property name="hibernate.connection.password">123456</property>
        <property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>

        <!-- 可选配置 -->
        <!-- 在控制台打印单行sql语句 -->
        <property name="hibernate.show_sql">true</property>
        <!-- 在控制台打印格式化的sql语句 -->
        <property name="hibernate.format_sql">true</property>
        <!-- 生成数据库表结构 create-drop, create, update, validate -->
        <property name="hibernate.hbm2dl,auto">update</property>

        <!-- 映射配置文件, 需要引入映射的配置文件 -->
        <mapping resource="com/package/User.hbm.xml"/>
    </session-factory>
</hibernate-configuration>
```

* 编写代码
    - 加载配置文件
    - 创建SessionFactory对象, 生成Session对象
    - 开启事务
    - 编写sql代码
    - 提交事务
    - 释放资源
* `Configuration`
    - 用于加载配置文件
* `SessionFactory`
    - 创建Session的工厂类. 作为代理, 创建Session
    - 一个项目通常只需要一个SessionFactory, 当有多个数据库时, 可以为每个数据库指定一个SessionFactory
* `Session`
    - 负责CRUD, 调用时创建, 用完后销毁
    - 非线程安全
    - `save(Object obj)`: 添加记录
    - `saveOrUpdate(Object obj)`: 添加或更新已有记录
    - `delete(Object obj)`: 通过JavaBean的主键删除指定记录
    - `update(Object obj)`: 通过JavaBean的主键更新指定记录
        - 注意更新时, 要先查询记录, 然后更新查询出来的记录
    - `get(Class clazz, int id)`: 通过主键查询记录
    - `Query createQuery(String hql)`: HQL查询
        - 条件查询:
            - `from JavaBean where field > ?`
            - `from JavaBean where field > :变量占位符`
    - `Criteria createCriteria(Class)`: Criteria条件查询
* `Query`
    - `setInteger(index, value)`: 设置hql占位符位置的值
    - `setString(index, value)`: 设置hql占位符位置的值
    - `List<T> list()`: 查询所有记录的所有字段
* `Criteria`: 条件查询
    - `add(Criterion)`: 添加查询条件
        - `Restrictions`工具类创建Criterion
            - `gt(column, value)`: 大于
            - `like(column, value)`: 匹配
    - `List<T> list()`: 查询所有记录的所有字段
* `Transaction`
    - `commit()`: 提交事务
    - `rollback()`: 回滚事务

Hibernate默认不会自动提交事务, 必须手动提交事务
如果没有开启事务, 那么Session的每个操作都作为一个单独的事务


```java
// 加载配置文件
Configuration config = new Configuration();
config.configure();  // 默认加载src目录下的hibernate.cfg.xml
// 创建SessionFactory
SessionFactory factory = config.buildSessionFactory();
// 创建Session对象
Session session = factory.openSession();
// 开启事务
Transaction tr = session.beginTransaction();
// sql

// 提交事务
tr.commit();
// 释放资源
session.close();
factory.close();
```


## 持久化类

* 持久化类: JavaBean与表建立了映射关系, 即`JavaBean + JavaBean.hbm.xml`
* 编写规则:
    - 提供一个无参数的public构造方法(用于反射)
    - 提供一个标识属性, 映射数据表主键字段(唯一标识OID, 用于确定表中记录)
    - 所有属性提供public的setter和getter
    - 标识属性尽量使用包装类型

## 主键生成策略

* `increment`
    - 适用于short, int, long类型的主键
    - 不使用自动增长
* `identity`
    - 适用于short, int, long
    - 使用自动增长, 必须使用在有自动增长数据库中
* `sequence`
    - 适用于Oracle数据库这种底层没有自动增长, 但仍要实现自动增长的情况
* `uuid`
    - 使用随机字符串作为主键
* `native`
    - 本地策略, 根据数据库的不同, 自动选择适用于该种数据库的生成策略
        - 如果使用MySQL, 相当于`identity`
        - 如果使用Oracle, 相当于`sequence`
* `assigned`
    - 主键的生成不用Hibernate管理, 手动设置主键

## 持久化对象的三个状态

* 瞬时态(transient)
    - 没有持久化标识OID
    - 没有被Session管理
    - 如: Java代码中创建了对象
* 持久态(persistent)
    - 有持久化标识OID
    - 有被Session管理
    - 如: Java代码中创建了对象, 使用session.save()保存到了缓存中
* 托管态(detached)
    - 有持久化标识OID
    - 没有被Session管理
    - 如: Java代码中创建了对象, 使用session.save()保存到了缓存中, 提交了事务, 关闭了session.close(), session不再管理, 但存在OID

三种状态的转换:
* 获取瞬时态
    - new对象
* 脱离瞬时态
    - 垃圾回收
* 获取持久态
    - `get()`
    - `load()`
    - `find()`
    - `iterate()`
    - 等等
* 脱离持久态
    - 垃圾回收
* 瞬时态 -> 持久态
    - `save()`
    - `saveOrUpdate()`
* 持久态 -> 瞬时态
    - `delete()`
* 持久态 -> 托管态
    - `evict()`
    - `close()`
    - `clear()`
* 托管态 -> 持久态
    - `update()`
    - `saveOrUpdate()`
    - `lock()`

## Session的一级缓存

* 缓存: 内存中存储数据的空间, 提升获取数据的效率
* 一级缓存: 自带, 不可取消. 生命周期与session一致, 是session级别的缓存
* 二级缓存: 默认没有开启, 需要配置开启. 二级缓存可以再多个session中共享数据, 是sessionFactory级别的缓存

## 操作session一级缓存

* `session.clear()`: 清空缓存
* `session.evict(Object entity)`: 从一级缓存中清除指定的实体对象
* `session.flush()`: 刷出缓存

## Session的快照机制

* 持久态根据缓存更新的原理
    - session操作后, 缓存区和快照区都会更新数据
    - 当修改数据后, 缓存区的数据会更新
    - commit之前, 会对比缓存区和快照区数据是否一致
        - 如果一致, 正常
        - 如果不一致, 则修改数据库中的值, 并更新快照区数据


## Transaction事务

* 事务: 一组操作, 必须同时成功, 或同时不成功
* 特性:
    - 原子性: 事务不可分割
    - 一致性: 事务执行的前后数据的完整性保持一致
    - 隔离性: 一个事务执行的过程中, 不应该受到其他的事务的干扰
    - 持久性: 事务一旦提交, 数据就永久保持到数据库中
* 如果不考虑隔离性, 会引发的:
    - 读的问题
        - 问题
            - 脏读: 一个事务读到了另一个事物未提交的数据
            - 不可重复读: 一个事务读到了另一个事物已经提交的update数据, 导致多次查询结果不一致
            - 虚读: 一个事务督导了另一个事物已经提交的insert数据, 导致多次查询结果不一致
        - 解决
        * 通过设置数据库的隔离级别来解决上述读的问题   
        Hibernate设置隔离级别, 在`hibernate.cfg.xml`配置文件中使用标签来配置`hibernate.connection.isolation = 4`
            - `1`: 未提交读(Read uncommitted isolation): 以上的读的问题都有可能发生
            - `2`: 已提交读(Read committed isolation): 避免脏读. 但是不可重复读, 虚读都有可能发生
            - `3`: 可重复读(Repeatable read isolation): 避免脏读和不可重复读. 但虚读有可能发生
            - `4`: 串行化(Serializable isolation): 以上读的情况都可以避免
    - 写的问题
        - 问题:
            - 丢失更新, 如两个事物同时修改一条记录
        - 解决:
            - 悲观锁(MySQL自带的, 不怎么用)
                - 在SQL语句后添加`for update`
            - 乐观锁(使用较多)
                - 给JavaBean添加新的属性`version`, 提供getter和setter, 每次操作判断版本
                - 然后在映射的配置文件中添加`<version name='version'/>`标签即可
* 事务绑定本地session
    - 作用: 需要在业务层开启事务, 如何操作
        - 方法1: 通过参数方式传递下去
        - 方式2: 把Connection绑定到ThreadLocal对象中
    - 原理: `ThreadLocal`类
        - 底层是Map集合: `Map<当前线程, 值>`
        - 将值保存到当前线程中, key就是固定的当前线程
        - 用于在不同层之间传递数据
    - 在hibernate框架中使用session对象开启事务, 使用框架提供的基于ThreadLocal的方式传递session对象
        - `hibernate.cfg.xml`配置开启: `<property name="hibernate.current_session_context_class">thread</property>`
        - `sessionFactory.getCurrentSession()`: 从ThreadLocal中获取session
        - 这种情况不用关session, 线程结束后session自动关闭

## 一对多操作

* 建表原则
    - 一表id是主键
    - 多表中增加一表的id字段, 作为`外键`, 指向一表的`主键`id
* 多的一方使用集合表示
    - hibernate中集合默认使用`Set`, 并且必须手动初始化

```java
// 一表
public class User {
    private Integer id;
    private Set<Contactor> contactors = new HashSet<>;  // 对多, 必须手动初始化
}

// 多表
public class Contactor {
    private Integer id;
    private User user;   // 对一
}
```

```xml
<!-- 一方 -->
<set name="多方属性名">
    <key column="外键字段"/>
    <one-to-many class="多方完整类名"/>
</set>

<!-- 多方 -->
<many-to-one name="属性名" class="类完整路径" column="外键字段"/>
```

保存数据的方式:
* 双向保存
    - 一表和多表都保存
* 单向级联保存(有方向性)
    - 保存一表, 级联保存多表
        - 在`<set>`标签中增加`cascade="save-update"`属性
        - 这时只存一表, 即可自动保存多表
    - 或者保存多表, 级联保存一表
        - 在`<many-to-one>`标签中增加`cascade="save-update"`属性
        - 这时只存多表, 即可自动保存多表
* 级联删除(有方向性)
    - 有外键引用时, 必须先把外键设置为空, 才能删除
    - 使用`cascase="delete"`属性
* `cascade`可以使用多个值, 用逗号分隔: `cascade="save-update, delete"`
    - `none`: 不使用级联
    - `save-update`: 级联保存或更新
    - `delete`: 级联删除
    - `delete-orphan`: 孤儿删除(只能应用在一对多关系)
    - `all`: 除了delete-orphan的所有情况(包含save-update, delete)
    - `all-delete-orphan`: 包含了delete-orphan的所有情况(包含save-update, delete, delete-orphan)
* 孤儿删除
    - 一对多关系中, 将一方认为是父方, 将多方认为是子方. 在解除了父子关系的时候, 将子方记录删除
* 一方放弃外键的维护
    - 作用: 避免产生多余数据
    - `<set>`标签加上`inverse="true"`属性, inverse用于维护外键
        - `true`: 放弃
        - `false`: 默认值, 不放弃


## 多对多操作

* 建表原则
    - 使用中间表, 保存两个多表的主键作为外键
    - 或者将多对多拆分为两个一对多

```xml
<!-- 多 -->
<set name="多表类属性" table="多表">
    <key column="当前对象在中间表的外键名称"/>
    <many-to-many class="集合存入对象的完整路径" column="集合中对象在中间表的外键的字段名称"/>
</set>

<!-- 另一个多, 同上 -->
```

保存数据的方式:(同一对多)
* 级联
    - 级联保存(双向级联)
    - 级联删除(多对多中很少使用)
* 放弃外键的维护
    - 注意: 多对多时, 必须要有一方放弃外键的维护


## 查询

Hibernate提供的查询方式:
* 唯一标识OID的查询方式
    - `session.get(类.class, OID)`
* 对象的导航的查询方式
    - `new User().getRole().getName()`
* HQL的查询方式(推荐)
    - Hibernate Query Language, 与SQL类似
    - 语法: **todo**
    - 查询方式
        - 链式调用
            - `session.createQuery("from Customer").list().size()`
        - 别名方式:
            - `session.createQuery("from Customer c")`
            - 别名: `session.createQuery("select c from Customer c")`
            - 条件: `session.createQuery("from User u where u.id > ?")`
                - `setParameter(int index, Object value)`: 设置指定index位置的`?`占位符的值
                - `setParameter(String key, Object value)`: 设置指定key变量的值
            - 投影: 查询指定字段
                - 普通方式返回的是对象数组: `List<Object[]> session.createQuery("select column1, column2 from User").list()`
                - 投影方式步骤:
                    1. 在JavaBean中声明包含要查询字段的带参构造方法(注意同时保留空参构造方法)
                    2. HQL语句中创建对象: `List<User> = session.createQuery("select new 类(column1, column2) from User").list();`
            - 排序: `session.createQuery("from User u order by u.id asc")`
            - 分页:
                - `setFirstResult(int offset)`: 从哪条记录开始, 从0开始
                - `setMaxResults(int limit)`: 每页需要的记录条数
                - 每页10个第2页: `List<User> users = session.createQuery("from User").setFirstResult(10).setMaxResults(10).list();`
            - 聚合:
                -
                - `count()`:
                    - `List<Number> list = session.createQuery("select count(*) from User").list();`
                    - `int count = list.get(0).intValue();`
                - `sum()`
                    - `List<Number> list = session.createQuery("select sum(id) from User").list();`
                    - `Long sum = list.get(0).longValue();`
                - `avg()`
                - `max()`
                - `min()`
* QBC查询方式(推荐, 用于条件查询)
    - Query By Criteria, 条件查询
    - `Criteria c = session.createCriteria(类.class);`
    - `Criteria`
        - 排序: `addOrder(Order.asc|desc(column))`
        - 分页
            - `setFirstResult(int offset)`
            - `setMaxResults(int limit)`
        - 条件: `criteria.add(Restrictions)`
            - `Restrictions.eq()`: 等于
            - `Restrictions.gt()`: 大于
            - `Restrictions.ge()`: 大于等于
            - `Restrictions.lt()`: 小于
            - `Restrictions.le()`: 大于等于
            - `Restrictions.between()`: 介于
            - `Restrictions.like()`: 模式匹配
            - `Restrictions.in()`: 在列表中
            - `Restrictions.and()`: 条件与
            - `Restrictions.or()`: 条件或
            - `Restrictions.isNull()`: 为空
        - 聚合
            - `List<Number> list = criteria.setProjection(Projections)`
                - `Projections.count()`
            - 清空Projection: `criteria.setProjection(null);`
                - 设置一种Projection后, 后续查询都会使用该Project, 如需更换查询, 必须清空Projection
        - 离线条件查询
            - 脱离session来创建查询, 可以不局限于service层查询
            - 创建离线条件查询对象: `DetachedCriteria dc = DetachedCriteria.fromClass(类.class);`
            - 执行查询: `List<T> list = dc.getExecutableCriteria(session).list();`
* SQL查询方式
    - 创建查询: `SQLQuery q = session.createSQLQuery(String sql);`
    - 默认返回Object数组: `List<Object[]> list = q.list();`
        - 可以通过设置返回类型修改返回List的泛型: `q.addEntity(类.class)`

### HQL进行多表查询

* 内连接查询
    - HQL: `from User u inner join fetch u.orders`
    - 两个问题的处理:
        - 返回列表泛型是Object[]的问题, 想要变成类型: 使用`fetch`关键字
            - fetch: 迫切连接, 把数据封装到对象中
        - 查询结果重复的问题: 将List集合放入Set中去重
* 左外连接查询
    - HQL: `from User u left join fetch u.orders`
* 右外连接查询
    - HQL: `from User u right join fetch u.orders`


## 性能优化

* 延迟加载, 提升性能
    - 先获取代理对象, 当真正用到该对象的属性时, 才会发送SQL语句
    - 2种延迟加载:
        - 类级别的延迟加载
            - session对象的load方法默认就是延迟加载: `User u = session.load(User.class, 1L);`
            - 在xml配置中的`<class>`标签增加属性`lazy="true"`, 设置为`false`可以关闭延迟加载
        - 关联级别的延迟加载
            - 默认就是延迟加载
* `<set>`标签的配置策略
    - `fetch`: 控制SQL语句的生成格式
        - `select`: 默认, 发送查询语句
        - `join`: 连接查询, 发送一条迫切左外连接, 配置了join, lazy就会失效
        - `subselect`: 子查询. 发送一条子查询, 查询其关联对象
    - `lazy`: 查找关联对象的时候是否采用延迟加载
        - `true`: 默认, 使用延迟加载
        - `false`: 不使用延迟加载
        - `extra`: 极为延迟
* `<many-to-one>`标签的配置策略:
    - `fetch`: 控制SQL格式
        - `select`: 默认, 发送基本select语句查询
        - `join`: 发送迫切左外连接查询
    - `lazy`: 控制加载关联对象是否采用延迟
        - `proxy`: 默认, 代理方式, 是否延迟由一表的以下情况决定:
            - 如果一表的`<class>`上的lazy为`true`, 则proxy的值就是`true`, 延迟加载
            - 否则就是`false`, 不延迟加载
        - `false`: 不采用