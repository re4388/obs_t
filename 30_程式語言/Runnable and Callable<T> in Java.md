在 Java 中，`Runnable` 是一個功能性介面（Functional Interface），它代表了一個可以被執行的任務。這個介面只有一個抽象方法 `run()`，當你想要在一個執行緒中執行某些代碼時，可以實現這個介面。

### 1. Runnable 介面的定義

`Runnable` 介面的定義如下：
```java

@FunctionalInterface
public interface Runnable {
    void run();
}
```

### 2. 使用 Runnable 的目的

使用 `Runnable` 介面的主要目的是為了將任務封裝成一個對象，這樣可以將其傳遞給執行緒（`Thread`）來執行。這樣的設計使得代碼更加靈活，因為你可以將任務與執行緒的創建和管理分開。

### 3. 如何使用 Runnable

以下是使用 `Runnable` 的基本步驟：

1. **實現 Runnable 介面**：創建一個類並實現 `Runnable` 介面，並重寫 `run()` 方法。
    
2. **創建 Thread 對象**：將實現了 `Runnable` 的對象傳遞給 `Thread` 的構造函數。
    
3. **啟動執行緒**：調用 `Thread` 對象的 `start()` 方法來啟動執行緒。
    

### 4. 示例代碼

以下是一個簡單的示例，展示如何使用 `Runnable` 介面：
```java

class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println("執行緒正在運行: " + i);
            try {
                Thread.sleep(1000); // 暫停 1 秒
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt(); // 恢復中斷狀態
            }
        }
    }
}

public class RunnableExample {
    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();
        Thread thread = new Thread(myRunnable);
        thread.start(); // 啟動執行緒
    }
}
```


### 5. 總結

- `Runnable` 介面是一個功能性介面，代表可以被執行的任務。
- 通過實現 `Runnable` 介面，可以將任務封裝成對象，並將其傳遞給 `Thread` 來執行。
- 使用 `Runnable` 可以使代碼更加靈活和可重用，並且支持多執行緒的編程。




在 Java 中，`Callable<T>` 是一個功能性介面，與 `Runnable` 類似，但它具有一些重要的區別。`Callable` 介面允許你定義一個可以返回結果的任務，並且可以拋出異常。這使得 `Callable` 在多執行緒編程中更具靈活性。

### 1. Callable 介面的定義

`Callable` 介面的定義如下：
```java

@FunctionalInterface
public interface Callable<V> {
    V call() throws Exception;
}

```

### 2. Callable 的特點

- **返回值**：`Callable` 的 `call()` 方法可以返回一個結果，這個結果的類型由泛型參數 `V` 指定。
- **異常處理**：`call()` 方法可以拋出異常，這使得在執行任務時可以處理各種可能的錯誤情況。
- **與執行緒的結合**：`Callable` 通常與 `ExecutorService` 一起使用，這樣可以方便地管理執行緒池和任務的執行。

### 3. 如何使用 Callable

使用 `Callable` 的基本步驟如下：

1. **實現 Callable 介面**：創建一個類並實現 `Callable` 介面，並重寫 `call()` 方法。
2. **提交任務**：將 `Callable` 對象提交給 `ExecutorService`，並獲取 `Future` 對象。
3. **獲取結果**：通過 `Future` 對象來獲取任務的執行結果。

### 4. 示例代碼

以下是一個使用 `Callable` 的簡單示例：
```java

import java.util.concurrent.Callable;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

class MyCallable implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        // 模擬一些計算
        int sum = 0;
        for (int i = 1; i <= 10; i++) {
            sum += i;
            Thread.sleep(100); // 暫停 100 毫秒
        }
        return sum; // 返回計算結果
    }
}

public class CallableExample {
    public static void main(String[] args) {
        ExecutorService executorService = Executors.newSingleThreadExecutor();
        MyCallable myCallable = new MyCallable();
        
        // 提交任務並獲取 Future 對象
        Future<Integer> future = executorService.submit(myCallable);
        
        try {
            // 獲取結果
            Integer result = future.get();
            System.out.println("計算結果: " + result);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        } finally {
            executorService.shutdown(); // 關閉執行緒池
        }
    }
}

```

### 5. 總結

- `Callable<T>` 介面允許你定義可以返回結果的任務，並且可以拋出異常。
- 它與 `ExecutorService` 結合使用，可以方便地管理多執行緒的任務執行。
- 使用 `Callable` 可以提高代碼的靈活性和可讀性，特別是在需要返回計算結果或處理異常的情況下。