#                             Java关键字及其含义
## 一、关键字总览
![这里写图片描述](http://img.blog.csdn.net/20170710142346319?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY29sbkdF/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
## 二、含义概述
####　　　　　　　　　　　　　      　1、访问控制（3）
　　**a、public（谁都可用）** 
　　public表示共有属性或者方法。 “公共的”，意思就是说，如果一个类被声明为public那么别的类就可以访问它和它里面的方法。需要注意的一点是，**如果一个类被声明为public，那么源文件必须以该类命名。**另外，**一个源文件里面有且只能有一个公共类。**
　　
　　**b、protected（同一个包、子女类可用）**
　　可以在声明 protected 类、方法或字段的类、同一个包中的其他任何类以及任何子类（无论子类是在哪个包中声明的）中引用这些类、方法或字段。
　　
　　**c、private（只有自己能用）**
　　只能在声明 private（内部）类、方法或字段的类中引用这些类、方法或字段。在类的外部或者对于子类而言，它们是不可见的。
　　　　　　　　　　　　　　　　　　　
####　　　　　　　　　　　　２、类、方法和变量修饰符（12）
　　**ａ、abstrcat**　　　　       
　　abstrcat是抽象的意思，用于定义抽象类，抽象函数。抽象类的用法规则如下：
　　1，用abstract关键字来修饰一个类时，这个类叫做抽象类；用abstract来修饰一个方法时，这个方法叫做抽象方法；
　　2，含有抽象方法的类必须被声明为抽象类，抽象类必须被继承，抽象方法必须被重写；
　　3，abstract类可以扩展（增加子类），但不能直接实例化；

　　**b、interface**　
　　用来声明新的 Java 接口，接口是方法的集合。
　　
　　**c、static**　
　　用于定义静态成员变量或方法，也就是成为附属于类的变量或方法。因此可以从类外部直接调用，而不用先实例化该类。类的成员表量在类的所有实例中只存在一个。
　　Java中有一个不好的地方，就是它居然允许实例变量去调用静态变量和方法。这是很容易引起初学者混淆的地方。其实，**通过实例调用到最后都会归结为通过类去调用这些静态变量或方法，因此多个实例调用的静态方法其实都是一样的！**
　　
　　**d、final(最终，不可改变)**
　　final 关键字可以应用于类，以指示不能扩展该类（不能有子类）。final 关键字可以应用于方法，以指示在子类中不能重写此方法。abstract 意味着必须扩展类，final 意味着不能扩展类。一个方法不能同时是 abstract 又是 final。因此，一个类不能同时是 abstract 又是 final。
　　
　　**e、extends**
　　extends 关键字用在 class 或 interface 声明中，用于指示所声明的类或接口。类继承类，或接口继承接口。 子类可以重写父类的任何非 final 方法。一个类只能扩展一个其他类。
　　
　　**f、strictfp**
　　strictfp的意思是FP-strict，是精确浮点的意思。在Java虚拟机进行浮点运算时，如果没有指定strictfp关键字时，Java的编译器以及运行环境在对浮点运算的表达式是采取一种我行我素的行为来完成这些操作，以致于得到的结果往往无法令人满意。而一旦使用了strictfp来声明一个类、接口或者方法时，那么所声明的范围内Java的编译器以及运行环境会完全依照浮点规范IEEE-754来执行。因此如果想让浮点运算更加精确，而且不会因为不同的硬件平台所执行的结果不一致的话，那就请用关键字strictfp。
　　可以将一个类、接口以及方法声明为strictfp，但是不允许对接口中的方法以及构造函数声明strictfp关键字。
　　
　　**g、new**
　　new用于新建一个对象，即类的实例化。new 关键字后面的参数必须是类名，并且类名的后面必须是一组构造方法参数（必须带括号）。
　　
　　**h、implements**
　　通过类来实现接口，并且需要在这个类中将所实现接口中所有的抽象方法实现出来。一个类可以实现多个接口。
　　
　　**i、synchronized**
　　锁定当前对象，在执行当前这个对象时不应许其他线程打断插入,为一次只应由一个线程执行的关键代码段提供保护。

 - 如果应用于静态方法，那么，当该方法一次由一个线程执行时，整个类将被锁定。
 - 如果应用于实例方法，那么，当该方法一次由一个线程访问时，该实例将被锁定。
 - 如果应用于对象或数组，当关联的代码块一次由一个线程执行时，对象或数组将被锁定。
 
 　以下是synchronized应用多线程的一个例子
```
pub·lic class Demo1 {


    public static void main(String[] args) {
        Example example = new Example();        

        Thread t1 = new TheThread(example);     

        Thread t2 = new TheThread2(example);

        t1.start();//由于对于同一个实例对象加了锁，所以t2会等t1执行完时才继续执行

        t2.start();

    }
}

class Example {

    public synchronized void execut() {//两个方法需要同时为非静态

        for (int i = 0; i < 5; i++) {

            try {
                Thread.sleep((long) (Math.random() * 1000));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println("hello: " + i);
        }
    }

    public synchronized void execute2() {//两个方法需要同时为非静态
        for (int i = 0; i < 5; i++) {
            try {
                Thread.sleep((long) (Math.random() * 1000));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println("world: " + i);
        }
    }
}

class TheThread extends Thread {

    private Example example;

    public TheThread(Example example) {//将实例传入
                                     //所以对于静态方法同非静态方法的加锁不同
        this.example = example;
    }

    @Override
    public void run() {
        this.example.execut();//通过实例调用方法执行
    }
}

class TheThread2 extends Thread {

    private Example example;

    public TheThread2(Example example) {
        this.example = example;
    }

    @Override
    public void run() {
        this.example.execute2();//通过实例调用方法执行
    }
}
```
　　
　　![这里写图片描述](http://img.blog.csdn.net/20170710190916158?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY29sbkdF/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
　　
　　注意：**被加锁的是对象，而不是方法。所以，如果两个方法不同时是静态方法或不同时是非静态方法的话就没法达到加锁的目的。**
　　运行效果如下：
　　![这里写图片描述](http://img.blog.csdn.net/20170710191619738?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY29sbkdF/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
　　
　　可以看出，synchronized的作用就是将一个对象加锁，执行当前这个对象时不应许其他线程打断插入,为一次只应由一个线程执行的关键代码段提供保护。
　　
　　**j、volatile**
　　volatile 关键字一个时间内只有一个线程可以访问，但可以被多个线程异步修改，以确保所有线程所看到的指定变量的值都是相同的。
　　volatile变量可以被看作是一种“程度较轻的synchronized”;与synchronized块相比,volatile变量所需的编码较少，并且运行时开销也较少，但是它所能实现的功能也仅是synchronized的一部分。
　　下面，是volatile的**特性**：
　　1、保证可见性
　　当两个线程同时对一个对象进行操作时，如果线程1修改了这个对象的值，那么线程2的工作内存中的缓存变量会无效，从而需要去主存中重新读取。所以volatile保证了可见性
　　
　　2、volatile不能确保原子性
　　仍然以上面的例子来讲，在进行自增操作之后线程1中的工作内存中变量不会改变，线程2中也不会，但是在拿到结果时这个变量的值却是会加1的。所以这就造成了上面所说的可见性失效。所以volatile不能保证原子可见性
　　
　　3、volatile保证原子有序性
　　使用volatile关键字修饰过的变量，能够保证在其前的代码都在它这个变量操作之前执行。之后的代码都在对这个变量操作之后执行。
　　
　　下面，是volatile的**应用实例**：
　　1、状态标记量

　　2、单例模式中的double check　　
　　**k、class**
　　class 关键字用来声明新的 Java 类，该类是相关变量和/或方法的集合。要使用类，通常使用 new 操作符将类的对象实例化，然后调用类的方法来访问类的功能。
　　**l、transient**
　　transient 关键字可以应用于类的成员变量，以便指出该成员变量不应在包含它的类实例已序列化时被序列化。
####　　　　　　　　　　　　3、程序控制语句（12）
　　**a、if**
　　如果满足条件则进入其代码块
　　
　　**b、else**
　　如果不满足if语句条件则进入其代码块
　　
　　**c、while**
　　条件只要为真就会重复的循环
　　
　　**d、do**
　　用于指定一个在每次迭代结束时检查其条件的循环
　　
　　**e、for**
　　用于指定一个在每次迭代结束前检查其条件的循环
　　
　　**f、continue**
　　用来跳转到 for、while 或 do 循环的下一个迭代。注意点：** 总是跳到最深层 while、for 或 do 语句的下一个迭代。**
　　
　　**g、break**
　　用于提前退出 for、while 或 do 循环，或者在 switch 语句中用来结束 case 块。**总是退出最深层的 while、for、do 或 switch 语句**
　　
　　**h、switch**
　　用于基于某个表达式选择执行多个代码块中的某一个。
　　
　　**i、case**
　　判断switch中的每个分支。符合则进入其代码块
　　
　　**j、default**
　　用来标记 switch 语句中的默认分支。**如果没有 default 语句，其参数与任何 case 块都不匹配的 switch 语句将不执行任何操作。**
　　
　　**k、return**
　　return关键字会导致方法返回到调用它的方法，从而传递与返回方法的返回类型匹配的值。
　　
　　**l、instanceof**
　　用来判断对象所属的类
####　　　　　　　　　　　　4、错误处理语句（12）
　　**a、try**
　　用于放置可能会发生异常的语句块。每个 try 块都必须至少有一个 catch 或 finally 子句。
如果某个特定异常类未被任何 catch 子句处理，该异常将沿着调用栈递归地传播到下一个封闭 try 块。如果任何封闭 try 块都未捕获到异常，Java 解释器将退出，并显示错误消息和堆栈跟踪信息。
　　
　　**b、catch**
　　catch中的代码块用于抓住异常，{}定义当出现异常时的处理方法。
　　
　　**c、throw**
　　用于抛出用户自己定义的异常
　　
　　**d、throws**
　　声明一个系统异常可能被抛出。应用于方法，以指出方法引发了特定类型的异常。
####　　　　　　　　　　　　5、包相关（2）
　　**a、import**
　　引入包名或者包中的某个对象
　　
　　**b、package**
　　如果 Java 源文件不包含 package 语句，在该文件中定义的类将位于“默认包”中。请注意，不能从非默认包中的类引用默认包中的类。如果要引用，需要import导入。
####　　　　　　　　　　　　6、基本类型（11）　
　　![这里写图片描述](http://img.blog.csdn.net/20170711081319714?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY29sbkdF/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
　　
####　　　　　　　　　　　　7、变量引用（3）
　　**a、this**
　　是指向该对象自身的标示符　
　　
　　**b、super**
　　调用基类的构造函数。只有在如下情况中才需要采用这种用法：要调用在该类中被重写的方法，以便指定应当调用在超类中的该方法。
　　
　　**c、void**
　　可以用作方法的返回类型，以指示该方法返回值为空
####　　　　　　　　　　　　8、保留字（3）　
　　**a、goto**　
　　C\C++中实现无条件转向语句，goto 语句的使用往往会使程序的可读性降低，且为了结构化程序设计，java中现在一般不用。
　　
　　**b、const**　
　　在Java中,const是作为保留字以备扩充。可以用final替换const，一般C++中用const，java中用final。
　　**c、native**
　　Java不是完美的，Java的不足除了体现在运行速度上要比传统的C++慢许多之外，Java无法直接访问到操作系统底层（如系统硬件等)，为此Java使用native方法来扩展Java程序的功能。
　　
　　可以将native方法比作Java程序同Ｃ程序的接口，其实现步骤：
　　１、在Java中声明native()方法，然后编译；
　　２、用javah产生一个.h文件；
　　３、写一个.cpp文件实现native导出方法，其中需要包含第二步产生的.h文件（注意其中又包含了JDK带的jni.h文件）；
　　４、将第三步的.cpp文件编译成动态链接库文件；
　　５、在Java中用System.loadLibrary()方法加载第四步产生的动态链接库文件，这个native()


----------
　　至此，Java的50个关键字就介绍完了。对于关键字的准确把握，能够使我们对Java基础理解得更加的透彻，可以说，益处多多。
　　
　　
　　参考文章：http://blog.csdn.net/cqboy1991/article/details/36423967
　　　　　　　http://blog.csdn.net/luoweifu/article/details/6776240#t6
　　　　　　　http://blog.csdn.net/jj___jj/article/details/5589415
　　

 

　　　　






　　