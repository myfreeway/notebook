站在一个组织的角度，假设使用maven作为包管理工具，假设有很多项目，那么会有以下问题。
1. 同个一个依赖包，在不同的项目用不同的版本号，有潜在的风险。
2. 肯定会有重复的内容，分散在不同项目中，难以升级和维护。

简单分析下，依赖包有2种，分别是公包、私包，重复的内容有多种，比如依赖包、版本号定义、属性、构建命令等

针对上述两个问题，可能第一反应是用父pom，把相同的依赖包和版本号、重复的内容都放到父pom里，让子项目继承。

那么父pom有很多种方式，先说不好的方式
1. 直接上依赖

```xml
	<groupId>com.xx</groupId>
	<artifactId>common-parent</artifactId>
	<version>1.0.1-SNAPSHOT</version>
	
	<dependencies>
		<!-- new -->
		<dependency>
			<groupId>com.netflix.hystrix</groupId>
			<artifactId>hystrix-javanica</artifactId>
			<version>${hystrix-version}</version>
		</dependency>
		<dependency>
			<groupId>com.netflix.hystrix</groupId>
			<artifactId>hystrix-core</artifactId>
			<version>${hystrix-version}</version>
		</dependency>
		<dependency>
			<groupId>com.netflix.hystrix</groupId>
			<artifactId>hystrix-metrics-event-stream</artifactId>
			<version>${hystrix-version}</version>
		</dependency>

		<!-- Spring -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-framework-bom</artifactId>
			<version>${spring.bom.version}</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
```
缺点：子项目可能会膨胀，把不必要的父pom的依赖包全部打进来。

2. 在第一种的基础上，加上modules

```xml
	<groupId>com.xxx</groupId>
	<artifactId>common-parent</artifactId>
	<version>1.0.1-SNAPSHOT</version>
	
	<dependencies>
		<!-- new -->
		<dependency>
			<groupId>com.netflix.hystrix</groupId>
			<artifactId>hystrix-javanica</artifactId>
			<version>${hystrix-version}</version>
		</dependency>
	</dependencies>
	
	<modules>
		<!-- 2.0.0-rc3 -->
		<module>xxx-common-bom</module>
		<module>xxx-common-domain</module>
		<module>xxx-common-event</module>
		<module>xxx-common-health-check</module>
	</modules>
```
缺点：如果这些module很不幸的需要继承本父pom，那么就是死循环了，所以强烈建议把父pom和modules分开两个pom.xml。

铺垫结束，进入正题，说说正确的打开方式。

## 目录结构

```txt
├─biz-app
│  └─biz-demo1-app
├─common-service
│  └─order-soa
│      ├─order-api
│      └─order-service
└─components
    ├─bom
    │  ├─common-bom
    │  └─pack-bom
    ├─common
    │  ├─common-demo1
    │  ├─common-demo2
    │  └─common-parent
    ├─pack
    │  └─json-pack
    ├─parent
    └─top-parent
```

    
工程化思路
====
1. maven的dependencyManagement和dependencies特性。dependencyManagement预定义包，dependencies只需指定group和artifactId，版本号继承而来。好处是统一管理版本号，避免混乱，对升级友好。
2. 发挥maven的继承特性，将很多统一配置放在顶级父pom，简化子项目的配置。比如构建，源码上传路径等。
3. 使用bom对一系列jar包进行封装，简化使用。
4. 从工程角度考虑项目结构，提高组织的开发效率。

## 各大组件关系介绍
1. top-parent 顶级父pom。定义源码deploy url，构建配置，定义公包依赖，业务项目不要直接继承
2. parent 业务项目的直接父pom。直接import各种聚合的bom，间接定义了公包和私包的依赖
3. bom/common-bom common项目的bom定义。依赖项可以是不存在的，尚未构建的。
4. bom/pack-bom 公包依赖项的组合包。方便业务项目引入。
5. pack/json-pack json组合包。目前只有jackson。可用于json序列化反序列化，springmvc的json输入输出。
6. common/common-parent common项目的直接父pom。单独一个父pom，是为了不造成循环依赖，也为了自由发展
7. common/common-demo1 common演示项目1。依赖一个公包。
8. common/common-demo2 common演示项目2。依赖common-demo1、json-pack

## 基础组件构建顺序
1. components/top-parent/pom.xml
2. components/pom.xml
3. components/common/pom.xml

## 源码路径与详细文档
[https://github.com/myfreeway/maven-project](https://github.com/myfreeway/maven-project)