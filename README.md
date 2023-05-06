# polymeric

#### 介绍
 polymeric 是一个实施分布式服务下跨库跨表的数据聚合实现，使用声明式配置将需要跨库跨表的字段，自动匹配远程数据源，规避良莠不一的聚合方式，降低代码量，polymeric结合mybatis查询结果的前置下，实现远程数据查询并反写到 mybatis 的查询结果中， 提供自由实现分页没有任何侵入，实现了轻松跨库，跨表，代码干净清爽并存的解决方案,也是微服务设计后各个微服务实现独立自治数据反写的优选方案。



#### 使用说明

1.  引入 polymeric-spring-boot-starter

```
    <dependency>
            <groupId>com.cdg.polymeric</groupId>
            <artifactId>polymeric-spring-boot-starter</artifactId>
            <version>2.0.0-SNAPSHOT</version>
    </dependency>
```

2.  在spring boot 工程中 根据自己实际需要增加缓存配置

```
    ploymeric:
      enabled: true #默认开启
      guavaCacheNumMaxSize: 1000 #guava缓存的键值数
      guavaCacheRefreshWriteTime: 10 #guava更新缓存的下一次时间,分钟
      guavaCacheRefreshThreadPoolSize: 10 #guava刷新线程池线程数量
```

     
3.  在需要跨库跨表的字段或者vo上增加声明式注解配置，比如案例如下

##### 第一种使用方式：
     
    聚合服务方： 

```java
    @Polymeric(key = "id",args = Long.class,feign = BasicDataFeignClientService.class,method = "getCustomerNameById")
    public class CustomerVo {

      private Long id;

      @PolymericValue(name = "customerName",enableCache = true)
      private String customerName;

      private String address;


      private String phone;
}
```

    远程数据源：

```java
    @GetMapping(value = "/getCustomerNameById")
    @ApiOperation(value = "测试聚合组件")
    public Map getCustomerNameById(@RequestParam(name = "id") Long id) {
        Map result = new HashMap();
        result.put("customerName","wyp");
        return result;
    }
```





```
@Polymeric
key： 远程数据源需要查询的参数
args： 远程数据源的参数类型
feign：支持openfeign 的feign客户端
method： feign客户端下调用的方法名
@PolymericValue
name:远程数据源需要聚合的字段名称
enableCache： 是否开启缓存
```

注：这里是调用一次远程数据源反写多个字段，比较适合一个vo中多个字段需要跨库跨表查询的场景，当所有字段都开启了缓存，才会走一级缓存，否则至多查询一次远程数据源。

##### 第二种使用方式：

    聚合服务方：
    
```java
public class CustomerVo {
        
        private Long id;
        
        @PolymericFiled(key = "id",args = Long.class,feign = BasicDataFeignClientService.class,method = "getCustomerNameById",enableCache = true)
        private String customerName;
        
        private String address;
        
        
        private String phone;
        
        
      }

```

也可以将查询配置在字段上，此场景比较适合vo至多有一个需要查询的字段，否在推荐使用 @Polymeric

```
@PolymericFiled
key： 远程数据源需要查询的参数
args： 远程数据源的参数类型
feign：支持openfeign 的feign客户端
method： feign客户端下调用的方法名
```


#### 参与贡献

1.  Fork 本仓库
2.  新建 Feat_dev 分支
3.  提交代码
4.  新建 Pull Request

#### spring boot 的支持
对spring boot 的支持见 https://gitee.com/youpiao_admin/polymeric-spring-boot

欢迎加qq群学习交流

![输入图片说明](polymeric%E5%AD%A6%E4%B9%A0%E4%BA%A4%E6%B5%81%E7%BE%A4%E7%BE%A4%E4%BA%8C%E7%BB%B4%E7%A0%81.png)
