վ��һ����֯�ĽǶȣ�����ʹ��maven��Ϊ�������ߣ������кܶ���Ŀ����ô�����������⡣
1. ͬ��һ�����������ڲ�ͬ����Ŀ�ò�ͬ�İ汾�ţ���Ǳ�ڵķ��ա�
2. �϶������ظ������ݣ���ɢ�ڲ�ͬ��Ŀ�У�����������ά����

�򵥷����£���������2�֣��ֱ��ǹ�����˽�����ظ��������ж��֣��������������汾�Ŷ��塢���ԡ����������

��������������⣬���ܵ�һ��Ӧ���ø�pom������ͬ���������Ͱ汾�š��ظ������ݶ��ŵ���pom�������Ŀ�̳С�

��ô��pom�кܶ��ַ�ʽ����˵���õķ�ʽ
1. ֱ��������

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
ȱ�㣺����Ŀ���ܻ����ͣ��Ѳ���Ҫ�ĸ�pom��������ȫ���������

2. �ڵ�һ�ֵĻ����ϣ�����modules

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
ȱ�㣺�����Щmodule�ܲ��ҵ���Ҫ�̳б���pom����ô������ѭ���ˣ�����ǿ�ҽ���Ѹ�pom��modules�ֿ�����pom.xml��

�̵�������������⣬˵˵��ȷ�Ĵ򿪷�ʽ��

## Ŀ¼�ṹ

```txt
����biz-app
��  ����biz-demo1-app
����common-service
��  ����order-soa
��      ����order-api
��      ����order-service
����components
    ����bom
    ��  ����common-bom
    ��  ����pack-bom
    ����common
    ��  ����common-demo1
    ��  ����common-demo2
    ��  ����common-parent
    ����pack
    ��  ����json-pack
    ����parent
    ����top-parent
```

    
���̻�˼·
====
1. maven��dependencyManagement��dependencies���ԡ�dependencyManagementԤ�������dependenciesֻ��ָ��group��artifactId���汾�ż̳ж������ô���ͳһ����汾�ţ�������ң��������Ѻá�
2. ����maven�ļ̳����ԣ����ܶ�ͳһ���÷��ڶ�����pom��������Ŀ�����á����繹����Դ���ϴ�·���ȡ�
3. ʹ��bom��һϵ��jar�����з�װ����ʹ�á�
4. �ӹ��̽Ƕȿ�����Ŀ�ṹ�������֯�Ŀ���Ч�ʡ�

## ���������ϵ����
1. top-parent ������pom������Դ��deploy url���������ã����幫��������ҵ����Ŀ��Ҫֱ�Ӽ̳�
2. parent ҵ����Ŀ��ֱ�Ӹ�pom��ֱ��import���־ۺϵ�bom����Ӷ����˹�����˽��������
3. bom/common-bom common��Ŀ��bom���塣����������ǲ����ڵģ���δ�����ġ�
4. bom/pack-bom �������������ϰ�������ҵ����Ŀ���롣
5. pack/json-pack json��ϰ���Ŀǰֻ��jackson��������json���л������л���springmvc��json���������
6. common/common-parent common��Ŀ��ֱ�Ӹ�pom������һ����pom����Ϊ�˲����ѭ��������ҲΪ�����ɷ�չ
7. common/common-demo1 common��ʾ��Ŀ1������һ��������
8. common/common-demo2 common��ʾ��Ŀ2������common-demo1��json-pack

## �����������˳��
1. components/top-parent/pom.xml
2. components/pom.xml
3. components/common/pom.xml

## Դ��·������ϸ�ĵ�
[https://github.com/myfreeway/maven-project](https://github.com/myfreeway/maven-project)