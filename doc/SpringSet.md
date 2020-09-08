#### Spring配置<import>标签
使用情景：在Maven项目中，我们在Spring 配置文件中需要用到<import resource="">标签来引入其他配置文件 <br>
* `<import resource="classpath:XXXXX.xml">` <br>
* (1)引入的是/WIN-INF/classes/xxxxx.xml文件，项目只会到这个目录下去寻找文件，其实就是src/main/resources文件下面的xxxxx.xml文件
* `<import resource="classpath*:XXXXX.xml">` <br>
(1)引入的是/WIN-INF/classes/xxxxx.xml文件，项目会到这个目录下去寻找文件，其实就是src/main/resources文件下面的xxxxx.xml文件
(2)也会在项目的jar包中寻找xxxxx.xml文件。在maven项目中，也会去项目依赖的打成jar文件的项目中去寻找xxxxx.xml文件。
