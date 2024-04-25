# spring-mechanism

## 如何编译Spring源码
> [!note]+ 版本    
> Spring 6.0.2  
> Gradle 8.7  

1. 修改`gradle/wrapper/gradle-wrapper.properties文件，可以替换Gradle为本地
   ```markdown
   原来的gradle地址被301重定向的，但是cmd或者gitbash不知道怎么处理，所以需要修改
   ```
   #distributionUrl=https\://services.gradle.org/distributions/gradle-8.7-bin.zip    
   distributionUrl=file:///your_gradle_base_path/gradle-8.7-bin.zip
2. 有需要使用阿里云之类的仓库的话，修改`build.gradle`文件
  ```configure
configure(allprojects) { project ->
    apply plugin: "org.springframework.build.localdev"
    group = "org.springframework"
    repositories {
        maven { url "https://maven.aliyun.com/repository/public" }  // 增加改行，先使用阿里仓库
        mavenCentral()
        maven {
            url "https://repo.spring.io/milestone"
            content {
                // Netty 5 optional support
                includeGroup 'io.projectreactor.netty'
            }
        }
        if (version.contains('-')) {
            maven { url "https://repo.spring.io/milestone" }
        }
        if (version.endsWith('-SNAPSHOT')) {
            maven { url "https://repo.spring.io/snapshot" }
        }
    }
    configurations.all {
        resolutionStrategy {
            cacheChangingModulesFor 0, "seconds"
            cacheDynamicVersionsFor 0, "seconds"
        }
    }
}
  ```
3. 配置gradle使用的jdk，`gradle.properties`
// 增加如下行即可
org.gradle.java.home=your_jdk_base_path\\JDK_17_GRAAL

4. 修改`setting.gradle`文件的插件管理
// 新增如下的配置
```configration
pluginManagement{
	repositories {
		maven { url "https://maven.aliyun.com/repository/public" }
		mavenCentral();
		gradlePluginPortal()
		maven {url "https://repo.spring.io/release" }
	}
}
```
5. 修改预编译执行脚本`gradlew`文件
// 在脚本的第二行增加如下的内容，可以让脚本执行过程可视化
set -x

6. 确定使用的pom和kotlin版本是否存在
```markdown
本人在预编译过程中遇到的版本问题如下：
1. kotlin版本找不到
   RE：修改`gradle.properties`文件的`kotlinVersion=1.9.23`版本号，可用的版本通过`https://developer.aliyun.com/mvn/search`可以查到，根据可以用的版本，进行修改即可；
2. micrometer-bom版本找不到
   RE：修改`framework-platform.gradle`文件的`	api(platform("io.micrometer:micrometer-bom:1.12.5"))`的版本号即可；
3. reactor-bom版本找不到
   RE：修改`framework-platform.gradle`文件的`	api(platform("io.projectreactor:reactor-bom:2023.0.5"))`的版本号即可；
```

7. 使用gitbash执行预编译命令
```bash
gradlew :spring-oxm:compileTestJava
```

8. 开始等待；持续的等待；漫长的等待........

