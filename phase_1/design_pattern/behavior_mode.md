## 一.行为型模式概述：

行为型模式(Behavioral Pattern)是对在不同的对象之间划分责任和算法的抽象化。

行为型模式不仅仅关注类和对象的结构，而且重点关注它们之间的相互作用。

通过行为型模式，可以更加清晰地划分类与对象的职责，并研究系统在运行时实例对象 之间的交互。在系统运行时，对象并不是孤立的，它们可以通过相互通信与协作完成某些复杂功能，一个对象在运行时也将影响到其他对象的运行。

行为型模式分为类行为型模式和对象行为型模式两种：

- 类行为型模式：类的行为型模式使用继承关系在几个类之间分配行为，类行为型模式主要通过多态等方式来分配父类与子类的职责。
- 对象行为型模式：对象的行为型模式则使用对象的聚合关联关系来分配行为，对象行为型模式主要是通过对象关联等方式来分配两个或多个类的职责。根据“合成复用原则”，系统中要尽量使用关联关系来取代继承关系，因此大部分行为型设计模式都属于对象行为型设计模式。

**包含模式**

- 策略模式(Strategy)、模板方法模式(Template Method)	
- 观察者模式(Observer)、	迭代器模式(Iterator)、职责链模式(Chain of Responsibility)、命令模式(Command)
- 备忘录模式(Memento)、状态模式(State)
- 访问者模式(Visitor)、中介者模式(Mediator)、解释器模式(Interpreter)

## 二.行为型设计模式详解，按照以下格式展开介绍

策略模式：

#### 1.UML类图

​		策略模式定义了一系列算法，并将每个算法封装起来，使他们可以相互替换，且算法的变化不会影响到使用算法的客户。需要设计一个接口，为一系列实现类提供统一的方法，多个实现类实现该接口，设计一个抽象类（可有可无，属于辅助类），提供辅助函数，关系图如下：

![](http://img.mukewang.com/5770eece0001573a05600260.png)

#### 2.代码示例

```java
public interface ICalculator {  
    public int calculate(String exp);  
}
public abstract class AbstractCalculator {  

    public int[] split(String exp,String opt){  
        String array[] = exp.split(opt);  
        int arrayInt[] = new int[2];  
        arrayInt[0] = Integer.parseInt(array[0]);  
        arrayInt[1] = Integer.parseInt(array[1]);  
        return arrayInt;  
    }  
}

public class Plus extends AbstractCalculator implements ICalculator {  

    @Override  
    public int calculate(String exp) {  
        int arrayInt[] = split(exp,"\\+");  
        return arrayInt[0]+arrayInt[1];  
    }  
}  

public class Minus extends AbstractCalculator implements ICalculator {  

    @Override  
    public int calculate(String exp) {  
        int arrayInt[] = split(exp,"-");  
        return arrayInt[0]-arrayInt[1];  
    }  

}  

public class Multiply extends AbstractCalculator implements ICalculator {  

    @Override  
    public int calculate(String exp) {  
        int arrayInt[] = split(exp,"\\*");  
        return arrayInt[0]*arrayInt[1];  
    }  
}

public class 策略模式 {  

    public static void main(String[] args) {  
        String exp = "2+8";  
        ICalculator cal = new Plus();  
        int result = cal.calculate(exp);  
        System.out.println(result);  
    }  
}
```

#### 

#### 3.使用场景

1、 多个类只区别在表现行为不同，可以使用Strategy模式，在运行时动态选择具体要执行的行为。

2、 需要在不同情况下使用不同的策略(算法)，或者策略还可能在未来用其它方式来实现。

3、 对客户隐藏具体策略(算法)的实现细节，彼此完全独立。

#### 4.优缺点

**优点：**

- 1、结构清晰，把策略分离成一个个单独的类「替换了传统的 if else」
- 2、代码耦合度降低，安全性提高「各个策略的细节被屏蔽」

**缺点：**

- 1、客户端必须要知道所有的策略类，否则你不知道该使用那个策略，所以策略模式适用于提前知道所有策略的情况下
- 2、策略类数量增多（每一个策略类复用性很小，如果需要增加算法，就只能新增类）





模板方法模式：

#### 1.UML类图

​	一个抽象类中，有一个主方法，再定义1...n个方法，可以是抽象的，也可以是实际的方法，定义一个类，继承该抽象类，重写抽象方法，通过调用抽象类，实现对子类的调用，先看个关系图：

![图片描述](http://img.mukewang.com/5770f0570001133a04780205.png)

#### 2.代码示例

```java
public abstract class AbstractCalculator {  

    /*主方法，实现对本类其它方法的调用*/  
    public final int calculate(String exp,String opt){  
        int array[] = split(exp,opt);  
        return calculate(array[0],array[1]);  
    }  

    /*被子类重写的方法*/  
    abstract public int calculate(int num1,int num2);  

    public int[] split(String exp,String opt){  
        String array[] = exp.split(opt);  
        int arrayInt[] = new int[2];  
        arrayInt[0] = Integer.parseInt(array[0]);  
        arrayInt[1] = Integer.parseInt(array[1]);  
        return arrayInt;  
    }  
}  

public class Plus extends AbstractCalculator {  

    @Override  
    public int calculate(int num1,int num2) {  
        return num1 + num2;  
    }  
}
public class Minus extends AbstractCalculator {

    @Override
    public int calculate(int num1,int num2) {
        return num1 - num2;
    }
}

public class 模板方法模式 {

    public static void main(String[] args) {
        String exp = "8-8";
        AbstractCalculator cal = new Minus ();
        int result = cal.calculate(exp, "\\-");
        System.out.println(result);
    }
}
```



#### 3.使用场景

当系统中算法的骨架是固定的时候，而算法的实现可能有很多种的时候，就需要使用模板方法模式。

- 多个子类有共有的方法，并且逻辑基本相同
- 重要、复杂的算法，可以把核心算法设计为模板方法，周边的相关细节功能则由各个子类实现
- 重构时，模板方法是一个经常使用的方法，把相同的代码抽取到父类中，然后通过构造函数约束其行为。

#### 4.优缺点

**优点：**

- 封装不变部分，扩展可变部分。把认为不变部分的算法封装到父类中实现，而可变部分的则可以通过继承来继续扩展。
- 提取公共部分代码，便于维护。
- 行为由父类控制，子类实现

**缺点：**

算法骨架需要改变时需要修改抽象类。

按照设计习惯，抽象类负责声明最抽象、最一般的事物属性和方法，实现类负责完成具体的事务属性和方法，但是模板方式正好相反，子类执行的结果影响了父类的结果，会增加代码阅读的难度。





观察者模式:

#### 1.UML类图

​		观察者模式很好理解，类似于邮件订阅和RSS订阅，当我们浏览一些博客或wiki时，经常会看到RSS图标，就这的意思是，当你订阅了该文章，如果后续有更新，会及时通知你。其实，简单来讲就一句话：当一个对象变化时，其它依赖该对象的对象都会收到通知，并且随着变化！对象之间是一种一对多的关系。先来看看关系图：

![图片描述](http://img.mukewang.com/5770f2130001982006260380.png)

#### 2.代码示例

```java
public interface Observer {  
    public void update();  
}
public class Observer1 implements Observer {  

    @Override  
    public void update() {  
        System.out.println("observer1 has received!");  
    }  
}  

public class Observer2 implements Observer {  

    @Override  
    public void update() {  
        System.out.println("observer2 has received!");  
    }  

}

public interface Subject {  

    /*增加观察者*/  
    public void add(Observer observer);  

    /*删除观察者*/  
    public void del(Observer observer);  

    /*通知所有的观察者*/  
    public void notifyObservers();  

    /*自身的操作*/  
    public void operation();  
}  

public abstract class AbstractSubject implements Subject {  

    private Vector<Observer> vector = new Vector<Observer>();  
    @Override  
    public void add(Observer observer) {  
        vector.add(observer);  
    }  

    @Override  
    public void del(Observer observer) {  
        vector.remove(observer);  
    }  

    @Override  
    public void notifyObservers() {  
        Enumeration<Observer> enumo = vector.elements();  
        while(enumo.hasMoreElements()){  
            enumo.nextElement().update();  
        }  
    }  
}  

public class MySubject extends AbstractSubject {  

    @Override  
    public void operation() {  
        System.out.println("update self!");  
        notifyObservers();  
    }  

}


public class 观察者模式 {

    public static void main(String[] args) {
        Subject sub = new MySubject();
        sub.add(new Observer1());
        sub.add(new Observer2());
    
        sub.operation();
    }
}
```



#### 3.使用场景

1、对一个对象状态的更新，需要其他对象同步更新，而且其他对象的数量动态可变。

2、对象仅需要将自己的更新通知给其他对象而不需要知道其他对象的细节。

#### 4.优缺点

优点：易扩展

缺点：观察者较多时性能可能会下降(由于是链式触发)





迭代器模式：

#### 1.UML类图

顾名思义，迭代器模式就是顺序访问聚集中的对象，一般来说，集合中非常常见，如果对集合类比较熟悉的话，理解本模式会十分轻松。这句话包含两层意思：一是需要遍历的对象，即聚集对象，二是迭代器对象，用于对聚集对象进行遍历访问。我们看下关系图：

![图片描述](http://img.mukewang.com/5770f3840001e4ea06400344.png)

#### 2.代码示例

```java
public interface Collection {  

    public Iterator iterator();  

    /*取得集合元素*/  
    public Object get(int i);  

    /*取得集合大小*/  
    public int size();  
}  

public interface Iterator {  
    //前移  
    public Object previous();  

    //后移  
    public Object next();  
    public boolean hasNext();  

    //取得第一个元素  
    public Object first();  
}


public class MyCollection implements Collection {  

    public String string[] = {"A","B","C","D","E"};  
    @Override  
    public Iterator iterator() {  
        return new MyIterator(this);  
    }  

    @Override  
    public Object get(int i) {  
        return string[i];  
    }  

    @Override  
    public int size() {  
        return string.length;  
    }  
}  

public class MyIterator implements Iterator {  

    private Collection collection;  
    private int pos = -1;  

    public MyIterator(Collection collection){  
        this.collection = collection;  
    }  

    @Override  
    public Object previous() {  
        if(pos > 0){  
            pos--;  
        }  
        return collection.get(pos);  
    }  

    @Override  
    public Object next() {  
        if(pos<collection.size()-1){  
            pos++;  
        }  
        return collection.get(pos);  
    }  

    @Override  
    public boolean hasNext() {  
        if(pos<collection.size()-1){  
            return true;  
        }else{  
            return false;  
        }  
    }  

    @Override  
    public Object first() {  
        pos = 0;  
        return collection.get(pos);  
    }  

}


public class 迭代器模式 {

    public static void main(String[] args) {
        Collection collection = new MyCollection();
        Iterator it = collection.iterator();

        while(it.hasNext()){
            System.out.println(it.next());
        }
    }
}
```



#### 3.使用场景

- 访问一个集合对象的内容而无需暴露它的内部表示
- 为遍历不同的集合结构提供一个统一的接口

#### 4.优缺点

**优点：**

- 迭代器模式使得访问一个聚合对象的内容而无需暴露它的内部表示，即迭代抽象。
- 迭代器模式为遍历不同的集合结构提供了一个统一的接口，从而支持同样的算法在不同的集合结构上进行操作

**缺点：**

- 迭代器模式在遍历的同时更改迭代器所在的集合结构会导致出现异常。所以使用foreach语句只能在对集合进行遍历，不能在遍历的同时更改集合中的元素。





责任链模式：

#### 1.UML类图

接下来我们将要谈谈责任链模式，有多个对象，每个对象持有对下一个对象的引用，这样就会形成一条链，请求在这条链上传递，直到某一对象决定处理该请求。但是发出者并不清楚到底最终那个对象会处理该请求，所以，责任链模式可以实现，在隐瞒客户端的情况下，对系统进行动态的调整。先看看关系图：

![图片描述](http://img.mukewang.com/5770f4a8000189a304860287.png)

#### 2.代码示例

```java
public interface Handler {  
    public void operator();  
}  

public abstract class AbstractHandler {  

    private Handler handler;  

    public Handler getHandler() {  
        return handler;  
    }  

    public void setHandler(Handler handler) {  
        this.handler = handler;  
    }  

}  

public class MyHandler extends AbstractHandler implements Handler {  

    private String name;  

    public MyHandler(String name) {  
        this.name = name;  
    }  

    @Override  
    public void operator() {  
        System.out.println(name+"deal!");  
        if(getHandler()!=null){  
            getHandler().operator();  
        }  
    }  
}  

public class 责任链模式 {  

    public static void main(String[] args) {  
        MyHandler h1 = new MyHandler("h1");  
        MyHandler h2 = new MyHandler("h2");  
        MyHandler h3 = new MyHandler("h3");  

        h1.setHandler(h2);  
        h2.setHandler(h3);  

        h1.operator();  
    }  
}
```



#### 3.使用场景

一个 请求 的 处理 , 需 要多个对象 中的 一个或若干个对象 协作进行处理 ;

#### 4.优缺点

优点 :

解耦 : 请求的 发送者 和 接收者 解耦 ; 接收者 是 请求的处理者 ;

动态组合 : 责任链 可以 动态组合 , 使用配置 设置责任链的 顺序及 是否出现 ; 可以随时对责任链排序 , 随时增加拆除责任链中的某个请求对象 ;


缺点 :

性能 : 如果 责任链 太长 , 或责任链中请求的 处理时间过长 , 可能会 影响性能 ;

个数 : 责任链 可能过多 ;





命令模式:

#### 1.UML类图

命令模式很好理解，举个例子，司令员下令让士兵去干件事情，从整个事情的角度来考虑，司令员的作用是，发出口令，口令经过传递，传到了士兵耳朵里，士兵去执行。这个过程好在，三者相互解耦，任何一方都不用去依赖其他人，只需要做好自己的事儿就行，司令员要的是结果，不会去关注到底士兵是怎么实现的。我们看看关系图：

![图片描述](http://img.mukewang.com/5770f5f00001881a06090234.png)

#### 2.代码示例

```java
public interface Command {  
    public void exe();  
}  

public class MyCommand implements Command {  

    private Receiver receiver;  

    public MyCommand(Receiver receiver) {  
        this.receiver = receiver;  
    }  

    @Override  
    public void exe() {  
        receiver.action();  
    }  
}  

public class Receiver {  
    public void action(){  
        System.out.println("command received!");  
    }  
}  

public class Invoker {  

    private Command command;  

    public Invoker(Command command) {  
        this.command = command;  
    }  

    public void action(){  
        command.exe();  
    }  
}  

public class 命令模式 {  

    public static void main(String[] args) {  
        Receiver receiver = new Receiver();  
        Command cmd = new MyCommand(receiver);  
        Invoker invoker = new Invoker(cmd);  
        invoker.action();  
    }  
}
```



#### 3.使用场景

当需要先将一个函数登记上，然后再以后调用此函数时，就需要使用命令模式，其实这就是回调函数。

有时候需要向某些对象发送请求，但是并不知道请求的接收者是谁，也不知道被请求的操作是什么。此时希望用一种松耦合的方式来设计程序，使得请求发送者和请求接收者能够消除彼此之间的耦合关系

#### 4.优缺点

**优点：**

- 类间解耦：调用者角色与接收者角色之间没有任何依赖关系，调用者实现功能时只需调用Command 抽象类的execute方法就可以，不需要了解到底是哪个接收者执行。
- 可扩展性：Command的子类可以非常容易地扩展，而调用者Invoker和高层次的模块Client不产生严 重的代码耦合。
- 命令模式结合其他模式会更优秀：命令模式可以结合责任链模式，实现命令族解析任务；结合模板方法模式，则可以减少 Command子类的膨胀问题。

**缺点：**

　　命令模式也是有缺点的，请看Command的子类：如果有N个命令，问题就出来 了，Command的子类就可不是几个，而是N个，这个类膨胀得非常大，这个就需要读者在项 目中慎重考虑使用。

#### 5.实际使用案例（举例说明）





### **备忘录模式:**

#### 	1）UML类图

**定义**：在不破坏封闭的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到原先保存的状态。

该模式用于保存对象当前状态，并且在之后可以再次恢复到此状态。备忘录模式**实现的方式**需要保证被保存的对象状态不能被对象从外部访问，**目的**是为了保护被保存的这些对象状态的完整性以及内部实现不向外暴露

![图片描述](http://img.mukewang.com/5770f74f0001653306290237.png)

#### 		2）代码示例

```java
public class Original {  

    private String value;  

    public String getValue() {  
        return value;  
    }  

    public void setValue(String value) {  
        this.value = value;  
    }  

    public Original(String value) {  
        this.value = value;  
    }  

    public Memento createMemento(){  
        return new Memento(value);  
    }  

    public void restoreMemento(Memento memento){  
        this.value = memento.getValue();  
    }  
}  

public class Memento {  

    private String value;  

    public Memento(String value) {  
        this.value = value;  
    }  

    public String getValue() {  
        return value;  
    }  

    public void setValue(String value) {  
        this.value = value;  
    }  
}  

public class Storage {  

    private Memento memento;  

    public Storage(Memento memento) {  
        this.memento = memento;  
    }  

    public Memento getMemento() {  
        return memento;  
    }   
}

public class 备忘录模式 {

    public static void main(String[] args) {

        // 创建原始类
        Original origi = new Original("egg");

        // 创建备忘录
        Storage storage = new Storage(origi.createMemento());

        // 修改原始类的状态
        System.out.println("初始化状态为：" + origi.getValue());
        origi.setValue("niu");
        System.out.println("修改后的状态为：" + origi.getValue());

        // 回复原始类的状态
        origi.restoreMemento(storage.getMemento());
        System.out.println("恢复后的状态为：" + origi.getValue());
    }
}

```



#### 		3）使用场景

1. 需要保存和恢复数据的相关状态场景。
2. 提供一个可回滚（rollback）的操作。
3. 数据库连接的事务管理就是用的备忘录模式。

#### 		4）优缺点

**优点**：

1、有时一些发起人对象的内部信息必须保存在发起人对象以外的地方，但是必须要由发起人对象自己读取，这时，使用备忘录模式可以把复杂的发起人内部信息对其他的对象屏蔽起来，从而可以恰当地保持封装的边界。

2、本模式简化了发起人类。发起人不再需要管理和保存其内部状态的一个个版本，客户端可以自行管理他们所需要的这些状态的版本。

3、当发起人角色的状态改变的时候，有可能这个状态无效，这时候就可以使用暂时存储起来的备忘录将状态复原。

**缺点**：

1、如果发起人角色的状态需要完整地存储到备忘录对象中，那么在资源消耗上面备忘录对象会很昂贵。

2、当负责人角色将一个备忘录 存储起来的时候，负责人可能并不知道这个状态会占用多大的存储空间，从而无法提醒用户一个操作是否很昂贵。

3、当发起人角色的状态改变的时候，有可能这个协议无效。如果状态改变的成功率不高的话，不如采取“假如”协议模式。



### 状态模式:

#### 		1）UML类图

**定义**：当一个对象的内在状态改变时允许改变其行为，这个对象看起来像是改变了其类。

**主要解决**：当控制一个对象状态的条件表达式过于复杂时的情况。把状态的判断逻辑转移到表示不同状态的一系列类中，可以把复杂的判断逻辑简化。

**意图**：允许一个对象在其内部状态改变时改变它的行为

![图片描述](http://img.mukewang.com/5770f8ab00013fc105500129.png)

#### 	2）代码示例

```java
public class State {  

    private String value;  

    public String getValue() {  
        return value;  
    }  

    public void setValue(String value) {  
        this.value = value;  
    }  

    public void method1(){  
        System.out.println("execute the first opt!");  
    }  

    public void method2(){  
        System.out.println("execute the second opt!");  
    }  
}

public class Context {  

    private State state;  

    public Context(State state) {  
        this.state = state;  
    }  

    public State getState() {  
        return state;  
    }  

    public void setState(State state) {  
        this.state = state;  
    }  

    public void method() {  
        if (state.getValue().equals("state1")) {  
            state.method1();  
        } else if (state.getValue().equals("state2")) {  
            state.method2();  
        }  
    }  
}


public class 状态模式 {

    public static void main(String[] args) {

        State state = new State();
        Context context = new Context(state);

        //设置第一种状态
        state.setValue("state1");
        context.method();

        //设置第二种状态
        state.setValue("state2");
        context.method();
    }
}

```



#### 		3）使用场景

1. 一个对象的行为取决于它的状态，并且它必须在运行时刻根据状态改变它的行为。
2. 一个操作中含有庞大的多分支结构，并且这些分支决定于对象的状态。

#### 		4）优缺点

**优点**：

　　1、封装了转换规则。 
   2、枚举可能的状态，在枚举状态之前需要确定状态种类。 
   3、将所有与某个状态有关的行为放到一个类中，并且可以方便地增加新的状态，只需要改变对象状态即可改变对象的行为。 
   4、允许状态转换逻辑与状态对象合成一体，而不是某一个巨大的条件语句块。 
   5、可以让多个环境对象共享一个状态对象，从而减少系统中对象的个数。

**缺点**：

　　1、状态模式的使用必然会增加系统类和对象的个数。 
   2、状态模式的结构与实现都较为复杂，如果使用不当将导致程序结构和代码的混乱。 
   3、状态模式对“开闭原则”的支持并不太好，对于可以切换状态的状态模式，增加新的状态类需要修改那些负责状态转换的源代码，否则无法切换到新增状态；而且修改某个状态类的行为也需修改对应类的源代码。





### 访问者模式:

#### 		1）UML类图

**定义**：表示一个作用于其对象结构中的各元素的操作，它使你可以在不改变各元素类的前提下定义作用于这些元素的新操作。

可以对定义这么理解：有这么一个操作，它是作用于一些元素之上的，而这些元素属于某一个对象结构。同时这个操作是在不改变各元素类的前提下，在这个前提下定义新操作是访问者模式精髓中的精髓。

**主要解决**：稳定的数据结构和易变的操作耦合问题。就是把数据结构和作用于结构上的操作解耦合，使得操作集合可相对自由地演化。

**本质**：预留通路，回调实现。它的实现主要就是通过预先定义好调用的通路，在被访问的对象上定义accept方法，在访问者的对象上定义visit方法；然后在调用真正发生的时候，通过两次分发的技术，利用预先定义好的通路，回调到访问者具体的实现上。

![图片描述](http://img.mukewang.com/5771053100019c6204430249.png)

#### 		2）代码示例

```java
public interface Visitor {  
    public void visit(Subject sub);  
}  

public class MyVisitor implements Visitor {  

    @Override  
    public void visit(Subject sub) {  
        System.out.println("visit the subject："+sub.getSubject());  
    }  
}

public interface Subject {  
    public void accept(Visitor visitor);  
    public String getSubject();  
}  

public class MySubject implements Subject {  

    @Override  
    public void accept(Visitor visitor) {  
        visitor.visit(this);  
    }  

    @Override  
    public String getSubject() {  
        return "love";  
    }  
}


public class 访问者模式 {

    public static void main(String[] args) {

        Visitor visitor = new MyVisitor();
        Subject sub = new MySubject();
        sub.accept(visitor);
    }
}
```



#### 		3）使用场景

1. 对象结构比较稳定，但经常需要在此对象结构上定义新的操作。
2. 需要对一个对象结构中的对象进行很多不同的且不相关的操作，而需要避免这些操作“污染”这些对象的类，也不希望在增加新操作时修改这些类。

#### 		4）优缺点

优点：访问者模式 增加新的操作 很容易 , 只需要增加一个新的 访问者 即可 ; 将相关的行为 , 封装到一个 访问者 中

缺点： 增加 新 数据结构 比较困难；元素变更 比较困难 ; 如 为 被访问 的对象 增加 / 减少 一些属性 , 相应的 访问者 也需要进行修改 ;







### 中介者模式：

#### 		1）UML类图

**用一个中介对象来封装一系列的对象交互。中介者使各对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互。**

中介者作为一种行为设计模式，它公开一个统一的接口，系统的不同对象或组件可以通过该接口进行通信。增加一个中介者对象后，所有的相关对象通过中介者对象来通信，而不是互相引用，所以当一个对象发生改变时，只需要通知中介者对象即可。

![图片描述](http://img.mukewang.com/577106b20001f0b606680337.png)

#### 		2）代码示例

```java
public interface Mediator {  
    public void createMediator();  
    public void workAll();  
}  

public class MyMediator implements Mediator {  

    private User user1;  
    private User user2;  

    public User getUser1() {  
        return user1;  
    }  

    public User getUser2() {  
        return user2;  
    }  

    @Override  
    public void createMediator() {  
        user1 = new User1(this);  
        user2 = new User2(this);  
    }  

    @Override  
    public void workAll() {  
        user1.work();  
        user2.work();  
    }  
}  

public abstract class User {  

    private Mediator mediator;  

    public Mediator getMediator(){  
        return mediator;  
    }  

    public User(Mediator mediator) {  
        this.mediator = mediator;  
    }  

    public abstract void work();  
}  

public class User1 extends User {  

    public User1(Mediator mediator){  
        super(mediator);  
    }  

    @Override  
    public void work() {  
        System.out.println("user1 exe!");  
    }  
}  

public class User2 extends User {  

    public User2(Mediator mediator){  
        super(mediator);  
    }  

    @Override  
    public void work() {  
        System.out.println("user2 exe!");  
    }  
}

public class 中介者模式 {

    public static void main(String[] args) {
        Mediator mediator = new MyMediator();
        mediator.createMediator();
        mediator.workAll();
    }
}
```



#### 		3）使用场景

1.  一组定义良好的对象，现在要进行复杂的通信。
2.  定制一个分布在多个类中的行为，而又不想生成太多的子类。

#### 		4）优缺点

优点：解耦。把同事类原来一对多的依赖变成一对一的依赖，降低类的耦合，同时也符合了迪米特原则。

缺点：中介者模式把业务流程和协调都写在中介者，当同事类越多，中介者的业务就越复杂，造成不好管理的弊端。





### 解释器模式:

#### 		1）UML类图

**定义**：给定一个语言，定义一个文法的一种表示， 并定义一个解释器， 这个解释器使用该表示来解释语言中的句子。

![图片描述](http://img.mukewang.com/577107d40001ac3005640292.png)

#### 		2）代码示例

```java
public interface Expression {  
    public int interpret(Context context);  
}  

public class Plus implements Expression {  

    @Override  
    public int interpret(Context context) {  
        return context.getNum1()+context.getNum2();  
    }  
}  

public class Minus implements Expression {  

    @Override  
    public int interpret(Context context) {  
        return context.getNum1()-context.getNum2();  
    }  
}  

public class Context {  

    private int num1;  
    private int num2;  

    public Context(int num1, int num2) {  
        this.num1 = num1;  
        this.num2 = num2;  
    }  

    public int getNum1() {  
        return num1;  
    }  
    public void setNum1(int num1) {  
        this.num1 = num1;  
    }  
    public int getNum2() {  
        return num2;  
    }  
    public void setNum2(int num2) {  
        this.num2 = num2;  
    }  

}  

public class 解释器模式 {

    public static void main(String[] args) {

        // 计算9+2-8的值
        int result = new Minus().interpret((new Context(new Plus().interpret(new Context(9, 2)), 8)));
        System.out.println(result);
    }
}

```



#### 		3）使用场景

1.当有一个语言需要解释执行，并且你可将该语言中的句子表示为一个抽象语法树，可以使用解释器模式。而当存在以下情况时该模式效果最好

2.该文法的类层次结构变得庞大而无法管理。此时语法分析程序生成器这样的工具是最好的选择。他们无需构建抽象语法树即可解释表达式，这样可以节省空间而且还可能节省时间。

3.效率不是一个关键问题，最高效的解释器通常不是通过直接解释语法分析树实现的，而是首先将他们装换成另一种形式，例如，正则表达式通常被装换成状态机，即使在这种情况下，转换器仍可用解释器模式实现，该模式仍是有用的

#### 		4）优缺点

**优点：**

1.可以很容易地改变和扩展方法， 因为该模式使用类来表示方法规则， 你可以使用继承来改变或扩展该方法。

2.也比较容易实现方法， 因为定义抽象语法树总各个节点的类的实现大体类似， 这些类都易于直接编写。

3.解释器模式就是将一句话，转变为实际的命令程序执行而已。 而不用解释器模式本身也可以分析， 但通过继承抽象表达式的方式， 由于依赖转置原则， 使得文法的扩展和维护都带来的方便。

**缺点：**

解释器模式为方法中的每一条规则至少定义了一个类， 因此包含许多规则的方法可能难以管理和维护。 因此当方法非常复杂时， 使用其他的技术如 语法分析程序 或 编译器生成器来处理。
