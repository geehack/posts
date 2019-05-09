## gRPC工程创建指南-Java版

### 框架与工具准备

- Java ` JDK8` 
- spring boot `v2.0.0+` 
- gRPC `v1.8+` 
- grpc-springboot-starter 2.2.0
- IDE推荐`Intellij IDEA` ，`Eclipse`暂未测试
- 依赖管理：`maven` ，未来将支持`gradle`



### 基本要求

- 文件编码 `UTF-8`

- 换行模式 `LF` ，注意：在windows下开发的同学，需要将IDE的换行模式修改为`LF`

- 截止2018.4，最佳组合版本为 grpc-springboot-starter v2.2.0 + protobuf v3.5.1 + grpc v1.10.1

  ​


### 创建项目框架




#### 步骤1: 创建项目框架

- 用`Intellij IDEA` 新建项目，并使用`Spring Initializr`向导


- 创建时如果遇到spring向导打开缓慢可以总自定义向导`http://start.spring.io` ，也可以通过访问web使用此向导
- spring项目基本信息按照实际工程需要填写，依赖管理选择`maven` （以后会添加gradle支持）
- 依赖组件，按照需要添加
- 最后确认工程名字和存储路径后，完成项目生成





#### 步骤2: 编辑POM文件

添加`properties`属性

```xml
<properties>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
	<java.version>1.8</java.version>
    <grpc.version>1.10.1</grpc.version><!-- CURRENT_GRPC_VERSION -->
</properties>
```



在依赖部分添加

```xml
<!-- 增加gRPC starter -->
<dependency>
    <groupId>org.lognet</groupId>
    <artifactId>grpc-spring-boot-starter</artifactId>
    <version>2.2.0</version>
</dependency>
```



添加构建段

```xml
<build>
    <extensions>
      <extension>
        <groupId>kr.motd.maven</groupId>
        <artifactId>os-maven-plugin</artifactId>
        <version>1.5.0.Final</version>
      </extension>
    </extensions>
    <plugins>
      <plugin>
        <groupId>org.xolstice.maven.plugins</groupId>
        <artifactId>protobuf-maven-plugin</artifactId>
        <version>0.5.1</version>
        <configuration>
          <protocArtifact>com.google.protobuf:protoc:3.5.1:exe:${os.detected.classifier}</protocArtifact>
          <pluginId>grpc-java</pluginId>
          <pluginArtifact>io.grpc:protoc-gen-grpc-java:${grpc.version}:exe:${os.detected.classifier}</pluginArtifact>
        </configuration>
        <executions>
          <execution>
            <goals>
              <goal>compile</goal>
              <goal>compile-custom</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```



最后，导入pom更改，完成。



### 创建服务配置文件



#### proto文件设置语法规范与约定

- 必须使用`protocal buffer 3`
- `java_multiple_files`选项必须为false
- `java_outer_classname`  选项必须以`Proto`作为后缀，如：`HelloWorldProto`
- 必须定义`java_package` 为`com.huwo.grpc.<项目标识>.gen`
- 请求消息命名必须以`Request`作为后缀
- 请求返回值必须以`Reply`作为后缀



#### 创建proto文件

在项目的`src/main`目录下创建`proto`目录（和`java` `resources` 两个目录平级）

在`proto`目录下创建`.proto`文件



**例子：helloworld文件**

```protobuf
syntax = "proto3";

option java_multiple_files = false;
option java_package = "com.huwo.grpc.helloworld.gen"; // 包名字
option java_outer_classname = "HelloWorldProto"; // Proto实体类名
option objc_class_prefix = "HW";

package helloworld;// 定义java_package后，此处被忽略

// 定义一个服务.
service Greeter {
  // 一个普通请求
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// 请求消息.
message HelloRequest {
  string name = 1;
}

// 返回消息
message HelloReply {
  string message = 1;
}
```



#### 编译生成gRPC代码

在ide中，在maven视图中，执行`complile`任务。（注意：使用mvn命令执行compile任务需要手动复制生成的文件到代码目录，不推荐）

查看`target/generated-sources/protobuf/` 目录下有两个目录`grpc-java`和`java`，分别生成了约定的包及文件



- `grpc-java`目录下生成的是`gRPC` 的抽象类, 包括客户端和服务端相关工具类
- `java`目录下生成的是`Protobuf`的服务实体文件




### 编写服务

1. 在项目包的`Application`目录下建立`api`包，下面建立`gRPC服务类`，命名规则为`<服务名称>Service`(如`HelloworldService`)
2. 在类声明上添加注解`@GRpcService`
3. 这个服务类需要`extend`之前自动生成的gRPC基类，一般为`<服务名称>Grpc.<服务名称>ImplBase`
4. 重写服务中的调用方法



一个服务例子：

```java
package com.huwo.grpcdemo.api;

import com.huwo.grpc.helloworld.GreeterGrpc;
import com.huwo.grpc.helloworld.HelloWorldProto;
import io.grpc.stub.StreamObserver;
import org.lognet.springboot.grpc.GRpcService;


@GRpcService
public class HelloworldService extends GreeterGrpc.GreeterImplBase {

    @Override
    public void sayHello(HelloWorldProto.HelloRequest request,
                         StreamObserver<HelloWorldProto.HelloReply> responseObserver){

        String msg = "Hello:" + request.getName();

        final HelloWorldProto.HelloReply.Builder replyBuilder = HelloWorldProto.HelloReply.newBuilder().setMessage(msg);

        responseObserver.onNext(replyBuilder.build());
        responseObserver.onCompleted();

        System.out.println("Returning " +msg);
    }
}
```



至此，一个服务基本框架搭建完毕。



#### 运行服务

点击IDE的运行按钮，启动springboot，服务启动完成。

另外，也可以把springboot应用打成jar，然后通过java命令手动运行:`java -jar <项目名称>.jar`



默认，grpc-springboot-starter 开放的gRPC服务端口为6565，可以通过`grpc.port`配置来更改端口



> 注意：运行服务之前一定要先执行maven的compile任务，以生成grpc相关代码



### 编写基于springboot的gRPC服务调用代码



```java
final HelloWorldProto.HelloRequest.Builder requestBuilder = HelloWorldProto.HelloRequest.newBuilder().setName("bitman");//.setMessage("bitman");;

final ManagedChannel channel = ManagedChannelBuilder.forAddress("127.0.0.1", 6565)
            .usePlaintext(true)
            .build();
final GreeterGrpc.GreeterFutureStub stub = GreeterGrpc.newFutureStub(channel);

stub.sayHello(requestBuilder.build());
```















