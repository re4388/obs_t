
chapter 1

![[IMG-Modern Java in Action-20250104090405371.png]]
變成
```java
`inventory.sort(comparing(Apple::getWeight));`

```


![[IMG-Modern Java in Action-20250104090405666.png]]


```java

File[] hiddenFiles = new File(".").listFiles(File::isHidden);

```

只需用 Java 8 的方法引用::语法(即“把这 个方法作为值”)将其传给 listFiles 方法


与用对象引用传递对象类似(对象引用是用 new 创建的)，在 Java 8 里写下 File::isHidden 的时候，你就创建了一个方法引用，你同样可以传递它



--

![[IMG-Modern Java in Action-20250104090405954.png]]![[IMG-Modern Java in Action-20250104090406206.png]]



第一章看完