# 🔥 MINCED 1.20.4 - АРХИТЕКТУРА И ШПОРА 🙏🙏🙏

## 📋 БЫСТРАЯ НАВИГАЦИЯ

```
free.minced/
├── Minced.java ........................ Главная точка входа (Синглтон)
├── modules/ ........................... Система модулей
│   ├── Module.java .................... Базовый класс всех модулей
│   ├── api/
│   │   ├── ModuleManager.java ......... Управление модулями
│   │   ├── ModuleCategory.java ........ Категории модулей
│   │   └── ModuleDescriptor.java ...... Аннотация @ModuleDescriptor
│   └── impl/
│       ├── combat/ .................... Боевые модули (атака, защита)
│       ├── movement/ .................. Движение (полёт, спринт, скорость)
│       ├── render/ .................... Визуальные эффекты (ESP, trails)
│       ├── display/ ................... UI интерфейс (ClickGUI, HUD)
│       └── misc/ ...................... Утилиты
├── framework/ ......................... Рендер и UI система
│   ├── animation/ ..................... Плавные анимации
│   ├── render/ ........................ Отрисовка и шейдеры
│   ├── screens/ ....................... Экраны меню
│   ├── buttons/ ....................... Кнопки UI
│   ├── color/ ......................... Работа с цветами
│   ├── font/ .......................... Шрифты
│   ├── interfaces/ .................... Главный интерфейс
│   └── particle/ ...................... Частицы
├── systems/ ........................... Системы и утилиты
│   ├── FileHandler.java ............... Работа с файлами
│   ├── SharedClass.java ............... Глобальные хелперы
│   ├── Generator.java ................. Генератор данных
│   ├── command/ ....................... Система команд в чате
│   ├── config/ ........................ Конфиги (JSON)
│   ├── draggable/ ..................... Перемещаемые элементы
│   ├── macros/ ........................ Макросы (автоматизация)
│   ├── partner/ ....................... Система друзей
│   ├── setting/ ....................... Настройки модулей
│   ├── theme/ ......................... Темы оформления
│   ├── rotations/ ..................... Система вращения (head, body)
│   └── helpers/ ....................... Вспомогательные утилиты
├── events/ ............................ Event система
│   ├── Event.java ..................... Базовый класс события
│   ├── EventLogic.java ................ Интерфейс обработки
│   ├── EventCollects.java ............. Регистрация слушателей
│   └── impl/
│       ├── input/ ..................... События клавиатуры/мышки
│       ├── player/ .................... События игрока
│       ├── world/ ..................... События мира
│       └── network/ ................... События пакетов
├── primary/ ........................... Базовые классы
│   ├── IHolder.java ................... Интерфейс holder (mc instance)
│   ├── module/
│   │   └── ModuleHandler.java ......... Базовый handler модулей
│   └── time/
│       └── TimerHandler.java ......... Таймер для модулей
├── addition/ .......................... Дополнения
│   └── ProfileHandler.java ............ Профиль клиента
└── mixin/ ............................ Миксины Fabric (внедрение в MC)
```

---

## 🔥 КЛЮЧЕВЫЕ КОМПОНЕНТЫ И ИХ ФУНКЦИИ

### 1️⃣ **Minced.java** - ГЛАВНЫЙ МОЗГ 🙏🙏🙏

**Что это:**
- Синглтон (одна копия на весь клиент)
- Точка входа при загрузке мода
- Управляет всеми системами

**Основные функции:**

```java
// Получить инстанс
Minced getInstance()

// Инициализация при запуске
void onInitialize()

// Сохранение данных (configs, modules)
JsonObject saveData()

// Загрузка данных из JSON
void loadData(JsonObject)

// Проверка версии клиента
void verifyVersion()

// Геттеры для всех систем
ModuleManager getModuleHandler()
ConfigHandler getConfigHandler()
CommandHandler getCommandHandler()
ThemeHandler getThemeHandler()
MacrosHandler getMacrosHandler()
// ... и т.д.
```

**Где находится:**
```
src/main/java/free/minced/Minced.java
```

---

### 2️⃣ **Module.java** - БАЗОВЫЙ КЛАСС МОДУЛЕЙ 💔💔💔

**Что это:**
- Суперкласс для ВСЕХ модулей (боевых, движения, рендера и т.д.)
- Каждый модуль наследует от этого класса
- Предоставляет базовый функционал

**Основные свойства:**

```java
String name                     // Название модуля
int key                         // Кнопка активации (GLFW код)
boolean enabled                 // Включен ли модуль
ModuleCategory category         // Категория (COMBAT, MOVEMENT, RENDER и т.д.)
boolean hidden                  // Скрыт ли в UI
List<Setting> settings          // Настройки модуля
Animation animation             // Анимация включения/отключения
```

**Основные методы:**

```java
// Управление состоянием
void setEnabled(boolean)        // Включить/отключить
void toggle()                   // Переключить состояние
void enable()                   // Включить
void disable()                  // Отключить

// Жизненный цикл модуля
void onEnable()                 // Срабатывает при включении
void onDisable()                // Срабатывает при отключении
void onEvent(Event event)       // Получить событие

// Сохранение/загрузка
JsonObject save()               // Сохранить в JSON
void load(JsonObject)           // Загрузить из JSON
```

**Как создать новый модуль:**

```java
@ModuleDescriptor(
    name = "MyModule",
    category = ModuleCategory.COMBAT,
    key = GLFW.GLFW_KEY_K  // Кнопка активации
)
public class MyModule extends Module {
    
    @Setting(name = "Speed")
    public double speed = 1.0;
    
    @Override
    public void onEnable() {
        super.onEnable();
        // Действия при включении
        EventCollects.registerListener(this);
    }
    
    @Override
    public void onDisable() {
        super.onDisable();
        // Действия при отключении
    }
    
    @Override
    public void onEvent(Event event) {
        if (event instanceof EventSync syncEvent) {
            // Обработка события
        }
    }
}
```

**Где находится:**
```
src/main/java/free/minced/modules/Module.java
```

---

### 3️⃣ **EVENT СИСТЕМА** 🔥🔥🔥

**Что это:**
- Сеть событий для отзывчивости модулей
- Срабатывают при определённых действиях
- Все модули слушают события

**Основные события:**

```java
// Ввод (клавиатура, мышка)
InputEvent(int type, int key)

// Синхронизация с сервером
EventSync

// Пакеты (сеть)
PacketEvent.Send      // Отправка пакета
PacketEvent.Receive   // Получение пакета

// Рендер
RenderEvent

// Движение игрока
MoveEvent
StrideEvent
```

**Как использовать событие:**

```java
@Override
public void onEvent(Event event) {
    // Проверяем тип события
    if (event instanceof EventSync) {
        // Синхронизация произошла
        doSomething();
    }
    
    if (event instanceof PacketEvent.Send sendEvent) {
        Packet<?> packet = sendEvent.getPacket();
        if (packet instanceof UpdateSelectedSlotC2SPacket) {
            // Обработка пакета выбора слота
        }
    }
}
```

**Как вызвать событие:**

```java
EventCollects.call(new MyEvent(...));
```

**Где находится:**
```
src/main/java/free/minced/events/
├── Event.java
├── EventLogic.java
├── EventCollects.java
└── impl/
    ├── input/
    ├── player/
    ├── world/
    └── network/
```

---

### 4️⃣ **КАТЕГОРИИ МОДУЛЕЙ** 💔

#### **БОЕВЫЕ (Combat)** - Атака и защита

```
AntiBot          ► Определяет ботов
AttackAura       ► Автоматическая атака врагов в радиусе
TPAura           ► Телепортационная аура (быстрые удары)
AutoAttack       ► Автоклик по врагам
AutoTotem        ► Держит тотем смерти в руке
AutoPotion       ► Пьёт зелья при урона
AutoSwap         ► Меняет оружие автоматически
BackTrack        ► Отслеживает позиции врага (от лага)
Reach            ► Увеличивает дальность удара
HitBox           ► Увеличивает хитбокс врагов
Velocity         ► Снижает урон от ударов
```

#### **ДВИЖЕНИЕ (Movement)** - Передвижение и спринт

```
Flight           ► Полёт в выживании
NoClip           ► Проходить сквозь блоки
Speed            ► Ускорение движения
Strafe           ► Управление в воздухе
Scaffold         ► Автоматическое строительство блоков
AutoSprint       ► Автоматический спринт
CreativeFlight   ► Полёт как в креативе
ElytraBounce     ► Прыгание на элитре
NoSlowDown       ► Убирает замедление с мечом
NoDelay          ► Убирает задержку атаки (60ms)
TimerModule      ► Ускорение/замедление времени
```

#### **РЕНДЕР (Render)** - Визуальные эффекты

```
EntityESP        ► Видно врагов сквозь стены (боксы)
TargetESP        ► Подсвет текущей цели
BlowParticles    ► Частицы при ударах
Trails           ► След позади игрока
Trajectories     ► Траектория снарядов
WorldParticles   ► Кастомные частицы мира
SwingAnimations  ► Кастомные анимации атак
ViewModel        ► Изменение первого лица оружия
Arrows           ► Кастомные стрелки
FullBright       ► Видимость в тёмноте
NoRender         ► Убирает туман, дождь
CustomWorld      ► Кастомный мир
ItemPhysic       ► Физика предметов
```

#### **ДИСПЛЕЙ (Display)** - Интерфейс

```
ClickGUI         ► Главный интерфейс управления модулями
HUD              ► Информационные панели на экране
```

---

### 5️⃣ **СИСТЕМЫ (SYSTEMS)** 🙏🙏🙏

#### **FileHandler** - Работа с файлами

```java
// Инициализирует директории
void initDirectory()

// Создаёт папки:
// - .minecraft/mods/invincible/configs/
// - .minecraft/mods/invincible/profiles/
```

#### **SharedClass** - Глобальные утилиты

```java
// Статические помощники и константы
static ExecutorService executor          // Многопоточность
static int ticksElytraFlying             // Тики полёта на элитре
static boolean lockSprint                // Блокировка спринта
static BlockPos GPS_POSITION             // GPS позиция

// Методы
onMouseKeyPressed(int button)            // Нажатие мышки
onMouseKeyReleased(int button)           // Отпускание мышки
keyPress(int key)                        // Нажатие клавиши
onSyncWithServer(PacketEvent)            // Синхронизация пакетов
```

#### **ConfigHandler** - Конфигурация

```java
// Сохранение конфига
void save(String configName)

// Загрузка конфига
void load(String configName)

// Список сохранённых конфигов
List<File> getConfigs()
```

#### **CommandHandler** - Команды в чате

```java
// Регистрация команды
void register(String command, Consumer<String[]> handler)

// Пример использования в чате:
// .teleport x y z
// .noclip toggle
```

#### **MacrosHandler** - Макросы

```java
// Сохранять последовательность клавиш
void recordMacro(String name, List<Integer> keys)

// Воспроизводить макрос
void playMacro(String name)

// Привязать к кнопке
void bindMacro(String macroName, int key)
```

#### **ThemeHandler** - Темы оформления

```java
// Список тем
Theme.DARK, Theme.LIGHT, Theme.CUSTOM

PrimaryTheme.PRIMARY_1, PRIMARY_2, etc.

// Смена темы
void setTheme(Theme theme)
void setPrimaryTheme(PrimaryTheme primary)
```

#### **DraggableHandler** - Перемещаемые элементы

```java
// Элементы UI которые можно тащить мышкой
Map<String, Draggable> draggables

// Сохранение позиций
void save()
void load()
```

#### **PartnerHandler** - Система друзей

```java
// Добавить друга
void addFriend(String name)

// Удалить друга
void removeFriend(String name)

// Получить список
List<String> getFriends()
```

---

### 6️⃣ **FRAMEWORK (UI/РЕНДЕР)** 🔥

#### **Animation** - Плавные переходы

```java
Animation animation = new Animation(Easing.EASE_IN_OUT_CUBIC, 600);

// Получить значение 0.0 - 1.0
double progress = animation.getProgress();

// Типы easing:
Easing.LINEAR
Easing.EASE_IN
Easing.EASE_OUT
Easing.EASE_IN_OUT_CUBIC
Easing.EASE_OUT_ELASTIC
```

#### **Render** - Отрисовка

```java
// Рисование прямоугольника
RenderSystem.drawRect(x, y, width, height, color)

// Рисование текста
FontRenderer.drawString(text, x, y, color)

// Рисование боксов (ESP)
RenderHelper.drawBox(Vec3d, color, thickness)

// Шейдеры для эффектов
ShaderHandler.initShaders()
```

#### **Buttons** - Кнопки интерфейса

```java
// Кнопка с функцией
Button button = new Button(x, y, width, height, label);
button.onClick(() -> {
    // Действие при клике
});
```

#### **Color** - Работа с цветами

```java
// RGB цвет
new Color(255, 100, 50)

// С альфа-каналом
new Color(255, 100, 50, 200)

// HEX
Color.decode("#FF6432")
```

#### **Font** - Шрифты

```java
// Использование шрифта
fontRenderer.drawString("Text", x, y, color)

// Размер шрифта
font.getWidth("Text")
font.getHeight()
```

---

### 7️⃣ **ModuleManager** - Управление модулями 💔💔💔

```java
// Получить модуль по классу
ModuleManager.get(AttackAura.class)

// Получить все модули категории
List<Module> combatModules = 
    ModuleManager.getModulesByCategory(ModuleCategory.COMBAT)

// Включить/отключить модуль
module.setEnabled(true)

// Получить все модули
List<Module> allModules = ModuleManager.getModules()

// При нажатии клавиши
ModuleManager.onKeyPress(key)

// Сохранить все модули
ModuleManager.save()

// Загрузить все модули
ModuleManager.load()
```

---

## 📊 ЖИЗНЕННЫЙ ЦИКЛ МОДУЛЯ

```
1. ЗАГРУЗКА МОДА
   ↓
2. Minced.onInitialize()
   ├─ ModuleManager.initModules()
   │  └─ Все модули создаются и регистрируются
   ├─ ConfigHandler загружает конфиги
   └─ Модули включаются в зависимости от конфига
   ↓
3. ИГРА РАБОТАЕТ
   ├─ События срабатывают (EventSync, InputEvent, и т.д.)
   ├─ Модули обрабатывают события (onEvent)
   ├─ Модули обновляются каждый тик
   └─ UI рендерится на экране
   ↓
4. ЗАКРЫТИЕ ИГРЫ
   ├─ Minced.saveData()
   ├─ ConfigHandler.save() - сохраняются все настройки
   └─ Все модули отключаются (onDisable)
```

---

## 🎯 КАК ДОБАВИТЬ НОВЫЙ МОДУЛЬ

### Шаг 1: Создать файл

```
src/main/java/free/minced/modules/impl/combat/MyNewModule.java
```

### Шаг 2: Написать класс

```java
package free.minced.modules.impl.combat;

import free.minced.modules.Module;
import free.minced.modules.api.ModuleCategory;
import free.minced.modules.api.ModuleDescriptor;
import free.minced.events.Event;
import free.minced.events.impl.player.EventSync;
import org.lwjgl.glfw.GLFW;

@ModuleDescriptor(
    name = "MyModule",
    description = "Описание функции",
    category = ModuleCategory.COMBAT,
    key = GLFW.GLFW_KEY_M
)
public class MyNewModule extends Module {
    
    // НАСТРОЙКИ МОДУЛЯ
    private double speed = 1.0;
    private boolean enabled = false;
    
    @Override
    public void onEnable() {
        super.onEnable();
        System.out.println("Модуль включен!");
    }
    
    @Override
    public void onDisable() {
        super.onDisable();
        System.out.println("Модуль отключен!");
    }
    
    @Override
    public void onEvent(Event event) {
        if (event instanceof EventSync) {
            // Выполнять каждый тик игры
            doSomething();
        }
    }
    
    private void doSomething() {
        // Логика модуля
    }
}
```

### Шаг 3: Зарегистрировать

ModuleManager автоматически загружает все модули из папок, поэтому ничего дополнительно не нужно!

---

## 🔧 БЫСТРАЯ СПРАВКА ПО ЧАСТО ИСПОЛЬЗУЕМЫМ ВЕЩАМ

### Получить главный игрок

```java
MinecraftClient.getInstance().player
// или
((IHolder) this).mc.player
```

### Получить мир

```java
MinecraftClient.getInstance().world
```

### Отправить пакет

```java
MinecraftClient.getInstance().getNetworkHandler().sendPacket(new MyPacket(...))
```

### Написать сообщение в чат

```java
MinecraftClient.getInstance().inGameHud.getChatHud()
    .addMessage(Text.of("Сообщение"))
```

### Проверить что модуль включен

```java
if (Minced.getInstance().getModuleHandler()
    .get(AttackAura.class).isEnabled()) {
    // Модуль включен
}
```

### Вызвать событие

```java
EventCollects.call(new MyEvent(...))
```

### Создать таймер

```java
TimerHandler timer = new TimerHandler();
if (timer.hasReached(1000)) {  // Каждые 1000ms
    doSomething();
    timer.reset();
}
```

---

## 📁 ФАЙЛОВАЯ СТРУКТУРА КОНФИГОВ

```
.minecraft/
└── config/
    └── invincible_client/
        ├── configs/
        │   ├── default.json ........... Конфиг по умолчанию
        │   ├── pvp.json .............. Боевой конфиг
        │   └── builder.json ........... Строительный конфиг
        └── themes/
            ├── dark.json
            └── light.json
```

**Структура конфига:**

```json
{
  "theme": "DARK",
  "primaryTheme": "PRIMARY_1",
  "modules": {
    "AttackAura": {
      "enabled": true,
      "key": 65,
      "range": 5.5,
      "speed": 10
    },
    "Flight": {
      "enabled": false,
      "key": 70,
      "speed": 2.0
    }
  },
  "friends": ["Player1", "Player2"],
  "macros": {}
}
```

---

## 🎓 ЧЕКЛИСТ ДЛЯ СОЗДАНИЯ МОДУЛЯ

- ✅ Создал класс в правильной папке
- ✅ Наследует от `Module`
- ✅ Добавил аннотацию `@ModuleDescriptor`
- ✅ Указал категорию и кнопку
- ✅ Реализовал `onEnable()` и `onDisable()`
- ✅ Реализовал `onEvent(Event)`
- ✅ Добавил слушание нужных событий
- ✅ Протестировал включение/отключение

---

**Готово!** Теперь ты знаешь как навигироваться по коду! 🔥🙏🙏🙏💔💔💔😭😭😭
