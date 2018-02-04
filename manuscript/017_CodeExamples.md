# O przykładach kodu

## Uwagi dla programistów języka C#

Językiem, który wybrano dla przykładów jest C#, aczkolwiek zrobiłem kilka wyjątków od typowej C# konwencji.

### Usunięcie "I" z nazw interfejsów

Osobiście, nie jestem fanem używania `ISomething` dla nazw interfejsu, więc zdecydowałem się nie umieszczać przedrostka `I` nawet jeśli większość programistów C# by tego oczekiwała. Mam nadzieję, że tym razem mi wybaczą.

### Konstrukcje języka charakterystyczne dla C\#

Większość kodu w tej książce to nie jest typowy C# ze swoimi idiomami. Próbowałem unikać właściwości (properties), zdarzeń (events) i większości nowoczesnych funkcji. Moim celem jest umożliwienie użytkownikom innych języków (zwłaszcza Javy) korzystanie z tej książki.

### Używanie podkreśleń w nazwach pól

Niektórzy to lubią, inni nie. Postanowiłem trzymać konwencję umieszczania znaku podkreślenia (`_`) przed nazwą pola klasy.

##  Uwagi dla programistów języka Java

Językiem, w którym ukazano przykłady kodu, jest C#. Zaznaczyłem jednak, że chcę, aby książka była jak najmniej związana z konkretną technologią, by umożliwić programistom języka Java czerpanie z niej korzyści. Próbowałem używać minimalnej liczby funkcji specyficznych dla C#, a w kilku miejscach nawet robiłem uwagi skierowane do programistów Java, aby im ułatwić zrozumienie. Jest jednak kilka rzeczy, których nie mogłem uniknąć, toteż stworzyłem listę opisującą kilka różnic między Javą i C#, co może się przydać.

### Konwencja nazewnictwa

Większość języków ma swoje domyślne konwencje nazewnictwa. Na przykład w języku Java nazwa klasy jest zapisana za pomocą PascalCase (np. `UserAccount`), metody i pola są zapisywane za pomocą camelCase, np. "payTaxes" a stałe/pola tylko do odczytu są zapisywane wielkimi literami z podkreśleniem (np. `CONNECTED_NODES`).

C# używa pascalCase dla nazw klas i metod (np. `UserAccount`,` PayTaxes`, `ConnectedNodes`). W przypadku pól istnieje kilka konwencji nazewnictwa. Wybrałem tą, która zaczyna się od znaku podkreślenia (np. `_myDependency`). Są jeszcze inne drobne różnice, ale z tymi się spotkasz najczęściej.

### słowo kluczowe `var`

By uczynić zapis bardziej zwięzłym, zdecydowałem się użyć  w przykładach słowa kluczowego `var`. To słowo kluczowe służy do automatycznego wnioskowania jakiego typu jest zmienna, np.

```csharp
var x = 123; // wnioskuję, że x jest liczbą całkowitą
```

Oczywiście, to nie jest typowanie dynamiczne (dynamic typing) - wszystko jest określane na etapie kompilacji.

Jeszcze jedno - słowo kluczowe "var" może być użyte tylko wtedy, kiedy można wywnioskować z jakim typem mamy do czynienia, w nnych wypadkach musiałem deklarować typy jawnie, jak w przypadku:

```csharp
List<string> list = null; //nie można użyć var by wnioskować jakiego typu jest lista
```

### słowo kluczowe `string`

C# ma typ `String`, podobnie jak Java. C# pozwala jednak na wpisanie nazwy tego typu jako słowa kluczowego, np. `string` zamiast` String`. To jest tylko lukier składniowy (syntactic sugar), który jest domyślnie używany przez społeczność C#.

### Atrybuty zamiast adnotacji

W języku C# istnieją atrybuty. Są używane w tym samym celu, co adnotacje (anntoations) w Javie. Tak więc, gdy widzisz:

```csharp
[Whatever]
public void doSomething()
```

myśl:

```java
@Whatever
public void doSomething()
```

### `readonly` i `const` w miejscu `final`

Tam, gdzie w Javie używa słowa `final` dla stałych (wraz z `static`) i pól tylko do odczytu, w C# używa się dwóch słów kluczowych:` const` i `readonly`. Bez wchodzenia w szczegóły, za każdym razem, gdy zobaczysz coś takiego:

```csharp
public class User
{
  // a constant with literal:
  private const int DefaultAge = 15;

  // a "constant" object:
  private static readonly TimeSpan DefaultSessionTime
    = TimeSpan.FromDays(2);

  // a read-only instance field:
  private readonly List<int> _marks = new List<int>();
}
```

myśl:

```java
public class User {
  //a constant with literal:
  private static final int DEFAULT_AGE = 15; 

  //a "constant" object:
  private static final Duration 
    DEFAULT_SESSION_TIME = Duration.ofDays(2);

  // a read-only instance field:
  private final List<Integer> marks = new ArrayList<>();
}
```

### Konstrukcja `List<T>`

Jeśli jesteś programistą języka Java to zauważ, że w C# `List<T>` nie jest klasą abstrakcyjną, ale konkretną. Ten typ jest zwykle używany tam, gdzie Ty używałbyś `ArrayList`

### Typy uogólnione (generyczne)

Jedną z największych różnic między Javą i C # jest to, jak traktuje się typy generyczne. Po pierwsze, C# pozwala na używanie typów prostych (prymitywnych) w deklaracjach typów uogólnionych, więc możesz napisać `List<int>` w języku C#, podczas gdy w Javie musisz napisać `List<Integer>`.

Inną różnicą jest to, że w języku C# nie ma wymazywania typów, tak jak w Javie. Kod napisany w C# zachowuje wszystkie informacje o typie generycznym w czasie wykonywania. To istotnie wpływa na to, jak są projektowane i jak można używać API korzystającego z typów generycznych.

Definicja klas generycznych i ich tworzenie, w Javie i C# wyglądają mniej więcej tak samo. Istnieje jednak różnica na poziomie metody. Metoda generyczna w Javie wygląda tak:


```java
public <T> List<T> createArrayOf(Class<T> type) {
    ...
}
```

a używamy jej tak:

```java
List<Integer> ints = createArrayOf(Integer.class);
```

podczas, gdy w C# ta sama metoda będzie zdefiniowana tak:

```java
public List<T> CreateArrayOf<T>()
{
    ...
}
```

i używana w taki sposób:

```csharp
List<int> ints = CreateArrayOf<int>();
```
Różnice te są widoczne w projekcie biblioteki, używanej w tej książce do generowania danych testowych[^anylibraryauthor]. W wersji C#, generujemy dane testowe, pisząc:

```csharp
var data = Any.Instance<MyData>();
```

 w jezyku Java zaś:

```java
MyData data = Any.instanceOf(MyData.class);
```
[^anyibraryauthor]: Również autorstwa Grzegorza Gałęzowskiego - przypis tłumacza