#### ORM框架
Hibernate是一种ORM框架，全称为 Object_Relative DateBase-Mapping，在Java对象与关系数据库之间建立某种映射，以实现直接存取Java对象。

              O代表的是Objcet
              R代表的是Relative
              M代表的是Mapping

#### 工作流程

             读取并解析配置文件。
             读取并解析映射文件，创建 SessionFactory。
             打开 Session。
             创建事务。
             进行持久化操作。
             提交事务。
             关闭 Session。
             关闭 SessionFactory。
             
             三种查询方式：hql、原生 SQL、条件查询 Criteria。
             在Config里面把hibernate. show_SQL设置为true，可以在控制台中打印程序运行的sql语句，但不建议开启，开启之后会降低程序的运行效率。
             使用Integer和int类型做映射区别：Integer类型为对象，它的值允许为null，而int属于基础数据类型，值不能为null。
             get()和 load()的区别：load()支持延迟加载；get() 不支持延迟加载。

#### 缓存机制

            hibernate 常用的缓存有一级缓存和二级缓存：
            一级缓存：也叫 Session 缓存，只在 Session 作用范围内有效，不需要用户干涉，由 hibernate 自身维护，可以通过：evict(object)清除 object 的缓存；
            clear()清除一级缓存中的所有缓存；flush()刷出缓存；
            二级缓存：应用级别的缓存，在所有 Session 中都有效，支持配置第三方的缓存，如：EhCache。

#### hibernate对象状态
* 临时/瞬时状态：直接 new 出来的对象，该对象还没被持久化（没保存在数据库中），不受 Session 管理。
* 持久化状态：当调用 Session 的 save/saveOrupdate/get/load/list 等方法的时候，对象就是持久化状态。
* 游离状态：Session 关闭之后对象就是游离状态。

#### 在hibernate中getCurrentSession和openSession的区别
* getCurrentSession 会绑定当前线程，而 openSession 则不会。
* getCurrentSession 事务是 Spring 控制的，并且不需要手动关闭，而 openSession 需要我们自己手动开启和提交事务。

