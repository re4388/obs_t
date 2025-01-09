

单例是一个仅实例化一次的类[Gamma95]。单例对象通常表示无状态对象，如函数 (条目 24) 或一个本质上唯一的系统组件。

让一个类成为单例会使测试它的客户变得困难，因为除非实现一个作为它类型的接口，否则不可能用一个模拟实现替代单例。


有两种常见的方法来实现单例。两者都基于保持构造方法私有和导出公共静态成员以提供对唯一实例的访问。在 第一种方法中，成员是 final 修饰的属性:

```java

// Singleton with public final field
public class Elvis {
    public static final Elvis INSTANCE = new Elvis(); // <== this
    private Elvis() { ... }
    public void leaveTheBuilding() { ... }
}

```
私有构造方法只调用一次，来初始化公共静态 final Elvis.INSTANCE 属性。缺少一个公共的或受保护的构造 方法，保证了全局的唯一性:一旦 Elvis 类被初始化，一个 Elvis 的实例就会存在——不多也不少。客户端所做的任何 事情都不能改变这一点，但需要注意的是:特权客户端可以使用 AccessibleObject.setAccessible 方法，以反 射方式调用私有构造方法 (条目 65)。如果需要防御此攻击，请修改构造函数，使其在请求创建第二个实例时抛出异 常。




  在第二个实现单例的方法中，公共成员是一个静态的工厂方法:
```java

// Singleton with static factory
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public static Elvis getInstance() { return INSTANCE; }
    public void leaveTheBuilding() { ... }
}

```
所有对 Elvis.getInstance 的调用都返回相同的对象引用，并且不会创建其他的 Elvis 实例(与前面提到的 警告相同)。

公共属性方法的主要优点是 API 明确表示该类是一个单例:公共静态属性是 final 的，所以它总是包含相同的对 象引用。 第二个好处是它更简单。


静态工厂方法的一个优点是，它可以灵活地改变你的想法，无论该类是否为单例而不必更改其 API。 工厂方法返回唯一的实例，但是可以修改，比如，返回调用它的每个线程的单独实例。 第二个好处是，如果你的应用程序需要它，可以编写一个泛型单例工厂(generic singleton factory )(条目 30)。 使用静态工厂的最后一个优点是方法引用 可以用 supplier ，例如 Elvis::instance 等同于 `Supplier<Elvis>` 。 除非与这些优点相关的，否则公共属 性方法是可取的。


创建一个使用上面这两种方法的单例类 (第 12 章)，仅仅将  implements Serializable   添加到声明中是不够的。 为了维护单例的保证，声明所有的实例属性为 transient ，并提供一个  readResole   方法 (条目 89)。否则，每 当序列化实例被反序列化时，就会创建一个新的实例，在我们的例子中，导致出现新的 Elvis 实例。为了防止这种情 况发生，将这个 readResolve 方法添加到 Elvis 类:

```java
// readResolve method to preserve singleton property
private Object readResolve() {

     // Return the one true Elvis and let the garbage collector

     // take care of the Elvis impersonator.
    return INSTANCE;

}
```


--

实现一个单例的第三种方法是声明单一元素的枚举类:
```java


// Enum singleton - the preferred approach
public enum Elvis {
	INSTANCE;
    public void leaveTheBuilding() { ... }
}

```

这种方式类似于公共属性方法，但更简洁，提供了免费的序列化机制，并提供了针对多个实例化的坚固保证，即 使是在复杂的序列化或反射攻击的情况下。这种方法可能感觉有点不自然，但是单一元素枚举类通常是实现单例的最 佳方式。注意，如果单例必须继承 Enum 以外的父类 (尽管可以声明一个 Enum 来实现接口)，那么就不能使用这种 方法。