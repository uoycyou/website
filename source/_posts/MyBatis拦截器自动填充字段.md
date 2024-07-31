---
title: MyBatis拦截器自动填充字段
tags:
  - Java
  - MyBatis
  - SpringBoot
categories:
  - MyBatis
cover: https://pic.zeng.cyou/wide/202406021253744.webp
abbrlink: 21450
date: 2024-07-14 15:52:28
---

> PS:在使用MyBatis的时候,需要对数据表里的更新时间和创建时间进行填充,但是每次手动写太过于麻烦,于是我们可以用MyBatis里的拦截器进行操作,实现自动填充

## 自定义注解

### CreatedAt

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface CreatedAt {

}
```

### UpdatedAt

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface UpdatedAt {

}
```

## 拦截器

```java
@Intercepts({@Signature(type = Executor.class, method = "update", args = {MappedStatement.class, Object.class})})
public class AutoTimestampInterceptor implements Interceptor {

    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        Object[] args = invocation.getArgs();
        MappedStatement ms = (MappedStatement) args[0];
        SqlCommandType sqlCommandType = ms.getSqlCommandType();
        Object parameter = args[1];
        if (parameter != null) {
            if (sqlCommandType == SqlCommandType.INSERT) {
                fillCreatedAt(parameter);
                fillUpdatedAt(parameter);
            } else if (sqlCommandType == SqlCommandType.UPDATE) {
                fillUpdatedAt(parameter);
            }
        }
        return invocation.proceed();
    }

    private void fillCreatedAt(Object parameter) throws IllegalAccessException {
        Field[] fields = parameter.getClass().getDeclaredFields();
        for (Field field : fields) {
            if (field.isAnnotationPresent(CreatedAt.class)) {
                field.setAccessible(true);
                if (field.get(parameter) == null) {
                    field.set(parameter, new Date());
                }
            }
        }
    }

    private void fillUpdatedAt(Object parameter) throws IllegalAccessException {
        Field[] fields = parameter.getClass().getDeclaredFields();
        for (Field field : fields) {
            if (field.isAnnotationPresent(UpdatedAt.class)) {
                field.setAccessible(true);
                field.set(parameter, new Date());
            }
        }
    }

}
```

## 使用方法

```java
    /**
     * 创建时间
     */
    @CreatedAt
    private Date createdAt;

    /**
     * 更新时间
     */
    @UpdatedAt
    private Date updatedAt;
```

```xml
<insert id="register" parameterType="User">
    INSERT INTO user
        (id, account, password, name, avatar, created_at, updated_at)
    VALUES (#{id}, #{account}, #{password}, #{name}, #{avatar}, #{createdAt}, #{updatedAt})
</insert>
```
