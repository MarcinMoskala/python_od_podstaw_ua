# Зміна стану

> У цьому розділі ми&nbsp;визначимо поведінку "змійки" та&nbsp;її харчування.

Забудьмо на&nbsp;мить про PyGame та&nbsp;всі ці&nbsp;кола і квадрати. Попрацюймо як&nbsp;професійні розробники і запрограмуймо поведінку нашої гри, не&nbsp;думаючи про те, як&nbsp;вона виглядає. Наша гра може бути представлена такими змінними:

- `snake` — список, який зберігає положення елементів "змійки" (де перший елемент — це&nbsp;"хвіст", а останній — "голова");
- `food` — позиція "їжі;
- `direction` — напрямок руху "змійки";
- `field_size` — розмір поля.

У відповідь на&nbsp;дії користувача змінюватиметься лише&nbsp;`direction`. Все інше змінюватиметься ніби саме собою. На&nbsp;кожному кроці (скажімо, що&nbsp;100 мс) "змійка" пересуватиметься у&nbsp;заданому напрямку. Якщо вона натрапить на&nbsp;"їжу", вона подовжиться, а нова "їжа" з’явиться на&nbsp;полі у&nbsp;випадковому місці. Якщо вона натрапить на&nbsp;власний "хвіст", ми&nbsp;повернемося до&nbsp;вихідної точки. Так працюватиме наша "змійка", і це&nbsp;ми&nbsp;реалізуємо в&nbsp;цьому розділі.

Забудьмо на&nbsp;мить про те, що&nbsp;ми&nbsp;вже написали, і створімо абсолютно новий файл під назвою&nbsp;`game_state.py`. Всередині нього ми&nbsp;створимо клас&nbsp;`GameState`, який буде представляти стан нашої гри. Отже, він повинен містити&nbsp;`snake`,&nbsp;`food`,&nbsp;`direction` і&nbsp;`field_size`.
int
```python
class GameState:
    def __init__(self,
                 snake,
                 direction,
                 food,
                 field_size):
        self.snake = snake
        self.direction = direction
        self.food = food
        self.field_size = field_size
```

`direction` ми&nbsp;представлятимемо послідовними номерами, які відповідатимуть можливим напрямкам. Однією з&nbsp;практик є зберігання таких чисел у&nbsp;спеціальному класі, де&nbsp;всі атрибути є статичними [^302_1]. Викладемо його в&nbsp;окремому файлі `direction.py`.

```python
class Direction:
    UP = 1
    DOWN = 2
    LEFT = 3
    RIGHT = 4
```

Повертаючись до&nbsp;класу `GameState`, нам потрібен метод `step`, який послідовно робитиме все, що&nbsp;має статися в&nbsp;певний час. Але як&nbsp;ми&nbsp;дізнаємося, чи&nbsp;наша реалізація цієї функції правильна? На це&nbsp;запитання відповідь дасть модульне тестування.

## Модульне тестування

Однією з&nbsp;найважливіших концепцій сучасного програмування є модульне тестування. Воно полягає у&nbsp;написанні функцій, які перевіряють, чи написаний нами код правильний. У тестах ми&nbsp;зазвичай спочатку показуємо початковий стан, потім — яку дію було виконано, і нарешті перевіряємо, чи відбулася очікувана зміна стану. Наприклад, початковим станом може бути "змійка" у&nbsp;певному місці, яка рухається у&nbsp;певному напрямку, дією — виклик функції `step`, а перевіркою — оцінка, чи правильне нове положення "змійки".

Ось приклад модульного тестування, яке могло б опинитися в&nbsp;методі тестового класу:

```python
# визначення початкового стану
state = GameState(
    snake=[
        Position(4, 0),
        Position(4, 1),
        Position(4, 2)
    ],
    direction=Direction.DOWN,
    food=Position(10, 10),
    field_size=20
)

# виклик функції, яка тестується
state.step()

#  перевірка результату роботи функції
expected_state = [
    Position(4, 1),
    Position(4, 2),
    Position(4, 3)
]
self.assertEqual(expected_state, state.snake)
```

![Точка (4, 2) та&nbsp;її околиці.](301_coordinates_plus.jpg)

Для модульного тестування ми&nbsp;будемо використовувати пакет `unittest`. Він вимагає, щоб наші тести були методами класу із назвами, які починаються від "test_". У великих проєктах створюється багато класів із тестами, нам достатньо буде одного. Для початку ми&nbsp;напишемо дуже простий тест, який перевіряє, чи працює належним чином функція `upper`. Він використовує метод `assertEqual` на&nbsp;`self`, який порівнює два об’єкти та, у&nbsp;випадку, якщо вони не&nbsp;були ідентичні, виводить відповідну помилку. Тут ми&nbsp;використовуємо його для порівняння результату роботи функції та&nbsp;очікуваного значення. Якщо функція `upper` працює нормально, тест буде пройдено. Перевірмо. Нам потрібно створити файл `game_state_test.py` з&nbsp;таким змістом:

```python
import unittest


class GameStateTest(unittest.TestCase):

    def test_example(self):
        self.assertEqual('foo'.upper(), 'FOO')
```

> Щоб&nbsp;тест працював, після назви класу потрібно використати дужки зі значенням `unittest.TestCase`. Це функціонал, відомий як&nbsp;успадкування. Крім того, методи, які містять тести, повинні починатися з&nbsp;`test_`.

Найпростіше розпочати наш тест з&nbsp;зеленого трикутника ліворуч від класу.

{width: 80%}
![](302_unit.png)

{width: 80%}
![](302_unit_2.png)

{width: 80%}
![](302_unit_3.png)

Якщо кнопка запуску тестування не&nbsp;відображається, можна запустити його з&nbsp;коду за допомогою функції `unittest.main()`.

```python
if __name__ == '__main__':
    unittest.main()
```

Щоб переконатися, що&nbsp;наш тест працює, спробуємо змінити один із рядків. Оскільки після цієї зміни тест буде написаний неправильно, він має закінчитися помилкою. Провалений тест буде чітко позначений у&nbsp;списку тестів. Також метод `assertEqual` має показувати деталі переданих йому значень, що&nbsp;зазвичай допомагає вивчити причини провалу тесту.

Ми вже знаємо, як&nbsp;перевірити написаний код. У наступних кроках я буду використовувати техніку, відому як&nbsp;TDD (test-driven development). Вона полягає в&nbsp;тому, що&nbsp;перед впровадженням функціоналу ми&nbsp;спочатку писатимемо тест для перевірки цієї зміни. Після написання нового тесту ми&nbsp;запускаємо всі тести, щоб переконатися, що&nbsp;новий тест не&nbsp;пройде (оскільки функціонал, який він має перевіряти, ще не&nbsp;написаний). Потім ми&nbsp;починаємо працювати над новим функціоналом і перевіряємо статус роботи, знову запускаючи тести. Запускаючи всі тести, а не&nbsp;лише той, який стосується нового функціоналу, ми&nbsp;не тільки дізнаємося, чи новий функціонал працює належним чином, але й чи не&nbsp;зіпсували ми&nbsp;щось, що&nbsp;працювало раніше. Це як&nbsp;скелелазіння зі страховкою.

{width: 80%}
![](302_unit_4.png)

## "Змійка" рухається

На кожному кроці гри наша "змійка" повинна рухатися в&nbsp;тому напрямку, в&nbsp;якому її&nbsp;направляє користувач. Як це&nbsp;перевірити? Достатньо помістити "змійку" в&nbsp;будь-якому місці, направленою у&nbsp;певному напрямку, та&nbsp;перевірити, чи справді вона рухається відповідно до очікувань. Приклади тестів руху ліворуч і праворуч можуть виглядати так:

```python
import unittest
from game_state import GameState
from position import Position
from direction import Direction


class GameStateTest(unittest.TestCase):

    def test_snake_should_move_right(self):
        state = GameState(
            snake=[
                Position(1, 2),
                Position(1, 3),
                Position(1, 4)
            ],
            direction=Direction.RIGHT,
            food=Position(10, 10),
            field_size=20
        )

        state.step()

        expected_state = [
            Position(1, 3),
            Position(1, 4),
            Position(2, 4)
        ]
        self.assertEqual(expected_state, state.snake)

    def test_snake_should_move_left(self):
        state = GameState(
            snake=[
                Position(1, 2),
                Position(1, 3),
                Position(1, 4)
            ],
            direction=Direction.LEFT,
            food=Position(10, 10),
            field_size=20
        )

        state.step()

        expected_state = [
            Position(1, 3),
            Position(1, 4),
            Position(0, 4)
        ]
        self.assertEqual(expected_state, state.snake)
```

Після написання тестів спершу запусти їх, щоб побачити, чи вони працюють, і як&nbsp;виглядатимуть результати. Завдяки особливостям `__str__` і `__repr__` в&nbsp;об’єкті `Position` ми&nbsp;повинні отримати чітке порівняння, як&nbsp;виглядає "змійка" під час&nbsp;перевірки, та&nbsp;як&nbsp;вона повинна виглядати.

![](302_test_fail.png)

> Тести пересування вгору та&nbsp;вниз напиши самостійно.

Як це&nbsp;реалізувати? Поміркуй, що&nbsp;відбувається з&nbsp;частинами нашої "змійки". Кінчик "хвоста" (тобто перший елемент "змійки") зникає. Це можна реалізувати, замінивши "змійку" на&nbsp;її позиції від 1 до кінця. Таким чином ми&nbsp;виключаємо один — перший — елемент і позбавляємося від кінчика "хвоста".

```python
self.snake = self.snake[1:]
```

З іншого боку, на&nbsp;початку з’являється новий елемент. Його положення, однак, залежить від напрямку руху "змійки". Ми&nbsp;можемо додати елемент за допомогою функції `append`, а його положення ми&nbsp;визначимо в&nbsp;окремій функції `next_head`.

```python
new_head = self.next_head(self.direction)
self.snake.append(new_head)
```

Функція `next_head` має повертати нове положення "голови" "змійки". Тому потрібно буде визначити поточне положення "голови". Це останній елемент у&nbsp;змінній `snake`, тобто `snake[-1]`. Потім ми&nbsp;повинні створити позицію, зміщену в&nbsp;заданому напрямку. Погляньмо ще раз на&nbsp;сітку координат.

![Точка (4, 2) та&nbsp;її околиці.](301_coordinates_plus.jpg)

Якщо нове положення "голови" має бути вгорі, то від змінної `y` ми&nbsp;повинні відняти 1. Якщо внизу — додати 1. Якщо ліворуч, то від змінної `x` ми&nbsp;повинні відняти 1. Якщо праворуч — додати 1. Ми&nbsp;можемо реалізувати це, використовуючи конструкцію if з&nbsp;elif.

```python
# Метод у класі GameState
def next_head(self, direction):
    pos = self.snake[-1]
    if direction == Direction.UP:
        return Position(pos.x, pos.y — 1)
    elif direction == Direction.DOWN:
        return Position(pos.x, pos.y + 1)
    elif direction == Direction.RIGHT:
        return Position(pos.x + 1, pos.y)
    elif direction == Direction.LEFT:
        return Position(pos.x — 1, pos.y)
```

> Ця функція, в&nbsp;принципі, може мати власні тести. Однак це&nbsp;не&nbsp;обов’язково, оскільки ми&nbsp;опосередковано перевіряємо її&nbsp;роботу під час&nbsp;тестування руху "змійки".

Ми будемо використовувати цей метод у&nbsp;`step`.

```python
# Метод у класі GameState
def step(self):
    new_head = self.next_head(self.direction)
    self.snake.append(new_head)
    self.snake = self.snake[1:]
```

Тепер усі наші тести мають бути успішно пройдені. Перевір це. Однак ми&nbsp;ще не&nbsp;вичерпали тему руху "змійки": що&nbsp;станеться, якщо ми&nbsp;дійдемо до кінця поля?

### Вихід за межі поля

У класичній "змійці", коли ми&nbsp;врізалися у&nbsp;край поля, гра або закінчується, або "змійка" виходить з&nbsp;іншого боку поля. Я вважаю другий варіант цікавішим, тому ми&nbsp;реалізуємо в&nbsp;нашій грі його.

Але почнемо з&nbsp;тестування. Щоб&nbsp;перевірити, чи "змійка" вийде з&nbsp;іншого боку, помістімо її&nbsp;в&nbsp;самий кінець поля і додамо крок. Далі перевіримо, чи "голова" "змійки" вийшла на&nbsp;іншому боці.

```python
# Нові методи у класі GameStateTest
def test_snake_should_move_up_on_top(self):
    state = GameState(
        snake=[
            Position(2, 2),
            Position(2, 1),
            Position(2, 0)
        ],
        direction=Direction.UP,
        food=Position(10, 10),
        field_size=20
    )

    state.step()

    expected_state = [
        Position(2, 1),
        Position(2, 0),
        Position(2, 19)
    ]
    self.assertEqual(expected_state, state.snake)


def test_snake_should_move_right_on_edge(self):
    state = GameState(
        snake=[
            Position(17, 1),
            Position(18, 1),
            Position(19, 1)
        ],
        direction=Direction.RIGHT,
        food=Position(10, 10),
        field_size=20
    )

    state.step()

    expected_state = [
        Position(18, 1),
        Position(19, 1),
        Position(0, 1)
    ]
    self.assertEqual(expected_state, state.snake)
```

> Тестування проходження через нижню та&nbsp;ліву стінки напиши самостійно.

Для того, щоб наш код працював, нам загалом потрібна лише невелика зміна під час&nbsp;визначення нових координат у&nbsp;функції `next_head`. Якщо після зміни будь-яка точка координат може становити -1, то замість цього вона має бути `field_size — 1`, тобто за замовчуванням 19. Якби вона мала 20, то становила би 0. Пам’ятаєш оператор залишку від ділення `%`? Щоб&nbsp;нагадати, як&nbsp;він працює: `123 % 5` буде `3`, оскільки найбільша кратність числа `5`, менша ніж `123` — `120`. Тож залишається `3` як&nbsp;остача від ділення. Що важливо, `20 % 20 == 0`, а `-1 % 20 == 19`. Цей оператор ідеально відповідає нашим потребам. Після додавання або віднімання від позиції `x` або `y` ми&nbsp;повинні виконати операцію остачі від ділення на&nbsp;`field_size`.

```python
# Метод у класі GameState
def next_head(self, direction):
    pos = self.snake[-1]
    if direction == Direction.UP:
        return Position(
            pos.x,
            (pos.y — 1) % self.field_size
        )
    elif direction == Direction.DOWN:
        return Position(
            pos.x,
            (pos.y + 1) % self.field_size
        )
    elif direction == Direction.RIGHT:
        return Position(
            (pos.x + 1) % self.field_size,
            pos.y
        )
    elif direction == Direction.LEFT:
        return Position(
            (pos.x — 1) % self.field_size,
            pos.y
        )
```

Після цього всі наші тести мають бути пройдені. Запрошую переконатися в&nbsp;цьому самостійно.

## З’їдання кульок

Коли "змійка" з’їдає кульку, вона повинна збільшитися, а нова "їжа" — з’явитися у&nbsp;випадковому місці на&nbsp;полі. Почнімо з&nbsp;першої частини цього завдання. Якщо ми&nbsp;поставимо "змійку" так, що&nbsp;на&nbsp;наступному кроці вона дістається до "їжі", то після виклику функції `step`, вона має збільшуватися. Ми&nbsp;могли би перевірити це, порівнявши попередню та&nbsp;нову довжину "змійки", але ще точніше буде перевірити, чи новий стан змінної `snake` відповідає нашим очікуванням.

```python
# Новий метод у класі GameStateTest
def test_snake_eats_food(self):
    state = GameState(
        snake=[
            Position(1, 2),
            Position(2, 2),
            Position(3, 2)
        ],
        direction=Direction.UP,
        food=Position(3, 1),
        field_size=20
    )

    state.step()

    expected_state = [
        Position(1, 2),
        Position(2, 2),
        Position(3, 2),
        Position(3, 1)
    ]
    self.assertEqual(expected_state, state.snake)
```

Зауваж, що&nbsp;єдина зміна, порівняно з&nbsp;попередніми кроками, полягає у&nbsp;тому, що&nbsp;ми&nbsp;не позбавляємося "хвоста" "змійки". Тобто "змійка" подовжується: "голова" додається, а "хвіст" залишається незмінним. Отже, ми&nbsp;повинні позбуватися кінчика "хвоста", лише тоді, коли нове положення "голови" не&nbsp;збігається з&nbsp;розташуванням "їжі".

```python
# Метод у класі GameState
def step(self):
    new_head = self.next_head(self.direction)
    self.snake.append(new_head)
    found_food = new_head == self.food
    if not found_food:
        self.snake = self.snake[1:]
```

Після цієї зміни всі наші тести знову мають бути пройдені. Настав час&nbsp;поставити "їжу" в&nbsp;нове положення. Перевірити вибір випадкового розташування дуже складно, тому зупинімося на&nbsp;перевірці того, що&nbsp;після того, як&nbsp;"голова" "змійки" опиняється на&nbsp;позиції "їжі", з’являється нова порція "їжі", а її&nbsp;положення не&nbsp;збігається з&nbsp;розташуванням "змійки". Для цього достатньо додати наприкінці написаного нами тесту такий тест:

```python
self.assertEqual(False, state.food in state.snake)
```

Як це&nbsp;реалізувати? На останньому кроці ми&nbsp;вже навчилися перевіряти, чи знайшла "змійка" "їжу". Якщо вона її&nbsp;знайшла, ми&nbsp;повинні задати "їжі" нове положення. Ми&nbsp;можемо об’єднати це&nbsp;з&nbsp;попередньо написаною умовою в&nbsp;одну інструкцію if із блоком else. Нове розташування "їжі" ми&nbsp;задамо в&nbsp;окремій функції `set_random_food_position`.

```python
# Метод у класі GameState
def step(self):
    new_head = self.next_head(self.direction)

    self.snake.append(new_head)

    if new_head == self.food:
        self.set_random_food_position()
    else:
        self.snake = self.snake[1:]
```

У розділі *Організація проєкту та&nbsp;імпорт* ми&nbsp;познайомилися з&nbsp;пакетом `random` та&nbsp;його функцією `randint`, яка дозволяє отримувати випадкове ціле число в&nbsp;певному діапазоні. В якості аргументів ми&nbsp;задамо мінімальне і максимальне значення. Мінімальне значення `x` та&nbsp;`y` має бути 0, а максимальне — `field_size — 1`. Отже, просте визначення випадкового значення може виглядати так:

```python
from random import randint


# Новий метод у класі GameState
def set_random_food_position(self):
    self.food = Position(
        randint(0, self.field_size — 1),
        randint(0, self.field_size — 1)
    )
```

Втім, оскільки це&nbsp;абсолютно випадкове розташування, у&nbsp;нас немає гарантії, що&nbsp;нова порція "їжі" не&nbsp;опиниться всередині "змійки". Є кілька способів вирішити цю проблему. Найпростіший із них — перевірити, чи позиція "їжі" правильна, і провести новий вибір випадкових координат, якщо ні. Для цього ми&nbsp;можемо використовувати цикл `while`, але можна також просто викликати функцію знову:

```python
# Метол у класі GameState
def set_random_food_position(self):
    self.food = Position(
        randint(0, self.field_size — 1),
        randint(0, self.field_size — 1)
    )
    if self.food in self.snake:
        self.set_random_food_position()


# або
def set_random_food_position(self):
    search = True
    while search:
        self.food = Position(
            randint(0, self.field_size — 1),
            randint(0, self.field_size — 1)
        )
        search = self.food in self.snake
```

Після визначення цієї функції наші тести повинні бути пройдені знову. "Змійка" вже може рухатися та&nbsp;їсти "їжу", яка після цього з’являтиметься у&nbsp;випадковому місці. Справжній рай, чого ще хотіти. На жаль, "змійка" може з’їсти заборонений плід. Тоді вона має померти.

## Смерть "змійки"

Коли "змійка" наступає на&nbsp;власний "хвіст", вона повинна повернутися до початкового стану (і так по&nbsp;колу). Поки що&nbsp;ми&nbsp;не визначили початкову позицію чи напрямок, але, думаю, настав час&nbsp;це зробити. Я визначу їх як&nbsp;змінну у&nbsp;файлі `game_state.py`.

```python
# Нові змінні у файлі game_state.py
INITIAL_SNAKE = [
    Position(1, 2),
    Position(2, 2),
    Position(3, 2)
]
INITIAL_DIRECTION = Direction.RIGHT
```

У тесті ми&nbsp;можемо імпортувати ці значення, щоб перевірити, чи були вони використані після зіткнення "змійки" з&nbsp;власним "хвостом". До&nbsp;цього я додам перевірку, чи "їжа" не&nbsp;розміщується всередині "змійки", та&nbsp;чи випадково не&nbsp;змінився розмір поля (хоча це&nbsp;не&nbsp;обов’язково).

```python
# Новий метод у класі GameStateTest
def test_snake_dies(self):
    state = GameState(
        snake=[
            Position(1, 2),
            Position(2, 2),
            Position(3, 2),
            Position(3, 3),
            Position(2, 3),
        ],
        direction=Direction.UP,
        food=Position(3, 1),
        field_size=25
    )

    state.step()

    from game_state import INITIAL_SNAKE
    self.assertEqual(INITIAL_SNAKE, state.snake)
    self.assertFalse(state.food in state.snake)
    from game_state import INITIAL_DIRECTION
    self.assertEqual(INITIAL_DIRECTION, state.direction)
    self.assertEqual(25, state.field_size)
```

> Імпорт значень `INITIAL_SNAKE` та&nbsp;`INITIAL_DIRECTION` можна перемістити на&nbsp;початок файлу, але, якщо значення потрібне лише в&nbsp;одному місці, рекомендується розміщувати імпорт перед його єдиним використанням. Імпорт створює об’єкт, тому найголовніше, щоб він був запущений перед його першим використанням.

Перше, що&nbsp;нам потрібно, це&nbsp;перевірити, чи було зіткнення. Як це&nbsp;зробити? У функції `step` ми&nbsp;вже знаємо нове положення "голови" і положення всіх частин "змійки". Тепер достатньо перевірити, чи ця нова позиція "голови" не&nbsp;збігається з&nbsp;позицією жодної іншої частини "змійки".

```python
# Всередині метода step
collision = new_head in self.snake
```

Якщо так, то сталося зіткнення. У цьому випадку ми&nbsp;повинні повернути "змійку" в&nbsp;початкове положення і відмовитися від решти етапів руху. Ключовим словом `return` ми&nbsp;можемо завершити виклик функції `step` на&nbsp;цьому кроці.

```python
# Метод у класі GameState
def step(self):
    new_head = self.next_head(self.direction)

    collision = new_head in self.snake
    if collision:
        self.set_initial_position()
        return

    self.snake.append(new_head)

    if new_head == self.food:
        self.set_random_food_position()
    else:
        self.snake = self.snake[1:]
```

Залишилося реалізувати `set_initial_position`. Ця функція повинна повернути "змійку" у&nbsp;вихідне положення, задати початковий напрямок і вибрати нове випадкове місце для "їжі". При розміщенні "змійки" за допомогою `INITIAL SNAKE` варто зробити копію через `[:]`, щоб захистити цю змінну від внутрішніх змін. Без цього наш виклик `append` у&nbsp;`step` міг би змінити `INITIAL_SNAKE`, що&nbsp;призвело б до неправильного скидання гри.

```python
# Новий метод у класі GameState
def set_initial_position(self):
    self.snake = INITIAL_SNAKE[:]
    self.direction = INITIAL_DIRECTION
    self.set_random_food_position()
```

## Стан на&nbsp;кінець гри

Ми практично закінчили основну роботу: налаштували поведінку "змійки" і "їжі". Правильність виконання підтверджують тести. Але що&nbsp;з&nbsp;того, якщо ми&nbsp;досі не&nbsp;можемо зіграти у&nbsp;нашу гру. У наступному розділі ми&nbsp;використаємо написані нами класи і переконаємося, що&nbsp;в гру&nbsp;нарешті можна зіграти. Ось так повинен виглядати клас `GameState`:

```python
from position import Position
from direction import Direction
from random import randint

INITIAL_SNAKE = [
    Position(1, 2),
    Position(2, 2),
    Position(3, 2)
]
INITIAL_DIRECTION = Direction.RIGHT


class GameState:
    def __init__(self,
                 snake=None,
                 direction=INITIAL_DIRECTION,
                 food=None,
                 field_size=20):
        if snake is None:
            snake = INITIAL_SNAKE[:]
        self.snake = snake
        self.direction = direction
        self.field_size = field_size

        if food is None:
            self.set_random_food_position()
        else:
            self.food = food

    def set_initial_position(self):
        self.snake = INITIAL_SNAKE[:]
        self.direction = INITIAL_DIRECTION
        self.set_random_food_position()

    def next_head(self, direction):
        pos = self.snake[-1]
        if direction == Direction.UP:
            return Position(
                pos.x,
                (pos.y — 1) % self.field_size
            )
        elif direction == Direction.DOWN:
            return Position(
                pos.x,
                (pos.y + 1) % self.field_size
            )
        elif direction == Direction.RIGHT:
            return Position(
                (pos.x + 1) % self.field_size,
                pos.y
            )
        elif direction == Direction.LEFT:
            return Position(
                (pos.x — 1) % self.field_size,
                pos.y
            )

    def set_random_food_position(self):
        search = True
        while search:
            self.food = Position(
                randint(0, self.field_size — 1),
                randint(0, self.field_size — 1)
            )
            search = self.food in self.snake

    def can_turn(self, direction):
        new_head = self.next_head(direction)
        return new_head != self.snake[-2]

    def step(self):
        new_head = self.next_head(self.direction)

        collision = new_head in self.snake
        if collision:
            self.set_initial_position()
            return

        self.snake.append(new_head)

        if new_head == self.food:
            self.set_random_food_position()
        else:
            self.snake = self.snake[1:]
```

[^ 302_1]: Тобто вони прив’язані до класу, а не&nbsp;до об’єкта, як&nbsp;описано в&nbsp;розділі *Класи та&nbsp;об’єкти*.

