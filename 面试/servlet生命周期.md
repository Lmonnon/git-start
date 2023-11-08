servlet生命周期

loadOnStartup为正数时，tomcat一启动就会执行new servlet()方法。

![image-20231106205630350](C:\Users\小月亮\AppData\Roaming\Typora\typora-user-images\image-20231106205630350.png)

当loadOnStartup为负数时，new servlet()会在第一次请求servlet时调用。

![image-20231106205840292](C:\Users\小月亮\AppData\Roaming\Typora\typora-user-images\image-20231106205840292.png)

![image-20231106210006320](C:\Users\小月亮\AppData\Roaming\Typora\typora-user-images\image-20231106210006320.png)

![image-20231106210021585](C:\Users\小月亮\AppData\Roaming\Typora\typora-user-images\image-20231106210021585.png)