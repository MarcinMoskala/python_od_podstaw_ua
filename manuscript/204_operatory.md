# Оператори

У розділі *Основні значення* ми&nbsp;дізналися про базові оператори, такі як&nbsp;математичні оператори (`+`, `-`, `*` тощо) або оператори порівняння (`>`, `==`, `> =` тощо) і побачили їх використання для основних значень. А як&nbsp;щодо класів, які ми&nbsp;визначаємо самі? Чи можуть такі класи також використовувати оператори? Відповідь: так! Однак необхідно визначити, як&nbsp;ці оператори повинні поводитися. Для цього Python використовує методи зі спеціальними назвами, наприклад, `__eq__` або `__gt__`. Коли вони визначені, будуть дозволені деякі додаткові операції для даного класу.

У цьому розділі ми&nbsp;дізнаємося про найважливіші з&nbsp;методів зі спеціальними назвами. Деякі з&nbsp;них, наприклад `__add__` та&nbsp;`__gt__`, дозволяють визначити новий оператор для класу. Інші, наприклад `__str__` та&nbsp;`__eq__`, визначають поведінку функцій або структур, які використовує Python. На прикладах все стане зрозумілим. Усі ці спеціальні методи зазвичай використовуються в&nbsp;проєктах і бібліотеках, а також значною мірою визначають зручність використання Python.

## `__str__`

Коли ми&nbsp;передали рядок або число як&nbsp;аргумент у&nbsp;функцію `print`, ми&nbsp;побачили прекрасний результат. Однак, коли ми&nbsp;створили власний клас, введений текст вже не&nbsp;дуже допоможе.

```python
print(1)  # 1
print("AAA")  # AAA


class User:
    def __init__(self, name):
        self.name = name


user = User("Alojzy")
print(user)  # <__main__.User object at 0x1109c6b20>
```

Від чого це&nbsp;залежить? Від методу `__str__` [^204_1]. Це спеціальний метод, який Python використовує, коли ми&nbsp;хочемо перетворити об’єкт на&nbsp;рядок. За замовчуванням відображається повна назва об’єкта (включно з&nbsp;його розташуванням) і адреса його пам’яті (це нас не&nbsp;повинно цікавити). Однак ми&nbsp;можемо замінити його таким чином, щоб він повертав більше корисної інформації про об’єкт. Щоб&nbsp;це&nbsp;зробити, нам слід визначити метод `__str__` і повернути з&nbsp;нього значення рядка, яке має представляти об’єкт.

```python
class User:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return f"User(name={self.name})"


user = User("Алоїз")
print(user)  # User(name=Алоїз)
```

Як це&nbsp;працює? Функція `print` використовує конструктор `str`, який використовує метод `__str__` нашого об’єкта, щоб перетворити його на&nbsp;рядок. Отже, цей метод має спеціальне значення. Однак його не&nbsp;слід застосовувати напряму. Замість того, щоб викликати метод `__str__`, краще використовувати конструктор `str` або f-рядок.

```python
print("Користувач: " + user.__str__())
# Користувач: User(name=Алоїз)
print("Користувач: " + str(user))
# Користувач: User(name=Алоїз)
print(f"Користувач: {user}")
# Користувач: User(name=Алоїз)
```

У Python існує ще багато подібних методів зі спеціальним значенням. Усі вони використовуються різними вбудованими засобами мови.

{pagebreak}

## `__repr__`

Коли ми&nbsp;відображаємо список, цей метод виводить елементи списку.

```python
l = [1, "A", True]
print(l)  # [1, 'A', True]
```

Це тому, що&nbsp;список має метод `__str__`, а він змінює кожен збережений об’єкт на&nbsp;рядок. Тут, однак, варто зауважити, що&nbsp;спосіб представлення цих елементів дещо інший, ніж при використанні `str`. Наприклад, для рядка `str` він повертає виключно його вміст, а коли ми&nbsp;показуємо рядок у&nbsp;списку, він береться в&nbsp;лапки. Це тому, що&nbsp;використовується "офіційне" представлення об’єкта у&nbsp;вигляді рядка, яке ми&nbsp;отримуємо за допомогою функції `repr` [^204_2] і визначаємо за допомогою методу `__repr__`.

```python
print(repr("A"))  # 'A'
print(str("A"))  # A
print("A".__repr__())  # 'A'
print("A".__str__())  # A
```

Хорошим прикладом різниці між двома методами може бути клас, який представляє ім’я та&nbsp;прізвище. Репрезентацією `__str__` можуть бути просто ім’я та&nbsp;прізвище. Репрезентацією `__repr__` - повна інформація про назву класу та&nbsp;його значення.

```python
class FullName:
    def __init__(self, name, surname):
        self.name = name
        self.surname = surname

    def __str__(self):
        return f"{self.name} {self.surname}"

    def __repr__(self):
        return "FullName("+\
            f"name={repr(self.name)}, "+\
            f"surname={repr(self.surname)})"


player = FullName("Алоїз", "Москала")
print(player)  # Алоїз Москала

print(str(player))  
# Алоїз Москала

print(repr(player))  
# FullName(name='Алоїз', surname='Москала')
```

> У прикладі вище я розділив рядок на&nbsp;кілька лінійок через обмежену ширину коду в&nbsp;книзі. Операції, розбиті на&nbsp;кілька лінійок, повинні бути або взяті в&nbsp;дужки, або завершуватися символами `\`. Це потрібно для того, щоб інтерпретатор розглядав наступний рядок як&nbsp;продовження попереднього.

Коли ми&nbsp;визначаємо об’єкти, то найчастіше хочемо, щоб `__repr__` працював так само, як&nbsp;`__str__`, що&nbsp;можна отримати шляхом виразу `__repr__ = __str__`.

```python
class Position:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"({self.x}, {self.y})"

    __repr__ = __str__
    # або
    # def __repr__(self):
    #     return self.__str__()


position = Position(10, 20)
print(position)  # (10, 20)
print(repr(position))  # (10, 20)
```

## `__eq__`

Ще одним важливим методом є `__eq__` [^204_3], який визначає, чи два об’єкти дорівнюють одне одному. Отже він використовується при порівнянні двох об’єктів за допомогою `==` або `!=`. Якщо ми&nbsp;не визначимо цей метод, два різні об’єкти цього класу ніколи не&nbsp;дорівнюватимуть одне одному. Для класів без визначеного методу `__eq__` оператор `==` повертає `True`, лише якщо з&nbsp;обох боків є абсолютно однаковий об’єкт (наприклад оператор `is`). Тому в&nbsp;прикладі нижче `user1 == user1` повертається `True`, але вже при `user1 == user2` — `False`, хоча атрибути обох об’єктів ідентичні.

```python
class User:
    def __init__(self, name):
        self.name = name


user1 = User("Алек")
user2 = User("Алек")
user3 = User("Болек")
print(user1 == user1)  # True
print(user1 == user2)  # False
print(user1 == user3)  # False
print(user1 is user1)  # True
print(user1 is user2)  # False
print(user1 is user3)  # False
print(user1 != user1)  # False
print(user1 != user2)  # True
print(user1 != user3)  # True
```

Коли ми&nbsp;визначимо `__eq__`, він повинен містити параметри `self` та&nbsp;`other`, які представляють об’єкти для порівняння. Він також має повертати логічне значення, яке відповідає на&nbsp;запитання, чи об’єкти дорівнюють одне одному (`True`), чи ні (`False`). Зазвичай ми&nbsp;починаємо з&nbsp;перевірки, чи `other` є того ж типу, а потім порівнюємо його властивості. Для перевірки типу ми&nbsp;використовуємо функцію `isinstance`. Як перший аргумент ми&nbsp;використовуємо об’єкт, тип якого ми&nbsp;хочемо перевірити, а як&nbsp;другий — назву класу.

```python
class A:
    pass

class B:
    pass

a = A()
print(isinstance(a, A))  # True
print(isinstance(a, B))  # False
b = B()
print(isinstance(b, A))  # False
print(isinstance(b, B))  # True
```

Коли реалізуємо метод `__eq__`, достатньо перевірити тип параметра `other`. Потім ми&nbsp;почергово порівнюємо значення важливих для нас атрибутів.

```python
class User:
    def __init__(self, name):
        self.name = name

    def __eq__(self, other):
        return (
                isinstance(other, User)
                and other.name == self.name
        )


user1 = User("Алек")
user2 = User("Алек")
user3 = User("Болек")
print(user1 == user1)  # True
print(user1 == user2)  # True
print(user1 == user3)  # False
print(user1 != user1)  # False
print(user1 != user2)  # False
print(user1 != user3)  # True
```

```python
# Оператор `is` не змінює поведінку
print(user1 is user1)  # True
print(user1 is user2)  # False
print(user1 is user3)  # False
```

Ось як&nbsp;може виглядати клас `Position` разом із реалізованим методом `__eq__`:

```python
class Position:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __eq__(self, other):
        return (
                isinstance(other, Position) and
                self.x == other.x and
                self.y == other.y
        )

    def __str__(self):
        return f"({self.x}, {self.y})"

    __repr__ = __str__


position1 = Position(10, 20)
position2 = Position(10, 20)
position3 = Position(1, 2)
print(position1 == position2)  # True
print(position1 == position3)  # False
```

> У реальних проєктах, коли ми&nbsp;визначаємо `__eq__`, ми&nbsp;повинні також визначити `__hash__`. Однак пояснення цього методу виходить за рамки цієї книги.

### Вправа: `__str__`, `__repr__` i `__eq__`

Створи клас `Money` з&nbsp;атрибутами `amount` і `currency`. Їхні значення повинні бути вказані в&nbsp;конструкторі. Два об’єкти повинні бути рівними, коли значення обох полів однакові. Перетворення на&nbsp;рядок має повертати суму та&nbsp;валюту, розділені пробілом. Офіційна репрезентація об’єкта має показувати його назву та&nbsp;атрибути `amount` і `currency`.

```python
money1 = Money(10.0, "PLN")
money2 = Money(10.0, "PLN")
money3 = Money(20.0, "PLN")
money4 = Money(10.0, "EUR")

print(money1 == money1)  # True
print(money1 == money2)  # True
print(money1 == money3)  # False
print(money1 == money4)  # False

print(money1)  # 10.0 PLN
print(money2)  # 10.0 PLN
print(money3)  # 20.0 PLN
print(money4)  # 10.0 EUR

print(repr(money1))
# Money(amount=10.0, currency='PLN')
print(repr(money3))
# Money(amount=20.0, currency='PLN')
print(repr(money4))
# Money(amount=10.0, currency='EUR')
```

У той же час&nbsp;порівняння з&nbsp;іншим класом, навіть із тими самими атрибутами, повертатиме `False`.

```python
class FakeMoney:
    def __init__(self, amount, currency):
        self.amount = amount
        self.currency = currency


money = Money(10, "PLN")
fakeMoney = FakeMoney(10, "PLN")
print(money == fakeMoney)  # False
```

Відповіді в кінці книги.

## Математичні дії та&nbsp;порівняння

Python підтримує багато операторів, а наші класи можуть скористатися ними, перевизначаючи різні спеціальні методи. Ці можливості використовує багато розробників пакетів. Чудовим прикладом є Pandas - пакет, який широко використовується людьми, які працюють із даними (аналітиками, інженерами даних, статистиками). Ми&nbsp;побачимо, як&nbsp;він працює, в&nbsp;розділі *Аналіз даних* у&nbsp;четвертій частині. Однак, щоб дати Тобі певне уявлення про наявні можливості, я наведу кілька прикладів надписання різних операторів.

Якщо ми&nbsp;хочемо уможливити математичні операції між об’єктами, нам слід визначити такі функції, як&nbsp;`__add__` [^204_4], `__sub__` [^204_5] чи `__mul__` [^204_6].

```python
class Position:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"({self.x}, {self.y})"

    def __add__(self, other):
        return Position(self.x + other.y,
                        self.y + other.y)

    def __sub__(self, other):
        return Position(self.x - other.y,
                        self.y - other.y)

    def __mul__(self, other):
        return Position(self.x * other,
                        self.y * other)


p1 = Position(1.0, 2.0)
p2 = Position(3.0, 4.0)
p3 = p1 + p2
print(p3)  # (5.0, 6.0)
p4 = p1 * 3
print(p4)  # (3.0, 6.0)
```

Якщо ми&nbsp;хочемо порівнювати об’єкти за допомогою операторів `>`, `<`, `> =` і `<=`, ми&nbsp;повинні визначити методи `__lt__` [^204_7] чи `__le__` [^204_8].

```python
class Position:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __str__(self):
        return f"({self.x}, {self.y})"

    def __lt__(self, other):
        self_mag = (self.x ** 2) + (self.y ** 2)
        other_mag = (other.x ** 2) + (other.y ** 2)
        return self_mag < other_mag

    def __le__(self, other):
        self_mag = (self.x ** 2) + (self.y ** 2)
        other_mag = (other.x ** 2) + (other.y ** 2)
        return self_mag <= other_mag


p1 = Position(1.0, 2.0)
p2 = Position(3.0, 4.0)
print(p1 > p2)  # False
print(p1 < p2)  # True
print(p1 < p1)  # False
print(p1 >= p2)  # False
print(p1 <= p2)  # True
print(p1 <= p1)  # True
```

Ми також могли би записати їх як&nbsp;`__gt__` [^204_9] і `__ge__` [^204_10]. Метод `__gt__` має повернути значення, протилежне до`__le__` (оскільки `a > b` має повертати те саме значення, що&nbsp;й `not (a <= b)`), а метод `__ge__` — значення, протилежне до `__lt__` (оскільки `a > = b` має повернути те ж значення, що&nbsp;й `not (a < b)`).

На завершення я хотів би представити метод, який широко використовується багатьма пакетами для зчитування та&nbsp;керування даними. `__getattr__` [^204_11] дозволяє нам вирішити, що&nbsp;має статися, коли ми&nbsp;спробуємо прочитати атрибут, якого не&nbsp;існує. У наступному прикладі клас `Echo` не&nbsp;містить жодних атрибутів, але коли ми&nbsp;запитуємо `Ааа`, `Ооо` і `Хей`, він відповідає текстом "Echo: " та&nbsp;назвою атрибута (параметр `item` містить назву атрибута).

```python
class Echo:

    def __getattr__(self, item):
        return f"Echo: {item}"


echo = Echo()
print(echo.Aaa)  # Echo: Ааа
print(echo.Ooo)  # Echo: Ооо
print(echo.Hej)  # Echo: Хей
```

### Завершення

Як бачиш, Python має дуже цікавий функціонал, який дає можливість надавати об’єктам особливої поведінки. Це дозволяє виконувати різноманітні операції над об’єктами, що&nbsp;забезпечує чудові можливості для розробників пакетів. Ми&nbsp;повернемося до цього, коли почнемо використовувати різні пакети.


[^204_1]: "str" — скорочення від англ. "string", "рядок".
[^204_2]: "repr" — скорочення від англ. "representation", репрезентація, представлення, тобто те, як&nbsp;має бути представлений об’єкт.
[^204_3]: "eq" — скорочення від англ. "equals", "дорівнює".
[^204_4]: "додати" — скорочення від англ. "addition", "додавання".
[^204_5]: "sub" — скорочення від англ. "subtraction", "віднімання".
[^204_6]: "mul" — скорочення від англ. "multiplication", "множення".
[^204_7]: "lt" — скорочення від англ. "less than", "менше ніж".
[^204_8]: "le" — скорочення від англ. "less or equal", "менше або дорівнює".
[^204_9]: "gt" — скорочення від англ. "greater than", "більше ніж".
[^204_10]: "ge" — скорочення від англ. "greater or equal", "більшe або дорівнює".
[^204_11]: "getattr" — скорочення від англ. "get attribute", "візьми атрибут".

