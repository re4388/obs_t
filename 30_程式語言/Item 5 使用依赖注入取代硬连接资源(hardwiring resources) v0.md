

许多类依赖于一个或多个底层资源。例如，拼写检查器依赖于字典。

将此类类实现为静态实用工具类并不少见 (条目 4):
```java


// Inappropriate use of static utility - inflexible & untestable!
public class SpellChecker {
    
    private static final Lexicon dictionary = ...; // 字典
    
    private SpellChecker() {} // Noninstantiable
    
    public static boolean isValid(String word) { ... }
    public static List<String> suggestions(String typo) { ... }
}

```


同样地，将它们实现为单例也并不少见 (条目 3):
```java

// Inappropriate use of singleton - inflexible & untestable!
public class SpellChecker {
    private final Lexicon dictionary = ...; // 字典
    private SpellChecker(...) {} // Noninstantiable
    
    public static INSTANCE = new SpellChecker(...);
    
    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}

```

**这两种方法都不令人满意，因为他们假设只有一本字典值得使用**。在实际中，每种语言都有自己的字典，特殊的字典被用于特殊的词汇表。另外，使用专门的字典来进行测试也是可取的。想当然地认为一本字典就足够了，这是一厢情愿的想法。

**可以通过使 dictionary 属性设置为非 final ，并添加一个方法来更改现有拼写检查器中的字典，从而让拼写检查器支持多个字典，但是在并发环境中，这是笨拙的、容易出错的和不可行的**。静态实用类和单例对于那些行为被底层资源参数化的类来说是不合适的。

**所需要的是能够支持类的多个实例 (在我们的示例中，即 SpellChecker )，每个实例都使用客户端所期望的资源 (在我们的例子中是 dictionary )。满足这一需求的简单模式是在创建新实例时将资源传递到构造方法中。这是依赖项注入(dependency injection)的一种形式:字典是拼写检查器的一个依赖项，当它创建时被注入到拼写检查器 中。**
```java

// Dependency injection provides flexibility and testability
public class SpellChecker {
    private final Lexicon dictionary;
    
    public SpellChecker(Lexicon dictionary) { // 依赖项注入(dependency injection)
        this.dictionary = Objects.requireNonNull(dictionary);
	}
	
    public boolean isValid(String word) { ... }
    public List<String> suggestions(String typo) { ... }
}

```

依赖注入模式非常简单，许多程序员使用它多年而不知道它有一个名字。 

虽然我们的拼写检查器的例子只有一个资源(字典)，但是依赖项注入可以使用任意数量的资源和任意依赖图。它保持了不变性(条目 17)，因此多个客户端可以共享依赖对象(假设客户需要相同的底层资源)。 依赖注入同样适用于构造方法，静态工厂(条目 1)和 builder 模式(条目 2)。


依赖注入模式的一个有用的变体是将资源工厂传递给构造方法。 工厂是可以重复调用以创建类型实例的对象。 这种工 厂体现了工厂方法模式(Factory Method pattern )[Gamma95]。 Java 8 中引入的 `Supplier<T>` 接口非常适合代表 工厂。 在输入上采用` Supplier<T>` 的方法通常应该使用有界的通配符类型 ( bounded wildcard type)(条目 31)约 束工厂的类型参数，以允许客户端传入工厂，创建指定类型的任何子类型。

例如，下面是一个使用客户端提供的工 厂生成 tile 的方法 `Mosaic create(Supplier<? extends Tile> tileFactory) { ... }`


尽管依赖注入极大地提高了灵活性和可测试性，但它可能使大型项目变得混乱，这些项目通常包含数千个依赖项。
使用依赖注入框架 (如 Dagger[Dagger]、Guice[Guice] 或 Spring[Spring]) 可以消除这些混乱。这些框架的使用超出了本书的范围，但是请注意，为手动依赖注入而设计的 API 非常适合这些框架的使用。

总之
- 不要使用单例或静态的实用类来实现一个类，该类依赖于一个或多个底层资源，这些资源的行为会影响类的行为，并且不让类直接创建这些资源。相反，将资源或工厂传递给构造方法 (或静态工厂或 builder 模式)。这种称为依赖注入的实践将极大地增强类的灵活性、可重用性和可测试性。