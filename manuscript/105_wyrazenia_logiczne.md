# Логічні вирази

Поки що ми бачили лише дуже прості умови, які перевіряли значення однієї змінної. Як поводитися в ситуації, коли ми хочемо виконати дію, в якій виконуються дві умови одночасно? Наприклад:
* твіт рекламується **і** походить від особи, за якою користувач стежить;
* твіт користувача **або** особи, за якою користувач стежить.

Щоб отримати відповіді на такі запитання, нам потрібні логічні оператори `and`, `or` і `not`, які виконують операції з логічними значеннями (подібно до того, як `+`, `-` і `*` виконують дії з числами).

## Оператор `and`

Щоб добре пояснити логічні оператори, наведу історію маленького Мишка. Він хотів погратися на своєму комп’ютері, тож пішов до мами, щоб запитати дозволу. Вона сказала: "Ти зможеш погратися, коли зробиш домашнє завдання **і** прибереш кімнату".

{width: 82%}
![](104_mama.png)

Ця умова в програмуванні може бути виражена таким чином:

```python
can_play_games = finished_homework and cleaned_room
```

Ми бачимо там змінні `finished_homework` і `cleaned_room`, які представляють відповідно виконання домашнього завдання та прибирання кімнати (ми припускаємо, що це логічні значення, тобто `True` або `False`). Між ними бачимо оператор `and` [^105_1], завдяки якому весь вираз буде `True`, тільки якщо обидві його частини будуть `True` [^105_2].

| a | b | a and b |
|:-----:|:-----:|:-------:|
| True | True | True |
| True | False | False |
| False | True | False |
| False | False | False |

```python
print(True and True)  # True
print(True and False)  # False
print(False and True)  # False
print(False and False)  # False

# Чи може Мишко завести щеня?
have_time = True
is_responsible = False
can_have_puppy = is_responsible and have_time
print(can_have_puppy)  # False
```

Прикладом використання цього оператора є перевірка, чи число розміщене в певному діапазоні, тобто чи воно більше ніж нижня межа **і** менше ніж верхня межа. Наприклад, ми можемо перевірити, чи змінна `percent` має правильне значення, тобто міститься між 0 і 100.

```python
if percent >= 0 and percent <= 100:
  print("Правильне значення")
```

Якщо `percent` буде від 0 до 100, відобразиться "правильне значення". В іншому ж випадку не відбудеться нічого.

## Оператор `or`

На жаль, Мишко не зробив домашнього завдання, тому не зміг погратися на комп’ютері. Але потім до нього подзвонили друзі і запросили в кіно. Мишко пішов до мами та попросив грошей, щоб піти. Вона сказала: "Я дам тобі гроші, якщо ти прибереш гараж **або** помиєш машину".

{width: 82%}
![](104_mama_albo.png)

Цю умову можна представити таким чином:

```python
can_go_to_cinema = cleaned_garage or washed_car
```

Оператор `or` [^105_3] представляє альтернативу. Весь вираз повертає `True`, якщо виконується або `cleaned_garage`, або `washed_car`, або обидві умови одночасно.

| a | b | a or b |
|:-----:|:-----:|:------:|
| True | True | True |
| True | False | True |
| False | True | True |
| False | False | False |

```python
print(True or True)  # True
print(True or False)  # True
print(False or True)  # True
print(False or False)  # False

# Чи отримає Мишко шоколадку?
behaved_well = True
cleaned_room = False
can_eat_chocolate = behaved_well or cleaned_room
print(can_eat_chocolate)  # True
```

Мишко так запрацювався, що і гараж прибирав, і машину помив. Здивована мама сказала: "Я хотіла, щоб ти зробив або те, або інше, а не і одне, і друге". Мишко відповів: "Ти використала сполучник *або*, який, серед іншого, може означати виконання і одного, і іншого, так само як `or` у програмуванні". Мама Мишка погодилася з цим поясненням, тому він пішов з друзями в кінотеатр.

## Оператор `not`

Останній логічний оператор, який ми повинні знати, — це заперечення, тобто `not`, перед логічним значенням. Слово "not" означає "ні". Подібно до `-` (мінуса) перед числом, який замінює його знак на протилежний, `not` перед логічним значенням перетворює його на зворотне. Оператор заперечення `not` змінює `True` на `False`, а `False` на `True`.

| `cond` | `!cond` |
|:------:|:-------:|
| True | False |
| False | True |

```python
print(not True)  # False
print(not False)  # True

is_amazing = True
print(not is_amazing)  # False

is_boring = False
print(not is_boring)  # True

# Як із числами
positive = 1
print(-positive)  # -1

negative = -1
print(-negative)  # 1
```

Програмування допускає подвійне заперечення, що означає, що кожен послідовний знак заперечення змінює логічне значення на протилежне [^105_4].

```python
print(True) # True
print(not True)  # False
print(not not True)  # True
print(not not not True)  # False
print(not not not not True)  # True
```

Оператор заперечення `not` часто ставиться у складнішому виразі перед окремою змінною. Мишко зрозумів це, коли мама сказала: "Ти зможеш погратися на комп’ютері, якщо не завалиш іспит з математики і прибереш кімнату". Він використав заперечення, щоб перекласти це речення на Python.

```python
can_play_games = not failed_math_test and cleaned_room
```

Зверни увагу, що заперечення тут стосується лише нездачі іспиту. `not True and False` повертає `False`, тому що спочатку `not True` повертає `False`, а потім `False and False` повертає `False`. Як і з числами `-10 + 11` дорівнює `1`, а не `-21`. Якщо ми хочемо, щоб заперечення охоплювало більшу область виразу, ми повинні взяти весь фрагмент у круглі дужки. `not (True and False)` повертає `True`, тому що спочатку `True and False` повертає `False`, яке потім заперечується через `not`.

```python
# Іспит зданий
failed_math_test = False

# Кімната неприбрана
cleaned_room = False

# Не завалив іспит і прибрав кімнату
print(not failed_math_test and cleaned_room)  # False

# Не є правдою, що 
# завалив іспит і прибрав кімнату
print(not (failed_math_test and cleaned_room))  # True
```

## Читання логічних виразів

Варто потренуватися читати логічні вирази вголос. Зазвичай я роблю це так:

- `and` я читаю як **і**;
- `or` я читаю як **або**;
- `not ` перед змінною я читаю як **ні**, а перед дужками як **не є правдою, що** або **неправда, що**.

Потренуймося на наведених нижче прикладах для змінних:

- `is_grounded` як "заборонено виходити";
- `cleaned room` як "прибрав кімнату";
- `passed_test` як "здав іспит".

Прочитай такі речення:

- `cleaned_room and passed_test` — прибрав кімнату і здав іспит.
- `not is_grounded or passed_test` — не заборонено виходити або здав іспит.
- `cleaned_room and not passed_test` — прибрав кімнату і не здав іспит.
- `not (is_grounded or not passed_test)` — не є правдою, що заборонено виходити або не здав іспит.
- `not (not cleaned_room and not passed_test)` — неправда, що не прибрав кімнату і не здав іспит. Це означає те саме, що й `cleaned_room or passed_test`.

> `not (not a and not b)` завжди однозначне `a or b`, а `not (not a or not b)` — рівнозначне з `a and b`. Такі перетворення використовуються, щоб позбутися складного для прочитання заперечення. Наприклад, якби у нас була умова `not (has_computer and not is_grounded)`, ми могли б використати факт, що `not (not a and not b)` — це `a or b`, і скоротити вираз до `not has_computer or is_grounded` (`not` перед `is_grounded` зникає, бо `not not a` — це просто `a`).

### Вправа: Складні логічні вирази

Що буде виведено в результаті роботи цього коду?

```python
has_computer = True
passed_test = False
is_grounded = False

print(has_computer and passed_test)
print(passed_test or is_grounded)
print(has_computer and not passed_test)

can_play_games = has_computer and not is_grounded
print(can_play_games)

play_games = has_computer and can_play_games

if not passed_test:
  is_grounded = True

print(play_games)
print(passed_test or not is_grounded)
print(not (not has_computer or not passed_test))
```

Відповіді [в кінці книги](https://kt.academy/pl/article/py-rozwiazania).

[^105_1]: "and" з англійської перекладається як "i".
[^105_2]: Точніше кажучи, увесь вираз truthy, коли обидві частини truthy. Однак ми не будемо говорити про значення як truthy або falsy.
[^105_3]: "or" з англійської перекладається як "або".
[^105_4]: Граючись мовою, ми могли б сказати, що неправда, що програмування не приймає подвійних заперечень. І навіть (не не не не) допускає багаторазові заперечення.



