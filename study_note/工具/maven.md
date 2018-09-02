# 一. maven 安装、配置

## 1.1 maven安装

## 1.2 maven镜像

```
<!-- 阿里提供的镜像 -->
<mirror>
    <id>alimaven</id>
	<name>aliyun maven</name>
	<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
	<mirrorOf>central</mirrorOf>        
</mirror>
```



# 二. maven打包

## 1.1 打包指定module
- Maven多个mudule只编译、打包指定module

- 参考地址：[Maven多个mudule只编译、打包指定module](https://blog.csdn.net/fqwgc8/article/details/50517821)
- 命令：
  ```shell
  mvn install -pl A
  ```

# 三. maven 打包异常

## 1.1 maven打包项目异常

异常：

>[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:2.12.4:test (default-test) on project dataAccess: Unable to generate classpath: org.apache.maven.artifact.resolver.ArtifactResolutionException: Unable to get dependency information for org.apache.maven.surefire:surefire-junit4:jar:2.12.4: Failed to retrieve POM for org.apache.maven.surefire:surefire-junit4:jar:2.12.4: Could not transfer artifact org.apache.maven.surefire:surefire-junit4:pom:2.12.4 from/to central (https://repo.maven.apache.org/maven2): Received fatal alert: protocol_version
>[ERROR] org.apache.maven.surefire:surefire-junit4:jar:2.12.4
>[ERROR] 
>[ERROR] from the specified remote repositories:
>[ERROR] central (https://repo.maven.apache.org/maven2, releases=true, snapshots=false)
>[ERROR] Path to dependency:
>[ERROR] 1) dummy:dummy:jar:1.0
>[ERROR] -> [Help 1]
>[ERROR] 
>[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
>[ERROR] Re-run Maven using the -X switch to enable full debug logging.
>[ERROR] 
>[ERROR] For more information about the errors and possible solutions, please read the following articles:
>[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/MojoExecutionException
>[ERROR] 
>[ERROR] After correcting the problems, you can resume the build with the command
>[ERROR]   mvn <goals> -rf :dataAccess

处理：
```
<build>
    <plugins>
        <!-- 打包要用 -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.12.4</version>
            <configuration>
                <skipTests>true</skipTests>
            </configuration>
        </plugin>
    </plugins>
 </build>
```
- 添加后不行，有可能是版本的问题，之前使用2.19.1不行换成2.12.4可以了（没加这个插件是提示的是）



