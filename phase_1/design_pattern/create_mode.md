##  一.设计模式定义

​		**设计模式**是一套被反复使用的、多数人知晓的、经过分类编目的、代码设计经验的总结，使用设计模式是为了可重用代码，让代码更容易被他人理解并且提高代码的可靠性。设计模式是一种用于对系统中不断重现的设计问题的解决方案进行文本化的技术,也是一种共享专家设计经验的技术。

##  二.设计模式的六大原则

**总原则：开闭原则**

1. **单一职责原则**：每个类负责单一职责，如果不是就应该对类进行拆分。
2. **里氏替换原则**：父类可以出现的地方，子类也一定能出现。
3. **依赖倒转原则**：也叫依赖注入原则。面向接口编程，依赖抽象而不依赖具体，即不与具体类交互，而与具体类的上层接口交互
4. **接口隔离原则**：把接口拆分，用小接口代替大接口，实现只实现需要的方法。现在可以使用新语法来实现这种原则。
5. **迪米特法则**：最少知道原则。暴露出的功能，细节越少越好。暴露必要的接口。用户知道必须的东西就行了，不需要他们知道全部，主要是为了安全考虑。
6. **合成复用原则**：代替继承，尽量不要使用继承，使用对象。但是在某些时候也不能不用继承。

##  三.设计模式分类及各模式分别解决了什么问题

### 根据模式的目的划分

1. **创建型模式**，共五种：工厂方法模式、抽象工厂模式、单例模式、建造者模式、原型模式。
2. **结构型模式**，共七种：适配器模式、装饰器模式、代理模式、外观模式、桥接模式、组合模式、享元模式。
3. **行为型模式**，共十一种：策略模式、模板方法模式、观察者模式、迭代子模式、责任链模式、命令模式、备忘录模式、状态模式、访问者模式、中介者模式、解释器模式。

## 根据模式的作用划分

1. ### **类模式**：工厂方法、（类）适配器、模板方法、解释器

2. ### **对象模式**： 

| **范围/目的** | **创建型模式**                            | **结构型模式**                                               | **行为型模式**                                               |
| ------------- | ----------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **类模式**    | 工厂方法                                  | （类）适配器                                                 | 模板方法<br/>解释器                                          |
| 对象模式      | 单例<br />原型<br />抽象工厂 <br />建造者 | 代理<br />（对象）适配器<br /> 桥接<br />装饰<br />外观<br />享元<br />组合 | 策略<br/>命令<br/>职责链<br/>状态<br/>观察者<br/>中介者<br/>迭代器<br/>访问者<br/>备忘录 |




##  四.创建型模式概述：

​		**创建型模式**是处理对象创建的设计模式，试图根据实际情况使用合适的方式创建对象。基本的对象创建方式可能会导致设计上的问题，或增加设计的复杂度。创建型模式通过以某种方式控制对象的创建来解决问题。

​		**创建型模式**由两个主导思想构成。一是将系统使用的具体类封装起来，二是隐藏这些具体类的实例创建和结合的方式。

​		**创建型模式**又分为对象创建型模式和类创建型模式。对象创建型模式处理对象的创建，类创建型模式处理类的创建。详细地说，对象创建型模式把对象创建的一部分推迟到另一个对象中，而类创建型模式将它对象的创建推迟到子类中

## 五.创建型设计模式详解，按照以下格式展开介绍

### 1.UML类图（亲手画图，不能直接down网上的）

1. 工厂方法：

   ![图片](https://mmbiz.qpic.cn/mmbiz_jpg/yqdSticVI7ROCMHbiaruI4hfDIaYNLnQvMZ5clAXqaRyLdDAcYHU5YZnD8qDSpeiaSnvppz6ib17bGNQExGza4ENYg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

2. 单例模式：

   ![图片](https://mmbiz.qpic.cn/mmbiz_jpg/yqdSticVI7ROCMHbiaruI4hfDIaYNLnQvMdicB4Ng5xia0u8Qc4qFJ5eSshMM7E4RibcTEfMt3icEVdN1EYuP4M0jt0A/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

3. 原型模式：

   ![图片](https://mmbiz.qpic.cn/mmbiz_jpg/yqdSticVI7ROCMHbiaruI4hfDIaYNLnQvMq5YqUV0G5vJExyjfX2CZjAB1kP04ibpqYYyTyrztMx3Y1Y3xdJ1wpLw/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

4. 抽象工厂：

   ![图片](https://mmbiz.qpic.cn/mmbiz_jpg/yqdSticVI7ROCMHbiaruI4hfDIaYNLnQvMF3ZLFLVMcAfzCP0GHAJSGcLbpJawo2mwOxLl1WAJfOdP3wJtjnPkqg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

5. 建造者：

   ![图片](https://mmbiz.qpic.cn/mmbiz_jpg/yqdSticVI7ROCMHbiaruI4hfDIaYNLnQvMOYceTURjZ4nwC9Ih5KzL4dCbwogS2m0SHQic4OxD9icE98lHaJatSHBQ/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### 2.代码示例

1. 工厂方法：

   ```java
   // 抽象产品：动物类
   interface IAnimal {
       public void show();
   }
   
   // 具体产品：马类
   public class Horse implements IAnimal {
       @Override
       public void show() {
           System.out.println("这是一匹马！");
       }
   }
   
   // 具体产品：牛类
   public class Cattle implements IAnimal {
       @Override
       public void show() {
           System.out.println("这是一头牛！");
       }
   }
   
   //抽象工厂：畜牧场
   interface IAnimalFarm {
       public IAnimal newAnimal();
   }
   
   //具体工厂：养马场
   class HorseFarm implements IAnimalFarm {
       public IAnimal newAnimal() {
           System.out.println("新马出生！");
           return new Horse();
       }
   }
   
   //具体工厂：养牛场
   class CattleFarm implements IAnimalFarm {
       public IAnimal newAnimal() {
           System.out.println("新牛出生！");
           return new Cattle();
       }
   }
   
   public class AnimalFarmTest {
   
       public static void main(String[] args) {
           try {
               IAnimal a;
               IAnimalFarm af = (IAnimalFarm) ReadXML.getObject(HorseFarm.class);
               if (af != null) {
                   a = af.newAnimal();
                   a.show();
               }
           } catch (Exception e) {
               System.out.println(e.getMessage());
           }
       }
   }
   
   public class ReadXML {
       public static Object getObject(Class<?> mClass) {
           try {
               return mClass.newInstance();
           } catch (InstantiationException e) {
               e.printStackTrace();
               return null;
           } catch (IllegalAccessException e) {
               e.printStackTrace();
               return null;
           }
       }
   }
   ```

   

2. 单例模式：

   ```java
   public class LwySingleton {
       private static volatile LwySingleton instance = null;    //保证 instance 在所有线程中同步
       private LwySingleton() {}    //private 避免类在外部被实例化
       public static synchronized LwySingleton getInstance() {
           // getInstance 方法前加同步
           if(instance == null) {
               instance = new LwySingleton();
           }
           return instance;
       }
   }
   ```

   

3. 原型模式：

   ```java
   // 具体原型类
   class Realizetype implements Cloneable {
       Realizetype() {
           System.out.println("具体原型创建成功！");
       }
       public Object clone() throws CloneNotSupportedException {
           System.out.println("具体原型复制成功！");
           return (Realizetype)super.clone();
       }
   }
   
   // 原型模式的测试类
   public class PrototypeTest {
       public static void main(String[] args)throws CloneNotSupportedException {
           Realizetype obj1=new Realizetype();
           Realizetype obj2=(Realizetype)obj1.clone();
           System.out.println("obj1==obj2?"+(obj1==obj2));
       }
   }
   ```

   

4. 抽象工厂：

   ```java
   // 抽象产品：动物类
   interface Animal {
       public void show();
   }
   // 具体产品：马类
   class Horse implements Animal {
       public void show() {
           System.out.println("这是一匹马");
       }
   }
   // 具体产品：牛类
   class Cattle implements Animal {
       public void show() {
           System.out.println("这是一头牛");
       }
   }
   
   // 抽象产品：植物类
   interface Plant {
       public void show();
   }
   // 具体产品：水果类
   class Fruitage implements Plant {
       public void show() {
           System.out.println("这是一水果");
       }
   }
   // 具体产品：蔬菜类
   class Vegetables implements Plant {
       public void show() {
           System.out.println("这是一蔬菜");
       }
   }
   
   // 抽象工厂：农场类
   interface Farm {
       public Animal newAnimal();
       public Plant newPlant();
   }
   
   // 具体工厂：韶关农场类
   class SGfarm implements Farm {
       public Animal newAnimal() {
           System.out.println("新牛出生！");
           return new Cattle();
       }
       public Plant newPlant() {
           System.out.println("蔬菜长成！");
           return new Vegetables();
       }
   }
   // 具体工厂：上饶农场类
   class SRfarm implements Farm {
       public Animal newAnimal() {
           System.out.println("新马出生！");
           return new Horse();
       }
       public Plant newPlant() {
           System.out.println("水果长成！");
           return new Fruitage();
       }
   }
   ```

   

5. 建造者：

   ```java
   // 产品：客厅
   class Parlour {
       private String wall;    //墙
       private String TV;    //电视
       private String sofa;    //沙发
       public void setWall(String wall) {
           this.wall = wall;
       }
       public void setTV(String TV) {
           this.TV = TV;
       }
       public void setSofa(String sofa) {
           this.sofa = sofa;
       }
       public void show() {
           System.out.println("开始装修客厅");
       }
   }
   // 抽象建造者：装修工人
   abstract class Decorator {
       // 创建产品对象
       protected Parlour product = new Parlour();
       publicabstract void buildWall();
       publicabstract void buildTV();
       publicabstract void buildSofa();
       //返回产品对象
       public  Parlour getResult() {
           return  product;
       }
   }
   // 具体建造者：具体装修工人1
   class ConcreteDecorator1  extends Decorator {
       public void buildWall() {
           product.setWall("w1");
       }
       public void buildTV() {
           product.setTV("TV1");
       }
       public void buildSofa() {
           product.setSofa("sf1");
       }
   }
   //具体建造者：具体装修工人2
   class ConcreteDecorator2 extends Decorator {
         public void buildWall() {
             product.setWall("w2");
         }
         public void buildTV() {
             product.setTV("TV2");
         }
         public void buildSofa() {
             product.setSofa("sf2");
         }
   }
   //指挥者：项目经理
   class ProjectManager {
       private Decorator builder;
       public ProjectManager(Decorator builder) {
             this.builder = builder;
       }
       //产品构建与组装方法
       public Parlour decorate() {
             builder.buildWall();
             builder.buildTV();
             builder.buildSofa();
             return builder.getResult();
       }
   }
   ```

   

### 3.使用场景（什么场景下，选用什么模式，解决什么问题）

1. **工厂方法**：

   - 当一个类不知道它所必须创建的对象的类的时候。


   - 当一个类希望由它的子类来指定它所创建的对象的时候。


   - 当类将创建对象的职责委托给多个帮助子类中的某一个，并且你希望将哪一个帮助子类是代理者这一信息局部化的时候。

2. **单例模式**：

   - 当类只能有一个实例而且客户可以从一个众所周知的访问点访问它时。


   - 当这个唯一实例应该是通过子类化可扩展的，并且客户应该无需更改代码就能使用一个扩展的实例时。

3. **原型模式**：

   - 当要实例化的类是在运行时刻指定时，例如，通过动态装载；或者


   - 为了避免创建一个与产品类层次平行的工厂类层次时；或者


   - 当一个类的实例只能有几个不同状态组合中的一种时。建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些。

4. **抽象工厂**：

   - 一个系统要独立于它的产品的创建、组合和表示时。

   - 一个系统要由多个产品系列中的一个来配置时。

   - 当你要强调一系列相关的产品对象的设计以便进行联合使用时。

   - 当你提供一个产品类库，而只想显示它们的接口而不是实现时。

5. **建造者**：

   - 当创建复杂对象的算法应该独立于该对象的组成部分以及它们的装配方式时。


   - 当构造过程必须允许被构造的对象有不同的表示时。

### 4.实际使用案例（举例说明）

