## 内存模型

### [#](https://xiaolincoding.com/interview/jvm.html#jvm%E7%9A%84%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8B)JVM 的内存模型介紹一下

根據 JVM8 規範，JVM 運行時内存共分為虛拟機棧、堆、元空間、程序計數器、本地方法棧五個部分。還有一部分内存叫直接内存，屬於操作系統的本地内存，也是可以直接操作的。

![[IMG-小林  Java 虛拟機面試題-20250127091034509.png]]

JVM 的内存結構主要分為以下幾個部分：

- **元空間**：元空間的本質和永久代類似，都是對 JVM 規範中方法區的實現。不過元空間與永久代之間最大的區别在於：元空間并不在虛拟機中，而是使用本地内存。
- **Java 虛拟機棧**：每個線程有一個私有的棧，随着線程的創建而創建。棧裏面存着的是一種叫 “棧幀” 的東西，每個方法會創建一個棧幀，棧幀中存放了局部變量表（基本數據類型和對象引用）、操作數棧、方法出口等信息。棧的大小可以固定也可以動态擴展。
- **本地方法棧**：與虛拟機棧類似，區别是虛拟機棧執行 Java 方法，本地方法站執行 native 方法。在虛拟機規範中對本地方法棧中方法使用的語言、使用方法與數據結構沒有強制規定，因此虛拟機可以自由實現它。
- **程序計數器**：程序計數器可以看成是當前線程所執行的字節碼指令的行號指示器。在任何一個确定的時刻，一個處理器（對於多内核來說是一個内核）都隻會執行一條線程中的指令。因此，為了線程切換後能恢複到正确的執行位置，每條線程都需要一個獨立的程序計數器，我們稱這類内存區域為 “線程私有” 内存。
- **堆内存**：堆内存是 JVM 所有線程共享的部分，在虛拟機啓動的時候就已經創建。所有的對象和數組都在堆上進行分配。這部分空間可通過 GC 進行回收。當申請不到空間時會抛出 OutOfMemoryError。堆是 JVM 内存占用最大，管理最複雜的一個區域。其唯一的用途就是存放對象實例：所有的對象實例及數組都在對上進行分配。jdk1.8 後，字符串常量池從永久代中剝離出來，存放在隊中。
- **直接内存**：直接内存并不是虛拟機運行時數據區的一部分，也不是 Java 虛拟機規範中定義的内存區域。在 JDK1.4 中新加入了 NIO (New Input/Output) 類，引入了一種基於通道 (Channel) 與緩沖區（Buffer）的 I/O 方式，它可以使用 native 函數庫直接分配堆外内存，然後通過一個存儲在 Java 堆中的 DirectByteBuffer 對象作為這塊内存的引用進行操作。這樣能在一些場景中顯著提高性能，因為避免了在 Java 堆和 Native 堆中來回複制數據。

### [#](https://xiaolincoding.com/interview/jvm.html#jvm%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B%E9%87%8C%E7%9A%84%E5%A0%86%E5%92%8C%E6%A0%88%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)JVM 内存模型裏的堆和棧有什麽區别？

- **用途**：棧主要用於存儲局部變量、方法調用的參數、方法返回地址以及一些臨時數據。每當一個方法被調用，一個棧幀（stack frame）就會在棧中創建，用於存儲該方法的信息，當方法執行完畢，棧幀也會被移除。堆用於存儲對象的實例（包括類的實例和數組）。當你使用 `new` 關鍵字創建一個對象時，對象的實例就會在堆上分配空間。
- **生命周期**：棧中的數據具有确定的生命周期，當一個方法調用結束時，其對應的棧幀就會被銷毀，棧中存儲的局部變量也會随之消失。堆中的對象生命周期不确定，對象會在垃圾回收機制（Garbage Collection, GC）檢測到對象不再被引用時才被回收。
- **存取速度**：棧的存取速度通常比堆快，因為棧遵循先進後出（LIFO, Last In First Out）的原則，操作簡單快速。堆的存取速度相對較慢，因為對象在堆上的分配和回收需要更多的時間，而且垃圾回收機制的運行也會影響性能。
- **存儲空間**：棧的空間相對較小，且固定，由操作系統管理。當棧溢出時，通常是因為遞歸過深或局部變量過大。堆的空間較大，動态擴展，由 JVM 管理。堆溢出通常是由於創建了態多的大對象或未能及時回收不再使用的對象。
- **可見性**：棧中的數據對線程是私有的，每個線程有自己的棧空間。堆中的數據對線程是共享的，所有線程都可以訪問堆上的對象。

### [#](https://xiaolincoding.com/interview/jvm.html#%E6%A0%88%E4%B8%AD%E5%AD%98%E7%9A%84%E5%88%B0%E5%BA%95%E6%98%AF%E6%8C%87%E9%92%88%E8%BF%98%E6%98%AF%E5%AF%B9%E8%B1%A1)棧中存的到底是指針還是對象？

在 JVM 内存模型中，棧（Stack）主要用於管理線程的局部變量和方法調用的上下文，而堆（Heap）則是用於存儲所有類的實例和數組。

當我們在棧中讨論 “存儲” 時，實際上指的是存儲基本類型的數據（如 int, double 等）和對象的引用，而不是對象本身。

這裏的關鍵點是，棧中存儲的**不是**對象，而是**對象的引用**。也就是說，當你在方法中聲明一個對象，比如 `MyObject obj = new MyObject();`，這裏的 `obj` 實際上是一個存儲在棧上的引用，指向堆中實際的對象實例。這個引用是一個固定大小的數據（例如在 64 位系統上是 8 字節），它指向堆中分配給對象的内存區域。

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%A0%86%E5%88%86%E4%B8%BA%E5%93%AA%E5%87%A0%E9%83%A8%E5%88%86%E5%91%A2)堆分為哪幾部分呢？

Java 堆（Heap）是 Java 虛拟機（JVM）中内存管理的一個重要區域，主要用於存放對象實例和數組。随着 JVM 的發展和不同垃圾收集器的實現，堆的具體劃分可能會有所不同，但通常可以分為以下幾個部分：

![[100_attachements/0b2e7379ad247347fc8a4b72c0de1538_MD5.webp]]

- **新生代（Young Generation）**: 新生代分為 Eden Space 和 Survivor Space。在 Eden Space 中， 大多數新創建的對象首先存放在這裏。Eden 區相對較小，當 Eden 區滿時，會觸發一次 Minor GC（新生代垃圾回收）。在 Survivor Spaces 中，通常分為兩個相等大小的區域，稱為 S0（Survivor 0）和 S1（Survivor 1）。在每次 Minor GC 後，存活下來的對象會被移動到其中一個 Survivor 空間，以繼續它們的生命周期。這兩個區域輪流充當對象的中轉站，幫助區分短暫存活的對象和長期存活的對象。
- **老年代（Old Generation/Tenured Generation）**: 存放過一次或多次 Minor GC 仍存活的對象會被移動到老年代。老年代中的對象生命周期較長，因此 Major GC（也稱為 Full GC，涉及老年代的垃圾回收）發生的頻率相對較低，但其執行時間通常比 Minor GC 長。老年代的空間通常比新生代大，以存儲更多的長期存活對象。
- **元空間（Metaspace）**: 從 Java 8 開始，永久代（Permanent Generation）被元空間取代，用於存儲類的元數據信息，如類的結構信息（如字段、方法信息等）。元空間并不在 Java 堆中，而是使用本地内存，這解決了永久代容易出現的内存溢出問題。
- **大對象區（Large Object Space / Humongous Objects）**: 在某些 JVM 實現中（如 G1 垃圾收集器），為大對象分配了專門的區域，稱為大對象區或 Humongous Objects 區域。大對象是指需要大量連續内存空間的對象，如大數組。這類對象直接分配在老年代，以避免因頻繁的年輕代晉升而導致的内存碎片化問題。

### [#](https://xiaolincoding.com/interview/jvm.html#%E7%A8%8B%E5%BA%8F%E8%AE%A1%E6%95%B0%E5%99%A8%E7%9A%84%E4%BD%9C%E7%94%A8-%E4%B8%BA%E4%BB%80%E4%B9%88%E6%98%AF%E7%A7%81%E6%9C%89%E7%9A%84)程序計數器的作用，為什麽是私有的？

Java 程序是支持多線程一起運行的，多個線程一起運行的時候 cpu 會有一個調動器組件給它們分配時間片，比如說會給線程 1 分給一個時間片，它在時間片内如果它的代碼沒有執行完，它就會把線程 1 的狀态執行一個暫存，切換到線程 2 去，執行線程 2 的代碼，等線程 2 的代碼執行到了一定程度，線程 2 的時間片用完了，再切換回來，再繼續執行線程 1 剩餘部分的代碼。

我們考慮一下，如果在線程切換的過程中，下一條指令執行到哪裏了，是不是還是會用到我們的程序計數器啊。每個線程都有自己的程序計數器，因為它們各自執行的代碼的指令地址是不一樣的呀，所以每個線程都應該有自己的程序計數器。

### [#](https://xiaolincoding.com/interview/jvm.html#%E6%96%B9%E6%B3%95%E5%8C%BA%E4%B8%AD%E7%9A%84%E6%96%B9%E6%B3%95%E7%9A%84%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B)方法區中的方法的執行過程？

當程序中通過對象或類直接調用某個方法時，主要包括以下幾個步驟：

- **解析方法調用**：JVM 會根據方法的符號引用找到實際的方法地址（如果之前沒有解析過的話）。
- **棧幀創建**：在調用一個方法前，JVM 會在當前線程的 Java 虛拟機棧中為該方法分配一個新的棧幀，用於存儲局部變量表、操作數棧、動态鏈接、方法出口等信息。
- **執行方法**：執行方法内的字節碼指令，涉及的操作可能包括局部變量的讀寫、操作數棧的操作、跳轉控制、對象創建、方法調用等。
- **返回處理**：方法執行完畢後，可能會返回一個結果給調用者，并清理當前棧幀，恢複調用者的執行環境。

### [#](https://xiaolincoding.com/interview/jvm.html#%E6%96%B9%E6%B3%95%E5%8C%BA%E4%B8%AD%E8%BF%98%E6%9C%89%E5%93%AA%E4%BA%9B%E4%B8%9C%E8%A5%BF)方法區中還有哪些東西？

《深入理解 Java 虛拟機》書中對方法區（Method Area）存儲内容描述如下：它用於存儲已被虛拟機加載的**類型信息、常量、靜态變量、即時編譯器編譯後的代碼緩存**等。

- 類信息：包括類的結構信息、類的訪問修飾符、父類與接口等信息。
- 常量池：存儲類和接口中的常量，包括字面值常量、符號引用，以及運行時常量池。
- 靜态變量：存儲類的靜态變量，這些變量在類初始化的時候被賦值。
- 方法字節碼：存儲類的方法字節碼，即編譯後的代碼。
- 符號引用：存儲類和方法的符號引用，是一種直接引用不同於直接引用的引用類型。
- 運行時常量池：存儲着在類文件中的常量池數據，在類加載後在方法區生成該運行時常量池。
- 常量池緩存：用於提升類加載的效率，將常用的常量緩存起來方便使用。

### [#](https://xiaolincoding.com/interview/jvm.html#string%E4%BF%9D%E5%AD%98%E5%9C%A8%E5%93%AA%E9%87%8C%E5%91%A2)String 保存在哪裏呢？

String 保存在字符串常量池中，不同於其他對象，它的值是不可變的，且可以被多個引用共享。

### [#](https://xiaolincoding.com/interview/jvm.html#string-s-new-string-abc-%E6%89%A7%E8%A1%8C%E8%BF%87%E7%A8%8B%E4%B8%AD%E5%88%86%E5%88%AB%E5%AF%B9%E5%BA%94%E5%93%AA%E4%BA%9B%E5%86%85%E5%AD%98%E5%8C%BA%E5%9F%9F)String s = new String（“abc”）執行過程中分别對應哪些内存區域？

首先，我們看到這個代碼中有一個 new 關鍵字，我們知道 **new** 指令是創建一個類的實例對象并完成加載初始化的，因此這個字符串對象是在 **運行期**才能确定的，創建的字符串對象是在**堆内存上**。

其次，在 String 的構造方法中傳遞了一個字符串 abc，由於這裏的 abc 是被 final 修飾的屬性，所以它是一個字符串常量。在首次構建這個對象時，JVM 拿字面量 "abc" 去字符串常量池試圖獲取其對應 String 對象的引用。於是在堆中創建了一個 "abc" 的 String 對象，并將其引用保存到字符串常量池中，然後返回；

所以，**如果 abc 這個字符串常量不存在，則創建兩個對象，分别是 abc 這個字符串常量，以及 new String 這個實例對象。如果 abc 這字符串常量存在，則隻會創建一個對象**。

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%BC%95%E7%94%A8%E7%B1%BB%E5%9E%8B%E6%9C%89%E5%93%AA%E4%BA%9B-%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB)引用類型有哪些？有什麽區别？

引用類型主要分為強軟弱虛四種：

- 強引用指的就是代碼中普遍存在的賦值方式，比如 A a = new A () 這種。強引用關聯的對象，永遠不會被 GC 回收。
- 軟引用可以用 SoftReference 來描述，指的是那些有用但是不是必須要的對象。系統在發生内存溢出前會對這類引用的對象進行回收。
- 弱引用可以用 WeakReference 來描述，他的強度比軟引用更低一點，弱引用的對象下一次 GC 的時候一定會被回收，而不管内存是否足夠。
- 虛引用也被稱作幻影引用，是最弱的引用關系，可以用 PhantomReference 來描述，他必須和 ReferenceQueue 一起使用，同樣的當發生 GC 的時候，虛引用也會被回收。可以用虛引用來管理堆外内存。

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%BC%B1%E5%BC%95%E7%94%A8%E4%BA%86%E8%A7%A3%E5%90%97-%E4%B8%BE%E4%BE%8B%E8%AF%B4%E6%98%8E%E5%9C%A8%E5%93%AA%E9%87%8C%E5%8F%AF%E4%BB%A5%E7%94%A8)弱引用了解嗎？舉例說明在哪裏可以用？

Java 中的弱引用是一種引用類型，它不會阻止一個對象被垃圾回收。

在 Java 中，弱引用是通過 `Java.lang.ref.WeakReference` 類實現的。弱引用的一個主要用途是創建非強制性的對象引用，這些引用可以在内存壓力大時被垃圾回收器清理，從而避免内存洩露。

弱引用的使用場景：

- **緩存系統**：弱引用常用於實現緩存，特别是當希望緩存項能夠在内存壓力下自動釋放時。如果緩存的大小不受控制，可能會導致内存溢出。使用弱引用來維護緩存，可以讓 JVM 在需要更多内存時自動清理這些緩存對象。
- **對象池**：在對象池中，弱引用可以用來管理那些暫時不使用的對象。當對象不再被強引用時，它們可以被垃圾回收，釋放内存。
- **避免内存洩露**：當一個對象不應該被長期引用時，使用弱引用可以防止該對象被意外地保留，從而避免潛在的内存洩露。

示例代碼：

假設我們有一個緩存系統，我們使用弱引用來維護緩存中的對象：

```
import Java.lang.ref.WeakReference;
import Java.util.HashMap;
import Java.util.Map;

public class CacheExample {

    private Map<String, WeakReference<MyHeavyObject>> cache = new HashMap<>();

    public MyHeavyObject get(String key) {
        WeakReference<MyHeavyObject> ref = cache.get(key);
        if (ref != null) {
            return ref.get();
        } else {
            MyHeavyObject obj = new MyHeavyObject();
            cache.put(key, new WeakReference<>(obj));
            return obj;
        }
    }

    // 假設MyHeavyObject是一個占用大量内存的對象
    private static class MyHeavyObject {
        private byte[] largeData = new byte[1024 * 1024 * 10]; // 10MB data
    }
}
```

在這個例子中，使用 `WeakReference` 來存儲 `MyHeavyObject` 實例，當内存壓力增大時，垃圾回收器可以自由地回收這些對象，而不會影響緩存的正常運行。

如果一個對象被垃圾回收，下次嘗試從緩存中獲取時，`get()` 方法會返回 `null`，這時我們可以重新創建對象并將其放入緩存中。因此，使用弱引用時要注意，一旦對象被垃圾回收，通過弱引用獲取的對象可能會變為 `null`，因此在使用前通常需要檢查這一點。

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F%E5%92%8C%E5%86%85%E5%AD%98%E6%BA%A2%E5%87%BA%E7%9A%84%E7%90%86%E8%A7%A3)内存洩漏和内存溢出的理解？

**内存洩露**：内存洩漏是指程序在運行過程中不再使用的對象仍然被引用，而無法被垃圾收集器回收，從而導致可用内存逐漸減少。雖然在 Java 中，垃圾回收機制會自動回收不再使用的對象，但如果有對象仍被不再使用的引用持有，垃圾收集器無法回收這些内存，最終可能導致程序的内存使用不斷增加。

内存洩露常見原因：

- **靜态集合**：使用靜态數據結構（如 `HashMap` 或 `ArrayList`）存儲對象，且未清理。
- **事件監聽**：未取消對事件源的監聽，導致對象持續被引用。
- **線程**：未停止的線程可能持有對象引用，無法被回收。

内存溢出：内存溢出是指 Java 虛拟機（JVM）在申請内存時，無法找到足夠的内存，最終引發 `OutOfMemoryError`。這通常發生在堆内存不足以存放新創建的對象時。

内存溢出常見原因：

- **大量對象創建**：程序中不斷創建大量對象，超出 JVM 堆的限制。
- **持久引用**：大型數據結構（如緩存、集合等）長時間持有對象引用，導致内存累積。
- **遞歸調用**：深度遞歸導致棧溢出。

### [#](https://xiaolincoding.com/interview/jvm.html#jvm-%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84%E6%9C%89%E5%93%AA%E5%87%A0%E7%A7%8D%E5%86%85%E5%AD%98%E6%BA%A2%E5%87%BA%E7%9A%84%E6%83%85%E5%86%B5)jvm 内存結構有哪幾種内存溢出的情況？

- **堆内存溢出**：當出現 Java.lang.OutOfMemoryError:Java heap space 異常時，就是堆内存溢出了。原因是代碼中可能存在大對象分配，或者發生了内存洩露，導致在多次 GC 之後，還是無法找到一塊足夠大的内存容納當前對象。
- **棧溢出**：如果我們寫一段程序不斷的進行遞歸調用，而且沒有退出條件，就會導致不斷地進行壓棧。類似這種情況，JVM 實際會抛出 StackOverFlowError；當然，如果 JVM 試圖去擴展棧空間的的時候失敗，則會抛出 OutOfMemoryError。
- **元空間溢出**：元空間的溢出，系統會抛出 Java.lang.OutOfMemoryError: Metaspace。出現這個異常的問題的原因是系統的代碼非常多或引用的第三方包非常多或者通過動态代碼生成類加載等方法，導致元空間的内存占用很大。
- **直接内存内存溢出**：在使用 ByteBuffer 中的 allocateDirect () 的時候會用到，很多 JavaNIO (像 netty) 的框架中被封裝為其他的方法，出現該問題時會抛出 Java.lang.OutOfMemoryError: Direct buffer memory 異常。

### [#](https://xiaolincoding.com/interview/jvm.html#%E6%9C%89%E5%85%B7%E4%BD%93%E7%9A%84%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F%E5%92%8C%E5%86%85%E5%AD%98%E6%BA%A2%E5%87%BA%E7%9A%84%E4%BE%8B%E5%AD%90%E4%B9%88%E8%AF%B7%E4%B8%BE%E4%BE%8B%E5%8F%8A%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88)有具體的内存洩漏和内存溢出的例子麽請舉例及解決方案？

> 1、靜态屬性導致内存洩露

會導致内存洩露的一種情況就是大量使用 static 靜态變量。在 Java 中，靜态屬性的生命周期通常伴随着應用整個生命周期（除非 ClassLoader 符合垃圾回收的條件）。下面來看一個具體的會導致内存洩露的實例：

```
public class StaticTest {
    public static List<Double> list = new ArrayList<>();
    public void populateList() {
        for (int i = 0; i < 10000000; i++) {
            list.add(Math.random());
        }
        Log.info("Debug Point 2");
    }
    public static void main(String[] args) {
        Log.info("Debug Point 1");
        new StaticTest().populateList();
        Log.info("Debug Point 3");
    }
}
```

如果監控内存堆内存的變化，會發現在打印 Point1 和 Point2 之間，堆内存會有一個明顯的增長趨勢圖。但當執行完 populateList 方法之後，對堆内存并沒有被垃圾回收器進行回收。

![[IMG-小林  Java 虛拟機面試題-20250127091034749.png]]

但針對上述程序，如果將定義 list 的變量前的 static 關鍵字去掉，再次執行程序，會發現内存發生了具體的變化。VisualVM 監控信息如下圖：

![[IMG-小林  Java 虛拟機面試題-20250127091034844.png]]

對比兩個圖可以看出，程序執行的前半部分内存使用情況都一樣，但當執行完 populateList 方法之後，後者不再有引用指向對應的數據，垃圾回收器便進行了回收操作。因此，我們要十分留意 static 的變量，如果集合或大量的對象定義為 static 的，它們會停留在整個應用程序的生命周期當中。而它們所占用的内存空間，本可以用於其他地方。

那麽如何優化呢？第一，進來減少靜态變量；第二，如果使用單例，盡量采用懶加載。

> 2、 未關閉的資源

無論什麽時候當我們創建一個連接或打開一個流，JVM 都會分配内存給這些資源。比如，數據庫鏈接、輸入流和 session 對象。

忘記關閉這些資源，會阻塞内存，從而導致 GC 無法進行清理。特别是當程序發生異常時，沒有在 finally 中進行資源關閉的情況。這些未正常關閉的連接，如果不進行處理，輕則影響程序性能，重則導致 OutOfMemoryError 異常發生。

如果進行處理呢？第一，始終記得在 finally 中進行資源的關閉；第二，關閉連接的自身代碼不能發生異常；第三，Java7 以上版本可使用 try-with-resources 代碼方式進行資源關閉。

> 3、 使用 ThreadLocal

ThreadLocal 提供了線程本地變量，它可以保證訪問到的變量屬於當前線程，每個線程都保存有一個變量副本，每個線程的變量都不同。ThreadLocal 相當於提供了一種線程隔離，將變量與線程相綁定，從而實現線程安全的特性。

![[IMG-小林  Java 虛拟機面試題-20250127091034959.png]]

ThreadLocal 的實現中，每個 Thread 維護一個 ThreadLocalMap 映射表，key 是 ThreadLocal 實例本身，value 是真正需要存儲的 Object。

ThreadLocalMap 使用 ThreadLocal 的弱引用作為 key，如果一個 ThreadLocal 沒有外部強引用來引用它，那麽系統 GC 時，這個 ThreadLocal 勢必會被回收，這樣一來，ThreadLocalMap 中就會出現 key 為 null 的 Entry，就沒有辦法訪問這些 key 為 null 的 Entry 的 value。

如果當前線程遲遲不結束的話，這些 key 為 null 的 Entry 的 value 就會一直存在一條強引用鏈：Thread Ref -> Thread -> ThreaLocalMap -> Entry -> value 永遠無法回收，造成内存洩漏。

如何解決此問題？

- 第一，使用 ThreadLocal 提供的 remove 方法，可對當前線程中的 value 值進行移除；
- 第二，不要使用 ThreadLocal.set (null) 的方式清除 value，它實際上并沒有清除值，而是查找與當前線程關聯的 Map 并將鍵值對分别設置為當前線程和 null。
- 第三，最好將 ThreadLocal 視為需要在 finally 塊中關閉的資源，以确保即使在發生異常的情況下也始終關閉該資源。

```
try {
    threadLocal.set(System.nanoTime());
    //... further processing
} finally {
    threadLocal.remove();
}
```

## [#](https://xiaolincoding.com/interview/jvm.html#%E7%B1%BB%E5%88%9D%E5%A7%8B%E5%8C%96%E5%92%8C%E7%B1%BB%E5%8A%A0%E8%BD%BD)類初始化和類加載

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%88%9B%E5%BB%BA%E5%AF%B9%E8%B1%A1%E7%9A%84%E8%BF%87%E7%A8%8B)創建對象的過程？

![[IMG-小林  Java 虛拟機面試題-20250127091035046.png]]在 Java 中創建對象的過程包括以下幾個步驟：

1. **類加載檢查**：虛拟機遇到一條 new 指令時，首先將去檢查這個指令的參數是否能在**常量池**中定位到一個類的**符號引用**，并且檢查這個符號引用代表的類是否已被**加載過、解析和初始化**過。如果沒有，那必須先執行相應的**類加載過程**。
2. **分配内存**：在類加載檢查通過後，接下來虛拟機將為新生對象分配内存。對象所需的**内存大小**在**類加載**完成後便可确定，為對象分配空間的任務等同於把一塊确定大小的内存從 Java 堆中劃分出來。
3. **初始化零值**：内存分配完成後，虛拟機需要將分配到的内存空間都初始化為零值（不包括對象頭），這一步操作保證了對象的實例字段在 Java 代碼中可以不賦初始值就直接使用，程序能訪問到這些字段的數據類型所對應的零值。
4. **進行必要設置，比如對象頭**：初始化零值完成之後，虛拟機要對對象進行**必要的設置**，例如這個對象是哪個類的實例、如何才能找到類的元數據信息、對象的哈希碼、對象的 GC 分代年齡等信息。這些信息存放在**對象頭**中。另外，根據虛拟機當前運行狀态的不同，如是否啓用偏向鎖等，對象頭會有不同的設置方式。
5. **執行 init 方法**：在上面工作都完成之後，從虛拟機的視角來看，一個新的對象已經産生了，但從 Java 程序的視角來看，對象創建才剛開始 —— 構造函數，即 class 文件中的方法還沒有執行，所有的字段都還為零，**對象需要的其他資源和狀态信息**還沒有按照預定的意圖構造好。所以一般來說，執行 new 指令之後會接着執行方法，把對象按照程序員的意願進行初始化，這樣一個真正可用的對象才算完全被構造出來。

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%AF%B9%E8%B1%A1%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F)對象的生命周期

對象的生命周期包括創建、使用和銷毀三個階段：

- 創建：對象通過關鍵字 new 在堆内存中被實例化，構造函數被調用，對象的内存空間被分配。
- 使用：對象被引用并執行相應的操作，可以通過引用訪問對象的屬性和方法，在程序運行過程中被不斷使用。
- 銷毀：當對象不再被引用時，通過垃圾回收機制自動回收對象所占用的内存空間。垃圾回收器會在适當的時候檢測并回收不再被引用的對象，釋放對象占用的内存空間，完成對象的銷毀過程。

### [#](https://xiaolincoding.com/interview/jvm.html#%E7%B1%BB%E5%8A%A0%E8%BD%BD%E5%99%A8%E6%9C%89%E5%93%AA%E4%BA%9B)類加載器有哪些？

![[IMG-小林  Java 虛拟機面試題-20250127091035143.png]]

- **啓動類加載器（Bootstrap Class Loader）**：這是最頂層的類加載器，負責加載 Java 的核心庫（如位於 jre/lib/rt.jar 中的類），它是用 C++ 編寫的，是 JVM 的一部分。啓動類加載器無法被 Java 程序直接引用。
- **擴展類加載器（Extension Class Loader）**：它是 Java 語言實現的，繼承自 ClassLoader 類，負責加載 Java 擴展目錄（jre/lib/ext 或由系統變量 Java.ext.dirs 指定的目錄）下的 jar 包和類庫。擴展類加載器由啓動類加載器加載，并且父加載器就是啓動類加載器。
- **系統類加載器（System Class Loader）/ 應用程序類加載器（Application Class Loader）**：這也是 Java 語言實現的，負責加載用戶類路徑（ClassPath）上的指定類庫，是我們平時編寫 Java 程序時默認使用的類加載器。系統類加載器的父加載器是擴展類加載器。它可以通過 ClassLoader.getSystemClassLoader () 方法獲取到。
- **自定義類加載器（Custom Class Loader）**：開發者可以根據需求定制類的加載方式，比如從網絡加載 class 文件、數據庫、甚至是加密的文件中加載類等。自定義類加載器可以用來擴展 Java 應用程序的靈活性和安全性，是 Java 動态性的一個重要體現。

這些類加載器之間的關系形成了雙親委派模型，其核心思想是當一個類加載器收到類加載的請求時，首先不會自己去嘗試加載這個類，而是把這個請求委派給父類加載器去完成，每一層次的類加載器都是如此，因此所有的加載請求最終都應該傳送到頂層的啓動類加載器中。

隻有當父加載器反饋自己無法完成這個加載請求（它的搜索範圍中沒有找到所需的類）時，子加載器才會嘗試自己去加載。

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%8F%8C%E4%BA%B2%E5%A7%94%E6%B4%BE%E6%A8%A1%E5%9E%8B%E7%9A%84%E4%BD%9C%E7%94%A8)雙親委派模型的作用

- **保證類的唯一性**：通過委托機制，确保了所有加載請求都會傳遞到啓動類加載器，避免了不同類加載器重複加載相同類的情況，保證了 Java 核心類庫的統一性，也防止了用戶自定義類覆蓋核心類庫的可能。
- **保證安全性**：由於 Java 核心庫被啓動類加載器加載，而啓動類加載器隻加載信任的類路徑中的類，這樣可以防止不可信的類假冒核心類，增強了系統的安全性。例如，惡意代碼無法自定義一個 Java.lang.System 類并加載到 JVM 中，因為這個請求會被委托給啓動類加載器，而啓動類加載器隻會加載標準的 Java 庫中的類。
- **支持隔離和層次劃分**：雙親委派模型支持不同層次的類加載器服務於不同的類加載需求，如應用程序類加載器加載用戶代碼，擴展類加載器加載擴展框架，啓動類加載器加載核心庫。這種層次化的劃分有助於實現沙箱安全機制，保證了各個層級類加載器的職責清晰，也便於維護和擴展。
- **簡化了加載流程**：通過委派，大部分類能夠被正确的類加載器加載，減少了每個加載器需要處理的類的數量，簡化了類的加載過程，提高了加載效率。

### [#](https://xiaolincoding.com/interview/jvm.html#%E8%AE%B2%E4%B8%80%E4%B8%8B%E7%B1%BB%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B)講一下類加載過程？

類從被加載到虛拟機内存開始，到卸載出内存為止，它的整個生命周期包括以下 7 個階段：

![[IMG-小林  Java 虛拟機面試題-20250127091035243.png]]

- **加載**：通過類的全限定名（包名 + 類名），獲取到該類的.class 文件的二進制字節流，將二進制字節流所代表的靜态存儲結構，轉化為方法區運行時的數據結構，在内存中生成一個代表該類的 Java.lang.Class 對象，作為方法區這個類的各種數據的訪問入口
    
- **連接**：驗證、準備、解析 3 個階段統稱為連接。
    
    - **驗證**：确保 class 文件中的字節流包含的信息，符合當前虛拟機的要求，保證這個被加載的 class 類的正确性，不會危害到虛拟機的安全。驗證階段大致會完成以下四個階段的檢驗動作：文件格式校驗、元數據驗證、字節碼驗證、符號引用驗證
        
    - **準備**：為類中的靜态字段分配内存，并設置默認的初始值，比如 int 類型初始值是 0。被 final 修飾的 static 字段不會設置，因為 final 在編譯的時候就分配了
        
    - **解析**：解析階段是虛拟機將常量池的「符號引用」直接替換為「直接引用」的過程。符號引用是以一組符號來描述所引用的目標，符號可以是任何形式的字面量，隻要使用的時候可以無歧義地定位到目標即可。直接引用可以是直接指向目標的指針、相對偏移量或是一個能間接定位到目標的句柄，直接引用是和虛拟機實現的内存布局相關的。如果有了直接引用， 那引用的目標必定已經存在在内存中了。
        
- **初始化**：初始化是整個類加載過程的最後一個階段，初始化階段簡單來說就是執行類的構造器方法（() ），要注意的是這裏的構造器方法 () 并不是開發者寫的，而是編譯器自動生成的。
    
- **使用**：使用類或者創建對象
    
- **卸載**：如果有下面的情況，類就會被卸載：1. 該類所有的實例都已經被回收，也就是 Java 堆中不存在該類的任何實例。2. 加載該類的 ClassLoader 已經被回收。 3. 類對應的 Java.lang.Class 對象沒有任何地方被引用，無法在任何地方通過反射訪問該類的方法。
    

### [#](https://xiaolincoding.com/interview/jvm.html#%E8%AE%B2%E4%B8%80%E4%B8%8B%E7%B1%BB%E7%9A%84%E5%8A%A0%E8%BD%BD%E5%92%8C%E5%8F%8C%E4%BA%B2%E5%A7%94%E6%B4%BE%E5%8E%9F%E5%88%99)講一下類的加載和雙親委派原則

我們把 Java 的類加載過程分為三個主要步驟：加載、鏈接、初始化。

首先是加載階段（Loading），它是 Java 將字節碼數據從不同的數據源讀取到 JVM 中，并映射為 JVM 認可的數據結構（Class 對象），這裏的數據源可能是各種各樣的形态，如 jar 文件、class 文件，甚至是網絡數據源等；如果輸入數據不是 ClassFile 的結構，則會抛出 ClassFormatError。

加載階段是用戶參與的階段，我們可以自定義類加載器，去實現自己的類加載過程。

第二階段是鏈接（Linking），這是核心的步驟，簡單說是把原始的類定義信息平滑地轉化入 JVM 運行的過程中。這裏可進一步細分為三個步驟：

- 驗證（Verification），這是虛拟機安全的重要保障，JVM 需要核驗字節信息是符合 Java 虛拟機規範的，否則就被認為是 VerifyError，這樣就防止了惡意信息或者不合規的信息危害 JVM 的運行，驗證階段有可能觸發更多 class 的加載。
- 準備（Preparation），創建類或接口中的靜态變量，并初始化靜态變量的初始值。但這裏的 “初始化” 和下面的顯式初始化階段是有區别的，側重點在於分配所需要的内存空間，不會去執行更進一步的 JVM 指令。
- 解析（Resolution），在這一步會將常量池中的符號引用（symbolic reference）替換為直接引用。

最後是初始化階段（initialization），這一步真正去執行類初始化的代碼邏輯，包括靜态字段賦值的動作，以及執行類定義中的靜态初始化塊内的邏輯，編譯器在編譯階段就會把這部分邏輯整理好，父類型的初始化邏輯優先於當前類型的邏輯。

再來談談雙親委派模型，簡單說就是當類加載器（Class-Loader）試圖加載某個類型的時候，除非父加載器找不到相應類型，否則盡量將這個任務代理給當前加載器的父加載器去做。使用委派模型的目的是避免重複加載 Java 類型。

## [#](https://xiaolincoding.com/interview/jvm.html#%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6)垃圾回收

### [#](https://xiaolincoding.com/interview/jvm.html#%E4%BB%80%E4%B9%88%E6%98%AFjava%E9%87%8C%E7%9A%84%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6-%E5%A6%82%E4%BD%95%E8%A7%A6%E5%8F%91%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6)什麽是 Java 裏的垃圾回收？如何觸發垃圾回收？

垃圾回收（Garbage Collection, GC）是自動管理内存的一種機制，它負責自動釋放不再被程序引用的對象所占用的内存，這種機制減少了内存洩漏和内存管理錯誤的可能性。垃圾回收可以通過多種方式觸發，具體如下：

- **内存不足時**：當 JVM 檢測到堆内存不足，無法為新的對象分配内存時，會自動觸發垃圾回收。
- **手動請求**：雖然垃圾回收是自動的，開發者可以通過調用 `System.gc()` 或 `Runtime.getRuntime().gc()` 建議 JVM 進行垃圾回收。不過這隻是一個建議，并不能保證立即執行。
- **JVM 參數**：啓動 Java 應用時可以通過 JVM 參數來調整垃圾回收的行為，比如：`-Xmx`（最大堆大小）、`-Xms`（初始堆大小）等。
- **對象數量或内存使用達到阈值**：垃圾收集器内部實現了一些策略，以監控對象的創建和内存使用，達到某個阈值時觸發垃圾回收。

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%88%A4%E6%96%AD%E5%9E%83%E5%9C%BE%E7%9A%84%E6%96%B9%E6%B3%95%E6%9C%89%E5%93%AA%E4%BA%9B)判斷垃圾的方法有哪些？

在 Java 中，判斷對象是否為垃圾（即不再被使用，可以被垃圾回收器回收）主要依據兩種主流的垃圾回收算法來實現：**引用計數法和可達性分析算法**。

> 引用計數法（Reference Counting）

- **原理**：為每個對象分配一個引用計數器，每當有一個地方引用它時，計數器加 1；當引用失效時，計數器減 1。當計數器為 0 時，表示對象不再被任何變量引用，可以被回收。
- **缺點**：不能解決循環引用的問題，即兩個對象相互引用，但不再被其他任何對象引用，這時引用計數器不會為 0，導致對象無法被回收。

> 可達性分析算法（Reachability Analysis）

![[IMG-小林  Java 虛拟機面試題-20250127091035352.png]]

Java 虛拟機主要采用此算法來判斷對象是否為垃圾。

- **原理**：從一組稱為 GC Roots（垃圾收集根）的對象出發，向下追溯它們引用的對象，以及這些對象引用的其他對象，以此類推。如果一個對象到 GC Roots 沒有任何引用鏈相連（即從 GC Roots 到這個對象不可達），那麽這個對象就被認為是不可達的，可以被回收。GC Roots 對象包括：虛拟機棧（棧幀中的本地變量表）中引用的對象、方法區中類靜态屬性引用的對象、本地方法棧中 JNI（Java Native Interface）引用的對象、活躍線程的引用等。

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E7%AE%97%E6%B3%95%E6%98%AF%E4%BB%80%E4%B9%88-%E6%98%AF%E4%B8%BA%E4%BA%86%E8%A7%A3%E5%86%B3%E4%BA%86%E4%BB%80%E4%B9%88%E9%97%AE%E9%A2%98)垃圾回收算法是什麽，是為了解決了什麽問題？

JVM 有垃圾回收機制的原因是為了解決内存管理的問題。在傳統的編程語言中，開發人員需要手動分配和釋放内存，這可能導致内存洩漏、内存溢出等問題。而 Java 作為一種高級語言，旨在提供更簡單、更安全的編程環境，因此引入了垃圾回收機制來自動管理内存。

垃圾回收機制的主要目標是**自動檢測和回收**不再使用的對象，從而釋放它們所占用的内存空間。這樣可以避免内存洩漏（一些對象被分配了内存卻無法被釋放，導致内存資源的浪費）。同時，垃圾回收機制還可以防止内存溢出（即程序需要的内存超過了可用内存的情況）。

通過垃圾回收機制，JVM 可以在程序運行時自動識别和清理不再使用的對象，使得開發人員無需手動管理内存。這樣可以提高開發效率、減少錯誤，并且使程序更加可靠和穩定。

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E7%AE%97%E6%B3%95%E6%9C%89%E5%93%AA%E4%BA%9B)垃圾回收算法有哪些？

- **標記 - 清除算法**：標記 - 清除算法分為 “標記” 和 “清除” 兩個階段，首先通過可達性分析，標記出所有需要回收的對象，然後統一回收所有被標記的對象。標記 - 清除算法有兩個缺陷，一個是效率問題，標記和清除的過程效率都不高，另外一個就是，清除結束後會造成大量的碎片空間。有可能會造成在申請大塊内存的時候因為沒有足夠的連續空間導致再次 GC。
- **複制算法**：為了解決碎片空間的問題，出現了 “複制算法”。複制算法的原理是，將内存分成兩塊，每次申請内存時都使用其中的一塊，當内存不夠時，將這一塊内存中所有存活的複制到另一塊上。然後將然後再把已使用的内存整個清理掉。複制算法解決了空間碎片的問題。但是也帶來了新的問題。因為每次在申請内存時，都隻能使用一半的内存空間。内存利用率嚴重不足。
- **標記 - 整理算法**：複制算法在 GC 之後存活對象較少的情況下效率比較高，但如果存活對象比較多時，會執行較多的複制操作，效率就會下降。而老年代的對象在 GC 之後的存活率就比較高，所以就有人提出了 “標記 - 整理算法”。標記 - 整理算法的 “標記” 過程與 “標記 - 清除算法” 的標記過程一致，但標記之後不會直接清理。而是將所有存活對象都移動到内存的一端。移動結束後直接清理掉剩餘部分。
- **分代回收算法**：分代收集是將内存劃分成了新生代和老年代。分配的依據是對象的生存周期，或者說經曆過的 GC 次數。對象創建時，一般在新生代申請内存，當經曆一次 GC 之後如果對還存活，那麽對象的年齡 +1。當年齡超過一定值 (默認是 15，可以通過參數 -XX:MaxTenuringThreshold 來設定) 後，如果對象還存活，那麽該對象會進入老年代。

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8%E6%9C%89%E5%93%AA%E4%BA%9B)垃圾回收器有哪些？

![[IMG-小林  Java 虛拟機面試題-20250127091035458.png]]

- Serial 收集器（複制算法): 新生代單線程收集器，標記和清理都是單線程，優點是簡單高效；
- ParNew 收集器 (複制算法): 新生代收并行集器，實際上是 Serial 收集器的多線程版本，在多核 CPU 環境下有着比 Serial 更好的表現；
- Parallel Scavenge 收集器 (複制算法): 新生代并行收集器，追求高吞吐量，高效利用 CPU。吞吐量 = 用戶線程時間 /(用戶線程時間 + GC 線程時間)，高吞吐量可以高效率的利用 CPU 時間，盡快完成程序的運算任務，适合後台應用等對交互相應要求不高的場景；
- Serial Old 收集器 (標記 - 整理算法): 老年代單線程收集器，Serial 收集器的老年代版本；
- Parallel Old 收集器 (標記 - 整理算法)： 老年代并行收集器，吞吐量優先，Parallel Scavenge 收集器的老年代版本；
- CMS (Concurrent Mark Sweep) 收集器（標記 - 清除算法）： 老年代并行收集器，以獲取最短回收停頓時間為目標的收集器，具有高并發、低停頓的特點，追求最短 GC 回收停頓時間。
- G1 (Garbage First) 收集器 (標記 - 整理算法)： Java 堆并行收集器，G1 收集器是 JDK1.7 提供的一個新收集器，G1 收集器基於 “標記 - 整理” 算法實現，也就是說不會産生内存碎片。此外，G1 收集器不同於之前的收集器的一個重要特點是：G1 回收的範圍是整個 Java 堆 (包括新生代，老年代)，而前六種收集器回收的範圍僅限於新生代或老年代

### [#](https://xiaolincoding.com/interview/jvm.html#%E6%A0%87%E8%AE%B0%E6%B8%85%E9%99%A4%E7%AE%97%E6%B3%95%E7%9A%84%E7%BC%BA%E7%82%B9%E6%98%AF%E4%BB%80%E4%B9%88)標記清除算法的缺點是什麽？

主要缺點有兩個：

- 一個是效率問題，標記和清除過程的效率都不高；
- 另外一個是空間問題，標記清除之後會産生大量不連續的内存碎片，空間碎片態多可能會導致，當程序在以後的運行過程中需要分配較大對象時無法找到足夠的連續内存而不得不提前觸發另一次垃圾收集動作。

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E7%AE%97%E6%B3%95%E5%93%AA%E4%BA%9B%E9%98%B6%E6%AE%B5%E4%BC%9Astop-the-world)垃圾回收算法哪些階段會 stop the world?

標記 - 複制算法應用在 CMS 新生代（ParNew 是 CMS 默認的新生代垃圾回收器）和 G1 垃圾回收器中。標記 - 複制算法可以分為三個階段：

- 標記階段，即從 GC Roots 集合開始，標記活躍對象；
- 轉移階段，即把活躍對象複制到新的内存地址上；
- 重定位階段，因為轉移導致對象的地址發生了變化，在重定位階段，所有指向對象舊地址的指針都要調整到對象新的地址上。

下面以 G1 為例，通過 G1 中標記 - 複制算法過程（G1 的 Young GC 和 Mixed GC 均采用該算法），分析 G1 停頓耗時的主要瓶頸。G1 垃圾回收周期如下圖所示：

![[IMG-小林  Java 虛拟機面試題-20250127091035554.png]]

G1 的混合回收過程可以分為標記階段、清理階段和複制階段。

**標記階段停頓分析**

- 初始標記階段：初始標記階段是指從 GC Roots 出發標記全部直接子節點的過程，該階段是 STW 的。由於 GC Roots 數量不多，通常該階段耗時非常短。
- 并發標記階段：并發標記階段是指從 GC Roots 開始對堆中對象進行可達性分析，找出存活對象。該階段是并發的，即應用線程和 GC 線程可以同時活動。并發標記耗時相對長很多，但因為不是 STW，所以我們不態關心該階段耗時的長短。
- 再標記階段：重新標記那些在并發標記階段發生變化的對象。該階段是 STW 的。

**清理階段停頓分析**

- 清理階段清點出有存活對象的分區和沒有存活對象的分區，該階段不會清理垃圾對象，也不會執行存活對象的複制。該階段是 STW 的。

**複制階段停頓分析**

- 複制算法中的轉移階段需要分配新内存和複制對象的成員變量。轉移階段是 STW 的，其中内存分配通常耗時非常短，但對象成員變量的複制耗時有可能較長，這是因為複制耗時與存活對象數量與對象複雜度成正比。對象越複雜，複制耗時越長。

四個 STW 過程中，初始標記因為隻標記 GC Roots，耗時較短。再標記因為對象數少，耗時也較短。清理階段因為内存分區數量少，耗時也較短。轉移階段要處理所有存活的對象，耗時會較長。

因此，G1 停頓時間的瓶頸主要是標記 - 複制中的轉移階段 STW。

### [#](https://xiaolincoding.com/interview/jvm.html#minorgc%E3%80%81majorgc%E3%80%81fullgc%E7%9A%84%E5%8C%BA%E5%88%AB-%E4%BB%80%E4%B9%88%E5%9C%BA%E6%99%AF%E8%A7%A6%E5%8F%91full-gc)minorGC、majorGC、fullGC 的區别，什麽場景觸發 full GC

在 Java 中，垃圾回收機制是自動管理内存的重要組成部分。根據其作用範圍和觸發條件的不同，可以將 GC 分為三種類型：Minor GC（也稱為 Young GC）、Major GC（有時也稱為 Old GC）、以及 Full GC。以下是這三種 GC 的區别和觸發場景：

> Minor GC (Young GC)

- **作用範圍**：隻針對年輕代進行回收，包括 Eden 區和兩個 Survivor 區（S0 和 S1）。
- **觸發條件**：當 Eden 區空間不足時，JVM 會觸發一次 Minor GC，將 Eden 區和一個 Survivor 區中的存活對象移動到另一個 Survivor 區或老年代（Old Generation）。
- **特點**：通常發生得非常頻繁，因為年輕代中對象的生命周期較短，回收效率高，暫停時間相對較短。

> Major GC

- **作用範圍**：主要針對老年代進行回收，但不一定隻回收老年代。
- **觸發條件**：當老年代空間不足時，或者系統檢測到年輕代對象晉升到老年代的速度過快，可能會觸發 Major GC。
- **特點**：相比 Minor GC，Major GC 發生的頻率較低，但每次回收可能需要更長的時間，因為老年代中的對象存活率較高。

> Full GC

- **作用範圍**：對整個堆内存（包括年輕代、老年代以及永久代 / 元空間）進行回收。
    
- **觸發條件**：
    
    - 直接調用 `System.gc()` 或 `Runtime.getRuntime().gc()` 方法時，雖然不能保證立即執行，但 JVM 會嘗試執行 Full GC。
        
    - Minor GC（新生代垃圾回收）時，如果存活的對象無法全部放入老年代，或者老年代空間不足以容納存活的對象，則會觸發 Full GC，對整個堆内存進行回收。
        
    - 當永久代（Java 8 之前的版本）或元空間（Java 8 及以後的版本）空間不足時。
        
- **特點**：Full GC 是最昂貴的操作，因為它需要停止所有的工作線程（Stop The World），遍曆整個堆内存來查找和回收不再使用的對象，因此應盡量減少 Full GC 的觸發。
    

### [#](https://xiaolincoding.com/interview/jvm.html#%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6%E5%99%A8-cms-%E5%92%8C-g1%E7%9A%84%E5%8C%BA%E5%88%AB)垃圾回收器 CMS 和 G1 的區别？

**區别一：使用的範圍不一樣：**

- CMS 收集器是老年代的收集器，可以配合新生代的 Serial 和 ParNew 收集器一起使用
- G1 收集器收集範圍是老年代和新生代。不需要結合其他收集器使用

**區别二：STW 的時間：**

- CMS 收集器以最小的停頓時間為目標的收集器。
- G1 收集器可預測[垃圾回收 (opens new window)](https://so.csdn.net/so/search?q=%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6&spm=1001.2101.3001.7020)的停頓時間（建立可預測的停頓時間模型）

**區别三： 垃圾碎片**

- CMS 收集器是使用 “標記 - 清除” 算法進行的垃圾回收，容易産生内存碎片
- G1 收集器使用的是 “標記 - 整理” 算法，進行了空間整合，沒有内存空間碎片。

**區别四： 垃圾回收的過程不一樣**

![[IMG-小林  Java 虛拟機面試題-20250127091035653.png]]

注意這兩個收集器第四階段得不同

**區别五: CMS 會産生浮動垃圾**

- CMS 産生浮動垃圾過多時會退化為 serial old，效率低，因為在上圖的第四階段，CMS 清除垃圾時是并發清除的，這個時候，垃圾回收線程和用戶線程同時工作會産生浮動垃圾，也就意味着 CMS 垃圾回收器必須預留一部分内存空間用於存放浮動垃圾
- 而 G1 沒有浮動垃圾，G1 的篩選回收是多個垃圾回收線程并行 gc 的，沒有浮動垃圾的回收，在執行‘并發清理’步驟時，用戶線程也會同時産生一部分可回收對象，但是這部分可回收對象隻能在下次執行清理是才會被回收。如果在清理過程中預留給用戶線程的内存不足就會出現‘Concurrent Mode Failure’, 一旦出現此錯誤時便會切換到 SerialOld 收集方式。

### [#](https://xiaolincoding.com/interview/jvm.html#%E4%BB%80%E4%B9%88%E6%83%85%E5%86%B5%E4%B8%8B%E4%BD%BF%E7%94%A8cms-%E4%BB%80%E4%B9%88%E6%83%85%E5%86%B5%E4%BD%BF%E7%94%A8g1)什麽情況下使用 CMS，什麽情況使用 G1?

CMS 适用場景：

- **低延遲需求**：适用於對停頓時間要求敏感的應用程序。
- **老生代收集**：主要針對老年代的垃圾回收。
- **碎片化管理**：容易出現内存碎片，可能需要定期進行 Full GC 來壓縮内存空間。

G1 适用場景：

- **大堆内存**：适用於需要管理大内存堆的場景，能夠有效處理數 GB 以上的堆内存。
- **對内存碎片敏感**：G1 通過緊湊整理來減少内存碎片，降低了碎片化對性能的影響。
- **比較平衡的性能**：G1 在提供較低停頓時間的同時，也保持了相對較高的吞吐量。

### [#](https://xiaolincoding.com/interview/jvm.html#g1%E5%9B%9E%E6%94%B6%E5%99%A8%E7%9A%84%E7%89%B9%E8%89%B2%E6%98%AF%E4%BB%80%E4%B9%88)G1 回收器的特色是什麽？

**G1 的特點：**

- G1 最大的特點是引入分區的思路，弱化了分代的概念。
- 合理利用垃圾收集各個周期的資源，解決了其他收集器、甚至 CMS 的衆多缺陷

**G1 相比較 CMS 的改進：**

- **算法**： G1 基於標記 -- 整理算法，不會産生空間碎片，在分配大對象時，不會因無法得到連續的空間，而提前觸發一次 FULL GC 。
- **停頓時間可控**： G1 可以通過設置預期停頓時間（Pause Time）來控制垃圾收集時間避免應用雪崩現象。
- **并行與并發**：G1 能更充分的利用 CPU 多核環境下的硬件優勢，來縮短 stop the world 的停頓時間。

### [#](https://xiaolincoding.com/interview/jvm.html#gc%E5%8F%AA%E4%BC%9A%E5%AF%B9%E5%A0%86%E8%BF%9B%E8%A1%8Cgc%E5%90%97)GC 隻會對堆進行 GC 嗎？

JVM 的垃圾回收器不僅僅會對堆進行垃圾回收，它還會對方法區進行垃圾回收。

1. **堆（Heap）：** 堆是用於存儲對象實例的内存區域。大部分的垃圾回收工作都發生在堆上，因為大多數對象都會被分配在堆上，而垃圾回收的重點通常也是回收堆中不再被引用的對象，以釋放内存空間。
2. **方法區（Method Area）：** 方法區是用於存儲類信息、常量、靜态變量等數據的區域。雖然方法區中的垃圾回收與堆有所不同，但是同樣存在對不再需要的常量、無用的類信息等進行清理的過程。