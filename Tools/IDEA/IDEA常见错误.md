 1. IntelliJ IDEA中Warning:java: 源值1.5已过时, 将在未来所有发行版中删除
 IDEA默认把项目的源代码版本设置为jdk1.5，目标代码设置为jdk1.5
 
- 修改Maven的Settings.xml文件添加如下内容
 ```
<profile>
  <id>jdk-1.8</id>
  <activation>
    <activeByDefault>true</activeByDefault>
    <jdk>1.8</jdk>
  </activation>
  <properties>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
  </properties>
</profile>
```

- 在项目的pom.xml文件中添加
```
<properties>
 <maven.compiler.source>1.8</maven.compiler.source>
  <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

- 打开项目配置，设置Modules的Language Level为”8”

- 最后按”Ctrl+Alt+S”打开设置，搜索”Java Compiler”，将默认jdk和当前modual的jdk版本切换为1.8即可