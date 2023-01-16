# Klasy 

## Docstring

Niektóre funkcje potrafią być naprawdę skomplikowane. W takich wypadkach sama ich nazwa może nie być wystarczająco jasna. Aby wyjaśnić znaczenie takich funkcji, na samym początku ich ciała umieszczamy cudzysłowy tworzące wartość typu string. Taka wartość nazywana jest Docstring i&nbsp;ma szczególne znaczenie w&nbsp;języku Python. Jeśli na podstawie naszego kodu będziemy generowali jego dokumentację (koncept podobny do&nbsp;instrukcji obsługi), to Docstring zostanie użyty do&nbsp;opisania tej funkcji. 

```python
def kos_root():
    """Return the pathname of the KOS root directory."""
    if _kos_root: 
        return _kos_root
    # ...
```

Także w&nbsp;kodzie możemy pobrać Docstring dowolnej funkcji poprzez atrybut `__doc__`[^107_2]. 

```python
print(kos_root.__doc__)
# Return the pathname of the KOS root directory.
```

## Określanie typów

Wiele z napisanych przez nas funkcji spodziewała się argumentów określonego typu. Przykładowo `cheer` spodziewało się, że przekażemy wartość typu `str`, a&nbsp;`print_stars` (z zadania) spodziewało się wartości typu `int`. Przypadkowe wywołanie tych funkcji z niepoprawnymi wartościami doprowadzi do&nbsp;błędu. Stąd powstaje istotna kwestia jak temu przeciwdziałać. Przez długi czas stosowane rozwiązanie polegało na opisywaniu w&nbsp;Docstring jakich typów spodziewamy się po&nbsp;danej funkcji. Nie było to jednak wystarczająco pomocne, gdyż programiści wciąż popełniali błędy. W związku z tym w&nbsp;późniejszych wersjach języka Python wprowadzona została możliwość określania oczekiwanego typu po&nbsp;dwukropku obok parametru. 

```python
def cheer(who_to_cheer: str):
    print("Siema " + who_to_cheer + "!")

def print_stars(num: int):
    stars = ""
    for i&nbsp;in range(num):
        stars += "*"
    print(stars)
```

Dla przypomnienia poznane przez nas typy to:
 - `str` - czyli stringi (wartości tekstowe),
 - `int` - czyli liczby całkowite,
 - `float` - czyli liczby rzeczywiste (z częścią dziesiętną po&nbsp;kropce),
 - `bool` - czyli wartości logiczne (`True` oraz `False`).

Określanie typów w&nbsp;ten sposób nie tylko jest łatwiejsze niż opisywanie ich w&nbsp;Docstring, ale także zapewnia większe bezpieczeństwo. Gdybyśmy użyli wartości niepoprawnego typu, środowisko programistyczne takie jak PyCharm poinformuje nas o tym błędzie. 

{width: 70%}
![](107_type_error.png)

Możemy również określić typ wartości zwracanej z funkcji. W takim przypadku pomiędzy zamknięciem nawiasu a&nbsp;dwukropkiem stawiamy `->` oraz określany typ. 

```python
def factorial(num: int) -> int:
    if num <= 1:
        return 1

    return factorial(num - 1) * num
```

Określanie tych typów jest nieobowiązkowe, niemniej jest przydatną informacją zarówno dla programistów czytających nasz kod, jak i&nbsp;środowiska (na przykład PyCharm), który na podstawie tej informacji daje nam lepsze podpowiedzi lub ostrzeżenia.

### Ćwiczenie: Poprawa jakości funkcji

Poniższa funkcja wymaga następujących zmian:
 - Dodania domyślnych wartości dla parametrów (w&nbsp;obu przypadkach powinno być to `False`).
 - Określenia typów parametrów oraz wartości zwracanej. 
 - Dodania Docstring o wartości "Calculates the optimal dog walking distance."

```python
def dog_walk_distance(
        is_evening, 
        listening_podcast
):
    distance = 500
    if listening_podcast:
        distance += 2000
    if is_evening:
        distance += 1000
    return distance
```

Tak powinna działać nasza funkcja po&nbsp;wprowadzonych zmianach:

```python
print(dog_walk_distance())  # 500
print(dog_walk_distance(is_evening = True))  # 1500
print(dog_walk_distance(listening_podcast = True))  
# 2500
print(dog_walk_distance(True, True))  # 3500
print(dog_walk_distance.__doc__)  
# Calculates the optimal dog walking distance.
```

Odpowiedzi [na końcu książki](https://kt.academy/pl/article/py-rozwiazania).

### Poprawa jakości funkcji

```python
def dog_walk_distance(
        is_evening: bool = False,
        listening_podcast: bool = False
) -> int:
    """Calculates the optimal dog walking distance."""
    distance = 500
    if listening_podcast:
        distance += 2000
    if is_evening:
        distance += 1000
    return distance
```