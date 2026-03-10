## [Apache RocketMQ](https://github.com/apache/rocketmq) Dashboard
[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)
[![CodeCov](https://codecov.io/gh/apache/rocketmq-dashboard/branch/master/graph/badge.svg)](https://codecov.io/gh/apache/rocketmq-dashboard)
[![Average time to resolve an issue](http://isitmaintained.com/badge/resolution/apache/rocketmq-dashboard.svg)](http://isitmaintained.com/project/apache/rocketmq-dashboard "Average time to resolve an issue")
[![Percentage of issues still open](http://isitmaintained.com/badge/open/apache/rocketmq-dashboard.svg)](http://isitmaintained.com/project/apache/rocketmq-dashboard "Percentage of issues still open")
[![Twitter Follow](https://img.shields.io/twitter/follow/ApacheRocketMQ?style=social)](https://twitter.com/intent/follow?screen_name=ApacheRocketMQ)
## 快速开始
RocketMQ-Dashboard V1.0.1 版本
当前配置: 关闭写入日志文件, 统计驾驶的error错误改为warn警告(如不需要可改成debug),前端默认展示中文

### 使用 Docker 安装部署

* 打包镜像
  国内可用: `Dockerfile`
```yaml
FROM swr.cn-north-4.myhuaweicloud.com/ddn-k8s/docker.io/eclipse-temurin:8-jdk-alpine
VOLUME /tmp
ADD rocketmq-dashboard-*.jar rocketmq-dashboard.jar
RUN sh -c 'touch /rocketmq-dashboard.jar'
ENV JAVA_OPTS=""
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -jar /rocketmq-dashboard.jar" ]
```
```bash
# 使用 Maven 打包并使用 Docker 构建镜像
mvn clean package -Dmaven.test.skip=true docker:build

# 打包Jar后,再使用Docker 构建镜像
docker build -t rocketmq-dashboard:latest .

# 或者直接拉取镜像使用(最新可用为2.0.0)
docker pull rocketmq-dashboard:latest
```
* 运行（请自行修改 namesrvAddr 和端口）

```bash
docker run -e "-Dserver.port=8080 -Drocketmq.config.namesrv_addr=172.16.16.164:30008;172.16.16.165:30008;172.16.16.166:30008 -Drocketmq.config.accessKey=rmqadmin -Drocketmq.config.secretKey=8oBB@6uTWG5nc -Drocketmq.config.loginRequired=true" -p 8080:8080 -t rocketmq-dashboard:latest
```
```yaml
version: '3'
services:
  rocketmq-console:
    image: rocketmq-dashboard:latest
    ports:
      - "8080:8080"
    environment:
      - JAVA_OPTS=-Xms1g -Xmx1g -Drocketmq.config.namesrv_addr=172.16.16.164:30008;172.16.16.165:30008;172.16.16.166:30008 -Drocketmq.config.accessKey=rmqadmin -Drocketmq.config.secretKey=8oBB@6uTWG5nc -Drocketmq.config.loginRequired=true
```

### 直接使用 Java 运行或部署
JDK 要求 java 1.8+
```bash
# 启动运行
mvn spring-boot:run
# 编译后运行Jar包
mvn clean package -Dmaven.test.skip=true
# 运行Jar包
java -jar target/rocketmq-dashboard-1.0.1.jar
```

#### 提示
* 如果下载包速度慢，可以更改 Maven 镜像（Maven 的 settings.xml）

  ```
  <mirrors>
      <mirror>
            <id>alimaven</id>
            <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>        
      </mirror>
  </mirrors>
  ```
* JAVA_OPTS 参数配置

  | 参数 | 说明 |
  | :--- | :--- |
  | `-Dserver.port` | 指定控制台 Web 服务监听的端口，默认为 `8080`。 |
  | `-Drocketmq.config.namesrvAddrs` | 指定 NameServer 地址列表，多个地址用分号 `;` 分隔。 |
  | `-Drocketmq.config.accessKey` | 访问控制所需的 Access Key。 |
  | `-Drocketmq.config.secretKey` | 访问控制所需的 Secret Key。 |
  | `-Drocketmq.config.loginRequired` | 是否需要登录才能访问控制台。 |
  | `-Drocketmq.client.logLevel` | 设置客户端日志级别，例如 `OFF` 表示关闭。 |


* 如果您使用的 RocketMQ 版本 < 3.5.8，请在启动 rocketmq-console-ng 时添加 -Dcom.rocketmq.sendMessageWithVIPChannel=false（或者您可以在运维页面中更改）
* 在 resource/application.yaml 中更改 rocketmq.config.namesrvAddr（或者您可以在运维页面中更改）
