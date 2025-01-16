




对于 Java 求职者来说，HashMap 可谓是重中之重，是面试的必考点。然而 HashMap 的知识点非常多，复习起来花费精力很大。

### [01、HashMap 的底层数据结构是什么？](https://javabetter.cn/interview/java-hashmap-13.html#_01%E3%80%81hashmap%E7%9A%84%E5%BA%95%E5%B1%82%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E6%98%AF%E4%BB%80%E4%B9%88)

JDK 7 中，HashMap 由 “数组 + 链表” 组成，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的。

在 JDK 8 中，HashMap 由 “数组 + 链表 + 红黑树” 组成。链表过长，会严重影响 HashMap 的性能，而红黑树搜索的时间复杂度是 O (logn)，而链表是糟糕的 O (n)。因此，JDK 8 对数据结构做了进一步的优化，引入了红黑树，链表和红黑树在达到一定条件会进行转换：

- 当链表超过 8 且数据总量超过 64 时会转红黑树。
- 将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树，以减少搜索时间。

链表长度超过 8 体现在 putVal 方法中的这段代码：

```
//链表长度大于8转换为红黑树进行处理
if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
    treeifyBin(tab, hash);
```

table 长度为 64 体现在 treeifyBin 方法中的这段代码：：

```
final void treeifyBin(Node<K,V>[] tab, int hash) {
    int n, index; Node<K,V> e;
    if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
        resize();
}
```

MIN_TREEIFY_CAPACITY 的值正好为 64。

```
static final int MIN_TREEIFY_CAPACITY = 64;
```

JDK 8 中 HashMap 的结构示意图：

![[100_attachements/b49269536dfbf03fb7212232e136d550_MD5.png]]

### [02、为什么链表改为红黑树的阈值是 8?](https://javabetter.cn/interview/java-hashmap-13.html#_02%E3%80%81%E4%B8%BA%E4%BB%80%E4%B9%88%E9%93%BE%E8%A1%A8%E6%94%B9%E4%B8%BA%E7%BA%A2%E9%BB%91%E6%A0%91%E7%9A%84%E9%98%88%E5%80%BC%E6%98%AF-8)

因为泊松分布，我们来看作者在源码中的注释：

> Because TreeNodes are about twice the size of regular nodes, we use them only when bins contain enough nodes to warrant use (see TREEIFY_THRESHOLD). And when they become too small (due to removal or resizing) they are converted back to plain bins. In usages with well-distributed user hashCodes, tree bins are rarely used. Ideally, under random hashCodes, the frequency of nodes in bins follows a Poisson distribution (http://en.wikipedia.org/wiki/Poisson_distribution) with a parameter of about 0.5 on average for the default resizing threshold of 0.75, although with a large variance because of resizing granularity. Ignoring variance, the expected occurrences of list size k are (exp(-0.5) pow(0.5, k) / factorial(k)). The first values are: 0: 0.60653066  
> 1: 0.30326533  
> 2: 0.07581633  
> 3: 0.01263606  
> 4: 0.00157952  
> 5: 0.00015795  
> 6: 0.00001316  
> 7: 0.00000094  
> 8: 0.00000006  
> more: less than 1 in ten million

翻译过来大概的意思是：理想情况下使用随机的哈希码，容器中节点分布在 hash 桶中的频率遵循泊松分布，按照泊松分布的计算公式计算出了桶中元素个数和概率的对照表，可以看到链表中元素个数为 8 时的概率已经非常小，再多的就更少了，所以原作者在选择链表元素个数时选择了 8，是根据概率统计而选择的。

### [03、解决 hash 冲突的办法有哪些？HashMap 用的哪种？](https://javabetter.cn/interview/java-hashmap-13.html#_03%E3%80%81%E8%A7%A3%E5%86%B3hash%E5%86%B2%E7%AA%81%E7%9A%84%E5%8A%9E%E6%B3%95%E6%9C%89%E5%93%AA%E4%BA%9B-hashmap%E7%94%A8%E7%9A%84%E5%93%AA%E7%A7%8D)

解决 Hash 冲突方法有：

- 开放定址法：也称为再散列法，基本思想就是，如果 p=H (key) 出现冲突时，则以 p 为基础，再次 hash，p1=H (p), 如果 p1 再次出现冲突，则以 p1 为基础，以此类推，直到找到一个不冲突的哈希地址 pi。因此开放定址法所需要的 hash 表的长度要大于等于所需要存放的元素，而且因为存在再次 hash，所以只能在删除的节点上做标记，而不能真正删除节点。
- 再哈希法：双重散列，多重散列，提供多个不同的 hash 函数，当 R1=H1 (key1) 发生冲突时，再计算 R2=H2 (key1)，直到没有冲突为止。这样做虽然不易产生堆集，但增加了计算的时间。
- 链地址法：拉链法，将哈希值相同的元素构成一个同义词的单链表，并将单链表的头指针存放在哈希表的第 i 个单元中，查找、插入和删除主要在同义词链表中进行。链表法适用于经常进行插入和删除的情况。
- 建立公共溢出区：将哈希表分为公共表和溢出表，当溢出发生时，将所有溢出数据统一放到溢出区。

HashMap 中采用的是链地址法 。

### [04、为什么在解决 hash 冲突的时候，不直接用红黑树？而选择先用链表，再转红黑树？](https://javabetter.cn/interview/java-hashmap-13.html#_04%E3%80%81%E4%B8%BA%E4%BB%80%E4%B9%88%E5%9C%A8%E8%A7%A3%E5%86%B3-hash-%E5%86%B2%E7%AA%81%E7%9A%84%E6%97%B6%E5%80%99-%E4%B8%8D%E7%9B%B4%E6%8E%A5%E7%94%A8%E7%BA%A2%E9%BB%91%E6%A0%91-%E8%80%8C%E9%80%89%E6%8B%A9%E5%85%88%E7%94%A8%E9%93%BE%E8%A1%A8-%E5%86%8D%E8%BD%AC%E7%BA%A2%E9%BB%91%E6%A0%91)

因为红黑树需要进行左旋，右旋，变色这些操作来保持平衡，而单链表不需要。

当元素小于 8 个的时候，此时做查询操作，链表结构已经能保证查询性能。当元素大于 8 个的时候， 红黑树搜索时间复杂度是 O (logn)，而链表是 O (n)，此时需要红黑树来加快查询速度，但是新增节点的效率变慢了。

因此，如果一开始就用红黑树结构，元素太少，新增效率又比较慢，无疑这是浪费性能的。

### [05、HashMap 默认加载因子是多少？为什么是 0.75，不是 0.6 或者 0.8 ？](https://javabetter.cn/interview/java-hashmap-13.html#_05%E3%80%81hashmap%E9%BB%98%E8%AE%A4%E5%8A%A0%E8%BD%BD%E5%9B%A0%E5%AD%90%E6%98%AF%E5%A4%9A%E5%B0%91-%E4%B8%BA%E4%BB%80%E4%B9%88%E6%98%AF-0-75-%E4%B8%8D%E6%98%AF-0-6-%E6%88%96%E8%80%85-0-8)

作为一般规则，默认负载因子（0.75）在时间和空间成本上提供了很好的折衷。

[详情参照这篇](https://mp.weixin.qq.com/s/a3qfatEWizKK1CpYaxVBbA)

### [06、HashMap 中 key 的存储索引是怎么计算的？](https://javabetter.cn/interview/java-hashmap-13.html#_06%E3%80%81hashmap-%E4%B8%AD-key-%E7%9A%84%E5%AD%98%E5%82%A8%E7%B4%A2%E5%BC%95%E6%98%AF%E6%80%8E%E4%B9%88%E8%AE%A1%E7%AE%97%E7%9A%84)

首先根据 key 的值计算出 hashcode 的值，然后根据 hashcode 计算出 hash 值，最后通过 hash&（length-1）计算得到存储的位置。

[详情参照这篇](https://mp.weixin.qq.com/s/aS2dg4Dj1Efwujmv-6YTBg)

### [07、JDK 8 为什么要 hashcode 异或其右移十六位的值？](https://javabetter.cn/interview/java-hashmap-13.html#_07%E3%80%81jdk-8-%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A6%81-hashcode-%E5%BC%82%E6%88%96%E5%85%B6%E5%8F%B3%E7%A7%BB%E5%8D%81%E5%85%AD%E4%BD%8D%E7%9A%84%E5%80%BC)

因为在 JDK 7 中扰动了 4 次，计算 hash 值的性能会稍差一点点。

从速度、功效、质量来考虑，JDK 8 优化了高位运算的算法，通过 hashCode () 的高 16 位异或低 16 位实现：`(h = k.hashCode()) ^ (h >>> 16)`。

这么做可以在数组 table 的 length 比较小的时候，也能保证考虑到高低 Bit 都参与到 Hash 的计算中，同时不会有太大的开销。

### [08、为什么 hash 值要与 length-1 相与？](https://javabetter.cn/interview/java-hashmap-13.html#_08%E3%80%81%E4%B8%BA%E4%BB%80%E4%B9%88-hash-%E5%80%BC%E8%A6%81%E4%B8%8Elength-1%E7%9B%B8%E4%B8%8E)

- 把 hash 值对数组长度取模运算，模运算的消耗很大，没有位运算快。
- 当 length 总是 2 的 n 次方时，`h& (length-1)` 运算等价于对 length 取模，也就是 h% length，但是 & 比 % 具有更高的效率。

### [09、HashMap 数组的长度为什么是 2 的幂次方？](https://javabetter.cn/interview/java-hashmap-13.html#_09%E3%80%81hashmap%E6%95%B0%E7%BB%84%E7%9A%84%E9%95%BF%E5%BA%A6%E4%B8%BA%E4%BB%80%E4%B9%88%E6%98%AF-2-%E7%9A%84%E5%B9%82%E6%AC%A1%E6%96%B9)

2 的 N 次幂有助于减少碰撞的几率。如果 length 为 2 的幂次方，则 length-1 转化为二进制必定是 11111…… 的形式，在与 h 的二进制与操作效率会非常的快，而且空间不浪费。我们来举个例子，看下图：

![[100_attachements/e3f3c544dd970ab90a0c24f0a056b576_MD5.png]]

当 length =15 时，6 和 7 的结果一样，这样表示他们在 table 存储的位置是相同的，也就是产生了碰撞，6、7 就会在一个位置形成链表，4 和 5 的结果也是一样，这样就会导致查询速度降低。

如果我们进一步分析，还会发现空间浪费非常大，以 length=15 为例，在 1、3、5、7、9、11、13、15 这八处没有存放数据。因为 hash 值在与 14（即 1110）进行 & 运算时，得到的结果最后一位永远都是 0，即 0001、0011、0101、0111、1001、1011、1101、1111 位置处是不可能存储数据的。

**再补充数组容量计算的小奥秘。**

HashMap 构造函数允许用户传入的容量不是 2 的 n 次方，因为它可以自动地将传入的容量转换为 2 的 n 次方。会取大于或等于这个数的 且最近的 2 次幂作为 table 数组的初始容量，使用 tableSizeFor (int) 方法，如 tableSizeFor (10) = 16（2 的 4 次幂），tableSizeFor (20) = 32（2 的 5 次幂），也就是说 table 数组的长度总是 2 的次幂。JDK 8 源码如下：

```
static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```

让 cap-1 再赋值给 n 的目的是另找到的目标值大于或等于原值。例如二进制 1000，十进制数值为 8。如果不对它减 1 而直接操作，将得到答案 10000，即 16。显然不是结果。减 1 后二进制为 111，再进行操作则会得到原来的数值 1000，即 8。

### [10、HashMap 的 put 方法流程？](https://javabetter.cn/interview/java-hashmap-13.html#_10%E3%80%81hashmap-%E7%9A%84put%E6%96%B9%E6%B3%95%E6%B5%81%E7%A8%8B)

以 JDK 8 为例，简要流程如下：

1、首先根据 key 的值计算 hash 值，找到该元素在数组中存储的下标；

2、如果数组是空的，则调用 resize 进行初始化；

3、如果没有哈希冲突直接放在对应的数组下标里；

4、如果冲突了，且 key 已经存在，就覆盖掉 value；

5、如果冲突后，发现该节点是红黑树，就将这个节点挂在树上；

6、如果冲突后是链表，判断该链表是否大于 8 ，如果大于 8 并且数组容量小于 64，就进行扩容；如果链表节点大于 8 并且数组的容量大于 64，则将这个结构转换为红黑树；否则，链表插入键值对，若 key 存在，就覆盖掉 value。

![[100_attachements/3ce66c088e03ecb5351f1d67779b3b2a_MD5.png]]

### [11、HashMap 的扩容方式？](https://javabetter.cn/interview/java-hashmap-13.html#_11%E3%80%81hashmap-%E7%9A%84%E6%89%A9%E5%AE%B9%E6%96%B9%E5%BC%8F)

HashMap 在容量超过负载因子所定义的容量之后，就会扩容。

[详情参照这篇](https://mp.weixin.qq.com/s/0KSpdBJMfXSVH63XadVdmw)

### [12、一般用什么作为 HashMap 的 key?](https://javabetter.cn/interview/java-hashmap-13.html#_12%E3%80%81%E4%B8%80%E8%88%AC%E7%94%A8%E4%BB%80%E4%B9%88%E4%BD%9C%E4%B8%BAhashmap%E7%9A%84key)

一般用 Integer、String 这种不可变类当作 HashMap 的 key，String 最为常见。

- 因为字符串是不可变的，所以在它创建的时候 hashcode 就被缓存了，不需要重新计算。
- 因为获取对象的时候要用到 equals () 和 hashCode () 方法，那么键对象正确的重写这两个方法是非常重要的。Integer、String 这些类已经很规范的重写了 hashCode () 以及 equals () 方法。

### [13、HashMap 为什么线程不安全？](https://javabetter.cn/interview/java-hashmap-13.html#_13%E3%80%81hashmap%E4%B8%BA%E4%BB%80%E4%B9%88%E7%BA%BF%E7%A8%8B%E4%B8%8D%E5%AE%89%E5%85%A8)

- JDK 7 时多线程下扩容会造成死循环。
- 多线程的 put 可能导致元素的丢失。
- put 和 get 并发时，可能导致 get 为 null。

[详情参照这篇](https://mp.weixin.qq.com/s/qk_neCdzM3aB6pVWVTHhNw)

> 参考链接：[https://zhuanlan.zhihu.com/p/362214327](https://zhuanlan.zhihu.com/p/362214327)