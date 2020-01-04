# 通过接口来引用对象

条款51建议应该使用接口而不是类作为参数类型。更一般地说，你应该优先使用接口而不是类来引用对象。**如果存在合适的接口类型，那么应该使用接口类型声明参数、返回值、变量和字段**。只有在使用构造函数创建对象时，才真正需要引用对象的类。具体来说，考虑`LinkedHashSet`的情况，它是`Set`接口的一个实现。好的声明类型的做法是：

```java
// Good - uses interface as type
Set<Son> sonSet = new LinkedHashSet<>();
```

而不是：

```java
// Bad - uses class as type!
LinkedHashSet<Son> sonSet = new LinkedHashSet<>();
```

**如果你养成了使用接口作为类型的习惯，那么你的程序将会更加灵活**。如果你决定要换一个实现，你所要做的就是更改构造函数中的类名（或使用不同的静态工厂）。例如，第一个声明可以改为：

```java
Set<Son> sonSet = new HashSet<>();
```

并且相关的所有代码都可以继续工作。相关代码并不关心旧的实现类型，因此，它们不知道类型改变了。这里有一个警告：如果起初的实现提供了一些接口规范之外的特殊功能，而且我们的代码也依赖于这些功能，那么新的实现提供相同的功能就显得极其重要。例如，如果围绕第一个声明的代码依赖于`LinkedHashSet`的排序策略，那么将声明中的`LinkedHashSet`替换为`HashSet`是不正确的，因为`HashSet`不保证迭代顺序。

那么，你为什么想要改变实现类型呢？因为第二个实现提供了比原来的实现更好的性能，或者是因为它提供了更恰当的原来实现没有的功能。例如，假设一个字段包含了一个`HashMap`实例。将`HashMap`该成`EnumMap`会提供更好的性能和与键的自然顺序一致的迭代顺序。但是你只能在键类型为枚举的情况下使用`EnumMap`。将`HashMap`更改为`LinkedHashMap`将提供可预测的迭代顺序，性能与`HashMap`相当，而且对键类型没有任何特殊要求。

你可能认为用实现类型来声明一个变量是没问题的。因为你可以同时改变声明类型和实现类型。但是你无法保证改完后的程序可以通过编译。如果客户端代码使用的是基于旧实现类型的方法，因为在替换实现类型时，方法还不存在。或者客户端代码把实例传给了需要旧实现类型的方法，那么改完后的代码就无法通过编译。使用接口类型声明变量可以使你保持诚实。

**如果没有合适的接口，那么通过类而不是接口来引用对象也是完全合适的**。比如，考虑值类，如`String`和`BigInteger`。在编写值类时，很少考虑到多个实现。它们通常是`final`的，很少有相应的接口。将这样的值类用作参数、变量、字段或返回类型是非常好的。

没有合适接口类型的第二种情况是属于框架的对象，其基本类型是类而不是接口。如果一个对象属于这样一个基于类的框架，那么最好通过相关的基类（通常是抽象的）来引用它，而不是通过它的实现类。` java.io`包下的类很多就属于这一类，比如`OutputStream`。

没有合适接口类型的最后一种情况是类实现了一个接口，但是它也提供了接口中没有的额外方法。比如说，`  PriorityQueue `有一个`comparator`方法，而这个方法并在`Queue`接口中。只有当程序依赖于额外的方法时，才应该使用这样的类来引用它的实例，但这种情况应该非常少见。

这三种情况并不是要面面俱到，而仅仅是为了表达通过类引用对象是适当的情况。在实践中，给定对象是否具有合适的接口是很明显的。如果有，那么使用接口引用对象，则你的程序将更灵活、更时尚。如果没有合适的接口，只需使用类层次结构中提供所需功能的最小的特定的类。