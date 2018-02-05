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

1.  Za każdym razem, gdy dodajemy nowy test, musimy zaktualizować metodę `Main()` o wywołanie nowego testu. Jeśli zapomnimy tego, test nigdy nie zostanie uruchomiony. Na początku nie jest to wielka sprawa, ale gdy już będziemy mieć dziesiątki testów, trudno będzie zauważyć te niedodane.
2.  Wyobraź sobie, że Twój system składa się z więcej niż jednej aplikacji - miałbyś problemy ze zbieraniem wyników testów z wszystkich aplikacji, z których składa się twój system.
3.  Wkrótce będziesz musiał napisać wiele innych metod podobnych do `AssertTwoIntegersAreEqual()` -- ta tutaj porównuje dwie liczby całkowite, ale co jeśli chcemy sprawdzić inny warunek, np. czy jedna liczba całkowita jest większa od innej? Co by było, gdybyśmy chcieli sprawdzić równość nie dla liczb całkowitych, ale dla znaków, ciągów znaków, itp.? Co by było, gdybyśmy chcieli sprawdzić pewne właściwości kolekcji, np. czy kolekcja jest posortowana, lub czy wszystkie elementy w kolekcji są unikatowe?
4.  Jeśli test się nie powiedzie, trudno będzie przenieść się od komunikatu na konsoli do odpowiedniego wiersza w kodzie źródłowym w twoim IDE. Czy nie byłoby łatwiej - kliknąć komunikat o błędzie i zostać przeniesionym do miejsca w kodzie, dzie wystąpił błąd?

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

1.  Lista testów (**Test List**) jest teraz tworzona automatycznie przez framework na podstawie wszystkich metod oznaczonych atrybutem `[Fact]`. Nie ma potrzeby zarządzać z poziomu kodu już żadnymi listami, dlatego znika metoda `Main()`.
2.  Metoda testująca (**Test Method**) wciąż jest obecna i wygląda niemalże tak jak wcześniej.
3.  Asercja (**Assertion**) przyjęła kształt statycznej metody `Assert.Equal()` -- xUnit.NET framework posiada szeroki zakres takich asercji, a więc użyłem jednej z nich. Oczywiście, nie ma przeszkód byś napisał swoją własną asercję, jeśli framework nie oferuje Ci tego, czego szukasz.

Uff, mam nadzieję, że to przejście do frameworka testującego okazało się w miarę bezbolesne dla Ciebie. Teraz ostatnia rzecz - skoro nie ma już metody `Main()`, to pewnie się zastanawiasz jakim cudem uruchamiamy te testy, prawda? Dobrze, wyjawię Ci ostatni sekret -- używamy zewnętrzenej aplikacji do tego celu (*po polsku można ją nazwać odpalaczem testów, po angielsku to* **Test Runner**) -- określamy które zestawy z testami (assemblies) chcemy załadować, rest runner uruchamia testy, tworzy raporty na podstawie wyników etc. Nasz odpalacz może przyjać wiele form, to może być aplikacja konsolowa, aplikacja z GUI albo plugin do IDE. Oto przykład test runner'a dostarczanego jako plugin do Visual Studio IDE, nazywającego się Resharper:

![Resharper test runner docked as a window in Visual Studio 2015 IDE](images/Resharper_Test_Runner.PNG)

## Framework mockujący

W> To wprowadzenie jest przeznaczone dla tych, którzy nie są biegli w używaniu mocków (*czytaj: "moków", `mock` to kolejny angielskojęzyczny termin w informatyce, który nie ma swojego odpowiednika w jęzku polskim - dosłownie tłumacząc z angielskiego, mock to imitacja*). Mocki mogą nie być najłatwiejsze do zrozumienia i dlatego jestem w stanie zaakceptować, jeśli na razie będziesz miał problemy z uchwyceniem koncepcji. Jeśli, podczas czytania tego wprowadzenia, zgubisz się - nie zwacaj na to w ogóle uwagi i idź dalej. Będziemy zajmować się mocno mockami w drugiej części książki, gdzie zagwarantuję Ci bogatszy i dokładniejszy opis.

Kiedy chcemy przetestować klasę, która zależy od innej klasy, możnaby sądzić, że dobrym pomysłem jest umieszczenie w teście również tej drugiej klasy. To, jednakże, nie pozwoli nam testować wyłącznie jednego obiektu, czy też małej grupy obiektów tak, byśmy mogli sprawdzić prawidłowe działanie najmniejszego fragmentu aplikacji. *Testujemy najmniejszy fragment programu, bo gdy test nie będzie przechodził, łatwiej będzie znaleźć w małym fragmencie miejsce i przyczynę wystąpienia błędu*. Jeśli sprawimy, iż nasza klasa nie zależy od innych klas, ale zależy raczej od abstrakcji w postaci interfejsów - możemy z łatwością implementować te interfejsy za pomocą specjalnych, "fałszywych" klas, stworzonych w taki sposób, by ułatwić nam testowanie. Na przykład obiekty takich klas mogą zawierać wstępnie zaprogramowane wartości zwracane dla metody zadeklarowanej w interfejsie. Mogą także zapamiętywać, które metody były wywoływane i zezwalać testowi na sprawdzenie, czy komunikacja między obiektem poddawanym testowi a jego zależnościami jest poprawna. 

To może nie mieć znaczenia w Twoim przypadku, ale preferowanym podejściem jest stworzenie imitacji obiektu na podstawie interfejsu, a nie klasy, ponieważ normalnie, jeśli podążasz za TDD (Test Driven Development), możesz napisać testy jednostkowe jeszcze przed napisaniem implementacji zależnych klas. Dlatego, nawet jeśli nie masz konkretnej klasy DataAccessImpl, nadal możesz używać interfejsu DataAccess.

 *Niekorzystanie z interfejsów skutecznie utrudnia TDD, bo zmusza nas do tworzenia zależnych klas wraz z ciałami metod, nawet wtedy, kiedy ich jeszcze nie potrzebujemy. Żeby skomplikować sprawę, dodam - że w Javie można, bez przeszkód, stworzyć imitacje na podstawie definicji klasy - a nie da się tego samego równie bezproblemowo zrobić w C#. Warto zaznaczyć, że interfejs w C# nigdy nie będzie zawierał metod. Interfejsy w Javie również były zbiorem sygnatur metod (bez ciała), ale Java 8 wprowadziła metody domyślne pozwalające zdefiniować ciało metody w interfejsie.*

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

At the beginning of the test we open a connection to the database and clean all existing orders in it (more on that shortly), then create an order object, insert it into the database and query the database for all orders it contains. At the end, we make an assertion that the order we tried to insert is among all orders in the database.

Why do we clean up the database at the beginning of the test? Remember that a database provides persistent storage. If we don't clean it up before executing the logic of this test, the database may already contain the item we are trying to add, e.g. from previous executions of this test. The database might not allow us to add the same item again and the test would fail. Ouch! It hurts so bad, because we wanted our tests to prove something works, but it looks like it can fail even when the logic is coded correctly. Of what use would be such a test if it couldn't reliably tell us whether the implemented logic is correct or not? So, to make sure that the state of the persistent storage is the same every time we run this test, we clean up the database before each run.

Now that the test is ready, did we get what we wanted from it? I would be hesitant to answer "yes". There are several reasons for that:

1. The test will most probably be slow, because accessing database is relatively slow. It is not uncommon to have more than a thousand tests in a suite and I don't want to wait half an hour for results every time I run them. Do you?
2. Everyone who wants to run this test will have to set up a special environment, e.g. a local database on their machine. What if their setup is slightly different from ours? What if the schema gets outdated -- will everyone manage to notice it and update the schema of their local databases accordingly? Should we re-run our database creation script only to ensure we have got the latest schema available to run your tests against?
3. There may be no implementation of the database engine for the operating system running on our development machine if our target is an exotic or mobile platform.
4. Note that the test we wrote is only one out of two. We still have to write another one for the scenario where inserting an order ends with an exception. How do we setup the database in a state where it throws an exception? It is possible, but requires significant effort (e.g. deleting a table and recreating it after the test, for use by other tests that might need it to run correctly), which may lead some to the conclusion that it is not worth writing such tests at all.

Now, let's try to approach this problem in a different way. Let's assume that the `MySqlOrderDatabase` that queries a real database query is already tested (this is because I don't want to get into a discussion on testing database queries just yet - we'll get to it in later chapters) and that the only thing we need to test is the `OrderProcessing` class (remember, we're trying to imagine really hard that there is some serious domain logic coded here). In this situation we can leave the `MySqlOrderDatabase` out of the test and instead create another, fake implementation of the `OrderDatabase` that acts as if it was a connection to a database but does not write to a real database at all -- it only stores the inserted records in a list in memory. The code for such a fake connection could look like this: 

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

Zauważ, że nieprawdziwa, udawana baza danych jest instancją klasy, która implementuje ten sam interfejs co `MySqlOrderDatabase`. Tak więc, możemy sprawić, że testowany kod użyje fałszywej bazy danych nawet o tym nie widząc.

Zastąpmy prawdziwą implementację bazę danych zamówień fałszywą instancją w naszym teście:

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

Zauważ, że nie czyścimy obiektu fałszywej bazy danych, tak jak robiliśmy to z prawdziwą bazą danych, ponieważ tworzymy nowy obiekt za każdym razem, gdy test jest uruchamiany, a wyniki są przechowywane w innym miejscu pamięci dla każdej instancji. Test będzie teraz znacznie szybszy, ponieważ nie mamy już dostępu do prawdziwej bazy danych. Co więcej, możemy teraz łatwo napisać test na wypadek błędu przy dodawaniu nowego zamówienia. W jaki sposób? Po prostu zrobimy kolejną, fałszywą bazę danych, zaimplementowaną w ten sposób:

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

Ok, na razie dobrze, ale teraz mamy dwie klasy fałszywych baz danych do utrzymania (i są szanse, że będziemy potrzebować ich jeszcze więcej). Każda metoda dodana do interfejsu `OrderDatabase` musi również zostać dodana do każdej z tych fałszywych klas. Możemy zaoszczędzić trochę kodu, czyniąc nasze imitacje nieco bardziej generycznymi, byśmy ich zachowania mogli konfigurować za pomocą wyrażeń lambda:

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

Teraz nie musimy tworzyć dodatkowych klas dla nowych scenariuszy, ale nasza składnia stała się bardziej uciążliwa. Oto jak konfigurujemy fałszywą bazę zamówień, by pamiętała i pozwalała odczytać wprowadzone zamówienie:

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

Note that we don't need the `SelectAllOrders()` method on the database connection interface anymore. It was there only to make writing the test easier -- no production code used it. We can delete the method and get rid of some more maintenance trouble. Instead of the call to `SelectAllOrders()`, mocks created by NSubstitute record all calls received and allow us to use a special method called `Received()` on them (see the last line of this test), which is actually a camouflaged assertion that checks whether the `Insert()` method was called with the order object as parameter.

This explanation of mock objects is very shallow and its purpose is only to get you up and running. We'll get back to mocks later as we've only scratched the surface here.

## Anonymous values generator

Looking at the test data in the previous section we see that many values are specified literally, e.g. in the following code:

```csharp
var order = new Order(
  name: "Grzesiek",
  surname: "Galezowski",
  product: "Agile Acceptance Testing",
  date: DateTime.Now,
  quantity: 1);
```

the name, surname, product, date and quantity are very specific. This might suggest that the exact values are important from the perspective of the behavior we are testing. On the other hand, when we look at the tested code again:

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

we can spot that these values are not used anywhere -- the tested class does not use or check them in any way. These values are important from the database point of view, but we already took the real database out of the picture. Doesn't it trouble you that we fill the order object with so many values that are irrelevant to the test logic itself and that clutter the structure of the test with needless details? To remove this clutter let's introduce a method with a descriptive name to create the order and hide the details we don't need from the reader of the test:

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

Now, that's better. Not only did we make the test shorter, we also provided a hint to the reader that the actual values used to create an order don't matter from the perspective of tested order-processing logic. Hence the name `AnonymousOrder()`.

By the way, wouldn't it be nice if we didn't have to provide the anonymous objects ourselves, but could rely on another library to generate these for us? Susprise, surprise, there is one! It's called [**Autofixture**](https://github.com/AutoFixture/AutoFixture). It is an example of so-called anonymous values generator (although its creator likes to say that it is also an implementation of Test Data Builder pattern, but let's skip this discussion here). 

After changing our test to use AutoFixture, we arrive at the following:

```csharp
private Fixture any = new Fixture();

[Fact] public void 
ShouldInsertNewOrderToDatabase()
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

W tym teście używamy instancji klasy `Fixture` (która jest częścią AutoFixture) do tworzenia anonimowych wartości za pomocą metody o nazwie `Create()`. To pozwala nam usunąć metodę `AnonymousOrder()`, dzięki czemu konfiguracja testu jest krótsza.

Nieźle, co? AutoFixture ma wiele zaawansowanych funkcji, ale żeby wszystko było proste, chciałbym ukryć jego użycie za statyczną klasą o nazwie `Any`. Najprostsza implementacja takiej klasy wyglądałaby tak:

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