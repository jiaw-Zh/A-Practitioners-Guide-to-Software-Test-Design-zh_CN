## 不可能测试所有组合

在 Robert Binder 的经典书籍 *Testing Object-Oriented Systems* 中，他提出一个不可能测试所有组合的例子：

```java
 int blech (int j) {
    j = j -1;     // should be j = j + 1
    j = j / 30000;
    return j;
    }
```

注意程序中第二行是错误的，函数接收 j 后减 1 再赋值给 j，最后除以 30000（整除，余数忽略）返回。如果在16位机器上，输入范围是 -32768 到 32767，那么这个小小的程序就需要 65536 个输入来测试（你们公司的程序肯定比这个大多了），你有这个时间和精力测试这 65536 个用例吗？当然不行，那么我们选择什么样的输入呢？考虑以下输入及其检测此缺陷的能力：

| 输入（j） | 预期结果 | 实际结果 |
| --------- | -------- | -------- |
| 1         | 0        | 0        |
| 42        | 0        | 0        |
| 40000     | 1        | 1        |
| -64000    | -2       | -2       |


哎呀！看到了吗，所选的测试用例均未检测到此缺陷，实际上只有四个输入值可以发现这个问题。四个全部选中的几率有多大呢？选中四个之中某一个的几率又有多大？你彩票中奖的几率有多大？你对这三个问题的回答都一样吗？