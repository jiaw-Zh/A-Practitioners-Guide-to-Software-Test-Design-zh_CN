## 简介

等价类划分法是一种能够将测试用例的数量减少到可管理的水平，同时保持合理测试覆盖率的技术。几乎所有的测试人员都曾下意识的使用过这种简单的技术，尽管他们可能不知道它是一种正式的测试设计方法。许多测试人员已经从逻辑上推断出它的用法，而其他人发现它仅仅是因为没有时间进行更彻底的测试。

考虑下面这种情况，我们正在为人力资源系统编写一个模块，该模块决定如何根据一个人的年龄处理就业申请。规则如下：

```
0 –16 ： 不予雇佣
16–18 ： 只能兼职雇用
18–55 ： 可以聘请为全职员工
55–99 ： 不予雇佣
```

 **注解：** 如果您发现这些规则存在问题，请不要担心，这样写是有目的的，将在下一章中修复。

&emsp; **观察**

&emsp; 有了这些规则，我们的组织就不会雇用 [Doogie Houser, M.D.](https://zh.wikipedia.org/wiki/%E5%B0%BC%E7%88%BE%C2%B7%E6%9F%8F%E5%BE%B7%E7%83%88%C2%B7%E5%A4%8F%E9%87%8C%E6%96%AF) (电视剧《天才小医生》主角) 或 Col. Harlan Sanders(肯德基的创始人)，一个太年轻，另一个太老。

我们是否需要使用以下年龄段来测试上面的规则：0, 1, 2, 3, 4, 5, 6, 7, 8, ..., 90, 91, 92, 93, 94, 95, 96, 97, 98, 99。如果我们有很多时间当然是可以的（我不介意这种令人麻木的重复，我们是按小时计酬的）。如果程序是下面这样实现的，我们需要测试所有年龄（如果你没有编程背景，也不要担心，这些例子很简单，只需阅读代码，你就会懂它的意思）。

```java
If (applicantAge == 0) hireStatus="NO";
    If (applicantAge == 1) hireStatus="NO";
    …
    If (applicantAge == 14) hireStatus="NO";
    If (applicantAge == 15) hireStatus="NO";
    If (applicantAge == 16) hireStatus="PART";
    If (applicantAge == 17) hireStatus="PART";
    If (applicantAge == 18) hireStatus="FULL";
    If (applicantAge == 19) hireStatus="FULL";
    …
    If (applicantAge == 53) hireStatus="FULL";
    If (applicantAge == 54) hireStatus="FULL";
    If (applicantAge == 55) hireStatus="NO";
    If (applicantAge == 56) hireStatus="NO";
    …
    If (applicantAge == 98) hireStatus="NO";
    If (applicantAge == 99) hireStatus="NO";
```

鉴于此实现，任何一组测试通过的用例都不能告诉我们下一次测试可以执行什么样的用例，它可能会通过，也可能会失败。

幸运的是，程序员不会这么写程序（至少不会经常这么写），一个好点的程序员也许是这样写的：

```java
If (applicantAge >= 0 && applicantAge <=16)
        hireStatus="NO";
If (applicantAge >= 16 && applicantAge <=18)
        hireStatus="PART";
If (applicantAge >= 18 && applicantAge <=55)
        hireStatus="FULL";
If (applicantAge >= 55 && applicantAge <=99)
        hireStatus="NO";
```

很明显，对于上面这个常见实现的第一个要求，我们不必测试 0-16 的所有数字，只需要测试其中的一个数字就可以了，那么选什么数呢？所有这个范围的数字任何一个都可以。其他范围也是一样。这里的范围就可以定义为**等价类**。一个等价类包含一组数据，这些数据对程序或模块来说都是一样的。在测试中，类别中的任一数据都是和其他数据等价的。具体来说，我们希望：

- 如果一个等价类中的一条用例发现了缺陷，那么这个等价类中的所有其他测试用例都能够发现同样的缺陷。
- 如果一个等价类中的一条用例未发现缺陷，那么这个等价类中的所有其他测试用例也无法发现缺陷。

&nbsp;&nbsp; **关键点：** 如果你认为某一组用例形成了一个等价类，那么：

- &emsp; 他们都测试同样的东西。
- &emsp; 其中一条用例发现一个 bug，其他的很可能也会。
- &emsp; 其中一条用例没有发现 bug，其他的可能也不会。

&emsp;&emsp;&emsp; Cem Kaner  `Testing Computer Software`

当然，这种方法假设存在一个定义了各种等价类的规范，它还假设程序员没有做一些奇怪的事情，比如

```java
If (applicantAge >= 0 && applicantAge <=16)
            hireStatus="NO";
If (applicantAge >= 16 && applicantAge <=18)
            hireStatus="PART";
If (applicantAge >= 18 && applicantAge <=41)
            hireStatus="FULL";
// strange statements follow
If (applicantAge == 42 && applicantName == "Lee")
            hireStatus="HIRE NOW AT HUGE SALARY";
If (applicantAge == 42 && applicantName <> "Lee")
            hireStatus="FULL";
// end of strange statements

If (applicantAge >= 43 && applicantAge <=55)
            hireStatus="FULL";
If (applicantAge >= 55 && applicantAge <=99)
            hireStatus="NO";
```

使用等价类方法，我们将测试用例的数量从 100 个（测试每个年龄）减少到 4 个（每个等价类测试一个年龄）——这是一个显著的节约。

现在，我们准备好开始测试了吗？也许还没有，我们需要为像 969、-42、FRED 和 &$#!@ 这样的无效输入值构建用例吗？正如任何优秀的顾问都会告诉你的那样，答案是：“这要看情况”。为了理解这个答案，我们需要了解一种来自面向对象编程的方法，称为契约式设计。

 **注解：** 根据圣经，Methuselah 是在969岁死的，感谢 Gideons 创造了这么好记的数据而不需要上网查。

在法律上，契约是两个个体或者团体之间的有法律效力的协议，定义双方能做什么和不能做什么，各自承诺形成互利。

在契约式设计中，模块（面向对象中叫方法），定义有前置条件和后置条件。后置条件定义了模块需要做什么（计算一个值，打开一个文件，打印一个报表，更新数据库记录，改变系统状态等）。前置条件定义模块要求以便达成后置条件。比如：你有一个模块叫做openFile，它需要做什么？打开一个文件。打开文件的前提条件是什么？第一，文件时存在的。第二，我们需要提供文件名称或其他可识别信息。第三，该文件可读，他不能被其他进程打开了。第四，我们需要有权限。等等。前置和后置条件形成了该模块和其他调用模块的契约。

基于契约的测试基于契约式设计哲学，它主要针对符合前置条件的情况设计用例，比如，对于openFile模块我们不会测试不存在文件下的情况，理由很简单，如果文件不存在，openFile没有承诺需要工作，如果它没有声明在特殊条件下能够工作，那么它没有必须要测试这种条件下的情况。

关于契约式设计希望得到更多信息参考： Bertrand Meyer's book 《Object-Oriented Software Construction》 

这种观点下，测试人员经常反对。是的，他们同意模块没有声明在这种情况下工作，但是假设在产品中前置条件违反了该怎么办？系统应该做什么？得到一个拼错的词？还是冒个烟？

不同于契约式设计的方法是防御式设计，在这种情况下，模块设计为可以接受任何输入。如果正常的前置条件遵循了，那么模块会达成正常的后置条件。如果前置条件没有遵循，模块会通知调用者，返回一个错误码，抛个异常等。这个通知就是模块其他的后置条件。基于此方法我们定义防御式测试：基于正常和异常前置条件测试方法。

 **看点：** 我们课堂的学生Fred说，他不在乎什么设计，我们经常用的就是防御式测试。当我问为什么？他说，如果模块不工作，应该是谁的责任？负责人还是测试人员？

怎么样应用于等价类划分？我们需要测试 `-42, FRED, and &$#!@` 吗？如果我们用契约式测试答案是否，如果我们是防御式测试，答案是是，问一下设计人员他们用什么设计方法，如果答案是契约或者防御式，那么你知道应该是什么测试。如果是“啊？”，意味着设计人员没有考虑过这个事情，那么你在集成测试初期将花费更多时间并且问题会复杂化。