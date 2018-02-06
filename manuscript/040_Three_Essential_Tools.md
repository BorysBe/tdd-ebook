# Niezbędne narzędzia {#chapter-essential-tools}

Czy kiedykolwiek oglądałeś film Karate Kid, starą lub nową wersję? W obu chodzi o to, że kiedy "dzieciak" zaczyna uczyć się od swojego mistrza karate (lub kung-fu), otrzymuje najprostsze zadanie do powtarzania (jak zdjęcie kurtki i założenie jej), nie wiedząc jeszcze po co to robi i gdzie go to zaprowadzi. ALbo, włącz sobie pierwszy film Rocky (tak, ten z udziałem Sylvestra Stallone'a), w którym Rocky ściga kurczaka, aby trenować zwinność.

Kiedy po raz pierwszy próbowałem nauczyć się grać na gitarze, znalazłem dwie porady w Internecie: pierwszą było opanowanie pojedynczego, trudnego utworu. Druga rada polegała na graniu na jednej strunie, nauczeniu się jak ta struna może brzmieć i próbie zagrania melodii ze słuchu właśnie na tej jednej strunie. Chyba nie muszę dodawać, że ta druga rada działała lepiej?

Szczerze mówiąc, mógłbym od razu rozpocząć od podstawowych technik TDD, ale wydaje mi się, że byłoby to tak, jakbym postawił Cię na ringu z bardzo wymagającym przeciwnikiem - prawdopodobnie zniechęciłbyś się przed zdobyciem niezbędnych umiejętności. Zamiast wyjaśniać, jak się wygrywa wyścigi, w tym rozdziale przyjrzymy się raczej, jakie błyszczące samochody będziemy prowadzić.

Innymi słowy, zaprezentuję króko trzy narzędzia, z których będziemy korzystać w tej książce

W tym rozdziale upraszczam niektóre rzeczy tylko po to, abyś zaczął działać bez wchodzenia w filozofię TDD (skojarz: lekcje fizyki w szkole podstawowej). Nie przejmuj się :-), nadrobię to w nadchodzących rozdziałach!

## Test framework

Pierwszym narzędziem, które wykorzystamy, będzie test framework (framework testujący). *W języku polskim nie ma odpowiednika słowa "framework", najbliżej jest słowo "platforma" lub "struktura". Framework to coś, co wyznacza pewne ramy, definiuje struktury do wykorzystania, dostarcza biblioteki, funkcje - w tym przypadku funkcje pomagające w pisaniu i wykonaniu testów.*

Załóżmy, na potrzeby naszego wprowadzenia, że mamy aplikację, która przyjmuje dwie liczby z linii poleceń, mnoży je i wypisuje wynik na konsoli. Kod jest dość prosty:

```csharp
public static void Main(string[] args) 
{
  try
  {
    int firstNumber = Int32.Parse(args[0]);
    int secondNumber = Int32.Parse(args[1]);

    var result = 
      new Multiplication(firstNumber, secondNumber).Perform();

    Console.WriteLine("Result is: " + result);
  }
  catch(Exception e)
  {
    Console.WriteLine("Multiplication failed because of: " + e);
  } 
}
```

Teraz załóżmy, że chcemy sprawdzić, czy program daje prawidłowe wyniki. Najbardziej oczywistym sposobem sprawdzenia byłoby wywołanie go z linii poleceń - ręcznie - za pomocą kilku przykładowych argumentów, następnie sprawdzenie wyników programu na konsoli i porównanie ich z tym, co czekaliśmy. Taka sesja testowa może wyglądać następująco:

```text
C:\MultiplicationApp\MultiplicationApp.exe 3 7
21
C:\MultiplicationApp\
```

Jak widać, nasz program daje wynik 21 dla mnożenia 3 przez 7. Jest to poprawne, więc zakładamy, że program zdał test.

Co się stanie, jeśli program będzie miał również zaimplementowane dodawanie, odejmowanie, dzielenie, całkowanie itp.? Ile razy będziemy musieli go ręcznie wywołać na różne sposoby, by upewnić się, że każda operacja, po naszych zmianach, wciąż działa poprawnie? Czy nie byłoby to czasochłonne? Ale czekaj, jesteśmy programistami, prawda? Tak więc możemy napisać programy do testowania dla nas! Poniżej znajdziesz kod źródłowy innej aplikacji, który używa naszej klasy Multiplication, ale w nieco inny sposób niż robił to nasz wcześniejszy program:

```csharp
public static void Main(string[] args) 
{
  var multiplication = new Multiplication(3,7);

  var result = multiplication.Perform();

  if(result != 21)
  {
    throw new Exception("Failed! Expected: 21 but was: " + result);
  }
}
```

Wygląda prosto, prawda? Teraz na tym kodzie oprzemy bardzo prymitywny szkielet testowy - by pokazać fragmenty, z których składają się frameworki testujące. Pierwszym krokiem w tym kierunku będzie wyodrębnienie sprawdzenia wyniku (`result`) do metody, którą będzie można używać wielokrotie. Po tym wszystkim, w mgnieniu oka dodamy do aplikacji dzielenie, pamiętasz? No to jedziemy:

```csharp
public static void Main(string[] args) 
{
  var multiplication = new Multiplication(3,7);
  
  var result = multiplication.Perform();
  
  AssertTwoIntegersAreEqual(expected: 21, actual: result);
}

// Wyodrębiony kod:
public static void AssertTwoIntegersAreEqual(
  int expected, int actual)
{
  if(actual != expected)
  {
    throw new Exception(
      "Failed! Expected: " 
        + expected + " but was: " + actual);
  }
}
```

Zauważ, że nazwę tej wyodrębnionej metody zacząłem od "Assert" - wkrótce wrócimy do nazewnictwa, na razie przyjmijmy, że jest to dobra nazwa dla metody, która sprawdza, czy wynik pasuje do naszych oczekiwań. Ostatnim krokiem będzie wyodrębnienie samego test, aby jego kod był w osobnej metodzie. Tej metodzie nadamy nazwę opisującą, co sprawdza ten test:

```csharp
public static void Main(string[] args)
{
  // Oczekujemy iloczynu dwóch liczb przekazanych do aplikacji
  Multiplication_ShouldResultInAMultiplicationOfTwoPassedNumbers();
}

public void 
Multiplication_ShouldResultInAMultiplicationOfTwoPassedNumbers()
{
  //Zakładając, że...
  var multiplication = new Multiplication(3,7);

  //Kiedy dzieje się coś takiego:
  var result = multiplication.Perform();

  //Wtedy wynik powinien być taki...
  AssertTwoIntegersAreEqual(expected: 21, actual: result);
}

public static void AssertTwoIntegersAreEqual(
  int expected, int actual)
{
  // Sprawdzamy, że podane liczby całkowite (w tym przypadku oczekiwana i zwrócona) są sobie równe.
  if(actual != expected)
  {
    throw new Exception(
    "Failed! Expected: " + expected + " but was: " + actual);
  }
}
```

I to wszystko. Teraz, jeśli potrzebujemy kolejnego testu, np. dla dzielenia, możemy po prostu dodać kolejne wywołanie, innej metody testujacej do `Main()` a następnie zaimplementować tę metodę. Wewnątrz nowego testu możemy ponownie użyć metody `AssertTwoIntegersAreEqual()`, ponieważ sprawdzenie wyników dzielenia będzie również opierało się na porównania dwóch wartości całkowitych - oczekiwanej i tej faktycznie zwróconej.

Jak widzisz, możemy łatwo napisać zautomatyzowane testy, używając naszych prymitywnych metod. Takie podejście ma jednak pewne wady:

1. Za każdym razem, gdy dodajemy nowy test, musimy zaktualizować metodę `Main()` o wywołanie nowego testu. Jeśli zapomnimy tego, test nigdy nie zostanie uruchomiony. Na początku nie jest to wielka sprawa, ale gdy już będziemy mieć dziesiątki testów, trudno będzie zauważyć te niedodane.
2. Wyobraź sobie, że Twój system składa się z więcej niż jednej aplikacji - miałbyś problemy ze zbieraniem wyników testów z wszystkich aplikacji, z których składa się twój system.
3. Wkrótce będziesz musiał napisać wiele innych metod podobnych do `AssertTwoIntegersAreEqual()` -- ta tutaj porównuje dwie liczby całkowite, ale co jeśli chcemy sprawdzić inny warunek, np. czy jedna liczba całkowita jest większa od innej? Co by było, gdybyśmy chcieli sprawdzić równość nie dla liczb całkowitych, ale dla znaków, ciągów znaków, itp.? Co by było, gdybyśmy chcieli sprawdzić pewne właściwości kolekcji, np. czy kolekcja jest posortowana, lub czy wszystkie elementy w kolekcji są unikatowe?
4. Jeśli test się nie powiedzie, trudno będzie przenieść się od komunikatu na konsoli do odpowiedniego wiersza w kodzie źródłowym w twoim IDE. Czy nie byłoby łatwiej - kliknąć komunikat o błędzie i zostać przeniesionym do miejsca w kodzie, dzie wystąpił błąd?

Z tego względu i kilku innych, stworzono zaawansowane, zautomatyzowane narzędzia do testowania aplikacji - frameworki testujące, takie jak CppUnit (dla C++), JUnit (dla Javy) lub NUnit (C#). Frameworki testujące są w zasadzie oparte na tej samej idei, którą opisałem powyżej, ale jednocześnie nadrabiają wady naszego wcześniejszego, prymitywnego podejścia. Struktura i funkcjonalność tych framework'ów wywodzą się ze Smalltalk's SUnit, są określane jako rodzina testów **xUnit**.

Szczerze mówiąc, nie mogę się doczekać, by pokazać Ci jak będzie wyglądać nasz wcześniejszy test, napisany przy użyciu frameworka testujacego. Jednakże najpierw podsumujmy to, co się nam udało osiągnąć do tej pory. Wprowadźmy też pewną terminologię, która pomoże nam zrozumieć, w jaki sposób zautomatyzowane frameworki testujące rozwiązują nasze problemy:

1. Metoda `Main()` posłużyła nam jako lista testów (**Test List**) - miejsce, w którym decyduje się, które testy należy uruchomić.
2. Metoda `Multiplication_ShouldResultInAMultiplicationOfTwoPassedNumbers()` była naszą metodą testową (**Test Method**).
3. Metoda `AssertTwoIntegersAreEqual ()` jest asercją (**Assertion**) - warunkiem, który - gdy nie zostanie spełniony - kończy test niepowodzeniem.

Ku naszej radości, te trzy chwalebne elementy są również obecne, gdy używamy frameworka testującego. Ponadto są znacznie bardziej zaawansowane. Aby to zilustrować, oto (nareszcie!) ten sam test, który napisaliśmy powyżej, teraz używający frameworka testowego [xUnit.Net] (http://xunit.github.io/):

```csharp
[Fact] public void 
Multiplication_ShouldResultInAMultiplicationOfTwoPassedNumbers()
{
  //Zakładając, że...
  var multiplication = new Multiplication(3,7);

  //Kiedy dzieje się to:
  var result = multiplication.Perform();

  //Wtedy wyniki powinny być takie...
  Assert.Equal(21, result);
}
```

Patrząc na przykład widzimy, że metoda testu jest jedyną rzeczą, która pozostała - lista testów i asercja, które poprzednio mieliśmy, zniknęły. Cóż, prawdę mówiąc, one nie do końca znikają - po prostu framework testujący oferuje zastępstwa, które są o wiele lepsze - więc ich użyliśmy. Odświeżmy sobie trzy elementy z poprzedniej wersji testu, o których mówiłem, że teraz również będą obecne:

1. Lista testów (**Test List**) jest teraz tworzona automatycznie przez framework na podstawie wszystkich metod oznaczonych atrybutem `[Fact]`. Nie ma potrzeby zarządzać z poziomu kodu już żadnymi listami, dlatego znika metoda `Main()`.
2. Metoda testująca (**Test Method**) wciąż jest obecna i wygląda niemalże tak jak wcześniej.
3. Asercja (**Assertion**) przyjęła kształt statycznej metody `Assert.Equal()` -- xUnit.NET framework posiada szeroki zakres takich asercji, a więc użyłem jednej z nich. Oczywiście, nie ma przeszkód byś napisał swoją własną asercję, jeśli framework nie oferuje Ci tego, czego szukasz.

Uff, mam nadzieję, że to przejście do frameworka testującego okazało się w miarę bezbolesne dla Ciebie. Teraz ostatnia rzecz - skoro nie ma już metody `Main()`, to pewnie się zastanawiasz jakim cudem uruchamiamy te testy, prawda? Dobrze, wyjawię Ci ostatni sekret -- używamy zewnętrzenej aplikacji do tego celu (*po polsku można ją nazwać odpalaczem testów, po angielsku to* **Test Runner**) -- określamy które zestawy z testami (assemblies) chcemy załadować, rest runner uruchamia testy, tworzy raporty na podstawie wyników etc. Nasz odpalacz może przyjać wiele form, to może być aplikacja konsolowa, aplikacja z GUI albo plugin do IDE. Oto przykład test runner'a dostarczanego jako plugin do Visual Studio IDE, nazywającego się Resharper:

![Resharper test runner docked as a window in Visual Studio 2015 IDE](images/Resharper_Test_Runner.PNG)

## Framework do mockowania

W> To wprowadzenie jest przeznaczone dla tych, którzy nie są biegli w używaniu mocków (*czytaj: "moków", `mock` to kolejny angielskojęzyczny termin w informatyce, który nie ma swojego odpowiednika w jęzku polskim - dosłownie tłumacząc z angielskiego, mock to imitacja*). Mocki mogą nie być najłatwiejsze do zrozumienia i dlatego jestem w stanie zaakceptować, jeśli na razie będziesz miał problemy z uchwyceniem tej koncepcji. Jeśli, podczas czytania tego wprowadzenia, zgubisz się - nie zwacaj na to w ogóle uwagi i idź dalej. Będziemy zajmować się mocno mockami w drugiej części książki, gdzie zagwarantuję Ci bogatszy i dokładniejszy opis.

Kiedy chcemy przetestować klasę, która zależy od innej klasy, możnaby sądzić, że dobrym pomysłem jest umieszczenie w teście również tej drugiej klasy. To, jednakże, nie pozwoli nam testować wyłącznie jednego obiektu, czy też małej grupy obiektów tak, byśmy mogli sprawdzić prawidłowe działanie najmniejszego fragmentu aplikacji. *Testujemy najmniejszy fragment programu, bo gdy test nie będzie przechodził, łatwiej będzie znaleźć w małym fragmencie miejsce i przyczynę wystąpienia błędu*. Jeśli sprawimy, iż nasza klasa nie zależy od innych klas, ale zależy raczej od abstrakcji w postaci interfejsów - możemy z łatwością implementować te interfejsy za pomocą specjalnych, "fałszywych" klas, stworzonych w taki sposób, by ułatwić nam testowanie. Na przykład obiekty takich klas mogą zawierać wstępnie zaprogramowane wartości zwracane dla metody zadeklarowanej w interfejsie. Mogą także zapamiętywać, które metody były wywoływane i zezwalać testowi na sprawdzenie, czy komunikacja między obiektem poddawanym testowi a jego zależnościami jest poprawna. 

To może nie mieć znaczenia w Twoim przypadku, ale preferowanym podejściem jest stworzenie imitacji obiektu na podstawie interfejsu, a nie klasy, ponieważ normalnie, jeśli podążasz za TDD (Test Driven Development), możesz napisać testy jednostkowe jeszcze przed napisaniem implementacji zależnych klas. Dlatego, nawet jeśli nie masz konkretnej klasy DataAccessImpl, nadal możesz używać interfejsu DataAccess.

 *Niekorzystanie z interfejsów skutecznie utrudnia TDD, bo zmusza nas do tworzenia zależnych klas wraz z ciałami metod, nawet wtedy, kiedy ich jeszcze nie potrzebujemy. Żeby skomplikować sprawę, dodam - że w Javie można, bez przeszkód, stworzyć imitacje na podstawie definicji klasy - nie da się tego samego równie bezproblemowo zrobić w C#. Warto zaznaczyć, że interfejs w C# nigdy nie będzie zawierał metod.*

Co więcej, frameworki do mockowania (imitowania) mają ograniczenia w imitowaniu klas, a niektóre frameworki pozwalają tylko na imitowanie interfejsów.

W dzisiejszych czasach możemy zdać się na narzędzia do generowania takiej "fałszywej" implementacji danego interfejsu, co pozwoli nam wykorzystać tę wygenerowaną implementację zamiast prawdziwego obiektu w testach. Dzieje się to na różny sposób, w zależności od języka. Czasami implementacje interfejsu mogą być generowane w czasie wykonywania (tak jak w Javie lub C#), czasami musimy polegać bardziej na generowaniu w czasie kompilacji (np. w C++).

Zawężając sprawę do samego C# - framework mockujący jest mechanizmem, który pozwala nam tworzyć obiekty-imitacje (zwane "mockami"), które kojarzone są z interfejsem, w czasie wykonywania. Działa to tak: typ interfejsu, który chcemy zaimplementować, jest zwykle przekazywany do specjalnej metody, która zwraca obiekt `mock` oparty na tym interfejsie (zobaczymy przykład w kilka sekund). Oprócz tworzenia imitacji obiektów, taki framework zapewnia interfejs API do określania, jak mocki powinny suę zachowywać podczas wywyływania określonych metod. To API pozwala nam również sprawdzić, które metody zostały wywołane. Jest to bardzo ważna funkcja, ponieważ możemy zasymulować takie sytuację lub zweryfikować takie warunki początkowe, które byłyby trudne do osiągnięcia przy użyciu kodu produkcyjnego. Frameworki do mockowania nie są tak stare jak frameworki do testowania, więc nie były używane w TDD od samego początku.

Teraz pokażę Ci krótki przykład użycia frameworka mockującego, natomiast przełożę dalsze wyjaśnienie do późniejsztch rozdziałów, ponieważ pełny opis mocków i ich miejsca w TDD nie jest taki łatwy do przekazania.

Załóżmy, że mamy klasę, która umożliwia składanie zamówień, a następnie umieszcza te zamówienia w bazie danych (za pomocą implementacji interfejsu o nazwie `OrderDatabase`). Dodatkowo, klasa obsługuje wszelkie wyjątki, które mogą wystąpić i zapisuje je do logu. Klasa sama w sobie nie robi żadnych ważnych rzeczy, ale spróbujmy wyobrazić sobie naprawdę mocno, że to ważna logika domenowa. Oto kod dla tej klasy:

```csharp
public class OrderProcessing
{
  OrderDatabase _orderDatabase; // OrderDatabase to interfejs
  Log _log;

  // pobieramy obiekt bazodanowy spoza klasy:
  public OrderProcessing(
    OrderDatabase database,
    Log log)
  {
    _orderDatabase = database;
    _log = log;
  }

  public void Place(Order order)
  {
    try
    {
      _orderDatabase.Insert(order);
    }
    catch(Exception e)
    {
      _log.Write("Could not insert an order. Reason: " + e);
    }
  }

   // reszta kodu...
}
```

Teraz wyobraź sobie, że musimy to przetestować -- jak to robimy? Już widzę, jak potrząsasz głową i mówisz: "Stwórzmy połączenie z bazą danych, wywołajmy metodę `Place()` i sprawdźmy, czy rekord jest poprawnie dodany do bazy danych". Jeśli to zrobimy, pierwszy test będzie wyglądał następująco:

```csharp
[Fact] public void ShouldInsertNewOrderToDatabaseWhenOrderIsPlaced()
{
  //GIVEN
  var orderDatabase = new MySqlOrderDatabase(); //użycie prawdziwej bazy danych
  orderDatabase.Connect();
  orderDatabase.Clean(); //posprzątaj po poprzednim teście
  var orderProcessing = new OrderProcessing(orderDatabase, new FileLog());
  var order = new Order(
    name: "Grzesiek", 
    surname: "Galezowski", 
    product: "Agile Acceptance Testing", 
    date: DateTime.Now,
    quantity: 1);

  //WHEN
  orderProcessing.Place(order);

  //THEN
  var allOrders = orderDatabase.SelectAllOrders();
  Assert.Contains(order, allOrders);
}
```

Na początku testu otwieramy połączenie z bazą danych i czyścimy wszystkie istniejące w niej zamówienia (więcej o tym wkrótce), następnie tworzymy obiekt zamówienia, wstawiamy go do bazy danych, a potem pobieramy wszystkie zamówienia z bazy. Na koniec sprawdzamy, czy zamówienie, które próbowaliśmy wprowadzić, znajduje się wśród wszystkich zamówień.

Dlaczego czyścimy bazę danych na początku testu? Pamiętaj, że baza danych trwale przechowuje dane. Jeśli nie wyczyścimy ich przed wykonaniem logiki testu, baza danych może już zawierać element, który próbujemy dodać, np. z poprzedniego wykonania się testu. Co więcej, baza danych może nie pozwolić nam na ponowne dodanie tego samego produktu, a test zakończy się niepowodzeniem. Ałaaa! To tak bardzo boli - chcieliśmy, aby nasze testy udowodniły, że coś działa, ale wygląda na to, że mogą zawieść nawet wtedy, gdy logika jest poprawnie zakodowana. Jakie zastosowanie miałby taki test, gdyby nie mógł nam odpowiedzieć na pytanie, czy zaimplementowana logika jest poprawna czy nie? Tak więc, aby upewnić się, że stan bazy danych jest taki sam za każdym razem, gdy uruchamiamy test, przed każdym uruchomieniem czyścimy bazę danych.

Czy teraz, kiedy test jest gotowy, dostaliśmy to, czego chcieliśmy? Wahałbym się, czy odpowiedzieć "tak". Jest kilka powodów:

1. Test będzie najprawdopodobniej wykonywał się wolno, ponieważ dostęp do bazy danych jest stosunkowo wolny. Nierzadko zdarza się, że w projekcie do wykonania jest ponad tysiąc testów. Nie chcę za każdym razem czekać pół godziny na wyniki, gdy uruchamiam testy jednostkowe. A ty?
2. Każdy, kto chce uruchomić ten test, musi skonfigurować specjalne środowisko, np. lokalną baza danych na swoim komputerze. Co, jeśli czyjaś konfiguracja różni się nieco od mojej? Co się stanie, jeśli schemat produkcyjnej bazy danych stanie się nieaktualny - czy każdy zdąży to zauważyć i zaktualizować schemat swoich lokalnych baz danych? Czy powinniśmy ponownie uruchomić nasz skrypt do tworzenia bazy danych tylko po to, aby upewnić się, że dysponujemy aktualnym schematem, dla którego można przeprowadzić testy?
3. Możemy nie być w stanie uruchomić systemu bazodanowego na naszym komputerze, jeśli docelowo ma on działać na jakiejś egzotycznej platformie, albo na urządzeniu mobilnym.
4. Zauważ, że test, który napisaliśmy, jest tylko jednym z dwóch nam potrzebnych. Nadal musimy napisać kolejny test dla scenariusza, gdzie wstawienie zamówienia kończy się rzuceniem wyjątku. Jak skonfigurować bazę danych, by rzuciła wyjątek? Jest to możliwe, ale wymaga znacznego wysiłku (np. usunięcie tabeli i odtworzeniu jej po teście - bo inne testy mogą wymagać tej tabeli do prawidłowego działania). To może doprowadzić nas do wniosku, że nie warto pisać testów w ogóle.

Teraz spróbujmy podejść do tego problemu w inny sposób. Załóżmy, że klasa `MySqlOrderDatabase` wysyłająca zapytania do bazy danych, jest już przetestowana (nie chcę jeszcze wdawać się w dyskusję na temat testowania zapytań do bazy danych - dojdziemy do tego w późniejszych rozdziałach) i załóżmy, że jedyną rzeczą, którą musimy przetestować, jest klasa `OrderProcessing` (pamiętajcie, staramy się naprawdę mocno wyobrazić, że jest tu zakodowana pewna poważna logika domenowa). W tej sytuacji możemy usunąć `MySqlOrderDatabase` z testu i zamiast tego stworzyć fałszywą implementację `OrderDatabase`. Będzie ona działała tak, jakby wykonywała prawdziwe połączenie z bazą danych, ale nie będzie w ogóle zapisywała tam informacji (zapisze wstawione rekordy na liście, w pamięci RAM komputera). Kod takiego udawanego połączenia może wyglądać tak:

```csharp
public class FakeOrderDatabase : OrderDatabase
{
  public Order _receivedArgument;

  public void Insert(Order order)
  {
    _receivedArgument = order;
  }

  public List<Order> SelectAllOrders()
  {
    return new List<Order>() { _receivedOrder };
  }
}
```

Zauważ, że klasa imitująca połączenie z bazą danych jest instancją klasy implementującą ten sam interfejs co `MySqlOrderDatabase`. Tak więc, możemy sprawić, że testowany kod użyje fałszywej bazy danych nawet o tym nie widząc.

Zastąpmy, w naszym teście, prawdziwe połączenia z bazą danych fałszywą implementacją:

```csharp
[Fact] public void 
ShouldInsertNewOrderToDatabaseWhenOrderIsPlaced()
{
  //GIVEN
  var orderDatabase = new FakeOrderDatabase();
  var orderProcessing = new OrderProcessing(orderDatabase, new FileLog());
  var order = new Order(
    name: "Grzesiek", 
    surname: "Galezowski", 
    product: "Agile Acceptance Testing", 
    date: DateTime.Now,
    quantity: 1);

  //WHEN
  orderProcessing.Place(order);

  //THEN
  var allOrders = orderDatabase.SelectAllOrders();
  Assert.Contains(order, allOrders);
}
```

Zauważ, że nie czyścimy obiektu fałszywej bazy danych, tak jak robiliśmy to z prawdziwą bazą danych, ponieważ tworzymy nowy obiekt za każdym razem, gdy test jest uruchamiany, a wyniki są przechowywane w innym miejscu pamięci dla każdej instancji. Test będzie teraz znacznie szybszy, ponieważ nie mamy już dostępu do prawdziwej bazy danych. Co więcej, możemy teraz łatwo napisać test na wypadek błędu przy dodawaniu nowego zamówienia. W jaki sposób? Po prostu zaimplementujemy kolejne połączenie z nieprawdziwą bazą danych w ten sposób:

```csharp
public class ExplodingOrderDatabase : OrderDatabase
{
  public void Insert(Order order)
  {
    throw new Exception();
  }

  public List<Order> SelectAllOrders()
  {
  }
}
```

Ok, na razie dobrze, ale teraz mamy dwie klasy połączeń z fałszywą bazą danych do utrzymania (i są szanse, że będziemy potrzebować ich jeszcze więcej). Każda metoda dodana do interfejsu `OrderDatabase` musi również zostać dodana do każdej z tych fałszywych klas. Możemy zaoszczędzić trochę kodu, czyniąc nasze imitacje nieco bardziej generycznymi, byśmy ich zachowania mogli konfigurować za pomocą wyrażeń lambda:

```csharp
public class ConfigurableOrderDatabase : OrderDatabase
{
  public Action<Order> doWhenInsertCalled;
  public Func<List<Order>> doWhenSelectAllOrdersCalled;

  public void Insert(Order order)
  {
    doWhenInsertCalled(order);
  }

  public List<Order> SelectAllOrders()
  {
    return doWhenSelectAllOrdersCalled();
  }
}
```

Teraz nie musimy tworzyć dodatkowych klas dla nowych scenariuszy, ale nasza składnia stała się bardziej uciążliwa. Oto jak konfigurujemy fałszywą bazę danych, by pamiętała i pozwalała odczytać wprowadzone zamówienie:

```csharp
var db = new ConfigurableOrderDatabase();
Order gotOrder = null;
db.doWhenInsertCalled = o => {gotOrder = o;};
db.doWhenSelectAllOrdersCalled = () => new List<Order>() { gotOrder };
```

A jeśli chcemy rzucić wyjątek, gdy coś jest wstawiane:

```csharp
var db = new ConfigurableOrderDatabase();
db.doWhenInsertCalled = o => {throw new Exception();};
```

Na szczęście niektórzy sprytni programiści stworzyli biblioteki, które zapewniają dalszą automatyzację w takich sytuacjach. Jedną z takich bibliotek jest [**NSubstitute**] (http://nsubstitute.github.io/). Zapewnia ona API w postaci metod rozszerzających (extension methods) C# - co może Ci się na początku wydawać się nieco magiczne, szczególnie jeśli nie znasz C#. Nie martw się, przyzwyczaisz się do tego.

Używając NSubstitute, nasz pierwszy test może zostać napisany w taki sposób:

```csharp
[Fact] public void ShouldInsertNewOrderToDatabaseWhenOrderisPlaced()
{
  //GIVEN
  var orderDatabase = Substitute.For<OrderDatabase>();
  var orderProcessing = new OrderProcessing(orderDatabase, new FileLog());
  var order = new Order(
    name: "Grzesiek", 
    surname: "Galezowski", 
    product: "Agile Acceptance Testing", 
    date: DateTime.Now,
    quantity: 1);

  //WHEN
  orderProcessing.Place(order);

  //THEN
  orderDatabase.Received(1).Insert(order);
}
```

Zauważ, że nie potrzebujemy już metody `SelectAllOrders()` w interfejsie do komunikacji z bazą danych. Istniała tylko po to, aby ułatwić pisanie testu - nie używał jej żaden kod produkcyjny. Możemy usunąć tę metodę i pozbyć się kolejnych problemów z utrzymaniem kodu. Zamiast wywoływania funkcji `SelectAllOrders()`, nasz mock utworzony przez NSubstitute zapisuje wywołania wszystkich swoich funkcji, pozwalając nam na skorzystanie ze specjalnej metody o nazwie `Received()` (patrz ostatnia linia tego testu). W istocie, jest to zakamuflowana asercja sprawdzająca, czy metoda `Insert()` została wywołana z konkretnym obiektem zamówienia jako parametr.

To objaśnienie mocków jest bardzo płytkie, a jego celem jest tylko sprawienie, abyś zaczął działać. Wrócimy do mocków później, ponieważ zaledwie podrapaliśmy powierzchnię.

## Generator wartości anonimizowanych

Patrząc na dane testowe z poprzedniej sekcji widzimy, że wiele wartości podano bardzo konkretnie, np. w następującym kodzie:

```csharp
var order = new Order(
  name: "Grzesiek",
  surname: "Galezowski",
  product: "Agile Acceptance Testing",
  date: DateTime.Now,
  quantity: 1);
```

imię, nazwisko, produkt, data i ilość są bardzo specyficzne. Może to sugerować, że te konkretne wartości są ważne z punktu widzenia zachowania, które testujemy. Z drugiej strony, gdy ponownie spojrzymy na kod, który jest testowany:

```csharp
public void Place(Order order)
{
  try
  {
    this.orderDatabase.Insert(order);
  }
  catch(Exception e)
  {
    this.log.Write("Could not insert an order. Reason: " + e);
  }
}
```

możemy zauważyć, że wartości te nie są nigdzie używane - testowana klasa nie wymaga ich, ani nie sprawdza w żaden sposób. Te wartości mogłyby być ważne z punktu widzenia bazy danych, ale już zdążyliśmy pozbyć się prawdziwej bazy danych z testów. Czy nie przeszkadza ci, że wypełniamy obiekt zamówienia tak wieloma wartościami, które nie mają związku z samą logiką testu i które zakłócają strukturę testu niepotrzebnymi szczegółami? Aby usunąć ten bałagan, wprowadźmy metodę o opisowej nazwie, tworzącą zamówienie ale ukrywającą szczegóły, których osoba czytająca test wcale nie potrzebuje:

```csharp
[Fact] public void 
ShouldInsertNewOrderToDatabase()
{
  //GIVEN
  var orderDatabase = Substitute.For<OrderDatabase>();
  var orderProcessing = new OrderProcessing(orderDatabase, new FileLog());
  var order = AnonymousOrder();

  //WHEN
  orderProcessing.Place(order);

  //THEN
  orderDatabase.Received(1).Insert(order);
}

public Order AnonymousOrder()
{
  return new Order(
    name: "Grzesiek", 
    surname: "Galezowski", 
    product: "Agile Acceptance Testing", 
    date: DateTime.Now,
    quantity: 1);
}
```

Teraz jest znacznie lepiej. Nie tylko sprawiliśmy, że test był krótszy, ale również pokazaliśmy czytelnikowi, że wartości użyte do utworzenia zamówienia nie mają znaczenia z punktu widzenia przetestowanej logiki przetwarzania zamówień, są zanonimizowane. Dlatego też nazwa `AnonymousOrder()`.

Przy okazji, czy nie byłoby miło, gdybyśmy sami nie musieli anonimizować obiektów, ale mogli polegać na innej bibliotece, która by dla nas generowała obiekty już zanonimizowane? Niespodzianka, jest jedna! Nazywa się [** Autofixture **] (https://github.com/AutoFixture/AutoFixture). Jest to przykład tak zwanego generatora anonimizowanych wartości (choć jego twórca lubi mówić, że jest to również implementacja wzorca projektowego Konstruktor Danych Testowych (Test Data Builder), ale pomińmy tutaj tę dyskusję.

Po zmianie naszego testu tak, by używał biblioteki `AutoFixture` dochodzimy do tego:

```csharp
private Fixture any = new Fixture();

[Fact] public void ShouldInsertNewOrderToDatabase()
{
  //GIVEN
  var orderDatabase = Substitute.For<OrderDatabase>();
  var orderProcessing = new OrderProcessing(orderDatabase, new FileLog());
  var order = any.Create<Order>();

  //WHEN
  orderProcessing.Place(order);

  //THEN
  orderDatabase.Received(1).Insert(order);
}
```

W tym teście używamy instancji klasy `Fixture` (która jest częścią `AutoFixture`) do tworzenia anonimizowanych wartości za pomocą metody o nazwie `Create()`. Tym samym, to pozwala nam usunąć metodę `AnonymousOrder()`, dzięki czemu konfiguracja testu jest krótsza.

Nieźle, co? AutoFixture ma wiele zaawansowanych funkcji, ale żeby wszystko było proste, chciałbym ukryć jego obecność za statyczną klasą o nazwie `Any`. Najprostsza implementacja takiej klasy wyglądałaby tak:

```csharp
public static class Any
{
  private static any = new Fixture();

  public static T Instance<T>()
  {
    return any.Create<T>();
  }
}
```

W następnych rozdziałach zobaczymy wiele różnych metod klasy `Any`, a także pełne wyjaśnienie filozofii, która za tym stoi. Im dłużej używasz tej klasy, tym bardziej rozszerza się ona o nowe metody tworzenia niestandardowych obiektów.

## Podsumowanie

W niniejszym rozdziale przedstawiono trzy narzędzia, których będziemy używać w tej książce, po opanowaniu których, Twoje wytwarzanie oprogramowania sterowane testami (test-driven development) będzie szło Ci bardziej płynnie. Jeśli ten rozdział nie sprawił, żebyś uważał użycie tych trzech narzędzi za zasadne, nie martw się - zagłębimy się w filozofię stojącą za nimi w następnych rozdziałach. Na razie chcę tylko, żebyś zapoznał się z ich składnią. No dalej, pobierz te narzędzia z Internetu, uruchom je, spróbuj napisać coś prostego przy ich użyciu. Nie musisz jeszcze rozumieć ich pełnego celu, po prostu zacznij zabawę :-).