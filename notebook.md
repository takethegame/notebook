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

###2015-04-13

+ static 字段的set方法不能为static，否则spring启动报错。

###2015-04-14

##### xml文件映射到Object里面如何保证字段的正确性

+ 1. 程序员自己保证。 在类org.apache.commons.digester.Digester中设置需要将xml文件转换成的类。
+ 2. XML schema 定义。XML Schema可定义出现在文档中的元素、属性、元素的子元素、元素次序、元素数目，元素和属性的数据类型，元素和属性的默认值固定值等。

###2015-04-16

##### TODO: 为什么用powermock取模拟Thread.currentThread().getContextClassLoader()的getResourceAsStream方法时，会得到多个方法，powermock无法具体确定哪个方法？而直接调用则可以Thread.currentThread().getContextClassLoader().getResourceAsStream(filename)则可以。mock的方法为：method(Thread.currentThread().getContextClassLoader().getClass(), "getResourceAsStream", String.class)

##### Class.getResourceAsStream(filename):如果filename不以/开头则默认是当前类所处的目录查找文件。ClassLoader.getResourceAsStream(filename):不管filename是什么情况都是以根目录开始查找文件的。
+ String.getResourceAsStream("tt.txt"),查找的文件是java/lang/tt.txt
+ String.getClassLoader().getResourceAsStream("tt.txt"),查找的文件是classpath/tt.txt

##### ClassLoader 的目录会往下传递：例如在测试方法中，通过junit对类进行测试，如果被测试类中用当前线程的ClassLoader则是测试类的目录，而不是被测试类的目录。

##### idea中再resources目录下简历目录用xxx.xxx.xxx不会直接生成一个一个的文档目录（xxx/xxx/xxx），而是生成一个目录名为xxx.xxx.xxx的目录文件。

###2015-04-17

##### TODO：PowerMock mock出来的方法，如何控制其调用次数。比如需要单测的方法里面对mock的方法有多次调用，如何精细空着这个调用只发生在部分次数中。

##### TODO：anyObject() 在什么情况下返回null

###2015-04-20

##### PowerMock mock静态私有void方法。
<pre><code>
    PowerMockito.spy(StaticClass.class);
    PowerMockito.doNothing().when(StaticClass.class, "privateMethod");
</code></pre>

###2015-04-21

##### PowerMock mock接口的公有方法。
<pre><code>
    public interface Pet{
        public Boolean doEating(String food);
    }
    public class Man {
        private Pet ownPet;
        pulic void feedPet(){
            ownPet.doEating();
        }
    }
    
    public class ManTest{
        @Test
        public void test_feedPet(){
            Man man = new Man();
            Class<?> clazz = Man.class;
            Field field = clazz.getDeclaredField("ownPet");
            field.setAccessible(true);
            Pet pet = PowerMockito.mock(Pet.class);//mock类
            field.set(man, pet);//注入要测试的实例中
            PowerMockito.when(pet.doEating(Matchers.<String>anyObject())).thenReturn(true);//调用条件，告诉PowerMock在哪里使用，哪里需要mock。
            man.feedPet();
            
        }
    }
</code></pre>

+ 此例中接口类是私有，没有注入方法，这里采用反射注入。如果有注入方法，则直接注入。

###2015-05-04

#### spring 设置不允许bean名字重定义。

+ spring源代码中DefaultListableBeanFactory类有一个allowBeanDefinitionOverriding属性,其默认值为true.　将这个值设置为false就解决bean名字重定义了。
　
<pre><code>
/** Whether to allow re-registration of a different definition with the same name */
private boolean allowBeanDefinitionOverriding = true;


//allowBeanDefinitionOverriding属性在下面代码中起作用:
synchronized (this.beanDefinitionMap) {
 Object oldBeanDefinition = this.beanDefinitionMap.get(beanName);
 if (oldBeanDefinition != null) {
  if (!this.allowBeanDefinitionOverriding) {
   throw new BeanDefinitionStoreException(beanDefinition.getResourceDescription(), beanName,
     "Cannot register bean definition [" + beanDefinition + "] for bean '" + beanName +
     "': There is already [" + oldBeanDefinition + "] bound.");
  }
  else {
   if (this.logger.isInfoEnabled()) {
    this.logger.info("Overriding bean definition for bean '" + beanName +
      "': replacing [" + oldBeanDefinition + "] with [" + beanDefinition + "]");
   }
  }
 }
 else {
  this.beanDefinitionNames.add(beanName);
  this.frozenBeanDefinitionNames = null;
 }
 this.beanDefinitionMap.put(beanName, beanDefinition);
}
</code></pre>

+ 解决方案１：自己写一个继承ContextLoaderListener的listener,比如SpringContextLoaderListener，然后重写方法customizeContext。并在web.xml使用自定义的listener。
<pre><code>
public class SpringContextLoaderListener extends ContextLoaderListener {
 
 @Override
 protected void customizeContext(ServletContext servletContext, ConfigurableWebApplicationContext applicationContext) {
  super.customizeContext(servletContext, applicationContext);
  
  XmlWebApplicationContext context = (XmlWebApplicationContext) applicationContext;
  context.setAllowBeanDefinitionOverriding(false); //在这里将XmlWebApplicationContext属性allowBeanDefinitionOverriding设置为false,这个属性的值最终
  //会传递给DefaultListableBeanFactory类的allowBeanDefinitionOverriding属性
 }
}
</code></pre>


<pre><code>
<listener>
      <listener-class>com.zyr.web.spring.SpringContextLoaderListener</listener-class>
</listener>
</code></pre>

+ 解决方案２：在org.springframework.web.context.ContextLoader类中找到了CONTEXT_INITIALIZER_CLASSES_PARAM常量,该常量可用于配置spring上下文相关全局特性,该常量在如下代码中起作用:

<pre><code>
protected List<Class<ApplicationContextInitializer<ConfigurableApplicationContext>>>
  determineContextInitializerClasses(ServletContext servletContext) {


 List<Class<ApplicationContextInitializer<ConfigurableApplicationContext>>> classes =
   new ArrayList<Class<ApplicationContextInitializer<ConfigurableApplicationContext>>>();


   .........


 String localClassNames = servletContext.getInitParameter(CONTEXT_INITIALIZER_CLASSES_PARAM);
 if (localClassNames != null) {
  for (String className : StringUtils.tokenizeToStringArray(localClassNames, INIT_PARAM_DELIMITERS)) {
   classes.add(loadInitializerClass(className));
  }
 }


 return classes;
}
</code></pre>


创建一个实现接口ApplicationContextInitializer的类，如SpringApplicationContextInitializer,代码如下:

<pre><code>
public class SpringApplicationContextInitializer implements ApplicationContextInitializer<XmlWebApplicationContext> {


 public void initialize(XmlWebApplicationContext applicationContext) {
  applicationContext.setAllowBeanDefinitionOverriding(false);//在这里将XmlWebApplicationContext属性allowBeanDefinitionOverriding设置为false,这个属
  //性的值最终会传递给DefaultListableBeanFactory类的allowBeanDefinitionOverriding属性
 }
}
</code></pre>

web.xml中的增加的配置如下:

<pre><code>
<context-param>
        <param-name>contextInitializerClasses</param-name>
        <param-value>com.zyr.web.spring.SpringApplicationContextInitializer</param-value>
 </context-param>
</code></pre>

缺点：如果项目在前端使用的是spring mvc时，问题还只解决了一半，即spring根容器(相对使用spring　mvc来说)如果存在同名id或者name时,容器报错停止启动.而spring mvc 的容器是作为子容器来初始化的，所以上述方案2只解决了spring根容器同名id或者name的问题，并没有解决spring mvc子容器的同名id或者name问题。

+ 解决方案３：在web.xml文件中的dispatcherServlet配置中增加如下部分配置
<pre><code>
<servlet>
 <servlet-name>dispatcherServlet</servlet-name>
 <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
 <init-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>classpath:spring/spring-mvc.xml</param-value>
 </init-param>
 <!--　增加如下配置　-->
 <init-param>　
  <param-name>contextInitializerClasses</param-name>
  <param-value>com.zyr.web.spring.SpringApplicationContextInitializer</param-value> <!--这个类与方案２中的是一个类 -->
 </init-param>
 <load-on-startup>1</load-on-startup>
</servlet>
</code></pre>


+ 网上来源：https://github.com/zgmzyr/sameIdOrNameForBeanInSpringTest.git 


###2015-05-09

#### nginx安装＋配置＋反向代理tomcat

+ 下载nginx，解压缩tar -xvf nginx.tar.gz, 进入nginx目录, 运行./configure检查配置, 运行make, 运行make install. 安装完毕

+ 安装遇到 /etc/nginx/nginx.conf, /var/log/nginx/error.log, 和/var/lib/nginx/body目录文件没有的问题。原因是因为我之前将这些目录手动删除了。解决方案：一、将解压缩后文件夹内的conf/nginx.conf复制到/etc/nginx/下。二、手动创建/var/log/nginx/目录。三、手动创建var/lib/nginx/body目录。

+ 配置文件更改:将/etc/nginx/nginx.conf文件内的
<pre>
<code>
   location / {
        root   html;
        index  index.html index.htm;
    }
</code>
</pre>
修改成:
<pre>
<code>
   location / {
        root   html;
        index  index.html index.htm;
        proxy_pass http://localhost:8080;
    }
</code>
</pre>












