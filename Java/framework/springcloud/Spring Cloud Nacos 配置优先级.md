## Spring Cloud Nacos 配置优先级

**最常用的情况：**

​		`远程共享(servername-shared.yml) > 远程约定(servername-*-dev.yml) > 本地bootstrap.yml > 本地application.yml` 

---

**较为复杂的情况：**

当同时存在代码、nacos和jar包外挂配置时，优先级从高到底是

`jar包外挂配置 > nacos > 代码`

归纳一下，按优先级从高到低排序：

1. jar运行命令中的spring.config.location
2. jar运行命令中的spring.profiles.active
3. jar运行命令中的spring.config.additional-location
4. nacos中带profile的配置（当有spring.profiles.active时）
5. nacos中spring.profiles.include的配置（当有spring.profiles.include时）
6. nacos中不带profile的配置
7. nacos中的ext配置
8. nacos中shared配置
9. 代码根目录/config/（此配置不会打包打jar）
10. 代码根目录（此配置不会打包打jar）
11. 代码classpath/config（即resouces/config)
12. 代码classpath根目录

使用spring.config.location时，其它位置的配置全部不生效。

不使用spring.config.location时，配置优先级从高到低进行merge。

**注意：**

​		如果配置文件中出现了数组，那么数组下标越大的优先级越高。

---

假设项目中有如下配置：

bootstrap.yaml

```yaml
spring:
  profiles:
    include:
      - include
    active:
      - dev
      - dev2
```


bootstrap-dev.yaml

```yaml
spring:
  cloud:
    nacos:
      config:
        server-addr: 127.0.0.1:8848
        namespace: test
        file-extension: yaml
        username: nacos
        password: nacos
        group: DEFAULT_GROUP
        prefix: springcloud_app_config_learning
        shared-configs:

   - dataId: springcloud_app_config_learning-shared.yaml
     group: group-shared
             extension-configs:
        - dataId: springcloud_app_config_learning-ext.yaml
          group: group-ext
```

​		项目启动的时候则会在nacos找如下文件（找不到不会报错）。由于nacos需要指定文件后缀，因此不存在properties与yaml优先级的区别，但在有spring.profile.active的情况下，带profile的文件优先级一样高于不带profile后缀，非共享非扩展配置比共享扩展配置优先级高，扩展配置比共享配置优先级高。总的来讲，优先级从高到低排序为：

| 优先级 | 文件名                                      | group         |
| :----: | :------------------------------------------ | ------------- |
|   1    | springcloud_app_config_learning-dev2.yml    | DEFAULT_GROUP |
|   2    | springcloud_app_config_learning-dev.yml     | DEFAULT_GROUP |
|   3    | springcloud_app_config_learning-include.yml | DEFAULT_GROUP |
|   4    | springcloud_app_config_learning.yml         | DEFAULT_GROUP |
|   5    | springcloud_app_config_learning             | DEFAULT_GROUP |
|   6    | springcloud_app_config_learning-ext.yml     | group-ext     |
|   7    | springcloud_app_config_learning-shared.yml  | group-shared  |

### 