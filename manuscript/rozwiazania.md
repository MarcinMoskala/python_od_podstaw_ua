# Відповіді до вправ

### Рядки

У першому випадку зверни увагу на використання одинарних лапок, які вживаються, щоб розмістити подвійні лапки всередині.

```python
print('Хто "вона"?')  # Хто "вона"?
```

У другому випадку ми не використали цей прийом, тому подвійні лапки всередині закінчують рядок, а потім — відкривають новий. На щастя, всередині є символ `+`, який поєднує два тексти в один. Зверни увагу на подвійний пробіл між "Використаю" та "щоб" у виведеному тексті.

```python
print("Використаю " + " для з’єднання текстів")
# Використаю  для з’єднання текстів
```

Якщо ми хочемо написати `"+"`, тоді ми повинні взяти весь текст в одинарні лапки.

```python
print('Використаю "+" для з’єднання текстів')
# Використаю "+" для з’днання текстів
```

В останньому випадку ми зробили типову помилку новачка і забули використати пробіл під час додавання двох рядків.

```python
print("Текст," + "наступний," + "ще один")
# Текст,наступний,ще один
```

Ми повинні додати цей пробіл у тексті ліворуч або праворуч від символу додавання.

```python
print("Текст, " + "наступний, " + "ще один")
# Текст, наступний, ще один

print("Текст," + " наступний," + " ще один")
# Текст, наступний, ще один
```

### Python як калькулятор

```python
print(1 * 2 + 3 * 4 + 5 * 6 + 7 * 8 + 9)
# 109
print(1 * (2 + 3) * (4 + 5) * (6 + 7) * (8 + 9))
# 9945
# Відрізняється від попереднього через дужки
# додавання виконується перед множенням.
print(1 * 2 / 3 * 4 / 5 * 6 / 7 * 8 / 9 * 10)
# 4.063492063492064
# мало бути більше, ніж 1,
№ тому що останнім виконується множення.
print(2 ** 10)  # 1024
# це дуже відоме число в програмуванні
# "кіло" в інформатиці зазвичай означає 1024,
# тоді як в інших сферах — 1000.
```

### Python як калькулятор порівняння

У першому випадку множення закінчується на 5, тому ми можемо очікувати, що права частина буде більшою. Наступний код це підтверджує.

```python
print(1 * 2 + 3 * 4 + 5 < 1 + 2 * 3 + 4 * 5)
# True
```

Нижче ми перевіряємо для інших випадків, чи ліва сторона більша за праву.

```python
print(2 ** 20 > 3 ** 15)
# False, тобто права сторона більша
print(2 ** 15 > 10 ** 4)
# True, тобто ліва сторона більша
```

Ми також можемо перевірити навпаки.

```python
print(2 ** 20 < 3 ** 15)  # True
print(2 ** 15 < 3 ** 4)  # False
```

### Визначення змінних 1

```python
name = "Міхал"
print(name)  # Міхал
name = "Марцін"
print(name)  # Марцін
points = 123
print(points)  # 123
```

### Визначення змінних 2

```python
num = 10
print(num)  # 10
num = 20
print(num)  # 20
```

### Називання змінних

- `public_workshops` — правильна назва.
- `1_way_ticket` — назви змінних починаємо з цифр. Краща версія: `one_way_ticket`.
- `arbitrary_user` — загалом правильно, хоча перша частина надто довга. Достатньо `user`. Однак, якщо ми хочемо вказати, що йдеться про якесь значення, ми можемо використати `a`/`an`, тобто `a_user`.
- `note_type` — правильна назва.
- `user_Maciek` — формально прийнятна назва, хоча використання польського імені — сумнівна практика. Крім того, ми зазвичай визначаємо змінні, щоб описати щось універсальне, що характеризує користувача. У деяких випадках така назва була би прийнятною, але зазвичай ми віддаємо перевагу, наприклад `user`, коли йдеться про "якогось користувача", `user1`, коли ідеться про одного з кількох, або `admin_user`, якщо ідеться про користувача з правами адміністратора.
- `post_user_request` — правильна назва. Тут `post` відноситься до метода POST інтернет-протоколу HTTP.
- `szkolenie_warszawa` — польські слова у назвах змінних не використовуються. Краща версія: `workshop_warsaw`.

### Додавання та віднімання від значень змінних

```python
fruits = "Інжир "
price = 0

fruits += "Груша "
price += 3.2

print(fruits)  # Інжир Груша
print(price)  # 3.2

fruits += "Банан "
price += 2.8

print(fruits)  # Інжир Груша Банан
print(price)  # 6.0

fruits += "(купон на знижку)"
price -= 1.4

print(price)  # 4.6
```

### `None`

```python
name = None
surname = "Міхальський"

print(name is None)  # True
print(name)  # None
print(surname)  # Міхальський

name = surname
surname = "Ґайос"

print(name is None)  # False
print(name)  # Міхальський
print(surname)  # Ґайос
```

### Вправа: if

```python
if is_important:
    print("З повагою")

print("Вітаю")

if gender == "female":
    print("Пані")
if gender == "male":  # albo elif
    print("Пана")
```

Рішення в одному рядку:

```python
text = ""
if is_important:
    text += "З повагою"

text += "Вітаю"

if gender == "female":
    text += " Пані"
if gender == "male":  # albo elif
    text += " Пана"
print(text)
```

### Вправа: else та elif

```python
# Приклади значень
coffee_finished = True
days_until_deadline = 3

# Розв’язок
if not coffee_finished:
    print("П’ю каву")
elif days_until_deadline < 2:
    print("Працюю")
else:
    print("Вчуся програмувати!")
```

### Умови

**Перший фрагмент, перший варіант**

```
Привіт, Міхал
Може, щось вип’єте?
```

**Перший фрагмент, другий варіант**

```
Вітаємо, Пане Голово
Привіт, Голова Марек
Може, щось вип’єте?
```

**Перший фрагмент, третій варіант**

```
Привіт, Марек
Може, цукерку?
```

**Перший фрагмент, четвертий варіант**

```
Може, цукерку?
```

**Другий фрагмент, перший варіант**

```
Привіт, Міхал
Може, щось вип’єте?
```

**Другий фрагмент, другий варіант**

```
Вітаємо, Пане Голово
Може, щось вип’єте?
```

**Другий фрагмент, третій варіант**

```
Привіт, Павел
Може, цукерку?
```

**Другий фрагмент, четвертий варіант**

```
Привіт
Може, цукерку?
```

**Третій фрагмент, перший варіант**

```
Привіт, Міхал
Категорія: Дорослі
```

**Третій фрагмент, другий варіант**

```
Вітаємо, Пане Голово
Категорія: Старші
```

**Третій фрагмент, третій варіант**

```
Привіт, Павел
Категорія: Діти
```

**Третій фрагмент, четвертий варіант**

```
Категорія: Молодь
```

### Складні логічні вирази

```python
has_computer = True
passed_test = False
is_grounded = False

print(has_computer and passed_test)
# False, тому що True and False
print(passed_test or is_grounded)
# False, тому що False or False
print(has_computer and not passed_test)
# True, тому що True and !False

can_play_games = has_computer and not is_grounded
# True, тому що True and !False,
# а отже True and True
print(can_play_games)  # True

play_games = has_computer and can_play_games
# True, тому що True and True

if not passed_test:
    is_grounded = True

print(play_games)  # True,
# оскільки, коли визначалося це значення,
# is_grounded було False
print(passed_test or not is_grounded)
# False, тому що False or !True,
# оскільки поточне значення is_grounded — True
print(not (not has_computer or not passed_test))
# False, тому що True and False,
# оскільки !(!a or !b) означає те ж, що й a and b
```

{pagebreak}

### Математичні послідовності

**Випиши послідовні парні числа (кратні 2), починаючи з 0, які менші за 100.**

```python
i = 0
while i < 100:
    print(i)
    i += 2  # або i = i + 2
```

**Випиши послідовні кратні 7, починаючи з 0, які менші за 100.**

```python
i = 0
while i < 100:
    print(i)
    i += 7  # або i = i + 7
```

**Випиши послідовні значення, утворені в результаті потроєння числа кілька разів, починаючи з 13, доки результат буде меншим за 1000.**

```python
i = 13
while i < 1000:
    print(i)
    i *= 3  # albo i = i * 3
```

**Випиши квадрати послідовних цілих чисел, які менші за 1000.**

```python
i = 0
while i * i < 1000:
    print(i * i)
    i += 1  # albo i = i + 1
```

{pagebreak}

### Використання циклів for для чисел

**Випиши 5 послідовних чисел, починаючи з 0.**

```python
for i in range(5):
    print(i)
```

**Випиши по черзі числа, починаючи з 5, які менші, ніж 10.**

```python
for i in range(5, 10):
    print(i)
```

**Випиши кожне четверте число від 5 до 30.**

```python
for i in range(5, 30, 4):
    print(i)
```
	
**Випиши числа від 10 до 5 (без 5).**

```python
for i in range(10, 5, -1):
    print(i)
```

**Випиши кожне друге число від 20 до 0 (без 0).**

```python
for i in range(20, 0, -2):
    print(i)
```

### Функції

```python
def print_sum(a, b):
    print(a + b)


def print_numbers(a, b):
    for i in range(a, b + 1):
        print(i)
```

```python
def print_stars(num):
    stars = ""
    for i in range(num):
        stars += "*"
    print(stars)


def print_square(size):
    for i in range(size):
        print_stars(size)


def print_triangle(size):
    for i in range(size):
        print_stars(i + 1)
```

### Функції, які повертають значення

```python
def days_to_millis(days):
    return days * 24 * 60 * 60 * 1000


def triangle_area(a, b):
    return a * b / 2


def biggest(a, b, c):
    if a >= b and a >= c:
        return a
    elif b >= c:
        return b
    else:
        return c
```

```python
def sum_range(a, b):
    res = 0
    for i in range(a, b):
        res += i
    return res


# рекурентне рішення
def sum_range(a, b):
    if b <= a:
        return 0

    return sum_range(a + 1, b) + a
```

### Називання класів

- `Personal_Invoice` — в назвах класів не ставиться підкреслення між словами. Краще використати назву `PersonalInvoice`.
- `UserAddress` — правильна назва.
- `Carengine` — "car" і "engine" — окремі слова. Краще використати назву `CarEngine`.
- `doctor` — назви класів повинні починатися з великої літери.
- `Dog` — правильна назва.

### Класи та атрибути

```python
class Player:
    pass


player = Player()
player.points = 0
print(player.points)  # 0
player.points = 1
print(player.points)  # 1
```

{pagebreak}

### Банківський рахунок

Вдалою назвою було би `BankAccount`.

```python
class BankAccount:
    def __init__(self):
        self.balance = 0

    def deposit(self, amount):
        self.balance += amount

    def withdraw(self, amount):
        if amount <= self.balance:
            self.balance -= amount
            return True
        else:
            return False


account1 = BankAccount()
account2 = BankAccount()
account1.deposit(1000)
print(account1.balance)  # 1000
print(account2.balance)  # 0
```

### Створення і редагування списків

```python
values = [True, 42, "AAA"]
print(values.pop())  # "AAA"
print(values.pop())  # 42
values.append(88)
print(values)  # [True, 88]

pets = ["dog", "cat"]
pets.pop()
pets.append("pig")
print(pets)  # ["dog", "pig"]
pet = pets.pop()
print(pets)  # ["dog"]
print(pet)  # "pig"
```

### Звернення до елементів списку

```python
names = ["Ада", "Бартек", "Чарек", "Дарія", "Ева"]

print(names[0])  # Ада
print(names[3])  # Дарія
print(names[-1])  # Ева
print(names[-2])  # Дарія
print(names[1])  # Бартек
print(names[-4])  # Бартек
```

### Звернення до елементів за допомогою діапазону

```python
names = ["Ада", "Бартек", "Чарек", "Дарія", "Ева"]

print(names[0:2])  # ['Ада', 'Бартек']
print(names[2:4])  # ['Чарек', 'Дарія']
print(names[:3])  # ['Ада', 'Бартек', 'Чарек']
print(names[3:-1])  # ['Дарія']
print(names[1:])  # ['Бартек', 'Чарек', 'Дарія', 'Ева']
print(names[1:3])  # ['Бартек', 'Чарек']
print(names[-3:])  # ['Чарек', 'Дарія', 'Ева']
print(names[1:-2])  # ['Бартек', 'Чарек']
print(names[:])  
# ['Ада', 'Бартек', 'Чарек', 'Дарія', 'Ева']
```

### Зміна елементів списку

```python
names = ["Ада", "Бартек", "Чарек"]

names[2] = "Інжир"
print(names)  # ['Ада', 'Бартек', 'Інжир']

names[-2] = "Геральт"
print(names)  # ['Ада', 'Геральт', 'Інжир']

names = ["Ада", "Бартек", "Чарек"]

names[:1] = []
print(names)  # ['Бартек', 'Чарек']

names[2:] = ["Ева", "Галина"]
print(names)  # ['Бартек', 'Чарек', 'Ева', 'Галина']

names[1:2] = ["Іза", "Ян"]
print(names)  
# ['Бартек', 'Іза', 'Ян', 'Ева', 'Галина']

names[:-1] = ["Кася"]
print(names)  # ['Кася', 'Галина']
```

### Розмір і елементи списку

```python
letters = ['A', 'B', 'C', 'D', 'E', 'F']
print(len(letters))  # 6
print(letters[4])  # E

numbers = [5, 6, 7, 8, 9, 10, 11, 12]
print(len(numbers))  # 8
print(numbers[3])  # 8
```

### Перевірка наявності елемента в списку

```python
letters = ["A", "C", "E"]

print("A" in letters)  # True
print("B" in letters)  # False
print("C" in letters)  # True

letters[1] = "F"
print("F" in letters)  # True
print("C" in letters)  # False
```

### Додавання та копіювання списків

```python
l1 = ["A", "B"]
l2 = ["D", "E"]
letters = l1 + l2
l1Copy = l1[:]
print(letters)  # ['A', 'B', 'D', 'E']

l1.append("C")
print(l1)  # ['A', 'B', 'C']
print(letters)  # ['A', 'B', 'D', 'E']
print(l1Copy)  # ['A', 'B']
```

### Тапли

```python
values = (True, 42, "AAA")
print(values[1])  # 42
print(values[2])  # AAA
print(values[:1])  # (True,)

empty = tuple()
print(len(empty))  # 0
single = ("AAA",)
print(single)  # ('AAA',)
print(len(single))  # ('AAA') <!-- # 1 -->
```

### Ітерація та генераторні списки

```python
names = ["міхал", "неля", "оля", "пшемек"]

upper = [name.capitalize() for name in names]
print(upper)  # ['Міхал', 'Неля', 'Оля', 'Пшемек']

girls = [name.capitalize() for name in names
         if name[-1] == "a"]
print(girls)  # ['Неля', 'Оля']

count = 0
for name in names:
    count += len(name)
print(count)  # 20
```

### `__str__`, `__repr__` i `__eq__`

```python
class Money:
    def __init__(self, amount, currency):
        self.amount = amount
        self.currency = currency

    def __eq__(self, other):
        return isinstance(other, Money)
               and other.amount == self.amount
               and other.currency == self.currency

    def __str__(self):
        return f"{self.amount} {self.currency}"

    def __repr__(self):
        return "Money(" +
               f"amount={repr(self.amount)}, " +
               f"currency={repr(self.currency)})"
```
