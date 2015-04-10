###2015-04-08

+ junit 的before注解对每一个test注解提供同样的数据。test中修改before中的变量对其他test不起任何作用。

+ texlive 安装：需要从[http://tug.org/texlive/acquire.html](http://tug.org/texlive/acquire.html)下载，可选择网络下载和镜像下载。安装时解压文件，然后运行sudo命令（ubuntu下）安装，安装前会有选项提示。在 customizing installation collections 选项中选择需要的安装包，我的需要安装中文，去掉了其他语言。

###2015-04-09

##### PowmerMock 中一下两种写法的执行路径和效果不一样。

+ PowerMockito.doReturn(false).when(Util.class, "test", anyObject());

+ PowerMockito.when(Util.class, "test", anyObject()).thenReturn(false);

##### 代码修改最保险的方式就是通过开关来控制新老罗辑的交替，每一处都要有开关控制。一定要保持新老罗辑兼容。开关能退回原来的版本。

###2015-04-10

##### 对于会议的结论，记录在案，以免踢皮球

##### PowerMock 中mock 类似与：静态类.静态方法().普通public方法() 时，在mock前面的静态类和静态方法后，再mock普通public方法时，mock并不能真正屏蔽掉这个方法，还会执行方法体，如果某些字段没有设置的话，会抛出空指针。

##### gsettings 一个修改dconf（一个低层级的配置管理系统）内配置的命令，以及一套api接口。

+ 修改gedit 3.x的配置，让其支持中文的gsettings命令：gsettings set org.gnome.gedit.preferences.encodings auto-detected "['GB18030', 'UTF-8', 'CURRENT', 'ISO-8859-15', 'UTF-16', 'GBK', 'GB2312', 'BIG5']"。这个亦可用dconf-editor来设置，这个需要安装这个工具，默认是不带的。

####2015-04-13

Todo:重建分支，diff


