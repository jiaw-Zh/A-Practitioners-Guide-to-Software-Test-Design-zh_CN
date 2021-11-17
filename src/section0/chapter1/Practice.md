### 练习

哪四个输入能够发现隐藏的缺陷？你是如何确定它们的？ 作为寻找其他缺陷的方法，这对您有何启发？

```java
 int blech (int j) {
    j = j -1;     // should be j = j + 1
    j = j / 30000;
    return j;
    }
```

