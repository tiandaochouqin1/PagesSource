---
title: DesignPatterns
tags:
  - [设计模式]
categories: [编程]
date: 2020-09-20 21:25:59
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->


[图说设计模式](http://design-patterns.readthedocs.org/zh_CN/latest/index.html)  [Github](https://github.com/me115/design_patterns)(C++实现，关于具体模式的实现内容不够详细)
https://github.com/youlookwhat/DesignPattern   （Java，更易于理解，总结了以下两个链接的内容）
https://www.runoob.com/design-pattern/design-pattern-intro.html
https://blog.csdn.net/lmj623565791/category_2206597.html （案例多来源于《Head First Design Pattern》）

# 原则
http://www.uml.org.cn/sjms/201211023.asp
1. 开放-关闭原则 (Open-Closed Principle)
一个软件实体如类、模块或函数应该对扩展开放，对修改关闭。
2. 单一职责原则 (Single Responsibility Principle)
应该有且仅有一个原因引起类的变更。即一个类只负责一项职责。
3. 里氏替换原则 (Liskov Substitution Principle)
所有引用基类对象的地方都必须能透明地使用其子类的对象。
4. 依赖倒转原则 (Dependence Inversion Principle)
   - 高层模块不应该依赖低层模块，两者都要改依赖其抽象（模块间的依赖通过抽象产生，实现类不发生直接的依赖关系）
   - 抽象不应该依赖细节（接口或者抽象类不依赖实现类）
   - 细节可以依赖抽象（实现类依赖接口或者抽象类）
5. 接口隔离原则 (Interface Segregation Principle)
客户端不应该依赖他不需要的接口，类之间的依赖关系应该建立在最小的接口上。
6. 迪米特法则（Law Of Demeter）
一个对象应该对其他对象有最少的了解（低耦合）。
7. 组合/聚合复用原则 (Composite/Aggregate Reuse Principle)
在一个新的对象里面使用一些已有的对象，使之成为新对象的一部分; 新的对象通过向这些对象的委派达到复用已有功能的目的。

# 重点模式
工厂方法模式、抽象工厂模式；简单工厂模式、单例模式
外观模式；适配器模式、代理模式
迭代器模式、观察者模式；命令模式、策略模式

# UML类图和时序图
## UML
UML：Unified Modeling Language。
UML分为模型和图形两大类。
UML图（包括用例图、协作图、活动图、序列图、部署图、构件图、类图、状态图）是模型中信息的图表表达形式，但是UML模型独立于UML图存在。
在UML系统开发中有三个主要的模型：
1. 功能模型：从用户的角度展示系统的功能，包括用例图。
2. 对象模型：采用对象，属性，操作，关联等概念展示系统的结构和基础，包括类别图、对象图。
3. 动态模型：展现系统的内部行为。包括序列图，活动图，状态图。

### UML类图

在UML类图中，类使用包含类名、属性(field) 和方法(method) 且带有分割线的矩形来表示。
**类名：**粗体，抽象类则为斜体。
**属性：**
```
可见性 名称：类型[=默认值]
可见性一般为public、private和protected（包括friend），在类图分别用+、-和#表示。
```
**方法：**
可见性 名称（参数列表 参数1，参数2） ：返回类型

### 类与类之间的关系表达
类图中类与类之间的关系主要由：继承、实现、依赖、关联、聚合、组合这六大类型。

类的继承结构表现在UML中为：泛化(generalize)与实现(realize，继承抽象类)。
依赖关系：与关联关系不同的是，它是一种临时性的关系，通常在运行期间产生，并且随着运行时的变化； 依赖关系也可能发生变化
聚合关系：与组合关系不同的是，整体和部分不是强依赖的，即使整体不存在了，部分仍然存在。
![UML关系](../images/UMLRelations.jpg)

https://juejin.im/post/6844903893327937550

## 时序图
一种UML交互图，通过描述对象间发送消息的时间顺序，显示多个对象之间的动态协作。
包括的建模元素主要有：角色（Actor）、对象（Object）、生命线（Lifeline）、控制焦点（Focus of control）、消息（Message）、自关联消息、组合片段等。
消息：同步消息（调用）、异步消息、返回消息。
组合片段：13种，用来解决交互执行的条件和方式，它允许在序列图中直接表示逻辑组件，用于通过指定条件或子进程的应用区域，为任何生命线的任何部分定义特殊条件和子进程。


Operator | Fragment Type
---------|--------------
alt | 备用多个片段：只执行条件为真的片段
opt | 可选：仅当提供的条件为真时才执行片段。 相当于只有一条迹线的 alt。
par | 并行：每个片段并行运行
loop | 循环：片段可以执行多次，并且防护指示迭代的基础
region | 关键区域：片段只能有一个线程一次执行它
neg | 否定：片段显示无效的交互
ref | 参考：指在另一个图上定义的交互。 绘制框架以覆盖交互中涉及的生命线。 您可以定义参数和返回值。
sd | 序列图：用于包围整个序列图

[什么是序列图 / 时序图](https://juejin.im/post/6844903861707096072)

[UML建模之时序图（Sequence Diagram）](http://www.woshipm.com/ucd/607593.html)

# 创建型模式
## 简单工厂模式
简单工厂模式(Simple Factory Pattern)：又称为静态工厂方法(Static Factory Method)模式，它属于类创建型模式。在简单工厂模式中，可以根据参数的不同返回不同类的实例。
简单工厂模式包含三个角色：工厂角色负责实现创建所有实例的内部逻辑；抽象产品角色是所创建的所有对象的父类，负责描述所有实例所共有的公共接口；具体产品角色是创建目标，所有创建的对象都充当这个角色的某个具体类的实例。
**优点**在于实现对象的创建和对象的使用分离，将对象的创建交给专门的工厂类负责。
**缺点**在于工厂类不够灵活，增加新的具体产品需要修改工厂类的判断逻辑代码，而且产品较多时，工厂方法代码将会非常复杂。
![SimpleFactory](../images/SimpleFactory.jpg)

## 工厂方法模式(Factory Method Pattern)
工厂方法模式包含如下角色：
- Product：抽象产品
- ConcreteProduct：具体产品
- Factory：抽象工厂
- ConcreteFactory：具体工厂
![FactoryMethod](../images/FactoryMethod.jpg)
![seq_FactoryMethod](../images/seq_FactoryMethod.jpg)

### 代码分析
```
///////////////////////////////////////////////////////////
//  ConcreteFactory.cpp
//  Implementation of the Class ConcreteFactory
//  Created on:      02-十月-2014 10:18:58
//  Original author: colin
///////////////////////////////////////////////////////////

#include "ConcreteFactory.h"
#include "ConcreteProduct.h"

Product* ConcreteFactory::factoryMethod(){

	return  new ConcreteProduct();
}

```
```
#include "Factory.h"
#include "ConcreteFactory.h"
#include "Product.h"
#include <iostream>
using namespace std;

int main(int argc, char *argv[])
{
	Factory * fc = new ConcreteFactory();
	Product * prod = fc->factoryMethod();
	prod->use();
	
	delete fc;
	delete prod;
	
	return 0;
}

```
## 抽象工厂模式(Abstract Factory)
角色组成通工厂方法模式。

产品等级结构 ：产品等级结构即产品的继承结构。
产品族 ：在抽象工厂模式中，产品族是指由同一个工厂生产的，位于不同产品等级结构中的一组产品。

抽象工厂模式与工厂方法模式最大的区别在于，工厂方法模式针对的是一个产品等级结构，而抽象工厂模式则需要面对多个产品等级结构，一个工厂等级结构可以负责多个不同产品等级结构中的产品对象的创建 。

![AbatractFactory](../images/AbatractFactory.jpg)
![seq_AbatractFactory](../images/seq_AbatractFactory.jpg)

### 代码分析
```
#include <iostream>
#include "AbstractFactory.h"
#include "AbstractProductA.h"
#include "AbstractProductB.h"
#include "ConcreteFactory1.h"
#include "ConcreteFactory2.h"
using namespace std;

int main(int argc, char *argv[])
{
	AbstractFactory * fc = new ConcreteFactory1();
	AbstractProductA * pa =  fc->createProductA();
	AbstractProductB * pb = fc->createProductB();
	pa->use();
	pb->eat();
	
	AbstractFactory * fc2 = new ConcreteFactory2();
	AbstractProductA * pa2 =  fc2->createProductA();
	AbstractProductB * pb2 = fc2->createProductB();
	pa2->use();
	pb2->eat();

```

```
///////////////////////////////////////////////////////////
//  ConcreteFactory1.cpp
//  Implementation of the Class ConcreteFactory1
//  Created on:      02-十月-2014 15:04:11
//  Original author: colin
///////////////////////////////////////////////////////////

#include "ConcreteFactory1.h"
#include "ProductA1.h"
#include "ProductB1.h"
AbstractProductA * ConcreteFactory1::createProductA(){
	return new ProductA1();
}


AbstractProductB * ConcreteFactory1::createProductB(){
	return new ProductB1();
}

```

```
///////////////////////////////////////////////////////////
//  ProductA1.cpp
//  Implementation of the Class ProductA1
//  Created on:      02-十月-2014 15:04:17
//  Original author: colin
///////////////////////////////////////////////////////////

#include "ProductA1.h"
#include <iostream>
using namespace std;
void ProductA1::use(){
	cout << "use Product A1" << endl;
}
```

## 建造者模式
造者模式(Builder Pattern)：将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示。
包含如下角色：
- Builder：抽象建造者
- ConcreteBuilder：具体建造者
- Director：指挥者
- Product：产品角色
指挥者类Director：一方面它隔离了客户与生产过程；另一方面它负责控制产品的生成过程。

![BuilderPattern](../images/BuilderPattern.jpg)
![seq_BuilderPattern](../images/seq_BuilderPattern.jpg)

## 单例模式(Singleton Pattern)
单例模式确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例，这个类称为单例类，它提供全局访问的方法。
在单例模式的实现过程中，需要注意如下三点：
1. 单例类的构造函数为私有；
2. 提供一个自身的静态私有成员变量；
3. 提供一个公有的静态工厂方法。
   
![Singleton](../images/Singleton.jpg)
![seq_Singleton](../images/seq_Singleton.jpg)

# 结构型模式
结构型模式描述如何将类或者对象结合在一起形成更大的结构。
## 适配器模式(Adapter Pattern) 
将一个接口转换成客户希望的另一个接口，适配器模式使接口不兼容的那些类可以一起工作，其别名为包装器(Wrapper)。适配器模式既可以作为类结构型模式，也可以作为对象结构型模式。
适配器模式包含如下角色：
- Target：目标抽象类,定义客户要用的特定领域的接口；
- Adapter：适配器类,可以调用另一个接口，作为一个转换器，对适配者和抽象目标类进行适配，它是适配器模式的核心；
- Adaptee：适配者类,被适配的角色，它定义了一个已经存在的接口，这个接口需要适配；
- Client：客户类，针对目标抽象类进行编程，调用在目标抽象类中定义的业务方法。

适用情况包括：
1. 系统需要使用现有的类，而这些类的接口不符合系统的需要；
2. 想要建立一个可以重复使用的类，用于与一些彼此之间没有太大关联的一些类一起工作。

![Adapter](../images/Adapter.jpg)

![Adapter_classModel](../images/Adapter_classModel.jpg)

![seq_Adapter](../images/seq_Adapter.jpg)

**代码分析**
```
#include <iostream>
#include "Adapter.h"
#include "Adaptee.h"
#include "Target.h"

using namespace std;

int main(int argc, char *argv[])
{
	Adaptee * adaptee  = new Adaptee();
	Target * tar = new Adapter(adaptee);
	tar->request();
	
	return 0;
}

```

## 桥接模式(Bridge Pattern)

将抽象部分与它的实现部分分离，使它们都可以独立地变化。它是一种对象结构型模式，又称为柄体(Handle and Body)模式或接口(Interface)模式。

桥接模式将继承关系转换为**关联关系**，从而降低了类与类之间的耦合，减少了代码编写量。

桥接模式包含如下角色：
- Abstraction：抽象类,定义了一个实现类接口类型的对象并可以维护该对象;
- RefinedAbstraction：扩充抽象类,扩充由抽象类定义的接口，它实现了在抽象类中定义的抽象业务方法，在扩充抽象类中可以调用在实现类接口中定义的业务方法;
- Implementor：实现类接口,定义了实现类的接口，实现类接口仅提供基本操作，而抽象类定义的接口可能会做更多更复杂的操作；
- ConcreteImplementor：具体实现类,实现了实现类接口并且具体实现它，在不同的具体实现类中提供基本操作的不同实现，在程序运行时，具体实现类对象将替换其父类对象，提供给客户端具体的业务操作方法。

![BridgePattern](../images/BridgePattern.jpg)
![seq_BridgePattern](../images/seq_BridgePattern.jpg)

**代码分析**
```
#include <iostream>
#include "ConcreteImplementorA.h"
#include "ConcreteImplementorB.h"
#include "RefinedAbstraction.h"
#include "Abstraction.h"

using namespace std;

int main(int argc, char *argv[])
{
	
	Implementor * pImp = new ConcreteImplementorA();
	Abstraction * pa = new RefinedAbstraction(pImp);
	pa->operation();
	
	Abstraction * pb = new RefinedAbstraction(new ConcreteImplementorB());
	pb->operation();		
	
	delete pa;
	delete pb;
	
	return 0;
}
```

## 装饰模式(Decorator Pattern) 
动态地给一个对象增加一些额外的职责(Responsibility)，就增加对象功能来说，装饰模式比生成子类实现更为灵活。对象结构型模式。

Component:抽象构件定义了对象的接口，可以给这些对 象动态增加职责（方法）；
ConcreteComponent:具体构件定义了具体的构件对象，实现了 在抽象构件中声明的方法，装饰器可以给它增加额外的职责（方法）；
Decorator:抽象装饰类是抽象构件类的子类，用于给具体构件增加职责，但是具 体职责在其子类中实现；
ConcreteDecorator:具体装饰类是抽象装饰类的子类，负责向构 件添加新的职责。

![DecoratorPattern](../images/DecoratorPattern.jpg)
![seq_DecoratorPatern](../images/seq_DecoratorPatern.jpg)

## 外观模式(Facade Pattern)
外部与一个子系统的通信必须通过一个统一的外观对象进行，为子系统中的一组接口提供一个一致的界面，外观模式定义了一个高层接口，这个接口使得这一子系统更加容易使用。外观模式又称为门面模式，它是一种对象结构型模式。


外观模式包含如下角色：
- Facade: 外观角色
- SubSystem:子系统角色

![FacadePattern](../images/FacadePattern.jpg)
![seq_FacadePattern](../images/seq_FacadePattern.jpg)

## 享元模式(Flyweight Pattern)
运用共享技术有效地支持大量细粒度对象的复用。系统只使用少量的对象，而这些对象都很相似，状态变化很小，可以实现对象的多次复用。由于享元模式要求能够共享的对象必须是细粒度对象，因此它又称为轻量级模式，它是一种对象结构型模式。


享元模式包含如下角色：
- Flyweight: 抽象享元类,声明一个接口，通过它可以接受并作用于外部状态；
- ConcreteFlyweight: 具体享元类,实现了抽象享元接口，其实例称为享元对象；
- UnsharedConcreteFlyweight: 非共享具体享元类,不能被共享的抽象享元类的子类；
- FlyweightFactory: 享元工厂类,用于创建并管理享元对象，它针对抽象享元类编程，将各种类型的具体享元对象存储在一个享元池中。
![FlyweightPattern](../images/FlyweightPattern.jpg)
![seq_FlyweightPattern](../images/seq_FlyweightPattern.jpg)


## 代理模式(Proxy Pattern) 
给某一个对象提供一个代 理，并由代理对象控制对原对象的引用。代理模式的英 文叫做Proxy或Surrogate，它是一种对象结构型模式。

代理模式包含如下角色：
- Subject: 抽象主题角色,声明了真实主题和代理主题的共同接口；
- Proxy: 代理主题角色,包含对真实主题的引用，从而可以在任何时候操作真实主题对象;
- RealSubject: 真实主题角色,定义了代理角色所代表的真实对象，在真实主题角色中实现了真实的业务操作，客户端可以通过代理主题角色间接调用真实主题角色中定义的方法
![ProxyPattern](../images/ProxyPattern.jpg)
![seq_ProxyPattern](../images/seq_ProxyPattern.jpg)


# 行为型模式
行为型模式(Behavioral Pattern)是对在不同的对象之间划分责任和算法的抽象化。

行为型模式不仅仅关注类和对象的结构，而且重点关注它们之间的相互作用。

## 观察者模式(Observer Pattern)
定义对象间的一种一对多依赖关系，使得每当一个对象状态发生改变时，其相关依赖对象皆得到通知并被自动更新。观察者模式又叫做发布-订阅（Publish/Subscribe）模式、模型-视图（Model/View）模式、源-监听器（Source/Listener）模式或从属者（Dependents）模式。

观察者模式是一种对象行为型模式。

![ObeserverPattern](../images/ObeserverPattern.jpg)
![seq_ObeserverPattern](../images/seq_ObeserverPattern.jpg)

## 策略模式(Strategy Pattern)
定义一系列算法，将每一个算法封装起来，并让它们可以相互替换。策略模式让算法独立于使用它的客户而变化，也称为政策模式(Policy)。

- Context:环境类在解决某个问题时可以采用多种策略，在环境类中维护一个对抽象策略类的引用实例；
- Strategy:抽象策略类为所支持的算法声明了抽象方法，是所有策略类的父类；
- ConcreteSrategy:具体策略类实现了在抽象策略类中定义的算法。

![StrategyPattern](../images/StrategyPattern.jpg)
![seq_StrategyPattern](../images/seq_StrategyPattern.jpg)

## 命令模式(Command Pattern)
将一个请求封装为一个对象，从而使我们可用不同的请求对客户进行参数化；对请求排队或者记录请求日志，以及支持可撤销的操作。命令模式是一种对象行为型模式，其别名为动作(Action)模式或事务(Transaction)模式。

- Command: 抽象命令类
- ConcreteCommand: 具体命令类
- Invoker: 调用者
- Receiver: 接收者
- Client:客户类

![CommandPattern](../images/CommandPattern.jpg)
![seq_CommandPattern](../images/seq_CommandPattern.jpg)

## 状态模式(State Pattern) 
允许一个对象在其内部状态改变时改变它的行为，对象看起来似乎修改了它的类。其别名为状态对象(Objects for States)，状态模式是一种对象行为型模式。
- 环境类又称为上下文类，它是拥有状态的对象，在环境类中维护一个抽象状态类State的实例，这个实例定义当前状态，在具体实现时，它是一个State子类的对象，可以定义初始状态；
- 抽象状态类用于定义一个接口以封装与环境类的一个特定状态相关的行为；
- 具体状态类是抽象状态类的子类，每一个子类实现一个与环境类的一个状态相关的行为，每一个具体状态类对应环境的一个具体状态，不同的具体状态类其行为有所不同。
![StatePattern](../images/StatePattern.jpg)
![seq_StatePattern](../images/seq_StatePattern.jpg)

