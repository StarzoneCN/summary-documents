# <div style="text-align:center;color:#FF9900">Apollo</div> 

这是一篇配置中心工具`Apollo`的一篇知识点记录文章，参考[github][Apollo]中的指导文档；此文章不再赘述太多，只记录难点、易遗忘点；



### AppId配置

1. `classpath:/META-INF/app.properties `配置文件中配置
2. 系统属性（`System Property `）中配置
   - `-Dapp.id=YOUR-APP-ID`

### Environment

##### **配置方式**

1.  **Java System Property** 
   - 可以通过Java的System Property `env`来指定环境
   - 在Java程序启动脚本中，可以指定`-Denv=YOUR-ENVIRONMENT` 
     - 如果是运行jar文件，需要注意格式是`java -Denv=YOUR-ENVIRONMENT -jar xxx.jar`
   - 注意key为全小写
2.  **System Environment** 
   - 还可以通过操作系统的System Environment `ENV`来指定
   - 注意key为全大写
3.  **config file**
   - 最后一个推荐的方式是通过配置文件来指定`env=YOUR-ENVIRONMENT`
     - 对于Mac/Linux，文件位置为`/opt/settings/server.properties`
     - 对于Windows，文件位置为`C:\opt\settings\server.properties`



##### 目前Environment支持的值（大小写不敏感）

* **DEV** (Development environment)、**FAT** (Feature Acceptance Test environment)、**UAT** (User Acceptance Test environment)、**PRO** (Production environment)



### 集群

##### 配置方式

1. `-Dapollo.cluster=SomeCluster` 
2. 配置文件
   - 对于Mac/Linux，文件位置为`/opt/settings/server.properties`
   - 对于Windows，文件位置为`C:\opt\settings\server.properties`

##### 集群配置优先级

*  `apollo.cluster`  >  `IDC`   >   `default`

### 本地缓存

##### 缓存路径

* **Mac/Linux**: `/opt/data/{*appId*}/config-cache`
* **Windows**: `C:\opt\data\{*appId*}\config-cache`

##### 文件命名格式

* `{appId}+{cluster}+{namespace}.properties` 
  * `namespace`就是应用使用的配置`namespace`，一般是`application`  









[Apollo]:https://github.com/ctripcorp/apollo	"Apollo"