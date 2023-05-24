---
title: 使用MyBatisPlus实现自动填充数据功能
tags:
  - java
  - 技术
  - 学习笔记
  - SpringBoot
  - MybatisPlus
  - Mybatis
category: java
date: 2023-05-24 14:50:52
---

在项目的开发中时常会有一些相似规律的字段需要在保存或者新增的时候填充一些数据进去，比如 **create\_time**、**create\_by**、**update\_time**、**update\_by** 等等。这些字段在保存或者新增的时候要手动添加进去这是很麻烦的。例如下面的方式

```java
    @Test
    void testAutoFill() {
        SysUser sysUser = new SysUser();
        sysUser.setName("test");
        sysUser.setAge(18);
        sysUser.setEmail("runbrick@cc.com");

        sysUser.setCreateBy("admin");
        sysUser.setCreateTime(LocalDateTime.now());
        sysUserMapper.insert(sysUser);
    }

```
这样在写每个方法需要填充这个值的时候将是噩梦，这时候就凸显出有一个自动填充功能的好处了。实现自动填充的方法有很多这篇文章主要是使用 **MyBatis Plus** 来实现。毕竟比较方便有现成的轮子干嘛要自己费劲巴拉的再去创造轮子呢。

![image](images/BxuYLKobMF8Yot63S8led1JPc5SSjFWzwlEGK2XzmN0.gif)

**MyBatis Plus 自动填充的优点很多**

1. 简化字段填充：自动填充功能可以在插入或更新操作时自动填充指定的字段值，无需手动编写填充代码。例如，你可以使用自动填充功能在插入记录时自动填充创建时间，或在更新记录时自动填充更新时间。这样可以减少代码量，提高开发效率。
2. 减少冗余代码：在传统的开发中，为了填充某些字段的值，通常需要在每个相关的方法中手动编写相应的代码。使用自动填充功能后，这些重复的代码可以集中在一个地方进行管理，减少了代码的冗余性，并且在需要修改填充逻辑时也更加方便。
3. 维护数据一致性：通过自动填充功能，可以确保一些字段的值在每次操作时保持一致。例如，你可以使用自动填充功能在插入记录时自动填充创建人和更新人字段，保证这些字段的值始终正确且一致。
4. 可扩展性：自动填充功能是可扩展的，你可以根据自己的需求进行定制和扩展。MyBatis-Plus提供了自定义的填充处理器（**MetaObjectHandler**），你可以实现自己的填充逻辑，并通过配置将其与自动填充功能集成。

综上所述，**MyBatis-Plus** 的自动填充功能可以简化开发过程，减少冗余代码，提高代码的可维护性和数据的一致性，同时还具有一定的可扩展性。

下面来写一个简单的示例程序，来体验一下自动填充带来的便利。首先先展示一下我本地的环境

> 1. Windows 11 笔记本电脑一台
> 2. jdk 1.8 的环境
> 3. 一款趁手的 IDE
> 4. SpringBoot 2.7.11 + mybatis-plus 3.5.3.1
> 5. mysql 5.7
> 6. lombok

创建一个测试表后面测试要用到

```sql
CREATE TABLE `sys_user` (
  `id` varchar(32) NOT NULL,
  `name` varchar(255) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  `email` varchar(255) DEFAULT NULL,
  `create_time` datetime DEFAULT NULL,
  `create_by` varchar(255) DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  `update_by` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

```
下面是代码结构

![image](images/uums_FEkZleZVbb2_sNmf4F9x5nfm8N20xZ8SCcVz3g.png)

创建相关代码

```java
@Data
@TableName(value = "sys_user")
public class SysUser {

    @TableId(type = IdType.ASSIGN_UUID)
    private String id;

    private String name;

    private Integer age;

    private String email;


    @TableField(fill = FieldFill.INSERT)
    private LocalDateTime createTime;

    @TableField(fill = FieldFill.INSERT)
    private String createBy;

    @TableField(fill = FieldFill.UPDATE)
    private LocalDateTime updateTime;

    @TableField(fill = FieldFill.UPDATE)
    private String updateBy;
}

```
```java
@Mapper
public interface SysUserMapper extends BaseMapper<SysUser> {
}

```
上面实体中 @TableField(fill = FieldFill.INSERT) 就是可以让字段自动填充的注解，其中 FieldFill.INSERT 是代表新建行的时候填充一些数据进去。扒了一下这个枚举类的代码可以使用下面几种类型。

```java
public enum FieldFill {
    /**
     * 默认不处理
     */
    DEFAULT,
    /**
     * 插入时填充字段
     */
    INSERT,
    /**
     * 更新时填充字段
     */
    UPDATE,
    /**
     * 插入和更新时填充字段
     */
    INSERT_UPDATE
}


```
下面的类是上面注解能生效的关键

```java
@Slf4j
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {

    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("start insert fill ....");
        this.strictInsertFill(metaObject, "createTime", LocalDateTime.class, LocalDateTime.now());
        this.strictInsertFill(metaObject, "createBy", String.class, "admin");
    }

    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("start update fill ....");
        this.strictUpdateFill(metaObject, "updateTime", LocalDateTime.class, LocalDateTime.now());
        this.strictUpdateFill(metaObject, "updateBy", String.class, "admin");
    }
}

```
> 注意事项：

> * 填充原理是直接给entity的属性设置值!!!
> * 注解则是指定该属性在对应情况下必有值,如果无值则入库会是null
> * MetaObjectHandler提供的默认方法的策略均为:如果属性有值则不覆盖,如果填充值为null则不填充
> * 字段必须声明TableField注解,属性fill选择对应策略,该声明告知Mybatis-Plus需要预留注入SQL字段
> * 填充处理器MyMetaObjectHandler在 Spring Boot 中需要声明@Component或@Bean注入
> * 要想根据注解FieldFill.xxx和字段名以及字段类型来区分必须使用父类的strictInsertFill或者strictUpdateFill方法
> * 不需要根据任何来区分可以使用父类的fillStrategy方法
> * update(T t,Wrapper updateWrapper)时t不能为空,否则自动填充失效




