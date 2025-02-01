

静态工厂和构造方法都有一个限制:它们不能很好地扩展到很多可选参数的情景

一個作法是 可伸缩(telescoping constructor)构 造方法模式
```java


// Telescoping constructor pattern - does not scale well!
public class NutritionFacts {
   private final int servingSize; // (mL) required
   private final int servings;
   private final int calories;
   private final int fat;
   private final int sodium;
   private final int carbohydrate; // (g/serving)

   public NutritionFacts(int servingSize, int servings) {
      this(servingSize, servings, 0);
   }

   public NutritionFacts(int servingSize, int servings,
         int calories) {
      this(servingSize, servings, calories, 0);
   }

   public NutritionFacts(int servingSize, int servings,
         int calories, int fat) {
      this(servingSize, servings, calories, fat, 0);
   }

   public NutritionFacts(int servingSize, int servings,
         int calories, int fat, int sodium) {
      this(servingSize, servings, calories, fat, sodium, 0);
   }

   public NutritionFacts(int servingSize, int servings,
          int calories, int fat, int sodium, int carbohydrate) {
          this.servingSize  = servingSize;
          this.servings = servings;
          this.calories = calories;
         this.fat = fat;
         this.sodium = sodium;
         this.carbohydrate = carbohydrate;
    }
}

NutritionFacts cocaCola = new NutritionFacts(240, 8, 100, 0, 35, 27);


```

简而言之，可伸缩构造方法模式是有效的，但是当有很多参数时，很难编写客户端代码，而且很难读懂它。

读者 不知道这些值是什么意思，并且必须仔细地计算参数才能找到答案。

一长串相同类型的参数可能会导致一些细微的 bug。

如果客户端意外地反转了两个这样的参数，编译器并不会抱怨，但是程序在运行时会出现错误行为 (条目 51)




当在构造方法中遇到许多可选参数时，另一种选择是 JavaBeans 模式，在这种模式中，调用一个无参数的构造函数来 创建对象，然后调用 setter 方法来设置每个必需的参数和可选参数:

```java

// JavaBeans Pattern - allows inconsistency, mandates mutability
public class NutritionFacts {
   // Parameters initialized to default values (if any)
   private int servingSize = -1; // Required; no default value
   private int servings = -1;
   private int calories = 0;
   private int fat = 0;
   private int sodium = 0;
   private int carbohydrate = 0;

   public NutritionFacts() {
   }

   // Setters
   public void setServingSize(int val) {
      servingSize = val;
   }

   public void setServings(int val) {
      servings = val;
   }

   public void setCalories(int val) {
      calories = val;
   }

   public void setFat(int val) {
      fat = val;
   }

   public void setSodium(int val) {
      sodium = val;
   }

   public void setCarbohydrate(int val) {
      carbohydrate = val;
   }
}



NutritionFacts cocaCola = new NutritionFacts();
cocaCola.setServingSize(240);
cocaCola.setServings(8);
cocaCola.setCalories(100);
cocaCola.setSodium(35);
cocaCola.setCarbohydrate(27);

```

不幸的是，JavaBeans 模式本身有严重的缺陷。**由于构造方法在多次调用中被分割，所以在构造过程中 JavaBean 可能处于不一致的状态。** 该类没有通过检查构造参数参数的有效性来执行一致性的选项。在不一致的状态下尝试使用 对象可能会导致与包含 bug 的代码大相径庭的错误，因此很难调试。一个相关的缺点是，JavaBeans 模式排除了让类 不可变的可能性 (条目 17)，并且需要在程序员的部分增加工作以确保线程安全。


当它的构造完成时，手动“冻结”对象，并且不允许它在解冻之前使用，可以减少这些缺点，但是这种变体在实践 中很难使用并且很少使用。 而且，在运行时会导致错误，因为编译器无法确保程序员在使用对象之前调用 freeze 方法。



幸运的是，还有第三种选择，它结合了可伸缩构造方法模式的安全性和 JavaBean 模式的可读性。 它是 Builder 模式[Gamma95] 的一种形式。客户端不直接调用所需的对象，而是调用构造方法 (或静态工厂)，并使用所有必需的参 数，并获得一个 builder 对象。然后，客户端调用 builder 对象的 setter 相似方法来设置每个可选参数。最后，客 户端调用一个无参的 build 方法来生成对象，该对象通常是不可变的。Builder 通常是它所构建的类的一个静态成 员类 (条目 24)。以下是它在实践中的示例:

```java

// Builder Pattern
public class NutritionFacts {
   private final int servingSize;
   private final int servings;
   private final int calories;
   private final int fat;
   private final int sodium;
   private final int carbohydrate;

   public static class Builder {
      // Required parameters
      private final int servingSize;
      private final int servings;
      
      // Optional parameters - initialized to default values
      private int calories = 0;
      private int fat = 0;
      private int sodium = 0;
      private int carbohydrate = 0;




      public Builder(int servingSize, int servings) {
         this.servingSize = servingSize;
         this.servings = servings;
      }

      public Builder calories(int val) {
         calories = val;
         return this;
      }

      public Builder fat(int val) {
         fat = val;
         return this;
      }

      public Builder sodium(int val) {
         sodium = val;
         return this;
      }

      public Builder carbohydrate(int val) {
         carbohydrate = val;
         return this;
      }

      public NutritionFacts build() {
         return new NutritionFacts(this);
      }
   }

   private NutritionFacts(Builder builder) {
      servingSize = builder.servingSize;
      servings = builder.servings;
      calories = builder.calories;
      fat = builder.fat;
      sodium = builder.sodium;
      carbohydrate = builder.carbohydrate;
   }
}



// NutritionFacts 类是不可变的，所有的参数默认值都在一个地方。builder 的 setter 方法返回 builder 本身， 这样调用就可以被链接起来，从而生成一个流畅的 API。下面是客户端代码的示例:
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)
	.calories(100).sodium(35).carbohydrate(27).build();
```


为了简洁起见，省略了有效性检查。 要尽快检测无效参数，检查 builder 的构造方法和方法中的参数有效性。 在 build 方法调用的构造方法中检查包含多个参数的不变性。为了确保这些不变性不受攻击，在从 builder 复制参数 后对对象属性进行检查(条目 50)。 如果检查失败，则抛出 IllegalArgumentException 异常(条目 72)，其详细消息指示哪些参数无效(条目 75)。




Builder 模式非常适合类层次结构。 使用平行层次的 builder，每个嵌套在相应的类中。 抽象类有抽象的 builder; 具体的类有具体的 builder。 例如，考虑代表各种比萨饼的根层次结构的抽象类:
```java

// Builder pattern for class hierarchies
import java.util.EnumSet;
import java.util.Objects;
import java.util.Set;


public abstract class Pizza {
    public enum Topping {HAM, MUSHROOM, ONION, PEPPER, SAUSAGE}
    final Set<Topping> toppings;
    
    
    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addTopping(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }
        abstract Pizza build();
        // Subclasses must override this method to return "this"
        protected abstract T self();
    }
    
    Pizza(Builder<?> builder) {
        toppings = builder.toppings.clone(); // See Item 50
    } 
}

```


请注意， Pizza.Builder 是一个带有递归类型参数( recursive type parameter)(条目 30)的泛型类型。 这 与抽象的 self 方法一起，允许方法链在子类中正常工作，而不需要强制转换。 Java 缺乏自我类型的这种变通解决 方法被称为模拟自我类型(simulated self-type)的习惯用法。
这里有两个具体的 Pizza 的子类，其中一个代表标准的纽约风格的披萨，另一个是半圆形烤乳酪馅饼。前者 有一个所需的尺寸参数，而后者则允许指定酱汁是否应该在里面或在外面:


```java

import java.util.Objects;

public class NyPizza extends Pizza {
   public enum Size {
      SMALL, MEDIUM, LARGE
   }

   private final Size size;

   public static class Builder extends Pizza.Builder<Builder> {
      private final Size size;

      public Builder(Size size) {
         this.size = Objects.requireNonNull(size);
      }

      @Override
      public NyPizza build() {
         return new NyPizza(this);
      }

      @Override
      protected Builder self() {
         return this;
      }
   }

   private NyPizza(Builder builder) {
      super(builder);
      size = builder.size;
   }
}

public class Calzone extends Pizza {
   private final boolean sauceInside;

   public static class Builder extends Pizza.Builder<Builder> {
      private boolean sauceInside = false; // Default

      public Builder sauceInside() {
         sauceInside = true;
         return this;
      }

      @Override
      public Calzone build() {
         return new Calzone(this);
      }

      @Override
      protected Builder self() {
         return this;
      }
   }

   private Calzone(Builder builder) {
      super(builder);
      sauceInside = builder.sauceInside;
   }
}

```

请注意，每个子类 builder 中的 方法被声明为返回正确的子类: NyPizza.Builder 的 build 方法 返回 NyPizza ，而 中的 build 方法返回   。 

这种技术，其一个子类的方法被声 明为返回在超类中声明的返回类型的子类型，称为协变返回类型 ( covariant return typing)。 它允许客户端使用这些 builder，而不需要强制转换。


这些“分层 builder”的客户端代码基本上与简单的 NutritionFacts builder 的代码相同。为了简洁起见,下面显 示的示例客户端代码假设枚举常量的静态导入:

```java

NyPizza pizza = new NyPizza.Builder(SMALL)
        .addTopping(SAUSAGE).addTopping(ONION).build();
        
Calzone calzone = new Calzone.Builder()
        .addTopping(HAM).sauceInside().build();

```



builder 对构造方法的一个微小的优势是，builder 可以有多个可变参数，因为每个参数都是在它自己的方法中指 定的。或者，builder 可以将传递给多个调用的参数聚合到单个属性中，如前面的 addTopping 方法所演示的那 样。


Builder 模式非常灵活。 单个 builder 可以重复使用来构建多个对象。 builder 的参数可以在构建方法的调用之间 进行调整，以改变创建的对象。 builder 可以在创建对象时自动填充一些属性，例如每次创建对象时增加的序列号。

Builder 模式也有缺点。为了创建对象，首先必须创建它的 builder。虽然创建这个 builder 的成本在实践中不太可 能被注意到，但在性能关键的情况下可能会出现问题。而且，builder 模式比伸缩构造方法模式更冗长，因此只有在 有足够的参数时才值得使用它，比如四个或更多。但是请记住，如果希望在将来添加更多的参数。但是，如果从构造 方法或静态工厂开始，并切换到 builder，当类演化到参数数量失控的时候，过时的构造方法或静态工厂就会面临尴 尬的处境。因此，所以，最好从一开始就创建一个 builder。


总而言之，当设计类的构造方法或静态工厂的参数超过几个时，Builder 模式是一个不错的选择，特别是如果许多参数是可选的或相同类型的。客户端代码比使用伸缩构造方法(telescoping constructors)更容易读写，并且 builder 比 JavaBeans 更安全。