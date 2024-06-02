---
title: Spring Boot通用类
tags:
  - Java
  - SpringBoot
categories:
  - Java
cover: https://raw.githubusercontent.com/uoycyou/pichost/main/wide/202406021253742.webp
abbrlink: 29646
date: 2024-06-01 21:42:37
---

## Enums

### 自定义枚举

```java
public enum AppHttpCodeEnum {

    SUCCESS(200, "操作成功"),
    SYSTEM_ERROR(500, "系统错误"),

    private Integer code;

    private String msg;

    AppHttpCodeEnum(Integer code, String errorMessage) {
        this.code = code;
        this.msg = errorMessage;
    }

    public Integer getCode() {
        return code;
    }

    public String getMsg() {
        return msg;
    }

}
```

## Result

### 通用响应类

```java
public class ResponseResult<T> {

    private Integer code; //状态码
    private String msg; //消息
    private T data; //数据
    private Integer totalItems; //总条目数
    private Integer currentPage; //当前页码
    private Integer pageSize; //每页数据量

    //在类内部使用Builder模式创建ResponseResult对象
    private ResponseResult(Builder<T> builder) {
        this.code = builder.code;
        this.msg = builder.msg;
        this.data = builder.data;
        this.totalItems = builder.totalItems;
        this.currentPage = builder.currentPage;
        this.pageSize = builder.pageSize;
    }

    //创建一个成功的响应
    public static <T> ResponseResult<T> success() {
        return new Builder<T>(AppHttpCodeEnum.SUCCESS.getCode(), AppHttpCodeEnum.SUCCESS.getMsg()).build();
    }

    //创建一个成功的响应,传入指定状态码和消息
    public static <T> ResponseResult<T> success(Integer code, String msg) {
        return new Builder<T>(code, msg).build();
    }

    //创建一个成功的响应,传入预设枚举
    public static <T> ResponseResult<T> success(AppHttpCodeEnum enums) {
        return new Builder<T>(enums.getCode(), enums.getMsg()).build();
    }

    //创建一个成功的响应,传入预设枚举和自定义信息
    public static <T> ResponseResult<T> success(AppHttpCodeEnum enums, String msg) {
        return new Builder<T>(enums.getCode(), msg).build();
    }

    //创建一个成功的响应,传入数据和预设枚举
    public static <T> ResponseResult<T> success(T data, AppHttpCodeEnum enums) {
        return new Builder<T>(enums.getCode(), enums.getMsg()).data(data).build();
    }

    //创建一个成功的响应,传入数据,预设枚举和自定义信息
    public static <T> ResponseResult<T> success(T data, AppHttpCodeEnum enums, String msg) {
        return new Builder<T>(enums.getCode(), msg).data(data).build();
    }

    //创建一个成功的响应,传入数据和分页信息
    public static <T> ResponseResult<T> success(T data, Integer totalItems, Integer currentPage, Integer pageSize) {
        return new Builder<T>(AppHttpCodeEnum.SUCCESS.getCode(), AppHttpCodeEnum.SUCCESS.getMsg())
                .data(data)
                .totalItems(totalItems)
                .currentPage(currentPage)
                .pageSize(pageSize)
                .build();
    }

    //创建一个失败的响应
    public static <T> ResponseResult<T> error() {
        return new Builder<T>(AppHttpCodeEnum.SYSTEM_ERROR.getCode(), AppHttpCodeEnum.SYSTEM_ERROR.getMsg()).build();
    }

    //创建一个失败的响应,传入指定状态码和消息
    public static <T> ResponseResult<T> error(Integer code, String msg) {
        return new Builder<T>(code, msg).build();
    }

    //创建一个失败的响应,传入预设枚举
    public static <T> ResponseResult<T> error(AppHttpCodeEnum enums) {
        return new Builder<T>(enums.getCode(), enums.getMsg()).build();
    }

    //创建一个失败的响应,传入预设枚举和自定义信息
    public static <T> ResponseResult<T> error(AppHttpCodeEnum enums, String msg) {
        return new Builder<T>(enums.getCode(), msg).build();
    }

    //创建一个失败的响应,传入数据和预设枚举
    public static <T> ResponseResult<T> error(T data, AppHttpCodeEnum enums) {
        return new Builder<T>(enums.getCode(), enums.getMsg()).data(data).build();
    }

    //创建一个失败的响应,传入数据,预设枚举和自定义信息
    public static <T> ResponseResult<T> error(T data, AppHttpCodeEnum enums, String msg) {
        return new Builder<T>(enums.getCode(), msg).data(data).build();
    }

    public Integer getCode() {
        return code;
    }

    public String getMsg() {
        return msg;
    }

    public T getData() {
        return data;
    }

    public Integer getTotalItems() {
        return totalItems;
    }

    public Integer getCurrentPage() {
        return currentPage;
    }

    public Integer getPageSize() {
        return pageSize;
    }

    public static class Builder<T> {

        private Integer code;
        private String msg;
        private T data;
        private Integer totalItems;
        private Integer currentPage;
        private Integer pageSize;

        public Builder(Integer code, String msg) {
            this.code = code;
            this.msg = msg;
        }

        public Builder<T> data(T data) {
            this.data = data;
            return this;
        }

        public Builder<T> totalItems(Integer totalItems) {
            this.totalItems = totalItems;
            return this;
        }

        public Builder<T> currentPage(Integer currentPage) {
            this.currentPage = currentPage;
            return this;
        }

        public Builder<T> pageSize(Integer pageSize) {
            this.pageSize = pageSize;

            return this;
        }

        public ResponseResult<T> build() {
            return new ResponseResult<>(this);
        }
    }

}
```

## Handler

### 自定义异常类

```java
public class CustomException extends RuntimeException {

    private Integer errorCode;
    private String errorMsg;

    public CustomException() {
        super();
    }

    //传入枚举作为参数
    public CustomException(AppHttpCodeEnum enums) {
        super(enums.getMsg());
        this.errorCode = enums.getCode();
        this.errorMsg = enums.getMsg();
    }

    //传入错误信息作为参数
    public CustomException(String errorMsg) {
        super(errorMsg);
        this.errorCode = AppHttpCodeEnum.SYSTEM_ERROR.getCode();
        this.errorMsg = errorMsg;
    }

    //传入枚举和Throwable作为参数
    public CustomException(AppHttpCodeEnum enums, Throwable cause) {
        super(enums.getMsg(), cause);
        this.errorCode = enums.getCode();
        this.errorMsg = enums.getMsg();
    }

    //传入枚举和错误信息作为参数
    public CustomException(AppHttpCodeEnum enums, String errorMsg) {
        super(errorMsg);
        this.errorCode = enums.getCode();
        this.errorMsg = errorMsg;
    }

    //传入枚举,错误信息和Throwable作为参数
    public CustomException(AppHttpCodeEnum enums, String errorMsg, Throwable cause) {
        super(errorMsg, cause);
        this.errorCode = enums.getCode();
        this.errorMsg = errorMsg;
    }

    public Integer getErrorCode() {
        return errorCode;
    }

    public String getErrorMsg() {
        return errorMsg;
    }

}
```

### 全局异常处理类

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    private static final Logger logger = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    //处理自定义的业务异常
    @ExceptionHandler(value = CustomException.class)
    @ResponseBody
    public ResponseResult<String> bizExceptionHandler(CustomException e) {
        logger.error("发生业务异常:{}", e.getErrorMsg());
        return ResponseResult.error(e.getErrorCode(), e.getErrorMsg());
    }

}
```

## Utils

### RedisUtil

```java
@Component
public class RedisUtil {

    @Autowired
    public RedisTemplate redisTemplate;

    /**
     * 缓存基本的对象,Integer,String,实体类等
     *
     * @param key 缓存的键
     * @param value 缓存的值
     */
    public <T> void setCacheObject(final String key, final T value) {
        redisTemplate.opsForValue().set(key, value);
    }

    /**
     * 缓存基本的对象,Integer,String,实体类等
     *
     * @param key 缓存的键
     * @param value 缓存的值
     * @param timeout 时间
     * @param timeUnit 时间颗粒度
     */
    public <T> void setCacheObject(final String key, final T value, final Integer timeout, final TimeUnit timeUnit) {
        redisTemplate.opsForValue().set(key, value, timeout, timeUnit);
    }

    /**
     * 设置有效时间
     *
     * @param key 缓存的键
     * @param timeout 超时时间
     * @return true=设置成功,false=设置失败
     */
    public boolean expire(final String key, final long timeout) {
        return expire(key, timeout, TimeUnit.SECONDS);
    }

    /**
     * 设置有效时间
     *
     * @param key 缓存的键
     * @param timeout 超时时间
     * @param unit 时间单位
     * @return true=设置成功,false=设置失败
     */
    public boolean expire(final String key, final long timeout, final TimeUnit unit) {
        return redisTemplate.expire(key, timeout, unit);
    }

    /**
     * 获得缓存的基本对象
     *
     * @param key 缓存的键
     * @return 缓存的对象
     */
    public <T> T getCacheObject(final String key) {
        ValueOperations<String, T> operation = redisTemplate.opsForValue();
        return operation.get(key);
    }

    /**
     * 删除单个对象
     *
     * @param key 缓存的键
     * @return true=删除成功,false=删除失败
     */
    public boolean deleteObject(final String key) {
        return redisTemplate.delete(key);
    }

    /**
     * 删除集合对象
     *
     * @param collection 多个对象
     * @return true=删除成功,false=删除失败
     */
    public long deleteObject(final Collection collection) {
        return redisTemplate.delete(collection);
    }

    /**
     * 缓存List数据
     *
     * @param key 缓存的键
     * @param dataList 待缓存的List数据
     * @return 缓存的对象
     */
    public <T> long setCacheList(final String key, final List<T> dataList) {
        Long count = redisTemplate.opsForList().rightPushAll(key, dataList);
        return count == null ? 0 : count;
    }

    /**
     * 获得缓存的List对象
     *
     * @param key 缓存的键
     * @return 缓存的键对应的数据
     */
    public <T> List<T> getCacheList(final String key) {
        return redisTemplate.opsForList().range(key, 0, -1);
    }

    /**
     * 缓存Set
     *
     * @param key 缓存键
     * @param dataSet 缓存的数据
     * @return 缓存数据的对象
     */
    public <T> BoundSetOperations<String, T> setCacheSet(final String key, final Set<T> dataSet) {
        BoundSetOperations<String, T> setOperation = redisTemplate.boundSetOps(key);
        Iterator<T> it = dataSet.iterator();
        while (it.hasNext()) {
            setOperation.add(it.next());
        }
        return setOperation;
    }

    /**
     * 获得缓存的Set
     *
     * @param key 缓存的键
     * @return 缓存数据的对象
     */
    public <T> Set<T> getCacheSet(final String key) {
        return redisTemplate.opsForSet().members(key);
    }

    /**
     * 缓存Map
     *
     * @param key 缓存的键
     * @param dataMap 缓存数据的对象
     */
    public <T> void setCacheMap(final String key, final Map<String, T> dataMap) {
        if (dataMap != null) {
            redisTemplate.opsForHash().putAll(key, dataMap);
        }
    }

    /**
     * 获得缓存的Map
     *
     * @param key 缓存键
     * @return 缓存数据的对象
     */
    public <T> Map<String, T> getCacheMap(final String key) {
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * 缓存Hash
     *
     * @param key Redis键
     * @param hKey Hash键
     * @param value Hash中的值
     */
    public <T> void setCacheMapValue(final String key, final String hKey, final T value) {
        redisTemplate.opsForHash().put(key, hKey, value);
    }

    /**
     * 获取Hash中的数据
     *
     * @param key Redis键
     * @param hKey Hash键
     * @return Hash中的对象
     */
    public <T> T getCacheMapValue(final String key, final String hKey) {
        HashOperations<String, String, T> opsForHash = redisTemplate.opsForHash();
        return opsForHash.get(key, hKey);
    }

    /**
     * 删除Hash中的数据
     *
     * @param key Redis键
     * @param hkey Hash键
     */
    public void delCacheMapValue(final String key, final String hkey) {
        HashOperations hashOperations = redisTemplate.opsForHash();
        hashOperations.delete(key, hkey);
    }

    /**
     * 获取多个Hash中的数据
     *
     * @param key Redis键
     * @param hKeys Hash键集合
     * @return Hash对象集合
     */
    public <T> List<T> getMultiCacheMapValue(final String key, final Collection<Object> hKeys) {
        return redisTemplate.opsForHash().multiGet(key, hKeys);
    }

    /**
     * 获得缓存的基本对象列表
     *
     * @param pattern 字符串前缀
     * @return 对象列表
     */
    public Collection<String> keys(final String pattern) {
        return redisTemplate.keys(pattern);
    }

}
```

### JwtUtil

```java
public class JwtUtil {

    //有效期为 60 * 60 * 1000 一个小时
    public static final Long JWT_TTL = 60 * 60 * 1000L;

    //设置秘钥明文
    public static final String JWT_KEY = "cyou";

    //设置签发者
    public static final String ISSUER = "cyou";

    /**
     * 生成UUID
     */
    public static String getUUID() {
        return UUID.randomUUID().toString().replaceAll("-", "");
    }

    /**
     * 生成加密后的秘钥 SecretKey
     */
    private static SecretKey generalKey() {
        byte[] encodedKey = Base64.getDecoder().decode(JwtUtil.JWT_KEY);
        return new SecretKeySpec(encodedKey, 0, encodedKey.length, "AES");
    }

    /**
     * 创建JWT
     *
     * @param subject 存放的数据(JSON格式)
     */
    public static String createJWT(String subject) {
        JwtBuilder builder = getJwtBuilder(subject, null, getUUID());
        return builder.compact();
    }

    /**
     * 创建JWT
     *
     * @param subject 存放的数据(JSON格式)
     * @param ttlMillis JWT超时时间
     */
    public static String createJWT(String subject, Long ttlMillis) {
        JwtBuilder builder = getJwtBuilder(subject, ttlMillis, getUUID());
        return builder.compact();
    }

    /**
     * 创建JWT
     *
     * @param id JWT唯一标识
     * @param subject 存放的数据(JSON格式)
     * @param ttlMillis JWT超时时间
     */
    public static String createJWT(String id, String subject, Long ttlMillis) {
        JwtBuilder builder = getJwtBuilder(subject, ttlMillis, id);
        return builder.compact();
    }

    /**
     * 生成JWT并构建JWT对象
     *
     * @param subject 存放的数据(JSON格式)
     * @param ttlMillis JWT超时时间
     * @param uuid 使用UUID来确保每个JWT都有唯一的标识
     */
    private static JwtBuilder getJwtBuilder(String subject, Long ttlMillis, String uuid) {
        //使用HS256算法进行签名
        SignatureAlgorithm signatureAlgorithm = SignatureAlgorithm.HS256;
        //获取用于签名的秘钥
        SecretKey secretKey = generalKey();
        //获取当前时间的毫秒数
        long nowMillis = System.currentTimeMillis();
        //构建一个Date对象,表示当前时间
        Date now = new Date(nowMillis);
        //如果传入的过期时间为NULL,则使用默认的JWT_TTL(1小时)
        if (ttlMillis == null) {
            ttlMillis = JwtUtil.JWT_TTL;
        }
        //计算Token过期时间的毫秒数
        long expMillis = nowMillis + ttlMillis;
        //构建一个Date对象,表示Token过期的时间
        Date expDate = new Date(expMillis);
        //使用Jwts.builder()来构建JWT
        return Jwts.builder()
                //设置唯一的ID,通常可以使用UUID
                .setId(uuid)
                //设置JWT的主体,即要存放的数据,通常为JSON格式数据
                .setSubject(subject)
                //设置JWT的签发者
                .setIssuer(ISSUER)
                //设置JWT的签发时间
                .setIssuedAt(now)
                //使用HS256对称加密算法签名,第二个参数为秘钥
                .signWith(signatureAlgorithm, secretKey)
                //设置JWT的过期时间
                .setExpiration(expDate);
    }

    /**
     * 解析JWT令牌,并提取其中的Claims对象
     *
     * @param jwt JWT令牌
     */
    public static Claims parseJWT(String jwt) throws Exception {
        SecretKey secretKey = generalKey();
        return Jwts.parser()
                .setSigningKey(secretKey)
                .parseClaimsJws(jwt)
                .getBody();
    }

}
```

### WebUtil

```java
public class WebUtil {

    /**
     * 将字符串渲染到客户端
     *
     * @param response
     * @param string
     * @author Cyou
     */
    public static void renderString(HttpServletResponse response, String string) {
        try {
            response.setStatus(200);
            response.setContentType("application/json");
            response.setCharacterEncoding("utf-8");
            response.getWriter().print(string);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```
