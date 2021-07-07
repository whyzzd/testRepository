在qt上使用MySql的注意事项

#### 1.MySql创建库，创建表，插入，以及查找命令

<div align=center><img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/Snipaste_2021-06-04_14-27-25.png" alt="Snipaste_2021-06-04_14-27-25" style="zoom:80%;" />

<div align=center><img src="https://cdn.jsdelivr.net/gh/whyzzd/pic-Bed/images/2021/Snipaste_2021-06-04_14-28-21.png" alt="Snipaste_2021-06-04_14-28-21" style="zoom:80%;" />

#### 2.安装过程中注意事项

在qt5.12以上中使用mysql除了按照[https://www.codenong.com/cs106561773/](https://www.codenong.com/cs106561773/)这上面做完之后，接着在MySql的安装中(例：C:\Program Files\MySQL\MySQL Server 5.7\lib)复制一份libmysql.dll到qt的安装目录下(例：D:\Qt\Qt5.12.10\5.12.10\mingw73_64\bin)再使用程序不会报错。



实例代码请参考：[demo](https://github.com/whyzzd/QtStudy/tree/master/23_Database)

