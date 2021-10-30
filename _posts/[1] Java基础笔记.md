# JAVA基础笔记



**获取当前线程名：**Thread.currentThread().getName();
**返回线程优先级：**getPriority();
**更改线程优先级：**setPriority(int newPriority);   1~10

#### 线程控制

​	join() ：等待这个线程死亡（调用此方法的线程结束后其他线程才可执行）
​	setDaemon(boolean on) ：标记为守护线程，当线程运行的所有线程均为守护线程时java虚拟机将退出
​				既主线程执行完毕后守护线程要跟着结束

#### Runnable接口

​	定义：class MyRunnable implements Runnable {}
​	运行：MyRunnable mr = new MyRunnable();
​	      Thread t1 = new Thread(mr, "线程名");
​	      t1.start();

#### 线程同步

​	同步代码块：synchronized(任意对象) {}

#### InetAddress

​	InetAddress address = InetAddress.getByName("ROG-S7V");	//确定主机名称的ip地址
​	String name = address.getHostName();			//获取此ip地址的主机名
​	String ip = address.getHostAddress();			//返回文本显示的ip地址

#### UDP发送数据

​	DatagramSocket ds = new DatagramSocket();   //创建发送端的socket对象
​	//DatagramPacket (byte[] buf, int length. InetAddress address, int port)
​	byte[] bys = "hello udp".getBytes();
​	DatagramPacket dp = new DatagramPacket(bys, bys.length, InetAddress.getByName("ROG-S7V"), 10086);
​	ds.send(dp);      //发送数据报包
​	ds.close();

#### UDP接收数据

​	DatagramSocket ds = new DatagramSocket(10086);
​	byte[] bys = new byte[1024];
​	DatagramPacket dp = new DatagramPacket(bys, bys.length);
​	ds.receive(dp);
​	byte[] datas = dp.getData();
​	String dataString = new String(datas, 0, dp.getLength());
​	System.out.println("数据是：" + dataString);
​	ds.close();

#### TCP发送数据

​	Socket s = new Socket(InetAddress.getByName("ROG-S7V"), 10000);
​	OutputStream os = s.getOutputStream();
​	os.write("hello, tcp".getBytes());
​	s.close();

#### TCP接收数据

​	ServerSocket ss = new ServerSocket(10000);
​	Socket s = ss.accept(); //监听到有连接就自动生成一个Socket对象
​	InputStream is = s.getInputStream();
​	byte[] bys = new byte[1024];
​	int len = is.read(bys);
​	String data = new String(bys, 0, len);
​	System.out.println("数据是" + data);
​	s.close();
​	ss.close();

#### 输入输出结束标记

​	s.shutdownInput();
​	s.shutdownOutput();

#### 多线程上传文件

```java
public class ServerDemo {
    public static void main(String[] args) throws Exception {
        ServerSocket ss = new ServerSocket(10005);
        while (true) {
            Socket s = ss.accept();
            new Thread(new ServerThread(s)).start();
        }
    }
}
public class ServerThread implements Runnable {
    private Socket s;
    public ServerThread(Socket s) {
        this.s = s;
    }
```

```java
@Override
public void run() {
    try {
        BufferedReader br = new BufferedReader(new InputStreamReader(s.getInputStream()));
        //BufferedWriter bw = new BufferedWriter(new FileWriter("D:\\JetBrains\\IDEA_Projects\\IDEA_demo_20.8.6\\Copy.java"));
        //解决名称冲突
        int count = 0;
        File file = new File("D:\\JetBrains\\IDEA_Projects\\IDEA_demo_20.8.6\\Copy[" + count + "].java");
        while (file.exists()) {
            count++;
            file = new File("D:\\JetBrains\\IDEA_Projects\\IDEA_demo_20.8.6\\Copy[" + count + "].java");
        }
        BufferedWriter bw = new BufferedWriter(new FileWriter(file));

        String line;
        while ((line = br.readLine()) != null) {
            bw.write(line);
            bw.newLine();
            bw.flush();
        }

        //反馈
        BufferedWriter bwServer = new BufferedWriter(new OutputStreamWriter(s.getOutputStream()));
        bwServer.write("上传成功");
        bwServer.newLine();
        bwServer.flush();

        s.close();
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```


#### Lambda表达式

​	组成Lambda三要素：形参、箭头、表达式
​	格式：( 形参 ) -> { 代码块 }
​	使用前提：有一个接口，接口中有且仅有一个抽象方法
​	例子：
​	new Thread( () -> {
​		System.out.println("多线程程序启动");
​	} ).start();

#### 接口中的默认方法

​	不强制被重写，可以有方法体，主要用于实现接口升级而不破坏现有代码
​	public interface MyInterFace {
​		default void show3() { ... }
​	}

#### 接口中的静态方法

​	只能被接口调用
​	范例：static void show() { ... }
​	调用：MyInterFace.show();

#### 接口中的私有方法

​	主要用于抽取出一个共性方法
​	默认方法可以调用私有的静态方法和非静态方法，静态方法只能调用私有静态方法
​	范例：private void show() {}     /     private static void method() {}

#### 方法引用

​	引用符为 ::
​	范例：userPrint(s -> System.out.println(s));   可改为   usePrint(System.out::println);

#### 函数式接口：

​	有且仅有一个抽象方法的接口，声明：@FunctionalInterface

#### 常用函数式接口

​	Supplier：内容供应商
​		包含一个无参方法 T get() 来获得结果
​		该方法不需要参数，会按照Lambda实现返回一个数据库，Supplier<T>泛型指定返回类型
​		范例：private static String getString(Supplier<String> sup) { return sup.get(); }
​			String s = getString(() -> "字符串");

#### Stream流

​	Stream流常用于过滤数据
​	常见生成方法：
​		1.Collection体系集合可用.stream()生成，如：list.stream();   set.stream();
​		2.Map体系间接生成，如：map.keySet().stream();   map.valueSet().stream();   map.entrySet().stream();
​		3.数组可用Stream的静态方法of，如：Stream.of(strArray);

```java
list.stream().filter(s -> s.startsWith("张"))
        .filter(s -> s.length() == 3)
        .forEach(System.out::println);
```



​	常见中间操作方法：

​		filter()：对流中的数据进行过滤

​		limit()：截取前几个元素

​		skip()：跳过几个元素

​		concat()：合并a和b两个流为一个流

​		distinct()：返回由该流的不同元素组成的流（去重）

​		sorted()：自然排序

​		sorted(Comparator comparator)：根据Comparator进行排序

​		map()：返回由给定函数应用与此流的元素结果组成的流（如转化为int）

​		mapToInt(ToIntFunction mapper)：返回一个IntStream包含给定函数的元素结果

```java
list.stream().skip(3).limit(3).forEach(System.out::println);
Stream.concat(s1, s2).distinct();

//排序
list.stream().sorted( (s1, s2) -> {
    int num1 = s1.length() - s2.length();
    int num2 = (num == 0) ? s1.compareTo(s2) : num;
    return num2;
} ).forEach(System.out::println);

//map
list.stream().map(Integer::paseInt).forEach(System.out::println);
//仅IntStream包含sum()方法
int resuklt = list.stream().mapToInt(Integer::paseInt).sum();
```



​	常见终结操作方法：

​		forEach()：对流的每个元素进行操作

​		count()：返回元素总数



​	综合应用：

```java
Stream.concat(manList.stream().filter(s -> s.length() == 3).limit(3),
             womanList.stream().filter(s -> s.startsWith("林")).skip(1).map(Actor::new).
             forEach(p -> System.out.println(p.getName()))
);
```



​	Stream流的收集操作：

```java
listStream.collect(Collectors.toList());	//转换为List集合
setStream.collect(Collectors.toSet());		//转换为Set集合
//得到字符串中年龄大于28的流并收集到Map集合中
Stream<String> arrayStream = Stream.of(strArray).filter(s -> Integer.parseInt(s.split(",")[1]) > 28);
Map<String, Integer> map = arrayStream.collect(Collectors.toMap(s -> s.split(",")[0], s -> Integer.parseInt(s.split(",")[1])));
```



#### 反射

​	获取Class类的对象：

```java
Student.class	//多用于对象获取字节码的方式
Student s = new Student();	s.getGlass();	//多用于参数传递
Class.forName("com.xxx.Student")			//多用于配置文件
```



​	反射获取构造方法并使用：

​		Class类中用于获取构造方法的方法：

```java
getConstructors()			//返回所有公共构造方法对象的数组
getDeclaredConstructors()	//返回所有构造方法对象的数组
getConstructor()			//返回单个公共构造方法对象
getDeclaredConstructor()	//返回单个构造方法对象
```

​		Constructor类中用于创建对象的方法：

```java
newInstance()				//根据指定的构造方法创建对象
personClass.newInstance()	//简化操作，适用于空参构造方法
```

​		范例：

```java
Class<?> c = Class.forName("com.ithm02.Student");
//基本数据类型也可用过.class得到对应Class类型
Constructor<?> con = c.getConstructor(String.class, int.class, String.class);
Object obj = con.newInstance("林青霞", 30, "西安");
System.out.println(obj);
```



​	访问私有构造方法时会出现异常，而暴力反射可以取消访问检查：

```java
con.setAccessible(true);	//值为true，取消访问检查
```



​	反射获取成员变量并使用：

```java
getFields()						//返回所有公共成员变量对象的数组
getDeclaredFields()				//返回所有成员对象的数组
getField(String name)			//返回单个公共成员变量对象
getDeclaredField(String name)	//返回单个成员变量对象
```

​	Field类中用于给成员变量赋值的方法

```java
void set(Object obj, Object value)		//给obj对象的成员变量赋值为value
```



​	反射获取成员方法：

```java
getMethods()			//返回所有公共成员方法对象的数组，包括继承的
getDeclaredMethods()	//返回所有成员方法对象的数组，不包括继承的
getMethod(String name, Class<?> ... parameterTypes)				//返回单个公共成员方法对象
getDeclaredMethod(String name, Class<?> ... parameterTypes)		//返回单个成员方法对象
```

​	Method类中用于调用成员方法的方法：

```java
Object invoke(Object obj, Object args)	//调用obj对象的成员方法，参数为args，返回值为Object类型
```



范例：反射可绕过泛型检查

```java
ArrayList<Integer> array = new ArrayList<Integer>();
Class<? extends ArrayList> c = array.getClass();
Method m = c.getMethod("add", Object.class);
m.invoke(array,"HelloWorld!");
System.out.println(array);
```



范例：反射运行配置文件指定内容

```java
Properties prop = new Properties();
/*
	ClassLoader classLoader = ReflectTest.class.getClassLoader();
	InputStream is = classLoader.getResourceAsStream("class.txt");
	prop.load(is);
*/
FileReader fr = new FileReader("class.txt");
prop.load(fr);
fr.close();
/*
    className=com.ithm04.Student
    methodName=study
 */
String className = prop.getProperty("className");
String methodName = prop.getProperty("methodName");
Class<?> c = Class.forName(className);
Constructor<?> con = c.getConstructor();
Object obj = con.newInstance();
Method m = c.getMethod(methodName);
m.invoke(obj);
```



#### 模块的基本使用

​	需求jdk：java9 以上

​	用法：

​		两个模块的 src 目录下新建 module-info.java 的文件，需要被引用的写上：

```java
module myOne {
    exports com.ithm01;
}
```

​		引用者写上：

```java
module MyTwo {
    requires myOne;
}
```



#### 模块服务的基本使用（面向接口编程）

​	需求jdk：java9 以上

​	用法：

​		服务提供者首先编写接口：

```java
public interface MyService {
    void service();
}
```

​		接着编写服务实现类：

```java
public class Czxy implements MyService {
    @Override
    public void service() {
        System.out.println("传智学院");
    }
}
```

​		module-info 中声明实现类

```java
module myOne {
    exports com.ithm03;
    provides MyService with Czxy;	//指定外界使用 MyService 的实现类为 Czxy
}
```

​		使用者的 module-info 中使用 uses 声明：

```java
module MyTwo {
    requires myOne;
    uses MyService;
}
```

​		通过 ServiceLoader 使用服务：

```java
ServiceLoader<MyService> myServices = ServiceLoader.load(MyService.class);
for (MyService my : myServices) {
    my.service();
}
```

