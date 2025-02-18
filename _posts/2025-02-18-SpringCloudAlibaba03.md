---
title: "JPA持久化技术入门"
date: 2025-02-18
---

## 导入依赖

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```
## 创建实体类
实体类可以与数据库中的表一一对应
```java

@Data
@Entity  ## 标记这是与数据库中的depart表进行对应的实体类
@JsonIgnoreProperties({"hibernateLazyInitializer","handler","fieldHandler"})   ## 处理代理对象查询为空的错误
public class Depart {

    @Id   # 标记表主键
    @GeneratedValue(strategy = GenerationType.IDENTITY)  # 设置主键自增
    private Integer id;

    private String name;

}
```

## 实现数据库操作接口
JPA的JpaRepository<A,B>接口已经规定了很多基础操作并且进行了实现，只需要继承即可
其中A为表格实体类类型,B为主键类型

```java
public interface DepartRepository extends JpaRepository<Depart, Integer> { }
```

## 注入DepartRepository接口实现类进行操作

```java
@Service
public class DepartServiceImpl implements DepartService {

    @Autowired
    private DepartRepository departRepository;

    @Override
    public boolean saveDepart(Depart depart) {
        Depart r = departRepository.save(depart);
        System.out.println(r.toString());
        if (r != null) {
            return true;
        }
        return false;
    }

    @Override
    public boolean updateDepart(Depart depart) {
        Depart r = departRepository.save(depart);
        if (r != null) {
            return true;
        }
        return false;
    }

    @Override
    public Depart getDepart(int id) {
        return departRepository.getReferenceById(id);
    }

    @Override
    public List<Depart> getAllDepart() {
        return departRepository.findAll();
    }

    @Override
    public boolean deleteDepart(int id) {
        if (departRepository.existsById(id)){
            departRepository.deleteById(id);
            return true;
        }
        return false;
    }
}
```

## 相关配置
```java
spring:
  jpa:
    generate-ddl: true  ## 根据实体类建表
    show-sql: true      ## 在控制台打印建表语句
    hibernate:
      ddl-auto: none    ## 表已存在时不再重新建表

logging:
  level:
    # 显示动态参数值
    org.hibernate.type.descriptor.sql.BasicBinder: trace
    # 显示查询结果
    org.hibernate.type.descriptor.sql.BasicExtractor: trace
```
