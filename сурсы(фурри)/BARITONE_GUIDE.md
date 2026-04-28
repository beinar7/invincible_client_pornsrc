# 🤖 BARITONE INTEGRATION GUIDE 🔥🔥🔥

**Baritone** - это автоматизационный мод для навигации в Minecraft 🙏🙏🙏

GitHub: https://github.com/cabaletta/baritone

---

## 📚 ЧТО ТАКОЕ BARITONE?

Baritone - это **AI навигация и автоматизация** для Minecraft:

✅ **Pathfinding** - находит оптимальный маршрут 🔥
✅ **Mining** - автоматически копает руду
✅ **Building** - строит по команде
✅ **Exploring** - автоматический экспорт
✅ **Farming** - фармит культуры
✅ **Block Placing** - ставит блоки

---

## 🎯 ОСНОВНЫЕ КОМАНДЫ BARITONE

```
#goto x y z              ► Идти в координаты
#goto player_name        ► Идти к игроку
#mine diamond ore        ► Искать и копать алмазы
#mine coal              ► Копать уголь
#build structure        ► Строить структуру
#farm                   ► Фармить культуры
#explore                ► Экспорить мир
#cancel                 ► Остановить текущий процесс
#reset                  ► Полный reset
#pause                  ► Пауза
#resume                 ► Продолжить
```

---

## 🔗 АРХИТЕКТУРА BARITONE

```
baritone/
├── api/
│   ├── IBaritone.java ................. Главный интерфейс
│   ├── Settings.java .................. Настройки
│   └── event/
│       ├── EventBus.java .............. Event система
│       └── events/
│           ├── PathStartEvent
│           ├── PathFinishEvent
│           └── и т.д.
├── behavior/
│   ├── PathingBehavior.java ........... Основной патhing
│   ├── LookBehavior.java .............. Управление взглядом
│   └── WalkingBehavior.java ........... Управление ходьбой
├── pathing/
│   ├── movement/
│   │   ├── ActionCosts.java ........... Стоимость движения
│   │   └── MovementHelper.java ........ Помощник движения
│   └── path/
│       ├── PathExecutor.java .......... Выполнение пути
│       └── IPath.java ................. Интерфейс пути
├── process/
│   ├── MineProcess.java ............... Процесс копания
│   ├── BuildProcess.java .............. Процесс строительства
│   ├── GetToBlockProcess.java ......... Процесс подхода к блоку
│   └── и т.д.
└── utils/
    ├── BlockStateInterface.java ........ Данные блоков
    └── WaystoneFinder.java ............ Поиск ориентиров
```

---

## 💻 КАК ИНТЕГРИРОВАТЬ BARITONE В MINCED

### Шаг 1: Создать модуль BaritonePath

```
src/main/java/free/minced/modules/impl/misc/BaritonePath.java
```

```java
@ModuleDescriptor(
    name = "BaritonePath",
    description = "Автоматическая навигация Baritone",
    category = ModuleCategory.MISC,
    key = GLFW.GLFW_KEY_P
)
public class BaritonePath extends Module {
    
    private static final IBaritone baritone = BaritoneAPI.getProvider()
        .getPrimaryBaritone();
    
    private double targetX;
    private double targetY;
    private double targetZ;
    
    @Setting(name = "AutoMine")
    public boolean autoMine = false;
    
    @Override
    public void onEnable() {
        super.onEnable();
        // Получить текущие координаты игрока как финиш
        if (mc.player != null) {
            targetX = mc.player.getX();
            targetY = mc.player.getY();
            targetZ = mc.player.getZ();
        }
    }
    
    @Override
    public void onDisable() {
        super.onDisable();
        baritone.getPathingBehavior().cancelEverything();
    }
    
    @Override
    public void onEvent(Event event) {
        if (event instanceof EventSync) {
            // Навигация каждый тик
            updateNavigation();
        }
    }
    
    private void updateNavigation() {
        if (mc.player == null) return;
        
        // Навигация к координатам
        baritone.getPathingBehavior().goto(
            new BlockPos((long)targetX, (long)targetY, (long)targetZ)
        );
    }
    
    // Команды в чате
    public void gotoCoords(double x, double y, double z) {
        this.targetX = x;
        this.targetY = y;
        this.targetZ = z;
        enable();
    }
}
```

### Шаг 2: Создать модуль BaritoneAutoMine

```
src/main/java/free/minced/modules/impl/misc/BaritoneAutoMine.java
```

```java
@ModuleDescriptor(
    name = "BaritoneAutoMine",
    description = "Автоматическое копание руды",
    category = ModuleCategory.MISC,
    key = GLFW.GLFW_KEY_M
)
public class BaritoneAutoMine extends Module {
    
    private static final IBaritone baritone = BaritoneAPI.getProvider()
        .getPrimaryBaritone();
    
    @Setting(name = "MinBlock")
    public String mineBlock = "diamond_ore";
    
    @Override
    public void onEnable() {
        super.onEnable();
        startMining();
    }
    
    @Override
    public void onDisable() {
        super.onDisable();
        baritone.getPathingBehavior().cancelEverything();
    }
    
    private void startMining() {
        // Начать копание
        baritone.getMineProcess().mineByName(mineBlock);
    }
}
```

### Шаг 3: Добавить Baritone в зависимости

В `build.gradle` добавить:

```gradle
repositories {
    maven {
        name = "baritone"
        url = "https://maven.baritone.leijurv.com/"
    }
}

dependencies {
    modImplementation "cabaletta:baritone-api:LATEST"
}
```

---

## 🎮 ИСПОЛЬЗОВАНИЕ В ИГРЕ

```
1. Нажать кнопку BaritonePath (по умолчанию P)
2. Написать в чат: .goto 1000 64 1000
3. Baritone автоматически идёт к координатам

Или:

1. Нажать BaritonePath
2. Написать: .mine diamond
3. Baritone ищет и копает алмазы
```

---

## ⚙️ НАСТРОЙКИ BARITONE

```java
// Скорость ходьбы
baritone.getSettings().movementSpeed.value = 1.5;

// Дальность поиска
baritone.getSettings().mineDistance.value = 32;

// Автоматическое выхождение для инструментов
baritone.getSettings().abandonEverythingElseMineMapart.value = false;

// Избегать опасных блоков
baritone.getSettings().avoidance.value = true;
```

---

## 🔥 СОБЫТИЯ BARITONE

```java
// При начале пути
baritone.getEventBus().register(new Object() {
    @Subscribe
    public void onPathStartEvent(PathStartEvent event) {
        System.out.println("Путь начался!");
    }
});

// При конце пути
@Subscribe
public void onPathFinishEvent(PathFinishEvent event) {
    System.out.println("Путь завершён!");
    Minced.getInstance().getModuleHandler()
        .get(BaritonePath.class).disable();
}
```

---

## 📋 ОСНОВНЫЕ МЕТОДЫ BARITONE

```java
// Управление навигацией
baritone.getPathingBehavior().goto(BlockPos)
baritone.getPathingBehavior().cancel()
baritone.getPathingBehavior().pause()
baritone.getPathingBehavior().resume()

// Процессы
baritone.getMineProcess().mineByName(String)
baritone.getBuildProcess().build(String, BlockPos)
baritone.getExploreProcess().explore(int radius)

// Поведение
baritone.getLookBehavior().updateTarget(...)
baritone.getWalkingBehavior().update()

// Настройки
baritone.getSettings().movementSpeed.value = 1.5
baritone.getSettings().mineDistance.value = 32

// Позиция
baritone.getPathingBehavior().getGoal()
baritone.getPathingBehavior().getPath()
```

---

## 🚀 ПРИМЕРЫ ИСПОЛЬЗОВАНИЯ

### Пример 1: Автогото

```java
public void goToCoords(int x, int y, int z) {
    baritone.getPathingBehavior()
        .goto(new BlockPos(x, y, z));
}

// Использование: goToCoords(1000, 64, 1000);
```

### Пример 2: Автокопание с фильтром

```java
public void mineDiamonds() {
    List<String> ores = Arrays.asList(
        "diamond_ore", 
        "deepslate_diamond_ore"
    );
    
    for (String ore : ores) {
        baritone.getMineProcess().mineByName(ore);
    }
}
```

### Пример 3: Поиск и подход к блоку

```java
public void findAndGotoBlock(String blockName) {
    baritone.getGetToBlockProcess()
        .getToBlock(blockName);
}

// Использование: findAndGotoBlock("crafting_table");
```

---

## ⚠️ ВАЖНОЕ

- ❌ Baritone **ЗАПРЕЩЁН** на большинстве серверов!
- ⚠️ Используй только на **приватных серверах** или **одиночной игре**
- 📖 Лицензия: **LGPL-3.0**

---

## 📚 ПОЛНАЯ ДОКУМЕНТАЦИЯ BARITONE

https://baritone.leijurv.com/

---

Готово! Теперь ты можешь интегрировать Baritone в свой клиент! 🔥🔥🔥 🙏🙏🙏 💔💔💔
