#### maven生命周期

                    maven生命周期：
                    验证 validate	验证项目	验证项目是否正确且所有必须信息是可用的
                    编译 compile	执行编译	源代码编译在此阶段完成
                    测试 Test	测试	使用适当的单元测试框架（例如JUnit）运行测试。
                    包装 package	打包	创建JAR/WAR包如在 pom.xml 中定义提及的包
                    检查 verify	检查	对集成测试的结果进行检查，以保证质量达标
                    安装 install	安装	安装打包的项目到本地仓库，以供其他项目使用
                    部署 deploy	部署	拷贝最终的工程包到远程仓库中，以共享给其他开发人员和工程
#### Maven仓库相关
     阿里中央仓库：
            <repository>  
                <id>alimaven</id>
                <name>aliyun maven</name>
                <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            </repository> 

            camunda.com 中央仓库：
            <repository>  
                <id>activiti-repos2</id>  
                <name>Activiti Repository 2</name>  
                <url>https://app.camunda.com/nexus/content/groups/public</url>  
            </repository>

            spring.io 中央仓库：
            <repository>  
                <id>springsource-repos</id>  
                <name>SpringSource Repository</name>  
                <url>http://repo.spring.io/release/</url>  
            </repository>

            maven.apache.org 中央仓库：
            <repository>  
                <id>central-repos</id>  
                <name>Central Repository</name>  
                <url>http://repo.maven.apache.org/maven2</url>  
            </repository>

            maven.org 中央仓库：
            <repository>  
                <id>central-repos1</id>  
                <name>Central Repository 2</name>  
                <url>http://repo1.maven.org/maven2/</url>  
            </repository>

            alfresco.com 中央仓库：
            <repository>  
                <id>activiti-repos</id>  
                <name>Activiti Repository</name>  
                <url>https://maven.alfresco.com/nexus/content/groups/public</url>  
            </repository>
