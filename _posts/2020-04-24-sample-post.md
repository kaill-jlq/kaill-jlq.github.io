
<center>

# 实验四：SQL  安全性



###### 姓名：姜凌麒
###### 学号：3210100336

</center>

---

### 实验目的：
+ 熟悉通过SQL进行数据完整性控制的方法。

### 实验平台：
+ 操作系统：  `macOS 13.2.1`  
+ 数据库管理系统：`MySQL 8.0.32`

### 实验内容和要求：
1. 建立表，考察表的生成者拥有该表的哪些权限。
2. 使用`SQL` 的`grant` 和`revoke`命令对其他用户进行授权和权力回收，考察相应的作用。
3. 建立视图，并把该视图的查询权限授予其他用户，考察通过视图进行权限控制的作用。

### 实验过程：
1. 登录，考察当前登录的用户的权限
   ```SQL
    show grants;
   ```
    ![](1.jpeg)
2. 用当前的root用户创建新的用户`user001`，并检查是否创建成功
    ```SQL
    create user 'user001'@'localhost' identified by '123456';
    //检查是否创建成功
    select User,authentication_string,Host from mysql.user;
    ```
    ![](2.jpeg)
3. 登录`user001`，并查看`user001`的用户权限
   ```SQL
   show grants;
   show databases;
   ```
   ![](3.jpeg)
   ![](5.jpeg)
   + 可以看到user001用户当前无`select`等权限，此时查看database也看不到`test`

4. 登录`root`用户，并用`grant`语句赋值`user001`用户在数据库`test`上的`select`权限
   ```SQL
   grant select on test.* to user001@localhost;
   ```
   ![](4.jpeg)
   + 设置权限成功
5. 重新登录`user001`,查看权限
   ```SQL
   show grants;
   ```
   ![](6.jpeg)
   + 可以看到此时`user001`多了在`test`数据库上的`select`权限
   ![](7.png) 
   成功在`test`的`branch`表上进行了`select`操作
6. 登录`root`用户回收权限,并重新查看用户`user001`权限
    ```SQL
    revoke all on test.* from user001@localhost
    ```
    ![](8.jpeg)
   + 如上图所示，权限删除成功
7. 创建视图，并授予用户`user001`在视图上的`select`权限，并查看是否授予权限成功
   ```SQL
   create view view01 as 
   select * from branch where branch_city='Shanghai';
   ```
   ![](9.jpeg)
   创建视图成功
   ```SQL
   grant select on view01 to user001@localhost;
   ```
   ![](10.jpeg)
   授予权限成功,登录`user001`查看权限
   ![](11.jpeg)
   