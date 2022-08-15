# MAVEN 专家;内行
# 1 maven基础
## 1.1 maven介绍
maven是一个项目管理工具，在项目开发阶段对Java项目进行依赖管理和项目构建。  
依赖管理：对jar包的管理，将仓库中的jar包导入当前项目。  
项目构建：清理（clean） -> 编译（compile） -> 测试（test） -> 报告 -> 打包（package） -> 部署（deploy）

## 1.2 maven仓库类型
    1.本地仓库
    2.远程仓库
        中央仓库：
        私服（公司局域网内的仓库，需要自己搭建）
        其他公共远程仓库


## 1.2 maven常用命令
清理（clean） -> 编译（compile） -> 测试（test） -> 报告 -> 打包（package） -> 安装（install）
      
## 1.3 [坐标](pom.xml)


# 2 依赖传递
## 2.1 什么是依赖传递

### 依赖冲突
由于依赖传递引起的一个问题：在同一个工程中依赖传递了不同版本的jar包。

## 2.2 解决依赖冲突
1.使用maven提供的依赖调解原则

        第一声明者优先原则：以先声明（pom.xml）的依赖为准
        路径近者优先原则：直接依赖大于依赖传递（显示依赖优先于隐式依赖的版本）
        
2.排除依赖：直接排除，需要的话需要在外面引入依赖

```xml
    <exclusion>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
    </exclusion>
```
                        
3.锁定版本（最常用）

```xml
    <!--    锁定jar包版本，并没有引入jar包 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>5.3.20</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
    
    <!--    引入锁定的版本-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-beans</artifactId>
     </dependency>
```

## 2.3 properties标签

```xml
    <properties>
        <spring.version>5.3.22</spring.version>
    </properties>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>${spring.version}</version>
            </dependency>

```

# 3 maven聚合工程（分模块）

## 3.1 分模块构建maven工程分析
    常见的拆分方式：  
        1.按业务模块拆分，每个模块拆分成一个maven工程，例如：用户模块、订单模块、购物车模块  
        2.按层拆分，例如：持久层、业务层、表现层等  
    两种拆分方式都会提供一个父工程，将一些公共的代码和配置提取到父工程中进行统一管理和配置、版本锁定。
    
![alt maven分模块构建工程](images\maven分模块构建工程.png "maven分模块构建工程")
    
## 3.2 maven工程的继承
父工程打包方式必须为pom

## 3.3 maven工程的聚合
在maven工程的pom.xml文件中可以使用M<modules>标签将其他maven工程聚合到一起，进行统一操作（编译、打包等）。  
没有继承也可以聚合。



      
        
## Maven构件特性

  （1）构件具有依赖传递。例如：项目依赖构件A，而构件A又依赖B，Maven会将A和B都视为项目的依赖。

  （2）构件之间存在版本冲突时，Maven会依据 "短路优先" 原则加载构件。此外，我们也可以在 pom.xml 中，使用 <exclusions></exclusions>显式排除某个版本的依赖，以确保项目能够运行。

      （a）项目依赖构件A和B，构件A → C → D(version:1.0.0)，构件B → D(version:1.1.0)，此时，Maven会优先解析加载D(version:1.1.0)。
      （b）项目依赖构件A和B，构件A → D(version:1.0.0)， 构件B → D(version:1.1.0)，此时，Maven会优先解析加载D(version:1.0.0)。

  （3）构件的依赖范围。Maven在项目的构建过程中，会编译三套 ClassPath，分别对应：编译期，运行期，测试期。而依赖范围就是为构件指定它可以作用于哪套 ClassPath。

## Maven库与依赖范围

Maven中的库是个物理概念，存放各种依赖 Jar，而依赖是个逻辑概念，指定所依赖 Jar 的可见性。

这种可见性是按照编译、测试、运行等三种情况来划分的。
## Maven依赖范围

    compile
      编译依赖范围（默认），使用此依赖范围对于编译、测试、运行三种都有效，即在编译、测试和运行的时候都要使用该依赖 Jar 包。

    test
      测试依赖范围，从字面意思就可以知道此依赖范围只能用于测试，而在编译和运行项目时无法使用此类依赖，典型的是 JUnit，它只用于编译测试代码和运行测试代码的时候才需要。

    provided
      此依赖范围，对于编译和测试有效，而对运行时无效。比如 servlet-api.jar 在 Tomcat 中已经提供了，我们只需要的是编译期提供而已。

    runtime
      运行时依赖范围，对于测试和运行有效，但是在编译主代码时无效，典型的就是JDBC驱动实现。

    system
      系统依赖范围，使用 system 范围的依赖时必须通过 systemPath 元素显示地指定依赖文件的路径，不依赖 Maven 仓库解析，所以可能会造成建构的不可移植。

![alt maven依赖.png](images\maven依赖.png "maven依赖")