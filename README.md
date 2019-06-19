### Nexus仓库
> 官方文档地址: https://help.sonatype.com/repomanager3

#### 1.下载安装包
> 下载nexus-3.16.2-01-unix.tar


#### 2.启动Nexus
> 启动命令: nexus-3.16.2-01/bin/nexus start

> 停止命令: nexus-3.16.2-01/bin/nexus stop

> 其他命令: start, stop, restart, force-reload and status

#### Nexus管理后台
> 默认端口号是8081: http://localhost:8081/

> 默认用户名: admin

> 默认密码: admin123

### 创建仓库
1.

[](doc/images/p1.png)

2.

[](doc/images/p2.png)

3.

[](doc/images/p3.png)


### 配置

配置~/.m2/settings.xml

```xml
<settings>

  <servers>
    <server>
      <id>nexus</id>
      <username>admin</username>
      <password>admin123</password>
    </server>
  </servers>
  
  <mirrors>
    <mirror>
      <!--This sends everything else to /public -->
      <id>nexus</id>
      <mirrorOf>*</mirrorOf>
      <url>http://localhost:8081/repository/maven-public/</url>
    </mirror>
  </mirrors>
  <profiles>
    <profile>
      <id>nexus</id>
      <!--Enable snapshots for the built in central repo to direct -->
      <!--all requests to nexus via the mirror -->
      <repositories>
        <repository>
          <id>central</id>
          <url>http://central</url>
          <releases><enabled>true</enabled></releases>
          <snapshots><enabled>true</enabled></snapshots>
        </repository>
      </repositories>
     <pluginRepositories>
        <pluginRepository>
          <id>central</id>
          <url>http://central</url>
          <releases><enabled>true</enabled></releases>
          <snapshots><enabled>true</enabled></snapshots>
        </pluginRepository>
      </pluginRepositories>
    </profile>
  </profiles>
  <activeProfiles>
    <!--make the profile active all the time -->
    <activeProfile>nexus</activeProfile>
  </activeProfiles>
</settings>
```

pom.xml

```xml

<distributionManagement>
    <repository>
      <id>nexus</id>
      <name>Releases</name>
      <url>http://localhost:8081/repository/maven-releases</url>
    </repository>
    <snapshotRepository>
      <id>nexus</id>
      <name>Snapshot</name>
      <url>http://localhost:8081/repository/maven-snapshots</url>
    </snapshotRepository>
  </distributionManagement>

```

#### 将jar包上传到私服

> mvn clean deploy

### Gradle配置

build.gradle

```groovy
repositories {
    maven {
        url "http://localhost:8081/repository/maven-public/"
    }
}
```

gradle.properties

```properties
nexusUrl=http://localhost:8081
nexusUsername=admin
nexusPassword=admin123 
```

build.gradle 上传本地jar包到私服

```groovy
uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: "${nexusUrl}/repository/maven-releases/") {
                authentication(userName: nexusUsername, password: nexusPassword)
            }
            snapshotRepository(url: "${nexusUrl}/repository/maven-snapshots") {
                authentication(userName: nexusUsername, password: nexusPassword)
            }
        }
    }
}

```

> gradle uploadArchives


### 将第三方jar手动安装到nexus
> mvn deploy:deploy-file

|参数|说明|示例|
|---|---|---|
|url|远程仓库地址|http://10.67.200.215:8081/repository/maven-public/|
|repositoryId|用户名密码|对应的是settings.xml配置文件里面的<id>，<server><id>mutms</id><username>mutms</username><password>ceair123</password></server>|
|file|要安装到远程的jar路径|path-to-your-artifact-jar|
|groupId|||
|artifactId|||
|version|||
|packaging||jar|
|generatePom|是否生成pom文件||
||||
||||



```
mvn deploy:deploy-file  -Durl=file:///C:/m2-repo \
                        -DrepositoryId=some.id \
                        -Dfile=path-to-your-artifact-jar \
                        -DgroupId=your.groupId \
                        -DartifactId=your-artifactId \
                        -Dversion=version \
                        -Dpackaging=jar \
                        -DgeneratePom=false
```
                       
                       