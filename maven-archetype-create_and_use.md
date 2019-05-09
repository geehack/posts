## 创建模板项目

在模板项目的`pom.xml`中加入下面部分

``` xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-archetype-plugin</artifactId>
    <version>3.0.1</version>
</plugin>
```


注意：模板项目的`archeId`不要带archetype后缀，生成模板的时候会自动在模板名字后添加`-archetype`后缀

### 从模板生成
在模板项目的`pom.xml`所在目录下执行mvn命令：`mvn archetype:create-from-project`。

这样就根据当前项目创建了`archetype`。

- 新生成的`archetype`项目，保存在作为模板的源项目的`target/generated-sources/archetype`目录中。
- `target/generated-sources/archetype/src/main/resources/archetype-resources`目录存放了项目模板。
- `target/generated-sources/archetype/src/main/resources/META-INF/maven/archetype-metadata.xml`保存了模板文件列表。




## 处理生成的Archetype

### 修改archetype的pom.xml

路径: `target/generated-sources/archetype/pom.xml` , 是archetype的pom.xml，不是模板项目的pom.xml

需要将pom.xml的tab替换为空格，并加入下面两部分

``` xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
</properties>

<!-- archetype release repo -->
<distributionManagement>
    <repository>
        <id>huwo-local-releases</id>
        <name>Local Releases</name>
        <url>http://artifactory.local/artifactory/list/libs-release-local/</url>
    </repository>
    <snapshotRepository>
        <id>huwo-local-snapshots</id>
        <name>Local Releases</name>
        <url>http://artifactory.local/artifactory/list/libs-snapshot-local/</url>
    </snapshotRepository>
</distributionManagement>
```

修改下面的相关描述，连接，开发者信息


### 处理无关文件
生成`archetype`的时候，项目中的无用文件并不会被清除，比如`.idea` `*.iml` `.settings.xml` 等等。

清理方法：
- 步骤一：这些文件需要手动删除
- 步骤二：编辑`target/generated-sources/archetype/src/main/resources/META-INF/maven/archetype-metadata.xml`文件，删除里面和之前删除的无用文件对应的项目并保存。


### 修改相关文件变量替换

1. 进入`target/generated-sources/archetype/src/main/resources/archetype-resources`目录下，手工编辑里面相关源代码，如果有些要进行变量替换的文件，就加入相关变量，如`${groupid}` `${artifactid}` `${package}`
2. 编辑`target/generated-sources/archetype/src/main/resources/META-INF/maven/archetype-metadata.xml`文件，把需要变量替换的文件项目开启`filtered=“true”`

此时，根据当前`archetype`创建项目过程中，这些变量会被替换掉。



## 新Archetype部署


### 部署到本地仓库
在新的`archetype`项目根目录下（target/generated-sources/archetype/), 执行命令：`mvn clean install`，部署到本地仓库

### 部署到私服
执行命令：`mvn clean deploy`



## 根据新archetype创建项目

### 修改settings.xml

#### 在settings.xml中添加profile
因为archetype插件在3.x之后是不能使用-DarchetypeRepository来指定库的，所以必须通过配置profile来实现

``` xml
<profiles>
    <profile>
      <id>archetype</id>
      <repositories>
        <repository>
          <id>archetype</id>
          <url>http://artifactory.local/artifactory/libs-snapshot-local</url>
          <releases>
            <enabled>true</enabled>
            <checksumPolicy>fail</checksumPolicy>
          </releases>
          <snapshots>
            <enabled>true</enabled>
            <checksumPolicy>warn</checksumPolicy>
          </snapshots>
        </repository>
      </repositories>
    </profile>
  </profiles>
```


如果在settings.xml中配置了mirror段，需要修改mirror设定，在mirror下的mirrorOf下加入 `!archetype`来表示排除profile中定义的repository

``` xml
<mirrorOf>*,!archetype</mirrorOf>
```

注意：此处排除的repository要和profile中定义的repository的id一致


### 创建命令

``` bash
mvn archetype:generate -B \
 -DarchetypeCatalog=internal \
 -DarchetypeGroupId=com.huwo.archetypes \
 -DarchetypeArtifactId=microservice-server-archetype \
 -DarchetypeVersion=0.0.6-SNAPSHOT \
 -DgroupId=com.huwo.demo.archetype \
 -DartifactId=test1 \
 -Dversion=1.0.0-SNAPSHOT A \
 -P archetype-create \
 -X
```
