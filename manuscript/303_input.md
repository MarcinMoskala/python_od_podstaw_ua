# Переймаємо контроль

> У цьому розділі ми&nbsp;використаємо реалізовану нами логіку, щоб нарешті можна було зіграти в&nbsp;нашу гру.

Оскільки логіка гри в&nbsp;нас уже реалізована, зараз достатньо її&nbsp;просто використати. Повернімося до&nbsp;файлу `game.py`. Нам не&nbsp;обійтися без імпорту `Position`, `Direction` i `GameState`.

```python
from position import Position
from direction import Direction
from game_state import GameState
```

Спершу нам потрібно створити об’єкт із&nbsp;класу `GameState` і встановити його початкові значення. Для встановлення цих значень у&nbsp;нас уже є функція `set_initial_position`. Тож ми&nbsp;можемо заповнити `snake`, `direction` і `food` будь-якими значеннями, після чого викликати `set_initial_position`, щоб перетворити їх&nbsp;на&nbsp;правильні початкові значення.

```python
state = GameState(
    snake=None,
    direction=None,
    food=None,
    field_size=CUBES_NUM
)
state.set_initial_position()
```

Ми тепер можемо зробити у&nbsp;нашому циклі крок, а потім відобразити новий стан за&nbsp;допомогою попередньо реалізованої функції `draw`.

```python
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            quit()

    state.step()
    draw(state.snake, state.food)
```

Однак, якщо ми&nbsp;це зробимо, наша "змійка" буде рухатися так швидко, що&nbsp;розмиватиметься. Потрібно встановити її&nbsp;швидкість, і ми&nbsp;це зробимо за&nbsp;допомогою об’єкта `pygame.time.Clock()`. Коли ми&nbsp;викличемо функцію `tick` у&nbsp;нашому циклі, вона сповільнить тіло "змійки" так, щоб воно викликалося певну кількість разів на&nbsp;секунду. Тож, якщо ми&nbsp;викличемо `tick(10)` в&nbsp;циклі, "змійка" робитиме 10 кроків за&nbsp;секунду. Для початку це&nbsp;хороша швидкість гри. Для того, щоб спростити або ускладнити гру, можна збільшити або зменшити це&nbsp;значення.

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

Якщо ми&nbsp;запустимо нашу гру&nbsp;зараз, "змійка" повинна рухатися. Але оскільки ми&nbsp;досі нею не&nbsp;керуємо, вона весь час&nbsp;рухатиметься по&nbsp;колу. Тож настав час&nbsp;перейняти над нею контроль.

## Керування

Коли ми&nbsp;натискаємо кнопку на&nbsp;клавіатурі, інформація про це&nbsp;передається як&nbsp;подія, англійською - "event". Під час&nbsp;кожної ітерації ми&nbsp;можемо відстежувати [^303_1] такі події. Скажу більше, за&nbsp;одним видом подій ми&nbsp;уже стежимо. Поки що&nbsp;лише за&nbsp;подією, яка закінчує гру, а зараз почнемо також фіксувати натискання кнопок, які нас цікавлять.

У PyGame ми&nbsp;використовуємо функцію `pygame.event.get()`, щоб отримати всі події, які відбулися з&nbsp;часу останнього запиту. Вона повертає об’єкт, після якого ми&nbsp;можемо рухатися циклом for. Тому зазвичай, щоб впоратися з&nbsp;усіма подіями, які виникають у&nbsp;ході гри, використовується `for event in pygame.event.get()`. Зрештою, гравець може, наприклад, майже одночасно натиснути стрілку і закрити гру. Оскільки ми&nbsp;не хочемо пропустити жодну подію, ми&nbsp;обробляємо їх&nbsp;послідовно.

Ми ідентифікуємо подію насамперед за&nbsp;її&nbsp;типом, тобто атрибутом `type`. Якщо він дорівнює `pygame.QUIT` [^303_2], це&nbsp;означає, що&nbsp;гру&nbsp;було закрито. Подія, яка вказує на&nbsp;натискання кнопки, має тип `pygame.KEYDOWN`.

Оскільки ми&nbsp;вже знаємо, що&nbsp;наша подія - це&nbsp;натискання кнопки, перевірмо, що&nbsp;це&nbsp;за кнопка. Код кнопки зберігається в&nbsp;атрибуті `key`, а коди стрілок ліворуч, праворуч, вгору та&nbsp;вниз - це, відповідно, `pygame.K_LEFT`, `pygame.K_RIGHT`, `pygame.K_UP` і `pygame.K_DOWN` [^303_3]. Тож ми&nbsp;використаємо наступний if-elif, щоб визначити, як&nbsp;нам реагувати на&nbsp;кожну зі&nbsp;стрілок, які нас цікавлять.

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

## Чи&nbsp;можу я зробити цей крок?

Тепер у&nbsp;нашу гру&nbsp;можна грати, але досі є деякі проблеми. Не&nbsp;всі напрямки руху дозволені. "Змійка" може рухатися вперед, повертати праворуч або ліворуч. Однак вона не&nbsp;повинна розвертатися у&nbsp;протилежному напрямі. Уявіть ситуацію, коли "змійка" рухається праворуч, а гравець натискає стрілку руху ліворуч. Що&nbsp;би сталося? "Голова" наступила б на&nbsp;шию, тобто на&nbsp;частину "змійки", що&nbsp;означало б закінчення гри. Тобто для кожного напрямку руху одна зі стрілок означає самогубство "змійки". Гра з&nbsp;таким обмеженням була б не&nbsp;дуже приємна. Щоб&nbsp;цього не&nbsp;ставалося, ми&nbsp;маємо заблокувати рух у&nbsp;недозволеному напрямку.

Для цього ми&nbsp;будемо використовувати функцію `turn`, яка змінюватиме напрям руху "змійки", але тільки якщо новий напрямок буде дозволено. Почнемо з&nbsp;тесту, який перевірить, чи працює наша функція. Ми&nbsp;поставимо "змійку" в&nbsp;таке ж положення, як&nbsp;перед смертю під час&nbsp;тесту `test_snake_dies`. Справа в&nbsp;тому, що&nbsp;наша функція перевіряла лише, щоб "змійка" не&nbsp;наступала на&nbsp;своє власне тіло, але дозволяла їй наступати собі на&nbsp;"хвіст". У цій конфігурації "змійка" може повертати вгору (що означало би її&nbsp;смерть), ліворуч і вниз. Однак вона не&nbsp;може повернути праворуч, тому що&nbsp;вона звідти йде.

```python
# Новий метод у&nbsp;GameStateTest
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
    # тому що&nbsp;не&nbsp;можна повертати праворуч
    self.assertEqual(Direction.DOWN, state.direction)
```

Логіка цієї функції проста. Якщо "змійка" може повернути в&nbsp;певному напрямку, вона повинна це&nbsp;зробити.

```python
# Новий метод у&nbsp;класі GameState
def turn(self, direction):
    if self.can_turn(direction):
        self.direction = direction
```

Цей метод залежить від метода `can_turn`, на&nbsp;якому ми&nbsp;зараз зосередимося. Але почнемо знову з&nbsp;тесту.

```python
# Новий метод у&nbsp;класі GameStateTest
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

Як написати такий метод? Як уже згадувалося, найпростіший спосіб — перевірити, чи нове положення "голови" "змійки" не&nbsp;збігається з&nbsp;положенням "шиї", тобто фрагментом "змійки", розташованим одразу перед "головою". "Шия" - це&nbsp;`snake[-2]`, нове положення "голови" можна визначити за допомогою `next_head`, а для порівняння можна використати знак `!=`.

```python
# Новий метод у&nbsp;класі GameState
def can_turn(self, direction):
    new_head = self.next_head(direction)
    return new_head != self.snake[-2]
```

Коли ми&nbsp;маємо цю функцію, нам достатньо модифікувати управління, щоб натискання клавіш замість зміни значення змінної `direction` викликало метод `turn`. Ось весь наш цикл гри із визначенням стану:

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

Ось і все. Ми&nbsp;добряче попрацювали, але нарешті можемо зіграти у&nbsp;"Змійку". Поб’єш мій рекорд? Я дійшов до... навіть не&nbsp;знаю, тому що&nbsp;ми&nbsp;не зробили лічильника очок, але моя "змійка" була справді довга.

Тож у&nbsp;цій грі ще багато можна зробити. Наприклад:

* додати лічильник очок;
* зробити так, щоб зіткнення з&nbsp;кінцем поля вбивало "змійку";
* додати на&nbsp;полі кілька перешкод;
* намалювати "змійці" очі та&nbsp;покращити вигляд "хвоста", поля та&nbsp;"їжі";
* сповільнити "змійку", але додати прискорення з&nbsp;часом;
* додати можливість зупинити гру&nbsp;(наприклад, пробілом);
* додати екран із демонстрацією результату після смерті "змійки";
* додати перелік найкращих результатів (що потребує зберігання результатів на&nbsp;диску).

[https://github.com/MarcinMoskala/snake](https://github.com/MarcinMoskala/snake)

{width: 20%}
![Замість посилання можна скористатися цим QR-кодом.](qr0.png)

У проєкті в&nbsp;папці "1-daj-mi-okno-na-swiat" є код, який повинен бути створений після першого розділу "Дай мені вікно у&nbsp;світ", а в&nbsp;папці "2-zmiana-stanu" — код, який має бути написаний після другого розділу "Зміна стану". Решта файлів — це&nbsp;остаточний код гри.

[^303_1]: У програмуванні ми&nbsp;говоримо про відстеження, коли визначаємо певну поведінку, яка має відбутися, коли стається визначена подія.
[^303_2]: Щоб&nbsp;дізнатися більше про типи подій, переглянь документацію пакета, а також форуми, StackOverflow та&nbsp;інші сайти для обговорень.
[^303_3]: Якщо ти хочеш додати реакції на&nbsp;інші клавіші, усі вони починаються з&nbsp;`K_`. Для літер і цифр після цього записується відповідна літера або цифра, напр. `K_2`, `K_a`.



