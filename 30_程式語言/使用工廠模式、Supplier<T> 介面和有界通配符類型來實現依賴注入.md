- **依賴注入 (Dependency Injection):** 一種設計模式，將物件的依賴關係從物件本身移到外部，通常通過構造方法或 setter 方法注入依賴。
- **工廠模式 (Factory Method pattern):** 一種創建型設計模式，使用工廠方法來創建物件，而不是直接使用 new 關鍵字。
- **`Supplier<T>` 介面:** Java 8 引入的函數式介面，代表一個供應者，可以重複調用以創建 T 類型的實例。



- 將工廠物件 (而不是直接的依賴物件) 傳遞給構造方法。這樣，物件就可以在需要時通過工廠來創建依賴物件。
	- 工廠物件可以多次調用，每次調用都會創建一個新的物件實例
-  `Supplier<T>` 介面非常適合代表工廠，因為它只包含一個 get() 方法，可以返回一個 T 類型的實例。
- 當方法接受 `Supplier<T>` 作為參數時，應該使用有界通配符類型 (bounded wildcard type) `Supplier<? extends Tile>` 來約束工廠的類型參數。這樣可以允許客戶端傳入可以創建 Tile 或 Tile 的任何子類別的工廠。
	- `**Mosaic create(Supplier<? extends Tile> tileFactory):`這個例子展示了一個使用客戶端提供的工廠來創建 tile 的方法。tileFactory 可以創建 Tile 或 Tile 的任何子類別。



好的，我們來用一個具體的例子來說明如何使用工廠模式、`Supplier<T>` 介面和有界通配符類型來實現依賴注入，並展示其靈活性和可測試性。

**情境：**
假設我們正在開發一個遊戲，其中需要創建不同類型的敵人。我們可以使用工廠模式來創建敵人，並使用 `Supplier<T>` 介面來代表工廠。

**程式碼：**
```java
import java.util.function.Supplier;

// 敵人介面
interface Enemy {
    void attack();
}

// 具體敵人類別
class Goblin implements Enemy {
    @Override
    public void attack() {
        System.out.println("Goblin attacks with a club!");
    }
}

class Orc implements Enemy {
    @Override
    public void attack() {
        System.out.println("Orc attacks with an axe!");
    }
}

// 遊戲類別
class Game {
    // 使用 Supplier<T> 作為工廠
    public void createEnemy(Supplier<? extends Enemy> enemyFactory) {
        Enemy enemy = enemyFactory.get();
        enemy.attack();
    }
}

// 測試類別
public class GameExample {
    public static void main(String[] args) {
        Game game = new Game();

		// 1. 使用 lambda 表達式創建工廠
        // Goblin 工廠
        game.createEnemy(() -> new Goblin()); // 使用 lambda 表達式創建工廠

        // Orc 工廠
        game.createEnemy(() -> new Orc()); // 使用 lambda 表達式創建工廠

        // 2. 使用匿名類別創建工廠
        game.createEnemy(new Supplier<Enemy>() {
            @Override
            public Enemy get() {
                return new Goblin();
            }
        });

        // 3. 使用有界通配符類型，可以傳入創建 Goblin 或 Orc 的工廠
        Supplier<? extends Enemy> goblinFactory = () -> new Goblin();
        game.createEnemy(goblinFactory);

        Supplier<? extends Enemy> orcFactory = () -> new Orc();
        game.createEnemy(orcFactory);
    }
}
```

**解釋：**

1. **`Enemy` 介面：** 定義了所有敵人需要實現的 `attack()` 方法。
2. **`Goblin` 和 `Orc` 類別：** 實現了 `Enemy` 介面，代表具體的敵人類型。
3. **`Game` 類別：**
   * `createEnemy(Supplier<? extends Enemy> enemyFactory)` 方法接受一個 `Supplier<? extends Enemy>` 作為參數，這是一個可以創建 `Enemy` 或 `Enemy` 的任何子類別的工廠。
   * `enemyFactory.get()` 方法調用工廠的 `get()` 方法來創建一個新的敵人實例。
4. **`GameExample` 類別：**
   * 展示了如何使用 `createEnemy()` 方法來創建不同類型的敵人。
   * 使用 lambda 表達式和匿名類別來創建工廠。
   * 使用有界通配符類型，可以將創建 `Goblin` 或 `Orc` 的工廠傳遞給 `createEnemy()` 方法。

**靈活性：**

* **可替換的敵人類型：**  可以輕鬆地創建新的敵人類型，並將其工廠傳遞給 `createEnemy()` 方法，而無需修改 `Game` 類別。
* **可測試性：**  可以通過傳遞模擬的工廠來測試 `Game` 類別，而無需依賴具體的敵人類型。

**可測試性範例：**

```java
import org.junit.jupiter.api.Test;
import static org.mockito.Mockito.*;

class GameTest {

    @Test
    void testCreateEnemy() {
        // arrage
        Game game = new Game();
        Supplier<Enemy> mockEnemyFactory = mock(Supplier.class);
        Enemy mockEnemy = mock(Enemy.class);
        when(mockEnemyFactory.get()).thenReturn(mockEnemy);

		// act
        game.createEnemy(mockEnemyFactory);

        // assert
        verify(mockEnemy).attack();
    }
}
```

在這個測試中，我們使用 Mockito 框架創建了一個模擬的 `Supplier<Enemy>` 工廠和一個模擬的 `Enemy` 物件。
我們驗證了 `createEnemy()` 方法是否調用了模擬的 `Enemy` 物件的 `attack()` 方法。

**總結：**

這個例子展示了如何使用工廠模式、`Supplier<T>` 介面和有界通配符類型來實現依賴注入。這種方法可以提高程式碼的靈活性和可測試性，並使程式碼更易於維護和擴展。通過使用有界通配符類型，可以允許客戶端傳入更靈活的工廠，從而提高程式碼的重用性。
