# 设计模式

## 创建型模式

### 单例模式

### 工厂模式（Factory Pattern）

工厂模式是 Java 中最常用的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

**注意：**策略模式和工厂模式非常相似，工厂模式是创造型模式，生产出来的都是新的对象，而策略模式是加载不同的策略，

```java
public class FactoryPatternDemo {
   public static void main(String[] args) {
      ShapeFactory shapeFactory = new ShapeFactory();
      //获取 Circle 的对象，并调用它的 draw 方法
      Shape shape1 = shapeFactory.getShape("CIRCLE");
      //调用 Circle 的 draw 方法
      shape1.draw();
      //获取 Rectangle 的对象，并调用它的 draw 方法
      Shape shape2 = shapeFactory.getShape("RECTANGLE");
      //调用 Rectangle 的 draw 方法
      shape2.draw();
   }
}
interface Shape {
   void draw();
}
class Rectangle implements Shape {
 
   @Override
   public void draw() {
      System.out.println("Inside Rectangle::draw() method.");
   }
}
class Square implements Shape {
 
   @Override
   public void draw() {
      System.out.println("Inside Square::draw() method.");
   }
}
class ShapeFactory {
   //使用 getShape 方法获取形状类型的对象
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }        
      if(shapeType.equalsIgnoreCase("Square")){
         return new Square();
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
      }
      return null;
   }
}
```

## 行为型模式

### 命令模式（Command Pattern）

```java
public class CommandPattern {
    public static void main(String[] args) {
        Light light = new Light();
        LightOnCommand onCommand = new LightOnCommand(light);
        LightOffCommand offCommand = new LightOffCommand(light);
        Controller controller = new Controller();
        controller.setCommand(onCommand, offCommand);
        controller.onButton();
        controller.offButton();
    }
}

class Light {
    public void lightOn() {
        System.out.println("light on");
    }
    public void lightOff() {
        System.out.println("light off");
    }
}
interface Command {
    void execute();
}

class LightOnCommand implements Command {
    private Light light;
    public LightOnCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.lightOn();
    }
}
class LightOffCommand implements Command {
    private Light light;
    public LightOffCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.lightOff();
    }
}

class Controller {
    private Command onCommand;
    private Command offCommand;

    public void setCommand(Command onCommand, Command offCommand) {
        this.onCommand = onCommand;
        this.offCommand = offCommand;
    }
    public void onButton() {
        onCommand.execute();
    }
    public void offButton() {
        offCommand.execute();
    }
}
```

### 策略模式（Strategy Pattern）

**意图：**定义一系列的算法,把它们一个个封装起来, 并且使它们可相互替换。

**主要解决：**在有多种算法相似的情况下，使用 if...else 所带来的复杂和难以维护。

**何时使用：**一个系统有许多许多类，而区分它们的只是他们直接的行为。

**如何解决：**将这些算法封装成一个一个的类，任意地替换。

**关键代码：**实现同一个接口。

**应用实例：** 1、诸葛亮的锦囊妙计，每一个锦囊就是一个策略。 2、旅行的出游方式，选择骑自行车、坐汽车，每一种旅行方式都是一个策略。 

**优点：** 1、算法可以自由切换。 2、避免使用多重条件判断。 3、扩展性良好。

**缺点：** 1、策略类会增多。 2、所有策略类都需要对外暴露。

**使用场景：** 1、如果在一个系统里面有许多类，它们之间的区别仅在于它们的行为，那么使用策略模式可以动态地让一个对象在许多行为中选择一种行为。 2、一个系统需要动态地在几种算法中选择一种。 3、如果一个对象有很多的行为，如果不用恰当的模式，这些行为就只好使用多重的条件选择语句来实现。

**注意事项：**如果一个系统的策略多于四个，就需要考虑使用混合模式，解决策略类膨胀的问题。

![&#x7B56;&#x7565;&#x6A21;&#x5F0F;uml&#x7ED3;&#x6784;&#x56FE;](../.gitbook/assets/image%20%285%29.png)

```java
public class StrategyPatternDemo {
   public static void main(String[] args) {
      OperationAdd operationAdd = new OperationAdd();
      OperationSubstract operationSubstract = new OperationSubstract();
      OperationMultiply operationMultiply = new OperationMultiply();
      Context context = new Context();
      
      context.setStrategy(operationAdd);
      System.out.println("10 + 5 = " + context.executeStrategy(10, 5));
      
      context.setStrategy(operationSubstract);  
      System.out.println("10 - 5 = " + context.executeStrategy(10, 5));

      context.setStrategy(operationMultiply);
      System.out.println("10 * 5 = " + context.executeStrategy(10, 5));
   }
}
interface Strategy {
   public int doOperation(int num1, int num2);
}
class OperationAdd implements Strategy{
   @Override
   public int doOperation(int num1, int num2) {
      return num1 + num2;
   }
}
class OperationSubstract implements Strategy{
   @Override
   public int doOperation(int num1, int num2) {
      return num1 - num2;
   }
}
class Context {
   private Strategy strategy;
 
   public setStrategy(Strategy strategy){
      this.strategy = strategy;
   }
 
   public int executeStrategy(int num1, int num2){
      return strategy.doOperation(num1, num2);
   }
}
```

## 结构型模式

### 代理模式（Proxy Pattern）

在代理模式中，一个类代表另一个类的功能。这种类型的设计模式属于结构型模式。

```java
public class ProxyPatternDemo {
   public static void main(String[] args) {
      Image image = new ProxyImage("test_10mb.jpg");
      // 图像将从磁盘加载
      image.display(); 
      System.out.println("");
      // 图像不需要从磁盘加载
      image.display();  
   }
}
interface Image {
   void display();
}
class RealImage implements Image {
   private String fileName;
   public RealImage(String fileName){
      this.fileName = fileName;
      loadFromDisk(fileName);
   }
   @Override
   public void display() {
      System.out.println("Displaying " + fileName);
   }
   private void loadFromDisk(String fileName){
      System.out.println("Loading " + fileName);
   }
}
class ProxyImage implements Image{
   private RealImage realImage;
   private String fileName;
   public ProxyImage(String fileName){
      this.fileName = fileName;
   }
   @Override
   public void display() {
      if(realImage == null){
         realImage = new RealImage(fileName);
      }
      realImage.display();
   }
}
```

### 装饰器模式（Decorator Pattern）

装饰器模式允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。这种模式创建了一个装饰类，用来包装原有的类，并在保持类方法签名完整性的前提下，提供了额外的功能。

```java
public class DecoratorPatternDemo {
   public static void main(String[] args) {
      Shape circle = new Circle();
      ShapeDecorator redCircle = new RedShapeDecorator(new Circle());
      ShapeDecorator redRectangle = new RedShapeDecorator(new Rectangle());
      //Shape redCircle = new RedShapeDecorator(new Circle());
      //Shape redRectangle = new RedShapeDecorator(new Rectangle());
      System.out.println("Circle with normal border");
      circle.draw();
      System.out.println("\nCircle of red border");
      redCircle.draw();
      System.out.println("\nRectangle of red border");
      redRectangle.draw();
   }
}
interface Shape {
   void draw();
}
class Rectangle implements Shape {
   @Override
   public void draw() {
      System.out.println("Shape: Rectangle");
   }
}
class Circle implements Shape {
   @Override
   public void draw() {
      System.out.println("Shape: Circle");
   }
}
abstract class ShapeDecorator implements Shape {
   protected Shape decoratedShape;
   public ShapeDecorator(Shape decoratedShape){
      this.decoratedShape = decoratedShape;
   }
   public void draw(){
      decoratedShape.draw();
   }  
}
class RedShapeDecorator extends ShapeDecorator {
   public RedShapeDecorator(Shape decoratedShape) {
      super(decoratedShape);     
   }
   @Override
   public void draw() {
      decoratedShape.draw();         
      setRedBorder(decoratedShape);
   }
   private void setRedBorder(Shape decoratedShape){
      System.out.println("Border Color: Red");
   }
}
```

