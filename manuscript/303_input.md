# Переймаємо контроль

> У цьому розділі ми використаємо реалізовану нами логіку, щоб нарешті можна було зіграти в нашу гру.

Оскільки логіка гри в нас уже реалізована, зараз достатньо її просто використати. Повернімося до файлу `game.py`. Нам не обійтися без імпорту `Position`, `Direction` i `GameState`.

```python
from position import Position
from direction import Direction
from game_state import GameState
```

Спершу нам потрібно створити об’єкт із класу `GameState` і встановити його початкові значення. Для встановлення цих значень у нас уже є функція `set_initial_position`. Тож ми можемо заповнити `snake`, `direction` і `food` будь-якими значеннями, після чого викликати `set_initial_position`, щоб перетворити їх на правильні початкові значення.

```python
state = GameState(
    snake=None,
    direction=None,
    food=None,
    field_size=CUBES_NUM <!-- можливо CUBE_NUM? (być może CUBE_NUM?) -->,
)
state.set_initial_position()
```

Ми тепер можемо зробити у нашому циклі крок, а потім відобразити новий стан за допомогою попередньо реалізованої функції `draw`.

```python
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            quit()

    state.step()
    draw(state.snake, state.food)
```

Однак, якщо ми це зробимо, наша "змійка" буде рухатися так швидко, що розмиватиметься. Потрібно встановити її швидкість, і ми це зробимо за допомогою об’єкта `pygame.time.Clock()`. Коли ми викличемо функцію `tick` у нашому циклі, вона сповільнить тіло "змійки" так, щоб воно викликалося певну кількість разів на секунду. Тож, якщо ми викличемо `tick(10)` в циклі, "змійка" робитиме 10 кроків за секунду. Для початку це хороша швидкість гри. Для того, щоб спростити або ускладнити гру, можна збільшити або зменшити це значення.

```python
clock = pygame.time.Clock()
while True:
    clock.tick(10)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            quit()

    state.step()
    draw(state.snake, state.food)
```

Якщо ми запустимо нашу гру зараз, "змійка" повинна рухатися. Але оскільки ми досі нею не керуємо, вона весь час рухатиметься по колу. Тож настав час перейняти над нею контроль.

## Керування

Коли ми натискаємо кнопку на клавіатурі, інформація про це передається як подія, англійською - "event". Під час кожної ітерації ми можемо відстежувати [^303_1] такі події. Скажу більше, за одним видом подій ми уже стежимо. Поки що лише за подією, яка закінчує гру, а зараз почнемо також фіксувати натискання кнопок, які нас цікавлять.

У PyGame ми використовуємо функцію `pygame.event.get()`, щоб отримати всі події, які відбулися з часу останнього запиту. Вона повертає об’єкт, після якого ми можемо рухатися циклом for. Тому зазвичай, щоб впоратися з усіма подіями, які виникають у ході гри, використовується `for event in pygame.event.get()`. Зрештою, гравець може, наприклад, майже одночасно натиснути стрілку і закрити гру. Оскільки ми не хочемо пропустити жодну подію, ми обробляємо їх послідовно.

Ми ідентифікуємо подію насамперед за її типом, тобто атрибутом `type`. Якщо він дорівнює `pygame.QUIT` [^303_2], це означає, що гру було закрито. Подія, яка вказує на натискання кнопки, має тип `pygame.KEYDOWN`.

Оскільки ми вже знаємо, що наша подія - це натискання кнопки, перевірмо, що це за кнопка. Код кнопки зберігається в атрибуті `key`, а коди стрілок ліворуч, праворуч, вгору та вниз - це, відповідно, `pygame.K_LEFT`, `pygame.K_RIGHT`, `pygame.K_UP` і `pygame.K_DOWN` [^303_3]. Тож ми використаємо наступний if-elif, щоб визначити, як нам реагувати на кожну зі стрілок, які нас цікавлять.

```python
while True:
    clock.tick(10)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            quit()

        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                state.direction = Direction.LEFT

            elif event.key == pygame.K_RIGHT:
                state.direction = Direction.RIGHT

            elif event.key == pygame.K_UP:
                state.direction = Direction.UP

            elif event.key == pygame.K_DOWN:
                state.direction = Direction.DOWN

    state.step()
    draw(state.snake, state.food)
```

## Чи можу я зробити цей крок?

Тепер у нашу гру можна грати, але досі є деякі проблеми. Не всі напрямки руху дозволені. "Змійка" може рухатися вперед, повертати праворуч або ліворуч. Однак вона не повинна розвертатися у протилежному напрямі. Уявіть ситуацію, коли "змійка" рухається праворуч, а гравець натискає стрілку руху ліворуч. Що би сталося? "Голова" наступила б на шию, тобто на частину "змійки", що означало б закінчення гри. Тобто для кожного напрямку руху одна зі стрілок означає самогубство "змійки". Гра з таким обмеженням була б не дуже приємна. Щоб цього не ставалося, ми маємо заблокувати рух у недозволеному напрямку.

Для цього ми будемо використовувати функцію `turn`, яка змінюватиме напрям руху "змійки", але тільки якщо новий напрямок буде дозволено. Почнемо з тесту, який перевірить, чи працює наша функція. Ми поставимо "змійку" в таке ж положення, як перед смертю під час тесту `test_snake_dies`. Справа в тому, що наша функція перевіряла лише, щоб "змійка" не наступала на своє власне тіло, але дозволяла їй наступати собі на "хвіст". У цій конфігурації "змійка" може повертати вгору (що означало би її смерть), ліворуч і вниз. Однак вона не може повернути праворуч, тому що вона звідти йде.

```python
# Новий метод у GameStateTest
def test_turn(self):
    state = GameState(
        snake=[
            Position(1, 2),
            Position(2, 2),
            Position(3, 2),
            Position(3, 3),
            Position(2, 3)
        ],
        direction=Direction.UP,
        food=Position(3, 1),
        field_size=25
    )

    state.turn(Direction.LEFT)
    self.assertEqual(Direction.LEFT, state.direction)

    state.turn(Direction.UP)
    self.assertEqual(Direction.UP, state.direction)

    state.turn(Direction.DOWN)
    self.assertEqual(Direction.DOWN, state.direction)

    state.turn(Direction.RIGHT)
    # Лишається старе значення,
    # тому що не можна повертати праворуч
    self.assertEqual(Direction.DOWN, state.direction)
```

Логіка цієї функції проста. Якщо "змійка" може повернути в певному напрямку, вона повинна це зробити.

```python
# Новий метод у класі GameState
def turn(self, direction):
    if self.can_turn(direction):
        self.direction = direction
```

Цей метод залежить від метода `can_turn`, на якому ми зараз зосередимося. Але почнемо знову з тесту.

```python
# Новий метод у класі GameStateTest
def test_can_turn(self):
    state = GameState(
        snake=[
            Position(1, 2),
            Position(2, 2),
            Position(3, 2),
            Position(3, 3),
            Position(2, 3)
        ],
        direction=Direction.UP,
        food=Position(3, 1),
        field_size=25
    )

    self.assertEqual(
        True,
        state.can_turn(Direction.LEFT)
    )
    self.assertEqual(
        True,
        state.can_turn(Direction.UP)
    )
    self.assertEqual(
        True,
        state.can_turn(Direction.DOWN)
    )
    self.assertEqual(
        False,
        state.can_turn(Direction.RIGHT)
    )
```

Як написати такий метод? Як уже згадувалося, найпростіший спосіб — перевірити, чи нове положення "голови" "змійки" не збігається з положенням "шиї", тобто фрагментом "змійки", розташованим одразу перед "головою". "Шия" - це `snake[-2]`, нове положення "голови" можна визначити за допомогою `next_head`, а для порівняння можна використати знак `!=`.

```python
# Новий метод у класі GameState
def can_turn(self, direction):
    new_head = self.next_head(direction)
    return new_head != self.snake[-2]
```

Коли ми маємо цю функцію, нам достатньо модифікувати управління, щоб натискання клавіш замість зміни значення змінної `direction` викликало метод `turn`. Ось весь наш цикл гри із визначенням стану:

```python
state = GameState(
    snake=None,
    direction=None,
    food=None,
    field_size=CUBES_NUM,
)
state.set_initial_position()

clock = pygame.time.Clock()
while True:
    clock.tick(10)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            quit()

        elif event.type == pygame.KEYDOWN:

            if event.key == pygame.K_LEFT:
                state.turn(Direction.LEFT)

            elif event.key == pygame.K_RIGHT:
                state.turn(Direction.RIGHT)

            elif event.key == pygame.K_UP:
                state.turn(Direction.UP)

            elif event.key == pygame.K_DOWN:
                state.turn(Direction.DOWN)

    state.step()
    draw(state.snake, state.food)
```

{pagebreak}

## Завершення

Ось і все. Ми добряче попрацювали, але нарешті можемо зіграти у "Змійку". Поб’єш мій рекорд? Я дійшов до... навіть не знаю, тому що ми не зробили лічильника очок, але моя "змійка" була справді довга.

Тож у цій грі ще багато можна зробити. Наприклад:

* додати лічильник очок;
* зробити так, щоб зіткнення з кінцем поля вбивало "змійку";
* додати на полі кілька перешкод;
* намалювати "змійці" очі та покращити вигляд "хвоста", поля та "їжі";
* сповільнити "змійку", але додати прискорення з часом;
* додати можливість зупинити гру (наприклад, пробілом);
* додати екран із демонстрацією результату після смерті "змійки";
* додати перелік найкращих результатів (що потребує зберігання результатів на диску).

[https://github.com/MarcinMoskala/snake](https://github.com/MarcinMoskala/snake)

{width: 20%}
![Замість посилання можна скористатися цим QR-кодом.](qr0.png)

У проєкті в папці "1-daj-mi-okno-na-swiat" є код, який повинен бути створений після першого розділу "Дай мені вікно у світ", а в папці "2-zmiana-stanu" — код, який має бути написаний після другого розділу "Зміна стану". Решта файлів — це остаточний код гри.

[^303_1]: У програмуванні ми говоримо про відстеження, коли визначаємо певну поведінку, яка має відбутися, коли стається визначена подія.
[^303_2]: Щоб дізнатися більше про типи подій, переглянь документацію пакета, а також форуми, StackOverflow та інші сайти для обговорень.
[^303_3]: Якщо ти хочеш додати реакції на інші клавіші, усі вони починаються з `K_`. Для літер і цифр після цього записується відповідна літера або цифра, напр. `K_2`, `K_a`.



