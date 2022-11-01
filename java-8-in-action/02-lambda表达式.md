### Lambda表达式

可以把Lambda 表达式看作匿名功能，它基本上就是没有声明名称的方法，但和匿名类一样，它也可以作为参 数传递给一个方法。

#### lambda管中窥豹

可以把Lambda表达式理解为简洁地表示可传递的匿名函数的一种方式:它没有名称，但它 有参数列表、函数主体、返回类型，可能还有一个可以抛出的异常列表。

- 匿名：它不像普通的方法那样有一个明确的名称:写得少而想得多
- 函数：Lambda函数不像方法那样属于某个特定的类。但和方 法一样，Lambda有参数列表、函数主体、返回类型，还可能有可以抛出的异常列表
- 传递：Lambda表达式可以作为参数传递给方法或存储在变量中
- 简洁：无需像匿名类那样写很多模板代码

```java
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());

Comparator<Apple> byWeight = new Comparator<Apple>() {
  public int compare(Apple a1, Apple a2) {
    return a1.getWeight().compareTo(a2.getWeight());
  }
}

Comparator<Apple> byWeight = (Apple a1, Apple a2) ->
  a1.getWeight().compareTo(a2.getWeight());
```

Lambda表达式有三个部分：

- 参数列表：采用了Comparator中compare方法的参数，两个Apple
- 箭头：箭头`->`把参数列表与Lambda主体分隔开
- Lambda主体：比较两个Apple的重量。表达式就是Lambda的返回值了。

五个有效的Lambda表达式

```java
(String s) -> s.length();
(Apple a) -> a.getWeight() > 150;
(int x, int y) -> {
  System.out.println("Result:");
  System.out.println(x+y);
};
() -> 42;
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());

// 布尔表达式
(List<String> list) -> list.isEmpty()
// 创建对象
() -> new Apple(10)
// 消费一个对象
(Apple a) -> {
  System.out.println(a.getWeight());
}
// 从一个对象中选择/抽取
(String a) -> s.length();
// 组合两个值
(int a, int b) -> a * b;
// 比较两个对象
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

#### 在哪里以及如何使用Lambda

- 可以在函数式接口上使用Lambda表达式
- 可以把Lambda表达式作为第二个参数传给filter方法

1. 函数式接口

函数式接口就是只定义一个抽象方法的接口

```java
public interface Comparator<T> {
    int compare(T o1, T o2);
}
public interface Runnable{
    void run();
}
public interface ActionListener extends EventListener{
    void actionPerformed(ActionEvent e);
}
public interface Callable<V>{
    V call();
}
public interface PrivilegedAction<V>{
    V run();
}
```

用函数式接口可以干什么呢?Lambda表达式允许你直接以内联的形式为函数式接口的抽象方法提供实现，并把整个表达式作为函数式接口的实例(具体说来，是函数式接口一个具体实现 的实例)。你用匿名内部类也可以完成同样的事情，只不过比较笨拙:需要提供一个实现，然后 再直接内联将它实例化。下面的代码是有效的，因为Runnable是一个只定义了一个抽象方法run 的函数式接口:

```java
Runnable r1 = () -> System.out.println("Hello World 1");
Runnable r2 = new Runnable(){
  public void run(){
    System.out.println("Hello World 2");
  }
};
public static void process(Runnable r){
  r.run();
}
process(r1);
process(r2);
process(() -> System.out.println("Hello World 3"));
```

2. 函数描述符

函数式接口的抽象方法的签名基本上就是Lambda表达式的签名。我们将这种抽象方法叫作 8 函数描述符。例如，Runnable接口可以看作一个什么也不接受什么也不返回(void)的函数的 签名，因为它只有一个叫作run的抽象方法，这个方法什么也不接受，什么也不返回(void)。

#### 把Lambda付诸实践:环绕执行模式

资源处理(例如处理文件或数据库)时一个常见的模式就是打开一个资源，做一些处理， 然后关闭资源。这个设置和清理阶段总是很类似，并且会围绕着执行处理的那些重要代码。这就 是所谓的环绕执行(execute around)模式

```java
public static String processFile() throws IOException {
  try (BufferedReader br = 
       	new BufferedReader(new FileReader("data.txt"))) {
    return br.readline();
  }
}
```

1. 记得行为参数化

需要一种方法把行为传递给processFile，以便它可以利用 BufferedReader执行不同的行为。

下面就是从BufferedReader中打印两行的写法

```java
String result = processFile((BufferedReader br) -> 
                            br.readLine() + br.readLine());
```

2. 使用函数式接口来传递行为

需要创建一个能匹配 BufferedReader -> String，还可以抛出IOException异常的接口。让我们把这一接口叫作 BufferedReaderProcessor吧。

```java
@FunctionalInterface
public interface BufferedReaderProcessor {
  String process(BufferedReader b) throws IOException;
}

public static String processFile(BufferedReaderProcessor p) throws IOException {
  ...
}
```

3. 执行一个行为

任何BufferedReader -> String形式的Lambda都可以作为参数来传递，因为它们符合 BufferedReaderProcessor接口中定义的process方法的签名。现在你只需要一种方法在 processFile主体内执行Lambda所代表的代码。请记住，Lambda表达式允许你直接内联，为 函数式接口的抽象方法提供实现，并且将整个表达式作为函数式接口的一个实例。因此，你可 以在processFile主体内，对得到的BufferedReaderProcessor对象调用process方法执行 处理:

```java
public static String processFile(BufferedReaderProcessor p) throws IOException {
  try (BufferedReader br =
      	new BufferedReader(new FileReader("data.txt"))) {
    return p.process(br);
  }
}
```

4. 传递Lambda

就可以通过传递不同的Lambda重用processFile方法，并以不同的方式处理文件了。

```java
String oneLine = processFile((BufferedReader br) -> br.readLine());
String twoLines =
	processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

#### 使用函数式接口

函数式接口定义且只定义了一个抽象方法。函数式接口很有用， 因为抽象方法的签名可以描述Lambda表达式的签名。函数式接口的抽象方法的签名称为函数描 述符。所以为了应用不同的Lambda表达式，你需要一套能够描述常见函数描述符的函数式接口。

Java 8的库设计师帮你在java.util.function包中引入了几个新的函数式接口，比如Predicate、Consumer和Function

1. Predicate

java.util.function.Predicate<T>接口定义了一个名叫test的抽象方法，它接受泛型 T对象，并返回一个boolean。这恰恰和你先前创建的一样，现在就可以直接使用了。在你需要 表示一个涉及类型T的布尔表达式时，就可以使用这个接口。比如，你可以定义一个接受String 对象的Lambda表达式

```java
@FunctionalInterface
public interface Predicate<T> {
  boolean test(T t);
}

public static <T> List<T> filter(List<T> list, Predicate<T> p) {
  List<T> results = new ArrayList<>();
  for (T s: list) {
    if (p.test(s)) {
      results.add(s);
    }
  }
  return results;
}

Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
List<String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate);
```

2. Consumer

java.util.function.Consumer<T>定义了一个名叫accept的抽象方法，它接受泛型T 的对象，没有返回(void)。你如果需要访问类型T的对象，并对其执行某些操作，就可以使用 这个接口。比如，你可以用它来创建一个forEach方法，接受一个Integers的列表，并对其中 每个元素执行操作。

```java
@FunctionalInterface
public interface Consumer<T> {
  void accept(T t);
}

public static <T> void forEach(List<T> list, Consumer<T> c) {
  for(T i: list) {
    c.accept(i);
  }
}

forEach(
  Array.asList(1,2,3,4,5),
  (Integer i) -> System.out.println(i)
);
```

3. Function

java.util.function.Function<T, R>接口定义了一个叫作apply的方法，它接受一个 泛型T的对象，并返回一个泛型R的对象。如果你需要定义一个Lambda，将输入对象的信息映射 到输出，就可以使用这个接口(比如提取苹果的重量，或把字符串映射为它的长度)。

```java
@FunctionalInterface
public interface Function<T, R> {
  R apply(T t);
}

public static <T, R> List<R> map(List<T> list,
                                     Function<T, R> f) {
  List<R> result = new ArrayList<>();
  for(T s: list){
    result.add(f.apply(s));
  }
  return result;
}
// [7, 2, 6]
List<Integer> l = map(
	Array.asList("lambda", "in", "action"),
  (String s) -> s.length()
);
```

原始类型特化

Java还有一个自动装箱机制来帮助程序员执行这一任务:装 箱和拆箱操作是自动完成的。比如，这就是为什么下面的代码是有效的(一个int被装箱成为 Integer):

```java
List<Integer> list = new ArrayList<>();
for (int i = 300; i < 400; i++){
  list.add(i);
}
```

但这在性能方面是要付出代价的。装箱后的值本质上就是把原始类型包裹起来，并保存在堆 里。因此，装箱后的值需要更多的内存，并需要额外的内存搜索来获取被包裹的原始值。

Java 8为我们前面所说的函数式接口带来了一个专门的版本，以便在输入和输出都是原始类 型时避免自动装箱的操作。比如，在下面的代码中，使用IntPredicate就避免了对值1000进行 装箱操作，但要是用Predicate<Integer>就会把参数1000装箱到一个Integer对象中:

```java
public interface IntPredicate{
    boolean test(int t);
}
IntPredicate evenNumbers = (int i) -> i % 2 == 0;
evenNumbers.test(1000);

Predicate<Integer> oddNumbers = (Integer i) -> i % 2 == 1;
oddNumbers.test(1000); 
```

一般来说，针对专门的输入参数类型的函数式接口的名称都要加上对应的原始类型前缀，比 如DoublePredicate、IntConsumer、LongBinaryOperator、IntFunction等。Function接口还有针对输出参数类型的变种:`ToIntFunction<T>`、IntToDoubleFunction等。

ava API中提供的最常用的函数式接口及其函数描述符。请记得这只是一个起 点。如果有需要，你可以自己设计一个。请记住，(T,U) -> R的表达方式展示了应当如何思考 一个函数描述符。表的左侧代表了参数类型。这里它代表一个函数，具有两个参数，分别为泛型T和U，返回类型为R。

```java
Predicate<T>
  T -> boolean
  IntPredicate, LongPredicate, DoublePredicate

Consumer<T>
  T -> void
  IntConsumer,LongConsumer,DoubleConsumer
  
Function<T, R>
  T -> R
  IntFunction<R>,
  IntToDoubleFunction,
  IntToLongFunction,
  LongFunction<R>,
  LongToDoubleFunction,
  LongToIntFunction,
  DoubleFunction<R>,
  ToIntFunction<T>,
  ToDoubleFunction<T>,
  ToLongFunction<T>
    
Supplier<T>
  () -> T
  BooleanSupplier
  IntSupplier
  LongSupplier
  DoubleSupplier

UnaryOpearator<T>
  T -> T
  IntUnaryOperator,
  LongUnaryOperator,
  DoubleUnaryOperator
      
BinaryOperator<T>
  <T, T> -> T
  IntUnaryOperator,
  LongUnaryOperator,
  DoubleUnaryOperator
    
BiPredicate<L, R>
  (L, R) -> boolean

BiConsumer<T, U>
  (T, U) -> void
  ObjIntConsumer<T>,
  ObjLongConsumer<T>,
  ObjDoubleConsumer<T>
  
BiFunction<T,U,R>
  (T,U)->R
  ToIntBiFunction<T,U>,
	ToLongBiFunction<T,U>,
	ToDoubleBiFunction<T,U>
```

```java
// 布尔表达式
(List<String> list) -> list.isEmpty
Predicate<List<String>>

// 创建对象
() -> new Apple(10)
Supplier<Apple>
  
// 消费一个对象
(Apple a) -> System.out.println(a.getWeight())
Consumer<Apple>

// 从一个对象中选择/提取
(String s) -> s.length()
Function<String, Integer>或ToIntFunction<String>
  
// 合并两个值
(int a, int b) -> a * b
IntBinaryOperator
  
// 比较两个对象
(Apple a, Apple b) -> a1.getWeight().comapreTo(a2.getWeight())
Comparator<Apple>
  或BiFunction<Apple, Apple, Integer>
  或ToIntBiFunction<Apple, Apple>
```

#### 类型检查、类型推断以及限制

它可以为函数式接口生成一个实例。然而，Lambda 表达式本身并不包含它在实现哪个函数式接口的信息。为了全面了解Lambda表达式，你应该知 道Lambda的实际类型是什么。

1. 类型检查

Lambda的类型是从使用Lambda的上下文推断出来的。上下文(比如，接受它传递的方法的参数，或接受它的值的局部变量)中Lambda表达式需要的类型称为目标类型。

```java
List<Apple> heavierThan150g = filter(inventory, 
                                     (Apple a) -> a.getWeight() > 150);
```

2. 同样的Lambda，不同的函数式接口

有了目标类型的概念，同一个Lambda表达式就可以与不同的函数式接口联系起来，只要它们的抽象方法签名能够兼容。

```java
Callable<Integer> c = () -> 42;
PrivilegedAction<Integer> p = () -> 42;

Comparator<Apple> c1 = 
  (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
ToIntBiFunction<Apple, Apple> c2 =
	(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
BiFunction<Apple, Apple, Integer> c3 =
	(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

有了目标类型的概念，同一个Lambda表达式就可以与不同的函数式接口联系起来，只要它 们的抽象方法签名能够兼容。

3. 类型推断

```java
List<Apple> c = 
  (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
Comparator<Apple> c =
	(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
Comparator<Apple> c =
   (a1, a2) -> a1.getWeight().compareTo(a2.getWeight());
```

4. 使用局部变量

Lambda表达式 也允许使用自由变量(不是参数，而是在外层作用域中定义的变量)，就像匿名类一样。 它们被称作捕获Lambda。

```java
int portNumber = 1337;
Runnable r = () -> System.out.println(portNumber);
```

尽管如此，还有一点点小麻烦:关于能对这些变量做什么有一些限制。Lambda可以没有限制地捕获(也就是在其主体中引用)实例变量和静态变量。但局部变量必须显式声明为final， 或事实上是final。换句话说，Lambda表达式只能捕获指派给它们的局部变量一次。(注:捕获实例变量可以被看作捕获最终局部变量this。) 例如，下面的代码无法编译，因为portNumber 变量被赋值两次:

```java
int portNumber = 1337;
Runnable r = () -> System.out.println(portNumber);
portNumber = 31337;
// 错误:Lambda表达式引用的局 部变量必须是最终的(final) 或事实上最终的
```

为什么局部变量有这些限制。第一，实例变量和局部变量背后的实现有一 个关键不同。实例变量都存储在堆中，而局部变量则保存在栈上。如果Lambda可以直接访问局 部变量，而且Lambda是在一个线程中使用的，则使用Lambda的线程，可能会在分配该变量的线 程将这个变量收回之后，去访问该变量。因此，Java在访问自由局部变量时，实际上是在访问它 的副本，而不是访问原始变量。如果局部变量仅仅赋值一次那就没有什么区别了——因此就有了 这个限制。

第二，这一限制不鼓励你使用改变外部变量的典型命令式编程模式(我们会在以后的各章中 解释，这种模式会阻碍很容易做到的并行处理)。

#### 方法引用

方法引用让你可以重复使用现有的方法定义，并像Lambda一样传递它们。在一些情况下， 比起使用Lambda表达式，它们似乎更易读，感觉也更自然。

```java
inventory.sort((Apple a1, Apple a2) 
               	-> a1.getWeight().compareTo(a2.getWeight()));

inventory.sort(comparing(Apple::getWeight));
```

1. 管中窥豹

方法引用就是让你根据已有的方法实现来创建 Lambda表达式。但是，显式地指明方法的名称，你的代码的可读性会更好。它是如何工作的呢? 当你需要使用方法引用时，目标引用放在分隔符::前，方法的名称放在后面。例如，Apple::getWeight就是引用了Apple类中定义的方法getWeight。请记住，不需要括号，因为你没有实际调用这个方法。方法引用就是Lambda表达式(Apple a) -> a.getWeight()的快捷写法

```java
(Apple a) -> a.getWeight() 								Apple::getWeight
() -> Thread.currentThread().dumpStack()  Thread.currentThread()::dumpStack
(str, i) -> str.substring(i)							String::substring
(String s) -> System.out.println(s)				System.out::println
```

你可以把方法引用看作针对仅仅涉及单一方法的Lambda的语法糖，因为你表达同样的事情 时要写的代码更少了

如何构建方法引用，方法引用主要有三类。

- 指向静态方法的方法引用(例如Integer的parseInt方法，写作Integer::parseInt)
- 指向任意类型实例方法的方法引用(例如String的length方法，写作 String::length)
- 指向现有对象的实例方法的方法引用(假设你有一个局部变量expensiveTransaction 用于存放Transaction类型的对象，它支持实例方法getValue，那么你就可以写expensive- Transaction::getValue)

第三种方法引用 指的是，你在Lambda中调用一个已经存在的外部对象中的方法。例如，Lambda表达式 ()->expensiveTransaction.getValue()可以写作expensiveTransaction::getValue。

2. 构造函数引用

对于一个现有构造函数，你可以利用它的名称和关键字new来创建它的一个引用: ClassName::new。它的功能与指向静态方法的引用类似。例如，假设有一个构造函数没有参数。 它适合Supplier的签名() -> Apple

```java
Supplier<Apple> c1 = Apple::new;
Apple a1 = c1.get();
===>
Supplier<Apple> c1 = () -> new Apple();
Apple a1 = c1.get();
```

如果你的构造函数的签名是Apple(Integer weight)，那么它就适合Function接口的签 名，于是你可以这样写:

```java
Function<Integer, Apple> c2 = Apple::new;
Apple a2 = c2.apply(110);
===>
Function<Integer, Apple> c2 = (weight) -> new Apple(weight);
Apple a2 = c2.apply(110);
```

一个由Integer构成的List中的每个元素都通过我们前面定义的类似的 map方法传递给了Apple的构造函数，得到了一个具有不同重量苹果的List:

```java
List<Integer> weights = Arrays.asList(7, 3, 4, 10);
List<Apple> apples = map(weights, Apple::new);

public static List<Apple> map(List<Integer> list,
                             Function<Integer, Apple> f) {
  List<Apple> result = new ArrayList<>();
  for (Integer e: list) {
    result.add(f.apply(e));
  }
  return result;
}
```

一个由Integer构成的List中的每个元素都通过我们前面定义的类似的 map方法传递给了Apple的构造函数，得到了一个具有不同重量苹果的List:

```java
BiFunction<String, Integer, Apple> c3 = Apple::new;
Apple c3 = c3.apply("green", 110);
===>
BiFunction<String, Integer, Apple> c3 =
    (color, weight) -> new Apple(color, weight);
Apple c3 = c3.apply("green", 110);
```

不将构造函数实例化却能够引用它，这个功能有一些有趣的应用。例如，你可以使用Map来 将构造函数映射到字符串值。你可以创建一个giveMeFruit方法，给它一个String和一个 Integer，它就可以创建出不同重量的各种水果:

```java
static Map<String, Function<Integer, Fruit>> map = new HashMap<>();
static {
  map.put("apple", Apple::new);
  map.put("orange", Orange::new);
  // etc...
}
public static Fruit giveMeFruit(String fruit, Integer weight) {
  return map.get(fruit.toLowerCase())
    .apply(weight);
}
```

#### Lambda 和方法引用实战

1. 传递代码

```java
void sort(Comparator<? super E> c)
  
public class AppleComparator implements Comparator<Apple> {
  public int compare(Apple a1, Apple a2){
    return a1.getWeight().compareTo(a2.getWeight());
  }
}
inventory.sort(new AppleComparator());
```

2. 使用匿名类

```java
inventory.sort(new Comparator<Apple>() {
  public int compare(Apple a1, Apple a2){
    return a1.getWeight().compareTo(a2.getWeight());
  }
});
```

3. 使用Lambda表达式

```java
inventory.sort((Apple a1, Apple a2)
                -> a1.getWeight().compareTo(a2.getWeight()));

inventory.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight()));

Comparator<Apple> c = Comparator.comparing((Apple a) -> a.getWeight());
import static java.util.Comparator.comparing;
inventory.sort(comparing((a) -> a.getWeight()));
```

4. 使用方法引用

```java
inventory.sort(comparing(Apple::getWeight));
```

#### 复合Lambda表达式的有用方法

许多函数式接口，比如用于传递Lambda表达式的Comparator、Function和Predicate都提供了允许你进行复合的方法。这是什么意思呢?在实践中，这意味着你可以把多个简单的Lambda复合成复杂的表达式。比如，你可以让两个谓词之间做一个or操作，组合成一个更大的谓词。而且，你还可以让一个函数的结 果成为另一个函数的输入。你可能会想，函数式接口中怎么可能有更多的方法呢?(毕竟，这违背了函数式接口的定义啊!)窍门在于，我们即将介绍的方法都是默认方法，也就是说它们不是抽象方法。

1. 比较器复合

```java
Comparator<Apple> c = Comparator.comparing(Apple::getWeight);
```

逆序

```java
inventory.sort(comparing(Apple::getWeight).reversed());
```

比较器链

```java
inventory.sort(comparing(Apple::getWeight)
              .reversed()
              .thenComparing(Apple::getCountry));
```

2. 谓词复合

谓词接口包括三个方法:negate、and和or，让你可以重用已有的Predicate来创建更复杂的谓词

```java
Predicate<Apple> notRedApple = redApple.negate();

Predicate<Apple> redAndHeavyApple =
    redApple.and(a -> a.getWeight() > 150);

Predicate<Apple> redAndHeavyAppleOrGreen =
  redApple.and(a -> a.getWeight() > 150)
  	.or(a -> "green".equals(a.getColor()));
```

and和or方法是按照在表达式链中的位置，从左向右确定优 先级的。因此，a.or(b).and(c)可以看作(a || b) && c

3. 函数复合

还可以把Function接口所代表的Lambda表达式复合起来。Function接口为此配 了andThen和compose两个默认方法，它们都会返回Function的一个实例。

andThen方法会返回一个函数，它先对输入应用一个给定函数，再对输出应用另一个函数。 比如，假设有一个函数f给数字加1 (x -> x + 1)，另一个函数g给数字乘2，你可以将它们组 合成一个函数h，先给数字加1，再给结果乘2:

```java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.andThen(g);
int result = h.apply(1); // 4
```

也可以类似地使用compose方法，先把给定的函数用作compose的参数里面给的那个函 数，然后再把函数本身用于结果。比如在上一个例子里用compose的话，它将意味着f(g(x))， 而andThen则意味着g(f(x)):

```java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.compose(g);
int result = h.apply(1); // 3
```

那么在实际中这有什么用呢?比方说你有一系列工具方法，对用String表示的一封信做文本转换:

```java
public class Letter {
  public static String addHeader(String text) {
    return "From Raoul, Mario and Alan: " + text;
  }
  public static String addFooter(String text) {
    return text + " Kind regards";
  }
  public static String checkSpelling(String text) {
    return text.replaceText("labda", "lambda");
  }
}
```

现在你可以通过复合这些工具方法来创建各种转型流水线了，比如创建一个流水线:先加上 抬头，然后进行拼写检查，最后加上一个落款

```java
Function<String, String> addHeader = Letter::addHeader;
Function<String, String> transformationPipeline = addHeader
  .andThen(Letter::checkSpelling)
  .andThen(Letter::addFoooter);
```

q第二个流水线可能只加抬头、落款，而不做拼写检查:

```java
Function<String, String> addHeader = Letter::addHeader;
Function<String, String> transformationPipeline
  = addHeader.andThen(Letter::addFooter);
```