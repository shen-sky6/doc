
### LocalDateTime转Long
```java
LocalDateTime dateTime = LocalDateTime.now();

// 转毫秒时间戳
long millis = dateTime.atZone(ZoneId.systemDefault()).toInstant().toEpochMilli();

// 转秒时间戳
long seconds = dateTime.atZone(ZoneId.systemDefault()).toInstant().getEpochSecond();

```


### 1.`@DateTimeFormat`和`@JsonFormat`的区别

| 注解                | 使用场景            | 影响                     |
| ----------------- | --------------- | ---------------------- |
| `@JsonFormat`     | JSON 序列化/反序列化   | 前后端 JSON 数据交互格式        |
| `@DateTimeFormat` | Spring MVC 参数绑定 | 表单参数、URL 参数转 Java 时间对象 |
```java
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
private LocalDateTime start;

@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private LocalDateTime start;
```

👉 所以如果你是 **前后端 JSON 请求/响应**，就用 `@JsonFormat`。  
👉 如果是 **URL query 参数** 或 **表单参数**（`application/x-www-form-urlencoded`），要用 `@DateTimeFormat`。  
👉 **两个注解经常一起用，避免不同场景出问题。**



