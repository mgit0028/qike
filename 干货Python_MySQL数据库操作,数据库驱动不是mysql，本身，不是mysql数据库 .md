04.01 14:31
Python+MySQL数据库操作,数据库驱动不是mysql，本身，不是mysql数据库。
干货 | Python+MySQL数据库操作
原创   数据魔术师  2019-02-25

Python+MySQL数据库操作

本文介绍如何利用python来对MySQL数据库进行操作，本文将主要从以下几个方面展开介绍：

 1.数据库介绍

 2.MySQL数据库安装和设置

 3.Python操作MySQL

在Python3.X上安装MySQL驱动

创建数据库连接

创建数据表

增、改、删、查

分组、聚合

按批量读取和处理数据

 4.小结

01
 数据库介绍
数据库（Database）是按照数据结构来组织、存储和管理数据的仓库，能直接通过条件快速查询到指定的数据。随着信息技术和市场的发展，数据管理不再仅仅是存储和管理数据，而转变成用户所需要的各种数据管理的方式。

目前，数据库主要有两种形式，一种是非关系型数据库，另一种是关系型数据库。目前，我们用得非常广泛的一种数据库类型是关系型数据库，它可以分为以下几种：

Oracle:

    付费产品，主要是银行在用（万一出错了有Oracle背锅）

DB2:

    付费产品，IBM产品

SQL Sever：

        付费产品，微软产品，windows专用

PostgreSQL：

    免费产品，主要是高校学术上使用

MySQL：

    大众，免费，开源

     作为手无寸金的大学生，我们应该用哪种数据库呢？当然是MySQL。一方面是因为MySQL免费，另一方面是因为普及率最高，出了错，可以很容易找到解决方法。而且，围绕MySQL有一大堆监控和运维的工具，安装和使用很方便。所以，本文接下来也会介绍如何用Python来操作MySQL。


02
 MySQL安装和设置

2.1 下载安装：
STEP1：MySQL官方网站上下载最新的MySQL Installer 8.0.14版本，下载链接为：

https://dev.mysql.com/downloads/installer/

STEP2：按照指示操作默认安装，在安装时，MySQL会要求我们设置一个本地登陆账号，账号名一般命为root，端口为3306，自定义一个password即可。

2.2 MySQL workbench创建用户与授权：

STEP1：创建新用户。以root用户登录MySQL workbench，先选择users and privileges，然后选择下方的add account去添加用户。



STEP2：我们以创建好的blank为例，创建好后在左边的表中出现了blank这个用户。返回MySQL Workbench主页，新建MySQL Connection，用户名和密码为新创建的用户名和密码，这个时候，我们就能看到除了root用户外，还能看到新创建的blank这个user账户了。



STEP3：创建数据库和数据表。在root账户中新建一个名字为test_s的Schema，然后我们会在左下角的schemas中看到我们新建的schema。对于Schema，在MySQL中，它等同于database，它是数据库对象的集合，这个集合包括了各种对象，如Tables（表）、Views（视图）、Sorted Procedures（存储过程）、Functions等，我们可以选中Table，点击鼠标右键，选择creat table，即可在该数据库下创建数据表。创建过程和创建数据库类似。



STEP4：设置用户权限：而当我们用blank连接数据库时，是没有test_s这个schema的，这个时候我们需要通过root开放权限给blank这个用户。返回root用户操作选项卡，选择users and privileges，选中blank用户，再选择schema privileges，点击add entry；在弹出来的窗口中选择权限范围，这里我们选择指定的test_s给blank这个user，点击ok；双击tets_s，privileges全部授予,点击select all。



STEP5：进入主界面，重新进入blank用户操作选项卡，我们会在schemas中看到test_s这个schema。blank这个用户可以对root授权的test_s这个schema中的表进行操作。


至此，我们完成了MySQL中用户的新建和授权。


################################

03
 Python操作MySQL


目前，关于Python操作数据库主要有以下几种方法：

MySQLdb的使用

MySQLdb是用于Python连接MySQL数据库的接口，它实现了Python数据库API规范V2.0，基于MySQL C API上建立的，目前只支持Python2.x。

PyMySQL的使用

PyMySQL是Python中用于连接MySQL服务器的一个库，它支持Python3.x，是一个纯Python写的MySQL客户端，它的目标是替代MySQLdb。PyMySQL在MIT许可下发布。

mysql.connector 的使用

由于 MySQL 服务器以独立的进程运行，并通过网络对外服务，所以，需要支持 Python 的 MySQL 驱动来连接到 MySQL 服务器。

目前，有两个 MySQL 驱动：

mysql-connector-python：是 MySQL 官方的纯 Python 驱动

MySQL-python ：是封装了 MySQL C驱动的 Python 驱动

SQLAlchemy的使用

是一种ORM（Object-Relational Mapping）框架，将关系数据库的表结构映射到对象上，隐藏了数据库操作背后的细节，简化了数据操作。

################################
3.1 在Python3.X上安装MySQL驱动
STEP1：由于MySQL官方提供了mysql-connector-python驱动。安装时，在Anaconda Prompt中输入：

    

conda install mysql-connector-python

                                       

STEP2：使用以下代码来测试mysql-connector是否安装成功：

import mysql.connector



如果没有产生错误，则表明安装成功。

3.2 创建数据库连接
这里连接的是我之前创建的blank这个user。如果数据库已经存在的话，我们可以直接连接；如果数据库不存在，直接连接则会报错，这个时候我们就需要创建一个数据库，创建数据库可以在MySQL Workbench中创建，也可以在python中使用"CREATE DATABASE"语句，在本实验中，我们使用已经在MySQL workbench中已经建好的test_s这个数据库。

import mysql.connector
#连接数据库
config = {
    'user' : 'blank'        #用户名
    'password' : 'password' #自己设定的密码
    'host' : '127.0.0.1'    #ip地址，本地填127.0.0.1，也可以填localhost
    'port' : '3306'         #端口，本地的一般为3306
    'database' : 'test_s'   #数据库名字，这里选用test_s
}
con = mysq;.connector.connect(**config)



 3.3 创建数据表
STEP1:当Python 和数据之间的连接建立起来之后，要操作数据库，就需要让 Python对数据库执行SQL语句。创建数据表我们使用"CREATE TABLE"语句，在test_s这个数据库中创建一个叫做customers的表格，其中包含id、name、address、sex、age、sl这六个columns。Python是通过游标执行SQL语句的，所以，连接建立之后，就要利用连接对象得到游标对象。

cursor()：表示游标

execute()：是执行语句

STEP2：一般在创建新表的时候，我们还会设置一个主键（PRIMARY KEY）来方便进行查询工作。创建主键，我们可以用"INT AUTO_INCREMENT PRIMARY KEY"

# 创建一个表
# buffered = True 不设的话，查询结果没有读完会报错
# raise errors.InternalError("Unread result found")
mycursor = con.cursor(buffered = True)
mycursor.execute("CREATE TABLE customers(id INT AUTO_INCREMENT PRIMARY KEY, \
                               name VARCHAR(255) , address VARCHAR(255), \
                               7sex VARCHAR(225) , age INT(10) , sl INT(10))")

   #VARCHAR()表示的是数据类型，定义的是变长字符串；INT（）表示整型

STEP3：执行语句。执行完后，我们可以回到MySQL workbench，可以看到在test_s下面的customers这个表格，其中Columns为我们创建的id，name，address，sex，age和sl。


STEP4：但是，当我们再次执行语句的时候，由于已经创建了"customers"这个表，所以再次执行会报错，这个时候就需要加一个判断，判断这个表是否已经存在于test_s这个数据库中

ProgrammingError: Table 'customers' alreadyy exists



STEP5：我们可以用"SHOW TABLES"语句来查看数据表是否已经存在，如果存在就print"table already exists",如果不存在，就print"table does not exist"。

def tableExists(mycursor, name):
    stmt = "SHOW TABLES LIKE '" +name+ "'"
    mycursor.execute(stmt)
    return mycursor.fetchone()
mycursor = con.cursor()
if tableExists(mycursor , 'customers'):
    print("table already exists")
else:
    print("table not exists")



STEP6：上面的语句只是为了帮助我们判断是否有同名表，当我们要新建一个表时，我们可以在这个判断的基础上，在创建新表前删掉数据库内的同名表，再建新表。删除我们用的是"DROP TABLE"，新建表是"CERATE TABLE"

import mysql.connector  
#连接数据库  
config = {  
    'user' : 'blank',        
    'password' :'fuying123888',  
    'host' : '127.0.0.1',   
    'port':'3306',           
    'database' : 'test_s'  
}  
con = mysql.connector.connect(**config)  
# 检查一个表是否存在  
def tableExists(mycursor, name):  
    stmt = "SHOW TABLES LIKE '"+name+"'"  
    mycursor.execute(stmt)  
    return mycursor.fetchone()     
# 删除一个表（无论它是否已经存在）  
def dropTable(mycursor, name):  
    stmt = "DROP TABLE IF EXISTS "+name  
    mycursor.execute(stmt)  
# buffered=True 不设的话，查询结果没有读完会报错  
# raise errors.InternalError("Unread result found")  
mycursor = con.cursor(buffered=True)  
# 删除临时表  
tableName = 'customers'  
dropTable(mycursor, tableName)  
# 创建一个表       
mycursor.execute("CREATE TABLE customers(id INT AUTO_INCREMENT PRIMARY KEY,\
                               name VARCHAR(255), address VARCHAR(255), \  
                               sex VARCHAR(225), age INT(10), sl INT(10))")

 3.4 增、改、删、查
    3.4.1增

      在cutomers表中插入数据用的是"INSERT INTO"语句。

除了用一条条用execute( )插入之外，我们还可以用executemany（）的方式批量插入，也就是val中包含的是一个元组列表，包含我们想要插入的数据。

需要注意的事是：如果数据表格有更新，那么必须用到commit()语句，否则在workbench是看不到插入的数据的。

# 往表里插入一些记录  
sql="INSERT INTO customers(name,address,sex,age,sl) VALUES(%s, %s,%s,%s,%s)"
val = ("John", "Highway 21","M",23,5000)  
mycursor.execute(sql, val)  
val = ("Jenny", "Highway 29","F",30,12500)  
mycursor.execute(sql, val)  
val=[("Tom","ABC 35","M",35,14000),  
     ("Tom1","Highway 29","M",28,6700),  
     ("Lily","Road 11","F",30,8000),  
     ("Martin","Road 24","M",35,14000),  
     ("Sally","Fast 56","M",32,15000)]  
mycursor.executemany(sql, val)  
con.commit()  

执行以上代码后，回到workbench，，我们可以看到最终的结果为：




