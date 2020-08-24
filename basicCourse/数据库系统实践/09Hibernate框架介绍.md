# 数据库系统实践
## 09Hibernate框架介绍
#### 1. Hibernate简介
- Hibernate是一个开放源代码的ORM(Object Relational Mapping，对象关系映射)框架，它对JDBC进行了非常轻量级的对象封装，它将POJO与数据库表建立映射关系，是一个全自动的ORM框架，Hibernate可以自动生成SQL语句，自动执行，使得Java程序员可以随心所欲的使用对象编程思维来操纵数据库。
- MVC全名是Model View Controller，是模型(model)－视图(view)－控制器(controller)的缩写，一种软件设计典范，用一种业务逻辑、数据、界面显示分离的方法组织代码，将业务逻辑聚集到一个部件里面，在改进和个性化定制界面及用户交互的同时，不需要重新编写业务逻辑
- 在实际应用场景中，各大企业公司根据实际需要，基于MVC模式设计了很多框架，常见的有SSH框架（Struts+Spring+Hibernate），其中Hibernate框架对持久层提供支持
- 展示层-提供用户交互界面
- 业务逻辑层-实现各种业务和逻辑
- 数据访问层-负责存放和管理应用程序的持久化业务数据
- Hibernate 将JDBC进行了很好的封装。Hibernate的目标是释放开发者通常的与数据库持久化相关的编程任务的 95%，消除那些针对特定数据库厂商的SQL代码
- 使用ORM框架的优缺点
    + 优点：快速开发，隐藏数据访问细节；数据模型集中定义，容易更新和维护，重用代码；迫使项目使用MVC模式，代码结构清晰
    + 缺点：牺牲性能；难以处理复杂的SQL查询；有一定学习成本，配置复杂
#### 2. Hibernate原理
- 连接数据库：通过hibernate.cfg.xml配置文件中的配置 ：提供用户交互界面，在这个文件中定义了数据库进行连接所需要的信息，包括JDBC驱动、用户名、密码、数据库方言，Configuration类借助dom4j的XML解析器解析设置环境，然后使用这些环境属性来生成 SessionFactory。这样这个sessionFactory生成的session就能成功获得数据库的连接。
- 操作数据库：对数据库的操作包括保存、更新和删除。当保存一个POJO持久对象时，触发Hibernate的保存事件监听器进行处理。Hibernate通过映射文件获得对象对应数据库表名以及属性所对应的表中的列名，然后通过反射机制持久化对象（实体对象）的各个属性，最终组织成向数据库插入新对象的SQL insert语句。调用了session.save()方法后，这个对象会标识成持久化状态存放在session中，对于Hibernate来说它就是一个持久化了的对象，但这个时候Hibernate还不会真正的执行insert语句，当进行session的刷新同步或事务提交时，Hibernate会把session缓存中的所有SQL语句一起执行，对于更新、删除操作也是采用类似的机制。然后，提交事务并事务提交成功后，这些写操作就会被永久地保存进数据库中，所以，使用session对数据库操作还依赖于Hibernate事务的处理。如果设置了二级缓存，那么这些操作会被同步到二级缓存中，Hibernate对数据库最终操作也是依赖于底层JDBC对数据库进行。
- 查询数据：Hibernate提供SQL HQL Criteria查询方式。HQL是其中运用最广泛的查询方式。用户使用 session.createQuery()方法以一条HQL语句为参数创建 Query查询对象后，Hibernate会使用Anltr库把HQL语句 解析成JDBC可以识别的SQL语句，如果设置了查询缓存，那么执行 Query.list()时，Hibernate会先对查询缓存进行查询，如果查询 缓存不存在，再使用select语句查询数据库。
#### 3. Hibernate工作流程
在使用Hibernate框架时候，通常会使用到下面5个主要接口：Configuration接口、SessionFactory接口、Session接口、Transaction接口和Query接口。通过这些接口可以对持久化对象进行操作，还可以进行事务控制。
#### 4. Hibernate核心组件
在Hibernate框架使用过程中，用户调用Hibernate API去对持久化对象操作时候，使用最多的6个核心组件是：Configuration接口、SessionFactory接口、Session接口、Transaction接口、Query接口和Criteria接口。
#### 5. Hibernate框架的配置
Hibernate框架配置主要有下面四个步骤：导入相关Hibernate的jar包、创建数据库及表、创建实体类（持久化类）、配置映射文件XX.hbm.xml、配置主配置文件hibernate.cfg.,xml、编写数据库操作(增删改查)。