
**1. Security on Web UI**

Using password, follow  
 [Security in Airflow](http://airflow.apache.org/security.html)  

> 1. install dependence:

    pip install 'apache-airflow[password]'

> 2. change config file:  
[webserver]  
authenticate = True  
auth_backend = airflow.contrib.auth.backends.password_auth  

> 3. update password:

    # navigate to the airflow installation directory
    $ cd ~/airflow
    
    $ python
    
    Python 2.7.9 (default, Feb 10 2015, 03:28:08)
    Type "help", "copyright", "credits" or "license" for more information.  
    
    import airflow
    from airflow import models, settings
    from airflow.contrib.auth.backends.password_auth import PasswordUser
    user = PasswordUser(models.User())
    user.username = 'ViolinSolo'
    user.email = 'i.violinsolo@gmail.com'
    user.password = 'zero@123456'
    session = settings.Session()
    session.add(user)
    session.commit()
    session.close()
    exit()


**2. Change backend database**

follow  
[额外的扩展包](https://airflow.apachecn.org/#/zh/installation)  
[初始化数据库](https://airflow.apachecn.org/#/zh/howto/initialize-database)  
[mysqlclient Prerequisites](https://pypi.org/project/mysqlclient/)

> 1. install dependence:

[1.1 How to install python3-devel on red hat 7](https://stackoverflow.com/questions/43047284/how-to-install-python3-devel-on-red-hat-7)
    
    sudo yum install python36-devel.x86_64
    sudo yum install mysql-devel
    
[1.2 额外的扩展包](https://airflow.apachecn.org/#/zh/installation)

    pip install 'apache-airflow[mysql]'

> 2. 

[](https://blog.csdn.net/weixin_38335527/article/details/79025446)


** 3. Pendulum error with pymysql **

If we choose pymysql to be backend, we will get :  
    `AttributeError: 'Pendulum' object has no attribute 'translate' `
To solve it, follow issue: [](https://github.com/sdispater/pendulum/issues/267)  
Change Airflow settings.py code..

    207     try:
    208         #import MySQLdb.converters
    209         #MySQLdb.converters.conversions[Pendulum] = MySQLdb.converters.DateTime2literal
    210         import pymysql.converters
    211         pymysql.converters.conversions[Pendulum] = pymysql.converters.escape_datetime
    212     except ImportError:
    213         pass



**Tricks**

1. execution_date 的坑，注意他是 start time 的对应period的起止时间。并不是 start time 的对应的截止时间，原因是因为ETL的设计原理。
[Incorrect work of scheduler interval and start time in Apache Airflow](https://stackoverflow.com/questions/49529156/incorrect-work-of-scheduler-interval-and-start-time-in-apache-airflow/49530174#49530174)
[airflow trigger_dag execution_date is the next day, why?](https://stackoverflow.com/questions/39612488/airflow-trigger-dag-execution-date-is-the-next-day-why)




参考资料
[从0到1，AirFlow搭建踩坑历程](https://blog.csdn.net/wjf774219636/article/details/80105598)



大数据系统的数据时间同步问题，采用ntpd和ntpdate解决，  
其中ntpd比较稳定，不会跳跃，更好，但是较长时间差距不能立刻同步，就需要ntpdate了
 

配置方法：  
最有用的->  
[Linux配置ntp时间服务器（全）](https://www.cnblogs.com/quchunhui/p/7658853.html)
- [evertnote ver](https://www.evernote.com/shard/s425/u/0/sh/5f2af725-2b86-45e9-b5a7-322e11e7e37d/e1b06b726214434f8f78212442dee3c2)


[linux时间同步ntp服务的安装与配置](https://www.cnblogs.com/dagege/p/5948997.html)

  

[CentOs修改系统时间](https://www.cnblogs.com/veryvalley/p/10424795.html)

Centos系统，必须同时修改系统时间和硬件时间，才可以保证修改有效，单纯的使用date命令修改系统时间，是立即生效，重启后系统还原。具体操作如下：

1.date {查看目前本地的时间}
2.hwclock --show {查看硬件的时间}
3.如果硬件时间和系统时间不同，那就对硬件的时间进行修改
4.hwclock --set --date '014-12-15 15:15:15' {设置硬件时间为14年12月15日15点15分15秒}
5.hwclock --hctosys {设置系统时间和硬件时间同步}
6.clock -w {保存时钟}
--------------------- 
