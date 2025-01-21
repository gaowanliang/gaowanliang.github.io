---
title: "后端开发人员面试问题"
summary: "看到GitHub上有人整理了一份后端开发人员面试问题，我觉得挺好的，就翻译过来了"
date: 2021-06-30 10:30:13
tags: ["面试"]
categories: ["面试"]
---

https://github.com/arialdomartini/Back-End-Developer-Interview-Questions

1. 通用问题
2. 开放式问题
3. 设计模式相关问题
4. 代码设计相关问题
5. 语言相关问题
6. Web 相关问题
7. 数据库相关问题
8. 非关系型数据库相关问题
9. 代码版本管理相关问题
10. 并发问题
11. 分布式系统相关问题
12. 软件生命周期和团队管理相关问题
13. 逻辑和算法相关问题
14. 软件架构相关问题
15. 面向服务架构(SOA)和微服务(Microservice)相关问题
16. 安全相关问题
17. 比尔盖茨式问题
18. 代码示例问题

## 通用问题:

1. 语言设计中空引用(null reference)的存在有什么问题？假设你想要将空引用的概念从你的首选语言中移除，可能导致什么结果？
2. 为什么函数式编程重要？什么时候适用函数式语言？
3. 设计(design)、架构(architecture)、功能(functionality)和美学(aesthetic)之间有什么区别？讨论一下。
4. 微软、谷歌、欧朋(opera)和火狐这类公司是如何从他们的浏览器中获利的？
5. 为什么打开 TCP 套接字有很大的开销？
6. 封装的重要性体现在哪儿？
7. 什么是实时系统？它与普通系统有什么区别？
8. 实时语言(real-time language)和堆内存分配(heap memory allocation)之间的关系是什么？
9. 不变性(Immutability)是指: (变量的)值只能在创建的时候被设置一次，之后就不能被改变。为什么不变性对写更加安全的代码有帮助？
10. 可变值(mutable values)和不可变值(immutable values)有哪些优缺点？
11. 什么是 O/R 阻抗失衡(Object-Relational impedence mismatch)？
12. 如果你需要使用缓存，你使用哪些原则来确定缓存的大小？
13. TCP 和 HTTP 有什么区别？
14. 在客户端渲染(client-side rendering)和服务端渲染(server-side rendering)之间，你是如何权衡的？
15. 如何在一个不可靠的协议之上构建一个可靠的通信协议？

## 开放式问题:

1. 为什么人们会抵制变化？
2. 如何向你的祖母解释什么是线程？
3. 作为一个软件工程师，你想要既要有创新力，又要产出具有可预测性。采用什么策略才能使这两个目标可以共存呢？
4. 什么是好的代码？
5. 解释什么是流(Streaming)和如何实现一个流？
6. 假设你的公司给你一周的时间，用来改善你和同事的生活: 你将如何使用这一周？
7. 本周你学了什么？
8. 所有的设计中都会有美学元素(aesthetic element)的存在。问题是，你认为美学元素是你的朋友还是敌人？
9. 列出最近你读过的 5 本书。
10. 假设目前有个大型公司（非常有钱），他们的开发流程是瀑布式流程（Waterfall），如果需要你在他们公司引入持续交付（Continue Devivery），你会怎么做？
11. 我们来谈谈"重复造轮子","非我发明症", "吃自己做出来的狗粮"的这些做法吧。
    (注: 重复造轮子: Reinventing the wheel; 非我发明症:Not Invented Here Syndrome; 吃自己做出来的狗粮: Eating Your Own Dog Food)
12. 在你当前的工作流中，什么事情是你计划下一步需要自动化的？
13. 为什么写软件是困难的？是什么使软件的维护变得困难？
14. 你更喜欢在全新项目（Green Field Project）上工作还是在已有项目(Brown Field Project)基础上工作？为什么？
15. 当你在浏览器地址栏输入 google.com 回车之后都发生了什么?
16. 当操作系统 CPU 处于空闲的时候，它可能在处理哪些事情？
17. 如何向一个 5 岁的孩子解释什么是 Unicode/数据库事务？
18. 如何维护单体架构(monolithic architecture)？
19. 一个"专业的开发者"意味着什么？
20. 软件开发是艺术、是技艺还是工程？你的观点是什么？
21. "喜欢这个的人也喜欢..."，如何在一个电子商务商店里实现这种功能？
22. 为什么在创新上，企业会比创业公司慢些？
23. 为什么说，你不应该尝试应用自己发明或者设计的密码学？

## 设计模式相关问题:

1. 请用一个例子表明，全局对象是邪恶的存在。

2. 假设你工作的系统不支持事务性，你会如何从头开始实现它？
3. 什么是好莱坞原则（Hollywood Principles）？
4. 关于迪米特法则(最少知识原则): 写一段代码违反它, 然后修复它。
   （注: 迪米特法则：the Law of Demeter, 最少知识原则： the Principle of Least Knowledge）
5. Active-Record 模式有什么限制和缺陷？
6. Data-Mapper 模式和 Active-Record 模式有什么区别？
7. 空对象模式(Null Object Pattern)的目的是什么？
8. 为什么组合(Composition)比继承(Inheritance)更好？
9. 什么是反腐败层(Anti-corruption Layer)?
10. 你可以写一个线程安全的单例(Singleton)类吗？
11. 数据抽象(Data Abstraction)能力是指能改变实现而不影响客户端的这种能力。请构造一个一个例子，违反这个特性，并且尝试修复它。
12. 你是如何处理依赖关系地狱(Dependency Hell)的？
13. 为什么说 goto 语句是恶魔般的存在？
14. 健壮性是进行软件设计时的一个通用原则，它建议 “发送时要保守，接收时要开放”。这也经常被写成，“做一个有耐心的读者，做一个谨慎的作者”。你能解释一些这背后的逻辑吗？
15. 译者注： "发送时要保守，接收时要开发"的原文是： "Be conservative in what you send, be liberal in what you accept"，有点类似于“严于律己，宽于待人”的意味。

## 代码设计相关问题:

1. 你在进行软件设计时会考虑软件测试吗？软件测试是如何影响软件设计的？
2. 内聚和耦合的区别是什么？
3. 重构在哪些场景下有用？
4. 代码中的注释有用吗？
5. 设计和架构有什么区别？
6. 为什么在测试驱动开发(TDD)中是先写测试，再写代码？
7. C++支持多继承，Java 允许类实现多个接口。这些特性对正交性有什么影响？使用多继承和使用多接口有区别吗？[这个问题来自 Andrew .Hunt 和 David Thomas 写的《程序员修炼之道》]
8. 在存储过程（Stored Procedures）中写业务逻辑有什么优缺点？

## 语言相关问题:

1. 告诉我你的首选语言的三个最坏的缺陷。
2. 为什么现在函数式编程这么越来越受关注？
3. 闭包是什么？它有什么用途？闭包和类有什么共同点？
4. 泛型有什么用途？
5. 什么是高阶函数？有什么用途？用你的首选语言写个例子出来。
6. 讨论一下，如何写一个循环，然后把它转换成递归函数，要避免易变性。
7. 有些语言将函数视为第一公民，这是什么意思？
8. 用一个例子说明匿名函数是有用的。
9. 什么是动态方法调度(Dynamic Method Dispatch)？
10. 名字空间(Namespace)有什么用？有什么可以替代它的吗？
11. 谈谈 Java 和 C#之间的互操作性(Interoperability) (任选其他两门语言都行)
12. 为什么很多软件工程师不喜欢 Java？
13. 你认为好的语言好在哪里？差的语言差在哪里？
14. 写两个函数，一个是"引用透明的(Referentially Transparent)"，另一个是"引用不透明的(Referentially Opaque)"。讨论之。
15. 什么是栈？什么是堆？
16. 为什么一个语言中，"函数是第一公民"是很重要的？
17. 模式匹配(Pattern Matching)和 Switch 语句(Switch clauses)的区别在哪儿？
18. 为什么有些语言设计上没有异常机制？这有什么优缺点？
19. 如果`Cat`是一个`Animal`, 那么`TaskCare<Cat>`是一个`TakeCare<Animal>`吗？

## web 相关问题:

1. 为什么"第一方 cookie(first-party cookie)"和"第三方 cookie(third-party cookie)"被如此不同的对待？

## 数据库相关问题:

2. 如果要你将一个项目从 MySQL 迁移至 PostgreSQL 中，你会如何迁移？
3. 为什么 SELECT \* FROM table WHERE field = null 不能匹配空的字段？
4. 什么是 ACID(原子性，一致性，隔离性，持久性)原则？
5. 你是如何进行数据库模式(Database schema)迁移的？
6. 延迟加载(lazy loading)是如何实现的？什么场景下有用？他有什么缺陷？
7. 什么是 N+1 问题？
8. 如何找出应用中开销最大的查询？

## 非关系型数据库相关问题:

1. 什么是最终一致性(Eventual Consistency)？
2. 关于 CAP 理论，举一些 CP、AP、CA 系统的例子。
3. NoSQL 是如何解决可伸缩性的挑战的？
4. 什么情况下你会使用类似于 MongoDB 的文档数据库而不是关系型数据库（如 Mysql 或者 PostgreSQL）？

## 代码版本管理相关问题:

1. 为什么在 Mercurial 或者 git 中(管理)分支比 SVN 容易？
2. 分散式版本控制系统（比如 git），相比集中式版本控制系统（如 svn）有哪些优势和劣势？
   (注:集中式版本控制系统: Centralized Version Control Systems；分散式版本控制系统: Distributed Version Control Systems)

3. 能描述一下什么是 GitHubFlow 和 GitFlow 工作流吗？
4. 什么是 rebase？
5. 为什么合并操作(merge)在 Mercurial 和 git 中比在 SVN 和 CVS 中容易？

## 并发问题:

1. 为什么我们需要并发呢？解释一下。
2. 为什么测试多线程/并发代码这么困难？
3. 什么是竞争条件（Race Condition）？用任何一个语言写一个例子。
4. 什么是死锁？用代码解释一下。
5. 什么是饿死？
6. 什么是 Wait-Free 算法？

## 分布式系统相关问题:

1. 怎么测试一个分布式系统？
2. 什么场景下你会在两个系统中采用异步通信机制？
3. 远程过程调用的通用缺点是什么？
4. 如果你为了可扩展性和鲁棒性而构建一个分布式的系统，分别在封闭安全的网络环境情况下，和地理上的位置不同但是网络环境不是封闭和安全的情况下，你会考虑什么不同的事情？
5. 在 Web 应用中如何管理容错性？在桌面端呢？
6. 在分布式系统中，如何处理故障？
7. 让我们来谈谈网络分裂(network partitions)后有的几种恢复的手段吧。
8. 你认为分布式计算中有哪些谬论？
9. 你在什么时候会使用 Request/Response 模式，什么时候使用 Publish/Subscribe 模式？

## 软件生命周期和团队管理相关问题:

1. 什么是敏捷（Agility）？
2. 你是如何处理遗留代码（Legacy Code）的？
3. 假设我是你们公司的 CEO，请向我解释什么是看板，并且说服我在它上面投资。
4. 敏捷（Agility）和瀑布（Waterfall）之间的最大区别是什么？
5. 作为团队管理者，你对会议太多这个问题是如何处理的？
6. 你会如何处理延期很长时间了的项目？
7. "个体与交互重于过程和工具"和"客户协作重于合同谈判"占了敏捷宣言（Agile Manifesto）的一半，谈论一下这两个观念。
8. 如果你是你们公司的 CTO，你会采取什么样的决策？
9. 你觉得项目经理有用吗？
10. 如果要你组织一个弹性工作制的开发团队（即没有强制工作时间的要求），并且假期制度是"按需休假"，你会如何做？
11. 你会如何管理一个人员流动非常高的团队？如何在不加薪的条件下说服团队成员不要离开？
12. 除了代码之外，你最关注你的同事的哪 3 项素质？
13. 关于代码，你最希望非技术人员能知道的的三件事是什么？

## 逻辑和算法相关问题:

1. 只用 LIFO 栈如何构造一个 FIFO 队列？只用 FIFO 队列如何构造一个 LIFO 栈？
2. 写一段有栈溢出的代码。
3. 写一个尾递归版本的阶乘函数。
4. 使用任何一个语言，写一个 REPL，功能是 echo 你输入的字符串。然后将它演化成一个逆波兰表达式的计算器。
5. 如果需要你设计一个文件系统磁盘碎片整理程序，你会如何设计？
6. 写一个生成随机迷宫的程序。
7. 写一段有内存泄漏的示例代码。
8. 随机生成一个的数字序列，里面每个数字都不同。
9. 写一个简单的垃圾回收系统。
10. 使用任何一门语言，写一个基本的消息代理。
11. 写一个基础的 web 服务器，然后画一张线路图，展示你将来还想要实现的功能。
12. 如何对一个 10GB 的文件进行排序？如果是 10TB 的数据，你会采用什么方法？
13. 请实现 rnd()函数

## 软件架构相关问题:

1. 什么情况下缓存是没用的，甚至是危险的？
2. 为什么事件驱动的架构能提高可扩展性(scalability)？
3. 什么样的代码是可读性强的代码？
4. 紧急设计(Emergent Design)和演化架构(Evolutionary Architecture)之间的区别是什么？
5. 横向扩展(scale out) vs 纵向扩展(scale up): 有什么区别？分别在什么场景下使用？
6. 分布式系统中如何处理"故障切换(failover)"和"用户会话(user session)"？
7. 什么是 CQRS(Command Query Responsibility Segregation)?他和最早的 Command-Query Separation 原则有什么区别？
8. 什么是三层架构？
9. 如何设计一个可扩展性高的系统？
10. 处理 C10k 问题的策略有哪些？
11. 如果让你来设计一个去中心化的 P2P 系统，你会如何设计？
12. 为什么 CGI 的扩展性不好？
13. 在设计系统时，你如何防止供应商依赖(Vendor Lock-in)？
14. 在可扩展性上，发布/订阅(Publish-Subscribe)模式有什么缺点？
15. 80 年代以后，CPU 有哪些变化？这些变化，对编程产生了什么影响？
16. 性能生命周期(performace lifecycle)中，你认为哪个部分是需要考虑进去的？ 如何管理？
17. 除了恶意攻击造成的拒绝服务现象以外，哪些设计或者架构上的问题会导致拒绝服务？
18. 性能和可扩展性之间有什么关系？
19. 什么时候紧耦合是 OK 的？
20. 一个系统要有什么特征才能适配云计算环境(Cloud Ready)？
21. Does unity of design imply an aristocracy of architects?

## 面向服务架构(SOA)和微服务(Microservice)相关问题:

1. 在 SOA 中，为什么长期存活的事务(Long-lived transation)不被看好，而 Saga 却被看好？
2. SOA 和 MicroService 之间有什么区别？
3. 我们来谈谈 Web 服务的版本管理、版本兼容性、重大变更管理这些事情吧.
4. 在 saga 中事务和补偿操作(compensation operation)之间的区别是什么？在 SOA 中呢？
5. 微服务不能做得太"微"，你认为什么时候微服务太"微"了？
6. MicroService 架构的优劣是什么？

## 安全相关问题:

1. 什么是双因素认证(Two Factor Authentication)？在一个已有的 Web 应用中，你如何实现这种机制？

## 比尔盖茨式问题:

1. 如果你把一面镜子放在扫描仪上，会发生什么？
2. 假设有一个和你完全一样的克隆人，而他是你的上司，你愿意和他工作吗？
3. 现在请你面试一下我。
4. 为什么 Quora 上的回答会比 Yahoo Answer 上的回答好？
5. 对手是现代语言，你的任务是要为 Cobol 辩护，你会如何进行？
6. 10 年后的你是什么样子？
7. 假设你是我老板，我被解雇了。你会如何通知我？
8. 我想要重构一个系统，而你想要从头重写。我们来争论一下该怎么弄吧。然后我们反转角色，再争论一下。
9. 老板要你对公司撒谎，你的反应是什么？
10. 如果你可以穿越到以前，你会给年轻时候的你什么建议？

## 代码示例问题:

1. 这段 Javascript 函数的输出是什么？

```js
function hookupevents() {
  for (var i = 0; i < 3; i++) {
    document
      .getElementById("button" + i)
      .addEventListener("click", function () {
        alert(i);
      });
  }
}
```

2. 关于类型擦除(Type Erasure)，这段 Java 代码的输出是什么？为什么？

```java
ArrayList<Integer> li = new ArrayList<Integer>();
ArrayList<Float> lf = new ArrayList<Float>();
if (li.getClass() == lf.getClass()) // evaluates to true
  System.out.println("Equal");
```

3. 你能指出哪儿有内存泄漏吗？

```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack() {
        elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        return elements[--size];
    }

    /**
     * Ensure space for at least one more element, roughly
     * doubling the capacity each time the array needs to grow.
     */
    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }
}
```

4. if 语句，或者更加通用点，条件表达式通常是过程式编程/命令式编程的形式。你能去掉这段代码中的 switch 语句，用面向对象的方式来修改这段代码吗？

```java
public class Formatter {

    private Service service;

    public Formatter(Service service) {
        this.service = service;
    }

    public String doTheJob(String theInput) {
        String response = service.askForPermission();
        switch (response) {
        case "FAIL":
            return "error";
        case "OK":
            return String.format("%s%s", theInput, theInput);
        default:
            return null;
        }
    }
}
```

5. 你能去掉这里的 if 语句，将它改成更加面向对象吗？

```java
public class TheService {
    private final FileHandler fileHandler;
    private final FooRepository fooRepository;

    public TheService(FileHandler fileHandler, FooRepository fooRepository) {
        this.fileHandler = fileHandler;
        this.fooRepository = fooRepository;
    }

    public String Execute(final String file) {

        final String rewrittenUrl = fileHandler.getXmlFileFromFileName(file);
        final String executionId = fileHandler.getExecutionIdFromFileName(file);

        if ((executionId == "") || (rewrittenUrl == "")) {
            return "";
        }

        Foo knownFoo = fooRepository.getFooByXmlFileName(rewrittenUrl);

        if (knownFoo == null) {
            return "";
        }

        return knownFoo.DoThat(file);
    }
}
```

6. 如何重构这段代码？

```java
function()
{
    HRESULT error = S_OK;

    if(SUCCEEDED(Operation1()))
    {
        if(SUCCEEDED(Operation2()))
        {
            if(SUCCEEDED(Operation3()))
            {
                if(SUCCEEDED(Operation4()))
                {
                }
                else
                {
                    error = OPERATION4FAILED;
                }
            }
            else
            {
                error = OPERATION3FAILED;
            }
        }
        else
        {
            error = OPERATION2FAILED;
        }
    }
    else
    {
        error = OPERATION1FAILED;
    }

    return error;
}
```
