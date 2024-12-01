
**背景事件**：近日，优衣库宣布不再使用新疆棉花，这一举措引发了广泛的社会讨论。消费者的反应和舆论的压力，让优衣库的决策迅速影响了市场和品牌形象。类似的，许多系统也面临着需要根据外部事件或状态的变化，做出即时反应的需求。


在软件设计中，**观察者模式（Observer Pattern）** 就是为了处理这种“状态变化与反应”的需求。它允许一个对象（主题）发生变化时，通知所有依赖它的对象（观察者），而不需要显式的调用，**即松耦合**地处理不同模块间的动态更新。


今天，我们将结合优衣库棉花事件，来讲解如何使用 **观察者模式** 来应对复杂业务中的事件驱动响应。


## 一、观察者模式概述


### 1\.1 观察者模式的定义


观察者模式属于**行为型设计模式**，其核心思想是：**当对象的状态发生改变时，所有依赖于它的对象都会收到通知并自动更新**。这种模式非常适用于当系统中的某一部分状态变化需要同时通知到其他部分的场景。


### 1\.2 观察者模式的组成部分


* **主题（Subject）**：状态变化的核心对象，负责管理所有的观察者并通知它们。
* **观察者（Observer）**：依赖于主题对象的变化，并作出响应。
* **通知机制**：通过注册和触发通知，观察者获得主题对象的状态变化。


在 **优衣库不使用新疆棉** 的事件中，**品牌决策**就像**主题对象**，而**消费者、舆论、媒体等**就是观察者。每当品牌作出决策，消费者的情感、舆论的反应都会发生变化，观察者（消费者）的情绪或行为会随之改变。


## 二、观察者模式与优衣库事件的类比


### 2\.1 事件背景


假设优衣库公司决定不再使用新疆棉，这一决策会触发不同的消费者、股东、媒体等不同的观察者产生反应：


* **消费者**可能对这一举措产生负面或正面的情绪，甚至会改变购买行为。
* **媒体**可能会通过报道引导舆论。
* **股东**可能对这一决策的影响做出反应，调整投资策略。


这些变化是**动态的**，且每当优衣库做出决策时，所有相关的观察者（消费者、媒体等）都会收到通知并做出相应反应。


### 2\.2 在系统设计中的应用


在类似的业务场景中，观察者模式非常有用。通过观察者模式，当主题（如优衣库品牌决策）发生变化时，所有需要响应变化的组件（如消费者、舆论）都会自动收到通知并执行相应的处理逻辑。这种设计使得不同的模块之间保持了松耦合的关系，不需要主题对象知道观察者的具体细节。


## 三、观察者模式的实现


接下来，我们通过代码来实现一个基于观察者模式的简单示例，模拟优衣库品牌决策引发的消费者反应。


### 3\.1 代码示例：实现观察者模式



```
// 主题类：优衣库品牌决策
import java.util.ArrayList;
import java.util.List;

// 主题接口
interface BrandDecision {
    void addObserver(ConsumerObserver observer);
    void removeObserver(ConsumerObserver observer);
    void notifyObservers();
}

// 具体主题：优衣库的品牌决策
class UniqloBrandDecision implements BrandDecision {
    private List observers = new ArrayList<>();
    private String decision;

    @Override
    public void addObserver(ConsumerObserver observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(ConsumerObserver observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers() {
        for (ConsumerObserver observer : observers) {
            observer.update(decision);
        }
    }

    public void makeDecision(String decision) {
        this.decision = decision;
        System.out.println("优衣库品牌做出决策: " + decision);
        notifyObservers();
    }
}

// 观察者接口：消费者反应
interface ConsumerObserver {
    void update(String decision);
}

// 具体观察者：消费者
class Consumer implements ConsumerObserver {
    private String name;

    public Consumer(String name) {
        this.name = name;
    }

    @Override
    public void update(String decision) {
        System.out.println(name + " 收到决策通知，决定如何行动： " + decision);
        if (decision.contains("不使用新疆棉")) {
            System.out.println(name + " 表示失望，可能不再购买！");
        } else {
            System.out.println(name + " 觉得决策不错，继续购买！");
        }
    }
}

```

### 3\.2 代码分析


在上面的代码中：


* **UniqloBrandDecision** 类是主题，它包含了消费者的观察者列表。当优衣库作出决策时，会调用 `makeDecision()` 方法，通知所有的观察者（消费者）进行反应。
* **Consumer** 类是具体的观察者，当品牌决策改变时，它会根据决策内容做出不同的反应。


### 3\.3 客户端代码：模拟消费者反应



```
public class ObserverPatternExample {
    public static void main(String[] args) {
        // 创建品牌决策主题
        UniqloBrandDecision uniqloDecision = new UniqloBrandDecision();

        // 创建消费者观察者
        Consumer consumer1 = new Consumer("消费者A");
        Consumer consumer2 = new Consumer("消费者B");

        // 消费者注册到品牌决策
        uniqloDecision.addObserver(consumer1);
        uniqloDecision.addObserver(consumer2);

        // 优衣库做出决策
        uniqloDecision.makeDecision("优衣库决定不再使用新疆棉，改用其他地区棉花");

        // 优衣库做出新的决策
        uniqloDecision.makeDecision("优衣库决定继续使用新疆棉");
    }
}

```

### 3\.4 输出结果



```
优衣库品牌做出决策: 优衣库决定不再使用新疆棉，改用其他地区棉花
消费者A 收到决策通知，决定如何行动： 优衣库决定不再使用新疆棉，改用其他地区棉花
消费者A 表示失望，可能不再购买！
消费者B 收到决策通知，决定如何行动： 优衣库决定不再使用新疆棉，改用其他地区棉花
消费者B 表示失望，可能不再购买！
优衣库品牌做出决策: 优衣库决定继续使用新疆棉
消费者A 收到决策通知，决定如何行动： 优衣库决定继续使用新疆棉
消费者A 觉得决策不错，继续购买！
消费者B 收到决策通知，决定如何行动： 优衣库决定继续使用新疆棉
消费者B 觉得决策不错，继续购买！

```

## 四、观察者模式的优势与应用


### 4\.1 优点


* **松耦合**：主题和观察者之间没有紧密的耦合关系。主题只知道它需要通知观察者，而不关心观察者的具体实现。
* **动态响应**：观察者可以根据主题的状态变化动态调整自己的行为，非常适合事件驱动的系统。
* **易于扩展**：添加新的观察者（如新消费者）非常方便，不需要改变现有的代码。


### 4\.2 缺点


* **通知开销**：如果观察者数量很多，通知机制可能会成为性能瓶颈。
* **依赖顺序**：观察者的顺序可能影响到通知的处理流程，某些场景下可能需要额外的控制。


## 五、总结


通过优衣库不使用新疆棉事件，我们可以清晰地看到观察者模式在应对复杂决策和动态反应中的优势。当品牌作出决策时，消费者和舆论等多个观察者会收到通知并做出反应，**无缝的事件驱动机制**让这种决策的传播变得高效且灵活。


如果你的系统中涉及到多个模块需要对某一变化做出响应，观察者模式无疑是一个非常合适的解决方案。它能够帮助你保持系统的松耦合，快速响应变化，同时确保灵活的扩展性。




---


希望这篇文章能够帮助你理解如何通过**观察者模式**应对复杂的业务决策和系统响应！如果你对其他设计模式的应用感
作者：[代老师的编程课](https://github.com)
出处：[https://zthinker.com/](https://github.com):[wgetcloud加速器官网下载](https://longdu.org)
如果你喜欢本文,请长按二维码，关注 **Java码界探秘**
.![代老师的编程课](https://img2024.cnblogs.com/other/124822/202411/124822-20241130140155823-1509240958.jpg)


