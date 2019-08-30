- [java基础](#java基础)
  - [数据类型](#数据类型)
  - [按值传递](#按值传递)
  - [类型转换](#类型转换)
  - [为什么字段常用private而不是public](#为什么字段常用private而不是public)
  - [super](#super)
  - [hashCode](#hashCode)
  - [clone](#clone)
  - [BigDecimal](#BigDecimal)
  - [Arrays.asList() ](#Arrays.asList() )
  - [将数组转换为ArrayList](#将数组转换为ArrayList)
  - [将List转换为数组](#将List转换为数组)
  - [servlet相比CGI](#servlet相比CGI)
  - [redirect和forward](#redirect和forward)
  - [JSP内置对象](#JSP内置对象)
  - [getAttribute()和getParameter()](#getAttribute()和getParameter())
  - [数组扩容](#数组扩容)

# java基础
## 数据类型
> new Integer() 和 Integer.valueOf(),其中后者使用了缓存池机制，所以不会频繁创建对象。当然前提是那个值在-128~127范围

> String不可变也是有好处的，作为hashcode只有它才可以使用String pool这样就只用计算一次就可以找到索引；

  作为网络参数，它更安全，可以让你的主机不会被随便改动，被浏览器以为是别的主机发的；
  
  new String("abc")，“abc”就是类加载过程中传说中的字面量，它先会在String pool（或许就是常量池吧）创建一个指向他的字符串对象；再去堆中生成字符串对象。
  
## 按值传递
> 形参中只要参数不是内存地址意义（如数组），那么它在方法中的改变不会改变它在内存中的值，也就是只有短暂的值改变，这也是递归参数的意义

## 类型转换
> 高类型没我想象那么厉害，他就是太高傲了，实现不了隐式转换。

## 为什么字段常用private而不是public
> 因为公共意味着客户端可以修改成和你不一样，这样你如果要把它类型转换就不能用setter方法去全部替换，因为他早就和你不一样了，它不受你控制

## super
> 继承父类要重写构造方法的时候，其实没有@Override，所以super和它功能类似

## hashCode
> 之所以是*31，是因为31是奇素数，不会乘法溢出？丢失信息   31 * x == (x<<5)-x，底层是这么移动的

## clone 
> clone可以实现类的实例对象的拷贝，浅拷贝深拷贝区别在于引用是否同一个对象，但其实不提倡clone，因为复杂还要类型转换，相对直接调用构造方法来说。所以它写了
一个带实例对象的构造方法，只要把第一个作为参数去生成第二个，第二个就是用拷贝构造方法的方式去复制。

## 反射
> class.ForName(..)

## BigDecimal
> 浮点数之间差值比较会有误差不准，所以有了它。还可以设置保留几位和取值约束（m.setScale()）;double阿里巴巴推荐用new BigDecimal("0.1")或BigDecimal.
valueOf(0.1),不会失精度。

BigDecimal 主要用来操作（大）浮点数，BigInteger 主要用来操作大整数（超过 long 类型）。

## Arrays.asList() 
>  调用的其实是Arrays的内部类，用的是适配器模式，转换了接口，返回的还是数组集合，所以数组可没有add(),remove()这些方法

## 将数组转换为ArrayList
> 如果数组是要自己写的话，直接把数组的生成用在ArrayList的创建中，new ArrayList(Arrays.asList({...}) 

如果是已经有的数组，就用Stream 配合 Collector 去实现： Arrays.stream(myArray).collect(Collector.toList())

## 将List转换为数组
> 前提是它是由Arrays.asList()来的或那种类型的   l.toArray(new String[0])  必须声明相应的泛型这里说明是String[]

 集合的remove/add应该在Iterator进行，而不是foreach循环，因为前者是fail-safe,后者是fail-fast.
 
## servlet相比CGI
> common gateway interface,servlet只需要执行一个“系统进程”,多个请求只加载一个“类”。

## redirect和forward
> 登录等携带信息的用forward,不用保留信息地用redirect

## JSP内置对象
> 4个作用域 2个输出 1个配置 1个报错 1个本身页面说是相当于java的this

## getAttribute()和getParameter()
> 表单、作用域 字符串、对象

## 数组扩容
> Arrays.copyOf(storage, newCapacity);可以把storage数组扩大容量为newCapacity
