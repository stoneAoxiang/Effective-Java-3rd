# 优先选择方法引用而非lambdas

lambdas相对于匿名类的主要优势是它们更简洁。Java提供了一个比lambdas更简洁的方式去生成函数对象：**方法引用**。这有一个程序的代码片段，该程序维护了一个任意的键到`Integer`值的映射关系。如果这个`Integer`值代表着这个`key`的实例个数，那么这个程序就是一个多重集合的实现。这段代码的功能是，如果这个`key`

不在`map`中，那么将其与数字1关联，如果`key`已经存在，那么增加关联值：

```java
map.merge(key, 1, (count, incr) -> count + incr);
```

注意，这段代码使用的是`merge`方法，它被添加到Java 8中的`Map`接口中。如果所给的`key`没有对应的映射，那么该方法就直接插入所给的值；如果映射已经存在，`merge`方法会将所给的函数应用到当前的值和所给的值上，并且得到的结果会覆盖当前的值。这段代码代表了`merge`方法的一个典型用例。

代码读很好读，但是仍然有一些样板文件。参数`count`和`incr`没有增加多少值，而且它们占用了相当大的空间。实际上，这个lambda告诉你的信息是该函数会返回它两个参数的和。从Java 8开始，`Integer`（以及所有其他装箱的数值原生类型）提供了一个静态方法`sum`，其功能完全相同。其实，我们可以简单地传递该方法的一个引用，这样既能得到相同的结果，又减少了代码复杂度。

```java
map.merge(key, 1, Integer::sum);
```

方法的参数越多，你可以通过方法引用消除的样板代码就越多。然而，在某些lambda中，你选择的参数名称提供了有用的文档，使得lambda比方法引用更具可读性和可维护性，即使lambda较长。

使用方法引用或者lambdas，你就不需做其他事情（有一个不太明显的例外—如果你感兴趣，请参阅JLS，9.9-2）。也就是说，方法引用通常会产生更短、更清晰的代码。如果lambdas太长或太复杂，他也会给你个提示：你可以将代码从lambda提取到一个新方法中，并用对该方法的引用替换lambda。你可以给方法取个好名字，并且记录你的核心内容。

如果你正在使用IDE编程，它会尽可能提示用方法引用代替lambda。通常来讲，你应该接受IDE的建议，但不总是如此。有时候，lambda会比方法引用更简洁。这种情况通常发生在方法与lambda位于同一个类中时。比如，考虑这个代码片段，它假定发生在一个名为`GoshThisClassNameIsHumongous`的类中：

```java
service.execute(GoshThisClassNameIsHumongous::action);
```

等价的lambda是这样的：

```java
service.execute(() -> action());
```


