---
tags: [JAVA]
title: JAVA学习笔记(杂)
created: '2021-03-01T08:47:06.487Z'
modified: '2021-03-01T08:54:35.119Z'
---

# JAVA学习笔记(杂)

![JAVA-IO](./JAVA-IO.jpg)

```
spring.datasource.driverClassName=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://172.21.100.239:3306
spring.datasource.username=contenthub
spring.datasource.password=Cxp@6TuFYa7s
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jackson.serialization.indent_output=true
```

```
@Controller
public class hello {
    @ResponseBody
    @RequestMapping("/hello")
    public String index() {
        return "Hello World";
    }
}

或者

@RestController
public class hello {

    @RequestMapping("/hello")
    public String index() {
        return "Hello World";
    }
}
```

典型示例

  root package结构：com.example.myproject
  应用主类Application.java置于root package下，通常我们会在应用主类中做一些框架配置扫描等配置，我们放在root package下可以帮助程序减少手工配置来加载到我们希望被Spring加载的内容
  实体（Entity）与数据访问层（Repository）置于com.example.myproject.domain包下
  逻辑层（Service）置于com.example.myproject.service包下
  Web层（web）置于com.example.myproject.web包下

```
com
  +- example
    +- myproject
      +- Application.java
      |
      +- domain
      |  +- Customer.java
      |  +- CustomerRepository.java
      |
      +- service
      |  +- CustomerService.java
      |
      +- web
      |  +- CustomerController.java
      |
```


```
@Controller：修饰class，用来创建处理http请求的对象
@RestController：Spring4之后加入的注解，原来在@Controller中返回json需要@ResponseBody来配合，如果直接用@RestController替代@Controller就不需要再配置@ResponseBody，默认返回json格式。
@RequestMapping：配置url映射


spring.jpa.properties.hibernate.hbm2ddl.auto是hibernate的配置属性，其主要作用是：自动创建、更新、验证数据库表结构。该参数的几种配置如下：
create：每次加载hibernate时都会删除上一次的生成的表，然后根据你的model类再重新来生成新表，哪怕两次没有任何改变也要这样执行，这就是导致数据库表数据丢失的一个重要原因。
create-drop：每次加载hibernate时根据model类生成表，但是sessionFactory一关闭,表就自动删除。
update：最常用的属性，第一次加载hibernate时根据model类会自动建立起表的结构（前提是先建立好数据库），以后加载hibernate时根据model类自动更新表结构，即使表结构改变了但表中的行仍然存在不会删除以前的行。要注意的是当部署到服务器后，表结构是不会被马上建立起来的，是要等应用第一次运行起来后才会。
validate：每次加载hibernate时，验证创建数据库表结构，只会和数据库中的表进行比较，不会创建新表，但是会插入新值。
```


一、声明实体
```
@Entity
对实体注释。任何Hibernate映射对象都要有这个注释
@Table
声明此对象映射到数据库的数据表，通过它可以为实体指定表(talbe),目录(Catalog)和schema的名字。
该注释不是必须的，如果没有则系统使用默认值(实体的短类名)。
@Version
该注释可用于在实体Bean中添加乐观锁支持。
```


二、声明主键
```
@Id
声明此属性为主键。该属性值可以通过应该自身创建，但是Hibernate推荐通过Hibernate生成
@GeneratedValue
指定主键的生成策略。有如下四个值
TABLE：使用表保存id值
IDENTITY：identitycolumn
SEQUENCR ：sequence
AUTO：根据数据库的不同使用上面三个
```


三、声明普通属性
```
@Column
声明该属性与数据库字段的映射关系。
 @Column(name=”category_name” length=20)
   Public void getCategoryName(){
     Return this.categoryName;
 } 
```

注意：
1、 当POJO有属性不需要映射的时候一定要用@Transitent修饰，该注释表示此属性与表没有映射关系，只是一个暂时的属性。
2、 @Lob注释表示该属性持久化为Blob或者Clob类型，具体取决于属性的类型。

四、声明关联关系
```
一对多关联关系
@OneToMany(mappedBy=” person”,cascade=CascadeType.ALL,fetch=FetchType.LAZY)
一对多声明
@ManyToOne(cascade=CascadeType.REFRESH,)
@JoinColumn
多对一声明 ,声明为双向关联
一对一关联关系
@OneToOne(optional= true,cascade =CascadeType.ALL, mappedBy = “person”)
一对一关联声明
@OneToOne(optional = false, cascade = CascadeType.REFRESH)
@JoinColumn(name = “Person_ID”, referencedColumnName = “personid”,unique = true)
声明为双向关联
多对多关联关系
@ManyToMany(mappedBy= “students”)
多对多关联声明。
@ManyToMany(cascade = CascadeType.PERSIST, fetch = FetchType.LAZY)
@JoinTable(name = “Teacher_Student”,
joinColumns = {@JoinColumn(name = “Teacher_ID”, referencedColumnName =“teacherid”)},
inverseJoinColumns = {@JoinColumn(name = “Student_ID”, referencedColumnName =“studentid”)})
```

[jpa 接口文档](https://docs.spring.io/spring-data/data-jpa/docs/current/api/)

```
@Configuration
public class DataSourceConfig {
 
    @Bean(name = "primaryDataSource") // 命名这个datasource，用来区分不同的bean，比如多个数据库源
    @Qualifier("primaryDataSource") // @Autowired默认是根据类型进行注入的，因此如果有多个类型一样的Bean候选者，
                                   // Qualifier则需要限定其中一个候选者，否则将抛出异常,
                                   // @Qualifier限定描述符除了能根据名字进行注入，更能进行更细粒度的控制如何选择候选者
    @ConfigurationProperties(prefix="spring.datasource.primary") // 读取前缀是什么的配置
    public DataSource primaryDataSource() {
        return DataSourceBuilder.create().build();
    }
}
```

【注】：
在看别人写的Makefile文件时，你可能会碰到以下三个变量：$@，$^，$<代表的意义分别是： 
他们三个是十分重要的三个变量，所代表的含义分别是：
```
$@--目标文件，$^--所有的依赖文件，$<--第一个依赖文件。
```

                   
