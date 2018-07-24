# pom.xml

### jdk版本设置

#### 方式一

```xml
<maven.compiler.source>1.8</maven.compiler.source>
<maven.compiler.target>1.8</maven.compiler.target>
```

#### 方式二

```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-compiler-plugin</artifactId>
	<version>3.1</version>
	<configuration>
		<source>1.8</source>
		<target>1.8</target>
		<showWarnings>true</showWarnings>
	</configuration>
</plugin>
```

