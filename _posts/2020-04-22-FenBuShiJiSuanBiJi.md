---
layout: post
title: "分布式计算笔记"
date: 2020-04-22
tags: ["学习笔记"]
comments: true
author: oneman233
excerpt: "基于阿里云"
---

* idea maven引入框架支持之后想删掉
    
    找到项目中的<项目名称>.imi，删除相关的facts标签

* 报错：`java.lang.ClassNotFoundException: "com.mysql.cj.jdbc.Driver" at java.net.URLClassLoader.findClass `

    这是加载JDBC的驱动问题。
  1. 第一步，检查 mysql-connector-java是否导进去，放在lib时候需要Add to Build path
  2. 第二步，检查mysql-connector-java jar版本是否跟本机安装的mysql版本匹配
  3. 第三步，检查是否存在拼写错误

* Intellij IDEA 添加jar包的方式：

    1. 打开 File -> Project Structure
    2. 单击 Modules -> Dependencies -> "+" -> "Jars or directories"
    3. 选择硬盘上的jar包并apply

* Java中switch case语句几乎与cpp一致：

    ```java
    switch(grade)
       {
          case 'A' :
            System.out.println("优秀"); 
            break;
          case 'B' :
          case 'C' :
            System.out.println("良好");
            break;
          case 'D' :
            System.out.println("及格");
            break;
          case 'F' :
            System.out.println("你需要再努力努力");
            break;
          default :
            System.out.println("未知等级");
       }
    ```

* Java的ArrayList类似于动态数组：

  1. Add方法用于添加一个元素到当前列表的末尾
  2. AddRange方法用于添加一批元素到当前列表的末尾
  3. Remove方法用于删除一个元素，通过元素本身的引用来删除
  4. RemoveAt方法用于删除一个元素，通过索引值来删除
  5. RemoveRange用于删除一批元素，通过指定开始的索引和删除的数量来删除
  6. Insert用于添加一个元素到指定位置，列表后面的元素依次往后移动
  7. InsertRange用于从指定位置开始添加一批元素，列表后面的元素依次往后移动
  8. Clear方法用于清除现有所有的元素
  9. Contains方法用来查找某个对象在不在列表之中
  10. ToArray方法把ArrayList的元素Copy到一个新的数组中

* Java数据库操作中`executeUpdate(sql)`方法的返回值是更新的条数。

* 关于fastjson
  * [fastjson下载](https://repo1.maven.org/maven2/com/alibaba/fastjson/1.2.9/)
  * [fastjson教程](https://www.runoob.com/w3cnote/fastjson-intro.html)
  * 使用fastjson解析json文件：

    ```java
    InputStream inputStream = new FileInputStream("test.json");
    String text = IOUtils.toString(inputStream,"utf8");
    List<Test> s=JSON.parseArray(text, Test.class);
    //把test.json中的数组转换成Java对象
    ```

* 报错：FileInputStream 系统找不到文件

    ```java
    FileInputStream fiStream = new FileInputStream("src\\text1\\FileInputStreamText.java");
    //使用相对于项目根目录的相对路径
    ```

* 报错：Software caused connection abort: recv failed

    可能是服务器端报错导致强制服务端终止，而此时客户端还在请求数据。

* Java判断某对象是否为null：
  * 直接使用`object == null`判断
  * 对象为null的时候返回true,不为null的时候返回false

* IDEA 写swing程序 生成Form Main报错：
  * 给JPanel绑定一个FieldName即可
  * 其创建方法为右键New-Swing UI Designer-GUI Form
  * 此外还需要alt+insert添加一个Main函数

* Java Socket实现多线程：

    ```java
    socket = serverSocket.accept();//侦听并接受到此套接字的连接
    InetAddress inetAddress=socket.getInetAddress();//获取客户端的连接
    ServerThread thread=new ServerThread(socket,inetAddress);//自己创建的线程类
    //ServerThread extends Thread并且需要实现run方法
    thread.start();//启动线程
    ```

* 获取object对应的类采用`object.getClass()`方法。

* 客户端总是收到空包的问题：

    检查在测试网络连接时发送空包后，客户端是否关闭了socket以及输入输出流。

* 在java1.9版本中，`newInstance()`已经被弃用，取而代之的是`class.getDeclaredConstructor().newInstance()`来获取某个类对应的实例

# 关于云服务器的ActiveMQ的配置

1. 首先在服务器上[安装jdk](https://www.jianshu.com/p/10949f44ce9c)。
2. 随后需要[在本地下载ActiveMQ并且用SSH丢到服务器上去](https://www.jianshu.com/p/23a01117b42d)。
3. 启动ActiveMQ时报错，解决办法是[用vim配置JAVA_HOME](https://www.fatalerrors.org/a/configuration-variable-java_home-or-javacmd-is-not-defined.html)。
4. [vim的简单命令](https://www.runoob.com/linux/linux-vim.html)，**实际上用阿里云的File Navigator就足够好**。
5. ActiveMQ的Broker的默认登录名和密码都是admin，**记得在阿里云后台把8161和61616端口打开**，前者用于ActiveMQ，后者用于对建立的MQ的访问，[实际创建工厂时用到的地址](tcp://182.92.108.139:61616/)。
6. Broker界面`send to`用来创建新消息，`purge`用来清空队列，`delete`用来删除队列
7. 消息被传递后，任何修改消息体的操作都应该抛出`MessageNotWriteableException`异常，所以回复时最好新建一条消息。
8. 要想在服务器端运行服务程序，必须要[打包成jar包](https://blog.csdn.net/baomw/article/details/86488290)才能在Linux下运行，之后在服务器上用命令`java -jar xxx.jar`就可以执行了。
9. 上传jar包后运行报错`this version of the Java Runtime only recognizes class file versions up to 52.0`
    
    JDK版本太老的问题，[解决参考](https://blog.csdn.net/wltsysterm/article/details/79169417)。

    首先下载[JDK8](https://www.oracle.com/java/technologies/javase-jdk8-downloads.html)，然后在`build artifacts`的时候改两个设置：   
    1. Modules下的Language level改成了8
    2. SDKs下面用了JDK1.8的默认路径
10. [关闭jar](https://blog.csdn.net/qq_39507276/article/details/82227416)
    
    [jar不断线的方法](https://blog.csdn.net/nn690960430/article/details/100559912)

11. [Redis的安装以及不掉线](https://www.jianshu.com/p/95322e2acf9a)
    
    [Redis的阿里云配置和密码修改](https://www.cnblogs.com/swda/p/12013439.html)

    [Redis Desktop的使用](https://www.jianshu.com/p/6895384d2b9e)

    [找不到redis-server的解决方法](https://blog.csdn.net/weixin_34415923/article/details/85970223)

    [make install的报错解决方法](https://www.cnblogs.com/richerdyoung/p/8066373.html)

12. [Redis工具类讲解](https://www.cnblogs.com/tengfly/p/9307373.html)
13. `ActiveMQ.DLQ`的出现意味着你的**某个消费者产生了某些无法消费的数据**。
14. activemq中解析ObjectMessage请先把监听到的message强制转换成objectmessage，再把objectmessage的getobject()方法强制转换成需要的类，像下面这样：

    ```java
    ObjectMessage objectMessage = (ObjectMessage) message;
    ticket = (Ticket) objectMessage.getObject();
    ```
15. `JavaComboBox`是下拉框，`getSelectedItem()`方法可以获得当前选中了谁，而`setSelectedIndex()`方法可以设定当前选中了第几序号的选项。
16. 想用JAR读取当前目录的配置文件，只需要这样写：

    ```java
    InputStream inputStream = new FileInputStream("name.json");
    ```

    然后把json放到跟打包好的jar包同一目录下即可。

17. 你只需要一行代码就能用fastjson把java类转换成json字符串：

    ```java
    String s = JSON.toJSONString(ticket);
    ```

18. [docker的安装以及启动tomcat](https://www.runoob.com/docker/docker-install-tomcat.html)，它是一个相当优秀的的容器管理工具，用于快速地发布和测试项目。
19. [docker安装之后tomcat首页404](https://blog.csdn.net/qq_40891009/article/details/103898876)
20. linux删除文件夹：`rm-rf /var/log/httpd/access`

    linux删除文件：`rm-f /var/log/httpd/access.log`

21. 服务器重启之后
    1. [activemq的启动](https://blog.csdn.net/fuck_money/article/details/81011236)
    2. [docker的启动](https://blog.csdn.net/XYKenny/article/details/91038472)
    3. [docker容器的启动](https://www.runoob.com/docker/docker-container-usage.html)

22. 服务器重启之后redis会重置密码，需要[重新设置](https://www.cnblogs.com/zfding/p/7966450.html)。
    [重启redis的命令](https://blog.csdn.net/guo13313/article/details/70666453)

23. [ps aux|grep命令详解](https://www.cnblogs.com/robertoji/p/5555449.html)
24. [docker启动和停止tomcat](https://blog.csdn.net/qq_34589867/article/details/101061864)

    [docker启动tomcat访问首页报404](https://blog.csdn.net/qq_40891009/article/details/103898876)

    [docker快速部署web应用](https://blog.csdn.net/wchenjt/article/details/78997900?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)(**实际上war放到启动docker镜像时配置的目录下就OK，重启镜像后就可以找到对应的war**)

25. [VUE学习参考](https://www.runoob.com/vue2/vue-loop.html)
    [官方文档](https://cn.vuejs.org/v2/guide/list.html) 
    **`v-for`的本质是把当前标签重复输出，并且可以调用父层级的数据**

26. [服务器重启导致redis数据清空](https://blog.csdn.net/zhangxing52077/article/details/79483819)
27. tomcat引入jar包之后还要[放到lib目录下](https://blog.csdn.net/u011304490/article/details/84603250)，否则会报500。
28. [定时刷新页面](http://blog.okbase.net/haobao/archive/56406.html)
29. [table标签居中](https://www.cnblogs.com/hfeng007/p/9138100.html)
30. [swing提示框](https://blog.csdn.net/xjh101010/article/details/76746726)
31. [引入Element](https://element.eleme.cn/#/zh-CN/component/installation)
    
    [Element的table的使用](https://www.jianshu.com/p/f1dd60cacfb5)

    [Element获取表格某一行的数据可以使用插槽](https://blog.csdn.net/qq_33616027/article/details/90411872)
    
    [Element用计算属性过滤](https://blog.csdn.net/qq_38451270/article/details/103988899)

32. JS中`x=>x+1`相当于以下函数：

    ```js
    function(x) {
        return x+1;
    }
    ```

33. 一个简单的正则表达式匹配：

    ```js
    let reg = new RegExp(this.InputSearch.trim());
    // let只在当前作用域有效，更安全
    return this.AllTicket.filter(item => reg.test(item.from))
    // 只匹配含有当前字符串的字符
    ```

34. [JS中Date和String的互换](https://blog.csdn.net/zqq3436/article/details/78683813)

    [利用正则替换字符串中的字符](https://blog.csdn.net/my13413527259/article/details/87072909)

    **正则表达式中如果出现了 '/' ，则需要用 '\\' 转义**

35. Element的排序只需要加上[sortable属性](https://element.eleme.cn/#/zh-CN/component/table)即可.
36. docker上部署的tomcat的lib文件夹需要更新？试试下面这种绑定方法：

    ```
    docker run --name ticket -p 8080:8080 -v $PWD/test:/usr/local/tomcat/webapps/test -v $PWD/tomcat_libs:/usr/local/tomcat/lib -d tomcat
    ```

    可以直接把lib目录绑定到当前目录的tomcat_libs下，webapps的test则绑定到了test下

37. **永远把war放在webapps下**，不要加任何其他目录，否则你都不知道为什么404。
38. axios的post可能会500，需要使用URLSearchParams，[参考](https://alone88.cn/archives/392.html)。
39. 一个想要序列化的类，除了自己`Serializable`之外，所有的成员变量也都要`Serializable`。
40. [解决ObjectMessage读取的时候报错](https://blog.csdn.net/g39173/article/details/53538484)（安全权限问题）

41. java编译中报错`Exception in thread “main" java.lang.UnsupportedClassVersionError`：

    是由较高版本的JDK编译的java class文件试图在较低版本的JVM上运行时产生的错误。

42. [activeMQ修改密码](https://www.cnblogs.com/lovelinux199075/p/8920656.html)

    [重启activeMQ](https://www.cnblogs.com/lichenx/p/9564087.html)

# 期末复习笔记

<embed src="../files/分布式计算大总结.pdf" type="application/pdf" />