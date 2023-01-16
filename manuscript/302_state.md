# Зміна стану

> У цьому розділі ми визначимо поведінку "змійки" та її харчування.

Забудьмо на мить про PyGame та всі ці кола і квадрати. Попрацюймо як професійні розробники і запрограмуймо поведінку нашої гри, не думаючи про те, як вона виглядає. Наша гра може бути представлена такими змінними:

- `snake` — список, який зберігає положення елементів "змійки" (де перший елемент — це "хвіст", а останній — "голова");
- `food` — позиція "їжі;
- `direction` — напрямок руху "змійки";
- `field_size` — розмір поля.

У відповідь на дії користувача змінюватиметься лише `direction`. Все інше змінюватиметься ніби саме собою. На кожному кроці (скажімо, що 100 мс) "змійка" пересуватиметься у заданому напрямку. Якщо вона натрапить на "їжу", вона подовжиться, а нова "їжа" з’явиться на полі у випадковому місці. Якщо вона натрапить на власний "хвіст", ми повернемося до вихідної точки. Так працюватиме наша "змійка", і це ми реалізуємо в цьому розділі.

Забудьмо на мить про те, що ми вже написали, і створімо абсолютно новий файл під назвою `game_state.py`. Всередині нього ми створимо клас `GameState`, який буде представляти стан нашої гри. Отже, він повинен містити `snake`, `food`, `direction` і `field_size`.
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

`direction` ми представлятимемо послідовними номерами, які відповідатимуть можливим напрямкам. Однією з практик є зберігання таких чисел у спеціальному класі, де всі атрибути є статичними [^302_1]. Викладемо його в окремому файлі `direction.py`.

```python
class Direction:
    UP = 1
    DOWN = 2
    LEFT = 3
    RIGHT = 4
```

Повертаючись до класу `GameState`, нам потрібен метод `step`, який послідовно робитиме все, що має статися в певний час. Але як ми дізнаємося, чи наша реалізація цієї функції правильна? На це запитання відповідь дасть модульне тестування.

## Модульне тестування

Однією з найважливіших концепцій сучасного програмування є модульне тестування. Воно полягає у написанні функцій, які перевіряють, чи написаний нами код правильний. У тестах ми зазвичай спочатку показуємо початковий стан, потім — яку дію було виконано, і нарешті перевіряємо, чи відбулася очікувана зміна стану. Наприклад, початковим станом може бути "змійка" у певному місці, яка рухається у певному напрямку, дією — виклик функції `step`, а перевіркою — оцінка, чи правильне нове положення "змійки".

Ось приклад модульного тестування, яке могло б опинитися в методі тестового класу:

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

![Точка (4, 2) та її околиці.](301_coordinates_plus.jpg)

Для модульного тестування ми будемо використовувати пакет `unittest`. Він вимагає, щоб наші тести були методами класу із назвами, які починаються від "test_". У великих проєктах створюється багато класів із тестами, нам достатньо буде одного. Для початку ми напишемо дуже простий тест, який перевіряє, чи працює належним чином функція `upper`. Він використовує метод `assertEqual` на `self`, який порівнює два об’єкти та, у випадку, якщо вони не були ідентичні, виводить відповідну помилку. Тут ми використовуємо його для порівняння результату роботи функції та очікуваного значення. Якщо функція `upper` працює нормально, тест буде пройдено. Перевірмо. Нам потрібно створити файл `game_state_test.py` з таким змістом:

```python
import unittest


class GameStateTest(unittest.TestCase):

    def test_example(self):
        self.assertEqual('foo'.upper(), 'FOO')
```

> Щоб тест працював, після назви класу потрібно використати дужки зі значенням `unittest.TestCase`. Це функціонал, відомий як успадкування. Крім того, методи, які містять тести, повинні починатися з `test_`.

Найпростіше розпочати наш тест з зеленого трикутника ліворуч від класу.

{width: 80%}
![](302_unit.png)

{width: 80%}
![](302_unit_2.png)

{width: 80%}
![](302_unit_3.png)

Якщо кнопка запуску тестування не відображається, можна запустити його з коду за допомогою функції `unittest.main()`.

```python
if __name__ == '__main__':
    unittest.main()
```

Щоб переконатися, що наш тест працює, спробуємо змінити один із рядків. Оскільки після цієї зміни тест буде написаний неправильно, він має закінчитися помилкою. Провалений тест буде чітко позначений у списку тестів. Також метод `assertEqual` має показувати деталі переданих йому значень, що зазвичай допомагає вивчити причини провалу тесту.

Ми вже знаємо, як перевірити написаний код. У наступних кроках я буду використовувати техніку, відому як TDD (test-driven development). Вона полягає в тому, що перед впровадженням функціоналу ми спочатку писатимемо тест для перевірки цієї зміни. Після написання нового тесту ми запускаємо всі тести, щоб переконатися, що новий тест не пройде (оскільки функціонал, який він має перевіряти, ще не написаний). Потім ми починаємо працювати над новим функціоналом і перевіряємо статус роботи, знову запускаючи тести. Запускаючи всі тести, а не лише той, який стосується нового функціоналу, ми не тільки дізнаємося, чи новий функціонал працює належним чином, але й чи не зіпсували ми щось, що працювало раніше. Це як скелелазіння зі страховкою.

{width: 80%}
![](302_unit_4.png)

## "Змійка" рухається

На кожному кроці гри наша "змійка" повинна рухатися в тому напрямку, в якому її направляє користувач. Як це перевірити? Достатньо помістити "змійку" в будь-якому місці, направленою у певному напрямку, та перевірити, чи справді вона рухається відповідно до очікувань. Приклади тестів руху ліворуч і праворуч можуть виглядати так:

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

Після написання тестів спершу запусти їх, щоб побачити, чи вони працюють, і як виглядатимуть результати. Завдяки особливостям `__str__` і `__repr__` в об’єкті `Position` ми повинні отримати чітке порівняння, як виглядає "змійка" під час перевірки, та як вона повинна виглядати.

![](302_test_fail.png)

> Тести пересування вгору та вниз напиши самостійно.

Як це реалізувати? Поміркуй, що відбувається з частинами нашої "змійки". Кінчик "хвоста" (тобто перший елемент "змійки") зникає. Це можна реалізувати, замінивши "змійку" на її позиції від 1 до кінця. Таким чином ми виключаємо один — перший — елемент і позбавляємося від кінчика "хвоста".

```python
self.snake = self.snake[1:]
```

З іншого боку, на початку з’являється новий елемент. Його положення, однак, залежить від напрямку руху "змійки". Ми можемо додати елемент за допомогою функції `append`, а його положення ми визначимо в окремій функції `next_head`.

```python
new_head = self.next_head(self.direction)
self.snake.append(new_head)
```

Функція `next_head` має повертати нове положення "голови" "змійки". Тому потрібно буде визначити поточне положення "голови". Це останній елемент у змінній `snake`, тобто `snake[-1]`. Потім ми повинні створити позицію, зміщену в заданому напрямку. Погляньмо ще раз на сітку координат.

![Точка (4, 2) та її околиці.](301_coordinates_plus.jpg)

Якщо нове положення "голови" має бути вгорі, то від змінної `y` ми повинні відняти 1. Якщо внизу — додати 1. Якщо ліворуч, то від змінної `x` ми повинні відняти 1. Якщо праворуч — додати 1. Ми можемо реалізувати це, використовуючи конструкцію if з elif.

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

> Ця функція, в принципі, може мати власні тести. Однак це не обов’язково, оскільки ми опосередковано перевіряємо її роботу під час тестування руху "змійки".

Ми будемо використовувати цей метод у `step`.

```python
# Метод у класі GameState
def step(self):
    new_head = self.next_head(self.direction)
    self.snake.append(new_head)
    self.snake = self.snake[1:]
```

Тепер усі наші тести мають бути успішно пройдені. Перевір це. Однак ми ще не вичерпали тему руху "змійки": що станеться, якщо ми дійдемо до кінця поля?

### Вихід за межі поля

У класичній "змійці", коли ми врізалися у край поля, гра або закінчується, або "змійка" виходить з іншого боку поля. Я вважаю другий варіант цікавішим, тому ми реалізуємо в нашій грі його.

Але почнемо з тестування. Щоб перевірити, чи "змійка" вийде з іншого боку, помістімо її в самий кінець поля і додамо крок. Далі перевіримо, чи "голова" "змійки" вийшла на іншому боці.

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

> Тестування проходження через нижню та ліву стінки напиши самостійно.

Для того, щоб наш код працював, нам загалом потрібна лише невелика зміна під час визначення нових координат у функції `next_head`. Якщо після зміни будь-яка точка координат може становити -1, то замість цього вона має бути `field_size — 1`, тобто за замовчуванням 19. Якби вона мала 20, то становила би 0. Пам’ятаєш оператор залишку від ділення `%`? Щоб нагадати, як він працює: `123 % 5` буде `3`, оскільки найбільша кратність числа `5`, менша ніж `123` — `120`. Тож залишається `3` як остача від ділення. Що важливо, `20 % 20 == 0`, а `-1 % 20 == 19`. Цей оператор ідеально відповідає нашим потребам. Після додавання або віднімання від позиції `x` або `y` ми повинні виконати операцію остачі від ділення на `field_size`.

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

Після цього всі наші тести мають бути пройдені. Запрошую переконатися в цьому самостійно.

## З’їдання кульок

Коли "змійка" з’їдає кульку, вона повинна збільшитися, а нова "їжа" — з’явитися у випадковому місці на полі. Почнімо з першої частини цього завдання. Якщо ми поставимо "змійку" так, що на наступному кроці вона дістається до "їжі", то після виклику функції `step`, вона має збільшуватися. Ми могли би перевірити це, порівнявши попередню та нову довжину "змійки", але ще точніше буде перевірити, чи новий стан змінної `snake` відповідає нашим очікуванням.

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

Зауваж, що єдина зміна, порівняно з попередніми кроками, полягає у тому, що ми не позбавляємося "хвоста" "змійки". Тобто "змійка" подовжується: "голова" додається, а "хвіст" залишається незмінним. Отже, ми повинні позбуватися кінчика "хвоста", лише тоді, коли нове положення "голови" не збігається з розташуванням "їжі".

```python
# Метод у класі GameState
def step(self):
    new_head = self.next_head(self.direction)
    self.snake.append(new_head)
    found_food = new_head == self.food
    if not found_food:
        self.snake = self.snake[1:]
```

Після цієї зміни всі наші тести знову мають бути пройдені. Настав час поставити "їжу" в нове положення. Перевірити вибір випадкового розташування дуже складно, тому зупинімося на перевірці того, що після того, як "голова" "змійки" опиняється на позиції "їжі", з’являється нова порція "їжі", а її положення не збігається з розташуванням "змійки". Для цього достатньо додати наприкінці написаного нами тесту такий тест:

```python
self.assertEqual(False, state.food in state.snake)
```

Як це реалізувати? На останньому кроці ми вже навчилися перевіряти, чи знайшла "змійка" "їжу". Якщо вона її знайшла, ми повинні задати "їжі" нове положення. Ми можемо об’єднати це з попередньо написаною умовою в одну інструкцію if із блоком else. Нове розташування "їжі" ми задамо в окремій функції `set_random_food_position`.

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

У розділі *Організація проєкту та імпорт* ми познайомилися з пакетом `random` та його функцією `randint`, яка дозволяє отримувати випадкове ціле число в певному діапазоні. В якості аргументів ми задамо мінімальне і максимальне значення. Мінімальне значення `x` та `y` має бути 0, а максимальне — `field_size — 1`. Отже, просте визначення випадкового значення може виглядати так:

```python
from random import randint


# Новий метод у класі GameState
def set_random_food_position(self):
    self.food = Position(
        randint(0, self.field_size — 1),
        randint(0, self.field_size — 1)
    )
```

Втім, оскільки це абсолютно випадкове розташування, у нас немає гарантії, що нова порція "їжі" не опиниться всередині "змійки". Є кілька способів вирішити цю проблему. Найпростіший із них — перевірити, чи позиція "їжі" правильна, і провести новий вибір випадкових координат, якщо ні. Для цього ми можемо використовувати цикл `while`, але можна також просто викликати функцію знову:

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

Після визначення цієї функції наші тести повинні бути пройдені знову. "Змійка" вже може рухатися та їсти "їжу", яка після цього з’являтиметься у випадковому місці. Справжній рай, чого ще хотіти. На жаль, "змійка" може з’їсти заборонений плід. Тоді вона має померти.

## Смерть "змійки"

Коли "змійка" наступає на власний "хвіст", вона повинна повернутися до початкового стану (і так по колу). Поки що ми не визначили початкову позицію чи напрямок, але, думаю, настав час це зробити. Я визначу їх як змінну у файлі `game_state.py`.

```python
# Нові змінні у файлі game_state.py
INITIAL_SNAKE = [
    Position(1, 2),
    Position(2, 2),
    Position(3, 2)
]
INITIAL_DIRECTION = Direction.RIGHT
```

У тесті ми можемо імпортувати ці значення, щоб перевірити, чи були вони використані після зіткнення "змійки" з власним "хвостом". До цього я додам перевірку, чи "їжа" не розміщується всередині "змійки", та чи випадково не змінився розмір поля (хоча це не обов’язково).

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

> Імпорт значень `INITIAL_SNAKE` та `INITIAL_DIRECTION` можна перемістити на початок файлу, але, якщо значення потрібне лише в одному місці, рекомендується розміщувати імпорт перед його єдиним використанням. Імпорт створює об’єкт, тому найголовніше, щоб він був запущений перед його першим використанням.

Перше, що нам потрібно, це перевірити, чи було зіткнення. Як це зробити? У функції `step` ми вже знаємо нове положення "голови" і положення всіх частин "змійки". Тепер достатньо перевірити, чи ця нова позиція "голови" не збігається з позицією жодної іншої частини "змійки".

```python
# Всередині метода step
collision = new_head in self.snake
```

Якщо так, то сталося зіткнення. У цьому випадку ми повинні повернути "змійку" в початкове положення і відмовитися від решти етапів руху. Ключовим словом `return` ми можемо завершити виклик функції `step` на цьому кроці.

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

Залишилося реалізувати `set_initial_position`. Ця функція повинна повернути "змійку" у вихідне положення, задати початковий напрямок і вибрати нове випадкове місце для "їжі". При розміщенні "змійки" за допомогою `INITIAL SNAKE` варто зробити копію через `[:]`, щоб захистити цю змінну від внутрішніх змін. Без цього наш виклик `append` у `step` міг би змінити `INITIAL_SNAKE`, що призвело б до неправильного скидання гри.

```python
# Новий метод у класі GameState
def set_initial_position(self):
    self.snake = INITIAL_SNAKE[:]
    self.direction = INITIAL_DIRECTION
    self.set_random_food_position()
```

## Стан на кінець гри

Ми практично закінчили основну роботу: налаштували поведінку "змійки" і "їжі". Правильність виконання підтверджують тести. Але що з того, якщо ми досі не можемо зіграти у нашу гру. У наступному розділі ми використаємо написані нами класи і переконаємося, що в гру нарешті можна зіграти. Ось так повинен виглядати клас `GameState`:

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

[^ 302_1]: Тобто вони прив’язані до класу, а не до об’єкта, як описано в розділі *Класи та об’єкти*.

