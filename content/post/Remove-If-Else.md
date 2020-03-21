---
title: "Remove if Else"
date: 2020-03-18T20:48:57+08:00
draft: true
---

## 整体概览

最近在做项目开发的时候，有时候在项目中碰见稍微复杂一点的逻辑，就会出现很多的if-else，当时就在想，有没有什么方法？能够帮助我们去除掉多余的if和else呢？经过一番资料的搜寻，我找到了几种方法可以帮助我去除if,else,下面以计算器的常见方法：增加，删除，乘，除四种方法来进行举例。

不太好的代码
```java
public class Calculator {
    public int calculator(int a, int b, String operator) {
        int result = 0;
        if ("add".equals(operator)) {
            result += a + b;
        } else if ("multiply".equals(operator)) {
            result += a * b;
        } else if ("subtract".equals(operator)) {
            result += a - b;
        } else if ("divide".equals(operator)) {
            result += a / b;
        }
        return result;
    }
}
```

好了，现在我们来进行重构，消除多余的if,else。


## 利用策略加工厂的方式

第一步，生成一个计算接口
```java
public interface Operator {
    int operator(int a, int b);
}
```

然后生成AddOoperator这个具体的策略类
```java
public class AddOperator implements Operator {
    public int operator(int a, int b) {
        return a + b;
    }
}
```

再然后我们利用Map的key,value特性来实现传入不同的type，调用的计算方法不一样

```java
public class OperatorFactory {
    static Map<String, Operator> operatorMap = new HashMap<String, Operator>();

    static {
        operatorMap.put("add", new AddOperator());
        operatorMap.put("subtract", new SubtractOperator());
        operatorMap.put("divide", new DivideOperator());
        operatorMap.put("multiply", new MultiplyOperator());
    }

    static int operator(int a, int b, String type) {
        Operator operator = operatorMap.get(type);
        if (!Objects.isNull(operator)) {
            return operator.operator(a, b);
        } else {
            throw new NullPointerException("operator is null");
        }
    }
}
```

最后我们写测试方法来测试一下

```java
@Test
    public void testAdd() {
        Assert.assertEquals(3, OperatorFactory.operator(1, 2, "add"));
    }
```

到这，策略和工厂模式的实现方法来去除if,else的实现已经说完了，相信聪明的你对于其他四个实现也是信手拈来了吧。

## 利用枚举来消除

Java枚举也可以消除if,else，快来看看怎么实现吧？代码如下

```java
public enum OperatorEnum {
    add {
        @Override
        int operator(int a, int b) {
            return a + b;
        }
    },
    subtract {
        @Override
        int operator(int a, int b) {
            return a - b;
        }
    },
    multiply {
        @Override
        int operator(int a, int b) {
            return a * b;
        }
    },
    divide {
        @Override
        int operator(int a, int b) {
            return a / b;
        }
    };

    abstract int operator(int a, int b);
}
```

下面是测试用例：
```java
public class TestOperatorEnum {
    @Test
    public void testAdd() {
        Assert.assertEquals(3, OperatorEnum.valueOf("add").operator(1, 2));
    }

    @Test
    public void testDivide() {
        Assert.assertEquals(2, OperatorEnum.valueOf("divide").operator(6, 3));
    }

    @Test
    public void testMultiply() {
        Assert.assertEquals(6, OperatorEnum.valueOf("multiply").operator(2, 3));
    }

    @Test
    public void testSubtract() {
        Assert.assertEquals(1, OperatorEnum.valueOf("subtract").operator(3, 2));
    }
}
```

大功告成

## 总结

虽然类多了，但是扩展性强了，不用在一堆if,else的苦海中徘徊了。就到这里[我的代码地址](https://github.com/xuzhang0636/Remove-If-Else)