[Java并发编程面试题 | 小林coding](https://xiaolincoding.com/interview/juc.html#%E5%A4%9A%E7%BA%BF%E7%A8%8B)


## 多線程

### [#](https://xiaolincoding.com/interview/juc.html#java%E9%87%8C%E9%9D%A2%E7%9A%84%E7%BA%BF%E7%A8%8B%E5%92%8C%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E7%9A%84%E7%BA%BF%E7%A8%8B%E4%B8%80%E6%A0%B7%E5%90%97)java 裏面的線程和操作系統的線程一樣嗎？

Java 底層會調用 pthread_create 來創建線程，所以本質上 java 程序創建的線程，就是和操作系統線程是一樣的，是 1 對 1 的線程模型。

![[100_attachements/0485048708be81e1dce7e9ab1c0c0ce9_MD5.webp]]

### [#](https://xiaolincoding.com/interview/juc.html#%E4%BD%BF%E7%94%A8%E5%A4%9A%E7%BA%BF%E7%A8%8B%E8%A6%81%E6%B3%A8%E6%84%8F%E5%93%AA%E4%BA%9B%E9%97%AE%E9%A2%98)使用多線程要注意哪些問題？

要保證多線程的允許是安全，不要出現數據競争造成的數據混亂的問題。

Java 的線程安全在三個方面體現：

- **原子性**：提供互斥訪問，同一時刻隻能有一個線程對數據進行操作，在 Java 中使用了 atomic 包（這個包提供了一些支持原子操作的類，這些類可以在多線程環境下保證操作的原子性）和 synchronized 關鍵字來确保原子性；
- **可見性**：一個線程對主内存的修改可以及時地被其他線程看到，在 Java 中使用了 synchronized 和 volatile 這兩個關鍵字确保可見性；
- **有序性**：一個線程觀察其他線程中的指令執行順序，由於指令重排序，該觀察結果一般雜亂無序，在 Java 中使用了 happens-before 原則來确保有序性。

### [#](https://xiaolincoding.com/interview/juc.html#%E4%BF%9D%E8%AF%81%E6%95%B0%E6%8D%AE%E7%9A%84%E4%B8%80%E8%87%B4%E6%80%A7%E6%9C%89%E5%93%AA%E4%BA%9B%E6%96%B9%E6%A1%88%E5%91%A2)保證數據的一致性有哪些方案呢？

- **事務管理**：使用數據庫事務來确保一組數據庫操作要麽全部成功提交，要麽全部失敗回滾。通過 ACID（原子性、一致性、隔離性、持久性）屬性，數據庫事務可以保證數據的一致性。
- **鎖機制**：使用鎖來實現對共享資源的互斥訪問。在 Java 中，可以使用 synchronized 關鍵字、ReentrantLock 或其他鎖機制來控制并發訪問，從而避免并發操作導致數據不一致。
- **版本控制**：通過樂觀鎖的方式，在更新數據時記錄數據的版本信息，從而避免同時對同一數據進行修改，進而保證數據的一致性。

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E7%9A%84%E5%88%9B%E5%BB%BA%E6%96%B9%E5%BC%8F%E6%9C%89%E5%93%AA%E4%BA%9B)線程的創建方式有哪些？

> 1. 繼承 Thread 類

這是最直接的一種方式，用戶自定義類繼承 java.lang.Thread 類，重寫其 run () 方法，run () 方法中定義了線程執行的具體任務。創建該類的實例後，通過調用 start () 方法啓動線程。

```
class MyThread extends Thread {
    @Override
    public void run() {
        // 線程執行的代碼
    }
}

public static void main(String[] args) {
    MyThread t = new MyThread();
    t.start();
}
```

采用繼承 Thread 類方式

- 優點：編寫簡單，如果需要訪問當前線程，無需使用 Thread.currentThread () 方法，直接使用 this，即可獲得當前線程
- 缺點：因為線程類已經繼承了 Thread 類，所以不能再繼承其他的父類

> 2. 實現 Runnable 接口

如果一個類已經繼承了其他類，就不能再繼承 Thread 類，此時可以實現 java.lang.Runnable 接口。實現 Runnable 接口需要重寫 run () 方法，然後將此 Runnable 對象作為參數傳遞給 Thread 類的構造器，創建 Thread 對象後調用其 start () 方法啓動線程。

```
class MyRunnable implements Runnable {
    @Override
    public void run() {
        // 線程執行的代碼
    }
}

public static void main(String[] args) {
    Thread t = new Thread(new MyRunnable());
    t.start();
}
```

采用實現 Runnable 接口方式：

- 優點：線程類隻是實現了 Runable 接口，還可以繼承其他的類。在這種方式下，可以多個線程共享同一個目標對象，所以非常适合多個相同線程來處理同一份資源的情況，從而可以將 CPU 代碼和數據分開，形成清晰的模型，較好地體現了面向對象的思想。
- 缺點：編程稍微複雜，如果需要訪問當前線程，必須使用 Thread.currentThread () 方法。

> 3. 實現 Callable 接口與 FutureTask

java.util.concurrent.Callable 接口類似於 Runnable，但 Callable 的 call () 方法可以有返回值并且可以抛出異常。要執行 Callable 任務，需將它包裝進一個 FutureTask，因為 Thread 類的構造器隻接受 Runnable 參數，而 FutureTask 實現了 Runnable 接口。

```
class MyCallable implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        // 線程執行的代碼，這裏返回一個整型結果
        return 1;
    }
}

public static void main(String[] args) {
    MyCallable task = new MyCallable();
    FutureTask<Integer> futureTask = new FutureTask<>(task);
    Thread t = new Thread(futureTask);
    t.start();

    try {
        Integer result = futureTask.get();  // 獲取線程執行結果
        System.out.println("Result: " + result);
    } catch (InterruptedException | ExecutionException e) {
        e.printStackTrace();
    }
}
```

采用實現 Callable 接口方式：

- 缺點：編程稍微複雜，如果需要訪問當前線程，必須調用 Thread.currentThread () 方法。
- 優點：線程隻是實現 Runnable 或實現 Callable 接口，還可以繼承其他類。這種方式下，多個線程可以共享一個 target 對象，非常适合多線程處理同一份資源的情形。

> 4. 使用線程池（Executor 框架）

從 Java 5 開始引入的 java.util.concurrent.ExecutorService 和相關類提供了線程池的支持，這是一種更高效的線程管理方式，避免了頻繁創建和銷毀線程的開銷。可以通過 Executors 類的靜态方法創建不同類型的線程池。

```
class Task implements Runnable {
    @Override
    public void run() {
        // 線程執行的代碼
    }
}

public static void main(String[] args) {
    ExecutorService executor = Executors.newFixedThreadPool(10);  // 創建固定大小的線程池
    for (int i = 0; i < 100; i++) {
        executor.submit(new Task());  // 提交任務到線程池執行
    }
    executor.shutdown();  // 關閉線程池
}
```

采用線程池方式：

- 缺點：程池增加了程序的複雜度，特别是當涉及線程池參數調整和故障排查時。錯誤的配置可能導致死鎖、資源耗盡等問題，這些問題的診斷和修複可能較為複雜。
- 優點：線程池可以重用預先創建的線程，避免了線程創建和銷毀的開銷，顯著提高了程序的性能。對於需要快速響應的并發請求，線程池可以迅速提供線程來處理任務，減少等待時間。并且，線程池能夠有效控制運行的線程數量，防止因創建過多線程導致的系統資源耗盡（如内存溢出）。通過合理配置線程池大小，可以最大化 CPU 利用率和系統吞吐量。

### [#](https://xiaolincoding.com/interview/juc.html#%E6%80%8E%E4%B9%88%E5%90%AF%E5%8A%A8%E7%BA%BF%E7%A8%8B)怎麽啓動線程 ？

啓動線程的通過 Thread 類的 **start()**。

```
//創建兩個線程，用start啓動線程
MyThread myThread1 = new MyThread();  
MyThread myThread2 = new MyThread();  
myThread1.start();  
myThread2.start();  
```

### [#](https://xiaolincoding.com/interview/juc.html#%E5%A6%82%E4%BD%95%E5%81%9C%E6%AD%A2%E4%B8%80%E4%B8%AA%E7%BA%BF%E7%A8%8B%E7%9A%84%E8%BF%90%E8%A1%8C)如何停止一個線程的運行？

主要有這些方法：

- **異常法停止**：線程調用 interrupt () 方法後，在線程的 run 方法中判斷當前對象的 interrupted () 狀态，如果是中斷狀态則抛出異常，達到中斷線程的效果。
- **在沉睡中停止**：先將線程 sleep，然後調用 interrupt 標記中斷狀态，interrupt 會將阻塞狀态的線程中斷。會抛出中斷異常，達到停止線程的效果
- **stop () 暴力停止**：線程調用 stop () 方法會被暴力停止，方法已棄用，該方法會有不好的後果：強制讓線程停止有可能使一些請理性的工作得不到完成。
- **使用 return 停止線程**：調用 interrupt 標記為中斷狀态後，在 run 方法中判斷當前線程狀态，如果為中斷狀态則 return，能達到停止線程的效果。

### [#](https://xiaolincoding.com/interview/juc.html#%E8%B0%83%E7%94%A8-interrupt-%E6%98%AF%E5%A6%82%E4%BD%95%E8%AE%A9%E7%BA%BF%E7%A8%8B%E6%8A%9B%E5%87%BA%E5%BC%82%E5%B8%B8%E7%9A%84)調用 interrupt 是如何讓線程抛出異常的？

每個線程都一個與之關聯的布爾屬性來表示其中斷狀态，中斷狀态的初始值為 false，當一個線程被其它線程調用 `Thread.interrupt()` 方法中斷時，會根據實際情況做出響應。

- 如果該線程正在執行低級别的可中斷方法（如 `Thread.sleep()`、`Thread.join()` 或 `Object.wait()`），則會解除阻塞并**抛出 `InterruptedException` 異常**。
- 否則 `Thread.interrupt()` 僅設置線程的中斷狀态，在該被中斷的線程中稍後可通過輪詢中斷狀态來決定是否要停止當前正在執行的任務。

### [#](https://xiaolincoding.com/interview/juc.html#java%E7%BA%BF%E7%A8%8B%E7%9A%84%E7%8A%B6%E6%80%81%E6%9C%89%E5%93%AA%E4%BA%9B)Java 線程的狀态有哪些？
![[Pasted image 20250116154808.png]]

源自《Java 并發編程藝術》 java.lang.Thread.State 枚舉類中定義了六種線程的狀态，可以調用線程 Thread 中的 getState () 方法**獲取當前線程的狀态**。

|線程狀态|解釋|
|---|---|
|NEW|尚未啓動的線程狀态，即線程創建，**還未調用 start 方法**|
|RUNNABLE|**就緒狀态**（調用 start，等待調度）+ **正在運行**|
|BLOCKED|**等待監視器鎖**時，陷入阻塞狀态|
|WAITING|等待狀态的線程正在**等待**另一線程執行特定的操作（如 notify）|
|TIMED_WAITING|具有**指定等待時間**的等待狀态|
|TERMINATED|線程完成執行，**終止狀态**|

### [#](https://xiaolincoding.com/interview/juc.html#blocked%E5%92%8Cwaiting%E6%9C%89%E5%95%A5%E5%8C%BA%E5%88%AB)blocked 和 waiting 有啥區别

- **觸發條件**: 線程進入 BLOCKED 狀态通常是因為試圖獲取一個對象的鎖（monitor lock），但該鎖已經被另一個線程持有。這通常發生在嘗試進入 synchronized 塊或方法時，如果鎖已被占用，則線程將被阻塞直到鎖可用。線程進入 WAITING 狀态是因為它正在等待另一個線程執行某些操作，例如調用 Object.wait () 方法、Thread.join () 方法或 LockSupport.park () 方法。在這種狀态下，線程將不會消耗 CPU 資源，并且不會參與鎖的競争。
- **喚醒機制**: 當一個線程被阻塞等待鎖時，一旦鎖被釋放，線程將有機會重新嘗試獲取鎖。如果鎖此時未被其他線程獲取，那麽線程可以從 BLOCKED 狀态變為 RUNNABLE 狀态。線程在 WAITING 狀态中需要被顯式喚醒。例如，如果線程調用了 Object.wait ()，那麽它必須等待另一個線程調用同一對象上的 Object.notify () 或 Object.notifyAll () 方法才能被喚醒。

### [#](https://xiaolincoding.com/interview/juc.html#wait-%E7%8A%B6%E6%80%81%E4%B8%8B%E7%9A%84%E7%BA%BF%E7%A8%8B%E5%A6%82%E4%BD%95%E8%BF%9B%E8%A1%8C%E6%81%A2%E5%A4%8D%E5%88%B0-running-%E7%8A%B6%E6%80%81)wait 狀态下的線程如何進行恢複到 running 狀态？

- 等待的線程**被其他線程對象喚醒**，`notify()` 和 `notifyAll()`。
- 如果線程沒有獲取到鎖則會直接進入 Waiting 狀态，其實這種本質上它就是執行了 LockSupport.park () 方法進入了 Waiting 狀态，那麽解鎖的時候會執行 `LockSupport.unpark(Thread)`，與上面 park 方法對應，給出許可證，**解除等待狀态**。

### [#](https://xiaolincoding.com/interview/juc.html#notify-%E5%92%8C-notifyall-%E7%9A%84%E5%8C%BA%E5%88%AB)notify 和 notifyAll 的區别？

同樣是喚醒等待的線程，同樣最多隻有一個線程能獲得鎖，同樣不能控制哪個線程獲得鎖。

區别在於：

- notify：喚醒一個線程，其他線程依然處於 wait 的等待喚醒狀态，如果被喚醒的線程結束時沒調用 notify，其他線程就永遠沒人去喚醒，隻能等待超時，或者被中斷
- notifyAll：所有線程退出 wait 的狀态，開始競争鎖，但隻有一個線程能搶到，這個線程執行完後，其他線程又會有一個幸運兒脫穎而出得到鎖

### [#](https://xiaolincoding.com/interview/juc.html#notify-%E9%80%89%E6%8B%A9%E5%93%AA%E4%B8%AA%E7%BA%BF%E7%A8%8B)notify 選擇哪個線程？

notify 在源碼的注釋中說到 notify 選擇喚醒的線程是任意的，但是依賴於具體實現的 jvm。

![[100_attachements/ac33f3610a91a1c332361691e0fafe2b_MD5.png]]

JVM 有很多實現，比較流行的就是 hotspot，hotspot 對 notofy () 的實現并不是我們以為的随機喚醒，，而是 “先進先出” 的順序喚醒。

## [#](https://xiaolincoding.com/interview/juc.html#%E5%B9%B6%E5%8F%91%E5%AE%89%E5%85%A8)并發安全

### [#](https://xiaolincoding.com/interview/juc.html#%E6%80%8E%E4%B9%88%E4%BF%9D%E8%AF%81%E5%A4%9A%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8)怎麽保證多線程安全？

- **synchronized 關鍵字**: 可以使用 `synchronized` 關鍵字來同步代碼塊或方法，确保同一時刻隻有一個線程可以訪問這些代碼。對象鎖是通過 `synchronized` 關鍵字鎖定對象的監視器（monitor）來實現的。

```
public synchronized void someMethod() { /* ... */ }

public void anotherMethod() {
    synchronized (someObject) {
        /* ... */
    }
}
```

- **volatile 關鍵字**:`volatile` 關鍵字用於變量，确保所有線程看到的是該變量的最新值，而不是可能存儲在本地寄存器中的副本。

```
public volatile int sharedVariable;
```

- **Lock 接口和 ReentrantLock 類**:`java.util.concurrent.locks.Lock` 接口提供了比 `synchronized` 更強大的鎖定機制，`ReentrantLock` 是一個實現該接口的例子，提供了更靈活的鎖管理和更高的性能。

```
private final ReentrantLock lock = new ReentrantLock();

public void someMethod() {
    lock.lock();
    try {
        /* ... */
    } finally {
        lock.unlock();
    }
}
```

- **原子類**：Java 并發庫（`java.util.concurrent.atomic`）提供了原子類，如 `AtomicInteger`、`AtomicLong` 等，這些類提供了原子操作，可以用於更新基本類型的變量而無需額外的同步。

示例：

```
AtomicInteger counter = new AtomicInteger(0);

int newValue = counter.incrementAndGet();
```

- **線程局部變量**:`ThreadLocal` 類可以為每個線程提供獨立的變量副本，這樣每個線程都擁有自己的變量，消除了競争條件。

```
ThreadLocal<Integer> threadLocalVar = new ThreadLocal<>();

threadLocalVar.set(10);
int value = threadLocalVar.get();
```

- **并發集合**: 使用 `java.util.concurrent` 包中的線程安全集合，如 `ConcurrentHashMap`、`ConcurrentLinkedQueue` 等，這些集合内部已經實現了線程安全的邏輯。
- **JUC 工具類**: 使用 `java.util.concurrent` 包中的一些工具類可以用於控制線程間的同步和協作。例如：`Semaphore` 和 `CyclicBarrier` 等。

### [#](https://xiaolincoding.com/interview/juc.html#java%E4%B8%AD%E6%9C%89%E5%93%AA%E4%BA%9B%E5%B8%B8%E7%94%A8%E7%9A%84%E9%94%81-%E5%9C%A8%E4%BB%80%E4%B9%88%E5%9C%BA%E6%99%AF%E4%B8%8B%E4%BD%BF%E7%94%A8)Java 中有哪些常用的鎖，在什麽場景下使用？

ava 中的鎖是用於管理多線程并發訪問共享資源的關鍵機制。鎖可以确保在任意給定時間内隻有一個線程可以訪問特定的資源，從而避免數據競争和不一致性。Java 提供了多種鎖機制，可以分為以下幾類：

- **内置鎖（synchronized）**：Java 中的 `synchronized` 關鍵字是内置鎖機制的基礎，可以用於方法或代碼塊。當一個線程進入 `synchronized` 代碼塊或方法時，它會獲取關聯對象的鎖；當線程離開該代碼塊或方法時，鎖會被釋放。如果其他線程嘗試獲取同一個對象的鎖，它們將被阻塞，直到鎖被釋放。其中，syncronized 加鎖時有無鎖、偏向鎖、輕量級鎖和重量級鎖幾個級别。偏向鎖用於當一個線程進入同步塊時，如果沒有任何其他線程競争，就會使用偏向鎖，以減少鎖的開銷。輕量級鎖使用線程棧上的數據結構，避免了操作系統級别的鎖。重量級鎖則涉及操作系統級的互斥鎖。
- **ReentrantLock**：`java.util.concurrent.locks.ReentrantLock` 是一個顯式的鎖類，提供了比 `synchronized` 更高級的功能，如可中斷的鎖等待、定時鎖等待、公平鎖選項等。`ReentrantLock` 使用 `lock()` 和 `unlock()` 方法來獲取和釋放鎖。其中，公平鎖按照線程請求鎖的順序來分配鎖，保證了鎖分配的公平性，但可能增加鎖的等待時間。非公平鎖不保證鎖分配的順序，可以減少鎖的競争，提高性能，但可能造成某些線程的饑餓。
- **讀寫鎖（ReadWriteLock）**：`java.util.concurrent.locks.ReadWriteLock` 接口定義了一種鎖，允許多個讀取者同時訪問共享資源，但隻允許一個寫入者。讀寫鎖通常用於讀取遠多於寫入的情況，以提高并發性。
- **樂觀鎖和悲觀鎖**：悲觀鎖（Pessimistic Locking）通常指在訪問數據前就鎖定資源，假設最壞的情況，即數據很可能被其他線程修改。`synchronized` 和 `ReentrantLock` 都是悲觀鎖的例子。樂觀鎖（Optimistic Locking）通常不鎖定資源，而是在更新數據時檢查數據是否已被其他線程修改。樂觀鎖常使用版本號或時間戳來實現。
- **自旋鎖**：自旋鎖是一種鎖機制，線程在等待鎖時會持續循環檢查鎖是否可用，而不是放棄 CPU 并阻塞。通常可以使用 CAS 來實現。這在鎖等待時間很短的情況下可以提高性能，但過度自旋會浪費 CPU 資源。

### [#](https://xiaolincoding.com/interview/juc.html#%E6%80%8E%E4%B9%88%E5%9C%A8%E5%AE%9E%E8%B7%B5%E4%B8%AD%E7%94%A8%E9%94%81%E7%9A%84)怎麽在實踐中用鎖的？

Java 提供了多種鎖的實現，包括 `synchronized` 關鍵字、`java.util.concurrent.locks` 包下的 `Lock` 接口及其具體實現如 `ReentrantLock`、`ReadWriteLock` 等。下面我們來看看這些鎖的使用方式。

> 1. `synchronized`

`synchronized` 關鍵字可以用於方法或代碼塊，它是 Java 中最早的鎖實現，使用起來非常簡單。

**示例：synchronized 方法**

```
public class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}
```

**示例：synchronized 代碼塊**

```
public class Counter {
    private Object lock = new Object();
    private int count = 0;

    public void increment() {
        synchronized (lock) {
            count++;
        }
    }
}
```

> 2. 使用 `Lock` 接口

`Lock` 接口提供了比 `synchronized` 更靈活的鎖操作，包括嘗試鎖、可中斷鎖、定時鎖等。`ReentrantLock` 是 `Lock` 接口的一個實現。

**示例：使用 ReentrantLock**

```
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class Counter {
    private Lock lock = new ReentrantLock();
    private int count = 0;

    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
}
```

> 3. 使用 `ReadWriteLock`

`ReadWriteLock` 接口提供了一種讀寫鎖的實現，允許多個讀操作同時進行，但寫操作是獨占的。

**示例：使用 ReadWriteLock**

```
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class Cache {
    private ReadWriteLock lock = new ReentrantReadWriteLock();
    private Lock readLock = lock.readLock();
    private Lock writeLock = lock.writeLock();
    private Object data;

    public Object readData() {
        readLock.lock();
        try {
            return data;
        } finally {
            readLock.unlock();
        }
    }

    public void writeData(Object newData) {
        writeLock.lock();
        try {
            data = newData;
        } finally {
            writeLock.unlock();
        }
    }
}
```

### [#](https://xiaolincoding.com/interview/juc.html#synchronized%E5%92%8Creentrantlock%E5%8F%8A%E5%85%B6%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF)synchronized 和 reentrantlock 及其應用場景？

> synchronized 工作原理

synchronized 是 Java 提供的原子性内置鎖，這種内置的并且使用者看不到的鎖也被稱為**監視器鎖**，

使用 synchronized 之後，會在編譯之後在同步的代碼塊前後加上 monitorenter 和 monitorexit 字節碼指令，他依賴操作系統底層互斥鎖實現。他的作用主要就是實現原子性操作和解決共享變量的内存可見性問題。

執行 monitorenter 指令時會嘗試獲取對象鎖，如果對象沒有被鎖定或者已經獲得了鎖，鎖的計數器 + 1。此時其他競争鎖的線程則會進入等待隊列中。執行 monitorexit 指令時則會把計數器 - 1，當計數器值為 0 時，則鎖釋放，處於等待隊列中的線程再繼續競争鎖。

synchronized 是排它鎖，當一個線程獲得鎖之後，其他線程必須等待該線程釋放鎖後才能獲得鎖，而且由於 Java 中的線程和操作系統原生線程是一一對應的，線程被阻塞或者喚醒時時會從用戶态切換到内核态，這種轉換非常消耗性能。

從内存語義來說，加鎖的過程會清除工作内存中的共享變量，再從主内存讀取，而釋放鎖的過程則是將工作内存中的共享變量寫回主内存。

實際上大部分時候我認為說到 monitorenter 就行了，但是為了更清楚的描述，還是再具體一點。

如果再深入到源碼來說，synchronized 實際上有兩個隊列 waitSet 和 entryList。

1. 當多個線程進入同步代碼塊時，首先進入 entryList
2. 有一個線程獲取到 monitor 鎖後，就賦值給當前線程，并且計數器 + 1
3. 如果線程調用 wait 方法，將釋放鎖，當前線程置為 null，計數器 - 1，同時進入 waitSet 等待被喚醒，調用 notify 或者 notifyAll 之後又會進入 entryList 競争鎖
4. 如果線程執行完畢，同樣釋放鎖，計數器 - 1，當前線程置為 null!


![[Pasted image 20250116154832.png|896]]



> reentrantlock 工作原理

ReentrantLock 的底層實現主要依賴於 AbstractQueuedSynchronizer（AQS）這個抽象類。AQS 是一個提供了基本同步機制的框架，其中包括了隊列、狀态值等。

ReentrantLock 在 AQS 的基礎上通過内部類 Sync 來實現具體的鎖操作。不同的 Sync 子類實現了公平鎖和非公平鎖的不同邏輯：

- **可中斷性**： ReentrantLock 實現了可中斷性，這意味着線程在等待鎖的過程中，可以被其他線程中斷而提前結束等待。在底層，ReentrantLock 使用了與 LockSupport.park () 和 LockSupport.unpark () 相關的機制來實現可中斷性。
- **設置超時時間**： ReentrantLock 支持在嘗試獲取鎖時設置超時時間，即等待一定時間後如果還未獲得鎖，則放棄鎖的獲取。這是通過内部的 tryAcquireNanos 方法來實現的。
- **公平鎖和非公平鎖**： 在直接創建 ReentrantLock 對象時，默認情況下是非公平鎖。公平鎖是按照線程等待的順序來獲取鎖，而非公平鎖則允許多個線程在同一時刻競争鎖，不考慮它們申請鎖的順序。公平鎖可以通過在創建 ReentrantLock 時傳入 true 來設置，例如：

```
ReentrantLock fairLock = new ReentrantLock(true);
```

- **多個條件變量**： ReentrantLock 支持多個條件變量，每個條件變量可以與一個 ReentrantLock 關聯。這使得線程可以更靈活地進行等待和喚醒操作，而不僅僅是基於對象監視器的 wait () 和 notify ()。多個條件變量的實現依賴於 Condition 接口，例如：

```
ReentrantLock lock = new ReentrantLock();
Condition condition = lock.newCondition();
// 使用下面方法進行等待和喚醒
condition.await();
condition.signal();
```

- **可重入性**： ReentrantLock 支持可重入性，即同一個線程可以多次獲得同一把鎖，而不會造成死鎖。這是通過内部的 holdCount 計數來實現的。當一個線程多次獲取鎖時，holdCount 遞增，釋放鎖時遞減，隻有當 holdCount 為零時，其他線程才有機會獲取鎖。

> 應用場景的區别

**synchronized**：

- **簡單同步需求**： 當你需要對代碼塊或方法進行簡單的同步控制時，`synchronized` 是一個很好的選擇。它使用起來簡單，不需要額外的資源管理，因為鎖會在方法退出或代碼塊執行完畢後自動釋放。
- **代碼塊同步**： 如果你想對特定代碼段進行同步，而不是整個方法，可以使用 `synchronized` 代碼塊。這可以讓你更精細地控制同步的範圍，從而減少鎖的持有時間，提高并發性能。
- **内置鎖的使用**： `synchronized` 關鍵字使用對象的内置鎖（也稱為監視器鎖），這在需要使用對象作為鎖對象的情況下很有用，尤其是在對象狀态與鎖保護的代碼緊密相關時。

**ReentrantLock：**

- **高級鎖功能需求**： `ReentrantLock` 提供了 `synchronized` 所不具備的高級功能，如公平鎖、響應中斷、定時鎖嘗試、以及多個條件變量。當你需要這些功能時，`ReentrantLock` 是更好的選擇。
- **性能優化**： 在高度競争的環境中，`ReentrantLock` 可以提供比 `synchronized` 更好的性能，因為它提供了更細粒度的控制，如嘗試鎖定和定時鎖定，可以減少線程阻塞的可能性。
- **複雜同步結構**： 當你需要更複雜的同步結構，如需要多個條件變量來協調線程之間的通信時，`ReentrantLock` 及其配套的 `Condition` 對象可以提供更靈活的解決方案。

綜上，`synchronized` 适用於簡單同步需求和不需要額外鎖功能的場景，而 `ReentrantLock` 适用於需要更高級鎖功能、性能優化或複雜同步邏輯的情況。選擇哪種同步機制取決於具體的應用需求和性能考慮。

### [#](https://xiaolincoding.com/interview/juc.html#synchronized%E5%92%8Creentrantlock%E5%8C%BA%E5%88%AB)synchronized 和 reentrantlock 區别？

synchronized 和 ReentrantLock 都是 Java 中提供的可重入鎖：

- **用法不同**：synchronized 可用來修飾普通方法、靜态方法和代碼塊，而 ReentrantLock 隻能用在代碼塊上。
- **獲取鎖和釋放鎖方式不同**：synchronized 會自動加鎖和釋放鎖，當進入 synchronized 修飾的代碼塊之後會自動加鎖，當離開 synchronized 的代碼段之後會自動釋放鎖。而 ReentrantLock 需要手動加鎖和釋放鎖
- **鎖類型不同**：synchronized 屬於非公平鎖，而 ReentrantLock 既可以是公平鎖也可以是非公平鎖。
- **響應中斷不同**：ReentrantLock 可以響應中斷，解決死鎖的問題，而 synchronized 不能響應中斷。
- **底層實現不同**：synchronized 是 JVM 層面通過監視器實現的，而 ReentrantLock 是基於 AQS 實現的。

### [#](https://xiaolincoding.com/interview/juc.html#%E6%80%8E%E4%B9%88%E7%90%86%E8%A7%A3%E5%8F%AF%E9%87%8D%E5%85%A5%E9%94%81)怎麽理解可重入鎖？

可重入鎖是指同一個線程在獲取了鎖之後，可以再次重複獲取該鎖而不會造成死鎖或其他問題。當一個線程持有鎖時，如果再次嘗試獲取該鎖，就會成功獲取而不會被阻塞。

ReentrantLock 實現可重入鎖的機制是基於線程持有鎖的計數器。

- 當一個線程第一次獲取鎖時，計數器會加 1，表示該線程持有了鎖。在此之後，如果同一個線程再次獲取鎖，計數器會再次加 1。每次線程成功獲取鎖時，都會將計數器加 1。
- 當線程釋放鎖時，計數器會相應地減 1。隻有當計數器減到 0 時，鎖才會完全釋放，其他線程才有機會獲取鎖。

這種計數器的設計使得同一個線程可以多次獲取同一個鎖，而不會造成死鎖或其他問題。每次獲取鎖時，計數器加 1；每次釋放鎖時，計數器減 1。隻有當計數器減到 0 時，鎖才會完全釋放。

ReentrantLock 通過這種計數器的方式，實現了可重入鎖的機制。它允許同一個線程多次獲取同一個鎖，并且能夠正确地處理鎖的獲取和釋放，避免了死鎖和其他并發問題。

### [#](https://xiaolincoding.com/interview/juc.html#synchronized-%E6%94%AF%E6%8C%81%E9%87%8D%E5%85%A5%E5%90%97-%E5%A6%82%E4%BD%95%E5%AE%9E%E7%8E%B0%E7%9A%84)synchronized 支持重入嗎？如何實現的？

synchronized 是基於原子性的内部鎖機制，是可重入的，因此在一個線程調用 synchronized 方法的同時在其方法體内部調用該對象另一個 synchronized 方法，也就是說一個線程得到一個對象鎖後再次請求該對象鎖，是允許的，這就是 synchronized 的可重入性。

synchronized 底層是利用計算機系統 mutex Lock 實現的。每一個可重入鎖都會關聯一個線程 ID 和一個鎖狀态 status。

當一個線程請求方法時，會去檢查鎖狀态。

1. 如果鎖狀态是 0，代表該鎖沒有被占用，使用 CAS 操作獲取鎖，將線程 ID 替換成自己的線程 ID。
2. 如果鎖狀态不是 0，代表有線程在訪問該方法。此時，如果線程 ID 是自己的線程 ID，如果是可重入鎖，會將 status 自增 1，然後獲取到該鎖，進而執行相應的方法；如果是非重入鎖，就會進入阻塞隊列等待。

在釋放鎖時，

1. 如果是可重入鎖的，每一次退出方法，就會將 status 減 1，直至 status 的值為 0，最後釋放該鎖。
2. 如果非可重入鎖的，線程退出方法，直接就會釋放該鎖。

### [#](https://xiaolincoding.com/interview/juc.html#syncronized%E9%94%81%E5%8D%87%E7%BA%A7%E7%9A%84%E8%BF%87%E7%A8%8B%E8%AE%B2%E4%B8%80%E4%B8%8B)syncronized 鎖升級的過程講一下

具體的鎖升級的過程是：**無鎖 -> 偏向鎖 -> 輕量級鎖 -> 重量級鎖**。

- **無鎖**：這是沒有開啓偏向鎖的時候的狀态，在 JDK1.6 之後偏向鎖的默認開啓的，但是有一個偏向延遲，需要在 JVM 啓動之後的多少秒之後才能開啓，這個可以通過 JVM 參數進行設置，同時是否開啓偏向鎖也可以通過 JVM 參數設置。
- **偏向鎖**：這個是在偏向鎖開啓之後的鎖的狀态，如果還沒有一個線程拿到這個鎖的話，這個狀态叫做匿名偏向，當一個線程拿到偏向鎖的時候，下次想要競争鎖隻需要拿線程 ID 跟 MarkWord 當中存儲的線程 ID 進行比較，如果線程 ID 相同則直接獲取鎖（相當於鎖偏向於這個線程），不需要進行 CAS 操作和將線程挂起的操作。
- **輕量級鎖**：在這個狀态下線程主要是通過 CAS 操作實現的。將對象的 MarkWord 存儲到線程的虛拟機棧上，然後通過 CAS 將對象的 MarkWord 的内容設置為指向 Displaced Mark Word 的指針，如果設置成功則獲取鎖。在線程出臨界區的時候，也需要使用 CAS，如果使用 CAS 替換成功則同步成功，如果失敗表示有其他線程在獲取鎖，那麽就需要在釋放鎖之後將被挂起的線程喚醒。
- **重量級鎖**：當有兩個以上的線程獲取鎖的時候輕量級鎖就會升級為重量級鎖，因為 CAS 如果沒有成功的話始終都在自旋，進行 while 循環操作，這是非常消耗 CPU 的，但是在升級為重量級鎖之後，線程會被操作系統調度然後挂起，這可以節約 CPU 資源。

了解完 4 種鎖狀态之後，我們就可以整體的來看一下鎖升級的過程了。

![[Pasted image 20250116154854.png]]

但如果 Mark Word 裏的線程不是線程 A，就會通過自旋嘗試獲取鎖，如果獲取到了，就將 Mark Word 中的線程 ID 改為自己的；如果競争失敗，就會立馬撤銷偏向鎖，膨脹為輕量級鎖。

後續的競争線程都會通過自旋來嘗試獲取鎖，如果自旋成功那麽鎖的狀态仍然是輕量級鎖。然而如果競争失敗，鎖會膨脹為重量級鎖，後續等待的競争的線程都會被阻塞。

### [#](https://xiaolincoding.com/interview/juc.html#jvm%E5%AF%B9synchornized%E7%9A%84%E4%BC%98%E5%8C%96)JVM 對 Synchornized 的優化？

synchronized 核心優化方案主要包含以下 4 個：

- **鎖膨脹**：synchronized 從無鎖升級到偏向鎖，再到輕量級鎖，最後到重量級鎖的過程，它叫做鎖膨脹也叫做鎖升級。JDK 1.6 之前，synchronized 是重量級鎖，也就是說 synchronized 在釋放和獲取鎖時都會從用戶态轉換成内核态，而轉換的效率是比較低的。但有了鎖膨脹機制之後，synchronized 的狀态就多了無鎖、偏向鎖以及輕量級鎖了，這時候在進行并發操作時，大部分的場景都不需要用戶态到内核态的轉換了，這樣就大幅的提升了 synchronized 的性能。
- **鎖消除**：指的是在某些情況下，JVM 虛拟機如果檢測不到某段代碼被共享和競争的可能性，就會將這段代碼所屬的同步鎖消除掉，從而到底提高程序性能的目的。
- **鎖粗化**：將多個連續的加鎖、解鎖操作連接在一起，擴展成一個範圍更大的鎖。
- **自适應自旋鎖**：指通過自身循環，嘗試獲取鎖的一種方式，優點在於它避免一些線程的挂起和恢複操作，因為挂起線程和恢複線程都需要從用戶态轉入内核态，這個過程是比較慢的，所以通過自旋的方式可以一定程度上避免線程挂起和恢複所造成的性能開銷。

### [#](https://xiaolincoding.com/interview/juc.html#%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8Baqs)介紹一下 AQS

AQS 全稱為 AbstractQueuedSynchronizer，是 Java 中的一個抽象類。 AQS 是一個用於構建鎖、同步器、協作工具類的工具類（框架）。

AQS 核心思想是，如果被請求的共享資源空閑，那麽就將當前請求資源的線程設置為有效的工作線程，將共享資源設置為鎖定狀态；如果共享資源被占用，就需要一定的阻塞等待喚醒機制來保證鎖分配。這個機制主要用的是 CLH 隊列的變體實現的，將暫時獲取不到鎖的線程加入到隊列中。

CLH：Craig、Landin and Hagersten 隊列，是單向鏈表，AQS 中的隊列是 CLH 變體的虛拟雙向隊列（FIFO），AQS 是通過將每條請求共享資源的線程封裝成一個節點來實現鎖的分配。

主要原理圖如下： 

AQS 使用一個 Volatile 的 int 類型的成員變量來表示同步狀态，通過内置的 FIFO 隊列來完成資源獲取的排隊工作，通過 CAS 完成對 State 值的修改。

AQS 廣泛用於控制并發流程的類，如下圖：
![[Pasted image 20250116154920.png|656]]
![[Pasted image 20250116154928.png|755]]

其中 `Sync` 是這些類中都有的内部類，其結構如下：

![[100_attachements/16d1f445eb70e630edfc32eafdae8042_MD5.png]]

可以看到：`Sync` 是 `AQS` 的實現。 `AQS` 主要完成的任務：

- 同步狀态（比如說計數器）的原子性管理；
- 線程的阻塞和解除阻塞；
- 隊列的管理。

> AQS 原理

AQS 最核心的就是三大部分：

- 狀态：state；
- 控制線程搶鎖和配合的 FIFO 隊列（雙向鏈表）；
- 期望協作工具類去實現的獲取 / 釋放等重要方法（重寫）。

**狀态 state**

- 這裏 state 的具體含義，會根據具體實現類的不同而不同：比如在 Semapore 裏，他表示剩餘許可證的數量；在 CountDownLatch 裏，它表示還需要倒數的數量；在 ReentrantLock 中，state 用來表示 “鎖” 的占有情況，包括可重入計數，當 state 的值為 0 的時候，標識該 Lock 不被任何線程所占有。
- state 是 volatile 修飾的，并被并發修改，所以修改 state 的方法都需要保證線程安全，比如 getState、setState 以及 compareAndSetState 操作來讀取和更新這個狀态。這些方法都依賴於 unsafe 類。

**FIFO 隊列**

- 這個隊列用來存放 “等待的線程，AQS 就是 “排隊管理器”，當多個線程争用同一把鎖時，必須有排隊機制將那些沒能拿到鎖的線程串在一起。當鎖釋放時，鎖管理器就會挑選一個合适的線程來占有這個剛剛釋放的鎖。
- AQS 會維護一個等待的線程隊列，把線程都放到這個隊列裏，這個隊列是雙向鏈表形式。

**實現獲取 / 釋放等方法**

- 這裏的獲取和釋放方法，是利用 AQS 的協作工具類裏最重要的方法，是由協作類自己去實現的，并且含義各不相同；
- 獲取方法：獲取操作會以來 state 變量，經常會阻塞（比如獲取不到鎖的時候）。在 Semaphore 中，獲取就是 acquire 方法，作用是獲取一個許可證； 而在 CountDownLatch 裏面，獲取就是 await 方法，作用是等待，直到倒數結束；
- 釋放方法：在 Semaphore 中，釋放就是 release 方法，作用是釋放一個許可證； 在 CountDownLatch 裏面，獲取就是 countDown 方法，作用是將倒數的數減一；
- 需要每個實現類重寫 tryAcquire 和 tryRelease 等方法。

### [#](https://xiaolincoding.com/interview/juc.html#threadlocal%E4%BD%9C%E7%94%A8-%E5%8E%9F%E7%90%86-%E5%85%B7%E4%BD%93%E9%87%8C%E9%9D%A2%E5%AD%98%E7%9A%84key-value%E6%98%AF%E5%95%A5-%E4%BC%9A%E6%9C%89%E4%BB%80%E4%B9%88%E9%97%AE%E9%A2%98-%E5%A6%82%E4%BD%95%E8%A7%A3%E5%86%B3)Threadlocal 作用，原理，具體裏面存的 key value 是啥，會有什麽問題，如何解決？

`ThreadLocal` 是 Java 中用於解決線程安全問題的一種機制，它允許創建線程局部變量，即每個線程都有自己獨立的變量副本，從而避免了線程間的資源共享和同步問題。
![[Pasted image 20250116154947.png]]

從内存結構圖，我們可以看到：

- Thread 類中，有個 ThreadLocal.ThreadLocalMap 的成員變量。
- ThreadLocalMap 内部維護了 Entry 數組，每個 Entry 代表一個完整的對象，key **是** ThreadLocal **本身**，value 是 ThreadLocal 的泛型對象值。

> ThreadLocal 的作用

- **線程隔離**：`ThreadLocal` 為每個線程提供了獨立的變量副本，這意味着線程之間不會相互影響，可以安全地在多線程環境中使用這些變量而不必擔心數據競争或同步問題。
- **降低耦合度**：在同一個線程内的多個函數或組件之間，使用 `ThreadLocal` 可以減少參數的傳遞，降低代碼之間的耦合度，使代碼更加清晰和模塊化。
- **性能優勢**：由於 `ThreadLocal` 避免了線程間的同步開銷，所以在大量線程并發執行時，相比傳統的鎖機制，它可以提供更好的性能。

> ThreadLocal 的原理

`ThreadLocal` 的實現依賴於 `Thread` 類中的一個 `ThreadLocalMap` 字段，這是一個存儲 `ThreadLocal` 變量本身和對應值的映射。每個線程都有自己的 `ThreadLocalMap` 實例，用於存儲該線程所持有的所有 `ThreadLocal` 變量的值。

當你創建一個 `ThreadLocal` 變量時，它實際上就是一個 `ThreadLocal` 對象的實例。每個 `ThreadLocal` 對象都可以存儲任意類型的值，這個值對每個線程來說是獨立的。

- 當調用 `ThreadLocal` 的 `get()` 方法時，`ThreadLocal` 會檢查當前線程的 `ThreadLocalMap` 中是否有與之關聯的值。
    
- 如果有，返回該值；
    
- 如果沒有，會調用 `initialValue()` 方法（如果重寫了的話）來初始化該值，然後將其放入 `ThreadLocalMap` 中并返回。
    
- 當調用 `set()` 方法時，`ThreadLocal` 會將給定的值與當前線程關聯起來，即在當前線程的 `ThreadLocalMap` 中存儲一個鍵值對，鍵是 `ThreadLocal` 對象自身，值是傳入的值。
    
- 當調用 `remove()` 方法時，會從當前線程的 `ThreadLocalMap` 中移除與該 `ThreadLocal` 對象關聯的條目。
    

> 可能存在的問題

當一個線程結束時，其 `ThreadLocalMap` 也會随之銷毀，但是 `ThreadLocal` 對象本身不會立即被垃圾回收，直到沒有其他引用指向它為止。

因此，在使用 `ThreadLocal` 時需要注意，**如果不顯式調用**`remove()`**方法，或者線程結束時未正确清理**`ThreadLocal`**變量，可能會導致内存洩漏，因為**`ThreadLocalMap`**會持續持有**`ThreadLocal`**變量的引用，即使這些變量不再被其他地方引用。**

因此，實際應用中需要在使用完 `ThreadLocal` 變量後調用 `remove()` 方法釋放資源。

### [#](https://xiaolincoding.com/interview/juc.html#%E6%82%B2%E8%A7%82%E9%94%81%E5%92%8C%E4%B9%90%E8%A7%82%E9%94%81%E7%9A%84%E5%8C%BA%E5%88%AB)悲觀鎖和樂觀鎖的區别？

- 樂觀鎖： 就像它的名字一樣，對於并發間操作産生的線程安全問題持樂觀狀态，樂觀鎖認為競争不總 是會發生，因此它不需要持有鎖，將比較 - 替換這兩個動作作為一個原子操作嘗試去修改内存中的變量，如果失敗則表示發生沖突，那麽就應該有相應的重試邏輯。
- 悲觀鎖： 還是像它的名字一樣，對於并發間操作産生的線程安全問題持悲觀狀态，悲觀鎖認為競争總 是會發生，因此每次對某資源進行操作時，都會持有一個獨占的鎖，就像 synchronized，不管三七二十一，直接上了鎖就操作資源了。

### [#](https://xiaolincoding.com/interview/juc.html#java%E4%B8%AD%E6%83%B3%E5%AE%9E%E7%8E%B0%E4%B8%80%E4%B8%AA%E4%B9%90%E8%A7%82%E9%94%81-%E9%83%BD%E6%9C%89%E5%93%AA%E4%BA%9B%E6%96%B9%E5%BC%8F)Java 中想實現一個樂觀鎖，都有哪些方式？

1. **CAS（Compare and Swap）操作：** CAS 是樂觀鎖的基礎。Java 提供了 java.util.concurrent.atomic 包，包含各種原子變量類（如 AtomicInteger、AtomicLong），這些類使用 CAS 操作實現了線程安全的原子操作，可以用來實現樂觀鎖。
2. **版本號控制**：增加一個版本號字段記錄數據更新時候的版本，每次更新時遞增版本號。在更新數據時，同時比較版本號，若當前版本號和更新前獲取的版本號一致，則更新成功，否則失敗。
3. **時間戳**：使用時間戳記錄數據的更新時間，在更新數據時，在比較時間戳。如果當前時間戳大於數據的時間戳，則說明數據已經被其他線程更新，更新失敗。

### [#](https://xiaolincoding.com/interview/juc.html#cas-%E6%9C%89%E4%BB%80%E4%B9%88%E7%BC%BA%E7%82%B9)CAS 有什麽缺點？

CAS 的缺點主要有 3 點：

- **ABA 問題**：ABA 的問題指的是在 CAS 更新的過程中，當讀取到的值是 A，然後準備賦值的時候仍然是 A，但是實際上有可能 A 的值被改成了 B，然後又被改回了 A，這個 CAS 更新的漏洞就叫做 ABA。隻是 ABA 的問題大部分場景下都不影響并發的最終效果。Java 中有 AtomicStampedReference 來解決這個問題，他加入了預期標志和更新後標志兩個字段，更新時不光檢查值，還要檢查當前的標志是否等於預期標志，全部相等的話才會更新。
- **循環時間長開銷大**：自旋 CAS 的方式如果長時間不成功，會給 CPU 帶來很大的開銷。
- **隻能保證一個共享變量的原子操作**：隻對一個共享變量操作可以保證原子性，但是多個則不行，多個可以通過 AtomicReference 來處理或者使用鎖 synchronized 實現。

### [#](https://xiaolincoding.com/interview/juc.html#%E4%B8%BA%E4%BB%80%E4%B9%88%E4%B8%8D%E8%83%BD%E6%89%80%E6%9C%89%E7%9A%84%E9%94%81%E9%83%BD%E7%94%A8cas)為什麽不能所有的鎖都用 CAS？

CAS 操作是基於循環重試的機制，如果 CAS 操作一直未能成功，線程會一直自旋重試，占用 CPU 資源。在高并發情況下，大量線程自旋會導致 CPU 資源浪費。

### [#](https://xiaolincoding.com/interview/juc.html#voliatle%E5%85%B3%E9%94%AE%E5%AD%97%E6%9C%89%E4%BB%80%E4%B9%88%E4%BD%9C%E7%94%A8)voliatle 關鍵字有什麽作用？

volatite 作用有 2 個：

- **保證變量對所有線程的可見性**。當一個變量被聲明為 volatile 時，它會保證對這個變量的寫操作會立即刷新到主存中，而對這個變量的讀操作會直接從主存中讀取，從而确保了多線程環境下對該變量訪問的可見性。這意味着一個線程修改了 volatile 變量的值，其他線程能夠立刻看到這個修改，不會受到各自線程工作内存的影響。
    
- **禁止指令重排序優化**。volatile 關鍵字在 Java 中主要通過内存屏障來禁止特定類型的指令重排序。
    
    - 1）**寫 - 寫（Write-Write）屏障**：在對 volatile 變量執行寫操作之前，會插入一個寫屏障。這确保了在該變量寫操作之前的所有普通寫操作都已完成，防止了這些寫操作被移到 volatile 寫操作之後。
        
    - 2）**讀 - 寫（Read-Write）屏障**：在對 volatile 變量執行讀操作之後，會插入一個讀屏障。它确保了對 volatile 變量的讀操作之後的所有普通讀操作都不會被提前到 volatile 讀之前執行，保證了讀取到的數據是最新的。
        
    - 3）**寫 - 讀（Write-Read）屏障**：這是最重要的一個屏障，它發生在 volatile 寫之後和 volatile 讀之前。這個屏障确保了 volatile 寫操作之前的所有内存操作（包括寫操作）都不會被重排序到 volatile 讀之後，同時也确保了 volatile 讀操作之後的所有内存操作（包括讀操作）都不會被重排序到 volatile 寫之前。
        

### [#](https://xiaolincoding.com/interview/juc.html#%E6%8C%87%E4%BB%A4%E9%87%8D%E6%8E%92%E5%BA%8F%E7%9A%84%E5%8E%9F%E7%90%86%E6%98%AF%E4%BB%80%E4%B9%88)指令重排序的原理是什麽？

在執行程序時，為了提高性能，處理器和編譯器常常會對指令進行重排序，但是重排序要滿足下面 2 個條件才能進行：

- 在單線程環境下不能改變程序運行的結果
- 存在數據依賴關系的不允許重排序。

所以重排序不會對單線程有影響，隻會破壞多線程的執行語義。

我們看這個例子，A 和 C 之間存在數據依賴關系，同時 B 和 C 之間也存在數據依賴關系。因此在最終執行的指令序列中，C 不能被重排序到 A 和 B 的前面，如果 C 排到 A 和 B 的前面，那麽程序的結果將會被改變。但 A 和 B 之間沒有數據依賴關系，編譯器和處理器可以重排序 A 和 B 之間的執行順序。
![[Pasted image 20250116155011.png|708]]


### [#](https://xiaolincoding.com/interview/juc.html#volatile%E5%8F%AF%E4%BB%A5%E4%BF%9D%E8%AF%81%E7%BA%BF%E7%A8%8B%E5%AE%89%E5%85%A8%E5%90%97)volatile 可以保證線程安全嗎？

volatile 關鍵字可以保證可見性，但不能保證原子性，因此不能完全保證線程安全。volatile 關鍵字用於修飾變量，當一個線程修改了 volatile 修飾的變量的值，其他線程能夠立即看到最新的值，從而避免了線程之間的數據不一致。

但是，volatile 并不能解決多線程并發下的複合操作問題，比如 i++ 這種操作不是原子操作，如果多個線程同時對 i 進行自增操作，volatile 不能保證線程安全。對於複合操作，需要使用 synchronized 關鍵字或者 Lock 來保證原子性和線程安全。

### [#](https://xiaolincoding.com/interview/juc.html#volatile%E5%92%8Csychronized%E6%AF%94%E8%BE%83)volatile 和 sychronized 比較？

Synchronized 解決了多線程訪問共享資源時可能出現的競态條件和數據不一致的問題，保證了線程安全性。Volatile 解決了變量在多線程環境下的可見性和有序性問題，确保了變量的修改對其他線程是可見的。

- Synchronized: Synchronized 是一種排他性的同步機制，保證了多個線程訪問共享資源時的互斥性，即同一時刻隻允許一個線程訪問共享資源。通過對代碼塊或方法添加 Synchronized 關鍵字來實現同步。
- Volatile: Volatile 是一種輕量級的同步機制，用來保證變量的可見性和禁止指令重排序。當一個變量被聲明為 Volatile 時，線程在讀取該變量時會直接從内存中讀取，而不會使用緩存，同時對該變量的寫操作會立即刷回主内存，而不是緩存在本地内存中。

### [#](https://xiaolincoding.com/interview/juc.html#%E4%BB%80%E4%B9%88%E6%98%AF%E5%85%AC%E5%B9%B3%E9%94%81%E5%92%8C%E9%9D%9E%E5%85%AC%E5%B9%B3%E9%94%81)什麽是公平鎖和非公平鎖？

- **公平鎖：** 指多個線程按照申請鎖的順序來獲取鎖，線程直接進入隊列中排隊，隊列中的第一個線程才能獲得鎖。公平鎖的優點在於各個線程公平平等，每個線程等待一段時間後，都有執行的機會，而它的缺點就在於整體執行速度更慢，吞吐量更小。
- **非公平鎖：** 多個線程加鎖時直接嘗試獲取鎖，能搶到鎖到直接占有鎖，搶不到才會到等待隊列的隊尾等待。非公平鎖的優勢就在於整體執行速度更快，吞吐量更大，但同時也可能産生線程饑餓問題，也就是說如果一直有線程插隊，那麽在等待隊列中的線程可能長時間得不到運行。

### [#](https://xiaolincoding.com/interview/juc.html#%E9%9D%9E%E5%85%AC%E5%B9%B3%E9%94%81%E5%90%9E%E5%90%90%E9%87%8F%E4%B8%BA%E4%BB%80%E4%B9%88%E6%AF%94%E5%85%AC%E5%B9%B3%E9%94%81%E5%A4%A7)非公平鎖吞吐量為什麽比公平鎖大？

- **公平鎖執行流程**：獲取鎖時，先將線程自己添加到等待隊列的隊尾并休眠，當某線程用完鎖之後，會去喚醒等待隊列中隊首的線程嘗試去獲取鎖，鎖的使用順序也就是隊列中的先後順序，在整個過程中，線程會從運行狀态切換到休眠狀态，再從休眠狀态恢複成運行狀态，但線程每次休眠和恢複都需要從用戶态轉換成内核态，而這個狀态的轉換是比較慢的，所以公平鎖的執行速度會比較慢。
- **非公平鎖執行流程**：當線程獲取鎖時，會先通過 CAS 嘗試獲取鎖，如果獲取成功就直接擁有鎖，如果獲取鎖失敗才會進入等待隊列，等待下次嘗試獲取鎖。這樣做的好處是，獲取鎖不用遵循先到先得的規則，從而避免了線程休眠和恢複的操作，這樣就加速了程序的執行效率。

### [#](https://xiaolincoding.com/interview/juc.html#synchronized%E6%98%AF%E5%85%AC%E5%B9%B3%E9%94%81%E5%90%97)Synchronized 是公平鎖嗎？

Synchronized 不屬於公平鎖，ReentrantLock 是公平鎖。

### [#](https://xiaolincoding.com/interview/juc.html#reentrantlock%E6%98%AF%E6%80%8E%E4%B9%88%E5%AE%9E%E7%8E%B0%E5%85%AC%E5%B9%B3%E9%94%81%E7%9A%84)ReentrantLock 是怎麽實現公平鎖的？

我們來看一下公平鎖與非公平鎖的加鎖方法的源碼。公平鎖的鎖獲取源碼如下：

```
protected final boolean tryAcquire(int acquires) {

    final Thread current = Thread.currentThread();
    int c = getState();

    if (c == 0) {

        if (!hasQueuedPredecessors() && //這裏判斷了 hasQueuedPredecessors()
                compareAndSetState(0, acquires)) {
            
            setExclusiveOwnerThread(current);
            
            return true;
        }

    } else if (current == getExclusiveOwnerThread()) {

        int nextc = c + acquires;

        if (nextc < 0) {
            throw new Error("Maximum lock count exceeded");
        }
        setState(nextc);
        return true;

    }
    return false;
}
```

非公平鎖的鎖獲取源碼如下：

```
final boolean nonfairTryAcquire(int acquires) {

    final Thread current = Thread.currentThread();
    int c = getState();

    if (c == 0) {

        if (compareAndSetState(0, acquires)) { //這裏沒有判斷      hasQueuedPredecessors()

            setExclusiveOwnerThread(current);

            return true;

        }

    }

    else if (current == getExclusiveOwnerThread()) {

        int nextc = c + acquires;

        if (nextc < 0) // overflow

        throw new Error("Maximum lock count exceeded");

        setState(nextc);

        return true;

    }

    return false;

}
```

通過對比，我們可以明顯的看出公平鎖與非公平鎖的 lock () 方法唯一的區别就在於公平鎖在獲取鎖時多了一個限制條件：hasQueuedPredecessors () 為 false，這個方法就是判斷在等待隊列中是否已經有線程在排隊了。

這也就是公平鎖和非公平鎖的核心區别，如果是公平鎖，那麽一旦已經有線程在排隊了，當前線程就不再嘗試獲取鎖；對於非公平鎖而言，無論是否已經有線程在排隊，都會嘗試獲取一下鎖，獲取不到的話，再去排隊。這裏有一個特例需要我們注意，針對 tryLock () 方法，它不遵守設定的公平原則。

例如，當有線程執行 tryLock () 方法的時候，一旦有線程釋放了鎖，那麽這個正在 tryLock 的線程就能獲取到鎖，即使設置的是公平鎖模式，即使在它之前已經有其他正在等待隊列中等待的線程，簡單地說就是 tryLock 可以插隊。

看它的源碼就會發現：

```
public boolean tryLock() {

    return sync.nonfairTryAcquire(1);

}
```

這裏調用的就是 nonfairTryAcquire ()，表明了是不公平的，和鎖本身是否是公平鎖無關。綜上所述，公平鎖就是會按照多個線程申請鎖的順序來獲取鎖，從而實現公平的特性。

非公平鎖加鎖時不考慮排隊等待情況，直接嘗試獲取鎖，所以存在後申請卻先獲得鎖的情況，但由此也提高了整體的效率。

## [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0)線程池

### [#](https://xiaolincoding.com/interview/juc.html#%E4%BB%8B%E7%BB%8D%E4%B8%80%E4%B8%8B%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86)介紹一下線程池的工作原理

線程池是為了減少頻繁的創建線程和銷毀線程帶來的性能損耗，線程池的工作原理如下圖：
![[Pasted image 20250116155030.png]]

線程池分為核心線程池，線程池的最大容量，還有等待任務的隊列，提交一個任務，如果核心線程沒有滿，就創建一個線程，如果滿了，就是會加入等待隊列，如果等待隊列滿了，就會增加線程，如果達到最大線程數量，如果都達到最大線程數量，就會按照一些丢棄的策略進行處理。

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E5%8F%82%E6%95%B0%E6%9C%89%E5%93%AA%E4%BA%9B)線程池的參數有哪些？

![[Pasted image 20250116155041.png]]

線程池的構造函數有 7 個參數：
- **corePoolSize**：線程池核心線程數量。默認情況下，線程池中線程的數量如果 <= corePoolSize，那麽即使這些線程處於空閑狀态，那也不會被銷毀。
- **maximumPoolSize**：線程池中最多可容納的線程數量。當一個新任務交給線程池，如果此時線程池中有空閑的線程，就會直接執行，如果沒有空閑的線程且當前線程池的線程數量小於 corePoolSize，就會創建新的線程來執行任務，否則就會將該任務加入到阻塞隊列中，如果阻塞隊列滿了，就會創建一個新線程，從阻塞隊列頭部取出一個任務來執行，并將新任務加入到阻塞隊列末尾。如果當前線程池中線程的數量等於 maximumPoolSize，就不會創建新線程，就會去執行拒絕策略。
- **keepAliveTime**：當線程池中線程的數量大於 corePoolSize，并且某個線程的空閑時間超過了 keepAliveTime，那麽這個線程就會被銷毀。
- **unit**：就是 keepAliveTime 時間的單位。
- **workQueue**：工作隊列。當沒有空閑的線程執行新任務時，該任務就會被放入工作隊列中，等待執行。
- **threadFactory**：線程工廠。可以用來給線程取名字等等
- **handler**：拒絕策略。當一個新任務交給線程池，如果此時線程池中有空閑的線程，就會直接執行，如果沒有空閑的線程，就會將該任務加入到阻塞隊列中，如果阻塞隊列滿了，就會創建一個新線程，從阻塞隊列頭部取出一個任務來執行，并將新任務加入到阻塞隊列末尾。如果當前線程池中線程的數量等於 maximumPoolSize，就不會創建新線程，就會去執行拒絕策略

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0%E5%B7%A5%E4%BD%9C%E9%98%9F%E5%88%97%E6%BB%A1%E4%BA%86%E6%9C%89%E5%93%AA%E4%BA%9B%E6%8B%92%E6%8E%A5%E7%AD%96%E7%95%A5)線程池工作隊列滿了有哪些拒接策略？

當線程池的任務隊列滿了之後，線程池會執行指定的拒絕策略來應對，常用的四種拒絕策略包括：CallerRunsPolicy、AbortPolicy、DiscardPolicy、DiscardOldestPolicy，此外，還可以通過實現 RejectedExecutionHandler 接口來自定義拒絕策略。

四種預置的拒絕策略：

- CallerRunsPolicy，使用線程池的調用者所在的線程去執行被拒絕的任務，除非線程池被停止或者線程池的任務隊列已有空缺。
- AbortPolicy，直接抛出一個任務被線程池拒絕的異常。
- DiscardPolicy，不做任何處理，靜默拒絕提交的任務。
- DiscardOldestPolicy，抛棄最老的任務，然後執行該任務。
- 自定義拒絕策略，通過實現接口可以自定義任務拒絕策略。

### [#](https://xiaolincoding.com/interview/juc.html#%E6%9C%89%E7%BA%BF%E7%A8%8B%E6%B1%A0%E5%8F%82%E6%95%B0%E8%AE%BE%E7%BD%AE%E7%9A%84%E7%BB%8F%E9%AA%8C%E5%90%97)有線程池參數設置的經驗嗎？

- CPU 密集型：corePoolSize = CPU 核數 + 1
- IO 密集型：corePoolSize = CPU 核數 * 2

### [#](https://xiaolincoding.com/interview/juc.html#%E6%A0%B8%E5%BF%83%E7%BA%BF%E7%A8%8B%E6%95%B0%E8%AE%BE%E7%BD%AE%E4%B8%BA0%E5%8F%AF%E4%B8%8D%E5%8F%AF%E4%BB%A5)**核心線程數設置為 0 可不可以？**

可以，當核心線程數為 0 的時候，會創建一個非核心線程進行執行。

從下面的源碼也可以看到，當核心線程數為 0 時，來了一個任務之後，會先將任務添加到任務隊列，同時也會判斷當前工作的線程數是否為 0，如果為 0，則會創建線程來執行線程池的任務。
![[Pasted image 20250116155059.png]]

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%A7%8D%E7%B1%BB%E6%9C%89%E5%93%AA%E4%BA%9B)線程池種類有哪些？

- ScheduledThreadPool：可以設置定期的執行任務，它支持定時或周期性執行任務，比如每隔 10 秒鍾執行一次任務，我通過這個實現類設置定期執行任務的策略。
- FixedThreadPool：它的核心線程數和最大線程數是一樣的，所以可以把它看作是固定線程數的線程池，它的特點是線程池中的線程數除了初始階段需要從 0 開始增加外，之後的線程數量就是固定的，就算任務數超過線程數，線程池也不會再創建更多的線程來處理任務，而是會把超出線程處理能力的任務放到任務隊列中進行等待。而且就算任務隊列滿了，到了本該繼續增加線程數的時候，由於它的最大線程數和核心線程數是一樣的，所以也無法再增加新的線程了。
- CachedThreadPool：可以稱作可緩存線程池，它的特點在於線程數是幾乎可以無限增加的（實際最大可以達到 Integer.MAX_VALUE，為 2^31-1，這個數非常大，所以基本不可能達到），而當線程閑置時還可以對線程進行回收。也就是說該線程池的線程數量不是固定不變的，當然它也有一個用於存儲提交任務的隊列，但這個隊列是 SynchronousQueue，隊列的容量為 0，實際不存儲任何任務，它隻負責對任務進行中轉和傳遞，所以效率比較高。
- SingleThreadExecutor：它會使用唯一的線程去執行任務，原理和 FixedThreadPool 是一樣的，隻不過這裏線程隻有一個，如果線程在執行任務的過程中發生異常，線程池也會重新創建一個線程來執行後續的任務。這種線程池由於隻有一個線程，所以非常适合用於所有任務都需要按被提交的順序依次執行的場景，而前幾種線程池不一定能夠保障任務的執行順序等於被提交的順序，因為它們是多線程并行執行的。
- SingleThreadScheduledExecutor：它實際和 ScheduledThreadPool 線程池非常相似，它隻是 ScheduledThreadPool 的一個特例，内部隻有一個線程。

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0%E4%B8%80%E8%88%AC%E6%98%AF%E6%80%8E%E4%B9%88%E7%94%A8%E7%9A%84)線程池一般是怎麽用的？

Java 中的 Executors 類定義了一些快捷的工具方法，來幫助我們快速創建線程池。《阿裏巴巴 Java 開發手冊》中提到，禁止使用這些方法來創建線程池，而應該手動 new ThreadPoolExecutor 來創建線程池。這一條規則的背後，是大量血淋淋的生産事故，最典型的就是 newFixedThreadPool 和 newCachedThreadPool，可能因為資源耗盡導致 OOM 問題。

所以，不建議使用 Executors 提供的兩種快捷的線程池，原因如下：

- 我們需要根據自己的場景、并發情況來評估線程池的幾個核心參數，包括核心線程數、最大線程數、線程回收策略、工作隊列的類型，以及拒絕策略，确保線程池的工作行為符合需求，一般都需要設置有界的工作隊列和可控的線程數。
- 任何時候，都應該為自定義線程池指定有意義的名稱，以方便排查問題。當出現線程數量暴增、線程死鎖、線程占用大量 CPU、線程執行出現異常等問題時，我們往往會抓取線程棧。此時，有意義的線程名稱，就可以方便我們定位問題。

除了建議手動聲明線程池以外，我還建議用一些監控手段來觀察線程池的狀态。線程池這個組件往往會表現得任勞任怨、默默無聞，除非是出現了拒絕策略，否則壓力再大都不會抛出一個異常。如果我們能提前觀察到線程池隊列的積壓，或者線程數量的快速膨脹，往往可以提早發現并解決問題。

### [#](https://xiaolincoding.com/interview/juc.html#%E7%BA%BF%E7%A8%8B%E6%B1%A0%E4%B8%ADshutdown-shutdownnow-%E8%BF%99%E4%B8%A4%E4%B8%AA%E6%96%B9%E6%B3%95%E6%9C%89%E4%BB%80%E4%B9%88%E4%BD%9C%E7%94%A8)線程池中 shutdown ()，shutdownNow () 這兩個方法有什麽作用？

從源碼【高亮】注釋可以很清晰的看出兩者的區别：

- shutdown 使用了以後會置狀态為 SHUTDOWN，正在執行的任務會繼續執行下去，沒有被執行的則中斷。此時，則不能再往線程池中添加任何任務，否則將會抛出 RejectedExecutionException 異常
- 而 shutdownNow 為 STOP，并試圖停止所有正在執行的線程，不再處理還在池隊列中等待的任務，當然，它會返回那些未執行的任務。 它試圖終止線程的方法是通過調用 Thread.interrupt () 方法來實現的，但是這種方法的作用有限，如果線程中沒有 sleep 、wait、Condition、定時鎖等應用，interrupt () 方法是無法中斷當前的線程的。所以，ShutdownNow () 并不代表線程池就一定立即就能退出，它可能必須要等待所有正在執行的任務都執行完成了才能退出。

**shutdown 源碼：**

```
public void shutdown() {
	final ReentrantLock mainLock = this.mainLock;
	mainLock.lock();
	try {
		checkShutdownAccess();
		// 高亮
		advanceRunState(SHUTDOWN);
		interruptIdleWorkers();
		onShutdown();
	} finally {
		mainLock.unlock();
	}
	tryTerminate();
}
```

**shutdownNow 源碼：**

```
public List<Runnable> shutdownNow() {
	List<Runnable> tasks;
	final ReentrantLock mainLock = this.mainLock;
	mainLock.lock();
	try {
		checkShutdownAccess();
		// 高亮
		advanceRunState(STOP);
		interruptWorkers();
		// 高亮
		tasks = drainQueue();
	} finally {
		mainLock.unlock();
	}
	tryTerminate();
	// 高亮
	return tasks;
}
```

### [#](https://xiaolincoding.com/interview/juc.html#%E6%8F%90%E4%BA%A4%E7%BB%99%E7%BA%BF%E7%A8%8B%E6%B1%A0%E4%B8%AD%E7%9A%84%E4%BB%BB%E5%8A%A1%E5%8F%AF%E4%BB%A5%E8%A2%AB%E6%92%A4%E5%9B%9E%E5%90%97)提交給線程池中的任務可以被撤回嗎？

可以，當向線程池提交任務時，會得到一個 `Future` 對象。這個 `Future` 對象提供了幾種方法來管理任務的執行，包括取消任務。

取消任務的主要方法是 `Future` 接口中的 `cancel(boolean mayInterruptIfRunning)` 方法。這個方法嘗試取消執行的任務。參數 `mayInterruptIfRunning` 指示是否允許中斷正在執行的任務。如果設置為 `true`，則表示如果任務已經開始執行，那麽允許中斷任務；如果設置為 `false`，任務已經開始執行則不會被中斷。

```
public interface Future<V> {
    // 是否取消線程的執行
    boolean cancel(boolean mayInterruptIfRunning);
    // 線程是否被取消
    boolean isCancelled();
    //線程是否執行完畢
    boolean isDone();
      // 立即獲得線程返回的結果
    V get() throws InterruptedException, ExecutionException;
      // 延時時間後再獲得線程返回的結果
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

取消線程池中任務的方式，代碼如下，通過 future 對象的 cancel (boolean) 函數來定向取消特定的任務。

```
public static void main(String[] args) {
        ExecutorService service = Executors.newSingleThreadExecutor();
        Future future = service.submit(new TheradDemo());

        try {
          // 可能抛出異常
            future.get();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }finally {
          //終止任務的執行
            future.cancel(true);
        }
 }
```

## [#](https://xiaolincoding.com/interview/juc.html#%E5%9C%BA%E6%99%AF)場景

### [#](https://xiaolincoding.com/interview/juc.html#%E5%A4%9A%E7%BA%BF%E7%A8%8B%E6%89%93%E5%8D%B0%E5%A5%87%E5%81%B6%E6%95%B0-%E6%80%8E%E4%B9%88%E6%8E%A7%E5%88%B6%E6%89%93%E5%8D%B0%E7%9A%84%E9%A1%BA%E5%BA%8F)多線程打印奇偶數，怎麽控制打印的順序

可以利用 wait () 和 notify () 來控制線程的執行順序。

以下是一個基於這種方法的簡單示例：

```
public class PrintOddEven {
    private static final Object lock = new Object();
    private static int count = 1;
    private static final int MAX_COUNT = 10;

    public static void main(String[] args) {
        Runnable printOdd = () -> {
            synchronized (lock) {
                while (count <= MAX_COUNT) {
                    if (count % 2 != 0) {
                        System.out.println(Thread.currentThread().getName() + ": " + count++);
                        lock.notify();
                    } else {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        };

        Runnable printEven = () -> {
            synchronized (lock) {
                while (count <= MAX_COUNT) {
                    if (count % 2 == 0) {
                        System.out.println(Thread.currentThread().getName() + ": " + count++);
                        lock.notify();
                    } else {
                        try {
                            lock.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        };

        Thread oddThread = new Thread(printOdd, "OddThread");
        Thread evenThread = new Thread(printEven, "EvenThread");

        oddThread.start();
        evenThread.start();
    }
}
```

在上面的示例中，通過一個共享的鎖對象 lock 來控制兩個線程的交替執行。一個線程負責打印奇數，另一個線程負責打印偶數，通過 wait () 和 notify () 方法來在兩個線程之間實現順序控制。當當前應該打印奇數時，偶數線程會進入等待狀态，反之亦然。