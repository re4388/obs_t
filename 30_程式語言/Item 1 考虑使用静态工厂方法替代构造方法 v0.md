- 有名字
	- new Cat vs  Cat.createHapppyCat(), Cat.createSadCat()
	- BigInteger(int bitLength, int certainty, Random rnd) -> 內建用來產生隨機prime 的 ctor, 但是如果可以這樣設計，會更清楚：BigInteger.probablePrime
	- ctor 沒有 name, then can take different arg to have different result -> 基本上你一定要去看 doc 才可以知道如何用，破壞封裝了
- 可以控制 instances
	- ctor, 你 new -> 一定建立一個 obj
	- 靜態方法，你可以控制
	- 類似應用：
		- The Boolean.valueOf(boolean) method illustrates this technique: it never creates an object. 
		- Flyweight pattern
- 可以返回 an object of any subtype of their return type
	- 例子 
		- interface-based frameworks (Item 20), 常用到, where interfaces provide natural return types for static factory methods
		- Java Collections Framework
			- the Java Collections Framework has forty-five utility implementations of its interfaces, providing unmodifiable collections, synchronized collections, and the like. Nearly all of these implementations are exported via static factory methods in one noninstantiable class (java.util.Collections). The classes of the returned objects are all nonpublic.
			- The programmer knows that the returned object has precisely the API specified by its interface, so there is no need to read additional class documentation for the implementation class. Furthermore, using such a static factory method requires the client to refer to the returned object by interface rather than implementation class, which is generally good practice
- The returned object can 改變 from call to call as a function of the input parameters.
	- The class of the returned object can also vary from release to release.
	- 例子
		- EnumSet class (Item 36) has no public constructors, only static factories.
		- OPenJDK 實作，如果大於 64, 返回 a JumboEnumSet instance, backed by a long array, 不然的話，返回  a RegularEnumSet instance, which is backed by a single long
		- 未來也可以持續進行優化。
		- Clients neither know nor care about the class of the object they get back from the factory
		- they care only that it is some subclass of EnumSet.
- the returned object 可以先不存在 when the class containing the method is written.
	- **service provider framework pattern**
		- like the Java Database Connectivity API (JDBC). 
		- A service provider framework is a system in which providers implement a service, and the system makes the implementations available to clients, decoupling the clients from the implementations.
		- service provider frameworks 的組成包括: 
			- a service interface, 實作本身
			- a provider registration API, 讓使用者去註冊要用哪一個impl
			- a service access API, 讓使用者來使用去 new to 實例 
		- 也可以有一個可選擇的組成是 a service provider interface, 就是一個工廠物件拿來產生這個服務介面的實例
		- PS: In the absence of a service provider interface, implementations must be instantiated reflectively (Item 65). 
		- In the case of JDBC
			- Connection -> service interface
			- DriverManager.registerDriver  -> provider registration API
			- DriverManager.getConnection  -> the service access API
			- Driver  -> the service provider interface.
		- 有很多 variants:
			- For example, the service access API can return a richer service interface to clients than the one furnished by providers. This is the Bridge pattern [Gamma95].
			- Dependency injection frameworks (Item 5) can be viewed as powerful service providers. Since Java 6, the platform includes a general-purpose service provider framework, java.util.ServiceLoader, so you needn’t, and generally shouldn’t, write your own (Item 59).

壞處1
The classes without public or protected constructors cannot be subclassed. 
像是 Collections Framework 都無法被 subclass (無法被 extends)
不過這或許是好是，因為 it encourages programmers to use composition instead of inheritance (Item 18), and is required for immutable types (Item 17).


壞處2
未必好找到要呼叫那個，因為大家naming convension 不同

下面提供一些目前常見的 naming convension

This list is far from exhaustive:
```
- from—A type-conversion method that takes a single parameter and returns a corresponding instance of this type, for example:
         Date d = Date.from(instant);
    
- of—An aggregation method that takes multiple parameters and returns an in- stance of this type that incorporates them, for example:
         Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
    
- valueOf—A more verbose alternative to from and of, for example:
         BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
    
- instance or getInstance—Returns an instance that is described by its pa- rameters (if any) but cannot be said to have the same value, for example:
         StackWalker luke = StackWalker.getInstance(options);
    
- create or newInstance—Like instance or getInstance, except that the method guarantees that each call returns a new instance, for example:
         Object newArray = Array.newInstance(classObject, arrayLen);
    
- getType—Like getInstance, but used if the factory method is in a differentclass.  Type is the type of object returned by the factory method, for example: 
	FileStore fs = Files.getFileStore(path);
    
- newType—Like newInstance, but used if the factory method is in a different class. Type is the type of object returned by the factory method, for example:
         BufferedReader br = Files.newBufferedReader(path);
    
- type—A concise alternative to getType and newType, for example:
         List<Complaint> litany = Collections.list(legacyLitany);
```

總之，大說時候，static factories are preferable, so avoid the reflex to provide public constructors without first considering static factories.