### 为什么关心java8

Java 8所做的改变，在许多方面比Java历史上任何一次改变都深远。而且好消息是，这些改变使得编程更容易

```java
Collections.sort(inventory, new Comparator<Apple>() {
  public int compare(Apple a1, Apple a2) {
    return a1.getWeight().compareTo(a2.getWeight());
  }
});

==> 
  
inventory.sort(comparing(Apple::getWeight))
```

Java8改变了并行的解决方案

- Stream API
- 向方法传递代码的技巧
- 接口中的默认方法

Java8提供了一个新的API（称为流，Stream），支持许多处理数据的并行操作，思路和数据库查询语言中的思路类似--用更高级的方式表达想要的东西，而由实现来选择最佳执行机制。这样就可以避免用synchronized编写代码，这一代码不仅容易出错，而且在多核CPU上执行所需的成本也更高

在Java 8中加入Streams可以看作把另外两项扩充加入Java 8 的直接原因:把代码传递给方法的简洁方式(方法引用、Lambda)和接口中的默认方法

#### 流处理

流是一系列数据项，一次只生成一项。程序可以从输 入流中一个一个读取数据项，然后以同样的方式将数据项写入输出流。一个程序的输出流很可能 是另一个程序的输入流

#### 用行为参数化把代码传递给方法

通过API来传递代码的能力：Java 8增加了把方法(开发者的代码)作为参数传递给另一个方法的能力

Stream API就是构建在通过传 递代码使操作行为实现参数化的思想上的，当把compareUsingCustomerId传进去就把sort的行为参数化了

```java
public int compareUsingCustomerId(String inv1, String inv2) {
  ...
}
```

#### 并行与共享的可变数据

行为必须能够同时对不同的输入安全地执行。一般情况下这就意味着，写代码时不能访问共享的可变数据。这些函数有时被称为“纯函数”或“无副作用函数”或“无状态函数”

Java 8的流实现并行比Java现有的线程API更容易，因此，尽管可以使用synchronized来打破“不能有共享的可变数据”这一规则，但这相当于是在和整个体系作对，因为它使所有围绕这 一规则做出的优化都失去意义了。在多个处理器内核之间使用synchronized，其代价往往比 预期的要大得多，因为同步迫使代码按照顺序执行，而这与并行处理的宗旨相悖

这两个要点(没有共享的可变数据，将方法和函数即代码传递给其他方法的能力)是我们平常所说的函数式编程范式的基石。

与此相反，在命令式编程范式中，写的程序则是一系列改变状态的指令。“不能有共享的可变数据”的要求意味着，一个方法是可以通过它将参数值转换为结果的方式完全描述的;换句话说，它的行为就像一个数学函数，没有可见的副作用

#### Java中的函数

与此相反，在命令式编程 范式中，你写的程序则是一系列改变状态的指令。“不能有共享的可变数据”的要求意味着，一 个方法是可以通过它将参数值转换为结果的方式完全描述的;换句话说，它的行为就像一个数学函数，没有可见的副作用

Java 8中新增了函数——值的一种新形式。它有助于使用流，有了它，Java 8可以进行多核处理器上的并行编程

- 方法和Lambda作为一等公民

  - Java 8的第一个新功能是方法引用

  比方说想要筛选一个目录中的所有隐藏 文件，需要编写一个方法，然后给它一个File，它就会告诉你文件是不是隐藏的。File类里面有一个叫作isHidden的方法。我们可以把它看作一个函数，接受一个File，返回一个布 尔值。但要用它做筛选，你需要把它包在一个FileFilter对象里，然后传递给File.listFiles 方法，如下所示:

  ```java
  File[] hiddenFiles = new File(".").listFiles(new FileFilter() {
    public boolean accept(File file) {
      return file.isHidden();
    }
  });
  
  File[] hiddenFiles = new File(".").listFiles(File::isHidden)
  ```

  **Java 8的方法引用::语法(即“把这 个方法作为值”)将其传给listFiles方法**

  好处一是代码现在读起来更接近问题的陈述了。方法不再是二等值了。与用对象引用传递对象类似(对象引用是用new创建的)，在Java 8里写下 File::isHidden的时候就创建了一个方法引用，同样可以传递它。

  - Lambda - 匿名函数

  除了允许(命名)函数成为一等值外，Java 8还体现了更广义的将函数作为值的思想，包括 Lambda（或匿名函数）

- 传递代码

设你有一个Apple类，它有一个getColor方法，还有一个变量inventory保存着一个Apples的列表。可能想要选出所有的绿苹果，并返回一个列表。通常我们用筛选(filter)一词来表达这个概念。在Java 8之前可能会写这样一个方法filterGreenApples:

```java
public static List<Apple> filterGreenApples(List<Apple> inventory) {
  List<Apple> result = new ArrayList<>();
  for (Apple apple: inventory) {
    if ("green".equals(apple.getColor())) {
      result.add(apple);
    }
  }
  return result;
}

public static List<Apple> filterHeavyApples(List<Apple> inventory) {
  List<Apple> result = new ArrayList<>();
  for (Apple apple: inventory) {
    if (apple.getWeight() > 150) {
      result.add(apple);
    }
  }
  return result;
}
```

Java 8会把条件代码作为参数传递进去，这样可以避免filter方法 出现重复的代码。现在你可以写:

```java
public static boolean isGreenApple(Apple apple) {
  return "green".equals(apple.getColor());
}

public static boolean isHeavyApple(Apple apple) {
  return apple.getWeight() > 150;
}

public interface Predict<T> {
  boolean test(T t);
}

static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> p) {
  List<Apple> result = new ArrayList<>();
  for (Apple apple: inventory) {
    if (p.test(apple)) {
      result.add(apple);
    }
  }
  return result;
}

filterApples(inventory, Apple::isGreenApple;
filterApples(inventory, Apple::isHeavyApple);
```

> 什么是谓词?
>
> 前面的代码传递了方法Apple::isGreenApple(它接受参数Apple并返回一个 boolean)给filterApples，后者则希望接受一个Predicate<Apple>参数。谓词(predicate) 在数学上常常用来代表一个类似函数的东西，它接受一个参数值，并返回true或false
>
> Java 8也会允许你写Function<Apple,Boolean>，但用Predicate<Apple>是更标准的方式，效率也会更高一 点儿，这避免了把boolean封装在Boolean里面

- 从传递方法到Lambada

把方法作为值来传递显然很有用，但要是为类似于isHeavyApple和isGreenApple这种可 能只用一两次的短方法写一堆定义有点儿烦人。不过Java 8也解决了这个问题，它引入了一套新 记法(匿名函数或Lambda)，让你可以写

```java
filterApple(inventory, (Apple a) -> "green".equals(a.getColor()));

filterApple(inventory, (Apple a) -> a.getWeight() > 150 );

filterApple(inventory, (Apple a) -> a.getWeight() < 80 || "brown".equals(a.getColor()));
```

甚至都不需要为只用一次的方法写定义;代码更干净、更清晰，因为你用不着去找自己到底传递了什么代码。但要是Lambda的长度多于几行(它的行为也不是一目了然)的话， 那你还是应该用方法引用来指向一个有描述性名称的方法，而不是使用匿名的Lambda。你**应该以代码的清晰度为准绳**

#### 流

几乎每个Java应用都会制造和处理集合。但集合用起来并不总是那么理想。比方说，你需要从一个列表中筛选金额较高的交易，然后按货币分组。你需要写一大堆套路化的代码来实现这个 数据处理命令，如下所示:

```java
Map<Currency, List<Transaction>> transactionsByCurrencies = 
  new HashMap<>();
for (Transaction transaction: transactions) {
  if (transaction.getPrice() > 1000) {
    Currency currency = transaction.getCurrency();
    List<Transaction> transactionsForCurrency =
      transactionsByCurrencies.get(currency);
    if (transactionsForCurrency == null) {
      transactionsForCurrency = new ArrayList<>();
      transactionsByCurrencies.put(currency, transactionsForCurrency);
    }
    transactionsForCurrency.add(transaction);
  }
}
```

```java
import static java.util.stream.Collection.toList;

Map<Currency, List<Transaction>> transactionsByCurrencies = 
  transactions.strram()
  	.filter((Transaction t) -> t.getPrice() > 1000)
  	.collect(groupingBy(Transaction::getCurrency));
```

有了Steam API，你根本用不着操心循环的事情。数据处理完全是在库内部进行的。我们把这种思想叫做内部迭代。

#### 多线程并非易事

java8使用Stream API解决了这两个问题：集合处理时的套路和晦涩，以及难以利用多核。

这样设计的第一个原因是：有许多反复出现的数据处理模式，类似于之前的filterApples或SQL等数据库查询语言里熟悉的操作，如果在库里有这些就会很方便，根据标准筛选数据，提取数据，或给数据分组等。

第二个原因是：这类操作常常可以并行化。

新的Stream API和Java现有的集合API的行为差不多：它们都能访问数据项目的序列。Collection主要是为了存储和访问数据，而Stream则主要用于描述对数据的计算。这里的关键点在于Stream允许并提倡并行处理一个Stream中的元素。筛选一个Collection的最快方法常常是将其转换为Stream，进行并行处理，然后再转换回List。

- 顺序处理

```java
import static java.util.stream.Collectors.toList;

List<Apple> heavyApples =
  inventory.stream().filter((Apple a) -> a.getWeight() > 150)
  	.collect(toList());
```

- 并行处理

```java
import static java.util.stream.Collectors.toList;

List<Apple> heavyApples =
  inventory.parallelStream().filter((Apple a) -> a.getWeight() > 150)
  	.collect(toList());
```

#### 默认方法

```java
List<Apple> heavyApple1 =
  inventory.stream().filter((Apple a) -> a.getWeight() > 150)
  	.collect(toList());
List<Apple> heavyApple2 = 
  inventory.parallelStream().filter((Apple a) -> a.getWeight() > 150)
  	.collect(toList());
```

在Java8之前，`List<T>`并没有stream或parallelStream方法，他实现的`Collection<T>`接口也没有。没有这些方法代码无法编译。最简单的解决方案就是让java8的设计者把stream方法加入Collection接口，并加入ArrayList类的实现。但是这意味着所有的实体类都必须为其提供一个实现。

java8的解决方案是借口可以包含实现类没有提供实现的方法签名。这就给接口设计者提供了一个扩充接口的方式，而不会破坏现有的代码。java8在接口声明中使用新的default关键字来表示这一点。

如在java8中，可以直接对List调用sort方法。它是用java8 List接口中的默认方法实现的，它会调用Collections.sort静态方法

```java
default void sort(Comparator<? super E> c) {
  Collections.sort(this, c);
}
```

这意味着List的任何实体类都不需要显式实现sort，而在以前的java版本中，除非提供了sort的实现，否则这些实体类在重新编译时都会失败

#### 来自函数式编程的其他好思想

- Optional

通过使用更多的描述性数据类型来避免null。在Java8里有一个`Optional<T>`类，如果能一致地使用它的话，就可以帮助避免出现NullPointer异常。它是一个容器对象，可以包含也可以不包含一个值。`Optional<T>`中有方法来明确处理值不存在的情况，这样就可以避免NullPointer异常了。它使用类型系统，允许表明开发者知道一个变量可能会没有值

- 结构模式匹配

为什么Java中的switch语句应该限于原始类型值和Strings呢?函数式语言倾向于允许 switch用在更多的数据类型上，包括允许模式匹配(在Scala代码中是通过match操作实现的)。 在面向对象设计中，常用的访客模式可以用来遍历一组类(如汽车的不同组件:车轮、发动机、底盘等)，并对每个访问的对象执行操作。模式匹配的一个优点是编译器可以报告常见错误，如:“Brakes类属于用来表示Car类的组件的一族类。你忘记了要显式处理它。”