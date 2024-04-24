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

6. 使用gitbash执行预编译命令
```bash
gradlew :spring-oxm:compileTestJava
```
7. 开始等待；持续的等待；漫长的等待........
   
