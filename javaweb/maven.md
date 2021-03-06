# Maven

* Apache Maven基于项目对象模型(POM)的概念
* Maven项目的结构和内容在一个名为`pom.xml`的XML文件中声明
* 作用:
    - 依赖管理
    - 项目构建(清理, 编译, 测试, 报告, 打包, 部署)

## 安装配置

* 官网[https://maven.apache.org/](https://maven.apache.org/)
* 安装步骤
    - 手动方式
        - 确认安装了JDK 1.7或以上, 并添加到环境变量中
        - 下载压缩包
        - 解压压缩包到任意目录
            - `unzip apache-maven-3.3.9-bin.zip`或`tar xzvf apache-maven-3.3.9-bin.tar.gz`
        - 添加maven的`bin/`目录到环境变量
            - `export PATH="/xxx/apache-maven-3.3.9/bin:$PATH"`
            - `source ./bash_profile`
        - 验证
            - `mvn -v`
        - 配置
            - `maven/conf/settings.xml`
            - `$UserHome/.m2/`也行, 可以实现maven升级同时配置不丢失
    - homebrew方式
        - `brew install maven`
        - 验证
            - `mvn -v`
        - 配置
            - `vim ~/.m2/settings.xml`


## maven仓库

* 3种仓库
    - 本地仓库(local): 本地电脑的仓库
    - 中央仓库(central): 官方maven仓库
    - 远程仓库(remote): 非官方的maven仓库, 有java.net, 开源中国maven或自建
* 当添加依赖时, 仓库的搜索顺序为: `本地仓库 > 中央仓库 > 远程仓库`

### 本地仓库

* 用于存储所有项目的依赖关系到本地文件夹
* 默认情况下, 本地仓库默认为`.m2`目录
    - Uxin/OSX: `~/.m2`
    - Windows: `C:\Documents and Settings\{your-username}\.m2`
* 修改默认仓库为其他目录
    - 编辑` {M2_HOME}\conf\setting.xml`文件
    - 修改`<localRepository></localRepository>`的值为其他目录
    - 执行`mvn archetype:generate -DgroupId=com.yiibai -DartifactId=NumberGenerator -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`保存

### 远程仓库

* 添加远程仓库: 在`pom.xml`文件中添加你需要的远程仓库

```xml
pom.xml
-------
<project ...>
    <repositories>
        <!-- 添加远程仓库信息 -->
        <repository>
            <id>java.net</id>
            <url>https://maven.java.net/content/repositories/public/</url>
        </repository>
        <repository>
            <id>JBoss repository</id>
            <url>http://repository.jboss.org/nexus/content/groups/public/</url>
        </repository>
    </repositories>
</project>
```


## maven依赖机制

* 传统方式下我们需要自己下载jar包然后导入到项目, 每次版本更新我们都需要下载新版本的jar包重新导入
* maven方式下, 我们只需要知道依赖库的坐标, 然后会自动下载指定版本的依赖jar包, 如果不指定版本, 则下载仓库中最新版本的依赖

* 坐标

```xml
<groupId>log4j</groupId>
<artifactId>log4j</artifactId>
<version>1.2.14</version>
```

* 在`pom.xml`中添加依赖

```xml
<dependencies>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.14</version>
    </dependency>
</dependencies>
```


## maven项目目录结构

* web项目

```
project/
    |_ src/                       # 源码
        |_ main/                  # 项目代码
            |_ java/              # java代码
            |_ resource/          # 资源文件
            |_ webapp/            # 服务器代码
                |_ index.jsp      
                |_ WEB-INF/       
                    |_ web.xml     
        |_ test/                  # 测试代码
            |_ java/              # java代码
            |_ resource/          # 资源文件
    |_ target/                    # 编译生成的文件(mvn compile生成)
    |_ pom.xml                    # maven项目配置
```

* java项目

```
project/
    |_ src/                       # 源码
        |_ main/                  # 项目代码
            |_ java/              # java代码   
        |_ test/                  # 测试代码
            |_ java/              # java代码
    |_ target/                    # 编译生成的文件(mvn compile生成)
    |_ pom.xml                    # maven项目配置
```


## POM

* POM, Project Object Model, 对象项目模型, 是Maven的基本单位, 是一个XML文件, 保存在项目根目录下的`pom.xml`文件中
* 每个项目应该有一个单一的POM文件
* 所有POM文件项目元素必须有三个必填字段:
    - groupId: 项目所属的组织的名称
    - artifactId: 项目名
    - version: 项目版本号
* 配置包括:
    - 依赖
    - 插件
    - 目标
    - 构建配置
    - 项目版本
    - 开发人员
    - 邮件列表

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
    http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <!-- 必须的三要素 -->
    <groupId>com.companyname.project-group</groupId>
    <artifactId>project</artifactId>
    <version>1.0</version>

    <!-- 构建信息 -->
    <build>
        <sourceDirectory>C:\MVN\project\src\main\java</sourceDirectory>
        <scriptSourceDirectory>src/main/scripts</scriptSourceDirectory>
        <testSourceDirectory>C:\MVN\project\src\test\java</testSourceDirectory>
        <outputDirectory>C:\MVN\project\target\classes</outputDirectory>
        <testOutputDirectory>C:\MVN\project\target\test-classes</testOutputDirectory>
        <resources>
            <resource>
                <mergeId>resource-0</mergeId>
                <directory>C:\MVN\project\src\main\resources</directory>
            </resource>
        </resources>
        <testResources>
            <testResource>
                <mergeId>resource-1</mergeId>
                <directory>C:\MVN\project\src\test\resources</directory>
            </testResource>
        </testResources>
        <directory>C:\MVN\project\target</directory>
        <finalName>project-1.0</finalName>

        <!-- 插件管理 -->
        <pluginManagement>
            <plugins>
                <plugin>
                    <artifactId>maven-antrun-plugin</artifactId>
                    <version>1.3</version>
                </plugin>
                <plugin>
                    <artifactId>maven-assembly-plugin</artifactId>
                    <version>2.2-beta-2</version>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>

    <!-- 仓库管理 -->
    <repositories>
        <repository>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <id>central</id>
            <name>Maven Repository Switchboard</name>
            <url>http://repo1.maven.org/maven2</url>
        </repository>
    </repositories>

    <!-- 插件仓库 -->
    <pluginRepositories>
        <pluginRepository>
            <releases>
                <updatePolicy>never</updatePolicy>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <id>central</id>
            <name>Maven Plugin Repository</name>
            <url>http://repo1.maven.org/maven2</url>
        </pluginRepository>
    </pluginRepositories>

    <!-- 报告生成目录 -->
    <reporting>
        <outputDirectory>C:\MVN\project\target/site</outputDirectory>
    </reporting>
</project>
```


## maven生命周期

* 生命周期设置构建项目的执行顺序


* 典型的Maven生命周期:

|阶段    |处理    |描述                                    |
|--------|--------|----------------------------------------|
|准备资源|资源复制|资源复制可以进行定制                    |
|编译    |执行编译|源代码编译在此阶段完成                  |
|打包    |打包    |创建JAR/WAR包如在 pom.xml 中定义提及的包|
|安装    |安装    |这一阶段在本地/远程Maven仓库安装程序包  |

* clean生命周期(执行`mvn clean`)
    - `pre-clean`
    - `clean`
    - `post-clean`

* 默认的生命周期(23个阶段):

|生命周期阶段         |描述                                                     |
|---------------------|---------------------------------------------------------|
|validate             |验证项目是否正确，并且所有必要的信息可用于完成构建过程   |
|initialize           |建立初始化状态，例如设置属性                             |
|generate-sources     |产生任何的源代码包含在编译阶段                           |
|process-sources      |处理源代码，例如，过滤器值                               |
|generate-resources   |包含在包中产生的资源                                     |
|process-resources    |复制和处理资源到目标目录，准备打包阶段                   |
|compile              |编译该项目的源代码                                       |
|process-classes      |从编译生成的文件提交处理，例如：Java类的字节码增强/优化  |
|generate-test-sources|生成任何测试的源代码包含在编译阶段                       |
|process-test-sources |处理测试源代码，例如，过滤器任何值                       |
|test-compile         |编译测试源代码到测试目标目录                             |
|process-test-classes |处理测试代码文件编译生成的文件                           |
|test                 |运行测试使用合适的单元测试框架(JUnit)                    |
|prepare-package      |执行必要的任何操作的实际打包之前准备一个包               |
|package              |提取编译后的代码，并在其分发格式打包，如JAR，WAR或EAR文件|
|pre-integration-test |完成执行集成测试之前所需操作。例如，设置所需的环境       |
|integration-test     |处理并在必要时部署软件包到集成测试可以运行的环境         |
|pre-integration-test |完成集成测试已全部执行后所需操作。例如，清理环境         |
|verify               |运行任何检查，验证包是有效的，符合质量审核规定           |
|install              |将包安装到本地存储库，它可以用作当地其他项目的依赖       |
|deploy               |复制最终的包到远程仓库与其他开发者和项目共享             |


## Maven构建环境配置

* 用于针对不同的环境(开发/生产)来自定义构建
* 配置文件有3种:
    - 按项目配置: 定义在项目根目录的`pom.xml`
    - 按用户配置: 定义在用户的Maven配置文件中(`%USER_HOME%/.m2/settings.xml`)
    - 全局配置: 定义在全局的Maven配置文件中(`%M2_HOME%/conf/settings.xml`)


### 配置方式

1. 配置pom.xml的profile

```xml
<profiles>
    <profile>
        <!-- 本地开发环境 -->
        <id>dev</id>
        <properties>
            <profiles.active>dev</profiles.active>
        </properties>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
    <profile>
        <!-- 测试环境 -->
        <id>test</id>
        <properties>
            <profiles.active>test</profiles.active>
        </properties>
    </profile>
    <profile>
        <!-- 生产环境 -->
        <id>product</id>
        <properties>
            <profiles.active>product</profiles.active>
        </properties>
    </profile>
</profiles>
```

2. 创建配置文件
    - 针对不同环境创建不同的配置文件, 配置文件都作为资源文件放在`src/main/resources`目录下. 配置文件使用键值对编写一些变量的配置参数
        - `env-dev.properties`: 开发环境
        - `env-test.properties`: 测试环境
        - `env-product.properties`: 生产环境

3. 配置构建资源

```xml
<build>
　　<filters>
        <filter>${project.basedir}/src/main/resources/environment/env-${profiles.active}.properties</filter>
　　</filters>
　　<resources>
    　　<resource>
        　　<directory>src/main/resources</directory>
        　　<filtering>true</filtering>
    　　</resource>
　　</resources>
</build>
```

4. 执行构建

```shell
# mvn package -P${env}
mvn package -Pdev      # 开发环境
mvn package -Ptest     # 测试环境
mvn package -Pproduct  # 生产环境
```


## Maven插件

* Maven是一个执行插件的框架, 每个任务实际上是由插件完成的
* 插件通常用于:
    - 创建jar文件
    - 创建war文件
    - 编译代码文件
    - 执行代码单元测试
    - 创建项目文档
    - 创建项目报告
* 一个插件通常提供了一组目标
    - 执行语法: `mvn [plugin-name]:[goal-name]`
    - 例如编译命令: `mvn compiler:compile`
* 插件类型:
    - 构建插件: 在生成过程中执行, 并在`pom.xml`中的`<build/>`标签进行配置
    - 报告插件: 在网站生成期间执行, 在`pom.xml`中的`<reporting/>`标签进行配置
* 常见插件:
    - clean: 编译后的清理目标, 删除目标目录
    - compiler: 编译Java源文件
    - surefile: 运行JUnit单元测试, 创建测试报告
    - jar: 从当前项目构建JAR文件
    - war: 从当前项目构建WAR文件
    - javadoc: 生成该项目的文档
    - antrun: 从构建所述的任何阶段运行一组Ant任务

### 插件运行示例

* 首先创建`pom.xml`文件, 编写如下内容

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.companyname.projectgroup</groupId>
    <artifactId>project</artifactId>
    <version>1.0</version>

    <build>
        <plugins>
            <!-- maven 编译插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <!-- 配置JDK版本 -->
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- maven ant插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-antrun-plugin</artifactId>
                <version>1.1</version>
                <executions>
                    <execution>
                        <id>id.clean</id>
                        <phase>clean</phase>
                        <goals>
                            <goal>run</goal>
                        </goals>
                        <configuration>
                            <tasks>
                                <echo>clean phase</echo>
                            </tasks>
                        </configuration>
                    </execution>     
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

* 运行clean命令

```shell
# 执行clean命令
mvn clean
```

* 查看命令运行结果

```text
# 输出内容
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------
[INFO] Building Unnamed - com.companyname.projectgroup:project:jar:1.0
[INFO]    task-segment: [post-clean]
[INFO] ------------------------------------------------------------------
[INFO] [clean:clean {execution: default-clean}]
[INFO] [antrun:run {execution: id.clean}]
[INFO] Executing tasks
     [echo] clean phase
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------
[INFO] Total time: < 1 second
[INFO] Finished at: Sat Jul 07 13:38:59 IST 2012
[INFO] Final Memory: 4M/44M
[INFO] ------------------------------------------------------------------
```


## Maven脚手架 - archetype

* `archetype`插件是专门用来创建项目的脚手架
* maven提供了多种现成的脚手架, 用于创建不同的项目; 我们也可以自定义脚手架来创建自定义的项目结构
    - 现成的脚手架:
        - 普通Java项目(最终生成jar文件): `org.apache.maven.archetypes:maven-archetype-quickstart`
        - 项目站点: `org.apache.maven.archetypes:maven-archetype-site`
        - Web应用项目(最终生成war文件): `org.apache.maven.archetypes:maven-archetype-webapp`
        - 使用现成的脚手架:
            - 互动方式:
                - 项目根目录执行`mvn archetype:generate`, maven会联网搜索远程的模板并列出, 输入模板编号
                    - 由于模板列表较长, 从命令行中要找到需要的很困难, 你可以使用以下方式将列表重定向到文件中查看: `mvn archetype:generate > templates.txt`
                - maven会提示选择该模板的版本号, 输入版本号
                - maven会提示输入项目的详细信息
                - maven会让你确认项目信息
                - maven执行创建
            - 非互动方式(如果已经知道要创建什么模板, 则可以通过参数直接指定)
                - Web项目: `mvn archetype:generate -DgroupId={groupId} -DartifactId={projectName} -DarchetypeArtifactId=maven-archetype-webapp - DinteractiveMode=false`
                - 普通Java项目: `mvn archetype:generate -DgroupId={groupId} -DartifactId={projectName} -DarchetypeArtifactId=maven-archetype-quickstart - DinteractiveMode=false`
    - 自定义脚手架
        - 创建自定义脚手架的方式有2种
            - 从现有项目结构导出
                - 准备好项目结构
                - 在项目根目录运行: `mvn archetype:create-from-project`
                - 在项目目录下的`{project}/target/generated-sources/archetype/`目录下生成了`maven-metadata.xml`项目描述文件
            - 自定义archetype描述文件
        - 使用自定义脚手架:
            - 在脚手架所在目录执行: `mvn install`安装
            - 新建maven项目, 即可看到导入的脚手架


## Maven依赖管理

* `<dependencies>`: 用于声明依赖的配置
    - `<dependency>`: 用于添加一个单独的依赖
        - `<groupId>`: 依赖的组织名称
        - `<artifactId>`: 依赖的名称
        - `<version>`: 依赖的版本号
        - `<scope>`: 依赖的作用范围
            - `compile`: 默认值, 编译范围内有效, 编译和打包都使用, 适用于所有阶段
            - `provided`: 类似compile, 编译和测试有效, 打包不会使用. 期望JDK, 容器或使用者已经提供这个依赖, 比如打包上传到Tomcat中, Tomcat已经存在了Servlet.jar
            - `runtime`: 只在运行时使用, 编译时候不使用. 适用于运行和测试阶段
            - `test`: 只在测试时使用, 用于编译和运行测试代码, 编译和打包都不使用, 不会随项目发布
            - `system`: 类似provided, 需要显式提供(手动导入)包含依赖的jar, maven不会再仓库中寻找
            - `import`: 只能用于`<dependencyManagement>`标签中, 用于从其他pom中导入dependency的配置
        - `<systemPath><`: 依赖文件的所在路径, 一般配合`system`的scope使用
        - `<exclusions>`: 排除依赖, 用于避免多个依赖内部依赖的版本不同, 导致的依赖冲突问题
            - `<exclusion>`: 声明一个要被排除的依赖
                - `<groupId>`: 依赖的组织名称
                - `<artifactId>`: 依赖的名称
* `<dependencyManagement>`: 对依赖进行版本管理
    - 如果`<dependencies>`中的`<dependency>`没有声明`<version>`, 则会到`<dependencyManagement>`中寻找
    - 如果`<dependencies>`中的`<dependency>`已经声明`<version>`, 则会以`<dependency>`自己声明的版本为准


```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/maven-v4_0_0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.companyname.bank</groupId>
    <artifactId>consumerBanking</artifactId>
    <packaging>jar</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>consumerBanking</name>
    <url>http://maven.apache.org</url>

    <!-- 所有依赖 -->
    <dependencies>
        <!-- 单个依赖 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>

        <!-- 单个依赖 -->
        <dependency>
            <groupId>ldapjdk</groupId>
            <artifactId>ldapjdk</artifactId>
            <scope>system</scope>
            <version>1.0</version>
            <systemPath>${basedir}\src\lib\ldapjdk.jar</systemPath>
        </dependency>
    </dependencies>

</project>
```


## Maven快照(SNAPSHOT)

* SNAPSHOT指的是一个特殊的版本, 代表目前开发版本的副本.
* 快照不同于常规版本, maven每生成一个远程存储库都会检查新的快照版本
* 快照的作用:
    - 如果使用普通版本, 如`data-service:1.0`, 那么在本地已经下载了该依赖的情况下, 当远程仓库的1.0版本已经有了更新, 那么本地也不会再去下载新的依赖, 除非你提升版本号
    - 如果使用快照版本, 如`data-service:1.0-SNAPSHOT`, 则每次构建时都会自动获取最新的快照版本

* 定义项目为SNAPSHOT版本

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>data-service</groupId>
    <artifactId>data-service</artifactId>
    <version>1.0-SNAPSHOT</version>   <!-- 这里定义快照 -->
    <packaging>jar</packaging>
    <name>health</name>
    <url>http://maven.apache.org</url>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
</project>
```

* 添加快照版本依赖

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>app-ui</groupId>
   <artifactId>app-ui</artifactId>
   <version>1.0</version>
   <packaging>jar</packaging>
   <name>health</name>
   <url>http://maven.apache.org</url>
   <properties>
      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
   </properties>
   <dependencies>
      <dependency>
      <groupId>data-service</groupId>
         <artifactId>data-service</artifactId>
         <version>1.0-SNAPSHOT</version>  <!-- 添加了快照版本的依赖 -->
         <scope>test</scope>
      </dependency>
   </dependencies>
</project>
```


## maven命令

```shell
# 清理项目
mvn clean

# 编译项目
mvn compile

# 测试
mvn test

# 打包
mvn package

# 打jar包或war包或发布到本地仓库
mvn install
```

### Maven构建项目

* 命令: `mvn package`
* 配置生成的文件类型: 在`pom.xml`中的`<project></project>`标签内, 配置`<packaging></packaging>`标签, 值为`jar`则生成jar文件, 值为`war`则生成war文件

```xml
<!-- jar包 -->
<project...>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.yiibai</groupId>
    <artifactId>Maven Example</artifactId>
    <packaging>jar</packaging>
    ...
</project>

<!-- war包 -->
<project...>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.yiibai</groupId>
    <artifactId>Maven Example</artifactId>
    <packaging>war</packaging>
    ...
</project>
```


### Maven清理项目

* 命令: `mvn clean`. 执行后`target`目录会被删除
* maven构建后会生成很多文件在`target`目录中, 如果执行新的构建, 则需要先清理该目录中的缓存文件.
* 建议打包时使用: `mvn clean package`, 即先清理, 再打包, 保证数据都是最新的


### Maven单元测试

* 命令:
    - `mvn test`
    - 指定测试的类: `mvn -Dtest={类} test`


### Maven部署到本地资源库

* 命令: `mvn install`
* 建议与清理一起执行: `mvn clean install`


### 生成项目的文档站点

* 命令: `mvn site`
* 生成的站点文件会在`target/site`目录中
* 该站点文件是描述项目的一些信息, 类似与文档信息


### 将文档站点部署到服务器

* 命令: `mvn site:deploy`
* 使用WebDAV机制, 需要验证信息
* 部署的配置:
    - 注意`dav:`前缀要添加在HTTP协议前

```xml
pom.xml配置服务器信息
--------------------
<distributionManagement>
    <site>
        <id>yiibaiserver</id>
        <url>dav:http://127.0.0.1/sites/</url>
    </site>
</distributionManagement>

settings.xml配置WebDAV认证信息
-----------------------------
<servers>
	<server>
		<id>yiibaiserver</id>
		<username>admin</username>
		<password>123456</password>
	</server>
</servers>
```


## 使用Maven部署war文件到Tomcat

* 在Tomcat中添加用户

```xml
<?xml version='1.0' encoding='utf-8'?>
<tomcat-users>

	<role rolename="manager-gui"/>
	<role rolename="manager-script"/>
	<user username="admin" password="password" roles="manager-gui,manager-script" />

</tomcat-users>
```

* 在项目中创建配置文件, 设置认证信息

```xml
%MAVEN_PATH%/conf/settings.xml
------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<settings ...>
	<servers>

		<server>
			<id>TomcatServer</id>
			<username>admin</username>
			<password>password</password>
		</server>

	</servers>
</settings>
```

* 在`pom.xml`中添加Tomcat的maven插件

```xml
pom.xml
-------
<plugin>
    <groupId>org.apache.tomcat.maven</groupId>
    <artifactId>tomcat7-maven-plugin</artifactId>
    <version>2.2</version>
    <configuration>
        <url>http://localhost:8080/manager/text</url>
        <server>TomcatServer</server>
        <path>/yiibaiWebApp</path>
    </configuration>
</plugin>
```

* 执行远程部署命令

```shell
mvn tomcat7:deploy    # 首次部署时使用
mvn tomcat7:undeploy  # 取消部署
mvn tomcat7:redeploy  # 再次部署时使用
```


## maven项目模块化

* 模块的聚合
    - maven提供模块化开发方式, 可以将一个项目分为多个子maven项目, 再由一个专门负责聚合的maven项目来聚合这些子项目
    - 聚合模块的要求
        - 聚合模块必须也是一个maven项目, 但只有pom.xml, 没有src等目录
        - 打包方式为pom
        - 在聚合模块的pom.xml中加入`<modules>`和`<module>`标签来引入子模块. 每个module的值都是相对于当前pom.xml的相对路径, 子模块所处的目录应与artifactId一致
        - 聚合模块的版本要和子模块一致
        - 习惯上将聚合模块放在项目目录最顶层, 子模块作为子目录
* 模块的继承
    - 多个模块可能有共同的依赖, 可以采取模块继承的方式共用相同的依赖, 避免下载重复的依赖
    - 继承模块的要求
        - 在模块的pom.xml中添加`<parent>`标签引入父模块
* 聚合和继承
    - 聚合是依靠父模块来聚合多个子模块
    - 继承是将公共的配置放在父模块中, 供子模块使用
* 多模块拆分规则:
    - 按业务模块拆分: 即每个功能一个模块
    - 按层拆分: 即每个层一个模块
* IDE中创建maven多模块项目
    - IDEA
        - 先创建父项目: 创建一个maven项目, 不选archetype
        - 建好该项目后, 创建子项目:
            - 在该项目名上右键, 新建一个module, 选择maven, 选择archetype. 建好后父项目和子项目的pom.xml中会自动添加相关标签
            - 重复以上步骤创建其他子项目
        - 如需添加模块之间的依赖, 添加Module依赖即可
    - Eclipse
        - 先创建父项目: 创建一个Maven Project, 跳过archetype, 打包类型选pom
        - 建好该项目后, 创建子项目:
            - 创建一个Maven Module, 选择Parent Project为刚创建的父项目, 打包类型正常选(jar或war). 建好后副项目和子项目的pom.xml中会自动添加相关标签
            - 重复以上步骤创建其他子项目
        - 如需添加模块之间的依赖, 在项目右键, maven, Add dependency


* 子父模块pom.xml配置演示

```xml
<!-- 父模块的pom.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.lixplor</groupId>
    <artifactId>maven-module-demo</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>

    <!-- 声明子模块 -->
    <modules>
        <module>submodule1</module>
        <module>submodule2</module>
    </modules>

</project>
```

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

    <!-- 声明父模块 -->
    <parent>
        <artifactId>maven-module-demo</artifactId>
        <groupId>com.lixplor</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <modelVersion>4.0.0</modelVersion>
    <artifactId>sub-module-1</artifactId>
    <packaging>war</packaging>
    <name>sub-module-1 Maven Webapp</name>
    <url>http://maven.apache.org</url>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <finalName>sub-module-1</finalName>
    </build>
</project>
```

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

    <!-- 声明父模块 -->
    <parent>
        <artifactId>maven-module-demo</artifactId>
        <groupId>com.lixplor</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <modelVersion>4.0.0</modelVersion>
    <artifactId>sub-module-2</artifactId>
    <packaging>war</packaging>
    <name>sub-module-2 Maven Webapp</name>
    <url>http://maven.apache.org</url>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <finalName>sub-module-2</finalName>
    </build>
</project>

```